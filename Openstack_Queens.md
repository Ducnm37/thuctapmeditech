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

Sử dụng lệnh vi /etc/hosts sử file theo nội dụng sau trên controller node và compute1 node :

<img src="https://github.com/anhict/images/blob/master/23.png?raw=true">

<li> Kiểm tra ping trên controller node và compute1 node</li>
<img src="https://github.com/anhict/images/blob/master/24.png?raw=true">
<img src="https://github.com/anhict/images/blob/master/25.png?raw=true">












































