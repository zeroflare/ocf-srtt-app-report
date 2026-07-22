# OCF SRTT App 分析報告

台灣常用 App 網路流量與資料流向分析。撰寫指引見 [分析手冊](HANDBOOK.md)。共分析 11 款 App，全部完成。

## 總覽對照

| App | 營運方（地區） | 核心資料落點 | 資料出境 | 第三方追蹤／廣告 |
|------|--------------|------------|:--------:|--------------|
| [行動自然人憑證](apps/twfido.md) | 內政部（台灣） | 台灣 GSN | 否 | 低（Google 推播／分析） |
| [警政服務](apps/police.md) | 警政署（台灣） | 台灣 HiNet／GSN | 否＊ | 低（＊165 打詐儀錶板→AWS 東京） |
| [健保快易通](apps/nhiapp.md) | 健保署（台灣） | 台灣 GSN | 否 | 低（公開站 Cloudflare、地圖 GCP） |
| [郵局（中華郵政）](apps/post.md) | 中華郵政（台灣） | 台灣（多家電信） | 否 | 低（Google 分析／地圖） |
| [台鐵e訂通](apps/tra.md) | 臺灣鐵路（台灣） | 台灣 HiNet | 否 | 低（Google 分析／廣告／推播） |
| [台灣行動支付（台灣Pay）](apps/taiwanpay.md) | TWMP（台灣） | 台灣（核心） | 部分 | 中（便利生活 webview 廣告／追蹤） |
| [LINE](apps/line.md) | LY Corporation（日本） | 日本（通訊）／台灣（LINE Bank） | 是（日本，本質） | 高（Google／Moloco／Dable 韓／Meta／Bing） |
| [Google 地圖](apps/googlemaps.md) | Google（美國） | 美國 Google | 是（美國，本質） | 低（僅 Google 自家） |
| [蝦皮購物](apps/shopee.md) | Shopee／Sea（新加坡） | 商城台灣；API／追蹤／付款新加坡 | 是（新加坡） | 高（自家 UBT＋Meta／Google／AppsFlyer） |
| [ChatGPT](apps/chatgpt.md) | OpenAI（美國） | 美國（Cloudflare） | 是（美國，本質） | 極低（無廣告；僅登入／訂閱／監控） |
| [智生活](apps/ihome.md) | SmaDay／今網智慧（台灣品牌） | **Google Cloud（美國）** | 是（公有雲） | **最高**（Clarity 側錄＋Smadex＋Google／Meta／Bing＋Infobip 英國） |

**觀察重點**：政府與公營 App（自然人憑證、警政、健保、郵局、台鐵）核心與敏感資料均留在台灣境內（政府 GSN／中華電信 HiNet），符合期待。外商服務（LINE、Google 地圖、ChatGPT、蝦皮）核心資料本即由境外業者處理，屬平台本質；其中 ChatGPT／Google 地圖流向單一無廣告，LINE／蝦皮則整合大量第三方追蹤。最值得注意者為**智生活**：台灣品牌卻將社區住戶資料後端託管於境外公有雲（Google Cloud），並疊加全系列最重之追蹤（含 Microsoft Clarity 行為側錄）。

## 報告列表

- ✅ [行動自然人憑證](apps/twfido.md)（範本）
- ✅ [警政服務](apps/police.md)
- ✅ [健保快易通](apps/nhiapp.md)
- ✅ [郵局（中華郵政）](apps/post.md)
- ✅ [台灣行動支付（台灣Pay）](apps/taiwanpay.md)
- ✅ [LINE](apps/line.md)
- ✅ [Google 地圖](apps/googlemaps.md)
- ✅ [台鐵e訂通](apps/tra.md)
- ✅ [蝦皮購物](apps/shopee.md)
- ✅ [ChatGPT](apps/chatgpt.md)
- ✅ [智生活](apps/ihome.md)

網站：https://zeroflare.github.io/ocf-srtt-app-report/
