---
layout: post
Title: Linux MySql Usage
Date: 2013-05-10
tags: [Linux, MySql]
---

User Usage
---
1) Start Mysql
	`mysqladmin start/restart/shutdown`
2) Connect Mysql
	`$ mysql -u root -p`
   Exit Mysql
	`> exit`
3) change passwd
	`mysqladmin -u $user_name -p $old_pass password $new_pass`
   or in mysql cmd
	`> SET PASSWORD FOR root=PASSWORD("$passwd");`
4) Add new user
	`grant select on ${data}.* to ${user}@${host} identified by "$passwd"`
	如增加一个用户test密码为123，让他可以在任何主机上登录，
	并对所有数据库有查询、插入、修改、删除的权限。首先用以root用户连入mysql,
	然后键入以下命令：
	grant select,insert,update,delete on *.* to Identified by "123";
5) install mysql with yum
	`# yum install -y mysql mysql-server`
	`# mysqladmin -u root password 'new-password'`

Normal Usage
---
1) `show databases;`
1) `use $my_data；`
1) `show tables;`
1) `describe $table_name`

1) Create database
	`create database $database_name;`
	GBK : `create database test2 DEFAULT CHARACTER SET gbk COLLATE
	gbk_chinese_ci;`
	UTF8: `CREATE DATABASE `test2` DEFAULT CHARACTER SET utf8 COLLATE
	utf8_general_ci;`

1) Create tables
	`use $database_name;`
	`create table $table_name(Name varchar(255) not null, number int not
	null) default charset=utf8;`

1) Delete database and tables
	`drop database $database;`
	`drop table $table_name`
	`delete from $table_name`
	`truncate table $table_name`

1) Show tables `select * from $table_name`

1) Instert data to tables
	`instert into $table_name values("$vaule_a", "$Value_b")`

1) Show mysql encoding
	`> show variables like '%collation_%';`
	`> show variables like 'character%';`
	`> show create database $db_name;`
	`> show create table $table_name;`
	`> show table status from $tablename like ’%countries%’;`
	`> show full columns from $tablename;`
	`> show char set;`

1) Change encoding
	edit /etc/my.cnf and restart mysqld.service
```
[mysqld]
character_set_server=utf8

[client]
default-character-set=utf8

[mysqld_safe]
default-character-set=utf8
```
	change database encoding
	`alter database $db_name default character set gbk;`
	or runing `> set names utf8;` while running mysql

1) Rename table name `alter table t1 rename t2;`

1) Import txt to tables
	`mysql> load data local infile "$path/mysql.txt" into table $my_table;`

1) Create table
	`create table if not exists $table_name ( `Name` text NOT NULL, `Email 
	Address` text NOT NULL ) ENGINE=CSV DEFAULT CHARSET=utf8;`


Select and Edit tables
---
1) select usage
	select all
		`> select * from $table_name;`
	select table or title which have a space
		1. use "`" to quote the name
		2. edit my.cnf and add `sql_mode = ANSI_QUOTES` below
		`[mysqld]`, then you can use double quotation marks
		3. start mysqld with `mysqld_safe –user=mysql
		–sql-mode=ANSI_QUOTES &`

1) limit usage : limit $start_line, $offset
	select the first line 
		`> select * from $table_name limit 0,1;`
	select line 11-12
		`> select * from $table_name limit 10, 2;`
	select the first 10 lines
		`> select * from $table_name limit 10;`

1) like usage
	1. % as any zero or many charactors : abc, aabc, aabcdddd
		`> select * from $table_name where $title like '%abc%'`
	2. _ as any single charactor : aabcd
		`> select * from $table_name where $title like '_abc_'`
	3. [] will select any string in it : ac, bc
		`> select * from $table_name where $title like '[ab]c'`
	4. [^] select string which not contain string in it : cc, dc
		`> select * from $table_name where $title like '[^ab]c'`

1) Union Usage
	1. union will remove the files which are all the same.
	`select * from $t_name where $Name like "abc" union select * from
	$t_name where $Email like "abc"`
	1. union all wont remove the same files
	`select * from $t_name where $Name like "abc" union all select * from
	$t_name where $Email like "abc"`

1) some useful cmd
	1. Select something from two lines
		`select * from $table_name where concat($title, $title) like "%sth%";`

