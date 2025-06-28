<!DOCTYPE html>
<html lang="ja">
<head>
    <meta charset="UTF-8" />
    <title>文化祭まとめサイト</title>
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <link rel="icon" href="favicon.ico" type="image/x-icon">
    <link href="https://fonts.googleapis.com/css2?family=Noto+Sans+JP:wght@300;400;500;700&display=swap" rel="stylesheet">
    <style>
        :root {
            --primary-color: #4CAF50;
            --primary-hover-color: #45a049;
            --secondary-color: #007bff;
            --secondary-hover-color: #0056b3;
            --text-dark: #333;
            --text-light: #f8f8f8;
            --background-gradient-start: #e0f7fa;
            --background-gradient-end: #c8e6c9;
            --shadow-medium: rgba(0, 0, 0, 0.2);
            --accent-color: #ff9800;
        }
        body {
            margin: 0;
            font-family: 'Noto Sans JP', 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
            background: linear-gradient(135deg, var(--background-gradient-start), var(--background-gradient-end));
            color: var(--text-dark);
            text-align: center;
            min-height: 100vh;
            display: flex;
            flex-direction: column;
            justify-content: center;
            align-items: center;
            user-select: none;
            overflow: hidden;
            transition: opacity 0.5s ease-in-out;
            opacity: 0;
        }
        body.loaded {
            opacity: 1;
        }
        body.virus-active {
            background-color: #000 !important;
            margin: 0 !important;
            padding: 0 !important;
            overflow: hidden !important;
            opacity: 1 !important;
        }
        .hidden {
            display: none !important;
        }
        #initial-screen {
            background: linear-gradient(135deg, var(--background-gradient-start), var(--background-gradient-end));
            color: var(--text-dark);
            height: 100vh;
            width: 100vw;
            position: fixed;
            top: 0;
            left: 0;
            z-index: 9997;
            display: flex;
            flex-direction: column;
            justify-content: center;
            align-items: center;
            padding: 1.5rem;
            box-sizing: border-box;
            animation: fadeInScale 0.8s ease-out forwards;
        }
        #initial-screen h1 {
            font-size: 2.8rem;
            color: var(--primary-color);
            margin-bottom: 2.5rem;
            text-shadow: 2px 2px 4px rgba(0,0,0,0.1);
            line-height: 1.3;
        }
        #initial-screen p {
            font-size: 1.2rem;
            margin-bottom: 3rem;
            max-width: 600px;
            line-height: 1.6;
        }
        #virus-screen {
            position: fixed;
            top: 0; left: 0; right: 0; bottom: 0;
            width: 100vw; height: 100vh;
            background-color: #000;
            color: #ff3300;
            z-index: 999999;
            font-family: 'Courier New', Courier, monospace;
            font-size: 1.2rem;
            overflow: hidden;
            text-align: center;
            display: flex;
            flex-direction: column;
            align-items: center;
            justify-content: center;
            box-sizing: border-box;
            pointer-events: none;
            animation: noise 0.1s infinite, flicker 0.2s infinite alternate;
            text-shadow: 0 0 5px #ff0000, 0 0 15px #ff0000;
        }
        #virus-screen::before {
            content: '';
            position: absolute;
            top: 0; left: 0;
            width: 100%; height: 100%;
            background-color: rgba(255, 0, 0, 0.2);
            background-image: url('data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAAQAAAAECAYAAACp8da+AAAAAXNSR0IArs4c6QAAABJJREFUGFdjYGBgYGBgYGAAAgABAAyW/71mAAAAAElFTkSuQmCC');
            background-size: 2px 2px;
            opacity: 0.7;
            pointer-events: none;
            z-index: -1;
        }
        #virus-screen h2 {
            font-size: 2.8rem;
            margin-bottom: 2rem;
            animation: blink-fast 0.3s infinite alternate, glitch 0.5s infinite alternate;
            width: 90%;
            text-align: center;
            box-sizing: border-box;
            text-shadow: 0 0 15px #f00, 0 0 30px #f00;
        }
        #countdown {
            font-size: 7rem;
            font-weight: bold;
            color: #00ff00;
            text-shadow: 0 0 8px #00ff00, 0 0 25px #00ff00;
            margin-bottom: 1.5rem;
            width: 90%;
            text-align: center;
            box-sizing: border-box;
            animation: glitch 0.3s infinite alternate, scanlines 0.1s infinite;
        }
        #reveal-message {
            font-size: 1.5rem;
            color: #f0f0f0;
            background-color: rgba(0, 0, 0, 0.85);
            padding: 1.5rem;
            border-radius: 8px;
            display: none;
            margin-top: 2rem;
            animation: fade-in 0.8s forwards;
            max-width: 85%;
            box-sizing: border-box;
            text-align: center;
            line-height: 1.6;
            border: 1px solid rgba(255, 255, 255, 0.2);
        }
        @keyframes fade-in {
            from { opacity: 0; }
            to { opacity: 1; }
        }
        @keyframes blink-fast {
            0%, 49% { opacity: 1; }
            50%, 100% { opacity: 0.5; }
        }
        @keyframes glitch {
            0% { transform: translate(0); }
            20% { transform: translate(2px, -2px); }
            40% { transform: translate(-1px, 1px); }
            60% { transform: translate(-2px, -1px); }
            80% { transform: translate(1px, 2px); }
            100% { transform: translate(0); }
        }
        @keyframes noise {
            0% { opacity: 0.05; }
            10% { opacity: 0.1; }
            20% { opacity: 0.08; }
            30% { opacity: 0.15; }
            40% { opacity: 0.03; }
            50% { opacity: 0.12; }
            60% { opacity: 0.07; }
            70% { opacity: 0.18; }
            80% { opacity: 0.06; }
            90% { opacity: 0.11; }
            100% { opacity: 0.04; }
        }
        @keyframes flicker {
            0%, 100% { opacity: 1; }
            50% { opacity: 0.8; }
        }
        @keyframes scanlines {
            0% { background-position: 0 0; }
            100% { background-position: 0 20px; }
        }
        #relief-screen {
            background: linear-gradient(45deg, #e0f7fa, #b2ebf2);
            color: #006064;
            height: 100vh;
            width: 100vw;
            position: fixed;
            top: 0; left: 0;
            z-index: 9998;
            justify-content: center;
            align-items: center;
            flex-direction: column;
            font-size: 2rem;
            font-weight: 500;
            display: none;
            user-select: text;
            animation: fade-in 0.8s ease-in-out;
            text-shadow: 1px 1px 3px rgba(0,0,0,0.1);
        }
        #relief-screen p {
            margin: 0.8rem 0;
        }
        #main {
            padding: 2.5rem;
            background: transparent;
            min-height: 100vh;
            width: 100vw;
            position: fixed;
            top: 0; left: 0;
            color: var(--text-dark);
            user-select: none;
            display: none;
            border-radius: 16px;
            box-sizing: border-box;
            animation: fadeInScale 0.8s ease-out forwards;
            justify-content: center;
            align-items: center;
            flex-direction: column;
        }
        #main.visible {
            display: flex;
        }
        .nav-buttons {
            display: flex;
            flex-direction: column;
            justify-content: center;
            align-items: center;
            gap: 1.5rem;
            width: 100%;
            max-width: 400px;
        }
        .nav-buttons button {
            width: 90%;
            max-width: 300px;
            background-color: var(--secondary-color);
            border: none;
            padding: 1.4rem 2rem;
            font-size: 1.6rem;
            font-weight: 600;
            border-radius: 10px;
            color: var(--text-light);
            cursor: pointer;
            box-shadow: 0 6px 12px var(--shadow-medium);
            transition: background-color 0.3s ease, transform 0.2s ease, box-shadow 0.3s ease;
            letter-spacing: 0.5px;
            display: flex;
            align-items: center;
            justify-content: center;
            gap: 0.8rem;
            white-space: nowrap;
            box-sizing: border-box;
        }
        .nav-buttons button:hover {
            background-color: var(--secondary-hover-color);
            transform: translateY(-3px) scale(1.02);
            box-shadow: 0 8px 16px var(--shadow-medium);
        }
        .share-icon {
            font-size: 1.6rem;
        }
        @media (max-width: 768px) {
            .nav-buttons button {
                padding: 1.2rem 1.8rem;
                font-size: 1.4rem;
            }
            .share-icon {
                font-size: 1.4rem;
            }
            #virus-screen h2 {
                font-size: 2.2rem;
            }
            #countdown {
                font-size: 5rem;
            }
            #reveal-message {
                font-size: 1.2rem;
                padding: 1.2rem;
            }
            #initial-screen h1 {
                font-size: 2.2rem;
                margin-bottom: 2rem;
            }
            #initial-screen p {
                font-size: 1.1rem;
                margin-bottom: 2rem;
            }
        }
        @media (max-width: 480px) {
            .nav-buttons {
                gap: 1rem;
            }
            .nav-buttons button {
                width: 95%;
                padding: 1rem 1.5rem;
                font-size: 1.3rem;
                max-width: none;
            }
            .share-icon {
                font-size: 1.3rem;
            }
            #virus-screen h2 {
                font-size: 1.8rem;
                margin-bottom: 0.8rem;
            }
            #countdown {
                font-size: 4rem;
            }
            #reveal-message {
                font-size: 1rem;
                padding: 0.8rem;
            }
            #initial-screen h1 {
                font-size: 1.8rem;
                margin-bottom: 1.5rem;
            }
            #initial-screen p {
                font-size: 1.0rem;
                margin-bottom: 1.5rem;
            }
        }
    </style>
