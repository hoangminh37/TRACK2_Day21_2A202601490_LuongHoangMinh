# Báo Cáo Lab Day 21 - CI/CD cho AI Systems

<!--
HƯỚNG DẪN - đọc rồi XÓA TOÀN BỘ các khối chú thích này sau khi điền xong:

  - Giới hạn: KHÔNG QUÁ 1 TRANG A4, tương đương khoảng 450 - 550 từ nội dung.
  - Chỉ điền vào các chỗ ___ và các ô trong bảng. Không thêm mục mới.
  - Viết bằng câu hoàn chỉnh, không gạch đầu dòng cụt lủn.
  - Kiểm tra độ dài sau khi đã xóa hết chú thích:
        wc -w nop-bai/bao-cao.md
    và xem trước bản in bằng cách mở file trên GitHub rồi Ctrl+P / Cmd+P.
-->

| | |
|---|---|
| Họ và tên | Lương Hoàng Minh |
| MSSV | 2A202601490 |
| Lớp / Khóa | K4 |
| Repo GitHub | https://github.com/hoangminh37/TRACK2_Day21_2A202601490_LuongHoangMinh |
| Ngày nộp | 21/08/2026 |

---

## 1. Bộ Siêu Tham Số Đã Chọn và Lý Do

| Lần chạy | n_estimators | learning_rate | max_depth | f1_score | accuracy |
|---|---|---|---|---|---|
| 1 | 100 | 0.1 | 3 | 0.7109 | 0.8780 |
| 2 | 50 | 0.05 | 2 | 0.6051 | 0.8460 |
| 3 | 200 | 0.1 | 5 | 0.7149 | 0.8740 |
| 4 | 150 | 0.1 | 4 | 0.7156 | 0.8760 |

**Bộ siêu tham số đã chọn:** `n_estimators=150`, `learning_rate=0.1`, `max_depth=4`.

**Lý do:** Bộ siêu tham số này đạt điểm f1_score cao nhất trên tập holdout (0.7156), vượt qua ngưỡng chất lượng 0.65. Đáng chú ý, Lần chạy 1 có accuracy cao nhất (0.8780) nhưng f1_score lại thấp hơn Lần chạy 4 (0.7109 so với 0.7156), chứng tỏ accuracy bị chi phối bởi lớp đa số và không phản ánh đúng chất lượng phân loại lớp thiểu số. Việc tăng n_estimators lên 150 kết hợp max_depth=4 giúp mô hình tối ưu hóa khả năng dự đoán mà không bị overfitting.

---

## 2. Vì Sao Ngưỡng Chất Lượng Đặt Trên F1 Chứ Không Phải Accuracy

Tập dữ liệu Adult Income bị mất cân bằng lớp nghiêm trọng khi chỉ có 24.8% mẫu thuộc lớp thu nhập cao (>50K USD). Một mô hình ngây thơ luôn dự đoán "thu nhập thấp" cho mọi đầu vào vẫn đạt accuracy lên tới 75.2%, tạo cảm giác đánh lừa rằng mô hình hoạt động tốt nhưng thực tế hoàn toàn vô dụng vì không phát hiện được bất kỳ trường hợp thu nhập cao nào. Do đó, f1_score của lớp dương (trung bình điều hòa giữa Precision và Recall của lớp >50K) là thước đo chuẩn xác để đánh giá đúng năng lực phân loại thực tế. Khi tính toán f1_score, không sử dụng average="macro" hay average="weighted" vì các tùy chọn này bị lớp đa số kéo điểm lên cao, làm mất đi ý nghĩa của ngưỡng kiểm định chất lượng (quality gate).

---

## 3. Khó Khăn Gặp Phải và Cách Giải Quyết

| Khó khăn | Nguyên nhân | Cách giải quyết |
|---|---|---|
| Lỗi unpickle mô hình khi khởi động FastAPI trên VM | Phiên bản scikit-learn trên VM cài mới nhất (1.7.2) không tương thích với bản train (1.4.2). | Cài đặt cố định chính xác scikit-learn==1.4.2 trên VM để đồng nhất môi trường. |
| Xung đột cổng 5000 khi chạy MLflow UI trên macOS | Tính năng AirPlay Receiver của macOS mặc định chiếm dụng cổng 5000. | Chuyển MLflow UI sang cổng 5001 bằng cờ --port 5001. |
| Đánh giá sai chất lượng mô hình do mất cân bằng lớp | Dữ liệu chỉ có 24.8% lớp dương khiến accuracy cao ảo dù mô hình vô dụng. | Thiết lập Quality Gate trên f1_score lớp dương (>= 0.65) thay vì accuracy. |

---

## 4. So Sánh Bước 2 và Bước 3 (bắt buộc, 2 - 3 câu)

<!-- Lấy số liệu từ bảng ở mục 3.6 của tasks/buoc-3.md. -->

| | f1_score | accuracy |
|---|---|---|
| Bước 2 (chỉ `train_batch1`) | ___ | ___ |
| Bước 3 (thêm `train_batch2`) | ___ | ___ |

**Nhận xét:** ___

<!--
Một câu trả lời trung thực kiểu "f1 giảm 0,01 vì dữ liệu mới cùng phân phối, không mang
thêm thông tin mới" được đánh giá cao hơn kết luận sai rằng thêm dữ liệu luôn tốt hơn.
-->

---

## 5. Phần Bonus Đã Thực Hiện (nếu có)

<!-- Xóa cả mục 5 nếu không làm bonus. Mỗi bonus tối đa 1 dòng. -->

- [ ] Bonus 1 - Tracking MLflow từ xa với DagsHub: ___
- [ ] Bonus 2 - Điều chỉnh ngưỡng quyết định: ___
- [ ] Bonus 3 - Báo cáo precision / recall tự động: ___
- [ ] Bonus 4 - Hoàn trả về phiên bản trước: ___
- [ ] Bonus 5 - Cảnh báo lệch lạc dữ liệu: ___
