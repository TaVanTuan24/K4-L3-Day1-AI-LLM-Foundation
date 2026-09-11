# K4 — Ngày 1: Bài Tập & Phản Ánh
## Khám Phá LLM API | Phiếu Thực Hành

**Thời lượng:** 4 tiếng
**Cách làm:** Trả lời từng câu ngay sau khi hoàn thành block tương ứng —
đừng để dồn hết về cuối buổi. Thay dòng `*Câu trả lời của bạn*` bằng câu
trả lời thật (chấm tự động sẽ đếm số câu đã trả lời).

---

## Block 1 — API Cơ Bản (trả lời sau Checkpoint 1)

### Câu 1.1 — Độ nhạy của temperature
Gọi `call_openai` với temperature 0.0, 0.5, 1.0 và 1.5 dùng prompt
**"Hãy kể cho tôi một sự thật thú vị về Việt Nam."**

**Bạn nhận thấy quy luật gì qua bốn phản hồi?** (2–3 câu)
> Chủ đề trả lời gần như luôn là Hang Sơn Đoòng (sự thật có xác suất cao nhất trong dữ liệu huấn luyện), nhưng mức đa dạng tăng dần theo temperature: ở 0.0, hai lần chạy liên tiếp cho ra phản hồi gần như y hệt nhau (cùng ~1691 ký tự, cùng cấu trúc 4 mục) — output gần như deterministic; ở 0.5 và 1.0, chủ đề giữ nguyên nhưng câu chữ, thứ tự mục, ví dụ phụ (Great Wall of Vietnam, giới hạn ~1.000 khách/năm...) và độ dài khác nhau rõ mỗi lần chạy (1340–1702 ký tự); ở 1.5 câu văn thêm phóng khoáng và chi tiết nhỏ bắt đầu nhấp nhô (năm phát hiện 1990 hay 1991). Quy luật: temperature không đổi "model biết gì" mà chỉ đổi mức chấp nhận rủi ro khi chọn token ít phổ biến tiếp theo.

### Câu 1.2 — Chọn temperature cho sản phẩm
**Bạn sẽ đặt temperature bao nhiêu cho chatbot hỗ trợ khách hàng, và tại sao?**
> Tôi sẽ đặt temperature thấp, khoảng 0.1–0.3. Chatbot hỗ trợ khách hàng ưu tiên độ chính xác và nhất quán: cùng một câu hỏi phải cho câu trả lời giống nhau, bám đúng chính sách, không bịa chính sách hay khuyến mãi không tồn tại — temperature cao làm tăng nguy cơ "sáng tạo" sai sự thật. Chỉ riêng phần soạn thư giới thiệu sản phẩm/nội dung marketing mới đáng nhích lên 0.7–0.9 vì cần câu chữ đa dạng hơn.

### Câu 1.3 — Đánh đổi chi phí
Kịch bản: 10.000 người dùng hoạt động mỗi ngày, mỗi người gọi API 3 lần,
mỗi lần trung bình ~350 token đầu ra.

**Ước tính GPT-4o đắt hơn GPT-4o-mini bao nhiêu lần cho workload này? Nêu một
trường hợp GPT-4o xứng đáng với chi phí và một trường hợp nên dùng mini:**
> Workload: 10.000 người × 3 lượt/ngày × ~350 token đầu ra = 10,5 triệu token output/ngày. Theo bảng giá: GPT-4o ≈ $105/ngày (~$3.150/tháng) so với GPT-4o-mini ≈ $6,30/ngày (~$189/tháng) — tức GPT-4o đắt hơn khoảng 16,7 lần (giá output $0.010 vs $0.0006 mỗi 1K token). GPT-4o xứng đáng cho các tác vụ suy luận phức tạp, sai một lần tốn kém: phân tích hợp đồng/tài chính, giải quyết khiếu nại khó, lý giải rủi ro. GPT-4o-mini phù hợp cho các tác vụ khối lượng lớn, sai một câu không gây hậu quả lớn: trả FAQ, phân loại/tuyến ticket, tóm tắt, dịch đơn giản.

---

## Block 2 — System Prompt & Token (trả lời sau Checkpoint 2)

