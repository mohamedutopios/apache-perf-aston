---
marp: true
title: Kubernetes
theme: utopios
paginate: true
author: Mohamed Aijjou
header: "![h:70px](https://utopios-marp-assets.s3.eu-west-3.amazonaws.com/logo_blanc.svg)"
footer: "Utopios® Tous droits réservés"
---

<!-- _class: lead -->
<!-- _paginate: false -->

# APACHE PERFECTIONNEMENT

---

## Sommaire

<br>

1. Apache HTTPD 2.4 : rappels et nouveautés
2. Héberger des applications PHP
3. Contrôle d'accès et authentification
4. Redirection, réécriture d'adresses, filtres
5. Reverse Proxy et Cache
6. Sécuriser les échanges avec HTTPS
7. Sécurité et détection d'attaques

</div>

---

<!-- _class: lead -->
<!-- _paginate: false -->

## Apache HTTPD 2.4 : rappels et nouveautés

---

## Apache HTTPD 2.4 : rappels et nouveautés

#### **Rappels :**

<br/>

<div style="font-size:28px">

- **Apache HTTP Server (HTTPD)** est l'un des serveurs web les plus populaires.
- Il repose sur une architecture modulaire.
- Supporte des fonctionnalités comme :
  - **Proxying** (mod_proxy, mod_proxy_http, mod_proxy_ajp, etc.)
  - **SSL/TLS** (via mod_ssl).
  - **Virtual hosts** (configuration de plusieurs sites sur une seule machine).
- Version 2.4 apporte des améliorations par rapport à 2.2 en termes de performance, sécurité et flexibilité.

</div>

---

## Apache HTTPD 2.4 : rappels et nouveautés

#### **Nouveautés d'Apache HTTPD 2.4 (comparé à 2.2) :**


<div style="font-size:21px">

**MPM améliorés** : 
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

</div>


---

## Apache HTTPD 2.4 : rappels et nouveautés

#### **Nouveautés d'Apache HTTPD 2.4 (comparé à 2.2) :**


<div style="font-size:23px">

- **Modules proxy améliorés** :
  - Support natif pour WebSocket via `mod_proxy_wstunnel`.
  - Meilleure gestion des backends via `mod_proxy_balancer`.
- **HTTP/2** :
  - Ajout du module `mod_http2` pour le protocole HTTP/2.
- **Amélioration des performances** :
  - Réduction de la consommation mémoire grâce à des optimisations dans le traitement des connexions.
- **Directive IncludeOptional** :
  - Permet d'inclure des fichiers de configuration optionnels, évitant les erreurs si un fichier est manquant.
</div>


---

## Apache HTTPD 2.4 : rappels et nouveautés

#### **Compilation, Installation et Test Initial :**

<br>
<div style="font-size:29px">

#### **1. Pré-requis :**
- Vérifier les dépendances :
  - **APR et APR-util** (Apache Portable Runtime).
  - OpenSSL pour SSL/TLS.
  - PCRE (Perl Compatible Regular Expressions) pour `mod_rewrite` et autres modules.

</div>


---

## Apache HTTPD 2.4 : rappels et nouveautés

#### **Compilation, Installation et Test Initial :**

<br>
<div style="font-size:22px">

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

</div>


---

## Apache HTTPD 2.4 : rappels et nouveautés

#### **Compilation, Installation et Test Initial :**

<br>
<div style="font-size:35px">

#### **4. Décompression des sources Apache HTTPD :**
```bash
tar -xzvf httpd-2.4.x.tar.gz
cd httpd-2.4.x
```
</div>


---

## Apache HTTPD 2.4 : rappels et nouveautés

#### **Compilation, Installation et Test Initial :**

<br>
<div style="font-size:30px">

#### **5. Configuration des options de compilation :**
```bash
./configure --enable-so --enable-ssl --enable-mods-shared=all --with-mpm=event --with-pcre --enable-rewrite
```
- **`--enable-so`** : Activer les modules dynamiques.
- **`--enable-ssl`** : Inclure le support SSL/TLS.
- **`--enable-mods-shared=all`** : Compiler tous les modules sous forme dynamique.
- **`--with-mpm=event`** : Choisir le MPM Event comme module de traitement par défaut.


</div>


---

## Apache HTTPD 2.4 : rappels et nouveautés

#### **Compilation, Installation et Test Initial :**

<br>
<div style="font-size:35px">

#### **6. Compilation et installation :**
```bash
make
sudo make install
```


</div>


---
## Apache HTTPD 2.4 : rappels et nouveautés

#### **Compilation, Installation et Test Initial :**


<div style="font-size:21px">

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


</div>


---

## Apache HTTPD 2.4 : rappels et nouveautés

#### **Structure par défaut de l'installation :**

<br>
<div style="font-size:30px">

- **Fichiers de configuration** :
  - `/usr/local/apache2/conf/httpd.conf` : fichier principal.
  - `/usr/local/apache2/conf/extra/` : configurations supplémentaires (SSL, virtual hosts, MPM).
- **Répertoires** :
  - `/usr/local/apache2/htdocs/` : emplacement des fichiers web par défaut.
  - `/usr/local/apache2/logs/` : logs d'erreurs et d'accès.


</div>


---

## Apache HTTPD 2.4 : rappels et nouveautés

#### **Installation complète d'Apache avec apt**

<br>
<div style="font-size:28px">

#### **1. Mettre à jour les paquets**
Avant toute installation, mettez à jour les informations des paquets pour garantir que vous obtenez la dernière version disponible :

```bash
sudo apt update
sudo apt upgrade -y
```
#### **2. Installer Apache**
Installez Apache HTTP Server avec la commande suivante :

```bash
sudo apt install apache2 -y
```

</div>


---

## Apache HTTPD 2.4 : rappels et nouveautés

#### **Installation complète d'Apache avec apt**

<br>
<div style="font-size:35px">

#### **3. Vérifier l'installation**
Vérifiez qu’Apache est bien installé et en cours d’exécution :

```bash
sudo systemctl status apache2
```

Vous devriez voir une sortie indiquant que le service est **`active (running)`**.


</div>


---

## Apache HTTPD 2.4 : rappels et nouveautés

#### **Installation complète d'Apache avec apt**

<br>
<div style="font-size:35px">

#### **4. Tester Apache**
1. **Dans un navigateur** : Accédez à votre serveur à l'adresse suivante :
   - Si c'est en local : [http://localhost](http://localhost) ou [http://127.0.0.1](http://127.0.0.1).
   - Si c'est une machine distante : [http://<adresse_IP>](http://<adresse_IP>).
2. Vous devriez voir une page intitulée **"Apache2 Ubuntu Default Page"**.


</div>


---

## Apache HTTPD 2.4 : rappels et nouveautés

#### **Installation complète d'Apache avec apt**

<br>
<div style="font-size:35px">

#### **5. Configurer le pare-feu**
Si vous utilisez un pare-feu (comme UFW), ouvrez le port 80 (HTTP) et 443 (HTTPS) pour permettre l'accès au serveur :

```bash
sudo ufw allow 'Apache Full'
sudo ufw enable
sudo ufw status
```

</div>


---

## Apache HTTPD 2.4 : rappels et nouveautés

#### **Installation complète d'Apache avec apt**

<div style="font-size:23px">

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

</div>


---


## Apache HTTPD 2.4 : rappels et nouveautés

#### **Installation complète d'Apache avec apt**

<br>

<div style="font-size:30px">

### **Outils et emplacements importants**
- **Fichiers de configuration des sites** :
  - **Disponibles** : `/etc/apache2/sites-available/`
  - **Activés** : `/etc/apache2/sites-enabled/`
- **Modules disponibles** : `/etc/apache2/mods-available/`
- **Répertoire des journaux** : `/var/log/apache2/`

</div>


---

## Apache HTTPD 2.4 : rappels et nouveautés

#### **Installation complète d'Apache avec apt**

<br>

<div style="font-size:30px">

### **Résumé des commandes principales**
- **Installer Apache** : `sudo apt install apache2`
- **Activer un module** : `sudo a2enmod <module>`
- **Activer un site** : `sudo a2ensite <site>`
- **Vérifier la configuration** : `sudo apache2ctl configtest`
- **Redémarrer Apache** : `sudo systemctl restart apache2`

</div>


---

#### Apache HTTPD 2.4 : rappels et nouveautés

###### **Comparatif dans les installations**

| **Méthode**                | **Avantages**                      | **Inconvénients**                     | **Cas d'usage**                             |
|----------------------------|------------------------------------|---------------------------------------|---------------------------------------------|
| Gestionnaire de paquets    | Facile, stable, rapide            | Peu de personnalisation               | Serveurs de production standard             |
| Compilation (sources)      | Personnalisation, flexibilité     | Maintenance manuelle, complexe        | Besoins spécifiques, environnements avancés |
| Conteneurs Docker          | Isolation, portabilité            | Complexité initiale                   | Microservices, CI/CD, Cloud-native          |
| Outils d’automatisation    | Automatisation, réplicabilité     | Nécessite des outils tiers            | Environnements à grande échelle             |
| Services Cloud préconfigurés| Simplicité, haute disponibilité   | Coût élevé, dépendance                | Startups, mise à l’échelle rapide           |


---

## Apache HTTPD 2.4 : rappels et nouveautés

#### **Comparatif dans les installations**

<div style="font-size:24px">


##### **La meilleure façon de faire**

#### **Cas généraux : Utilisation du gestionnaire de paquets**
- Simple, rapide et stable.
- Idéal pour des environnements de production nécessitant peu de personnalisation.

#### **Cas spécifiques : Compilation des sources**
- Permet de répondre à des besoins particuliers (versions spécifiques, optimisations).
- Nécessite un bon niveau en administration système.

#### **Environnements modernes : Docker et Automatisation**
- Docker pour des environnements flexibles, réplicables et isolés.
- Automatisation (Ansible, Terraform) pour les infrastructures complexes à grande échelle.

</div>


---

## Apache HTTPD 2.4 : rappels et nouveautés

#### **Configuration générale du serveur:**

<div style="font-size:18px">

La localisation des dossiers principaux d’**Apache HTTPD** dépend de la méthode d’installation et du système d’exploitation. 

### **1. Installation par les gestionnaires de paquets**
Si Apache est installé via un gestionnaire de paquets (comme `apt` ou `yum`), les fichiers principaux se trouvent généralement sous **`/etc/`** pour la configuration et **`/var/www/`** pour les fichiers web.

#### **Debian/Ubuntu**
- **Fichiers de configuration** : Localisés sous **`/etc/apache2/`**.
  - `/etc/apache2/apache2.conf` : Fichier principal de configuration.
  - `/etc/apache2/sites-available/` : Configurations des Virtual Hosts disponibles.
  - `/etc/apache2/sites-enabled/` : Liens symboliques vers les Virtual Hosts activés.
  - `/etc/apache2/mods-available/` : Modules disponibles.
  - `/etc/apache2/mods-enabled/` : Liens symboliques vers les modules activés.

- **Répertoire des sites web** : **`/var/www/`**
  - Par défaut, `/var/www/html` est le répertoire racine pour les fichiers accessibles par le serveur.



</div>


---


## Apache HTTPD 2.4 : rappels et nouveautés

#### **Configuration générale du serveur:**

<div style="font-size:23px">

La localisation des dossiers principaux d’**Apache HTTPD** dépend de la méthode d’installation et du système d’exploitation. 

### **1. Installation par les gestionnaires de paquets**

#### **CentOS/RHEL**
- **Fichiers de configuration** : Localisés sous **`/etc/httpd/`**.
  - `/etc/httpd/conf/httpd.conf` : Fichier principal de configuration.
  - `/etc/httpd/conf.d/` : Configurations additionnelles (ex. SSL, Virtual Hosts).
  - `/etc/httpd/conf.modules.d/` : Liste des modules disponibles ou chargés.

- **Répertoire des sites web** : **`/var/www/`**
  - Par défaut, `/var/www/html` est utilisé comme racine des sites.



</div>


---

## Apache HTTPD 2.4 : rappels et nouveautés

#### **Configuration générale du serveur:**

<div style="font-size:23px">

### **2. Installation à partir des sources**
Si Apache est installé à partir des **sources**, l’organisation par défaut diffère. En général, les fichiers principaux sont placés sous **`/usr/local/`**, sauf si des options spécifiques sont définies lors de la compilation.

#### **Répertoires typiques** :
- **Configuration** : **`/usr/local/apache2/conf/`**
  - `/usr/local/apache2/conf/httpd.conf` : Fichier principal de configuration.
- **Modules** : **`/usr/local/apache2/modules/`**
- **Logs** : **`/usr/local/apache2/logs/`**
- **Répertoire des sites web** : **`/usr/local/apache2/htdocs/`**


</div>


---

## Apache HTTPD 2.4 : rappels et nouveautés

#### **Configuration générale du serveur:**

<div style="font-size:28px">

### **3. Comparaison des répertoires principaux**

| **Installation**            | **Configuration**             | **Modules**               | **Sites web**         | **Logs**               |
|-----------------------------|-------------------------------|---------------------------|------------------------|------------------------|
| **Debian/Ubuntu**           | `/etc/apache2/`              | `/etc/apache2/mods-available/` | `/var/www/html/`      | `/var/log/apache2/`    |
| **CentOS/RHEL**             | `/etc/httpd/`                | `/etc/httpd/modules/`     | `/var/www/html/`      | `/var/log/httpd/`      |
| **Compilation (sources)**   | `/usr/local/apache2/conf/`   | `/usr/local/apache2/modules/` | `/usr/local/apache2/htdocs/` | `/usr/local/apache2/logs/` |


</div>


---

## Apache HTTPD 2.4 : rappels et nouveautés

#### **Choisir le bon MPM (Multi-Processing Module) :**

<br>

<div style="font-size:35px">


- Le choix du MPM et la gestion des limites sont cruciaux pour optimiser les performances d’Apache en fonction de la charge de travail et des ressources système. 
- Apache HTTPD offre plusieurs MPMs. Chaque MPM a ses propres caractéristiques qui influencent les performances et l’utilisation des ressources

</div>


---



#### Apache HTTPD 2.4 : rappels et nouveautés

##### **Gérer la charge et les limites :**

| **MPM**     | **Description**                                                                                             | **Avantages**                                                                                     | **Limites**                                                                                 |
|-------------|-------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------|
| **prefork** | Basé sur des processus, chaque requête est traitée par un processus distinct.                                | - Stable.<br>- Compatible avec les modules non thread-safe.<br>- Simple à configurer.             | - Consomme beaucoup de mémoire.<br>- Performances limitées pour les charges élevées.        |
| **worker**  | Combinaison de processus et threads. Un processus peut traiter plusieurs requêtes via des threads.           | - Performances élevées.<br>- Meilleure utilisation des ressources.<br>- Moins de mémoire utilisée.| - Modules non thread-safe incompatibles.<br>- Configuration plus complexe.                 |

</div>


---

#### Apache HTTPD 2.4 : rappels et nouveautés

##### **Gérer la charge et les limites :**

<br>


| **MPM**     | **Description**                                                                                             | **Avantages**                                                                                     | **Limites**                                                                                 |
|-------------|-------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------|
| **event**   | Extension de worker. Optimisé pour les connexions persistantes (Keep-Alive).                                | - Très performant pour les connexions Keep-Alive.<br>- Faible consommation des ressources.        | - Modules non thread-safe incompatibles.<br>- Configuration avancée requise.               |
</div>


---

## Apache HTTPD 2.4 : rappels et nouveautés

#### **Choisir le bon MPM (Multi-Processing Module) :**

<div style="font-size:25px">


### **2. Choisir le bon MPM**

#### **Critères de choix :**

1. **Charge prévue :**
   - **Faible charge ou faible trafic** : `prefork` est suffisant.
   - **Charge moyenne à élevée** : `worker` ou `event` est plus adapté.
   - **Connexions persistantes (Keep-Alive)** : `event` est recommandé.

2. **Compatibilité des modules :**
   - **Modules non thread-safe** (par exemple certaines extensions PHP) : Utilisez `prefork`.
   - **Modules thread-safe** : Préférez `worker` ou `event`.


</div>


---

## Apache HTTPD 2.4 : rappels et nouveautés

#### **Choisir le bon MPM (Multi-Processing Module) :**

<div style="font-size:27px">

### **3. Configurer les limites pour chaque MPM**

#### **a. Configuration du MPM `prefork`**
Dans le fichier de configuration Apache (`httpd.conf` ou un fichier spécifique selon votre système), ajustez les paramètres suivants :

```apache
<IfModule mpm_prefork_module>
    StartServers         5        # Nombre initial de processus.
    MinSpareServers      5        # Processus minimum en attente.
    MaxSpareServers     10        # Processus maximum en attente.
    MaxRequestWorkers   150       # Limite maximale de processus simultanés.
    MaxConnectionsPerChild 0      # Nombre de requêtes par processus (0 = illimité).
</IfModule>
```

</div>


---

## Apache HTTPD 2.4 : rappels et nouveautés

#### **Choisir le bon MPM (Multi-Processing Module) :**

<div style="font-size:27px">

### **3. Configurer les limites pour chaque MPM**


#### **b. Configuration du MPM `worker`**
Les paramètres à ajuster pour `worker` :

```apache
<IfModule mpm_worker_module>
    StartServers         2        # Processus de démarrage.
    MinSpareThreads     25        # Threads minimum en attente.
    MaxSpareThreads     75        # Threads maximum en attente.
    ThreadLimit         64        # Limite de threads par processus.
    ThreadsPerChild     25        # Threads gérés par chaque processus.
    MaxRequestWorkers   150       # Nombre total maximum de threads (processeurs * threads).
    MaxConnectionsPerChild 0      # Nombre de requêtes avant de recréer un processus.
</IfModule>
```

</div>


---

## Apache HTTPD 2.4 : rappels et nouveautés

#### **Choisir le bon MPM (Multi-Processing Module) :**

<div style="font-size:25px">

### **3. Configurer les limites pour chaque MPM**


#### **c. Configuration du MPM `event`**
Les paramètres pour `event` sont similaires à ceux de `worker`, avec des optimisations pour les connexions Keep-Alive :

```apache
<IfModule mpm_event_module>
    StartServers         2
    MinSpareThreads     25
    MaxSpareThreads     75
    ThreadLimit         64
    ThreadsPerChild     25
    MaxRequestWorkers   150
    MaxConnectionsPerChild 0
</IfModule>
```

</div>

---

## Apache HTTPD 2.4 : rappels et nouveautés

#### **Choisir le bon MPM (Multi-Processing Module) :**

<div style="font-size:27px">

### **4. Gérer la charge : Limites et optimisations**

#### **a. MaxRequestWorkers**
- Définit le nombre maximum de requêtes simultanées que le serveur peut traiter.
- Adaptez ce paramètre en fonction de la mémoire disponible et de la charge prévue.

#### **b. Timeout**
- Contrôle le temps maximum qu’Apache attend pour qu'une requête soit traitée.
- Réduisez cette valeur pour éviter des connexions bloquantes :
  ```apache
  Timeout 60
  ```

</div>

---

## Apache HTTPD 2.4 : rappels et nouveautés

#### **Choisir le bon MPM (Multi-Processing Module) :**

<div style="font-size:20px">

### **4. Gérer la charge : Limites et optimisations**

#### **c. Keep-Alive**
- Permet aux clients de réutiliser une connexion TCP pour plusieurs requêtes.
- Réglez les paramètres pour optimiser les connexions persistantes :
  ```apache
  KeepAlive On
  MaxKeepAliveRequests 100
  KeepAliveTimeout 5
  ```

#### **d. Gestion de la mémoire**
- Surveillez l’utilisation de la mémoire avec des outils comme `top` ou `htop`.
- Augmentez les limites système si nécessaire :
  - Exemple pour augmenter les fichiers ouverts :
    ```bash
    ulimit -n 4096
    ```

</div>

---

## Apache HTTPD 2.4 : rappels et nouveautés

#### **Choisir le bon MPM (Multi-Processing Module) :**

<div style="font-size:30px">

### **4. Gérer la charge : Limites et optimisations**

#### **e. Surveillance et monitoring**
- Utilisez des outils comme **Prometheus**, **Nagios**, ou **Grafana** pour surveiller la charge et les performances.
- Analysez les fichiers de logs pour détecter les goulots d’étranglement :
  ```bash
  tail -f /var/log/apache2/access.log
  tail -f /var/log/apache2/error.log
  ```

</div>

---

## Apache HTTPD 2.4 : rappels et nouveautés

#### **Choisir le bon MPM (Multi-Processing Module) :**

<div style="font-size:25px">

### **5. Tests et ajustements**
1. **Testez vos paramètres avec des outils de charge** :
   - Exemple : **Apache Benchmark** (ab) :
     ```bash
     ab -n 1000 -c 100 http://localhost/
     ```
   - Cela permet de tester comment Apache gère un grand nombre de requêtes simultanées.

2. **Ajustez les paramètres** :
   - Si Apache atteint la limite de `MaxRequestWorkers`, vous devrez augmenter cette valeur ou optimiser l'application.

</div>

---

## Apache HTTPD 2.4 : rappels et nouveautés

#### **Choisir le bon MPM (Multi-Processing Module) :**

<div style="font-size:25px">

### **6. Résumé : Quelle stratégie adopter ?**

| **Scénario**                     | **MPM recommandé** | **Stratégie**                                                                                  |
|-----------------------------------|--------------------|-----------------------------------------------------------------------------------------------|
| **Site avec faible trafic**       | `prefork`          | Paramètres par défaut, Keep-Alive activé.                                                     |
| **Site avec trafic modéré/élevé** | `worker`           | Ajustez `ThreadsPerChild` et `MaxRequestWorkers` pour gérer la charge.                        |
| **API ou connexions Keep-Alive**  | `event`            | Optimisez Keep-Alive et les threads pour maximiser les performances avec des connexions longues. |
</div>

---

## Apache HTTPD 2.4 : rappels et nouveautés

#### **Choisir le bon MPM (Multi-Processing Module) :**

<div style="font-size:25px">


### **2. Choisir le bon MPM**

#### **Critères de choix :**

3. **Ressources disponibles :**
   - **Mémoire limitée** : Évitez `prefork` et optez pour `worker` ou `event`.
   - **Environnements virtualisés/cloud** : Préférez `worker` ou `event`.

4. **Type d’application :**
   - Applications lourdes en PHP : `prefork`.
   - API ou sites modernes : `worker` ou `event`.


</div>


---

## Apache HTTPD 2.4 : rappels et nouveautés

#### **Chargement des modules et modules à activer :**

