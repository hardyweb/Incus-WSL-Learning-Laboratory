# Chapter 9 — Programming Laboratory

## Introduction

This chapter uses Incus containers as **disposable programming environments**. Each container is isolated, so you can experiment with different languages, versions, and tools without affecting your main system.

## Setting Up a Container for Programming

Start with a Debian container (if you don't have one):

[WSL Debian]
```bash
incus launch images:debian/13 prog01
```

Enter the container:
[WSL Debian]
```bash
incus exec prog01 -- bash
```

## Installing Programming Languages

### PHP

[Incus Debian container]
```bash
apt install php-cli php-mysql php-mbstring -y
apt update
apt install php
```

Check version:
[Incus Debian container]
```bash
php --version
```

Create a simple PHP script:
```php
<?php
echo "Hello, World!\n";
echo "PHP is working.\n";
?>
```

Run it:
[Incus Debian container]
```bash
echo '<?php echo "Hello, World!\n"; ?>' > hello.php
php hello.php
```

### Python

[Incus Debian container]
```bash
apt install python3 python3-pip -y
apt update
apt install python3
```

Check version:
[Incus Debian container]
```bash
python3 --version
python3 -V
```

Check pip:
[Incus Debian container]
```bash
pip3 --version
```

> **Note on `venv`:** For basic Python programming (running scripts), you do **not** need to set up a virtual environment. However, when you start installing third-party packages with `pip` (e.g., `pip3 install requests`), you **should** use a virtual environment (`venv`) to keep your project dependencies isolated and avoid conflicts with the system Python.

#### Without venv (basic scripts only)

Run Python directly:
[Incus Debian container]
```bash
python3 hello.py
```

#### With venv (when using pip to install packages)

Create a virtual environment:
[Incus Debian container]
```bash
python3 -m venv myproject-env
source myproject-env/bin/activate
```

Now `pip3 install` will only affect this project's environment:
[Incus Debian container]
```bash
pip3 install requests
python3 myscript.py
```

Deactivate when done:
[Incus Debian container]
```bash
deactivate
```

Create a simple Python script:
```python
#!/usr/bin/env python3
print("Hello, World!")
print("Python is working.")

# Simple calculator
x = 5
y = 3
print(f"{x} + {y} = {x + y}")
print(f"{x} * {y} = {x * y}")
```

Run it:
[Incus Debian container]
```bash
cat > hello.py << 'EOF'
#!/usr/bin/env python3
print("Hello, World!")
print("Python is working.")

# Simple calculator
x = 5
y = 3
print(f"{x} + {y} = {x + y}")
print(f"{x} * {y} = {x * y}")
EOF
python3 hello.py
```

### Go (Golang)

**Go Installation Options**

Option 1: Debian repository (simpler):
[Incus Debian container]
```bash
apt install golang-go -y
apt update
apt install golang
```

Check version:
[Incus Debian container]
```bash
go version
```

Option 2: Official Go installation (recommended for latest version):
[Incus Debian container]
```bash
declare -r GO_VERSION="1.22.3"
cd /tmp
download_url="https://go.dev/dl/go${GO_VERSION}.linux-amd64.tar.gz"
wget -q $download_url
tar -C /usr/local -xzf "go${GO_VERSION}.linux-amd64.tar.gz"
rm "go${GO_VERSION}.linux-amd64.tar.gz"
echo "export PATH=$PATH:/usr/local/go/bin" >> ~/.bashrc
source ~/.bashrc
go version
```

Create a simple Go program:
```go
package main

import "fmt"

func main() {
    fmt.Println("Hello, World!")
    fmt.Println("Go is working.")
    
    // Simple calculation
    result := add(5, 3)
    fmt.Printf("5 + 3 = %d\n", result)
}

func add(a, b int) int {
    return a + b
}
```

Save as `hello.go`:
[Incus Debian container]
```bash
cat > hello.go << 'EOF'
package main

import "fmt"

func main() {
    fmt.Println("Hello, World!")
    fmt.Println("Go is working.")
    
    // Simple calculation
    result := add(5, 3)
    fmt.Printf("5 + 3 = %d\n", result)
}

func add(a, b int) int {
    return a + b
}
EOF
go run hello.go
```

### Node.js (JavaScript)

[Incus Debian container]
```bash
curl -fsSL https://deb.nodesource.com/setup_20.x | sudo -E bash -
sudo apt install -y nodejs
```

Check version:
[Incus Debian container]
```bash
node --version
npm --version
```

Create a simple Node.js script:
```javascript
console.log("Hello, World!");
console.log("Node.js is working.");

// Simple calculation
let x = 5;
let y = 3;
console.log(`${x} + ${y} = ${x + y}`);
```

Save as `hello.js`:
[Incus Debian container]
```bash
cat > hello.js << 'EOF'
console.log("Hello, World!");
console.log("Node.js is working.");

// Simple calculation
let x = 5;
let y = 3;
console.log(`${x} + ${y} = ${x + y}`);
EOF
node hello.js
```

### Git

[Incus Debian container]
```bash
apt install git -y
apt update
apt install git
```

Check version:
[Incus Debian container]
```bash
git --version
```

Initialize a simple Git repository:
[Incus Debian container]
```bash
mkdir my-project
cd my-project
git init
git config user.name "Student Name"
git config user.email "student@example.com"
```

Create a file:
[Incus Debian container]
```bash
cat > README.md << 'EOF'
# My Project

Hello, Git!

This is a test repository for learning.
EOF
```

Stage and commit:
[Incus Debian container]
```bash
git add README.md
git commit -m "Initial commit: Hello Git!"
```

Create a remote branch:
[Incus Debian container]
```bash
git branch feature-test
git push origin feature-test
```

## Why Use Disposable Programming Environments?

| Feature | Incus Container | Virtual Machine | Dual Boot |
|---------|----------------|----------------|-----------|
| Setup time | Seconds | Minutes | Reboot |
| Isolation | ✓ | ✓ | ✓ |
| Resource usage | Low | High | N/A |
| Cleanup | One command | One command | Format |
| Experimentation | Safe | Safe | Risky |

## Good Practices

### 1. Use Git in Containers

Start each project in a fresh container to:
- Test code across different environments
- Avoid polluting your workspace
- Learn with different package managers

### 2. Keep Projects Separate

Use `git init` inside each container for isolated project management.

### 3. Install Only What You Need

Minimal installations reduce attack surface and improve performance.

### 4. Version Management

Keep track of the container images used:
[WSL Debian]
```bash
incus image list images: | grep debian
```

### 5. Use Snapshots for Important Work

If you are developing a more complex project, take snapshots:
[WSL Debian]
```bash
incus snapshot create prog01 v1
```

Restore if needed:
[WSL Debian]
```bash
incus restore prog01 v1
```

## Exercise: Programming Environment Setup

1. [WSL Debian] `incus launch images:debian/13 code01`
2. [WSL Debian] `incus exec code01 -- bash`
3. [Incus code01] Install PHP: `apt update && apt install php-cli -y`
4. [Incus code01] Test PHP: `echo '<?php echo "PHP works"; ?>' | php`
5. [Incus code01] Install Python: `apt install python3 -y`
6. [Incus code01] Test Python: `python3 -c 'print("Hello, Python!")'`
7. [Incus code01] Install Go: `apt install golang-go -y`
8. [Incus code01] Test Go: `go version` and run a simple program
9. [Incus code01] Initialize a Git project: `git init project && cd project && git config user.name "Student" && echo "Test" > README && git add README && git commit -m "Initial commit"`
10. [Incus code01] `exit`
11. [WSL Debian] `incus stop code01`
12. [WSL Debian] `incus delete --force code01`

## Next Step

Continue to [Chapter 10 — FreeBSD Laboratory](10-freebsd-lab.md) to explore a different Unix-like operating system.

## Tested With

> **Tested with:**
> - Debian 13 container
> - PHP 8.x
> - Python 3.11
> - Go 1.22
> - Node.js 20.x
> - Git 2.45
>
> All commands may vary with different versions. Check package manager documentation for your versions.