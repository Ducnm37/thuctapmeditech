<h3>1. Quản lý flavor </h3>
<p>Instance flavor là template của máy ảo và nó chỉ ra máy ảo thuộc loại nào. Ngay sau khi cài đặt OpenStack cloud, người dùng sẽ có trước một vài các flavor. Bạn có thể thêm hoặc xóa các flavor có sẵn.</p>
<table>
<thead>
<tr>
<th>Element</th>
<th>Description</th>
</tr>
</thead>
<tbody>
<tr>
<td>Name</td>
<td>Tên mô tả</td>
</tr>
<tr>
<td>Memory MB</td>
<td>RAM máy ảo (megabytes)</td>
</tr>
<tr>
<td>Disk</td>
<td>Ổ đĩa máy ảo (gigabytes). Đây là ephemeral disk mà base image được copy lên. Khi boot từ volume thì nó không được sử dụng</td>
</tr>
<tr>
<td>Ephemeral</td>
<td>Kích thước của ephemeral data disk số 2. Đây là đĩa trống, chưa được format và chỉ tồn tại khi máy ảo chạy</td>
</tr>
<tr>
<td>Swap</td>
<td>Đây là tùy chọn cho swap của máy ảo. Giá trị mặc định là 0</td>
</tr>
<tr>
<td>VCPUs</td>
<td>Số lượng CPUs ảo của máy ảo</td>
</tr>
<tr>
<td>Is Public</td>
<td>Quy định flavor có thể được dùng bởi tất cả các user hay chỉ những user trong project nào đó</td>
</tr>
<tr>
<td>Extra Specs</td>
<td>Quy định flavor được dùng trên node compute nào</td>
</tr></tbody></table>
<p>Tạo 1 flavor </p>
<pre>
root@controller:~# openstack flavor create --id 1 --vcpus 1 --ram 64 --disk 1 m1.nano1
+----------------------------+----------+
| Field                      | Value    |
+----------------------------+----------+
| OS-FLV-DISABLED:disabled   | False    |
| OS-FLV-EXT-DATA:ephemeral  | 0        |
| disk                       | 1        |
| id                         | 1        |
| name                       | m1.nano1 |
| os-flavor-access:is_public | True     |
| properties                 |          |
| ram                        | 64       |
| rxtx_factor                | 1.0      |
| swap                       |          |
| vcpus                      | 1        |
+----------------------------+----------+
root@controller:~#</pre>
<p>- Hiển thị danh sách các flavor </p>
<pre>root@controller:~# openstack flavor list
+----+---------+-----+------+-----------+-------+-----------+
| ID | Name    | RAM | Disk | Ephemeral | VCPUs | Is Public |
+----+---------+-----+------+-----------+-------+-----------+
| 0  | m1.nano |  64 |    1 |         0 |     1 | True      |
+----+---------+-----+------+-----------+-------+-----------+
root@controller:~#</pre>
<p>- Hiển thị thông tin chi tiết 1 flavor</p>
<pre>root@controller:~# openstack flavor show m1.nano
+----------------------------+---------+
| Field                      | Value   |
+----------------------------+---------+
| OS-FLV-DISABLED:disabled   | False   |
| OS-FLV-EXT-DATA:ephemeral  | 0       |
| access_project_ids         | None    |
| disk                       | 1       |
| id                         | 0       |
| name                       | m1.nano |
| os-flavor-access:is_public | True    |
| properties                 |         |
| ram                        | 64      |
| rxtx_factor                | 1.0     |
| swap                       |         |
| vcpus                      | 1       |
+----------------------------+---------+
root@controller:~#</pre>
<p>Tạo mới flavor với tên m10.tiny có 5GB disk, 400MB RAM và 1 vCPU, sử dụng câu lệnh </p>
<pre>root@controller:~# nova flavor-create --is-public true m10.tiny auto 400 5 1
+--------------------------------------+----------+-----------+------+-----------+------+-------+-------------+-----------+
| ID                                   | Name     | Memory_MB | Disk | Ephemeral | Swap | VCPUs | RXTX_Factor | Is_Public |
+--------------------------------------+----------+-----------+------+-----------+------+-------+-------------+-----------+
| 098c0f63-9626-4879-b654-566ce22f099a | m10.tiny | 400       | 5    | 0         |      | 1     | 1.0         | True      |
+--------------------------------------+----------+-----------+------+-----------+------+-------+-------------+-----------+
root@controller:~#</pre>

