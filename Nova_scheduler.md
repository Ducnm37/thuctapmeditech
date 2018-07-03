<h3>1. Giới thiệu về nova-scheduler </h3>
<p>Nova Scheduler hỗ trợ filtering và weighting để quyết định instance mới được tạo trên node Compute nào. Scheduler chỉ hỗ trợ nodes Compute.</p>
<p> Tất cả các compute node sẽ public trạng thái của nó bao gồm tài nguyên hiện có và dung lượng phần cứng khả dụng cho nova-scheduler thông qua queue. nova-scheduler sau đó sẽ dựa vào những dữ liệu này để đưa ra quyết định khi có request.</p>
<p>Quá trình filtering và weightering </p>
<img src="https://github.com/anhict/images/blob/master/nova-scheduler1.png">
<p>Cấu hình nova scheduler thông qua file /etc/nova/nova.conf </p>
<pre>scheduler_driver_task_period = 60
scheduler_driver = nova.scheduler.filter_scheduler.FilterScheduler
scheduler_available_filters = nova.scheduler.filters.all_filters
scheduler_default_filters = RetryFilter, AvailabilityZoneFilter, RamFilter, DiskFilter, ComputeFilter, ComputeCapabilitiesFilter, ImagePropertiesFilter, ServerGroupAntiAffinityFilter, ServerGroupAffinityFilter</pre>

<p>Mặc định thì scheduler_driver được cấu hình như là filter scheduler, scheduler này sẽ xem xét các host có đầy đủ các tiêu chí sau:</p>
<ul><li>Chưa từng tham gia vào scheduling (RetryFilter)</li>
<li>Nằm trong vùng requested availability zone (requested availability zone)</li>
<li>Có RAM phù hợp (RamFilter).</li>
<li>Có dung lượng ổ cứng phù hợp cho root và ephemeral storage (DiskFilter).</li>
<li>Có thể thực thi yêu cầu (ComputeFilter)</li>
<li>Đáp ứng các yêu cầu ngoại lệ với các instance type (ComputeCapabilitiesFilter)</li>
<li>Đáp ứng mọi yêu cầu về architecture, hypervisor type, hoặc virtual machine mode properties được khai báo trong instance’s image properties (ImagePropertiesFilter).</li>
<li>Ở host khác với các instance khác trong group (nếu có) (ServerGroupAntiAffinityFilter).</li>
<li>Ở trong danh sách các group hosts (nếu có) (ServerGroupAffinityFilter)</li></ul>
<p>Scheduler sẽ cache lại danh sách available hosts, dùng scheduler_driver_task_period để quy định thời gian danh sách được update.</p>
<p>- Quá trình filter được lặp lại trên các compute nodes. Danh sách các hosts được chọn sẽ được sắp xếp sau bởi weighers. Scheduler sau đó sẽ chọn host theo số lượng các instances request. Nếu scheduler không thể chọn bất cứ host nào thì có nghĩa instance đó không thể được scheduled. Filter Scheduler có rất nhiều cơ chế filtering và weighting. Bạn cũng có thể tự chọn cho mình những giải thuật phù hợp.</p>

