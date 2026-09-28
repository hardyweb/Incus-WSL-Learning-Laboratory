# Lab 5 — FreeBSD Exploration

## Objective
Explore FreeBSD using Incus VM support. Learn the differences between Linux and FreeBSD administration.

## What You Will Learn

- How to launch a FreeBSD VM in Incus
- FreeBSD basics: package management, services, networking
- Comparison of Linux and FreeBSD commands
- Understanding of Unix-like operating systems

## Prerequisites

- Lab 1 completed
- `/dev/kvm` available on the WSL host (required for VMs)
- Basic Linux terminal usage

## Step 0

### Command

Check whether your WSL environment supports virtual machines:

[WSL Debian]
```bash
ls -la /dev/kvm
```

### What happened?

- If `/dev/kvm` exists → VMs are supported, continue to Step 1
- If `/dev/kvm` does **not** exist → VMs cannot run in Incus; skip the commands and read the conceptual sections instead

> **Note:** Without the KVM device, Incus cannot launch any VM (FreeBSD, Linux, or Windows). Linux containers still work.

## Step 1

### Command

[WSL Debian]
```bash
incus launch images:freebsd/14.5 freebsd-lab --vm -c security.secureboot=false
```

### What happened?

- Creates a FreeBSD virtual machine named `freebsd-lab`
- Downloads the FreeBSD 14.5 image
- Boots the VM (may take 30-60 seconds)
- `-c security.secureboot=false` disables UEFI Secure Boot, which is required because FreeBSD images do not carry the Secure Boot keys trusted by the default firmware

> **Note:** If this command fails, first check `/dev/kvm` (Step 0). Otherwise your WSL2/Incus environment may not support nested virtualization. See Chapter 10 for alternatives.

### If You Already Created the VM and It Fails to Boot

If the VM was created but does not boot because Secure Boot is enforced, turn it off on the existing instance instead of recreating it:

[WSL Debian]
```bash
incus config set freebsd-lab security.secureboot=false
incus restart freebsd-lab
```

## Step 2

### Command

[WSL Debian]
```bash
incus list
```

### What happened?

- Shows the VM with state `RUNNING`
- Type is `VIRTUAL-MACHINE`
- IP may show if network is configured

Wait for the VM to fully boot (30-60 seconds).

## Step 3

### Command

[WSL Debian]
```bash
incus console freebsd-lab
```

### What happened?

- Opens the serial console of the FreeBSD VM
- You see the boot process and login prompt

Login as `root` (no password initially).

## Step 4

### Command

[FreeBSD]
```bash
uname -a
```

### What happened?

- Shows FreeBSD kernel version and system information
- Example: `FreeBSD freebsd-lab 14.0-RELEASE amd64`

## Step 5

### Command

[FreeBSD]
```bash
ifconfig
```

### What happened?

- Shows network interfaces (typically `vtnet0`)
- Compare with Linux's `ip addr`

## Step 6

### Command

[FreeBSD]
```bash
pkg update
```

### What happened?

- Updates the FreeBSD package manager database
- FreeBSD uses `pkg` for binary packages (similar to `apt`)

## Step 7

### Command

[FreeBSD]
```bash
pkg install -y vim
```

### What happened?

- Installs Vim text editor using FreeBSD's package manager

## Step 8

### Command

[FreeBSD]
```bash
sysrc nginx_enable=YES
```

### What happened?

- Adds `nginx_enable=YES` to `/etc/rc.conf`
- FreeBSD uses `sysrc` to configure services (similar to systemd's `systemctl enable`)

## Step 9

### Command

[FreeBSD]
```bash
pkg install -y nginx
service nginx start
service nginx status
```

### What happened?

- Installs Nginx
- Starts the service using FreeBSD's `service` command
- Shows service status

## Step 10

### Command

[FreeBSD]
```bash
cat /etc/rc.conf
```

### What happened?

- Shows FreeBSD service configuration
- Compare with Linux's `/etc/systemd/system/`

## Step 11

### Command

[FreeBSD]
```bash
ls /etc/
```

### What happened?

- Shows FreeBSD's `/etc` directory
- Compare with Linux's `/etc`

## Step 12

### Command

[FreeBSD]
```bash
pw useradd student
```

### What happened?

- Creates a user named `student`
- FreeBSD uses `pw` for user management (Linux uses `adduser`/`useradd`)

## Step 13

### Command

[FreeBSD]
```bash
exit
```

### What happened?

- Exits the FreeBSD shell
- Returns to the serial console
- Press `Ctrl+A Q` to exit the console

## Step 14

### Command

[WSL Debian]
```bash
incus stop freebsd-lab
```

### What happened?

- Stops the FreeBSD VM

## Step 15

### Command

[WSL Debian]
⚠️ **Warning: This deletes the container and all its data.**
```bash
incus delete --force freebsd-lab
```

### What happened?

- Removes the FreeBSD VM and all its data

## Expected Result

- FreeBSD VM launched and accessible
- Basic FreeBSD commands learned
- Comparison with Linux commands understood

## Questions

1. What is the difference between `pkg` and `apt`?
2. How does FreeBSD's `service` command differ from Linux's `systemctl`?
3. What is the difference between `ifconfig` and `ip addr`?
4. Why is FreeBSD's user management different from Linux's?

## Challenge

- Try to access the FreeBSD VM from WSL using SSH
- Try to run a web server on FreeBSD and access it from WSL
- Compare the file sizes of a Linux container vs FreeBSD VM

## Cleanup

[WSL Debian]
```bash
# VM already deleted
# If you created other files in WSL:
rm -f *.sh
```

All VM data is automatically removed.