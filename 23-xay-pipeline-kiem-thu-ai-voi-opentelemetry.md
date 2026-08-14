# Hướng dẫn xây pipeline kiểm thử AI với OpenTelemetry

> Một hướng dẫn thực hành giúp QA engineer và developer instrument AI app, xuất telemetry qua OTLP, đặt baseline cho latency và token, bảo vệ dữ liệu nhạy cảm rồi dùng trace để triage lỗi thay vì chỉ nhìn câu trả lời cuối.

![Square editorial cover showing a QA engineer's AI testing pipeline with an AI application on the left, an OpenTelemetry trace tree in the center, and a regression dashboard on the right. Show three concise labels exactly Trace, Metric, and Event. Minimalist flat vector UI design. Premium professional EdTech editorial artwork. Clean bento-grid composition with strong negative space. Paper White background #fafafa. Zinc-900 content #18181b. T5Edu Blue accent #1a73e8. Amber highlight #f59e0b. Subtle one-pixel borders and restrained liquid-glass layers. Simple geometric icons, crisp edges, professional engineering documentation style. No people, no faces, no hands, no photorealism, no 3D, no purple, no violet, no pink, no neon, no logo, no watermark](https://files.manuscdn.com/user_upload_by_module/session_file/310519663091035343/pCGZzBZODMnnCWfH.png)

## Bài toán không nằm ở việc thiếu log

Một AI app có thể nhận request, tạo prompt, gọi model, dùng tool, retry, truy vấn dữ liệu rồi mới trả về câu trả lời. Khi output sai hoặc chậm, một log kiểu `request failed` không cho biết bước nào tạo ra lỗi. QA cần evidence nối được request của người dùng với từng lần gọi model, tool result, latency, token usage và kết quả evaluation.

Bài chọn một góc thực hành: xây pipeline observability tối thiểu để phục vụ kiểm thử AI. Mục tiêu không phải instrument mọi thứ ngay từ đầu, cũng không phải chọn một dashboard đắt tiền. Mục tiêu là tạo ra dữ liệu đủ nhất quán để trả lời bốn câu hỏi: request đã đi qua đâu, bước nào bất thường, output có đạt tiêu chí không, và regression bắt đầu từ thay đổi nào.

[OpenTelemetry](https://opentelemetry.io/) cung cấp lớp chuẩn để thu thập và xuất telemetry. Với GenAI, repository [OpenTelemetry GenAI Semantic Conventions](https://github.com/open-telemetry/semantic-conventions-genai) mô tả các convention cho spans, metrics và events của GenAI client, MCP và một số provider. Các convention này vẫn đang phát triển, vì vậy pipeline phải ghi rõ version, backend và phạm vi field được hỗ trợ.

<grid-content>
Pipeline tối thiểu cho một AI test run
> Mỗi lớp trả lời một câu hỏi khác nhau và không thể thay thế hoàn toàn cho lớp còn lại.
```markdown
**Instrumentation**
- Gắn trace và span vào request, model call, tool call
- Ghi model, provider, finish reason và token usage
- Tạo trace ID để nối UI, service và bug report
```
```markdown
**Collector hoặc exporter**
- Nhận dữ liệu qua OTLP
- Redact, sample, enrich hoặc route trước khi export
- Tách local debugging khỏi production telemetry
```
```markdown
**Evaluation và triage**
- Chấm output theo rubric hoặc assertion
- So sánh baseline về latency, token và lỗi
- Dùng trace để tìm first abnormal signal
```
</grid-content>

Bạn có thể đọc thêm [Test observability cho người mới](/blogs/test-observability-cho-nguoi-moi). Phần còn lại tập trung vào cách biến telemetry thành một phần của quy trình test AI.

![Wide 21:9 architecture diagram showing an AI test pipeline from Test scenario to AI application to OpenTelemetry SDK to OTLP Collector to Observability backend and Evaluation report. Use blue arrows left to right, an amber governance checkpoint at the Collector, and concise labels exactly Kịch bản test, AI app, OTel SDK, Collector, Backend, and Evaluation. Minimalist flat vector UI design. Premium professional EdTech editorial artwork. Clean horizontal bento-grid composition with strong negative space. Paper White background #fafafa. Zinc-900 content #18181b. T5Edu Blue accent #1a73e8. Amber highlight #f59e0b. Subtle one-pixel borders. No people, no faces, no hands, no 3D, no photorealism, no purple, no violet, no pink, no neon, no logo, no watermark](https://files.manuscdn.com/user_upload_by_module/session_file/310519663091035343/muufTACIYcLMKlCU.png)

## Xác định telemetry cần có trước khi instrument

Một sai lầm phổ biến là bật capture content trước rồi mới nghĩ xem cần dùng dữ liệu đó để làm gì. Cách an toàn hơn là bắt đầu từ test question. Nếu muốn biết model nào được gọi, metadata về model và provider là đủ. Nếu muốn điều tra tool trả sai dữ liệu, cần tool span, status và một bản tool result đã redaction. Nếu muốn tìm bottleneck, cần duration của root span và child span.

OpenTelemetry mô hình hóa một lần thực thi bằng trace chứa nhiều span. Với agent, span cấp cao có thể mô tả `invoke_agent`, bên dưới là các span `chat` cho model call và `execute_tool` cho tool call. Các field như `gen_ai.request.model`, `gen_ai.usage.input_tokens`, `gen_ai.usage.output_tokens` và `gen_ai.response.finish_reasons` giúp QA lọc và so sánh các lần chạy.

| Câu hỏi kiểm thử | Dữ liệu tối thiểu | Không nên suy luận quá mức |
| --- | --- | --- |
| Vì sao request chậm? | Root duration và child span duration | Span dài chưa chắc là root cause |
| Prompt có làm tăng chi phí? | Input token, output token và scenario version | Token tăng không tự động chứng minh output tốt hơn |
| Tool có được gọi đúng? | Tool span, arguments đã mask và result đã mask | Không ghi full secret chỉ để tiện debug |
| Output có đạt yêu cầu? | Evaluation score hoặc assertion và trace ID | Trace không thay thế evaluation |

Hãy định nghĩa schema nội bộ cho test run trước khi chọn dashboard. Một record có thể gồm `scenario_id`, `prompt_version`, `model`, `trace_id`, `evaluation_result`, `input_tokens`, `output_tokens`, `duration_ms` và `environment`. Khi schema ổn định, bạn có thể đổi backend mà không phải viết lại toàn bộ testcase.

## Thiết lập đường xuất OTLP cho môi trường test

Pipeline có ba điểm cần phân biệt. Instrumentation tạo dữ liệu. OTLP exporter gửi dữ liệu. Collector hoặc backend nhận, xử lý và hiển thị dữ liệu. Trong local development, một OTLP-compatible backend như Aspire Dashboard có thể nhận telemetry và cho xem traces, metrics cùng structured logs. Bài hướng dẫn chính thức của OpenTelemetry dùng chính mô hình này để quan sát GenAI workload mà không cần bắt đầu bằng một tài khoản cloud.

Pipeline local có thể mô tả như sau:

```text
Test runner
  -> AI application
  -> OpenTelemetry SDK
  -> OTLP endpoint localhost:4318
  -> Collector hoặc dashboard
  -> Evaluation result và bug report
```

Khi chạy Docker, hãy cấu hình thống nhất endpoint giữa exporter và receiver. Đừng trộn port OTLP HTTP với OTLP gRPC mà không kiểm tra tài liệu backend. Sau lần chạy đầu tiên, hãy xác nhận ba điều: trace xuất hiện, span con được nối đúng parent, và metric có cùng `scenario_id` hoặc thuộc tính đủ để lọc.

![Wide 21:9 technical illustration of an OTLP telemetry pipeline with three compact panels labeled exactly Instrumentation, OTLP, and Dashboard, showing blue signal lines for traces, metrics, and events flowing through a collector. Include small labels exactly Trace, Metric, Event, and Export. Minimalist flat vector UI design. Premium professional EdTech editorial artwork. Clean horizontal bento-grid composition with strong negative space. Paper White background #fafafa. Zinc-900 content #18181b. T5Edu Blue accent #1a73e8. Amber highlight #f59e0b. Subtle one-pixel borders and restrained liquid-glass layers. No people, no faces, no hands, no photorealism, no 3D, no purple, no violet, no pink, no neon, no logo, no watermark](https://files.manuscdn.com/user_upload_by_module/session_file/310519663091035343/hVbHmROouDIytFlG.png)

Nếu hệ thống dùng MLflow, tài liệu [OpenTelemetry GenAI Semantic Conventions của MLflow](https://mlflow.org/docs/latest/genai/tracing/opentelemetry/genai-semconv/) mô tả cách export trace theo field `gen_ai.*`. MLflow có thể map model, provider, token usage, input messages và output messages sang convention này. Mô hình dual export cũng cho phép giữ nơi lưu trữ thí nghiệm hiện tại đồng thời gửi trace đến một backend OTel-compatible khác.

## Viết testcase kết hợp behavior, quality và telemetry

AI testcase không nên chỉ có assertion kiểu “response không rỗng”. Hãy tách expected result thành ba lớp. Lớp thứ nhất là behavior, chẳng hạn agent gọi đúng tool khi input chứa mã đơn hàng. Lớp thứ hai là output quality, chẳng hạn câu trả lời đúng trạng thái, không bịa và không hiển thị order của người khác. Lớp thứ ba là telemetry contract, chẳng hạn trace có model, tool span, finish reason và token usage cần thiết.

```ts
import { test, expect } from '@playwright/test';

test('order agent uses the lookup tool and returns a safe status', async ({ page }) => {
  const traceIds: string[] = [];

  await page.goto('/support-agent');
  await page.getByRole('textbox', { name: 'Question' })
    .fill('Kiểm tra trạng thái đơn ORD-001');
  await page.getByRole('button', { name: 'Send' }).click();

  await expect(page.getByTestId('assistant-answer'))
    .toContainText('shipped');

  const traceId = await page.getAttribute('[data-trace-id]', 'data-trace-id');
  expect(traceId).toBeTruthy();
  traceIds.push(traceId as string);

  const answer = await page.getByTestId('assistant-answer').textContent();
  expect(answer).not.toContain('ORD-002');
});
```

Đây là ví dụ UI assertion. Trong hệ thống thật, telemetry contract thường được kiểm tra ở service hoặc test harness riêng, vì trace backend có thể xuất hiện trễ hơn response. Không nên để UI test phụ thuộc cứng vào dashboard. Hãy lấy trace ID từ response header, test context hoặc correlation field rồi truy vấn telemetry bằng một bước kiểm tra có timeout hợp lý.

<table-testcase>
| Lớp testcase | Expected result | Evidence khi fail |
| --- | --- | --- |
| Behavior | Tool đúng được gọi với input hợp lệ | Tool span và arguments đã mask |
| Quality | Output đúng rubric, không lộ dữ liệu | Evaluation output và response đã mask |
| Telemetry | Có root span, model span và trace ID | Trace query và span attributes |
| Performance | Latency hoặc token nằm trong ngưỡng baseline | Metric theo scenario và model |
</table-testcase>

## Đặt baseline và triage regression

Baseline không phải một con số duy nhất. Cùng một model có thể có latency khác nhau giữa câu hỏi đơn giản và agent gọi nhiều tool. Một baseline hữu ích phải gắn với scenario, model, prompt version, dữ liệu test và môi trường. Với mỗi nhóm scenario, hãy lưu median hoặc percentile latency, input token, output token, tỷ lệ lỗi và evaluation score.

Khi regression xuất hiện, đừng tăng timeout hoặc đổi model ngay. Hãy so sánh theo thứ tự: scenario có giống nhau không, prompt version có đổi không, token usage có phình lên không, child span nào tăng duration, tool result có khác baseline không, rồi evaluation mới giảm ở đâu. Mục tiêu là tìm first abnormal signal, không chỉ ghi symptom cuối.

<multiple-choice correct="B" select="single">
Sau khi đổi prompt, p95 latency tăng nhưng evaluation score không đổi. Bước kiểm tra nào hợp lý nhất?
- A: Tăng timeout cho toàn bộ pipeline
- B: So sánh input token, child span và prompt version với baseline
- C: Xóa trace vì output vẫn đạt
- D: Kết luận model đã bị lỗi
</multiple-choice>

Một regression report nên chứa cả kết quả và đường điều tra. Ví dụ:

```text
Scenario: Tra cứu trạng thái đơn hàng
Change: prompt_version v18 -> v19
Observed: p95 tăng từ baseline, output score không đổi
First abnormal signal: input_tokens tăng ở span chat
Evidence: trace_id, model, prompt hash, child spans, redacted tool result
Next action: review prompt context và kiểm tra dữ liệu được đưa vào template
```

![Wide 21:9 regression triage dashboard showing three aligned panels labeled exactly Latency, Token usage, and Evaluation, with blue baseline lines, amber regression markers, and a trace ID linking the panels to a bug report. Include a small table of scenario and prompt version, no fake numeric values. Minimalist flat vector UI design. Premium professional EdTech editorial artwork. Clean horizontal bento-grid composition with strong negative space. Paper White background #fafafa. Zinc-900 content #18181b. T5Edu Blue accent #1a73e8. Amber highlight #f59e0b. Subtle one-pixel borders. No people, no faces, no hands, no 3D, no photorealism, no purple, no violet, no pink, no neon, no logo, no watermark](https://files.manuscdn.com/user_upload_by_module/session_file/310519663091035343/vuohrrWqKGnVPmmo.png)

## Redaction phải nằm trước nơi lưu trữ

Prompt, system instruction, tool argument và tool result có thể chứa password, access token, thông tin cá nhân hoặc dữ liệu khách hàng. OpenTelemetry cho phép capture content khi được bật có chủ đích, nhưng mặc định metadata tối thiểu thường an toàn hơn. Vì vậy, đừng coi redaction là việc dọn dẹp sau khi log đã vào dashboard.

Collector là điểm phù hợp để áp dụng policy tập trung: mask field nhạy cảm, giới hạn retention, sampling trace và route dữ liệu theo môi trường. Bài viết của Datadog về OTel GenAI Semantic Conventions cũng nhấn mạnh Collector có thể xử lý redaction, enrichment, sampling và routing trước khi telemetry rời khỏi network. Đây là góc kiến trúc cần xem xét dù backend cuối cùng là Datadog, MLflow, Aspire Dashboard hay hệ thống khác.

<dropdown-content>
Checklist review telemetry trước khi chạy với dữ liệu thật
```markdown
- Dùng dữ liệu giả hoặc dữ liệu đã được ẩn danh trong test suite.
- Không ghi password, access token, refresh token hoặc secret vào span.
- Mask email, số điện thoại, mã khách hàng và nội dung không cần cho triage.
- Tách endpoint local, staging và production bằng policy riêng.
- Xác định ai được xem prompt, tool argument và tool result.
- Đặt retention, sampling và quy trình xóa evidence.
- Kiểm tra bug report chỉ giữ đoạn tối thiểu để tái tạo lỗi.
```
</dropdown-content>

Không nên biến redaction thành lý do xóa toàn bộ evidence. Nếu cần điều tra tool trả sai, có thể giữ hash của input, loại tool, status, latency và một result đã mask. Với trường hợp cần xem content đầy đủ trong local, hãy tách profile debug khỏi profile chạy dữ liệu thật và ghi rõ profile nào đã được sử dụng.

## Từ trace production đến golden test set

Observability có giá trị hơn khi kết quả điều tra quay lại test suite. Trace production đã redaction có thể trở thành scenario đại diện cho lỗi thực tế. QA có thể giữ prompt hash, tool flow, expected rubric và evaluation label, sau đó dùng nó làm golden test case khi thay prompt, model hoặc orchestration.

Datadog mô tả cách chuyển các production trace đáng chú ý thành dataset có version, bổ sung annotation và evaluation metadata để chạy lại experiment. Ý tưởng này không phụ thuộc vào một vendor: điều quan trọng là trace phải có đủ context để tái tạo, dữ liệu phải được phép lưu, và scenario phải được tách khỏi thông tin định danh thật.

Workflow có kiểm soát có thể là:

```text
Production trace đã redaction
  -> Xác nhận policy và loại PII
  -> Tách scenario, expected rubric, tool contract
  -> Đưa vào golden test set có version
  -> Chạy model hoặc prompt mới
  -> So sánh evaluation, latency, token và trace
  -> Review trước khi cập nhật baseline
```

Nếu tool phía sau AI có API contract riêng, hãy kết hợp với [API Testing cơ bản](/courses/api-testing-co-ban) để kiểm tra service trước khi kết luận model sai. Khi failure dao động theo thời gian hoặc môi trường, [Cách khắc phục flaky test cho SDET](/blogs/cach-khac-phuc-flaky-test-cho-sdet) giúp tách flaky signal khỏi regression thật.

## Khi nào OpenTelemetry chưa phải lựa chọn đầu tiên?

OpenTelemetry phù hợp khi team cần một lớp telemetry mở, có correlation giữa AI workload và service, hoặc muốn giảm phụ thuộc vào schema độc quyền của một backend. Nó không tự tạo evaluation rubric, không tự chứng minh câu trả lời đúng và không tự giải quyết data governance.

Nếu AI app còn là prototype một endpoint, bạn có thể bắt đầu bằng một trace root, một model span, một metric latency và một evaluation log. Khi có tool call, retry hoặc nhiều provider, hãy mở rộng schema và thêm Collector policy. Khi đã có production traffic, cần đánh giá chi phí lưu trữ, sampling, access control và khả năng biến trace thành golden test set.

Đừng chọn backend trước rồi mới định nghĩa câu hỏi kiểm thử. Hãy xác định evidence cần thiết, đặt schema tối thiểu, thử trên local, kiểm tra redaction, sau đó mới quyết định dashboard hay vendor nào đáp ứng tốt nhất.

## Tổng kết

- Một pipeline kiểm thử AI có thể bắt đầu từ instrumentation, OTLP export, trace review và evaluation, không cần instrument toàn bộ hệ thống.
- Trace giúp điều tra execution path, metric giúp nhận diện xu hướng, còn evaluation xác định output có đạt yêu cầu hay không.
- Baseline phải gắn với scenario, model, prompt version và môi trường; khi regression xảy ra, hãy tìm first abnormal signal.
- Redaction, sampling và retention nên được áp dụng trước nơi lưu trữ, đặc biệt khi capture prompt hoặc tool result.
- Nếu team của bạn đã có AI app, hãy chọn một journey có tool call và thiết kế telemetry contract cho journey đó trước khi mở rộng sang toàn hệ thống.

## Hashtag

opentelemetry, genai, aitesting, observability, softwaretesting
