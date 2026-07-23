---
title: LINE
---

# LINE App 網路流量分析報告

## 概述

<img src="https://is1-ssl.mzstatic.com/image/thumb/Purple211/v4/3d/1c/a8/3d1ca800-855b-c093-0f19-038316458142/basic_default-0-0-1x_U007epad-0-6-0-0-sRGB-0-85-220.png/512x512bb.jpg" alt="LINE App 圖示" width="150" height="150" style="border-radius: 22%; object-fit: cover; box-shadow: 0 4px 12px rgba(0, 0, 0, 0.18);">

本報告依據 SRTT 與分包截取工具分析 **LINE**（`jp.naver.line`）App 的網路請求。此 App 由 **LY Corporation（日本，LINE 與 Yahoo! JAPAN 合併後之營運公司，母集團為 SoftBank／Naver）**提供，是一款整合即時通訊、社群（VOOM 短影音）、新聞（LINE Today）、貼圖商店、購物（LINE Shopping）、支付與銀行（LINE Pay／LINE Bank）、票券發票、音樂、旅遊、點數等眾多服務之**超級 App（super app）**。

**與前幾款台灣政府／金融 App 本質不同**：LINE 為**外商（日本）**服務，其核心通訊與社群資料**本即由日本公司處理**，跨境傳輸至日本屬平台本質、而非隱蔽外洩。本報告聚焦於各服務之資料流向、CDN 節點分布，以及所整合之第三方廣告與追蹤生態。

| 項目 | 內容 |
|------|------|
| App 名稱 | LINE |
| App 版本 | 26.10.0 |
| 裝置 | iPhone 16 Pro / iOS 26.5.2 |
| 擷取時間 | 2026-07-22 |
| 涉及網域 | 約 80 個（SRTT 41 + Stream 補充；含 LINE 各服務、CDN 與第三方廣告／追蹤） |

> **測試方式與歸屬說明**：本次操作涵蓋貼圖、新聞（Today）、短影音（VOOM）、LINE Bank 等功能。本報告以**網域與資料流向**為分析主軸；HTTP 端點層級（method／path／個資欄位／加密方式）之細節不在本次擷取範圍內。因 SRTT／MITM 為**全裝置**抓包，且 LINE 為整合眾多服務與第三方 SDK 之超級 App，個別廣告／追蹤網域之確切歸屬（LINE 本體或內嵌內容）建議另以 iOS「App 隱私權報告」佐證；本報告就觀察到之網域與流向據實記錄。SRTT 未擷取到之 Stream 網域，已另以 DNS／Cymru 補齊 IP 與 ASN。網域數量龐大，以下依角色分組並列舉代表網域。

---

## 網域分析

依**所屬服務／角色**分為七類。各類列舉代表網域及其節點所在。

| 類別 | 代表網域 | 所屬／營運 | 節點國家 | ASN | 用途 |
|------|----------|-----------|----------|-----|------|
| LINE 核心平台 | `legy.line-apps.com`、`api.line.me`、`access.line.me`、`lin.ee`、`notice.line.me` | LY Corporation（日本） | 日本／CDN（美） | AS38631 / AS16625 / AS20940 | 通訊傳輸、API、登入、短網址、通知 |
| LINE 內容服務 | `today.line.me`、`stickershop.line.me`、`buy.line.me`、`points.line.me`、`music-tw.line.me`、`travel.line.me`、`invoice.line.me` | LY Corporation | 日本／CDN | AS38631 / AS16625 / AS20940 | Today 新聞、貼圖、購物、點數、音樂、旅遊、發票 |
| LINE 靜態 CDN | `obs.line-scdn.net`、`today-obs`、`shopping.line-scdn.net`、`profile.line-scdn.net`、`stickershop.line-scdn.net` | LINE（Akamai／AWS） | 台灣／美國 | AS16625 / AS20940 / AS16509 | 圖片、貼圖、大頭貼等靜態資源 |
| LINE Bank／Pay（台灣金融） | `cwa.linebank.com.tw`、`img.linebank.com.tw`、`tsln.taishinbank.com.tw`、`web-tw-pay.line.me` | 連線商業銀行 LINE Bank／台新銀行 | 台灣 | AS9924 / AS9919 / AS131660 | 網銀、支付 |
| 廣告與追蹤 | `doubleclick`、`googlesyndication`、`moloco.com`、`dable.io`、`connect.facebook.net`、`bat.bing.com`、`adjust.com` | Google／Moloco／Dable（韓）／Meta／微軟／Adjust | 美國／韓國／台灣 | AS15169 等 | 廣告聯播、內容推薦、轉換追蹤 |
| 分析／監控／安全 | `firebase.googleapis.com`、`google-analytics`、`sentry.io`、`v-key.com` | Google／Sentry／V-Key | 美國 | AS15169 / AS396982 / AS8075 | 分析、錯誤監控、App 安全 SDK |
| 其他第三方嵌入 | `platform.twitter.com`、`scontent.xx.fbcdn.net`、`linetoday.edh.tw` | Twitter／Meta／聯合報系 | 香港／台灣／美國 | AS54113 / AS32934 / AS396982 | 社群嵌入、新聞內容 |

