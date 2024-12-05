Voici une liste complète des **options de `mod_rewrite`** d'Apache, leurs utilisations, et des exemples pratiques pour chaque cas.

---

## **Options principales dans `mod_rewrite`**

### **1. RewriteEngine**
- **Description** : Active ou désactive le moteur de réécriture.
- **Syntaxe** :
  ```apache
  RewriteEngine On
  ```
- **Cas d'utilisation** : Obligatoire pour que `mod_rewrite` fonctionne.

---

### **2. RewriteBase**
- **Description** : Définit le chemin de base pour les réécritures relatives.
- **Syntaxe** :
  ```apache
  RewriteBase /subdirectory/
  ```
- **Cas d'utilisation** :
  - Utilisé dans des environnements avec des sous-répertoires ou lorsque les chemins relatifs ne fonctionnent pas correctement.
- **Exemple** :
  - Si vous avez un site dans `/var/www/html/subdirectory/` et que vous voulez réécrire `/page` en `/subdirectory/page`.

---

### **3. RewriteCond**
- **Description** : Ajoute des conditions pour que `RewriteRule` soit appliqué.
- **Syntaxe** :
  ```apache
  RewriteCond TEST_CONDITION PATTERN [FLAGS]
  ```
- **Cas d'utilisation** : Permet de spécifier des conditions basées sur :
  - Variables d'environnement.
  - Paramètres d'URL.
  - Agent utilisateur.
  - Adresse IP.

#### **Exemple : Rediriger uniquement si l'utilisateur n'est pas en HTTPS**
```apache
RewriteCond %{HTTPS} !=on
RewriteRule ^(.*)$ https://%{HTTP_HOST}/$1 [R=301,L]
```

---

### **4. RewriteRule**
- **Description** : Définit la règle de réécriture, y compris la correspondance et la cible.
- **Syntaxe** :
  ```apache
  RewriteRule PATTERN TARGET [FLAGS]
  ```
- **Cas d'utilisation** :
  - Réécriture d'URL pour des URLs plus propres.
  - Redirections internes ou externes.

#### **Exemple : Réécriture pour un blog**
```apache
RewriteRule ^blog/([0-9]+)$ post.php?id=$1 [L]
```

---

### **5. Flags (`[FLAGS]`)**
- **Description** : Modifient le comportement des règles.
- **Syntaxe** : Les flags sont spécifiés entre crochets `[ ]`, séparés par des virgules si multiples.
- **Liste des principaux flags et cas d'utilisation** :

