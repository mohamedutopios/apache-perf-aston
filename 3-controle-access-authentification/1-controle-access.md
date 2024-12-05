### **Contrôle d'accès et authentification avec Apache : Modules `mod_authz*`**

Apache HTTPD 2.4 offre une gestion flexible et puissante du contrôle d'accès grâce aux modules **`mod_authz_*`**, qui permettent de restreindre l'accès aux ressources en fonction de différents critères (adresses IP, utilisateurs, groupes, etc.).

---

### **1. Introduction aux modules `mod_authz*`**

Les modules **`mod_authz_*`** sont utilisés pour définir les règles d'autorisation dans Apache HTTPD. Ils remplacent les anciennes directives (`Allow`, `Deny`, `Order`) utilisées dans les versions antérieures à 2.4.

#### **Principaux modules `mod_authz*`**
| **Module**               | **Description**                                                                 |
|---------------------------|-------------------------------------------------------------------------------|
| **`mod_authz_host`**      | Autorisation basée sur des adresses IP ou des noms d'hôte.                   |
| **`mod_authz_user`**      | Autorisation basée sur des utilisateurs définis.                             |
| **`mod_authz_groupfile`** | Autorisation basée sur des groupes d'utilisateurs définis dans un fichier.   |
| **`mod_authz_core`**      | Base pour toutes les règles d'autorisation, fournit les directives `Require`.|
| **`mod_authz_owner`**     | Autorisation basée sur le propriétaire du fichier.                           |

---

### **2. Syntaxe des règles d'autorisation dans Apache 2.4**

#### **Directive principale : `Require`**
La directive `Require` détermine qui est autorisé à accéder à une ressource.

#### **Syntaxe de base :**
```apache
Require <type> <value>
```

- **`<type>`** : Spécifie le type de contrôle (utilisateur, IP, etc.).
- **`<value>`** : Les valeurs autorisées pour ce type.

#### **Exemple :**
```apache
Require ip 192.168.1.0/24
Require user admin
```

---

### **3. Contrôle d'accès avec `mod_authz_host`**

Le module **`mod_authz_host`** contrôle l'accès en fonction des adresses IP ou des noms d'hôte.

#### **Directives disponibles :**
| **Directive** | **Description**                          |
|---------------|------------------------------------------|
| `Require ip`  | Autorise l'accès aux adresses IP spécifiées. |
| `Require host`| Autorise l'accès aux noms d'hôte spécifiés.|

#### **Exemples :**

- **Autoriser uniquement une adresse IP spécifique** :
  ```apache
  <Directory "/var/www/secure">
      Require ip 192.168.1.100
  </Directory>
  ```

- **Autoriser une plage d'adresses IP** :
  ```apache
  <Directory "/var/www/secure">
      Require ip 192.168.1.0/24
  </Directory>
  ```

- **Autoriser un nom d'hôte** :
  ```apache
  <Directory "/var/www/secure">
      Require host example.com
  </Directory>
  ```

- **Interdire tout accès sauf une IP** :
  ```apache
  <Directory "/var/www/secure">
      Require all denied
      Require ip 192.168.1.100
  </Directory>
  ```

---

### **4. Contrôle d'accès avec `mod_authz_user`**

Le module **`mod_authz_user`** permet de restreindre l'accès en fonction des utilisateurs authentifiés.

#### **Directive disponible :**
| **Directive**   | **Description**                          |
|-----------------|------------------------------------------|
| `Require user`  | Autorise l'accès uniquement aux utilisateurs spécifiés.|

#### **Exemples :**

- **Autoriser un utilisateur spécifique** :
  ```apache
  <Directory "/var/www/secure">
      Require user admin
  </Directory>
  ```

- **Autoriser plusieurs utilisateurs** :
  ```apache
  <Directory "/var/www/secure">
      Require user admin john alice
  </Directory>
  ```

---

### **5. Contrôle d'accès avec `mod_authz_groupfile`**

Le module **`mod_authz_groupfile`** gère l'accès basé sur des groupes d'utilisateurs définis dans un fichier.

