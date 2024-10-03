---
title: Linux服务器配置MySQL
date: 2024-09-24 19:12:22
tags: 
  - MySQL
categories: 
  - [数据库,MySQL]
---
### MySQL

#### Linux部署MySQL

##### **1. 下载安装包**

[MySQL官方](https://downloads.mysql.com/archives/community/)

##### ![20240828170632](20240828170632.png)

将下载好的压缩包，上传到Linux服务器：

- 如果是本地的虚拟机，可以直接复制到Linux服务器上。

- 如果是远程服务器，建议使用XShell+Xftp远程服务器访问工具。

![20240831230428](20240831230428.png)

上传到服务器后，进入对应目录，解压到指定目录（或任意目录）

```shell
tar -xvf mysql-server_8.0.28-1ubuntu18.04_amd64.deb-bundle.tar
```

![20240828171509](20240828171509.png)

进入解压后的目录，输入命令：

```shell
ls
```

查看解压后得到的文件：

![20240828171632](20240828171632.png)



##### **3. 安装**

由于mysql的安装包之前存在依赖性，需要依次按顺序安装以下deb文件：

1. mysql-common_8.0.28-1ubuntu18.04_amd64.deb
2. mysql-community-client-plugins_8.0.26-1ubuntu18.04_amd64.deb
3. libmysqlclient21_8.0.28-1ubuntu18.04_amd64.deb
4. libmysqlclient-dev_8.0.28-1ubuntu18.04_amd64.deb
5. mysql-community-client-core_8.0.28-1ubuntu18.04_amd64.deb
6. mysql-community-client_8.0.28-1ubuntu18.04_amd64.deb
7. mysql-client_8.0.28-1ubuntu18.04_amd64.deb
8. mysql-community-server-core_8.0.28-1ubuntu18.04_amd64.deb
9. mysql-community-server_8.0.28-1ubuntu18.04_amd64.deb
   此时会提示设置mysql的 root密码，请牢记，安装过程可能比较慢。
10. mysql-server_8.0.28-1ubuntu18.04_amd64.deb

依次执行如下命令：

```shell
sudo dpkg -i mysql-common_8.0.28-1ubuntu18.04_amd64.deb
sudo dpkg -i mysql-community-client-plugins_8.0.26-1ubuntu18.04_amd64.deb
sudo dpkg -i libmysqlclient21_8.0.28-1ubuntu18.04_amd64.deb
sudo dpkg -i libmysqlclient-dev_8.0.28-1ubuntu18.04_amd64.deb
sudo dpkg -i mysql-community-client-core_8.0.28-1ubuntu18.04_amd64.deb
sudo dpkg -i mysql-community-client_8.0.28-1ubuntu18.04_amd64.deb
sudo dpkg -i mysql-client_8.0.28-1ubuntu18.04_amd64.deb
sudo dpkg -i mysql-community-server-core_8.0.28-1ubuntu18.04_amd64.deb
sudo dpkg -i mysql-community-server_8.0.28-1ubuntu18.04_amd64.deb 
sudo dpkg -i mysql-server_8.0.28-1ubuntu18.04_amd64.deb
```

此时MySQL安装完成。

##### **4. 开启MySQL服务端口远程访问**

登录MySQL，使用在安装过程中设置的密码。并修改mysql库中user表的信息。

```mysql
mysql -u root -p;
use mysql; #进入mysql库
show tables; #查看mysql库中的表
select user,host from user; #查询user表中的user和host字段，loclahost表示只支持本地访问，%表示支持所有
update user set host="%" where user="root"; #更改host为%
exit; #退出mysql
```

![20240831231743](20240831231743.png)

修改 /etc/mysql/mysql.conf.d/mysqld.cnf配置（**注意：**ubuntu 16以下版本为/etc/mysql/my.cnf（本人未进行验证））

```shell
vim /etc/mysql/mysql.conf.d/mysqld.cnf
#键盘按i进行编辑
bind-address = 0.0.0.0 #插入或修改bind-address为0.0.0.0
#键盘按Esc切换编辑状态
:wq #退出并保存（需要root权限（sudo -i 可切换为root用户））
```

![20240828173504](20240828173504.png)

此时，可通过Linux服务器IP和端口号进行远程访问！

##### **5.  远程访问**

```shell
 ip addr #查看服务器IP地址
```

![20240831232455](20240831232455.png)

在本地或其他主机上（以Windows为例），**管理员身份**打开命令提示符。

```mysql
net start mysql
mysql -h 192.168.148.132 -u root -p
```

![20240831232923](20240831232923.png)

输入密码：

![20240831233052](20240831233052.png)

此时大功告成，远程访问成功！
