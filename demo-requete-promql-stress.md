Parfait, tu as déjà une **excellente série de requêtes PromQL** ! Maintenant, pour **voir évoluer ces métriques en temps réel**, tu peux déclencher des **stress tests** ciblés sur ta ou tes machines surveillées (Linux ou Windows) pour **générer de la charge CPU, mémoire, disque ou réseau**.

Voici **comment simuler ces charges**, avec des outils simples à installer.

---

## 🔧 Sur une machine Linux (ex: `vm1:9100` avec Node Exporter)

### ✅ 1. Stress CPU

```bash
sudo apt install -y stress
# Charge 2 cœurs pendant 2 minutes
stress --cpu 2 --timeout 120
```

Ou :

```bash
yes > /dev/null &
yes > /dev/null &
```

### ✅ 2. Stress Mémoire

```bash
# Alloue 1 Go de RAM pendant 2 minutes
stress --vm 1 --vm-bytes 1G --timeout 120
```

Ou :

```bash
dd if=/dev/zero of=/dev/null bs=1M count=1024
```

### ✅ 3. Stress I/O (disque)

```bash
stress --hdd 1 --timeout 120
```

Ou avec `dd` :

```bash
dd if=/dev/zero of=testfile bs=100M count=10 oflag=direct
```

### ✅ 4. Stress Réseau (recevoir des requêtes)

Sur la **machine cible** (à superviser) :

```bash
python3 -m http.server 8000
```

Sur une **autre machine** :

```bash
curl http://<ip-cible>:8000/ > /dev/null &
curl http://<ip-cible>:8000/ > /dev/null &
curl http://<ip-cible>:8000/ > /dev/null &
```

---

## 🪟 Sur une machine Windows (avec `windows_exporter`)

### ✅ 1. Stress CPU

Ouvre PowerShell ou CMD :

```powershell
for ($i=0; $i -lt 4; $i++) { Start-Job { while ($true) {} } }
```

(ça utilise 4 threads CPU, interrompt avec `Get-Job | Remove-Job`)

### ✅ 2. Stress RAM

```powershell
$a = @()
1..1000000 | ForEach-Object { $a += "A very large string $_" }
```

### ✅ 3. I/O Disque

```powershell
for ($i = 0; $i -lt 5; $i++) {
    Start-Job { Get-Content C:\Windows\System32\config\SOFTWARE -ReadCount 0 }
}
```

### ✅ 4. Uplink HTTP (réseau)

Sur Windows :

```powershell
Invoke-WebRequest http://<ip-linux>:8000 -OutFile NUL
```

---

## 🎯 Ensuite : observe tes métriques avec PromQL

Exemples **pendant le stress** :

### CPU usage

```promql
avg(rate(node_cpu_seconds_total{instance="vm1:9100", mode!="idle"}[1m])) by (instance)
```

### RAM usage

```promql
100 * (1 - (node_memory_MemAvailable_bytes{instance="vm1:9100"} / node_memory_MemTotal_bytes{instance="vm1:9100"}))
```

### Disque :

```promql
(node_filesystem_size_bytes - node_filesystem_free_bytes) / node_filesystem_size_bytes
```

### Réseau :

```promql
rate(node_network_receive_bytes_total{instance="vm1:9100"}[1m])
rate(node_network_transmit_bytes_total{instance="vm1:9100"}[1m])
```

---

## 📈 Bonus : créer une alerte si CPU > 80%

```yaml
groups:
  - name: cpu_alerts
    rules:
      - alert: HighCpuUsage
        expr: avg(rate(node_cpu_seconds_total{mode="user", instance="vm1:9100"}[2m])) > 0.8
        for: 1m
        labels:
          severity: warning
        annotations:
          summary: "CPU élevé sur {{ $labels.instance }}"
          description: "La machine {{ $labels.instance }} a un CPU user > 80% pendant plus d'1 minute."
```

---

Souhaites-tu un script shell ou PowerShell automatisé qui déclenche plusieurs charges en série pour tester tous les cas d’usage ?
