<!DOCTYPE html>
<html lang="ja">
<head>
    <meta charset="UTF-8" />
    <title>文化祭まとめサイト</title>
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <link rel="icon" href="favicon.ico" type="image/x-icon">
    <link href="https://fonts.googleapis.com/css2?family=Noto+Sans+JP:wght@300;400;500;700&display=swap" rel="stylesheet">
    <style>
        /* CSS Variables for a cohesive design */
        :root {
            --primary-color: #4CAF50; /* Greenish */
            --primary-hover-color: #45a049;
            --secondary-color: #007bff; /* Blueish */
            --secondary-hover-color: #0056b3;
            --text-dark: #333;
            --text-light: #f8f8f8;
            --background-light: #e0f2f7; /* Light cyan background */
            --background-gradient-start: #e0f7fa;
            --background-gradient-end: #c8e6c9;
            --card-background: #ffffff;
            --shadow-light: rgba(0, 0, 0, 0.1);
            --shadow-medium: rgba(0, 0, 0, 0.2);
            --border-color: #e0e0e0;
            --accent-color: #ff9800; /* Orange for highlights */
        }

        body {
            margin: 0;
            font-family: 'Noto Sans JP', 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
            background: linear-gradient(135deg, var(--background-gradient-start) 0%, var(--background-gradient-end) 100%);
            color: var(--text-dark);
            text-align: center;
            min-height: 100vh;
            display: flex;
            flex-direction: column;
            justify-content: center;
            align-items: center;
            user-select: none;
            overflow-x: hidden; /* Prevent horizontal scroll */
            overflow-y: auto;
            transition: background-color 0.3s ease-in-out, opacity 0.5s ease-in-out;
            opacity: 0; /* JavaScriptでloadedクラスが付与されたら表示 */
        }

        body.loaded {
            opacity: 1;
        }

        body.virus-active {
            background-color: #000 !important;
            margin: 0 !important;
            padding: 0 !important;
            overflow: hidden !important;
            opacity: 1 !important; /* ウイルス画面中は強制的に表示 */
        }

        .hidden {
            display: none !important;
        }

        /* Initial Screen styles */
        #initial-screen {
            background: linear-gradient(135deg, var(--background-gradient-start) 0%, var(--background-gradient-end) 100%);
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


        /* Virus Screen styles */
        #virus-screen {
            position: fixed; /* 全画面表示 */
            top: 0;
            left: 0;
            right: 0;
            bottom: 0;
            width: 100vw;
            height: 100vh;
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
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background-color: rgba(255, 0, 0, 0.2);
            background-image: url('data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAAQAAAAECAYAAACp8da+AAAAAXNSR0IArs4c6QAAABJJREFUGFdjYGBgYGBgYGAAAgABAAyW/71mAAAAAElFTkSuQmCC');
            background-size: 2px 2px;
            opacity: 0.7;
            pointer-events: none;
            z-index: -1;
        }

        #virus-screen.visible {
            display: flex;
        }

        #virus-screen h2 {
            font-size: 2.8rem;
            margin-bottom: 2rem;
            animation: blink-fast 0.3s infinite alternate, glitch 0.5s infinite alternate;
            width: 90%;
            text-align: center;
            box-sizing: border-box;
            text-shadow: 0 0 15px #f00, 0 0 30px #f00; /* Stronger shadow */
        }

        #countdown {
            font-size: 7rem; /* Larger countdown */
            font-weight: bold;
            color: #00ff00;
            text-shadow: 0 0 8px #00ff00, 0 0 25px #00ff00; /* Stronger shadow */
            margin-bottom: 1.5rem;
            width: 90%;
            text-align: center;
            box-sizing: border-box;
            animation: glitch 0.3s infinite alternate, scanlines 0.1s infinite;
        }

        #reveal-message {
            font-size: 1.5rem; /* Larger reveal message */
            color: #f0f0f0;
            background-color: rgba(0, 0, 0, 0.85); /* Slightly darker background */
            padding: 1.5rem;
            border-radius: 8px; /* More rounded */
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
            0% { opacity: 1; }
            49% { opacity: 1; }
            50% { opacity: 0.5; }
            100% { opacity: 1; }
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

        /* Relief Screen Styles */
        #relief-screen {
            background: linear-gradient(45deg, #e0f7fa, #b2ebf2); /* Brighter gradient */
            color: #006064;
            height: 100vh;
            width: 100vw;
            position: fixed;
            top: 0;
            left: 0;
            z-index: 9998;
            justify-content: center;
            align-items: center;
            flex-direction: column;
            font-size: 2rem; /* Larger font */
            font-weight: 500;
            display: none;
            user-select: text;
            animation: fade-in 0.8s ease-in-out;
            text-shadow: 1px 1px 3px rgba(0,0,0,0.1); /* Subtle text shadow */
        }

        #relief-screen p {
            margin: 0.8rem 0; /* More spacing */
        }

        /* Main Content Styles - ボタンのみにするため調整 */
        #main {
            padding: 2.5rem; /* 適度なパディング */
            background: transparent; /* 背景を透明に */
            min-height: 100vh; /* 全画面表示 */
            width: 100vw; /* 全画面表示 */
            position: fixed; /* 全画面表示 */
            top: 0;
            left: 0;
            color: var(--text-dark);
            user-select: none;
            display: none; /* 初期は非表示 */
            border-radius: 16px;
            box-shadow: none; /* 影も削除 */
            margin-top: 0; /* マージンを削除 */
            box-sizing: border-box;
            animation: fadeInScale 0.8s ease-out forwards;
            justify-content: center; /* ボタン群を中央に */
            align-items: center; /* ボタン群を中央に */
            flex-direction: column; /* 縦並びにするため */
        }

        #main.visible {
            display: flex;
        }

        /* ナビゲーションボタンの共通スタイル */
        .nav-buttons {
            display: flex;
            flex-direction: column; /* ボタンを縦並びにする */
            justify-content: center;
            align-items: center; /* ボタン群を中央寄せ */
            gap: 1.5rem; /* ボタン間の間隔 */
            width: 100%; /* 親要素の幅いっぱいに */
            max-width: 400px; /* ボタン群の最大幅 */
        }

        .nav-buttons button {
            width: 90%; /* スマホで押しやすいように幅を広く */
            max-width: 300px; /* ボタンの最大幅を制限 */
            background-color: var(--secondary-color); /* ボタンの色 */
            border: none;
            padding: 1.4rem 2rem; /* パディング */
            font-size: 1.6rem; /* フォントサイズ */
            font-weight: 600;
            border-radius: 10px; /* 角丸 */
            color: var(--text-light); /* 文字色 */
            cursor: pointer;
            box-shadow: 0 6px 12px var(--shadow-medium); /* 影 */
            transition: background-color 0.3s ease, transform 0.2s ease, box-shadow 0.3s ease;
            letter-spacing: 0.5px;
            display: flex; /* アイコンとテキストを中央揃えにするため */
            align-items: center;
            justify-content: center;
            gap: 0.8rem; /* アイコンとテキストの間のスペース */
            white-space: nowrap; /* ボタン内のテキストの折り返しを防ぐ */
            box-sizing: border-box; /* パディングを幅に含める */
        }

        .nav-buttons button:hover {
            background-color: var(--secondary-hover-color);
            transform: translateY(-3px) scale(1.02); /* ホバー時の動き */
            box-shadow: 0 8px 16px var(--shadow-medium);
        }

        .share-icon {
            font-size: 1.6rem; /* アイコンサイズ */
        }

        /* レスポンシブデザイン */
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
                gap: 1rem; /* スマホでより詰める */
            }
            .nav-buttons button {
                width: 95%; /* より広く */
                padding: 1rem 1.5rem;
                font-size: 1.3rem;
                max-width: none; /* スマホでは最大幅の制限をなくす */
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
                font-size: 1rem;
                margin-bottom: 1.5rem;
            }
        }
    </style>
