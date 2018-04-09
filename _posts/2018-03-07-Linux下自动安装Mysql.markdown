---
layout:     post
title:      "Linux一键安装Mysql"
subtitle:   "编写Shell实现Mysql的自动安装"
date:       2017-06-11
author:     "Chenyan"
header-img: "img/post-bg-os-metro.jpg"
catalog:    false
tags:
    - Shell
    - Linux
---

#### Shell脚本

```
#!/bin/sh
# auto install mysql for 5.7.15

password="111111"
basedir=/usr/local
datadir=/usr/local/mysql/data/
sqldir=${HOME}/sqlGenerated

tarfile=$1
if [ -z ${tarfile} ]
then
    echo "mysql file name is empty... "
    echo "example => /bin/sh crontab_mysql_install.sh mysql-5.7.15-linux-glibc2.5-x86_64.tar.gz"
    exit 1
fi

# 1.检查库文件是否存在，如有删除
echo "1）check if mysql lib file exists"
rpm=`rpm -qa | grep mysql`
if [ -n ${rpm} ]
then
    rpm -e ${rpm} --nodeps
    echo "delete mysql lib file"
fi

# 2.检查mysql组和用户是否存在，如无创建
echo "2）check whether user and group exists"
group=`cat /etc/group | grep mysql`
if [ -z ${group} ]
then
    groupadd mysql
    echo "create mysql group "
else
    echo "group is exists"
fi
user=`cat /etc/passwd | grep mysql`
if [ -z ${user} ]
then
    useradd -r -g mysql mysql
    echo ${password} | passwd mysql --stdin
    echo "create mysql user "
else
    echo "user is exists"
fi

# 3.解压TAR包，更改所属的组和用户
echo "3) decompressing files and chmod"
echo "decompressing ... "
tar xzf ${tarfile} -C ${basedir}
cd ${basedir}
mv ${tarfile/.tar.gz/} mysql

chown -R mysql mysql/
chgrp -R mysql mysql/
cd mysql/bin/

# 4.安装和初始化数据库
echo "4) install and init mysql"
basedir=${basedir}/mysql
./mysql_install_db --user=mysql --basedir=${basedir} --datadir=${datadir}

cp -a ../support-files/my-default.cnf /etc/my.cnf
cp -a ../support-files/mysql.server  /etc/init.d/mysqld

./mysqld_safe --user=mysql &
# 修改大小写敏感
echo "lower_case_table_names=1"  >> /etc/my.cnf
# 重启服务
/etc/init.d/mysqld restart

# 设置开机自启
chkconfig --level 35 mysqld on

# 5.初始化密码
echo "5) init password"

default_pwd=`awk 'END {print}' /root/.mysql_secret`
./mysql -uroot -p${default_pwd} -e"SET PASSWORD = PASSWORD('${password}');flush privileges;" --connect-expired-password

# 6.添加远程访问权限
./mysql -uroot -p${password} -e "use mysql; update user set host = '%' where user = 'root';"

# 7.初始化数据
echo "create database siminfo"
./mysql -uroot -p${password} -e "CREATE DATABASE IF NOT EXISTS siminfo DEFAULT CHARSET utf8 COLLATE utf8_general_ci;"
echo "create database sync"
./mysql -uroot -p${password} -e "CREATE DATABASE IF NOT EXISTS sync DEFAULT CHARSET utf8 COLLATE utf8_general_ci;"
echo "create database dbclear"
./mysql -uroot -p${password} -e "CREATE DATABASE IF NOT EXISTS dbclear DEFAULT CHARSET utf8 COLLATE utf8_general_ci;"
echo "create database snap"
./mysql -uroot -p${password} -e "CREATE DATABASE IF NOT EXISTS snap DEFAULT CHARSET utf8 COLLATE utf8_general_ci;"

./mysql -uroot -p${password} -e "source ${sqldir}/siminfo_SimInfo_create.sql"
./mysql -uroot -p${password} -e "source ${sqldir}/sync_Sync_create.sql"
./mysql -uroot -p${password} -e "source ${sqldir}/dbclear_DBClear_create.sql"
./mysql -uroot -p${password} -e "source ${sqldir}/snap_Snap_create.sql"

# 8.重启数据库
/etc/init.d/mysqld restart
echo "finished........."

```
