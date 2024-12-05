Le module utilisé pour gérer uniquement les **redirections simples** dans Apache est le module **`mod_alias`**. Ce module est plus simple à configurer que `mod_rewrite` et convient parfaitement pour des redirections directes sans logique conditionnelle complexe.

---

## **1. Activation du module `mod_alias`**

1. **Activer le module (si non activé)** :
   ```bash
   sudo a2enmod alias
   sudo systemctl restart apache2
   ```

2. Vérifiez qu'il est activé :
   ```bash
   apachectl -M | grep alias
   ```
   Vous devriez voir `alias_module` dans la liste.

---

## **2. Commandes principales dans `mod_alias`**

### **Redirect**
- **Description** : Redirige une URL vers une nouvelle URL.
- **Syntaxe** :
  ```apache
  Redirect [status] [URL_PATH] [TARGET_URL]
  ```
  - **`status`** : Le code HTTP (par défaut : `302`). Peut être `301`, `302`, ou d'autres codes de redirection.
  - **`URL_PATH`** : Le chemin relatif sur le serveur.
  - **`TARGET_URL`** : L'URL vers laquelle rediriger.

---

### **RedirectMatch**
- **Description** : Redirige les requêtes en utilisant des expressions régulières.
- **Syntaxe** :
  ```apache
  RedirectMatch [status] REGEX TARGET_URL
  ```
  - **`REGEX`** : Expression régulière pour correspondre aux URLs.
  - **`TARGET_URL`** : L'URL cible.

---

## **3. Exemples pratiques avec `mod_alias`**

### **3.1. Redirection permanente d'un domaine entier**
**Cas** : Rediriger tout le trafic de `http://example.com` vers `https://newexample.com`.
```apache
<VirtualHost *:80>
    ServerName example.com
    Redirect 301 / https://newexample.com/
</VirtualHost>
```

---

### **3.2. Redirection d'une page spécifique**
**Cas** : Rediriger `/old-page` vers `/new-page`.
```apache
Redirect 301 /old-page /new-page
```

#### **Effet** :
- `http://example.com/old-page` → `http://example.com/new-page`.

---

### **3.3. Redirection vers un autre site**
**Cas** : Rediriger `/blog` vers un blog externe.
```apache
Redirect 302 /blog https://blog.example.com
```

#### **Effet** :
- `http://example.com/blog` → `https://blog.example.com`.

---

### **3.4. Redirection basée sur un pattern**
**Cas** : Rediriger toutes les pages terminant par `.html` vers `.php`.
```apache
RedirectMatch 301 (.*)\.html$ $1.php
```

#### **Effet** :
- `http://example.com/page.html` → `http://example.com/page.php`.

---

### **3.5. Redirection conditionnelle (par sous-domaine)**
**Cas** : Rediriger `http://shop.example.com` vers une boutique externe.
```apache
<VirtualHost *:80>
    ServerName shop.example.com
    Redirect 301 / https://shopify.com/shop
</VirtualHost>
```

---

### **3.6. Redirection d'une section vers une nouvelle structure**
**Cas** : Rediriger toutes les pages d'une section `/old-section` vers `/new-section`.
```apache
RedirectMatch 301 ^/old-section/(.*)$ /new-section/$1
```

#### **Effet** :
- `http://example.com/old-section/page` → `http://example.com/new-section/page`.

---

## **4. Cas d'erreurs ou pages spéciales**

### **4.1. Redirection d'une erreur 404 vers une page personnalisée**
**Cas** : Rediriger toutes les erreurs 404 vers une page `/not-found`.
```apache
ErrorDocument 404 /not-found
Redirect 301 /not-found /custom-404-page
```

---

### **4.2. Blocage d'une URL avec un code d'erreur**
**Cas** : Retourner une erreur 410 (Gone) pour une page supprimée.
```apache
Redirect 410 /deleted-page
```

#### **Effet** :
- Les utilisateurs recevront une erreur `410 Gone` pour cette page.

---

## **5. Cas spécifiques : Gestion de HTTPS avec `mod_alias`**

### **Redirection de HTTP vers HTTPS**
**Cas** : Forcer tout le trafic HTTP à utiliser HTTPS.
```apache
<VirtualHost *:80>
    ServerName example.com
    Redirect 301 / https://example.com/
</VirtualHost>
```

---

### **6. Avantages de `mod_alias` par rapport à `mod_rewrite`**
- **Simplicité** : Configuration plus simple pour les redirections directes.
- **Performance** : Moins gourmand en ressources que `mod_rewrite`.
- **Cas idéal** : Utilisé pour des redirections simples et permanentes (301 ou 302).

---

## **Conclusion**
`mod_alias` est l'outil idéal pour des redirections simples et efficaces, notamment pour :
1. Migrer un domaine.
2. Rediriger des pages ou sections spécifiques.
3. Implémenter des redirections temporaires ou permanentes.

Si vous avez besoin d'un exemple spécifique ou d'une redirection conditionnelle plus complexe, dites-le-moi !