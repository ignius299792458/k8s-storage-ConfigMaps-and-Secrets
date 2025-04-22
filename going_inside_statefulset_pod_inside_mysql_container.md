# Interaction the created stateful container within statefulsets of pods

1. Going inside mysql-statefulset-0 pod for mysql-lunched ps
```
➜  mysql git:(main) kubectl exec -it mysql-statefulset-0 -n mysql-ns -- bash
E0423 00:06:08.827568   29110 websocket.go:296] Unknown stream id 1, discarding message
bash-5.1#

```

2. Working with mysql
See the database `devops` created as mentioned on statefulsets.yml configuration inside pod/mysql-statefulset-0

```
bash-5.1# mysql -u root -p root
Enter password: 
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 9
Server version: 9.3.0 MySQL Community Server - GPL

Copyright (c) 2000, 2025, Oracle and/or its affiliates.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql>

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
5 rows in set (0.036 sec)

```

3. Work around with mysql database
Creating table, inserting values, queries, update, etc.
```
mysql> use devops;
Database changed
mysql> CREATE TABLE engineers (
    ->     id INT AUTO_INCREMENT PRIMARY KEY,
    ->     first_name VARCHAR(50) NOT NULL,
    ->     last_name VARCHAR(50) NOT NULL,
    ->     email VARCHAR(100) UNIQUE NOT NULL,
    ->     profession ENUM('devops', 'dev', 'tester', 'officer') NOT NULL,
    ->     hire_date DATE NOT NULL,
    ->     salary DECIMAL(10,2) NOT NULL,
    ->     department VARCHAR(50),
    ->     is_active BOOLEAN DEFAULT TRUE
    -> );
Query OK, 0 rows affected (0.058 sec)


mysql> INSERT INTO engineers (first_name, last_name, email, profession, hire_date, salary, department)
    -> VALUES 
    -> ('John', 'Smith', 'john.smith@company.com', 'devops', '2022-01-15', 85000.00, 'Infrastructure'),
    -> ('Maria', 'Garcia', 'maria.garcia@company.com', 'dev', '2021-06-21', 92000.00, 'Backend'),
    -> ('Ahmed', 'Hassan', 'ahmed.h@company.com', 'tester', '2023-03-10', 75000.00, 'QA'),
    -> ('Lisa', 'Wang', 'lisa.wang@company.com', 'officer', '2020-11-05', 110000.00, 'Management');
Query OK, 4 rows affected (0.014 sec)
Records: 4  Duplicates: 0  Warnings: 0

mysql> select * from engineers;
+----+------------+-----------+--------------------------+------------+------------+-----------+----------------+-----------+
| id | first_name | last_name | email                    | profession | hire_date  | salary    | department     | is_active |
+----+------------+-----------+--------------------------+------------+------------+-----------+----------------+-----------+
|  1 | John       | Smith     | john.smith@company.com   | devops     | 2022-01-15 |  85000.00 | Infrastructure |         1 |
|  2 | Maria      | Garcia    | maria.garcia@company.com | dev        | 2021-06-21 |  92000.00 | Backend        |         1 |
|  3 | Ahmed      | Hassan    | ahmed.h@company.com      | tester     | 2023-03-10 |  75000.00 | QA             |         1 |
|  4 | Lisa       | Wang      | lisa.wang@company.com    | officer    | 2020-11-05 | 110000.00 | Management     |         1 |
+----+------------+-----------+--------------------------+------------+------------+-----------+----------------+-----------+
4 rows in set (0.002 sec)

mysql> UPDATE engineers 
    -> SET salary = salary * 1.05, department = 'Infrastructure Operations' 
    -> WHERE profession = 'devops';
Query OK, 1 row affected (0.013 sec)
Rows matched: 1  Changed: 1  Warnings: 0

mysql> select * from engineers where profession="devops";
+----+------------+-----------+------------------------+------------+------------+----------+---------------------------+-----------+
| id | first_name | last_name | email                  | profession | hire_date  | salary   | department                | is_active |
+----+------------+-----------+------------------------+------------+------------+----------+---------------------------+-----------+
|  1 | John       | Smith     | john.smith@company.com | devops     | 2022-01-15 | 89250.00 | Infrastructure Operations |         1 |
+----+------------+-----------+------------------------+------------+------------+----------+---------------------------+-----------+
1 row in set (0.002 sec)

```



