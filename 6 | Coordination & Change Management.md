# 🧭 6 | Coordination & Change Management

---

## 🎯 Lab Objective

• Coordinate testing and deployment cycles among all stakeholders.
• Facilitate communication between business users, technical teams, and vendors.
• Verify application functionality after upgrades or maintenance activities.
• Document change requests, maintain ITIL change management practices.

---

# 🧾 TASK 6.1: Creating a Change Request (CR) Document

---

## 📌 Step 1: Change Request Template

Every deployment must have a formal Change Request. Use this structure:

| CR Field          | Description / Example                           |
| ----------------- | ----------------------------------------------- |
| CR Title          | Deploy MyApp v2.3.1 to Production               |
| Change Type       | Standard / Emergency / Normal                   |
| Priority          | Low / Medium / High / Critical                  |
| Requester         | Application Owner / Business Team               |
| Implementer       | Vinod Muleva (IT App Engineer)                  |
| Scheduled Date    | 2024-02-10 02:00 AM IST (maintenance window)    |
| Rollback Plan     | Restore myapp.war.bkp and restart Tomcat        |
| Pre-change Backup | Database dump + WAR backup taken at 01:30 AM    |
| Test Plan         | Health check URL, smoke test 5 key workflows    |
| Approvers         | IT Manager + Business Owner signature required  |
| Post-change Steps | Verify 3 core transactions, monitor logs 30 min |

---

# ✅ TASK 6.2: Post-Deployment Verification Checklist

---

## 🔍 Step 1: Application Health Verification Steps

After every deployment, systematically verify these checkpoints:

```bash id="q9m1xk"
IT APPLICATION ENGINEER — Hands-On Lab Guide Application Deployment | Support | Security | Databases | Automation
IT Application Engineer Lab Guide | Confidential Page 22
```

---

### 🖥️ 1. Service Status Check

```bash
sudo systemctl status tomcat # or apache2 / iis
```

---

### 🌐 2. Application Health Endpoint

```bash
curl -I http://prod-server:8080/myapp/health
```

Expected:

```
HTTP/1.1 200 OK
```

---

### 📜 3. Log Check (first 5 min)

```bash
tail -n 200 /opt/tomcat/logs/catalina.out | grep -i 'error\|exception'
```

---

### 🗄️ 4. Database Connectivity from App

```bash
psql -h prod-db -U myapp_user -d myapp_db -c 'SELECT 1;'
```

---

### 🧪 5. End-to-End Smoke Test

Manually test 3–5 critical user journeys:

• User login
• Core transaction
• Report generation

---

### 📣 6. Notify Stakeholders

• Send deployment completion email/Slack message
• Confirm system stability after verification

---

# 📢 TASK 6.3: Stakeholder Communication Template

---

## 📬 Step 1: Maintenance Window Announcement

Send this communication 48 hours before scheduled maintenance:

```
Subject: [ACTION REQUIRED] Planned Maintenance — MyApp — Feb 10, 2:00–4:00 AM IST

Dear Team,

Please be informed that MyApp will be unavailable during planned maintenance:

DATE   : Saturday, February 10, 2024  
TIME   : 02:00 AM – 04:00 AM IST  
IMPACT : MyApp will be completely inaccessible during this window  
REASON : Version 2.3.1 deployment with security patches and performance fixes  

Please plan your work accordingly.

In case of emergency contact: vinod.muleva@company.com | +91-XXXXXXXXXX

Regards,  
IT Application Support Team
```

---

## 📬 Step 2: Post-Maintenance Completion Notice

Send this after successful deployment:

```bash id="xk3d8p"
IT APPLICATION ENGINEER — Hands-On Lab Guide Application Deployment | Support | Security | Databases | Automation
IT Application Engineer Lab Guide | Confidential Page 23
```

```
Subject: [RESOLVED] MyApp Maintenance Complete — All Systems Normal

Dear Team,

The planned maintenance for MyApp has been completed successfully.

COMPLETED AT : 03:15 AM IST (ahead of schedule)  
VERSION      : v2.3.1 now live in Production  
STATUS       : All systems operational  
VERIFIED     : Health checks passed | Core transactions validated  

Please resume normal operations. If you notice any issues,
raise a ticket at helpdesk@company.com or call +91-XXXXXXXXXX.

Regards,
IT Application Support Team
```

---

# 🔎 TASK 6.4: Root Cause Analysis (RCA) Documentation

---

## 🧠 Step 1: Write a Proper RCA Report

RCA must be completed within 24–48 hours of every P1/P2 incident:

| RCA Section          | Content                                                        |                   
| -------------------- | -------------------------------------------------------------- | 
| Incident Summary     | MyApp login page unavailable — 2h 15min on Jan 20, 2024        |                    
| Timeline             | 10:00 AM: Alert fired, 10:05: Engineer engaged, 12:15: Resolved|  
| Root Cause           | Database connection pool exhausted due to unclosed connections |                        
| Contributing Factors | Connection timeout config missing from app.properties          |                        
| Impact               | 250 users affected, 2 failed transactions (recoverable)        |                     
| Immediate Fix        | Restarted app server, increased pool size from 10 to 50        |                        
| Permanent Fix        | Added connection timeout config, PR reviewed and deployed      |                        
| Preventive Actions   | Added DB connection pool monitoring alert in Prometheus        |                     
| Owner / Due Date     | Vinod Muleva, Feb 1, 2024                                                   

---

# ⚡ BEST PRACTICE TIPS

**BEST PRACTICE TIPS**

* Never deploy directly to PROD—always follow: **DEV → UAT (signoff) → PROD** with a Change Request (CR).
* All Change Requests must have documented rollback plans before CAB approval.
* Send pre-maintenance communication at least 48 hours in advance to all stakeholders.
* Run smoke tests on five critical business flows within 30 minutes of every PROD deployment.
* Complete RCA documentation within 24 hours of every P1 incident.
* Keep a deployment runbook with exact commands in order, and test it on UAT before PROD.
* Always confirm with the business owner before closing any incident ticket.

