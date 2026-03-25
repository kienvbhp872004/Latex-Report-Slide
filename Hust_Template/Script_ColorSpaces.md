# PHÂN TÍCH CHUYÊN SÂU: LÝ THUYẾT KHÔNG GIAN MÀU TRONG FUSION

> **Tài liệu gốc:** @[Hust_Template/slide_w3.tex]
> **Chủ đề:** Color Space Theory and Applications in Medical Image Fusion
> **Cấp độ:** Chuyên sâu (Phân tích sinh học & tiêu chuẩn kỹ thuật)

---

## [SLIDE 3-4] TỔNG QUAN RGB & CHIẾN LƯỢC FUSION

### 1. Tại sao RGB lại "tồi" cho Fusion?
*   **Vấn đề Tương quan cao (High Correlation):** 
    *   Trong RGB, thông tin cấu trúc (độ sáng) bị trộn lẫn với thông tin màu sắc ở cả 3 kênh. Khi bạn thay đổi cường độ sáng ở ảnh MRI để làm rõ xương, màu sắc của ảnh PET/SPECT cũng bị biến đổi theo một cách không thể kiểm soát (**Color Distortion**).
*   **Chiến lược Tách biệt cấu trúc vs Chức năng:**
    *   **Anatomy (Giải phẫu):** Lấy từ kênh Intensity (I) hay Luminance (Y).
    *   **Functional (Chức năng):** Lấy từ kênh Màu (Color/Chrominance).
*   **Mục tiêu:** Chỉ can thiệp toán học lớp cao (Wavelet, NSCT) vào đúng kênh "Độ sáng" để giữ nét, còn kênh màu chỉ cần "nhúng" giữ nguyên.

---

## [SLIDE 5] KHÔNG GIAN MÀU YUV (ITU-R BT.601)

### 2. Tại sao $Y = 0.299R + 0.587G + 0.114B$?
*   **Phân tích nhân trắc học:**
    *   0.587 (Green) > 0.299 (Red) > 0.114 (Blue). 
    *   **Tại sao Green lại cao nhất?** Tế bào hình nón trong mắt người nhạy cảm nhất với ánh sáng xanh lục. Công thức này mô phỏng trung thực 100% cách não bộ chúng ta cảm nhận độ sáng từ các nguồn màu khác nhau.
*   **Ý nghĩa:** YUV là nền tảng để tách biệt "nguồn sáng" vật lý ra khỏi "sắc thái" màu.

---

## [SLIDE 6-8] YCBCR - TIÊU CHUẨN VÀNG TRONG NGHIÊN CỨU

### 3. Tại sao giới khoa học "ưu ái" YCbCr?
*   **YCbCr là Hệ màu Kỹ thuật số (Digital):**
    *   Khác với YUV là tín hiệu tương tự (analog), YCbCr có các giá trị offset (+16 cho Y, +128 cho CbCr). 
    *   **Tại sao (+16, +128)?** Để ngăn chặn lỗi tràn số (overflow) khi thực hiện các phép toán hợp nhất phức tạp trên máy tính, đồng thời loại trừ nhiễu đen (footroom) và trắng (headroom).
*   **Kênh Y (Luminance) - "Chiến trường" chính:**
    *   Trong YCbCr, kênh Y nén toàn bộ các thành phần tần số cao (High-freq). Đây là nơi chứa các khối u, mạch máu và cấu trúc xương.
*   **Tối ưu Metric:** Khi ta tính **SSIM** hay **Entropy** trên kênh Y, kết quả sẽ khớp 100% với cảm nhận trực giác của bác sĩ về độ nét lâm sàng.

---

## [SLIDE 10] HỆ MÀU HSY/HSV VÀ CẢNH BÁO

### 4. Tại sao HSV lại Nguy hiểm cho Fusion chuyên nghiệp?
*   **Vấn đề Tuyến tính (Non-linearity):**
    *   HSV là một hệ tọa độ trụ. Khi ta tính toán trung bình cộng trọng số (weighted average) trong fusion, mô hình này sẽ gây ra các lỗi phi tuyến lớn.
*   **Điểm Bất định (Singularity):**
    *   Tại vùng ảnh xám (S=0), góc màu Hue trở thành vô định. Trong ảnh y tế (vốn nhiều vùng xám đen), HSV sẽ tạo ra các điểm ảnh màu sắc "loạn xạ" (artifacts) tại các biên của mô giải phẫu. 
    *   **Kết luận:** HSV chỉ phù hợp để phân đoạn vùng màu (Segmentation) chứ không an toàn cho Hợp nhất (Fusion).

---

## [SLIDE 11-12] TỔNG KẾT VÀ LỰA CHỌN TỐI ƯU

### 5. Tại sao chọn YCbCr làm Kết luận?
*   **YCbCr thắng thế vì:**
    1.  Tách biệt hoàn toàn Sáng/Màu (Decoupling).
    2.  Tương thích tuyệt đối với các chỉ số đánh giá khoa học (Metric compatibility).
    3.  Bảo tồn màu sắc chức năng (Functional preservation) tốt nhất trong các báo cáo khoa học quốc tế.

---
**LƯU Ý THUYẾT TRÌNH:**
*   Khi nói về YCbCr, hãy nhấn mạnh nó là "Standard" của JPEG/MPEG để tạo lòng tin về kỹ thuật.
*   Sử dụng nhược điểm của HSV làm luận điểm phản biện nếu Hội đồng hỏi tại sao không dùng các hệ màu phổ thông khác.
