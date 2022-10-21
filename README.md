# MySQL

- MySQL Ver 15.1 Distrib 10.9.2-MariaDB, for Android (aarch64) using EditLine wrapper
- Termux v0.118.0
- Android 11

- `mysqld_safe -u $(whoami) # server`
- `mysql -u $(whoami) # client`

## Snippets
#### list all databases
```sql
SHOW databases;
```

#### use database
```sql
USE <db_name>;
```

#### display current db
```sql
SELECT DATABASE();
SELECT DATABASE() FROM DUAL;  -- legacy
```

#### list all tables
```sql
SHOW tables;
```

#### create a database
```sql
CREATE DATABASE <db_name>;
```

#### create a table
```sql
CREATE TABLE <table_name>(<first_colname> <type>(<size>));
```

#### create a table, 1st row can't be NULL
```sql
CREATE TABLE <table_name>(<first_colname> <type>(<size>) NOT NULL);
```

#### insert row
```sql
INSERT INTO <table_name>(<colname_list>) VALUES (<col_val_list>);
```

#### list all columns in a table
```sql
DESCRIBE <table_name>;
```

#### drop a table
```sql
DROP TABLE <table_name>;
```

#### drop a database
```sql
DROP DATABASE <db_name>;
```

#### change a column
```sql
ALTER TABLE <table_name>
    CHANGE <colname> <newname> <type>;
```

#### add a column
```sql
ALTER TABLE <table_name>
    ADD <colname> <type> <?NOT NULL>
    AFTER <colname>;
```

#### drop a column
```sql
ALTER TABLE <tab_name>
    DROP COLUMN <colname>;
```

#### update a row
```sql
UPDATE <table_name>
    SET <colname> = <literal>
    WHERE <condition>;
```

