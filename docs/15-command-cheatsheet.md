# Chapter 15 — Command Cheat Sheet

## WSL Commands

### Basic WSL Management
[Windows PowerShell]
```powershell
wsl                          # Start default distribution
wsl -d Debian                # Start specific distribution
wsl --list --verbose         # List distributions with versions
wsl --shutdown               # Stop all WSL instances
wsl --terminate Debian       # Stop specific distribution
wsl --set-version Debian 2   # Convert to WSL2
wsl --set-default-version 2  # Set default to WSL2
wsl --update                 # Update WSL kernel
wsl --status                 # Show WSL status
```

### File Access
[Windows PowerShell]
```powershell
wsl ls /home/user/           # List Linux files from Windows
wsl cat /etc/os-release      # View Linux file from Windows
explorer.exe \\wsl$\Debian\  # Open in File Explorer
```

## Incus Commands

### Instance Management
[WSL Debian]
```bash
incus launch <image> <name>          # Create and start instance
incus list                            # List all instances
incus info <name>                     # Detailed instance info
incus start <name>                    # Start stopped instance
incus stop <name>                     # Stop running instance
incus restart <name>                  # Restart instance
incus delete <name>                   # Delete stopped instance
incus delete --force <name>           # Delete running/stopped instance
incus exec <name> -- <command>        # Run command in instance
incus exec <name> -- bash             # Enter instance shell
incus console <name>                  # Connect to console (VMs)
incus snapshot create <name> <snap>   # Create snapshot
incus snapshot list <name>            # List snapshots
incus snapshot restore <name> <snap>  # Restore snapshot
incus snapshot delete <name> <snap>   # Delete snapshot
```

### Images
[WSL Debian]
```bash
incus image list                       # List all images
incus image list images:               # List remote images
incus image copy images:debian/13 local: --alias debian13  # Copy image
incus image alias create images:debian/13 debian13         # Create alias
incus image delete <alias|fingerprint> # Delete image
```

### Networks
[WSL Debian]
```bash
incus network list                      # List networks
incus network show <network>            # Show network details
incus network create <name>             # Create network
incus network delete <name>             # Delete network
incus network set <name> <key> <value>  # Set network config
```

### Storage
[WSL Debian]
```bash
incus storage list                      # List storage pools
incus storage show <pool>               # Show storage details
incus storage volume list <pool>        # List volumes in pool
incus storage volume create <pool> <vol> # Create volume
```

### Profiles
[WSL Debian]
```bash
incus profile list                      # List profiles
incus profile show <profile>            # Show profile details
incus profile create <name>             # Create profile
incus profile edit <name>               # Edit profile in editor
incus profile device add <profile> <name> <type> [key=value]... # Add device
incus profile device remove <profile> <name> # Remove device
```

### Other Useful Commands
[WSL Debian]
```bash
incus version                           # Show version
incus monitor                           # Monitor events (Ctrl+C to stop)
incus admin init                        # Re-initialise Incus
incus admin init --dump                 # Show current config
incus admin shutdown                    # Shutdown Incus daemon
incus admin init --storage <driver>     # Initialise with specific storage
```

## Linux Commands

### File System
[WSL Debian]
```bash
pwd                        # Print working directory
ls [-la]                   # List files
cd <directory>             # Change directory
mkdir [-p] <dir>           # Create directory
touch <file>               # Create empty file
cp [-r] <src> <dest>       # Copy file/directory
mv <src> <dest>            # Move/rename file
rm [-rf] <file|dir>        # Remove file/directory
cat <file>                 # Display file
less <file>                # View file page by page
head [-n] <file>           # First lines of file
tail [-n] <file>           # Last lines of file
grep <pattern> <file>      # Search in file
find <path> -name <name>   # Find files
```

### System Information
[WSL Debian]
```bash
whoami                     # Current username
hostname                   # System hostname
uname -a                   # Kernel and system info
date                       # Current date/time
uptime                     # System uptime
df [-h]                    # Disk space usage
du [-sh] <dir>             # Directory size
free [-h]                  # Memory usage
ps aux                     # List all processes
top                        # Interactive process viewer
htop                       # Enhanced process viewer (install first)
ip addr                    # Show IP addresses
ip route                   # Show routing table
ss -tulpn                  # Show listening ports
```