### Câu 2.1 — Sức mạnh của persona
Gọi `chat_with_system_prompt` hai lần với cùng câu hỏi
**"Giải thích blockchain là gì?"** nhưng hai system prompt khác nhau:
- "Bạn là giáo viên tiểu học, giải thích thật đơn giản cho trẻ 8 tuổi."
- "Bạn là chuyên gia tài chính, trả lời chuyên sâu bằng thuật ngữ kỹ thuật."

**Hai phản hồi khác nhau như thế nào (độ dài, từ vựng, ví dụ)? System prompt
ảnh hưởng đến hành vi model ra sao?** (3–4 câu)
> Cùng câu hỏi "Giải thích blockchain là gì?": persona giáo viên tiểu học trả lời ~2.230 ký tự, xưng "thầy/cô" gọi người nghe là "con", giải thích bằng ẩn dụ đời thường — cả nhóm bạn cùng viết vào "cuốn sổ ma thuật" mà không ai sửa được — rồi mới gắn thuật ngữ Block/Chain, giọng thân thiện và hỏi lại cuối bài. Persona chuyên gia tài chính trả lời dài hơn ~2,6 lần (~5.840 ký tự), dày đặc thuật ngữ (Distributed Consensus Protocol, DLT, Merkle Root, Proof of Stake, DeFi, Asset Tokenization, CBDC...) dưới dạng mục lục phân tích chuyên nghiệp. Thực nghiệm cho thấy system prompt là mức điều khiển hành vi mạnh nhất mà không cần đổi model: một dòng mở đầu quyết định vai trò, từ vựng, độ dài, ví dụ và cả giọng điệu của toàn bộ câu trả lời.

### Câu 2.2 — tiktoken vs đếm từ
Chọn một đoạn văn tiếng Việt ~100 từ. So sánh số token theo `count_tokens`
(tiktoken) với ước lượng `số từ / 0.75` mà Part 1 đã dùng.

**Hai con số chênh nhau bao nhiêu phần trăm? Vì sao tiếng Việt thường tốn
nhiều token hơn tiếng Anh cùng độ dài?**
> Đoạn thử nghiệm có 119 từ / 528 ký tự: `count_tokens` trả về 132 token, ước lượng từ/0.75 trả ~159 token — chênh nhau ~17%. Tiếng Việt tốn token hơn tiếng Anh vì: (1) là ngôn ngữ thanh tiết, một từ nhiều âm được tách thành nhiều từ đơn có khoảng trắng; (2) các dấu (ă, ơ, thanh điệu) khiến mỗi âm tiết viết thành 3–4 ký tự Latin; (3) tokenizer BPE được huấn luyện chủ yếu trên tiếng Anh nên không gộp được các âm tiết tiếng Việt thành token dài, phải cắt nhỏ từng âm tiết — kết quả trung bình 1 từ tiếng Việt tốn ~2 token (251/119) so với ~1,3 token/từ của tiếng Anh.

---

## Block 3 — Streaming & Độ Bền (trả lời sau Checkpoint 3)

### Câu 3.1 — Trải nghiệm người dùng với streaming
**Streaming quan trọng nhất trong trường hợp nào, và khi nào thì
non-streaming lại phù hợp hơn?** (1 đoạn văn)
> Streaming quan trọng nhất với ứng dụng tương tác trực tiếp với người dùng (chatbot, trợ lý CLI, sản phẩm có phản hồi dài): người dùng nhìn thấy dòng chữ đầu tiên sau vài trăm ms thay vì nhìn màn chờ 10–30 giây, cảm nhận tốc độ tốt hơn rất nhiều, và có thể bấm dừng sớm khi thấy câu trả lời lệch hướng — tiết kiệm luôn token. Non-streaming phù hợp hơn khi hệ thống cần xử lý cả khối câu trả lời một thể: trích xuất dữ liệu JSON có cấu trúc (phải có chuỗi hoàn chỉnh mới parse được), pipeline batch/aggregation xử lý hàng nghìn request tự động không ai xem theo thời gian thực, hay khi cần kiểm duyệt/đánh giá toàn bộ câu trả lời trước khi cho người dùng nhìn thấy.

