<h3>1. Tổng quan về cloud init </h3>
<p>Cloud-init là một phương pháp được sử dụng rộng rãi để tùy chỉnh một máy ảo Linux khi nó khởi động lần đầu tiên. Bạn có thể sử dụng cloud-init để cài đặt các gói và ghi các tập tin, hoặc để cấu hình người dùng và bảo mật. Bởi vì cloud-init được gọi trong quá trình khởi động ban đầu, không có bước bổ sung hoặc các agents cần thiết để áp dụng cấu hình của bạn.</p>
<p>Các tác động mà cloud-init thực hiện phụ thuộc vào loại format thông tin mà nó tìm kiếm được. Các format hỗ trợ:</p>
<ul>
<li>Shell scripts (bắt đầu với #!)</li>
<li>Cloud config files (bắt đầu với #cloud-config)</li>
<li>MIME multipart archive.</li>
<li>Gzip Compressed Content</li>
<li>Cloud Boothook</li>
</ul>
<p>Một trong những định dạng thông dụng nhất dành cho các scripts đó là <code>cloud-config</code>.</p>
