1. ✅ La **VM Prometheus** (celle qui collecte les métriques)
2. ✅ Les **VMs exportant les métriques** (avec `node_exporter` ou `windows_exporter`)

---

# 🧠 Objectif global

| VM                           | Rôle                        | Ports à ouvrir                                  |
| ---------------------------- | --------------------------- | ----------------------------------------------- |
| `monitoring-vm`              | Contient Prometheus + NGINX | `22` (SSH), `8080` (accès Prometheus via NGINX) |
| `linux-vm-01`, `linux-vm-02` | Contient `node_exporter`    | `9100` (pour Prometheus uniquement)             |
| `windows-vm-01`              | Contient `windows_exporter` | `9182` (pour Prometheus uniquement)             |

---

## 🛠️ 1. RÈGLES DE PARE-FEU POUR LA VM PROMETHEUS

### 🎯 But : autoriser uniquement l’accès SSH + accès à Prometheus via reverse proxy (`:8080`) + laisser les connexions sortantes vers `node_exporter`.

### 🔧 Commandes UFW :


```bash
# installation si necessaire : 
sudo apt install ufw -y

# Autoriser SSH
sudo ufw allow ssh

# Autoriser l’accès à Prometheus via NGINX (port 8080)
sudo ufw allow 8080/tcp

# Tout interdire en entrée sauf ce qui est autorisé
sudo ufw default deny incoming

# Autoriser toutes les connexions sortantes (vers exporters notamment)
sudo ufw default allow outgoing

# Activer le pare-feu
sudo ufw enable
```

✅ Tu peux te connecter à la VM, consulter Prometheus via navigateur, et Prometheus peut scrapper d’autres VMs.

---

## 🛠️ 2. RÈGLES DE PARE-FEU POUR LES VM LINUX AVEC `node_exporter`

### 🎯 But : autoriser **uniquement Prometheus** à se connecter au port 9100 + SSH local.

### 🔧 Commandes UFW :

```bash
# Autoriser SSH (pour gestion)
sudo ufw allow ssh

# Autoriser Prometheus uniquement à accéder à node_exporter
sudo ufw allow from 192.168.1.100 to any port 9100 proto tcp

# Refuser tout autre trafic entrant
sudo ufw default deny incoming

# Autoriser les connexions sortantes
sudo ufw default allow outgoing

# Activer le pare-feu
sudo ufw enable
```

> Remplace `192.168.1.100` par **l’adresse IP de la VM Prometheus**.

---

## 🛠️ 3. VM WINDOWS AVEC `windows_exporter` (port 9182)

* Ouvre le **Pare-feu Windows** :

  * Panneau de configuration > Système et sécurité > Pare-feu Windows Defender > Règles de trafic entrant
* Ajoute une **nouvelle règle** :

  * **Type** : Port
  * **Protocole** : TCP
  * **Port** : `9182`
  * **Action** : Autoriser la connexion
  * **Portée** : Limiter à l’IP de Prometheus (optionnel mais recommandé)
  * **Nom** : `Prometheus - Windows Exporter`

---

## ✅ Test final depuis la VM Prometheus

```bash
curl http://192.168.1.101:9100/metrics   # Linux
curl http://192.168.1.103:9182/metrics   # Windows
```

Tu dois recevoir un grand bloc de texte commençant par :

```
# HELP go_gc_duration_seconds A summary of the pause duration...
```