### Câu 3.2 — Vì sao backoff theo cấp số nhân?
**So với delay cố định (ví dụ luôn chờ 1 giây), exponential backoff có lợi
thế gì khi API bị quá tải? Điều gì xảy ra nếu hàng nghìn client cùng retry
với delay cố định giống nhau?**
> Exponential backoff tự giãn các lần thử (0.1 → 0.2 → 0.4 → 0.8...), nên tổng lượng request đè lên server giảm dần theo thời gian và mỗi client sẽ rơi vào các mốc thời gian khác nhau — cho API thời gian hồi phục. Nếu hàng nghìn client cùng retry với delay cố định giống nhau thì xảy ra hiệu ứng "thundering herd": mọi client bắn request đồng loạt đúng vào các nhịp retry ấy, server chưa kịp phục hồi đã bị quá tải kéo dài thay vì giảm dần; trải đều bằng jitter (delay ngẫu nhiên) trong exponential backoff giúp giải tan hiện tượng này.

---

## Block 4 — Mini-Project (trả lời sau Checkpoint 4)

### Câu 4.1 — Thiết kế persona
**Bạn chọn persona gì cho trợ lý của mình? Viết lại system prompt đó và giải
thích 1–2 lựa chọn từ ngữ quan trọng trong prompt (ví dụ: vì sao yêu cầu
"trả lời ngắn gọn", vì sao chỉ định ngôn ngữ...):**
> Persona: "Bạn là trợ giảng thân thiện của khóa AI. Trả lời ngắn gọn (dưới 5 câu) bằng tiếng Việt, dùng ví dụ đời thường. Khi không chắc, hãy thẳng thắn nói không biết và hỏi lại người dùng." Hai lựa chọn quan trọng: (1) "trả lời ngắn gọn" giúp giới hạn output — trong trợ lý CLI mỗi lượt gọi đều tốn token và latency, output càng dài thì phiên chat càng đắt và chậm, nên giới hạn độ dài theo dạng "dưới 5 câu" rõ ràng hơn "ngắn gọn" trừu tượng; (2) "nói không biết và hỏi lại" là hàng rào chống bịa: chỉ định sẵn hành vi khi thiếu thông tin thay vì để model tự tưởng tượng ra câu trả lời, giúp trợ lý đáng tin hơn. Chỉ định "tiếng Việt" để câu trả lời không lẫn tiếng Anh, và "ví dụ đời thường" để bắt model rời lối diễn giải hàn lâm khô khan.

### Câu 4.2 — Hạn chế & cải thiện
**Trợ lý của bạn hiện có hạn chế lớn nhất là gì (ví dụ: history chỉ 3 lượt,
không có bộ nhớ dài hạn, không kiểm duyệt nội dung...)? Đề xuất một cải
thiện cụ thể và mô tả ngắn cách triển khai:**
> Hạn chế lớn nhất là bộ nhớ hội thoại tạm thời: history mỗi lượt bị cắt còn 3 lượt (6 message), nên sau vài lượt trợ lý quên các ràng buộc người dùng đã nêu sớm (ngân sách, lứa tuổi, mục đích học...), và cả phiên mất hẳn khi tắt CLI. Cải thiện cụ thể: giới thiệu tóm lược hội thoại cuộn (rolling summary) — khi history vượt ngưỡng, gọi một lượt API nhẹ (model mini, nhiệt độ thấp) để tóm tắt các lượt sắp bị cắt thành một đoạn ngắn, rồi chèn đoạn tóm tắt này vào đầu messages (ngay sau system prompt); mỗi N lượt chỉ tốn ~1 lượt gọi phụ nhưng giữ được bối cảnh dài hạn, còn chi phí thêm là cố định. Triển khai: giữ biến `summary` trong `run_assistant`, trước khi cắt history kiểm tra `len(history) > 6` rồi tạo tóm tắt từ message cũ, xây dựng `messages = [system] + [summary] + history[-6:] + [user]`.

---

## Danh Sách Kiểm Tra Nộp Bài

- [ ] `python grade.py` — xem điểm tự động, mục tiêu ≥ 75/100
- [ ] Cả 4 checkpoint pytest đều pass
- [ ] Tất cả 9 câu trong file này đã được trả lời
- [ ] Đã copy bài làm vào folder `solution/`, push lên fork và dán link trên trang bài Lab ở VLearn trước 23:59 ngày 11/09/2026
