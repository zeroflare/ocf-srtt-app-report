---
title: 健保快易通
---

# 健保快易通（全民健保行動快易通｜健康存摺）App 網路流量分析報告

## 概述

<img src="https://is1-ssl.mzstatic.com/image/thumb/Purple211/v4/c5/ab/8e/c5ab8ed6-7c1a-bcfb-7c0a-0bc64f52ee6c/AppIcon-0-0-1x_U007emarketing-0-11-0-0-85-220.png/512x512bb.jpg" alt="健保快易通 App 圖示" width="150" height="150" style="border-radius: 22%; object-fit: cover; box-shadow: 0 4px 12px rgba(0, 0, 0, 0.18);">

本報告依據 SRTT 與分包截取工具分析**全民健保行動快易通｜健康存摺**（`tw.gov.nhi.NHIAppPhone`）App 的網路請求。此 App 由**衛生福利部中央健康保險署**提供，是民眾查詢個人健保與醫療資料的主要入口，功能包含**健康存摺**（就醫及用藥紀錄、檢驗檢查結果、健檢報告、照護計畫）、**虛擬健保卡**、**健保櫃檯**、就醫院所查詢、友善就醫查詢等。

此 App 涉及**高度敏感之個人醫療資料**（就醫、用藥、檢驗、健檢紀錄）與**身分驗證**（虛擬健保卡、健保卡註冊、行動電話門號認證），故資料流向與存放位置為本報告重點。

| 項目 | 內容 |
|------|------|
| App 名稱 | 全民健保行動快易通｜健康存摺（健保快易通） |
| App 版本 | 3.1.17 |
| 裝置 | iPhone 16 Pro / iOS 26.5.2 |
| 擷取時間 | 2026-07-22 |
| 涉及網域 | 24 個（App 相關；另有 5 個 iOS 系統背景網域已排除） |

> **測試方式與資料範圍說明**：本次為「各功能點選瀏覽」之測試，觀察到之請求以頁面／資料載入（GET）為主，未逐一走完寫入型流程。本報告以**網域與資料流向**為分析主軸；HTTP 端點層級（method／path／請求參數／個資欄位／加密方式）之細節不在本次擷取範圍內。部分身分認證與健保署子網域僅出現於 Stream 截取清單、未出現在 SRTT DNS 結果，已另以 DNS／traceroute 補齊其 IP 與 ASN（均為台灣政府／電信／憑證機構）。

---

## 網域分析

以下依**所屬單位／角色**分為六類彙整。iOS 系統層背景網域（Apple 定位／推播／App Store）非 App 業務流量，已於文末另列並排除。

| 網域 | 所屬單位 | 國家 | 雲端 | ASN | 主要用途 |
|------|----------|------|------|-----|----------|
| `myhealthbank.nhi.gov.tw` | 健保署（GSN） | 台灣 | 否（政府網路） | AS4782 | 健康存摺（就醫用藥／檢驗紀錄） |
| `mhbdata2.nhi.gov.tw` | 健保署（GSN） | 台灣 | 否（政府網路） | AS4782 | 健康存摺資料 |
| `vhc.nhi.gov.tw` | 健保署（GSN） | 台灣 | 否（政府網路） | AS4782 | 虛擬健保卡（Virtual Health Card） |
| `med.nhi.gov.tw` | 健保署（GSN） | 台灣 | 否（政府網路） | AS4782 | 醫療／就醫相關服務 |
| `info.nhi.gov.tw` | 健保署（GSN） | 台灣 | 否（政府網路） | AS4782 | 健保資訊服務 |
| `cloudicweb.nhi.gov.tw` | 健保署（GSN） | 台灣 | 否（政府網路） | AS4782 | 健保卡網路服務註冊 |
| `etask.nhi.gov.tw` | 健保署（GSN） | 台灣 | 否（政府網路） | AS4782 | 健保署線上服務 |
| `www.nhi.gov.tw` | 健保署（公開網站） | 台灣（節點） | **是（Cloudflare）** | AS13335 | 健保署官方網站（公開資訊） |
| `mnogw.cht.com.tw` | 中華電信（行動） | 台灣 | 否（電信機房） | AS17421 | 行動電話門號認證 |
| `login2.fetnet.net` | 遠傳電信 | 台灣 | 否（電信機房） | AS9674 | 行動電話門號認證 |
| `mobileconnect.gtcloud.net.tw` | 關貿網路（代管於數位聯合 Seednet） | 台灣 | 否 | AS7482 | 行動身分識別／門號認證 |
| `mid.twca.com.tw` | 臺灣網路認證 TWCA | 台灣 | 否（CA 機房） | AS9924 | 行動身分識別（Mobile ID） |
| `rootocsp.twca.com.tw` | 臺灣網路認證 TWCA | 台灣 | 否（CA 機房） | AS9924 / AS3462 | 憑證狀態查詢（OCSP） |
| `twcasslocsp.twca.com.tw` | 臺灣網路認證 TWCA | 台灣 | 否（CA 機房） | AS9924 / AS3462 | SSL 憑證狀態查詢（OCSP） |
| `wmts.nlsc.gov.tw` | 內政部國土測繪中心（國網中心） | 台灣 | 否（政府機房） | AS7539 | 就醫院所地圖圖磚 |
| `api.tgos.tw` | 內政部 TGOS 地圖服務 | 台灣（節點） | **是（Google Cloud）** | AS396982 | 地圖／定位 API |
| `www.google-analytics.com` | Google | 台灣（節點） | 是 | AS15169 | 使用行為分析 |
| `firebaselogging-pa.googleapis.com` | Google（Firebase） | 台灣（節點） | 是 | AS15169 | Firebase 事件記錄 |
| `app-analytics-services.com` | Google | 台灣（節點） | 是 | AS15169 | App 使用分析 |
| `www.googletagmanager.com` | Google | 台灣（節點） | 是 | AS15169 | 代碼／追蹤管理 |
| `fcmtoken.googleapis.com` | Google（FCM） | 台灣（節點） | 是 | AS15169 | 推播 Token |
| `device-provisioning.googleapis.com` | Google（FCM） | 台灣（節點） | 是 | AS15169 | 推播裝置註冊 |
| `www.google.com` | Google | 台灣（節點） | 是 | AS15169 | Google 一般服務 |

