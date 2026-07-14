# 行動自然人憑證 App 網路流量分析報告

## 概述

本報告依據 SRTT 與分包截取工具分析**行動自然人憑證**（`tw.gov.moi.tfido`）App 的網路請求。此 App 由內政部提供，主要用途為**身分驗證與數位簽章**，使用者可透過生物辨識完成免插卡、免帳密之政府服務身分驗證。

| 項目 | 內容 |
|------|------|
| App 名稱 | 行動自然人憑證 |
| App 版本 | 2.7.5（2606181） |
| 裝置 | iPhone 16 Plus / iOS 27.0 |
| 擷取時間 | 2026-07-14 |
| 涉及網域 | 3 個 |

---

## 網域分析

| 網域 | 所屬單位 | 是否在台灣 | 是否為雲端服務 | 解析 IP | ASN | 國家 | 主要用途 |
|------|----------|------------|----------------|---------|-----|------|----------|
| `fido.moi.gov.tw` | 內政部（MOI） | 是 | 否 | 117.56.7.69 | AS4782 | TW | 身分驗證、簽章、推播 Token 註冊、App 內容 |
| `iid.googleapis.com` | Google | 是（Anycast 節點） | 是（Firebase Cloud Messaging） | 172.217.x.4<br>216.239.x.223<br>見下方 DNS 解析 | AS15169 | TW | 推播通知 Token 註冊與 Topic 訂閱 |
| `app-analytics-services.com` | Google | 是（Anycast 節點） | 是（Firebase / Google Analytics） | 142.250.77.206 | AS15169 | TW | App 使用行為分析 |

> **國家判定說明**：除 `117.56.7.69`（GSN，WHOIS 確認）外，其餘 Google 服務採 Anycast，連線節點判定位於台灣，營運商仍為 Google 海外雲端。

### 網域細部說明

#### 1. fido.moi.gov.tw（內政部，台灣，非雲端）

* **域名性質**：`.gov.tw` 政府域名，為內政部行動自然人憑證（FIDO）官方服務端點
* **地理位置**：解析 IP `117.56.7.69` 隸屬 **GSN（Government Service Network，政府服務網路）**，ASN **AS4782**（Data Communication Business Group），國家 **TW（台灣）**，機房位於台北
* **基礎設施**：非公有雲，推測為政府自建或 GSN 託管環境；回應標頭顯示 `Server: nginx`
* **角色**：App 核心後端，負責憑證查詢、數位簽章結果回報、FCM Token 綁定，以及 App 內嵌 WebView 頁面（最新消息、功能教學）
* **資料特性**：請求與回應含裝置資訊、憑證序號、使用者聯絡資訊、簽章結果等，屬**身分驗證核心資料**，均留在台灣政府網路範圍內
* **DNS 解析結果**（A Record）：

| 類型 | 解析 IP | ASN | 國家 |
|------|---------|-----|------|
| A | 117.56.7.69 | AS4782 | TW |

ASN **AS4782** 為 Data Communication Business Group（GSN）。

#### 2. iid.googleapis.com（Google，台灣 Anycast 節點，雲端）

* **域名性質**：Google Firebase Cloud Messaging（FCM）的 Instance ID 服務
* **地理位置**：判定連線節點位於**台灣**（Google Anycast 邊緣節點）
* **基礎設施**：Google Cloud 公有雲服務（AS15169）
* **角色**：App 向 Google 註冊推播 Token，並訂閱 `/topics/MobileMOICA` Topic，以便接收簽章請求等推播通知
* **資料特性**：傳送 App 識別（`tw.gov.moi.tfido`）、裝置 ID、FCM Token、App 版本；不含身分證字號或憑證內容，但 Token 可用於定向推播
* **DNS 解析結果**（A Record）：

| 類型 | 解析 IP | ASN | 國家 |
|------|---------|-----|------|
| A | 172.217.112.4 | AS15169 | TW |
| A | 172.217.113.4 | AS15169 | TW |
| A | 172.217.114.4 | AS15169 | TW |
| A | 172.217.115.4 | AS15169 | TW |
| A | 172.217.116.4 | AS15169 | TW |
| A | 172.217.117.4 | AS15169 | TW |
| A | 172.217.118.4 | AS15169 | TW |
| A | 172.217.119.4 | AS15169 | TW |
| A | 216.239.32.223 | AS15169 | TW |
| A | 216.239.34.223 | AS15169 | TW |
| A | 216.239.36.223 | AS15169 | TW |
| A | 216.239.38.223 | AS15169 | TW |

ASN **AS15169** 為 Google LLC。

#### 3. app-analytics-services.com（Google，台灣 Anycast 節點，雲端）

* **域名性質**：Google Firebase Analytics / App Analytics 服務
* **地理位置**：IP `142.250.77.206`，ASN **AS15169**（Google LLC），判定連線節點位於**台灣**
* **基礎設施**：Google Cloud 公有雲服務
* **角色**：收集 App 使用行為與事件資料，供開發團隊分析使用狀況
* **資料特性**：POST 請求 body 經 gzip 壓縮，回應為 204 No Content；屬**非核心業務**之輔助分析流量
* **DNS 解析結果**（A Record）：

| 類型 | 解析 IP | ASN | 國家 |
|------|---------|-----|------|
| A | 142.250.77.206 | AS15169 | TW |

ASN **AS15169** 為 Google LLC。

---

## API 用途整理

### 一、身分驗證與憑證管理（fido.moi.gov.tw）

此類 API 為 App 核心功能，所有請求均以 JSON 格式傳送，並附帶 `signed_user_data`（Base64 編碼之交易資訊）與 `signature_of_user_data`（以行動憑證私鑰簽章），確保請求不可否認性。

