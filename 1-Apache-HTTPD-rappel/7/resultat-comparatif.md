Analysons les données des résultats des tests avec **`wrk`** pour **HTTP/2** et **HTTP/1.1**. Voici une décomposition des données affichées, leur signification, et une comparaison des performances entre les deux.

---

### **Décryptage des données pour HTTP/2 et HTTP/1.1**

#### **1. Informations générales sur le test :**
- **12 threads et 100 connexions :**
  - Le test a utilisé 12 threads de traitement avec 100 connexions ouvertes simultanément.
- **Durée de 30 secondes (`-d30s`) :**
  - Le test a été exécuté sur une durée de 30 secondes.

---

#### **2. Statistiques des threads :**
Les données sous "Thread Stats" résument les performances observées pour chaque thread.

- **`Latency` (en millisecondes) :**
  - **HTTP/2** : `358.10ms (Stdev: 431.74ms)`
  - **HTTP/1.1** : `339.83ms (Stdev: 442.72ms)`
  - **Latence moyenne** : Temps moyen pour qu'une requête soit envoyée, traitée, et que la réponse soit reçue.
    - Une latence légèrement plus faible pour HTTP/1.1 peut être due au fait qu'HTTP/2 gère plus de connexions simultanément, augmentant temporairement la latence.

- **`Max` :**
  - **HTTP/2** : `2.00s` (latence maximale observée).
  - **HTTP/1.1** : `2.00s` (latence maximale observée).
  - Ce résultat est identique pour les deux tests.

- **`+/- Stdev` :**
  - 84% des latences observées sont en dessous de cette plage pour les deux protocoles, montrant une certaine régularité.

---

#### **3. Distribution de la latence :**
- Cette section montre la répartition des temps de latence en pourcentage :
  - **50% (médiane)** :
    - **HTTP/2** : `163.87ms`
    - **HTTP/1.1** : `139.65ms`
    - La médiane est plus basse pour HTTP/1.1, mais cela ne reflète pas le débit global.

  - **75%** :
    - **HTTP/2** : `407.90ms`
    - **HTTP/1.1** : `343.65ms`
  
  - **90%** :
    - **HTTP/2** : `1.08s`
    - **HTTP/1.1** : `1.08s`

  - **99%** :
    - **HTTP/2** : `1.80s`
    - **HTTP/1.1** : `1.84s`
    - Les extrêmes sont légèrement mieux gérés par HTTP/2.

---

#### **4. Nombre total de requêtes :**
- **HTTP/2** : `9541 requests`
- **HTTP/1.1** : `7778 requests`
  - HTTP/2 gère plus de requêtes dans le même laps de temps, grâce au multiplexage.

---

#### **5. Volume de données transférées :**
- **HTTP/2** : `9.33GB`
- **HTTP/1.1** : `7.61GB`
  - HTTP/2 transfère environ 22% de données supplémentaires dans le même laps de temps.

---

#### **6. Erreurs sur les sockets :**
- **HTTP/2** : `connect 0, read 63, write 0, timeout 162`
- **HTTP/1.1** : `connect 0, read 59, write 0, timeout 250`
  - HTTP/1.1 présente plus de timeouts, indiquant que les connexions sont plus souvent saturées.

---

#### **7. Requêtes par seconde (`Requests/sec`) :**
- **HTTP/2** : `317.02`
- **HTTP/1.1** : `258.39`
  - HTTP/2 gère environ 23% plus de requêtes par seconde que HTTP/1.1.

---

#### **8. Transfert par seconde (`Transfer/sec`) :**
- **HTTP/2** : `317.45MB/sec`
- **HTTP/1.1** : `259.01MB/sec`
  - Le débit pour HTTP/2 est également environ 23% plus élevé.

---

### **Comparaison entre HTTP/2 et HTTP/1.1**

| Critère                   | HTTP/2             | HTTP/1.1           | Amélioration |
|---------------------------|--------------------|--------------------|--------------|
| Latence moyenne           | 358.10ms          | 339.83ms          | -            |
| Médiane latence (50%)     | 163.87ms          | 139.65ms          | -            |
| Requêtes totales          | 9541              | 7778              | +22.7%       |
| Données transférées       | 9.33GB            | 7.61GB            | +22.6%       |
| Requêtes par seconde      | 317.02            | 258.39            | +22.6%       |
| Débit par seconde         | 317.45MB/sec      | 259.01MB/sec      | +22.6%       |
| Timeouts                  | 162               | 250               | HTTP/2 meilleur |

---

### **Conclusion**
HTTP/2 offre des performances nettement supérieures à HTTP/1.1 dans ce test :
- **Plus de requêtes et un débit plus élevé** : Le multiplexage permet à HTTP/2 de gérer plusieurs requêtes simultanément sur une même connexion, réduisant le temps global de transfert.
- **Moins de timeouts** : HTTP/2 est plus stable et résilient sous forte charge.

Si vous souhaitez d'autres tests ou des analyses plus détaillées, faites-le-moi savoir ! 😊