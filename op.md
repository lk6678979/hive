## 基本操作
### 1. 进入hive
```shell
[root@node1 ~]# hive
WARNING: Use "yarn jar" to launch YARN applications.
SLF4J: Class path contains multiple SLF4J bindings.
SLF4J: Found binding in [jar:file:/usr/lib/hive/lib/log4j-slf4j-impl-2.8.2.jar!/org/slf4j/impl/StaticLoggerBinder.class]
SLF4J: Found binding in [jar:file:/usr/lib/zookeeper/lib/slf4j-log4j12-1.7.25.jar!/org/slf4j/impl/StaticLoggerBinder.class]
SLF4J: See http://www.slf4j.org/codes.html#multiple_bindings for an explanation.
SLF4J: Actual binding is of type [org.apache.logging.slf4j.Log4jLoggerFactory]

Logging initialized using configuration in jar:file:/usr/lib/hive/lib/hive-common-2.1.1-cdh6.2.0.jar!/hive-log4j2.properties Async: false

WARNING: Hive CLI is deprecated and migration to Beeline is recommended.
hive> 
```
### 2. 创建数据库
```
hive> create database testhive;
OK
Time ta
```
* 创建数据库会在hdfs的hive目录(默认/user/hive/warehouse)下创建一个文件夹，名字为：`数据库名.db`，这里是testhive.db
### 3. 使用数据库
```
hive> use testhive;
OK
Time taken: 1.362 seconds
```
### 4. 查看当前使用数据库
```
hive> select current_database();
OK
testhive
Time taken: 0.808 seconds, Fetched: 1 row(s)
```
