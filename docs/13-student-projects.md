# Chapter 13 — Student Projects

This chapter provides progressively harder projects. Each project follows the lab structure: objective, prerequisites, tasks, commands, expected result, questions, and extension exercise.

## Project 1 — Personal Website Server

### Objective
Run a personal website using Nginx inside a container and access it from WSL/Windows.

### Prerequisites
- Completed Chapters 1–8
- Incus installed and initialised
- Debian container

### Tasks

1. Launch a Debian container:
[WSL Debian]
```bash
incus launch images:debian/13 webserver
```

2. Install Nginx:
[Incus webserver container]
```bash
apt update && apt install nginx -y
```

3. Start Nginx (if systemd unavailable):
[Incus webserver container]
```bash
nginx
```

4. Find the container IP:
[WSL Debian]
```bash
incus list
```

5. Test from WSL:
[WSL Debian]
```bash
curl http://$(incus list webserver -c ipv4.address -f csv)
```

6. Create a custom page:
[Incus webserver container]
```bash
cat > /var/www/html/index.html << 'EOF'
<!DOCTYPE html>
<html>
<head><title>My Lab</title></head>
<body><h1>Welcome to My Linux Lab</h1></body>
</html>
EOF
```

7. Access from Windows browser (if port forwarding configured):
```
http://localhost:8080
```

### Expected Result
Custom webpage accessible via WSL `curl` or Windows browser.

### Questions
1. What IP address did your container get?
2. Why can't Windows access `localhost` directly?
3. What is `incusbr0`?

### Challenge
Add a second container running a different service and configure port forwarding for both.

### Cleanup
[WSL Debian]
```bash
incus delete --force webserver
```

---

## Project 2 — Multi-Distribution Networking Lab

### Objective
Create containers running different distributions and test connectivity between them.

### Prerequisites
- Chapter 6 (multiple distributions)
- Chapter 7 (networking)

### Tasks

1. Launch three containers:
[WSL Debian]
```bash
incus launch images:debian/13 net-debian
incus launch images:ubuntu/24.04 net-ubuntu
incus launch images:alpine/3.22 net-alpine
```

2. Get IPs:
[WSL Debian]
```bash
incus list
```

3. Test connectivity between all pairs:
[Incus net-debian container]
```bash
ping -c 3 10.10.10.11
ping -c 3 10.10.10.12
```

4. Check DNS from Alpine:
[Incus net-alpine container]
```bash
apk add busybox-extras
nslookup google.com
```

5. Test internet access from each container.

### Expected Result
All containers can ping each other and access the internet.

### Questions
1. Are all containers on the same subnet?
2. What happens if you remove a container's network device?
3. How does DHCP work in this setup?

### Challenge
Create a second bridge network and connect only two of the containers to it. Test if those two can communicate on a different subnet.

### Cleanup
[WSL Debian]
```bash
incus delete --force net-debian net-ubuntu net-alpine
```

---

## Project 3 — Programming Environment with Git

### Objective
Set up a container with a programming language and Git for version control.

### Prerequisites
- Chapter 9 (programming lab)

### Tasks

1. Launch a container:
[WSL Debian]
```bash
incus launch images:debian/13 prog-lab
```

2. Enter and install:
[Incus prog-lab container]
```bash
apt update && apt install python3 git -y
```

3. Create a project:
[Incus prog-lab container]
```bash
mkdir ~/project && cd ~/project
git init
cat > main.py << 'EOF'
#!/usr/bin/env python3
import sys
print(f"Python {sys.version}")
print("Hello from my project!")
EOF
chmod +x main.py
python3 main.py
```

4. Git workflow:
[Incus prog-lab container]
```bash
git add main.py
git config user.name "Student"
git config user.email "student@lab.local"
git commit -m "Add main.py"
git log --oneline
```

5. Test from another container:
[WSL Debian]
```bash
incus launch images:debian/13 prog-viewer
incus exec prog-viewer -- bash -c "apt install python3 -y && incus file pull prog-lab/home/user/project/main.py /tmp/main.py && python3 /tmp/main.py"
```

### Expected Result
Python runs, Git tracks changes, and another container can access the project files.

