系统性学习可以看下这个博客：https://www.cnblogs.com/qingyunzong/p/8707885.html  下面的笔记是自己测试的总结
## 笔记
## hive on spark 在CDH的hive配置中搜索spark 配置完重启
### 1.创建数据库
* 创建数据库会在hdfs的hive目录(默认/user/hive/warehouse)下创建一个文件夹，名字为：`数据库名.db`，这里是testhive.db
### 2. 建内部表
* 该操作会在hdfs中数据库目录下创建一个同名的文件夹
### 3. 使用load往内部表中加载数据
* 需要提前准备数据文件
* load方法会将源文件复制一份到表所在的hdfs目录中，不会直接使用源文件
* load加载数据文件，会在表目录下创建和被load的文件名相同的一个文件（如果文件名冲突，会生成_copy_*的文件）
* 提交的数据，每行数字可以字段不足，不足的字段在表中int会转为null，字符串会转为空字符串，不会异常
* 如果字段无法转换成表结构中定义的数据格式，会加载为null
* 使用overwrite后，会删除hdfs表目录下所有的文件，并写入新的文件
### 4. 使用insert往内部表中加载数据
* insert方法会将源文件复制一份到表所在的hdfs目录中，不会直接使用源文件
* 会在表目录中新建一个文件存储insert的数据(不管是values还是select子查询到的数据），文件名从0开始递增，例如000000_0
### 5. 创建分区表
* 创建分区表的时候不会创建额外的目录
* 创建分区表后，在加载数据之前，需要手动去创建分区，创建分区的操作会在表的hdfs目录下创建对应的文件件，名称：分区字段=值，例如：city=beijing
### 6. 使用load往内部分区表中加载数据
* 和load到普通表一样，只不过文件操作是在表的分区目录下
* load的分区字段不能存在元数据的字段中，否则会异常
### 7. 使用insert往内部分区表中加载数据
* 和insert到普通表一样，只不过文件操作是在表的分区目录下
### 8. 中文问题
* 写入中文和读取中文需要设置字符串编码，hive默认读取数据的编码是UTF-8
* 需要SerDe支持指定编码格式，一般建议使用GBK，否则Hdfs中的数据下载下来全是乱码
### 9. 创建好表或者分区后，直接通过hdfs往文件夹中写文件，hive也是可以查询到的
### 10. Hive可以将查询的结果数据写入文件系统中
### 11. Hive如果想支持delete和update操作，需要去hive手动配置支持事务
* 普通hive配置方式：https://blog.csdn.net/levy_cui/article/details/66970903
* CDH安装的hive配置方式：http://note.youdao.com/noteshare?id=2abb1f4e913c9886f067036b80b3e101&sub=C8AA582BBDD5479292E888A7E3B8DD40
* 如果一个表要实现update和delete功能，该表就必须支持ACID，而支持ACID，就必须满足以下条件：
* 只能使用内部表（外部表不支持transactional）
* 表的存储格式必须是ORC（STORED AS ORC）；
* 表必须进行分桶（CLUSTERED BY (col_name, col_name, …) INTO num_buckets BUCKETS）；
* Table property中参数transactional必须设定为True（tblproperties(‘transactional’=‘true’)）；
* 说明：ORC是RCfile的升级版，性能有大幅度提升，而且数据可以压缩存储，压缩比和Lzo压缩差不多，比text文件压缩比可以达到70%的空间。而且读性能非常高，可以实现高效查询。具体介绍https://cwiki.apache.org/confluence/display/Hive/LanguageManual+ORC
### 12. 内部表，设置可以使用事务、分桶、分区、使用ORC
* 由于使用了ORC，HDFS文件无法直接读取查看
* delete和update会在分桶文件中修改，不会生成新的文件
### 12. 内部表或者外部表，分桶、分区、不使用ORC
* 无法使用delete和update，insert会生成新的文件，文件名为【桶文件名_copy_次数】，例如：000000_0_copy_1
# 结论：要想在hive表中追加少量数据，只能去分区中的文件里直接追加数据，不能使用hive接口去追加，否则会产生大量小文件
