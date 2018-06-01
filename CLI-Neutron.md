
Các lệnh cơ bản thường dùng trong Neutron

<pre>root@controller:~# openstack network list
+--------------------------------------+----------+--------------------------------------+
| ID                                   | Name     | Subnets                              |
+--------------------------------------+----------+--------------------------------------+
| 594a203a-b97a-4fdc-9e65-13859be9abd0 | provider | ac039666-3a6a-4cfb-8f33-7ff83f0156bd |
+--------------------------------------+----------+--------------------------------------+</pre>

<pre>root@controller:~# openstack subnet list
+--------------------------------------+---------------+--------------------------------------+------------------+
| ID                                   | Name          | Network                              | Subnet           |
+--------------------------------------+---------------+--------------------------------------+------------------+
| ac039666-3a6a-4cfb-8f33-7ff83f0156bd | sub1-provider | 594a203a-b97a-4fdc-9e65-13859be9abd0 | 192.168.239.0/24 |
+--------------------------------------+---------------+--------------------------------------+------------------+</pre>

<pre>root@controller:~# openstack security group list
+--------------------------------------+---------+------------------------+----------------------------------+
| ID                                   | Name    | Description            | Project                          |
+--------------------------------------+---------+------------------------+----------------------------------+
| 672039e7-960e-47e1-999c-abc724b2fa70 | default | Default security group | bdf777efa371433ca3bc6f58d51f11d0 |
| a14a11a8-441d-4ff5-9020-a6b373b981f2 | default | Default security group | 7ad160531200440fbcc2e241a20faaee |
+--------------------------------------+---------+------------------------+----------------------------------+</pre>
<pre>root@controller:~# openstack flavor list
+----+---------+-----+------+-----------+-------+-----------+
| ID | Name    | RAM | Disk | Ephemeral | VCPUs | Is Public |
+----+---------+-----+------+-----------+-------+-----------+
| 0  | m1.nano |  64 |    1 |         0 |     1 | True      |
+----+---------+-----+------+-----------+-------+-----------+</pre>
<p>Show thông tin một network</p>
<p>Để xem thông tin chi tiết của network, sử dụng câu lệnh neutron net-show hoặc openstack network show</p>
<pre>root@controller:~# neutron net-show provider
+---------------------------+--------------------------------------+
| Field                     | Value                                |
+---------------------------+--------------------------------------+
| admin_state_up            | True                                 |
| availability_zone_hints   |                                      |
| availability_zones        | nova                                 |
| created_at                | 2018-06-01T07:41:52Z                 |
| description               |                                      |
| id                        | 594a203a-b97a-4fdc-9e65-13859be9abd0 |
| ipv4_address_scope        |                                      |
| ipv6_address_scope        |                                      |
| mtu                       | 1500                                 |
| name                      | provider                             |
| port_security_enabled     | True                                 |
| project_id                | bdf777efa371433ca3bc6f58d51f11d0     |
| provider:network_type     | flat                                 |
| provider:physical_network | provider                             |
| provider:segmentation_id  |                                      |
| revision_number           | 5                                    |
| router:external           | True                                 |
| shared                    | True                                 |
| status                    | ACTIVE                               |
| subnets                   | ac039666-3a6a-4cfb-8f33-7ff83f0156bd |
| tags                      |                                      |
| tenant_id                 | bdf777efa371433ca3bc6f58d51f11d0     |
| updated_at                | 2018-06-01T07:41:54Z                 |
+---------------------------+--------------------------------------+</pre>

<pre>root@controller:~# neutron net-list
+--------------------------------------+----------+----------------------------------+-------------------------------------------------------+
| id                                   | name     | tenant_id                        | subnets                                               |
+--------------------------------------+----------+----------------------------------+-------------------------------------------------------+
| 594a203a-b97a-4fdc-9e65-13859be9abd0 | provider | bdf777efa371433ca3bc6f58d51f11d0 | ac039666-3a6a-4cfb-8f33-7ff83f0156bd 192.168.239.0/24 |
+--------------------------------------+----------+----------------------------------+-------------------------------------------------------+</pre>


<pre>root@controller:~# neutron agent-list
+--------------------------------------+--------------------+------------+-------------------+-------+----------------+---------------------------+
| id                                   | agent_type         | host       | availability_zone | alive | admin_state_up | binary                    |
+--------------------------------------+--------------------+------------+-------------------+-------+----------------+---------------------------+
| 4759c4f7-73d9-43d4-ab5b-b449766bf099 | Linux bridge agent | compute1   |                   | :-)   | True           | neutron-linuxbridge-agent |
| 4dc6ba32-7513-40f5-ba16-e332626dc46e | DHCP agent         | controller | nova              | :-)   | True           | neutron-dhcp-agent        |
| 853d2bec-26b2-4557-9524-895721ceaa12 | Linux bridge agent | controller |                   | :-)   | True           | neutron-linuxbridge-agent |
| a669c5a2-74e7-471c-b189-18c783e13760 | Metadata agent     | controller |                   | :-)   | True           | neutron-metadata-agent    |
+--------------------------------------+--------------------+------------+-------------------+-------+----------------+---------------------------+</pre>


