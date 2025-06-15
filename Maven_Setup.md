

This guide walks you through installing **Apache Maven 3.9.9** on a **Red Hat Linux EC2 instance**, including Java installation, Maven setup, and environment configuration.

---

## 🛠 System Requirements

- **JDK**: 1.8 or above (Java is required before installing Maven)
- **Disk Space**: ~10MB for Maven + dependencies
- **OS**: Any Linux flavor; tested on Red Hat EC2
- **Maven Version**: 3.9.9

> All external software is installed in the `/opt` directory.

---

## 📋 Prerequisites

- Must be **logged in as root user** or use `sudo`
- Internet connection required (to download Maven and Java)

---

## 📦 Step-by-Step Installation Instructions

### ✅ Step 1: Switch to Root User

```bash
sudo su -
```

---

### ✅ Step 2: Install Java (JDK)

You can install **Java 1.8** (default) or **Java 11**:

```bash
# For JDK 11
sudo yum install wget -y
wget https://download.java.net/openjdk/jdk11.0.0.2/ri/openjdk-11.0.0.2_linux-x64.tar.gz
tar -xzf openjdk-11.0.0.2_linux-x64.tar.gz
sudo mv jdk-11.0.0.2 /opt/java-11

sudo tee /etc/profile.d/java.sh <<EOF
export JAVA_HOME=/opt/java-11
export PATH=\$JAVA_HOME/bin:\$PATH
EOF

source /etc/profile.d/java.sh

```

✅ Check the version:

```bash
java -version
javac -version
```
---

### ✅ Step 3: Prepare Environment

Install required tools and go to the `/opt` directory:

```bash
cd /opt
yum install -y wget unzip
```
---

### ✅ Step 4: Download and Extract Maven

```bash
wget https://dlcdn.apache.org/maven/maven-3/3.9.10/binaries/apache-maven-3.9.10-bin.zip
unzip apache-maven-3.9.10-bin.zip
```

---

### ✅ Step 5: Set Environment Variables

Instead of updating user-specific profiles, we’ll set up a global script:

```bash
vi /etc/profile.d/maven.sh
```

Paste the following into the file:

```bash
export M2_HOME=/opt/apache-maven-3.9.10
export PATH=$PATH:$M2_HOME/bin
```

Make the script executable and apply it:

```bash
chmod +x /etc/profile.d/maven.sh
source /etc/profile.d/maven.sh
```

---

### ✅ Step 6: Verify Maven Installation

```bash
mvn -version
```

Expected output:

```
Apache Maven 3.9.9 (...)
Java version: 1.8.x or 11.x
```

---

## ⚠️ Common Errors & Fixes

| Error | Cause | Fix |
|-------|-------|-----|
| `mvn: command not found` | Environment not loaded | Run `source /etc/profile.d/maven.sh` |
| `java: command not found` | Java not installed | Install Java using yum |
| Permission denied | Not root user | Use `sudo` or switch to root |

---

##  Bonus: Make It a Script

### Here is a portable shell script to install OpenJDK 17 manually (without a package manager) on Amazon Linux, RedHat, or Ubuntu.

```bash
#!/bin/bash
# === Configuration ===

#JDK_VERSION="11"
#JDK_DIR="jdk-11.0.2"
#JDK_TARBALL="openjdk-11.0.2_linux-x64_bin.tar.gz"
#JDK_DOWNLOAD_URL="https://download.java.net/java/GA/jdk11/9/GPL/${JDK_TARBALL}"
#INSTALL_DIR="/opt/jdk"

JDK_VERSION="17"
JDK_DIR="jdk-17"
JDK_TARBALL="openjdk-${JDK_VERSION}_linux-x64_bin.tar.gz"
JDK_DOWNLOAD_URL="https://download.java.net/java/GA/jdk17/0d483a097f51456b8dd33c965829f0b4/35/GPL/${JDK_TARBALL}"
INSTALL_DIR="/opt/jdk"

# === Step 1: Download JDK tar.gz ===
echo "Downloading JDK ${JDK_VERSION}..."
mkdir -p /tmp/jdk-install
cd /tmp/jdk-install || exit 1

if [ ! -f "${JDK_TARBALL}" ]; then
    curl -O "${JDK_DOWNLOAD_URL}"
    if [ $? -ne 0 ]; then
        echo "Failed to download JDK tarball."
        exit 1
    fi
fi

# === Step 2: Extract to /opt/jdk ===
echo "Installing to ${INSTALL_DIR}..."
sudo mkdir -p "${INSTALL_DIR}"
sudo tar -xvzf "${JDK_TARBALL}" -C "${INSTALL_DIR}"

# === Step 3: Set Environment Variables ===
echo "Setting up environment variables..."
sudo tee /etc/profile.d/jdk.sh > /dev/null <<EOF
export JAVA_HOME=${INSTALL_DIR}/${JDK_DIR}
export PATH=\$JAVA_HOME/bin:\$PATH
EOF

sudo chmod +x /etc/profile.d/jdk.sh
source /etc/profile.d/jdk.sh

# === Step 4: Verify ===
echo "Verifying installation..."
java -version
javac -version

echo "JDK ${JDK_VERSION} installed successfully."
```

### How to Use:

- Save as script:

```bash
vi install_jdk17_manual.sh
# Paste the script and save
```

- Make executable and run:

```bash
chmod +x install_jdk17_manual.sh
./install_jdk17_manual.sh
```

### Notes:
- Requires sudo to extract to /opt.
- Works on any Linux with curl, tar, and basic utilities.
- Installs only OpenJDK 17 (can be modified for other versions).

### You can automate the Maven Installation process using this script

```bash
#!/bin/bash

# Run as: sudo ./install-maven.sh

yum install -y java-1.8.0-openjdk-devel wget unzip
cd /opt
wget https://dlcdn.apache.org/maven/maven-3/3.9.9/binaries/apache-maven-3.9.9-bin.zip
unzip apache-maven-3.9.9-bin.zip

echo 'export M2_HOME=/opt/apache-maven-3.9.9' > /etc/profile.d/maven.sh
echo 'export PATH=$PATH:$M2_HOME/bin' >> /etc/profile.d/maven.sh

chmod +x /etc/profile.d/maven.sh
source /etc/profile.d/maven.sh

mvn -version
```

Save this as `install-maven.sh`, make it executable (`chmod +x install-maven.sh`), and run it using `sudo`.

---
## Using Linux Package Managers

### Amazon Linux 2

```bash
sudo yum update -y
sudo amazon-linux-extras enable corretto17
sudo yum install java-17-amazon-corretto -y
sudo yum install maven -y
java -version
javac -version
mvn --version
```
### Red Hat Enterprise Linux (RHEL 8/9)
```bash
sudo dnf install java-17-openjdk-devel -y
sudo dnf install maven -y
java -version
javac -version
mvn --version
```

### Ubuntu (20.04 / 22.04 / 24.04)

```bash
sudo apt update
sudo apt install openjdk-17-jdk -y
sudo apt install maven -y
java -version
javac -version
mvn --version
```
---
## ✅ Summary

| Task | Command |
|------|---------|
| Install Java | `yum install java-1.8.0-openjdk-devel -y` |
| Download Maven | `wget ...` |
| Unzip | `unzip apache-maven-3.9.9-bin.zip` |
| Set Env Vars | `vi /etc/profile.d/maven.sh` |
| Load Env | `source /etc/profile.d/maven.sh` |
| Verify | `mvn -version` |
---
