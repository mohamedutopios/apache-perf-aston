### **Apache HTTPD 2.4 : Rappels et Nouveautés**

---

#### **Rappels sur Apache HTTPD :**
- **Apache HTTPD (HTTP Server)** est un serveur web open-source très utilisé.
- Ses principales fonctionnalités incluent :
  - Hébergement de sites web.
  - Support de protocoles (HTTP/1.1, HTTP/2 avec mod_http2).
  - Virtual Hosts pour gérer plusieurs sites sur une même machine.
  - Modularité pour activer ou désactiver des fonctionnalités (ex. SSL, rewriting, proxying).
- Sa flexibilité repose sur son architecture modulaire et la prise en charge multi-plateformes (Linux, Windows, macOS).

---

#### **Nouveautés d'Apache HTTPD 2.4 (comparé à 2.2) :**
- **MPM améliorés** : 
  - Nouveau MPM **Event**, optimisé pour les connexions persistantes et HTTP/2.
  - Amélioration des MPM Worker et Prefork.
- **Authentification unifiée** :
  - Nouvelle architecture avec `mod_authz_core`, permettant des directives comme `Require` pour centraliser la gestion d'accès.
  - Authentification via base de données avec `mod_authn_dbd`.
- **Configuration conditionnelle** :
  - Support des directives `If`, `ElseIf`, `Else` pour une configuration plus dynamique.
  - Exemple :
    ```conf
    <If "%{HTTP_HOST} == 'example.com'">
      DocumentRoot "/var/www/example"
    </If>
    ```
- **Modules proxy améliorés** :
  - Support natif pour WebSocket via `mod_proxy_wstunnel`.
  - Meilleure gestion des backends via `mod_proxy_balancer`.
- **HTTP/2** :
  - Ajout du module `mod_http2` pour le protocole HTTP/2.
- **Amélioration des performances** :
  - Réduction de la consommation mémoire grâce à des optimisations dans le traitement des connexions.
- **Directive IncludeOptional** :
  - Permet d'inclure des fichiers de configuration optionnels, évitant les erreurs si un fichier est manquant.

---

### **Compilation, Installation et Test Initial :**

#### **1. Pré-requis :**
- Vérifier les dépendances :
  - **APR et APR-util** (Apache Portable Runtime).
  - OpenSSL pour SSL/TLS.
  - PCRE (Perl Compatible Regular Expressions) pour `mod_rewrite` et autres modules.

#### **2. Télécharger les sources :**
```bash
wget https://downloads.apache.org/httpd/httpd-2.4.x.tar.gz
```

#### **3. Télécharger les dépendances APR :**
```bash
wget https://downloads.apache.org/apr/apr-1.x.tar.gz
wget https://downloads.apache.org/apr/apr-util-1.x.tar.gz
```

Décompressez APR et APR-util, puis placez-les dans le répertoire des sources d'Apache (sous `srclib/`) :
```bash
tar -xzvf apr-1.x.tar.gz
tar -xzvf apr-util-1.x.tar.gz
mv apr-1.x httpd-2.4.x/srclib/apr
mv apr-util-1.x httpd-2.4.x/srclib/apr-util
```

#### **4. Décompression des sources Apache HTTPD :**
```bash
tar -xzvf httpd-2.4.x.tar.gz
cd httpd-2.4.x
```

#### **5. Configuration des options de compilation :**
```bash
./configure --enable-so --enable-ssl --enable-mods-shared=all --with-mpm=event --with-pcre --enable-rewrite
```
- **`--enable-so`** : Activer les modules dynamiques.
- **`--enable-ssl`** : Inclure le support SSL/TLS.
- **`--enable-mods-shared=all`** : Compiler tous les modules sous forme dynamique.
- **`--with-mpm=event`** : Choisir le MPM Event comme module de traitement par défaut.

#### **6. Compilation et installation :**
```bash
make
sudo make install
```

#### **7. Test initial :**
1. Vérifiez la version installée :
   ```bash
   /usr/local/apache2/bin/httpd -v
   ```

2. Démarrez le serveur Apache :
   ```bash
   /usr/local/apache2/bin/apachectl start
   ```

3. Vérifiez le processus Apache :
   ```bash
   ps aux | grep httpd
   ```

4. Testez dans un navigateur :
   - Accédez à `http://localhost`.
   - Si tout est correctement configuré, la page par défaut d'Apache apparaîtra.

---

### **Structure par défaut de l'installation :**
- **Fichiers de configuration** :
  - `/usr/local/apache2/conf/httpd.conf` : fichier principal.
  - `/usr/local/apache2/conf/extra/` : configurations supplémentaires (SSL, virtual hosts, MPM).
- **Répertoires** :
  - `/usr/local/apache2/htdocs/` : emplacement des fichiers web par défaut.
  - `/usr/local/apache2/logs/` : logs d'erreurs et d'accès.

---

Si vous souhaitez un guide détaillé pour configurer des fonctionnalités spécifiques comme SSL, virtual hosts ou proxy, faites-le-moi savoir !