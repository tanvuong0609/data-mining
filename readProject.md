# Hotel Booking Demand - Phân tích tổng hợp

Tài liệu này mô tả notebook [`DO_AN/phan-tich-tong-hop.ipynb`](DO_AN/phan-tich-tong-hop.ipynb). Notebook kết hợp **Classification** và **Clustering** để nhận diện và giải thích rủi ro hủy booking trên bộ dữ liệu Hotel Booking Demand.

## 1. Mục tiêu và câu hỏi phân tích

Mục tiêu nghiệp vụ là hỗ trợ khách sạn nhận diện sớm booking có nguy cơ hủy và hiểu nhóm hành vi đứng sau rủi ro đó.

Notebook trả lời hai câu hỏi:

1. Những đặc điểm nào của booking liên quan đến khả năng hủy?
2. Các booking có thể được chia thành những nhóm hành vi có mức rủi ro hủy khác nhau hay không?

Biến mục tiêu:

- `is_canceled = 0`: booking không hủy.
- `is_canceled = 1`: booking bị hủy.

## 2. Dữ liệu sử dụng

Notebook chỉ đọc hai file trong `DO_AN/data/`:

```text
DO_AN/data/hotel_bookings_processed.csv
DO_AN/data/D1_metrics.csv
```

Thông tin dữ liệu sau Bài 1:

- Số dòng: `117.398`.
- Số cột: `43`.
- Không hủy: `62,51%`.
- Hủy phòng: `37,49%`.

`is_canceled` không được dùng làm đầu vào tạo cluster. Nhãn hủy chỉ được ghép lại sau khi phân cụm để đánh giá rủi ro của từng nhóm.

## 3. Kết quả kỹ thuật kế thừa

### 3.1. Classification từ Bài 2

| Mô hình | Accuracy | Precision | Recall | F1 | ROC-AUC |
|---|---:|---:|---:|---:|---:|
| Decision Tree | 0,7884 | 0,5788 | 0,8507 | 0,6888 | 0,8903 |
| Logistic Regression | 0,7481 | 0,5289 | 0,7821 | 0,6310 | 0,8420 |

Decision Tree được chọn vì có F1 và ROC-AUC cao hơn Logistic Regression.

### 3.2. Clustering từ Bài 4

| Thuật toán | Cấu hình | Số cụm | Silhouette | Davies-Bouldin | Noise |
|---|---|---:|---:|---:|---:|
| K-Means | `K=2` | 2 | 0,2226 | 2,5680 | 0% |
| DBSCAN | `eps=9.0`, `min_samples=88` | 3 | 0,4146 | 0,9486 | 1,72% |

DBSCAN được dùng cho phân tích chính vì có Silhouette cao hơn, Davies-Bouldin thấp hơn và có khả năng nhận diện noise.

## 4. Sample phân tích DBSCAN

Notebook tái tạo DBSCAN bằng đúng cấu hình của Bài 4:

```python
RANDOM_STATE = 42
DBSCAN_SAMPLE_N = 15000
DBSCAN_EPS = 9.0
min_samples = 88
```

Các bước chính:

1. Loại `is_canceled` và các cột không dùng để tạo cluster.
2. Chuyển tháng sang dạng số và one-hot encode các biến phân loại còn lại.
3. Chuẩn hóa bằng `StandardScaler`.
4. Lấy cố định 15.000 dòng với seed 42.
5. Chạy DBSCAN và gắn nhãn cluster trở lại từng booking.

Kết quả: 3 cluster và 258 điểm noise, tương ứng `1,72%` sample.

## 5. Rủi ro hủy theo cluster

Tỷ lệ hủy trung bình của sample là khoảng `37,79%`.

| Cluster | Số booking | Tỷ trọng | Tỷ lệ hủy | Lift so với sample |
|---|---:|---:|---:|---:|
| Noise | 258 | 1,72% | 21,71% | 0,57 |
| Cluster 0 | 14.509 | 96,73% | 38,07% | 1,01 |
| Cluster 1 | 134 | 0,89% | 27,61% | 0,73 |
| Cluster 2 | 99 | 0,66% | 52,53% | 1,39 |

`cluster_2` có tỷ lệ hủy cao nhất nhưng chỉ gồm 99 booking, vì vậy cần diễn giải thận trọng.

## 6. Tổng hợp Classification và Clustering

Đây là phần liên kỹ thuật chính của đồ án. Decision Tree sử dụng cấu hình tốt nhất từ Bài 2 và tạo dự đoán **5-fold out-of-fold** trên đúng 15.000 booking đã được DBSCAN gán cluster. Mỗi booking được dự đoán bởi mô hình chưa học chính booking đó.

Kết quả tổng thể trên sample đã align:

- F1: `0,7769`.
- ROC-AUC: `0,9159`.

### 6.1. Hiệu quả dự đoán theo cluster

| Cluster | N | Hủy thực tế | Xác suất dự đoán | Precision | Recall | F1 | FP | FN |
|---|---:|---:|---:|---:|---:|---:|---:|---:|
| Noise | 258 | 21,71% | 28,26% | 0,576 | 0,607 | 0,591 | 25 | 22 |
| Cluster 0 | 14.509 | 38,07% | 43,26% | 0,727 | 0,838 | 0,779 | 1.735 | 897 |
| Cluster 1 | 134 | 27,61% | 42,28% | 0,579 | 0,892 | 0,702 | 24 | 4 |
| Cluster 2 | 99 | 52,53% | 52,75% | 0,880 | 0,846 | 0,863 | 6 | 8 |

