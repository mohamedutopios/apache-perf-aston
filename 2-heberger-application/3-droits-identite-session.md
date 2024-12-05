### **Gérer les droits, identités dédiées et sessions dans un environnement PHP avec Apache**

Lors de l'hébergement d'applications PHP avec Apache et PHP-FPM, il est important de :
1. Configurer des **droits et identités dédiées** pour isoler les applications.
2. Gérer les **sessions PHP** pour assurer la sécurité et le bon fonctionnement des utilisateurs.

---

### **1. Droits et identités dédiées**

Chaque application hébergée doit fonctionner sous une identité utilisateur différente pour :
- Isoler les applications (éviter qu'une application compromise n'accède aux données d'une autre).
- Améliorer la sécurité globale du serveur.

#### **Étapes pour configurer des identités dédiées**

##### **a. Créer des utilisateurs système pour chaque application**

1. Créez un utilisateur système pour chaque application :
   ```bash
   sudo adduser --system --no-create-home --group phpapp1
   sudo adduser --system --no-create-home --group phpapp2
   ```

2. Attribuez les permissions des répertoires des applications :
   - Application 1 :
     ```bash
     sudo chown -R phpapp1:phpapp1 /var/www/phpapp1
     ```
   - Application 2 :
     ```bash
     sudo chown -R phpapp2:phpapp2 /var/www/phpapp2
     ```

---

##### **b. Configurer PHP-FPM avec des pools dédiés**

1. Modifiez le fichier de configuration des pools PHP-FPM pour chaque application :

- **Pour l'application 1** : `/etc/php/7.4/fpm/pool.d/phpapp1.conf`
  ```ini
  [phpapp1]
  user = phpapp1
  group = phpapp1
  listen = /run/php/phpapp1.sock
  listen.owner = www-data
  listen.group = www-data
  listen.mode = 0660
  ```

- **Pour l'application 2** : `/etc/php/8.1/fpm/pool.d/phpapp2.conf`
  ```ini
  [phpapp2]
  user = phpapp2
  group = phpapp2
  listen = /run/php/phpapp2.sock
  listen.owner = www-data
  listen.group = www-data
  listen.mode = 0660
  ```

2. Redémarrez PHP-FPM pour appliquer les changements :
   ```bash
   sudo systemctl restart php7.4-fpm
   sudo systemctl restart php8.1-fpm
   ```

---

##### **c. Configurer Apache pour utiliser les pools dédiés**

Modifiez les Virtual Hosts pour chaque application afin d'utiliser les sockets spécifiques :

- **Application 1** : `/etc/apache2/sites-available/phpapp1.conf`
  ```apache
  <VirtualHost *:80>
      ServerName phpapp1.example.com
      DocumentRoot /var/www/phpapp1

      <Directory /var/www/phpapp1>
          AllowOverride All
          Require all granted
      </Directory>

      <FilesMatch \.php$>
          SetHandler "proxy:unix:/run/php/phpapp1.sock|fcgi://localhost"
      </FilesMatch>
  </VirtualHost>
  ```

- **Application 2** : `/etc/apache2/sites-available/phpapp2.conf`
  ```apache
  <VirtualHost *:80>
      ServerName phpapp2.example.com
      DocumentRoot /var/www/phpapp2

      <Directory /var/www/phpapp2>
          AllowOverride All
          Require all granted
      </Directory>

      <FilesMatch \.php$>
          SetHandler "proxy:unix:/run/php/phpapp2.sock|fcgi://localhost"
      </FilesMatch>
  </VirtualHost>
  ```

Activez les Virtual Hosts et redémarrez Apache :
```bash
sudo a2ensite phpapp1.conf phpapp2.conf
sudo systemctl reload apache2
```

---

### **2. Gérer les sessions PHP**

Les sessions sont une fonctionnalité clé pour les applications web dynamiques. Une mauvaise gestion des sessions peut exposer les utilisateurs à des risques (vol de session, accès non autorisé).

#### **a. Configurer un répertoire de sessions dédié pour chaque application**

1. Créez des répertoires distincts pour stocker les fichiers de session :
   ```bash
   sudo mkdir -p /var/lib/php/sessions/phpapp1
   sudo mkdir -p /var/lib/php/sessions/phpapp2
   ```

2. Attribuez les permissions appropriées :
   ```bash
   sudo chown -R phpapp1:phpapp1 /var/lib/php/sessions/phpapp1
   sudo chown -R phpapp2:phpapp2 /var/lib/php/sessions/phpapp2
   sudo chmod 700 /var/lib/php/sessions/phpapp1 /var/lib/php/sessions/phpapp2
   ```

#### **b. Configurer PHP-FPM pour utiliser ces répertoires**

- **Pour l'application 1** :
  Modifiez `/etc/php/7.4/fpm/pool.d/phpapp1.conf` :
  ```ini
  php_value[session.save_path] = /var/lib/php/sessions/phpapp1
  ```

- **Pour l'application 2** :
  Modifiez `/etc/php/8.1/fpm/pool.d/phpapp2.conf` :
  ```ini
  php_value[session.save_path] = /var/lib/php/sessions/phpapp2
  ```

3. Redémarrez PHP-FPM :
   ```bash
   sudo systemctl restart php7.4-fpm
   sudo systemctl restart php8.1-fpm
   ```

---

### **3. Sécurisation supplémentaire des sessions**

#### **a. Configurer des options sécurisées pour les sessions PHP**
Ajoutez ces directives dans les fichiers de configuration PHP (`php.ini`) :

```ini
session.cookie_httponly = 1      ; Empêche l'accès aux cookies via JavaScript.
session.cookie_secure = 1        ; Force l'utilisation de HTTPS pour les cookies.
session.use_strict_mode = 1      ; Rejette les sessions non valides.
session.use_only_cookies = 1     ; Interdit l'utilisation d'URL contenant des identifiants de session.
```

#### **b. Utiliser une session basée sur une base de données**
Pour une application complexe, vous pouvez stocker les sessions dans une base de données sécurisée. Cela facilite la gestion et améliore la résilience des sessions.

---

### **Résumé de la configuration**

1. **Identités dédiées** :
   - Chaque application a son propre utilisateur et groupe système.
   - Chaque application fonctionne dans un pool PHP-FPM dédié.

2. **Gestion des sessions** :
   - Les sessions sont stockées dans des répertoires séparés pour éviter toute interférence.
   - Les sessions sont sécurisées avec des options PHP appropriées.

3. **Résultat** :
   - Applications isolées en termes de droits et sessions.
   - Sécurité renforcée pour les utilisateurs et données.

Cette configuration garantit un environnement sécurisé et optimisé pour l'hébergement d'applications PHP multi-utilisateurs ou multi-versions.