## 🧠 Définition

> Une **métrique** dans Prometheus est une **valeur numérique collectée à un instant donné**, associée à un **nom** et à des **étiquettes (labels)**. Elle représente un **comportement observable du système**, par exemple :

* le nombre de requêtes HTTP,
* l’usage du CPU,
* l’espace disque utilisé,
* le nombre de connexions réseau.

Chaque métrique est une **série temporelle**, c’est-à-dire une suite de valeurs associées à des instants précis.

---

## 📦 Structure d’une métrique

Une métrique Prometheus a cette forme :

```text
<nom_de_la_métrique>{label1="valeur1", label2="valeur2"} valeur @timestamp
```

🔍 Exemple :

```text
http_requests_total{method="GET", status="200", instance="192.168.1.10:8080"} 12345  @1686723600
```

* `http_requests_total` : nom de la métrique
* `{...}` : labels qui ajoutent du contexte
* `12345` : valeur observée
* `@1686723600` : horodatage (timestamp)

---

## 🎯 À quoi servent les métriques ?

* 📊 Créer des **graphes** dans Grafana
* ⚠ Déclencher des **alertes** (via Alertmanager)
* 🧪 Faire de l’analyse de performance
* 📉 Observer le comportement d’un service dans le temps

---

## 📚 Types de métriques

| Type        | Description                               | Exemple typique                        |
| ----------- | ----------------------------------------- | -------------------------------------- |
| `Counter`   | Cumul qui **augmente seulement**          | `http_requests_total`                  |
| `Gauge`     | Valeur qui peut **monter et descendre**   | `memory_usage_bytes`, `cpu_temp`       |
| `Histogram` | Découpe des valeurs en **intervalles**    | `http_request_duration_seconds_bucket` |
| `Summary`   | Calcule des **quantiles** + count + somme | `rpc_duration_seconds`                 |

---

## 🧪 Exemple réel (avec `node_exporter`)

```text
node_cpu_seconds_total{cpu="0", mode="user", instance="vm1:9100"} 12483.56
```

> Cela signifie que le **core 0** de la VM `vm1` a passé **12483.56 secondes en mode utilisateur**.

---

## 🔁 Comment sont collectées les métriques ?

1. Prometheus **scrape** (interroge) ses cibles à intervalles réguliers (ex : toutes les 15s).
2. Chaque cible (exporter ou app instrumentée) expose des métriques sur un endpoint `/metrics`.
3. Prometheus stocke les valeurs et les étiquettes dans sa base de données en séries temporelles.

