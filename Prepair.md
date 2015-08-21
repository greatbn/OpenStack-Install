Cài đặt OpenStack sử dụng mô hình 3 node: Controller - Network - Compute

#1.Mô Hình

<img src="http://i.imgur.com/G8mSnL9.png">

#2.Cấu hình trên node Controller

*Cài NTP Server*

`apt-get install ntp -y`

**Cấu hình NTP Server**

Sửa file: /etc/ntp.conf
Khai báo: 


		```sh
		server NTP_SERVERiburst
		restrict -4 default kod notrap nomodify
		restrict -6 default kod notrap nomodify
		```

		
Khởi động lại NTP

	`service ntp restart`
	
*Cài đặt repo OpenStack*

		`apt-get install ubuntu-cloud-keyring`
		
tạo file /etc/apt/sources.list.d/cloudarchive-huno.list nội dung:

		`deb http://ubuntu-cloud.archive.canonical.com/ubuntu trusty-updates/juno main`
		
Update
 
		`apt-get update && apt-get dist-upgrade -y`
		
* Cài đặt database*

 `apt-get install mariadb-server python-mysqldb -y`
 
Cấu hình database /etc/mysql/my.cnf
 
	```sh
	[mysqld]
	bind-address = 10.10.10.11
	default-storage-engine = innodb
	innodb_file_per_table
	collation-server = utf8_general_ci
	init-connect = 'SET NAMES utf8'
	character-set-server = utf8
	```sh
	
khởi động mysql: 
 
 `service mysql restart`
 
Note: cài đặt pass root: 

	mysql_secure_install
	
*Cài đặt messaging server: sử dụng rabbitMQ*

	apt-get install rabbitmq-server -y
	
Cấu hình RabbitMQ

	rabbitmqctl change_password guest pass
	
** Đặt pass cho guest hãy nhớ pass này**

check RabbitMQ running

	rabbitmqctl status | grep rabbit
	
restart rabbitmq-server

	service rabbitmq-server restart