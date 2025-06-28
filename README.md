<!DOCTYPE html>
<html lang="ja">
<head>
  <meta charset="UTF-8" />
  <title>文化祭まとめサイト</title>
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <link rel="icon" href="favicon.ico" type="image/x-icon">
  <style>
    :root {
        --primary-color: #007bff;
        --primary-hover-color: #0056b3;
        --success-color: #4CAF50;
        --success-hover-color: #45a049;
        --background-light: #f4f4f4;
        --text-dark: #333;
        --shadow-light: rgba(0, 0, 0, 0.1);
        --shadow-medium: rgba(0, 0, 0, 0.15);
    }

    body {
      margin: 0;
      font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
      background: var(--background-light);
      color: var(--text-dark);
      text-align: center;
      min-height: 100vh;
      display: flex;
      flex-direction: column;
      justify-content: center;
      align-items: center;
      user-select: none;
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

    #fake-site {
      display: none; /* 初期はJSで制御 */
      flex-direction: column;
      align-items: center;
      background-color: #fff;
      padding: 3rem;
      border-radius: 12px;
      box-shadow: 0 4px 12px var(--shadow-light);
    }

    #fake-site h1 {
      font-size: 3.5rem;
      margin-bottom: 2.5rem;
      text-shadow: 1px 1px 0 #eee, 2px 2px 4px var(--shadow-light);
      line-height: 1.3;
      color: #555;
      font-weight: 700;
    }

    #fake-site h1 span {
      display: block;
    }

    #fake-site h1 span:first-child {
      font-size: 2.2rem;
      color: #777;
      font-weight: 500;
    }

    #fake-site button {
      background-color: var(--primary-color);
      border: none;
      padding: 1.5rem 3.5rem;
      font-size: 1.8rem;
      font-weight: 600;
      border-radius: 8px;
      color: #fff;
      cursor: pointer;
      box-shadow: 0 4px 8px var(--shadow-medium);
      transition: background-color 0.2s ease-in-out, transform 0.1s ease-in-out;
      margin-top: 1.5rem;
    }

    #fake-site button:hover {
      background-color: var(--primary-hover-color);
      transform: translateY(-2px);
    }

    #virus-screen {
      position: fixed;
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
      pointer-events: none; /* クリックや選択を無効化 */
      animation: noise 0.1s infinite, flicker 0.2s infinite alternate;
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
      font-size: 2.5rem;
      margin-bottom: 1.5rem;
      animation: blink-fast 0.3s infinite alternate, glitch 0.5s infinite alternate;
      width: 90%;
      text-align: center;
      box-sizing: border-box;
      text-shadow: 0 0 10px #f00, 0 0 20px #f00;
    }

    #countdown {
      font-size: 6rem;
      font-weight: bold;
      color: #00ff00;
      text-shadow: 0 0 5px #00ff00, 0 0 15px #00ff00;
      margin-bottom: 1rem;
      width: 90%;
      text-align: center;
      box-sizing: border-box;
      animation: glitch 0.3s infinite alternate, scanlines 0.1s infinite;
    }

    #reveal-message {
      font-size: 1.3rem;
      color: #f0f0f0;
      background-color: rgba(0, 0, 0, 0.8);
      padding: 1rem;
      border-radius: 5px;
      display: none;
      margin-top: 1.5rem;
      animation: fade-in 0.5s forwards;
      max-width: 80%;
      box-sizing: border-box;
      text-align: center;
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

    #relief-screen {
      background: linear-gradient(45deg, #e0f7fa, #b2ebf2);
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
      font-size: 1.8rem;
      display: none;
      user-select: text;
      animation: fade-in 0.5s ease-in-out;
      font-weight: 500;
    }

    #relief-screen p {
        margin: 0.5rem 0;
        text-shadow: 1px 1px 2px rgba(255,255,255,0.5);
    }

    #main {
      padding: 3rem;
      background: #fff;
      min-height: auto;
      color: var(--text-dark);
      user-select: none;
      display: none; /* 初期はJSで制御 */
      border-radius: 12px;
      box-shadow: 0 4px 12px var(--shadow-light);
      max-width: 90%;
      margin-top: 2rem;
      box-sizing: border-box; /* paddingを含めて幅を計算 */
    }

    #main.visible {
      display: block;
      animation: fade-in 0.3s ease-in-out;
    }

    #main h1 {
      font-size: 3rem;
      margin-bottom: 2rem;
      text-shadow: 1px 1px 0 #eee, 2px 2px 4px var(--shadow-light);
      color: #555;
      font-weight: 700;
    }

    /* メインナビゲーションボタン */
    .main-nav-buttons {
      display: flex;
      justify-content: center;
      gap: 1rem;
      margin-bottom: 2rem;
      flex-wrap: wrap; /* スマホ対応 */
    }

    .main-nav-buttons button {
      padding: 1.2rem 2.5rem;
      font-size: 1.4rem;
      cursor: pointer;
      background-color: var(--primary-color);
      border: none;
      border-radius: 8px;
      color: #fff;
      font-weight: 600;
      box-shadow: 0 2px 6px var(--shadow-light);
      transition: background-color 0.2s ease-in-out, transform 0.1s ease-in-out;
    }

    .main-nav-buttons button:hover {
      background-color: var(--primary-hover-color);
      transform: translateY(-1px);
    }

    /* 各コンテンツエリアのスタイル */
    #schedule-content {
      background: #fff;
      padding: 2rem;
      border-radius: 8px;
      box-shadow: 0 2px 8px rgba(0, 0, 0, 0.05);
      margin-top: 1rem;
      min-height: 300px;
      display: none; /* 初期はJSで制御 */
      width: 100%;
      max-width: 800px;
      box-sizing: border-box;
    }

    #schedule-content.active {
      display: block;
    }

    #schedule-content .schedule-nav-buttons button { /* スケジュールの日付選択ボタン */
        background-color: #6c757d; /* グレー系 */
        color: white;
        border: none;
        padding: 0.8rem 1.5rem;
        margin: 0.5rem;
        border-radius: 5px;
        cursor: pointer;
        font-size: 1.1rem;
        transition: background-color 0.2s ease;
    }
    #schedule-content .schedule-nav-buttons button:hover {
        background-color: #5a6268;
    }

    #schedule-image-container img {
      max-width: 95%;
      max-height: 70vh;
      border: 2px solid #ddd;
      border-radius: 8px;
      box-shadow: 0 2px 8px var(--shadow-light);
      margin-top: 1.5rem;
    }

    #share-buttons {
      margin-top: 2.5rem;
      display: none;
      justify-content: center;
      gap: 1.5rem;
    }

    #share-buttons.visible {
      display: flex;
    }

    .share-btn {
      cursor: pointer;
      padding: 1rem 2rem;
      border-radius: 10px;
      font-weight: 600;
      font-size: 1.2rem;
      color: white;
      box-shadow: 0 2px 6px var(--shadow-light);
      display: flex;
      align-items: center;
      gap: 0.8rem;
      user-select: none;
      transition: transform 0.1s ease-in-out;
    }

    .share-btn:hover {
      transform: translateY(-1px);
    }

    .share-x {
      background-color: #1DA1F2;
    }

    .share-x:hover {
      background-color: #0d8ddb;
    }

    .share-line {
      background-color: #00c300;
    }

    .share-line:hover {
      background-color: #00a000;
    }

    .share-icon {
      font-size: 1.4rem;
    }

    /* レスポンシブデザイン */
    @media (max-width: 768px) {
        #fake-site {
            padding: 1.5rem;
        }
        #fake-site h1 {
            font-size: 2.5rem;
            margin-bottom: 1.5rem;
        }
        #fake-site h1 span:first-child {
            font-size: 1.8rem;
        }
        #fake-site button {
            padding: 1rem 2rem;
            font-size: 1.4rem;
        }
        #main {
            padding: 1.5rem;
            max-width: 95%;
        }
        #main h1 {
            font-size: 2.2rem;
            margin-bottom: 1.5rem;
        }
        .main-nav-buttons button {
            padding: 1rem 1.8rem;
            font-size: 1.2rem;
        }
        #schedule-content {
            padding: 1.5rem;
        }
        .share-btn {
            padding: 0.8rem 1.5rem;
            font-size: 1.1rem;
            gap: 0.5rem;
        }
        #virus-screen h2 {
            font-size: 1.8rem;
        }
        #countdown {
            font-size: 4rem;
        }
        #reveal-message {
            font-size: 1.1rem;
            padding: 0.8rem;
        }
    }
  </style>
