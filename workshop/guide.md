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
        image: jenkins/jenkins
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

### Customizing Jenkins with plugins

After unlocking Jenkins, the Customize Jenkins page appears.

Click one of the two options shown:
Install suggested plugins to install the recommended set of plugins, which are based on most common use cases.
Select plugins to install - to choose which set of plugins to initially install. When you first access the plugin selection page, the suggested plugins are selected by default.
You can click Install suggested plugins and you have to make sure they all uploaded successfully.

### Docker Plugin Setup

These plugins allow containers to be dynamically provisioned as Jenkins nodes using Docker. It is a Jenkins Cloud plugin for Docker.
```ssh
Docker 
Docker pipeline
```
If your Jenkins instance is not on the same OS as the docker install, you will need to open the docker TCP port so that Jenkins can communicate with the docker daemon. This can be achieved by editing the docker config file and setting.

![image](https://user-images.githubusercontent.com/46446809/124654774-a2f80500-dea7-11eb-8375-513a785828bf.png)

### Creating a docker image
You need a docker image that can be used to run Jenkins agent runtime.

![image](https://user-images.githubusercontent.com/46446809/124657210-e30cb700-deaa-11eb-8f7a-04128ab3ed05.png)
	
 ```ssh
labels: maven-agent
name: maven
docker image: 19912508/jenkins-dnd-agent:0.0.3
Network: golden_gate
Volume: /var/run/docker.sock:/var/run/docker.sock
Connection method : Attach docker container
 ```
docker image: I used this 
```ssh 
19912508/jenkins-dnd-agent:0.0.3 
```
image because maven and openjdk11 are installed in this image.(You can create your own image and use it) 

Network: You can manage networks with docker. you write the netwok name in the network section.
 ```ssh 
 docker network ls
 docker inspect "network name"
 ```
![image](https://user-images.githubusercontent.com/46446809/124659570-c9b93a00-dead-11eb-9420-e107fe8af10e.png)

You must fill all the fields in the container settings area otherwise the slave will not be able to access the jenkins container.

## Create Docker Hub Repository
1) Sign up for a Docker account
2) Create your first repository 
![image](https://user-images.githubusercontent.com/46446809/124659877-1dc41e80-deae-11eb-98f7-714083455bca.png)
3) Creating a Dockerfile to specify your application
```ssh 
FROM jenkins/ssh-slave:jdk11
FROM maven:3.8.1-openjdk-11
RUN curl -sSL https://get.docker.com/ | sh
 ```
4) Run docker build -t <your_username>/my-private-repo . to build your Docker image.
5) Run docker push <your_username>/my-private-repo to push your Docker image to Docker Hub.

### Test Plugin Setup
You go to Jenkins Plugin management again and install the following plugins.

The JUnit plugin provides a publisher that consumes XML test reports generated during the builds and provides some graphical visualization of the historical test results.

Test Result Analyzer plugin that shows history of test execution results in a tabular format.

```ssh 
Junit
TestResult Analyzer Plugin
 ```
 
 ### Creating First Jenkins Job 
 
Let's go to home page, From left pane, select new item.
If you are unable to see this icon, it means that you don’t have sufficient privileges. 

![image](https://user-images.githubusercontent.com/46446809/124662408-777a1800-deb1-11eb-9b84-f4df20500804.png)


![image](https://user-images.githubusercontent.com/46446809/124662476-91b3f600-deb1-11eb-8753-f05f133288b5.png)

### Declarative Pipeline

You create a new pipeline project by clicking on new item on the home page and write the following declarative script to the pipeline script area.

```ssh 
	pipeline {
	    environment {
	    registry = "19912508/devops-training"
	    registryCredential = 'dockerhub'
	    dockerImage = ''
	    }
		agent {
		    label "maven-agent"
		}
		stages {
			stage ('Cloning Git') {
				steps {
	                git branch: 'module1-unit-test', url: 'https://github.com/susantez/devops-training.git' 
	               
	                }
			}
			stage ('Build') {
				steps {
	             
	               sh 'mvn clean package'
	               
	                }
			}
			stage ('Test') {
				steps {
	                sh 'mvn test'
	                }
	                post{
	                    always{
	                        junit '**/target/surefire-reports/TEST-*.xml'
	                    }
	                }
			} 
	     	stage('Building image') {
	            steps{
	                script {
	                  // sleep 2333
	                  dockerImage = docker.build registry + ":$BUILD_NUMBER"
	                  }
	              }
	        }
	         stage('Push Image') {
	            steps{
	                script {
	                  docker.withRegistry( '', registryCredential ) {
	                    dockerImage.push()
	                  }
	                }
	            }
	        }
	        stage('Deploy Image') {
	            steps{
	                script {
	                  docker.withRegistry( '', registryCredential ) {
	                     dockerImage.pull() 
	                  }
	                }
	            }
	        }
	        stage('Run Image') {
	            steps{
	                script {
	                  docker.withRegistry( '', registryCredential ) {
	                     sh 'docker container run -it -d -p 3000:8080  --name docker-trraining '+registry + ":$BUILD_NUMBER"+'' 
	                     echo 'DevOps Training application is up.' 	
	                  }
	                }
	            }
	        }
	      
		}
}

 ```

### Slack Configuration

1) Get a Slack account

2) Configure the Jenkins integration
 Click on Manage Plugins and search for Slack Notification in the Available tab. Click the checkbox and install the plugin.
![image](https://user-images.githubusercontent.com/46446809/124663893-5ca8a300-deb3-11eb-875b-c0da298a92f0.png)

After it's installed, click on Manage Jenkins again in the left navigation, and then go to Configure System. Find the Global Slack Notifier Settings section and add the following values:
![image](https://user-images.githubusercontent.com/46446809/124664188-bf9a3a00-deb3-11eb-9e13-07a292d4a044.png)

## Pipeline Job
```ssh
	def attachments = [
	     [
	        text: 'Jenkins slack integration..',
	        fallback: 'Hey, vendor seems to be mad at you',
	        color: '#ff0000',
	     ]
	   ]
	def slackResponse = slackSend(channel: "jenkins", message: "This is devops-training job")
```
You can run this command at any stage of the pipeline
```ssh
slackSend(channel: slackResponse.channelId, message: "Build: ${env.JOB_NAME} Completed Successfuly ${env.BUILD_URL}", timestamp: slackResponse.ts)
slackSend(channel: slackResponse.channelId, message: "http://localhost:3000/training/v1/book/all", timestamp: slackResponse.ts)

```

 





