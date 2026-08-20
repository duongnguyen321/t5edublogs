# Viết Bug Report Với AI Cho Tester Mới

> AI có thể giúp tester mới sắp xếp bug report nhanh hơn, nhưng không thể thay thế việc tái hiện lỗi, phân biệt expected result và actual result, hoặc kiểm tra bằng chứng. Bài viết hướng dẫn quy trình nền tảng để biến một quan sát mơ hồ thành báo cáo có thể reproduce.

![Square 1:1 cover illustration for an article titled 'AI hỗ trợ viết bug report cho tester mới'. The visual must communicate a beginner tester turning vague failure evidence into a reproducible bug report. Composition: a clean bento-grid with a central document card labeled 'Bug report', left small cards labeled 'Triệu chứng' and 'Evidence', right small card labeled 'Reproduce', solid blue arrows flowing left to right, one amber warning badge labeled 'Kiểm chứng'. Exact Vietnamese labels: 'Bug report', 'Triệu chứng', 'Evidence', 'Reproduce', 'Kiểm chứng'. Minimalist flat vector UI design. Premium professional EdTech editorial artwork. Clean bento-grid composition with strong negative space. Paper White background #fafafa, Zinc-900 content #18181b, T5Edu Blue accent #1a73e8, Amber highlight #f59e0b, Subtle one-pixel borders and restrained liquid-glass layers. Simple flat icons and geometric shapes, no people, no faces, no hands, no 3D, no glossy plastic, no photorealism, no dramatic lighting, no purple, no violet, no pink, no neon, no logo, no watermark.](https://files.manuscdn.com/user_upload_by_module/session_file/310519663091035343/uBGrzSIvlgRHtCcG.png)

## Vì sao bug report là kỹ năng nền tảng quan trọng với tester mới?

Bug report là tài liệu để developer kiểm tra lại một hành vi bất thường. Một report rõ ràng giúp team triage, sửa lỗi và quyết định release.

| Report cần trả lời | Ví dụ câu hỏi |
| --- | --- |
| Known state | Tester bắt đầu từ trạng thái nào? |
| Action | Tester đã làm chính xác gì? |
| Actual result | Phần mềm đã phản hồi ra sao? |
| Expected result | Kết quả nào cần xảy ra? |

[Hướng dẫn bug report của QA Wolf](https://www.qawolf.com/blog/what-makes-a-great-bug-report) cũng nhấn mạnh steps to reproduce, expected/actual result và evidence.

Nếu đang học nghề, hãy củng cố kiến thức trong [khóa Testing cơ bản](https://t5edu.site/courses/testing-co-ban). AI chỉ sắp xếp thông tin đã thu thập, không thay tester tương tác và kiểm chứng sản phẩm.

<grid-content>
Bốn mảnh ghép của một bug report có thể xử lý
> Hãy thu thập sự thật trước, rồi mới nhờ AI sắp xếp câu chữ.
```markdown
**Expected và actual**

Expected là kết quả cần xảy ra theo requirement. Actual là điều tester thật sự quan sát được.
```

```markdown
**Steps to reproduce**

Các bước đánh số, bắt đầu từ known state và có giá trị input cụ thể.
```

```markdown
**Evidence**

Screenshot, video, log, request hoặc response giúp người khác xác minh nhanh.
```

```markdown
**Environment**

Browser, OS, device, version và môi trường như staging hoặc production.
```
</grid-content>

## Tester mới nên quan sát gì trước khi mở AI?

Trước khi mở chat tool, hãy ghi nhận sự kiện bằng ngôn ngữ quan sát được. Không kết luận “backend bị lỗi” chỉ vì một toast không xuất hiện.

### Quy trình quan sát 5 bước

1. Đưa ứng dụng về **known state**, ví dụ tài khoản test mới trên staging.
2. Thực hiện từng action với input cụ thể.
3. Ghi expected result từ user story, acceptance criteria hoặc test case.
4. Ghi actual result đúng như quan sát được.
5. Reproduce ít nhất một lần và lưu evidence phù hợp.

Kết luận nguyên nhân quá sớm sẽ làm report khó kiểm chứng và dễ khiến team đi sai hướng.

| Câu hỏi | Ví dụ ghi nhận đúng | Ví dụ nên tránh |
| --- | --- | --- |
| Đã bắt đầu từ đâu? | Tài khoản user mới, staging, Chrome 126 | App bị lỗi |
| Đã làm gì? | Nhập `abc@example.com`, bấm Lưu | Test form |
| Expected là gì? | Hiện thông báo lưu thành công | Đáng lẽ phải đúng |
| Actual là gì? | Spinner chạy hơn 10 giây, không có thông báo | API chết |

![Wide 21:9 educational diagram explaining the observation-to-bug-report flow for a beginner tester. Layout: four horizontal cards from left to right labeled 'Known state', 'Action', 'Expected', 'Actual', followed by an evidence card labeled 'Bằng chứng'. Solid T5Edu Blue arrows connect each card in sequence, and a dashed Amber arrow loops from 'Actual' back to 'Re-check'. Exact Vietnamese labels: 'Known state', 'Action', 'Expected', 'Actual', 'Bằng chứng', 'Re-check'. Minimalist flat vector UI design. Premium professional EdTech editorial artwork. Clean bento-grid composition with strong negative space. Paper White and Zinc-50 background #fafafa, Zinc-900 content #18181b, T5Edu Blue accent #1a73e8, Amber highlight #f59e0b, Subtle one-pixel borders and restrained liquid-glass layers. Simple flat icons and geometric shapes, no people, no faces, no hands, no 3D, no glossy plastic, no photorealism, no dramatic lighting, no purple, no violet, no pink, no neon, no logo, no watermark.](https://files.manuscdn.com/user_upload_by_module/session_file/310519663091035343/VIqSSMPQwCdLGVFD.png)

<multiple-choice correct="C" select="single">
Tester nên làm gì trước khi nhờ AI viết bug report?
- A: Yêu cầu AI đoán root cause từ một câu mô tả ngắn
- B: Chụp một ảnh bất kỳ rồi gửi ngay cho developer
- C: Đưa ứng dụng về known state và ghi lại expected, actual, steps
- D: Đổi browser liên tục để tìm một kết quả khác
</multiple-choice>

## AI giúp tester mới ở phần nào mà không làm thay phần kiểm thử?

### AI nên làm gì?

- Sắp xếp ghi chú rời rạc thành cấu trúc report.
- Phát hiện field còn thiếu.
- Đề xuất title cụ thể hơn.
- Rút gọn mô tả dài mà không đổi facts.

### Prompt an toàn mẫu

```text
Hãy sắp xếp các ghi chú sau thành bug report gồm title, environment, steps, expected, actual và evidence.
Chỉ dùng dữ kiện đã có.
Nếu thiếu thông tin, ghi CẦN BỔ SUNG, không suy đoán root cause.
```

### Trước khi gửi dữ liệu vào AI

- Không gửi secret, token, dữ liệu cá nhân hoặc production data nhạy cảm.
- Thay email, ID và payload bằng dữ liệu giả khi cần minh họa.
- Mở lại sản phẩm và kiểm tra từng step sau khi AI viết lại.
- Xóa mọi chi tiết AI tự thêm trước khi tạo ticket.

<dropdown-content>
AI có được tự đoán root cause không?
> Câu trả lời dành cho tester mới khi dùng AI hỗ trợ bug report.
```markdown
Không nên. Root cause là giả thuyết cần được kiểm tra bằng log, network trace, code hoặc trao đổi với developer. Report ban đầu nên mô tả symptom, expected result, actual result và evidence. Nếu tester có giả thuyết, hãy đặt nó trong phần “Hypothesis” và ghi rõ đây chưa phải kết luận.

Một câu như “Có thể request bị timeout vì response chưa về sau 10 giây” hữu ích hơn câu “Backend chắc chắn hỏng”. Câu đầu giữ được hướng điều tra mà không biến phỏng đoán thành fact.
```
</dropdown-content>

## Làm thế nào viết title và reproduction steps để developer chạy lại được?

### Công thức viết title

`[Area] - [Action] - [Unexpected result]`

Ví dụ: `Profile - Bấm Lưu email hợp lệ - spinner không kết thúc trên staging`.

| Nên có trong step | Vì sao cần |
| --- | --- |
| Known state | Người khác biết bắt đầu từ đâu |
| Một action mỗi bước | Tránh nhập nhằng khi reproduce |
| Tên button hoặc field chính xác | Giảm lỗi thao tác |
| Giá trị input cụ thể | Tái hiện đúng điều kiện |
| Timing nếu có liên quan | Phân biệt lỗi tức thời và timeout |

Sau khi viết, nhờ một người chưa xem lỗi chạy theo report. Nếu họ không biết phải làm gì, report chưa đủ rõ.

<table-testcase cols="5" rows="3" headers="ID|Known state|Steps|Expected|Actual">
| TC01 | User mới ở trang Profile | Nhập email hợp lệ, bấm Lưu | Hiện thông báo thành công | Spinner chạy hơn 10 giây |
| TC02 | User mới ở trang Profile | Nhập email thiếu @, bấm Lưu | Hiện validation message | Form submit không phản hồi |
| TC03 | User đã lưu email | Reload trang Profile | Email mới vẫn hiển thị | Trang hiển thị email cũ |
</table-testcase>

![Wide 21:9 educational diagram showing a reproducible bug report template. Layout: a large left document labeled 'Title' above numbered cards '1 Known state', '2 Action', '3 Input', '4 Expected vs Actual', with a right evidence panel labeled 'Screenshot + Log'. Solid blue connectors point from each numbered card to the evidence panel, and an amber badge says 'Không đoán'. Exact Vietnamese labels: 'Title', 'Known state', 'Action', 'Input', 'Expected', 'Actual', 'Screenshot + Log', 'Không đoán'. Minimalist flat vector UI design. Premium professional EdTech editorial artwork. Clean bento-grid composition with strong negative space. Paper White and Zinc-50 background #fafafa, Zinc-900 content #18181b, T5Edu Blue accent #1a73e8, Amber highlight #f59e0b, Subtle one-pixel borders and restrained liquid-glass layers. Simple flat icons and geometric shapes, no people, no faces, no hands, no 3D, no glossy plastic, no photorealism, no dramatic lighting, no purple, no violet, no pink, no neon, no logo, no watermark.](https://files.manuscdn.com/user_upload_by_module/session_file/310519663091035343/ftxSKAtHqeSgLBuG.png)

## Evidence nào đủ để report không bị trả lại?

### Chọn evidence theo loại lỗi

| Loại lỗi | Evidence nên ưu tiên |
| --- | --- |
| Hiển thị | Screenshot có cả vùng liên quan và trạng thái sau action |
| Timing, animation, nhiều bước | Video hoặc screen recording |
| Error kỹ thuật | Console log, request và response |
| Form hoặc ticket có nhiều field | Dùng template để không bỏ sót version, browser, log và screenshot |

[GitHub Docs về issue forms](https://docs.github.com/en/communities/using-templates-to-encourage-useful-issues-and-pull-requests/syntax-for-issue-forms) minh họa cách yêu cầu các field bắt buộc trong issue form.

### Ghi severity

- Dựa trên **impact**, không dựa trên cảm xúc.
- Mất dữ liệu hoặc chặn đăng nhập thường nghiêm trọng hơn lệch một pixel.
- Nếu chưa biết quy ước của team, mô tả impact và hỏi người phụ trách.
- Không tự gán `critical` khi chưa có căn cứ.

## Checklist 10 phút trước khi gửi bug report là gì?

Hãy đọc report như một developer không có mặt lúc lỗi xảy ra.

Nếu AI đã viết lại report, so sánh từng câu với note gốc. Xóa các chi tiết AI tự thêm, đặc biệt là:

- Browser version.
- API status.
- Root cause.
- Mức độ ảnh hưởng.

Đây là ranh giới giữa “AI giúp viết nhanh” và “AI tạo ra một ticket nghe hợp lý nhưng sai”.

Một tester mới có thể dùng checklist sau trong công việc đầu tiên:

1. Title nêu rõ feature, action và failure.
2. Environment có browser, OS, device, build và môi trường.
3. Steps bắt đầu từ known state và đánh số.
4. Expected lấy từ requirement hoặc acceptance criteria.
5. Actual chỉ mô tả điều đã quan sát.
6. Evidence mở được và không chứa secret.
7. Lỗi đã được reproduce lại ít nhất một lần.
8. Duplicate hoặc issue liên quan đã được tìm kiếm.
9. Severity dựa trên impact và quy ước của team.
10. AI không còn thêm dữ kiện chưa kiểm chứng.

Nếu muốn luyện cách phân tích case trước khi viết report, hãy xem [bài Tester mới sai lầm ở đâu khi viết test case](https://t5edu.site/blogs/tester-moi-sai-lam-o-dau-khi-viet-test-case), sau đó thực hành với các case trong [lộ trình học tester từ zero](https://t5edu.site/blogs/lo-trinh-hoc-tester-tu-zero-den-chuyen-nghiep).

![Wide 21:9 educational diagram showing the final bug report review checklist. Layout: a left checklist card labeled '10 phút review' with blue check icons, a center AI editor card labeled 'AI chỉ sắp xếp', and a right release decision card labeled 'Gửi ticket'. Solid blue arrows connect checklist to AI editor to ticket, while a dashed amber line loops from ticket back to 'Reproduce'. Exact Vietnamese labels: '10 phút review', 'AI chỉ sắp xếp', 'Gửi ticket', 'Reproduce', 'Không có secret'. Minimalist flat vector UI design. Premium professional EdTech editorial artwork. Clean bento-grid composition with strong negative space. Paper White and Zinc-50 background #fafafa, Zinc-900 content #18181b, T5Edu Blue accent #1a73e8, Amber highlight #f59e0b, Subtle one-pixel borders and restrained liquid-glass layers. Simple flat icons and geometric shapes, no people, no faces, no hands, no 3D, no glossy plastic, no photorealism, no dramatic lighting, no purple, no violet, no pink, no neon, no logo, no watermark.](https://files.manuscdn.com/user_upload_by_module/session_file/310519663091035343/OjjucsfqsPyVSWkK.png)

## Tổng kết

- Bug report tốt bắt đầu từ known state, expected result, actual result và bước reproduce rõ ràng.
- AI nên làm editor giúp sắp xếp và phát hiện field thiếu, không được thay tester xác minh sản phẩm.
- Evidence phải giúp người khác tái hiện hoặc chẩn đoán, đồng thời không chứa secret hay dữ liệu nhạy cảm.
- Nếu đang học testing cơ bản, hãy luyện một report hoàn chỉnh cho mỗi lỗi thay vì chỉ ghi một câu kết luận.

Nếu bạn là tester mới và muốn xây nền tảng từ requirement đến test case và bug report, hãy học [khóa Testing cơ bản](https://t5edu.site/courses/testing-co-ban). Câu hỏi tự đánh giá: nếu developer chỉ có report của bạn mà không có cuộc gọi bổ sung, họ có thể chạy lại lỗi không?

## Hashtag
> ai testing, bug report, manual testing, tester moi, qa
