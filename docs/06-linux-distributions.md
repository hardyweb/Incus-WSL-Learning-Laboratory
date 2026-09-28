# Chapter 6 — Working with Multiple Linux Distributions

## Why Multiple Distributions?

Different distributions teach different concepts:

- **Debian** — stable, general-purpose learning
- **Ubuntu** — popular in servers and cloud
- **Alpine** — minimal, security-focused, different package manager
- **Arch Linux** — rolling release, teaches pacman and yay, deep understanding of Linux

Each teaches different package managers, filesystem layouts, and conventions.

## Launch Multiple Containers

[WSL Debian]
```bash
incus launch images:debian/13 debian01
incus launch images:ubuntu/24.04 ubuntu01
incus launch images:alpine/3.22 alpine01
incus launch images:archlinux arch01
```

### Verify
[WSL Debian]
```bash
incus list
```

Expected output:
```
+----------+---------+-----------+------+-----------+-----------+
| NAME     | STATE   | IPV4      | IPV6 | TYPE      | SNAPSHOTS |
+----------+---------+-----------+------+-----------+-----------+
| debian01 | RUNNING | 10.x.x.x  |      | CONTAINER | 0         |
| ubuntu01 | RUNNING | 10.x.x.x  |      | CONTAINER | 0         |
| alpine01 | RUNNING | 10.x.x.x  |      | CONTAINER | 0         |
| arch01   | RUNNING | 10.x.x.x  |      | CONTAINER | 0         |
+----------+---------+-----------+------+-----------+-----------+
```

## Compare the Distributions

### Debian Container
[WSL Debian]
```bash
incus exec debian01 -- cat /etc/os-release
```

[WSL Debian]
```bash
incus exec debian01 -- apt list --installed | head -20
```

### Ubuntu Container
[WSL Debian]
```bash
incus exec ubuntu01 -- cat /etc/os-release
```

[WSL Debian]
```bash
incus exec ubuntu01 -- apt list --installed | head -20
```

### Alpine Container
[WSL Debian]
```bash
incus exec alpine01 -- cat /etc/os-release
```

[WSL Debian]
```bash
incus exec alpine01 -- apk list --installed | head -20
```

### Arch Linux Container
[WSL Debian]
```bash
incus exec arch01 -- cat /etc/os-release
```

[WSL Debian]
```bash
incus exec arch01 -- pacman -Q | head -20
```

| Distribution | Package Manager | Init System | Typical Learning Use |
| ------------ | --------------- | ----------- | --------------------- |
| Debian       | `apt`           | systemd     | General Linux        |
| Ubuntu       | `apt`           | systemd     | Desktop/server ecosystem |
| Alpine       | `apk`           | OpenRC (or none) | Minimal, security, containers |
| Arch Linux   | `pacman`, `yay` | systemd     | Rolling release, deep Linux understanding |

## Arch Linux: pacman and yay

Arch Linux uses a different philosophy: simplicity, user control, and a rolling release model.

### pacman — Arch Package Manager

Basic commands:

[WSL Debian]
```bash
# Search packages
incus exec arch01 -- pacman -Ss nginx

# Install package
incus exec arch01 -- pacman -S nginx

# Update system
incus exec arch01 -- pacman -Syu

# Remove package
incus exec arch01 -- pacman -R nginx

# List installed packages
incus exec arch01 -- pacman -Q

# Count installed packages
incus exec arch01 -- pacman -Q | wc -l
```

pacman options:
- `-S` — install (sync)
- `-R` — remove
- `-Syu` — sync + upgrade all packages
- `-Ss` — search
- `-Q` — query (list installed)

### yay — Yet Another Yogurt (AUR Helper)

Arch has the **Arch User Repository (AUR)** with packages not in the official repos. `yay` is an AUR helper that builds and installs AUR packages.

> **Note:** Installing `yay` requires the `base-devel` group and git first.

[WSL Debian]
```bash
# Install base-devel tools
incus exec arch01 -- pacman -S --needed base-devel git

# Install yay from AUR (using pacman to bootstrap, then yay itself)
incus exec arch01 -- pacman -S --needed yay
```

Or build yay manually:
[WSL Debian]
```bash
incus exec arch01 -- bash -c "git clone https://aur.archlinux.org/yay.git /tmp/yay && cd /tmp/yay && makepkg -si"
```

Using yay:
[WSL Debian]
```bash
# Install from AUR
incus exec arch01 -- yay -S package-name

# Update AUR packages
incus exec arch01 -- yay -Syu
```

### Arch vs Debian Package Management Comparison