> **國家判定說明**：健保署所有 `*.nhi.gov.tw` 服務網域（含 `myhealthbank`、`mhbdata2`、`vhc`、`med`、`info`、`cloudicweb`、`etask`）解析至 `210.69.215.x`，ASN **AS4782（GSN，政府服務網路）**，為政府實體機房，資料留在台灣境內。身分認證鏈之網域經 DNS／traceroute 確認，均為**台灣境內**電信與憑證機構：中華電信行動（`mnogw.cht.com.tw`，AS17421）、遠傳（`login2.fetnet.net`，AS9674）、關貿網路（`mobileconnect.gtcloud.net.tw`，代管於數位聯合 Seednet AS7482）、臺灣網路認證 TWCA（`mid`／`rootocsp`／`twcasslocsp.twca.com.tw`，AS9924／AS3462）。`www.nhi.gov.tw`（公開網站）與 `api.tgos.tw`（地圖 API）分別由 **Cloudflare（AS13335）**與 **Google Cloud（AS396982）**承載，採 Anycast，連線節點判定位於台灣，但**營運商為海外雲端**，出境記「可能」。Google 分析與推播服務（AS15169）同屬海外雲端。

### 1. 健保署核心健康資料（中央健康保險署，台灣，非公有雲）⭐ 敏感

* **域名性質**：`.nhi.gov.tw` 政府域名，為健保署健康存摺、虛擬健保卡等核心服務端點
* **地理位置**：`myhealthbank`／`mhbdata2`／`vhc`／`med`／`info.nhi.gov.tw` 解析至 `210.69.215.x`，ASN **AS4782（GSN，Data Communication Business Group）**，國家 **TW**
* **基礎設施**：非公有雲，政府服務網路（GSN）機房
* **角色**：App 核心後端，承載**健康存摺**（就醫及用藥紀錄、檢驗檢查結果、成人預防保健／癌症篩檢／健檢報告）、**虛擬健保卡**、照護計畫等
* **資料特性**：涉及**高度敏感之個人醫療資料**（就醫、處方用藥、檢驗數值、健檢結果、投保與身分資訊）。此類最敏感資料**均留在台灣政府網路（GSN）範圍內，未經公有雲、未出境**
* **DNS 解析結果**（A Record）：

| 網域 | 類型 | 解析 IP | ASN | 國家 |
|------|------|---------|-----|------|
| myhealthbank.nhi.gov.tw | A | 210.69.215.158 | AS4782 | TW |
| mhbdata2.nhi.gov.tw | A | 210.69.215.149 | AS4782 | TW |
| vhc.nhi.gov.tw | A | 210.69.215.143 | AS4782 | TW |
| med.nhi.gov.tw | A | 210.69.215.170 | AS4782 | TW |
| info.nhi.gov.tw | A | 210.69.215.152 | AS4782 | TW |
| cloudicweb.nhi.gov.tw | A | 210.69.215.202 | AS4782 | TW |
| etask.nhi.gov.tw | A | 210.69.215.166 | AS4782 | TW |

