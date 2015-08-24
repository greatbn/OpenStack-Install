#Cài Đặt Keystone
`mysql -u root -p`
*Gõ password user root MariaDB*

Tạo database keystone

`create database keystone`

Tạo user keystone và set quyền trên database keystone

```s
grant all privileges on keystone.* to 'keystone'@'localhost' identity by PASS
grant all privileges on keystone.* to 'keystone'@'%' identity by PASS
```

*thay PASS bằng pass keystone của bạn*

Cài đặt keystone

`apt-get install keystone python-keystoneclient -y`

- Cấu hình keystone
<ul>
<li>tạo admin token bằng openssl</li>

`openssl rand -hex 10`

<li>Sửa file /etc/keystone/keystone.conf</li>
```sh
			[DEFAULT]
			admin_token = ADMIN_TOKEN (chuỗi tạo ở trên)
			[DATABASE]
			connection = mysql://keystone:passwd@controller/keystone
			[token]
			provider = keystone.token.providers.uuid.Provider
			driver = keystone.token.persistence.backends.sql.Token
			[revoke]
			driver = keystone.contrib.revoke.backends.sql.Revoke
			[DEFAULT]
			verbose = True
```

<li>Chạy lệnh đồng bộ database</li>

			`su -s /bin/sh -c "keystone-manage db_sync" keystone`

<li>restart keystone</li>
			`service keystone restart`
<li>xóa sqlite database</li>
			`rm -f /var/lib/keystone/keystone.db`
<li>sử dụng crontab để thay đổi token của admin sau 1h</li>
		tạo file /var/spool/cron/crontabs/keystone nội dung
		```sh
		@hourly /usr/bin/keystone-manage token_flush >/var/log/keystone/
		keystone-tokenflush.log 2>&1
		```
</ul>
<ul>
- Tạo tenants, users, và roles
		```sh
		export OS_SERVICE_TOKEN = ADMIN_TOKEN (nãy tạo và ghi trong file cấu hình keystone)
		export OS_SERVICE_ENDPOINT=http://controller:35357/v2.0
		```
<li>tạo admin tenant </li>
		`keystone tenant-create --name admin --description "Admin Tenant"`
		
<li>tạo admin user</li>
		`keystone user-create --name admin --pass saphi --email saphi070@gmail.com`
<li>tạo admin role</li>
		`keystone role-create --name admin`
<li>thêm admin role  cho admin tenant và user</li>
		`keystone user-role-add --user admin --tenant admin --role admin`
<li>tạo demo tenant </li>
		`keystone tenant-create --name demo --description "Demo Tenant"`
		
<li>tạo demo user dưới demo tenant</li>
		`keystone user-create --name demo --tenant demo --pass saphi --email saphi070@gmail.com`
<li>tạo service tenant</li>
		`keystone tenant-create --name service --description "Service Tenant"`
<li>tạo service identity</li>
		`keystone service-create --name keystone --type identity --description "OpenStack Identity"`
<li>tạo identity service api endpoints</li>
		```sh
		keystone endpoint-create \
		--service-id $(keystone service-list | awk '/ identity / {print $2}') \
		--publicurl http://controller:5000/v2.0 \
		--internalurl http://controller:5000/v2.0 \
		--adminurl http://controller:35357/v2.0 \
		--region regionOne
		```
</ul>
Xác thực hệ thống
```sh
		unset OS_SERVICE_TOKEN và OS_SERVICE_ENDPOINT
		unset OS_SERVICE_TOKEN OS_SERVICE_ENDPOINT
```