https://www.youtube.com/watch?v=eKdHTADjQEY

https://www.coachdevops.com/2021/01/install-sonarqube-8-on-ubuntu-how-to.html

Pre-requistes:
Instance should have at least 2 GB RAM. For AWS or Azure cloud, instance should be atleast 2 GB RAM

SonarQube Architecture


SonarQube have three components namely
1. Scanner - This contains scanner and analyser to scan application code.
2. SonarQube server - contains Webserver(UI) and search server 
3. DB server - used for storing the analysis reports.

Let us start with java install (skip java install if you already have it installed)
Install Open JDK 11
sudo apt-get update && sudo apt-get install default-jdk -y
Postgres DB Setup

sudo sh -c 'echo "deb http://apt.postgresql.org/pub/repos/apt/ `lsb_release -cs`-pgdg main" >> /etc/apt/sources.list.d/pgdg.list'

 
 
 
sudo wget -q https://www.postgresql.org/media/keys/ACCC4CF8.asc -O - | sudo apt-key add -

 
sudo apt-get -y install postgresql postgresql-contrib


Ignore the message in red color below:


sudo systemctl start postgresql
sudo systemctl enable postgresql

Login as postgres user
sudo su - postgres

Now create a user below by executing below command
createuser sonar

9. Switch to sql shell by entering
psql








Execute the below three lines (one by one)

ALTER USER sonar WITH ENCRYPTED password 'password';

CREATE DATABASE sonarqube OWNER sonar;

 GRANT ALL PRIVILEGES ON DATABASE sonarqube to sonar;

Now to come out of Postgres by below command and press enter

\q







and then type exit to come out of postgres user.








3. Download SonarQube and Install

sudo wget https://binaries.sonarsource.com/Distribution/sonarqube/sonarqube-8.6.0.39681.zip

sudo apt-get -y install unzip
sudo unzip sonarqube*.zip -d /opt








sudo mv /opt/sonarqube-8.6.0.39681 /opt/sonarqube -v






Create Group and User:
sudo groupadd sonarGroup

Now add the user with directory access
sudo useradd -c "user to run SonarQube" -d /opt/sonarqube -g sonarGroup sonar 
sudo chown sonar:sonarGroup /opt/sonarqube -R

Modify sonar.properties file
sudo vi /opt/sonarqube/conf/sonar.properties
uncomment the below lines by removing # and add values highlighted yellow
sonar.jdbc.username=sonar
sonar.jdbc.password=password






Next, add the below line:
sonar.jdbc.url=jdbc:postgresql://localhost/sonarqube


 
 
Now press escape button, and enter :wq! to come out of the above screen.

Edit the sonar script file and set RUN_AS_USER
sudo vi /opt/sonarqube/bin/linux-x86-64/sonar.sh
Add enable the below line 
RUN_AS_USER=sonar









Setup SonarQube as a service(this will enable to start automatically when you restart the server)
Execute the below command:

sudo vi /etc/systemd/system/sonar.service












add the below code in green color:
[Unit]
Description=SonarQube service
After=syslog.target network.target

[Service]
Type=forking

ExecStart=/opt/sonarqube/bin/linux-x86-64/sonar.sh start
ExecStop=/opt/sonarqube/bin/linux-x86-64/sonar.sh stop
LimitNOFILE=131072
LimitNPROC=8192
User=sonar
Group=sonarGroup
Restart=always

[Install]
WantedBy=multi-user.target

Save the file by entering :wq!
 
Kernel System changes
we must make a few modifications to a couple of kernel system limits files for sonarqube to work.
sudo vi /etc/sysctl.conf
Add the following lines to the bottom of that file:

vm.max_map_count=262144
fs.file-max=65536


Next, we're going to edit limits.conf. Open that file with the command:

sudo vi /etc/security/limits.conf

At the end of this file, add the following: 

sonar   -   nofile   65536
sonar   -   nproc    4096

Reload system level changes without server boot
sudo sysctl -p

Start SonarQube Now
sudo systemctl start sonar

sudo systemctl enable sonar

sudo systemctl status sonar

Wait for SonarQube to come up after you executed above commands, It will take a few mins to come up.

type q now to come out of this mode.
Now execute the below command to see if Sonarqube is up and running. This may take a few minutes.

tail -f /opt/sonarqube/logs/sonar*.log

Make sure you get the below message that says sonarqube is up..


Now access sonarQube UI by going to browser and enter public dns name with port 9000
Now to go to browser --> http://your_SonarQube_publicdns_name:9000/

How to integrate SonarQube with Jenkins?

https://www.coachdevops.com/2020/04/how-to-integrate-sonarqube-with-jenkins.html
