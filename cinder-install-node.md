<h3>Cài đặt Cinder , tạo volume và launch instane bằng volume.</h3>
<h4>1. Cài đặt Cinder trên một node riêng.</h4>
<li>Lưu ý : Trên mô hình phải được cài đặt sẵn OpenStack , nếu chưa có tham khảo tại <a href="https://github.com/congto/OpenStack-Mitaka-Scripts/tree/master/OPS-Mitaka-LB-Ubuntu">đây</a></li>
<h3>1.1. Mô hình.</h3>
<img src="https://github.com/anhict/images/blob/master/Screenshot_52.png">
<h4>Trên Node Cinder thiết lập điah chỉ IP và hostname</h4>
<pre>vi /etc/network/interfaces</pre>
<pre>192.168.239.190 controller
192.168.239.191 compute1
192.168.239.192 compute2
192.168.239.193 compute3
192.168.239.194 cinder</pre>
<ul>
<li>Lưu lại file cấu hình và tiến hành yêu cầu phát phát lại địa chỉ IP :</li>
</ul>
<pre>ifdown -a <span class="pl-k">&amp;&amp;</span> ifup -a</pre>
<ul>
<li>Thiết lập file hosts :</li>
</ul>
<code>vi /etc/hosts</code>
<pre>127.0.0.1       localhost
127.0.1.1       controller
192.168.239.190 controller
192.168.239.191 compute1
192.168.239.192 compute2
192.168.239.193 compute3
192.168.239.194 cinder</pre>

<p>Chỉnh sửa tương tự trên node Controller và Compute1</p>
<ul>
<li>Reboot lại máy chủ để lấy cấu hình mới :</li>
</ul>
<h4>1.2. Cài đặt.</h4>
<ul>
<li>Ở đây mình chỉ tiến hành cài node Cinder, mặc định là OpenStack đã có và chúng ta chỉ cài thêm dịch vụ lưu trữ Block Storage (Cinder) , nếu chưa có OpenStack có thể xem tại <a href="https://github.com/congto/OpenStack-Mitaka-Scripts/tree/master/OPS-Mitaka-LB-Ubuntu/scripts">đây</a> để đồng bộ với mô hình cài node cinder .</li>
</ul>
<h4>Trên Node Controller :</h4>
<p>Cài đặt theo docs trên trang chủ https://docs.openstack.org/cinder/queens/install/cinder-controller-install-ubuntu.html </p>
<ul>
<li>Tạo cơ sở dữ liệu cho Cinder :</li>
</ul>
<p>Truy cập vào cơ sở dữ liệu và tạo databases:</p>
<pre>mysql -u root -pWelcome123</pre>

<p>Tạo cinder database:</p>

<pre>CREATE DATABASE cinder;
GRANT ALL PRIVILEGES ON cinder.* TO 'cinder'@'localhost' \
  IDENTIFIED BY 'Welcome123';
GRANT ALL PRIVILEGES ON cinder.* TO 'cinder'@'%' \
  IDENTIFIED BY 'Welcome123';</pre>
  <p>Chạy source admin-openrc để có quyền truy cập vào các lệnh CLI của quản trị viên :</p>
  <pre>. admin-openrc</pre>
  <p>Tạo cinder user :</p>
  <pre>openstack user create --domain default --password-prompt cinder</pre>
  
  <p>Thêm role admin vào cinder user :</p>
  <pre>openstack role add --project service --user cinder admin</pre>
  <p>Tạo các entities cinderv2 và cinderv3 :</p>
  
  <pre>openstack service create --name cinderv2 \
  --description "OpenStack Block Storage" volumev2
  openstack service create --name cinderv3 \
  --description "OpenStack Block Storage" volumev3</pre>
  <p>Tạo các enpoint cho Cinder :</p>
  <pre>openstack endpoint create --region RegionOne \
  volumev2 public http://controller:8776/v2/%\(project_id\)s
  openstack endpoint create --region RegionOne \
  volumev2 internal http://controller:8776/v2/%\(project_id\)s
  openstack endpoint create --region RegionOne \
  volumev2 admin http://controller:8776/v2/%\(project_id\)s
  openstack endpoint create --region RegionOne \
  volumev3 public http://controller:8776/v3/%\(project_id\)s
  openstack endpoint create --region RegionOne \
  volumev3 internal http://controller:8776/v3/%\(project_id\)s
  openstack endpoint create --region RegionOne \
  volumev3 admin http://controller:8776/v3/%\(project_id\)s</pre>
  
  

























