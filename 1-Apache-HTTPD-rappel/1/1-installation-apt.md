Voici une procédure complète pour installer Apache à l'aide de **`apt`** sur une distribution basée sur Debian/Ubuntu. L'installation via `apt` est simple et configure automatiquement les services nécessaires.

---

### **Étapes pour une installation complète d'Apache avec apt**

#### **1. Mettre à jour les paquets**
Avant toute installation, mettez à jour les informations des paquets pour garantir que vous obtenez la dernière version disponible :

```bash
sudo apt update
sudo apt upgrade -y
```

---

#### **2. Installer Apache**
Installez Apache HTTP Server avec la commande suivante :

```bash
sudo apt install apache2 -y
```

---

#### **3. Vérifier l'installation**
Vérifiez qu’Apache est bien installé et en cours d’exécution :

```bash
sudo systemctl status apache2
```

Vous devriez voir une sortie indiquant que le service est **`active (running)`**.

---

#### **4. Tester Apache**
1. **Dans un navigateur** : Accédez à votre serveur à l'adresse suivante :
   - Si c'est en local : [http://localhost](http://localhost) ou [http://127.0.0.1](http://127.0.0.1).
   - Si c'est une machine distante : [http://<adresse_IP>](http://<adresse_IP>).
2. Vous devriez voir une page intitulée **"Apache2 Ubuntu Default Page"**.

---

#### **5. Configurer le pare-feu**
Si vous utilisez un pare-feu (comme UFW), ouvrez le port 80 (HTTP) et 443 (HTTPS) pour permettre l'accès au serveur :

```bash
sudo ufw allow 'Apache Full'
sudo ufw enable
sudo ufw status
```

---

#### **6. Activer les modules nécessaires**
Apache inclut plusieurs modules utiles. Voici les commandes pour activer les modules les plus courants :

1. **Module SSL** (pour HTTPS) :
   ```bash
   sudo a2enmod ssl
   ```

2. **Module Rewrite** (pour les réécritures d’URL comme dans WordPress) :
   ```bash
   sudo a2enmod rewrite
   ```

3. Redémarrez Apache pour appliquer les changements :
   ```bash
   sudo systemctl restart apache2
   ```

---

#### **7. Configurer un site**
1. **Créer un fichier de configuration pour un site**
   - Exemple : `/etc/apache2/sites-available/monsite.conf`
   ```bash
   sudo nano /etc/apache2/sites-available/monsite.conf
   ```

   Contenu typique :
   ```apache
   <VirtualHost *:80>
       ServerName www.example.com
       ServerAlias example.com
       DocumentRoot /var/www/monsite
       ErrorLog ${APACHE_LOG_DIR}/error.log
       CustomLog ${APACHE_LOG_DIR}/access.log combined
   </VirtualHost>
   ```

2. **Créer le répertoire du site** :
   ```bash
   sudo mkdir -p /var/www/monsite
   ```

3. **Attribuer les permissions** :
   ```bash
   sudo chown -R www-data:www-data /var/www/monsite
   sudo chmod -R 755 /var/www/monsite
   ```

4. **Activer le site** :
   ```bash
   sudo a2ensite monsite.conf
   sudo systemctl reload apache2
   ```

---

#### **8. Activer HTTPS avec un certificat SSL (Let’s Encrypt)**
1. Installez l'outil `certbot` pour gérer les certificats SSL :
   ```bash
   sudo apt install certbot python3-certbot-apache -y
   ```

2. Configurez automatiquement HTTPS pour votre site avec :
   ```bash
   sudo certbot --apache
   ```

3. Suivez les instructions pour générer un certificat SSL.

4. Testez le renouvellement automatique des certificats :
   ```bash
   sudo certbot renew --dry-run
   ```

---

#### **9. Vérifier la configuration**
1. Vérifiez la syntaxe des fichiers de configuration Apache :
   ```bash
   sudo apache2ctl configtest
   ```

2. Si tout est correct, redémarrez Apache :
   ```bash
   sudo systemctl restart apache2
   ```

---

### **Outils et emplacements importants**
- **Fichiers de configuration des sites** :
  - **Disponibles** : `/etc/apache2/sites-available/`
  - **Activés** : `/etc/apache2/sites-enabled/`
- **Modules disponibles** : `/etc/apache2/mods-available/`
- **Répertoire des journaux** : `/var/log/apache2/`

---

### **Résumé des commandes principales**
- **Installer Apache** : `sudo apt install apache2`
- **Activer un module** : `sudo a2enmod <module>`
- **Activer un site** : `sudo a2ensite <site>`
- **Vérifier la configuration** : `sudo apache2ctl configtest`
- **Redémarrer Apache** : `sudo systemctl restart apache2`

Avec ces étapes, vous aurez une installation complète et fonctionnelle d'Apache prête pour la production ou le développement.