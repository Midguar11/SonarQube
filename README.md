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
- admin / admin
- add new pasword : ***************

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

# Install PostgreSQL and SonarQube my server

https://developerinsider.co/install-sonarqube-on-ubuntu/

