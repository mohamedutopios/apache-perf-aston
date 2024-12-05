### **Démonstration simple d'utilisation du contexte `<Directory>`**

#### **Contexte :**
Vous souhaitez configurer Apache pour :
1. Autoriser l'accès au répertoire `/var/www/html/public`.
2. Bloquer l'accès au répertoire `/var/www/html/private`.
3. Utiliser le contexte `<Directory>` pour gérer les autorisations.

---

### **Configuration simple**

#### **1. Modifier la configuration principale**

Ouvrez le fichier de configuration principal d'Apache (par exemple, `/etc/apache2/sites-available/000-default.conf` sur Ubuntu ou `/etc/httpd/conf/httpd.conf` sur CentOS) :

```bash
sudo nano /etc/apache2/sites-available/000-default.conf
```

Ajoutez ou modifiez les directives suivantes :

```apache
<VirtualHost *:80>
    ServerName example.com
    DocumentRoot /var/www/html

    # Autoriser l'accès au répertoire public
    <Directory "/var/www/html/public">
        Require all granted
    </Directory>

    # Bloquer l'accès au répertoire private
    <Directory "/var/www/html/private">
        Require all denied
    </Directory>

    ErrorLog ${APACHE_LOG_DIR}/error.log
    CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>
```

#### **2. Créer les répertoires**

Assurez-vous que les répertoires `public` et `private` existent :

```bash
sudo mkdir -p /var/www/html/public
sudo mkdir -p /var/www/html/private
```

Ajoutez un fichier de test dans chaque répertoire :

- Pour le répertoire public :
  ```bash
  echo "Bienvenue dans le dossier public !" | sudo tee /var/www/html/public/index.html
  ```

- Pour le répertoire private :
  ```bash
  echo "Vous ne devriez pas voir ce contenu." | sudo tee /var/www/html/private/index.html
  ```

#### **3. Redémarrer Apache**

Rechargez ou redémarrez Apache pour appliquer les changements :

```bash
sudo systemctl reload apache2  # Sur Ubuntu/Debian
sudo systemctl reload httpd    # Sur CentOS/RHEL
```

---

### **Tester le résultat**

1. **Accéder au répertoire public** :
   - URL : `http://example.com/public/`
   - Résultat attendu : La page affiche "Bienvenue dans le dossier public !".

2. **Accéder au répertoire private** :
   - URL : `http://example.com/private/`
   - Résultat attendu : Une erreur 403 (Accès refusé).

---

### **Explications**

1. **Contexte `<Directory>`** :
   - Le contexte `<Directory>` permet de définir des permissions spécifiques pour un chemin sur le système de fichiers.

2. **`Require all granted`** :
   - Autorise l'accès à tout le monde pour le répertoire `/var/www/html/public`.

3. **`Require all denied`** :
   - Bloque complètement l'accès au répertoire `/var/www/html/private`.

---

### **Résumé des étapes simples**

1. Configurez le contexte `<Directory>` pour gérer les permissions.
2. Créez les répertoires nécessaires et ajoutez des fichiers de test.
3. Rechargez Apache et testez l'accès.

Cette démonstration illustre une utilisation de base du contexte `<Directory>` pour gérer l'accès aux répertoires de manière simple et claire.