</head>
<body>
  <div id="fake-site" role="main" aria-label="ようこそ文化祭まとめサイトへ">
    <h1>
      <span style="display:block; font-size:2rem;">ようこそ</span>
      <span style="display:block; font-size:3rem;">文化祭まとめサイトへ</span>
    </h1>
    <button id="start-btn" type="button">スケジュールを見る</button>
  </div>

  <div id="virus-screen" role="alert" aria-live="assertive" aria-atomic="true" class="hidden">
  </div>

  <div id="relief-screen" class="hidden" role="region" aria-live="polite" aria-atomic="true">
    <p>...異常は検出されませんでした。</p>
    <p>安心してご覧ください。</p>
  </div>

  <div id="main" role="main" aria-label="文化祭まとめコンテンツ">
    <button id="repeat-virus-btn" type="button">もう一度ウイルス演出を見る</button>
    
    <div class="main-nav-buttons">
      <button id="show-schedule-btn" type="button">文化祭スケジュール</button>
      <button id="show-quiz-minigame-btn" type="button">クイズ＆ミニゲーム</button>
    </div>

    <div id="schedule-content" class="active">
      <h1>文化祭スケジュール</h1>
      <div class="schedule-nav-buttons">
        <button id="schedule-junbi" type="button">準備日</button>
        <button id="schedule-day1" type="button">一日目</button>
        <button id="schedule-day2" type="button">二日目</button>
        <button id="schedule-kataduke" type="button">片付け日</button>
      </div>
      <div id="schedule-image-container" aria-live="polite" aria-atomic="true"></div>
    </div>

    <div id="share-buttons" aria-label="SNS共有ボタン" aria-hidden="true">
      <div class="share-btn share-x" role="button" tabindex="0" data-sns="x" title="Xで共有" aria-label="Xで共有">
        <span class="share-icon">🐦</span> Xで共有
      </div>
      <div class="share-btn share-line" role="button" tabindex="0" data-sns="line" title="LINEで共有" aria-label="LINEで共有">
        <span class="share-icon">💬</span> LINEで共有
      </div>
    </div>
  </div>

  <script>
    document.addEventListener("DOMContentLoaded", () => {
      const localStorageKey = "bunkasai_visited";
      const VIRUS_COUNTDOWN_SECONDS = 5;

      const body = document.body;
      const fakeSite = document.getElementById("fake-site");
      const virusScreen = document.getElementById("virus-screen");
      const reliefScreen = document.getElementById("relief-screen");
      const mainScreen = document.getElementById("main");
      const repeatVirusBtn = document.getElementById("repeat-virus-btn");
      const scheduleImageContainer = document.getElementById("schedule-image-container");
      const shareButtons = document.getElementById("share-buttons");
      const startBtn = document.getElementById("start-btn");

      const showScheduleBtn = document.getElementById("show-schedule-btn");
      const showQuizMinigameBtn = document.getElementById("show-quiz-minigame-btn");

      const scheduleContent = document.getElementById("schedule-content");

      let synth = window.speechSynthesis;
      let utterance = null;
      let alarmAudio = null;
      let voiceLoopRunning = false;
      let countdownIntervalId = null;
      let isLineBrowserDetected = false;

      // ジャンプ先のクイズサイトURL
      const QUIZ_SITE_URL = "https://miyashitt.github.io/Shit/";

      // --- Helper Functions ---
      function isLineBrowser() {
        return navigator.userAgent.includes("Line");
      }

      function requestFullscreen() {
        if (document.documentElement.requestFullscreen) {
          document.documentElement.requestFullscreen().catch(e => {
            console.warn("Fullscreen request failed:", e);
          });
        }
      }

      function startJapaneseVoiceLoop() {
        if (isLineBrowserDetected) return;
        if (voiceLoopRunning) return;
        voiceLoopRunning = true;

        utterance = new SpeechSynthesisUtterance("あなたのスマホはウイルスに感染しました。全てのデータを削除します。キャンセルはできません。");
        utterance.lang = "ja-JP";
        utterance.rate = 0.6;
        utterance.pitch = 0.4;
        utterance.volume = 1.0;

        utterance.onend = () => {
          if (voiceLoopRunning) {
            try {
                synth.speak(utterance);
            } catch (e) {
                console.error("Failed to re-speak utterance:", e);
                voiceLoopRunning = false;
            }
          }
        };

        try {
          synth.cancel();
          synth.speak(utterance);
        } catch (e) {
          console.error("Failed to speak utterance:", e);
          voiceLoopRunning = false;
        }
      }

      function stopJapaneseVoiceLoop() {
        if (isLineBrowserDetected) return;
        voiceLoopRunning = false;
        try {
          synth.cancel();
        } catch (e) {
            console.warn("Failed to cancel speech synthesis:", e);
        }
      }

      async function playAlarmSound() {
        if (isLineBrowserDetected) return null;
        try {
          if (!alarmAudio) {
              alarmAudio = new Audio("https://upload.wikimedia.org/wikipedia/commons/b/b2/Sos-morse-code.ogg");
              alarmAudio.loop = true;
              alarmAudio.volume = 1.0;
              alarmAudio.preload = "auto";
              await alarmAudio.load();
          }
          await alarmAudio.play();
          return alarmAudio;
        } catch (e) {
          console.error("Failed to play alarm sound:", e);
          return null;
        }
      }

      function stopAlarmSound() {
        if (alarmAudio) {
          alarmAudio.pause();
          alarmAudio.currentTime = 0;
          alarmAudio = null;
        }
      }

      function startVirusSimulation() {
        isLineBrowserDetected = isLineBrowser();

        body.classList.add('virus-active');
        body.classList.remove('loaded');
        requestFullscreen();

        fakeSite.classList.add("hidden");
        reliefScreen.classList.add("hidden");
        mainScreen.classList.remove("visible");
        mainScreen.classList.add("hidden");
        
        virusScreen.classList.remove("hidden");
        virusScreen.classList.add("visible");

        virusScreen.innerHTML = '';
        const virusTitle = document.createElement("h2");
        virusTitle.textContent = "⚠ ウイルスに感染しました";
        virusScreen.appendChild(virusTitle);

        const countdownElement = document.createElement("div");
        countdownElement.id = "countdown";
        virusScreen.appendChild(countdownElement);

        const revealMessageElement = document.createElement("div");
        revealMessageElement.id = "reveal-message";
        revealMessageElement.classList.add("hidden");
        revealMessageElement.innerHTML = "ご安心ください！これは文化祭の面白い仕掛けです。<br>あなたのデータは無事です！";
        virusScreen.appendChild(revealMessageElement);
        
        if (!isLineBrowserDetected) {
            startJapaneseVoiceLoop();
            playAlarmSound();
        }

        let count = VIRUS_COUNTDOWN_SECONDS;
        countdownElement.textContent = count;

        if (countdownIntervalId !== null) {
          clearInterval(countdownIntervalId);
        }

        countdownIntervalId = setInterval(() => {
          count--;
          if (count >= 0) {
            countdownElement.textContent = count;
          }

          if (count === 0) {
            clearInterval(countdownIntervalId);
            countdownIntervalId = null;

            if (!isLineBrowserDetected) {
                stopJapaneseVoiceLoop();
                stopAlarmSound();
            }

            countdownElement.classList.add("hidden");
            revealMessageElement.classList.remove("hidden");
            
            localStorage.setItem(localStorageKey, "1");

            setTimeout(() => {
              showReliefScreen();
            }, 2000);
          }
        }, 1000);
      }

      function showReliefScreen() {
        virusScreen.classList.remove("visible");
        virusScreen.classList.add("hidden");
        virusScreen.innerHTML = '';

        reliefScreen.classList.remove("hidden");
        reliefScreen.style.display = "flex";

        setTimeout(() => {
          reliefScreen.style.display = "none";
          reliefScreen.classList.add("hidden");
          showMainScreen();
          showShareButtons();
          body.classList.remove('virus-active');
          body.classList.add('loaded');
        }, 3000);
      }

      function showMainScreen() {
        mainScreen.classList.remove("hidden");
        mainScreen.classList.add("visible");
        showContent('schedule'); // 初期表示はスケジュールコンテンツ
      }

      function showShareButtons() {
        shareButtons.classList.add("visible");
        shareButtons.setAttribute("aria-hidden", "false");
      }

      function showScheduleImage(day) {
        if (!day) {
          scheduleImageContainer.innerHTML = "<p>上記ボタンからスケジュールを選択してください。</p>";
          return;
        }
        const imgPath = `${day}.png`;
        const img = new Image();
        img.onload = () => {
          scheduleImageContainer.innerHTML = `<img src="${imgPath}" alt="${day}のスケジュール" />`;
        };
        img.onerror = () => {
          scheduleImageContainer.innerHTML = `<p>スケジュール画像 (${imgPath}) が見つかりませんでした。</p>`;
          console.error(`Error loading image: ${imgPath}`);
        };
        img.src = imgPath;
      }

      function createExternalBrowserURL(originalUrl) {
        return `googlechrome://navigate?url=${encodeURIComponent(originalUrl)}`;
      }

      function shareOnSNS(platform) {
        const currentURL = window.location.href;
        const shareText = "文化祭まとめサイトをチェック！";
        let url = "";

        if (platform === 'x') {
          url = `https://twitter.com/intent/tweet?text=${encodeURIComponent(shareText)}%0A${encodeURIComponent(currentURL)}`;
        } else if (platform === 'line') {
          const externalURL = createExternalBrowserURL(currentURL);
          url = `https://line.me/R/msg/text/?${encodeURIComponent(shareText)}%0A${encodeURIComponent(externalURL)}`;
        }
        window.open(url, "_blank", "noopener");
      }

      function showContent(contentId) {
        const contents = [scheduleContent];
        contents.forEach(content => {
          content.classList.remove('active');
          content.classList.add('hidden');
        });

        if (contentId === 'schedule') {
          scheduleContent.classList.add('active');
          scheduleContent.classList.remove('hidden');
          showScheduleImage('day1');
        }
      }

      // --- Event Listeners ---
      startBtn.addEventListener("click", startVirusSimulation);
      repeatVirusBtn.addEventListener("click", startVirusSimulation);

      document.getElementById('schedule-junbi').addEventListener('click', () => showScheduleImage('junbi'));
      document.getElementById('schedule-day1').addEventListener('click', () => showScheduleImage('day1'));
      document.getElementById('schedule-day2').addEventListener('click', () => showScheduleImage('day2'));
      document.getElementById('schedule-kataduke').addEventListener('click', () => showScheduleImage('kataduke'));

      showScheduleBtn.addEventListener("click", () => showContent('schedule'));
      
      // クイズ＆ミニゲームボタンの挙動をサイトジャンプに変更
      showQuizMinigameBtn.addEventListener("click", () => {
        window.open(QUIZ_SITE_URL, "_blank", "noopener");
      });

      document.querySelectorAll('.share-btn').forEach(button => {
        button.addEventListener('click', (event) => {
          const sns = event.currentTarget.dataset.sns;
          shareOnSNS(sns);
        });
      });

      // --- Initial Load Logic ---
      window.addEventListener("load", () => {
        isLineBrowserDetected = isLineBrowser();

        const visited = localStorage.getItem(localStorageKey);
        if (!visited) {
          fakeSite.style.display = "flex";
        } else {
          showMainScreen();
          showShareButtons();
        }
        body.classList.add('loaded');
      });
    });
  </script>
</body>
</html>
