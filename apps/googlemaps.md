---
title: Google 地圖
---

# Google 地圖 App 網路流量分析報告

## 概述

<img src="https://is1-ssl.mzstatic.com/image/thumb/Purple211/v4/f7/b9/65/f7b965a9-44c6-4b9e-98b8-64112f6a9e7e/maps_2025-0-0-1x_U007epad-0-0-0-1-0-0-sRGB-0-0-85-220.png/512x512bb.jpg" alt="Google 地圖 App 圖示" width="150" height="150" style="border-radius: 22%; object-fit: cover; box-shadow: 0 4px 12px rgba(0, 0, 0, 0.18);">

本報告依據 SRTT 與分包截取工具分析 **Google 地圖**（`com.google.Maps`）App 的網路請求。此 App 由 **Google LLC（美國）**提供，為全球主流地圖與導航服務，功能包含地圖瀏覽、路線導航、街景、地點搜尋與評論、位置分享與時間軸（位置記錄）等。

**與外商通訊 App（如 LINE）性質相近**：Google 地圖為**外商（美國）**服務，其地圖、位置與帳號資料**本即由 Google 處理**——這是使用 Google 服務的**平台本質**。本報告最顯著之特徵是：**幾乎所有流量都指向 Google 自家基礎設施（AS15169），第三方極少**；資料流向單一但高度敏感（涉及位置與位置記錄）。

| 項目 | 內容 |
|------|------|
| App 名稱 | Google 地圖 |
| App 版本 | 26.29.1 |
| 裝置 | iPhone 16 Pro / iOS 26.5.2 |
| 擷取時間 | 2026-07-22 |
| 涉及網域 | 約 33 個（App 相關，幾乎全為 Google；另有 4 個 iOS 系統背景已排除） |

> **測試方式與歸屬說明**：本次為地圖瀏覽與功能點選之測試。本報告以**網域與資料流向**為分析主軸；HTTP 端點層級之細節不在本次擷取範圍內。SRTT 擷取共 200 筆記錄，其中 **172 筆（約 86%）為 Google（AS15169）**，其餘為 iOS App Store 系統背景。SRTT 未擷取到之 Stream 網域，性質相同（均為 `googleapis.com`／`gstatic.com`／`googleusercontent.com`／`googlevideo.com` 等 Google 網域）。

---

## 網域分析

Google 地圖之網域**幾乎全部隸屬 Google（AS15169）**，採 Anycast，連線節點多解析至美國，部分為台灣、澳洲節點。以下依**功能角色**分類。

| 類別 | 代表網域 | 所屬 | 節點國家 | ASN | 用途 |
|------|----------|------|----------|-----|------|
| 地圖核心 | `mobilemaps.googleapis.com`、`mobilemaps-pa-gz.googleapis.com`、`maps.google.com`、`maps.gstatic.com` | Google | 美國／台灣（Anycast） | AS15169 | 地圖圖磚與向量資料 |
| 位置與記錄 ⚠️ | `locationhistory-pa.googleapis.com`、`streetviewpixels-pa.googleapis.com`、`mapsphotoupload.googleapis.com` | Google | 美國 | AS15169 | 位置記錄（時間軸）、街景、相片上傳 |
| Google 帳號 | `accounts.google.com`、`oauth2.googleapis.com`、`oauthaccountmanager.googleapis.com`、`people-pa.googleapis.com` | Google | 美國／台灣 | AS15169 | 登入、帳號、聯絡人 |
| 使用者內容／圖片 | `lh3~lh6.googleusercontent.com`、`gz0.googleusercontent.com`、`googlehosted.l.googleusercontent.com` | Google | 美國／台灣 | AS15169 | 地點相片、使用者內容 |
| 影音內容 | `rr*.googlevideo.com`、`manifest.googlevideo.com` | Google | 美國（Anycast） | AS15169 | 影片內容傳遞 |
| 服務與遙測 | `growth-pa`、`notifications-pa`、`feedback-pa.googleapis.com`、`play.googleapis.com`、`app-analytics-services.com` | Google | 美國 | AS15169 | 推播、回饋、分析、Play 服務 |
| 廣告與字型 | `tpc.googlesyndication.com`、`fonts.gstatic.com`、`www.gstatic.com`、`ssl.gstatic.com` | Google | 美國／台灣 | AS15169 | Google 廣告、字型與靜態資源 |

