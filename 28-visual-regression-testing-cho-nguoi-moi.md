# Visual Regression Testing: Baseline Và Triage

> Visual regression testing giúp tester phát hiện thay đổi giao diện ngoài ý muốn bằng cách so sánh screenshot với baseline có kiểm soát, từ đó biết khi nào cần báo bug và khi nào chỉ là thay đổi hợp lệ.

![Minimalist flat vector UI design, premium professional EdTech editorial artwork, 1:1 square cover illustration of a beginner tester comparing two web UI screenshots labeled exactly Baseline and Lần chạy mới, with a blue magnifying glass over a small highlighted visual difference and an amber warning badge labeled Visual diff. Clean bento-grid composition with strong negative space. Paper White or Zinc-50 background #fafafa. Zinc-900 content #18181b. T5Edu Blue accent #1a73e8. Amber highlight #f59e0b. Subtle one-pixel borders and restrained liquid-glass layers. Simple flat icons, geometric shapes, crisp Vietnamese labels, no people, no faces, no hands, no 3D, no photorealism, no purple, no violet, no pink, no neon, no logo, no watermark](https://files.manuscdn.com/user_upload_by_module/session_file/310519663091035343/uLpBWtuNwZiUNFwu.png)

## Visual regression testing là gì và khác functional testing ra sao?

**Visual regression testing** chụp lại giao diện ở một trạng thái đã biết, lưu ảnh đó làm **baseline**. Sau đó nó so sánh screenshot của lần chạy mới với baseline để tìm thay đổi ngoài ý muốn.

Nếu một nút bị lệch, chữ bị tràn hoặc màu nền sai sau khi sửa CSS, visual diff chỉ ra thay đổi mà assertion chức năng không nhìn thấy.

Functional testing hỏi “tester có bấm được nút và nhận đúng kết quả không”. Visual testing hỏi thêm “user có đang nhìn thấy giao diện đúng như thiết kế không”.

Một form có thể submit thành công nhưng label bị che, nút nằm ngoài màn hình mobile hoặc thông báo lỗi cùng màu với nền.

Vì vậy visual regression testing bổ sung cho test case chức năng, không thay thế chúng.

Nếu đang xây foundation, hãy xem [khóa học Tester và QA trên T5Edu](/courses) để củng cố các khái niệm test case, expected result và defect trước khi thêm visual diff vào bộ kiểm thử.

Bạn cũng có thể xem [các bài blog testing của T5Edu](/blogs) để nối kỹ thuật này với quy trình kiểm thử thực tế.

<multiple-choice correct="C" select="single">
Một test chức năng kiểm tra nút “Đăng nhập” có thể click và chuyển trang thành công. Visual regression testing bổ sung câu hỏi nào?
- A: Database có bao nhiêu bảng?
- B: API có bao nhiêu endpoint?
- C: Nút, chữ, khoảng cách và trạng thái hiển thị có đúng như baseline không?
- D: Tester đã chạy test bao nhiêu lần?
</multiple-choice>

## Baseline screenshot được tạo và phê duyệt như thế nào?

Baseline là ảnh chuẩn để so sánh, nhưng không phải ảnh đầu tiên được tạo ra đều mặc nhiên đúng.

Trước khi lưu baseline, tester cần mở trang ở dữ liệu ổn định, kiểm tra viewport, xác nhận font đã tải và bảo đảm nội dung trong ảnh phản ánh trạng thái mà team muốn bảo vệ.

Playwright Test hỗ trợ `await expect(page). toHaveScreenshot()`. Theo [tài liệu visual comparisons của Playwright](https://playwright.dev/docs/test-snapshots), lần chạy đầu tạo reference screenshot, những lần chạy sau so sánh với reference.

Khi một thay đổi giao diện đã được review và chấp nhận, team mới cập nhật baseline bằng cờ `--update-snapshots`, thay vì cập nhật ngay mỗi khi test đỏ.

Quy trình phê duyệt baseline cho người mới có thể đơn giản như sau:

| Bước | Câu hỏi cần trả lời | Bằng chứng cần giữ |
| --- | --- | --- |
| Chuẩn bị | Trang có dữ liệu và viewport ổn định chưa? | URL, viewport, test data |
| Chụp | Ảnh có hiển thị đủ component cần bảo vệ không? | Screenshot hiện tại |
| Review | Khác biệt là chủ đích hay lỗi ngoài ý muốn? | Diff và mô tả thay đổi |
| Lưu | Reviewer đã đồng ý cập nhật chuẩn chưa? | Commit hoặc pull request |

Đừng tạo baseline từ một trang đang có banner ngẫu nhiên, đồng hồ chạy hoặc dữ liệu tài khoản thay đổi theo mỗi request. Nếu baseline sai, mọi lần so sánh sau sẽ báo lỗi sai theo cùng một mẫu.

![Minimalist flat vector UI design, premium professional EdTech editorial artwork, 21:9 wide educational diagram showing the visual regression baseline workflow from left to right. Four rounded cards connected by solid T5Edu Blue arrows, labeled exactly Chuẩn bị dữ liệu, Chụp screenshot, Review diff, and Lưu baseline. Include flat icons for stable browser, camera, magnifying glass, and approved checkmark. Add an amber checkpoint badge labeled Phê duyệt trước khi cập nhật. Clean horizontal bento-grid composition with strong negative space. Paper White or Zinc-50 background #fafafa. Zinc-900 content #18181b. T5Edu Blue accent #1a73e8. Amber highlight #f59e0b. Subtle one-pixel borders and restrained liquid-glass layers. Exact short Vietnamese labels only, no people, no faces, no hands, no 3D, no photorealism, no purple, no violet, no pink, no neon, no logo, no watermark](https://files.manuscdn.com/user_upload_by_module/session_file/310519663091035343/IBycGODUUihyxKBq.png)

## Làm visual diff đầu tiên với Playwright như thế nào?

Bạn không cần bắt đầu bằng một nền tảng visual testing phức tạp. Với một project Playwright đã setup, hãy tạo một test nhỏ chỉ bảo vệ một trạng thái quan trọng, chẳng hạn trang login sau khi tải xong.

Ví dụ dưới đây dùng TypeScript và chọn screenshot có tên rõ ràng:

```ts
import { test, expect } from '@playwright/test';

test('login page keeps the expected visual layout', async ({ page }) => {
  await page.goto('https://example.test/login');
  await expect(page).toHaveScreenshot('login-page.png');
});
```

Lần chạy đầu có thể tạo file snapshot, từ lần thứ hai test sẽ báo diff nếu ảnh mới khác baseline theo ngưỡng đã cấu hình. Khi học, hãy bắt đầu với một component hoặc một khu vực nhỏ.

Screenshot toàn trang thuận tiện để nhìn tổng thể nhưng khó triage, vì một thay đổi nhỏ ở header có thể làm tester phải đọc một ảnh rất lớn.

Playwright dùng pixel comparison và cho phép cấu hình `maxDiffPixels`.

Tuy nhiên, đừng chọn một ngưỡng lớn chỉ để làm test xanh; ngưỡng phải phản ánh mức nhiễu chấp nhận được của giao diện, còn thay đổi quan trọng như mất nút, lệch layout hoặc sai màu trạng thái

vẫn phải khiến tester điều tra.

Nếu muốn củng cố JavaScript và TypeScript trước khi viết test, [khóa JavaScript cho QA trên T5Edu](/courses/javascript-cho-qa-engineer) là bước chuẩn bị phù hợp.

Người mới cũng nên đọc [blog testing của T5Edu](/blogs) rồi tự chuyển một trạng thái trong đó thành visual test nhỏ.

<table-testcase cols="4" rows="4" headers="ID|Trạng thái|Thao tác|Kết quả mong đợi">
| VR01 | Trang login mặc định | Mở trang với viewport desktop ổn định | Screenshot khớp baseline |
| VR02 | Thông báo nhập sai | Submit dữ liệu sai | Vùng lỗi xuất hiện đúng vị trí, màu và kích thước |
| VR03 | Viewport mobile | Mở trang ở chiều rộng mobile | Layout không tràn ngang, nút vẫn nhìn thấy |
| VR04 | Dữ liệu động | Mở dashboard có số dư thay đổi | Vùng động được mask hoặc mock trước khi so sánh |
</table-testcase>

## Vì sao visual test bị flaky và xử lý dynamic content ra sao?

Visual test flaky là test lúc pass, lúc fail dù code giao diện không có thay đổi có chủ đích.

Nguyên nhân thường gặp là font tải chưa xong, ảnh có kích thước khác nhau, animation đang chạy, timestamp thay đổi, dữ liệu random hoặc browser chạy trên môi trường khác baseline. [Tài liệu Playwright về visual comparisons](https://playwright.

dev/docs/test-snapshots) cảnh báo rằng rendering có thể khác theo hệ điều hành, phiên bản browser, font, hardware và chế độ headless.

Cách thực tế nhất cho người mới là tạo baseline và chạy test trong cùng image hoặc môi trường CI, đồng thời tắt animation và dùng dữ liệu cố định.

Khi có vùng động, có ba lựa chọn: mock response để nội dung luôn giống nhau, mask vùng không cần kiểm tra bằng option phù hợp của framework, hoặc tách component động ra khỏi screenshot nếu mục tiêu chỉ

là bảo vệ layout tĩnh. [Best practices của Applitools về Playwright visual testing](https://applitools. com/blog/recap-playwright-visual-testing-best-practices/) cũng khuyến nghị snapshot component nhỏ, xử lý layout shift, mask dữ liệu động và debug theo vùng thay vì chỉ nhìn ảnh toàn trang.

Đây là nguyên tắc quan trọng: hãy làm giảm nhiễu trước khi tăng tolerance.

<grid-content>
Cách giảm false failure trong visual regression testing
> Mỗi lựa chọn phải giữ lại tín hiệu lỗi quan trọng, không chỉ làm số test pass tăng lên.

```markdown

**Dữ liệu ổn định**

Dùng fixture hoặc mock data cố định. Tránh timestamp, số random, avatar thay đổi và nội dung phụ thuộc tài khoản thật.

```

```markdown

**Môi trường ổn định**

Giữ nhất quán browser, OS, font, viewport và chế độ headless giữa lúc tạo baseline và lúc chạy kiểm thử.

```

```markdown

**Phạm vi vừa đủ**

Ưu tiên screenshot component hoặc vùng quan trọng. Screenshot nhỏ giúp diff dễ đọc và root cause dễ tìm hơn.

```

</grid-content>

![Minimalist flat vector UI design, premium professional EdTech editorial artwork, 21:9 wide educational illustration for a Vietnamese T5Edu blog about visual regression testing. Show a clean horizontal triage board with three connected panels labeled exactly Baseline, Actual, and Diff, followed by two decision cards labeled Bug and Accepted change. Use a blue magnifying-glass icon, blue arrows, amber highlighted mismatch, Paper White or Zinc-50 background #fafafa, Zinc-900 content #18181b, T5Edu Blue accent #1a73e8, Amber highlight #f59e0b, subtle one-pixel borders, restrained liquid-glass layers, strong negative space, flat geometric icons. Exact short English labels only. No people, no faces, no hands, no purple, no violet, no pink, no neon, no photorealism, no 3D, no glossy plastic, no dramatic lighting, no logo, no watermark, no dense paragraphs](https://files.manuscdn.com/user_upload_by_module/session_file/310519663091035343/pcgozSfxUbTcmxtv.png)

## Triage visual diff thế nào để biết đó là bug?

Khi test đỏ, đừng cập nhật baseline ngay.

Hãy đọc diff theo ba lớp: **độ rộng thay đổi** (một pixel noise nhỏ khác với cả card biến mất), **nguyên nhân** (CSS mới, font chưa tải, viewport sai hoặc dữ liệu động) và **tác động đến user** (mất

nút thanh toán nghiêm trọng hơn thay đổi border của card). Quy trình triage tối thiểu gồm bốn câu hỏi:

1. Thay đổi này có nằm trong yêu cầu hoặc design đã được duyệt không?
2. Diff có lặp lại ổn định khi chạy lại trong cùng môi trường không?
3. Nó chỉ nằm ở vùng dynamic content hay ảnh hưởng layout, text, màu và trạng thái tương tác?
4. Nếu là bug, tester cần report component nào, viewport nào và bước tái hiện nào?

Nếu thay đổi có chủ đích, cập nhật baseline trong cùng pull request với code UI và ghi rõ lý do.

Nếu chưa rõ, giữ test đỏ, đính kèm actual image, baseline image và diff image để developer hoặc designer cùng review. Việc “approve tất cả thay đổi” làm baseline mất giá trị kiểm soát.

![Minimalist flat vector UI design, premium professional EdTech editorial artwork, 21:9 wide visual diff triage illustration with three panels labeled exactly Baseline, Actual, and Diff. Show a stable login card in the first panel, a shifted button in the second, and an amber highlighted difference in the third, connected by blue arrows. Add a compact decision branch labeled Bug or Accepted change, with a blue bug icon and amber check icon. Clean horizontal bento-grid composition with strong negative space. Paper White or Zinc-50 background #fafafa. Zinc-900 content #18181b. T5Edu Blue accent #1a73e8. Amber highlight #f59e0b. Exact short Vietnamese labels only, no people, no faces, no hands, no 3D, no photorealism, no purple, no violet, no pink, no neon, no logo, no watermark](https://files.manuscdn.com/user_upload_by_module/session_file/310519663091035343/UktTjnIpLaNAyOqE.png)

<dropdown-content>
Khi nào nên cập nhật baseline và khi nào phải báo bug?
> Chỉ cập nhật baseline khi thay đổi đã được xác nhận là chủ đích và có người review.

```markdown

Nếu yêu cầu đổi màu nút đã được duyệt, screenshot mới đúng với design và không có vùng khác bị ảnh hưởng, cập nhật baseline là hợp lý. Nếu nút tự nhiên lệch khỏi layout, chữ bị cắt hoặc diff chỉ xuất hiện trên một máy, hãy điều tra trước. Baseline là tiêu chuẩn đã được phê duyệt, không phải nơi che giấu test đỏ.

```

</dropdown-content>

## Người mới nên bắt đầu visual regression testing từ đâu?

Hãy bắt đầu theo lộ trình bốn bước:

| Bước | Việc cần làm | Evidence cần lưu |
|---|---|---|
| 1 | Chọn login page hoặc màn hình ít biến động, tạo dữ liệu cố định | URL, viewport và dữ liệu test |
| 2 | Chụp baseline rồi chạy lại để tạo diff có chủ đích | Baseline và actual image |
| 3 | Đổi một thuộc tính CSS, review diff và viết bug report | Component, expected result và evidence |
| 4 | Mở rộng sang responsive layout, theme, component library hoặc nhiều browser | Ma trận màn hình và quyết định coverage |

Đừng vội bảo vệ mọi route. Một visual test có giá trị phải có mục tiêu mà team hiểu và có thể review khi test fail.

1. Học foundation kiểm thử: kết hợp bài này với [khóa học Tester và QA](/courses).
2. Luyện tư duy theo câu hỏi và expected result: đọc [khu vực blog testing của T5Edu](/blogs), sau đó thiết kế bảng testcase visual cho màn hình nhỏ.

## Tổng kết

Visual regression testing kiểm tra giao diện bằng baseline và screenshot comparison, còn functional testing kiểm tra hành vi và kết quả nghiệp vụ.

Baseline phải được tạo trong môi trường ổn định, review trước khi lưu và chỉ cập nhật khi thay đổi đã được chấp nhận.

Dynamic content, font, animation, viewport và khác biệt môi trường là nguồn false failure phổ biến, cần xử lý bằng mock, mask hoặc cấu hình ổn định.

Khi visual diff xuất hiện, tester phải triage trước khi approve, đồng thời phân biệt bug thật với accepted change.

Nếu bạn đã nắm test case và expected result, hãy học thêm [các khóa học nền tảng cho Tester](/courses) rồi tự viết một visual test cho login page.

Bạn sẽ bảo vệ component nào đầu tiên, và bằng chứng nào giúp bạn quyết định diff đó là bug?

## Hashtag
> visual regression testing, screenshot testing, UI testing, test automation, QA
