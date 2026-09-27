# 📊 Linux System Monitoring Commands

## 🖥️ CPU

```bash
top
```

```bash
htop
```

**Use:** Check CPU usage, CPU load, running processes, and system activity.

---

## 🧠 Memory

```bash
free -h
```

**Use:** Check RAM usage, available memory, used memory, and swap.

---

## 💾 Disk Usage

### Check filesystem disk usage

```bash
df -h
```

**Use:** Shows disk space used and available on mounted filesystems.

### Check directory/file size

```bash
du -sh <directory>
```

Example:

```bash
du -sh /var/log
```

### Check disks and partitions

```bash
lsblk
```

**Use:** Displays available disks, partitions, sizes, and mount points.

---

## ⚙️ Process

### List running processes

```bash
ps -ef
```

**Use:** Display running processes with details such as PID, PPID, user, and command.

### Find a process

```bash
ps -ef | grep nginx
```

or:

```bash
pgrep nginx
```

---

## 🔴 Kill a Process

### Gracefully terminate a process

```bash
kill <PID>
```

### Force kill a process

```bash
kill -9 <PID>
```

> ⚠️ Use `kill -9` only when the process does not terminate normally.

---

# 🧠 CMDP Memory Trick

```text
C → CPU      → top / htop
M → Memory   → free -h
D → Disk     → df -h / du -sh / lsblk
P → Process  → ps -ef / pgrep
```

---

# ⭐ Most Important Commands

```bash
top              # CPU + Memory + Processes
free -h          # Memory
df -h            # Disk usage
du -sh           # Directory/file size
lsblk            # Disks and partitions
ps -ef           # Running processes
pgrep <name>     # Find process PID
kill <PID>       # Terminate process
kill -9 <PID>    # Force kill process
```

## 🔥 Quick Revision

```text
CPU      → top
Memory   → free -h
Disk     → df -h
Filesize → du -sh
Disks    → lsblk
Process  → ps -ef
Kill     → kill <PID>
```