# Chapter 1 — Windows and WSL

## What is WSL?

**WSL** (Windows Subsystem for Linux) lets you run a Linux environment directly on Windows, without a virtual machine or dual boot.

WSL translates Linux system calls into Windows system calls. Your Linux programs run on the Windows kernel.

## Terminal Options

For the best experience, consider using **Windows Terminal** instead of the default Windows console. Windows Terminal offers tabs, split panes, GPU-accelerated text rendering, and extensive customization.

You can install Windows Terminal via:
- **Microsoft Store**: Search for "Windows Terminal" in the Store app
- **winget**: [Windows PowerShell]
```powershell
winget install Microsoft.WindowsTerminal
```
- **GitHub releases**: Download from https://github.com/microsoft/terminal/releases

Once installed, you can launch your WSL distribution from Windows Terminal by selecting it from the dropdown menu, or by running `wsl` in a PowerShell/Command Prompt tab.

## What is WSL2?

**WSL2** is the second version of WSL. It uses a real Linux kernel inside a lightweight virtual machine.

| Feature | WSL1 | WSL2 |
|---------|------|------|
| Linux kernel | No (translation layer) | Yes (real kernel) |
| System call compatibility | Partial | Full |
| Performance (file I/O) | Fast on Windows files | Fast on Linux files |
| Docker/container support | Limited | Full |
| Nested virtualization | No | Yes |

WSL2 is what you want for learning Linux and running Incus.

## Why WSL2 for Learning Linux?

- **Real Linux kernel** — `uname -a` shows `Linux`
- **Full system call compatibility** — containers, Kubernetes, eBPF work
- **No dual boot** — switch between Windows and Linux instantly
- **Windows integration** — access Windows files from Linux (`/mnt/c/...`)
- **Fast startup** — seconds, not minutes

## WSL2 vs Dual Boot

| | Dual Boot | WSL2 |
|---|-----------|------|
| Switching OS | Reboot required | Instant |
| Windows apps | Not available | Available |
| Linux files on Windows | Difficult | Easy (`/mnt/c/...`) |
| Hardware access | Full | Limited (improving) |
| Risk to Windows | Partition changes | None |

## WSL2 vs Traditional VM (VirtualBox/VMware)

| | Traditional VM | WSL2 |
|---|----------------|------|
| Resource overhead | High | Low |
| Integration with Windows | Poor | Excellent |
| Startup time | Minutes | Seconds |
| GPU/compute | Manual config | Improving support |
| Nested virtualization | Yes | Yes (with config) |

## Windows Requirements

- Windows 11 (or Windows 10 22H2+)
- 64-bit CPU with virtualization support
- Virtualization enabled in BIOS/UEFI
- At least 8 GB RAM (16 GB recommended)
- 20 GB free disk space

## Check Your Windows Version

[Windows PowerShell]
```powershell
winver
```

Or:

[Windows PowerShell]
```powershell
systeminfo | findstr /B /C:"OS Name" /C:"OS Version"
```

You need **Windows 11** or **Windows 10 version 22H2 (build 19045) or later**.

## Enable and Install WSL

The modern, Microsoft-supported method:

[Windows PowerShell]
```powershell
wsl --install
```

### What this command does:

1. Enables the `Virtual Machine Platform` Windows feature
2. Enables the `WSL` Windows feature
3. Downloads and installs the WSL2 Linux kernel
4. Sets WSL2 as the default version
5. Installs **Ubuntu** as the default distribution

> After running this, **restart your computer** when prompted.

## Install Debian Instead of Ubuntu

If you want Debian (recommended for this guide):

[Windows PowerShell]
```powershell
wsl --install -d Debian
```
```
Downloading: Debian
Installing: Debian
Distribution successfully installed. It can be launched via 'wsl.exe -d Debian'
Launching Debian...
Provisioning the new WSL instance Debian
This might take a while...
Create a default Unix user account: <username>
```

It is used to create a regular (non-root) user, which will be the default user for the WSL distribution

```
Enter new UNIX username:
New password:
```
### See all available distributions:

[Windows PowerShell]
```powershell
wsl --list --online
```

Example output:
```
NAME            FRIENDLY NAME
Ubuntu          Ubuntu
Debian          Debian GNU/Linux
kali-linux      Kali Linux Rolling
openSUSE-42     openSUSE Leap 42
SLES-15         SUSE Linux Enterprise Server 15
Ubuntu-24.04    Ubuntu 24.04 LTS
Ubuntu-22.04    Ubuntu 22.04 LTS
```

## Verify WSL Installation

After restarting:

[Windows PowerShell]
```powershell
wsl --list --verbose
```

Example output:
```
  NAME      STATE           VERSION
* Debian    Running         2
```

- `STATE` — Running/Stopped
- `VERSION` — Must show `2` for WSL2

If VERSION shows `1`, convert it:

[Windows PowerShell]
```powershell
wsl --set-version Debian 2
```

This may take a minute.

## Set Default WSL Version to 2

[Windows PowerShell]
```powershell
wsl --set-default-version 2
```

New distributions will install as WSL2 automatically.

## Start Debian

[Windows PowerShell]
```powershell
wsl -d Debian
```

Or simply:

[Windows PowerShell]
```powershell
wsl
```



You are now inside a **Debian Linux terminal** running on WSL2.

## What Just Happened?

```
Windows 11
   │
   └── WSL2 (lightweight VM)
        │
        └── Linux Kernel (Microsoft build)
             │
             └── Debian userland (apt, bash, systemd*)
```

- `systemd` support depends on Windows/WSL version (enabled by default on Windows 11 22H2+)
- You have a real Linux environment with `apt`, `systemctl`, `ip`, `ss`, etc.

## Common WSL Commands

| Command | Description |
|---------|-------------|
| `wsl` | Start default distribution |
| `wsl -d Debian` | Start specific distribution |
| `wsl --list --verbose` | Show all distributions and versions |
| `wsl --shutdown` | Stop all WSL instances |
| `wsl --terminate Debian` | Stop specific distribution |
| `wsl --set-version Debian 2` | Convert to WSL2 |
| `wsl --set-default Debian` | Set default distribution |

## Access Windows Files from Linux

Inside Debian:
```bash
ls /mnt/c/
ls /mnt/c/Users/YourName/
```

Windows drives are mounted under `/mnt/`.

## Access Linux Files from Windows

In Windows Explorer:
```
\\wsl$\Debian\home\youruser\
```

Or from PowerShell:
```powershell
explorer.exe \\wsl$\Debian\home\youruser\
```

⚠️ **Do not modify Linux files from Windows** using Windows tools. Use the Linux terminal for Linux files.

## Next Step

Continue to [Chapter 2 — First Steps in Debian](02-first-steps-debian.md) to learn basic Linux commands.
