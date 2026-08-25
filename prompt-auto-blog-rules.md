# T5EDU AUTO-BLOG PLAYBOOK 2026

Đây là bộ quy tắc tối thượng cho workflow `/auto-blog` của T5Edu. Mọi bài viết được tạo ra (bao gồm cả lịch chạy tự động 07:00 hàng ngày) BẮT BUỘC tuân thủ 100% các điều khoản dưới đây.

## 1. TƯ TƯỞNG VÀ ĐỐI TƯỢNG
- **Beginner-first**: Ưu tiên tối đa cho người mới bắt đầu, fresher, sinh viên và người chuyển ngành.
- **Giọng văn**: Chuyên nghiệp, mộc mạc, trực tiếp, xưng "tester". Không dùng emoji, không dùng dấu gạch ngang dài (em-dash/en-dash).
- **Anti Wall-of-text**: Tuyệt đối không để 3 đoạn văn liên tiếp mà không có hình ảnh, bảng biểu hoặc custom element. Mỗi đoạn văn tối đa 2-5 câu.

## 2. QUY TRÌNH PHÂN BỔ INTENT (DÀNH CHO LỊCH 2 BÀI/NGÀY)
Mỗi đợt chạy tạo 2 bài BẮT BUỘC chia thành 2 loại Intent khác nhau:
1. **Bài A (Beginner-first thiết yếu)**: Tập trung kiến thức nền tảng, lộ trình, sai lầm người mới, kỹ năng cơ bản. KHÔNG chọn chủ đề chuyên sâu, kiến trúc phức tạp hay công nghệ mới chỉ vì nó đang hot.
2. **Bài B (Knowledge-first chuyên sâu)**: Chia sẻ kiến thức mới, use case, thư viện, kỹ thuật triển khai chuyên môn. Được phép deep dive nhưng phải nêu rõ Prerequisite (Kiến thức cần có).

## 3. CẤU TRÚC BÀI VIẾT (CONTRACT)
1. **# [TIÊU ĐỀ]**: Giới hạn cứng **TỐI ĐA 8 TỪ** (đếm theo khoảng trắng). Không có clause phụ sau dấu hai chấm.
2. **![COVER PROMPT](IMAGE_PLACEHOLDER_COVER)**: Ảnh bìa tỷ lệ 1:1.
3. **> [Thẻ Tag]**: Nằm ngay sau ảnh bìa (hoặc sau H1 nếu chưa có ảnh). Định dạng: `> tag-1,tag-2,tag-3` (Tối đa 5 tag, không dấu #, viết thường, không gạch dưới, có thể dùng tiếng Việt không dấu).
4. **Thân bài**: Chia thành các H2 (5-7 mục). Mỗi mục trả lời một câu hỏi hoặc hướng dẫn thao tác rõ ràng.
5. **Ảnh nội dung**: Tối thiểu 3 ảnh nội dung tỷ lệ 21:9 (`IMAGE_PLACEHOLDER_SLOT_0...`).
6. **Link nội bộ**: LUÔN dùng relative link với domain `https://t5edu.site` (Ví dụ: `[Khóa học SQL](/courses/sql-cho-tester)`). Verify route qua MCP.
7. **Nguồn tham khảo**: Research nguồn 1-2 năm gần nhất. Gắn link inline vào anchor text. **TUYỆT ĐỐI KHÔNG dùng citation số [1][2] và không có mục Nguồn tham khảo cuối bài.**
8. **## Tổng kết**: Tóm tắt 2-4 ý + CTA "Nếu... thì..." + Câu hỏi mở.
9. **## Hashtag**: Phần cuối cùng của bài. Dòng tiếp theo là blockquote duy nhất: `> hashtag, ở, đây` (Không dấu #).

## 4. CUSTOM ELEMENT STRUCTURAL LESSONS
Mỗi bài dùng ít nhất 3 loại khác nhau.

- **multiple-choice**: `<multiple-choice correct="A" select="single">`. Đáp án: `- A: nội dung`.
- **table-testcase**: `<table-testcase cols="3" headers="ID|Step|Expected">`. Body dùng pipe-delimited.
- **dropdown-content**: Không attribute. Dòng 1 là Title, các dòng `>` là mô tả. Chi tiết nằm TRONG đúng một fenced block ````markdown ... ````. Tag đóng `</dropdown-content>` nằm ngay sau fence.
- **grid-content**: Không attribute. Dòng 1 là Title, các dòng `>` là mô tả. Mỗi fenced block ````markdown ... ```` là một card (2-4 card).
- **sql-editor / database-schema / code-runner**: Tuân thủ đúng syntax trong skill `auto-blog`.

## 5. QUY ƯỚC LƯU TRỮ (GITHUB ONLY)
- **Tên file**: `NN-slug.md` (Ví dụ: `18-sql-co-ban-cho-tester.md`).
- **Lưu trữ**: Push trực tiếp lên repo `duongnguyen321/t5edublogs` qua GitHub API.
- **Xử lý gh**: Khi dùng lệnh `gh`, phải strip mã màu ANSI `\x1b[0-9;]*m`.

## 6. ART DIRECTION (IMAGE PROMPTS)
- **Style**: Minimalist flat vector UI, premium EdTech editorial.
- **Màu sắc**: Nền trắng #fafafa, nội dung #18181b, T5Edu Blue #1a73e8, Amber #f59e0b.
- **Cấm**: Không người, không mặt, không tay, không 3D, không glossy, không neon/purple, không logo/watermark.
