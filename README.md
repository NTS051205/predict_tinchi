# Project Title

**Last_Dance** – Ứng dụng mô hình học máy trong dự báo tín chỉ hoàn thành của sinh viên

---

## Prerequisites

- **Python:** 3.8 trở lên (khuyến nghị 3.11).
- **Docker:** Dự án không sử dụng Docker. Tất cả bước chạy bằng Jupyter Notebook / Python.
- **RAM:** Tối thiểu 4GB (khuyến nghị 8GB trở lên khi chạy training và SHAP).
- **Dữ liệu đầu vào:** Đặt trong thư mục `data/`:
  - `data/admission.csv`
  - `data/academic_records.csv`
  - `data/test.csv` (cho bước dự báo)

---

## Installation

### Cài đặt thư viện

```bash
pip install -r requirements.txt
```

## How to Run (QUAN TRỌNG NHẤT)

Chạy lần lượt các file theo đúng thứ tự dưới đây (trong Jupyter/VS Code: **Run All** từng notebook).

### Bước 1: Tiền xử lý dữ liệu (làm sạch + chuẩn bị)

1. **Làm sạch dữ liệu**  
   Chạy notebook: **`data_cleaning.ipynb`**  
   - Input: `data/admission.csv`, `data/academic_records.csv`  
   - Output: `data/cleaned_admission.csv`, `data/final_combined_cleaned.csv`

2. **Chuẩn bị train/valid và tách theo năm**  
   Chạy notebook: **`data_preprocessing.ipynb`**  
   - Input: `data/cleaned_admission.csv`, `data/final_combined_cleaned.csv`, `data/test.csv`  
   - Output: `data/train_year_1.csv` … `data/train_year_6.csv`, `data/valid.csv`, cùng các file test đã tách.

### Bước 2: Feature engineering và Training (hoặc load model)

3. **Tạo features cho từng nhóm năm**  
   Chạy notebook: **`feature_engineering.ipynb`**  
   - Tạo các file features cho train/valid/test (ví dụ: `train_Y1_features.csv`, `valid_Y2plus_features.csv`, `test_Y1_Sem1_features.csv`, …).

4. **Training mô hình**  
   Chạy notebook: **`training.ipynb`**  
   - Train mô hình XGBoost theo nhóm năm (Y1–Y6).  
   - Lưu model và preprocessor vào `outputs/` (ví dụ: `xgb_model_Y1.joblib`, `preprocessor_Y1.joblib`, …).  
   - Nếu đã có sẵn thư mục `outputs/` với các file `.joblib`, có thể bỏ qua bước này và chỉ dùng **Bước 3** để load model và dự báo.

### Bước 3: Dự báo và kết quả

5. **Dự báo trên test**  
   Chạy notebook: **`predict.ipynb`**  
   - Load model và preprocessor từ `outputs/`.  
   - Đọc dữ liệu test và features đã tạo từ Bước 1–2.  
   - Ghi kết quả dự báo ra **`outputs/submission.csv`**.

**Lưu ý:** Dự án không có Dashboard hay API riêng. Kết quả dự báo nằm tại **`outputs/submission.csv`** và có thể xem trực tiếp trong **`predict.ipynb`** hoặc mở file CSV.

**Tóm tắt thứ tự chạy:**

| Thứ tự | File cần chạy | Mục đích |
|--------|----------------|----------|
| 1 | `data_cleaning.ipynb` | Làm sạch dữ liệu |
| 2 | `data_preprocessing.ipynb` | Chuẩn bị train/valid/test theo năm |
| 3 | `feature_engineering.ipynb` | Tạo features |
| 4 | `training.ipynb` | Training (hoặc bỏ qua nếu đã có model trong `outputs/`) |
| 5 | `predict.ipynb` | Dự báo → `outputs/submission.csv` |

---

## Project Structure

```
last_dance/
├── README.md                 # File hướng dẫn này
├── requirements.txt         # Danh sách thư viện Python
│
├── data_cleaning.ipynb      # Bước 1: Làm sạch dữ liệu (admission, academic_records)
├── data_preprocessing.ipynb  # Bước 2: Chuẩn bị train/valid/test theo năm
├── feature_engineering.ipynb# Bước 3: Tạo features cho Y1, Y2+
├── training.ipynb           # Bước 4: Training XGBoost (Y1–Y6), lưu model vào outputs/
├── predict.ipynb            # Bước 5: Load model, dự báo test → submission.csv
│
├── data/                    # Dữ liệu đầu vào và trung gian
│   ├── admission.csv        # Dữ liệu tuyển sinh (input)
│   ├── academic_records.csv # Bảng điểm/học vụ (input)
│   ├── test.csv             # Test set (input)
│   ├── cleaned_admission.csv
│   ├── final_combined_cleaned.csv
│   ├── train_year_1.csv … train_year_6.csv
│   ├── valid.csv
│   ├── train_Y*_features.csv, valid_*.csv, test_*_features.csv  # Features (sau feature_engineering)
│   └── … (các file CSV khác do pipeline tạo ra)
│
└── outputs/                 # Model và kết quả
    ├── preprocessor_Y1.joblib … preprocessor_Y6.joblib
    ├── xgb_model_Y1.joblib … xgb_model_Y6.joblib
    ├── submission.csv       # Kết quả dự báo cuối cùng
    ├── metrics_*.csv        # Đánh giá (MAE, RMSE, …)
    ├── feature_importance_*.png
    └── shap_summary_*.png
```

- **Code xử lý dữ liệu:** `data_cleaning.ipynb`, `data_preprocessing.ipynb`  
- **Code tạo features:** `feature_engineering.ipynb`  
- **Code training:** `training.ipynb`  
- **Code dự báo:** `predict.ipynb`  
- **Dữ liệu:** thư mục `data/`  
- **Model và kết quả:** thư mục `outputs/`
