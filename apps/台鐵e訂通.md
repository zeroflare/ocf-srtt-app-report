---
title: 台鐵e訂通
---

# 台鐵e訂通 App 網路流量分析報告

## 概述

<img src="https://is1-ssl.mzstatic.com/image/thumb/Purple211/v4/75/4a/42/754a4216-58d8-1b63-9d9c-0828c812dc2f/AppIcon-0-0-1x_U007emarketing-0-8-0-85-220.png/512x512bb.jpg" alt="台鐵e訂通 App 圖示" width="150" height="150" style="border-radius: 22%; object-fit: cover; box-shadow: 0 4px 12px rgba(0, 0, 0, 0.18);">

本報告依據 SRTT 與封包擷取工具分析**台鐵e訂通**（`tw.gov.tra.twtraffic`）App 的網路請求。此 App 由**交通部臺灣鐵路（Taiwan Railways）**提供，功能包含火車時刻查詢、訂票、付款（Apple Pay／台灣Pay）、電子車票 QR Code 進出站、會員點數等。

此 App **核心網域組成單純**（訂票走臺灣鐵路自有網域／HiNet，付款整合台灣Pay），另有 Google 分析／廣告／推播等常見第三方；複測另見疑似 App 內廣告之 Trip.com／Coupang 等。涉及個資（訂票、身分、付款）為關注重點。

| 項目 | 內容 |
|------|------|
| App 名稱 | 台鐵e訂通 |
| App 版本 | 2.2.2 |
| 裝置 | iPhone 16 Pro / iOS 26.5.2 |
| 擷取時間 | 2026-07-22（初測）／**2026-07-30（複測，本報告以複測為準）** |
| 涉及網域 | 初測約 18 個；**複測觀察到 18 個主機**，其中新增 Trip.com／Coupang 等廣告／旅遊第三方 |

> **測試方式與歸屬說明**：**複測全程採 HTTPS MITM，惟 App 對後端採憑證綁定（cert pinning），本次所有連線僅以 CONNECT 出現、內容未解密**；因此本次主要用於**驗證網域與連線 IP 之歸屬與國家**，無法檢視 HTTP 內容。未實際完成訂票／付款。因 SRTT／MITM 為全裝置擷取，下列廣告／旅遊第三方（Trip.com、Coupang 等）雖與台鐵 App 之廣告版位相符，惟均為單次 CONNECT，**歸屬記為「疑似 App 內廣告」並標明待確認**。

---

## 網域分析

依**所屬單位／角色**分為五類，國家與雲端歸屬以本次 HAR 內**實際連線的 server IP** 為準。iOS 系統層背景網域已於文末另列並排除。

| 網域 | 所屬單位 | 國家 | 雲端 | ASN | Anycast | 主要用途 |
|------|------|------|------|------|------|------|
| `www.railway.gov.tw` | 臺灣鐵路 | 台灣 | 否（HiNet） | AS3462（中華電信 HiNet） | | 台鐵官方服務／訂票（本次 89 次，僅 CONNECT） |
| `tip-tr4cdn.cdn.hinet.net` | 臺灣鐵路（HiNet CDN） | 台灣 | 否（電信 CDN） | AS3462（中華電信 HiNet） | | 台鐵服務 CDN 前端 |
| `www.taiwanpay.com.tw` | 臺灣行動支付 TWMP（台灣Pay） | 台灣 | 是（Akamai） | AS20940（Akamai） | ✓ | 付款（台灣Pay，本次未觸發） |
| `www.google-analytics.com`／`www.googletagmanager.com`／`app-analytics-services.com` | Google | 台灣 | 是 | AS15169（Google） | ✓ | 使用行為分析 |
| `www.googleadservices.com` | Google | 台灣 | 是 | AS15169（Google） | ✓ | 廣告轉換（本次未觸發） |
| `firebase*`／`device-provisioning`／`play.googleapis.com`／`clients4.google.com` | Google（Firebase／FCM／Play） | 台灣 | 是 | AS15169（Google） | ✓ | 推播／Play 服務 |
| `fonts.gstatic.com`／`fonts.googleapis.com`／`ssl.gstatic.com` | Google | 台灣 | 是 | AS15169（Google） | ✓ | 網頁字型 |
| `pages.trip.com`／`tw.trip.com`／`*.tripcdn.com` | Trip.com（攜程／Ctrip） | 台灣 | 是（Akamai） | AS20940（Akamai） | ✓ | **疑似 App 內廣告／旅遊合作**（待確認） |
| `link.tw.coupang.com` | Coupang（韓國酷澎） | 台灣 | 是（Akamai） | AS20940（Akamai） | ✓ | **疑似 App 內廣告**（待確認） |
| `img1a.coupangcdn.com` | Coupang（韓國酷澎） | 台灣 | 是（CloudFront） | AS16509（AWS） | ✓ | **疑似 App 內廣告**（待確認） |
| `look.twword.com` | twword（Cloudflare 承載） | 台灣 | 是（Cloudflare） | AS13335（Cloudflare） | ✓ | **疑似 App 內廣告**（待確認） |