> **國家判定說明**：LINE 核心（`*.line-apps.com`、多數 `*.line.me` 原站）解析至 **AS38631（LY Corporation）**，實體在**日本**——此為 LINE 平台本質。CDN 網域（`line-scdn.net`、`api.line.me`、`today.line.me` 等）透過 Akamai（AS16625／AS20940）與 AWS CloudFront（AS16509）分布於**台灣、美國、香港**等節點。**LINE Bank／Pay 之台灣金融網域**（`linebank.com.tw`、`taishinbank.com.tw`）解析至**台灣**（AS9924 台灣固網、AS9919 新世紀資通、AS131660 台新銀行），金融資料在境內。廣告與追蹤第三方多在**美國**，內容推薦商 **Dable（`api.dable.io`）位於韓國（AWS 首爾）**。

### 1. LINE 核心平台（LY Corporation，日本）

* **域名性質**：`legy.line-apps.com`（LINE 核心傳輸閘道 LEGY）、`api.line.me`、`access.line.me`（登入）、`lin.ee`（短網址）、`notice.line.me`、`obs-tw.line-apps.com`、`torimochi.line-apps.com` 等
* **地理位置**：原站解析至 **AS38631（LY Corporation，日本）**，`147.92.x.x`；部分經 Akamai（`api.line.me`→AS16625、`access.line.me`→AS20940）之美國節點
* **角色**：即時通訊訊息傳輸、帳號登入與驗證、通知、各服務 API 入口
* **資料特性**：承載通訊與帳號核心資料，**由日本 LY Corporation 處理**（平台本質）
* **DNS 解析結果**（代表值）：

| 網域 | 解析 IP | ASN | 國家 |
|------|---------|-----|------|
| legy.line-apps.com | 147.92.146.129 | AS38631 | JP |
| web-tw-pay.line.me | 147.92.184.234 | AS38631 | JP |
| notice.line.me | 147.92.191.86 | AS38631 | JP |
| api.line.me | 23.217.78.121 | AS16625（Akamai） | US（節點） |
| access.line.me | 23.195.81.107 | AS20940（Akamai） | US（節點） |

AS38631 為 LY Corporation（日本）。

### 2. LINE 內容服務（Today／VOOM／貼圖／購物／點數／音樂／旅遊／發票）

* **域名性質**：`today.line.me`（新聞）、`voom-obs.line-scdn.net`（短影音）、`stickershop.line.me`（貼圖）、`buy.line.me`／`shopping.line-scdn.net`（購物）、`points.line.me`（點數）、`music-tw.line.me`（音樂）、`travel.line.me`（旅遊）、`invoice.line.me`（發票）
* **地理位置**：原站多為 AS38631（日本）；圖片與靜態經 Akamai／AWS 之台灣、美國節點
* **角色**：LINE 超級 App 之各內容與電商服務
* **資料特性**：瀏覽行為、內容互動；`invoice.line.me`（雲端發票）涉及發票／消費資料
* **DNS 解析結果**（代表值）：

