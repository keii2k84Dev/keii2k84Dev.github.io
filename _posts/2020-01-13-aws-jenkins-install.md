---
title: "AWS에 jenkins설치하기 AMI:Amazon Linux 2"
date: 2020-01-13 10:26:28 +0900
categories: devOps
---

기존 AMI1에 jenkins 설치하는 케이스는 있는데 jenkins에서 도커를 사용하기 위해서는 AMI으로는 제약이 있는거 같아 
AMI2에 인스톨하는 방법을 정리하였습니다. 

### AWS jenkins install tutorial (AMI:Amazon Linux 2)

#### EC2 사양 
AMI: Amazon Linux 2 AMI (HVM), SSD Volume Type
instance: t3.midium

### Jenkins 설치하기
---
jenkins를 실행하기 위해서는 java가 필요하기 때문에 먼저 설치를 해줍니다. 
java8버전이상부터 가능한데 여기서는 java11(openjdk11)을 설치합니다. 

```
# install java 11 
sudo amazon-linux-extras install java-openjdk11
```

자바 설치가 끝나면 패키지매니저를 이용해서 jenkins를 설치합니다.
jenkins의경우 패키지매니저의 기본 저장소에는 등록되어 있지 않기 때문에 저장소를 추가한 후에 설치.
```
# install jenkins 
sudo yum update
sudo wget -O /etc/yum.repos.d/jenkins.repo https://pkg.jenkins.io/redhat-stable/jenkins.repo
sudo rpm --import https://pkg.jenkins.io/redhat-stable/jenkins.io.key
sudo yum install jenkins
```

인스톨이 정상적으로 끝났다면 jenkins를 실행합니다. 
실행시 기본 설정포트는 8080입니다
(실제 웹에 접속하기 위해서는 security group에서 inbound에 8080포트를 개방해줘야합니다.)
```
# start jenkins. default port 8080
sudo service jenkins start
```

### Nginx 
---
nginx를 이용해서 포트번호 지정없이 접속할수 있게 proxy설정을 위해 설치합니다. 

먼저 nginx를 설치합니다.
```
sudo yum install nginx
```

설치후 nginx.conf파일을 열어 수정을 합니다. 
```
#open 
sudo vim /etc/nginx/nginx.conf

# before
location / {
}

# after
location / {
        proxy_pass http://localhost:8080;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header Host $http_host;
}
```

그리고 ec2리부팅시에 nginx를 자동실행하기 위한 스크립트를 추가합니다. 
generate shell file in `/etc/profile.d/`
```
[ec2-user@ip-172-31-18-35 profile.d]$ cat nginx.sh
# Initialization script for bash and sh

# excute nginx
sudo service nginx start
```



