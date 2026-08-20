# Mutation Testing với StrykerJS

> Mutation testing bổ sung một câu hỏi quan trọng cho code coverage: test suite có thật sự phát hiện được lỗi trong code hay chỉ đi qua các dòng lệnh?

![Square 1:1 educational editorial illustration for T5Edu about mutation testing with StrykerJS in a JavaScript and TypeScript project. Minimalist flat vector UI design, premium professional EdTech editorial artwork, clean bento-grid composition with strong negative space, Paper White background #fafafa, Zinc-900 content #18181b, T5Edu Blue accent #1a73e8, Amber highlight #f59e0b, subtle one-pixel borders, rounded cards. Show a split testing laboratory: on the left a clean TypeScript function card labeled exactly “Production code”, in the center a small mutation spark icon and three mutant cards labeled “Killed”, “Survived”, and “No coverage”, on the right a test report card labeled exactly “Mutation score”. Use short Vietnamese labels only, no long paragraphs, no vendor logo, no realistic terminal screenshot, no gradients, no 3D, no photorealism, no clutter.](https://files.manuscdn.com/user_upload_by_module/session_file/310519663091035343/SbQgCjoRBKDlzhnp.png)

Code coverage cho biết test đã đi qua bao nhiêu phần của code. Nó chưa cho biết test có phát hiện được thay đổi làm sai behavior hay không.

| Câu hỏi | Code coverage | Mutation testing |
|---|---|---|
| Đo điều gì? | Vùng code đã được chạy | Khả năng test phát hiện thay đổi sai |
| Ví dụ rủi ro | 90% line coverage nhưng điều kiện chưa được kiểm tra đủ | Đảo điều kiện tạo mutant, test vẫn PASS |
| Kết quả cần đọc | Coverage percentage | Killed, survived hoặc no coverage |

Mutation testing tạo thay đổi nhỏ, có chủ đích trong production code rồi chạy test trên từng mutant. Nếu test thất bại, mutant bị **killed**; nếu test vẫn PASS, test suite có thể đang thiếu một assertion quan trọng.

**Prerequisite của bài:**

- Biết JavaScript hoặc TypeScript.
- Đã viết unit test.
- Biết cách chạy test trong CI.

Mục tiêu là hiểu mental model của mutation testing và thiết kế một lần chạy StrykerJS có giá trị, không biến mutation score thành KPI mù quáng.

## Mutation testing đang đo điều gì?

Một mutant là phiên bản của code thật sau khi công cụ áp dụng một thay đổi được gọi là mutation operator.

Ví dụ, biểu thức `total >= limit` có thể được đổi thành `total > limit`, hoặc `return isValid` có thể bị đổi thành `return true`.

Đây không phải lỗi thật được đưa vào production. Nó là phép thử giả lập để xem test suite có đủ nhạy với thay đổi đó không.

Nếu một test fail khi chạy trên mutant, mutant bị **kill**. Nếu toàn bộ test vẫn pass, mutant **survives**.

Một mutant sống sót có thể chỉ ra test thiếu assertion, thiếu case biên hoặc đang kiểm tra implementation quá hời hợt.

Tuy nhiên, nó cũng có thể là equivalent mutant, tức thay đổi nhìn khác về cú pháp nhưng không làm thay đổi hành vi có thể quan sát.

| Kết quả | Ý nghĩa thực tế | Việc nên làm |
|---|---|---|
| Killed | Test suite phát hiện thay đổi này | Kiểm tra test nào đã kill và giữ regression value |
| Survived | Test suite không phân biệt được code thật và mutant | Xem lại assertion, input và scenario |
| No coverage | Không có test chạy qua vùng code đó | Bổ sung test hoặc loại vùng code khỏi phạm vi hợp lý |
| Timeout | Mutant làm test chạy quá lâu hoặc bị treo | Kiểm tra test async, dependency và timeout policy |
| Equivalent | Thay đổi không tạo khác biệt hành vi | Đánh dấu hoặc loại khỏi phân tích nếu có căn cứ |

Theo [tài liệu giới thiệu StrykerJS](https://stryker-mutator.io/docs/stryker-js/introduction/), mutation testing tạo mutant trong code rồi chạy test để xem mutant nào bị kill hoặc sống sót. Mutation score vì vậy không phải phần trăm line coverage được đổi tên.

Nó là một góc nhìn khác về khả năng test suite phát hiện thay đổi có hại.

![Wide 21:9 educational diagram explaining the mutation testing loop in StrykerJS. Use a left-to-right flow with four large cards and arrows: “Code thật” arrow to “Tạo mutant” arrow to “Chạy test” arrow to “Kill hoặc survive”. Under the final card show two branches labeled exactly “Test fail = Killed” in blue and “Test pass = Survived” in amber. Include a small TypeScript function card with one condition being changed, but show no long code. Minimalist flat vector UI design, premium professional EdTech editorial artwork, clean bento-grid composition with strong negative space, Paper White #fafafa background, Zinc-900 #18181b text, T5Edu Blue #1a73e8 arrows and borders, Amber #f59e0b for surviving mutant branch, subtle one-pixel borders, rounded cards, exact Vietnamese labels only, no logos, no photorealism, no 3D, no gradients, no clutter.](https://files.manuscdn.com/user_upload_by_module/session_file/310519663091035343/uaAQNaKniFKvogDC.png)

## Vì sao code coverage chưa đủ?

Line coverage cho biết test đã chạy qua dòng code. Branch coverage cho biết các nhánh nhất định đã được đi qua.

Cả hai đều hữu ích, nhưng vẫn có thể đạt cao khi assertion không kiểm tra kết quả quan trọng.

Ví dụ hàm sau có thể được gọi trong test nhưng assertion chỉ kiểm tra hàm không throw exception:

```ts

export function canCheckout(total: number, stock: number): boolean {

  return total > 0 && stock > 0;

}

```

Một test gọi `canCheckout(100, 5)` và chỉ assert rằng hàm chạy thành công có thể tạo coverage tốt cho dòng và nhánh, nhưng chưa chứng minh `false` được trả về khi `stock` bằng 0.

Nếu Stryker đổi `stock > 0` thành `stock >= 0` mà test vẫn pass, mutant sống sót nhắc team rằng boundary assertion đang thiếu.

Điều này không có nghĩa code coverage vô dụng. Coverage giúp tìm vùng chưa được chạy.

Mutation testing giúp tìm vùng đã chạy nhưng test chưa đủ sức phân biệt hành vi đúng và hành vi bị thay đổi.

Hai chỉ số bổ sung cho nhau, không nên dùng một chỉ số để phủ nhận chỉ số còn lại.

<grid-content>
Các điểm cần nhớ
> Coverage hỏi gì?

```markdown

**Coverage hỏi gì?**

Test đã đi qua dòng, function hoặc branch nào?

```

> Mutation hỏi gì?

```markdown

**Mutation hỏi gì?**

Test có phát hiện khi hành vi trong vùng đó bị thay đổi không?

```

> Coverage cao, assertion yếu

```markdown

**Coverage cao, assertion yếu**

Code được chạy nhưng kết quả quan trọng chưa được xác nhận.

```

> Mutation score thấp

```markdown

**Mutation score thấp**

Có thể cần thêm boundary case, negative case hoặc assertion cụ thể.

```

</grid-content>

## Chuẩn bị project JavaScript hoặc TypeScript

StrykerJS cần một test runner mà project đang dùng, chẳng hạn Jest, Mocha hoặc Vitest thông qua adapter phù hợp. Trước khi bật mutation testing, unit test thông thường phải chạy ổn định và có thể chạy lặp lại.

Nếu test đang flaky, phụ thuộc mạng hoặc dùng dữ liệu thay đổi theo thời gian, mutation run sẽ tạo ra nhiều tín hiệu khó phân biệt.

[Tài liệu cấu hình StrykerJS](https://stryker-mutator.io/docs/stryker-js/configuration/) mô tả package, cấu hình, test runner integration và reporter cho project JavaScript hoặc TypeScript. Với project TypeScript, hãy xác định rõ test chạy trên source TypeScript trực tiếp hay trên output đã build.

Sai khác giữa `src`, `dist`, alias module và source map có thể khiến báo cáo khó đọc hoặc mutate nhầm file.

Một cách khởi đầu an toàn là giới hạn mutate vào một module có logic nghiệp vụ rõ. Không nên mutate toàn bộ monorepo ngay lần đầu.

Hãy chọn một module có test hiện hữu, thời gian chạy chấp nhận được và có boundary logic để kết quả giúp team học được điều gì đó.

Cấu hình tối thiểu thường cần trả lời bốn câu hỏi:

| Câu hỏi | Ví dụ quyết định |
|---|---|
| Mutate file nào? | Chỉ `src/pricing/**/*.ts` |
| Test command nào? | Lệnh unit test ổn định của project |
| Loại coverage nào? | Bắt đầu với perTest hoặc all phù hợp runner |
| Reporter nào? | HTML để đọc chi tiết, text để CI log |

Không copy nguyên một cấu hình trên mạng rồi coi là hoàn thành. Hãy đọc lại glob và chạy thử để xác nhận báo cáo chỉ chứa file mong muốn.

![Wide 21:9 educational workspace illustration for configuring StrykerJS in a TypeScript repository. Show a clean three-column layout: left card labeled exactly “src/pricing”, center card labeled “stryker.config”, right card labeled “Mutation report”. Connect them with blue arrows. Add small configuration chips labeled “mutate”, “testRunner”, and “reporters”. Use only short Vietnamese or technical labels, no long code, no terminal logo, no brand logo. Minimalist flat vector UI design, premium professional EdTech editorial artwork, clean bento-grid composition, strong negative space, Paper White #fafafa, Zinc-900 #18181b, T5Edu Blue #1a73e8, Amber #f59e0b, subtle one-pixel borders, rounded cards, no gradients, no 3D, no photorealism, no clutter.](https://files.manuscdn.com/user_upload_by_module/session_file/310519663091035343/zClWEoarpJxoLElb.png)

## Đọc surviving mutant thay vì chỉ nhìn score

Mutation score là một tín hiệu tổng hợp, nhưng hành động hữu ích nhất thường bắt đầu từ danh sách surviving mutant. Mở từng mutant và hỏi: thay đổi đó có thể đại diện cho bug thật không?

Test nào đáng lẽ phải fail? Assertion hiện tại đang kiểm tra output, state, side effect hay chỉ kiểm tra code không ném exception?

Hãy phân loại surviving mutant thành bốn nhóm. Nhóm thứ nhất là **missing test**, khi không có scenario cho hành vi đó.

Nhóm thứ hai là **weak assertion**, khi test chạy đúng flow nhưng assertion quá rộng. Nhóm thứ ba là **test coupling**, khi test phụ thuộc implementation nên không chạm được behavior cần thiết.

Nhóm cuối là **equivalent hoặc không đáng xét**, khi mutant không thể tạo khác biệt quan sát được hoặc nằm trong code không thuộc risk hiện tại.

Một workflow triage có thể ghi lại thông tin sau:

<table-testcase cols="5" rows="4" headers="Mutant|Thay đổi|Chẩn đoán|Hành động|Ưu tiên">
| M01 | Đổi > thành >= | Boundary assertion thiếu | Thêm test total bằng limit | Cao |
| M02 | Xóa nhánh lỗi | Không có negative scenario | Thêm test input không hợp lệ | Cao |
| M03 | Đổi tên biến | Equivalent mutant | Đánh dấu không actionable | Thấp |
| M04 | Đổi giá trị mặc định | Test thiếu state khác | Bổ sung fixture và assertion | Trung bình |
</table-testcase>

Không nên sửa test chỉ để giết mọi mutant bằng mọi giá. Một test mới cần thể hiện behavior hoặc risk mà team thực sự muốn bảo vệ.

Nếu chỉ thêm assertion vào implementation detail để làm score tăng, test suite có thể trở nên brittle mà không tăng khả năng phát hiện lỗi thực tế.

## Tối ưu thời gian chạy trong CI

Mutation testing thường tốn thời gian hơn unit test thông thường vì có nhiều mutant và mỗi mutant có thể kích hoạt một phần hoặc toàn bộ test suite.

StrykerJS có các cơ chế test selection và coverage analysis để giảm số test không liên quan cần chạy, như được mô tả trong [tài liệu tối ưu hóa của StrykerJS](https://stryker-mutator.io/docs/stryker-js/guides/).

Tuy vậy, tối ưu chỉ có ý nghĩa sau khi baseline đã đúng.

Trong CI, team có thể tách hai mức kiểm tra. Pull request chạy phạm vi nhỏ trên module vừa thay đổi, dùng để phản hồi nhanh.

Scheduled job hoặc pipeline chính chạy phạm vi rộng hơn, lưu HTML report và theo dõi các surviving mutant mới.

Đừng đặt một global threshold cao ngay khi project chưa có baseline, vì team sẽ dễ tìm cách né quality gate thay vì hiểu nguyên nhân.

Một policy thực tế có thể bắt đầu bằng việc không cho phép **giảm** mutation score của module đã có baseline, đồng thời bắt buộc triage các mutant mới liên quan đến logic nghiệp vụ.

Threshold chỉ nên là một phần của policy. Code review vẫn cần xem test có thể hiện requirement hay không, còn mutation report chỉ cung cấp thêm bằng chứng.

<multiple-choice correct="C" select="single">
Khi một mutant survive, hành động đầu tiên có giá trị nhất là gì?
- A: Xóa ngay mutant khỏi báo cáo
- B: Tăng threshold toàn project
- C: Đọc thay đổi của mutant và kiểm tra test assertion liên quan
- D: Viết một test bất kỳ để tăng score
</multiple-choice>

## Giới hạn và cách dùng đúng mutation score

Mutation testing không chứng minh hệ thống không có bug.

Bộ mutation operator không bao phủ mọi loại lỗi, equivalent mutant có thể gây nhiễu, còn test suite tốt vẫn cần kiểm tra integration, contract, security, performance và các rủi ro ngoài unit boundary.

Mutation score cũng không nên dùng để so sánh máy móc giữa hai repository khác nhau. Một module có logic đơn giản và một module xử lý nhiều side effect sẽ có profile mutant khác nhau.

Hãy xem score trong bối cảnh lịch sử của chính module, danh sách mutant bị survive và risk của thay đổi.

Nếu team mới bắt đầu, hãy triển khai theo trình tự: chọn một module nhỏ, ghi baseline, đọc report bằng tay, triage một nhóm mutant, thêm test có lý do, rồi mới cân nhắc CI gate.

Cách này biến mutation testing thành hoạt động cải thiện feedback loop thay vì một cuộc thi phần trăm.

<dropdown-content>
FAQ về StrykerJS
> Mutation testing có thay thế code coverage không?

```markdown

Không. Coverage cho biết vùng code đã được chạy; mutation testing kiểm tra test suite có phát hiện một số thay đổi có chủ đích hay không. Hai kỹ thuật bổ sung cho nhau.

```

> Có cần giết tất cả mutant không?

```markdown

Không nhất thiết. Equivalent mutant và mutant ngoài risk hoặc scope có thể được triage, loại trừ có lý do hoặc ghi nhận riêng. Mục tiêu là tăng khả năng phát hiện lỗi, không phải tối đa hóa điểm số.

```

> Có nên chạy mutation testing ở mọi pull request không?

```markdown

Tùy kích thước project và thời gian chạy. Có thể chạy phạm vi nhỏ trong pull request và phạm vi rộng theo lịch, miễn là policy, baseline và cách triage được ghi rõ.

```

</dropdown-content>

## Tổng kết

- Mutation testing tạo thay đổi có chủ đích để kiểm tra test suite có phát hiện được thay đổi đó hay không.
- `Killed`, `Survived`, `No coverage`, `Timeout` và `Equivalent` là các trạng thái cần được đọc khác nhau.
- StrykerJS nên bắt đầu trên một module nhỏ, có unit test ổn định và cấu hình mutate rõ ràng.
- Mutation score chỉ là tín hiệu. Surviving mutant, risk và chất lượng assertion mới quyết định hành động tiếp theo.

Nếu bạn đã có một project JavaScript hoặc TypeScript với unit test ổn định, hãy chạy StrykerJS trên một module nghiệp vụ nhỏ và triage năm surviving mutant đầu tiên.

Trong số đó, có bao nhiêu mutant chỉ ra test thiếu thật sự và bao nhiêu mutant là equivalent hoặc ngoài scope?

## Hashtag
> mutation testing, StrykerJS, JavaScript testing, TypeScript testing, test quality