</head>
<body>
    <div id="virus-screen" role="alert" aria-live="assertive" aria-atomic="true" class="hidden">
    </div>

    <div id="relief-screen" class="hidden" role="region" aria-live="polite" aria-atomic="true">
        <p>...異常は検出されませんでした。</p>
        <p>安心してご覧ください。</p>
    </div>

    <div id="initial-screen" role="region" aria-label="文化祭特設サイトへようこそ">
        <h1>ようこそ！文化祭特設サイトへ</h1>
        <p>文化祭の情報を公開しています。<br>「文化祭情報を見る」ボタンを押して、文化祭の世界へどうぞ！</p>
        <div class="nav-buttons">
            <button id="show-initial-bunkasai-info-btn" type="button">
                <span class="share-icon">💡</span> 文化祭情報を見る
            </button>
        </div>
    </div>

    <div id="main" role="main" aria-label="文化祭まとめコンテンツ" class="hidden">
        <div class="nav-buttons">
            <button id="repeat-virus-btn" type="button">
                <span class="share-icon">🚨</span> もう一度ウイルス演出を見る
            </button>
            <button id="show-bunkasai-info-btn" type="button">
                <span class="share-icon">💡</span> 文化祭情報
            </button>
            <button id="show-quiz-minigame-btn" type="button">
                <span class="share-icon">🎮</span> クイズ
            </button>
            <button class="share-btn share-x" role="button" tabindex="0" data-sns="x" title="Xで共有" aria-label="Xで共有">
                <span class="share-icon">🐦</span> Xで共有
            </button>
            <button class="share-btn share-line" role="button" tabindex="0" data-sns="line" title="LINEで共有" aria-label="LINEで共有">
                <span class="share-icon">💬</span> LINEで共有
            </button>
        </div>
    </div>

    <script>
        document.addEventListener("DOMContentLoaded", () => {
            const localStorageKey = "bunkasai_visited";
            const VIRUS_COUNTDOWN_SECONDS = 5;

            const body = document.body;
            const initialScreen = document.getElementById("initial-screen"); // Added initial screen
            const virusScreen = document.getElementById("virus-screen");
            const reliefScreen = document.getElementById("relief-screen");
            const mainScreen = document.getElementById("main");

            const showInitialBunkasaiInfoBtn = document.getElementById("show-initial-bunkasai-info-btn"); // Button on initial screen
            const repeatVirusBtn = document.getElementById("repeat-virus-btn");
            const showBunkasaiInfoBtn = document.getElementById("show-bunkasai-info-btn");
            const showQuizMinigameBtn = document.getElementById("show-quiz-minigame-btn");

            let synth = window.speechSynthesis;
            let utterance = null;
            let alarmAudio = null;
            let voiceLoopRunning = false;
            let countdownIntervalId = null;
            let isLineBrowserDetected = false;

            // ジャンプ先のクイズサイトURL
            const QUIZ_SITE_URL = "https://miyashitt.github.io/Shit/";
            // 文化祭公式InstagramアカウントのURL
            const BUNKASAI_INSTAGRAM_URL = "https://www.instagram.com/kenryo_fes_78th?utm_source=ig_web_button_share_sheet&igsh=MWkyZDRrbjRuYnl6ag==";

            // LINEブラウザ判定
            isLineBrowserDetected = navigator.userAgent.includes("Line");

            // ウイルス演出の関数
            function startVirusSimulation() {
                body.classList.add("virus-active");
                initialScreen.classList.add("hidden"); // Hide initial screen
                mainScreen.classList.add("hidden"); // Hide main screen
                virusScreen.classList.remove("hidden");
                virusScreen.innerHTML = `
                    <h2><span style="color:red;">WARNING!!!</span> SYSTEM INTEGRITY COMPROMISED.</h2>
                    <div id="countdown" style="color: #00FF00;">${VIRUS_COUNTDOWN_SECONDS}</div>
                    <div id="reveal-message" class="hidden">
                        <h3>これは文化祭の告知です！</h3>
                        <p><strong>文化祭の日程や見どころはInstagram公式アカウントをチェック！</strong></p>
                        <p>最新情報をGETして文化祭を楽しもう！</p>
                        <p>※偽のウイルス演出はこれで終了です。</p>
                        <a href="${BUNKASAI_INSTAGRAM_URL}" target="_blank" style="color: #66ccff; text-decoration: underline; font-weight: bold;">Instagram公式アカウントへ</a>
                    </div>
                `;

                if (!isLineBrowserDetected) { // LINEブラウザでない場合のみ音声再生
                    speak(`警告。システムに異常を検知しました。`);
                }

                let countdown = VIRUS_COUNTDOWN_SECONDS;
                const countdownElement = document.getElementById("countdown");
                const revealMessage = document.getElementById("reveal-message");

                countdownIntervalId = setInterval(() => {
                    countdown--;
                    if (countdownElement) {
                        countdownElement.textContent = countdown;
                    }

                    if (countdown <= 0) {
                        clearInterval(countdownIntervalId);
                        if (countdownElement) {
                            countdownElement.classList.add("hidden");
                        }
                        if (revealMessage) {
                            revealMessage.classList.remove("hidden");
                        }
                        if (!isLineBrowserDetected) { // LINEブラウザでない場合のみ音声停止
                            stopVoiceLoop();
                            speak("警告は解除されました。文化祭をお楽しみください。"); // 演出終了のメッセージ
                        }
                        virusScreen.style.pointerEvents = 'auto'; // クリック可能に

                        // 一定時間後に自動で relief-screen を表示し、その後 mainScreen を表示する
                        setTimeout(() => {
                            virusScreen.classList.add("hidden");
                            reliefScreen.classList.remove("hidden");
                            reliefScreen.style.display = 'flex'; // Flexboxで表示

                            setTimeout(() => {
                                reliefScreen.classList.add("hidden");
                                reliefScreen.style.display = 'none';
                                mainScreen.classList.remove("hidden");
                                mainScreen.classList.add("visible");
                                body.classList.remove("virus-active");
                                localStorage.setItem(localStorageKey, "true"); // 演出完了フラグをセット
                            }, 2000); // 2秒後にリリーフ画面を非表示にしてメイン画面へ
                        }, 3000); // 3秒後に演出終了メッセージ表示
                    }
                }, 1000);

                if (!isLineBrowserDetected) { // LINEブラウザでない場合のみ音声ループとアラーム音
                    startVoiceLoop();
                    playAlarmSound();
                }
            }

            // 音声読み上げの関数
            function speak(text) {
                if (synth.speaking) {
                    synth.cancel();
                }
                utterance = new SpeechSynthesisUtterance(text);
                utterance.lang = 'ja-JP';
                utterance.rate = 1.0;
                utterance.pitch = 1.0;
                utterance.volume = 0.7;
                synth.speak(utterance);
            }

            // 音声ループの開始と停止
            function startVoiceLoop() {
                voiceLoopRunning = true;
                loopVoice();
            }

            function stopVoiceLoop() {
                voiceLoopRunning = false;
                if (synth.speaking) {
                    synth.cancel();
                }
                if (alarmAudio) {
                    alarmAudio.pause();
                    alarmAudio.currentTime = 0;
                }
            }

            function loopVoice() {
                if (!voiceLoopRunning) return;
                speak("異常を検知。システムチェック。");
                utterance.onend = () => {
                    if (voiceLoopRunning) {
                        setTimeout(loopVoice, 3000); // 3秒後に再度ループ
                    }
                };
            }

            // 警告音の再生
            function playAlarmSound() {
                if (alarmAudio) {
                    alarmAudio.pause();
                    alarmAudio.currentTime = 0;
                }
                alarmAudio = new Audio('alarm.mp3'); // 警告音のパス
                alarmAudio.loop = true;
                alarmAudio.volume = 0.5;
                alarmAudio.play().catch(e => console.error("音声再生エラー:", e));
            }

            // ページ読み込み時に実行される処理
            body.classList.add("loaded"); // ロード完了時にフェードイン

            // 初回訪問時またはリセット時のみ初期画面を表示
            if (localStorage.getItem(localStorageKey) === "true") {
                initialScreen.classList.add("hidden");
                mainScreen.classList.remove("hidden");
                mainScreen.classList.add("visible");
            } else {
                initialScreen.classList.remove("hidden");
            }

            // Initial Screenの「文化祭情報を見る」ボタン
            showInitialBunkasaiInfoBtn.addEventListener("click", () => {
                // 音声再生が目的のため、まずInstagramへ誘導
                window.open(BUNKASAI_INSTAGRAM_URL, "_blank");
                // その後、ウイルス演出を開始
                startVirusSimulation();
            });

            // 各ボタンのイベントリスナー
            repeatVirusBtn.addEventListener("click", () => {
                // 全画面を非表示にし、ボディのクラスをリセット
                mainScreen.classList.add("hidden");
                reliefScreen.classList.add("hidden");
                virusScreen.classList.add("hidden");
                body.classList.remove("virus-active");

                // localStorage のフラグを削除して、再度演出が実行されるようにする
                localStorage.removeItem(localStorageKey);

                // ウイルス演出を再度開始
                startVirusSimulation();
            });

            showBunkasaiInfoBtn.addEventListener("click", () => {
                // 文化祭情報はInstagram公式アカウントへ誘導
                window.open(BUNKASAI_INSTAGRAM_URL, "_blank");
            });

            showQuizMinigameBtn.addEventListener("click", () => {
                window.open(QUIZ_SITE_URL, "_blank");
            });

            // 共有ボタンのイベントリスナー
            document.querySelectorAll('.share-btn').forEach(button => {
                button.addEventListener('click', () => {
                    const sns = button.dataset.sns;
                    const url = encodeURIComponent(window.location.href);
                    const text = encodeURIComponent("私たちの文化祭の特設サイトを見てね！\n#文化祭 #高校生活"); // 共有するテキスト
                    let shareUrl = '';

                    switch (sns) {
                        case 'x':
                            shareUrl = `https://twitter.com/intent/tweet?url=${url}&text=${text}`;
                            break;
                        case 'line':
                            // LINEの共有URLは特殊。LINEアプリがインストールされている環境でのみ動作する。
                            // PCやLINEアプリがない場合は、ただのURL共有になるか、エラーになる。
                            shareUrl = `https://social-plugins.line.me/lineit/share?url=${url}&text=${text}`;
                            if (isLineBrowserDetected) {
                                // LINEブラウザの場合、直接外部ブラウザで開くよう促すメッセージ
                                alert("LINEアプリ以外で開くと共有がスムーズです。ブラウザを切り替えてお試しください。");
                            }
                            break;
                    }

                    if (shareUrl) {
                        window.open(shareUrl, '_blank');
                    }
                });
            });
        });
    </script>
</body>
</html>