> **國家判定說明（以本次實際連線 IP 為準）**：台鐵核心（`www.railway.gov.tw` → `210.242.36.6`、`tip-tr4cdn.cdn.hinet.net` → `203.66.32.x`）為 **AS3462（中華電信 HiNet）**，台灣境內、非公有雲。付款 `www.taiwanpay.com.tw`（TWMP）本次未再連線。Google 分析／推播／字型（AS15169）採 Anycast，連線節點部分在台灣，營運商為 Google 海外，出境記「可能／是」。**廣告／旅遊第三方連線節點皆在台灣、且均為 Anycast**：Trip.com（`pages/tw.trip.com`、`*.tripcdn.com`）與 `link.tw.coupang.com` 經 **Akamai**（CNAME `*.edgekey.net`／`*.edgesuite.net`，邊緣 IP 落 HiNet `210.71.227.x`／`203.69.81.x`，約 5 ms）；`img1a.coupangcdn.com` 經 **CloudFront**（PTR **`tpe53`**，約 7 ms）；`look.twword.com` 經 **Cloudflare**（約 5 ms）。營運商仍為海外（攜程＝星／中、酷澎＝韓、Cloudflare 承載），資料出境與連線節點分開判斷。此三者為單次 CONNECT、內容未解密，研判為 App 內廣告版位所載，歸屬待確認。

> **國家／Anycast 判定（`mtr --tcp --port 443`）**：以 TCP/443 終點延遲與 PTR／ASN 判定連線節點國家；Cloudflare、Google、Meta、**Akamai**、CloudFront、GCP Global LB 等標 Anycast ✓。營運商為海外者，資料出境仍可能為「是／可能」，與連線節點國家分開判斷。

### 1. 台鐵核心服務（臺灣鐵路，台灣，非公有雲）

* **域名性質**：`www.railway.gov.tw`（台鐵官方服務／訂票）、`tip-tr4cdn.cdn.hinet.net`（HiNet CDN 前端）
* **地理位置**：`www.railway.gov.tw`（`210.242.36.6`，AS3462 HiNet）、`tip-tr4cdn.cdn.hinet.net`（`203.66.32.101`，AS3462 HiNet CDN），均台灣
* **基礎設施**：中華電信 HiNet 機房／CDN，非公有雲
* **角色**：時刻查詢、訂票、電子車票、會員等核心功能
* **資料特性**：訂票涉及乘車人身分、聯絡與付款資訊（本次未實際訂票，且全程僅 CONNECT、內容未解密）；核心服務主機在台灣境內。`www.railway.gov.tw` 為本次最主要之連線（89 次），確認持續連台灣 HiNet `210.242.36.6`
* **DNS／連線 IP**（本次實際連線）：

| 網域 | 連線 IP | ASN | 國家 |
|------|---------|-----|------|
| www.railway.gov.tw | 210.242.36.6 | AS3462（中華電信 HiNet） | 台灣 |
| tip-tr4cdn.cdn.hinet.net | 203.66.32.5／.105／.194 | AS3462（中華電信 HiNet） | 台灣 |

### 2. 付款整合（台灣Pay，台灣）

* **域名性質**：`www.taiwanpay.com.tw` 為臺灣行動支付（TWMP）之台灣Pay，供 App 內付款
* **地理位置（mtr 443）**：營運商 TWMP 位於台灣；`www.taiwanpay.com.tw` 經 **Akamai（AS20940，`*.edgekey.net`）**，邊緣 IP 落 HiNet（約 **6 ms**）→ **台灣 Anycast**
* **角色**：訂票付款（App 另支援 Apple Pay）
* **資料特性**：付款流程；TWMP 支付核心在台灣（詳見台灣Pay 專篇報告）
* **DNS／mtr 結果**：

| 網域 | 連線 IP | ASN | 國家 | Anycast |
|------|---------|-----|------|---------|
| www.taiwanpay.com.tw | 61.220.62.226／203.69.81.136 | AS20940（Akamai）→ HiNet | 台灣 | ✓ |

