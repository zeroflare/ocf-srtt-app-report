---
title: 台灣行動支付（台灣Pay）
---

# 台灣行動支付（台灣Pay）App 網路流量分析報告

## 概述

<img src="https://is1-ssl.mzstatic.com/image/thumb/Purple221/v4/07/d5/b1/07d5b1c3-fe78-4781-a23c-2f235a99ec4c/AppIcon-0-0-1x_U007emarketing-0-7-0-85-220.png/512x512bb.jpg" alt="台灣行動支付 App 圖示" width="150" height="150" style="border-radius: 22%; object-fit: cover; box-shadow: 0 4px 12px rgba(0, 0, 0, 0.18);">

本報告依據 SRTT 與分包截取工具分析**台灣行動支付（台灣Pay）**（`tw.com.twmp.twhcewallet`）App 的網路請求。此 App 由**臺灣行動支付股份有限公司（TWMP）**提供，為金融同業共同投資之行動支付平台，功能包含收款、掃碼、付款、繳費、繳稅、轉帳、乘車碼、ATM 提款、新增卡片、餘額查詢等**金融支付服務**，並整合「便利生活」專區（用店家、好券 Buy 電商、點燈祈福、線上結匯／生活圈等）。

此 App 涉及**金融支付與個資**，資料流向為重點；同時其「便利生活」webview 專區引入較多**第三方電商、廣告與追蹤服務**，亦一併分析。

| 項目 | 內容 |
|------|------|
| App 名稱 | 台灣行動支付（台灣Pay） |
| App 版本 | 2.1.780 |
| 裝置 | iPhone 16 Pro / iOS 26.5.2 |
| 擷取時間 | 2026-07-22 |
| 涉及網域 | 約 37 個（App 相關；另約 9 個 iOS 系統背景網域已排除） |

> **測試方式與歸屬說明**：本次為「各功能點選瀏覽」之測試，觀察到之請求以頁面／資料載入（GET）為主。本報告以**網域與資料流向**為分析主軸；HTTP 端點層級（method／path／個資欄位／加密方式）之細節不在本次擷取範圍內。因 SRTT／MITM 為**全裝置**抓包，下述**廣告與追蹤類第三方**多由 App 內「便利生活」webview（好券 Buy、點燈祈福等第三方頁面）載入，其確切歸屬（App 自身 SDK 或 webview 內容）建議另以 iOS「App 隱私權報告」按 App 檢視佐證；本報告就觀察到之網域與流向據實記錄。SRTT 未擷取到之 Stream 網域，已另以 DNS／Cymru 補齊 IP 與 ASN。

---

## 網域分析

以下依**所屬單位／角色**分為六類彙整（廣告與追蹤類網域眾多，於分類中列舉代表）。iOS 系統層背景網域已於文末另列並排除。

