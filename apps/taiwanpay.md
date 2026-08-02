---
title: 台灣行動支付（台灣Pay）
---

# 台灣行動支付（台灣Pay）App 網路流量分析報告

## 概述

<img src="https://is1-ssl.mzstatic.com/image/thumb/Purple221/v4/07/d5/b1/07d5b1c3-fe78-4781-a23c-2f235a99ec4c/AppIcon-0-0-1x_U007emarketing-0-7-0-85-220.png/512x512bb.jpg" alt="台灣行動支付 App 圖示" width="150" height="150" style="border-radius: 22%; object-fit: cover; box-shadow: 0 4px 12px rgba(0, 0, 0, 0.18);">

本報告依據 SRTT 與封包擷取工具分析**台灣行動支付（台灣Pay）**（`tw.com.twmp.twhcewallet`）App 的網路請求。此 App 由**臺灣行動支付股份有限公司（TWMP）**提供，為金融同業共同投資之行動支付平台，功能包含收款、掃碼、付款、繳費、繳稅、轉帳、乘車碼、ATM 提款、新增卡片、餘額查詢等**金融支付服務**，並整合「便利生活」專區（用店家、好券 Buy 電商、點燈祈福、線上結匯／生活圈等）。

此 App 涉及**金融支付與個資**，資料流向為重點；同時其「便利生活」webview 專區引入較多**第三方電商、廣告與追蹤服務**，亦一併分析。

| 項目 | 內容 |
|------|------|
| App 名稱 | 台灣行動支付（台灣Pay） |
| App 版本 | 2.1.780 |
| 裝置 | iPhone 16 Pro / iOS 26.5.2 |
| 擷取時間 | 2026-07-22（初測）／**2026-07-30（複測，本報告以複測為準）** |
| 涉及網域 | 初測約 37 個；**複測觀察到 45 個主機**（含新見之 GroupBuyForms 團購、OpenStreetMap、YouTube 等） |

> **測試方式與歸屬說明**：**複測全程採 HTTPS MITM，App 對後端採憑證綁定，本次幾乎全為 CONNECT（僅 2 筆 GET，屬 `www.twmp.com.tw`）、內容未解密**；因此本次以**網域與連線 IP／連接埠之歸屬與國家**為據實記錄之對象，無法檢視 HTTP 內容。TWMP 支付核心觀察到走**非標準高埠**（`wsp:8743/9143`、`merchant:9043/8343/9898`）。因 SRTT／MITM 為**全裝置**擷取，下述廣告／追蹤與部分第三方（YouTube、OpenStreetMap 等）之確切歸屬（App／webview／其他 App 背景）無法由本次資料斷定；本報告採「**看到什麼記什麼、疑似雜訊者標明**」原則，據實列出並標註待確認項。

---

## 網域分析

以下依**所屬單位／角色**分為六類彙整（廣告與追蹤類網域眾多，於分類中列舉代表）。iOS 系統層背景網域已於文末另列並排除。

