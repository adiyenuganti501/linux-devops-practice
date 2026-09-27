# 👤 Linux Users & Groups

## 👤 Users

|Command|Purpose|Example|
|---|---|---|
|`useradd`|Create a new user|`sudo useradd adi`|
|`passwd`|Set or change a user's password|`sudo passwd adi`|
|`userdel`|Delete a user|`sudo userdel adi`|

### Examples

```bash
# Create a user
sudo useradd adi

# Set password
sudo passwd adi

# Delete a user
sudo userdel adi
```

---

## 👥 Groups

|Command|Purpose|Example|
|---|---|---|
|`groupadd`|Create a new group|`sudo groupadd devops`|
|`groupdel`|Delete a group|`sudo groupdel devops`|

### Examples

```bash
# Create a group
sudo groupadd devops

# Delete a group
sudo groupdel devops
```

---

## 🔗 User–Group Association

### Add a user to a group

```bash
sudo usermod -aG devops adi
```

**Meaning:**

```text
-a → Append (preserve existing group memberships)
-G → Specify supplementary group
devops → Group name
adi → Username
```

### Remove a user from a group

```bash
sudo gpasswd -d adi devops
```

**Meaning:**

```text
-d → Delete user from group
adi → Username
devops → Group name
```

---

## 🔍 Verify User & Group Information

### Check current user

```bash
whoami
```

### Check user's groups

```bash
groups adi
```

### Detailed user/group information

```bash
id adi
```

Example:

```text
uid=1001(adi) gid=1001(adi) groups=1001(adi),1002(devops)
```

---

## 🧠 Quick Cheat Sheet

```text
USER
useradd       → Create user
passwd        → Set/change password
userdel       → Delete user

GROUP
groupadd      → Create group
groupdel      → Delete group

USER ↔ GROUP
usermod -aG   → Add user to group
gpasswd -d    → Remove user from group

VERIFY
whoami        → Current user
groups        → User's groups
id            → UID, GID & group info
```