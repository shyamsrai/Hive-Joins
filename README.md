# Joins in Hive

Primer introduction to JOINs in Hive Database. This repository includes hands on introduction on types of common JOINs in SQL and JOINs specific to Hive database in the Hadoop world, with examples.

## Equijoin/Inner join/Simple Join
Known with different names, same type of join.
 
Retrieving data from two tables with a common joining column. For instance, joining student ID with table enrollment (student ID) to find out how many students enrolled for a particular course. 

#### Create tables 
```
create table students (id int, name string) row format delimited fields terminated by ',' stored as orc; 
create table enrollment (student_id int, subject string) row format delimited fields terminated by ',' stored as orc;
```
#### Insert sample data 
```
insert into students values (1001,'Alex'),(1002,'Ryan'),(1003,'Justin'); 
insert into enrollment values (1001,'Maths'),(1001,'Science'),(1001,'History'),(1002,'Physics'),(1002,'Maths'),(1003,'Physics'),(1003,'Social Studies'),(1004,'Maths'); 
```

#### Querying the data using "Equi Join" should result with rows between the two tables where student ID is common
```
select students.id,students.name, enrollment.subject from students join enrollment on students.id = enrollment.student_id;
```

##### Resulting Output should look something like this
```
Query ID = hive_20160926223103_c7aaccc7-e04d-4ada-8a07-6d250839f852
Total jobs = 1
Launching Job 1 out of 1


Status: Running (Executing on YARN cluster with App id application_1474695223471_0014)

--------------------------------------------------------------------------------
        VERTICES      STATUS  TOTAL  COMPLETED  RUNNING  PENDING  FAILED  KILLED
--------------------------------------------------------------------------------
Map 1 ..........   SUCCEEDED      1          1        0        0       0       0
Map 2 ..........   SUCCEEDED      1          1        0        0       0       0
--------------------------------------------------------------------------------
VERTICES: 02/02  [==========================>>] 100%  ELAPSED TIME: 5.57 s     
--------------------------------------------------------------------------------
OK
1001	Alex	Maths
1001	Alex	Science
1001	Alex	History
1002	Ryan	Physics
1002	Ryan	Maths
1003	Justin	Physics
1003	Justin	Social Studies
Time taken: 7.465 seconds, Fetched: 7 row(s)
```

NOTICE that enrollment table has one entry where student_id column has an entry 1004, however, since it is not available within table "students" the entry for that table will not be included, thus only values equaling the other table.



## Left Join

This JOIN method retrieves all the rows from table on LEFT side of the keyword "LEFT JOIN" &x` unique rows from the table on the right side. Using the same example as the rows in prior example.

```
select students.id,students.name, enrollment.subject from enrollment left join students  on students.id = enrollment.student_id;
```

##### Resulting output would look something like

```
INFO  : Session is already open
DEBUG : Adding local resource: scheme: "hdfs" host: "master.hortonworks.com.hortonworks.com" port: 8020 file: "/tmp/hive/hive/_tez_session_dir/9be4477d-e925-44b2-871e-a917098d95b9/hive-hcatalog-core.jar"
INFO  : Dag name: select students.id,st...nrollment.student_id(Stage-1)
DEBUG : DagInfo: {"context":"Hive","description":"select students.id,students.name, enrollment.subject from students left join enrollment on students.id = enrollment.student_id"}
DEBUG : Setting Tez DAG access for hive
INFO  : 

INFO  : Status: Running (Executing on YARN cluster with App id application_1474695223471_0016)

INFO  : Map 1: 0/1	Map 2: 0(+1)/1	
INFO  : Map 1: 0(+1)/1	Map 2: 1/1	
INFO  : Map 1: 1/1	Map 2: 1/1	
Getting log thread is interrupted, since query is done!
+--------------+----------------+---------------------+--+
| students.id  | students.name  | enrollment.subject  |
+--------------+----------------+---------------------+--+
| 1001         | Alex           | Maths               |
| 1001         | Alex           | History             |
| 1001         | Alex           | Science             |
| 1002         | Ryan           | Physics             |
| 1002         | Ryan           | Maths               |
| 1003         | Justin         | Physics             |
| 1003         | Justin         | Social Studies      |
+--------------+----------------+---------------------+--+
7 rows selected (1.714 seconds)

