# KỊCH BẢN THUYẾT TRÌNH TỔNG LỰC: PHÂN TÍCH & QUY TẮC HỢP NHẤT (FULL-DIVE)

> **Tài liệu gốc:** @[Hust_Template/Decompositon_Fusion_Rule.tex]
> **Hướng dẫn:** Phần **[NÓI]** là script để bạn trình bày, phần **[GIẢI MÃ TOÁN HỌC]** là chiều sâu kỹ thuật để bạn hiểu và phản biện.

---

## PHẦN 1: TỔNG QUAN VÀ CHIẾN LƯỢC PHÂN TÍCH

### [SLIDE 1-3] GIỚI THIỆU & MỤC TIÊU
**[NÓI]:** Chào thầy và các bạn. Chúng ta không chỉ đơn giản là "cộng" hai bức ảnh MRI và CT lại. Mục tiêu thực sự là tách biệt thông tin cấu trúc và chi tiết để chọn lọc những gì tốt nhất. 
**[GIẢI MÃ TOÁN HỌC]:** 
*   **Tại sao phải Phân tích?** Vì ảnh y tế là sự chồng chéo của các dải thông (sub-bands). Phân tích giúp tách thành **Base layer** (năng lượng thấp, cấu trúc) và **Detail layer** (năng lượng cao, đặc trưng cạnh).

---

### [SLIDE 5-8] PHƯƠNG PHÁP COLOR SPACE (IHS)
**[NÓI]:** IHS giúp chúng ta tách biệt hoàn toàn "Độ sáng" (Intensity) ra khỏi "Màu sắc". Trong y tế, màu sắc thường mang tính chất chức năng (Functional), nên chúng ta giữ nguyên chúng và chỉ "nâng cấp" độ nét bằng cách thay đổi thành phần cường độ.
**[GIẢI MÃ TOÁN HỌC]:** 
*   $I = (R+G+B)/3$: Là phép chiếu đơn giản nhất để lấy năng lượng trung bình. 
*   $S = 1 - \frac{3\min(R,G,B)}{R+G+B}$: Tại sao dùng $\min$? Để đo khoảng cách tới trục xám (càng gần xám, màu càng nhạt, $S$ càng về 0).
*   $H = \theta$: Là góc lệch vector màu trên mặt phẳng vuông góc với trục xám.

---

### [SLIDE 9] PHƯƠNG PHÁP PYRAMID (KIM TỰ THÁP)
**[NÓI]:** Kim tự tháp giúp bác sĩ nhìn ảnh ở nhiều mức độ phóng đại khác nhau. Ta làm mờ ảnh để lấy cấu trúc nền và trừ đi ảnh gốc để thu hoạch những chi tiết bị mất.
**[GIẢI MÃ TOÁN HỌC]:** 
*   $p_i = F_i * I$: Phép nhân chập với nhân Gauss (Low-pass filter).
*   $r_i = I - p_i$: Là **Phần dư (Residue)**. Đây chính là toán học của việc giữ lại thông tin cạnh sắc.
*   $I_F = \sum r_i + p_{\text{max}}$: Toán học của việc tái tạo (Reconstruction) bằng cách đắp lại các lớp chi tiết.

---

### [SLIDE 10-11] PHƯƠNG PHÁP WAVELET & NSCT
**[NÓI]:** Nếu Kim tự tháp chỉ có 1 hướng thì Wavelet và NSCT có vô số hướng (Directive Filter). Nó giúp chúng ta bắt được mọi đường cong li ti của mạch máu hay khối u.
**[GIẢI MÃ TOÁN HỌC]:** 
*   **Directive Filter:** Chia mặt phẳng tần số thành các hình nêm (wedges) để định vị hướng chi tiết.
*   **Non-subsampled (NS):** Loại bỏ phép lấy mẫu xuống để tránh **lỗi Gibb** (nhiễu rung xung quanh biên xương). Đây là cấp độ cao nhất của bảo toàn dữ liệu y tế.

---

### [SLIDE 12] SPARSE REPRESENTATION (SR)
**[NÓI]:** Ý tưởng ở đây là mỗi bức ảnh được ghép từ một "Bảng chữ cái" (Dictionary) các mẫu cơ bản ranh giới. Phương pháp này cực kỳ giỏi trong việc "lọc sạch" nhiễu mà vẫn giữ được độ nét.
**[GIẢI MÃ TOÁN HỌC]:** 
*   $V = DS$: Đây là bài toán tối ưu hóa $L_0/L_1$ norm. 
*   **Tại sao lại là "Thưa" (Sparse)?** Để tìm ra lời giải đơn giản và đặc trưng nhất cho một tín hiệu phức tạp, từ đó tự động loại bỏ những thành phần thông tin hỗn tạp (nhiễu).

