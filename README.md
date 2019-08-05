# 13 Zabbix & Prometheus + Grafana

## Install Zabbix from reposytory.

`SELinux` в `permissive` (необязательный)
```php
sed -i 's/^SELINUX=.*/SELINUX=permissive/g' /etc/selinux/config
reboot
```

```php
yum install epel-release
yum install zlib-devel  glibc-devel curl-devel libidn-devel openssl-devel net-snmp-devel rpm-devel OpenIPMI-devel iksemel-devel libssh2-devel
```

Команды
```php
rpm -ivh https://repo.zabbix.com/zabbix/4.2/rhel/7/x86_64/zabbix-release-4.2-1.el7.noarch.rpm
yum-config-manager --enable rhel-7-server-optional-rpms
```

```php
yum install mariadb-server -y
systemctl enable mariadb && systemctl start mariadb 
```

```php
yum install httpd -y
systemctl enable httpd && systemctl start httpd
```

```php
groupadd zabbix
useradd –m –s /bin/bash -g zabbix zabbix
useradd –m –s /bin/bash -g zabbix zabbixsvr
```

Установка `Zabbix` из пакетов.
```php
yum install zabbix-server-mysql -y
yum install zabbix-web-mysql -y
yum install zabbix-agent -y
```

```php
systemctl enable zabbix-server.service
systemctl enable zabbix-agent.service
```

```php
iptables -I INPUT 1 -p tcp --dport 10050 -j ACCEPT
iptables-save
```

```php
cat /etc/zabbix/zabbix_server.conf
```
`DBHost=localhost`  
`DBName=zabbix`   
`DBUser=zabbix`  
`DBPassword=pass1234`  

```php
chmod 600 /etc/zabbix/zabbix_server.conf
```

```php
mysql -uroot
mysql> create database zabbix character set utf8 collate utf8_bin;
mysql> grant all privileges on zabbix.* to zabbix@localhost identified by 'pass1234';
mysql> quit;
```

`/usr/share/doc/zabbix-server-mysql-4.2.4/` - где раньше были `schema.sql`, `images.sql` и `data.sql`
```php
zcat /usr/share/doc/zabbix-server-mysql-4.2.4/create.sql.gz | mysql -uzabbix -ppass1234 zabbix
```

Редактирование временной зоны, по умолчанию  `php_value date.timezone` `Europe/Riga`  
надо раскомментировать # и добавить своё
```php
vi /etc/httpd/conf.d/zabbix.conf
systemctl restart httpd
```

```php
systemctl start zabbix-server
systemctl start zabbix-agent
```

http://192.168.11.150/zabbix/setup.php
Теперь можно произвести первый логин через веб-интерефс `Zabbix`.   
Для входа необходимо использовать логин `Admin` (с большой буквы), пароль — `zabbix`.
