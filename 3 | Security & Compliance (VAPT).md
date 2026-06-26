# 📘 3 | Security & Compliance (VAPT)

---

## 🧭 Lab Objective

* Coordinate Vulnerability Assessment and Penetration Testing (VAPT) activities
* Configure SSL/TLS to secure application communications
* Communicate VAPT findings to vendors and track remediation closure
* Understand compliance evaluations pertaining to the application

---

# 🧩 TASK 3.1: Vulnerability Assessment using Nmap & Nikto

---

## 🟦 Step 1: Install Assessment Tools

```bash id="v1n9aa"
sudo apt update
sudo apt install -y nmap nikto
```

---

## 🟦 Step 2: Network Port Scan (Nmap)

```bash id="v2n8bb"
nmap -sV <target>
nmap -p 1-1024 <target>
nmap -A -T4 <target>
nmap -sV -oN scan_results.txt <target>
```

---

## 🟦 Step 3: Web Application Scan (Nikto)

```bash id="v3n7cc"
nikto -h http://<host>:8080
nikto -h https://<host> -ssl
nikto -h http://<host>:8080 -o nikto_report.html -Format htm
```

---

## 🟦 Step 4: Analyze & Document Findings

| Severity | CVSS Score | Action Required     | Timeline    |
| -------- | ---------- | ------------------- | ----------- |
| Critical | 9.0 – 10.0 | Immediate patch/fix | 24–48 hours |
| High     | 7.0 – 8.9  | Urgent remediation  | 1 week      |
| Medium   | 4.0 – 6.9  | Planned fix         | 1 month     |
| Low      | 0.1 – 3.9  | Accepted risk / fix | Quarter     |
| Info     | 0.0        | Awareness only      | As needed   |

---

# 🧩 TASK 3.2: SSL/TLS Certificate Configuration

---

## 🟨 Step 1: Generate Self-Signed Certificate (UAT)

```bash id="s1c9aa"
openssl req -x509 -nodes -days 365 -newkey rsa:2048 \
-keyout /etc/ssl/private/myapp.key \
-out /etc/ssl/certs/myapp.crt \
-subj '/CN=myapp.example.com/O=MyOrg/C=IN'
```

---

## 🟨 Step 2: Configure SSL in Apache

```bash id="s2c8bb"
sudo a2enmod ssl
sudo nano /etc/apache2/sites-available/myapp-ssl.conf
```

### 📄 SSL Virtual Host Configuration

```apache id="s3c7cc"
ServerName myapp.example.com
SSLEngine on
SSLCertificateFile /etc/ssl/certs/myapp.crt
SSLCertificateKeyFile /etc/ssl/private/myapp.key

SSLProtocol all -SSLv2 -SSLv3 -TLSv1 -TLSv1.1
SSLCipherSuite HIGH:!aNULL:!MD5

DocumentRoot /var/www/myapp
```

```bash id="s4c6dd"
sudo a2ensite myapp-ssl.conf
sudo systemctl reload apache2
```

---

## 🟨 Step 3: Configure SSL in Tomcat

### 📄 server.xml modification

```xml id="t1m9aa"
<Connector port="8443"
           protocol="org.apache.coyote.http11.Http11NioProtocol"
           maxThreads="150"
           SSLEnabled="true"
           scheme="https"
           secure="true"
           keystoreFile="conf/keystore.jks"
           keystorePass="changeit" />
```

---

### 🔐 Create Keystore

```bash id="t2m8bb"
openssl pkcs12 -export -in myapp.crt -inkey myapp.key -out myapp.p12
keytool -importkeystore -destkeystore keystore.jks \
-srckeystore myapp.p12 -srcstoretype PKCS12
```

---

## 🟨 Step 4: Verify SSL Configuration

```bash id="v4s9aa"
openssl s_client -connect myapp.example.com:443 -showcerts
```

```bash id="v5s8bb"
echo | openssl s_client -connect myapp.example.com:443 2>/dev/null \
| openssl x509 -noout -dates
```

```bash id="v6s7cc"
nmap --script ssl-enum-ciphers -p 443 myapp.example.com
```

* Online validation:

  * [https://www.ssllabs.com/ssltest/](https://www.ssllabs.com/ssltest/)

---

# 🧩 TASK 3.3: VAPT Finding Remediation & Tracking

---

## 🟪 Step 1: VAPT Tracking Sheet

| Field                | Description                    |
| -------------------- | ------------------------------ |
| Finding ID           | Unique ID                      |
| Severity             | Critical / High / Medium / Low |
| CVE ID               | Vulnerability reference        |
| Description          | Issue details                  |
| Affected Component   | Module/service impacted        |
| Vendor Notified Date | When vendor informed           |
| Expected Fix Date    | SLA-based timeline             |
| Status               | Open / In Progress / Closed    |
| Closure Date         | Final resolution date          |

---

## 🟪 Step 2: Vendor Communication Template

**Subject:**
VAPT Findings — [Application Name] — [Date]

**Content:**

* Executive Summary (Critical / High / Medium / Low counts)
* Detailed vulnerability description
* CVE references
* Affected components & versions
* Evidence (logs / screenshots)
* Recommended remediation steps
* Expected fix timelines

---

## 🟪 Step 3: Post-Fix Verification

```bash id="v7v9aa"
nmap -sV -p <port> <target>
```

```bash id="v8v8bb"
nikto -h https://<host> -ssl
```

* Update tracker status → **CLOSED with evidence**

---

# ⭐ BEST PRACTICE TIPS

* Never run VAPT on production without written approval
* Enforce **TLS 1.2 / 1.3 minimum** (disable older protocols)
* Monitor certificate expiry (alert ≥ 30 days before expiry)
* Maintain centralized VAPT tracking with audit history
* Follow **OWASP Top 10** security guidelines
* Always re-scan after vendor fixes (verify remediation)
* Apply least privilege for application service accounts
* Secure VAPT reports with restricted access

---

## 📌 Notes

This lab focuses on enterprise application security operations including vulnerability scanning, SSL/TLS hardening, compliance tracking, vendor coordination, and remediation validation within a structured IT security workflow.
