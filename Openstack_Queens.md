<h1> Cài đặt Openstack Queens theo docs </h1>
<h2> I. Cài đặt cơ bản </h2>
<h4>1. Chuẩn bị môi trường </h4>
<h4><li> Mô hình mạng </li></h4>
<img src="https://github.com/anhict/images/blob/master/net.PNG?raw=true">
<h4> <li> 2. Cài đặt trên controller node </li></h4>
<p> Lưu ý: </p>
<ul>
<p><li> Đăng nhập với quyền root trên tất cả các bước cài đặt.</li> </p>
<p><li> Các thao tác sửa file trong hướng dẫn này sử dụng lệnh vi hoặc vim</li> </p>
<p><li> Password thống nhất cho tất cả các dịch vụ là Welcome123</li> </p>
</ul>
<h4> 2.1 Cài đặt các thành phần cơ bản </h4>
<h6> 2.1.1 Thiết lập và cài đặt các gói cơ bản</h6>
<li> Chạy lệnh để cập nhật các gói phần mềm </li>
<pre>  apt-get -y update </pre> 
<li> Thiết lập địa chỉ IP</li>
<li>Dùng lệnh <code> vi </code>để sửa file <code> /etc/network/interfaces</code> với nội dung như sau.</li>

<pre>
 # Interface EXT
 auto ens33
 iface ens33 inet static
 	address 192.168.239.162
 	netmask 255.255.255.0
 	gateway 192.168.239.2
 	dns-nameservers 8.8.8.8
</pre>


<li>Khởi động lại card mạng sau khi thiết lập IP tĩnh</li>

<pre>ifdown –a && ifup –a </pre>
<p> Ping kiểm tra : </p>
<img src="https://github.com/anhict/images/blob/master/21.png?raw=true">
<img src="https://github.com/anhict/images/blob/master/22.png?raw=true">
<li>Cấu hình hostname</li>

Sử dụng lệnh vi /etc/hosts sử file theo nội dụng sau trên controller node và compute1 node :
<pre>
 127.0.0.1      localhost controller
 192.168.239.162    controller
 192.168.239.163    compute1
 </pre>
<img src="https://github.com/anhict/images/blob/master/23.png?raw=true">

<li> Kiểm tra ping trên controller node và compute1 node</li>
<img src="https://github.com/anhict/images/blob/master/24.png?raw=true">
<img src="https://github.com/anhict/images/blob/master/25.png?raw=true">

<h6> 2.1.2 Cài đặt Network Time Protocol (NTP) </h6>
<p><li> Cài đặt các packages </li></p>
<pre>  apt-get -y install chrony </pre>

Sửa file vi /etc/chrony/chrony.conf 
Comment dòng 
<pre> pool 2.debian.pool.ntp.org offline iburst </pre>
Thay thế bằng dòng :
<pre>server controller iburst </pre>
Thêm dòng :
<pre> allow 192.168.239.0/24 </pre>
<li> Khởi động lại dịch vụ NTP </li>
<pre> service chrony restart </pre>
<li> Chạy lệnh kiểm tra : </li>
<pre> root@controller:~# chronyc sources </pre>

<img src="https://github.com/anhict/images/blob/master/26.PNG?raw=true">

<h6> 2.1.2 Cài đặt OpenStack packages trên tất cả các node </h6>

<pre> 
# apt install software-properties-common
# add-apt-repository cloud-archive:queens 
</pre><li> Cập nhật các gói phần mềm trên tất cả các node </li>
<pre> 
# apt update && apt dist-upgrade
</pre>

<li> Cài đặt OpenStack client: </li>
<pre>
# apt install python-openstackclient
</pre>
<li> Khởi động lại: </li>
<pre>
init 6
</pre>
<h6> 2.1.4 Cài đặt SQL database </h6>
<li> Cài đặt các packages </li>
<pre>apt install mariadb-server python-pymysql
</pre><li>Tạo file /etc/mysql/mariadb.conf.d/99-openstack.cnf với nội dung sau :
<pre> vi /etc/mysql/mariadb.conf.d/99-openstack.cnf
</pre>
<pre>
[mysqld]
bind-address = 192.168.239.162

