---
title: Mysql基于pos主从复制
date: 2019-05-18 16:23:08
tags: [mysql]
categories: 数据库
---

​		mysql中的主从复制是通过二进制日志(binlog)进行的。主数据库启用binlog作为master，它的所有修改操作都会记录到日志中，其他的从数据库监控master的日志变化，发现有改变则IO线程把变化读取过来放到中继日志(relaylog),sql线程重放原过程，这样来实现主从的一致性。

#### 环境描述

​		使用的是两台centos 7虚拟机，一台安装好mysql然后虚拟机克隆过来的，mysql版本5.7

​		主库:192.168.128.136

​		从库:192.168.128.137

#### 主数据库配置

​		在mysql的配置文件/etc/my.cnf的[mysqld]下加入两行

```
[mysqld]
...
log-bin=mysql-bin #open binlog
server-id=1 #set server-id
```

​		进入mysql，创建从库用于访问的账号密码

```
mysql> create user 'slave1'@'192.168.128.137' identified by 'slave1pass';
Query OK, 0 rows affected (0.00 sec)
```

​		授权账号

```
mysql> grant replication slave on *.* to 'slave1'@'192.168.128.137';
Query OK, 0 rows affected (0.00 sec)
mysql> flush privileges;
Query OK, 0 rows affected (0.00 sec)
```

​		查看master状态，这里没有指定同步数据库默认全部同步，这里的File和Position后面会用到

```
mysql> show master status;
+------------------+----------+--------------+------------------+-------------------+
        | File             | Position | Binlog_Do_DB | Binlog_Ignore_DB |
        +------------------+----------+--------------+------------------+-----------+
        | mysql-bin.000004 |     1663 |              |                  |                   |
+------------------+----------+--------------+------------------+-------------------+
        1 row in set (0.00 sec)
```

#### 从数据库配置

​		同样需要配置server-id，打开/etc/my.cnf

```
[mysqld]
...
server-id=2 #set server-id
```

​		打开mysql，执行slave同步master语句，这里的最后两个参数就是 show master status显示出来的

```
 mysql> change master to
-> master_host='192.168.128.136',
-> master_user='slave1',
-> master_password='slave1pass',
-> master_log_file='mysql-bin.000004',
-> master_log_pos=1663;
```

​		然后查看slave状态信息发现有错误

```
mysql> show slave status\G;
*************************** 1. row ***************************
        Slave_IO_State:
        Master_Host: 192.168.128.136
        Master_User: slave1
        Master_Port: 3306
        Connect_Retry: 60
        Master_Log_File: mysql-bin.000004
        Read_Master_Log_Pos: 1663
        Relay_Log_File: localhost-relay-bin.000001
        Relay_Log_Pos: 4
        Relay_Master_Log_File: mysql-bin.000004
        Slave_IO_Running: No
        Slave_SQL_Running: Yes
        Replicate_Do_DB:
        Replicate_Ignore_DB:
        Replicate_Do_Table:
        Replicate_Ignore_Table:
        Replicate_Wild_Do_Table:
        Replicate_Wild_Ignore_Table:
        Last_Errno: 0
        Last_Error:
        Skip_Counter: 0
        Exec_Master_Log_Pos: 1663
        Relay_Log_Space: 154
        Until_Condition: None
        Until_Log_File:
        Until_Log_Pos: 0
        Master_SSL_Allowed: No
        Master_SSL_CA_File:
        Master_SSL_CA_Path:
        Master_SSL_Cert:
        Master_SSL_Cipher:
        Master_SSL_Key:
        Seconds_Behind_Master: NULL
        Master_SSL_Verify_Server_Cert: No
        Last_IO_Errno: 1593
        Last_IO_Error: Fatal error: The slave I/O thread stops because master and slave have equal MySQL server UUIDs; these UUIDs must be different for replication to work.
                Last_SQL_Errno: 0
        Last_SQL_Error:
        Replicate_Ignore_Server_Ids:
        Master_Server_Id: 1
        Master_UUID:
        Master_Info_File: /var/lib/mysql/master.info
        SQL_Delay: 0
        SQL_Remaining_Delay: NULL
        Slave_SQL_Running_State: Slave has read all relay log; waiting for more updates
        Master_Retry_Count: 86400
        Master_Bind:
        Last_IO_Error_Timestamp: 190516 16:43:46
        Last_SQL_Error_Timestamp:
        Master_SSL_Crl:
        Master_SSL_Crlpath:
        Retrieved_Gtid_Set:
        Executed_Gtid_Set:
        Auto_Position: 0
        Replicate_Rewrite_DB:
        Channel_Name:
        Master_TLS_Version:
        1 row in set (0.01 sec)
```

