# Workout with configmap.yml

```
➜  configsmaps_secrets git:(main) kubectl apply -f configMap.yml 
configmap/mysql-config-map created

➜  configsmaps_secrets git:(main) kubectl get configmap -n mysql-ns
NAME               DATA   AGE
kube-root-ca.crt   1      67m
mysql-config-map   1      15s

```
