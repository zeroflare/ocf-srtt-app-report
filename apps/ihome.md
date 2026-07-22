---
title: 智生活
---

# 智生活 App 網路流量分析報告

## 概述

<img src="https://is1-ssl.mzstatic.com/image/thumb/Purple221/v4/28/46/45/284645db-eff5-e79c-1826-20f73995a345/AppIcon-1x_U007emarketing-0-11-0-0-0-85-220-0.png/512x512bb.jpg" alt="智生活 App 圖示" width="150" height="150" style="border-radius: 22%; object-fit: cover; box-shadow: 0 4px 12px rgba(0, 0, 0, 0.18);">

本報告依據 SRTT 與分包截取工具分析**智生活**（`chp.kingnet.ios`）App 的網路請求。此 App 由 **SmaDay Technology Co., Ltd.（今網智慧／智生活）**提供，為**智慧社區生活平台**，功能包含社區公告、包裹與訪客通知、繳費、社群、生活資訊等，涉及**住戶身分與社區居住資料**。

本報告有兩項顯著發現：(1) 智生活雖為台灣品牌、處理社區住戶資料，但其**自家後端完全託管於 Google Cloud 公有雲（非台灣自建機房）**；(2) 本 App 整合了**本系列中最龐大的第三方追蹤堆疊**——含 **Microsoft Clarity（行為側錄／session 側錄）**、**Smadex（程式化廣告 DSP）**、Google／Facebook 廣告與分析、Bing、Infobip（英國）簡訊等。

| 項目 | 內容 |
|------|------|
| App 名稱 | 智生活 |
| App 版本 | 4.17.1 |
| 裝置 | iPhone 16 Pro / iOS 26.5.2 |
| 擷取時間 | 2026-07-22 |
| 涉及網域 | 約 40 個（App 相關；另有 iOS 系統背景已排除） |

> **測試方式與歸屬說明**：本次為功能點選瀏覽之測試。本報告以**網域與資料流向**為分析主軸；HTTP 端點層級之細節不在本次擷取範圍內。因 SRTT／MITM 為**全裝置**抓包，部分廣告／追蹤網域之確切歸屬（App 本體或內嵌內容）建議另以 iOS「App 隱私權報告」佐證。SRTT 未擷取到之 Stream 網域，已另以 DNS／Cymru 補齊。

---

## 網域分析

依**所屬單位／角色**分為六類。iOS 系統層背景網域已於文末排除。

| 類別 | 代表網域 | 所屬／營運 | 節點國家 | ASN | 用途 |
|------|----------|-----------|----------|-----|------|
| 自家後端（Google Cloud）⚠️ | `ecs`／`member-api`／`chatapi`／`notification`／`vip-subscription`／`ad`／`cdn`／`file.kingnetsmart.com.tw`、`api`／`access`／`urec.smartdaily.com.tw`、`www.dailypro.com.tw`、`link.knst.tw` | SmaDay／今網智慧（託管於 Google Cloud） | 美國（GCP，IP 註冊） | AS396982 | 會員、聊天、通知、訂閱、廣告、內容 API |
| 台灣節點 | `img.smartdaily.com.tw` | SmaDay（HiNet） | 台灣 | AS3462 | 圖片資源 |
| Firebase／推播 | `smartlife-3d231.firebaseio.com`、`firebaseremoteconfig`／`firebaseinstallations`／`fcmtoken`／`crashlyticsreports-pa.googleapis.com` | Google（Firebase） | 美國 | AS396982 / AS15169 | 即時資料庫、設定、推播、當機回報 |
| 行為側錄 ⚠️ | `t.clarity.ms`、`c.clarity.ms`、`scripts.clarity.ms`、`www.clarity.ms` | Microsoft（Clarity） | 美國／新加坡 | AS8075 | 使用者 session 側錄／熱區分析 |
| 廣告與追蹤 ⚠️ | `*.smadex.com`、`securepubads`／`pagead2.googlesyndication`／`doubleclick`／`adtrafficquality`、`c.bing.com`、`connect.facebook.net`／`graph.facebook.com`、`analytics.google.com`／`googletagmanager` | Smadex／Google／微軟／Meta | 美國／台灣 | AS14618 / AS15169 / AS8075 / AS32934 | 程式化廣告、廣告聯播、分析 |
| 簡訊／監控 | `mobile.infobip.com`、`o4504…ingest.sentry.io` | Infobip（英國）／Sentry | 英國／美國 | AS43009 / AS396982 | 簡訊／OTP、錯誤監控 |

