---
title: 郵局（中華郵政）
---

# 行動郵局（中華郵政）App 網路流量分析報告

## 概述

<img src="https://is1-ssl.mzstatic.com/image/thumb/Purple221/v4/ef/40/24/ef402436-f7c9-d17f-d041-9d7e3f6b0316/AppIcon-0-0-1x_U007emarketing-0-8-0-85-220.png/512x512bb.jpg" alt="行動郵局 App 圖示" width="150" height="150" style="border-radius: 22%; object-fit: cover; box-shadow: 0 4px 12px rgba(0, 0, 0, 0.18);">

本報告依據 SRTT 與封包擷取工具分析**行動郵局**（`tw.gov.post.mpost`）App 的網路請求。此 App 由**中華郵政股份有限公司**提供，整合郵務與儲匯服務，功能包含郵件狀態查詢、寄件服務、i 郵箱、快捷郵件時效查詢、地址英譯、郵遞區號查詢、郵資費率、投遞郵局查詢、查匯利率（外幣匯率／存簿儲金利率）、據點查詢（地圖）等，並提供帳戶登入後之網路郵局／儲匯功能。

本次測試以**郵務類查詢功能**為主（多為 WebView 或跳離 App 開啟），未登入帳戶、未進行金融交易。

| 項目 | 內容 |
|------|------|
| App 名稱 | 行動郵局 |
| App 版本 | 1.52.0 |
| 裝置 | iPhone 16 Pro / iOS 26.5.2 |
| 擷取時間 | 2026-07-22（初測）／**2026-07-30（複測，本報告以複測為準）** |
| 涉及網域 | 初測彙整 14 個；**複測觀察到 10 個 App 相關主機**（另 2 個 Google CalDAV 為 iOS 帳號同步、已排除） |

> **測試方式與歸屬說明**：複測實際操作郵務查詢（郵遞區號／地址、i郵箱、據點地圖、郵政商城）等功能，觀察到 **POST API 請求**（查詢與遙測），仍未登入帳戶、未進行金融交易。為保護測試者，內文僅描述欄位類型。**本次擷取採 HTTPS MITM，部分採憑證綁定（cert pinning）之連線無法解密，僅以 CONNECT 出現、內容未還原**（如 `mpost`／`mpush.post.gov.tw`）；此類仍可由連線 IP 判定歸屬與國家。因 SRTT／MITM 為**全裝置**擷取，屬其他 App／系統背景之流量（如 iOS 行事曆對 Google 帳號之 CalDAV 同步）已排除。初測曾見但本次未再觸發之網域（`ipost`、TWCA OCSP 等），沿用初測 DNS 佐證。
>
> **關於「儲金保險登入／儲匯」未擷取到之原因**：行動郵局在**登入與金融（儲匯）功能**上實作了**連線加密偵測**——當偵測到 MITM 憑證（本次側錄所用之 Stream 憑證）時，App 會主動判定「SSL 異常」並**基於安全考量停止提供該功能**，跳出 **ES-0103** 警告（見下圖）。這是**良好的資安設計**（可防範中間人攔截金融資料），但也使得本次無法解密其登入／儲匯流量；此類受保護之流量僅能以 CONNECT 之連線 IP 判定其落點（台灣）。

### 擷取限制示意：登入功能之 SSL 異常偵測（ES-0103）

<figure style="margin:1.5em 0; text-align:center;">
  <img src="{{ '/img/post.png' | relative_url }}" alt="行動郵局 SSL 連線異常警告（ES-0103）" style="max-width:300px; width:100%; border:1px solid #ddd; border-radius:14px; box-shadow:0 4px 12px rgba(0,0,0,0.12);">
  <figcaption style="font-size:0.9em; color:#666; margin-top:0.6em;">▲ 行動郵局於「儲金保險登入」偵測到連線加密（SSL）異常，跳出 ES-0103 警告並停止提供登入／儲匯功能。此為 App 之資安防護（防中間人攔截），亦是本次無法解密其金融流量之原因。</figcaption>
</figure>

> **不同 App 對 MITM 憑證之反應不一**：以 Stream 憑證側錄 HTTPS 時，各 App 反應不同——(1) **主動偵測並提示**（如行動郵局 ES-0103，明確告知並封鎖敏感功能）；(2) **憑證綁定使連線僅剩 CONNECT／內容不解密**（如台鐵、蝦皮、部分台灣Pay）；(3) **直接故障或跳出錯誤訊息**而無法操作。本系列各報告已就各 App 之實際情形分別說明；凡未能解密者，一律僅以域名與連線 IP 據實記錄、不臆測內容。

