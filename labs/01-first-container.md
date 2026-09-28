# Lab 1 — First Container

## Objective
Set up and explore a basic Incus container. Learn container lifecycle, file management, and networking.

## What You Will Learn

- How to launch an Incus container
- Basic container operations (start, stop, restart, delete)
- Viewing container information
- Accessing container file system
- Testing network connectivity

## Prerequisites

- Windows 11 with WSL2
- WSL2 Debian distribution
- Incus installed and initialised (see Chapter 4)

## Step 1

### Command

[WSL Debian]
```bash
incus launch images:debian/13 lab1-debian
```

### What happened?

- Incus creates a new instance named `lab1-debian`
- Downloads the Debian 13 image if not already cached
- Starts the container
- Gets an IP address on the `incusbr0` network

The output should look like:
```
Creating lab1-debian
Starting lab1-debian
```

## Step 2

### Command

[WSL Debian]
```bash
incus list
```

### What happened?

- Shows all Incus instances
- Displays the new container with status `RUNNING`
- Shows its IP address (e.g., `10.x.x.x`)

You should see something like:
```
+------------+---------+-----------+------+-----------+-----------+
|    NAME    |  STATE  |   IPV4    | IPV6 |   TYPE    | SNAPSHOTS |
+------------+---------+-----------+------+-----------+-----------+
| lab1-debian| RUNNING | 10.x.x.x  |      | CONTAINER | 0         |
+------------+---------+-----------+------+-----------+-----------+
```

## Step 3

### Command

[WSL Debian]
```bash
incus info lab1-debian
```

### What happened?

- Shows detailed information about the container
- Displays configuration, devices, resource usage, and status
- Shows the container's network interface details (eth0, IP address)

Focus on:
- **State**: RUNNING (or stopped)
- **IPV4**: The container's network address
- **Devices**: Storage, network, file systems

## Step 4

### Command

[WSL Debian]
```bash
incus exec lab1-debian -- bash
```

### What happened?

- Enters the container's interactive shell
- You are now inside the container as root
- The prompt will show: `root@lab1-debian:~#`

Note: You are now in a different environment from your WSL Debian host.

## Step 5

### Command

[Incus lab1-debian container]
```bash
cat /etc/os-release
hostname
ip addr
```

### What happened?

- `cat /etc/os-release`: Shows Debian version and details
- `hostname`: Shows the container's name
- `ip addr`: Shows the container's network interfaces

Look for the container's IP address (likely `10.x.x.x/24` on `eth0`).

## Step 6

### Command

[Incus lab1-debian container]
```bash
ls -la /root
```

### What happened?

- Shows the root user's home directory
- Explores container filesystem hierarchy

## Step 7

### Command

[Incus lab1-debian container]
```bash
apt update
apt install curl -y
```

### What happened?

- Updates Debian package lists
- Installs curl (command-line web tool)

## Step 8

### Command

[WSL Debian]
```bash
incus exec lab1-debian -- curl http://example.com
```

### What happened?

- Uses curl inside the container to fetch a website
- Tests container's internet connectivity

## Step 9

### Command

[Incus lab1-debian container]
```bash
exit
```

### What happened?

- Exits the container and returns to WSL Debian host
- The container continues running in the background

## Step 10

### Command

[WSL Debian]
```bash
incus stop lab1-debian
```

### What happened?

- Gracefully stops the container
- Container status changes to `STOPPED`

## Step 11

### Command

[WSL Debian]
```bash
incus start lab1-debian
```

### What happened?

- Starts the stopped container
- Container status becomes `RUNNING` again

## Step 12

### Command

[WSL Debian]
```bash
incus restart lab1-debian
```

### What happened?

- Restarts the running container
- Equivalent to stopping then starting again

## Step 13

### Command

[WSL Debian]
```bash
incus pause lab1-debian
```

### What happened?

- Pauses the container
- Container continues to use resources but processes are frozen
- You can unpause later

## Step 14

### Command

[WSL Debian]
```bash
incus unpause lab1-debian
```

### What happened?

- Resumes the container after it was paused
- Processes continue as before

## Step 15

### Command

[WSL Debian]
⚠️ **Warning: This deletes the container and all its data.**
```bash
incus delete --force lab1-debian
```

### What happened?

- Permanently removes the container and all its data
- Frees up storage space

## Expected Result

- Container lifecycle is understood (create, use, manage, delete)
- You have installed curl in the container
- You understand that containers are separate environments
- You understand basic networking inside Incus

## Questions

1. What is the difference between `incus exec` and SSH?
2. What is the IP address of your container?
3. What happens if you delete a container that is running?
4. What is the purpose of `apt update`?
5. Why do we use `apt install -y` in containers?

## Challenge

- Try creating another container: `incus launch images:debian/13 lab1-debian2`
- See if you can copy files between containers using `incus file push`

## Cleanup

(Already completed - container was deleted in Step 15)

To clean up any files created during this lab:

[WSL Debian]
```bash
# If you created files in WSL:
rm -f your-files.txt
```

[Incus lab1-debian container] (if not deleted)
```bash
# Remove any files you created:
rm -f temporary-files.txt
```

All container data is automatically removed when the container is deleted.