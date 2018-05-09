<h1> Cài đặt Openstack Queens theo docs mô hình Self-service networks </h1>
<h2> I. Cài đặt cơ bản </h2>
<h4>1. Chuẩn bị môi trường </h4>
<h4><li> Mô hình mạng </li></h4>
<img src="https://github.com/anhict/images/blob/master/SSV.PNG?raw=true">
<li> Sử dụng 2 node đã cài sẵn Ubuntu phiên bản 16.04.</li>
<h4> <li> 2. Cài đặt trên controller node </li></h4>
<p> Lưu ý: </p>
<ul>
<p><li> Đăng nhập với quyền root trên tất cả các bước cài đặt.</li> </p>
<p><li> Các thao tác sửa file trong hướng dẫn này sử dụng lệnh vi hoặc vim</li> </p>
<p><li> Password thống nhất cho tất cả các dịch vụ là Welcome123</li> </p>
</ul>
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
<pre>root@controller:~# chronyc sources 
root@controller:~# chronyc sources
210 Number of sources = 1
MS Name/IP address         Stratum Poll Reach LastRx Last sample
===============================================================================
^? localhost                     0   7     0   10y     +0ns[   +0ns] +/-    0ns

</pre>
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
bind-address = 0.0.0.0

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
initial-cluster: controller=http://192.168.239.166:2380
initial-advertise-peer-urls: http://192.168.239.166:2380
advertise-client-urls: http://192.168.239.166:2379
listen-peer-urls: http://0.0.0.0:2380
listen-client-urls: http://192.168.239.166:2379
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
<li> Khởi động lại dịch vụ </li>
<pre>
# systemctl enable etcd
# systemctl start etcd
</pre>
<h4> 3. Cài đặt Keystone </h4>
<h6> 3.1 Cài đặt và cấu hình cho keysonte </h6>
<h6> 3.1.1. Tạo database cài đặt các gói và cấu hình keystone </h6>
<li> Đăng nhập vào MariaDB </li>
<pre>mysql -u root -pWelcome123</pre>
<li> Tạo user, database cho keystone </li>
<pre>
MariaDB [(none)]> CREATE DATABASE keystone;
MariaDB [(none)]> GRANT ALL PRIVILEGES ON keystone.* TO 'keystone'@'localhost' \
IDENTIFIED BY 'Welcome123';
MariaDB [(none)]> GRANT ALL PRIVILEGES ON keystone.* TO 'keystone'@'%' \
IDENTIFIED BY 'Welcome123';
</pre>
<li> Cài đặt các packages </li>
<pre># apt install keystone  apache2 libapache2-mod-wsgi </pre>
<li> Dùng lệnh vi sửa file  /etc/keystone/keystone.conf với nội dung sau : </li>
<pre>
[database]
connection = mysql+pymysql://keystone:Welcome123@controller/keystone
[token]
provider = fernet
</pre>
<li> Đồng bộ database cho keystone </li>
<pre> # su -s /bin/sh -c "keystone-manage db_sync" keystone </pre>
<li> Khởi tạo kho lưu trữ khóa Fernet:</li>
<pre>
# keystone-manage fernet_setup --keystone-user keystone --keystone-group keystone
# keystone-manage credential_setup --keystone-user keystone --keystone-group keystone
</pre>
<li> Khởi động dịch vụ Identity </li>
<pre>
# keystone-manage bootstrap --bootstrap-password Welcome123 \
  --bootstrap-admin-url http://controller:5000/v3/ \
  --bootstrap-internal-url http://controller:5000/v3/ \
  --bootstrap-public-url http://controller:5000/v3/ \
  --bootstrap-region-id RegionOne
 </pre>
 <li> Sử dụng lệnh vi sửa file /etc/apache2/apache2.conf thêm vào dòng: </li>
 <pre> ServerName controller </pre>
 
 <li> Khởi động lại dịch vụ : </li>
 <pre># service apache2 restart </pre>
 <li> Cấu hình tài khoản </li>
 <pre>
$ export OS_USERNAME=admin
$ export OS_PASSWORD=Welcome123
$ export OS_PROJECT_NAME=admin
$ export OS_USER_DOMAIN_NAME=Default
$ export OS_PROJECT_DOMAIN_NAME=Default
$ export OS_AUTH_URL=http://controller:5000/v3
$ export OS_IDENTITY_API_VERSION=3
</pre>

<li> Tạo  domain, projects, users, and roles </li>
<pre>openstack domain create --description "An Example Domain" example</pre>
<pre>openstack project create --domain default \
  --description "Service Project" service </pre>
