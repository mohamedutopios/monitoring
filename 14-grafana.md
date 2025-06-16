Voici un **guide complet et à jour pour installer Grafana** sur une machine **Ubuntu** (fonctionne aussi sur Debian). On part d’une machine fraîche.

---

## ✅ 1. Mise à jour de la machine

```bash
sudo apt update && sudo apt upgrade -y
```

---

## ✅ 2. Installer les dépendances (si curl ou gnupg ne sont pas installés)

```bash
sudo apt install -y apt-transport-https software-properties-common curl gnupg2
```

---

## ✅ 3. Ajouter le dépôt officiel Grafana

```bash
sudo mkdir -p /etc/apt/keyrings
curl -fsSL https://apt.grafana.com/gpg.key | sudo gpg --dearmor -o /etc/apt/keyrings/grafana.gpg
```

Ensuite ajoute le dépôt stable :

```bash
echo "deb [signed-by=/etc/apt/keyrings/grafana.gpg] https://apt.grafana.com stable main" | sudo tee /etc/apt/sources.list.d/grafana.list
```

---

## ✅ 4. Installer Grafana OSS

```bash
sudo apt update
sudo apt install grafana -y
```

---

## ✅ 5. Activer et démarrer le service Grafana

```bash
sudo systemctl enable grafana-server
sudo systemctl start grafana-server
```

Vérifie qu’il fonctionne :

```bash
sudo systemctl status grafana-server
```

---

## ✅ 6. Accéder à Grafana

Par défaut, Grafana est accessible sur le port **3000** :

👉 Ouvre ton navigateur et va à :

```
http://<IP-de-la-machine>:3000
```

### ✅ Identifiants par défaut :

* **Login** : `admin`
* **Password** : `admin`

Tu seras invité à modifier le mot de passe à la première connexion.

---

## ✅ 7. \[Optionnel] Ouvrir le port 3000 dans UFW

```bash
sudo ufw allow 3000
```

---

## 🔧 Exemple : vérifier l’IP de la machine

```bash
ip a
```

---

## 🔄 Mettre à jour Grafana plus tard

```bash
sudo apt update
sudo apt upgrade grafana
```

---

Souhaites-tu maintenant :

* Ajouter **Prometheus comme source de données** dans Grafana ?
* Créer des **dashboards personnalisés** pour CPU, RAM, disque ?
* Installer Grafana via **Docker** à la place ?
