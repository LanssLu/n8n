# 手把手教材：AI Agent 與 Agentic Workflow 實作教學
![image](https://github.com/user-attachments/assets/918b8d95-135e-480d-ae12-ed85159ca818)

## 目錄
1. [第一部分：課前準備與環境安裝](#第一部分課前準備與環境安裝)  
   1.1 [安裝 Docker 或 NVM + Node.js](#11-安裝-docker-或-nvm--nodejs)  
   1.2 [安裝 n8n](#12-安裝-n8n)  
   1.3 [安裝 Ollama](#13-安裝-ollama)  
   1.4 [學員電腦配備建議](#14-學員電腦配備建議)

2. [第二部分：n8n 基礎操作與介面介紹](#第二部分n8n-基礎操作與介面介紹)  
   2.1 [建立並管理工作流程（Workflow）](#21-建立並管理工作流程workflow)  
   2.2 [常用節點（Nodes）簡介](#22-常用節點nodes簡介)

3. [第三部分：AI Agent 與大語言模型 (LLM) 串接](#第三部分ai-agent-與大語言模型-llm-串接)  
   3.1 [AI Agent 基本概念](#31-ai-agent-基本概念)  
   3.2 [實作步驟範例：從使用者輸入到 LLM 回覆](#32-實作步驟範例從使用者輸入到-llm-回覆)

4. [第四部分：示範：建置一個會議記錄整理並透過 Line 回覆的 AI Agent](#第四部分示範建置一個會議記錄整理並透過-line-回覆的-ai-agent)  
   4.1 [前置作業：Line Bot 設定](#41-前置作業line-bot-設定)  
   4.2 [Flow 實作流程](#42-flow-實作流程)

5. [第五部分：RAG（檢索增強生成）簡介與實作](#第五部分rag檢索增強生成簡介與實作)  
   5.1 [RAG 原理介紹](#51-rag-原理介紹)  
   5.2 [實作步驟：以 Wikipedia + Ollama 為例](#52-實作步驟以-wikipedia--ollama-為例)

6. [第六部分：特定情境問答機器人部署實作 – 以金融為例](#第六部分特定情境問答機器人部署實作--以金融為例)  
   6.1 [金融情境資料蒐集](#61-金融情境資料蒐集)  
   6.2 [工作流程設計](#62-工作流程設計)

7. [第七部分：小組專題討論與成果發表](#第七部分小組專題討論與成果發表)

8. [第八部分：Kuwa 介紹與應用（選修/延伸）](#第八部分kuwa-介紹與應用選修延伸)

9. [附錄：常見錯誤排除與最佳實踐](#附錄常見錯誤排除與最佳實踐)  
   A.1 [常見錯誤排除](#a1-常見錯誤排除)  
   A.2 [最佳實踐](#a2-最佳實踐)

---

## 第一部分：課前準備與環境安裝

### 1.1 安裝 Docker 或 NVM + Node.js

1. **檢查作業系統版本**  
   - Windows：10 以上  
   - macOS：至少 macOS 10.15（建議搭配 M1 或 M2 晶片以上）  
   - Linux：Ubuntu、Debian、CentOS 等常見發行版皆可

2. **安裝 Docker**  
   - 前往 [Docker 官網](https://docs.docker.com/get-docker/) 下載對應作業系統的安裝程式，並依照官方教學完成安裝  
   - Windows/Mac 安裝完成後，打開 Docker Desktop 確認 Docker 是否正常啟動

3. **安裝 NVM（若不使用 Docker，可直接使用 NVM + Node.js）**  
   - **Mac/Linux：**可使用 [官方 GitHub](https://github.com/nvm-sh/nvm) 指令安裝
   ```bash
   curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.3/install.sh | bash
   source ~/.bashrc
   nvm install --lts
   ```
   - **Windows：**可使用 [nvm-windows](https://github.com/coreybutler/nvm-windows) 安裝後，再安裝對應 Node.js 版本
   ```bash
   nvm install lts
   nvm use lts
   ```

4. **確認 Node.js 版本**  
   ```bash
   node -v
   # 建議版本為 16.x 或以上
   ```

### 1.2 安裝 n8n

1. **Docker 方式（建議）**  
   - 建立 `docker-compose.yml` 檔案並填入以下內容：
     ```yaml
     version: "3"

     services:
       n8n:
         image: n8nio/n8n:latest
         restart: always
         ports:
           - "5678:5678"
         volumes:
           - ~/.n8n:/home/node/.n8n
     ```
   - 於同資料夾內執行
     ```bash
     docker-compose up -d
     ```
   - 瀏覽器打開 `http://localhost:5678` 確認 n8n 是否正常啟動

2. **Node.js (NPM) 方式**  
   ```bash
   npm install n8n -g
   n8n start
   ```
   - 瀏覽器打開 `http://localhost:5678` 確認 n8n 是否正常啟動

### 1.3 安裝 Ollama

> **Ollama** 主要針對 macOS 上的 M1/M2 晶片，有相對應安裝包。若您使用其他作業系統，請參考 [Ollama 官方文件](https://github.com/jmorganca/ollama)。

1. **macOS (M1/M2) 安裝 Ollama**  
   ```bash
   brew install ollama/tap/ollama
   ```
   - 安裝完成後，輸入 `ollama run llama2` 可測試模型推論 (首次執行會下載模型檔)

2. **其他系統安裝**  
   - 參考 Ollama 官方文件或社群提供的方式

### 1.4 學員電腦配備建議
- **8GB 記憶體以上**（建議 **16GB 以上**）
- 若為 Mac，建議至少 **M1** 晶片以上

---

## 第二部分：n8n 基礎操作與介面介紹
![image](https://github.com/user-attachments/assets/a8e9adc0-f1f9-402f-b9de-66874d59d9bc)

圖：n8n 平台建置 AI Agent。使用者詢問問題，經開源的大型語言模型調用相對應的工具進行搜尋 Wikipedia 或是網路文章，再由大型語言模型根據開發者設定的固定格式進行資訊的整理輸出傳送到 Line上。

### 2.1 建立並管理工作流程（Workflow）
1. **進入 n8n UI** 之後，點選 **New** 建立新工作流程  
2. 工作流程畫面左側為 **Nodes 清單**，可搜尋自己所需的 Node  
3. 將節點拖曳 (Drag & Drop) 到右側編輯區域，透過連線 (Connector) 將各節點串接起來

### 2.2 常用節點（Nodes）簡介
- **Trigger 節點**：如 HTTP Trigger、Webhook、Timer 等，用於啟動工作流程的訊號  
- **Function 節點**：可在其中撰寫自訂 JavaScript 程式邏輯  
- **HTTP Request 節點**：對外部 API 進行 GET/POST/PUT/DELETE 等操作  
- **整合服務節點**：如 Slack、Gmail、Line、GitHub、Jira 等，可快速串接外部服務  

---

## 第三部分：AI Agent 與大語言模型 (LLM) 串接

### 3.1 AI Agent 基本概念
- **AI Agent** 是透過一個或多個大型語言模型 (LLM) 進行推理、決策，並在流程中根據需求自動呼叫外部工具或 API  
- 在 n8n 中，可以將 LLM 的訊問、回覆和工具使用流程都整合成一條完整工作流程  

### 3.2 實作步驟範例：從使用者輸入到 LLM 回覆
1. **建立 Chat Trigger 節點**  
   - 例如透過 Line bot 或 HTTP Webhook 接收使用者訊息
2. **連接 Ollama 節點 (或 ChatGPT API 節點) 做大語言模型推理**  
   - 將使用者輸入（prompt）傳送給 LLM，設定參數（溫度、max tokens、模型名稱等）
3. **調用外部工具**  
   - 例如 Wikipedia API 或 SERPAPI 節點搜尋外部資訊，將搜尋結果再餵回 LLM，讓 LLM 進行補充與組織
4. **固定格式輸出**  
   - 可以利用 Function 節點或 LLM 的 Prompt，讓回覆以 JSON、Markdown、或特定格式輸出
5. **將結果傳送回使用者**  
   - 例如使用 Line Node 或其他可回覆使用者的渠道  

---

## 第四部分：示範：建置一個會議記錄整理並透過 Line 回覆的 AI Agent

> 對應到課程表 Day 1 下午的「AI Agents 實作 - 會議記錄整理-Line bot 前置作業」與「Line串接」。

### 4.1 前置作業：Line Bot 設定

1. **在 Line Developers 建立 Messaging API**  
   - 前往 [Line Developers](https://developers.line.biz/) 建立一個 Channel  
   - 取得 **Channel Access Token** 與 **Channel Secret**
2. **建立 Webhook URL**  
   - 在 n8n 中建立 HTTP Trigger 或 Webhook 節點，複製該 URL 到 Line Developers 後台的 Webhook 區域
3. **開啟 Webhook**  
   - 設定「Use webhook」為啟用 (Enabled)，並對應到剛才的 n8n URL

### 4.2 Flow 實作流程

1. **Webhook/HTTP Trigger 節點**  
   - 監聽 Line Bot 傳入的使用者訊息
2. **Function 節點 (可選)**  
   - 解析使用者訊息結構 (JSON 解析、擷取文字)
3. **Ollama Chat Model 節點**  
   - Prompt 範例：
     ```plaintext
     你是一個會議記錄助手，請根據以下輸入的會議文字進行摘要，並以要點列舉的方式輸出。
     文字內容：{{$json["text"]}}
     ```
4. **(可選) 搜尋工具 節點**  
   - 若需要外部資訊補充，可使用 Wikipedia 或 SERPAPI 等
5. **Line 節點**  
   - 將 LLM 摘要結果回傳給使用者

**範例節點串接（簡化示意）**：
```
[Webhook] → [Function (parse message)] → [Ollama Chat Model] → [Line (reply)]
```

---

## 第五部分：RAG（檢索增強生成）簡介與實作

對應到課程表 Day 2「檢索增強生成簡介及實作」。

### 5.1 RAG 原理介紹
- **RAG（Retrieval-Augmented Generation）** 是一種結合「知識檢索」與「生成式模型」的技術流程  
- **基本流程**：
  1. 從資料庫或外部檢索工具中取得相關知識（檔案、網路文章、維基百科…等）  
  2. 將檢索結果作為提示 (Context) 輸入到 LLM  
  3. LLM 根據該 Context 進行更準確的回覆或生成  

### 5.2 實作步驟：以 Wikipedia + Ollama 為例
1. **建立 Webhook/HTTP Trigger 節點**  
   - 接收使用者提問（例如：「請問愛因斯坦的生平重點？」）
2. **Wikipedia Node**  
   - 使用者提問關鍵字進行維基百科搜索，擷取前幾段文字
3. **Function 節點**  
   - 將維基百科回傳文字整理成一個 Prompt Context
4. **Ollama Chat Model 節點**  
   - Prompt 範例：
     ```plaintext
     你是一個知識型助理，以下是維基百科提供的相關內容：
     {{ $json["context"] }}
     請根據內容回答下列問題：
     {{ $json["question"] }}
     如果不在提供的內容裡，就直接回答「抱歉，沒有相關資訊」。
     ```
5. **回傳給使用者**  
   - 透過 Line 或其他回覆管道

---

## 第六部分：特定情境問答機器人部署實作 – 以金融為例

對應到課程表 Day 2「13:00-14:20 特定情境問答機器人部署實作 - 以金融為例」。

### 6.1 金融情境資料蒐集
- 準備銀行業、股票或理財產品 FAQ、新聞等文本  
- 可自行建立一個簡易的知識庫 (JSON / CSV / Markdown / 自訂資料庫)

### 6.2 工作流程設計
1. **使用者提問**  
   - 範例：「請問我該怎麼投資基金？」
2. **檢索**  
   - 從金融 FAQ 資料庫中找到關聯度最高的段落
3. **LLM 回覆**  
   - 結合檢索到的內容，並可進行推理與語言整理後給出答案
4. **格式要求**  
   - 如果是金融建議，可加上免責聲明

**Prompt 參考**：
```plaintext
你是一個金融顧問，請根據以下查到的金融知識庫內容回答使用者問題。
如果查到的內容不足以回答，請如實說明。
查到的內容：
{{ $json["retrieved_content"] }}
使用者問題：
{{ $json["user_question"] }}
```

---

## 第七部分：小組專題討論與成果發表

對應到課程表 Day 1、Day 2 的小組實作與成果分享環節。

1. **選定應用主題**  
   - 例如自動化會議助手、特定產業問答機器人、RAG 知識庫系統等
2. **工作流程設計**  
   - 使用 n8n 串接所需工具 (Line Bot、SERPAPI、Ollama)  
3. **測試與優化**  
   - 確保在不同邏輯分支、不同輸入情境下都能得到預期結果  
4. **成果發表**  
   - 展示流程設計、系統 Demo、應用場景與後續改進方向

---

## 第八部分：Kuwa 介紹與應用（選修/延伸）

對應到 Day 2「09:00-10:20 Kuwa 介紹 (孫曉恩 | 台灣人工智慧學校技術處)」。

- **Kuwa 是什麼？**  
  - 一種針對資料治理、分散式存證或特定應用的區塊鏈解決方案（視實際課程內容而定）
- **與 n8n 或 AI Agents 的結合**  
  - 若需要將關鍵紀錄上鏈，可在 n8n 中串接 Kuwa 的 API 或 SDK  
  - 可以建立工作流程：LLM 產出資料 → Kuwa 紀錄/稽核 → 發送通知到各渠道  

---

## 附錄：常見錯誤排除與最佳實踐

### A.1 常見錯誤排除

1. **n8n 無法啟動**  
   - 檢查埠號 5678 是否被佔用  
   - Docker 版本是否過舊  
   - Node.js 版本相容性  
2. **Line Bot 無法回覆**  
   - 檢查是否正確設定 Webhook URL  
   - Channel Access Token 是否過期  
   - n8n Workflow 是否有正確部署  
3. **Ollama 模型無法載入**  
   - 確認硬體記憶體、M1/M2 晶片是否支援  
   - 模型檔是否已下載或放置在正確目錄

### A.2 最佳實踐

1. **Prompt Engineering**  
   - 為 LLM 設定清晰的身分與任務指令，可避免答非所問
2. **工作流程模組化**  
   - 建議把「資料檢索」、「格式整理」、「通知回傳」等步驟拆開，方便維護與除錯
3. **資料安全性**  
   - 自行托管 n8n 與開源 LLM，可避免機敏資料外洩到第三方 API
4. **觀察 & 監控**  
   - 加入錯誤通知或監控節點（如 Slack / Email / Line），確保流程健康運行

---

以上為 **「手把手」AI Agent 與 Agentic Workflow 實作教學** 的完整 Markdown 版本，可直接複製貼上至 GitHub 或 HackMD。若有更多問題，請隨時與助教或講師討論。祝學習順利！
