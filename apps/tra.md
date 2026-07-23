---
title: 台鐵e訂通
---

# 台鐵e訂通 App 網路流量分析報告

## 概述

<img src="https://is1-ssl.mzstatic.com/image/thumb/Purple211/v4/75/4a/42/754a4216-58d8-1b63-9d9c-0828c812dc2f/AppIcon-0-0-1x_U007emarketing-0-8-0-85-220.png/512x512bb.jpg" alt="台鐵e訂通 App 圖示" width="150" height="150" style="border-radius: 22%; object-fit: cover; box-shadow: 0 4px 12px rgba(0, 0, 0, 0.18);">

本報告依據 SRTT 與分包截取工具分析**台鐵e訂通**（`tw.gov.tra.twtraffic`）App 的網路請求。此 App 由**交通部臺灣鐵路（Taiwan Railways）**提供，功能包含火車時刻查詢、訂票、付款（Apple Pay／台灣Pay）、電子車票 QR Code 進出站、會員點數等。

此 App **網域組成單純**：核心訂票走臺灣鐵路自有網域（台灣 HiNet），付款整合台灣Pay，另有 Google 分析／廣告／推播等常見第三方。涉及個資（訂票、身分、付款）為關注重點。

| 項目 | 內容 |
|------|------|
| App 名稱 | 台鐵e訂通 |
| App 版本 | 2.2.2 |
| 裝置 | iPhone 16 Pro / iOS 26.5.2 |
| 擷取時間 | 2026-07-22 |
| 涉及網域 | 約 18 個（App 相關；另有 5 個 iOS 系統背景已排除） |

> **測試方式與歸屬說明**：本次為功能點選瀏覽之測試，未實際完成訂票／付款。本報告以**網域與資料流向**為分析主軸；HTTP 端點層級之細節不在本次擷取範圍內。SRTT 未擷取到之 Stream 網域，已另以 DNS／Cymru 補齊 IP 與 ASN。

---

## 網域分析

依**所屬單位／角色**分為四類。iOS 系統層背景網域已於文末另列並排除。

| 網域 | 所屬單位 | 國家 | 雲端 | ASN | 主要用途 |
|------|----------|------|------|-----|----------|
| `www.railway.gov.tw` | 臺灣鐵路 | 台灣 | 否（HiNet） | AS3462 | 台鐵官方服務／訂票 |
| `tip-tr4cdn.cdn.hinet.net` | 臺灣鐵路（HiNet CDN） | 台灣 | 否（電信 CDN） | AS3462 | 台鐵服務 CDN 前端 |
| `www.taiwanpay.com.tw` | 臺灣行動支付 TWMP（台灣Pay） | 台灣 | 部分（Akamai） | AS3462 / AS20940 | 付款（台灣Pay） |
| `www.google-analytics.com`／`www.googletagmanager.com` | Google | 台灣（節點） | 是 | AS15169 | 使用行為分析 |
| `www.googleadservices.com` | Google | 美國 | 是 | AS15169 | 廣告轉換 |
| `firebaseinstallations`／`firebaselogging-pa.googleapis.com`／`device-provisioning.googleapis.com` | Google（Firebase／FCM） | 美國 | 是 | AS15169 | 推播與 Firebase |
| `app-analytics-services.com`／`play.googleapis.com`／`clients4.google.com` | Google | 台灣／美國 | 是 | AS15169 | 分析／Play 服務 |
| `fonts.gstatic.com`／`fonts.googleapis.com`／`ssl.gstatic.com` | Google | 台灣（節點） | 是 | AS15169 | 網頁字型 |

> **國家判定說明**：台鐵核心（`www.railway.gov.tw`、`tip-tr4cdn.cdn.hinet.net`）解析至 **AS3462（中華電信 HiNet）**，台灣境內、非公有雲。付款之 `www.taiwanpay.com.tw`（臺灣行動支付 TWMP）營運商在台灣，本次解析經 Akamai（AS20940）節點。Google 分析／廣告／推播（AS15169）採 Anycast，連線節點部分在台灣，營運商為 Google 海外，出境記「可能／是」。

### 1. 台鐵核心服務（臺灣鐵路，台灣，非公有雲）

* **域名性質**：`www.railway.gov.tw`（台鐵官方服務／訂票）、`tip-tr4cdn.cdn.hinet.net`（HiNet CDN 前端）
* **地理位置**：`www.railway.gov.tw`（`210.242.36.6`，AS3462 HiNet）、`tip-tr4cdn.cdn.hinet.net`（`203.66.32.101`，AS3462 HiNet CDN），均台灣
* **基礎設施**：中華電信 HiNet 機房／CDN，非公有雲
* **角色**：時刻查詢、訂票、電子車票、會員等核心功能
* **資料特性**：訂票涉及乘車人身分、聯絡與付款資訊（本次未實際訂票）；核心服務主機在台灣境內
* **DNS 解析結果**：

| 網域 | 解析 IP | ASN | 國家 |
|------|---------|-----|------|
| www.railway.gov.tw | 210.242.36.6 | AS3462 | TW |
| tip-tr4cdn.cdn.hinet.net | 203.66.32.101 | AS3462 | TW |

### 2. 付款整合（台灣Pay，台灣）

