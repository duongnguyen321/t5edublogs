# T5Edu Auto-Blog Playbook

Đây là lịch chạy hằng ngày lúc 07:00 giờ Việt Nam, timezone Asia/Saigon. Mỗi lần chạy tạo đúng 2 bài blog tiếng Việt về hot topic mới trong software testing/QA, nhưng bắt buộc chia thành 2 intent khác nhau: đúng 1 bài beginner-first thiết yếu cho người mới và đúng 1 bài knowledge-first chuyên sâu về kiến thức mới.

## QUY TRÌNH BẮT BUỘC

1. Search Internet để chọn 2 topic mới trong 1-2 năm gần đây, ưu tiên tài liệu chính thức, tổ chức chuyên môn, Ministry of Testing, TestGuild, Applitools, OpenTelemetry, tin automation/AI testing và nguồn có dữ kiện kiểm chứng. Mỗi topic nghiên cứu ít nhất 2-3 nguồn độc lập. Không dùng nguồn quá cũ nếu không cần cho định nghĩa nền tảng.

2. Phân bổ topic bắt buộc, không được tạo cả 2 bài cho người mới:
   - Bài A, beginner-first thiết yếu: chỉ phục vụ người mới vào ngành tester, mới học, intern, fresher, mới đi làm hoặc chuyển ngành. Chọn chính xác kiến thức nền tảng họ thật sự cần để hiểu, thực hành hoặc tránh lỗi phổ biến trong công việc đầu tiên. Không chọn protocol, thư viện mới, framework nâng cao, CI/CD, distributed systems, LLM evaluation, kiến trúc, userflow phức tạp, use case chuyên sâu hoặc xu hướng hot chỉ vì mới. Nếu topic cần prerequisite chuyên môn thì không được dùng cho Bài A. SEO của Bài A phải bám search intent nền tảng, không dùng kiến thức chuyên sâu làm trung tâm.
   - Bài B, knowledge-first chuyên sâu: dành cho chia sẻ kiến thức mới, ý tưởng, thư viện, use case, userflow, kỹ thuật triển khai hoặc chủ đề chuyên môn. Được phép deep dive, phải khóa audience có nền tảng phù hợp, nêu prerequisite và reader outcome rõ ràng.
   Trước khi viết phải khóa nội bộ cho từng bài: article_intent, primary_audience, subject, reader_outcome, prerequisites và lý do topic phù hợp. Nếu chưa tìm được đúng một topic thiết yếu cho người mới và một topic chuyên sâu có giá trị mới, tiếp tục nghiên cứu, không lấp chỗ bằng hai topic cùng audience.

3. Title là giới hạn cứng: mọi title chính thức tối đa 8 từ, đếm theo khoảng trắng. Áp dụng cho H1, SEO title, meta title, title trên card/cover và mọi metadata có tên title. Phải đếm và xác nhận trước khi viết, trước khi tạo ảnh và trước khi publish; nếu vượt 8 từ thì rút gọn, không được tiếp tục.

4. Chọn slug theo NN-slug.md, NN là số cao nhất cộng 1 trong repo duongnguyen321/t5edublogs. Kiểm tra slug không trùng trước khi viết.

5. Viết Markdown tiếng Việt 1500-2800 từ/bài theo file rule này và skill auto-blog. Bài A phải beginner-first thật sự, dùng thuật ngữ, ví dụ và prerequisite phù hợp người mới, không viết bản rút gọn của bài B. Bài B được chuyên sâu và phải nêu prerequisite. Không tác giả, không excerpt ngoài quy tắc đã duyệt, không citation số, không mục Nguồn tham khảo trừ khi được yêu cầu, không em dash, en dash hoặc emoji. Hashtag chỉ được xuất hiện một lần ở cuối bài trong section `## Hashtag`; ngay dòng kế tiếp bắt buộc là một blockquote duy nhất theo dạng `> hashtag, ở, đây`, tối đa 5 tag, không dùng ký hiệu `#`, không được đặt ở đầu bài, trong H1, excerpt, trước cover hoặc giữa các section. Dùng internal link với đúng canonical domain `https://t5edu.site`, không dùng domain khác, không đoán route, mỗi bài 3-7 backlink có ngữ cảnh và phải verify route đích.

6. Ưu tiên kiến thức truyền tải. Mỗi H2 phải trả lời câu hỏi hoặc giúp tester thực hiện thao tác rõ ràng. Không ép hai bài dùng cùng một cấu trúc. Chỉ dùng roadmap khi topic thật sự cần trình tự học. Custom element phải phục vụ learning objective, không chèn để đủ số lượng.

