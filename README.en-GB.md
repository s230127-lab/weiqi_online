# Weiqi (Go)

A browser-based game of Go with Traditional Chinese as the default interface language. Play on a desktop, tablet or phone against the computer, with another person on the same device, or face-to-face around a shared device.

[繁體中文](./README.md) · [简体中文](./README.zh-CN.md) · [English (UK)](./README.en-GB.md) · [English (US)](./README.en-US.md)

## About the project

This is a single-page browser game of Go. It does not require a self-hosted AI inference server: the built-in computer levels and the KataGo ONNX model run in the browser. Google sign-in and Firebase are used only for saving results and displaying rankings.

- **Game site:** [GitHub Pages](https://s230127-lab.github.io/weiqi_online/)
- **GitHub repository:** https://github.com/s230127-lab/weiqi_online
- **Main game:** [index.html](./index.html)
- **KataGo ONNX model:** [models/katago.onnx](./models/katago.onnx)
- **Firestore security rules:** [firestore.rules](./firestore.rules)
- **Leela Zero licence file:** [LEELA_ZERO_LICENSE.txt](./LEELA_ZERO_LICENSE.txt)

## Features

- **Three game modes:** play against the computer, two players on one device, or face-to-face play.
- **Six computer levels:** Lv 1–4 use local strategies/simulations; Lv 5 uses KataGo ONNX; Lv 6 uses a legacy Leela Zero browser engine.
- **Three board sizes:** 9×9, 13×13 and 19×19.
- **Colour choice:** choose to play Black or White in computer mode.
- **Game controls:** undo, pass, resign, start a new game and optionally restart automatically.
- **Game timer:** displays elapsed game time and can be stopped and resumed manually.
- **End-game scoring:** mark dead stones manually or use automatic marking, then count using Chinese area scoring.
- **Face-to-face layout:** on a shared tablet or laptop, the White player's information faces the top and the Black player's information faces the bottom.
- **First-time guide:** explains the modes, board, moves and game controls; it can be reopened during play.
- **Result screen:** shows the winner or scoring result; scored games include a chart comparing Black's and White's points.
- **AI analysis:** position analysis, candidate moves, principal variations and move-by-move whole-game analysis.

## Getting started

Open the [game website](https://s230127-lab.github.io/weiqi_online/). A recent version of Chrome, Edge, Firefox or Safari is recommended.

The first time you use Lv 5, your browser loads the approximately 75 MB KataGo ONNX model from this repository. An internet connection is required for the initial load. The browser may reuse a cached copy on later visits if its cache remains available.

## Game modes

| Mode | Description |
| --- | --- |
| Play vs computer | Play against the computer. Choose Lv 1–Lv 6, a board size and whether you play Black or White. |
| Two players | Two people take turns on the same device and board. |
| Face-to-face | Place a tablet or laptop between the players. White sits at the top and Black at the bottom; each player's information and controls face them. |

Face-to-face mode is intended for a shared device. Resigning requires a second confirmation to reduce accidental resignations.

## Computer levels

| Level | Name | Playing method and limits |
| --- | --- | --- |
| Lv 1 | Beginner | Selects a legal move at random. |
| Lv 2 | Easy | Uses basic tactical rules, such as captures, responding to atari and avoiding self-atari. |
| Lv 3 | Intermediate | Uses Monte Carlo simulations to evaluate candidate moves. |
| Lv 4 | Advanced | Uses more simulations and usually takes longer to think than Lv 3. |
| Lv 5 | KataGo AI | Uses the ONNX model in this repository for inference in the browser. |
| Lv 6 | Leela Zero (legacy WebGL) | Uses a 2017 browser port; supports 19×19 boards only. |

Levels 1–5 support 9×9, 13×13 and 19×19 boards. Lv 6 supports 19×19 only. Thinking time varies with device performance and board size.

## KataGo and Kaya-style AI analysis

The KataGo model is stored at `models/katago.onnx` and is approximately 75 MB. The game loads it when you use Lv 5 or start KataGo analysis; the other computer levels do not need this model.

- **Model location:** the model is included in this repository's `models/` folder; you do not need to find a separate model file.
- **Inference:** ONNX Runtime Web and WebAssembly run the model in your browser. Board positions are not sent to a remote AI inference API.
- **First load:** the browser downloads the model data and the ONNX Runtime Web library. A browser cache may be used on later visits.
- **Position analysis:** **Analyse current position** runs PUCT MCTS search and displays candidate moves, visit counts, an estimated Black win rate, an estimated score lead and principal variations.
- **Search visits:** choose 12, 24, 48 or 96 visits per position. More visits generally require more time and device resources.
- **Whole-game move-by-move analysis:** reviews positions throughout the recorded game, compares each played move with the model's top choice, and displays estimated win-rate changes and move assessments.
- **Ownership heat map:** available only when the model supplies ownership output. The interface does not substitute an unrelated estimate when that output is absent.
- **Scope:** this project integrates Kaya-inspired KataGo ONNX/PUCT analysis concepts. It is not the full Kaya application and does not include all Kaya settings or its complete analysis-tree browser.

**Timer behaviour:** analysing the current position pauses the timer automatically; the timer resumes when that analysis finishes. Selecting **Analyse every move** (whole-game analysis) does not pause the timer.

Win rates, score estimates, candidate moves and move assessments are model estimates based on a limited search. They are not guaranteed outcomes or official tournament win rates.

## Leela Zero (Lv 6)

Lv 6 uses the browser port of [NTT123/leela-zero](https://github.com/NTT123/leela-zero/tree/565ae3380946546504bb38c67a714b6c70300a23/src/docs). This is a legacy 2017 WebGL/WebAssembly demonstration, not the current Leela Zero desktop engine.

- On first use, the engine code and neural-network data are loaded from a pinned third-party CDN version.
- Neural-network inference runs through browser WebGL; the engine uses WebAssembly.
- A WebGL-capable browser is required, and only 19×19 boards are supported.
- The legacy code and network may not work on some newer browsers or devices.
- See this repository's [Leela Zero licence file](./LEELA_ZERO_LICENSE.txt) and the [upstream licence information](https://github.com/NTT123/leela-zero/blob/565ae3380946546504bb38c67a714b6c70300a23/COPYING).

## Basic rules

1. **Black plays first:** players then take turns placing one stone on a legal board intersection.
2. **Liberties and capture:** connected stones of the same colour form a group. Empty adjacent intersections are its liberties. A group with no liberties is captured and removed.
3. **Suicide:** a move that leaves your own group without liberties is illegal unless it captures opposing stones.
4. **Positional superko:** a move may not recreate any earlier board position.
5. **Ending the game:** two consecutive passes end the game.
6. **Dead stones and counting:** both players mark dead groups before selecting **Count**. Groups can be marked individually or with the automatic marking feature; both players should check the automatic result.

### Scoring

The game uses **Chinese area scoring**: count living stones and the empty points surrounded by each colour.

- **9×9:** Black gives White 7 points of komi (equivalent to 3½ stones).
- **13×13 and 19×19:** Black gives White 7.5 points of komi (equivalent to 3¾ stones).

## Controls

- **Place a stone:** click or tap an intersection.
- **Undo:** return to the previous position.
- **Pass:** skip the current turn.
- **Resign:** end the game. Face-to-face mode asks for confirmation again.
- **Stop timer:** pause the clock and lock the game; play can resume when the timer is restarted.
- **Count:** after two consecutive passes, confirm dead stones and calculate the score.
- **New game:** restart and, if required, change the mode, board size, colour or computer level.

## Language and first-time guide

The game interface supports Traditional Chinese and English. Change the language on the welcome screen or in the game toolbar; the selection is saved in the current browser.

A first-time guide explains the basics. It can be skipped and reopened at any time using **How to play**. Completion is stored in the current browser, so clearing the site's browser data may cause the guide to appear again.

## Google sign-in and Firebase leaderboard

Google sign-in is not required to play. Without Firebase, the board, rules and local computer opponents remain available; sign-in, result saving and leaderboard loading will not work.

The game uses Firebase Authentication and Cloud Firestore:

- `leaderboard/{uid}`: overall player statistics, including wins, losses, draws, games and points.
- `levelLeaderboard/{level}/players/{uid}`: the fastest recorded winning time for each computer level.
- Scoring is 3 points for a win, 1 point for a draw and 0 points for a loss.
- The in-game leaderboard panel displays the overall top 10 by points. The separate [Ranking-of-weiqi_online repository](https://github.com/s230127-lab/Ranking-of-weiqi_online) also provides a website that displays the overall ranking only.
- Players must sign in with Google to save results. A signed-in player can write their own records; leaderboard data is publicly readable.

### Configure Firebase for your own deployment

The current code includes web-app settings for the `weiqi-online` Firebase project. To use your own project:

1. Create a Firebase project and add a Web App in the [Firebase Console](https://console.firebase.google.com/).
2. Enable **Google** under **Authentication → Sign-in method**.
3. Create a **Cloud Firestore** database.
4. Add your Web App configuration values to `FIREBASE_CONFIG` in `index.html`.
5. Add your GitHub Pages domain or custom domain under Firebase Authentication's **Authorized domains**.
6. Publish this repository's [firestore.rules](./firestore.rules) as your Firestore Rules.

The Firebase web configuration is not an Admin SDK private key. Never put a service-account private key or other server secret in front-end code.

**Security limitation:** the leaderboard is intended for casual game records, not cheat-proof competitive play. Games and timing run in the browser; the server does not independently verify the full move record or the player's device timer.

## Preview locally and deploy

Open the game through an HTTP or HTTPS URL. Do not open `index.html` directly with `file://`; the browser needs a web server to load the model and other resources.

From the repository root, run:

```bash
python -m http.server 8000
```

Then open http://localhost:8000/.

When deploying to GitHub Pages or another static host, preserve the repository structure, especially `models/katago.onnx`, `leela-engine.html`, `firestore.rules` and the licence files.

### GitHub Pages setup

1. Open the repository's **Settings → Pages**.
2. Under **Build and deployment**, choose **Deploy from a branch**.
3. Select the `main` branch and the `/(root)` folder.
4. Save the settings and wait for GitHub Pages to finish deploying.

Website: https://s230127-lab.github.io/weiqi_online/

## Repository structure

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

## Automated checks

The GitHub Actions workflow **Validate game JavaScript** checks the syntax of the inline JavaScript in `index.html` and `leela-engine.html`. Pushes and pull requests that change either file trigger the check.

## Licences and third-party components

This repository includes components from different sources and under different licence terms. The game code, KataGo model, ONNX Runtime Web, Chart.js, Firebase SDK, Leela Zero browser port and related model files may each have separate terms. Check the original projects and the licence information supplied with each component before using, redistributing or modifying those files.