| Task | Debian/Ubuntu | Alpine | Arch Linux |
|------|---------------|--------|------------|
| Update package list | `apt update` | `apk update` | `pacman -Sy` |
| Install package | `apt install pkg` | `apk add pkg` | `pacman -S pkg` |
| Upgrade all | `apt upgrade` | `apk upgrade` | `pacman -Syu` |
| Remove package | `apt remove pkg` | `apk del pkg` | `pacman -R pkg` |
| Search | `apt search term` | `apk search term` | `pacman -Ss term` |
| List installed | `apt list --installed` | `apk list --installed` | `pacman -Q` |
| AUR packages | N/A | N/A | `yay -S pkg` |

## Key Differences to Observe

### Package Manager

Debian/Ubuntu use `apt`:
```bash
apt install nginx
apt update
apt upgrade
```

Alpine uses `apk`:
```bash
apk add nginx
apk update
apk upgrade
```

Arch Linux uses `pacman`:
```bash
pacman -Syu
pacman -S nginx
pacman -R nginx
pacman -Ss nginx
pacman -Q
```

### User Management

Debian/Ubuntu use `adduser`/`useradd`:
```bash
adduser student
```

Alpine also has `adduser`:
```bash
adduser student
```

Arch Linux uses `useradd` (more manual):
```bash
useradd -m student
passwd student
```

### Filesystem Layout

Mostly the same (FHS — Filesystem Hierarchy Standard), but Alpine is minimal:

| Directory | Debian/Ubuntu | Alpine | Arch Linux |
|-----------|---------------|--------|------------|
| `/bin` | Core binaries | Symlink to `/usr/bin` | Core binaries |
| `/sbin` | System binaries | Symlink to `/usr/sbin` | System binaries |
| `/lib` | Libraries | Symlink to `/usr/lib` | Libraries |

### init / PID 1

Debian/Ubuntu use **systemd**:
```bash
ps aux | head -5
# PID 1 = /usr/bin/python3 /usr/bin/upstart
# or /lib/systemd/systemd
```

Alpine uses **OpenRC** or no init system:
```bash
ps aux | head -5
# PID 1 = /sbin/init (OpenRC)
```

Arch Linux uses **systemd** (same as Debian/Ubuntu):
```bash
ps aux | head -5
# PID 1 = /usr/lib/systemd/systemd
```

## List Available Images

[WSL Debian]
```bash
incus image list images:
```

Filter by alias:
[WSL Debian]
```bash
incus image list images: | grep debian
incus image list images: | grep ubuntu
incus image list images: | grep archlinux
```

## Useful Multi-Container Commands

### Execute the Same Command on Multiple Containers
[WSL Debian]
```bash
for c in debian01 ubuntu01 alpine01 arch01; do echo "=== $c ==="; incus exec $c -- hostname; done
```

### Stop All Containers
[WSL Debian]
```bash
incus stop --all
```

### Delete All Containers
[WSL Debian]
```bash
incus delete --force --all
```

## Cleanup

Remove all containers from this chapter:
[WSL Debian]
```bash
incus stop debian01 ubuntu01 alpine01 arch01
incus delete --force debian01 ubuntu01 alpine01 arch01
```

## Exercise: Distribution Comparison

1. [WSL Debian] Launch Debian, Ubuntu, Alpine, and Arch containers
2. [WSL Debian] `incus list` — verify all four are running
3. [WSL Debian] `incus exec debian01 -- cat /etc/os-release`
4. [WSL Debian] `incus exec ubuntu01 -- cat /etc/os-release`
5. [WSL Debian] `incus exec alpine01 -- cat /etc/os-release`
6. [WSL Debian] `incus exec arch01 -- cat /etc/os-release`
7. [WSL Debian] `incus exec debian01 -- ip addr` — compare with Ubuntu, Alpine, and Arch
8. [WSL Debian] `incus exec debian01 -- apt list | wc -l` — count packages (Debian)
9. [WSL Debian] `incus exec alpine01 -- apk list | wc -l` — count packages (Alpine)
10. [WSL Debian] `incus exec arch01 -- pacman -Q | wc -l` — count packages (Arch)
11. [WSL Debian] Compare the size of each container: `incus info debian01 | grep -A5 Size`
12. [WSL Debian] Delete all four containers

> **Tip:** Arch Linux containers are typically smaller than Debian/Ubuntu because Arch installs only what you choose, with no default GUI or extra tools.

## Important Notes

> ⚠️ **Version awareness:**
> - Image names and versions change over time
> - Verify availability with `incus image list images:`
> - If `images:debian/13` is not available, try `images:debian/12` or `images:debian/13`
> - For Arch Linux, use `images:archlinux` (no version suffix — it's a rolling release)
> - Check the official image server for current versions

> **Tip:** Always check `incus image list images:` before writing a command that references a specific image version.

## Next Step

Continue to [Chapter 7 — Incus Networking](07-incus-networking.md) to learn about container networking.