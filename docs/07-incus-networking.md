# Chapter 7 — Incus Networking

## How Incus Networking Works

Incus creates a **virtual network bridge** (`incusbr0`) inside WSL2. This bridge acts like a physical network switch.

Containers connect to this bridge and get IP addresses via DHCP.

```
Windows 11
   │
   └── WSL2 (Debian host)
        │
        └── Incus
             │
             └── incusbr0 (virtual bridge)
                  │
          ┌───────┴───────┐
          │               │
      debian01       ubuntu01
     10.x.x.x       10.x.x.x
```

## List Networks

[WSL Debian]
```bash
incus network list
```

Expected output:
```
+----------+----------+---------+-------------+---------+---------+
|   NAME   |   TYPE   | MANAGED | DESCRIPTION | USED BY |   STATUS  |
+----------+----------+---------+-------------+---------+---------+
| incusbr0 | bridge   | YES     |             |    1    | UP      |
+----------+----------+---------+-------------+---------+---------+
```

## Show Network Details

[WSL Debian]
```bash
incus network show incusbr0
```

Look for:
- `config` — DHCP, IPv4/IPv6 subnets, DNS settings
- `used_by` — which containers use this network
- `status` — UP or DOWN

## Find Container IP Addresses

[WSL Debian]
```bash
incus list
```

The `IPV4` column shows each container's IP address.

Or get specific info:
[WSL Debian]
```bash
incus info debian01 | grep eth0
```

## Test Connectivity Inside Containers

### Check the Container's IP
[Incus Debian container]
```bash
ip addr
```

### Check Routing
[Incus Debian container]
```bash
ip route
```

### Test Container-to-Container Communication

First, find IPs:
[WSL Debian]
```bash
incus list
```

Then ping from one container to another:
[Incus Debian container]
```bash
ping -c 4 10.10.10.11
```
(Replace `10.10.10.11` with the actual IP of another container)

### Test Internet Access
[Incus Debian container]
```bash
ping -c 4 8.8.8.8
```

[Incus Debian container]
```bash
ping -c 4 google.com
```

### Check DNS
[Incus Debian container]
```bash
cat /etc/resolv.conf
```

```bash
nslookup google.com
```

## Networking Configuration

### Default Network Settings

The `incusbr0` bridge typically has:
- IPv4 subnet: `10.x.x.0/24` (Incus picks automatically)
- DHCP: enabled
- DNS: enabled

### Change Network Settings

[WSL Debian]
```bash
incus network set incusbr0 ipv4.address=10.10.10.1/24
incus network set incusbr0 ipv6.address=none
```

### Add a New Network

[WSL Debian]
```bash
incus network create mynetwork --type bridge --config ipv4.address=192.168.100.1/24
```

### Attach Container to a Network

When launching, specify the network:
[WSL Debian]
```bash
incus launch images:debian/13 debian01 -c default
```

Or edit after creation:
[WSL Debian]
```bash
incus config device add debian01 eth0 nic network=incusbr0 name=eth0 nictype=bridged
```

### Detach a Network Device

[WSL Debian]
```bash
incus config device remove debian01 eth0
```

## Understanding the Network Stack

```
Container eth0
    │
    └── incusbr0 (bridge)
         │
         └── WSL2 virtual network (NAT)
              │
              └── Windows networking
```

The container sees `incusbr0` as its default gateway. Incus handles NAT to Windows.

## Port Forwarding

Expose a service from a container to WSL/Windows:

[WSL Debian]
```bash
incus config device add debian01 httpproxy proxy listen=tcp:0.0.0.0:8080 connect=tcp:10.10.10.10:80
```

Then from Windows or WSL:
```
http://localhost:8080
```

## Exercise: Network Exploration

1. [WSL Debian] `incus network list`
2. [WSL Debian] `incus network show incusbr0`
3. [WSL Debian] Launch `incus launch images:debian/13 net01`
4. [WSL Debian] Launch `incus launch images:debian/13 net02`
5. [WSL Debian] `incus list` — note both IPs
6. [Incus net01] `ip addr` — check its IP
7. [Incus net01] `ping -c 3 10.x.x.x` (replace with net02's IP)
8. [Incus net01] `ping -c 3 8.8.8.8`
9. [Incus net01] `cat /etc/resolv.conf`
10. [WSL Debian] `incus delete --force net01 net02`

## Common Networking Issues

### Container Has No IP
Wait a few seconds. Restart if needed:
[WSL Debian]
```bash
incus restart container-name
```

### Cannot Ping Another Container
Check they are on the same network (both attached to `incusbr0`):
[WSL Debian]
```bash
incus config device list container-name
```

### No Internet from Container
Check DHCP and routing:
[Incus Debian container]
```bash
ip addr
ip route
cat /etc/resolv.conf
```

### Firewall Blocking Traffic
If you have a host firewall, ensure the `incusbr0` bridge is allowed.

## Next Step

Continue to [Chapter 8 — Running Services](08-services.md) to run a web server inside a container.