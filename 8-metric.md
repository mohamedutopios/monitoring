## 🧠 Concepts clés des métriques dans Prometheus

1. **Métrique**

   * Une **valeur mesurable** à un instant donné, comme `CPU usage`, `nombre de requêtes`, `espace disque`, etc.

2. **Séries temporelle**

   * Chaque métrique est enregistrée **au fil du temps**, ce qui permet de faire des **graphes** et **des alertes**.
   * Chaque série est identifiée par :

     * **Nom de la métrique**
     * **Labels** (étiquettes) : pour ajouter des dimensions (`instance`, `job`, etc.)

3. **Scraping**

   * Prometheus **interroge régulièrement** ses cibles (exporters ou applications instrumentées) pour récupérer les valeurs.

4. **Labels**

   * Permettent de **différencier plusieurs instances** d'une même métrique.
   * Exemple :
     `http_requests_total{method="POST", status="500", instance="192.168.1.1:8080"}`

5. **PromQL**

   * Le langage de requêtage de Prometheus, pour interroger et manipuler les métriques.

---

## 📊 Types de métriques (selon Prometheus)

| Type          | Description                                                           | Exemple                                  |
| ------------- | --------------------------------------------------------------------- | ---------------------------------------- |
| **Counter**   | Compte qui **n’augmente que** (jamais ne diminue).                    | `http_requests_total`                    |
| **Gauge**     | Valeur pouvant **monter ou descendre**.                               | `memory_usage_bytes`, `cpu_temp_celsius` |
| **Histogram** | Comptabilise les **valeurs dans des intervalles (buckets)**.          | `http_request_duration_seconds_bucket`   |
| **Summary**   | Fournit des **quantiles** (moyenne, 95e percentile) + total et count. | `rpc_duration_seconds`                   |

---

## 🛠 Exemples concrets

### ✅ 1. Counter

```prometheus
http_requests_total{method="GET", status="200"}  15423
```

> 🔹 Utilisé pour compter les requêtes HTTP traitées.

---

### ✅ 2. Gauge

```prometheus
node_memory_Active_bytes{instance="server1:9100"}  2048000000
```

> 🔹 Utilisé pour afficher la mémoire utilisée actuellement.

---

### ✅ 3. Histogram

```prometheus
http_request_duration_seconds_bucket{le="0.5"}  1123
http_request_duration_seconds_bucket{le="1.0"}  1534
http_request_duration_seconds_sum  345.98
http_request_duration_seconds_count  2345
```

> 🔹 Montre combien de requêtes ont duré moins de 0.5s, 1s, etc.

---

### ✅ 4. Summary

```prometheus
rpc_duration_seconds{quantile="0.5"} 0.05
rpc_duration_seconds{quantile="0.9"} 0.1
rpc_duration_seconds{quantile="0.99"} 0.15
rpc_duration_seconds_sum  15.2
rpc_duration_seconds_count  310
```

> 🔹 Mesure les durées et donne des quantiles pour les temps de réponse.

---

## 🔎 Exemple PromQL

1. **Nombre de requêtes HTTP en 5 minutes :**

```promql
sum(rate(http_requests_total[5m]))
```

2. **Utilisation mémoire :**

```promql
node_memory_Active_bytes / node_memory_MemTotal_bytes
```

3. **CPU par core :**

```promql
rate(node_cpu_seconds_total{mode="user"}[1m])
```

---

## 🧪 Sources de métriques (exporters)

* `node_exporter` → pour Linux (CPU, mémoire, disques…)
* `blackbox_exporter` → pour faire du ping/HTTP/tcp.
* `vmware_exporter`, `mysqld_exporter`, `nginx_exporter`, etc.
* Ou directement depuis votre **application instrumentée** avec des bibliothèques Prometheus (Go, Java, Python…).


