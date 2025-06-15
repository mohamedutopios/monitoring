-# ✅ 🔐 Tutoriel pas à pas pour mettre en place l'authentification basique sur Prometheus

> 👇 Ce guide suit **exactement** la méthode proposée par Prometheus.io
> VM Linux Ubuntu/Debian avec Prometheus déjà en place sur `localhost:9090`

---

## 🧩 ÉTAPE 1 — Installer NGINX et les outils nécessaires

```bash
sudo apt update
sudo apt install nginx apache2-utils -y
```

---

## 🔐 ÉTAPE 2 — Créer un fichier `.htpasswd` pour l’authentification

```bash
sudo htpasswd -c /etc/nginx/.htpasswd admin
```

* Le `-c` crée le fichier
* `admin` est le nom d'utilisateur (remplace-le si besoin)
* Entres ton mot de passe quand demandé

> Le fichier `/etc/nginx/.htpasswd` contiendra quelque chose comme :

```
admin:$apr1$X...$9vX1zYmHNgXIO....
```

---

## 🔧 ÉTAPE 3 — Créer ou modifier la config NGINX pour Prometheus

Crée un nouveau fichier de site :

```bash
sudo nano /etc/nginx/sites-available/prometheus
```

Colle ceci :

```nginx
server {
    listen 8080;

    location / {
        proxy_pass http://localhost:9090;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;

        auth_basic "Protected Prometheus";
        auth_basic_user_file /etc/nginx/.htpasswd;
    }
}
```

> Tu peux aussi écouter sur le port `80` (si tu veux remplacer par défaut) ou `443` (avec HTTPS), mais ici on reste sur `8080`.

---

## 🔗 ÉTAPE 4 — Activer la config et recharger NGINX

```bash
sudo ln -s /etc/nginx/sites-available/prometheus /etc/nginx/sites-enabled/
sudo nginx -t
sudo systemctl reload nginx
```

---

## 🔍 ÉTAPE 5 — Tester

Dans ton navigateur :

```
http://[IP_VM_LINUX]:8080
```

✅ Tu verras un **popup de login** :

* Utilisateur : `admin`
* Mot de passe : celui que tu as défini

Ensuite, tu verras l’interface Prometheus mais protégée.

---

## 🛡️ ÉTAPE 6 — (Optionnel mais recommandé) : restreindre l'accès externe à Prometheus

Prometheus écoute encore sur `:9090` sans protection !

Tu peux :

* Modifier `/etc/prometheus/prometheus.yml` ou le service pour qu’il écoute **uniquement sur localhost** :

### 🔧 Exemple dans le service systemd :

```bash
ExecStart=/usr/local/bin/prometheus \
  --web.listen-address=127.0.0.1:9090 \
  ...
```

Puis :

```bash
sudo systemctl daemon-reexec
sudo systemctl daemon-reload
sudo systemctl restart prometheus
```

> Ainsi, seul NGINX pourra le joindre via `localhost:9090`, et **toute connexion externe passe par le reverse proxy sécurisé**.

---

Souhaites-tu :

* Ajouter aussi un certificat HTTPS avec Let’s Encrypt ?
* Protéger d'autres endpoints (`/metrics`) différemment ?
* Ou créer un équivalent pour Grafana également ?
