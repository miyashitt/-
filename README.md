<!DOCTYPE html>
<html lang="ja">
<head>
  <meta charset="UTF-8" />
  <title>文化祭まとめサイト</title>
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <style>
    body {
      margin: 0;
      font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
      background: #f4f4f4;
      color: #333;
      text-align: center;
      min-height: 100vh;
      display: flex;
      flex-direction: column;
      justify-content: center;
      align-items: center;
      user-select: none;
      overflow-y: auto;
      transition: background-color 0.3s ease-in-out, opacity 0.5s ease-in-out;
      opacity: 0; /* 最初は非表示 */
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

    #fake-site {
      display: flex;
      flex-direction: column;
      align-items: center;
      background-color: #fff;
      padding: 3rem;
      border-radius: 12px;
      box-shadow: 0 4px 12px rgba(0, 0, 0, 0.1);
    }

    #fake-site h1 {
      font-size: 3.5rem;
      margin-bottom: 2.5rem;
      text-shadow: 1px 1px 0 #eee, 2px 2px 4px rgba(0, 0, 0, 0.1);
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
      background-color: #007bff;
      border: none;
      padding: 1.5rem 3.5rem;
      font-size: 1.8rem;
      font-weight: 600;
      border-radius: 8px;
      color: #fff;
      cursor: pointer;
      box-shadow: 0 4px 8px rgba(0, 0, 0, 0.15);
      transition: background-color 0.2s ease-in-out, transform 0.1s ease-in-out;
      margin-top: 1.5rem;
    }

    #fake-site button:hover {
      background-color: #0056b3;
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
      pointer-events: none;
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
      color: #333;
      user-select: none;
      display: none;
      border-radius: 12px;
      box-shadow: 0 4px 12px rgba(0, 0, 0, 0.1);
      max-width: 90%;
      margin-top: 2rem;
    }

    #main.visible {
      display: block;
      animation: fade-in 0.3s ease-in-out;
    }

    #main h1 {
      font-size: 3rem;
      margin-bottom: 2rem;
      text-shadow: 1px 1px 0 #eee, 2px 2px 4px rgba(0, 0, 0, 0.1);
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
      background-color: #007bff; /* 青系のボタン */
      border: none;
      border-radius: 8px;
      color: #fff;
      font-weight: 600;
      box-shadow: 0 2px 6px rgba(0, 0, 0, 0.1);
      transition: background-color 0.2s ease-in-out, transform 0.1s ease-in-out;
    }

    .main-nav-buttons button:hover {
      background-color: #0056b3;
      transform: translateY(-1px);
    }

    /* 各コンテンツエリアのスタイル */
    #schedule-content, #quiz-minigame-content { /* IDを修正 */
      background: #fff;
      padding: 2rem;
      border-radius: 8px;
      box-shadow: 0 2px 8px rgba(0, 0, 0, 0.05);
      margin-top: 1rem;
      min-height: 300px; /* ある程度の高さを確保 */
      display: none; /* 初期は非表示 */
      width: 100%; /* 親要素の幅に合わせる */
      max-width: 800px; /* 必要に応じて最大幅を設定 */
      box-sizing: border-box;
    }

    #schedule-content.active, #quiz-minigame-content.active { /* IDを修正 */
      display: block; /* アクティブなコンテンツを表示 */
    }

    #schedule img {
      max-width: 95%;
      max-height: 70vh;
      border: 2px solid #ddd;
      border-radius: 8px;
      box-shadow: 0 2px 8px rgba(0, 0, 0, 0.1);
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
      box-shadow: 0 2px 6px rgba(0, 0, 0, 0.1);
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

    /* クイズゲームのスタイル */
    #quiz-minigame-content { /* IDを修正 */
      background: #f8f8f8; /* クイズの背景色 */
      padding: 20px;
      box-sizing: border-box;
    }

    #quiz-minigame-content h1 { /* IDを修正 */
      font-size: 2em;
      margin-bottom: 20px;
      color: #333;
    }

    .question-box {
      font-size: 1.5em;
      min-height: 100px;
      margin: 20px auto;
      width: 90%; /* 幅を調整 */
      max-width: 600px; /* 最大幅を設定 */
      background: #fff;
      padding: 20px;
      border-radius: 10px;
      box-shadow: 0 2px 5px rgba(0,0,0,0.1);
      display: flex;
      align-items: center;
      justify-content: center;
      text-align: center;
      word-break: break-word; /* 長い単語の折り返し */
    }

    .choices {
      display: flex;
      flex-wrap: wrap;
      justify-content: center;
      gap: 10px;
      margin-top: 20px;
    }

    .choice {
      padding: 10px 20px;
      background: #ddd;
      border-radius: 10px;
      cursor: pointer;
      font-size: 1.3em;
      transition: background 0.3s ease;
      flex-grow: 1; /* 均等に幅を広げる */
      max-width: 45%; /* 2列表示にするための調整 */
      box-sizing: border-box;
    }

    .choice:hover {
      background: #bbb;
    }

    #quiz-startBtn, #quiz-nextBtn, #quiz-buzzBtn { /* IDを修正 */
      padding: 10px 20px;
      font-size: 1.2em;
      margin-top: 10px;
      cursor: pointer;
      background-color: #4CAF50; /* 緑系のボタン */
      color: white;
      border: none;
      border-radius: 5px;
      transition: background-color 0.3s ease;
    }

    #quiz-startBtn:hover, #quiz-nextBtn:hover, #quiz-buzzBtn:hover { /* IDを修正 */
      background-color: #45a049;
    }

    #quiz-timer, #quiz-answerBox, #quiz-scoreBox, #quiz-bestScoreBox, #quiz-feedbackBox { /* IDを修正 */
      margin-top: 20px;
      font-size: 1.2em;
      color: #555;
    }

    #quiz-feedbackBox { /* IDを修正 */
      transition: opacity 0.6s ease;
      white-space: pre-wrap; /* コメントの改行を有効にする */
    }

    /* YouTube動画の埋め込みスタイル */
    .youtube-container {
        position: relative;
        width: 100%;
        padding-bottom: 56.25%; /* 16:9 アスペクト比 */
        height: 0;
        margin-top: 15px;
    }
    .youtube-container iframe {
        position: absolute;
        top: 0;
        left: 0;
        width: 100%;
        height: 100%;
    }
  </style>
