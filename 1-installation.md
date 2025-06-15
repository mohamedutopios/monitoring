Voici un guide **complet, propre et structuré** pour **installer Prometheus** sur une VM Linux (Ubuntu ou Debian), avec les **bonnes pratiques** en production légère (service, utilisateur dédié, permissions, etc.).

---

# 📦 Installer Prometheus sur une VM Linux

### ✅ Compatible : Ubuntu 22.04+, Debian 11+, Rocky/Alma/RHEL (adaptable)

---

## 🧭 Étapes recommandées (méthode propre et manuelle)

### 1. ✅ **Créer un utilisateur système dédié**

```bash
sudo useradd --no-create-home --shell /usr/sbin/nologin prometheus
```

---

### 2. ✅ **Créer les répertoires nécessaires**

```bash
sudo mkdir /etc/prometheus
sudo mkdir /var/lib/prometheus
```

---

### 3. 📥 **Télécharger la dernière version de Prometheus**

Vérifie ici : [https://prometheus.io/download/](https://prometheus.io/download/)

Puis :

```bash
cd /tmp
wget https://github.com/prometheus/prometheus/releases/latest/download/prometheus-2.52.0.linux-amd64.tar.gz
tar xvf prometheus-*.tar.gz
cd prometheus-*
```

---

### 4. 🚀 **Déplacer les binaires**

```bash
sudo cp prometheus /usr/local/bin/
sudo cp promtool /usr/local/bin/
```

---

### 5. 📂 **Déplacer les fichiers de configuration**

```bash
sudo cp -r consoles /etc/prometheus
sudo cp -r console_libraries /etc/prometheus
sudo cp prometheus.yml /etc/prometheus
```

---

### 6. 🔐 **Donner les bonnes permissions**

```bash
sudo chown -R prometheus:prometheus /etc/prometheus
sudo chown -R prometheus:prometheus /var/lib/prometheus
sudo chown prometheus:prometheus /usr/local/bin/prometheus
sudo chown prometheus:prometheus /usr/local/bin/promtool
```

---

### 7. ⚙️ **Créer le service systemd**

Crée un fichier :

```bash
sudo nano /etc/systemd/system/prometheus.service
```

Contenu :

```ini
[Unit]
Description=Prometheus Monitoring
Wants=network-online.target
After=network-online.target

[Service]
User=prometheus
Group=prometheus
Type=simple
ExecStart=/usr/local/bin/prometheus \
  --config.file=/etc/prometheus/prometheus.yml \
  --storage.tsdb.path=/var/lib/prometheus/ \
  --web.console.templates=/etc/prometheus/consoles \
  --web.console.libraries=/etc/prometheus/console_libraries

[Install]
WantedBy=multi-user.target
```

---

### 8. 🔄 **Démarrer et activer Prometheus**

```bash
sudo systemctl daemon-reexec
sudo systemctl daemon-reload
sudo systemctl start prometheus
sudo systemctl enable prometheus
```

---

### 9. 🌐 **Accéder à Prometheus**

* Ouvre ton navigateur :

  ```
  http://[IP_VM]:9090
  ```

---

## 🧠 Bonnes pratiques de production (en version légère)

| Bonne pratique                                      | Pourquoi                                 |
| --------------------------------------------------- | ---------------------------------------- |
| ✅ Utilisateur non-root (`prometheus`)               | Sécurité renforcée                       |
| ✅ Service systemd                                   | Démarrage automatique, gestion fiable    |
| ✅ Répertoires propres (`/etc`, `/var/lib`)          | Structure claire pour sauvegarde         |
| ❌ Éviter installation via APT                       | Version souvent trop ancienne            |
| ✅ Téléchargement depuis GitHub officiel             | Version stable et à jour                 |
| ✅ Ne jamais lancer en root avec `./prometheus` seul | Risque de mauvaise permission + sécurité |

