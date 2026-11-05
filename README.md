# 圍棋 Weiqi

以繁體中文為預設介面的瀏覽器圍棋遊戲。可在電腦、平板及手機上遊玩，支援人機對弈、同機雙人對弈，以及面對面對弈。

[繁體中文](./README.md) · [简体中文](./README.zh-CN.md) · [English (UK)](./README.en-GB.md) · [English (US)](./README.en-US.md)

## 專案簡介

本專案是一個以單一網頁為核心的圍棋遊戲。遊戲不需要自建 AI 推論伺服器；一般電腦等級及 KataGo ONNX 模型都在瀏覽器內運算。Google 登入與 Firebase 只用於保存成績及顯示排行榜。

- **遊戲網站：** [GitHub Pages](https://s230127-lab.github.io/weiqi_online/)
- **GitHub 儲存庫：** https://github.com/s230127-lab/weiqi_online
- **主要遊戲程式：** [index.html](./index.html)
- **KataGo ONNX 模型：** [models/katago.onnx](./models/katago.onnx)
- **Firestore 安全規則：** [firestore.rules](./firestore.rules)
- **Leela Zero 授權文件：** [LEELA_ZERO_LICENSE.txt](./LEELA_ZERO_LICENSE.txt)

## 遊戲功能

- **三種對弈模式：** 人機對弈、雙人對弈、面對面對弈。
- **六種電腦等級：** Lv 1–4 使用本機策略／模擬；Lv 5 使用 KataGo ONNX；Lv 6 使用舊版 Leela Zero 瀏覽器引擎。
- **三種棋盤尺寸：** 9 路、13 路及 19 路。
- **棋色選擇：** 人機模式可選擇執黑或執白。
- **對局操作：** 悔棋、虛手、認輸、新遊戲及自動重新開始選項。
- **計時器：** 顯示對局用時，可手動停止及繼續。
- **終局計分：** 手動標記死子或使用自動標記，再按中國規則數子。
- **面對面版面：** 共用一台平板或筆電時，白方資訊朝向上方、黑方資訊朝向下方。
- **新手導覽：** 提供玩法、棋盤、落子與對局操作說明，可在遊戲中重新開啟。
- **對局結果頁：** 顯示勝負或數子結果；數子結束時提供黑白雙方得分圖表。
- **AI 分析：** 提供局面分析、候選著手、主要變化及整盤逐手分析。

## 開始遊戲

開啟 [遊戲網站](https://s230127-lab.github.io/weiqi_online/) 即可遊玩。建議使用近期版本的 Chrome、Edge、Firefox 或 Safari。

第一次使用 Lv 5 時，瀏覽器會從本儲存庫載入約 75 MB 的 KataGo ONNX 模型。首次載入需要網路連線；瀏覽器可在支援且快取仍存在時重用模型。

## 對弈模式

| 模式 | 說明 |
| --- | --- |
| 人機對弈 | 與電腦對弈。可選擇 Lv 1–Lv 6、棋盤尺寸，以及自己執黑或執白。 |
| 雙人對弈 | 兩位玩家輪流使用同一台裝置及同一個棋盤。 |
| 面對面對弈 | 將平板或筆電平放在兩位玩家中間；白方坐上方、黑方坐下方，雙方資訊及操作按鈕會朝向各自一側。 |

面對面模式適合共用一台裝置。認輸需要再次確認，以減少誤觸。

## 電腦等級

| 等級 | 名稱 | 下棋方式及限制 |
| --- | --- | --- |
| Lv 1 | 入門 | 隨機選擇合法棋步。 |
| Lv 2 | 初級 | 使用基本戰術規則，例如提子、應對叫吃及避免自打吃。 |
| Lv 3 | 中級 | 使用蒙地卡羅模擬評估候選棋步。 |
| Lv 4 | 高級 | 使用較多模擬思考，通常比 Lv 3 花費更長時間。 |
| Lv 5 | KataGo AI | 使用本儲存庫內的 ONNX 模型，在瀏覽器本機推論。 |
| Lv 6 | Leela Zero（舊版 WebGL） | 使用 2017 年的瀏覽器移植版；只支援 19 路棋盤。 |

Lv 1–Lv 5 支援 9 路、13 路及 19 路棋盤。Lv 6 只支援 19 路棋盤。實際思考時間會因裝置效能及棋盤尺寸而異。

## KataGo 與 Kaya 風格 AI 分析

KataGo 模型檔案位於 `models/katago.onnx`。檔案大小約 75 MB。遊戲會在使用 Lv 5 或啟動 KataGo 分析時載入模型；其他電腦等級不需要此模型。

- **模型位置：** 模型存放在本專案的 `models/` 資料夾，不需要自行尋找外部模型檔案。
- **推論方式：** 透過 ONNX Runtime Web 及 WebAssembly 在瀏覽器內執行，不會將棋盤位置傳送到遠端 AI 推論 API。
- **首次載入：** 瀏覽器需要下載模型資料及 ONNX Runtime Web 程式庫；後續可能使用瀏覽器快取。
- **局面分析：** 「分析目前局面」使用 PUCT MCTS 搜索，顯示候選著手、搜索訪問數、黑方勝率估計、目數估計及主要變化。
- **搜索量：** 可選擇每個局面的 12、24、48 或 96 次訪問。較高訪問數通常需要更多時間及裝置資源。
- **整盤逐手分析：** 逐一分析棋譜中的局面，對照實際著手與模型首選，並顯示估計勝率走勢及逐手評價。
- **領地熱圖：** 只有模型提供 ownership 輸出時才可使用；模型沒有提供該輸出時，介面不會以其他估算冒充熱圖。
- **功能範圍：** 本專案整合 Kaya 風格的 KataGo ONNX／PUCT 分析概念，並不是完整的 Kaya 應用程式，也不包含 Kaya 的所有設定或完整分析樹瀏覽器。

**計時器行為：** 分析目前局面時，計時器會自動暫停，分析結束後恢復。按下「整盤逐手分析」時，計時器不會暫停。

勝率、目數、候選棋步及逐手評價都是有限搜索量下的模型估計，不是保證結果，也不是正式比賽勝率。

## Leela Zero（Lv 6）

Lv 6 使用 [NTT123/leela-zero 的瀏覽器版移植](https://github.com/NTT123/leela-zero/tree/565ae3380946546504bb38c67a714b6c70300a23/src/docs)。這是 2017 年的舊版 WebGL／WebAssembly 示範，不是最新 Leela Zero 桌面引擎。

- 首次使用時會從固定版本的第三方 CDN 載入引擎程式及神經網路資料。
- 神經網路推論透過瀏覽器 WebGL 執行；引擎使用 WebAssembly 執行。
- 需要支援 WebGL 的瀏覽器，只支援 19 路棋盤。
- 舊版程式及模型可能不適用於部分較新的瀏覽器或裝置。
- 請參閱本儲存庫的 [Leela Zero 授權文件](./LEELA_ZERO_LICENSE.txt)及[上游授權資訊](https://github.com/NTT123/leela-zero/blob/565ae3380946546504bb38c67a714b6c70300a23/COPYING)。

## 基本規則

1. **黑先白後：** 黑方先行，之後雙方輪流在棋盤交叉點下一子。
2. **氣與提子：** 相連的同色棋子形成棋塊。棋塊相鄰的空交叉點稱為「氣」；棋塊沒有任何氣時，會被提走。
3. **禁著點／自殺：** 落子後自己的棋塊沒有氣，而且沒有因此提走對方棋子，該步便是自殺手，不可下。
4. **禁止全局同形：** 不可使棋盤重現先前出現過的任何局面。
5. **終局：** 雙方連續虛手即結束對局。
6. **死子及數子：** 終局時雙方標記死子，再按「數子」計算。可逐一點選棋塊，也可使用自動標記；自動結果仍建議由雙方確認。

### 計分方式

遊戲採用**中國規則的數子法**，計算存活棋子及該方圍住的空點。

- **9 路：** 黑貼 7 目（相當於 3 又 1/2 子）。
- **13 路及 19 路：** 黑貼 7.5 目（相當於 3 又 3/4 子）。

## 操作說明

- **落子：** 點擊或觸控棋盤交叉點。
- **悔棋：** 返回之前的局面。
- **虛手：** 跳過目前回合。
- **認輸：** 結束對局；面對面模式會要求再次確認。
- **停止計時：** 暫停計時並鎖定棋局；繼續計時後才能再次操作。
- **數子：** 雙方連續虛手後，確認死子並計算分數。
- **新遊戲：** 重新開始，並可更改模式、棋盤尺寸、棋色及電腦等級。

## 語言與新手導覽

遊戲介面支援繁體中文及英文，可在歡迎頁或遊戲畫面上方切換。語言選擇會保存在目前瀏覽器。

第一次遊玩時會顯示新手導覽。導覽可略過，亦可在遊戲畫面按「新手導覽」再次開啟。導覽完成狀態保存在目前瀏覽器；清除網站資料後可能再次顯示。

## Google 登入與 Firebase 排行榜

Google 登入不是開始遊戲的必要條件。未設定 Firebase 時，棋盤、規則及本機電腦對弈仍可使用；登入、保存成績及排行榜則不會運作。

本遊戲使用 Firebase Authentication 及 Cloud Firestore：

- `leaderboard/{uid}`：玩家總成績，包括勝、敗、和、總局數及積分。
- `levelLeaderboard/{level}/players/{uid}`：各電腦等級的最快勝局時間紀錄。
- 勝局 3 分、和局 1 分、敗局 0 分。
- 主遊戲的排行榜面板顯示總積分前 10 名；獨立的 [Ranking-of-weiqi_online 儲存庫](https://github.com/s230127-lab/Ranking-of-weiqi_online)亦提供只顯示總排行榜的網站。
- 玩家需使用 Google 登入，成績才可保存。遊戲只允許登入者寫入自己的紀錄；排行榜資料可公開讀取。

### 為自己的部署設定 Firebase

目前程式已包含 `weiqi-online` 專案的網頁設定。如要使用自己的 Firebase 專案，請依照以下步驟：

1. 在 [Firebase Console](https://console.firebase.google.com/) 建立專案並新增 Web App。
2. 在 **Authentication → Sign-in method** 啟用 **Google** 登入。
3. 建立 **Cloud Firestore** 資料庫。
4. 將自己的 Web App 設定值填入 `index.html` 中的 `FIREBASE_CONFIG`。
5. 在 Firebase Authentication 的 **Authorized domains** 加入實際部署的 GitHub Pages 網域或自訂網域。
6. 將本專案的 [firestore.rules](./firestore.rules) 發佈到 Firestore Rules。

網頁端 Firebase 設定不是 Admin SDK 私密金鑰。切勿把服務帳戶私密金鑰或其他伺服器秘密放進前端程式碼。

**安全限制：** 排行榜供一般遊戲記錄使用，不是防作弊競技伺服器。對局及計時在瀏覽器端執行，伺服器不會重新驗證完整棋譜或玩家裝置的計時過程。

## 本機預覽與自行部署

請透過 HTTP 或 HTTPS 網址開啟遊戲，不要直接使用 `file://` 開啟 `index.html`。瀏覽器需要透過網站伺服器載入模型及其他資源。

在專案根目錄執行：

```bash
python -m http.server 8000
```

然後開啟 http://localhost:8000/。

部署到 GitHub Pages 或其他靜態網站主機時，請保留整個專案結構，尤其是 `models/katago.onnx`、`leela-engine.html`、`firestore.rules` 及授權文件。

### GitHub Pages 設定

1. 開啟儲存庫的 **Settings → Pages**。
2. 在 **Build and deployment** 選擇 **Deploy from a branch**。
3. 選擇 `main` 分支及 `/(root)` 目錄。
4. 儲存設定，等待 GitHub Pages 完成部署。

網站網址：https://s230127-lab.github.io/weiqi_online/

## 專案結構

```text
.
├── index.html
├── leela-engine.html
├── firestore.rules
├── LEELA_ZERO_LICENSE.txt
├── models/
│   └── katago.onnx
└── .github/
    └── workflows/
        └── validate-game.yml
```

## 自動檢查

GitHub Actions 中的 **Validate game JavaScript** 工作流程會檢查 `index.html` 及 `leela-engine.html` 內嵌 JavaScript 的語法。對這兩個檔案的 push 或 pull request 會觸發檢查。

## 授權與第三方元件

本儲存庫包含不同來源及授權條件的元件。遊戲程式、KataGo 模型、ONNX Runtime Web、Chart.js、Firebase SDK、Leela Zero 瀏覽器移植版及相關模型可能各自適用不同條款。使用、散布或修改相關檔案前，請查閱各元件原始專案及隨附的授權資訊。