</head>
<body>
  <div id="fake-site" class="hidden" role="main" aria-label="ようこそ文化祭まとめサイトへ">
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

  <div id="main" class="hidden" role="main" aria-label="文化祭まとめコンテンツ">
    <button id="repeat-virus-btn" type="button">もう一度ウイルス演出を見る</button>
    
    <div class="main-nav-buttons">
      <button id="show-schedule-btn" type="button">文化祭スケジュール</button>
      <button id="show-quiz-minigame-btn" type="button">クイズ＆ミニゲーム</button>
    </div>

    <div id="schedule-content" class="active">
      <h1>文化祭スケジュール</h1>
      <button type="button" onclick="showScheduleImage('junbi')">準備日</button>
      <button type="button" onclick="showScheduleImage('day1')">一日目</button>
      <button type="button" onclick="showScheduleImage('day2')">二日目</button>
      <button type="button" onclick="showScheduleImage('kataduke')">片付け日</button>
      <div id="schedule" aria-live="polite" aria-atomic="true"></div>
    </div>

    <div id="quiz-minigame-content" class="hidden">
      <h1>縣陵百科クイズ</h1>
      <audio id="bgm" src="https://cdn.pixabay.com/download/audio/2023/03/14/audio_ef16bc184b.mp3?filename=fun-pop-music-14550.mp3" loop></audio>
      <audio id="correctSound" src="https://cdn.pixabay.com/download/audio/2022/03/15/audio_e4c2590b01.mp3?filename=correct-answer-2-109766.mp3"></audio>
      <audio id="wrongSound" src="https://cdn.pixabay.com/download/audio/2022/03/15/audio_f1fc1bb3f7.mp3?filename=wrong-answer-3-204254.mp3"></audio>

      <div class="question-box" id="quiz-question"></div>
      <div id="quiz-timer">制限時間: <span id="quiz-time">15</span>秒</div>
      <div id="quiz-answerBox"></div>
      <button id="quiz-buzzBtn" style="display:none;">早押し！</button>
      <div class="choices" id="quiz-choices"></div>
      <div id="quiz-feedbackBox"></div>
      <button id="quiz-nextBtn" style="display:none;">次の問題</button>
      <div id="quiz-scoreBox"></div>
      <div id="quiz-bestScoreBox"></div>
      <button id="quiz-startBtn">ゲームスタート</button>
    </div>

    <div id="share-buttons" aria-label="SNS共有ボタン" aria-hidden="true">
      <div class="share-btn share-x" role="button" tabindex="0" onclick="shareX()" title="Xで共有" aria-label="Xで共有">
        <span class="share-icon">🐦</span> Xで共有
      </div>
      <div class="share-btn share-line" role="button" tabindex="0" onclick="shareLINE()" title="LINEで共有" aria-label="LINEで共有">
        <span class="share-icon">💬</span> LINEで共有
      </div>
    </div>
  </div>

  <script>
    (() => {
      const localStorageKey = "bunkasai_visited";

      const body = document.body;
      const fakeSite = document.getElementById("fake-site");
      const virusScreen = document.getElementById("virus-screen");
      const reliefScreen = document.getElementById("relief-screen");
      const mainScreen = document.getElementById("main");
      const repeatVirusBtn = document.getElementById("repeat-virus-btn");
      const scheduleDiv = document.getElementById("schedule");
      const shareButtons = document.getElementById("share-buttons");
      const startBtn = document.getElementById("start-btn");

      // メインナビゲーションボタン
      const showScheduleBtn = document.getElementById("show-schedule-btn");
      const showQuizMinigameBtn = document.getElementById("show-quiz-minigame-btn"); // IDを修正

      // コンテンツエリア
      const scheduleContent = document.getElementById("schedule-content");
      const quizMinigameContent = document.getElementById("quiz-minigame-content"); // IDを修正

      let synth = window.speechSynthesis;
      let utterance = null;
      let alarmAudio = null;
      let voiceLoopRunning = false;
      let countdownIntervalId = null;
      let isLine = false;

      let virusTitle = null;
      let countdownElement = null;
      let revealMessageElement = null;

      function isLineBrowser() {
        return navigator.userAgent.includes("Line");
      }

      async function requestFullscreen() {
        try {
          if (document.documentElement.requestFullscreen) {
            await document.documentElement.requestFullscreen();
          }
        } catch (e) {
          console.warn("Fullscreen request failed:", e);
        }
      }

      function startJapaneseVoiceLoop() {
        if (isLine) return;

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
        if (isLine) return;

        voiceLoopRunning = false;
        try {
          synth.cancel();
        } catch (e) {
            console.warn("Failed to cancel speech synthesis:", e);
        }
      }

      async function playAlarmSound() {
        if (isLine) return null;

        try {
          const audio = new Audio("https://upload.wikimedia.wikimedia.org/wikipedia/commons/b/b2/Sos-morse-code.ogg");
          audio.loop = true;
          audio.volume = 1.0;
          await audio.play();
          return audio;
        } catch (e) {
          console.error("Failed to play alarm sound:", e);
          return null;
        }
      }

      function startVirus() {
        isLine = isLineBrowser();

        body.classList.add('virus-active');
        body.classList.remove('loaded');
        requestFullscreen();

        fakeSite.classList.add("hidden");
        reliefScreen.classList.add("hidden");
        mainScreen.classList.remove("visible");
        mainScreen.classList.add("hidden");
        
        virusScreen.classList.remove("hidden");
        virusScreen.classList.add("visible");

        while (virusScreen.firstChild) {
          virusScreen.removeChild(virusScreen.firstChild);
        }

        virusTitle = document.createElement("h2");
        virusTitle.textContent = "⚠ ウイルスに感染しました";
        virusScreen.appendChild(virusTitle);

        countdownElement = document.createElement("div");
        countdownElement.id = "countdown";
        virusScreen.appendChild(countdownElement);

        revealMessageElement = document.createElement("div");
        revealMessageElement.id = "reveal-message";
        revealMessageElement.classList.add("hidden");
        revealMessageElement.innerHTML = "ご安心ください！これは文化祭の面白い仕掛けです。<br>あなたのデータは無事です！";
        virusScreen.appendChild(revealMessageElement);
        
        if (!isLine) {
            startJapaneseVoiceLoop();
            playAlarmSound().then(audio => { alarmAudio = audio; });
        }

        let count = 5;
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

            if (!isLine) {
                stopJapaneseVoiceLoop();
                if (alarmAudio) {
                  alarmAudio.pause();
                  alarmAudio.currentTime = 0;
                  alarmAudio = null;
                }
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

        while (virusScreen.firstChild) {
          virusScreen.removeChild(virusScreen.firstChild);
        }

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
        // 初期表示はスケジュールコンテンツ
        showContent('schedule');
      }

      function showShareButtons() {
        shareButtons.classList.add("visible");
        shareButtons.setAttribute("aria-hidden", "false");
      }

      function showScheduleImage(day) {
        if (!day) {
          scheduleDiv.innerHTML = "<p>上記ボタンからスケジュールを選択してください。</p>";
          return;
        }
        scheduleDiv.innerHTML = `<img src="${day}.png" alt="${day}のスケジュール" />`;
      }

      function createExternalBrowserURL(originalUrl) {
        const encodedUrl = encodeURIComponent(originalUrl);
        return `googlechrome://navigate?url=${encodedUrl}`;
      }

      function shareX() {
        const currentURL = location.href;
        const externalURL = createExternalBrowserURL(currentURL);
        const shareText = encodeURIComponent("文化祭まとめサイトをチェック！");

        const url = `https://twitter.com/intent/tweet?text=${shareText}%0A${encodeURIComponent(externalURL)}`;
        window.open(url, "_blank", "noopener");
      }

      function shareLINE() {
        const currentURL = location.href;
        const externalURL = createExternalBrowserURL(currentURL);
        const shareText = encodeURIComponent("文化祭まとめサイトをチェック！");

        const url = `https://line.me/R/msg/text/?${shareText}%0A${encodeURIComponent(externalURL)}`;
        window.open(url, "_blank", "noopener");
      }

      // コンテンツ切り替え関数
      function showContent(contentId) {
        const contents = [scheduleContent, quizMinigameContent]; // 変更
        contents.forEach(content => {
          content.classList.remove('active');
          content.classList.add('hidden');
        });

        if (contentId === 'schedule') {
          scheduleContent.classList.add('active');
          scheduleContent.classList.remove('hidden');
          // クイズゲームの音声を停止
          if (!isLineBrowser()) {
            document.getElementById('bgm').pause();
            document.getElementById('bgm').currentTime = 0;
          }
        } else if (contentId === 'quiz-minigame') { // 変更
          quizMinigameContent.classList.add('active'); // 変更
          quizMinigameContent.classList.remove('hidden'); // 変更
          // クイズ表示時にBGMを再生（LINEブラウザ以外）
          if (!isLineBrowser()) {
            document.getElementById('bgm').play().catch(e => console.error("BGM playback failed:", e));
          }
        }
      }

      window.addEventListener("load", () => {
        const visited = localStorage.getItem(localStorageKey);
        if (!visited) {
          fakeSite.classList.remove("hidden");
          fakeSite.style.display = "flex";
        } else {
          showMainScreen();
          showShareButtons();
        }
        body.classList.add('loaded');
      });

      startBtn.addEventListener("click", () => {
        startVirus();
      });

      repeatVirusBtn.addEventListener("click", () => {
        startVirus();
      });

      // ナビゲーションボタンのイベントリスナー
      showScheduleBtn.addEventListener("click", () => showContent('schedule'));
      showQuizMinigameBtn.addEventListener("click", () => showContent('quiz-minigame')); // 変更

      window.showScheduleImage = showScheduleImage;
      window.shareX = shareX;
      window.shareLINE = shareLINE;

      // クイズゲームのスクリプト
      const fullData = [
        {q:"伝説の文化祭OPは？○○○○サマー", a:"アーリー", comment:"幾度となく塗り替えようとされてきたがいまだにこれを超えるクオリティーの曲は発表されていない。音源は生徒会が管理しており、一般生徒が触れることはできない"},
        {q:"専門は家族社会学、ジェンダー論、女性学である、日本のフェミニスト・社会学者は？", a:"うえのちづこ", comment:""},
        {q:"高校生向け化学の動画を投稿し大学入試センターと戦うチャンネルは？\nOnline Chemistry by ○○○○○", a:"ヒガシマキ", comment:"https://www.youtube.com/embed/PjEwIq7y7w8"},
        {q:"縣陵生になると体育の時間に覚えさせられるものは?", a:"けんりょうたいそう", comment:"「準備体操とは元々軍隊などで訓練のために行われていたものである。」という真偽不明の由来故になかなかハードで準備体操にしては長めな運動を体育の前にやらされる。先生によっては少し喋っただけで最初からやり直しとなる可能性もあり、注意が必要である。なお2年生以降ではその存在は突然無くなり覚えている人間は、強力な洗脳に耐えた1部の者だけであり秘密裏にその存在は語り継がれている。「やる意味が無い」というような発言をした者は1人残らず消されている。"},
        {q:"質実剛健であれ　大道を闊歩せよ　あとひとつは？", a:"よわねをはくな", comment:"3つから成る我が校に古くから伝わる三大精神である。ほとんどの縣陵生は弱音を吐くなしか知らない。お昼の放送の曲で軽くあしらわれているが、実は在学3年間にこの精神の下、高校生活を遂行したものは殿堂入りを果たすことができる。しかし未だ達成したものはいない。"},
        {q:"地球の会←なんて読む？", a:"そらのかい", comment:"難読漢字の一種。ただの初見殺し。部活は月1"},
        {q:"縣陵応援団の言うPTAのAとは?", a:"アルコール", comment:"パチンコ、タバコ、アルコールの略であり縣陵生の誰もが知っている。応援団の「いいかお前らPTAには手を出すなよ」というフレーズは去年流行語大賞に選ばれた。"},
        {q:"小体育館の下に存在している場所は？", a:"ピロティ", comment:"最初に言われたときはどこのことか全くわからない。特に一年生の物販委販売の時に迷子が目立つ。その利用方法は多岐にわたり、普段はダンス部が利用しているが時には応援団の練習場所としてもつかわれる。大した場所ではないがここまで使わないとあの狭い校舎には人が入りきらない。"},
        {q:"第76th縣陵祭テーマソングは？", a:"ひゃっぽ", comment:"神曲。もうすぐでYouTube1万回再生される、第76回縣陵祭のテーマソングである。下級生から神と称えられるメンバーにより作詞作曲された。ほんと神曲。好き。\nhttps://www.youtube.com/embed/g1L2Kk_2bGE"},
        {q:"焼肉きんぐあがた店はかつてなんだった？", a:"おこほん", comment:"広丘駅に大きな店舗があり、昔はすべての縣陵生徒がそこでパーティーをしていた。ただしおこほん運営も縣陵生が消費量の大半を占めていることを知り、松本県店を進出させた。今では焼肉きんぐに。"},
        {q:"2学年が探究成果を発表する大会とは？", a:"KRGP", comment:"優秀賞が普通科から3名、探究科から3名、計6名選出され、その中から大賞が1名大学の教授や校長によって選ばれる。"},
        {q:"お昼に流れる校内放送の名称は？", a:"けんりょうオンエア", comment:"独立したメディアかと思えばコンテンツ内容に関しては生徒会が一枚かんでいる。要するに縣陵版のN〇Kである。"},
        {q:"県ケ丘高校の文化祭の名称は？", a:"けんりょうさい", comment:"入場者は5000人程で山形村の人口くらい。毎年この準備のため生徒会役員は官僚レベルの重労働が強制される。"},
        {q:"8つの学部と6つの大学院を持つ総合大学で、学部には人文学部、教育学部、経法学部、理学部、医学部、工学部、農学部、繊維学部がある、長野県松本市に本部を置く国立大学は？", a:"しんしゅうだいがく", comment:"縣陵生が実質支配しているといっても過言ではない大学。松本にもキャンパスがあり、一年生は全員松本で過ごすので松本のことをよく知っている縣陵生は少し優位に立てるといわれいている。しかも実家通いのためお金の心配をしなくてもよい。"},
        {q:"中学校や高等学校において、生徒が主体的に学校生活の改善や向上を目指すための組織は？", a:"せいとかい", comment:"事実上の独裁体制を敷いていると思われがちだが、アニメやラノベ小説の世界ほど権力は高くない。"},
        {q:"松本市の中心部を走る城下町まつもとの有名観光スポットを巡るのにぴったりな周遊バスは？", a:"タウンスニーカー", comment:"学校から松本駅までの間を送迎してくれるサービス。雨の日は特に特に使用率が高く学校前のバス停には長蛇の列ができる。\n7月豪雨の際にはバス待ちの生徒の列が松本駅まで続いたという。"},
        {q:"基礎的な学力に加え、知識を総合的に活用する能力や、課題解決力、創造力、表現力を養うことを目的とする、生徒が自ら課題を設定し、解決に向けて探究活動を行うことを重視した松本県ヶ丘高校の学科は？", a:"たんきゅうか", comment:"英語科を前身として生み出された精鋭部隊。週2回の探究の授業を組み込むために授業進度はかなり無理をして頑張っている。倍率は年々異なるがだいたい2倍近くあり小論文がどんどん難しくなっている。変人:変態:常人＝5:4:1"},
        {q:"学校で、各教科の学習成果を評価するために、定期的に行われる試験は？", a:"ていきこうさ", comment:"全校強制参加のエクストリームスポーツ。年5回ほど行われていて、英気を養うための7日間というものがテスト前に設けられている。生徒たちはこの期間に日々の生活時間の乱れを回復するために睡眠時間を多くとる"},
        {q:"書籍や記録などの資料を収集・整理・保存し、一般の人々が利用できるように提供する施設は？", a:"としょかん", comment:"本を読むまたは借りる目的で使う人は存在しない。治外法権が適応されている場所であり、授業をサボることが罪に問われない。ただし文系科目の自習の時間に使われることがまれにあるため、容易に授業中逃げ込むことは避けた方が良い。"},
        {q:"応援練習における、発声練習で発する語は？", a:"け", comment:""},
        {q:"学校教育法で定められた春季休暇は？", a:"はるやすみ", comment:"春休みと呼ばれる期間はたった2週間以下だが、３月はなんだかんだ休みがたくさんあるので困ることはない。春が来たからと言って君の彼女いない歴＝年齢の呪いが終わるわけではないのでせいぜい頑張ってほしい。お決まり通り課題は鬼。"},
        {q:"数研出版から出版されている高校数学の網羅系参考書で、一般に上から二番目の難易度とされる問題集は青チャートであるが、それと比較されがちな啓林館が出版しているマスター編、チャレンジ編、実践編で構成される参考書は？", a:"フォーカスゴールド", comment:"世間一般でも使われている標準的な数学参考書。その使い方は実に多様であり漬物石、トレーニング、鈍器、聖書…etc.　"},
        {q:"松本県ヶ丘高等学校の応援歌において1つだけ毛色の異なる応援歌はカタカナ三文字で？", a:"ラララ", comment:"応援歌の一つ。その歌い出しと、途中の掛け声が特徴である。1年生のうちは応援団の恐怖により触れることを畏れるが、2・3年生になると仲間内でふざけている最中に「ラーラーラ　そーーれ」という掛け声とともに歌い出す。"},
        {q:"主に大学受験において、現役で志望校に合格できず、もう一年受験勉強に励む人のことをなんと言う？", a:"ろうにん", comment:"楽しい高校生活をもう一年行えるエクストリームスポーツ。"}
      ];

      const HIRAGANA = [..."あいうえおかきくけこさしすせそたちつてとなにぬねのはひふへほまみむめもやゆよらりるれろわをん"];
      const KATAKANA = [..."アイウエオカキクケコサシスセソタチツテトナニヌネノハヒフヘホマミムメもヤユヨラリルレロワヲンー"];
      const ALPHABET = [..."ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz"];
      const NUMBERS = [..."0123456789"];
      const SYMBOLS = [..."！？、。ー・"]; 

      let quizData = [], current = 0, score = 0, bestScore = 0,
          currentAnswer = "", answerProgress = "",
          revealTimer = null, answerTimer = null,
          startTime = 0;

      const qEl = document.getElementById('quiz-question');
      const choicesEl = document.getElementById('quiz-choices');
      const timerEl = document.getElementById('quiz-time');
      const feedbackBox = document.getElementById('quiz-feedbackBox');
      const scoreBox = document.getElementById('quiz-scoreBox');
      const bestBox = document.getElementById('quiz-bestScoreBox');
      const answerBox = document.getElementById('quiz-answerBox');
      const buzzBtn = document.getElementById('quiz-buzzBtn');
      const nextBtn = document.getElementById('quiz-nextBtn');
      const quizStartBtn = document.getElementById('quiz-startBtn');
      const bgm = document.getElementById('bgm');
      const correctSound = document.getElementById('correctSound');
      const wrongSound = document.getElementById('wrongSound');
