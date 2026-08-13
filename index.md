---
---

# OCF SRTT App 分析報告

台灣常用 App 網路流量與資料流向分析。撰寫方式見 [分析手冊](#/分析手冊)。共分析 11 款 App。

> 📋 **[閱讀綜合報告 →](#/綜合報告)**　（11 款 App 重點彙整、風險分級與方法說明，建議先讀）
>
> 🌊 **[科普：海纜斷了，App 還能用嗎？ →](#/科普報導-海纜斷了還能用嗎)**　（給民眾：模擬對外海纜中斷時，11 款 App 能否在島內繼續使用）
>
> 🔎 **[這些 App 是怎麼測的？ →](#/如何測試)**　（SRTT DNS 觀測、ping 修正國家、Stream 拆 HTTPS）

## 總覽對照

| App | 營運方（地區） | 連線節點（核心） | 資料出境 | 第三方追蹤／廣告 |
|------|--------------|----------------|:--------:|--------------|
| [行動自然人憑證](#/apps/行動自然人憑證) | 內政部（台灣） | 台灣 GSN | 否 | 低（Google 推播／分析，台灣 Anycast） |
| [警政服務](#/apps/警政服務) | 警政署（台灣） | 台灣 HiNet／GSN | 否＊ | 低（＊165 打詐儀錶板→AWS 東京） |
| [健保快易通](#/apps/健保快易通) | 健保署（台灣） | 台灣 GSN | 否 | 低（Cloudflare／地圖 GCP／Google＝台灣節點） |
| [郵局（中華郵政）](#/apps/郵局) | 中華郵政（台灣） | 台灣（多家電信） | 否 | 低（Tracestrack／Google＝台灣 Anycast） |
| [台鐵e訂通](#/apps/台鐵e訂通) | 臺灣鐵路（台灣） | 台灣 HiNet | 否 | 低—中（Google 台灣 Anycast；Trip.com／Coupang 待確認） |
| [台灣行動支付（台灣Pay）](#/apps/台灣Pay) | TWMP（台灣） | 台灣（HiNet／Akamai） | 部分 | 中（便利生活 webview 廣告／追蹤） |
| [LINE](#/apps/LINE) | LY Corporation（日本） | 日本（LEGY）／台灣（api／CDN／Bank） | 是（營運日本） | 高（Google／Dable 韓／Meta／Bing；Adjust＝德國） |
| [Google 地圖](#/apps/Google地圖) | Google（美國） | **台灣（Google Anycast）** | 是（營運美國） | 極低（僅 Google 自家；連線亦台灣） |
| [蝦皮購物](#/apps/蝦皮購物) | Shopee／Sea（新加坡） | 商城台灣；API／追蹤／付款新加坡 | 是（新加坡） | 高（自家 UBT＋Meta／Google／AppsFlyer） |
| [ChatGPT](#/apps/ChatGPT) | OpenAI（美國） | **台灣（Cloudflare Anycast）** | 是（營運美國） | 極低（無廣告；RevenueCat＝`tpe54` 台灣） |
| [智生活](#/apps/智生活) | SmaDay／今網智慧（台灣品牌） | 台灣邊緣（GCP／HiNet）；後端託管 GCP | **是（公有雲）** | **最高**（Clarity＋Smadex＋Google／Meta／Bing＋Infobip 德國） |

> **連線節點**以 `mtr --tcp --port 443` 判定（Best &lt; 12 ms 多為台灣 Anycast／邊緣）；**資料出境**另依營運商所在與託管位置判斷，兩者分開。

**觀察重點**：

政府與公營 App（自然人憑證、警政、健保、郵局、台鐵）之核心與敏感資料均留在**台灣境內**（政府 GSN 或中華電信 HiNet 機房）；少數例外為「165 打詐儀錶板」連 AWS 東京。

外商服務（LINE、Google 地圖、ChatGPT、蝦皮）之**營運商在境外**，核心資料由境外業者處理；惟連線節點常落於台灣 Anycast／邊緣（如 Google 地圖、ChatGPT 經 Cloudflare 本次皆判**台灣**）。其中 ChatGPT、Google 地圖流向單一（無跨站廣告追蹤），LINE、蝦皮則整合大量第三方廣告與追蹤。

最值得注意的是**智生活**——台灣品牌、處理社區住戶資料，卻將**自家後端託管於境外公有雲（Google Cloud）**，並疊加全系列最重的追蹤堆疊（含 Microsoft Clarity 行為側錄與 Smadex 程式化廣告）。

> 各 App 之網域清單以擷取當時之觀察為準；第三方追蹤之確切歸屬（App 本體或內嵌 webview 內容）建議另以 iOS「App 隱私權報告」按 App 檢視佐證。詳見各報告內文。

## 報告列表

- ✅ [行動自然人憑證](#/apps/行動自然人憑證)
- ✅ [警政服務](#/apps/警政服務)
- ✅ [健保快易通](#/apps/健保快易通)
- ✅ [郵局（中華郵政）](#/apps/郵局)
- ✅ [台灣行動支付（台灣Pay）](#/apps/台灣Pay)
- ✅ [LINE](#/apps/LINE)
- ✅ [Google 地圖](#/apps/Google地圖)
- ✅ [台鐵e訂通](#/apps/台鐵e訂通)
- ✅ [蝦皮購物](#/apps/蝦皮購物)
- ✅ [ChatGPT](#/apps/ChatGPT)
- ✅ [智生活](#/apps/智生活)
