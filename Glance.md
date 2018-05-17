<h3> Quản lý các images </h3>
<h4>Hiển thị danh sách image </h4>
<p><li> Để liệt kê danh sách các images sử dụng lệnh : glance image-list </li></p>
<pre>root@controller:~# glance image-list
+--------------------------------------+--------+
| ID                                   | Name   |
+--------------------------------------+--------+
| d1019cd9-8dbf-4a0b-a33b-070798c85dab | cirros |
+--------------------------------------+--------+
root@controller:~#</pre>
<p><li>Hiển thị chi tiết thông tin của 1 image sử dụng lệnh: glance image-show image_id</li></p>
<pre>root@controller:~# glance image-show d1019cd9-8dbf-4a0b-a33b-070798c85dab
+------------------+--------------------------------------+
| Property         | Value                                |
+------------------+--------------------------------------+
| checksum         | f8ab98ff5e73ebab884d80c9dc9c7290     |
| container_format | bare                                 |
| created_at       | 2018-05-11T07:46:08Z                 |
| disk_format      | qcow2                                |
| id               | d1019cd9-8dbf-4a0b-a33b-070798c85dab |
| min_disk         | 0                                    |
| min_ram          | 0                                    |
| name             | cirros                               |
| owner            | 74455f45a933413f81d9c36751c9dfd0     |
| protected        | False                                |
| size             | 13267968                             |
| status           | active                               |
| tags             | []                                   |
| updated_at       | 2018-05-11T07:46:09Z                 |
| virtual_size     | None                                 |
| visibility       | public                               |
+------------------+--------------------------------------+</pre>

<h4> Tạo image </h4>
<p>Sử dụng lệnh :</p>
<pre>openstack image create "cirros" \
  --file cirros-0.3.5-x86_64-disk.img \
  --disk-format qcow2 --container-format bare \
  --public</pre>
<pre>root@controller:~# openstack image create "cirros1" \
>   --file cirros-0.3.5-x86_64-disk.img \
>   --disk-format qcow2 --container-format bare \
>   --public
+------------------+------------------------------------------------------+
| Field            | Value                                                |
+------------------+------------------------------------------------------+
| checksum         | f8ab98ff5e73ebab884d80c9dc9c7290                     |
| container_format | bare                                                 |
| created_at       | 2018-05-17T09:29:32Z                                 |
| disk_format      | qcow2                                                |
| file             | /v2/images/99c50513-9d32-47cf-b4bf-7eec42339621/file |
| id               | 99c50513-9d32-47cf-b4bf-7eec42339621                 |
| min_disk         | 0                                                    |
| min_ram          | 0                                                    |
| name             | cirros1                                              |
| owner            | 74455f45a933413f81d9c36751c9dfd0                     |
| protected        | False                                                |
| schema           | /v2/schemas/image                                    |
| size             | 13267968                                             |
| status           | active                                               |
| tags             |                                                      |
| updated_at       | 2018-05-17T09:29:33Z                                 |
| virtual_size     | None                                                 |
| visibility       | public                                               |
+------------------+------------------------------------------------------+</pre>
<h4>Upload image</h4>
<p>Trong trường hợp ta tạo ra một image mới và rỗng, ta cần upload dữ liệu cho nó, sử dụng câu lệnh: glance image-upload --file file_name image_id</p>
<pre>root@controller:~# glance image-create --name cirros2 --container bare --disk-format qcow2
+------------------+--------------------------------------+
| Property         | Value                                |
+------------------+--------------------------------------+
| checksum         | None                                 |
| container_format | bare                                 |
| created_at       | 2018-05-17T09:32:29Z                 |
| disk_format      | qcow2                                |
| id               | d8be6c30-ad58-48ba-8c54-a406206f6b1f |
| min_disk         | 0                                    |
| min_ram          | 0                                    |
| name             | cirros2                              |
| owner            | 74455f45a933413f81d9c36751c9dfd0     |
| protected        | False                                |
| size             | None                                 |
| status           | queued                               |
| tags             | []                                   |
| updated_at       | 2018-05-17T09:32:29Z                 |
| virtual_size     | None                                 |
| visibility       | shared                               |
+------------------+--------------------------------------+</pre>


 
