# Chapter 14 — Troubleshooting

## Introduction

This chapter covers common problems and solutions for the Incus WSL learning laboratory.

## Common Issues

### WSL Not Installed

**Symptoms:**
- `wsl --install` fails with errors
- "Windows cannot install WSL2"

**Solutions:**

1. **Check Virtualization**
   - Ensure virtualization is enabled in BIOS/UEFI
   - Check: `wmic cpu get VirtualizationEnabled`

2. **Windows Updates**
   ```powershell
   Windows Update Settings -> Advanced settings -> Get updates from more than one place -> Check for updates
   ```

3. **PowerShell as Administrator**
   Run `wsl --install` from elevated PowerShell.

4. **Reinstall WSL**
   ```powershell
   wsl --shutdown
   wsl --install
   ```

### WSL Version 1 Instead of 2

**Symptoms:**
- `wsl --status` shows VERSION = 1
- `incus` errors about kernel features

**Solutions:**

1. Convert a single distribution:
[Windows PowerShell]
```powershell
wsl --set-version Debian 2
```

2. Convert all distributions:
[Windows PowerShell]
```powershell
wsl --set-default-version 2
```

3. Reinstall WSL2:
[Windows PowerShell]
```powershell
wsl --shutdown
wsl --install -d Debian
```

### Debian Cannot Start

**Symptoms:**
- `wsl -d Debian` hangs or fails
- Error: "exit code 1"

**Solutions:**

1. **Check Systemd**
   If systemd is not available:
[WSL Debian]
```bash
journalctl --version
```

2. **Restart WSL**
[Windows PowerShell]
```powershell
wsl --shutdown
```

3. **Check WSL Status**
[Windows PowerShell]
```powershell
wsl --status
```

4. **Restart Windows**
After major WSL configuration changes.

### sudo Problems

**Symptoms:**
- "sudo: no tty present"
- "sudo: sorry, you must have a tty to run sudo"

**Solutions:**

1. **Terminal Support**
   Use a terminal that supports pseudo-terminal (most do)

2. **WSL Configuration**
   [WSL Debian]
   ```bash
   cat /etc/wsl.conf
   ```

3. **Alternative to sudo**
   Run commands as `root` in the container (already root)

### Incus Command Not Found

**Symptoms:**
- `incus` command not recognized
- "incus: command not found"

**Solutions:**

1. **Check Installation**
[WSL Debian]
```bash
which incus
ls -la /usr/bin/incus
```

2. **Reinstall Incus**
[WSL Debian]
```bash
sudo apt install --reinstall incus
```

3. **Add to PATH**
   Check shell profile (~/.bashrc, ~/.profile)

### Permission Denied

**Symptoms:**
- "Permission denied" when running commands
- "You are not authorized"

**Solutions:**

1. **Check permissions**
[WSL Debian]
```bash
ls -la file.txt
```

2. **Use sudo when needed**
[WSL Debian]
```bash
sudo cp file /etc
```

3. **Root inside container**
   By default, Incus containers run as root.

### Incus Initialisation Problems

**Symptoms:**
- `incus admin init` hangs
- "Error: initialization failed"

**Solutions:**

1. **Simple Init**
   Accept all defaults.
   - Yes to clustering? No
   - Storage pool name? default
   - Driver? dir (simplest)
   - Bridge? incusbr0
   - IPv4? yes
   - IPv6? no
   - DNS? yes

2. **Reset and Try Again**
[WSL Debian]
```bash
sudo incus admin shutdowntest
sudo incus admin destroy --all
sudo incus admin init
```

3. **Check Resources**
   Ensure enough disk space.

### Networking Problems

**Symptoms:**
- Containers cannot ping each other
- No internet access from containers

**Solutions:**

1. **Check Network Status**
[WSL Debian]
```bash
incus network list
incus network show incusbr0
```

2. **Check Container Networking**
[WSL Debian]
```bash
incus info container-name | grep -A10 eth0
```

3. **Restart Containers**
[WSL Debian]
```bash
incus restart container-name
```

4. **Check Host Network**
[WSL Debian]
```bash
ip addr
```

### Container Cannot Obtain IP

**Symptoms:**
- `incus list` shows "N/A" for IPv4
- `incus exec container -- ip addr` shows no eth0

**Solutions:**

1. **Wait**
   It takes a few seconds after startup.

2. **Check Network**
[WSL Debian]
```bash
incus network list
```

3. **Restart**
[WSL Debian]
```bash
incus restart container-name
```

4. **Manual IP (if DHCP fails)**
[WSL Debian]
```bash
incus config device add container-name eth0 nic name=eth0 parent=incusbr0
```

### Container Cannot Reach Internet

**Symptoms:**
- `ping 8.8.8.8` fails
- `curl google.com` fails

