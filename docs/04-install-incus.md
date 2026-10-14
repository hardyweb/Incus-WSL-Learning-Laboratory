# Chapter 4 — Install Incus

## What is Incus?

**Incus** is a system for managing **Linux containers** and **virtual machines**.

It is the community fork of **LXD** after the project diverged from Canonical's roadmap.

### Key Concepts

| Term | Meaning |
|------|---------|
| **Container** | Lightweight, isolated Linux environment sharing the host kernel |
| **Virtual Machine** | Full OS with its own kernel, running inside a hypervisor |
| **Image** | A pre-built template for a container or VM |
| **Instance** | A running container or VM |
| **Profile** | Configuration template applied to instances |
| **Snapshot** | Saved state of a container/VM that can be restored |

### Linux Containers vs Virtual Machines

| Feature | Container | Virtual Machine |
|---------|-----------|-----------------|
| Kernel | Shared with host | Has its own kernel |
| Startup | Seconds | Minutes |
| Resource usage | Low | Higher |
| Isolation | Process-level | Hardware-level |
| OS flexibility | Must be Linux | Any OS |
| Typical use | Services, apps | Full OS, BSD, Windows |

Incus supports **both** containers and VMs.

### Why Incus for Education?

- **Free and open-source**
- **Safe and isolated** — experiments don't harm the host
- **Disposable** — create, destroy, repeat
- **Snapshot support** — recover from mistakes easily
- **Multiple distributions** — Debian, Ubuntu, Alpine, and more
- **VM support** — try FreeBSD and other operating systems
- **Simple CLI** — `incus launch`, `incus exec`, `incus list`

## Prerequisites

Before installing Incus, you need:

1. WSL2 running Debian (see Chapter 1)
2. WSL2 (not WSL1)
3. Virtualization enabled on your Windows machine
4. A Debian installation where `systemd` is available or you can work without it

> **Important:** Incus requires Linux kernel features (namespaces, cgroups) that are provided by WSL2's Linux kernel.

> **VM note:** Running **virtual machines** (FreeBSD, Linux, or Windows) in Incus additionally requires the KVM device. Check with `ls -la /dev/kvm` — if it does not exist, VMs cannot run, but Linux **containers** work fine.

## Check WSL Version

[WSL Debian]
```bash
wsl --status
```
Ensure VERSION shows `2`.

If inside WSL, check kernel version:
[WSL Debian]
```bash
uname -r
```
This should show a WSL2 kernel version.

### Check VM (KVM) Support

[WSL Debian]
```bash
ls -la /dev/kvm
```

- `/dev/kvm` present → VMs are supported
- `/dev/kvm` absent → containers only (no FreeBSD/Linux/Windows VMs)

## Install Incus on Debian

> **Note on versions:** The Debian repository provides the **LTS version** of Incus (6.x). If you want the **latest Incus** (currently 7.5 at the time of writing), follow the steps below to add the official Incus repository.

### Option A: Install from Debian Repository (LTS, simpler)

[WSL Debian]
```bash
sudo apt update
sudo apt install -y incus
```

This installs the Incus version packaged for your Debian release. It is stable and well-tested, but may be an older version.

### Option B: Install from Incus Repository (latest, recommended, ) [ version 7.0 lts at this time ]

#### Step 1: Install Dependencies
[WSL Debian]
```bash
sudo apt update
sudo apt install -y curl gnupg lsb-release
```

#### Step 2: Add the Incus Repository

[WSL Debian]
```bash
sudo mkdir -p /usr/share/keyrings
curl -fsSL https://pkgs.zabbly.com/key.asc -o /etc/apt/keyrings/zabbly.asc
```
```bash
su sh -c 'cat <<EOF > /etc/apt/sources.list.d/zabbly-incus-lts-7.0.sources
Enabled: yes
Types: deb
URIs: https://pkgs.zabbly.com/incus/lts-7.0
Suites: $(. /etc/os-release && echo ${VERSION_CODENAME})
Components: main
Architectures: $(dpkg --print-architecture)
Signed-By: /etc/apt/keyrings/zabbly.asc

EOF'
```

#### Step 3: Install Incus
[WSL Debian]
```bash
sudo apt update
sudo apt install -y incus
```

#### Alternative: Install from Snap (if available)
[WSL Debian]
```bash
sudo apt install -y snapd
sudo snap install incus
```

## Verify Installation

[WSL Debian]
```bash
incus --version
```

Expected output shows the version number, for example:
```
incus 7.x  ( if you install from 7.0 lts source ) 
```

## Initialise Incus

Before using Incus, you need to initialise it:

```bash
sudo adduser $USER incus-admin
newgrp incus-admin
```

[WSL Debian]
```bash
sudo incus admin init
```

You will be prompted with several questions. **Read each one carefully.**

### Question 1: Clustering

```
Would you like to use Incus clustering? (yes/no) [default=no]:
```

**Answer: `no`**

For a beginner laboratory, you do not need clustering. Clustering is for multiple Incus servers working together.

### Question 2: Storage Pool

```
Name of the existing storage pool or new storage pool name:
```

**Answer: `default`** (or press Enter to accept the default)

This creates a storage pool that holds container images and data. The storage pool is where your containers live on disk.

