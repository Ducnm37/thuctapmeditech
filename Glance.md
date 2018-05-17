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
<h4>Xóa image</h4>
<p> Để xóa image, ta sử dụng câu lệnh : glance image-delete image_id </p>
<pre>root@controller:~# glance image-list
+--------------------------------------+---------+
| ID                                   | Name    |
+--------------------------------------+---------+
| d1019cd9-8dbf-4a0b-a33b-070798c85dab | cirros  |
| 95502eef-fadf-4f58-ad5f-bd3872822ed7 | cirros  |
| 99c50513-9d32-47cf-b4bf-7eec42339621 | cirros1 |
| d8be6c30-ad58-48ba-8c54-a406206f6b1f | cirros2 |
+--------------------------------------+---------+
root@controller:~# glance image-delete d8be6c30-ad58-48ba-8c54-a406206f6b1f
root@controller:~# glance image-list
+--------------------------------------+---------+
| ID                                   | Name    |
+--------------------------------------+---------+
| d1019cd9-8dbf-4a0b-a33b-070798c85dab | cirros  |
| 95502eef-fadf-4f58-ad5f-bd3872822ed7 | cirros  |
| 99c50513-9d32-47cf-b4bf-7eec42339621 | cirros1 |
+--------------------------------------+---------+</pre>
<h4> Thay đổi trạng thái máy ảo </h4>
<p>Như ta đã biết một image upload thành công sẽ ở trạng thái active, người dùng có thể đưa nó về trạng thái deactivate cũng như thay đổi qua lại giữa hai trạng thái bằng câu lệnh :<code> glance image-deactivate <IMAGE_ID>></code> và <code>glance image-reactivate <IMAGE_ID></code><p>
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
+------------------+--------------------------------------+
root@controller:~#</pre>
 <h3> Sử dụng OpenStack client </h3>
 <p>Giống với glance command line, OpenStack client sử dụng câu lệnh để quản lí glance. Bảng sau đây thể hiện mối quan hệ tương tác giữa hai câu lệnh trên:</p>
  <img src="https://camo.githubusercontent.com/17dbcf7c070792e453760ff7c2a5f99693807740/687474703a2f2f692e696d6775722e636f6d2f65484c753253732e706e67">
  <p> Xem thêm về OpenStack client cho glance tại link sau:
    https://docs.openstack.org/python-openstackclient/latest/</p>
  <p>Cách kiểm tra thông tin trong database</p>
  <p>Bạn có thể connect rồi query ra để xem để hiển thị nội dung database:</p>
  <pre>
root@controller:~# mysql -u root -pWelcome123
Welcome to the MariaDB monitor.  Commands end with ; or \g.
Your MariaDB connection id is 61
Server version: 10.0.34-MariaDB-0ubuntu0.16.04.1 Ubuntu 16.04

Copyright (c) 2000, 2018, Oracle, MariaDB Corporation Ab and others.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

MariaDB [(none)]> connect glance
Reading table information for completion of table and column names
You can turn off this feature to get a quicker startup with -A

Connection id:    62
Current database: glance

MariaDB [glance]> select * from image_locations;
+----+--------------------------------------+--------------------------------------------------------------------+---------------------+---------------------+------------+---------+-----------+--------+
| id | image_id                             | value                                                              | created_at          | updated_at          | deleted_at | deleted | meta_data | status |
+----+--------------------------------------+--------------------------------------------------------------------+---------------------+---------------------+------------+---------+-----------+--------+
|  1 | d1019cd9-8dbf-4a0b-a33b-070798c85dab | file:///var/lib/glance/images/d1019cd9-8dbf-4a0b-a33b-070798c85dab | 2018-05-11 07:46:09 | 2018-05-11 07:46:09 | NULL       |       0 | {}        | active |
|  2 | 95502eef-fadf-4f58-ad5f-bd3872822ed7 | file:///var/lib/glance/images/95502eef-fadf-4f58-ad5f-bd3872822ed7 | 2018-05-17 09:29:03 | 2018-05-17 09:29:03 | NULL       |       0 | {}        | active |
|  3 | 99c50513-9d32-47cf-b4bf-7eec42339621 | file:///var/lib/glance/images/99c50513-9d32-47cf-b4bf-7eec42339621 | 2018-05-17 09:29:33 | 2018-05-17 09:29:33 | NULL       |       0 | {}        | active |
+----+--------------------------------------+--------------------------------------------------------------------+---------------------+---------------------+------------+---------+-----------+--------+
3 rows in set (0.00 sec)

MariaDB [glance]></pre>



 
