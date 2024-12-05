Voici un guide détaillé pour mettre en place la démonstration avec Apache sur une VM Ubuntu dans VirtualBox, en configurant les redirections et les filtres demandés. Vous pourrez tester toutes les options configurées dans un environnement contrôlé.

---

### **1. Préparation de la VM Ubuntu**

#### **1.1 Installer Ubuntu dans VirtualBox**
1. Téléchargez une image ISO d'Ubuntu Server ou Desktop depuis [le site officiel](https://ubuntu.com/download).
2. Créez une nouvelle VM dans VirtualBox :
   - **Nom** : `Ubuntu-Apache`.
   - **Mémoire** : Au moins 2 Go.
   - **Disque dur** : 20 Go minimum.
3. Démarrez la VM et installez Ubuntu depuis l'ISO.

#### **1.2 Configuration Réseau**
1. Configurez l'adaptateur réseau en mode "Bridged Adapter" ou "NAT avec port forwarding".
2. Si vous utilisez NAT, ajoutez une règle de redirection de ports dans VirtualBox :
   - Port hôte : `8080`
   - Port invité : `80`

#### **1.3 Mise à Jour du Système**
Connectez-vous à la VM et mettez à jour les paquets :
```bash
sudo apt update && sudo apt upgrade -y
```

---

### **2. Installation d'Apache**

#### **2.1 Installer Apache**
Exécutez les commandes suivantes pour installer Apache :
```bash
sudo apt install apache2 -y
```

#### **2.2 Vérifier l'Installation**
Lancez Apache et vérifiez son état :
```bash
sudo systemctl start apache2
sudo systemctl enable apache2
sudo systemctl status apache2
```
Testez dans un navigateur en accédant à `http://<IP_VM>` (ou `http://localhost:8080` si NAT est utilisé).

---

### **3. Configuration de la Démonstration**

#### **3.1 Activer les Modules Requis**
Activez les modules nécessaires pour les redirections et les filtres :
```bash
sudo a2enmod rewrite
sudo a2enmod headers
sudo a2enmod filter
sudo a2enmod deflate
sudo systemctl restart apache2
```

#### **3.2 Créer un VirtualHost**
Créez un fichier de configuration pour le site :
```bash
sudo nano /etc/apache2/sites-available/demo.conf
```
Ajoutez la configuration suivante :

```apache
<VirtualHost *:80>
    ServerName demo.local
    DocumentRoot /var/www/demo

    # Redirection HTTP -> HTTPS
    RewriteEngine On
    RewriteCond %{HTTPS} off
    RewriteRule ^(.*)$ https://%{HTTP_HOST}/$1 [R=301,L]

    # Alias pour contenu statique
    Alias /static "/var/www/static-content"
    <Directory "/var/www/static-content">
        Options Indexes FollowSymLinks
        Require all granted
    </Directory>

    # Redirection permanente pour anciennes URLs
    Redirect 301 /old-page.html /new-page.html
    Redirect 301 /blog/old-post /blog/new-post

    # En-têtes HTTP de sécurité
    Header always set X-Content-Type-Options "nosniff"
    Header always set X-Frame-Options "DENY"
    Header always set Content-Security-Policy "default-src 'self'"

    # Réécriture d'URL dynamique
    RewriteRule ^product/([0-9]+)$ /product-details.php?id=$1 [L]

    # Compression GZIP
    FilterDeclare Compress
    FilterProvider Compress DEFLATE "%{Content_Type} = 'text/html'"
    FilterProvider Compress DEFLATE "%{Content_Type} = 'text/css'"
    FilterProvider Compress DEFLATE "%{Content_Type} = 'application/javascript'"
    FilterChain Compress

    # Désactiver la mise en cache pour certains fichiers
    <FilesMatch "\.(json|xml|txt)$">
        Header set Cache-Control "no-store, no-cache, must-revalidate, proxy-revalidate, max-age=0"
        Header set Pragma "no-cache"
        Header set Expires "0"
    </FilesMatch>
</VirtualHost>
```

#### **3.3 Activer le VirtualHost**
Activez le site et désactivez le site par défaut :
```bash
sudo a2ensite demo.conf
sudo a2dissite 000-default.conf
sudo systemctl reload apache2
```

---

### **4. Préparation des Répertoires et Contenus**

#### **4.1 Créer les Répertoires**
Créez les répertoires pour les fichiers du site et du contenu statique :
```bash
sudo mkdir -p /var/www/demo
sudo mkdir -p /var/www/static-content
```

#### **4.2 Ajouter des Fichiers**
Ajoutez des fichiers pour tester les fonctionnalités.

- Fichier principal (`/var/www/demo/index.html`) :
  ```html
  <!DOCTYPE html>
  <html lang="en">
  <head>
      <meta charset="UTF-8">
      <meta name="viewport" content="width=device-width, initial-scale=1.0">
      <title>Demo Site</title>
  </head>
  <body>
      <h1>Welcome to the Demo Site</h1>
      <p><a href="/static/example.txt">Static Content</a></p>
  </body>
  </html>
  ```

- Contenu statique (`/var/www/static-content/example.txt`) :
  ```txt
  This is a static text file served via the /static alias.
  ```

- Fichiers pour les règles dynamiques :
  - `/var/www/demo/product-details.php` :
    ```php
    <?php
    echo "Product Details for ID: " . htmlspecialchars($_GET['id']);
    ?>
    ```

#### **4.3 Appliquer les Permissions**
```bash
sudo chown -R www-data:www-data /var/www/demo /var/www/static-content
sudo chmod -R 755 /var/www/demo /var/www/static-content
```

---

### **5. Ajouter un Nom de Domaine Local**
Ajoutez un enregistrement dans le fichier `hosts` pour utiliser `demo.local` dans le navigateur.

#### **Sur la Machine Locale (hôte)** :
Modifiez le fichier `hosts` :
```bash
sudo nano /etc/hosts
```
Ajoutez :
```
<IP_VM> demo.local
```
Remplacez `<IP_VM>` par l’adresse IP de la VM.

---

### **6. Tests et Vérifications**

#### **6.1 Tester dans le Navigateur**
1. Accédez à `http://demo.local` et vérifiez la redirection vers `https://demo.local`.
2. Testez les URLs suivantes :
   - `https://demo.local/old-page.html` → Redirige vers `/new-page.html`.
   - `https://demo.local/product/123` → Affiche `Product Details for ID: 123`.
   - `https://demo.local/static/example.txt` → Sert le contenu du fichier texte.

#### **6.2 Vérifier les En-Têtes**
Utilisez `curl` pour inspecter les en-têtes HTTP :
```bash
curl -I https://demo.local
```
Vérifiez :
- `X-Content-Type-Options: nosniff`
- `X-Frame-Options: DENY`
- `Content-Security-Policy: default-src 'self'`

#### **6.3 Tester la Compression**
Vérifiez si la compression GZIP est activée :
```bash
curl -H "Accept-Encoding: gzip" -I https://demo.local
```
Regardez si l'en-tête `Content-Encoding: gzip` est présent.

---

### **Résumé**
Vous avez maintenant une démonstration fonctionnelle d'Apache sur Ubuntu dans VirtualBox, avec :
- Redirections (HTTP vers HTTPS, redirections permanentes).
- Réécriture d’URL dynamique.
- Configuration d’alias.
- Filtres de compression et de sécurité.

Si vous rencontrez des problèmes ou souhaitez personnaliser davantage la configuration, faites-le moi savoir !