---
title: 警政服務
---

# 警政服務 App 網路流量分析報告

## 概述

<img src="https://is1-ssl.mzstatic.com/image/thumb/Purple211/v4/4c/56/a5/4c56a589-dcff-9dc1-746a-9403596abd11/AppIcon-0-0-1x_U007emarketing-0-8-0-85-220.png/512x512bb.jpg" alt="警政服務 App 圖示" width="150" height="150" style="border-radius: 22%; object-fit: cover; box-shadow: 0 4px 12px rgba(0, 0, 0, 0.18);">

本報告依據 SRTT 與封包擷取工具分析**警政服務**（`tw.gov.npa.110APP`）App 的網路請求。此 App 由內政部警政署提供，是一款**入口／整合型**應用，集合報案（110／165／113）、防詐（165 打詐儀錶板、可疑訊息分析）、交通（即時路況、測速執法點、違規查詢、違規拖吊查詢）、查詢（失竊車輛、失蹤人口、通緝犯、刑事紀錄證明）、防空避難、收聽警廣、呼叫計程車等三十餘項功能。

多數功能以 **WebView 載入警政署各子系統或外部網站**，少部分為**撥打電話**（如 110、165、113）或**外連 Facebook**（NPA署長室）。

| 項目 | 內容 |
|------|------|
| App 名稱 | 警政服務 |
| App 版本 | 8.9.1 |
| 裝置 | iPhone 16 Pro / iOS 26.5.2 |
| 擷取時間 | 2026-07-22（初測）／**2026-07-30（複測，本報告以複測為準）** |
| 涉及網域 | 本次複測觀察到 App 相關主機名約 55 個（Meta／Google 之 CDN 子網域變體較多，歸併同源後與前次相當）；另有 iOS 系統背景網域已排除 |

> **測試方式與資料範圍說明**：本次複測較初測**更進一步實際操作**——除各功能點瀏覽外，另實測了「110 定位報案（送出定位）」「失竊車輛查詢」「通緝犯查詢」等**會送出使用者輸入的寫入／查詢操作**，因此本報告除網域與資料流向外，另就這些操作**實際送出的欄位類型與落點**加以佐證。惟為保護測試者隱私，內文僅描述**欄位類型**（如「手機號碼」「GPS 座標」「姓名／身分證／車牌」），不列出實際值。

---

## 網域分析

警政服務為整合型 App，涉及網域較多，以下依**所屬單位／角色**分為七類彙整。國家與雲端歸屬以本次 HAR 內**實際連線的 server IP** 為準（較事後 DNS 查詢更貼近當下觀察）。iOS 系統層背景網域非 App 業務流量，已於文末另列並排除；封包擷取工具本身之串流網域（如含 `stream` 者）亦非本 App 流量，一併排除。

