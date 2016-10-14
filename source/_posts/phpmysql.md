---
title: php操作mysql基础
tags:
  - php
  - mysql
categories:
  - php
  - mysql
date: 2016-08-15 15:17:33
---
<Excerpt in index | 首页摘要> 
``PHP``连接``MySQL``数据库用到的三种API
1、PHP的MySQL扩展
2、PHP的mysqli扩展
3、PHP数据对象(PDO)

## MySql 扩展 ##
> 设计开发允许PHP应用与MySQL数据库交互的早期扩展。mysql扩展提供了一个面向过程  的接口，并且是针对MySQL4.1.3或更早版本设计的。因此，这个扩展虽然可以与MySQL4.1.3或更新的数据库服务端  进行交互，但并不支持后期MySQL服务端提供的一些特性。

## mysqli扩展 ##
> mysqli扩展，我们有时称之为MySQL增强扩展，可以用于使用   MySQL4.1.3或更新版本中新的高级特性。mysqli扩展在PHP 5及以后版本中包含。

<!-- more -->
<The rest of contents | 余下全文>

## php数据对象（PDO）##
> PHP数据对象，是PHP应用中的一个数据库抽象层规范。PDO提供了一个统一的API接口可以使得你的PHP应用不去关心具体要  连接的数据库服务器系统类型。也就是说，如果你使用PDO的API，可以在任何需要的时候无缝切换数据库服务器，比如从Firebird   到MySQL，仅仅需要修改很少的PHP代码。
     当然，PDO也有它自己的先进性，比如一个干净的，简单的，可移植的API，它最主要的缺点是会限制让你不能使用  后期MySQL服务端提供所有的数据库高级特性。比如，PDO不允许使用MySQL支持的多语句执行。
          PDO的MySQL驱动并不是一套API，至少从PHP程序员的角度来看是这样的。实际上，PDO的MySQL驱动处于PDO自己的下层，  提供了特定的Mysql功能。程序员直接调用PDO的API，而PDO使用了PDO的MySQL驱动完成与MySQL服务器端的交互。  
               PDO的MySQL驱动是众多PDO驱动中的一个。其他可用的PDO驱动包括Firebird，PostgreSQL等等。


## php原生mysql操作 ##

```php

<?php
//链接数据
$con = mysql_connect("localhost","root","root");

if (!$con){
    die('Could not connect:'.mysql_error());
} else {
    echo 'Connecting...'."\n";
}

//创建数据 my_db
echo 'Now create database my_db...';
$sql = "CREATE DATABASE my_db";
if (mysql_query($sql,$con)){
    echo 'Database created...'."\n";
    } else {
            echo 'Error creating database:'.mysql_error()."\n";
}

//创建Persons表
mysql_select_db("my_db",$con);
$sql = "
    CREATE TABLE Persons
    (
        personID int NOT NULL AUTO_INCREMENT, 
        PRIMARY KEY(personID),
        FirstName varchar(15),
        LastName varchar(15),
        Age int
    )
";
if (mysql_query($sql,$con)){
    echo 'Table creating...'."\n";
    } else{
        echo 'Error creating table:'.mysql_error()."\n";
}
                  
//插入数据
mysql_select_db('my_db',$con);
$sql = "INSERT INTO Persons(FirstName, LastName, Age) VALUES('Glenn', 'Quagmire', '33')";
if (mysql_query($sql, $con)){
    echo 'Info inserted...'."\n";
    } else {
        echo 'Error insert:'.mysql_error()."\n";
}

//查询数据
echo 'select data...'."\n";
mysql_select_db('my_db',$con);
//$res = mysql_query("select * from Persons where FirstName='Peter'");
$res = mysql_query("select * from Persons order by age");
while ($row = mysql_fetch_array($res)){
    echo $row['FirstName'] . " " . $row['LastName']." ".$row['Age'];;
    echo "\n";
}

//更新数据
mysql_select_db('my_db',$con);
mysql_query("update Persons set age='36' where FirstName='Peter' and LastName='Griffin'");
$res = mysql_query("select * from Persons order by age");
while ($row = mysql_fetch_array($res)){
    echo $row['FirstName'] . " " . $row['LastName']." ".$row['Age'];;
    echo "\n";
}

//删除数据
mysql_select_db('my_db',$con);
mysql_query("delete from Persons where FirstName='Peter' and LastName='Griffin'");
$res = mysql_query("select * from Persons");
while ($row = mysql_fetch_array($res)){
    echo $row['FirstName'] . " " . $row['LastName']." ".$row['Age'];;
    echo "\n";
}

mysql_close($con);
?>
```
