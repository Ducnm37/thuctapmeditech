<h3>1. Tổng quan về Openstack metadata service </h3>
<p>Openstask sử dụng Metadata Service để thêm các tùy chỉnh đến các Instances thông qua Network (Neutron). Ví dụ như ta muốn thêm ssh keys, passwords, hostnames, hoặc các scripts tới Instances. Nếu đứng từ góc độ người dùng, Metadata Service giúp bất kỳ client nào trong Openstack Instances có thể lấy được các thông tin của chính mình như:</p>
<p>– Thông tin về Public IP/Hostname</p>
<p>– Thông tin về SSH Public Keys</p>
<p>Chúng ta biết rằng máy ảo OpenStack được khởi tạo thông qua cloud-init, chẳng hạn như cấu hình card mạng, tên máy, password. Cloud-init là một tiến trình chạy bên trong một máy ảo. Nó thu thập thông tin cấu hình (metadata) của máy ảo thông qua datasource. Cloud-init triển khai nhiều nguồn dữ liệu khác nhau và các nguyên tắc triển khai nguồn dữ liệu khác nhau khác nhau. Có hai nguồn dữ liệu chính thường được sử dụng:</p>
<p>ConfigDriver: Nova ghi tất cả thông tin cấu hình vào một raw file và gắn nó vào máy ảo thông qua cdrom. Tại thời điểm này, bạn có thể thấy / dev / sr0a thiết bị ảo tương tự như bên trong của máy ảo (Lưu ý: sr là viết tắt của scsi + rom). Cloud-init chỉ cần đọc thông tin / dev / sr0file để lấy thông tin cấu hình máy ảo.</p>
<p>Metadata: Nova bắt đầu dịch vụ siêu dữ liệu HTTP local. Máy ảo chỉ cần truy cập dịch vụ siêu dữ liệu qua HTTP để lấy thông tin cấu hình máy ảo liên quan</p>
<p>Metadata giải quyết được các vấn đề </p>
<p>Dịch vụ Metadata Nova được bắt đầu trên máy chủ (nút điều khiển nơi đặt nova-api). Mạng vật lý của tenant network và máy chủ bên trong máy ảo bị ngắt kết nối. Máy ảo truy cập dịch vụ siêu dữ liệu của Nova như thế nào?</p>
<p>Làm thế nào để dịch vụ metadata Nova biết VM nào đã khởi tạo yêu cầu.<p>
<h3>2. Metadata service configuration</h3>
<h4>2.1 Cấu hình Nova</h4>
<p>Tên dịch vụ metadata của Nova là nova-api-metadata, nhưng dịch vụ thường được hợp nhất với dịch vụ nova-api:</p>
<pre>[DEFAULT]
enabled_apis = osapi_compute,metadata</pre>
<p>Ngoài ra, máy ảo để truy cập dịch vụ metadata của Nova cần Neutron để chuyển tiếp. Vì lý do này, ở đây chúng ta cần cấu hình file nova.conf :</p>
<pre>[neutron]
service_metadata_proxy = true</pre>
<h4>2.2 Cấu hình Neutron </h4>
<p>Máy ảo truy cập dịch vụ metadata của Nova cần Neutron để chuyển tiếp nó, và nó có thể được chuyển tiếp bởi L3 agent hoặc được chuyển tiếp bởi DHCP agent. Có 2 lựa chọn:</p>
<p>Thông qua chuyển tiếp l3-agent,mạng mà VM sử dụng được kết hợp với Router.</p>
<p>Với chuyển tiếp DHCP agent, chức năng dhcp phải được kích hoạt trên mạng nơi máy ảo được cấu hình.</p>
<p>Metadata được chuyển tiếp bởi L3-agent theo mặc định, nhưng trong thực tế, chức năng dhcp thường được bật trên mạng của máy ảo và không cần thiết phải sử dụng bộ định tuyến. Vì vậy,có thể sử dụng dhcp-agent để chuyển tiếp các gói tin. Cấu hình như sau:</p>
<pre># /etc/neutron/dhcp_agent.ini [DEFAULT]
force_metadata = true
# /etc/neuron/l3_agent.ini [DEFAULT]
enable_metadata_proxy = false</pre>
<h3>3. Làm thế nào Openstack VMs truy cập service metadata </h3>
<h4>3.1 Truy cập metadata từ VM </h4>
<p>Cloud-init truy cập vào địa chỉ URL của dịch vụ metadata là URL address,đây là địa chỉ đặc biệt AWS's Metadata service, cụ thể là địa chỉ IPv4 Link Local IP private (10.0.0.0/8, 172.16.0.0/12, 192.168.0.0/16), nó không thể được sử dụng để định tuyến Internet. Nó thường chỉ được sử dụng cho các mạng kết nối trực tiếp. Nếu hệ điều hành (Windows) không nhận được IP, nó có thể được cấu hình tự động như là một địa chỉ IP của phân đoạn mạng.http: //169.254.169.254 169.254.0.0/16 169.254.0.0/16</p>
<p>Tại sao AWS chọn IP 169.254.169.254? Điều này là do việc chọn Link Local IP có thể tránh xung đột IP với người dùng. Tại sao lại chọn 169.254.169.254 IP này thay vì IP khác 169.254.0.0/24, có lẽ do nó dễ nhớ.</p>
<p>Ngoài ra AWS có một số địa chỉ rất thú vị:</p>
<pre>169.254.169.253: DNS service.
169.254.169.123: NTP service.</pre>
<p>Máy ảo OpenStack cũng thu thập thông tin cấu hình ban đầu của máy ảo: http://169.254.169.254</p>
<pre>curl http://169.254.169.254/2009-04-04/meta-data</pre>
<p>chúng ta có thể thấy từ service metadata, chúng ta có được uuid, hostname, project ID, availability_zone... của máy ảo.</p>
<p>Làm cách nào một máy ảo có được thông tin metadata bằng cách truy cập địa chỉ này 169.254.169.254? Trước tiên hãy xem bảng định tuyến cho máy ảo tiếp theo:</p>
<pre># route -n
Kernel IP routing table
Destination     Gateway         Genmask         Flags Metric Ref    Use Iface
0.0.0.0         10.0.0.126      0.0.0.0         UG    0      0        0 eth0
10.0.0.64       0.0.0.0         255.255.255.192 U     0      0        0 eth0
169.254.169.254 10.0.0.66       255.255.255.255 UGH   0      0        0 eth0</pre>
<p>Chúng ta có thể thấy rằng next hop tiếp theo của 169.254.169.254 là 10.0.0.66. IP của 10.0.0.66 là gì? Chúng tôi xem xét thông tin về cổng của Neutron:</p>
<pre># neutron port-list -c network_id -c device_owner -c mac_address -c fixed_ips -f csv | grep 10.0.0.66
"2c4b658c-f2a0-4a17-9ad2-c07e45e13a8a","network:dhcp","fa:16:3e:b3:e8:38","[{u'subnet_id': u'6f046aae-2158-4882-a818-c56d81bc8074', u'ip_address': u'10.0.0.66'}]"</pre>
<pre>Bạn có thể thấy rằng 10.0.0.66 xảy ra là địa chỉ dhcp của mạng 2c4b658c-f2a0-4a17-9ad2-c07e45e13a8a, có thể được xác minh thêm:</pre>
<pre># ip netns exec qdhcp-2c4b658c-f2a0-4a17-9ad2-c07e45e13a8a ifconfig
tap1332271e-0d: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1450
        inet 10.0.0.66  netmask 255.255.255.192  broadcast 10.0.0.127
        inet6 fe80::f816:3eff:feb3:e838  prefixlen 64  scopeid 0x20<link>
        ether fa:16:3e:b3:e8:38  txqueuelen 1000  (Ethernet)
        RX packets 662  bytes 58001 (56.6 KiB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 410  bytes 55652 (54.3 KiB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0</pre>
<p>Vì vậy, chúng ta có thể kết luận rằng truy cập máy ảo OpenStack 169.254.169.254 sẽ được định tuyến đến địa chỉ DHCP của mạng nơi máy ảo được đặt. Địa chỉ DHCP và IP máy ảo phải tương thích với nhau, do đó giải quyết bên trong máy ảo với bên ngoài máy chủ. Vấn đề giao tiếp. DHCP chuyển tiếp đến dịch vụ metadata Nova như thế nào? Phần tiếp theo mô tả cách giải quyết vấn đề này.</p>     
<h4>3.2 Requets metadata được chuyển tiếp lần đầu tiên</h4>
<p>Máy ảo truy cập địa chỉ dịch vụ metadata 169.254.169.254 và sau đó chuyển tiếp nó đến địa chỉ DHCP. Chúng ta biết rằng cổng DHCP của Neutron được đặt trong namespace, chúng ta có thể nhập vào namespace của mạng nơi máy ảo được đặt:</p>
<pre>ip netns exec qdhcp-2c4b658c-f2a0-4a17-9ad2-c07e45e13a8a bash</pre>
<p>Đầu tiên hãy nhìn vào route của namespace:</p>
<pre># route -n
Kernel IP routing table
Destination     Gateway         Genmask         Flags Metric Ref    Use Iface
0.0.0.0         10.0.0.126      0.0.0.0         UG    0      0        0 tap1332271e-0d
10.0.0.64       0.0.0.0         255.255.255.192 U     0      0        0 tap1332271e-0d
169.254.0.0     0.0.0.0         255.255.0.0     U     0      0        0 tap1332271e-0d</pre>
<p>Nhìn từ bảng định tuyến 169.254.0.0/16is tap1332271e-0dsent từ card mạng, chúng ta xem thông tin địa chỉ của card mạng:</p>
<pre># ip a
18: tap1332271e-0d: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1450 qdisc noqueue state UNKNOWN qlen 1000
    link/ether fa:16:3e:b3:e8:38 brd ff:ff:ff:ff:ff:ff
    inet 10.0.0.66/26 brd 10.0.0.127 scope global tap1332271e-0d
       valid_lft forever preferred_lft forever
    inet 169.254.169.254/16 brd 169.254.255.255 scope global tap1332271e-0d
       valid_lft forever preferred_lft forever
    inet6 fe80::f816:3eff:feb3:e838/64 scope link
       valid_lft forever preferred_lft forever</pre>
<p>Chúng ta thấy rằng 169.254.169.254 thực sự là cặp virtual IP paired tap1332271e-0da được ghép nối với thẻ mạng. Không ngạc nhiên khi một máy ảo có thể truy cập địa chỉ 169.254.169.254. Cần lưu ý rằng cấu hình chuyển tiếp metadate của bài viết này được thực hiện bởi DHCP-agent. Nếu nó là L3-agent, 169.254.169.254 được chuyển tiếp bởi iptables.</p>      
<p>Chúng ta có thể truy cập và biết rằng địa chỉ này chắc chắn mở port 80: curl http://169.254.169.254</p>
<pre># netstat -lnpt
Proto Recv-Q Send-Q Local Address           Foreign Address         State       PID/Program name
tcp        0      0 0.0.0.0:80              0.0.0.0:*               LISTEN      11334/haproxy
tcp        0      0 10.0.0.66:53            0.0.0.0:*               LISTEN      11331/dnsmasq
tcp        0      0 169.254.169.254:53      0.0.0.0:*               LISTEN      11331/dnsmasq
tcp6       0      0 fe80::f816:3eff:feb3:53 :::*                    LISTEN      11331/dnsmasq</pre>
<p>Ngoài dịch vụ DHCP (cổng 53), chương trình thực sự đang quét trên cổng 80, pid xử lý là 11334/haproxy.<p>
<p>Máy ảo OpenStack đầu tiên sẽ chuyển tiếp yêu cầu tới cổng nghe haproxy 80 của namespace DHCP 11334/haproxy.</p>
<p>Mạng namespace nơi DHCP được đặt vẫn không tương thích với metadata Nova. Haproxy yêu cầu chuyển tiếp đến dịch vụ Nova metadata như thế nào?</p>
<h4>3.3 Metadata gửi request lần thứ 2</h4>
<p>Trước đó chúng ta đã giới thiệu rằng truy cập máy ảo OpenStack sẽ được chuyển tiếp đến cổng 80 haproxy của namespace DHCP được đặt. Tuy nhiên, dịch vụ Nova metadata vẫn không thể truy cập được trong namespace .http: //169.254.169.254</p>
<p>Thông tin của quá trình haproxy này:</p>
<pre>cat /proc/11334/cmdline | tr '\0' ' '
haproxy -f /opt/stack/data/neutron/ns-metadata-proxy/2c4b658c-f2a0-4a17-9ad2-c07e45e13a8a.conf</pre>
<p>Trong phần cấu hình 2c4b658c-f2a0-4a17-9ad2-c07e45e13a8a.conf như sau:</p>
<pre>listen listener
    bind 0.0.0.0:80
    server metadata /opt/stack/data/neutron/metadata_proxy
    http-request add-header X-Neutron-Network-ID 2c4b658c-f2a0-4a17-9ad2-c07e45e13a8a</pre>
<p>Chúng ta thấy rằng cổng liên kết haproxy là 80 và địa chỉ back-end là một file /opt/stack/data/neutron/metadata_proxy. Phần cuối không phải là địa chỉ IP/TCP, nó phải là một tệp UNIX Socket:</p>
<pre># ll /opt/stack/data/neutron/metadata_proxy
srw-r--r-- 1 stack stack 0 Jul  1 13:30 /opt/stack/data/neutron/metadata_proxy</pre>
<p>Do đó, chúng ta kết luận rằng quá trình haproxy sẽ chuyển tiếp các yêu cầu metadata máy ảo OpenStack tới một tệp socket cục bộ.<p>
<p>UNIX Domain Socket là một giao tiếp interprocess (IPC) được phát triển trên kiến trúc socket cho cùng một máy chủ. Nó không cần phải sao chép dữ liệu lớp ứng dụng từ một tiến trình này sang tiến trình khác thông qua ngăn xếp giao thức mạng. Nó tương tự như  Unix pipeline.</p>
<p>Vấn đề tiếp theo:</p>
<p>- Chúng ta thấy từ cấu hình haproxy, địa chỉ nghe là 0.0.0.0:80, nếu có nhiều mạng lắng nghe trên cổng 80 cùng một lúc, nó không phải là một xung đột cổng?</p>
<p>- Sockets chỉ có thể được sử dụng cho truyền thông liên tiến trình trên cùng một máy chủ. Nếu dịch vụ Nova metadata và DHCP Neutron agent không nằm trên cùng một máy chủ, rõ ràng là không thể giao tiếp được.</p>
<p>Vấn đề đầu tiên thực sự đã được giải quyết Haproxy được khởi động trong DHCP namespace của mạng nơi máy ảo cư trú. Chúng ta có thể xác minh rằng:</p>
<pre># lsof -i :80
COMMAND   PID  USER   FD   TYPE   DEVICE SIZE/OFF NODE NAME
haproxy 11334 stack    4u  IPv4 65729753      0t0  TCP *:http (LISTEN)
# ip netns identify 11334
qdhcp-2c4b658c-f2a0-4a17-9ad2-c07e45e13a8a</pre>
<p>Ngoài ra, cần lưu ý rằng phiên bản mới của OpenStack được chuyển tiếp trực tiếp bằng cách sử dụng  haproxy agent. Trong một số phiên bản cũ hơn, neutron-ns-metadata-proxy chịu trách nhiệm chuyển tiếp.Việc triển khai thực hiện là neutron/agent/metadata/namespace_proxy.py:</p>
<pre>def _proxy_request(self, remote_address, method, path_info,
                       query_string, body):
    headers = {
        'X-Forwarded-For': remote_address,
    }

    if self.router_id:
        headers['X-Neutron-Router-ID'] = self.router_id
    else:
        headers['X-Neutron-Network-ID'] = self.network_id

    url = urlparse.urlunsplit((
        'http',
        '169.254.169.254',
        path_info,
        query_string,
        ''))

    h = httplib2.Http()
    resp, content = h.request(
        url,
        method=method,
        headers=headers,
        body=body,
        connection_type=agent_utils.UnixDomainHTTPConnection)</pre>
        
<p>Bạn có thể có câu hỏi về URL yêu cầu 169.254.169.254. Làm thế nào để bạn chuyển tiếp nó cho chính mình? Điều này là bởi vì đây là một yêu cầu Socket Domain UNIX. Trên thực tế, URL này chỉ là một trình giữ chỗ tham số. Việc bạn điền thông tin gì không quan trọng. Yêu cầu này tương đương với:</p>
<pre>curl -H "X-Neutron-Network-ID: ${network_uuid}" \
     -H "X-Forwarded-For: ${request_ip}" \
     -X GET \
     --unix /var/lib/neutron/metadata_proxy \
     http://169.254.169.254</pre>
<h4>3.4 Metadata Request lần 3 </h4>
<p>Như đã đề cập trước đó, haproxy sẽ chuyển tiếp yêu cầu metadata đến một local socket file. Vậy, quá trình nào đang lắng nghe tệp socket proxy /opt/stack/data/neutron/metadata_proxy?</p>
<pre># lsof /opt/stack/data/neutron/metadata_proxy
COMMAND     PID  USER   FD   TYPE             DEVICE SIZE/OFF     NODE NAME
neutron-m 11085 stack    3u  unix 0xffff8801c8711c00      0t0 65723197 /opt/stack/data/neutron/metadata_proxy
neutron-m 11108 stack    3u  unix 0xffff8801c8711c00      0t0 65723197 /opt/stack/data/neutron/metadata_proxy
neutron-m 11109 stack    3u  unix 0xffff8801c8711c00      0t0 65723197 /opt/stack/data/neutron/metadata_proxy
# cat /proc/11085/cmdline  | tr '\0' ' '
/usr/bin/python /usr/bin/neutron-metadata-agent --config-file /etc/neutron/neutron.conf</pre>
<p>Có thể thấy rằng neutron-metadata-agent lắng nghe socket file này, tương đương với haproxy chuyển tiếp dịch vụ metadata tới dịch vụ neutron-metadata-agent thông qua socket file.</p>
<pre>def run(self):
    server = agent_utils.UnixDomainWSGIServer('neutron-metadata-agent')
    server.start(MetadataProxyHandler(self.conf),
                 self.conf.metadata_proxy_socket,
                 workers=self.conf.metadata_workers,
                 backlog=self.conf.metadata_backlog,
                 mode=self._get_socket_mode())
    self._init_state_reporting()
    server.wait()</pre>
    <p>/opt/stack/data/neutron/metadata_proxysocket file.</p>
 <p>Vì neutron-metadata-agent là quá trình trên controller node, nó chắc chắn tương thích với dịch vụ Nova metadata. Vấn đề về cách máy ảo OpenStack truy cập dịch vụ Nova Metadata về cơ bản đã được giải quyết.</p>
<pre>curl 169.254.169.254 -> haproxy  -> UNIX Socket -> neutron-metadata-agent -> nova-api-metadata </pre>