---

## PHẦN 2: QUY TẮC HỢP NHẤT (FUSION RULES)

### [SLIDE 16] FUZZY LOGIC (LOGIC MỜ)
**[NÓI]:** Biên giới giữa mô bệnh và mô lành luôn mờ nhạt. Logic mờ giúp chúng ta xử lý sự không chắc chắn này bằng cách đặt niềm tin vào từng pixel theo một dải liên tục thay vì chỉ "0 hoặc 1".
**[GIẢI MÃ TOÁN HỌC]:** 
*   Gaussian $\mu_{w_i}(y) = e^{-\dots}$: Đây là "Độ tin cậy". Tại $\mu$ (tâm), niềm tin lớn nhất bằng 1; càng xa tâm, niềm tin càng giảm.
*   **Takagi-Sugeno:** Cho phép đầu ra là một hàm tuyến tính mượt mà, giúp tránh hiện tượng ảnh bị "vỡ" tại vùng chuyển tiếp.

---

### [SLIDE 17-18] STATISTICS (PCA & HMT)
**[NÓI]:** PCA tự động tìm ra đâu là ảnh mang nhiều thông tin "năng lượng" hơn để ưu tiên trọng số. Còn HMT giữ cho cấu trúc dọc như tủy sống hay mạch máu không bị đứt đoạn.
**[GIẢI MÃ TOÁN HỌC]:** 
*   **Covariance Matrix:** Đo lường sự "đồng biến" giữa hai ảnh đầu vào.
*   **Eigenvalues:** Đại diện cho năng lượng tín hiệu. Trọng số $w$ tỉ lệ thuận với Eigenvalue để ưu tiên ảnh chứa nhiều đặc trưng hơn.
*   **HMT Transition P:** Mô hình hóa xác suất Cha-Con. Nếu pixel cha là cạnh rõ nét, xác suất cao pixel con cũng vậy.

---

### [SLIDE 19-20] HVS & PCNN (THỊ GIÁC NGƯỜI)
**[NÓI]:** PCNN mô phỏng nơ-ron điện ở vỏ não, giúp hợp nhất ảnh theo "vùng" thay vì từng pixel rời rạc. Điều này khiến ảnh trông cực kỳ "thuận mắt" đối với các bác sĩ.
**[GIẢI MÃ TOÁN HỌC]:** 
*   **Visibility $V(x,y)$:** Dựa trên định luật Stevens (số mũ $\alpha \approx 0.6-0.7$). Mắt người cảm nhận độ sáng qua một hàm mũ, không phải một đường thẳng.
*   **Feeding & Linking trong PCNN:** Cơ chế "Triggering" nơ-ron: các pixel giống nhau sẽ cùng phát xung đồng bộ, giúp duy trì tính nhất quán về vùng (regional consistency).

---

### [SLIDE 21] CHỈ SỐ ĐÁNH GIÁ (METRICS)
**[NÓI]:** Cuối cùng, chúng ta dùng các thước đo như MI (độ trung thực thông tin) và SF (độ sắc nét) để làm "ban giám khảo" đánh giá xem quá trình hợp nhất có thực sự hiệu quả hay không.
**[GIẢI MÃ TOÁN HỌC]:** 
*   $SF = \sqrt{RF^2 + CF^2}$: Tổng hợp năng lượng cạnh từ cả hàng và cột theo định lý Pytago.
*   **Mutual Information (MI):** Đo lượng tin "chảy" từ ảnh gốc sang ảnh fusion.

---

## KẾT LUẬN TỔNG LỰC
**[NÓI]:** Thưa Hội đồng, hợp nhất ảnh đa modal là sự phối hợp tinh vi giữa toán hình học đa hướng và lý thuyết nơ-ron thị giác. Thấu hiểu các công thức này chính là chìa khóa để hỗ trợ bác sĩ cứu người một cách chính xác nhất.

---
**MẸO THUYẾT TRÌNH:**
1. Đọc phần **[NÓI]** cho khán giả.
2. Dùng phần **[GIẢI MÃ TOÁN HỌC]** để trả lời câu hỏi của thầy cô.
3. Luôn nhấn mạnh vào **"Y tế"** và **"Bác sĩ"** khi kết luận một phép toán khó.
