# Tomcat Multi-Instance Setup on Linux

## Overview

This project demonstrates how to:

- Install Java and Tomcat
- Create two Tomcat instances (`instance1`, `instance2`)
- Configure ports for the second instance
- Deploy a sample `.war` application
- Assign different Java versions to each instance
- Create a systemd service unit for managing Tomcat instances

## Directory Structure

```
/opt/
├── java/
│   ├── jdk-19.0.1/
│   └── jdk-20.0.1/
├── tomcat/
│   ├── apache-tomcat-9.0.102/   # base tomcat
│   ├── instance1/
│   └── instance2/
```

## Step-by-Step Instructions

### 1. Create Necessary Directories

```bash
cd /opt/
mkdir java tomcat instance1 instance2
```

### 2. Create Tomcat User

```bash
useradd tomcat
passwd tomcat
chown tomcat:tomcat tomcat/ -R
```

### 3. Install Java

```bash
cd /opt/java
wget https://download.java.net/java/GA/jdk22.0.1/.../openjdk-22.0.1_linux-x64_bin.tar.gz
tar -xvf openjdk-22.0.1_linux-x64_bin.tar.gz
```

### 4. Set JAVA_HOME (example for jdk-19.0.1)

```bash
echo 'export JAVA_HOME=/opt/java/jdk-19.0.1' >> ~/.bashrc
echo 'export PATH=$JAVA_HOME/bin:$PATH' >> ~/.bashrc
source ~/.bashrc
```

### 5. Install Apache Tomcat

```bash
cd /opt/tomcat
wget https://dlcdn.apache.org/tomcat/tomcat-9/v9.0.102/bin/apache-tomcat-9.0.102.tar.gz
tar -xvf apache-tomcat-9.0.102.tar.gz
cp apache-tomcat-9.0.102/ ../instance1 -rp
cp apache-tomcat-9.0.102/ ../instance2 -rp
```

### 6. Download Sample WAR File

```bash
wget https://tomcat.apache.org/tomcat-7.0-doc/appdev/sample/sample.war
cp sample.war ../instance1/apache-tomcat-9.0.102/webapps/
cp sample.war ../instance2/apache-tomcat-9.0.102/webapps/
```

### 7. Configure Ports for Second Instance

Edit `instance2/apache-tomcat-9.0.102/conf/server.xml` and change ports:

```bash
:%s/8005/8006/g
:%s/8080/9090/g
:%s/8443/9443/g
```

You can also comment out other `<Host>` tags to disable them if desired.

### 8. Start the Instances

```bash
cd /opt/instance1/apache-tomcat-9.0.102/bin
./startup.sh

cd /opt/instance2/apache-tomcat-9.0.102/bin
./startup.sh
```

### 9. Optional: Customize WAR File for Instance2

```bash
mv sample.war sample-red.war
vim webapps/sample-red/index.html  # change "white" to "red"
```

### 10. Use Different Java Versions (optional)

```bash
# For instance1
export JAVA_HOME=/opt/java/jdk-19.0.1
export JRE_HOME=/opt/java/jdk-19.0.1
export PATH=$JAVA_HOME/bin:$PATH

# For instance2
export JAVA_HOME=/opt/java/jdk-20.0.1
export JRE_HOME=/opt/java/jdk-20.0.1
export PATH=$JAVA_HOME/bin:$PATH
```

### 11. Create a systemd Service File

Create `/etc/systemd/system/tomcat@.service`:

```ini
[Unit]
Description=Apache Tomcat Instance %i
After=network.target

[Service]
Type=forking
User=tomcat
Group=tomcat
ExecStart=/opt/tomcat/%i/bin/startup.sh
ExecStop=/opt/tomcat/%i/bin/shutdown.sh
Restart=always

[Install]
WantedBy=multi-user.target
```

### 12. Reload systemd and Enable the Services

```bash
systemctl daemon-reload
systemctl enable tomcat@instance1
systemctl enable tomcat@instance2
systemctl start tomcat@instance1
systemctl start tomcat@instance2
```

## Notes

- Ensure ports `8080`, `9090`, etc. are not in use by other services.
- You may want to configure firewall rules to allow access to the Tomcat ports.
- Use `ps aux | grep tomcat` and `netstat -tuln` to verify instances are running.
