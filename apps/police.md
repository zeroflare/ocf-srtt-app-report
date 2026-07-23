---
title: 警政服務
---

# 警政服務 App 網路流量分析報告

## 概述

<img src="https://is1-ssl.mzstatic.com/image/thumb/Purple211/v4/4c/56/a5/4c56a589-dcff-9dc1-746a-9403596abd11/AppIcon-0-0-1x_U007emarketing-0-8-0-85-220.png/512x512bb.jpg" alt="警政服務 App 圖示" width="150" height="150" style="border-radius: 22%; object-fit: cover; box-shadow: 0 4px 12px rgba(0, 0, 0, 0.18);">

本報告依據 SRTT 與分包截取工具分析**警政服務**（`tw.gov.npa.110APP`）App 的網路請求。此 App 由內政部警政署提供，是一款**入口／整合型（super app）**應用，集合報案（110／165／113）、防詐（165 打詐儀錶板、可疑訊息分析）、交通（即時路況、測速執法點、違規查詢）、查詢（失竊車輛、失蹤人口、通緝犯、刑事紀錄證明）、防空避難、收聽警廣、呼叫計程車等三十餘項功能。

多數功能以 **WebView 載入警政署各子系統或外部網站**，少部分為**撥打電話**（如 110、165、113，不產生網路流量）或**外連 Facebook**（NPA署長室）。

| 項目 | 內容 |
|------|------|
| App 名稱 | 警政服務 |
| App 版本 | 8.9.1 |
| 裝置 | iPhone 16 Pro / iOS 26.5.2 |
| 擷取時間 | 2026-07-22 |
| 涉及網域 | 36 個（App 相關；另有 4 個 iOS 系統背景網域已排除） |

> **測試方式與資料範圍說明**：本次為「各功能點一輪」之瀏覽測試，未實際執行報案、簽章等寫入操作，故觀察到的請求以 **GET／WebView 頁面載入**為主。本報告以**網域與資料流向**為分析主軸；HTTP 端點層級（method／path／請求參數／個資欄位）之細節不在本次擷取範圍內，留待後續針對特定功能加測。

---

## 網域分析

警政服務為整合型 App，涉及網域較多，以下依**所屬單位／角色**分為七類彙整。iOS 系統層背景網域（App Store、iCloud、定位服務）非 App 業務流量，已於文末另列並排除。