**Solutions:**

1. **Check DNS**
[Incus container]
```bash
cat /etc/resolv.conf
```

2. **Test Connectivity**
[Incus container]
```bash
ping -c 3 8.8.8.8
```

3. **Check Container IP**
[WSL Debian]
```bash
incus exec container -- ip route
```

4. **Check Incus Network**
[WSL Debian]
```bash
incus network show incusbr0
```

### Windows Cannot Access Container Service

**Symptoms:**
- Cannot access container web service from Windows browser
- `curl http://container-ip` works from WSL, not Windows

**Solutions:**

1. **Port Forwarding**
[WSL Debian]
```bash
incus config device add container-name httpproxy proxy listen=tcp:0.0.0.0:8080 connect=tcp:10.x.x.x:80
```

2. **Available on WSL**
   WSL has full internet access through Windows.

3. **Access via WSL**
   Use container IP from WSL, not Windows.

### Virtualization Limitations

**Symptoms:**
- FreeBSD VM, Linux VM, or Windows VM fails to start
- Error: "Unable to start VM"
- Error mentioning `kvm`, `/dev/kvm`, or virtualization

**Solutions:**

1. **Check `/dev/kvm` First (most important)**
   [WSL Debian]
   ```bash
   ls -la /dev/kvm
   ```
   - If the device **does not exist** → VMs cannot run in Incus in this WSL setup. Use Linux containers instead.
   - If the device **exists** → VMs should be possible; continue troubleshooting below.

2. **Check Resources**
   VMs need more resources (RAM, CPU) than containers. Increase allocated memory if needed.

3. **WSL Limitations**
   WSL2 may not expose nested virtualization depending on Windows version and configuration.

4. **Alternative**
   Use a separate Windows VM, QEMU/KVM host, or physical machine for FreeBSD testing.

### FreeBSD VM Limitations

**Symptoms:**
- FreeBSD VM fails
- Error about unsupported features
- VM starts but does not boot (blank console)

**Solutions:**

1. **Check `/dev/kvm`**
   [WSL Debian]
   ```bash
   ls -la /dev/kvm
   ```
   If missing, FreeBSD VMs cannot run. Linux containers are still fully supported.

2. **Disable Secure Boot**
   FreeBSD images do not trust the default UEFI Secure Boot keys. Launch with:
   [WSL Debian]
   ```bash
   incus launch images:freebsd/14.5 freebsd01 --vm -c security.secureboot=false
   ```
   For an existing instance:
   [WSL Debian]
   ```bash
   incus config set freebsd01 security.secureboot=false
   incus restart freebsd01
   ```

3. **Accept Limitation**
   FreeBSD VM support depends on the host virtualization configuration.

4. **Focus on Linux**
   Learn with Linux containers first.

5. **External Testing**
   Test FreeBSD on a separate machine.

## Diagnostic Commands

### WSL Status

[Windows PowerShell]
```powershell
wsl --status
```

### Incus Status

[WSL Debian]
```bash
incus version
incus list
incus network list
```

### VM / Virtualization Check

[WSL Debian]
```bash
ls -la /dev/kvm          # Does the KVM device exist?
incus info <vm-name>     # Check VM status and errors
incus admin init --dump  # Check Incus configuration
```

### Container Debug

[WSL Debian]
```bash
incus info container-name
incus exec container-name -- ip addr
incus exec container-name -- ps aux
incus exec container-name -- top -bn1
```

### Filesystem Check

[WSL Debian]
```bash
df -h
mount
```

## When to Ask for Help

### Documentation
- Incus docs: https://docs.incus-labs.org/
- WSL docs: https://learn.microsoft.com/en-us/windows/wsl/
- Debian docs: https://www.debian.org/doc/

### Common Search Terms
- "incus wsl vm not working"
- "container no ip address"
- "systemd not available wsl"

### Local Support
- Try simple commands first
- Check logs and error messages
- Use snapshots to recover
- Document what you've tried

## Exercise: Troubleshooting Practice

1. **Practice Scenario 1:** A student cannot run `sudo apt update`
   - What commands help?
   - What errors might you see?

2. **Practice Scenario 2:** A container has no network
   - What diagnostic commands?
   - What potential causes?

3. **Practice Scenario 3:** Incus fails to start
   - What checks?
   - What reset commands?

4. **Practice Scenario 4:** Cannot access internet from container
   - What commands to debug?
   - What network settings?

## Next Step

Continue to [Chapter 15 — Command Cheat Sheet](15-command-cheatsheet.md) for quick reference.

## Tested With

> **Tested with:**
> - Windows 11 23H2
> - WSL2
> - Debian 13
> - Incus 6.x
>
> Verify against your specific version and configuration.