---

## 網域分析

以下依**所屬單位／角色**分為五類彙整，國家與雲端歸屬以本次 HAR 內**實際連線的 server IP** 為準。iOS 系統層背景網域已於文末另列並排除。

| 網域 | 所屬單位 | 國家 | 雲端 | ASN | 主要用途 |
|------|----------|------|------|-----|----------|
| `mfp.post.gov.tw` | 中華郵政 | 台灣 | 否（電信機房） | AS3462 / FET | **App 自家事件遙測收集**（`/post/collect`） |
| `postserv.post.gov.tw` | 中華郵政 | 台灣 | 否（電信機房） | AS3462 / FET | 郵務／i郵箱交易後端（`pstmail`） |
| `www.postmall.com.tw` | 中華郵政（郵政商城 ePost） | 台灣 | 否（電信機房） | FET | 郵政商城／首頁工具列（`/epost/`） |
| `emap.post.gov.tw` | 中華郵政 | 台灣 | 否（電信機房） | AS3462 / FET | 據點查詢地圖後端＋自有圖磚（`tu_m`） |
| `www.post.gov.tw` | 中華郵政 | 台灣 | 否（電信機房） | AS3462 | 官方網站＋郵遞區號／地址查詢 |
| `mpost.post.gov.tw` | 中華郵政 | 台灣 | 否（電信機房） | FET / AS9924 | 行動郵局服務（本次僅 CONNECT、未解密） |
| `mpush.post.gov.tw` | 中華郵政 | 台灣 | 否（電信機房） | AS7482 / FET | 推播通知（本次僅 CONNECT、未解密） |
| `ipost.post.gov.tw` | 中華郵政 | 台灣 | 否（電信機房） | AS9924 | i 郵箱（本次未觸發，沿用初測） |
| `sslserver.twca.com.tw` | 臺灣網路認證 TWCA | 台灣 | 否（CA 機房） | AS9924 | SSL 憑證服務 |
| `evsslocsp.twca.com.tw` | 臺灣網路認證 TWCA | 台灣 | 否（CA 機房） | AS9924 | EV SSL 憑證狀態查詢（OCSP） |
| `rootocsp.twca.com.tw` | 臺灣網路認證 TWCA | 台灣 | 否（CA 機房） | AS9924 | 根憑證狀態查詢（OCSP） |
| `tile.tracestrack.com` | Tracestrack（地圖圖磚） | 海外節點 | 是（Cloudflare） | AS13335 | 據點查詢地圖圖磚 |
| `ssl.google-analytics.com` | Google | 海外節點 | 是 | AS15169 | 使用行為分析 |
| `app-analytics-services.com` | Google | 台灣（節點） | 是 | AS15169 | App 使用分析 |
| `fonts.gstatic.com` | Google | 海外節點 | 是 | AS15169 | 網頁字型 |

> **國家判定說明（以本次實際連線 IP 為準）**：中華郵政各 `*.post.gov.tw` 與郵政商城 `www.postmall.com.tw` 皆連台灣電信業者機房，跨多家業者多重連線——`124.219.11x.x` 經 whois 為 **遠傳電信（FET，原亞太電信 APBB 於 2024 併入）**、`203.69.145.x` 為 **中華電信 HiNet（AS3462）**、另有台灣固網（AS9924）、數位聯合 Seednet（AS7482），皆位於台灣境內、非公有雲。身分憑證服務（TWCA，AS9924）亦為台灣。`tile.tracestrack.com`（地圖圖磚，Cloudflare）與 Google 分析／字型（AS15169）採 Anycast，連線節點雖多在台灣，營運商為海外雲端，出境記「可能」。此外本次見 `google.com`／`calendar.google.com` 之 WebDAV（`REPORT`／`PROPFIND`）請求，為 **iOS 行事曆對 Google 帳號之 CalDAV 同步**，非行動郵局流量，已排除。

### 1. 中華郵政核心服務（`*.post.gov.tw`，台灣，非公有雲）

