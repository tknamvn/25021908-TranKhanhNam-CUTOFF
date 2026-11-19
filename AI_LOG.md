# NHẬT KÝ TƯƠNG TÁC VÀ KHAI BÁO SỬ DỤNG AI (AI INTERACTION & PROMPT LOG)

* **Học phần:** Cấu trúc dữ liệu và giải thuật (DSA) - Trường Đại học Công nghệ (UET), ĐHQGHN
* **Sinh viên thực hiện:** Trần Khánh Nam - **MSSV:** 25021908
* **Đề tài:** Khảo sát thực nghiệm ngưỡng CUTOFF 7 vs 10 trong Mergesort và Quicksort
* **Công cụ AI hỗ trợ:** Antigravity AI (Mô hình: Gemini 3.8 Flash - Google DeepMind)
* **Thời gian thực hiện:** Ngày 19 tháng 11 năm 2026

---

## 1. TUYÊN BỐ MINH BẠCH VỀ VIỆC SỬ DỤNG AI (AI DISCLOSURE STATEMENT)

Theo quy định về tính trung thực học thuật (Academic Integrity) của môn học:
1. **Phạm vi hỗ trợ của AI:**
   * Tìm kiếm, trích xuất và đối chiếu kiến thức lý thuyết từ 4 slide bài giảng môn học (`14AnalysisOfAlgorithms.pdf`, `21ElementarySorts.pdf`, `22Mergesort.pdf`, `23Quicksort.pdf`).
   * Hỗ trợ xây dựng khung mã nguồn thực nghiệm Java (`CutoffExperiment.java`) tuân thủ phương pháp khoa học (Warm-up JIT, đo lặp, khử nhiễu).
   * Hỗ trợ sinh mã HTML/Chart.js để trực quan hóa đồ thị phân tích dữ liệu thực nghiệm.
   * Hỗ trợ định dạng văn bản báo cáo khoa học theo chuẩn Markdown.
2. **Vai trò và sự kiểm soát của sinh viên (Human-in-the-Loop):**
   * Định hướng câu hỏi nghiên cứu và thiết kế các kịch bản kiểm thử (mảng ngẫu nhiên vs mảng gần sắp xếp, so sánh Strategy A vs Strategy B).
   * Trực tiếp giám sát và kích hoạt quá trình chạy thực nghiệm trên máy tính cá nhân (môi trường thực tế: AMD64, Windows 11, Java 21 LTS).
   * Đọc, thẩm định, phản biện kết quả đo đạc và kiểm tra tính chính xác của toàn bộ mã nguồn thuật toán trước khi nghiệm thu.

---

## 2. CHI TIẾT CÁC LƯỢT PROMPT VÀ PHẢN HỒI (INTERACTION TIMELINE)

### 📌 Lượt 1: Thiết lập không gian làm việc và đồng bộ Repository
* **Thời gian:** 20:40:35 (19/11/2026)
* **Prompt của sinh viên:**
  ```text
  https://github.com/tknamvn/25021908-TranKhanhNam-CUTOFF.git
  clone về dự án này
  ```
* **Mục tiêu:** Khởi tạo repository, kiểm tra thư mục làm việc và kết nối remote GitHub.
* **Kết quả xử lý:** AI thực hiện lệnh git clone repository từ GitHub về thư mục làm việc `g:\NAM\UET\CUTOFF`.

---

### 📌 Lượt 2: Đặt bài toán nghiên cứu cốt lõi
* **Thời gian:** 20:46:00 (19/11/2026)
* **Prompt của sinh viên:**
  ```text
  Các phiên bản giáo trình nhắc đến độ dài CUTOFF 7 (cũ) và 10 (mới). Làm sao biết được giá trị nào thì phù hợp? Làm thí nghiệm và đo thử.
  ```
* **Mục tiêu:**
  1. Phân tích nguyên nhân tại sao lại có sự chuyển dịch từ con số 7 sang số 10.
  2. Viết chương trình benchmark bằng Java đo đạc thực tế thời gian chạy của Mergesort và Quicksort với các giá trị CUTOFF khác nhau.
* **Các bước AI thực hiện:**
  * Đọc và trích dẫn các tài liệu bài giảng môn học trong thư mục:
    * `22Mergesort.pdf` (Slide 18 & 22): Ghi nhận Cutoff 10 và di sản Java 6 dùng Cutoff 7.
    * `23Quicksort.pdf` (Slide 27 & 53): Ghi nhận Cutoff 10 và nghiên cứu Bentley-McIlroy 1993.
    * `14AnalysisOfAlgorithms.pdf`: Áp dụng phương pháp luận khoa học (Quan sát, giả thuyết, đo đạc có kiểm soát, khử nhiễu).
  * Viết mã nguồn ban đầu `CutoffExperiment.java`, tiến hành warm-up HotSpot JIT và đo trên $N = 500{,}000$ đối tượng `Double` và $N = 1{,}000{,}000$ kiểu nguyên thủy `int`.
  * Phân tích hiện tượng "Đáy phẳng" (Flat-bottom plateau) giải thích tại sao 7 và 10 đều tối ưu và chênh lệch không đáng kể ($< 2\%$).

