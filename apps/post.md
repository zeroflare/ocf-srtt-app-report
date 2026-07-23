---
title: 郵局（中華郵政）
---

# 行動郵局（中華郵政）App 網路流量分析報告

## 概述

<img src="https://is1-ssl.mzstatic.com/image/thumb/Purple221/v4/ef/40/24/ef402436-f7c9-d17f-d041-9d7e3f6b0316/AppIcon-0-0-1x_U007emarketing-0-8-0-85-220.png/512x512bb.jpg" alt="行動郵局 App 圖示" width="150" height="150" style="border-radius: 22%; object-fit: cover; box-shadow: 0 4px 12px rgba(0, 0, 0, 0.18);">

本報告依據 SRTT 與分包截取工具分析**行動郵局**（`tw.gov.post.mpost`）App 的網路請求。此 App 由**中華郵政股份有限公司**提供，整合郵務與儲匯服務，功能包含郵件狀態查詢、寄件服務、i 郵箱、快捷郵件時效查詢、地址英譯、郵遞區號查詢、郵資費率、投遞郵局查詢、查匯利率（外幣匯率／存簿儲金利率）、據點查詢（地圖）等，並提供帳戶登入後之網路郵局／儲匯功能。

本次測試以**郵務類查詢功能**為主（多為 WebView 或跳離 App 開啟），未登入帳戶、未進行金融交易。

| 項目 | 內容 |
|------|------|
| App 名稱 | 行動郵局 |
| App 版本 | 1.52.0 |
| 裝置 | iPhone 16 Pro / iOS 26.5.2 |
| 擷取時間 | 2026-07-22 |
| 涉及網域 | 14 個（App 相關；另有 iOS 系統背景網域已排除） |

> **測試方式與歸屬說明**：本次為「各功能點選瀏覽」之測試，觀察到之請求以頁面／資料載入（GET）為主；多數功能會跳離 App 或以 WebView 開啟。本報告以**網域與資料流向**為分析主軸；HTTP 端點層級（method／path／個資欄位／加密方式）之細節不在本次擷取範圍內。因 SRTT／MITM 為**全裝置**抓包，本報告經**多次隔離冷啟動**（徹底關閉 App、關閉其他所有 App、開飛航僅留 Wi-Fi）重測交叉比對，**僅納入可穩定重現、可歸屬於行動郵局之網域**；屬其他 App／系統背景之流量已排除。SRTT 未擷取到之 Stream 網域，另以 DNS／Cymru 補齊 IP 與 ASN。

---

## 網域分析

以下依**所屬單位／角色**分為四類彙整。iOS 系統層背景網域已於文末另列並排除。

| 網域 | 所屬單位 | 國家 | 雲端 | ASN | 主要用途 |
|------|----------|------|------|-----|----------|
| `mfp.post.gov.tw` | 中華郵政 | 台灣 | 否（電信機房） | AS3462 / AS24154 | 行動郵局主要後端 |
| `mpost.post.gov.tw` | 中華郵政 | 台灣 | 否（電信機房） | AS3462 / AS9924 | 行動郵局服務 |
| `postserv.post.gov.tw` | 中華郵政 | 台灣 | 否（電信機房） | AS3462 / AS24154 | 郵務服務 |
| `emap.post.gov.tw` | 中華郵政 | 台灣 | 否（電信機房） | AS3462 / AS24154 | 據點查詢地圖後端 |
| `ipost.post.gov.tw` | 中華郵政 | 台灣 | 否（電信機房） | AS9924 | i 郵箱 |
| `mpush.post.gov.tw` | 中華郵政 | 台灣 | 否（電信機房） | AS7482 / AS24154 | 推播通知 |
| `www.post.gov.tw` | 中華郵政 | 台灣 | 否（電信機房） | AS3462 | 官方網站（公開資訊） |
| `sslserver.twca.com.tw` | 臺灣網路認證 TWCA | 台灣 | 否（CA 機房） | AS9924 | SSL 憑證服務 |
| `evsslocsp.twca.com.tw` | 臺灣網路認證 TWCA | 台灣 | 否（CA 機房） | AS9924 | EV SSL 憑證狀態查詢（OCSP） |
| `rootocsp.twca.com.tw` | 臺灣網路認證 TWCA | 台灣 | 否（CA 機房） | AS9924 | 根憑證狀態查詢（OCSP） |
| `tile.tracestrack.com` | Tracestrack（地圖圖磚） | 海外節點 | 是（Cloudflare） | AS13335 | 據點查詢地圖圖磚 |
| `ssl.google-analytics.com` | Google | 海外節點 | 是 | AS15169 | 使用行為分析 |
| `app-analytics-services.com` | Google | 台灣（節點） | 是 | AS15169 | App 使用分析 |
| `fonts.gstatic.com` | Google | 海外節點 | 是 | AS15169 | 網頁字型 |

