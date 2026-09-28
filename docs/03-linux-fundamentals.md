# Chapter 3 — Linux Fundamentals

## Processes

A **process** is a running program. Every command you run starts a process.

### View Running Processes

[WSL Debian]
```bash
ps
```
Shows processes for your current shell session.

[WSL Debian]
```bash
ps aux
```
Shows **all** processes for all users (a = all users, u = user format, x = include processes without terminal).

[WSL Debian]
```bash
ps aux | head -20
```
First 20 lines.

### Real-time Process Viewer

[WSL Debian]
```bash
top
```
Interactive process monitor. Press `q` to quit.

Better alternative (install if needed):
[WSL Debian]
```bash
htop
```

### Process Tree

[WSL Debian]
```bash
pstree
```
Shows parent-child relationships.

[WSL Debian]
```bash
pstree -p
```
Shows PIDs (Process IDs).

### Foreground and Background

Start a command in foreground (you wait for it):
[WSL Debian]
```bash
sleep 10
```

Start in background:
[WSL Debian]
```bash
sleep 10 &
```

See background jobs:
[WSL Debian]
```bash
jobs
```

Bring to foreground:
[WSL Debian]
```bash
fg %1
```

### Kill a Process

By PID:
[WSL Debian]
```bash
kill 1234
```

Force kill:
[WSL Debian]
```bash
kill -9 1234
```

By name:
[WSL Debian]
```bash
pkill sleep
```

## Services and systemd

### What is a Service?

A **service** (or daemon) is a process that runs in the background, usually started at boot.

Examples: SSH server, web server, database, cron.

### systemd — Service Manager

Most modern Linux distributions (including Debian 12+) use **systemd** to manage services.

> **Note:** WSL's systemd support varies. On Windows 11 22H2+, systemd is enabled by default. Check with `systemctl --version`.

### Service Management Commands

Check status:
[WSL Debian]
```bash
systemctl status ssh
```

Start a service:
[WSL Debian]
```bash
sudo systemctl start ssh
```

Stop a service:
[WSL Debian]
```bash
sudo systemctl stop ssh
```

Restart:
[WSL Debian]
```bash
sudo systemctl restart ssh
```

Enable (start at boot):
[WSL Debian]
```bash
sudo systemctl enable ssh
```

Disable:
[WSL Debian]
```bash
sudo systemctl disable ssh
```

List all services:
[WSL Debian]
```bash
systemctl list-units --type=service
```

### View Service Logs

[WSL Debian]
```bash
journalctl -u ssh
```

Follow logs in real-time:
[WSL Debian]
```bash
journalctl -u ssh -f
```

## Files and Permissions

### File Types

```bash
ls -l
```

First character indicates type:
- `-` — regular file
- `d` — directory
- `l` — symbolic link
- `b` — block device
- `c` — character device
- `p` — named pipe
- `s` — socket

### Permissions