> **國家判定說明**：智生活自家網域（`*.kingnetsmart.com.tw`、`api`／`access`／`urec.smartdaily.com.tw`、`www.dailypro.com.tw`、`link.knst.tw`）ASN 均為 **AS396982（Google Cloud Platform）**，連線 IP（`34.x`／`35.x`）**註冊於美國**——即**託管於 Google 公有雲、非台灣自建機房**（GCP 全球負載平衡 IP 雖可能路由至亞洲區域，惟營運於 Google 公有雲）。唯一台灣節點為 `img.smartdaily.com.tw`（AS3462 HiNet）。第三方中，**Microsoft Clarity（`clarity.ms`，AS8075）位於美國／新加坡**、**Smadex（`smadex.com`）位於美國**、**Infobip（`infobip.com`，AS43009）位於英國**，其餘 Google／Meta 採 Anycast，營運商為海外。

### 1. 自家後端（SmaDay／今網智慧，託管於 Google Cloud 美國）⚠️

* **域名性質**：`ecs`／`member-api`（會員）／`chatapi`（聊天）／`notification`（通知）／`vip-subscription`（訂閱）／`ad`（廣告）／`cdn`／`file.kingnetsmart.com.tw`、`api`／`access`／`urec.smartdaily.com.tw`、`www.dailypro.com.tw`、`link.knst.tw`、`maintenance-mqtt-message-provider.kingnetsmart.com.tw`（MQTT 訊息）
* **地理位置**：解析至 **AS396982（Google Cloud）**，IP `34.49.98.62`／`34.120.x`／`35.186.x` 等，**註冊美國**
* **基礎設施**：**Google Cloud 公有雲**（非台灣自建機房）
* **角色**：智生活之核心後端——會員身分、聊天、社區通知、訂閱、廣告、內容 API、即時訊息（MQTT）
* **資料特性**：承載**住戶身分與社區居住資料**（會員、社區、繳費、聊天等）；主機在 Google 公有雲，屬雲端託管與資料出境路徑
* **DNS 解析結果**（代表值）：

| 網域 | 解析 IP | ASN | 國家 |
|------|---------|-----|------|
| ecs.kingnetsmart.com.tw | 34.49.98.62 | AS396982（GCP） | US |
| member-api.kingnetsmart.com.tw | 34.49.98.62 | AS396982（GCP） | US |
| chatapi.kingnetsmart.com.tw | 34.49.98.62 | AS396982（GCP） | US |
| api.smartdaily.com.tw | 34.49.98.62 | AS396982（GCP） | US |
| img.smartdaily.com.tw | 59.125.4.147 | AS3462（HiNet） | TW |

### 2. Firebase 與推播（Google）

* **域名性質**：`smartlife-3d231.firebaseio.com`（Firebase 即時資料庫）、`firebaseremoteconfig`／`firebaseinstallations`／`fcmtoken`／`device-provisioning`／`crashlyticsreports-pa.googleapis.com`
* **地理位置**：Firebase 即時資料庫（AS396982 GCP，美國）、其餘 googleapis（AS15169）
* **角色**：即時資料同步、遠端設定、FCM 推播、Crashlytics 當機回報
* **資料特性**：即時資料與遙測；營運商 Google
* **DNS 解析結果**（代表值）：

| 網域 | 解析 IP | ASN | 國家 |
|------|---------|-----|------|
| smartlife-3d231.firebaseio.com | 34.120.160.131 | AS396982（GCP） | US |
| fcmtoken.googleapis.com | 172.217.112.4 | AS15169 | US |

### 3. 行為側錄：Microsoft Clarity（美國）⚠️

