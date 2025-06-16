Excellente question 👌 ! Les préfixes comme `node_`, `http_`, `rpc_` dans les noms de métriques Prometheus **indiquent la provenance** ou le **contexte fonctionnel** de la métrique.

---

## 🧠 Règle générale

```text
<contexte>_<type de donnée>_<unité éventuelle>
```

Cela suit la convention [Prometheus Naming Best Practices](https://prometheus.io/docs/practices/naming/).

---

## 🔹 Signification des préfixes courants

| Préfixe      | Signification                         | Source habituelle           |
| ------------ | ------------------------------------- | --------------------------- |
| `node_`      | 🔧 Données système (machine, OS)      | `node_exporter`             |
| `http_`      | 🌐 Requêtes HTTP (serveur ou client)  | Applications instrumentées  |
| `rpc_`       | 🔄 Appels RPC (Remote Procedure Call) | Services distribués / gRPC  |
| `process_`   | ⚙️ Processus local supervisé          | Prometheus lui-même ou apps |
| `go_`        | 🐹 Statistiques sur la VM Go          | App écrite en Go            |
| `kube_`      | ☸️ État de Kubernetes (Pods, etc.)    | `kube-state-metrics`        |
| `container_` | 🐳 Statistiques conteneurs            | cAdvisor / kubelet          |
| `nginx_`     | 🌐 Métriques NGINX                    | `nginx_exporter`            |
| `mysql_`     | 🗄️ Métriques MySQL                   | `mysqld_exporter`           |
| `redis_`     | 🧠 Métriques Redis                    | `redis_exporter`            |

---

## 🔍 Exemples concrets

### 🔧 `node_` (machine physique ou VM)

```text
node_cpu_seconds_total{mode="idle"}
node_memory_Active_bytes
node_network_receive_bytes_total
```

📌 Signifie : CPU, mémoire ou réseau **de la machine**.

---

### 🌐 `http_` (trafic HTTP)

```text
http_requests_total{method="GET"}
http_request_duration_seconds_bucket{le="0.5"}
```

📌 Requêtes HTTP traitées, avec détails sur la méthode et les temps de réponse.

---

### 🔄 `rpc_` (appels distants)

```text
rpc_duration_seconds{quantile="0.99"}
rpc_calls_total
```

📌 Appels RPC effectués par ou vers un service distribué (ex : gRPC).

---

### ⚙️ `process_` (processus Prometheus ou application)

```text
process_cpu_seconds_total
process_resident_memory_bytes
```

📌 Utilisation CPU et mémoire **du processus lui-même**.

---

### 🐹 `go_` (runtime Go)

```text
go_goroutines
go_memstats_alloc_bytes
```

📌 Informations sur la mémoire et les goroutines **du runtime Go**.

---

## 🔁 Astuce Prometheus

Tu peux voir tous les noms de métriques d’un exporteur ou d’un job avec :

```promql
label_values(__name__)
```

Ou via l’API :

```
http://localhost:9090/api/v1/label/__name__/values
```

---

## 🧠 Conseil de nommage personnel

Quand tu instrumentes tes propres applications :

* Utilise un **préfixe clair** lié à ton domaine métier :

  * `webshop_orders_total`
  * `auth_failed_logins_total`
  * `billing_invoice_duration_seconds`

---

Souhaites-tu une **fiche de correspondance complète** par exporteur avec les préfixes et leurs métriques typiques ?