7. Custom element chỉ dùng khi tăng learning value hoặc interaction, luôn đóng đúng tag, không tự bịa element/attribute và không bọc Markdown thông thường bằng tag. Chỉ được dùng 7 element sau theo guide chính xác:
   - `multiple-choice` bắt buộc `correct="A"` và `select="single"`, hoặc `correct="A,C"` và `select="multiple"`; mỗi đáp án là một dòng `- KEY: nội dung`, key đúng một chữ cái A-Z.
   - `table-testcase` dùng `headers` phân cách bằng `|`, `cols` bằng số header, mỗi body row pipe-delimited đúng số ô, không thêm hàng header Markdown hoặc `---`; `rows` là số dòng khởi tạo khi body trống.
   - `dropdown-content` không có attribute; dòng không rỗng đầu tiên là title, các dòng `>` ngay sau là mô tả tùy chọn, chi tiết nằm trong đúng một fenced block `markdown`.
   - `grid-content` không có attribute; dòng đầu là title, các dòng `>` ngay sau là mô tả tùy chọn, mỗi fenced block `markdown` tạo đúng một card, 2-4 card.
   - `sql-editor` dùng cho SQL/database, body có ít nhất một fenced block `sql`; dòng không rỗng ngay trước block là tên tab tùy chọn, mỗi block là một tab theo thứ tự; chỉ dùng schema context được cung cấp, không hướng dẫn `DROP DATABASE` hoặc `TRUNCATE TABLE`.
   - `database-schema` chứa đúng một fenced block `dbml`, quan hệ dùng `Ref: table.column > table.column`.
   - `code-runner` bắt buộc `lang` là `python`, `js`, `ts`, `java` hoặc `sql`; body là source thuần, tuyệt đối không fenced code block.
   Markdown thông thường dùng bảng Markdown, blockquote cho tip và fenced `mermaid` khi cần. Không đặt element để trang trí.

8. Ảnh gồm 1 cover 1:1 và ít nhất 3 ảnh nội dung 21:9, alt là full English prompt tự chứa theo đúng T5Edu art direction: minimalist flat vector UI design, premium professional EdTech editorial artwork, clean bento-grid composition, strong negative space, Paper White/Zinc-50 background #fafafa, Zinc-900 #18181b, T5Edu Blue #1a73e8, Amber #f59e0b, subtle one-pixel borders, restrained liquid-glass layers, simple flat icons and geometric blocks, clean connector lines, no people/faces/hands, no 3D, no glossy plastic, no photorealism, no dramatic lighting, no purple/violet/pink/neon, no logo or watermark. Mỗi prompt phải mô tả đúng layout, vật thể, quan hệ, nhãn tiếng Việt ngắn nếu có, màu HEX, hướng mũi tên và negative constraints; không dùng prompt chung chung hoặc `same style as above`. Ngay sau khi viết xong và có prompt, tạo ảnh bằng gpt-image-2 default, upload CDN và thay toàn bộ placeholder trong Markdown. Không để placeholder trong bài phát hành.

9. Trước khi tạo ảnh hoặc push, bắt buộc thực hiện REFLECT độc lập cho từng bài: đọc lại toàn bài và kiểm tra đúng intent, accuracy, completeness, beginner-first riêng cho Bài A, prerequisite và độ sâu riêng cho Bài B, focus, không lan man, không wall-of-text, custom element, image prompt/ảnh, CTA, tag và Markdown. Nếu phát hiện lỗi thì sửa rồi reflect lại.

10. VERIFY link trước khi publish: mở hoặc gọi kiểm tra từng internal route T5Edu bằng trang đích/MCP; kiểm tra từng external content URL, xác nhận nội dung đúng chủ đề và dùng canonical URL sau redirect. Không dùng route nội bộ đoán từ title. Kiểm tra CDN image URL trả về thành công.

11. Sau reflect và link verification, chạy validator cuối. Push bằng Git Data API qua gh api blobs, trees, commits và PATCH refs/heads/main; khi subprocess gh phải strip ANSI `\x1b[0-9;]*m` và dùng `--input` file tạm. Mỗi lần chạy chỉ commit đúng 2 file blog, không commit script, reflect, research hoặc file phụ.

12. Báo cáo slug, file, intent của từng bài, tóm tắt 2 bài, link commit và kết quả reflect/link verification. Xác nhận domain sử dụng là `https://t5edu.site`, hashtag ở cuối bài theo đúng blockquote contract và mọi image prompt đã qua style check.

## LESSON LEARNED BẮT BUỘC SAU SỰ CỐ CUSTOM ELEMENT

A. Với grid-content, không có attribute; dòng đầu là title, blockquote ngay sau là mô tả tùy chọn, mỗi fenced block markdown là đúng một card. Mỗi grid phải có từ 2 đến 4 fenced block markdown, không gộp toàn bộ nội dung vào một block.

B. Với dropdown-content, không có attribute; dòng đầu là title, blockquote ngay sau là mô tả tùy chọn, phần chi tiết phải nằm trong đúng một fenced block markdown. Không đặt prose chi tiết trực tiếp ngoài fence.

C. Trước reflect và publish, phải chạy structural validator cho từng custom element: tag mở/đóng khớp, không có attribute lạ, grid có 2-4 card, dropdown có đúng 1 markdown fence, và không bọc toàn bộ raw tag trong code fence. Nếu fail thì sửa và chạy lại trước khi tạo commit.

Daily 07:00 Asia/Saigon. Generate exactly two Vietnamese testing/QA blogs with one beginner-first and one knowledge-first intent. Apply the custom element lessons in the playbook before reflect, validation, and publish.
