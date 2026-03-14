# Day 11: Install and Configure Tomcat Server

## Task Description
Deploy Apache Tomcat on App Server 2 (stapp02) with a custom port configuration and WAR file deployment. Configure it to operate on port 5000 instead of the default 8080, deploy a ROOT.war file, and verify the application is accessible via HTTP.

## Solution Commands

### 1. Install Java:
```bash
sudo yum install -y java-11-openjdk java-11-openjdk-devel
```

### 2. Create tomcat user:
```bash
sudo useradd -r -m -U -d /opt/tomcat -s /bin/false tomcat
```

### 3. Download and install Tomcat 9.0.75:
```bash
cd /tmp
wget https://archive.apache.org/dist/tomcat/tomcat-9/v9.0.75/bin/apache-tomcat-9.0.75.tar.gz
sudo tar xzvf apache-tomcat-9.0.75.tar.gz -C /opt/tomcat --strip-components=1
sudo chown -R tomcat:tomcat /opt/tomcat
```

### 4. Configure custom port (5000):
```bash
sudo sed -i '0,/Connector port="8080"/s//Connector port="5000"/' /opt/tomcat/conf/server.xml
```

### 5. Deploy WAR file:
```bash
# Copy from Jump host
scp /path/to/ROOT.war user@stapp02:/tmp/
# On stapp02
sudo cp /tmp/ROOT.war /opt/tomcat/webapps/
sudo chown tomcat:tomcat /opt/tomcat/webapps/ROOT.war
```

### 6. Create systemd service file:
```bash
sudo vi /etc/systemd/system/tomcat.service
```

### Service file content:
```ini
[Unit]
Description=Apache Tomcat Web Application Container
After=network.target

[Service]
Type=forking

Environment=JAVA_HOME=/usr/lib/jvm/jre
Environment=CATALINA_PID=/opt/tomcat/temp/tomcat.pid
Environment=CATALINA_HOME=/opt/tomcat
Environment=CATALINA_BASE=/opt/tomcat

ExecStart=/opt/tomcat/bin/startup.sh
ExecStop=/opt/tomcat/bin/shutdown.sh

User=tomcat
Group=tomcat
UMask=0007
RestartSec=10
Restart=always

[Install]
WantedBy=multi-user.target
```

### 7. Start and enable service:
```bash
sudo systemctl daemon-reload
sudo systemctl start tomcat
sudo systemctl enable tomcat
```

### 8. SELinux configuration (RHEL/CentOS):
```bash
sudo semanage port -a -t http_port_t -p tcp 5000
```

### 9. Verification:
```bash
sudo systemctl status tomcat
curl http://localhost:5000
sudo tail -f /opt/tomcat/logs/catalina.out
```

## Key Tomcat Directories

- **/opt/tomcat/bin** - Startup/shutdown scripts
- **/opt/tomcat/conf** - Configuration files
- **/opt/tomcat/webapps** - Web applications (WAR files)
- **/opt/tomcat/logs** - Log files

## Notes
- Version 9.0.75 is suggested as a stable release
- Common issues include permission problems, port conflicts, and SELinux restrictions on RHEL/CentOS systems
- JAVA_HOME configuration is essential for JVM
- Testing involves checking Tomcat logs and using curl to confirm HTTP 200 responses
- WAR files automatically extract to webapps/ROOT/ when named ROOT.war
