<h3>1. Tổng quan về Openstack metadata service </h3>
<p>Openstask sử dụng Metadata Service để thêm các tùy chỉnh đến các Instances thông qua Network (Neutron). Ví dụ như ta muốn thêm ssh keys, passwords, hostnames, hoặc các scripts tới Instances. Nếu đứng từ góc độ người dùng, Metadata Service giúp bất kỳ client nào trong Openstack Instances có thể lấy được các thông tin của chính mình như:</p>
<p>– Thông tin về Public IP/Hostname</p>
<p>– Thông tin về SSH Public Keys</p>
<p>Chúng ta biết rằng máy ảo OpenStack được khởi tạo thông qua cloud-init, chẳng hạn như cấu hình card mạng, tên máy, password. Cloud-init là một tiến trình chạy bên trong một máy ảo. Nó thu thập thông tin cấu hình (metadata) của máy ảo thông qua datasource. Cloud-init triển khai nhiều nguồn dữ liệu khác nhau và các nguyên tắc triển khai nguồn dữ liệu khác nhau khác nhau. Có hai nguồn dữ liệu chính thường được sử dụng:</p>
<p>ConfigDriver: Nova ghi tất cả thông tin cấu hình vào một raw file và gắn nó vào máy ảo thông qua cdrom. Tại thời điểm này, bạn có thể thấy / dev / sr0a thiết bị ảo tương tự như bên trong của máy ảo (Lưu ý: sr là viết tắt của scsi + rom). Cloud-init chỉ cần đọc thông tin / dev / sr0file để lấy thông tin cấu hình máy ảo.</p>
<p>Metadata: Nova bắt đầu dịch vụ siêu dữ liệu HTTP local. Máy ảo chỉ cần truy cập dịch vụ siêu dữ liệu qua HTTP để lấy thông tin cấu hình máy ảo liên quan</p>
<p>Metadata giải quyết được các vấn đề </p>
<p>Dịch vụ Metadata Nova được bắt đầu trên máy chủ (nút điều khiển nơi đặt nova-api). Mạng vật lý của tenant network và máy chủ bên trong máy ảo bị ngắt kết nối. Máy ảo truy cập dịch vụ siêu dữ liệu của Nova như thế nào?</p>
<p>Làm thế nào để dịch vụ metadata Nova biết VM nào đã khởi tạo yêu cầu.<p>
<h3>2. Metadata service configuration</h3>
<h4>2.1 Cấu hình Nova</h4>
<p>Tên dịch vụ metadata của Nova là nova-api-metadata, nhưng dịch vụ thường được hợp nhất với dịch vụ nova-api:</p>
<pre>[DEFAULT]
enabled_apis = osapi_compute,metadata</pre>
<p>Ngoài ra, máy ảo để truy cập dịch vụ metadata của Nova cần Neutron để chuyển tiếp. Vì lý do này, ở đây chúng ta cần cấu hình file nova.conf :</p>
<pre>[neutron]
service_metadata_proxy = true</pre>
<h4>2.2 Cấu hình Neutron </h4>
<p>máy ảo truy cập dịch vụ metadata của Nova cần Neutron để chuyển tiếp nó, và nó có thể được chuyển tiếp bởi L3 agent hoặc được chuyển tiếp bởi DHCP agent. Có 2 lựa chọn:</p>
<p>Thông qua chuyển tiếp l3-agent,mạng mà VM sử dụng được kết hợp với Router.</p>
<p>Với chuyển tiếp DHCP agent, chức năng dhcp phải được kích hoạt trên mạng nơi máy ảo được cấu hình.</p>
<p>Metadata được chuyển tiếp bởi L3-agent theo mặc định, nhưng trong thực tế, chức năng dhcp thường được bật trên mạng của máy ảo và không cần thiết phải sử dụng bộ định tuyến. Vì vậy,có thể sử dụng dhcp-agent để chuyển tiếp các gói tin. Cấu hình như sau:</p>
<pre># /etc/neutron/dhcp_agent.ini [DEFAULT]
force_metadata = true

# /etc/neuron/l3_agent.ini [DEFAULT]
enable_metadata_proxy = false</pre>
<h3>3. Làm thế nào Openstack VMs truy cập service metadata </h3>
<h4>3.1 Truy cập metadata từ VM </h4>
<p>Cloud-init truy cập vào địa chỉ URL của dịch vụ metadata là URL address,đây là địa chỉ đặc biệt AWS's Metadata service, cụ thể là địa chỉ IPv4 Link Local IP private (10.0.0.0/8, 172.16.0.0/12, 192.168.0.0/16), nó không thể được sử dụng để định tuyến Internet. Nó thường chỉ được sử dụng cho các mạng kết nối trực tiếp. Nếu hệ điều hành (Windows) không nhận được IP, nó có thể được cấu hình tự động như là một địa chỉ IP của phân đoạn mạng.http: //169.254.169.254 169.254.0.0/16 169.254.0.0/16</p>
<p>Tại sao AWS chọn IP 169.254.169.254? Điều này là do việc chọn Link Local IP có thể tránh xung đột IP với người dùng. Tại sao lại chọn 169.254.169.254 IP này thay vì IP khác 169.254.0.0/24, có lẽ do nó dễ nhớ.</p>
<p>Ngoài ra AWS có một số địa chỉ rất thú vị:</p>
<pre>169.254.169.253: DNS service.
169.254.169.123: NTP service.</pre>
<p>Máy ảo OpenStack cũng thu thập thông tin cấu hình ban đầu của máy ảo: http://169.254.169.254</p>
<pre>curl http://169.254.169.254/2009-04-04/meta-data</pre>
<p>chúng ta có thể thấy từ service metadata, chúng ta có được uuid, hostname, project ID, availability_zone... của máy ảo.</p>
<p>Làm cách nào một máy ảo có được thông tin metadata bằng cách truy cập địa chỉ này 169.254.169.254? Trước tiên hãy xem bảng định tuyến cho máy ảo tiếp theo:</p>
<pre># route -n
Kernel IP routing table
Destination     Gateway         Genmask         Flags Metric Ref    Use Iface
0.0.0.0         10.0.0.126      0.0.0.0         UG    0      0        0 eth0
10.0.0.64       0.0.0.0         255.255.255.192 U     0      0        0 eth0
169.254.169.254 10.0.0.66       255.255.255.255 UGH   0      0        0 eth0</pre>
