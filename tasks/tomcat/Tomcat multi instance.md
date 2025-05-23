cd /opt/
mkdir -p tomcat java instance1 instance2

useradd tomcat
passwd tomcat
chown -R tomcat:tomcat /opt/tomcat

wget https://download.java.net/java/GA/jdk22.0.1/c7ec1332f7bb44aeba2eb341ae18aca4/8/GPL/openjdk-22.0.1_linux-x64_bin.tar.gz
tar -xvf openjdk-22.0.1_linux-x64_bin.tar.gz
rm -rf openjdk-22.0.1_linux-x64_bin.tar.gz

echo 'export JAVA_HOME=/opt/java/jdk-19.0.1' >> ~/.bashrc
echo 'export PATH=$JAVA_HOME/bin:$PATH' >> ~/.bashrc
source ~/.bashrc

wget https://dlcdn.apache.org/tomcat/tomcat-9/v9.0.102/bin/apache-tomcat-9.0.102.tar.gz
tar -xvf apache-tomcat-9.0.102.tar.gz
rm -rf apache-tomcat-9.0.102.tar.gz
cp -rp apache-tomcat-9.0.102/ instance1/
cp -rp apache-tomcat-9.0.102/ instance2/

vim instance2/apache-tomcat-9.0.102/conf/server.xml

8005 → 8006

8080 → 9090

8443 → 9443

wget https://tomcat.apache.org/tomcat-7.0-doc/appdev/sample/sample.war
cp sample.war instance1/apache-tomcat-9.0.102/webapps/
cp sample.war instance2/apache-tomcat-9.0.102/webapps/

cd instance1/apache-tomcat-9.0.102/bin
./startup.sh

cd ../../instance2/apache-tomcat-9.0.102/bin
./startup.sh

cd instance2/apache-tomcat-9.0.102/webapps/
mv sample.war sample-red.war
vim sample-red/index.html

cd /opt/java
wget https://download.java.net/java/GA/jdk20.0.1/b4887098932d415489976708ad6d1a4b/9/GPL/openjdk-20.0.1_linux-x64_bin.tar.gz
tar -xvf openjdk-20.0.1_linux-x64_bin.tar.gz

vim /opt/tomcat/instance1/bin/setenv.sh

export JAVA_HOME=/opt/java/jdk-19.0.1
export JRE_HOME=/opt/java/jdk-19.0.1
export PATH=$JAVA_HOME/bin:$PATH

vim /opt/tomcat/instance2/bin/setenv.sh

export JAVA_HOME=/opt/java/jdk-19.0.1
export JRE_HOME=/opt/java/jdk-19.0.1
export PATH=$JAVA_HOME/bin:$PATH

vim /opt/tomcat/instance2/bin/setenv.sh

vim /etc/systemd/system/tomcat@.service

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

sudo systemctl daemon-reload
sudo systemctl enable tomcat@instance1
sudo systemctl enable tomcat@instance2
sudo systemctl start tomcat@instance1
sudo systemctl start tomcat@instance2

