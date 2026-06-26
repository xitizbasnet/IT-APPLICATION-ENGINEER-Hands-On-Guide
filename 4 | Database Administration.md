# 📘 4 | Database Administration

---

## 🧭 Lab Objective

* Deploy and administer PostgreSQL, MySQL, Oracle, and NoSQL databases
* Execute SQL and PL/SQL queries for data management and troubleshooting
* Perform database health checks and basic DBA tasks
* Understand database connectivity from application servers

---

# 🧩 TASK 4.1: PostgreSQL Installation & Administration

---

## 🟦 Step 1: Install PostgreSQL on Linux

```bash id="p1d9aa"
sudo apt install -y postgresql postgresql-contrib
sudo systemctl start postgresql
sudo systemctl enable postgresql
sudo systemctl status postgresql
```

---

## 🟦 Step 2: Create Database & User

```bash id="p2d8bb"
sudo -u postgres psql
```

### 📄 Inside psql shell

```sql id="p3d7cc"
CREATE DATABASE myapp_db;
CREATE USER myapp_user WITH ENCRYPTED PASSWORD 'SecureP@ss123';
GRANT ALL PRIVILEGES ON DATABASE myapp_db TO myapp_user;
\q
```

---

## 🟦 Step 3: Common PostgreSQL Admin Commands

```sql id="p4d6dd"
\l          -- List databases
\c myapp_db -- Connect to database
\dt         -- List tables
\d table_name
\du         -- List users/roles
\timing     -- Enable query timing
```

### 📊 Database Health Queries

```sql id="p5d5ee"
SELECT pg_size_pretty(pg_database_size('myapp_db'));

SELECT pid, usename, application_name, state
FROM pg_stat_activity;
```

### 📦 Table Size Check

```sql id="p6d4ff"
SELECT relname,
       pg_size_pretty(pg_total_relation_size(relid))
FROM pg_catalog.pg_statio_user_tables
ORDER BY pg_total_relation_size(relid) DESC;
```

---

## 🟦 Step 4: Backup & Restore PostgreSQL

```bash id="p7d3gg"
pg_dump -U myapp_user -h localhost myapp_db > /backup/myapp_db_$(date +%Y%m%d).sql
pg_dumpall -U postgres > /backup/all_databases.sql
```

```bash id="p8d2hh"
psql -U myapp_user -h localhost myapp_db < /backup/myapp_db_20240101.sql
```

---

# 🧩 TASK 4.2: MySQL Administration

---

## 🟨 Step 1: Install MySQL

```bash id="m1y9aa"
sudo apt install -y mysql-server
sudo mysql_secure_installation
sudo systemctl start mysql
```

---

## 🟨 Step 2: User & Database Management

```bash id="m2y8bb"
mysql -u root -p
```

### 📄 MySQL Commands

```sql id="m3y7cc"
CREATE DATABASE myapp_db CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;

CREATE USER 'myapp_user'@'%' IDENTIFIED BY 'SecureP@ss123';

GRANT ALL PRIVILEGES ON myapp_db.* TO 'myapp_user'@'%';

FLUSH PRIVILEGES;

SHOW DATABASES;
SHOW TABLES FROM myapp_db;

SHOW PROCESSLIST;
```

---

## 🟨 Step 3: MySQL Backup & Restore

```bash id="m4y6dd"
mysqldump -u myapp_user -p myapp_db > /backup/myapp_db_$(date +%Y%m%d).sql
```

```bash id="m5y5ee"
mysql -u myapp_user -p myapp_db < /backup/myapp_db_20240101.sql
```

---

# 🧩 TASK 4.3: SQL & PL/SQL for Support

---

## 🟪 Step 1: Essential SQL Queries

```sql id="s1q9aa"
SELECT * FROM transactions
WHERE created_at BETWEEN '2024-01-01' AND '2024-01-31';
```

```sql id="s2q8bb"
SELECT status, COUNT(*) AS total
FROM orders
GROUP BY status;
```

```sql id="s3q7cc"
SELECT email, COUNT(*)
FROM users
GROUP BY email
HAVING COUNT(*) > 1;
```

```sql id="s4q6dd"
UPDATE orders
SET status = 'PROCESSED'
WHERE order_id = 12345;
```

```sql id="s5q5ee"
SELECT u.name, o.order_id, o.amount
FROM users u
INNER JOIN orders o ON u.user_id = o.user_id
WHERE o.status = 'PENDING';
```

---

## 🟪 Step 2: PL/SQL Block (Oracle)

```sql id="o1p9aa"
DECLARE
    v_count NUMBER;
BEGIN
    SELECT COUNT(*) INTO v_count FROM orders WHERE status = 'FAILED';

    IF v_count > 0 THEN
        DBMS_OUTPUT.PUT_LINE('ALERT: ' || v_count || ' failed orders found!');
    ELSE
        DBMS_OUTPUT.PUT_LINE('All orders OK.');
    END IF;
END;
/
```

---

# 🧩 TASK 4.4: NoSQL — MongoDB Basics

---

## 🟩 Step 1: Install MongoDB

```bash id="n1m9aa"
sudo apt install -y gnupg curl

curl -fsSL https://www.mongodb.org/static/pgp/server-7.0.asc | \
sudo gpg -o /usr/share/keyrings/mongodb-server-7.0.gpg --dearmor

echo 'deb [ signed-by=/usr/share/keyrings/mongodb-server-7.0.gpg ] \
https://repo.mongodb.org/apt/ubuntu jammy/mongodb-org/7.0 multiverse' | \
sudo tee /etc/apt/sources.list.d/mongodb-org-7.0.list

sudo apt update && sudo apt install -y mongodb-org
sudo systemctl start mongod
```

---

## 🟩 Step 2: MongoDB Operations

```javascript id="n2m8bb"
mongosh

use myapp_db

db.users.insertOne({ name: 'Vinod', role: 'Engineer', active: true })

db.users.find().pretty()

db.users.find({ active: true })

db.users.updateOne(
  { name: 'Vinod' },
  { $set: { role: 'Senior Engineer' } }
)

db.users.deleteOne({ name: 'Vinod' })

db.users.countDocuments()
```

---

# ⭐ BEST PRACTICE TIPS

* Never run `UPDATE` or `DELETE` without `WHERE` clause in production
* Always take DB backup before schema changes
* Use `EXPLAIN / EXPLAIN ANALYZE` before heavy queries
* Never use root/admin DB users for application connections
* Monitor slow query logs regularly
* Index frequently used columns for performance
* Rotate DB credentials using secrets manager (Vault / AWS SSM)
* Always validate DB connectivity after infrastructure changes

---

## 📌 Notes

This lab covers enterprise database administration across relational and NoSQL systems, focusing on installation, query management, backup strategies, troubleshooting, and production-grade best practices for application support environments.