```

## Right Join

During this JOIN all rows for the table after the keyword "RIGHT JOIN" are displayed regardless of a match & any matching rows on the table to the left. 

```
select students.id,students.name, enrollment.subject from students right join enrollment on students.id = enrollment.student_id;
```

##### Output would display all rows on right side of the JOIN 

```
INFO  : Session is already open
DEBUG : Adding local resource: scheme: "hdfs" host: "master.hortonworks.com.hortonworks.com" port: 8020 file: "/tmp/hive/hive/_tez_session_dir/9be4477d-e925-44b2-871e-a917098d95b9/hive-hcatalog-core.jar"
INFO  : Dag name: select students.id,st...nrollment.student_id(Stage-1)
DEBUG : DagInfo: {"context":"Hive","description":"select students.id,students.name, enrollment.subject from students right join enrollment on students.id = enrollment.student_id"}
DEBUG : Setting Tez DAG access for hive
INFO  : 

INFO  : Status: Running (Executing on YARN cluster with App id application_1474695223471_0017)

INFO  : Map 1: 0/1	Map 2: 0/1	
INFO  : Map 1: 0(+1)/1	Map 2: 0(+1)/1	
INFO  : Map 1: 1/1	Map 2: 0(+1)/1	
INFO  : Map 1: 1/1	Map 2: 1/1	
Getting log thread is interrupted, since query is done!
+--------------+----------------+---------------------+--+
| students.id  | students.name  | enrollment.subject  |
+--------------+----------------+---------------------+--+
| 1001         | Alex           | Maths               |
| 1001         | Alex           | Science             |
| 1001         | Alex           | History             |
| 1002         | Ryan           | Physics             |
| 1002         | Ryan           | Maths               |
| 1003         | Justin         | Physics             |
| 1003         | Justin         | Social Studies      |
| NULL         | NULL           | Maths               |
+--------------+----------------+---------------------+--+
8 rows selected (7.069 seconds)

```

Table on the right i.e. enrollment here displays all the rows in that table, including the unmatching row which cotains the subject "Maths" for the students ID entry 1004, however, from the right side of the join.



## FULL OUTER Join / FULL Join

This type of join with revert all the rows from both the tables 

```
select students.id,students.name, enrollment.subject from students full outer join enrollment on students.id = enrollment.student_id;
```

##### Full outer join retrieves all the rows from either tables. 

```
0: jdbc:hive2://master.hortonworks.com:10000/> insert into students values (1004,'Rick');
INFO  : Session is already open
DEBUG : Adding local resource: scheme: "hdfs" host: "master.hortonworks.com.hortonworks.com" port: 8020 file: "/tmp/hive/hive/_tez_session_dir/9be4477d-e925-44b2-871e-a917098d95b9/hive-hcatalog-core.jar"
INFO  : Dag name: insert into students values (1004,'Rick')(Stage-1)
DEBUG : DagInfo: {"context":"Hive","description":"insert into students values (1004,'Rick')"}
DEBUG : Setting Tez DAG access for hive
INFO  : 

INFO  : Status: Running (Executing on YARN cluster with App id application_1474695223471_0019)

INFO  : Map 1: 0/1	
INFO  : Map 1: 0(+1)/1	
INFO  : Map 1: 1/1	
INFO  : Loading data to table default.students from hdfs://master.hortonworks.com.hortonworks.com:8020/apps/hive/warehouse/students/.hive-staging_hive_2016-09-27_05-48-38_791_8890950333319219872-1/-ext-10000
INFO  : Table default.students stats: [numFiles=2, numRows=4, totalSize=632, rawDataSize=368]
Getting log thread is interrupted, since query is done!
No rows affected (5.758 seconds)
....
....
....
0: jdbc:hive2://master.hortonworks.com:10000/> select students.id,students.name, enrollment.subject from enrollment full outer join students on students.id = enrollment.student_id;
INFO  : Session is already open
DEBUG : Adding local resource: scheme: "hdfs" host: "master.hortonworks.com.hortonworks.com" port: 8020 file: "/tmp/hive/hive/_tez_session_dir/9be4477d-e925-44b2-871e-a917098d95b9/hive-hcatalog-core.jar"
INFO  : Dag name: select students.id,st...nrollment.student_id(Stage-1)
DEBUG : DagInfo: {"context":"Hive","description":"select students.id,students.name, enrollment.subject from enrollment full outer join students on students.id = enrollment.student_id"}
DEBUG : Setting Tez DAG access for hive
INFO  : 

