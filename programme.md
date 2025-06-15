Parfait ! Si votre environnement de formation est basé sur **VMware ESXi**, voici comment **adapter le programme** pour coller à ce contexte tout en gardant un haut niveau de réalisme pour les participants.

---

## ✅ **Adaptation du programme de monitoring sur ESXi (VMware)**

### 🎯 Objectif complémentaire :

En plus des objectifs classiques, les apprenants apprendront à **monitorer les hôtes ESXi, les VMs, le stockage et les ressources réseau dans VMware** à l’aide de Prometheus, Grafana, et des exporters spécifiques.

---

## 🗓️ **Jour 1 – Monitoring Linux + ESXi avec Prometheus**

### 🕘 Matin :

* Concepts de base du monitoring.
* Introduction à Prometheus.
* Installation de Prometheus sur une VM Linux dans ESXi.
* Configuration réseau pour permettre la supervision des autres VMs.
* Accès SSH entre VMs (si nécessaire).

> **Atelier** : Création d'une VM Ubuntu + installation de Prometheus.

---

### 🕑 Après-midi :

* Installation de **Node Exporter** sur plusieurs VMs Linux.
* Visualisation des métriques système (CPU, RAM, disque).
* Introduction à **vmware\_exporter** (Exporter officiel pour ESXi).

> **Atelier** :

* Installation de `vmware_exporter` sur Prometheus.
* Connexion au vCenter ou hôte ESXi avec un compte lecture seule.
* Configuration du scraping Prometheus.

> 🎯 Résultat attendu : Scraping des métriques ESXi comme :

* CPU/RAM des hôtes
* VMs (CPU ready, memory usage, disk latency)
* Datastores

---

## 🗓️ **Jour 2 – Grafana, Alertes et Dashboards VMware**

### 🕘 Matin :

* Installation de Grafana sur une VM.
* Ajout de la source Prometheus.
* Création de dashboards :

  * Ressources ESXi
  * Datastore usage
  * Uptime VMs
* Import de dashboards communautaires (VMware Overview, etc.)

> **Atelier** : Création d’un dashboard pour suivre l’état des hôtes + VMs.

---

### 🕑 Après-midi :

* Introduction à Alertmanager.
* Règles d’alerte pour seuils CPU, RAM ou datastores.
* Envoi d’alertes par email ou webhook.

> **Atelier** : Générer des alertes simulées depuis ESXi ou VM Linux.

---

## 🗓️ **Jour 3 – Logs, supervision complète et cas d’usage**

### 🕘 Matin :

* Intégration avec **Grafana Loki** ou **ELK** pour collecter les logs système des VMs.
* Promtail ou Filebeat sur les VMs Linux.
* Vue corrélée entre logs et métriques.

> **Atelier** : Collecte des logs `/var/log/syslog` + visualisation dans Grafana.

---

### 🕑 Après-midi :

* Projet global : supervision d’un environnement ESXi complet
* Bonus : monitoring réseau (ICMP, ports) avec `blackbox_exporter`.
* Sécurisation et bonnes pratiques.

> **Atelier final** : Déploiement complet :

* Prometheus + vmware\_exporter
* Node Exporter sur 2 VMs
* Grafana avec 2 dashboards (système + VMware)
* Alertmanager avec règle critique sur usage datastore

---

## 🔧 Prérequis pour l’environnement :

* Une VM Linux Prometheus/Grafana
* Un hôte ESXi avec accès API activé
* Un compte en lecture seule ESXi ou vCenter
* Accès réseau entre les VMs
* Possibilité d’ouvrir des ports : 9100, 9272, 9090, 3000, etc.

---

Souhaitez-vous ce programme complet au format Word ou PowerPoint pour l’utiliser en formation ?
