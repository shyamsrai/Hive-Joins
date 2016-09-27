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