### Questions
1. Why use containers for programming instead of installing on the host?
2. How does `git commit` differ from `git push`?
3. What is the difference between `apt` and `pip`?

### Challenge
Add a requirements.txt file, install dependencies with pip, and create a second branch in Git.

### Cleanup
[WSL Debian]
```bash
incus delete --force prog-lab prog-viewer
```

---

## Project 4 — Container Comparison Lab

### Objective
Compare how different distributions handle the same task.

### Prerequisites
- Chapter 6 (multiple distributions)

### Tasks

For each distribution (Debian, Ubuntu, Alpine):

1. Launch container:
[WSL Debian]
```bash
incus launch images:debian/13 cmp-debian
incus launch images:ubuntu/24.04 cmp-ubuntu
incus launch images:alpine/3.22 cmp-alpine
```

2. Install nginx and check version:
[Incus cmp-debian container]
```bash
apt update && apt install nginx -y
nginx -v
```

[Incus cmp-ubuntu container]
```bash
apt update && apt install nginx -y
nginx -v
```

[Incus cmp-alpine container]
```bash
apk add nginx
nginx -v
```

3. Compare filesystem size:
[WSL Debian]
```bash
incus info cmp-debian | grep -i size
incus info cmp-ubuntu | grep -i size
incus info cmp-alpine | grep -i size
```

4. Compare process counts:
[WSL Debian]
```bash
incus exec cmp-debian -- ps aux | wc -l
incus exec cmp-ubuntu -- ps aux | wc -l
incus exec cmp-alpine -- ps aux | wc -l
```

### Expected Result
Each distribution installs Nginx differently. Alpine is smallest; Debian and Ubuntu are similar.

### Questions
1. Which distribution used the least disk space?
2. Which has the fewest running processes?
3. Why does Alpine use `apk` instead of `apt`?

### Challenge
Write a script that runs the same set of commands on all three containers and compares the output.

### Cleanup
[WSL Debian]
```bash
incus delete --force cmp-debian cmp-ubuntu cmp-alpine
```

---

## Project 5 — Basic Security Monitoring Lab

### Objective
Monitor container activity and learn basic security concepts.

### Prerequisites
- Chapter 3 (Linux fundamentals)
- Chapter 5 (first container)

### Tasks

1. Launch a container:
[WSL Debian]
```bash
incus launch images:debian/13 sec-lab
```

2. Install monitoring tools:
[Incus sec-lab container]
```bash
apt update && apt install procps net-tools iproute2 -y
```

3. Monitor processes:
[Incus sec-lab container]
```bash
ps aux
```

[Incus sec-lab container]
```bash
top -bn1 | head -20
```

4. Check listening ports:
[Incus sec-lab container]
```bash
ss -tulpn
```

5. Check user accounts:
[Incus sec-lab container]
```bash
cat /etc/passwd
cat /etc/shadow
groups
id
```

6. Check file permissions:
[Incus sec-lab container]
```bash
ls -la /etc/passwd /etc/shadow /etc/ssh/sshd_config 2>/dev/null
ls -la /tmp/
```

7. Check logs:
[Incus sec-lab container]
```bash
journalctl -n 20 2>/dev/null || cat /var/log/syslog 2>/dev/null | tail -20
```

### Expected Result
You can see running processes, network listeners, user accounts, and file permissions.

### Questions
1. What processes should always be running?
2. Which files should NOT be world-readable?
3. What does `ss -tulpn` tell you?

### Challenge
Set up a simple firewall rule using `iptables` or `nftables` inside the container.

### Cleanup
[WSL Debian]
```bash
incus delete --force sec-lab
```

---

## Learning Progression

```
Level 1: Single container, basic commands
Level 2: Multiple containers, networking
Level 3: Programming environment, Git
Level 4: Comparison and automation
Level 5: Security and monitoring
```

## Next Step

Continue to [Chapter 14 — Troubleshooting](14-troubleshooting.md) for common issues and solutions.

## Tested With

> **Tested with:**
> - Debian 13 containers
> - Ubuntu 24.04 containers
> - Alpine 3.22 containers
> - Incus 6.x
>
> Image availability may vary. Check `incus image list images:` before starting.