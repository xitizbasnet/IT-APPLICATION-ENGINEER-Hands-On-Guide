# 🧪 5 | Automation & API Testing

---

## 🎯 Lab Objective

• Use Selenium to automate repetitive UI testing and regression activities.

• Work with API requests (GET, POST, PUT, DELETE) for application testing.

• Understand IP addresses and ports for effective network troubleshooting.

• Use Browserstack for cross-browser testing of web applications.


---

# 🧩 TASK 5.1: Selenium Web Automation Basics

---

## ⚙️ Step 1: Install Selenium and ChromeDriver

Set up Selenium in Python:

```bash
pip install selenium webdriver-manager

# Also install Chrome browser
wget https://dl.google.com/linux/direct/google-chrome-stable_current_amd64.deb
sudo apt install ./google-chrome-stable_current_amd64.deb
```

---

## 🤖 Step 2: Write a Basic Selenium Test

Create a test that opens a browser and performs login:

```python
# test_login.py
from selenium import webdriver
from selenium.webdriver.common.by import By
from selenium.webdriver.chrome.service import Service
from webdriver_manager.chrome import ChromeDriverManager
import time

driver = webdriver.Chrome(service=Service(ChromeDriverManager().install()))
driver.get('http://myapp.example.com/login')
driver.maximize_window()

# Enter credentials
driver.find_element(By.ID, 'username').send_keys('testuser')
driver.find_element(By.ID, 'password').send_keys('testpass')
driver.find_element(By.XPATH, '//button[@type="submit"]').click()

time.sleep(2)

# Verify login success
assert 'Dashboard' in driver.title, 'Login FAILED!'
print('Login test PASSED')

driver.quit()
```

---

## 🧪 Step 3: Run Selenium Test Suite with Pytest

Organize tests with pytest for better reporting:

```
IT APPLICATION ENGINEER — Hands-On Lab Guide Application Deployment | Support | Security | Databases | Automation
IT Application Engineer Lab Guide | Confidential Page 18
```

```bash
pip install pytest

# Run all tests
pytest test_login.py -v

# Run with HTML report
pip install pytest-html
pytest test_login.py -v --html=report.html
```

---

# 🌐 TASK 5.2: API Testing with curl and Python Requests

---

## 🔄 Step 1: Test APIs with curl

curl is the quickest way to test REST APIs:

```bash
# GET request
curl -X GET https://api.myapp.com/users -H 'Authorization: Bearer '

# POST request with JSON body
curl -X POST https://api.myapp.com/users \
-H 'Content-Type: application/json' \
-H 'Authorization: Bearer ' \
-d '{"name": "Vinod", "email": "vinod@example.com"}'

# PUT request (update)
curl -X PUT https://api.myapp.com/users/1 \
-H 'Content-Type: application/json' \
-d '{"name": "Vinod M"}'

# DELETE request
curl -X DELETE https://api.myapp.com/users/1 -v
```

---

## 🐍 Step 2: Test APIs using Python Requests Library

Write reusable API tests in Python:

```python
import requests

BASE_URL = 'https://api.myapp.com'
HEADERS = {'Authorization': 'Bearer ', 'Content-Type': 'application/json'}

# GET — list users
resp = requests.get(f'{BASE_URL}/users', headers=HEADERS)
print(resp.status_code, resp.json())

# POST — create user
payload = {'name': 'Vinod', 'email': 'vinod@example.com'}
resp = requests.post(f'{BASE_URL}/users', json=payload, headers=HEADERS)
assert resp.status_code == 201, f'Expected 201, got {resp.status_code}'
print('User created:', resp.json())
```

---

## 📊 Step 3: Check HTTP Response Codes

Understand common response codes during API troubleshooting:

| Code | Meaning      | Action                                   |
| ---- | ------------ | ---------------------------------------- |
| 200  | OK           | Success — All good                       |
| 201  | Created      | Resource created — POST succeeded        |
| 400  | Bad Request  | Invalid request body — Check JSON fields |
| 401  | Unauthorized | Auth failed — Check token/credentials    |
| 403  | Forbidden    | Access denied — Check permissions        |
| 404  | Not Found    | Resource missing — Check endpoint URL    |
| 500  | Server Error | App crashed — Check server logs          |
| 503  | Unavailable  | Service down — Check app status/restart  |

---

```
IT APPLICATION ENGINEER — Hands-On Lab Guide Application Deployment | Support | Security | Databases | Automation
IT Application Engineer Lab Guide | Confidential Page 19
```

---

# 🖧 TASK 5.3: Networking Basics — IPs, Ports & Troubleshooting

---

## 🛠️ Step 1: Key Networking Commands for App Support

Essential commands every application engineer must know:

```bash
# Check IP address of server
ip addr show
hostname -I

# Check which ports are listening
ss -tlnp
netstat -tlnp

# Check if specific port is reachable
telnet 8080
nc -zv 8080 # Better than telnet

# DNS lookup
nslookup myapp.example.com
dig myapp.example.com

# Trace network path to server
traceroute myapp.example.com

# Check firewall rules (iptables)
sudo iptables -L -n
sudo ufw status verbose
```

---

## 🔌 Step 2: Common Application Ports Reference

Memorize these standard application ports:

| Port | Protocol/Service | Port  | Protocol/Service |
| ---- | ---------------- | ----- | ---------------- |
| 22   | SSH              | 3306  | MySQL            |
| 80   | HTTP             | 5432  | PostgreSQL       |
| 443  | HTTPS            | 1521  | Oracle DB        |
| 8080 | Tomcat HTTP      | 27017 | MongoDB          |
| 8443 | Tomcat HTTPS     | 6379  | Redis            |
| 25   | SMTP             | 9200  | Elasticsearch    |
| 21   | FTP              | 2181  | Zookeeper        |
| 389  | LDAP             | 9092  | Kafka            |



---

# ⚡ BEST PRACTICE TIPS

* BEST PRACTICE TIPS

* Use explicit waits in Selenium (`WebDriverWait`) instead of `time.sleep()` for reliable tests.
* Maintain a Postman/Insomnia collection for all application API endpoints and share it with the team.
* Always test APIs with invalid inputs to ensure proper error handling (400/401/403).
* Use environment variables for API tokens and secrets; never hardcode credentials in scripts.
* Document all automation test results with timestamps and attach them to deployment tickets.
* For BrowserStack, test on the browser/OS combinations used by your actual end users.
* Run port checks (`nc -zv`) after every firewall change to confirm connectivity is intact.
* Label automation test scripts by sprint/version to help trace regressions quickly.