* **域名性質**：`www.taiwanpay.com.tw` 為臺灣行動支付（TWMP）之台灣Pay，供 App 內付款
* **地理位置**：營運商 TWMP 位於台灣；本次解析經 Akamai（AS20940）節點（`23.195.81.154`）
* **角色**：訂票付款（App 另支援 Apple Pay）
* **資料特性**：付款流程；TWMP 支付核心在台灣（詳見台灣Pay 專篇報告）
* **DNS 解析結果**：

| 網域 | 解析 IP | ASN | 國家 |
|------|---------|-----|------|
| www.taiwanpay.com.tw | 23.195.81.154 | AS20940（Akamai） | US（節點） |

### 3. Google 分析／廣告／推播／字型（海外雲端）

* **域名性質**：分析（`www.google-analytics.com`、`www.googletagmanager.com`、`app-analytics-services.com`）、廣告（`www.googleadservices.com`）、Firebase／FCM 推播（`firebaseinstallations`、`firebaselogging-pa`、`device-provisioning.googleapis.com`）、Play 服務（`play.googleapis.com`、`clients4.google.com`）、字型（`fonts.gstatic.com` 等）
* **地理位置**：ASN **AS15169（Google LLC）**，Anycast
* **角色**：使用分析、廣告轉換追蹤、推播通知、網頁字型
* **資料特性**：傳送使用事件、廣告識別、推播 Token 與裝置資訊；營運商為 Google 海外
* **DNS 解析結果**（代表值）：

| 網域 | 解析 IP | ASN | 國家 |
|------|---------|-----|------|
| www.google-analytics.com | 142.250.66.78 | AS15169 | TW（節點） |
| www.googleadservices.com | 173.194.64.154 | AS15169 | US |
| firebaselogging-pa.googleapis.com | 172.217.112.4 | AS15169 | US |

### 4. iOS 系統背景網域（非 App 業務流量，已排除）

下列為 iOS 系統背景流量，與台鐵e訂通功能無關，於分析中排除：`stocks-data-service.apple.com`、`gateway.fe2.apple-dns.net`、`get-bx.g.aaplimg.com`、`gspe19-2-ssl.ls.apple.com`、`a1556.dscapi9.akamai.net`（Apple／Akamai）。

---

## API 用途整理

> **說明**：本次為瀏覽式測試，觀察到之請求以頁面／資料載入（GET）為主，故此節依「網域／功能角色」整理，不列具體端點與請求參數。實際訂票／付款流程未於本次觸發。

### 一、訂票與查詢（`www.railway.gov.tw`、`tip-tr4cdn.cdn.hinet.net`）

時刻查詢、訂票、電子車票、會員等核心功能向台鐵 `www.railway.gov.tw` 取得，靜態資源經 HiNet CDN（`tip-tr4cdn`）。台灣境內。

### 二、付款（`www.taiwanpay.com.tw`、Apple Pay）

付款整合台灣Pay（TWMP，台灣），另支援 Apple Pay。

### 三、分析、廣告與推播（Google）

App 嵌入 Google Analytics／Tag Manager、Google Ads 轉換、Firebase／FCM 推播，傳送使用行為與裝置資訊至 Google 海外。

---

## 請求流程概觀

```
App 啟動 / 首頁
  ├─→ 台鐵服務 / 訂票                    (www.railway.gov.tw ← HiNet 台灣)
  ├─→ 靜態資源 CDN                       (tip-tr4cdn.cdn.hinet.net ← HiNet)
  ├─→ Google 分析 / Tag Manager          (google-analytics, googletagmanager)
  ├─→ Firebase / FCM 推播                (firebaseinstallations, device-provisioning)
  └─→ 字型                               (fonts.gstatic.com)

訂票 → 付款
  ├─ 台灣Pay →                           (www.taiwanpay.com.tw ← TWMP 台灣)
  └─ Apple Pay（系統）

（廣告轉換：www.googleadservices.com）
```

---

## 摘要

| 分類 | 網域 | 是否核心功能 | 連線節點 | 資料是否出境 |
|------|------|--------------|----------|--------------|
| 台鐵核心服務 | `www.railway.gov.tw`、`tip-tr4cdn.cdn.hinet.net` | 是 | 台灣（HiNet） | 否 |
| 付款（台灣Pay） | `www.taiwanpay.com.tw` | 是 | 台灣（TWMP，Akamai 節點） | 否（營運商台灣） |
| Google 分析／廣告／推播 | `google-analytics`／`googleadservices`／`firebase` 等 | 否 | 台灣／美國（Google Anycast） | 可能／是 |
| 字型 | `fonts.gstatic.com` | 否 | 台灣（節點） | 可能 |

台鐵e訂通的**核心訂票與查詢功能**走臺灣鐵路自有之 `www.railway.gov.tw`（中華電信 HiNet，台灣境內），付款整合台灣Pay（TWMP，台灣營運）與 Apple Pay，**核心與付款資料均在台灣境內**，此部分符合期待。網域組成單純，第三方僅 Google 的分析、廣告轉換與 Firebase 推播等常見輔助服務。

> **隱私風險評估**：核心訂票與付款在台灣境內，風險低。第三方僅 Google 之分析、廣告轉換與推播（會傳送使用行為與裝置資訊至 Google 海外），為政府／公營 App 常見之嵌入，範圍相對有限。本次為未訂票之瀏覽式測試，實際訂票／付款時之個資（乘車人身分、聯絡、付款資訊）傳輸內容與加密方式，建議後續加測 HTTP 明細以完整評估。