### Question 3: Storage Driver

```
Would you like to create a new storage pool? (yes/no) [default=yes]:
Name of the existing storage pool or new storage pool name: [default=default]:
Driver to use: [default=btrfs]:
```

For simplicity, accept the defaults. Common options:
- `dir` — simplest, uses directories (good for learning)
- `btrfs` — supports snapshots natively
- `zfs` — advanced, needs special setup

If you want a simple `dir` pool:
```
Would you like to create a new storage pool? (yes/no) [default=yes]:
Name of the existing storage pool or new storage pool name: [default=default]:
Driver to use: [default=btrfs]: dir
```

### Question 4: Connect to a Network Bridge

```
Would you like to connect to a pre-existing network bridge or host device? (yes/no) [default=no]:
```

**Answer: `yes`**

This enables networking between containers.

### Question 5: Bridge Name

```
Name of the existing bridge or host device: [default=incusbr0]:
```

**Answer: `incusbr0`** (press Enter)

This creates a virtual network bridge. Think of it as a virtual switch connecting your containers.

### Question 6: IPv4

```
Would you like to setup a DHCP IPv4 subnet? (yes/no) [default=yes]:
```

**Answer: `yes`**

Containers will get automatic IP addresses.

### Question 7: IPv4 Subnet

```
IPv4 subnet to use: [default=10.x.x.x/24]:
```

Press Enter to accept the default. Incus will automatically pick a subnet.

### Question 8: IPv6

```
Would you like to setup a DHCP IPv6 subnet? (yes/no) [default=yes]:
```

**Answer: `no`**

For a simple setup, skip IPv6 for now.

### Question 9: DNS

```
Would you like to set up a local DNS server? (yes/no) [default=yes]:
```

**Answer: `yes`**

Containers will be able to resolve names using the Incus DNS server.

### Question 10: Image Auto-Update

```
Would you like to have images automatically updated? (yes/no) [default=yes]:
```

**Answer: `yes`**

Keeps images current.

### Question 11: Clustering Member

```
Would you like to create a new cluster? (yes/no) [default=no]:
```

**Answer: `no`**

Already covered — we are not using clustering.

If all goes well, you will see:
```
Incus initialised successfully.
```
---

On my spare laptop, it looks like this.

```bash

hardy@DESKTOP-40PBI82:~$ newgrp incus-admin
hardy@DESKTOP-40PBI82:~$ sudo incus admin init
Would you like to use clustering? (yes/no) [default=no]: no
Do you want to configure a new storage pool? (yes/no) [default=yes]: yes
Name of the new storage pool [default=default]:
Name of the storage backend to use (dir, truenas) [default=dir]: dir
Where should this storage pool store its data? [default=/var/lib/incus/storage-pools/default]:
Would you like to create a new local network bridge? (yes/no) [default=yes]:
What should the new bridge be called? [default=incusbr0]:
What IPv4 address should be used? (CIDR subnet notation, “auto” or “none”) [default=auto]:
What IPv6 address should be used? (CIDR subnet notation, “auto” or “none”) [default=auto]:
Would you like the server to be available over the network? (yes/no) [default=no]:
Would you like stale cached images to be updated automatically? (yes/no) [default=yes]:
Would you like a YAML "init" preseed to be printed? (yes/no) [default=no]:
hardy@DESKTOP-40PBI82:~$ incus list
To start your first container, try: incus launch images:ubuntu/24.04
Or for a virtual machine: incus launch images:ubuntu/24.04 --vm

+------+-------+------+------+------+-----------+
| NAME | STATE | IPV4 | IPV6 | TYPE | SNAPSHOTS |
+------+-------+------+------+------+-----------+
```
---
## Verify Initialisation

[WSL Debian]
```bash
incus admin init --dump
```

Shows the current configuration without prompts.

Or check the storage pools:
[WSL Debian]
```bash
incus storage list
```

Or the networks:
[WSL Debian]
```bash
incus network list
```

## Understanding What You Just Did

```
Debian (WSL)
   │
   └── Incus
        ├── Storage pool ("default") — where containers are stored
        ├── Network bridge ("incusbr0") — virtual switch for containers
        └── Configuration — IP addresses, DNS, DHCP
```

The storage pool is like a disk where Incus saves container images and data.

The network bridge (`incusbr0`) is like a virtual router/switch. Containers connected to it can talk to each other and to the outside world.

## What If Something Goes Wrong?

To reset Incus and start over:

[WSL Debian]
```bash
sudo incus admin shutdowntest
sudo incus admin destroy --all
sudo incus admin init
```

> ⚠️ **Warning: `incus admin destroy` deletes all containers and snapshots.**

For a complete reset, you can also delete the Incus configuration:
[WSL Debian]
```bash
sudo rm -rf ~/.local/share/lxc
sudo incus admin init
```

> ⚠️ **Warning: This permanently deletes all Incus data.**

## Next Step

Continue to [Chapter 5 — First Incus Container](05-first-container.md) to launch your first container.

## Tested With

> **Tested with:**
> - Windows 11 23H2
> - WSL2
> - Debian 13
> - Incus 7.5 (from official repository) / Incus 6.x (from Debian repository)
>
> Commands may vary with different versions. Check `incus --version` to confirm.
