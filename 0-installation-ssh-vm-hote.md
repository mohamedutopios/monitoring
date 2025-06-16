Parfait, voici un **guide complet étape par étape** pour **configurer une connexion SSH (avec mot de passe ou automatisée)** **depuis une machine Windows vers une VM Ubuntu**.

---

## ✅ Côté Ubuntu (la VM cible)

### 1. 📦 Installer le serveur SSH (si ce n’est pas déjà fait)

```bash
sudo apt update
sudo apt install openssh-server -y
```

---

### 2. ✅ Vérifier que le service SSH est actif

```bash
sudo systemctl status ssh
```

✅ Si actif : tu devrais voir `active (running)`
❌ Si inactif :

```bash
sudo systemctl enable --now ssh
```

---

### 3. 🔧 Configuration du serveur SSH (optionnel mais recommandé)

Édite le fichier :

```bash
sudo nano /etc/ssh/sshd_config
```

Vérifie ou modifie ces lignes :

```ini
Port 22
PermitRootLogin yes                # ou 'prohibit-password' si tu n'utilises pas root
PasswordAuthentication yes         # doit être YES si tu veux te connecter par mot de passe
```

Redémarre SSH :

```bash
sudo systemctl restart ssh
```

---

### 4. 🌐 Vérifie l’adresse IP de ta VM Ubuntu

```bash
ip a
```

Note celle sur l’interface `eth0`, `ens33`, etc. Exemple : `192.168.56.101`

---

### 5. 🔥 Vérifie le pare-feu (UFW)

Autorise SSH si ce n’est pas fait :

```bash
sudo ufw allow ssh
sudo ufw enable       # (si le pare-feu est désactivé)
```

---

## 💻 Côté Windows (machine hôte)

### A. Utiliser PowerShell ou CMD

```bash
ssh username@192.168.x.x
```

> Si ça ne fonctionne pas, vérifie que :
>
> * ta VM est en **mode bridge** ou **NAT avec port forward**.
> * le port `22` est accessible.
> * le pare-feu Windows n’empêche pas la sortie sur le port 22.

---

### B. 📦 Utiliser `sshpass` (dans WSL ou Git Bash) pour automatiser la connexion sans clé

```bash
sshpass -p 'motdepasse' ssh -o StrictHostKeyChecking=no user@192.168.x.x
```

> 🚨 Ce n'est pas sécurisé, mais fonctionne pour un lab/test.

---

## 📦 Résumé

| Étape | Côté Ubuntu (VM)                            |
| ----- | ------------------------------------------- |
| 1     | Installer `openssh-server`                  |
| 2     | Démarrer le service SSH                     |
| 3     | Configurer `/etc/ssh/sshd_config` si besoin |
| 4     | Ouvrir le port avec `ufw allow ssh`         |
| 5     | Trouver l’IP avec `ip a`                    |

| Étape | Côté Windows                                       |
| ----- | -------------------------------------------------- |
| A     | Tester connexion avec `ssh user@ip`                |
| B     | Optionnel : automatiser avec `sshpass` ou `expect` |

---

Souhaites-tu aussi :

* un **script de vérification automatique** côté Ubuntu ?
* ou un **tutoriel complet pour configurer des connexions SSH par clé (sécurisé)** ?
