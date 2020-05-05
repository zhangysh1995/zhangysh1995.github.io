---
layout: post
title: The MySQL Value Evaluation Trap
categories: notes database mysql
---

## Introduction
Recently I found several interesting behavior of the operation on values, including float and int.

### Float vs. Double
For example, let's run the following query:

````
mysql> select -0.1 = -0.1;
+-------------+
| -0.1 = -0.1 |
+-------------+
|           1 |
+-------------+
1 row in set (0.00 sec)
````

It returns `1` which means the result is `true`. Let's construct two tables, the first one with `float`,
and the second one with `double`.

````
mysql> create table t0(foo float);
Query OK, 0 rows affected (0.00 sec)
mysql> insert into  t0 values(0.1);
Query OK, 1 row affected (0.00 sec)

mysql> create table t1(foo double);
Query OK, 0 rows affected (0.00 sec)
mysql> insert into  t1 values(0.1);
Query OK, 1 row affected (0.00 sec)

mysql> desc t0;
+-------+-------+------+------+---------+-------+
| Field | Type  | Null | Key  | Default | Extra |
+-------+-------+------+------+---------+-------+
| foo   | float | YES  |      | NULL    |       |
+-------+-------+------+------+---------+-------+
1 row in set (0.00 sec)

mysql> desc t1;
+-------+--------+------+------+---------+-------+
| Field | Type   | Null | Key  | Default | Extra |
+-------+--------+------+------+---------+-------+
| foo   | double | YES  |      | NULL    |       |
+-------+--------+------+------+---------+-------+
1 row in set (0.00 sec)
````

Let's run two queries:

````
mysql> select * from t0 where foo=0.1;
Empty set (0.00 sec)

mysql> select * from t1 where foo=0.1;
+------+
| foo  |
+------+
|  0.1 |
+------+
1 row in set (0.00 sec)
````

The first query returns empty set from the table with type `float`. The second query retuns the row we inserted but with type `double`. Why does it ouput different results? Let's see:

````
mysql> select foo+foo from t0;
+---------------------+
| foo+foo             |
+---------------------+
| 0.20000000298023224 |
+---------------------+
1 row in set (0.01 sec)

mysql> select foo+foo from t1;
+---------+
| foo+foo |
+---------+
|     0.2 |
+---------+
1 row in set (0.00 sec)
````

Because of precision, they stores the value differently. In other words, `0.1` cannot be represented precisely with `float type`.

See [MySQL bug report](https://bugs.mysql.com/bug.php?id=1961), [Stackoverflow discussion](https://stackoverflow.com/questions/2160810/mysql-whats-the-difference-between-float-and-double) for details.

### Int vs. Unsigned

Let's see an [example](https://bugs.mysql.com/bug.php?id=20093) first:

````
mysql> create table mhtest (id int unsigned, score int);
insertQuery OK, 0 rows affected (0.02 sec)

 mysql> insert into mhtest values (1,-1);
Query OK, 1 row affected (0.00 sec)

smysql> select id * score from mhtest;
+----------------------+
| id * score           |
+----------------------+
| 18446744073709551615 |
+----------------------+
1 row in set (0.00 sec)
````

Well, this result is really unexpected. But where did this number come?

```
mysql> select cast(-1 as unsigned);
+----------------------+
| cast(-1 as unsigned) |
+----------------------+
| 18446744073709551615 |
+----------------------+
1 row in set (0.00 sec)
````

Using `2's compelent`, the number `-1` is represented as `11111111 11111111 .... (64 bits of 1)`, and this binary number is exactly the integer `18446744073709551615`.

However, the issue here is the default type of the result value. Currently, it is an `unsigned` instead of a normal `int`.

See [A Tutorial on Data Representation](https://www3.ntu.edu.sg/home/ehchua/programming/java/datarepresentation.html) for an explanation on the binary representation of numbers.
