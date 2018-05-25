# ServerMonitor
服务器监控系统

1.安装

1.安装mysql
a. Install Repository with MySQL database
```
# wget http://repo.zabbix.com/zabbix/3.0/ubuntu/pool/main/z/zabbix-release/zabbix-release_3.0-2+trusty_all.deb
# dpkg -i  zabbix-release_3.0-2+trusty_all.deb
# apt update
```
b. Install Zabbix server, frontend, agent
```
# apt install zabbix-server-mysql zabbix-frontend-php zabbix-agent
```
c. Create initial database
```
# mysql -uroot -p
password
mysql> create database zabbix character set utf8 collate utf8_bin;
mysql> grant all privileges on zabbix.* to zabbix@localhost identified by 'password';
mysql> quit;
Import initial schema and data. You will be prompted to enter your newly created password.
# zcat /usr/share/doc/zabbix-server-mysql*/create.sql.gz | mysql -uzabbix -p zabbixd. Configure the database for Zabbix server
sudo vi /etc/zabbix/zabbix_server.conf
设置属性DBPassword
service zabbix-server start
sudo vi /etc/apache2/conf-enabled/zabbix.conf修改时区
```

重启apache2
sudo service apache2 restart
访问:http://ip/zabbix就可以进入管理页面

查看日志: cat /var/log/zabbix/zabbix_server.log 
2.登录和配置用户
(1)默认管理员账户:Admin/zabbix
(2)新建监控主机

1、添加zabbix用户群组
sudo groupadd zabbix
sudo useradd -g zabbix -m zabbix
2、下载zabbix源码安装包
3、解压、编译、安装

tar -zxvf zabbix-3.0.17.tar.gz
cd zabbix-3.0.17

./configure --prefix=/usr/local/zabbix --enable-agent

    sudo make install
4、修改agentd配置文件，配置zabbix server地址以及agented自己的Hostname


vim /usr/local/zabbix/etc/zabbix_agentd.conf
Server= view.jstinno.com #服务端ip地址
ServerActive= view.jstinno.com #服务端ip地址(主动模式)
Hostname=jenkins10 #该值跟zabbix web上配置的主机名称要一致(配置-》主机-》主机名称)


5、配置成使用service启动关闭
```
sudo cp zabbix-3.0.3/misc/init.d/tru64/zabbix_agentd /etc/init.d/
sudo chmod +x /etc/init.d/zabbix_ agentd

sudo ln -s /usr/local/zabbix/sbin/* /usr/local/sbin/

sudo ln -s /usr/local/zabbix/bin/* /usr/local/bin/

sudo ln -s /usr/local/zabbix/etc/zabbix_agentd.conf /usr/local/etc/
```
6、启动、检查
```
sudo service zabbix_agentd start
ps -aux|grep zabbix_agentd
解决乱码:
https://www.cnblogs.com/cloudos/p/8297856.html
```
3.与grafana混合使用

1.安装grafana

