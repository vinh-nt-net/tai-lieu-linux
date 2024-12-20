**Galera Cluster** là một giải pháp **replication** (sao chép) đồng bộ cho **MariaDB** và **MySQL**, hỗ trợ tính năng **multi-master replication** và **high availability** (HA) với các tính năng chính như **sao chép đồng bộ**, **tính nhất quán mạnh mẽ**, và **khả năng phục hồi tự động**. Đây là một công cụ mạnh mẽ để tạo ra các cơ sở dữ liệu phân tán với khả năng chịu lỗi cao.

### **Các đặc điểm chính của Galera Cluster:**

1. **Multi-Master Replication (Sao chép đa chủ):**
    - Mỗi node trong Galera Cluster đều có thể xử lý các thao tác **ghi** và **đọc**. Điều này có nghĩa là bạn có thể ghi dữ liệu vào bất kỳ node nào trong cluster, và thay đổi đó sẽ được đồng bộ ngay lập tức đến tất cả các node còn lại.
    - Các thao tác ghi sẽ được **đồng bộ** giữa các node trước khi chúng được thực hiện, đảm bảo tính nhất quán của dữ liệu.
2. **Sao chép Đồng bộ (Synchronous Replication):**
    - Galera sử dụng sao chép đồng bộ, có nghĩa là các thay đổi dữ liệu phải được xác nhận và đồng bộ giữa tất cả các node trong cluster trước khi thay đổi đó được cam kết.
    - Khi một node nhận một thay đổi (ví dụ: `INSERT`, `UPDATE`, hoặc `DELETE`), thay đổi này sẽ được gửi đến tất cả các node khác trong cluster và chỉ khi tất cả node xác nhận rằng thay đổi đã được áp dụng thì thay đổi đó mới được cam kết.
3. **High Availability (Khả năng chịu lỗi cao):**
    - Vì tất cả các node trong Galera Cluster đều có thể thực hiện các thao tác ghi và có thể thay thế nhau, hệ thống đảm bảo tính sẵn sàng cao.
    - Nếu một node bị lỗi hoặc mất kết nối với cluster, các node khác vẫn có thể hoạt động bình thường mà không làm gián đoạn dịch vụ. Các node sẽ tự động **rejoin** (tham gia lại) vào cluster khi sự cố được khắc phục.
4. **Tính Nhất Quán (Consistency):**
    - Galera đảm bảo **tính nhất quán mạnh mẽ** thông qua **quy trình kiểm tra Write-Set** (WSREP). Trước khi thay đổi được cam kết, các node phải đồng bộ để đảm bảo rằng tất cả các node trong cluster luôn có cùng một trạng thái dữ liệu.
    - Điều này giúp tránh các vấn đề về phân mảnh dữ liệu và các tình huống "split-brain" (khi các node không thể đồng bộ hóa dữ liệu).
5. **Auto-Join và Auto-Recovery:**
    - Khi một node bị ngắt kết nối hoặc gặp sự cố, nó có thể tự động **rejoin** vào cluster mà không cần can thiệp thủ công.
    - Sau khi khôi phục kết nối, node sẽ thực hiện **state transfer** để đồng bộ lại với các node còn lại trong cluster.
6. **Quy trình hoạt động của Galera Cluster:**
    - **WSREP** (Write Set Replication) là cơ chế chịu trách nhiệm đồng bộ dữ liệu trong Galera. Mỗi khi có một thay đổi dữ liệu, thay đổi này được **đóng gói** thành một "write-set" và sau đó được **phát tán** đến các node khác trong cluster.
    - **Flow Control** được sử dụng để kiểm tra tốc độ truyền tải giữa các node nhằm tránh tình trạng tắc nghẽn hoặc mất đồng bộ.
    - **Certification-based Replication**: Trước khi thay đổi được thực hiện, Galera sử dụng một quy trình **certification** để kiểm tra xem thao tác ghi có thể thực hiện mà không vi phạm tính nhất quán của cluster hay không.

### **Cấu trúc của Galera Cluster:**

1. **Cluster Nodes:**
    - Các node trong Galera Cluster có thể là **master** hoặc **slave**, nhưng tất cả đều có khả năng thực hiện cả thao tác đọc và ghi.
    - Tất cả các node trong cluster đều đồng bộ với nhau và có thể xử lý cả các yêu cầu đọc và ghi.
2. **WSREP (Write Set Replication):**
    - WSREP là một giao thức đồng bộ cho phép các thay đổi dữ liệu được **chia sẻ** và **xác nhận** giữa các node trong cluster.
3. **Quorum and Split-Brain:**
    - Một cluster Galera yêu cầu **quorum** (đa số node phải đồng ý với một quyết định) để đảm bảo tính nhất quán trong trường hợp một số node bị lỗi hoặc mất kết nối.
    - Nếu một phân đoạn của cluster bị tách rời và không còn đủ số lượng node tham gia, nó sẽ không thể ghi dữ liệu cho đến khi kết nối lại với phần còn lại của cluster.
4. **Node Recovery (Khôi phục node):**
    - Khi một node khôi phục, nó phải thực hiện **state transfer** để đồng bộ lại với các node còn lại trong cluster và đảm bảo dữ liệu đồng nhất.

### **Ưu điểm của Galera Cluster:**

- **Tính sẵn sàng cao**: Hệ thống có thể tiếp tục hoạt động ngay cả khi một hoặc nhiều node gặp sự cố.
- **Không có điểm thất bại đơn lẻ (SPOF)**: Không có một node duy nhất chịu trách nhiệm, mỗi node đều có thể hoạt động như một master.
- **Dễ dàng mở rộng**: Bạn có thể thêm hoặc loại bỏ node mà không làm gián đoạn dịch vụ.
- **Dữ liệu nhất quán và an toàn**: Sao chép đồng bộ giúp tránh tình trạng dữ liệu bị mất hoặc không đồng bộ giữa các node.

### **Nhược điểm của Galera Cluster:**

- **Chi phí băng thông và độ trễ cao**: Vì các thay đổi phải được xác nhận đồng bộ giữa tất cả các node, việc này có thể gây tăng độ trễ và tiêu tốn băng thông trong các môi trường có nhiều dữ liệu thay đổi liên tục.
- **Phức tạp trong cấu hình và quản lý**: Việc duy trì và quản lý một cluster Galera có thể phức tạp hơn so với các hệ thống sao chép thông thường, đặc biệt là trong các tình huống node bị lỗi hoặc mất kết nối.

### **Tóm tắt:**

- **Galera Cluster** giúp bạn xây dựng một hệ thống **MariaDB** có khả năng chịu lỗi cao và phân tán với khả năng **multi-master replication**.
- Mọi node trong cluster đều có thể xử lý các yêu cầu ghi và đọc, và dữ liệu giữa các node được đồng bộ ngay lập tức nhờ vào **sao chép đồng bộ**.
- Cluster này cung cấp sự ổn định, tính sẵn sàng cao, nhưng đòi hỏi việc quản lý và cấu hình phức tạp hơn.

Nếu bạn có thêm câu hỏi hoặc cần hỗ trợ thêm về cấu hình hoặc vận hành Galera Cluster, đừng ngần ngại hỏi nhé!

tao user va phan quyen 


CREATE USER 'vinh'@'%' IDENTIFIED BY 'abc@123';

```

GRANT SELECT ON my_database.* TO 'vinh'@'%';

```
