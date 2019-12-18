---
layout: article
title: Tìm hiểu về Socket và áp dụng trong NDK Android
tags: [socket, udp, tcp, android, cpp, c++]
---
## 1. Tìm hiểu về socket
### 1. Socket là gì ? 
Socket là một điểm cuối (end-point) của liên kết truyền thông hai chiều (two-way communication) giữa hai chương trình chạy trên mạng. Các lớp Socket được sử dụng để biểu diễn kết nối giữa client và server,  được ràng buộc với một cổng port (thể hiện là một con số cụ thể) để các tầng TCP (TCP Layer) có thể định danh ứng dụng mà dữ liệu sẽ được gửi tới.

Lập trình socket là lập trình cho phép người dùng kết nối các máy tính truyền tải và nhận dữ liệu từ máy tính thông qua mạng

Hiểu đơn giản, socket là thiết bị truyền thông hai chiều gửi và nhận dữ liệu từ máy khác.

### 2. Tại sao người dùng lại cần đến socket?
Trong quá trình làm việc các bạn có thể chạy nhiều socket cùng một lúc nên công việc của bạn sẽ nhanh hơn, nâng cao hiệu suất làm việc.

Ngày nay, Socket được hỗ trợ trong hầu hết các hệ điều hành như MS Windows, Linux và được sử dụng trong nhiều ngôn ngữ lập trình khác nhau: như C, C++, Java, Visual Basic, Visual C++,…

### 3. Socket hoạt động như thế nào?
Là một giao diện lập trình ứng dụng mạng, socket giúp các bạn lập trình kết nối các ứng dụng để truyền và nhận giữ liệu trong môi trường có kết nối Internet bằng cách sử dụng phương thức TCPIP và UDP.

Khi cần trao đổi dữ liệu cho nhau thì 2 ứng dụng cần phải biết thông tin tối thiểu là IP và sô hiểu cổng của ứng dụng kia.
+ 2 ứng dụng có thể nằm cùng trên một máy
+ 2 ứng dụng cùng nằm trên một máy không được cùng số hiệu cổng

### 4. Phân loại Socket
(+) Stream Socket
Dựa trên giao thức TCP( Tranmission Control Protocol), việc truyền dữ liệu chỉ thực hiện giữa 2 quá trình đã thiết lập kết nối. Do đó, hình thức này được gọi là socket hướng kết nối.

**Ưu điểm**: Có thể dùng để liên lạc theo mô hình client và sever. Nếu là mô hình client /sever thì sever lắng nghe và chấp nhận từ client. Giao thức này đảm bảo dữ liệu được truyền đến nơi nhận một cách đáng tin cậy, đúng thứ tự nhờ vào cơ chế quản lý luồng lưu thông trên mạng và cơ chế chống tắc nghẽn. Đồng thời, mỗi thông điệp gửi phải có xác nhận trả về và các gói tin chuyển đi tuần tự.

**Hạn chế**: Có một đường kết nối (địa chỉ IP) giữa 2 tiến trình nên 1 trong 2 tiến trình kia phải đợi tiến trình kia yêu cầu kết nối. 
(+) Datagram Socket
Dựa trên giao thức UDP( User Datagram Protocol) việc truyền dữ liệu không yêu cầu có sự thiết lập kết nối giữa 2 quá trình. Do đó, hình thức này được gọi là socket không hướng kết nối.

**Ưu điểm**: Do không yêu cầu thiết lập kết nối, không phải có những cơ chế phức tạp nên tốc độ giao thức khá nhanh, thuận tiện cho  các ứng dụng truyền dữ liệu nhanh như chat, game…..

**Hạn chế**: Ngược lại với giao thức TCP thì dữ liệu được truyền theo giao thức UDP không được tin cậy, có thế không đúng trình tự và lặp lại. 

5. Websocket là gì?
Websocket là công nghệ hỗ trợ giao tiếp hai chiều giữa client và server bằng cách sử dụng một TCP socket để tạo một kết nối hiệu quả và ít tốn kém.

Mặc dù được thiết kế để chuyên sử dụng cho các ứng dụng web, lập trình viên vẫn có thể đưa chúng vào bất kì loại ứng dụng nào.

**Ưu điểm**
WebSockets cung cấp khả năng giao tiếp hai chiều mạnh mẽ, có độ trễ thấp và dễ xử lý lỗi.

API cũng rất dễ sử dụng trực tiếp mà không cần bất kỳ các tầng bổ sung nào, so với Comet, thường đòi hỏi một thư viện tốt để xử lý kết nối lại, thời gian chờ timeout, các Ajax request (yêu cầu Ajax), các tin báo nhận và các dạng truyền tải tùy chọn khác nhau (Ajax long-polling và jsonp polling).

Không cần phải có nhiều kết nối như phương pháp Comet long-polling.

**Hạn chế**
Là một đặc tả mới của HTML5, WebSocket vẫn chưa được tất cả các trình duyệt hỗ trợ.
Do WebSocket là một TCP socket chứ không phải là HTTP request, nên không dễ sử dụng các dịch vụ có phạm vi-yêu cầu, như SessionInViewFilter của Hibernate. 

### 5. Unix socket là gì?
Unix socket là một điểm giao tiếp để trao đổi dữ liệu giữa các ứng dụng trên cùng một máy tính. Khác với giao thức TCP/IP thực hiện ở giao thức mạng, Unix socket thực hiện ở nhân hệ điều hành, nhờ vậy có thể tránh được cách bước như kiểm tra hoặc routing, đem lại tốc độ kết nối nhanh hơn và nhẹ hơn so với TCP/IP.

**Ưu điểm**:

So với TCP/IP, Unix socket giúp tốc độ truy cập MySQL tăng 30-50%, giảm latency từ 60ms xuống còn 5ms, PostgreSQL tăng hơn 30%, Redis tăng 50%,… 

**Hạn chế:**

Không thể kết nối hai ứng dụng bằng kết nối Unix socket nếu chúng ta có dự định scale ứng dụng ở nhiều máy chủ khác nhau, kể cả private network.

Bản chất Unix socket là một "tập tin" trên máy chủ nên thỉnh thoảng vấn đề phân quyền sẽ khiến bạn hơi đau đầu để giải quyết.
## 2. Áp dụng
Trong bài viết này mình sẽ tập chung vào sử dụng socket trên Android. Sử dụng NDK (viết code c++ để tạo socket server và connect socket client). Bài viết của mình có tham khảo cuốn **Pro Android C++ with the NDK** bạn có thể download tại [đây](https://drive.google.com/open?id=12dVMG5coSI81gkrRLnZdiR1Biwme4CQ-).


Updattig ... !