| 網域 | 所屬單位 | 國家 | 雲端 | ASN | 主要用途 |
|------|----------|------|------|-----|----------|
| `www.npa.gov.tw` | 內政部警政署 | 台灣 | 否（HiNet CDN） | AS3462 | 警政署官網、各功能 WebView 內容頁 |
| `www-npa.cdn.hinet.net` | 中華電信 HiNet（承載警政署） | 台灣 | 否（電信 CDN） | AS3462 | 警政署官網之 CDN 前端 |
| `www.apb.npa.gov.tw` | 警政署刑事警察局 | 台灣 | 否（HiNet CDN） | AS3462 | 刑事局相關查詢／內容頁 |
| `165.npa.gov.tw` | 刑事警察局 165 | 台灣 | 否（HiNet CDN） | AS3462 | 165 反詐騙服務頁 |
| `nv2.npa.gov.tw` | 警政署（GSN） | 台灣 | 否（政府網路） | AS4782 / AS9919 | 警政後端服務 |
| `eze8.npa.gov.tw` | 警政署（GSN） | 台灣 | 否（政府網路） | AS4782 / AS9919 | 警政後端服務 |
| `ps.npa.gov.tw` | 警政署（GSN） | 台灣 | 否（政府網路） | AS4782 | 警政後端服務 |
| `tm2.npa.gov.tw` | 警政署（新世紀資通） | 台灣 | 否（政府網路） | AS9919 | 警政後端服務 |
| `op2.npa.gov.tw` | 警政署（新世紀資通） | 台灣 | 否（政府網路） | AS9919 | 警政後端服務 |
| `eli.npa.gov.tw` | 警政署（新世紀資通） | 台灣 | 否（政府網路） | AS9919 | 警政後端服務 |
| `wmts.nlsc.gov.tw` | 內政部國土測繪中心（國網中心） | 台灣 | 否（政府機房） | AS7539 | 地圖圖磚（路況／測速點／避難地圖） |
| `rtr.pbs.gov.tw` | 警察廣播電臺（GSN） | 台灣 | 否（政府網路） | AS4782 | 收聽警廣（線上串流） |
| `165dashboard.tw` | 刑事局 165 打詐儀錶板（委外） | **日本** | **是（AWS）** | AS16509 | 打詐儀錶板前端 |
| `api-next.no8.io` | 打詐儀錶板 API（委外 NO8） | **日本** | **是（AWS）** | AS16509 | 打詐儀錶板 API |
| `assets.no8.io` | 打詐儀錶板靜態資源（委外 NO8） | 台灣 | 是（AWS CloudFront） | AS16509 | 靜態資源 |
| `live-chat-console.no8.io` | 打詐儀錶板線上客服（委外 NO8） | 台灣 | 是（AWS CloudFront） | AS16509 | 線上客服／聊天 |
| `www.facebook.com` 等 Meta 網域 | Meta | 台灣（節點） | 是 | AS32934 | NPA署長室外連、FB SDK |
| `scontent.ftpe7-*.fbcdn.net` | Meta（FB CDN） | 台灣（節點） | 是 | AS32934 | FB 圖片／靜態內容 |
| `cse.google.com` 等 Google 搜尋 | Google | 台灣（節點） | 是 | AS15169 | 站內搜尋（Custom Search） |
| `pagead2.googlesyndication.com` 等 | Google | 台灣（節點） | 是 | AS15169 | 廣告（AdSense/DoubleClick） |
| `www.google-analytics.com` 等 | Google | 台灣（節點） | 是 | AS15169 | 使用行為分析（GA/GTM） |
| `fonts.googleapis.com`／`fonts.gstatic.com` | Google | 台灣（節點） | 是 | AS15169 | 網頁字型 |
| `i.ytimg.com`／`googlevideo.com` | Google（YouTube） | 台灣（節點） | 是 | AS15169 | YouTube 影片縮圖／串流 |
| `unpkg.com` | Cloudflare | 台灣（節點） | 是 | AS13335 | 前端 JS 函式庫 CDN |

> **國家判定說明**：`.npa.gov.tw` 之 GSN 子網域（`nv2`、`eze8`、`ps` 等，AS4782／AS9919）與 `wmts.nlsc.gov.tw`（AS7539 國網中心）為政府實體機房，WHOIS／ASN 註冊國均為台灣，資料留在境內。`www.npa.gov.tw`、`www.apb.npa.gov.tw`、`165.npa.gov.tw` 由**中華電信 HiNet CDN（AS3462，`www-npa.cdn.hinet.net`）**承載，仍屬台灣電信基礎設施。Google、Meta、Cloudflare 採 Anycast，連線節點判定位於台灣，但**營運商為海外雲端**，資料出境記「可能」。**165 打詐儀錶板（`165dashboard.tw`、`api-next.no8.io`）解析至 AWS 東京（日本）節點，為本次唯一資料實際落於境外之服務。**

### 1. 警政署核心後端（內政部警政署，台灣，非公有雲）

* **域名性質**：`.gov.tw` / `.npa.gov.tw` 政府域名，為警政署官網與各業務子系統端點
* **地理位置**：`www.npa.gov.tw`、`www.apb.npa.gov.tw`、`165.npa.gov.tw` 解析至 HiNet CDN（AS3462，`203.66.32.x`，中華電信，台北）；`nv2`／`eze8`／`ps.npa.gov.tw` 解析至 `122.146.27.x`／`210.69.154.x`，屬 **GSN（政府服務網路，AS4782）**與**新世紀資通（AS9919）**
* **基礎設施**：非公有雲；官網類頁面透過中華電信 HiNet CDN 加速，後端業務服務位於政府網路
* **角色**：App 核心後端，承載各功能之 WebView 內容頁與查詢服務（報案說明、通緝／失蹤／失竊查詢、刑事紀錄證明、交通違規等）
* **資料特性**：本次以瀏覽為主，觀察到者多為頁面載入（GET）；實際查詢／報案若送出，將包含使用者輸入之個資，惟均留在台灣政府網路範圍內
* **DNS 解析結果**（A Record，代表值）：