<pre>openstack project create --domain default \
  --description "Demo Project" demo </pre>
<pre>openstack user create --domain default \
  --password-prompt demo </pre>
<pre>openstack role create user </pre>
<pre>openstack role add --project demo --user demo user </pre>

<li> Verify </li>
- Bỏ thiết lập trong biến môi trường của OS_TOKEN và OS_URL bằng lệnh
<pre>$ unset OS_AUTH_URL </pre>
-  Là người dùng quản trị, yêu cầu mã thông báo xác thực:


<pre>$ openstack --os-auth-url http://controller:5000/v3 \
  --os-project-domain-name Default --os-user-domain-name Default \
  --os-project-name admin --os-username admin token issue </pre>
<pre>$ openstack --os-auth-url http://controller:5000/v3 \
  --os-project-domain-name Default --os-user-domain-name Default \
  --os-project-name demo --os-username demo token issue </pre>
  
  <li> Tạo file admin-openrc với nội dung sau :</li>
<pre>export OS_PROJECT_DOMAIN_NAME=Default
export OS_USER_DOMAIN_NAME=Default
export OS_PROJECT_NAME=admin
export OS_USERNAME=admin
export OS_PASSWORD=Welcome123
export OS_AUTH_URL=http://controller:5000/v3
export OS_IDENTITY_API_VERSION=3
export OS_IMAGE_API_VERSION=2 </pre>
 <li> Tạo file demo-openrc với nội dung sau :</li>
 <pre>export OS_PROJECT_DOMAIN_NAME=Default
export OS_USER_DOMAIN_NAME=Default
export OS_PROJECT_NAME=demo
export OS_USERNAME=demo
export OS_PASSWORD=Welcome123
export OS_AUTH_URL=http://controller:5000/v3
export OS_IDENTITY_API_VERSION=3
export OS_IMAGE_API_VERSION=2</pre>
<li> Chạy script admin-openrc </li>
<pre> $ . admin-openrc </pre>
<li> Gõ lệnh dưới để kiểm tra biến môi trường ở trên đã chính xác hay chưa </li>
<pre> $ openstack token issue </pre>
<h4> 4. Cài đặt Glance </h4>
Glance là dịch vụ cung cấp các image (các hệ điều hành đã được đóng gói sẵn), các image này sử dụng theo cơ chế template để tạo ra các máy ảo. )

Lưu ý: Thư mục chứa các file images trong hướng dẫn này là /var/lib/glance/images/

Glance có các thành phần sau:
<ul>
<li>glance-api:</li>
<li>glance-registry:</li>
<li>Database:</li>
<li>Storage repository for image file</li>
<li>Metadata definition service</li>
</ul>
<h6> 4.1. Tạo database và endpoint cho glance </h6>
<li> Đăng nhập vào mysql <li>
<pre> mysql -uroot -pWelcome123 </pre>
<pre>
MariaDB [(none)]> CREATE DATABASE glance;
MariaDB [(none)]> GRANT ALL PRIVILEGES ON glance.* TO 'glance'@'localhost' \
IDENTIFIED BY 'Welcome123';
MariaDB [(none)]> GRANT ALL PRIVILEGES ON glance.* TO 'glance'@'%' \
IDENTIFIED BY 'Welcome123';
</pre>
<h6> 4.1.2. Cấu hình xác thực cho dịch vụ glance </h6>
<li>Thiết lập biến môi trường:</li>
<pre>$ . admin-openrc</pre>
<li>Tạo user Glance </li>
<pre>$ openstack user create --domain default --password-prompt glance</pre>
<li>Add the admin role to the glance user and service project:</li>
<pre>$ openstack role add --project service --user glance admin</pre>
</li>Tạo glance service</li>
<pre>$ openstack service create --name glance \
  --description "OpenStack Image" image
</pre>
<li>Tạo các endpoint cho dịch vụ glance</li>
<pre>
$ openstack endpoint create --region RegionOne \
  image public http://controller:9292
$ openstack endpoint create --region RegionOne \
  image internal http://controller:9292
$ openstack endpoint create --region RegionOne \
  image admin http://controller:9292
</pre>

