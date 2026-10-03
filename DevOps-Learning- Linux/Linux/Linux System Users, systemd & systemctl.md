	# 1. System User

A **system user** is a Linux account created primarily to run a service or application rather than for interactive human login.

### Why do we need system users?

Main reason:

> **Security — Principle of Least Privilege**

Instead of running an application as `root`, we run it as a dedicated user.

Example:

```text
nginx → nginx user
mysql → mysql user
prometheus → prometheus user
myapp → myapp user
```

If the application is compromised, the attacker has the permissions of the application user instead of full root privileges.

---

## 2. Create a System User

```bash
sudo useradd --system myapp
```

Short form:

```bash
sudo useradd -r myapp
```

Recommended service account example:

```bash
sudo useradd --system --no-create-home --shell /sbin/nologin myapp
```

Check:

```bash
id myapp
```

```bash
getent passwd myapp
```

```bash
grep myapp /etc/passwd
```

---

## 3. `/sbin/nologin`

Service accounts normally do not need interactive login.

Example:

```text
myapp:x:997:997::/home/myapp:/sbin/nologin
```

`/sbin/nologin` prevents normal interactive login for that account.

This is commonly used for service accounts.

---

# 4. Normal User vs System User

|Feature|Normal User|System User|
|---|---|---|
|Purpose|Human|Service/application|
|Example|adi|nginx|
|Interactive login|Usually yes|Usually no|
|Password|Usually configured|Usually unnecessary|
|Used by services|Sometimes|Commonly|
|Home directory|Usually|Often not required|

A system user is still a real Linux user account.

---

# 5. systemd

`systemd` is the system and service manager used by many modern Linux distributions.

It manages:

- Services
    
- Processes
    
- Service dependencies
    
- Startup
    
- Shutdown
    
- Logging integration
    
- Service restart behavior
    

Architecture:

```text
Linux
  |
  └── systemd
        |
        ├── nginx.service
        ├── sshd.service
        ├── docker.service
        └── myapp.service
```

---

# 6. systemctl

`systemctl` is the command-line tool used to communicate with and control `systemd`.

Think:

```text
systemd = service manager

systemctl = command used to control systemd
```

---

# 7. Important systemctl Commands

### Start

```bash
sudo systemctl start nginx
```

Starts the service immediately.

### Stop

```bash
sudo systemctl stop nginx
```

Stops the service.

### Restart

```bash
sudo systemctl restart nginx
```

Stops and starts the service again.

### Reload

```bash
sudo systemctl reload nginx
```

Reloads configuration without completely restarting the service, if supported.

### Status

```bash
systemctl status nginx
```

Shows service state, recent logs and process information.

### Enable

```bash
sudo systemctl enable nginx
```

Configures the service to start automatically during boot.

### Disable

```bash
sudo systemctl disable nginx
```

Prevents automatic startup during boot.

### Enable + Start

```bash
sudo systemctl enable --now nginx
```

Enables the service at boot and starts it immediately.

---

# 8. Start vs Enable

Very important:

```text
start
  ↓
Start NOW
```

```text
enable
  ↓
Start automatically during BOOT
```

They are different operations.

---

# 9. systemd Service File

A systemd service is normally defined using a unit file:

```text
myapp.service
```

Common custom-service location:

```text
/etc/systemd/system/
```

Example:

```text
/etc/systemd/system/myapp.service
```


- What the service does
    
- Which user runs it
    
- Which command starts it
    
- When it should start
    
- What to do if it fails
    

---

# 10. Service File Structure

Example:

```ini
[Unit]
Description=My Custom Application
After=network.target

[Service]
Type=simple
User=myapp
ExecStart=/usr/local/bin/myapp.sh
Restart=always

[Install]
WantedBy=multi-user.target
```

---

# 11. `[Unit]`

```ini
[Unit]
Description=My Custom Application
After=network.target
```

### Description

```ini
Description=My Custom Application
```

Human-readable description.

### After

```ini
After=network.target
```

Controls startup ordering.

Meaning:

> Start this service after `network.target`.

Important:

`After=` controls **ordering**. It does not by itself necessarily pull the other unit into the transaction.

---

# 12. `[Service]`

```ini
[Service]
Type=simple
User=myapp
ExecStart=/usr/local/bin/myapp.sh
Restart=always
```

## Type

```ini
Type=simple
```

Defines how systemd should consider the service started.

## User

```ini
User=myapp
```

Runs the application as the `myapp` Linux user.

This avoids unnecessarily running the application as root.

## ExecStart

```ini
ExecStart=/usr/local/bin/myapp.sh
```

Command executed when the service starts.

Examples:

```ini
ExecStart=/usr/bin/java -jar /opt/myapp/app.jar
```

```ini
ExecStart=/usr/bin/python3 /opt/myapp/app.py
```

## Restart

```ini
Restart=always
```

Tells systemd to restart the service when the process exits.

Common values:

```text
no
on-success
on-failure
always
```

---

# 13. `[Install]`

```ini
[Install]
WantedBy=multi-user.target
```

Defines how the service participates in enabling/startup.

When we run:

```bash
sudo systemctl enable myapp
```

