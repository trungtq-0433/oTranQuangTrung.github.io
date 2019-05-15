### Acronyms
  - mysql_server: Server to install mysql database
  - remote: Is the place will access mysql_server
  - db_user: Database username
  - db_pw: Database password
  - db_name: Database name
### Setup
##### I. On mysql_server.
**1. Install Mysql**
```
  sudo apt-get install mysql-server mysql-client libmysqlclient-dev
```
Check install succeed:
```
  sudo service mysql status
  mysql -uroot -p
  ```
  
**2. Enable Remote Access To MySQL.**
```
  vi /etc/mysql/mysql.conf.d/mysqld.cnf
```  
Edit this line:
```
  bind-address = <mysql_server's ip>
```
  Sau khi config thì ta restart lại mysql
After config, you need restart mysql service:
```
  sudo service mysql restart
```
2.1. Can not restart mysql.
  if you can not restart mysql and receive errors relative apparmour.
```
  vi /etc/apparmor.d/usr.sbin.mysqld
```
Add 3 line:
```
  /proc/*/status r,
  /sys/devices/system/node/ r,
  /sys/devices/system/node/node0/meminfo r,
```
Restart apparmour, mysql services:
``` 
  sudo service apparmour restart
  sudo service mysql restart
```

**3. Grant access to database.**
```sql
  mysql -uroot -p
  CREATE DATABASE xxx;
  GRANT ALL ON xxx.* TO <db_user>@'<remote's ip>' IDENTIFIED BY '<db_pw>';
```
In this case grant all privilege.
If you want to knows other privilege, you can refer [privilege document](https://dev.mysql.com/doc/refman/5.6/en/privileges-provided.html#priv_grant-option)

3.1. Remove grant privilege.
```sql
  mysql -uroot -p
  SELECT host, user FROM mysql.user WHERE User="<db_user>";
  DROP user '<db_user>'@'<remote's ip>'
```

3.2. Update grant privilege.
```sql
  mysql -uroot -p
  update db set Host='<new_host_ip>' where Db='<db_name>';
  update user set Host='<new_host_ip>' where user='<db_user>';
```

##### II. On remote
On remote:
```
mysql -u<db_user> -h<mysql_server's ip> -p<db_pw>
```

