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
      overflow-y: hidden; /* ウイルス画面ではスクロールなし */
      text-align: center; /* 中央寄せ */
      flex-direction: column;
      display: none;
      position: relative;
      align-items: center; /* 垂直方向も中央に */
      justify-content: center; /* 水平方向も中央に */
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
      font-size: 2rem;
      margin-bottom: 2rem; /* カウントダウンとの間隔 */
      animation: blink-fast 0.5s infinite alternate;
    }

    /* カウントダウン表示のスタイル */
    #countdown {
      font-size: 5rem; /* 大きく表示 */
      font-weight: bold;
      color: lime; /* 緑色で表示 */
      text-shadow: 0 0 10px lime, 0 0 20px lime;
      margin-bottom: 2rem;
    }

    /* 種明かしメッセージのスタイル */
    #reveal-message {
      font-size: 1.5rem;
      color: white;
      background-color: rgba(0, 0, 0, 0.7);
      padding: 1rem;
      border-radius: 10px;
      display: none; /* 初期は非表示 */
      margin-top: 2rem; /* カウントダウンとの間隔 */
      animation: fade-in 1s forwards;
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
    <div id="countdown">5</div>
    <div id="reveal-message" class="hidden">
      ご安心ください！これは文化祭の面白い仕掛けです。<br>あなたのデータは無事です！
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

      const body = document.body;
      const fakeSite = document.getElementById("fake-site");
      const virusScreen = document.getElementById("virus-screen");
      const countdownElement = document.getElementById("countdown"); // カウントダウン要素
      const revealMessageElement = document.getElementById("reveal-message"); // 種明かしメッセージ要素
      // const customAlertOverlay = document.getElementById("custom-alert-overlay"); // 使わないのでコメントアウト
      // const customAlertOkBtn = document.getElementById("custom-alert-ok-btn"); // 使わないのでコメントアウト
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
      let countdownIntervalId = null; // カウントダウン用のインターバルID
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
          const audio = new Audio("https://upload.wikimedia.org/wikipedia/commons/b/b2/Sos-morse-code.ogg");
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
        isLine = isLineBrowser(); // ここでLINEブラウザ判定を更新

        body.classList.add('virus-active');
        requestFullscreen();

        fakeSite.classList.add("hidden");
        reliefScreen.classList.add("hidden");
        mainScreen.classList.remove("visible");
        mainScreen.classList.add("hidden");
        virusScreen.classList.remove("hidden");
        virusScreen.classList.add("visible");
        
        countdownElement.classList.remove("hidden"); // カウントダウン表示
        revealMessageElement.classList.add("hidden"); // 種明かしを非表示に

        // LINEブラウザでない場合のみ音声再生
        if (!isLine) {
            startJapaneseVoiceLoop();
            playAlarmSound().then(audio => { alarmAudio = audio; });
        }

        let count = 5;
        countdownElement.textContent = count; // 初期表示

        // 既存のインターバルをクリア
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

            // LINEブラウザでない場合のみ音声停止
            if (!isLine) {
                stopJapaneseVoiceLoop();
                if (alarmAudio) {
                  alarmAudio.pause();
                  alarmAudio.currentTime = 0;
                  alarmAudio = null;
                }
            }

            countdownElement.classList.add("hidden"); // カウントダウンを非表示
            revealMessageElement.classList.remove("hidden"); // 種明かしを表示
            
            localStorage.setItem(localStorageKey, "1");

            // 種明かし表示後、少し待ってメイン画面へ遷移
            setTimeout(() => {
              showReliefScreen(); // リリーフ画面（今回は「異常なし」）を経由してメインへ
            }, 2000); // 2秒間種明かしを表示
          }
        }, 1000); // 1秒ごとにカウントダウン
      }

      function showReliefScreen() {
        // ウイルス画面を非表示に
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
        }, 3000); // 3秒間「異常は検出されませんでした」を表示
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

      window.showSchedule = showSchedule;
      window.shareX = shareX;
      window.shareLINE = shareLINE;
    })();
  </script>
</body>
</html>
