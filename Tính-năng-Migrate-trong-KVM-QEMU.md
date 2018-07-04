<h2>Tài liệu lab Live Migrate trên KVM</h2>
<h3>1. Khái niệm KVM-QEMU </h3>
<p>KVM (Kernel-base virtual machine): là một mudule nằm trong nhân Linux để có thể tạo ra không gian cho các ứng dụng để các ứng dụng đó có thể chạy các tính với quyền lớn nhất. Qemu: là một hypervisor dạng Paravirtualization nó tương tự như VMwate workstation</p>
<p>Vậy KVM-QEMU là ảo hóa kết hợp QEMU với KVM theo kiểu QEMU sẽ móc nối với mudule KVM trở thành dạng ảo hóa Full virtualization</p>
<p>Kiến trúc của KVM-QEMU</p>
<img src="https://github.com/anhict/images/blob/master/687474703a2f2f692e696d6775722e636f6d2f4c44554a534e5a2e706e67.png">
<h3>2. Các tool để điều khiển KVM-Qemu</h3>
<ul>
<li>Bao gồm:</li>

<li>virsh</li>
<li>virt-manager</li>
<li>Openstack</li>
<li>ovirt</li>
...</ul>
<p>Hình trên ta có thể hiểu: Đối với từng dạng ảo hóa như Kvm, Xen, .. sẽ có một tiến trình Libvirt chạy để điều khiển các dang ảo hóa và cung cấp những API để các tool như virsh, virt-manager, Openstack, ovirt có thể giao tiếp với KVM-Qemu thông qua livbirt</p>
<img src="https://github.com/anhict/images/blob/master/687474703a2f2f692e696d6775722e636f6d2f6332516e3456382e706e67.png">
<h3>3. Tính năng Migrate trong KVM-QEMU</h3>
<h4>Khái niệm</h4>
<p>Migrate là chức năng được KVM-QEMU hỗ trợ, nó cho phép di chuyển các guest từ một host vật lý này sang host vật lý khác và không ảnh hướng để guest đang chạy cũng như dữ liệu bên trong nó.</p>
<h4>Vai trò</h4>
<p>Migrate giúp cho nhà quản trị có thể di chuyển các guest trên host đi để phục vụ cho việc bảo trì và nâng cấp hệ thống, nó cũng giúp nhà quản trị nâng cao tính dự phòng, và cũng có thể làm nhiệm vụ load bandsing cho hệ thống khi một máy host quá tải.</p>
<h4>Cơ chế:</h4>
<p>Migrate có 2 cơ chế:</p>
<li>Cơ chế Offline Migrate: là cơ chế cần phải tắt guest đi thực hiện việc di chuyển image và file xml của guest sang một host khác Mô hình thuần túy của cơ chế Offline Migrate.</li>
<li>Cơ chế Live Migrate: đây là cơ chế di chuyển guest khi guest vẫn đang hoạt động, quá trình trao đổi diễn ra rất nhanh các phiên làm việc kết nối hầu như không cảm nhận được sự gián đoạn nào. Quá trình Live Migrate được diễn ra như sau: Bước đầu tiên của quá trình Live Migrate 1 ảnh chụp ban đầu của guest trên host1 được chuyển sang host2. Trong trường hợp người dùng đang truy cập tại host1 thì những sự thay đổi và hoạt động trên host1 vẫn diễn ra bình thường, tuy nhiên những thay đổi này sẽ được ghi nhận. Những thay đổi trên host1 được đồng bộ liên tục đến host2 Khi đã đồng bộ xong thì guest trên host1 sẽ offline và các phiên truy cập trên host1 được chuyển sang host2.</li>