INFO  : Status: Running (Executing on YARN cluster with App id application_1474695223471_0019)

INFO  : Map 1: 0/1	Map 3: 0/1	Reducer 2: 0/2	
INFO  : Map 1: 0/1	Map 3: 0(+1)/1	Reducer 2: 0/2	
INFO  : Map 1: 0(+1)/1	Map 3: 0(+1)/1	Reducer 2: 0/2	
INFO  : Map 1: 0(+1)/1	Map 3: 1/1	Reducer 2: 0/2	
INFO  : Map 1: 1/1	Map 3: 1/1	Reducer 2: 0(+1)/1	
INFO  : Map 1: 1/1	Map 3: 1/1	Reducer 2: 1/1	
Getting log thread is interrupted, since query is done!
+--------------+----------------+---------------------+--+
| students.id  | students.name  | enrollment.subject  |
+--------------+----------------+---------------------+--+
| 1001         | Alex           | Maths               |
| 1001         | Alex           | Science             |
| 1001         | Alex           | History             |
| 1002         | Ryan           | Physics             |
| 1002         | Ryan           | Maths               |
| 1003         | Justin         | Physics             |
| 1003         | Justin         | Social Studies      |
| 1004         | Rick           | Maths               |
+--------------+----------------+---------------------+--+
```

##### Now inserting two additional rows in the table

```
insert into students values (1005,'Richard');
insert into students values (1006,'Robert');
```

##### FULL OUTER JOIN will now display all the rows from students table 

```
0: jdbc:hive2://master.hortonworks.com:10000/> select students.id,students.name, enrollment.subject from students full outer join enrollment on students.id = enrollment.student_id;
INFO  : Session is already open
DEBUG : Adding local resource: scheme: "hdfs" host: "master.hortonworks.com.hortonworks.com" port: 8020 file: "/tmp/hive/hive/_tez_session_dir/9be4477d-e925-44b2-871e-a917098d95b9/hive-hcatalog-core.jar"
INFO  : Dag name: select students.id,st...nrollment.student_id(Stage-1)
DEBUG : DagInfo: {"context":"Hive","description":"select students.id,students.name, enrollment.subject from students full outer join enrollment on students.id = enrollment.student_id"}
DEBUG : Setting Tez DAG access for hive
INFO  : 

INFO  : Status: Running (Executing on YARN cluster with App id application_1474695223471_0019)

INFO  : Map 1: 0/2	Map 3: 0/1	Reducer 2: 0/2	
INFO  : Map 1: 0(+1)/2	Map 3: 0(+1)/1	Reducer 2: 0/2	
INFO  : Map 1: 0(+2)/2	Map 3: 0(+1)/1	Reducer 2: 0/2	
INFO  : Map 1: 1(+1)/2	Map 3: 0(+1)/1	Reducer 2: 0/2	
INFO  : Map 1: 2/2	Map 3: 0(+1)/1	Reducer 2: 0/2	
INFO  : Map 1: 2/2	Map 3: 1/1	Reducer 2: 0(+1)/1	
INFO  : Map 1: 2/2	Map 3: 1/1	Reducer 2: 1/1	
Getting log thread is interrupted, since query is done!
+--------------+----------------+---------------------+--+
| students.id  | students.name  | enrollment.subject  |
+--------------+----------------+---------------------+--+
| 1001         | Alex           | Maths               |
| 1001         | Alex           | Science             |
| 1001         | Alex           | History             |
| 1002         | Ryan           | Physics             |
| 1002         | Ryan           | Maths               |
| 1003         | Justin         | Physics             |
| 1003         | Justin         | Social Studies      |
| 1004         | Rick           | Maths               |
| 1005         | Richard        | NULL                |
| 1006         | Robert         | NULL                |
+--------------+----------------+---------------------+--+
10 rows selected (7.175 seconds)

