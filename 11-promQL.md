# 🧠 Introduction à PromQL

**PromQL** est le langage utilisé par Prometheus pour **interroger, filtrer, agréger et transformer** les séries temporelles de métriques.

Il permet :

* d'afficher les valeurs actuelles,
* de calculer des taux, des moyennes, des percentiles,
* de grouper les métriques par labels,
* de déclencher des alertes (avec Alertmanager),
* d'afficher des graphiques dans Grafana.

---

# 🧱 1. 🧠 Structure de base

```promql
<metric_name>{<label_filters>} <operator> <expression>
```

Exemples :

```promql
http_requests_total
http_requests_total{method="GET", status="200"}
```

---

# 📂 2. 🧾 Types de données

| Type           | Exemple                   | Description                        |
| -------------- | ------------------------- | ---------------------------------- |
| Instant vector | `http_requests_total`     | Valeur actuelle d’une métrique     |
| Range vector   | `http_requests_total[5m]` | Valeurs sur une **plage de temps** |
| Scalar         | `5`, `1.0`                | Un simple nombre                   |
| String         | Rarement utilisé          | Chaîne de texte                    |

---

# 🔧 3. 🔎 Fonctions de base

## 📈 3.1 — `rate()` : taux d’évolution (par seconde)

```promql
rate(http_requests_total[1m])
```

> Nombre moyen de requêtes HTTP par seconde sur les 1 dernières minutes.

---

## 🧮 3.2 — `sum()` : somme

```promql
sum(rate(http_requests_total[5m]))
```

> Total des requêtes HTTP par seconde sur toutes les instances.

---

## 🧾 3.3 — `avg()`, `min()`, `max()`, `count()`

```promql
avg(node_memory_Active_bytes)
```

> Moyenne de la mémoire utilisée sur toutes les machines.

---

## 📊 3.4 — `irate()` : taux instantané

```promql
irate(node_network_receive_bytes_total[1m])
```

> Taux immédiat (plus sensible aux pics que `rate()`).

---

## 🧪 3.5 — `increase()` : variation sur une période

```promql
increase(http_requests_total[1h])
```

> Nombre de requêtes HTTP supplémentaires sur la dernière heure.

---

## 📐 3.6 — `histogram_quantile()`

```promql
histogram_quantile(0.95, sum(rate(http_request_duration_seconds_bucket[5m])) by (le))
```

> Donne le **95e percentile** du temps de réponse HTTP.

---

# 🎯 4. 🎚 Opérateurs PromQL

## 📊 Opérateurs arithmétiques

```promql
node_memory_MemFree_bytes / node_memory_MemTotal_bytes
```

> Pourcentage de mémoire libre.

## 🧱 Opérateurs logiques

```promql
up == 0
```

> Filtre les services "down".

## 🔀 Opérateurs de jointure `on()` et `ignoring()`

```promql
rate(http_requests_total[1m]) / on(instance) rate(http_errors_total[1m])
```

> Taux d’erreur par instance.

---

# 🧩 5. 🔁 Agrégations avec `by()` ou `without()`

## Exemple :

```promql
sum(rate(http_requests_total[5m])) by (job)
```

> Somme du taux de requêtes HTTP groupée **par job**.

---

# 💡 6. ⚠️ Exemples utiles

### 🔹 Services qui ne répondent plus :

```promql
up == 0
```

---

### 🔹 CPU utilisé par core :

```promql
rate(node_cpu_seconds_total{mode="user"}[5m])
```

---

### 🔹 RAM utilisée en pourcentage :

```promql
100 * (1 - (node_memory_MemAvailable_bytes / node_memory_MemTotal_bytes))
```

---

### 🔹 Répartition des requêtes par statut HTTP :

```promql
sum(rate(http_requests_total[5m])) by (status)
```

---

### 🔹 Mémoire utilisée par instance :

```promql
node_memory_Active_bytes{job="node_exporter"}
```

---

### 🔹 Nombre de requêtes dans le dernier quart d’heure :

```promql
increase(http_requests_total[15m])
```

---

# 🧪 Bonus : Tester dans Prometheus

👉 Tu peux tester tes requêtes ici :

* **Interface Web de Prometheus** : `http://<ip_prometheus>:9090`
* Onglet **Graph** ou **Console**
* Également dans **Grafana**, dans les panels de type "Time series"