* **域名性質**：`.post.gov.tw` 政府／公營事業域名，為行動郵局各功能之後端
* **地理位置**：解析至 `124.219.11x.x`／`203.69.145.x`／`175.98.173.x`，分屬 **AS3462（HiNet）、AS24154（亞太電信）、AS9924（台灣固網）、AS7482（數位聯合 Seednet）**，均為台灣
* **基礎設施**：託管於多家台灣電信業者機房（多重連線以提升可用性），非公有雲
* **角色**：郵件狀態查詢、寄件、i 郵箱、時效查詢、郵資費率、據點地圖（`emap`）、郵政商城（`postmall`）、推播（`mpush`），以及帳戶登入後之網路郵局／儲匯服務
* **本次實測 API 佐證**：
  * **自家事件遙測**：`POST mfp.post.gov.tw/post/collect`（56 次，JSON）——送出 App 安裝 `uuid`、裝置雜湊（`mac`，非真實網卡位址）、`eventList`（點擊事件）等，`customerId` 為空（未登入）。此為**中華郵政自建之第一方分析收集器，落點台灣**（相對於外送 Google，隱私上較佳）。
  * **郵務／i郵箱交易**：`POST postserv.post.gov.tw/pstmail/EsoafDispatcher`（JSON，Systex jbranch 交易框架，`TxnCode` 如 `EB500100`），並帶 `jcaptcha` 圖形驗證碼、`SessionServlet`；WebView 為 `main_mail.html`。
  * **郵遞區號／地址查詢**：`POST www.post.gov.tw/post/APP/postal/index.jsp`（帶縣市／鄉鎮區等查詢參數，不涉個資）。
  * **郵政商城／首頁工具列**：`GET www.postmall.com.tw/ajax/ajaxEpostIndexToollist.aspx` 等（`/epost/`）。
  * **據點地圖**：`emap.post.gov.tw` 除後端外，另供**自有點陣圖磚**（`/tu_m/…gif`），與 `tile.tracestrack.com` 底圖搭配。
  * 上述請求**皆送往台灣境內主機，未出境**；登入後之金融與個資交易本次未觸發。
* **DNS／連線 IP**（本次實際連線代表值）：

| 網域 | 連線 IP | ASN／所屬 | 國家 |
|------|---------|-----------|------|
| mfp.post.gov.tw | 124.219.114/115.109、203.69.145.109 | FET／HiNet | TW |
| postserv.post.gov.tw | 124.219.115.108 | FET | TW |
| www.postmall.com.tw | 124.219.115.153 | FET | TW |
| emap.post.gov.tw | 124.219.114.120 | FET | TW |
| www.post.gov.tw | 175.98.228.167 | 台灣固網 | TW |
| mpost.post.gov.tw | 175.98.228.114、124.219.115.114 | 台固／FET（僅 CONNECT） | TW |
| mpush.post.gov.tw | 124.219.115.137 | FET（僅 CONNECT） | TW |

`124.219.11x.x` 經 whois 為遠傳電信（FET，原亞太電信 APBB）；`203.69.145.x` 為中華電信 HiNet（AS3462）；`175.98.x` 為台灣固網（AS9924）；AS7482 為數位聯合 Seednet。`mpost`／`mpush` 本次因憑證綁定僅見 CONNECT、內容未解密，惟連線 IP 仍在台灣。

### 2. 憑證服務（TWCA，台灣，非公有雲）

* **域名性質**：`sslserver`／`evsslocsp`／`rootocsp.twca.com.tw` 為臺灣網路認證（TWCA）之 SSL 憑證與憑證狀態查詢（OCSP）服務
* **地理位置**：解析至 `219.87.64.x`，ASN **AS9924（台灣固網）**，台灣
* **角色**：驗證連線所用之 SSL／EV 憑證是否有效，確保 App 與後端之 TLS 連線可信；憑證狀態查詢於首次 TLS 握手時發生，之後由 OS 快取（**本次複測因憑證狀態已快取而未再觸發，沿用初測 DNS 佐證**）
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

下列為 iOS 系統／推播／CDN／帳號同步之背景流量，與行動郵局功能無關，於分析中排除，僅列出供辨識：`init.push.apple.com`、`gateway.fe2.apple-dns.net`（Apple 推播／服務）、`sequoia.cdn-apple.com`（Apple 內容 CDN）；以及 `google.com`／`calendar.google.com` 之 CalDAV 同步（`REPORT`／`PROPFIND`／`OPTIONS`，為 iOS 行事曆連結 Google 帳號所致）。

---

## API 用途整理

> **說明**：複測實際操作查詢功能，觀察到 POST API（含自家遙測與郵務交易）；為保護測試者僅描述欄位類型。登入後之網路郵局／儲匯交易未於本次觸發。

### 一、郵務查詢與服務（`*.post.gov.tw`、`postmall`）

