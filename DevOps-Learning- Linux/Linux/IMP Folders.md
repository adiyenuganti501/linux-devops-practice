# 📁 Important Linux Directories & Configuration Files

## ⚙️ `/etc` → Configuration

The `/etc` directory contains **system and application configuration files**.

| File / Directory       | Purpose                                                   |
| ---------------------- | --------------------------------------------------------- |
| `/etc/passwd`          | Contains information about system users                   |
| `/etc/shadow`          | Contains password hashes and password-related information |
| `/etc/group`           | Contains information about groups                         |
| `/etc/os-release`      | Contains Linux distribution and OS details                |
| `/etc/ssh/sshd_config` | SSH server configuration                                  |
| `/home/username`       | Home directory of a normal user                           |

### 🔍 Useful Commands

```bash
# View users
cat /etc/passwd

# View password-related information
sudo cat /etc/shadow

# View groups
cat /etc/group

# Check OS information
cat /etc/os-release

# Check SSH server configuration
sudo cat /etc/ssh/sshd_config

# List users' home directories
ls /home
```

---

## 📊 `/var` → Variable / Changing Data

`/var` contains data that **changes frequently while the system is running**.

Important directories:

```text
/var/log    → System and application logs
/var/lib    → Persistent application/system data
/var/cache  → Cached data
/var/tmp    → Temporary files preserved longer than /tmp
```

### 🔍 Useful Commands

```bash
# View logs
ls /var/log

# Check log directory size
du -sh /var/log

# Monitor a log in real time
tail -f /var/log/syslog
```

---

## 🧠 Quick Memory

```text
/etc        → CONFIGURATION
/var        → CHANGING DATA
/var/log    → LOGS
/home       → USER HOME DIRECTORIES
```

### ⭐ Important Files to Remember

```text
/etc/passwd              → User information
/etc/shadow              → Password hashes
/etc/group               → Group information
/etc/os-release          → OS information
/etc/ssh/sshd_config     → SSH server configuration
/home/username           → User's home directory
```