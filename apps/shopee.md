---
title: 蝦皮購物
---

# 蝦皮購物 App 網路流量分析報告

## 概述

<img src="https://is1-ssl.mzstatic.com/image/thumb/Purple211/v4/31/03/d0/3103d03a-a33a-77b1-7723-a1d7fcc2cf60/AppIcon-0-1x_U007emarketing-0-6-0-0-85-220-0.png/512x512bb.jpg" alt="蝦皮購物 App 圖示" width="150" height="150" style="border-radius: 22%; object-fit: cover; box-shadow: 0 4px 12px rgba(0, 0, 0, 0.18);">

本報告依據 SRTT 與分包截取工具分析**蝦皮購物**（`com.beeasy.shopee.tw`）App 的網路請求。此 App 由 **Shopee（母集團 Sea Limited，新加坡）**提供，為台灣主要電商平台，功能包含商城購物、直播、聊聊客服、ShopeePay 支付、蝦幣等。

**與 LINE、Google 地圖同屬外商服務**：蝦皮母集團為新加坡 Sea Group，且**自建網路（擁有專屬 ASN）**。本報告最顯著之發現是：雖為「蝦皮購物**台灣**」，但相當比例之功能網域（API、追蹤、設定、客服，**乃至 ShopeePay 支付**）實際解析至 **Shopee 新加坡（AS138341）**節點——資料流向新加坡屬平台本質。

| 項目 | 內容 |
|------|------|
| App 名稱 | 蝦皮購物 |
| App 版本 | 3.78.33.7.12.4（App Store 標示 3.79.x） |
| 裝置 | iPhone 16 Pro / iOS 26.5.2 |
| 擷取時間 | 2026-07-22 |
| 涉及網域 | 約 45 個（App 相關；另有 iOS 系統背景已排除） |

> **測試方式與歸屬說明**：本次為功能點選瀏覽之測試，未實際下單付款。本報告以**網域與資料流向**為分析主軸；HTTP 端點層級之細節不在本次擷取範圍內。蝦皮擁有專屬 ASN（**AS131623 Shopee Taiwan**、**AS138341 Shopee Singapore**），故可較明確判定各服務之實體網路歸屬。SRTT 未擷取到之 Stream 網域，已另以 DNS／Cymru 補齊。

---

## 網域分析

依**所屬服務／角色**分為六類。iOS 系統層背景網域已於文末排除。

| 類別 | 代表網域 | 所屬／營運 | 節點國家 | ASN | 用途 |
|------|----------|-----------|----------|-----|------|
| 購物商城核心 | `shopee.tw`、`mall.shopee.tw` | Shopee Taiwan | 台灣 | AS131623 | 商城首頁／商品 |
| 平台服務／API／追蹤 ⚠️ | `api.is.shopee.tw`、`ubt/ubta.tracking.shopee.tw`、`sv.shopee.tw`、`live.shopee.tw`、`data.cs.shopee.tw`、`help.shopee.tw`、`remote-config.gslb.sgw.shopeemobile.com` | **Shopee Singapore** | **新加坡** | AS138341 | API、行為追蹤、直播、設定、客服 |
| ShopeePay 支付 ⚠️ | `pay.shopee.sg`、`api.fe.shopeepay.tw` | **Shopee Singapore** | **新加坡** | AS138341 | 支付 |
| 內容 CDN | `deo.shopeemobile.com`、`cs.deo.shopeemobile.com`、`*.susercontent.com`、`deo.shopeesz.com` | Shopee（Akamai／AWS） | 台灣／美國 | AS20940 / AS16509 | 圖片、影音、靜態資源 |
| Garena（Sea 集團） | `content.garena.com`、`cdngarenanow-a.akamaihd.net` | Garena（Sea） | 台灣／美國（Akamai） | AS20940 / AS3462 | 內容／遊戲關聯 |
| 第三方追蹤 | `connect.facebook.net`、`analytics.google.com`、`ad.doubleclick.net`、`att-launches.appsflyersdk.com`、`crashlyticsreports-pa.googleapis.com` | Meta／Google／AppsFlyer | 台灣／美國 | AS32934 / AS15169 / AS16509 | 廣告、分析、歸因、當機回報 |

