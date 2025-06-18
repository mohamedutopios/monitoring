# 🚀 Mise en place d'Alertmanager avec Slack

---

## ✅ 1. **Créer un Webhook Slack**

1. Va sur [https://api.slack.com/apps](https://api.slack.com/apps)
2. Clique sur **"Create New App" → From Scratch**
3. Donne un nom à ton app (ex: `Alertmanager`)
4. Choisis ton **workspace Slack**
5. Dans la gauche, clique sur **“Incoming Webhooks”**
6. Active **“Incoming Webhooks”**
7. Clique sur **“Add New Webhook to Workspace”**
8. Choisis un **canal Slack** (ex : `#monitoring`, `#tous-formation`)
9. Clique sur **“Allow”**
10. Copie l’**URL Webhook** générée :

```
https://hooks.slack.com/services/Txxxx/Bxxxx/xxxxxxxxxxxx
```

---

## ✅ 2. Créer le fichier `alertmanager.yml`

📄 Chemin : `/etc/alertmanager/alertmanager.yml`

```yaml
global:
  resolve_timeout: 5m

route:
  receiver: 'slack-notifications'

receivers:
  - name: 'slack-notifications'
    slack_configs:
      - api_url: 'https://hooks.slack.com/services/Txxxx/Bxxxx/xxxxxxxxxxxx'
        channel: '#tous-formation'  # ou ton canal
        send_resolved: true
        text: "{{ range .Alerts }}🔔 *{{ .Labels.alertname }}* — {{ .Annotations.summary }}\n{{ end }}"
```

---

## ✅ 3. Créer une règle d’alerte dans Prometheus

📄 Chemin : `/etc/prometheus/rules/test-alert-rule.yml`

```yaml
groups:
  - name: test-alert-group
    rules:
      - alert: SlackTestAlert
        expr: vector(1)
        for: 10s
        labels:
          severity: test
        annotations:
          summary: "✅ Alerte de test envoyée vers Slack"
          description: "Ceci est une alerte de test générée manuellement pour valider l’intégration Alertmanager + Slack"
```

---

## ✅ 4. Modifier le fichier `prometheus.yml`

📄 Chemin : `/etc/prometheus/prometheus.yml`

```yaml
global:
  scrape_interval: 15s

rule_files:
  - "/etc/prometheus/rules/*.yml"

alerting:
  alertmanagers:
    - static_configs:
        - targets: ['localhost:9093']
```

---

## ✅ 5. Redémarrer les services

```bash
sudo systemctl restart alertmanager
sudo systemctl restart prometheus
```

---

## ✅ 6. Vérifications

### 🔎 Alerte dans Prometheus

→ Accède à `http://localhost:9090/alerts`
Tu dois voir `SlackTestAlert` en **firing** ✅

### 🔎 Alerte dans Alertmanager

→ Accède à `http://localhost:9093`
Tu dois voir ton alerte en **firing**

### 🔔 Alerte dans Slack

→ Ton canal `#tous-formation` reçoit :

```
🔔 SlackTestAlert — ✅ Alerte de test envoyée vers Slack
```

---

## 🛠️ Bonus : pour tester manuellement le webhook

```bash
curl -X POST -H 'Content-type: application/json' \
  --data '{"text":"Test manuel depuis curl"}' \
  https://hooks.slack.com/services/Txxxx/Bxxxx/xxxxxxxxxxxx
```