systemd uses the install relationship.

---

# 14. Complete Custom Service Example

Create system user:

```bash
sudo useradd --system --no-create-home --shell /sbin/nologin myapp
```

Create application:

```bash
sudo vi /usr/local/bin/myapp.sh
```

Content:

```bash
#!/bin/bash

while true
do
    echo "My application is running"
    sleep 10
done
```

Make executable:

```bash
sudo chmod +x /usr/local/bin/myapp.sh
```

Create service:

```bash
sudo vi /etc/systemd/system/myapp.service
```

Content:

```ini
[Unit]
Description=My Custom Application
After=network.target

[Service]
Type=simple
User=myapp
ExecStart=/usr/local/bin/myapp.sh
Restart=always

[Install]
WantedBy=multi-user.target
```

Reload systemd:

```bash
sudo systemctl daemon-reload
```

Start:

```bash
sudo systemctl start myapp
```

Check:

```bash
systemctl status myapp
```

Enable at boot:

```bash
sudo systemctl enable myapp
```

Or:

```bash
sudo systemctl enable --now myapp
```

---

# 15. Why daemon-reload?

After creating or modifying a systemd unit file:

```bash
sudo systemctl daemon-reload
```

This tells systemd:

> Re-read the unit files and their configuration.

Typical workflow:

```bash
sudo vi /etc/systemd/system/myapp.service

sudo systemctl daemon-reload

sudo systemctl restart myapp

systemctl status myapp
```

---

# 16. Troubleshooting Services

Check status:

```bash
systemctl status myapp
```

View logs:

```bash
journalctl -u myapp
```

Last 50 lines:

```bash
journalctl -u myapp -n 50
```

Follow logs:

```bash
journalctl -u myapp -f
```

Logs from current boot:

```bash
journalctl -u myapp -b
```

---

# 17. Common Service Problems

## Wrong ExecStart

```ini
ExecStart=/wrong/path/app.sh
```

Check:

```bash
ls -l /wrong/path/app.sh
```

---

## Script is not executable

```bash
ls -l /usr/local/bin/myapp.sh
```

Fix:

```bash
sudo chmod +x /usr/local/bin/myapp.sh
```

---

## Permission problem

Check:

```bash
ls -l /opt/myapp
```

If appropriate:

```bash
sudo chown -R myapp:myapp /opt/myapp
```

---

## Port already in use

```bash
sudo ss -lntp
```

or:

```bash
sudo lsof -i :8080
```

---

## Service logs

```bash
journalctl -u myapp -n 100
```

---

# 18. Important Commands Cheat Sheet

```bash
systemctl status <service>
systemctl start <service>
systemctl stop <service>
systemctl restart <service>
systemctl reload <service>
systemctl enable <service>
systemctl disable <service>
systemctl enable --now <service>

systemctl is-active <service>
systemctl is-enabled <service>

systemctl list-units --type=service
systemctl list-unit-files --type=service

sudo systemctl daemon-reload

journalctl -u <service>
journalctl -u <service> -n 50
journalctl -u <service> -f
journalctl -u <service> -b
```

---

# 19. DevOps Mental Model

```text
SYSTEM USER
    |
    | runs
    ↓
APPLICATION
    |
    | managed by
    ↓
SYSTEMD
    |
    | controlled using
    ↓
SYSTEMCTL
```

Example:

```text
myapp Linux user
       |
       ↓
myapp process
       |
       ↓
myapp.service
       |
       ↓
systemd
       |
       ↓
systemctl
```

---

# 20. Interview Questions

### Q1. Why create a system user?

To run services with a dedicated identity and limited permissions instead of running them as root. This follows the principle of least privilege.

### Q2. What is systemd?

`systemd` is a system and service manager responsible for managing services, processes, startup, dependencies and related system functionality.

### Q3. What is systemctl?

`systemctl` is the command-line utility used to manage systemd units and services.

### Q4. Difference between start and enable?

`start` starts the service immediately.

`enable` configures the service to start automatically during boot.

### Q5. Why use daemon-reload?

After creating or modifying a systemd unit file, `daemon-reload` makes systemd re-read the unit configuration.

### Q6. How do you troubleshoot a failed service?

```bash
systemctl status myapp
journalctl -u myapp
journalctl -u myapp -n 50
```

### Q7. What does User= do?

```ini
User=myapp
```

Specifies the Linux user under which the service process runs.

### Q8. What does ExecStart do?

Specifies the command that systemd executes when starting the service.

### Q9. What does Restart=always mean?

It tells systemd to restart the service when the service process exits.

### Q10. Where do custom systemd service files commonly go?

```text
/etc/systemd/system/
```

---

# ⭐ Most Important Things to Remember

```text
System user
    ↓
Dedicated account for service/application

systemd
    ↓
Service/system manager

systemctl
    ↓
Command used to control systemd

.service
    ↓
Configuration file describing a service

User=
    ↓
Which Linux user runs the service

ExecStart=
    ↓
What command to execute

Restart=
    ↓
What to do when process exits

After=
    ↓
Startup ordering

WantedBy=
    ↓
Installation/startup relationship

daemon-reload
    ↓
Re-read modified unit files
```