| 網域 | 所屬單位 | 國家 | 雲端 | ASN | Anycast | 主要用途 |
|------|------|------|------|------|------|------|
| `www.taiwanpay.com.tw` | 臺灣行動支付 TWMP | TW | 部分（Akamai 前端） | AS3462（中華電信 HiNet） / AS20940（Akamai） |  | 台灣Pay 官方服務 |
| `www.twmp.com.tw` | 臺灣行動支付 TWMP | TW | 否（電信機房） | AS3462（中華電信 HiNet） |  | 官方網站 |
| `merchant.twmp.com.tw` | 臺灣行動支付 TWMP | TW | 否（電信機房） | AS3462（中華電信 HiNet） / AS9919（新世紀資通） |  | 特約商店服務 |
| `wsp.twmp.com.tw` | 臺灣行動支付 TWMP | TW | 否（電信機房） | AS3462（中華電信 HiNet） |  | 支付服務端點 |
| `twmp.edenred.com.tw` | Edenred（好券 Buy 電商） | TW | 是（AWS） | AS16509（AWS） |  | 即享券電商 |
| `edenred-multisite.s3.ap-northeast-1.amazonaws.com` | Edenred（AWS S3） | JP | **是（AWS）** | AS16509（AWS） |  | 電商靜態資源儲存 |
| `www.lifemap.com.tw` | Lifemap 人生（點燈祈福） | TW | 是（AWS） | AS16509（AWS） | ✓ | 點燈祈福 webview |
| `api.map8.zone` | map8 地圖 | TW | 是（Google Cloud） | AS396982（Google Cloud） | ✓ | 地圖／定位 API |
| `ad.doubleclick.net`／`pagead2.googlesyndication.com` 等 | Google 廣告（AdSense） | TW | 是 | AS15169（Google） | ✓ | 廣告聯播 |
| `www.google-analytics.com`／`app-measurement.com`／`googletagmanager` | Google 分析 | TW | 是 | AS15169（Google） | ✓ | 使用行為分析 |
| `connect.facebook.net`／`www.facebook.com` | Meta | TW／US | 是 | AS32934（Meta） | ✓ | FB Pixel／SDK |
| `s.yimg.com` | Yahoo | TW | 是 | AS24376（Yahoo） |  | Yahoo 資源／廣告 |
| `sp.analytics.yahoo.com` | Yahoo（AWS） | TW | 是（AWS） | AS16509（AWS） |  | Yahoo 分析追蹤 |
| `tr.line.me` | LINE（LINE Tag） | JP | 是 | AS38631（LINE） |  | LINE 轉換追蹤 |
| `d.line-scdn.net` | LINE（CDN） | TW | 是（Akamai） | AS16625（Akamai） |  | LINE 靜態資源 |
| `api.revenuecat.com` | RevenueCat（訂閱管理） | TW | 是（AWS） | AS16509（AWS） | ✓ | 訂閱／購買管理 SaaS |
| `fh-…ecs.us-west-2.on.aws` | Amazon AWS（us-west-2） | **美國** | **是（AWS）** | AS16509（AWS） |  | 用途待確認之 AWS 端點 |
| `fonts.gstatic.com`／`fonts.googleapis.com`／`ssl.gstatic.com` | Google | TW | 是 | AS15169（Google） | ✓ | 網頁字型 |

> **國家判定說明**：TWMP 支付核心網域（`taiwanpay.com.tw`、`twmp.com.tw`、`merchant`、`wsp`）解析至台灣電信機房（**AS3462 中華電信 HiNet**，部分經 Akamai AS20940 台灣節點、`merchant` 另有 AS9919 新世紀資通），皆台灣境內。境外實體節點包括：Edenred 電商 S3（`edenred-multisite.s3.ap-northeast-1`）位於 **AWS 東京（日本）**；`tr.line.me`（LINE Tag）位於 **日本**；`fh-…on.aws` 位於 **AWS 美國**。`api.revenuecat.com` 經 mtr 443 為 CloudFront **tpe54（台灣）**。Google、Meta、Yahoo、LINE CDN 等採 Anycast，連線節點多在台灣，營運商為海外雲端，出境記「可能」。

> **國家／Anycast 判定（`mtr --tcp --port 443`）**：以 TCP/443 終點延遲與 PTR／ASN 判定連線節點國家；Cloudflare、Google、Meta、CloudFront、GCP Global LB 等標 Anycast ✓。營運商為海外者，資料出境仍可能為「是／可能」，與連線節點國家分開判斷。

### 1. 支付核心服務（TWMP，台灣，非公有雲）⭐

* **域名性質**：`twmp.com.tw`／`taiwanpay.com.tw` 為臺灣行動支付之官方與服務域名
* **地理位置**：`www.twmp.com.tw`（`118.163.83.170`，AS3462 HiNet）、`wsp.twmp.com.tw`（`60.250.8.197`，AS3462）、`merchant.twmp.com.tw`（`122.147.170.136`／`210.242.162.127`，AS3462／AS9919）、`www.taiwanpay.com.tw`（`210.61.249.x`，AS3462／經 Akamai AS20940 台灣節點）
* **基礎設施**：台灣電信機房，非公有雲（`taiwanpay.com.tw` 前端部分經 Akamai 加速）
* **角色**：收款、掃碼、付款、繳費、繳稅、轉帳、乘車碼、ATM 提款、卡片與餘額等**金融支付核心**
* **資料特性**：涉及金融交易與個資（本次未實際完成交易，全程 CONNECT 未解密）；核心支付服務主機均在台灣境內
* **DNS／連線 IP（本次實際連線；含觀察到之連接埠）**：