| 網域 | 解析 IP | ASN | 國家 |
|------|---------|-----|------|
| today.line.me | 23.217.76.44 | AS16625（Akamai） | US（節點） |
| stickershop.line.me | 18.238.118.84 | AS16509（AWS） | US（節點） |
| buy.line.me | 210.71.227.51 | AS3462 / AS20940 | TW（節點） |
| points.line.me | 147.92.242.166 | AS38631 | JP |
| music-tw.line.me | 147.92.144.186 | AS38631 | JP |

### 3. LINE 靜態 CDN（`line-scdn.net`，Akamai／AWS）

* **域名性質**：`obs.line-scdn.net`、`today-obs`、`shopping.line-scdn.net`、`profile.line-scdn.net`（大頭貼）、`stickershop.line-scdn.net`、`wcc-image`、`vos.line-scdn.net` 等
* **地理位置**：透過 Akamai（AS16625／AS20940，台灣節點如 `210.61.248.x`）與 AWS CloudFront（AS16509，美國節點 `54.192.x.x`）分布
* **角色**：圖片、貼圖、大頭貼、影音縮圖等靜態資源傳遞
* **資料特性**：靜態內容，不直接涉個資
* **DNS 解析結果**（代表值）：

| 網域 | 解析 IP | ASN | 國家 |
|------|---------|-----|------|
| obs.line-scdn.net | 184.25.120.178 | AS16625（Akamai） | TW（節點） |
| today-obs.line-scdn.net | 210.61.248.18 | AS20940（Akamai） | TW（節點） |
| buy-obs.line-scdn.net | 54.192.255.100 | AS16509（AWS） | US（節點） |

### 4. LINE Bank／Pay（台灣金融，境內）⭐

* **域名性質**：`cwa.linebank.com.tw`、`img.linebank.com.tw`、`line-img.linebank.com.tw`（LINE Bank 連線商業銀行）、`tsln.taishinbank.com.tw`（台新銀行）、`web-tw-pay.line.me`
* **地理位置**：`cwa.linebank.com.tw`（`210.208.97.97`，AS9924 台灣固網）、`img.linebank.com.tw`（`122.147.229.226`，AS9919 新世紀資通）、`tsln.taishinbank.com.tw`（`203.74.220.1`，AS131660 台新銀行），均**台灣**；`line-img.linebank.com.tw` 經 Cloudflare（美國節點）
* **角色**：LINE Bank 網路銀行與 LINE Pay 台灣支付
* **資料特性**：金融與帳戶資料；LINE Bank 為台灣持照銀行（連線商業銀行，台新銀行為主要股東），**金融主機在台灣境內**
* **DNS 解析結果**：

| 網域 | 解析 IP | ASN | 國家 |
|------|---------|-----|------|
| cwa.linebank.com.tw | 210.208.97.97 | AS9924 | TW |
| img.linebank.com.tw | 122.147.229.226 | AS9919 | TW |
| tsln.taishinbank.com.tw | 203.74.220.1 | AS131660 | TW |

AS9924 台灣固網；AS9919 新世紀資通；AS131660 台新銀行（Taishin）。

### 5. 廣告與追蹤第三方（Google／Moloco／Dable／Meta／微軟／Adjust）⚠️

> **歸屬提醒**：LINE 於 Today 新聞、VOOM、購物等內容場景整合多家廣告與內容推薦服務；此類會傳送瀏覽行為、廣告識別與裝置資訊至海外第三方。

* **Google 廣告**：`ad.doubleclick.net`、`pagead2.googlesyndication.com`、`*.safeframe.googlesyndication.com`、`adservice.google.com`、`www.googleadservices.com`、`stats.g.doubleclick.net`、`imasdk.googleapis.com`、`s0.2mdn.net`、`fundingchoicesmessages.google.com`、`ep1/ep2.adtrafficquality.google`（AS15169，多在美國）
* **Moloco（廣告 DSP）**：`eventfnt-asia.dsp-api.moloco.com`（Google Cloud 美國）、`cdn-f.adsmoloco.com`（Fastly 美國）
* **Dable（內容推薦，韓國）**：`api.dable.io`（**AWS 首爾／韓國**）、`static.dable.io`、`images.dable.io`（Akamai）
* **Meta**：`connect.facebook.net`、`scontent.xx.fbcdn.net`、`star-mini.c10r.facebook.com`（AS32934）
* **微軟 Bing**：`bat.bing.com`（AS8075，廣告轉換追蹤）
* **Adjust**：`view.adjust.com`（AS61273，歸因追蹤，美國）
* **Twitter**：`platform.twitter.com`（嵌入，Fastly 香港節點）
* **資料特性**：屬**非核心之廣告與跨站追蹤**，傳送使用行為與識別資訊；營運商多為海外
* **DNS 解析結果**（代表值）：

