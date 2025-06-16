Voici une vue claire et détaillée des **principaux dossiers et fichiers utilisés par Prometheus**, leur rôle, et leur utilité dans un déploiement typique :

---

## 📁 Structure typique de Prometheus

```bash
/etc/prometheus/
├── prometheus.yml             # ✅ Fichier principal de configuration
├── rules/                     # 📦 Dossier des rules (alerting/recording)
│   ├── alerting_rules.yml     # 🚨 Règles d’alerte
│   └── recording_rules.yml    # 🧮 Règles d’enregistrement
├── file_sd/                   # 📄 Cibles statiques via fichiers JSON/YAML
│   └── targets.json
├── consoles/                  # 🎛 Fichiers pour interface web (console UI)
├── console_libraries/         # 📚 Librairies pour les consoles personnalisées
/var/lib/prometheus/           # 📂 Base de données locale (TSDB)
```

---

## 🗂 Détails des fichiers/dossiers principaux

### 📄 `prometheus.yml` (**Fichier de configuration principal**)

Le **cœur** de la configuration Prometheus.

Contient :

* les **targets** à scraper (via `static_configs`, `file_sd`, etc.),
* la config de **scrape interval**,
* les fichiers de **rules** (`rule_files:`),
* les alertmanagers à contacter.

📌 Exemple :

```yaml
global:
  scrape_interval: 15s

scrape_configs:
  - job_name: 'node_exporter'
    static_configs:
      - targets: ['192.168.1.10:9100']

rule_files:
  - "rules/alerting_rules.yml"
  - "rules/recording_rules.yml"

alerting:
  alertmanagers:
    - static_configs:
        - targets: ['localhost:9093']
```

---

### 📂 `/var/lib/prometheus` (**TSDB : Time Series Database**)

* Contient toutes les **séries temporelles** collectées par Prometheus.
* **⚠️ Ne jamais modifier manuellement !**
* Géré automatiquement avec :

  * fichiers WAL (Write-Ahead Log),
  * blocs de données (2h de données par bloc).

---

### 📁 `rules/` (**Règles d’alerte et d’enregistrement**)

Contient tes fichiers de règles personnalisées :

* `alerting_rules.yml` : contient des règles comme :

```yaml
- alert: InstanceDown
  expr: up == 0
  for: 1m
```

* `recording_rules.yml` : exemple :

```yaml
- record: instance:cpu:avg_rate5m
  expr: avg(rate(node_cpu_seconds_total[5m])) by (instance)
```

---

### 📁 `file_sd/` (**File-based Service Discovery**)

Permet d’ajouter dynamiquement des **cibles de scraping via fichier JSON/YAML**, utile pour les environnements dynamiques.

Exemple : `file_sd/targets.json`

```json
[
  {
    "labels": { "job": "custom" },
    "targets": ["10.0.0.1:9100", "10.0.0.2:9100"]
  }
]
```

Dans `prometheus.yml` :

```yaml
scrape_configs:
  - job_name: 'custom_targets'
    file_sd_configs:
      - files: ["file_sd/targets.json"]
```

---

### 📁 `consoles/` et `console_libraries/` (**Interface web HTML custom**)

* Anciennement utilisés pour afficher des pages HTML dans l’UI web de Prometheus (`http://localhost:9090/consoles`)
* Peu utilisés aujourd’hui (Grafana est préféré)

---

## 🧪 Bonus : autres fichiers/dossiers selon l'installation

| Fichier/Dossier                          | Utilité                                                 |
| ---------------------------------------- | ------------------------------------------------------- |
| `/etc/default/prometheus`                | Variables d’environnement (Debian/Ubuntu)               |
| `/etc/systemd/system/prometheus.service` | Unité systemd pour lancer Prometheus                    |
| `/usr/local/bin/prometheus`              | Binaire Prometheus                                      |
| `/usr/local/bin/promtool`                | Outil de validation de config/règles (`promtool check`) |

---

Souhaites-tu que je te prépare un exemple **complet d’arborescence + contenu de fichiers pour un projet de monitoring réel** (avec node\_exporter, alerting, et Slack) ?
