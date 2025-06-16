

This guide walks you through installing **Apache Maven 3.9.10** on a **Amazon Linux EC2 instance**, including Java installation, Maven setup, and environment configuration.

---

## 🛠 System Requirements

- **JDK**: 1.8 or above (Java is required before installing Maven)
- **Disk Space**: ~10MB for Maven + dependencies
- **OS**: Any Linux flavor; Tested on Amzon Linux EC2
- **Maven Version**: 3.9.10

> All external software is installed in the `/opt` directory.

---

## 📋 Prerequisites

- Must be **logged in as root user** or use `sudo`
- Internet connection required (to download Maven and Java)

---

## 📦 Step-by-Step Installation Instructions(Works On Any Linux Distribution)

### ✅ Step 1: Switch to Root User

```bash
sudo su -
```

---

### ✅ Step 2: Install Java (JDK)

You can install **Java 1.8**  or **Above**

### We Can Find Java Softwares Open JDK Versions Here:-  https://jdk.java.net/archive/


```bash
# For JDK 11
yum install wget -y

# In case of ubuntu or debian O.S
# apt install wget -y
wget https://download.java.net/java/GA/jdk11/9/GPL/openjdk-11.0.2_linux-x64_bin.tar.gz
tar -xzf openjdk-11.0.2_linux-x64_bin.tar.gz
mv jdk-11.0.2 /opt/java-11

# User Level (Exit as a Root User and Run as Regular/Normal User (Only It will work for current User)

echo 'export JAVA_HOME=/opt/java-11' >> ~/.bash_profile
echo 'export PATH=$PATH:$JAVA_HOME/bin' >> ~/.bash_profile

source ~/.bash_profile


# Sytem Level For All Users
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
sudo su -
yum install -y wget unzip

# In case of ubuntu or debian O.S
# apt install wget unzip -y
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

# User Level (Exit as a Root User and Run as Regular/Normal User (Only It will work for current User)

echo 'export M2_HOME=/opt/apache-maven-3.9.10' >> ~/.bash_profile
echo 'export PATH=$PATH:$M2_HOME/bin' >> ~/.bash_profile

source ~/.bash_profile

# Sytem Level For All Users

sudo tee /etc/profile.d/mvn.sh <<EOF
export M2_HOME=/opt/apache-maven-3.9.10
export PATH=$PATH:$M2_HOME/bin
EOF

source /etc/profile.d/mvn.sh

```

---

### ✅ Step 6: Verify Maven Installation

```bash
mvn -version
```

Expected output:

```
Apache Maven 3.9.10 (...)
Java version: 1.8.x or 11.x
```

---

## ⚠️ Common Errors & Fixes

| Error | Cause | Fix |
|-------|-------|-----|
| `mvn: command not found` | Environment not loaded | Run `source /etc/profile.d/mvn.sh` |
| `java: command not found` | Java not installed or  environment not loaded| Install Java Or Run `source /etc/profile.d/javav.sh`|
| Permission denied | Not root user | Use `sudo` or switch to root |

---

## Using Package Managers

### Amazon Linux 2

```bash
sudo yum update -y
sudo amazon-linux-extras enable corretto17
sudo yum install java-11-amazon-corretto -y
sudo yum install maven -y
java -version
javac -version
mvn --version
```

### Ubuntu (20.04 / 22.04 / 24.04)

```bash
sudo apt update
sudo apt install openjdk-11-jdk -y
sudo apt install maven -y
java -version
javac -version
mvn --version
```
