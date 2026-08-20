visual-regression-testing, visual-testing, playwright, qa-beginner, software-testing

# Visual Regression Testing Cho Người Mới

![Minimalist flat vector UI design, premium professional EdTech editorial artwork, 1:1 square cover for a beginner visual regression testing article, clean bento-grid composition with strong negative space, Paper White background #fafafa, Zinc-900 content #18181b, T5Edu Blue accent #1a73e8, Amber highlight #f59e0b, two browser cards labeled "Baseline" and "Lần chạy mới" with a blue magnifying glass over a small highlighted visual difference and an amber badge labeled "Visual diff", simple flat geometric icons, crisp short Vietnamese labels only, subtle one-pixel borders and restrained liquid-glass layers, no gradients, no people, no faces, no hands, no 3D, no photorealism, no purple, no violet, no pink, no neon, no logo, no watermark](https://files.manuscdn.com/user_upload_by_module/session_file/310519663091035343/uLpBWtuNwZiUNFwu.png)

## Visual regression testing là gì và khác functional testing ra sao?

**Visual regression testing** là cách chụp lại giao diện ở một trạng thái đã biết, lưu ảnh đó làm **baseline**, rồi so sánh screenshot của lần chạy mới với baseline để tìm thay đổi ngoài ý muốn. Nếu một nút bị lệch, chữ bị tràn, card biến mất hoặc màu nền sai sau khi sửa CSS, visual diff có thể chỉ ra thay đổi mà assertion chức năng không nhìn thấy.

Functional testing thường hỏi: “Tester có bấm được nút và nhận đúng kết quả không?”. Visual testing hỏi thêm: “User có đang nhìn thấy giao diện đúng như thiết kế không?”. Một form có thể submit thành công nhưng label bị che, nút nằm ngoài màn hình mobile hoặc thông báo lỗi cùng màu với nền. Vì vậy visual regression testing bổ sung cho test case chức năng, không thay thế chúng.

Nếu bạn đang xây foundation, hãy xem [khóa học Tester và QA trên T5Edu](https://t5edu.site/courses) để củng cố các khái niệm test case, expected result và defect trước khi thêm visual diff vào bộ kiểm thử. Bạn cũng có thể xem [các bài blog testing của T5Edu](https://t5edu.site/blogs) để nối kỹ thuật này với quy trình kiểm thử thực tế.

<multiple-choice correct="C" select="single">
Một test chức năng kiểm tra nút “Đăng nhập” có thể click và chuyển trang thành công. Visual regression testing bổ sung câu hỏi nào?
- A: Database có bao nhiêu bảng?
- B: API có bao nhiêu endpoint?
- C: Nút, chữ, khoảng cách và trạng thái hiển thị có đúng như baseline không?
- D: Tester đã chạy test bao nhiêu lần?
</multiple-choice>

## Baseline screenshot được tạo và phê duyệt như thế nào?

Baseline là ảnh chuẩn để so sánh, nhưng không phải ảnh đầu tiên được tạo ra đều mặc nhiên đúng. Trước khi lưu baseline, tester cần mở trang ở dữ liệu ổn định, kiểm tra viewport, xác nhận font đã tải và bảo đảm nội dung trong ảnh phản ánh trạng thái mà team muốn bảo vệ.

Playwright Test hỗ trợ `await expect(page).toHaveScreenshot()`. Theo [tài liệu visual comparisons của Playwright](https://playwright.dev/docs/test-snapshots), lần chạy đầu tạo reference screenshot, những lần chạy sau so sánh với reference. Khi một thay đổi giao diện đã được review và chấp nhận, team mới cập nhật baseline bằng cờ `--update-snapshots`, thay vì cập nhật ngay mỗi khi test đỏ.

Quy trình phê duyệt baseline cho người mới có thể đơn giản như sau:

| Bước | Câu hỏi cần trả lời | Bằng chứng cần giữ |
| --- | --- | --- |
| Chuẩn bị | Trang có dữ liệu và viewport ổn định chưa? | URL, viewport, test data |
| Chụp | Ảnh có hiển thị đủ component cần bảo vệ không? | Screenshot hiện tại |
| Review | Khác biệt là chủ đích hay lỗi ngoài ý muốn? | Diff và mô tả thay đổi |
| Lưu | Reviewer đã đồng ý cập nhật chuẩn chưa? | Commit hoặc pull request |

Đừng tạo baseline từ một trang đang có banner ngẫu nhiên, đồng hồ chạy hoặc dữ liệu tài khoản thay đổi theo mỗi request. Nếu baseline sai, mọi lần so sánh sau sẽ báo lỗi sai theo cùng một mẫu.

![Minimalist flat vector UI design, premium professional EdTech editorial artwork, 21:9 wide section image for a visual regression testing baseline workflow, clean horizontal bento-grid composition with strong negative space, Paper White background #fafafa, Zinc-900 content #18181b, T5Edu Blue accent #1a73e8, Amber highlight #f59e0b, four rounded cards connected left-to-right by solid blue arrows labeled "Chuẩn bị dữ liệu", "Chụp screenshot", "Review diff", and "Lưu baseline", flat icons for fixture, camera, magnifying glass, and approved checkmark, amber checkpoint badge labeled "Phê duyệt trước khi cập nhật", short Vietnamese labels only, subtle one-pixel borders and restrained liquid-glass layers, no gradients, no people, no faces, no hands, no 3D, no photorealism, no purple, no violet, no pink, no neon, no logo, no watermark](https://files.manuscdn.com/user_upload_by_module/session_file/310519663091035343/IBycGODUUihyxKBq.png)

## Làm visual diff đầu tiên với Playwright như thế nào?

Bạn không cần bắt đầu bằng một nền tảng visual testing phức tạp. Với một project Playwright đã setup, hãy tạo một test nhỏ chỉ bảo vệ một trạng thái quan trọng, chẳng hạn trang login sau khi tải xong. Ví dụ dưới đây dùng TypeScript và chọn screenshot có tên rõ ràng:

```ts
import { test, expect } from '@playwright/test';

test('login page keeps the expected visual layout', async ({ page }) => {
  await page.goto('https://example.test/login');
  await expect(page).toHaveScreenshot('login-page.png');
});
```

Lần chạy đầu có thể tạo file snapshot. Từ lần thứ hai, test sẽ báo diff nếu ảnh mới khác baseline theo ngưỡng đã cấu hình. Khi học, hãy bắt đầu với một component hoặc một khu vực nhỏ. Screenshot toàn trang thuận tiện để nhìn tổng thể nhưng khó triage vì một thay đổi nhỏ ở header có thể làm tester phải đọc một ảnh rất lớn.

Playwright dùng pixel comparison và cho phép cấu hình `maxDiffPixels`. Tuy nhiên, đừng chọn một ngưỡng lớn chỉ để làm test xanh. Ngưỡng phải phản ánh mức nhiễu chấp nhận được của giao diện, còn thay đổi quan trọng như mất nút, lệch layout hoặc sai màu trạng thái vẫn phải khiến tester điều tra.

Nếu bạn muốn củng cố JavaScript và TypeScript trước khi viết test, [khóa JavaScript cho QA trên T5Edu](https://t5edu.site/courses/javascript-cho-qa-engineer) là bước chuẩn bị phù hợp. Người mới cũng nên đọc [blog testing của T5Edu](https://t5edu.site/blogs) rồi tự chuyển một trạng thái trong đó thành visual test nhỏ.

<table-testcase cols="4" rows="4" headers="ID|Trạng thái|Thao tác|Kết quả mong đợi">
| VR01 | Trang login mặc định | Mở trang với viewport desktop ổn định | Screenshot khớp baseline |
| VR02 | Thông báo nhập sai | Submit dữ liệu sai | Vùng lỗi xuất hiện đúng vị trí, màu và kích thước |
| VR03 | Viewport mobile | Mở trang ở chiều rộng mobile | Layout không tràn ngang, nút vẫn nhìn thấy |
| VR04 | Dữ liệu động | Mở dashboard có số dư thay đổi | Vùng động được mask hoặc mock trước khi so sánh |
</table-testcase>

## Vì sao visual test bị flaky và xử lý dynamic content ra sao?

Visual test flaky là test lúc pass, lúc fail dù code giao diện không có thay đổi có chủ đích. Nguyên nhân thường gặp là font tải chưa xong, ảnh có kích thước khác nhau, animation đang chạy, timestamp thay đổi, dữ liệu random hoặc browser được chạy trên môi trường khác baseline.

[Tài liệu Playwright về visual comparisons](https://playwright.dev/docs/test-snapshots) cảnh báo rằng rendering có thể khác theo hệ điều hành, phiên bản browser, font, hardware và chế độ headless. Cách thực tế nhất cho người mới là tạo baseline và chạy test trong cùng image hoặc môi trường CI, đồng thời tắt animation và dùng dữ liệu cố định.

Khi có vùng động, bạn có ba lựa chọn. Thứ nhất là mock response để nội dung luôn giống nhau. Thứ hai là mask vùng không cần kiểm tra bằng option phù hợp của framework. Thứ ba là tách component động ra khỏi screenshot nếu mục tiêu của test chỉ là bảo vệ layout tĩnh.

[Best practices của Applitools về Playwright visual testing](https://applitools.com/blog/recap-playwright-visual-testing-best-practices/) cũng khuyến nghị snapshot component nhỏ, xử lý layout shift, mask dữ liệu động và debug theo vùng thay vì chỉ nhìn ảnh toàn trang. Đây là nguyên tắc quan trọng: hãy làm giảm nhiễu trước khi tăng tolerance.

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

## Triage visual diff thế nào để biết đó là bug?

Khi test đỏ, đừng cập nhật baseline ngay. Hãy đọc diff theo ba lớp. Lớp thứ nhất là **độ rộng thay đổi**, chẳng hạn một pixel noise nhỏ khác với cả card biến mất. Lớp thứ hai là **nguyên nhân**, chẳng hạn CSS mới, font chưa tải, viewport sai hoặc dữ liệu động. Lớp thứ ba là **tác động đến user**, chẳng hạn thay đổi làm mất nút thanh toán nghiêm trọng hơn thay đổi border của card.

Quy trình triage tối thiểu gồm bốn câu hỏi:

1. Thay đổi này có nằm trong yêu cầu hoặc design đã được duyệt không?
2. Diff có lặp lại ổn định khi chạy lại trong cùng môi trường không?
3. Nó chỉ nằm ở vùng dynamic content hay ảnh hưởng layout, text, màu và trạng thái tương tác?
4. Nếu là bug, tester cần report component nào, viewport nào và bước tái hiện nào?

Nếu thay đổi có chủ đích, cập nhật baseline trong cùng pull request với code UI và ghi rõ lý do. Nếu chưa rõ, giữ test đỏ, đính kèm actual image, baseline image và diff image để developer hoặc designer cùng review. Việc “approve tất cả thay đổi” làm baseline mất giá trị kiểm soát.

![Minimalist flat vector UI design, premium professional EdTech editorial artwork, 21:9 wide section image for visual regression testing triage, clean horizontal bento-grid composition with strong negative space, Paper White background #fafafa, Zinc-900 content #18181b, T5Edu Blue accent #1a73e8, Amber highlight #f59e0b, three browser panels labeled "Baseline", "Actual", and "Diff" connected by blue arrows, shifted login button highlighted in amber, decision branch labeled "Bug" and "Accepted change" with flat bug and approval icons, short Vietnamese labels only, subtle one-pixel borders and restrained liquid-glass layers, no gradients, no people, no faces, no hands, no 3D, no photorealism, no purple, no violet, no pink, no neon, no logo, no watermark](https://files.manuscdn.com/user_upload_by_module/session_file/310519663091035343/UktTjnIpLaNAyOqE.png)

<dropdown-content>
Khi nào nên cập nhật baseline và khi nào phải báo bug?
> Chỉ cập nhật baseline khi thay đổi đã được xác nhận là chủ đích và có người review.
```markdown
Nếu yêu cầu đổi màu nút đã được duyệt, screenshot mới đúng với design và không có vùng khác bị ảnh hưởng, cập nhật baseline là hợp lý. Nếu nút tự nhiên lệch khỏi layout, chữ bị cắt hoặc diff chỉ xuất hiện trên một máy, hãy điều tra trước. Baseline là tiêu chuẩn đã được phê duyệt, không phải nơi che giấu test đỏ.
```
</dropdown-content>

## Người mới nên bắt đầu visual regression testing từ đâu?

Hãy bắt đầu bằng một trang ít biến động và một mục tiêu nhỏ. Trong buổi đầu, tester có thể chọn login page, tạo dữ liệu cố định, chụp baseline và chạy lại để thấy một diff có chủ đích. Trong buổi tiếp theo, thay đổi một thuộc tính CSS, quan sát actual image và viết bug report có component, viewport, expected result và evidence.

Đừng vội bảo vệ mọi route. Một visual test hữu ích là test mà team hiểu nó đang bảo vệ điều gì và có thể review khi nó fail. Sau khi đã quen với baseline và triage, bạn mới mở rộng sang responsive layout, theme, component library hoặc nhiều browser.

Nếu bạn đang học foundation kiểm thử, hãy kết hợp bài này với [khóa học Tester và QA](https://t5edu.site/courses) thay vì chỉ học syntax của Playwright. Nếu muốn luyện tư duy theo câu hỏi và expected result, hãy bắt đầu từ [khu vực blog testing của T5Edu](https://t5edu.site/blogs) rồi chọn một màn hình nhỏ để tự thiết kế bảng testcase visual.

## Tổng kết

- Visual regression testing kiểm tra giao diện bằng baseline và screenshot comparison, còn functional testing kiểm tra hành vi và kết quả nghiệp vụ.
- Baseline phải được tạo trong môi trường ổn định, review trước khi lưu và chỉ cập nhật khi thay đổi đã được chấp nhận.
- Dynamic content, font, animation, viewport và khác biệt môi trường là nguồn false failure phổ biến, cần xử lý bằng mock, mask hoặc cấu hình ổn định.
- Khi visual diff xuất hiện, tester phải triage trước khi approve, đồng thời phân biệt bug thật với accepted change.

Nếu bạn đã nắm test case và expected result, hãy học thêm [các khóa học nền tảng cho Tester](https://t5edu.site/courses) rồi tự viết một visual test cho login page. Bạn sẽ bảo vệ component nào đầu tiên, và bằng chứng nào giúp bạn quyết định diff đó là bug?



## Hashtag
> visual-regression-testing, visual-testing, playwright, qa-beginner, software-testing
