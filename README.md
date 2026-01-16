# Dự án Nghiên cứu Định lượng & Giao dịch Tự động (Quantitative Trading Research)

## Tổng quan
Dự án này mô phỏng quy trình làm việc thực tế của một Quantitative Researcher. Mục tiêu là xây dựng một hệ thống hoàn chỉnh từ khâu xử lý dữ liệu thô, phân tích chiến thuật, kiểm định quá khứ (Backtest) cho đến áp dụng các mô hình Thống kê và Học máy (Machine Learning) trên thị trường chứng khoán Việt Nam (Mã: FPT).

## Quy trình xử lý dữ liệu (The Pipeline)
Hệ thống được xây dựng theo quy trình 5 bước nối tiếp nhau:

**1. Data Engineering (ETL) -> 2. Analysis -> 3. Backtesting -> 4. Statistics -> 5. Machine Learning**

### Chi tiết từng giai đoạn:

### Giai đoạn 1: Xây dựng Kho dữ liệu (Data Engineering)
*Mục tiêu: Chuyển đổi dữ liệu rời rạc thành cơ sở dữ liệu chuẩn.*
- **Input:** Các file CSV chứa lịch sử giá cổ phiếu (OHLCV).
- **Xử lý (ETL):** Làm sạch dữ liệu, xử lý định dạng ngày tháng, loại bỏ giá trị lỗi.
- **Output:** Một file cơ sở dữ liệu SQLite (`quant_trading.db`) giúp truy xuất dữ liệu nhanh chóng bằng câu lệnh SQL thay vì mở file Excel.

### Giai đoạn 2: Phân tích & Tạo tín hiệu (Market Analysis)
*Mục tiêu: Tìm kiếm ý tưởng giao dịch (Strategy Idea).*
- Sử dụng Python (Pandas) để tính toán các chỉ báo kỹ thuật.
- **Chiến thuật:** Giao cắt Trung bình động (SMA Crossover).
    - Tín hiệu Mua: Đường SMA 20 cắt lên SMA 50 (Golden Cross).
    - Tín hiệu Bán: Đường SMA 20 cắt xuống SMA 50 (Death Cross).
- Kết quả: Trực quan hóa được các điểm mua bán tiềm năng trên biểu đồ FPT.

### Giai đoạn 3: Kiểm định Chiến thuật (Event-Driven Backtesting)
*Mục tiêu: Trả lời câu hỏi "Nếu giao dịch thật thì lãi bao nhiêu?".*
- Chuyển từ Pandas sang Framework chuyên nghiệp **Backtrader**.
- Mô phỏng sát thực tế:
    - Khớp lệnh theo từng cây nến (Bar-by-bar).
    - Tính phí giao dịch (Commission) và trượt giá (Slippage).
    - Áp dụng Quản lý vốn: All-in 95% vốn kết hợp Lãi kép.
- **Kết quả (Mã FPT):**
    - Lợi nhuận: ~80% trong giai đoạn 2023-2024.
    - Sharpe Ratio: > 3.0 (Hiệu quả cao so với rủi ro).
    - Max Drawdown: ~16% (Mức sụt giảm chấp nhận được).

### Giai đoạn 4: Kiểm định Thống kê (Statistical Checks)
*Mục tiêu: Xác minh dữ liệu có đủ tin cậy để chạy mô hình dự báo hay không.*
- Chuyển đổi giá sang Lợi nhuận Logarit (Log Returns).
- **Kiểm định ADF:** Kết quả cho thấy chuỗi lợi nhuận có tính dừng (Stationary), đủ điều kiện áp dụng mô hình toán học.
- **Phân tích Phân phối:** Phát hiện hiện tượng "Đuôi dày" (Fat Tail) với Kurtosis > 0, rủi ro cao hơn phân phối chuẩn lý thuyết.