### 6.2. Quan hệ giữa hai kỹ thuật

- **Xác nhận:** `cluster_2` có cả tỷ lệ hủy thực tế và xác suất hủy dự đoán cao nhất.
- **Bổ sung:** Classification ước lượng rủi ro từng booking; Clustering giải thích booking thuộc nhóm hành vi nào.
- **Điểm cần kiểm tra:** `cluster_1` có xác suất dự đoán trung bình cao hơn đáng kể tỷ lệ hủy thực tế. Cần xem xét false positive và cỡ mẫu nhỏ trước khi áp dụng.

## 7. Feature importance và cluster profiling

Các đặc trưng quan trọng nhất của Decision Tree gồm:

- `deposit_type_Non Refund`
- `country_PRT`
- `lead_time`
- `total_of_special_requests`
- `market_segment_Online TA`
- `required_car_parking_spaces`
- `agent`
- `arrival_date_year`
- `customer_type_Transient`
- `previous_cancellations`

Các đặc trưng nổi bật ở cả Classification và Clustering:

- `lead_time`
- `total_of_special_requests`
- `adr`
- `booking_changes`
- `total_stay`

Nhóm biến này vừa có đóng góp trong Decision Tree, vừa thể hiện khác biệt giữa các cluster.

## 8. Kỹ thuật nâng cao: Bootstrap confidence interval

Các cluster nhỏ có tỷ lệ hủy kém ổn định. Notebook dùng 2.000 lần bootstrap để ước lượng khoảng tin cậy 95% cho tỷ lệ hủy của từng cluster.

| Cluster | N | Tỷ lệ hủy | Khoảng tin cậy 95% |
|---|---:|---:|---:|
| Cluster 2 | 99 | 52,53% | 42,42% - 62,63% |
| Cluster 0 | 14.509 | 38,07% | 37,28% - 38,83% |
| Cluster 1 | 134 | 27,61% | 20,15% - 35,82% |
| Noise | 258 | 21,71% | 16,67% - 26,74% |

Cluster 1 và cluster 2 có khoảng tin cậy rộng hơn cluster 0. Vì vậy, các nhóm nhỏ là tín hiệu đáng chú ý nhưng chưa nên được xem là kết luận tuyệt đối.

## 9. Biểu đồ đầu ra

Notebook chỉ tạo ba biểu đồ trong `DO_AN/output/`:

```text
DO_AN/output/bieu_do_cluster_risk_profile.png
DO_AN/output/bieu_do_classification_theo_cluster.png
DO_AN/output/bieu_do_bootstrap_ci.png
```

- `bieu_do_cluster_risk_profile.png`: tỷ lệ hủy và heatmap profiling theo cluster.
- `bieu_do_classification_theo_cluster.png`: tỷ lệ hủy thực tế và xác suất dự đoán theo cluster.
- `bieu_do_bootstrap_ci.png`: khoảng tin cậy 95% cho tỷ lệ hủy.

## 10. Phát hiện, hạn chế và khuyến nghị

### Phát hiện chính

1. Decision Tree cho thấy dữ liệu có tín hiệu dự đoán hủy rõ ràng.
2. Classification và Clustering cùng xác định cluster 2 là nhóm rủi ro cao nhất.
3. Một số biến quan trọng của mô hình cũng là biến phân biệt hành vi giữa các cluster.

### Hạn chế

- Kết quả thể hiện quan hệ thống kê, không chứng minh quan hệ nhân quả.
- Cluster 1 và cluster 2 có ít booking và khoảng tin cậy rộng.
- DBSCAN nhạy với cách chuẩn hóa, `eps`, `min_samples` và sample được chọn.

### Khuyến nghị

1. Dùng xác suất Classification để cảnh báo sớm và dùng cluster để lựa chọn cách xử lý phù hợp.
2. Ưu tiên xác minh booking thuộc nhóm rủi ro cao trước khi áp dụng chính sách đặt cọc hoặc xác nhận lại.
3. Kiểm tra các cluster nhỏ trên nhiều sample hoặc giai đoạn thời gian trước khi triển khai thực tế.

## 11. Cách chạy notebook

Từ thư mục gốc dự án, cài đặt thư viện:

```bash
pip install -r requirements.txt
```

Mở file:

```text
DO_AN/phan-tich-tong-hop.ipynb
```

Chạy toàn bộ cell theo thứ tự từ trên xuống. Notebook hỗ trợ chạy từ thư mục gốc dự án hoặc từ thư mục `DO_AN`. Các bảng kết quả được hiển thị trực tiếp và ba biểu đồ được lưu vào `DO_AN/output/`.

## 12. Kết luận

Classification và Clustering bổ sung cho nhau: Decision Tree ước lượng rủi ro của từng booking, còn DBSCAN cung cấp nhóm hành vi để giải thích và ưu tiên hành động. Hai kỹ thuật xác nhận nhau ở cluster 2, nhưng kết quả của các cluster nhỏ cần tiếp tục được kiểm chứng bằng dữ liệu mới.
