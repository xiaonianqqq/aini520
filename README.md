<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>躲避小球游戏</title>
    <style>
        body {
            margin: 0;
            overflow: hidden;
            font-family: Arial, sans-serif;
            background-color: #222;
            color: white;
        }
        #game {
            position: relative;
            width: 100%;
            height: 100vh;
            background-color: #333;
        }
        #player {
            position: absolute;
            bottom: 20px;
            left: 50%;
            width: 50px;
            height: 50px;
            background-color: #4CAF50;
            transform: translateX(-50%);
        }
        .ball {
            position: absolute;
            width: 30px;
            height: 30px;
            background-color: #FF5252;
            border-radius: 50%;
        }
        #score {
            position: absolute;
            top: 10px;
            left: 10px;
            font-size: 1.5em;
        }
        #game-over {
            position: absolute;
            top: 50%;
            left: 50%;
            transform: translate(-50%, -50%);
            font-size: 2em;
            display: none;
        }
    </style>
</head>
<body>
    <div id="game">
        <div id="score">得分: 0</div>
        <div id="player"></div>
        <div id="game-over">游戏结束！</div>
    </div>

    <script>
        const game = document.getElementById('game');
        const player = document.getElementById('player');
        const scoreElement = document.getElementById('score');
        const gameOverElement = document.getElementById('game-over');

        let score = 0;
        let gameInterval;
        let ballSpeed = 2;
        let gameActive = true;

        // 移动玩家
        document.addEventListener('mousemove', (e) => {
            if (gameActive) {
                const gameWidth = game.offsetWidth;
                const playerWidth = player.offsetWidth;
                let x = e.clientX - game.getBoundingClientRect().left - playerWidth / 2;

                // 限制玩家移动范围
                if (x < 0) x = 0;
                if (x > gameWidth - playerWidth) x = gameWidth - playerWidth;

                player.style.left = `${x}px`;
            }
        });

        // 创建小球
        function createBall() {
            if (!gameActive) return;

            const ball = document.createElement('div');
            ball.classList.add('ball');
            ball.style.left = `${Math.random() * (game.offsetWidth - 30)}px`;
            game.appendChild(ball);

            let ballY = 0;
            const ballInterval = setInterval(() => {
                if (!gameActive) {
                    clearInterval(ballInterval);
                    return;
                }

                ballY += ballSpeed;
                ball.style.top = `${ballY}px`;

                // 检测碰撞
                if (ballY + 30 >= game.offsetHeight - 50 && 
                    ball.offsetLeft + 30 >= player.offsetLeft && 
                    ball.offsetLeft <= player.offsetLeft + 50) {
                    endGame();
                }

                // 移除超出屏幕的小球
                if (ballY > game.offsetHeight) {
                    ball.remove();
                    score++;
                    scoreElement.textContent = `得分: ${score}`;
                    if (score % 5 === 0) ballSpeed += 0.5; // 每得5分增加难度
                }
            }, 10);
        }

        // 结束游戏
        function endGame() {
            gameActive = false;
            gameOverElement.style.display = 'block';
            clearInterval(gameInterval);
        }

        // 开始游戏
        gameInterval = setInterval(createBall, 1000);
    </script>
</body>
</html>