### Package Management (Debian/Ubuntu)
[WSL Debian]
```bash
sudo apt update            # Update package lists
sudo apt upgrade           # Upgrade installed packages
sudo apt install <pkg>     # Install package
sudo apt remove <pkg>      # Remove package
sudo apt purge <pkg>       # Remove package and config
sudo apt autoremove        # Remove unused dependencies
apt search <term>          # Search for packages
apt show <pkg>             # Show package details
dpkg -L <pkg>              # List files in package
dpkg -i <deb-file>         # Install .deb package
```

### Users and Groups
[WSL Debian]
```bash
id                         # Show user and group IDs
groups                     # Show group membership
adduser <username>         # Add new user
deluser <username>         # Delete user
addgroup <groupname>       # Add new group
delgroup <groupname>       # Delete group
usermod -aG <group> <user> # Add user to group
su - <username>            # Switch user
sudo <command>             # Run command as root
```

### Service Management (systemd)
[WSL Debian]
```bash
systemctl status <service> # Check service status
systemctl start <service>  # Start service
systemctl stop <service>   # Stop service
systemctl restart <service># Restart service
systemctl reload <service> # Reload service config
systemctl enable <service> # Enable at boot
systemctl disable <service># Disable at boot
systemctl list-units --type=service --state=running # List running services
journalctl -u <service>    # View service logs
journalctl -f              # Follow logs in real-time
journalctl --since "1 hour ago" # Logs from last hour
```

### Networking
[WSL Debian]
```bash
ip addr show               # Show network interfaces
ip link show               # Show link status
ip route show              # Show routing table
ip neigh                   # Show ARP/ND table
ping [-c count] <host>     # Test connectivity
traceroute <host>          # Trace route to host
dig <domain>               # DNS lookup
nslookup <domain>          # DNS lookup
host <domain>              # DNS lookup
ss [-tulpn]                # Show sockets
netstat [-tulpn]           # Legacy socket stats
```

### File Permissions
[WSL Debian]
```bash
ls -l <file>               # Show permissions
chmod [<who>][<op>][<perm>] <file>  # Change permissions
chmod 755 <file>           # rwxr-xr-x
chmod 644 <file>           # rw-r--r--
chmod 600 <file>           # rw-------
chown <user>:<group> <file># Change owner/group
chown -R <user>:<group> <dir># Change recursively
umask                      # Show/default file creation mask
```

### Process Management
[WSL Debian]
```bash
bg                         # Put job in background
fg                         # Bring job to foreground
jobs                       # List background jobs
kill <PID>                 # Send signal to process
kill -9 <PID>              # Force kill process
pkill <pattern>            # Kill processes by name
pgrep <pattern>            # Find processes by name
nice <command>             # Run with low priority
renice <priority> <PID>    # Change process priority
```

### Text Processing
[WSL Debian]
```bash
cat <file> | grep <pattern>     # Search in file
grep -r <pattern> <dir>         # Recursive search
sort <file>                     # Sort lines
uniq <file>                     # Remove duplicate lines
cut -d: -f1 <file>              # Extract first column (colon-delimited)
awk '{print $1}' <file>         # Print first field
sed 's/old/new/g' <file>        # Replace text
```

### Archives and Compression
[WSL Debian]
```bash
tar -czf archive.tar.gz <dir>   # Create gzipped tar
tar -xzf archive.tar.gz         # Extract gzipped tar
tar -cjf archive.tar.bz2 <dir>  # Create bzipped tar
tar -xjf archive.tar.bz2        # Extract bzipped tar
gzip <file>                     # Compress file
gunzip <file.gz>                # Decompress file
zip -r archive.zip <dir>        # Create zip archive
unzip archive.zip               # Extract zip archive
```

### Text Editors
[WSL Debian]
```bash
nano <file>                   # Simple editor
vim <file>                    # Vi Improved editor
# Basic vim commands:
# i = insert mode, Esc = normal mode
# :w = save, :q = quit, :wq = save and quit
# dd = delete line, yy = yank line, p = paste
```

## Programming Language Commands

### PHP
[WSL Debian]
```bash
php --version               # Show PHP version
php -m                      # Show loaded modules
php -i                      # Show PHP info
php file.php                # Run PHP script
php -l file.php             # Check PHP syntax
php -r 'echo "Hello\n";'    # Run PHP code inline
```

