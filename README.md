# Môi trường

Để chạy các bài thực hành, bạn cần cài đặt môi trường Python (khuyến nghị `Python >= 3.10`). Tất cả các thư viện bắt buộc đã được liệt kê trong file `requirements.txt`.

Cách cài đặt môi trường:
1. Tải và cài đặt Python nếu chưa có.
2. Mở Terminal (Command Prompt / PowerShell) và truy cập vào thư mục `data-mining`.
3. Khuyến nghị tạo một môi trường ảo (virtual environment) để tránh xung đột thư viện:
   ```bash
   python -m venv .venv
   .venv\Scripts\activate
   ```
4. Cài đặt các thư viện cần thiết:
   ```bash
   pip install -r requirements.txt
   ```
5. Cài đặt các công cụ đọc Jupyter Notebook như **VS Code** (cùng extension Jupyter) hoặc **JupyterLab**.

# Cách chạy lại

* Mã nguồn của 4 bài tập đều là các file Jupyter Notebook (có đuôi `.ipynb`).
* Mở từng file bằng ứng dụng hoặc IDE (như VS Code).
* Để thực thi hãy nhấn **Run All** hoặc chạy thủ công từng cell mã (từng khối code) từ trên xuống dưới.
* Kết quả phân tích (bảng, biểu đồ) sẽ hiển thị ngay bên dưới mỗi cell. Quá trình chạy có thể ghi một số file đầu ra vào thư mục `data/`, vui lòng không sửa đổi đường dẫn đã được thiết lập sẵn trong mã nguồn.

# Thứ tự thực thi

Dự án bao gồm 4 bài thực hành và kết quả của các bài sau sẽ phụ thuộc vào bài trước (đặc biệt là khâu tiền xử lý dữ liệu). Bạn **bắt buộc phải chạy theo đúng thứ tự sau**:

1. **Bài 1 — Khảo sát & Tiền xử lý dữ liệu** (`bai1-du-lieu-tien-xu-ly/`)
   Bạn cần chạy để sinh ra các file dữ liệu đã được làm sạch trước khi làm các bài tiếp theo:
   * Chạy `D1/khao-sat.ipynb` 
   * Chạy `D2/khao-sat.ipynb` 
   * Chạy `D3/khao-sat.ipynb`

2. **Bài 2 — Phân lớp** (`bai2-phan-lop/`)
   * Chạy file `phan-lop.ipynb`

3. **Bài 3 — Luật kết hợp** (`bai3-luat-ket-hop/`)
   * Chạy `D2/luat-ket-hop.ipynb`
   * Chạy `D3/luat-ket-hop.ipynb`

4. **Bài 4 — Gom cụm** (`bai4-gom-cum/`)
   * Chạy `D1/gom-cum.ipynb`
   * Chạy `D2/gom-cum.ipynb`
