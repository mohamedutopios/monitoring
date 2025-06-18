# 🧪 TP : Supervision et Alerting Multi-VM avec Prometheus, Grafana, Alertmanager et Node Exporter



## 🎯 Objectif du TP

Vous allez mettre en place une **infrastructure complète de supervision** basée sur **Prometheus, Grafana, Alertmanager** et **Node Exporter**, répartie sur **3 machines virtuelles Ubuntu**.
Le but est de surveiller l’état des serveurs, de déclencher des alertes (par mail ou Slack), et de visualiser l’ensemble dans un dashboard personnalisé (ou existant dans une banque de donnée Grafana).


## 🖥️ Environnement de travail

| Nom de la VM    | Rôle                                                         |
| --------------- | ------------------------------------------------------------ |
| `vm-prometheus` | Serveur de supervision (Prometheus + Grafana + Alertmanager) |
| `vm-server1`    | Serveur supervisé n°1 (avec Node Exporter)                   |
| `vm-server2`    | Serveur supervisé n°2 (avec Node Exporter)                   |

> # Les 3 machines tournent sur VMware Workstation (pas d'ESXi) !!



## 🧩 Étapes du TP



### 🔹 Étape 1 — Préparation des machines

1. Vérifiez que les 3 VMs ont bien un accès réseau entre elles (testez avec `ping` ou `curl`).
2. Mettez à jour les paquets et installez les utilitaires de base nécessaires.
3. Donnez un **nom d’hôte explicite** à chaque machine (`vm-prometheus`, `vm-server1`, `vm-server2`).



### 🔹 Étape 2 — Installation des composants

#### Sur `vm-prometheus` :

* Installez et configurez :

  * Prometheus
  * Grafana
  * Alertmanager
* Assurez-vous que chaque service démarre au boot et écoute sur son port standard.

#### Sur `vm-server1` et `vm-server2` :

* Installez **Node Exporter**
* Assurez-vous qu’il s’exécute comme un service et expose les métriques sur le port 9100.



### 🔹 Étape 3 — Configuration de Prometheus

1. Configurez Prometheus pour récupérer les métriques depuis `vm-server1` et `vm-server2`.
2. Associez des **labels clairs** à chaque instance (ex : `instance: server1`, `instance: server2`).
3. Mettez en place un dossier dédié pour les règles (`rules/`) et préparez Prometheus à charger vos futures règles d’alerte.



### 🔹 Étape 4 — Création des règles d’alerte

Vous devez créer deux règles dans un fichier dédié :

* Une alerte **pour `vm-server1`** lorsque l’espace disque utilisé dépasse **50%** pendant plus d'une minute.
  Cette alerte devra être **envoyée par mail** à `mohamed@utopios.net`.

* Une alerte **pour `vm-server2`** lorsqu'une **charge élevée** est détectée (par exemple : load > 1).
  Cette alerte devra être **envoyée sur un canal Slack** appelé `#probleme-server2`.

> Chaque règle devra contenir une **description claire** et un **niveau de sévérité**.



### 🔹 Étape 5 — Configuration d’Alertmanager

1. Créez une configuration pour **router les alertes vers différents "receivers"** :

   * Un **receiver email** pour les alertes `server1`
   * Un **receiver Slack** pour les alertes `server2`

2. Configurez un **routing conditionnel** basé sur les labels d’alerte (par exemple `instance` ou `alert_target`).

3. Vérifiez que la configuration est bien prise en compte au redémarrage du service.



### 🔹 Étape 6 — Configuration de Grafana

1. Connectez Grafana à Prometheus comme source de données.

#### Option 2 : Creation de son dashboard

2. Créez un **dashboard unique** avec les éléments suivants :

   * Utilisation disque pour `vm-server1`
   * Charge système pour `vm-server2`
   * CPU et RAM pour les deux serveurs
   * Un panneau qui affiche l’état des alertes actives

3. Organisez votre dashboard de façon claire et exploitable.

#### Option 1 : Utilisation Dashboard propoosé par Grafana

Le **dashboard** ou les **dashboard** aura, à minima, les éléments suivants :

   * Utilisation disque pour `vm-server1`
   * Charge système pour `vm-server2`
   * CPU et RAM pour les deux serveurs
   * Un panneau qui affiche l’état des alertes actives



### 🔹 Étape 7 — Vérification des alertes et simulation

#### Pour `vm-server1` :

* Simulez une consommation importante de disque (sans danger pour le système).
* Vérifiez le déclenchement de l’alerte dans Prometheus et la réception du **mail**.

#### Pour `vm-server2` :

* Lancez un processus qui augmente temporairement la charge système (ex: `stress`).
* Vérifiez la réception d’une **notification Slack**.

> Vérifiez aussi que l’information est visible et cohérente sur le dashboard Grafana.


### 🔹 Étape 8 — Rendu et validation

Chaque binôme devra fournir :

1. Une capture d’écran de Grafana avec les bons panels visibles.
2. Une capture d’écran du mail et de la notification Slack reçus.
3. Une courte note expliquant :

   * Les métriques utilisées dans chaque alerte
   * Comment la configuration des routes Alertmanager a été pensée
   * Ce qui pourrait être amélioré pour une production réelle


