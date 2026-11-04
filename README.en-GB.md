# Weiqi (Go)

A browser-based game of Go with Traditional Chinese as the default interface language. Play on a desktop, tablet or phone against the computer, with another person on the same device, or face-to-face around a shared device.

[繁體中文](./README.md)
[English (US)](./README.en-US.md)

## Getting started

Open the deployed GitHub Pages site, or deploy this repository yourself.

- **Repository:** https://github.com/s230127-lab/weiqi_online
- **Main game:** [index.html](./index.html)
- **KataGo ONNX model:** [models/katago.onnx](./models/katago.onnx)
- **Firestore security rules:** [firestore.rules](./firestore.rules)

### Deploying the game

Deploy the entire repository to a static web host and keep the `models/` directory and its model file in place. Open the game over HTTP or HTTPS. Do not open `index.html` directly using a `file://` URL: the browser needs a web server to load the model and other resources.

## First-time guide

The first time you press **Play**, the game opens a five-step guide covering game modes, board sizes, computer levels, placing stones and the game controls. Once you finish or skip it, the browser remembers your choice. You can reopen it at any time using **How to play** in the game toolbar. Clearing the site's browser data may cause the guide to appear again.

## Features

- **Three game modes:** play against the computer, two players on one device, or face-to-face.
- **Six computer levels:** from simple local strategies to KataGo and a legacy Leela Zero browser engine.
- **Three board sizes:** 9×9, 13×13 and 19×19.
- **Colour choice:** choose to play Black or White in computer mode.
- **Game controls:** undo, pass, resign and start a new game.
- **End-of-game scoring:** mark dead stones manually or use automatic marking, then score using Chinese rules.
- **Face-to-face layout:** place a tablet or laptop between the players; each player's information and controls face their side.
- **Touch and desktop support:** use a mouse or a touch screen on a range of screen sizes.
- **Kaya-style AI analysis:** local KataGo ONNX inference for MCTS position analysis, candidate moves, principal variations, move-by-move game analysis, estimated win-rate charts and an ownership heat map when the model supports it.

## Game modes

| Mode | Description |
| --- | --- |
| Play vs computer | Play against the computer and choose its level and your colour. |
| Two players | Two people take turns using the same board. |
| Face-to-face | Place the device flat between the players, with Black at the bottom and White at the top. Each player's information and controls are oriented towards them. |

Face-to-face mode is designed for a shared tablet or laptop. Resigning requires a second confirmation to reduce accidental resignations.

## Computer levels

| Level | Name | How it plays |
| --- | --- | --- |
| Lv 1 | Beginner | Chooses a legal move at random. |
| Lv 2 | Easy | Uses basic tactical rules, such as capturing, responding to atari and avoiding self-atari. |
| Lv 3 | Intermediate | Uses Monte Carlo simulations to evaluate candidate moves. |
| Lv 4 | Advanced | Uses more simulations and usually takes longer to think than Lv 3. |
| Lv 5 | KataGo AI | Runs inference locally using the ONNX model included in this repository. |
| Lv 6 | Leela Zero (legacy WebGL) | Uses a 2017 browser port; supports 19×19 boards only. |

Levels 1–5 can be used with 9×9, 13×13 and 19×19 boards. The legacy Leela Zero level supports 19×19 only. The computer runs in your browser; thinking time varies with device performance and board size.

## KataGo and Kaya-style analysis

The KataGo ONNX model is stored at:

```text
models/katago.onnx
```

The game loads this model when you select **Lv 5** in computer mode or start AI analysis. Other computer levels do not need this model.

- **First load:** the browser loads around 75 MB of model data and the ONNX Runtime Web library, so an internet connection is required the first time.
- **Later use:** the browser attempts to cache the model. If the cache remains available, another download may not be needed.
- **Local inference:** once loaded, move generation and analysis run on your device in the browser. The game does not send the board position to a remote AI inference API.
- **Position analysis:** **Analyse current position** runs PUCT Monte Carlo tree search and displays candidate moves, visit counts, an estimated Black win rate, an estimated score and continuations for leading candidates.
- **Search visits:** choose the number of visits per position. More visits generally mean longer analysis and greater device workload.
- **Full-game analysis:** review positions before each move, compare the played move with the search's top choice, and inspect estimated win-rate changes and move summaries.
- **Charts and ownership:** the game can display an estimated Black win-rate chart. The ownership heat map is available only when the current model provides KataGo ownership output; the interface will not substitute an unrelated estimate.
- **Scope:** this integrates Kaya-inspired KataGo ONNX and PUCT analysis concepts into this single-page game. It is not the full Kaya application and does not include all Kaya settings or its complete analysis-tree browser.

