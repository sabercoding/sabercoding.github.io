---
layout: post
title: "wget 搭建lnmp环境"
date: 2015-10-28 02:46:00.000000000 +09:00
tags: ""
---
### 安装必要的软件 
```
yum install -y update 
yum install pcre 
yum install openssl* 
yum -y install gcc gcc-c++ autoconf libjpeg libjpeg-devel libpng libpng-devel freetype freetype-devel libxml2 libxml2-devel zlib zlib-devel glibc glibc-devel glib2 glib2-devel bzip2 bzip2-devel ncurses ncurses-devel curl curl-devel e2fsprogs e2fsprogs-devel krb5 krb5-devel libidn libidn-devel openssl openssl-devel openldap openldap-devel nss_ldap openldap-clients openldap-servers make 
yum -y install gd gd2 gd-devel gd2-devel cmake bison ncurses 
```
```
mdkir /data/software 
cd /data/software
```
### 安装pcre 
```
wget http://jaist.dl.sourceforge.net/project/pcre/pcre/8.33/pcre-8.33.tar.gz 
tar zxvf pcre-8.33.tar.gz 
cd pcre-8.33 
./configure -prefix=/data/webserver/pcre 
make && make install 
cd ../ 
```

### 安装nginx 
```
/usr/sbin/groupadd www 
/usr/sbin/useradd www -g www -M -s /sbin/nologin  
wget  http://nginx.org/download/nginx-1.4.2.tar.gz 
tar zxvf nginx-1.4.2.tar.gz 
cd nginx-1.4.2 
./configure --user=www --group=www --prefix=/data/webserver/nginx --with-http_stub_status_module --with-http_ssl_module --with-pcre=/data/software/pcre-8.33 --with-http_realip_module --with-http_image_filter_module 
make 
make install 
cd ../ 
```
### 安装 MySQL： 
#### 创建账号 
```
/usr/sbin/groupadd mysql 
/usr/sbin/useradd mysql -g mysql -M -s /sbin/nologin 
wget http://cdn.mysql.com/Downloads/MySQL-5.6/mysql-5.6.13.tar.gz 
tar zxvf mysql-5.6.13.tar.gz 
cd mysql-5.6.13 
cmake \\ 
-DCMAKE_INSTALL_PREFIX=/data/webserver/mysql \\ 
-DMYSQL_DATADIR=/data/webserver/mysql/data \\ 
-DSYSCONFDIR=/data/webserver/mysql \\  #my.cnf路径 
-DWITH_MYISAM_STORAGE_ENGINE=1 \\ 
-DWITH_INNOBASE_STORAGE_ENGINE=1 \\ 
-DWITH_MEMORY_STORAGE_ENGINE=1 \\ 
-DWITH_READLINE=1 \\ #快捷键功能 
-DMYSQL_UNIX_ADDR=/tmp/mysqld.sock \\ 
-DMYSQL_TCP_PORT=3306 \\ 
-DENABLED_LOCAL_INFILE=1 \\ #允许从本地导入数据 
-DWITH_PARTITION_STORAGE_ENGINE=1 \\ #安装支持数据库分区 
-DEXTRA_CHARSETS=all \\ 
-DDEFAULT_CHARSET=utf8 \\ 
-DDEFAULT_COLLATION=utf8_general_ci 

make 
make install 
cd /data/webserver/mysql 
chown -R mysql:mysql . #为了安全安装完成后请修改权限给mysql用户 
scripts/mysql_install_db --user=mysql #先进行这一步再做如下权限的修改 
chown -R root:mysql .  #将权限设置给root用户，并设置给mysql组， 取消其他用户的读写执行权限，仅留给mysql "rx"读执行权限，其他用户无任何权限 
chown -R mysql:mysql ./data   #给数据库存放目录设置成mysql用户mysql组，并赋予chmod -R ug+rwx  读写执行权限，其他用户权限一律删除仅给mysql用户权限 
cp support-files/my-default.cnf  /data/webserver/mysql/my.cnf 
```

#### 将mysql的启动服务添加到系统服务中 
```
cp support-files/mysql.server /etc/init.d/mysql   
```
####现在可以使用下面的命令 
```
service mysql start   #启动mysql  
service mysql stop   #停止mysql服务   
service mysql restart   #重启mysql服务   
chkconfig --add mysql #开机启动  
cd /data/webserver/mysql 
./bin/mysqladmin -u root password  #修改默认root账户密码，默认密码为空
```


