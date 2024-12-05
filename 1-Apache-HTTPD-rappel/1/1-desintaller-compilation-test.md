Pour désinstaller une installation d'Apache réalisée à partir du code source (via `./configure`, `make`, et `make install`), suivez ces étapes :

---

### **1. Identifier le répertoire d'installation**
- Par défaut, Apache est installé dans **`/usr/local/apache2`** lorsque compilé depuis le code source.
- Si vous avez utilisé une autre option pour `--prefix` lors de la configuration, remplacez `/usr/local/apache2` par votre chemin personnalisé.

---

### **2. Supprimer les fichiers installés**
La désinstallation manuelle consiste à supprimer les fichiers installés lors du processus d'installation. Exécutez la commande suivante pour supprimer le répertoire d'installation :

```bash
sudo rm -rf /usr/local/apache2
```

---

### **3. Supprimer les fichiers temporaires de compilation**
Si le répertoire source de compilation d'Apache (par exemple, `/tmp/httpd-2.4.62`) existe toujours, supprimez-le pour libérer de l'espace disque :

```bash
sudo rm -rf /tmp/httpd-2.4.62
```

---

### **4. Nettoyage des binaires liés**
Si vous avez des fichiers binaires ajoutés à votre système PATH, assurez-vous de les supprimer également. Les binaires installés dans `/usr/local/apache2/bin` sont supprimés en supprimant le répertoire, mais vérifiez s'il y a d'autres configurations ou liens symboliques.

Par exemple :
```bash
sudo rm -f /usr/local/bin/apachectl
sudo rm -f /usr/local/bin/httpd
```

---

### **5. Supprimer les dépendances associées (optionnel)**
Si Apache a été compilé avec APR ou APR-Util dans des emplacements spécifiques, vous pouvez également les désinstaller si vous n'en avez plus besoin :

1. Supprimez APR :
   ```bash
   sudo rm -rf /usr/local/apr
   ```

2. Supprimez les bibliothèques associées (par exemple, PCRE, OpenSSL) uniquement si elles ne sont pas utilisées par d'autres applications.

---

### **6. Nettoyer les fichiers de configuration système**
Vérifiez si des fichiers de configuration personnalisés ont été ajoutés (par exemple, dans `/etc/`) et supprimez-les manuellement.

---

### **7. Vérifier l’absence d’Apache**
Pour confirmer que tout a été correctement désinstallé, exécutez les commandes suivantes pour vérifier qu'Apache n'est plus accessible :

```bash
which httpd
```

```bash
/usr/local/apache2/bin/httpd -v
```

Si aucun chemin ou version n’est affiché, l’installation a été correctement supprimée.

---

### **Conseil : Utiliser `make uninstall` (si disponible)**
Certaines applications compilées depuis le code source fournissent une cible `make uninstall` pour supprimer les fichiers installés. Si le fichier `Makefile` d'Apache prend en charge cette option (peu courant avec Apache), exécutez la commande suivante dans le répertoire source :

```bash
sudo make uninstall
```

Cela supprimera automatiquement les fichiers installés.

---

### **Résumé**
1. Supprimez le répertoire d’installation principal (`/usr/local/apache2` par défaut).
2. Supprimez les fichiers temporaires ou binaires additionnels.
3. Nettoyez les dépendances associées (APR, OpenSSL, PCRE) si elles ne sont plus nécessaires.
4. Vérifiez qu’Apache est complètement désinstallé en vérifiant les binaires ou fichiers de configuration restants.

Avec ces étapes, Apache sera complètement désinstallé de votre système.