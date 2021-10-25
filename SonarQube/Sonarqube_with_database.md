Need an EC2 instance (min t2.small)
Install Java-11
 apt-get update   
 apt  list | grep openjdk-11  
 apt-get install openjdk-11-jdk -y   
 
 CREATE POSTGRESDB IN AWS
 
 
 
 Install Postgres database
sudo apt-get -y install postgresql

psql \ --host=<DB instance endpoint:5432> \ --port=<port> \ --username=<master username> \ --password 
  
  
postgres=# create database sonardb;
postgres=# create user sonar with encrypted password 'sonar';
postgres=# grant all privileges on database sonardb to sonar;
  
  apt install net-tools
  
  netstat -tulpn
  
  SonarQube Setup
  
Download soarnqube and extract it.
  
  cd /opt
  
wget https://binaries.sonarsource.com/Distribution/sonarqube/sonarqube-8.9.2.46101.zip
unzip sonarqube-8.9.2.46101.zip
  
  mv sonarqube-8.9.2.46101  /opt/sonar
  
  please un-comment this lines in 
  
Update sonar.properties with below information sonar.properties


sonar.jdbc.username=sonar
sonar.jdbc.password=password
sonar.jdbc.url=jdbc:postgresql://localhost or aws db endpoint with : port/sonarqube
sonar.search.javaOpts=-Xmx512m -Xms512m -XX:+HeapDumpOnOutOfMemoryError
sonar.web.host=0.0.0.0
sonar.web.port=9000
  


1. Create a `/etc/systemd/system/sonarqube.service` file start sonarqube service at the boot time 
```sh   
cat >> /etc/systemd/system/sonarqube.service <<EOL
[Unit]
Description=SonarQube service
After=syslog.target network.target

[Service]
Type=forking
User=root
Group=root
PermissionsStartOnly=true
ExecStart=/opt/sonar/bin/linux-x86-64/sonar.sh start 
ExecStop=/opt/sonar/bin/linux-x86-64/sonar.sh stop
StandardOutput=syslog
LimitNOFILE=65536
LimitNPROC=4096
TimeoutStartSec=5
Restart=always

[Install]
WantedBy=multi-user.target
EOL

sudo chown root:root /opt/sonar/ -R  
  
Reload the demon and start sonarqube service
systemctl daemon-reload 
systemctl start sonarqube.service 
  
  
  
  
systemctl daemon-reloadsystemctl enable sonarqube.service systemctl start sonarqube.service
 
 
 netstat -tulpena | grep 9000
