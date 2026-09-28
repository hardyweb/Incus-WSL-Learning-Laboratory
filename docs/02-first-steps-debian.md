# Chapter 2 — First Steps in Debian

## Your First Linux Terminal

When you run `wsl`, you start a **shell** — a program that reads your commands and runs them.

The default shell is usually **Bash** (Bourne Again SHell).

You see a prompt like:
```
user@debian:~$
```

This means:
- `user` — your username
- `debian` — the hostname (computer name)
- `~` — your current directory (home directory)
- `$` — normal user prompt (`#` would be root)

## Basic Navigation Commands

### whoami — Who am I?
[WSL Debian]
```bash
whoami
```
Shows your username.

### hostname — What is this computer called?
[WSL Debian]
```bash
hostname
```
Shows the computer name.

### pwd — Print Working Directory
[WSL Debian]
```bash
pwd
```
Shows your current location in the filesystem.

Output might be:
```
/home/user
```

### ls — List Directory Contents
[WSL Debian]
```bash
ls
```
Lists files in the current directory.

With details:
[WSL Debian]
```bash
ls -la
```
- `-l` — long format (permissions, size, date)
- `-a` — show all files (including hidden ones starting with `.`)

### cd — Change Directory
[WSL Debian]
```bash
cd /etc
```
Change to `/etc` directory.

[WSL Debian]
```bash
cd ~
```
Go to your home directory (`~` is a shortcut).

[WSL Debian]
```bash
cd -
```
Go to the previous directory.

[WSL Debian]
```bash
cd ..
```
Go up one level.

### mkdir — Make Directory
[WSL Debian]
```bash
mkdir practice
```
Create a directory named `practice`.

[WSL Debian]
```bash
mkdir -p project/{src,bin,docs}
```
Create nested directories at once.

### touch — Create Empty File or Update Timestamp
[WSL Debian]
```bash
touch notes.txt
```
Create an empty file named `notes.txt`.

[WSL Debian]
```bash
touch file1.txt file2.txt file3.txt
```
Create multiple files.

### cp — Copy Files and Directories
[WSL Debian]
```bash
cp notes.txt notes-backup.txt
```
Copy a file.

[WSL Debian]
```bash
cp -r practice/ practice-copy/
```
Copy a directory and its contents (`-r` = recursive).

### mv — Move or Rename
[WSL Debian]
```bash
mv notes.txt important.txt
```
Rename a file.

[WSL Debian]
```bash
mv important.txt ~/practice/
```
Move a file to another directory.

### rm — Remove Files and Directories
[WSL Debian]
```bash
rm notes.txt
```
Delete a file.

[WSL Debian]
⚠️ **Warning: Be careful with these commands!**
```bash
rm -rf directory-name
```
Delete a directory and all its contents **permanently**.

### cat — Concatenate and Display Files
[WSL Debian]
```bash
cat /etc/os-release
```
Show the entire content of a file.

[WSL Debian]
```bash
cat file1.txt file2.txt > combined.txt
```
Combine two files into a new file.

### less — View Files Page by Page
[WSL Debian]
```bash
less /etc/os-release
```
Navigate with:
- `Space` — next page
- `b` — previous page
- `/pattern` — search forward
- `?pattern` — search backward
- `q` — quit

### man — Read the Manual
[WSL Debian]
```bash
man ls
```
Show the manual page for the `ls` command.

Navigate same as `less`. Press `q` to exit.

To search manuals:
[WSL Debian]
```bash
man -k network
```
Find commands related to networking.

## Getting Help

Most commands support `--help`:
[WSL Debian]
```bash
ls --help
```

Or try the `help` builtin for shell commands:
[WSL Debian]
```bash
help cd
```

## Superuser and sudo

### root — The Superuser
In Linux, `root` is the administrator account with full access.

### sudo — Superuser Do
Instead of logging in as root, use `sudo` to run commands with temporary superuser privileges.

[WSL Debian]
```bash
sudo apt update
```
You will be prompted for **your password** (not root's).

### When to Use sudo

Use `sudo` for:
- Installing/removing software (`apt install`, `apt remove`)
- Modifying system files (`/etc/*`)
- Managing services (`systemctl`)
- Viewing system logs (`journalctl`)

Do **not** use `sudo` for:
- Editing your own files in `~/`
- Running most user programs
- Basic file operations (`ls`, `cd`, `cp`, etc.)

## The Linux Filesystem Hierarchy

Linux organizes files in a tree structure starting at `/` (root).

Key directories:
```
/etc        # System configuration files
/home       # Users' home directories (/home/user, /home/teacher)
/var        # Variable data (logs, caches, websites)
/usr        # User programs and libraries (most software)
/tmp        # Temporary files (cleared on reboot)
/opt        # Optional third-party software
/boot       # Boot loader files (kernel, boot config)
/dev        # Device files (disks, terminals)
/proc       # Process and kernel information (virtual)
/sys        # System information (virtual)
/root       # root's home directory
```

### Example: Viewing /etc
[WSL Debian]
```bash
ls -la /etc
```
You'll see configuration files like:
- `hostname` — system hostname
- `hosts` — static IP-to-name mapping
- `resolv.conf` — DNS configuration
- `passwd` — user accounts
- `group` — user groups
- `fstab` — filesystems to mount at boot

## Updating Debian Package System

### What is a Package Manager?

A package manager installs, updates, and removes software from trusted repositories.

Debian uses **APT** (Advanced Package Tool).

### Update Package Lists
[WSL Debian]
```bash
sudo apt update
```
Downloads the latest package information from repositories.

Does **not** install updates — just refreshes the list.

### Upgrade Installed Packages
[WSL Debian]
```bash
sudo apt upgrade
```
Installs available updates for currently installed packages.

Safe to run regularly.

### Full Upgrade (Handles Dependencies)
[WSL Debian]
```bash
sudo apt full-upgrade
```
Like `upgrade` but may remove packages if needed to resolve dependencies.

### Clean Up
[WSL Debian]
```bash
sudo apt autoremove
```
Removes packages that were installed as dependencies but are no longer needed.

[WSL Debian]
```bash
sudo apt autoclean
```
Removes old downloaded package files.

## Exercise: Explore Your System

Try these commands:

1. [WSL Debian] `whoami && hostname && pwd`
2. [WSL Debian] `ls -la /`
3. [WSL Debian] `ls -la /etc | head -20`
4. [WSL Debian] `touch testfile.txt && ls -l testfile.txt && rm testfile.txt`
5. [WSL Debian] `mkdir -p ~/practice/{dir1,dir2,dir3} && tree ~/practice` (install tree first: `sudo apt install tree`)
6. [WSL Debian] `cat /etc/os-release`
7. [WSL Debian] `man ls | head -30`
8. [WSL Debian] `sudo apt update` (wait for completion)
9. [WSL Debian] `df -h` (show disk usage)
10. [WSL Debian] `free -h` (show memory usage)

## Next Step

Continue to [Chapter 3 — Linux Fundamentals](03-linux-fundamentals.md) to learn about processes, permissions, and services.