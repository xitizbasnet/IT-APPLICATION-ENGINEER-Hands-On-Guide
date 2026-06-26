# 📘 1 | Application Deployment & Management

---

## 🧭 Lab Objective

* Understand how to deploy and manage applications on **Tomcat, Apache, IIS, and JBOSS**
* Set up **UAT, Production, and Disaster Recovery (DR)** environments
* Manage application upgrades, patches, and database deployments
* Ensure continuous uptime and availability of applications

---

# 🧩 TASK 1.1: Installing & Configuring Apache Tomcat

---

## 🟦 Step 1: Install Java (Tomcat Dependency)

Tomcat requires Java. Install OpenJDK on Linux:

```bash id="j1t9aa"
sudo apt update && sudo apt install -y openjdk-17-jdk
java -version   # Verify installation
```

---

## 🟦 Step 2: Download and Install Tomcat

```bash id="j2t8bb"
wget https://downloads.apache.org/tomcat/tomcat-10/v10.1.19/bin/apache-tomcat-10.1.19.tar.gz

tar -xvzf apache-tomcat-10.1.19.tar.gz -C /opt/
ln -s /opt/apache-tomcat-10.1.19 /opt/tomcat

chmod +x /opt/tomcat/bin/*.sh
```

---

## 🟦 Step 3: Create Tomcat Systemd Service

Create service file:

```bash id="j3t7cc"
sudo nano /etc/systemd/system/tomcat.service
```

### 📄 Service Configuration

```ini
[Unit]
Description=Apache Tomcat
After=network.target

[Service]
Type=forking
Environment=JAVA_HOME=/usr/lib/jvm/java-17-openjdk-amd64
Environment=CATALINA_HOME=/opt/tomcat
ExecStart=/opt/tomcat/bin/startup.sh
ExecStop=/opt/tomcat/bin/shutdown.sh
Restart=on-failure

[Install]
WantedBy=multi-user.target
```

---

### ▶️ Enable and Start Service

```bash id="j4t6dd"
sudo systemctl daemon-reload
sudo systemctl start tomcat
sudo systemctl enable tomcat
sudo systemctl status tomcat
```

---

## 🟦 Step 4: Deploy a WAR Application

```bash id="j5t5ee"
sudo cp myapp.war /opt/tomcat/webapps/
```

* Tomcat auto-deploys WAR files
* Access application:

  * `http://<server-ip>:8080/myapp`

### 📊 Monitor Logs

```bash id="j6t4ff"
tail -f /opt/tomcat/logs/catalina.out
```

---

# 🧩 TASK 1.2: Installing & Configuring Apache HTTP Server

---

## 🟨 Step 1: Install Apache

```bash id="a1p9aa"
sudo apt install -y apache2
sudo systemctl start apache2
sudo systemctl enable apache2
sudo systemctl status apache2
```

* Default page:

  * `http://<server-ip>/`

---

## 🟨 Step 2: Configure Virtual Host

```bash id="a2p8bb"
sudo nano /etc/apache2/sites-available/myapp.conf
```

### 📄 Virtual Host Config

```apache
ServerName myapp.example.com
DocumentRoot /var/www/myapp
ErrorLog ${APACHE_LOG_DIR}/myapp_error.log
CustomLog ${APACHE_LOG_DIR}/myapp_access.log combined
```

### ▶️ Enable Site

```bash id="a3p7cc"
sudo a2ensite myapp.conf
sudo systemctl reload apache2
```

---

## 🟨 Step 3: Enable Reverse Proxy to Tomcat

```bash id="a4p6dd"
sudo a2enmod proxy proxy_http
```

### 📄 Proxy Configuration

```apache
ProxyPass / http://localhost:8080/myapp/
ProxyPassReverse / http://localhost:8080/myapp/
```

```bash id="a5p5ee"
sudo systemctl reload apache2
```

---

# 🧩 TASK 1.3: IIS Setup on Windows Server

---

## 🟩 Step 1: Install IIS

```powershell id="i1s9aa"
Install-WindowsFeature -Name Web-Server -IncludeManagementTools
Get-Service -Name W3SVC
```

---

## 🟩 Step 2: Create New Website

```powershell id="i2s8bb"
New-WebSite -Name 'MyApp' -Port 80 -PhysicalPath 'C:\inetpub\myapp'
Start-WebSite -Name 'MyApp'
```

* Access:

  * `http://localhost`

---

## 🟩 Step 3: Deploy .NET Application

```powershell id="i3s7cc"
New-WebAppPool -Name 'MyAppPool'

Set-ItemProperty IIS:\AppPools\MyAppPool managedRuntimeVersion v4.0
```

```powershell id="i4s6dd"
Set-WebConfigurationProperty `
-Filter '/system.applicationHost/sites/site[@name="MyApp"]' `
-Name 'applicationPool' -Value 'MyAppPool'
```

---

# 🧩 TASK 1.4: UAT, Production & DR Environments

---

## 🟪 Step 1: Environment Strategy

```bash id="e1u9aa"
mkdir -p /opt/myapp/{uat,prod,dr}/conf
mkdir -p /opt/myapp/{uat,prod,dr}/logs
mkdir -p /opt/myapp/{uat,prod,dr}/webapps
```

---

## 🟪 Step 2: Environment Config Files

### UAT

```properties
db.host=uat-db.internal
db.port=5432
db.name=myapp_uat
```

### PROD

```properties
db.host=prod-db.internal
db.port=5432
db.name=myapp_prod
```

---

## 🟪 Step 3: Deploy to UAT First

```bash id="e2u8bb"
cp myapp.war /opt/myapp/uat/webapps/
/opt/tomcat/bin/startup.sh

curl http://uat-server:8080/myapp/health
```

---

## 🟪 Step 4: Promote to Production

```bash id="e3u7cc"
cp /opt/myapp/prod/webapps/myapp.war /opt/myapp/prod/webapps/myapp.war.bkp
cp myapp.war /opt/myapp/prod/webapps/

sudo systemctl restart tomcat
curl http://prod-server:8080/myapp/health
```

---

## 🟪 Step 5: DR Setup

```bash id="e4u6dd"
rsync -avz /opt/myapp/prod/ dr-server:/opt/myapp/dr/
```

### Cron Job

```bash id="e5u5ee"
crontab -e
0 2 * * * rsync -avz /opt/myapp/prod/ dr-server:/opt/myapp/dr/ >> /var/log/dr-sync.log 2>&1
```

---

# ⭐ BEST PRACTICE TIPS

* Always deploy to **UAT first**, then PROD
* Maintain **rollback WAR backups** before deployment
* Use **version-controlled configs (Git)** for environment properties
* Monitor logs using `tail -f` during deployments
* Implement **health check endpoints** (`/health`, `/status`)
* Document every deployment in **Change Management logs**
* Restrict firewall ports: **80, 443, 8080 only**
* Test DR failover **quarterly**

---

## 📌 Notes

This lab demonstrates enterprise application deployment lifecycle management across multiple platforms (Tomcat, Apache, IIS), including environment separation (UAT/PROD/DR), reverse proxy configuration, and production-grade deployment practices.
