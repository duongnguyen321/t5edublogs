# Testcontainers Node.js: Integration Testing Với Dependency Thật

> Testcontainers Node.js giúp QA và developer chạy integration test với PostgreSQL, Redis hoặc service thật trong container disposable, giảm khoảng cách giữa test environment và production mà vẫn giữ được isolation.

![Minimalist flat vector UI design, premium professional EdTech editorial artwork, 1:1 square cover illustration of an experienced QA engineer's abstract testing workspace showing a Node.js test runner connected to a disposable PostgreSQL container, with a blue shield labeled Integration test and an amber badge labeled Real dependency. Clean bento-grid composition with strong negative space. Paper White or Zinc-50 background #fafafa. Zinc-900 content #18181b. T5Edu Blue accent #1a73e8. Amber highlight #f59e0b. Subtle one-pixel borders and restrained liquid-glass layers. Simple flat icons, geometric shapes, crisp short Vietnamese labels exactly Integration test, Real dependency, Cleanup, no people, no faces, no hands, no 3D, no photorealism, no purple, no violet, no pink, no neon, no logo, no watermark](https://files.manuscdn.com/user_upload_by_module/session_file/310519663091035343/pYFXupNtHmEZKYBs.png)

## Testcontainers Node.js giải quyết khoảng trống nào của integration testing?

- Bài này dành cho automation tester, QA engineer và developer đã có nền tảng TypeScript hoặc JavaScript async-await, Jest hoặc test runner tương đương, Docker, SQL/PostgreSQL và phân biệt được unit test, integration test với end-to-end test.
- Nếu bạn chưa biết các khái niệm đó, hãy bắt đầu từ [khóa học Tester và QA trên T5Edu](/courses) và các [bài blog testing nền tảng](/blogs) trước khi triển khai.
- **Testcontainers Node.js** là thư viện điều khiển container tạm thời trong lúc test chạy. Thay vì dùng mock hoặc database chung, test khởi động PostgreSQL thật, lấy connection URI, chạy assertion rồi dọn container theo lifecycle.
- Trang [Testcontainers chính thức](https://testcontainers.com/) mô tả mô hình này cho database, browser và nhiều dependency khác.
- Khoảng trống cần giải quyết là sự khác nhau giữa mock và dependency thật. Mock giúp unit test chạy nhanh, nhưng không phát hiện lỗi do SQL dialect, transaction, index, serialization, permission hoặc hành vi thật của database.
- Ngược lại, một integration test dùng database dùng chung dễ bị nhiễm data, phụ thuộc thứ tự chạy và khó tái hiện. Testcontainers đưa dependency thật vào một vòng đời cô lập hơn.

<multiple-choice correct="B" select="single">
Tình huống nào là lý do phù hợp nhất để thêm Testcontainers vào integration test?
- A: Muốn thay toàn bộ unit test bằng một suite chậm hơn
- B: Muốn kiểm tra repository với PostgreSQL thật thay vì chỉ tin vào mock
- C: Muốn bỏ hoàn toàn việc reset dữ liệu sau test
- D: Muốn test UI mà không cần browser
</multiple-choice>

## Prerequisite nào cần có trước khi dùng Testcontainers?

- Testcontainers không thay thế foundation của integration testing. Trước khi viết code, cần biết boundary, dependency thật, dữ liệu reset và điều kiện ready.
- Nếu chỉ copy `new PostgreSqlContainer().start()` mà không hiểu lifecycle, suite dễ gặp timeout, state leakage hoặc lỗi cleanup.
- Bốn prerequisite tối thiểu gồm:

| Prerequisite | Bạn cần làm được gì | Vì sao quan trọng |
| --- | --- | --- |
| JavaScript/TypeScript async | Đọc và viết `await`, promise, hook setup | Container start và client connection đều bất đồng bộ |
| Test runner | Dùng `beforeAll`, `afterAll`, timeout và assertion | Dependency phải được tạo trước test và dọn sau test |
| Docker | Có Docker environment mà Testcontainers hỗ trợ | Thư viện cần Docker daemon để pull image và chạy container |
| Database/API testing | Biết connection, schema, transaction và expected result | Container chỉ tạo môi trường, không tự thiết kế test oracle |

Với TypeScript, truyền connection URI và port động qua fixture hoặc test context, không hard-code cổng database local.

## Cấu trúc lifecycle của một Testcontainers integration test là gì?

- Một test có Testcontainers thường đi qua năm trạng thái: **declare**, **start**, **wait until ready**, **exercise**, và **stop**. Mỗi trạng thái có lỗi riêng.
- Declare sai image hoặc module làm test không khởi động. Start chưa hoàn tất mà client đã connect gây connection refused.
- Không stop khiến container và connection còn sót sau suite.
- Với PostgreSQL, lifecycle tối thiểu trong Jest có thể bắt đầu như sau:

```ts

- import { Client } from 'pg';
- import { PostgreSqlContainer, StartedPostgreSqlContainer } from '@testcontainers/postgresql';
- describe('customer repository integration', () => {
- let container: StartedPostgreSqlContainer;
- let client: Client;
- beforeAll(async () => {
- container = await new PostgreSqlContainer('postgres:16-alpine').start();
- client = new Client({ connectionString: container.getConnectionUri() });
- await client.connect();
- await client.query(`
- create table customers (
- id integer primary key,
- name text not null
- )
- `);
- }, 60_000);
- afterAll(async () => {
- await client.end();
- await container.stop();
- });
- it('reads the customer persisted in PostgreSQL', async () => {
- await client.query('insert into customers (id, name) values ($1, $2)', [1, 'Lan']);
- const result = await client.query('select id, name from customers where id = $1', [1]);
- expect(result.rows).toEqual([{ id: 1, name: 'Lan' }]);
- });
- });

```

[Hướng dẫn Docker cho Testcontainers Node.js](https://docs.docker.com/guides/testcontainers-nodejs-getting-started/) cũng dùng flow start container, lấy URI, chạy query rồi stop ở `afterAll`. Lần chạy đầu có thể pull image, nên timeout cần tính chi phí khởi động.

![Minimalist flat vector UI design, premium professional EdTech editorial artwork, 21:9 wide technical diagram of a Testcontainers Node.js lifecycle from left to right. Five connected cards labeled exactly Declare, Start, Ready, Exercise, and Cleanup, with a Node.js test runner above and a PostgreSQL container below. Use solid blue arrows between lifecycle steps and an amber gate labeled Wait strategy between Start and Ready. Clean horizontal bento-grid composition with strong negative space. Paper White or Zinc-50 background #fafafa. Zinc-900 content #18181b. T5Edu Blue accent #1a73e8. Amber highlight #f59e0b. Subtle one-pixel borders and restrained liquid-glass layers. Exact short Vietnamese labels only where needed, no people, no faces, no hands, no 3D, no photorealism, no purple, no violet, no pink, no neon, no logo, no watermark](https://files.manuscdn.com/user_upload_by_module/session_file/310519663091035343/XMseGimELPfwCWxb.png)

## Vì sao “container đã start” chưa có nghĩa database đã ready?

- Docker có thể báo container đang chạy trong khi PostgreSQL vẫn đang khởi tạo data directory, bind socket hoặc mở cổng.
- Nếu test gọi `client.connect()` ngay sau khi container process được tạo, kết quả có thể là connection refused hoặc authentication chưa sẵn sàng. Đây là khác biệt giữa **process liveness** và **service readiness**.
- Testcontainers Node.js có wait strategy để chờ điều kiện phù hợp. [Tài liệu containers của Testcontainers Node.js](https://node.testcontainers.org/features/containers/) cho thấy thư viện hỗ trợ Generic Container, environment variables, port exposure, log streaming và lifecycle operations.
- Với database module, nên ưu tiên module có wait strategy phù hợp; với service tự xây, có thể chờ port mở, log chứa thông báo ready hoặc HTTP endpoint trả status hợp lệ.
- Không nên dùng một `setTimeout(5000)` cố định làm wait strategy chính. Năm giây có thể dư ở máy này nhưng thiếu ở CI, còn khi service lỗi thật, test chỉ chậm thêm rồi mới fail.
- Một readiness check tốt trả lời được câu hỏi: “Dependency có chấp nhận request mà test sắp gửi chưa?”.

<grid-content>
Ba lớp readiness cần phân biệt
> Chờ đúng lớp giúp failure nói lên nguyên nhân thay vì chỉ báo timeout chung chung.

```markdown

**Process running**

Container process đã được Docker start. Trạng thái này chưa chứng minh database hoặc API đã nhận request.

```

```markdown

**Port reachable**

Cổng đã mở và TCP connect được. Service vẫn có thể đang migrate schema hoặc từ chối authentication.

```

```markdown

**Application ready**

Một health check hoặc query đơn giản trả kết quả hợp lệ. Đây mới là điều kiện phù hợp để bắt đầu assertion nghiệp vụ.

```

</grid-content>

## Mock, shared database và Testcontainers khác nhau ở đâu?

Không có một loại test environment đúng cho mọi tầng. Unit test nên dùng mock hoặc fake để kiểm tra logic nhanh.

Integration test nên dùng dependency thật ở boundary quan trọng. End-to-end test có thể dùng một môi trường gần production hơn, nhưng không nên biến mọi test thành E2E vì chi phí setup và triage lớn.

| Cách tiếp cận | Tín hiệu nhận được | Rủi ro chính | Nên dùng khi |
| --- | --- | --- | --- |
| Mock repository | Logic service và nhánh lỗi | Không phát hiện lỗi SQL hoặc mapping thật | Unit test, kiểm tra business logic |
| Shared database | Hành vi gần thật | Data leakage, thứ tự chạy, khó tái hiện | Smoke hoặc môi trường đã quản trị chặt |
| Testcontainers | Dependency thật, lifecycle cô lập | Cần Docker, start chậm hơn mock | Integration test trong local và CI |
| Database ephemeral managed | Môi trường thật do platform cung cấp | Chi phí, network và cleanup phức tạp | Suite lớn cần hạ tầng riêng |

- Dùng Testcontainers không có nghĩa là bỏ mock.
- Một repository có thể được kiểm tra bằng unit test với mock để bao phủ nhánh logic, sau đó có một nhóm integration test nhỏ với PostgreSQL thật để xác nhận query, schema và mapping.
- Hai lớp test trả lời hai câu hỏi khác nhau.
- Nếu bạn cần củng cố cách viết query gắn với rule nghiệp vụ, hãy xem [khóa học và nội dung Tester trên T5Edu](/courses), sau đó dùng Testcontainers để kiểm tra chính rule đó trên database thật.
- Đừng bắt đầu bằng việc tạo một container cho mọi test case nếu chưa biết test nào đang bảo vệ integration boundary.

## Làm isolation và cleanup thế nào để suite đáng tin cậy?

- Isolation có nhiều mức. Bạn có thể tạo một container cho mỗi test, một container cho mỗi test file hoặc một container cho toàn suite.
- Mỗi mức đổi tốc độ lấy độ độc lập. Container cho mỗi test mạnh về isolation nhưng chậm.
- Container cho toàn suite nhanh hơn nhưng bắt buộc reset schema và data giữa các test.
- Với suite nhỏ, `beforeAll` và `afterAll` là điểm bắt đầu rõ ràng. Nếu test sửa cùng bảng, hãy dùng transaction rollback hoặc truncate có kiểm soát.
- Nếu test chạy song song, tránh tên container và cổng cố định.
- [Tài liệu Node.js của Testcontainers](https://node.testcontainers.org/features/containers/) cảnh báo việc đặt tên container thủ công có thể gây conflict khi tên đã tồn tại; network alias là lựa chọn phù hợp hơn khi cần giao tiếp giữa container.
- Cleanup phải dọn cả client connection lẫn container. Nếu chỉ stop container mà quên `client.end()`, Jest có thể không thoát hoặc log lỗi open handle.
- Nếu chỉ đóng client, container vẫn giữ tài nguyên Docker; hãy xác nhận behavior remove theo API và runtime mà team đang dùng.
- Khi debug failure, bật log có chọn lọc và ghi lại image version, container logs, connection target đã mask, test name và test data version.

<table-testcase cols="4" rows="4" headers="ID|Rủi ro|Dấu hiệu|Cách kiểm tra">
| INT01 | Database chưa ready | Connection refused ở beforeAll | Kiểm tra wait strategy và container logs |
| INT02 | Data leakage | Test pass riêng nhưng fail khi chạy cả file | Reset schema hoặc transaction giữa test |
| INT03 | Cleanup thiếu | Jest báo open handle hoặc Docker còn container | Đóng client rồi stop container trong afterAll |
| INT04 | Image drift | Local pass, CI fail sau một thời gian | Pin image tag và ghi nhận version trong log |
</table-testcase>

![Minimalist flat vector UI design, premium professional EdTech editorial artwork, 21:9 wide integration testing comparison illustration with three columns labeled exactly Mock, Shared DB, and Testcontainers. Show a blue isolated container around a real PostgreSQL icon in the Testcontainers column, an amber warning around shared data in Shared DB, and a simple stub icon in Mock. Add a bottom decision strip labeled Unit, Integration, and E2E with arrows to the preferred approach. Clean horizontal bento-grid composition with strong negative space. Paper White or Zinc-50 background #fafafa. Zinc-900 content #18181b. T5Edu Blue accent #1a73e8. Amber highlight #f59e0b. Subtle one-pixel borders and restrained liquid-glass layers. Exact short English labels only, no people, no faces, no hands, no 3D, no photorealism, no purple, no violet, no pink, no neon, no logo, no watermark](https://files.manuscdn.com/user_upload_by_module/session_file/310519663091035343/UddHWlkTxFgSDIsn.png)

![Minimalist flat vector UI design, premium professional EdTech editorial artwork, 21:9 wide technical illustration for a Vietnamese T5Edu blog about Testcontainers Node.js integration testing. Show three horizontal isolation lanes labeled exactly Test, Container, and Cleanup. In the center lane, a blue Node.js test runner connects to a disposable PostgreSQL container with a small amber shield labeled Isolated. Add blue arrows from Start to Ready to Exercise to Cleanup, with a compact amber readiness gate between Start and Ready. Paper White or Zinc-50 background #fafafa, Zinc-900 content #18181b, T5Edu Blue accent #1a73e8, Amber highlight #f59e0b, subtle one-pixel borders, restrained liquid-glass layers, clean bento-grid composition, strong negative space, flat geometric icons. Exact short English labels only. No people, no faces, no hands, no purple, no violet, no pink, no neon, no photorealism, no 3D, no glossy plastic, no dramatic lighting, no logo, no watermark, no dense paragraphs](https://files.manuscdn.com/user_upload_by_module/session_file/310519663091035343/VMEbHCGsMqmVoxCj.png)

## Khi nào Testcontainers không phải lựa chọn tốt?

- Testcontainers phù hợp khi dependency thật là một phần của câu hỏi kiểm thử, nhưng không phải lúc nào cũng đáng dùng.
- Nếu chỉ kiểm tra hàm tính phí không chạm database, một container chỉ làm test chậm và khó debug. Nếu CI không có Docker environment được hỗ trợ, suite sẽ fail trước khi test application bắt đầu.
- Một số dependency có thể yêu cầu license, dữ liệu lớn, startup rất lâu hoặc cấu hình network phức tạp.
- Trong trường hợp đó, team có thể giữ một nhóm contract hoặc integration test chạy trên managed environment, còn Testcontainers bao phủ phần dependency nhỏ, quan trọng và tái tạo được.
- Quyết định nên dựa trên boundary, tín hiệu và chi phí, không dựa trên việc container đang là xu hướng.
- Cũng cần pin image version thay vì luôn dùng `latest`. Image thay đổi có thể làm schema, extension hoặc behavior khác đi mà không có commit trong repository.
- Khi nâng version, hãy coi đó là một thay đổi test environment cần review riêng, chạy smoke integration test và đọc release note của image hoặc module.

<dropdown-content>
Làm sao phân biệt lỗi application với lỗi test environment?
> Hãy tìm first abnormal signal trong lifecycle thay vì chỉ nhìn error cuối.

```markdown

Nếu container không start, image pull fail hoặc readiness timeout, ưu tiên kiểm tra Docker daemon, image version, resource và wait strategy. Nếu container ready, query chạy được nhưng kết quả sai, khi đó mới điều tra application, schema, transaction hoặc test data. Log nên ghi rõ lifecycle phase, không gom mọi lỗi thành “integration test failed”.

```

</dropdown-content>

## Thiết kế một suite Testcontainers Node.js có giá trị thế nào?

Bắt đầu bằng một integration boundary có rủi ro thật, chẳng hạn repository lưu customer vào PostgreSQL. Sau đó chọn test theo ma trận sau:

| Loại test | Mục tiêu | Tín hiệu cần kiểm tra |
|---|---|---|
| Happy path | Xác nhận kết nối và mapping | Record được lưu và đọc đúng |
| Negative | Kiểm tra constraint hoặc input lỗi | Error message và trạng thái transaction |
| Transaction/query | Bảo vệ logic quan trọng | Rollback, isolation hoặc query result |

Trước khi mở rộng suite, hãy kiểm tra bốn điểm:

- Thời gian setup có chấp nhận được không?
- Test có fail do environment nhiều hơn do behavior không?
- Mỗi test có chạy độc lập không?
- Failure evidence có chỉ ra query, schema, readiness hay Docker không?

Một suite tốt không phải suite có nhiều container nhất. Đó là suite mà dependency thật, dữ liệu, readiness, cleanup và evidence đều được nói rõ trong từng test.

- Củng cố async-await và xử lý data: học [JavaScript cho QA trên T5Edu](/courses/javascript-cho-qa-engineer) trước khi tổ chức fixture.
- Ghi lại decision về mock, dependency thật và mức isolation: dùng [blog testing của T5Edu](/blogs) sau khi integration test đã ổn định.

## Tổng kết

- Testcontainers Node.js cho phép integration test nói chuyện với dependency thật trong container disposable, nhưng không thay thế unit test hoặc test oracle.
- Lifecycle cần tách rõ declare, start, readiness, exercise và cleanup; container chạy chưa đồng nghĩa service đã sẵn sàng.
- Mock, shared database và Testcontainers phục vụ các lớp kiểm thử khác nhau. Hãy chọn theo boundary và tín hiệu cần kiểm tra.
- Pin image version, reset dữ liệu, tránh tên và cổng cố định, đóng client rồi stop container để suite có thể tái hiện trong local và CI.

Nếu bạn đã có nền tảng Docker, TypeScript và database testing, hãy học thêm [nội dung QA và Tester theo khóa học](/courses) rồi chọn một repository boundary để chuyển từ mock sang PostgreSQL thật.

Dependency nào trong hệ thống của bạn đang tạo rủi ro lớn nhất nếu chỉ test bằng mock?

## Hashtag
> Testcontainers, Node.js testing, integration testing, Docker, QA automation