> **國家判定說明**：所有上述網域 ASN 均為 **AS15169（Google LLC）**，採 Anycast。SRTT 記錄中連線節點以**美國**為主（174 筆），部分解析至**台灣**（11 筆）與澳洲（15 筆，Google Anycast 常見之地理定位）。因營運商為 Google（美國），資料出境記「是（Google 美國）」，屬使用外商服務之本質。

### 1. 地圖核心與位置資料（Google，美國）⚠️

* **域名性質**：`mobilemaps.googleapis.com`／`mobilemaps-pa-gz.googleapis.com`（行動地圖向量資料）、`maps.google.com`／`maps.gstatic.com`（地圖資源）、`streetviewpixels-pa.googleapis.com`（街景）、**`locationhistory-pa.googleapis.com`（位置記錄／時間軸）**、`mapsphotoupload.googleapis.com`（相片上傳）
* **地理位置**：AS15169（Google），節點多在美國，部分台灣 Anycast
* **角色**：地圖繪製、導航、街景、地點相片，以及**位置記錄（Location History／時間軸）**
* **資料特性**：涉及**高度敏感之位置資料**——即時定位、移動軌跡、地點搜尋；`locationhistory-pa` 更關聯到帳號層級之長期位置記錄。此類資料由 Google（美國）處理，屬使用 Google 地圖之本質
* **DNS 解析結果**（代表值）：

| 網域 | 解析 IP | ASN | 國家 |
|------|---------|-----|------|
| streetviewpixels-pa.googleapis.com | 216.239.38.135 | AS15169 | US |
| locationhistory-pa.googleapis.com | 172.217.117.4 | AS15169 | US |
| maps.gstatic.com | 142.250.192.131 | AS15169 | TW（節點） |

### 2. Google 帳號與服務（Google，美國）

* **域名性質**：`accounts.google.com`、`oauth2.googleapis.com`、`oauthaccountmanager.googleapis.com`（登入）、`people-pa.googleapis.com`（聯絡人／個人資料）、`growth-pa`／`notifications-pa`／`feedback-pa.googleapis.com`、`play.googleapis.com`
* **角色**：Google 帳號登入與同步、推播、回饋、Play 服務
* **資料特性**：帳號識別與個人資料，關聯至 Google 帳號
* **DNS 解析結果**（代表值）：

| 網域 | 解析 IP | ASN | 國家 |
|------|---------|-----|------|
| accounts.google.com | 64.233.188.84 | AS15169 | TW（節點） |
| oauth2.googleapis.com | 142.251.170.95 | AS15169 | TW（節點） |
| people-pa.googleapis.com | 172.217.112.4 | AS15169 | US |

### 3. 內容、影音、廣告與字型（Google，美國）

* **域名性質**：`lh3~lh6.googleusercontent.com`／`gz0.googleusercontent.com`（地點相片與使用者內容）、`rr*.googlevideo.com`／`manifest.googlevideo.com`（影音內容）、`tpc.googlesyndication.com`（Google 廣告）、`fonts.gstatic.com`／`www.gstatic.com`／`ssl.gstatic.com`（字型與靜態資源）
* **角色**：地點相片、影片、廣告與靜態資源傳遞
* **資料特性**：內容傳遞與 Google 自家廣告；營運商均為 Google
* **DNS 解析結果**（代表值）：

| 網域 | 解析 IP | ASN | 國家 |
|------|---------|-----|------|
| lh5.googleusercontent.com | 142.250.204.33 | AS15169 | TW（節點） |
| manifest.googlevideo.com | 142.250.204.46 | AS15169 | US／TW |
| tpc.googlesyndication.com | 142.250.77.193 | AS15169 | TW（節點） |

### 4. iOS 系統背景網域（非 App 業務流量，已排除）

下列為 iOS App Store／系統背景流量，與 Google 地圖功能無關，於分析中排除：`amp-api.apps.apple.com`、`is1-ssl.mzstatic.com`、`play.itunes.apple.com`、`bag-cdn.itunes-apple.com.akadns.net`（Apple／Fastly／Akamai）。

