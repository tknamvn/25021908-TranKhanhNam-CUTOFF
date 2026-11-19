# Báo cáo Thực nghiệm: Ngưỡng CUTOFF 7 vs 10 trong Mergesort và Quicksort

* **Học phần:** Cấu trúc dữ liệu và giải thuật (DSA) - Trường Đại học Công nghệ (UET), ĐHQGHN
* **Sinh viên:** Trần Khánh Nam - MSSV: 25021908
* **Tài liệu tham khảo chính:** *Algorithms*, 4th Edition (Robert Sedgewick, Kevin Wayne)

---

## 📌 Nội dung chính
Kho lưu trữ này chứa toàn bộ mã nguồn đo đạc thực nghiệm, dữ liệu benchmark và báo cáo phân tích đối chứng giữa **CUTOFF = 7** (Bentley-McIlroy 1993, Java 6) và **CUTOFF $\approx$ 10** (Sedgewick & Wayne 4th Ed.).

👉 **Xem báo cáo chi tiết đầy đủ tại:** [BAO_CAO_THI_NGHIEM_CUTOFF.md](BAO_CAO_THI_NGHIEM_CUTOFF.md)  
👉 **Xem đồ thị trực quan tương tác Chart.js:** [report.html](report.html)  
👉 **Dữ liệu đo đạc thực nghiệm:** [cutoff_benchmark_results.csv](cutoff_benchmark_results.csv)  
👉 **Nhật ký tương tác AI (AI Log):** [AI_LOG.md](AI_LOG.md)

---

## 🚀 Hướng dẫn biên dịch và chạy thực nghiệm
Yêu cầu: Java Development Kit (JDK 17+ hoặc JDK 21 LTS).

```bash
# Biên dịch mã nguồn
javac CutoffExperiment.java

# Chạy bài đo thực nghiệm
java CutoffExperiment
```

Sau khi chạy xong, chương trình sẽ tự động:
1. In bảng phân tích so sánh thời gian thực thi của Mergesort và Quicksort (Strategy A và B) trên màn hình terminal.
2. Cập nhật tập dữ liệu thô `cutoff_benchmark_results.csv`.
3. Tạo file `report.html` có biểu đồ Chart.js trực quan hóa đường cong hiệu năng.
