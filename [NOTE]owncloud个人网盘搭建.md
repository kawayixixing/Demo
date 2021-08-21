# owncloud个人网盘搭建



## 1、安装Apache服务

### 1.1更新软件库、更新软件

```
apt-get update | apt-get upgrade -y
```



### 1.2安装Apache服务

```
apt-get install apache2 -y
```



### 1.3重启Apache服务

```
/etc/init.d/apache2 restart
```



## 2、安装MySQL服务

### 2.1下载APT存储库

```
wget https://dev.mysql.com/get/mysql-apt-config_0.8.10-1_all.deb
```



### 2.2安装DEB包

```
dpkg -i mysql-apt-config_0.8.10-1_all.deb
```



### 2.3更新软件库，安装MySQL

```
apt-get update     
apt-get install mysql-server -y
```

## 3、安装PHP及PHP扩展

### 3.1安装PHP

```
apt-get install php7.0 -y
```



### 3.2安装PHP扩展模块

```
apt install php-bz2 php-curl php-gd php-imagick php-intl php-mbstring php-xml php-zip -y
```



### 3.3安装phpmyadmin

```
apt-get install phpmyadmin -y
（使用空格选择apache2,然后回车）接着按2次enter键
```

### 3.4查看数据库用户名和密码

```
cat /etc/mysql/debian.cnf
```

## 4、创建OwnCloud数据库

### 4.1进入数据库

```
sudo mysql -u root -p
(MySQL数据库默认没有密码，在Enterpassword处回车即可)
```

### 4.2使用数据库

```
use mysql;
```

### 4.3创建数据库，其中owncloud为数据库名

```
CREATE DATABASE owncloud;
```

### 4.4在owncloud数据库上创建用户

```
CREATE USER 'owncloud'@'localhost' IDENTIFIED BY 'owncloud';
```

### 4.5为用户授权

```
grant all privileges on owncloud.* to 'owncloud'@'localhost' with grant option;
```

### 4.6刷新数据库

```
FLUSH PRIVILEGES;
```

### 4.7退出数据库

```
exit
```

## 5、搭建Owncloud环境

### 5.1下载owncloud服务器包

```
wget https://download.owncloud.org/community/owncloud-10.0.9.zip
（建议另外下载，然后上传过来，太慢了）
```

### 5.2解压owncloud服务器包

```
unzip owncloud-10.0.9.zip
```

### 5.3将owncloud包移动到/var/www/html目录下

```
mv owncloud /var/www/html
```

### 5.4赋予owncloud文件夹权限

```
chown -R www-data:www-data /var/www/html/owncloud/
chmod -R 777 /var/www/html/owncloud/
```

### 5.5重启Apache服务

```
/etc/init.d/apache2 restart
```

### 5.6打开浏览器

```
访问http：//IP/owncloud，依次输入管理员用户名、管理员密码、数据库用户名、数据库密码、数据库名、localhost，单击安装完成。
```

> 参数说明：
>
> 管理员用户名：设置一个用户名。
>  管理员密码：设置一个用户密码。
>  数据库用户名：输入owncloud。
>  数据库密码：输入owncloud。
>  localhost：输入localhost:5432。

## 6、将外挂硬盘作为存储对象

### 6.1设置开机自动挂载硬盘

```
sudo vim /etc/fstab
```

> 文档末尾处插入：[UUID] [挂载目录] [硬盘格式] [options] [dump] [pass]
> 例如：UUID=40cf7135-534e-446d-939d-9b9fef18411 /mnt/data ext4 defaults 0 0
> 然后reboot，用于自动挂载

### 6.2移植数据

进入挂载好的硬盘，创建owncloud目录

```
sudo mkdir /mnt/data/owncloud
```

将原有的数据文件复制到该目录下

```
sudo cp -R /var/www/html/owncloud/data /mnt/data/9、owncloud
```

### 6.3 Owncloud配置

进入owncloud的config文件进行修改配置

```
sudo vim /var/www/html/owncloud/config/config.php
```

将datadirectory改为挂载的硬盘下创建的目录并保存

```
'datadirectory' => '/mnt/data/9、owncloud/data',
```

### 6.4更改SELinux模式

```
setenforce 0  #这相当于设置SELinux 成为permissive模式，不需要重启
```



## 7、常见问题

## 7.1文件权限问题（解决0770问题）

```
已经给予的权限：（归属用户、所拥权限）
chown -R www-data:www-data /var/www/html/owncloud/
chmod -R 0777 /var/www/html/owncloud/

给予外部存储硬盘存储数据文件权限：
sudo chown -R www-data:www-data /mnt/data/9、owncloud/
sudo chmod -R 0770 /mnt/data/9、owncloud/
```

## 7.2NTFS格式无法更改权限问题

```
UUID=C0F8ACE9F8ACDEC2     /mnt/data    ntfs-3g  permissions  0 0
加入permissions参数即可

以可读写方式挂载ntfe分区
mount -t ntfs-3g /dev/sdb1 /mnt/ntfs
```

## 7.2常用命令

```
sudo vim /var/www/html/owncloud/config/config.php
  'datadirectory' => '/mnt/data/9、owncloud/data',

```



------

> 8、参考资料
> 8.1：https://developer.aliyun.com/article/784286
> 8.2：https://blog.csdn.net/qq_43445362/article/details/107228935
>
> 8.3:https://www.cnblogs.com/bhlsheji/p/5327718.html

