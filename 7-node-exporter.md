## ✅ Étapes pour installer Node Exporter

### 🔹 1. Connexion SSH à la VM

```bash
ssh utilisateur@ip_de_la_vm
```

---

### 🔹 2. Créer un utilisateur dédié (optionnel mais recommandé)

```bash
sudo useradd --no-create-home --shell /usr/sbin/nologin node_exporter
```

---

### 🔹 3. Télécharger Node Exporter

Vérifie l’architecture (`uname -m`) puis télécharge :

```bash
cd /tmp
wget https://github.com/prometheus/node_exporter/releases/download/v1.9.1/node_exporter-1.9.1.linux-amd64.tar.gz
```

(⚠️ mets à jour `1.8.1` si une nouvelle version est sortie : [https://prometheus.io/download/#node\_exporter](https://prometheus.io/download/#node_exporter))

---

### 🔹 4. Décompression et installation

```bash
tar -xvf node_exporter-1.8.1.linux-amd64.tar.gz
sudo mv node_exporter-1.8.1.linux-amd64/node_exporter /usr/local/bin/
sudo chown node_exporter:node_exporter /usr/local/bin/node_exporter
```

---

### 🔹 5. Créer un service systemd

Crée le fichier `node_exporter.service` :

```bash
sudo nano /etc/systemd/system/node_exporter.service
```

Colle ceci :

```ini
[Unit]
Description=Node Exporter
Wants=network-online.target
After=network-online.target

[Service]
User=node_exporter
Group=node_exporter
Type=simple
ExecStart=/usr/local/bin/node_exporter

[Install]
WantedBy=default.target
```

---

### 🔹 6. Démarrer et activer Node Exporter

```bash
sudo systemctl daemon-reexec
sudo systemctl daemon-reload
sudo systemctl enable node_exporter
sudo systemctl start node_exporter
```

---

### 🔹 7. Vérifier qu’il fonctionne

```bash
systemctl status node_exporter
```

Et depuis un navigateur :

```text
http://IP_VM:9100/metrics
```

---

## 🛡️ Pare-feu : autoriser le port 9100

Si tu utilises `ufw` ou `iptables`, ajoute une règle :

### Avec UFW :

```bash
sudo ufw allow 9100/tcp
```

---

## 🟢 Dans Prometheus

Ajoute ceci à ton `prometheus.yml` :

```yaml
scrape_configs:
  - job_name: 'vm1-node'
    static_configs:
      - targets: ['IP_VM:9100']
```


 ## Puis : 

```bash
sudo systemctl restart prometheus
```