default-storage-engine = innodb
innodb_file_per_table = on
max_connections = 4096
collation-server = utf8_general_ci
character-set-server = utf8
</pre><li> Khởi động lại dịch vụ SQL </li>
<pre> 
# service mysql restart
</pre><li> Thiết lập cơ bản cho MariaDB </li>
<pre> 
mysql_secure_installation
</pre>

<pre>  mysql -u root -pWelcome123
</pre>

<pre>  
 root@controller:~# mysql -u root -pWelcome123
 Enter password:
 Welcome to the MariaDB monitor.  Commands end with ; or \g.
 Your MariaDB connection id is 29
 Server version: 5.5.47-MariaDB-1ubuntu0.14.04.1 (Ubuntu)

 Copyright (c) 2000, 2015, Oracle, MariaDB Corporation Ab and others.

 Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

 MariaDB [(none)]>
 MariaDB [(none)]>
 MariaDB [(none)]> exit;
</pre>

<h6> 2.1.5 Cài đặt RabbitMQ </h6>
<li> Cài đặt packages </li>
<pre>  # apt install rabbitmq-server</pre>
<li> Gán quyền read, write cho tài khoản openstack trong RabbitMQ </li>

<pre>  # rabbitmqctl set_permissions openstack ".*" ".*" ".*"
</pre>

<h6> 2.1.5 Cài đặt  Memcached </h6>
<pre>  # apt install memcached python-memcache
</pre>
Sửa file /etc/memcached.conf
<pre>  -l 192.168.239.162
</pre>
<li> Khởi động lại dịch vụ </li>
<pre>  # service memcached restart
</pre>
<h6> 2.1.6 Cài đặt  Etcd </h6>
<li>Tạo etcd user:</li>
<pre># groupadd --system etcd
# useradd --home-dir "/var/lib/etcd" \
      --system \
      --shell /bin/false \
      -g etcd \
      etcd </pre>
 <li>Tạo các thư mục cần thiết: </li>
 <pre>
# mkdir -p /etc/etcd
# chown etcd:etcd /etc/etcd
# mkdir -p /var/lib/etcd
# chown etcd:etcd /var/lib/etcd
</pre>
 <li>Download and install the etcd tarball:  </li>
 <pre>
 # ETCD_VER=v3.2.7
# rm -rf /tmp/etcd && mkdir -p /tmp/etcd
# curl -L https://github.com/coreos/etcd/releases/download/${ETCD_VER}/etcd-${ETCD_VER}-linux-amd64.tar.gz -o /tmp/etcd-${ETCD_VER}-linux-amd64.tar.gz
# tar xzvf /tmp/etcd-${ETCD_VER}-linux-amd64.tar.gz -C /tmp/etcd --strip-components=1
# cp /tmp/etcd/etcd /usr/bin/etcd
# cp /tmp/etcd/etcdctl /usr/bin/etcdctl
</pre>

<li>Tạo file /etc/etcd/etcd.conf.yml </li>
<pre>
name: controller
data-dir: /var/lib/etcd
initial-cluster-state: 'new'
initial-cluster-token: 'etcd-cluster-01'
initial-cluster: controller=http://192.168.239.162:2380
initial-advertise-peer-urls: http://192.168.239.162:2380
advertise-client-urls: http://192.168.239.162:2379
listen-peer-urls: http://0.0.0.0:2380
listen-client-urls: http://192.168.239.162:2379

</pre>
<li> Tạo file /lib/systemd/system/etcd.service </li>
<pre>
[Unit]
After=network.target
Description=etcd - highly-available key value store

[Service]
LimitNOFILE=65536
Restart=on-failure
Type=notify
ExecStart=/usr/bin/etcd --config-file /etc/etcd/etcd.conf.yml
User=etcd

[Install]
WantedBy=multi-user.target
</pre>


<h4> 3. Cài đặt Keystone </h4>
<h6> 3.1 Cài đặt và cấu hình cho keysonte </h6>
<h6> 3.1.1. Tạo database cài đặt các gói và cấu hình keystone </h6>
<li> Đăng nhập vào MariaDB </li>
<pre>mysql -u root -pWelcome123</pre>
<li> Tạo user, database cho keystone </li>








































