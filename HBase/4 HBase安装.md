# HBase独立模式安装



**linux安装**

- 官方：http://hbase.apache.org/book.html#quickstart

**环境**

- jdk1.8
- **hbase-2.4.7-bin.tar.gz**
- *centos7.7**

**安装步骤**

1. 目录环境

   1. /opt/software是存放原安装软件目录
   2. /opt/module/是解压后可运行软件目录

2. jdk安装

   ```
   #解压
   tar -xvf /opt/software/jdk.tar.gz -C /opt/module/
   
   #配置
   cd /etc/profile.d
   vim my_env.sh
   ---------------------内容--------------
   export JAVA_HOME=/opt/module/jdk1.8.0_281
   export PATH=$PATH:$JAVA_HOME/bin
   ----------------------------------
   
   #使java配置生效
   source /etc/profile
   
   #测试
   java -verison
   
   ```

   

3. hbase安装

   ```
   #解压
   tar -xvf /opt/software/hbase.tar.gz -C /opt/module/
   
   #配置
   vim /opt/module/hbase/conf/hbase-env.sh
   ---------------------内容--------------
   export JAVA_HOME=/opt/module/jdk1.8.0_281
   ----------------------------------
   ```

   



**测试**

1. 启动

   ```
   bin/start-hbase.sh
   
   #使用jps查看是否启动成功.
   jps
   --hmaster
   有hmaster进程说明启动成功.同时也可以通过http://localhost:16010管理访问
   ```

   

2. 操作

   ```
   #连接到hbase
   bin/hbase shell
   hbase(main):001:0>
   
   #创建表
   create 'test', 'cf'
   
   #验证表是否存在
   list 'test'
   
   #查看表细节
   describe 'test'
   
   #插入几条数据
   put 'test', 'row1', 'cf:a', 'value1'
   put 'test', 'row2', 'cf:b', 'value2'
   put 'test', 'row3', 'cf:c', 'value3'
   
   #查看表全部数据记录
   scan 'test'
   
   #获取单条记录
   get 'test', 'row1'
   
   #使表不可用
   disable 'test'
   
   #恢复表可用
   enable 'test'
   
   #删除表
   drop 'test'
   ```

   

3. 停止

   ```
   bin/stop-hbase.sh
   ```

   

