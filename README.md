<!DOCTYPE html>
<html lang="ja">
<head>
  <meta charset="UTF-8" />
  <title>文化祭スケジュール</title>
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
      transition: background-color 0.3s ease-in-out, opacity 0.5s ease-in-out; /* opacityのtransitionを追加 */
      opacity: 0; /* 最初は非表示 */
    }

    /* bodyが準備できた時に表示する */
    body.loaded {
      opacity: 1;
    }

    /* ウイルス画面表示時のbodyスタイル */
    body.virus-active {
      background-color: #000 !important;
      margin: 0 !important;
      padding: 0 !important;
      overflow: hidden !important;
      opacity: 1 !important; /* ウイルス画面中は常に表示 */
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

    #main button {
      padding: 1.2rem 2.5rem;
      font-size: 1.4rem;
      margin: 0.8rem;
      cursor: pointer;
      background-color: #28a745;
      border: none;
      border-radius: 8px;
      color: #fff;
      font-weight: 600;
      box-shadow: 0 2px 6px rgba(0, 0, 0, 0.1);
      transition: background-color 0.2s ease-in-out, transform 0.1s ease-in-out;
    }

    #main button:hover {
      background-color: #1e7e34;
      transform: translateY(-1px);
    }

    #repeat-virus-btn {
      background-color: #dc3545;
      margin-bottom: 1.5rem;
      box-shadow: 0 2px 6px rgba(0, 0, 0, 0.1);
    }

    #repeat-virus-btn:hover {
      background-color: #c82333;
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

  <div id="main" class="hidden" role="main" aria-label="文化祭スケジュール">
    <button id="repeat-virus-btn" type="button">もう一度ウイルス演出を見る</button>
    <h1>文化祭スケジュール</h1>
    <button type="button" onclick="showSchedule('junbi')">準備日</button>
    <button type="button" onclick="showSchedule('day1')">一日目</button>
    <button type="button" onclick="showSchedule('day2')">二日目</button>
    <button type="button" onclick="showSchedule('kataduke')">片付け日</button>
    <div id="schedule" aria-live="polite" aria-atomic="true"></div>
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
        body.classList.remove('loaded'); // ウイルス画面中はフェードアウトしないようにloadedを削除
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
          body.classList.add('loaded'); // メイン画面表示時にフェードイン
        }, 3000);
      }

      function showMainScreen() {
        mainScreen.classList.remove("hidden");
        mainScreen.classList.add("visible");
      }

      function showShareButtons() {
        shareButtons.classList.add("visible");
        shareButtons.setAttribute("aria-hidden", "false");
      }

      function showSchedule(day) {
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

      window.addEventListener("load", () => {
        const visited = localStorage.getItem(localStorageKey);
        if (!visited) {
          fakeSite.classList.remove("hidden");
          fakeSite.style.display = "flex";
        } else {
          showMainScreen();
          showShareButtons();
        }
        body.classList.add('loaded'); // ページロード完了後、bodyをフェードイン
      });

      startBtn.addEventListener("click", () => {
        startVirus();
      });

      repeatVirusBtn.addEventListener("click", () => {
        startVirus();
      });

      window.showSchedule = showSchedule;
      window.shareX = shareX;
      window.shareLINE = shareLINE;
    })();
  </script>
</body>
</html>
