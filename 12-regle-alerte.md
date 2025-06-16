### 🚨 Qu’est-ce qu’une **Alerting Rule** dans Prometheus ?

---

## 🧠 Définition

Une **alerting rule** est une règle définie dans Prometheus qui permet de **détecter automatiquement un comportement anormal** (ex : panne, surcharge, erreur) en évaluant une **condition PromQL**.
Lorsqu’elle est vraie pendant un certain temps (`for`), elle **déclenche une alerte**.

---

## 📦 Objectif

✅ Être prévenu **immédiatement** quand un problème survient
✅ Automatiser la **surveillance** des métriques
✅ Intégrer des systèmes de **notification** : email, Slack, MS Teams, PagerDuty, etc. via **Alertmanager**

---

## 🔧 Structure d'une Alerting Rule

```yaml
groups:
  - name: alerting-rules
    rules:
      - alert: NomDeLalerte
        expr: <expression PromQL>
        for: <durée>
        labels:
          severity: <niveau>
        annotations:
          summary: "Message court"
          description: "Détails de l’alerte"
```

---

## 📊 Exemple concret

```yaml
groups:
  - name: cpu-alerts
    rules:
      - alert: HighCPUUsage
        expr: 100 - (avg by(instance) (rate(node_cpu_seconds_total{mode="idle"}[5m])) * 100) > 90
        for: 2m
        labels:
          severity: critical
        annotations:
          summary: "💥 CPU élevé sur {{ $labels.instance }}"
          description: "Le CPU est au-dessus de 90% depuis 2 minutes."
```

### 🔍 Ce que fait cette alerte :

* **Condition** : CPU > 90%
* **Durée** : pendant au moins 2 minutes (`for: 2m`)
* **Gravité** : `critical`
* **Messages** : texte lisible dans Grafana/Alertmanager/Slack

---

## ⚙️ Comment ça fonctionne techniquement

1. **Prometheus** évalue la règle à chaque intervalle (ex : toutes les 15s)
2. Si la condition est vraie pendant toute la durée `for`, une **alerte active** est générée.
3. Elle est **envoyée à Alertmanager**
4. Alertmanager **gère la notification** (destinataire, groupement, silence, inhibition…)

---

## 🧪 Exemple simple d'alerte pour un service inactif

```yaml
- alert: InstanceDown
  expr: up == 0
  for: 1m
  labels:
    severity: warning
  annotations:
    summary: "L’instance {{ $labels.instance }} est hors ligne"
```

---

## 📥 Où les placer ?

Dans un fichier (ex: `alerting_rules.yml`), déclaré dans `prometheus.yml` :

```yaml
rule_files:
  - "rules/alerting_rules.yml"
```

---

## 🆚 Résumé : Alerting Rule vs Recording Rule

| Caractéristique    | Recording Rule                     | Alerting Rule                     |
| ------------------ | ---------------------------------- | --------------------------------- |
| 📌 Rôle            | Pré-calcule une requête PromQL     | Déclenche une alerte              |
| 📦 Résultat        | Nouvelle **métrique**              | **Notification** via Alertmanager |
| 🕒 Évaluation      | Régulière, stockée dans Prometheus | Régulière, mais pas stockée       |
| 🛠 Usage principal | Dashboards, performance            | Supervision, incidents            |