| 網域（埠） | 連線 IP | ASN | 國家 |
|------|---------|-----|------|
| www.twmp.com.tw:443 | 118.163.83.170 | AS3462（中華電信 HiNet） | TW |
| wsp.twmp.com.tw:8743／9143 | 60.248.93.69 | AS3462（中華電信 HiNet） | TW |
| merchant.twmp.com.tw:9043／8343／9898 | 210.242.162.127 | AS3462（中華電信 HiNet） / AS9919（新世紀資通） | TW |
| www.taiwanpay.com.tw:443 | 203.69.81.136 | AS3462（中華電信 HiNet） / AS20940（Akamai） | TW |

（本次 `wsp`／`merchant` 之連線 IP 與初測略有不同、且使用非標準高埠，惟 whois 仍為中華電信 HiNet／新世紀資通，台灣境內。）

AS3462＝中華電信 HiNet；AS9919＝新世紀資通；AS20940＝Akamai（台灣節點）。

### 2. 便利生活電商與服務（Edenred、Lifemap、map8）

* **域名性質**：「便利生活」專區之第三方服務——`twmp.edenred.com.tw`／`edenred-multisite.s3.ap-northeast-1.amazonaws.com`（好券 Buy 即享券電商，Edenred）、`www.lifemap.com.tw`（點燈祈福，Lifemap 人生）、`api.map8.zone`（地圖）
* **地理位置**：`twmp.edenred.com.tw`（`52.223.34.133`，AWS TW）、**`edenred-multisite.s3.ap-northeast-1`（AWS 東京，日本）**、`www.lifemap.com.tw`（`65.9.180.x`，AWS CloudFront TW）、`api.map8.zone`（`35.194.211.228`，Google Cloud TW）
* **角色**：好券 Buy 販售各式即享券／商品卡（webview 電商）；點燈祈福為 Lifemap 之線上點燈 webview；map8 提供地圖
* **資料特性**：電商與生活服務頁面；其中 **Edenred 電商靜態資源儲存於日本東京 S3**，屬境外
* **DNS 解析結果**：

| 網域 | 解析 IP | ASN | 國家 |
|------|---------|-----|------|
| twmp.edenred.com.tw | 52.223.34.133 | AS16509（AWS） | TW |
| edenred-multisite.s3.ap-northeast-1.amazonaws.com | 3.5.155.164 | AS16509（AWS） | JP |
| www.lifemap.com.tw | 65.9.180.102 | AS16509（AWS） | TW |
| api.map8.zone | 35.194.211.228 | AS396982（Google Cloud） | TW |

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
| ad.doubleclick.net | 64.233.189.148 | AS15169（Google） | TW（節點） |
| connect.facebook.net | 31.13.87.5 | AS32934（Meta） | TW（節點） |
| s.yimg.com | 180.222.109.252 | AS24376（Yahoo） | TW（節點） |
| sp.analytics.yahoo.com | 180.222.109.252 | AS24376（Yahoo） | **TW（本次為 Yahoo 台灣節點）** |
| tr.line.me | 147.92.191.92 | AS38631（LINE） | JP |

> **本次複測差異**：(1) `sp.analytics.yahoo.com` 本次解析至 Yahoo 台灣節點（`180.222.109.252`），與初測之美國 IP 不同（Anycast，營運商仍為海外）。(2) 本次 Google 廣告類**未見** `ad.doubleclick.net`／`pagead2.googlesyndication.com`／`app-measurement.com`／`firebaseinstallations`，實際出現者為 **AdSense for Search 一組**（`cse.google.com`、`syndicatedsearch.goog`、`www.adsensecustomsearchads.com`、`ep1/ep2.adtrafficquality.google`、`clients1.google.com`）＋ `googletagmanager`／`analytics.google.com`。此差異可能與本次操作路徑及未解密有關。

