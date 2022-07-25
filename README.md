# Setup to server

- connect to server with SSH

    sudo sysctl -w vm.max_map_count=524288
    sudo sysctl -w fs.file-max=131072 
    ulimit -n 131072
    ulimit -u 8192

# Runing Sonar Qube in docker

    docker run -d --name sonarqube -p 9000:9000 sonarqube
    docker ps

# Login sonarqube

- qube is runing 
- open browser myserverip:9000
- when outside accept, open this port
- admin / admin
- add new pasword : ***************
- Administration /security / users / tokens / Update tokens ( under token line icon )
- " jenkins " Generate copy this token
- Open Jenkins server / Manage Jenkins / Configure System / 
- Sonar qube / Server authentication token / Add / jenkins
- kind " Secret Text " 
- Secret paste
- id " sonar-jenkins "
- Save
- select this token

# Project

- Projects / Manualy 
- give display name " testproject "
- Open my jenkins server install plugin " SonarQube Scanner "
- select localy and making new token
- Quality gates / create / test

# Remov old and was making new sonarqube

    sudo docker ps
    sudo docker rm -f sonarqube
    docker run -d -p 9000:9000 -v sonarqube_conf:/opt/sonarqube/conf -v sonarqube_extensions:/opt/sonarqube/extensions -v sonarqube_logs:/opt/sonarqube/logs -v sonarqube_data:/opt/sonarqube/data sonarqube
    sudo docker ps
    sudo docker volume ls

# Install PostgreSQL my server

- sudo nano /etc/sysctl.conf
- " vm.max_map_count=262144
fs.file-max=65536
ulimit -n 65536
ulimit -u 4096 "
- sudo nano /etc/security/limits.conf
- " sonarqube   -   nofile   65536
sonarqube   -   nproc    4096 "

    sudo apt-get update
    sudo apt-get upgrade
    sudo sh -c 'echo "deb http://apt.postgresql.org/pub/repos/apt/ `lsb_release -cs`-pgdg main" >> /etc/apt/sources.list.d/pgdg.list'
    wget -q https://www.postgresql.org/media/keys/ACCC4CF8.asc -O - | sudo apt-key add -
    sudo apt-get -y install postgresql postgresql-contrib
    sudo systemctl start postgresql
    sudo systemctl status postgresql
    psql --version
    sudo systemctl enable postgresql
    sudo passwd postgres
    
- give new pass

        su - postgres
        createuser sonar
        psql  
        ALTER USER sonar WITH ENCRYPTED password 'P@ssword';
        CREATE DATABASE sonarqube OWNER sonar;
        GRANT ALL PRIVILEGES ON DATABASE sonarqube to sonar;
        \q
        exit
        systemctl restart  postgresql
        systemctl status -l   postgresql


# Install SonarQube my server

    wget https://binaries.sonarsource.com/Distribution/sonarqube/sonarqube-9.5.0.56709.zip
    apt-get -y install unzipls
    sudo unzip sonarqube-9.5.0.56709.zip -d /opt
    sudo mv /opt/sonarqube-9.5.0.56709 /opt/sonarqube
    sudo groupadd sonar
    sudo useradd -c "SonarQube - User" -d /opt/sonarqube/ -g sonar sonar
    sudo chown sonar:sonar /opt/sonarqube/ -R
    sudo nano /opt/sonarqube/conf/sonar.properties

- delete sign # : " sonar.jdbc.username=sonar "
- delete sign # : " sonar.jdbc.password=P@ssword "
- delete sign # this line " sonar.jdbc.url=jdbc:postgresql://localhost/sonar "
- delete sign # sonar.web.javaOpts=-Xmx512m -Xms128m -XX:+HeapDumpOnOutOfMemoryError

      nano /etc/systemd/system/sonarqube.service

- [Unit]
Description=SonarQube service
After=syslog.target network.target

[Service]
Type=forking

ExecStart=/opt/sonarqube/bin/linux-x86-64/sonar.sh start
ExecStop=/opt/sonarqube/bin/linux-x86-64/sonar.sh stop

User=sonar
Group=sonar
Restart=always

LimitNOFILE=65536
LimitNPROC=4096


[Install]
WantedBy=multi-user.target

    systemctl daemon-reload 
    systemctl enable sonarqube.service
    systemctl start sonarqube.service
    systemctl status -l sonarqube.service


 
