# 💰❄️🛡️ 「冷卻金流」概念性驗證專案 (Cooling Fund Flow PoC)

[![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/vvchung/Cooling_Fund_Flow_Demo/blob/main/Cooling_Fund_Flow_Demo.ipynb)

本專案旨在回應日益猖獗的網路投資詐騙問題：165反詐騙專線靠24支電話與51名警察，一年成功擋下5800件詐騙案，救回民眾血汗錢高達3.79億元。為此，我們參考[「一年臺灣民眾被詐騙一千億，得覺醒」](https://medium.com/@bohachu/%E4%B8%80%E5%B9%B4%E8%87%BA%E7%81%A3%E6%B0%91%E7%9C%BE%E8%A2%AB%E8%A9%90%E9%A8%99%E4%B8%80%E5%8D%83%E5%84%84-%E5%BE%97%E8%A6%BA%E9%86%92-11ba5b6ec18e)提出的「冷卻金流」、「跨行聯合雷達」和「社群早期偵測」三層式主動防禦策略，而這個 Colab Notebook 專注於實作第一層防線：

**🛡️ 「冷卻金流：為可疑的線上轉帳提供一個後悔期，以降低詐騙案件的財務損失」**


## 🎯 專案目標

根據文件描述，本專案旨在透過程式碼模擬以下流程：

*   🧐 **1. 風險評分模型**：當一筆轉帳發生時，系統會根據多個特徵（如金額、收款方資訊、關鍵字等）即時計算出一個風險分數。
*   ⏳ **2. 延遲佇列服務**：如果風險分數超過一個預設閾值（例如 0.7），該筆交易不會立即執行，而是被放入一個「延遲佇列」。
*   🔔 **3. 給予後悔時間**：在延遲期間（例如 4-24 小時），用戶會收到銀行 App 的通知，告知此筆交易存在風險。
*   🛑 **4. 一鍵取消**：用戶可以在此「冷卻期」內，透過 App 一鍵取消該筆交易，成功攔截款項。
*   🤖 **5. 自動執行**：若用戶未在冷卻期內取消，且無人工介入，系統將在延遲時間結束後自動執行該筆轉帳。

## 🛠️ 技術棧

本專案使用以下技術進行模擬：

*   **Google Colab / Jupyter Notebook**: 提供一個互動式的開發與展示環境。
*   **Python**: 主要的程式語言。
*   **XGBoost**: 一個高效能的梯度提升函式庫，用於建立我們的風險評分模型。
*   **Redis**: 一個高效能的 in-memory 資料庫，我們使用其 Sorted Set 資料結構來實作一個毫秒級的延遲佇列。
*   **Flask**: 一個輕量級的 Web 框架，用於建立一個模擬的後端 API，提供取消交易的端點。
*   **Pandas / Numpy**: 用於資料處理與科學計算。

## 🚀 如何使用

### 透過 Google Colab (建議)

1.  點擊上方的 "Open In Colab" 徽章。
2.  在開啟的 Colab 筆記本中，依照儲存格 (cell) 的順序依次執行。點擊每個儲存格左側的「播放」按鈕即可。
3.  當執行到 **"4. 前端提示與取消 API"** 時，它會啟動一個 Flask 網頁伺服器，並透過 `ngrok` 提供一個公開的 URL。
4.  你可以使用 `curl` 指令或 Postman 等工具，向該 URL 的 `/cancel_transaction` 端點發送 POST 請求，來模擬取消一筆正在延遲佇列中的交易。

### 🧪 API 測試範例 (使用 curl)

```bash
# 請將 <ngrok_url> 替換為 Colab Cell 輸出中的 ngrok 網址
# 請將 <transaction_id> 替換為你想取消的交易 ID (也會顯示在輸出中)
curl -X POST -H "Content-Type: application/json" \
     -d '{"transaction_id": "<transaction_id>"}' \
     <ngrok_url>/cancel_transaction
```

### 在本地端執行

1.  確保你已安裝 Python 3.7+。
2.  安裝 Redis 並啟動 Redis Server。
3.  複製 `Cooling_Fund_Flow_Demo.ipynb` 的程式碼到本地的 Jupyter Notebook 或 Python 腳本中。
4.  安裝所需的套件：
    ```bash
    pip install xgboost pandas numpy redis flask
    ```
5.  修改程式碼中 Redis 連線的部分，確保 `host` 和 `port` 正確。
6.  執行程式碼。

## ⚖️ 監理合規

本方案的設計參考了《洗錢防制法》第 8–1 條中關於「暫緩交易」的條款，旨在為金融機構提供一個主動防禦金融犯罪的法理基礎。透過建立基於風險的內部延遲處理機制，金融機構不僅能更好地保護客戶，也能更有效地履行其社會與法律責任。

## ⚠️ 免責聲明

這是一個用於演示和教育目的的專案，並非生產級別的程式碼。在實際應用中，需要考慮更全面的安全性、錯誤處理、系統擴展性與使用者介面設計。
