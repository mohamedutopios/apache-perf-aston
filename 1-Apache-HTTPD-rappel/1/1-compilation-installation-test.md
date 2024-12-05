### **Apache HTTPD 2.4 : Rappels et Nouveautés**
Apache HTTPD 2.4 est une version majeure du serveur web Apache offrant de nombreuses améliorations en termes de performances, fonctionnalités et flexibilité. Voici un rappel de ses concepts fondamentaux et des nouveautés introduites avec cette version.

---

### **1. Rappels sur Apache HTTPD**

#### **Architecture d'Apache**
- **Modules dynamiques** : Apache est modulaire, permettant d'ajouter ou de supprimer des fonctionnalités via des modules (ex. `mod_ssl`, `mod_rewrite`).
- **Multi-Processing Modules (MPM)** :
  - Contrôlent la gestion des connexions (processus ou threads).
  - Choix entre `prefork`, `worker`, et `event` selon les besoins.

#### **Composants principaux**
- **Virtual Hosts** : Permettent d'héberger plusieurs sites web sur un même serveur.
- **Fichiers de configuration** :
  - **httpd.conf** : Configuration principale.
  - **sites-available/** et **sites-enabled/** : Gèrent les sites sur Debian/Ubuntu.

#### **Flux HTTP**
- Apache gère les requêtes HTTP/HTTPS, renvoie des fichiers statiques ou exécute des scripts (PHP, Python, etc.).

---

### **2. Nouveautés majeures dans Apache 2.4**

1. **Améliorations des MPMs** :
   - Le MPM **event** est désormais recommandé par défaut pour sa gestion efficace des connexions persistantes (Keep-Alive).

2. **Support HTTP/2** :
   - Introduction du module `mod_http2` pour prendre en charge le protocole HTTP/2.

3. **Directives conditionnelles (`<If>`)** :
   - Permet de conditionner la configuration sur des variables ou expressions dynamiques.

4. **Gestion améliorée des proxys** :
   - Améliorations dans les modules `mod_proxy` et `mod_proxy_fcgi`.

5. **Nouvelles directives pour les performances** :
   - `KeepAliveTimeout` ajustable pour réduire les délais.
   - Compression améliorée avec `mod_deflate` et `mod_brotli`.

6. **Simplification de la configuration des droits d'accès** :
   - Nouvelle syntaxe des directives `Require` (remplaçant `Order`, `Allow`, et `Deny`).

7. **Améliorations en termes de sécurité** :
   - Support SNI pour HTTPS multi-domaines.
   - Intégration simplifiée avec des WAF (ex. `mod_security`).

---

### **3. Compilation, Installation et Test Initial**

#### **Prérequis**
- **Système** : Linux (Debian/Ubuntu ou CentOS/RHEL).
- **Outils nécessaires** :
  - GNU Compiler Collection (**gcc**).
  - Outils de compilation : `make`, `tar`.
  - Dépendances : `apr`, `apr-util`, `pcre`.

---

#### **Étape 1 : Télécharger les sources**


1. Rendez-vous sur le site officiel d'Apache :
   - [https://httpd.apache.org/download.cgi](https://httpd.apache.org/download.cgi)

2. Téléchargez la dernière version stable :
   ```bash
   wget https://downloads.apache.org/httpd/httpd-2.4.x.tar.gz
   ```

3. Extrayez l’archive :
   ```bash
   tar -xvf httpd-2.4.x.tar.gz
   cd httpd-2.4.x
   ```

---

#### **Étape 2 : Installer les dépendances**

1. Installez les bibliothèques requises :
   - Sur **Debian/Ubuntu** :
     ```bash
     sudo apt update
     sudo apt install build-essential libpcre3 libpcre3-dev libssl-dev zlib1g-dev
     ```

   - Sur **CentOS/RHEL** :
     ```bash
     sudo yum groupinstall "Development Tools"
     sudo yum install pcre pcre-devel openssl openssl-devel zlib zlib-devel
     ```

2. Téléchargez et installez **APR** (Apache Portable Runtime) si ce n'est pas déjà fait :
   ```bash
   wget https://downloads.apache.org/apr/apr-1.7.5.tar.gz
   tar -xvf apr-1.7.5.tar.gz
   cd apr-1.7.5
   ./configure
   make
   sudo make install
   ```

3. Téléchargez et installez **APR-Util** :
   ```bash
   wget https://downloads.apache.org/apr/apr-util-1.6.3.tar.gz
   tar -xvf apr-util-1.6.3.tar.gz
   cd apr-util-1.6.3
   ./configure --with-apr=/usr/local/apr --with-expat=/usr
   make
   sudo make install
   ```

---

#### **Étape 3 : Compiler et installer Apache**

1. Configurez les options de compilation :
   ```bash
./configure --enable-so \
            --enable-ssl \
            --enable-cgi \
            --enable-rewrite \
            --with-pcre \
            --with-mpm=event \
            --with-apr=/usr/local/apr \
            --with-apr-util=/usr/local/apr
   ```

   - **`--enable-so`** : Active les modules dynamiques.
   - **`--enable-ssl`** : Active le support HTTPS.
   - **`--with-mpm=event`** : Définit `event` comme MPM par défaut.
   - Chargement de modules dynamiques (--enable-so).
   - Support des connexions HTTPS (--enable-ssl).
   - Exécution de scripts CGI (--enable-cgi).
   - Réécriture avancée des URL (--enable-rewrite).
   - Utilisation des expressions régulières compatibles Perl avec PCRE (--with-pcre).
   - Optimisation des performances via le MPM event (--with-mpm=event).
   - Utilisation d'APR et APR-Util installés dans /usr/local/apr.

2. Compilez et installez Apache :
   ```bash
   make
   sudo make install
   ```

3. Vérifiez qu’Apache est installé correctement :
   ```bash
   /usr/local/apache2/bin/httpd -v
   ```

---

#### **Étape 4 : Configurer Apache**

1. Modifiez le fichier de configuration principal (`/usr/local/apache2/conf/httpd.conf`) :
   - Activez le module SSL :
     ```apache
     LoadModule ssl_module modules/mod_ssl.so
     ```

   - Configurez le répertoire des sites web :
     ```apache
     DocumentRoot "/usr/local/apache2/htdocs"
     <Directory "/usr/local/apache2/htdocs">
         AllowOverride All
         Require all granted
     </Directory>
     ```

2. Démarrez Apache :
   ```bash
   /usr/local/apache2/bin/apachectl start
   ```

---

#### **Étape 5 : Tester l’installation**

1. Vérifiez que le service est actif :
   ```bash
   ps aux | grep httpd
   ```

2. Accédez au serveur dans un navigateur :
   - URL : `http://<votre_adresse_IP>`

3. Vérifiez les logs pour des erreurs potentielles :
   ```bash
   tail -f /usr/local/apache2/logs/error_log
   ```

---

### **4. Résumé**

#### **Commandes principales**
- **Démarrer Apache** :
  ```bash
  /usr/local/apache2/bin/apachectl start
  ```
- **Arrêter Apache** :
  ```bash
  /usr/local/apache2/bin/apachectl stop
  ```
- **Redémarrer Apache** :
  ```bash
  /usr/local/apache2/bin/apachectl restart
  ```

#### **Améliorations à envisager**
- Configurer des Virtual Hosts pour plusieurs sites.
- Activer le support HTTP/2 via `mod_http2`.
- Ajouter des modules supplémentaires (`mod_rewrite`, `mod_proxy`, etc.).

Avec Apache HTTPD 2.4, vous bénéficiez d’une base robuste et flexible pour héberger des sites et applications modernes.


------- Librairies importantes : 


Ces bibliothèques sont nécessaires pour compiler et installer des logiciels (par exemple, **Nginx**, **HAProxy**, ou d'autres outils réseau ou serveurs web) qui dépendent de fonctionnalités spécifiques offertes par ces packages. Voici une explication détaillée de leur utilité :

### 1. **build-essential**
   - **Pourquoi ?**
     - Ce méta-package contient des outils essentiels pour la compilation de logiciels à partir de leur code source.
   - **Ce qu'il inclut :**
     - GCC (GNU Compiler Collection) : Un compilateur pour divers langages comme C et C++.
     - Make : Un outil d'automatisation pour la compilation.
     - Outils comme `libc6-dev` : Nécessaires pour compiler des programmes qui dépendent de la bibliothèque standard C.

### 2. **libpcre3** et **libpcre3-dev**
   - **Pourquoi ?**
     - Ces bibliothèques implémentent le support des expressions régulières Perl-Compatible (PCRE), utilisées dans de nombreuses applications.
   - **Utilisation courante :**
     - Serveurs comme **Nginx** ou **HAProxy** utilisent PCRE pour les configurations complexes, comme le routage basé sur des modèles.
   - **Différence entre `libpcre3` et `libpcre3-dev` :**
     - `libpcre3` est la bibliothèque binaire (runtime).
     - `libpcre3-dev` fournit les fichiers nécessaires pour développer ou compiler des logiciels qui utilisent PCRE (comme les en-têtes et fichiers `.so`).

### 3. **libssl-dev**
   - **Pourquoi ?**
     - Pour ajouter le support des connexions sécurisées (HTTPS/SSL/TLS) dans les applications que vous compilez.
   - **Ce qu'il fournit :**
     - Les en-têtes et bibliothèques nécessaires pour intégrer **OpenSSL**, une bibliothèque utilisée pour le chiffrement des communications.
   - **Exemple d'utilisation :**
     - Pour activer HTTPS dans **Nginx** ou toute autre application nécessitant des connexions sécurisées.

### 4. **zlib1g-dev**
   - **Pourquoi ?**
     - Nécessaire pour ajouter le support de la compression dans les logiciels.
   - **Utilisation courante :**
     - Par exemple, **Nginx** ou **Apache** utilisent zlib pour la compression gzip des pages web.
   - **Différence entre `zlib1g` et `zlib1g-dev` :**
     - `zlib1g` est la bibliothèque runtime (utilisée par des applications déjà compilées).
     - `zlib1g-dev` contient les fichiers nécessaires à la compilation d'applications qui intègrent la compression.

---

### **Conclusion**
Ces bibliothèques sont requises pour :
- Compiler des applications qui nécessitent des fonctionnalités spécifiques comme la gestion des expressions régulières, des connexions sécurisées, ou de la compression.
- Offrir les fichiers de développement nécessaires pour les logiciels qui s'appuient sur des bibliothèques externes (comme les en-têtes `.h` ou les fichiers de liaison).

Ces dépendances sont courantes dans des environnements où vous compilez ou configurez manuellement des outils, plutôt que d'utiliser uniquement des packages précompilés.