Three categories:
- **Owner** (user who owns the file)
- **Group** (users in the file's group)
- **Others** (everyone else)

Three permissions per category:
- `r` — read (4)
- `w` — write (2)
- `x` — execute (1)

### Example
```bash
-rwxr-xr-- 1 user group 1234 Jan 1 12:00 script.sh
```
- Owner: `rwx` (read, write, execute)
- Group: `r-x` (read, execute)
- Others: `r--` (read only)

### Change Permissions

Symbolic:
[WSL Debian]
```bash
chmod +x script.sh       # Add execute for everyone
chmod u+x script.sh      # Add execute for owner only
chmod go-w script.sh     # Remove write from group and others
```

Numeric (octal):
[WSL Debian]
```bash
chmod 755 script.sh      # rwxr-xr-x
chmod 644 file.txt       # rw-r--r--
chmod 600 secret.txt     # rw-------
```

### Change Owner and Group

[WSL Debian]
```bash
sudo chown user:group file.txt
sudo chown -R user:group directory/   # Recursive
```

## Users and Groups

### View Current User
[WSL Debian]
```bash
whoami
id
```

`id` shows UID, GID, and all groups.

### List All Users
[WSL Debian]
```bash
cat /etc/passwd
```
Format: `username:password:UID:GID:info:home:shell`

### List All Groups
[WSL Debian]
```bash
cat /etc/group
```

### Add a User
[WSL Debian]
```bash
sudo adduser newuser
```

### Add User to Group
[WSL Debian]
```bash
sudo usermod -aG groupname username
```

The `-a` (append) is important — without it, user is removed from other groups.

### Switch User
[WSL Debian]
```bash
su - username
```

## Environment Variables

Variables that affect how processes behave.

### View All
[WSL Debian]
```bash
env
```
Or:
[WSL Debian]
```bash
printenv
```

### Common Variables

| Variable | Purpose |
|----------|---------|
| `HOME` | Your home directory |
| `USER` | Your username |
| `PATH` | Directories to search for executables |
| `SHELL` | Your default shell |
| `LANG` | Locale/language settings |
| `TERM` | Terminal type |
| `PWD` | Current directory |

### View One Variable
[WSL Debian]
```bash
echo $HOME
echo $PATH
```

### Set Temporarily (Current Shell)
[WSL Debian]
```bash
export MY_VAR="hello"
```

### Set Permanently

Add to `~/.bashrc`:
```bash
echo 'export MY_VAR="hello"' >> ~/.bashrc
source ~/.bashrc
```

### PATH — Where Commands Are Found

[WSL Debian]
```bash
echo $PATH
```
Output:
```
/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
```

When you type `ls`, the shell searches these directories in order.

### Add to PATH
[WSL Debian]
```bash
export PATH="$PATH:/new/directory"
```

## Package Management (apt)

### Search for Packages
[WSL Debian]
```bash
apt search nginx
apt search "web server"
```

### Show Package Information
[WSL Debian]
```bash
apt show nginx
```

### Install Package
[WSL Debian]
```bash
sudo apt install nginx
```

### Install Multiple Packages
[WSL Debian]
```bash
sudo apt install git curl vim htop
```

### Remove Package
[WSL Debian]
```bash
sudo apt remove nginx
```

Remove with configuration files:
[WSL Debian]
```bash
sudo apt purge nginx
```

### List Installed Packages
[WSL Debian]
```bash
apt list --installed
```

### Show Files in Package
[WSL Debian]
```bash
dpkg -L nginx
```

## Logs

### systemd Journal (systemctl/journalctl)
[WSL Debian]
```bash
journalctl
journalctl -f           # Follow
journalctl -p err       # Errors only
journalctl --since "1 hour ago"
```

### Traditional Log Files (in /var/log/)
[WSL Debian]
```bash
ls /var/log/
sudo tail -f /var/log/syslog
sudo tail -f /var/log/auth.log
```

## Networking Basics

### Show IP Addresses
[WSL Debian]
```bash
ip addr
```
Or the older command:
[WSL Debian]
```bash
ifconfig
```
(Install with `sudo apt install net-tools` if not present)

### Show Routing Table
[WSL Debian]
```bash
ip route
```

### Show Listening Ports
[WSL Debian]
```bash
ss -tulpn
```
- `-t` — TCP
- `-u` — UDP
- `-l` — listening
- `-p` — process info
- `-n` — numeric (no DNS resolution)

### Test Connectivity
[WSL Debian]
```bash
ping 8.8.8.8
ping google.com
```

### Trace Route
[WSL Debian]
```bash
traceroute 8.8.8.8
```
(Install with `sudo apt install traceroute`)

### DNS Lookup
[WSL Debian]
```bash
dig google.com
nslookup google.com
```

### Show ARP Table
[WSL Debian]
```bash
ip neigh
```

## Exercise: System Exploration

1. [WSL Debian] `ps aux | grep -E "(systemd|ssh)"`
2. [WSL Debian] `systemctl list-units --type=service --state=running`
3. [WSL Debian] `ls -l /etc/passwd /etc/shadow`
4. [WSL Debian] `id`
5. [WSL Debian] `groups`
6. [WSL Debian] `env | grep -E "(HOME|PATH|USER|SHELL)"`
7. [WSL Debian] `apt search htop && sudo apt install htop`
8. [WSL Debian] `htop` (press `q` to quit)
9. [WSL Debian] `ip addr show`
10. [WSL Debian] `ss -tulpn | head -20`
11. [WSL Debian] `journalctl -n 50`

## Next Step

Continue to [Chapter 4 — Install Incus](04-install-incus.md) to set up your container laboratory.