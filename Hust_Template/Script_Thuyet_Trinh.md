# PHÂN TÍCH CHUYÊN SÂU SLIDE & KỊCH BẢN THUYẾT TRÌNH CHI TIẾT

> **Chủ đề:** Hợp nhất Ảnh Y tế Đa Modal (Phương pháp Phân tích & Quy tắc Hợp nhất)
> **Mục tiêu:** Giải quyết bài toán tại sao công thức ra đời và ý nghĩa kỹ thuật từng mục.

---

# PHẦN 1: CÁC PHƯƠNG PHÁP PHÂN TÍCH ẢNH (DECOMPOSITION)

## [SLIDE 1-3] TỔNG QUAN VÀ MỤC TIÊU PHÂN TÍCH

### 1. Tại sao phải Phân tích (Decomposition)?
*   **Vấn đề:** Ảnh y tế gốc (như MRI hay CT) chứa hỗn hợp cả thông tin cấu trúc (xương, mô) và thông tin nhiễu. Nếu cộng trực tiếp, các chi tiết này sẽ làm mờ nhau.
*   **Giải pháp:** Phải tách ảnh thành các "kênh" thông tin độc lập:
    *   **Base Layer:** Chứa cấu trúc chung, độ tương phản thấp.
    *   **Detail Layer:** Chứa các đường cạnh, kết cấu, nhiễu.
*   **Ý nghĩa:** Chỉ khi tách ra, ta mới có thể chọn lọc: "Lấy cái tốt nhất của MRI ghép với cái tốt nhất của CT".

---

## [SLIDE 5-8] PHƯƠNG PHÁP COLOR SPACE (IHS)

### 2. Phân tích Công thức IHS
*   **Công thức Intensity (I):** $I = (R+G+B)/3$
    *   **Tại sao?** Đây là phép trung bình cộng đơn giản nhất để trích xuất năng lượng ánh sáng tổng thể mà không quan tâm đến màu sắc.
*   **Công thức Hue (H) & Saturation (S):**
    *   **Tại sao lại phức tạp vậy?** Vì $H$ được biểu diễn trên vòng tròn $360^\circ$ (tọa độ góc). Việc dùng $\arccos$ là để tìm góc lệch của vector màu so với trục chuẩn.
*   **Logic Hợp nhất:** Ta giữ $H, S$ và thay $I$.
    *   **Tại sao?** Trong ảnh y tế, màu sắc (H, S) mang thông tin chức năng (ví dụ: vùng não đang hoạt động có màu đỏ). Cấu trúc giải phẫu lại nằm ở cường độ sáng (I). Thay $I$ giúp ta "nhúng" cấu trúc vào lớp vỏ màu.

---

## [SLIDE 9] PHƯƠNG PHÁP PYRAMID (KIM TỰ THÁP)

### 3. Công thức Xây tháp $p_i = F_i * I$ và $r_i = I - p_i$
*   **Tại sao có công thức này?** 
    *   $F_i * I$ là phép chập (Convolution) với một bộ lọc làm mờ. Nó tượng trưng cho việc ta "lùi xa" để nhìn bức ảnh ở quy mô lớn hơn (thấp hơn).
    *   $I - p_i$ (Ảnh gốc - Ảnh mờ) = **Phần dư**. Phần dư này chính là các chi tiết nhỏ bị mất đi khi làm mờ.
*   **Kiến thức tách bạch:** 
    *   **Mục tiêu:** Xử lý đa độ phân giải.
    *   **Ưu điểm:** Giúp quan sát các khối u ở nhiều kích thước khác nhau.
    *   **Nhược điểm:** Dễ bị nhiễu tại các tầng cao do thiếu tính bất biến.

---

## [SLIDE 10-11] PHƯƠNG PHÁP WAVELET & NSCT

### 4. Tại sao Wavelet lại mạnh hơn Pyramid?
*   **Đặc tính Directive Filter (Lọc hướng):**
    *   **Tại sao quan trọng?** Cơ thể người là những đường cong sinh học. Wavelet truyền thống chỉ lọc ngang, dọc, chéo ($0^\circ, 90^\circ, 45^\circ$). Shearlet hay NSCT chia mặt phẳng tần số thành vô số hình nêm nhỏ để bắt trọn mọi góc uốn lượn của mạch máu.
*   **Non-subsampled (NS):** 
    *   **Tại sao lại dùng từ này?** Thông thường, sau khi lọc, người ta hay bỏ bớt pixel để nén ảnh (subsampling). Nhưng trong y tế, việc bỏ pixel làm mất tính "Shift-invariant", gây ra lỗi rung (ringing) quanh các cạnh xương. **NSCT** giữ lại toàn bộ pixel để đảm bảo độ chính xác tuyệt đối từng milimet.

---

## [SLIDE 12] SPARSE REPRESENTATION (BIỂU DIỄN THƯA)

### 5. Công thức $V = DS$ (Từ điển và Mã hóa)
*   **Phân tích logic:**
    *   $D$ (Dictionary): Giống như một bảng chữ cái gồm các "mẫu ảnh" cơ bản (cạnh ngang, chấm tròn, góc vuông).
    *   $S$ (Sparse vector): Một danh sách các con số có rất nhiều số 0.
*   **Tại sao lại chọn "thưa" (Sparse)?**
    *   Vì não bộ con người khi nhìn ảnh cũng chỉ kích hoạt một vài nơ-ron chuyên biệt cho từng loại hình dạng. Việc ép $S$ phải thưa buộc thuật toán phải tìm ra những mẫu **đặc trưng nhất**, giúp loại bỏ nhiễu (noise thường không thể biểu diễn thưa một cách có cấu trúc).

---