| 網域 | 所屬單位 | 國家 | 雲端 | ASN | 主要用途 |
|------|----------|------|------|-----|----------|
| `www.taiwanpay.com.tw` | 臺灣行動支付 TWMP | 台灣 | 部分（Akamai 前端） | AS3462 / AS20940 | 台灣Pay 官方服務 |
| `www.twmp.com.tw` | 臺灣行動支付 TWMP | 台灣 | 否（電信機房） | AS3462 | 官方網站 |
| `merchant.twmp.com.tw` | 臺灣行動支付 TWMP | 台灣 | 否（電信機房） | AS3462 / AS9919 | 特約商店服務 |
| `wsp.twmp.com.tw` | 臺灣行動支付 TWMP | 台灣 | 否（電信機房） | AS3462 | 支付服務端點 |
| `twmp.edenred.com.tw` | Edenred（好券 Buy 電商） | 台灣 | 是（AWS） | AS16509 | 即享券電商 |
| `edenred-multisite.s3.ap-northeast-1.amazonaws.com` | Edenred（AWS S3） | **日本（東京）** | **是（AWS）** | AS16509 | 電商靜態資源儲存 |
| `www.lifemap.com.tw` | Lifemap 人生（點燈祈福） | 台灣（節點） | 是（AWS） | AS16509 | 點燈祈福 webview |
| `api.map8.zone` | map8 地圖 | 台灣（節點） | 是（Google Cloud） | AS396982 | 地圖／定位 API |
| `ad.doubleclick.net`／`pagead2.googlesyndication.com` 等 | Google 廣告（AdSense） | 台灣（節點） | 是 | AS15169 | 廣告聯播 |
| `www.google-analytics.com`／`app-measurement.com`／`googletagmanager` | Google 分析 | 台灣（節點） | 是 | AS15169 | 使用行為分析 |
| `connect.facebook.net`／`www.facebook.com` | Meta | 台灣（節點） | 是 | AS32934 | FB Pixel／SDK |
| `s.yimg.com` | Yahoo | 台灣（節點） | 是 | AS24376 | Yahoo 資源／廣告 |
| `sp.analytics.yahoo.com` | Yahoo（AWS） | 美國 | 是（AWS） | AS16509 | Yahoo 分析追蹤 |
| `tr.line.me` | LINE（LINE Tag） | **日本** | 是 | AS38631 | LINE 轉換追蹤 |
| `d.line-scdn.net` | LINE（CDN） | 台灣（節點） | 是（Akamai） | AS16625 | LINE 靜態資源 |
| `api.revenuecat.com` | RevenueCat（訂閱管理） | 美國 | 是（AWS） | AS16509 | 訂閱／購買管理 SaaS |
| `fh-…ecs.us-west-2.on.aws` | Amazon AWS（us-west-2） | **美國** | **是（AWS）** | AS16509 | 用途待確認之 AWS 端點 |
| `fonts.gstatic.com`／`fonts.googleapis.com`／`ssl.gstatic.com` | Google | 台灣（節點） | 是 | AS15169 | 網頁字型 |

> **國家判定說明**：TWMP 支付核心網域（`taiwanpay.com.tw`、`twmp.com.tw`、`merchant`、`wsp`）解析至台灣電信機房（**AS3462 中華電信 HiNet**，部分經 Akamai AS20940 台灣節點、`merchant` 另有 AS9919 新世紀資通），皆台灣境內。境外實體節點包括：Edenred 電商 S3（`edenred-multisite.s3.ap-northeast-1`）位於 **AWS 東京（日本）**；`tr.line.me`（LINE Tag）位於 **日本**；`sp.analytics.yahoo.com`、`api.revenuecat.com`、`fh-…on.aws` 位於 **AWS 美國**。Google、Meta、Yahoo、LINE CDN 等採 Anycast，連線節點多在台灣，營運商為海外雲端，出境記「可能」。

### 1. 支付核心服務（TWMP，台灣，非公有雲）⭐

* **域名性質**：`twmp.com.tw`／`taiwanpay.com.tw` 為臺灣行動支付之官方與服務域名
* **地理位置**：`www.twmp.com.tw`（`118.163.83.170`，AS3462 HiNet）、`wsp.twmp.com.tw`（`60.250.8.197`，AS3462）、`merchant.twmp.com.tw`（`122.147.170.136`／`210.242.162.127`，AS3462／AS9919）、`www.taiwanpay.com.tw`（`210.61.249.x`，AS3462／經 Akamai AS20940 台灣節點）
* **基礎設施**：台灣電信機房，非公有雲（`taiwanpay.com.tw` 前端部分經 Akamai 加速）
* **角色**：收款、掃碼、付款、繳費、繳稅、轉帳、乘車碼、ATM 提款、卡片與餘額等**金融支付核心**
* **資料特性**：涉及金融交易與個資（本次未實際完成交易）；核心支付服務主機均在台灣境內
* **DNS 解析結果**（A Record，代表值）：