| 網域 | 所屬單位 | 國家 | 雲端 | ASN | Anycast | 主要用途 |
|------|------|------|------|------|------|------|
| `www.npa.gov.tw` | 內政部警政署 | TW | 否（HiNet CDN） | AS3462（中華電信 HiNet） |  | 警政署官網、各功能 WebView 內容頁 |
| `www.apb.npa.gov.tw` | 警政署刑事警察局 | TW | 否（HiNet CDN） | AS3462（中華電信 HiNet） |  | 刑事局相關查詢／內容頁 |
| `165.npa.gov.tw` | 刑事警察局 165 | TW | 否（HiNet CDN） | AS3462（中華電信 HiNet） |  | 165 反詐騙服務頁 |
| `adr.npa.gov.tw` | 警政署（防空避難） | TW | 否（HiNet CDN） | AS3462（中華電信 HiNet） |  | 防空避難處所查詢（`adr-web` 避難地圖／資訊） |
| `app110.npa.gov.tw` | 警政署（110 報案，GSN） | TW | 否（政府網路） | AS4782（GSN） |  | **110 定位報案 API**（`Service.asmx`，送出手機＋GPS） |
| `nv2.npa.gov.tw` | 警政署（GSN） | TW | 否（政府網路） | AS4782（GSN） |  | 警政後端服務（遺失物等） |
| `ps.npa.gov.tw` | 警政署（GSN） | TW | 否（政府網路） | AS4782（GSN） |  | 違規拖吊查詢（`TowingService`） |
| `eze8.npa.gov.tw` | 警政署（新世紀資通） | TW | 否（政府網路） | AS9919（新世紀資通） |  | 失竊車輛／通緝犯查詢（`NpaE8ServerRWD`） |
| `tm2.npa.gov.tw` | 警政署（新世紀資通） | TW | 否（政府網路） | AS9919（新世紀資通） |  | 交通違規／事故子系統 |
| `op2.npa.gov.tw` | 警政署（新世紀資通） | TW | 否（政府網路） | AS9919（新世紀資通） |  | 警政後端服務 |
| `eli.npa.gov.tw` | 警政署（新世紀資通） | TW | 否（政府網路） | AS9919（新世紀資通） |  | 警政後端服務 |
| `wmts.nlsc.gov.tw` | 內政部國土測繪中心（國網中心） | TW | 否（政府機房） | AS7539（國網中心） |  | 地圖圖磚（路況／測速點／避難底圖） |
| `vt.nlsc.gov.tw` | 內政部國土測繪中心（國網中心） | TW | 否（政府機房） | AS7539（國網中心） |  | 向量圖磚（避難處所點位 MVT） |
| `rtr.pbs.gov.tw` | 警察廣播電臺（GSN） | TW | 否（政府網路） | AS4782（GSN） |  | 收聽警廣（線上串流） |
| `165dashboard.tw` | 刑事局 165 打詐儀錶板（委外） | JP | **是（AWS 東京）** | AS16509（AWS） |  | 打詐儀錶板前端與 `CIB_DWS_API` 統計 API |
| `api-next.no8.io` | 打詐儀錶板線上客服 API（委外 NO8） | JP | **是（AWS 東京）** | AS16509（AWS） |  | 打詐儀錶板 `live_chat` API |
| `assets.no8.io` | 打詐儀錶板靜態資源（委外 NO8） | TW | 是（AWS CloudFront） | AS16509（AWS） | ✓ | 靜態資源 |
| `live-chat-console.no8.io` | 打詐儀錶板線上客服（委外 NO8） | TW | 是（AWS CloudFront） | AS16509（AWS） | ✓ | 線上客服／聊天 |
| `www/graph/web/api.facebook.com` 等 Meta 網域 | Meta | US | 是 | AS32934（Meta） | ✓ | NPA署長室外連、FB SDK |
| `scontent.ftpe7-*.fna.fbcdn.net` 等 | Meta（FB CDN） | TW | 是 | AS32934（Meta） | ✓ | FB 圖片／靜態內容 |
| `cse.google.com`／`syndicatedsearch.goog` | Google | TW | 是 | AS15169（Google） | ✓ | 站內搜尋（Custom Search） |
| `www.adsensecustomsearchads.com`／`*.adtrafficquality.google` | Google | TW | 是 | AS15169（Google） | ✓ | 站內搜尋隨附廣告（AdSense for Search） |
| `www.googletagmanager.com`／`analytics.google.com`／`app-analytics-services.com` | Google | TW | 是 | AS15169（Google） | ✓ | 使用行為分析（GA／GTM／Firebase） |
| `fonts.googleapis.com`／`fonts.gstatic.com` | Google | TW | 是 | AS15169（Google） | ✓ | 網頁字型 |
| `i.ytimg.com`／`maps.googleapis.com` | Google | TW | 是 | AS15169（Google） | ✓ | YouTube 影片縮圖／地圖 API |
| `unpkg.com` | Cloudflare | TW | 是 | AS13335（Cloudflare） | ✓ | 前端 JS 函式庫 CDN |

> **國家判定說明（以本次實際連線 IP 為準）**：`.npa.gov.tw` 之 GSN 子網域（`app110`、`nv2`、`ps`，連 `210.69.154.x`，AS4782）與新世紀資通子網域（`eze8`、`tm2`、`op2`，連 `122.146.27.x`，AS9919）皆為政府／承包商實體機房，註冊國台灣，資料留在境內。`www.npa.gov.tw`、`www.apb.npa.gov.tw`、`165.npa.gov.tw`、`adr.npa.gov.tw` 由**中華電信 HiNet CDN（AS3462，`203.66.3x.x`）**承載，仍屬台灣電信基礎設施；`wmts`／`vt.nlsc.gov.tw`（`140.110.x.x`，AS7539 國網中心）亦為台灣。Google、Meta、Cloudflare 採 Anycast，連線節點判定位於台灣，但**營運商為海外雲端**，資料出境記「可能」。**165 打詐儀錶板（`165dashboard.tw`、`api-next.no8.io`）經 mtr（TCP/443）與 PTR 反查確認為 AWS 東京 EC2（`ap-northeast-1`），為唯一資料實際落於境外之服務。**

