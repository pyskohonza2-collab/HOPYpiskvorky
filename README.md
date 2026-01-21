<!DOCTYPE html>
<html lang="cs">
<head>
<meta charset="UTF-8">
<title>Online Piškvorky + Bot</title>

<script src="https://unpkg.com/peerjs@1.5.2/dist/peerjs.min.js"></script>

<style>
body{
    margin:0;
    font-family:Segoe UI,Arial;
    background:radial-gradient(circle at top,#3a0ca3,#000 70%);
    color:white;
    text-align:center;
    overflow-x: hidden;
}

header{
    padding:25px;
    background:rgba(0,0,0,.7);
}

header h1{
    color:#e0aaff;
    text-shadow:0 0 20px #a855f7;
}

button{
    padding:12px 30px;
    margin:10px;
    border:none;
    border-radius:30px;
    background:#9d4edd;
    color:white;
    font-size:16px;
    cursor:pointer;
    transition: 0.3s;
}

button:hover {
    background: #c77dff;
    transform: scale(1.05);
}

.bot-btn {
    background: #3f37c9;
}

input{
    padding:12px;
    border-radius:20px;
    border:none;
    text-align:center;
    font-size:16px;
    width:160px;
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

#code{
    margin-top:15px;
    font-size:22px;
    color:#c77dff;
    font-weight:bold;
}

#winOverlay {
    display: none;
    position: fixed;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    background: rgba(0,0,0,0.85);
    z-index: 1000;
    justify-content: center;
    align-items: center;
    flex-direction: column;
}

#winOverlay img {
    max-width: 90%;
    max-height: 70%;
    border-radius: 20px;
    box-shadow: 0 0 50px #e0aaff;
    border: 5px solid #9d4edd;
}

#winOverlay h2 {
    font-size: 40px;
    margin-bottom: 20px;
    color: #e0aaff;
    text-shadow: 0 0 10px #a855f7;
}
</style>
</head>

<body>

<div id="winOverlay">
    <h2 id="winText">🎉 VYHRÁL JSI! 🎉</h2>
    <img src="image_0.png" alt="Vítězná fotka">
</div>

<header>
    <h1>🎮 Online Piškvorky</h1>
    <button onclick="openYT()">📺 My YouTube Channel</button>
</header>

<br>

<div id="menu">
    <button onclick="createRoom()">➕ Vytvořit místnost</button>
    <button class="bot-btn" onclick="startBotGame()">🤖 Hrát s botem</button>
    <br>
    <input id="joinCode" placeholder="4místný kód" maxlength="4">
    <button onclick="joinRoom()">➡️ Připojit se k hráči</button>
</div>

<p id="code"></p>
<h2 id="status">Vyberte si režim hry</h2>

<div class="board">
    <div class="cell" onclick="play(0)"></div>
    <div class="cell" onclick="play(1)"></div>
    <div class="cell" onclick="play(2)"></div>
    <div class="cell" onclick="play(3)"></div>
    <div class="cell" onclick="play(4)"></div>
    <div class="cell" onclick="play(5)"></div>
    <div class="cell" onclick="play(6)"></div>
    <div class="cell" onclick="play(7)"></div>
    <div class="cell" onclick="play(8)"></div>
</div>

<script>
let peer;
let conn;
let mySymbol;
let board = Array(9).fill("");
let turn = "X";
let isBotMode = false;

function openYT(){
    window.open("https://www.youtube.com/channel/UCoIl4gO_SxWYUGktSmdM8kw", "_blank");
}

/* REŽIM BOT */
function startBotGame() {
    resetGame();
    isBotMode = true;
    mySymbol = "X";
    turn = "X";
    document.getElementById("status").innerText = "Hraješ proti botovi (jsi X)";
    document.getElementById("code").innerText = "🤖 Režim: Proti botovi";
}