### 3. Google 分析／廣告／推播／字型（海外雲端）

* **域名性質**：分析（`www.google-analytics.com`、`www.googletagmanager.com`、`app-analytics-services.com`）、廣告（`www.googleadservices.com`）、Firebase／FCM 推播（`firebaseinstallations`、`firebaselogging-pa`、`device-provisioning.googleapis.com`）、Play 服務（`play.googleapis.com`、`clients4.google.com`）、字型（`fonts.gstatic.com` 等）
* **地理位置**：ASN **AS15169（Google LLC）**，Anycast
* **角色**：使用分析、廣告轉換追蹤、推播通知、網頁字型
* **資料特性**：傳送使用事件、廣告識別、推播 Token 與裝置資訊；營運商為 Google 海外
* **DNS 解析結果**（代表值）：

| 網域 | 解析 IP | ASN | 國家 |
|------|---------|-----|------|
| www.google-analytics.com | 142.250.66.78 | AS15169（Google） | 台灣（節點） |
| www.googleadservices.com | 142.250.204.34 | AS15169（Google） | 台灣（Anycast） |
| firebaselogging-pa.googleapis.com | 172.217.117.4 | AS15169（Google） | 台灣（Anycast） |

### 4. 廣告／旅遊第三方（疑似 App 內廣告，待確認）

* **域名性質**：`pages.trip.com`／`tw.trip.com`／`dimg04.tripcdn.com`／`ak-s-cw.tripcdn.com`／`ak-d.tripcdn.com`（Trip.com 攜程旅遊）、`link.tw.coupang.com`／`img1a.coupangcdn.com`（Coupang 酷澎電商）、`look.twword.com`
* **地理位置（mtr 443）**：下列網域**連線節點皆在台灣，且皆為 Anycast**——Trip.com 與 `link.tw.coupang.com` 經 **Akamai（AS20940）**（CNAME `*.edgekey.net`／`*.edgesuite.net`，邊緣 IP 落 HiNet，約 5 ms）；`img1a.coupangcdn.com` 經 **AWS CloudFront**（PTR **`tpe53`**，約 7 ms）；`look.twword.com` 經 **Cloudflare（AS13335）**（約 5 ms）
* **角色**：均為單次 CONNECT，研判為 App 首頁／查詢頁之**廣告版位或旅遊合作連結**（台鐵 App 設有廣告與訂房入口）
* **資料特性**：本次未解密，無法確認實際傳輸內容；**營運商為海外業者**（攜程＝星／中、酷澎＝韓），即使連線落於台灣 Anycast 邊緣，資料仍由海外業者處理
* **歸屬說明**：因全裝置擷取且為單次連線，不排除來自其他來源；本報告記為「疑似 App 內廣告」並標明待確認，建議後續於乾淨環境複測佐證
* **DNS／mtr 結果**（代表值）：

| 網域 | 連線 IP | CDN／ASN | 國家 | Anycast |
|------|---------|----------|------|---------|
| pages.trip.com／tw.trip.com／dimg04.tripcdn.com | 210.71.227.209 | Akamai（`*.edgekey.net`）→ HiNet | 台灣 | ✓ |
| ak-s-cw.tripcdn.com／ak-d.tripcdn.com | 210.71.227.138 | Akamai（`*.edgesuite.net`）→ HiNet | 台灣 | ✓ |
| link.tw.coupang.com | 203.69.81.137 | Akamai（`*.edgekey.net`）→ HiNet | 台灣 | ✓ |
| img1a.coupangcdn.com | 3.169.121.56（CloudFront `tpe53`） | AS16509（AWS CloudFront） | 台灣 | ✓ |
| look.twword.com | 104.21.1.176 | AS13335（Cloudflare） | 台灣 | ✓ |

### 5. iOS 系統背景網域（非 App 業務流量，已排除）

下列為 iOS 系統背景流量，與台鐵e訂通功能無關，於分析中排除：`stocks-data-service.apple.com`、`gateway.fe2.apple-dns.net`、`get-bx.g.aaplimg.com`、`gspe19-2-ssl.ls.apple.com`、`a1556.dscapi9.akamai.net`（Apple／Akamai）。

---

## API 用途整理

> **說明**：複測全程僅 CONNECT、內容未解密（憑證綁定），此節依「網域／功能角色」整理，無法列出端點與參數。實際訂票／付款流程未於本次觸發。