> **國家／Anycast 判定（`mtr --tcp --port 443`）**：以 TCP/443 終點延遲與 PTR／ASN 判定連線節點國家；Cloudflare、Google、Meta、CloudFront、GCP Global LB 等標 Anycast ✓。營運商為海外者，資料出境仍可能為「是／可能」，與連線節點國家分開判斷。

### 1. 警政署核心後端（內政部警政署，台灣，非公有雲）

* **域名性質**：`.npa.gov.tw` 政府域名，為警政署官網與各業務子系統端點
* **地理位置（本次實際連線 IP）**：`www.npa.gov.tw`、`www.apb.npa.gov.tw`、`165.npa.gov.tw`、`adr.npa.gov.tw` 連 HiNet CDN（AS3462，`203.66.32.x`／`203.66.34.x`／`203.66.35.x`，中華電信，台北）；`app110`、`nv2`、`ps.npa.gov.tw` 連 `210.69.154.x`，屬 **GSN（政府服務網路，AS4782）**；`eze8`、`tm2`、`op2.npa.gov.tw` 連 `122.146.27.x`，屬**新世紀資通（AS9919）**
* **基礎設施**：非公有雲；官網類頁面透過中華電信 HiNet CDN 加速，業務服務位於政府／承包商網路
* **角色**：App 核心後端，承載各功能之 WebView 內容頁與查詢／報案服務（防空避難、通緝／失蹤／失竊查詢、違規拖吊、刑事紀錄證明、110 定位報案等）
* **資料特性（本次實測）**：
  * **110 定位報案**：`app110.npa.gov.tw/APP_110/Service.asmx` 以 SOAP `InsertGPSDataIntoDB` **送出手機號碼與 GPS 經緯度**（報案定位），落點為 GSN（台灣）。
  * **失竊車輛／通緝犯查詢**：`eze8.npa.gov.tw/NpaE8ServerRWD`（`doCLQuery`／`doNKQuery`）以查詢條件**送出車牌／姓名／身分證等欄位**，採 HTTPS＋Bearer JWT＋CSRF token，落點為新世紀資通（台灣）。
  * 上述敏感欄位**均留在台灣政府網路範圍內，未出境**。
* **DNS／連線 IP**（本次代表值）：

| 網域 | 類型 | 連線 IP | ASN | 國家 |
|------|------|---------|-----|------|
| www.npa.gov.tw | A | 203.66.32.13 | AS3462（中華電信 HiNet） | TW |
| www.apb.npa.gov.tw | A | 203.66.32.43 | AS3462（中華電信 HiNet） | TW |
| 165.npa.gov.tw | A | 203.66.35.104 | AS3462（中華電信 HiNet） | TW |
| adr.npa.gov.tw | A | 203.66.34.36 | AS3462（中華電信 HiNet） | TW |
| app110.npa.gov.tw | A | 210.69.154.36 | AS4782（GSN） | TW |
| nv2.npa.gov.tw | A | 210.69.154.93 | AS4782（GSN） | TW |
| ps.npa.gov.tw | A | 210.69.154.38 | AS4782（GSN） | TW |
| eze8.npa.gov.tw | A | 122.146.27.116 | AS9919（新世紀資通） | TW |
| tm2.npa.gov.tw | A | 122.146.27.67 | AS9919（新世紀資通） | TW |
| op2.npa.gov.tw | A | 122.146.27.88 | AS9919（新世紀資通） | TW |

AS3462 為中華電信 HiNet；AS4782 為 GSN（政府服務網路，Data Communication Business Group）；AS9919 為新世紀資通（New Century InfoComm，Seednet）。

### 2. 地圖與警廣（其他政府單位，台灣，非公有雲）

* **域名性質**：`wmts.nlsc.gov.tw`（點陣圖磚 WMTS）、`vt.nlsc.gov.tw`（向量圖磚 MVT，本次見避難處所點位 `MVT_Shelter_Points`）為內政部國土測繪中心地圖服務；`rtr.pbs.gov.tw` 為警察廣播電臺串流
* **地理位置（本次實際連線 IP）**：`wmts.nlsc.gov.tw` → `140.110.20.85`、`vt.nlsc.gov.tw` → `140.110.134.39`，ASN **AS7539（國家高速網路與計算中心，NCHC）**，台灣；`rtr.pbs.gov.tw` → `117.56.47.51`，ASN **AS4782（GSN）**，台灣
* **基礎設施**：政府自建／學研機房，非公有雲
* **角色**：圖磚供「即時路況」「測速執法點」「防空避難」等地圖功能載入底圖與點位；警廣串流供「收聽警廣」
* **資料特性**：圖磚與音訊串流，不涉個資
* **DNS／連線 IP**：

