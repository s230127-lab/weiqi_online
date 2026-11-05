# 围棋 Weiqi

以简体中文为默认界面的浏览器围棋游戏。可在电脑、平板和手机上游玩，支持人机对弈、同机双人对弈，以及面对面对弈。

[繁體中文](./README.md) · [简体中文](./README.zh-CN.md) · [English (UK)](./README.en-GB.md) · [English (US)](./README.en-US.md)

## 项目简介

本项目是一个以单个网页为核心的围棋游戏。游戏不需要自建 AI 推理服务器；一般电脑等级和 KataGo ONNX 模型都在浏览器内运行。Google 登录和 Firebase 仅用于保存成绩及显示排行榜。

- **游戏网站：** [GitHub Pages](https://s230127-lab.github.io/weiqi_online/)
- **GitHub 仓库：** https://github.com/s230127-lab/weiqi_online
- **主要游戏程序：** [index.html](./index.html)
- **KataGo ONNX 模型：** [models/katago.onnx](./models/katago.onnx)
- **Firestore 安全规则：** [firestore.rules](./firestore.rules)
- **Leela Zero 许可文件：** [LEELA_ZERO_LICENSE.txt](./LEELA_ZERO_LICENSE.txt)

## 游戏功能

- **三种对弈模式：** 人机对弈、双人对弈、面对面对弈。
- **六种电脑等级：** Lv 1–4 使用本地策略／模拟；Lv 5 使用 KataGo ONNX；Lv 6 使用旧版 Leela Zero 浏览器引擎。
- **三种棋盘尺寸：** 9 路、13 路和 19 路。
- **棋色选择：** 人机模式可选择执黑或执白。
- **对局操作：** 悔棋、虚手、认输、新游戏及自动重新开始选项。
- **计时器：** 显示对局用时，可手动停止及继续。
- **终局计分：** 手动标记死子或使用自动标记，再按中国规则数子。
- **面对面布局：** 共用一台平板或笔记本电脑时，白方信息朝向上方，黑方信息朝向下方。
- **新手引导：** 提供玩法、棋盘、落子和对局操作说明，可在游戏中重新打开。
- **对局结果页：** 显示胜负或数子结果；数子结束后提供黑白双方得分图表。
- **AI 分析：** 提供局面分析、候选着手、主要变化及整盘逐手分析。

## 开始游戏

打开 [游戏网站](https://s230127-lab.github.io/weiqi_online/) 即可游玩。建议使用较新版本的 Chrome、Edge、Firefox 或 Safari。

第一次使用 Lv 5 时，浏览器会从本仓库加载约 75 MB 的 KataGo ONNX 模型。首次加载需要网络连接；如果浏览器支持且缓存仍存在，之后可能可以重用模型。

## 对弈模式

| 模式 | 说明 |
| --- | --- |
| 人机对弈 | 与电脑对弈。可选择 Lv 1–Lv 6、棋盘尺寸，以及自己执黑或执白。 |
| 双人对弈 | 两位玩家轮流使用同一台设备和同一个棋盘。 |
| 面对面对弈 | 将平板或笔记本电脑平放在两位玩家中间；白方坐上方、黑方坐下方，双方信息及操作按钮会朝向各自一侧。 |

面对面模式适合共用一台设备。认输需要再次确认，以减少误触。

## 电脑等级

| 等级 | 名称 | 下棋方式及限制 |
| --- | --- | --- |
| Lv 1 | 入门 | 随机选择合法棋步。 |
| Lv 2 | 初级 | 使用基本战术规则，例如提子、应对叫吃及避免自打吃。 |
| Lv 3 | 中级 | 使用蒙特卡罗模拟评估候选棋步。 |
| Lv 4 | 高级 | 使用更多模拟思考，通常比 Lv 3 花费更长时间。 |
| Lv 5 | KataGo AI | 使用本仓库内的 ONNX 模型，在浏览器本地推理。 |
| Lv 6 | Leela Zero（旧版 WebGL） | 使用 2017 年的浏览器移植版；只支持 19 路棋盘。 |

Lv 1–Lv 5 支持 9 路、13 路和 19 路棋盘。Lv 6 只支持 19 路棋盘。实际思考时间会因设备性能和棋盘尺寸而异。

## KataGo 与 Kaya 风格 AI 分析

KataGo 模型文件位于 `models/katago.onnx`，大小约 75 MB。游戏会在使用 Lv 5 或启动 KataGo 分析时加载模型；其他电脑等级不需要此模型。

- **模型位置：** 模型存放在本项目的 `models/` 文件夹，不需要自行寻找外部模型文件。
- **推理方式：** 通过 ONNX Runtime Web 和 WebAssembly 在浏览器内运行，不会把棋盘局面发送到远程 AI 推理 API。
- **首次加载：** 浏览器需要下载模型数据和 ONNX Runtime Web 程序库；之后可能使用浏览器缓存。
- **局面分析：** “分析当前局面”使用 PUCT MCTS 搜索，显示候选着手、搜索访问次数、黑方胜率估计、目数估计及主要变化。
- **搜索量：** 可选择每个局面的 12、24、48 或 96 次访问。较高访问次数通常需要更多时间和设备资源。
- **整盘逐手分析：** 逐一分析棋谱中的局面，对照实际着手与模型首选，并显示估计胜率走势及逐手评价。
- **领地热图：** 只有模型提供 ownership 输出时才可使用；模型没有提供该输出时，界面不会用其他估算冒充热图。
- **功能范围：** 本项目整合 Kaya 风格的 KataGo ONNX／PUCT 分析概念，并不是完整的 Kaya 应用程序，也不包含 Kaya 的全部设置或完整分析树浏览器。

**计时器行为：** 分析当前局面时，计时器会自动暂停，并在分析结束后恢复。按下“整盘逐手分析”时，计时器不会暂停。

胜率、目数、候选棋步及逐手评价都是有限搜索量下的模型估计，不是保证结果，也不是正式比赛胜率。

## Leela Zero（Lv 6）

Lv 6 使用 [NTT123/leela-zero 的浏览器版移植](https://github.com/NTT123/leela-zero/tree/565ae3380946546504bb38c67a714b6c70300a23/src/docs)。这是 2017 年的旧版 WebGL／WebAssembly 示例，不是最新 Leela Zero 桌面引擎。

- 首次使用时会从固定版本的第三方 CDN 加载引擎程序及神经网络数据。
- 神经网络推理通过浏览器 WebGL 执行；引擎使用 WebAssembly 执行。
- 需要支持 WebGL 的浏览器，只支持 19 路棋盘。
- 旧版程序及模型可能不适用于部分较新的浏览器或设备。
- 请参阅本仓库的 [Leela Zero 许可文件](./LEELA_ZERO_LICENSE.txt)及[上游许可信息](https://github.com/NTT123/leela-zero/blob/565ae3380946546504bb38c67a714b6c70300a23/COPYING)。

## 基本规则

1. **黑先白后：** 黑方先行，之后双方轮流在棋盘交叉点下一子。
2. **气与提子：** 相连的同色棋子形成棋块。棋块相邻的空交叉点称为“气”；棋块没有任何气时，会被提走。
3. **禁着点／自杀：** 落子后自己的棋块没有气，而且没有因此提走对方棋子，该步就是自杀手，不可下。
4. **禁止全局同形：** 不可使棋盘重现先前出现过的任何局面。
5. **终局：** 双方连续虚手即结束对局。
6. **死子及数子：** 终局时双方标记死子，再按“数子”计算。可逐一点击棋块，也可使用自动标记；自动结果仍建议由双方确认。

### 计分方式

游戏采用**中国规则的数子法**，计算存活棋子及该方围住的空点。

- **9 路：** 黑贴 7 目（相当于 3 又 1/2 子）。
- **13 路和 19 路：** 黑贴 7.5 目（相当于 3 又 3/4 子）。

## 操作说明

- **落子：** 点击或触控棋盘交叉点。
- **悔棋：** 返回之前的局面。
- **虚手：** 跳过当前回合。
- **认输：** 结束对局；面对面模式会要求再次确认。
- **停止计时：** 暂停计时并锁定棋局；继续计时后才能再次操作。
- **数子：** 双方连续虚手后，确认死子并计算分数。
- **新游戏：** 重新开始，并可更改模式、棋盘尺寸、棋色及电脑等级。

## 语言与新手引导

游戏界面支持繁体中文和英文，可在欢迎页或游戏画面上方切换。语言选择会保存在当前浏览器。

第一次游玩时会显示新手引导。引导可以跳过，也可以在游戏画面按“新手引导”重新打开。引导完成状态保存在当前浏览器；清除网站数据后可能再次显示。

## Google 登录与 Firebase 排行榜

Google 登录不是开始游戏的必要条件。未设置 Firebase 时，棋盘、规则和本地电脑对弈仍可使用；登录、保存成绩和排行榜则不会运行。

本游戏使用 Firebase Authentication 和 Cloud Firestore：

- `leaderboard/{uid}`：玩家总成绩，包括胜、负、和、总局数及积分。
- `levelLeaderboard/{level}/players/{uid}`：各电脑等级的最快胜局时间记录。
- 胜局 3 分、和局 1 分、负局 0 分。
- 主游戏的排行榜面板显示总积分前 10 名；独立的 [Ranking-of-weiqi_online 仓库](https://github.com/s230127-lab/Ranking-of-weiqi_online)也提供只显示总排行榜的网站。
- 玩家需要使用 Google 登录，成绩才能保存。游戏只允许登录者写入自己的记录；排行榜数据可公开读取。

### 为自己的部署配置 Firebase

当前程序已包含 `weiqi-online` 项目的网页配置。如果要使用自己的 Firebase 项目，请按以下步骤操作：

1. 在 [Firebase Console](https://console.firebase.google.com/) 创建项目并添加 Web App。
2. 在 **Authentication → Sign-in method** 中启用 **Google** 登录。
3. 创建 **Cloud Firestore** 数据库。
4. 将自己的 Web App 配置值填入 `index.html` 中的 `FIREBASE_CONFIG`。
5. 在 Firebase Authentication 的 **Authorized domains** 中添加实际部署的 GitHub Pages 域名或自定义域名。
6. 将本仓库的 [firestore.rules](./firestore.rules) 发布到 Firestore Rules。

网页端 Firebase 配置不是 Admin SDK 私钥。切勿将服务账户私钥或其他服务器秘密放进前端代码。

**安全限制：** 排行榜用于一般游戏记录，不是防作弊竞技服务器。对局和计时在浏览器端运行，服务器不会重新验证完整棋谱或玩家设备的计时过程。

## 本地预览与自行部署

请通过 HTTP 或 HTTPS 网址打开游戏，不要直接使用 `file://` 打开 `index.html`。浏览器需要通过网站服务器加载模型和其他资源。

在项目根目录运行：

```bash
python -m http.server 8000
```

然后打开 http://localhost:8000/。

部署到 GitHub Pages 或其他静态网站主机时，请保留整个项目结构，尤其是 `models/katago.onnx`、`leela-engine.html`、`firestore.rules` 和许可文件。

### GitHub Pages 设置

1. 打开仓库的 **Settings → Pages**。
2. 在 **Build and deployment** 中选择 **Deploy from a branch**。
3. 选择 `main` 分支和 `/(root)` 目录。
4. 保存设置，等待 GitHub Pages 完成部署。

网站地址：https://s230127-lab.github.io/weiqi_online/

## 项目结构

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

## 自动检查

GitHub Actions 中的 **Validate game JavaScript** 工作流程会检查 `index.html` 和 `leela-engine.html` 中内嵌 JavaScript 的语法。对这两个文件的 push 或 pull request 会触发检查。

## 许可与第三方组件

本仓库包含不同来源和许可条件的组件。游戏程序、KataGo 模型、ONNX Runtime Web、Chart.js、Firebase SDK、Leela Zero 浏览器移植版及相关模型可能分别适用不同条款。使用、分发或修改相关文件前，请查阅各组件原始项目及随附的许可信息。
