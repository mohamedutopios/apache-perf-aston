Voici la **seconde démonstration complète**, avec toutes les étapes révisées pour intégrer les modifications nécessaires en lien avec la configuration et l'étape 1.

---

### **Démonstration : Droits dédiés et gestion des sessions PHP pour deux applications avec Apache et PHP-FPM**

#### **Contexte**
Vous souhaitez :
1. Héberger deux applications PHP distinctes.
2. Isoler les deux applications avec des **identités dédiées** (utilisateurs distincts).
3. Gérer les **sessions PHP** de manière séparée pour éviter tout mélange ou interférence entre les applications.

---

## **Étape 1 : Préparation de l’environnement**

### **1.1 Installer Apache et PHP avec PHP-FPM**
1. Mettez à jour le système et installez Apache ainsi que les versions nécessaires de PHP avec leurs gestionnaires PHP-FPM :
   ```bash
   sudo apt update
   sudo apt install apache2 php7.4 php7.4-fpm php8.1 php8.1-fpm -y
   ```

2. Activez les modules nécessaires pour utiliser PHP-FPM avec Apache :
   ```bash
   sudo a2enmod proxy_fcgi setenvif actions
   sudo systemctl restart apache2
   ```

---

### **1.2 Désactiver les configurations par défaut conflictuelles**

#### **Désactiver les fichiers de pool par défaut**
Les fichiers de pool par défaut peuvent créer des conflits avec les configurations spécifiques à `app1` et `app2`. Renommez-les ou désactivez-les :
```bash
sudo mv /etc/php/7.4/fpm/pool.d/www.conf /etc/php/7.4/fpm/pool.d/www.conf.disabled
sudo mv /etc/php/8.1/fpm/pool.d/www.conf /etc/php/8.1/fpm/pool.d/www.conf.disabled
```

#### **Redémarrer PHP-FPM pour appliquer les modifications**
```bash
sudo systemctl restart php7.4-fpm
sudo systemctl restart php8.1-fpm
```

---

## **Étape 2 : Configurer les identités dédiées pour les applications**

### **2.1 Créer des utilisateurs système pour chaque application**
Ces utilisateurs garantiront l'isolation des applications :
```bash
sudo adduser --system --no-create-home --group app1
sudo adduser --system --no-create-home --group app2
```

### **2.2 Configurer les répertoires des applications**
1. Créez les répertoires des applications :
   ```bash
   sudo mkdir -p /var/www/app1
   sudo mkdir -p /var/www/app2
   ```

2. Attribuez les répertoires aux utilisateurs correspondants :
   ```bash
   sudo chown -R app1:app1 /var/www/app1
   sudo chown -R app2:app2 /var/www/app2
   ```

---

## **Étape 3 : Configurer PHP-FPM avec des pools dédiés**

### **3.1 Créer des fichiers de configuration pour chaque pool**

#### **Fichier pour `app1`** : `/etc/php/7.4/fpm/pool.d/app1.conf`
```ini
[app1]
user = app1
group = app1
listen = /run/php/php7.4-app1.sock
listen.owner = www-data
listen.group = www-data
listen.mode = 0660

pm = dynamic
pm.max_children = 10
pm.start_servers = 2
pm.min_spare_servers = 1
pm.max_spare_servers = 3

php_value[session.save_path] = /var/lib/php/sessions/app1
php_admin_value[error_log] = /var/log/php7.4-fpm-app1.log
php_admin_flag[log_errors] = on
```

#### **Fichier pour `app2`** : `/etc/php/8.1/fpm/pool.d/app2.conf`
```ini
[app2]
user = app2
group = app2
listen = /run/php/php8.1-app2.sock
listen.owner = www-data
listen.group = www-data
listen.mode = 0660

pm = dynamic
pm.max_children = 10
pm.start_servers = 2
pm.min_spare_servers = 1
pm.max_spare_servers = 3

php_value[session.save_path] = /var/lib/php/sessions/app2
php_admin_value[error_log] = /var/log/php8.1-fpm-app2.log
php_admin_flag[log_errors] = on
```

---