> **國家判定說明**：蝦皮**自建網路**，判定明確。商城首頁（`shopee.tw`、`mall.shopee.tw`）解析至 **AS131623（Shopee Taiwan）**，台灣（`103.117.4.x`）。**但大量功能網域**——API（`api.is.shopee.tw`）、行為追蹤（`ubt/ubta.tracking.shopee.tw`）、直播（`live.shopee.tw`）、設定（`remote-config…sgw.shopeemobile.com`）、客服（`help.shopee.tw`）、以及 **ShopeePay 支付（`pay.shopee.sg`、`api.fe.shopeepay.tw`）**——均解析至 **AS138341（Shopee Singapore），新加坡**（`147.136.x.x`、`103.115.x.x`）。內容 CDN 經 Akamai（AS20940）之台灣節點與 AWS（AS16509）之美國節點。第三方（Meta、Google、AppsFlyer）採 Anycast，營運商為海外。

### 1. 購物商城核心（Shopee Taiwan，台灣）

* **域名性質**：`shopee.tw`、`mall.shopee.tw`（商城首頁與商品頁）
* **地理位置**：解析至 `103.117.4.75`，**AS131623（Shopee Taiwan Co. Ltd.）**，台灣
* **角色**：商城瀏覽、商品展示
* **資料特性**：商品瀏覽；此類主入口在台灣節點
* **DNS 解析結果**：

| 網域 | 解析 IP | ASN | 國家 |
|------|---------|-----|------|
| shopee.tw | 103.117.4.75 | AS131623 | TW |
| mall.shopee.tw | 103.117.4.75 | AS131623 | TW |

### 2. 平台服務、API 與行為追蹤（Shopee Singapore，新加坡）⚠️

* **域名性質**：`api.is.shopee.tw`（API）、`ubt.tracking.shopee.tw`／`ubta.tracking.shopee.tw`／`apm.tracking.shopee.tw`（使用者行為追蹤 UBT／效能監控 APM）、`sv.shopee.tw`、`live.shopee.tw`（直播）、`data.cs.shopee.tw`、`patronus.idata.shopeemobile.com`（資料回報）、`remote-config.gslb.sgw.shopeemobile.com`（遠端設定）、`help.shopee.tw`、`chatbot.shopee.sg`（客服）
* **地理位置**：解析至 `147.136.x.x`／`143.92.x.x`，**AS138341（Shopee Singapore Private Limited）**，**新加坡**
* **角色**：App 之核心 API、**使用者行為與效能追蹤**、直播、遠端設定、客服
* **資料特性**：承載 App 運作與**大量使用行為追蹤資料**；實體網路在**新加坡**，屬資料出境（平台本質）
* **DNS 解析結果**（代表值）：

| 網域 | 解析 IP | ASN | 國家 |
|------|---------|-----|------|
| api.is.shopee.tw | 143.92.78.129 | AS138341 | SG |
| ubt.tracking.shopee.tw | 147.136.146.190 | AS138341 | SG |
| ubta.tracking.shopee.tw | 147.136.145.191 | AS138341 | SG |
| live.shopee.tw | 147.136.145.192 | AS138341 | SG |
| remote-config.gslb.sgw.shopeemobile.com | 143.92.88.140 | AS138341 | SG |

### 3. ShopeePay 支付（Shopee Singapore，新加坡）⚠️

* **域名性質**：`pay.shopee.sg`、`api.fe.shopeepay.tw`（ShopeePay 支付）
* **地理位置**：`pay.shopee.sg`（`103.115.76.16`）、`api.fe.shopeepay.tw`（`147.136.146.217`），均 **AS138341（Shopee Singapore），新加坡**
* **角色**：ShopeePay 電子支付
* **資料特性**：涉及付款資料；支付服務主機在**新加坡**（本次未實際付款）
* **DNS 解析結果**：

