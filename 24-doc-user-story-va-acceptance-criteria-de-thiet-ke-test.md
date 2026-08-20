# Đọc User Story để Viết Test

> Hiểu đúng user story và acceptance criteria giúp tester mới biến yêu cầu còn mơ hồ thành các scenario có thể kiểm tra, đặt câu hỏi đúng và tránh viết test case theo suy đoán.

![Square 1:1 educational editorial illustration for T5Edu about a beginner tester reading a user story and acceptance criteria. Minimalist flat vector UI design, premium professional EdTech editorial artwork, clean bento-grid composition with strong negative space, Paper White background #fafafa, Zinc-900 content #18181b, T5Edu Blue accent #1a73e8, Amber highlight #f59e0b, subtle one-pixel borders, rounded cards, calm precise instructional mood. Show a young Vietnamese tester at a desk reviewing three connected cards labeled exactly “User story”, “Điều kiện chấp nhận”, and “Scenario kiểm thử”, with a simple arrow from left to right, small checkmarks and question marks, no code, no automation tools, no logos, no realistic text paragraphs, no gradients, no 3D, no photorealism, no clutter.](https://files.manuscdn.com/user_upload_by_module/session_file/310519663091035343/zumnKtgaMuFmQuqE.png)

Khi mới vào nghề, tester thường nhận một ticket vài dòng mô tả rồi được giao nhiệm vụ “test giúp”. Phản xạ phổ biến là mở ứng dụng, bấm thử vài trường hợp và viết test case ngay.

Cách làm này dễ bỏ sót điều kiện quan trọng, vì tester đang kiểm tra theo suy đoán thay vì theo điều sản phẩm cần đáp ứng.

Bài viết này giúp **tester mới, intern và fresher** đọc user story cùng acceptance criteria, từ đó tách yêu cầu thành điều kiện kiểm thử và tạo bộ scenario đầu tiên.

Phạm vi chỉ tập trung vào kỹ năng phân tích yêu cầu nền tảng. Bạn chưa cần biết automation, API hay framework kiểm thử để áp dụng.

## User story và acceptance criteria khác nhau thế nào?

User story mô tả một nhu cầu từ góc nhìn người sử dụng theo mẫu “Với vai trò là [người dùng], tôi muốn [hành động], để [lợi ích]”.

Câu này cho tester biết ai cần gì và vì sao, nhưng thường chưa đủ chi tiết để kiểm tra mọi điều kiện.

Acceptance criteria, hay điều kiện chấp nhận, là các điều kiện work item phải đáp ứng để được xem là hoàn thành. [Atlassian](https://www.atlassian.com/work-management/project-management/acceptance-criteria) mô tả chúng là những yêu cầu định trước.

[Scrum.org](https://www.scrum.org/resources/blog/how-use-acceptance-criteria) nhấn mạnh chúng giúp team làm rõ scope, giới hạn và outcome.

| Thành phần | Câu hỏi tester cần trả lời | Ví dụ |
|---|---|---|
| User story | Ai cần gì và vì sao? | Người mua muốn lưu địa chỉ giao hàng để thanh toán nhanh hơn |
| Acceptance criteria | Điều kiện nào phải đúng? | Địa chỉ hợp lệ được lưu và hiển thị ở lần thanh toán sau |
| Test scenario | Cần kiểm tra nhóm hành vi nào? | Lưu địa chỉ hợp lệ, bỏ trống trường bắt buộc, sửa địa chỉ |
| Test case | Thao tác cụ thể và kết quả mong đợi là gì? | Nhập dữ liệu, bấm Lưu, kiểm tra thông báo và dữ liệu hiển thị |

Điểm quan trọng là **không biến user story thành danh sách thao tác ngay lập tức**. Trước tiên, tester phải hiểu outcome và ranh giới của yêu cầu.

Nếu acceptance criteria chưa nói rõ một hành vi, hãy xem đó là câu hỏi cần làm rõ. Đừng tự thêm yêu cầu chỉ vì hành vi đó thường xuất hiện ở sản phẩm khác.

<multiple-choice correct="B" select="single">
Acceptance criteria có vai trò chính nào trong kiểm thử?
- A: Thay thế hoàn toàn mọi test case
- B: Làm rõ điều kiện để work item được chấp nhận
- C: Chọn framework automation cho team
- D: Đo tốc độ chạy của ứng dụng
</multiple-choice>

## Đọc một user story theo bốn câu hỏi cơ bản

Khi nhận ticket, tester mới không cần hiểu toàn bộ hệ thống trong một lần. Hãy đọc user story theo bốn câu hỏi:

1. **Ai** thực hiện hành động?
2. **Muốn làm gì**?
3. **Để đạt điều gì**?
4. **Ranh giới nào** đang được nói đến?

Ví dụ: “Với vai trò là khách hàng đã đăng nhập, tôi muốn lưu sản phẩm vào danh sách yêu thích để xem lại trước khi mua.”

Tester có thể ghi nhận ba điểm:

- **Precondition**: khách hàng đã đăng nhập.
- **Hành động trung tâm**: lưu sản phẩm.
- **Outcome**: xem lại sản phẩm trong tương lai.

Sau đó, hãy đánh dấu các từ có thể ảnh hưởng đến phạm vi kiểm thử:

- **“Đã đăng nhập”** là precondition.
- **“Sản phẩm”** gợi câu hỏi về trạng thái còn bán, hết hàng hoặc đã xóa.
- **“Xem lại”** gợi câu hỏi về dữ liệu sau khi reload hoặc đăng nhập lại.

Chỉ đưa các trường hợp này vào test chính thức khi ticket hoặc team xác nhận.

Một kỹ thuật đơn giản là viết lại user story bằng ngôn ngữ quan sát được:

> Người dùng đã đăng nhập chọn biểu tượng yêu thích trên một sản phẩm, hệ thống ghi nhận lựa chọn đó và hiển thị sản phẩm trong danh sách yêu thích.

Câu viết lại này chưa phải test case. Nó giúp tester nhìn thấy actor, trigger, system response và outcome để chuẩn bị câu hỏi tiếp theo.

![Wide 21:9 educational diagram showing a beginner tester decomposing a Vietnamese user story into four labeled cards: “Ai”, “Hành động”, “Mục tiêu”, and “Phạm vi”. Minimalist flat vector UI design, premium professional EdTech editorial artwork, clean bento-grid composition with strong negative space, Paper White background #fafafa, Zinc-900 text #18181b, T5Edu Blue #1a73e8 for the user story card, Amber #f59e0b for questions, thin borders, rounded cards. Place the original short Vietnamese example “Khách hàng lưu sản phẩm yêu thích” in the center, arrows flowing outward to the four cards, exact short labels only, no long paragraphs, no code, no logos, no 3D, no photorealism, no gradients, no visual clutter.](https://files.manuscdn.com/user_upload_by_module/session_file/310519663091035343/fxSHhFMvAAdrPjFs.png)

## Tách acceptance criteria thành điều kiện kiểm thử

Một acceptance criterion tốt nói về điều kiện có thể quan sát hoặc xác nhận. Khi đọc từng criterion, tester hãy đánh dấu bốn thành phần:

- **Động từ** hoặc hành động.
- **Dữ liệu đầu vào**.
- **Điều kiện giới hạn**.
- **Kết quả mong đợi**.

Giả sử ticket có các criteria sau:

1. Người dùng đã đăng nhập có thể thêm một sản phẩm còn hoạt động vào danh sách yêu thích.
2. Khi thêm thành công, biểu tượng yêu thích đổi trạng thái và sản phẩm xuất hiện trong danh sách.
3. Người dùng có thể bỏ sản phẩm khỏi danh sách yêu thích.
4. Sản phẩm đã bị ngừng bán không thể được thêm vào danh sách.

Từ đó, tester có thể tạo bảng phân tích:

| Criterion | Điều kiện đầu vào | Hành động | Kết quả cần quan sát |
|---|---|---|---|
| Thêm sản phẩm hoạt động | Đã đăng nhập, sản phẩm hoạt động | Chọn biểu tượng yêu thích | Trạng thái đổi và sản phẩm xuất hiện |
| Thêm thành công | Sản phẩm đã được thêm | Mở danh sách yêu thích | Sản phẩm có trong danh sách |
| Bỏ sản phẩm | Sản phẩm đang được yêu thích | Chọn lại biểu tượng | Sản phẩm không còn trong danh sách |
| Sản phẩm ngừng bán | Đã đăng nhập, sản phẩm ngừng bán | Thử thêm vào yêu thích | Hệ thống từ chối theo cách đã thống nhất |

Bảng phân tích giúp phân biệt **điều kiện** với **thao tác**: “Đã đăng nhập” là điều kiện đầu vào, còn “chọn biểu tượng” là hành động. Kết quả cần quan sát mới quyết định pass hay fail.

Nếu criterion dùng các từ như “nhanh”, “dễ dàng”, “hợp lệ” mà không có cách đo, hãy ghi lại để hỏi.

Tester không nên tự đặt ngưỡng thời gian hoặc quy tắc dữ liệu rồi báo bug; team cần thống nhất quy tắc trước khi kết luận sản phẩm sai.

<grid-content>
Các điểm cần nhớ
> Đã rõ
```markdown
**Đã rõ**
Người dùng đã đăng nhập có thể thêm sản phẩm đang hoạt động vào danh sách yêu thích.
```
> Cần hỏi thêm
```markdown
**Cần hỏi thêm**
Hệ thống phải phản hồi nhanh khi người dùng lưu sản phẩm.
```
> Đã quan sát được
```markdown
**Đã quan sát được**
Biểu tượng đổi trạng thái và sản phẩm xuất hiện trong danh sách.
```
> Không nên tự đoán
```markdown
**Không nên tự đoán**
Phản hồi nhanh nghĩa là dưới bao nhiêu giây và đo từ thời điểm nào?
```
</grid-content>

## Từ điều kiện thành test scenario như thế nào?

Test scenario là một hướng kiểm tra ở mức khái quát. Tester mới nên chia scenario thành ba nhóm:

- **Luồng đúng:** hành vi hợp lệ theo yêu cầu.
- **Luồng không hợp lệ:** dữ liệu hoặc trạng thái bị từ chối.
- **Biên và trạng thái khác:** trường hợp sát giới hạn hoặc thay đổi trạng thái.

Với tính năng yêu thích, scenario có thể được phân nhóm như sau:

| Nhóm | Ví dụ |
|---|---|
| Luồng đúng | Thêm sản phẩm hoạt động, bỏ sản phẩm đã thêm |
| Không hợp lệ | Người chưa đăng nhập, sản phẩm ngừng bán |
| Biên hoặc trạng thái khác | Danh sách rỗng, sản phẩm đã xóa, mạng chậm |

Hãy chỉ chọn trường hợp phù hợp với scope và rủi ro của ticket.

Một scenario tốt không phải là danh sách càng dài càng tốt mà là danh sách giúp team nhìn thấy hành vi quan trọng cần xác nhận.

Nếu scenario không liên hệ được với user story, acceptance criteria hoặc rủi ro đã thống nhất, hãy hỏi vì sao nó cần xuất hiện.

<table-testcase cols="5" rows="4" headers="ID|Scenario|Loại|Liên kết criterion|Kết quả mong đợi">
| S01 | Thêm sản phẩm đang hoạt động | Positive | AC1, AC2 | Sản phẩm được lưu và hiển thị trong danh sách |
| S02 | Bỏ sản phẩm đã lưu | Positive | AC3 | Sản phẩm bị gỡ khỏi danh sách |
| S03 | Thêm sản phẩm ngừng bán | Negative | AC4 | Hệ thống từ chối theo quy tắc đã thống nhất |
| S04 | Người chưa đăng nhập chọn yêu thích | Question | Chưa rõ | Cần xác nhận yêu cầu đăng nhập hoặc hành vi chuyển hướng |
</table-testcase>

## Khi acceptance criteria chưa đủ rõ, tester nên làm gì?

Không rõ yêu cầu không có nghĩa tester phải im lặng hoặc tự quyết định. Hãy biến điểm mơ hồ thành câu hỏi có bối cảnh, tác động và đề xuất kiểm chứng.

Hãy chuyển câu hỏi chung thành câu hỏi có bối cảnh:

| Câu hỏi chung | Câu hỏi có thể kiểm chứng |
|---|---|
| Tính năng này hoạt động thế nào? | Ở AC4, sản phẩm ngừng bán không thể thêm vào danh sách. Hệ thống cần ẩn biểu tượng, vô hiệu hóa nút hay hiển thị thông báo sau khi user bấm? |

Câu hỏi cụ thể giúp product owner, BA và developer trả lời nhanh hơn. Nó cũng tạo dấu vết để team hiểu vì sao scenario được thiết kế theo cách đó.

| Dấu hiệu mơ hồ | Câu hỏi nên đặt |
|---|---|
| Không có dữ liệu mẫu | Giá trị nào được xem là hợp lệ và ai cung cấp dữ liệu test? |
| Không rõ quyền | Vai trò nào được thực hiện hành động này? |
| Không rõ trạng thái lỗi | Người dùng thấy thông báo, trạng thái giữ nguyên hay được chuyển trang? |
| Không rõ phạm vi | Hành vi này áp dụng cho web, mobile hay cả hai? |
| Không rõ tiêu chí pass | Team xác nhận kết quả bằng UI, dữ liệu hay cả hai? |

[ISTQB Agile Tester](https://www.istqb.org/certifications/certified-tester-foundation-level) nêu rằng tester có thể hỗ trợ team định nghĩa user story, scenario, requirement và acceptance criteria theo cách dễ hiểu, có thể kiểm thử.

Tester không chỉ nhận yêu cầu để thực hiện. Tester còn góp phần làm yêu cầu rõ hơn trước khi kiểm thử.

![Wide 21:9 educational illustration of a QA refinement conversation. Three flat cards labeled exactly “Câu hỏi”, “Bằng chứng trong ticket”, and “Quyết định của team” connected left to right by blue arrows. Show a beginner tester pointing at an ambiguous Vietnamese acceptance criterion, with an amber question mark and a small green resolved checkmark on the final card. Minimalist flat vector UI design, premium professional EdTech editorial artwork, clean bento-grid composition, Paper White #fafafa background, Zinc-900 #18181b text, T5Edu Blue #1a73e8, Amber #f59e0b, subtle one-pixel borders, exact short Vietnamese labels only, no long text, no code, no logos, no 3D, no photorealism, no gradients, no clutter.](https://files.manuscdn.com/user_upload_by_module/session_file/310519663091035343/jRreqTNzbYvGvycc.png)

## Checklist trước khi viết test case

Trước khi mở công cụ quản lý test, hãy kiểm tra năm điểm:

1. Đã xác định đúng người dùng và precondition chưa?
2. Mỗi scenario có liên kết với criterion hoặc rủi ro cụ thể không?
3. Expected result có thể quan sát được không?
4. Có từ nào trong yêu cầu còn mơ hồ không?
5. Có đang thêm hành vi chỉ vì “sản phẩm nào cũng phải thế” không?

Nếu câu trả lời cuối cùng là có, hãy tách phần đó thành câu hỏi hoặc assumption được ghi rõ. Đừng biến thói quen cá nhân thành yêu cầu của sản phẩm.

<dropdown-content>
Acceptance criteria có phải là test case không?
```markdown
Không. Acceptance criteria là điều kiện để work item được chấp nhận. Test case là cách cụ thể để kiểm tra các điều kiện đó, gồm dữ liệu, bước thực hiện và kết quả mong đợi.
```
</dropdown-content>

<dropdown-content>
Có cần viết test case cho mọi câu trong user story không?
```markdown
Không nhất thiết. Hãy dùng user story để hiểu mục tiêu, dùng acceptance criteria và rủi ro để chọn scenario. Một criterion có thể cần nhiều test case, nhưng cũng có chi tiết chỉ là bối cảnh.
```
</dropdown-content>

<dropdown-content>
Nếu yêu cầu thiếu thì tester có được tự quyết định không?
```markdown
Tester có thể đưa ra giả định để thảo luận, nhưng không nên âm thầm biến giả định thành expected result. Hãy ghi câu hỏi, phạm vi ảnh hưởng và quyết định của team.
```
</dropdown-content>

## Tổng kết

User story trả lời ai cần gì và vì sao; acceptance criteria mô tả điều kiện để work item được chấp nhận.

Hãy tách từng criterion thành input, action, điều kiện giới hạn và kết quả có thể quan sát, rồi phủ đủ luồng đúng, luồng không hợp lệ và trường hợp biên phù hợp với scope.

Khi yêu cầu mơ hồ, câu hỏi cụ thể có giá trị hơn một test case được xây trên suy đoán.

Nếu đang học testing, hãy làm theo checklist này:

1. Chọn một ticket nhỏ.
2. Đánh dấu bốn thành phần: ai, hành động, mục tiêu và phạm vi.
3. Viết ba scenario liên kết rõ với acceptance criteria.
4. Ghi lại phần còn thiếu để team có thể kết luận pass hoặc fail.

Câu hỏi tự kiểm tra: phần nào trong ticket hiện tại vẫn chưa đủ rõ để kết luận pass hoặc fail?

## Hashtag
> user story, acceptance criteria, test scenario, manual testing, software testing
