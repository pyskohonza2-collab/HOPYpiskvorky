<!DOCTYPE html>
<html lang="cs">
<head>
<meta charset="UTF-8">
<title>Piškvorky – Hopy</title>

<style>
body{
    margin:0;
    font-family:Segoe UI,Arial;
    background:radial-gradient(circle at top,#3a0ca3,#000 70%);
    color:white;
    text-align:center;
}

header{
    padding:25px;
    background:rgba(0,0,0,.7);
}

header h1{
    color:#e0aaff;
    text-shadow:0 0 20px #a855f7;
}

.board{
    display:grid;
    grid-template-columns:repeat(3,110px);
    gap:15px;
    justify-content:center;
    margin:40px auto;
}

.cell{
    width:110px;
    height:110px;
    background:rgba(255,255,255,.08);
    border-radius:20px;
    font-size:60px;
    display:flex;
    align-items:center;
    justify-content:center;
    cursor:pointer;
    box-shadow:0 0 20px rgba(160,0,255,.6);
}

#status{
    font-size:22px;
    margin-top:15px;
}

button{
    padding:12px 30px;
    border:none;
    border-radius:30px;
    background:#9d4edd;
    color:white;
    font-size:16px;
    cursor:pointer;
}

input{
    padding:10px;
    border-radius:20px;
    border:none;
    font-size:16px;
    text-align:center;
}
</style>
</head>

<body>

<header>
    <h1>🎮 Piškvorky</h1>
</header>

<br>

<input id="nameInput" placeholder="Zadej jméno (např. Hopy)">
<br><br>
<button onclick="startGame()">▶️ Start</button>

<h2 id="status">Zadej jméno a klikni Start</h2>

<div class="board">
    <div class="cell" onclick="playerMove(0)"></div>
    <div class="cell" onclick="playerMove(1)"></div>
    <div class="cell" onclick="playerMove(2)"></div>
    <div class="cell" onclick="playerMove(3)"></div>
    <div class="cell" onclick="playerMove(4)"></div>
    <div class="cell" onclick="playerMove(5)"></div>
    <div class="cell" onclick="playerMove(6)"></div>
    <div class="cell" onclick="playerMove(7)"></div>
    <div class="cell" onclick="playerMove(8)"></div>
</div>

<button onclick="resetGame()">🔄 Reset</button>

<script>
let board = Array(9).fill("");
let gameOver = false;
let playerName = "Hopy";
let gameStarted = false;

const winCombos = [
    [0,1,2],[3,4,5],[6,7,8],
    [0,3,6],[1,4,7],[2,5,8],
    [0,4,8],[2,4,6]
];

function startGame(){
    const input = document.getElementById("nameInput").value.trim();
    playerName = input ? input : "Hopy";
    resetGame();
    gameStarted = true;
    document.getElementById("status").innerText = `${playerName} je na tahu`;
}

function playerMove(i){
    if(!gameStarted || board[i] || gameOver) return;
    board[i] = "X";
    render();
    if(checkEnd()) return;
    document.getElementById("status").innerText = "Bot přemýšlí…";
    setTimeout(botMove, 350);
}

function botMove(){
    let moves = [];

    for(let i=0;i<9;i++){
        if(board[i] === ""){
            board[i] = "O";
            let score = minimax(board,0,false);
            board[i] = "";
            moves.push({i, score});
        }
    }

    moves.sort((a,b)=>b.score - a.score);

    const mistakeChance = 0.06; // BOT JE TĚŽKÝ, ALE PORAZITELNÝ
    let choice = Math.random() < mistakeChance && moves.length > 1
        ? moves[1].i
        : moves[0].i;

    board[choice] = "O";
    render();
    checkEnd();
}

function minimax(b, depth, isMax){
    let res = winner();
    if(res !== null){
        return res === "O" ? 10-depth : res === "X" ? depth-10 : 0;
    }

    if(isMax){
        let best = -Infinity;
        for(let i=0;i<9;i++){
            if(b[i] === ""){
                b[i] = "O";
                best = Math.max(best, minimax(b, depth+1, false));
                b[i] = "";
            }
        }
        return best;
    } else {
        let best = Infinity;
        for(let i=0;i<9;i++){
            if(b[i] === ""){
                b[i] = "X";
                best = Math.min(best, minimax(b, depth+1, true));
                b[i] = "";
            }
        }
        return best;
    }
}

function winner(){
    for(let c of winCombos){
        if(board[c[0]] && board[c[0]] === board[c[1]] && board[c[0]] === board[c[2]]){
            return board[c[0]];
        }
    }
    if(board.every(c => c)) return "draw";
    return null;
}

function checkEnd(){
    let w = winner();
    if(!w){
        document.getElementById("status").innerText = `${playerName} je na tahu`;
        return false;
    }

    gameOver = true;
    document.getElementById("status").innerText =
        w === "draw" ? "🤝 Remíza!" :
        w === "X" ? `🎉 ${playerName} vyhrál!` : `💀 ${playerName} prohrál!`;
    return true;
}

function render(){
    document.querySelectorAll(".cell").forEach((c,i)=>{
        c.innerText = board[i];
    });
}

function resetGame(){
    board = Array(9).fill("");
    gameOver = false;
    render();
}
</script>

</body>
</html>