| 網域 | 類型 | 連線 IP | ASN | 國家 |
|------|------|---------|-----|------|
| wmts.nlsc.gov.tw | A | 140.110.20.85 | AS7539（國網中心） | TW |
| vt.nlsc.gov.tw | A | 140.110.134.39 | AS7539（國網中心） | TW |
| rtr.pbs.gov.tw | A | 117.56.47.51 | AS4782（GSN） | TW |

AS7539 為 National Center for High-performance Computing（國網中心）；AS4782 為 GSN。

### 3. 165 打詐儀錶板（刑事局委外，AWS 東京日本）⚠️

* **域名性質**：`165dashboard.tw` 為 165 打詐儀錶板獨立網站（`CIB_DWS_API` 提供詐騙統計、案例、趨勢等 API）；`*.no8.io` 為其技術服務商（NO8）之線上客服 API、靜態資源網域
* **地理位置（mtr／PTR 驗證）**：以 `mtr --tcp --port 443` 與 DNS PTR 反查確認，兩者皆為 **AWS 東京 EC2（`ap-northeast-1`，日本）**：
  * `165dashboard.tw` → `52.197.64.104`／`54.95.60.138` → `ec2-52-197-64-104.ap-northeast-1.compute.amazonaws.com`／`ec2-54-95-60-138.ap-northeast-1.compute.amazonaws.com`
  * `api-next.no8.io` → `52.192.81.202`／`13.113.30.63` → `ec2-52-192-81-202.ap-northeast-1.compute.amazonaws.com`／`ec2-13-113-30-63.ap-northeast-1.compute.amazonaws.com`
  * `assets.no8.io`（`65.9.180.10`）、`live-chat-console.no8.io`（`54.192.248.81`）走 AWS CloudFront 邊緣節點
* **基礎設施**：**Amazon AWS 公有雲 EC2（AS16509，東京區域）**
* **角色**：App 內「打詐儀錶板」防詐統計資訊頁與 API；`api-next.no8.io`／`live-chat-console.no8.io` 提供線上客服（`live_chat`）
* **資料特性**：本次觀察以**讀取公開防詐統計**（`GetCounter`、`GetDailyCityFraudData`、`GetMonthlyFraudMethodRanking` 等 GET）為主，另有一筆 `AddCounter` 計數；惟前端與 API 主機實體位於**日本東京 EC2**，若使用者於此輸入查詢或客服對話，**資料將實際傳輸至境外（日本）**——此為本報告最需注意之出境點
* **DNS／連線 IP**（含 PTR 反查）：

| 網域 | 類型 | 連線 IP | PTR（EC2 主機名） | ASN | 國家 |
|------|------|---------|-------------------|-----|------|
| 165dashboard.tw | A | 52.197.64.104 | `ec2-52-197-64-104.ap-northeast-1.compute.amazonaws.com` | AS16509（AWS） | JP |
| 165dashboard.tw | A | 54.95.60.138 | `ec2-54-95-60-138.ap-northeast-1.compute.amazonaws.com` | AS16509（AWS） | JP |
| api-next.no8.io | A | 52.192.81.202 | `ec2-52-192-81-202.ap-northeast-1.compute.amazonaws.com` | AS16509（AWS） | JP |
| api-next.no8.io | A | 13.113.30.63 | `ec2-13-113-30-63.ap-northeast-1.compute.amazonaws.com` | AS16509（AWS） | JP |
| assets.no8.io | A | 65.9.180.10 | （CloudFront） | AS16509（AWS） | TW（邊緣） |
| live-chat-console.no8.io | A | 54.192.248.81 | （CloudFront） | AS16509（AWS） | TW（邊緣） |

AS16509 為 Amazon.com, Inc.（AWS）。PTR 主機名中的 `ap-northeast-1` 即東京區域，證實主機為該區 EC2 實例。

### 4. Facebook / Meta（NPA署長室外連與 FB SDK，海外）

