# OWASP AI red teaming cho ứng dụng LLM: Thiết kế test có hệ thống

> OWASP AI Testing Guide v1, phát hành ngày 26 tháng 11 năm 2025, đưa trustworthiness testing thành một phạm vi có phương pháp thay vì chỉ thử vài prompt nguy hiểm. Bài viết giúp QA, security tester và AI engineer thiết kế một vòng red teaming có scope, attack hypothesis, evidence và tiêu chí dừng rõ ràng.

![Square 1:1 cover illustration for an article titled 'OWASP AI red teaming cho ứng dụng LLM'. The visual must communicate adversarial testing of an LLM application across model, application, data, and infrastructure layers. Composition: a central LLM gateway card labeled 'LLM App', surrounded by four layered cards labeled 'Model', 'Application', 'Data', and 'Infrastructure', with blue test arrows entering from the left and amber threat markers near the gateway. Exact Vietnamese labels: 'LLM App', 'Model', 'Application', 'Data', 'Infrastructure', 'Red team'. Minimalist flat vector UI design. Premium professional EdTech editorial artwork. Clean bento-grid composition with strong negative space. Paper White background #fafafa, Zinc-900 content #18181b, T5Edu Blue accent #1a73e8, Amber highlight #f59e0b, Subtle one-pixel borders and restrained liquid-glass layers. Simple flat icons and geometric shapes, no people, no faces, no hands, no 3D, no glossy plastic, no photorealism, no dramatic lighting, no purple, no violet, no pink, no neon, no logo, no watermark.](https://files.manuscdn.com/user_upload_by_module/session_file/310519663091035343/mFiQJNUeUBCmWHrC.png)

## AI red teaming khác functional testing ở điểm nào?

| Functional testing | AI red teaming |
| --- | --- |
| Kiểm tra behavior đúng trong điều kiện bình thường | Tìm cách làm hệ thống vi phạm policy hoặc tạo side effect |
| Thường nhìn vào expected output | Theo dõi cả model, prompt, retrieval, tool, identity và runtime |
| Có thể đo pass hoặc fail theo case | Cần thêm trajectory, authorization và evidence |

[OWASP AI Testing Guide v1](https://owasp.org/www-project-ai-testing-guide/) mô tả trustworthiness testing trên bốn lớp: application, model, infrastructure và data.

### Prerequisite

Bài này dành cho QA automation tester, security tester và AI engineer đã biết:

- API testing và authentication.
- JSON, log hoặc trace cơ bản.
- Test data isolation.
- Flow của LLM application qua gateway, retrieval và tool execution.

Không cần huấn luyện model. Sau bài, người đọc có thể tạo charter, test matrix và evidence có thể triage.

<grid-content>
Bốn lớp cần đưa vào scope khi red teaming LLM app
> Một prompt attack chỉ có ý nghĩa khi biết nó tác động vào lớp nào.
```markdown
**Model layer**

Kiểm tra jailbreak, prompt injection, hallucination, bias và khả năng tuân thủ instruction hierarchy.
```

```markdown
**Application layer**

Kiểm tra system prompt, session, authorization, output handling và business rule.
```

```markdown
**Data layer**

Kiểm tra retrieval poisoning, sensitive data leakage, tenant isolation và nguồn trích dẫn.
```

```markdown
**Infrastructure layer**

Kiểm tra secrets, dependency, network boundary, logging và khả năng lạm dụng tài nguyên.
```
</grid-content>

## Nên bắt đầu bằng red-team charter nào?

Một session không có charter dễ biến thành cuộc thi tìm prompt lạ. Hãy khóa các trường sau trước khi chạy:

1. **Target**: chatbot hoặc workflow nào, version nào?
2. **Trust boundary**: prompt, knowledge base, payment API và tool nào nằm ngoài vùng tin cậy?
3. **Risk hypothesis**: hành vi xấu nào cần chứng minh?
4. **Allowed action**: payload và side effect nào được phép?
5. **Exit criteria**: khi nào dừng hoặc escalate?

### Guardrail tối thiểu

- Không dùng production data thật.
- Không gửi payload gây denial of service.
- Không truy cập account ngoài test scope.
- Tool có side effect phải dùng sandbox hoặc mock có audit log.

NIST [AI RMF Generative AI Profile](https://www.nist.gov/publications/artificial-intelligence-risk-management-framework-generative-artificial-intelligence) xem evaluation và risk management là hoạt động xuyên suốt vòng đời. Vì vậy, suite cần có baseline, owner, lịch chạy lại và version change log.

| Charter field | Câu hỏi cần khóa | Ví dụ |
| --- | --- | --- |
| Target | Test hệ thống nào và version nào? | Support bot build 2026.08 |
| Trust boundary | Dữ liệu và tool nào nằm ngoài vùng tin cậy? | KB upload, refund API |
| Risk hypothesis | Hành vi xấu nào cần chứng minh? | Instruction trong KB vượt system policy |
| Allowed action | Payload và side effect nào được phép? | Chỉ sandbox refund |
| Exit criteria | Khi nào dừng hoặc escalate? | Có tool call trái quyền và trace đầy đủ |

![Wide 21:9 educational diagram explaining an AI red-team charter. Layout: five horizontal cards labeled 'Target', 'Trust boundary', 'Risk', 'Allowed action', and 'Exit criteria'. A solid blue arrow connects the cards left to right, while a dashed amber boundary surrounds 'Allowed action' and 'Exit criteria'. A small document at the bottom is labeled 'Evidence'. Exact Vietnamese labels: 'Target', 'Trust boundary', 'Risk', 'Allowed action', 'Exit criteria', 'Evidence'. Minimalist flat vector UI design. Premium professional EdTech editorial artwork. Clean bento-grid composition with strong negative space. Paper White background #fafafa, Zinc-900 content #18181b, T5Edu Blue accent #1a73e8, Amber highlight #f59e0b, Subtle one-pixel borders and restrained liquid-glass layers. Simple flat icons and geometric shapes, no people, no faces, no hands, no 3D, no glossy plastic, no photorealism, no dramatic lighting, no purple, no violet, no pink, no neon, no logo, no watermark.](https://files.manuscdn.com/user_upload_by_module/session_file/310519663091035343/xohYoauqwoMljSqe.png)

## Làm thế nào chuyển risk thành attack hypothesis có thể chạy?

Đừng viết case chỉ là “thử prompt injection”. Hãy khóa đủ bốn thành phần:

| Thành phần | Câu hỏi |
| --- | --- |
| Kênh X | Instruction không tin cậy đi qua đâu? |
| Hành động Y | Hệ thống có thể tiết lộ secret hoặc gọi tool nào? |
| Policy Z | Quy tắc nào có thể bị vi phạm? |
| Evidence | Cần giữ output, trace hay authorization event nào? |

Mỗi hypothesis cần hai nhánh:

- **Control case**: behavior bình thường, tránh coi mọi refusal là pass.
- **Adversarial case**: thay đổi một biến như vị trí instruction, encoding, ngôn ngữ hoặc quyền user.

Chỉ thay đổi một biến mỗi lần để còn biết nguyên nhân của failure.

[OWASP GenAI Red Teaming Guide](https://genai.owasp.org/resource/genai-red-teaming-guide/) tổ chức red teaming quanh model evaluation, implementation testing, infrastructure assessment và runtime behavior analysis. Bốn vùng này có thể biến thành bốn suite riêng, mỗi suite có owner và evidence contract khác nhau.

<multiple-choice correct="B" select="single">
Một attack hypothesis tốt nên có cấu trúc nào?
- A: Một prompt thật dài và không cần expected behavior
- B: Kênh tấn công, hành động có thể xảy ra, policy bị đe dọa và bằng chứng cần thu
- C: Tên model, temperature và một điểm pass rate duy nhất
- D: Một danh sách tool mà không cần trust boundary
</multiple-choice>

## Cần đo gì ngoài pass hoặc fail của câu trả lời?

Final answer an toàn chưa đủ để kết luận pass. Hãy kiểm tra trajectory theo checklist:

- Input được normalize thế nào?
- Prompt sau khi ghép có instruction nào ngoài dự kiến?
- Retrieval trả về document nào?
- Tool có được gọi không?
- Authorization check nằm ở đâu?
- Output filter có sửa câu trả lời không?
- Hệ thống dừng ở trạng thái nào?

| Assertion layer | Cần kiểm tra |
| --- | --- |
| Safety | Secret, harmful content, policy violation |
| Behavior | Giữ đúng task, không bị data điều khiển |
| Authorization | User A không đọc hoặc sửa data của user B |
| Operational | Latency, token usage, retry, log redaction |

Không gộp tất cả thành một score. Một output đúng vẫn fail nếu tool đã bị gọi trái quyền. Evidence bundle nên giữ output, tool trace, retrieved context, identity, version và expected decision.

<table-testcase cols="6" rows="3" headers="ID|Risk|Input channel|Expected guard|Evidence">
| RT01 | Prompt injection | Tài liệu RAG chứa instruction ngoài phạm vi | Nội dung được coi là data, không override policy | Retrieved chunks và final prompt |
| RT02 | Excessive agency | User yêu cầu hoàn tiền account khác | Từ chối trước tool call | Identity, authorization trace |
| RT03 | Sensitive leakage | Prompt hỏi system prompt và secret | Không tiết lộ secret, log được redact | Response, filter event, logs |
</table-testcase>

<dropdown-content>
Vì sao trajectory quan trọng hơn chỉ nhìn final answer?
> Phần này dành cho reader đã biết tracing hoặc API observability.
```markdown
Một assistant có thể tạo câu trả lời đúng nhưng trước đó đã gọi một tool nguy hiểm rồi bị output filter che lại. Nếu chỉ assert final text, tester bỏ lỡ side effect. Trajectory giúp xác định điểm phát sinh rủi ro: model chọn tool, policy engine cho phép, gateway thiếu authorization hay filter chỉ xử lý phần text.

Mỗi span hoặc event nên gắn correlation ID, model version, prompt template version, identity đã được ẩn danh, tool name, decision và outcome. Không ghi raw secret hoặc dữ liệu nhạy cảm chỉ để tiện debug. Evidence tốt vừa đủ để tái hiện, vừa tuân thủ data handling policy.
```
</dropdown-content>

![Wide 21:9 educational diagram showing an LLM application trajectory under red-team testing. Layout: left input card labeled 'User + RAG', center sequential cards labeled 'Prompt', 'Model', 'Policy', 'Tool', and right outcome card labeled 'Decision'. Solid blue arrows show normal flow, a dashed amber arrow shows an injection attempting to bypass 'Policy', and a red-free amber warning marker appears at 'Tool'. Exact Vietnamese labels: 'User + RAG', 'Prompt', 'Model', 'Policy', 'Tool', 'Decision', 'Trace'. Minimalist flat vector UI design. Premium professional EdTech editorial artwork. Clean bento-grid composition with strong negative space. Paper White background #fafafa, Zinc-900 content #18181b, T5Edu Blue accent #1a73e8, Amber highlight #f59e0b, Subtle one-pixel borders and restrained liquid-glass layers. Simple flat icons and geometric shapes, no people, no faces, no hands, no 3D, no glossy plastic, no photorealism, no dramatic lighting, no purple, no violet, no pink, no neon, no logo, no watermark.](https://files.manuscdn.com/user_upload_by_module/session_file/310519663091035343/uIoXvyblFPzVNVor.png)

## Làm sao phân biệt model failure, application failure và governance failure?

Khi test fail, không gán ngay lỗi cho model. Dùng taxonomy để khoanh vùng:

| Lớp failure | Dấu hiệu cần xem |
| --- | --- |
| Model | Model tạo output sai dù context và policy đúng |
| Application | Ghép sai context, lộ retrieval data, bỏ qua authorization |
| Infrastructure | Secret trong log, network boundary sai, dependency vulnerable |
| Governance | Scope, policy, owner hoặc risk acceptance chưa rõ |

Taxonomy giúp team sửa đúng lớp và tránh lặp lại lỗi khi đổi model.

Bài [Testing AI Agent: Quy trình QA và quản trị rủi ro](https://t5edu.site/blogs/testing-ai-agent-quy-trinh-qa-va-quan-tri-rui-ro) hữu ích khi hệ thống có planner, memory hoặc nhiều tool. Bài này không thay thế nội dung đó, mà bổ sung góc adversarial testing cho việc chứng minh hệ thống có thể bị ép qua trust boundary nào.

## Nên tổ chức regression suite cho red teaming ra sao?

### Regression suite nên có gì?

| Nhóm | Field hoặc kiểm tra |
| --- | --- |
| Reproducibility | Seed, canonicalized input, test version |
| Risk context | Category, expected policy, risk owner |
| Coverage | Control set và high-risk adversarial set |
| Judgment | Deterministic assertion, rubric, reviewer và threshold |
| Remediation | Finding, fix, verification và residual risk |

Khi model hoặc prompt template đổi:

1. Chạy lại control set.
2. Chạy high-risk adversarial set.
3. Kiểm tra biến thể gần nghĩa.
4. Xác nhận control case không bị phá.

Không dùng một LLM tự chấm duy nhất cho risk cao nếu chưa có sample review và threshold. [Bài Test AI Agent hôm nay pass, mai fail](https://t5edu.site/blogs/test-ai-agent-hom-nay-pass-mai-fail) giải thích vì sao output không deterministic cần regression strategy.

![Wide 21:9 educational diagram showing a red-team regression loop. Layout: four cards in a circular flow labeled 'Seed + Case', 'Run', 'Evidence', and 'Fix + Verify', with a central small card labeled 'Risk owner'. Solid T5Edu Blue arrows flow clockwise, and dashed Amber arrows branch from 'Evidence' to 'Risk owner' and back to 'Fix + Verify'. Exact Vietnamese labels: 'Seed + Case', 'Run', 'Evidence', 'Fix + Verify', 'Risk owner', 'Control case'. Minimalist flat vector UI design. Premium professional EdTech editorial artwork. Clean bento-grid composition with strong negative space. Paper White background #fafafa, Zinc-900 content #18181b, T5Edu Blue accent #1a73e8, Amber highlight #f59e0b, Subtle one-pixel borders and restrained liquid-glass layers. Simple flat icons and geometric shapes, no people, no faces, no hands, no 3D, no glossy plastic, no photorealism, no dramatic lighting, no purple, no violet, no pink, no neon, no logo, no watermark.](https://files.manuscdn.com/user_upload_by_module/session_file/310519663091035343/JiUQFAXzljAyFcyp.png)

## Khi nào một finding đủ điều kiện để escalate?

### Escalation checklist

Một finding đủ điều kiện escalate khi có:

- Target và version rõ.
- Payload hoặc điều kiện chạy lại được.
- Expected policy đã khóa.
- Actual behavior vi phạm policy.
- Evidence đủ để người khác kiểm tra.
- Impact và side effect đã mô tả.
- Audit record nếu có tool call hoặc thay đổi dữ liệu.

### Finding template

| Field | Nội dung |
| --- | --- |
| Scope | Target, version, precondition |
| Behavior | Steps, expected, actual |
| Risk | Impact, suspected layer, residual risk |
| Evidence | Output, trace, authorization, audit record |
| Next step | Mitigation hypothesis và owner |

Nếu có secret, user data hoặc khả năng thực thi hành động, dùng security incident channel thay vì issue công khai. Red teaming phải nằm trong phạm vi được ủy quyền.

## Tổng kết

- AI red teaming cần test toàn bộ LLM application, không chỉ câu trả lời cuối của model.
- OWASP AI Testing Guide v1 cung cấp cách nhìn trustworthiness theo application, model, infrastructure và data layer.
- Một charter tốt khóa target, trust boundary, risk hypothesis, allowed action và exit criteria trước khi chạy.
- Evidence cần giữ trajectory, tool call, authorization và version để phân biệt lỗi model với lỗi application hoặc governance.
- Regression suite phải có control case, seed, rubric và bước verification sau remediation.

Nếu bạn đã có nền tảng API testing và muốn thiết kế pipeline quan sát cho hệ thống AI, hãy xem [bài xây pipeline kiểm thử AI với OpenTelemetry](https://t5edu.site/blogs/xay-pipeline-kiem-thu-ai-voi-opentelemetry) và [khóa API Testing nâng cao](https://t5edu.site/courses/api-testing-nang-cao). Câu hỏi mở: trong LLM app bạn đang kiểm thử, trust boundary nào chưa có evidence để chứng minh khi bị tấn công?

## Hashtag
> ai red teaming, ai testing, llm security, prompt injection, qa testing
