# K3S INSTALLATION



Install k3s master with calico network

```sh
curl -sfL https://get.k3s.io | INSTALL_K3S_EXEC="--flannel-backend=none --disable-network-policy --cluster-cidr=192.168.0.0/16" sh -

kubectl create -f https://docs.projectcalico.org/manifests/tigera-operator.yaml

curl https://docs.projectcalico.org/manifests/custom-resources.yaml -o custom-resources.yaml
```

Edit the custom-resource.yaml to enable IP forwarding (if the host also set ip-forwarding = 1

```
apiVersion: operator.tigera.io/v1
kind: Installation
metadata:
  name: default
spec:
  # Configures Calico networking.
  calicoNetwork:
    # Note: the containerIPForwarding is enable this ip forwarding
    containerIPForwarding: Enabled
    ipPools:
    - blockSize: 26
      cidr: 192.168.0.0/16
      encapsulation: VXLANCrossSubnet
      natOutgoing: Enabled
      nodeSelector: all()
```

Apply the custom-resource.yaml
```
kubectl apply -f custom-resources.yaml
```

Install k3s on worker node

 **myserver = IP address of master node

** mytoken : get from **/var/lib/rancher/k3s/server/node-token** on master

```sh
curl -sfL https://get.k3s.io | K3S_URL=https://myserver:6443 K3S_TOKEN=mynodetoken sh -
```