| 網域 | 解析 IP | ASN | 國家 |
|------|---------|-----|------|
| www.twmp.com.tw | 118.163.83.170 | AS3462 | TW |
| wsp.twmp.com.tw | 60.250.8.197 | AS3462 | TW |
| merchant.twmp.com.tw | 122.147.170.136 / 210.242.162.127 | AS3462 / AS9919 | TW |
| www.taiwanpay.com.tw | 210.61.249.24 | AS3462 / AS20940 | TW |

AS3462＝中華電信 HiNet；AS9919＝新世紀資通；AS20940＝Akamai（台灣節點）。

### 2. 便利生活電商與服務（Edenred、Lifemap、map8）

* **域名性質**：「便利生活」專區之第三方服務——`twmp.edenred.com.tw`／`edenred-multisite.s3.ap-northeast-1.amazonaws.com`（好券 Buy 即享券電商，Edenred）、`www.lifemap.com.tw`（點燈祈福，Lifemap 人生）、`api.map8.zone`（地圖）
* **地理位置**：`twmp.edenred.com.tw`（`52.223.34.133`，AWS TW）、**`edenred-multisite.s3.ap-northeast-1`（AWS 東京，日本）**、`www.lifemap.com.tw`（`65.9.180.x`，AWS CloudFront TW）、`api.map8.zone`（`35.194.211.228`，Google Cloud TW）
* **角色**：好券 Buy 販售各式即享券／商品卡（webview 電商）；點燈祈福為 Lifemap 之線上點燈 webview；map8 提供地圖
* **資料特性**：電商與生活服務頁面；其中 **Edenred 電商靜態資源儲存於日本東京 S3**，屬境外
* **DNS 解析結果**：

| 網域 | 解析 IP | ASN | 國家 |
|------|---------|-----|------|
| twmp.edenred.com.tw | 52.223.34.133 | AS16509 | TW |
| edenred-multisite.s3.ap-northeast-1.amazonaws.com | 3.5.155.164 | AS16509 | JP |
| www.lifemap.com.tw | 65.9.180.102 | AS16509 | TW |
| api.map8.zone | 35.194.211.228 | AS396982 | TW |

### 3. 廣告與追蹤第三方（Google AdSense／Meta／Yahoo／LINE）⚠️

> **歸屬提醒**：此類廣告與追蹤網域**多由「便利生活」webview（好券 Buy、點燈祈福等第三方頁面）載入**，屬 App 內嵌第三方內容之常見廣告聯播與轉換追蹤；是否亦有 App 本體 SDK 直接發送，建議以 App 隱私權報告佐證。就資料流向而言，均會傳送瀏覽行為、廣告識別與裝置資訊至海外第三方。

* **Google 廣告（AdSense／DoubleClick）**：`ad.doubleclick.net`、`pagead2.googlesyndication.com`、`www.adsensecustomsearchads.com`、`syndicatedsearch.goog`、`ep1/ep2.adtrafficquality.google`、`cse.google.com`、`clients1.google.com`、`stats.g.doubleclick.net`（AS15169）
* **Google 分析／Firebase**：`www.google-analytics.com`、`analytics.google.com`、`app-measurement.com`、`app-analytics-services.com`、`www.googletagmanager.com`、`firebaseinstallations.googleapis.com`（AS15169）
* **Meta（Facebook Pixel／SDK）**：`connect.facebook.net`、`www.facebook.com`（AS32934）
* **Yahoo**：`s.yimg.com`（AS24376，資源／廣告）、`sp.analytics.yahoo.com`（AS16509 AWS 美國，分析追蹤）
* **LINE**：`tr.line.me`（AS38631，LINE Tag 轉換追蹤，日本）、`d.line-scdn.net`（AS16625 Akamai，CDN）
* **資料特性**：屬**非核心之廣告與跨站追蹤**，會傳送使用行為、廣告識別（如 Cookie／IDFA）與裝置資訊；營運商為海外第三方。對一款**金融支付** App 而言，此類廣告追蹤之範圍與必要性值得檢視
* **DNS 解析結果**（代表值）：

