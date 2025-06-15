https://prometheus.io/docs/guides/basic-auth/

-  apt install python3-bcrypt

- nano gen-pass.py

- 
```python
import getpass
import bcrypt

password = getpass.getpass("password: ")
hashed_password = bcrypt.hashpw(password.encode("utf-8"), bcrypt.gensalt())
print(hashed_password.decode())
```


- $ python3 gen-pass.py
- That should prompt you for a password:

- password: (12345)
- $2b$12$hNf2lSsxfm0.i4a.1kVpSOVyBCfIB51VRjgBUyv6kdnyTlgWj81Ay


- nano web.yml
```bash
basic_auth_users:
    admin: $2b$12$hNf2lSsxfm0.i4a.1kVpSOVyBCfIB51VRjgBUyv6kdnyTlgWj81Ay
```

- $ promtool check web-config web.yml
- web.yml SUCCESS

- mv web.yml /etc/prometheus/web.yml

- $ prometheus --web.config.file=web.yml

- nano /etc/systemd/system/prometheus.service : 

```bash
[Unit]
Description=Prometheus Monitoring
Wants=network-online.target
After=network-online.target

[Service]
User=prometheus
Group=prometheus
Type=simple
ExecStart=/usr/local/bin/prometheus \
  --config.file=/etc/prometheus/prometheus.yml \
  --storage.tsdb.path=/var/lib/prometheus/ \
  --web.console.templates=/etc/prometheus/consoles \
  --web.config.file /etc/prometheus/web.yaml \
  --web.console.libraries=/etc/prometheus/console_libraries

[Install]
WantedBy=multi-user.target
```
- sudo systemctl daemon-reload
- sudo systemctl restart prometheus
- sudo systemctl status prometheus

- ouvrir le navigateur sur 192.168.126.139:9090 -> username : admin -password : 12345