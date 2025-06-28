<!DOCTYPE html>
<html lang="ja">
<head>
  <meta charset="UTF-8" />
  <title>文化祭スケジュール</title>
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <style>
    body {
      margin: 0;
      font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif; /* より現代的なフォント */
      background: #f4f4f4; /* 明るい背景 */
      color: #333; /* ダークなテキスト */
      text-align: center;
      min-height: 100vh;
      display: flex;
      flex-direction: column;
      justify-content: center;
      align-items: center;
      user-select: none;
      overflow-y: auto;
      transition: background-color 0.3s ease-in-out;
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

    /* 初期は非表示だが、display: flex/block で上書きされる */
    #fake-site, #relief-screen, #main, #virus-screen {
      /* display: none; */
    }

    #fake-site {
      display: flex;
      flex-direction: column;
      align-items: center;
      background-color: #fff; /* 白い背景 */
      padding: 3rem; /* 大きめのパディング */
      border-radius: 12px; /* 角を丸く */
      box-shadow: 0 4px 12px rgba(0, 0, 0, 0.1); /* 控えめなシャドウ */
    }

    #fake-site h1 {
      font-size: 3.5rem; /* 少し大きく */
      margin-bottom: 2.5rem; /* 少し空ける */
      text-shadow: 1px 1px 0 #eee, 2px 2px 4px rgba(0, 0, 0, 0.1); /* 控えめなテキストシャドウ */
      line-height: 1.3;
      color: #555; /* 少し落ち着いた色 */
      font-weight: 700; /* 太字 */
    }

    #fake-site h1 span {
      display: block;
    }

    #fake-site h1 span:first-child {
      font-size: 2.2rem; /* 少し小さく */
      color: #777; /* さらに落ち着いた色 */
      font-weight: 500; /* 少し細く */
    }

    #fake-site button {
      background-color: #007bff; /* 青系のボタン */
      border: none;
      padding: 1.5rem 3.5rem; /* 少し大きく */
      font-size: 1.8rem; /* 少し大きく */
      font-weight: 600; /* 少し太く */
      border-radius: 8px; /* 角を丸く */
      color: #fff;
      cursor: pointer;
      box-shadow: 0 4px 8px rgba(0, 0, 0, 0.15); /* 控えめなシャドウ */
      transition: background-color 0.2s ease-in-out, transform 0.1s ease-in-out; /* ホバーエフェクト */
      margin-top: 1.5rem; /* 少し空ける */
    }

    #fake-site button:hover {
      background-color: #0056b3; /* ホバー時の色 */
      transform: translateY(-2px); /* 少し持ち上げる */
    }

    #virus-screen {
      position: fixed;
      top: 0;
      left: 0;
      right: 0;
      bottom: 0;
      width: 100vw;
      height: 100vh;
      background-color: #000; /* より深い黒 */
      color: #ff3300; /* より強調された赤 */
      z-index: 999999;
      font-family: 'Courier New', Courier, monospace; /* より機械的なフォント */
      font-size: 1.2rem; /* 少し大きく */
      overflow: hidden;
      text-align: center;
      display: flex;
      flex-direction: column;
      align-items: center;
      justify-content: center;
      box-sizing: border-box;
      pointer-events: none; /* 下の要素をクリックできるように */
      animation: noise 0.1s infinite; /* ノイズアニメーションを適用 */
    }

    /* ノイズ効果のための ::before 要素は、背景のパターンとして機能 */
    #virus-screen::before {
      content: '';
      position: absolute;
      top: 0;
      left: 0;
      width: 100%;
      height: 100%;
      background-color: rgba(255, 0, 0, 0.2); /* 赤のオーバーレイを少し強く */
      background-image: url('data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAAQAAAAECAYAAACp8da+AAAAAXNSR0IArs4c6QAAABJJREFUGFdjYGBgYGBgYGAAAgABAAyW/71mAAAAAElFTkSuQmCC'); /* 細かいパターン */
      background-size: 2px 2px;
      opacity: 0.7; /* 少し強く */
      pointer-events: none;
      z-index: -1;
    }

    #virus-screen.visible {
      display: flex;
    }

    #virus-screen h2 {
      font-size: 2.5rem; /* 少し大きく */
      margin-bottom: 1.5rem; /* 少し詰める */
      animation: blink-fast 0.3s infinite alternate, glitch 0.5s infinite alternate; /* グリッチアニメーション追加 */
      width: 90%; /* 少し幅を狭める */
      text-align: center;
      box-sizing: border-box;
    }

    /* カウントダウン表示のスタイル */
    #countdown { /* このIDを持つ要素はJSで生成されます */
      font-size: 6rem; /* さらに大きく */
      font-weight: bold;
      color: #00ff00; /* より鮮やかな緑 */
      text-shadow: 0 0 5px #00ff00, 0 0 15px #00ff00; /* 強めのシャドウ */
      margin-bottom: 1rem; /* さらに詰める */
      width: 90%; /* 少し幅を狭める */
      text-align: center;
      box-sizing: border-box;
      animation: glitch 0.3s infinite alternate; /* グリッチアニメーション追加 */
    }

    /* 種明かしメッセージのスタイル */
    #reveal-message { /* このIDを持つ要素はJSで生成されます */
      font-size: 1.3rem; /* 少し小さく */
      color: #f0f0f0; /* 少し明るいグレー */
      background-color: rgba(0, 0, 0, 0.8); /* より暗く */
      padding: 1rem;
      border-radius: 5px; /* 少し角を丸く */
      display: none;
      margin-top: 1.5rem; /* 少し詰める */
      animation: fade-in 0.5s forwards; /* 少し速く */
      max-width: 80%; /* さらに幅を狭める */
      box-sizing: border-box;
      text-align: center;
    }

    /* アニメーションの定義 */
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

    @keyframes noise { /* 画面全体にノイズをかけるアニメーション (CSSで制御するように変更) */
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

    /* リリーフ画面 */
    #relief-screen {
      background: linear-gradient(45deg, #e0f7fa, #b2ebf2); /* 水色系のグラデーション */
      color: #006064; /* 濃いシアン */
      height: 100vh;
      width: 100vw;
      position: fixed;
      top: 0;
      left: 0;
      z-index: 9998;
      justify-content: center;
      align-items: center;
      flex-direction: column;
      font-size: 1.8rem; /* 少し大きく */
      display: none;
      user-select: text;
      animation: fade-in 0.5s ease-in-out; /* フェードイン */
      font-weight: 500;
    }

    #relief-screen p {
        margin: 0.5rem 0;
        text-shadow: 1px 1px 2px rgba(255,255,255,0.5);
    }

    /* メインコンテンツ */
    #main {
      padding: 3rem; /* 大きめのパディング */
      background: #fff; /* 白い背景 */
      min-height: auto;
      color: #333; /* ダークなテキスト */
      user-select: none;
      display: none;
      border-radius: 12px; /* 角を丸く */
      box-shadow: 0 4px 12px rgba(0, 0, 0, 0.1); /* 控えめなシャドウ */
      max-width: 90%; /* 幅を制限 */
      margin-top: 2rem; /* 少し空ける */
    }

    #main.visible {
      display: block;
      animation: fade-in 0.3s ease-in-out; /* フェードインアニメーション */
    }

    #main h1 {
      font-size: 3rem; /* 少し大きく */
      margin-bottom: 2rem; /* 少し空ける */
      text-shadow: 1px 1px 0 #eee, 2px 2px 4px rgba(0, 0, 0, 0.1); /* 控えめなテキストシャドウ */
      color: #555; /* 少し落ち着いた色 */
      font-weight: 700; /* 太字 */
    }

    #main button {
      padding: 1.2rem 2.5rem; /* 少し大きく */
      font-size: 1.4rem; /* 少し大きく */
      margin: 0.8rem; /* 少し調整 */
      cursor: pointer;
      background-color: #28a745; /* 緑系のボタン */
      border: none;
      border-radius: 8px; /* 角を丸く */
      color: #fff;
      font-weight: 600; /* 少し太く */
      box-shadow: 0 2px 6px rgba(0, 0, 0, 0.1); /* 控えめなシャドウ */
      transition: background-color 0.2s ease-in-out, transform 0.1s ease-in-out; /* ホバーエフェクト */
    }

    #main button:hover {
      background-color: #1e7e34; /* ホバー時の色 */
      transform: translateY(-1px); /* 少し持ち上げる */
    }

    #repeat-virus-btn {
      background-color: #dc3545; /* 赤系のボタン */
      margin-bottom: 1.5rem; /* 少し空ける */
      box-shadow: 0 2px 6px rgba(0, 0, 0, 0.1); /* 控えめなシャドウ */
    }

    #repeat-virus-btn:hover {
      background-color: #c82333; /* ホバー時の色 */
    }

    #schedule img {
      max-width: 95%; /* 少し広げる */
      max-height: 70vh; /* 少し高く */
      border: 2px solid #ddd; /* 薄いボーダー */
      border-radius: 8px; /* 角を丸く */
      box-shadow: 0 2px 8px rgba(0, 0, 0, 0.1); /* 控えめなシャドウ */
      margin-top: 1.5rem; /* 少し空ける */
    }

    #share-buttons {
      margin-top: 2.5rem; /* 少し空ける */
      display: none;
      justify-content: center;
      gap: 1.5rem; /* 少し広げる */
    }

    #share-buttons.visible {
      display: flex;
    }

    .share-btn {
      cursor: pointer;
      padding: 1rem 2rem; /* 少し大きく */
      border-radius: 10px; /* 角を丸く */
      font-weight: 600; /* 少し太く */
      font-size: 1.2rem; /* 少し大きく */
      color: white;
      box-shadow: 0 2px 6px rgba(0, 0, 0, 0.1); /* 控えめなシャドウ */
      display: flex;
      align-items: center;
      gap: 0.8rem; /* 少し広げる */
      user-select: none;
      transition: transform 0.1s ease-in-out; /* ホバーエフェクト */
    }

    .share-btn:hover {
      transform: translateY(-1px); /* 少し持ち上げる */
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
      font-size: 1.4rem; /* 少し大きく */
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

      // 新しく生成される要素への参照（グローバルスコープで保持）
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
        if (isLine) return; // LINEブラウザの場合は音声再生しない

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
          synth.cancel(); // 既存の音声を停止
          synth.speak(utterance);
        } catch (e) {
          console.error("Failed to speak utterance:", e);
          voiceLoopRunning = false;
        }
      }

      function stopJapaneseVoiceLoop() {
        if (isLine) return; // LINEブラウザの場合は音声再生しないので停止も不要

        voiceLoopRunning = false;
        try {
          synth.cancel();
        } catch (e) {
            console.warn("Failed to cancel speech synthesis:", e);
        }
      }

      async function playAlarmSound() {
        if (isLine) return null; // LINEブラウザの場合は音声再生しない

        try {
          // 音声ファイルのURLが正しいことを確認してください
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
        requestFullscreen();

        fakeSite.classList.add("hidden");
        reliefScreen.classList.add("hidden");
        mainScreen.classList.remove("visible");
        mainScreen.classList.add("hidden");
        
        // ウイルス画面を表示
        virusScreen.classList.remove("hidden");
        virusScreen.classList.add("visible");

        // virusScreen の既存の子要素を全てクリア (これにより、前のノイズ要素などが確実に削除される)
        while (virusScreen.firstChild) {
          virusScreen.removeChild(virusScreen.firstChild);
        }

        // <h2>要素を作成し追加
        virusTitle = document.createElement("h2");
        virusTitle.textContent = "⚠ ウイルスに感染しました";
        virusScreen.appendChild(virusTitle);

        // カウントダウン要素を作成し追加
        countdownElement = document.createElement("div");
        countdownElement.id = "countdown";
        virusScreen.appendChild(countdownElement);

        // 種明かしメッセージ要素を作成し追加
        revealMessageElement = document.createElement("div");
        revealMessageElement.id = "reveal-message";
        revealMessageElement.classList.add("hidden");
        revealMessageElement.innerHTML = "ご安心ください！これは文化祭の面白い仕掛けです。<br>あなたのデータは無事です！";
        virusScreen.appendChild(revealMessageElement);
        
        // ノイズ効果の div 要素を動的に生成し、#virus-screen の子要素として追加 (CSSで制御)
        // CSSの noise アニメーションが #virus-screen 自体に適用されているため、
        // ここで個別のノイズ要素をJavaScriptで生成する必要はなくなりました。
        // ただし、以前のコードでランダムなノイズ要素を配置していた場合の名残として
        // もし必要なら以下のようなコードを再導入できますが、
        // 現状のCSSアニメーションで十分な場合は不要です。
        /*
        for (let i = 0; i < 50; i++) { // ノイズの数を減らすなど調整可能
            const noiseDot = document.createElement('div');
            noiseDot.style.position = 'absolute';
            noiseDot.style.width = '1px';
            noiseDot.style.height = '1px';
            noiseDot.style.backgroundColor = 'rgba(255, 255, 255, 0.8)'; // 白い点
            noiseDot.style.top = Math.random() * 100 + '%';
            noiseDot.style.left = Math.random() * 100 + '%';
            noiseDot.style.opacity = Math.random();
            noiseDot.classList.add('noise-dot'); // CSSでさらにアニメーションを適用するため
            virusScreen.appendChild(noiseDot);
        }
        */

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
            }, 2000); // 2秒間種明かしを表示
          }
        }, 1000); // 1秒ごとにカウントダウン
      }

      function showReliefScreen() {
        virusScreen.classList.remove("visible");
        virusScreen.classList.add("hidden");

        // virusScreen の子要素を削除してクリーンアップ
        while (virusScreen.firstChild) {
          virusScreen.removeChild(virusScreen.firstChild);
        }

        reliefScreen.classList.remove("hidden");
        reliefScreen.style.display = "flex"; // flexboxで中央表示

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
        // 画像ファイルのパスが正しいことを確認してください。
        // 例: 'junbi.png', 'day1.png', 'day2.png', 'kataduke.png' がHTMLファイルと同じディレクトリにあることを想定。
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
          fakeSite.style.display = "flex"; // 初期表示
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
