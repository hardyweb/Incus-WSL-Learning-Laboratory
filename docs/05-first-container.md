# Chapter 5 — First Incus Container

## Launch a Debian Container

The most common command to start a container:

[WSL Debian]
```bash
incus launch images:debian/13 debian01
```

### What This Command Does

| Part | Meaning |
|------|---------|
| `incus` | The Incus command-line tool |
| `launch` | Create and start a new instance |
| `images:debian/13` | **Image source**: `images:` = public image server, `debian/13` = Debian 13 image |
| `debian01` | **Instance name**: your chosen name for this container |

### Expected Output

```
Creating debian01
Starting debian01
```

If this is the first time using this image, Incus will download it (a few hundred MB). Subsequent launches are fast.

## List Containers

[WSL Debian]
```bash
incus list
```

### Expected Output

```
+----------+---------+---------------------+------+-----------+-----------+
| NAME     | STATE   | IPV4                | IPV6 | TYPE      | SNAPSHOTS |
+----------+---------+---------------------+------+-----------+-----------+
| debian01 | RUNNING | 10.10.10.10 (eth0)  |      | CONTAINER | 0         |
+----------+---------+---------------------+------+-----------+-----------+
```

- `NAME` — your instance name
- `STATE` — RUNNING, STOPPED, FROZEN
- `IPV4` — IP address assigned by Incus DHCP
- `TYPE` — CONTAINER or VIRTUAL-MACHINE

## Enter the Container

[WSL Debian]
```bash
incus exec debian01 -- bash
```

### What This Command Does

| Part | Meaning |
|------|---------|
| `incus exec` | Execute a command inside an instance |
| `debian01` | Target instance name |
| `--` | Separates Incus options from the command |
| `bash` | The command to run (interactive shell) |

### Inside the Container

Your prompt changes:
```
root@debian01:~#
```

