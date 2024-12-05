Héberger des applications PHP et faire cohabiter **PHP 5** et **PHP 7** sur le même serveur Apache peut être utile lorsque vous devez prendre en charge des applications héritées utilisant PHP 5 tout en supportant des applications modernes développées avec PHP 7 ou une version ultérieure.

Voici comment configurer un tel environnement.

---

### **1. Concepts clés**

1. **Apache et PHP-FPM** :
   - Apache peut exécuter plusieurs versions de PHP en utilisant **PHP-FPM** (FastCGI Process Manager).
   - Chaque version de PHP est configurée dans un pool distinct.

2. **Virtual Hosts** :
   - Vous configurez Apache pour assigner des versions spécifiques de PHP à des **Virtual Hosts** (ou des répertoires spécifiques).

---

### **2. Installation des versions PHP**

#### **Installer PHP 5.6 et PHP 7.4 (ou autres versions)**

Sur Ubuntu/Debian :
1. Installez les dépôts nécessaires :
   ```bash
   sudo apt update
   sudo apt install software-properties-common
   sudo add-apt-repository ppa:ondrej/php
   sudo apt update
   ```

2. Installez PHP 5.6 et PHP 7.4 avec leurs modules FPM :
   ```bash
   sudo apt install php5.6 php5.6-fpm php7.4 php7.4-fpm
   ```

Sur CentOS/RHEL :
1. Activez les dépôts Remi :
   ```bash
   sudo yum install epel-release
   sudo yum install https://rpms.remirepo.net/enterprise/remi-release-7.rpm
   sudo yum install yum-utils
   ```

2. Installez les versions nécessaires :
   ```bash
   sudo yum-config-manager --enable remi-php56
   sudo yum-config-manager --enable remi-php74
   sudo yum install php56 php56-php-fpm php74 php74-php-fpm
   ```

---

### **3. Configurer PHP-FPM**

#### **Configurer les pools pour chaque version**
Les pools PHP-FPM permettent de séparer les applications exécutées sous différentes versions de PHP.

1. **Fichier de configuration PHP-FPM pour PHP 5.6** :
   - Chemin : `/etc/php/5.6/fpm/pool.d/www.conf` (ou similaire selon l'OS).
   - Modifiez le port ou le socket pour éviter les conflits.
     ```ini
     [www]
     listen = /run/php/php5.6-fpm.sock
     ```

2. **Fichier de configuration PHP-FPM pour PHP 7.4** :
   - Chemin : `/etc/php/7.4/fpm/pool.d/www.conf`.
   - Exemple :
     ```ini
     [www]
     listen = /run/php/php7.4-fpm.sock
     ```

3. Redémarrez les services PHP-FPM :
   ```bash
   sudo systemctl restart php5.6-fpm
   sudo systemctl restart php7.4-fpm
   ```

---

### **4. Configurer Apache pour cohabiter PHP 5 et PHP 7**

#### **Activer les modules Apache nécessaires**
Activez les modules requis pour PHP-FPM et FastCGI :
```bash
sudo a2enmod proxy_fcgi setenvif
sudo a2enmod actions
sudo systemctl reload apache2
```

#### **Configurer des Virtual Hosts**
Créez un Virtual Host pour chaque version de PHP.

1. **Virtual Host pour PHP 5.6** :
   Fichier : `/etc/apache2/sites-available/php5.6.example.com.conf`.
   ```apache
   <VirtualHost *:80>
       ServerName php5.6.example.com
       DocumentRoot /var/www/php5.6

       <Directory /var/www/php5.6>
           AllowOverride All
           Require all granted
       </Directory>

       <FilesMatch \.php$>
           SetHandler "proxy:unix:/run/php/php5.6-fpm.sock|fcgi://localhost"
       </FilesMatch>

       ErrorLog ${APACHE_LOG_DIR}/php5.6-error.log
       CustomLog ${APACHE_LOG_DIR}/php5.6-access.log combined
   </VirtualHost>
   ```

2. **Virtual Host pour PHP 7.4** :
   Fichier : `/etc/apache2/sites-available/php7.4.example.com.conf`.
   ```apache
   <VirtualHost *:80>
       ServerName php7.4.example.com
       DocumentRoot /var/www/php7.4

       <Directory /var/www/php7.4>
           AllowOverride All
           Require all granted
       </Directory>

       <FilesMatch \.php$>
           SetHandler "proxy:unix:/run/php/php7.4-fpm.sock|fcgi://localhost"
       </FilesMatch>

       ErrorLog ${APACHE_LOG_DIR}/php7.4-error.log
       CustomLog ${APACHE_LOG_DIR}/php7.4-access.log combined
   </VirtualHost>
   ```

3. Activez les Virtual Hosts :
   ```bash
   sudo a2ensite php5.6.example.com.conf
   sudo a2ensite php7.4.example.com.conf
   sudo systemctl reload apache2
   ```

---

### **5. Tester la configuration**

1. **Créer des répertoires de test**
   - Répertoire pour PHP 5.6 :
     ```bash
     sudo mkdir -p /var/www/php5.6
     echo "<?php phpinfo(); ?>" | sudo tee /var/www/php5.6/index.php
     ```

   - Répertoire pour PHP 7.4 :
     ```bash
     sudo mkdir -p /var/www/php7.4
     echo "<?php phpinfo(); ?>" | sudo tee /var/www/php7.4/index.php
     ```

2. **Accéder aux Virtual Hosts**
   - Testez les URLs :
     - [http://php5.6.example.com](http://php5.6.example.com) : Vous devriez voir la page PHP Info indiquant **PHP 5.6**.
     - [http://php7.4.example.com](http://php7.4.example.com) : Vous devriez voir la page PHP Info indiquant **PHP 7.4**.

---

### **6. Résolution des problèmes**

- **Erreur 500 ou aucun contenu PHP exécuté :**
  - Vérifiez que les sockets PHP-FPM sont actifs :
    ```bash
    sudo systemctl status php5.6-fpm
    sudo systemctl status php7.4-fpm
    ```
  - Vérifiez les logs Apache pour plus de détails :
    ```bash
    tail -f /var/log/apache2/error.log
    ```

- **Conflits entre les versions PHP :**
  - Assurez-vous que chaque version utilise un socket ou un port distinct.

---

### **Résumé**

1. Installez plusieurs versions de PHP via PHP-FPM.
2. Configurez des pools PHP-FPM distincts pour chaque version.
3. Configurez Apache avec des Virtual Hosts assignés à chaque version PHP.
4. Testez les sites pour vérifier que chaque Virtual Host utilise la version PHP appropriée.

Cette approche garantit une cohabitation efficace et maintenable de PHP 5 et PHP 7 sur le même serveur, tout en permettant des migrations progressives des applications PHP anciennes vers des versions plus récentes.