---
title: "Bản đề xuất"
date: 2026-07-19
weight: 2
chapter: false
pre: " <b> 2. </b> "
---

# CloudPay - Hệ thống thanh toán trên AWS
## Giải pháp thanh toán trực tuyến an toàn, mở rộng và chịu tải cao

### 1. Tóm tắt điều hành

CloudPay là hệ thống thanh toán trực tuyến được thiết kế trên nền tảng AWS, hỗ trợ doanh nghiệp tiếp nhận, xác thực và xử lý giao dịch từ nhiều cổng thanh toán như MoMo, VNPay và ZaloPay.

Hệ thống được xây dựng theo kiến trúc **Serverless** kết hợp **Event-driven**, sử dụng AWS Lambda, Amazon API Gateway và Amazon SQS để xử lý các yêu cầu thanh toán. Dữ liệu giao dịch được lưu trữ trong Amazon RDS thông qua AWS RDS Proxy nhằm hạn chế tình trạng cạn kết nối khi số lượng Lambda tăng cao.

Ngoài luồng dành cho người dùng, hệ thống còn cung cấp khu vực quản trị dành cho Admin. Admin có thể đăng nhập, theo dõi giao dịch, xem báo cáo, kiểm tra giao dịch thất bại và thực hiện xử lý lại thông qua Dead Letter Queue.

CloudPay tập trung giải quyết bốn yêu cầu chính:

- Không làm mất giao dịch thanh toán.
- Bảo vệ cơ sở dữ liệu khi tải tăng cao.
- Hỗ trợ giám sát, cảnh báo và bù lỗi.
- Cung cấp chức năng quản trị và theo dõi giao dịch cho Admin.

### 2. Tuyên bố vấn đề

#### Vấn đề hiện tại

Các hệ thống thanh toán trực tuyến thường gặp các vấn đề sau:

- Lượng giao dịch tăng đột biến trong thời gian khuyến mãi hoặc giờ cao điểm.
- Nhiều Lambda kết nối trực tiếp đến database có thể gây cạn connection.
- Webhook từ cổng thanh toán có thể được gửi nhiều lần.
- Giao dịch có thể bị bỏ sót nếu hệ thống lỗi trong quá trình ghi dữ liệu.
- Khó truy vết lỗi giữa API Gateway, Lambda, Queue và Database.
- Admin thiếu công cụ theo dõi và xử lý lại giao dịch thất bại.
- Người dùng không nhận được thông báo kịp thời sau khi thanh toán.

#### Giải pháp đề xuất

CloudPay sử dụng kiến trúc AWS Serverless kết hợp xử lý bất đồng bộ.

Người dùng truy cập hệ thống thông qua AWS WAF và Amazon CloudFront. Giao diện web và Admin Dashboard được lưu trữ trên Amazon S3. Người dùng và Admin xác thực bằng Amazon Cognito trước khi gọi API thông qua Amazon API Gateway.

Payment Lambda chịu trách nhiệm tạo URL thanh toán và chuyển người dùng đến cổng thanh toán. Sau khi giao dịch hoàn tất, cổng thanh toán gửi IPN hoặc Webhook về hệ thống.

Webhook Lambda thực hiện:

- Xác thực chữ ký.
- Kiểm tra mã giao dịch.
- Kiểm tra số tiền và trạng thái.
- Ngăn xử lý trùng lặp.
- Đưa giao dịch hợp lệ vào Amazon SQS.

Worker Lambda xử lý các message trong queue và ghi dữ liệu vào Amazon RDS thông qua AWS RDS Proxy. Nếu message xử lý thất bại nhiều lần, message được chuyển sang Amazon SQS Dead Letter Queue.

Admin có thể:

- Xem danh sách giao dịch.
- Tra cứu lịch sử thanh toán.
- Xem báo cáo.
- Theo dõi giao dịch thất bại.
- Retry các giao dịch trong DLQ.

#### Lợi ích và hoàn vốn đầu tư

Giải pháp mang lại các lợi ích:

- Giảm nguy cơ mất giao dịch nhờ Amazon SQS.
- Giảm tải và bảo vệ database bằng AWS RDS Proxy.
- Tự động mở rộng theo số lượng giao dịch.
- Giảm công việc vận hành máy chủ.
- Hỗ trợ phát hiện và xử lý lỗi nhanh.
- Cải thiện trải nghiệm người dùng qua Email và SMS.
- Cho phép Admin theo dõi hệ thống tập trung.

Với môi trường học tập hoặc MVP có lưu lượng thấp, phần lớn dịch vụ Serverless có thể tận dụng AWS Free Tier. Chi phí chủ yếu đến từ Amazon RDS, RDS Proxy, log và lưu lượng mạng.

### 3. Kiến trúc giải pháp

CloudPay áp dụng kiến trúc Serverless kết hợp Event-driven Architecture.

![Kiến trúc hệ thống thanh toán trên AWS](/images/2-Proposal/payment_architecture.png)

#### Tổng quan luồng xử lý

1. Người dùng gửi HTTPS Request đến hệ thống.
2. AWS WAF kiểm tra và lọc request độc hại.
3. Amazon CloudFront phân phối giao diện từ Amazon S3.
4. Người dùng đăng nhập qua Amazon Cognito.
5. Cognito cấp JWT Token.
6. Frontend gọi API thông qua Amazon API Gateway.
7. Payment Lambda tạo URL thanh toán.
8. Người dùng được chuyển đến MoMo, VNPay hoặc ZaloPay.
9. Cổng thanh toán gửi IPN/Webhook về hệ thống.
10. Webhook Lambda xác thực chữ ký và giao dịch.
11. Giao dịch hợp lệ được đưa vào Amazon SQS.
12. Worker Lambda đọc message và xử lý giao dịch.
13. Worker Lambda ghi dữ liệu vào Amazon RDS thông qua RDS Proxy.
14. Amazon SNS gửi Email, SMS hoặc sự kiện sang dịch vụ khác.
15. Giao dịch lỗi được chuyển vào Amazon SQS DLQ.
16. Admin theo dõi và xử lý lại giao dịch thất bại.

#### Dịch vụ AWS sử dụng

- **AWS WAF**: Bảo vệ ứng dụng và lọc request độc hại.
- **Amazon CloudFront**: Phân phối giao diện web với độ trễ thấp.
- **Amazon S3**: Lưu trữ Web Application và Admin Dashboard.
- **Amazon Cognito**: Xác thực người dùng và Admin.
- **Amazon API Gateway**: Tiếp nhận và định tuyến API request.
- **AWS Lambda**: Xử lý nghiệp vụ thanh toán, webhook, worker và admin.
- **Amazon SQS**: Lưu hàng đợi giao dịch và điều tiết tải.
- **Amazon SQS DLQ**: Lưu giao dịch xử lý thất bại.
- **AWS RDS Proxy**: Quản lý kết nối đến database.
- **Amazon RDS**: Lưu dữ liệu giao dịch và lịch sử thanh toán.
- **Amazon SNS**: Gửi thông báo và cảnh báo.
- **Amazon CloudWatch**: Thu thập log và metric.
- **AWS X-Ray**: Truy vết request giữa các dịch vụ.
- **AWS IAM**: Quản lý quyền truy cập.
- **Amazon VPC**: Cô lập Worker Lambda, RDS Proxy và Amazon RDS trong private subnet.

#### Thiết kế thành phần

##### Edge và Frontend

AWS WAF được đặt phía trước Amazon CloudFront để kiểm tra request. CloudFront phân phối nội dung tĩnh được lưu trên Amazon S3, bao gồm giao diện người dùng và Admin Dashboard.

##### Authentication

Amazon Cognito quản lý hai nhóm:

- Customer.
- Admin.

Sau khi đăng nhập thành công, Cognito trả về JWT Token. API Gateway xác thực token trước khi cho phép truy cập API.

##### Payment Lambda

Payment Lambda thực hiện:

- Kiểm tra thông tin đơn hàng.
- Tính số tiền thanh toán.
- Tạo mã giao dịch.
- Lưu trạng thái ban đầu.
- Tạo Payment URL.
- Trả Payment URL cho frontend.

