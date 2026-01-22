<!DOCTYPE html>
<html lang="cs">
<head>
<meta charset="UTF-8">
<title>Piškvorky</title>
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<script src="https://unpkg.com/peerjs@1.5.2/dist/peerjs.min.js"></script>
<style>
body{
    margin:0;
    font-family:Arial, Helvetica, sans-serif;
    background:linear-gradient(135deg,#1a001f,#4b0082);
    color:white;
    display:flex;
    justify-content:space-between;
    min-height:100vh;
    overflow:hidden;
}
#leftPanel{
    width:250px;
    background:rgba(0,0,0,0.65);
    padding:20px;
    margin:20px;
    border-radius:15px;
    box-shadow:0 0 25px #9d4edd;
    white-space:pre-line;
    font-size:16px;
}
#rightPanel{
    width:250px;
    background:rgba(0,0,0,0.65);
    padding:20px;
    margin:20px;
    border-radius:15px;
    box-shadow:0 0 25px #9d4edd;
    white-space:pre-line;
    font-size:16px;
    overflow-y:auto;
    max-height:calc(100vh - 40px);
}
.container{
    width:480px;
    background:rgba(0,0,0,0.7);
    border-radius:20px;
    box-shadow:0 0 50px #9d4edd;
    padding:30px;
    text-align:center;
    position:relative;
    margin:20px;
}
h1{
    margin-top:0;
    color:#e0aaff;
    text-shadow:0 0 20px #c77dff;
    font-size:38px;
}
button{
    padding:15px;
    margin:8px 0;
    border:none;
    border-radius:15px;
    font-size:20px;
    cursor:pointer;
    width:95%;
    background:#7b2cbf;
    color:white;
}
button:hover{background:#9d4edd;}
input{
    width:95%;
    padding:15px;
    margin:8px 0;
    border:none;
    border-radius:15px;
    font-size:20px;
    text-align:center;
}
.board{
    display:grid;
    grid-template-columns:repeat(3,1fr);
    gap:15px;
    margin-top:30px;
}
.cell{
    height:130px;
    background:#240046;
    border-radius:15px;
    font-size:64px;
    display:flex;
    align-items:center;
    justify-content:center;
    cursor:pointer;
}
.cell:hover{background:#3c096c;}
.status{
    margin-top:15px;
    min-height:40px;
    font-weight:bold;
    color:#f1c6ff;
    font-size:22px;
}
#backBtn{
    position:absolute;
    top:15px;
    left:15px;
    font-size:22px;
    background:#7b2cbf;
    width:auto;
    padding:6px 12px;
    display:none;
}
#readyStatus{
    font-size:18px;
    opacity:0.85;
    margin-top:5px;
}
#restartBtn{
    font-size:22px;
    margin-top:20px;
    display:none;
}
#overlay{
    display:none;
    position:absolute;
    top:0; left:0;
    width:100%; height:100%;
    background:rgba(0,0,0,0.85);
    justify-content:center;
    align-items:center;
    flex-direction:column;
    z-index:100;
    cursor:pointer;
}
#overlay h2{
    font-size:50px;
    text-shadow:0 0 25px #fff;
    margin-bottom:20px;
    animation: blink 0.7s infinite alternate;
}
@keyframes blink{0%{opacity:1;}100%{opacity:0.4;}}
.confetti{
    position:absolute;
    width:10px; height:10px;
    background:yellow;
    animation:fall 2s linear infinite;
}
@keyframes fall{
    0%{transform:translateY(0) rotate(0deg);}
    100%{transform:translateY(600px) rotate(360deg);}
}
.lebka{
    font-size:100px;
    animation:shake 0.5s infinite alternate;
}
@keyframes shake{
    0%{transform:translateX(0);}
    100%{transform:translateX(20px);}
}
</style>
</head>
<body>

<div id="leftPanel">
Víš že:
Hopy neni slepice
komu se neleni tomu se zeleni
Martin poslal Tengemu 100kč xd.
</div>

<div class="container">
    <button id="backBtn" onclick="goBack()">⬅ Zpět</button>
    <h1>PIŠKVORKY</h1>

    <div id="mainMenu">
        <input id="name" placeholder="Zadej své jméno">
        <button onclick="chooseMode('bot')">🤖 PROTI BOTOVI</button>
        <button onclick="chooseMode('online')">🌐 ONLINE</button>
    </div>

    <div class="onlineOptions" id="onlineOptions" style="display:none;">
        <button onclick="createRoom()">Vytvořit místnost</button>
        <input id="roomCode" placeholder="4-místný kód">
        <button onclick="joinRoom()">Připojit se</button>
        <div id="readyStatus"></div>
    </div>

    <div class="status" id="status"></div>
    <div class="board" id="board" style="display:none;"></div>
    <button id="restartBtn" onclick="restartGame()">🔄 HRÁT ZNOVU</button>
