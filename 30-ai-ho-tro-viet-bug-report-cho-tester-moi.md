# AI hỗ trợ viết bug report cho tester mới: Từ triệu chứng đến bằng chứng

> AI có thể giúp tester mới sắp xếp bug report nhanh hơn, nhưng không thể thay thế việc tái hiện lỗi, phân biệt expected result và actual result, hoặc kiểm tra bằng chứng. Bài viết hướng dẫn quy trình nền tảng để biến một quan sát mơ hồ thành báo cáo có thể reproduce.

![Square 1:1 editorial cover for an article titled 'AI hỗ trợ viết bug report cho tester mới'. The visual must communicate a beginner tester turning vague failure evidence into a reproducible bug report. Composition: a clean bento-grid with a central document card labeled 'Bug report', left small cards labeled 'Triệu chứng' and 'Evidence', right small card labeled 'Reproduce', solid blue arrows flowing left to right, one amber warning badge labeled 'Kiểm chứng'. Exact Vietnamese labels: 'Bug report', 'Triệu chứng', 'Evidence', 'Reproduce', 'Kiểm chứng'. Minimalist flat vector UI design, premium professional EdTech editorial artwork, clean bento-grid composition with strong negative space, Paper White or Zinc-50 background #fafafa, Zinc-900 content #18181b, T5Edu Blue accent #1a73e8, Amber highlight #f59e0b, subtle one-pixel borders and restrained liquid-glass layers, simple flat icons and geometric blocks, no people, no faces, no hands, no 3D, no glossy plastic, no photorealism, no dramatic lighting, no purple, no violet, no pink, no neon, no logo, no watermark.](https://files.manuscdn.com/user_upload_by_module/session_file/310519663091035343/vcmmRehEmkSPpKMp.png)

## Vì sao bug report là kỹ năng nền tảng quan trọng với tester mới?

Bug report là tài liệu mô tả một hành vi không phù hợp của phần mềm, cách tái hiện và bằng chứng để developer có thể kiểm tra lại. Với tester mới, viết được một report rõ ràng quan trọng hơn việc biết thật nhiều tool, vì report là đầu ra trực tiếp mà team dùng để triage, sửa lỗi và quyết định release.

Một report tốt không chỉ nói “nút không hoạt động”. Nó trả lời bốn câu hỏi: tester đã ở trạng thái nào, đã làm chính xác gì, phần mềm thực tế phản hồi ra sao và kết quả nào được mong đợi. [Hướng dẫn bug report của QA Wolf](https://www.qawolf.com/blog/what-makes-a-great-bug-report) nhấn mạnh expected và actual result, bước reproduce, bằng chứng hình ảnh cùng log kỹ thuật là các thành phần cốt lõi.

Nếu đang học nghề, tester nên củng cố các khái niệm trong [khóa Testing cơ bản](/courses/testing-co-ban) trước khi dùng AI để viết. AI chỉ giúp diễn đạt thông tin đã thu thập, không tạo ra sự thật thay cho việc interact với sản phẩm.

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

Trước khi mở một chat tool, hãy tự ghi nhận sự kiện bằng ngôn ngữ quan sát được. Đừng bắt đầu bằng kết luận như “backend bị lỗi” khi tester mới chỉ thấy một toast không xuất hiện. Kết luận nguyên nhân quá sớm làm report khó kiểm chứng và dễ khiến team đi sai hướng.

Quy trình nền tảng gồm năm bước. Đầu tiên, đưa ứng dụng về known state, chẳng hạn đăng nhập bằng tài khoản test mới và mở đúng trang. Tiếp theo, thực hiện từng hành động với input cụ thể. Sau đó ghi expected result từ user story, acceptance criteria hoặc test case. Cuối cùng, lặp lại lỗi ít nhất một lần và lưu evidence phù hợp.

| Câu hỏi | Ví dụ ghi nhận đúng | Ví dụ nên tránh |
| --- | --- | --- |
| Đã bắt đầu từ đâu? | Tài khoản user mới, staging, Chrome 126 | App bị lỗi |
| Đã làm gì? | Nhập `abc@example.com`, bấm Lưu | Test form |
| Expected là gì? | Hiện thông báo lưu thành công | Đáng lẽ phải đúng |
| Actual là gì? | Spinner chạy hơn 10 giây, không có thông báo | API chết |

![Wide 21:9 educational diagram explaining the observation-to-bug-report flow for a beginner tester. Layout: four horizontal cards from left to right labeled 'Known state', 'Action', 'Expected', 'Actual', followed by an evidence card labeled 'Bằng chứng'. Solid T5Edu Blue arrows connect each card in sequence, and a dashed Amber arrow loops from 'Actual' back to 'Re-check'. Exact Vietnamese labels: 'Known state', 'Action', 'Expected', 'Actual', 'Bằng chứng', 'Re-check'. Minimalist flat vector UI design, premium professional EdTech editorial artwork, clean bento-grid composition with strong negative space, Paper White and Zinc-50 background #fafafa, Zinc-900 content #18181b, T5Edu Blue accent #1a73e8, Amber highlight #f59e0b, subtle one-pixel borders and restrained liquid-glass layers, simple flat icons and geometric blocks, no people, no faces, no hands, no 3D, no glossy plastic, no photorealism, no dramatic lighting, no purple, no violet, no pink, no neon, no logo, no watermark.](https://files.manuscdn.com/user_upload_by_module/session_file/310519663091035343/SNBNFoeGrhkgdSUi.png)

<multiple-choice correct="C" select="single">
Tester nên làm gì trước khi nhờ AI viết bug report?
- A: Yêu cầu AI đoán root cause từ một câu mô tả ngắn
- B: Chụp một ảnh bất kỳ rồi gửi ngay cho developer
- C: Đưa ứng dụng về known state và ghi lại expected, actual, steps
- D: Đổi browser liên tục để tìm một kết quả khác
</multiple-choice>

## AI giúp tester mới ở phần nào mà không làm thay phần kiểm thử?

AI phù hợp với các việc có tính biên tập: biến ghi chú rời rạc thành cấu trúc report, phát hiện trường còn thiếu, đề xuất tiêu đề cụ thể hơn hoặc rút gọn đoạn mô tả dài. Tester có thể đưa vào prompt các dữ kiện đã xác minh và yêu cầu AI không suy đoán nguyên nhân.

Một prompt an toàn nên nói rõ vai trò, format đầu ra và giới hạn dữ liệu. Ví dụ: “Hãy sắp xếp các ghi chú sau thành bug report gồm title, environment, steps, expected, actual và evidence. Chỉ dùng dữ kiện đã có. Nếu thiếu thông tin, ghi `CẦN BỔ SUNG`, không suy đoán root cause.” Cách này biến AI thành editor, không biến AI thành người thực thi test.

Không đưa secret, token, dữ liệu cá nhân hay nội dung production nhạy cảm vào tool chưa được team phê duyệt. Nếu cần minh họa, thay email, ID và payload bằng data giả. Tester vẫn phải mở lại sản phẩm, kiểm tra từng step và sửa mọi câu AI diễn đạt sai trước khi tạo ticket.

<dropdown-content>
AI có được tự đoán root cause không?
> Câu trả lời dành cho tester mới khi dùng AI hỗ trợ bug report.
```markdown
Không nên. Root cause là giả thuyết cần được kiểm tra bằng log, network trace, code hoặc trao đổi với developer. Report ban đầu nên mô tả symptom, expected result, actual result và evidence. Nếu tester có giả thuyết, hãy đặt nó trong phần “Hypothesis” và ghi rõ đây chưa phải kết luận.

Một câu như “Có thể request bị timeout vì response chưa về sau 10 giây” hữu ích hơn câu “Backend chắc chắn hỏng”. Câu đầu giữ được hướng điều tra mà không biến phỏng đoán thành fact.
```
</dropdown-content>

## Làm thế nào viết title và reproduction steps để developer chạy lại được?

Title nên mô tả khu vực, hành động và kết quả bất thường. Công thức đơn giản là `[Area] - [Action] - [Unexpected result]`, chẳng hạn “Profile - Bấm Lưu email hợp lệ - spinner không kết thúc trên staging”. Title như “Không lưu được” quá ngắn, khó tìm duplicate và không giúp triage.

Reproduction steps cần bắt đầu từ trạng thái biết trước. Mỗi bước chỉ nên chứa một hành động, gọi đúng tên button hoặc field, nêu giá trị input và ghi timing khi timing là điều kiện của lỗi. Sau khi viết, tester nên đưa report cho một người chưa xem lỗi và nhờ họ chạy theo step. Nếu họ không thể hiểu phải làm gì, report vẫn chưa đủ rõ.

<table-testcase cols="5" rows="3" headers="ID|Known state|Steps|Expected|Actual">
| TC01 | User mới ở trang Profile | Nhập email hợp lệ, bấm Lưu | Hiện thông báo thành công | Spinner chạy hơn 10 giây |
| TC02 | User mới ở trang Profile | Nhập email thiếu @, bấm Lưu | Hiện validation message | Form submit không phản hồi |
| TC03 | User đã lưu email | Reload trang Profile | Email mới vẫn hiển thị | Trang hiển thị email cũ |
</table-testcase>

![Wide 21:9 educational diagram showing a reproducible bug report template. Layout: a large left document labeled 'Title' above numbered cards '1 Known state', '2 Action', '3 Input', '4 Expected vs Actual', with a right evidence panel labeled 'Screenshot + Log'. Solid blue connectors point from each numbered card to the evidence panel, and an amber badge says 'Không đoán'. Exact Vietnamese labels: 'Title', 'Known state', 'Action', 'Input', 'Expected', 'Actual', 'Screenshot + Log', 'Không đoán'. Minimalist flat vector UI design, premium professional EdTech editorial artwork, clean bento-grid composition with strong negative space, Paper White and Zinc-50 background #fafafa, Zinc-900 content #18181b, T5Edu Blue accent #1a73e8, Amber highlight #f59e0b, subtle one-pixel borders and restrained liquid-glass layers, simple flat icons and geometric blocks, no people, no faces, no hands, no 3D, no glossy plastic, no photorealism, no dramatic lighting, no purple, no violet, no pink, no neon, no logo, no watermark.](https://files.manuscdn.com/user_upload_by_module/session_file/310519663091035343/vmDaXnUvQVYfhkmv.png)

## Evidence nào đủ để report không bị trả lại?

Evidence không phải càng nhiều càng tốt. Hãy chọn loại giúp người khác xác minh đúng hành vi. Screenshot phù hợp với lỗi hiển thị; video phù hợp với lỗi liên quan đến timing, animation hoặc nhiều bước; console log và network details hữu ích khi có error kỹ thuật. Với lỗi UI, ảnh nên cho thấy cả vùng liên quan và trạng thái sau hành động, không chỉ cắt sát một icon.

[GitHub Docs về issue forms](https://docs.github.com/en/communities/using-templates-to-encourage-useful-issues-and-pull-requests/syntax-for-issue-forms) cho thấy form có thể yêu cầu các field như what happened, version, browser, log và screenshot. Tester mới có thể học cách dùng cấu trúc này ngay cả khi team đang dùng Jira hoặc tool khác, vì mục tiêu là không bỏ quên thông tin quan trọng.

Hãy ghi severity theo impact, không theo cảm xúc. Một lỗi làm mất dữ liệu hoặc chặn đăng nhập có impact khác lỗi lệch một pixel. Nếu chưa hiểu quy ước severity của team, hãy mô tả impact và hỏi người phụ trách thay vì tự gán “critical”.

## Checklist 10 phút trước khi gửi bug report là gì?

Hãy đọc report như một developer không có mặt lúc tester phát hiện lỗi. Tester có thể tự hỏi: người khác có biết bắt đầu từ đâu không, input có đủ cụ thể không, expected có nguồn không, actual có mô tả quan sát được không, evidence có đúng phiên bản không và lỗi có tái hiện lại được không.

Nếu AI đã viết lại report, hãy so sánh từng câu với note gốc. Xóa mọi chi tiết AI tự thêm, đặc biệt là browser version, API status, root cause hoặc mức độ ảnh hưởng mà tester chưa kiểm tra. Đây là bước phân biệt giữa “AI giúp viết nhanh” và “AI tạo ra một ticket nghe hợp lý nhưng sai”.

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

Nếu muốn luyện cách phân tích case trước khi viết report, hãy xem [bài Tester mới sai lầm ở đâu khi viết test case](/blogs/tester-moi-sai-lam-o-dau-khi-viet-test-case), sau đó thực hành với các case trong [lộ trình học tester từ zero](/blogs/lo-trinh-hoc-tester-tu-zero-den-chuyen-nghiep).

![Wide 21:9 educational diagram showing the final bug report review checklist. Layout: a left checklist card labeled '10 phút review' with blue check icons, a center AI editor card labeled 'AI chỉ sắp xếp', and a right release decision card labeled 'Gửi ticket'. Solid blue arrows connect checklist to AI editor to ticket, while a dashed amber line loops from ticket back to 'Reproduce'. Exact Vietnamese labels: '10 phút review', 'AI chỉ sắp xếp', 'Gửi ticket', 'Reproduce', 'Không có secret'. Minimalist flat vector UI design, premium professional EdTech editorial artwork, clean bento-grid composition with strong negative space, Paper White and Zinc-50 background #fafafa, Zinc-900 content #18181b, T5Edu Blue accent #1a73e8, Amber highlight #f59e0b, subtle one-pixel borders and restrained liquid-glass layers, simple flat icons and geometric blocks, no people, no faces, no hands, no 3D, no glossy plastic, no photorealism, no dramatic lighting, no purple, no violet, no pink, no neon, no logo, no watermark.](https://files.manuscdn.com/user_upload_by_module/session_file/310519663091035343/CHDzqMryzjYCFqlR.png)

## Tổng kết

- Bug report tốt bắt đầu từ known state, expected result, actual result và bước reproduce rõ ràng.
- AI nên làm editor giúp sắp xếp và phát hiện field thiếu, không được thay tester xác minh sản phẩm.
- Evidence phải giúp người khác tái hiện hoặc chẩn đoán, đồng thời không chứa secret hay dữ liệu nhạy cảm.
- Nếu đang học testing cơ bản, hãy luyện một report hoàn chỉnh cho mỗi lỗi thay vì chỉ ghi một câu kết luận.

Nếu bạn là tester mới và muốn xây nền tảng từ requirement đến test case và bug report, hãy học [khóa Testing cơ bản](/courses/testing-co-ban). Câu hỏi tự đánh giá: nếu developer chỉ có report của bạn mà không có cuộc gọi bổ sung, họ có thể chạy lại lỗi không?

## Hashtag

> ai testing, bug report, manual testing, tester moi, QA