| 網域 | 類型 | 解析 IP | ASN | 國家 |
|------|------|---------|-----|------|
| www.npa.gov.tw | A | 203.66.32.109 | AS3462 | TW |
| www.apb.npa.gov.tw | A | 203.66.32.102 | AS3462 | TW |
| 165.npa.gov.tw | A | 203.66.32.107 | AS3462 | TW |
| nv2.npa.gov.tw | A | 122.146.27.93 / 210.69.154.93 | AS4782 / AS9919 | TW |
| eze8.npa.gov.tw | A | 122.146.27.116 | AS4782 / AS9919 | TW |
| ps.npa.gov.tw | A | 210.69.154.38 | AS4782 | TW |
| tm2.npa.gov.tw | A | 122.146.27.67 | AS9919 | TW |
| op2.npa.gov.tw | A | 122.146.27.88 | AS9919 | TW |
| eli.npa.gov.tw | A | 122.146.27.103 | AS9919 | TW |

AS3462 為中華電信 HiNet；AS4782 為 GSN（政府服務網路，Data Communication Business Group）；AS9919 為新世紀資通（New Century InfoComm，Seednet）。`tm2`／`op2`／`eli.npa.gov.tw` 經 traceroute 確認解析至 `122.146.27.x`，ASN 為 **AS9919（新世紀資通）**，均在台灣。

### 2. 地圖與警廣（其他政府單位，台灣，非公有雲）

* **域名性質**：`wmts.nlsc.gov.tw` 為內政部國土測繪中心 WMTS 地圖圖磚服務；`rtr.pbs.gov.tw` 為警察廣播電臺串流
* **地理位置**：`wmts.nlsc.gov.tw` 解析至 `140.110.x.x`，ASN **AS7539（國家高速網路與計算中心，NCHC）**，台灣；`rtr.pbs.gov.tw` 經 traceroute 確認解析至 `117.56.47.51`，ASN **AS4782（GSN，Data Communication Business Group）**，台灣
* **基礎設施**：政府自建／學研機房，非公有雲
* **角色**：地圖圖磚供「即時路況查詢」「測速執法點查詢」「防空避難」等地圖功能載入底圖；警廣串流供「收聽警廣」
* **資料特性**：圖磚與音訊串流，不涉個資
* **DNS 解析結果**（A Record）：

| 網域 | 類型 | 解析 IP | ASN | 國家 |
|------|------|---------|-----|------|
| wmts.nlsc.gov.tw | A | 140.110.134.19 | AS7539 | TW |
| rtr.pbs.gov.tw | A | 117.56.47.51 | AS4782 | TW |

AS7539 為 National Center for High-performance Computing（國網中心）；AS4782 為 GSN（政府服務網路）。

### 3. 165 打詐儀錶板（刑事局委外，AWS 雲端，部分節點日本）⚠️

* **域名性質**：`165dashboard.tw` 為 165 打詐儀錶板獨立網站（2024/12 上線）；`*.no8.io` 為其技術服務商（NO8）之 API、靜態資源與線上客服網域
* **地理位置**：`165dashboard.tw`（`13.192.191.59`）與 `api-next.no8.io`（`13.115.161.183`、`35.78.34.140`）解析至 **AWS 東京區域（`ap-northeast-1`，日本）**；`assets.no8.io`、`live-chat-console.no8.io` 走 AWS CloudFront 台灣邊緣節點
* **基礎設施**：**Amazon AWS 公有雲（AS16509）**
* **角色**：App 內「打詐儀錶板」「可疑訊息分析」等防詐資訊頁；`live-chat-console.no8.io` 提供線上客服／聊天
* **資料特性**：以公開防詐統計資訊之呈現為主；惟前端與 API 主機實體位於**日本東京**，若使用者於此輸入查詢或客服對話，**資料將實際傳輸至境外（日本）**——此為本報告最需注意之出境點
* **DNS 解析結果**（A Record）：

