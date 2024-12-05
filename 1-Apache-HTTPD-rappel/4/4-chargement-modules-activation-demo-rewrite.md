### **Démonstration complète : Installation, configuration de PHP, mise en place du projet web et gestion avec et sans `mod_rewrite`**

---

### **Étape 1 : Installer Apache et PHP**

#### **1. Installer Apache**
```bash
sudo apt update
sudo apt install apache2 -y
```

#### **2. Installer PHP et le module Apache pour PHP**
```bash
sudo apt install php libapache2-mod-php -y
```

#### **3. Vérifier l'installation**
- Vérifiez la version d'Apache :
  ```bash
  apache2 -v
  ```
- Vérifiez la version de PHP :
  ```bash
  php -v
  ```

---

### **Étape 2 : Configuration d'Apache**

#### **1. Activer `mod_rewrite`**
Activez le module avec :
```bash
sudo a2enmod rewrite
sudo systemctl reload apache2
```

#### **2. Configurer le Virtual Host**
Modifiez le fichier de configuration par défaut :
```bash
sudo nano /etc/apache2/sites-available/000-default.conf
```

Ajoutez ou modifiez les lignes suivantes :
```apache
<VirtualHost *:80>
    ServerName localhost
    DocumentRoot /var/www/html

    <Directory /var/www/html>
        AllowOverride All
        Require all granted
    </Directory>
</VirtualHost>
```

Rechargez Apache :
```bash
sudo systemctl reload apache2
```

---

### **Étape 3 : Créer le projet PHP**

#### **Structure du projet**
```
/var/www/html/
├── .htaccess
├── index.php
├── pages/
│   ├── contact.php
│   ├── about.php
│   └── home.php
```

#### **1. Créer les fichiers PHP**

**Fichier principal : `index.php`**
```php
<?php
// index.php

$page = isset($_GET['page']) ? $_GET['page'] : 'home';

// Valider les pages disponibles
$allowed_pages = ['contact', 'about', 'home'];

if (in_array($page, $allowed_pages)) {
    include "pages/$page.php";
} else {
    include "pages/home.php"; // Par défaut, on affiche la page d'accueil
}
?>
```

**Page d'accueil : `pages/home.php`**
```php
<h1>Bienvenue sur notre site !</h1>
<p>Ceci est la page d'accueil.</p>
<a href="/contact">Contactez-nous</a> | <a href="/about">À propos</a>
```

**Page de contact : `pages/contact.php`**
```php
<h1>Contactez-nous</h1>
<p>Envoyez-nous un email à contact@example.com.</p>
<a href="/">Retour à l'accueil</a>
```

**Page "À propos" : `pages/about.php`**
```php
<h1>À propos de nous</h1>
<p>Nous sommes une entreprise dédiée à la qualité.</p>
<a href="/">Retour à l'accueil</a>
```

---

### **Étape 4 : Configurer `.htaccess`**

Créez un fichier **`.htaccess`** :
```bash
sudo nano /var/www/html/.htaccess
```

Ajoutez les règles suivantes :
```apache
RewriteEngine On

# Réécrire "contact" vers "index.php?page=contact"
RewriteRule ^contact$ index.php?page=contact [L]

# Réécrire "about" vers "index.php?page=about"
RewriteRule ^about$ index.php?page=about [L]

# Rediriger "/" vers la page d'accueil
RewriteRule ^$ index.php?page=home [L]
```

---

### **Étape 5 : Tester avec et sans `mod_rewrite`**

#### **1. Tester sans `mod_rewrite`**
Désactivez `mod_rewrite` :
```bash
sudo a2dismod rewrite
sudo systemctl reload apache2
```

- **Accéder aux pages via des URLs complexes :**
  - Accédez à l'accueil :
    ```
    http://localhost/index.php?page=home
    ```
  - Accédez à la page de contact :
    ```
    http://localhost/index.php?page=contact
    ```

---

#### **2. Tester avec `mod_rewrite`**
Activez `mod_rewrite` :
```bash
sudo a2enmod rewrite
sudo systemctl reload apache2
```

- **Accéder aux pages via des URLs simplifiées :**
  - Accédez à l'accueil :
    ```
    http://localhost/
    ```
  - Accédez à la page de contact :
    ```
    http://localhost/contact
    ```

---

### **Étape 6 : Résolution des problèmes courants**

1. **Erreur 500 :**
   - Vérifiez les permissions de `.htaccess` :
     ```bash
     sudo chmod 644 /var/www/html/.htaccess
     ```

2. **Fichier PHP affiché en clair :**
   - Assurez-vous que `libapache2-mod-php` est installé et activé.

3. **Module non activé :**
   - Assurez-vous que `mod_rewrite` est activé avec :
     ```bash
     apache2ctl -M | grep rewrite
     ```

---

### **Résumé : Avantages de `mod_rewrite`**
- URLs plus propres et lisibles pour les utilisateurs.
- Amélioration du SEO grâce à des URLs simplifiées.
- Séparation claire entre logique d'application et configuration serveur.

Ce projet vous permet de voir concrètement les différences entre l'utilisation et l'absence du module `mod_rewrite` et la gestion des routes avec PHP.