AS4782 為 GSN（政府服務網路）。`cloudicweb.nhi.gov.tw`（健保卡網路服務註冊）與 `etask.nhi.gov.tw` 經 DNS 確認亦解析至 `210.69.215.x`（AS4782 GSN，台灣），與其他健保署核心網域一致。

### 2. 健保署公開網站（Cloudflare，海外雲端）

* **域名性質**：`www.nhi.gov.tw` 為健保署官方網站首頁
* **地理位置**：CNAME 解析至 **Cloudflare（AS13335，`172.65.90.x`）**，連線節點判定位於台灣，營運商為 Cloudflare 海外
* **基礎設施**：Cloudflare 公有雲 CDN
* **角色**：載入健保署公開資訊頁面（消息、宣導等）
* **資料特性**：公開資訊網站，**不涉個人醫療資料**（個人資料走 §1 之 GSN 網域）；惟公開網站由海外 CDN 承載一節值得留意
* **DNS 解析結果**：

| 網域 | 類型 | 解析 IP | ASN | 國家 |
|------|------|---------|-----|------|
| www.nhi.gov.tw | CNAME→A | 172.65.90.66 / 172.65.90.67 | AS13335 | TW/US |

AS13335 為 Cloudflare, Inc.。

### 3. 身分認證鏈：電信門號 + 憑證（台灣電信／CA，非公有雲）

* **域名性質**：虛擬健保卡與健康存摺之**免插卡身分驗證**，透過「行動電話門號認證」與「行動身分識別」完成，涉及電信業者與憑證機構
* **組成**：
  * `mnogw.cht.com.tw`（中華電信）、`login2.fetnet.net`（遠傳電信）— **電信門號快速認證**（以 SIM／門號確認持有人身分）
  * `mobileconnect.gtcloud.net.tw`（關貿網路）— 行動身分識別／門號認證中介
  * `mid.twca.com.tw`（臺灣網路認證 TWCA）— 行動身分識別（Mobile ID）
  * `rootocsp.twca.com.tw`、`twcasslocsp.twca.com.tw`（TWCA）— 憑證狀態即時查詢（OCSP），驗證連線與身分憑證是否有效
* **地理位置**：網域擁有者均為**台灣**之電信業者（中華電信、遠傳）與憑證機構（TWCA）、加值服務商（關貿網路），推定機房位於台灣境內
* **基礎設施**：電信／CA 自建機房，非公有雲
* **角色**：完成免插卡之持卡人身分驗證，是虛擬健保卡與健康存摺登入的信任基礎
* **資料特性**：傳輸門號、裝置識別、憑證驗證等身分資訊；經 DNS／traceroute 確認，**所有認證主機之營運單位與 IP 均位於台灣境內**
* **DNS 解析結果**（A Record）：

| 網域 | 解析 IP | ASN | 所屬 | 國家 |
|------|---------|-----|------|------|
| mnogw.cht.com.tw | 211.79.35.179 | AS17421 | 中華電信（行動） | TW |
| login2.fetnet.net | 211.73.144.8 | AS9674 | 遠傳電信 | TW |
| mobileconnect.gtcloud.net.tw | 210.200.69.57 | AS7482 | 關貿網路（代管數位聯合 Seednet） | TW |
| mid.twca.com.tw | 219.87.64.184 / 60.250.3.152 | AS9924 / AS3462 | TWCA | TW |
| rootocsp.twca.com.tw | 219.87.64.165 / 60.250.3.135 | AS9924 / AS3462 | TWCA | TW |
| twcasslocsp.twca.com.tw | 219.87.64.165 / 60.250.3.135 | AS9924 / AS3462 | TWCA | TW |

AS17421 為中華電信行動業務；AS9674 為遠傳電信（Far EasTone）；AS7482 為數位聯合電信（Seednet，代管關貿服務）；AS9924 為台灣固網（Taiwan Fixed Network）；AS3462 為中華電信 HiNet。TWCA 之主機同時多重連線於 AS9924 與 AS3462，皆台灣。

### 4. 地圖服務（就醫院所查詢）