</div>

<div id="rightPanel">
Petr Bezruč  
Ostrava  
Sto roků v šachtě žil, mlčel jsem  
sto roků kopal jsem uhlí,  
za sto let v rameni bezmasém  
svaly mi v železo ztuhly.  

Uhelný prach sed mi do očí,  
rubíny ze rtů mi uhly,  
ze vlasů, z vousů a z obočí  
visí mi rampouchy uhlí.  

Chléb s uhlím beru si do práce,  
z roboty jdu na robotu,  
při Dunaji strmí paláce,  
z krve mé a z mého potu.  

Sto roků v kopalně mlčel jsem,  
kdo mi těch sto roků vrátí?  
Když jsem jim pohrozil kladivem,  
kdekdo se začal mi smáti.  

Abych měl rozum, šel v kopalnu zas,  
pro pány dřel se jak prve -  
máchl jsem kladivem - teklo to v ráz  
na Polské Ostravě krve!  

Všichni vy na Slezské, všichni vy, dím,  
nech je vám Petr neb Pavel,  
mějž prs kryt krunýřem ocelovým,  
tisícům k útoku zavel,  

Všichni vy na Slezské, všichni vy, dím,  
hlubokých páni vy dolů,  
přijde den, z dolů jde plamen a dým,  
přijde den, zúčtujem spolu!
</div>

<div id="overlay" class="overlay" onclick="hideOverlay()">
    <h2 id="overlayText"></h2>
</div>

<script>
// --- proměnné ---
let board=[], myTurn=false, mySymbol="X", enemySymbol="O", gameOver=false;
let mode="", peer, conn, playerName="", readyCount=0;

const boardDiv=document.getElementById("board");
const statusDiv=document.getElementById("status");
const backBtn=document.getElementById("backBtn");
const restartBtn=document.getElementById("restartBtn");
const readyStatusDiv=document.getElementById("readyStatus");
const overlay=document.getElementById("overlay");
const overlayText=document.getElementById("overlayText");

// --- funkce ---
function goBack(){
    boardDiv.style.display="none";
    boardDiv.innerHTML="";
    statusDiv.textContent="";
    restartBtn.style.display="none";
    document.getElementById("onlineOptions").style.display="none";
    document.getElementById("mainMenu").style.display="block";
    backBtn.style.display="none";
    overlay.style.display="none";
    if(conn) conn.close();
    if(peer) peer.destroy();
    peer=null; conn=null; mode=""; readyCount=0; readyStatusDiv.textContent="";
}

function chooseMode(m){
    playerName=document.getElementById("name").value.trim();
    if(!playerName) return alert("Zadej jméno");
    mode=m; document.getElementById("mainMenu").style.display="none";
    backBtn.style.display="block";

    if(mode==="bot"){
        boardDiv.style.display="grid";
        myTurn=true; mySymbol="X"; enemySymbol="O";
        initBoard();
        statusDiv.textContent=playerName+" je na tahu";
    } else if(mode==="online"){
        document.getElementById("onlineOptions").style.display="block";
    }
}

function initBoard(){
    board=["","","","","","","","",""];
    boardDiv.innerHTML="";
    gameOver=false;
    restartBtn.style.display="none";
    overlay.style.display="none";
    for(let i=0;i<9;i++){
        const c=document.createElement("div");
        c.className="cell";
        c.onclick=()=>play(i);
        boardDiv.appendChild(c);
    }
}

function play(i){
    if(gameOver || board[i] || !myTurn) return;
    board[i]=mySymbol;
    updateBoard();
    if(checkWin(mySymbol)){end(true);return;}
    if(draw()){end(null);return;}
    myTurn=false;

    if(mode==="bot"){
        statusDiv.textContent="Bot přemýšlí...";
        setTimeout(botMove,400);
    } else if(conn){
        conn.send(board);
        statusDiv.textContent="Soupeř je na tahu";
    }
}