| 網域 | 解析 IP | ASN | 國家 |
|------|---------|-----|------|
| api.dable.io | 13.124.109.155 | AS16509（AWS 首爾） | KR |
| eventfnt-asia.dsp-api.moloco.com | 107.178.244.18 | AS396982（GCP） | US |
| view.adjust.com | 185.151.204.50 | AS61273（Adjust） | US |
| bat.bing.com | 150.171.27.10 | AS8075（Microsoft） | US |
| ad.doubleclick.net | 173.194.174.148 | AS15169 | US |

### 6. 分析／監控／安全（Firebase、Sentry、V-Key）

* **域名性質**：`firebase.googleapis.com`、`www.google-analytics.com`、`www.googletagmanager.com`（Google 分析）、`ly.my.sentry.io`／`sentry-prem.line-apps.com`（Sentry 錯誤監控）、`1176-ti.cloud.v-key.com`（V-Key 行動安全 SDK）
* **地理位置**：`ly.my.sentry.io`（Google Cloud 美國）、`1176-ti.cloud.v-key.com`（Azure 美國，AS8075）
* **角色**：使用分析、App 當機／錯誤回報、App 完整性／威脅防護（V-Key）
* **資料特性**：遙測與安全檢測資料；營運商為海外
* **DNS 解析結果**（代表值）：

| 網域 | 解析 IP | ASN | 國家 |
|------|---------|-----|------|
| ly.my.sentry.io | 34.111.57.229 | AS396982（GCP） | US |
| 1176-ti.cloud.v-key.com | 40.90.184.244 | AS8075（Azure） | US |

### 7. iOS 系統背景與泛用 CDN（部分歸屬未定）

SRTT 另擷取到數個 Akamai 泛用對應網域（`e1102.a.akamaiedge.net`、`a274.dscv.akamai.net`、`a1433.d.akamai.net`、`a.line.me.akadns.net` 等），多為 LINE 內容經 Akamai 傳遞之基礎設施；`mpc-prod-…run.app`（Google Cloud Run）、`d1r9h2320m2w6r.cloudfront.net`（AWS）為後端／CDN 端點。此類為傳輸基礎設施，不單獨判定。

---

## API 用途整理

> **說明**：本次為瀏覽式測試，觀察到之請求以頁面／資料載入（GET）為主，故此節依「服務／角色」整理，不列具體端點與請求參數。實際通訊、金融交易之流程未於本次深入觸發。

### 一、通訊與帳號核心（LY Corporation，日本）

即時通訊訊息傳輸經 `legy.line-apps.com`（LEGY 閘道），登入與 API 經 `access.line.me`／`api.line.me`。核心通訊與帳號資料由日本 LY Corporation 處理。

### 二、內容與電商（Today／VOOM／貼圖／購物／點數／音樂／旅遊）

各服務入口為 `*.line.me`，靜態資源經 `line-scdn.net`（Akamai／AWS）。新聞內容另有聯合報系 `linetoday.edh.tw`。

### 三、台灣金融（LINE Bank／Pay）

`linebank.com.tw`、`taishinbank.com.tw`、`web-tw-pay.line.me` 承載網路銀行與支付，主機在台灣。

### 四、廣告、追蹤與監控

內容場景整合 Google 廣告、Moloco、Dable（韓）、Meta、Bing、Adjust 等廣告與追蹤，以及 Firebase／Sentry 分析監控、V-Key 安全 SDK。

---

## 請求流程概觀

