# Chapter 8 — Running Services

## Running a Web Server Inside a Container

The first service example: an **Nginx** web server.

### Start a Container and Install Nginx

1. Launch a Debian container (if you don't have one):
[WSL Debian]
```bash
incus launch images:debian/13 webserver01
```

2. Enter the container:
[WSL Debian]
```bash
incus exec webserver01 -- bash
```

3. Install nginx:
[Incus Debian container]
```bash
apt update
apt install nginx
```

### Tip: Use tmux for Multiple Screens

When running services inside a container, it is often useful to have multiple terminal windows open at the same time:

- **Screen 1**: Inside the container (running the service, checking logs)
- **Screen 2**: On the WSL host (running `curl`, checking container IP, monitoring)

You can install **tmux** (terminal multiplexer) to manage multiple screens within one terminal window:

[WSL Debian]
```bash
sudo apt update
sudo apt install tmux
```

Then start a tmux session:
[WSL Debian]
```bash
tmux new -s webserver
```

Inside tmux, you can create multiple panes:
- Press `Ctrl+b` then `"` to split horizontally (top/bottom panes)
- Press `Ctrl+b` then `%` to split vertically (left/right panes)
- Press `Ctrl+b` followed by arrow keys to switch between panes
- Press `Ctrl+b` then `c` to create a new window
- Press `Ctrl+b` then `n`/`p` to switch windows
- Press `Ctrl+b` then `d` to detach from tmux (session keeps running)
- Reattach with: `tmux attach -t webserver`

**Suggested setup:**

| Pane | Location | Purpose |
|------|----------|---------|
| Top/Left | `incus exec webserver01 -- bash` | Run nginx, check logs |
| Bottom/Right | WSL host | Run `curl http://<container-ip>`, `incus list`, etc. |

This way you can see the service output and test it at the same time without opening multiple terminal windows.

### Why `apt` Works Without `sudo` Inside the Container

Inside an Incus container, you are already `root` by default. So `apt` commands do not need `sudo`.

> **Note:** Not all containers have `systemd` available. If `systemctl` does not work, see the "systemd in Containers" section below.

### Try the Web Server

Exit the container:
[Incus Debian container]
```bash
exit
```

Find the container IP:
[WSL Debian]
```bash
incus list
```

Access from WSL (not from Windows, unless port forwarding is configured):
[WSL Debian]
```bash
curl http://10.x.x.x
```
(Replace `10.x.x.x` with the container's IP)

Or from WSL terminal:
```bash
curl http://localhost:8080  # if port forwarding is configured
```

### What `localhost` Means

From inside the container: `localhost` refers to the container itself.

From WSL Debian host: `localhost` refers to the WSL Debian system, **not** the container.

From Windows: `localhost` refers to Windows, not the container or WSL.

To access a container service from WSL, use the container's IP address on the `incusbr0` network.

## Using systemd Inside Containers

### What is systemd?

**systemd** is the service manager that starts and stops services.

### Does Debian WSL Containers Have systemd?

It depends on the Debian version and how Incus was started.

**Common situation:** systemd is partially functional, but some features may not work.

### Try systemd in Container
[Incus Debian container]
```bash
systemctl status nginx
```

### If systemctl Reports an Error

Typical error: `Failed to connect to socket /run/systemd/system: Connection refused`

### Alternatives When systemd Is Not Fully Available

#### Manual Start

[Incus Debian container]
```bash
nginx
```

Check it's running:
[Incus Debian container]
```bash
ps aux | grep nginx
```

Or:
[Incus Debian container]
```bash
pgrep nginx
```

#### Using exec for One-off Commands

[WSL Debian]
```bash
incus exec webserver01 -- nginx
```

#### Restart

[WSL Debian]
```bash
incus restart webserver01
```

### Installing htop to Monitor Processes

[Incus Debian container]
```bash
apt install htop
htop
```

### When systemd Does Work (Debian 12+, WSL2)

Some newer configurations enable systemd. Try:

[Incus Debian container]
```bash
systemctl start nginx
systemctl enable nginx
```

If this works, excellent! But do not rely on it for all setups.

### Using supervisord as Alternative (if needed)

Some users install and use `supervisord` to manage processes.

[Incus Debian container]
```bash
apt install supervisor
```

But for simplicity, this guide uses manual start/stop or simple `apt install` services that may or may not use systemd.

## Example: Simple Python Web Server

No nginx needed. Python can serve files natively.

[Incus Debian container]
```bash
apt install python3 -y
```

Start a web server:
[Incus Debian container]
```bash
python3 -m http.server 8080
```

Or serve a specific directory:
[Incus Debian container]
```bash
cd /var/www/html
python3 -m http.server 8080
```

Access from WSL:
[WSL Debian]
```bash
curl http://10.x.x.x:8080
```

Exit the background process with `Ctrl+C`.

## Service Lifecycle Commands

### Restart the Container

[WSL Debian]
```bash
incus restart webserver01
```

### Re-enter Container

[WSL Debian]
```bash
incus exec webserver01 -- bash
```

### Stop the Container

[WSL Debian]
```bash
incus stop webserver01
```

### Delete the Container

[WSL Debian]
⚠️ **Warning: This deletes the container and all its data.**
```bash
incus delete --force webserver01
```

## Exercise: Running a Web Server

1. [WSL Debian] `incus launch images:debian/13 web01`
2. [WSL Debian] Optional: Install tmux for multi-screen management
   ```bash
   sudo apt update && sudo apt install tmux
   tmux new -s webserver
   # Split pane: Ctrl+B then "
   ```
3. [WSL Debian - Pane 1] `incus exec web01 -- bash`
4. [Incus web01] `apt update && apt install nginx`
5. [Incus web01] `nginx` (start manually)
6. [WSL Debian - Pane 2] Find the IP: `incus list`
7. [WSL Debian - Pane 2] `curl http://10.x.x.x`
8. [Incus web01 - Pane 1] Press `Ctrl+C` to stop nginx
9. [Incus web01 - Pane 1] `exit`
10. [WSL Debian] `tmux kill-session -t webserver`
11. [WSL Debian] `incus stop web01`
12. [WSL Debian] `incus delete --force web01`

## Next Step

Continue to [Chapter 9 — Programming Laboratory](09-programming-lab.md) to install programming languages and tools.

## Tested With

> **Tested with:**
> - Debian 13 container
> - Nginx standard install
> - Internet access required for `apt install nginx`
> - tmux for multi-terminal management (optional)
>
> Note: If `systemctl` is unavailable, use manual start (`nginx`) and `incus restart` to restart the container.