#### POST `/moise/app/queryUserInfo`

查詢使用者、裝置或交易紀錄，依 `target_code` 決定回傳內容：

| target_code | 用途 | 回傳資料 |
|-------------|------|----------|
| `DEV` | 查詢裝置與憑證狀態 | 裝置型號、OS、FIDO 狀態、憑證序號（`mcert_enc_cert_sn`）、憑證有效期、Push Token、已綁定裝置數 |
| `USR` | 查詢使用者基本資料 | 姓名（`username`）、Email、電話、憑證是否公開 |
| `TXN` | 查詢近期操作紀錄 | 操作類型（`SIGN` 等）、服務提供者名稱、操作時間、結果 |

* **觸發時機**：App 啟動、進入首頁、簽章完成後刷新狀態
* **安全機制**：每筆請求含唯一 `transaction_id` 與時間戳，並以憑證私鑰簽章

#### POST `/moise/app/reportSignResult`

回報數位簽章結果至內政部後端。

* **觸發時機**：使用者完成生物辨識並執行簽章後
* **請求內容**：`sign_info` 含簽章所用憑證（X.509）、簽章值、簽章演算法等
* **回應**：`{"error_code":"0","error_message":"SUCCESS"}` 表示簽章結果已成功回報
* **用途**：此為 App **最核心的身分驗證流程**——將本地簽章結果提交給政府服務端，供外部 SP（Service Provider）驗證

#### POST `/moise/app/fcmtoken`

將 FCM 推播 Token 綁定至使用者裝置與憑證。

* **觸發時機**：App 啟動或 Token 更新時
* **請求內容**：裝置資訊（型號、OS 版本）、憑證序號（`enc_sn`）、FCM Token、簽章資料
* **用途**：讓內政部後端能在使用者需簽章時，透過 FCM 推播通知喚醒 App

---

### 二、App 內容頁面（fido.moi.gov.tw）

App 透過 WebView 載入內政部網站頁面，供使用者瀏覽公告與教學。

| 方法 | 路徑 | 用途 |
|------|------|------|
| GET | `/pt/news?IsApp=Y` | 最新消息列表 |
| GET | `/pt/news_detail?id=80085&IsApp=Y` | 消息詳情（例：v2.7.5 版本更新公告） |
| GET | `/pt/teaching?IsApp=Y` | 功能教學頁面 |

* **特性**：`IsApp=Y` 參數表示由 App 內 WebView 開啟，頁面會套用 App 專用樣式
* **資料特性**：純靜態 HTML，不涉及身分驗證資料

---

### 三、推播通知（iid.googleapis.com）

#### POST `/iid/register`

向 Google FCM 註冊推播 Token 並訂閱 Topic。

* **請求參數**：

| 參數 | 值 | 說明 |
|------|-----|------|
| `app` | `tw.gov.moi.tfido` | App 識別 |
| `app_ver` | `2.7.5` | App 版本 |
| `device` | 裝置 Instance ID | FCM 裝置識別 |
| `sender` | FCM Server Key | 推播發送端識別 |
| `X-gcm.topic` | `/topics/MobileMOICA` | 訂閱之推播 Topic |

* **用途**：完成 Token 取得後，App 再將 Token 提交至 `fido.moi.gov.tw/moise/app/fcmtoken` 完成綁定
* **流程**：Google FCM 註冊 → 取得 Token → 提交至內政部後端綁定

---

### 四、使用分析（app-analytics-services.com）

#### POST `/a`

向 Google 回報 App 使用事件。

* **請求格式**：`application/x-www-form-urlencoded`，body 經 gzip 壓縮
* **回應**：HTTP 204 No Content
* **用途**：Firebase / Google Analytics 事件收集，用於 App 使用狀況分析
* **與核心功能關係**：不參與身分驗證流程，屬輔助性第三方服務

---

## 請求流程概觀

```
App 啟動
  ├─→ Google FCM 註冊 Token          (iid.googleapis.com)
  ├─→ 綁定 Token 至內政部後端         (fido.moi.gov.tw/fcmtoken)
  ├─→ 查詢裝置／使用者／交易紀錄       (fido.moi.gov.tw/queryUserInfo)
  └─→ 回報 Analytics 事件            (app-analytics-services.com)

使用者收到簽章推播 → 生物辨識 → 本地簽章
  └─→ 回報簽章結果                   (fido.moi.gov.tw/reportSignResult)
  └─→ 刷新使用者／裝置狀態           (fido.moi.gov.tw/queryUserInfo)

使用者瀏覽公告／教學
  └─→ 載入 WebView 頁面              (fido.moi.gov.tw/pt/*)
```

---

## 摘要

| 分類 | 網域 | 是否核心功能 | 連線節點 | 資料是否出境 |
|------|------|--------------|----------|--------------|
| 身分驗證／簽章 | `fido.moi.gov.tw` | 是 | 台灣（GSN） | 否 |
| 推播通知 | `iid.googleapis.com` | 是（輔助） | 台灣（Google Anycast） | 可能（Google 海外營運） |
| 使用分析 | `app-analytics-services.com` | 否 | 台灣（Google Anycast） | 可能（Google 海外營運） |

行動自然人憑證 App 的**核心身分驗證與簽章流程**完全走內政部自建之 `fido.moi.gov.tw`（台灣 GSN）。推播與分析雖連線至 Google 台灣 Anycast 節點，營運商仍為 Google 海外雲端，傳送內容為 Token 與使用事件，不含憑證私鑰或完整身分資料。