| 網域 | 解析 IP | ASN | 國家 |
|------|---------|-----|------|
| pay.shopee.sg | 103.115.76.16 | AS138341 | SG |
| api.fe.shopeepay.tw | 147.136.146.217 | AS138341 | SG |

### 4. 內容 CDN（Akamai／AWS）

* **域名性質**：`deo.shopeemobile.com`、`cs.deo.shopeemobile.com`、`deo.shopeesz.com`、`*.susercontent.com`（`down-*-tw.vod/img.susercontent.com`、`fileproxycdn.cs.susercontent.com`、`app-tw.deo.susercontent.com`）
* **地理位置**：Akamai（AS20940）台灣節點（`184.26.127.x`）與 AWS（AS16509）美國節點（`65.9.180.x`）
* **角色**：商品圖片、直播影音、App 靜態資源傳遞
* **資料特性**：靜態內容
* **DNS 解析結果**（代表值）：

| 網域 | 解析 IP | ASN | 國家 |
|------|---------|-----|------|
| deo.shopeemobile.com | 184.26.127.42 | AS20940（Akamai） | TW（節點） |
| fileproxycdn.cs.susercontent.com | 65.9.180.10 | AS16509（AWS） | US（節點） |

### 5. Garena（Sea 集團）

* **域名性質**：`content.garena.com`、`cdngarenanow-a.akamaihd.net`（Garena 為 Sea Group 之遊戲事業，與蝦皮同集團）
* **地理位置**：Akamai（AS20940／AS3462），台灣／美國節點
* **角色**：關聯內容／集團服務
* **DNS 解析結果**：

| 網域 | 解析 IP | ASN | 國家 |
|------|---------|-----|------|
| content.garena.com | 210.71.227.122 | AS20940 / AS3462 | TW（節點） |
| cdngarenanow-a.akamaihd.net | 23.51.25.83 | AS20940（Akamai） | US（節點） |

### 6. 第三方廣告與追蹤（Meta／Google／AppsFlyer）⚠️

* **域名性質**：Meta（`www.facebook.com`、`connect.facebook.net`、`graph.facebook.com`）、Google（`analytics.google.com`、`www.googletagmanager.com`、`ad.doubleclick.net`、`www.googleadservices.com`、`app-measurement.com`、`firebaselogging-pa`、`crashlyticsreports-pa.googleapis.com`、`firebaseremoteconfig.googleapis.com`）、AppsFlyer（`att-launches.appsflyersdk.com`，歸因追蹤）
* **地理位置**：Meta（AS32934）、Google（AS15169）台灣 Anycast 節點；AppsFlyer（AS16509 AWS 美國）
* **角色**：廣告、使用分析、行銷歸因、Firebase 設定與當機回報
* **資料特性**：非核心之第三方廣告與追蹤，傳送使用行為與識別資訊至海外
* **DNS 解析結果**（代表值）：

| 網域 | 解析 IP | ASN | 國家 |
|------|---------|-----|------|
| connect.facebook.net | 31.13.87.5 | AS32934 | TW（節點） |
| ad.doubleclick.net | 64.233.189.148 | AS15169 | US |
| att-launches.appsflyersdk.com | 3.169.201.72 | AS16509（AWS） | US |

### 7. iOS 系統背景網域（非 App 業務流量，已排除）

`amp-api.apps.apple.com`、`amp-api-edge.apps.apple.com`、`amp-api-search-edge.apps.apple.com`、`is1-ssl.mzstatic.com`、`apple.com`、`xp.apple.com`（Apple／Fastly／Akamai）。

---

## API 用途整理

> **說明**：本次為瀏覽式測試，觀察到之請求以頁面／資料載入（GET）為主，故此節依「服務／角色」整理，不列具體端點與請求參數。實際下單／付款流程未於本次觸發。

