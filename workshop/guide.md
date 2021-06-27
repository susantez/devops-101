# Workshop Guide

## Check Requirements
To complete this tutorial git, jdk11 and docker should be installed your computer

```ssh
java -version

openjdk version "11.0.2" 2019-01-15
OpenJDK Runtime Environment 18.9 (build 11.0.2+9)
OpenJDK 64-Bit Server VM 18.9 (build 11.0.2+9, mixed mode)
```

```ssh
git --version

git version 2.32.0.windows.1
```

```ssh 
docker --version

Docker version 20.10.7, build f0df350

```

## Basic Docker commands will be used during workshop

### Run Jenkins on 8080

```ssh
docker run -p 8080:8080 --name=jenkins-master jenkins/jenkins
```
### List all images

```ssh
docker image ls
```

### List all running containers
```ssh
docker container ls
```

### Stop running container with name
```ssh
docker stop jenkins-master
```

### Remove container with name
```ssh
docker rm jenkins-master
```


## Setup Jenkins with Docker-Compose

create setup folder on your working directory

```ssh
cd workspace
mkdir setup
cd setup
```

create jenkins-compose.yaml

```yaml
version: "3.3"
 
services:
    jenkins:
        image: jenkinsci/jenkins
        container_name: jenkins
        restart: always
        privileged: true
        ports:
            - "8080:8080"
            - "50000:50000"
        networks:
            - training-nw
        volumes:
            - jenkins_data:/var/jenkins_home
            - /var/run/docker.sock:/var/run/docker.sock
            - docker_certs:/certs/client:ro
    socat:
        image: alpine/socat:latest
        container_name: alpine-socat
        restart: always
        command: tcp-listen:2375, fork,reuseaddr unix-connect:/var/run/docker.sock
        networks:
            - training-nw
        volumes:
            - /var/run/docker.sock:/var/run/docker.sock
        expose:
            - "2375"
networks:
    training-nw:
        driver: bridge
volumes:
    jenkins_data: {}
    docker_certs: {}
```

you may usw ``-d`` command to deattach the process. In this tutorial i don't prefer to use it

```ssh
docker-compose -f jenkins-compose.yaml up
```

check the status of the containers in a new terminal

```ssh
docker-compose -f jenkins-compose.yaml ps
```

if jenkins is up navigate to [localhost:8080](http://localhost:8080) . You will notice that jenkins setup is asking for the initial admin password. Connect to the jenkins container and copy the initialAdminPassword

```ssh
docker exec -it jenkins bash
```
if you are useing git bash use winpty command to connect container
```ssh 
winpty docker exec -it jenkins bash
```

copy initialAdminPassword
```ssh
cat /var/jenkins_home/secrets/initialAdminPassword
```