​		意思是服务器UUID重复，原因是虚拟机克隆，需要修改UUID。通过 show variables like 'datadir';找到mysql数据目录，打开目录下的auto.cnf文件，修改UUID，完成重启即可解决。

​		Slave_IO_Running和Slave_SQL_Running都为YES就表示同步设置成功，信息如下：

```
mysql> show slave status\G;
*************************** 1. row ***************************
        Slave_IO_State: Waiting for master to send event
        Master_Host: 192.168.128.136
        Master_User: slave1
        Master_Port: 3306
        Connect_Retry: 60
        Master_Log_File: mysql-bin.000004
        Read_Master_Log_Pos: 2756
        Relay_Log_File: localhost-relay-bin.000003
        Relay_Log_Pos: 320
        Relay_Master_Log_File: mysql-bin.000004
        Slave_IO_Running: Yes
        Slave_SQL_Running: Yes
        Replicate_Do_DB:
        Replicate_Ignore_DB:
        Replicate_Do_Table:
        Replicate_Ignore_Table:
        Replicate_Wild_Do_Table:
        Replicate_Wild_Ignore_Table:
        Last_Errno: 0
        Last_Error:
        Skip_Counter: 0
        Exec_Master_Log_Pos: 2756
        Relay_Log_Space: 531
        Until_Condition: None
        Until_Log_File:
        Until_Log_Pos: 0
        Master_SSL_Allowed: No
        Master_SSL_CA_File:
        Master_SSL_CA_Path:
        Master_SSL_Cert:
        Master_SSL_Cipher:
        Master_SSL_Key:
        Seconds_Behind_Master: 0
        Master_SSL_Verify_Server_Cert: No
        Last_IO_Errno: 0
        Last_IO_Error:
        Last_SQL_Errno: 0
        Last_SQL_Error:
        Replicate_Ignore_Server_Ids:
        Master_Server_Id: 1
        Master_UUID: cdd8cef1-7302-11e9-b89d-000c2982d315
        Master_Info_File: /var/lib/mysql/master.info
        SQL_Delay: 0
        SQL_Remaining_Delay: NULL
        Slave_SQL_Running_State: Slave has read all relay log; waiting for more updates
        Master_Retry_Count: 86400
        Master_Bind:
        Last_IO_Error_Timestamp:
        Last_SQL_Error_Timestamp:
        Master_SSL_Crl:
        Master_SSL_Crlpath:
        Retrieved_Gtid_Set:
        Executed_Gtid_Set:
        Auto_Position: 0
        Replicate_Rewrite_DB:
        Channel_Name:
        Master_TLS_Version:
        1 row in set (0.00 sec)
```

#### 主从测试

​		本人是在建库cloud建表bintest，然后新增几条数据之后再配置主从的，也就是pos的位置之后只有操作cloud.bintest但没有建表语句，这是slave会报错说表不存在，这也侧面印证了从库是通过语句来实现同步的。

还有我在pos所在的insert语句之后，主库执行创建另外一个库after的语句，这是从库也不会执行，因为前一步的错误让从库的同步中断，这时我在从库建立cloud库bintest表，再start slave，然后再查看数据库发现从库所有的都更新成最新的了，show slave status也变成了正常的状态。

