# 🧾 CHEAT SHEET | IT Application Engineer Commands Reference

---

# 🐧 Linux Troubleshooting

Essential Linux commands for system monitoring and troubleshooting:

```bash
top / htop
```

• CPU & memory monitoring

```bash
df -h / du -sh *
```

• Disk usage overview
• `df -h` = filesystem usage
• `du -sh *` = folder-level usage

```bash
tail -f /var/log/*.log
```

• Live log monitoring (real-time updates)

```bash
grep -i 'error' app.log
```

• Search errors inside logs (case-insensitive)

```bash
systemctl status
```

• Check service status (systemd-based services)

```bash
ss -tlnp
```

• List open/listening ports with process details

```bash
ps aux | grep <process_name>
```

• Check running processes

---

# 🖥️ Application Server Commands

```bash
sudo systemctl start|stop|restart tomcat
```

• Control Tomcat service lifecycle

```bash
sudo systemctl reload apache2
```

• Reload Apache configuration without full restart

```bash
iisreset /noforce
```

• Restart IIS (Windows servers)

```bash
curl -I http://localhost:8080/app/health
```

• Application health check (HTTP header response)

```bash
tail -f /opt/tomcat/logs/catalina.out
```

• Live monitoring of Tomcat logs

---

# 🗄️ Database Quick Commands

```bash
sudo -u postgres psql
```

• Login to PostgreSQL

```bash
mysql -u root -p
```

• Login to MySQL

```sql
\l
-- or --
show databases;
```

• List all databases

```sql
\c dbname
-- or --
use dbname;
```

• Switch to a specific database

```bash
pg_dump -U user db > bkp.sql
```

• PostgreSQL backup

```bash
mysqldump -u user -p db > bkp.sql
```

• MySQL backup

---

# 🔐 Security & SSL Commands

```bash
openssl s_client -connect host:443
```

• Check SSL certificate details

```bash
openssl x509 -noout -dates -in cert.crt
```

• Check SSL certificate expiry

```bash
nmap -sV
```

• Port scanning & service detection

```bash
nikto -h http://:8080
```

• Web vulnerability scan

```bash
a2enmod ssl && systemctl reload apache2
```

• Enable SSL module in Apache and reload service

---

# 🌐 API Testing Commands

```bash
curl -X GET https://api.app.com/resource
```

• Send GET request

```bash
curl -X POST -d '{"key":"val"}'
```

• Send POST request with JSON payload

```bash
curl -I
```

• Fetch headers only (useful for quick checks)

```bash
nc -zv <host> <port>
```

• Check port reachability / connectivity test

---

 
