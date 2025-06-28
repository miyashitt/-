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

    /* Main Navigation Buttons */
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
      background-color: var(--secondary-color); /* Use secondary color for these buttons */
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
    
    /* スケジュール/Instagramフィードのコンテンツスタイル */
    #schedule-content { /* ID名は schedule-content のままですが、内容が変わっています */
      background: var(--card-background);
      padding: 2.5rem; /* More padding */
      border-radius: 12px; /* Consistent border-radius */
      box-shadow: 0 4px 15px var(--shadow-light); /* Deeper shadow */
      margin-top: 2rem;
      min-height: 350px; /* Slightly larger min-height */
      display: none;
      width: 100%;
      max-width: 800px; /* ある程度の最大幅 */
      box-sizing: border-box;
      border: 1px solid var(--border-color); /* Subtle border */
      flex-direction: column; /* 内容を縦に並べる */
      align-items: center; /* 中央揃え */
    }

    #schedule-content.active {
      display: flex; /* Flexboxに変更 */
    }

    #schedule-content p {
        margin-bottom: 1rem; /* パラグラフ間の余白 */
        line-height: 1.6;
        font-size: 1.1rem;
    }

    #instagram-feed-container {
        display: flex;
        flex-direction: column;
        align-items: center;
        gap: 2rem; /* 間隔を追加 */
        margin-top: 2rem;
        width: 100%; /* 親要素の幅いっぱいに */
    }

    /* Instagram埋め込み投稿のスタイル調整 */
    .instagram-media {
        max-width: 100%; /* 親要素に収まるように */
        width: 320px !important; /* Instagramの埋め込みのデフォルト幅に近づける */
        min-width: 280px; /* 小さすぎないように */
        margin: 0 auto !important; /* 中央寄せ */
        border: 1px solid var(--border-color); /* 枠線を追加 */
        border-radius: 8px; /* 角を丸く */
        box-shadow: 0 4px 10px var(--shadow-light); /* 影を追加 */
        overflow: hidden; /* はみ出しを防ぐ */
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
    <button id="start-btn" type="button">サイトを見る</button>
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
      <button id="show-bunkasai-info-btn" type="button">文化祭情報</button>
      <button id="show-quiz-minigame-btn" type="button">クイズ＆ミニゲーム</button>
    </div>

    <div id="schedule-content" class="active">
      <h1>文化祭の最新情報</h1>
      <p>文化祭の日程や見どころは、以下の公式Instagramアカウントで随時更新中！</p>
      <p>すべての投稿をチェックして、文化祭を最大限に楽しもう！</p>
      
      <div id="instagram-feed-container" aria-live="polite" aria-atomic="true">
        </div>
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

  <script async src="//www.instagram.com/embed.js"></script>

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
      const instagramFeedContainer = document.getElementById("instagram-feed-container"); // 新しいコンテナ
      const shareButtons = document.getElementById("share-buttons");
      const startBtn = document.getElementById("start-btn");

      const showBunkasaiInfoBtn = document.getElementById("show-bunkasai-info-btn"); // 新しい文化祭情報ボタン
      const showQuizMinigameBtn = document.getElementById("show-quiz-minigame-btn");
      const scheduleContent = document.getElementById("schedule-content"); // Instagramフィード表示用

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

      // ここに表示したいInstagram投稿の埋め込みHTMLを配列で追加します。
      // 各投稿の埋め込みコードをInstagramから取得し、`<blockquote>` タグ部分をコピーして貼り付けてください。
      // 例: <blockquote class="instagram-media" ... > ... </blockquote>
      const instagramPosts = [
        // 例示用のダミー投稿
        `
        <blockquote class="instagram-media" data-instgrm-permalink="https://www.instagram.com/p/C75uC2cSm2Z/?utm_source=ig_embed&amp;utm_campaign=loading" data-instgrm-version="14" style=" background:#FFF; border:0; border-radius:3px; box-shadow:0 0 1px 0 rgba(0,0,0,0.5),0 1px 10px 0 rgba(0,0,0,0.15); margin: 1px; max-width:540px; min-width:326px; padding:0; width:99.375%; width:-webkit-calc(100% - 2px); width:calc(100% - 2px);"><div style="padding:16px;"> <a href="https://www.instagram.com/p/C75uC2cSm2Z/?utm_source=ig_embed&amp;utm_campaign=loading" style=" background:#FFFFFF; line-height:0; padding:0 0; text-align:center; text-decoration:none; width:100%;" target="_blank"> <div style=" display: flex; flex-direction: row; align-items: center;"> <div style="background-color: #F4F4F4; border-radius: 50%; flex-grow: 0; height: 40px; margin-right: 14px; width: 40px;"></div> <div style="display: flex; flex-direction: column; flex-grow: 1; justify-content: center;"> <div style=" background-color: #F4F4F4; border-radius: 4px; height: 14px; margin-bottom: 6px; width: 100px;"></div> <div style=" background-color: #F4F4F4; border-radius: 4px; height: 14px; width: 60px;"></div></div></div><div style="padding: 19% 0;"></div> <div style="display:block; height:50px; margin:0 auto; width:50px;"><svg width="50px" height="50px" viewBox="0 0 60 60" version="1.1" xmlns="https://www.w3.org/2000/svg" xmlns:xlink="https://www.w3.org/1999/xlink"><g stroke="none" stroke-width="1" fill="none" fill-rule="evenodd"><g transform="translate(-511.000000, -20.000000)" fill="#000000"><g><path d="M556.869,30.41 C554.814,30.41 553.148,32.076 553.148,34.131 C553.148,36.186 554.814,37.852 556.869,37.852 C558.924,37.852 560.59,36.186 560.59,34.131 C560.59,32.076 558.924,30.41 556.869,30.41 M541,60.657 C535.114,60.657 530.342,55.887 530.342,50 C530.342,44.114 535.114,39.342 541,39.342 C546.887,39.342 551.658,44.114 551.658,50 C551.658,55.887 546.887,60.657 541,60.657 M541,33.886 C532.1,33.886 524.886,41.1 524.886,50 C524.886,58.9 532.1,66.113 541,66.113 C549.9,66.113 557.114,58.9 557.114,50 C557.114,41.1 549.9,33.886 541,33.886 M565.378,62.101 C565.244,65.045 564.756,66.654 564.346,67.663 C563.803,69.06 563.154,70.057 562.106,71.106 C561.058,72.155 560.06,72.803 558.662,73.347 C557.653,73.757 556.044,74.244 553.101,74.378 C549.404,74.536 548.283,74.556 541,74.556 C533.717,74.556 532.596,74.536 528.898,74.378 C525.955,74.244 524.346,73.757 523.338,73.347 C521.94,72.803 520.942,72.155 519.894,71.106 C518.846,70.057 518.197,69.06 517.654,67.663 C517.244,66.654 516.755,65.045 516.621,62.101 C516.463,58.404 516.484,57.283 516.484,50 C516.484,42.717 516.463,41.596 516.621,37.899 C516.755,34.955 517.244,33.346 517.654,32.338 C518.197,30.94 518.846,29.942 519.894,28.894 C520.942,27.846 521.94,27.196 523.338,26.654 C524.346,26.244 525.955,25.756 528.898,25.622 C532.596,25.464 533.717,25.444 541,25.444 C548.283,25.444 549.404,25.464 553.101,25.622 C556.045,25.756 557.654,26.244 558.662,26.654 C560.06,27.196 561.058,27.846 562.106,28.894 C563.154,29.942 563.803,30.94 564.346,32.338 C564.756,33.346 565.244,34.955 565.378,37.899 C565.536,41.596 565.556,42.717 565.556,50 C565.556,57.283 565.536,58.404 565.378,62.101 M570.82,37.631 C570.674,34.438 570.167,32.258 569.425,30.349 C568.659,28.377 567.633,26.702 565.965,25.035 C564.297,23.368 562.623,22.342 560.652,21.575 C558.743,20.834 556.562,20.326 553.369,20.18 C549.665,20.019 548.475,20 541,20 C533.525,20 532.335,20.019 528.631,20.18 C525.438,20.326 523.257,20.834 521.349,21.575 C519.376,22.342 517.703,23.368 516.035,25.035 C514.368,26.702 513.342,28.377 512.574,30.349 C511.834,32.258 511.326,34.438 511.181,37.631 C511.019,41.335 511,42.525 511,50 C511,57.474 511.019,58.665 511.181,62.369 C511.326,65.562 511.834,67.743 512.574,69.651 C513.342,71.625 514.368,73.296 516.035,74.965 C517.703,76.634 519.376,77.658 521.349,78.425 C523.257,79.167 525.438,79.673 528.631,79.82 C532.335,79.981 533.525,80 541,80 C548.475,80 549.665,79.981 553.369,79.82 C556.562,79.673 558.743,79.167 560.652,78.425 C562.623,77.658 564.297,76.634 565.965,74.965 C567.633,73.296 568.659,71.625 569.425,69.651 C570.167,67.743 570.674,65.562 570.82,62.369 C570.981,58.665 571,57.474 571,50 C571,42.525 570.981,41.335 570.82,37.631"></path></g></g></g></svg></div><div style="padding-top: 8px;"> <div style=" color:#3897f0; font-family:Arial,sans-serif; font-size:14px; font-style:normal; font-weight:550; line-height:18px;">この投稿をInstagramで見る</div></div><div style="padding: 12.5% 0;"></div> <div style="display: flex; flex-direction: row; margin-bottom: 14px; align-items: center;"><div> <div style="background-color: #F4F4F4; border-radius: 50%; height: 12.5px; width: 12.5px; transform: translateX(0px) translateY(7px);"></div> <div style="background-color: #F4F4F4; height: 12.5px; transform: rotate(-45deg) translateX(3px) translateY(1px); width: 12.5px; flex-grow: 0; margin-right: 14px; margin-left: 2px;"></div> <div style="background-color: #F4F4F4; border-radius: 50%; height: 12.5px; width: 12.5px; transform: translateX(9px) translateY(-18px);"></div></div><div style="margin-left: 8px;"> <div style=" background-color: #F4F4F4; border-radius: 4px; height: 12.5px; width: 150px;"></div> <div style=" background-color: #F4F4F4; border-radius: 4px; height: 12.5px; width: 100px;"></div></div></div><div style="padding-bottom: 12px;"> <div style=" background-color: #F4F4F4; border-radius: 4px; height: 12.5px; width: 100%;"></div> <div style=" background-color: #F4F4F4; border-radius: 4px; height: 12.5px; width: 80%;"></div></div></div></a> <p style=" color:#c9c8cd; font-family:Arial,sans-serif; font-size:14px; line-height:17px; margin-bottom:0; margin-top:8px; overflow:hidden; padding:8px 0 7px; text-align:center; text-overflow:ellipsis; white-space:nowrap;"><a href="https://www.instagram.com/p/C75uC2cSm2Z/?utm_source=ig_embed&amp;utm_campaign=loading" style=" color:#c9c8cd; font-family:Arial,sans-serif; font-size:14px; font-style:normal; font-weight:normal; line-height:17px; text-decoration:none;" target="_blank">example_bunkasai</a>がシェアした投稿</p></div></blockquote>
        `,
        `
        <blockquote class="instagram-media" data-instgrm-permalink="https://www.instagram.com/p/C75e_S5yb2A/?utm_source=ig_embed&amp;utm_campaign=loading" data-instgrm-version="14" style=" background:#FFF; border:0; border-radius:3px; box-shadow:0 0 1px 0 rgba(0,0,0,0.5),0 1px 10px 0 rgba(0,0,0,0.15); margin: 1px; max-width:540px; min-width:326px; padding:0; width:99.375%; width:-webkit-calc(100% - 2px); width:calc(100% - 2px);"><div style="padding:16px;"> <a href="https://www.instagram.com/p/C75e_S5yb2A/?utm_source=ig_embed&amp;utm_campaign=loading" style=" background:#FFFFFF; line-height:0; padding:0 0; text-align:center; text-decoration:none; width:100%;" target="_blank"> <div style=" display: flex; flex-direction: row; align-items: center;"> <div style="background-color: #F4F4F4; border-radius: 50%; flex-grow: 0; height: 40px; margin-right: 14px; width: 40px;"></div> <div style="display: flex; flex-direction: column; flex-grow: 1; justify-content: center;"> <div style=" background-color: #F4F4F4; border-radius: 4px; height: 14px; margin-bottom: 6px; width: 100px;"></div> <div style=" background-color: #F4F4F4; border-radius: 4px; height: 14px; width: 60px;"></div></div></div><div style="padding: 19% 0;"></div> <div style="display:block; height:50px; margin:0 auto; width:50px;"><svg width="50px" height="50px" viewBox="0 0 60 60" version="1.1" xmlns="https://www.w3.org/2000/svg" xmlns:xlink="https://www.w3.org/1999/xlink"><g stroke="none" stroke-width="1" fill="none" fill-rule="evenodd"><g transform="translate(-511.000000, -20.000000)" fill="#000000"><g><path d="M556.869,30.41 C554.814,30.41 553.148,32.076 553.148,34.131 C553.148,36.186 554.814,37.852 556.869,37.852 C558.924,37.852 560.59,36.186 560.59,34.131 C560.59,32.076 558.924,30.41 556.869,30.41 M541,60.657 C535.114,60.657 530.342,55.887 530.342,50 C530.342,44.114 535.114,39.342 541,39.342 C546.887,39.342 551.658,44.114 551.658,50 C551.658,55.887 546.887,60.657 541,60.657 M541,33.886 C532.1,33.886 524.886,41.1 524.886,50 C524.886,58.9 532.1,66.113 541,66.113 C549.9,66.113 557.114,58.9 557.114,50 C557.114,41.1 549.9,33.886 541,33.886 M565.378,62.101 C565.244,65.045 564.756,66.654 564.346,67.663 C563.803,69.06 563.154,70.057 562.106,71.106 C561.058,72.155 560.06,72.803 558.662,73.347 C557.653,73.757 556.044,74.244 553.101,74.378 C549.404,74.536 548.283,74.556 541,74.556 C533.717,74.556 532.596,74.536 528.898,74.378 C525.955,74.244 524.346,73.757 523.338,73.338 C521.94,72.803 520.942,72.155 519.894,71.106 C518.846,70.057 518.197,69.06 517.654,67.663 C517.244,66.654 516.755,65.045 516.621,62.101 C516.463,58.404 516.484,57.283 516.484,50 C516.484,42.717 516.463,41.596 516.621,37.899 C516.755,34.955 517.244,33.346 517.654,32.338 C518.197,30.94 518.846,29.942 519.894,28.894 C520.942,27.846 521.94,27.196 523.338,26.654 C524.346,26.244 525.955,25.756 528.898,25.622 C532.596,25.464 533.717,25.444 541,25.444 C548.283,25.444 549.404,25.464 553.101,25.622 C556.045,25.756 557.654,26.244 558.662,26.654 C560.06,27.196 561.058,27.846 562.106,28.894 C563.154,29.942 563.803,30.94 564.346,32.338 C564.756,33.346 565.244,34.955 565.378,37.899 C565.536,41.596 565.556,42.717 565.556,50 C565.556,57.283 565.536,58.404 565.378,62.101 M570.82,37.631 C570.674,34.438 570.167,32.258 569.425,30.349 C568.659,28.377 567.633,26.702 565.965,25.035 C564.297,23.368 562.623,22.342 560.652,21.575 C558.743,20.834 556.562,20.326 553.369,20.18 C549.665,20.019 548.475,20 541,20 C533.525,20 532.335,20.019 528.631,20.18 C525.438,20.326 523.257,20.834 521.349,21.575 C519.376,22.342 517.703,23.368 516.035,25.035 C514.368,26.702 513.342,28.377 512.574,30.349 C511.834,32.258 511.326,34.438 511.181,37.631 C511.019,41.335 511,42.525 511,50 C511,57.474 511.019,58.665 511.181,62.369 C511.326,65.562 511.834,67.743 512.574,69.651 C513.342,71.625 514.368,73.296 516.035,74.965 C517.703,76.634 519.376,77.658 521.349,78.425 C523.257,79.167 525.438,79.673 528.631,79.82 C532.335,79.981 533.525,80 541,80 C548.475,80 549.665,79.981 553.369,79.82 C556.562,79.673 558.743,79.167 560.652,78.425 C562.623,77.658 564.297,76.634 565.965,74.965 C567.633,73.296 568.659,71.625 569.425,69.651 C570.167,67.743 570.674,65.562 570.82,62.369 C570.981,58.665 571,57.474 571,50 C571,42.525 570.981,41.335 570.82,37.631"></path></g></g></g></svg></div><div style="padding-top: 8px;"> <div style=" color:#3897f0; font-family:Arial,sans-serif; font-size:14px; font-style:normal; font-weight:550; line-height:18px;">この投稿をInstagramで見る</div></div><div style="padding: 12.5% 0;"></div> <div style="display: flex; flex-direction: row; margin-bottom: 14px; align-items: center;"><div> <div style="background-color: #F4F4F4; border-radius: 50%; height: 12.5px; width: 12.5px; transform: translateX(0px) translateY(7px);"></div> <div style="background-color: #F4F4F4; height: 12.5px; transform: rotate(-45deg) translateX(3px) translateY(1px); width: 12.5px; flex-grow: 0; margin-right: 14px; margin-left: 2px;"></div> <div style="background-color: #F4F4F4; border-radius: 50%; height: 12.5px; width: 12.5px; transform: translateX(9px) translateY(-18px);"></div></div><div style="margin-left: 8px;"> <div style=" background-color: #F4F4F4; border-radius: 4px; height: 12.5px; width: 150px;"></div> <div style=" background-color: #F4F4F4; border-radius: 4px; height: 12.5px; width: 100px;"></div></div></div><div style="padding-bottom: 12px;"> <div style=" background-color: #F4F4F4; border-radius: 4px; height: 12.5px; width: 100%;"></div> <div style=" background-color: #F4F4F4; border-radius: 4px; height: 12.5px; width: 80%;"></div></div></div></a> <p style=" color:#c9c8cd; font-family:Arial,sans-serif; font-size:14px; line-height:17px; margin-bottom:0; margin-top:8px; overflow:hidden; padding:8px 0 7px; text-align:center; text-overflow:ellipsis; white-space:nowrap;"><a href="https://www.instagram.com/p/C75e_S5yb2A/?utm_source=ig_embed&amp;utm_campaign=loading" style=" color:#c9c8cd; font-family:Arial,sans-serif; font-size:14px; font-style:normal; font-weight:normal; line-height:17px; text-decoration:none;" target="_blank">example_bunkasai</a>がシェアした投稿</p></div></blockquote>
        `,
        `
        <blockquote class="instagram-media" data-instgrm-permalink="https://www.instagram.com/p/C75c-51Sj1X/?utm_source=ig_embed&amp;utm_campaign=loading" data-instgrm-version="14" style=" background:#FFF; border:0; border-radius:3px; box-shadow:0 0 1px 0 rgba(0,0,0,0.5),0 1px 10px 0 rgba(0,0,0,0.15); margin: 1px; max-width:540px; min-width:326px; padding:0; width:99.375%; width:-webkit-calc(100% - 2px); width:calc(100% - 2px);"><div style="padding:16px;"> <a href="https://www.instagram.com/p/C75c-51Sj1X/?utm_source=ig_embed&amp;utm_campaign=loading" style=" background:#FFFFFF; line-height:0; padding:0 0; text-align:center; text-decoration:none; width:100%;" target="_blank"> <div style=" display: flex; flex-direction: row; align-items: center;"> <div style="background-color: #F4F4F4; border-radius: 50%; flex-grow: 0; height: 40px; margin-right: 14px; width: 40px;"></div> <div style="display: flex; flex-direction: column; flex-grow: 1; justify-content: center;"> <div style=" background-color: #F4F4F4; border-radius: 4px; height: 14px; margin-bottom: 6px; width: 100px;"></div> <div style=" background-color: #F4F4F4; border-radius: 4px; height: 14px; width: 60px;"></div></div></div><div style="padding: 19% 0;"></div> <div style="display:block; height:50px; margin:0 auto; width:50px;"><svg width="50px" height="50px" viewBox="0 0 60 60" version="1.1" xmlns="https://www.w3.org/2000/svg" xmlns:xlink="https://www.w3.org/1999/xlink"><g stroke="none" stroke-width="1" fill="none" fill-rule="evenodd"><g transform="translate(-511.000000, -20.000000)" fill="#000000"><g><path d="M556.869,30.41 C554.814,30.41 553.148,32.076 553.148,34.131 C553.148,36.186 554.814,37.852 556.869,37.852 C558.924,37.852 560.59,36.186 560.59,34.131 C560.59,32.076 558.924,30.41 556.869,30.41 M541,60.657 C535.114,60.657 530.342,55.887 530.342,50 C530.342,44.114 535.114,39.342 541,39.342 C546.887,39.342 551.658,44.114 551.658,50 C551.658,55.887 546.887,60.657 541,60.657 M541,33.886 C532.1,33.886 524.886,41.1 524.886,50 C524.886,58.9 532.1,66.113 541,66.113 C549.9,66.113 557.114,58.9 557.114,50 C557.114,41.1 549.9,33.886 541,33.886 M565.378,62.101 C565.244,65.045 564.756,66.654 564.346,67.663 C563.803,69.06 563.154,70.057 562.106,71.106 C561.058,72.155 560.06,72.803 558.662,73.347 C557.653,73.757 556.044,74.244 553.101,74.378 C549.404,74.536 548.283,74.556 541,74.556 C533.717,74.556 532.596,74.536 528.898,74.378 C525.955,74.244 524.346,73.757 523.338,73.338 C521.94,72.803 520.942,72.155 519.894,71.106 C518.846,70.057 518.197,69.06 517.654,67.663 C517.244,66.654 516.755,65.045 516.621,62.101 C516.463,58.404 516.484,57.283 516.484,50 C516.484,42.717 516.463,41.596 516.621,37.899 C516.755,34.955 517.244,33.346 517.654,32.338 C518.197,30.94 518.846,29.942 519.894,28.894 C520.942,27.846 521.94,27.196 523.338,26.654 C524.346,26.244 525.955,25.756 528.898,25.622 C532.596,25.464 533.717,25.444 541,25.444 C548.283,25.444 549.404,25.464 553.101,25.622 C556.045,25.756 557.654,26.244 558.662,26.654 C560.06,27.196 561.058,27.846 562.106,28.894 C563.154,29.942 563.803,30.94 564.346,32.338 C564.756,33.346 565.244,34.955 565.378,37.899 C565.536,41.596 565.556,42.717 565.556,50 C565.556,57.283 565.536,58.404 565.378,62.101 M570.82,37.631 C570.674,34.438 570.167,32.258 569.425,30.349 C568.659,28.377 567.633,26.702 565.965,25.035 C564.297,23.368 562.623,22.342 560.652,21.575 C558.743,20.834 556.562,20.326 553.369,20.18 C549.665,20.019 548.475,20 541,20 C533.525,20 532.335,20.019 528.631,20.18 C525.438,20.326 523.257,20.834 521.349,21.575 C519.376,22.342 517.703,23.368 516.035,25.035 C514.368,26.702 513.342,28.377 512.574,30.349 C511.834,32.258 511.326,34.438 511.181,37.631 C511.019,41.335 511,42.525 511,50 C511,57.474 511.019,58.665 511.181,62.369 C511.326,65.562 511.834,67.743 512.574,69.651 C513.342,71.625 514.368,73.296 516.035,74.965 C517.703,76.634 519.376,77.658 521.349,78.425 C523.257,79.167 525.438,79.673 528.631,79.82 C532.335,79.981 533.525,80 541,80 C548.475,80 549.665,79.981 553.369,79.82 C556.562,79.673 558.743,79.167 560.652,78.425 C562.623,77.658 564.297,76.634 565.965,74.965 C567.633,73.296 568.659,71.625 569.425,69.651 C570.167,67.743 570.674,65.562 570.82,62.369 C570.981,58.665 571,57.474 571,50 C571,42.525 570.981,41.335 570.82,37.631"></path></g></g></g></svg></div><div style="padding-top: 8px;"> <div style=" color:#3897f0; font-family:Arial,sans-serif; font-size:14px; font-style:normal; font-weight:550; line-height:18px;">この投稿をInstagramで見る</div></div><div style="padding: 12.5% 0;"></div> <div style="display: flex; flex-direction: row; margin-bottom: 14px; align-items: center;"><div> <div style="background-color: #F4F4F4; border-radius: 50%; height: 12.5px; width: 12.5px; transform: translateX(0px) translateY(7px);"></div> <div style="background-color: #F4F4F4; height: 12.5px; transform: rotate(-45deg) translateX(3px) translateY(1px); width: 12.5px; flex-grow: 0; margin-right: 14px; margin-left: 2px;"></div> <div style="background-color: #F4F4F4; border-radius: 50%; height: 12.5px; width: 12.5px; transform: translateX(9px) translateY(-18px);"></div></div><div style="margin-left: 8px;"> <div style=" background-color: #F4F4F4; border-radius: 4px; height: 12.5px; width: 150px;"></div> <div style=" background-color: #F4F4F4; border-radius: 4px; height: 12.5px; width: 100px;"></div></div></div><div style="padding-bottom: 12px;"> <div style=" background-color: #F4F4F4; border-radius: 4px; height: 12.5px; width: 100%;"></div> <div style=" background-color: #F4F4F4; border-radius: 4px; height: 12.5px; width: 80%;"></div></div></div></a> <p style=" color:#c9c8cd; font-family:Arial,sans-serif; font-size:14px; line-height:17px; margin-bottom:0; margin-top:8px; overflow:hidden; padding:8px 0 7px; text-align:center; text-overflow:ellipsis; white-space:nowrap;"><a href="https://www.instagram.com/p/C75c-51Sj1X/?utm_source=ig_embed&amp;utm_campaign=loading" style=" color:#c9c8cd; font-family:Arial,sans-serif; font-size:14px; font-style:normal; font-weight:normal; line-height:17px; text-decoration:none;" target="_blank">example_bunkasai</a>がシェアした投稿</p></div></blockquote>
        `
        // 実際のInstagram埋め込みコードをここに追加してください
      ];

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
        showContent('schedule'); // 初期表示はInstagramフィード (スケジュールコンテンツ)
      }

      function showShareButtons() {
        shareButtons.classList.add("visible");
        shareButtons.setAttribute("aria-hidden", "false");
      }
      
      // Instagramフィードを埋め込む関数
      function loadInstagramFeed() {
        instagramFeedContainer.innerHTML = ''; // 既存のコンテンツをクリア

        if (instagramPosts.length === 0) {
            instagramFeedContainer.innerHTML = "<p>表示できるInstagram投稿がありません。</p>";
            return;
        }

        instagramPosts.forEach(postHtml => {
          const div = document.createElement('div');
          div.innerHTML = postHtml;
          instagramFeedContainer.appendChild(div);
        });

        // Instagramの埋め込みスクリプトを再実行して、新しく追加された投稿をレンダリング
        // window.instgrm.Embeds が利用可能になるまで待つ必要があるため、setTimeout で遅延させる
        setTimeout(() => {
            if (window.instgrm && window.instgrm.Embeds) {
                window.instgrm.Embeds.process();
            } else {
                console.warn("Instagram Embeds script not loaded yet.");
            }
        }, 500); // 少し遅延させて確実に読み込まれるのを待つ
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
        // 現在、表示するメインコンテンツはInstagramフィードのみなので、常にそれを表示する
        // 将来的に他のコンテンツを追加する場合は、ここで切り替えるロジックを実装
        const contents = [scheduleContent]; // scheduleContent は Instagramフィードのコンテナとして使用

        contents.forEach(content => {
          content.classList.remove('active');
          content.classList.add('hidden');
        });

        if (contentId === 'schedule') { // contentId が 'schedule' の場合に Instagramフィードを表示
          scheduleContent.classList.add('active');
          scheduleContent.classList.remove('hidden');
          loadInstagramFeed(); // Instagramフィードを読み込む
        }
      }

      // --- Event Listeners ---
      startBtn.addEventListener("click", startVirusSimulation);
      repeatVirusBtn.addEventListener("click", startVirusSimulation);
      
      // 新しい「文化祭情報」ボタンの挙動（Instagramアカウントへジャンプ）
      showBunkasaiInfoBtn.addEventListener("click", () => {
        window.open(BUNKASAI_INSTAGRAM_URL, "_blank", "noopener");
      });

      // クイズ＆ミニゲームボタンの挙動
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
          startBtn.textContent = "サイトを見る"; // フェイクサイトのボタンテキストを調整
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
