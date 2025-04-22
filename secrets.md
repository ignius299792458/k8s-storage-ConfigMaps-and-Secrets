# Working with secrets.yml
1. Create a secret.yml with all usable variables
2. Use those all usable secret variables on workloads config (alike statefulset.yml)
3. Then apply all

## Example
```
➜  configsmaps_secrets git:(main) ✗ kubectl apply -f secrets.yml 
secret/mysql-secret created
➜  configsmaps_secrets git:(main) ✗ kubectl apply -f statefulset.yml 
statefulset.apps/mysql-statefulset configured
➜  configsmaps_secrets git:(main) ✗ kubectl exec -it mysql-statefulset-0 -n mysql-ns -- bash
E0423 01:20:24.378106   32232 websocket.go:296] Unknown stream id 1, discarding message
                                                                                       bbash-5.1# mysql -u root -p
Enter password: 
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 9
Server version: 9.3.0 MySQL Community Server - GPL

Copyright (c) 2000, 2025, Oracle and/or its affiliates.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql> show databases;
+--------------------+
| Database           |
+--------------------+
| devops             |
| information_schema |
| mysql              |
| performance_schema |
| sys                |
+--------------------+
5 rows in set (0.025 sec)

mysql> 

```
Hence, everything is working with configured secrets variable on secrets.yml
