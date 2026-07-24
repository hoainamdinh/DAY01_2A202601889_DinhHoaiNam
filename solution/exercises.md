# K3 — Ngày 1: Bài Tập & Phản Ánh
## Khám Phá LLM API | Phiếu Thực Hành

**Thời lượng:** 9h00–13h00
**Cách làm:** Trả lời từng câu ngay sau khi hoàn thành block tương ứng —
đừng để dồn hết về cuối buổi. Thay dòng `*Câu trả lời của bạn*` bằng câu
trả lời thật (chấm tự động sẽ đếm số câu đã trả lời).

---

## Block 1 — API Cơ Bản (trả lời sau Checkpoint 1)

### Câu 1.1 — Độ nhạy của temperature
Gọi `call_openai` với temperature 0.0, 0.5, 1.0 và 1.5 dùng prompt
**"Hãy kể cho tôi một sự thật thú vị về Việt Nam."**

**Bạn nhận thấy quy luật gì qua bốn phản hồi?** (2–3 câu)
> khi temperature = 0.0, phản hồi có tính nhất quán cao nhất, logic và ngắn gọn, độ sáng tạo thấp. Khi tăng temperature lên 0.5 - 1.0, câu trả lời trở nên đa dạng về vốn từ và phong phú hơn về góc nhìn. Khi lên mức 1.5, phản hồi xuất hiện các cấu trúc từ ngữ lạ, ngẫu nhiên cao và có nguy cơ bị lan man hoặc hoang tưởng (hallucination).

### Câu 1.2 — Chọn temperature cho sản phẩm
**Bạn sẽ đặt temperature bao nhiêu cho chatbot hỗ trợ khách hàng, và tại sao?**
> Tôi sẽ đặt temperature từ 0.1 đến 0.3. Lý do là chatbot hỗ trợ khách hàng đòi hỏi sự chính xác tuyệt đối, trung thực với thông tin dịch vụ/sản phẩm và tính nhất quán cao, tránh việc mô hình tự bịa ra thông tin sai sự thật hoặc trả lời không đúng quy chuẩn.

### Câu 1.3 — Đánh đổi chi phí
Kịch bản: 10.000 người dùng hoạt động mỗi ngày, mỗi người gọi API 3 lần,
mỗi lần trung bình ~350 token đầu ra.

**Ước tính GPT-4o đắt hơn GPT-4o-mini bao nhiêu lần cho workload này? Nêu một
trường hợp GPT-4o xứng đáng với chi phí và một trường hợp nên dùng mini:**
> Theo bảng giá, chi phí output của GPT-4o ($0.010/1K token) đắt hơn GPT-4o-mini ($0.0006/1K token) khoảng 16.7 lần. GPT-4o xứng đáng chi phí cho các bài toán phân tích hợp đồng pháp lý phức tạp hoặc lập trình thuật toán nâng cao. Ngược lại, GPT-4o-mini nên được áp dụng cho các tác vụ phổ thông như tóm tắt văn bản ngắn, phân loại cảm xúc phản hồi hoặc chát tự động hỏi-đáp cơ bản với khách hàng.

---

## Block 2 — System Prompt & Token (trả lời sau Checkpoint 2)

### Câu 2.1 — Sức mạnh của persona
Gọi `chat_with_system_prompt` hai lần với cùng câu hỏi
**"Giải thích blockchain là gì?"** nhưng hai system prompt khác nhau:
- "Bạn là giáo viên tiểu học, giải thích thật đơn giản cho trẻ 8 tuổi."
- "Bạn là chuyên gia tài chính, trả lời chuyên sâu bằng thuật ngữ kỹ thuật."

**Hai phản hồi khác nhau như thế nào (độ dài, từ vựng, ví dụ)? System prompt
ảnh hưởng đến hành vi model ra sao?** (3–4 câu)
> Phản hồi dành cho học sinh 8 tuổi sử dụng hình ảnh so sánh sinh động (như cuốn sổ nhật ký chung cả lớp), câu từ ngắn gọn và từ vựng rất đơn giản. Trong khi đó, phản hồi của chuyên gia tài chính tập trung vào các thuật ngữ chuyên ngành như "cơ chế đồng mật (consensus algorithm)", "sổ cái phân tán (DLT)", và "tính bất biến (immutability)". System prompt đóng vai trò như một bộ lọc định hình tư duy và tư cách ứng xử, quyết định tông giọng, độ sâu tri thức và cách thức tiếp cận đối tượng của mô hình.

### Câu 2.2 — tiktoken vs đếm từ
Chọn một đoạn văn tiếng Việt ~100 từ. So sánh số token theo `count_tokens`
(tiktoken) với ước lượng `số từ / 0.75` mà Part 1 đã dùng.