### **3.2 Créer les répertoires pour les sessions**
1. Créez les répertoires pour stocker les sessions PHP :
   ```bash
   sudo mkdir -p /var/lib/php/sessions/app1
   sudo mkdir -p /var/lib/php/sessions/app2
   ```

2. Attribuez les permissions correctes :
   ```bash
   sudo chown -R app1:app1 /var/lib/php/sessions/app1
   sudo chown -R app2:app2 /var/lib/php/sessions/app2
   sudo chmod 700 /var/lib/php/sessions/app1
   sudo chmod 700 /var/lib/php/sessions/app2
   ```

### **3.3 Redémarrer PHP-FPM**
Redémarrez PHP-FPM pour appliquer les configurations :
```bash
sudo systemctl restart php7.4-fpm
sudo systemctl restart php8.1-fpm
```

---

## **Étape 4 : Configurer Apache avec des Virtual Hosts**

### **4.1 Créer les Virtual Hosts**

#### **Virtual Host pour `app1`** : `/etc/apache2/sites-available/app1.conf`
```apache
<VirtualHost *:80>
    ServerName app1.example.com
    DocumentRoot /var/www/app1

    <Directory /var/www/app1>
        AllowOverride All
        Require all granted
    </Directory>

    <FilesMatch \.php$>
        SetHandler "proxy:unix:/run/php/php7.4-app1.sock|fcgi://localhost"
    </FilesMatch>

    ErrorLog ${APACHE_LOG_DIR}/app1-error.log
    CustomLog ${APACHE_LOG_DIR}/app1-access.log combined
</VirtualHost>
```

#### **Virtual Host pour `app2`** : `/etc/apache2/sites-available/app2.conf`
```apache
<VirtualHost *:80>
    ServerName app2.example.com
    DocumentRoot /var/www/app2

    <Directory /var/www/app2>
        AllowOverride All
        Require all granted
    </Directory>

    <FilesMatch \.php$>
        SetHandler "proxy:unix:/run/php/php8.1-app2.sock|fcgi://localhost"
    </FilesMatch>

    ErrorLog ${APACHE_LOG_DIR}/app2-error.log
    CustomLog ${APACHE_LOG_DIR}/app2-access.log combined
</VirtualHost>
```

---

### **4.2 Activer les Virtual Hosts**
Activez les Virtual Hosts créés et rechargez Apache :
```bash
sudo a2ensite app1.conf
sudo a2ensite app2.conf
sudo systemctl reload apache2
```

---

## **Étape 5 : Tester la configuration**

### **5.1 Créer des fichiers de test**
1. Ajoutez des fichiers de test dans chaque répertoire :
   - Pour `app1` :
     ```bash
     echo "<?php session_start(); echo 'App1 Session ID: ' . session_id(); ?>" | sudo tee /var/www/app1/index.php
     ```
   - Pour `app2` :
     ```bash
     echo "<?php session_start(); echo 'App2 Session ID: ' . session_id(); ?>" | sudo tee /var/www/app2/index.php
     ```

### **5.2 Ajouter les noms de domaine au fichier `hosts`**
Ajoutez les entrées suivantes dans `/etc/hosts` :
```plaintext
127.0.0.1 app1.example.com
127.0.0.1 app2.example.com
```

### **5.3 Accéder aux applications**
- Accédez à [http://app1.example.com](http://app1.example.com) et vérifiez que vous obtenez un ID de session unique pour App1.
- Accédez à [http://app2.example.com](http://app2.example.com) et vérifiez que vous obtenez un ID de session unique pour App2.

---

## **Résumé**

1. **Isoler chaque application** :
   - Les pools PHP-FPM, répertoires de sessions, et utilisateurs système sont indépendants.
2. **Modifier les configurations conflictuelles** :
   - Les fichiers de pool par défaut ont été désactivés.
3. **Tester les Virtual Hosts** :
   - Chaque application utilise un Virtual Host spécifique.

---

Cette configuration garantit que les deux applications PHP sont isolées et fonctionnent indépendamment. Si des problèmes persistent, partagez les messages d'erreur pour un dépannage ciblé. 😊