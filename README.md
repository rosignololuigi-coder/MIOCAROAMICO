<!DOCTYPE html>
<html lang="it">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0, viewport-fit=cover, maximum-scale=1.0, user-scalable=no" />
  <title>Operazione Testimone Arcade</title>
  <style>
    :root {
      --bg1: #0d1431;
      --bg2: #1d3370;
      --bg3: #3867d8;
      --gold: #ffd85b;
      --red: #ff5d73;
      --blue: #55bbff;
      --green: #4be08d;
      --ink: #1c2540;
      --panel: rgba(9, 15, 34, 0.72);
      --white: #ffffff;
    }

    * {
      box-sizing: border-box;
      -webkit-tap-highlight-color: transparent;
      touch-action: manipulation;
    }

    html, body {
      margin: 0;
      width: 100%;
      height: 100%;
      overflow: hidden;
      overscroll-behavior: none;
      position: fixed;
      background: radial-gradient(circle at top, #3d67d8 0%, #1d3270 45%, #0d1431 100%);
      font-family: Arial, Helvetica, sans-serif;
      color: white;
    }

    body {
      padding: env(safe-area-inset-top) 0 env(safe-area-inset-bottom) 0;
    }

    .app {
      width: 100vw;
      height: 100dvh;
      display: grid;
      grid-template-rows: auto auto 1fr auto;
      gap: 6px;
      justify-items: center;
      padding: 6px;
      overflow: hidden;
      user-select: none;
    }

    .title {
      font-size: clamp(18px, 2.8vw, 34px);
      font-weight: 900;
      letter-spacing: .8px;
      text-transform: uppercase;
      color: var(--gold);
      text-shadow: 0 3px 0 #a57508, 0 10px 18px rgba(0,0,0,.26);
      text-align: center;
      line-height: 1;
      margin-top: 2px;
    }

    .hud {
      width: min(1180px, 100%);
      display: flex;
      flex-wrap: wrap;
      justify-content: center;
      gap: 6px;
    }

    .pill {
      background: var(--panel);
      border: 2px solid rgba(255,255,255,.12);
      border-radius: 999px;
      padding: 6px 10px;
      font-size: 11px;
      font-weight: 800;
      box-shadow: 0 8px 16px rgba(0,0,0,.18);
    }

    .shell {
      width: min(1180px, 100%);
      min-height: 0;
      height: 100%;
      background: linear-gradient(180deg, rgba(255,255,255,.08), rgba(255,255,255,.03));
      border: 3px solid rgba(255,255,255,.16);
      border-radius: 18px;
      padding: 6px;
      position: relative;
      box-shadow: 0 20px 50px rgba(0,0,0,.28);
      overflow: hidden;
    }

    canvas {
      width: 100%;
      height: 100%;
      display: block;
      border-radius: 14px;
      background: linear-gradient(180deg, #3968d8 0%, #1e3776 45%, #101a3e 100%);
      touch-action: none;
    }

    .controls {
      width: min(1180px, 100%);
      display: grid;
      grid-template-columns: 1fr auto 1fr;
      align-items: center;
      gap: 8px;
      padding-bottom: 2px;
    }

    .hint {
      background: var(--panel);
      border: 2px solid rgba(255,255,255,.12);
      border-radius: 14px;
      padding: 8px 10px;
      text-align: center;
      font-size: 11px;
      color: #dfe8ff;
      line-height: 1.2;
    }

    .touch {
      display: flex;
      align-items: center;
      justify-content: center;
      gap: 12px;
    }

    .dpad {
      display: grid;
      grid-template-columns: repeat(2, 64px);
      gap: 10px;
    }

    .btn {
      border: 0;
      width: 64px;
      height: 64px;
      border-radius: 18px;
      background: linear-gradient(180deg, #476ed2, #294388);
      color: white;
      font-size: 28px;
      font-weight: 900;
      box-shadow: 0 10px 20px rgba(0,0,0,.24), inset 0 2px 0 rgba(255,255,255,.12), inset 0 -3px 0 rgba(0,0,0,.18);
    }

    .btn.big {
      width: 86px;
      height: 86px;
      border-radius: 999px;
      background: linear-gradient(180deg, #ff7e8e, #d73b53);
      font-size: 20px;
    }

    .btn.restart {
      background: linear-gradient(180deg, #57c7ff, #1f77d1);
    }

    .btn:active,
    .btn.active {
      transform: translateY(2px) scale(.98);
      filter: brightness(1.06);
    }

    .touch-overlay {
      position: absolute;
      inset: auto 12px 12px 12px;
      display: none;
      align-items: end;
      justify-content: space-between;
      pointer-events: none;
      z-index: 4;
    }

    .touch-overlay .cluster,
    .touch-overlay .cluster-right {
      display: flex;
      gap: 10px;
      pointer-events: auto;
    }

    .touch-overlay .cluster-right {
      align-items: end;
    }

    .touch-overlay .mini {
      width: 58px;
      height: 58px;
      border-radius: 18px;
      border: 0;
      background: rgba(17, 31, 74, .66);
      color: white;
      font-size: 24px;
      font-weight: 900;
      backdrop-filter: blur(5px);
      box-shadow: 0 12px 22px rgba(0,0,0,.25), inset 0 2px 0 rgba(255,255,255,.1);
    }

    .touch-overlay .mini.jump {
      width: 72px;
      height: 72px;
      border-radius: 999px;
      background: rgba(211, 58, 83, .78);
      font-size: 16px;
    }

    .intro-screen,
    .level-card,
    .pause-screen {
      position: absolute;
      inset: 0;
      display: flex;
      align-items: center;
      justify-content: center;
      background: linear-gradient(180deg, rgba(4,7,18,.28), rgba(4,7,18,.82));
      z-index: 7;
      backdrop-filter: blur(2px);
      padding: 8px;
    }

    .intro-hidden,
    .level-hidden,
    .pause-hidden {
      display: none;
    }

    .intro-card,
    .level-popup,
    .pause-card {
      width: min(760px, calc(100% - 12px));
      max-height: calc(100dvh - 40px);
      overflow: auto;
      background: linear-gradient(180deg, rgba(20,28,62,.96), rgba(11,17,38,.96));
      border: 2px solid rgba(255,255,255,.14);
      border-radius: 22px;
      box-shadow: 0 25px 70px rgba(0,0,0,.35);
      position: relative;
      -webkit-overflow-scrolling: touch;
    }

    .intro-top {
      position: relative;
      min-height: 120px;
      padding: 14px 14px 0;
      background: radial-gradient(circle at 20% 15%, rgba(96,176,255,.25), transparent 38%), linear-gradient(180deg, #284892 0%, #152552 58%, #101936 100%);
    }

    .intro-city {
      position: absolute;
      left: 0;
      right: 0;
      bottom: 0;
      height: 78px;
      background:
        linear-gradient(180deg, transparent 0%, transparent 36%, rgba(3,5,12,.15) 37%),
        linear-gradient(180deg, transparent 0, transparent 60%, #0b142b 60%),
        repeating-linear-gradient(90deg, #1a2952 0 48px, #0f1b3f 48px 92px, #18295a 92px 146px);
      clip-path: polygon(0 100%,0 35%,5% 20%,10% 48%,16% 15%,21% 54%,28% 26%,33% 62%,40% 18%,46% 50%,51% 12%,57% 60%,65% 27%,72% 68%,79% 20%,85% 58%,92% 30%,100% 64%,100% 100%);
      opacity: .9;
    }

    .intro-content {
      position: relative;
      z-index: 1;
      padding: 0 14px 14px;
    }

    .intro-badge {
      display: inline-block;
      background: rgba(255,216,91,.14);
      border: 1px solid rgba(255,216,91,.28);
      color: #ffe38a;
      padding: 6px 10px;
      border-radius: 999px;
      font-size: 10px;
      font-weight: 800;
      margin-bottom: 8px;
    }

    .intro-title {
      font-size: clamp(20px, 3.2vw, 38px);
      font-weight: 900;
      line-height: .95;
      margin: 0 0 8px;
      text-transform: uppercase;
      color: white;
      text-shadow: 0 6px 18px rgba(0,0,0,.24);
    }

    .intro-text {
      color: #dfe8ff;
      font-size: 14px;
      line-height: 1.3;
      margin: 0 0 10px;
    }

    .intro-grid {
      display: grid;
      grid-template-columns: 1.2fr .8fr;
      gap: 10px;
      align-items: stretch;
      margin-top: 10px;
    }

    .intro-panel,
    .intro-side {
      background: rgba(255,255,255,.04);
      border: 1px solid rgba(255,255,255,.08);
      border-radius: 16px;
      padding: 12px;
    }

    .intro-side {
      display: grid;
      align-content: space-between;
      gap: 12px;
    }

    .intro-panel p,
    .intro-side p,
    .pause-card p {
      margin: 0;
      color: #d8e4ff;
      line-height: 1.28;
      font-size: 13px;
    }

    .cta-row {
      display: flex;
      gap: 10px;
      flex-wrap: wrap;
      margin-top: 10px;
    }

    .cta {
      border: 0;
      border-radius: 14px;
      padding: 11px 14px;
      font-size: 14px;
      font-weight: 900;
      cursor: pointer;
      color: white;
      box-shadow: 0 10px 18px rgba(0,0,0,.18);
    }

    .cta.primary {
      background: linear-gradient(180deg, #ffcf59, #db8f1c);
      color: #1a223b;
    }

    .cta.secondary {
      background: linear-gradient(180deg, #5fb8ff, #246fd9);
    }

    .cta.pause {
      background: linear-gradient(180deg, #67d4a1, #1f9c68);
      color: #092236;
    }

    .comic-line {
      font-size: 13px;
      color: #ffe39a;
      font-weight: 800;
      letter-spacing: .2px;
    }

    .portrait {
      height: 88px;
      border-radius: 18px;
      background: linear-gradient(180deg, rgba(95,184,255,.22), rgba(40,71,147,.12));
      position: relative;
      overflow: hidden;
    }

    .portrait svg {
      width: 100%;
      height: 100%;
      display: block;
    }

    .level-popup,
    .pause-card {
      padding: 18px;
      text-align: center;
      width: min(520px, calc(100% - 12px));
    }

    .level-popup h3,
    .pause-card h3 {
      margin: 0 0 8px;
      font-size: clamp(24px, 4vw, 38px);
      color: var(--gold);
      text-transform: uppercase;
    }

    .level-popup p {
      margin: 0;
      color: #dbe6ff;
      line-height: 1.35;
      font-size: 15px;
    }

    @media (max-width: 900px) {
      .controls { grid-template-columns: 1fr; }
      .hint { order: -1; }
      .intro-grid { grid-template-columns: 1fr; }
      .intro-side { display: none; }
    }

    @media (max-width: 780px) {
      .touch-overlay { display: flex; }
    }

    @media (max-width: 640px) {
      .app { padding: 4px; gap: 4px; }
      .shell { border-radius: 14px; padding: 4px; }
      .dpad { grid-template-columns: repeat(2, 52px); gap: 8px; }
      .btn { width: 52px; height: 52px; font-size: 22px; border-radius: 16px; }
      .btn.big { width: 68px; height: 68px; font-size: 15px; }
      .pill { padding: 5px 8px; font-size: 10px; }
      .hint { display: none; }
      .intro-top { min-height: 92px; padding: 10px 10px 0; }
      .intro-content { padding: 0 10px 10px; }
      .intro-text { font-size: 12px; }
      .intro-panel, .intro-side { padding: 10px; }
      .cta { width: 100%; font-size: 13px; padding: 10px 12px; }
      .touch-overlay .mini { width: 48px; height: 48px; font-size: 20px; }
      .touch-overlay .mini.jump { width: 64px; height: 64px; font-size: 14px; }
      .level-popup, .pause-card { padding: 16px; width: min(420px, calc(100% - 18px)); }
      .level-popup h3, .pause-card h3 { font-size: 24px; }
      .level-popup p, .pause-card p { font-size: 14px; }
    }
  </style>
</head>
<body>
  <div class="app">
    <div class="title">Operazione Testimone Arcade</div>

    <div class="hud">
      <div class="pill" id="hudLevel">🚓 Livello 1 / 3</div>
      <div class="pill" id="hudLives">❤️ Vite: 3</div>
      <div class="pill" id="hudBadges">🛡️ Distintivi: 0 / 4</div>
      <div class="pill" id="hudBoss">⚠️ Boss: no</div>
      <div class="pill" id="hudMsg">📢 Tocca i pulsanti o usa tastiera</div>
    </div>

    <div class="shell">
      <canvas id="game" width="1280" height="720"></canvas>

      <div class="touch-overlay" id="touchOverlay">
        <div class="cluster">
          <button class="mini" id="leftBtnOverlay">◀</button>
          <button class="mini" id="rightBtnOverlay">▶</button>
        </div>
        <div class="cluster-right">
          <button class="mini jump" id="jumpBtnOverlay">JUMP</button>
        </div>
      </div>

      <div class="intro-screen" id="introScreen">
        <div class="intro-card">
          <div class="intro-top">
            <div class="intro-city"></div>
          </div>
          <div class="intro-content">
            <div class="intro-badge">TORINO • NOTTE • OPERAZIONE STRAORDINARIA</div>
            <h2 class="intro-title">Luigi contro la notte</h2>
            <p class="intro-text">
              Io sono <b>Luigi</b>, poliziotto testardo, nervoso il giusto e con zero voglia di lasciare Torino ai delinquenti.
              Stasera la città è nel caos, e da qualche parte c'è <b>una ragazza speciale da proteggere</b> prima che la situazione degeneri.
            </p>

            <div class="intro-grid">
              <div class="intro-panel">
                <p class="comic-line">"La città è mia. I criminali no."</p>
                <p style="margin-top:10px;">
                  Salta sui tetti, raccogli i distintivi, evita i colpi del boss, metti fuori gioco i criminali e apriti la strada fino al finale.
                  Se arrivi in fondo, non trovi una medaglia. Trovi una domanda molto più importante.
                </p>
              </div>
              <div class="intro-side">
                <div class="portrait">
                  <svg viewBox="0 0 220 136" xmlns="http://www.w3.org/2000/svg" aria-hidden="true"><defs><linearGradient id="sky" x1="0" y1="0" x2="0" y2="1"><stop offset="0%" stop-color="#3b68d8"/><stop offset="100%" stop-color="#172a59"/></linearGradient><linearGradient id="coat" x1="0" y1="0" x2="0" y2="1"><stop offset="0%" stop-color="#6dc9ff"/><stop offset="100%" stop-color="#2470db"/></linearGradient></defs><rect width="220" height="136" fill="url(#sky)"/><circle cx="181" cy="24" r="18" fill="#ffe7a0" opacity=".9"/><g opacity=".7"><rect x="0" y="95" width="40" height="41" fill="#17244a"/><rect x="30" y="70" width="38" height="66" fill="#1a2a57"/><rect x="62" y="84" width="28" height="52" fill="#142044"/><rect x="87" y="60" width="48" height="76" fill="#1b2d5f"/><rect x="129" y="80" width="32" height="56" fill="#15224a"/></g><ellipse cx="110" cy="126" rx="32" ry="6" fill="#0b1124" opacity=".22"/><rect x="95" y="74" width="30" height="36" rx="10" fill="url(#coat)"/><rect x="104" y="76" width="10" height="18" rx="3" fill="#f2c844"/><rect x="94" y="69" width="32" height="10" rx="6" fill="#1b2f60"/><rect x="90" y="79" width="9" height="21" rx="5" fill="#2d73df"/><rect x="121" y="79" width="9" height="21" rx="5" fill="#2d73df"/><rect x="91" y="97" width="8" height="8" rx="4" fill="#f3c89d"/><rect x="122" y="97" width="8" height="8" rx="4" fill="#f3c89d"/><rect x="100" y="36" width="22" height="20" rx="9" fill="#f3c89d"/><rect x="96" y="33" width="30" height="12" rx="8" fill="#20305e"/><rect x="100" y="38" width="22" height="5" rx="4" fill="#5cb5ff"/><circle cx="107" cy="46" r="2" fill="#111"/><circle cx="116" cy="46" r="2" fill="#111"/><path d="M106 52c2 3 8 3 10 0" stroke="#a56c57" stroke-width="2" fill="none" stroke-linecap="round"/><rect x="98" y="109" width="10" height="19" rx="5" fill="#223466"/><rect x="112" y="109" width="10" height="19" rx="5" fill="#223466"/><rect x="96" y="124" width="14" height="6" rx="3" fill="#161a22"/><rect x="111" y="124" width="14" height="6" rx="3" fill="#161a22"/></svg>
                </div>
                <p>
                  <b>Obiettivo:</b> aiutami a ripulire Torino, arrivare fino in fondo e mettere al sicuro la persona più importante della serata.
                </p>
              </div>
            </div>

            <div class="cta-row">
              <button class="cta primary" id="startBtn" type="button">FACCIAMOLO INSIEME</button>
              <button class="cta secondary" id="skipIntroBtn" type="button">SALTA INTRO</button>
            </div>
          </div>
        </div>
      </div>

      <div class="level-card level-hidden" id="levelCard">
        <div class="level-popup">
          <h3 id="levelPopupTitle">Livello 1</h3>
          <p id="levelPopupText">Torino non dorme. E noi nemmeno.</p>
        </div>
      </div>

      <div class="pause-screen pause-hidden" id="pauseScreen">
        <div class="pause-card">
          <h3>Pausa</h3>
          <p>Respira un attimo. Quando vuoi, riparti e torna a ripulire Torino.</p>
          <div class="cta-row" style="justify-content:center;">
            <button class="cta pause" id="resumeBtn" type="button">RIPRENDI</button>
            <button class="cta secondary" id="restartFromPauseBtn" type="button">RICOMINCIA</button>
          </div>
        </div>
      </div>
    </div>

    <div class="controls">
      <div class="touch">
        <div class="dpad">
          <button class="btn" id="leftBtn" type="button">◀</button>
          <button class="btn" id="rightBtn" type="button">▶</button>
        </div>
        <button class="btn big" id="jumpBtn" type="button">JUMP</button>
      </div>

      <div class="hint">
        Telefono: usa i tasti qui sotto o quelli sopra il gioco. La pagina non deve più scorrere.<br>
        Obiettivo: raccogli i distintivi, schiaccia i nemici, schiva i colpi del boss e arriva in fondo. Premi <b>P</b> per la pausa.
      </div>

      <div class="touch">
        <button class="btn restart" id="restartBtn" type="button">↻</button>
      </div>
    </div>
  </div>

  <script>
    const canvas = document.getElementById('game');
    const ctx = canvas.getContext('2d');

    const hudLevel = document.getElementById('hudLevel');
    const hudLives = document.getElementById('hudLives');
    const hudBadges = document.getElementById('hudBadges');
    const hudBoss = document.getElementById('hudBoss');
    const hudMsg = document.getElementById('hudMsg');
    const introScreen = document.getElementById('introScreen');
    const levelCard = document.getElementById('levelCard');
    const levelPopupTitle = document.getElementById('levelPopupTitle');
    const levelPopupText = document.getElementById('levelPopupText');
    const startBtn = document.getElementById('startBtn');
    const skipIntroBtn = document.getElementById('skipIntroBtn');
    const pauseScreen = document.getElementById('pauseScreen');
    const resumeBtn = document.getElementById('resumeBtn');
    const restartFromPauseBtn = document.getElementById('restartFromPauseBtn');

    ['gesturestart','gesturechange','gestureend','touchmove'].forEach(ev => {
      document.addEventListener(ev, e => {
        if (ev === 'touchmove') e.preventDefault();
      }, { passive: false });
    });

    const keys = { left:false, right:false, jump:false };
    const GRAVITY = 0.6;

    const audioCtx = window.AudioContext ? new AudioContext() : (window.webkitAudioContext ? new webkitAudioContext() : null);

    function beep(freq = 440, duration = 0.08, type = 'square', volume = 0.02) {
      if (!audioCtx) return;
      if (audioCtx.state === 'suspended') audioCtx.resume().catch(() => {});
      const osc = audioCtx.createOscillator();
      const gain = audioCtx.createGain();
      osc.type = type;
      osc.frequency.value = freq;
      gain.gain.value = volume;
      osc.connect(gain);
      gain.connect(audioCtx.destination);
      const now = audioCtx.currentTime;
      gain.gain.setValueAtTime(volume, now);
      gain.gain.exponentialRampToValueAtTime(0.0001, now + duration);
      osc.start(now);
      osc.stop(now + duration);
    }

    const levels = [
      { name:'Quartiere Porto', popup:'Parti dal quartiere vecchio. Pattuglia i vicoli, prendi i distintivi e fai capire a tutti chi comanda stasera.', width:2500, spawn:{x:90,y:530}, exit:{x:2320,y:484,w:92,h:118}, badges:[{x:300,y:490},{x:790,y:330},{x:1280,y:392},{x:1820,y:300}], platforms:[{x:0,y:610,w:2600,h:130,type:'ground'},{x:220,y:530,w:180,h:28,type:'platform'},{x:520,y:450,w:160,h:28,type:'platform'},{x:740,y:370,w:200,h:28,type:'platform'},{x:1050,y:430,w:220,h:28,type:'platform'},{x:1370,y:350,w:170,h:28,type:'platform'},{x:1630,y:470,w:170,h:28,type:'platform'},{x:1770,y:320,w:180,h:28,type:'platform'},{x:2060,y:410,w:190,h:28,type:'platform'}], enemies:[{x:350,y:552,w:56,h:60,range:110,speed:1.4,type:'thug'},{x:835,y:312,w:56,h:60,range:80,speed:1.6,type:'thug'},{x:1450,y:292,w:56,h:60,range:90,speed:1.5,type:'thug'},{x:2090,y:372,w:56,h:60,range:100,speed:1.7,type:'thug'}], theme:'port' },
      { name:'Tetti in Città', popup:'Sali sui tetti. Qui i criminali sono più veloci e Torino, vista da lassù, sembra quasi bella. Quasi.', width:2800, spawn:{x:110,y:500}, exit:{x:2610,y:438,w:92,h:118}, badges:[{x:410,y:420},{x:900,y:275},{x:1460,y:355},{x:2210,y:245}], platforms:[{x:0,y:610,w:2900,h:130,type:'ground'},{x:160,y:500,w:200,h:28,type:'platform'},{x:420,y:430,w:180,h:28,type:'platform'},{x:690,y:350,w:180,h:28,type:'platform'},{x:920,y:280,w:220,h:28,type:'platform'},{x:1230,y:420,w:190,h:28,type:'platform'},{x:1490,y:360,w:170,h:28,type:'platform'},{x:1750,y:300,w:200,h:28,type:'platform'},{x:2030,y:440,w:180,h:28,type:'platform'},{x:2160,y:250,w:180,h:28,type:'platform'},{x:2450,y:390,w:180,h:28,type:'platform'}], enemies:[{x:480,y:392,w:56,h:60,range:90,speed:1.8,type:'agent'},{x:980,y:242,w:56,h:60,range:100,speed:1.9,type:'agent'},{x:1550,y:322,w:56,h:60,range:85,speed:2.0,type:'agent'},{x:2230,y:212,w:56,h:60,range:70,speed:2.0,type:'agent'},{x:2500,y:352,w:56,h:60,range:75,speed:1.85,type:'agent'}], theme:'rooftop' },
      { name:'Covile del Boss', popup:'Ultimo round. Più atmosfera, più cattiveria, ma ora puoi salire senza bestemmiare: piattaforme più accessibili e salti umani.', width:3320, spawn:{x:100,y:530}, exit:{x:3070,y:434,w:106,h:130}, badges:[{x:550,y:450},{x:1170,y:340},{x:1740,y:400},{x:2480,y:260}], platforms:[{x:0,y:610,w:3420,h:130,type:'ground'},{x:300,y:520,w:200,h:28,type:'platform'},{x:580,y:460,w:200,h:28,type:'platform'},{x:860,y:400,w:220,h:28,type:'platform'},{x:1150,y:340,w:220,h:28,type:'platform'},{x:1450,y:420,w:200,h:28,type:'platform'},{x:1700,y:360,w:180,h:28,type:'platform'},{x:1950,y:320,w:200,h:28,type:'platform'},{x:2200,y:460,w:180,h:28,type:'platform'},{x:2400,y:380,w:170,h:28,type:'platform'},{x:2600,y:320,w:170,h:28,type:'platform'},{x:2800,y:270,w:180,h:28,type:'platform'}], enemies:[{x:680,y:512,w:56,h:60,range:95,speed:1.8,type:'agent'},{x:1380,y:392,w:56,h:60,range:95,speed:2.0,type:'agent'},{x:2030,y:282,w:56,h:60,range:110,speed:1.9,type:'agent'}], boss:{x:2860,y:482,w:140,h:146,life:5,range:180,speed:1.4,dir:1,fireCooldown:95}, theme:'boss' }
    ];

    const state = { levelIndex:0, lives:3, won:false, cameraX:0, message:'', msgTimer:0, shake:0, invincibleTimer:0, started:false, paused:false, showLevelCard:false, levelCardTimer:0, bossShots:[], checkpoints:{} };

    function currentLevel(){ return levels[state.levelIndex]; }
    function makePlayer(){ const lvl=currentLevel(); return { x:lvl.spawn.x, y:lvl.spawn.y, w:54, h:76, vx:0, vy:0, speed:0.9, maxSpeed:6.2, jumpPower:14, onGround:false, facing:1, invuln:0, anim:0, attackFlash:0 }; }

    let player = makePlayer();
    let particles = [];
    let rainDrops = [];
    let tick = 0;

    function buildRain(){ rainDrops=[]; for(let i=0;i<65;i++){ rainDrops.push({x:Math.random()*canvas.width,y:Math.random()*canvas.height,len:10+Math.random()*14,speed:5+Math.random()*4}); } }
    function showLevelIntro(){ const lvl=currentLevel(); levelPopupTitle.textContent=`Livello ${state.levelIndex+1} – ${lvl.name}`; levelPopupText.textContent=lvl.popup; state.showLevelCard=true; state.levelCardTimer=105; levelCard.classList.remove('level-hidden'); }
    function hideLevelIntro(){ state.showLevelCard=false; levelCard.classList.add('level-hidden'); }
    function hideIntro(){ introScreen.classList.add('intro-hidden'); introScreen.style.pointerEvents='none'; }

    function startGame(){ hideIntro(); state.started=true; state.paused=false; state.message='Torino non si ripulisce da sola.'; state.msgTimer=110; showLevelIntro(); beep(660,.09,'square',.025); updateHud(); }
    function setCheckpoint(){ state.checkpoints[state.levelIndex] = { x: player.x, y: Math.max(0, player.y) }; }
    function respawnAtCheckpoint(){ const cp = state.checkpoints[state.levelIndex]; const lvl=currentLevel(); if(cp){ player.x=cp.x; player.y=cp.y; } else { player.x=lvl.spawn.x; player.y=lvl.spawn.y; } player.vx=0; player.vy=0; state.bossShots=[]; }

    function resetLevel(fullResetBadges = true){ const lvl=currentLevel(); player=makePlayer(); state.cameraX=0; state.shake=0; state.bossShots=[]; particles=[]; if(fullResetBadges) lvl.badges.forEach(b=>b.taken=false); lvl.enemies.forEach(e=>{ e.dead=false; e.baseX=e.x; e.dir=1; e.phase=Math.random()*Math.PI*2; }); if(lvl.boss){ lvl.boss.currentLife=lvl.boss.life; lvl.boss.baseX=lvl.boss.x; lvl.boss.dir=1; lvl.boss.hitCooldown=0; lvl.boss.fireCooldown=75; } state.checkpoints[state.levelIndex] = { x: lvl.spawn.x, y: lvl.spawn.y }; updateHud(); }

    function restartGame(){ state.levelIndex=0; state.lives=3; state.won=false; state.paused=false; state.message='Si parte.'; state.msgTimer=80; state.started=true; state.checkpoints={}; levels.forEach(lvl=>{ lvl.badges.forEach(b=>b.taken=false); lvl.enemies.forEach(e=>e.dead=false); if(lvl.boss) lvl.boss.currentLife=lvl.boss.life; }); resetLevel(true); showLevelIntro(); hidePause(); }

    function togglePause(){ if (!state.started || state.won || !introScreen.classList.contains('intro-hidden')) return; state.paused = !state.paused; if (state.paused) pauseScreen.classList.remove('pause-hidden'); else pauseScreen.classList.add('pause-hidden'); beep(state.paused ? 360 : 620, .07, 'square', .02); }
    function hidePause(){ state.paused = false; pauseScreen.classList.add('pause-hidden'); }

    function addParticles(x,y,color,count=10,power=3){ for(let i=0;i<count;i++){ particles.push({x,y,vx:(Math.random()-.5)*power*2,vy:(Math.random()-.5)*power*2,life:20+Math.random()*22,size:4+Math.random()*4,color}); } }
    function rectsCollide(a,b){ return a.x < b.x+b.w && a.x+a.w > b.x && a.y < b.y+b.h && a.y+a.h > b.y; }
    function roundedRect(x,y,w,h,r,fill=true,stroke=false){ ctx.beginPath(); ctx.moveTo(x+r,y); ctx.arcTo(x+w,y,x+w,y+h,r); ctx.arcTo(x+w,y+h,x,y+h,r); ctx.arcTo(x,y+h,x,y,r); ctx.arcTo(x,y,x+w,y,r); ctx.closePath(); if(fill) ctx.fill(); if(stroke) ctx.stroke(); }

    function updateHud(){ const lvl=currentLevel(); const collected=lvl.badges.filter(b=>b.taken).length; hudLevel.textContent=`🚓 Livello ${state.levelIndex+1} / 3`; hudLives.textContent=`❤️ Vite: ${state.lives}`; hudBadges.textContent=`🛡️ Distintivi: ${collected} / ${lvl.badges.length}`; hudBoss.textContent=lvl.boss ? `⚠️ Boss: ${lvl.boss.currentLife}/${lvl.boss.life}` : '⚠️ Boss: no'; hudMsg.textContent=state.msgTimer>0 ? `📢 ${state.message}` : `📢 ${lvl.name}`; }

    function collectBadge(badge){ badge.taken=true; state.message='Distintivo raccolto.'; state.msgTimer=55; addParticles(badge.x,badge.y,'#ffd85b',16,4); beep(880,.08,'triangle',.03); setCheckpoint(); updateHud(); }
    function hitEnemy(enemy){ enemy.dead=true; player.vy=-8; player.attackFlash=8; addParticles(enemy.x+enemy.w/2,enemy.y+18,'#ff6278',18,4.2); state.message='Nemico sistemato.'; state.msgTimer=60; beep(240,.08,'sawtooth',.03); }
    function hitBoss(boss){ if(boss.hitCooldown>0) return; boss.currentLife--; boss.hitCooldown=18; player.vy=-9; player.attackFlash=10; state.shake=10; addParticles(boss.x+boss.w/2,boss.y+40,'#ffd85b',20,5); state.message=boss.currentLife>0 ? `Boss colpito! ${boss.currentLife} colpi rimasti.` : 'Boss distrutto.'; state.msgTimer=70; beep(180,.09,'sawtooth',.04); if (boss.currentLife <= 0) setCheckpoint(); }
    function fireBossShot(boss){ const dir=player.x < boss.x ? -1 : 1; state.bossShots.push({x:boss.x+boss.w/2,y:boss.y+50,vx:dir*(5.5+Math.random()*1.4),vy:-1.2+Math.random()*.8,r:14,life:180}); beep(320,.05,'square',.015); }

    function loseLife(msg){ if(player.invuln>0 || state.won || !state.started) return; state.lives--; player.invuln=90; state.message=msg; state.msgTimer=100; state.shake=12; addParticles(player.x+player.w/2,player.y+player.h/2,'#ff6278',20,5); beep(120,.15,'sawtooth',.04); if(state.lives<=0){ restartGame(); state.message='Game over. Riparti e non fare il turista.'; state.msgTimer=120; } else { respawnAtCheckpoint(); } updateHud(); }
    function nextLevel(){ if(state.levelIndex < levels.length-1){ state.levelIndex++; state.message=`Livello ${state.levelIndex+1}: ${currentLevel().name}`; state.msgTimer=120; resetLevel(true); showLevelIntro(); beep(720,.1,'triangle',.03); } else { state.won=true; beep(880,.12,'triangle',.04); setTimeout(()=>beep(1120,.18,'triangle',.04),120); } }

    function handleInput(){ if(keys.left){ player.vx -= player.speed; player.facing=-1; } if(keys.right){ player.vx += player.speed; player.facing=1; } if(!keys.left && !keys.right) player.vx *= .82; player.vx = Math.max(-player.maxSpeed, Math.min(player.maxSpeed, player.vx)); if(keys.jump && player.onGround){ player.vy = -player.jumpPower; player.onGround=false; addParticles(player.x+player.w/2,player.y+player.h,'#d8f3ff',10,3); beep(520,.05,'square',.02); } keys.jump=false; }

    function updateBossShots(lvl){ if(!lvl.boss || lvl.boss.currentLife<=0){ state.bossShots=[]; return; } state.bossShots = state.bossShots.filter(s=>s.life>0); for(const s of state.bossShots){ s.x+=s.vx; s.y+=s.vy; s.vy+=.03; s.life--; const hit={x:s.x-s.r,y:s.y-s.r,w:s.r*2,h:s.r*2}; if(rectsCollide(player,hit)){ s.life=0; loseLife('Il boss ti ha preso con un colpo.'); } if(s.y>canvas.height+80 || s.x<-100 || s.x>lvl.width+100) s.life=0; } }

    function updateGame(){ if(!state.started || state.won || state.paused) return; const lvl=currentLevel(); tick += .016; if(state.msgTimer>0) state.msgTimer--; if(state.shake>0) state.shake *= .84; if(player.invuln>0) player.invuln--; if(player.attackFlash>0) player.attackFlash--; if(state.levelCardTimer>0){ state.levelCardTimer--; if(state.levelCardTimer<=0) hideLevelIntro(); } handleInput(); player.anim += Math.abs(player.vx)*.16 + .04; player.vy += GRAVITY; if(player.vy>14) player.vy=14; player.x += player.vx; for(const p of lvl.platforms){ if(rectsCollide(player,p)){ if(player.vx>0) player.x=p.x-player.w; else if(player.vx<0) player.x=p.x+p.w; player.vx=0; } } player.y += player.vy; player.onGround=false; for(const p of lvl.platforms){ if(rectsCollide(player,p)){ if(player.vy>0){ player.y=p.y-player.h; player.vy=0; player.onGround=true; } else if(player.vy<0){ player.y=p.y+p.h; player.vy=0; } } } if(player.y > canvas.height+120) loseLife('Sei caduto. Mossa alquanto cretina.'); if(player.x < 0) player.x=0; if(player.x + player.w > lvl.width) player.x = lvl.width - player.w; for(const enemy of lvl.enemies){ if(enemy.dead) continue; enemy.x += enemy.speed*enemy.dir; if(enemy.x > enemy.baseX+enemy.range || enemy.x < enemy.baseX-enemy.range) enemy.dir*=-1; const hit={x:enemy.x,y:enemy.y,w:enemy.w,h:enemy.h}; if(rectsCollide(player,hit)){ if(player.vy>0 && player.y+player.h-12 < enemy.y+20) hitEnemy(enemy); else loseLife('Ti hanno menato. Male.'); } } if(lvl.boss){ const boss=lvl.boss; if(boss.currentLife>0){ if(boss.hitCooldown>0) boss.hitCooldown--; boss.fireCooldown--; boss.x += boss.speed*boss.dir; if(boss.x > boss.baseX+boss.range || boss.x < boss.baseX-boss.range) boss.dir*=-1; if(boss.fireCooldown<=0){ fireBossShot(boss); boss.fireCooldown = 65 + Math.random()*30; } const hit={x:boss.x,y:boss.y,w:boss.w,h:boss.h}; if(rectsCollide(player,hit)){ if(player.vy>0 && player.y+player.h-14 < boss.y+26) hitBoss(boss); else loseLife('Il boss ti ha asfaltato.'); } } } for(const badge of lvl.badges){ if(badge.taken) continue; const box={x:badge.x-18,y:badge.y-18,w:36,h:36}; if(rectsCollide(player,box)) collectBadge(badge); } updateBossShots(lvl); const allBadges = lvl.badges.every(b=>b.taken); const bossDown = !lvl.boss || lvl.boss.currentLife<=0; if(allBadges && bossDown && rectsCollide(player,lvl.exit)) nextLevel(); const targetCam = player.x - canvas.width*.32; state.cameraX += (targetCam - state.cameraX)*.1; state.cameraX = Math.max(0, Math.min(lvl.width - canvas.width, state.cameraX)); particles = particles.filter(p=>p.life>0); for(const p of particles){ p.x+=p.vx; p.y+=p.vy; p.vy+=.12; p.vx*=.98; p.life--; } updateHud(); }

    function drawCloud(x,y,s=1,alpha=.9){ ctx.save(); ctx.translate(x,y); ctx.scale(s,s); ctx.globalAlpha=alpha; ctx.fillStyle='#ffffff'; ctx.beginPath(); ctx.arc(18,24,16,0,Math.PI*2); ctx.arc(40,18,22,0,Math.PI*2); ctx.arc(67,23,18,0,Math.PI*2); ctx.arc(49,31,20,0,Math.PI*2); ctx.fill(); ctx.restore(); ctx.globalAlpha=1; }
    function drawWindowGrid(x,base,w,h,color){ ctx.fillStyle=color; for(let yy=base-h+18; yy<base-12; yy+=16){ for(let xx=x+12; xx<x+w-10; xx+=14){ if(((xx+yy)%3)===0) ctx.fillRect(xx,yy,6,9); } } }
    function drawRain(){ ctx.save(); ctx.strokeStyle='rgba(180,220,255,.18)'; ctx.lineWidth=2; for(const d of rainDrops){ ctx.beginPath(); ctx.moveTo(d.x,d.y); ctx.lineTo(d.x-5,d.y+d.len); ctx.stroke(); d.y+=d.speed; d.x-=1.1; if(d.y>canvas.height+20){ d.y=-10; d.x=Math.random()*canvas.width; } if(d.x<-20) d.x=canvas.width+20; } ctx.restore(); }
    function drawSearchLight(){ const baseX=canvas.width-150; const sway=Math.sin(tick*1.2)*70; ctx.save(); ctx.fillStyle='rgba(255,255,200,.08)'; ctx.beginPath(); ctx.moveTo(baseX,220); ctx.lineTo(baseX+sway-120,canvas.height); ctx.lineTo(baseX+sway+120,canvas.height); ctx.closePath(); ctx.fill(); ctx.restore(); }

    function drawBackground(theme){ const g=ctx.createLinearGradient(0,0,0,canvas.height); if(theme==='boss'){ g.addColorStop(0,'#42234d'); g.addColorStop(.45,'#24143f'); g.addColorStop(1,'#101220'); } else if(theme==='rooftop'){ g.addColorStop(0,'#3f63d9'); g.addColorStop(.42,'#203b80'); g.addColorStop(1,'#0e1838'); } else { g.addColorStop(0,'#4d78ee'); g.addColorStop(.45,'#274893'); g.addColorStop(1,'#101b40'); } ctx.fillStyle=g; ctx.fillRect(0,0,canvas.width,canvas.height); ctx.fillStyle = theme==='boss' ? '#ffbe79' : '#fff0b0'; ctx.beginPath(); ctx.arc(canvas.width-120,90,44,0,Math.PI*2); ctx.fill(); if(theme!=='boss'){ for(let i=0;i<14;i++){ const x=((i*210)-state.cameraX*.14)%(canvas.width+220)-110; const y=70+(i%4)*22; drawCloud(x,y,.82+(i%3)*.16,.82); } } const layers = theme==='boss' ? [{speed:.16,base:410,color:'#2d1f46',alpha:.48,windows:'rgba(255,146,103,.10)'},{speed:.28,base:470,color:'#1b1435',alpha:.8,windows:'rgba(255,164,96,.12)'}] : theme==='rooftop' ? [{speed:.16,base:410,color:'#223b76',alpha:.42,windows:'rgba(201,223,255,.12)'},{speed:.28,base:470,color:'#142750',alpha:.78,windows:'rgba(255,231,156,.17)'}] : [{speed:.16,base:410,color:'#223b76',alpha:.45,windows:'rgba(255,230,150,.18)'},{speed:.28,base:470,color:'#172a5b',alpha:.72,windows:'rgba(255,230,150,.18)'}]; for(const layer of layers){ ctx.save(); ctx.globalAlpha=layer.alpha; ctx.fillStyle=layer.color; for(let i=-2;i<18;i++){ const x=i*170-(state.cameraX*layer.speed%170); const w=92+(i*19%55); const h=130+(i*37%200); ctx.fillRect(x,layer.base-h,w,h); drawWindowGrid(x,layer.base,w,h,layer.windows); ctx.fillStyle=layer.color; } ctx.restore(); } if(theme==='port'){ const waterY=545; const waterGrad=ctx.createLinearGradient(0,waterY,0,canvas.height); waterGrad.addColorStop(0,'rgba(41,94,182,.22)'); waterGrad.addColorStop(1,'rgba(7,21,54,.45)'); ctx.fillStyle=waterGrad; ctx.fillRect(0,waterY,canvas.width,canvas.height-waterY); ctx.strokeStyle='rgba(175,213,255,.18)'; ctx.lineWidth=2; for(let i=0;i<7;i++){ ctx.beginPath(); const yy=waterY+18+i*18; ctx.moveTo(0,yy+Math.sin(tick*2+i)*2); for(let x=0;x<=canvas.width;x+=28){ ctx.lineTo(x,yy+Math.sin(tick*2+i+x*.02)*2); } ctx.stroke(); } } if(theme==='rooftop'){ drawSearchLight(); drawRain(); for(let i=0;i<4;i++){ const sx=180+i*260-(state.cameraX*.22%140); const sy=190+(i%2)*25; ctx.save(); ctx.translate(sx,sy); ctx.strokeStyle='rgba(255,255,255,.2)'; ctx.lineWidth=3; ctx.beginPath(); ctx.moveTo(0,0); ctx.lineTo(70,-18); ctx.lineTo(120,20); ctx.stroke(); ctx.restore(); } } }

    function drawGroundDecor(theme){ const sirenX=currentLevel().width-430-state.cameraX; if(sirenX>-180 && sirenX<canvas.width+160) drawPoliceCar(sirenX,548); if(theme==='port'){ const craneX=260-state.cameraX*.45; if(craneX>-240 && craneX<canvas.width+120){ ctx.save(); ctx.translate(craneX,250); ctx.fillStyle='rgba(27,46,90,.55)'; ctx.fillRect(0,0,16,270); ctx.fillRect(0,0,180,12); ctx.fillRect(130,12,12,90); ctx.fillRect(118,94,34,10); ctx.restore(); } } if(theme==='boss'){ const fx=2480-state.cameraX; if(fx>-200 && fx<canvas.width+200) drawFence(fx,548); const fireX=2140-state.cameraX; if(fireX>-100 && fireX<canvas.width+100) drawFireBarrel(fireX,553); } }
    function drawFence(x,y){ ctx.save(); ctx.translate(x,y); ctx.fillStyle='#b5c6e4'; for(let i=0;i<7;i++){ ctx.fillRect(i*18,0,8,56); ctx.beginPath(); ctx.moveTo(i*18,0); ctx.lineTo(i*18+4,-10); ctx.lineTo(i*18+8,0); ctx.fill(); } ctx.fillRect(0,16,120,8); ctx.fillRect(0,34,120,8); ctx.restore(); }
    function drawFireBarrel(x,y){ ctx.save(); ctx.translate(x,y); ctx.fillStyle='#3c455d'; roundedRect(0,0,34,46,6,true,false); ctx.fillStyle='#2a3042'; ctx.fillRect(0,10,34,4); ctx.fillRect(0,30,34,4); const f=Math.sin(tick*8); ctx.fillStyle='#ff9e42'; ctx.beginPath(); ctx.moveTo(10,4); ctx.quadraticCurveTo(17+f*2,-20,24,5); ctx.quadraticCurveTo(22,0,16,-8); ctx.quadraticCurveTo(12+f,-2,10,4); ctx.fill(); ctx.restore(); }
    function drawPlatform(p){ const x=p.x-state.cameraX; if(x+p.w<-100 || x>canvas.width+100) return; if(p.type==='ground'){ ctx.fillStyle='#40506e'; ctx.fillRect(x,p.y,p.w,p.h); ctx.fillStyle='#50627f'; for(let i=0;i<p.w;i+=90) ctx.fillRect(x+i,p.y+20,60,6); ctx.fillStyle='#2dd27c'; ctx.fillRect(x,p.y-10,p.w,12); ctx.fillStyle='#60ef9d'; for(let i=0;i<p.w;i+=26){ ctx.beginPath(); ctx.moveTo(x+i,p.y-4); ctx.lineTo(x+i+7,p.y-18); ctx.lineTo(x+i+14,p.y-4); ctx.fill(); } } else { const grad=ctx.createLinearGradient(x,p.y,x,p.y+p.h); grad.addColorStop(0,'#ffc76f'); grad.addColorStop(1,'#da8843'); ctx.fillStyle=grad; roundedRect(x,p.y,p.w,p.h,14,true,false); ctx.fillStyle='#7b4927'; for(let i=18;i<p.w;i+=40) ctx.fillRect(x+i,p.y+4,4,p.h-8); ctx.fillRect(x,p.y+11,p.w,3); ctx.fillStyle='#39db81'; roundedRect(x,p.y-12,p.w,16,12,true,false); ctx.fillStyle='#8cffb4'; for(let i=0;i<p.w;i+=24){ ctx.beginPath(); ctx.moveTo(x+i,p.y-3); ctx.lineTo(x+i+7,p.y-16); ctx.lineTo(x+i+14,p.y-3); ctx.fill(); } } }
    function drawBadge(b){ if(b.taken) return; const x=b.x-state.cameraX; const y=b.y+Math.sin(tick*4+b.x*.02)*6; if(x<-50 || x>canvas.width+50) return; ctx.save(); ctx.translate(x,y); ctx.fillStyle='#ffd85b'; ctx.beginPath(); for(let i=0;i<10;i++){ const angle=Math.PI/5*i-Math.PI/2; const r=i%2===0?20:9; const px=Math.cos(angle)*r; const py=Math.sin(angle)*r; if(i===0) ctx.moveTo(px,py); else ctx.lineTo(px,py); } ctx.closePath(); ctx.fill(); ctx.fillStyle='#20407b'; ctx.beginPath(); ctx.arc(0,0,8,0,Math.PI*2); ctx.fill(); ctx.fillStyle='#ffffff'; ctx.font='bold 14px Arial'; ctx.textAlign='center'; ctx.fillText('★',0,5); ctx.restore(); ctx.textAlign='left'; }
    function drawExit(){ const ex=currentLevel().exit.x-state.cameraX; const ey=currentLevel().exit.y; const unlocked=currentLevel().badges.every(b=>b.taken)&&(!currentLevel().boss || currentLevel().boss.currentLife<=0); const glow=unlocked?'#7bffbe':'#ff7f87'; ctx.fillStyle=glow; roundedRect(ex-5,ey-5,currentLevel().exit.w+10,currentLevel().exit.h+10,20,true,false); const g=ctx.createLinearGradient(ex,ey,ex,ey+currentLevel().exit.h); g.addColorStop(0,'#e7eefc'); g.addColorStop(1,'#b5c2dc'); ctx.fillStyle=g; roundedRect(ex,ey,currentLevel().exit.w,currentLevel().exit.h,18,true,false); ctx.fillStyle='#355188'; roundedRect(ex+10,ey+12,currentLevel().exit.w-20,currentLevel().exit.h-24,12,true,false); ctx.fillStyle=unlocked?'#3be38a':'#ff5d73'; roundedRect(ex+10,ey-18,currentLevel().exit.w-20,24,12,true,false); ctx.fillStyle='white'; ctx.font='bold 20px Arial'; ctx.textAlign='center'; ctx.fillText('EXIT',ex+currentLevel().exit.w/2,ey-1); ctx.textAlign='left'; }
    function drawPoliceCar(x,y){ ctx.save(); ctx.translate(x,y); const flash=Math.floor(tick*12)%2===0; const g=ctx.createLinearGradient(0,0,0,44); g.addColorStop(0,'#f9fcff'); g.addColorStop(1,'#d8e4f7'); ctx.fillStyle=g; roundedRect(0,0,150,44,18,true,false); roundedRect(24,-28,70,34,16,true,false); ctx.fillStyle='#8fe3ff'; roundedRect(35,-21,48,20,10,true,false); ctx.fillStyle='#27417a'; ctx.fillRect(0,24,150,14); ctx.fillStyle=flash?'#ff5d73':'#7f3642'; roundedRect(58,-38,16,12,6,true,false); ctx.fillStyle=flash?'#55bbff':'#2e557a'; roundedRect(75,-38,16,12,6,true,false); ctx.fillStyle='rgba(255,70,95,.09)'; if(flash){ ctx.beginPath(); ctx.moveTo(72,-38); ctx.lineTo(-15,-82); ctx.lineTo(18,10); ctx.closePath(); ctx.fill(); ctx.fillStyle='rgba(85,187,255,.08)'; ctx.beginPath(); ctx.moveTo(82,-38); ctx.lineTo(170,-82); ctx.lineTo(128,10); ctx.closePath(); ctx.fill(); } ctx.fillStyle='#252a34'; ctx.beginPath(); ctx.arc(34,42,18,0,Math.PI*2); ctx.arc(118,42,18,0,Math.PI*2); ctx.fill(); ctx.fillStyle='#95a4bb'; ctx.beginPath(); ctx.arc(34,42,9,0,Math.PI*2); ctx.arc(118,42,9,0,Math.PI*2); ctx.fill(); ctx.restore(); }
    function drawPlayer(){ if(player.invuln>0 && Math.floor(player.invuln/6)%2===0) return; const x=player.x-state.cameraX; const y=player.y; const walk=Math.sin(player.anim)*5; ctx.save(); ctx.translate(x,y); if(player.facing<0){ ctx.scale(-1,1); ctx.translate(-player.w,0); } ctx.fillStyle='rgba(0,0,0,.18)'; ctx.beginPath(); ctx.ellipse(player.w/2,player.h+2,18,6,0,0,Math.PI*2); ctx.fill(); ctx.fillStyle='#223466'; roundedRect(12,45+Math.max(0,walk*.18),10,25,5,true,false); roundedRect(30,45+Math.max(0,-walk*.18),10,25,5,true,false); ctx.fillStyle='#161a22'; roundedRect(10,66+Math.max(0,walk*.18),14,8,4,true,false); roundedRect(28,66+Math.max(0,-walk*.18),14,8,4,true,false); const g=ctx.createLinearGradient(0,18,0,48); g.addColorStop(0,'#5dc0ff'); g.addColorStop(1,'#2470db'); ctx.fillStyle=g; roundedRect(10,18,32,30,10,true,false); ctx.fillStyle='#f3c845'; roundedRect(23,20,6,18,3,true,false); ctx.fillStyle='#1d2f62'; roundedRect(10,16,32,8,6,true,false); ctx.fillStyle='#2d73df'; roundedRect(5,24,9,20,5,true,false); roundedRect(38,24,9,20,5,true,false); ctx.fillStyle='#f4c99a'; roundedRect(6,40,8,8,4,true,false); roundedRect(38,40,8,8,4,true,false); ctx.fillStyle='#f4c99a'; roundedRect(13,4,26,20,9,true,false); ctx.fillStyle='#20305e'; roundedRect(10,0,32,11,8,true,false); ctx.fillStyle='#5cb5ff'; roundedRect(14,5,24,5,4,true,false); ctx.fillStyle='#111'; ctx.beginPath(); ctx.arc(22,14,2,0,Math.PI*2); ctx.arc(31,14,2,0,Math.PI*2); ctx.fill(); ctx.strokeStyle='#a56c57'; ctx.lineWidth=2; ctx.beginPath(); ctx.arc(26,18,5,0,.9*Math.PI); ctx.stroke(); if(player.attackFlash>0){ ctx.strokeStyle='#ffd85b'; ctx.lineWidth=4; ctx.beginPath(); ctx.arc(26,34,26+player.attackFlash,0,Math.PI*2); ctx.stroke(); } ctx.restore(); }
    function drawEnemy(e){ if(e.dead) return; const x=e.x-state.cameraX; const y=e.y+Math.sin(tick*4+e.phase)*2.5; if(x<-120 || x>canvas.width+120) return; ctx.save(); ctx.translate(x,y); ctx.fillStyle='rgba(0,0,0,.18)'; ctx.beginPath(); ctx.ellipse(e.w/2,e.h+2,19,6,0,0,Math.PI*2); ctx.fill(); ctx.fillStyle=e.type==='agent' ? '#202238' : '#281723'; roundedRect(11,36,12,23,6,true,false); roundedRect(31,36,12,23,6,true,false); ctx.fillStyle='#17171b'; roundedRect(8,56,16,7,4,true,false); roundedRect(30,56,16,7,4,true,false); const g=ctx.createLinearGradient(0,16,0,44); if(e.type==='agent'){ g.addColorStop(0,'#918dff'); g.addColorStop(1,'#4c44d7'); } else { g.addColorStop(0,'#ff8277'); g.addColorStop(1,'#cb3348'); } ctx.fillStyle=g; roundedRect(10,16,34,26,11,true,false); ctx.fillStyle=e.type==='agent' ? '#38326f' : '#842131'; roundedRect(8,23,8,15,4,true,false); roundedRect(38,23,8,15,4,true,false); ctx.fillStyle='#f2c59e'; roundedRect(13,2,28,20,10,true,false); ctx.fillStyle='#1a1a1f'; roundedRect(10,8,34,8,5,true,false); ctx.fillStyle='#ff6572'; ctx.beginPath(); ctx.arc(22,12,2.2,0,Math.PI*2); ctx.arc(33,12,2.2,0,Math.PI*2); ctx.fill(); ctx.restore(); }
    function drawBoss(boss){ if(boss.currentLife<=0) return; const x=boss.x-state.cameraX; const y=boss.y+Math.sin(tick*3)*1.5; if(x<-200 || x>canvas.width+200) return; ctx.save(); ctx.translate(x,y); ctx.fillStyle='rgba(0,0,0,.22)'; ctx.beginPath(); ctx.ellipse(boss.w/2,boss.h+4,36,10,0,0,Math.PI*2); ctx.fill(); ctx.fillStyle='#2d2130'; roundedRect(18,72,26,56,8,true,false); roundedRect(94,72,26,56,8,true,false); ctx.fillStyle='#18181d'; roundedRect(14,121,34,10,5,true,false); roundedRect(92,121,34,10,5,true,false); const g=ctx.createLinearGradient(0,24,0,86); g.addColorStop(0,'#ff924e'); g.addColorStop(1,'#d2482d'); ctx.fillStyle=g; roundedRect(22,20,90,68,18,true,false); ctx.fillStyle='#763528'; roundedRect(4,32,20,48,8,true,false); roundedRect(110,32,20,48,8,true,false); ctx.fillStyle='#f4c99a'; roundedRect(30,0,72,44,18,true,false); ctx.fillStyle='#2f263d'; roundedRect(24,12,84,15,8,true,false); ctx.fillStyle='#ff5d73'; ctx.beginPath(); ctx.arc(49,20,5,0,Math.PI*2); ctx.arc(82,20,5,0,Math.PI*2); ctx.fill(); ctx.strokeStyle='#8d523c'; ctx.lineWidth=3; ctx.beginPath(); ctx.arc(65,31,13,0,Math.PI); ctx.stroke(); const hpW=128; ctx.fillStyle='rgba(0,0,0,.4)'; roundedRect(0,-24,hpW,12,6,true,false); ctx.fillStyle='#ff6278'; roundedRect(0,-24,hpW*(boss.currentLife/boss.life),12,6,true,false); ctx.restore(); }
    function drawBossShots(){ for(const s of state.bossShots){ ctx.save(); ctx.translate(s.x-state.cameraX,s.y); const grad=ctx.createRadialGradient(0,0,2,0,0,s.r+2); grad.addColorStop(0,'#ffd56b'); grad.addColorStop(.45,'#ff8b4a'); grad.addColorStop(1,'rgba(255,96,72,0)'); ctx.fillStyle=grad; ctx.beginPath(); ctx.arc(0,0,s.r+4,0,Math.PI*2); ctx.fill(); ctx.fillStyle='#ff7548'; ctx.beginPath(); ctx.arc(0,0,s.r,0,Math.PI*2); ctx.fill(); ctx.restore(); } }
    function drawParticles(){ for(const p of particles){ ctx.globalAlpha=Math.max(0,p.life/42); ctx.fillStyle=p.color; ctx.beginPath(); ctx.arc(p.x-state.cameraX,p.y,p.size,0,Math.PI*2); ctx.fill(); } ctx.globalAlpha=1; }

    function drawOverlay(){
      const lvl=currentLevel();
      const compact = canvas.width < 900;
      if (!compact) {
        ctx.fillStyle='rgba(10,15,34,.42)';
        roundedRect(18,16,255,72,18,true,false);
        ctx.fillStyle='#ffffff';
        ctx.font='bold 24px Arial';
        ctx.fillText(`VITE ${state.lives}`,34,45);
        ctx.fillText(`DIST. ${lvl.badges.filter(b=>b.taken).length}/${lvl.badges.length}`,34,74);
        const unlocked=lvl.badges.every(b=>b.taken)&&(!lvl.boss || lvl.boss.currentLife<=0);
        const text=state.won ? 'Missione completata' : unlocked ? 'Uscita sbloccata. Vai.' : lvl.boss ? 'Raccogli i distintivi e abbatti il boss' : 'Raccogli tutti i distintivi';
        ctx.fillStyle='rgba(10,15,34,.42)';
        roundedRect(canvas.width/2-220,16,440,48,18,true,false);
        ctx.fillStyle='#e8efff';
        ctx.font='bold 22px Arial';
        ctx.textAlign='center';
        ctx.fillText(text,canvas.width/2,47);
        ctx.textAlign='left';
        if(state.levelIndex===2 && lvl.boss && lvl.boss.currentLife>0){ ctx.fillStyle='rgba(10,15,34,.42)'; roundedRect(canvas.width-250,16,220,48,18,true,false); ctx.fillStyle='#ffcad0'; ctx.font='bold 20px Arial'; ctx.fillText(`BOSS HP ${lvl.boss.currentLife}/${lvl.boss.life}`,canvas.width-225,47); }
      } else {
        ctx.fillStyle='rgba(10,15,34,.52)';
        roundedRect(10,10,168,34,14,true,false);
        ctx.fillStyle='#fff';
        ctx.font='bold 16px Arial';
        ctx.fillText(`❤ ${state.lives}   ★ ${lvl.badges.filter(b=>b.taken).length}/${lvl.badges.length}`,20,32);
        if(state.levelIndex===2 && lvl.boss && lvl.boss.currentLife>0){
          ctx.fillStyle='rgba(10,15,34,.52)';
          roundedRect(canvas.width-122,10,112,34,14,true,false);
          ctx.fillStyle='#ffcad0';
          ctx.font='bold 16px Arial';
          ctx.fillText(`BOSS ${lvl.boss.currentLife}`,canvas.width-108,32);
        }
      }
    }

    function drawWinScreen(){ ctx.fillStyle='rgba(6,10,18,.68)'; ctx.fillRect(0,0,canvas.width,canvas.height); const w=Math.min(860, canvas.width-24), h=Math.min(300, canvas.height-40); const x=canvas.width/2-w/2; const y=canvas.height/2-h/2; const g=ctx.createLinearGradient(x,y,x,y+h); g.addColorStop(0,'#3551a4'); g.addColorStop(1,'#1a2a56'); ctx.fillStyle=g; roundedRect(x,y,w,h,30,true,false); ctx.strokeStyle='rgba(255,255,255,.16)'; ctx.lineWidth=3; roundedRect(x,y,w,h,30,false,true); ctx.textAlign='center'; ctx.fillStyle='#ffd85b'; ctx.font=`900 ${Math.max(28, Math.min(58, w*0.07))}px Arial`; ctx.fillText('MISSIONE COMPIUTA',canvas.width/2,y+Math.min(78,h*.28)); ctx.fillStyle='#ffffff'; ctx.font=`900 ${Math.max(22, Math.min(62, w*0.06))}px Arial`; ctx.fillText('VUOI ESSERE IL MIO TESTIMONE?',canvas.width/2,y+Math.min(155,h*.53)); ctx.fillStyle='#dce7ff'; ctx.font=`bold ${Math.max(14, Math.min(28, w*0.032))}px Arial`; ctx.fillText('Dopo tutto questo, direi che ormai te lo sei meritato.',canvas.width/2,y+Math.min(212,h*.73)); ctx.fillStyle='#ffd85b'; ctx.font=`bold ${Math.max(13, Math.min(24, w*0.028))}px Arial`; ctx.fillText('Premi ↻ per ricominciare',canvas.width/2,y+Math.min(254,h*.86)); ctx.textAlign='left'; }

    function render(){ ctx.save(); if(state.shake>0) ctx.translate((Math.random()-.5)*state.shake,(Math.random()-.5)*state.shake); drawBackground(currentLevel().theme); drawGroundDecor(currentLevel().theme); for(const p of currentLevel().platforms) drawPlatform(p); for(const b of currentLevel().badges) drawBadge(b); for(const e of currentLevel().enemies) drawEnemy(e); if(currentLevel().boss) drawBoss(currentLevel().boss); drawBossShots(); drawExit(); drawPlayer(); drawParticles(); drawOverlay(); if(state.won) drawWinScreen(); ctx.restore(); }
    function gameLoop(){ updateGame(); render(); requestAnimationFrame(gameLoop); }

    function holdButton(id,onDown,onUp){ const el=document.getElementById(id); if(!el) return; const down=e=>{ e.preventDefault(); e.stopPropagation(); el.classList.add('active'); onDown(); }; const up=e=>{ e.preventDefault(); e.stopPropagation(); el.classList.remove('active'); onUp(); }; ['pointerdown','touchstart','mousedown','click'].forEach(evt=>el.addEventListener(evt, down, { passive:false })); ['pointerup','pointerleave','pointercancel','touchend','touchcancel','mouseup'].forEach(evt=>el.addEventListener(evt, up, { passive:false })); }

    document.addEventListener('keydown', e=>{ if(!state.started) { if (e.key === 'Enter' || e.key === ' ') { e.preventDefault(); startGame(); } return; } if (e.key === 'p' || e.key === 'P') { e.preventDefault(); togglePause(); return; } if(state.paused) return; if(['ArrowLeft','a','A'].includes(e.key)) keys.left=true; if(['ArrowRight','d','D'].includes(e.key)) keys.right=true; if(['ArrowUp','w','W',' '].includes(e.key)) { e.preventDefault(); keys.jump=true; } });
    document.addEventListener('keyup', e=>{ if(['ArrowLeft','a','A'].includes(e.key)) keys.left=false; if(['ArrowRight','d','D'].includes(e.key)) keys.right=false; });

    const leftDown=()=>{ if(state.started && !state.paused) keys.left=true; }, leftUp=()=>keys.left=false;
    const rightDown=()=>{ if(state.started && !state.paused) keys.right=true; }, rightUp=()=>keys.right=false;
    const jumpDown=()=>{ if(state.started && !state.paused) keys.jump=true; };

    holdButton('leftBtn',leftDown,leftUp); holdButton('rightBtn',rightDown,rightUp); holdButton('jumpBtn',jumpDown,()=>{}); holdButton('leftBtnOverlay',leftDown,leftUp); holdButton('rightBtnOverlay',rightDown,rightUp); holdButton('jumpBtnOverlay',jumpDown,()=>{});
    document.getElementById('restartBtn').addEventListener('click', ()=>{ beep(500,.06,'square',.02); restartGame(); });
    startBtn.addEventListener('click', e=>{ e.preventDefault(); e.stopPropagation(); startGame(); });
    skipIntroBtn.addEventListener('click', e=>{ e.preventDefault(); e.stopPropagation(); startGame(); });
    startBtn.addEventListener('touchend', e=>{ e.preventDefault(); e.stopPropagation(); startGame(); }, { passive:false });
    skipIntroBtn.addEventListener('touchend', e=>{ e.preventDefault(); e.stopPropagation(); startGame(); }, { passive:false });
    introScreen.addEventListener('click', e=>{ if (e.target === introScreen) startGame(); });
    introScreen.addEventListener('touchend', e=>{ if (e.target === introScreen) { e.preventDefault(); startGame(); } }, { passive:false });
    resumeBtn.addEventListener('click', ()=>togglePause());
    restartFromPauseBtn.addEventListener('click', ()=>restartGame());

    buildRain(); window.addEventListener('resize', buildRain);
    restartGame(); state.started=false; state.paused=false; introScreen.classList.remove('intro-hidden'); introScreen.style.pointerEvents='auto'; levelCard.classList.add('level-hidden'); pauseScreen.classList.add('pause-hidden'); gameLoop();
  </script>
</body>
</html>