##### Webhook Lambda

Webhook Lambda thực hiện:

- Xác thực chữ ký.
- Kiểm tra mã giao dịch.
- Kiểm tra số tiền.
- Kiểm tra trạng thái.
- Chống xử lý trùng lặp.
- Đưa transaction vào SQS.

##### Transaction Queue

Amazon SQS đóng vai trò buffer giữa Webhook Lambda và Worker Lambda.

SQS giúp:

- Điều tiết số lượng giao dịch.
- Tránh ghi đồng thời quá lớn xuống database.
- Tự động retry khi Worker Lambda lỗi.
- Hạn chế mất giao dịch.

##### Worker Lambda

Worker Lambda thực hiện:

- Cập nhật trạng thái thanh toán.
- Lưu lịch sử giao dịch.
- Cập nhật đơn hàng.
- Ghi audit log.
- Publish notification event.
- Đảm bảo xử lý idempotent.

##### RDS Proxy và Amazon RDS

Worker Lambda kết nối đến Amazon RDS thông qua RDS Proxy.

RDS Proxy giúp:

- Pool và tái sử dụng connection.
- Giảm số lượng connection đồng thời.
- Cải thiện khả năng chịu tải.
- Tăng độ ổn định của database.

Amazon RDS được triển khai theo mô hình Primary và Standby để tăng tính sẵn sàng.

##### Admin API Lambda

Admin API Lambda cung cấp:

- Xem danh sách giao dịch.
- Tìm kiếm giao dịch.
- Xem lịch sử thanh toán.
- Xem thống kê và báo cáo.
- Kiểm tra giao dịch lỗi.
- Retry giao dịch trong DLQ.

##### Observability

Amazon CloudWatch thu thập:

- API Gateway error.
- Lambda error.
- Lambda duration.
- SQS message count.
- DLQ message count.
- RDS connection.
- Database CPU.
- Database latency.

AWS X-Ray hỗ trợ truy vết request qua API Gateway, Lambda, SQS, Worker Lambda, RDS Proxy và Amazon RDS.

Amazon SNS gửi cảnh báo Email hoặc SMS cho Admin khi:

- Lambda có lỗi.
- DLQ có message mới.
- API Gateway có tỷ lệ lỗi cao.
- Database CPU tăng cao.
- Queue tồn đọng nhiều message.

### 4. Triển khai kỹ thuật

#### Các giai đoạn triển khai

1. **Nghiên cứu và thu thập yêu cầu**
   - Phân tích luồng thanh toán.
   - Nghiên cứu IPN/Webhook.
   - Xác định yêu cầu cho người dùng và Admin.

2. **Thiết kế kiến trúc**
   - Thiết kế kiến trúc AWS.
   - Xác định luồng dữ liệu.
   - Thiết kế database.
   - Thiết kế cơ chế xử lý lỗi.

3. **Xây dựng Frontend**
   - Tạo giao diện thanh toán.
   - Xây dựng trang kết quả thanh toán.
   - Xây dựng Admin Dashboard.

4. **Xây dựng Backend**
   - Tạo API Gateway.
   - Xây dựng Payment Lambda.
   - Xây dựng Webhook Lambda.
   - Xây dựng Worker Lambda.
   - Xây dựng Admin API Lambda.

5. **Tích hợp Queue và Database**
   - Tạo Transaction Queue.
   - Cấu hình DLQ.
   - Cấu hình RDS Proxy.
   - Kết nối Worker Lambda với Amazon RDS.

6. **Giám sát và cảnh báo**
   - Cấu hình CloudWatch Logs.
   - Tạo CloudWatch Alarm.
   - Bật AWS X-Ray.
   - Tạo SNS Topic.

7. **Kiểm thử**
   - Kiểm thử thanh toán thành công.
   - Kiểm thử thanh toán thất bại.
   - Kiểm thử duplicate webhook.
   - Kiểm thử retry.
   - Kiểm thử DLQ.
   - Kiểm thử Admin Dashboard.