<br>

<div style="font-size:35px">

- Apache HTTPD est un serveur modulaire, ce qui signifie que ses fonctionnalités peuvent être étendues ou réduites en activant ou désactivant des modules. 
- Le choix des modules à activer dépend de vos besoins spécifiques, comme la sécurité, les performances, ou les fonctionnalités nécessaires pour votre application.

</div>


---


## Apache HTTPD 2.4 : rappels et nouveautés

#### **Chargement des modules et modules à activer :**

<div style="font-size:27px">

### **1. Gestion des modules dans Apache**

#### **b. Activer un module**
- Sur **Ubuntu/Debian** :
  ```bash
  sudo a2enmod module_name
  ```
  Exemple : Activer le module `rewrite` :
  ```bash
  sudo a2enmod rewrite
  sudo systemctl reload apache2
  ```
</div>


---


## Apache HTTPD 2.4 : rappels et nouveautés

#### **Chargement des modules et modules à activer :**

<div style="font-size:27px">

### **1. Gestion des modules dans Apache**

- Sur **CentOS/RHEL** :
  - Modifiez le fichier principal de configuration (`httpd.conf`) ou les fichiers sous `/etc/httpd/conf.modules.d/`.
  - Exemple :
    ```apache
    LoadModule rewrite_module modules/mod_rewrite.so
    ```
  - Rechargez la configuration :
    ```bash
    sudo systemctl reload httpd
    ````

</div>

---

## Apache HTTPD 2.4 : rappels et nouveautés

#### **Chargement des modules et modules à activer :**

<div style="font-size:29px">

### **1. Gestion des modules dans Apache**

#### **c. Désactiver un module**
- Sur **Ubuntu/Debian** :
  ```bash
  sudo a2dismod module_name
  sudo systemctl reload apache2
  ```

- Sur **CentOS/RHEL** :
  - Commentez ou supprimez la ligne `LoadModule` correspondante.
``

</div>


---

#### Apache HTTPD 2.4 : rappels et nouveautés

##### **Chargement des modules et modules à activer :**

##### **2. Modules essentiels et leurs rôles : Module base**


| **Module**       | **Description**                                                                 |
|-------------------|---------------------------------------------------------------------------------|
| `mod_core`       | Module de base, chargé automatiquement.                                         |
| `mod_mpm_*`      | Multi-Processing Modules (choisir `prefork`, `worker` ou `event`).              |
| `mod_log_config` | Permet de configurer les journaux (access logs, error logs).                    |
| `mod_status`     | Fournit une page de statut pour surveiller les performances et connexions actives. |
| `mod_alias`      | Permet de créer des alias d'URL (par exemple, rediriger `/docs` vers un dossier spécifique). |

</div>

---

#### Apache HTTPD 2.4 : rappels et nouveautés

##### **Chargement des modules et modules à activer :**

##### **2. Modules essentiels et leurs rôles : Modules de performance**

<br>

| **Module**       | **Description**                                                                 |
|-------------------|---------------------------------------------------------------------------------|
| `mod_deflate`    | Compression des réponses HTTP pour économiser de la bande passante.             |
| `mod_headers`    | Permet de modifier les en-têtes HTTP.                                           |
| `mod_cache`      | Met en cache les contenus pour accélérer les réponses.                         |
| `mod_expires`    | Définit les politiques de cache basées sur la durée de vie des ressources.       |

</div>

---

#### Apache HTTPD 2.4 : rappels et nouveautés

##### **Chargement des modules et modules à activer :**

##### **2. Modules essentiels et leurs rôles : Modules de sécurité**


| **Module**       | **Description**                                                                 |
|-------------------|---------------------------------------------------------------------------------|
| `mod_ssl`        | Ajoute le support HTTPS (SSL/TLS).                                              |
| `mod_security`   | Améliore la sécurité en bloquant les requêtes malveillantes (WAF - Web Application Firewall). |
| `mod_auth_basic` | Authentification basique par mot de passe.                                      |
| `mod_auth_digest`| Authentification plus sécurisée que `mod_auth_basic`.                          |
| `mod_evasive`    | Protège contre les attaques DDoS ou brute-force en limitant les connexions répétées. |

</div>

---

#### Apache HTTPD 2.4 : rappels et nouveautés

##### **Chargement des modules et modules à activer :**

##### **2. Modules essentiels et leurs rôles : Redirections et réécritures**

<br>

| **Module**       | **Description**                                                                 |
|-------------------|---------------------------------------------------------------------------------|
| `mod_rewrite`    | Permet la réécriture des URL (très utilisé pour les sites dynamiques et les SEO).|
| `mod_redirect`   | Configure des redirections HTTP simples.                                        |

</div>

---

#### Apache HTTPD 2.4 : rappels et nouveautés

##### **Chargement des modules et modules à activer :**

##### **2. Modules essentiels et leurs rôles : Applications et contenu**


| **Module**       | **Description**                                                                 |
|-------------------|---------------------------------------------------------------------------------|
| `mod_cgi`        | Exécute des scripts CGI.                                                        |
| `mod_php`        | Intègre le support PHP directement dans Apache (souvent remplacé par PHP-FPM).  |
| `mod_proxy`      | Configure un proxy ou une passerelle pour d'autres serveurs.                    |
| `mod_proxy_http` | Support pour le proxy HTTP (souvent utilisé avec `mod_proxy`).                  |
| `mod_proxy_fcgi` | Permet la communication avec PHP-FPM via FastCGI.                               |
| `mod_wsgi`       | Permet d'exécuter des applications Python (par exemple, Django).                |

</div>

---


#### Apache HTTPD 2.4 : rappels et nouveautés

##### **Chargement des modules et modules à activer :**

<br>

##### **2. Modules essentiels et leurs rôles : Modules de diagnostic**


| **Module**       | **Description**                                                                 |
|-------------------|---------------------------------------------------------------------------------|
| `mod_info`       | Fournit des informations sur la configuration actuelle d'Apache.               |
| `mod_status`     | Fournit des statistiques en temps réel sur le serveur (ex. requêtes actives).   |

</div>

---

## Apache HTTPD 2.4 : rappels et nouveautés

#### **Chargement des modules et modules à activer :**

<div style="font-size:27px">

### **3. Quels modules activer selon les besoins**

#### **a. Serveur web classique (HTML/CSS/JS uniquement)**
- Modules recommandés :
  - `mod_deflate`
  - `mod_headers`
  - `mod_alias`
  - `mod_expires`
  - `mod_status`


</div>


---

## Apache HTTPD 2.4 : rappels et nouveautés

#### **Chargement des modules et modules à activer :**

<div style="font-size:27px">

### **3. Quels modules activer selon les besoins**

#### **b. Serveur pour applications dynamiques (PHP, Python, etc.)**
- Modules recommandés :
  - `mod_php` ou `mod_proxy_fcgi` (pour PHP-FPM)
  - `mod_wsgi` (pour Python)
  - `mod_rewrite`
  - `mod_deflate`
  - `mod_headers`
</div>


---

## Apache HTTPD 2.4 : rappels et nouveautés

#### **Chargement des modules et modules à activer :**

<div style="font-size:27px">

### **3. Quels modules activer selon les besoins**

#### **c. Serveur sécurisé avec HTTPS**
- Modules recommandés :
  - `mod_ssl`
  - `mod_security`
  - `mod_evasive`
  - `mod_headers`
  - `mod_rewrite`

</div>


---

## Apache HTTPD 2.4 : rappels et nouveautés

#### **Chargement des modules et modules à activer :**

<div style="font-size:27px">

### **3. Quels modules activer selon les besoins**

#### **d. Serveur proxy/reverse proxy**
- Modules recommandés :
  - `mod_proxy`
  - `mod_proxy_http`
  - `mod_proxy_fcgi`
  - `mod_cache`
  - `mod_headers`

</div>


---


## Apache HTTPD 2.4 : rappels et nouveautés

#### **Chargement des modules et modules à activer :**


<div style="font-size:30px">

### **4. Désactiver les modules inutiles**

<br>

Pour des raisons de performance et de sécurité, désactivez les modules non utilisés. Par exemple :
- Modules CGI si vous n'exécutez pas de scripts CGI :
  ```bash
  sudo a2dismod cgi
  sudo systemctl reload apache2
  ```

</div>


---

## Apache HTTPD 2.4 : rappels et nouveautés

#### **Chargement des modules et modules à activer :**


<div style="font-size:30px">

### **5. Vérification après activation/désactivation**

- **Tester la configuration Apache :**
  ```bash
  apache2ctl configtest
  ```
  Exemple de sortie si tout est correct :
  ```
  Syntax OK
  ```

- **Vérifier les modules activés :**
  ```bash
  apache2ctl -M
  ```

</div>


---

## Apache HTTPD 2.4 : rappels et nouveautés

#### **Chargement des modules et modules à activer :**


<div style="font-size:30px">

### **Résumé**

1. **Modules essentiels** : `mod_ssl`, `mod_rewrite`, `mod_headers`, `mod_status`.
2. **Modules à activer selon les besoins spécifiques** :
   - Performance : `mod_deflate`, `mod_expires`, `mod_cache`.
   - Sécurité : `mod_security`, `mod_evasive`.
   - Applications dynamiques : `mod_php`, `mod_wsgi`, `mod_proxy_fcgi`.
3. Désactivez les modules inutiles pour améliorer la sécurité et les performances.

</div>


---

## Apache HTTPD 2.4 : rappels et nouveautés

#### **Nouveaux types de contextes dans Apache HTTPD 2.4**

<br>

<div style="font-size:32px">

- Apache HTTPD 2.4 introduit des fonctionnalités permettant une gestion plus fine des configurations grâce à de nouveaux **types de contextes**. 
- Ces contextes permettent de personnaliser les comportements selon des conditions ou des environnements spécifiques.

- Ces contextes sont une manière de structurer la configuration du serveur en fonction de l’endroit ou du niveau où les directives s’appliquent.

</div>

---

## Apache HTTPD 2.4 : rappels et nouveautés

#### **Nouveaux types de contextes dans Apache HTTPD 2.4**

<br>

<div style="font-size:25px">

### **1. Contextes standards existants dans Apache**

#### **a. Serveur global (`server config`)**
- **Description** :
  - Les directives s'appliquent à l'ensemble du serveur.
  - Configuré directement dans le fichier principal (`httpd.conf` ou `apache2.conf`).
- **Exemple** :
  ```apache
  ServerRoot "/etc/httpd"
  Listen 80
  ```
</div>

---


## Apache HTTPD 2.4 : rappels et nouveautés

#### **Nouveaux types de contextes dans Apache HTTPD 2.4**


<div style="font-size:27px">

#### **b. Virtual Host (`virtual host`)**
- **Description** :
  - Les directives s'appliquent à un hôte virtuel spécifique (une configuration pour chaque domaine ou sous-domaine).
  - Définies dans les fichiers comme `/etc/apache2/sites-available/example.conf`.
- **Exemple** :
  ```apache
  <VirtualHost *:80>
      ServerName www.example.com
      DocumentRoot /var/www/example
  </VirtualHost>
  ```
</div>

---

## Apache HTTPD 2.4 : rappels et nouveautés

#### **Nouveaux types de contextes dans Apache HTTPD 2.4**


<div style="font-size:28px">

#### **c. Répertoires (`directory`)**
- **Description** :
  - Les directives s'appliquent à des répertoires ou chemins spécifiques du système de fichiers.
- **Exemple** :
  ```apache
  <Directory "/var/www/html">
      AllowOverride All
      Require all granted
  </Directory>
  ```

</div>

---

## Apache HTTPD 2.4 : rappels et nouveautés

#### **Nouveaux types de contextes dans Apache HTTPD 2.4**


<div style="font-size:30px">

#### **d. Emplacement URL (`location`)**
- **Description** :
  - Les directives s'appliquent à des parties spécifiques de l'URL.
- **Exemple** :
  ```apache
  <Location "/admin">
      Require ip 192.168.1.0/24
  </Location>
  ```


</div>

---

## Apache HTTPD 2.4 : rappels et nouveautés

#### **Nouveaux types de contextes dans Apache HTTPD 2.4**


<div style="font-size:30px">

#### **e. Fichiers (`files`)**
- **Description** :
  - Les directives s'appliquent à des fichiers spécifiques.
- **Exemple** :
  ```apache
  <Files "secret.html">
      Require all denied
  </Files>
  ```


</div>

---

## Apache HTTPD 2.4 : rappels et nouveautés

#### **Nouveaux types de contextes dans Apache HTTPD 2.4**


<div style="font-size:30px">

#### **f. Module spécifique (`ifmodule`)**
- **Description** :
  - Les directives ne s’appliquent que si un module spécifique est activé.
- **Exemple** :
  ```apache
  <IfModule mod_ssl.c>
      SSLEngine On
  </IfModule>
  ```
</div>


---

## Apache HTTPD 2.4 : rappels et nouveautés

#### **Nouveaux types de contextes dans Apache HTTPD 2.4**

<div style="font-size:19px">

### **2. Nouveaux types ou usages de contextes dans Apache**

Avec les versions modernes d'Apache HTTPD (2.4+), de nouveaux types de contextes ou des fonctionnalités avancées ont émergé.

#### **a. Contexte conditionnel (`if`)**
- **Description** :
  - Permet de conditionner les directives en fonction d'expressions dynamiques (comme des variables d'environnement, des conditions IP, etc.).
- **Exemple** :
  ```apache
  <If "%{HTTP_HOST} == 'example.com'">
      DocumentRoot "/var/www/example"
  </If>
  ```
- **Utilité** :
  - Flexibilité accrue dans les configurations.
  - Simplifie les scénarios complexes comme la gestion de plusieurs domaines ou configurations spécifiques.

</div>


---

## Apache HTTPD 2.4 : rappels et nouveautés

#### **Nouveaux types de contextes dans Apache HTTPD 2.4**

<div style="font-size:18px">

### **2. Nouveaux types ou usages de contextes dans Apache**

Avec les versions modernes d'Apache HTTPD (2.4+), de nouveaux types de contextes ou des fonctionnalités avancées ont émergé.

#### **b. Contexte utilisateur (`userdir`)**
- **Description** :
  - Définit les règles pour les répertoires personnels des utilisateurs.
- **Exemple** :
  ```apache
  <IfModule mod_userdir.c>
      UserDir public_html
      <Directory "/home/*/public_html">
          AllowOverride FileInfo AuthConfig
          Require all granted
      </Directory>
  </IfModule>
  ```
- **Utilité** :
  - Permet aux utilisateurs du système d'avoir leurs propres sites web accessibles via `/~username`.

</div>


---

## Apache HTTPD 2.4 : rappels et nouveautés

#### **Nouveaux types de contextes dans Apache HTTPD 2.4**

<div style="font-size:22px">

### **2. Nouveaux types ou usages de contextes dans Apache**

Avec les versions modernes d'Apache HTTPD (2.4+), de nouveaux types de contextes ou des fonctionnalités avancées ont émergé.

#### **c. Contexte fichier spécifique (`filesmatch`)**
- **Description** :
  - S'applique aux fichiers correspondant à des motifs spécifiques (par exemple, via regex).
- **Exemple** :
  ```apache
  <FilesMatch "\.(txt|log)$">
      Require all denied
  </FilesMatch>
  ```
- **Utilité** :
  - Utile pour protéger certains types de fichiers sensibles (comme `.env`, `.htpasswd`, etc.).
</div>


---

## Apache HTTPD 2.4 : rappels et nouveautés

#### **Nouveaux types de contextes dans Apache HTTPD 2.4**

<div style="font-size:22px">

### **2. Nouveaux types ou usages de contextes dans Apache**

Avec les versions modernes d'Apache HTTPD (2.4+), de nouveaux types de contextes ou des fonctionnalités avancées ont émergé.

#### **d. Contexte dynamique pour les proxys (`proxy`)**
- **Description** :
  - Utilisé pour gérer des directives spécifiques aux connexions proxy ou reverse proxy.
- **Exemple** :
  ```apache
  <Proxy "http://backend.example.com">
      Require ip 192.168.0.0/24
  </Proxy>
  ```
- **Utilité** :
  - Permet un contrôle précis des connexions aux proxys.

</div>


---




## Apache HTTPD 2.4 : rappels et nouveautés

#### **Nouveaux types de contextes dans Apache HTTPD 2.4**

<div style="font-size:17px">

### **2. Nouveaux types ou usages de contextes dans Apache**

#### **e. Contexte dynamique par moteur de règles (`ifdefine` et `ifenv`)**

- **Description** :
  - Conditionne les directives en fonction des variables définies ou de l'environnement.
- **Exemples** :
  - **IfDefine** :
    ```apache
    <IfDefine DEBUG>
        LogLevel debug
    </IfDefine>
    ```
    - Utilisé pour activer/désactiver des configurations spécifiques en ligne de commande :
      ```bash
      apache2ctl -D DEBUG
      ```

  - **IfEnv** :
    ```apache
    <IfEnv TEST_ENV>
        DocumentRoot "/var/www/test"
    </IfEnv>
    ```
</div>


---

## Apache HTTPD 2.4 : rappels et nouveautés

#### **Nouveaux types de contextes dans Apache HTTPD 2.4**

<div style="font-size:28px">

### **2. Nouveaux types ou usages de contextes dans Apache**

#### **f. Contexte par balises HTML intégrées (`htaccess inline`)**

- **Description** :
  - Directives placées directement dans un fichier `.htaccess`.
- **Exemple** :
  ```apache
  RewriteEngine On
  RewriteRule ^about$ /about.html [L]
  ```

</div>

---

## Apache HTTPD 2.4 : rappels et nouveautés

#### **Nouveaux types de contextes dans Apache HTTPD 2.4**

<div style="font-size:28px">

### **3. Bonnes pratiques dans l’utilisation des contextes**

1. **Minimisez l’utilisation des fichiers `.htaccess`** :
   - Préférez la configuration dans les fichiers de Virtual Host ou dans le fichier principal (`httpd.conf`).
   - `.htaccess` ajoute une surcharge, car Apache le charge à chaque requête.

2. **Utilisez des directives conditionnelles (`<If>` et `<IfModule>`)** pour les configurations dynamiques ou spécifiques.

</div>

---

## Apache HTTPD 2.4 : rappels et nouveautés

#### **Nouveaux types de contextes dans Apache HTTPD 2.4**

<div style="font-size:28px">

### **3. Bonnes pratiques dans l’utilisation des contextes**

3. **Protégez les fichiers sensibles avec des contextes `FilesMatch`** :
   - Exemple pour protéger `.htpasswd` :
     ```apache
     <FilesMatch "^\.ht">
         Require all denied
     </FilesMatch>
     ```

4. **Planifiez et organisez vos Virtual Hosts** :
   - Placez un fichier distinct par domaine dans `/etc/apache2/sites-available/` ou `/etc/httpd/conf.d/`.
</div>

---

## Apache HTTPD 2.4 : rappels et nouveautés

#### **Nouveaux types de contextes dans Apache HTTPD 2.4**

<div style="font-size:28px">

### **Résumé des nouveaux types de contextes**

| **Contexte**       | **Usage principal**                                                                 |
|---------------------|-------------------------------------------------------------------------------------|
| `<If>`             | Conditions dynamiques basées sur des variables ou expressions (ex. domaine, IP).   |
| `<FilesMatch>`     | S'applique aux fichiers correspondant à un motif spécifique (regex).               |
| `<Proxy>`          | Contrôle des proxys et reverse-proxys.                                             |
| `<IfDefine>`       | Activé/désactivé en fonction de variables définies au démarrage d'Apache.          |
| `<IfEnv>`          | Activé/désactivé en fonction des variables d'environnement.  

</div>

---

## Apache HTTPD 2.4 : rappels et nouveautés

#### **Panorama des modules d'Apache 2.4**



<div style="font-size:35px">

<br>

Les modules étendent les fonctionnalités d'Apache HTTPD et permettent de répondre à des besoins spécifiques en matière de sécurité, performances, gestion des contenus, proxy, etc. Nous allons faire un petit panorama des modules par catégorie.


</div>


---

### Apache HTTPD 2.4 : rappels et nouveautés

#### **Panorama des modules d'Apache 2.4**


<div style="font-size:18px">

### **1. Modules de base**

Ces modules sont nécessaires au fonctionnement principal du serveur Apache.

| **Module**       | **Description**                                                                 |
|-------------------|---------------------------------------------------------------------------------|
| `core`           | Gère les fonctionnalités principales (traitement des requêtes, gestion des connexions). |
| `mpm_prefork`    | Multi-Processing Module basé sur des processus (mode non threadé).              |
| `mpm_worker`     | Multi-Processing Module basé sur des threads et processus combinés.             |
| `mpm_event`      | Extension de `worker`, optimisé pour les connexions persistantes (Keep-Alive).  |
| `mod_log_config` | Permet la configuration des journaux d'accès et d'erreurs.                     |
| `mod_version`    | Active des directives en fonction de la version d'Apache.   
</div>

---


#### Apache HTTPD 2.4 : rappels et nouveautés

##### **Panorama des modules d'Apache 2.4 : Modules de sécurité**

<div style="font-size:16px">

##### **2. Modules de sécurité**

| **Module**         | **Description**                                                                 |
|---------------------|---------------------------------------------------------------------------------|
| `mod_ssl`          | Ajoute le support HTTPS via SSL/TLS.                                           |
| `mod_auth_basic`   | Permet l'authentification basique par mot de passe.                            |
| `mod_auth_digest`  | Authentification plus sécurisée que `mod_auth_basic`.                         |
| `mod_authz_host`   | Contrôle l'accès basé sur les adresses IP.                                     |
| `mod_security`     | Module tiers pour un pare-feu d'application web (WAF).                        |
| `mod_evasive`      | Protège contre les attaques DoS/DDoS.                                          |
| `mod_headers`      | Ajoute ou modifie les en-têtes HTTP (utile pour CSP, CORS, etc.).              |
| `mod_remoteip`     | Remplace l'adresse IP client par celle envoyée dans un en-tête (proxy).        |  
</div>

---

#### Apache HTTPD 2.4 : rappels et nouveautés

##### **Panorama des modules d'Apache 2.4 : Modules de contenus**

| **Module**         | **Description**                                                                 |
|---------------------|---------------------------------------------------------------------------------|
| `mod_dir`          | Permet de définir le fichier par défaut d'un répertoire (exemple : `index.html`).|
| `mod_autoindex`    | Génère automatiquement une liste de fichiers pour les répertoires sans index.   |
| `mod_alias`        | Crée des alias d'URL (exemple : `/docs` vers un autre répertoire).              |
| `mod_negotiation`  | Permet la négociation de contenu (choix du fichier basé sur les préférences du client). |
| `mod_mime`         | Détermine le type MIME des fichiers en fonction de leur extension.              |
| `mod_rewrite`      | Permet la réécriture dynamique des URL.                                         |
| `mod_deflate`      | Comprime les réponses HTTP pour réduire l'utilisation de la bande passante.     |

</div>

---

#### Apache HTTPD 2.4 : rappels et nouveautés

##### **Panorama des modules d'Apache 2.4 : Modules de proxy et reverse proxy**

| **Module**         | **Description**                                                                 |
|---------------------|---------------------------------------------------------------------------------|
| `mod_proxy`        | Active les fonctionnalités de proxy de base.                                   |
| `mod_proxy_http`   | Permet le proxying des requêtes HTTP.                                           |
| `mod_proxy_fcgi`   | Gère les connexions avec des applications FastCGI (exemple : PHP-FPM).          |
| `mod_proxy_balancer`| Gère l'équilibrage de charge entre plusieurs serveurs backend.                 |
| `mod_proxy_wstunnel`| Supporte les connexions WebSocket via un proxy.                               |
| `mod_proxy_ftp`    | Permet le proxying des connexions FTP.                                          |

</div>

---


#### Apache HTTPD 2.4 : rappels et nouveautés

##### **Panorama des modules d'Apache 2.4 : Modules pour les applications dynamiques**

| **Module**         | **Description**                                                                 |
|---------------------|---------------------------------------------------------------------------------|
| `mod_cgi`          | Exécute des scripts CGI traditionnels.                                         |
| `mod_php`          | Intègre PHP directement dans Apache (souvent remplacé par PHP-FPM).            |
| `mod_perl`         | Permet d'exécuter des scripts Perl.                                            |
| `mod_wsgi`         | Exécute des applications Python (exemple : Django).                           |
| `mod_lua`          | Permet d'intégrer des scripts Lua dans Apache.                                |

</div>

---


#### Apache HTTPD 2.4 : rappels et nouveautés

##### **Panorama des modules d'Apache 2.4 : Modules conditionnels**


<br>

| **Module**         | **Description**                                                                 |
|---------------------|---------------------------------------------------------------------------------|
| `mod_status`       | Fournit une page de statut avec des informations sur les performances et connexions.|
| `mod_info`         | Affiche la configuration complète d'Apache pour diagnostic.                    |
| `mod_watchdog`     | Fournit un mécanisme pour surveiller les services d'arrière-plan d'Apache.      |

</div>

---

#### Apache HTTPD 2.4 : rappels et nouveautés

##### **Panorama des modules d'Apache 2.4 : Modules de diagnostic**


<br>

| **Module**         | **Description**                                                                 |
|---------------------|---------------------------------------------------------------------------------|
| `mod_if`           | Permet d'utiliser des conditions dynamiques basées sur des variables.           |
| `mod_define`       | Définit des variables utilisées dans les directives conditionnelles.            |
| `mod_env`          | Permet de définir ou d'utiliser des variables d'environnement.                  |

</div>

---

#### Apache HTTPD 2.4 : rappels et nouveautés

##### **Panorama des modules d'Apache 2.4 : Modules spécifiques à HTTP/2**


<br>

| **Module**         | **Description**                                                                 |
|---------------------|---------------------------------------------------------------------------------|
| `mod_http2`        | Ajoute le support HTTP/2, améliorant la vitesse et les performances des connexions. |

</div>

---

#### Apache HTTPD 2.4 : rappels et nouveautés

##### **Panorama des modules d'Apache 2.4 : Modules tiers populaires**


<br>

| **Module**         | **Description**                                                                 |
|---------------------|---------------------------------------------------------------------------------|
| `mod_pagespeed`    | Optimise automatiquement les performances des sites (cache, minification, etc.).|
| `mod_security`     | Fournit une protection WAF (pare-feu d'application web).                       |
| `mod_cluster`      | Permet le clustering avec Apache Tomcat ou JBoss.                              |

</div>

---

#### Apache HTTPD 2.4 : rappels et nouveautés

##### **Panorama des modules d'Apache 2.4**

<div style="font-size:20px">

### **Résumé des modules essentiels à activer selon les besoins**

| **Type de serveur**            | **Modules essentiels à activer**                                                    |
|--------------------------------|-------------------------------------------------------------------------------------|
| **Serveur de base (HTTP)**      | `mod_dir`, `mod_alias`, `mod_mime`, `mod_deflate`, `mod_headers`, `mod_rewrite`    |
| **Serveur HTTPS**               | `mod_ssl`, `mod_headers`, `mod_deflate`, `mod_expires`                             |
| **Proxy ou reverse proxy**      | `mod_proxy`, `mod_proxy_http`, `mod_proxy_fcgi`, `mod_proxy_balancer`              |
| **Applications dynamiques**     | `mod_php`, `mod_proxy_fcgi`, `mod_wsgi`, `mod_perl`                                |
| **Serveur haute performance**   | `mod_http2`, `mod_deflate`, `mod_expires`, `mod_cache`                             |
| **Sécurité avancée**            | `mod_security`, `mod_evasive`, `mod_headers`, `mod_remoteip`                       |

</div>

---

## Apache HTTPD 2.4 : rappels et nouveautés

#### **Support du protocole HTTP/2**

<br>

<div style="font-size:30px">

- Apache HTTPD 2.4 introduit le support natif du protocole **HTTP/2** via le module `mod_http2`. 
- HTTP/2 est conçu pour améliorer les performances des sites web modernes.
- Le protocole **HTTP/2** est une amélioration significative de HTTP/1.1. 
- Il améliore les performances, la latence et la gestion des connexions réseau. 
- Apache HTTPD prend en charge HTTP/2 à partir de la version **2.4.17**, mais il nécessite une configuration spécifique.


</div>


---


## Apache HTTPD 2.4 : rappels et nouveautés

#### **Support du protocole HTTP/2**

<div style="font-size:25px">

### **1. Avantages du protocole HTTP/2**
- **Multiplexage** : Plusieurs requêtes et réponses peuvent être échangées simultanément sur une seule connexion TCP.
- **Compression des en-têtes** : Réduit la taille des en-têtes HTTP, ce qui améliore les performances.
- **Priorisation des ressources** : Permet de prioriser certains contenus, comme les fichiers CSS ou JavaScript critiques.
- **Connexions persistantes** : Réduit la surcharge en limitant l'ouverture de connexions multiples.
- **Performances accrues** : Idéal pour les sites modernes, notamment ceux avec beaucoup de fichiers (CSS, JS, images).
</div>


---

## Apache HTTPD 2.4 : rappels et nouveautés

#### **Support du protocole HTTP/2**

<div style="font-size:25px">

### **2. Prérequis pour activer HTTP/2**

1. **Version d'Apache** : HTTP/2 est pris en charge à partir de la version **2.4.17**.
   - Vérifiez votre version avec :
     ```bash
     apache2 -v
     ```

2. **SSL/TLS activé** : HTTP/2 nécessite HTTPS pour être activé dans Apache.

3. **Module requis** : Le module `mod_http2` doit être activé.

4. **Version d’OpenSSL** : OpenSSL **1.0.2** ou supérieur est requis.

</div>

---

## Apache HTTPD 2.4 : rappels et nouveautés

#### **Support du protocole HTTP/2**

<div style="font-size:25px">

### **3. Étapes pour activer HTTP/2**

#### **a. Vérifier et activer le module HTTP/2**
- **Ubuntu/Debian** :
  Activez le module `http2` :
  ```bash
  sudo a2enmod http2
  sudo systemctl reload apache2
  ```

#### **b. Modifier la configuration SSL pour activer HTTP/2**

Ajoutez ou modifiez la directive `Protocols` dans le fichier de configuration du Virtual Host HTTPS.

</div>


---

## Apache HTTPD 2.4 : rappels et nouveautés

#### **Support du protocole HTTP/2**

<div style="font-size:20px">

### **3. Étapes pour activer HTTP/2**

#### **b. Modifier la configuration SSL pour activer HTTP/2**

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
</div>

---

## Apache HTTPD 2.4 : rappels et nouveautés

#### **Support du protocole HTTP/2**

<div style="font-size:24px">

### **3. Étapes pour activer HTTP/2**

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

</div>

---

## Apache HTTPD 2.4 : rappels et nouveautés

#### **Support du protocole HTTP/2**

<div style="font-size:24px">

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

</div>

---

## Apache HTTPD 2.4 : rappels et nouveautés

#### **Support du protocole HTTP/2**

<div style="font-size:24px">

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
</div>

---

## Apache HTTPD 2.4 : rappels et nouveautés

#### **Support du protocole HTTP/2**

<br>
<div style="font-size:30px">

### **5. Résolution des problèmes courants**

 3. **Problème de compatibilité avec MPM prefork**
   - HTTP/2 ne fonctionne pas avec `mpm_prefork`. Utilisez `mpm_event` ou `mpm_worker`.

</div>

---

## Apache HTTPD 2.4 : rappels et nouveautés

#### **Support du protocole HTTP/2**

<div style="font-size:35px">

### **6. Résumé des directives importantes pour HTTP/2**

| **Directive**       | **Description**                                                                 |
|---------------------|---------------------------------------------------------------------------------|
| `Protocols`         | Définit les protocoles pris en charge (exemple : `h2 h2c http/1.1`).           |
| `ProtocolsHonorOrder` | Priorise les protocoles dans l'ordre spécifié dans `Protocols`.                |
</div>

---

## Apache HTTPD 2.4 : rappels et nouveautés

#### **Support du protocole HTTP/2**

<div style="font-size:30px">

### **7. Bénéfices pratiques de HTTP/2**

1. **Sites à fort trafic** : HTTP/2 réduit la latence et améliore les temps de chargement.
2. **Applications modernes** : Idéal pour les sites avec beaucoup de fichiers (CSS, JS, images).
3. **Meilleures performances SEO** : Les moteurs de recherche favorisent les sites rapides et sécurisés.

</div>

---

## Apache HTTPD 2.4 : rappels et nouveautés

<br>
<br>
<center>
<img src="./assets/demo.jpg" width="400px">
</center>


---

<!-- _class: lead -->
<!-- _paginate: false -->

## Héberger des applications PHP

---

## Héberger des applications PHP

<div style="font-size:40px">

<br>
<br>

Héberger des applications PHP et faire cohabiter **PHP 5** et **PHP 7** sur le même serveur Apache peut être utile lorsque vous devez prendre en charge des applications héritées utilisant PHP 5 tout en supportant des applications modernes développées avec PHP 7 ou une version ultérieure.

</div>

---

## Héberger des applications PHP

#### Faire cohabiter PHP5 et PHP7.

<div style="font-size:29px">

### **1. Concepts clés**

1. **Apache et PHP-FPM** :
   - Apache peut exécuter plusieurs versions de PHP en utilisant **PHP-FPM** (FastCGI Process Manager).
   - Chaque version de PHP est configurée dans un pool distinct.

2. **Virtual Hosts** :
   - Vous configurez Apache pour assigner des versions spécifiques de PHP à des **Virtual Hosts** (ou des répertoires spécifiques).

</div>

---

## Héberger des applications PHP

#### Faire cohabiter PHP5 et PHP7.

<div style="font-size:25px">

### **2. Installation des versions PHP**

#### **Installer PHP 5.6 et PHP 7.4 (ou autres versions)**

Sur Ubuntu/Debian :
1. Installez les dépôts nécessaires :
   ```bash
   sudo apt update
   sudo apt install software-properties-common
   sudo add-apt-repository ppa:ondrej/php
   sudo apt update
   ```

2. Installez PHP 5.6 et PHP 7.4 avec leurs modules FPM :
   ```bash
   sudo apt install php5.6 php5.6-fpm php7.4 php7.4-fpm
   ```
</div>

---

## Héberger des applications PHP

#### Faire cohabiter PHP5 et PHP7.

<div style="font-size:18px">

### **3. Configurer PHP-FPM**

#### **Configurer les pools pour chaque version**
Les pools PHP-FPM permettent de séparer les applications exécutées sous différentes versions de PHP.

1. **Fichier de configuration PHP-FPM pour PHP 5.6** :
   - Chemin : `/etc/php/5.6/fpm/pool.d/www.conf` (ou similaire selon l'OS).
   - Modifiez le port ou le socket pour éviter les conflits.
     ```ini
     [www]
     listen = /run/php/php5.6-fpm.sock
     ```

2. **Fichier de configuration PHP-FPM pour PHP 7.4** :
   - Chemin : `/etc/php/7.4/fpm/pool.d/www.conf`.
   - Exemple :
     ```ini
     [www]
     listen = /run/php/php7.4-fpm.sock
     ```
</div>

---

## Héberger des applications PHP

#### Faire cohabiter PHP5 et PHP7.

<div style="font-size:30px">

<br>

### **3. Configurer PHP-FPM**

#### **Configurer les pools pour chaque version**

3. Redémarrez les services PHP-FPM :
   ```bash
   sudo systemctl restart php5.6-fpm
   sudo systemctl restart php7.4-fpm
   ```
</div>

---


## Héberger des applications PHP

#### Faire cohabiter PHP5 et PHP7.

<div style="font-size:35px">

### **4. Configurer Apache pour cohabiter PHP 5 et PHP 7**

#### **Activer les modules Apache nécessaires**
Activez les modules requis pour PHP-FPM et FastCGI :
```bash
sudo a2enmod proxy_fcgi setenvif
sudo a2enmod actions
sudo systemctl reload apache2
```


</div>

---

## Héberger des applications PHP

#### Faire cohabiter PHP5 et PHP7.

<div style="font-size:18px">

#### **Configurer des Virtual Hosts**
Créez un Virtual Host pour chaque version de PHP.

1. **Virtual Host pour PHP 5.6** :
   Fichier : `/etc/apache2/sites-available/php5.6.example.com.conf`.
   ```apache
   <VirtualHost *:80>
       ServerName php5.6.example.com
       DocumentRoot /var/www/php5.6

       <Directory /var/www/php5.6>
           AllowOverride All
           Require all granted
       </Directory>

       <FilesMatch \.php$>
           SetHandler "proxy:unix:/run/php/php5.6-fpm.sock|fcgi://localhost"
       </FilesMatch>

       ErrorLog ${APACHE_LOG_DIR}/php5.6-error.log
       CustomLog ${APACHE_LOG_DIR}/php5.6-access.log combined
   </VirtualHost>
   ```
</div>

---

## Héberger des applications PHP

#### Faire cohabiter PHP5 et PHP7.

<div style="font-size:18px">

#### **Configurer des Virtual Hosts**
Créez un Virtual Host pour chaque version de PHP.

2. **Virtual Host pour PHP 7.4** :
   Fichier : `/etc/apache2/sites-available/php7.4.example.com.conf`.
   ```apache
   <VirtualHost *:80>
       ServerName php7.4.example.com
       DocumentRoot /var/www/php7.4

       <Directory /var/www/php7.4>
           AllowOverride All
           Require all granted
       </Directory>

       <FilesMatch \.php$>
           SetHandler "proxy:unix:/run/php/php7.4-fpm.sock|fcgi://localhost"
       </FilesMatch>

       ErrorLog ${APACHE_LOG_DIR}/php7.4-error.log
       CustomLog ${APACHE_LOG_DIR}/php7.4-access.log combined
   </VirtualHost>
   ```
</div>

---

## Héberger des applications PHP

#### Faire cohabiter PHP5 et PHP7.

<div style="font-size:30px">

<br>

#### **Configurer des Virtual Hosts**
Créez un Virtual Host pour chaque version de PHP.

3. Activez les Virtual Hosts :
   ```bash
   sudo a2ensite php5.6.example.com.conf
   sudo a2ensite php7.4.example.com.conf
   sudo systemctl reload apache2
   ```

</div>

---

## Héberger des applications PHP

#### Faire cohabiter PHP5 et PHP7.

<div style="font-size:28px">

### **5. Tester la configuration**

1. **Créer des répertoires de test**
   - Répertoire pour PHP 5.6 :
     ```bash
     sudo mkdir -p /var/www/php5.6
     echo "<?php phpinfo(); ?>" | sudo tee /var/www/php5.6/index.php
     ```

   - Répertoire pour PHP 7.4 :
     ```bash
     sudo mkdir -p /var/www/php7.4
     echo "<?php phpinfo(); ?>" | sudo tee /var/www/php7.4/index.php
     ```

</div>

---

## Héberger des applications PHP

#### Faire cohabiter PHP5 et PHP7.

<div style="font-size:30px">

### **5. Tester la configuration**

2. **Accéder aux Virtual Hosts**
   - Testez les URLs :
     - [http://php5.6.example.com](http://php5.6.example.com) : Vous devriez voir la page PHP Info indiquant **PHP 5.6**.
     - [http://php7.4.example.com](http://php7.4.example.com) : Vous devriez voir la page PHP Info indiquant **PHP 7.4**.


</div>

---

## Héberger des applications PHP

#### Faire cohabiter PHP5 et PHP7.

<div style="font-size:26px">

### **6. Résolution des problèmes**

- **Erreur 500 ou aucun contenu PHP exécuté :**
  - Vérifiez que les sockets PHP-FPM sont actifs :
    ```bash
    sudo systemctl status php5.6-fpm
    sudo systemctl status php7.4-fpm
    ```
  - Vérifiez les logs Apache pour plus de détails :
    ```bash
    tail -f /var/log/apache2/error.log
    ```

- **Conflits entre les versions PHP :**
  - Assurez-vous que chaque version utilise un socket ou un port distinct.

</div>

---

## Héberger des applications PHP

#### Faire cohabiter PHP5 et PHP7.

<div style="font-size:32px">

### **Résumé**

1. Installez plusieurs versions de PHP via PHP-FPM.
2. Configurez des pools PHP-FPM distincts pour chaque version.
3. Configurez Apache avec des Virtual Hosts assignés à chaque version PHP.
4. Testez les sites pour vérifier que chaque Virtual Host utilise la version PHP appropriée.

</div>

---

## Héberger des applications PHP

#### CGI, CGID, Fast CGI et PHP-FPM

<br>

<div style="font-size:43px">

Ces technologies sont utilisées pour exécuter des applications dynamiques (comme des scripts PHP) sur des serveurs web tels qu'Apache.

</div>

---

## Héberger des applications PHP

#### CGI, CGID, Fast CGI et PHP-FPM


<div style="font-size:28px">

### **1. Common Gateway Interface (CGI)**

#### **Description**
- Le **Common Gateway Interface (CGI)** est une norme permettant à un serveur web de communiquer avec des scripts ou programmes externes pour générer des pages dynamiques.
- Fonctionnement :
  - Pour chaque requête, le serveur web exécute un nouveau processus pour le script.
  - Le script retourne une réponse au serveur qui est ensuite envoyée au client.
</div>

---

## Héberger des applications PHP

#### CGI, CGID, Fast CGI et PHP-FPM

<div style="font-size:23px">

### **1. Common Gateway Interface (CGI)**

#### **Avantages**
- Simplicité : Facile à implémenter.
- Compatibilité : Supporte de nombreux langages (Perl, Python, PHP, etc.).
- Isolation : Chaque requête étant un processus distinct, les erreurs affectent moins le reste du système.

#### **Inconvénients**
- Performance médiocre : Un nouveau processus est créé pour chaque requête, ce qui est coûteux en ressources.
- Non adapté à une charge élevée : Problèmes de scalabilité dans des environnements à fort trafic.

</div>

---

## Héberger des applications PHP

#### CGI, CGID, Fast CGI et PHP-FPM

<div style="font-size:23px">

### **2. CGI Daemon (CGID)**

#### **Description**
- **CGID** est une variante de CGI introduite dans Apache.
- Fonctionnement :
  - Les scripts CGI sont gérés par un daemon dédié au lieu d'être directement exécutés par le serveur web.
  - Cela réduit la surcharge liée à la création et destruction de processus à chaque requête.

#### **Avantages**
- Réduction de la charge serveur par rapport à CGI classique.
- Toujours isolé entre chaque requête.
</div>

---

## Héberger des applications PHP

#### CGI, CGID, Fast CGI et PHP-FPM

<div style="font-size:28px">

### **2. CGI Daemon (CGID)**

#### **Inconvénients**
- Toujours limité en termes de performance par rapport à des solutions modernes comme FastCGI.
- Légèrement plus complexe à configurer que CGI.

#### **Cas d'usage**
- Environnements où CGI est nécessaire mais avec une meilleure gestion des ressources.
</div>

---

## Héberger des applications PHP

#### CGI, CGID, Fast CGI et PHP-FPM

<div style="font-size:31px">

### **3. FastCGI**

#### **Description**
- **FastCGI** est une amélioration significative de CGI.
- Fonctionnement :
  - Au lieu de créer un nouveau processus pour chaque requête, un **processus persistant** est créé.
  - Ce processus persistant gère plusieurs requêtes, réduisant la surcharge.


</div>

---

## Héberger des applications PHP

#### CGI, CGID, Fast CGI et PHP-FPM

<div style="font-size:21px">

### **3. FastCGI**

#### **Avantages**
- Performance : Réutilisation des processus permet une gestion beaucoup plus rapide des requêtes.
- Support de plusieurs langages : Fonctionne avec PHP, Python, Ruby, etc.
- Scalabilité : Convient aux sites à fort trafic.
- Séparation : Les scripts peuvent être exécutés sur un serveur distant ou dédié.

#### **Inconvénients**
- Configuration plus complexe que CGI.
- Nécessite un module spécifique sur le serveur web (comme `mod_proxy_fcgi` pour Apache).

#### **Cas d'usage**
- Applications web dynamiques à fort trafic nécessitant de meilleures performances que CGI classique.


</div>

---

## Héberger des applications PHP

#### CGI, CGID, Fast CGI et PHP-FPM

<div style="font-size:29px">

### **4. PHP-FPM (FastCGI Process Manager)**

#### **Description**
- **PHP-FPM** est une implémentation de FastCGI spécialement conçue pour PHP.
- Fonctionnement :
  - Crée des processus persistants pour traiter les scripts PHP.
  - Offre des fonctionnalités avancées comme la gestion des pools, des limites de ressources, et des logs.


</div>

---

## Héberger des applications PHP

#### CGI, CGID, Fast CGI et PHP-FPM

<div style="font-size:20px">

### **4. PHP-FPM (FastCGI Process Manager)**

#### **Avantages**
- Performance : Plus rapide que CGI ou CGID.
- Gestion des ressources : Vous pouvez configurer des pools distincts avec des limites spécifiques (CPU, RAM) pour chaque application ou domaine.
- Fiabilité : Gère les scripts PHP de manière isolée pour minimiser les effets des erreurs.
- Flexibilité : Compatible avec plusieurs serveurs web (Apache, Nginx, etc.).

#### **Inconvénients**
- Limité à PHP.
- Configuration initiale légèrement complexe.

#### **Cas d'usage**
- Hébergement d'applications PHP modernes avec des besoins en performances et scalabilité.
- Idéal pour les serveurs web combinant Apache ou Nginx avec PHP.

</div>

---

## Héberger des applications PHP

#### CGI, CGID, Fast CGI et PHP-FPM

<div style="font-size:20px">

### **Comparaison des technologies**

| **Critère**          | **CGI**           | **CGID**         | **FastCGI**      | **PHP-FPM**       |
|-----------------------|-------------------|------------------|------------------|-------------------|
| **Performance**      | Faible            | Moyenne          | Élevée           | Très élevée       |
| **Compatibilité**     | Tous les langages | Tous les langages| Tous les langages| PHP uniquement    |
| **Scalabilité**       | Faible            | Moyenne          | Bonne            | Excellente        |
| **Complexité**        | Simple            | Moyenne          | Moyenne          | Moyenne à élevée  |
| **Usage recommandé** | Sites très simples| Sites avec faible à modérée charge| Sites dynamiques ou API modernes | Applications PHP performantes |

</div>

---


## Héberger des applications PHP

#### CGI, CGID, Fast CGI et PHP-FPM

<div style="font-size:30px">

<br>

### **Exemple de configuration**

#### **FastCGI avec PHP-FPM dans Apache**

1. **Activer les modules nécessaires**
   ```bash
   sudo a2enmod proxy_fcgi setenvif
   sudo systemctl restart apache2
   ```

</div>

---

## Héberger des applications PHP

#### CGI, CGID, Fast CGI et PHP-FPM

<div style="font-size:20px">

2. **Configurer un Virtual Host**
   Fichier : `/etc/apache2/sites-available/example.conf`
   ```apache
   <VirtualHost *:80>
       ServerName example.com
       DocumentRoot /var/www/html

       <Directory /var/www/html>
           AllowOverride All
           Require all granted
       </Directory>

       # Utiliser PHP-FPM via FastCGI
       <FilesMatch \.php$>
           SetHandler "proxy:unix:/run/php/php7.4-fpm.sock|fcgi://localhost"
       </FilesMatch>

       ErrorLog ${APACHE_LOG_DIR}/example-error.log
       CustomLog ${APACHE_LOG_DIR}/example-access.log combined
   </VirtualHost>
   ```
</div>

---

## Héberger des applications PHP

#### CGI, CGID, Fast CGI et PHP-FPM

<div style="font-size:30px">

3. **Redémarrer les services**
   ```bash
   sudo systemctl restart php7.4-fpm
   sudo systemctl reload apache2
   ```

4. **Tester**
   - Créez un fichier `info.php` :
     ```php
     <?php phpinfo(); ?>
     ```
   - Accédez à `http://example.com/info.php` et vérifiez que PHP fonctionne via FastCGI.

