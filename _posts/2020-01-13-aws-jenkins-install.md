---
title
--- 

### AWS jenkins install tutorial 

#### Spec 
AMI:            Amazon Linux 2 AMI (HVM), SSD Volume Type

instanceType:   t2.medium

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