**Hai con số chênh nhau bao nhiêu phần trăm? Vì sao tiếng Việt thường tốn
nhiều token hơn tiếng Anh cùng độ dài?**
> Thực tế số token theo tiktoken thường nhiều hơn ước lượng (số từ / 0.75) từ 30% đến 50%. Lý do là các bộ mã hóa BPE (như cl100k_base) được tối ưu hóa chủ yếu cho tiếng Anh. Tiếng Việt có nhiều từ ghép, âm tiết rời có dấu thanh (dấu sắc, huyền, hỏi, ngã, nặng) khiến các từ thường bị tách thành nhiều sub-word token lẻ thay vì giữ nguyên nguyên từ.

---

## Block 3 — Streaming & Độ Bền (trả lời sau Checkpoint 3)

### Câu 3.1 — Trải nghiệm người dùng với streaming
**Streaming quan trọng nhất trong trường hợp nào, và khi nào thì
non-streaming lại phù hợp hơn?** (1 đoạn văn)
> Streaming quan trọng nhất trong các giao diện tương tác trực tiếp với người dùng như chatbot CLI/Webui hoặc tính năng trợ lý gõ văn bản, giúp giảm thời gian chờ cảm nhận (perceived latency) xuống gần như bằng 0. Ngược lại, non-streaming phù hợp hơn cho các tác vụ xử lý ngầm ở backend (background jobs), gọi API trả về dữ liệu dạng JSON cấu trúc (Structured Outputs), hoặc khi cần kiểm tra/đánh giá toàn bộ câu trả lời trước khi gửi đi.

### Câu 3.2 — Vì sao backoff theo cấp số nhân?
**So với delay cố định (ví dụ luôn chờ 1 giây), exponential backoff có lợi
thế gì khi API bị quá tải? Điều gì xảy ra nếu hàng nghìn client cùng retry
với delay cố định giống nhau?**
> Exponential backoff giúp nới rộng khoảng cách thời gian giữa các lần thử lại, tạo không gian và thời gian cho hệ thống server khôi phục từ tình trạng quá tải. Nếu hàng nghìn client cùng retry với thời gian cố định 1 giây, sẽ dẫn đến hiện tượng "thảm họa dồn dập" (Thundering Herd Problem), tiếp tục làm nghẽn server và khiến hệ thống không thể tự phục hồi.

---

## Block 4 — Mini-Project (trả lời sau Checkpoint 4)

### Câu 4.1 — Thiết kế persona
**Bạn chọn persona gì cho trợ lý của mình? Viết lại system prompt đó và giải
thích 1–2 lựa chọn từ ngữ quan trọng trong prompt (ví dụ: vì sao yêu cầu
"trả lời ngắn gọn", vì sao chỉ định ngôn ngữ...):**
> Persona: "Bạn là trợ giảng thân thiện của khóa AI, trả lời ngắn gọn bằng tiếng Việt." 
> Việc chọn từ "thân thiện" giúp mô hình giữ tông giọng cởi mở, khuyến khích học viên đặt câu hỏi. Yêu cầu "trả lời ngắn gọn" nhằm tối ưu chi phí token và giữ cho cuộc hội thoại đi thẳng vào trọng tâm, còn chỉ định "bằng tiếng Việt" đảm bảo tính nhất quán ngôn ngữ trong toàn bộ phiên hội thoại.

### Câu 4.2 — Hạn chế & cải thiện
**Trợ lý của bạn hiện có hạn chế lớn nhất là gì (ví dụ: history chỉ 3 lượt,
không có bộ nhớ dài hạn, không kiểm duyệt nội dung...)? Đề xuất một cải
thiện cụ thể và mô tả ngắn cách triển khai:**
> Hạn chế lớn nhất hiện tại là lịch sử chỉ giữ 3 lượt gần nhất (6 message), khiến mô hình dễ "quên" ngữ cảnh quan trọng được thảo luận từ đầu phiên chat. Cải thiện đề xuất: Triển khai kỹ thuật "Conversation Summarization". Khi lịch sử vượt quá 6 messages, sử dụng mô hình mini để tóm tắt các đoạn chat cũ thành một đoạn tóm tắt ngắn (summary prompt) và đính kèm vào phần đầu của history thay vì xóa bỏ hoàn toàn.

---

## Danh Sách Kiểm Tra Nộp Bài

- [ ] `python grade.py` — xem điểm tự động, mục tiêu ≥ 75/100
- [ ] Cả 4 checkpoint pytest đều pass
- [ ] Tất cả 9 câu trong file này đã được trả lời
- [ ] Đã copy bài làm vào folder `solution/` và zip theo hướng dẫn README
