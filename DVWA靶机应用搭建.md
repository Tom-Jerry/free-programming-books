## DVWA配置安装过程
### 安装apache服务
1. 检查当前系统是否包含apache服务
	```shell
	httpd -v
	# Server version: Apache/2.4.6 (CentOS)
	# Server built:   Aug  8 2019 11:41:18
	```
	如果显示内容如图，则说明当前系统已经配置apache服务，无需重复配置
2. 搭建apache服务
	键入指令
	```shell
	yum install httpd
	```
	安装完成之后，会产生下面两个文件
	```shell
	/ect/httpd/conf/httpd.conf	#主配置文件
	/var/www/html			#默认网站的home目录
	```	
3. 启动apache服务
	安装apache有关软件包完成之后，键入指令
	```shell
	systemctl start httpd.service
	service httpd start
	```
	两条指令均可启动apache服务
4. 检查apache服务状态
	键入指令
	```shell
		systemctl status httpd.service
		httpd.service - The Apache HTTP Server
   		Loaded: loaded (/usr/lib/systemd/system/httpd.service; enabled; vendor preset: disabled)
   		Active: active (running) since 四 2019-11-21 16:39:05 CST; 22h ago
     		Docs: man:httpd(8)
      		     man:apachectl(8)
  		Process: 14907 ExecStop=/bin/kill -WINCH ${MAINPID} (code=exited, status=0/SUCCESS)
  		Process: 63956 ExecReload=/usr/sbin/httpd $OPTIONS -k graceful (code=exited, 	status=0/	SUCCESS)
		Main PID: 15008 (/usr/sbin/httpd)
   		Status: "Total requests: 73196; Current requests/sec: 0; Current traffic:   0 B/sec"
    		Tasks: 11
   		CGroup: /system.slice/httpd.service
      		     ├─ 15008 /usr/sbin/httpd -DFOREGROUND
      		     ├─ 15014 /usr/sbin/httpd -DFOREGROUND
      		     ├─ 15016 /usr/sbin/httpd -DFOREGROUND
      		     ├─ 15019 /usr/sbin/httpd -DFOREGROUND
      		     ├─ 15020 /usr/sbin/httpd -DFOREGROUND
      		     ├─ 15025 /usr/sbin/httpd -DFOREGROUND
      		     ├─ 15095 /usr/sbin/httpd -DFOREGROUND
      		     ├─ 17798 /usr/sbin/httpd -DFOREGROUND
      		     ├─ 17802 /usr/sbin/httpd -DFOREGROUND
      		     ├─ 17803 /usr/sbin/httpd -DFOREGROUND
      		     └─119387 /usr/sbin/httpd -DFOREGROUND
	```
	如上所示，apache服务运行状态正常，打开任意浏览器，输入127.0.0.1地址，查看是否显示默认apache主页
5. 当前使用的版本
	```shell
	Server version: Apache/2.4.6 (CentOS)
	```
	
### 安装mysql服务
1. 检查当前系统是否已经安装mysql服务
	```shell
		[hadoop@slave3 ~]$ mysql -v
		Welcome to the MariaDB monitor.  Commands end with ; or \g.
		Your MariaDB connection id is 167
		Server version: 10.2.27-MariaDB MariaDB Server
	
		Copyright (c) 2000, 2018, Oracle, MariaDB Corporation Ab and others.
	
		Reading history-file /home/hadoop/.mysql_history
		Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.
	
		MariaDB [(none)]> 

	```
	如上所示，当前系统已经安装了mysql服务
2. 安装mysql服务
	centos已经预装mysql的一种分支数据库MariaDB
3. 启动mysql服务
	```shell
		systemctl start mysqld.service	
	```
4. 验证安装是否成功
	```shell
		[hadoop@slave3 ~]$ systemctl status mysql.service 
		● mariadb.service - MariaDB 10.2.27 database server
  		Process: 108616 ExecStartPre=/bin/sh -c [ ! -e /usr/bin/galera_recovery ] && VAR= ||   VAR=`/usr/bin/galera_recovery`; [ $? -eq 0 ]   && systemctl set-environment 		_WSREP_START_POSITION=$VAR || exit 1 (code=exited, status=0/SUCCESS)
		Process: 108609 ExecStartPre=/bin/sh -c systemctl unset-environment _WSREP_START_POSITION (code=exited, status=0/SUCCESS)

	```
	如上所示，安装成功，并且已经正常运行mysql数据库服务
5. 当前使用的版本
	```shell
	Server version: 10.2.27-MariaDB MariaDB Server
	```
### 安装php环境
1. 检查当前系统是否已经安装php服务 
	```shell 
		[hadoop@slave3 ~]$ php -v
		PHP 7.4.0RC3 (cli) (built: Oct  1 2019 08:30:29) ( NTS )
		Copyright (c) The PHP Group
		Zend Engine v3.4.0-dev, Copyright (c) Zend Technologies

	```
	如上所示，当前系统已经包含php服务
2. 安装PHP
	```shell
		[root@localhost ~]# yum install php
		[root@localhost ~]# yum install php-mysql php-gd libjpeg* php-ldap php-odbc php-pear php-xml php-xmlrpc php-mbstring php-bcmath php-mhash
		systemctl restart mysqld.service 或service mysql restart
		systemctl restart httpd.service
	```
3. 验证php安装
	在浏览器中，输入127.0.0.1/phpinfo.php,查看是否能够正常显示php简介页面
4. 当前使用的版本号
### 安装DVWA应用
1. 下载DVWA安装包
	github地址：https://github.com/ethicalhack3r/DVWA
2. 解压安装DVWA
	```shell
		unzip DVWA-master.zip
		sudo mv DVWA-master /var/www/html/
		chgrp -R apache DVWA-master
		chown -R apache DVWA-master
	```
3. 配置文件修改
	```shell
		cd /var/www/html/DVWA-master
		chmod 755 setup.php

		#修改config.inc.php
		cd /var/www/html/DVWA-master/config/
		cp config.inc.php.dist config.inc.php
		vim config.inc.php
		$_DVWA[ 'db_database' ] = 'dvwa'
		$_DVWA[ 'db_database' ] = 'dvwa'
		$_DVWA[ 'db_database' ] = 'dvwa'
		$_DVWA[ 'recaptcha_public_key' ]  = '6LePqhAUAAAAAH6Bn2okO9-8G-zNw46PNhotV4Q6';
		$_DVWA[ 'recaptcha_private_key' ] = '6LePqhAUAAAAAMVqhBbFr1NVA9H13FKyfAgBeZYx';
		
		#修改/etc/php.ini文件
		allow_url_include = On

		#必要的权限修改
		cd /var/www/html/DVWA-master/hackable/
		chmod 777 uploads
		cd /var/www/html/DVWA-master/external/phpids/0.6/lib/IDS/tmp
		chmod 777 phpids_log.txt
	```
4. 查看安装是否成功
	任意浏览器访问127.0.0.1/DVWA-master/setup.php，查看是否出现DVWA首页

