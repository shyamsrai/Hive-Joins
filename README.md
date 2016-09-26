# Joins in Hive

Primer introduction to JOINs in Hive Database. This repository includes hands on introduction on types of common JOINs in SQL and JOINs specific to Hive database in the Hadoop world, with examples.

# Equijoin/Inner join/Simple Join
Known with different names, same type of join.

# Equi-Join 
Retrieving data from two tables with a common joining column. For instance, joining student ID with table enrollment (student ID) to find out how many students enrolled for a particular course. 

Create tables <br>
<code>
create table students (id int, name string) row format delimited fields terminated by ',' stored as orc; 
</code>
<code>
create table enrollment (student_id int, subject string) row format delimited fields terminated by ',' stored as orc; <br>
</code>

Insert sample data <br>
<code>
insert into students values (1001,'Alex'),(1002,'Ryan'),(1003,'Justin'); 
</code>
<code>
insert into enrollment values (1001,'Maths'),(1001,'Science'),(1001,'History'),(1002,'Physics'),(1002,'Maths'),(1003,'Physics'),(1003,'Social Studies'),(1004,'Maths'); <br>
</code>


