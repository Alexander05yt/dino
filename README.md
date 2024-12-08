<!DOCTYPE html>
<html>
<head>
    <title>Run Dino Run</title>
    <style>
        canvas {
            display: block;
            margin: 0 auto;
            background: #f4f4f4;
            border: 2px solid #000;
        }
        body {
            text-align: center;
            font-family: Arial, sans-serif;
        }
    </style>
</head>
<body>
    <h1>Run Dino Run</h1>
    <div>
        <label>Difficoltà:</label>
        <select id="difficulty">
            <option value="easy">Facile</option>
            <option value="normal">Normale</option>
            <option value="hard">Difficile</option>
        </select>
        <label>Velocità iniziale:</label>
        <input type="range" id="speed" min="5" max="20" value="10">
        <button onclick="startGame()">Inizia</button>
    </div>
    <canvas id="gameCanvas" width="800" height="300"></canvas>

    <script>
        let canvas, ctx, player, obstacles, score, gameSpeed, isGameOver;

        function startGame() {
            const difficulty = document.getElementById('difficulty').value;
            const initialSpeed = parseInt(document.getElementById('speed').value);

            canvas = document.getElementById('gameCanvas');
            ctx = canvas.getContext('2d');

            score = 0;
            gameSpeed = initialSpeed;
            isGameOver = false;
            player = { x: 50, y: 250, width: 20, height: 20, jump: false, velocityY: 0 };
            obstacles = [];

            setInterval(() => {
                if (!isGameOver) {
                    gameSpeed += 0.1; // Incrementa gradualmente la velocità
                    score++;
                }
            }, 100);

            document.addEventListener('keydown', (e) => {
                if (e.code === 'Space' && !player.jump) {
                    player.jump = true;
                    player.velocityY = -10;
                }
            });

            gameLoop();
        }

        function gameLoop() {
            if (isGameOver) {
                ctx.font = '30px Arial';
                ctx.fillStyle = 'red';
                ctx.fillText('Game Over!', 300, 150);
                return;
            }

            // Pulizia canvas
            ctx.clearRect(0, 0, canvas.width, canvas.height);

            // Player
            ctx.fillStyle = 'blue';
            ctx.fillRect(player.x, player.y, player.width, player.height);

            // Gravità e movimento
            if (player.jump) {
                player.velocityY += 1; // Gravità
                player.y += player.velocityY;
                if (player.y >= 250) {
                    player.y = 250;
                    player.jump = false;
                }
            }

            // Generazione ostacoli
            if (Math.random() < 0.02 * (gameSpeed / 10)) {
                const height = Math.random() * 50 + 10;
                obstacles.push({ x: canvas.width, y: 300 - height, width: 20, height });
            }

            // Muovi ostacoli
            ctx.fillStyle = 'green';
            obstacles = obstacles.filter(o => o.x > -20);
            obstacles.forEach(o => {
                o.x -= gameSpeed / 5;
                ctx.fillRect(o.x, o.y, o.width, o.height);

                // Collisione
                if (
                    player.x < o.x + o.width &&
                    player.x + player.width > o.x &&
                    player.y < o.y + o.height &&
                    player.height + player.y > o.y
                ) {
                    isGameOver = true;
                }
            });

            // Punteggio
            ctx.font = '20px Arial';
            ctx.fillStyle = 'black';
            ctx.fillText(`Punteggio: ${score} m`, 10, 20);

            requestAnimationFrame(gameLoop);
        }
    </script>
</body>
</html>
