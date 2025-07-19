# tic-toe-game

<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0"/>
  <title>Tic Tac Toe</title>
  <style>
    /* Har element ka box-size theek karna aur margin/padding hata dena */
    * {
      box-sizing: border-box;
      margin: 0;
      padding: 0;
    }

    /* Body ka design, background aur center alignment */
    body {
      height: 100vh;
      display: flex;
      justify-content: center;
      align-items: center;
      background: linear-gradient(135deg, #74ebd5, #ACB6E5);
      font-family: 'Segoe UI', sans-serif;
    }

    /* Center mein sab kuch rakhne ke liye container */
    .container {
      text-align: center;
    }

    /* Heading ke liye styling */
    h1 {
      font-size: 3em;
      margin-bottom: 30px;
      color: #fff;
      text-shadow: 2px 2px 4px rgba(0,0,0,0.5);
    }

    /* 3x3 board grid banai gayi hai */
    .board {
      display: grid;
      grid-template-columns: repeat(3, 100px);
      grid-template-rows: repeat(3, 100px);
      gap: 10px;
      margin: 0 auto;
    }

    /* Har cell ka design */
    .cell {
      background: rgba(255, 255, 255, 0.9);
      border-radius: 10px;
      display: flex;
      justify-content: center;
      align-items: center;
      font-size: 3rem;
      color: #333;
      cursor: pointer;
      transition: all 0.3s ease;
      user-select: none;
      box-shadow: 0 5px 10px rgba(0,0,0,0.2);
    }

    /* Hover effect jab user mouse lekar aaye */
    .cell:hover {
      transform: scale(1.1);
      background: #ffffff;
    }

    /* Agar cell mein X ho to red color dikhana */
    .cell.X {
      color: #ff3b30;
    }

    /* Agar cell mein O ho to blue color dikhana */
    .cell.O {
      color: #007aff;
    }

    /* Game khatam hone ke baad jo message show hota hai */
    .winning-message {
      position: fixed;
      top: 0;
      left: 0;
      height: 100%;
      width: 100%;
      background: rgba(0,0,0,0.6);
      display: flex;
      justify-content: center;
      align-items: center;
      opacity: 0;
      pointer-events: none;
      transition: opacity 0.3s ease;
    }

    /* Jab jeet ya draw ho to ye class message dikhayegi */
    .winning-message.show {
      opacity: 1;
      pointer-events: all;
    }

    /* Message box ka design */
    .message-box {
      background: #fff;
      padding: 30px 50px;
      border-radius: 10px;
      text-align: center;
      box-shadow: 0 5px 15px rgba(0,0,0,0.3);
    }

    /* Message ka text */
    .message-box p {
      font-size: 2em;
      margin-bottom: 20px;
      color: #333;
    }

    /* Play Again button ka design */
    .message-box button {
      padding: 10px 25px;
      font-size: 1.2em;
      background: linear-gradient(45deg, #74ebd5, #ACB6E5);
      border: none;
      color: #fff;
      border-radius: 5px;
      cursor: pointer;
      transition: transform 0.2s ease;
    }

    /* Button hover effect */
    .message-box button:hover {
      transform: scale(1.1);
    }
  </style>
</head>
<body>
  <!-- Game ka container -->
  <div class="container">
    <h1>Tic Tac Toe</h1>
    
    <!-- Game ka board jisme 9 cells hain -->
    <div class="board" id="board">
      <div class="cell" data-cell></div>
      <div class="cell" data-cell></div>
      <div class="cell" data-cell></div>
      <div class="cell" data-cell></div>
      <div class="cell" data-cell></div>
      <div class="cell" data-cell></div>
      <div class="cell" data-cell></div>
      <div class="cell" data-cell></div>
      <div class="cell" data-cell></div>
    </div>
  </div>

  <!-- Win ya draw ka message dikhane wala box -->
  <div class="winning-message" id="winningMessage">
    <div class="message-box">
      <p id="messageText"></p>
      <button id="restartButton">Play Again</button>
    </div>
  </div>

  <script>
    // Sab cells, board aur buttons ko JS mein lena
    const cells = document.querySelectorAll('[data-cell]');
    const board = document.getElementById('board');
    const winningMessage = document.getElementById('winningMessage');
    const messageText = document.getElementById('messageText');
    const restartButton = document.getElementById('restartButton');

    // Jeetne ke possible combinations
    const WINNING_COMBINATIONS = [
      [0, 1, 2],  // pehli row
      [3, 4, 5],  // doosri row
      [6, 7, 8],  // teesri row
      [0, 3, 6],  // pehla column
      [1, 4, 7],  // doosra column
      [2, 5, 8],  // teesra column
      [0, 4, 8],  // diagonal
      [2, 4, 6]   // doosra diagonal
    ];

    let isCircleTurn; // ye check karta hai k X ki bari hai ya O ki

    // Game shuru karne ka kaam
    startGame();

    // Button click hone par game restart karo
    restartButton.addEventListener('click', startGame);

    function startGame() {
      isCircleTurn = false; // pehli bari X ki hoti hai

      // Har cell ko reset karo
      cells.forEach(cell => {
        cell.classList.remove('X');
        cell.classList.remove('O');
        cell.textContent = '';
        cell.removeEventListener('click', handleClick);
        cell.addEventListener('click', handleClick, { once: true }); // sirf 1 dafa click chale
      });

      // Jeet ka message chhupa do
      winningMessage.classList.remove('show');
    }

    // Jab user kisi cell pe click kare
    function handleClick(e) {
      const cell = e.target;
      const currentClass = isCircleTurn ? 'O' : 'X'; // ab kiski bari hai

      placeMark(cell, currentClass); // mark lagao cell pe

      // Check karo agar jeet gaya koi
      if (checkWin(currentClass)) {
        endGame(false, currentClass); // jeet gaya
      } 
      // Agar board bhar gaya aur koi nahi jeeta
      else if (isDraw()) {
        endGame(true); // draw ho gaya
      } 
      else {
        isCircleTurn = !isCircleTurn; // ab doosre ki bari
      }
    }

    // Cell mein X ya O lagana
    function placeMark(cell, currentClass) {
      cell.classList.add(currentClass);
      cell.textContent = currentClass;
    }

    // Check karo koi player jeeta ya nahi
    function checkWin(currentClass) {
      return WINNING_COMBINATIONS.some(combination => {
        return combination.every(index => {
          return cells[index].classList.contains(currentClass);
        });
      });
    }

    // Check karo draw to nahi hua
    function isDraw() {
      return [...cells].every(cell => {
        return cell.classList.contains('X') || cell.classList.contains('O');
      });
    }

    // Game end hone par result dikhana
    function endGame(draw, winner) {
      if (draw) {
        messageText.textContent = "It's a Draw!";
      } else {
        messageText.textContent = `${winner} Wins!`;
      }
      winningMessage.classList.add('show'); // message dikhao
    }
  </script>
</body>
</html>
