## Simple DevOps Project -3 


1. Launch an EC2 instance for Docker host

2. Install docker on EC2 instance and start services 
  ```sh 
  yum install docker
  service docker start
  ```

3. create a new user for Docker management and add him to Docker (default) group
```sh
useradd dockeradmin
passwd dockeradmin
usermod -aG docker dockeradmin
```

4. Write a Docker file under /opt/docker

```sh
mkdir /opt/docker

### vi Dockerfile
# Pull base image 
FROM java:8-jdk-alpine

COPY ./crud-0.0.1-SNAPSHOT.war /usr/app/

WORKDIR /usr/app

RUN sh -c 'touch crud-0.0.1-SNAPSHOT.war'

ENTRYPOINT ["java","-jar","crud-0.0.1-SNAPSHOT.war"]
```

5. Login to Jenkins console and add Docker server to execute commands from Jenkins  
Manage Jenkins --> Configure system -->  Publish over SSH --> add Docker server and credentials

6. Create Jenkins job 

A) Source Code Management  
 Repository : https://github.com/saikrishna2653/crud_examples.git  
 Branches to build : */master  

B) Build
 Root POM: pom.xml  
 Goals and options : clean install package  
 
C) send files or execute commands over SSH
 Name: docker_host  
 Source files	: **/*.war  
 Remove prefix	: /target  
 Remote directory	: //opt//docker  
 Exec command	: docker stop department_employee || true; docker rm -f department_employee || true; docker image rm -f department_employee || true;
cd /opt/docker; docker build -t department_employee .  

D) send files or execute commands over SSH  
  Name: docker_host  
  Exec command	: docker run -d --name department_employee -p 8090:8080 department_employee  

7. Login to Docker host and check images and containers. (no images and containers)

8. Execute Jenkins job

9. check images and containers again on Docker host. This time an image and container get creates through Jenkins job

10. Access web application from browser which is running on container
<docker_host_Public_IP>:8090
