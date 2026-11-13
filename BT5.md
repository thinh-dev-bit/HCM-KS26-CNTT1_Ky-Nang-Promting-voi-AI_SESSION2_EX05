# Phần 1: Phân tích cơ chế In-Context Learning (Học trong ngữ cảnh)

**Tại sao AI phân loại sai trong tình huống Zero-shot?**
Khi chỉ nhận lệnh Zero-shot (không có ví dụ), AI phân tích câu chữ dựa trên nghĩa hiển ngôn bề mặt (Surface Lexical Meaning). Gặp câu *"máy thiết kế đẹp mê ly... khỏi cần bật lò sưởi"*, AI ghi nhận các từ khóa tích cực và kết luận đây là một lời khen, hoàn toàn bỏ qua ngữ cảnh kỹ thuật ngầm hiểu (98 độ C là mức nhiệt độ phá hủy phần cứng).

**Giải pháp: Cơ chế In-Context Learning (ICL)**
Theo tài liệu đào tạo (Mục 2.2), bộ não AI hoạt động dựa trên cơ chế **In-Context Learning (Học trong ngữ cảnh)**. Cơ chế này sở hữu sức mạnh vượt trội:
*   **Học "nóng" không cần huấn luyện lại:** Kỹ sư không cần phải can thiệp vào mã nguồn hay tinh chỉnh trọng số (fine-tuning) của mô hình tốn kém hàng ngàn đô la. AI có khả năng tự động trích xuất quy luật, khuôn mẫu và sắc thái ngữ nghĩa trực tiếp từ các ví dụ mẫu (Few-shot) được dán vào trong Cửa sổ ngữ cảnh (Context Window) của phiên chat.
*   **"Bắt sóng" sắc thái phức tạp:** Bằng cách cung cấp từ 2-5 ví dụ về câu nói mỉa mai, AI sẽ hiểu ra quy luật mới: *"À, nếu khách hàng dùng từ ngữ khen ngợi nhưng đi kèm với một thông số kỹ thuật tệ hại (nóng 98 độ, pin 30 phút, giao 15 ngày), thì bản chất đó là lời chê bai (TIÊU CỰC)"*.

---

# Phần 2: Thiết kế Prompt sửa lỗi bằng Few-shot

Dưới đây là câu lệnh Few-shot hoàn chỉnh, cung cấp 3 ví dụ mẫu chuẩn xác về các tình huống khen ngầm/mỉa mai (Nhiệt độ, Pin, Giao hàng) để "dạy nóng" AI trước khi yêu cầu nó phân loại câu đánh giá thực tế:

> **[Context & Request]**
> Bạn là Chuyên gia Phân tích Dữ liệu Khách hàng tại hệ thống TechShop. Nhiệm vụ của bạn là đọc các phản hồi (Review) và phân loại chúng vào 1 trong 2 nhãn: `[TÍCH_CỰC]` hoặc `[TIÊU_CỰC]`.
>
> **[Explanation - Rào cản định kiến]**
> Khách hàng công nghệ thường dùng cách nói mỉa mai (Sarcasm). Đừng chỉ đánh giá qua các tính từ khen ngợi bề mặt (như "tuyệt vời", "nhanh", "trâu"). Hãy chú ý đến các thông số kỹ thuật thực tế đi kèm. Nếu thông số đó là một lỗi nghiêm trọng hoặc trải nghiệm tồi tệ, nhãn phải là `[TIÊU_CỰC]`.
>
> **[Few-shot Examples - Dữ liệu dạy học]**
> Hãy học quy luật phân tích sắc thái từ 3 ví dụ mẫu sau đây:
> 
> *   **Ví dụ 1 (Về Pin):**
>     *   Input: "Pin máy này trâu vô cùng, sạc đầy 100% mang ra quán cafe lướt web được hẳn 30 phút là sập nguồn tối thui."
>     *   Output: `[TIÊU_CỰC]`
> *   **Ví dụ 2 (Về Nhiệt độ):**
>     *   Input: "Hệ thống tản nhiệt hoạt động tuyệt vời, chơi game LOL 5 phút là có thể đập trứng lên bàn phím ốp la ăn sáng luôn."
>     *   Output: `[TIÊU_CỰC]`
> *   **Ví dụ 3 (Về Giao hàng):**
>     *   Input: "TechShop giao hàng thần tốc như chớp, đặt mua từ dịp Sale mùng 1 Tết mà đến tận Rằm tháng Giêng hàng đã tới nơi rồi."
>     *   Output: `[TIÊU_CỰC]`
>
> **[Action - Thực thi]**
> Áp dụng quy luật trên, hãy phân loại câu đánh giá sau và chỉ xuất ra đúng nhãn kết quả:
> *   Input: *"Máy thiết kế đẹp mê ly, cầm rất đầm tay, mùa đông ôm máy chơi game thì khỏi cần bật lò sưởi vì nhiệt độ CPU lên hẳn 98 độ C!"*
> *   Output: