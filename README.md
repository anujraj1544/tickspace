by anuj raj

<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Tic-Tac-Toe</title>
    <style>
        body {
            display: flex;
            flex-direction: column;
            justify-content: center;
            align-items: center;
            height: 100vh;
            margin: 0;
            background-color: #a89275;
            font-family: Arial, sans-serif;
        }
        #gameBoard {
            display: grid;
            grid-template-columns: repeat(3, 100px);
            grid-template-rows: repeat(3, 100px);
            gap: 5px;
            background: url('https://upload.wikimedia.org/wikipedia/commons/2/25/Wood_table_texture.jpg');
            background-size: cover;
            border: 10px solid #5a3825;
            box-shadow: 0px 8px 15px rgba(0, 0, 0, 0.5);
        }
        .cell {
            width: 100px;
            height: 100px;
            display: flex;
            justify-content: center;
            align-items: center;
            background-color: rgba(255, 255, 255, 0.9);
            border: 2px solid #333;
            font-size: 2rem;
            font-weight: bold;
            cursor: pointer;
            box-shadow: inset 0px 4px 6px rgba(0, 0, 0, 0.3);
        }
        .cell.taken {
            pointer-events: none;
        }
        #controls {
            margin-bottom: 20px;
            font-size: 1.2rem;
        }
        #winner {
            margin-top: 20px;
            font-size: 1.5rem;
        }
        button {
            background-color: #5a3825;
            color: white;
            border: none;
            padding: 10px 20px;
            margin: 5px;
            font-size: 1rem;
            cursor: pointer;
            border-radius: 5px;
            box-shadow: 0px 4px 6px rgba(0, 0, 0, 0.3);
        }
        button:hover {
            background-color: #764c34;
        }
    </style>
</head>
<body>
    <div id="controls">
        <button id="twoPlayerMode">2 Player Mode</button>
        <button id="computerMode">Play with Computer</button>
    </div>
    <div>
        <div id="gameBoard"></div>
        <div id="winner"></div>
    </div>
    <script>
        const gameBoard = document.getElementById('gameBoard');
        const winnerDisplay = document.getElementById('winner');
        const twoPlayerModeButton = document.getElementById('twoPlayerMode');
        const computerModeButton = document.getElementById('computerMode');

        let currentPlayer = 'X';
        let board = Array(9).fill(null);
        let isComputerMode = false;

        const winningCombos = [
            [0, 1, 2], [3, 4, 5], [6, 7, 8], // rows
            [0, 3, 6], [1, 4, 7], [2, 5, 8], // columns
            [0, 4, 8], [2, 4, 6]             // diagonals
        ];

        function checkWinner() {
            for (const combo of winningCombos) {
                const [a, b, c] = combo;
                if (board[a] && board[a] === board[b] && board[a] === board[c]) {
                    return board[a];
                }
            }
            return board.includes(null) ? null : 'Draw';
        }

        function handleClick(event) {
            const cell = event.target;
            const index = cell.dataset.index;

            if (!board[index]) {
                board[index] = currentPlayer;
                cell.textContent = currentPlayer;
                cell.classList.add('taken');

                const winner = checkWinner();
                if (winner) {
                    winnerDisplay.textContent = winner === 'Draw' ? 'It\'s a Draw!' : `${winner} Wins!`;
                    gameBoard.querySelectorAll('.cell').forEach(cell => cell.classList.add('taken'));
                } else {
                    currentPlayer = currentPlayer === 'X' ? 'O' : 'X';
                    if (isComputerMode && currentPlayer === 'O') {
                        computerMove();
                    }
                }
            }
        }

        function computerMove() {
            // Check if the computer can win
            for (const combo of winningCombos) {
                const [a, b, c] = combo;
                if (board[a] === 'O' && board[b] === 'O' && !board[c]) return makeMove(c);
                if (board[a] === 'O' && board[c] === 'O' && !board[b]) return makeMove(b);
                if (board[b] === 'O' && board[c] === 'O' && !board[a]) return makeMove(a);
            }

            // Block the player from winning
            for (const combo of winningCombos) {
                const [a, b, c] = combo;
                if (board[a] === 'X' && board[b] === 'X' && !board[c]) return makeMove(c);
                if (board[a] === 'X' && board[c] === 'X' && !board[b]) return makeMove(b);
                if (board[b] === 'X' && board[c] === 'X' && !board[a]) return makeMove(a);
            }

            // Take the center if available
            if (!board[4]) return makeMove(4);

            // Take a corner if available
            const corners = [0, 2, 6, 8];
            for (const corner of corners) {
                if (!board[corner]) return makeMove(corner);
            }

            // Take any remaining spot
            const availableIndexes = board.map((val, index) => (val === null ? index : null)).filter(index => index !== null);
            if (availableIndexes.length > 0) {
                const randomIndex = availableIndexes[0];
                makeMove(randomIndex);
            }
        }

        function makeMove(index) {
            const cell = gameBoard.querySelector(`[data-index='${index}']`);
            board[index] = 'O';
            cell.textContent = 'O';
            cell.classList.add('taken');

            const winner = checkWinner();
            if (winner) {
                winnerDisplay.textContent = winner === 'Draw' ? 'It\'s a Draw!' : `${winner} Wins!`;
                gameBoard.querySelectorAll('.cell').forEach(cell => cell.classList.add('taken'));
            } else {
                currentPlayer = 'X';
            }
        }

        function createBoard() {
            board = Array(9).fill(null);
            gameBoard.innerHTML = '';
            winnerDisplay.textContent = '';
            currentPlayer = 'X';

            for (let i = 0; i < 9; i++) {
                const cell = document.createElement('div');
                cell.classList.add('cell');
                cell.dataset.index = i;
                cell.addEventListener('click', handleClick);
                gameBoard.appendChild(cell);
            }
        }

        twoPlayerModeButton.addEventListener('click', () => {
            isComputerMode = false;
            createBoard();
        });

        computerModeButton.addEventListener('click', () => {
            isComputerMode = true;
            createBoard();
        });

        createBoard();
    </script>
</body>
</html>
