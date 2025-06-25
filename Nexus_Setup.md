
# Nexus 3.80.X Setup on Linux

## Prerequisites

| Requirement         | Details                                                            |
| ------------------- | ------------------------------------------------------------------ |
| **OS**              | Linux (e.g., RHEL, CentOS, Amazon Linux, Ubuntu)                   |
| **User**            | Non-root user with `sudo` privileges                               |
| **Java**            | Java 17 (Nexus 3.80.x is bundled with Temurin JDK)                 |
| **RAM**             | Minimum **2 GB** (Recommended: 8 GB+)                              |
| **Disk Space**      | Minimum **10 GB free** (Recommended: 30+ GB for repositories)      |
| **Ports**           | **8081** (default port, configurable) open and allowed by firewall |
| **Internet Access** | Required for downloading Nexus and dependencies                    |

---

## Download Java 17 Or Above For Nexus 3.80.X

```bash
cd /opt
sudo wget https://download.java.net/openjdk/jdk17.0.0.1/ri/openjdk-17.0.0.1+2_linux-x64_bin.tar.gz
sudo tar -xvzf openjdk-17.0.0.1+2_linux-x64_bin.tar.gz
sudo rm openjdk-17.0.0.1+2_linux-x64_bin.tar.gz
sudo mv jdk-17.0.0.1 java-17
sudo ln -s /opt/java-17/bin/java /usr/bin/java
echo 'export JAVA_HOME=/opt/java-17' | sudo tee -a /etc/profile
echo 'export PATH=$PATH:$JAVA_HOME/bin' | sudo tee -a /etc/profile
source /etc/profile

java -version
```

---

## Install Nexus 3.81.1

```bash
cd /opt
sudo wget https://download.sonatype.com/nexus/3/nexus-3.81.1-01-linux-x86_64.tar.gz
sudo tar -xzf nexus-3.81.1-01-linux-x86_64.tar.gz
sudo mv nexus-3.81.1-01 nexus

sudo useradd nexus -r -d /opt/nexus
sudo chown -R nexus:nexus /opt/nexus /opt/sonatype-work
```

---

## Start & Stop Nexus

```bash
/opt/nexus/bin/nexus start
/opt/nexus/bin/nexus stop
```

---

## Configure Nexus as a Systemd Service

```bash
sudo tee /etc/systemd/system/nexus.service > /dev/null <<EOF
[Unit]
Description=Nexus Repository Manager 3.81.1
After=network.target

[Service]
Type=forking
User=nexus
LimitNOFILE=65536
ExecStart=/opt/nexus/bin/nexus start
ExecStop=/opt/nexus/bin/nexus stop
Restart=on-failure

[Install]
WantedBy=multi-user.target
EOF

sudo systemctl daemon-reload
sudo systemctl enable nexus
sudo systemctl start nexus
```

---

## Nexus 3.80.x Directory Structure Overview

```plaintext
nexus-3.80.1-01/
├── bin/
│   ├── nexus
│   └── nexus.vmoptions
├── etc/
│   ├── nexus-default.properties
│   └── ...
├── jdk/
│   └── temurin_17.x.x_xx_linux_x64/
│       └── jdk-17.0.x+xx/
├── lib/
│   └── ...
├── nexus/
│   └── system/
├── public/
├── tmp/
└── NOTICE.txt

sonatype-work/nexus3/
├── blobs/
├── db/
├── elasticsearch/
├── keystores/
├── log/
├── metrics/
├── tmp/
└── cache/
```

---

## Notes

- Nexus runs as a Java application, launched via scripts in `bin/`.
- `nexus.vmoptions` controls JVM memory and GC settings.
- Backup the `sonatype-work/nexus3/` directory for recovery, **not** the extracted Nexus folder.
- Default port is `8081`, configured in `etc/nexus-default.properties`.

---

## Nexus Repository Manager 3.80.x is Bundled with Temurin JDK

### ✅ Meaning

Nexus includes the Eclipse Temurin JDK within its TAR/ZIP distribution.

### 🔍 What is Eclipse Temurin JDK?

- Free, open-source build of OpenJDK by Eclipse Adoptium
- Production-grade and widely adopted
- Alternatives: Oracle JDK, Amazon Corretto, Red Hat OpenJDK

### 📦 Example Structure

```plaintext
nexus-3.80.1-01/
├── bin/
├── etc/
├── jdk/
│   └── temurin_17.x.x_xx_linux_x64/
│       └── jdk-17.0.x+xx/
├── lib/
└── nexus/
```

### 🚀 Benefits

| Feature                  | Description                                                |
|--------------------------|------------------------------------------------------------|
| ✅ No external Java      | Already included                                           |
| ✅ Version compatibility | Uses supported JDK version                                 |
| ✅ Easier deployments    | Ideal for Docker and automation                            |

### ⚙️ How It Works

```bash
./bin/nexus start
```

Uses the bundled JDK instead of system Java or `JAVA_HOME`.

### 📝 Important

- Do not delete or modify the `jdk/` directory.
- Use `JAVA_HOME` or `nexus.vmoptions` to override JDK if needed.
