# Chapter 10 — FreeBSD Laboratory

## What is FreeBSD?

**FreeBSD** is a complete, open-source Unix-like operating system. It is **not Linux**.

| Aspect | Linux | FreeBSD |
|--------|-------|---------|
| Kernel | Linux kernel | FreeBSD kernel |
| Userland | GNU + other tools | FreeBSD base system (single source tree) |
| Package manager | apt, dnf, pacman | `pkg` (binary), `ports` (source) |
| Service management | systemd, OpenRC, sysvinit | `rc.d` |
| Containers | namespaces/cgroups | **jails** |
| Filesystems | ext4, XFS, Btrfs | UFS, ZFS |

## Verify Virtualization Support

Before attempting to run any VM (FreeBSD, Linux, or Windows), check if your WSL environment exposes the KVM device:

[WSL Debian]
```bash
ls -la /dev/kvm
```

- If `/dev/kvm` exists → VMs **should** work in Incus
- If `/dev/kvm` does **not** exist → VMs **cannot** run in Incus; continue with Linux containers only

> **Important:** Without `/dev/kvm`, Incus cannot launch virtual machines. This applies to FreeBSD VMs, Linux VMs, and Windows VMs alike.

## FreeBSD in Incus: VM, Not Container

> **Important:** FreeBSD cannot run as a Linux container because it has a different kernel.

Incus runs FreeBSD as a **virtual machine** (VM) using QEMU/KVM.

```
Incus
   │
   └── Virtual Machine
        │
        └── FreeBSD kernel + userland
```

This means:
- Slower startup (seconds → minutes)
- More resource usage
- Full hardware virtualization (requires `/dev/kvm`)
- **Different commands and administration**

## Verify FreeBSD VM Support

Check if your Incus/WSL environment supports VMs:

[WSL Debian]
```bash
incus admin init --dump | grep -A5 -B5 vm
```

Or try launching a FreeBSD VM:

[WSL Debian]
```bash
incus launch images:freebsd/14.5 freebsd01 --vm -c security.secureboot=false
```

If this fails with an error about missing kernel or virtualization support, your WSL2 setup may not support nested virtualization.

> **Note:** FreeBSD VM support in Incus on WSL2 depends on:
> - `/dev/kvm` being available (check above)
> - Windows hypervisor features
> - WSL2 nested virtualization support
> - Incus version
> - Your CPU virtualization extensions

## If FreeBSD VM Works

### Launch FreeBSD VM

[WSL Debian]
```bash
incus launch images:freebsd/14.5 freebsd01 --vm -c security.secureboot=false
```

Wait for the VM to boot (30–60 seconds typically).

#### What the Options Mean

| Part | Meaning |
|------|---------|
| `--vm` | Create a **virtual machine** instead of a container |
| `-c security.secureboot=false` | Disable UEFI Secure Boot for this instance |

> **Why disable Secure Boot?** The FreeBSD images do not ship with the Secure Boot keys trusted by the default UEFI firmware used by Incus/QEMU. Without disabling Secure Boot, the VM may fail to boot. This setting applies only to this instance, not the whole system.

#### Fixing an Existing VM That Fails to Boot

If you already created the VM and it is failing to boot because Secure Boot is enforced, you do not need to delete it. Turn off Secure Boot on the existing instance:

[WSL Debian]
```bash
incus config set freebsd01 security.secureboot=false
incus restart freebsd01
```

The same applies to any VM name — replace `freebsd01` with your instance name.

> **Tip:** FreeBSD image aliases include point releases, e.g. `freebsd/14.5`, `freebsd/15.0`, `freebsd/15.1`. Verify what is available before launching:
> [WSL Debian]
> ```bash
> incus image list images: | grep -i freebsd
> ```

### Check Status

[WSL Debian]
```bash
incus list
```

You should see:
```
+-----------+---------+-----+------+----------------+-----------+
| NAME      | STATE   | IPV4| IPV6 | TYPE           | SNAPSHOTS |
+-----------+---------+-----+------+----------------+-----------+
| freebsd01 | RUNNING |     |      | VIRTUAL-MACHINE| 0         |
+-----------+---------+-----+------+----------------+-----------+
```

### Access FreeBSD Console

[WSL Debian]
```bash
incus console freebsd01
```

This opens the serial console. Login as:
- Username: `freebsd` (or `root` if configured)
- Password: Check the image documentation (often no password initially)

Exit console: `Ctrl+A Q` (or `Ctrl+]` depending on terminal)

### Alternative: SSH Access

If the VM gets an IP and SSH is enabled:
[WSL Debian]
```bash
ssh freebsd@10.x.x.x
```

## FreeBSD Basics for Linux Users

### Identify the OS
[FreeBSD]
```bash
uname -a
```

Output:
```
FreeBSD freebsd01 14.0-RELEASE ...
```

### Package Manager: pkg

[FreeBSD]
```bash
pkg update
pkg install nginx
```

### Service Management: rc.d

[FreeBSD]
```bash
sysrc nginx_enable=YES
service nginx start
service nginx status
```

### Network Tools

[FreeBSD]
```bash
ifconfig
netstat -rn
sockstat -4
```

