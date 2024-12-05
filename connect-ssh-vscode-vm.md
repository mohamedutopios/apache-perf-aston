Voici les étapes complètes pour connecter Visual Studio Code (VSCode) en SSH à une VM sur VirtualBox avec l'utilisateur `root` :

---

### **1. Préparer la VM sur VirtualBox**
#### **1.1 Activer la connexion réseau**
1. **Configurer le réseau VirtualBox :**
   - Ouvrez les paramètres de votre VM dans VirtualBox.
   - Allez dans l'onglet **Réseau** :
     - Activez un adaptateur réseau.
     - Sélectionnez **Accès par pont (Bridge)** pour une connexion réseau externe, ou utilisez **Réseau NAT** avec une règle de redirection de port pour le port SSH.

#### **1.2 Configurer une redirection de port (si NAT est utilisé)**
1. Allez dans **Paramètres > Réseau > Avancé > Redirection de port**.
2. Ajoutez une nouvelle règle :
   - **Nom :** SSH
   - **Protocole :** TCP
   - **Port hôte :** 2222 (ou un autre port non utilisé)
   - **Port invité :** 22 (port SSH par défaut de la VM)

---

### **2. Installer et configurer le serveur SSH sur la VM**
#### **2.1 Installer OpenSSH**
Connectez-vous à la VM et installez OpenSSH Server si ce n'est pas encore fait :

```bash
sudo apt update
sudo apt install openssh-server
```

#### **2.2 Vérifier le statut du service SSH**
Assurez-vous que le service SSH est actif :

```bash
sudo systemctl status ssh
```

Si le service n'est pas actif, démarrez-le :

```bash
sudo systemctl start ssh
sudo systemctl enable ssh
```

#### **2.3 Activer l'accès SSH pour `root`**
1. Éditez le fichier de configuration SSH :
   ```bash
   sudo nano /etc/ssh/sshd_config
   ```

2. Trouvez la ligne contenant `PermitRootLogin` et modifiez-la pour autoriser `root` :
   ```plaintext
   PermitRootLogin yes
   ```

3. Sauvegardez et quittez (CTRL+O, Enter, puis CTRL+X).

4. Redémarrez le service SSH pour appliquer les modifications :
   ```bash
   sudo systemctl restart ssh
   ```

#### **2.4 Définir un mot de passe pour `root`**
Si ce n'est pas déjà fait, attribuez un mot de passe à l'utilisateur `root` :

```bash
sudo passwd root
```

---

### **3. Configurer Visual Studio Code**
#### **3.1 Installer l'extension Remote - SSH**
1. Ouvrez VSCode.
2. Allez dans l'onglet **Extensions** (icône sur la barre latérale ou `CTRL+SHIFT+X`).
3. Recherchez **Remote - SSH** et installez cette extension.

#### **3.2 Ajouter la configuration SSH**
1. Ouvrez la palette de commande (`CTRL+SHIFT+P` sur Windows/Linux ou `CMD+SHIFT+P` sur macOS).
2. Tapez **Remote-SSH: Open SSH Configuration File** et sélectionnez l'option.
3. Choisissez le fichier SSH à modifier (généralement `~/.ssh/config`).
4. Ajoutez une nouvelle entrée pour votre VM :

   Si vous utilisez **Bridge** :
   ```plaintext
   Host my-vm
       HostName <IP_ADDRESS_OF_VM>
       User root
       Port 22
   ```

   Si vous utilisez **NAT avec redirection de port** :
   ```plaintext
   Host my-vm
       HostName 127.0.0.1
       User root
       Port 2222
   ```

   - **Host :** Un alias pour votre connexion (vous pouvez le nommer comme vous voulez).
   - **HostName :** L'adresse IP de la VM (ou `127.0.0.1` pour une redirection NAT).
   - **User :** L'utilisateur SSH (`root` ici).
   - **Port :** Le port SSH (par défaut `22` ou celui configuré dans la redirection NAT).

5. Sauvegardez le fichier.

---

### **4. Connecter VSCode à la VM**
1. Dans VSCode, ouvrez la palette de commande (`CTRL+SHIFT+P`).
2. Tapez **Remote-SSH: Connect to Host** et sélectionnez l'alias défini précédemment (`my-vm`).
3. Saisissez le mot de passe root lorsque cela est demandé.
4. Une nouvelle fenêtre VSCode s’ouvrira, connectée à votre VM.

---

### **5. Vérifier la connexion et explorer**
1. Dans la fenêtre VSCode connectée, ouvrez un terminal intégré (`CTRL+~`).
2. Vous devriez voir l'invite de commande de votre VM (`root@hostname:~#`).

---

### **6. Sécuriser la connexion (optionnel mais recommandé)**
1. **Désactiver l'accès root après configuration :**
   Une fois la configuration terminée, vous pouvez désactiver `PermitRootLogin` pour renforcer la sécurité.
   ```bash
   sudo nano /etc/ssh/sshd_config
   ```
   Remplacez `PermitRootLogin yes` par `PermitRootLogin no`, puis redémarrez SSH :
   ```bash
   sudo systemctl restart ssh
   ```

2. **Configurer une authentification par clé SSH :**
   - Générer une paire de clés SSH sur votre machine hôte.
   - Ajouter la clé publique au fichier `~/.ssh/authorized_keys` sur la VM.

---

Avec ces étapes, vous serez connecté à votre VM en tant que `root` via VSCode et SSH. N'oubliez pas d'appliquer les bonnes pratiques de sécurité dans un environnement de production.