* **域名性質**：`www/api/graph/web.facebook.com`、`rupload.facebook.com` 為 Meta 服務端點；`scontent.ftpe7-*.fna.fbcdn.net`、`scontent-tpe1-1.xx.fbcdn.net`、`static/z-m-static.xx.fbcdn.net` 為 FB 內容 CDN；`*-netseer-ipaddr-assoc.*.fbcdn.net` 為 Meta 網路測量
* **地理位置（本次實際連線 IP）**：`www.facebook.com` → `31.13.87.36`、`scontent.ftpe7-*` → `203.74.65.x`，ASN **AS32934（Facebook）**，連線節點判定位於**台灣（TPE 邊緣）**
* **基礎設施**：Meta 公有雲
* **角色**：「NPA署長室」功能外連 Facebook 粉專；App 內嵌 FB SDK（登入／分享／內容嵌入）
* **資料特性**：傳送 App 識別、裝置資訊與 FB 互動資料；營運商為 Meta 海外

AS32934 為 Facebook, Inc.（Meta）。

### 5. Google 服務（站內搜尋／分析／字型／YouTube，海外雲端）

* **域名性質**：本次觀察涵蓋——
  * **站內搜尋（Custom Search）**：`cse.google.com`、`syndicatedsearch.goog`、`clients1.google.com`
  * **站內搜尋隨附廣告（AdSense for Search）**：`www.adsensecustomsearchads.com`、`ep1/ep2.adtrafficquality.google`
  * **分析**：`www.googletagmanager.com`、`analytics.google.com`、`ssl.google-analytics.com`、`app-analytics-services.com`（Firebase）
  * **網頁字型**：`fonts.googleapis.com`、`fonts.gstatic.com`
  * **YouTube／地圖**：`i.ytimg.com`（影片縮圖）、`maps.googleapis.com`
* **地理位置**：ASN **AS15169（Google LLC）**，Anycast，連線節點判定多位於**台灣**
* **基礎設施**：Google 公有雲
* **角色**：WebView 頁面所嵌之 Google 站內搜尋、GA／Tag Manager／Firebase 追蹤、字型與 YouTube 縮圖；**均為第三方輔助，非警政核心業務**
* **資料特性**：傳送頁面瀏覽事件、廣告識別、裝置資訊等；營運商為 Google 海外
* **本次與初測差異說明**：初測曾列一般橫幅廣告聯播網（`pagead2.googlesyndication.com`、`ad.doubleclick.net`）與 `*.googlevideo.com`，**本次複測均未再出現**；本次所見「廣告」流量實為**站內搜尋結果隨附之 AdSense for Search**（`adsensecustomsearchads.com`／`adtrafficquality.google`），並非獨立的橫幅廣告投放。

| 網域 | 類型 | 連線 IP | ASN | 國家 |
|------|------|---------|-----|------|
| cse.google.com | A | 142.250.192.142 | AS15169（Google） | TW |
| www.googletagmanager.com | A | 142.250.77.200 | AS15169（Google） | TW |
| i.ytimg.com | A | 142.250.66.86 | AS15169（Google） | TW |

AS15169 為 Google LLC。

### 6. 其他 CDN（Cloudflare，海外雲端）

* **域名性質**：`unpkg.com` 為 npm 前端函式庫 CDN（Cloudflare，AS13335，本次連 `104.18.1.22`）
* **角色**：WebView 頁面載入前端 JavaScript／CSS 函式庫等靜態資源
* **資料特性**：純靜態資源，不涉個資；營運商為海外雲端

AS13335 為 Cloudflare, Inc.。

### 7. iOS 系統背景與非 App 流量（已排除）

下列非警政服務 App 業務流量，於分析中排除，僅列出供辨識：

* `pbs2i.cdn-apple.com`（`17.253.117.134`，Apple CDN，iOS 系統）
* `afs.ampaeservices.com`（本次僅見 CONNECT、未見資料傳輸，連線 IP `17.57.10.19` 屬 **Apple Inc.**，研判為 iOS 系統層而非 App 業務；初測誤植於 Google 廣告分類，本次更正）
* 封包擷取工具自身之串流網域（含 `stream` 者，如 `stream.pbs.gov.tw`）——屬側錄工具流量，非本 App 產生，排除

---

## API 用途整理

> **說明**：本次複測除瀏覽外，另實測「110 定位報案」「失竊車輛／通緝犯查詢」等會送出資料之操作，故此節就觀察到的**端點與欄位類型**加以整理（為保護測試者，僅描述欄位類型，不列實際值）。

