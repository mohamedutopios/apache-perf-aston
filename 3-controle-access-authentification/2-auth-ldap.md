### **Authentification LDAP avec `mod_authnz_ldap` dans Apache HTTPD**

Le module **`mod_authnz_ldap`** permet à Apache HTTPD d'authentifier les utilisateurs via un annuaire LDAP (Lightweight Directory Access Protocol). Cela est souvent utilisé pour intégrer un serveur web dans un environnement d'entreprise utilisant des annuaires comme Active Directory ou OpenLDAP.

---

### **1. Prérequis**

1. **Modules Apache nécessaires** :
   - `mod_ldap`
   - `mod_authnz_ldap`

   Vérifiez leur disponibilité et activez-les :
   ```bash
   sudo a2enmod ldap authnz_ldap
   sudo systemctl restart apache2
   ```

2. **Serveur LDAP** :
   - Un serveur LDAP ou Active Directory doit être configuré et accessible.

3. **Informations LDAP nécessaires** :
   - URL du serveur LDAP (ex. : `ldap://ldap.example.com` ou `ldaps://ldap.example.com` pour une connexion sécurisée).
   - Base DN (ex. : `dc=example,dc=com`).
   - DN de liaison pour les recherches LDAP (ex. : `cn=admin,dc=example,dc=com`).
   - Attributs utilisateur (ex. : `uid` ou `sAMAccountName` dans Active Directory).

---

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

---

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

---

### **4. Tester la configuration**

1. **Activer le site et redémarrer Apache**
   - Activez le site (Debian/Ubuntu) :
     ```bash
     sudo a2ensite ldap-auth.conf
     ```
   - Redémarrez Apache :
     ```bash
     sudo systemctl restart apache2
     ```

2. **Accéder au site**
   - Naviguez vers `https://secure.example.com`.
   - Une boîte de dialogue de connexion s'affichera, demandant un nom d'utilisateur et un mot de passe.

3. **Vérifier les journaux**
   - En cas de problème, consultez les logs pour identifier les erreurs :
     ```bash
     tail -f /var/log/apache2/ldap-auth-error.log
     ```

---

### **5. Contrôle d'accès avancé**

#### **Autoriser des utilisateurs spécifiques**
Au lieu d’autoriser tous les utilisateurs (`Require valid-user`), limitez l’accès à certains utilisateurs :
```apache
Require ldap-user john alice
```

#### **Autoriser des groupes LDAP**
Pour restreindre l’accès à des groupes LDAP spécifiques :
```apache
AuthLDAPGroupAttribute member
AuthLDAPGroupAttributeIsDN On
Require ldap-group cn=admins,dc=example,dc=com
```

#### **Combinaison des règles**
Vous pouvez combiner des règles d’accès avec les directives `RequireAll`, `RequireAny`, ou `RequireNone` :
```apache
<RequireAny>
    Require ldap-user john
    Require ldap-group cn=developers,dc=example,dc=com
</RequireAny>
```

---

### **6. Sécurisation supplémentaire**

1. **Utiliser LDAPS (LDAP sécurisé)** :
   - Préférez `ldaps://` pour chiffrer les communications LDAP.
   - Vérifiez que votre serveur LDAP utilise un certificat SSL valide.

2. **Protéger le fichier de configuration**
   - Évitez que le mot de passe de liaison LDAP soit lisible par tous les utilisateurs :
     ```bash
     sudo chmod 640 /etc/apache2/sites-available/ldap-auth.conf
     sudo chown root:www-data /etc/apache2/sites-available/ldap-auth.conf
     ```

---

### **7. Exemple complet**

```apache
<VirtualHost *:443>
    ServerName secure.example.com
    DocumentRoot "/var/www/secure"

    SSLEngine On
    SSLCertificateFile "/etc/ssl/certs/example.com.crt"
    SSLCertificateKeyFile "/etc/ssl/private/example.com.key"

    <Directory "/var/www/secure">
        AuthType Basic
        AuthName "Restricted Area"
        AuthBasicProvider ldap
        AuthLDAPURL "ldaps://ldap.example.com:636/dc=example,dc=com?uid?sub?(objectClass=person)"
        AuthLDAPBindDN "cn=admin,dc=example,dc=com"
        AuthLDAPBindPassword "admin_password"

        # Autoriser uniquement le groupe admins ou les utilisateurs spécifiques
        <RequireAny>
            Require ldap-group cn=admins,dc=example,dc=com
            Require ldap-user john alice
        </RequireAny>
    </Directory>

    ErrorLog ${APACHE_LOG_DIR}/ldap-auth-error.log
    CustomLog ${APACHE_LOG_DIR}/ldap-auth-access.log combined
</VirtualHost>
```

---

### **8. Résumé**

1. **Modules nécessaires** : `mod_ldap` et `mod_authnz_ldap`.
2. **Directives importantes** :
   - `AuthLDAPURL` : Spécifie le serveur et la structure de recherche LDAP.
   - `Require` : Contrôle d’accès basé sur les utilisateurs ou les groupes LDAP.
3. **Bonnes pratiques** :
   - Utilisez LDAPS pour sécuriser les communications.
   - Limitez les droits d'accès aux fichiers contenant des informations sensibles.
4. **Cas d’usage** :
   - Portails internes nécessitant une authentification centralisée.
   - Intégration avec Active Directory ou OpenLDAP.

Avec cette configuration, votre serveur Apache est prêt pour une authentification LDAP robuste et sécurisée.