### 一、商城與 API（`shopee.tw`／`api.is.shopee.tw`）

商城首頁在 Shopee Taiwan（台灣）；核心 API 走 Shopee Singapore（新加坡）。

### 二、行為追蹤（`ubt/ubta/apm.tracking.shopee.tw`）

蝦皮自有之使用者行為追蹤（UBT）與效能監控（APM），主機在**新加坡**。

### 三、支付（ShopeePay，`pay.shopee.sg`）

ShopeePay 支付主機在**新加坡**。

### 四、第三方（Meta／Google／AppsFlyer）

廣告、分析、行銷歸因與 Firebase／Crashlytics，傳送至海外第三方。

---

## 請求流程概觀

```
App 啟動 / 商城首頁
  ├─→ 商城 / 商品                        (shopee.tw, mall.shopee.tw ← Shopee Taiwan 台灣)
  ├─→ 核心 API / 遠端設定                (api.is.shopee.tw, remote-config…sgw ← Shopee 新加坡)
  ├─→ 行為追蹤 UBT / 效能 APM ⚠️          (ubt/ubta/apm.tracking.shopee.tw ← 新加坡)
  ├─→ 圖片 / 影音 CDN                    (deo.shopeemobile.com, susercontent.com ← Akamai/AWS)
  └─→ 第三方分析 / 歸因                  (facebook, google-analytics, appsflyer, crashlytics)

購物 → 付款
  └─ ShopeePay ⚠️ →                      (pay.shopee.sg, api.fe.shopeepay.tw ← 新加坡)

其他：直播 live.shopee.tw、客服 help/chatbot、Garena 集團內容
```

---

## 摘要

| 分類 | 網域 | 是否核心功能 | 連線節點 | 資料是否出境 |
|------|------|--------------|----------|--------------|
| 購物商城核心 | `shopee.tw`、`mall.shopee.tw` | 是 | 台灣（Shopee Taiwan） | 否 |
| 平台 API／追蹤 | `api.is`／`ubt.tracking.shopee.tw` 等 | 是 | **新加坡（Shopee SG）** | **是** |
| ShopeePay 支付 | `pay.shopee.sg`、`api.fe.shopeepay.tw` | 是 | **新加坡** | **是** |
| 內容 CDN | `shopeemobile.com`、`susercontent.com` | 是（輔助） | 台灣／美國 | 可能 |
| 第三方追蹤 | Meta／Google／AppsFlyer | 否 | 台灣／美國 | 是／可能 |

蝦皮購物為**新加坡 Sea Group** 之外商電商，且**自建網路**（專屬 ASN，判定明確）。其**商城首頁**（`shopee.tw`）在 **Shopee Taiwan（台灣，AS131623）**；但**大量核心功能**——API、**使用者行為追蹤（UBT／APM）**、直播、遠端設定、客服，**乃至 ShopeePay 支付**——均實際解析至 **Shopee Singapore（新加坡，AS138341）**。換言之，台灣使用者的**行為追蹤與付款資料，路由至新加坡的蝦皮基礎設施**，此為使用蝦皮（外商平台）之本質。此外亦整合 Meta、Google、AppsFlyer 等第三方廣告與追蹤。

> **隱私風險評估**：使用蝦皮的前提，是接受**行為資料與交易（含 ShopeePay 付款）由新加坡 Sea Group 之基礎設施處理**——這是外商電商平台的本質，非隱蔽外洩。相較台灣本土電商，蝦皮的資料流向以**新加坡**為主軸；其自有之行為追蹤（UBT）範圍廣泛，並疊加 Meta／Google／AppsFlyer 等第三方追蹤。本次為未下單之瀏覽式測試，實際下單、付款與聊聊時之個資（收件人、地址、付款、對話）傳輸內容與加密方式，建議後續加測 HTTP 明細以完整評估。
