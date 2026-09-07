# BÀI DỮ LIỆU TIỀN XỬ LÝ — D1, D2, D3

## Cấu trúc

bai-du-lieu-tien-xu-ly-full/
├── D1/
│   ├── data/raw/
│   ├── data/processed/
│   ├── outputs/figures/
│   └── khao-sat.ipynb
├── D2/
│   ├── data/raw/
│   ├── data/processed/
│   ├── outputs/figures/
│   └── khao-sat.ipynb
├── D3/
│   ├── data/raw/
│   ├── data/processed/
│   ├── outputs/figures/
│   └── khao-sat.ipynb
├── requirements.txt
└── README.md

## Yêu cầu đã bao phủ

### D1 — Hotel Booking Demand
- Nguồn – giấy phép – quy mô
- Từ điển dữ liệu
- Tri thức lĩnh vực + lập luận kỹ thuật
- IQR + Z-score + Boxplot
- Giá trị thiếu
- Thêm/xóa/biến đổi thuộc tính
- Phân lớp
- Gom cụm

### D2 — Instacart Market Basket Analysis
- Nguồn: Kaggle, Instacart; giấy phép CC BY-NC-SA 4.0; khoảng 3.4 triệu dòng giao dịch và hơn 34 thuộc tính trên nhiều bảng
- Từ điển dữ liệu và tri thức lĩnh vực
- Giá trị thiếu và rà soát ngoại lệ bằng Z-score
- Biến đổi dữ liệu giao dịch thành đặc trưng khách hàng
- Luật kết hợp
- Gom cụm khách hàng

### D3 — US Accidents
- Nguồn: Kaggle, Moosavi et al. (GeoAI); giấy phép CC BY-NC-SA 4.0; khoảng 3.0 triệu bản ghi, 48 thuộc tính
- Từ điển dữ liệu và tri thức lĩnh vực
- Giá trị thiếu và rà soát ngoại lệ bằng Z-score
- Tạo thuộc tính thời gian, thời lượng và cờ hạ tầng
- Phân lớp mức độ nghiêm trọng
- Gom cụm tai nạn

## Cài đặt

pip install -r requirements.txt

Sau đó mở từng file `.ipynb` bằng VS Code hoặc Jupyter và chạy từ trên xuống.

## Nguồn dữ liệu

- D1: Hotel Booking Demand — https://www.kaggle.com/datasets/jessemostipak/hotel-booking-demand
- D2: Instacart Market Basket Analysis — https://www.kaggle.com/datasets/psparks/instacart-market-basket-analysis
- D3: US Accidents — https://www.kaggle.com/datasets/sobhanmoosavi/us-accidents

Ngày truy cập trong báo cáo: 25/08/2026.
