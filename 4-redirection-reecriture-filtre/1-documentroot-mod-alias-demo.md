### **1. DocumentRoot**
#### **Définition** :
- `DocumentRoot` est une directive dans Apache qui spécifie le répertoire racine où le serveur web cherche les fichiers à servir pour un domaine ou un sous-domaine donné.

#### **Usage dans un VirtualHost** :
- Chaque hôte virtuel (`VirtualHost`) peut avoir son propre `DocumentRoot`.
- Exemple :
  ```apache
  <VirtualHost *:80>
      ServerName example.com
      ServerAlias www.example.com
      DocumentRoot "/var/www/html/example"
      <Directory "/var/www/html/example">
          Options Indexes FollowSymLinks
          AllowOverride All
          Require all granted
      </Directory>
  </VirtualHost>
  ```
  - **ServerName** : Nom du domaine principal.
  - **ServerAlias** : Autres noms de domaine pointant vers ce même site.
  - **DocumentRoot** : Chemin où Apache recherche les fichiers pour ce site.
  - **Directory** : Permet de spécifier les permissions et options pour le répertoire associé.

#### **Options courantes pour DocumentRoot** :
- **Indexes** : Affiche une liste des fichiers si aucun fichier `index` n’est trouvé.
- **FollowSymLinks** : Autorise l'utilisation de liens symboliques.
- **AllowOverride** : Permet d'utiliser un fichier `.htaccess` pour modifier la configuration.

#### **Cas pratique** :
Si un fichier `index.html` est placé dans `/var/www/html/example`, il sera servi à l'URL `http://example.com`.

---

### **2. Module mod_alias**
#### **Définition** :
- `mod_alias` est un module d'Apache qui permet :
  - De créer des **alias** pour mapper une URL à un chemin différent sur le système de fichiers.
  - De configurer des **redirections simples** d'une URL vers une autre.

#### **Alias** :
- Permet de servir des fichiers depuis un répertoire différent de `DocumentRoot`.
- Syntaxe :
  ```apache
  Alias /url_path /system_path
  ```
- Exemple :
  ```apache
  Alias /images "/var/www/assets/images"
  <Directory "/var/www/assets/images">
      Options Indexes FollowSymLinks
      AllowOverride None
      Require all granted
  </Directory>
  ```
  - **Alias /images** : Lorsque l'utilisateur accède à `http://example.com/images`, Apache sert les fichiers depuis `/var/www/assets/images`.

#### **Redirect** :
- Redirige une URL vers une autre.
- Syntaxe :
  ```apache
  Redirect [status_code] /source_path http://destination_url
  ```
- Exemple :
  ```apache
  Redirect 301 /old-page.html http://example.com/new-page.html
  ```
  - Redirige de manière permanente (`301`) les requêtes pour `http://example.com/old-page.html` vers `http://example.com/new-page.html`.

#### **RedirectMatch** :
- Redirection conditionnelle utilisant des expressions régulières.
- Exemple :
  ```apache
  RedirectMatch 403 ^/private
  ```
  - Toute URL commençant par `/private` retourne une erreur `403 Forbidden`.

---

### **Exemples pratiques**
#### **1. Combinaison de DocumentRoot et Alias** :
Vous avez deux répertoires :
- `/var/www/html` : Contient votre site principal.
- `/var/www/media` : Contient vos images.

**Configuration** :
```apache
<VirtualHost *:80>
    ServerName example.com
    DocumentRoot "/var/www/html"

    Alias /media "/var/www/media"
    <Directory "/var/www/media">
        Options Indexes FollowSymLinks
        AllowOverride None
        Require all granted
    </Directory>
</VirtualHost>
```
- Accéder à `http://example.com/media` affiche les fichiers de `/var/www/media`, même s'ils ne sont pas dans `DocumentRoot`.

---

#### **2. Redirection avec mod_alias** :
Rediriger tout le contenu d'un ancien site vers un nouveau.
```apache
<VirtualHost *:80>
    ServerName oldsite.com
    Redirect 301 / http://newsite.com/
</VirtualHost>
```
- Toutes les requêtes vers `oldsite.com` sont redirigées vers `newsite.com`.

---

#### **3. Alias pour des applications spécifiques** :
Vous hébergez plusieurs applications sur le même serveur :
- Application principale : `/var/www/html`.
- Interface d'administration : `/var/www/admin`.

**Configuration** :
```apache
<VirtualHost *:80>
    ServerName example.com
    DocumentRoot "/var/www/html"

    Alias /admin "/var/www/admin"
    <Directory "/var/www/admin">
        Options FollowSymLinks
        Require all granted
    </Directory>
</VirtualHost>
```
- Accéder à `http://example.com/admin` charge les fichiers depuis `/var/www/admin`.

---

#### **4. Alias pour des ressources dynamiques** :
Servir des fichiers générés dynamiquement à partir d'un script.
```apache
Alias /dynamic "/var/www/dynamic"
<Directory "/var/www/dynamic">
    Options +ExecCGI
    AddHandler cgi-script .cgi
    Require all granted
</Directory>
```
- Tout fichier `.cgi` dans `/var/www/dynamic` sera exécuté et le résultat envoyé au client.

---

### **Résumé**
- **DocumentRoot** : Définit le répertoire principal où sont stockés les fichiers du site.
- **mod_alias** :
  - `Alias` : Permet de mapper une URL à un chemin sur le système de fichiers.
  - `Redirect` : Simplifie les redirections.
  - `RedirectMatch` : Gère les redirections conditionnelles avec des expressions régulières.

Besoin de plus d'exemples spécifiques ou d'autres cas pratiques ?