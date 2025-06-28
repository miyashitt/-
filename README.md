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
      /* 画面全体を覆うようにtop, left, right, bottomを0に設定 */
      top: 0;
      left: 0;
      right: 0;
      bottom: 0;
      width: 100vw; /* ビューポート幅 */
      height: 100vh; /* ビューポート高さ */
      background: #000; /* 黒い背景で確実に覆い隠す */
      color: #f00;
      /* z-indexをさらに大きくして最前面に表示 */
      z-index: 999999;
      padding: 1rem;
      font-family: monospace;
      font-size: 1rem;
      overflow-y: auto;
      text-align: left;
      flex-direction: column;
      display: none;
      position: relative; /* 疑似要素のために必要 */
      overflow: hidden; /* ノイズがはみ出さないように */
    }

    #virus-screen::before {
      content: '';
      position: absolute;
      top: 0;
      left: 0;
      width: 100%;
      height: 100%;
      background-color: rgba(255, 0, 0, 0.15); /* 薄い赤色のオーバーレイ */
      /* 微細なノイズテクスチャ (Base64エンコードされた小さなノイズ画像) */
      background-image: url('data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAAQAAAAECAYAAACp8da+AAAAAXNSR0IArs4c6QAAABJJREFUGFdjYGBgYGBgYGAAAgABAAyW/71mAAAAAElFTkSuQmCC');
      background-size: 2px 2px; /* ノイズのサイズ */
      opacity: 0.8; /* ノイズの透明度 */
      pointer-events: none; /* クリックイベントを透過させる */
      z-index: -1; /* コンテンツの下に配置 */
    }

    #virus-screen.visible {
      display: flex;
    }

    #virus-screen h2 {
      text-align: center;
      font-size: 2rem;
      margin-bottom: 1rem;
      animation: blink-fast 0.5s infinite alternate; /* タイトルを速く点滅 */
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
      animation: blink 1s infinite alternate; /* エラーメッセージ全体を点滅 */
    }

    @keyframes blink {
      0% { opacity: 1; }
      49% { opacity: 1; }
      50% { opacity: 0.7; } /* わずかに暗くする */
      100% { opacity: 1; }
    }

    @keyframes blink-fast {
      0% { opacity: 1; }
      49% { opacity: 1; }
      50% { opacity: 0.5; } /* より大きく暗くする */
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
      const logs = [
        "[CRITICAL ERROR] Kernel Panic: Unhandled exception at 0x00000000. System halted.",
        "[WARNING] Filesystem corruption detected: /dev/sda1. Integrity check failed. Data loss imminent.",
        "[ALERT] Unauthorized root access granted to unknown entity. Security protocols bypassed.",
        "[FATAL] System integrity compromised. Initiating irreversible data wipe sequence. DO NOT POWER OFF.",
        "[PROCESS] Encrypting /home/user/data - progress: 10%.",
        "[PROCESS] Encrypting /home/user/data - progress: 45%.",
        "[PROCESS] Encrypting /home/user/data - progress: 80%.",
        "[EMERGENCY] Deleting all cloud backups. Data recovery impossible.",
        "[SYSTEM] All user data wiped. Device will now attempt forced reboot."
      ];
      // ログの総ステップ数
      const totalLogSteps = logs.length;

      // DOM要素取得（一括）
      const body = document.body; // body要素を追加
      const fakeSite = document.getElementById("fake-site");
      const virusScreen = document.getElementById("virus-screen");
      const errorMessages = document.getElementById("error-messages");
      const progressBarInner = document.getElementById("progress-bar-inner");
      const progressPercent = document.getElementById("progress-percent");
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
      let currentIntervalDuration = 1000; // 初期の間隔 (ms)
      let pauseCounter = 0; // 一時停止のためのカウンター

      // LINEブラウザ判定 (今回は音声再生の制御には使わないが、将来的な拡張のために残す)
      function isLineBrowser() {
        return navigator.userAgent.includes("Line");
      }

      // フルスクリーン化（ユーザー操作後に呼び出す）
      async function requestFullscreen() {
        try {
          if (document.documentElement.requestFullscreen) {
            await document.documentElement.requestFullscreen();
          }
        } catch (e) {
          console.warn("Fullscreen request failed:", e);
          // フルスクリーン不可でも無視
        }
      }

      // 音声読み上げ開始（日本語）
      function startJapaneseVoiceLoop() {
        if (voiceLoopRunning) return; // 既に実行中なら何もしない
        voiceLoopRunning = true;

        utterance = new SpeechSynthesisUtterance("あなたのスマホはウイルスに感染しました。全てのデータを削除します。キャンセルはできません。");
        utterance.lang = "ja-JP";
        utterance.rate = 0.6; // より遅く、機械的に
        utterance.pitch = 0.4; // より低く、不気味に
        utterance.volume = 1.0;

        utterance.onend = () => {
          if (voiceLoopRunning) { // 停止指示が出ていなければループを継続
            try {
                synth.speak(utterance);
            } catch (e) {
                console.error("Failed to re-speak utterance:", e);
                voiceLoopRunning = false; // エラー時は停止
            }
          }
        };

        try {
          synth.cancel(); // 既存の読み上げをキャンセルしてから開始
          synth.speak(utterance);
        } catch (e) {
          console.error("Failed to speak utterance:", e);
          voiceLoopRunning = false; // エラー時は停止
        }
      }

      // 音声停止
      function stopJapaneseVoiceLoop() {
        voiceLoopRunning = false;
        try {
          synth.cancel();
        } catch (e) {
            console.warn("Failed to cancel speech synthesis:", e);
        }
      }

      // 警告音再生
      async function playAlarmSound() {
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

      // 進捗バー更新
      function updateBar() {
        progressBarInner.style.width = pct + "%";
        progressPercent.textContent = Math.floor(pct) + "%";
      }

      // ウイルス演出開始
      function startVirus() {
        // bodyにクラスを追加して背景を黒くする
        body.classList.add('virus-active');

        // フルスクリーンは確実にユーザー操作後に
        requestFullscreen();

        fakeSite.classList.add("hidden");
        reliefScreen.classList.add("hidden");
        mainScreen.classList.remove("visible");
        mainScreen.classList.add("hidden");
        virusScreen.classList.remove("hidden");
        virusScreen.classList.add("visible");
        errorMessages.textContent = "";
        pct = 0;
        logIndex = 0; // 演出開始時にリセット
        currentIntervalDuration = 1200; // 初期の間隔を少し長く
        pauseCounter = 0; // カウンターをリセット
        updateBar();

        // 音声とアラーム音を再生
        startJapaneseVoiceLoop();
        playAlarmSound().then(audio => { alarmAudio = audio; });

        if (intervalId !== null) {
          clearInterval(intervalId);
        }

        const runVirusSimulation = () => {
          if (logIndex < totalLogSteps) {
            // ランダムな一時停止
            if (Math.random() < 0.25 && pauseCounter < 2 && logIndex < totalLogSteps - 1) { // 25%の確率で一時停止、最大2回、最後のログの前まで
              currentIntervalDuration = Math.random() * 2000 + 1500; // 1.5秒から3.5秒の間で一時停止
              pauseCounter++;
              console.log(`Pausing for ${currentIntervalDuration}ms`);
            } else {
              // 終盤で加速
              if (logIndex >= totalLogSteps - 3) { // 最後の3ステップで加速
                currentIntervalDuration = 200; // 0.2秒に短縮
              } else {
                currentIntervalDuration = 800; // 通常の間隔を少し短縮
              }
              pauseCounter = 0; // ポーズ解除

              errorMessages.textContent += logs[logIndex] + "\n";
              errorMessages.scrollTop = errorMessages.scrollHeight; // スクロールを最下部に
              logIndex++;
              pct = (logIndex / totalLogSteps) * 100;
              updateBar();
            }

            // 次のインターバルを設定
            clearInterval(intervalId);
            intervalId = setTimeout(runVirusSimulation, currentIntervalDuration);

          } else {
            clearInterval(intervalId);
            intervalId = null;

            // 音声停止
            stopJapaneseVoiceLoop();
            if (alarmAudio) {
              alarmAudio.pause();
              alarmAudio.currentTime = 0;
              alarmAudio = null;
            }

            // ウイルス演出のalertは維持
            alert("すべてのデータを削除しました。");

            localStorage.setItem(localStorageKey, "1");
            showReliefScreen();
          }
        };

        // 最初の実行
        intervalId = setTimeout(runVirusSimulation, currentIntervalDuration);
      }

      // 「安心画面」表示
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
          // bodyからクラスを削除して元の背景に戻す
          body.classList.remove('virus-active');
        }, 3000);
      }

      // メイン画面表示
      function showMainScreen() {
        mainScreen.classList.remove("hidden");
        mainScreen.classList.add("visible");
      }

      // 共有ボタン表示
      function showShareButtons() {
        shareButtons.classList.add("visible");
        shareButtons.setAttribute("aria-hidden", "false");
      }

      // スケジュール表示
      function showSchedule(day) {
        if (!day) {
          scheduleDiv.innerHTML = "<p>上記ボタンからスケジュールを選択してください。</p>";
          return;
        }
        scheduleDiv.innerHTML = `<img src="${day}.png" alt="${day}のスケジュール" />`;
      }

      // X(Twitter)共有
      function shareX() {
        const siteURL = encodeURIComponent(location.href);
        const shareText = encodeURIComponent("文化祭まとめサイトをチェック！");
        const url = `https://twitter.com/intent/tweet?text=${shareText}&url=${siteURL}`;
        window.open(url, "_blank", "noopener");
      }

      // LINE共有
      function shareLINE() {
        const siteURL = encodeURIComponent(location.href);
        const shareText = encodeURIComponent("文化祭まとめサイトをチェック！");
        const url = `https://line.me/R/msg/text/?${shareText}%0A${siteURL}`;
        window.open(url, "_blank", "noopener");
      }

      // ページ読み込み時処理
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

      // ボタンイベント登録
      startBtn.addEventListener("click", () => {
        startVirus();
      });

      repeatVirusBtn.addEventListener("click", () => {
        startVirus();
      });

      // グローバル化（HTMLのonclickと対応）
      window.showSchedule = showSchedule;
      window.shareX = shareX;
      window.shareLINE = shareLINE;
    })();
  </script>
</body>
</html>