# PHẦN 2: CÁC QUY TẮC HỢP NHẤT (FUSION RULES)

## [SLIDE 16] FUZZY LOGIC (LOGIC MỜ)

### 6. Tại sao dùng hàm Gaussian $\mu_{w_i}(y) = e^{-\frac{(y-\mu_i)^2}{2\sigma_i^2}}$?
*   **Bản chất:** Đây là đường cong hình chuông.
*   **Tại sao?** Trong ảnh y tế, ranh giới giữa mô bệnh và mô lành luôn mờ nhạt. Hàm Gaussian cho phép một pixel thuộc về cả hai nhóm (thuộc nhóm "bệnh" 20% và "lành" 80%). 
*   **Cách thức:** Quy tắc IF-THEN kết hợp với hàm Gaussian giúp thuật toán "suy luận" mềm dẻo như một bác sĩ giàu kinh nghiệm, không máy móc "đen hay trắng".

---

## [SLIDE 17-18] STATISTICS (PCA & HMT)

### 7. Phân tích PCA: Tại sao dùng Covariance & Eigenvalues?
*   **Ma trận Hiệp phương sai (Covariance):** Đo lường sự giống nhau giữa hai ảnh. Ảnh MRI và CT càng giống nhau ở vùng nào, giá trị này càng lớn.
*   **Eigenvalues (Trị riêng):** Đại diện cho "năng lượng" thông tin. 
*   **Tại sao lại lấy tỷ lệ $w_1 = V_{1,1}/(V_{1,1}+V_{2,1})$?** Để tạo ra trọng số tự động: ảnh nào mang nhiều thông tin biến thiên hơn (Eigenvalue cao hơn), nó sẽ có tiếng nói lớn hơn trong bức ảnh cuối cùng.

---

## [SLIDE 19-20] HUMAN VISUAL SYSTEM & PCNN

### 8. PCNN (Pulse-Coupled Neural Network)
*   **Tại sao lại dùng "Xung điện"?**
    *   Đây là mô phỏng vỏ não mèo. Các pixel không chỉ là con số, chúng là nơ-ron.
    *   **Cơ chế Linking:** Nếu pixel A phát xung và nó giống pixel B, nó sẽ lôi kéo pixel B phát xung theo.
*   **Ý nghĩa:** Giúp hợp nhất các "vùng" ảnh có cấu trúc tương đồng (ví dụ cả một khối u) thay vì chỉ hợp nhất từng pixel rời rạc, tránh hiện tượng ảnh bị "nổ" hay "lốm đốm".

---

# PHẦN 3: CHIẾN LƯỢC KHÔNG GIAN MÀU (SLIDE W3)

## [SLIDE 3-4] TỔNG QUAN RGB & TẠI SAO PHẢI CHUYỂN ĐỔI

### 9. Vấn đề "High Correlation" của RGB
*   **Tại sao RGB lại tồi cho Fusion?** 
    *   Ba kênh R, G, B luôn dính chặt lấy nhau. Khi bạn tăng độ sáng, cả 3 kênh đều tăng. 
    *   Nếu ta hợp nhất trực tiếp trên RGB, màu sắc sẽ bị biến đổi khủng khiếp (color distortion).
*   **Chiến lược tách biệt:** Phải đưa về hệ màu có kênh **Độ sáng (Luminance - Y)** riêng biệt.

---

## [SLIDE 5-6] YUV VÀ YCBCR - TIÊU CHUẨN VÀNG

### 10. Tại sao $Y = 0.299R + 0.587G + 0.114B$?
*   **Phân tích hệ số:**
    *   Màu Xanh lá (Green) chiếm tới gần 60% trọng số. 
    *   **Tại sao?** Vì tế bào hình nón trong mắt người nhạy cảm nhất với bước sóng ánh sáng xanh lá. Công thức này không phải ngẫu nhiên, nó là công thức "định lượng thị giác người".
*   **Sự khác biệt của YCbCr:**
    *   Nó là hệ màu kỹ thuật số (digital). Các giá trị offset (+16, +128) giúp tránh lỗi tràn số khi tính toán trên máy tính.

---

## [SLIDE 9] HỆ MÀU HSV VÀ NHỮNG CẠM BẪY

### 11. Tính "Non-linearity" và "Singularity"
*   **Vấn đề:** Khi độ bão hòa $S=0$ (ảnh đen trắng), góc màu $H$ trở thành vô nghĩa (không xác định được góc).
*   **Hậu quả:** Trong hợp nhất ảnh y tế (vốn nhiều vùng xám), thuật toán sẽ bị "loạn" tại các điểm này, gây ra những chấm màu lạ (artifacts). Đây là lý lẽ đập tan việc dùng HSV cho các báo cáo Fusion chuyên sâu.

---

# KẾT LUẬN CHUNG CHO TOÀN BỘ BÁO CÁO

1.  **Về Toán học:** Mọi công thức đều hướng tới việc tách biệt "Tín hiệu" ra khỏi "Nhiễu".
2.  **Về Thị giác:** Mọi hệ màu (YCbCr, HVS) đều hướng tới việc làm thỏa mãn đôi mắt của bác sĩ.
3.  **Về Khoa học:** Hợp nhất ảnh là sự phối hợp giữa Hình học đa thang độ (Wavelet/NSCT) và Lý thuyết thông tin (MI/SSIM).

---
**HƯỚNG DẪN THUYẾT TRÌNH:**
*   Khi gặp công thức khó, hãy dùng ví dụ thực tế (như MRI/CT) để giải thích "tại sao" ta cần nó.
*   Nhấn mạnh vào tính "tối ưu" của YCbCr và "độ sâu" của NSCT/Shearlet để thể hiện trình độ chuyên môn.
