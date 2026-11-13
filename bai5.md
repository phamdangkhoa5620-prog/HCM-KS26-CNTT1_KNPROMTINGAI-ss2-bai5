# BÀI LÀM: IN-CONTEXT LEARNING VÀ KỸ THUẬT FEW-SHOT – TECHSHOP

## PHẦN 1: PHÂN TÍCH CƠ CHẾ IN-CONTEXT LEARNING

### 1. Khái niệm

- **In-Context Learning (ICL)** là khả năng AI học cách thực hiện nhiệm vụ thông qua những hướng dẫn và ví dụ được cung cấp ngay trong Prompt.
- AI không cần huấn luyện lại (Training) hoặc cập nhật trọng số mô hình (Model Weights).
- AI dựa vào ngữ cảnh hiện tại để nhận diện quy luật và áp dụng vào dữ liệu mới.

### 2. Cơ chế hoạt động

- **Bước 1:** Người dùng cung cấp yêu cầu phân loại cảm xúc.
- **Bước 2:** Đưa vào Prompt các ví dụ mẫu gồm đánh giá và nhãn cảm xúc tương ứng.
- **Bước 3:** AI phân tích điểm chung giữa các ví dụ để nhận diện cách diễn đạt, ý nghĩa ẩn dụ và sự mỉa mai.
- **Bước 4:** AI áp dụng quy luật vừa nhận diện để phân loại những đánh giá mới.

### 3. Áp dụng vào tình huống TechShop

Review: "Máy thiết kế đẹp mê ly, cầm rất đầm tay, mùa đông ôm máy chơi game thì khỏi cần bật lò sưởi vì nhiệt độ CPU lên hẳn 98 độ C!"

- Zero-shot dễ bị đánh lừa bởi những từ ngữ tích cực như "đẹp mê ly", "đầm tay".
- Few-shot cung cấp ví dụ về cách khách hàng khen đểu thiết bị quá nóng.
- AI nhận diện câu nói "khỏi cần bật lò sưởi" là lời mỉa mai về nhiệt độ CPU quá cao.

**Kết luận:** In-Context Learning giúp AI nhận biết quy luật từ ví dụ ngay trong ngữ cảnh Prompt mà không cần thay đổi trọng số mô hình.

## PHẦN 2: SỬA LỖI BẰNG FEW-SHOT

### 1. Prompt Few-shot hoàn chỉnh

```text
[ROLE]
Bạn là chuyên viên Đảm bảo Chất lượng (QA) của TechShop,
có nhiệm vụ phân loại cảm xúc đánh giá sản phẩm.

[TASK]
Phân loại mỗi đánh giá thành một trong hai nhãn:
- TÍCH CỰC
- TIÊU CỰC

[INSTRUCTION]
Không chỉ dựa vào từ ngữ khen hoặc chê xuất hiện trực tiếp.
Phải phân tích toàn bộ ngữ cảnh, ý nghĩa ẩn dụ,
câu nói mỉa mai và tiếng lóng công nghệ.

Đặc biệt chú ý:
- Nhiệt độ CPU quá cao là dấu hiệu tiêu cực.
- Pin nhanh hết là dấu hiệu tiêu cực.
- Giao hàng chậm trễ là dấu hiệu tiêu cực.
- Lời khen mang ý nghĩa châm biếm phải được phân loại
  theo ý nghĩa thực tế, không theo từ ngữ bề mặt.

[FEW-SHOT EXAMPLES]

Ví dụ 1:
Input:
"Mùa đông khỏi cần bật lò sưởi, chỉ cần mở game
là CPU nóng tới 98 độ C, quá tuyệt vời!"

Output:
TIÊU CỰC

Lý do:
Khách hàng mỉa mai tình trạng CPU quá nóng,
có thể ảnh hưởng đến hiệu năng và độ bền thiết bị.

Ví dụ 2:
Input:
"Pin siêu đỉnh, vừa sạc đầy đã phải cắm sạc lại
sau 30 phút sử dụng, đúng là tiện lợi hết sức!"

Output:
TIÊU CỰC

Lý do:
Khách hàng nói mỉa mai về thời lượng pin quá ngắn,
không đáp ứng nhu cầu sử dụng.

Ví dụ 3:
Input:
"Shop giao hàng nhanh thật, đặt từ thứ Hai
đến tận Chủ nhật mới nhận được, tốc độ đáng ngưỡng mộ!"

Output:
TIÊU CỰC

Lý do:
Khách hàng mỉa mai vì thời gian giao hàng quá lâu,
không đúng kỳ vọng.

[OUTPUT FORMAT]
Chỉ trả về kết quả theo định dạng JSON:

{
  "review": "",
  "sentiment": "",
  "reason": ""
}

[CONSTRAINTS]
- Phân loại đúng theo ý nghĩa thực tế của đánh giá.
- Không bị đánh lừa bởi những từ ngữ khen đểu.
- Không tự suy diễn thông tin không có trong đánh giá.
- Nếu đánh giá vừa khen vừa chê, hãy xác định cảm xúc
  chủ đạo của khách hàng.
- Chỉ sử dụng hai nhãn TÍCH CỰC hoặc TIÊU CỰC.
- Không thêm nội dung bên ngoài JSON.

[INPUT]
Review:
"Máy thiết kế đẹp mê ly, cầm rất đầm tay,
mùa đông ôm máy chơi game thì khỏi cần bật lò sưởi
vì nhiệt độ CPU lên hẳn 98 độ C!"
```

### 2. Kết quả chạy thử

```json
{
  "review": "Máy thiết kế đẹp mê ly, cầm rất đầm tay, mùa đông ôm máy chơi game thì khỏi cần bật lò sưởi vì nhiệt độ CPU lên hẳn 98 độ C!",
  "sentiment": "TIÊU CỰC",
  "reason": "Khách hàng mỉa mai tình trạng CPU quá nóng ở mức 98 độ C. Những lời khen về thiết kế và cảm giác cầm nắm không làm thay đổi ý nghĩa chê bai về nhiệt độ máy."
}
```

### 3. Kết luận

- **Zero-shot:** Phù hợp với những câu đánh giá đơn giản, có cảm xúc thể hiện rõ ràng nhưng dễ nhầm lẫn khi gặp câu mỉa mai hoặc nghĩa bóng.
- **Few-shot:** Cung cấp ví dụ cụ thể giúp AI nhận diện sắc thái ngôn ngữ phức tạp và phân loại chính xác hơn.

**Kết luận chung:** TechShop nên sử dụng kỹ thuật Few-shot kết hợp In-Context Learning để xử lý 5.000 đánh giá sản phẩm. Phương pháp này giúp giảm nhầm lẫn giữa lời khen thật và lời khen mỉa mai, nâng cao chất lượng phân loại cảm xúc mà không cần huấn luyện lại mô hình AI.