| 網域 | 解析 IP | ASN | 國家 |
|------|---------|-----|------|
| ad.doubleclick.net | 64.233.189.148 | AS15169 | TW（節點） |
| connect.facebook.net | 31.13.87.5 | AS32934 | TW（節點） |
| s.yimg.com | 180.222.109.251 | AS24376 | TW（節點） |
| sp.analytics.yahoo.com | 54.160.143.175 | AS16509 | US |
| tr.line.me | 147.92.191.92 | AS38631 | JP |

### 4. 其他境外 SaaS（RevenueCat、AWS 端點）

* **域名性質**：`api.revenuecat.com`（RevenueCat 訂閱／App 內購買管理 SaaS）、`fh-118116076e9a4c2a96a99fbb70bea2a0.ecs.us-west-2.on.aws`（AWS us-west-2 之 ECS／函式端點，用途待確認）
* **地理位置**：兩者均解析至 **AWS 美國**（RevenueCat `13.226.251.11`、AWS 端點 `100.20.53.235`）
* **資料特性**：RevenueCat 用於管理訂閱／購買狀態；`fh-…on.aws` 為 hash 命名之端點，僅見網域無法確認用途與內容，屬境外
* **DNS 解析結果**：

| 網域 | 解析 IP | ASN | 國家 |
|------|---------|-----|------|
| api.revenuecat.com | 13.226.251.11 | AS16509 | US |
| fh-…ecs.us-west-2.on.aws | 100.20.53.235 | AS16509 | US |

### 5. 網頁字型（Google，海外雲端）

`fonts.googleapis.com`、`fonts.gstatic.com`、`ssl.gstatic.com`（AS15169），供 webview 頁面載入字型，非核心。

### 6. iOS 系統背景網域（非 App 業務流量，已排除）

下列為 iOS 系統／App Store／地圖等背景流量，與台灣Pay 功能無關，於分析中排除：`gateway.fe2.apple-dns.net`、`configuration.ls.apple.com`、`gsp57-ssl-background.ls.apple.com`、`se2.itunes.apple.com`、`bag.itunes.apple.com`、`radio-services.itunes.apple.com`、`fpinit-itunes.g.aaplimg.com`、`h3.apis.apple.map.fastly.net`、`api-spotlight-aapne1c.smoot.apple.com`。

---

## API 用途整理

> **說明**：本次為瀏覽式測試，觀察到之請求以頁面／資料載入（GET）為主，故此節依「網域／功能角色」整理，不列具體端點與請求參數。實際完成收付款、繳費、轉帳等交易之流程未於本次觸發，留待後續加測。

### 一、金融支付核心（`*.twmp.com.tw`、`taiwanpay.com.tw`）

收款、掃碼、付款、繳費、繳稅、轉帳、乘車碼、ATM 提款、卡片與餘額等，向 TWMP 之 `wsp`／`merchant.twmp.com.tw` 與 `taiwanpay.com.tw` 取得。均為台灣境內主機。繳費／繳稅另由「臺灣銀行」透過全國繳費網、財政部繳稅服務網（`paytax.nat.gov.tw`）處理。

### 二、便利生活（Edenred、Lifemap、map8）

好券 Buy 即享券電商由 Edenred 提供（含日本東京 S3 資源）；點燈祈福為 Lifemap webview；地圖由 map8 提供。

### 三、廣告與追蹤（Google／Meta／Yahoo／LINE）

「便利生活」webview 載入 Google AdSense 廣告、Google/Firebase 分析、Facebook Pixel、Yahoo 與 LINE Tag 等第三方廣告與追蹤，傳送瀏覽行為與識別資訊至海外。

---

## 請求流程概觀

