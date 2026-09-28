# Lab 4 — Programming Environment

## Objective
Set up a container with multiple programming languages and tools, then compare them side by side.

## What You Will Learn

- Install multiple programming languages in one container
- Use Git for version control
- Run simple programs in PHP, Python, Go, and Node.js
- Understand why containers are useful for programming experiments

## Prerequisites

- Lab 1 completed
- Incus with networking working
- Basic Linux terminal usage

## Step 1

### Command

[WSL Debian]
```bash
incus launch images:debian/13 prog-lab
```

### What happened?

- Creates a Debian container named `prog-lab`
- Downloads Debian 13 image
- Starts container on incusbr0 network

## Step 2

### Command

[WSL Debian]
```bash
incus exec prog-lab -- bash
```

### What happened?

- Enters the container as root
- Prompt changes to: `root@prog-lab:~#`

## Step 3

### Command

[Incus prog-lab container]
```bash
# Update and install all languages
apt update
apt install -y php-cli python3 python3-pip golang-go nodejs npm git
```

### What happened?

- Updates package lists
- Installs all four languages plus Git

## Step 4

### Command

[Incus prog-lab container]

### PHP Test

```bash
php --version
echo '<?php echo "Hello PHP"; ?>' | php
```

### Python Test

```bash
python3 --version
python3 -c 'print("Hello Python")'
```

### Go Test

```bash
go version
go run -exec 'echo' - <<'EOF'
package main
import "fmt"
func main() { fmt.Println("Hello Go") }
EOF
```

### Node.js Test

```bash
node --version
node -e 'console.log("Hello Node.js")'
```

### Git Test

```bash
git --version
git config user.name "Student"
git config user.email "student@lab.local"
```

## Step 5

### Command

[Incus prog-lab container]

### PHP File

```bash
mkdir -p ~/programming
cat > ~/programming/hello.php << 'EOF'
<?php
echo "Language: PHP\n";
echo "Version: " . PHP_VERSION . "\n";
echo "Message: Hello from my programming lab!\n";
EOF
php ~/programming/hello.php
```

### Python File

```bash
cat > ~/programming/hello.py << 'EOF'
#!/usr/bin/env python3
print("Language: Python")
print("Version: ", __import__("sys").version)
print("Message: Hello from my programming lab!")
EOF
python3 ~/programming/hello.py
```

### Go File

```bash
cat > ~/programming/hello.go << 'EOF'
package main

import "fmt"

func main() {
    fmt.Println("Language: Go")
    fmt.Println("Version: ", version)
    fmt.Println("Message: Hello from my programming lab!")
}
EOF
go run ~/programming/hello.go 2>/dev/null || go run - <<'GOEOF'
package main
import "fmt"
func main() { fmt.Println("Hello from Go") }
GOEOF
```

### Node.js File

```bash
cat > ~/programming/hello.js << 'EOF'
console.log("Language: Node.js");
console.log("Version: ", process.version);
console.log("Message: Hello from Node.js lab!");
EOF
node ~/programming/hello.js
```

## Step 6

### Command

[Incus prog-lab container]

### Initialize Git

```bash
cd ~/programming
git init
git add hello.php hello.py hello.go hello.js
git config user.name "Student"
git config user.email "student@lab.local"
git commit -m "Add programming language test files"
git log --oneline
```

## Step 7

### Command

[WSL Debian]
```bash
exit
```

### What happened?

- Exits the container back to WSL Debian host
- Container continues running in background

## Step 8

### Command

[WSL Debian]
```bash
incus exec prog-lab -- bash -c "python3 -c 'print(2+2)'"
```

### What happened?

- Runs Python from WSL without entering the container

## Expected Result

- All four programming languages installed and tested
- Git initialized in the programming directory
- Can run programs in PHP, Python, Go, and Node.js
- Understand how containers consolidate different development environments

## Questions

1. What are the advantages of using one container vs separate containers for each language?
2. How does `git commit` work inside a container?
3. Why might you want to use `apt install` vs downloading binaries from the internet?
4. What is the difference between `python3` and `python`?

## Challenge

- Create a simple script that auto-detects which language is available and runs a test
- Write a README.md in the programming directory explaining the setup
- Test that `pip3 install` works inside the container

## Cleanup

[WSL Debian]
```bash
incus delete --force prog-lab
```

All container data is automatically removed.