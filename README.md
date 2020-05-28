### Docker
是一个开源的应用容器引擎，让开发者可以打包他们的应用以及依赖包到一个可移植的镜像中，然后发布到任何流行的 Linux或Windows 机器上，也可以实现虚拟化。容器是完全使用沙箱机制，相互之间不会有任何接口。

### Docker Compose
Docker撰写依赖于Docker引擎来完成任何有意义的工作，因此请确保在本地或远程安装了Docker引擎，具体取决于您的设置。

### 环境依赖安装
```
yum install -y yum-utils device-mapper-persistent-data lvm2 git vim net-tools
```

### 安装containerd
```
yum  install -y https://download.docker.com/linux/centos/7/x86_64/stable/Packages/containerd.io-1.2.6-3.3.el7.x86_64.rpm
```

### 安装 docker-ce-cli
```
yum install -y https://download.docker.com/linux/centos/7/x86_64/stable/Packages/docker-ce-cli-19.03.8-3.el7.x86_64.rpm
```

### 安装 docker-ce
```
yum install -y https://download.docker.com/linux/centos/7/x86_64/stable/Packages/docker-ce-19.03.8-3.el7.x86_64.rpm
```

### 启动 docker
systemctl enable --now docker

### 查看安装版本
docker --version

### 安装Docker Compose
```
curl -L "https://github.com/docker/compose/releases/download/1.25.5/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
```

### 备用地址 
```
curl -L "https://github.com/han110zheng/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
```

### Docker Compose对二进制文件应用可执行权限
chmod +x /usr/local/bin/docker-compose

### 如果docker-compose命令在安装后失败，请检查路径。您还可以创建到/usr/bin或路径中的任何其他目录的符号链接
ln -s /usr/local/bin/docker-compose /usr/bin/docker-compose

### 查看Docker Compose版本是否安装
docker-compose --version

### 启动docker-compose前先创建好网络
docker network create --driver=bridge --subnet=172.30.0.0/16 redis

### 执行安装
docker-compose up -d

### 进入容器
docker exec -it node-80 /bin/bash

# 集群快速搭建
通过`docker inspect --format='{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}' node-80`可以查看各个节点在容器内网的ip，
下面的命令必须使用ip而非容器名称。
```
docker exec -it  node-80 redis-cli -p 6380 --cluster create {node-80 ip}:6380  {node-81 ip}:6381  {node-82 ip}:6382  {node-83 ip}:6383  {node-84 ip}:6384  {node-85 ip}:6385 --cluster-replicas 1
```
实际执行命令和结果
```
docker exec -it  node-80 redis-cli -p 6380 --cluster create 172.30.0.30:6380 172.30.0.31:6381 172.30.0.32:6382 172.30.0.33:6383 172.30.0.34:6384 172.30.0.35:6385 --cluster-replicas 1
```

```
>>> Performing hash slots allocation on 6 nodes...
Master[0] -> Slots 0 - 5460
Master[1] -> Slots 5461 - 10922
Master[2] -> Slots 10923 - 16383
Adding replica 172.30.0.34:6384 to 172.30.0.30:6380
Adding replica 172.30.0.35:6385 to 172.30.0.31:6381
Adding replica 172.30.0.33:6383 to 172.30.0.32:6382
M: 81b8d8276efb73e8a41b798c330baa440c60c4cc 172.30.0.30:6380
   slots:[0-5460] (5461 slots) master
M: a50e94082f9ce823b6e39f40f6c602d17b11ab0f 172.30.0.31:6381
   slots:[5461-10922] (5462 slots) master
M: c373c1703739bcfb3f3bc041ddcdcf312ec28568 172.30.0.32:6382
   slots:[10923-16383] (5461 slots) master
S: 89346481a8fae9e510c7faf786da12bbd1e852b7 172.30.0.33:6383
   replicates c373c1703739bcfb3f3bc041ddcdcf312ec28568
S: 2e2cd8ed32ebf90ece9fc575af495bf5b9e6d151 172.30.0.34:6384
   replicates 81b8d8276efb73e8a41b798c330baa440c60c4cc
S: dd66373ba1186c49559e1041635a2941c1b573ac 172.30.0.35:6385
   replicates a50e94082f9ce823b6e39f40f6c602d17b11ab0f
Can I set the above configuration? (type 'yes' to accept): yes
>>> Nodes configuration updated
>>> Assign a different config epoch to each node
>>> Sending CLUSTER MEET messages to join the cluster
Waiting for the cluster to join
......
>>> Performing Cluster Check (using node 172.30.0.30:6380)
M: 81b8d8276efb73e8a41b798c330baa440c60c4cc 172.30.0.30:6380
   slots:[0-5460] (5461 slots) master
   1 additional replica(s)
S: 2e2cd8ed32ebf90ece9fc575af495bf5b9e6d151 172.30.0.34:6384
   slots: (0 slots) slave
   replicates 81b8d8276efb73e8a41b798c330baa440c60c4cc
M: a50e94082f9ce823b6e39f40f6c602d17b11ab0f 172.30.0.31:6381
   slots:[5461-10922] (5462 slots) master
   1 additional replica(s)
S: dd66373ba1186c49559e1041635a2941c1b573ac 172.30.0.35:6385
   slots: (0 slots) slave
   replicates a50e94082f9ce823b6e39f40f6c602d17b11ab0f
M: c373c1703739bcfb3f3bc041ddcdcf312ec28568 172.30.0.32:6382
   slots:[10923-16383] (5461 slots) master
   1 additional replica(s)
S: 89346481a8fae9e510c7faf786da12bbd1e852b7 172.30.0.33:6383
   slots: (0 slots) slave
   replicates c373c1703739bcfb3f3bc041ddcdcf312ec28568
[OK] All nodes agree about slots configuration.
>>> Check for open slots...
>>> Check slots coverage...
[OK] All 16384 slots covered.
```