1) change/modify tables
	1. if you want to change name, use change
	`alter table $t_name change $odd $new varchar(64) not null;`
	2. if you only want to change type , use modify
	`alter table $t_name modify $line_name varchar(64) not null;`
	3. change table engine
	`alter table $t_name type=myisam;`
	4. if you want to make it default, add `default-storage-engine=INNODB`
	under [mysqld] in my.cnf
	5. change table engine;
	`alter table $t_name engine=myisam;`

1) Insert/update/delete
	1. `insert into $t_name(name,address) values('a','b');`
	1. `insert into $t_name values('a','b');`
	1. `update $t_name set Password="123" where Name="abc";`
	1. `delete from $t_name where ...`

1) Add/delete/modify field
	1. Add a new field
	`alter table $t_name add $new_field_id type not null default '0';`
	e.g. `alter table eg_table add eg_id integer not null default 372;`
	2. delete field
	` alter table $t_name drop column $colname;`
	3. change field name
	`alter table $t_name change $odd_field_name $new_name $old_type;`
	4. change field type
	`alter table $t_name change $odd_field_name $old_name $new_type;`

1) Create index : http://database.51cto.com/art/200910/156685.htm
http://www.cnblogs.com/hustcat/archive/2009/10/28/1591648.html
  1. Normal index
  	1. Create index
	`CREATE INDEX indexName ON mytable(username(length));`
	如果是CHAR，VARCHAR类型，length可以小于字段实际长度；如果是BLOB和TEXT类型，必须指定
	length
	2. modify index
	`ALTER mytable ADD INDEX [indexName] ON (username(length));`
	3. delete index
	`DROP INDEX [indexName] ON mytable;`
  1. Unique index
  `CREATE UNIQUE INDEX indexName ON mytable(username(length));`
  1. combine index
  `ALTER TABLE mytable ADD INDEX name_city_age (name(10),city,age);`
  1. when use index, some thing you need remember
  http://blog.csdn.net/csliuruidongdn/article/details/8783426


Select,Union, and Count
---
```
use $db_name;
insert into $table_4
select username,count(username)
from
(
select username from $table_1
union all
select username from $table_2
union all
select username from $table_3
) temp_table
group by username
order by count(username) desc,username asc;
```


Show database size
---
1. `use information_schema;`

1. Show all tables size 
`select concat(round(sum(data_length/1024/1024),2),'MB') as data from tables;`

1. Show database you selected
`select concat(round(sum(data_length/1024/1024),2),'MB') as data from tables
where table_schema='$your_db_name';`

1. Show a table size
`select concat(round(sum(data_length/1024/1024),2),'MB') as data from tables
where table_schema='$your_db_name' and table_name='$your_table_name'`

Backup and load
---
1) Backup : `$ mysqldump -u user -p db tab1 tab2 > db.sql`
2) Reload : `$ mysql -u user -p db < db.sql`


Errors:
---
1) ERROR 1206 (HY000): The total number of locks exceeds the lock table size

MySQL is trying to tell you that it doesn’t have enough room to store all of
the row locks that it would need to execute your query. The only way to fix it
for sure is to adjust innodb_buffer_pool_size and restart MySQL. By default,
this is set to only 8MB, which is too small for anyone who is using InnoDB to
do anything.

2) ERROR 1 (HY000): Cant create/write to file '/tmp/#sql_3fd4_0.MYI' (Errcode: 13)

Most time is because you dont have permission to write, make sure you have the
right permission. use `chmod 1777 /tmp`. If still have this problem. make
sure you have disabled seLinux `setenforce 0`.

3 ) ERROR 3 (HY000): Error writing file '/tmp/MYf1RZMk' (Errcode: 28)

This is because you dont have enough space in /tmp, you can mount other path
to /tmp, like `# mount --bind /home/tmp /tmp`

4) mysql> create index addr_index on address(Name, `QQ Number`, `Email
Address`);
ERROR 1069 (42000): Too many keys specified; max 0 keys allowed

Each ENGINE have its limit when create index. MYISAM index length limit is
1000, INNODE length limit is 767, so make sure your lines are now text.

Referfence :
[1] http://www.cnblogs.com/xiaochaohuashengmi/archive/2011/10/18/2216279.html
[2] diff about engines
http://www.cnblogs.com/sopc-mc/archive/2011/11/01/2232212.html
