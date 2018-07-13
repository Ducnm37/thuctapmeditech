<h3>1. Giới thiệu Gluster FS</h3>
<h4>1.1 Giới thiệu Gluster FS</h4>
<ul>
<li>
<p>GlusterFS là một open source, là tập hợp file hệ thống có thể được nhân rộng tới vài peta-byte và có thể xử lý hàng ngàn Client.</p>
</li>
<li>
<p>GlusterFS có thể linh hoạt kết hợp với các thiết bị lưu trữ vật lý, ảo, và tài nguyên điện toán đám mây để cung cấp 1 hệ thống lưu trữ có tính sẵn sàng cao và khả năng performant cao .</p>
</li>
<li>
<p>Chương trình có thể lưu trữ dữ liệu trên các mô hình, thiết bị khác nhau, nó kết nối với tất cả các nút cài đặt GlusterFS qua giao thức TCP hoặc RDMA tạo ra một nguồn tài nguyên lưu trữ duy nhất kết hợp tất cả các không gian lưu trữ có sẵn thành một khối lượng lưu trữ duy nhất (distributed mode) hoặc sử dụng tối đa không gian ổ cứng có sẵn trên tất cả các ghi chú để nhân bản dữ liệu của bạn (replicated mode).</p>
</li>
</ul>
<h4>1.2 Thuật ngữ trong Gluster FS</h4>
<p>Để có thể hiểu rõ về GlusterFS và ứng dụng được sản phẩm này, trước hết ta cần phải biết rõ những khái niệm có trong GlusterFS. Sau đây là những khái niệm quan trọng khi sử dụng Glusterfs</p>
<ul>
<li><b>Trusted Storage Pool</b>: Trong một hệ thống GlusterFS, những server dùng để lưu trữ được gọi là những node, và những node này kết hợp lại với nhau thành một không gian lưu trữ lớn được gọi là Pool. Dưới đây là mô hình kết nối giữa 2 node thành một Trusted Storage Pool.</li>
</ul>
<img src="https://github.com/anhict/images/blob/master/687474703a2f2f692e696d6775722e636f6d2f57757842536e5a2e706e67.png">
<ul>
<li><b>Brick</b>:</li>
</ul>
<ul>
<li>Từ những phần vùng lưu trữ mới (những phân vùng chưa dùng đến) trên mỗi node, chúng ta có thể tạo ra những brick.</li>
<li>Brick được định nghĩa bởi 1 server (name or IP) và 1 đường dẫn. Vd: 10.10.10.20:/mnt/brick (đã mount 1 partition (/dev/sdb1) vào /mnt)</li>
<li>Mỗi brick có dung lượng bị giới hạn bởi filesystem....</li>
<li>Trong mô hình lý tưởng, mỗi brick thuộc cluster có dung lượng bằng nhau. Để có thể hiểu rõ hơn về Bricks, chúng ta có thể tham khảo hình dưới đây:</li>
<img src="https://github.com/anhict/images/blob/master/687474703a2f2f692e696d6775722e636f6d2f7645766d304a372e706e67.png"> 
</ul>
<ul>
<li><b>Volume</b>:</li>
</ul>
<ul>
<li>Từ những brick trên các node thuộc cùng một Pool, kết hợp những brick đó lại thành một không gian lưu trữ lớn và thống nhất để client có thể mount đến và sử dụng.</li>
<li>Một volume là tập hợp logic của các brick. Tên volume được chỉ định bởi administrator</li>
<li>Volume được mount bởi client: mount -t glusterfs server1:/ /my/mnt/point </li>
<li>Một volume có thể chứa các brick từ các node khác nhau. Sau đây là mô hình tập hợp những Brick thành Volume:</li>
</ul>
<img src="https://github.com/anhict/images/blob/master/687474703a2f2f692e696d6775722e636f6d2f53676f6c5654712e706e67.png">
<p>Tại hình trên, chúng ta có thể thấy mỗi Node1, Node2, Node3 đã tạo 2 brick là /export/brick1 và /export/brick2, và từ 3 brick /export/brick1trên 3 Node tập hợp lại tạo thành volume music. Tương tự 3 brick /export/brick2 trên 3 Node tập hợp lại tạo thành volume Videos.</p>
<h3>1.3 Một số loại volume cơ bản</h3>
<p>Khi sử dụng GlusterFS có thể tạo nhiều loại volume và mỗi loại có được những tính năng khác nhau. Dưới đây là 5 loại volume cơ bản</p>
<strong>Distributed volume:</strong>
<p>Distributed Volume có những đặc điểm cơ bản sau:</p>
<p>Dữ liệu được lưu trữ phân tán trên từng bricks, file1 nằm trong brick 1, file 2 nằm trong brick 2,...</p>
<p>Vì metadata được lưu trữ trực tiếp trên từng bricks nên không cần thiết phải có một metadata server ở bên ngoài, giúp cho các tổ chức tiết kiệm được tài nguyên.</p>
<p>Ưu điểm: mở rộng được dung lượng store ( dung lượng store bằng tổng dung lượng các brick)</p>
<p>Nhược điểm: nếu 1 trong các brick bị lỗi, dữ liệu trên brick đó sẽ mất</p>
<img src="https://github.com/anhict/images/blob/master/687474703a2f2f692e696d6775722e636f6d2f5a41366438664f2e706e67.png">
<strong>Replicated volume:</strong>
<p>Dữ liệu sẽ được nhân bản đến những brick còn lại, trên tất cả các node và đồng bộ tất cả các nhân bản mới cập nhật.</p>
<p>Đảm bảo tính nhất quán.</p>
<p>Không giới hạn số lượng replicas.</p>
<p>Ưu điểm: phù hợp với hệ thống yêu cầu tính sẵn sàng cao và dự phòng</p>
<p>Nhược điểm: tốn tài nguyên hệ thống</p>
<img src="https://github.com/anhict/images/blob/master/687474703a2f2f692e696d6775722e636f6d2f48396d73424e482e706e67.png">
<strong>Stripe volume:</strong>
<p>Dữ liệu chia thành những phần khác nhau và lưu trữ ở những brick khác nhau, ( 1 file được chia nhỏ ra trên các brick )</p>
<p>Ưu điểm : phù hợp với những môi trường yêu cầu hiệu năng, đặc biệt truy cập những file lớn.</p>
<p>Nhược điểm: 1 brick bị lỗi volume không thể hoạt động được.</p>
<img src="https://github.com/anhict/images/blob/master/687474703a2f2f692e696d6775722e636f6d2f6e50496e59656e2e706e67.png">
<strong>Distributed replicated:</strong>
<p>Kết hợp từ distributed và replicated</p>

