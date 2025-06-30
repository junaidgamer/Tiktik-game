<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0"/>
  <title>Tik Tik Game junaid</title>
  <style>
    * { box-sizing: border-box; }
    body {
      font-family: sans-serif;
      text-align: center;
      background: #000814;
      color: white;
      margin: 0;
      padding: 0;
      display: flex;
      flex-direction: column;
      min-height: 100vh;
      justify-content: space-between;
    }
    h1 {
      margin: 20px 10px 10px;
      font-size: 6vw;
      color: #00f0ff;
      text-shadow: 0 0 10px #00f0ff;
    }
    .scoreboard {
      font-size: 4vw;
      margin-bottom: 10px;
      color: #00f0ff;
      text-shadow: 0 0 10px #00f0ff;
    }
    .start-screen, .game-screen {
      display: none;
      flex-direction: column;
      align-items: center;
      justify-content: center;
      padding: 20px;
      flex: 1;
    }
    .start-screen.active, .game-screen.active {
      display: flex;
    }
    .start-btn {
      padding: 15px 30px;
      font-size: 1.2em;
      margin: 20px;
      border-radius: 10px;
      border: none;
      cursor: pointer;
      background: #28a745;
      color: white;
      box-shadow: 0 0 15px #28a745;
      position: absolute;
      bottom: 80px;
    }
    .mode-buttons {
      display: flex;
      justify-content: center;
      flex-wrap: wrap;
      margin: 10px;
    }
    .mode-buttons button {
      margin: 5px;
      padding: 12px 20px;
      font-size: 4.5vw;
      background: #001f3f;
      color: white;
      border: none;
      border-radius: 8px;
      cursor: pointer;
      flex: 1 0 40%;
      max-width: 200px;
      box-shadow: 0 0 10px #00f0ff;
    }
    .board {
      display: grid;
      grid-template-columns: repeat(3, 1fr);
      gap: 4vw;
      margin: 20px auto;
      width: 90vw;
      max-width: 360px;
    }
    .cell {
      width: 100%;
      padding-top: 100%;
      background-color: #001f3f;
      font-size: 10vw;
      position: relative;
      border-radius: 12px;
      cursor: pointer;
      border: 4px solid transparent;
      animation: rainbow-border 3s linear infinite;
    }
    .cell span {
      position: absolute;
      top: 50%;
      left: 50%;
      transform: translate(-50%, -50%);
    }
    .status {
      margin: 15px;
      font-size: 5vw;
      color: #00f0ff;
      text-shadow: 0 0 8px #00f0ff;
    }
    button.restart {
      margin-top: 10px;
      padding: 12px 25px;
      font-size: 4.5vw;
      background: #007bff;
      color: white;
      border: none;
      border-radius: 8px;
      cursor: pointer;
      box-shadow: 0 0 10px #00f0ff;
    }
    .signature {
      margin: 30px 0 20px;
      font-size: 1.5em;
      background: linear-gradient(90deg, red, orange, yellow, green, cyan, blue, violet);
      background-clip: text;
      -webkit-background-clip: text;
      color: transparent;
      animation: glow 2s linear infinite;
    }
    @keyframes glow {
      0% { text-shadow: 0 0 5px red; }
      50% { text-shadow: 0 0 20px violet; }
      100% { text-shadow: 0 0 5px red; }
    }
    @keyframes rainbow-border {
      0% { border-color: red; }
      14% { border-color: orange; }
      28% { border-color: yellow; }
      42% { border-color: green; }
      57% { border-color: cyan; }
      71% { border-color: blue; }
      85% { border-color: violet; }
      100% { border-color: red; }
    }
    @media (min-width: 600px) {
      h1 { font-size: 2.5em; }
      .mode-buttons button, button.restart { font-size: 1em; }
      .status { font-size: 1.2em; }
    }
  </style>
