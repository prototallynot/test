## 功能名稱
 - 為貪食蛇遊戲添加簡單計分板、最高分記錄和暫停功能

## 為什麼要加
 - 解決計分需求：玩家需要知道自己得了多少分，這是遊戲的基本反饋機制

 - 增加挑戰樂趣：最高分記錄讓玩家有明確目標，想要打破自己的紀錄

 - 提升遊戲體驗：暫停功能讓玩家可以短暫休息，減少遊戲壓力

 - 基本競技元素：有分數才有競爭，有競爭才更有趣

## 具體要求
 - 計分顯示：在畫面上方顯示當前分數
 - 暫停按鈕：點擊按鈕可暫停/繼續遊戲
 - 簡單設計：UI保持簡潔，不添加複雜特效

## 現有代碼
<!DOCTYPE html>
<html lang="zh-TW">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>HTML5 貪食蛇小遊戲</title>
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
        }
        
        body {
            display: flex;
            flex-direction: column;
            align-items: center;
            justify-content: center;
            min-height: 100vh;
            background: linear-gradient(135deg, #1a1a2e 0%, #16213e 100%);
            color: #fff;
            padding: 20px;
        }
        
        .header {
            text-align: center;
            margin-bottom: 20px;
        }
        
        h1 {
            font-size: 2.8rem;
            margin-bottom: 10px;
            background: linear-gradient(90deg, #00b4db, #0083b0);
            -webkit-background-clip: text;
            background-clip: text;
            color: transparent;
            text-shadow: 0 2px 5px rgba(0, 0, 0, 0.2);
        }
        
        .subtitle {
            color: #a1c4fd;
            font-size: 1.1rem;
            margin-bottom: 25px;
        }
        
        .game-container {
            display: flex;
            flex-wrap: wrap;
            justify-content: center;
            gap: 30px;
            max-width: 1200px;
            width: 100%;
        }
        
        .left-panel {
            flex: 1;
            min-width: 300px;
            background: rgba(255, 255, 255, 0.08);
            border-radius: 15px;
            padding: 25px;
            backdrop-filter: blur(10px);
            border: 1px solid rgba(255, 255, 255, 0.1);
            box-shadow: 0 10px 30px rgba(0, 0, 0, 0.3);
        }
        
        .game-board {
            position: relative;
            border-radius: 10px;
            overflow: hidden;
            box-shadow: 0 10px 30px rgba(0, 0, 0, 0.5);
        }
        
        canvas {
            background-color: #0d1b2a;
            display: block;
        }
        
        .stats {
            display: flex;
            justify-content: space-between;
            margin-bottom: 25px;
            background: rgba(0, 0, 0, 0.3);
            padding: 15px;
            border-radius: 10px;
        }
        
        .stat-item {
            text-align: center;
        }
        
        .stat-label {
            font-size: 0.9rem;
            color: #8da1b9;
            margin-bottom: 5px;
        }
        
        .stat-value {
            font-size: 2rem;
            font-weight: bold;
            color: #4cc9f0;
        }
        
        .controls {
            margin-bottom: 25px;
        }
        
        h2 {
            font-size: 1.4rem;
            margin-bottom: 15px;
            color: #a1c4fd;
            border-bottom: 2px solid rgba(161, 196, 253, 0.3);
            padding-bottom: 8px;
        }
        
        .difficulty-buttons {
            display: flex;
            gap: 12px;
            flex-wrap: wrap;
        }
        
        .btn {
            padding: 12px 20px;
            border: none;
            border-radius: 8px;
            font-weight: bold;
            cursor: pointer;
            transition: all 0.3s ease;
            font-size: 1rem;
        }
        
        .btn-difficulty {
            flex: 1;
            min-width: 120px;
            background: rgba(255, 255, 255, 0.1);
            color: #fff;
        }
        
        .btn-difficulty:hover {
            background: rgba(255, 255, 255, 0.2);
            transform: translateY(-3px);
        }
        
        .btn-difficulty.active {
            background: #4cc9f0;
            color: #16213e;
            box-shadow: 0 5px 15px rgba(76, 201, 240, 0.4);
        }
        
        .game-buttons {
            display: flex;
            gap: 15px;
            margin-top: 20px;
        }
        
        .btn-primary {
            flex: 1;
            background: linear-gradient(90deg, #00b4db, #0083b0);
            color: white;
        }
        
        .btn-secondary {
            flex: 1;
            background: rgba(255, 255, 255, 0.1);
            color: white;
        }
        
        .btn:hover {
            transform: translateY(-3px);
            box-shadow: 0 7px 15px rgba(0, 0, 0, 0.3);
        }
        
        .btn:active {
            transform: translateY(0);
        }
        
        .instructions {
            margin-top: 25px;
            background: rgba(0, 0, 0, 0.3);
            padding: 20px;
            border-radius: 10px;
        }
        
        .instructions ul {
            list-style-type: none;
            padding-left: 10px;
        }
        
        .instructions li {
            margin-bottom: 10px;
            padding-left: 25px;
            position: relative;
        }
        
        .instructions li:before {
            content: "•";
            color: #4cc9f0;
            font-size: 1.5rem;
            position: absolute;
            left: 0;
            top: -5px;
        }
        
        .key {
            display: inline-block;
            background: rgba(255, 255, 255, 0.1);
            padding: 3px 8px;
            border-radius: 5px;
            font-family: monospace;
            margin: 0 3px;
        }
        
        .game-over {
            position: absolute;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background: rgba(0, 0, 0, 0.85);
            display: flex;
            flex-direction: column;
            justify-content: center;
            align-items: center;
            z-index: 10;
            display: none;
        }
        
        .game-over h3 {
            font-size: 3rem;
            color: #ff4d4d;
            margin-bottom: 20px;
            text-shadow: 0 0 10px rgba(255, 77, 77, 0.7);
        }
        
        .final-score {
            font-size: 2rem;
            margin-bottom: 30px;
            color: #4cc9f0;
        }
        
        @media (max-width: 768px) {
            .game-container {
                flex-direction: column;
            }
            
            .left-panel {
                order: 2;
            }
            
            h1 {
                font-size: 2.2rem;
            }
        }
        
        .footer {
            margin-top: 30px;
            text-align: center;
            color: #8da1b9;
            font-size: 0.9rem;
        }
    </style>
</head>
<body>
    <div class="header">
        <h1>HTML5 貪食蛇</h1>
        <p class="subtitle">經典的貪食蛇遊戲，使用HTML5 Canvas和JavaScript開發</p>
    </div>
    
    <div class="game-container">
        <div class="left-panel">
            <div class="stats">
                <div class="stat-item">
                    <div class="stat-label">分數</div>
                    <div class="stat-value" id="score">0</div>
                </div>
                <div class="stat-item">
                    <div class="stat-label">最高分</div>
                    <div class="stat-value" id="high-score">0</div>
                </div>
                <div class="stat-item">
                    <div class="stat-label">長度</div>
                    <div class="stat-value" id="length">1</div>
                </div>
            </div>
            
            <div class="controls">
                <h2>遊戲控制</h2>
                <div class="difficulty-buttons">
                    <button class="btn btn-difficulty active" data-difficulty="easy">簡單</button>
                    <button class="btn btn-difficulty" data-difficulty="medium">中等</button>
                    <button class="btn btn-difficulty" data-difficulty="hard">困難</button>
                </div>
                
                <div class="game-buttons">
                    <button class="btn btn-primary" id="start-btn">開始遊戲</button>
                    <button class="btn btn-secondary" id="pause-btn">暫停</button>
                    <button class="btn btn-secondary" id="reset-btn">重新開始</button>
                </div>
            </div>
            
            <div class="instructions">
                <h2>遊戲說明</h2>
                <ul>
                    <li>使用 <span class="key">↑</span><span class="key">↓</span><span class="key">←</span><span class="key">→</span> 方向鍵或 <span class="key">W</span><span class="key">A</span><span class="key">S</span><span class="key">D</span> 控制蛇的方向</li>
                    <li>吃到紅色食物可增加長度和分數</li>
                    <li>避免撞到牆壁或自己的身體</li>
                    <li>遊戲速度會隨長度增加而加快</li>
                    <li>點擊"開始遊戲"開始遊玩</li>
                </ul>
            </div>
        </div>
        
        <div class="game-board">
            <canvas id="gameCanvas" width="600" height="600"></canvas>
            <div class="game-over" id="game-over">
                <h3>遊戲結束!</h3>
                <div class="final-score">分數: <span id="final-score">0</span></div>
                <button class="btn btn-primary" id="restart-btn">再玩一次</button>
            </div>
        </div>
    </div>
    
    <div class="footer">
        <p>HTML5 Canvas貪食蛇遊戲 &copy; 2023 | 使用純JavaScript開發</p>
    </div>

    <script>
        // 遊戲變數
        const canvas = document.getElementById('gameCanvas');
        const ctx = canvas.getContext('2d');
        const scoreElement = document.getElementById('score');
        const highScoreElement = document.getElementById('high-score');
        const lengthElement = document.getElementById('length');
        const startBtn = document.getElementById('start-btn');
        const pauseBtn = document.getElementById('pause-btn');
        const resetBtn = document.getElementById('reset-btn');
        const restartBtn = document.getElementById('restart-btn');
        const gameOverScreen = document.getElementById('game-over');
        const finalScoreElement = document.getElementById('final-score');
        const difficultyButtons = document.querySelectorAll('.btn-difficulty');
        
        // 遊戲設置
        const gridSize = 20;
        const gridWidth = canvas.width / gridSize;
        const gridHeight = canvas.height / gridSize;
        
        // 遊戲狀態
        let snake = [];
        let food = {};
        let direction = 'right';
        let nextDirection = 'right';
        let gameSpeed = 150; // 毫秒
        let score = 0;
        let highScore = localStorage.getItem('snakeHighScore') || 0;
        let gameRunning = false;
        let gameLoop;
        let difficulty = 'easy';
        
        // 初始化遊戲
        function initGame() {
            // 初始化蛇 - 起始位置在中央，長度為3
            snake = [
                {x: 10, y: 10},
                {x: 9, y: 10},
                {x: 8, y: 10}
            ];
            
            // 生成第一個食物
            generateFood();
            
            // 重置遊戲狀態
            direction = 'right';
            nextDirection = 'right';
            score = 0;
            updateScore();
            
            // 設置遊戲速度
            setGameSpeed();
            
            // 隱藏遊戲結束畫面
            gameOverScreen.style.display = 'none';
        }
        
        // 根據難度設置遊戲速度
        function setGameSpeed() {
            switch(difficulty) {
                case 'easy':
                    gameSpeed = 150;
                    break;
                case 'medium':
                    gameSpeed = 120;
                    break;
                case 'hard':
                    gameSpeed = 90;
                    break;
            }
        }
        
        // 生成食物
        function generateFood() {
            // 隨機生成食物位置
            food = {
                x: Math.floor(Math.random() * gridWidth),
                y: Math.floor(Math.random() * gridHeight)
            };
            
            // 確保食物不會出現在蛇身上
            for (let segment of snake) {
                if (segment.x === food.x && segment.y === food.y) {
                    return generateFood();
                }
            }
        }
        
        // 繪製遊戲
        function drawGame() {
            // 清除畫布
            ctx.fillStyle = '#0d1b2a';
            ctx.fillRect(0, 0, canvas.width, canvas.height);
            
            // 繪製網格
            drawGrid();
            
            // 繪製蛇
            drawSnake();
            
            // 繪製食物
            drawFood();
        }
        
        // 繪製網格
        function drawGrid() {
            ctx.strokeStyle = 'rgba(255, 255, 255, 0.05)';
            ctx.lineWidth = 1;
            
            // 垂直線
            for (let x = 0; x <= canvas.width; x += gridSize) {
                ctx.beginPath();
                ctx.moveTo(x, 0);
                ctx.lineTo(x, canvas.height);
                ctx.stroke();
            }
            
            // 水平線
            for (let y = 0; y <= canvas.height; y += gridSize) {
                ctx.beginPath();
                ctx.moveTo(0, y);
                ctx.lineTo(canvas.width, y);
                ctx.stroke();
            }
        }
        
        // 繪製蛇
        function drawSnake() {
            // 繪製蛇的每個部分
            for (let i = 0; i < snake.length; i++) {
                const segment = snake[i];
                
                // 蛇頭用不同顏色
                if (i === 0) {
                    ctx.fillStyle = '#4cc9f0';
                } else {
                    // 漸變色身體
                    const intensity = 150 + Math.floor(105 * (i / snake.length));
                    ctx.fillStyle = `rgb(76, ${intensity}, 240)`;
                }
                
                // 繪製圓角矩形
                drawRoundedRect(
                    segment.x * gridSize, 
                    segment.y * gridSize, 
                    gridSize, 
                    gridSize, 
                    4
                );
                
                // 繪製蛇頭眼睛
                if (i === 0) {
                    ctx.fillStyle = '#16213e';
                    
                    // 根據方向繪製眼睛
                    let eyeOffsetX = 0, eyeOffsetY = 0;
                    if (direction === 'right') {
                        eyeOffsetX = gridSize - 6;
                        eyeOffsetY = 5;
                    } else if (direction === 'left') {
                        eyeOffsetX = 4;
                        eyeOffsetY = 5;
                    } else if (direction === 'up') {
                        eyeOffsetX = gridSize / 2 - 1;
                        eyeOffsetY = 4;
                    } else if (direction === 'down') {
                        eyeOffsetX = gridSize / 2 - 1;
                        eyeOffsetY = gridSize - 6;
                    }
                    
                    ctx.beginPath();
                    ctx.arc(
                        segment.x * gridSize + eyeOffsetX,
                        segment.y * gridSize + eyeOffsetY,
                        2, 0, Math.PI * 2
                    );
                    ctx.fill();
                    
                    // 第二隻眼睛
                    if (direction === 'right' || direction === 'left') {
                        ctx.beginPath();
                        ctx.arc(
                            segment.x * gridSize + eyeOffsetX,
                            segment.y * gridSize + gridSize - 5,
                            2, 0, Math.PI * 2
                        );
                        ctx.fill();
                    } else {
                        ctx.beginPath();
                        ctx.arc(
                            segment.x * gridSize + gridSize - 6,
                            segment.y * gridSize + eyeOffsetY,
                            2, 0, Math.PI * 2
                        );
                        ctx.fill();
                    }
                }
            }
        }
        
        // 繪製圓角矩形
        function drawRoundedRect(x, y, width, height, radius) {
            ctx.beginPath();
            ctx.moveTo(x + radius, y);
            ctx.lineTo(x + width - radius, y);
            ctx.quadraticCurveTo(x + width, y, x + width, y + radius);
            ctx.lineTo(x + width, y + height - radius);
            ctx.quadraticCurveTo(x + width, y + height, x + width - radius, y + height);
            ctx.lineTo(x + radius, y + height);
            ctx.quadraticCurveTo(x, y + height, x, y + height - radius);
            ctx.lineTo(x, y + radius);
            ctx.quadraticCurveTo(x, y, x + radius, y);
            ctx.closePath();
            ctx.fill();
        }
        
        // 繪製食物
        function drawFood() {
            // 繪製紅色食物
            ctx.fillStyle = '#ff4757';
            ctx.beginPath();
            ctx.arc(
                food.x * gridSize + gridSize / 2,
                food.y * gridSize + gridSize / 2,
                gridSize / 2 - 2,
                0,
                Math.PI * 2
            );
            ctx.fill();
            
            // 食物高光效果
            ctx.fillStyle = 'rgba(255, 255, 255, 0.7)';
            ctx.beginPath();
            ctx.arc(
                food.x * gridSize + gridSize / 2 - 3,
                food.y * gridSize + gridSize / 2 - 3,
                3,
                0,
                Math.PI * 2
            );
            ctx.fill();
        }
        
        // 更新遊戲狀態
        function updateGame() {
            // 更新蛇的方向
            direction = nextDirection;
            
            // 計算新的蛇頭位置
            const head = {...snake[0]};
            
            switch(direction) {
                case 'up':
                    head.y -= 1;
                    break;
                case 'down':
                    head.y += 1;
                    break;
                case 'left':
                    head.x -= 1;
                    break;
                case 'right':
                    head.x += 1;
                    break;
            }
            
            // 檢查是否撞牆
            if (
                head.x < 0 || 
                head.x >= gridWidth || 
                head.y < 0 || 
                head.y >= gridHeight
            ) {
                gameOver();
                return;
            }
            
            // 檢查是否撞到自己
            for (let segment of snake) {
                if (head.x === segment.x && head.y === segment.y) {
                    gameOver();
                    return;
                }
            }
            
            // 將新頭部添加到蛇
            snake.unshift(head);
            
            // 檢查是否吃到食物
            if (head.x === food.x && head.y === food.y) {
                // 增加分數
                score += 10 * (snake.length - 2);
                updateScore();
                
                // 生成新食物
                generateFood();
                
                // 隨著蛇變長，遊戲速度稍微加快
                if (gameSpeed > 50 && snake.length % 5 === 0) {
                    gameSpeed -= 5;
                }
            } else {
                // 如果沒吃到食物，移除尾部
                snake.pop();
            }
            
            // 重新繪製遊戲
            drawGame();
        }
        
        // 更新分數顯示
        function updateScore() {
            scoreElement.textContent = score;
            lengthElement.textContent = snake.length;
            
            // 更新最高分
            if (score > highScore) {
                highScore = score;
                localStorage.setItem('snakeHighScore', highScore);
                highScoreElement.textContent = highScore;
            }
        }
        
        // 遊戲結束
        function gameOver() {
            gameRunning = false;
            clearInterval(gameLoop);
            
            // 顯示遊戲結束畫面
            gameOverScreen.style.display = 'flex';
            finalScoreElement.textContent = score;
            
            // 更新按鈕文字
            startBtn.textContent = '開始遊戲';
        }
        
        // 開始遊戲
        function startGame() {
            if (!gameRunning) {
                gameRunning = true;
                startBtn.textContent = '遊戲中...';
                
                // 開始遊戲循環
                gameLoop = setInterval(updateGame, gameSpeed);
            }
        }
        
        // 暫停/繼續遊戲
        function togglePause() {
            if (!gameRunning) return;
            
            if (gameLoop) {
                clearInterval(gameLoop);
                gameLoop = null;
                pauseBtn.textContent = '繼續';
            } else {
                gameLoop = setInterval(updateGame, gameSpeed);
                pauseBtn.textContent = '暫停';
            }
        }
        
        // 重置遊戲
        function resetGame() {
            clearInterval(gameLoop);
            gameRunning = false;
            initGame();
            drawGame();
            startBtn.textContent = '開始遊戲';
            pauseBtn.textContent = '暫停';
        }
        
        // 設置難度
        function setDifficulty(level) {
            difficulty = level;
            
            // 更新按鈕狀態
            difficultyButtons.forEach(btn => {
                if (btn.dataset.difficulty === level) {
                    btn.classList.add('active');
                } else {
                    btn.classList.remove('active');
                }
            });
            
            // 設置遊戲速度
            setGameSpeed();
            
            // 如果遊戲正在運行，重新設置遊戲循環
            if (gameRunning) {
                clearInterval(gameLoop);
                gameLoop = setInterval(updateGame, gameSpeed);
            }
        }
        
        // 鍵盤控制
        document.addEventListener('keydown', (e) => {
            // 防止頁面滾動
            if ([37, 38, 39, 40, 87, 65, 83, 68].includes(e.keyCode)) {
                e.preventDefault();
            }
            
            // 根據按鍵設置下一個方向
            switch(e.key) {
                case 'ArrowUp':
                case 'w':
                case 'W':
                    if (direction !== 'down') nextDirection = 'up';
                    break;
                case 'ArrowDown':
                case 's':
                case 'S':
                    if (direction !== 'up') nextDirection = 'down';
                    break;
                case 'ArrowLeft':
                case 'a':
                case 'A':
                    if (direction !== 'right') nextDirection = 'left';
                    break;
                case 'ArrowRight':
                case 'd':
                case 'D':
                    if (direction !== 'left') nextDirection = 'right';
                    break;
                case ' ':
                    // 空格鍵暫停/繼續
                    togglePause();
                    break;
            }
        });
        
        // 事件監聽器
        startBtn.addEventListener('click', startGame);
        pauseBtn.addEventListener('click', togglePause);
        resetBtn.addEventListener('click', resetGame);
        restartBtn.addEventListener('click', resetGame);
        
        // 難度按鈕事件
        difficultyButtons.forEach(btn => {
            btn.addEventListener('click', () => {
                setDifficulty(btn.dataset.difficulty);
            });
        });
        
        // 初始化遊戲
        highScoreElement.textContent = highScore;
        initGame();
        drawGame();
    </script>
</body>
</html>