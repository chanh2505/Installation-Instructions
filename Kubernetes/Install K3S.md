# K3S INSTALLATION



Install k3s master with calico network

```sh
curl -sfL https://get.k3s.io | INSTALL_K3S_EXEC="--flannel-backend=none --disable-network-policy --cluster-cidr=192.168.0.0/16" sh -

kubectl create -f https://docs.projectcalico.org/manifests/tigera-operator.yaml

kubectl create -f https://docs.projectcalico.org/manifests/custom-resources.yaml
```



Install k3s on worker node

 **myserver = IP address of master node

** mytoken : get from **/var/lib/rancher/k3s/server/node-token** on master

```sh
curl -sfL https://get.k3s.io | K3S_URL=https://myserver:6443 K3S_TOKEN=mynodetoken sh -
```