```

##### Now inserting two new rows in enrollment table

```
0: jdbc:hive2://master.hortonworks.com:10000/> insert into enrollment values (1005,'Zoology'),(1006,'Botany');
INFO  : Session is already open
DEBUG : Adding local resource: scheme: "hdfs" host: "master.hortonworks.com.hortonworks.com" port: 8020 file: "/tmp/hive/hive/_tez_session_dir/9be4477d-e925-44b2-871e-a917098d95b9/hive-hcatalog-core.jar"
INFO  : Dag name: insert into enrollmen...gy'),(1006,'Botany')(Stage-1)
DEBUG : DagInfo: {"context":"Hive","description":"insert into enrollment values (1005,'Zoology'),(1006,'Botany')"}
DEBUG : Setting Tez DAG access for hive
INFO  : 

INFO  : Status: Running (Executing on YARN cluster with App id application_1474695223471_0019)

INFO  : Map 1: 0/1	
INFO  : Map 1: 0(+1)/1	
INFO  : Map 1: 1/1	
INFO  : Loading data to table default.enrollment from hdfs://master.hortonworks.com.hortonworks.com:8020/apps/hive/warehouse/enrollment/.hive-staging_hive_2016-09-27_06-13-12_139_1846079772484976949-1/-ext-10000
INFO  : Table default.enrollment stats: [numFiles=2, numRows=10, totalSize=758, rawDataSize=948]
Getting log thread is interrupted, since query is done!
No rows affected (5.736 seconds)
```

##### FULL OUTER JOIN will now return the newly added rows which are also matching between either tables

```
0: jdbc:hive2://master.hortonworks.com:10000/> select students.id,students.name, enrollment.subject from students full outer join enrollment on students.id = enrollment.student_id;
INFO  : Session is already open
DEBUG : Adding local resource: scheme: "hdfs" host: "master.hortonworks.com.hortonworks.com" port: 8020 file: "/tmp/hive/hive/_tez_session_dir/9be4477d-e925-44b2-871e-a917098d95b9/hive-hcatalog-core.jar"
INFO  : Dag name: select students.id,st...nrollment.student_id(Stage-1)
DEBUG : DagInfo: {"context":"Hive","description":"select students.id,students.name, enrollment.subject from students full outer join enrollment on students.id = enrollment.student_id"}
DEBUG : Setting Tez DAG access for hive
INFO  : 

INFO  : Status: Running (Executing on YARN cluster with App id application_1474695223471_0019)

INFO  : Map 1: 0/2	Map 3: 0/1	Reducer 2: 0/2	
INFO  : Map 1: 0(+1)/2	Map 3: 0/1	Reducer 2: 0/2	
INFO  : Map 1: 0(+1)/2	Map 3: 0(+1)/1	Reducer 2: 0/2	
INFO  : Map 1: 0(+2)/2	Map 3: 0(+1)/1	Reducer 2: 0/2	
INFO  : Map 1: 1(+1)/2	Map 3: 0(+1)/1	Reducer 2: 0/2	
INFO  : Map 1: 1(+1)/2	Map 3: 1/1	Reducer 2: 0/2	
INFO  : Map 1: 2/2	Map 3: 1/1	Reducer 2: 0(+1)/1	
INFO  : Map 1: 2/2	Map 3: 1/1	Reducer 2: 1/1	
Getting log thread is interrupted, since query is done!
+--------------+----------------+---------------------+--+
| students.id  | students.name  | enrollment.subject  |
+--------------+----------------+---------------------+--+
| 1001         | Alex           | Maths               |
| 1001         | Alex           | Science             |
| 1001         | Alex           | History             |
| 1002         | Ryan           | Physics             |
| 1002         | Ryan           | Maths               |
| 1003         | Justin         | Physics             |
| 1003         | Justin         | Social Studies      |
| 1004         | Rick           | Maths               |
| 1005         | Richard        | Zoology             |
| 1006         | Robert         | Botany              |
+--------------+----------------+---------------------+--+
10 rows selected (7.072 seconds)
```


