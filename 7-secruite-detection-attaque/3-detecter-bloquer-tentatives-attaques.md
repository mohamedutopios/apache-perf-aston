### **Détection et Blocage des Tentatives d'Attaques avec Apache et `mod_security`**

`mod_security` est un module WAF (**Web Application Firewall**) intégré à Apache qui offre des fonctionnalités puissantes pour **détecter et bloquer les attaques** en analysant le trafic HTTP/HTTPS. En combinant des règles prédéfinies (comme celles de l’OWASP CRS) et des règles personnalisées, `mod_security` protège les applications web contre les attaques courantes.

---

### **1. Étapes Clés pour Détecter et Bloquer les Attaques**

1. **Installer et Configurer `mod_security`**.
2. **Activer les Règles OWASP Core Rule Set (CRS)**.
3. **Créer des Règles Personnalisées** pour des besoins spécifiques.
4. **Surveiller les Journaux pour Détecter les Tentatives**.
5. **Optimiser et Affiner les Règles** pour minimiser les faux positifs.

---

### **2. Installer et Configurer `mod_security`**

#### **Installation sur Apache**

1. Installez `mod_security` :
   ```bash
   sudo apt update
   sudo apt install libapache2-mod-security2
   ```

2. Activez le module :
   ```bash
   sudo a2enmod security2
   sudo systemctl restart apache2
   ```

3. Vérifiez que le module est actif :
   ```bash
   apachectl -M | grep security2
   ```

#### **Configurer `mod_security`**

1. Copiez le fichier de configuration recommandé :
   ```bash
   sudo cp /etc/modsecurity/modsecurity.conf-recommended /etc/modsecurity/modsecurity.conf
   ```

2. Activez le moteur de détection ou de blocage dans `/etc/modsecurity/modsecurity.conf` :
   ```plaintext
   SecRuleEngine On
   ```
   - **`DetectionOnly`** : Journalise les tentatives sans les bloquer (mode test).
   - **`On`** : Active la détection et le blocage.

---

### **3. Activer les Règles OWASP Core Rule Set (CRS)**

Les **OWASP CRS** sont un ensemble de règles prêtes à l'emploi pour détecter et bloquer les attaques courantes telles que :
- **Injection SQL**.
- **Cross-Site Scripting (XSS)**.
- **Exfiltration de données sensibles**.
- **Command Injection**.

#### **Installation des Règles OWASP CRS**

1. Installez les règles :
   ```bash
   sudo apt install modsecurity-crs
   ```

2. Activez les règles dans `/etc/modsecurity/modsecurity.conf` :
   ```plaintext
   IncludeOptional /usr/share/modsecurity-crs/*.conf
   IncludeOptional /usr/share/modsecurity-crs/rules/*.conf
   ```

3. Redémarrez Apache :
   ```bash
   sudo systemctl restart apache2
   ```

---

### **4. Créer des Règles Personnalisées**

#### **Exemple 1 : Bloquer une Adresse IP**
Bloque toutes les requêtes provenant d'une adresse IP spécifique :
```plaintext
SecRule REMOTE_ADDR "^192\.168\.1\.100$" "id:1001,phase:1,deny,status:403,msg:'Blocked IP Address'"
```

#### **Exemple 2 : Bloquer une Injection SQL**
Détecte une tentative d'injection SQL dans les paramètres de requête :
```plaintext
SecRule ARGS "select.+from" "id:1002,phase:2,deny,status:403,msg:'SQL Injection Detected'"
```

#### **Exemple 3 : Bloquer un User-Agent Malveillant**
Bloque les requêtes avec un User-Agent spécifique (ex. `curl`) :
```plaintext
SecRule REQUEST_HEADERS:User-Agent "curl" "id:1003,phase:1,deny,status:403,msg:'Blocked User-Agent'"
```

#### **Exemple 4 : Limiter la Taille des Corps de Requêtes**
Empêche les requêtes POST volumineuses qui pourraient provoquer une surcharge :
```plaintext
SecRequestBodyLimit 102400
SecRule REQUEST_BODY_LENGTH "@gt 102400" "id:1004,phase:2,deny,status:413,msg:'Request Body Too Large'"
```

#### **Exemple 5 : Bloquer les Téléchargements Dangereux**
Bloque le téléchargement de fichiers exécutables :
```plaintext
SecRule FILES_NAMES "\.(exe|sh|bat|php)$" "id:1005,phase:2,deny,status:403,msg:'Blocked Dangerous File Upload'"
```

---

### **5. Surveiller et Analyser les Journaux**

