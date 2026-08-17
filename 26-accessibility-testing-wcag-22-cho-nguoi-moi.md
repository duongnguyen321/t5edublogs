# Accessibility Testing với WCAG 2.2 cho Tester Mới

> Accessibility testing với WCAG 2.2 giúp tester mới kiểm tra những rào cản cơ bản khiến người dùng không thể đọc, điều hướng hoặc hoàn thành một tác vụ trên website, thay vì chỉ xác nhận giao diện có hiển thị đẹp.

![Minimalist flat vector UI design, premium professional EdTech editorial artwork for a beginner accessibility testing guide, 1:1 square cover, clean bento-grid composition with strong negative space, Paper White background #fafafa, Zinc-900 content #18181b, T5Edu Blue accent #1a73e8, Amber highlight #f59e0b, subtle one-pixel borders and restrained liquid-glass layers, a friendly tester at a laptop checking a web login page with three simple panels labeled "Keyboard", "Focus", and "Form", small accessibility icons for keyboard, eye, and captions, English labels only, balanced editorial layout, crisp flat geometry, no gradients, no photorealism, no brand logos, no dense text, no decorative watermark](https://files.manuscdn.com/user_upload_by_module/session_file/310519663091035343/BGwZBESUAuXQpHfQ.png)

## Accessibility testing là gì và tester mới cần kiểm tra điều gì?

Accessibility testing là quá trình kiểm tra xem website hoặc ứng dụng có thể được sử dụng bởi người có các nhu cầu khác nhau hay không. Tester không chỉ nhìn màu sắc hoặc kích thước chữ. Tester cần kiểm tra cả khả năng đọc nội dung, điều hướng bằng bàn phím, nhận biết trạng thái focus, điền form và hiểu thông báo lỗi.

WCAG 2.2 là bộ hướng dẫn của W3C với các success criteria được viết thành phát biểu có thể kiểm thử và không phụ thuộc một công nghệ cụ thể. Phiên bản hiện hành được W3C công bố dưới dạng Recommendation vào ngày 12 tháng 12 năm 2024 và bổ sung 9 success criteria so với WCAG 2.1. Tuy nhiên, một tester mới không cần học thuộc toàn bộ tiêu chuẩn trước khi chạy được những kiểm tra có giá trị.

Điểm bắt đầu phù hợp là xem một trang như một user đang cố hoàn thành tác vụ. Ví dụ, với form đăng nhập, tester cần trả lời: user có biết ô nào là email không, có thể đi đến nút Login chỉ bằng bàn phím không, khi nhập sai thì có hiểu lỗi nằm ở đâu không, và focus có bị che bởi một thanh cố định hay không.

<multiple-choice correct="B" select="single">
Một trang vượt qua công cụ scan tự động nhưng tester không thể đi đến nút Submit bằng bàn phím. Kết luận nào phù hợp nhất?
- A: PASS vì công cụ tự động không báo lỗi
- B: FAIL ở khả năng keyboard access và cần ghi nhận bằng chứng
- C: PASS nếu giao diện nhìn rõ trên Chrome
- D: BLOCK vì chưa biết framework frontend
</multiple-choice>

## Làm checklist accessibility testing đầu tiên như thế nào?

W3C Easy Checks gọi đây là first review, tức review nhanh để phát hiện các vấn đề cơ bản chứ không phải đánh giá tuân thủ toàn diện. Với một tester mới, checklist nên ngắn, có thể lặp lại và gắn với từng page hoặc user flow.

| Nhóm kiểm tra | Câu hỏi tester cần trả lời | Bằng chứng nên lưu |
|---|---|---|
| Page title | Tab và title có mô tả đúng nội dung page không? | Tên tab, URL, screenshot |
| Heading | Heading có thể hiện cấu trúc nội dung không? | Outline heading hoặc screenshot |
| Alt text | Ảnh có ý nghĩa có mô tả phù hợp không? Ảnh trang trí có bị đọc thừa không? | Tên ảnh, DOM hoặc screenshot |
| Contrast | Chữ và thành phần quan trọng có đủ tương phản không? | Màu, công cụ đo, screenshot |
| Keyboard | Tab có đi qua đúng thứ tự và không bị kẹt không? | Video ngắn hoặc danh sách bước |
| Focus | Tester có nhìn thấy phần tử đang được focus không? | Screenshot trước và sau khi Tab |
| Form | Label, lỗi và trạng thái bắt buộc có rõ không? | Input, message, expected result |

Hãy chọn một flow nhỏ như đăng nhập hoặc tìm kiếm. Chạy flow một lần bằng chuột để hiểu expected result, sau đó tải lại page và chạy lại chỉ bằng bàn phím. Cách này giúp tester phân biệt lỗi nghiệp vụ với lỗi accessibility thay vì ghi mọi khác biệt thành một bug chung chung.

![Minimalist flat vector UI design, premium professional EdTech editorial artwork, 21:9 wide section image for a beginner accessibility testing checklist, clean bento-grid horizontal composition with strong negative space, Paper White background #fafafa, Zinc-900 content #18181b, T5Edu Blue accent #1a73e8, Amber highlight #f59e0b, subtle one-pixel borders and restrained liquid-glass layers, left side a browser page card with checkbox rows labeled "Title", "Heading", "Keyboard", "Form", right side a tester checklist card with a solid blue arrow moving from page review to evidence, short English labels only, flat icons, crisp editorial vector style, no gradients, no photorealism, no dense paragraphs, no logos, no watermark](https://files.manuscdn.com/user_upload_by_module/session_file/310519663091035343/NiCGRJjysUShNUSo.png)

## Kiểm tra bàn phím và focus ra sao để không bỏ sót lỗi?

Bàn phím là phép kiểm tra có giá trị cao vì tester có thể thực hiện ngay, không cần cài framework hoặc hiểu sâu về screen reader. Mở page ở trạng thái mới, dùng `Tab` để đi qua các phần tử tương tác, dùng `Shift + Tab` để đi ngược, `Enter` hoặc `Space` để kích hoạt, và phím mũi tên khi component yêu cầu.

Một flow cơ bản có thể gồm bảy bước: đặt con trỏ tại đầu page, nhấn Tab, ghi nhận phần tử focus đầu tiên, tiếp tục đến input, nhập data hợp lệ, đi đến nút Submit, rồi gửi form. Expected result không chỉ là form submit thành công. Thứ tự focus phải hợp lý, focus phải nhìn thấy được, không có vùng tương tác bị bỏ qua và không xuất hiện keyboard trap khiến tester không thể rời khỏi component.

Focus bị che là một điểm mới đáng chú ý trong WCAG 2.2. Nếu khi Tab đến nút Submit mà một sticky header hoặc cookie banner che mất phần tử, user vẫn có thể đang ở đúng DOM element nhưng không nhìn thấy nơi mình đang thao tác. Tester nên ghi rõ phần tử bị che, chiều cao vùng che, trạng thái scroll và cách tái hiện.

<grid-content>
Keyboard checklist cho một flow login
> Dùng checklist này sau khi hiểu flow bình thường bằng chuột.
```markdown
1. Tab từ đầu page và ghi thứ tự focus.
2. Kiểm tra focus có nhìn thấy rõ trên nền sáng và tối.
3. Nhập email, password mà không dùng chuột.
4. Mở hoặc đóng password visibility bằng bàn phím.
5. Gửi form bằng Enter hoặc Space theo thiết kế.
6. Dùng Shift + Tab để quay lại và không bị kẹt.
```
```markdown
Khi ghi bug, luôn mô tả:
- Phần tử bị bỏ qua hoặc bị che.
- Phím đã nhấn và thứ tự focus quan sát được.
- Expected result, actual result và video nếu lỗi khó nhìn.
```
</grid-content>

## Kiểm tra form, label và thông báo lỗi thế nào?

Form là nơi người mới dễ ghi bug mơ hồ nhất. Một lỗi như “form không accessible” không giúp developer sửa nhanh. Tester cần chỉ ra input nào thiếu label, message nào không gắn với field, lỗi có được đọc lại khi focus quay về hay không, và người dùng có biết cách sửa hay không.

Hãy dùng một form có email, password và nút Login để thực hành. Trước tiên để trống toàn bộ field rồi submit. Sau đó nhập email sai định dạng, nhập password quá ngắn và thử xoá một field sau khi đã có lỗi. Với mỗi case, expected result nên mô tả cả vị trí hiển thị lỗi, nội dung dễ hiểu, trạng thái field và việc user có thể tiếp tục flow.

<table-testcase cols="5" rows="5" headers="ID|Tình huống|Thao tác|Expected result|Bằng chứng">
| A11Y-01 | Bỏ trống email | Submit khi email trống | Field được xác định rõ, lỗi nói cách sửa, focus hoặc thông báo dẫn user đến lỗi | Screenshot và steps |
| A11Y-02 | Email sai format | Nhập `abc` rồi submit | Không báo lỗi chung chung, message gắn đúng với email | Screenshot message |
| A11Y-03 | Chỉ dùng bàn phím | Tab qua form và submit | Thứ tự focus hợp lý, focus nhìn thấy, submit được | Video ngắn |
| A11Y-04 | Sửa lỗi | Nhập lại email hợp lệ | Lỗi cũ biến mất hoặc cập nhật theo rule | Before/after |
</table-testcase>

Nếu form có lỗi, tester đừng chỉ kiểm tra màu đỏ. Người dùng có thể không phân biệt màu, không nhìn rõ hoặc đang dùng công nghệ hỗ trợ. Hãy quan sát cả text message, vị trí, focus và mối quan hệ giữa field với lỗi. Đây là kỹ năng nền tảng liên quan trực tiếp đến cách viết [test case rõ ràng cho tester mới](/blogs/tester-moi-sai-lam-o-dau-khi-viet-test-case).

![Minimalist flat vector UI design, premium professional EdTech editorial artwork, 21:9 wide section image showing accessible form testing, clean bento-grid composition with strong negative space, Paper White background #fafafa, Zinc-900 content #18181b, T5Edu Blue accent #1a73e8, Amber highlight #f59e0b, left browser form card with English labels "Email", "Password", "Login", a red inline error card labeled "Enter a valid email", right evidence panel with keyboard icon, focus ring and magnifying glass, arrows showing field to error relationship, flat vector EdTech style, one-pixel borders, no gradients, no photorealism, no dense text, no logos, no watermark](https://files.manuscdn.com/user_upload_by_module/session_file/310519663091035343/laAqJRoUCdiueFNC.png)

## Công cụ tự động giúp gì và không thể thay tester ở đâu?

Accessibility checker có thể giúp phát hiện nhanh một số vấn đề như thiếu thuộc tính, màu tương phản thấp hoặc cấu trúc HTML đáng ngờ. Công cụ này hữu ích khi tester muốn quét nhiều page hoặc bắt lỗi lặp lại sớm. Nhưng kết quả scan không chứng minh rằng user thật sự hoàn thành được task.

Có những câu hỏi cần người kiểm tra. Alt text có thể tồn tại nhưng mô tả sai mục đích của ảnh. Heading có thể đúng cấp nhưng nội dung vẫn khó hiểu. Một component có thể không bị báo lỗi tự động nhưng vẫn không dùng được bằng bàn phím. Vì vậy, hãy xem automated check là tín hiệu để điều tra, không phải giấy chứng nhận PASS.

Quy trình thực tế cho người mới có thể gồm ba lớp. Lớp đầu là scan nhanh để tìm lỗi lặp lại. Lớp hai là keyboard và focus cho flow quan trọng. Lớp ba là kiểm tra bằng user flow, form và nội dung hiển thị, sau đó chuyển case khó sang người có kinh nghiệm hoặc accessibility specialist.

<dropdown-content>
### Vì sao không nên chỉ dùng một công cụ scan?

Công cụ tự động chỉ nhìn được các pattern mà nó có thể suy luận. Nó không hiểu đầy đủ mục đích của ảnh, chất lượng câu chữ, tính hợp lý của thứ tự focus hoặc việc thông báo lỗi có giúp user hoàn thành tác vụ hay không.

### Người mới có cần học thuộc WCAG 2.2 không?

Không cần học thuộc trước khi bắt đầu. Hãy học cách đọc một success criterion, chuyển nó thành câu hỏi kiểm thử, chạy trên một flow nhỏ và ghi evidence. Khi gặp lỗi lặp lại, đọc thêm Understanding WCAG để hiểu nguyên nhân và cách kiểm chứng.

### Accessibility testing có thay manual testing không?

Không. Accessibility testing là một góc kiểm tra bổ sung trong manual testing. Tester vẫn cần hiểu requirement, expected result, risk, test data và cách viết bug report có thể tái hiện.
</dropdown-content>

## Ghi bug accessibility testing thế nào để developer sửa được?

Một bug report tốt cần chỉ ra user bị cản trở ở bước nào, không chỉ gọi tên tiêu chuẩn. Title nên mô tả hành vi và thành phần, ví dụ “Không thể gửi form Login bằng bàn phím vì focus bị kẹt ở password visibility”. Trong description, ghi environment, precondition, steps, actual result, expected result và evidence.

Nếu biết success criterion liên quan, tester có thể ghi ở phần reference của bug, nhưng không nên dùng tên criterion thay cho mô tả lỗi. Một report rõ ràng sẽ giúp team ưu tiên theo ảnh hưởng: không thể hoàn thành login thường nghiêm trọng hơn một heading chưa tối ưu trên trang phụ.

![Minimalist flat vector UI design, premium professional EdTech editorial artwork, 21:9 wide section image for an accessibility bug report workflow, clean horizontal bento-grid layout with strong negative space, Paper White background #fafafa, Zinc-900 content #18181b, T5Edu Blue accent #1a73e8, Amber highlight #f59e0b, left card labeled "Reproduce" with keyboard and browser icons, center card labeled "Evidence" with screenshot frame, right card labeled "Expected / Actual" connected by solid blue arrows, short English labels only, flat professional EdTech vector style, subtle liquid-glass layers, no gradients, no photorealism, no logos, no dense paragraphs, no watermark](https://files.manuscdn.com/user_upload_by_module/session_file/310519663091035343/LlJorPZADbtzvuUC.png)

## Tổng kết

- Bắt đầu accessibility testing bằng một flow nhỏ, kiểm tra keyboard, focus, form, heading, alt text và contrast.
- WCAG 2.2 là nền để đặt câu hỏi kiểm thử, còn Easy Checks chỉ là first review, không thay thế đánh giá đầy đủ.
- Automated checker giúp tìm tín hiệu nhanh, nhưng tester phải xác nhận bằng thao tác và evidence thực tế.
- Nếu muốn củng cố nền tảng test case, API và tư duy kiểm thử trước khi mở rộng sang accessibility, hãy xem [khóa học Testing cơ bản](/courses/testing-co-ban) và [luyện thi ISTQB](/istqb).

Flow nào trong dự án hiện tại sẽ thay đổi kết luận nếu tester chỉ dùng chuột mà không thử bàn phím?

## Hashtag

> accessibility testing, wcag 2.2, manual testing, qa beginner, web testing
