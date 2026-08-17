# Đọc User Story để Viết Test

> Hiểu đúng user story và acceptance criteria giúp tester mới biến yêu cầu còn mơ hồ thành các scenario có thể kiểm tra, đặt câu hỏi đúng và tránh viết test case theo suy đoán.

![Square 1:1 educational editorial illustration for T5Edu about a beginner tester reading a user story and acceptance criteria. Minimalist flat vector UI design, premium professional EdTech editorial artwork, clean bento-grid composition with strong negative space, Paper White background #fafafa, Zinc-900 content #18181b, T5Edu Blue accent #1a73e8, Amber highlight #f59e0b, subtle one-pixel borders, rounded cards, calm precise instructional mood. Show a young Vietnamese tester at a desk reviewing three connected cards labeled exactly “User story”, “Điều kiện chấp nhận”, and “Scenario kiểm thử”, with a simple arrow from left to right, small checkmarks and question marks, no code, no automation tools, no logos, no realistic text paragraphs, no gradients, no 3D, no photorealism, no clutter.](https://files.manuscdn.com/user_upload_by_module/session_file/310519663091035343/zumnKtgaMuFmQuqE.png)

Khi mới vào nghề, tester thường nhận một ticket có vài dòng mô tả rồi được giao nhiệm vụ “test giúp”. Phản xạ phổ biến là mở ứng dụng, bấm thử vài trường hợp và bắt đầu viết test case. Cách làm này dễ bỏ sót điều kiện quan trọng, vì tester đang kiểm tra theo suy đoán của mình thay vì theo điều sản phẩm cần đáp ứng.

Bài viết này giúp **tester mới, intern và fresher** đọc user story cùng acceptance criteria, tách chúng thành điều kiện kiểm thử và tạo bộ scenario đầu tiên. Phạm vi chỉ tập trung vào kỹ năng phân tích yêu cầu nền tảng. Bạn chưa cần biết automation, API hay framework kiểm thử để áp dụng.

## User story và acceptance criteria khác nhau thế nào?

User story mô tả một nhu cầu từ góc nhìn người sử dụng. Mẫu quen thuộc là: “Với vai trò là [người dùng], tôi muốn [hành động], để [lợi ích].” Câu này cho tester biết ai đang cần gì và vì sao nhu cầu đó có ý nghĩa, nhưng thường chưa đủ chi tiết để kiểm tra mọi điều kiện.

Acceptance criteria, hay điều kiện chấp nhận, là các điều kiện mà work item phải đáp ứng để được xem là hoàn thành và chấp nhận. Atlassian mô tả acceptance criteria như những yêu cầu và điều kiện định trước, còn Scrum.org nhấn mạnh vai trò của chúng trong việc làm rõ scope, giới hạn và outcome của một work item [1] [2].

| Thành phần | Câu hỏi tester cần trả lời | Ví dụ |
|---|---|---|
| User story | Ai cần gì và vì sao? | Người mua muốn lưu địa chỉ giao hàng để thanh toán nhanh hơn |
| Acceptance criteria | Điều kiện nào phải đúng? | Địa chỉ hợp lệ được lưu và hiển thị ở lần thanh toán sau |
| Test scenario | Cần kiểm tra nhóm hành vi nào? | Lưu địa chỉ hợp lệ, bỏ trống trường bắt buộc, sửa địa chỉ |
| Test case | Thao tác cụ thể và kết quả mong đợi là gì? | Nhập dữ liệu, bấm Lưu, kiểm tra thông báo và dữ liệu hiển thị |

Điểm quan trọng là **không biến user story thành một danh sách thao tác ngay lập tức**. Trước tiên, tester phải hiểu outcome và ranh giới của yêu cầu. Nếu acceptance criteria chưa nói rõ một hành vi, đó có thể là câu hỏi cần làm rõ chứ chưa phải quyền tự thêm yêu cầu.

<multiple-choice-question>
{"question":"Acceptance criteria có vai trò chính nào trong kiểm thử?","options":["Thay thế hoàn toàn mọi test case","Làm rõ điều kiện để work item được chấp nhận","Chọn framework automation cho team","Đo tốc độ chạy của ứng dụng"],"correctAnswer":1,"explanation":"Acceptance criteria mô tả điều kiện cần đạt, giúp team có căn cứ chung để phát triển và kiểm thử. Tester vẫn cần chuyển chúng thành scenario và test case phù hợp."}
</multiple-choice-question>

## Đọc một user story theo bốn câu hỏi cơ bản

Khi nhận ticket, tester mới không cần cố hiểu toàn bộ hệ thống trong một lần. Hãy đọc user story theo bốn câu hỏi: **ai**, **muốn làm gì**, **để đạt điều gì**, và **ranh giới nào đang được nói đến**.

Ví dụ: “Với vai trò là khách hàng đã đăng nhập, tôi muốn lưu sản phẩm vào danh sách yêu thích để xem lại trước khi mua.” Từ câu này, tester có thể ghi nhận người dùng phải đăng nhập, hành động trung tâm là lưu sản phẩm, còn outcome là xem lại sản phẩm trong tương lai.

Sau đó, hãy đánh dấu các từ có thể ảnh hưởng đến phạm vi kiểm thử. “Đã đăng nhập” là precondition. “Sản phẩm” có thể gợi ra câu hỏi về sản phẩm còn bán, hết hàng hoặc đã bị xóa. “Xem lại” gợi ý cần kiểm tra dữ liệu có còn sau khi tải lại trang hoặc đăng nhập lại hay không, nhưng chỉ đưa vào test chính thức khi ticket hoặc team xác nhận đó là yêu cầu.

Một kỹ thuật đơn giản là viết lại user story bằng ngôn ngữ quan sát được:

> Người dùng đã đăng nhập chọn biểu tượng yêu thích trên một sản phẩm, hệ thống ghi nhận lựa chọn đó và hiển thị sản phẩm trong danh sách yêu thích.

Câu viết lại này chưa phải test case. Nó chỉ giúp tester nhìn thấy actor, trigger, system response và outcome để chuẩn bị câu hỏi tiếp theo.

![Wide 21:9 educational diagram showing a beginner tester decomposing a Vietnamese user story into four labeled cards: “Ai”, “Hành động”, “Mục tiêu”, and “Phạm vi”. Minimalist flat vector UI design, premium professional EdTech editorial artwork, clean bento-grid composition with strong negative space, Paper White background #fafafa, Zinc-900 text #18181b, T5Edu Blue #1a73e8 for the user story card, Amber #f59e0b for questions, thin borders, rounded cards. Place the original short Vietnamese example “Khách hàng lưu sản phẩm yêu thích” in the center, arrows flowing outward to the four cards, exact short labels only, no long paragraphs, no code, no logos, no 3D, no photorealism, no gradients, no visual clutter.](https://files.manuscdn.com/user_upload_by_module/session_file/310519663091035343/fxSHhFMvAAdrPjFs.png)

## Tách acceptance criteria thành điều kiện kiểm thử

Một acceptance criterion tốt thường nói về điều kiện có thể quan sát hoặc xác nhận. Khi đọc từng criterion, tester hãy gạch chân động từ, dữ liệu đầu vào, điều kiện giới hạn và kết quả mong đợi.

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

Bảng này giúp phân biệt **điều kiện** với **thao tác**. “Đã đăng nhập” không phải kết quả. “Chọn biểu tượng” không phải tiêu chí chấp nhận. Kết quả cần quan sát mới là phần quyết định pass hay fail.

Nếu criterion dùng các từ như “nhanh”, “dễ dàng”, “hợp lệ”, “đúng” hoặc “phù hợp” nhưng không có cách đo hay ví dụ, hãy ghi lại để hỏi. Tester không nên tự đặt ngưỡng thời gian hoặc quy tắc dữ liệu rồi báo bug vì sản phẩm không tuân theo quy tắc chưa được thống nhất.

<grid-content>
{"columns":2,"items":[{"title":"Đã rõ","content":"Người dùng đã đăng nhập có thể thêm sản phẩm đang hoạt động vào danh sách yêu thích."},{"title":"Cần hỏi thêm","content":"Hệ thống phải phản hồi nhanh khi người dùng lưu sản phẩm."},{"title":"Đã quan sát được","content":"Biểu tượng đổi trạng thái và sản phẩm xuất hiện trong danh sách."},{"title":"Không nên tự đoán","content":"Phản hồi nhanh nghĩa là dưới bao nhiêu giây và đo từ thời điểm nào?"}]}
</grid-content>

## Từ điều kiện thành test scenario như thế nào?

Test scenario là một hướng kiểm tra ở mức khái quát. Tester mới nên tạo scenario theo ba nhóm: **luồng đúng**, **luồng không hợp lệ** và **điều kiện biên hoặc trạng thái khác**.

Với tính năng yêu thích, nhóm luồng đúng có thể gồm thêm sản phẩm hoạt động và bỏ sản phẩm đã thêm. Nhóm không hợp lệ gồm người chưa đăng nhập thử lưu sản phẩm hoặc sản phẩm ngừng bán. Nhóm biên gồm danh sách rỗng, sản phẩm có tên dài, sản phẩm đã bị xóa sau khi được thêm và thao tác liên tiếp khi mạng chậm, nhưng chỉ chọn những trường hợp phù hợp scope và rủi ro của ticket.

Một scenario tốt không phải là danh sách càng dài càng tốt. Nó phải giúp team nhìn thấy những hành vi quan trọng cần xác nhận. Nếu một scenario không liên hệ được với user story, acceptance criteria hoặc rủi ro đã thống nhất, hãy hỏi vì sao nó cần xuất hiện.

<testcase-table>
{"title":"Scenario kiểm thử từ acceptance criteria","columns":["ID","Scenario","Loại","Liên kết criterion","Kết quả mong đợi"],"rows":[["S01","Thêm sản phẩm đang hoạt động","Positive","AC1, AC2","Sản phẩm được lưu và hiển thị trong danh sách"],["S02","Bỏ sản phẩm đã lưu","Positive","AC3","Sản phẩm bị gỡ khỏi danh sách"],["S03","Thêm sản phẩm ngừng bán","Negative","AC4","Hệ thống từ chối theo quy tắc đã thống nhất"],["S04","Người chưa đăng nhập chọn yêu thích","Question","Chưa rõ","Cần xác nhận yêu cầu đăng nhập hoặc hành vi chuyển hướng"]]}
</testcase-table>

## Khi acceptance criteria chưa đủ rõ, tester nên làm gì?

Không rõ yêu cầu không đồng nghĩa với việc tester phải im lặng hoặc tự quyết định. Hãy biến điểm mơ hồ thành câu hỏi có bối cảnh, tác động và đề xuất kiểm chứng. Ví dụ, thay vì hỏi “Tính năng này hoạt động thế nào?”, hãy hỏi: “Ở AC4, sản phẩm ngừng bán không thể thêm vào danh sách. Hệ thống cần ẩn biểu tượng, vô hiệu hóa nút hay hiển thị thông báo sau khi người dùng bấm?”

Câu hỏi cụ thể giúp product owner, BA và developer trả lời nhanh hơn. Nó cũng tạo dấu vết để sau này team biết vì sao scenario được thiết kế theo cách đó.

| Dấu hiệu mơ hồ | Câu hỏi nên đặt |
|---|---|
| Không có dữ liệu mẫu | Giá trị nào được xem là hợp lệ và ai cung cấp dữ liệu test? |
| Không rõ quyền | Vai trò nào được thực hiện hành động này? |
| Không rõ trạng thái lỗi | Người dùng thấy thông báo, trạng thái giữ nguyên hay được chuyển trang? |
| Không rõ phạm vi | Hành vi này áp dụng cho web, mobile hay cả hai? |
| Không rõ tiêu chí pass | Team xác nhận kết quả bằng UI, dữ liệu hay cả hai? |

ISTQB Agile Tester cũng nêu rằng tester có thể hỗ trợ team định nghĩa user story, scenario, requirement và acceptance criteria dễ hiểu, có thể kiểm thử [3]. Điều đó có nghĩa tester không chỉ nhận yêu cầu để执行, mà còn góp phần làm yêu cầu rõ hơn trước khi kiểm thử.

![Wide 21:9 educational illustration of a QA refinement conversation. Three flat cards labeled exactly “Câu hỏi”, “Bằng chứng trong ticket”, and “Quyết định của team” connected left to right by blue arrows. Show a beginner tester pointing at an ambiguous Vietnamese acceptance criterion, with an amber question mark and a small green resolved checkmark on the final card. Minimalist flat vector UI design, premium professional EdTech editorial artwork, clean bento-grid composition, Paper White #fafafa background, Zinc-900 #18181b text, T5Edu Blue #1a73e8, Amber #f59e0b, subtle one-pixel borders, exact short Vietnamese labels only, no long text, no code, no logos, no 3D, no photorealism, no gradients, no clutter.](https://files.manuscdn.com/user_upload_by_module/session_file/310519663091035343/jRreqTNzbYvGvycc.png)

## Checklist trước khi viết test case

Trước khi mở công cụ quản lý test, hãy tự kiểm tra năm điểm. Bạn đã xác định đúng người dùng và precondition chưa? Bạn có thể trích từng scenario từ một acceptance criterion hoặc một rủi ro cụ thể không? Kết quả mong đợi có thể quan sát được không? Có từ nào trong yêu cầu còn mơ hồ cần hỏi không? Cuối cùng, bạn có đang thêm một hành vi chỉ vì “thường sản phẩm nào cũng phải thế” không?

Nếu câu trả lời cuối cùng là có, hãy tách phần đó thành câu hỏi hoặc assumption được ghi rõ. Đừng biến thói quen cá nhân thành yêu cầu của sản phẩm.

<dropdown-content>
{"title":"FAQ cho tester mới","items":[{"question":"Acceptance criteria có phải là test case không?","answer":"Không. Acceptance criteria là điều kiện để work item được chấp nhận. Test case là cách cụ thể để kiểm tra các điều kiện đó, gồm dữ liệu, bước thực hiện và kết quả mong đợi."},{"question":"Có cần viết test case cho mọi câu trong user story không?","answer":"Không nhất thiết. Hãy dùng user story để hiểu mục tiêu, dùng acceptance criteria và rủi ro để chọn scenario. Một criterion có thể cần nhiều test case, nhưng cũng có chi tiết chỉ là bối cảnh."},{"question":"Nếu yêu cầu thiếu thì tester có được tự quyết định không?","answer":"Tester có thể đưa ra giả định để thảo luận, nhưng không nên âm thầm biến giả định thành expected result. Hãy ghi câu hỏi, phạm vi ảnh hưởng và quyết định của team."}]}
</dropdown-content>

## Tổng kết

- User story trả lời ai cần gì và vì sao; acceptance criteria mô tả điều kiện để work item được chấp nhận.
- Hãy tách từng criterion thành input, action, điều kiện giới hạn và kết quả có thể quan sát.
- Scenario nên bao gồm luồng đúng, luồng không hợp lệ và trường hợp biên phù hợp với scope.
- Khi yêu cầu mơ hồ, câu hỏi cụ thể có giá trị hơn một test case được xây trên suy đoán.

Nếu bạn đang học testing, hãy chọn một ticket nhỏ, đánh dấu bốn thành phần “ai, hành động, mục tiêu, phạm vi”, rồi viết ba scenario có liên kết rõ với acceptance criteria. Bạn thấy phần nào trong ticket hiện tại vẫn chưa đủ rõ để có thể kết luận pass hoặc fail?

## Nguồn tham khảo

- [Acceptance Criteria, Atlassian](https://www.atlassian.com/work-management/project-management/acceptance-criteria)
- [How to Use Acceptance Criteria?, Scrum.org](https://www.scrum.org/resources/blog/how-use-acceptance-criteria)
- [Certified Tester Foundation Level Agile Tester, ISTQB](https://www.istqb.org/certifications/certified-tester-foundation-level)

## Hashtag

#acceptancecriteria, #userstory, #testscenario, #softwaretesting, #manualtesting, #testernewbie, #qa
