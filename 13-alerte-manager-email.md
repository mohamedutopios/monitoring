## ✅ Où exécuter ProtonMail Bridge ?

Tu dois **installer et exécuter ProtonMail Bridge sur la même machine** où **Alertmanager est installé**, **car le Bridge expose un serveur SMTP local (`127.0.0.1:1025`)** **non accessible depuis l'extérieur**.

---

### 🔸 Donc :

* Si **Alertmanager tourne sur ta VM Prometheus Linux**, alors :
  👉 **installe et exécute ProtonMail Bridge sur cette même VM.**

---

## 🧱 Architecture logique

```
[Alertmanager] --> SMTP local (127.0.0.1:1025) --> ProtonMail Bridge --> ProtonMail Serveurs
```

---

## ⚠️ Important :

* ProtonMail Bridge **nécessite une interface graphique** pour te connecter au compte la première fois.

  * Sur un **poste desktop (Linux/Mac/Windows)** : OK.
  * Sur un **serveur headless (sans GUI)** : il faut la version **CLI (ProtonMail Bridge CLI)**.
    👉 C’est ce que tu dois utiliser si ta **VM Prometheus est en ligne de commande uniquement**.

---

## ✅ Étapes si ta VM Prometheus est sans interface graphique :

### 🔧 1. Installer la version CLI de ProtonMail Bridge

```bash
wget https://mohamed-formation.s3.eu-west-3.amazonaws.com/protonmail-bridge_3.20.0-1_amd64.deb
sudo dpkg -i protonmail-bridge_3.20.0-1_amd64.deb
sudo apt install -f  # corrige les dépendances
```

### 🔐 2. Lancer la configuration initiale (mode CLI)

```bash
protonmail-bridge --cli
```

Tu auras une invite comme :

```bash
Bridge CLI started
> login
Email: ton_mail@protonmail.com
Password: ********
```

Puis, après `login`, tape :

```bash
> info
```

Tu verras les infos suivantes :

* SMTP username
* SMTP password
* Local SMTP port

---

### 🛠️ 3. Reprendre ces infos dans `alertmanager.yml`

```yaml
smtp_smarthost: '127.0.0.1:1025'
smtp_auth_username: 'ton_mail@protonmail.com'
smtp_auth_password: 'mot_de_passe_donné_par_bridge'
smtp_require_tls: false
```

---

### 📌 Résumé

| Cas                         | Solution                          |
| --------------------------- | --------------------------------- |
| VM avec interface graphique | Installe Bridge GUI               |
| VM sans interface graphique | Utilise `protonmail-bridge --cli` |

---

Souhaites-tu que je t’aide à automatiser l’installation du Bridge CLI sur ta VM Prometheus (script Bash) ?


- /etc/alertmanager/alertmanager.yml
```yaml
global:
  smtp_smarthost: '127.0.0.1:1025'
  smtp_from: 'aijjou@pm.me'
  smtp_auth_username: 'aijjou@pm.me'
  smtp_auth_password: 'DVVpcjQZsRM1NBRUY7HAqw'
  smtp_require_tls: true

route:
  group_by: ['alertname']
  group_wait: 10s
  group_interval: 30s
  repeat_interval: 2h
  receiver: 'default-receiver'
  routes:
    - receiver: 'slack-notifications'
      match:
        severity: test
    - receiver: 'mail-proton'
      match:
        severity: info

receivers:
  - name: 'slack-notifications'
    slack_configs:
      - api_url: 'https://hooks.slack.com/services/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'
        channel: '#tous-formation'
        send_resolved: true
        text: "{{ range .Alerts }}🔔 *{{ .Labels.alertname }}* - {{ .Annotations.summary }}\n{{ end }}"

  - name: 'mail-proton'
    email_configs:
      - to: 'mohamed@utopios.net'
        send_resolved: true
        headers:
          subject: '[ALERTE] {{ .CommonLabels.alertname }}'
        html: |
          <b>Résumé :</b> {{ .CommonAnnotations.summary }}<br>
          <b>Description :</b> {{ .CommonAnnotations.description }}

  - name: 'default-receiver'
    email_configs:
      - to: 'mohamed@utopios.net'
```

- /etc/prometheus/prometheus.yml


```yaml
groups:
  - name: cpu-alerts
    rules:
      - alert: HighAverageCPU
        expr: 100 - (avg by(instance) (rate(node_cpu_seconds_total{mode="idle"}[1m])) * 100) > 80
        for: 10s
        labels:
          severity: info
        annotations:
          summary: "💥 CPU élevé sur {{ $labels.instance }}"
          description: "La moyenne de l'utilisation CPU est > 80% depuis 2 minutes (calculée sur les 5 dernières minutes)."
```


sudo systemctl restart alertmanager
sudo systemctl restart prometheus


Sur l'autre machine :

- apt install stress
- stress --cpu $(nproc) --timeout 300
