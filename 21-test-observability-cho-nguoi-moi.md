# Test Observability Cho Người Mới

> test observability, qa beginner, automation testing, debug test fail, fresher tester

![Square 1:1 premium EdTech editorial cover about test observability for beginner QA testers. Show a clean bento-grid on Paper White background #fafafa with a central dashboard card labeled exactly 'Test observability' and three connected signal cards labeled 'Logs', 'Metrics', and 'Traces'; an amber magnifying glass points toward a red failure marker and a blue root-cause node; Zinc-900 content #18181b, T5Edu Blue #1a73e8 arrows, Amber #f59e0b highlights, subtle one-pixel borders, restrained liquid-glass layers, minimalist flat vector UI design, strong negative space, professional EdTech editorial artwork, no people, no faces, no hands, no 3D, no photorealism, no glossy plastic, no dramatic lighting, no purple, no violet, no pink, no neon, no logo, no watermark](https://files.manuscdn.com/user_upload_by_module/session_file/310519663091035343/yRQneStsgKMvhXsi.png)

## Test observability là gì và khác log test ra sao

Test observability là khả năng hiểu điều gì xảy ra bên trong hệ thống trong lúc test chạy bằng cách kết hợp data như logs, metrics và traces. Theo [OpenTelemetry Observability Primer](https://opentelemetry.io/docs/concepts/observability-primer/), observability giúp team đặt câu hỏi “Vì sao chuyện này xảy ra?” từ góc nhìn bên ngoài mà không cần đoán toàn bộ implementation bên trong.

Với người mới, hãy hình dung một test fail giống như một chiếc xe không đến đích. Log có thể nói xe đã rẽ ở đâu, metric cho biết xe chạy nhanh hay chậm, còn trace cho thấy hành trình đi qua những trạm nào. Chỉ nhìn dòng `Expected 200, received 500` thường chưa đủ để biết lỗi nằm ở browser, API gateway, service thanh toán hay database.

![Wide 21:9 educational illustration showing a failed automated test journey across a horizontal service map. Cards from left to right: 'Browser test', 'API gateway', 'Order service', 'Payment service', 'Database'. A red failure marker appears at 'Payment service'; blue trace line connects all cards, small log icons sit below services, and metric mini charts show latency spike near payment. Labels in Vietnamese exactly as written. Minimalist flat vector UI design, premium professional EdTech editorial artwork, clean bento-grid horizontal composition, Paper White #fafafa background, Zinc-900 #18181b, T5Edu Blue #1a73e8, Amber #f59e0b, red only for failure marker, subtle one-pixel borders, restrained liquid-glass layers, no people, no faces, no hands, no 3D, no photorealism, no purple, no violet, no pink, no neon, no logo, no watermark](https://files.manuscdn.com/user_upload_by_module/session_file/310519663091035343/zpCKGvlKPoWnjRoO.png)

Ba loại signal có vai trò khác nhau:

| Signal | Nó trả lời câu hỏi nào? | Ví dụ khi test checkout fail |
| --- | --- | --- |
| Logs | Sự kiện cụ thể nào đã xảy ra? | Payment service trả `card_declined` |
| Metrics | Hệ thống có biến động gì theo thời gian? | Tỷ lệ lỗi tăng từ 1% lên 18% |
| Traces | Một request đã đi qua những service nào? | Request dừng ở payment service sau 2,4 giây |

Test observability không thay thế testcase, assertion hay bug report. Nó bổ sung evidence để tester đi từ “test fail” đến “failure có thể giải thích và reproduce”. Đây là lý do chủ đề này đáng học sau khi bạn đã biết [Testing cơ bản](/courses/testing-co-ban) và cách ghi expected result.

<multiple-choice correct="B" select="single">
Một test checkout nhận HTTP 500. Evidence nào giúp tester tìm root cause tốt nhất?
- A: Chỉ chụp screenshot của nút Checkout
- B: Kết hợp log lỗi, metric tỷ lệ lỗi và trace của request
- C: Chạy lại test vô hạn đến khi pass
- D: Đổi tên testcase cho dễ đọc hơn
</multiple-choice>

## Vì sao automation test hay fail nhưng chưa chắc có bug

Một test fail có ít nhất ba khả năng: product có defect, test có vấn đề hoặc môi trường không ổn định. Nếu không có observability, người mới thường rerun test nhiều lần rồi đánh dấu flaky mà chưa có bằng chứng.

Ví dụ test `should create order` fail ở bước click Pay. Log browser cho biết nút đã được click. Trace cho thấy request POST `/payments` mất 8 giây. Metric của payment service đồng thời có p95 latency tăng mạnh. Trong tình huống này, screenshot chỉ cho biết UI đang ở trạng thái chờ, còn signals liên kết mới chỉ ra bottleneck phía service.

<grid-content>
Cách đọc một test failure bằng ba câu hỏi
> Luôn đi từ hành trình của request, không bắt đầu bằng phỏng đoán component nào có lỗi.
```markdown
**Điều gì đã xảy ra?**
Đọc log theo timestamp và test step. Xác định action cuối cùng đã hoàn tất, status code và error message chính xác.

**Nó xảy ra ở đâu?**
Dùng trace id hoặc request id để nối browser, API gateway và service. Nếu không có trace, ghi nhận đây là gap về evidence.

**Có lặp lại theo quy luật không?**
So sánh metric theo thời điểm, môi trường, browser, branch và version. Một failure chỉ xuất hiện lúc tải cao có thể khác lỗi luôn tái hiện ở mọi run.
```
</grid-content>

Nguyên tắc quan trọng là không gọi mọi lỗi test là flaky. Flaky test là test có cùng code và cùng điều kiện nhưng kết quả không ổn định. Nếu môi trường hoặc dependency thay đổi, tester cần ghi rõ context thay vì dùng nhãn flaky quá sớm.

Nếu bạn đang học automation với Playwright, bài [Khắc Phục Flaky Test Trong Automation](/blogs/khac-phuc-flaky-test-trong-automation) là nền tảng phù hợp để nối khái niệm ổn định test với evidence khi debug. Bạn cũng có thể xem [Playwright với TypeScript cơ bản cho Tester](/blogs/playwright-voi-typescript-co-ban-cho-tester) để luyện cách tạo test step rõ ràng.

## Bắt đầu thu thập logs, metrics và traces từ đâu

Người mới không nên bật mọi signal và tạo ra một núi data không ai đọc. Hãy bắt đầu từ một user journey quan trọng, chẳng hạn login, checkout hoặc tạo ticket. Mỗi journey cần một correlation id để nối test run với request trong hệ thống.

Bộ evidence tối thiểu nên có:

- Test run id, commit hoặc build number, browser và environment.
- Timestamp của từng step, URL hoặc API route và status code.
- Error message có context, không chỉ có tên exception.
- Request id hoặc trace id để tìm các service liên quan.
- Screenshot hoặc video chỉ ở bước giúp xác nhận trạng thái UI.

![Wide 21:9 educational diagram showing a beginner-friendly test observability setup. Left card labeled 'Test runner' emits a blue line with 'trace id' to a central collector card labeled 'Telemetry'. From the collector, three branches go to cards labeled 'Logs', 'Metrics', and 'Traces', then converge into a right dashboard labeled 'Root cause'. Use solid blue arrows for data flow and dotted amber arrows for investigation flow. Minimalist flat vector UI design, premium professional EdTech editorial artwork, clean horizontal bento-grid, Paper White #fafafa, Zinc-900 #18181b, T5Edu Blue #1a73e8, Amber #f59e0b, subtle one-pixel borders, restrained liquid-glass layers, exact Vietnamese labels, no people, no faces, no hands, no 3D, no photorealism, no purple, no violet, no pink, no neon, no logo, no watermark](https://files.manuscdn.com/user_upload_by_module/session_file/310519663091035343/WfiTfvZWJlEhEVPB.png)

OpenTelemetry là một lựa chọn trung lập để instrument ứng dụng và phát ra telemetry. Tài liệu của họ mô tả trace là đường đi của một request qua nhiều service, còn span là một operation cụ thể trong trace. Tester không nhất thiết phải trở thành observability engineer, nhưng nên biết đọc các field như `http.route`, `http.response.status_code`, duration và service name.

<table-testcase cols="5" rows="4" headers="ID|Test step|Evidence cần lưu|Dấu hiệu bất thường|Kết luận ban đầu">
| OBS-01 | Mở trang checkout | Trace id, browser log | API cart trả 401 | Có thể session hết hạn |
| OBS-02 | Gửi payment request | Request id, response log | Timeout sau 8 giây | Kiểm tra payment service |
| OBS-03 | Xác nhận đơn | DB event, trace | Không có event tạo đơn | Kiểm tra transaction |
| OBS-04 | Chạy lại dưới tải cao | Metric latency, error rate | p95 tăng mạnh | Có dấu hiệu capacity |
</table-testcase>

Một dashboard tốt không cần quá nhiều biểu đồ. Với mini project, chỉ cần filter theo build number, test name, environment và trace id. Khi mở một failure, reviewer phải đi được từ test step sang request, từ request sang service và từ service sang error cụ thể.

## Thiết kế testcase có observability ngay từ đầu

Observability hiệu quả bắt đầu từ testcase có cấu trúc. Thay vì viết “kiểm tra thanh toán”, hãy viết rõ precondition, action, expected result và evidence. Cấu trúc này giúp developer biết cần tìm data nào khi test fail.

Ví dụ testcase tốt: “Với user có giỏ hàng chứa một sản phẩm và payment sandbox hoạt động, gửi request thanh toán hợp lệ. Expected result là API trả 201, tạo một order event và UI hiển thị mã đơn. Evidence gồm trace id, response body đã ẩn thông tin nhạy cảm và timestamp của order event.”

<dropdown-content>
Tester có nên log toàn bộ response và data người dùng không?
> Không nên. Log phải đủ để debug nhưng không được làm lộ password, token, số thẻ, email nhạy cảm hoặc thông tin định danh không cần thiết.
```markdown
Quy tắc redaction tối thiểu:
- Mask access token và refresh token.
- Chỉ giữ bốn số cuối của mã thanh toán nếu cần đối soát.
- Không ghi password dù testcase dùng data giả.
- Dùng test data có định danh rõ nhưng không gắn với user thật.
- Ghi service, route, status và trace id thay vì dump toàn bộ object.
```
</dropdown-content>

Đây là điểm người mới dễ bỏ qua: observability cũng có risk. Data càng chi tiết càng dễ giúp debug, nhưng cũng tăng chi phí lưu trữ và nguy cơ lộ thông tin. Hãy thống nhất trước field nào bắt buộc, field nào được mask và thời gian lưu evidence.

## Từ test failure đến bug report có thể hành động

Observability chỉ tạo giá trị khi evidence được chuyển thành kết luận có thể hành động. Một bug report tốt không chép toàn bộ log. Nó chọn đúng đoạn chứng minh, chỉ ra điểm bắt đầu bất thường và phân biệt triệu chứng với nguyên nhân có khả năng cao.

| Thành phần report | Nội dung nên ghi | Ví dụ ngắn |
| --- | --- | --- |
| Scenario | User flow và test case | Checkout với payment sandbox |
| First abnormal signal | Signal đầu tiên lệch expected | Payment span tăng lên 7,8 giây |
| Correlation | ID nối các hệ thống | `trace_id=4f3c2a` |
| Impact | Người dùng hoặc chức năng bị ảnh hưởng | Không tạo được order |
| Reproduction | Điều kiện tái hiện | Chỉ xảy ra dưới tải cao |
| Next action | Bước kiểm tra tiếp theo | Kiểm tra timeout và pool connection |

![Wide 21:9 educational bug triage board for test observability. Arrange six connected cards from left to right labeled exactly 'Scenario', 'First signal', 'Trace ID', 'Impact', 'Reproduction', and 'Next action', ending in a blue bug report card labeled 'Actionable report'. Use blue arrows for evidence flow and amber highlights on the first abnormal signal and next action. Minimalist flat vector UI design, premium professional EdTech editorial artwork, clean horizontal bento-grid, Paper White #fafafa background, Zinc-900 #18181b, T5Edu Blue #1a73e8, Amber #f59e0b, subtle one-pixel borders, no people, no faces, no hands, no 3D, no photorealism, no purple, no violet, no pink, no neon, no logo, no watermark](https://files.manuscdn.com/user_upload_by_module/session_file/310519663091035343/mPvmPQyWQYsXnWwu.png)

<multiple-choice correct="D" select="single">
Một trace cho thấy payment service chậm, nhưng database và API gateway vẫn bình thường. Kết luận nào phù hợp nhất trong bug report?
- A: Database chắc chắn bị hỏng
- B: Test chắc chắn bị flaky
- C: Chỉ cần chụp thêm screenshot
- D: Payment service là điểm cần điều tra tiếp, chưa đủ evidence để kết luận nguyên nhân cuối
</multiple-choice>

Hãy tránh hai lỗi khi viết report. Lỗi thứ nhất là kết luận quá sớm, chẳng hạn gọi payment service là root cause chỉ vì span của nó dài nhất. Lỗi thứ hai là đính kèm quá nhiều dữ liệu không liên quan khiến developer không tìm thấy signal quan trọng. Evidence tốt phải có timestamp, correlation id, expected behavior và một bước điều tra tiếp theo.

## Tổng kết

- Test observability nối test runner với logs, metrics và traces để tester hiểu root cause thay vì chỉ thấy pass hoặc fail.
- Người mới nên bắt đầu với một journey, một trace id và một dashboard nhỏ, không bật mọi signal cùng lúc.
- Testcase cần định nghĩa evidence ngay từ đầu, đồng thời mask data nhạy cảm và tránh log thừa.
- Nếu bạn thường gặp test fail khó giải thích, hãy học [Khắc Phục Flaky Test Trong Automation](/blogs/khac-phuc-flaky-test-trong-automation) rồi thực hành với [API Testing cơ bản](/courses/api-testing-co-ban).
- Khi một test fail lần tới, bạn sẽ kiểm tra log, metric hay trace trước, và vì sao?