8. **Clean-up**
   - Xóa Lambda.
   - Xóa API Gateway.
   - Xóa Queue và DLQ.
   - Xóa RDS Proxy và RDS.
   - Xóa S3 Bucket và CloudFront.
   - Xóa CloudWatch Alarm và SNS Topic.

#### Yêu cầu kỹ thuật

- **Frontend**: React hoặc Next.js.
- **Backend**: Node.js hoặc Python.
- **Infrastructure as Code**: AWS SAM, CDK hoặc Terraform.
- **Database**: Amazon RDS PostgreSQL hoặc MySQL.
- **Authentication**: Amazon Cognito.
- **API**: API Gateway REST API hoặc HTTP API.
- **Queue**: Amazon SQS Standard Queue.
- **Monitoring**: CloudWatch và X-Ray.
- **Payment Gateway**: MoMo, VNPay hoặc ZaloPay Sandbox.

#### Yêu cầu IAM

Mỗi Lambda sử dụng IAM Role riêng.

- Payment Lambda chỉ được phép tạo và cập nhật transaction ban đầu.
- Webhook Lambda chỉ được gửi message vào SQS.
- Worker Lambda chỉ được đọc SQS và truy cập RDS Proxy.
- Admin API Lambda chỉ được đọc dữ liệu và thực hiện retry.
- Worker Lambda chỉ được publish vào SNS.

Không sử dụng quyền AdministratorAccess cho Lambda, không hard-code Access Key hoặc secret, không đặt RDS trong public subnet và không mở public S3 nếu không cần thiết.

### 5. Lộ trình và mốc triển khai

#### Tuần 1–2: Phân tích và thiết kế

- Nghiên cứu AWS Serverless.
- Phân tích bài toán thanh toán.
- Thiết kế sơ đồ kiến trúc.
- Xác định dịch vụ AWS.

#### Tuần 3–4: Frontend và Authentication

- Tạo S3 Bucket.
- Cấu hình CloudFront.
- Tạo Cognito User Pool.
- Xây dựng giao diện đăng nhập.
- Tạo Admin Dashboard cơ bản.

#### Tuần 5–6: Payment API

- Tạo API Gateway.
- Xây dựng Payment Lambda.
- Tích hợp cổng thanh toán Sandbox.
- Tạo Payment URL.
- Kiểm thử redirect.

#### Tuần 7–8: Webhook và Queue

- Xây dựng Webhook Lambda.
- Xác thực chữ ký.
- Tạo Transaction Queue.
- Tạo DLQ.
- Kiểm thử retry và redrive.

#### Tuần 9–10: Database và Worker

- Tạo Amazon RDS.
- Tạo AWS RDS Proxy.
- Xây dựng Worker Lambda.
- Ghi transaction vào database.
- Publish event tới SNS.

#### Tuần 11: Admin và Monitoring

- Hoàn thiện Admin API Lambda.
- Xây dựng màn hình báo cáo.
- Cấu hình CloudWatch.
- Bật AWS X-Ray.
- Tạo SNS Alarm.

#### Tuần 12: Kiểm thử và hoàn thiện

- Kiểm thử end-to-end.
- Kiểm thử lỗi.
- Kiểm tra log và metric.
- Hoàn thiện tài liệu workshop.
- Clean-up tài nguyên.

### 6. Ước tính ngân sách

Chi phí tham khảo cho môi trường học tập hoặc MVP:

- AWS Lambda: 0–10 USD/tháng.
- Amazon API Gateway: 0–10 USD/tháng.
- Amazon SQS: 0–5 USD/tháng.
- Amazon SNS: 0–5 USD/tháng.
- Amazon S3: 1–5 USD/tháng.
- Amazon CloudFront: 1–10 USD/tháng.
- Amazon Cognito: 0–5 USD/tháng.
- Amazon RDS: 15–40 USD/tháng.
- RDS Proxy: phụ thuộc cấu hình database.
- CloudWatch Logs và X-Ray: 1–10 USD/tháng.

**Tổng chi phí dự kiến: khoảng 25–80 USD/tháng.**

Giải pháp giảm chi phí:

