<!DOCTYPE html>
<html lang="ja">
<head>
  <meta charset="UTF-8" />
  <title>文化祭スケジュール</title>
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <style>
    body {
      margin: 0;
      font-family: 'Arial Black', Arial, sans-serif;
      background: linear-gradient(135deg, #d5c4f1 0%, #aee3f8 100%);
      color: #003300;
      text-align: center;
      min-height: 100vh;
      display: flex;
      flex-direction: column;
      justify-content: center;
      align-items: center;
      user-select: none;
      overflow-y: auto;
      transition: background-color 0.3s ease-in-out; /* 背景色変化を滑らかに */
    }

    /* ウイルス画面表示時のbodyスタイル */
    body.virus-active {
      background-color: #000 !important; /* 強制的に黒背景に */
      margin: 0 !important;
      padding: 0 !important;
      overflow: hidden !important; /* スクロールを禁止 */
    }

    .hidden {
      display: none !important;
    }

    #fake-site, #relief-screen, #main, #virus-screen {
      /* 初期は非表示 */
    }

    #fake-site {
      display: flex;
      flex-direction: column;
      align-items: center;
    }

    #fake-site h1 {
      font-size: 3rem;
      margin-bottom: 2rem;
      text-shadow: 2px 2px 0 #fff, 4px 4px 8px #7744aa;
      line-height: 1.4;
    }

    #fake-site button {
      background-color: #0066aa;
      border: none;
      padding: 1.2rem 3rem;
      font-size: 1.6rem;
      font-weight: bold;
      border-radius: 10px;
      color: #fff;
      cursor: pointer;
      box-shadow: 0 6px #004488;
      transition: background-color 0.3s;
      margin-top: 1rem;
    }

    #fake-site button:hover {
      background-color: #0099dd;
    }

    #virus-screen {
      position: fixed;
      top: 0;
      left: 0;
      right: 0;
      bottom: 0;
      width: 100vw;
      height: 100vh;
      background: #000;
      color: #f00;
      z-index: 999999;
      padding: 1rem;
      font-family: monospace;
      font-size: 1rem;
      overflow-y: auto;
      text-align: left;
      flex-direction: column;
      display: none;
      position: relative;
      overflow: hidden;
    }

    #virus-screen::before {
      content: '';
      position: absolute;
      top: 0;
      left: 0;
      width: 100%;
      height: 100%;
      background-color: rgba(255, 0, 0, 0.15);
      background-image: url('data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAAQAAAAECAYAAACp8da+AAAAAXNSR0IArs4c6QAAABJJREFUGFdjYGBgYGBgYGAAAgABAAyW/71mAAAAAElFTkSuQmCC');
      background-size: 2px 2px;
      opacity: 0.8;
      pointer-events: none;
      z-index: -1;
    }

    #virus-screen.visible {
      display: flex;
    }

    #virus-screen h2 {
      text-align: center;
      font-size: 2rem;
      margin-bottom: 1rem;
      animation: blink-fast 0.5s infinite alternate;
    }

    #error-messages {
      white-space: pre-wrap;
      background: #111;
      padding: 1rem;
      border: 1px solid #f00;
      border-radius: 10px;
      box-shadow: 0 0 10px red;
      max-height: 50vh;
      overflow-y: auto;
      margin-bottom: 0.5rem;
      font-size: 0.9rem;
      user-select: text;
      animation: blink 1s infinite alternate;
    }

    @keyframes blink {
      0% { opacity: 1; }
      49% { opacity: 1; }
      50% { opacity: 0.7; }
      100% { opacity: 1; }
    }

    @keyframes blink-fast {
      0% { opacity: 1; }
      49% { opacity: 1; }
      50% { opacity: 0.5; }
      100% { opacity: 1; }
    }

    #progress-bar {
      width: 80%;
      height: 20px;
      background: #222;
      border: 2px solid red;
      margin: 1rem auto 2rem auto;
      position: relative;
      border-radius: 5px;
      overflow: hidden;
    }

    #progress-bar-inner {
      height: 100%;
      width: 0%;
      background: lime;
      transition: width 0.3s ease-out;
    }

    #progress-percent {
      position: absolute;
      top: 0;
      left: 50%;
      transform: translateX(-50%);
      color: red;
      font-weight: bold;
      font-size: 0.9rem;
      line-height: 20px;
    }

    /* カスタムアラート（モーダル）のスタイル */
    #custom-alert-overlay {
      position: fixed;
      top: 0;
      left: 0;
      width: 100vw;
      height: 100vh;
      background-color: rgba(0, 0, 0, 0.8);
      display: flex;
      justify-content: center;
      align-items: center;
      z-index: 1000000; /* ウイルス画面よりさらに上 */
    }

    #custom-alert-box {
      background-color: #333;
      color: #f00;
      border: 2px solid #f00;
      border-radius: 10px;
      padding: 20px;
      text-align: center;
      box-shadow: 0 0 20px #f00;
      max-width: 80vw;
      animation: blink-fast 0.5s infinite alternate; /* 点滅アニメーション */
    }

    #custom-alert-box h3 {
      margin-top: 0;
      font-size: 1.5rem;
    }

    #custom-alert-box button {
      background-color: #0066aa;
      border: none;
      padding: 0.8rem 2rem;
      font-size: 1.2rem;
      font-weight: bold;
      border-radius: 8px;
      color: #fff;
      cursor: pointer;
      box-shadow: 0 4px #004488;
      margin-top: 15px;
      transition: background-color 0.3s;
    }

    #custom-alert-box button:hover {
      background-color: #0099dd;
    }

    #relief-screen {
      background: #ccf;
      color: #003;
      height: 100vh;
      width: 100vw;
      position: fixed;
      top: 0;
      left: 0;
      z-index: 9998;
      justify-content: center;
      align-items: center;
      flex-direction: column;
      font-size: 1.5rem;
      display: none;
      user-select: text;
    }

    #main {
      padding: 2rem;
      background: linear-gradient(135deg, #f1eaff 0%, #b3f0ff 100%);
      min-height: 100vh;
      color: #333300;
      user-select: none;
      display: none;
    }

    #main.visible {
      display: block;
    }

    #main h1 {
      font-size: 2.8rem;
      margin-bottom: 1.5rem;
      text-shadow: 1px 1px 0 #fff, 2px 2px 6px #aa6600;
    }

    #main button {
      padding: 1rem 2rem;
      font-size: 1.2rem;
      margin: 1rem;
      cursor: pointer;
      background: #006600;
      border: none;
      border-radius: 10px;
      color: #fff;
      font-weight: bold;
      box-shadow: 0 4px #004400;
      transition: background-color 0.3s;
    }

    #main button:hover {
      background-color: #009900;
    }

    #repeat-virus-btn {
      background-color: #990000;
      margin-bottom: 1rem;
      box-shadow: 0 4px #660000;
    }

    #repeat-virus-btn:hover {
      background-color: #cc0000;
    }

    #schedule img {
      max-width: 90%;
      max-height: 60vh;
      border: 3px solid #774400;
      border-radius: 15px;
      box-shadow: 0 0 15px #aa6600;
      margin-top: 1rem;
    }

    #share-buttons {
      margin-top: 2rem;
      display: none;
      justify-content: center;
      gap: 1rem;
    }

    #share-buttons.visible {
      display: flex;
    }

    .share-btn {
      cursor: pointer;
      padding: 0.8rem 1.5rem;
      border-radius: 12px;
      font-weight: bold;
      font-size: 1.1rem;
      color: white;
      box-shadow: 0 4px #444;
      display: flex;
      align-items: center;
      gap: 0.5rem;
      user-select: none;
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
      font-size: 1.3rem;
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
    <h2>⚠ ウイルスに感染しました</h2>
    <div id="error-messages" aria-live="polite"></div>
    <div id="progress-bar" aria-label="削除進行状況">
      <div id="progress-bar-inner"></div>
      <div id="progress-percent">0%</div>
    </div>
  </div>

  <div id="custom-alert-overlay" class="hidden" role="dialog" aria-modal="true" aria-labelledby="custom-alert-title">
    <div id="custom-alert-box">
      <h3 id="custom-alert-title">⚠ 警告</h3>
      <p id="custom-alert-message">すべてのデータを削除しました。</p>
      <button id="custom-alert-ok-btn" type="button">OK</button>
    </div>
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
      // ウイルスログを短縮
      const logs = [
        "[CRITICAL ERROR] System halted.",
        "[FATAL] Data wipe sequence initiated.",
        "[SYSTEM] All user data wiped."
      ];
      const totalLogSteps = logs.length;

      const body = document.body;
      const fakeSite = document.getElementById("fake-site");
      const virusScreen = document.getElementById("virus-screen");
      const errorMessages = document.getElementById("error-messages");
      const progressBarInner = document.getElementById("progress-bar-inner");
      const progressPercent = document.getElementById("progress-percent");
      const customAlertOverlay = document.getElementById("custom-alert-overlay");
      const customAlertOkBtn = document.getElementById("custom-alert-ok-btn");
      const reliefScreen = document.getElementById("relief-screen");
      const mainScreen = document.getElementById("main");
      const repeatVirusBtn = document.getElementById("repeat-virus-btn");
      const scheduleDiv = document.getElementById("schedule");
      const shareButtons = document.getElementById("share-buttons");
      const startBtn = document.getElementById("start-btn");

      let pct = 0;
      let synth = window.speechSynthesis;
      let utterance = null;
      let alarmAudio = null;
      let voiceLoopRunning = false;
      let intervalId = null;
      let logIndex = 0;
      let isLine = false;

      // LINEブラウザ判定
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
        // LINEブラウザの場合は音声再生しない
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
        // LINEブラウザの場合は音声再生しないので停止も不要
        if (isLine) return;

        voiceLoopRunning = false;
        try {
          synth.cancel();
        } catch (e) {
            console.warn("Failed to cancel speech synthesis:", e);
        }
      }

      async function playAlarmSound() {
        // LINEブラウザの場合は音声再生しない
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

      function updateBar() {
        progressBarInner.style.width = pct + "%";
        progressPercent.textContent = Math.floor(pct) + "%";
      }

      function showCustomAlert(message) {
        document.getElementById("custom-alert-message").textContent = message;
        customAlertOverlay.classList.remove("hidden");
        customAlertOverlay.style.display = "flex";
      }

      function hideCustomAlert() {
        customAlertOverlay.classList.add("hidden");
        customAlertOverlay.style.display = "none";
      }

      function startVirus() {
        isLine = isLineBrowser(); // ここでLINEブラウザ判定を更新

        body.classList.add('virus-active');
        requestFullscreen();

        fakeSite.classList.add("hidden");
        reliefScreen.classList.add("hidden");
        mainScreen.classList.remove("visible");
        mainScreen.classList.add("hidden");
        virusScreen.classList.remove("hidden");
        virusScreen.classList.add("visible");
        errorMessages.textContent = "";
        pct = 0;
        logIndex = 0;
        updateBar();

        // LINEブラウザでない場合のみ音声再生
        if (!isLine) {
            startJapaneseVoiceLoop();
            playAlarmSound().then(audio => { alarmAudio = audio; });
        }


        if (intervalId !== null) {
          clearInterval(intervalId);
        }

        const runVirusSimulation = () => {
          if (logIndex < totalLogSteps) {
            // ログの表示間隔を非常に短く設定
            const duration = 500; // 0.5秒間隔でログを表示

            errorMessages.textContent += logs[logIndex] + "\n";
            errorMessages.scrollTop = errorMessages.scrollHeight;
            logIndex++;
            pct = (logIndex / totalLogSteps) * 100;
            updateBar();

            clearInterval(intervalId);
            intervalId = setTimeout(runVirusSimulation, duration);

          } else {
            clearInterval(intervalId);
            intervalId = null;

            // LINEブラウザでない場合のみ音声停止
            if (!isLine) {
                stopJapaneseVoiceLoop();
                if (alarmAudio) {
                  alarmAudio.pause();
                  alarmAudio.currentTime = 0;
                  alarmAudio = null;
                }
            }

            showCustomAlert("すべてのデータを削除しました。");
            localStorage.setItem(localStorageKey, "1");
          }
        };

        // 最初のログ表示をすぐに開始
        intervalId = setTimeout(runVirusSimulation, 100); // 100ms後に最初のログを表示
      }

      function showReliefScreen() {
        virusScreen.classList.remove("visible");
        virusScreen.classList.add("hidden");

        reliefScreen.classList.remove("hidden");
        reliefScreen.style.display = "flex";

        setTimeout(() => {
          reliefScreen.style.display = "none";
          reliefScreen.classList.add("hidden");
          showMainScreen();
          showShareButtons();
          body.classList.remove('virus-active');
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

      // 外部ブラウザで開くための共通URL生成関数
      function createExternalBrowserURL(originalUrl) {
        const encodedUrl = encodeURIComponent(originalUrl);
        // Chromeがインストールされている場合はChromeで開くURLスキームを優先
        // そうでない場合は通常のHTTPS URLを返す (システムがデフォルトブラウザで開くことを期待)
        return `googlechrome://navigate?url=${encodedUrl}`;
      }

      // X(Twitter)共有
      function shareX() {
        const currentURL = location.href;
        const externalURL = createExternalBrowserURL(currentURL);
        const shareText = encodeURIComponent("文化祭まとめサイトをチェック！");

        // Xの共有URLに外部ブラウザURLを組み込む
        const url = `https://twitter.com/intent/tweet?text=${shareText}%0A${encodeURIComponent(externalURL)}`;
        window.open(url, "_blank", "noopener");
      }

      // LINE共有
      function shareLINE() {
        const currentURL = location.href;
        const externalURL = createExternalBrowserURL(currentURL);
        const shareText = encodeURIComponent("文化祭まとめサイトをチェック！");

        // LINEの共有URLに外部ブラウザURLを組み込む
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
      });

      startBtn.addEventListener("click", () => {
        startVirus();
      });

      repeatVirusBtn.addEventListener("click", () => {
        startVirus();
      });

      customAlertOkBtn.addEventListener("click", () => {
        hideCustomAlert();
        showReliefScreen();
      });

      window.showSchedule = showSchedule;
      window.shareX = shareX;
      window.shareLINE = shareLINE;
    })();
  </script>
</body>
</html>
