### **Support du protocole HTTP/2 dans Apache 2.4**

Le protocole **HTTP/2** est une amélioration significative de HTTP/1.1. Il améliore les performances, la latence et la gestion des connexions réseau. Apache HTTPD prend en charge HTTP/2 à partir de la version **2.4.17**, mais il nécessite une configuration spécifique.

---

### **1. Avantages du protocole HTTP/2**
- **Multiplexage** : Plusieurs requêtes et réponses peuvent être échangées simultanément sur une seule connexion TCP.
- **Compression des en-têtes** : Réduit la taille des en-têtes HTTP, ce qui améliore les performances.
- **Priorisation des ressources** : Permet de prioriser certains contenus, comme les fichiers CSS ou JavaScript critiques.
- **Connexions persistantes** : Réduit la surcharge en limitant l'ouverture de connexions multiples.
- **Performances accrues** : Idéal pour les sites modernes, notamment ceux avec beaucoup de fichiers (CSS, JS, images).

---

### **2. Prérequis pour activer HTTP/2**

1. **Version d'Apache** : HTTP/2 est pris en charge à partir de la version **2.4.17**.
   - Vérifiez votre version avec :
     ```bash
     apache2 -v
     ```

2. **SSL/TLS activé** : HTTP/2 nécessite HTTPS pour être activé dans Apache.

3. **Module requis** : Le module `mod_http2` doit être activé.

4. **Version d’OpenSSL** : OpenSSL **1.0.2** ou supérieur est requis.

---

### **3. Étapes pour activer HTTP/2**

#### **a. Vérifier et activer le module HTTP/2**
- **Ubuntu/Debian** :
  Activez le module `http2` :
  ```bash
  sudo a2enmod http2
  sudo systemctl reload apache2
  ```

- **CentOS/RHEL** :
  Assurez-vous que le module est chargé dans `/etc/httpd/conf.modules.d/00-mpm.conf` ou directement dans `httpd.conf` :
  ```apache
  LoadModule http2_module modules/mod_http2.so
  ```
  Rechargez Apache :
  ```bash
  sudo systemctl reload httpd
  ```

#### **b. Modifier la configuration SSL pour activer HTTP/2**
Ajoutez ou modifiez la directive `Protocols` dans le fichier de configuration du Virtual Host HTTPS.

Exemple de configuration dans `/etc/apache2/sites-available/default-ssl.conf` (Ubuntu) ou `/etc/httpd/conf.d/ssl.conf` (CentOS) :

```apache
<VirtualHost *:443>
    ServerName example.com
    DocumentRoot /var/www/html

    SSLEngine On
    SSLCertificateFile /etc/apache2/ssl/example.crt
    SSLCertificateKeyFile /etc/apache2/ssl/example.key

    # Activer HTTP/2
    Protocols h2 h2c http/1.1

    ErrorLog ${APACHE_LOG_DIR}/error.log
    CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>
```

#### **c. Vérifier et activer MPM event**
HTTP/2 fonctionne mieux avec le MPM **event**. Activez-le si ce n’est pas déjà fait :

- Désactivez le MPM prefork (si activé) :
  ```bash
  sudo a2dismod mpm_prefork
  ```

- Activez le MPM event :
  ```bash
  sudo a2enmod mpm_event
  ```

- Rechargez Apache :
  ```bash
  sudo systemctl reload apache2
  ```

---

### **4. Tester HTTP/2**

#### **a. Avec un navigateur**
- Accédez à votre site via HTTPS (`https://example.com`).
- Ouvrez les outils de développement (F12), allez dans l'onglet **Network**, et vérifiez la colonne **Protocol**. Si HTTP/2 est activé, vous verrez `h2`.

#### **b. Avec `curl`**
Utilisez l'option `--http2` :
```bash
curl -I --http2 https://example.com
```

#### **c. Avec un outil en ligne**
Utilisez des outils comme [HTTP/2 Test](https://tools.keycdn.com/http2-test) pour vérifier si HTTP/2 est activé sur votre site.

---

### **5. Résolution des problèmes courants**

1. **HTTP/2 non activé malgré la configuration**
   - Vérifiez que le module `http2` est chargé :
     ```bash
     apache2ctl -M | grep http2
     ```
     Vous devriez voir :
     ```
     http2_module (shared)
     ```

2. **Erreurs liées à OpenSSL**
   - Assurez-vous que votre version d’OpenSSL est **1.0.2** ou supérieure :
     ```bash
     openssl version
     ```

3. **Problème de compatibilité avec MPM prefork**
   - HTTP/2 ne fonctionne pas avec `mpm_prefork`. Utilisez `mpm_event` ou `mpm_worker`.

---

### **6. Résumé des directives importantes pour HTTP/2**

| **Directive**       | **Description**                                                                 |
|---------------------|---------------------------------------------------------------------------------|
| `Protocols`         | Définit les protocoles pris en charge (exemple : `h2 h2c http/1.1`).           |
| `ProtocolsHonorOrder` | Priorise les protocoles dans l'ordre spécifié dans `Protocols`.                |

---

### **7. Bénéfices pratiques de HTTP/2**

1. **Sites à fort trafic** : HTTP/2 réduit la latence et améliore les temps de chargement.
2. **Applications modernes** : Idéal pour les sites avec beaucoup de fichiers (CSS, JS, images).
3. **Meilleures performances SEO** : Les moteurs de recherche favorisent les sites rapides et sécurisés.

---

En activant HTTP/2 dans Apache, vous améliorez significativement l'expérience utilisateur et les performances globales de vos sites.