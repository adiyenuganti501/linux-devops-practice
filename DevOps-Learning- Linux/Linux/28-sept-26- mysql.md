# MySQL Installation & Configuration on Red Hat EC2

## 1. Launch EC2 Instance

Launch an EC2 instance with the following configuration:

### AMI

```text
Redhat-9-DevOps-Practice
AMI ID: ami-0220d79f3f480ecf5
```

### Default User

```bash
ec2-user
```

### Password

```text
DevOps321
```

> **Note:** The password is only relevant if password-based authentication is configured. EC2 instances normally use SSH key-based authentication.

---

# 2. Connect to the EC2 Instance

Connect using SSH:

```bash
ssh  ec2-user@<EC2-PUBLIC-IP>
```

Example:

```bash
ssh  ec2-user@13.x.x.x
```

---

# 3. Switch to Root User

```bash
sudo su -
```

### Explanation

```text
sudo → Execute command with elevated privileges
su   → Switch user
-    → Load root user's environment
```

Verify:

```bash
whoami
```

Expected:

```text
root
```

---

# 4. Install MySQL

On Red Hat 9, install MySQL using:

```bash
dnf install mysql-server -y
```

### Explanation

```text
dnf          → Package manager
install      → Install a package
mysql-server → MySQL server package
-y           → Automatically answer Yes
```

---

# 5. Enable MySQL Service

```bash
systemctl enable mysqld
```

### Purpose

Makes MySQL start automatically when the server boots.

---

# 6. Start MySQL Service

```bash
systemctl start mysqld
```

### Purpose

Starts the MySQL service immediately.

---

# 7. Check MySQL Status

```bash
systemctl status mysqld
```

Expected:

```text
Active: active (running)
```

### Short form

```bash
systemctl is-active mysqld
```

---

# 8. Check MySQL Processes

Use:

```bash
ps -ef | grep mysql
```

### Explanation

```text
ps       → Display running processes
-e       → Show all processes
-f       → Full-format listing
|        → Pipe output
grep     → Search/filter text
mysql    → Search for MySQL processes
```

Example:

```text
mysql    1234 ... /usr/libexec/mysqld
```

> **Important:** The correct command is `ps -ef`, not `pf -ef`.

---

# 9. Check MySQL Port

MySQL normally listens on:

```text
3306
```

You can check it with:

```bash
netstat -ntlp
```

If `netstat` is not installed:

```bash
ss -ntlp
```

Filter specifically for MySQL:

```bash
ss -ntlp | grep 3306
```

Example:

```text
LISTEN  0  80  0.0.0.0:3306  0.0.0.0:*
```

### Common MySQL Port

```text
3306 → MySQL
```

---

# 10. Login to MySQL

Try:

```bash
mysql
```

If authentication is required:

```bash
mysql -u root -p
```

Then enter the MySQL root password.

After successful login:

```text
mysql>
```

---

# 11. MySQL Commands

## Show Databases

```sql
SHOW DATABASES;
```

### Purpose

Lists all databases available in MySQL.

---

## Select a Database

```sql
USE database_name;
```

Example:

```sql
USE employees;
```

---

## Show Tables

```sql
SHOW TABLES;
```

### Purpose

Displays tables inside the currently selected database.

---

## Describe a Table

```sql
DESC table_name;
```

or:

```sql
DESCRIBE table_name;
```

### Purpose

Shows the table structure, columns, data types, keys, etc.

---

## Query Data

```sql
SELECT * FROM table_name;
```

Example:

```sql
SELECT * FROM employees;
```

### Explanation

```text
SELECT *     → Select all columns
FROM         → Specify the table
table_name   → Table from which data is retrieved
```

---

# 12. Basic MySQL Workflow

```text
EC2 Instance
     ↓
Red Hat 9
     ↓
Install MySQL
     ↓
dnf install mysql-server -y
     ↓
Enable MySQL
     ↓
systemctl enable mysqld
     ↓
Start MySQL
     ↓
systemctl start mysqld
     ↓
Check Status
     ↓
systemctl status mysqld
     ↓
Check Process
     ↓
ps -ef | grep mysql
     ↓
Check Port
     ↓
ss -ntlp | grep 3306
     ↓
Login
     ↓
mysql
     ↓
SHOW DATABASES;
     ↓
USE database_name;
     ↓
SHOW TABLES;
     ↓
SELECT * FROM table_name;
```

# Quick Commands

|Task|Command|
|---|---|
|Switch to root|`sudo su -`|
|Install MySQL|`dnf install mysql-server -y`|
|Enable MySQL|`systemctl enable mysqld`|
|Start MySQL|`systemctl start mysqld`|
|Check status|`systemctl status mysqld`|
|Check process|`ps -ef \| grep mysql`|
|Check ports|`ss -ntlp`|
|Check MySQL port|`ss -ntlp \| grep 3306`|
|Login|`mysql`|
|Login with user/password|`mysql -u root -p`|
|List databases|`SHOW DATABASES;`|
|Select database|`USE database_name;`|
|List tables|`SHOW TABLES;`|
|Describe table|`DESC table_name;`|
|Query table|`SELECT * FROM table_name;`|

# Important Ports

| Service |   Port |
| ------- | -----: |
| SSH     |   `22` |
| MySQL   | `3306` |
| HTTP    |   `80` |
| HTTPS   |  `443` |