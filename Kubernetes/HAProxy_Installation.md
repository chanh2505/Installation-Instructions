# Set up HAproxy

Follow this documentation to set up a highly available Kubernetes cluster using Centos7

### Install HAproxy

```shell
sudo yum update && yum install -y haproxy
```

#### Change cgroups to systemd

```shell
sudo mkdir /etc/docker
cat <<EOF | sudo tee /etc/docker/daemon.json
{
  "exec-opts": ["native.cgroupdriver=systemd"],
  "log-driver": "json-file",
  "log-opts": {
    "max-size": "100m"
  },
  "storage-driver": "overlay2"
}
EOF
```

#### 	Update Iptables settings

```shell
cat <<EOF > /etc/sysctl.d/k8s.conf
net.bridge.bridge-nf-call-ip6tables = 1
net.bridge.bridge-nf-call-iptables = 1
EOF
sysctl --system
```

#### 	Disable SELinux

```shell
sudo setenforce 0
sudo sed -i 's/^SELINUX=enforcing$/SELINUX=permissive/' /etc/selinux/config
```

#### 	Disable SWAP

```shell
sudo sed -i '/swap/d' /etc/fstab
sudo swapoff -a
```

### Check HAProxy

```shell
systemctl status haproxy
```

