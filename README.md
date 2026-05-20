# Car_Speed_Test
My Car Speed Test GPS
<!-- ========= EXTRA FEATURES SECTION ========= -->

<!-- PWA Support -->
<link rel="manifest" href="/manifest.json" />
<meta name="theme-color" content="#0f172a" />

<!-- Icons -->
<link
  rel="stylesheet"
  href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.1/css/all.min.css"
/>

<style>
  body {
    margin: 0;
    font-family: "Montserrat", sans-serif;
    background: linear-gradient(135deg, #0f172a, #111827);
    color: white;
    overflow-x: hidden;
  }

  /* HERO */
  .hero {
    padding: 40px 20px;
    text-align: center;
  }

  .hero h1 {
    font-size: 42px;
    font-weight: 800;
    margin-bottom: 10px;
  }

  .hero p {
    color: #cbd5e1;
    font-size: 18px;
  }

  /* FEATURE CARDS */
  .speed-grid {
    display: grid;
    grid-template-columns: repeat(auto-fit, minmax(170px, 1fr));
    gap: 20px;
    padding: 30px;
  }

  .speed-card {
    background: rgba(255,255,255,0.08);
    backdrop-filter: blur(10px);
    border-radius: 25px;
    padding: 30px 20px;
    text-align: center;
    transition: 0.3s;
    cursor: pointer;
    border: 1px solid rgba(255,255,255,0.1);
  }

  .speed-card:hover {
    transform: translateY(-8px) scale(1.03);
    background: rgba(255,255,255,0.12);
  }

  .speed-card i {
    font-size: 45px;
    margin-bottom: 15px;
    color: #38bdf8;
  }

  .speed-card h3 {
    margin: 0;
    font-size: 20px;
  }

  /* SPEEDOMETER */
  .meter-box {
    margin: 40px auto;
    width: 320px;
    height: 320px;
    border-radius: 50%;
    background: radial-gradient(circle, #1e293b, #020617);
    border: 10px solid #38bdf8;
    display: flex;
    align-items: center;
    justify-content: center;
    position: relative;
    box-shadow: 0 0 40px rgba(56,189,248,0.5);
  }

  .speed-number {
    font-size: 70px;
    font-weight: 800;
  }

  .km {
    font-size: 24px;
    color: #94a3b8;
  }

  /* BUTTONS */
  .btns {
    display: flex;
    justify-content: center;
    gap: 15px;
    flex-wrap: wrap;
    margin: 30px;
  }

  .btn {
    background: #38bdf8;
    border: none;
    padding: 14px 28px;
    border-radius: 14px;
    color: black;
    font-weight: 700;
    cursor: pointer;
    transition: 0.3s;
  }

  .btn:hover {
    transform: scale(1.05);
  }

  /* DARK MODE */
  .light-mode {
    background: #f1f5f9;
    color: black;
  }

  .light-mode .speed-card {
    background: white;
    color: black;
  }

  .light-mode .meter-box {
    background: white;
  }

  /* LIVE STATUS */
  .status {
    text-align: center;
    margin-top: 20px;
    color: #22c55e;
    font-weight: bold;
  }

  /* MAP */
  #map {
    width: 90%;
    height: 350px;
    margin: 40px auto;
    border-radius: 25px;
    overflow: hidden;
    border: 3px solid #38bdf8;
  }

</style>

<!-- HERO -->
<section class="hero">
  <h1>🚀 Smart Speed Meter</h1>
  <p>
    Internet Speed • GPS Speed • AI Voice • Live Map • Top Speed Record
  </p>
</section>

<!-- SPEED OPTIONS -->
<div class="speed-grid">

  <div class="speed-card">
    <i class="fa-solid fa-car"></i>
    <h3>Car Speed</h3>
  </div>

  <div class="speed-card">
    <i class="fa-solid fa-motorcycle"></i>
    <h3>Bike Speed</h3>
  </div>

  <div class="speed-card">
    <i class="fa-solid fa-bus"></i>
    <h3>Bus Speed</h3>
  </div>

  <div class="speed-card">
    <i class="fa-solid fa-train"></i>
    <h3>Train Speed</h3>
  </div>

  <div class="speed-card">
    <i class="fa-solid fa-person-walking"></i>
    <h3>Walk Speed</h3>
  </div>

  <div class="speed-card">
    <i class="fa-solid fa-wifi"></i>
    <h3>Internet Speed</h3>
  </div>

</div>

<!-- SPEEDOMETER -->
<div class="meter-box">
  <div>
    <div class="speed-number" id="speed">0</div>
    <div class="km">KM/H</div>
  </div>
</div>

<div class="status" id="status">
  Waiting for GPS...
</div>

<!-- BUTTONS -->
<div class="btns">
  <button class="btn" onclick="startTracking()">
    Start GPS
  </button>

  <button class="btn" onclick="toggleMode()">
    Dark / Light
  </button>

  <button class="btn" onclick="speakSpeed()">
    AI Voice
  </button>

  <button class="btn" onclick="fullscreenMode()">
    Full Screen
  </button>
</div>

<!-- LIVE MAP -->
<iframe
  id="map"
  src="https://maps.google.com/maps?q=india&t=&z=5&ie=UTF8&iwloc=&output=embed">
</iframe>

<script>
  let topSpeed = 0;
  let currentSpeed = 0;

  // GPS TRACKING
  function startTracking() {

    if (navigator.geolocation) {

      navigator.geolocation.watchPosition((position) => {

        let speed = position.coords.speed;

        if (speed != null) {

          currentSpeed = (speed * 3.6).toFixed(1);

          document.getElementById("speed").innerText = currentSpeed;

          document.getElementById("status").innerHTML =
            "🚀 Live Speed Tracking Active";

          // SAVE TOP SPEED
          if (currentSpeed > topSpeed) {
            topSpeed = currentSpeed;
            localStorage.setItem("topSpeed", topSpeed);
          }

        }

      });

    } else {
      alert("GPS Not Supported");
    }
  }

  // DARK MODE
  function toggleMode() {
    document.body.classList.toggle("light-mode");
  }

  // AI VOICE
  function speakSpeed() {

    let speech = new SpeechSynthesisUtterance(
      "Your current speed is " + currentSpeed + " kilometers per hour"
    );

    speech.lang = "en-US";
    speech.volume = 1;
    speech.rate = 1;

    window.speechSynthesis.speak(speech);
  }

  // FULL SCREEN
  function fullscreenMode() {

    if (document.documentElement.requestFullscreen) {
      document.documentElement.requestFullscreen();
    }

  }

  // LOAD TOP SPEED
  window.onload = () => {

    let saved = localStorage.getItem("topSpeed");

    if (saved) {
      console.log("Top Speed:", saved);
    }

  };
</script>
