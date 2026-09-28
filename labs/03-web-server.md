# Lab 3 — Web Server

## Objective
Set up a web server inside an Incus container and expose it from the host.

## What You Will Learn

- Installing and running a web server (Nginx) in a container
- Testing web access from WSL and Windows
- Understanding service management in containers (systemd vs manual start)
- Configuring proxy devices for external access

## Prerequisites

- Lab 2 completed
- Incus with networking working
- Basic Linux terminal usage

## Step 1

### Command

[WSL Debian]
```bash
incus launch images:debian/13 web01
```

### What happened?

- Creates a Debian container named `web01`
- Downloads Debian image if needed
- Starts container with IP on incusbr0 network

## Step 2

### Command

[WSL Debian]
```bash
incus list
```

### What happened?

- Shows the container with its IP address
- Example: `web01` has IP `10.10.10.2`

Remember the IP for later steps.

## Step 3

### Command

[WSL Debian]
```bash
incus exec web01 -- bash
```

### What happened?

- Enters the container as root
- Prompt changes to: `root@web01:~#`

## Step 4

### Command

[Incus web01 container]
```bash
apt update
apt install nginx -y
```

### What happened?

- Updates package lists
- Installs Nginx web server

## Step 5

### Command

[Incus web01 container]
```bash
nginx
```

### What happened?

- Starts Nginx web server
- Runs in the foreground (background)
- Press `Ctrl+C` to stop later

## Step 6

### Command

[Incus web01 container]
```bash
nginx -v
```

### What happened?

- Shows Nginx version

## Step 7

### Command

[Incus web01 container]
```bash
ss -tulpn
```

### What happened?

- Shows network sockets
- Look for `LISTEN 0.0.0.0:80`
- Port 80 is where Nginx listens

## Step 8

### Command

[Incus web01 container]
```bash
pkill nginx
```

### What happened?

- Stops Nginx (Ctrl+C also works)

## Step 9

### Command

[WSL Debian]
```bash
incus restart web01
```

### What happened?

- Restarts the container
- When container starts, Nginx is not started automatically

## Step 10

### Command

[WSL Debian]
```bash
incus exec web01 -- bash
```

### What happened?

- Starts another shell inside the container

## Step 11

### Command

[Incus web01 container]
```bash
# Navigate to Nginx web root
cd /var/www/html
# Create a custom HTML page
cat > index.html << 'EOF'
<!DOCTYPE html>
<html>
<head>
    <title>My Incus Web Server</title>
    <style>
        body { background-color: #f0f8ff; font-family: Arial; }
        h1 { color: #0066cc; }
        .container { max-width: 800px; margin: 50px auto; }
    </style>
</head>
<body>
    <div class="container">
        <h1>Welcome to My Incus Linux Lab!</h1>
        <p>This web server is running inside an Incus container.</p>
        <p>Lab: Web Server Setup</p>
        <p>Server time: $(date)</p>
    </div>
</body>
</html>
EOF
```

### What happened?

- Creates a custom HTML page

## Step 12

### Command

[Incus web01 container]
```bash
nginx
```

### What happened?

- Restarts Nginx with the new HTML page

## Step 13

### Command

[Incus web01 container]
```bash
cat /var/www/html/index.html
```

### What happened?

- Shows the HTML content

## Step 14

### Command

[WSL Debian]
```bash
curl http://10.10.10.2
```

### What happened?

- From WSL host, accesses the container's web server
- Returns the HTML page

## Step 15

### Command

[WSL Debian]
```bash
incus config device add web01 httpproxy proxy listen=tcp:0.0.0.0:8080 connect=tcp:10.10.10.2:80
```

### What happened?

- Creates a proxy device to forward port 8080 on the host to port 80 on the container

## Step 16

### Command

[WSL Debian]
```bash
curl http://localhost:8080
```

### What happened?

- From WSL, uses `localhost:8080` (proxy) to access the container

Test from Windows browser:
```
http://localhost:8080
```

## Step 17

### Command

[WSL Debian]
```bash
incus config device remove web01 httpproxy
```

### What happened?

- Removes the proxy device

## Step 18

### Command

[WSL Debian]
⚠️ **Warning: This deletes the container and all its data.**
```bash
incus delete --force web01
```

### What happened?

- Removes the container and its web server files

## Expected Result

- Nginx web server running in a container
- Custom HTML page accessible
- Web access from WSL and Windows (via proxy)

## Questions

1. What port does Nginx listen on inside the container?
2. What is the difference between using the container IP vs proxy for access?
3. Why does `nginx` not start automatically when the container starts?
4. What happens if you don't have systemd in the container?
5. Why is port 8080 used for the proxy instead of 80?

## Challenge

- Try using systemd instead: enable nginx with `systemctl` if possible
- Try adding a second page: `cat >> index.html` with more content
- Try exposing a different port (e.g., 8081)

## Cleanup

[WSL Debian]
```bash
# Container already deleted
# If you created other files in WSL:
rm -f *.html *.sh
```

All container data is automatically removed.