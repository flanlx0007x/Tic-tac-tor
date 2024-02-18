
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Tic-Tac-Toe with AI</title>
<style>
  body {
      display: flex;
      justify-content: center;
      align-items: center;
      height: 100vh;
      margin: 0;
      background-color: #301934; /* สีพื้นหลังแบบมืด */
  }

  .container {
      text-align: center;
  }

  button {
      font-size: 1.5em;
      margin: 10px;
      padding: 10px 20px;
      cursor: pointer;
      background-color: #9c4dcc; /* สีปุ่ม */
      color: white; /* สีข้อความของปุ่ม */
      border: none;
      border-radius: 5px;
  }

  .board {
      display: grid;
      grid-template-columns: repeat(3, 100px);
      grid-template-rows: repeat(3, 100px);
      gap: 2px;
      margin-top: 20px;
  }

  .cell {
      display: flex;
      justify-content: center;
      align-items: center;
      font-size: 3em;
      cursor: pointer;
      background-color: #b385ff; /* สีเซลล์ของเกม */
      color: white; /* สีข้อความของเซลล์ */
  }
</style>
</head>
<body>
<div class="container" id="startMenu">
  <h1>Select Your Symbol</h1>
  <button onclick="startGame('X')">X</button>
  <button onclick="startGame('O')">O</button>
</div>

<div class="container" id="game" style="display: none;">
  <div class="board">
    <div class="cell" id="cell-0-0" onclick="makeMove(0, 0)"></div>
    <div class="cell" id="cell-0-1" onclick="makeMove(0, 1)"></div>
    <div class="cell" id="cell-0-2" onclick="makeMove(0, 2)"></div>
    <div class="cell" id="cell-1-0" onclick="makeMove(1, 0)"></div>
    <div class="cell" id="cell-1-1" onclick="makeMove(1, 1)"></div>
    <div class="cell" id="cell-1-2" onclick="makeMove(1, 2)"></div>
    <div class="cell" id="cell-2-0" onclick="makeMove(2, 0)"></div>
    <div class="cell" id="cell-2-1" onclick="makeMove(2, 1)"></div>
    <div class="cell" id="cell-2-2" onclick="makeMove(2, 2)"></div>
  </div>
</div>
<script>
  let playerSymbol = '';
  let aiSymbol = '';
  let currentPlayer = '';
  let board = [
    ['', '', ''],
    ['', '', ''],
    ['', '', '']
  ];

  function startGame(symbol) {
    playerSymbol = symbol;
    aiSymbol = symbol === 'X' ? 'O' : 'X';
    currentPlayer = 'X'; // AI always starts first in this implementation
    document.getElementById('startMenu').style.display = 'none';
    document.getElementById('game').style.display = 'block';
    if (currentPlayer === aiSymbol) {
      makeAiMove();
    }
  }

  function makeMove(row, col) {
    if (board[row][col] === '' && currentPlayer === playerSymbol) {
      board[row][col] = playerSymbol;
      document.getElementById(`cell-${row}-${col}`).innerText = playerSymbol;
      if (checkWinner(playerSymbol)) {
        alert('You win!');
        resetBoard();
        return;
      } else if (checkDraw()) {
        alert("It's a draw!");
        resetBoard();
        return;
      }
      currentPlayer = aiSymbol;
      makeAiMove();
    }
  }

  function makeAiMove() {
    let bestScore = -Infinity;
    let move;
    for (let i = 0; i < 3; i++) {
      for (let j = 0; j < 3; j++) {
        if (board[i][j] === '') {
          board[i][j] = aiSymbol;
          let score = minimax(board, 0, false);
          board[i][j] = '';
          if (score > bestScore) {
            bestScore = score;
            move = { i, j };
          }
        }
      }
    }
    board[move.i][move.j] = aiSymbol;
    document.getElementById(`cell-${move.i}-${move.j}`).innerText = aiSymbol;
    if (checkWinner(aiSymbol)) {
      alert('AI wins!');
      resetBoard();
      return;
    } else if (checkDraw()) {
      alert("It's a draw!");
      resetBoard();
      return;
    }
    currentPlayer = playerSymbol;
  }

  function minimax(board, depth, isMaximizing) {
    let result = checkResult();
    if (result !== null) {
      return result;
    }
    if (isMaximizing) {
      let bestScore = -Infinity;
      for (let i = 0; i < 3; i++) {
        for (let j = 0; j < 3; j++) {
          if (board[i][j] === '') {
            board[i][j] = aiSymbol;
            let score = minimax(board, depth + 1, false);
            board[i][j] = '';
            bestScore = Math.max(score, bestScore);
          }
        }
      }
      return bestScore;
    } else {
      let bestScore = Infinity;
      for (let i = 0; i < 3; i++) {
        for (let j = 0; j < 3; j++) {
          if (board[i][j] === '') {
            board[i][j] = playerSymbol;
            let score = minimax(board, depth + 1, true);
            board[i][j] = '';
            bestScore = Math.min(score, bestScore);
          }
        }
      }
      return bestScore;
    }
  }

  function checkWinner(symbol) {
    for (let i = 0; i < 3; i++) {
      if (board[i][0] === symbol && board[i][1] === symbol && board[i][2] === symbol) {
        return true;
      }
      if (board[0][i] === symbol && board[1][i] === symbol && board[2][i] === symbol) {
        return true;
      }
    }
    if (board[0][0] === symbol && board[1][1] === symbol && board[2][2] === symbol) {
      return true;
    }
    if (board[0][2] === symbol && board[1][1] === symbol && board[2][0] === symbol) {
      return true;
    }
    return false;
  }

  function checkDraw() {
    for (let i = 0; i < 3; i++) {
      for (let j = 0; j < 3; j++) {
        if (board[i][j] === '') {
          return false;
        }
      }
    }
    return true;
  }

  function resetBoard() {
    playerSymbol = '';
    aiSymbol = '';
    currentPlayer = '';
    board = [
      ['', '', ''],
      ['', '', ''],
      ['', '', '']
    ];
    document.querySelectorAll('.cell').forEach(cell => {
      cell.innerText = '';
    });
    document.getElementById('startMenu').style.display = 'block';
    document.getElementById('game').style.display = 'none';
  }

  function checkResult() {
    for (let i = 0; i < 3; i++) {
      if (board[i][0] === board[i][1] && board[i][1] === board[i][2]) {
        if (board[i][0] === aiSymbol) {
          return 1;
        } else if (board[i][0] === playerSymbol) {
          return -1;
        }
      }
      if (board[0][i] === board[1][i] && board[1][i] === board[2][i]) {
        if (board[0][i] === aiSymbol) {
          return 1;
        } else if (board[0][i] === playerSymbol) {
          return -1;
        }
      }
    }
    if (board[0][0] === board[1][1] && board[1][1] === board[2][2]) {
      if (board[0][0] === aiSymbol) {
        return 1;
      } else if (board[0][0] === playerSymbol) {
        return -1;
      }
    }
    if (board[0][2] === board[1][1] && board[1][1] === board[2][0]) {
      if (board[0][2] === aiSymbol) {
        return 1;
      } else if (board[0][2] === playerSymbol) {
        return -1;
      }
    }
    return checkDraw() ? 0 : null;
  }

</script>
</body>
</html>