### 4. 其他境外 SaaS（RevenueCat、AWS 端點）

* **域名性質**：`api.revenuecat.com`（RevenueCat 訂閱／App 內購買管理 SaaS）、`fh-118116076e9a4c2a96a99fbb70bea2a0.ecs.us-west-2.on.aws`（AWS us-west-2 之 ECS／函式端點，用途待確認）
* **地理位置**：兩者均解析至 **AWS 美國**（RevenueCat `13.226.251.11`、AWS 端點 `100.20.53.235`）
* **資料特性**：RevenueCat 用於管理訂閱／購買狀態；`fh-…on.aws` 為 hash 命名之端點，僅見網域無法確認用途與內容，屬境外
* **DNS 解析結果**：

| 網域 | 解析 IP | ASN | 國家 |
|------|---------|-----|------|
| api.revenuecat.com | 13.226.251.11（本次未再現） | AS16509（AWS） | US |
| fh-…ecs.us-west-2.on.aws | 16.147.119.161（本次仍見） | AS16509（AWS） | US |

（`fh-118116076e9a4c2a96a99fbb70bea2a0.ecs.us-west-2.on.aws` 本次仍出現一次，連 AWS 美國 `us-west-2`；`api.revenuecat.com` 本次未再出現。此 hash 命名端點用途仍待確認。）

### 4b. 複測新見網域（raw data，部分疑似雜訊，待確認）

以下為 2026-07-30 複測新出現、初測未列之主機，全為 CONNECT（未解密）。依「看到什麼記什麼」原則據實列出，並標明疑似雜訊者：

| 網域 | 連線 IP | 所屬／雲端 | 國家 | 研判 |
|------|---------|-----------|------|------|
| `p3.groupbuyforms.tw` | 35.194.187.24 | Google Cloud | US（GCP，asia-east1） | GroupBuyForms 團購表單服務（疑似便利生活 webview） |
| `gbf.tw` | 104.199.236.4 | Google Cloud | US（GCP） | 同上（GroupBuyForms 短網域） |
| `cdn.groupbuyforms.com` | 104.21.65.220 | Cloudflare | 海外節點 | GroupBuyForms 靜態資源 |
| `api.nlsc.gov.tw` | 140.110.134.21 | 國網中心 NCHC | TW | 內政部國土測繪地圖 API（地圖功能） |
| `a/b/c.tile.openstreetmap.org` | 199.232.113.91 | Fastly | 海外節點 | OpenStreetMap 地圖圖磚 |
| `cdnjs.cloudflare.com` | 104.17.24.14 | Cloudflare | 海外節點 | 前端 JS 函式庫 CDN（webview） |
| `scdn.line-apps.com` | 104.116.17.150 | Akamai（LINE） | 海外節點 | LINE 靜態資源 |
| `www.youtube.com`／`i.ytimg.com`／`yt3.ggpht.com`／`*.googlevideo.com`／`jnn-pa.googleapis.com` | 142.250.x／210.61.221.209 | Google（YouTube） | 台灣／海外節點 | **疑似 App 內嵌／背景 YouTube，歸屬待確認（可能為雜訊）** |

> **雜訊說明**：因全裝置擷取且多為單次 CONNECT，上表 YouTube 一組與部分海外 CDN 不排除來自其他 App 或系統背景；GroupBuyForms、`api.nlsc.gov.tw`、OpenStreetMap 則與「便利生活／地圖」功能相符，較可能屬本 App。以上均待後續乾淨環境或 App 隱私權報告佐證。

### 5. 網頁字型（Google，海外雲端）

`fonts.googleapis.com`、`fonts.gstatic.com`、`ssl.gstatic.com`（AS15169），供 webview 頁面載入字型，非核心。

### 6. iOS 系統背景網域（非 App 業務流量，已排除）

