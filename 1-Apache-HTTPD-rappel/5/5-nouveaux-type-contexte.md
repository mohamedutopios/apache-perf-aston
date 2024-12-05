Les **nouveaux types de contextes** dans Apache HTTPD font référence aux contextes où des directives spécifiques peuvent être utilisées ou configurées. Ces contextes sont une manière de structurer la configuration du serveur en fonction de l’endroit ou du niveau où les directives s’appliquent.

Voici un aperçu des contextes existants et des "nouveaux types de contextes" qui émergent ou sont davantage utilisés dans des versions modernes d'Apache.

---

### **1. Contextes standards existants dans Apache**

#### **a. Serveur global (`server config`)**
- **Description** :
  - Les directives s'appliquent à l'ensemble du serveur.
  - Configuré directement dans le fichier principal (`httpd.conf` ou `apache2.conf`).
- **Exemple** :
  ```apache
  ServerRoot "/etc/httpd"
  Listen 80
  ```

#### **b. Virtual Host (`virtual host`)**
- **Description** :
  - Les directives s'appliquent à un hôte virtuel spécifique (une configuration pour chaque domaine ou sous-domaine).
  - Définies dans les fichiers comme `/etc/apache2/sites-available/example.conf`.
- **Exemple** :
  ```apache
  <VirtualHost *:80>
      ServerName www.example.com
      DocumentRoot /var/www/example
  </VirtualHost>
  ```

#### **c. Répertoires (`directory`)**
- **Description** :
  - Les directives s'appliquent à des répertoires ou chemins spécifiques du système de fichiers.
- **Exemple** :
  ```apache
  <Directory "/var/www/html">
      AllowOverride All
      Require all granted
  </Directory>
  ```

#### **d. Emplacement URL (`location`)**
- **Description** :
  - Les directives s'appliquent à des parties spécifiques de l'URL.
- **Exemple** :
  ```apache
  <Location "/admin">
      Require ip 192.168.1.0/24
  </Location>
  ```

#### **e. Fichiers (`files`)**
- **Description** :
  - Les directives s'appliquent à des fichiers spécifiques.
- **Exemple** :
  ```apache
  <Files "secret.html">
      Require all denied
  </Files>
  ```

#### **f. Module spécifique (`ifmodule`)**
- **Description** :
  - Les directives ne s’appliquent que si un module spécifique est activé.
- **Exemple** :
  ```apache
  <IfModule mod_ssl.c>
      SSLEngine On
  </IfModule>
  ```

---

### **2. Nouveaux types ou usages de contextes dans Apache**

Avec les versions modernes d'Apache HTTPD (2.4+), de nouveaux types de contextes ou des fonctionnalités avancées ont émergé.

---

#### **a. Contexte conditionnel (`if`)**
- **Description** :
  - Permet de conditionner les directives en fonction d'expressions dynamiques (comme des variables d'environnement, des conditions IP, etc.).
- **Exemple** :
  ```apache
  <If "%{HTTP_HOST} == 'example.com'">
      DocumentRoot "/var/www/example"
  </If>
  ```
- **Utilité** :
  - Flexibilité accrue dans les configurations.
  - Simplifie les scénarios complexes comme la gestion de plusieurs domaines ou configurations spécifiques.

---

#### **b. Contexte utilisateur (`userdir`)**
- **Description** :
  - Définit les règles pour les répertoires personnels des utilisateurs.
- **Exemple** :
  ```apache
  <IfModule mod_userdir.c>
      UserDir public_html
      <Directory "/home/*/public_html">
          AllowOverride FileInfo AuthConfig
          Require all granted
      </Directory>
  </IfModule>
  ```
- **Utilité** :
  - Permet aux utilisateurs du système d'avoir leurs propres sites web accessibles via `/~username`.

---

#### **c. Contexte fichier spécifique (`filesmatch`)**
- **Description** :
  - S'applique aux fichiers correspondant à des motifs spécifiques (par exemple, via regex).
- **Exemple** :
  ```apache
  <FilesMatch "\.(txt|log)$">
      Require all denied
  </FilesMatch>
  ```
- **Utilité** :
  - Utile pour protéger certains types de fichiers sensibles (comme `.env`, `.htpasswd`, etc.).

---

#### **d. Contexte dynamique pour les proxys (`proxy`)**
- **Description** :
  - Utilisé pour gérer des directives spécifiques aux connexions proxy ou reverse proxy.
- **Exemple** :
  ```apache
  <Proxy "http://backend.example.com">
      Require ip 192.168.0.0/24
  </Proxy>
  ```
- **Utilité** :
  - Permet un contrôle précis des connexions aux proxys.

---

#### **e. Contexte dynamique par moteur de règles (`ifdefine` et `ifenv`)**
- **Description** :
  - Conditionne les directives en fonction des variables définies ou de l'environnement.
- **Exemples** :
  - **IfDefine** :
    ```apache
    <IfDefine DEBUG>
        LogLevel debug
    </IfDefine>
    ```
    - Utilisé pour activer/désactiver des configurations spécifiques en ligne de commande :
      ```bash
      apache2ctl -D DEBUG
      ```

  - **IfEnv** :
    ```apache
    <IfEnv TEST_ENV>
        DocumentRoot "/var/www/test"
    </IfEnv>
    ```

---

#### **f. Contexte par balises HTML intégrées (`htaccess inline`)**
- **Description** :
  - Directives placées directement dans un fichier `.htaccess`.
- **Exemple** :
  ```apache
  RewriteEngine On
  RewriteRule ^about$ /about.html [L]
  ```

---

### **3. Bonnes pratiques dans l’utilisation des contextes**

1. **Minimisez l’utilisation des fichiers `.htaccess`** :
   - Préférez la configuration dans les fichiers de Virtual Host ou dans le fichier principal (`httpd.conf`).
   - `.htaccess` ajoute une surcharge, car Apache le charge à chaque requête.

2. **Utilisez des directives conditionnelles (`<If>` et `<IfModule>`)** pour les configurations dynamiques ou spécifiques.

3. **Protégez les fichiers sensibles avec des contextes `FilesMatch`** :
   - Exemple pour protéger `.htpasswd` :
     ```apache
     <FilesMatch "^\.ht">
         Require all denied
     </FilesMatch>
     ```

4. **Planifiez et organisez vos Virtual Hosts** :
   - Placez un fichier distinct par domaine dans `/etc/apache2/sites-available/` ou `/etc/httpd/conf.d/`.

---

### **Résumé des nouveaux types de contextes**

| **Contexte**       | **Usage principal**                                                                 |
|---------------------|-------------------------------------------------------------------------------------|
| `<If>`             | Conditions dynamiques basées sur des variables ou expressions (ex. domaine, IP).   |
| `<FilesMatch>`     | S'applique aux fichiers correspondant à un motif spécifique (regex).               |
| `<Proxy>`          | Contrôle des proxys et reverse-proxys.                                             |
| `<IfDefine>`       | Activé/désactivé en fonction de variables définies au démarrage d'Apache.          |
| `<IfEnv>`          | Activé/désactivé en fonction des variables d'environnement.                        |

Ces nouveaux contextes offrent une flexibilité accrue et simplifient la gestion de configurations complexes dans Apache.