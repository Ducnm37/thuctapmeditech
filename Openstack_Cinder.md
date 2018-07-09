<h3>1. Vai trò của project Cinder</h3>
<p>Cinder là dịch vụ Block storage trong Openstack.Nó được thiết kế để người sử dụng cuối có thể thực hiện việc lưu trữ bởi Nova,việc này được thực hiện bởi LVM hoặc các nền tảng lưu trữ khác.Cinder ảo hóa việc quản lý các thiết bị block storage và cung cấp cho người dùng cuối 1 API đáp ứng được nhu cầu tự phục vụ cũng như sử dụng các tài nguyên đó mà không cần biết quá nhiều kiến thức chuyên sâu.


<h3>2. Các thành phần của project Cinder </h3>
<img src="https://github.com/anhict/images/raw/master/11.png">
<p>Cinder-api: là một ứng dụng WSGI chấp nhận và xác nhận các yêu cầu REST (JSON hoặc XML) từ client và chuyển chúng tới các quy trình Cinder khác nếu thích hợp với AMQP</p>
<p>Cinder scheduler: Lên lịch và định tuyến các yêu cầu tới dịch vụ volume thích hợp. Tùy thuộc vào cách cấu hình có thể là dùng round-robin để định ra việc sẽ dùng volume service nào hoặc có thể phức tạp hơn bằng cách dùng Filter scheduler.Filter scheduler là mặc định và bật các bộ lọc như Capacity,Avaibility zone,Volume type và Capability.</p>
<p>Cinder-volume: Quản lí các thiết bị block storage,đặc biệt các các thiết bị back-end
<p>Cinder-backup: Cung cấp phương thức để backup một Block storage volume tới Openstack Oject storage(Swift)Khi một máy client yêu cầu sao lưu volume được tạo ra hoặc quản lý.</p>
<p>SQLDB: Cung cấp 1 phương tiện dùng để backup dữ liệu từ Switf/ceph ...</p>
<ul><li>Back-end storage device: Dịch vụ Block storage yêu cầu một vài kiểm của back-end storage mà dịch vụ có thể chạy trên đó.Mặc định là sử dụng LVM trên một local volume group tên là "Cinder-volume"</li>
<li>User và project: Cinder được dùng bởi người dùng hoặc khách hàng khác nhau(project trong 1 share system),sử dụng chỉ định truy cập dựa vào role(role based access).Các role kiểm soát các hành động mà người dùng được phép thực hiện.Trong cấu hình mặc định,phần lớn các hành động không yêu cầu 1 role cụ thể nhưng sysadmin có thể cấu hình trong file policy.json để quản lý các rule.Mộ truy cập của người dùng có thể bị hạn chế bởi project,nhưng username và pass được gán chỉ định cho mỗi user.Keypair cho phép truy cập tới 1 volume được mở cho mỗi user,nhưng hạn ngạch để kiểm soát sự sử dụng tài nguyên trên các tài nguyên phần cứng có sẵn là mỗi project.</li></ul>
<ul><li>Volume, snapshot và backup</li>
 <ul>
   <li>Volume: Các tài nguyên block storage được phân phối để có thể gán vào máy ảo như một ổ lưu trữ thứ 2 hoặc có thể dùng như là vùng lưu trữ cho root để boot vào máy ảo.Volume là các thiết bị bloc storage R/W bền vững thường được dùng để gán vào compute node thông qua iSCSI.</li> 
   <li>Snapshot: Một bản copy trong một thời điểm nhất định của volume snapshot có thể được tạo từ một volume mà mới được dùng gần đây trong trạng thái sẵn sàng.Snapshot có thể được dùng để tạo một volume mới thông qua việc tạo từ snapshot.</li>
   <li>Backup: Một bản copy lưu trữ của 1 volume thông thường được lưu ở swift.</li>
 </ul> 
</ul>
<h3>3. Luồng làm việc của Cinder</h3>
<h4>3.1 Workflow của Cinder khi tạo mới Volume </h4>
<img src="https://github.com/anhict/images/raw/master/12.png">
<p>Hình bên trên mô tả quy trình tạo Volume , tiếp theo chúng ta cùng đến với quy trình tạo ra volume mới của Cinder :</p>
<img src="https://github.com/anhict/images/raw/master/14.png">

