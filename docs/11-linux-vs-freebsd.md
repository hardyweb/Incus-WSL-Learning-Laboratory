# Chapter 11 — Linux vs FreeBSD

## Comparison Table

| Area | Linux | FreeBSD |
| --- | --- | --- |
| Kernel | Linux kernel | FreeBSD kernel |
| Userland | GNU + other tools | FreeBSD base system |
| Package manager | apt/dnf/pacman/etc. | `pkg` / `ports` |
| Service management | systemd / OpenRC / sysvinit | `rc.d` |
| Containers | namespaces/cgroups | jails |
| Filesystem options | ext4, XFS, Btrfs, ZFS | UFS, ZFS |
| Networking tools | `ip`, `ss`, `nmcli`, `dig` | `ifconfig`, `sockstat`, `ping` |
| Kernel config | Modular (can customize) | Monolithic (standardized) |
| Update model | Per-package | Base system + ports/packages |
| Ports tree | N/A | Ports (/usr/ports) |
| Boot system | GRUB / systemd-boot | `boot0` / UEFI loader |

## Kernel and Userland

### Linux
```
Linux kernel + GNU userland + third-party tools
```

The Linux ecosystem is assembled from different projects (kernel, GNU tools, systemd, etc.). Different distributions combine these in different ways.

### FreeBSD
```
FreeBSD kernel + FreeBSD userland = single source tree
```

The kernel and core utilities (ls, cp, cat, etc.) come from the same project. This is called the **base system**.

## Package Management

### Linux (Debian/Ubuntu)

[Incus Debian container]
```bash
sudo apt install nginx
```

Packages are managed per distribution repository.

### FreeBSD

[FreeBSD]
```bash
pkg install nginx
```

FreeBSD has:
- **`pkg`** — binary packages (fast, pre-compiled)
- **Ports** — compile from source with customizations (/usr/ports)

FreeBSD's base system (kernel + core tools) is a single unit updated separately from packages.

## Service Management

### Linux (systemd in Debian)

[Incus Debian container]
```bash
systemctl start nginx
systemctl enable nginx
systemctl status nginx
journalctl -u nginx -f
```

### FreeBSD (rc.d)

[FreeBSD]
```bash
sysrc nginx_enable=YES
service nginx start
service nginx status
tail -f /var/log/nginx/error.log
```

Key difference: FreeBSD uses `rc.d` scripts (simple shell scripts) while systemd uses unit files and has more features (dependencies, cgroups, logging).

## Network Tools

### Linux

[Incus Debian container]
```bash
ip addr        # show IP addresses
ip route       # show routes
ss -tulpn      # show listening ports
```

### FreeBSD

[FreeBSD]
```bash
ifconfig              # show IP addresses
netstat -rn           # show routes
sockstat -4 -l        # show listening ports
```

## Filesystem Layout

### Linux

```
/bin, /etc, /home, /lib, /proc, /usr, /var, /dev
```

### FreeBSD

```
/bin, /sbin, /etc, /home, /usr, /var, /proc, /dev, /mnt, /tmp
```

Notable difference:
- FreeBSD puts more tools in `/usr/local` (installed packages go there)
- FreeBSD's `rc.d` scripts are in `/etc/rc.d/` (base) and `/usr/local/etc/rc.d/` (packages)

## Philosophy Differences

### Linux: Freedom of Choice
- Many distributions
- Different tools for the same task
- Highly customizable
- Community and corporate driven
- Rapid innovation

### FreeBSD: Cohesion and Stability
- Single integrated project
- One way to do things (base system)
- Emphasis on correctness and stability
- University/community driven
- Conservative, tested changes

## Boot Process

### Linux (systemd-based)
1. UEFI/BIOS → GRUB/systemd-boot
2. systemd (PID 1)
3. systemd targets (equivalent to runlevels)
4. Services started via systemctl

### FreeBSD
1. UEFI/BIOS → loader (boot1.efi / /boot/loader)
2. Kernel initialization
3. `rc.d` scripts (in order defined by rcorder)
4. Services started via `/etc/rc.conf`

## Containers vs Jails

### Linux Containers
```
Host kernel + Namespaces + Cgroups + Container runtime
```
Containers share the host kernel. Only Linux distributions can run as containers on a Linux host.

### FreeBSD Jails
```
Host kernel + jail + chroot + network isolation
```
Jails are like lightweight VMs that share the FreeBSD kernel. Only FreeBSD can run as a jail on a FreeBSD host.

## Why Both Matter

**Linux** dominates:
- 90%+ of cloud servers
- Most web hosting
- DevOps and containerization
- Android, embedded systems

**FreeBSD** is strong in:
- Network appliances and firewalls
- Storage appliances (FreeNAS/TrueNAS)
- Security-focused deployments
- Infrastructure with ZFS

Learning both makes you a more flexible systems administrator.

## Exercise: Compare Environments

1. [WSL Debian] `uname -a`
2. [Incus Linux container] `cat /etc/os-release`
3. [Incus Linux container] `systemctl --version` (or note if unavailable)
4. [FreeBSD VM, if available] `uname -a`
5. [FreeBSD VM] `ifconfig`
6. Compare networking, service management, and filesystem layout

## Next Step

Continue to [Chapter 12 — Snapshots and Recovery](12-snapshots.md) to learn about protecting your work.

## Tested With

> **Tested with:**
> - Debian 13 (Linux container)
> - FreeBSD 14.x (VM, if available)
> - Incus 6.x
>
> If a FreeBSD VM is not available in your environment, compare with documentation and continue with Linux containers.