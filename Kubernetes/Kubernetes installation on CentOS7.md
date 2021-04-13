# Kubernetes Installation on CentOS7

## Prerequisites

1. Hardwares:

   ​	1 master node - at least 2 cores cpu, 4 GB RAM

   ​	1-* worker node 

2. Softwares:

   ​	Installed CentOS7

   ​	Installed Docker

## K8s Installation

### On all Nodes in cluster

#### 	Set hostname for nodes (reboot if needs)

```shell
hostnamectl set-hostname <your-new-hostname>
```

#### 	Configure hosts file 

​	Change the hosts file with below command 

```shell
vi /etc/hosts
```

​	Insert ipaddr & hostname all node in your cluster as example:

```shell
10.0.0.26 master
10.0.0.27 worker01
10.0.0.28 worker02
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

```shell
sudo systemctl enable docker
sudo systemctl daemon-reload
sudo systemctl restart docker
```

#### 	Configure repo for k8s

```shell
cat <<EOF > /etc/yum.repos.d/kubernetes.repo

[kubernetes]
name=Kubernetes
baseurl=https://packages.cloud.google.com/yum/repos/kubernetes-el7-x86_64
enabled=1
gpgcheck=1
repo_gpgcheck=1
gpgkey=https://packages.cloud.google.com/yum/doc/yum-key.gpg https://packages.cloud.google.com/yum/doc/rpm-package-key.gpg

EOF
```

#### 	Install kubelet, kubeadm & kubectl ( kubectl install on only master)

```shell
yum install -y kubelet-1.18.8 kubeadm-1.18.8 kubectl-1.18.8
systemctl enable kubelet
systemctl start kubelet
```

#### 	Configure firewall

```shell
firewall-cmd --permanent --add-port=6443/tcp
firewall-cmd --permanent --add-port=2379-2380/tcp
firewall-cmd --permanent --add-port=10250/tcp
firewall-cmd --permanent --add-port=10251/tcp
firewall-cmd --permanent --add-port=10252/tcp
firewall-cmd --permanent --add-port=10255/tcp
firewall-cmd --permanent --add-port=8472/udp
firewall-cmd --add-masquerade --permanent
firewall-cmd --permanent --add-port=30000-32767/tcp
systemctl restart firewalld
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

### On master node 

​	Only execute on 1 master node (if you intent to use multiple master nodes)

#### Configure kubeadm with **Flannel** pod network 

```shell
sysctl -w net.ipv4.ip_forward=1
sudo kubeadm init --apiserver-advertise-address=192.168.1.30 --pod-network-cidr=10.244.0.0/16
```

Notes: We will have 2 join command in result (one for another master, one for worker), save them to use later.

#### Copy config for admin

```shell
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
```

#### Setup pod network

```shell
sudo kubectl apply -f https://raw.githubusercontent.com/coreos/flannel/master/Documentation/kube-flannel.yml
```

#### Verify status ready

```shell
sudo kubectl get nodes
```

### On worker nodes

#### Join to cluster

Use the command what you saved from result of **Init kubeadm step**

```shell
sysctl -w net.ipv4.ip_forward=1
kubeadm join --discovery-token xxxxxxxx --discovery-token-ca-cert-hash sha256:xxxxxxxxxx
```

### Verify cluster

Run the below command on master nodes

```shell
kubectl get nodes
```

### Check etcd and controller manager

```shell
kubectl get cs
```

​	If result has error **127.0.0.1:10252: connect: connection refused**, do the step below to fix it

​	1. Edit 2 files

```
/etc/kubernetes/manifests/kube-controller-manager.yaml
/etc/kubernetes/manifests/kube-scheduler.yaml
```

​	2. Command out the line which has **--port=0**

​	3. Restart kubelet services on all nodes

```shell
systemctl restart kubelet.service
```

 	4. Verify them again:

```shell
kubectl get cs
```

