
<!DOCTYPE html>
<html lang="id">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>🎡 ShiroWheel — by LeafZuya 🌿</title>
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
    top: -15px;
    left: 50%;
    transform: translateX(-50%);
    width: 0;
    height: 0;
    border-left: 15px solid transparent;
    border-right: 15px solid transparent;
    border-bottom: 35px solid gold;
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
</style>
</head>
<body>

<h1>🎡 ShiroWheel — by LeafZuya 🌿</h1>
<div class="wheel-container">
  <div class="arrow"></div>
  <canvas id="wheel" width="360" height="360"></canvas>
</div>

<div class="controls">
  <textarea id="items" placeholder="Tulis daftar item, pisahkan dengan koma... Contoh: Es Teh, Nasi Goreng, Ayam Bakar"></textarea><br>
  <button id="spinBtn">Putar Sekarang!</button>
  <div id="result"></div>
</div>

<footer>🌿 Made with 💚 by LeafZuya | Gradient Vibe 💙</footer>

<script>
  const canvas = document.getElementById("wheel");
  const ctx = canvas.getContext("2d");
  const result = document.getElementById("result");
  const spinBtn = document.getElementById("spinBtn");
  const itemsInput = document.getElementById("items");

  let items = ["LeafCy", "Shiroko", "Blue Archive", "Nasi Goreng", "Tidur"];
  let startAngle = 0;
  let arc;
  let spinTimeout = null;
  let spinAngleStart = 0;
  let spinning = false;

  function drawWheel() {
    const outsideRadius = 150;
    const textRadius = 115;
    const insideRadius = 40;

    ctx.clearRect(0, 0, 360, 360);
    arc = Math.PI / (items.length / 2);

    for (let i = 0; i < items.length; i++) {
      const angle = startAngle + i * arc;
      ctx.fillStyle = i % 2 === 0 ? "#00c16b" : "#27a2ff";
      ctx.beginPath();
      ctx.arc(180, 180, outsideRadius, angle, angle + arc, false);
      ctx.arc(180, 180, insideRadius, angle + arc, angle, true);
      ctx.fill();

      ctx.save();
      ctx.fillStyle = "white";
      ctx.translate(180 + Math.cos(angle + arc / 2) * textRadius,
                    180 + Math.sin(angle + arc / 2) * textRadius);
      ctx.rotate(angle + arc / 2 + Math.PI / 2);
      const text = items[i];
      ctx.font = "bold 14px Poppins";
      ctx.fillText(text, -ctx.measureText(text).width / 2, 0);
      ctx.restore();
    }

    // Center circle
    ctx.beginPath();
    ctx.arc(180, 180, insideRadius, 0, 2 * Math.PI);
    ctx.fillStyle = "white";
    ctx.fill();

    ctx.fillStyle = "#0a6";
    ctx.font = "bold 15px Poppins";
    ctx.fillText("ShiroPop", 130, 185);
  }

  function rotateWheel() {
    spinAngleStart *= 0.97;
    if (spinAngleStart < 0.3) {
      clearTimeout(spinTimeout);
      stopRotateWheel();
    } else {
      startAngle += (spinAngleStart * Math.PI / 180);
      drawWheel();
      spinTimeout = setTimeout(rotateWheel, 20);
    }
  }

  function stopRotateWheel() {
    const degrees = startAngle * 180 / Math.PI + 90;
    const arcd = arc * 180 / Math.PI;
    const index = Math.floor((360 - (degrees % 360)) / arcd) % items.length;
    result.innerHTML = `🎉 Hasil: <b>${items[index]}</b> 🌿`;
    spinning = false;
  }

  spinBtn.addEventListener("click", () => {
    if (spinning) return;
    spinning = true;
    const val = itemsInput.value.trim();
    if (val) items = val.split(",").map(i => i.trim());
    spinAngleStart = Math.random() * 15 + 30;
    rotateWheel();
  });

  drawWheel();
</script>

</body>
</html>
