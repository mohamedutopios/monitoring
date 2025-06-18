> 🧩 **Windows Exporter** (anciennement `wmi_exporter`)

---

## ✅ Étapes pour exporter les métriques depuis une VM Windows

---

### 1. 📥 Télécharger Windows Exporter

Va sur la page officielle :

👉 [https://github.com/prometheus-community/windows\_exporter/releases](https://github.com/prometheus-community/windows_exporter/releases)

Télécharge le dernier `.msi`, par exemple :

```
windows_exporter-0.25.1-amd64.msi
```

---

### 2. 💾 Installer Windows Exporter

1. Double-clique sur le `.msi` téléchargé.
2. Tu peux sélectionner les **collectors** que tu veux (ex: cpu, memory, disk, net, etc.)
3. Finalise l’installation.
   Cela installe un service Windows nommé `windows_exporter`.

Par défaut, le service expose les métriques sur :

```
http://localhost:9182/metrics
```

---

### 3. 🔥 Autoriser le port 9182 dans le pare-feu Windows

Dans PowerShell admin :

```powershell
New-NetFirewallRule -DisplayName "Windows Exporter" -Direction Inbound -Protocol TCP -LocalPort 9182 -Action Allow
```

---

### 4. 🧠 Vérifier que ça fonctionne

Dans ton navigateur sur ta VM Windows :

```
http://localhost:9182/metrics
```

Tu dois voir un texte brut avec des lignes comme :

```
# HELP ...
# TYPE ...
windows_cpu_time_total{cpu="0",mode="idle"} 123456
...
```

---

### 5. 📡 Ajouter cette VM dans Prometheus

Sur ta machine Prometheus (ex: Ubuntu), édite `prometheus.yml` :

```yaml
scrape_configs:
  - job_name: 'windows-vm'
    static_configs:
      - targets: ['IP-WINDOWS:9182']
```

Par exemple :

```yaml
      - targets: ['192.168.56.101:9182']
```

Puis redémarre Prometheus :

```bash
sudo systemctl restart prometheus
```

---

### 6. ✅ Vérifier dans Prometheus

Va sur Prometheus (`http://<IP>:9090`) > "Status" > "Targets"

Tu dois voir :

```
windows-vm   |  UP   |  http://192.168.56.101:9182/metrics
```

---

## 📈 Exemples de métriques disponibles

Tu pourras ensuite interroger dans Prometheus ou Grafana :

```promql
windows_cpu_time_total{mode="user"}
windows_os_physical_memory_free_bytes
windows_logical_disk_free_bytes
windows_net_bytes_received_total
```

---

## 🎁 Bonus

Tu peux aussi installer Windows Exporter avec plus d’options :

```powershell
.\windows_exporter.exe --collectors.enabled "cpu,cs,logical_disk,net,os,system"
```


