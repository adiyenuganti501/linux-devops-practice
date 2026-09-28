# SSH Password Authentication

## 1. Create a Linux User

Create the user `adi`:

```bash
sudo useradd adi
```

Verify the user:

```bash
id adi
```

## 2. Set Password for the User

Set a password for `adi`:

```bash
sudo passwd adi
```

Enter the password when prompted.

Verify the user information:

```bash
grep '^adi:' /etc/passwd
```

## 3. Configure SSH Password Authentication

SSH server configuration file:

```bash
/etc/ssh/sshd_config
```

Open the configuration file:

```bash
sudo vi /etc/ssh/sshd_config
```

Enable password authentication:

```text
PasswordAuthentication yes
```

## 4. Restart SSH Service

On Amazon Linux / RHEL-based systems:

```bash
sudo systemctl restart sshd
```

Check the SSH service:

```bash
sudo systemctl status sshd
```

The service should show:

```text
Active: active (running)
```

## 5. Login as the New User

From another machine:

```bash
ssh adi@<SERVER-IP>
```

Enter the password created using:

```bash
sudo passwd adi
```

Verify the logged-in user:

```bash
whoami
```

Expected output:

```text
adi
```

## Authentication Type

This is called:

**SSH Password-Based Authentication**

The authentication flow is:

```text
Client
   |
   | ssh adi@SERVER-IP
   |
   v
SSH Server (sshd)
   |
   | Password Authentication
   |
   v
User adi
   |
   v
Login successful
```

## Important SSH Files

SSH server configuration:

```text
/etc/ssh/sshd_config
```

User account information:

```text
/etc/passwd
```

User password information:

```text
/etc/shadow
```

## Commands Summary

```bash
sudo useradd adi
sudo passwd adi

sudo vi /etc/ssh/sshd_config

# Set:
PasswordAuthentication yes

sudo systemctl restart sshd
sudo systemctl status sshd

ssh adi@<SERVER-IP>

whoami
```