| 網域 | 類型 | 解析 IP | ASN | 國家 |
|------|------|---------|-----|------|
| 165dashboard.tw | A | 13.192.191.59 / 54.250.155.180 | AS16509 | JP |
| api-next.no8.io | A | 13.115.161.183 / 35.78.34.140 | AS16509 | JP |
| assets.no8.io | A | 65.9.180.10 | AS16509 | TW |
| live-chat-console.no8.io | A | 54.192.248.114 | AS16509 | TW |

AS16509 為 Amazon.com, Inc.（AWS）。

### 4. Facebook / Meta（NPA署長室外連與 FB SDK，海外）

* **域名性質**：`www/api/graph/web/gateway/edge-mqtt.facebook.com` 為 Meta 服務端點；`scontent.ftpe7-*.fna.fbcdn.net`、`static.*.fbcdn.net` 為 FB 內容 CDN；`*-netseer-ipaddr-assoc.*.fbcdn.net` 為 Meta 網路測量
* **地理位置**：解析至 `31.13.87.x`，ASN **AS32934（Facebook）**，連線節點判定位於**台灣（TPE 邊緣）**
* **基礎設施**：Meta 公有雲
* **角色**：「NPA署長室」功能外連 Facebook 粉專；App 內嵌 FB SDK（登入／分享／內容嵌入）
* **資料特性**：傳送 App 識別、裝置資訊與 FB 互動資料；營運商為 Meta 海外
* **DNS 解析結果**（A Record）：

| 網域 | 類型 | 解析 IP | ASN | 國家 |
|------|------|---------|-----|------|
| www.facebook.com | A | 31.13.87.36 | AS32934 | TW |
| api.facebook.com | A | 31.13.87.1 | AS32934 | TW |

AS32934 為 Facebook, Inc.（Meta）。

### 5. Google 服務（搜尋／廣告／分析／字型／YouTube，海外雲端）

* **域名性質**：涵蓋站內搜尋（`cse.google.com`、`syndicatedsearch.goog`、`www.adsensecustomsearchads.com`、`clients1.google.com`）、廣告（`pagead2.googlesyndication.com`、`ad.doubleclick.net`、`ep1/ep2.adtrafficquality.google`）、分析（`www.google-analytics.com`、`ssl.google-analytics.com`、`analytics.google.com`、`www.googletagmanager.com`、`app-analytics-services.com`）、網頁字型（`fonts.googleapis.com`、`fonts.gstatic.com`）、YouTube（`i.ytimg.com`、`*.googlevideo.com`）
* **地理位置**：ASN **AS15169（Google LLC）**，Anycast，連線節點判定多位於**台灣**
* **基礎設施**：Google 公有雲
* **角色**：WebView 頁面所嵌之 Google 站內搜尋、AdSense 廣告、Google Analytics／Tag Manager 追蹤、字型與 YouTube 影片；**均為第三方輔助，非警政核心業務**
* **資料特性**：傳送頁面瀏覽事件、廣告識別、裝置資訊等；營運商為 Google 海外
* **DNS 解析結果**（A Record，代表值）：

| 網域 | 類型 | 解析 IP | ASN | 國家 |
|------|------|---------|-----|------|
| cse.google.com | A | 64.233.187.100 | AS15169 | TW |
| www.google-analytics.com | A | 142.250.77.14 | AS15169 | TW |
| i.ytimg.com | A | 108.177.125.119 | AS15169 | TW |

AS15169 為 Google LLC。

