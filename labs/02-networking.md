# Lab 2 — Networking

## Objective
Explore Incus networking by creating multiple containers, verifying their connectivity, and testing internet access.

## What You Will Learn

- How Incus networking works (bridge, DHCP)
- Container-to-container communication
- Network diagnostics inside containers
- DNS resolution
- Port exposure via proxy devices

## Prerequisites

- Lab 1 completed
- Incus initialised with default network (`incusbr0`)
- Debian WSL running

## Step 1

### Command

[WSL Debian]
```bash
incus launch images:debian/13 net01
incus launch images:debian/13 net02
incus launch images:alpine/3.22 net03
```

### What happened?

- Three containers launched on the same network (`incusbr0`)
- Each gets an IP address via DHCP
- They can communicate with each other and the internet

## Step 2

### Command

[WSL Debian]
```bash
incus list
```

### What happened?

- Shows all three containers with their IP addresses
- Example output:
```
+-------+---------+-----------+------+-----------+-----------+
| NAME  |  STATE  |   IPV4    | IPV6 |   TYPE    | SNAPSHOTS |
+-------+---------+-----------+------+-----------+-----------+
| net01 | RUNNING | 10.10.10.2|      | CONTAINER | 0         |
| net02 | RUNNING | 10.10.10.3|      | CONTAINER | 0         |
| net03 | RUNNING | 10.10.10.4|      | CONTAINER | 0         |
+-------+---------+-----------+------+-----------+-----------+
```

Note the IP addresses for the next steps.

## Step 3

### Command

[WSL Debian]
```bash
incus network list
incus network show incusbr0
```

### What happened?

- Shows the default network bridge `incusbr0`
- Displays IPv4 subnet (e.g., `10.10.10.1/24`)
- Shows DHCP is enabled, DNS is configured

## Step 4

### Command

[Incus net01 container]
```bash
ip addr
ip route
```

### What happened?

- `ip addr`: Shows the container's network interface (eth0) with its IP
- `ip route`: Shows routing table — default gateway is the bridge IP

The container's default gateway is typically `10.10.10.1` (the bridge).

## Step 5

### Command

[Incus net01 container]
```bash
ping -c 3 10.10.10.3
```

### What happened?

- Tests connectivity from net01 to net02
- Should succeed (all on same bridge)
- `-c 3` limits to 3 pings

Try pinging net03's IP too:
[Incus net01 container]
```bash
ping -c 3 10.10.10.4
```

## Step 6

### Command

[Incus net02 container]
```bash
ping -c 3 10.10.10.2
```

### What happened?

- Tests connectivity from net02 to net01
- Should succeed (bidirectional)

## Step 7

### Command

[Incus net01 container]
```bash
ping -c 3 8.8.8.8
```

### What happened?

- Tests internet connectivity from inside a container
- Should succeed (NAT through host)

If this fails, check:
- Container has an IP
- Default gateway is set
- DNS works (see next step)

## Step 8

### Command

[Incus net01 container]
```bash
cat /etc/resolv.conf
```

### What happened?

- Shows DNS configuration
- Typically points to the bridge IP (`10.10.10.1`) or a DNS provided by Incus

Test DNS resolution:
[Incus net01 container]
```bash
ping -c 3 google.com
```

## Step 9

### Command

[Incus net03 container]
```bash
apk add iproute2
ip addr
```

### What happened?

- Alpine uses `apk` instead of `apt`
- `iproute2` provides `ip` command (may be pre-installed)
- Verifies Alpine container networking works similarly

## Step 10

### Command

[WSL Debian]
```bash
incus exec net01 -- ss -tulpn
incus exec net02 -- ss -tulpn
```

### What happened?

- Shows listening ports on each container
- Initially there should be none (no services running)

## Step 11

### Command

[Incus net01 container]
```bash
apt update && apt install nginx -y
nginx
```

### What happened?

- Installs nginx web server
- Starts nginx manually (since systemd may not be available)

## Step 12

### Command

[WSL Debian]
```bash
curl http://10.10.10.2
```

### What happened?

- From WSL host, access net01's nginx using its IP
- Should return nginx default HTML page

## Step 13

### Command

[WSL Debian]
```bash
incus config device add net01 httpproxy proxy listen=tcp:0.0.0.0:8080 connect=tcp:10.10.10.2:80
```

### What happened?

- Creates a proxy device that forwards:
  - Host port 8080 → Container port 80
  - Allows access from Windows browser at `http://localhost:8080`

Test from WSL:
[WSL Debian]
```bash
curl http://localhost:8080
```

Test from Windows browser:
```
http://localhost:8080
```

## Step 14

### Command

[WSL Debian]
```bash
incus config device remove net01 httpproxy
```

### What happened?

- Removes the proxy device
- Port 8080 no longer forwards to container

## Step 15

### Command

[WSL Debian]
```bash
incus delete --force net01 net02 net03
```

### What happened?

- Cleans up all containers created in this lab

## Expected Result

- Three containers created on the same network
- Container-to-container ping works
- Internet access from containers works
- DNS resolution works
- Proxy device exposes container service to host

## Questions

1. What network bridge connects the containers?
2. What is the default gateway IP for the containers?
3. How does DHCP work in Incus?
4. Why can Windows access `localhost:8080` but not the container IP directly?
5. What is the difference between a bridge network and a macvlan network?

## Challenge

- Create a second bridge network: `incus network create mybridge --config ipv4.address=192.168.100.1/24`
- Attach one container to it and test connectivity
- Try connecting two containers to different bridges — can they communicate?

## Cleanup

[WSL Debian]
```bash
# Delete containers if not already removed:
incus delete --force net01 net02 net03

# Optional: remove custom network if created:
incus network delete mybridge
```

All lab resources are cleaned up.