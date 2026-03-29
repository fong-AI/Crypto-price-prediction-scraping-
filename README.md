# 📈 Bitcoin Price Prediction & Scraping

Thu thập dữ liệu lịch sử Bitcoin từ CoinMarketCap bằng Selenium, sau đó huấn luyện và so sánh 4 mô hình dự đoán giá đóng cửa. Kết quả được hiển thị qua giao diện Tkinter.

---

## ✨ Features

- **Web scraping** — tự động thu thập 1,000+ bản ghi giá Bitcoin từ CoinMarketCap
- **4 mô hình dự đoán** — Linear Regression, Ridge, MLP, Stacking Ensemble
- **Đánh giá đa chỉ số** — R², NSE, RMSE, MAE cho từng mô hình
- **GUI Tkinter** — nhập giá thủ công, so sánh kết quả 4 mô hình cùng lúc

---

## 📊 Dataset

**Nguồn:** [CoinMarketCap — Bitcoin Historical Data](https://coinmarketcap.com/currencies/bitcoin/historical-data/)

| Thông số | Giá trị |
|----------|---------|
| Tổng bản ghi | 1,017 ngày |
| Phạm vi | Đến tháng 12/2024 |
| Train / Test | 70% / 30% (temporal split) |

**Các cột dữ liệu:**

| Cột | Mô tả |
|-----|-------|
| `date` | Ngày giao dịch |
| `open` | Giá mở cửa |
| `high` | Giá cao nhất trong ngày |
| `low` | Giá thấp nhất trong ngày |
| `close` | Giá đóng cửa *(target)* |
| `volume` | Khối lượng giao dịch |
| `market_cap` | Vốn hóa thị trường |

---

## 🔄 Pipeline

```
CoinMarketCap (Selenium)
        ↓
Raw HTML table (1,017 rows × 7 cols)
        ↓
Data Cleaning
  - Xóa ký tự $, dấu phẩy, khoảng trắng
  - Chuyển datetime, float
        ↓
MinMaxScaler → [0, 1]
        ↓
Train/Test Split (70/30, shuffle=False)
        ↓
Features: open, high, low, volume
Target:   close
        ↓
┌──────────────┬────────────┬─────────────┬────────────────────┐
│   Linear     │   Ridge    │     MLP     │     Stacking       │
│ Regression   │ α=0.0001   │ (200,500)   │ Linear+Ridge+MLP   │
└──────────────┴────────────┴─────────────┴────────────────────┘
        ↓
Evaluation: R², NSE, RMSE, MAE
        ↓
Tkinter GUI
```

---

## 🤖 Models

### 1. Linear Regression
- Baseline model, default parameters
- Học quan hệ tuyến tính giữa features và close price

### 2. Ridge Regression
- `alpha = 0.0001` — regularization nhẹ
- Giảm overfitting so với Linear thông thường

### 3. MLP Regressor (Neural Network)

| Parameter | Value |
|-----------|-------|
| Hidden layers | (200, 500) neurons |
| Activation | `identity` |
| Solver | `lbfgs` |
| Max iterations | 100 |

### 4. Stacking Ensemble

| Thành phần | Chi tiết |
|-----------|---------|
| Base learners | Linear Regression, Ridge, MLP |
| Meta-learner | RidgeCV |
| Cơ chế | Dự đoán của 3 base models → input cho meta-learner |

---

## 📏 Evaluation Metrics

| Metric | Ý nghĩa |
|--------|---------|
| **R²** | Hệ số xác định (1.0 = hoàn hảo) |
| **NSE** | Nash-Sutcliffe Efficiency — `1 - SS_res/SS_tot` |
| **RMSE** | Root Mean Squared Error |
| **MAE** | Mean Absolute Error |

---

## 🖥️ GUI (Tkinter)

Giao diện cho phép nhập giá và dự đoán thủ công:

```
┌─────────────────────────────────┐
│  Open:    [___________]         │
│  High:    [___________]         │
│  Low:     [___________]         │
│  Volume:  [___________]         │
│                                 │
│  [Linear] [Ridge] [MLP] [Stack] │
│                                 │
│  Predicted Close: $________     │
│  R²: ___ | RMSE: ___ | MAE: ___│
└─────────────────────────────────┘
```

> Nhập giá trị đã **chuẩn hóa** (MinMaxScaler) vào các ô input.

---

## 📁 Project Structure

```
Crypto-price-prediction-scraping-/
├── crypto.ipynb                  # Toàn bộ pipeline: scraping, preprocessing, training, GUI
└── bitcoin_historical_data.csv   # Dataset đã thu thập (1,017 bản ghi)
```

---

## 🚀 Getting Started

### Yêu cầu

- Python 3.8+
- Google Chrome + [ChromeDriver](https://chromedriver.chromium.org/) (cùng version với Chrome)
- Jupyter Notebook

### Cài đặt

```bash
git clone https://github.com/fong-AI/Crypto-price-prediction-scraping-.git
cd Crypto-price-prediction-scraping-
pip install selenium pandas numpy scikit-learn
```

### Chạy notebook

```bash
jupyter notebook crypto.ipynb
```

> **Dataset có sẵn** tại `bitcoin_historical_data.csv` — có thể bỏ qua bước scraping nếu không cần cập nhật dữ liệu mới.

### Cập nhật dữ liệu mới (scraping)

1. Đảm bảo ChromeDriver đã cài và khớp phiên bản Chrome
2. Chạy các cell scraping trong notebook
3. Dữ liệu mới sẽ ghi đè `bitcoin_historical_data.csv`

---

## 📦 Dependencies

| Thư viện | Mục đích |
|----------|---------|
| `selenium` | Web scraping tự động |
| `pandas` | Xử lý dữ liệu |
| `numpy` | Tính toán số học |
| `scikit-learn` | ML models, scaler, metrics |
| `tkinter` | GUI (built-in Python) |

---

## 📝 Notes

- Temporal split (`shuffle=False`) giữ nguyên thứ tự thời gian — tránh data leakage
- GUI nhận **giá trị đã normalize** (0–1); cần scale dữ liệu trước khi nhập
- Stacking ensemble kết hợp 3 mô hình → thường cho kết quả ổn định hơn từng mô hình đơn lẻ
- CoinMarketCap có thể thay đổi cấu trúc HTML — selector `.cmc-table tbody tr` cần cập nhật nếu scraping bị lỗi

---

## 📄 License

This project is for personal / educational use.