* **域名性質**：`wmts.nlsc.gov.tw` 為國土測繪中心地圖圖磚；`api.tgos.tw` 為內政部 TGOS 通用電子地圖／定位 API
* **地理位置**：`wmts.nlsc.gov.tw` → `140.110.x.x`，AS7539（國網中心，台灣）；`api.tgos.tw` → `34.160.0.116`，**AS396982（Google Cloud）**，連線節點判定台灣
* **基礎設施**：`wmts` 為政府機房；`api.tgos.tw` 由 Google Cloud 承載
* **角色**：「就醫院所查詢（地圖）」「友善就醫查詢」載入底圖與定位服務
* **資料特性**：圖磚與地圖查詢；`api.tgos.tw` 雖為政府地圖服務，但主機位於 Google Cloud 公有雲，屬海外營運
* **DNS 解析結果**：

| 網域 | 類型 | 解析 IP | ASN | 國家 |
|------|------|---------|-----|------|
| wmts.nlsc.gov.tw | A | 140.110.134.19 | AS7539 | TW |
| api.tgos.tw | A | 34.160.0.116 | AS396982 | TW |

AS7539 為國網中心（NCHC）；AS396982 為 Google Cloud Platform。

### 5. Google 分析與推播（Firebase／GA／FCM／Tag Manager，海外雲端）

* **域名性質**：使用分析（`www.google-analytics.com`、`app-analytics-services.com`、`www.googletagmanager.com`、`firebaselogging-pa.googleapis.com`）與推播（`fcmtoken.googleapis.com`、`device-provisioning.googleapis.com`）
* **地理位置**：ASN **AS15169（Google LLC）**，Anycast，連線節點判定台灣
* **基礎設施**：Google 公有雲
* **角色**：收集 App 使用行為事件、Firebase 記錄，以及推播通知 Token 註冊
* **資料特性**：傳送使用事件、裝置識別、推播 Token；**不含健康存摺醫療內容**，但作為政府健康類 App 內嵌 Google 分析／Firebase，會將使用行為傳送至海外第三方，此點值得檢視
* **DNS 解析結果**（A Record，代表值）：

| 網域 | 類型 | 解析 IP | ASN | 國家 |
|------|------|---------|-----|------|
| www.google-analytics.com | A | 216.239.32.178 | AS15169 | TW |
| firebaselogging-pa.googleapis.com | A | 172.217.112.4 | AS15169 | TW |

AS15169 為 Google LLC。`app-analytics-services.com`、`www.googletagmanager.com`、`fcmtoken.googleapis.com`、`device-provisioning.googleapis.com` 亦屬 Google（AS15169，Anycast，連線節點台灣，營運商海外）。

### 6. iOS 系統背景網域（非 App 業務流量，已排除）

下列為 iOS 系統／推播／App Store 背景流量，與健保快易通功能無關，於分析中排除，僅列出供辨識：`gateway.fe2.apple-dns.net`、`get-bx.g.aaplimg.com`、`gsp-ssl.ls.apple.com`、`gspe19-2-ssl.ls.apple.com`（Apple 定位／服務）、`a1556.dscapi9.akamai.net`（Apple 內容透過 Akamai CDN）。

---

## API 用途整理

> **說明**：本次為瀏覽式測試，觀察到之請求以頁面／資料載入（GET）為主，故此節依「網域／功能角色」整理，不列具體端點與請求參數。端點層級（含個資欄位、加密方式）之細節，留待後續針對特定功能（如健康存摺查詢、虛擬健保卡簽發）加測時補充。

### 一、健康存摺與虛擬健保卡（`*.nhi.gov.tw`）

「健康存摺」之就醫及用藥紀錄、就醫總覽、檢驗檢查結果（血糖／血脂／影像病理）、健康檢查報告等，向健保署 `myhealthbank.nhi.gov.tw`／`mhbdata2.nhi.gov.tw` 取得；「虛擬健保卡」走 `vhc.nhi.gov.tw`；健保卡網路服務註冊走 `cloudicweb.nhi.gov.tw`。此類為核心敏感資料，走台灣政府 GSN 網路，不出境。

### 二、身分認證（電信門號 + TWCA 憑證）

免插卡登入時，App 透過中華電信（`mnogw.cht.com.tw`）、遠傳（`login2.fetnet.net`）之門號認證，搭配關貿（`mobileconnect.gtcloud.net.tw`）與 TWCA（`mid.twca.com.tw`）之行動身分識別完成持卡人驗證；連線並向 TWCA OCSP（`rootocsp`／`twcasslocsp.twca.com.tw`）查詢憑證狀態。均為台灣境內機構。

### 三、地圖查詢（`wmts.nlsc.gov.tw`、`api.tgos.tw`）

