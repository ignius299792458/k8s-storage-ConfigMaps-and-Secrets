# Illustration of overall Workout of statefulset flow of `mysql`

## 1. namespace
First implementation of overall namespace isolation for mysql resouces
```
➜ git:(main) kubectl apply -f namespace.yml 
namespace/mysql-ns created

➜ git:(main) kubectl get namespaces
NAME                 STATUS   AGE
default              Active   7d3h
kube-node-lease      Active   7d3h
kube-public          Active   7d3h
kube-system          Active   7d3h
local-path-storage   Active   7d3h
`mysql-ns             Active   7s`

```

## 2. service or service-proxy
```
➜  git:(main) kubectl apply -f service.yml 
service/mysql-service created

➜  git:(main) kubectl get services
NAME         TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)   AGE
kubernetes   ClusterIP   10.96.0.1    <none>        443/TCP   2m19s

➜  git:(main) kubectl get services -n mysql-ns
NAME            TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)    AGE
mysql-service   ClusterIP   None         <none>        3306/TCP   15s

```

## 3. stateful-set workload implementation of mysql with above mysql-service and mysql-ns

```
➜  git:(main) ✗ kubectl apply -f statefulset.yml                 
statefulset.apps/mysql-statefulset created

➜  git:(main) ✗ kubectl get pods -n mysql-ns
NAME                  READY   STATUS        RESTARTS   AGE
mysql-statefulset-0   1/1     Terminating   0          68s

➜  git:(main) ✗ kubectl get pods -n mysql-ns
NAME                  READY   STATUS        RESTARTS   AGE
mysql-statefulset-0   1/1     Terminating   0          80s

➜ git:(main) ✗ kubectl get pods -n mysql-ns
NAME                  READY   STATUS        RESTARTS   AGE
mysql-statefulset-0   1/1     Terminating   0          87s

# this is behavor is due I had initially created stateful pods and delete them so now it reapply it creating:

➜  git:(main) ✗ kubectl get pods -n mysql-ns
No resources found in mysql-ns namespace.

➜  git:(main) ✗ kubectl get pods -n mysql-ns
NAME                  READY   STATUS              RESTARTS   AGE
mysql-statefulset-0   1/1     Running             0          26s
mysql-statefulset-1   0/1     ContainerCreating   0          23s

➜  git:(main) ✗ kubectl get pods -n mysql-ns
NAME                  READY   STATUS              RESTARTS   AGE
mysql-statefulset-0   1/1     Running             0          30s
mysql-statefulset-1   0/1     ContainerCreating   0          27s

➜  git:(main) ✗ kubectl get pods -n mysql-ns
NAME                  READY   STATUS              RESTARTS   AGE
mysql-statefulset-0   1/1     Running             0          36s
mysql-statefulset-1   0/1     ContainerCreating   0          33s

➜  git:(main) ✗ kubectl get pods -n mysql-ns
NAME                  READY   STATUS    RESTARTS   AGE
mysql-statefulset-0   1/1     Running   0          3m38s
mysql-statefulset-1   1/1     Running   0          3m35s
mysql-statefulset-2   1/1     Running   0          2m28s


```