</div>

---

## Héberger des applications PHP

#### CGI, CGID, Fast CGI et PHP-FPM

<div style="font-size:30px">

### **Résumé**

1. **CGI** : Simple mais obsolète, adapté à des environnements avec peu de trafic.
2. **CGID** : Une légère amélioration de CGI pour des cas spécifiques.
3. **FastCGI** : Une solution moderne et performante pour exécuter des scripts dynamiques.
4. **PHP-FPM** : Implémentation de FastCGI optimisée pour PHP, offrant des performances et une scalabilité excellentes.
</div>

---

## Héberger des applications PHP

#### Droits et identité dédiée, sessions

<div style="font-size:35px">

<br>

Lors de l'hébergement d'applications PHP avec Apache et PHP-FPM, il est important de :
1. Configurer des **droits et identités dédiées** pour isoler les applications.
2. Gérer les **sessions PHP** pour assurer la sécurité et le bon fonctionnement des utilisateurs.

</div>

---

## Héberger des applications PHP

#### Droits et identité dédiée, sessions

<div style="font-size:24px">

### **1. Droits et identités dédiées**

Chaque application hébergée doit fonctionner sous une identité utilisateur différente pour :
- Isoler les applications (éviter qu'une application compromise n'accède aux données d'une autre).
- Améliorer la sécurité globale du serveur.

#### **Étapes pour configurer des identités dédiées**

##### **a. Créer des utilisateurs système pour chaque application**

1. Créez un utilisateur système pour chaque application :
   ```bash
   sudo adduser --system --no-create-home --group phpapp1
   sudo adduser --system --no-create-home --group phpapp2
   ```
</div>

---

## Héberger des applications PHP

#### Droits et identité dédiée, sessions

<div style="font-size:25px">

### **1. Droits et identités dédiées**

#### **Étapes pour configurer des identités dédiées**

##### **a. Créer des utilisateurs système pour chaque application**

2. Attribuez les permissions des répertoires des applications :
   - Application 1 :
     ```bash
     sudo chown -R phpapp1:phpapp1 /var/www/phpapp1
     ```
   - Application 2 :
     ```bash
     sudo chown -R phpapp2:phpapp2 /var/www/phpapp2
     ```

</div>

---

## Héberger des applications PHP

#### Droits et identité dédiée, sessions

<div style="font-size:23px">

### **1. Droits et identités dédiées**

#### **Étapes pour configurer des identités dédiées**

##### **b. Configurer PHP-FPM avec des pools dédiés**

1. Modifiez le fichier de configuration des pools PHP-FPM pour chaque application :

- **Pour l'application 1** : `/etc/php/7.4/fpm/pool.d/phpapp1.conf`
  ```ini
  [phpapp1]
  user = phpapp1
  group = phpapp1
  listen = /run/php/phpapp1.sock
  listen.owner = www-data
  listen.group = www-data
  listen.mode = 0660
  ```

</div>

---

## Héberger des applications PHP

#### Droits et identité dédiée, sessions

<div style="font-size:23px">

### **1. Droits et identités dédiées**

#### **Étapes pour configurer des identités dédiées**

##### **b. Configurer PHP-FPM avec des pools dédiés**

1. Modifiez le fichier de configuration des pools PHP-FPM pour chaque application :

- **Pour l'application 2** : `/etc/php/8.1/fpm/pool.d/phpapp2.conf`
  ```ini
  [phpapp2]
  user = phpapp2
  group = phpapp2
  listen = /run/php/phpapp2.sock
  listen.owner = www-data
  listen.group = www-data
  listen.mode = 0660
  ```

</div>

---

## Héberger des applications PHP

#### Droits et identité dédiée, sessions

<div style="font-size:17px">

### **1. Droits et identités dédiées**

#### **Étapes pour configurer des identités dédiées**

##### **b. Configurer PHP-FPM avec des pools dédiés**

1. Modifiez le fichier de configuration des pools PHP-FPM pour chaque application :

- **Pour l'application 2** : `/etc/php/8.1/fpm/pool.d/phpapp2.conf`
  ```ini
  [phpapp2]
  user = phpapp2
  group = phpapp2
  listen = /run/php/phpapp2.sock
  listen.owner = www-data
  listen.group = www-data
  listen.mode = 0660
  ```
2. Redémarrez PHP-FPM pour appliquer les changements :
   ```bash
   sudo systemctl restart php7.4-fpm
   sudo systemctl restart php8.1-fpm
   ```

</div>

---

## Héberger des applications PHP

#### Droits et identité dédiée, sessions

<div style="font-size:19px">

### **1. Droits et identités dédiées**

#### **Étapes pour configurer des identités dédiées**

##### **c. Configurer Apache pour utiliser les pools dédiés**

Modifiez les Virtual Hosts pour chaque application afin d'utiliser les sockets spécifiques :

- **Application 1** : `/etc/apache2/sites-available/phpapp1.conf`
  ```apache
  <VirtualHost *:80>
      ServerName phpapp1.example.com
      DocumentRoot /var/www/phpapp1

      <Directory /var/www/phpapp1>
          AllowOverride All
          Require all granted
      </Directory>

      <FilesMatch \.php$>
          SetHandler "proxy:unix:/run/php/phpapp1.sock|fcgi://localhost"
      </FilesMatch>
  </VirtualHost>
  ```
</div>

---

## Héberger des applications PHP

#### Droits et identité dédiée, sessions

<div style="font-size:19px">

### **1. Droits et identités dédiées**

#### **Étapes pour configurer des identités dédiées**

##### **c. Configurer Apache pour utiliser les pools dédiés**

Modifiez les Virtual Hosts pour chaque application afin d'utiliser les sockets spécifiques :

- **Application 2** : `/etc/apache2/sites-available/phpapp2.conf`
  ```apache
  <VirtualHost *:80>
      ServerName phpapp2.example.com
      DocumentRoot /var/www/phpapp2

      <Directory /var/www/phpapp2>
          AllowOverride All
          Require all granted
      </Directory>

      <FilesMatch \.php$>
          SetHandler "proxy:unix:/run/php/phpapp2.sock|fcgi://localhost"
      </FilesMatch>
  </VirtualHost>
  ```
</div>

---

## Héberger des applications PHP

#### Droits et identité dédiée, sessions

<div style="font-size:26px">

### **1. Droits et identités dédiées**

#### **Étapes pour configurer des identités dédiées**

##### **c. Configurer Apache pour utiliser les pools dédiés**

Activez les Virtual Hosts et redémarrez Apache :
```bash
sudo a2ensite phpapp1.conf phpapp2.conf
sudo systemctl reload apache2
```

</div>

---

## Héberger des applications PHP

#### Droits et identité dédiée, sessions

<div style="font-size:23px">

### **2. Gérer les sessions PHP**

Les sessions sont une fonctionnalité clé pour les applications web dynamiques. Une mauvaise gestion des sessions peut exposer les utilisateurs à des risques (vol de session, accès non autorisé).

#### **a. Configurer un répertoire de sessions dédié pour chaque application**

1. Créez des répertoires distincts pour stocker les fichiers de session :
   ```bash
   sudo mkdir -p /var/lib/php/sessions/phpapp1
   sudo mkdir -p /var/lib/php/sessions/phpapp2
   ```

2. Attribuez les permissions appropriées :
   ```bash
   sudo chown -R phpapp1:phpapp1 /var/lib/php/sessions/phpapp1
   sudo chown -R phpapp2:phpapp2 /var/lib/php/sessions/phpapp2
   sudo chmod 700 /var/lib/php/sessions/phpapp1 /var/lib/php/sessions/phpapp2
   ```


</div>

---

## Héberger des applications PHP

#### Droits et identité dédiée, sessions

<div style="font-size:23px">

### **2. Gérer les sessions PHP**

Les sessions sont une fonctionnalité clé pour les applications web dynamiques. Une mauvaise gestion des sessions peut exposer les utilisateurs à des risques (vol de session, accès non autorisé).

#### **b. Configurer PHP-FPM pour utiliser ces répertoires**

- **Pour l'application 1** :
  Modifiez `/etc/php/7.4/fpm/pool.d/phpapp1.conf` :
  ```ini
  php_value[session.save_path] = /var/lib/php/sessions/phpapp1
  ```

- **Pour l'application 2** :
  Modifiez `/etc/php/8.1/fpm/pool.d/phpapp2.conf` :
  ```ini
  php_value[session.save_path] = /var/lib/php/sessions/phpapp2
  ```

3. Redémarrez PHP-FPM :
   ```bash
   sudo systemctl restart php7.4-fpm
   sudo systemctl restart php8.1-fpm
   ```

</div>

---

## Héberger des applications PHP

#### Droits et identité dédiée, sessions

<div style="font-size:29px">

<br>

### **3. Sécurisation supplémentaire des sessions**

#### **a. Configurer des options sécurisées pour les sessions PHP**
Ajoutez ces directives dans les fichiers de configuration PHP (`php.ini`) :

```ini
session.cookie_httponly = 1      ; Empêche l'accès aux cookies via JavaScript.
session.cookie_secure = 1        ; Force l'utilisation de HTTPS pour les cookies.
session.use_strict_mode = 1      ; Rejette les sessions non valides.
session.use_only_cookies = 1     ; Interdit l'utilisation d'URL contenant des identifiants de session.
```

</div>

---

## Héberger des applications PHP

#### Droits et identité dédiée, sessions

<div style="font-size:30px">

<br>

### **3. Sécurisation supplémentaire des sessions**

#### **b. Utiliser une session basée sur une base de données**
Pour une application complexe, vous pouvez stocker les sessions dans une base de données sécurisée. Cela facilite la gestion et améliore la résilience des sessions.

</div>

---

## Héberger des applications PHP

#### Droits et identité dédiée, sessions

<div style="font-size:23px">

### **Résumé de la configuration**

1. **Identités dédiées** :
   - Chaque application a son propre utilisateur et groupe système.
   - Chaque application fonctionne dans un pool PHP-FPM dédié.

2. **Gestion des sessions** :
   - Les sessions sont stockées dans des répertoires séparés pour éviter toute interférence.
   - Les sessions sont sécurisées avec des options PHP appropriées.

3. **Résultat** :
   - Applications isolées en termes de droits et sessions.
   - Sécurité renforcée pour les utilisateurs et données.

</div>

---





## Héberger des applications PHP

<br>
<center>
<img src="./assets/demo.jpg" width="650px">
</center>


---


<!-- _class: lead -->
<!-- _paginate: false -->

## Contrôle d'accès et authentification

---


## Contrôle d'accès et authentification

##### Contrôle d'accès et authentification avec Apache : Modules mod_authz

<div style="font-size:35px">

<br>

Héberger des applications PHP et faire cohabiter PHP 5 et PHP 7 sur le même serveur Apache peut être utile lorsque vous devez prendre en charge des applications héritées utilisant PHP 5 tout en supportant des applications modernes développées avec PHP 7 ou une version ultérieure.
</div>

---

#### Contrôle d'accès et authentification

##### Contrôle d'accès et authentification avec Apache : Modules mod_authz

<div style="font-size:19px">

### **1. Introduction aux modules `mod_authz*`**

Les modules **`mod_authz_*`** sont utilisés pour définir les règles d'autorisation dans Apache HTTPD. Ils remplacent les anciennes directives (`Allow`, `Deny`, `Order`) utilisées dans les versions antérieures à 2.4.


| **Module**               | **Description**                                                                 |
|---------------------------|-------------------------------------------------------------------------------|
| **`mod_authz_host`**      | Autorisation basée sur des adresses IP ou des noms d'hôte.                   |
| **`mod_authz_user`**      | Autorisation basée sur des utilisateurs définis.                             |
| **`mod_authz_groupfile`** | Autorisation basée sur des groupes d'utilisateurs définis dans un fichier.   |
| **`mod_authz_core`**      | Base pour toutes les règles d'autorisation, fournit les directives `Require`.|
| **`mod_authz_owner`**     | Autorisation basée sur le propriétaire du fichier.                           |

</div>

---

#### Contrôle d'accès et authentification

##### Contrôle d'accès et authentification avec Apache : Modules mod_authz

<div style="font-size:21px">

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

</div>

---

### Contrôle d'accès et authentification

#### Contrôle d'accès et authentification avec Apache : Modules mod_authz

<div style="font-size:26px">

### **3. Contrôle d'accès avec `mod_authz_host`**

Le module **`mod_authz_host`** contrôle l'accès en fonction des adresses IP ou des noms d'hôte.

#### **Directives disponibles :**
| **Directive** | **Description**                          |
|---------------|------------------------------------------|
| `Require ip`  | Autorise l'accès aux adresses IP spécifiées. |
| `Require host`| Autorise l'accès aux noms d'hôte spécifiés.|

</div>

---

### Contrôle d'accès et authentification

#### Contrôle d'accès et authentification avec Apache : Modules mod_authz

<div style="font-size:26px">

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

</div>

---

### Contrôle d'accès et authentification

#### Contrôle d'accès et authentification avec Apache : Modules mod_authz

<div style="font-size:26px">

#### **Exemples :**

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

</div>

---


### Contrôle d'accès et authentification

#### Contrôle d'accès et authentification avec Apache : Modules mod_authz

<div style="font-size:26px">

### **4. Contrôle d'accès avec `mod_authz_user`**

Le module **`mod_authz_user`** permet de restreindre l'accès en fonction des utilisateurs authentifiés.

#### **Directive disponible :**
| **Directive**   | **Description**                          |
|-----------------|------------------------------------------|
| `Require user`  | Autorise l'accès uniquement aux utilisateurs spécifiés.|


</div>

---

### Contrôle d'accès et authentification

#### Contrôle d'accès et authentification avec Apache : Modules mod_authz

<div style="font-size:26px">

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


</div>

---

### Contrôle d'accès et authentification

#### Contrôle d'accès et authentification avec Apache : Modules mod_authz

<div style="font-size:26px">

### **5. Contrôle d'accès avec `mod_authz_groupfile`**

Le module **`mod_authz_groupfile`** gère l'accès basé sur des groupes d'utilisateurs définis dans un fichier.

#### **Configuration :**

1. **Créer un fichier de groupes** :
   Fichier : `/etc/apache2/groups`
   ```txt
   admin: john alice
   developers: mike sara
   ```

</div>

---

### Contrôle d'accès et authentification

#### Contrôle d'accès et authentification avec Apache : Modules mod_authz

<div style="font-size:22px">

### **5. Contrôle d'accès avec `mod_authz_groupfile`**

#### **Configuration :**

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
     
</div>

---

### Contrôle d'accès et authentification

#### Contrôle d'accès et authentification avec Apache : Modules mod_authz

<div style="font-size:25px">

### **6. Contrôle d'accès avancé avec `mod_authz_core`**

Le module **`mod_authz_core`** introduit des conditions logiques pour combiner des règles d'autorisation.

#### **Opérateurs disponibles :**
| **Opérateur**       | **Description**                                               |
|---------------------|---------------------------------------------------------------|
| `RequireAll`        | Toutes les conditions doivent être vraies.                    |
| `RequireAny`        | Au moins une des conditions doit être vraie.                  |
| `RequireNone`       | Aucune des conditions ne doit être vraie.                     |

</div>

---

### Contrôle d'accès et authentification

#### Contrôle d'accès et authentification avec Apache : Modules mod_authz

<div style="font-size:25px">

### **6. Contrôle d'accès avancé avec `mod_authz_core`**

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
</div>

---

### Contrôle d'accès et authentification

#### Contrôle d'accès et authentification avec Apache : Modules mod_authz

<div style="font-size:25px">

### **6. Contrôle d'accès avancé avec `mod_authz_core`**

#### **Exemples :**

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
</div>

---

### Contrôle d'accès et authentification

#### Contrôle d'accès et authentification avec Apache : Modules mod_authz

<div style="font-size:25px">

### **6. Contrôle d'accès avancé avec `mod_authz_core`**

#### **Exemples :**

- **Interdire tout sauf une IP spécifique** :
  ```apache
  <Directory "/var/www/secure">
      <RequireNone>
          Require ip 10.0.0.0/8
      </RequireNone>
      Require ip 192.168.1.100
  </Directory>
  ```

</div>

---

### Contrôle d'accès et authentification

#### Contrôle d'accès et authentification avec Apache : Modules mod_authz

<div style="font-size:20px">

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

</div>

---

### Contrôle d'accès et authentification

#### Contrôle d'accès et authentification avec Apache : Modules mod_authz

<div style="font-size:13px">

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

</div>

---

## Contrôle d'accès et authentification

#### **Authentification LDAP avec `mod_authnz_ldap` dans Apache HTTPD**

<div style="font-size:35px">

<br>

- Le module mod_authnz_ldap permet à Apache HTTPD d'authentifier les utilisateurs via un annuaire LDAP (Lightweight Directory Access Protocol). 
- Cela est souvent utilisé pour intégrer un serveur web dans un environnement d'entreprise utilisant des annuaires comme Active Directory ou OpenLDAP.

</div>

---

## Contrôle d'accès et authentification

#### **Authentification LDAP avec `mod_authnz_ldap` dans Apache HTTPD**

<div style="font-size:27px">

### **1. Prérequis**

1. **Modules Apache nécessaires** :
   - `mod_ldap`
   - `mod_authnz_ldap`

   Vérifiez leur disponibilité et activez-les :
   ```bash
   sudo a2enmod ldap authnz_ldap
   sudo systemctl restart apache2
   ```

</div>

---

## Contrôle d'accès et authentification

#### **Authentification LDAP avec `mod_authnz_ldap` dans Apache HTTPD**

<div style="font-size:27px">

2. **Serveur LDAP** :
   - Un serveur LDAP ou Active Directory doit être configuré et accessible.

3. **Informations LDAP nécessaires** :
   - URL du serveur LDAP (ex. : `ldap://ldap.example.com` ou `ldaps://ldap.example.com` pour une connexion sécurisée).
   - Base DN (ex. : `dc=example,dc=com`).
   - DN de liaison pour les recherches LDAP (ex. : `cn=admin,dc=example,dc=com`).
   - Attributs utilisateur (ex. : `uid` ou `sAMAccountName` dans Active Directory).

</div>

---

## Contrôle d'accès et authentification

#### **Authentification LDAP avec `mod_authnz_ldap` dans Apache HTTPD**

<div style="font-size:13px">

### **2. Configurer Apache pour l'authentification LDAP**

1. **Modifier le fichier de configuration du Virtual Host**
   Fichier : `/etc/apache2/sites-available/ldap-auth.conf` (Debian/Ubuntu) ou `/etc/httpd/conf.d/ldap-auth.conf` (CentOS/RHEL)

   Exemple de configuration :
   ```apache
   <VirtualHost *:443>
       ServerName secure.example.com
       DocumentRoot "/var/www/secure"

       SSLEngine On
       SSLCertificateFile "/etc/ssl/certs/example.com.crt"
       SSLCertificateKeyFile "/etc/ssl/private/example.com.key"

       # Configuration LDAP
       <Directory "/var/www/secure">
           AuthType Basic
           AuthName "LDAP Authentication"
           AuthBasicProvider ldap
           AuthLDAPURL "ldap://ldap.example.com:389/dc=example,dc=com?uid?sub?(objectClass=person)"
           AuthLDAPBindDN "cn=admin,dc=example,dc=com"
           AuthLDAPBindPassword "admin_password"

           # Autoriser uniquement les utilisateurs authentifiés
           Require valid-user
       </Directory>

       ErrorLog ${APACHE_LOG_DIR}/ldap-auth-error.log
       CustomLog ${APACHE_LOG_DIR}/ldap-auth-access.log combined
   </VirtualHost>
   ```
</div>

---


### Contrôle d'accès et authentification

##### **Authentification LDAP avec `mod_authnz_ldap` dans Apache HTTPD**

<div style="font-size:13px">

### **3. Explications des directives LDAP**

| **Directive**             | **Description**                                                                                               |
|---------------------------|-------------------------------------------------------------------------------------------------------------|
| **`AuthType`**            | Définit le type d'authentification (ici, `Basic`).                                                          |
| **`AuthName`**            | Message affiché dans la boîte de dialogue de connexion.                                                     |
| **`AuthBasicProvider`**   | Définit le fournisseur d'authentification (ici, `ldap`).                                                    |
| **`AuthLDAPURL`**         | URL du serveur LDAP, suivie du Base DN, de l'attribut utilisateur, et du filtre d'objets LDAP.              |
| **`AuthLDAPBindDN`**      | DN de l'utilisateur utilisé pour rechercher les entrées LDAP.                                               |
| **`AuthLDAPBindPassword`**| Mot de passe du DN de liaison.                                                                               |
| **`Require valid-user`**  | Autorise tous les utilisateurs LDAP authentifiés.                                                           |
</div>

---

### Contrôle d'accès et authentification

##### **Authentification LDAP avec `mod_authnz_ldap` dans Apache HTTPD**

<div style="font-size:22px">

### **3. Explications des directives LDAP**

#### **Exemple d’URL LDAP dans `AuthLDAPURL`** :
- LDAP classique :
  ```text
  ldap://ldap.example.com:389/dc=example,dc=com?uid?sub?(objectClass=person)
  ```
  - `dc=example,dc=com` : Base DN.
  - `uid` : Attribut utilisateur (peut être `sAMAccountName` dans Active Directory).
  - `sub` : Étendue de recherche (sous-arbre complet).
  - `(objectClass=person)` : Filtre optionnel pour restreindre les résultats.

- LDAP sécurisé (LDAPS) :
  ```text
  ldaps://ldap.example.com:636/dc=example,dc=com?uid?sub?(objectClass=person)
  ```

</div>

---
### Contrôle d'accès et authentification

##### **Authentification LDAP avec `mod_authnz_ldap` dans Apache HTTPD**

<div style="font-size:22px">

### **3. Explications des directives LDAP**

#### **Exemple d’URL LDAP dans `AuthLDAPURL`** :
- LDAP classique :
  ```text
  ldap://ldap.example.com:389/dc=example,dc=com?uid?sub?(objectClass=person)
  ```
  - `dc=example,dc=com` : Base DN.
  - `uid` : Attribut utilisateur (peut être `sAMAccountName` dans Active Directory).
  - `sub` : Étendue de recherche (sous-arbre complet).
  - `(objectClass=person)` : Filtre optionnel pour restreindre les résultats.

- LDAP sécurisé (LDAPS) :
  ```text
  ldaps://ldap.example.com:636/dc=example,dc=com?uid?sub?(objectClass=person)
  ```

</div>

---

## Contrôle d'accès et authentification

<br>
<center>
<img src="./assets/demo.jpg" width="650px">
</center>


---


<!-- _class: lead -->
<!-- _paginate: false -->

## Redirection, réécriture d'adresses, filtres

---

## Redirection, réécriture d'adresses, filtres


<div style="font-size:30px">

<br>


- La redirection consiste à informer le client (navigateur ou autre) que la ressource demandée se trouve à une autre adresse (URL). Cela se fait en renvoyant un code HTTP de redirection.
- La réécriture d'adresses (URL rewriting) modifie l'URL en interne sur le serveur sans que le client en soit informé. Elle est utilisée pour rendre les URLs plus propres ou pour masquer la structure réelle du site.

- Les filtres permettent de transformer ou d'ajouter des éléments aux réponses HTTP avant qu'elles ne soient envoyées au client

</div>

---

### Redirection, réécriture d'adresses, filtres

##### **DocumentRoot et le module mod_alias**

<div style="font-size:19px">

### **1. DocumentRoot**
#### **Définition** :
- `DocumentRoot` est une directive dans Apache qui spécifie le répertoire racine où le serveur web cherche les fichiers à servir pour un domaine ou un sous-domaine donné.

#### **Usage dans un VirtualHost** :
- Chaque hôte virtuel (`VirtualHost`) peut avoir son propre `DocumentRoot`.
- Exemple :
  ```apache
  <VirtualHost *:80>
      ServerName example.com
      ServerAlias www.example.com
      DocumentRoot "/var/www/html/example"
      <Directory "/var/www/html/example">
          Options Indexes FollowSymLinks
          AllowOverride All
          Require all granted
      </Directory>
  </VirtualHost>
  ```
</div>

---

### Redirection, réécriture d'adresses, filtres

##### **DocumentRoot et le module mod_alias**

<div style="font-size:30px">

<br>

### **1. DocumentRoot**

  - **ServerName** : Nom du domaine principal.
  - **ServerAlias** : Autres noms de domaine pointant vers ce même site.
  - **DocumentRoot** : Chemin où Apache recherche les fichiers pour ce site.
  - **Directory** : Permet de spécifier les permissions et options pour le répertoire associé.
</div>

---

### Redirection, réécriture d'adresses, filtres

##### **DocumentRoot et le module mod_alias**

<div style="font-size:28px">

### **1. DocumentRoot**

#### **Options courantes pour DocumentRoot** :
- **Indexes** : Affiche une liste des fichiers si aucun fichier `index` n’est trouvé.
- **FollowSymLinks** : Autorise l'utilisation de liens symboliques.
- **AllowOverride** : Permet d'utiliser un fichier `.htaccess` pour modifier la configuration.

#### **Cas pratique** :
Si un fichier `index.html` est placé dans `/var/www/html/example`, il sera servi à l'URL `http://example.com`.

</div>

---

### Redirection, réécriture d'adresses, filtres

##### **DocumentRoot et le module mod_alias**

<div style="font-size:30px">

### **2. Module mod_alias**
#### **Définition** :
- `mod_alias` est un module d'Apache qui permet :
  - De créer des **alias** pour mapper une URL à un chemin différent sur le système de fichiers.
  - De configurer des **redirections simples** d'une URL vers une autre.

</div>

---

### Redirection, réécriture d'adresses, filtres

##### **DocumentRoot et le module mod_alias**

<div style="font-size:20px">

### **2. Module mod_alias**
#### **Alias** :
- Permet de servir des fichiers depuis un répertoire différent de `DocumentRoot`.
- Syntaxe :
  ```apache
  Alias /url_path /system_path
  ```
- Exemple :
  ```apache
  Alias /images "/var/www/assets/images"
  <Directory "/var/www/assets/images">
      Options Indexes FollowSymLinks
      AllowOverride None
      Require all granted
  </Directory>
  ```
    - **Alias /images** : Lorsque l'utilisateur accède à `http://example.com/images`, Apache sert les fichiers depuis `/var/www/assets/images`.
</div>

---

### Redirection, réécriture d'adresses, filtres

##### **DocumentRoot et le module mod_alias**

<div style="font-size:25px">

### **2. Module mod_alias**
#### **Redirect** :
- **Description** : Redirige une URL vers une nouvelle URL.
- **Syntaxe** :
  ```apache
  Redirect [status] [URL_PATH] [TARGET_URL]
  ```
  - **`status`** : Le code HTTP (par défaut : `302`). Peut être `301`, `302`, ou d'autres codes de redirection.
  - **`URL_PATH`** : Le chemin relatif sur le serveur.
  - **`TARGET_URL`** : L'URL vers laquelle rediriger.

</div>

---

### Redirection, réécriture d'adresses, filtres

##### **DocumentRoot et le module mod_alias**

<div style="font-size:25px">

### **2. Module mod_alias**
#### **Redirect** :
- Redirige une URL vers une autre.
- Syntaxe :
  ```apache
  Redirect [status_code] /source_path http://destination_url
  ```
- Exemple :
  ```apache
  Redirect 301 /old-page.html http://example.com/new-page.html
  ```
  - Redirige de manière permanente (`301`) les requêtes pour `http://example.com/old-page.html` vers `http://example.com/new-page.html`.

</div>

---

### Redirection, réécriture d'adresses, filtres

#### **DocumentRoot et le module mod_alias**

<div style="font-size:28px">

## **3. Exemples pratiques avec `mod_alias`**

### **3.3. Redirection vers un autre site**
**Cas** : Rediriger `/blog` vers un blog externe.
```apache
Redirect 302 /blog https://blog.example.com
```

#### **Effet** :
- `http://example.com/blog` → `https://blog.example.com`.

</div>

---





### Redirection, réécriture d'adresses, filtres

##### **DocumentRoot et le module mod_alias**

<div style="font-size:27px">

### **2. Module mod_alias**
### **RedirectMatch**
- **Description** : Redirige les requêtes en utilisant des expressions régulières.
- **Syntaxe** :
  ```apache
  RedirectMatch [status] REGEX TARGET_URL
  ```
  - **`REGEX`** : Expression régulière pour correspondre aux URLs.
  - **`TARGET_URL`** : L'URL cible.

</div>

---

### Redirection, réécriture d'adresses, filtres

##### **DocumentRoot et le module mod_alias**

<div style="font-size:33px">

### **2. Module mod_alias**
#### **RedirectMatch** :
- Redirection conditionnelle utilisant des expressions régulières.
- Exemple :
  ```apache
  RedirectMatch 403 ^/private
  ```
  - Toute URL commençant par `/private` retourne une erreur `403 Forbidden`.

</div>

---

### Redirection, réécriture d'adresses, filtres

##### **DocumentRoot et le module mod_alias**

<div style="font-size:31px">

## **3. Exemples pratiques avec `mod_alias`**

### **3.1. Redirection permanente d'un domaine entier**
**Cas** : Rediriger tout le trafic de `http://example.com` vers `https://newexample.com`.
```apache
<VirtualHost *:80>
    ServerName example.com
    Redirect 301 / https://newexample.com/
</VirtualHost>
```

</div>

---

### Redirection, réécriture d'adresses, filtres

##### **DocumentRoot et le module mod_alias**

<div style="font-size:31px">

## **3. Exemples pratiques avec `mod_alias`**

### **3.2. Redirection d'une page spécifique**
**Cas** : Rediriger `/old-page` vers `/new-page`.
```apache
Redirect 301 /old-page /new-page
```

#### **Effet** :
- `http://example.com/old-page` → `http://example.com/new-page`.


</div>

---

### Redirection, réécriture d'adresses, filtres

##### **DocumentRoot et le module mod_alias**

<div style="font-size:31px">

## **3. Exemples pratiques avec `mod_alias`**

### **3.3. Redirection vers un autre site**
**Cas** : Rediriger `/blog` vers un blog externe.
```apache
Redirect 302 /blog https://blog.example.com
```

#### **Effet** :
- `http://example.com/blog` → `https://blog.example.com`.

</div>

---

### Redirection, réécriture d'adresses, filtres

##### **DocumentRoot et le module mod_alias**

<div style="font-size:31px">

## **3. Exemples pratiques avec `mod_alias`**

### **3.4. Redirection basée sur un pattern**
**Cas** : Rediriger toutes les pages terminant par `.html` vers `.php`.
```apache
RedirectMatch 301 (.*)\.html$ $1.php
```

#### **Effet** :
- `http://example.com/page.html` → `http://example.com/page.php`.

</div>

---

### Redirection, réécriture d'adresses, filtres

##### **DocumentRoot et le module mod_alias**

<div style="font-size:31px">

## **3. Exemples pratiques avec `mod_alias`**

### **3.5. Redirection conditionnelle (par sous-domaine)**
**Cas** : Rediriger `http://shop.example.com` vers une boutique externe.
```apache
<VirtualHost *:80>
    ServerName shop.example.com
    Redirect 301 / https://shopify.com/shop
</VirtualHost>
```

</div>

---

## Redirection, réécriture d'adresses, filtres

### **DocumentRoot et le module mod_alias**

<div style="font-size:28px">

## **3. Exemples pratiques avec `mod_alias`**

### **3.6. Redirection d'une section vers une nouvelle structure**
**Cas** : Rediriger toutes les pages d'une section `/old-section` vers `/new-section`.
```apache
RedirectMatch 301 ^/old-section/(.*)$ /new-section/$1
```

#### **Effet** :
- `http://example.com/old-section/page` → `http://example.com/new-section/page`.

</div>

---

## Redirection, réécriture d'adresses, filtres

### **DocumentRoot et le module mod_alias**

<div style="font-size:28px">

## **4. Cas d'erreurs ou pages spéciales**

### **4.1. Redirection d'une erreur 404 vers une page personnalisée**
**Cas** : Rediriger toutes les erreurs 404 vers une page `/not-found`.
```apache
ErrorDocument 404 /not-found
Redirect 301 /not-found /custom-404-page
```
</div>

---

## Redirection, réécriture d'adresses, filtres

### **DocumentRoot et le module mod_alias**

<div style="font-size:28px">

## **4. Cas d'erreurs ou pages spéciales**

### **4.2. Blocage d'une URL avec un code d'erreur**
**Cas** : Retourner une erreur 410 (Gone) pour une page supprimée.
```apache
Redirect 410 /deleted-page
```

#### **Effet** :
- Les utilisateurs recevront une erreur `410 Gone` pour cette page.

</div>

---

## Redirection, réécriture d'adresses, filtres

### **DocumentRoot et le module mod_alias**

<div style="font-size:28px">

## **5. Cas spécifiques : Gestion de HTTPS avec `mod_alias`**

### **Redirection de HTTP vers HTTPS**
**Cas** : Forcer tout le trafic HTTP à utiliser HTTPS.
```apache
<VirtualHost *:80>
    ServerName example.com
    Redirect 301 / https://example.com/
</VirtualHost>
```

</div>


---

## Redirection, réécriture d'adresses, filtres

### **DocumentRoot et le module mod_alias**

<div style="font-size:28px">

### **Exemples pratiques**
#### **2. Redirection avec mod_alias** :
Rediriger tout le contenu d'un ancien site vers un nouveau.
```apache
<VirtualHost *:80>
    ServerName oldsite.com
    Redirect 301 / http://newsite.com/
</VirtualHost>
```
- Toutes les requêtes vers `oldsite.com` sont redirigées vers `newsite.com`.

</div>

---

## Redirection, réécriture d'adresses, filtres

### **DocumentRoot et le module mod_alias**

<div style="font-size:20px">

### **Exemples pratiques**
#### **3. Alias pour des applications spécifiques** :
Vous hébergez plusieurs applications sur le même serveur :
- Application principale : `/var/www/html`.
- Interface d'administration : `/var/www/admin`.

**Configuration** :
```apache
<VirtualHost *:80>
    ServerName example.com
    DocumentRoot "/var/www/html"

    Alias /admin "/var/www/admin"
    <Directory "/var/www/admin">
        Options FollowSymLinks
        Require all granted
    </Directory>
</VirtualHost>
```
- Accéder à `http://example.com/admin` charge les fichiers depuis `/var/www/admin`.

</div>


---

## Redirection, réécriture d'adresses, filtres

### **Notion de Répertoire Virtuel et d'Alias dans Apache**

<div style="font-size:28px">

### **2. Alias**
#### **Définition** :
Un **alias** est une directive utilisée pour définir un chemin alternatif pour les ressources sur le serveur.

</div>

---

### Redirection, réécriture d'adresses, filtres

##### **Notion de Répertoire Virtuel et d'Alias dans Apache**

<div style="font-size:22px">

### **2. Alias**
#### **Cas d'Usage des Alias** :

1. **Organiser les Ressources** :
   - Si les images, CSS et JavaScript sont stockés dans des répertoires différents, vous pouvez les servir via des chemins d'alias pour plus de clarté.

   **Exemple** :
   ```apache
   Alias /images "/var/www/media/images"
   Alias /css "/var/www/media/styles"
   Alias /js "/var/www/media/scripts"
   <Directory "/var/www/media">
       Options Indexes FollowSymLinks
       AllowOverride None
       Require all granted
   </Directory>
   ```

</div>

---

### Redirection, réécriture d'adresses, filtres

##### **Notion de Répertoire Virtuel et d'Alias dans Apache**

<div style="font-size:17px">

### **2. Alias**
#### **Cas d'Usage des Alias** :

1. **Organiser les Ressources** :
   - Si les images, CSS et JavaScript sont stockés dans des répertoires différents, vous pouvez les servir via des chemins d'alias pour plus de clarté.

   **Exemple** :
   ```apache
   Alias /images "/var/www/media/images"
   Alias /css "/var/www/media/styles"
   Alias /js "/var/www/media/scripts"
   <Directory "/var/www/media">
       Options Indexes FollowSymLinks
       AllowOverride None
       Require all granted
   </Directory>
   ```
   **Résultat** :
   - Accéder à `http://example.com/images` sert les fichiers de `/var/www/media/images`.
   - Accéder à `http://example.com/css` sert les fichiers de `/var/www/media/styles`.

</div>

---

### Redirection, réécriture d'adresses, filtres

##### **Notion de Répertoire Virtuel et d'Alias dans Apache**

<div style="font-size:17px">

### **2. Alias**
#### **Cas d'Usage des Alias** :

2. **Héberger des Applications Séparées** :
   - Si vous avez une application d'administration et une application principale sur le même serveur.

   **Exemple** :
   ```apache
   Alias /admin "/var/www/apps/admin"
   Alias /app "/var/www/apps/main"
   <Directory "/var/www/apps">
       Options Indexes FollowSymLinks
       Require all granted
   </Directory>
   ```

   **Résultat** :
   - L'URL `http://example.com/admin` accède aux fichiers de `/var/www/apps/admin`.
   - L'URL `http://example.com/app` accède aux fichiers de `/var/www/apps/main`.

3. **Servir des Fichiers Hors de `DocumentRoot`** :
   - Les alias permettent de servir des fichiers qui ne sont pas situés dans le répertoire défini par `DocumentRoot`.

</div>

---

### Redirection, réécriture d'adresses, filtres

##### **Notion de Répertoire Virtuel et d'Alias dans Apache**

<div style="font-size:27px">

### **3. Redirection vs Alias**
<br>

Bien que les deux modifient l'interprétation des URL, ils ont des objectifs différents :
- **Alias** : Mappe une URL à un chemin physique sur le système de fichiers.
- **Redirect** : Envoie une réponse HTTP (comme une redirection 301) pour pointer vers une autre URL.

**Exemple de Redirection** :
```apache
Redirect 301 /old-url http://example.com/new-url
```
</div>

---

### Redirection, réécriture d'adresses, filtres

##### Les Règles de Ré-écriture d'URL et le Module mod_rewrite dans Apache

<div style="font-size:35px">

<br>

Le module **`mod_rewrite`** d'Apache est un outil puissant permettant de manipuler les URL pour :
- Les rendre plus lisibles.
- Mettre en place des redirections dynamiques.
- Masquer la structure interne du site Web.

</div>

---

### Redirection, réécriture d'adresses, filtres

##### Les Règles de Ré-écriture d'URL et le Module mod_rewrite dans Apache

<div style="font-size:30px">

<br>

### **1. Activation du Module `mod_rewrite`**
Avant d'utiliser `mod_rewrite`, vous devez vous assurer qu'il est activé :
```bash
sudo a2enmod rewrite
sudo systemctl restart apache2
```

</div>

---

### Redirection, réécriture d'adresses, filtres

##### Les Règles de Ré-écriture d'URL et le Module mod_rewrite dans Apache

<div style="font-size:25px">

### **2. Syntaxe de Base des Règles de Ré-écriture**
- Les règles se définissent dans :
  - La configuration du serveur (fichier `.conf`).
  - Un fichier `.htaccess` (si `AllowOverride` est activé).
  
#### **Directives Principales**
1. **RewriteEngine** :
   - Active ou désactive le module.
   ```apache
   RewriteEngine On
   ```

</div>

---

### Redirection, réécriture d'adresses, filtres

##### Les Règles de Ré-écriture d'URL et le Module mod_rewrite dans Apache

<div style="font-size:18px">

### **2. Syntaxe de Base des Règles de Ré-écriture**
  
#### **Directives Principales**

2. **RewriteRule** :
   - Définit une règle de ré-écriture pour transformer une URL entrante.
   ```apache
   RewriteRule Pattern Substitution [Flags]
   ```
   - **Pattern** : Une expression régulière définissant les URL à réécrire.
   - **Substitution** : L'URL modifiée ou le chemin vers lequel la requête est redirigée.
   - **Flags** : Modificateurs influençant le comportement de la règle.

3. **RewriteCond** :
   - Ajoute des conditions pour qu’une règle soit appliquée.
   ```apache
   RewriteCond TestString Condition
   ```

</div>

---

### Redirection, réécriture d'adresses, filtres

##### Les Règles de Ré-écriture d'URL et le Module mod_rewrite dans Apache

<div style="font-size:19px">

### **3. Exemples Pratiques**
#### **3.1 Réécriture Simple**
Transformer une URL complexe en une URL plus lisible.
```apache
RewriteEngine On
RewriteRule ^product/([0-9]+)$ /product.php?id=$1 [L]
```
- **Explication** :
  - `^product/([0-9]+)$` : Correspond aux URL commençant par `product/` et suivies d'un nombre.
  - `/product.php?id=$1` : Redirige la requête vers `product.php` avec le nombre capturé comme paramètre `id`.
  - `[L]` : Indique que cette règle est la dernière si elle correspond.

**Exemple** :  
URL entrée : `http://example.com/product/123`  
Apache réécrit : `/product.php?id=123`

</div>

---

### Redirection, réécriture d'adresses, filtres

##### Les Règles de Ré-écriture d'URL et le Module mod_rewrite dans Apache

<div style="font-size:32px">

#### **3.2 Redirection Permanente**
Rediriger une ancienne URL vers une nouvelle.
```apache
RewriteEngine On
RewriteRule ^old-page.html$ /new-page.html [R=301,L]
```
- **Explication** :
  - `[R=301]` : Envoie une redirection HTTP permanente (301).

</div>

---

### Redirection, réécriture d'adresses, filtres

##### Les Règles de Ré-écriture d'URL et le Module mod_rewrite dans Apache

<div style="font-size:22px">


#### **3.3 Forcer l’Utilisation de HTTPS**
Rediriger automatiquement tout le trafic HTTP vers HTTPS.
```apache
RewriteEngine On
RewriteCond %{HTTPS} off
RewriteRule ^(.*)$ https://%{HTTP_HOST}/$1 [R=301,L]
```
- **RewriteCond %{HTTPS} off** : Vérifie si HTTPS n'est pas utilisé.
- **RewriteRule** : Redirige vers l'URL équivalente avec HTTPS.

**Exemple** :  
URL entrée : `http://example.com/page`  
Résultat : `https://example.com/page`

</div>

---

### Redirection, réécriture d'adresses, filtres

##### Les Règles de Ré-écriture d'URL et le Module mod_rewrite dans Apache

<div style="font-size:22px">


#### **3.4 Forcer le WWW**
Rediriger tout le trafic vers une version avec `www`.
```apache
RewriteEngine On
RewriteCond %{HTTP_HOST} !^www\.
RewriteRule ^(.*)$ https://www.%{HTTP_HOST}/$1 [R=301,L]
```
- **RewriteCond %{HTTP_HOST} !^www\\.** : Vérifie que l'hôte ne commence pas par `www`.
- **RewriteRule** : Ajoute `www.` au début de l'URL.

**Exemple** :  
URL entrée : `http://example.com`  
Résultat : `http://www.example.com`

</div>


---


### Redirection, réécriture d'adresses, filtres

##### Les Règles de Ré-écriture d'URL et le Module mod_rewrite dans Apache

<div style="font-size:23px">

#### **3.5 Redirection Basée sur l’Agent Utilisateur**
Diriger les utilisateurs mobiles vers une version spécifique du site.
```apache
RewriteEngine On
RewriteCond %{HTTP_USER_AGENT} "Mobile|Android|iPhone"
RewriteRule ^(.*)$ /mobile/$1 [L]
```
- **RewriteCond %{HTTP_USER_AGENT}** : Vérifie si l'agent utilisateur correspond à des mots-clés mobiles.
- **RewriteRule** : Redirige vers le sous-répertoire `/mobile`.

**Exemple** :  
URL entrée : `http://example.com/page` (via mobile)  
Résultat : `http://example.com/mobile/page`

</div>


---

### Redirection, réécriture d'adresses, filtres

##### Les Règles de Ré-écriture d'URL et le Module mod_rewrite dans Apache

<div style="font-size:35px">

### **5. Flags (`[FLAGS]`)**
- **Description** : Modifient le comportement des règles.
- **Syntaxe** : Les flags sont spécifiés entre crochets `[ ]`, séparés par des virgules si multiples.

</div>


---

#### Redirection, réécriture d'adresses, filtres

###### Les Règles de Ré-écriture d'URL et le Module mod_rewrite dans Apache

<div style="font-size:18px">

### **5. Flags (`[FLAGS]`)**


| **Flag**   | **Description**                                                                 | **Cas d'utilisation**                                      |
|------------|---------------------------------------------------------------------------------|-----------------------------------------------------------|
| **`L`**    | Arrête le traitement des règles si celle-ci correspond.                        | Empêcher des règles supplémentaires d'être appliquées.    |
| **`R`**    | Effectue une redirection. Valeurs possibles : `R=301` (permanente), `R=302`.   | Rediriger vers un autre domaine ou protocole.             |
| **`P`**    | Proxy : Passe la requête à un serveur backend.                                 | Utilisé pour les proxys ou passerelles.                   |
| **`QSA`**  | Ajoute les paramètres de la requête à la cible (Query String Append).          | Conserver les paramètres GET d'une requête.               |
| **`NC`**   | Insensible à la casse (No Case).                                               | Utilisé pour les correspondances insensibles à la casse.  |
</div>


---

#### Redirection, réécriture d'adresses, filtres

###### Les Règles de Ré-écriture d'URL et le Module mod_rewrite dans Apache

<div style="font-size:18px">




| **Flag**   | **Description**                                                                 | **Cas d'utilisation**                                      |
|------------|---------------------------------------------------------------------------------|-----------------------------------------------------------|
| **`NE`**   | Empêche l'encodage des caractères spéciaux (No Escape).                        | Utile pour les caractères réservés dans les URL.          |
| **`OR`**   | Combine les conditions avec un **OU** logique (par défaut, c'est `ET`).         | Utilisé dans `RewriteCond`.                               |
| **`T`**    | Change le type MIME de la réponse.                                             | Servir un fichier avec un type MIME différent.            |
| **`G`**    | Retourne une erreur `410 Gone`.                                                | Indiquer qu'une ressource a été supprimée.                |
| **`F`**    | Retourne une erreur `403 Forbidden`.                                           | Bloquer l'accès à une ressource.                          |
| **`C`**    | Lien conditionnel entre plusieurs règles (Chain).                              | Utilisé pour enchaîner des règles qui doivent être testées ensemble. |

</div>


---

#### Redirection, réécriture d'adresses, filtres

###### Les Règles de Ré-écriture d'URL et le Module mod_rewrite dans Apache

<div style="font-size:18px">




| **Flag**   | **Description**                                                                 | **Cas d'utilisation**                                      |
|------------|---------------------------------------------------------------------------------|-----------------------------------------------------------|
| **`NE`**   | Empêche l'encodage des caractères spéciaux (No Escape).                        | Utile pour les caractères réservés dans les URL.          |
| **`OR`**   | Combine les conditions avec un **OU** logique (par défaut, c'est `ET`).         | Utilisé dans `RewriteCond`.                               |
| **`T`**    | Change le type MIME de la réponse.                                             | Servir un fichier avec un type MIME différent.            |
| **`G`**    | Retourne une erreur `410 Gone`.                                                | Indiquer qu'une ressource a été supprimée.                |
| **`F`**    | Retourne une erreur `403 Forbidden`.                                           | Bloquer l'accès à une ressource.                          |
| **`C`**    | Lien conditionnel entre plusieurs règles (Chain).                              | Utilisé pour enchaîner des règles qui doivent être testées ensemble. |

</div>

---



#### Redirection, réécriture d'adresses, filtres

###### Les Règles de Ré-écriture d'URL et le Module mod_rewrite dans Apache

<div style="font-size:17px">

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

   
</div>

---


#### Redirection, réécriture d'adresses, filtres

###### Les Règles de Ré-écriture d'URL et le Module mod_rewrite dans Apache

<div style="font-size:30px">

## **Variables et conditions disponibles dans `RewriteCond`**

### **1. Variables de serveur**
#### **Exemple : Rediriger en fonction de l'adresse IP**
```apache
RewriteCond %{REMOTE_ADDR} ^192\.168\.1\.100$
RewriteRule ^(.*)$ /blocked.html [L]
```
   
</div>

---

#### Redirection, réécriture d'adresses, filtres

###### Les Règles de Ré-écriture d'URL et le Module mod_rewrite dans Apache

<div style="font-size:20px">

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
   
</div>

---

#### Redirection, réécriture d'adresses, filtres

###### Les Règles de Ré-écriture d'URL et le Module mod_rewrite dans Apache

<div style="font-size:26px">

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

</div>

---

#### Redirection, réécriture d'adresses, filtres

###### Les Règles de Ré-écriture d'URL et le Module mod_rewrite dans Apache

<div style="font-size:24px">

### **2. Redirection conditionnelle**
**Objectif** : Rediriger uniquement les utilisateurs mobiles.
```apache
RewriteCond %{HTTP_USER_AGENT} "Mobile" [NC]
RewriteRule ^(.*)$ /mobile/ [L]
```

### **3. Blocage d'accès à un fichier**
**Objectif** : Bloquer l'accès à `config.php`.
```apache
RewriteRule ^config\.php$ - [F]
```

### **4. Servir une page multilingue**
**Objectif** : Réécrire `/fr/contact` en `contact.php?lang=fr`.
```apache
RewriteRule ^([a-z]{2})/(.*)$ $2.php?lang=$1 [L,QSA]
```

</div>

---

### Redirection, réécriture d'adresses, filtres

##### Les Règles de Ré-écriture d'URL et le Module mod_rewrite dans Apache

<div style="font-size:21px">

### **4. Flags Courants dans mod_rewrite**
Les flags modifient le comportement des règles. Quelques exemples importants :

4. **QSA (Query String Append)** :
   - Ajoute la chaîne de requête existante à la nouvelle URL.
   ```apache
   RewriteRule ^search/(.*)$ /search.php?query=$1 [QSA,L]
   ```

5. **P (Proxy)** :
   - Active le mode proxy, utile pour rediriger vers des serveurs externes.
   ```apache
   RewriteRule ^api/(.*)$ http://backend.example.com/$1 [P]
   ```
</div>

---

### Redirection, réécriture d'adresses, filtres

##### Les Règles de Ré-écriture d'URL et le Module mod_rewrite dans Apache

<div style="font-size:17px">

### **5. Bonnes Pratiques**
1. **Tester les Règles** :
   - Utilisez des outils comme [Regex101](https://regex101.com/) pour valider vos expressions régulières.

2. **Utilisation du Fichier `.htaccess`** :
   - Activez `AllowOverride All` dans la configuration Apache pour permettre l'utilisation de fichiers `.htaccess` :
     ```apache
     <Directory /var/www/html>
         AllowOverride All
     </Directory>
     ```

3. **Minimiser le Nombre de Règles** :
   - Trop de règles peut ralentir le serveur.

4. **Débogage** :
   - Activez les journaux de débogage pour vérifier l'exécution des règles.
   ```apache
   LogLevel alert rewrite:trace3
   ```

</div>

---

### Redirection, réécriture d'adresses, filtres

##### Les Règles de Ré-écriture d'URL et le Module mod_rewrite dans Apache

<div style="font-size:17px">

### **5. Bonnes Pratiques**
1. **Tester les Règles** :
   - Utilisez des outils comme [Regex101](https://regex101.com/) pour valider vos expressions régulières.

2. **Utilisation du Fichier `.htaccess`** :
   - Activez `AllowOverride All` dans la configuration Apache pour permettre l'utilisation de fichiers `.htaccess` :
     ```apache
     <Directory /var/www/html>
         AllowOverride All
     </Directory>
     ```

3. **Minimiser le Nombre de Règles** :
   - Trop de règles peut ralentir le serveur.

4. **Débogage** :
   - Activez les journaux de débogage pour vérifier l'exécution des règles.
   ```apache
   LogLevel alert rewrite:trace3
   ```

</div>

---

## Redirection, réécriture d'adresses, filtres

<br>
<center>
<img src="./assets/demo.jpg" width="600px">
</center>


---

<!-- _class: lead -->
<!-- _paginate: false -->

## Reverse Proxy et Cache

---


## Reverse Proxy et Cache

#### Les fonctionnalités de proxy et de Reverse Proxy

<div style="font-size:35px">

<br>

Apache peut fonctionner comme un **proxy** et un **reverse proxy**, offrant des fonctionnalités avancées pour rediriger, équilibrer et gérer les requêtes entre clients et serveurs backend.
</div>


---

## Reverse Proxy et Cache

#### Les fonctionnalités de proxy et de Reverse Proxy

<div style="font-size:19px">

### **1. Concepts de Proxy et Reverse Proxy**

#### **Proxy**
Un proxy agit comme un intermédiaire entre le client et le serveur. Il est utilisé pour :
- Cacher l'identité du client.
- Filtrer ou journaliser les requêtes sortantes.
- Accélérer les requêtes avec du caching.

#### **Reverse Proxy**
Un reverse proxy agit comme un intermédiaire entre les clients et un ou plusieurs serveurs backend. Il est utilisé pour :
- Cacher l'identité des serveurs backend.
- Répartir la charge entre plusieurs serveurs (load balancing).
- Terminer le SSL.
- Fournir un point unique d'accès sécurisé pour les clients.

</div>


---

## Reverse Proxy et Cache

#### Les fonctionnalités de proxy et de Reverse Proxy

<div style="font-size:20px">

### **2. Modules Nécessaires**

Apache utilise les modules suivants pour le proxy :
- **`mod_proxy`** : Fournit les fonctionnalités de base de proxy.
- **`mod_proxy_http`** : Proxy pour les requêtes HTTP.
- **`mod_proxy_balancer`** : Gestion du load balancing.
- **`mod_proxy_fcgi`** : Proxy pour les requêtes FastCGI.
- **`mod_ssl`** : Gestion des connexions HTTPS.

#### **Activer les modules**
Pour activer ces modules, utilisez les commandes :
```bash
sudo a2enmod proxy
sudo a2enmod proxy_http
sudo a2enmod proxy_balancer
sudo a2enmod ssl
sudo systemctl restart apache2
```

</div>


---

## Reverse Proxy et Cache

#### Les fonctionnalités de proxy et de Reverse Proxy

<div style="font-size:21px">

### **3. Configuration de Base : Reverse Proxy HTTP**

#### **Objectif**
Rediriger les requêtes d’un domaine vers un serveur backend situé à `http://backend.local`.

#### **Configuration**
Fichier `/etc/apache2/sites-available/reverse-proxy.conf` :
```apache
<VirtualHost *:80>
    ServerName example.com

    ProxyPreserveHost On
    ProxyPass / http://backend.local/
    ProxyPassReverse / http://backend.local/

    ErrorLog ${APACHE_LOG_DIR}/reverse_proxy_error.log
    CustomLog ${APACHE_LOG_DIR}/reverse_proxy_access.log combined
</VirtualHost>
```

</div>


---

## Reverse Proxy et Cache

#### Les fonctionnalités de proxy et de Reverse Proxy

<div style="font-size:27px">

### **3. Configuration de Base : Reverse Proxy HTTP**

#### **Objectif**
Rediriger les requêtes d’un domaine vers un serveur backend situé à `http://backend.local`.

#### **Explication**
- **`ProxyPreserveHost On`** : Préserve l'en-tête `Host` de la requête d'origine.
- **`ProxyPass`** : Redirige les requêtes entrantes vers `http://backend.local/`.
- **`ProxyPassReverse`** : Modifie les en-têtes de réponse pour correspondre à l'URL du client.


</div>


---

## Reverse Proxy et Cache

#### Les fonctionnalités de proxy et de Reverse Proxy

<div style="font-size:18px">

### **4. Reverse Proxy avec HTTPS**

#### **Objectif**
Proxy HTTPS vers un backend sécurisé situé à `https://secure-backend.local`.

#### **Configuration**
Fichier `/etc/apache2/sites-available/secure-reverse-proxy.conf` :
```apache
<VirtualHost *:443>
    ServerName secure.example.com

    SSLEngine On
    SSLCertificateFile /etc/ssl/certs/example.com.crt
    SSLCertificateKeyFile /etc/ssl/private/example.com.key

    ProxyPreserveHost On
    ProxyPass / https://secure-backend.local/
    ProxyPassReverse / https://secure-backend.local/

    ErrorLog ${APACHE_LOG_DIR}/secure_reverse_proxy_error.log
    CustomLog ${APACHE_LOG_DIR}/secure_reverse_proxy_access.log combined
</VirtualHost>
```

</div>


---

## Reverse Proxy et Cache

#### Les fonctionnalités de proxy et de Reverse Proxy

<div style="font-size:28px">

### **4. Reverse Proxy avec HTTPS**

#### **Objectif**
Proxy HTTPS vers un backend sécurisé situé à `https://secure-backend.local`.

#### **Explication**
- **SSL** : Termination SSL sur Apache avec des certificats locaux.
- Le reverse proxy gère les connexions sécurisées.


</div>


---

## Reverse Proxy et Cache

#### Les fonctionnalités de proxy et de Reverse Proxy

<div style="font-size:17px">

### **5. Load Balancing avec Reverse Proxy**

#### **Objectif**
Distribuer les requêtes entre plusieurs serveurs backend pour équilibrer la charge.

#### **Configuration**
Fichier `/etc/apache2/sites-available/load-balancer.conf` :
```apache
<Proxy "balancer://mycluster">
    BalancerMember http://backend1.local
    BalancerMember http://backend2.local
    ProxySet lbmethod=byrequests
</Proxy>

<VirtualHost *:80>
    ServerName example.com

    ProxyPreserveHost On
    ProxyPass / balancer://mycluster/
    ProxyPassReverse / balancer://mycluster/

    ErrorLog ${APACHE_LOG_DIR}/load_balancer_error.log
    CustomLog ${APACHE_LOG_DIR}/load_balancer_access.log combined
</VirtualHost>
```

</div>

---

## Reverse Proxy et Cache

#### Les fonctionnalités de proxy et de Reverse Proxy

<div style="font-size:24px">

### **5. Load Balancing avec Reverse Proxy**

#### **Objectif**
Distribuer les requêtes entre plusieurs serveurs backend pour équilibrer la charge.

#### **Explication**
- **`BalancerMember`** : Déclare les serveurs backend.
- **`ProxySet lbmethod=byrequests`** : Utilise une méthode d’équilibrage basée sur le nombre de requêtes.
- Méthodes disponibles :
  - **`byrequests`** : Répartit en fonction du nombre de requêtes.
  - **`bytraffic`** : Répartit en fonction du trafic.
  - **`bybusyness`** : Répartit en fonction de la charge actuelle.


</div>

---

## Reverse Proxy et Cache

#### Les fonctionnalités de proxy et de Reverse Proxy

<div style="font-size:18px">

### **6. Reverse Proxy pour WebSocket**

#### **Objectif**
Supporter les connexions WebSocket.

#### **Configuration**
```apache
<VirtualHost *:80>
    ServerName websocket.example.com

    ProxyPreserveHost On
    ProxyPass /ws/ ws://backend.local:8080/
    ProxyPassReverse /ws/ ws://backend.local:8080/

    ErrorLog ${APACHE_LOG_DIR}/websocket_proxy_error.log
    CustomLog ${APACHE_LOG_DIR}/websocket_proxy_access.log combined
</VirtualHost>
```

#### **Explication**
- **`ws://`** : Protocole WebSocket.
- **`/ws/`** : Redirige uniquement les requêtes vers WebSocket.

</div>

---

## Reverse Proxy et Cache

#### Les fonctionnalités de proxy et de Reverse Proxy

<div style="font-size:28px">

### **7. Gestion du Cache Proxy**

Apache peut mettre en cache les réponses backend pour améliorer les performances.

#### **Configuration**
Ajoutez le cache dans le VirtualHost :
```apache
CacheQuickHandler off
CacheLock on
CacheRoot /var/cache/apache2/proxy
CacheEnable disk /
CacheIgnoreNoLastMod On
CacheIgnoreCacheControl On
```

</div>

---


## Reverse Proxy et Cache

#### Les fonctionnalités de proxy et de Reverse Proxy

<div style="font-size:28px">

### **8. Sécurisation du Reverse Proxy**

#### **1. Filtrage des Requêtes**
Bloquez les requêtes indésirables :
```apache
<Proxy "http://backend.local">
    Require ip 192.168.1.0/24
</Proxy>
```

#### **2. Limitation de la Taille**
Limitez la taille des requêtes :
```apache
LimitRequestBody 10485760
```

</div>

---

## Reverse Proxy et Cache

#### Les fonctionnalités de proxy et de Reverse Proxy

<div style="font-size:35px">

### **9. Journaux pour Debugging**

Activez les journaux de débogage pour le proxy :
```apache
LogLevel proxy:trace2
```

Consultez les logs Apache :
```bash
sudo tail -f /var/log/apache2/proxy.log
```

</div>

---

## Reverse Proxy et Cache

#### Les fonctionnalités de proxy et de Reverse Proxy

<div style="font-size:17px">

### **Cas Pratiques et Résultats**

1. **Simple Reverse Proxy** :
   - Client : `http://example.com/page`
   - Backend : `http://backend.local/page`

2. **HTTPS Proxy** :
   - Client : `https://secure.example.com/api`
   - Backend : `https://secure-backend.local/api`

3. **Load Balancer** :
   - Client : `http://example.com/`
   - Backend 1 : `http://backend1.local/`
   - Backend 2 : `http://backend2.local/`

4. **WebSocket** :
   - Client : `ws://websocket.example.com/ws`
   - Backend : `ws://backend.local:8080/`

</div>

---

## Reverse Proxy et Cache

#### Les fonctionnalités de proxy et de Reverse Proxy

<div style="font-size:29px">

### **Résumé**

- Apache peut être configuré comme **proxy** et **reverse proxy** pour gérer des scénarios complexes.
- Les modules nécessaires incluent **`mod_proxy`**, **`mod_proxy_http`**, et **`mod_proxy_balancer`**.
- Les fonctionnalités incluent le routage HTTP/HTTPS, l'équilibrage de charge, le support WebSocket, et le caching.
- La sécurité et les performances peuvent être optimisées via des filtres et le caching.

</div>

---

## Reverse Proxy et Cache

#### Répartition de Charge et Affinité de Session avec Apache

<div style="font-size:36px">

<br>

Dans une architecture où plusieurs serveurs backend sont utilisés pour traiter les requêtes des utilisateurs, la **répartition de charge** (load balancing) et l'**affinité de session** (session stickiness) jouent un rôle clé pour garantir à la fois les performances et la cohérence des sessions.

</div>

---

## Reverse Proxy et Cache

#### Répartition de Charge et Affinité de Session avec Apache

<div style="font-size:28px">

### **1. Répartition de Charge**

#### **Définition**
La répartition de charge consiste à distribuer les requêtes des utilisateurs entre plusieurs serveurs backend pour :
- **Améliorer les performances** : Éviter la surcharge d'un seul serveur.
- **Augmenter la disponibilité** : Si un serveur est défaillant, les autres prennent le relais.
- **Assurer l'évolutivité** : Ajouter des serveurs backend au cluster en fonction de la demande.


</div>

---

## Reverse Proxy et Cache

#### Répartition de Charge et Affinité de Session avec Apache

<div style="font-size:20px">

### **1. Répartition de Charge**

#### **Méthodes de Répartition de Charge dans Apache**
Apache prend en charge plusieurs méthodes via le module **`mod_proxy_balancer`** :

| **Méthode**      | **Description**                                                                                       |
|-------------------|-----------------------------------------------------------------------------------------------------|
| **`byrequests`**  | Équilibre le nombre de requêtes en les répartissant de manière égale entre les serveurs.             |
| **`bytraffic`**   | Équilibre en fonction de la quantité de trafic (en octets) envoyée à chaque serveur.                 |
| **`bybusyness`**  | Envoie les requêtes aux serveurs les moins occupés.                                                  |
| **`heartbeat`**   | Utilise des informations de santé des serveurs backend (requiert le module **`mod_heartbeat`**).     |

</div>

---

## Reverse Proxy et Cache

#### Répartition de Charge et Affinité de Session avec Apache

<div style="font-size:19px">

### **1. Répartition de Charge**

#### **Configuration de Base**
Un exemple de configuration de répartition de charge avec **`byrequests`** :
```apache
<Proxy "balancer://mycluster">
    BalancerMember http://backend1.local
    BalancerMember http://backend2.local
    ProxySet lbmethod=byrequests
</Proxy>

<VirtualHost *:80>
    ServerName example.com

    ProxyPreserveHost On
    ProxyPass / balancer://mycluster/
    ProxyPassReverse / balancer://mycluster/

    ErrorLog ${APACHE_LOG_DIR}/load_balancer_error.log
    CustomLog ${APACHE_LOG_DIR}/load_balancer_access.log combined
</VirtualHost>
```
</div>

---

## Reverse Proxy et Cache

#### Répartition de Charge et Affinité de Session avec Apache

<div style="font-size:29px">

### **2. Affinité de Session (Session Stickiness)**

#### **Définition**
L'affinité de session est une stratégie permettant de garantir qu'un utilisateur est toujours redirigé vers le même serveur backend pour une session donnée. Cela est essentiel lorsque :
- Les sessions utilisateur sont stockées en mémoire locale sur les serveurs backend.
- Une requête sur un autre serveur pourrait entraîner une perte de session.

</div>

---

## Reverse Proxy et Cache

#### Répartition de Charge et Affinité de Session avec Apache

<div style="font-size:22px">

### **2. Affinité de Session (Session Stickiness)**

#### **Mécanismes de l'Affinité de Session**
Apache supporte l'affinité de session avec :
1. **Cookies de Session** :
   - Apache peut injecter un cookie unique pour identifier le serveur backend correspondant.
   - Le cookie est envoyé au client, qui le renvoie dans les requêtes suivantes.
   
2. **Paramètres d'URL** :
   - L'affinité est basée sur un identifiant stocké dans l'URL.
   
3. **Adresse IP Source** :
   - Apache dirige les requêtes provenant de la même IP vers le même backend.
</div>

---

## Reverse Proxy et Cache

#### Répartition de Charge et Affinité de Session avec Apache

<div style="font-size:19px">

### **2. Affinité de Session (Session Stickiness)**

#### **Configuration avec un Cookie**
Ajoutez l'option **`stickysession`** pour utiliser les cookies :
```apache
<Proxy "balancer://mycluster">
    BalancerMember http://backend1.local
    BalancerMember http://backend2.local
    ProxySet lbmethod=byrequests stickysession=JSESSIONID
</Proxy>

<VirtualHost *:80>
    ServerName example.com

    ProxyPreserveHost On
    ProxyPass / balancer://mycluster/
    ProxyPassReverse / balancer://mycluster/

    ErrorLog ${APACHE_LOG_DIR}/load_balancer_error.log
    CustomLog ${APACHE_LOG_DIR}/load_balancer_access.log combined
</VirtualHost>
```
</div>

---

## Reverse Proxy et Cache

#### Répartition de Charge et Affinité de Session avec Apache

<div style="font-size:30px">

### **2. Affinité de Session (Session Stickiness)**

#### **Configuration avec un Cookie**

- **`stickysession=JSESSIONID`** : Utilise le cookie `JSESSIONID` pour l'affinité.
- Si le cookie `JSESSIONID` n'est pas trouvé, Apache attribue un backend en fonction de la méthode de répartition de charge définie.

</div>

---

## Reverse Proxy et Cache

#### Répartition de Charge et Affinité de Session avec Apache

<div style="font-size:19px">

### **3. Avantages et Inconvénients**

#### **Avantages de l'Affinité de Session**
1. **Simplicité** : Maintient les sessions utilisateur cohérentes sans changer l'infrastructure existante.
2. **Compatibilité** : Fonctionne bien avec des applications web classiques.
3. **Évolutivité Modérée** : Peut être combinée avec des stratégies de répartition.

#### **Inconvénients**
1. **Risque de Déséquilibre** : Certains serveurs peuvent être surchargés si les sessions ne sont pas distribuées équitablement.
2. **Tolérance aux Pannes** :
   - Si un serveur backend tombe en panne, les sessions associées sont perdues.
3. **Complexité de Mise à l’Échelle** :
   - Si les sessions sont liées à un serveur spécifique, il est difficile d'ajouter ou de retirer des serveurs dynamiquement.

</div>

---

## Reverse Proxy et Cache

#### Répartition de Charge et Affinité de Session avec Apache

<div style="font-size:21px">

### **4. Améliorations Possibles**

1. **Stockage de Session Centralisé** :
   - Utilisez un stockage partagé (par exemple, Redis, Memcached, ou une base de données) pour conserver les sessions.
   - Cela élimine le besoin d'affinité stricte.

2. **Réplication de Session** :
   - Configurez les serveurs backend pour répliquer les sessions entre eux.
   - Permet à tout serveur de traiter les requêtes.

3. **Monitoring des Backends** :
   - Utilisez le module **`mod_status`** pour surveiller la charge et l'état des backends.
   - Activez un système d'alerte pour détecter les surcharges ou les pannes.

</div>

---


## Reverse Proxy et Cache

#### Répartition de Charge et Affinité de Session avec Apache

<div style="font-size:22px">

### **5. Surveillance et Debugging**

#### **Surveiller les Backends**
Activez le tableau de bord du balancer :
```apache
<Location "/balancer-manager">
    SetHandler balancer-manager
    Require ip 192.168.1.0/24
</Location>
```
Accédez à `http://example.com/balancer-manager` pour visualiser et ajuster manuellement les serveurs.

#### **Augmenter la Verbosité des Logs**
Ajoutez des logs détaillés pour déboguer la répartition de charge :
```apache
LogLevel proxy:trace2
```

</div>

---

## Reverse Proxy et Cache

#### Répartition de Charge et Affinité de Session avec Apache

<div style="font-size:19px">

### **6. Cas Pratique : API avec Load Balancer et Affinité**

Supposons une API où les sessions utilisateur sont critiques.

#### **Configuration**
```apache
<Proxy "balancer://apicluster">
    BalancerMember http://api1.local route=api1
    BalancerMember http://api2.local route=api2
    ProxySet lbmethod=byrequests stickysession=SESSIONID
</Proxy>

<VirtualHost *:80>
    ServerName api.example.com

    ProxyPreserveHost On
    ProxyPass /api balancer://apicluster/
    ProxyPassReverse /api balancer://apicluster/

    ErrorLog ${APACHE_LOG_DIR}/api_error.log
    CustomLog ${APACHE_LOG_DIR}/api_access.log combined
</VirtualHost>
```
</div>

---

## Reverse Proxy et Cache

#### Répartition de Charge et Affinité de Session avec Apache

<div style="font-size:30px">

### **6. Cas Pratique : API avec Load Balancer et Affinité**

Supposons une API où les sessions utilisateur sont critiques.

#### **Configuration**

- Les requêtes incluent un cookie `SESSIONID` pour l'affinité.
- Les backends `api1.local` et `api2.local` sont équilibrés selon le nombre de requêtes.

</div>

---

## Reverse Proxy et Cache

#### Gestion de l'État des Serveurs, Failover et Heartbeat avec Apache

<div style="font-size:35px">

<br>

Dans une architecture distribuée, Apache HTTPD peut gérer la disponibilité et la résilience des serveurs backend en surveillant leur état, en redirigeant le trafic en cas de panne (failover), et en utilisant un système de heartbeat pour vérifier la santé des serveurs.
</div>

---

## Reverse Proxy et Cache

#### Gestion de l'État des Serveurs, Failover et Heartbeat avec Apache

<div style="font-size:25px">

### **1. Concepts Clés**

#### **Gestion de l'État des Serveurs**
Apache peut surveiller l'état des serveurs backend pour déterminer :
- Si un serveur est en ligne ou hors ligne.
- La charge actuelle d'un serveur.

#### **Failover**
Le **failover** consiste à rediriger automatiquement les requêtes vers un serveur secondaire en cas de défaillance du serveur principal. Apache supporte cela nativement via **`mod_proxy_balancer`**.
</div>

---

## Reverse Proxy et Cache

#### Gestion de l'État des Serveurs, Failover et Heartbeat avec Apache

<div style="font-size:35px">

### **1. Concepts Clés**

#### **Heartbeat**
Le module **`mod_heartbeat`** permet à Apache de surveiller en temps réel l'état des serveurs backend à l'aide de messages de heartbeat (battements de cœur), qui indiquent si un serveur est opérationnel.

</div>

---

## Reverse Proxy et Cache

#### Gestion de l'État des Serveurs, Failover et Heartbeat avec Apache

<div style="font-size:24px">

### **2. Modules Requis**

Pour activer ces fonctionnalités, les modules suivants sont nécessaires :
- **`mod_proxy`** : Proxy et reverse proxy.
- **`mod_proxy_balancer`** : Load balancing et gestion de l'état des serveurs.
- **`mod_heartbeat`** : Surveillance de la santé des serveurs backend.
- **`mod_lbmethod_heartbeat`** : Méthode d'équilibrage basée sur le heartbeat.

#### **Activer les Modules**
```bash
sudo a2enmod proxy proxy_balancer heartbeat lbmethod_heartbeat
sudo systemctl restart apache2
```


</div>

---

## Reverse Proxy et Cache

#### Gestion de l'État des Serveurs, Failover et Heartbeat avec Apache

<div style="font-size:22px">

### **3. Configuration : Gestion de l'État des Serveurs**

#### **1. Surveiller l'État des Serveurs avec le Balancer Manager**
Ajoutez une interface de gestion pour surveiller les serveurs backend :
```apache
<Location "/balancer-manager">
    SetHandler balancer-manager
    Require ip 192.168.1.0/24
</Location>
```

Accédez à `http://example.com/balancer-manager` pour visualiser :
- L'état des serveurs backend.
- Les statistiques de charge.
- La possibilité de marquer un serveur comme "hors ligne".

</div>

---

## Reverse Proxy et Cache

#### Gestion de l'État des Serveurs, Failover et Heartbeat avec Apache

<div style="font-size:16px">

### **3. Configuration : Gestion de l'État des Serveurs**

#### **2. Détection Automatique des Pannes**
Apache détecte automatiquement les serveurs non réactifs et les désactive temporairement. Exemple de configuration avec `mod_proxy_balancer` :
```apache
<Proxy "balancer://mycluster">
    BalancerMember http://backend1.local retry=5
    BalancerMember http://backend2.local retry=5
    ProxySet lbmethod=byrequests
</Proxy>

<VirtualHost *:80>
    ServerName example.com

    ProxyPreserveHost On
    ProxyPass / balancer://mycluster/
    ProxyPassReverse / balancer://mycluster/

    ErrorLog ${APACHE_LOG_DIR}/failover_error.log
    CustomLog ${APACHE_LOG_DIR}/failover_access.log combined
</VirtualHost>
```
</div>

---

## Reverse Proxy et Cache

#### Gestion de l'État des Serveurs, Failover et Heartbeat avec Apache

<div style="font-size:35px">

### **3. Configuration : Gestion de l'État des Serveurs**

#### **2. Détection Automatique des Pannes**
- **`retry=5`** : Réessaie un serveur défaillant après 5 secondes.
- Les requêtes sont redirigées vers les serveurs encore en ligne.
</div>

---

## Reverse Proxy et Cache

#### Gestion de l'État des Serveurs, Failover et Heartbeat avec Apache

<div style="font-size:15px">

### **4. Failover avec Serveur Principal et de Secours**

#### **Objectif**
Configurer un serveur principal et un serveur de secours pour garantir la disponibilité.

#### **Configuration**
```apache
<Proxy "balancer://mycluster">
    BalancerMember http://primary-backend.local loadfactor=1
    BalancerMember http://secondary-backend.local status=+H loadfactor=10
    ProxySet lbmethod=byrequests
</Proxy>

<VirtualHost *:80>
    ServerName example.com

    ProxyPreserveHost On
    ProxyPass / balancer://mycluster/
    ProxyPassReverse / balancer://mycluster/

    ErrorLog ${APACHE_LOG_DIR}/failover_error.log
    CustomLog ${APACHE_LOG_DIR}/failover_access.log combined
</VirtualHost>
```
</div>

---

## Reverse Proxy et Cache

#### Gestion de l'État des Serveurs, Failover et Heartbeat avec Apache

<div style="font-size:27px">

### **4. Failover avec Serveur Principal et de Secours**

#### **Objectif**
Configurer un serveur principal et un serveur de secours pour garantir la disponibilité.

#### **Configuration**

- **`loadfactor`** : Priorité des serveurs (1 = haute priorité).
- **`status=+H`** : Définit le serveur comme de secours. Les requêtes y sont envoyées uniquement si le serveur principal est défaillant.

</div>

---

## Reverse Proxy et Cache

#### Gestion de l'État des Serveurs, Failover et Heartbeat avec Apache

<div style="font-size:27px">

### **5. Utilisation de Heartbeat pour la Surveillance des Backends**

#### **1. Activer Heartbeat sur les Backends**
Chaque serveur backend doit envoyer des messages de heartbeat pour signaler son état. Configurez Apache sur chaque backend pour activer `mod_heartbeat` :
```apache
<IfModule mod_heartbeat.c>
    HeartbeatAddress 239.0.0.1:12345
    HeartbeatMaxServers 10
    HeartbeatStorage file:/var/run/heartbeat
</IfModule>
```
</div>

---

## Reverse Proxy et Cache

#### Gestion de l'État des Serveurs, Failover et Heartbeat avec Apache

<div style="font-size:17px">

### **5. Utilisation de Heartbeat pour la Surveillance des Backends**

#### **2. Configurer Apache avec `mod_lbmethod_heartbeat`**
Sur le serveur frontend Apache, configurez l'équilibrage basé sur le heartbeat :
```apache
<Proxy "balancer://mycluster">
    BalancerMember http://backend1.local
    BalancerMember http://backend2.local
    ProxySet lbmethod=heartbeat
</Proxy>

<VirtualHost *:80>
    ServerName example.com

    ProxyPreserveHost On
    ProxyPass / balancer://mycluster/
    ProxyPassReverse / balancer://mycluster/

    ErrorLog ${APACHE_LOG_DIR}/heartbeat_error.log
    CustomLog ${APACHE_LOG_DIR}/heartbeat_access.log combined
</VirtualHost>
```
</div>

---

## Reverse Proxy et Cache

#### Gestion de l'État des Serveurs, Failover et Heartbeat avec Apache

<div style="font-size:29px">

### **5. Utilisation de Heartbeat pour la Surveillance des Backends**

#### **2. Configurer Apache avec `mod_lbmethod_heartbeat`**

- Apache ajustera automatiquement la répartition de charge en fonction des rapports de santé des serveurs backend.

</div>

---

## Reverse Proxy et Cache

#### Gestion de l'État des Serveurs, Failover et Heartbeat avec Apache

<div style="font-size:19px">

### **6. Résilience et Monitoring**

#### **Monitoring des Backends**
Pour surveiller les backends et identifier les pannes, utilisez :
1. **`balancer-manager`** : Interface web pour voir l'état des serveurs.
2. **Logs Apache** : Activez des logs détaillés pour le proxy et le heartbeat :
   ```apache
   LogLevel proxy:trace2 heartbeat:trace2
   ```

#### **Résilience**
- **Failover Automatique** : Redirige le trafic vers un serveur de secours en cas de panne.
- **Retry Automatique** : Réessaye les serveurs défaillants après un délai configurable.
- **Désactivation Manuelle** : Permet d'isoler un serveur pour maintenance via `balancer-manager`.

</div>

---

## Reverse Proxy et Cache

#### Gestion de l'État des Serveurs, Failover et Heartbeat avec Apache

<div style="font-size:20px">

### **7. Bonnes Pratiques**

1. **Redondance**
   - Ajoutez plusieurs serveurs backend pour garantir une haute disponibilité.
   - Configurez des serveurs de secours avec une priorité basse.

2. **Surveillance**
   - Intégrez des outils comme **Nagios** ou **Zabbix** pour surveiller les performances des backends.

3. **Optimisation des Temps de Requête**
   - Configurez des délais (`timeout`, `retry`) adaptés à votre infrastructure.

4. **Cohérence des Sessions**
   - Combinez failover avec une gestion centralisée des sessions (Redis, Memcached).

</div>

---

## Reverse Proxy et Cache

#### Gestion de l'État des Serveurs, Failover et Heartbeat avec Apache

<div style="font-size:30px">

### **9. Résumé**

Avec Apache HTTPD, la gestion de l'état des serveurs, le failover, et le heartbeat offrent des solutions robustes pour garantir la disponibilité et la résilience des services. Les fonctionnalités clés incluent :
- **Surveillance Automatique** : Détection des serveurs défaillants.
- **Failover** : Redirection transparente en cas de panne.
- **Heartbeat** : Vérification en temps réel de la santé des serveurs backend.
</div>

---

## Reverse Proxy et Cache

#### Gestion de l'État des Serveurs, Failover et Heartbeat avec Apache

<div style="font-size:30px">

### **9. Résumé**

Avec Apache HTTPD, la gestion de l'état des serveurs, le failover, et le heartbeat offrent des solutions robustes pour garantir la disponibilité et la résilience des services. Les fonctionnalités clés incluent :
- **Surveillance Automatique** : Détection des serveurs défaillants.
- **Failover** : Redirection transparente en cas de panne.
- **Heartbeat** : Vérification en temps réel de la santé des serveurs backend.
</div>

---

## Reverse Proxy et Cache

#### Keepalived et la Haute Disponibilité du Reverse-Proxy avec Apache

<div style="font-size:35px">

<br>

- Dans les architectures réseau, **Keepalived** est un outil clé pour garantir la **haute disponibilité** (HA) des services critiques tels que les reverse-proxies. 
- Associé à Apache, Keepalived permet de gérer automatiquement le basculement (failover) entre plusieurs serveurs en cas de défaillance.
</div>

---

## Reverse Proxy et Cache

#### Keepalived et la Haute Disponibilité du Reverse-Proxy avec Apache

<div style="font-size:18px">

### **1. Concepts Clés**

#### **Haute Disponibilité (HA)**
- Garantit la continuité du service même si un serveur tombe en panne.
- Implémente une redondance via plusieurs serveurs actifs et un mécanisme de basculement (failover).

#### **Keepalived**
- Utilise le protocole **VRRP** (Virtual Router Redundancy Protocol) pour offrir une IP virtuelle flottante (VIP) qui peut être associée dynamiquement à un serveur.
- Fonctionne avec une configuration **master-slave** ou **actif-actif** pour les serveurs.

#### **IP Virtuelle (VIP)**
- Une adresse IP virtuelle partagée entre plusieurs serveurs.
- Les clients se connectent à la VIP, et Keepalived assure que l’un des serveurs répond toujours.

</div>

---

## Reverse Proxy et Cache

#### Keepalived et la Haute Disponibilité du Reverse-Proxy avec Apache

<div style="font-size:19px">

### **2. Cas d’Usage : Reverse Proxy Apache avec Keepalived**

#### **Objectif**
- Configurer deux serveurs Apache comme reverse proxies avec Keepalived.
- Garantir que l’adresse IP virtuelle reste accessible même en cas de panne de l’un des serveurs.

#### **Architecture**
- **Serveurs** :
  - **Apache-1 (Master)** : 192.168.1.101
  - **Apache-2 (Backup)** : 192.168.1.102
- **VIP** : 192.168.1.200
- **Backends** :
  - Serveurs backend à équilibrer via Apache.

</div>

---

## Reverse Proxy et Cache

#### Keepalived et la Haute Disponibilité du Reverse-Proxy avec Apache

<div style="font-size:30px">

### **3. Installation de Keepalived**

Sur les deux serveurs Apache, installez **Keepalived** :
```bash
sudo apt update
sudo apt install keepalived
```
</div>

---

### Reverse Proxy et Cache

##### Keepalived et la Haute Disponibilité du Reverse-Proxy avec Apache

<div style="font-size:14px">

### **4. Configuration de Keepalived**

#### **Sur le Serveur Master (Apache-1)**

Éditez le fichier de configuration `/etc/keepalived/keepalived.conf` :
```plaintext
vrrp_instance VI_1 {
    state MASTER
    interface eth0  # Interface réseau associée
    virtual_router_id 51
    priority 100  # Priorité la plus élevée pour le master
    advert_int 1

    authentication {
        auth_type PASS
        auth_pass secretpass
    }

    virtual_ipaddress {
        192.168.1.200  # IP virtuelle
    }

    track_script {
        chk_apache
    }
}
# Script pour vérifier si Apache fonctionne
vrrp_script chk_apache {
    script "/usr/bin/systemctl is-active apache2"
    interval 2
    weight 2
}
```
</div>

---

### Reverse Proxy et Cache

##### Keepalived et la Haute Disponibilité du Reverse-Proxy avec Apache

<div style="font-size:14px">

### **4. Configuration de Keepalived**

#### **Sur le Serveur Backup (Apache-2)**
Éditez `/etc/keepalived/keepalived.conf` :
```plaintext
vrrp_instance VI_1 {
    state BACKUP
    interface eth0
    virtual_router_id 51
    priority 90  # Priorité inférieure pour le backup
    advert_int 1

    authentication {
        auth_type PASS
        auth_pass secretpass
    }

    virtual_ipaddress {
        192.168.1.200
    }

    track_script {
        chk_apache
    }
}

vrrp_script chk_apache {
    script "/usr/bin/systemctl is-active apache2"
    interval 2
    weight 2
}
```
</div>

---

### Reverse Proxy et Cache

##### Keepalived et la Haute Disponibilité du Reverse-Proxy avec Apache

<div style="font-size:35px">

### **4. Configuration de Keepalived**

#### **Sur le Serveur Backup (Apache-2)**
#### **Redémarrez Keepalived**
Sur les deux serveurs :
```bash
sudo systemctl enable keepalived
sudo systemctl start keepalived
```
</div>

---

### Reverse Proxy et Cache

##### Keepalived et la Haute Disponibilité du Reverse-Proxy avec Apache

<div style="font-size:14px">

### **5. Configuration d’Apache comme Reverse Proxy**

Configurez Apache sur les deux serveurs pour agir comme reverse proxies.

#### **VirtualHost Configuration**
Éditez `/etc/apache2/sites-available/000-default.conf` :

```apache
<VirtualHost *:80>
    ServerName 192.168.1.200  # Correspond à la VIP

    ProxyPreserveHost On

    <Proxy "balancer://mycluster">
        BalancerMember http://backend1.local
        BalancerMember http://backend2.local
        ProxySet lbmethod=byrequests
    </Proxy>

    ProxyPass / balancer://mycluster/
    ProxyPassReverse / balancer://mycluster/

    ErrorLog ${APACHE_LOG_DIR}/proxy_error.log
    CustomLog ${APACHE_LOG_DIR}/proxy_access.log combined
</VirtualHost>
```

Redémarrez Apache sur les deux serveurs :

```bash
sudo systemctl restart apache2

```
</div>

---

### Reverse Proxy et Cache

##### Keepalived et la Haute Disponibilité du Reverse-Proxy avec Apache

<div style="font-size:18px">

### **7. Tests**

1. **Tester la VIP** :
   - Accédez à `http://192.168.1.200` dans un navigateur ou utilisez `curl` :
     ```bash
     curl http://192.168.1.200
     ```

2. **Simuler une Panne du Master** :
   - Arrêtez Apache ou Keepalived sur le serveur Master (Apache-1) :
     ```bash
     sudo systemctl stop apache2
     ```
   - Vérifiez que la VIP passe au serveur Backup (Apache-2).

3. **Retour du Master** :
   - Relancez Apache sur le Master et observez que la VIP revient automatiquement sur le Master :
     ```bash
     sudo systemctl start apache2
     ```

</div>

---

### Reverse Proxy et Cache

##### Keepalived et la Haute Disponibilité du Reverse-Proxy avec Apache

<div style="font-size:21px">

### **8. Surveillance et Debugging**

1. **Logs de Keepalived** :
   - Vérifiez les logs pour identifier les problèmes :
     ```bash
     sudo journalctl -u keepalived
     ```

2. **Surveillance Active** :
   - Intégrez Keepalived à un système de monitoring (Nagios, Prometheus).

3. **Verifier les États VRRP** :
   - Utilisez `ip` pour vérifier l’attribution de la VIP :
     ```bash
     ip addr show
     ```

</div>

---

### Reverse Proxy et Cache

##### Keepalived et la Haute Disponibilité du Reverse-Proxy avec Apache

<div style="font-size:21px">

### **9. Bonnes Pratiques**

1. **Redondance Réseau** :
   - Configurez plusieurs interfaces réseau pour éviter un point de défaillance unique.

2. **Scripts de Santé** :
   - Personnalisez le script de vérification de santé pour inclure d’autres vérifications, comme la disponibilité des backends.

3. **Priorités Ajustées** :
   - Ajustez les priorités des serveurs en fonction de leurs capacités.

4. **Évitez les Conflits IP** :
   - Assurez-vous que la VIP n'est pas utilisée ailleurs dans le réseau.

</div>

---

### Reverse Proxy et Cache

##### Keepalived et la Haute Disponibilité du Reverse-Proxy avec Apache

<div style="font-size:30px">

### **10. Résumé**

Avec Keepalived et Apache :
- La **haute disponibilité** est assurée via une **VIP** flottante.
- Les **basculements automatiques** (failover) garantissent la continuité du service.
- Apache, configuré comme **reverse proxy**, assure l’équilibrage de charge entre les backends.
</div>

---

## Reverse Proxy et Cache

<br>
<center>
<img src="./assets/demo.jpg" width="650px">
</center>


---



<!-- _class: lead -->
<!-- _paginate: false -->

## Sécurité et détection d'attaques

---

## Sécurité et détection d'attaques

### Qu'est-ce que mod_security ? 

<div style="font-size:39px">

<br>

- **`mod_security`** est un **module Apache** qui agit comme un **WAF (Web Application Firewall)** pour protéger les applications web contre les attaques courantes. 
- Il inspecte et filtre les requêtes HTTP entrantes et sortantes en fonction de règles prédéfinies.
</div>

---


## Sécurité et détection d'attaques

### Qu'est-ce que mod_security ? 

<div style="font-size:23px">

### **1. Pourquoi utiliser `mod_security` ?**

#### **Protéger les Applications Web**
Les applications web sont exposées à des vulnérabilités comme :
- **Injection SQL**.
- **Cross-Site Scripting (XSS)**.
- **Cross-Site Request Forgery (CSRF)**.
- **Inclusions de fichiers (LFI, RFI)**.
- **Attaques DDoS**.

`mod_security` aide à prévenir ces attaques en surveillant les requêtes et en bloquant celles qui correspondent à des schémas malveillants.
</div>

---

## Sécurité et détection d'attaques

### Qu'est-ce que mod_security ? 

<div style="font-size:19px">

### **1. Pourquoi utiliser `mod_security` ?**

#### **Principales Fonctionnalités**
1. **Inspection Profonde des Requêtes** :
   - Analyse les en-têtes, les URI, les corps des requêtes et des réponses.
2. **Règles de Sécurité Personnalisables** :
   - Écriture et gestion de règles pour bloquer des patterns spécifiques.
3. **Détection d'Anomalies** :
   - Score d'anomalie basé sur des comportements suspects.
4. **Journalisation Complète** :
   - Enregistre les requêtes malveillantes pour analyse postérieure.
5. **Compatibilité avec OWASP CRS (Core Rule Set)** :
   - Ensemble de règles prêtes à l'emploi pour détecter les vulnérabilités les plus courantes.
</div>

---

## Sécurité et détection d'attaques

### Qu'est-ce que mod_security ? 

<div style="font-size:22px">

### **2. Installation et Configuration de `mod_security`**

#### **2.1 Installation sur Apache**
1. Installez le module :
   ```bash
   sudo apt update
   sudo apt install libapache2-mod-security2
   ```

2. Activez le module dans Apache :
   ```bash
   sudo a2enmod security2
   sudo systemctl restart apache2
   ```

3. Vérifiez que le module est actif :
   ```bash
   apachectl -M | grep security2
   ```

</div>

---

## Sécurité et détection d'attaques

### Qu'est-ce que mod_security ? 

<div style="font-size:15px">

### **2. Installation et Configuration de `mod_security`**

#### **2.2 Activer les Règles**

**Chemin des fichiers principaux :**
- **`modsecurity.conf`** : Configuration globale (`/etc/modsecurity/`).
- **`rules/*.conf`** : Ensemble de règles spécifiques.

1. Copiez le fichier de configuration par défaut :
   ```bash
   sudo cp /etc/modsecurity/modsecurity.conf-recommended /etc/modsecurity/modsecurity.conf
   ```

2. Activez le mode de détection ou de protection :
   - Ouvrez `/etc/modsecurity/modsecurity.conf` et modifiez :
     ```plaintext
     SecRuleEngine DetectionOnly
     ```
     - **`DetectionOnly`** : Journalise les attaques sans les bloquer (mode de test).
     - **`On`** : Bloque les requêtes malveillantes (mode de protection).

3. Redémarrez Apache :
   ```bash
   sudo systemctl restart apache2
   ```

</div>

---

## Sécurité et détection d'attaques

### Qu'est-ce que mod_security ? 

<div style="font-size:20px">

### **2. Installation et Configuration de `mod_security`**

#### **2.3 Ajouter les Règles OWASP CRS**

Les **OWASP Core Rule Set (CRS)** sont un ensemble de règles prêtes à l'emploi pour protéger contre les vulnérabilités courantes.

1. Installez les règles OWASP :
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

</div>

---

## Sécurité et détection d'attaques

### Qu'est-ce que mod_security ? 

<div style="font-size:16px">

### **3. Fonctionnement de `mod_security`**

#### **Analyse des Requêtes HTTP**
1. **Étapes d'Inspection** :
   - Inspection des URI, des en-têtes HTTP, des corps de requêtes, et des réponses.
   - Correspondance des requêtes avec les règles définies.
   - Blocage ou journalisation des requêtes malveillantes.

2. **Règles de Détection**
   - Les règles suivent ce format :
     ```plaintext
     SecRule ARGS "select.+from" "id:1001,phase:2,deny,status:403,msg:'SQL Injection Detected'"
     ```
     - **`ARGS`** : Analyse des paramètres de requête.
     - **`select.+from`** : Requête SQL malveillante détectée.
     - **`phase:2`** : Inspection après réception de tous les paramètres.
     - **`deny`** : Bloque la requête.
     - **`status:403`** : Retourne une erreur 403 au client.

</div>

---

## Sécurité et détection d'attaques

### Qu'est-ce que mod_security ? 

<div style="font-size:20px">

### **3. Fonctionnement de `mod_security`**

#### **Modes d'Action**
1. **DetectionOnly** :
   - Journalise les comportements suspects sans bloquer.
2. **On** :
   - Bloque les requêtes malveillantes et enregistre les détails.

#### **Journalisation**
- Les journaux sont enregistrés dans :
  ```bash
  /var/log/apache2/modsec_audit.log
  ```

</div>

---

## Sécurité et détection d'attaques

### Qu'est-ce que mod_security ? 

<div style="font-size:26px">

### **4. Exemple de Configuration**

#### **Protéger un Répertoire avec `mod_security`**

Configuration pour un répertoire spécifique (`/var/www/html/secure`) :
```apache
<Directory /var/www/html/secure>
    SecRuleEngine On
    SecRule ARGS "select.+from" "id:1001,phase:2,deny,status:403,msg:'SQL Injection Detected'"
    SecRule REQUEST_HEADERS:User-Agent "curl" "id:1002,phase:1,deny,status:403,msg:'Blocking curl User-Agent'"
</Directory>
```

- **`SecRule ARGS`** : Bloque les injections SQL.
- **`REQUEST_HEADERS:User-Agent`** : Bloque les requêtes avec un User-Agent `curl`.

</div>

---

## Sécurité et détection d'attaques

### Qu'est-ce que mod_security ? 

<div style="font-size:19px">

### **5. Surveillance et Débogage**

1. **Activer les Logs Détaillés**
   - Modifiez `/etc/modsecurity/modsecurity.conf` :
     ```plaintext
     SecDebugLog /var/log/apache2/modsec_debug.log
     SecDebugLogLevel 9
     ```

2. **Analyser les Journaux**
   - Audit des requêtes suspectes :
     ```bash
     tail -f /var/log/apache2/modsec_audit.log
     ```

3. **Test des Règles**
   - Simulez une attaque pour vérifier les règles :
     ```bash
     curl -H "User-Agent: curl" http://example.com/secure
     ```

</div>

---
## Sécurité et détection d'attaques

### Qu'est-ce que mod_security ? 

<div style="font-size:23px">

### **6. Bonnes Pratiques**

1. **Mode Test Avant Production** :
   - Activez `SecRuleEngine DetectionOnly` pour tester les règles sans bloquer les requêtes.

2. **Mises à Jour des Règles OWASP** :
   - Maintenez les règles OWASP CRS à jour pour suivre les nouvelles vulnérabilités.

3. **Personnalisation des Règles** :
   - Ajoutez des règles spécifiques à votre application.

4. **Journalisation Modérée** :
   - Ajustez `SecDebugLogLevel` pour éviter une surcharge des journaux.


</div>

---

## Sécurité et détection d'attaques

### Qu'est-ce que mod_security ? 

<div style="font-size:26px">

### **7. Limites de `mod_security`**

1. **Faux Positifs** :
   - Certaines requêtes légitimes peuvent être bloquées.
   - Nécessite une personnalisation des règles pour éviter cela.

2. **Performance** :
   - L'analyse approfondie des requêtes peut augmenter la latence.

3. **Complémentarité** :
   - `mod_security` ne remplace pas les bonnes pratiques de développement sécurisé.

</div>

---

## Sécurité et détection d'attaques

### Qu'est-ce que mod_security ? 

<div style="font-size:33px">

### **8. Résumé**

- **`mod_security`** est un **WAF puissant** intégré à Apache, capable de protéger contre les attaques courantes.
- Avec les **règles OWASP CRS**, il fournit une protection prête à l'emploi.
- Une configuration soigneuse et une surveillance active sont essentielles pour éviter les faux positifs et optimiser les performances.
</div>

---

## Sécurité et détection d'attaques

### Le Principe des Règles dans `mod_security`

<div style="font-size:35px">


<br>

Les **règles** de `mod_security` sont des directives permettant de définir des actions à exécuter lorsqu’une requête HTTP (ou une réponse) correspond à un certain **modèle** ou **critère**. Elles constituent l'épine dorsale de `mod_security`, permettant de détecter, bloquer, ou journaliser des comportements malveillants ou suspects.
</div>

---

## Sécurité et détection d'attaques

### Le Principe des Règles dans `mod_security`

<div style="font-size:30px">

### **1. Structure d'une Règle**

Une règle `mod_security` est composée de plusieurs éléments clés :

```plaintext
SecRule VARIABLE OPERATOR [ACTIONS]
```

- **`VARIABLE`** : Ce que la règle analyse (URI, paramètres, en-têtes, etc.).
- **`OPERATOR`** : Le test effectué sur la variable (correspondance, comparaison, etc.).
- **`ACTIONS`** : Ce qui doit être fait si la règle est déclenchée.

</div>

---

#### Sécurité et détection d'attaques

##### Le Principe des Règles dans `mod_security`

<div style="font-size:18px">

### **2. Composants en Détail**
#### **2.1 Variables**


| **Variable**              | **Description**                                               |
|---------------------------|---------------------------------------------------------------|
| `ARGS`                    | Paramètres de requête (GET et POST).                         |
| `REQUEST_URI`             | URI de la requête.                                           |
| `REQUEST_HEADERS`         | En-têtes HTTP de la requête.                                 |
| `RESPONSE_BODY`           | Corps de la réponse du serveur.                              |
| `REMOTE_ADDR`             | Adresse IP du client.                                        |
| `FILES`                   | Fichiers téléchargés dans une requête multipart/form-data.   |
| `REQUEST_BODY`            | Corps de la requête (POST).  
</div>

---

#### Sécurité et détection d'attaques

##### Le Principe des Règles dans `mod_security`

<div style="font-size:20px">

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

#### Sécurité et détection d'attaques

##### Le Principe des Règles dans `mod_security`

<div style="font-size:17px">

#### **2.3 Actions**

| **Action**             | **Description**                                                                                   |
|------------------------|---------------------------------------------------------------------------------------------------|
| `deny`                 | Bloque la requête.                                                                               |
| `allow`                | Permet la requête, même si d'autres règles la bloquent.                                          |
| `log`                  | Journalise l'événement dans les logs.                                                            |
| `phase:1` ou `phase:2` | Définit à quel moment la règle est exécutée (avant ou après la réception complète de la requête). |
| `id`                   | Identifiant unique pour la règle.                                                                |
| `status`               | Définit le code de statut HTTP à retourner (ex. 403).                                            |
| `msg`                  | Message descriptif pour les logs.                                                                |

</div>

---


#### Sécurité et détection d'attaques

##### Le Principe des Règles dans `mod_security`

<div style="font-size:22px">

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


</div>

---

#### Sécurité et détection d'attaques

##### Le Principe des Règles dans `mod_security`

<div style="font-size:28px">

### **3. Exemples de Règles**

#### **3.2 Bloquer une Adresse IP Spécifique**
```plaintext
SecRule REMOTE_ADDR "^192\.168\.1\.100$" "id:1002,phase:1,deny,status:403,msg:'Blocked IP Address'"
```
- **Variable** : `REMOTE_ADDR` (adresse IP du client).
- **Opérateur** : Regex pour matcher une IP spécifique.
- **Actions** :
  - `deny` : Bloque les requêtes provenant de cette IP.



</div>

---

#### Sécurité et détection d'attaques

##### Le Principe des Règles dans `mod_security`

<div style="font-size:30px">

### **3. Exemples de Règles**

#### **3.3 Bloquer les Téléchargements de Fichiers PHP**
```plaintext
SecRule FILES_NAMES "\.php$" "id:1003,phase:2,deny,status:403,msg:'Blocked PHP Upload'"
```
- **Variable** : `FILES_NAMES` (noms des fichiers téléchargés).
- **Opérateur** : Regex `\.php$` (fichiers se terminant par `.php`).
- **Actions** :
  - Bloque les téléchargements de fichiers PHP.

</div>

---

#### Sécurité et détection d'attaques

##### Le Principe des Règles dans `mod_security`

<div style="font-size:30px">

### **3. Exemples de Règles**

#### **3.4 Limiter la Taille des Requêtes POST**
```plaintext
SecRequestBodyLimit 102400
SecRule REQUEST_BODY_LENGTH "@gt 102400" "id:1004,phase:2,deny,status:413,msg:'Request Body Too Large'"
```
- **Variable** : `REQUEST_BODY_LENGTH` (taille du corps de la requête).
- **Opérateur** : `@gt` (supérieur à).
- **Action** : Bloque les requêtes dont le corps dépasse 100 Ko.

</div>

---

#### Sécurité et détection d'attaques

##### Le Principe des Règles dans `mod_security`

<div style="font-size:32px">

### **3. Exemples de Règles**

#### **3.5 Bloquer un User-Agent Spécifique**
```plaintext
SecRule REQUEST_HEADERS:User-Agent "curl" "id:1005,phase:1,deny,status:403,msg:'Blocked curl User-Agent'"
```
- **Variable** : `REQUEST_HEADERS:User-Agent` (analyse l'en-tête `User-Agent`).
- **Opérateur** : Regex pour détecter `curl`.
- **Action** : Bloque les requêtes provenant de cet outil.

</div>

---

#### Sécurité et détection d'attaques

##### Le Principe des Règles dans `mod_security`

<div style="font-size:19px">

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

</div>

---

#### Sécurité et détection d'attaques

##### Le Principe des Règles dans `mod_security`

<div style="font-size:30px">

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

</div>

---

#### Sécurité et détection d'attaques

##### Le Principe des Règles dans `mod_security`

<div style="font-size:20px">

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

</div>

---

## Sécurité et détection d'attaques

### Le Principe des Règles dans `mod_security`

<div style="font-size:25px">

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

</div>

---

## Sécurité et détection d'attaques

### Le Principe des Règles dans `mod_security`

<div style="font-size:29px">

<br>

### **Résumé**

- Les **règles de `mod_security`** permettent de protéger les applications web en analysant chaque aspect des requêtes HTTP.
- Elles utilisent des **variables**, des **opérateurs**, et des **actions** pour détecter et bloquer les comportements malveillants.
- Une configuration soigneuse et l’utilisation des **OWASP CRS** offrent une protection robuste contre les attaques courantes.
</div>


---

## Sécurité et détection d'attaques

### Détection et Blocage des Tentatives d'Attaques avec Apache et `mod_security`

<div style="font-size:30px">

<br>

- `mod_security` est un module WAF (**Web Application Firewall**) intégré à Apache qui offre des fonctionnalités puissantes pour **détecter et bloquer les attaques** en analysant le trafic HTTP/HTTPS. 
- En combinant des règles prédéfinies (comme celles de l’OWASP CRS) et des règles personnalisées, `mod_security` protège les applications web contre les attaques courantes.
</div>

---

## Sécurité et détection d'attaques

### Détection et Blocage des Tentatives d'Attaques avec Apache et `mod_security`

<div style="font-size:27px">

### **1. Étapes Clés pour Détecter et Bloquer les Attaques**

1. **Installer et Configurer `mod_security`**.
2. **Activer les Règles OWASP Core Rule Set (CRS)**.
3. **Créer des Règles Personnalisées** pour des besoins spécifiques.
4. **Surveiller les Journaux pour Détecter les Tentatives**.
5. **Optimiser et Affiner les Règles** pour minimiser les faux positifs.

</div>

---

## Sécurité et détection d'attaques

### Détection et Blocage des Tentatives d'Attaques avec Apache et `mod_security`

<div style="font-size:23px">

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
   ``
</div>

---

## Sécurité et détection d'attaques

### Détection et Blocage des Tentatives d'Attaques avec Apache et `mod_security`

<div style="font-size:21px">

### **2. Installer et Configurer `mod_security`**

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

</div>

---
#### Sécurité et détection d'attaques

##### Détection et Blocage des Tentatives d'Attaques avec Apache et `mod_security`

<div style="font-size:15px">

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

</div>

---

### Sécurité et détection d'attaques

#### Détection et Blocage des Tentatives d'Attaques avec Apache et `mod_security`

<div style="font-size:26px">

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
</div>

---

### Sécurité et détection d'attaques

#### Détection et Blocage des Tentatives d'Attaques avec Apache et `mod_security`

<div style="font-size:25px">

### **4. Créer des Règles Personnalisées**

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
</div>

---

### Sécurité et détection d'attaques

#### Détection et Blocage des Tentatives d'Attaques avec Apache et `mod_security`

<div style="font-size:29px">

<br>

### **4. Créer des Règles Personnalisées**

#### **Exemple 5 : Bloquer les Téléchargements Dangereux**
Bloque le téléchargement de fichiers exécutables :
```plaintext
SecRule FILES_NAMES "\.(exe|sh|bat|php)$" "id:1005,phase:2,deny,status:403,msg:'Blocked Dangerous File Upload'"
```
</div>

---

## Sécurité et détection d'attaques

### Détection et Blocage des Tentatives d'Attaques avec Apache et `mod_security`

<div style="font-size:25px">



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

</div>

---

## Sécurité et détection d'attaques

### Détection et Blocage des Tentatives d'Attaques avec Apache et `mod_security`

<div style="font-size:18px">

### **5. Surveiller et Analyser les Journaux**

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


</div>

---

## Sécurité et détection d'attaques

### Détection et Blocage des Tentatives d'Attaques avec Apache et `mod_security`

<div style="font-size:21px">

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


</div>

---

### Sécurité et détection d'attaques

#### Détection et Blocage des Tentatives d'Attaques avec Apache et `mod_security`

<div style="font-size:18px">

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
</div>

---

### Sécurité et détection d'attaques

#### Détection et Blocage des Tentatives d'Attaques avec Apache et `mod_security`

<div style="font-size:18px">

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

</div>

---

#### Sécurité et détection d'attaques

##### Détection et Blocage des Tentatives d'Attaques avec Apache et `mod_security`

<div style="font-size:18px">

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
</div>

---


### Sécurité et détection d'attaques

#### Détection et Blocage des Tentatives d'Attaques avec Apache et `mod_security`

<div style="font-size:30px">

<br>

### **10. Résumé**

Avec `mod_security`, vous pouvez :
- **Détecter et bloquer les attaques courantes** (SQL Injection, XSS, etc.).
- **Créer des règles personnalisées** pour des besoins spécifiques.
- **Surveiller les tentatives d'attaques** via les journaux.
- **Renforcer la sécurité** de vos applications web en combinant
</div>


---

## Sécurité et détection d'attaques

<br>
<center>
<img src="./assets/demo.jpg" width="650px">
</center>


---


<br>
<center>
<img src="./assets/end.jpg" width="800px">
</center>


