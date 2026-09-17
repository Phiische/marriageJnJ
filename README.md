# Liebe Josi, lieber Janni
Wir, also Angi Anita Anna und ich wünschen euch nur alles erdenklich gute für euch und euren neuen alten lebensabschnitt. 
Ob sich etwas ändert? JA! Jeden Tag. Das ist der Code des Lebens und ich wünsche euch die Ausdauer, Leidenschaft und die 
Liebe diesen Code jeden Tag weiter zu schreiben. 
Alles Liebe eure Familie, die man sich nicht aussuchen kann

<!DOCTYPE html>
<html lang="de">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Hochzeits-Frogger: Pixel Edition</title>
  <!-- Retro Pixel Font -->
  <link href="https://fonts.googleapis.com/css2?family=Press+Start+2P&display=swap" rel="stylesheet">
  <style>
    body {
      background: #2b2b36;
      font-family: 'Press Start 2P', monospace;
      display: flex;
      flex-direction: column;
      align-items: center;
      justify-content: center;
      margin: 0;
      padding: 20px;
      color: #fff;
    }
    h1 { 
      margin-bottom: 10px; 
      color: #ff70a6; 
      font-size: 16px;
      text-align: center;
      line-height: 1.5;
    }
    p { 
      margin-top: 0; 
      font-size: 9px; 
      color: #ff97b7;
      text-align: center;
      margin-bottom: 15px;
      line-height: 1.6;
    }
    canvas {
      border: 4px solid #ff70a6;
      border-radius: 4px;
      box-shadow: 0 0 20px rgba(255, 112, 166, 0.4);
      background: #111119;
      /* Verhindert verschwommene Pixel auf Canvas */
      image-rendering: pixelated;
      image-rendering: crisp-edges;
    }
    .controls {
      margin-top: 15px;
    }
    button {
      padding: 10px 15px;
      font-family: 'Press Start 2P', monospace;
      font-size: 10px;
      background: #ff70a6;
      color: white;
      border: none;
      border-radius: 4px;
      cursor: pointer;
      box-shadow: 0 4px 0 #b3406f;
    }
    button:active {
      transform: translateY(4px);
      box-shadow: none;
    }
  </style>
