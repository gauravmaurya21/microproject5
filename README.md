

import  './TicTacToe.css';
import React, { useState, useEffect } from 'react';


const TicTacToe = ({ choice }) => {
  const initialBoard = Array(9).fill(null);
  const [board, setBoard] = useState(initialBoard);
  const [xIsNext, setXIsNext] = useState(true);
  const [winner, setWinner] = useState(null);
  const [score, setScore] = useState({ human: 0, computer: 0, tie:0 });
 

  useEffect(() => {
    const storedScore = JSON.parse(localStorage.getItem('ticTacToeScore')) || { human: 0, computer: 0, tie:0 };
    setScore(storedScore);
  }, []);

  useEffect(() => {
    if (winner) {
      const newScore = { ...score };
      if (winner === 'X') {
        newScore.human += 1;
      } else if (winner === 'O') {
        newScore.computer += 1;
      }
      else if(winner==="tie") {
         newScore.tie +=1;
      }
      setScore(newScore);
      localStorage.setItem('ticTacToeScore', JSON.stringify(newScore));
    }
  }, [winner]);

  useEffect(() => {
    if (!xIsNext) {
      const computerMoveTimeout = setTimeout(() => makeComputerMove(), 500);
      return () => clearTimeout(computerMoveTimeout);
    }
  }, [xIsNext]);

  const handleClick = (index) => {
    if (winner || board[index]) {
      return;
    }

    const newBoard = [...board];
    newBoard[index] = choice ? 'X' : 'O'; // Human player always plays 'X'

    setBoard(newBoard);
    setXIsNext(!xIsNext);
    setWinner(calculateWinner(newBoard));
  };



  const makeComputerMove = () => {
    if (winner || board.every((square) => square !== null)) {
      return;
    }

    const availableMoves = board
      .map((square, index) => (square === null ? index : null))
      .filter((move) => move !== null);

    // Check if computer can win in the next move
    for (let i = 0; i < availableMoves.length; i++) {
      const testMove = availableMoves[i];
      const testBoard = [...board];
      testBoard[testMove] = 'O';
      if (calculateWinner(testBoard) === 'O') {
        newMove(testMove);
        return;
      }
    }

    // Check if human can win in the next move and block it
    for (let i = 0; i < availableMoves.length; i++) {
      const testMove = availableMoves[i];
      const testBoard = [...board];
      testBoard[testMove] = 'X';
      if (calculateWinner(testBoard) === 'X') {
        newMove(testMove);
        return;
      }
    }

    // If no winning move, make a random move
    const randomIndex = Math.floor(Math.random() * availableMoves.length);
    newMove(availableMoves[randomIndex]);
  };

  const newMove = (index) => {
    const newBoard = [...board];
    newBoard[index] = !choice ? 'X' : 'O';
    
    setBoard(newBoard);
    setXIsNext(!xIsNext);
    setWinner(calculateWinner(newBoard));
  };

  const handleRefresh = () => {
    setBoard(initialBoard);
    setXIsNext(true);
    setWinner(null);
  };

  const renderSquare = (index) => (
    <button className="square" onClick={() => handleClick(index)}>
      {board[index]}
    </button>
  );

  const currentPlayer = xIsNext ? 'X' : 'O';

  const status = winner
    ? `Winner: ${winner}`
    : board.every((square) => square !== null)
    ? "It's a draw!"
    
    : `Next player: ${currentPlayer}`;

   
   

  return (
    
    <div>
      <div className="status"> {status}</div>


      <div className="board grid-container " >
        {[0, 1, 2].map((row) => (
          <div key={row} className="board-row grid-item">
            {[0, 1, 2].map((col) => renderSquare(row * 3 + col))}
          </div>
        ))}
      </div>
      <div className="score">
        <p>Human: {score.human}</p>
        <p>Computer: {score.computer}</p>
        <p>Tie:{score.tie}</p>
      </div>
      <button onClick={handleRefresh}>Refresh</button>
      <div>{choice}</div>
    </div>
  );
};

const calculateWinner = (squares) => {
  const lines = [
    [0, 1, 2],
    [3, 4, 5],
    [6, 7, 8],
    [0, 3, 6],
    [1, 4, 7],
    [2, 5, 8],
    [0, 4, 8],
    [2, 4, 6],
  ];

 



  for (let i = 0; i < lines.length; i++) {
    const [a, b, c] = lines[i];
    if (squares[a] && squares[a] === squares[b] && squares[a] === squares[c]) {
      return squares[a];
    }
  }

  return squares.every((square) => square !== null) ? "tie" : null;
};



export default TicTacToe;



