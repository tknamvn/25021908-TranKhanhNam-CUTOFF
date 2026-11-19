# NHẬT KÝ SỬ DỤNG AI (AI PROMPTS LOG)

* **Học phần:** Cấu trúc dữ liệu và giải thuật (DSA) - UET, ĐHQGHN
* **Sinh viên:** Trần Khánh Nam - MSSV: 25021908
* **Công cụ hỗ trợ:** Gemini AI (Google DeepMind)

---

### Prompt 1: Hỏi về cơ sở lý thuyết CUTOFF 7 vs 10
**User:**
> "Chào bạn, mình đang làm bài tập môn DSA. Trong slide Mergesort và Quicksort, mình thấy tài liệu cũ và Java 6 quy định CUTOFF sang Insertion Sort là 7, nhưng giáo trình mới của Sedgewick lại ghi là khoảng 10. Cho mình hỏi tại sao lại có sự khác biệt giữa 2 con số này?"

**AI Response:**
> Sự khác biệt này bắt nguồn từ lịch sử phát triển phần cứng và cách tiếp cận:
> 1. **Số 7 (cũ):** Xuất phát từ nghiên cứu kinh điển năm 1993 của Jon Bentley và Douglas McIlroy (*"Engineering a Sort Function"*). Họ tối ưu hàm `qsort` của C trên các dòng máy tính thập niên 80-90 (cache nhỏ, chi phí gọi hàm so sánh lớn). Kết quả thực nghiệm khi đó chỉ ra $M = 7$ là tối ưu nhất. Thư viện Java 6 kế thừa con số này cho `Arrays.sort`.
> 2. **Số 10 (mới):** Trong sách *Algorithms 4th Edition*, Sedgewick & Wayne đề xuất $\approx 10$ vì CPU hiện đại có cache L1/L2 lớn hơn, bộ dự đoán rẽ nhánh (branch predictor) và pipeline sâu hơn, giúp việc dịch chuyển phần tử liên tiếp trong Insertion sort rẻ hơn. Ngoài ra, đồ thị thời gian theo cutoff tạo thành một "đáy phẳng" từ 7 đến 15 (chênh lệch rất nhỏ), nên con số 10 được chọn như một quy tắc tròn chục dễ nhớ trong giảng dạy.

---

### Prompt 2: Tham khảo phương pháp đo thời gian chính xác trong Java
**User:**
> "Mình đang chuẩn bị viết code Java để benchmark đo thời gian thực tế của Mergesort và Quicksort khi thay đổi CUTOFF từ 1 đến 50. Trong Java, làm thế nào để đo chính xác mà không bị ảnh hưởng bởi JIT Compiler hay Garbage Collection?"

**AI Response:**
> Để kết quả đo đạc phản ánh đúng hiệu năng thuật toán, bạn nên chú ý các điểm sau:
> 1. **Warm-up JIT:** Trước khi bấm giờ chính thức, hãy cho chạy lặp vài lần trên mảng mẫu để HotSpot JIT (C1/C2) biên dịch bytecode sang mã máy native.
> 2. **Dùng System.nanoTime():** Tránh dùng `System.currentTimeMillis()` vì độ phân giải thấp. Chia cho `1e6` để đổi ra mili-giây.
> 3. **Kiểm soát biến độc lập:** Sinh 1 mảng dữ liệu gốc rồi dùng `.clone()` để các thuật toán và các mức cutoff đều chạy trên cùng một bộ dữ liệu giống hệt nhau.
> 4. **Chạy lặp nhiều lần (Multiple trials):** Thực hiện đo khoảng 10–12 lần rồi lấy giá trị trung bình để triệt tiêu nhiễu từ hệ điều hành.

---

### Prompt 3: Tối ưu vòng lặp Insertion Sort trên mảng con
**User:**
> "Bạn gợi ý giúp mình cách cài đặt Insertion Sort trên đoạn con `a[lo..hi]` sao cho tối ưu số phép gán nhất để mình nhúng vào hàm đệ quy."

**AI Response:**
> Thay vì hoán vị liên tục (`exch`) tốn 3 phép gán cho mỗi bước dịch chuyển, bạn nên dùng kỹ thuật dịch chuyển nửa bước (half-exchange / shift):
> ```java
> public static void insertionSort(Comparable[] a, int lo, int hi) {
>     for (int i = lo + 1; i <= hi; i++) {
>         Comparable val = a[i];
>         int j = i;
>         while (j > lo && a[j - 1].compareTo(val) > 0) {
>             a[j] = a[j - 1];
>             j--;
>         }
>         a[j] = val;
>     }
> }
> ```
> Cách này chỉ gán giá trị `val` một lần duy nhất khi tìm được đúng vị trí chèn.

---

### Prompt 4: Hỏi về cách giải thích hiện tượng khi chạy thực nghiệm
**User:**
> "Mình đã chạy thử nghiệm với mảng 500,000 phần tử. Kết quả cho thấy:
> - Với mảng ngẫu nhiên: CUTOFF từ 7 đến 15 thời gian chạy gần như tương đương nhau (lệch nhau chưa tới 2%).
> - Nhưng với mảng gần như đã sắp xếp (chỉ xáo trộn 5%): khi tăng CUTOFF lên 30, 40 thì thời gian lại chạy nhanh hơn hẳn so với CUTOFF = 7 hay 10.
> Bạn giải thích giúp mình hiện tượng này dưới góc độ lý thuyết được không?"

**AI Response:**
> Kết quả đo đạc của bạn rất chuẩn xác và phản ánh đúng 2 nguyên lý quan trọng:
> 1. **Đặc trưng đáy phẳng (Flat-bottom plateau):** Trên dữ liệu ngẫu nhiên, từ $M=7$ đến $M=15$, chi phí tiết kiệm được từ việc giảm đệ quy xấp xỉ cân bằng với chi phí tăng thêm của Insertion sort. Do đó đường cong hiệu năng có đáy rất phẳng, chứng minh cả 7 và 10 đều là các lựa chọn tốt.
> 2. **Tính thích ứng (Adaptive) của Insertion Sort:** Với mảng gần như đã sắp xếp, số cặp nghịch thế (inversions) rất ít. Lúc này Insertion sort chạy với độ phức tạp tiệm cận $O(N)$ thay vì $O(N^2)$. Khi nâng CUTOFF lên 30–50, bạn đã chuyển giao phần lớn khối lượng công việc cho Insertion sort xử lý tuyến tính cực nhanh, đồng thời triệt tiêu gần như hoàn toàn chi phí đệ quy và phân hoạch của Quicksort/Mergesort.

---

### Prompt 5: Gợi ý cách trực quan hóa đồ thị
**User:**
> "Mình muốn vẽ biểu đồ đường biểu diễn sự thay đổi của thời gian theo cutoff để đưa vào báo cáo thì nên dùng thư viện nào đơn giản, dễ xem nhất?"

**AI Response:**
> Cách gọn gàng và đẹp nhất là bạn tạo một file HTML đơn giản rồi nhúng thư viện **Chart.js** qua CDN:
> ```html
> <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
> <canvas id="myChart"></canvas>
> ```
> Bạn chỉ cần truyền mảng `cutoffs` làm trục X và mảng thời gian trung bình làm trục Y. File HTML này có thể mở trực tiếp bằng bất kỳ trình duyệt nào mà không cần cài thêm Python hay môi trường phức tạp.