Notice:
- You are now `root` inside the container
- Hostname is `debian01`
- You are in `/root` (root's home)

### Explore Inside the Container

[Incus Debian container]
```bash
cat /etc/os-release
```

Output:
```
PRETTY_NAME="Debian GNU/Linux 13 (trixie)"
NAME="Debian GNU/Linux"
VERSION_ID="13"
...
```

[Incus Debian container]
```bash
hostname
```
Output:
```
debian01
```

[Incus Debian container]
```bash
ip addr
```

Shows the container's network interface (typically `eth0`) with an IP like `10.10.10.10/24`.

[Incus Debian container]
```bash
exit
```

Returns you to the WSL Debian host.

## Key Distinction: Two Different Linux Environments

```
Windows 11
   │
   └── WSL2
        │
        └── Debian (host) — "WSL Debian"
             │
             └── Incus
                  │
                  └── Debian (container) — "Incus Debian container"
```

These are **completely separate**:

| Aspect | WSL Debian | Incus Debian Container |
|--------|------------|------------------------|
| Kernel | WSL2 kernel | Same WSL2 kernel (shared) |
| Filesystem | `/home/user`, `/etc`, etc. | Own isolated filesystem |
| Network | Windows-integrated | On `incusbr0` bridge |
| Processes | Your processes | Isolated process tree |
| Root access | Your user + sudo | Full root by default |

## Container Information

[WSL Debian]
```bash
incus info debian01
```

Shows detailed information:
- Configuration
- Devices
- Resource usage
- Snapshots

## Container Lifecycle Commands

### Stop a Container
[WSL Debian]
```bash
incus stop debian01
```

Graceful shutdown (sends SIGTERM, then SIGKILL after timeout).

### Start a Container
[WSL Debian]
```bash
incus start debian01
```

### Restart a Container
[WSL Debian]
```bash
incus restart debian01
```

### Delete a Container

Must be stopped first:
[WSL Debian]
```bash
incus stop debian01
incus delete debian01
```

Force delete (running or stopped):
[WSL Debian]
⚠️ **Warning: This deletes the container and all its data immediately.**
```bash
incus delete --force debian01
```

### Freeze/Unfreeze (Pause)

Freeze (pause processes, keep memory):
[WSL Debian]
```bash
incus pause debian01
```

Unfreeze:
[WSL Debian]
```bash
incus unpause debian01
```

## Run a Single Command Inside Container

Without entering interactive shell:
[WSL Debian]
```bash
incus exec debian01 -- cat /etc/os-release
```

[WSL Debian]
```bash
incus exec debian01 -- apt update
```

[WSL Debian]
```bash
incus exec debian01 -- ip addr
```

## Execute as Non-Root User

By default, `incus exec` runs as root. To run as a specific user:
[WSL Debian]
```bash
incus exec debian01 -- su - user
```

Or create a user first:
[WSL Debian]
```bash
incus exec debian01 -- adduser student
incus exec debian01 -- su - student
```

## Copy Files Between Host and Container

From host to container:
[WSL Debian]
```bash
incus file push ./local-file.txt debian01/home/user/remote-file.txt
```

From container to host:
[WSL Debian]
```bash
incus file pull debian01/etc/os-release ./container-os-release.txt
```

Pull a directory recursively:
[WSL Debian]
```bash
incus file pull -r debian01/etc/ ./container-etc/
```

## Exercise: Container Lifecycle

1. [WSL Debian] `incus launch images:debian/13 test01`
2. [WSL Debian] `incus list`
3. [WSL Debian] `incus exec test01 -- cat /etc/os-release`
4. [WSL Debian] `incus exec test01 -- ip addr`
5. [WSL Debian] `incus stop test01`
6. [WSL Debian] `incus list` (note STATE = STOPPED)
7. [WSL Debian] `incus start test01`
8. [WSL Debian] `incus list` (STATE = RUNNING)
9. [WSL Debian] `incus info test01`
10. [WSL Debian] `incus delete --force test01`
11. [WSL Debian] `incus list` (test01 is gone)

## Common Issues

### "Error: Instance already exists"
The name is taken. Choose a different name or delete the existing one.

### "Error: Image not found"
Check available images:
[WSL Debian]
```bash
incus image list images:
```

### Container has no IP
Wait a few seconds for DHCP, or restart:
[WSL Debian]
```bash
incus restart debian01
```

### Cannot exec into container
Check if it's running:
[WSL Debian]
```bash
incus list
```

### Behind a Proxy

If you are in a network that requires a proxy to access the internet (e.g., corporate, school, or university network), you may need to configure the proxy inside the container for `apt update` and `apt install` to work.

#### Temporary Proxy (for this session only)

Set the proxy environment variables inside the container:

[WSL Debian]
```bash
incus exec debian01 -- bash
```

Inside the container:
[Incus Debian container]
```bash
export http_proxy="http://proxy.example.com:8080"
export https_proxy="http://proxy.example.com:8080"
export HTTP_PROXY="$http_proxy"
export HTTPS_PROXY="$https_proxy"

# For apt
echo -e "Acquire::http::proxy \"http://proxy.example.com:8080\";
Acquire::https::proxy \"http://proxy.example.com:8080\";" | sudo tee /etc/apt/apt.conf.d/proxy.conf
```

Exit the container:
[Incus Debian container]
```bash
exit
```

#### Permanent Proxy (add to shell profile)

Add proxy settings to `~/.bashrc` inside the container:

[WSL Debian]
```bash
incus exec debian01 -- bash -c 'echo "export http_proxy=\"http://proxy.example.com:8080\"" >> ~/.bashrc'
incus exec debian01 -- bash -c 'echo "export https_proxy=\"http://proxy.example.com:8080\"" >> ~/.bashrc'
source ~/.bashrc
```

### Why This Matters

> Without proxy configuration, `apt update` and `apt install` will fail with connection errors inside the container.

> Check with your network administrator for the correct proxy address and port.

### Common Proxy Settings

| Variable | Purpose |
|----------|---------|
| `http_proxy` | HTTP proxy for `apt`, `git`, `curl`, etc. |
| `https_proxy` | HTTPS proxy for secure connections |
| `no_proxy` | Comma-separated list of hosts/domains to bypass proxy |

### Test After Configuration

[WSL Debian]
```bash
incus exec debian01 -- apt update
```

If this succeeds, the proxy is configured correctly.

## Next Step
Continue to [Chapter 6 — Working with Multiple Linux Distributions](06-linux-distributions.md) to try Ubuntu, Alpine, and more.