| **Flag**   | **Description**                                                                 | **Cas d'utilisation**                                      |
|------------|---------------------------------------------------------------------------------|-----------------------------------------------------------|
| **`L`**    | Arrête le traitement des règles si celle-ci correspond.                        | Empêcher des règles supplémentaires d'être appliquées.    |
| **`R`**    | Effectue une redirection. Valeurs possibles : `R=301` (permanente), `R=302`.   | Rediriger vers un autre domaine ou protocole.             |
| **`P`**    | Proxy : Passe la requête à un serveur backend.                                 | Utilisé pour les proxys ou passerelles.                   |
| **`QSA`**  | Ajoute les paramètres de la requête à la cible (Query String Append).          | Conserver les paramètres GET d'une requête.               |
| **`NC`**   | Insensible à la casse (No Case).                                               | Utilisé pour les correspondances insensibles à la casse.  |
| **`NE`**   | Empêche l'encodage des caractères spéciaux (No Escape).                        | Utile pour les caractères réservés dans les URL.          |
| **`OR`**   | Combine les conditions avec un **OU** logique (par défaut, c'est `ET`).         | Utilisé dans `RewriteCond`.                               |
| **`T`**    | Change le type MIME de la réponse.                                             | Servir un fichier avec un type MIME différent.            |
| **`G`**    | Retourne une erreur `410 Gone`.                                                | Indiquer qu'une ressource a été supprimée.                |
| **`F`**    | Retourne une erreur `403 Forbidden`.                                           | Bloquer l'accès à une ressource.                          |
| **`C`**    | Lien conditionnel entre plusieurs règles (Chain).                              | Utilisé pour enchaîner des règles qui doivent être testées ensemble. |
| **`PT`**   | Passe la requête au gestionnaire suivant (Pass Through).                       | Utilisé avec des alias ou des proxys.                     |

---

## **Variables et conditions disponibles dans `RewriteCond`**

### **1. Variables de serveur**
| **Variable**           | **Description**                                                      |
|-------------------------|----------------------------------------------------------------------|
| `%{HTTP_HOST}`          | Le nom de domaine ou l'adresse IP du serveur.                       |
| `%{REQUEST_URI}`        | L'URI de la requête.                                                |
| `%{QUERY_STRING}`       | Les paramètres GET.                                                 |
| `%{REMOTE_ADDR}`        | Adresse IP de l'utilisateur.                                        |
| `%{HTTP_USER_AGENT}`    | L'agent utilisateur (navigateur).                                   |
| `%{HTTPS}`              | Indique si HTTPS est activé (`on` ou vide).                        |

#### **Exemple : Rediriger en fonction de l'adresse IP**
```apache
RewriteCond %{REMOTE_ADDR} ^192\.168\.1\.100$
RewriteRule ^(.*)$ /blocked.html [L]
```

---

### **2. Expressions régulières**
- **Syntaxe** : Les patterns utilisent des expressions régulières pour correspondre.
- **Caractères courants** :
  | **Caractère** | **Signification**            |
  |---------------|------------------------------|
  | `^`           | Début de l'URI.              |
  | `$`           | Fin de l'URI.                |
  | `(.*)`        | Capturer tout.               |
  | `[abc]`       | Correspond à `a`, `b` ou `c`.|
  | `\d`          | Correspond à un chiffre.     |
  | `\w`          | Correspond à un mot.         |

#### **Exemple : Rediriger tous les fichiers `.php` vers une page statique**
```apache
RewriteRule \.php$ /static.html [L]
```

---

### **3. Conditions temporelles**
- **Variables temporelles** :
  | **Variable**        | **Description**               |
  |---------------------|-------------------------------|
  | `%{TIME_HOUR}`      | L'heure actuelle (00-23).     |
  | `%{TIME_MIN}`       | Les minutes (00-59).          |
  | `%{TIME_SEC}`       | Les secondes (00-59).         |

#### **Exemple : Redirection en dehors des heures d'ouverture**
```apache
RewriteCond %{TIME_HOUR} <09 [OR]
RewriteCond %{TIME_HOUR} >17
RewriteRule ^(.*)$ /closed.html [L]
```

---

## **Cas pratiques : Combinations fréquentes**

### **1. Créer des URLs propres**
**Objectif** : Réécrire `index.php?page=about` en `/about`.
```apache
RewriteEngine On
RewriteRule ^([a-zA-Z0-9_-]+)$ index.php?page=$1 [L]
```

---

### **2. Redirection conditionnelle**
**Objectif** : Rediriger uniquement les utilisateurs mobiles.
```apache
RewriteCond %{HTTP_USER_AGENT} "Mobile" [NC]
RewriteRule ^(.*)$ /mobile/ [L]
```

---

### **3. Blocage d'accès à un fichier**
**Objectif** : Bloquer l'accès à `config.php`.
```apache
RewriteRule ^config\.php$ - [F]
```

---

### **4. Servir une page multilingue**
**Objectif** : Réécrire `/fr/contact` en `contact.php?lang=fr`.
```apache
RewriteRule ^([a-z]{2})/(.*)$ $2.php?lang=$1 [L,QSA]
```

---

### **5. Proxy inversé (reverse proxy)**
**Objectif** : Rediriger vers un serveur backend.
```apache
RewriteRule ^/api/(.*)$ http://backend.local/$1 [P]
```

---

Ces règles couvrent presque tous les scénarios d'utilisation de `mod_rewrite`. Si vous avez un cas particulier ou souhaitez approfondir un exemple, dites-le-moi !