# kubectl cheat sheet

## POD

```shell
#get all pods in all namespaces
kubectl get pods --all-namespaces
#get all pods in a namespaces
kubectl get pods -n [namespace]
##get image of pod named my-pod in current namespace(default)
kubectl describe pod mypod | grep -i image
## get more information of pods 
kubect get pods -o wide
## deploy a pod
kubectl run my-pod --image=nginx
## generate a yaml file for pod deployment
kubectl run my-pod --image=nginx --dry-run-client -o yaml > my-pod.yaml	
## to edit a pod information
kubectl edit pod <pod-name>
##delete a pod
kubectl delete pod <pod-name>
##create pod with expose service
kubectl run my-pod --image nginx --port 80 --expose --dry-run=client -o yaml
## execute bash in pod with kubectl
kubectl exec -it  mongo-pod -- bash -c "mongo"
```

## ReplicaSet

```shell
##get all replicaSet in all namespaces
kubectl get rs --all-namespaces
##edit replicaSet
kubectl edit rs my-replica-set
##scale in/out a replica set
kubectl scale rs my-replica-set --replicas=[number]
##get describe of replica set
kubectl describe rs my-replica-set
## export replica set to yaml
kubectl get rs my-replica-set -o yaml > replica.yaml
## delete a replicaset
kubectl delete rs my-replica-set
```

## Deployment

```shell
## get all deployments
kubectl get deploy --all-namespaces
## edit deployment
kubectl edit deploy my-deployment
## get describe deployment
kubectl describe deploy my-deployment
## create a deployment
kubectl create deploy my-deployment --image=busybox --dry-run=client -o yaml > my-deploymente.yaml
## export a deployment as yaml
kubectl get deploy my-deployment -o yaml > my_deployment.yaml
## scale a deployment
kubectl scale deploy my-deployment --replicas=[number]
## re-deploy a deployment
kubectl rollout restart deploy my-deployment
## roll-back to a previous
kubectl rollout undo deploy my-deployment
## delete a deployment
kubectl delete deploy my-deployment
```

## Namespaces

```shell
##create a namespaces
kubectl create ns my-namespace
##set ns to current context
kubectl config set-context $(kubectl config current-context) -n my-namespace
```

## Services

```shell
##create a clusterIP service, the system will automatically use the pod's label as selectors)
kubectl expose pod redis --port=6379 --name redis-service --dry-run=client -o yaml
##create a NodePort service, the NodePort will generate automatically
kubectl expose pod nginx --port=80 --name nginx-service --type=NodePort --dry-run=client -o yaml
##create a NodePort service with specified port
kubectl create service nodeport nginx --tcp=80:80 --node-port=30080 --dry-run=client -o yaml
```

## Nodes

```shell
kubectl get nodes -o wide
##add taint for a node
kubectl taint nodes [nodenames] [key]=[value]:[NoSchedule/Exists/NoExist]
##remove taint for a node
kubectl taint nodes [nodenames] [taint-string]-
##label for a node
kubectl label nodes [nodename] [key]=[value]

 k run tmp --restart=Never --rm --image=nginx:alpine -i -- curl http://project-plt-6cc-svc.pluto:3333
 k -n sun label pod -l type=runner protected=true # run for label runner
```

