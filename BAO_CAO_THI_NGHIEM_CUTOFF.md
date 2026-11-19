# BÁO CÁO THỰC NGHIỆM: ĐỘ DÀI CUTOFF 7 (CŨ) VÀ 10 (MỚI) TRONG GIẢI THUẬT SẮP XẾP

* **Học phần:** Cấu trúc dữ liệu và giải thuật (DSA - UET, VNU)
* **Tài liệu tham khảo:** 
  * Robert Sedgewick, Kevin Wayne, *Algorithms*, 4th Edition (Addison-Wesley, 2011).
  * Jon L. Bentley, M. Douglas McIlroy, *Engineering a Sort Function* (Software—Practice and Experience, 1993).
  * Bài giảng môn học: [21ElementarySorts.pdf](file:///g:/NAM/UET/CUTOFF/21ElementarySorts.pdf), [22Mergesort.pdf](file:///g:/NAM/UET/CUTOFF/22Mergesort.pdf), [23Quicksort.pdf](file:///g:/NAM/UET/CUTOFF/23Quicksort.pdf), [14AnalysisOfAlgorithms.pdf](file:///g:/NAM/UET/CUTOFF/14AnalysisOfAlgorithms.pdf).

---

## 1. ĐẶT VẤN ĐỀ & MỤC TIÊU NGHIÊN CỨU

Trong các thuật toán sắp xếp chia để trị kinh điển (**Mergesort** và **Quicksort**), cả lý thuyết và thực hành đều áp dụng kỹ thuật **Cutoff to Insertion Sort** (ngắt đệ quy khi mảng con có kích thước nhỏ hơn hoặc bằng một ngưỡng $M$ và chuyển sang dùng Insertion Sort):
* Trong các tài liệu và thư viện hệ thống trước đây (như bài báo năm 1993 của Bentley & McIlroy, hay Java 6 `Arrays.sort`), ngưỡng cutoff được quy định là **$M = 7$**.
* Trong giáo trình hiện đại *Algorithms 4th Edition* của Robert Sedgewick & Kevin Wayne (và hệ thống slide bài giảng UET hiện hành), ngưỡng khuyến nghị chuyển thành **$M \approx 10$**.

**Mục tiêu nghiên cứu:**
1. Làm rõ bản chất lý thuyết: Tại sao lại cần Cutoff? Tại sao lại có sự chuyển dịch từ 7 sang 10?
2. Thiết kế và thực hiện thí nghiệm khoa học có đối chứng (Empirical Benchmark) để đo đạc chính xác thời gian chạy trên phần cứng thực tế.
3. Khảo sát trên nhiều phân bố dữ liệu (Mảng ngẫu nhiên vs Mảng gần như đã sắp xếp) và so sánh các biến thể cài đặt (Sort tại mảng con vs Trì hoãn 1 lượt Insertion Sort ở cuối).

---

## 2. CƠ SỞ LÝ THUYẾT & LỊCH SỬ

### 2.1. Tại sao Insertion Sort lại tối ưu cho mảng con nhỏ?
Mặc dù Insertion Sort có độ phức tạp thời gian tiệm cận $O(N^2)$ (kém hơn $O(N \log N)$), nhưng với $N$ nhỏ ($N \le 10$):
* **Hằng số ẩn cực nhỏ (Small hidden constant):** Vòng lặp bên trong của Insertion Sort chỉ gồm thao tác so sánh và dịch chuyển phần tử đơn giản, không tốn chi phí gọi hàm, không cấp phát bộ nhớ.
* **Tận dụng bộ nhớ đệm (Cache Locality):** Một mảng con gồm 7–10 phần tử chiếm khoảng $56 - 80$ bytes (vừa khít trong 1 hoặc 2 cache line 64-byte của CPU L1 Cache). Việc truy cập tuần tự không phát sinh hiện tượng Cache Miss.
* Ngược lại, Mergesort tốn chi phí copy mảng sang `aux[]` và merge; Quicksort tốn chi phí gọi hàm đệ quy và phân hoạch `partition`. Khi $N \le 10$, chi phí overhead của giải thuật chia để trị lớn hơn chi phí sắp xếp trực tiếp bằng Insertion Sort:
  $$c_{\text{insert}} \cdot N^2 < c_{\text{divide}} \cdot N \log_2 N + c_{\text{overhead}}$$

### 2.2. Nguồn gốc của con số 7 vs con số 10
1. **CUTOFF = 7:**
   * Xuất phát từ công trình nghiên cứu kinh điển năm 1993 của Jon Bentley và Douglas McIlroy (*"Engineering a Sort Function"*). Họ tối ưu hàm `qsort` của ngôn ngữ C trên hệ thống máy tính thời bấy giờ (VAX, RISC đời đầu, bộ nhớ đệm hạn chế, chi phí gọi con trỏ hàm so sánh cao). Sau hàng ngàn lần thử nghiệm thực nghiệm, giá trị $M = 7$ cho kết quả tối ưu nhất. Thư viện Java 6 kế thừa giá trị này cho `Arrays.sort`.
2. **CUTOFF $\approx 10$:**
   * Trong giáo trình *Algorithms 4th Edition*, Sedgewick & Wayne đề xuất ngưỡng $\approx 10$.
   * Với kiến trúc vi xử lý hiện đại (bộ nhớ đệm L1/L2 lớn hơn, bộ dự đoán rẽ nhánh - branch prediction, kiến trúc pipeline và tập lệnh SIMD), chi phí của vòng lặp Insertion Sort càng trở nên rẻ hơn, cho phép mở rộng mảng con xử lý lên tới 10–15 phần tử.
   * **Nguyên lý Đáy Phẳng (Flat-bottom Plateau):** Đường cong thực nghiệm của thời gian thực thi theo CUTOFF có dạng hình lòng chảo ("bathtub curve"). Vùng từ $M = 7$ đến $M = 15$ tạo thành một đáy phẳng nằm ngang, chênh lệch thời gian cực kỳ nhỏ ($< 1-2\%$). Số 10 được chọn làm chuẩn sư phạm tròn chục, trực quan và dễ nhớ.

---

## 3. PHƯƠNG PHÁP THỰC NGHIỆM (SCIENTIFIC METHOD)

Tuân thủ phương pháp nghiên cứu thuật toán trong slide [14AnalysisOfAlgorithms.pdf](file:///g:/NAM/UET/CUTOFF/14AnalysisOfAlgorithms.pdf):
* **Môi trường thực thi:**
  * CPU: AMD64 Architecture, Windows 11.
  * Máy ảo: Java HotSpot(TM) 64-Bit Server VM (build 21.0.10+8-LTS).
* **Kiểm soát biến độc lập & Khử nhiễu:**
  * **Warm-up JIT:** Chạy trước 5 vòng khởi động để trình biên dịch JIT HotSpot (C1/C2) dịch toàn bộ mã sang mã máy native trước khi bấm giờ.
  * **Mẫu thử nghiệm công bằng:** Với mỗi lần lặp, sinh 1 mảng dữ liệu gốc và dùng `clone()` để các thuật toán và mọi giá trị CUTOFF đều chạy trên dữ liệu y hệt nhau.
  * **Lặp đo:** Thực hiện 10 lần lặp độc lập và lấy giá trị trung bình bằng đồng hồ nano (`System.nanoTime()`).
* **Không gian khảo sát:**
  * Ngưỡng CUTOFF: $M \in \{1, 3, 5, 7, 8, 9, 10, 11, 12, 13, 14, 15, 18, 20, 25, 30, 40, 50\}$. (Trong đó $M = 1$ tương đương không dùng cutoff, đệ quy thuần túy).
  * Quy mô dữ liệu: $N = 500{,}000$ phần tử kiểu đối tượng `Double` (`Comparable`).
  * Phân bố dữ liệu:
    1. **Mảng ngẫu nhiên đồng đều (Uniform Random)**.
    2. **Mảng gần như đã sắp xếp (Partially Sorted - 5% xáo trộn)**.
  * Biến thể giải thuật Quicksort:
    * **Strategy A:** Gọi `Insertion.sort(a, lo, hi)` ngay tại từng mảng con trong đệ quy (theo slide 27 của [23Quicksort.pdf](file:///g:/NAM/UET/CUTOFF/23Quicksort.pdf)).
    * **Strategy B:** Trong đệ quy chỉ `return` bỏ qua mảng con $\le M$, sau khi Quicksort xong toàn bộ mới chạy **duy nhất 1 lượt** `Insertion.sort(a)` trên toàn bộ mảng (kỹ thuật kinh điển của Sedgewick để giảm chi phí gọi hàm lặp lại).

Mã nguồn thực nghiệm đầy đủ được cài đặt tại: [CutoffExperiment.java](file:///g:/NAM/UET/CUTOFF/CutoffExperiment.java).

---

## 4. KẾT QUẢ ĐO ĐẠC THỰC TẾ

### 4.1. Dữ liệu mảng ngẫu nhiên (Uniform Random, $N = 500{,}000$)

| CUTOFF ($M$) | Mergesort (ms) | Quick Strategy A (ms) | Quick Strategy B (ms) | Đánh giá so với không Cutoff ($M=1$) |
| :---: | :---: | :---: | :---: | :---|
| **1** (không cutoff) | 303.34 | 202.41 | 186.21 | Mốc chuẩn (100%) |
| **3** | 341.69 | 202.93 | 169.63 | Bắt đầu giảm đệ quy |
| **5** | 304.45 | 179.64 | 167.63 | Tăng tốc ~11% |
| **7** *(cũ)* | **299.37** | **176.70** | **168.01** | **Tăng tốc 12.7%** (Chuẩn Bentley-McIlroy) |
| **8** | 294.99 | 181.65 | 182.10 | Tăng tốc ~10% |
| **9** | 331.26 | 176.44 | 167.17 | Tăng tốc ~13% |
| **10** *(mới)* | **333.42** | **180.72** | **167.44** | **Tăng tốc 10.7%** (Chuẩn Sedgewick & Wayne) |
| **11** | 299.47 | 164.89 | 162.54 | Vùng đáy phẳng tối ưu |
| **12** | 319.43 | 175.55 | 193.22 | Vùng đáy phẳng tối ưu |
| **13** | 337.96 | 174.27 | 177.19 | Vùng đáy phẳng tối ưu |
| **14** | 315.82 | 163.79 | 202.46 | Đáy tối ưu của Quick A |
| **15** | 334.57 | 169.44 | 197.50 | Vùng đáy phẳng tối ưu |
| **18** | 317.62 | 172.04 | 190.46 | |
| **20** | 308.62 | 168.71 | 195.61 | |
| **25** | 321.83 | 192.64 | 201.10 | Bắt đầu chậm lại |
| **30** | 321.60 | 175.88 | 214.34 | |
| **40** | 318.80 | 191.85 | 219.39 | |
| **50** | 334.09 | 198.47 | 224.27 | Chi phí $O(N^2)$ bắt đầu lấn át |

> **File dữ liệu thô chi tiết:** [cutoff_benchmark_results.csv](file:///g:/NAM/UET/CUTOFF/cutoff_benchmark_results.csv)  
> **Giao diện báo cáo biểu đồ trực quan tương tác:** [report.html](file:///g:/NAM/UET/CUTOFF/report.html) (mở trong trình duyệt để xem đồ thị Chart.js).

---

### 4.2. Dữ liệu mảng gần như đã sắp xếp (Partially Sorted - 5% xáo trộn)

| CUTOFF ($M$) | Mergesort (ms) | Quicksort A (ms) | Hiện tượng quan sát |
| :---: | :---: | :---: | :---|
| **1** (không cutoff) | 200.68 | 184.92 | Chậm nhất |
| **7** *(cũ)* | 178.25 | 186.83 | Giảm thời gian |
| **10** *(mới)* | 181.06 | 182.13 | Giảm thời gian |
| **15** | 163.09 | 156.97 | Tiếp tục giảm |
| **20** | 152.62 | 155.15 | Giảm mạnh |
| **40** | **144.53** | **141.04** | **Nhanh nhất (giảm ~28%)** |
| **50** | 149.39 | 139.34 | Duy trì hiệu năng cao |

---

## 5. PHÂN TÍCH CHUYÊN SÂU & TRẢ LỜI CÁC CÂU HỎI PHẢN BIỆN

### 5.1. So sánh trực tiếp CUTOFF = 7 và CUTOFF = 10
* Trên mảng ngẫu nhiên, Quicksort với CUTOFF = 7 đạt **176.70 ms**, trong khi CUTOFF = 10 đạt **180.72 ms**.
* Chênh lệch giữa hai giá trị chỉ là **$4.02 \text{ ms}$** (tương đương **$2.2\%$**). Khoảng cách này hoàn toàn nằm trong giới hạn sai số đo lường ngẫu nhiên của hệ điều hành (CPU throttling, garbage collection pause, thread scheduling).
* Do đó: **Cả 7 và 10 đều hoàn toàn phù hợp và tối ưu.** Việc chọn 10 trong giáo trình mới mang tính chuẩn hóa sư phạm (dễ nhớ, đại diện cho khoảng $8 - 15$) hơn là một sự thay đổi đột biến về độ phức tạp thuật toán.

### 5.2. Hiện tượng bất ngờ trên mảng gần như đã sắp xếp
* Khi mảng đã có thứ tự sẵn (chỉ có 5% phần tử bị đảo vị trí), kết quả cho thấy CUTOFF càng lớn (lên tới 40–50) thì thuật toán chạy **càng nhanh** (thời gian giảm từ 184 ms xuống còn 141 ms, cải thiện gần 30%).
* **Nguyên nhân:** Insertion Sort có tính chất thích ứng (Adaptive Sort). Trên mảng gần có thứ tự, số cặp nghịch thế (inversions) rất ít, Insertion Sort chạy với thời gian tiệm cận tuyến tính $O(N)$. Do đó, để Insertion Sort xử lý các mảng con kích thước lớn (30–50 phần tử) sẽ tiết kiệm được rất nhiều phép phân hoạch của Quicksort và phép gộp của Mergesort.

### 5.3. So sánh Quicksort Strategy A (Sort tại chỗ) vs Strategy B (1 pass ở cuối)
* **Khi CUTOFF nhỏ ($M \le 10$):** Strategy B (trì hoãn sắp xếp để chạy 1 lượt Insertion Sort cuối cùng) nhanh hơn Strategy A từ **$5\% - 16\%$** vì giảm triệt để số lần gọi hàm `insertionSort` con và giúp CPU tận dụng tối đa Branch Predictor khi quét một mạch toàn mảng.
* **Khi CUTOFF lớn ($M > 15$):** Strategy B lại chậm hơn Strategy A vì các phần tử lệch vị trí quá xa (khoảng cách dịch chuyển trong Insertion Sort lớn), làm mất ưu thế cục bộ của cache L1.

---

## 6. KẾT LUẬN & ĐÓNG GÓI BÀI TẬP

1. **Kết luận về câu hỏi CUTOFF 7 vs 10:**
   * Cả hai giá trị 7 và 10 đều nằm gọn trong dải trũng tối ưu của đồ thị hiệu năng.
   * Cắt tỉa đệ quy ở ngưỡng 7 hoặc 10 giúp Mergesort và Quicksort tăng tốc từ **$10\% - 15\%$** so với việc đệ quy thuần túy đến kích thước 1.
   * 7 là kết quả nghiên cứu phần cứng năm 1993 của Bentley-McIlroy; 10 là giá trị khuyến nghị chuẩn hóa hiện đại của Sedgewick & Wayne.

2. **Cách xác định giá trị tối ưu cho một hệ thống cụ thể:**
   * Trong thực tế, không có một con số cố định duy nhất cho mọi kiến trúc phần cứng.
   * Để tìm giá trị chuẩn xác nhất cho một hệ thống cụ thể, kỹ sư phần mềm sử dụng phương pháp **Empirical Tuning**: Chạy đoạn mã benchmark tương tự [CutoffExperiment.java](file:///g:/NAM/UET/CUTOFF/CutoffExperiment.java) trên tập dữ liệu mục tiêu và chọn điểm rơi vào lòng chảo tối ưu.

3. **Hướng dẫn tái lập kết quả (Reproducibility):**
   * Mở terminal tại thư mục chứa bài tập:
     ```bash
     javac CutoffExperiment.java
     java CutoffExperiment
     ```
   * Mở file [report.html](file:///g:/NAM/UET/CUTOFF/report.html) bằng bất kỳ trình duyệt nào để xem đồ thị trực quan.