* **域名性質**：`t.clarity.ms`、`c.clarity.ms`、`scripts.clarity.ms`、`www.clarity.ms`——Microsoft Clarity 之**使用者行為側錄（session recording／replay）與熱區分析**
* **地理位置**：AS8075（Microsoft），節點美國／新加坡
* **角色**：記錄使用者於 App／WebView 內之操作行為（點擊、捲動、停留等）以供分析
* **資料特性**：**session 側錄屬較高強度之行為追蹤**，可重播使用者操作；營運商 Microsoft 海外
* **DNS 解析結果**：

| 網域 | 解析 IP | ASN | 國家 |
|------|---------|-----|------|
| t.clarity.ms | 20.80.37.232 | AS8075 | US |
| scripts.clarity.ms | 150.171.110.70 | AS8075 | US |

### 4. 廣告與追蹤（Smadex／Google／Bing／Meta）⚠️

* **域名性質**：**Smadex**（`static-content-1`／`pixel-ed`／`br-trk`／`creatives.smadex.com`，程式化行動廣告 DSP）、Google 廣告（`securepubads.g.doubleclick.net`、`pagead2/tpc.googlesyndication.com`、`ep1/ep2.adtrafficquality.google`、`stats.g.doubleclick.net`、`safeframe`）、Bing（`c.bing.com`）、Meta（`connect.facebook.net`、`graph.facebook.com`）、Google 分析（`analytics.google.com`、`www.googletagmanager.com`）
* **地理位置**：Smadex（AS14618 AWS 美國）、Google（AS15169）、Microsoft（AS8075）、Meta（AS32934）
* **角色**：程式化廣告投放、廣告聯播、轉換追蹤與使用分析
* **資料特性**：**非核心之廣告與跨站追蹤**，傳送使用行為、廣告識別與裝置資訊至海外多家第三方
* **DNS 解析結果**（代表值）：

| 網域 | 解析 IP | ASN | 國家 |
|------|---------|-----|------|
| br-trk.smadex.com | 13.219.29.64 | AS14618（AWS） | US |
| securepubads.g.doubleclick.net | 142.250.192.130 | AS15169 | US |
| c.bing.com | 150.171.27.10 | AS8075 | US |

### 5. 簡訊與錯誤監控（Infobip／Sentry）

* **域名性質**：`mobile.infobip.com`（Infobip 全通路簡訊／OTP 平台，英國）、`o4504591611002880.ingest.sentry.io`（Sentry 錯誤監控）
* **地理位置**：Infobip（AS43009，**英國 GB**）、Sentry（AS396982 GCP，美國）
* **角色**：簡訊／一次性密碼（OTP）發送、App 錯誤回報
* **資料特性**：OTP 涉及手機門號；錯誤遙測
* **DNS 解析結果**：

| 網域 | 解析 IP | ASN | 國家 |
|------|---------|-----|------|
| mobile.infobip.com | 62.140.31.164 | AS43009（Infobip） | GB |
| o4504591611002880.ingest.sentry.io | 34.160.81.0 | AS396982（GCP） | US |

### 6. iOS 系統背景網域（非 App 業務流量，已排除）

`gsa.apple.com`、`gsas.apple.com`、`appleid.cdn-apple.com`（Apple ID／系統，Apple／Akamai）。

---

## API 用途整理

> **說明**：本次為瀏覽式測試，觀察到之請求以頁面／資料載入（GET）為主，故此節依「網域／功能角色」整理，不列具體端點與請求參數。

### 一、社區與會員（`*.kingnetsmart.com.tw`、`*.smartdaily.com.tw`）

會員（`member-api`）、聊天（`chatapi`）、通知（`notification`）、訂閱（`vip-subscription`）、即時訊息（`maintenance-mqtt`）、內容（`smartdaily`／`dailypro`）等，**均託管於 Google Cloud 公有雲（美國註冊 IP）**。

### 二、即時資料與推播（Firebase／FCM）

Firebase 即時資料庫（`firebaseio.com`）與 FCM 推播。

### 三、行為側錄、廣告與追蹤（Clarity／Smadex／Google／Meta／Bing）

Microsoft Clarity 進行 session 側錄；Smadex 程式化廣告；Google／Meta／Bing 廣告與分析。

