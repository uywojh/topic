##mac升级mysql8.0碰到的坑

* 通过homebrew安装mysql

```
1、打开终端

2、执行安装命令“brew install mysql”

3、执行启动命令“brew services start mysql”, 重启命令“brew services restart mysql”,停止命令“brew services stop mysql”

4、执行设置密码命令“mysql_secure_installation”；随后按照提示说明设置即可

```

* 通过工具发现登录不上mysql

```
MySQL 连接出现 Authentication plugin 'caching_sha2_password'   
cannot be loaded

很多用户在使用Navicat Premium 12连接MySQL数据库时会出现  
Authentication plugin 'caching_sha2_password' cannot be   
loaded的错误。  


出现这个原因是mysql8 之前的版本中加密规则是mysql_native_password,  
而在mysql8之后,加密规则是caching_sha2_password, 解决问题方法有两  
种,一种是升级navicat驱动,一种是把mysql用户登录密码加密规则还原成  
mysql_native_password. 

```

* 解决方案

```
登录mysql用刚才设置的密码
mysql -u root -p 
修改账户密码加密规则并更新用户密码
ALTER USER 'root'@'localhost' IDENTIFIED BY 'password' 
PASSWORD EXPIRE NEVER;   #修改加密规则 

ALTER USER 'root'@'localhost' IDENTIFIED WITH 
mysql_native_password BY 'password';   #更新一下用户的密码 

FLUSH PRIVILEGES;   #刷新权限 

问题解决
```