> **國家判定說明**：中華郵政各 `*.post.gov.tw` 服務網域解析至台灣電信業者機房，並跨多家業者多重連線（**AS3462 中華電信 HiNet、AS24154 亞太電信、AS9924 台灣固網、AS7482 數位聯合 Seednet**），皆位於台灣境內、非公有雲。身分憑證服務（TWCA，AS9924）亦為台灣。`tile.tracestrack.com`（地圖圖磚，Cloudflare）與 Google 分析／字型（AS15169）採 Anycast，連線節點雖多在台灣，營運商為海外雲端，出境記「可能」。

### 1. 中華郵政核心服務（`*.post.gov.tw`，台灣，非公有雲）

* **域名性質**：`.post.gov.tw` 政府／公營事業域名，為行動郵局各功能之後端
* **地理位置**：解析至 `124.219.11x.x`／`203.69.145.x`／`175.98.173.x`，分屬 **AS3462（HiNet）、AS24154（亞太電信）、AS9924（台灣固網）、AS7482（數位聯合 Seednet）**，均為台灣
* **基礎設施**：託管於多家台灣電信業者機房（多重連線以提升可用性），非公有雲
* **角色**：郵件狀態查詢、寄件服務、i 郵箱（`ipost`）、時效查詢、郵資費率、據點地圖後端（`emap`）、推播（`mpush`）、以及帳戶登入後之網路郵局／儲匯服務
* **資料特性**：本次以查詢瀏覽為主；登入後之金融與個資交易未於本次觸發，惟核心服務主機均在台灣境內
* **DNS 解析結果**（A Record，代表值）：

| 網域 | 解析 IP | ASN | 國家 |
|------|---------|-----|------|
| mfp.post.gov.tw | 124.219.114.109 | AS3462 / AS24154 | TW |
| mpost.post.gov.tw | 203.69.145.114 | AS3462 / AS9924 | TW |
| postserv.post.gov.tw | 124.219.115.108 | AS3462 / AS24154 | TW |
| emap.post.gov.tw | 203.69.145.120 | AS3462 / AS24154 | TW |
| ipost.post.gov.tw | 175.98.173.23 | AS9924 | TW |
| mpush.post.gov.tw | 124.219.114.137 | AS7482 / AS24154 | TW |
| www.post.gov.tw | 203.69.145.167 | AS3462 | TW |

AS3462＝中華電信 HiNet；AS24154＝亞太電信（APBB）；AS9924＝台灣固網；AS7482＝數位聯合 Seednet。

### 2. 憑證服務（TWCA，台灣，非公有雲）

* **域名性質**：`sslserver`／`evsslocsp`／`rootocsp.twca.com.tw` 為臺灣網路認證（TWCA）之 SSL 憑證與憑證狀態查詢（OCSP）服務
* **地理位置**：解析至 `219.87.64.x`，ASN **AS9924（台灣固網）**，台灣
* **角色**：驗證連線所用之 SSL／EV 憑證是否有效，確保 App 與後端之 TLS 連線可信；憑證狀態查詢於首次 TLS 握手時發生，之後由 OS 快取
* **資料特性**：憑證序號與狀態查詢，不涉個人業務資料；營運單位為台灣境內
* **DNS 解析結果**：

| 網域 | 解析 IP | ASN | 國家 |
|------|---------|-----|------|
| sslserver.twca.com.tw | 219.87.64.186 | AS9924 | TW |
| evsslocsp.twca.com.tw | 219.87.64.165 | AS9924 | TW |
| rootocsp.twca.com.tw | 219.87.64.165 | AS9924 | TW |

### 3. 地圖圖磚（Tracestrack，海外雲端）

* **域名性質**：`tile.tracestrack.com` 為 Tracestrack 地圖圖磚服務
* **地理位置**：解析至 `104.21.9.167`，ASN **AS13335（Cloudflare）**，Anycast，連線節點判定台灣
* **角色**：「據點查詢」地圖底圖（搭配 OpenStreetMap 資料）；為功能性用途，開啟地圖時載入
* **資料特性**：地圖圖磚，不涉個資；營運商為海外雲端（Cloudflare）
* **DNS 解析結果**：

| 網域 | 解析 IP | ASN | 國家 |
|------|---------|-----|------|
| tile.tracestrack.com | 104.21.9.167 | AS13335 | 海外節點 |

### 4. Google 分析與字型（海外雲端）

* **域名性質**：`ssl.google-analytics.com`、`app-analytics-services.com`（使用行為分析）、`fonts.gstatic.com`（網頁字型）
* **地理位置**：ASN **AS15169（Google LLC）**，Anycast
* **角色**：收集 App 使用事件、載入網頁字型；屬非核心第三方輔助服務
* **資料特性**：傳送使用事件與裝置資訊，營運商為 Google 海外
* **DNS 解析結果**：