<h6>4.1.3. Cài đặt các gói và cấu hình cho dịch vụ glance</h6>
<pre># apt install glance </pre>
<li>Chỉnh sửa file /etc/glance/glance-api.conf </li>
<pre>
[database]
connection = mysql+pymysql://glance:Welcome123@controller/glance
[keystone_authtoken]
auth_uri = http://controller:5000
auth_url = http://controller:5000
memcached_servers = controller:11211
auth_type = password
project_domain_name = Default
user_domain_name = Default
project_name = service
username = glance
password = Welcome123
[paste_deploy]
flavor = keystone
[glance_store]
stores = file,http
default_store = file
filesystem_store_datadir = /var/lib/glance/images/
</pre>
<li>Sửa file /etc/glance/glance-registry.conf </li>
<pre>
[database]
connection = mysql+pymysql://glance:Welcome123@controller/glance
[keystone_authtoken]
auth_uri = http://controller:5000
auth_url = http://controller:5000
memcached_servers = controller:11211
auth_type = password
project_domain_name = Default
user_domain_name = Default
project_name = service
username = glance
password = Welcome123
[paste_deploy]
flavor = keystone
</pre>
<li> Đồng bộ database cho glance </li>
<pre># su -s /bin/sh -c "glance-manage db_sync" glance</pre>
<li>Khởi động lại dịch vụ Glance</li>
<pre># service glance-registry restart
# service glance-api restart
</pre>


<li> Thiết lập biến môi trường :</li>
<pre>$ . admin-openrc</pre>
<li> Download the source image: </li>
<pre>$ wget http://download.cirros-cloud.net/0.3.5/cirros-0.3.5-x86_64-disk.img</pre>
<li>Upload file image vừa tải về </li>
<pre>
openstack image create "cirros" \
  --file cirros-0.3.5-x86_64-disk.img \
  --disk-format qcow2 --container-format bare \
  --public
</pre>
  <li>Kiểm tra</li>
  <pre> openstack image list
</pre>
 <pre>
root@controller:~# openstack image list
+--------------------------------------+--------+--------+
| ID                                   | Name   | Status |
+--------------------------------------+--------+--------+
| 9cb53978-b51a-41a5-a14a-de648a1762cd | cirros | active |
+--------------------------------------+--------+--------+
 root@controller:~# </pre>
<h4>Cài đặt NOVA (Compute service)</h4>
<h6>5.2. Cài đặt và cấu hình nova</h6>
<li>Đăng nhập vào database với quyền root</li>
<pre>mysql -uroot -pWelcome123</pre>
<li>Tạo database</li>
<pre>
MariaDB [(none)]> CREATE DATABASE nova_api;
MariaDB [(none)]> CREATE DATABASE nova;
MariaDB [(none)]> CREATE DATABASE nova_cell0;
MariaDB [(none)]> GRANT ALL PRIVILEGES ON nova_api.* TO 'nova'@'localhost' \
  IDENTIFIED BY 'Welcome123';
MariaDB [(none)]> GRANT ALL PRIVILEGES ON nova_api.* TO 'nova'@'%' \
  IDENTIFIED BY 'Welcome123';
MariaDB [(none)]> GRANT ALL PRIVILEGES ON nova.* TO 'nova'@'localhost' \
  IDENTIFIED BY 'Welcome123';
MariaDB [(none)]> GRANT ALL PRIVILEGES ON nova.* TO 'nova'@'%' \
  IDENTIFIED BY 'Welcome123';
MariaDB [(none)]> GRANT ALL PRIVILEGES ON nova_cell0.* TO 'nova'@'localhost' \
  IDENTIFIED BY 'Welcome123';
MariaDB [(none)]> GRANT ALL PRIVILEGES ON nova_cell0.* TO 'nova'@'%' \
  IDENTIFIED BY 'Welcome123';
</pre>
<li>Khai báo biến môi trường</li>
<pre>$ . admin-openrc</pre>
<li>Tạo user </li>
<pre> $ openstack user create --domain default --password-prompt nova </pre>
<li>Add the admin role to the nova user: </li>
<pre>$ openstack role add --project service --user nova admin</pre>
<li>Tạo service </li>
<pre>$ openstack service create --name nova \
  --description "OpenStack Compute" compute</pre>
  </li>Tạo endpoint</li>
  <pre>
  $ openstack endpoint create --region RegionOne \
  compute public http://controller:8774/v2.1
  $ openstack endpoint create --region RegionOne \
  compute internal http://controller:8774/v2.1
  $ openstack endpoint create --region RegionOne \
  compute admin http://controller:8774/v2.1
  </pre>
  <li>Create a Placement service </li>
  <pre>$ openstack user create --domain default --password-prompt placement</pre>
  <li>Add the Placement user to the service project with the admin role:</li>