### 一、警政署核心服務（`*.npa.gov.tw`）

App 各功能透過 WebView 載入警政署官網（`www.npa.gov.tw`）與業務子系統，本次觀察到的代表端點與資料流：

* **110 定位報案**：`POST app110.npa.gov.tw/APP_110/Service.asmx`（SOAP `InsertGPSDataIntoDB`）——送出**手機號碼、GPS 經緯度、定位半徑**。落點 GSN（台灣）。
* **失竊車輛查詢**：`GET eze8.npa.gov.tw/NpaE8ServerRWD/CL_Query.jsp` → `POST doCLQuery`——以**車牌**等欄位查詢，Bearer JWT＋CSRF。落點新世紀資通（台灣）。
* **通緝犯查詢**：`GET .../NK_Query.jsp` → `POST doNKQuery`——以**姓名、身分證**等欄位查詢，Bearer JWT＋CSRF。落點台灣。
* **交通違規／事故**：`tm2.npa.gov.tw/NM105-505ClientRWD2`（`UtilServlet` 取縣市／單位／新聞等下拉資料，不涉個資）。
* **違規拖吊查詢**：`ps.npa.gov.tw/TowingService`、遺失物 `nv2.npa.gov.tw/NM107-604ClientE`、`op2.npa.gov.tw/NM107-512Client` 等 WebView 子系統。
* **防空避難**：`adr.npa.gov.tw/adr-web`（避難地圖／處所資訊頁）。

此類請求走台灣政府網路／HiNet CDN，**含個資之報案與查詢資料均未出境**。

### 二、防詐服務（`165.npa.gov.tw`、`165dashboard.tw`、`*.no8.io`）

「165 反詐騙」：`165.npa.gov.tw`（台灣，HiNet）承載反詐騙服務頁；「打詐儀錶板」載入 `165dashboard.tw/CIB_DWS_API`（AWS 東京）之統計 API（`GetCounter`、`GetDailyCityFraudData`、`GetMonthlyFraudMethodRanking` 等，本次以 GET 讀取公開統計為主），並透過 `api-next.no8.io`／`live-chat-console.no8.io`（AWS 東京／CloudFront）提供線上客服。**此類流量部分實際落於日本境外。**

### 三、地圖與廣播（`wmts.nlsc.gov.tw`、`vt.nlsc.gov.tw`、`rtr.pbs.gov.tw`）

即時路況、測速執法點、防空避難等地圖功能，載入國土測繪中心 WMTS 點陣圖磚（`wmts.nlsc.gov.tw`）與 MVT 向量圖磚（`vt.nlsc.gov.tw`，避難處所點位），均在台灣；「收聽警廣」載入警察廣播電臺串流（`rtr.pbs.gov.tw`，台灣）。

### 四、第三方嵌入（Facebook、Google、Cloudflare）

「NPA署長室」外連 Facebook 粉專並載入 FB SDK；WebView 頁面另嵌入 Google 站內搜尋（Custom Search）及其隨附之 AdSense for Search、Google Analytics／Tag Manager／Firebase 追蹤、Google 字型、YouTube 縮圖，以及 `unpkg.com` 前端函式庫。此類均為第三方輔助服務，非警政核心業務，營運商為海外雲端。

---

## 請求流程概觀

```
App 啟動 / 進入首頁
  ├─→ 載入官網內容與功能清單          (www.npa.gov.tw ← HiNet CDN)
  ├─→ 分析 / 追蹤                     (googletagmanager, analytics.google.com, app-analytics-services)
  └─→ 網頁字型 / 前端函式庫            (fonts.gstatic.com, unpkg.com)

使用者操作功能
  ├─ 110 定位報案 → 送出 手機＋GPS ⚠️個資  (app110.npa.gov.tw ← GSN, 台灣)
  ├─ 失竊車輛查詢 → 送出 車牌            (eze8.npa.gov.tw doCLQuery ← AS9919, 台灣)
  ├─ 通緝犯查詢 → 送出 姓名/身分證       (eze8.npa.gov.tw doNKQuery ← AS9919, 台灣)
  ├─ 違規拖吊 / 遺失物 / 交通違規 → 警政子系統 (ps, nv2, tm2, op2 .npa.gov.tw)
  ├─ 165 打詐儀錶板 → AWS 東京 ⚠️出境    (165dashboard.tw, api-next.no8.io)
  │        └─ 線上客服                  (live-chat-console.no8.io)
  ├─ 即時路況 / 測速點 / 防空避難 → 地圖圖磚 (wmts.nlsc.gov.tw, vt.nlsc.gov.tw)
  ├─ 收聽警廣 → 串流                    (rtr.pbs.gov.tw)
  └─ NPA署長室 → 外連 Facebook          (www.facebook.com, fbcdn.net)

功能頁內嵌第三方
  ├─→ 站內搜尋 (Google CSE)            (cse.google.com, syndicatedsearch.goog)
  ├─→ 搜尋隨附廣告 (AdSense for Search)  (adsensecustomsearchads.com, adtrafficquality.google)
  └─→ YouTube 影片縮圖                  (i.ytimg.com)

（撥打電話類：110 電話報案、165 反詐騙專線、113 保護專線 → 直接撥號，不產生網路流量）
```

