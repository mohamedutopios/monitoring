## 🔹 1. **Requêtes simples de métriques**

```promql
# CPU utilisé sur toutes les machines
rate(node_cpu_seconds_total{mode!="idle"}[5m])
```

```promql
# Mémoire libre
node_memory_MemFree_bytes
```

```promql
# Espace disque utilisé
node_filesystem_size_bytes - node_filesystem_free_bytes
```

---

## 🔹 2. **Moyennes, sommes et agrégats**

```promql
# Moyenne CPU par instance (machine)
avg(rate(node_cpu_seconds_total{mode="user"}[5m])) by (instance)
```

```promql
# Total RAM utilisée (en bytes)
sum(node_memory_MemTotal_bytes - node_memory_MemAvailable_bytes)
```

---

## 🔹 3. **Filtrage sur des labels**

Tu peux filtrer sur un label `instance` pour **cibler une machine** :

```promql
# CPU idle pour la machine "vm1:9100"
rate(node_cpu_seconds_total{instance="vm1:9100", mode="idle"}[5m])
```

---

## 🔹 4. **Calculs avec expressions**

```promql
# Pourcentage de RAM utilisée
100 * (1 - (node_memory_MemAvailable_bytes / node_memory_MemTotal_bytes))
```

```promql
# Taux de requêtes HTTP 5xx
rate(http_requests_total{status=~"5.."}[5m])
```

---

## 🔹 5. **Utilisation disque par partition**

```promql
# Espace utilisé pour chaque partition
(node_filesystem_size_bytes - node_filesystem_free_bytes) / node_filesystem_size_bytes
```

---

## 🔹 6. **Débit réseau**

```promql
# Total entrant sur interface eth0
rate(node_network_receive_bytes_total{device="eth0"}[1m])
```

```promql
# Total sortant
rate(node_network_transmit_bytes_total{device="eth0"}[1m])
```

---

## 🔹 7. **Uptime de la machine**

```promql
# Depuis combien de temps la machine est up (en secondes)
(time() - node_boot_time_seconds)
```

---

## 🔹 8. **Alertes potentielles (seuils)**

```promql
# CPU user time > 80% sur une machine
avg(rate(node_cpu_seconds_total{mode="user", instance="vm1:9100"}[5m])) > 0.8
```

```promql
# RAM disponible < 10%
(node_memory_MemAvailable_bytes / node_memory_MemTotal_bytes) < 0.1
```

---

## 🔹 9. **Exemple complet avec groupement et instance**

```promql
# Moyenne CPU total (hors idle) par machine
avg(rate(node_cpu_seconds_total{mode!="idle"}[5m])) by (instance)
```

```promql
# RAM utilisée pour une machine donnée
(node_memory_MemTotal_bytes{instance="vm1:9100"} - node_memory_MemAvailable_bytes{instance="vm1:9100"})
```

---

## ✅ **Comment filtrer une machine en particulier**

Utilise le label `instance="nom_machine:port"` :

```promql
metric_name{instance="vm1:9100"}
```

Tu peux aussi filtrer sur des labels comme :

* `job="node"`
* `datacenter="paris"`
* `env="prod"`

Exemple :

```promql
rate(node_cpu_seconds_total{job="node", instance="vm-dc1-prod-01:9100", mode="user"}[5m])
```