| 網域 | 解析 IP | ASN | 國家 |
|------|---------|-----|------|
| ssl.google-analytics.com | 64.233.188.97 | AS15169 | 海外節點 |
| app-analytics-services.com | 74.125.204.100 | AS15169 | 台灣（節點） |
| fonts.gstatic.com | 172.253.155.94 | AS15169 | 海外節點 |

### 5. iOS 系統背景網域（非 App 業務流量，已排除）

下列為 iOS 系統／推播／CDN 背景流量，與行動郵局功能無關，於分析中排除，僅列出供辨識：`init.push.apple.com`、`gateway.fe2.apple-dns.net`（Apple 推播／服務）、`sequoia.cdn-apple.com`（Apple 內容 CDN）。

---

## API 用途整理

> **說明**：本次為瀏覽式測試，觀察到之請求以頁面／資料載入（GET）為主，多數功能跳離 App 或以 WebView 開啟，故此節依「網域／功能角色」整理，不列具體端點與請求參數。登入後之網路郵局／儲匯交易未於本次觸發，留待後續加測。

### 一、郵務查詢與服務（`*.post.gov.tw`）

郵件狀態查詢、快捷郵件時效查詢、地址英譯、郵遞區號查詢、郵資費率、投遞郵局查詢等，向中華郵政 `mfp`／`mpost`／`postserv.post.gov.tw` 取得；i 郵箱走 `ipost.post.gov.tw`；據點查詢地圖走 `emap.post.gov.tw`（底圖由 Tracestrack 提供）；推播走 `mpush.post.gov.tw`。均為台灣境內主機。

### 二、憑證驗證（TWCA）

連線過程向 TWCA（`sslserver`／`evsslocsp`／`rootocsp.twca.com.tw`）查詢 SSL／EV 憑證狀態，確保 TLS 連線可信。台灣境內。

### 三、地圖與分析（Tracestrack、Google）

據點查詢地圖底圖由 Tracestrack（Cloudflare）提供；App 另嵌入 Google 使用分析與網頁字型。此類為非核心之第三方輔助服務。

---

## 請求流程概觀

```
App 冷啟動 / 郵務服務首頁
  ├─→ 載入主要後端                      (mfp / mpost / postserv.post.gov.tw ← 台灣電信)
  ├─→ 憑證狀態查詢 (OCSP，首次握手)      (sslserver / evsslocsp / rootocsp.twca.com.tw)
  ├─→ 使用分析 / 字型 (Google)          (ssl.google-analytics.com, fonts.gstatic.com)

各功能（多為 WebView 或跳離 App）
  ├─ 郵件狀態 / 時效 / 地址英譯 / 郵資 → (mfp / mpost / postserv.post.gov.tw)
  ├─ i 郵箱 →                            (ipost.post.gov.tw)
  ├─ 據點查詢（地圖）→ 後端 + 圖磚       (emap.post.gov.tw + tile.tracestrack.com)
  └─ 推播 →                              (mpush.post.gov.tw)

（帳戶登入 / 儲匯交易：本次未觸發）
```

---

## 摘要

| 分類 | 網域 | 是否核心功能 | 連線節點 | 資料是否出境 |
|------|------|--------------|----------|--------------|
| 中華郵政核心服務 | `*.post.gov.tw` | 是 | 台灣（多家電信） | 否 |
| 憑證服務 | `*.twca.com.tw` | 是（輔助） | 台灣 | 否 |
| 地圖圖磚（Tracestrack） | `tile.tracestrack.com` | 是（輔助） | 海外節點 | 可能 |
| Google 分析／字型 | `google-analytics`／`gstatic` | 否 | 海外節點 | 可能 |

行動郵局的**核心郵務與（未測試之）儲匯服務**走中華郵政自有的 `*.post.gov.tw`，託管於多家台灣電信業者（HiNet、亞太、台固、Seednet），並以 TWCA 憑證驗證，皆在台灣境內，此部分資料保護符合期待。其餘為「據點查詢」地圖底圖（Tracestrack）與 Google 使用分析／字型，屬非核心之第三方輔助，連線節點多在台灣 Anycast、營運商為海外。

> **隱私風險評估**：以可重現、可歸屬於行動郵局之流量而言，核心郵務與儲匯後端在台灣境內，風險低；非核心之第三方僅地圖圖磚與 Google 分析／字型，會傳送地圖請求與使用行為至海外，屬常見之輔助流量。本次為未登入之瀏覽式測試，登入與寄件流程之個資傳輸內容與加密方式，建議後續針對單一功能加測 HTTP 明細以完整評估。
