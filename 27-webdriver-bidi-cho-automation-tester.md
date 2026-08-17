# WebDriver BiDi: Deep Dive Cho Automation Tester

> WebDriver BiDi mở rộng browser automation từ mô hình request-response sang giao tiếp hai chiều, event-driven. Bài viết này giúp automation tester có nền tảng WebDriver thiết kế flow theo dõi navigation và network, đồng thời biết giới hạn của protocol đang tiếp tục hoàn thiện.

![Minimalist flat vector UI design, premium professional EdTech editorial artwork for a WebDriver BiDi deep dive, 1:1 square cover, clean bento-grid composition with strong negative space, Paper White background #fafafa, Zinc-900 content #18181b, T5Edu Blue accent #1a73e8, Amber highlight #f59e0b, a browser window connected by two opposing arrows to an automation client, three small cards labeled "Command", "Event", and "Network", Vietnamese labels only, geometric flat icons, premium technical EdTech editorial style, subtle one-pixel borders and restrained liquid-glass layers, no gradients, no photorealism, no brand logos, no dense text, no watermark](https://files.manuscdn.com/user_upload_by_module/session_file/310519663091035343/sGrykcTIUUAsVVst.png)

## WebDriver BiDi giải quyết giới hạn nào của automation truyền thống?

Bài này dành cho automation tester hoặc QA engineer đã biết WebDriver session, locator, assertion, HTTP cơ bản và JavaScript hoặc TypeScript async/await. Reader outcome là sau khi đọc, tester có thể phân tích một use case cần lắng nghe browser event, chọn module BiDi phù hợp, bật khả năng BiDi trong Selenium và xây một test strategy không nhầm protocol capability với wrapper API.

WebDriver classic thường được hiểu qua chuỗi client gửi request rồi chờ browser trả response. Mô hình này phù hợp với thao tác như navigate, find element và click. Nhưng các tình huống như “hãy báo ngay khi navigation fail”, “ghi lại console error trong lúc test”, hoặc “chặn request trước khi server nhận” cần một kênh để browser chủ động phát tín hiệu về client.

[Đặc tả WebDriver BiDi của W3C](https://www.w3.org/TR/webdriver-bidi/) định nghĩa một protocol bidirectional để remote control user agents. [MDN](https://developer.mozilla.org/en-US/docs/Web/WebDriver/Reference/BiDi) diễn giải BiDi là giao tiếp event-driven giữa automation client và browser, khác mô hình HTTP request-response của WebDriver classic và hỗ trợ WebSocket-based communication.

![Minimalist flat vector UI design, premium professional EdTech editorial artwork, 21:9 wide section image comparing WebDriver classic and BiDi, clean horizontal bento-grid composition with strong negative space, Paper White background #fafafa, Zinc-900 content #18181b, T5Edu Blue accent #1a73e8, Amber highlight #f59e0b, left lane labeled "Classic" with one-way solid arrow Client to Browser and cards "Request" and "Response", right lane labeled "BiDi" with two-way arrows and cards "Command" and "Event", short Vietnamese labels only, flat vector technical illustration, one-pixel borders, no gradients, no photorealism, no logos, no dense paragraphs, no watermark](https://files.manuscdn.com/user_upload_by_module/session_file/310519663091035343/BoLqHqfQigmdUabl.png)

## Protocol BiDi gồm những lớp nào cần hiểu trước khi code?

Không nên bắt đầu bằng việc copy một snippet intercept request. Trước tiên, tester cần tách bốn khái niệm: session, module, command và event.

| Khái niệm | Ý nghĩa trong BiDi | Câu hỏi khi thiết kế test |
|---|---|---|
| Session | Phiên kết nối giữa automation client và browser | Khi nào tạo, subscribe và kết thúc session? |
| Module | Nhóm khả năng theo domain như browsingContext, log, network hoặc script | Use case thuộc domain nào? |
| Command | Yêu cầu client gửi để inspect hoặc control browser | Command trả về response nào và có thể fail ra sao? |
| Event | Notification browser chủ động gửi khi có sự kiện | Event nào cần subscribe, correlation bằng ID nào? |
| Transport | Cách truyền message giữa hai đầu | Wrapper đang che giấu WebSocket và lifecycle ở mức nào? |

Đặc tả hiện chia protocol thành các phần infrastructure, protocol definition, session, modules, commands, errors, events và transport. Cách chia này quan trọng vì một test có thể vừa gửi command navigate vừa subscribe event load hoặc network. Nếu chỉ nhìn API cấp cao, tester dễ không nhận ra rằng failure có thể nằm ở lifecycle subscription, browser support hoặc mapping của library.

Một nguyên tắc thực dụng là viết test intent trước rồi mới chọn API. Ví dụ intent “khi submit login, không gửi request đến analytics nếu user từ chối consent” cần network event hoặc request handler. Intent “page đã hiển thị message sau khi navigation hoàn tất” có thể dùng browsingContext event kết hợp assertion trên DOM. Hai intent này khác nhau dù đều xuất hiện trong cùng một browser flow.

<grid-content>
Cách chọn module theo test intent
> Bắt đầu từ tín hiệu cần quan sát, không bắt đầu từ tên API.
```markdown
Navigation hoặc lifecycle page: browsingContext
Console error và log: log
Request, response, auth hoặc status: network
Thao tác và file dialog: input
Script, sandbox và DOM state: script
```
```markdown
Nếu test cần browser gửi tín hiệu chủ động:
1. Xác định event.
2. Xác định subscription scope.
3. Lưu context hoặc request id.
4. Chạy action gây event.
5. Assert event và cleanup subscription.
```
</grid-content>

## Bật BiDi trong Selenium và kiểm tra capability thế nào?

Selenium docs yêu cầu tester bật BiDi trong Options trước khi dùng các tính năng tương ứng. Chi tiết setup phụ thuộc ngôn ngữ và browser, vì vậy không nên coi một snippet JavaScript là contract chung cho Java, Python, C# hoặc Ruby. Hãy bắt đầu bằng trang [Selenium WebDriver BiDi](https://www.selenium.dev/documentation/webdriver/bidi/) và kiểm tra phần enabling BiDi của binding đang dùng.

Với TypeScript, tester cần kiểm tra ba điểm trước khi viết assertion. Thứ nhất, phiên bản Selenium đang dùng có expose wrapper cho module cần thiết hay chưa. Thứ hai, browser và driver có capability tương ứng hay không. Thứ ba, test runner có xử lý lifecycle bất đồng bộ và cleanup event listener sau mỗi test hay không.

Không nên kết luận “BiDi không hoạt động” chỉ vì wrapper thiếu method. W3C protocol, browser implementation, driver và Selenium binding là bốn lớp khác nhau. Một capability có thể tồn tại trong đặc tả nhưng chưa được binding expose, hoặc đã expose nhưng implementation đang được theo dõi qua issue. [Tài liệu network của Selenium](https://www.selenium.dev/documentation/webdriver/bidi/network/) hiện liên kết việc triển khai với issue #13993, vì vậy tester cần kiểm tra release và browser matrix thay vì ghi hard-code một claim hỗ trợ tuyệt đối.

<multiple-choice correct="C" select="single">
Một command có trong W3C WebDriver BiDi nhưng binding Selenium của team chưa có method tương ứng. Kết luận kỹ thuật nào hợp lý nhất?
- A: Browser chắc chắn không hỗ trợ BiDi
- B: Protocol đã ổn định nên team phải tự gọi mọi message ngay
- C: Kiểm tra version, binding, browser support và implementation status trước
- D: Xoá test vì BiDi chỉ dành cho developer
</multiple-choice>

## Network interception trong BiDi dùng cho use case nào?

Network namespace có giá trị khi tester cần quan sát hoặc điều chỉnh traffic trong một browser flow. Selenium mô tả authentication handlers để xử lý authentication request như Basic Auth hoặc Digest Auth. Request handlers có thể intercept và thay đổi outgoing request trước khi gửi, còn response handlers làm việc với response để kiểm tra hoặc điều chỉnh header, status code và content.

Một use case phù hợp là kiểm tra UI phản ứng ra sao khi API profile trả 401, 429 hoặc response chậm. Tester không cần dựng một backend riêng cho từng biến thể, nhưng vẫn phải phân biệt giữa mô phỏng lỗi có chủ đích và hành vi thật của môi trường. Nếu handler sửa response, evidence phải ghi rõ đó là synthetic response để người đọc không nhầm với lỗi production.

Use case thứ hai là xác minh request contract. Khi user submit form, tester có thể quan sát method, URL, header cần thiết và body trước khi request rời browser. Đây là lớp kiểm tra bổ sung cho UI assertion, không thay thế [API Testing cơ bản](/courses/api-testing-co-ban) hoặc kiểm tra service độc lập. Nếu mục tiêu là kiểm tra backend rule, tester nên giữ boundary rõ ràng thay vì nhồi tất cả vào browser test.

<table-testcase cols="5" rows="5" headers="ID|Intent|Tín hiệu BiDi|Action|Expected result">
| BIDI-01 | API trả 401 | response event của profile request | Mở trang profile khi session hết hạn | UI hiển thị trạng thái cần login lại, không báo loaded data giả |
| BIDI-02 | Chặn analytics | request event | Từ chối consent rồi mở product page | Không gửi request analytics bị cấm |
| BIDI-03 | Timeout mô phỏng | response hoặc failure event | Gây delay ở request search | UI hiển thị loading và lỗi theo rule, không treo vô hạn |
| BIDI-04 | Auth prompt | authentication request | Mở endpoint Basic Auth | Handler cung cấp credential đúng hoặc flow fail rõ ràng |
</table-testcase>

![Minimalist flat vector UI design, premium professional EdTech editorial artwork, 21:9 wide section image for WebDriver BiDi network interception, clean horizontal bento-grid composition with strong negative space, Paper White background #fafafa, Zinc-900 content #18181b, T5Edu Blue accent #1a73e8, Amber highlight #f59e0b, left browser card labeled "Browser" sending request cards, center interception layer labeled "Network", right API card returning "401" and "429", solid arrows for command and dotted arrows for event, short Vietnamese labels only, flat technical vector style, one-pixel borders, no gradients, no photorealism, no logos, no dense text, no watermark](https://files.manuscdn.com/user_upload_by_module/session_file/310519663091035343/xygLJYQOvSKbLLdF.png)

## Subscribe event và cleanup thế nào để test không flaky?

Event-driven test có rủi ro race condition. Nếu subscribe sau khi action đã xảy ra, tester có thể bỏ lỡ event và nhận false failure. Nếu listener sống quá lâu, event của test trước có thể chảy sang test sau. Vì vậy, hãy đặt subscription trước action, lọc đúng context hoặc request, chờ event với timeout hợp lý và luôn cleanup trong teardown.

Một flow có thể viết theo thứ tự: tạo session, subscribe event cần thiết, tạo promise chờ event với predicate cụ thể, thực hiện navigate hoặc submit, await event, kiểm tra payload, huỷ subscription và đóng session. Predicate không nên chỉ là “có một response”. Nó nên kiểm tra context id, URL pattern, method hoặc request id để tránh bắt nhầm traffic của resource khác.

Timeout cũng cần có lý do. Timeout quá ngắn tạo false failure khi CI chậm, còn timeout quá dài che giấu lỗi thực sự và làm suite mất tín hiệu. Nếu test nhiều browser, hãy ghi nhận sự khác nhau về event order và capability thay vì ép mọi implementation có cùng timing tuyệt đối.

<code-runner language="typescript" title="Mẫu tư duy event-driven">
```typescript
const eventPromise = waitForEvent({
  contextId,
  predicate: event => event.url.includes('/profile') && event.status === 401,
  timeoutMs: 5000,
});

await page.click('[data-testid="open-profile"]');
const event = await eventPromise;
expect(event.status).toBe(401);
await unsubscribe();
```
</code-runner>

Đoạn trên là pseudo-code mô tả thứ tự và predicate, không phải API copy-paste cho mọi Selenium binding. Trong code thật, tester phải dùng đúng interface của binding và quản lý cleanup theo test runner. Cách trình bày này cố ý tách design pattern khỏi chi tiết wrapper, vì wrapper có thể thay đổi nhanh hơn protocol.

## BiDi khác CDP ra sao khi chọn chiến lược automation?

CDP là protocol gắn chặt với Chrome DevTools ecosystem, trong khi WebDriver BiDi hướng tới một chuẩn WebDriver hai chiều có khả năng dùng qua nhiều browser implementation. Điều này không có nghĩa BiDi ngay lập tức thay thế mọi khả năng CDP. Tester cần so sánh use case, browser matrix, độ ổn định của binding và yêu cầu portability.

Selenium đặt BiDi cạnh phần CDP trong tài liệu để người dùng hiểu lộ trình chuyển sang lựa chọn standards-based. Với team chỉ chạy Chrome và cần một capability DevTools đặc thù, CDP có thể vẫn phù hợp. Với team cần giảm phụ thuộc vendor và theo dõi event theo mô hình WebDriver, BiDi đáng được đánh giá bằng một spike nhỏ có acceptance criteria rõ ràng.

<dropdown-content>
### Khi nào nên thử BiDi?

Nên thử khi test cần event browser, network interception, log hoặc capability mà request-response không diễn đạt tốt. Hãy chọn một use case nhỏ, chạy trên browser matrix của team và đo độ ổn định trước khi mở rộng.

### Có nên rewrite toàn bộ suite Selenium sang BiDi không?

Không nên rewrite chỉ vì protocol mới. Giữ WebDriver classic cho thao tác ổn định, sau đó bổ sung BiDi ở boundary thật sự cần event hoặc network. Mỗi capability mới cần test compatibility, cleanup và failure evidence riêng.

### Bài này có phù hợp cho tester mới học automation không?

Không. Tester mới nên nắm locator, wait, assertion, HTTP và lifecycle test trước. Có thể củng cố nền bằng [Playwright với TypeScript cơ bản](/blogs/playwright-voi-typescript-co-ban-cho-tester), sau đó quay lại BiDi khi đã hiểu bất đồng bộ và browser session.
</dropdown-content>

## Đánh giá BiDi trong CI mà không biến test thành hộp đen

Một proof of concept có giá trị nên ghi lại browser, driver, Selenium binding, capability đã bật, event đã subscribe và kết quả theo từng run. Đừng chỉ báo “test pass” vì phần quan trọng của BiDi là evidence ở giữa flow. Khi có failure, cần biết command nào đã gửi, event nào không đến, payload có gì và cleanup có chạy hay không.

Hãy thêm các assertion chống false positive. Nếu request bị chặn, xác nhận request thực sự không rời browser hoặc response không được dùng như dữ liệu thật. Nếu nhận 401, kiểm tra UI state và API state có nhất quán. Nếu theo dõi log, phân biệt error do ứng dụng với error do browser hoặc test harness.

Với suite lớn, event log có thể làm report khó đọc. Chỉ lưu payload cần cho triage, che thông tin nhạy cảm và dùng correlation id để nối action với event. Kỹ thuật này gần với tư duy [test observability cho người mới](/blogs/test-observability-cho-nguoi-moi), nhưng bài này tập trung vào protocol và event boundary của browser automation, không phải thiết kế observability cho toàn hệ thống.

![Minimalist flat vector UI design, premium professional EdTech editorial artwork, 21:9 wide section image for a WebDriver BiDi CI evaluation workflow, clean horizontal bento-grid composition with strong negative space, Paper White background #fafafa, Zinc-900 content #18181b, T5Edu Blue accent #1a73e8, Amber highlight #f59e0b, left card "Test Runner", center event timeline cards "Command", "Event", "Assertion", right card "CI Evidence" with browser matrix icons, arrows showing lifecycle and cleanup, short Vietnamese labels only, flat professional technical vector style, one-pixel borders and restrained liquid-glass layers, no gradients, no photorealism, no brand logos, no dense paragraphs, no watermark](https://files.manuscdn.com/user_upload_by_module/session_file/310519663091035343/qCAikAJICpAcWzNh.png)

## Tổng kết

- WebDriver BiDi là protocol hai chiều, event-driven, nhưng tài liệu W3C hiện vẫn là Working Draft và implementation cần được kiểm tra theo browser, driver và binding.
- Thiết kế test nên bắt đầu từ intent, sau đó chọn module, event, subscription scope và cleanup strategy.
- Network handlers phù hợp cho kiểm tra authentication, request contract và phản ứng UI trước response lỗi, nhưng không thay thế API test ở boundary service.
- Nếu team đang xử lý flaky test hoặc cần quan sát evidence trong automation, hãy đọc [cách khắc phục flaky test](/blogs/cach-khac-phuc-flaky-test-cho-sdet) và bài [xây pipeline kiểm thử AI với OpenTelemetry](/blogs/xay-pipeline-kiem-thu-ai-voi-opentelemetry) để phân biệt protocol event với test observability.

Nếu phải chọn một use case để chạy spike WebDriver BiDi tuần này, team sẽ ưu tiên bắt navigation event, kiểm tra network response hay thu console log, và acceptance criteria cụ thể là gì?

## Hashtag

> webdriver bidi, selenium, automation testing, browser automation, network testing
