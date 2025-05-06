<!DOCTYPE html>
<html lang="zh">
<head>
  <meta charset="UTF-8">
  <title>Ront 粒子动画</title>
  <style>
    html, body {
      margin: 0;
      overflow: hidden;
      background: #b30000; /* 红色背景 */
    }
    canvas {
      display: block;
    }
  </style>
</head>
<body>
  <audio id="bg-music" src="youjing.mp3" autoplay loop></audio>
  <canvas id="canvas"></canvas>

  <script>
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
      // 尾迹效果（透明覆盖）
      ctx.fillStyle = 'rgba(179, 0, 0, 0.3)';
      ctx.fillRect(0, 0, canvas.width, canvas.height);

      balls.forEach(ball => {
        const dx = ball.tx - ball.x;
        const dy = ball.ty - ball.y;

        // 轻微旋转扰动
        const angleOffset = Math.sin(ball.angle) * 10;
        const targetX = ball.tx + Math.sin(ball.angle) * 5;
        const targetY = ball.ty + Math.cos(ball.angle) * 5;

        ball.vx += (targetX - ball.x) * 0.01;
        ball.vy += (targetY - ball.y) * 0.01;

        ball.vx *= 0.85;
        ball.vy *= 0.85;

        ball.x += ball.vx;
        ball.y += ball.vy;

        ball.angle += rotationFactor;

        // 粒子绘制 + 发光
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

    // 自动播放背景音乐（处理浏览器限制）
    const music = document.getElementById("bg-music");
    document.body.addEventListener("click", () => music.play());
  </script>
</body>
</html>