### Giai đoạn 5: Ứng dụng Machine Learning
*Mục tiêu: Dự báo xu hướng giá Tăng/Giảm bằng AI.*
- **Mô hình:** Random Forest Classifier.
- **Features (Đầu vào):** SMA Ratio, Volatility, Momentum.
- **Kết quả:** Độ chính xác (Accuracy) đạt ~50% trên tập kiểm tra.
- **Kết luận:** Thị trường có tính ngẫu nhiên cao trong ngắn hạn. Các chỉ báo kỹ thuật đơn giản chưa đủ để AI dự đoán chính xác hướng đi của giá, cần tập trung vào quản trị rủi ro thay vì cố gắng dự đoán.

## MIÊU TẢ CÁC FRAMEWORK ĐÃ SỬ DỤNG
- **Ngôn ngữ:** Python
- **Database:** SQLite
- **Phân tích:** Pandas, NumPy, Matplotlib
- **Framework Backtest:** Backtrader
- **Machine Learning:** Scikit-learn, Statsmodels
- 

# Quantitative Trading Research Project

## Overview
This project simulates the end-to-end workflow of a Quantitative Researcher. The goal is to build a comprehensive system ranging from raw data processing, strategy analysis, and event-driven backtesting to the application of Statistical methods and Machine Learning on the Vietnam Stock Market (Ticker: FPT).

## The Pipeline
The system follows a sequential 5-step pipeline:

**1. Data Engineering (ETL) -> 2. Analysis -> 3. Backtesting -> 4. Statistics -> 5. Machine Learning**

### Detailed Breakdown:

### Phase 1: Data Engineering (ETL)
*Goal: Convert fragmented data into a structured database.*
- **Input:** Raw CSV files containing historical stock prices (OHLCV).
- **Process:** Data cleaning, datetime formatting, and error handling.
- **Output:** A structured SQLite database (`quant_trading.db`) allowing for efficient SQL queries instead of manual CSV parsing.

### Phase 2: Market Analysis & Signal Generation
*Goal: Identify trading ideas.*
- Utilized Pandas for feature engineering.
- **Strategy:** Moving Average Crossover (SMA).
    - Buy Signal: SMA 20 crosses above SMA 50 (Golden Cross).
    - Sell Signal: SMA 20 crosses below SMA 50 (Death Cross).
- Result: Visualized potential entry and exit points on the FPT price chart.

### Phase 3: Event-Driven Backtesting
*Goal: Answer the question "Is this strategy profitable in reality?".*
- Migrated logic to the **Backtrader** framework for professional simulation.
- Real-world constraints simulated:
    - Bar-by-bar execution.
    - Transaction costs (Commission) and Slippage.
    - Money Management: Kelly-inspired sizing (95% equity) with Compounding.
- **Results (FPT Stock):**
    - Total Return: ~80% (2023-2024).
    - Sharpe Ratio: > 3.0 (Excellent risk-adjusted return).
    - Max Drawdown: ~16% (Acceptable risk level).

### Phase 4: Statistical Analysis
*Goal: Validate data assumptions for modeling.*
- Transformed prices into Log Returns.
- **ADF Test:** Confirmed that Log Returns are stationary (p-value < 0.05), making them suitable for statistical modeling.
- **Distribution Analysis:** Identified "Fat Tail" risks (Kurtosis > 0), indicating higher probability of extreme events compared to a Normal Distribution.

### Phase 5: Machine Learning Application
*Goal: Predict price direction (Up/Down) using AI.*
- **Model:** Random Forest Classifier.
- **Features:** SMA Ratio, Volatility, Momentum.
- **Result:** Achieved ~50% Accuracy on the test set.
- **Conclusion:** The market exhibits significant short-term randomness (Random Walk). Simple technical indicators are insufficient for high-accuracy prediction, reinforcing the importance of Risk Management over pure Prediction.

## Tech Stack
- **Language:** Python
- **Database:** SQLite
- **Analysis:** Pandas, NumPy, Matplotlib
- **Backtesting Framework:** Backtrader
- **Machine Learning:** Scikit-learn, Statsmodels
