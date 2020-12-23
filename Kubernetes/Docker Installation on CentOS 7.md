# Docker Installation

#### Update docker package

```shell
sudo yum check-update
```

#### Install the dependencies

```shell
sudo yum install -y yum-utils device-mapper-persistent-data lvm2
```

#### Add docker repo

```shell
sudo yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
```

#### Install docker

```shell
sudo yum install docker
```

#### Start docker service

```shell
sudo systemctl start docker
```

#### Set run on startup for docker service

```shell
sudo systemctl enable docker
```

#### Check status service

```shell
sudo systemctl status docker
```