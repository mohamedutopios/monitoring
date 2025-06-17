Voici une **sélection des meilleurs dashboards Grafana** pour une supervision complète avec **Prometheus**, classés par usage (système, réseau, services, etc.), avec leurs **ID d'importation Grafana.com**.

---

## 🧰 DASHBOARDS INDISPENSABLES POUR PROMETHEUS

### 🔹 1. **Node Exporter Full**

> Pour superviser Linux avec `node_exporter`

* **ID Grafana** : `1860`
* 🔍 Affiche : CPU, RAM, disques, réseau, charge système, uptime
* 📎 Lien : [https://grafana.com/grafana/dashboards/1860/](https://grafana.com/grafana/dashboards/1860/)

---

### 🔹 2. **Windows Node Exporter**

> Pour superviser Windows avec `windows_exporter`

* **ID Grafana** : `21240`
* 🔍 Affiche : CPU, mémoire, swap, disques, services, uptime
* 📎 Lien : [https://grafana.com/grafana/dashboards/21240/](https://grafana.com/grafana/dashboards/21240/)

---

### 🔹 3. **Prometheus Stats**

> Pour superviser **Prometheus lui-même**

* **ID Grafana** : `3662`
* 🔍 Affiche : durée des scrapes, erreurs, nombre de séries, capacité disque, etc.
* 📎 Lien : [https://grafana.com/grafana/dashboards/3662/](https://grafana.com/grafana/dashboards/3662/)

---

### 🔹 4. **Node Exporter for Host Metrics**

> Dashboard officiel Grafana Labs

* **ID Grafana** : `11074`
* 🔍 CPU, RAM, swap, filesystems, IO, réseau — design moderne et clair
* 📎 Lien : [https://grafana.com/grafana/dashboards/11074/](https://grafana.com/grafana/dashboards/11074/)

---

## 🖥️ DASHBOARDS PAR TYPE DE MÉTRIQUE

### 💾 5. **Disk Stats**

* **ID Grafana** : `13105`
* 🔍 Utilisation par point de montage, lecture/écriture par device
* Idéal pour suivre la santé des disques.

---

### 🌐 6. **Network Overview**

* **ID Grafana** : `10000`
* 🔍 RX/TX par interface, erreurs, drops, débit max, etc.

---

### 🧠 7. **Memory Usage Overview**

* **ID Grafana** : `12708`
* 🔍 RAM totale, libre, cache, buffers, swap…

---

### 🔌 8. **Powerful Process Explorer**

* **ID Grafana** : `6671`
* 🔍 Liste des processus en cours, consommation mémoire/CPU par processus (nécessite un exporter adapté)

---

## ⚠️ ALERTES & RESILIENCE

### 🚨 9. **Alertmanager Dashboard**

* **ID Grafana** : `9578`
* 🔍 Affiche les alertes actives, leur état, leur fréquence, les routes

---

## ⚙️ AUTRES DASHBOARDS UTILES

### ☁️ 10. **Blackbox Exporter Dashboard**

* **ID Grafana** : `7587`
* Pour surveiller des **cibles externes HTTP, ICMP, DNS** (nécessite blackbox\_exporter)

### 🔐 11. **SSL Certificate Expiry Dashboard**

* **ID Grafana** : `12174`
* 🔍 Affiche les dates d’expiration des certificats HTTPS (via blackbox ou ssl exporter)

---

## 📥 Comment les importer dans Grafana ?

1. Dans Grafana : menu gauche → **+ (Create)** → **Import**
2. Colle l'**ID** du dashboard (ex : `1860`)
3. Sélectionne ta **source Prometheus**
4. Clique sur **Import**

---

Souhaites-tu que je te crée un **dashboard combiné** avec les métriques essentielles CPU, RAM, disque, uptime et alertes, pour plusieurs VM ?