#### **Configuration :**

1. **Créer un fichier de groupes** :
   Fichier : `/etc/apache2/groups`
   ```txt
   admin: john alice
   developers: mike sara
   ```

2. **Configurer Apache** :
   - Ajouter la directive pour spécifier le fichier des groupes :
     ```apache
     AuthGroupFile /etc/apache2/groups
     ```

   - Autoriser un groupe spécifique :
     ```apache
     <Directory "/var/www/secure">
         Require group admin
     </Directory>
     ```

---

### **6. Contrôle d'accès avancé avec `mod_authz_core`**

Le module **`mod_authz_core`** introduit des conditions logiques pour combiner des règles d'autorisation.

#### **Opérateurs disponibles :**
| **Opérateur**       | **Description**                                               |
|---------------------|---------------------------------------------------------------|
| `RequireAll`        | Toutes les conditions doivent être vraies.                    |
| `RequireAny`        | Au moins une des conditions doit être vraie.                  |
| `RequireNone`       | Aucune des conditions ne doit être vraie.                     |

#### **Exemples :**

- **Combinaison avec `RequireAll`** :
  - Autorise uniquement l'utilisateur `admin` depuis une IP spécifique.
    ```apache
    <Directory "/var/www/secure">
        <RequireAll>
            Require user admin
            Require ip 192.168.1.100
        </RequireAll>
    </Directory>
    ```

- **Combinaison avec `RequireAny`** :
  - Autorise soit l'utilisateur `admin`, soit les connexions depuis un réseau spécifique.
    ```apache
    <Directory "/var/www/secure">
        <RequireAny>
            Require user admin
            Require ip 192.168.1.0/24
        </RequireAny>
    </Directory>
    ```

- **Interdire tout sauf une IP spécifique** :
  ```apache
  <Directory "/var/www/secure">
      <RequireNone>
          Require ip 10.0.0.0/8
      </RequireNone>
      Require ip 192.168.1.100
  </Directory>
  ```

---

### **7. Meilleures pratiques**

1. **Toujours tester la configuration** :
   Avant de recharger Apache, vérifiez la syntaxe :
   ```bash
   apachectl configtest
   ```

2. **Documenter les règles** :
   - Incluez des commentaires dans votre configuration pour expliquer les règles d'accès.

3. **Combiner avec SSL/TLS** :
   - Utilisez `mod_ssl` pour sécuriser les connexions lors de l'utilisation de `mod_authz_user` ou `mod_authz_groupfile`.

4. **Minimiser l’accès par défaut** :
   - Utilisez `Require all denied` comme base et ajoutez des exceptions pour limiter l'exposition.

---

### **8. Exemple complet : Contrôle d'accès avancé**

Voici un exemple combinant plusieurs modules `mod_authz_*` :

```apache
<VirtualHost *:443>
    ServerName secure.example.com
    DocumentRoot "/var/www/secure"

    SSLEngine On
    SSLCertificateFile "/etc/ssl/certs/example.com.crt"
    SSLCertificateKeyFile "/etc/ssl/private/example.com.key"

    <Directory "/var/www/secure">
        # Interdire tout accès par défaut
        Require all denied

        # Autoriser les utilisateurs du groupe 'admin' depuis le réseau local
        <RequireAll>
            Require group admin
            Require ip 192.168.1.0/24
        </RequireAll>

        # Autoriser l'utilisateur 'john' quelle que soit son IP
        RequireAny user john
    </Directory>

    ErrorLog /var/log/apache2/secure-error.log
    CustomLog /var/log/apache2/secure-access.log combined
</VirtualHost>
```

---

### **Résumé**

Les modules **`mod_authz_*`** permettent une gestion fine et flexible des règles de contrôle d'accès. Ils couvrent une large gamme de besoins, des contrôles IP simples aux combinaisons complexes de groupes et d'utilisateurs authentifiés. Avec des directives comme `Require`, Apache HTTPD 2.4 offre une approche moderne, puissante et modulaire pour sécuriser les ressources.