<p align="center">
  <img src="https://img.shields.io/badge/Score-125%2F100-brightgreen?style=for-the-badge" />
  <img src="https://img.shields.io/badge/OS-Debian_12-A81D33?style=for-the-badge&logo=debian&logoColor=white" />
  <img src="https://img.shields.io/badge/42_Network-000000?style=for-the-badge&logo=42&logoColor=white" />
  <img src="https://img.shields.io/badge/VirtualBox-183A61?style=for-the-badge&logo=virtualbox&logoColor=white" />
</p>

<h1 align="center">🖥️ Born2beRoot</h1>

<p align="center">
  <i>This project aims to introduce you to the wonderful world of virtualization.</i>
  <br><br>
  A system administration project where you set up a <b>Debian virtual machine</b><br>
  with strict security policies, encrypted partitions, and automated monitoring —<br>
  learning the foundations of Linux server hardening from the ground up.
</p>

---

## 📋 Table of Contents

- [About](#-about)
- [Why Debian?](#-why-debian)
- [Partition Layout (LVM)](#-partition-layout-lvm)
- [Security Configuration](#-security-configuration)
- [Service Setup](#-service-setup)
- [Monitoring Script](#-monitoring-script)
- [Bonus: WordPress Stack](#-bonus-wordpress-stack)
- [Evaluation Checklist](#-evaluation-checklist)
- [Quick Command Reference](#-quick-command-reference)
- [Troubleshooting](#-troubleshooting)
- [Author](#-author)

---

## 💡 About

**Born2beRoot** is a 42 School project that introduces system administration through hands-on VM configuration. You must set up a Debian server with:

- **Encrypted LVM partitions** for disk security
- **Strict sudo policies** with logging
- **Strong password policies** via PAM
- **SSH on port 4242** (no root login)
- **UFW firewall** with only necessary ports open
- **Automated monitoring** via cron every 10 minutes

> No GUI. No graphical environment. Pure command-line administration.

---

## 🐧 Why Debian?

| Factor | Debian |
|:-------|:-------|
| **Stability** | Rock-solid release cycle |
| **Package Manager** | `apt` — intuitive and well-documented |
| **Community** | Massive, active, beginner-friendly |
| **Security** | AppArmor enabled by default |
| **42 Recommended** | Officially recommended for newcomers |

---

## 💾 Partition Layout (LVM)

The project requires **encrypted partitions using LVM**. Here is the layout matching the subject requirements:

```
┌────────────────────────────────────────────────────────────┐
│                     DISK: /dev/sda (30G)                   │
├────────────────────────────────────────────────────────────┤
│                                                            │
│  sda1 (500MB) ── /boot ── Primary, ext4                    │
│                                                            │
│  sda5 (remaining) ── Encrypted (LUKS)                      │
│    └── LVMGroup (Volume Group)                             │
│         ├── root      (10G)  → /           ext4            │
│         ├── swap      (2.3G) → [SWAP]                      │
│         ├── home      (5G)   → /home       ext4            │
│         ├── var       (3G)   → /var        ext4            │
│         ├── srv       (3G)   → /srv        ext4            │
│         ├── tmp       (3G)   → /tmp        ext4            │
│         └── var--log  (4G)   → /var/log    ext4            │
│                                                            │
└────────────────────────────────────────────────────────────┘
```

### Verify Partitions

```bash
lsblk
```

---

## 🔐 Security Configuration

### Sudo Policies

File: `/etc/sudoers.d/sudo_config`

```
Defaults     passwd_tries=3
Defaults     badpass_message="Wrong password. Try again."
Defaults     logfile="/var/log/sudo/sudo.log"
Defaults     log_input, log_output
Defaults     iolog_dir="/var/log/sudo"
Defaults     requiretty
Defaults     secure_path="/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/snap/bin"
```

| Policy | Effect |
|:-------|:-------|
| `passwd_tries=3` | Max 3 password attempts |
| `logfile` | All sudo commands logged |
| `log_input/output` | Full I/O logging |
| `requiretty` | TTY required for sudo |
| `secure_path` | Restricted PATH for sudo commands |

### Password Policies

File: `/etc/login.defs`

```
PASS_MAX_DAYS   30      # Password expires after 30 days
PASS_MIN_DAYS   2       # Minimum 2 days between changes
PASS_WARN_AGE   7       # Warning 7 days before expiry
```

File: `/etc/pam.d/common-password`

```
password requisite pam_pwquality.so retry=3 minlen=10 ucredit=-1 dcredit=-1 lcredit=-1 maxrepeat=3 reject_username difok=7 enforce_for_root
```

| Rule | Meaning |
|:-----|:--------|
| `minlen=10` | Minimum 10 characters |
| `ucredit=-1` | At least 1 uppercase letter |
| `dcredit=-1` | At least 1 digit |
| `lcredit=-1` | At least 1 lowercase letter |
| `maxrepeat=3` | Max 3 consecutive identical chars |
| `reject_username` | Cannot contain username |
| `difok=7` | Must differ by 7 chars from old password |
| `enforce_for_root` | Applies to root too |

---

## 🛠️ Service Setup

### SSH Configuration

File: `/etc/ssh/sshd_config`

```
Port 4242                 # Custom port
PermitRootLogin no        # Root cannot SSH in
```

```bash
# Verify SSH status
sudo service ssh status

# Connect from host
ssh username@localhost -p 4242
```

### UFW Firewall

```bash
# Install and enable
sudo apt install ufw
sudo ufw enable

# Allow only port 4242
sudo ufw allow 4242

# Verify
sudo ufw status
```

```
Status: active

To                         Action      From
--                         ------      ----
4242                       ALLOW       Anywhere
4242 (v6)                  ALLOW       Anywhere (v6)
```

### User & Group Management

```bash
# Create user42 group
sudo addgroup user42

# Add user to groups
sudo adduser <username> user42
sudo adduser <username> sudo

# Verify
getent group user42
getent group sudo
```

---

## 📊 Monitoring Script

The `monitoring.sh` script broadcasts system information to all terminals every 10 minutes via cron.

### Script Content

```bash
#!/bin/bash

# Architecture
arch=$(uname -a)

# CPU
pcpu=$(grep "physical id" /proc/cpuinfo | sort -u | wc -l)
vcpu=$(grep "^processor" /proc/cpuinfo | wc -l)

# RAM
ram_total=$(free -m | awk '$1 == "Mem:" {print $2}')
ram_used=$(free -m | awk '$1 == "Mem:" {print $3}')
ram_perc=$(free -m | awk '$1 == "Mem:" {printf("%.2f"), $3/$2*100}')

# Disk
disk_total=$(df -BG | grep '^/dev/' | grep -v '/boot$' | awk '{ft += $2} END {print ft}')
disk_used=$(df -BM | grep '^/dev/' | grep -v '/boot$' | awk '{ut += $3} END {print ut}')
disk_perc=$(df -BM | grep '^/dev/' | grep -v '/boot$' | awk '{ut += $3} {ft += $2} END {printf("%d"), ut/ft*100}')

# CPU load
cpul=$(vmstat 1 2 | tail -1 | awk '{printf "%.1f%%", 100 - $15}')

# Last boot
lb=$(who -b | awk '$1 == "system" {print $3 " " $4}')

# LVM
lvm=$(if [ $(lsblk | grep "lvm" | wc -l) -gt 0 ]; then echo yes; else echo no; fi)

# TCP connections
tcp=$(ss -ta | grep ESTAB | wc -l)

# Users logged in
ulog=$(users | wc -w)

# IP & MAC
ip=$(hostname -I)
mac=$(ip link | grep "ether" | awk '{print $2}')

# Sudo commands
cmds=$(journalctl _COMM=sudo | grep COMMAND | wc -l)

wall "   #Architecture: $arch
   #CPU physical: $pcpu
   #vCPU: $vcpu
   #Memory Usage: $ram_used/${ram_total}MB ($ram_perc%)
   #Disk Usage: $disk_used/${disk_total}Gb ($disk_perc%)
   #CPU load: $cpul
   #Last boot: $lb
   #LVM use: $lvm
   #Connections TCP: $tcp ESTABLISHED
   #User log: $ulog
   #Network: IP $ip ($mac)
   #Sudo: $cmds cmd"
```

### Cron Setup

```bash
sudo crontab -u root -e
# Add this line:
*/10 * * * * /usr/local/bin/monitoring.sh
```

### Example Output

```
   #Architecture: Linux hostname 6.1.0-18-amd64 #1 SMP x86_64 GNU/Linux
   #CPU physical: 1
   #vCPU: 2
   #Memory Usage: 287/1024MB (28.03%)
   #Disk Usage: 2145/30Gb (7%)
   #CPU load: 3.2%
   #Last boot: 2024-11-15 10:42
   #LVM use: yes
   #Connections TCP: 1 ESTABLISHED
   #User log: 2
   #Network: IP 10.0.2.15 (08:00:27:xx:xx:xx)
   #Sudo: 42 cmd
```

---

## 🌐 Bonus: WordPress Stack

### Components

| Service | Purpose |
|:--------|:--------|
| **Lighttpd** | Lightweight web server |
| **MariaDB** | MySQL-compatible database |
| **PHP** | Server-side scripting |
| **WordPress** | CMS platform |

### Installation Summary

```bash
# Lighttpd
sudo apt install lighttpd
sudo ufw allow 80

# MariaDB
sudo apt install mariadb-server
sudo mysql_secure_installation

# PHP
sudo apt install php-cgi php-mysql

# WordPress
cd /var/www/html
sudo wget https://wordpress.org/latest.tar.gz
sudo tar -xzvf latest.tar.gz
```

---

## ✅ Evaluation Checklist

<details>
<summary><b>Click to expand full checklist</b></summary>

### General

- [ ] VM runs on VirtualBox (or UTM)
- [ ] No graphical interface installed
- [ ] Debian chosen as OS
- [ ] Encrypted partitions visible with `lsblk`

### Users & Groups

- [ ] User `<login>` exists
- [ ] User belongs to `user42` and `sudo` groups
- [ ] Can create new user and assign groups
- [ ] Password policy is enforced

### Sudo

- [ ] `sudo` is installed
- [ ] New user can be added to sudo group
- [ ] `/var/log/sudo/` contains logs
- [ ] TTY mode enabled
- [ ] Paths restricted

### UFW

- [ ] UFW is installed and active
- [ ] Only port 4242 is open
- [ ] Can add and remove rules

### SSH

- [ ] SSH running on port 4242
- [ ] Cannot SSH as root
- [ ] Can SSH with normal user

### Monitoring Script

- [ ] Script displays all required information
- [ ] Runs every 10 minutes via cron
- [ ] Can stop cron without editing script

### Bonus

- [ ] WordPress functional on Lighttpd
- [ ] Additional service configured and justified

</details>

---

## 📖 Quick Command Reference

```bash
# Check OS
uname -a

# Check partitions
lsblk

# Check AppArmor
sudo aa-status

# Check SSH
sudo service ssh status

# Check UFW
sudo ufw status

# Check sudo logs
ls -la /var/log/sudo/

# Check groups
getent group sudo
getent group user42

# Check password policy
sudo chage -l <username>

# Check cron
sudo crontab -u root -l

# Check hostname
hostnamectl

# Change hostname
sudo hostnamectl set-hostname <new_hostname>
sudo reboot
```

---

## ❓ Troubleshooting

| Issue | Solution |
|:------|:---------|
| SSH connection refused | Check `sudo service ssh status`, verify port 4242 |
| UFW blocking connection | `sudo ufw allow 4242` |
| Can't sudo | Verify user in sudo group: `groups <user>` |
| Password rejected | Check PAM config in `/etc/pam.d/common-password` |
| Cron not running | `sudo crontab -u root -l` to verify |
| SSH host key changed | Delete old key: `ssh-keygen -R [localhost]:4242` |
| Port forwarding issue | VirtualBox → Settings → Network → Port Forwarding |

---

## 👤 Author

**Adil Bourji** — [@adi7-x](https://github.com/adi7-x)

<p align="center">
  <a href="https://github.com/adi7-x"><img src="https://img.shields.io/badge/GitHub-adi7--x-181717?style=flat-square&logo=github" /></a>
  <a href="https://linkedin.com/in/adil-bourji"><img src="https://img.shields.io/badge/LinkedIn-Adil_Bourji-0A66C2?style=flat-square&logo=linkedin" /></a>
</p>

<p align="center"><sub>42 School · Common Core · System Administration</sub></p>