/* CREATE ROOM */
function createRoom(){
    resetGame();
    isBotMode = false;
    const roomCode = Math.floor(1000 + Math.random() * 9000).toString();
    peer = new Peer(roomCode);
    peer.on("open", id => {
        document.getElementById("code").innerText = "🟢 KÓD MÍSTNOSTI: " + id;
        mySymbol = "X";
        document.getElementById("status").innerText = "Čeká se na druhého hráče...";
    });
    peer.on("connection", c => {
        conn = c;
        setup();
        document.getElementById("status").innerText = "Hraješ za X – tvůj tah";
    });
}

/* JOIN ROOM */
function joinRoom(){
    resetGame();
    isBotMode = false;
    const code = document.getElementById("joinCode").value;
    if(code.length !== 4) return alert("Zadej 4místný kód");

    peer = new Peer();
    peer.on("open", () => {
        conn = peer.connect(code);
        mySymbol = "O";
        setup();
        document.getElementById("status").innerText = "Hraješ za O – čekej na tah";
    });
}

function resetGame() {
    board = Array(9).fill("");
    turn = "X";
    document.querySelectorAll(".cell").forEach(c => c.innerText = "");
    if(peer) peer.destroy();
    conn = null;
}

function setup(){
    conn.on("data", d => {
        board[d.i] = d.s;
        document.querySelectorAll(".cell")[d.i].innerText = d.s;
        turn = mySymbol;
        if(!checkWinner()) document.getElementById("status").innerText = "Tvůj tah";
    });
}

/* PLAY */
function play(i){
    if(board[i] || turn !== mySymbol || ( !isBotMode && !conn)) return;

    // Tah hráče
    makeMove(i, mySymbol);

    if (checkWinner()) return;

    if (isBotMode) {
        turn = "O"; // Tah bota
        document.getElementById("status").innerText = "Bot přemýšlí...";
        setTimeout(botMove, 600); // Malá pauza, aby to vypadalo realističtěji
    } else {
        conn.send({i:i, s:mySymbol});
        turn = mySymbol === "X" ? "O" : "X";
        document.getElementById("status").innerText = "Čekej na soupeře";
    }
}

function makeMove(i, symbol) {
    board[i] = symbol;
    document.querySelectorAll(".cell")[i].innerText = symbol;
}

/* LOGIKA BOTA */
function botMove() {
    if (turn !== "O") return;

    // Najít všechna volná políčka
    let available = [];
    board.forEach((val, idx) => {
        if (val === "") available.push(idx);
    });

    if (available.length > 0) {
        // Jednoduchý bot: vybere náhodné pole
        const randomIndex = available[Math.floor(Math.random() * available.length)];
        makeMove(randomIndex, "O");
        
        if (!checkWinner()) {
            turn = "X";
            document.getElementById("status").innerText = "Tvůj tah (X)";
        }
    }
}

/* CHECK WINNER */
function checkWinner(){
    const combos = [
        [0,1,2],[3,4,5],[6,7,8], 
        [0,3,6],[1,4,7],[2,5,8], 
        [0,4,8],[2,4,6]
    ];

    for(const c of combos){
        if(board[c[0]] && board[c[0]] === board[c[1]] && board[c[0]] === board[c[2]]){
            const winner = board[c[0]];
            
            if(winner === mySymbol){
                document.getElementById("status").innerText = "🎉 Vyhrál jsi!";
                showWinOverlay("🎉 VYHRÁL JSI! 🎉");
            } else {
                document.getElementById("status").innerText = "💀 Prohrál jsi!";
                // Pokud chceš ukázat fotku i při prohře, můžeš zavolat showWinOverlay s jiným textem
            }
            turn = null; 
            return true;
        }
    }

    if(board.every(cell => cell)){
        document.getElementById("status").innerText = "🤝 Remíza!";
        turn = null;
        return true;
    }

    return false;
}

function showWinOverlay(text) {
    const overlay = document.getElementById("winOverlay");
    document.getElementById("winText").innerText = text;
    overlay.style.display = "flex";
    setTimeout(() => {
        overlay.style.display = "none";
    }, 4000);
}
</script>

</body>
</html>