```
App 啟動 / 登入
  ├─→ 核心傳輸閘道 LEGY                 (legy.line-apps.com ← 日本 LY Corp)
  ├─→ 登入 / API                        (access.line.me / api.line.me ← Akamai)
  └─→ 分析 / 監控 / 安全                 (firebase, google-analytics, sentry.io, v-key.com ← 美國)

各服務
  ├─ 聊天 / 貼圖 → 原站 + 靜態 CDN       (line.me + line-scdn.net：日/台/美節點)
  ├─ Today 新聞 → 內容 + 聯合報系        (today.line.me + linetoday.edh.tw)
  ├─ VOOM 短影音 → 影音 OBS              (voom-obs.line-scdn.net)
  ├─ 購物 / 點數 / 音樂 / 旅遊 / 發票 →   (*.line.me ← 多為日本原站)
  └─ LINE Bank / Pay → 台灣金融          (linebank.com.tw / taishinbank.com.tw ← 台灣)

內容場景內嵌廣告與追蹤（非核心）
  ├─ Google 廣告                         (doubleclick, googlesyndication, adtrafficquality ← 美)
  ├─ Moloco 廣告 DSP                     (dsp-api.moloco.com, adsmoloco.com ← 美)
  ├─ Dable 內容推薦                      (api.dable.io ← 韓國)
  ├─ Meta / Twitter / Bing               (facebook, platform.twitter.com, bat.bing.com)
  └─ Adjust 歸因                         (view.adjust.com ← 美)
```

---

## 摘要

| 分類 | 網域 | 是否核心功能 | 連線節點 | 資料是否出境 |
|------|------|--------------|----------|--------------|
| LINE 核心通訊 | `*.line-apps.com`、`*.line.me` | 是 | **日本**（LY Corp）／CDN | **是（日本，平台本質）** |
| LINE 內容 CDN | `line-scdn.net` | 是（輔助） | 台灣／美國（Akamai／AWS） | 可能 |
| LINE Bank／Pay | `linebank.com.tw`、`taishinbank.com.tw` | 是 | 台灣 | 否 |
| 廣告與追蹤 | Google／Moloco／Dable／Meta／Bing／Adjust | 否 | 美國／**韓國**／台灣 | 是／可能 |
| 分析／監控／安全 | Firebase／Sentry／V-Key | 否 | 美國 | 是 |

LINE 是**日本 LY Corporation** 營運之外商超級 App，其**核心通訊與帳號資料本即由日本公司處理**（`AS38631`，日本）——這是使用 LINE 的**平台本質**，而非隱蔽外洩：選擇使用 LINE，即等同將通訊與社群資料交由日本業者處理。內容與靜態資源透過 Akamai／AWS 分布於台灣、美國等節點。

**LINE Bank／LINE Pay 之台灣金融服務**（連線商業銀行、台新銀行）主機在**台灣境內**，金融資料受國內監理，此部分與外商通訊服務分屬不同體系。

LINE 作為超級 App 整合了**相當廣泛的第三方廣告與追蹤生態**——Google 廣告全家桶、Moloco 廣告 DSP、**韓國 Dable 內容推薦**、Meta、Bing、Adjust 歸因，以及 Firebase／Sentry 監控與 V-Key 安全 SDK。這些多在 Today 新聞、VOOM、購物等內容場景載入，會將使用行為與識別資訊傳送至美國、韓國等地之第三方。

> **隱私風險評估**：使用 LINE 的核心前提，是接受**通訊／社群資料由日本業者處理**（平台本質，非漏洞）。相對地，**LINE Bank／Pay 的台灣金融資料留在境內**，受國內監理。較需個別留意者為**廣泛的第三方廣告與跨站追蹤**（含韓國 Dable、多家美國廣告網），其傳送之瀏覽行為與識別資訊範圍較廣。惟本次為**全裝置抓包、未逐一深入各功能、且無 HTTP 內容**，個別廣告／追蹤之確切歸屬與傳輸內容尚無法逐一確認；建議以 iOS「App 隱私權報告」按 App 檢視，並針對特定功能加測 HTTP 明細以完整評估。