### 6. 其他 CDN（Cloudflare／Akamai，海外雲端）

* **域名性質**：`unpkg.com` 為 npm 前端函式庫 CDN（Cloudflare，AS13335）；`e673.dsce9.akamaiedge.net` 為 Akamai CDN
* **角色**：WebView 頁面載入前端 JavaScript／CSS 函式庫等靜態資源
* **資料特性**：純靜態資源，不涉個資；營運商為海外雲端
* **DNS 解析結果**（A Record）：

| 網域 | 類型 | 解析 IP | ASN | 國家 |
|------|------|---------|-----|------|
| unpkg.com | A | 104.18.0.22 | AS13335 | TW |

AS13335 為 Cloudflare, Inc.。

### 7. iOS 系統背景網域（非 App 業務流量，已排除）

下列網域為 iOS 系統／App Store／iCloud 之背景流量，與警政服務 App 功能無關，於分析中排除，僅列出供辨識：`gsp-ssl.ls.apple.com`（定位服務）、`gateway.icloud.com`（iCloud）、`itunes.apple.com`、`amp-api.apps.apple.com`（App Store）。

---

## API 用途整理

> **說明**：本次為瀏覽式測試，未執行實際報案／查詢送出，觀察到之請求以 **GET／WebView 頁面載入**為主，故此節依「網域／功能角色」整理，不列具體端點與請求參數。端點層級（method／path／個資欄位／加密方式）之細節，留待後續針對特定功能（如線上報案、刑事紀錄證明申請）加測時補充。

### 一、警政署核心服務（`*.npa.gov.tw`）

App 各功能透過 WebView 載入警政署官網（`www.npa.gov.tw`）與業務子系統（`www.apb.npa.gov.tw`、`nv2`／`eze8`／`ps`／`tm2`／`op2`／`eli.npa.gov.tw`）之頁面。對應功能涵蓋：受理案件查詢、通緝犯查詢平臺、失蹤人口查詢、失竊車輛查詢、警察刑事紀錄證明書、交通違規（違規拖吊、車損事故、交通事故資料申請）、拾得遺失物、警察法規等。此類請求走台灣政府網路／HiNet CDN，資料不出境。

### 二、防詐服務（`165.npa.gov.tw`、`165dashboard.tw`、`*.no8.io`）

「165 反詐騙」相關功能：`165.npa.gov.tw`（台灣，HiNet）承載反詐騙 QA、可疑訊息分析等；「打詐儀錶板」則載入 `165dashboard.tw`（AWS 東京）與 `api-next.no8.io`（AWS 東京）之前端與 API，並可能透過 `live-chat-console.no8.io` 提供線上客服。**此類流量部分實際落於日本境外。**

### 三、地圖與廣播（`wmts.nlsc.gov.tw`、`rtr.pbs.gov.tw`）

即時路況查詢、測速執法點查詢、防空避難等地圖功能，載入國土測繪中心 WMTS 圖磚（`wmts.nlsc.gov.tw`，台灣）；「收聽警廣」載入警察廣播電臺串流（`rtr.pbs.gov.tw`，台灣）。

### 四、第三方嵌入（Facebook、Google、Cloudflare）

「NPA署長室」外連 Facebook 粉專並載入 FB SDK；WebView 頁面另嵌入 Google 站內搜尋、AdSense 廣告、Google Analytics／Tag Manager 追蹤、Google 字型、YouTube 影片，以及 `unpkg.com` 前端函式庫。此類均為第三方輔助服務，非警政核心業務，營運商為海外雲端。

---

## 請求流程概觀

