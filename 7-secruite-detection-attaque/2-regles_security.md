### **Le Principe des Règles dans `mod_security`**

Les **règles** de `mod_security` sont des directives permettant de définir des actions à exécuter lorsqu’une requête HTTP (ou une réponse) correspond à un certain **modèle** ou **critère**. Elles constituent l'épine dorsale de `mod_security`, permettant de détecter, bloquer, ou journaliser des comportements malveillants ou suspects.

---

### **1. Structure d'une Règle**

Une règle `mod_security` est composée de plusieurs éléments clés :

```plaintext
SecRule VARIABLE OPERATOR [ACTIONS]
```

- **`VARIABLE`** : Ce que la règle analyse (URI, paramètres, en-têtes, etc.).
- **`OPERATOR`** : Le test effectué sur la variable (correspondance, comparaison, etc.).
- **`ACTIONS`** : Ce qui doit être fait si la règle est déclenchée.

---

### **2. Composants en Détail**

#### **2.1 Variables**
Les variables définissent les parties de la requête ou de la réponse à analyser.

| **Variable**              | **Description**                                               |
|---------------------------|---------------------------------------------------------------|
| `ARGS`                    | Paramètres de requête (GET et POST).                         |
| `REQUEST_URI`             | URI de la requête.                                           |
| `REQUEST_HEADERS`         | En-têtes HTTP de la requête.                                 |
| `RESPONSE_BODY`           | Corps de la réponse du serveur.                              |
| `REMOTE_ADDR`             | Adresse IP du client.                                        |
| `FILES`                   | Fichiers téléchargés dans une requête multipart/form-data.   |
| `REQUEST_BODY`            | Corps de la requête (POST).                                  |

---

#### **2.2 Opérateurs**
Les opérateurs testent les variables pour vérifier si elles correspondent à un certain modèle ou condition.

| **Opérateur**  | **Description**                                      |
|----------------|------------------------------------------------------|
| `@rx`          | Expression régulière (Regex).                       |
| `@streq`       | Comparaison exacte (string equality).               |
| `@beginsWith`  | Vérifie si la variable commence par une valeur.     |
| `@endsWith`    | Vérifie si la variable se termine par une valeur.   |
| `@contains`    | Vérifie si la variable contient une sous-chaîne.    |
| `@eq`          | Vérification d'égalité (numérique).                 |

---

#### **2.3 Actions**
Les actions déterminent le comportement de `mod_security` lorsqu'une règle est déclenchée.

| **Action**             | **Description**                                                                                   |
|------------------------|---------------------------------------------------------------------------------------------------|
| `deny`                 | Bloque la requête.                                                                               |
| `allow`                | Permet la requête, même si d'autres règles la bloquent.                                          |
| `log`                  | Journalise l'événement dans les logs.                                                            |
| `phase:1` ou `phase:2` | Définit à quel moment la règle est exécutée (avant ou après la réception complète de la requête). |
| `id`                   | Identifiant unique pour la règle.                                                                |
| `status`               | Définit le code de statut HTTP à retourner (ex. 403).                                            |
| `msg`                  | Message descriptif pour les logs.                                                                |

---

### **3. Exemples de Règles**

#### **3.1 Bloquer une Requête Contenant une Injection SQL**
```plaintext
SecRule ARGS "select.+from" "id:1001,phase:2,deny,status:403,msg:'SQL Injection Detected'"
```
- **Variable** : `ARGS` (analyse les paramètres de la requête).
- **Opérateur** : Expression régulière `select.+from` (recherche des mots clés SQL).
- **Actions** :
  - `id:1001` : Identifiant de la règle.
  - `phase:2` : Exécute la règle après avoir reçu tous les paramètres.
  - `deny` : Bloque la requête.
  - `status:403` : Retourne un code 403 (Forbidden).
  - `msg` : Ajoute une entrée dans les logs pour indiquer l'attaque.

---

#### **3.2 Bloquer une Adresse IP Spécifique**
```plaintext
SecRule REMOTE_ADDR "^192\.168\.1\.100$" "id:1002,phase:1,deny,status:403,msg:'Blocked IP Address'"
```
- **Variable** : `REMOTE_ADDR` (adresse IP du client).
- **Opérateur** : Regex pour matcher une IP spécifique.
- **Actions** :
  - `deny` : Bloque les requêtes provenant de cette IP.