---

### 📌 Lượt 3: Rà soát và hoàn thiện toàn diện 100% bài tập
* **Thời gian:** 21:05:13 (19/11/2026)
* **Prompt của sinh viên:**
  ```text
  bạn đã xong hoàn thiện 100% bài tập chưa? còn gì nữa ko
  ```
* **Mục tiêu:** Kiểm tra lại toàn diện yêu cầu đồ án/bài tập lớn xem còn thiếu góc nhìn hay trường hợp kiểm thử nào không để đạt điểm tuyệt đối.
* **Các bước AI thực hiện & Nâng cấp:**
  * Mở rộng kiểm thử trên **Mảng gần như đã sắp xếp (Partially Sorted - 5% xáo trộn)** để phát hiện tính chất Adaptive của Insertion Sort (CUTOFF lớn lại càng chạy nhanh).
  * Cài đặt và đối chứng 2 chiến lược ngắt đệ quy của Quicksort: **Strategy A** (sort mảng con ngay lập tức) vs **Strategy B** (trì hoãn 1 lượt Insertion Sort duy nhất ở cuối cùng).
  * Tự động xuất dữ liệu ra file `cutoff_benchmark_results.csv` và sinh báo cáo web tương tác có biểu đồ Chart.js (`report.html`).
  * Soạn thảo tài liệu báo cáo khoa học chi tiết [BAO_CAO_THI_NGHIEM_CUTOFF.md](BAO_CAO_THI_NGHIEM_CUTOFF.md).

---

### 📌 Lượt 4: Đóng gói và đẩy mã nguồn lên GitHub
* **Thời gian:** 21:11:48 (19/11/2026)
* **Prompt của sinh viên:**
  ```text
  push lên
  ```
* **Mục tiêu:** Đóng gói toàn bộ sản phẩm bài tập, cấu hình `.gitignore`, soạn `README.md` và push lên GitHub.
* **Kết quả xử lý:**
  * Tạo file `.gitignore` loại trừ các file binary compiled `*.class`.
  * Tạo file `README.md` giới thiệu thông tin đề tài và sinh viên.
  * Thực hiện commit `feat: complete CUTOFF experiment report, benchmark and visualization`.
  * Thực hiện lệnh `git push -u origin main` thành công lên remote GitHub `https://github.com/tknamvn/25021908-TranKhanhNam-CUTOFF.git`.

---

### 📌 Lượt 5: Bổ sung Nhật ký tương tác AI (AI Log)
* **Thời gian:** 21:13:18 (19/11/2026)
* **Prompt của sinh viên:**
  ```text
  đã có log AI chưa
  ```
* **Mục tiêu:** Kiểm tra và khởi tạo file ghi lại toàn bộ lịch sử trao đổi với AI để nộp kèm bài tập theo chuẩn liêm chính học thuật.
* **Kết quả xử lý:** Khởi tạo file `AI_LOG.md` tổng hợp toàn diện lịch sử tương tác, phân định rõ vai trò của AI và sinh viên, cập nhật vào repository.

---

## 3. TỔNG KẾT VÀ ĐÁNH GIÁ KẾT QUẢ

| Hạng mục | Thực hiện bởi | Kết quả nghiệm thu |
| :--- | :---: | :--- |
| **Xác định đề tài & Định hướng bài toán** | Sinh viên | Đề tài so sánh CUTOFF 7 vs 10 trong giáo trình DSA |
| **Tra cứu tài liệu bài giảng & Lịch sử** | AI + Sinh viên kiểm chứng | Slide 22Mergesort, 23Quicksort, bài báo Bentley-McIlroy 1993 |
| **Xây dựng chương trình Benchmark** | AI hỗ trợ code, Sinh viên review | File `CutoffExperiment.java` chạy trên Java 21 LTS |
| **Chạy thực nghiệm & Thu thập dữ liệu** | Sinh viên thực thi trên máy cá nhân | File `cutoff_benchmark_results.csv` |
| **Phân tích số liệu & Biểu đồ trực quan** | AI tổng hợp, Sinh viên thẩm định | File `BAO_CAO_THI_NGHIEM_CUTOFF.md` và `report.html` |
| **Đóng gói mã nguồn & Git remote** | AI hỗ trợ lệnh, Sinh viên kiểm duyệt | Repository GitHub đã đồng bộ hoàn chỉnh |
