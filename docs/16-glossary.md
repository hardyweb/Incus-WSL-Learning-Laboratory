# Chapter 16 — Glossary

## A

### apt
Debian/Ubuntu package manager (Advanced Package Tool). Installs, updates, and removes software.
```bash
apt install nginx
```

### apt-get
Low-level version of apt. Used in older tutorials and scripts.
```bash
apt-get install nginx
```

### Alpine
A minimal, security-focused Linux distribution using `apk` package manager. Often used in containers.

### Application Programming Interface (API)
A set of rules that lets one program talk to another. Incus has a REST API for remote management.

## B

### Background Process
A process that runs while your terminal can accept new commands.
```bash
ping example.com &
```

### Base System (FreeBSD)
The core FreeBSD operating system (kernel + userland), updated as one unit.

## C

### CLI (Command-Line Interface)
A text-based interface where you type commands.

### Container
A lightweight, isolated Linux environment that shares the host kernel but has its own filesystem and process space.

### CPU (Central Processing Unit)
The "brain" of the computer; executes instructions.

### Cron
A time-based job scheduler in Linux.
```bash
crontab -e  # Edit scheduled tasks
```

## D

### Daemon
A background process that provides a service (e.g., `sshd`, `nginx`).

### Debian
A popular, stable Linux distribution known for its apt package manager and stability.

### Directory
A folder in the Linux filesystem. Shown as a tree from `/` (root).

### Disk Image (.img, .qcow2)
A file that represents a virtual disk for a virtual machine (VM).

### DNS (Domain Name System)
Translates domain names (e.g., `google.com`) into IP addresses (e.g., `8.8.8.8`).

## E

### Environment Variable
A variable that affects how processes run (e.g., `PATH`, `HOME`).
```bash
echo $PATH
export MY_VAR="hello"
```

## F

### File Descriptor
A number representing an open file (0=stdin, 1=stdout, 2=stderr).

### Filesystem
The way files and directories are organized and stored. Common Linux filesystems: ext4, XFS, ZFS.

### Firewall
A security system that filters network traffic.

### FreeBSD
A Unix-like operating system known for stability, networking, and the ZFS filesystem. Uses pkg/ports for packages.

## G

### Git
A distributed version control system.
```bash
git commit -m "message"
```

### Group
A collection of users with common permissions in Linux/Unix.

## H

### Hard Disk Drive (HDD)
A traditional spinning disk storage device.

### Hostname
The name of a computer on a network.
```bash
hostname
```

### HTTP
Hypertext Transfer Protocol — the basis of web browsing.

### HTTPS
Secure version of HTTP, encrypted with SSL/TLS.

## I

### IP Address (Internet Protocol Address)
A numerical address for a device on a network (e.g., `192.168.1.10`).

### ISO
An archive file of an optical disc (CD/DVD). Used for installing operating systems.

### Incus
A manager for Linux containers and virtual machines (community fork of LXD).

## J

### Jail
FreeBSD's technology for lightweight OS-level virtualization (similar to Linux containers).

### Journal
In systemd systems, a log stored by `journald` (viewed via `journalctl`).

## K

### Kernel
The core part of an operating system that manages hardware and processes.

### KVM (Kernel-based Virtual Machine)
A Linux kernel feature that provides hardware-assisted virtualization. Incus uses KVM to run virtual machines. If `/dev/kvm` is not available, VMs cannot run.
```bash
ls -la /dev/kvm
```

### Kill Signal
SIGTERM (15, asks process to terminate) or SIGKILL (9, force termination).
```bash
kill 1234
kill -9 1234
```

## L

### LXC
Linux Containers — the project that provides the underlying container technology for Incus.

### LXD / Incus
LXD was the original container manager (started by Canonical). Incus is the community fork.

### lsblk
Lists block devices (storage): `lsblk`

## M

### Memory (RAM)
Random-Access Memory — temporary storage for running programs.

### Mount
Making a filesystem accessible at a directory.
```bash
mount /dev/sda1 /mnt
```

### MBR (Master Boot Record)
A boot sector format (legacy, replaced by UEFI).

## N

### Namespace
A Linux kernel feature that isolates process groups, network, filesystem, etc. Used for containers.

### Network Bridge
A virtual switch that connects multiple network interfaces (e.g., `incusbr0`).

### Network Interface
A port or virtual port that connects to a network (e.g., `eth0`).

## O

### OS (Operating System)
A collection of software that manages computer hardware and software (e.g., Debian, FreeBSD, Windows).