function botMove(){
    let best=-1;
    for(let i=0;i<9;i++){if(!board[i]){board[i]=enemySymbol;if(checkWin(enemySymbol)) best=i;board[i]="";}}
    if(best===-1){for(let i=0;i<9;i++){if(!board[i]){board[i]=mySymbol;if(checkWin(mySymbol)) best=i;board[i]="";}}}
    if(best===-1){if(!board[4]) best=4;else{let free=board.map((v,i)=>v===""?i:null).filter(v=>v!==null);best=free[Math.floor(Math.random()*free.length)];}}
    board[best]=enemySymbol;
    updateBoard();
    if(checkWin(enemySymbol)){end(false);return;}
    if(draw()){end(null);return;}
    myTurn=true; statusDiv.textContent=playerName+" je na tahu";
}

function updateBoard(){document.querySelectorAll(".cell").forEach((c,i)=>c.textContent=board[i]);}
function checkWin(s){const w=[[0,1,2],[3,4,5],[6,7,8],[0,3,6],[1,4,7],[2,5,8],[0,4,8],[2,4,6]];return w.some(a=>a.every(i=>board[i]===s));}
function draw(){return board.every(x=>x);}
function end(win){
    gameOver=true; restartBtn.style.display="block";
    if(win===true){
        overlayText.textContent="YOU WON 🎉";
        overlay.style.display="flex";
        confettiEffect();
    } else if(win===false){
        overlayText.innerHTML="YOU LOST 💀";
        overlay.style.display="flex";
        skullEffect();
    } else{
        overlayText.textContent="REMÍZA";
        overlay.style.display="flex";
    }
}
function hideOverlay(){overlay.style.display="none";}

// efekty
function confettiEffect(){
    for(let i=0;i<100;i++){
        const c=document.createElement("div");
        c.className="confetti";
        c.style.background=randomColor();
        c.style.left=Math.random()*window.innerWidth+"px";
        c.style.animationDuration=(1+Math.random()*2)+"s";
        document.body.appendChild(c);
        setTimeout(()=>c.remove(),2000);
    }
}
function skullEffect(){
    const s=document.createElement("div");
    s.className="lebka";
    s.textContent="💀";
    document.body.appendChild(s);
    s.style.position="absolute";
    s.style.top="50%";
    s.style.left="50%";
    s.style.transform="translate(-50%,-50%)";
    setTimeout(()=>s.remove(),2000);
}
function randomColor(){const colors=["#ff0040","#00ffdd","#ffff00","#ff00ff","#00ff00","#ff7f00"];return colors[Math.floor(Math.random()*colors.length)];}

// restart hry
function restartGame(){
    initBoard(); 
    if(mode==="bot") myTurn=true;
    statusDiv.textContent=playerName+" je na tahu";
    readyCount=0;
    readyStatusDiv.textContent="0/2 ready";
    overlay.style.display="none";
}

// ONLINE funkce
function generateCode(){return Math.floor(1000+Math.random()*9000).toString();}
function createRoom(){
    const code=generateCode();
    peer=new Peer(code);
    mySymbol="X"; enemySymbol="O"; myTurn=false;
    initBoard(); boardDiv.style.display="none";
    peer.on("open",()=>statusDiv.textContent="Kód místnosti: "+code);
    peer.on("connection",c=>{
        conn=c;
        conn.on("data",receiveData);
        readyCount=1; readyStatusDiv.textContent="1/2 ready";
        if(conn) boardDiv.style.display="grid";
        statusDiv.textContent=playerName+" je na tahu";
        myTurn=true;
    });
}
function joinRoom(){
    const code=document.getElementById("roomCode").value.trim();
    if(!code) return alert("Zadej kód");
    peer=new Peer();
    mySymbol="O"; enemySymbol="X"; myTurn=false;
    initBoard(); boardDiv.style.display="none";
    peer.on("open",()=>{
        conn=peer.connect(code);
        conn.on("data",receiveData);
        readyCount=1; readyStatusDiv.textContent="1/2 ready";
        if(conn) boardDiv.style.display="grid";
        statusDiv.textContent="Čekáš na soupeře";
    });
}
function receiveData(d){
    if(d.ready){
        readyCount++;
        readyStatusDiv.textContent=readyCount+"/2 ready";
        if(readyCount===2) boardDiv.style.display="grid";
        return;
    }
    board=d;
    updateBoard();
    if(checkWin(enemySymbol)){end(false);return;}
    if(draw()){end(null);return;}
    myTurn=true; statusDiv.textContent=playerName+" je na tahu";
}
</script>
</body>
</html>