```
App 啟動 / 首頁
  ├─→ 支付核心服務                      (wsp / merchant.twmp.com.tw, taiwanpay.com.tw ← 台灣)
  ├─→ Google 分析 / Firebase            (google-analytics, app-measurement, firebaseinstallations)
  └─→ 網頁字型                          (fonts.gstatic.com)

金融支付（收款 / 付款 / 掃碼 / 繳費 / 繳稅 / 轉帳 / 乘車碼 / ATM）
  └─→ 均走 TWMP 台灣主機                (wsp / merchant.twmp.com.tw)
        └─ 繳費/繳稅 → 臺灣銀行全國繳費網 / 財政部 paytax.nat.gov.tw

便利生活 webview（第三方內容，帶入廣告與追蹤）
  ├─ 好券 Buy（電商）→ Edenred          (twmp.edenred.com.tw + S3 東京/日本)
  ├─ 點燈祈福 → Lifemap                 (www.lifemap.com.tw ← AWS)
  ├─ 地圖 → map8                        (api.map8.zone ← Google Cloud)
  └─→ 廣告 / 追蹤（webview 內嵌）：
        ├─ Google AdSense               (doubleclick, googlesyndication, adtrafficquality)
        ├─ Facebook Pixel               (connect.facebook.net)
        ├─ Yahoo 追蹤                    (s.yimg.com, sp.analytics.yahoo.com ← 美國)
        └─ LINE Tag                      (tr.line.me ← 日本)

其他 SaaS：RevenueCat 訂閱管理、AWS us-west-2 端點（美國，用途待確認）
```

---

## 摘要

| 分類 | 網域 | 是否核心功能 | 連線節點 | 資料是否出境 |
|------|------|--------------|----------|--------------|
| 金融支付核心 | `*.twmp.com.tw`、`taiwanpay.com.tw` | 是 | 台灣（HiNet／Akamai TW） | 否 |
| 便利生活電商 | `edenred`、`lifemap`、`map8` | 否 | 台灣為主；Edenred S3 於**日本** | 部分是（Edenred S3 日本） |
| 廣告與追蹤 | Google AdSense／Meta／Yahoo／LINE | 否 | 多為台灣 Anycast；LINE Tag 日本、Yahoo 分析美國 | 可能／部分是 |
| 其他 SaaS | `api.revenuecat.com`、`…on.aws` | 否 | **美國** | 是（若屬本 App） |
| 網頁字型 | `fonts.gstatic.com` 等 | 否 | 台灣（節點） | 可能 |

台灣Pay 的**金融支付核心功能**（收款、付款、掃碼、繳費、繳稅、轉帳、乘車碼、ATM）走臺灣行動支付（TWMP）自有之 `*.twmp.com.tw`／`taiwanpay.com.tw`，託管於台灣電信機房（HiNet，部分經 Akamai 台灣節點），繳費繳稅另由臺灣銀行／財政部繳稅網處理，**核心支付與金融資料在台灣境內**，此部分符合期待。

較需留意的是「**便利生活**」專區——其以 webview 引入**第三方電商（Edenred，靜態資源儲存於日本東京 S3）、Lifemap 點燈祈福**，並隨之載入**大量廣告與跨站追蹤**：Google AdSense（`doubleclick`、`googlesyndication`、`adtrafficquality` 等）、Facebook Pixel、Yahoo（分析主機在美國）、LINE Tag（日本）。此外另見 RevenueCat 與一個 AWS 美國端點。對一款**金融支付** App 而言，內嵌如此範圍之廣告與跨站追蹤，其必要性與傳輸內容值得檢視。

> **隱私風險評估**：**金融支付核心**在台灣境內，風險低。風險集中於**非核心的「便利生活」webview 所引入之第三方廣告與追蹤**——會將使用者瀏覽行為與廣告識別傳送至 Google、Meta、Yahoo、LINE 等海外第三方（部分節點在日本、美國），且 Edenred 電商資源存於日本。惟因本次為**全裝置抓包、未登入、且無 HTTP 內容**，這些廣告／追蹤之**確切歸屬（App 本體或 webview 內容）與傳輸內容尚無法確認**。建議後續：(a) 以 iOS「App 隱私權報告」按 App 檢視「台灣行動支付」名下網域，確認上述廣告／追蹤是否確為本 App 所發；(b) 針對支付與便利生活流程加測 HTTP 明細。在此之前，本報告僅就網域與流向提出觀察，不對第三方之風險程度作定論。
