# Hotel Booking Demand - Phan tich tong hop

File nay tom tat noi dung trong notebook `DO_AN/phan-tich-tong-hop.ipynb`. Notebook ket hop hai ky thuat Classification va Clustering de phan tich rui ro huy phong tren bo du lieu Hotel Booking Demand.

## 1. Muc tieu bai toan

Muc tieu nghiep vu la ho tro nha quan ly khach san nhan dien cac booking co nguy co huy phong cao, tu do dua ra chinh sach theo doi, uu tien xu ly hoac dieu chinh dat coc phu hop.

Bien muc tieu duoc phan tich la:

- `is_canceled = 0`: booking khong huy.
- `is_canceled = 1`: booking bi huy.

Notebook tap trung tra loi hai cau hoi:

1. Nhung dac diem cua booking co lien quan den kha nang huy phong hay khong?
2. Cac booking co the duoc chia thanh nhung nhom hanh vi co muc rui ro huy khac nhau hay khong?

## 2. Du lieu su dung

Notebook doc du lieu da tien xu ly tu file:

```text
data/processed/D1_hotel_booking/hotel_bookings_processed.csv
```

Thong tin du lieu sau tien xu ly:

- So dong: `117,398`.
- So cot: `43`.
- Ty le huy phong trong toan bo du lieu: `37.49%`.

Notebook co ham `find_processed_data()` de tu dong tim file `hotel_bookings_processed.csv` theo nhieu duong dan tuong doi khac nhau, giup co the chay notebook tu thu muc `DO_AN` hoac thu muc goc du an.

## 3. Thu vien can thiet

Du an su dung cac thu vien chinh:

- `pandas`, `numpy`: xu ly bang du lieu va tinh toan.
- `matplotlib`, `seaborn`: truc quan hoa du lieu.
- `scikit-learn`: chuan hoa du lieu, phan cum DBSCAN va tinh metric.
- `IPython.display`: hien thi bang va markdown trong notebook.

Co the cai dat moi truong bang lenh:

```bash
pip install -r requirements.txt
```

## 4. Cau truc xu ly trong notebook

### 4.1. Doc du lieu va cau hinh

Notebook khai bao cac tham so chinh:

```python
RANDOM_STATE = 42
CLASSIFICATION_SAMPLE_N = 40000
DBSCAN_SAMPLE_N = 15000
DBSCAN_EPS = 9.0
```

Thu muc luu bieu do:

```text
DO_AN/output/
```

### 4.2. Phan tich bien muc tieu

Notebook tinh ty le cua bien `is_canceled`, sau do ve bieu do ty le booking huy va khong huy. Ket qua duoc luu tai:

```text
DO_AN/output/bieu_do_ty_le_huy_phong.png
```

### 4.3. Tong hop ket qua Classification

Notebook doc metric Classification tu file `D1_metrics.csv`. Ket qua hien co:

| Model | Accuracy | Precision | Recall | F1 | ROC-AUC |
|---|---:|---:|---:|---:|---:|
| Decision Tree | 0.7884 | 0.5788 | 0.8507 | 0.6888 | 0.8903 |
| Logistic Regression | 0.7481 | 0.5289 | 0.7821 | 0.6310 | 0.8420 |

Theo F1 va ROC-AUC, `Decision Tree` la mo hinh Classification tot nhat trong file metric hien co. Classification duoc dung de chung minh rang du lieu booking co tin hieu du doan kha nang huy phong.

Bieu do so sanh metric Classification duoc luu tai:

```text
DO_AN/output/bieu_do_classification.png
```

### 4.4. Chuan bi du lieu cho Clustering

Truoc khi chay DBSCAN, notebook loai bo cac cot khong dung de tao cluster:

```python
DROP_COLS = [
    "is_canceled",
    "country",
    "agent",
    "company",
    "reserved_room_type",
    "assigned_room_type",
]
```

Ly do loai `is_canceled`: clustering phai duoc tao tu dac diem booking, khong dung truc tiep bien muc tieu huy phong. Sau khi co cluster, notebook moi ghep lai `is_canceled` de danh gia ty le huy theo tung nhom.

Mot so buoc xu ly chinh:

- Chuyen `arrival_date_month` tu ten thang sang so.
- One-hot encode cac cot phan loai `distribution_channel` va `customer_type`.
- Chuan hoa du lieu bang `StandardScaler`.
- Lay sample `15,000` dong de chay DBSCAN.

### 4.5. Chay DBSCAN

Tham so DBSCAN:

```python
eps = 9.0
min_samples = 88
metric = "euclidean"
```

Ket qua DBSCAN tren sample 15,000 dong:

- So cluster khong tinh noise: `3`.
- Ty le noise: `1.72%`.
- Silhouette khong tinh noise: `0.4146`.
- Davies-Bouldin khong tinh noise: `0.9486`.

Notebook ke thua nhan xet tu Bai 4: DBSCAN co Silhouette cao hon va Davies-Bouldin thap hon K-Means, nen duoc chon lam cau hinh clustering chinh cho phan phan tich tong hop.

