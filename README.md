# 114-2 智慧物聯網： ESP32 HW1 系統報告

**技術堆疊**: ESP32 (C++/PlatformIO) ➔ FastAPI (Python) ➔ SQLite3 ➔ Streamlit

---

## 專案成員

- 4112064214 侯竣奇
- 4112064201 鍾冠泓
- 4112064206 龔裕棠(我)

---

## 專案展示與連結資訊

1. **GitHub 儲存庫:** [coke5151/iot2026-esp32-hw1](https://github.com/coke5151/iot2026-esp32-hw1)
2. **公開線上展示 (Streamlit Community Cloud):** [https://iot2026-arduino-hw1-pytree.streamlit.app/](https://iot2026-arduino-hw1-pytree.streamlit.app/)
3. **YouTube 實機展示影片:** [觀看硬體實作影片](https://www.youtube.com/watch?v=hUg5mjioVz8)
   > 影片中展示了 ESP32 實際讀取 DHT11 感測器溫濕度資料，並成功上傳至後端伺服器，最後即時呈現於 Streamlit 前端儀表板上的完整流程。
4. **開發歷程紀錄:** 請查閱根目錄的 [`聊天記錄.md`](./聊天記錄.md)。

---

## 系統整體架構 (System Architecture)

系統從底層硬體到頂層介面採用**四層式架構**：設備端 (Edge) ➔ 接收端 (API) ➔ 儲存端 (Database) ➔ 展示端 (Dashboard)。
為因應不同展示情境，儀表板支援兩種主要的運作模式：

### 1. 區域網路連線模式 (Live Mode)

這是在本地端執行時的標準模式，遵循完整的四層架構。ESP32 透過 Wi-Fi 區域網路將感測資料發送至後端並存入資料庫，前端再從資料庫提取資料顯示。

![Live Mode Pipeline](https://kroki.io/mermaid/png/eNp90l1r01AYB_D7foqH9GaFFJOcvA-EvOIkdHXpXdlF2qQ2LDYlSd12N0ZFEHEXm9N6440DocwxFZF9HCFtvdKPYJbT1q4Yc3dyHn7n_zznlDpBuN_uOlEC1k4Jsq8dOHGsex1wvae10PWg4weBXNZ5QzFFMk6icM-Ty4wosAo_X1b3fTfpykz_gGyHQRjJZVrhGIHfvAs6fX8FNDUTaeYSVAWJo9RCUNB5nhLXQLe1GpAzOQMtPdoQWVYqDshyCmLWvMGdfJxhrDTMIyQJbKHHKgzilDUvcA69SA0PsNgLe96Sk6RlNteJs_lHzqEMLBSfwPPZPHM_HrQeR06_CxYNTev2DKB38x2lSfx-f3oNs4-X6fFoOr6CDcOuI-ae6ex5FWJXluX5reblXs9dF5mFyGBRbRI_3o1-fT-ByauLydm33DSdOFHqWyBSFIXV-dUWqWihIqxqzY0s6JvhrZsOP6eXb3PXfmT5iYcqRCVP2vofyS5IFpN63voLSK9fTz_cYC6JPOdJ4CcgchSNgw7-kVOBahWIB41GHerbdgMe2ts1Ivt3H1Q8g3w_vRqnzy5mX55PzkfpzRi2arax08B1Gu4qr5t9OkpPzqdfj36-HIJtWIY2L9JLf19HdnWkxZAWIrNGFs9ks_QHY9Unfg==)

### 2. 雲端隨機模擬模式 (Random Mode)

由於我們將前端部署在 Streamlit Community Cloud，為解決雲端無後端資料庫連線的問題，特別設計了隨機生成測試資料的模式。此模式僅靠前端程式碼就能自動產生動態的感測器折線圖。

![Random Mode Pipeline](https://kroki.io/mermaid/png/eNrjSsvJL0_OSCwqUfAJ4lIAguScxOJil9Q0hdz85Gy__JRUhbTMnBwrZTcXVycXA53ikqL87FQrZSdHE0cDGFe3PDOlJMPKqKBCJzk_J7_IStncwsjC0M0a1cTSTGTzTF1d3Szg5pkZG1uam-A0z8TRyNjU0ZoLbKBjtNKH-X2bFJ6tWPhs8poXK9Y-bZr5fPV6BQ1foJMVXBJLEjWVYq2srGA-AGtyAmma3KXwdOPU50t2gZUHlxSlJubmZJYoWJgaGEL0QNwItUdBV1dB6UX7qqfdU59Pmf-sY4KChuHztW36z9b3P18-SVMJKG-n4AQxHkntk_3rnnauApqfk5mSmZeuEJ6Zl5JfDlXuyAUApIKE6A==)

---

## 專案目錄結構與核心檔案

專案被劃分為三個主要區塊：硬體設備端 (`edge`)、後端伺服器 (`backend`) 以及前端介面 (根目錄)。

| 目錄與檔案 | 主要功能描述 |
|---|---|
| `edge/` | ESP32 的 PlatformIO 開發環境與 C++ 原始碼。<br>負責定期讀取 DHT11 溫濕度並透過 WiFi 發送 HTTP POST。 |
| `backend/main.py` | FastAPI 應用程式。<br>提供 RESTful API 接收硬體資料並負責寫入 SQLite 資料庫。 |
| `app.py` | Streamlit 主程式。<br>整合資料並視覺化，提供 LiveDB 與 Random 雙模式切換的互動儀表板。 |
| `pyproject.toml` | 專案依賴設定檔，統一使用最新工具 `uv` 進行管理。 |
| `.env` & `.env.example` | 環境變數設定檔，用於存放 Wi-Fi SSID、密碼以及後端伺服器的 IP 位址。 |

### 硬體配置資訊

- **微控制器**: ESP32 (ESP-WROOM-32)
- **感測元件**: DHT11 溫濕度感測器
- **其他周邊**: LED、按鈕等

---

## 環境建置與執行指南

本專案使用 `uv` 替代傳統的 `pip` 進行 Python 套件管理，確保極速的開發體驗。
先備條件：請確保您的系統已安裝 `uv` 工具。

### 步驟一：啟動 FastAPI 後端伺服器

後端伺服器是連接硬體與資料庫的橋樑。它將接收 ESP32 傳來的 JSON Payload 並自動建立/更新 `sensor_data.db` SQLite 資料庫。

1. 開啟終端機並進入 `backend` 資料夾：

   ```powershell
   cd backend
   ```

2. 啟動伺服器：

   ```powershell
   uv run main.py
   ```

   > 伺服器啟動後，本機網址為 `http://127.0.0.1:8000`。您可瀏覽 `http://localhost:8000/docs` 查看自動生成的 Swagger API 測試文件。

### 步驟二：啟動 Streamlit 視覺化儀表板

前端儀表板會從 SQLite 抓取歷史資料並即時繪製折線圖。

1. 在**實體機** 開啟另一個終端機，確保路徑停留在「專案根目錄」。
2. 啟動 Streamlit：

   ```powershell
   uv run streamlit run app.py
   ```

   > 儀表板預設會開啟在 `http://localhost:8501`。頁面具備 5 秒自動刷新的輪詢機制以顯示最新資料。

### 步驟三：設定與燒錄 ESP32

1. 複製根目錄的 `.env.example` 檔案並重命名為 `.env`。
2. 編輯 `.env`，填入您的 **Wi-Fi SSID** 與 **密碼**。
   > ⚠️ **注意**: 您的電腦 (執行 FastAPI 的主機) 與 ESP32 必須連線到**同一個區域網路 (LAN)**。
3. 使用 VSCode 開啟 `edge` 資料夾 (請確保已安裝 PlatformIO 擴充套件)。
4. 透過 USB 將 ESP32 連接至電腦。
5. 點擊 PlatformIO 側邊欄的 **Upload** 按鈕，系統將自動編譯並將程式碼燒錄至開發板中。

---

## 模式展示與驗證

### 🟢 Live 模式 (實體資料連線)

- 前端會定時向 `sensor_data.db` 撈取最新溫濕度數值。
- 若偵測不到資料庫或無資料，畫面會明確顯示錯誤提示 (`🚨 Error: Local Database Not Found or Empty!`)。
- 結合 Plotly Dark 主題，帶出濃厚科技感的互動圖表。

<details>
<summary>點擊展開長截圖 (Live Mode)</summary>

![Live Database Demo 2](./LiveDB_2.png)
</details>

![Live Database Demo 1](./LiveDB_1.png)

### 🔵 Random 模式 (模擬測試資料)

- 運用 `numpy` 生成常態分佈的隨機變數作為假資料，並暫存於 Streamlit Session State。
- 採用「Sliding Window 自動修剪機制」，確保前端畫面擁有一秒一動的平滑折線滾動效果，且絕不耗盡伺服器記憶體。
- 目前公開的 Streamlit App 連結即採用此模式運作。

<details>
<summary>點擊展開長截圖 (Random Mode)</summary>

![Random Database Demo 2](./RandomDB_2.png)
</details>

![Random Database Demo 1](./RandomDB_1.png)
