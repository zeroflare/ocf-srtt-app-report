# OCF SRTT App 分析報告

台灣常用 App 網路流量與資料流向分析。撰寫指引見 [分析手冊](HANDBOOK.md)。共分析 11 款 App。

網站（hash 路由）：https://zeroflare.github.io/ocf-srtt-app-report/

## 本機預覽

需以靜態伺服器開啟（瀏覽器直接開 `file://` 無法 fetch `.md`）：

```bash
cd ocf-srtt-app-report
python3 -m http.server 8000
```

瀏覽 http://localhost:8000/  
路由範例：`#/`、`#/SUMMARY`、`#/apps/chatgpt`

## 報告

- [綜合報告](SUMMARY.md)
- [行動自然人憑證](apps/twfido.md)
- [警政服務](apps/police.md)
- [健保快易通](apps/nhiapp.md)
- [郵局（中華郵政）](apps/post.md)
- [台鐵e訂通](apps/tra.md)
- [台灣Pay](apps/taiwanpay.md)
- [LINE](apps/line.md)
- [Google 地圖](apps/googlemaps.md)
- [蝦皮購物](apps/shopee.md)
- [ChatGPT](apps/chatgpt.md)
- [智生活](apps/ihome.md)