- Sử dụng AWS Free Tier.
- Chỉ bật RDS khi thực hành.
- Giảm thời gian lưu log.
- Dùng cấu hình database nhỏ.
- Xóa tài nguyên sau workshop.
- Đặt AWS Budget Alarm.

### 7. Đánh giá rủi ro

| Rủi ro | Mức ảnh hưởng | Xác suất |
|---|---|---|
| Duplicate Webhook | Cao | Trung bình |
| Giao dịch không được ghi nhận | Cao | Thấp |
| Cạn connection database | Cao | Trung bình |
| Queue tồn đọng | Trung bình | Trung bình |
| Sai chữ ký thanh toán | Cao | Thấp |
| Rò rỉ dữ liệu nhạy cảm | Cao | Thấp |
| Chi phí vượt ngân sách | Trung bình | Trung bình |
| Lỗi khi Admin retry | Trung bình | Thấp |
| Lambda cold start | Thấp | Trung bình |

#### Chiến lược giảm thiểu

- Sử dụng Transaction ID làm idempotency key.
- Đưa giao dịch vào SQS trước khi xử lý ghi dữ liệu.
- Sử dụng RDS Proxy để quản lý connection pool.
- Theo dõi `ApproximateNumberOfMessagesVisible`.
- Theo dõi `ApproximateAgeOfOldestMessage`.
- Tạo CloudWatch Alarm khi Queue tồn đọng.
- Sử dụng HTTPS và JWT Token.
- Kiểm tra chữ ký webhook.
- Sử dụng Secrets Manager.
- Áp dụng IAM Least Privilege.
- Đặt RDS trong private subnet.
- Tạo AWS Budget và billing alert.
- Clean-up tài nguyên sau workshop.

#### Kế hoạch dự phòng

- Message lỗi được đưa vào DLQ.
- Admin có thể retry.
- Transaction được ghi log để đối soát.
- Amazon RDS có Standby.
- Infrastructure as Code được dùng để triển khai lại hệ thống.

### 8. Kết quả kỳ vọng

Sau khi hoàn thành, hệ thống có thể:

- Tạo URL thanh toán.
- Chuyển người dùng đến cổng thanh toán.
- Nhận và xác thực Webhook.
- Xử lý giao dịch bất đồng bộ.
- Lưu dữ liệu vào Amazon RDS.
- Gửi Email hoặc SMS.
- Theo dõi log và metric.
- Truy vết lỗi bằng X-Ray.
- Đưa giao dịch lỗi vào DLQ.
- Cho phép Admin xem báo cáo và retry giao dịch.

#### Tiêu chí đánh giá thành công

- Giao dịch hợp lệ được ghi nhận đầy đủ.
- Duplicate Webhook không tạo dữ liệu trùng.
- Queue xử lý được tải tăng đột biến.
- Worker Lambda không làm cạn connection database.
- Giao dịch lỗi được chuyển đúng sang DLQ.
- Admin xem được lịch sử giao dịch.
- Admin retry được giao dịch lỗi.
- CloudWatch Alarm gửi cảnh báo thành công.
- Có tài liệu triển khai và clean-up đầy đủ.

#### Giá trị dài hạn

CloudPay có thể được phát triển cho:

- Website thương mại điện tử.
- Hệ thống đặt vé.
- Ứng dụng đặt hàng.
- Nền tảng bán khóa học.
- Hệ thống thu phí dịch vụ.
- Ứng dụng thanh toán hóa đơn.

Kiến trúc có thể mở rộng thêm:

- Nhiều cổng thanh toán.
- Hoàn tiền.
- Đối soát giao dịch.
- Fraud Detection.
- Audit Log.
- Dashboard doanh thu.
- CI/CD Pipeline.
- AWS KMS Encryption.
- AWS Secrets Manager.
- Multi-Region Disaster Recovery.

Dự án thể hiện khả năng thiết kế một hệ thống AWS thực tế, sử dụng nhiều dịch vụ Serverless và Managed Service, đồng thời giải quyết các yêu cầu về hiệu năng, bảo mật, giám sát và vận hành.