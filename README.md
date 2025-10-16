<html lang="id">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>🎡 Leaf-Wheel — by LeafZuya 🌿</title>
<style>
  body {
    margin: 0;
    padding: 0;
    height: 100vh;
    background: linear-gradient(135deg, #00e69a, #39a7ff);
    font-family: 'Poppins', sans-serif;
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
    color: white;
    overflow: hidden;
  }

  h1 {
    font-size: 2em;
    text-shadow: 0 4px 14px rgba(0,0,0,0.25);
    margin-bottom: 15px;
  }

  .wheel-container {
    position: relative;
    width: 360px;
    height: 360px;
  }

  canvas {
    border-radius: 50%;
    box-shadow: 0 0 25px rgba(0,255,180,0.4);
  }

  .arrow {
    position: absolute;
    bottom: -25px;
    left: 50%;
    transform: translateX(-50%) rotate(180deg);
    width: 0;
    height: 0;
    border-left: 15px solid transparent;
    border-right: 15px solid transparent;
    border-top: 35px solid black;
    filter: drop-shadow(0 0 8px rgba(255,255,180,0.9));
  }

  .controls {
    margin-top: 15px;
    text-align: center;
  }

  textarea {
    width: 300px;
    height: 80px;
    border-radius: 10px;
    border: none;
    padding: 10px;
    font-size: 14px;
    resize: none;
    box-shadow: 0 4px 10px rgba(0,0,0,0.2);
  }

  button {
    background: linear-gradient(135deg, #00c16b, #27a2ff);
    border: none;
    padding: 12px 24px;
    border-radius: 15px;
    color: white;
    font-weight: bold;
    cursor: pointer;
    font-size: 16px;
    margin-top: 10px;
    box-shadow: 0 6px 18px rgba(0,0,0,0.25);
    transition: transform 0.2s ease, box-shadow 0.3s ease;
  }

  button:hover {
    transform: scale(1.05);
    box-shadow: 0 0 20px rgba(0,255,200,0.6);
  }

  #result {
    margin-top: 12px;
    font-size: 1.2em;
    font-weight: 600;
    text-shadow: 0 2px 8px rgba(0,0,0,0.3);
  }

  footer {
    position: absolute;
    bottom: 6px;
    font-size: 12px;
    opacity: 0.8;
  }

  /* 🌿 Popup hasil tengah */
  #popup {
    position: fixed;
    top: 0; left: 0;
    width: 100%; height: 100%;
    background: rgba(0,0,0,0.65);
    display: flex;
    align-items: center;
    justify-content: center;
    visibility: hidden;
    opacity: 0;
    transition: opacity 0.4s ease;
    z-index: 999;
  }

  #popup.show {
    visibility: visible;
    opacity: 1;
  }

  .popup-box {
    background: linear-gradient(135deg, #00e69a, #39a7ff);
    border-radius: 25px;
    padding: 25px 40px;
    text-align: center;
    box-shadow: 0 0 40px rgba(0,255,200,0.6);
    position: relative;
    overflow: hidden;
  }

  .popup-box h2 {
    font-size: 1.8em;
    margin-bottom: 10px;
    color: white;
    text-shadow: 0 0 15px rgba(255,255,255,0.7);
  }

  .popup-box span {
    font-size: 2em;
    font-weight: bold;
    color: gold;
    text-shadow: 0 0 20px rgba(255,255,180,1);
  }

  /* 🌸 Efek daun dan bunga */
  .decor {
    position: absolute;
    width: 40px;
    height: 40px;
    opacity: 0.8;
    animation: float 3s infinite ease-in-out;
  }

  .leaf { background: url('https://cdn-icons-png.flaticon.com/512/765/765613.png') no-repeat center/contain; }
  .flower { background: url('https://cdn-icons-png.flaticon.com/512/616/616408.png') no-repeat center/contain; }
  .ice { background: url('https://cdn-icons-png.flaticon.com/512/1170/1170627.png') no-repeat center/contain; }

  @keyframes float {
    0%,100% { transform: translateY(0) rotate(0deg); }
    50% { transform: translateY(-10px) rotate(5deg); }
  }
</style>
</head>
<body>

<h1>🎡 Leaf-Wheel — by LeafZuya 🌿</h1>
<div class="wheel-container">
  <div class="arrow"></div>
  <canvas id="wheel" width="360" height="360"></canvas>
</div>

<div class="controls">
  <textarea id="items" placeholder="Tulis daftar item, pisahkan dengan koma... Contoh: Es Teh, Nasi Goreng, Ayam Bakar"></textarea><br>
  <button id="spinBtn">Putar Sekarang!</button>
  <div id="result"></div>
</div>

<!-- 🌿 Popup hasil -->
<div id="popup">
  <div class="popup-box">
    <div class="decor leaf" style="top:-15px;left:-15px;"></div>
    <div class="decor flower" style="top:-20px;right:-20px;"></div>
    <div class="decor ice" style="bottom:-20px;left:20px;"></div>
    <h2>🎉 Hasil Pilihan:</h2>
    <span id="popupResult"></span>
  </div>
</div>

<footer>🌿 Made with 💚 by LeafZuya | Deluxe Edition 💙</footer>

<audio id="tickSound" src="Leaf.mp3" preload="auto"></audio>

<script>
const canvas = document.getElementById("wheel");
const ctx = canvas.getContext("2d");
const result = document.getElementById("result");
const spinBtn = document.getElementById("spinBtn");
const itemsInput = document.getElementById("items");
const tickSound = document.getElementById("tickSound");
const popup = document.getElementById("popup");
const popupResult = document.getElementById("popupResult");

let items = ["Isi Sendiri", "Bakso", "Mie Ayam", "Sate", "Nasi Goreng"];
let startAngle = 0;
let arc;
let spinAngleStart = 0;
let spinTime = 0;
let spinTimeTotal = 0;
let spinning = false;
let lastSector = -1;

function drawWheel() {
  const outsideRadius = 150;
  const textRadius = 115;
  const insideRadius = 40;

  ctx.clearRect(0, 0, 360, 360);
  arc = Math.PI / (items.length / 2);
  const colors = ["#00c16b", "#27a2ff", "#FFD700"];

  for (let i = 0; i < items.length; i++) {
    const angle = startAngle + i * arc;
    ctx.fillStyle = colors[i % colors.length];
    ctx.beginPath();
    ctx.arc(180, 180, outsideRadius, angle, angle + arc, false);
    ctx.arc(180, 180, insideRadius, angle + arc, angle, true);
    ctx.fill();

    ctx.save();
    ctx.fillStyle = "#000";
    ctx.translate(180 + Math.cos(angle + arc / 2) * textRadius,
                  180 + Math.sin(angle + arc / 2) * textRadius);
    ctx.rotate(angle + arc / 2 + Math.PI / 2);
    const text = items[i];
    ctx.font = "bold 14px Poppins";
    ctx.fillText(text, -ctx.measureText(text).width / 2, 0);
    ctx.restore();
  }

  ctx.beginPath();
  ctx.arc(180, 180, insideRadius, 0, 2 * Math.PI);
  ctx.fillStyle = "#ff3b3b";
  ctx.fill();
}

function rotateWheel() {
  spinTime += 16; // frame ~60fps
  if (spinTime >= spinTimeTotal) {
    stopRotateWheel();
    return;
  }

  const spinAngle = spinAngleStart - easeOut(spinTime, 0, spinAngleStart, spinTimeTotal);
  startAngle += (spinAngle * Math.PI / 180);
  drawWheel();

  // 🎵 Suara sinkron dengan sektor
  const degrees = (startAngle * 180 / Math.PI + 270) % 360;
  const currentSector = Math.floor(degrees / (360 / items.length));
  if (currentSector !== lastSector) {
    tickSound.currentTime = 0;
    tickSound.play();
    lastSector = currentSector;
  }

  requestAnimationFrame(rotateWheel);
}

function stopRotateWheel() {
  const degrees = startAngle * 180 / Math.PI + 270;
  const arcd = arc * 180 / Math.PI;
  const index = Math.floor((360 - (degrees % 360)) / arcd) % items.length;

  popupResult.innerHTML = items[index];
  popup.classList.add("show");
  setTimeout(() => popup.classList.remove("show"), 4000);
  spinning = false;
}

function easeOut(t, b, c, d) {
  const ts = (t /= d) * t;
  const tc = ts * t;
  return b + c * (tc + -3 * ts + 3 * t);
}

spinBtn.addEventListener("click", () => {
  if (spinning) return;
  const val = itemsInput.value.trim();
  if (val) items = val.split(",").map(i => i.trim());
  spinning = true;
  lastSector = -1;
  spinAngleStart = Math.random() * 25 + 50; // putaran lebih cepat
  spinTime = 0;
  spinTimeTotal = Math.random() * 4000 + 5000; // durasi lebih lama
  rotateWheel();
});

drawWheel();
</script>

</body>
</html>

