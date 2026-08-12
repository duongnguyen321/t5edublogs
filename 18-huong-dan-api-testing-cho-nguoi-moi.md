# Hướng Dẫn API Testing Cho Người Mới

> api testing, fresher tester, postman co ban, kiem thu api, tai lieu api

![Square 1:1 editorial cover for an article titled 'Hướng Dẫn API Testing Cho Người Mới'. Visual style: minimalist flat vector UI design, premium professional EdTech editorial artwork, clean bento-grid composition with strong negative space, paper white and zinc-50 background #fafafa, zinc-900 content #18181b, t5edu blue accent #1a73e8, amber highlight #f59e0b. Composition features a central card showing a client-server HTTP request-response flow with flat icons for a client laptop, a network tunnel, and a server database. Exact Vietnamese labels: 'Client', 'API', 'Server'. No people, no faces, no hands, no 3D, no glossy plastic, no photorealism, no watermark](https://files.manuscdn.com/user_upload_by_module/session_file/310519663091035343/EecMolQmeHrazCrF.png)

## API testing là gì và vì sao tester cần biết sớm

Khi mới bắt đầu học kiểm thử, đa số chúng ta đều quen thuộc với giao diện người dùng (UI testing) như bấm nút, nhập form và kiểm tra kết quả hiển thị trên màn hình. Tuy nhiên, trong các hệ thống phần mềm hiện đại, giao diện chỉ là lớp vỏ bên ngoài, trong khi toàn bộ logic nghiệp vụ, xử lý dữ liệu và kết nối đều diễn ra ở tầng API (Application Programming Interface). Nếu tester chỉ kiểm tra ở tầng UI, họ sẽ bỏ sót rất nhiều lỗi logic ngầm xảy ra bên dưới.

Kiểm thử API là quá trình gửi các yêu cầu (requests) trực tiếp đến các điểm cuối (endpoints) của hệ thống và kiểm tra phản hồi (responses) trả về xem có đúng về mã trạng thái, cấu trúc dữ liệu và logic nghiệp vụ hay không. Việc này không yêu cầu giao diện phải hoàn thiện, giúp tester bắt đầu kiểm thử từ rất sớm ngay khi developer vừa xây dựng xong phần backend. Kiến thức nền tảng này gắn liền với các kỹ năng lập trình như trong khóa học [Java cho QA engineer](https://t5edu.site/courses/java-cho-qa-engineer), nơi bạn hiểu cách dữ liệu được truyền tải và xử lý trong hệ thống.

Một hiểu lầm phổ biến của người mới là nghĩ rằng API testing chỉ dành cho automation tester hoặc developer. Thực tế, bất kỳ manual tester nào cũng có thể và nên học kiểm thử API cơ bản bằng các công cụ trực quan như Postman hoặc cURL. Khi kết hợp tư duy kiểm thử từ bài viết [Trả Lời Phỏng Vấn Fresher Tester](https://t5edu.site/blogs/tra-loi-phong-van-fresher-tester), bạn sẽ thấy việc kiểm tra API giúp làm rõ các yêu cầu nghiệp vụ ẩn mà giao diện chưa thể hiện hết.

## Cấu trúc một request và response trong kiểm thử API

Để kiểm thử API hiệu quả, bạn cần nắm vững bốn thành phần cốt lõi của một HTTP request và response. Việc hiểu rõ cấu trúc này giúp bạn không bị bỡ ngỡ khi nhìn vào các công cụ như Postman hay khi đọc tài liệu kỹ thuật từ đội ngũ phát triển.

Thành phần đầu tiên của request là **Method (Phương thức)**, thể hiện hành động bạn muốn thực hiện trên tài nguyên. Các phương thức phổ biến nhất gồm GET để lấy dữ liệu, POST để tạo mới dữ liệu, PUT hoặc PATCH để cập nhật dữ liệu, và DELETE để xóa dữ liệu. Thành phần thứ hai là **Endpoint URL**, địa chỉ định danh tài nguyên trên máy chủ. Thành phần thứ ba là **Headers**, chứa metadata như định dạng dữ liệu (Content-Type: application/json) hoặc token xác thực (Authorization). Thành phần cuối cùng là **Body**, dữ liệu gửi kèm theo trong các request kiểu POST hoặc PUT.

Phía bên kia, response trả về từ server bao gồm **Status Code** (mã trạng thái), headers và body chứa kết quả dữ liệu dưới dạng JSON hoặc XML. Status code là yếu tố quan trọng nhất mà tester cần kiểm tra đầu tiên. Nhóm mã 2xx báo hiệu thành công, nhóm 4xx báo hiệu lỗi từ phía client (như 400 Bad Request, 401 Unauthorized, 404 Not Found), và nhóm 5xx báo hiệu lỗi từ phía server.

![Wide 3:1 educational diagram explaining HTTP request and response structure for API testing. Layout: three horizontal blocks representing Request, Network, and Response, connected by blue arrows. Request block shows Method and Headers, Response block shows Status Code 200 OK and JSON body. Minimalist flat vector UI design, premium professional EdTech editorial artwork, paper white background #fafafa, zinc-900 content, t5edu blue accent #1a73e8, amber highlight #f59e0b, no people, no watermark](https://files.manuscdn.com/user_upload_by_module/session_file/310519663091035343/YYORgzXWtZeKeNLM.png)

## Các bước thực hiện API testing đầu tiên với Postman

Postman là công cụ phổ biến nhất hiện nay giúp người mới bắt đầu làm quen với API testing mà chưa cần viết code. Quy trình thực hiện một ca kiểm thử API cơ bản gồm bốn bước rõ ràng, giúp bạn tự tin thao tác ngay từ ngày đầu tiên tiếp cận.

Bước một là chuẩn bị môi trường và thu thập tài liệu API từ đội ngũ phát triển hoặc Swagger/OpenAPI spec. Bạn cần biết rõ endpoint URL cần gọi là gì và yêu cầu những tham số nào. Bước hai là mở Postman, chọn đúng HTTP method (ví dụ GET), dán URL vào thanh địa chỉ và thêm các headers hoặc authentication nếu có.

Bước ba là gửi request bằng cách bấm nút Send và quan sát phần response trả về ở khung bên dưới. Ở đây, bạn kiểm tra xem status code có phải 200 OK hay không, cấu trúc JSON có khớp với tài liệu thiết kế hay không, và các trường dữ liệu quan trọng có đầy đủ giá trị hay bị null. Bước bốn là viết các ca kiểm thử bằng tay hoặc sử dụng tính năng Test scripts tích hợp sẵn trong Postman để tự động hóa việc kiểm tra status code và dữ liệu trả về.

<table-testcase cols="4" rows="3" headers="Thành phần|Mô tả chi tiết|Ví dụ thực tế|Lỗi hay gặp của người mới">
| Method | Xác định hành động gửi lên server | GET, POST, PUT, DELETE | Chọn nhầm POST thay vì GET khi lấy dữ liệu |
| Status Code | Mã phản hồi trạng thái từ server | 200 OK, 400 Bad Request, 500 Error | Chỉ nhìn giao diện mà quên kiểm tra mã trạng thái |
| Response Body | Dữ liệu trả về dưới dạng JSON | {"id": 1, "status": "active"} | Bỏ qua việc kiểm tra kiểu dữ liệu của các trường |
</table-testcase>

Trong quá trình viết test case cho API, bạn cần tránh những sai lầm cơ bản được phân tích kỹ trong bài [Tester Mới Sai Lầm Ở Đâu Khi Viết Test Case](https://t5edu.site/blogs/tester-moi-sai-lam-o-dau-khi-viet-test-case), đặc biệt là việc chỉ kiểm thử luồng đúng (positive case) mà quên mất các trường hợp dữ liệu biên hoặc dữ liệu sai định dạng.

## Quản lý mã nguồn kiểm thử và kịch bản API

Khi số lượng API endpoint tăng lên, việc lưu trữ và chia sẻ các collection test trong team trở thành một thách thức lớn. Bạn không thể lưu các file cấu hình API một cách tùy tiện trên máy cá nhân mà cần đưa vào hệ thống quản lý phiên bản chuyên nghiệp. Kiến thức về cách tổ chức thư mục, theo dõi thay đổi và cộng tác nhóm qua Git là kỹ năng bắt buộc đối với mọi tester hiện đại.

Việc nắm vững các thao tác cơ bản như commit bộ sưu tập Postman, cấu hình file `.gitignore` để ẩn các token bảo mật nhạy cảm giúp bạn làm việc an toàn và chuyên nghiệp hơn. Bạn có thể tham khảo chi tiết cách quản lý mã nguồn kiểm thử tại khóa học [Git và Github](https://t5edu.site/courses/git-va-github), nơi hướng dẫn từng bước từ cơ bản đến quy trình làm việc nhóm thực tế.

<multiple-choice correct="C" select="single">
Khi kiểm thử một API đăng ký tài khoản và nhận lại mã 201 Created, điều này có ý nghĩa gì đối với tester?
- A: Yêu cầu gửi lên bị lỗi cú pháp JSON
- B: Máy chủ gặp sự cố nội bộ không thể xử lý
- C: Tài nguyên mới đã được tạo thành công trên hệ thống
- D: Người dùng chưa được cấp quyền truy cập endpoint
</multiple-choice>

<dropdown-content>
Làm thế nào để kiểm thử bảo mật cơ bản cho API khi bạn là người mới?
> Bảo mật API không chỉ là việc củapenetration tester mà tester thông thường cũng có thể thực hiện một số kiểm tra cơ bản ngay trên Postman.
```markdown
Các bước kiểm tra bảo mật cơ bản gồm:
1. Kiểm tra Authentication: Gửi request gọi API nhạy cảm mà không kèm Token hoặc Cookie xem hệ thống có trả về mã 401 Unauthorized hay không.
2. Kiểm tra Authorization: Đăng nhập bằng tài khoản thông thường nhưng cố gắng gọi endpoint dành riêng cho quản trị viên xem hệ thống có chặn với mã 403 Forbidden hay không.
3. Kiểm tra Input Validation: Gửi các ký tự đặc biệt hoặc dữ liệu quá dài vào các trường dữ liệu để xem API có xử lý an toàn tránh lỗi injection hay không.
```
</dropdown-content>

![Wide 3:1 educational diagram showing API security testing workflow with authentication checks. Layout: three sequential blocks representing Unauthenticated Request, Authorization Check, and Response Validation, connected by T5Edu blue arrows. Minimalist flat vector UI design, premium professional EdTech editorial artwork, paper white background #fafafa, zinc-900 content, t5edu blue accent #1a73e8, amber highlight #f59e0b, no people, no watermark](https://files.manuscdn.com/user_upload_by_module/session_file/310519663091035343/uQJRcuOKXsIFssdV.png)

## Tổng kết

- API testing giúp kiểm tra logic nghiệp vụ và dữ liệu ở tầng backend trước hoặc song song với giao diện.
- Nắm vững cấu trúc request, response và ý nghĩa các status code là nền tảng cốt lõi cho mọi tester.
- Sử dụng các công cụ trực quan như Postman kết hợp kỹ năng quản lý mã nguồn qua Git giúp tối ưu hóa công việc kiểm thử.
- Nếu bạn muốn nâng cao kỹ năng lập trình để tiến xa hơn trong automation testing, hãy tham khảo các khóa học chuyên sâu trên nền tảng T5Edu.
- Bạn thường gặp khó khăn gì nhất khi bắt đầu đọc tài liệu API và tự viết ca kiểm thử đầu tiên?
