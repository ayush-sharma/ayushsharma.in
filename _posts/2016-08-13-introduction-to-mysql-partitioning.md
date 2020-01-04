---
layout: post
title:  "Introduction to MySQL Partitioning"
number: 7
date:   2016-08-13 2:00
categories: databases
---
These notes are for partitioning in MySQL for version 5.6.

## Introduction
A nice thing about SQL is that it is declarative. You can tell SQL what you want and it will figure out how you get it. This makes life a lot easier. But at a certain threshold, you want to optimise how the underlying data is actually stored on your machine. That is where partitioning comes in. It allows you to dictate how the data is stored, based on something called a partition function.

Let's say you have a lot of employee data in one gigantic table. Let's say you want a list of all employees who joined in 2016. Right now you need to scan the entire table and find out this information. Wouldn't it be nice if everyone who joined in 2016 be in one place, and all the 2015 guys in another? You could of course manually create all these tables, or you could let MySQL do it on its own in the background. That's basically how partitioning works. You operate on one table and let MySQL figure out where that data goes behind the scenes.

Before I begin let me point out that Partitioning is not a silver bullet solution for performance issues. It is just another way of optimizing queries. In order to decide whether you need partitioning or not, and what kind of partitioning you need, you should first list down all the queries that will run against the table and go from there. Use `explain` to figure out your queries.

MySQL has different ways in which you could partition your data. But before we can do that, you need to check if your database installation supports partitioning.

## Check for Support
Run any of the following queries and check if partitioning is active.

- `SHOW PLUGINS`;
- `SELECT plugin_name, plugin_version, plugin_status FROM information_schema.plugins`;

One more thing. There are two kinds of partitioning. The first is horizontal partitioning, where different rows are assigned to different physical partitions. This is what we're talking about in this post. The second is vertical partitioning, in which different columns are assigned to different physical partitions. MySQL does not support vertical partitioning as of now.

## Types of Partitioning
MySQL supports several types of partitioning:

- Range: This partitioning saves rows in a partition based on the value of a column falling within a given range.
- List: This partitioning saves rows in the partition which matches the value of a column against a given list of values.
- Hash: This partitioning saves rows based on a non-negative integer value returned by a user-defined function.
- Key: This partitioning saves rows based on the value returned by an internal MySQL hashing function.

### Range Partitioning
When you partition a table by using range partitioning, the values are placed in a particular partition as long as the value falls within the given range for that partition. Ranges should be contiguous but not overlapping, and are defined using the `VALUES LESS THAN` operator. For example:

```sql
CREATE TABLE range_test (
     `id` INTEGER NOT NULL,
     `some_date` DATE NOT NULL
)
PARTITION BY RANGE(id) (
     PARTITION p0 VALUES LESS THAN (5),
     PARTITION p1 VALUES LESS THAN (10),
     PARTITION p2 VALUES LESS THAN (15),
     PARTITION p3 VALUES LESS THAN (20)
);
```

Each partition must be defined in order, from lowest to highest. The value you specify is not included in the range, meaning if we insert the value 5 in the above table, it will go in the second partition.

Let's insert the values from 1 to 20 in this table. The table now looks like this:

```
+----+------------+
| id | some_date  |
+----+------------+
|  1 | 2016-05-20 |
|  2 | 2016-05-20 |
|  3 | 2016-05-20 |
|  4 | 2016-05-20 |
|  5 | 2016-05-20 |
|  6 | 2016-05-20 |
|  7 | 2016-05-20 |
|  8 | 2016-05-20 |
|  9 | 2016-05-20 |
| 10 | 2016-05-20 |
| 11 | 2016-05-20 |
| 12 | 2016-05-20 |
| 13 | 2016-05-20 |
| 14 | 2016-05-20 |
| 15 | 2016-05-20 |
| 16 | 2016-05-20 |
| 17 | 2016-05-20 |
| 18 | 2016-05-20 |
| 19 | 2016-05-20 |
+----+------------+
```

Note that when you try to insert the value 20, it will give you the following error:
`ERROR 1526 (HY000): Table has no partition for value 20`.
That's because the cut off values defined in the partition expression will go into the next partition, meaning the greatest value that can go in this table is 19, and there is no partition defined for 20.