</head>
<body>

  <h1>ALLES GUTE ZUR HOCHZEIT! 💍</h1>
  <p>PFEILTASTEN: 🕺 Bräutigam | WASD: 💃 Braut</p>

  <canvas id="gameCanvas" width="520" height="600"></canvas>

  <div class="controls">
    <button onclick="resetGame()">NEUSTART</button>
  </div>

  <script>
    const canvas = document.getElementById("gameCanvas");
    const ctx = canvas.getContext("2d");

    const GRID_SIZE = 40;

    // Spieler
    let player1 = { x: 2 * GRID_SIZE, y: 14 * GRID_SIZE, emoji: "🕺", wins: false };
    let player2 = { x: 10 * GRID_SIZE, y: 0 * GRID_SIZE, emoji: "💃", wins: false };

    // Spezifische Lebens-Hindernisse
    const obstacleTypes = [
      { name: "Eigenes Auto", emoji: "🚗", y: 2 * GRID_SIZE, speed: 2.5 },
      { name: "Haus", emoji: "🏠", y: 3 * GRID_SIZE, speed: -2 },
      { name: "Kinder", emoji: "👶", y: 5 * GRID_SIZE, speed: 3.5 },
      { name: "Rechnungen", emoji: "💸", y: 6 * GRID_SIZE, speed: -3 },
      { name: "Schlaflose Nächte", emoji: "🥱", y: 8 * GRID_SIZE, speed: 2 },
      { name: "Rechnungen", emoji: "💸", y: 9 * GRID_SIZE, speed: -3.5 },
      { name: "Kinder", emoji: "👶", y: 11 * GRID_SIZE, speed: 2.8 },
      { name: "Eigenes Auto", emoji: "🚗", y: 12 * GRID_SIZE, speed: -3 }
    ];

    let obstacleData = [];
    obstacleTypes.forEach(o => {
      for (let i = 0; i < 3; i++) {
        obstacleData.push({
          x: i * 200,
          y: o.y,
          speed: o.speed,
          emoji: o.emoji,
          label: o.name
        });
      }
    });

    let gameWon = false;

    // Steuerung
    window.addEventListener("keydown", (e) => {
      if (gameWon) return;

      // Bräutigam (Pfeiltasten)
      if (!player1.wins) {
        if (e.key === "ArrowUp" && player1.y > 0) player1.y -= GRID_SIZE;
        if (e.key === "ArrowDown" && player1.y < canvas.height - GRID_SIZE) player1.y += GRID_SIZE;
        if (e.key === "ArrowLeft" && player1.x > 0) player1.x -= GRID_SIZE;
        if (e.key === "ArrowRight" && player1.x < canvas.width - GRID_SIZE) player1.x += GRID_SIZE;
      }

      // Braut (WASD)
      if (!player2.wins) {
        if (e.key === "w" && player2.y > 0) player2.y -= GRID_SIZE;
        if (e.key === "s" && player2.y < canvas.height - GRID_SIZE) player2.y += GRID_SIZE;
        if (e.key === "a" && player2.x > 0) player2.x -= GRID_SIZE;
        if (e.key === "d" && player2.x < canvas.width - GRID_SIZE) player2.x += GRID_SIZE;
      }

      checkWin();
    });

    function checkWin() {
      if (player1.y === 0) player1.wins = true;
      if (player2.y === canvas.height - GRID_SIZE) player2.wins = true;
      if (player1.wins && player2.wins) gameWon = true;
    }

    function checkCollision(p) {
      if (p.wins) return;

      obstacleData.forEach(o => {
        if (
          Math.abs(p.x - o.x) < GRID_SIZE * 0.7 &&
          Math.abs(p.y - o.y) < GRID_SIZE * 0.7
        ) {
          if (p === player1) {
            p.x = 2 * GRID_SIZE;
            p.y = 14 * GRID_SIZE;
          } else {
            p.x = 10 * GRID_SIZE;
            p.y = 0 * GRID_SIZE;
          }
        }
      });
    }

    function update() {
      if (gameWon) return;

      obstacleData.forEach(o => {
        o.x += o.speed;
        if (o.speed > 0 && o.x > canvas.width) o.x = -GRID_SIZE;
        if (o.speed < 0 && o.x < -GRID_SIZE) o.x = canvas.width;
      });

      checkCollision(player1);
      checkCollision(player2);
    }

    function draw() {
      ctx.clearRect(0, 0, canvas.width, canvas.height);

      // Pixelige Wege / Hintergründe
      ctx.fillStyle = "#ff70a6"; // Zielzonen
      ctx.fillRect(0, 0, canvas.width, GRID_SIZE);
      ctx.fillRect(0, canvas.height - GRID_SIZE, canvas.width, GRID_SIZE);

      ctx.fillStyle = "#3a3a4c"; // Safe Zone
      ctx.fillRect(0, 7 * GRID_SIZE, canvas.width, GRID_SIZE);

      // Hindernisse & Figuren
      ctx.font = "26px serif";
      ctx.textAlign = "left";
      ctx.textBaseline = "top";

      obstacleData.forEach(o => {
        ctx.fillText(o.emoji, o.x, o.y + 6);
      });

      ctx.fillText(player1.emoji, player1.x, player1.y + 6);
      ctx.fillText(player2.emoji, player2.x, player2.y + 6);

      // Gewonnen-Screen im 8-Bit-Style
      if (gameWon) {
        ctx.fillStyle = "rgba(17, 11, 25, 0.92)";
        ctx.fillRect(0, 0, canvas.width, canvas.height);

        ctx.fillStyle = "#ff70a6";
        ctx.font = "14px 'Press Start 2P'";
        ctx.textAlign = "center";
        ctx.fillText("GEMEINSAM STARK!", canvas.width / 2, 220);

        ctx.fillStyle = "#ffffff";
        ctx.font = "8px 'Press Start 2P'";
        ctx.fillText("Ihr meistert alle Huerden des Lebens", canvas.width / 2, 270);
        ctx.fillText("zusammen!", canvas.width / 2, 290);

        ctx.font = "24px serif";
        ctx.fillText("❤️ 👩‍❤️‍👨 ❤️", canvas.width / 2, 350);
      }
    }

    function gameLoop() {
      update();
      draw();
      requestAnimationFrame(gameLoop);
    }

    function resetGame() {
      player1 = { x: 2 * GRID_SIZE, y: 14 * GRID_SIZE, emoji: "🕺", wins: false };
      player2 = { x: 10 * GRID_SIZE, y: 0 * GRID_SIZE, emoji: "💃", wins: false };
      gameWon = false;
    }

    gameLoop();
  </script>
</body>
</html>
