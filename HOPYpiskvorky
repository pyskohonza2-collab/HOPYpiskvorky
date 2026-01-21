<!DOCTYPE html>
<html lang="cs">
<head>
<meta charset="UTF-8">
<title>Online Piškvorky</title>

<script src="https://unpkg.com/peerjs@1.5.2/dist/peerjs.min.js"></script>

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

header button{
    margin-top:10px;
    padding:10px 25px;
    border:none;
    border-radius:25px;
    background:#9d4edd;
    color:white;
    cursor:pointer;
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
</style>
</head>

<body>

<header>
    <h1>🎮 Online Piškvorky</h1>
    <button onclick="openYT()">📺 My YouTube Channel</button>
</header>

<br>

<button onclick="createRoom()">➕ Vytvořit místnost</button><br>

<input id="joinCode" placeholder="4místný kód" maxlength="4">
<button onclick="joinRoom()">➡️ Připojit</button>

<p id="code"></p>
<h2 id="status"></h2>

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

/* YOUTUBE */
function openYT(){
    window.open(
        "https://www.youtube.com/channel/UCoIl4gO_SxWYUGktSmdM8kw",
        "_blank"
    );
}

/* CREATE ROOM */
function createRoom(){
    const roomCode = Math.floor(1000 + Math.random() * 9000).toString();

    peer = new Peer(roomCode);
    peer.on("open", id => {
        document.getElementById("code").innerText =
            "🟢 KÓD MÍSTNOSTI: " + id;
        mySymbol = "X";
        document.getElementById("status").innerText =
            "Čeká se na druhého hráče...";
    });

    peer.on("connection", c => {
        conn = c;
        setup();
        document.getElementById("status").innerText =
            "Hraješ za X – tvůj tah";
    });
}

/* JOIN ROOM */
function joinRoom(){
    const code = document.getElementById("joinCode").value;
    if(code.length !== 4) return alert("Zadej 4místný kód");

    peer = new Peer();
    peer.on("open", () => {
        conn = peer.connect(code);
        mySymbol = "O";
        setup();
        document.getElementById("status").innerText =
            "Hraješ za O – čekej na tah";
    });
}

/* CONNECTION */
function setup(){
    conn.on("data", d => {
        board[d.i] = d.s;
        document.querySelectorAll(".cell")[d.i].innerText = d.s;
        turn = mySymbol;
        document.getElementById("status").innerText = "Tvůj tah";
    });
}

/* PLAY */
function play(i){
    if(board[i] || turn !== mySymbol || !conn) return;

    board[i] = mySymbol;
    document.querySelectorAll(".cell")[i].innerText = mySymbol;
    conn.send({i:i,s:mySymbol});
    turn = mySymbol === "X" ? "O" : "X";
    document.getElementById("status").innerText = "Čekej na soupeře";
}
</script>

</body>
</html>
