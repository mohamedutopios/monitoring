Voici une **série complète de règles d'alerte Prometheus (Alerting Rules)** avec des exemples pour :

* 🔧 *détection CPU/RAM/disque réseau*
* 📦 *format Prometheus*
* 🧪 *comment les déclencher avec des stress tests (`stress`, `dd`, `curl`, etc.)*

---

## ✅ 1. Exemple de fichier `alerting_rules.yml`

```yaml
groups:
  - name: alert.rules
    interval: 30s
    rules:
      - alert: HighCPUUsage
        expr: avg(rate(node_cpu_seconds_total{mode!="idle"}[5m])) by (instance) > 0.9
        for: 2m
        labels:
          severity: warning
        annotations:
          summary: "CPU élevé sur {{ $labels.instance }}"
          description: "L'utilisation CPU dépasse 90% pendant plus de 2 minutes."

      - alert: HighMemoryUsage
        expr: (node_memory_MemTotal_bytes - node_memory_MemAvailable_bytes) / node_memory_MemTotal_bytes > 0.9
        for: 2m
        labels:
          severity: critical
        annotations:
          summary: "Mémoire presque saturée sur {{ $labels.instance }}"
          description: "Plus de 90% de la mémoire est utilisée."

      - alert: HighDiskUsage
        expr: (node_filesystem_size_bytes{fstype!="tmpfs", mountpoint!~"/(boot|run)"} - node_filesystem_free_bytes{fstype!="tmpfs", mountpoint!~"/(boot|run)"}) / node_filesystem_size_bytes{fstype!="tmpfs", mountpoint!~"/(boot|run)"} > 0.9
        for: 2m
        labels:
          severity: warning
        annotations:
          summary: "Disque presque plein sur {{ $labels.instance }}"
          description: "Plus de 90% de l’espace disque est utilisé."

      - alert: HighLoadAverage
        expr: node_load1 > count(count(node_cpu_seconds_total{mode="idle"}) by (cpu)) * 1.5
        for: 2m
        labels:
          severity: warning
        annotations:
          summary: "Load average élevé sur {{ $labels.instance }}"
          description: "La charge système dépasse le nombre de CPU disponibles."

      - alert: HighNetworkReceive
        expr: rate(node_network_receive_bytes_total[5m]) > 10000000
        for: 2m
        labels:
          severity: info
        annotations:
          summary: "Fort trafic entrant sur {{ $labels.instance }}"
          description: "Trafic réseau entrant supérieur à 10 Mo/s."
```

---

## 📁 Référence dans `prometheus.yml`

```yaml
rule_files:
  - "alerting_rules.yml"
```

---

## 🧪 2. Comment déclencher manuellement ces alertes ?

### 🚨 A. **High CPU Usage**

```bash
sudo apt install -y stress
stress --cpu 2 --timeout 180
```

* Cela utilise 2 CPU pendant 3 minutes → déclenche `HighCPUUsage`.

---

### 🚨 B. **High Memory Usage**

```bash
stress --vm 2 --vm-bytes 90% --timeout 180
```

* Alloue 90% de la RAM avec 2 processus → déclenche `HighMemoryUsage`.

---

### 🚨 C. **High Disk Usage**

```bash
mkdir -p /tmp/filldisk && dd if=/dev/zero of=/tmp/filldisk/fill bs=1M count=8000
```

* Remplit `/tmp` avec 8 Go de données → déclenche `HighDiskUsage`.

🧼 Nettoyage :

```bash
rm -rf /tmp/filldisk
```

---

### 🚨 D. **High Load Average**

```bash
yes > /dev/null &
yes > /dev/null &
yes > /dev/null &
```

* Simule une forte charge CPU → vérifie `node_load1`.

🧼 Nettoyage :

```bash
killall yes
```

---

### 🚨 E. **High Network Traffic**

## 🔥 Méthodes puissantes pour générer du trafic réseau

### ✅ **1. `iperf3` – Le plus précis pour simuler des débits**

#### Installation sur les 2 machines :

```bash
sudo apt install iperf3
```

#### Sur la **machine cible** (serveur) :

```bash
iperf3 -s
```

#### Sur la **machine attaquante** (client) :

```bash
iperf3 -c <IP cible> -t 120 -P 4
```

* `-t 120` : durée 2 minutes.
* `-P 4` : 4 connexions parallèles.

> 👉 Cela simule un débit **soutenu**, en TCP (ou UDP), parfait pour déclencher `HighNetworkReceive`.

---

### ✅ **2. `wget` ou `ab` (ApacheBench) – Téléchargement massif**

#### Serveur :

```bash
python3 -m http.server 8000
```

#### Client :

```bash
ab -n 100000 -c 50 http://<IP>:8000/
```

* `-n 100000` : nombre total de requêtes
* `-c 50` : 50 connexions simultanées

> Résultat : saturation rapide de la bande passante entrante + montée du nombre de connexions réseau.

---

### ✅ **3. `hping3` – Simuler du trafic TCP/UDP brut**

```bash
sudo apt install hping3
```

```bash
sudo hping3 -S -p 80 --flood <IP cible>
```

* `-S` : envoie des paquets SYN (TCP)
* `--flood` : très rapide (peut surcharger)

⚠️ **Attention** : très agressif, peut être détecté comme attaque DoS par des outils de sécurité.

---

### ✅ **4. `tcpreplay` – Relecture de fichiers PCAP**

Si tu as un fichier `.pcap` :

```bash
sudo tcpreplay --intf1=eth0 file.pcap
```

> Simule un vrai trafic enregistré (HTTP, FTP, DNS…).

---

### ✅ **5. `iperf3` en mode UDP (encore plus réaliste)**

```bash
iperf3 -c <IP cible> -t 120 -u -b 100M
```

* `-u` : mode UDP
* `-b 100M` : 100 Mbits/s simulés

---

## 🔍 Vérification dans Prometheus

Tu peux observer en PromQL :

```promql
rate(node_network_receive_bytes_total[1m]) by (instance)
rate(node_network_transmit_bytes_total[1m]) by (instance)
```

---

## 📦 Bonus : Règle d'alerte plus adaptée

```yaml
- alert: NetworkReceiveTooHigh
  expr: rate(node_network_receive_bytes_total[1m]) by (instance) > 20000000
  for: 1m
  labels:
    severity: warning
  annotations:
    summary: "Trafic réseau élevé sur {{ $labels.instance }}"
    description: "Plus de 20 MB/s reçus pendant plus d'une minute."
```

---

Souhaites-tu que je te génère un **script bash complet de test réseau + déclenchement Prometheus + vérification dans Grafana** ?


* Cela va générer un débit réseau → peut déclencher `HighNetworkReceive`.

---

## 🔔 3. Affichage dans Prometheus

Tu peux voir les alertes sur `http://<prometheus>:9090/alerts`

---

## 📦 Bonus : Intégration avec Alertmanager

Dans `prometheus.yml` :

```yaml
alerting:
  alertmanagers:
    - static_configs:
        - targets:
            - "localhost:9093"
```

Et configure ensuite Alertmanager pour Slack, mail ou autre.