### Python
[WSL Debian]
```bash
python3 --version           # Show Python version
pip3 list                   # Show installed packages
pip3 install <package>      # Install package
pip3 uninstall <package>    # Uninstall package
python3 file.py             # Run Python script
python3 -m http.server 8000 # Simple web server
python3 -m venv venv        # Create virtual environment
source venv/bin/activate    # Activate virtualenv
```

### Go
[WSL Debian]
```bash
go version                  # Show Go version
go env                      # Show Go environment
go run file.go              # Run Go program
go build file.go            # Build Go binary
go test ./...               # Run tests
go get <module>             # Install module
go mod init <module>        # Initialize module
go mod tidy                 # Clean up dependencies
```

### Node.js
[WSL Debian]
```bash
node --version              # Show Node.js version
npm --version               # Show npm version
npm list                    # Show installed packages
npm install <package>       # Install package
npm install -g <package>    # Install globally
npm uninstall <package>     # Uninstall package
node file.js                # Run Node.js script
npm init                    # Initialize package.json
npm start                   # Run start script
```

### Git
[WSL Debian]
```bash
git --version               # Show Git version
git init                    # Initialize repository
git clone <url>             # Clone repository
git add <file>              # Stage file
git commit -m "message"     # Commit changes
git status                  # Show status
git log                     # Show commit history
git branch                  # List branches
git checkout <branch>       # Switch branch
git merge <branch>          # Merge branch
git push                    # Push to remote
git pull                    # Pull from remote
git remote -v               # Show remotes
git stash                   # Stash changes
git stash pop               # Apply stashed changes
```

## Miscellaneous Useful Commands

### System Information
[WSL Debian]
```bash
lscpu                       # CPU information
lsblk                       # Block devices
lspci                       # PCI devices
lsusb                       # USB devices
dmidecode                   # Hardware information (needs sudo)
```

### Text and Data
[WSL Debian]
```bash
wc -l <file>                # Count lines
echo "text"                 # Print text
printf "format" <args>      # Formatted print
base64 <file>               # Encode/decode base64
md5sum <file>               # Calculate MD5 checksum
sha256sum <file>            # Calculate SHA256 checksum
diff <file1> <file2>        # Compare files
patch <file> <patch>        # Apply patch
```

### Date and Time
[WSL Debian]
```bash
date                        # Show current date/time
date +%Y-%m-%d              # Format date
date -d "tomorrow"          # Relative date
timedatectl                 # Show time settings
hwclock                     # Hardware clock
```

### User Environment
[WSL Debian]
```bash
env                         # Show environment variables
printenv <VAR>              # Show specific variable
export VAR="value"          # Set environment variable
alias ll='ls -la'           # Create alias
unalias ll                  # Remove alias
history                     # Show command history
!!                          # Repeat last command
!$                          # Last argument of previous command
```

## Keyboard Shortcuts (Terminal)

### General
- `Ctrl+C` - Interrupt current command
- `Ctrl+D` - End of input (exit shell)
- `Ctrl+Z` - Suspend process (fg to resume)
- `Ctrl+A` - Move to beginning of line
- `Ctrl+E` - Move to end of line
- `Ctrl+U` - Delete line before cursor
- `Ctrl+K` - Delete line after cursor
- `Ctrl+R` - Search command history
- `Tab` - Auto-complete

### Navigation
- `Page Up/Down` - Scroll terminal
- `Shift+Page Up/Down` - Scroll terminal (alternative)
- `mouse wheel` - Scroll terminal

## Exercise: Command Practice

Practice these command combinations:

1. [WSL Debian] `ps aux | grep nginx | grep -v grep | awk '{print $2}' | xargs kill`
2. [WSL Debian] `find /etc -name "*.conf" -type f | head -5`
3. [WSL Debian] `df -h | grep '/dev/' | awk '{print $1, $5}'`
4. [WSL Debian] `cut -d: -f1,7 /etc/passwd | sort`
5. [WSL Debian] `history | tail -10 | tac`

## Next Step

Continue to [Chapter 16 — Glossary](16-glossary.md) for term definitions.

## Tested With

> **Tested with:**
> - Debian 13 containers
> - Incus 6.x
> - Standard Linux commands
>
> Command availability may vary based on installed packages. Install packages as needed (e.g., `htop`, `tree`, `vim`).