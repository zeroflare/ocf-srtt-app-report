---
title: 分析手冊
description: App 網路流量分析報告撰寫指引
---

# App 網路流量分析手冊

本手冊說明實機測試時，每一份報告（`apps/<app>.md`）需要蒐集哪些資訊、如何判定與填寫。範本以完整的 [行動自然人憑證](apps/twfido.html) 報告為準。

---

## 一、測試前準備

| 項目 | 說明 |
|------|------|
| 裝置 | 記錄型號與 OS 版本（例：iPhone 16 Plus / iOS 27.0） |
| 擷取工具 | SRTT + 分包截取工具（MITM proxy） |
| App 版本 | 於 App Store / Play 商店或 App 內「關於」查看 |
| 套件名稱 / Bundle ID | Android：Play 商店網址 `?id=` 後字串；iOS：可由憑證/流量識別 |
| 擷取日期 | 記錄實測當天日期 YYYY-MM-DD |

**操作腳本建議**：安裝後首次啟動 → 登入/註冊 → 走完 App 主要功能（各分頁都點一次）→ 觸發核心業務（如付款、簽章、查詢）→ 背景閒置數分鐘觀察心跳/分析流量。全程開著截取工具。

---

## 二、每份報告需蒐集的資料

### 1. 概述表格

- **App 名稱**：中文全名
- **App 版本**：含 build number（例：`2.7.5（2606181）`）
- **裝置**：型號 / OS
- **擷取時間**：YYYY-MM-DD
- **涉及網域**：測到的**不重複網域總數**
- **App 圖示 URL**：可取 App Store / Play 商店的圖示連結
- **簡介**：由誰提供、主要用途（預留檔已預填，實測後校正）

### 2. 網域分析（每個網域一列 + 一段展開）

彙整表每個網域需填 6 欄：

| 欄位 | 怎麼取得 |
|------|----------|
| 網域 | 從截取的請求 Host 蒐集，去重 |
| 所屬單位 | WHOIS / 憑證 CN / 常識判定（內政部、Google、Shopee…） |
| 國家 | 見下方「國家判定」 |
| 雲端 | 是否為公有雲（AWS/GCP/Azure/Cloudflare…）；政府自建/GSN 記「否」 |
| ASN | 由解析 IP 反查 ASN（例 AS15169 = Google） |
| 主要用途 | 該網域在 App 中的角色 |

每個網域再展開一段，含 6 個要點：**域名性質、地理位置、基礎設施、角色、資料特性、DNS 解析結果（A Record 表格）**。

### 3. 國家判定原則

- **實體固定 IP**（如政府 GSN、電信機房）：以 WHOIS / ASN 註冊國為準。
- **Anycast / CDN**（Google、Cloudflare、Akamai…）：連線**節點**可能在台灣，但**營運商**為海外雲端 → 於報告註明「連線節點台灣，營運商海外」，摘要「資料是否出境」欄記「可能」。
- 每份報告在網域表下方用一段 `>` 引言說明判定方式。

### 4. 判斷工具與指令

```
# DNS A record 與 ASN
dig +short <domain> A
whois <ip>            # 查 ASN、netname、國家
# 或線上：bgp.he.net、ipinfo.io/<ip>、who.is

# 憑證所屬單位
openssl s_client -connect <domain>:443 -servername <domain> </dev/null 2>/dev/null | openssl x509 -noout -subject -issuer
```

### 5. API 用途整理

依「網域 / 功能」分類，逐一列出觀察到的端點。每個端點記錄：

- **方法 + 路徑**（例：`POST /moise/app/reportSignResult`）
- **觸發時機**：什麼操作會打這支
- **請求內容**：關鍵參數、是否含個資 / 憑證 / Token、加密或簽章方式、Content-Type、是否壓縮
- **回應**：狀態碼與代表意義（例：`204 No Content`、`{"error_code":"0"}`）
- **用途**：這支在業務流程中的角色，是否為核心功能

> **敏感資料重點**：特別標注是否傳輸身分證字號、姓名、電話、Email、健保/憑證資料、位置、裝置 ID、付款資訊等；以及是否走 TLS、是否有額外簽章。

### 6. 請求流程概觀

用 ASCII 流程圖描述「App 啟動 → 各網域請求順序」與「核心操作觸發的請求鏈」（參考範本）。

### 7. 摘要表

每個分類一列：**分類 / 網域 / 是否核心功能 / 連線節點 / 資料是否出境**，最後一段總結核心功能走哪些網域、資料是否出境、隱私風險評估。

---

## 三、判定速查

| 情境 | 判定 |
|------|------|
| `.gov.tw` + GSN（AS4782）自建 IP | 台灣、非雲端、核心 |
| Google（AS15169）Anycast | 節點台灣、雲端、出境「可能」 |
| Cloudflare（AS13335） | 雲端、出境「可能」 |
| Firebase / Analytics / Crashlytics | 非核心、輔助第三方分析 |
| FCM（`iid.googleapis.com`、`fcm.googleapis.com`） | 推播、核心（輔助） |
| App 內 WebView 載入官網頁面 | 內容頁、通常不涉個資 |

---

## 四、待完成清單

| App | 檔案 | 狀態 |
|-----|------|------|
| 行動自然人憑證 | `apps/twfido.md` | ✅ 完成（範本） |
| 警政服務 | `apps/police.md` | ✅ 完成 |
| 健保快易通 | `apps/nhiapp.md` | ✅ 完成 |
| 郵局（中華郵政） | `apps/post.md` | ✅ 完成 |
| 台灣行動支付（台灣Pay） | `apps/taiwanpay.md` | ✅ 完成 |
| LINE | `apps/line.md` | ✅ 完成 |
| Google 地圖 | `apps/googlemaps.md` | ✅ 完成 |
| 台鐵e訂通 | `apps/tra.md` | ✅ 完成 |
| 蝦皮購物 | `apps/shopee.md` | ✅ 完成 |
| ChatGPT | `apps/chatgpt.md` | ✅ 完成 |
| 智生活 | `apps/ihome.md` | ✅ 完成 |