#### **Fichier de Log d'Audit**
Le fichier d'audit enregistre les requêtes bloquées et les détails associés :
```bash
/var/log/apache2/modsec_audit.log
```

#### **Activer les Logs Détaillés**
Activez un niveau de journalisation plus élevé pour déboguer les règles :
```plaintext
SecDebugLog /var/log/apache2/modsec_debug.log
SecDebugLogLevel 9
```

#### **Analyser une Tentative d'Attaque**
Une entrée typique dans `modsec_audit.log` ressemble à ceci :
```plaintext
--2abc1234-A--
[04/Dec/2024:10:15:42 +0000] 192.168.1.100 12345 192.168.1.200 80
GET /index.php?id=1; DROP TABLE users HTTP/1.1
User-Agent: curl/7.68.0

--2abc1234-B--
403 Forbidden
```
- **`192.168.1.100`** : Adresse IP de l'attaquant.
- **`GET /index.php?id=1; DROP TABLE users`** : Tentative d'injection SQL.
- **`403 Forbidden`** : Requête bloquée par `mod_security`.

---

### **6. Tester la Protection**

#### **1. Injection SQL**
Simulez une requête malveillante :
```bash
curl "http://example.com/index.php?id=1; DROP TABLE users"
```
- **Résultat attendu** : La requête est bloquée et retourne une erreur 403.

#### **2. Bloquer un User-Agent**
Simulez un User-Agent malveillant :
```bash
curl -H "User-Agent: curl" "http://example.com"
```
- **Résultat attendu** : La requête est bloquée.

---

### **7. Affiner les Règles**

#### **Minimiser les Faux Positifs**
1. **Activer le Mode Détection** :
   - Utilisez `SecRuleEngine DetectionOnly` pour analyser les requêtes sans bloquer.
2. **Analyser les Journaux** :
   - Identifiez les requêtes légitimes bloquées par erreur.
3. **Exclure des Chemins ou Paramètres** :
   - Ajoutez des exceptions aux règles si nécessaire :
     ```plaintext
     SecRuleRemoveById 1002
     ```

#### **Mettre à Jour les Règles OWASP CRS**
Mettez régulièrement à jour les règles OWASP CRS pour protéger contre les nouvelles vulnérabilités.

---

### **8. Bonnes Pratiques**

1. **Toujours Utiliser HTTPS** :
   - Chiffrez les communications pour empêcher l’interception des données sensibles.

2. **Intégrer avec des Solutions de Sécurité** :
   - Combinez `mod_security` avec des outils comme Fail2Ban pour bloquer les IP après plusieurs tentatives.

3. **Limiter les Permissions** :
   - Restreignez l’accès aux ressources critiques avec des règles `Require`.

4. **Surveiller les Journaux** :
   - Intégrez les logs de `mod_security` dans un système de surveillance comme ELK ou Splunk.

5. **Tester en Continu** :
   - Effectuez des tests de pénétration réguliers pour identifier les vulnérabilités restantes.

---

### **9. Exemple Complet**

#### **Configuration dans `/etc/apache2/modsecurity.d/custom-rules.conf`**
```plaintext
SecRuleEngine On

# Bloquer les injections SQL
SecRule ARGS "select.+from" "id:1001,phase:2,deny,status:403,msg:'SQL Injection Detected'"

# Bloquer une IP spécifique
SecRule REMOTE_ADDR "^192\.168\.1\.100$" "id:1002,phase:1,deny,status:403,msg:'Blocked IP Address'"

# Limiter la taille des requêtes
SecRequestBodyLimit 102400
SecRule REQUEST_BODY_LENGTH "@gt 102400" "id:1003,phase:2,deny,status:413,msg:'Request Body Too Large'"

# Bloquer les User-Agent malveillants
SecRule REQUEST_HEADERS:User-Agent "curl" "id:1004,phase:1,deny,status:403,msg:'Blocked User-Agent'"
```

Activez ces règles en les incluant dans la configuration principale :
```plaintext
Include /etc/apache2/modsecurity.d/custom-rules.conf
```

---

### **10. Résumé**

Avec `mod_security`, vous pouvez :
- **Détecter et bloquer les attaques courantes** (SQL Injection, XSS, etc.).
- **Créer des règles personnalisées** pour des besoins spécifiques.
- **Surveiller les tentatives d'attaques** via les journaux.
- **Renforcer la sécurité** de vos applications web en combinant

 détection, blocage, et audit.

Besoin d'aide pour personnaliser vos règles ou affiner votre configuration ? Je suis à votre disposition !