### 四、簡訊與監控（Infobip／Sentry）

Infobip（英國）發送簡訊／OTP；Sentry 錯誤監控。

---

## 請求流程概觀

```
App 啟動 / 登入
  ├─→ 會員 / 認證                        (member-api.kingnetsmart.com.tw ← Google Cloud 美國)
  ├─→ OTP 簡訊                           (mobile.infobip.com ← 英國)
  ├─→ Firebase 即時資料 / 遠端設定        (firebaseio.com, firebaseremoteconfig)
  ├─→ FCM 推播                           (fcmtoken, device-provisioning.googleapis.com)
  └─→ 錯誤監控                           (sentry.io)

社區功能（公告 / 通知 / 聊天 / 繳費 / 內容）
  ├─→ 核心 API                           (ecs / chatapi / notification.kingnetsmart.com.tw ← GCP 美國)
  ├─→ 即時訊息 MQTT                      (maintenance-mqtt…kingnetsmart.com.tw)
  └─→ 內容 / 圖片                        (smartdaily.com.tw, dailypro.com.tw；img 為 HiNet 台灣)

行為側錄 + 廣告追蹤（非核心，較重）⚠️
  ├─→ Microsoft Clarity（session 側錄）   (t/c/scripts.clarity.ms ← 美國)
  ├─→ Smadex 程式化廣告                   (*.smadex.com ← 美國)
  ├─→ Google / Bing 廣告 + 分析           (doubleclick, googlesyndication, c.bing.com)
  └─→ Facebook                            (connect.facebook.net, graph.facebook.com)
```

---

## 摘要

| 分類 | 網域 | 是否核心功能 | 連線節點 | 資料是否出境 |
|------|------|--------------|----------|--------------|
| 自家後端 | `*.kingnetsmart.com.tw`／`*.smartdaily.com.tw` | 是 | **Google Cloud（美國註冊 IP）** | **是（公有雲託管）** |
| Firebase／推播 | `firebaseio.com`／`googleapis.com` | 是（輔助） | 美國 | 是 |
| 行為側錄（Clarity） | `*.clarity.ms` | 否 | 美國／新加坡 | 是 |
| 廣告與追蹤 | Smadex／Google／Bing／Meta | 否 | 美國／台灣 | 是／可能 |
| 簡訊／監控 | `infobip.com`／`sentry.io` | 是（輔助）／否 | **英國**／美國 | 是 |

智生活是一款**智慧社區生活平台**，處理**住戶身分與社區居住資料**。與台灣政府／公營 App 將資料留在境內（GSN／HiNet）截然不同，智生活的**自家後端（會員、聊天、通知、訂閱、內容、MQTT）完全託管於 Google Cloud 公有雲（連線 IP 註冊美國）**，僅 `img.smartdaily.com.tw` 一個圖片網域在台灣 HiNet。

更值得注意的是，本 App 整合了**本系列迄今最龐大、最高強度的第三方追蹤堆疊**：**Microsoft Clarity 的 session 行為側錄**（可重播使用者操作）、**Smadex 程式化廣告 DSP**、Google／Bing／Facebook 之廣告與分析，以及 Infobip（英國）簡訊。對一款承載社區住戶資料的生活平台而言，此追蹤範圍相當可觀。

> **隱私風險評估**：智生活的風險輪廓與政府 App 相反——它是**台灣品牌、卻將社區住戶資料託管於境外公有雲（Google Cloud），並疊加高強度追蹤**。三項最需留意：(1) **自家後端在 Google 公有雲（美國註冊 IP），住戶／社區資料屬雲端託管與出境**；(2) **Microsoft Clarity 的 session 側錄**屬較強之行為追蹤；(3) **Smadex 等程式化廣告與 Google／Meta／Bing 追蹤**範圍廣泛。惟本次為全裝置抓包、未登入、無 HTTP 內容，個別追蹤之確切歸屬與傳輸內容尚無法逐一確認；建議以 iOS「App 隱私權報告」按 App 檢視，並針對登入與社區功能加測 HTTP 明細，以完整評估住戶資料之實際傳輸範圍。
