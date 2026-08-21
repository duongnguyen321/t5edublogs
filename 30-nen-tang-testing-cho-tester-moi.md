# Nền Tảng Testing Cho Tester Mới

> Nền tảng testing giúp tester mới hiểu đúng mục tiêu kiểm thử, phân biệt error, defect và failure, rồi biến một user story thành checklist có thể thực hành ngay trong công việc đầu tiên.

![Minimalist flat vector UI design, premium professional EdTech editorial artwork for a beginner software testing foundations guide, 1:1 square cover, clean bento-grid composition with strong negative space, Paper White background #fafafa, Zinc-900 content #18181b, T5Edu Blue accent #1a73e8, Amber highlight #f59e0b, a simple browser card connected to three flat cards labeled "Yêu cầu", "Kiểm thử", and "Bằng chứng", one small checklist icon, short Vietnamese labels only, subtle one-pixel borders, restrained liquid-glass layers, geometric blocks and clean connector lines, no people, no faces, no hands, no 3D, no glossy plastic, no photorealism, no dramatic lighting, no purple, no violet, no pink, no neon, no logo, no watermark](https://files.manuscdn.com/user_upload_by_module/session_file/310519663091035343/twpbyfsqHPJdvIes.png)

## Testing là gì và tester mới cần hiểu gì trước?

Testing là hoạt động đánh giá sản phẩm bằng cách kiểm tra behavior thực tế so với yêu cầu, rủi ro và kỳ vọng đã thống nhất. Testing không chỉ là bấm qua màn hình để tìm lỗi, cũng không phải lời cam kết rằng sản phẩm không còn defect.

Theo [CTFL v4.0 của ISTQB](https://istqb.org/certifications/certified-tester-foundation-level-ctfl-v4-0/), tester cần nắm terminology, testing principles, test process, test analysis, test design và cách testing diễn ra trong vòng đời phát triển. Với tester mới, điều quan trọng không phải học thuộc toàn bộ syllabus, mà là biết dùng các concept này để đặt câu hỏi và tạo evidence rõ ràng.

Bài này dành cho người mới học testing, intern, fresher hoặc người chuyển ngành. Bạn không cần biết framework, code, CI/CD hay protocol. Bạn chỉ cần biết một sản phẩm có yêu cầu và một người dùng cần hoàn thành tác vụ.

![Minimalist flat vector UI design, premium professional EdTech editorial artwork for software testing foundations, 21:9 wide section image, clean horizontal bento-grid composition with strong negative space, Paper White background #fafafa, Zinc-900 content #18181b, T5Edu Blue accent #1a73e8, Amber highlight #f59e0b, left card labeled "Yêu cầu", center card labeled "Thao tác", right card labeled "Kết quả", solid arrows left to right and a small defect marker below the result, short Vietnamese labels only, flat icons, subtle one-pixel borders, restrained liquid-glass layers, no people, no faces, no hands, no 3D, no glossy plastic, no photorealism, no dramatic lighting, no purple, no violet, no pink, no neon, no logo, no watermark](https://files.manuscdn.com/user_upload_by_module/session_file/310519663091035343/QHLvRuJYIXMHLuEh.png)

## Error, defect và failure khác nhau thế nào?

Ba từ này thường bị dùng lẫn trong bug report. Phân biệt đúng giúp tester mô tả nguyên nhân và bằng chứng chính xác hơn.

| Khái niệm | Hiểu đơn giản | Ví dụ trong công việc |
|---|---|---|
| Error | Sai sót của tester, developer, BA hoặc người tạo yêu cầu | Developer viết điều kiện `amount > 0` thay vì `amount >= 0` |
| Defect | Khuyết điểm nằm trong work product hoặc code | Rule cho phép số tiền bằng 0 nhưng code từ chối |
| Failure | Behavior quan sát được khi sản phẩm chạy | User nhập 0 và nhận thông báo lỗi dù yêu cầu cho phép |

Một error có thể tạo ra defect, còn defect có thể chưa gây failure nếu flow chưa chạm tới điều kiện đó. Trong bug report, tester nên ghi điều đã quan sát được, expected result, actual result, bước tái hiện và evidence, thay vì kết luận vội về ý định hoặc năng lực của developer.

<multiple-choice correct="B" select="single">
Tester nhập số tiền bằng 0, hệ thống hiển thị "Số tiền không hợp lệ", trong khi acceptance criteria cho phép giá trị từ 0. Khẳng định nào đúng nhất?
- A: Đây chắc chắn là error của tester
- B: Đây là failure do một defect có thể nằm trong validation rule
- C: Đây chỉ là lỗi môi trường nên không cần report
- D: Đây là proof rằng toàn bộ chức năng thanh toán hỏng
</multiple-choice>

## Test case đầu tiên nên bắt đầu từ đâu?

Tester mới thường mở màn hình rồi nghĩ ngay đến các thao tác. Cách an toàn hơn là bắt đầu từ mục tiêu của user story và acceptance criteria. Nếu yêu cầu chưa rõ, câu hỏi làm rõ cũng là một output có giá trị của testing.

Ví dụ user story là: “Là khách hàng, tôi muốn đổi mật khẩu để bảo vệ tài khoản.” Trước khi viết test case, hãy tách các điều cần biết: user đã đăng nhập chưa, mật khẩu cũ bắt buộc không, độ dài tối thiểu là bao nhiêu, hai lần nhập mới có phải giống nhau không, và sau khi đổi thành công session cũ xử lý thế nào.

Bạn có thể đọc thêm [Đọc User Story để Viết Test](https://t5edu.site/blogs/doc-user-story-de-viet-test) để luyện cách chuyển acceptance criteria thành scenario. Đừng tạo hàng chục test case khi rule chính còn chưa được xác nhận.

<table-testcase cols="5" rows="5" headers="ID|Điều kiện|Thao tác|Expected result|Evidence">
| LOGIN-01 | Tài khoản hợp lệ | Nhập email và password đúng rồi submit | User vào trang chính | Screenshot trang chính và test data |
| LOGIN-02 | Password sai | Nhập email đúng, password sai rồi submit | Có message lỗi, không tạo session hợp lệ | Screenshot message và network status |
| LOGIN-03 | Bỏ trống email | Để email trống rồi submit | Hiển thị validation cạnh field email | Screenshot validation |
| LOGIN-04 | Password ngắn hơn rule | Nhập password không đạt độ dài | Không cho submit hoặc báo đúng rule | Screenshot và dữ liệu nhập |
</table-testcase>

## Smoke test, regression test và exploratory test dùng khi nào?

Smoke test là tập kiểm tra ngắn để xem build có đủ ổn định cho các test tiếp theo hay không. Tester thường kiểm tra app có mở được, đăng nhập được, điều hướng chính có hoạt động và dữ liệu tối thiểu có hiển thị hay không.

Regression test kiểm tra các behavior đã từng hoạt động sau khi có thay đổi. Nó không có nghĩa là chạy mọi test case trong mọi lần build. Tester nên ưu tiên khu vực vừa thay đổi, dependency liên quan và chức năng có rủi ro cao.

Exploratory testing là cách học về sản phẩm, thiết kế test và thực hiện test gần như đồng thời. Nó hữu ích khi requirement chưa đầy đủ hoặc tester cần tìm rủi ro mà checklist cố định chưa bao phủ. Tuy nhiên, exploratory testing vẫn cần ghi lại charter, thời gian, dữ liệu, phát hiện và evidence.

| Loại test | Câu hỏi chính | Output nên có |
|---|---|---|
| Smoke | Build có đủ điều kiện để test sâu hơn không? | Pass hoặc fail rõ cho các flow chính |
| Regression | Thay đổi mới có làm hỏng behavior cũ không? | Kết quả theo khu vực rủi ro |
| Exploratory | Tester còn học được rủi ro nào chưa có trong checklist? | Notes, bug report và câu hỏi mới |

## Bug report tốt cần có những phần nào?

Một bug report tốt giúp người khác tái hiện và đánh giá impact mà không phải đoán. Tiêu đề nên mô tả behavior và điều kiện quan trọng, ví dụ “Login chấp nhận password sai sau khi refresh session”, thay vì “Login bị lỗi”.

Nội dung tối thiểu nên gồm environment, precondition, steps to reproduce, expected result, actual result, severity hoặc impact, evidence và tần suất xảy ra. Nếu defect xảy ra không ổn định, hãy ghi rõ số lần thử và điều kiện khiến kết quả thay đổi.

Tester không cần viết dài để chứng minh mình đã test nhiều. Một report ngắn nhưng có data, bước tái hiện và expected result rõ thường có giá trị hơn một đoạn mô tả cảm tính.

<grid-content>
Cách tự review bug report trước khi gửi
> Đọc report như một developer chưa từng thấy flow này.

```markdown
1. Người khác có biết bắt đầu từ màn hình hoặc data nào không?
2. Mỗi step có một hành động rõ ràng không?
3. Expected result có căn cứ từ requirement không?
4. Actual result có mô tả behavior quan sát được không?
5. Screenshot, video, log hoặc request có đủ để kiểm tra không?
6. Severity đang mô tả impact, không phải mức độ bực mình, đúng không?
```

</grid-content>

## Checklist nền tảng cho tuần đầu đi làm

Trong tuần đầu, tester mới không cần cố chứng minh bằng cách nhận mọi task. Hãy tạo một checklist nhỏ có thể lặp lại cho mỗi user story: đọc requirement, xác định happy path, tìm boundary value, kiểm tra validation, thử error path, ghi evidence và hỏi điểm chưa rõ.

Khi gặp task mới, hãy bắt đầu bằng một bảng ba cột: “Điều cần chứng minh”, “Cách kiểm tra” và “Bằng chứng”. Bảng này giúp bạn không nhầm giữa đã thao tác và đã kiểm tra đúng một rule.

Bạn có thể luyện thêm [Hướng Dẫn API Testing Cho Người Mới](https://t5edu.site/blogs/huong-dan-api-testing-cho-nguoi-moi) sau khi nắm được flow UI cơ bản. Hãy giữ phạm vi vừa sức và ghi lại câu hỏi để trao đổi trong refinement hoặc daily.

<dropdown-content>
Tester mới có cần học ISTQB trước khi làm test không?
> Không bắt buộc phải học xong chứng chỉ mới được bắt đầu. Hãy dùng các foundation concept như requirement, risk, expected result, defect và evidence để làm việc, rồi học có hệ thống qua [khu vực luyện thi ISTQB của T5Edu](https://t5edu.site/istqb).
</dropdown-content>

<dropdown-content>
Nếu test pass nhưng vẫn có bug thì tester đã làm sai chưa?
> Không nhất thiết. Testing giảm risk bằng evidence trong một phạm vi, dữ liệu và thời điểm cụ thể. Tester cần nói rõ coverage và limitation thay vì hứa rằng sản phẩm không còn defect.
</dropdown-content>

## Tổng kết

Nền tảng testing cho tester mới không bắt đầu từ việc thuộc nhiều tool, mà từ khả năng hiểu requirement, dự đoán risk, tạo test có mục tiêu và mô tả evidence.

Nếu bạn đang chuẩn bị task đầu tiên, hãy chọn một user story nhỏ, viết bốn scenario gồm happy path, invalid input, boundary và error path, rồi review lại expected result với requirement.

Nếu muốn luyện theo lộ trình có hệ thống, hãy bắt đầu từ [khu vực luyện thi ISTQB](https://t5edu.site/istqb), sau đó đối chiếu với [Blog Tester của T5Edu](https://t5edu.site/blogs) để chọn bài thực hành tiếp theo.

Bạn thường gặp khó khăn nhất ở bước hiểu requirement, thiết kế scenario hay viết bug report?

## Hashtag

> software testing, tester fresher, manual testing, test case, bug report