</head>
<body>
    <div id="virus-screen" role="alert" aria-live="assertive" aria-atomic="true" class="hidden"></div>

    <div id="relief-screen" class="hidden" role="region" aria-live="polite" aria-atomic="true">
        <p>冗談だよ😊</p>
        <p>文化祭を楽しんで</p>
    </div>

    <div id="initial-screen" role="region" aria-label="文化祭特設サイトへようこそ">
        <h1>ようこそ！文化祭特設サイトへ</h1>
        <p>文化祭の情報を公開しています。</p>
        <div class="nav-buttons">
            <button id="trigger-virus-btn" type="button">
                <span class="share-icon"></span> 文化祭情報を見る
            </button>
        </div>
    </div>

    <div id="main" role="main" aria-label="文化祭まとめコンテンツ" class="hidden">
        <div class="nav-buttons">
            <button id="repeat-virus-btn" type="button">
                <span class="share-icon"></span> もう一度ウイルス演出を見る
            </button>
            <button id="show-bunkasai-info-btn" type="button">
                <span class="share-icon"></span> 文化祭情報
            </button>
            <button id="show-quiz-minigame-btn" type="button">
                <span class="share-icon"></span> クイズ
            </button>
            <button class="share-btn share-x" role="button" tabindex="0" data-sns="x" title="Xで共有" aria-label="Xで共有">
                <span class="share-icon"></span> 𝕏で共有
            </button>
            <button class="share-btn share-line" role="button" tabindex="0" data-sns="line" title="LINEで共有" aria-label="LINEで共有">
                <span class="share-icon"></span> LINEで共有
            </button>
        </div>
    </div>

    <script defer>
        document.addEventListener("DOMContentLoaded", () => {
            const LOCAL_STORAGE_KEY = "bunkasai_visited";
            const VIRUS_COUNTDOWN_SECONDS = 5;
            const QUIZ_SITE_URL = "https://miyashitt.github.io/Quiz/";
            const virusScreen = document.getElementById("virus-screen");
            const reliefScreen = document.getElementById("relief-screen");
            const initialScreen = document.getElementById("initial-screen");
            const mainScreen = document.getElementById("main");
            const triggerVirusBtn = document.getElementById("trigger-virus-btn");
            const repeatVirusBtn = document.getElementById("repeat-virus-btn");
            const showBunkasaiInfoBtn = document.getElementById("show-bunkasai-info-btn");
            const showQuizMinigameBtn = document.getElementById("show-quiz-minigame-btn");
            const shareXBtn = document.querySelector(".share-x");
            const shareLineBtn = document.querySelector(".share-line");
            const revealMessage = document.createElement("div");
            revealMessage.id = "reveal-message";

            let countdownInterval = null;
            let countdownValue = VIRUS_COUNTDOWN_SECONDS;

            // 初期表示時の処理
            function showMain() {
                initialScreen.classList.add("hidden");
                reliefScreen.classList.add("hidden");
                virusScreen.classList.add("hidden");
                mainScreen.classList.remove("hidden");
                mainScreen.classList.add("visible");
                document.body.classList.remove("virus-active");
            }

            function showInitial() {
                initialScreen.classList.remove("hidden");
                mainScreen.classList.add("hidden");
                mainScreen.classList.remove("visible");
                reliefScreen.classList.add("hidden");
                virusScreen.classList.add("hidden");
                document.body.classList.remove("virus-active");
            }

            // ウイルス演出スタート
            function startVirusEffect() {
                initialScreen.classList.add("hidden");
                mainScreen.classList.add("hidden");
                mainScreen.classList.remove("visible");
                reliefScreen.classList.add("hidden");
                virusScreen.classList.remove("hidden");
                virusScreen.innerHTML = `
                    <h2>!!! ウイルスに感染しました !!!</h2>
                    <div id="countdown">${countdownValue}</div>
                `;
                document.body.classList.add("virus-active");

                countdownInterval = setInterval(() => {
                    countdownValue--;
                    const countdownEl = document.getElementById("countdown");
                    if (countdownEl) {
                        countdownEl.textContent = countdownValue;
                    }
                    if (countdownValue <= 0) {
                        clearInterval(countdownInterval);
                        showReliefMessage();
                    }
                }, 1000);
            }

            // ジョーク明かし表示
            function showReliefMessage() {
                virusScreen.innerHTML = `
                    <h2>!!! ウイルスに感染しました !!!</h2>
                    <div id="countdown">0</div>
                `;
                reliefScreen.style.display = "flex";
                reliefScreen.classList.remove("hidden");
                virusScreen.appendChild(revealMessage);
                revealMessage.style.display = "block";
                revealMessage.textContent = "これはジョークです！文化祭を楽しんでね😊";
                setTimeout(() => {
                    reliefScreen.style.display = "none";
                    reliefScreen.classList.add("hidden");
                    virusScreen.classList.add("hidden");
                    revealMessage.style.display = "none";
                    showMain();
                    localStorage.setItem(LOCAL_STORAGE_KEY, "true");
                    countdownValue = VIRUS_COUNTDOWN_SECONDS;
                }, 3000);
            }

            // イベント登録
            triggerVirusBtn.addEventListener("click", startVirusEffect);

            repeatVirusBtn.addEventListener("click", startVirusEffect);

            showBunkasaiInfoBtn.addEventListener("click", () => {
                window.open("https://miyashitt.github.io/", "_blank");
            });

            showQuizMinigameBtn.addEventListener("click", () => {
                window.open(QUIZ_SITE_URL, "_blank");
            });

            shareXBtn.addEventListener("click", () => {
                const url = encodeURIComponent(location.href);
                const text = encodeURIComponent("文化祭まとめサイトはこちら！");
                const shareUrl = `https://twitter.com/intent/tweet?text=${text}&url=${url}`;
                window.open(shareUrl, "_blank", "noopener,noreferrer");
            });

            shareLineBtn.addEventListener("click", () => {
                const url = encodeURIComponent(location.href);
                const text = encodeURIComponent("文化祭まとめサイトはこちら！");
                const shareUrl = `https://social-plugins.line.me/lineit/share?url=${url}&text=${text}`;
                window.open(shareUrl, "_blank", "noopener,noreferrer");
            });

            // ページロード時に以前の訪問履歴で判断
            if (localStorage.getItem(LOCAL_STORAGE_KEY) === "true") {
                showMain();
            } else {
                showInitial();
            }

            // 読み込み完了でフェードイン
            setTimeout(() => {
                document.body.classList.add("loaded");
            }, 100);
        });
    </script>
</body>
</html>