To see which partitions are being used, execute the following query:
`SELECT table_schema, table_name, partition_name, partition_method, partition_expression, table_rows FROM information_schema.partitions WHERE table_schema = "partition_test";`
You should see something like this:

```
+----------------+------------+----------------+------------------+----------------------+------------+
| table_schema   | table_name | partition_name | partition_method | partition_expression | table_rows |
+----------------+------------+----------------+------------------+----------------------+------------+
| partition_test | range_test | p0             | RANGE            | id                   |          4 |
| partition_test | range_test | p1             | RANGE            | id                   |          5 |
| partition_test | range_test | p2             | RANGE            | id                   |          5 |
| partition_test | range_test | p3             | RANGE            | id                   |          5 |
+----------------+------------+----------------+------------------+----------------------+------------+
```

Here you can see the information for your partitioned table.

If you want a partition that will contain all values defined after 19, then you can defined a partition like so:
`PARTITION p3 VALUES LESS THAN MAXVALUE`

We can also define partitions based on the `DATE` column. It might be something like this:

```sql
CREATE TABLE range_test_2 (
     `id` INTEGER NOT NULL,
     `some_date` DATE NOT NULL
)
PARTITION BY RANGE(YEAR(some_date)) (
     PARTITION p0 VALUES LESS THAN (2000),
     PARTITION p1 VALUES LESS THAN (2005),
     PARTITION p2 VALUES LESS THAN (2010),
     PARTITION p3 VALUES LESS THAN (2015),
     PARTITION p4 VALUES LESS THAN MAXVALUE
);
```

You can use a lot of date functions to define partitions, like: `UNIX_TIMESTAMP()`, `TO_DAYS()`, `TO_SECONDS()`, `WEEKDAY()`, `DAYOFYEAR()`, `MONTH()`, etc. You can use pretty much any date function that returns an integer or null.

There are certain advantages of using this kind of partitioning:

- To delete old data, you can simply delete the partition that contains that data. For example: `ALTER TABLE range_test DROP PARTITION p0;`
- Queries which use the column you've partitioned on can automatically figure out which partitions to use. For example, try this: `EXPLAIN PARTITIONS SELECT COUNT(*) FROM range_test WHERE id < 4;`. The above command will show you which partitions the query will look in for data.

If the value of the partitioning expression evaluates to `NULL`, the row will be inserted in the lowest partition.

### List Partitioning
Partitioning a table using list partitioning means saving data into partitions based on a predefined list of values. For example:

```sql
CREATE TABLE list_test (
     `id` INTEGER NOT NULL,
     `some_date` DATE NOT NULL
)
PARTITION BY LIST(id) (
     PARTITION p0 VALUES IN (NULL, 1, 2, 3, 4, 5),
     PARTITION p1 VALUES IN (6, 7, 8, 9, 10)
);
```

Unlike range, there is no `MAXVALUE` catch-all for list partitions. All partitions must be explicitly defined. Also, if you want to keep data containing `NULL` in any partition, it should be explicitly defined in the list of values.

### Range Columns Partitioning
This type of partitioning is a variation of Range partitioning, which has certain advantages over normal Range partitioning. It allows partitioning on multiple columns and non-integer columns. It does not, however, support expressions, and only allows list of columns. When using multiple columns, the range of all columns is checked. You can also specify limiting conditions in multiple definitions, as long as they are strictly increasing. Like so:

```sql
CREATE TABLE range_columns_string_test (
     `id` INTEGER NOT NULL,
     `first_name` VARCHAR(30),
     `last_name` VARCHAR(30)

)
PARTITION BY RANGE COLUMNS(first_name, last_name) (
     PARTITION p0 VALUES LESS THAN ('a', 'h'),
     PARTITION p1 VALUES LESS THAN ('h', 'p'),
     PARTITION p2 VALUES LESS THAN ('p', MAXVALUE)

);
```

Different character sets and collations have different sorting orders, which will affect how rows are distributed in different partitions.

We can also use `DATE` field with range column partitioning:

```sql
CREATE TABLE range_columns_date_test (
     `id` INTEGER NOT NULL,
     `some_date` DATE NOT NULL
)
PARTITION BY RANGE COLUMNS(some_date) (
     PARTITION p0 VALUES LESS THAN ('2000-01-01'),
     PARTITION p1 VALUES LESS THAN ('2005-01-01'),
     PARTITION p2 VALUES LESS THAN ('2010-01-01')
);
```

### List Columns Partitioning
This is an extension of the list partitioning scheme, which allows you to use non-integer columns in partition function, and allows you to use multiple columns in the partition definition. For example:

```sql
CREATE TABLE list_columns_test (
     `id` INTEGER NOT NULL,
     `some_text` VARCHAR(30)
)
PARTITION BY LIST COLUMNS (some_text) (
     PARTITION p0 VALUES IN ('a', 'b', 'c', 'd'),
     PARTITION p1 VALUES IN ('e', 'f', 'g', 'h')
);
```

### Hash Partitioning
With Range or List partitioning, the partitions need to be explicitly defined. With Hash partitioning, you can specify an integer partition function and the number of partitions to be created, and MySQL will take care of the rest. The partition function can be an expression that returns an integer, or be the name of the column which is an integer type. The default number of partitions is 1. For example:

```sql
CREATE TABLE hash_test (
     `id` INTEGER NOT NULL,
     `some_text` VARCHAR(30),
     `some_id` INTEGER NOT NULL
)
PARTITION BY HASH(some_id)
PARTITIONS 10;
```

Another example could be:

```sql
CREATE TABLE hash_test_employees (
     `id` INTEGER NOT NULL,
     `first_name` VARCHAR(30),
     `last_name` VARCHAR(30),
     `joining_date` DATE NOT NULL
)
PARTITION BY HASH(YEAR(joining_date))
PARTITIONS 5;
```

Good partition functions are those whose values vary proportionately with every change in the value of the partition column, i.e., the graph of the column value and the column expression must be a straight line traced by y = cx. To determine which partition the row goes into, it evaluates the following expression:

`Partition number = MOD(expression, number of partitions)`

### Linear Hash Partitioning
This is a variation of the Hash partitioning. The difference is, that the Hash partitioning employs a modulus function to determine the partition number, but Linear Hash employs a linear power of 2 algorithm. The algorithm is:

- V = Power(2, Ceiling(Log(2, num)))
- N = F(column_list) & (V - 1)
- While N >= Num:
    - Set V = Ceil(V/2)
    - Set N = N & (V-1)

The disadvantage is that the data is less likely to be evenly distributed as compared to Hash partitioning.

### Key Partitioning
Key partitioning is like Hash partitioning, the difference being that in this partitioning scheme, the partition function is supplied by MySQL, which is either `MD5` or `PASSWORD`. Non-integer columns can also be used in the partition expression. If no column is specified, the primary key column is used.

```sql
CREATE TABLE key_test (
     `id` INTEGER NOT NULL,
     `some_text` VARCHAR(30) NOT NULL
)
PARTITION BY KEY(some_text)
PARTITIONS 5;
```

The `DATE`, `TIME`, and `DATETIME` columns can also be used in the partitioning expression.

### Linear Key Partitioning
Much like Linear Hash partitioning, Linear Key partitioning relies on a power of 2 function instead of a modulus function to distribute data among partitions.

If you've experimented with a few partition types and have file-per-table enabled, head on over to your MySQL data directory and see if you can spot your partition files.

## Things to remember

- All columns used in the partitioning expression must be part of every unique key of that table.
- All partitions of one table must use the same storage engine.
- Partitioning applies to both indexes and tables.
- Certain functions, like `YEAR()` and `TO_DAYS()`, are specially optimised for use with partitions.

## Partitioning Information
To view partitioning information:

- Using the `SHOW CREATE TABLE` statement to view the partitioning clauses used in creating a partitioned table.
- Using the `SHOW TABLE STATUS` statement to determine whether a table is partitioned.
- Querying the `INFORMATION_SCHEMA.PARTITIONS` table.
- Using the statement `EXPLAIN PARTITIONS SELECT` to see which partitions are used by a given `SELECT`. This is useful to figure out which partitions are involved in a given select statement.