### OpenRC
An init system used by Alpine and others as a lightweight alternative to systemd.

### OpenSSL
A software library for SSL/TLS (encryption) protocols.

### Orphan Process
A process whose parent has terminated. Adopted by init (PID 1).

## P

### Package Manager
A tool for installing, updating, and removing software (e.g., `apt`, `apk`, `pkg`).

### PATH
An environment variable listing directories where the shell searches for executable programs.
```bash
echo $PATH
```

### Ping
A network utility to test connectivity.
```bash
ping google.com
```

### Port
A number that identifies a process on a network (e.g., 80 for web, 22 for SSH).

### Process
An executing instance of a program. Each has a unique Process ID (PID).

### Process ID (PID)
A unique number that identifies each running process.

### Profile (Incus)
A configuration template applied to Incus instances (networks, disks, etc.).

### Port Forwarding
Redirecting traffic from one port to another (e.g., expose container web server on host port 8080).

### ports
In IPv6, ports work the same as IPv4.

## Q

### QEMU
An emulator used by Incus (and others) to run virtual machines by emulation/virtualization.

## R

### Repository
A central location storing software packages (e.g., apt sources in `/etc/apt/sources.list`).

### Reverse Proxy
A server that forwards client requests to backend servers (e.g., nginx proxying to a container).

### root
The superuser with full system access. Username: `root`. UID: 0.

### Round Robin
A scheduling method (used in DNS load balancing, for example).

## S

### SSH (Secure Shell)
A protocol to securely access a remote command-line shell.
```bash
ssh user@host
```

### SSD (Solid State Drive)
A storage device that is faster than HDD but typically smaller and more expensive.

### Snapshot
A saved state of a container or VM at a point in time.

### Socket
An endpoint for communication. Listening sockets accept connections.
```bash
ss -tulpn  # Show sockets
```

### Soft Link (Symbolic Link)
A pointer to another file or directory.
```bash
ln -s /real/path /link
```

### Swap
A disk space used as virtual memory when RAM is full.

### System Call
A function that asks the kernel to do something (e.g., reading a file).

### systemd
A system and service manager used by most Linux distributions. It has components like `systemctl` and `journalctl`.

## T

### TCP (Transmission Control Protocol)
A protocol for reliable network communication.

### Tree
A command that shows directories in a tree-like format.
```bash
tree /etc
```

## U

### UDP (User Datagram Protocol)
A simpler, faster protocol (no reliability). Used for DNS, video streaming.

### UEFI (Unified Extensible Firmware Interface)
The modern replacement for BIOS. Handles boot process.

### Ubuntu
A popular Linux distribution based on Debian.

### umask
A setting that controls default file permissions.

### Unmount
To detach a filesystem.
```bash
umount /mnt
```

### uname
Shows system information.
```bash
uname -a
```

### User
An account in the system. Each has a username and UID.

### User ID (UID)
A unique number identifying a user (e.g., root = UID 0).

## V

### Version Control System (VCS)
Tracks changes in source code (e.g., Git).

### Virtual Machine (VM)
A complete, self-contained operating system running as software on a physical host.

### Virtualization
Running multiple virtual machines or containers on one physical machine.

### VLAN (Virtual LAN)
A way to segment a network logically (not directly used in basic Incus setups).

## W

### WSL (Windows Subsystem for Linux)
A tool to run Linux on Windows.

### WSL1 / WSL2
- WSL1: Translates Linux syscalls to Windows (older)
- WSL2: Runs a real Linux kernel in a VM (recommended for containers)

### Web Server
A service that serves web pages (e.g., nginx, Apache).

## X

### Xen
A virtual machine monitor (alternative to QEMU used for virtualization).

## Z

### ZFS
A modern filesystem with features like snapshots, compression, and checksums. Used in FreeBSD and available on some Linux distributions.

## Additional Resources

- [Arch Wiki Glossary](https://wiki.archlinux.org/title/Glossary)
- [GNU Glossary](https://www.gnu.org/gnu/glossary)
- [FreeBSD Glossary](https://docs.freebsd.org/en/books/faq/glossary/)
- [Incus Documentation](https://docs.incus-labs.org/)
- [WSL Documentation](https://learn.microsoft.com/en-us/windows/wsl/)

## Next Step

All chapters complete! Review the [Command Cheat Sheet](15-command-cheatsheet.md) and try the labs in the `labs/` directory.