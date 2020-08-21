---
title: linux上mysql5.7版本用户登录问题
date: 2019-05-14 15:39:15
tags: [数据库,mysql]
categories: 数据库
---

#### 问题

​		linux上安装mysql5.7之后，使用mysql -u root -p尝试进入mysql报错**ERROR 1045 (28000): Access denied for user 'root'@'localhost' (using password: YES)**，从字面意思上看是验证的过程被拒绝，百度并尝试一下方法可解决。

#### 解决

1. 打开/etc/my.cnf配置文件，文件末尾添加`skit-grant-tables`跳过权限验证，这时可以不用密码登录，网上还有另外一种办法是说找到用户目录中的.mysql_secret里面的随机密码登录，这个我当时没找到

2. 使用**mysql -u root**进入mysql，**use mysql;**进入database名为mysql的库,然后修改root用户的密码

 `update user set authentication_string=password("你的新密码") where user="root";`

3. 然后使用新的密码登录mysql，我这里是使用了**show datatables;**报错如下，意思是必须重置密码

```
mysql> show databases;
ERROR 1820 (HY000): You must reset your password using ALTER USER statement before executing this statement.
```

4. 然后再使用2.中的语句更新密码，这时又出现了一个错误，意思是密码不合要求

```
mysql> alter user 'root'@'localhost' identified by 'admin123';
ERROR 1819 (HY000): Your password does not satisfy the current policy requirements
```

5. 如果想密码设简单一点可以使用如下语句修改全局参数放宽限制

```
mysql> set global validate_password_policy=0;
Query OK, 0 rows affected (0.00 sec)

mysql> set global validate_password_length=2;
Query OK, 0 rows affected (0.00 sec)

mysql> alter user 'root'@'localhost' identified by 'root';
Query OK, 0 rows affected (0.00 sec)
```

借鉴: https://www.cnblogs.com/gumuzi/p/5711495.html

​         https://blog.csdn.net/memory6364/article/details/82426052