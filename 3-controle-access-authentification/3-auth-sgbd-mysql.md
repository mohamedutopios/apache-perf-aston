### **Authentification externe avec Apache HTTPD via SGBD (DBM, MySQL, etc.)**

Apache HTTPD peut utiliser des bases de données externes pour gérer l’authentification des utilisateurs. Cela offre une flexibilité accrue pour les environnements où les informations d’identification des utilisateurs sont déjà stockées dans des systèmes de gestion de base de données (SGBD).

---

### **1. Options d’authentification avec Apache et SGBD**

#### **a. DBM (Database Manager Files)**
- Les fichiers DBM sont des fichiers binaires optimisés pour stocker des paires clé-valeur.
- Apache utilise **`mod_authn_dbm`** pour authentifier les utilisateurs via ces fichiers.

#### **b. MySQL / PostgreSQL**
- Apache peut utiliser des bases de données relationnelles comme MySQL ou PostgreSQL pour gérer l’authentification via **`mod_authn_dbd`**.
- Les informations des utilisateurs (noms d’utilisateurs, mots de passe, rôles) sont stockées dans des tables SQL.

#### **c. SQLite**
- Similaire à MySQL ou PostgreSQL, mais plus léger, SQLite est aussi compatible avec `mod_authn_dbd`.

---

### **2. Authentification avec DBM (`mod_authn_dbm`)**

#### **Étape 1 : Installer les modules nécessaires**
Assurez-vous que le module `mod_authn_dbm` est activé :
```bash
sudo a2enmod authn_dbm
sudo systemctl restart apache2
```

#### **Étape 2 : Créer un fichier DBM**
Utilisez `htdbm` pour créer un fichier DBM avec les utilisateurs :
```bash
htdbm -c /etc/apache2/auth.dbm user1
```
- L'option `-c` crée le fichier. Ajoutez d'autres utilisateurs sans cette option :
  ```bash
  htdbm /etc/apache2/auth.dbm user2
  ```

#### **Étape 3 : Configurer Apache**
Ajoutez la configuration suivante dans un Virtual Host :
```apache
<Directory "/var/www/secure">
    AuthType Basic
    AuthName "Restricted Area"
    AuthBasicProvider dbm
    AuthDBMUserFile /etc/apache2/auth.dbm

    Require valid-user
</Directory>
```

#### **Étape 4 : Tester**
- Accédez au site dans un navigateur.
- Connectez-vous avec un nom d'utilisateur et un mot de passe créés dans le fichier DBM.

---

### **3. Authentification avec MySQL / PostgreSQL (`mod_authn_dbd`)**

#### **Étape 1 : Installer les modules nécessaires**
Assurez-vous que le module `mod_authn_dbd` et le pilote DB sont activés :
```bash
sudo a2enmod authn_dbd
sudo apt install libaprutil1-dbd-mysql # Pour MySQL
sudo systemctl restart apache2
```

#### **Étape 2 : Configurer la base de données**

1. **Créer une table pour les utilisateurs**
   Exemple avec MySQL :
   ```sql
   CREATE DATABASE authdb;
   USE authdb;

   CREATE TABLE users (
       username VARCHAR(50) PRIMARY KEY,
       password VARCHAR(255) NOT NULL
   );

   INSERT INTO users (username, password) VALUES ('john', MD5('password123'));
   INSERT INTO users (username, password) VALUES ('alice', MD5('alicepass'));
   ```

2. **Vérifiez la connectivité avec un utilisateur SQL valide**
   - Nom d’utilisateur : `auth_user`
   - Mot de passe : `auth_password`

---

#### **Étape 3 : Configurer Apache**

1. **Ajouter les informations de connexion à la base**
   Modifiez le fichier `/etc/apache2/conf-available/authn_dbd.conf` (ou créez-le) :
   ```apache
   DBDriver mysql
   DBDParams "host=localhost dbname=authdb user=auth_user pass=auth_password"
   DBDMin 1
   DBDKeep 2
   DBDMax 10
   DBDExptime 300
   ```

   Activez la configuration et redémarrez Apache :
   ```bash
   sudo a2enconf authn_dbd
   sudo systemctl restart apache2
   ```

2. **Configurer le Virtual Host pour l'authentification**
   Ajoutez cette configuration dans le Virtual Host :
   ```apache
   <Directory "/var/www/secure">
       AuthType Basic
       AuthName "Restricted Area"
       AuthBasicProvider dbd
       AuthDBDUserPWQuery "SELECT password FROM users WHERE username = %s"

       Require valid-user
   </Directory>
   ```

   - **`AuthDBDUserPWQuery`** : Spécifie la requête SQL pour valider l’utilisateur.

---

#### **Étape 4 : Tester**

1. Accédez au site dans un navigateur :
   - URL : `https://secure.example.com`
   - Nom d’utilisateur : `john`
   - Mot de passe : `password123`.

2. En cas d'erreur, consultez les logs :
   ```bash
   tail -f /var/log/apache2/error.log
   ```

---

### **4. Authentification avancée avec MySQL : Groupes d'utilisateurs**

Pour restreindre l’accès à des groupes spécifiques, ajoutez une table `user_groups` dans la base de données.

1. **Créer une table de groupes**
   ```sql
   CREATE TABLE user_groups (
       username VARCHAR(50),
       group_name VARCHAR(50),
       PRIMARY KEY (username, group_name)
   );

   INSERT INTO user_groups (username, group_name) VALUES ('john', 'admin');
   INSERT INTO user_groups (username, group_name) VALUES ('alice', 'developer');
   ```

2. **Configurer Apache**
   Ajoutez une requête SQL pour vérifier les groupes :
   ```apache
   AuthDBDUserPWQuery "SELECT password FROM users WHERE username = %s"
   AuthDBDGroupQuery "SELECT group_name FROM user_groups WHERE username = %s"

   <Directory "/var/www/secure">
       Require dbd-group admin
   </Directory>
   ```

---

### **5. Comparaison des méthodes**

| **Méthode**      | **Avantages**                       | **Inconvénients**                  | **Cas d'usage**                   |
|------------------|------------------------------------|------------------------------------|-----------------------------------|
| **DBM**          | Simple, rapide, sans serveur externe| Non évolutif, pas de gestion avancée| Petits environnements.            |
| **MySQL/PostgreSQL** | Flexible, gestion avancée des utilisateurs| Nécessite un serveur DB externe   | Grandes organisations, multi-sites. |
| **SQLite**       | Léger, portable                   | Moins performant pour de gros volumes| Applications légères.             |

---

### **6. Résumé**

- **DBM** : Idéal pour des besoins simples avec peu d’utilisateurs.
- **MySQL/PostgreSQL** : Préféré pour des environnements plus complexes nécessitant des requêtes dynamiques ou la gestion des groupes.
- **Configuration avancée** : Combinez les requêtes SQL pour vérifier les groupes, les rôles, ou d'autres métadonnées utilisateur.

En utilisant Apache avec ces modules, vous pouvez centraliser l’authentification des utilisateurs tout en maintenant une grande flexibilité pour les environnements variés.