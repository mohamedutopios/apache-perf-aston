Voici une version complète, corrigée et fonctionnelle de la démonstration précédente, adaptée à votre environnement, avec les correctifs appliqués pour éviter les erreurs :

---

### **Configuration complète et corrigée**

#### **Fichier : `/etc/apache2/sites-available/context-demo.conf`**

```apache
<VirtualHost *:80>
    ServerName context-demo.local
    DocumentRoot /var/www/context-demo

    # Journaux globaux pour ce Virtual Host
    ErrorLog ${APACHE_LOG_DIR}/context-demo-error.log
    CustomLog ${APACHE_LOG_DIR}/context-demo-access.log combined

    # Redirections basées sur User-Agent
    <Directory /var/www/context-demo>
        Options Indexes FollowSymLinks
        AllowOverride None
        Require all granted

        <If "%{HTTP_USER_AGENT} =~ /(Mobile|Android|iPhone)/">
            RedirectMatch ^/$ /mobile.html
        </If>
        <Else>
            RedirectMatch ^/$ /desktop.html
        </Else>
    </Directory>

    # Inclusion conditionnelle d'un fichier
    <IfFile "/etc/apache2/enable-feature.conf">
        Include /etc/apache2/enable-feature.conf
    </IfFile>
</VirtualHost>

<IfModule mod_ssl.c>
<VirtualHost *:443>
    ServerName context-demo.local
    DocumentRoot /var/www/context-demo

    SSLEngine On
    SSLCertificateFile /etc/ssl/certs/ssl-cert-snakeoil.pem
    SSLCertificateKeyFile /etc/ssl/private/ssl-cert-snakeoil.key

    <Directory /var/www/context-demo>
        Options Indexes FollowSymLinks
        AllowOverride None
        Require all granted
    </Directory>
</VirtualHost>
</IfModule>
```

---

### **Étapes à suivre pour mettre en place et tester**

#### **1. Installer et configurer Apache**

1. Installez Apache si ce n'est pas déjà fait :
   ```bash
   sudo apt update
   sudo apt install apache2
   ```

2. Activez les modules nécessaires :
   ```bash
   sudo a2enmod rewrite ssl
   sudo systemctl restart apache2
   ```

---

#### **2. Créer la structure des fichiers**

1. Créez le répertoire pour le site :
   ```bash
   sudo mkdir -p /var/www/context-demo
   sudo chown -R $USER:$USER /var/www/context-demo
   ```

2. Ajoutez les fichiers HTML nécessaires :
   ```bash
   echo "<h1>Welcome to the default page</h1>" > /var/www/context-demo/index.html
   echo "<h1>Welcome Mobile Users</h1>" > /var/www/context-demo/mobile.html
   echo "<h1>Welcome Desktop Users</h1>" > /var/www/context-demo/desktop.html
   ```

3. Créez un fichier conditionnel (vide pour l'instant) :
   ```bash
   sudo touch /etc/apache2/enable-feature.conf
   ```

---

#### **3. Configurer le Virtual Host**

1. Placez le fichier de configuration corrigé dans le répertoire approprié :
   ```bash
   sudo nano /etc/apache2/sites-available/context-demo.conf
   ```
   Collez-y la configuration complète donnée plus haut.

2. Activez le site et désactivez le site par défaut si nécessaire :
   ```bash
   sudo a2ensite context-demo.conf
   sudo a2dissite 000-default.conf
   ```

3. Ajoutez le domaine local à `/etc/hosts` :
   ```bash
   echo "127.0.0.1 context-demo.local" | sudo tee -a /etc/hosts
   ```

4. Rechargez Apache pour appliquer les modifications :
   ```bash
   sudo systemctl reload apache2
   ```

---

#### **4. Tester la configuration**

##### **a. Tester les redirections basées sur User-Agent**

1. Simulez un User-Agent mobile :
   ```bash
   curl -A "Mozilla/5.0 (iPhone)" http://context-demo.local
   ```
   Attendu : Contenu de `mobile.html`.

2. Simulez un User-Agent desktop :
   ```bash
   curl -A "Mozilla/5.0 (Windows NT 10.0)" http://context-demo.local
   ```
   Attendu : Contenu de `desktop.html`.

##### **b. Tester l’inclusion conditionnelle**

1. Activez une fonctionnalité en ajoutant du contenu au fichier conditionnel :
   ```bash
   echo "SetEnv FEATURE_ENABLED true" | sudo tee /etc/apache2/enable-feature.conf
   sudo systemctl reload apache2
   ```

2. Vérifiez si la fonctionnalité est activée (cela dépend du contenu ajouté).

3. Désactivez la fonctionnalité en supprimant le fichier :
   ```bash
   sudo rm /etc/apache2/enable-feature.conf
   sudo systemctl reload apache2
   ```

##### **c. Tester HTTPS**

1. Vérifiez que `mod_ssl` est activé :
   ```bash
   sudo a2enmod ssl
   sudo systemctl restart apache2
   ```

2. Accédez au site via HTTPS :
   ```bash
   https://context-demo.local
   ```
   Ignorez les avertissements si vous utilisez les certificats Snakeoil (auto-signés).

---

#### **5. Dépannage**

1. **Testez la configuration Apache :**
   ```bash
   sudo apache2ctl configtest
   ```
   Attendu : `Syntax OK`.

2. **Vérifiez les logs Apache :**
   - Journaux des erreurs :
     ```bash
     sudo tail -f /var/log/apache2/error.log
     ```
   - Journaux d'accès :
     ```bash
     sudo tail -f /var/log/apache2/access.log
     ```

---

### **Résumé**

- La configuration est fonctionnelle et respecte les contraintes des directives Apache.
- **Redirections conditionnelles** : Basées sur l’User-Agent.
- **Inclusion conditionnelle** : Basée sur l'existence d'un fichier.
- **HTTPS** : Activé si `mod_ssl` est disponible.

Ce guide couvre toutes les étapes nécessaires pour déployer et tester efficacement cette configuration sur Ubuntu. Si vous rencontrez des problèmes spécifiques, partagez les détails pour une assistance ciblée !