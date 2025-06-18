### 🔎 Résumé de tes targets

| `job_name`   | Target                 | Exporter utilisé     | Type de métriques disponibles                           |
| ------------ | ---------------------- | -------------------- | ------------------------------------------------------- |
| `prometheus` | `localhost:9090`       | Prometheus lui-même  | Statistiques de Prometheus (scrape, storage, alerting…) |
| `vm1-node`   | `192.168.126.140:9100` | **Node Exporter**    | Métriques système Linux (CPU, RAM, disque, réseau…)     |
| `windows`    | `192.168.126.1:9182`   | **Windows Exporter** | Métriques système Windows (CPU, RAM, disque, process…)  |

---

## ✅ 1. **Prometheus (`job_name: prometheus`)**

Tu peux superviser **Prometheus lui-même** :

### 🔧 Requêtes utiles :

```promql
# Temps pris pour scraper les cibles
rate(scrape_duration_seconds{job="prometheus"}[5m])

# Taux de succès des scrapes
rate(scrape_samples_scraped{job="prometheus"}[5m])

# Taille de la base de données sur disque
prometheus_tsdb_storage_blocks_bytes

# Nombre de séries stockées
prometheus_tsdb_head_series

# Nombre d'alertes actives
ALERTS{job="prometheus"}
```

---

## ✅ 2. **Node Exporter (`job_name: vm1-node`)**

Ce service te donne **les métriques système Linux** classiques.

### 🔧 CPU :

```promql
# Pourcentage CPU par mode (user, system, idle, etc.)
rate(node_cpu_seconds_total{job="vm1-node", mode!="idle"}[5m])
```

### 🔧 RAM :

```promql
# RAM libre en Mo
node_memory_MemAvailable_bytes / 1024 / 1024

# RAM utilisée (en %)
1 - (node_memory_MemAvailable_bytes / node_memory_MemTotal_bytes)
```

### 🔧 Disques :

```promql
# Espace libre par partition
node_filesystem_avail_bytes{fstype!~"tmpfs|overlay"}

# Utilisation disque
100 * (1 - (node_filesystem_free_bytes / node_filesystem_size_bytes))
```

### 🔧 Réseau :

```promql
# Débit entrant
rate(node_network_receive_bytes_total[5m])

# Débit sortant
rate(node_network_transmit_bytes_total[5m])
```

---

## ✅ 3. **Windows Exporter (`job_name: windows`)**

Ce service expose les métriques système **Windows**. Tu as utilisé un label :

```yaml
labels:
  name: windows
```

Ce qui ajoute `name="windows"` dans tes requêtes.

### 🔧 CPU :

```promql
# Pourcentage d'utilisation CPU global
100 - avg by (instance) (rate(windows_cpu_time_total{name="windows", mode="idle"}[5m]) * 100)
```

### 🔧 RAM :

```promql
# RAM disponible
windows_os_physical_memory_free_bytes

# RAM totale
windows_cs_physical_memory_bytes

# RAM utilisée
1 - (windows_os_physical_memory_free_bytes / windows_cs_physical_memory_bytes)
```

### 🔧 Disques :

```promql
# Espace disque disponible
windows_logical_disk_free_bytes{name="windows", volume=~"C:"}

# Utilisation disque (en %)
100 * (1 - (windows_logical_disk_free_bytes / windows_logical_disk_size_bytes))
```

### 🔧 Processus :

```promql
# Nombre de processus
windows_system_processes

# Uptime système
windows_system_system_up_time
```

---

## 🛠️ Astuce : Filtrage par `job` ou `name`

* Pour la VM Linux :

  ```promql
  rate(node_cpu_seconds_total{job="vm1-node"}[5m])
  ```

* Pour la VM Windows :

  ```promql
  rate(windows_cpu_time_total{name="windows"}[5m])
  ```