```
App 啟動 / 進入首頁
  ├─→ 載入官網內容與功能清單          (www.npa.gov.tw ← HiNet CDN)
  ├─→ Google Analytics / Tag Manager  (www.google-analytics.com, googletagmanager)
  └─→ 網頁字型 / 前端函式庫            (fonts.gstatic.com, unpkg.com)

使用者點選功能（多為 WebView）
  ├─ 報案 / 查詢類 → 警政署子系統       (*.npa.gov.tw：apb, nv2, eze8, ps, ...)
  ├─ 165 打詐儀錶板 / 可疑訊息分析 → AWS 東京 ⚠️  (165dashboard.tw, api-next.no8.io)
  │        └─ 線上客服                  (live-chat-console.no8.io)
  ├─ 即時路況 / 測速點 / 防空避難 → 地圖圖磚 (wmts.nlsc.gov.tw)
  ├─ 收聽警廣 → 串流                    (rtr.pbs.gov.tw)
  └─ NPA署長室 → 外連 Facebook          (www.facebook.com, fbcdn.net)

功能頁內嵌第三方
  ├─→ 站內搜尋 (Google CSE)            (cse.google.com, syndicatedsearch.goog)
  ├─→ 廣告 (AdSense/DoubleClick)        (pagead2.googlesyndication.com, doubleclick)
  └─→ YouTube 影片縮圖 / 串流           (i.ytimg.com, googlevideo.com)

（撥打電話類：110 電話報案、165 反詐騙專線、113 保護專線 → 直接撥號，不產生網路流量）
```

---

## 摘要

| 分類 | 網域 | 是否核心功能 | 連線節點 | 資料是否出境 |
|------|------|--------------|----------|--------------|
| 警政署核心後端 | `*.npa.gov.tw`（HiNet CDN + GSN） | 是 | 台灣 | 否 |
| 地圖／警廣 | `wmts.nlsc.gov.tw`、`rtr.pbs.gov.tw` | 是（輔助） | 台灣 | 否 |
| 165 打詐儀錶板 | `165dashboard.tw`、`api-next.no8.io` | 是（輔助） | **日本（AWS 東京）** | **是** |
| 打詐儀錶板靜態／客服 | `assets.no8.io`、`live-chat-console.no8.io` | 否 | 台灣（AWS CloudFront） | 可能（AWS 海外營運） |
| Facebook / Meta | `*.facebook.com`、`fbcdn.net` | 否 | 台灣（Meta 節點） | 可能（Meta 海外營運） |
| Google 搜尋／廣告／分析 | `google.com`、`googlesyndication`、`google-analytics` 等 | 否 | 台灣（Google Anycast） | 可能（Google 海外營運） |
| 前端 CDN | `unpkg.com`（Cloudflare） | 否 | 台灣（節點） | 可能（Cloudflare 海外營運） |

警政服務 App 的**核心報案與查詢功能**主要以 WebView 載入警政署 `*.npa.gov.tw` 各子系統，走中華電信 HiNet CDN 與 GSN 政府網路，資料留在台灣境內。地圖（國土測繪中心）與警廣亦為台灣政府機房。

值得注意的是，**「165 打詐儀錶板」（`165dashboard.tw`、`api-next.no8.io`）之前端與 API 主機實際位於 AWS 東京（日本）**，是本次觀察到唯一資料明確落於境外之服務；其靜態資源與線上客服（`*.no8.io`）雖走 AWS 台灣邊緣節點，營運商仍為海外雲端。此外，App 內多個 WebView 頁面嵌入 **Facebook、Google（搜尋／廣告／分析／字型／YouTube）、Cloudflare** 等第三方服務，連線節點雖多在台灣 Anycast／邊緣，營運商均為海外，會傳送瀏覽行為、廣告識別與裝置資訊，屬非核心之第三方流量。

> **隱私風險評估**：核心政府服務資料留在境內，風險低。惟需留意：(1)「打詐儀錶板」實際連線日本東京節點，涉境外傳輸；(2) 官方 App 內嵌大量 Google 廣告／分析與 Facebook SDK，會將使用者瀏覽行為傳送至海外第三方，對政府服務而言之必要性值得檢視。本次為瀏覽式測試，實際報案／查詢送出時之個資傳輸內容與加密方式，建議後續針對單一功能加測 HTTP 明細以完整評估。