</head>
<body>
  <h1>😈 Tik Tik Game 😈</h1>
  <div class="scoreboard" id="scoreBoard">X: 0 | O: 0</div>

  <div class="start-screen active" id="startScreen">
    <button class="start-btn" onclick="startScreen()">Start Game</button>
  </div>

  <div class="game-screen" id="gameScreen">
    <div class="mode-buttons">
      <button onclick="startGame(false)">👫 2 Player</button>
      <button onclick="startGame(true)">🤖 CPU</button>
    </div>
    <div class="board" id="board"></div>
    <div class="status" id="status"></div>
    <button class="restart" onclick="startGame(cpuMode)">🔁 Restart</button>
  </div>

  <div class="signature">Made by Junaid bhai 💙✨</div>

  <script>
    const boardElement = document.getElementById("board");
    const statusElement = document.getElementById("status");
    const gameScreen = document.getElementById("gameScreen");
    const startScreenDiv = document.getElementById("startScreen");
    const scoreBoard = document.getElementById("scoreBoard");

    let board, currentPlayer, gameOver, cpuMode;
    const themeX = "aqua", themeO = "blue";

    let score = {
      X: parseInt(localStorage.getItem("scoreX")) || 0,
      O: parseInt(localStorage.getItem("scoreO")) || 0
    };

    function updateScoreBoard() {
      scoreBoard.textContent = `X: ${score.X} | O: ${score.O}`;
    }

    function startScreen() {
      startScreenDiv.classList.remove("active");
      gameScreen.classList.add("active");
      startGame();
    }

    function startGame(vsCPU = false) {
      cpuMode = vsCPU;
      board = Array(9).fill("");
      currentPlayer = "X";
      gameOver = false;
      renderBoard();
      updateStatus();
      updateScoreBoard();
    }

    function renderBoard() {
      boardElement.innerHTML = "";
      board.forEach((cell, i) => {
        const cellElement = document.createElement("div");
        cellElement.classList.add("cell");
        if (cell) {
          const color = cell === "X" ? themeX : themeO;
          cellElement.innerHTML = `<span style="color:${color}; text-shadow: 0 0 10px ${color}">${cell}</span>`;
        } else {
          cellElement.innerHTML = `<span></span>`;
        }
        cellElement.onclick = () => handleMove(i);
        boardElement.appendChild(cellElement);
      });
    }

    function handleMove(index) {
      if (board[index] || gameOver) return;
      board[index] = currentPlayer;
      renderBoard();
      if (checkWinner(currentPlayer)) {
        statusElement.textContent = `${currentPlayer} wins! 🎉`;
        score[currentPlayer]++;
        localStorage.setItem("scoreX", score.X);
        localStorage.setItem("scoreO", score.O);
        gameOver = true;
        updateScoreBoard();
      } else if (board.every(cell => cell)) {
        statusElement.textContent = "It's a draw!";
        gameOver = true;
      } else {
        currentPlayer = currentPlayer === "X" ? "O" : "X";
        updateStatus();
        if (cpuMode && currentPlayer === "O" && !gameOver) {
          setTimeout(cpuMove, 300);
        }
      }
    }

    function cpuMove() {
      const availableMoves = board.map((v, i) => v === "" ? i : null).filter(i => i !== null);
      const randomMove = availableMoves[Math.floor(Math.random() * availableMoves.length)];
      handleMove(randomMove);
    }

    function updateStatus() {
      statusElement.textContent = `${currentPlayer}'s turn ${cpuMode && currentPlayer === 'O' ? '(CPU)' : ''}`;
    }

    function checkWinner(player) {
      const wins = [
        [0,1,2], [3,4,5], [6,7,8],
        [0,3,6], [1,4,7], [2,5,8],
        [0,4,8], [2,4,6]
      ];
      return wins.some(indices => indices.every(i => board[i] === player));
    }

    updateScoreBoard(); // Initial call to show score if any
  </script>
</body>
</html>