## 5. Ket qua phan cum va rui ro huy phong

Cancellation rate trung binh cua sample DBSCAN la `37.79%`.

| Cluster | Nhan | So luong | Ty trong | So booking huy | Cancellation rate | Lift |
|---:|---|---:|---:|---:|---:|---:|
| 2 | cluster_2 | 99 | 0.66% | 52 | 52.53% | 1.39 |
| 0 | cluster_0 | 14,509 | 96.73% | 5,524 | 38.07% | 1.01 |
| 1 | cluster_1 | 134 | 0.89% | 37 | 27.61% | 0.73 |
| -1 | noise | 258 | 1.72% | 56 | 21.71% | 0.57 |

Nhan xet:

- `cluster_0` chiem phan lon sample va co cancellation rate gan trung binh.
- `cluster_2` co cancellation rate cao nhat, cao hon trung binh sample voi lift `1.39`.
- `cluster_1` co cancellation rate thap hon trung binh.
- Nhom noise co cancellation rate thap nhat trong sample hien tai.
- `cluster_1` va `cluster_2` co kich thuoc nho, nen can dien giai than trong.

## 6. Cac bieu do dau ra

Notebook tao cac bieu do trong thu muc `DO_AN/output/`:

- `bieu_do_ty_le_huy_phong.png`: ty le huy phong trong du lieu sau Bai 1.
- `bieu_do_classification.png`: so sanh metric Classification.
- `bieu_do_cluster_size.png`: kich thuoc cac cluster DBSCAN.
- `bieu_do_cluster_size_thg_log.png`: kich thuoc cluster tren thang log.
- `bieu_do_Cancellation_rate.png`: cancellation rate theo cluster.
- `bieu_do_cluster_profiling.png`: heatmap chenh lech dac trung cua cluster so voi trung binh sample.
- `bieu_do_bootstrap_ci.png`: khoang tin cay bootstrap 95% cho cancellation rate theo cluster.

## 7. Ky thuat nang cao: Bootstrap confidence interval

Do `cluster_1` va `cluster_2` co kich thuoc nho, notebook dung bootstrap confidence interval de kiem tra do on dinh cua cancellation rate theo cluster.

Ham chinh:

```python
def bootstrap_cancel_rate(values, n_boot=2000, random_state=42):
    rng = np.random.default_rng(random_state)
    values = np.asarray(values)
    boot_rates = []

    for _ in range(n_boot):
        sample = rng.choice(values, size=len(values), replace=True)
        boot_rates.append(sample.mean())

    boot_rates = np.asarray(boot_rates)
    return values.mean(), np.percentile(boot_rates, 2.5), np.percentile(boot_rates, 97.5)
```

Ket qua bootstrap cho thay cac cluster nho co khoang tin cay rong hon cluster lon. Vi vay, cac cluster nho co the duoc xem la tin hieu rui ro dang chu y, nhung chua nen dung nhu ket luan tuyet doi neu chua kiem tra them tren sample khac.

## 8. Ket luan chinh

Notebook cho thay hai ky thuat Classification va Clustering bo sung cho nhau:

- Classification cho biet du lieu booking co tin hieu de du doan kha nang huy phong. Trong ket qua hien co, Decision Tree tot hon Logistic Regression theo F1 va ROC-AUC.
- Clustering bang DBSCAN chia booking thanh cac nhom hanh vi khac nhau.
- Khi ghep `is_canceled` sau buoc clustering, cac cluster co cancellation rate khac nhau, cho thay viec phan nhom co the ho tro phan tich rui ro huy phong.

Ket luan quan trong: day la moi lien he thong ke, khong phai quan he nhan qua. Ngoai ra, Classification dung sample 40,000 dong, con DBSCAN dung sample 15,000 dong, nen notebook chi tong hop theo xu huong va khong so sanh truc tiep tung dong giua hai ky thuat.

## 9. Khuyen nghi ung dung

1. Theo doi ky cac nhom booking co cancellation rate cao hon trung binh.
2. Khong ap dung cung mot chinh sach cho moi booking; nen phan tang booking theo muc rui ro.
3. Dung Classification nhu cong cu canh bao som.
4. Dung Clustering de giai thich cac nhom hanh vi va so sanh rui ro giua cac nhom.
5. Kiem tra them do on dinh cua cac cluster nho truoc khi dua ra quyet dinh nghiep vu manh.

## 10. Cach chay lai notebook

Tu thu muc goc du an `data-mining`, cai dat thu vien:

```bash
pip install -r requirements.txt
```

Sau do mo notebook:

```text
DO_AN/phan-tich-tong-hop.ipynb
```

Chay lan luot cac cell tu tren xuong duoi bang Jupyter Notebook, JupyterLab hoac VS Code. Sau khi chay, cac bang ket qua se hien thi trong notebook va cac bieu do se duoc ghi vao thu muc `DO_AN/output/`.