下列為 iOS 系統／App Store／地圖等背景流量，與台灣Pay 功能無關，於分析中排除：`gateway.fe2.apple-dns.net`、`configuration.ls.apple.com`、`gsp57-ssl-background.ls.apple.com`、`se2.itunes.apple.com`、`bag.itunes.apple.com`、`radio-services.itunes.apple.com`、`fpinit-itunes.g.aaplimg.com`、`h3.apis.apple.map.fastly.net`、`api-spotlight-aapne1c.smoot.apple.com`。

---

## API 用途整理

> **說明**：複測幾乎全為 CONNECT、內容未解密，此節依「網域／功能角色」整理，無法列出端點與參數。實際完成收付款、繳費、轉帳等交易之流程未於本次觸發。

### 一、金融支付核心（`*.twmp.com.tw`、`taiwanpay.com.tw`）

收款、掃碼、付款、繳費、繳稅、轉帳、乘車碼、ATM 提款、卡片與餘額等，向 TWMP 之 `wsp`／`merchant.twmp.com.tw` 與 `taiwanpay.com.tw` 取得。均為台灣境內主機。繳費／繳稅另由「臺灣銀行」透過全國繳費網、財政部繳稅服務網（`paytax.nat.gov.tw`）處理。

### 二、便利生活（Edenred、Lifemap、map8）

好券 Buy 即享券電商由 Edenred 提供（含日本東京 S3 資源）；點燈祈福為 Lifemap webview；地圖由 map8 提供。複測另見 **GroupBuyForms 團購表單**（`p3.groupbuyforms.tw`、`gbf.tw`、`cdn.groupbuyforms.com`，GCP／Cloudflare）、**國土測繪 `api.nlsc.gov.tw`（台灣）**與 **OpenStreetMap 圖磚**，研判屬便利生活／地圖功能（詳 §4b）。

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
| 便利生活新見（複測） | GroupBuyForms（`groupbuyforms.tw`／`gbf.tw`）、`api.nlsc.gov.tw`、OpenStreetMap | 否 | GCP／台灣／Fastly | 部分是 |
| 其他 SaaS／待確認 | `…ecs.us-west-2.on.aws`（本次仍見）、YouTube 一組（疑似雜訊） | 否 | **美國**／台灣節點 | 是／待確認 |
| 網頁字型 | `fonts.gstatic.com` 等 | 否 | 台灣（節點） | 可能 |

台灣Pay 的**金融支付核心功能**（收款、付款、掃碼、繳費、繳稅、轉帳、乘車碼、ATM）走臺灣行動支付（TWMP）自有之 `*.twmp.com.tw`／`taiwanpay.com.tw`，託管於台灣電信機房（HiNet，部分經 Akamai 台灣節點），繳費繳稅另由臺灣銀行／財政部繳稅網處理，**核心支付與金融資料在台灣境內**，此部分符合期待。

較需留意的是「**便利生活**」專區——其以 webview 引入**第三方電商（Edenred，靜態資源儲存於日本東京 S3）、Lifemap 點燈祈福**，並隨之載入**大量廣告與跨站追蹤**：Google AdSense（`doubleclick`、`googlesyndication`、`adtrafficquality` 等）、Facebook Pixel、Yahoo（分析主機在美國）、LINE Tag（日本）。此外另見 RevenueCat 與一個 AWS 美國端點。對一款**金融支付** App 而言，內嵌如此範圍之廣告與跨站追蹤，其必要性與傳輸內容值得檢視。

> **隱私風險評估**：**金融支付核心**在台灣境內，風險低。風險集中於**非核心的「便利生活」webview 所引入之第三方廣告與追蹤**——會將使用者瀏覽行為與廣告識別傳送至 Google、Meta、Yahoo、LINE 等海外第三方（部分節點在日本、美國），且 Edenred 電商資源存於日本。惟因本次為**全裝置擷取、未登入、且無 HTTP 內容**，這些廣告／追蹤之**確切歸屬（App 本體或 webview 內容）與傳輸內容尚無法確認**。建議後續：(a) 以 iOS「App 隱私權報告」按 App 檢視「台灣行動支付」名下網域，確認上述廣告／追蹤是否確為本 App 所發；(b) 針對支付與便利生活流程加測 HTTP 明細。在此之前，本報告僅就網域與流向提出觀察，不對第三方之風險程度作定論。
