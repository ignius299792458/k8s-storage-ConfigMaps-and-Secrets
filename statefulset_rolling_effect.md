# StatefulSets Rolling Deployment and State Persistence

StatefulSets in Kubernetes provide unique stability guarantees for stateful applications. When pods managed by a StatefulSet are deleted or crash, Kubernetes automatically maintains the replica count by creating new pods that preserve the state and identity of the previous instances.
StatefulSet Auto-healing Mechanism
When a StatefulSet pod terminates for any reason (deletion, crash, node failure), Kubernetes follows these key principles:

- Persistent Identity: New pods receive the same network identity (hostname) as their predecessors
- Ordered Deployment: Pods are recreated in sequential order
- State Preservation: Persistent volumes remain attached to the same pod identities

Example: Demonstrating StatefulSet Recovery
Let's demonstrate the rolling recovery (auto-healing) effect by deliberately deleting pods and observing how StatefulSets maintain state:
```
➜  mysql git:(main) kubectl delete pod mysql-statefulset-0 -n mysql-ns
pod "mysql-statefulset-0" deleted

➜  mysql git:(main) kubectl get pods -n mysql-ns
NAME                  READY   STATUS    RESTARTS   AGE
mysql-statefulset-0   1/1     Running   0          8s
mysql-statefulset-1   1/1     Running   0          28m
mysql-statefulset-2   1/1     Running   0          27m

➜  mysql git:(main) kubectl delete pod mysql-statefulset-0 -n mysql-ns
pod "mysql-statefulset-0" deleted

➜  mysql git:(main) kubectl get pods -n mysql-ns                      
NAME                  READY   STATUS              RESTARTS   AGE
mysql-statefulset-0   0/1     ContainerCreating   0          2s
mysql-statefulset-1   1/1     Running             0          28m
mysql-statefulset-2   1/1     Running             0          27m

➜  mysql git:(main) kubectl delete pod mysql-statefulset-0 -n mysql-ns
pod "mysql-statefulset-0" deleted

➜  mysql git:(main) kubectl get pods -n mysql-ns                      
NAME                  READY   STATUS              RESTARTS   AGE
mysql-statefulset-0   0/1     ContainerCreating   0          1s
mysql-statefulset-1   1/1     Running             0          28m
mysql-statefulset-2   1/1     Running             0          27m

➜  mysql git:(main) kubectl get pods -n mysql-ns
NAME                  READY   STATUS    RESTARTS   AGE
mysql-statefulset-0   1/1     Running   0          6s
mysql-statefulset-1   1/1     Running   0          28m
mysql-statefulset-2   1/1     Running   0          27m

```

2. Let check all database data and volume on new statefulset pod created from which was created on deleted pod
```
➜  mysql git:(main) kubectl exec -it mysql-statefulset-0 -n mysql-ns -- bash
E0423 00:27:33.610556   29702 websocket.go:296] Unknown stream id 1, discarding message
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
5 rows in set (0.023 sec)

mysql> use devops
Reading table information for completion of table and column names
You can turn off this feature to get a quicker startup with -A

Database changed
mysql> use devops;
Database changed
mysql> show tables;
+------------------+
| Tables_in_devops |
+------------------+
| engineers        |
+------------------+
1 row in set (0.006 sec)

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
Hence, as you can even after multiple deletions, the data of database remain intacted. 

