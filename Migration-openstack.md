<h3>1. Giới thiệu về migrate trong Openstack </h3>
<img src="https://github.com/anhict/images/blob/master/migration-1.png">
<p>- Migration là quá trình di chuyển máy ảo từ host vật lí này sang host vật lí khác.Migration được sinh ra để làm nhiệm vụ bảo trì nâng cấp hệ thống.Ngày nay tính năng này được phát triển để thực hiện nhiều tác vụ hơn: </p>
<ul>
<li>Cân bằng tải : Di chuyển VMs tới các host khác khi phát hiện host đang chạy có dấu hiệu quá tải.</li>
<li>Bảo trì,nâng cấp hệ thống: Di chuyển các VMs ra khỏi trước khi tắt nó đi.</li>
<li>Khôi phục lại máy ảo khi host gặp lỗi: Restart máy ảo trên 1 host khác.</li>
</ul>
<p>- Trong Openstack,việc migrate được thực hiện giữa các node compute với nhau hoặc giữa các project trên cùng 1 node compute.</p>
<h3>2. Các kiểu migrate hiện có trong Openstack và workflow của chúng </h3>
<p>Openstack hỗ trợ 2 kiểu migration đó là :
<ul>
<li>Cold migration : Non-live migration</li>
<li>Live migration:</li>
<ul>
<li>True live migration (shared storage or volume-based)</li>
<li>Block live migration</li>
</ul>
</ul>
<h4>2.1 Cold Migrate ( Non-live migrate)</h4>
<p> Migrate khác live migrate ở chỗ nó thực hiện migration ở chỗ nó thực hiện migration khi tắt máy ảo (Libvirt domain không chạy)</p>
<p> Yêu cầu SSH ky pairs được triển khai cho user đang chạy nova-compute với mọi hypervisors.</p>
<p> Migrate workflow:</p>
<ul>
<li>Tắt máy ảo (tương tự `'vish destroy'`) và disconnect các volume.</li>
<li>Di chuyển thực mục hiện hành của máy ảo ra ngoài(instance_dir -> instance_dir_resize).Tiến hành resize instance sẽ tạo ra thư mục tạm thời</Li>
<li>Nếu sử dụng QCOW2,convert image sang định dạng RAW.</li>
<li>Với hệ thống share storage,di chuyển thư mục instance_dir mới vào.Nếu không, copy thông qua scp.</li>
</ul>
<h4> 2.2.Live Migration </h4>


