# T5EDU AUTO-BLOG PLAYBOOK 2026

Đây là bộ quy tắc tối thượng cho workflow `/auto-blog` của T5Edu. Mọi bài viết được tạo ra (bao gồm cả lịch chạy tự động 07:00 hàng ngày) BẮT BUỘC tuân thủ 100% các điều khoản dưới đây.

## 1. TƯ TƯỞNG VÀ ĐỐI TƯỢNG
- **Phân bổ Intent**: Mỗi đợt chạy 2 bài BẮT BUỘC chia thành:
  1. **Bài A (Beginner-first thiết yếu)**: Chỉ phục vụ người mới, fresher, chuyển ngành. Tập trung kiến thức nền tảng, tránh topic chuyên sâu/phức tạp.
  2. **Bài B (Knowledge-first chuyên sâu)**: Chia sẻ kiến thức mới, kỹ thuật triển khai chuyên môn. Phải nêu rõ Prerequisite (Kiến thức cần có).
- **Giọng văn**: Chuyên nghiệp, mộc mạc, xưng "tester". Không emoji, không em-dash/en-dash.
- **Anti Wall-of-text**: Không để 3 đoạn văn liên tiếp mà không có hình ảnh/bảng/element. Đoạn văn 2-5 câu.

## 2. QUY TRÌNH BẮT BUỘC
1. **Search**: Nghiên cứu 2-3 nguồn độc lập (1-2 năm gần đây).
2. **Title**: Giới hạn cứng **TỐI ĐA 8 TỪ**. Xác nhận số từ trước khi viết.
3. **Slug**: Dạng `NN-slug.md`, kiểm tra không trùng trong repo `duongnguyen321/t5edublogs`.
4. **Internal Link**: Dùng canonical domain `https://t5edu.site` (3-7 link/bài). Verify route qua MCP.
5. **Nguồn**: Gắn inline anchor text. TUYỆT ĐỐI KHÔNG dùng citation số [1][2] và không có mục Nguồn tham khảo.
6. **Ảnh**: 1 cover (1:1) + ít nhất 3 ảnh nội dung (21:9). Alt là full English prompt theo T5Edu Art Direction.
7. **Reflect & Verify**: Đọc lại toàn bài, kiểm tra intent, accuracy, wall-of-text, custom elements, link đích và ảnh trước khi push.

## 3. CẤU TRÚC BÀI VIẾT (MARKDOWN)
1. `# [TIÊU ĐỀ]`
2. `![COVER PROMPT](IMAGE_PLACEHOLDER_COVER)`
3. Thân bài (H2 từ 5-7 mục).
4. `## Tổng kết` (Tóm tắt + CTA "Nếu... thì..." + Câu hỏi mở).
5. `## Hashtag` (Section cuối cùng). Dòng tiếp theo là blockquote: `> tag-1,tag-2,tag-3` (Tối đa 5 tag, không dấu #).

## 4. CUSTOM ELEMENTS GUIDE (7 LOẠI)
- **multiple-choice**: `<multiple-choice correct="A" select="single">`. Đáp án `- A: nội dung`.
- **table-testcase**: `<table-testcase cols="3" headers="ID|Step|Expected">`.
- **dropdown-content**: Title dòng 1, `>` mô tả, chi tiết TRONG đúng một fenced block ````markdown ... ````.
- **grid-content**: Title dòng 1, `>` mô tả, mỗi fenced block ````markdown ... ```` là một card (2-4 card).
- **sql-editor**: Chứa fenced block `sql`, dòng trước block là tên tab.
- **database-schema**: Chứa đúng một fenced block `dbml`.
- **code-runner**: `lang` (python/js/ts/java/sql), body là source thuần (không fenced block).

## 5. LƯU TRỮ (GITHUB ONLY)
- Push trực tiếp lên repo `duongnguyen321/t5edublogs` qua GitHub API.
- Xử lý gh: Strip mã màu ANSI `\x1b[0-9;]*m` và dùng `--input` file tạm.
