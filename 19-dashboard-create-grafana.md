Créer son propre **dashboard dans Grafana** est simple et très puissant. Voici un **guide pas-à-pas** pour créer un dashboard à partir de **Prometheus** (mais ça fonctionne aussi avec d’autres sources de données).

---

## 🧭 Étapes pour créer un Dashboard Grafana

### ✅ 1. **Se connecter à Grafana**

* Ouvre Grafana : http\://<ip-de-ton-grafana>:3000
* Connecte-toi (par défaut : `admin` / `admin` puis change le mot de passe).

---

### ✅ 2. **Vérifie ta source de données (Prometheus)**

* Menu latéral → **Gear (⚙️) → Data Sources**
* Clique sur **Prometheus**
* Vérifie que l’URL est bien celle de ton Prometheus (`http://localhost:9090`, par exemple)
* Clique sur **"Save & Test"**

---

### ✅ 3. **Créer un nouveau dashboard**

* Menu latéral → **+ (Create)** → **Dashboard**
* Clique sur **"Add a new panel"**

---

### ✅ 4. **Ajouter un panel avec une requête PromQL**

* Dans l’éditeur de panel :

  * En haut : choisis **Prometheus** comme source de données
  * Saisis une requête PromQL, par exemple :

    ```promql
    node_cpu_seconds_total{mode="user"}
    ```
  * Tu peux utiliser `rate()` ou `avg()` selon ton besoin :

    ```promql
    rate(node_cpu_seconds_total{mode="user"}[5m])
    ```

* Choisis le **type de visualisation** à droite :

  * **Graph** (Time series)
  * **Gauge**
  * **Bar gauge**
  * **Stat**
  * **Table**
  * etc.

---

### ✅ 5. **Configurer le panel**

* **Titre** : Donne un nom au panel.
* **Axes** : Personnalise les axes Y (unités, min/max).
* **Legend** : Active ou désactive la légende.
* **Thresholds** : Ajoute des couleurs selon des seuils.

---

### ✅ 6. **Ajouter plusieurs panels au dashboard**

* Clique sur **“Add panel”** pour chaque nouvelle métrique que tu veux visualiser :

  * CPU usage
  * Memory usage
  * Disk usage
  * Network traffic
  * etc.

---

### ✅ 7. **Sauvegarder ton dashboard**

* En haut : clique sur l’icône 💾 ou **"Save dashboard"**
* Donne-lui un nom (ex : `VM1 Monitoring`) et clique sur **"Save"**

---

## 🎯 Exemples de requêtes PromQL utiles

| Nom               | Requête PromQL                                                                  |
| ----------------- | ------------------------------------------------------------------------------- |
| CPU usage (%)     | `100 - (avg by(instance)(rate(node_cpu_seconds_total{mode="idle"}[5m])) * 100)` |
| RAM usage         | `1 - (node_memory_MemAvailable_bytes / node_memory_MemTotal_bytes)`             |
| Disk used (bytes) | `node_filesystem_size_bytes - node_filesystem_free_bytes`                       |
| RX network        | `rate(node_network_receive_bytes_total[5m])`                                    |
| TX network        | `rate(node_network_transmit_bytes_total[5m])`                                   |

---

## 📦 Bonus : Utiliser un Dashboard communautaire

* Menu → **+ (Import)** → colle un ID depuis [https://grafana.com/grafana/dashboards](https://grafana.com/grafana/dashboards)
* Exemple : `1860` (Node Exporter Full)
* Choisis Prometheus comme source et clique sur **Import**

---

Souhaites-tu que je te propose un modèle de dashboard personnalisé pour tes VM Linux et Windows déjà surveillées ?