## Sub partitioning/Composite Partitioning
Sub partitioning allows you to further divide a given table into more partitions.

```sql
CREATE TABLE subpartition_test (
     `id` INTEGER NOT NULL,
     `joining_date` DATE,
     `employee_id` INTEGER
)
PARTITION BY RANGE(YEAR(joining_date))
SUBPARTITION BY HASH (employee_id) (
     PARTITION p0 VALUES LESS THAN (1990) (
          SUBPARTITION s0,
          SUBPARTITION s1
     ),
     PARTITION p1 VALUES LESS THAN (2000) (
          SUBPARTITION s2,
          SUBPARTITION s3
     )
);
```

Now let's add some data to the above table to it looks like this:

```
+----+--------------+-------------+
| id | joining_date | employee_id |
+----+--------------+-------------+
|  1 | 1980-01-01   |           1 |
|  1 | 1981-01-01   |           2 |
|  1 | 1982-01-01   |           3 |
|  1 | 1983-01-01   |           4 |
|  1 | 1984-01-01   |           5 |
|  1 | 1985-01-01   |           6 |
|  1 | 1986-01-01   |           7 |
|  1 | 1987-01-01   |           8 |
|  1 | 1988-01-01   |           9 |
|  1 | 1989-01-01   |          10 |
|  1 | 1990-01-01   |          11 |
|  1 | 1991-01-01   |          12 |
|  1 | 1992-01-01   |          13 |
|  1 | 1993-01-01   |          14 |
|  1 | 1994-01-01   |          15 |
|  1 | 1995-01-01   |          16 |
|  1 | 1996-01-01   |          17 |
|  1 | 1997-01-01   |          18 |
|  1 | 1998-01-01   |          19 |
|  1 | 1999-01-01   |          20 |
+----+--------------+-------------+
```

Now let's see where all that data ended up:

`SELECT table_schema, table_name, partition_name, partition_method, partition_expression, subpartition_name, subpartition_method, subpartition_expression, table_rows FROM information_schema.partitions WHERE table_schema = "partition_test" AND table_name = "subpartition_test";`


```
+----------------+-------------------+----------------+------------------+----------------------+-------------------+---------------------+-------------------------+------------+
| table_schema   | table_name        | partition_name | partition_method | partition_expression | subpartition_name | subpartition_method | subpartition_expression | table_rows |
+----------------+-------------------+----------------+------------------+----------------------+-------------------+---------------------+-------------------------+------------+
| partition_test | subpartition_test | p0             | RANGE            | YEAR(joining_date)   | s0                | HASH                | employee_id             |          5 |
| partition_test | subpartition_test | p0             | RANGE            | YEAR(joining_date)   | s1                | HASH                | employee_id             |          5 |
| partition_test | subpartition_test | p1             | RANGE            | YEAR(joining_date)   | s2                | HASH                | employee_id             |          5 |
| partition_test | subpartition_test | p1             | RANGE            | YEAR(joining_date)   | s3                | HASH                | employee_id             |          5 |
+----------------+-------------------+----------------+------------------+----------------------+-------------------+---------------------+-------------------------+------------+
```

Some things to note:

- Each partition must have the same number of sub partitions.
- If you explicitly define one sub partition, you must define them all.

It is also possible to define data and index directories for individual partitions.

```
CREATE TABLE subpartition_directories_test (
     `id` INTEGER NOT NULL,
     `joining_date` DATE,
     `employee_id` INTEGER
)
PARTITION BY RANGE(YEAR(joining_date))
SUBPARTITION BY HASH (employee_id) (
     PARTITION p0 VALUES LESS THAN (1990) (
          SUBPARTITION s0
               DATA DIRECTORY = '/tmp/disk1/data'
               INDEX DIRECTORY = '/tmp/disk1/index',
          SUBPARTITION s1
               DATA DIRECTORY = '/tmp/disk2/data'
               INDEX DIRECTORY = '/tmp/disk2/index'

     ),
     PARTITION p1 VALUES LESS THAN (2000) (
          SUBPARTITION s2
               DATA DIRECTORY = '/tmp/disk3/data'
               INDEX DIRECTORY = '/tmp/disk3/index',
          SUBPARTITION s3
               DATA DIRECTORY = '/tmp/disk4/data'
               INDEX DIRECTORY = '/tmp/disk4/index'
     )
);
```

