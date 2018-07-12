<h3>1. Các câu lệnh sử dụng trong LVM</h3>
<p>Đây là những câu lệnh thông dụng để làm việc với LVM</p>
<h4>1.1 Physical volume:</h4>
<p>Câu lệnh với cú pháp pv... đây là những câu lệnh làm việc với physical volume
</p>
<p>Tạo 1 physical volume pvcreate</p>

<p>Hiển thị thuộc tính của physical volume</p>

<pre>pvdisplay</pre>

<p>Điều chỉnh dụng lượng của physical volume</p>
<pre>pvresize</pre>

<p>Xóa physical volume</p>
<pre>pvremove</pre>

<p>Quét tất cả ổ đĩa là physical volume</p>
<pre>pvscan</pre>

<p>Hiển thị các thông số của physical volume</p>
<pre>pvs</pre>
<h4>1.2 Volume group</h4>
<p>Các câu lệnh làm việc với volume group với cú pháp vg.</p>

<p>Tạo volume:</p>
<code>vgcreate [tên các pv để tạo volume group]</code>
<p>Hiển thị các thuộc tính của volume group <code>vgdisplay</code></p>
<p>Mở rộng volume group <code>vgextend [tên pv]</code></p>
<p>Xóa các physical volume ra khỏi volume group <code>vgreduce [tên pv]</code></p>

<p>Xóa volume group <code>vgremove</code> </p>

<p>Xem các thông số của volume group <code>vgs</code></p>
<h4>1.3 Logical volume</h4>
<p>Các câu lệnh làm việc với logical volume với cú pháp lv</p>

<p>Tạo logical volume <code>lvcreate</code></p>
<p>Hiển thị các thuộc tính của logical volume <code>lvdisplay</code></p>

<p>Mở rộng logical volume. Lưu ý: chỉ mở rộng khi volume group còn dung lượng trống <code>lvextend</code></p>

<p>Xóa logical volume khỏi volume group <code>lvremove</code></p>

<p>Thay đổi dung lượng logical volume <code>lvresize</code></p>
<h3.2. Lab LVM</h3>
<p>Sử dụng VMware, HĐH Ubuntu Server 14.04</p>
<p>Tiến hành thêm các ổ cứng vào như hình :</p>
<img src="https://github.com/anhict/images/blob/master/Screenshot_29.png">
<p>Bạn có thể kiểm tra xem có những Hard Drives nào trên hệ thống bằng cách sử dụng câu lệnh lsblk.</p>
<pre>lsblk</pre>
<img src="https://github.com/anhict/images/blob/master/Screenshot_30.png">
