<!DOCTYPE html>
<html lang="zh">
<head>
  <meta charset="UTF-8">
  <title>Ront 动画挑战</title>
  <style>
    html, body {
      margin: 0;
      overflow: hidden;
      font-family: sans-serif;
    }

    #challenge {
      position: absolute;
      top: 0;
      left: 0;
      width: 100%;
      height: 100%;
      background: #111;
      color: white;
      display: flex;
      flex-direction: column;
      align-items: center;
      justify-content: center;
      z-index: 10;
    }

    #challenge h1 {
      font-size: 2em;
      margin-bottom: 1em;
    }

    #challenge input {
      padding: 10px;
      font-size: 1em;
    }

    #challenge button {
      margin-top: 10px;
      padding: 8px 20px;
      font-size: 1em;
      background: crimson;
      border: none;
      color: white;
      cursor: pointer;
    }

    #challenge p {
      margin-top: 10px;
    }

    canvas {
      display: block;
    }
  </style>
</head>
<body>

<!-- 挑战界面 -->
<div id="challenge">
  <h1>微积分题：f(x) = x² 的导数是多少？</h1>
  <input type="text" id="answer" placeholder="请输入答案（例如：2x）" />
  <button onclick="checkAnswer()">提交</button>
  <p id="feedback" style="color:red;"></p>
</div>

<!-- 背景音乐 -->
<audio id="bg-music" src="youjing.mp3" loop></audio>

<!-- 动画画布 -->
<canvas id="canvas"></canvas>

<script>
  function checkAnswer() {
    const input = document.getElementById('answer').value.trim().replace(/\s+/g, '').toLowerCase();
    const correctAnswers = ['2x', '2*x'];

    if (correctAnswers.includes(input)) {
      document.getElementById('challenge').style.display = 'none';
      document.getElementById('bg-music').play().catch(() => {});
      startAnimation();
    } else {
      document.getElementById('feedback').innerText = '答案错误，请再试一次。';
    }
  }

  function startAnimation() {
    const canvas = document.getElementById('canvas');
    const ctx = canvas.getContext('2d');
    canvas.width = window.innerWidth;
    canvas.height = window.innerHeight;

    const text = "Ront";
    const fontSize = 200;
    const spacing = 6;
    const balls = [];
    const rotationFactor = 0.003;

    function getTextPixels() {
      const offscreen = document.createElement('canvas');
      const ctx2 = offscreen.getContext('2d');
      offscreen.width = canvas.width;
      offscreen.height = canvas.height;
      ctx2.fillStyle = "white";
      ctx2.font = `${fontSize}px bold sans-serif`;
      ctx2.textAlign = "center";
      ctx2.fillText(text, canvas.width / 2, canvas.height / 2 + fontSize / 3);

      const imageData = ctx2.getImageData(0, 0, canvas.width, canvas.height);
      const pixels = [];

      for (let y = 0; y < imageData.height; y += spacing) {
        for (let x = 0; x < imageData.width; x += spacing) {
          const index = (y * imageData.width + x) * 4;
          if (imageData.data[index + 3] > 128) {
            pixels.push({ x, y });
          }
        }
      }
      return pixels;
    }

    const targetPoints = getTextPixels();

    for (let i = 0; i < targetPoints.length; i++) {
      balls.push({
        x: Math.random() * canvas.width,
        y: Math.random() * canvas.height,
        tx: targetPoints[i].x,
        ty: targetPoints[i].y,
        vx: 0,
        vy: 0,
        angle: Math.random() * Math.PI * 2,
      });
    }

    function animate() {
      // 尾迹背景，保持红色并留痕
      ctx.fillStyle = 'rgba(179, 0, 0, 0.3)';
      ctx.fillRect(0, 0, canvas.width, canvas.height);

      balls.forEach(ball => {
        const targetX = ball.tx + Math.sin(ball.angle) * 5;
        const targetY = ball.ty + Math.cos(ball.angle) * 5;

        ball.vx += (targetX - ball.x) * 0.01;
        ball.vy += (targetY - ball.y) * 0.01;

        ball.vx *= 0.85;
        ball.vy *= 0.85;

        ball.x += ball.vx;
        ball.y += ball.vy;

        ball.angle += rotationFactor;

        ctx.beginPath();
        ctx.fillStyle = "white";
        ctx.shadowColor = "#ffdddd";
        ctx.shadowBlur = 8;
        ctx.arc(ball.x, ball.y, 2, 0, Math.PI * 2);
        ctx.fill();
      });

      requestAnimationFrame(animate);
    }

    animate();
  }

  // 点击播放音乐（应对浏览器限制）
  document.body.addEventListener("click", () => {
    const music = document.getElementById("bg-music");
    music.play().catch(() => {});
  });
</script>

</body>
</html>
