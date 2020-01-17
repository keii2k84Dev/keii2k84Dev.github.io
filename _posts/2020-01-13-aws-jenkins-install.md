---
title: "install jenkins to aws"
date: 2020-01-13 10:26:28 +0900
categories: devOps
---

### AWS jenkins install tutorial 

#### Spec 
AMI:            Amazon Linux 2 AMI (HVM), SSD Volume Type
![image](https://user-images.githubusercontent.com/32765822/72327382-5093b900-36f4-11ea-8611-ed81da5bc4fd.png?s=100)

### Install Jenkins 
---

```
# install java 11 
sudo amazon-linux-extras install java-openjdk11

# install jenkins 
sudo yum update
sudo wget -O /etc/yum.repos.d/jenkins.repo https://pkg.jenkins.io/redhat-stable/jenkins.repo
sudo rpm --import https://pkg.jenkins.io/redhat-stable/jenkins.io.key
sudo yum install jenkins

# start jenkins. default port 8080
sudo service jenkins start
```

### Nginx 
---
port fowarding 80 -> 8080

#### install 
```
sudo yum install nginx
```

#### edit nginx config
```
#open 
sudo vim /etc/nginx/nginx.conf
```
##### before 
```
        location / {
        }
```
#### after 
```
        location / {
                proxy_pass http://localhost:8080;
                proxy_set_header X-Real-IP $remote_addr;
                proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
                proxy_set_header Host $http_host;
        }
```

#### set run nginx when boot system 

generate shell file in `/etc/profile.d/`
```
[ec2-user@ip-172-31-18-35 profile.d]$ cat nginx.sh
# Initialization script for bash and sh

# excute nginx
sudo service nginx start
[ec2-user@ip-172-31-18-35 profile.d]$
```



