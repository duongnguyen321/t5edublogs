# Tester Mới Sai Lầm Ở Đâu Khi Viết Test Case
> test-case,fresher,manual-testing,qa-engineer,bug-report

![Square 1:1 editorial cover for an article titled 'Sai Lầm Của Tester Mới Khi Viết Test Case'. The visual must communicate a tester correcting mistakes directly. Composition: a clean bento-grid layout with a large central panel showing a document icon with several red X marks being crossed out by a blue checkmark. Left supporting block: a warning triangle icon labeled 'Happy Path'. Right supporting block: a magnifying glass icon labeled 'Edge Case'. Bottom strip: a small progress bar block labeled 'Coverage'. Exact Vietnamese labels: 'Test Case', 'Happy Path', 'Edge Case'. Visual relationships: thin T5Edu Blue connector lines link the central document to both side blocks; an Amber highlight dot marks the warning triangle. Minimalist flat vector UI design, premium professional EdTech editorial artwork, clean bento-grid composition with strong negative space, Paper White and Zinc-50 background #fafafa, Zinc-900 content #18181b, T5Edu Blue accent #1a73e8, Amber highlights #f59e0b, subtle one-pixel borders and restrained liquid-glass layers, simple flat icons and geometric shapes, no people, no faces, no hands, no 3D, no glossy plastic, no photorealism, no dramatic lighting, no purple, no violet, no pink, no neon, no logo, no watermark.](IMAGE_PLACEHOLDER_COVER)

## Vì sao bài viết này dành cho bạn?

Nếu bạn vừa mới bắt đầu làm tester, từng viết test case mà bị senior review và nhận về một mớ comment, hoặc cảm thấy mình "viết test theo bản năng" mà không biết sai ở đâu, thì bài viết này viết cho bạn. Nội dung tổng hợp các sai lầm phổ biến nhất của tester mới, giải thích vì sao từng lỗi xảy ra, và đưa ra cách sửa cụ thể kèm ví dụ thật. Bạn không cần biết code hay tool phức tạp, chỉ cần hiểu cách dùng phần mềm là đủ để áp dụng.

Cộng đồng QA quốc tế thường xuyên trao đổi về các lỗi kinh điển mà freshers mắc phải, trong đó phổ biến nhất là chỉ test "happy path" (kịch bản sử dụng đúng cách) và bỏ qua edge case (trường hợp bất thường), theo thảo luận trên [cộng đồng AskMeTraining](https://www.linkedin.com/posts/askmetraining_softwaretesting-qualityassurance-qabeginner-activity-7409551708462329856-kD-i). Các lỗi còn lại đến từ thói quen viết vội, chưa hiểu nghiệp vụ và chưa quen với cách senior review test case.

<multiple-choice correct="B" select="single">
Đâu là sai lầm phổ biến nhất của tester mới khi viết test case?
- A: Viết quá nhiều test case cho một chức năng
- B: Chỉ viết test case cho kịch bản đúng (happy path) và bỏ qua trường hợp bất thường
- C: Dùng quá nhiều bảng Excel để quản lý test case
- D: Hỏi senior quá nhiều câu hỏi khi chưa rõ yêu cầu
</multiple-choice>

## Sai lầm 1: Chỉ test happy path

Đây là lỗi số một. Happy path là kịch bản user dùng đúng mọi thứ: nhập đúng email, đúng mật khẩu, bấm nút đúng lúc. Người mới viết 5 trong 6 test case là happy path vì nó dễ nghĩ ra nhất và hệ thống "chạy xanh" rất đẹp. Nhưng bug thật thường nằm ở những chỗ user làm sai, làm nhanh, làm nửa chừng.

Hãy so sánh hai cách viết cho chức năng đăng nhập:

| Cách viết của tester mới | Cách viết đủ tốt |
|---|---|
| Nhập đúng email, đúng mật khẩu, bấm Đăng nhập | Nhập đúng email, đúng mật khẩu, bấm Đăng nhập |
| Nhập email thiếu ký tự, bấm Đăng nhập | Nhập mật khẩu sai, kiểm tra thông báo lỗi |
| | Nhập email sai định dạng (thiếu @, có khoảng trắng) |
| | Bấm Đăng nhập nhiều lần liên tục |
| | Đăng nhập với tài khoản đã bị khóa |
| | Ngắt mạng giữa lúc đang đăng nhập |

Bảng bên trái chỉ tìm bug khi input "gần đúng". Bảng bên phải bắt được cả lỗi xử lý lỗi (error handling), lỗi bảo mật cơ bản và lỗi khi hệ thống chập chờn, đúng tinh thần phân tích test như trong bài [Em Sẽ Test Form Login Này Thế Nào?](/blogs/em-se-test-form-login-nay-the-nao).

## Sai lầm 2: Bước thực hiện quá chung chung

Câu bước như "Kiểm tra đăng nhập" hoặc "Thử nhập sai" khiến người đọc không biết phải làm gì, làm ở đâu, với dữ liệu gì. Khi tester khác cầm test case đó chạy lại, họ sẽ hiểu theo cách riêng và kết quả không so sánh được.

Test case tốt trả lời được ba câu hỏi: lấy dữ liệu nào, thao tác gì, theo thứ tự nào. Ví dụ thay "Kiểm tra đăng nhập" bằng "Đăng nhập với email test01@example.com và mật khẩu Sai123!, bấm nút Đăng nhập, quan sát thông báo hiển thị". Chi tiết hơn không có nghĩa là dài hơn; nghĩa là cụ thể hơn.

<table-testcase cols="4" rows="3" headers="ID|Precondition|Các bước thực hiện|Kết quả mong đợi">
| TC01 | Tài khoản test01@example.com tồn tại, chưa đăng nhập | Nhập email test01@example.com, mật khẩu Sai123!, bấm Đăng nhập | Hiển thị thông báo "Email hoặc mật khẩu không chính xác", không chuyển màn hình |
| TC02 | User đã đăng nhập trên trình duyệt khác | Mở ứng dụng, đăng nhập lại với cùng tài khoản | Hiển thị thông báo "Tài khoản đang được sử dụng ở thiết bị khác" |
| TC03 | Tài khoản đã bị khóa bởi admin | Nhập đúng email và mật khẩu của tài khoản bị khóa, bấm Đăng nhập | Hiển thị thông báo "Tài khoản đã bị khóa", không cho vào hệ thống |
</table-testcase>

## Sai lầm 3: Nhầm severity và priority

Hai khái niệm này làm khó tester mới từ ngày đầu đi làm. Severity (mức độ nghiêm trọng) đo thiệt hại kỹ thuật mà bug gây ra. Priority (mức độ ưu tiên) đo việc bug có cần sửa gấp hay không theo góc nhìn kinh doanh. Hai chiều này độc lập.

Một ví dụ kinh điển: logo trên trang chủ bị lệch màu đúng thương hiệu. Về kỹ thuật, bug này hầu như không ảnh hưởng gì, severity thấp. Nhưng logo sai màu nghĩa là thương hiệu sai, phải sửa ngay, priority cao. Ngược lại, ứng dụng crash khi bấm một nút ẩn sâu trong cài đặt: severity cao về mặt kỹ thuật, nhưng nếu chức năng đó ít user dùng, priority có thể trung bình.

| Tình huống | Severity | Priority | Vì sao |
|---|---|---|---|
| Crash khi thanh toán | Cao | Cao | Mất giao dịch, ảnh hưởng doanh thu ngay |
| Logo lệch màu trang chủ | Thấp | Cao | Ảnh hưởng thương hiệu, sửa trước khi release |
| Crash nút ẩn trong phần cài đặt nâng cao | Cao | Trung bình | Ít người dùng chạm tới |
| Tooltip chính tả sai trên trang About | Thấp | Thấp | Không ảnh hưởng chức năng lẫn hình ảnh lớn |

Bài [Test Thanh Toán: Đừng Chỉ Bấm Pay](/blogs/test-thanh-toan-dung-chi-bam-pay) minh họa rõ việc một thao tác bấm nút đơn giản của user có thể giấu cả mê cung kiểm thử phía sau, điều này ảnh hưởng trực tiếp đến cách bạn đánh giá severity của bug thanh toán.

## Sai lầm 4: Viết test case trước khi hiểu nghiệp vụ

Nhiều tester mới nhận yêu cầu, mở Excel và viết ngay test case vì sợ "làm chậm tiến độ". Kết quả là test case chạy xanh nhưng product release xong vẫn dính bug nghiêm trọng, vì các case chỉ phủ bề mặt màn hình mà không hiểu luồng dữ liệu chạy như thế nào. Đây chính là nguyên nhân sâu xa của hiện tượng dashboard xanh nhưng tiền vẫn bay mà bài [Test Pass, Tiền Vẫn Bay](/blogs/test-pass-tien-van-bay) đã phân tích.

Thứ tự đúng là hỏi trước khi viết: chức năng này phục vụ ai, nghiệp vụ phía sau là gì, dữ liệu đi từ màn hình xuống database theo luồng nào, các trạng thái có thể có của đơn hàng là gì. Mỗi câu hỏi trả lời được sẽ sinh ra một nhóm test case mà cách viết theo bản năng không bao giờ chạm tới. Khi nghiệp vụ phức tạp, đừng ngại vẽ lại luồng dưới dạng sơ đồ và đối chiếu với developer trước khi viết test.

<dropdown-content>
Làm sao biết mình đang viết test case ở mức hời hợt?
> Dùng checklist tự rà sau khi viết xong mỗi nhóm test case: có đủ positive case, negative case, boundary case và lỗi hệ thống hay chưa; mỗi bước có trả lời được "dữ liệu nào, thao tác gì, thứ tự nào" hay chưa; severity và priority có được đánh riêng hay chưa.
```markdown
Checklist rà test case cho tester mới:
1. Positive case: kịch bản user dùng đúng chức năng, đã phủ hết các nhánh chính.
2. Negative case: input sai, thao tác sai thứ tự, dữ liệu thiếu, quyền không đủ.
3. Boundary case: giá trị min, max, vừa vượt giới hạn (0 ký tự, giới hạn ký tự của ô input, số tiền 0 đồng, số tiền cực lớn).
4. Hệ thống: ngắt mạng, load lâu, bấm nhanh nhiều lần, đóng giữa chừng.
5. Mỗi bước viết đủ ba phần: dữ liệu đầu vào, thao tác cụ thể, kết quả mong đợi rõ ràng.
6. Severity và Priority được đánh riêng, kèm lý do ngắn gọn.
```
Nếu checklist trả về "chưa" ở bất kỳ mục nào, hãy quay lại viết thêm trước khi gửi review.
</dropdown-content>

## Sai lầm 5: Bug report viết như nhật ký cá nhân

Bug report là sản phẩm thứ hai của tester sau test case, và cũng là nơi tester mới lộ rõ nhất sự thiếu chuyên nghiệp. Report kiểu "Nó bị lỗi rồi, bấm vào là hỏng" khiến developer đọc xong vẫn không biết phải sửa gì. Report tốt là report mà developer đọc xong có thể tái hiện bug mà không cần hỏi lại.

Report chuẩn gồm các trường bắt buộc: tiêu đề ngắn gọn, môi trường (máy, trình duyệt, phiên bản), các bước tái hiện theo thứ tự, kết quả thực tế, kết quả mong đợi, ảnh chụp hoặc video kèm theo. Tiêu đề bug nên theo mẫu "Hành động gây lỗi + hậu quả", ví dụ "Bấm Thanh toán khi giỏ hàng trống: hệ thống hiển thị lỗi 500 thay vì thông báo nhắc user". Một mẹo nhỏ: trước khi gửi, tự đặt câu hỏi "người không biết gì về việc này có tái hiện được không".

<grid-content>
Năm thói quen giúp tester mới viết test case tốt hơn trong 30 ngày
> Chọn hai thói quen và duy trì liên tục, thay vì ôm cả năm thứ một lúc.
```markdown
**Viết case negative trước**

Sau khi viết xong happy path, bắt buộc bổ sung 2-3 negative case trước khi tự cho là xong.
```
```markdown
**Rà lại bằng checklist**

Dùng checklist sáu mục trong dropdown bên trên cho mỗi nhóm test case trước khi gửi review.
```
```markdown
**Đọc lại bug của mình sau một tuần**

Lấy bug report cũ đọc lại xem người lạ có tái hiện được không, ghi chú chỗ cần sửa.
```
```markdown
**Hỏi nghiệp vụ trước khi viết**

Mỗi yêu cầu mới, dành 15 phút hỏi BA/PO về luồng dữ liệu và trạng thái nghiệp vụ.
```
```markdown
**Xin review có cấu trúc**

Khi gửi test case cho senior, hỏi rõ "chỗ nào thiếu case, chỗ nào bước chưa cụ thể" thay vì chỉ hỏi "ok không".
```
</grid-content>

Nếu bạn muốn có lộ trình bài bản hơn thay vì tự dò dẫm, bài [Lộ Trình Học Tester: Từ Zero Đến Chuyên Nghiệp](/blogs/lo-trinh-hoc-tester-tu-zero-den-chuyen-nghiep) giúp đặt kỹ năng viết test case vào đúng vị trí trong hành trình nghề nghiệp. Còn nếu nghiệp vụ phía sau ứng dụng bạn đang test liên quan đến database, khóa [SQL dành cho QA engineer](/courses/sql-danh-cho-qa-engineer) là bước tiếp theo tự nhiên, vì hiểu dữ liệu là cách tốt nhất để viết được test case sâu về nghiệp vụ.

## Tổng kết

Năm sai lầm phổ biến của tester mới không đến từ thiếu thông minh, mà đến từ thiếu phương pháp: chỉ test happy path, bước thực hiện chung chung, nhầm severity với priority, viết test trước khi hiểu nghiệp vụ và viết bug report thiếu cấu trúc. Cả năm lỗi này đều sửa được bằng checklist và thói quen, không cần kinh nghiệm nhiều năm.

- Test case không tính theo số lượng mà tính theo khả năng bắt bug: negative case và boundary case mới là nơi bug sống.
- Mỗi bước test case phải cụ thể đến mức người lạ cũng tái hiện được.
- Severity và priority là hai chiều độc lập; đánh riêng từng cái kèm lý do.
- Hiểu nghiệp vụ trước, viết test sau; không có đường tắt nào bền vững.

Nếu bạn vừa bắt đầu với nghề tester và cảm thấy mình đang viết test theo bản năng, hãy thử áp dụng checklist sáu mục trong bài này cho nhóm test case tiếp theo của bạn. Bạn sẽ thấy sự khác biệt ngay trong lượt review đầu tiên. Vậy câu hỏi đặt ra cho bạn là: trong test case gần nhất bạn viết, negative case chiếm bao nhiêu phần trăm?
