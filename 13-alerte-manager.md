Très bien ! Voici un **tutoriel complet et fonctionnel** pour installer **Alertmanager** avec **Prometheus**, et envoyer des **notifications email via Gmail**.

---

## 🧰 Prérequis

* Prometheus déjà installé sur `/etc/prometheus/`
* Ports ouverts (9093 pour Alertmanager)
* Un compte Gmail avec mot de passe d'application (important !)

---

## ✅ Étape 1 — Installer Alertmanager

```bash
cd /opt/
wget https://github.com/prometheus/alertmanager/releases/latest/download/alertmanager-*-linux-amd64.tar.gz
tar -xzf alertmanager-*-linux-amd64.tar.gz
cd alertmanager-*-linux-amd64
sudo cp alertmanager amtool /usr/local/bin/
sudo mkdir -p /etc/alertmanager /var/lib/alertmanager
sudo cp alertmanager.yml /etc/alertmanager/
```

---

## ✅ Étape 2 — Créer le fichier de configuration `/etc/alertmanager/alertmanager.yml`

```yaml
global:
  smtp_smarthost: 'smtp.gmail.com:587'
  smtp_from: 'tonemail@gmail.com'
  smtp_auth_username: 'tonemail@gmail.com'
  smtp_auth_password: 'mot_de_passe_application'
  smtp_require_tls: true

route:
  receiver: 'gmail-notifier'

receivers:
  - name: 'gmail-notifier'
    email_configs:
      - to: 'destinataire@gmail.com'
        send_resolved: true
```

> 🟠 Remplace `tonemail@gmail.com` et `mot_de_passe_application` par ceux de ton compte Gmail
> 🔐 Le mot de passe doit être un **mot de passe d'application** (généré dans ton compte Google si 2FA activé)

---

## ✅ Étape 3 — Créer le service systemd d’Alertmanager

Fichier `/etc/systemd/system/alertmanager.service` :

```ini
[Unit]
Description=Alertmanager
After=network.target

[Service]
User=root
ExecStart=/usr/local/bin/alertmanager \
  --config.file=/etc/alertmanager/alertmanager.yml \
  --storage.path=/var/lib/alertmanager
Restart=always

[Install]
WantedBy=multi-user.target
```

Puis :

```bash
sudo systemctl daemon-reexec
sudo systemctl daemon-reload
sudo systemctl enable --now alertmanager
```

---

## ✅ Étape 4 — Modifier la config de Prometheus

Dans `/etc/prometheus/prometheus.yml`, ajoute :

```yaml
alerting:
  alertmanagers:
    - static_configs:
        - targets: ['localhost:9093']

rule_files:
  - "alerte_rules.yml"
```

---

## ✅ Étape 5 — Créer une règle d’alerte (`/etc/prometheus/alerte_rules.yml`)

```yaml
groups:
  - name: alert_test
    rules:
      - alert: InstanceDown
        expr: up == 0
        for: 1m
        labels:
          severity: critical
        annotations:
          summary: "Instance {{ $labels.instance }} down"
          description: "{{ $labels.instance }} ne répond plus."
```

---

## ✅ Étape 6 — Redémarrer Prometheus

```bash
sudo systemctl restart prometheus
```

---

## ✅ Étape 7 — Tester

* Va sur : `http://localhost:9093` → Alertmanager
* Va sur : `http://localhost:9090` → Prometheus
* Tape `ALERTS` dans "Graph" pour voir les alertes actives
* Arrête un service surveillé pour tester une vraie alerte

---

## 🔐 Mot de passe d'application Gmail (obligatoire)

> Si ton compte Gmail a l’authentification à 2 facteurs :

1. Va dans **[https://myaccount.google.com/security](https://myaccount.google.com/security)**
2. Active la **vérification en deux étapes**
3. Puis clique sur **"Mots de passe d’application"**
4. Génére un mot de passe (par exemple pour "Autre > Alertmanager")
5. Utilise ce mot dans `smtp_auth_password`

---

Souhaites-tu :

* Un script `bash` d'installation complet ?
* Ajouter Slack, Telegram ou Discord comme canaux aussi ?