## Example
```
MariaDB [testbase]> select * as data from tab1;
ERROR 1064 (42000): You have an error in your SQL syntax; check the manual that corresponds to your MariaDB server version for the right syntax to use near 'as data from tab1' at line 1
MariaDB [testbase]> select * from tab1;
+-------+
| sl_no |
+-------+
|     0 |
|     1 |
+-------+
2 rows in set (0.062 sec)

MariaDB [testbase]> if (select * from tab1) > 5, 'yes', 'no';
ERROR 1064 (42000): You have an error in your SQL syntax; check the manual that corresponds to your MariaDB server version for the right syntax to use near ' 'yes', 'no'' at line 1
MariaDB [testbase]> if ((select * from tab1) > 5, 'yes', 'no');
ERROR 1064 (42000): You have an error in your SQL syntax; check the manual that corresponds to your MariaDB server version for the right syntax to use near '' at line 1
MariaDB [testbase]> select sl_no as @s from tab1;
ERROR 1064 (42000): You have an error in your SQL syntax; check the manual that corresponds to your MariaDB server version for the right syntax to use near '@s from tab1' at line 1
MariaDB [testbase]> select sl_no as s from tab1;
+---+
| s |
+---+
| 0 |
| 1 |
+---+
2 rows in set (0.001 sec)

MariaDB [testbase]> if (s>1, 'yes', 'no');
ERROR 1327 (42000): Undeclared variable: s
MariaDB [testbase]> if (@s>1, 'yes', 'no');
ERROR 1064 (42000): You have an error in your SQL syntax; check the manual that corresponds to your MariaDB server version for the right syntax to use near '' at line 1
MariaDB [testbase]> select sl_no as s from tab1
    -> if (s>1, 'yes', 'no');
ERROR 1064 (42000): You have an error in your SQL syntax; check the manual that corresponds to your MariaDB server version for the right syntax to use near 'if (s>1, 'yes', 'no')' at line 2
MariaDB [testbase]> select sl_no as s if (s>1, 'yes', 'no') from tab1;
ERROR 1064 (42000): You have an error in your SQL syntax; check the manual that corresponds to your MariaDB server version for the right syntax to use near 'if (s>1, 'yes', 'no') from tab1' at line 1
MariaDB [testbase]> select sl_no as s if (sl_no>1, 'yes', 'no') from tab1;
ERROR 1064 (42000): You have an error in your SQL syntax; check the manual that corresponds to your MariaDB server version for the right syntax to use near 'if (sl_no>1, 'yes', 'no') from tab1' at line 1
MariaDB [testbase]> select sl_no if (sl_no>1, 'yes', 'no') from tab1;
ERROR 1064 (42000): You have an error in your SQL syntax; check the manual that corresponds to your MariaDB server version for the right syntax to use near 'if (sl_no>1, 'yes', 'no') from tab1' at line 1
MariaDB [testbase]> select sl_no as s, if (sl_no>1, 'yes', 'no') from t
ab1;
+---+---------------------------+
| s | if (sl_no>1, 'yes', 'no') |
+---+---------------------------+
| 0 | no                        |
| 1 | no                        |
+---+---------------------------+
2 rows in set (0.025 sec)

MariaDB [testbase]> select sl_no as s, if (s>1, 'yes', 'no') from tab1;
ERROR 1054 (42S22): Unknown column 's' in 'field list'
MariaDB [testbase]> select sl_no as s, if (sl_no>1, 'yes', 'no') from tab1;
+---+---------------------------+
| s | if (sl_no>1, 'yes', 'no') |
+---+---------------------------+
| 0 | no                        |
| 1 | no                        |
+---+---------------------------+
2 rows in set (0.002 sec)

MariaDB [testbase]> select sl_no, field1, if (sl_no>1, 'yes', 'no') from tab1;
ERROR 1054 (42S22): Unknown column 'field1' in 'field list'
MariaDB [testbase]> describe tab1;
+-------+---------+------+-----+---------+-------+
| Field | Type    | Null | Key | Default | Extra |
+-------+---------+------+-----+---------+-------+
| sl_no | int(11) | NO   |     | NULL    |       |
+-------+---------+------+-----+---------+-------+
1 row in set (0.016 sec)

MariaDB [testbase]> alter table
    -> add column field1 varchar(32) not null
    -> ;
ERROR 1064 (42000): You have an error in your SQL syntax; check the manual that corresponds to your MariaDB server version for the right syntax to use near 'add column field1 varchar(32) not null' at line 2
MariaDB [testbase]> alter table tab1 add column field1 varchar(32) not null;
Query OK, 0 rows affected (0.031 sec)
Records: 0  Duplicates: 0  Warnings: 0

MariaDB [testbase]> describe tab1;
+--------+-------------+------+-----+---------+-------+
| Field  | Type        | Null | Key | Default | Extra |
+--------+-------------+------+-----+---------+-------+
| sl_no  | int(11)     | NO   |     | NULL    |       |
| field1 | varchar(32) | NO   |     | NULL    |       |
+--------+-------------+------+-----+---------+-------+
2 rows in set (0.011 sec)

MariaDB [testbase]> update tab1
    -> set field1 = 'dat1'
    -> where sl_no = 0;
Query OK, 1 row affected (0.015 sec)
Rows matched: 1  Changed: 1  Warnings: 0

MariaDB [testbase]> update tab1 set field1 = 'dat2' where sl_no = 1;
Query OK, 1 row affected (0.014 sec)
Rows matched: 1  Changed: 1  Warnings: 0

MariaDB [testbase]> select sl_no, field1 from tab1;
+-------+--------+
| sl_no | field1 |
+-------+--------+
|     0 | dat1   |
|     1 | dat2   |
+-------+--------+
2 rows in set (0.002 sec)

MariaDB [testbase]> select sl_no, field1, if (sl_no>1, 'yes', 'no') from tab1;
+-------+--------+---------------------------+
| sl_no | field1 | if (sl_no>1, 'yes', 'no') |
+-------+--------+---------------------------+
|     0 | dat1   | no                        |
|     1 | dat2   | no                        |
+-------+--------+---------------------------+
2 rows in set (0.003 sec)

MariaDB [testbase]> select sl_no, field1, if (sl_no<1, 'yes', 'no') from tab1;
+-------+--------+---------------------------+
| sl_no | field1 | if (sl_no<1, 'yes', 'no') |
+-------+--------+---------------------------+
|     0 | dat1   | yes                       |
|     1 | dat2   | no                        |
+-------+--------+---------------------------+
2 rows in set (0.003 sec)

MariaDB [testbase]> select sl_no, field1, if (field1 = null, 'yes', 'no') from tab1;
+-------+--------+---------------------------------+
| sl_no | field1 | if (field1 = null, 'yes', 'no') |
+-------+--------+---------------------------------+
|     0 | dat1   | no                              |
|     1 | dat2   | no                              |
+-------+--------+---------------------------------+
2 rows in set (0.002 sec)

MariaDB [testbase]> insert into tab1(sl_no, field1) values (3, 'dat3');
Query OK, 1 row affected (0.029 sec)

MariaDB [testbase]> select sl_no, field1, if (field1 = null, 'yes', 'no') from tab1;
+-------+--------+---------------------------------+
| sl_no | field1 | if (field1 = null, 'yes', 'no') |
+-------+--------+---------------------------------+
|     0 | dat1   | no                              |
|     1 | dat2   | no                              |
|     3 | dat3   | no                              |
+-------+--------+---------------------------------+
3 rows in set (0.002 sec)

MariaDB [testbase]> insert into tab1(sl_no, field1) values (2, null);
ERROR 1048 (23000): Column 'field1' cannot be null
MariaDB [testbase]> insert into tab1(sl_no, field1) values (2, '');
Query OK, 1 row affected (0.014 sec)

MariaDB [testbase]> select sl_no, field1, if (field1 = null, 'yes', 'no') from tab1;
+-------+--------+---------------------------------+
| sl_no | field1 | if (field1 = null, 'yes', 'no') |
+-------+--------+---------------------------------+
|     0 | dat1   | no                              |
|     1 | dat2   | no                              |
|     3 | dat3   | no                              |
|     2 |        | no                              |
+-------+--------+---------------------------------+
4 rows in set (0.003 sec)

MariaDB [testbase]> select sl_no, field1, if (field1 = null or field1 = '', 'yes', 'no') from tab1;
+-------+--------+------------------------------------------------+
| sl_no | field1 | if (field1 = null or field1 = '', 'yes', 'no') |
+-------+--------+------------------------------------------------+
|     0 | dat1   | no                                             |
|     1 | dat2   | no                                             |
|     3 | dat3   | no                                             |
|     2 |        | yes                                            |
+-------+--------+------------------------------------------------+
4 rows in set (0.001 sec)

MariaDB [testbase]> quit
Bye
```
