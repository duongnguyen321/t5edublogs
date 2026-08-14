# OpenTelemetry GenAI Observability cho người mới: đọc trace để test AI app

> OpenTelemetry GenAI observability giúp tester nhìn thấy model, token, thời gian, tool call và lỗi trong một lần chạy AI, từ đó phân biệt lỗi prompt, lỗi tích hợp, lỗi dữ liệu và lỗi hệ thống thay vì chỉ thấy câu trả lời sai.

![Wide square editorial cover showing a beginner tester inspecting an AI application through an OpenTelemetry trace, with three connected panels labeled exactly 'Trace', 'Metric', and 'Event', a small chatbot window and magnifying glass, no dense text. Minimalist flat vector UI design, premium professional EdTech editorial artwork, clean bento-grid composition with strong negative space, Paper White background #fafafa, Zinc-900 content #18181b, T5Edu Blue accent #1a73e8, Amber highlight #f59e0b, subtle one-pixel borders and restrained liquid-glass layers, simple flat icons, geometric shapes, crisp edges, no people, no faces, no hands, no photorealism, no 3D, no purple, no violet, no pink, no neon, no logo, no watermark](https://files.manuscdn.com/user_upload_by_module/session_file/310519663091035343/pCGZzBZODMnnCWfH.png)

## Vì sao test AI app cần observability?

Một AI app có thể gọi nhiều model, dùng tool, retry request và xử lý dữ liệu qua nhiều service trước khi trả lời. Khi người dùng nhận câu trả lời sai hoặc phải chờ quá lâu, một screenshot không cho biết lỗi bắt đầu ở prompt, model, tool hay service phụ thuộc nào. Tester cần evidence nối được toàn bộ journey.

[OpenTelemetry](https://opentelemetry.io/) là framework mở để thu thập và xuất telemetry. Với GenAI, [GenAI semantic conventions](https://github.com/open-telemetry/semantic-conventions-genai) đang được phát triển để chuẩn hóa cách ghi nhận model, token, duration, prompt, completion, tool call và tool result. [Bài viết chính thức của OpenTelemetry năm 2026](https://opentelemetry.io/blog/2026/genai-observability/) minh họa cách xem các tín hiệu này trong trace, metrics và structured logs.

Observability không trả lời thay cho evaluation. Trace cho biết hệ thống đã làm gì, còn evaluation trả lời kết quả có đúng, an toàn, hữu ích hoặc phù hợp với tiêu chí hay không. Người mới nên xem hai việc này là hai lớp bổ sung: evaluation phát hiện output không đạt, observability giúp điều tra vì sao.

<grid-content>
Ba loại evidence tester cần phân biệt
> Hãy bắt đầu từ câu hỏi debug, sau đó chọn signal phù hợp thay vì bật mọi loại log.
```markdown
**Trace**
- Cho thấy một request đi qua các span nào
- Phù hợp để tìm bước chậm, retry và tool call lỗi
- Có thể nối bằng trace ID
```
```markdown
**Metric**
- Tóm tắt duration, token usage hoặc tỷ lệ lỗi
- Phù hợp để so sánh model và phát hiện regression
- Không thay thế chi tiết của một request
```
```markdown
**Event hoặc log**
- Ghi sự kiện có ngữ cảnh như finish reason hoặc exception
- Phù hợp để xem trạng thái và dữ liệu chẩn đoán
- Cần kiểm soát data nhạy cảm
```
</grid-content>

Nếu bạn mới học khái niệm này, [Test observability cho người mới](/blogs/test-observability-cho-nguoi-moi) giải thích mối liên hệ giữa test runner, logs, metrics và traces trong hệ thống thông thường. Bài này đi sâu hơn vào AI workload.

![Wide 21:9 educational diagram showing an AI request journey from 'User request' to 'LLM call' to 'Tool call' to 'Final answer', with a trace tree below and three signal lanes labeled exactly 'Trace', 'Metric', and 'Event'. Use blue arrows for request flow and amber highlights on latency and token usage. Minimalist flat vector UI design, premium professional EdTech editorial artwork, clean horizontal bento-grid, Paper White background #fafafa, Zinc-900 content #18181b, T5Edu Blue accent #1a73e8, Amber highlight #f59e0b, subtle one-pixel borders, short labels only, no people, no faces, no hands, no 3D, no photorealism, no purple, no violet, no pink, no neon, no logo, no watermark](https://files.manuscdn.com/user_upload_by_module/session_file/310519663091035343/muufTACIYcLMKlCU.png)

## GenAI trace cho tester thấy những gì?

Một trace là hồ sơ của một request xuyên qua hệ thống. Trong AI agent, span cấp cao có thể biểu diễn `invoke_agent`, bên dưới là các span `chat` cho từng lần gọi model và `execute_tool` cho từng tool. Nhìn cây span giúp tester biết câu trả lời chậm vì model xử lý lâu, tool trả về chậm hay agent retry quá nhiều.

Các thuộc tính thường hữu ích gồm model được gọi, input token, output token, finish reason và thời lượng. Với test hiệu năng hoặc regression, bạn có thể so sánh cùng một scenario giữa hai phiên bản model. Với test failure, trace ID giúp nối kết quả trên UI với request ở backend và log của service.

Một cách đọc trace đơn giản là đi từ ngoài vào trong. Xác nhận span gốc có đúng scenario. Tìm span có duration bất thường. Kiểm tra child span cuối cùng trước khi failure. Sau đó đối chiếu expected behavior với response hoặc tool result, không kết luận root cause chỉ vì một span dài.

<table-testcase>
| Câu hỏi khi debug | Signal nên xem | Điều cần kết luận |
| --- | --- | --- |
| Vì sao phản hồi chậm? | Trace duration và child spans | Bước chậm đầu tiên cần điều tra |
| Model có dùng quá nhiều token? | Token usage metric | Có regression hoặc prompt phình to không |
| Agent dừng vì lý do gì? | Finish reason và event | Dừng bình thường, tool call hay lỗi |
| Tool trả dữ liệu sai? | Tool span và tool result | Lỗi nằm trước hay sau lần gọi tool |
</table-testcase>

## Metric giúp phát hiện regression như thế nào?

Metric là dữ liệu tổng hợp theo thời gian, model, service hoặc scenario. OpenTelemetry GenAI observability minh họa các metric về thời lượng gọi client và token usage. Tester có thể dùng chúng để phát hiện prompt làm tăng token, model mới làm tăng latency hoặc tỷ lệ lỗi tăng sau khi thay đổi tool.

Đừng dùng một ngưỡng duy nhất cho mọi scenario. Một chatbot hỏi đáp đơn giản và một agent thực hiện nhiều tool call có baseline khác nhau. Hãy ghi rõ scenario, model, dữ liệu và môi trường khi thu metric. Nếu chỉ nhìn average, bạn có thể bỏ lỡ một nhóm request rất chậm, vì vậy nên xem thêm percentile hoặc phân phối khi hệ thống hỗ trợ.

<multiple-choice correct="B" select="single">
Sau khi đổi prompt, p95 latency tăng rõ rệt nhưng câu trả lời vẫn đúng. Bước tiếp theo hợp lý nhất là gì?
- A: Xóa toàn bộ trace vì output vẫn đúng
- B: So sánh token usage, child span và scenario trước khi kết luận nguyên nhân
- C: Tăng timeout cho mọi test
- D: Kết luận model mới bị hỏng
</multiple-choice>

Với người mới, hãy bắt đầu bằng một bảng baseline nhỏ: scenario, model, thời lượng, input token, output token và kết quả evaluation. Mục tiêu không phải thu mọi dữ liệu, mà là tạo đủ bằng chứng để nhận ra thay đổi đáng kể.

![Wide 21:9 educational dashboard visual showing three aligned panels for an AI test baseline: a latency chart, a token usage chart, and a pass or fail evaluation list. Labels exactly 'Latency', 'Token usage', and 'Evaluation'. Use blue bars and lines, amber markers for regression, no fake numbers, no axes with dense text. Minimalist flat vector UI design, premium professional EdTech editorial artwork, clean horizontal bento-grid, Paper White background #fafafa, Zinc-900 content #18181b, T5Edu Blue accent #1a73e8, Amber highlight #f59e0b, subtle one-pixel borders, no people, no faces, no hands, no 3D, no photorealism, no purple, no violet, no pink, no neon, no logo, no watermark](https://files.manuscdn.com/user_upload_by_module/session_file/310519663091035343/vuohrrWqKGnVPmmo.png)

![Wide 21:9 educational visual showing a redacted telemetry review board with cards labeled exactly Prompt, Tool result, Trace ID, and Redaction, with sensitive data represented by blacked-out bars and a blue bug report card. Use amber warning accents and blue evidence arrows. Minimalist flat vector UI design, premium professional EdTech editorial artwork, clean horizontal bento-grid, Paper White background #fafafa, Zinc-900 content #18181b, T5Edu Blue accent #1a73e8, Amber highlight #f59e0b, subtle one-pixel borders, no dense text, no people, no faces, no hands, no 3D, no photorealism, no purple, no violet, no pink, no neon, no logo, no watermark](https://files.manuscdn.com/user_upload_by_module/session_file/310519663091035343/hVbHmROouDIytFlG.png)

## Có nên ghi prompt và response đầy đủ không?

Telemetry có thể ghi metadata như model, duration và token mà không ghi toàn bộ nội dung. Theo [hướng dẫn GenAI observability của OpenTelemetry](https://opentelemetry.io/blog/2026/genai-observability/), việc capture full prompt, completion, tool argument và tool result cần được bật có chủ đích vì chúng có thể chứa dữ liệu nhạy cảm. Tài liệu [monitoring agents của VS Code](https://code.visualstudio.com/docs/copilot/guides/monitoring-agents) cũng cho phép cấu hình mức telemetry và capture content riêng. Trong môi trường local, capture content có thể hữu ích để debug. Trong môi trường dùng dữ liệu thật, tester phải tuân thủ policy redaction, access control và retention.

Một log tốt không phải là dump toàn bộ object. Trước khi bật content capture, hãy trả lời: dữ liệu này có chứa password, token, thông tin cá nhân hoặc nội dung khách hàng không; ai được xem; lưu trong bao lâu; và có thể mask phần nào mà vẫn debug được không. Nếu chỉ cần kiểm tra routing, model name và token count có thể đã đủ.

<dropdown-content>
Checklist bảo vệ dữ liệu khi test AI app
> Dùng dữ liệu giả và bắt đầu với metadata tối thiểu.
```markdown
- Không ghi access token, refresh token hoặc password.
- Không gửi prompt của khách hàng thật vào dashboard thử nghiệm.
- Mask email, số điện thoại và mã định danh không cần thiết.
- Phân quyền dashboard theo vai trò.
- Ghi rõ khi nào full content được capture.
- Đặt thời gian lưu và quy trình xóa evidence.
- Khi bug report cần nội dung, trích đoạn tối thiểu có thể tái tạo lỗi.
```
</dropdown-content>

## Làm sao biến trace thành testcase và bug report?

Testcase AI nên có hai lớp expected result. Lớp thứ nhất là hành vi hệ thống: request được route đúng, tool được gọi đúng điều kiện, không retry vô hạn và response có schema hợp lệ. Lớp thứ hai là chất lượng output: câu trả lời có đáp ứng rubric, không bịa thông tin, không lộ dữ liệu và phù hợp ngữ cảnh.

Khi test fail, bắt đầu bằng evidence tối thiểu: scenario, input đã được làm sạch, model, trace ID, duration, finish reason, tool result và tiêu chí output không đạt. Sau đó mô tả first abnormal signal. Nếu model trả lời sai nhưng tool result đã sai từ trước, bug report nên chỉ ra tool hoặc data layer cần điều tra, thay vì ghi chung chung “AI trả lời sai”.

```text
Scenario: Tìm trạng thái đơn hàng bằng mã test ORD-001
Expected: Trả về trạng thái shipped và không hiển thị dữ liệu người khác
Observed: Agent gọi đúng tool nhưng tool trả về mã đơn ORD-002
Evidence: trace_id, tool span, redacted tool result, evaluation output
Next action: Kiểm tra mapping order_id trong service truy vấn
```

Bạn có thể kết hợp cách này với [API Testing cơ bản](/courses/api-testing-co-ban) để xác minh contract của tool trước khi đánh giá câu trả lời. Nếu pipeline có nhiều test chạy song song, [Cách khắc phục flaky test cho SDET](/blogs/cach-khac-phuc-flaky-test-cho-sdet) giúp bạn tránh nhầm lỗi hạ tầng với lỗi sản phẩm.

## Bắt đầu với OpenTelemetry GenAI observability từ đâu?

Đừng bắt đầu bằng việc instrument toàn bộ hệ thống. Chọn một journey có giá trị, chẳng hạn agent trả lời câu hỏi và gọi một tool. Xác định một trace gốc, một vài span cần có, hai metric baseline và một policy redaction. Sau đó chạy cùng một scenario nhiều lần để phân biệt biến động tự nhiên với regression.

Quy trình học thực tế có thể gồm bốn bước. Trước hết, vẽ request flow và đánh dấu nơi gọi model, nơi gọi tool. Tiếp theo, bật metadata telemetry tối thiểu và kiểm tra trace tree. Sau đó thêm metric latency và token usage. Cuối cùng, nối evidence với evaluation rubric và bug report.

[OpenTelemetry GenAI semantic conventions](https://github.com/open-telemetry/semantic-conventions-genai) vẫn đang phát triển cho GenAI. Vì vậy, hãy kiểm tra phiên bản tài liệu và backend đang dùng, không hard-code giả định rằng mọi vendor có cùng tên field hoặc cùng mức hỗ trợ. Khi schema thay đổi, review lại dashboard, parser và validator của bạn.

## Tổng kết

- Trace cho biết một AI request đã đi qua model và tool nào, metric giúp theo dõi xu hướng, event bổ sung ngữ cảnh.
- Observability giải thích hệ thống đã làm gì, còn evaluation xác định output có đạt yêu cầu hay không.
- Bắt đầu bằng metadata tối thiểu, dữ liệu giả và một journey có thể tái tạo.
- Khi test AI app, hãy ghi first abnormal signal và trace ID thay vì chép toàn bộ log.
- Nếu một câu trả lời AI sai, bạn sẽ kiểm tra output, tool result hay trace tree trước, và vì sao?

## Hashtag

opentelemetry, genai, ai testing, testobservability, softwaretesting, qa
