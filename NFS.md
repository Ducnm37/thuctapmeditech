<h3>Network Filesystem</h3>
<p>NFS (the Network File System) là một giao thức được dùng cho việc chia sẻ data qua physical systems. Người quản trị gắn các thư mục của người dùng từ xa trên một máy chủ để cho phép họ truy cập vào cùng một tệp và cấu hình.</p>
<p>Hoạt động theo cơ chế client-server</p>
<p>Một số vấn đề với NFS</p>
<ul>
<li>Không bảo mật, mã hóa dữ liệu</li>
<li>Hiệu suất hoạt động trung bình ở mức khá, nhưng không ổn định</li>
<li>Dữ liệu phân tán có thể bị phá vỡ nếu có nhiều phiên sử dụng đồng thời</li>
</ul>
<p>File <code>/etc/export</code> chứa các đường dẫn thư mục và quyền hạn mà một host muốn chia sẻ dữ liệu với host khác qua NSF.</p>
<p>Các máy chủ có quyền hạn sau:</p>
<ul>
<li><code>rw</code>: Đọc và ghi</li>
<li><code>ro</code>: Chỉ được đọc</li>
<li><code>noacess</code>: Cấm truy cập vào các thư mục con của thư mục đc chia sẻ</li>
</ul>
<p>Ví dụ bạn muốn chia sẻ thư mục <code>/share</code> cho các máy có địa chỉ trong 192.168.1.1/28 có quyền đọc ghi thì thêm vào nội dung file dòng sau:</p>
<pre><code>/Share 192.168.1.1/28(rw)
</code></pre>
<h3>Cài đặt trên Ubuntu</h3>
