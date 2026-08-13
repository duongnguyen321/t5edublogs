# MCP Testing Cho Người Mới Bắt Đầu

> mcp testing, ai testing, qa beginner, automation testing, tester fresher

![Square 1:1 premium EdTech editorial cover about MCP testing for beginner software testers. Show a clean bento-grid composition on Paper White background #fafafa: central card with a simplified AI assistant connected through a blue USB-C style bridge to three testing cards labeled exactly 'Test plan', 'Browser', and 'Bug report'; left amber card labeled 'Tester'; solid T5Edu Blue arrows #1a73e8 flow from Tester to AI assistant and from assistant to testing tools; Zinc-900 text #18181b, Amber #f59e0b highlights, subtle one-pixel borders, restrained liquid-glass layers, minimalist flat vector UI design, premium professional EdTech editorial artwork, strong negative space, no long paragraphs, no people, no faces, no hands, no 3D, no photorealism, no glossy plastic, no dramatic lighting, no purple, no violet, no pink, no neon, no logo, no watermark](https://files.manuscdn.com/user_upload_by_module/session_file/310519663091035343/qIsfRiuwzgilQWAI.png)

## MCP testing là gì và vì sao tester mới nên quan tâm

MCP testing là cách kiểm thử các hệ thống dùng Model Context Protocol, một open-source standard kết nối ứng dụng AI với data source, tool và workflow bên ngoài. Tài liệu chính thức của [Model Context Protocol](https://modelcontextprotocol.io/docs/2026-07-28/getting-started/intro) ví MCP như cổng USB-C cho AI: ứng dụng AI có thể khám phá và sử dụng công cụ theo một cách thống nhất hơn thay vì mỗi integration dùng một quy ước riêng.

Với tester mới, điểm quan trọng không phải là học thuộc protocol ngay lập tức. Điều cần hiểu trước là hệ thống giờ có thêm một lớp giao tiếp giữa AI client, MCP server và tool thật. Một câu trả lời trông hợp lý vẫn có thể sai nếu AI chọn nhầm tool, truyền thiếu tham số, vượt quyền hoặc báo đã hoàn thành dù hành động chưa xảy ra.

![Wide 21:9 educational diagram explaining MCP testing architecture for beginners. Horizontal bento layout with four connected cards: 'Người dùng' on the far left, 'AI client' next, 'MCP server' next, and a grouped tools area on the right containing 'Browser', 'Database', and 'Test runner'. Solid blue arrows point left to right for request flow, dashed amber arrows point right to left for result flow. Add a small shield icon above MCP server labeled 'Permission'. Minimalist flat vector UI design, premium professional EdTech editorial artwork, clean horizontal composition with strong negative space, Paper White #fafafa background, Zinc-900 #18181b content, T5Edu Blue #1a73e8, Amber #f59e0b, subtle one-pixel borders, no people, no faces, no hands, no 3D, no photorealism, no purple, no violet, no pink, no neon, no logo, no watermark](https://files.manuscdn.com/user_upload_by_module/session_file/310519663091035343/pLUzXRavpIkRwmLP.png)

Trong thực tế, tester có thể gặp MCP ở ba vị trí. Thứ nhất là kiểm thử MCP server, nơi server công bố tool và xử lý request. Thứ hai là kiểm thử AI agent sử dụng các tool đó. Thứ ba là kiểm thử cả workflow, từ ý định của người dùng đến kết quả cuối trong hệ thống.

| Thành phần | Câu hỏi tester cần đặt ra | Ví dụ beginner-first |
| --- | --- | --- |
| Tool discovery | AI có nhìn thấy đúng tool không? | Tool `search_orders` có mô tả rõ và xuất hiện đúng không? |
| Input schema | Tool có nhận đúng dữ liệu không? | `order_id` thiếu thì trả lỗi dễ hiểu hay chạy sai? |
| Tool execution | Tool có thực hiện đúng hành động không? | Tìm đơn hàng có đúng mã và đúng trạng thái không? |
| Result handling | AI có hiểu đúng kết quả không? | Kết quả rỗng có bị diễn giải thành đã giao hàng không? |
| Permission | AI có bị giới hạn quyền không? | User chỉ được xem đơn, không được hoàn tiền |

Nếu chưa vững nền tảng, bạn có thể bắt đầu từ [khóa Testing cơ bản](/courses/testing-co-ban) và ôn lại cách viết testcase trong bài [Tester Mới Sai Lầm Ở Đâu Khi Viết Test Case](/blogs/tester-moi-sai-lam-o-dau-khi-viet-test-case). Hai kỹ năng này vẫn là foundation trước khi thêm AI vào quy trình.

<multiple-choice correct="C" select="single">
Một AI assistant gọi tool tìm đơn hàng nhưng trả lời rằng đơn đã được giao dù tool trả về trạng thái `processing`. Lớp nào cần kiểm tra trước?
- A: Màu sắc của nút trên giao diện
- B: Tốc độ tải trang chủ
- C: Cách AI diễn giải kết quả tool
- D: Tên branch Git của project
</multiple-choice>

## Một testcase MCP tốt cần kiểm tra những gì

Tester mới thường bắt đầu bằng câu hỏi “AI trả lời đúng chưa?”. Câu hỏi này quá rộng. Với MCP testing, nên tách một scenario thành request, tool selection, input, execution, output và final response để biết lỗi nằm ở đâu.

Hãy dùng scenario đơn giản: user hỏi “Kiểm tra trạng thái đơn hàng DH001”. Testcase không chỉ assert câu trả lời cuối. Tester cần xác minh AI chọn đúng `get_order_status`, gửi đúng `order_id`, không gọi thêm tool không cần thiết và hiển thị trạng thái dựa trên data thật.

<table-testcase cols="5" rows="4" headers="ID|Tình huống|Input|Điểm cần kiểm tra|Expected result">
| MCP-01 | Gọi tool hợp lệ | order_id = DH001 | Đúng tool, đúng schema | Trả đúng trạng thái đơn |
| MCP-02 | Thiếu tham số | Không có order_id | Không tự bịa giá trị | Hỏi lại user hoặc báo lỗi rõ |
| MCP-03 | Tool timeout | Server không phản hồi | Có timeout và fallback | Không nói đã hoàn thành |
| MCP-04 | Không đủ quyền | User chỉ có quyền xem | Request bị chặn | Có thông báo permission |
</table-testcase>

Có bốn nhóm assertion quan trọng. **Assertion về giao thức** kiểm tra request có đúng format. **Assertion về nghiệp vụ** kiểm tra trạng thái đơn, số tiền hoặc quyền truy cập. **Assertion về hành vi agent** kiểm tra tool call và thứ tự hành động. **Assertion về an toàn** kiểm tra prompt không thể khiến agent bỏ qua permission hoặc làm lộ data của user khác.

Một lỗi phổ biến là chỉ mock mọi tool rồi assert text cuối. Cách này giúp test chạy nhanh nhưng có thể bỏ sót việc AI gọi nhầm tool. Với các scenario quan trọng, nên lưu lại tool name, arguments, thời điểm gọi, response và trace id để reviewer đọc được toàn bộ đường đi.

![Wide 21:9 educational comparison diagram for beginner MCP testing. Four horizontal columns labeled exactly 'Contract', 'Behavior', 'Agent', and 'Safety', each with a simple flat icon and two short Vietnamese labels: 'Schema' under Contract, 'Nghiệp vụ' under Behavior, 'Tool call' under Agent, 'Permission' under Safety. A blue progress line connects the columns from left to right, with amber check badges on each column. Minimalist flat vector UI design, premium professional EdTech editorial artwork, clean bento-grid composition with strong negative space, Paper White #fafafa background, Zinc-900 #18181b, T5Edu Blue #1a73e8, Amber #f59e0b, subtle one-pixel borders, restrained liquid-glass layers, no people, no faces, no hands, no 3D, no photorealism, no purple, no violet, no pink, no neon, no logo, no watermark](https://files.manuscdn.com/user_upload_by_module/session_file/310519663091035343/IDUZqreuwWZVXtEa.png)

## MCP testing khác gì với testing AI agent thông thường

Hai khái niệm này liên quan nhưng không giống nhau. Testing AI agent tập trung vào khả năng lập kế hoạch, ghi nhớ context, xử lý nhiều bước và đạt mục tiêu. MCP testing tập trung sâu hơn vào boundary giữa AI application và external tool.

| Phạm vi | Ví dụ câu hỏi | Loại lỗi thường gặp |
| --- | --- | --- |
| Model response | Câu trả lời có phù hợp không? | Hallucination, thiếu context |
| Agent workflow | Agent có lập kế hoạch đúng không? | Lặp vô hạn, bỏ qua bước |
| MCP contract | Tool có mô tả đúng schema không? | Sai type, thiếu field |
| Authorization | User có được phép gọi tool không? | Privilege escalation |
| External effect | Hành động thật có xảy ra đúng không? | Gửi nhầm email, cập nhật nhầm record |

Theo [Applitools](https://applitools.com/blog/model-context-protocol-ai-testing/), MCP có giá trị vì AI nhận được context có cấu trúc hơn, chẳng hạn framework đang dùng, file đang mở hoặc tool đang sẵn sàng. Điều đó có thể giúp test generation và debugging chính xác hơn, nhưng không biến output AI thành bằng chứng tự động đúng. Tester vẫn phải kiểm tra expected behavior bằng data và rule độc lập.

<grid-content>
Bốn lớp kiểm thử MCP mà fresher có thể áp dụng
> Tách lớp giúp tester không gộp mọi lỗi vào một kết luận mơ hồ như “AI trả lời sai”.
```markdown
**Contract**
Kiểm tra tool name, mô tả, input schema, output schema và error format. Đây là lớp dễ bắt đầu nhất vì expected result khá ổn định.

**Behavior**
Gửi input đại diện cho happy path, boundary và invalid case. Xác minh tool thực hiện đúng nghiệp vụ thay vì chỉ trả HTTP 200.

**Agent**
Kiểm tra AI có chọn đúng tool, truyền đủ argument, xử lý kết quả và dừng đúng lúc hay không.

**Safety**
Kiểm tra permission, data isolation, confirmation trước hành động rủi ro và khả năng chống prompt injection.
```
</grid-content>

Nếu muốn làm quen API trước khi kiểm thử tool, hãy thực hành với [API Testing cơ bản](/courses/api-testing-co-ban), sau đó xem [Hướng dẫn API Testing cho người mới](/blogs/huong-dan-api-testing-cho-nguoi-moi). MCP server thường được hiểu dễ hơn khi tester đã quen request, response, status code và schema.

## Chuyển kết quả kiểm thử thành evidence có thể review

Một MCP test chỉ có giá trị khi người khác hiểu được vì sao nó pass hoặc fail. Vì vậy, sau mỗi lần chạy, hãy lưu bốn nhóm evidence: user goal, tool call thực tế, dữ liệu tool trả về và câu trả lời cuối của agent. Nếu có hành động gây thay đổi dữ liệu, cần thêm confirmation step và trạng thái trước, sau hành động.

| Evidence | Cần ghi gì | Dùng để phát hiện lỗi nào |
| --- | --- | --- |
| User goal | Ý định ban đầu, role và context | Agent hiểu sai yêu cầu |
| Tool call | Tên tool, arguments, thứ tự gọi | Chọn nhầm tool, gọi thừa |
| Tool result | Status, schema, data chính | Tool lỗi hoặc AI đọc sai |
| Final response | Nội dung trả lời và action đã xác nhận | Hallucination, báo thành công giả |
| Security context | Permission, confirmation, data scope | Lộ dữ liệu hoặc vượt quyền |

![Wide 21:9 educational evidence matrix for MCP testing. Show five horizontal evidence cards labeled exactly 'User goal', 'Tool call', 'Tool result', 'Final response', and 'Security context', connected by blue arrows into a right-side card labeled 'Reviewable test result'. Each card has a distinct flat icon and one short Vietnamese descriptor, with an amber shield on Security context. Minimalist flat vector UI design, premium professional EdTech editorial artwork, clean horizontal bento-grid, Paper White #fafafa background, Zinc-900 #18181b, T5Edu Blue #1a73e8, Amber #f59e0b, subtle one-pixel borders, no people, no faces, no hands, no 3D, no photorealism, no purple, no violet, no pink, no neon, no logo, no watermark](https://files.manuscdn.com/user_upload_by_module/session_file/310519663091035343/ivfmdShDfShHAaRU.png)

<dropdown-content>
Khi nào cần human confirmation trước tool call?
> Cần confirmation khi tool tạo external side effect như gửi email, hoàn tiền, xóa dữ liệu, thay đổi quyền hoặc cập nhật bản ghi quan trọng. Với tool chỉ đọc dữ liệu, vẫn phải kiểm tra permission nhưng thường không cần thêm bước xác nhận.
```markdown
Review trước khi cho phép hành động:
- Tool có đúng với user goal không?
- User role có quyền trên resource này không?
- Input có đúng resource và phạm vi dữ liệu không?
- Agent đã hiển thị hành động sắp thực hiện chưa?
- Kết quả sau hành động có được kiểm tra độc lập không?
```
</dropdown-content>

Khi có evidence như trên, bug report sẽ cụ thể hơn: agent chọn sai tool ở bước nào, argument nào sai, tool trả dữ liệu gì và câu trả lời cuối đã lệch khỏi rule nào. Đó là cách biến MCP testing từ việc đọc một đoạn text thành kiểm thử một workflow có thể audit.

## Tổng kết

- MCP là chuẩn kết nối AI với data, tool và workflow, vì vậy tester cần kiểm tra cả contract, behavior, agent workflow và safety.
- Testcase MCP nên lưu tool name, arguments, response và expected behavior, không chỉ assert câu text cuối.
- Người mới nên bắt đầu bằng một tool đọc dữ liệu an toàn, sau đó mở rộng sang timeout, permission và multi-step flow.
- Nếu bạn đang học từ foundation, hãy học [Testing cơ bản](/courses/testing-co-ban) rồi thực hành [API Testing cơ bản](/courses/api-testing-co-ban) trước khi xây mini project MCP.
- Bạn sẽ chọn scenario MCP nào đầu tiên để viết testcase, tìm đơn hàng, tạo bug report hay chạy browser test?