<pre>$ openstack role add --project service --user placement admin  </pre>
  <li>Create the Placement API entry in the service catalog:   </li>
  <pre>$ openstack service create --name placement --description "Placement API" placement   </pre>
    <li>Create the Placement API service endpoints:   </li>
  <pre>
  $ openstack endpoint create --region RegionOne placement public http://controller:8778
  $ openstack endpoint create --region RegionOne placement internal http://controller:8778
  $ openstack endpoint create --region RegionOne placement admin http://controller:8778
</pre>
    <li>Cài đặt các gói cho nova và cấu hình   </li>
  <pre># apt install nova-api nova-conductor nova-consoleauth \
  nova-novncproxy nova-scheduler nova-placement-api   </pre>
    <li>Sửa file /etc/nova/nova.conf   </li>
  <pre>[api_database]
connection = mysql+pymysql://nova:Welcome123@controller/nova_api
[database]
connection = mysql+pymysql://nova:Welcome123@controller/nova 
[DEFAULT]
transport_url = rabbit://openstack:Welcome123@controller
[api]
auth_strategy = keystone
[keystone_authtoken]
auth_uri = http://controller:5000
auth_url = http://controller:5000
memcached_servers = controller:11211
auth_type = password
project_domain_name = default
user_domain_name = default
project_name = service
username = nova
password = Welcome123
[DEFAULT]
my_ip = 192.268.239.162
use_neutron = True
firewall_driver = nova.virt.firewall.NoopFirewallDriver
[vnc]
enabled = true
server_listen = $my_ip
server_proxyclient_address = $my_ip
[glance]
api_servers = http://controller:9292
[oslo_concurrency]
lock_path = /var/lib/nova/tmp
[placement]
# ...
os_region_name = RegionOne
project_domain_name = Default
project_name = service
auth_type = password
user_domain_name = Default
auth_url = http://controller:5000/v3
username = placement
password = Welcome123
</pre>
Comment dòng log_dir trong [DEFAULT] section.
    <li>Populate the nova-api database:   </li>
  <pre># su -s /bin/sh -c "nova-manage api_db sync" nova   </pre>
    <li>Register the cell0 database:   </li>
  <pre># su -s /bin/sh -c "nova-manage cell_v2 map_cell0" nova
   </pre>
    <li>Create the cell1 cell:   </li>
  <pre># su -s /bin/sh -c "nova-manage cell_v2 create_cell --name=cell1 --verbose" nova   </pre>
    <li>Populate the nova database:   </li>
  <pre># su -s /bin/sh -c "nova-manage db sync" nova   </pre>
    <li># nova-manage cell_v2 list_cells   </li>
  <pre>+-------+--------------------------------------+------------------------------------+-------------------------------------------------+
|  Name |                 UUID                 |           Transport URL            |               Database Connection               |
+-------+--------------------------------------+------------------------------------+-------------------------------------------------+
| cell0 | 00000000-0000-0000-0000-000000000000 |               none:/               | mysql+pymysql://nova:****@controller/nova_cell0 |
| cell1 | bca97ce7-5143-41a9-93d3-75e410dd509a | rabbit://openstack:****@controller |    mysql+pymysql://nova:****@controller/nova    |
+-------+--------------------------------------+------------------------------------+-------------------------------------------------+
   </pre>
<li>Kết thúc bước cài đặt và cấu hình nova   </li>
  <pre># service nova-api restart
# service nova-consoleauth restart
# service nova-scheduler restart
# service nova-conductor restart
# service nova-novncproxy restart   </pre>
<li>Kiểm tra xem các service của nova hoạt động tốt hay chưa bằng lệnh dưới:</li>
  <pre> openstack compute service list
root@controller:~# openstack compute service list
+----+------------------+------------+----------+---------+-------+----------------------------+
| ID | Binary           | Host       | Zone     | Status  | State | Updated At                 |
+----+------------------+------------+----------+---------+-------+----------------------------+
|  1 | nova-scheduler   | controller | internal | enabled | up    | 2018-05-09T09:12:01.000000 |
|  5 | nova-consoleauth | controller | internal | enabled | up    | 2018-05-09T09:12:02.000000 |
|  6 | nova-conductor   | controller | internal | enabled | up    | 2018-05-09T09:12:03.000000 |
|  7 | nova-compute     | compute1   | nova     | enabled | up    | 2018-05-09T09:12:02.000000 |
+----+------------------+------------+----------+---------+-------+----------------------------+
root@controller:~#</pre>

  
  // Đang hoàn thiện.
