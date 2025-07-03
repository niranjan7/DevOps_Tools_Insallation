
# Jenkins & Java 21 Installation Guide on Linux EC2

This guide covers step-by-step installation instructions for **Jenkins** with **Java 21 (Temurin)** on the following Linux distributions running on AWS EC2 instances:

- Amazon Linux 2 (2023)
- RHEL 8/9
- Ubuntu 20.04/22.04

---

## ✅ Common Prerequisites

- EC2 instance with public IP
- Inbound rule for **TCP port 8080**
- `sudo` privileges
- Update OS:
  ```bash
  # Amazon & RHEL:
  sudo yum update -y
  # Ubuntu:
  sudo apt update -y
  ```

---

## Amazon Linux 2 (2023)

### Step 1: Install Java 21 (Temurin)
```bash
sudo rpm --import https://packages.adoptium.net/artifactory/api/gpg/key/public

sudo tee /etc/yum.repos.d/temurin.repo <<EOF
[Temurin]
name=Temurin OpenJDK
baseurl=https://packages.adoptium.net/artifactory/rpm/amazonlinux/2/x86_64
enabled=1
gpgcheck=1
gpgkey=https://packages.adoptium.net/artifactory/api/gpg/key/public
EOF

sudo yum update -y
  
sudo yum install -y temurin-21-jdk

java -version
```

### Step 2: Install Jenkins
```bash
sudo wget -O /etc/yum.repos.d/jenkins.repo https://pkg.jenkins.io/redhat-stable/jenkins.repo
sudo rpm --import https://pkg.jenkins.io/redhat-stable/jenkins.io-2023.key

sudo yum install -y jenkins
sudo systemctl enable jenkins
sudo systemctl start jenkins
```

---

##  RHEL 8/9

### Step 1: Install Java 21 (Temurin)
```bash
sudo rpm --import https://packages.adoptium.net/artifactory/api/gpg/key/public

sudo tee /etc/yum.repos.d/temurin.repo <<EOF
[Temurin]
name=Temurin OpenJDK
baseurl=https://packages.adoptium.net/artifactory/rpm/centos/8/x86_64
enabled=1
gpgcheck=1
gpgkey=https://packages.adoptium.net/artifactory/api/gpg/key/public
EOF

sudo dnf update -y

sudo dnf install -y temurin-21-jdk

java -version
```

### Step 2: Install Jenkins
```bash
sudo wget -O /etc/yum.repos.d/jenkins.repo https://pkg.jenkins.io/redhat-stable/jenkins.repo
sudo rpm --import https://pkg.jenkins.io/redhat-stable/jenkins.io-2023.key

sudo dnf install -y jenkins
sudo systemctl enable --now jenkins
sudo systemctl start jenkins
```

---

## Ubuntu 20.04/22.04

### Step 1: Install Java 21 (Temurin)
```bash
sudo apt install -y wget gnupg software-properties-common apt-transport-https

wget -O - https://packages.adoptium.net/artifactory/api/gpg/key/public | sudo gpg --dearmor -o /usr/share/keyrings/adoptium.gpg

echo "deb [signed-by=/usr/share/keyrings/adoptium.gpg] https://packages.adoptium.net/artifactory/deb $(lsb_release -cs) main" | sudo tee /etc/apt/sources.list.d/adoptium.list

sudo apt update -y
sudo apt install -y temurin-21-jdk
java -version
```

### Step 2: Install Jenkins
```bash
curl -fsSL https://pkg.jenkins.io/debian-stable/jenkins.io-2023.key | sudo tee /usr/share/keyrings/jenkins-keyring.asc > /dev/null

echo deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc] \
  https://pkg.jenkins.io/debian-stable binary/ | sudo tee \
  /etc/apt/sources.list.d/jenkins.list > /dev/null

sudo apt update
sudo apt install -y jenkins
sudo systemctl enable --now jenkins
sudo systemctl start jenkins
```

---

## 🛠 Access Jenkins

- URL: `http://<EC2-IP>:8080`
- Unlock password:
  ```bash
  sudo cat /var/lib/jenkins/secrets/initialAdminPassword
  ```
