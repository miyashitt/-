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

    #fake-site {
      display: none; /* 初期はJSで制御 */
      flex-direction: column;
      align-items: center;
      background-color: var(--card-background);
      padding: 4rem; /* Increased padding */
      border-radius: 16px; /* Slightly larger border-radius */
      box-shadow: 0 10px 30px var(--shadow-medium); /* Deeper shadow */
      max-width: 90%;
      box-sizing: border-box;
      animation: fadeInScale 0.8s ease-out forwards;
    }

    @keyframes fadeInScale {
      from { opacity: 0; transform: scale(0.9); }
      to { opacity: 1; transform: scale(1); }
    }

    #fake-site h1 {
      font-size: 3.8rem; /* Larger font size */
      margin-bottom: 2.8rem;
      color: #444;
      font-weight: 700;
      letter-spacing: -1px;
      text-shadow: 2px 2px 5px var(--shadow-light);
    }

    #fake-site h1 span:first-child {
      font-size: 2.5rem; /* Larger sub-heading */
      color: #666;
      font-weight: 500;
      margin-bottom: 0.5rem;
    }

    #fake-site button {
      background-color: var(--primary-color);
      border: none;
      padding: 1.8rem 4rem; /* Larger padding for button */
      font-size: 2rem; /* Larger font size for button */
      font-weight: 600;
      border-radius: 10px; /* Slightly more rounded */
      color: var(--text-light);
      cursor: pointer;
      box-shadow: 0 6px 12px var(--shadow-medium); /* Enhanced shadow */
      transition: background-color 0.3s ease, transform 0.2s ease, box-shadow 0.3s ease;
      margin-top: 2rem;
      letter-spacing: 0.5px;
    }

    #fake-site button:hover {
      background-color: var(--primary-hover-color);
      transform: translateY(-3px) scale(1.02); /* More pronounced hover */
      box-shadow: 0 8px 16px var(--shadow-medium);
    }

    /* Virus Screen styles */
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

    /* Main Content Styles */
    #main {
      padding: 3.5rem; /* More padding */
      background: var(--card-background);
      min-height: auto;
      color: var(--text-dark);
      user-select: none;
      display: none;
      border-radius: 16px; /* Consistent border-radius */
      box-shadow: 0 10px 30px var(--shadow-medium); /* Consistent shadow */
      max-width: 90%;
      margin-top: 2.5rem; /* More margin */
      box-sizing: border-box;
      animation: fadeInScale 0.8s ease-out forwards;
    }

    #main.visible {
      display: block;
      animation: fadeInScale 0.8s ease-out forwards;
    }

    #main h1 {
      font-size: 3.2rem; /* Consistent large heading */
      margin-bottom: 2.5rem;
      color: #444;
      font-weight: 700;
      letter-spacing: -0.5px;
      text-shadow: 1px 1px 3px var(--shadow-light);
    }

    /* Main Navigation Buttons (only for Quiz button now) */
    .main-nav-buttons {
      display: flex;
      justify-content: center;
      gap: 1.5rem; /* Increased gap */
      margin-bottom: 3rem; /* More margin */
      flex-wrap: wrap;
    }

    .main-nav-buttons button {
      padding: 1.4rem 3rem; /* Larger button */
      font-size: 1.6rem; /* Larger font */
      cursor: pointer;
      background-color: var(--secondary-color); /* Use secondary color for this button */
      border: none;
      border-radius: 10px;
      color: var(--text-light);
      font-weight: 600;
      box-shadow: 0 4px 10px var(--shadow-light);
      transition: background-color 0.3s ease, transform 0.2s ease, box-shadow 0.3s ease;
      letter-spacing: 0.5px;
    }

    .main-nav-buttons button:hover {
      background-color: var(--secondary-hover-color);
      transform: translateY(-2px) scale(1.01);
      box-shadow: 0 6px 12px var(--shadow-medium);
    }

    /* Schedule Content Styles */
    #schedule-content {
      background: var(--card-background);
      padding: 2.5rem; /* More padding */
      border-radius: 12px; /* Consistent border-radius */
      box-shadow: 0 4px 15px var(--shadow-light); /* Deeper shadow */
      margin-top: 2rem;
      min-height: 350px; /* Slightly larger min-height */
      display: none;
      width: 100%;
      max-width: 800px;
      box-sizing: border-box;
      border: 1px solid var(--border-color); /* Subtle border */
    }

    #schedule-content.active {
      display: block;
    }

    #schedule-content .schedule-nav-buttons {
        margin-bottom: 2rem; /* More margin */
        display: flex;
        flex-wrap: wrap;
        justify-content: center;
        gap: 0.8rem; /* Consistent gap */
    }

    #schedule-content .schedule-nav-buttons button {
        background-color: #78909c; /* Blue-grey for schedule buttons */
        color: white;
        border: none;
        padding: 0.9rem 1.8rem; /* Larger padding */
        border-radius: 6px;
        cursor: pointer;
        font-size: 1.2rem; /* Larger font */
        font-weight: 500;
        transition: background-color 0.2s ease, transform 0.1s ease;
        box-shadow: 0 2px 5px rgba(0,0,0,0.1);
    }
    #schedule-content .schedule-nav-buttons button:hover {
        background-color: #607d8b;
        transform: translateY(-1px);
    }

    #schedule-image-container img {
      max-width: 100%; /* Ensure it fits container */
      height: auto; /* Maintain aspect ratio */
      max-height: 75vh; /* Allow it to be taller */
      border: 3px solid var(--border-color); /* Thicker border */
      border-radius: 10px; /* More rounded */
      box-shadow: 0 5px 15px var(--shadow-light); /* Deeper shadow */
      margin-top: 2rem;
      object-fit: contain; /* Ensure image fits without cropping */
    }

    /* Share Buttons Styles */
    #share-buttons {
      margin-top: 3rem; /* More margin */
      display: none;
      justify-content: center;
      gap: 1.8rem; /* Increased gap */
      flex-wrap: wrap;
    }

    #share-buttons.visible {
      display: flex;
    }

    .share-btn {
      cursor: pointer;
      padding: 1.2rem 2.5rem; /* Larger padding */
      border-radius: 12px; /* More rounded */
      font-weight: 600;
      font-size: 1.3rem; /* Larger font */
      color: var(--text-light);
      box-shadow: 0 4px 10px var(--shadow-light);
      display: flex;
      align-items: center;
      gap: 1rem; /* More space for icon */
      user-select: none;
      transition: transform 0.1s ease-in-out, box-shadow 0.2s ease;
    }

    .share-btn:hover {
      transform: translateY(-2px);
      box-shadow: 0 6px 12px var(--shadow-medium);
    }

    .share-x {
      background-color: #1a8cd8; /* Official X blue */
    }

    .share-x:hover {
      background-color: #167ac7;
    }

    .share-line {
      background-color: #00c300;
    }

    .share-line:hover {
      background-color: #00a000;
    }

    .share-icon {
      font-size: 1.6rem; /* Larger icon */
    }

    /* Responsive Design */
    @media (max-width: 768px) {
        #fake-site {
            padding: 2.5rem;
        }
        #fake-site h1 {
            font-size: 3rem;
            margin-bottom: 2rem;
        }
        #fake-site h1 span:first-child {
            font-size: 2rem;
        }
        #fake-site button {
            padding: 1.2rem 2.5rem;
            font-size: 1.6rem;
        }
        #main {
            padding: 2rem;
            margin-top: 1.5rem;
        }
        #main h1 {
            font-size: 2.5rem;
            margin-bottom: 2rem;
        }
        .main-nav-buttons button {
            padding: 1rem 2rem;
            font-size: 1.4rem;
        }
        #schedule-content {
            padding: 1.8rem;
        }
        #schedule-content .schedule-nav-buttons button {
            padding: 0.7rem 1.4rem;
            font-size: 1rem;
        }
        .share-btn {
            padding: 1rem 1.8rem;
            font-size: 1.2rem;
            gap: 0.8rem;
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
    }

    @media (max-width: 480px) {
      #fake-site {
          padding: 1.5rem;
          margin: 1rem;
      }
      #fake-site h1 {
          font-size: 2.2rem;
          margin-bottom: 1.2rem;
      }
      #fake-site h1 span:first-child {
          font-size: 1.6rem;
      }
      #fake-site button {
          padding: 0.9rem 1.8rem;
          font-size: 1.3rem;
          margin-top: 1rem;
      }
      #main {
          padding: 1.5rem;
          margin-top: 1rem;
      }
      #main h1 {
          font-size: 2rem;
          margin-bottom: 1.5rem;
      }
      .main-nav-buttons {
          flex-direction: column; /* Stack buttons vertically */
          gap: 0.8rem;
      }
      .main-nav-buttons button {
          width: 90%; /* Full width */
          max-width: 300px;
          padding: 0.8rem 1.5rem;
          font-size: 1.2rem;
      }
      #schedule-content {
          padding: 1rem;
      }
      #schedule-content .schedule-nav-buttons {
          flex-direction: row; /* Keep schedule day buttons horizontal if space allows */
          flex-wrap: wrap;
          justify-content: center;
          gap: 0.5rem;
      }
      #schedule-content .schedule-nav-buttons button {
          padding: 0.6rem 1.1rem;
          font-size: 0.95rem;
      }
      .share-btn {
          padding: 0.7rem 1.2rem;
          font-size: 1rem;
          gap: 0.5rem;
      }
      .share-icon {
          font-size: 1.2rem;
      }
      #virus-screen h2 {
          font-size: 1.5rem;
          margin-bottom: 1rem;
      }
      #countdown {
          font-size: 3.5rem;
      }
      #reveal-message {
          font-size: 1rem;
          padding: 0.8rem;
      }
    }
  </style>
</head>
<body>
  <div id="fake-site" role="main" aria-label="ようこそ文化祭まとめサイトへ">
    <h1>
      <span style="display:block;">ようこそ</span>
      <span style="display:block;">文化祭まとめサイトへ</span>
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

      // const showScheduleBtn = document.getElementById("show-schedule-btn"); // スケジュールボタンは削除
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

      // スケジュールボタンは削除されたため、このイベントリスナーは不要
      // showScheduleBtn.addEventListener("click", () => showContent('schedule')); 
      
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