---

## API 用途整理

> **說明**：本次為瀏覽式測試，觀察到之請求以頁面／資料載入（GET）為主，故此節依「功能角色」整理，不列具體端點與請求參數。

### 一、地圖與位置（`*-pa.googleapis.com`、`mobilemaps`）

地圖向量資料經 `mobilemaps.googleapis.com`；街景經 `streetviewpixels-pa`；**位置記錄（時間軸）經 `locationhistory-pa.googleapis.com`**；地點相片上傳經 `mapsphotoupload`。均為 Google 之 Private API（`-pa` 後綴），承載位置與地圖資料。

### 二、Google 帳號（`accounts.google.com`、`oauth*`）

登入與帳號同步透過 Google 帳號體系，資料關聯至使用者 Google 帳號。

### 三、內容與廣告（`googleusercontent`／`googlevideo`／`googlesyndication`）

地點相片、影片內容，以及 Google 自家廣告（`tpc.googlesyndication.com`）。

---

## 請求流程概觀

```
App 啟動 / 登入
  ├─→ Google 帳號登入                   (accounts.google.com, oauth2.googleapis.com)
  ├─→ 個人資料 / 聯絡人                  (people-pa.googleapis.com)
  └─→ 服務初始化 / 分析                  (growth-pa, notifications-pa, app-analytics-services)

地圖使用
  ├─ 地圖瀏覽 / 導航 → 向量資料          (mobilemaps.googleapis.com, maps.gstatic.com)
  ├─ 街景 →                              (streetviewpixels-pa.googleapis.com)
  ├─ 位置記錄（時間軸）→ ⚠️              (locationhistory-pa.googleapis.com)
  ├─ 地點相片 → 上傳 / 檢視              (mapsphotoupload / lh3~6.googleusercontent.com)
  └─ 影片 / 廣告 →                       (googlevideo.com, tpc.googlesyndication.com)

（所有連線之營運商均為 Google，AS15169，節點多在美國）
```

---

## 摘要

| 分類 | 網域 | 是否核心功能 | 連線節點 | 資料是否出境 |
|------|------|--------------|----------|--------------|
| 地圖核心與位置 | `mobilemaps`／`streetviewpixels`／`locationhistory-pa` | 是 | 美國／台灣（Google Anycast） | **是（Google 美國）** |
| Google 帳號 | `accounts.google.com`／`oauth*` | 是 | 美國／台灣 | 是（Google 美國） |
| 內容／影音／廣告 | `googleusercontent`／`googlevideo`／`googlesyndication` | 否／輔助 | 美國／台灣 | 是（Google 美國） |
| 字型／靜態 | `gstatic.com` | 否 | 美國／台灣 | 可能 |

Google 地圖是**單一營運商（Google，美國）**之外商服務——其最鮮明的特徵是**幾乎所有流量都指向 Google 自家基礎設施（AS15169），第三方極少**（僅見 Google 自家廣告 `googlesyndication`）。這與台灣政府／金融 App 的「多電信＋多第三方」結構截然不同：資料流向極為單一，但**全部交由 Google 處理**。

使用 Google 地圖的本質，是接受**位置、移動軌跡、地點搜尋與帳號資料由 Google（美國）處理**。其中 `locationhistory-pa.googleapis.com`（位置記錄／時間軸）尤其涉及**帳號層級之長期位置軌跡**，為最敏感之資料類型。連線節點雖部分在台灣（Google Anycast），營運商仍為 Google 美國。

> **隱私風險評估**：Google 地圖不存在「第三方資料外流」的複雜問題——它是**單一且集中的資料流向：全部到 Google**。風險本質不在「流向多少第三方」，而在於**使用者是否接受將高度敏感的位置與位置記錄交由外商（Google 美國）長期保存與運用**。此為使用 Google 服務之平台本質，非隱蔽外洩。若在意位置隱私，可於 Google 帳號設定中關閉「位置記錄／時間軸」與廣告個人化。本次為瀏覽式測試，實際傳輸之定位頻率與精度、位置記錄之上傳內容，建議後續加測 HTTP 明細以完整評估。