「就醫院所查詢（地圖）」「友善就醫查詢」載入國土測繪中心圖磚與 TGOS 地圖／定位 API；其中 `api.tgos.tw` 主機位於 Google Cloud。

### 四、第三方分析與推播（Google）

App 內嵌 Google Analytics／Firebase／Tag Manager 收集使用行為，並透過 FCM（`fcmtoken`／`device-provisioning.googleapis.com`）註冊推播。屬非核心之第三方輔助服務，營運商為 Google 海外。

---

## 請求流程概觀

```
App 啟動 / 進入首頁
  ├─→ 載入公開網站內容                  (www.nhi.gov.tw ← Cloudflare)
  ├─→ Google Analytics / Firebase / TagManager  (google-analytics, firebaselogging, googletagmanager)
  └─→ FCM 推播註冊                      (fcmtoken / device-provisioning.googleapis.com)

登入 / 身分驗證（免插卡）
  ├─→ 行動電話門號認證                  (mnogw.cht.com.tw / login2.fetnet.net)
  ├─→ 行動身分識別                      (mobileconnect.gtcloud.net.tw / mid.twca.com.tw)
  └─→ 憑證狀態查詢 (OCSP)               (rootocsp / twcasslocsp.twca.com.tw)

使用者查詢健康資料（核心，台灣 GSN）⭐
  ├─ 健康存摺（就醫用藥／檢驗／健檢）→   (myhealthbank / mhbdata2.nhi.gov.tw)
  ├─ 虛擬健保卡 →                        (vhc.nhi.gov.tw)
  ├─ 健保卡網路服務註冊 →                (cloudicweb.nhi.gov.tw)
  └─ 醫療／健保資訊 →                    (med / info.nhi.gov.tw)

就醫院所查詢（地圖）
  └─→ 地圖圖磚 + 定位 API               (wmts.nlsc.gov.tw / api.tgos.tw ← Google Cloud)
```

---

## 摘要

| 分類 | 網域 | 是否核心功能 | 連線節點 | 資料是否出境 |
|------|------|--------------|----------|--------------|
| 健康存摺／虛擬健保卡（敏感） | `*.nhi.gov.tw`（GSN） | 是 | 台灣（GSN） | 否 |
| 健保署公開網站 | `www.nhi.gov.tw` | 否 | 台灣（Cloudflare 節點） | 可能（Cloudflare 海外營運） |
| 身分認證（電信／TWCA） | `cht.com.tw`／`fetnet.net`／`gtcloud.net.tw`／`twca.com.tw` | 是（輔助） | 台灣 | 否 |
| 地圖服務 | `wmts.nlsc.gov.tw` | 是（輔助） | 台灣 | 否 |
| 地圖 API | `api.tgos.tw` | 是（輔助） | 台灣（Google Cloud 節點） | 可能（Google 海外營運） |
| Google 分析／推播 | `google-analytics`／`googleapis`／`googletagmanager` 等 | 否 | 台灣（Google Anycast） | 可能（Google 海外營運） |

健保快易通是本系列迄今**最敏感**之 App，惟其**核心健康資料（健康存摺之就醫、用藥、檢驗、健檢紀錄與虛擬健保卡）完全走健保署自建之 `*.nhi.gov.tw`（台灣 GSN，AS4782），未經公有雲、未出境**，資料保護設計良好。免插卡身分驗證透過台灣電信（中華電信、遠傳）門號認證與 TWCA 憑證機構完成，亦為境內機構。

需留意者有三：(1) **公開網站 `www.nhi.gov.tw` 由 Cloudflare（海外雲端）承載**——雖不涉個人醫療資料，但政府網站託管於海外 CDN 一節可再確認；(2) **就醫院所地圖 `api.tgos.tw` 主機位於 Google Cloud**，屬海外雲端節點；(3) App 內嵌 **Google Analytics／Firebase／Tag Manager 使用分析與 FCM 推播**，會將使用行為與裝置識別傳送至 Google 海外，對一款承載全民醫療資料的政府 App 而言，第三方分析之必要性與範圍值得檢視。

> **隱私風險評估**：最敏感之醫療與身分資料留在台灣政府網路，風險低，設計符合期待。整體風險集中於「非核心」的公開網站 CDN、地圖 API 與 Google 分析／推播等海外雲端服務——這些不涉健康存摺內容，但仍會傳送使用行為與裝置資訊至境外第三方。本次為瀏覽式測試，實際查詢健康存摺／簽發虛擬健保卡時之個資傳輸內容與加密方式，建議後續針對單一功能加測 HTTP 明細以完整評估。
