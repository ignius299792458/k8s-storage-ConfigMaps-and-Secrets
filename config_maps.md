# Workout with configMap.yml

1. Applying the written configMap.yml configuration
```
➜  configsmaps_secrets git:(main) kubectl apply -f configMap.yml 
configmap/mysql-config-map created

➜  configsmaps_secrets git:(main) kubectl get configmap -n mysql-ns
NAME               DATA   AGE
kube-root-ca.crt   1      67m
mysql-config-map   1      15s

```

2. Applying the Configuration variable of configMap.yml to statefulset.yml

```
➜  configsmaps_secrets git:(main) kubectl apply -f statefulset.yml            
statefulset.apps/mysql-statefulset configured

➜  configsmaps_secrets git:(main) kubectl get pods -n mysql-ns  
NAME                  READY   STATUS    RESTARTS   AGE
mysql-statefulset-0   1/1     Running   0          2m46s
mysql-statefulset-1   1/1     Running   0          2m53s
mysql-statefulset-2   1/1     Running   0          2m59s

➜  configsmaps_secrets git:(main) kubectl exec -it mysql-statefulset-0 -n mysql-ns -- bash
bash-5.1# mysql -u root -p
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
5 rows in set (0.026 sec)

mysql> use devops;
Reading table information for completion of table and column names
You can turn off this feature to get a quicker startup with -A

Database changed
mysql> show tables;
+------------------+
| Tables_in_devops |
+------------------+
| engineers        |
+------------------+
1 row in set (0.005 sec)

mysql> select * from engineers;
+----+------------+-----------+--------------------------+------------+------------+-----------+---------------------------+-----------+
| id | first_name | last_name | email                    | profession | hire_date  | salary    | department                | is_active |
+----+------------+-----------+--------------------------+------------+------------+-----------+---------------------------+-----------+
|  1 | John       | Smith     | john.smith@company.com   | devops     | 2022-01-15 |  89250.00 | Infrastructure Operations |         1 |
|  2 | Maria      | Garcia    | maria.garcia@company.com | dev        | 2021-06-21 |  92000.00 | Backend                   |         1 |
|  3 | Ahmed      | Hassan    | ahmed.h@company.com      | tester     | 2023-03-10 |  75000.00 | QA                        |         1 |
|  4 | Lisa       | Wang      | lisa.wang@company.com    | officer    | 2020-11-05 | 110000.00 | Management                |         1 |
+----+------------+-----------+--------------------------+------------+------------+-----------+---------------------------+-----------+
4 rows in set (0.004 sec)

mysql>
```
All working the configMap variable to new pod