郵件狀態、時效、地址英譯、郵遞區號查詢、郵資費率等，向中華郵政 `postserv`（`pstmail/EsoafDispatcher` 交易後端）與 `www.post.gov.tw`（郵遞區號查詢）取得；郵政商城與首頁工具列走 `www.postmall.com.tw`；據點查詢地圖走 `emap.post.gov.tw`（自有圖磚 `tu_m` + Tracestrack 底圖）；`mfp.post.gov.tw/post/collect` 為 App 自家事件遙測收集器。i 郵箱（`ipost`）、推播（`mpush`）本次未解密／未觸發。均為台灣境內主機。

### 二、憑證驗證（TWCA）

連線過程向 TWCA（`sslserver`／`evsslocsp`／`rootocsp.twca.com.tw`）查詢 SSL／EV 憑證狀態，確保 TLS 連線可信。台灣境內。

### 三、地圖與分析（Tracestrack、Google）

據點查詢地圖底圖由 Tracestrack（Cloudflare）提供；App 另嵌入 Google 使用分析與網頁字型。此類為非核心之第三方輔助服務。

---

## 請求流程概觀

```
App 冷啟動 / 郵務服務首頁
  ├─→ 首頁工具列 / 郵政商城              (www.postmall.com.tw ← 台灣 FET)
  ├─→ 自家事件遙測 (第一方，台灣)        (mfp.post.gov.tw/post/collect)
  ├─→ 憑證狀態查詢 (OCSP，OS 快取)       (…twca.com.tw；本次未再觸發)
  └─→ 使用分析 / 字型 (Google，海外)     (ssl.google-analytics.com, fonts.gstatic.com)

各功能（多為 WebView 或跳離 App）
  ├─ 郵務 / i郵箱交易 → POST            (postserv.post.gov.tw/pstmail ← jbranch + jcaptcha)
  ├─ 郵遞區號 / 地址查詢 → POST          (www.post.gov.tw/post/APP/postal)
  ├─ 據點查詢（地圖）→ 後端 + 自有/第三方圖磚 (emap.post.gov.tw/tu_m + tile.tracestrack.com)
  └─ 行動郵局服務 / 推播 → CONNECT 未解密 (mpost / mpush.post.gov.tw ← 台灣，憑證綁定)

（帳戶登入 / 儲匯交易：本次未觸發）
（iOS 行事曆 Google CalDAV 同步：非本 App，已排除）
```

---

## 摘要

| 分類 | 網域 | 是否核心功能 | 連線節點 | 資料是否出境 |
|------|------|--------------|----------|--------------|
| 中華郵政核心服務（含郵務交易 POST） | `*.post.gov.tw` | 是 | 台灣（多家電信） | 否 |
| 郵政商城 | `www.postmall.com.tw` | 是 | 台灣（FET） | 否 |
| 自家事件遙測（第一方分析） | `mfp.post.gov.tw/post/collect` | — | 台灣 | 否 |
| 憑證服務 | `*.twca.com.tw` | 是（輔助） | 台灣 | 否（本次未觸發） |
| 地圖圖磚（Tracestrack） | `tile.tracestrack.com` | 是（輔助） | 海外節點 | 可能 |
| Google 分析／字型 | `google-analytics`／`gstatic` | 否 | 海外節點 | 可能 |

行動郵局的**核心郵務、郵政商城與（未測試之）儲匯服務**走中華郵政自有的 `*.post.gov.tw`／`postmall.com.tw`，託管於多家台灣電信業者（HiNet、遠傳/亞太、台固、Seednet），皆在台灣境內，此部分資料保護符合期待。值得一提的是，App 的**使用行為遙測以自建的 `mfp.post.gov.tw/post/collect`（台灣境內）收集**，相較將行為外送第三方之作法，隱私上較佳；另仍有一份 Google 使用分析與網頁字型（海外）並存。「據點查詢」地圖底圖由 Tracestrack（Cloudflare）提供，屬非核心之第三方輔助。

> **隱私風險評估**：以可重現、可歸屬於行動郵局之流量而言，核心郵務、商城與遙測後端皆在台灣境內，風險低。複測確認查詢與遙測資料未出境；`mpost`／`mpush` 雖因憑證綁定未能解密，惟連線 IP 仍在台灣。海外流量僅剩地圖圖磚（Tracestrack）與 Google 分析／字型，屬常見之輔助流量。本次仍為未登入測試，登入與寄件、儲匯流程之個資傳輸內容，建議後續針對單一功能加測以完整評估。
