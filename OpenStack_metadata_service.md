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
