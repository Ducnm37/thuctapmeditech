<h3> Tìm hiểu về iptables
</p>
Nội dung 
</h3>
<ul> 
<li>
<a href="#gioi_thieu"> 1. Giới thiệu </a>
</li> 
<li>
<a href="#bat_dau_tim_hieu"> 2. Bắt đầu tìm hiểu </a>
</li> 
<li>
<a href="#viet_mot_quy_tac_don_gian"> 3. Viết một bộ quy tắc đơn giản </a>
</li> 
<li>
<a href="#giao_dien"> 4. Giao diện </a>
</li> 
<li>
<a href="#cac_dia_chi_ip"> 5. Các địa chỉ IP </a>
</li> 
<li>
<a href="#port_va_protocols"> 6. Port và Protocols </a>
</li> 
<li>
<a href="#putting_it_all_together"> 7. Putting It All Together </a>
</li> 
<li>
<a href="#tom_tat"> 8. Tóm tắt </a>
</li>
</ul>
# <h4> 1. Giới thiệu </h4>
<p>CentOS có một tường lửa cực mạnh được xây dựng trong, thường được gọi là iptables, nhưng chính xác hơn là iptables / netfilter. Iptables là mô đun userpace, bit mà bạn, người dùng, tương tác với tại dòng lệnh để nhập các quy tắc tường lửa vào các bảng được xác định trước. Netfilter là một mô-đun hạt nhân, được tích hợp trong hạt nhân, thực sự lọc. Có nhiều giao diện người dùng GUI cho iptables, cho phép người dùng thêm hoặc xác định các quy tắc dựa trên một điểm và một giao diện người dùng, nhưng thường thiếu sự linh hoạt trong việc sử dụng giao diện dòng lệnh và giới hạn sự hiểu biết của người dùng về những gì thực sự xảy ra. Chúng ta sẽ học giao diện dòng lệnh của iptables.</p>
<p>Trước khi chúng ta có thể nắm bắt được với iptables, chúng ta cần phải có ít nhất một sự hiểu biết cơ bản về cách nó hoạt động. Iptables sử dụng khái niệm địa chỉ IP, các giao thức (tcp, udp, icmp) và các cổng. Chúng ta không cần phải là những chuyên gia trong lĩnh vực này để bắt đầu (vì chúng ta có thể tìm kiếm bất kỳ thông tin nào chúng ta cần), nhưng giúp bạn có được sự hiểu biết chung.</p>
<p>Iptables đặt các quy tắc vào các chuỗi được xác định trước (INPUT, OUTPUT và FORWARD) được kiểm tra đối với bất kỳ lưu lượng mạng nào (các gói IP) có liên quan đến các chuỗi đó và quyết định được làm gì với mỗi gói tin dựa trên kết quả của các quy tắc đó, hoặc bỏ gói tin. Các hành động này được gọi là các mục tiêu, trong đó hai mục tiêu được xác định trước phổ biến nhất là DROP để thả một gói hoặc ACCEPT để chấp nhận một gói tin.</p>
<li>
Chains
</li>
<p>Đây là 3 chuỗi được xác định trước trong bảng lọc mà chúng ta có thể thêm các quy tắc để xử lý các gói tin IP đi qua các chuỗi đó. Các chuỗi này là:</p>
<ul>
<li>INPUT - Tất cả các gói tin dành cho máy chủ.</li>
<li>OUTPUT - Tất cả các gói tin có nguồn gốc từ máy chủ.</li>
<li>FORWARD - Tất cả các gói tin không dành cho cũng không bắt nguồn từ máy tính chủ, nhưng đi qua (chuyển bởi) máy tính chủ. Chuỗi này được sử dụng nếu bạn đang sử dụng máy tính như một bộ định tuyến.</li>
</ul>
<p>Đối với hầu hết các phần, chúng ta sẽ giải quyết chuỗi INPUT để lọc các gói dữ liệu nhập vào máy tính của chúng ta - nghĩa là giữ các kẻ xấu.</p>
<p>Các quy tắc được thêm vào một danh sách cho mỗi chuỗi. Một gói được kiểm tra đối với mỗi quy tắc lần lượt, bắt đầu từ đầu, và nếu nó phù hợp với quy tắc đó thì hành động được thực hiện như chấp nhận (ACCEPT) hoặc thả (DROP) gói. Khi một quy tắc đã được kết hợp và một hành động được thực hiện, gói tin sẽ được xử lý theo kết quả của quy tắc đó và không được xử lý bởi các quy tắc khác trong chuỗi. Nếu một gói tin đi qua tất cả các quy tắc trong chuỗi và đạt đến đáy mà không khớp với bất kỳ quy tắc nào, thì hành động mặc định cho chuỗi đó được thực hiện. Đây được gọi là chính sách mặc định và có thể được đặt thành ACCEPT hoặc DROP gói.</p>
<h4>
 Khái niệm chính sách mặc định trong chuỗi tạo ra hai khả năng cơ bản mà chúng ta phải xem xét trước khi quyết định cách chúng ta sắp xếp tường lửa của chúng ta.
</h4>
<p>1. Chúng ta có thể thiết lập một chính sách mặc định để DROP tất cả các gói tin và sau đó thêm các quy tắc để đặc biệt cho phép (ACCEPT) các gói tin có thể được từ các địa chỉ IP đáng tin cậy, hoặc cho một số cổng mà chúng ta có các dịch vụ chạy như bittorrent, FTP server, Web Server , Máy chủ tệp Samba ...</p>
<p>Hay cách khác,</p>
<p>2. Chúng ta có thể thiết lập chính sách mặc định để ACCEPT tất cả các gói dữ liệu và sau đó thêm các quy tắc để chặn các gói đặc biệt (DROP) có thể là từ các địa chỉ IP hoặc các vùng địa chỉ IP gây phiền toái đặc biệt hoặc cho một số cổng mà chúng ta có các dịch vụ riêng hoặc không có các dịch vụ đang chạy.</p>
<p>Nói chung, tùy chọn 1 ở trên được sử dụng cho chuỗi INPUT nơi chúng tôi muốn kiểm soát những gì được phép truy cập vào máy tính của chúng tôi và tùy chọn 2 sẽ được sử dụng cho chuỗi OUTPUT, nơi chúng ta thường tin tưởng vào lưu lượng truy cập đang để lại (có nguồn gốc từ) máy của chúng tôi.</p>
<h4> 2. Bắt đầu tìm hiểu </h4>
<p>Làm việc với iptables từ dòng lệnh đòi hỏi quyền root, vì vậy bạn cần phải trở thành root cho hầu hết mọi thứ chúng ta sẽ làm.</p>
<code>
QUAN TRỌNG: Chúng tôi sẽ tắt iptables và đặt lại các quy tắc tường lửa của bạn, vì vậy nếu bạn dựa vào tường lửa Linux làm tuyến phòng thủ chính, bạn nên biết về điều này.
</code>
<p></p>
<p></p>
<p></p>
<p></p>