---

## 摘要

| 分類 | 網域 | 是否核心功能 | 連線節點 | 資料是否出境 |
|------|------|--------------|----------|--------------|
| 警政署核心後端 | `*.npa.gov.tw`（HiNet CDN + GSN + 新世紀資通） | 是 | 台灣 | 否 |
| ├ 110 定位報案（含個資） | `app110.npa.gov.tw` | 是 | 台灣（GSN） | **否** |
| └ 車輛／通緝查詢（含個資） | `eze8.npa.gov.tw` | 是 | 台灣（AS9919 新世紀資通） | **否** |
| 地圖／警廣 | `wmts`／`vt.nlsc.gov.tw`、`rtr.pbs.gov.tw` | 是（輔助） | 台灣 | 否 |
| 165 打詐儀錶板 | `165dashboard.tw`、`api-next.no8.io` | 是（輔助） | **日本（AWS 東京）** | **是** |
| 打詐儀錶板靜態／客服 | `assets.no8.io`、`live-chat-console.no8.io` | 否 | 台灣（AWS CloudFront） | 可能（AWS 海外營運） |
| Facebook / Meta | `*.facebook.com`、`fbcdn.net` | 否 | 台灣（Meta 節點） | 可能（Meta 海外營運） |
| Google 搜尋／分析／字型 | `google.com`、`googletagmanager`、`adsensecustomsearchads` 等 | 否 | 台灣（Google Anycast） | 可能（Google 海外營運） |
| 前端 CDN | `unpkg.com`（Cloudflare） | 否 | 台灣（節點） | 可能（Cloudflare 海外營運） |

警政服務 App 的**核心報案與查詢功能**主要以 WebView 載入警政署 `*.npa.gov.tw` 各子系統，走中華電信 HiNet CDN、GSN 政府網路與新世紀資通，資料留在台灣境內。本次複測進一步實測了會送出個資的操作——**110 定位報案（手機＋GPS）與失竊車輛／通緝犯查詢（車牌／姓名／身分證）——經確認其送出目標均為台灣政府網路，敏感個資未出境**，此為對民眾最重要的正面結論。地圖（國土測繪中心）與警廣亦為台灣政府機房。

值得注意的是，**「165 打詐儀錶板」（`165dashboard.tw`、`api-next.no8.io`）之前端與 API 主機本次實測位於 AWS 東京（日本）**，是唯一資料明確落於境外之服務；其靜態資源與線上客服（`*.no8.io`）雖走 AWS 邊緣節點，營運商仍為海外雲端。此外，App 內多個 WebView 頁面嵌入 **Facebook 與 Google（站內搜尋／分析／字型／YouTube 縮圖）** 等第三方服務，連線節點雖多在台灣 Anycast／邊緣，營運商均為海外，會傳送瀏覽行為與裝置資訊，屬非核心之第三方流量。

> **隱私風險評估**：核心政府服務資料留在境內，**連 110 定位報案與個資查詢的送出目標亦確認在台灣政府網路，風險低**。惟需留意：(1)「打詐儀錶板」實際連線日本東京節點，涉境外傳輸（惟本次觀察多為讀取公開統計）；(2) 官方 App 內嵌 Google 分析／站內搜尋廣告與 Facebook SDK，會將使用者瀏覽行為傳送至海外第三方，對政府服務而言之必要性值得檢視。本次「廣告」流量經核實為**站內搜尋隨附的 AdSense for Search**，並非橫幅廣告聯播網，且初測所見的 DoubleClick／googlevideo 本次未再出現。
