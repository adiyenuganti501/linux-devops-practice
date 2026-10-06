# Nginx – Service Management & Troubleshooting

## 1. Nginx Service Commands

```bash
systemctl enable nginx
```

**Use:** To enable Nginx to start automatically when the system boots.

```bash
systemctl restart nginx
```

**Use:** To restart the Nginx service.

```bash
systemctl start nginx
```

**Use:** To start the Nginx service.

```bash
systemctl status nginx
```

**Use:** To show the current status of the Nginx service.

---

## 2. Check Listening Ports

```bash
netstat -lntp
```

**Use:** Shows TCP ports that are currently listening and the processes using them.

### Options

|Option|Meaning|
|---|---|
|`-l`|Show listening ports|
|`-n`|Show IP addresses and port numbers instead of names|
|`-t`|Show TCP connections|
|`-p`|Show process/PID using the port|

For Nginx, you can check whether it is listening on:

```text
80 → HTTP
443 → HTTPS
```

---

## 3. Check Nginx Processes

```bash
ps -ef | grep nginx
```

**Use:** Displays processes related to Nginx.

You may see:

```text
nginx: master process
nginx: worker process
```

---

# 4. Check Nginx Logs

## View All Journal Logs

```bash
journalctl
```

**Use:** Displays system logs collected by `systemd-journald`.

---

## View Nginx Logs

```bash
journalctl -u nginx
```

**Use:** Shows logs related to the Nginx service.

---

## View Last 50 Nginx Logs

```bash
journalctl -u nginx -n 50
```

**Use:** Shows the last 50 Nginx log entries.

---

## View Nginx Logs in Real Time

```bash
journalctl -u nginx -f
```

**Use:** Continuously displays new Nginx log entries as they are generated.

Press:

```text
Ctrl + C
```

to stop following the logs.

---

# Nginx Troubleshooting Flow

```text
Nginx Problem
     ↓
systemctl status nginx
     ↓
journalctl -u nginx
     ↓
Check the error
     ↓
Fix the problem
     ↓
systemctl restart nginx
     ↓
systemctl status nginx
     ↓
netstat -lntp
     ↓
ps -ef | grep nginx
```

# Important Commands

```bash
systemctl enable nginx
systemctl start nginx
systemctl restart nginx
system
```