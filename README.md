# OCF SRTT App 分析報告

台灣常用 App 網路流量與資料流向分析。撰寫指引見 [分析手冊](分析手冊.md)。共分析 11 款 App。

網站（hash 路由）：https://zeroflare.github.io/ocf-srtt-app-report/

## 本機預覽

需以靜態伺服器開啟（瀏覽器直接開 `file://` 無法 fetch `.md`）：

```bash
cd ocf-srtt-app-report
python3 -m http.server 8000
```

瀏覽 http://localhost:8000/  
路由範例：`#/`、`#/綜合報告`、`#/apps/ChatGPT`

## 報告

- [綜合報告](綜合報告.md)
- [行動自然人憑證](apps/行動自然人憑證.md)
- [警政服務](apps/警政服務.md)
- [健保快易通](apps/健保快易通.md)
- [郵局（中華郵政）](apps/郵局.md)
- [台鐵e訂通](apps/台鐵e訂通.md)
- [台灣Pay](apps/台灣Pay.md)
- [LINE](apps/LINE.md)
- [Google 地圖](apps/Google地圖.md)
- [蝦皮購物](apps/蝦皮購物.md)
- [ChatGPT](apps/ChatGPT.md)
- [智生活](apps/智生活.md)