## Managing Partitions
You can use several commands to manage partitions:

- You can remove partitioning without losing data with the following command:
`ALTER TABLE table_name REMOVE PARTITIONING;`.
- You can truncate a partition with:
`ALTER TABLE table_name TRUNCATE PARTITION p0;`.
- You can drop a partition with:
`ALTER TABLE table_name DROP PARTITION p0;`.
- You can alter the partition definition of a partitioned table using:
`ALTER TABLE table_name PARTITION BY KEY(id) PARTITIONS 2;`. This will create a new partitioned table, copy over all the rows, and then drop the old non-partitioned table.
- If a List partition is dropped then, unlike range, any values that could have been inserted into the dropped partition will no longer be inserted.
- Range partitions can only be added after the highest partition was defined; they cannot be added in the beginning or in the middle.
- Existing data can be reorganised into new partitions.
`ALTER TABLE table_name REORGANISE PARTITION p0 INTO (PARTITION s0 VALUES LESS THAN (10), PARTITION s1 VALUES LESS THAN (20));`. No data is lost with this command.
- Existing partitions can be merged into one: `ALTER TABLE table_name REORGANISE PARTITION s0,s1 INTO (PARTITION p0 VALUES LESS THAN (20));`. No data is lost with this command.
- For managing Hash and Key partitions:
`ALTER TABLE table_name ADD PARTITION PARTITIONS 6;` and 
`ALTER TABLE table_name COALESCE PARTITION 4;`.
- Partitions can also be exchanged.
`ALTER TABLE table1 EXCHANGE PARTITION p1 WITH table2;`. Exchanging partitions does not invoke triggers, and auto-increment columns in exchanged tables are reset. The above command will work, provided:
    - User has `DROP` privileges.
    - Table2 is not itself partitioned and is not a temporary table.
    - Structure of both tables is identical. The number, order, names, and types of columns and indexes of the partitioned table and the non-partitioned table must match exactly. In addition, both tables must use the same storage engine.
    - Table1 does not participate in any foreign keys.
    - There are no rows in table2 which lie outside the boundaries of partition definition of p1.

## Maintenance of Partitions
You can use several commands to maintain partitions:

- Partitions can be rebuilt using the following: `ALTER TABLE table_name REBUILD PARTITION p0;`. This command can be useful for defragmenting partitions. It essentially drops all records and reinserts them.
- To reclaim disk space: `ALTER TABLE table_name OPTIMISE PARTITION p0;`.
- Others:
    - `ALTER TABLE table_name ANALYZE PARTITION p0;`
    - `ALTER TABLE table_name REPAIR PARTITION p0;`
    - `ALTER TABLE table_name CHECK PARTITION p0;`

## Partition Pruning
Partition pruning is what partitioning is really about. It involves getting the MySQL query optimiser to only search those partitions where it is going to find data. For example, if you've created partitions by year, then the trick is to get MySQL to search only the partition for the year that you need. You can find out what partition a query will be using by sticking `EXPLAIN PARTITIONS` in front of your query. Pruning can be applied to `SELECT`s, `UPDATE`s, and `DELETE`s.

## Partition Selection
Partition selection is another aspect of partitioning. It’s like pruning, expect here you get to specify which partitions to search in the query itself. For example:

```sql
SELECT * FROM table_name PARTITION (p0);
```

You can use partition selection with `SELECT`s, `INSERT`s, `DELETE`s, `UPDATE`s, and a few more.

## Resources

- [https://dev.mysql.com/doc/refman/5.6/en/partitioning-overview.html](https://dev.mysql.com/doc/refman/5.6/en/partitioning-overview.html)
- [http://www.slideshare.net/datacharmer/mysql-partitions-tutorial](http://www.slideshare.net/datacharmer/mysql-partitions-tutorial)
- [http://dba.stackexchange.com/questions/tagged/partitioning+mysql](http://dba.stackexchange.com/questions/tagged/partitioning+mysql)