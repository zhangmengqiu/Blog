title: MySQL命令
author: Tao Feng
tags:
  - Hacking
  - Research Path
categories: []
date: 2016-12-14 09:25:00
---
性能好，安装使用方便，又免费开源的数据库肯定少不了MySQL。当然PostgreSQL性能更优秀，也是开源，不过我们入门使用MySQL肯定是没错。这里就是一些基本的MySQL的知识。

### Common Commands

login in shell

```bash
mysql -uroot -p
```

mysql>

```sql
SHOW DATABASES;
CREATE DATABASE db;
DROP DATABASE db;

USE db;
SHOW TABLES;
DESCRIBE tb;
SELECT * FROM tb;
INSERT INTO tb(`a`,`b`) VALUES("a","b");
UPDATE tb SET f="f" WHERE 1=1;
DELETE FROM tb WHERE 1=1;
```
<!-- more -->

data dump

```bash
mysqldump -uroot -p db tb > db.sql
```
        
### Basic

#### Create Database

```sql
CREATE DATABASE `db_name`;
USE db_name;
```

#### Create/Drop Table

```sql
CREATE TABLE `tb_name`
(
id INT(11) PRIMARY KEY AUTO_INCREMENT NOT NULL,
name VARCHAR(25) NOT NULL,
deptId INT(11) DEFAULT 1111,
salary FLOAT
)DEFAULT CHARSET=`utf8` AUTO_INCREMENT=1;
DROP TABLE IF EXISTS tb_dept;
```

#### Field/FK

```sql
ALTER TABLE tb_dept ADD column1 VARCHAR(11) NOT NULL;
ALTER TABLE tb_dept ADD column2 INT(11) FIRST;
ALTER TABLE tb_dept ADD column3 AFTER name;
ALTER TABLE tb_Dept MODIFY column1 VARCHAR(12) AFTER location;
UPDATE article a SET a.journalid=10 WHERE a.journalid=20;
DELETE from journal WHERE journalid=20;

ALTER TABLE `article` 
ADD CONSTRAINT `c_article_journal_FK` 
FOREIGN KEY (`journalid`)  
REFERENCES `journal` (`journalid`);

UPDATE article SET article.journalid =3 
WHERE article.journalid IN
(SELECT journalid 
FROM 
(SELECT journal.journalid, journal 
FROM article LEFT JOIN journal 
ON article.journalid=journal.journalid) AS c 
GROUP BY journalid
HAVING COUNT(*)<20);
SELECT COUNT(*), journal, journalid 
FROM
(SELECT journal.journalid, journal 
FROM article LEFT JOIN journal 
ON article.journalid=journal.journalid) AS c 
GROUP BY journalid 
ORDER BY COUNT(*) ASC;
```

#### View

```sql
CREATE VIEW view_name AS SELECT ...;
UPDATE view_name SET ...;
```

#### Trigger

```sql
CREATE TRIGGER trigger_name trigger_time trigger_event ON tb_name FOR EACH ROW trigger_stmt;
CREATE TRIGGER ins_sum 
BEFORE INSERT ON account 
FOR EACH ROW 
SET @sum=@sum+NEW.amount;
```

#### Insert

```sql
        INSERT INTO tb_name(...) values(...),(...),...;
```

#### User 

```sql
        CREATE USER 'jeffery'@'localhost' IDENTIFIED BY 'mypass';
        GRANT privileges ON db.table TO user@host [IDENTIFIED BY 'password'] [WITH GRANT OPTION];
        DROP USER 'user'@'localhost';
        SET PASSWORD FOR 'user'@'host' = PASSWORD('otherpassword');
        SET PASSWORD = PASSWORD('otherpassword'); -- FOR ROOT
        REVOKE privileges ON db.table FROM 'user'@'host';
```

### Issues

#### Reset Root Password

```bash
sudo service mysql stop                                         
sudo mysqld_safe --skip-grant-table
mysql -uroot mysql
```

mysql>

```sql
SHOW DATABASES;
USE mysql;
SHOW TABLES;
UPDATE user SET Password=PASSWORD('password') WHERE User='root';         
FLUSH PRIVILEGES;
\q;
```

restart mysql

```bash
sudo service mysql start
```