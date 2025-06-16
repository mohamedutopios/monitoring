Voici un **guide clair et complet** pour connecter **Grafana à Prometheus**.

---

## ✅ Prérequis

* ✅ **Grafana** est installé et accessible sur `http://<ip>:3000`
* ✅ **Prometheus** est en cours d’exécution (par défaut sur `http://localhost:9090`)
* 🚨 Les deux machines doivent pouvoir communiquer (vérifie firewall et réseau)

---

## 🧭 Étapes pour connecter Prometheus à Grafana

### 1. 🔐 Connecte-toi à Grafana

Va sur :

```
http://<IP-de-ta-machine>:3000
```

Connecte-toi avec :

* **Login** : `admin`
* **Mot de passe** : `admin` (puis tu devras le changer)

---

### 2. ➕ Ajouter Prometheus comme **source de données**

#### A. Dans Grafana :

* Clique sur le **rouage ⚙️** (menu de gauche)
* Clique sur **"Data sources"**
* Clique sur **"Add data source"**
* Choisis **"Prometheus"**

#### B. Configure Prometheus :

* Dans le champ **URL**, mets l'adresse de ton serveur Prometheus :

  ```
  http://localhost:9090
  ```

  (ou autre IP : `http://192.168.56.101:9090` par exemple)

* Laisse les autres options par défaut (ou adapte `Access` à `Server` ou `Browser`)

* Clique sur **"Save & Test"**

✅ Tu dois voir un message vert : `Data source is working`

---

## ✅ Vérifier que ça fonctionne

Tu peux maintenant :

* Aller dans le menu **"Dashboards"** > **"New"** > **"New Dashboard"**
* Ajouter un **Panel**
* Mettre une requête PromQL simple comme :

```promql
node_cpu_seconds_total
```

Ou plus utile :

```promql
rate(node_cpu_seconds_total{mode!="idle"}[5m])
```

---

## 📦 Exemple de métriques utiles (pour tester) :

```promql
node_memory_MemAvailable_bytes
rate(node_network_receive_bytes_total[1m])
node_filesystem_avail_bytes
```

---

Souhaites-tu ensuite :

* Un **dashboard clé-en-main pour Grafana** (CPU, RAM, disque) ?
* Ajouter **Alertmanager** dans Grafana ?
* Visualiser les **alertes Prometheus dans Grafana** ?