### 安装PHP依赖库: 
```
mkdir -p /data/webserver/libs/ 
wget http://www.ijg.org/files/jpegsrc.v9.tar.gz      
tar zxvf jpegsrc.v9.tar.gz 
cd jpeg-9/ 
./configure --prefix=/data/webserver/libs/ --enable-shared --enable-static --prefix=/data/webserver/libs 
make 
make install 
cd ../ 

wget http://prdownloads.sourceforge.net/libpng/libpng-1.6.2.tar.gz 
tar zxvf libpng-1.6.2.tar.gz 
cd libpng-1.6.2/ 
./configure --prefix=/data/webserver/libs 
make 
make install 
cd ../ 

wget http://download.savannah.gnu.org/releases/freetype/freetype-2.4.12.tar.gz 
tar zxvf freetype-2.4.12.tar.gz 
cd freetype-2.4.12/ 
./configure --prefix=/data/webserver/libs 
make && make install 
cd ../ 


wget "http://downloads.sourceforge.net/mhash/mhash-0.9.9.9.tar.gz?big_mirror=0" 
wget "http://downloads.sourceforge.net/mcrypt/libmcrypt-2.5.8.tar.gz?big_mirror=0" 
wget "http://downloads.sourceforge.net/mcrypt/mcrypt-2.6.8.tar.gz?big_mirror=0" 


tar zxvf libmcrypt-2.5.8.tar.gz 
cd libmcrypt-2.5.8/ 
./configure --prefix=/data/webserver/libs 
make 
make install 
cd libltdl/ 
./configure --prefix=/data/webserver/libs --enable-ltdl-install 
make && make install 
cd ../../ 




tar zxvf mhash-0.9.9.9.tar.gz 
cd mhash-0.9.9.9/ 
./configure --prefix=/data/webserver/libs 
make && make install 
cd ../ 


vi /etc/ld.so.conf 
/data/webserver/libs/lib 
ldconfig 


tar zxvf mcrypt-2.6.8.tar.gz 
cd mcrypt-2.6.8/ 
export LDFLAGS="-L/data/webserver/libs/lib -L/usr/lib" 
export CFLAGS="-I/data/webserver/libs/include -I/usr/include" 
touch malloc.h 
./configure --prefix=/data/webserver/libs --with-libmcrypt-prefix=/data/webserver/libs 
make 
make install 
cd ../ 
```


### 安装 php5.5: 
```
wget http://us2.php.net/distributions/php-5.5.3.tar.gz 
tar zxvf php-5.5.3.tar.gz  
cd php-5.5.3 
#export LIBS="-lm -ltermcap -lresolv" 
#export CFLAGS=;export LIBS= 
export DYLD_LIBRARY_PATH="/data/webserver/mysql/lib/:/lib/:/usr/lib/:/usr/local/lib:/lib64/:/usr/lib64/:/usr/local/lib64" 
export LD_LIBRARY_PATH="/data/webserver/mysql/lib/:/lib/:/usr/lib/:/usr/local/lib:/lib64/:/usr/lib64/:/usr/local/lib64"
./configure --prefix=/data/webserver/php --with-config-file-path=/data/webserver/php/etc --with-mysql=/data/webserver/mysql --with-mysqli=/data/webserver/mysql/bin/mysql_config --with-iconv-dir --with-freetype-dir=/data/webserver/libs --with-jpeg-dir=/data/webserver/libs --with-png-dir=/data/webserver/libs --with-zlib --with-libxml-dir=/usr --enable-xml --disable-rpath --enable-bcmath --enable-shmop --enable-sysvsem --enable-inline-optimization --with-curl --enable-mbregex --enable-fpm --enable-mbstring --with-mcrypt=/data/webserver/libs --with-gd --enable-gd-native-ttf --with-openssl --with-mhash --enable-pcntl --enable-sockets --with-xmlrpc --enable-zip --enable-soap --enable-opcache --with-pdo-mysql 
make 
make install 
cp php.ini-development /data/webserver/php/etc/php.ini 
cd ../ 
```

#### 编译安装PHP扩展 
```
wget http://ftp.gnu.org/gnu/autoconf/autoconf-latest.tar.gz 
tar zxvf autoconf-latest.tar.gz 
cd autoconf-2.69/ 
./configure --prefix=/data/webserver/libs 
make 
make install 
cd ../ 


wget http://pecl.php.net/get/memcache-2.2.7.tgz 
tar zxvf memcache-2.2.7.tgz 
cd memcache-2.2.7/ 
export PHP_AUTOCONF="/data/webserver/libs/bin/autoconf" 
export PHP_AUTOHEADER="/data/webserver/libs/bin/autoheader" 
/data/webserver/php/bin/phpize 
./configure --with-php-config=/data/webserver/php/bin/php-config 
make 
make install 
cd ../ 


vi /data/webserver/php/etc/php.ini 
#查找 ; extension_dir = "ext" 
#在其后增加一行： 
extension = "memcache.so"
```

##### 扩展相关
1. pcre, libpng,libjpeg,freetype等扩展库是centos, rhel自带的，你不需要再重新使用源码编译。使用系统自带的库，一方面安装方便，另一方面兼容性也较好，如果使用你的方法可能导致扩展库冲突，导致安装的php不稳定，安装方法是 yum install pcre* libjpeg* libpng* freetype*

2. PHP 5.3.0及以上版本已经内置mysqlnd驱动, 所以安装php时并不需要预先安装mysql, 你可以在安装php之后再安装mysql(这与之前版本的php安装顺序差异较大). mysqlnd是php官方专为php开发的驱动程序, 其在内存管理和性能上都比传统的libmysql驱动要好,故推荐使用mysqlnd, 不使用传统的libmysql. 
编译参数中使用 --with-mysql=mysqlnd --with-mysqli=mysqlnd --with-pdo-mysql=mysqlnd即可使用mysqllnd