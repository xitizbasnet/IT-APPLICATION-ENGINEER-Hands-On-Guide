# 📘 LAB 2 | Technical Support & Troubleshooting

---

## 🧭 Lab Objective

* Serve as first point of contact for application availability and performance issues
* Troubleshoot Linux and Windows environments where applications are deployed
* Escalate complex issues to vendors while maintaining SLA compliance
* Analyze logs to identify root cause and resolve incidents quickly

---

# 🧩 TASK 2.1: Linux System Troubleshooting

---

## 🟦 Step 1: Check System Resource Usage

Start by verifying CPU, memory, and disk usage to rule out resource starvation:

```bash id="l1r9aa"
top            # Real-time CPU & memory usage
htop           # Interactive process monitoring
free -h        # Memory usage
df -h          # Disk usage
du -sh /opt/myapp/*   # Application-level disk usage
iostat -x 1 5  # Disk I/O statistics
```

---

## 🟦 Step 2: Check Running Processes & Application Status

```bash id="l2r8bb"
ps aux | grep tomcat
ps aux | grep java
systemctl status tomcat
netstat -tlnp | grep 8080
ss -tlnp | grep 8080
```

---

## 🟦 Step 3: Analyze Logs (Critical for RCA)

```bash id="l3r7cc"
tail -f /opt/tomcat/logs/catalina.out
grep -i 'error\|exception' /opt/tomcat/logs/catalina.out
tail -f /var/log/apache2/error.log
journalctl -u tomcat -n 100 --no-pager
journalctl -u tomcat --since '1 hour ago'
```

---

## 🟦 Step 4: Check Network Connectivity

```bash id="l4r6dd"
ping -c 4 google.com
curl -I http://localhost:8080/myapp
telnet <host> 5432
traceroute <destination>
nslookup myapp.example.com
```

---

## 🟦 Step 5: Java Heap & Thread Dump Analysis

```bash id="l5r5ee"
PID=$(pgrep -f tomcat)

jstack $PID > /tmp/threaddump_$(date +%Y%m%d_%H%M%S).txt

jmap -dump:format=b,file=/tmp/heapdump.hprof $PID
```

### 🧠 GC Logging Configuration

```bash id="l6r4ff"
-Xlog:gc*:file=/opt/tomcat/logs/gc.log:time,uptime:filecount=10,filesize=5m
```

---

# 🧩 TASK 2.2: Windows Server Troubleshooting

---

## 🟩 Step 1: System Resource Checks

```powershell id="w1s9aa"
Get-Process | Sort-Object CPU -Descending | Select-Object -First 10

Get-WmiObject Win32_OperatingSystem |
Select FreePhysicalMemory,TotalVisibleMemorySize

Get-PSDrive -PSProvider FileSystem

Get-EventLog -LogName Application -Newest 50 -EntryType Error
```

---

## 🟩 Step 2: IIS Application Status

```powershell id="w2s8bb"
Get-WebSite | Select Name,State,PhysicalPath
Get-WebAppPoolState -Name 'MyAppPool'

Restart-WebAppPool -Name 'MyAppPool'
iisreset /noforce
```

---

## 🟩 Step 3: Windows Event Logs

```powershell id="w3s7cc"
Get-EventLog -LogName Application -EntryType Error -Newest 20
Get-EventLog -LogName System -EntryType Error -Newest 20
Get-EventLog -LogName Application -InstanceId 1000 -Newest 10
```

---

# 🧩 TASK 2.3: SLA Management & Escalation

---

## 🟨 Step 1: SLA Matrix

| Priority | Type                   | Response Time | Resolution Time |
| -------- | ---------------------- | ------------- | --------------- |
| P1       | Critical system outage | 15 minutes    | 2 hours         |
| P2       | Major feature failure  | 30 minutes    | 4 hours         |
| P3       | Partial degradation    | 2 hours       | 8 hours         |
| P4       | Minor issue/request    | 4 hours       | 24 hours        |

---

## 🟨 Step 2: Incident Workflow

1. **DETECT** → Monitoring alert or user report
2. **LOG** → Create ticket in ServiceNow / Jira
3. **TRIAGE** → Assign priority (P1–P4)
4. **DIAGNOSE** → Collect logs, reproduce issue
5. **FIX** → Apply fix or workaround
6. **VALIDATE** → Confirm resolution
7. **CLOSE** → Document RCA
8. **PREVENT** → Improve monitoring/runbook

---

## 🟨 Step 3: Vendor Escalation Checklist

Include:

* Incident ticket ID and priority
* Error message / stack trace
* Steps to reproduce
* Time of occurrence
* System details (OS, app version, JVM version)
* Relevant logs
* Troubleshooting already performed

---

# ⭐ BEST PRACTICE TIPS

* Start with logs — most root causes are found there
* Never restart services before capturing diagnostics (thread/heap dump)
* Maintain updated runbooks for recurring issues
* Use monitoring tools (Prometheus / Zabbix / Nagios) proactively
* Provide stakeholder updates every 30 minutes during P1 incidents
* Always document troubleshooting steps in ticket history
* Share sanitized logs only during vendor escalation

---

## 📌 Notes

This lab focuses on real-world L2 support operations, emphasizing structured troubleshooting, SLA-driven incident handling, and effective escalation practices across Linux and Windows enterprise environments.
