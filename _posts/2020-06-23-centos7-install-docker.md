---
layout: mypost
title: centos 7 安装docker & docker-compose
categories: [docker]
---
```
rpm -i http://mirrors.aliyun.com/epel/epel-release-latest-7.noarch.rpm
yum install python36-pip gcc python36-devel openssl-devel -y
pip3 install docker-compose

curl https://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo -o /etc/yum.repos.d/docker-ce.repo
yum install docker-ce -y

mkdir -p /etc/docker
tee /etc/docker/daemon.json <<-'EOF'
{
  "registry-mirrors": ["https://1bzqzttk.mirror.aliyuncs.com"]
}
EOF
systemctl daemon-reload
systemctl enable docker
systemctl restart docker
```
