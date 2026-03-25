# HƯỚNG DẪN GIẢI MÃ TOÁN HỌC: PHƯƠNG PHÁP & QUY TẮC HỢP NHẤT (DEEP-DIVE)

> **Tài liệu nghiên cứu:** @[Hust_Template/Decompositon_Fusion_Rule.tex]
> **Mục tiêu:** Phân tích chi tiết từng công thức, ý nghĩa lô-gic và lý do khoa học tại sao chúng tồn tại.

---

## 1. PHÂN TÍCH CÔNG THỨC IHS (COLOR SPACE)

### Slide 6: Intensity (I) và Saturation (S)
$$I = \frac{R+G+B}{3}$$ 
*   **Tại sao chia 3?** Đây là phép toán "Dân chủ hóa" màu sắc. Nó coi đóng góp của Đỏ, Xanh lá, Xanh dương là như nhau để ước lượng năng lượng sáng (Intensity) trung bình của một pixel.
*   **Ý nghĩa:** Tách thông tin "Năng lượng" ra khỏi thông tin "Màu sắc". 

$$S = 1 - \frac{3\min(R,G,B)}{R+G+B}$$ 
*   **Tại sao lại dùng $\min(R,G,B)$?** Một pixel càng gần màu xám thì R, G, B càng bằng nhau (ví dụ: 128, 128, 128). Khi đó $\min(R,G,B)$ sẽ lớn, làm giá trị phân số tiến tới 1/3, dẫn đến $S$ tiến tới 0 (mất màu).
*   **Ý nghĩa:** Đo lường "độ tinh khiết" của màu. Nếu có một kênh bằng 0 (màu cực kỳ rực rỡ), $S$ sẽ bằng 1.

---

### Slide 7: Công thức Hué (H)
$$\theta = \cos^{-1} \left( \frac{(R-G)+(R-B)}{2\sqrt{(R-G)^2+(R-B)(G-B)}} \right)$$
*   **Tại sao lại có căn bậc hai và hiệu (R-G), (R-B)?** 
    *   Đây là công thức hình chiếu vector màu lên một mặt phẳng vuông góc với trục xám (đường nối Đen-Trắng). 
    *   Tử số là "độ lệch" của R so với G và B. Mẫu số là độ dài vector để chuẩn hóa về đoạn [-1, 1] trước khi dùng hàm $\cos^{-1}$.
*   **Ý nghĩa:** Xác định "vị trí" của màu trên vòng tròn sắc thái (Hue circle).

---

## 2. PHÂN TÍCH CÔNG THỨC PYRAMID (SLIDE 9)

$$p_i = F_i * I \quad \text{và} \quad r_i = I - p_i$$
*   **Bản chất:** Hệ thống lọc dải thông (Sub-band filter).
*   **Tại sao lấy $I - p_i$?** Khi bạn làm mờ ảnh ($p_i$), những chi tiết nhỏ (cạnh sắc) sẽ biến mất. Phép trừ này giúp chúng ta "thu hoạch" lại chính xác những gì đã mất.
*   **Ý nghĩa:** Tái tạo ảnh hợp nhất bằng công thức $I_F = \sum r_i + p_{\text{max}}$. Ta cộng dồn các "phần dư chi tiết" từ nhiều cấp độ khác nhau để tạo ra một bức ảnh cực kỳ giàu thông tin.

---

## 3. PHÂN TÍCH CÔNG THỨC SPARSE REPRESENTATION (SLIDE 12)

$$V = DS$$
*   **Tại sao gọi là "Sparse" (Thưa)?** Vì ta giải bài toán tối ưu hóa: $\min \|S\|_0$ (Số lượng phần tử khác 0 tối thiểu).
*   **Lô-gic:** Một tín hiệu tự nhiên luôn có thể được giải thích bằng một vài "nguyên tử" (atoms) cơ bản nhất. 
*   **Ý nghĩa:** Trong ảnh MRI, các cấu trúc giải phẫu lặp đi lặp lại. Việc ép $S$ thưa giúp loại bỏ nhiễu (noise là tín hiệu không bao giờ biểu diễn thưa được). Đây là cách "lọc nhiễu thông minh" vượt xa các bộ lọc truyền thống.

---

## 4. PHÂN TÍCH QUY TẮC MỜ (SLIDE 16)

