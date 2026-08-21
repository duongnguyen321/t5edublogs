# Kiểm Thử Passkey Với WebAuthn

> Kiểm thử passkey với WebAuthn cần mô phỏng đúng credential state, RP ID và authentication ceremony. Bài này hướng dẫn QA engineer dùng virtual authenticator của Playwright để test flow đăng ký, đăng nhập, từ chối và tái sử dụng passkey.

![Minimalist flat vector UI design, premium professional EdTech editorial artwork for an advanced passkey and WebAuthn testing guide, 1:1 square cover, clean bento-grid composition with strong negative space, Paper White background #fafafa, Zinc-900 content #18181b, T5Edu Blue accent #1a73e8, Amber highlight #f59e0b, browser card connected to a virtual authenticator card and a test runner card, labels "WebAuthn", "Passkey", "Assertion", short Vietnamese labels only, geometric flat icons, subtle one-pixel borders, restrained liquid-glass layers, no people, no faces, no hands, no 3D, no glossy plastic, no photorealism, no dramatic lighting, no purple, no violet, no pink, no neon, no logo, no watermark](https://files.manuscdn.com/user_upload_by_module/session_file/310519663091035343/muBZaojPntkqIDBj.png)

## Vì sao passkey làm browser test khó hơn login thường?

Passkey là credential dựa trên WebAuthn, trong đó browser và authenticator phối hợp với relying party để tạo hoặc xác minh public-key credential. Test không chỉ assert text trên UI. Tester phải kiểm tra credential state, origin, RP ID, user verification, challenge và kết quả assertion.

Bài này dành cho QA engineer, automation engineer và developer đã biết JavaScript hoặc TypeScript, npm, Playwright, BrowserContext, async/await và các khái niệm trace hoặc test isolation cơ bản. Đây không phải bài nhập môn WebAuthn, cũng không phải hướng dẫn thay thế coverage trên thiết bị thật.

[WebAuthn Level 3 của W3C](https://www.w3.org/TR/webauthn-3/) mô tả public-key credential được scope theo relying party và được user agent cùng authenticator trung gian bảo vệ. Vì vậy, test passkey cần kiểm tra boundary của relying party, không chỉ kiểm tra việc click nút “Đăng nhập bằng passkey”.

![Minimalist flat vector UI design, premium professional EdTech editorial artwork for WebAuthn ceremony testing, 21:9 wide section image, clean horizontal bento-grid composition with strong negative space, Paper White background #fafafa, Zinc-900 content #18181b, left lane labeled "Register" with browser and authenticator cards, center lane labeled "Credential" with a key icon, right lane labeled "Assert" with verified and rejected result cards, solid arrows for request and dotted arrows for user verification, short Vietnamese labels only, flat vector technical illustration, Zinc-900 content #18181b, T5Edu Blue accent #1a73e8, Amber highlight #f59e0b, subtle one-pixel borders, restrained liquid-glass layers, no people, no faces, no hands, no 3D, no glossy plastic, no photorealism, no dramatic lighting, no purple, no violet, no pink, no neon, no logo, no watermark](https://files.manuscdn.com/user_upload_by_module/session_file/310519663091035343/WIcTpcnqVjvdKvEh.png)

## WebAuthn registration và assertion gồm những boundary nào?

Registration tạo credential mới. Application gửi creation options, browser gọi `navigator.credentials.create()`, authenticator tạo key pair và application lưu public credential data ở server. Private key không được gửi lên server trong flow bình thường.

Authentication còn được gọi là assertion. Server gửi challenge và request options, browser gọi `navigator.credentials.get()`, authenticator ký dữ liệu phù hợp, rồi server verify signature, challenge, RP ID và các thuộc tính cần thiết trước khi tạo session.

| Boundary | Điều cần test | Failure thường gặp |
|---|---|---|
| Origin và RP ID | Credential chỉ dùng đúng relying party scope | Test chạy trên host khác với host app đăng ký |
| Registration | User có thể tạo và lưu passkey hợp lệ | UI báo thành công nhưng server không lưu credential |
| Assertion | Passkey đúng tạo session mới | Credential bị seed sai hoặc không được install trước page call |
| User verification | Flow accept hoặc reject theo trạng thái | Test luôn auto-approve nên không bao phủ từ chối |
| Credential lifecycle | Credential được tạo, đọc, xóa và tái sử dụng đúng | State rò giữa test làm test sau pass giả |

Không nên gom mọi failure thành “passkey không hoạt động”. Hãy xác định failure nằm ở browser API, virtual authenticator, application ceremony, server verification hay test setup.

## Virtual authenticator của Playwright hoạt động ra sao?

API [Credentials của Playwright](https://playwright.dev/docs/api/class-credentials) cung cấp virtual WebAuthn authenticator gắn với một BrowserContext. Nó cho phép test seed credential, cài authenticator trước khi page chạm vào `navigator.credentials`, đọc credential đã đăng ký và xóa credential sau test mà không cần hardware key.

Một điểm quan trọng là `install()` phải được gọi trước khi page lần đầu gọi WebAuthn API. Nếu app khởi tạo auth capability ngay khi load, việc cài virtual authenticator sau `page.goto()` có thể tạo false failure hoặc khiến test chạy bằng một state khác với dự kiến.

Ví dụ dưới đây là pattern setup, không phải contract cho mọi version hoặc mọi app. Hãy kiểm tra phiên bản Playwright đang dùng và giữ credential state trong thư mục test artifact được bảo vệ.

<code-runner lang="ts">
import { test, expect } from '@playwright/test';

 test('registers and signs in with a passkey', async ({ browser }) => {
  const context = await browser.newContext();
  await context.credentials.install();
  const page = await context.newPage();

  await page.goto('https://app.example.test/register');
  await page.getByRole('button', { name: 'Tạo passkey' }).click();
  await expect(page.getByText('Passkey đã được tạo')).toBeVisible();

  const credentials = await context.credentials.get({ rpId: 'app.example.test' });
  expect(credentials).toHaveLength(1);

  await context.close();
});
</code-runner>

Pattern trên kiểm tra registration và credential state. Nó chưa chứng minh server verify assertion đúng, chưa kiểm tra user verification bị từ chối và chưa kiểm tra session sau logout. Mỗi outcome đó cần một test intent riêng.

## Làm sao test registration và login mà không rò state?

Mỗi test nên có BrowserContext mới, RP ID rõ ràng và data user riêng. Không nên dùng một credential file chung cho toàn bộ suite nếu test có thể thay đổi credential state hoặc server record. Credential captured có private key, vì vậy không commit file này vào repository và không đưa nó vào log CI.

Một chiến lược có hai lớp. Lớp đầu tạo user, đăng ký passkey và kiểm tra server lưu public credential. Lớp sau seed credential đã biết, mở login page và kiểm tra assertion tạo đúng session. Hai lớp giúp phân biệt lỗi registration với lỗi login.

<table-testcase cols="5" rows="5" headers="ID|Precondition|Action|Expected result|Evidence">
| PASSKEY-01 | User chưa có credential | Mở register, click tạo passkey | UI báo thành công, server có credential mới | Credential count và response assertion |
| PASSKEY-02 | User có credential hợp lệ | Logout, mở login, chọn passkey | Session mới thuộc đúng user | Session assertion và user identity |
| PASSKEY-03 | Credential không thuộc RP ID | Seed credential với RP ID khác | Flow fail an toàn, không tạo session | Error state và server audit |
| PASSKEY-04 | User verification bị từ chối | Tắt auto approval hoặc set verified false | UI hiển thị lỗi, không tạo session | Error message và response status |
</table-testcase>

<grid-content>
Tách test setup khỏi test intent
> Setup tạo điều kiện, assertion chứng minh behavior.

```markdown
**Setup**

1. Tạo BrowserContext mới.
2. Install virtual authenticator.
3. Tạo user data riêng.
4. Gắn RP ID và origin đúng.
```

```markdown
**Intent và cleanup**

1. Thực hiện registration hoặc assertion.
2. Kiểm tra UI state.
3. Kiểm tra server-side outcome.
4. Cleanup context và credential.
```

</grid-content>

[Hướng dẫn passkey thực hành của Oursky](https://www.oursky.com/blogs/a-practical-guide-automating-passkey-testing-with-playwright-and-authgear/) cũng minh họa cách dùng virtual authenticator và CDP để mô phỏng presence, user verification, credential listing và rejection flow. Điểm cần giữ trong test production là boundary: CDP hoặc virtual API giúp tạo deterministic test, nhưng không thay thế browser matrix và hardware coverage.

## Làm sao kiểm tra rejection và invalid credential?

Một suite chỉ auto-approve sẽ bỏ qua các nhánh quan trọng của authentication. Tester nên có ít nhất một scenario user verification bị từ chối, một credential bị xóa, một credential sai RP ID và một challenge hết hạn hoặc đã dùng lại, tùy vào khả năng của application backend.

Rejection phải được assert ở hai lớp. UI cần hiển thị trạng thái có thể hiểu và không giả vờ đã đăng nhập. Server không được tạo session hoặc cập nhật last-login như thể assertion thành công. Nếu chỉ kiểm tra message trên UI, test có thể pass dù backend đã phát hành session sai.

Invalid credential test cần tránh giả định rằng mọi lỗi sẽ có cùng error message trên mọi browser. Hãy assert invariant về security outcome, như “không có authenticated session”, rồi kiểm tra message theo contract UI nếu team đã cam kết wording.

<multiple-choice correct="C" select="single">
Test passkey đăng nhập thành công trên UI nhưng API kiểm tra session trả về user anonymous. Kết luận phù hợp nhất là gì?
- A: Test pass vì UI đã hiển thị trang dashboard
- B: Xóa assertion API vì browser test chỉ kiểm tra UI
- C: Flow có dấu hiệu false positive hoặc lỗi session propagation, cần giữ cả hai assertion
- D: Luôn sửa expected result thành anonymous
</multiple-choice>

## RP ID, origin và browser matrix cần được kiểm tra thế nào?

RP ID thường gắn với effective domain của relying party, còn origin bao gồm scheme, host và port. App chạy ở `https://app.example.test` và app chạy ở `http://localhost:3000` không phải cùng một test environment chỉ vì cùng codebase.

Khi chạy local, preview, staging và production-like environment, hãy lập matrix gồm origin, RP ID, browser engine và cách server tạo options. Nếu framework có baseURL, test cần đọc baseURL từ config và không hard-code một host trong helper rồi dùng cho tất cả environment.

Browser automation cũng không chứng minh passkey sync giữa thiết bị. Virtual authenticator kiểm tra ceremony và application integration trong một context. Coverage trên iOS, Android, Windows Hello, hardware security key, platform authenticator và cross-device flow cần test thật hoặc provider matrix riêng.

![Minimalist flat vector UI design, premium professional EdTech editorial artwork for passkey test coverage, 21:9 wide section image, clean bento-grid matrix with columns labeled "Origin", "RP ID", "Browser", and "Device", rows labeled "Local", "Staging", and "Production-like", blue check icons for deterministic virtual tests and amber boundary markers for real-device coverage, arrows showing credential scope, short Vietnamese labels only, Paper White background #fafafa, Zinc-900 content #18181b, T5Edu Blue accent #1a73e8, Amber highlight #f59e0b, subtle one-pixel borders, restrained liquid-glass layers, no people, no faces, no hands, no 3D, no glossy plastic, no photorealism, no dramatic lighting, no purple, no violet, no pink, no neon, no logo, no watermark](https://files.manuscdn.com/user_upload_by_module/session_file/310519663091035343/JpLNJhLlVeGHPmBf.png)

## Test passkey nên đo evidence nào ngoài pass hoặc fail?

Passkey test có nhiều state ẩn. Khi fail, artifact nên cho biết browser engine, Playwright version, baseURL, RP ID, test user id không nhạy cảm, ceremony đang chạy, credential count và server response đã được redacted. Không log private key, raw assertion nếu policy bảo mật không cho phép hoặc token session.

Với team đã có [Playwright với TypeScript cơ bản cho Tester](https://t5edu.site/blogs/playwright-voi-typescript-co-ban-cho-tester), có thể thêm helper tạo context, install authenticator, seed data và cleanup. Helper không nên che mất intent của test. Người đọc test vẫn cần thấy test đang chứng minh registration, assertion, rejection hay credential reuse.

Bạn cũng nên ghi rõ coverage boundary trong report. “Passkey login pass trên Chromium với virtual authenticator” là claim kiểm chứng được. “Passkey hoạt động trên mọi thiết bị” là claim vượt quá evidence của test.

<dropdown-content>
Có nên lưu private key của passkey để reuse giữa các run không?
> Lưu ý bảo mật và tính tái lập khi quản lý credential trong test.

```markdown
Có thể dùng trong test isolation nếu key được lưu ở artifact hoặc secret store phù hợp, không commit vào Git và không đưa vào log. Với test registration, tạo credential mới thường phản ánh đúng lifecycle hơn.
```
</dropdown-content>

<dropdown-content>
Virtual authenticator có thay thế test trên thiết bị thật không?
> Phạm vi coverage mà virtual authenticator có thể chứng minh.

```markdown
Không. Nó phù hợp để kiểm tra ceremony, state transition và server integration có tính deterministic. Thiết bị thật vẫn cần cho platform behavior, UX prompt, biometric or PIN behavior, sync và browser-specific coverage.
```
</dropdown-content>

## Tổng kết

Kiểm thử passkey với WebAuthn cần giữ bốn boundary: credential state, RP ID hoặc origin, user verification và server-side session outcome.

Nếu team đã có Playwright suite, hãy bắt đầu bằng một proof of concept gồm registration, assertion và rejection trong BrowserContext riêng, sau đó thêm matrix origin và browser trước khi mở rộng.

Nếu cần củng cố nền tảng automation trước khi đi sâu vào WebAuthn, hãy xem [Blog Tester của T5Edu](https://t5edu.site/blogs) và [Playwright Test Agents](https://t5edu.site/blogs/playwright-test-agents-planner-generator-va-healer) để đối chiếu cách team đang tổ chức browser test, nhưng đừng xem agent flow là thay thế cho security assertion.

Bạn đã kiểm tra server-side session và RP ID trong passkey test, hay mới chỉ assert dashboard hiển thị?

## Hashtag

> passkey, webauthn, playwright, automation testing, qa engineer