### Process Viewer

[FreeBSD]
```bash
top
```

### Filesystem Hierarchy

| Path | Purpose |
|------|---------|
| `/etc` | System configuration |
| `/usr/local/etc` | Third-party software config |
| `/var` | Variable data (logs, www) |
| `/usr/local` | Installed ports/packages |
| `/boot` | Kernel and modules |

### Users and Groups

[FreeBSD]
```bash
pw useradd student
pw groupadd developers
pw groupmod developers -m student
```

### Shutdown

[WSL Debian]
```bash
incus stop freebsd01
```

Or inside FreeBSD:
[FreeBSD]
```bash
shutdown -p now
```

## FreeBSD Jails (Conceptual)

FreeBSD has its own container technology called **jails**.

> **Note:** Jails run **inside** a FreeBSD host. You cannot run a FreeBSD jail inside a Linux container. You need a FreeBSD host (or VM) to use jails.

Conceptual comparison:
| Linux | FreeBSD |
|-------|---------|
| Container (namespaces) | Jail (chroot + isolation) |
| cgroups | rctl (resource control) |
| Docker/LXC/Incus | `jail`, `bastille`, `ezjail` |

### What to Do Instead

- Check `/dev/kvm` exists on the WSL host (see the "Verify Virtualization Support" section above)
- If `/dev/kvm` does not exist: VMs cannot run. Focus on Linux containers and study FreeBSD concepts from documentation.
- If `/dev/kvm` exists: Try the steps below to launch FreeBSD in Incus.

## If FreeBSD VM Does Not Work

### Check `/dev/kvm` First

If VM launch fails, verify:

[WSL Debian]
```bash
ls -la /dev/kvm
```

- If the device does not exist → VMs are not supported in this WSL configuration. Use Linux containers only.
- If the device exists → Continue troubleshooting with other reasons below.

### Common Reasons VMs Fail (if `/dev/kvm` exists):

1. **Secure Boot enforced** — the VM starts but does not boot (blank console). Fix:
   [WSL Debian]
   ```bash
   incus config set <vm-name> security.secureboot=false
   incus restart <vm-name>
   ```
2. **Insufficient resources** — VMs need more RAM/CPU allocated
3. **Incus not configured for VMs** — check `incus admin init` settings
4. **Nested virtualization disabled** — may need BIOS/UEFI changes

### What to Do Instead

- If `/dev/kvm` does not exist: Study FreeBSD concepts from this chapter's documentation and use Linux containers for your lab.
- If `/dev/kvm` exists but VMs still fail: Try using a separate QEMU/KVM setup outside of Incus, or test FreeBSD on a physical machine or separate virtualization host.
- Continue with Linux containers for your lab work.

## FreeBSD Resources

- [FreeBSD Handbook](https://docs.freebsd.org/en/books/handbook/)
- [FreeBSD Quickstart for Linux Users](https://docs.freebsd.org/en/books/handbook/quickstart/)
- [FreeBSD pkg](https://docs.freebsd.org/en/books/handbook/pkg/)
- [FreeBSD Jails](https://docs.freebsd.org/en/books/handbook/jails/)

## Exercise: FreeBSD Exploration (If VM Works)

First, verify VM support:

[WSL Debian]
```bash
# 1. Check KVM device
ls -la /dev/kvm

# If /dev/kvm does NOT exist:
#   Skip to "If VM Does Not Work" section below
#   Focus on studying FreeBSD concepts from the text
#   Use Linux containers for hands-on practice

# If /dev/kvm exists, continue:
# 2. Launch FreeBSD VM
incus launch images:freebsd/14.5 freebsd01 --vm -c security.secureboot=false
incus list  # wait for RUNNING
incus console freebsd01
[FreeBSD] uname -a
[FreeBSD] ifconfig
[FreeBSD] pkg update
[FreeBSD] pkg install -y vim-tiny
[FreeBSD] sysrc nginx_enable=YES
[FreeBSD] service nginx start
[FreeBSD] service nginx status
[FreeBSD] exit  (Ctrl+A Q)
[WSL Debian] incus stop freebsd01
[WSL Debian] incus delete --force freebsd01
```

## If FreeBSD VM Does Not Work

[WSL Debian]
```bash
# 1. Verify KVM device does not exist
ls -la /dev/kvm  # shows "No such file or directory"

# 2. Focus on conceptual learning
#    Read the "FreeBSD Basics for Linux Users" section above
#    Study the comparison tables and command differences

# 3. Continue with Linux containers for hands-on practice
#    Complete the other labs in this documentation
```

## Next Step

Continue to [Chapter 11 — Linux vs FreeBSD](11-linux-vs-freebsd.md) for a detailed comparison.

## Tested With

> **Tested with:**
> - Incus 6.x
> - FreeBSD 14.5 image (`images:freebsd/14.5`)
> - `/dev/kvm` available (VMs can run)
> - Windows 11 23H2
> - WSL2
>
> **Important:** If `/dev/kvm` is not available in your WSL environment, VMs (FreeBSD, Linux, Windows) cannot be launched in Incus. The conceptual learning in this chapter still applies — study the FreeBSD basics and comparison sections.
