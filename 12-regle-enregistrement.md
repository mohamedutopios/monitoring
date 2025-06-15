### 🎯 Qu’est-ce qu’une **recording rule** dans Prometheus ?

---

## 🧠 Définition

Une **recording rule** est une règle dans Prometheus qui permet de **pré-calculer** une requête PromQL et d’enregistrer son résultat **sous une nouvelle métrique**.

> Cela évite de recalculer des requêtes complexes à chaque fois, ce qui **améliore les performances**.

---

## 📦 À quoi ça sert concrètement ?

✅ **Optimiser les performances** :
Les requêtes complexes (avec `rate()`, `sum()`, `avg()`, etc.) sont calculées à l’avance.

✅ **Simplifier l’écriture** :
Tu peux utiliser une **métrique lisible** au lieu d’une requête longue dans Grafana ou les alertes.

✅ **Préparer des métriques sur mesure** :
Créer des métriques plus proches du métier (ex : `cpu_utilisation_pct`, `web_errors_rate`).

---

## 🧾 Exemple concret

### Requête complexe :

```promql
sum(rate(http_requests_total[5m])) by (job)
```

### En recording rule :

```yaml
groups:
  - name: recording-rules
    rules:
      - record: job:http_requests_rate_5m
        expr: sum(rate(http_requests_total[5m])) by (job)
```

> Résultat : Prometheus stocke le résultat sous une **nouvelle métrique** appelée `job:http_requests_rate_5m`.

Tu peux ensuite l’utiliser comme une métrique native :

```promql
job:http_requests_rate_5m{job="web"}
```

---

## ⚙️ Où l’écrire ?

Dans un fichier `rules.yml` (ou autre nom), que tu ajoutes dans `prometheus.yml` :

```yaml
rule_files:
  - "rules/recording_rules.yml"
```

---

## 🔁 Quand est-elle évaluée ?

Les recording rules sont **réévaluées automatiquement à chaque intervalle** d’évaluation (souvent 15s ou 30s selon la config de Prometheus).

---

## 🆚 Différence avec une alerting rule ?

| Recording Rule                         | Alerting Rule                                   |
| -------------------------------------- | ----------------------------------------------- |
| Pré-calcule une requête PromQL         | Déclenche une alerte si une condition est vraie |
| Stocke le résultat comme une métrique  | Envoie une notification (via Alertmanager)      |
| Optimise les dashboards et les alertes | Sert à la supervision directe                   |

---

Souhaites-tu un exemple pratique de **recording rule avec le CPU**, ou un exemple de projet complet avec dashboards et alertes ?
