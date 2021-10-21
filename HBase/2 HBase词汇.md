# HBase词汇

**RowKey**

> 与nosql数据库一样，RowKey是用来检索记录的主键。

访问HBasetable中的行。有三种方式

1. 通过单个RowKey访问
2. 通过RowKey的range正则访问
3. 全表扫描

RowKey介绍

> rowkey可以是任意字符串，最大长度64KB，实际应用中一般为10-100bytes，70-100bytes用的较多。rowkey被经字节数组形式保存在HBase中，数据按照rowkey的字典序（byte order）排序存储。设计rowkey时，要考虑排序存储这个我，将经常一起读取的行存储到一起（位置相关性）。



**列族-Column Family**

> 同属性列的集合，创建表时必须定义列族，列名也以列族作为前缀。



**Cell**

> cell是数据存储单元，数据没有类型，以字节码形式存储。由{rowkey，column family:column,version}确定唯一单元数据。其中version是由timestamp实现。

**Time Stamp**

> 每个cell存在多个版本数据，版本由时间戳实现，时间戳类型是64位整型。时间戳可以由HBase自动赋值，默认精度是毫秒。也可以由客户显示赋值，如果应用程序要避免数据版本冲突，就必须自己生成具有唯一性的时间戳。每个cell中，不同版本的数据按照时间倒序排序，即最新的数据排在最前面。
>
> 为了避免数据存在过多版本造成的管理负担，HBase提供两种版本数据回收方式，一是保存数据的最后N个版本，二是保存最近一段时间内的版本（比如最近七天）。用户可以针对每个列族进行设置。



**命名突空间**

> namespace

1. Table：所有的表都是命名空间成员，表必须属于某个命名空间，默认是default命名空间。
2. RegionServer Group：一个命名空间默认包括RegionServer Group
3. Permission:命名空间可来定义访问控制列表-ACL（Aceess Control List）例如：创建表，读取表，删除，更新等操作。
4. Quota：强制一个命名空间可包含的region的数量

