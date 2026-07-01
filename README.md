<!DOCTYPE html>
<html lang="hi">
<head>
  <meta name="google-site-verification" content="E8G7aip5KatJ0BRmGzxPg6o8TJ1K8acLmci8oKZdWec" />
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Speed Meter Test — Har Vehicle Ka</title>
<style>
  @import url('https://fonts.googleapis.com/css2?family=Rajdhani:wght@500;600;700&family=Chakra+Petch:wght@400;500;600;700&family=JetBrains+Mono:wght@500;700&display=swap');

  :root{
    --bg-deep:#0b0e13;
    --bg-panel:#12161d;
    --steel:#232a35;
    --steel-line:#333d4b;
    --text-main:#e7ecf2;
    --text-dim:#7c8798;
    --accent:#ff6a2b;
    --accent-glow:rgba(255,106,43,0.45);
    --good:#3ddc84;
    --warn:#ffcf4d;
    --danger:#ff4d5e;
  }

  *{box-sizing:border-box;}
  html,body{margin:0;padding:0;}
  body{
    background:
      radial-gradient(1200px 600px at 50% -10%, #171d26 0%, var(--bg-deep) 60%),
      var(--bg-deep);
    color:var(--text-main);
    font-family:'Chakra Petch', sans-serif;
    min-height:100vh;
    padding:28px 16px 60px;
    display:flex;
    flex-direction:column;
    align-items:center;
  }

  /* ---------- Header ---------- */
  .masthead{
    width:100%;
    max-width:980px;
    display:flex;
    justify-content:space-between;
    align-items:flex-end;
    margin-bottom:22px;
    border-bottom:1px solid var(--steel-line);
    padding-bottom:14px;
    gap:12px;
    flex-wrap:wrap;
  }
  .masthead .eyebrow{
    font-family:'JetBrains Mono', monospace;
    font-size:11px;
    letter-spacing:0.22em;
    color:var(--accent);
    text-transform:uppercase;
    margin-bottom:6px;
  }
  .masthead h1{
    font-family:'Rajdhani', sans-serif;
    font-weight:700;
    font-size:clamp(28px,5vw,42px);
    letter-spacing:0.01em;
    margin:0;
    line-height:1;
  }
  .masthead .sub{
    color:var(--text-dim);
    font-size:14px;
    margin-top:6px;
    max-width:520px;
  }
  .odometer-mini{
    font-family:'JetBrains Mono', monospace;
    text-align:right;
    color:var(--text-dim);
    font-size:12px;
  }
  .odometer-mini b{
    display:block;
    color:var(--text-main);
    font-size:20px;
    letter-spacing:0.05em;
  }

  /* ---------- Layout ---------- */
  .rig{
    width:100%;
    max-width:980px;
    display:grid;
    grid-template-columns:1fr;
    gap:20px;
  }

  /* ---------- Vehicle picker ---------- */
  .vehicle-strip{
    display:flex;
    gap:10px;
    overflow-x:auto;
    padding-bottom:4px;
  }
  .vcard{
    flex:0 0 auto;
    background:var(--bg-panel);
    border:1px solid var(--steel-line);
    border-radius:10px;
    padding:12px 16px;
    cursor:pointer;
    min-width:128px;
    transition:border-color .15s ease, transform .12s ease, background .15s ease;
    user-select:none;
  }
  .vcard:hover{ transform:translateY(-2px); border-color:#4a5568; }
  .vcard.active{
    border-color:var(--accent);
    background:linear-gradient(180deg, rgba(255,106,43,0.10), rgba(255,106,43,0.02));
    box-shadow:0 0 0 1px var(--accent) inset;
  }
  .vcard .icon{ font-size:26px; line-height:1; margin-bottom:6px; }
  .vcard .name{ font-family:'Rajdhani',sans-serif; font-weight:700; font-size:16px; }
  .vcard .top{ font-family:'JetBrains Mono', monospace; font-size:11px; color:var(--text-dim); margin-top:2px;}

  /* ---------- Dashboard ---------- */
  .dashboard{
    background:linear-gradient(180deg, #151a22, #0f131a);
    border:1px solid var(--steel-line);
    border-radius:18px;
    padding:26px clamp(14px,4vw,40px) 20px;
    display:grid;
    grid-template-columns:1fr;
    gap:18px;
    position:relative;
    overflow:hidden;
  }
  .dashboard::before{
    content:"";
    position:absolute; inset:0;
    background:
      repeating-linear-gradient(115deg, rgba(255,255,255,0.015) 0 2px, transparent 2px 26px);
    pointer-events:none;
  }

  .gauge-row{
    display:flex;
    justify-content:center;
    align-items:center;
    position:relative;
  }

  .readout{
    position:absolute;
    text-align:center;
    top:58%;
    left:50%;
    transform:translate(-50%,-50%);
    pointer-events:none;
  }
  .readout .num{
    font-family:'JetBrains Mono', monospace;
    font-weight:700;
    font-size:clamp(40px,9vw,64px);
    letter-spacing:0.01em;
    color:var(--text-main);
    text-shadow:0 0 18px rgba(255,255,255,0.06);
  }
  .readout .unit{
    font-family:'JetBrains Mono', monospace;
    font-size:13px;
    color:var(--text-dim);
    letter-spacing:0.18em;
    margin-top:-4px;
  }

  /* ---------- Controls ---------- */
  .controls{
    display:flex;
    justify-content:center;
    gap:14px;
    flex-wrap:wrap;
  }
  .btn{
    font-family:'Rajdhani', sans-serif;
    font-weight:700;
    font-size:16px;
    letter-spacing:0.04em;
    border-radius:10px;
    padding:14px 26px;
    border:1px solid var(--steel-line);
    background:var(--steel);
    color:var(--text-main);
    cursor:pointer;
    -webkit-user-select:none;
    user-select:none;
    touch-action:none;
    transition:transform .08s ease, box-shadow .12s ease, border-color .12s ease;
    min-width:150px;
    text-align:center;
  }
  .btn small{ display:block; font-family:'JetBrains Mono',monospace; font-weight:500; font-size:10px; color:var(--text-dim); letter-spacing:0.1em; margin-top:2px;}
  .btn:active{ transform:scale(0.97); }
  .btn.accel{ border-color:rgba(61,220,132,0.4); }
  .btn.accel.pressed{ background:linear-gradient(180deg, rgba(61,220,132,0.25), rgba(61,220,132,0.05)); box-shadow:0 0 22px rgba(61,220,132,0.25); border-color:var(--good); }
  .btn.brake{ border-color:rgba(255,77,94,0.4); }
  .btn.brake.pressed{ background:linear-gradient(180deg, rgba(255,77,94,0.25), rgba(255,77,94,0.05)); box-shadow:0 0 22px rgba(255,77,94,0.25); border-color:var(--danger); }
  .btn.reset{ border-color:var(--steel-line); }
  .btn.reset:hover{ border-color:var(--accent); }

  .hint{
    text-align:center;
    font-family:'JetBrains Mono', monospace;
    font-size:11px;
    color:var(--text-dim);
    letter-spacing:0.05em;
  }

  /* ---------- Stats strip ---------- */
  .stats{
    display:grid;
    grid-template-columns:repeat(auto-fit, minmax(130px,1fr));
    gap:12px;
  }
  .stat{
    background:var(--bg-panel);
    border:1px solid var(--steel-line);
    border-radius:10px;
    padding:12px 14px;
  }
  .stat .label{
    font-family:'JetBrains Mono', monospace;
    font-size:10px;
    letter-spacing:0.12em;
    color:var(--text-dim);
    text-transform:uppercase;
  }
  .stat .val{
    font-family:'Rajdhani', sans-serif;
    font-weight:700;
    font-size:22px;
    margin-top:4px;
  }
  .stat.hero .val{ color:var(--accent); }

  footer{
    margin-top:24px;
    color:var(--text-dim);
    font-family:'JetBrains Mono', monospace;
    font-size:11px;
    text-align:center;
    max-width:600px;
  }

  @media (prefers-reduced-motion: reduce){
    * { transition:none !important; }
  }

  /* ---------- Nav ---------- */
  .nav{
    position:sticky; top:0; z-index:50;
    width:100%;
    background:rgba(11,14,19,0.86);
    backdrop-filter:blur(10px);
    border-bottom:1px solid var(--steel-line);
  }
  .nav-inner{
    max-width:1100px; margin:0 auto;
    display:flex; align-items:center; justify-content:space-between;
    padding:14px 20px;
  }
  .logo{
    display:flex; align-items:center; gap:9px;
    font-family:'Rajdhani',sans-serif; font-weight:700; font-size:19px; letter-spacing:0.02em;
  }
  .logo .dot{ width:10px; height:10px; border-radius:50%; background:var(--accent); box-shadow:0 0 10px var(--accent-glow); }
  .nav-links{ display:flex; gap:26px; list-style:none; margin:0; padding:0; }
  .nav-links a{
    color:var(--text-dim); text-decoration:none; font-size:14px; font-weight:500;
    font-family:'Chakra Petch',sans-serif; letter-spacing:0.02em;
    transition:color .15s ease;
  }
  .nav-links a:hover{ color:var(--text-main); }
  .nav-cta{
    font-family:'Rajdhani',sans-serif; font-weight:700; font-size:13px; letter-spacing:0.04em;
    background:var(--accent); color:#1a0e08; border:none; border-radius:8px;
    padding:9px 16px; cursor:pointer; text-decoration:none;
  }
  @media (max-width:760px){
    .nav-links{ display:none; }
  }

  /* ---------- Hero ---------- */
  .hero{
    width:100%; max-width:1100px; margin:0 auto;
    padding:64px 20px 40px;
    display:grid; grid-template-columns:1.1fr 0.9fr; gap:40px; align-items:center;
  }
  @media (max-width:820px){ .hero{ grid-template-columns:1fr; padding-top:44px; } }
  .hero .eyebrow{
    font-family:'JetBrains Mono',monospace; font-size:11px; letter-spacing:0.22em;
    color:var(--accent); text-transform:uppercase; margin-bottom:14px;
  }
  .hero h1{
    font-family:'Rajdhani',sans-serif; font-weight:700; font-size:clamp(34px,6vw,58px);
    line-height:1.02; margin:0 0 16px;
  }
  .hero h1 span{ color:var(--accent); }
  .hero p{ color:var(--text-dim); font-size:16px; line-height:1.6; max-width:460px; margin:0 0 26px; }
  .hero-ctas{ display:flex; gap:12px; flex-wrap:wrap; }
  .hero-ctas .primary{
    background:var(--accent); color:#1a0e08; border:none; border-radius:10px;
    font-family:'Rajdhani',sans-serif; font-weight:700; font-size:15px; letter-spacing:0.03em;
    padding:13px 24px; cursor:pointer; text-decoration:none; display:inline-block;
  }
  .hero-ctas .secondary{
    background:transparent; color:var(--text-main); border:1px solid var(--steel-line); border-radius:10px;
    font-family:'Rajdhani',sans-serif; font-weight:700; font-size:15px; letter-spacing:0.03em;
    padding:13px 24px; cursor:pointer; text-decoration:none; display:inline-block;
  }
  .hero-stats{ display:flex; gap:28px; margin-top:34px; flex-wrap:wrap; }
  .hero-stats div .n{ font-family:'JetBrains Mono',monospace; font-weight:700; font-size:24px; color:var(--text-main); }
  .hero-stats div .l{ font-family:'JetBrains Mono',monospace; font-size:10px; color:var(--text-dim); letter-spacing:0.1em; text-transform:uppercase; }

  .hero-art{
    background:linear-gradient(180deg,#151a22,#0f131a);
    border:1px solid var(--steel-line); border-radius:18px;
    padding:26px; display:flex; flex-direction:column; align-items:center; justify-content:center; gap:10px;
    min-height:220px;
  }
  .hero-art .mini-icons{ font-size:30px; letter-spacing:10px; }
  .hero-art .cap{ font-family:'JetBrains Mono',monospace; font-size:11px; color:var(--text-dim); text-align:center; letter-spacing:0.08em; }

  /* ---------- Section shell ---------- */
  section.block{ width:100%; max-width:1100px; margin:0 auto; padding:54px 20px; }
  .block-head{ text-align:center; max-width:560px; margin:0 auto 34px; }
  .block-head .eyebrow{
    font-family:'JetBrains Mono',monospace; font-size:11px; letter-spacing:0.2em;
    color:var(--accent); text-transform:uppercase; margin-bottom:10px;
  }
  .block-head h2{ font-family:'Rajdhani',sans-serif; font-weight:700; font-size:clamp(24px,4vw,34px); margin:0 0 10px; }
  .block-head p{ color:var(--text-dim); font-size:15px; line-height:1.6; }

  /* ---------- Features ---------- */
  .features{ display:grid; grid-template-columns:repeat(auto-fit,minmax(230px,1fr)); gap:16px; }
  .feature{
    background:var(--bg-panel); border:1px solid var(--steel-line); border-radius:14px; padding:22px 20px;
    transition:border-color .15s ease, transform .12s ease;
  }
  .feature:hover{ border-color:#4a5568; transform:translateY(-3px); }
  .feature .fi{ font-size:24px; margin-bottom:12px; }
  .feature h3{ font-family:'Rajdhani',sans-serif; font-weight:700; font-size:17px; margin:0 0 8px; }
  .feature p{ color:var(--text-dim); font-size:13.5px; line-height:1.55; margin:0; }

  /* ---------- How it works ---------- */
  .steps{ display:grid; grid-template-columns:repeat(auto-fit,minmax(220px,1fr)); gap:18px; counter-reset:step; }
  .step{ position:relative; padding:20px 18px 20px 54px; background:var(--bg-panel); border:1px solid var(--steel-line); border-radius:14px; }
  .step::before{
    counter-increment:step; content:counter(step);
    position:absolute; left:16px; top:18px;
    font-family:'JetBrains Mono',monospace; font-weight:700; font-size:14px;
    color:var(--accent); border:1px solid var(--accent); border-radius:50%;
    width:26px; height:26px; display:flex; align-items:center; justify-content:center;
  }
  .step h3{ font-family:'Rajdhani',sans-serif; font-weight:700; font-size:16px; margin:0 0 6px; }
  .step p{ color:var(--text-dim); font-size:13.5px; margin:0; line-height:1.5; }

  /* ---------- Compare table ---------- */
  .compare-wrap{ overflow-x:auto; border:1px solid var(--steel-line); border-radius:14px; }
  table.compare{ width:100%; border-collapse:collapse; min-width:560px; font-family:'Chakra Petch',sans-serif; }
  table.compare th, table.compare td{ padding:12px 16px; text-align:left; font-size:13.5px; border-bottom:1px solid var(--steel-line); }
  table.compare th{
    background:var(--bg-panel); color:var(--text-dim); font-family:'JetBrains Mono',monospace;
    font-size:10.5px; letter-spacing:0.08em; text-transform:uppercase; font-weight:500;
  }
  table.compare tr:last-child td{ border-bottom:none; }
  table.compare td.icon-cell{ font-size:18px; }

  /* ---------- FAQ ---------- */
  .faq{ display:flex; flex-direction:column; gap:10px; }
  .faq-item{ background:var(--bg-panel); border:1px solid var(--steel-line); border-radius:12px; overflow:hidden; }
  .faq-q{
    width:100%; background:none; border:none; text-align:left; cursor:pointer;
    padding:16px 20px; display:flex; justify-content:space-between; align-items:center;
    color:var(--text-main); font-family:'Rajdhani',sans-serif; font-weight:700; font-size:15px;
  }
  .faq-q .plus{ font-family:'JetBrains Mono',monospace; color:var(--accent); font-size:18px; transition:transform .2s ease; }
  .faq-item.open .faq-q .plus{ transform:rotate(45deg); }
  .faq-a{ max-height:0; overflow:hidden; transition:max-height .25s ease; }
  .faq-item.open .faq-a{ max-height:200px; }
  .faq-a p{ margin:0; padding:0 20px 18px; color:var(--text-dim); font-size:13.5px; line-height:1.6; }

  /* ---------- CTA band ---------- */
  .cta-band{
    width:100%; max-width:1100px; margin:0 auto 54px; padding:40px 30px;
    background:linear-gradient(135deg, rgba(255,106,43,0.14), rgba(255,106,43,0.02));
    border:1px solid rgba(255,106,43,0.35); border-radius:18px;
    display:flex; align-items:center; justify-content:space-between; gap:20px; flex-wrap:wrap;
  }
  .cta-band h3{ font-family:'Rajdhani',sans-serif; font-weight:700; font-size:22px; margin:0 0 6px; }
  .cta-band p{ color:var(--text-dim); font-size:14px; margin:0; }

  /* ---------- Footer ---------- */
  .site-footer{
    width:100%; border-top:1px solid var(--steel-line); margin-top:20px;
    background:var(--bg-panel);
  }
  .footer-inner{
    max-width:1100px; margin:0 auto; padding:44px 20px 24px;
    display:grid; grid-template-columns:1.4fr repeat(3,1fr); gap:28px;
  }
  @media (max-width:720px){ .footer-inner{ grid-template-columns:1fr 1fr; } }
  .footer-brand .logo{ margin-bottom:10px; }
  .footer-brand p{ color:var(--text-dim); font-size:13px; line-height:1.6; max-width:260px; }
  .footer-col h4{ font-family:'JetBrains Mono',monospace; font-size:11px; letter-spacing:0.1em; color:var(--text-dim); text-transform:uppercase; margin:0 0 12px; }
  .footer-col ul{ list-style:none; margin:0; padding:0; display:flex; flex-direction:column; gap:9px; }
  .footer-col a{ color:var(--text-main); text-decoration:none; font-size:13.5px; }
  .footer-col a:hover{ color:var(--accent); }
  .footer-social{ display:flex; gap:10px; margin-top:14px; }
  .footer-social a{
    width:32px; height:32px; border:1px solid var(--steel-line); border-radius:8px;
    display:flex; align-items:center; justify-content:center; color:var(--text-dim); text-decoration:none; font-size:14px;
  }
  .footer-social a:hover{ border-color:var(--accent); color:var(--accent); }
  .footer-bottom{
    max-width:1100px; margin:0 auto; padding:18px 20px 30px;
    display:flex; justify-content:space-between; flex-wrap:wrap; gap:10px;
    border-top:1px solid var(--steel-line);
    font-family:'JetBrains Mono',monospace; font-size:11px; color:var(--text-dim);
  }
  .footer-bottom a{ color:var(--text-dim); text-decoration:none; margin-left:14px; }
  .footer-bottom a:hover{ color:var(--text-main); }

  section[id]{ scroll-margin-top:70px; }
</style>
</head>
<body>

  <nav class="nav">
    <div class="nav-inner">
      <div class="logo"><span class="dot"></span>VELOCITY RIG</div>
      <ul class="nav-links">
        <li><a href="#tool">Test Bench</a></li>
        <li><a href="#features">Features</a></li>
        <li><a href="#how">How it works</a></li>
        <li><a href="#compare">Compare</a></li>
        <li><a href="#faq">FAQ</a></li>
      </ul>
      <a class="nav-cta" href="#tool">Start Test</a>
    </div>
  </nav>

  <section class="hero">
    <div>
      <div class="eyebrow">Speed Meter Test Bench</div>
      <h1>Test any vehicle's <span>speedometer</span>, live.</h1>
      <p>Bicycle se sports car tak — har vehicle ka apna acceleration aur top-speed profile. Analog gauge, real-time needle, redline zone — sab kuch ek jagah.</p>
      <div class="hero-ctas">
        <a class="primary" href="#tool">Start Speed Test</a>
        <a class="secondary" href="#how">See How It Works</a>
      </div>
      <div class="hero-stats">
        <div><div class="n">6</div><div class="l">Vehicle Types</div></div>
        <div><div class="n">320</div><div class="l">Max KM/H Range</div></div>
        <div><div class="n">0ms</div><div class="l">Input Lag</div></div>
      </div>
    </div>
    <div class="hero-art">
      <div class="mini-icons">🚲 🛵 🏍️ 🚗 🏎️ 🚚</div>
      <div class="cap">EK RIG · CHHA VEHICLES · EK GAUGE</div>
    </div>
  </section>

  <div class="masthead" id="tool">
    <div>
      <div class="eyebrow">Speed Meter Test Bench</div>
      <h1>VELOCITY RIG</h1>
      <div class="sub">Vehicle chuno, accelerate/brake dabao, aur uska speedometer live test karo — bike se lekar truck tak.</div>
    </div>
    <div class="odometer-mini">SESSION MAX<b id="sessionMax">000</b>km/h</div>
  </div>

  <div class="rig">

    <div class="vehicle-strip" id="vehicleStrip"></div>

    <div class="dashboard">
      <div class="gauge-row">
        <svg id="gaugeSvg" viewBox="0 0 400 260" width="100%" style="max-width:440px;"></svg>
        <div class="readout">
          <div class="num" id="speedNum">0</div>
          <div class="unit">KM/H</div>
        </div>
      </div>

      <div class="controls">
        <button class="btn accel" id="accelBtn">ACCELERATE<small>hold / press ↑</small></button>
        <button class="btn brake" id="brakeBtn">BRAKE<small>hold / press ↓</small></button>
        <button class="btn reset" id="resetBtn">RESET<small>clear run</small></button>
      </div>

      <div class="hint">Mouse ya finger se button dabaye rakho, ya keyboard arrow keys use karo.</div>

      <div class="stats">
        <div class="stat">
          <div class="label">Vehicle</div>
          <div class="val" id="statVehicle">—</div>
        </div>
        <div class="stat hero">
          <div class="label">Top Speed</div>
          <div class="val" id="statTop">—</div>
        </div>
        <div class="stat">
          <div class="label">0→Top Time</div>
          <div class="val" id="statAccelTime">—</div>
        </div>
        <div class="stat">
          <div class="label">Status</div>
          <div class="val" id="statStatus">IDLE</div>
        </div>
      </div>
    </div>

  </div>

  <footer style="max-width:1100px; padding:0 20px; margin-top:20px;">
    Ye ek simulated speedometer test hai — physical vehicle speed test nahi. Har vehicle ka acceleration aur top-speed profile alag rakha gaya hai taaki test realistic lage.
  </footer>

  <section class="block" id="features">
    <div class="block-head">
      <div class="eyebrow">Kya Milta Hai</div>
      <h2>Har test ke liye zaroori features</h2>
      <p>Ek real dashboard jaisa feel, bina kisi physical hardware ke.</p>
    </div>
    <div class="features">
      <div class="feature">
        <div class="fi">⚡</div>
        <h3>Live Analog Gauge</h3>
        <p>Needle real-time move karti hai, exactly jaise asli speedometer mein hoti hai — smooth aur responsive.</p>
      </div>
      <div class="feature">
        <div class="fi">🚦</div>
        <h3>Redline Zone</h3>
        <p>Har vehicle ke top speed ke paas ek warning zone — pata chalega kab limit ke kareeb ho.</p>
      </div>
      <div class="feature">
        <div class="fi">⌨️</div>
        <h3>Keyboard + Touch</h3>
        <p>Arrow keys se ya button dabakar — mobile aur desktop dono pe smoothly kaam karta hai.</p>
      </div>
      <div class="feature">
        <div class="fi">📊</div>
        <h3>0→Top Timing</h3>
        <p>Zero se top speed tak pahunchne ka exact time measure hota hai, har run ke liye.</p>
      </div>
      <div class="feature">
        <div class="fi">🔁</div>
        <h3>Instant Vehicle Switch</h3>
        <p>Ek click mein vehicle badlo — gauge aur scale turant naye vehicle ke hisaab se adjust ho jaate hain.</p>
      </div>
      <div class="feature">
        <div class="fi">🏁</div>
        <h3>Session Max Tracker</h3>
        <p>Poori session ka sabse tez speed top mein hamesha dikhta rehta hai.</p>
      </div>
    </div>
  </section>

  <section class="block" id="how">
    <div class="block-head">
      <div class="eyebrow">Process</div>
      <h2>3 step mein test shuru</h2>
      <p>Koi sign-up nahi, koi setup nahi — seedha test bench pe jao aur chalu karo.</p>
    </div>
    <div class="steps">
      <div class="step">
        <h3>Vehicle Chuno</h3>
        <p>Strip se apna vehicle select karo — har ek ka top speed pehle se dikhta hai.</p>
      </div>
      <div class="step">
        <h3>Accelerate / Brake</h3>
        <p>Button dabao ya arrow keys use karo — gauge live update hota hai.</p>
      </div>
      <div class="step">
        <h3>Result Dekho</h3>
        <p>Top speed, timing aur session max — sab stats panel mein turant milta hai.</p>
      </div>
    </div>
  </section>

  <section class="block" id="compare">
    <div class="block-head">
      <div class="eyebrow">Specs</div>
      <h2>Vehicle comparison chart</h2>
      <p>Kaunsa vehicle sabse tez accelerate karta hai aur kiska top speed sabse zyada hai.</p>
    </div>
    <div class="compare-wrap">
      <table class="compare">
        <thead>
          <tr><th></th><th>Vehicle</th><th>Top Speed</th><th>Acceleration</th><th>Braking</th></tr>
        </thead>
        <tbody>
          <tr><td class="icon-cell">🚲</td><td>Bicycle</td><td>35 km/h</td><td>Low</td><td>Medium</td></tr>
          <tr><td class="icon-cell">🛵</td><td>Scooter</td><td>80 km/h</td><td>Medium</td><td>Medium</td></tr>
          <tr><td class="icon-cell">🏍️</td><td>Motorbike</td><td>180 km/h</td><td>High</td><td>High</td></tr>
          <tr><td class="icon-cell">🚗</td><td>Car</td><td>210 km/h</td><td>Medium-High</td><td>High</td></tr>
          <tr><td class="icon-cell">🏎️</td><td>Sports Car</td><td>320 km/h</td><td>Very High</td><td>Very High</td></tr>
          <tr><td class="icon-cell">🚚</td><td>Truck</td><td>120 km/h</td><td>Low</td><td>Medium</td></tr>
        </tbody>
      </table>
    </div>
  </section>

  <section class="block" id="faq">
    <div class="block-head">
      <div class="eyebrow">Sawal Jawab</div>
      <h2>FAQ</h2>
      <p>Jo sabse zyada pucha jata hai.</p>
    </div>
    <div class="faq" id="faqList">
      <div class="faq-item open">
        <button class="faq-q"><span>Kya ye asli vehicle ka speed test hai?</span><span class="plus">+</span></button>
        <div class="faq-a"><p>Nahi — ye ek simulated speedometer test hai jo browser mein chalta hai. Kisi physical vehicle se connect nahi hota.</p></div>
      </div>
      <div class="faq-item">
        <button class="faq-q"><span>Kya mobile pe kaam karega?</span><span class="plus">+</span></button>
        <div class="faq-a"><p>Haan, buttons touch-friendly hain aur layout mobile screens ke hisaab se adjust hota hai.</p></div>
      </div>
      <div class="faq-item">
        <button class="faq-q"><span>Kya naya vehicle add karwa sakte hain?</span><span class="plus">+</span></button>
        <div class="faq-a"><p>Bilkul — bas top speed aur acceleration/braking values bata do, list mein add ho jayega.</p></div>
      </div>
      <div class="faq-item">
        <button class="faq-q"><span>Data kahan save hota hai?</span><span class="plus">+</span></button>
        <div class="faq-a"><p>Kahin nahi — sab kuch sirf current session ke liye browser memory mein hota hai, page reload hote hi reset ho jata hai.</p></div>
      </div>
    </div>
  </section>

  <div class="cta-band">
    <div>
      <h3>Apna pehla speed test abhi karo</h3>
      <p>Vehicle chuno, accelerate dabao — 10 second mein result.</p>
    </div>
    <a class="hero-ctas" style="display:inline-block;" href="#tool"><span class="primary" style="display:inline-block;">Go To Test Bench</span></a>
  </div>

  <footer class="site-footer">
    <div class="footer-inner">
      <div class="footer-brand">
        <div class="logo"><span class="dot"></span>VELOCITY RIG</div>
        <p>Har vehicle ka speedometer test karne ke liye ek simple, browser-based test bench.</p>
        <div class="footer-social">
          <a href="#" aria-label="Twitter">𝕏</a>
          <a href="#" aria-label="Instagram">◎</a>
          <a href="#" aria-label="GitHub">⌥</a>
        </div>
      </div>
      <div class="footer-col">
        <h4>Product</h4>
        <ul>
          <li><a href="#tool">Test Bench</a></li>
          <li><a href="#features">Features</a></li>
          <li><a href="#compare">Compare</a></li>
        </ul>
      </div>
      <div class="footer-col">
        <h4>Resources</h4>
        <ul>
          <li><a href="#how">How it works</a></li>
          <li><a href="#faq">FAQ</a></li>
        </ul>
      </div>
      <div class="footer-col">
        <h4>Legal</h4>
        <ul>
          <li><a href="#">Privacy Policy</a></li>
          <li><a href="#">Terms of Use</a></li>
        </ul>
      </div>
    </div>
    <div class="footer-bottom">
      <span>© 2026 Velocity Rig. Simulated tool, for fun testing only.</span>
      <span><a href="#">Privacy</a><a href="#">Terms</a></span>
    </div>
  </footer>

<script>
  // FAQ accordion
  document.querySelectorAll('.faq-item').forEach(item=>{
    item.querySelector('.faq-q').addEventListener('click', ()=>{
      const wasOpen = item.classList.contains('open');
      document.querySelectorAll('.faq-item').forEach(i=>i.classList.remove('open'));
      if(!wasOpen) item.classList.add('open');
    });
  });
</script>

<script>
(function(){
  const vehicles = [
    { id:'bicycle', name:'Bicycle', icon:'🚲', top:35,  accel:14, decel:22, color:'#3ddc84' },
    { id:'scooter',  name:'Scooter', icon:'🛵', top:80,  accel:20, decel:30, color:'#4dd0e1' },
    { id:'bike',     name:'Motorbike', icon:'🏍️', top:180, accel:38, decel:46, color:'#ffcf4d' },
    { id:'car',      name:'Car', icon:'🚗', top:210, accel:30, decel:42, color:'#ff6a2b' },
    { id:'sports',   name:'Sports Car', icon:'🏎️', top:320, accel:60, decel:55, color:'#ff4d5e' },
    { id:'truck',    name:'Truck', icon:'🚚', top:120, accel:12, decel:20, color:'#9aa5b8' },
  ];

  let current = vehicles[3];
  let speed = 0;
  let sessionMax = 0;
  let accelHeld = false;
  let brakeHeld = false;
  let runStart = null;
  let hitTop = false;

  const svgNS = "http://www.w3.org/2000/svg";
  const gaugeSvg = document.getElementById('gaugeSvg');
  const speedNum = document.getElementById('speedNum');
  const sessionMaxEl = document.getElementById('sessionMax');
  const statVehicle = document.getElementById('statVehicle');
  const statTop = document.getElementById('statTop');
  const statAccelTime = document.getElementById('statAccelTime');
  const statStatus = document.getElementById('statStatus');
  const vehicleStrip = document.getElementById('vehicleStrip');
  const accelBtn = document.getElementById('accelBtn');
  const brakeBtn = document.getElementById('brakeBtn');
  const resetBtn = document.getElementById('resetBtn');

  const CX = 200, CY = 210, R = 150;
  const START_ANGLE = -220, END_ANGLE = 40; // degrees, 0 = pointing right, matches SVG rotate convention below

  function polar(cx, cy, r, angleDeg){
    const a = (angleDeg - 90) * Math.PI / 180;
    return { x: cx + r * Math.cos(a), y: cy + r * Math.sin(a) };
  }

  function angleForFraction(f){
    return START_ANGLE + (END_ANGLE - START_ANGLE) * f;
  }

  function buildGauge(){
    gaugeSvg.innerHTML = '';

    // outer bezel
    const bezel = document.createElementNS(svgNS,'circle');
    bezel.setAttribute('cx', CX); bezel.setAttribute('cy', CY); bezel.setAttribute('r', R+14);
    bezel.setAttribute('fill', '#0d1117');
    bezel.setAttribute('stroke', '#2a323f');
    bezel.setAttribute('stroke-width', '2');
    gaugeSvg.appendChild(bezel);

    // arc background (track)
    const trackPath = describeArc(CX, CY, R, START_ANGLE, END_ANGLE);
    const track = document.createElementNS(svgNS,'path');
    track.setAttribute('d', trackPath);
    track.setAttribute('fill','none');
    track.setAttribute('stroke','#1b212b');
    track.setAttribute('stroke-width','16');
    track.setAttribute('stroke-linecap','round');
    gaugeSvg.appendChild(track);

    // colored zone (progress) placeholder, id for update
    const zone = document.createElementNS(svgNS,'path');
    zone.setAttribute('id','zoneArc');
    zone.setAttribute('fill','none');
    zone.setAttribute('stroke', current.color);
    zone.setAttribute('stroke-width','16');
    zone.setAttribute('stroke-linecap','round');
    zone.setAttribute('opacity','0.9');
    gaugeSvg.appendChild(zone);

    // danger redline near top of this vehicle (last 10%)
    const redStart = angleForFraction(0.9);
    const redPath = describeArc(CX, CY, R, redStart, END_ANGLE);
    const red = document.createElementNS(svgNS,'path');
    red.setAttribute('d', redPath);
    red.setAttribute('fill','none');
    red.setAttribute('stroke','#ff4d5e');
    red.setAttribute('stroke-width','4');
    red.setAttribute('opacity','0.55');
    gaugeSvg.appendChild(red);

    // ticks + labels
    const steps = 10;
    for(let i=0;i<=steps;i++){
      const f = i/steps;
      const ang = angleForFraction(f);
      const p1 = polar(CX,CY,R-14, ang);
      const p2 = polar(CX,CY,R+2, ang);
      const tick = document.createElementNS(svgNS,'line');
      tick.setAttribute('x1',p1.x); tick.setAttribute('y1',p1.y);
      tick.setAttribute('x2',p2.x); tick.setAttribute('y2',p2.y);
      tick.setAttribute('stroke', i%5===0 ? '#8a94a6' : '#3a4353');
      tick.setAttribute('stroke-width', i%5===0 ? '2.5' : '1.5');
      gaugeSvg.appendChild(tick);

      if(i%2===0){
        const lp = polar(CX,CY,R-30, ang);
        const label = document.createElementNS(svgNS,'text');
        label.setAttribute('x', lp.x); label.setAttribute('y', lp.y+4);
        label.setAttribute('text-anchor','middle');
        label.setAttribute('font-family','JetBrains Mono, monospace');
        label.setAttribute('font-size','11');
        label.setAttribute('fill','#6b7484');
        label.textContent = Math.round(current.top * f);
        gaugeSvg.appendChild(label);
      }
    }

    // needle group
    const needleGroup = document.createElementNS(svgNS,'g');
    needleGroup.setAttribute('id','needleGroup');
    const needle = document.createElementNS(svgNS,'polygon');
    needle.setAttribute('points', `${CX-4},${CY} ${CX+4},${CY} ${CX+2},${CY-R+30} ${CX-2},${CY-R+30}`);
    needle.setAttribute('fill', '#e7ecf2');
    needleGroup.appendChild(needle);
    const hub = document.createElementNS(svgNS,'circle');
    hub.setAttribute('cx',CX); hub.setAttribute('cy',CY); hub.setAttribute('r','10');
    hub.setAttribute('fill','#1b212b');
    hub.setAttribute('stroke', current.color);
    hub.setAttribute('stroke-width','2');
    needleGroup.appendChild(hub);
    gaugeSvg.appendChild(needleGroup);
  }

  function polarToCartesian(cx, cy, r, angleDeg){
    const a = (angleDeg-90) * Math.PI/180;
    return { x: cx + r*Math.cos(a), y: cy + r*Math.sin(a) };
  }
  function describeArc(cx, cy, r, startAngle, endAngle){
    const start = polarToCartesian(cx, cy, r, endAngle);
    const end = polarToCartesian(cx, cy, r, startAngle);
    const largeArc = endAngle - startAngle <= 180 ? 0 : 1;
    return `M ${start.x} ${start.y} A ${r} ${r} 0 ${largeArc} 0 ${end.x} ${end.y}`;
  }

  function updateGaugeVisual(){
    const f = Math.max(0, Math.min(1, speed / current.top));
    const ang = angleForFraction(f);
    const needleGroup = document.getElementById('needleGroup');
    if(needleGroup){
      needleGroup.setAttribute('transform', `rotate(${ang} ${CX} ${CY})`);
    }
    const zone = document.getElementById('zoneArc');
    if(zone){
      if(f > 0.002){
        zone.setAttribute('d', describeArc(CX, CY, R, START_ANGLE, ang));
        zone.setAttribute('stroke', f > 0.9 ? '#ff4d5e' : current.color);
      } else {
        zone.setAttribute('d','');
      }
    }
    speedNum.textContent = Math.round(speed);
  }

  function renderVehicleStrip(){
    vehicleStrip.innerHTML = '';
    vehicles.forEach(v=>{
      const card = document.createElement('div');
      card.className = 'vcard' + (v.id===current.id ? ' active' : '');
      card.innerHTML = `<div class="icon">${v.icon}</div><div class="name">${v.name}</div><div class="top">Top ${v.top} km/h</div>`;
      card.addEventListener('click', ()=> selectVehicle(v.id));
      vehicleStrip.appendChild(card);
    });
  }

  function selectVehicle(id){
    current = vehicles.find(v=>v.id===id) || current;
    speed = 0;
    runStart = null;
    hitTop = false;
    statAccelTime.textContent = '—';
    statStatus.textContent = 'IDLE';
    renderVehicleStrip();
    buildGauge();
    updateStats();
    updateGaugeVisual();
  }

  function updateStats(){
    statVehicle.textContent = current.name;
    statTop.textContent = current.top + ' km/h';
    sessionMaxEl.textContent = String(Math.round(sessionMax)).padStart(3,'0');
  }

  let lastT = performance.now();
  function tick(t){
    const dt = Math.min(0.05, (t - lastT)/1000);
    lastT = t;

    if(accelHeld && speed < current.top){
      if(runStart===null){ runStart = t; hitTop=false; }
      speed = Math.min(current.top, speed + current.accel * dt);
      statStatus.textContent = 'ACCELERATING';
    } else if(brakeHeld && speed > 0){
      speed = Math.max(0, speed - current.decel * dt);
      statStatus.textContent = 'BRAKING';
    } else if(!accelHeld && !brakeHeld && speed > 0){
      // natural coast-down (light friction)
      speed = Math.max(0, speed - (current.decel*0.18) * dt);
      statStatus.textContent = speed > 0.5 ? 'COASTING' : 'IDLE';
    } else if(speed<=0){
      statStatus.textContent = 'IDLE';
    }

    if(speed >= current.top - 0.05 && !hitTop && runStart!==null){
      hitTop = true;
      const elapsed = (t - runStart)/1000;
      statAccelTime.textContent = elapsed.toFixed(2) + 's';
    }

    if(speed > sessionMax){
      sessionMax = speed;
      sessionMaxEl.textContent = String(Math.round(sessionMax)).padStart(3,'0');
    }

    updateGaugeVisual();
    requestAnimationFrame(tick);
  }

  function bindHold(el, onDown, onUp){
    const down = (e)=>{ e.preventDefault(); onDown(); };
    const up = (e)=>{ onUp(); };
    el.addEventListener('mousedown', down);
    el.addEventListener('touchstart', down, {passive:false});
    ['mouseup','mouseleave','touchend','touchcancel'].forEach(ev=> el.addEventListener(ev, up));
  }

  bindHold(accelBtn,
    ()=>{ accelHeld = true; accelBtn.classList.add('pressed'); },
    ()=>{ accelHeld = false; accelBtn.classList.remove('pressed'); }
  );
  bindHold(brakeBtn,
    ()=>{ brakeHeld = true; brakeBtn.classList.add('pressed'); },
    ()=>{ brakeHeld = false; brakeBtn.classList.remove('pressed'); }
  );

  resetBtn.addEventListener('click', ()=>{
    speed = 0; runStart = null; hitTop = false;
    statAccelTime.textContent = '—';
    statStatus.textContent = 'IDLE';
  });

  window.addEventListener('keydown', (e)=>{
    if(e.key === 'ArrowUp'){ accelHeld = true; accelBtn.classList.add('pressed'); }
    if(e.key === 'ArrowDown'){ brakeHeld = true; brakeBtn.classList.add('pressed'); }
  });
  window.addEventListener('keyup', (e)=>{
    if(e.key === 'ArrowUp'){ accelHeld = false; accelBtn.classList.remove('pressed'); }
    if(e.key === 'ArrowDown'){ brakeHeld = false; brakeBtn.classList.remove('pressed'); }
  });

  renderVehicleStrip();
  buildGauge();
  updateStats();
  updateGaugeVisual();
  requestAnimationFrame(tick);
})();
</script>

</body>
</html>
