# Resolution for common errors k8s

### Connection Refused after reboot

Sometimes, the result of "**kubectl get nodes**" will show **The connection to  the server [ip]:6443 was refused**

Resolution:

```shell
sudo -i
swapoff -a
```

### Master node status is NotReady

##### Estimation

```shell
systemctl status kubelet.service
```

​	Error:

```shell
master kubelet[15582]: : [failed to find plugin "flannel" in path [/opt/cni/bin] failed to find plugin "portmap"...i/bin]]
```

##### Resolutions:

###### 	Re-install kubeadm with other cni provider

​	**1.Reset kubeadm** (all nodes)

```
kubeadm reset
```

​	**2. Remove file config in $HOME/.kube**

​	**3.Configure Networkmanager to use Calico**

```shell
cat >>/etc/NetworkManager/conf.d/calico.conf<<EOF
[keyfile]
unmanaged-devices=interface-name:cali*;interface-name:tunl*
EOF
```

​	**4. Init kubeadm with Calico CNI** (need sudo if init is timeout error)

```shell
firewall-cmd --permanent --add-port=179/tcp
systemctl restart firewalld

sudo kubeadm init --apiserver-advertise-address=10.100.33.70 --pod-network-cidr=192.168.0.0/16
```

​	**5. Copy file config**

```shell
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
```

​	**6. Install Calico**

```shell
sudo kubectl apply -f https://docs.projectcalico.org/v3.10/manifests/calico.yaml
```

### Calico-node not ready.

​	**1. Edit yaml of calico**

​			Download file yaml of calico

```shell
curl https://docs.projectcalico.org/v3.10/manifests/calico.yaml --output calico.yaml
```

​			Add one  env to **calico-node** container (replace **enp0s3** by your interface ip, such as: eth0, eth1,...)

```shell
# Specify interface
- name: IP_AUTODETECTION_METHOD
  value: "interface=enp0s3"
```

​	**2. Edit yaml of calico**

​		Check If FELIX_IPV6SUPPORT = false, disable IPv6 on your machine.

​	**3. Edit yaml of calico**

​		Re-apply calico yaml.

```shell
kubectl delete daemonset/calico-node -n kube-system
kubectl apply -f calico.yaml
```

