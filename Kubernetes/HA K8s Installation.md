# Setup k8s with multi master node

Follow this documentation to set up a highly available Kubernetes cluster using Centos7

### 	Environment Assumption

| Role          | FQDN     | IP            | OS       | RAM  | CPU  |
| ------------- | -------- | ------------- | -------- | ---- | ---- |
| Load Balancer | proxy    | 192.168.1.196 | CentOS 7 | 1G   | 1    |
| Master        | master01 | 192.168.1.190 | CentOS 7 | 2G   | 2    |
| Master        | master02 | 192.168.1.191 | CentOS 7 | 2G   | 2    |
| Worker        | worker01 | 192.168.1.192 | CentOS 7 | 2G   | 2    |
| Worker        | worker02 | 192.168.1.193 | CentOS 7 | 2G   | 2    |

### 	1. Set up HAproxy node

Please reference at **HAProxy_Installation.md**

### 2. Configure HAproxy

```shell
##---------------------------------------------------------------------
## configure load balacing for api-server k8s
##---------------------------------------------------------------------
frontend fe-apiservers
    bind *:6443
    mode tcp
    option tcplog
    default_backend be-apiservers

backend be-apiservers
    mode tcp
    option tcp-check
    balance roundrobin
    server master01 172.16.16.101:6443 check fall 3 rise 2
    server master-2 172.16.16.102:6443 check fall 3 rise 2

##---------------------------------------------------------------------
## configure load balacing for guest to call application in k8s
##---------------------------------------------------------------------
frontend fe-app80
    bind *:80
    mode tcp
    option tcplog
    default_backend be-app80

backend be-app80
    mode tcp
    option tcp-check
    balance roundrobin
    server master01 192.168.1.190:80 check fall 3 rise 2
    server master02 192.168.1.191:80 check fall 3 rise 2

```

##### Restart HAproxy and check status

```shell
sudo systemctl restart haproxy
sudo systemctl status haproxy
```

### 3. Install k8s on master01 node

​	Follow this instruction: [Install k8s](./Kubernetes installation on CentOS7.md)  and stop at step **Init kubeadm with Calico CNI**

