# OWASP AI red teaming cho ứng dụng LLM: Thiết kế test có hệ thống

> OWASP AI Testing Guide v1, phát hành ngày 26 tháng 11 năm 2025, đưa trustworthiness testing thành một phạm vi có phương pháp thay vì chỉ thử vài prompt nguy hiểm. Bài viết giúp QA, security tester và AI engineer thiết kế một vòng red teaming có scope, attack hypothesis, evidence và tiêu chí dừng rõ ràng.

![Square 1:1 cover illustration for an article titled 'OWASP AI red teaming cho ứng dụng LLM'. The visual must communicate adversarial testing of an LLM application across model, application, data, and infrastructure layers. Composition: a central LLM gateway card labeled 'LLM App', surrounded by four layered cards labeled 'Model', 'Application', 'Data', and 'Infrastructure', with blue test arrows entering from the left and amber threat markers near the gateway. Exact Vietnamese labels: 'LLM App', 'Model', 'Application', 'Data', 'Infrastructure', 'Red team'. Minimalist flat vector UI design. Premium professional EdTech editorial artwork. Clean bento-grid composition with strong negative space. Paper White background #fafafa, Zinc-900 content #18181b, T5Edu Blue accent #1a73e8, Amber highlight #f59e0b, Subtle one-pixel borders and restrained liquid-glass layers. Simple flat icons and geometric shapes, no people, no faces, no hands, no 3D, no glossy plastic, no photorealism, no dramatic lighting, no purple, no violet, no pink, no neon, no logo, no watermark.](https://files.manuscdn.com/user_upload_by_module/session_file/310519663091035343/mFiQJNUeUBCmWHrC.png)

## AI red teaming khác functional testing ở điểm nào?

Functional testing hỏi hệ thống có thực hiện đúng behavior đã định trong điều kiện bình thường hay không. AI red teaming đặt hệ thống vào các điều kiện adversarial, tìm cách làm nó vi phạm policy, tiết lộ dữ liệu, gọi tool sai hoặc tạo ra hành vi nguy hiểm. Đối tượng không chỉ là model, mà là toàn bộ sociotechnical system gồm prompt, retrieval, tool, identity, runtime, người dùng và các policy bao quanh.

[OWASP AI Testing Guide v1](https://owasp.org/www-project-ai-testing-guide/) mô tả một methodology technology-agnostic cho trustworthiness testing, với phạm vi trải trên AI application layer, model layer, infrastructure layer và data layer. Đây là khác biệt quan trọng với việc chỉ chạy một bộ prompt rồi đếm pass rate.

Bài này dành cho QA automation tester, security tester và AI engineer đã biết API testing, authentication, log hoặc trace cơ bản, JSON, test data isolation và cách đọc một LLM application flow. Người đọc không cần huấn luyện model, nhưng cần hiểu request đi qua gateway, prompt template, retrieval và tool execution như thế nào. Sau bài, reader có thể thiết kế một red-team charter nhỏ, tạo test matrix và chuyển phát hiện thành evidence có thể triage.

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

Một session không có charter thường biến thành cuộc thi tìm prompt lạ. Trước khi chạy, hãy khóa năm trường: target, trust boundary, risk hypothesis, allowed action và exit criteria. Ví dụ target là chatbot hỗ trợ hoàn tiền; trust boundary gồm user prompt, knowledge base, payment API và tool cấp quyền refund; risk hypothesis là prompt injection trong tài liệu có thể khiến assistant gọi refund cho account khác.

Charter cũng cần nêu rõ những gì không được làm. Không dùng dữ liệu production thật, không gửi payload gây denial of service, không truy cập account không thuộc test scope và không tự ý thay đổi policy. Nếu test một tool có side effect, dùng sandbox endpoint hoặc mock có audit log.

NIST [AI RMF Generative AI Profile](https://www.nist.gov/publications/artificial-intelligence-risk-management-framework-generative-artificial-intelligence) xem evaluation và risk management là hoạt động xuyên suốt vòng đời AI. Vì vậy, red teaming không nên chỉ diễn ra trước launch. Test suite cần có baseline, owner, lịch chạy lại và cơ chế ghi nhận model hoặc prompt thay đổi.

| Charter field | Câu hỏi cần khóa | Ví dụ |
| --- | --- | --- |
| Target | Test hệ thống nào và version nào? | Support bot build 2026.08 |
| Trust boundary | Dữ liệu và tool nào nằm ngoài vùng tin cậy? | KB upload, refund API |
| Risk hypothesis | Hành vi xấu nào cần chứng minh? | Instruction trong KB vượt system policy |
| Allowed action | Payload và side effect nào được phép? | Chỉ sandbox refund |
| Exit criteria | Khi nào dừng hoặc escalate? | Có tool call trái quyền và trace đầy đủ |

![Wide 21:9 educational diagram explaining an AI red-team charter. Layout: five horizontal cards labeled 'Target', 'Trust boundary', 'Risk', 'Allowed action', and 'Exit criteria'. A solid blue arrow connects the cards left to right, while a dashed amber boundary surrounds 'Allowed action' and 'Exit criteria'. A small document at the bottom is labeled 'Evidence'. Exact Vietnamese labels: 'Target', 'Trust boundary', 'Risk', 'Allowed action', 'Exit criteria', 'Evidence'. Minimalist flat vector UI design. Premium professional EdTech editorial artwork. Clean bento-grid composition with strong negative space. Paper White background #fafafa, Zinc-900 content #18181b, T5Edu Blue accent #1a73e8, Amber highlight #f59e0b, Subtle one-pixel borders and restrained liquid-glass layers. Simple flat icons and geometric shapes, no people, no faces, no hands, no 3D, no glossy plastic, no photorealism, no dramatic lighting, no purple, no violet, no pink, no neon, no logo, no watermark.](https://files.manuscdn.com/user_upload_by_module/session_file/310519663091035343/xohYoauqwoMljSqe.png)

## Làm thế nào chuyển risk thành attack hypothesis có thể chạy?

Đừng viết test case dạng “thử prompt injection”. Hãy viết hypothesis có điều kiện, hành động và bằng chứng mong đợi. Mẫu hữu ích là: “Nếu attacker đưa instruction không tin cậy qua kênh X, hệ thống có thể thực hiện hành động Y trái policy Z hay không?” Với chatbot có RAG, X có thể là tài liệu được index; Y là trích xuất system prompt hoặc gọi tool; Z là không tiết lộ secret và không thao tác tenant khác.

Một hypothesis tốt có control case và adversarial case. Control case kiểm tra behavior bình thường để tránh coi mọi refusal là pass. Adversarial case thay đổi một biến như vị trí instruction, encoding, ngôn ngữ hoặc quyền user. Nếu thay quá nhiều biến cùng lúc, tester không biết nguyên nhân nào tạo ra failure.

[OWASP GenAI Red Teaming Guide](https://genai.owasp.org/resource/genai-red-teaming-guide/) tổ chức red teaming quanh model evaluation, implementation testing, infrastructure assessment và runtime behavior analysis. Bốn vùng này có thể biến thành bốn suite riêng, mỗi suite có owner và evidence contract khác nhau.

<multiple-choice correct="B" select="single">
Một attack hypothesis tốt nên có cấu trúc nào?
- A: Một prompt thật dài và không cần expected behavior
- B: Kênh tấn công, hành động có thể xảy ra, policy bị đe dọa và bằng chứng cần thu
- C: Tên model, temperature và một điểm pass rate duy nhất
- D: Một danh sách tool mà không cần trust boundary
</multiple-choice>

## Cần đo gì ngoài pass hoặc fail của câu trả lời?

Một output an toàn chưa đủ để kết luận pass. Với LLM app, tester cần quan sát cả trajectory: input được normalize thế nào, prompt sau khi ghép có gì, retrieval trả về document nào, tool có được gọi không, authorization check ở đâu, output filter có sửa câu trả lời không và hệ thống dừng ở trạng thái nào.

Các assertion nên chia thành nhiều lớp. Lớp safety kiểm tra secret, harmful content và policy violation. Lớp behavior kiểm tra assistant có giữ đúng task và không bị instruction trong data điều khiển. Lớp authorization kiểm tra user A không đọc hoặc thay đổi data của user B. Lớp operational kiểm tra latency, token usage, retry và log redaction khi payload bất thường.

Không nên gộp tất cả thành một score. Một test có thể trả lời đúng nhưng vẫn fail vì đã gọi tool trái quyền. Ngược lại, một refusal có thể là false positive nếu policy cho phép tác vụ. Report phải giữ output, tool trace, retrieved context, identity, version và expected decision trong cùng một evidence bundle.

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

Khi test fail, đừng gán ngay cho model. Cùng một output nguy hiểm có thể đến từ model không tuân instruction, application ghép untrusted text vào system message, tool gateway không kiểm tra quyền hoặc governance không định nghĩa policy rõ. Root-cause taxonomy giúp team sửa đúng lớp và tránh lặp lại lỗi khi đổi model.

Model failure thường thể hiện ở việc model tạo output sai dù prompt và policy context đúng. Application failure xảy ra khi code truyền sai context, lộ retrieval data, bỏ qua authorization hoặc xử lý output không an toàn. Infrastructure failure có thể là secret trong log, network boundary sai hoặc dependency vulnerable. Governance failure nằm ở scope, policy, owner, risk acceptance và cách xử lý incident.

Bài [Testing AI Agent: Quy trình QA và quản trị rủi ro](https://t5edu.site/blogs/testing-ai-agent-quy-trinh-qa-va-quan-tri-rui-ro) hữu ích khi hệ thống có planner, memory hoặc nhiều tool. Bài này không thay thế nội dung đó, mà bổ sung góc adversarial testing cho việc chứng minh hệ thống có thể bị ép qua trust boundary nào.

## Nên tổ chức regression suite cho red teaming ra sao?

Đầu tiên, lưu seed, category, expected policy, test version và risk owner. Khi model hoặc prompt template đổi, chạy lại control set và high-risk adversarial set trước. Nếu payload dùng randomization, lưu seed và canonicalized input để failure có thể chạy lại.

Tiếp theo, phân tách deterministic assertion và rubric judgment. “Có gọi refund tool hay không” là assertion khá rõ. “Câu trả lời có gây hiểu nhầm không” cần rubric, nhiều reviewer hoặc evaluator được kiểm chuẩn. Không dùng một LLM tự chấm duy nhất cho rủi ro cao mà không có sample review và threshold được định nghĩa trước.

Cuối cùng, liên kết finding với remediation và verification. Một bản vá chỉ được coi là có tác dụng khi test cũ pass, biến thể gần nghĩa vẫn pass và control case không bị phá. [Bài Test AI Agent hôm nay pass, mai fail](https://t5edu.site/blogs/test-ai-agent-hom-nay-pass-mai-fail) giải thích vì sao output không deterministic cần regression strategy thay vì một lần chạy thành công.

![Wide 21:9 educational diagram showing a red-team regression loop. Layout: four cards in a circular flow labeled 'Seed + Case', 'Run', 'Evidence', and 'Fix + Verify', with a central small card labeled 'Risk owner'. Solid T5Edu Blue arrows flow clockwise, and dashed Amber arrows branch from 'Evidence' to 'Risk owner' and back to 'Fix + Verify'. Exact Vietnamese labels: 'Seed + Case', 'Run', 'Evidence', 'Fix + Verify', 'Risk owner', 'Control case'. Minimalist flat vector UI design. Premium professional EdTech editorial artwork. Clean bento-grid composition with strong negative space. Paper White background #fafafa, Zinc-900 content #18181b, T5Edu Blue accent #1a73e8, Amber highlight #f59e0b, Subtle one-pixel borders and restrained liquid-glass layers. Simple flat icons and geometric shapes, no people, no faces, no hands, no 3D, no glossy plastic, no photorealism, no dramatic lighting, no purple, no violet, no pink, no neon, no logo, no watermark.](https://files.manuscdn.com/user_upload_by_module/session_file/310519663091035343/JiUQFAXzljAyFcyp.png)

## Khi nào một finding đủ điều kiện để escalate?

Một finding nên được escalate khi tester có target và version rõ, payload hoặc điều kiện chạy lại được, expected policy đã khóa, actual behavior vi phạm policy, evidence đủ để người khác kiểm tra và impact đã được mô tả. Với side effect, cần có audit record và xác nhận phạm vi dữ liệu đã dùng.

Không nên gửi một chuỗi prompt rời rạc kèm kết luận “model không an toàn”. Hãy viết finding theo cấu trúc: summary, scope, precondition, steps, expected, actual, impact, evidence, suspected layer, mitigation hypothesis và residual risk. Mitigation hypothesis là đề xuất để điều tra, không phải kết luận root cause.

Nếu finding liên quan đến secret, user data hoặc khả năng thực thi hành động, dùng kênh security incident của tổ chức thay vì issue công khai. Red teaming chỉ có giá trị khi hoạt động trong phạm vi được ủy quyền và có khả năng bảo vệ dữ liệu kiểm thử.

## Tổng kết

- AI red teaming cần test toàn bộ LLM application, không chỉ câu trả lời cuối của model.
- OWASP AI Testing Guide v1 cung cấp cách nhìn trustworthiness theo application, model, infrastructure và data layer.
- Một charter tốt khóa target, trust boundary, risk hypothesis, allowed action và exit criteria trước khi chạy.
- Evidence cần giữ trajectory, tool call, authorization và version để phân biệt lỗi model với lỗi application hoặc governance.
- Regression suite phải có control case, seed, rubric và bước verification sau remediation.

Nếu bạn đã có nền tảng API testing và muốn thiết kế pipeline quan sát cho hệ thống AI, hãy xem [bài xây pipeline kiểm thử AI với OpenTelemetry](https://t5edu.site/blogs/xay-pipeline-kiem-thu-ai-voi-opentelemetry) và [khóa API Testing nâng cao](https://t5edu.site/courses/api-testing-nang-cao). Câu hỏi mở: trong LLM app bạn đang kiểm thử, trust boundary nào chưa có evidence để chứng minh khi bị tấn công?

## Hashtag

> ai red teaming, AI testing, LLM security, prompt injection, QA testing