Win rates, score estimates, candidate moves and move assessments are model estimates based on a limited search. They are not guaranteed outcomes or definitive playing strengths.

## Leela Zero (Lv 6)

Lv 6 uses the browser port of [NTT123/leela-zero](https://github.com/NTT123/leela-zero/tree/565ae3380946546504bb38c67a714b6c70300a23/src/docs). This is a legacy 2017 WebGL/WebAssembly demonstration, not the current Leela Zero desktop engine.

- On first use, the game loads the engine and neural-network data from a pinned third-party CDN version.
- Neural-network inference runs through browser WebGL; the engine runs through WebAssembly.
- A WebGL-capable browser is required, and only 19×19 boards are supported.
- The legacy engine and network may not work on some newer browsers or devices.
- The original source code is licensed under GPL-3.0. See the [upstream licence](https://github.com/NTT123/leela-zero/blob/565ae3380946546504bb38c67a714b6c70300a23/COPYING). Refer to the upstream project for rights notices covering the browser port and model files.

## Basic rules

1. Black plays first, then the players alternate, placing one stone on a legal intersection per turn.
2. Connected stones of the same colour form a group. Empty adjacent intersections are called **liberties**. A group with no liberties is captured and removed.
3. A move that leaves your own group with no liberties is suicide and is illegal, unless the move captures opposing stones.
4. Positional superko is enforced: a move may not recreate an earlier board position.
5. Two consecutive passes end the game.
6. At the end, both players confirm and mark dead groups before scoring. You can mark groups individually or use **Auto-mark dead stones**; both players should still check the automatic result.

### Scoring

The game uses **Chinese area scoring**: count living stones and the empty points surrounded by each colour.

- 13×13 and 19×19 boards: Black gives White **7.5 points**.
- 9×9 boards: this game uses a **7-point komi** for Black.

## Controls

- **Place a stone:** click or tap an intersection.
- **Undo:** return to the previous position.
- **Timer:** select **Stop timer** to pause the clock and lock the game so that no moves or game actions can be made. The timer also pauses automatically during KataGo position or full-game analysis, then resumes when analysis ends.
- **Pass:** skip your turn.
- **Resign:** end the game. Face-to-face mode asks for confirmation again.
- **Count:** after two consecutive passes, mark dead groups and select **Count** to calculate the result.
- **New game:** select **New game** to restart, or change the mode, board size, colour or computer level.

## Language, sign-in and leaderboard

The game provides Traditional Chinese and English interfaces. Change the language on the welcome screen or in the game toolbar; the selection is saved in the current browser.

The leaderboard uses Google sign-in with Firebase Authentication and Cloud Firestore. The game and local computer opponents remain playable without Firebase, but sign-in, saving results and the leaderboard will not work until Firebase is configured. To enable them:

1. Create a Firebase project in the [Firebase Console](https://console.firebase.google.com/) and add a Web App.
2. Enable **Google** under **Authentication → Sign-in method**.
3. Create a **Firestore Database**.
4. Add the Web App configuration values (`apiKey`, `authDomain`, `projectId` and `appId`) to `FIREBASE_CONFIG` in `index.html`. These are public browser configuration values, not Admin SDK secrets. Never put a service-account private key in the web page.
5. Add the GitHub Pages domain or your custom domain under Firebase Authentication's **Authorised domains**.
6. Publish this repository's [firestore.rules](./firestore.rules) as your Firestore Rules.

The leaderboard is for casual use, not cheat-proof competitive play. Games run in the browser and the server does not independently verify the full move record. A competitive service should add server-side game-record validation.

## Technical notes

- Single-page web application; the main game code is in `index.html`.
- HTML Canvas is used to draw the board and stones.
- The KataGo model is in ONNX format and runs in the browser through ONNX Runtime Web.
- Playing against computer levels 1–6 and using local AI inference do not require your own AI inference API or API key. Google sign-in and the leaderboard require Firebase configuration.
- Local AI inference and the Firebase leaderboard are separate features; you do not need to sign in to play against the computer.

## Licences and models

This README describes how to use the project. The game code, KataGo model, ONNX Runtime Web, Leela Zero browser port and associated model files may each be subject to different licence terms. Check the original project and the licence information supplied with each component before using, redistributing or modifying those files.