$$\mu_{w_i}(y) = e^{-\frac{(y-\mu_i)^2}{2\sigma_i^2}}$$
*   **Tại sao dùng hàm mũ ($e$)?** Để tạo ra sự suy giảm mượt mà. 
*   **Biến $\sigma$ (Sigma):** Độ lệch chuẩn quyết định "độ rộng" của niềm tin. $\sigma$ lớn nghĩa là ta chấp nhận phạm vi sai lệch rộng hơn.
*   **Ý nghĩa:** Tại biên của một khối u, ta không biết chắc pixel đó là "bệnh" hay "lành". Hàm Gaussian giúp tính toán trọng số trung bình mượt mà, tránh hiện tượng ảnh bị "vỡ hạt" hay "đứt gãy" tại các biên.

---

## 5. PHÂN TÍCH THỐNG KÊ PCA & HMT (SLIDE 17, 18)

### PCA - Ma trận Hiệp phương sai (Covariance Matrix)
$$\text{Cov}(C_1,C_2) = E\left[(C_1-\mu_1)(C_2-\mu_2)^T\right]$$
*   **Tại sao nhân $(C_1-\mu)(C_2-\mu)^T$?** Để đo lường mức độ ảnh MRI và CT "đi cùng nhau". Nếu chúng cùng tăng/giảm đồng thời tại một vị trí, Covariance lớn.
*   **Ý nghĩa:** Tìm ra các hướng (components) mà tại đó dữ liệu biến đổi mạnh nhất (chứa nhiều thông tin nhất).

### PCA - Trọng số ($w$)
$$w_1 = \frac{V_{1,1}}{V_{1,1}+V_{2,1}}$$
*   **Tại sao là Eigenvalues ($V$)?** Trị riêng $V$ đại diện cho "năng lượng thông tin". Trọng số $w_1$ lớn hơn nghĩa là ảnh 1 cung cấp nhiều thông tin đặc trưng hơn.

---

## 6. PHÂN TÍCH CÔNG THỨC THỊ GIÁC HVS (SLIDE 19)

### Visibility $V(x,y)$
$$V(x,y) = \sum_{m,n} \frac{|I(x,y) - \mu|}{\mu^\alpha}$$
*   **Tại sao có số mũ $\alpha \approx 0.6-0.7$?** Đây là con số thực nghiệm từ **Định luật Stevens**. Mắt người cảm nhận độ sáng không theo đường thẳng mà theo hàm mũ. 
*   **Mẫu số $\mu^\alpha$:** Đây là phép chuẩn hóa độ tương phản nhạy cảm (CSF). Mắt người nhạy với sự thay đổi trong vùng tối hơn vùng sáng.

### SUSAN Feature $n(r_o)$
$$n(r_o) = \sum \exp\!\left(-\frac{(r-r_o)^2}{2\eta^2}\right) \exp\!\left(-\frac{(F(r)-F(r_o))^2}{T^2}\right)$$
*   **Tại sao có 2 hàm $\exp$ nhân nhau?** 
    1.  Hàm thứ nhất: Ưu tiên các pixel ở **GẦN** (về khoảng cách hình học). 
    2.  Hàm thứ hai: Ưu tiên các pixel có **GIÁ TRỊ MÀU GIỐNG NHAU**. 
*   **Ý nghĩa:** Chỉ những pixel vừa gần, vừa giống nhau mới được coi là cùng một cấu trúc. Đây là lõi của thuật toán giữ cạnh (edge-preserving).

---

## 7. PHÂN TÍCH CHỈ SỐ ĐÁNH GIÁ (SLIDE 21)

### Spatial Frequency (SF)
$$SF = \sqrt{RF^2 + CF^2}$$
*   **Tại sao là Pythagoras?** 
    *   $RF$ (Row Frequency): Đo sự thay đổi theo hàng ngang.
    *   $CF$ (Column Frequency): Đo sự thay đổi theo cột dọc.
*   **Ý nghĩa:** $SF$ tổng hợp toàn bộ năng lượng cạnh của ảnh. $SF$ càng cao, ảnh càng sắc nét (nhiều detail).

---
**HUẤN LUYỆN THUYẾT TRÌNH:**
*   **Công thức càng khó, hãy nói về "Năng lượng":** Trong xử lý ảnh, mọi công thức đều là cách để tìm xem "Năng lượng nằm ở đâu".
*   **Ý nghĩa y tế:** Hãy luôn kết luận: "Nhờ công thức này, bác sĩ có thể nhìn rõ hơn các mạch máu li ti mà ảnh gốc không thấy được".