---

#### **3.3 Bloquer les Téléchargements de Fichiers PHP**
```plaintext
SecRule FILES_NAMES "\.php$" "id:1003,phase:2,deny,status:403,msg:'Blocked PHP Upload'"
```
- **Variable** : `FILES_NAMES` (noms des fichiers téléchargés).
- **Opérateur** : Regex `\.php$` (fichiers se terminant par `.php`).
- **Actions** :
  - Bloque les téléchargements de fichiers PHP.

---

#### **3.4 Limiter la Taille des Requêtes POST**
```plaintext
SecRequestBodyLimit 102400
SecRule REQUEST_BODY_LENGTH "@gt 102400" "id:1004,phase:2,deny,status:413,msg:'Request Body Too Large'"
```
- **Variable** : `REQUEST_BODY_LENGTH` (taille du corps de la requête).
- **Opérateur** : `@gt` (supérieur à).
- **Action** : Bloque les requêtes dont le corps dépasse 100 Ko.

---

#### **3.5 Bloquer un User-Agent Spécifique**
```plaintext
SecRule REQUEST_HEADERS:User-Agent "curl" "id:1005,phase:1,deny,status:403,msg:'Blocked curl User-Agent'"
```
- **Variable** : `REQUEST_HEADERS:User-Agent` (analyse l'en-tête `User-Agent`).
- **Opérateur** : Regex pour détecter `curl`.
- **Action** : Bloque les requêtes provenant de cet outil.

---

### **4. Phases d'Exécution des Règles**

Les règles peuvent être exécutées à différents moments du traitement de la requête :
1. **Phase 1 : Requête Initiale**
   - Avant de lire le corps de la requête.
   - Analyse des URI, en-têtes, et adresses IP.
2. **Phase 2 : Corps de Requête**
   - Après réception complète du corps de la requête.
   - Utile pour analyser les données POST.
3. **Phase 3 : Réponse du Serveur**
   - Analyse des en-têtes de réponse.
4. **Phase 4 : Corps de Réponse**
   - Analyse le contenu de la réponse du serveur.
5. **Phase 5 : Journalisation**
   - Après que la réponse a été envoyée.

---

### **5. Logs de `mod_security`**

#### **Journalisation des Règles Déclenchées**
Les logs d'audit fournissent des détails sur les requêtes bloquées :
```bash
/var/log/apache2/modsec_audit.log
```

#### **Débogage des Règles**
Activez les logs détaillés :
```plaintext
SecDebugLog /var/log/apache2/modsec_debug.log
SecDebugLogLevel 9
```

---

### **6. Bonnes Pratiques pour Écrire des Règles**

1. **Définir des ID Uniques**
   - Assignez un ID unique à chaque règle pour faciliter leur suivi et gestion.

2. **Prioriser la Performance**
   - Placez les règles les plus fréquemment utilisées en haut.
   - Évitez les regex complexes pour les variables fréquemment analysées.

3. **Utiliser des Règles Prêtes à l’Emploi**
   - Intégrez les **OWASP Core Rule Set (CRS)** comme base.

4. **Tester en Mode Détection**
   - Activez `SecRuleEngine DetectionOnly` avant de déployer en production.

5. **Minimiser les Faux Positifs**
   - Analysez les logs pour affiner les règles et éviter les blocages inutiles.

---

### **7. OWASP Core Rule Set (CRS)**

Le **OWASP CRS** est un ensemble de règles prêtes à l'emploi pour détecter :
- Injections SQL.
- XSS.
- CSRF.
- Dépassements de taille de requête.

Pour l'activer, ajoutez dans `modsecurity.conf` :
```plaintext
IncludeOptional /usr/share/modsecurity-crs/*.conf
IncludeOptional /usr/share/modsecurity-crs/rules/*.conf
```

---

### **Résumé**

- Les **règles de `mod_security`** permettent de protéger les applications web en analysant chaque aspect des requêtes HTTP.
- Elles utilisent des **variables**, des **opérateurs**, et des **actions** pour détecter et bloquer les comportements malveillants.
- Une configuration soigneuse et l’utilisation des **OWASP CRS** offrent une protection robuste contre les attaques courantes.

Besoin d’un exemple spécifique ou d’une personnalisation avancée ? Je suis là pour vous aider !