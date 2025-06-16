Parfait ! Tu veux activer **HTTPS** pour accéder à l’interface de Prometheus. C’est une **excellente pratique** pour sécuriser l’accès (chiffrement, protection contre l’interception, etc.).

---

# ✅ Objectif

* Accès à Prometheus via `https://IP_VM:443` (ou un domaine)
* Utiliser **NGINX** comme **reverse proxy TLS** devant Prometheus
* Avec **certificats SSL/TLS valides (Let’s Encrypt)** ou **auto-signés** selon les cas

---

## 📌 Deux solutions possibles :

### ✅ **Solution 1 : Avec Let’s Encrypt** (recommandé si tu as un domaine public)

* Gratuit, simple avec **Certbot**
* Nécessite que la VM soit **accessible depuis Internet** (port 80 ouvert)
* Résultat : certificat TLS signé par une autorité reconnue

### ✅ **Solution 2 : Certificat auto-signé**

* Rapide pour usage **interne**
* Le navigateur affichera une alerte, mais le trafic est bien chiffré

---

# 🔧 Mise en place HTTPS avec NGINX

---

## 🔁 PRÉREQUIS

* Prometheus tourne sur `localhost:9090`
* NGINX est installé (`sudo apt install nginx`)
* Authentification basique en place (facultatif)
* Prometheus n'est **accessible que via NGINX**

---

## 🛠️ ÉTAPES POUR HTTPS (avec certificat auto-signé)

---

### ✅ 1. Générer un certificat auto-signé

```bash
sudo openssl req -x509 -nodes -days 365 -newkey rsa:2048 \
 -keyout /etc/ssl/private/prometheus.key \
 -out /etc/ssl/certs/prometheus.crt \
 -subj "/C=FR/ST=Ile-de-France/L=Paris/O=Monitoring/OU=IT/CN=prometheus.local"
```

---

### ✅ 2. Configurer NGINX avec SSL

Édite ou crée le fichier NGINX pour Prometheus :

```bash
sudo nano /etc/nginx/sites-available/prometheus
```

Contenu :

```nginx
server {
    listen 443 ssl;
    server_name _;

    ssl_certificate     /etc/ssl/certs/prometheus.crt;
    ssl_certificate_key /etc/ssl/private/prometheus.key;

    location / {
        proxy_pass http://localhost:9090;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;

        auth_basic "Protected Prometheus";
        auth_basic_user_file /etc/nginx/.htpasswd;
    }
}

# Optionnel : rediriger HTTP vers HTTPS
server {
    listen 80;
    return 301 https://$host$request_uri;
}
```

---

### ✅ 3. Activer le site et recharger NGINX

```bash
sudo ln -s /etc/nginx/sites-available/prometheus /etc/nginx/sites-enabled/
sudo nginx -t
sudo systemctl reload nginx
```

### Parefeu : 

- sudo ufw status
- sudo ufw allow 80/tcp
- sudo ufw allow 443/tcp
- sudo ufw reload

---

### ✅ 4. Tester

Ouvre dans ton navigateur :

```
https://[IP_VM]/
```

* Le navigateur affichera une **alerte de certificat non valide** (car auto-signé)
* Tu peux l’accepter pour continuer
* 🔐 Le trafic est maintenant **chiffré en HTTPS**

---

## ✅ Variante : utiliser **Let’s Encrypt (Certbot)** avec un domaine

> Si tu as un domaine pointant vers ta VM (ex : `prometheus.mondomaine.com`)

### 1. Installe certbot

```bash
sudo apt install certbot python3-certbot-nginx -y
```

### 2. Obtiens un certificat

```bash
sudo certbot --nginx -d prometheus.mondomaine.com
```

→ NGINX sera automatiquement reconfiguré avec HTTPS + renouvellement auto

---

## 🔐 Bonus : durcir la config TLS (optionnel)

Ajoute dans ton bloc `server` :

```nginx
ssl_protocols TLSv1.2 TLSv1.3;
ssl_ciphers HIGH:!aNULL:!MD5;
ssl_prefer_server_ciphers on;
```

---

Souhaites-tu que je t’envoie :

* Un script Bash automatique pour faire tout ça ?
* Ou la même chose pour Grafana en HTTPS ?
