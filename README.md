<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Bubble Pop Blitz</title>
<style>
    body {
        margin: 0;
        padding: 0;
        display: flex;
        justify-content: center;
        align-items: center;
        height: 100vh;
        background: linear-gradient(135deg, #4B6CB7, #182848);
        font-family: Arial, sans-serif;
    }
    #homeScreen {
        display: flex;
        flex-direction: column;
        align-items: center;
        justify-content: center;
    }
    .title {
        font-size: 36px;
        color: #fff;
        text-shadow: 2px 2px 4px rgba(0, 0, 0, 0.3);
        margin-bottom: 30px;
    }
    #playBtn {
        padding: 10px 20px;
        background-color: #4CAF50;
        color: white;
        border: none;
        border-radius: 5px;
        cursor: pointer;
        font-size: 18px;
        margin-bottom: 20px;
    }
    #playBtn:hover {
        background-color: #45a049;
    }
    #gameOverPanel {
        display: none;
        position: absolute;
        top: 50%;
        left: 50%;
        transform: translate(-50%, -50%);
        background-color: rgba(255, 255, 255, 0.9);
        padding: 20px;
        border-radius: 10px;
        box-shadow: 0 0 10px rgba(0, 0, 0, 0.5);
        text-align: center;
    }
    #homeBtn {
        padding: 10px 20px;
        background-color: #4CAF50;
        color: white;
        border: none;
        border-radius: 5px;
        cursor: pointer;
        font-size: 18px;
        margin-top: 20px;
    }
    #homeBtn:hover {
        background-color: #45a049;
    }
    canvas {
        border: 2px solid #333;
    }
</style>
</head>
<body>
<div id="homeScreen">
    <h1 class="title">Welcome to Bubble Pop Blitz</h1>
    <button id="playBtn">Play</button>
</div>
<canvas id="gameCanvas" width="400" height="600" style="display: none;"></canvas>
<div id="gameOverPanel">
    <h1>Game Over</h1>
    <p>Your Score: <span id="finalScore">0</span></p>
    <button id="homeBtn">Home</button>
</div>
<script>
    const homeScreen = document.getElementById('homeScreen');
    const playBtn = document.getElementById('playBtn');
    const canvas = document.getElementById('gameCanvas');
    const ctx = canvas.getContext('2d');
    const gameOverPanel = document.getElementById('gameOverPanel');
    const finalScoreElement = document.getElementById('finalScore');
    const homeBtn = document.getElementById('homeBtn');

    const bubbleRadius = 20;
    let bubbleSpeed = 0.5; // Decreased bubble speed initially
    const maxBubbles = 20;

    const colorNames = ['Red', 'Green', 'Blue', 'Yellow', 'Orange', 'Purple']; // Add more colors if needed
    let currentColorIndex = 0;

    let bubbles = [];
    let score = 0;

    function drawBubble(x, y, color) {
        ctx.beginPath();
        ctx.arc(x, y, bubbleRadius, 0, Math.PI * 2);
        ctx.fillStyle = color;
        ctx.fill();
        ctx.closePath();
    }

    function drawScore() {
        ctx.font = '24px Arial';
        ctx.fillStyle = '#000';
        ctx.fillText('Score: ' + score, 10, 30);
    }

    function drawColorName() {
        ctx.font = '24px Arial';
        ctx.fillStyle = '#000';
        ctx.fillText('Color: ' + colorNames[currentColorIndex], 10, canvas.height - 20);
    }

    function update() {
        ctx.clearRect(0, 0, canvas.width, canvas.height);
        drawScore();

        for (let i = 0; i < bubbles.length; i++) {
            drawBubble(bubbles[i].x, bubbles[i].y, bubbles[i].color);
            bubbles[i].y -= bubbleSpeed;

            if (bubbles[i].y + bubbleRadius < 0) {
                bubbles.splice(i, 1);
                i--;
            }
        }

        if (bubbles.length < maxBubbles && Math.random() < 0.05) {
            const x = Math.random() * (canvas.width - bubbleRadius * 2) + bubbleRadius;
            const color = colorNames[Math.floor(Math.random() * colorNames.length)];
            bubbles.push({ x, y: canvas.height + bubbleRadius, color });
        }

        drawColorName();
        requestAnimationFrame(update);
    }

    function showGameOverPanel() {
        gameOverPanel.style.display = 'block';
        finalScoreElement.textContent = score;
    }

    function hideGameOverPanel() {
        gameOverPanel.style.display = 'none';
    }

    function resetGame() {
        score = 0;
        bubbles = [];
        bubbleSpeed = 0.5;
        hideGameOverPanel();
    }

    function increaseBubbleSpeed() {
        bubbleSpeed += 0.1;
    }

    function changeColor() {
        currentColorIndex = (currentColorIndex + 1) % colorNames.length;
    }

    function playGame() {
        homeScreen.style.display = 'none';
        canvas.style.display = 'block';
        resetGame();
        update();
    }

    function goHome() {
        homeScreen.style.display = 'flex';
        canvas.style.display = 'none';
        hideGameOverPanel(); // Hide the game over panel when going home
    }

    function playAgain() {
        resetGame();
        update();
    }

    canvas.addEventListener('click', function(event) {
        const rect = canvas.getBoundingClientRect();
        const mouseX = event.clientX - rect.left;
        const mouseY = event.clientY - rect.top;

        let clicked = false;

        for (let i = 0; i < bubbles.length; i++) {
            const dx = mouseX - bubbles[i].x;
            const dy = mouseY - bubbles[i].y;
            const distance = Math.sqrt(dx * dx + dy * dy);

            if (distance < bubbleRadius) {
                clicked = true;
                if (bubbles[i].color !== colorNames[currentColorIndex]) {
                    showGameOverPanel();
                } else {
                    score++;
                    if (score >= 5) {
                        increaseBubbleSpeed();
                    }
                    bubbles.splice(i, 1);
                }
                break;
            }
        }

        if (!clicked) {
            // Do nothing if clicked outside bubbles
        }
    });

    playBtn.addEventListener('click',playGame);
    homeBtn.addEventListener('click', goHome);

    setInterval(changeColor, 10000); //// Change color every 10 seconds
</script>
</body>
</html