### 一、訂票與查詢（`www.railway.gov.tw`、`tip-tr4cdn.cdn.hinet.net`）

時刻查詢、訂票、電子車票、會員等核心功能向台鐵 `www.railway.gov.tw` 取得，靜態資源經 HiNet CDN（`tip-tr4cdn`）。台灣境內。

### 二、付款（`www.taiwanpay.com.tw`、Apple Pay）

付款整合台灣Pay（TWMP，台灣），另支援 Apple Pay。

### 三、分析、廣告與推播（Google）

App 嵌入 Google Analytics／Tag Manager、Firebase／FCM／Play 服務，傳送使用行為與裝置資訊至 Google 海外（本次 `googleadservices` 廣告轉換未再觸發）。

### 四、廣告／旅遊第三方（Trip.com、Coupang，疑似 App 內廣告）

複測另見 Trip.com（攜程旅遊）與 Coupang（酷澎）之網域，研判為 App 廣告版位／旅遊合作入口所載；連線節點皆在**台灣 Anycast**（Akamai／CloudFront／Cloudflare），惟營運商為海外業者。因單次連線且未解密，歸屬待確認。

---

## 請求流程概觀

```
App 啟動 / 首頁
  ├─→ 台鐵服務 / 訂票 (www.railway.gov.tw ← HiNet 台灣)
  ├─→ 靜態資源 CDN (tip-tr4cdn.cdn.hinet.net ← HiNet)
  ├─→ Google 分析 / Tag Manager (google-analytics, googletagmanager)
  ├─→ Firebase / FCM 推播 (firebaseinstallations, device-provisioning)
  └─→ 字型 (fonts.gstatic.com)

訂票 → 付款（本次未觸發）
  ├─ 台灣Pay → (www.taiwanpay.com.tw ← Akamai 台灣 Anycast／TWMP)
  └─ Apple Pay（系統）

首頁／查詢頁廣告版位（疑似，單次 CONNECT 未解密）
  ├─ Trip.com 旅遊 → (pages/tw.trip.com, *.tripcdn.com ← Akamai 台灣 Anycast／海外營運)
  ├─ Coupang 酷澎 → (link.tw.coupang.com ← Akamai；img1a ← CloudFront tpe53／海外營運)
  └─ twword → (look.twword.com ← Cloudflare 台灣 Anycast)

※ 本次全程僅 CONNECT、內容未解密（憑證綁定）
```

---

## 摘要

| 分類 | 網域 | 是否核心功能 | 連線節點 | 資料是否出境 |
|------|------|--------------|----------|--------------|
| 台鐵核心服務 | `www.railway.gov.tw`、`tip-tr4cdn.cdn.hinet.net` | 是 | 台灣（HiNet） | 否 |
| 付款（台灣Pay，本次未觸發） | `www.taiwanpay.com.tw` | 是 | 台灣（Akamai Anycast／TWMP） | 否（營運商台灣） |
| Google 分析／推播／字型 | `google-analytics`／`firebase`／`gstatic` 等 | 否 | 台灣（Google Anycast） | 可能／是 |
| 廣告／旅遊第三方（疑似，待確認） | Trip.com、Coupang、`look.twword.com` | 否 | 台灣（Anycast）／海外營運 | 可能（海外業者處理） |

台鐵e訂通的**核心訂票與查詢功能**走臺灣鐵路自有之 `www.railway.gov.tw`（中華電信 HiNet，台灣境內），付款整合台灣Pay（TWMP，台灣營運）與 Apple Pay，**核心與付款資料均在台灣境內**，此部分符合期待。惟複測（全程未解密、僅驗證域名／IP）另發現 App 除 Google 分析／推播外，尚有 **Trip.com（攜程旅遊）與 Coupang（酷澎）等海外業者之廣告／旅遊版位**——連線節點皆為台灣 Anycast（Akamai／CloudFront／Cloudflare），資料仍由海外業者處理，較「僅核心＋Google 第三方」之樣貌更複雜。

> **隱私風險評估**：核心訂票與付款在台灣境內，風險低。第三方除 Google 之分析／推播外，複測另見疑似 App 內廣告之 Trip.com、Coupang 等海外業者版位，會將曝光／點擊等資訊交由海外業者處理，對公營 App 而言其廣告版位之第三方組成值得檢視。**本次全程採憑證綁定、內容未解密，僅能驗證域名與 IP 歸屬**；上述廣告第三方為單次連線、歸屬待確認。實際訂票／付款之個資傳輸內容，建議於乾淨環境並搭配可解密之方式加測以完整評估。
