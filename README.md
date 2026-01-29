<!DOCTYPE html>
<html lang="cs">
<head>
<meta charset="UTF-8">
<title>Piškvorky ONLINE</title>
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<script src="https://unpkg.com/peerjs@1.5.2/dist/peerjs.min.js"></script>

<style>
body{
margin:0;font-family:Arial;
background:linear-gradient(135deg,#1a001f,#4b0082);
color:white;display:flex;justify-content:center;
padding-top:40px
}
.container{
width:420px;background:rgba(0,0,0,.75);
border-radius:20px;padding:25px;text-align:center
}
button,input{
width:100%;padding:14px;margin:8px 0;
border:none;border-radius:14px;font-size:18px
}
button{background:#7b2cbf;color:white;cursor:pointer}
button:hover{background:#9d4edd}
.board{
display:grid;grid-template-columns:repeat(3,1fr);
gap:12px;margin-top:20px
}
.cell{
height:110px;background:#240046;
border-radius:14px;font-size:60px;
display:flex;align-items:center;justify-content:center;
cursor:pointer
}
.status{margin-top:12px;font-size:20px}
#startBtn{background:#2ec4b6;display:none}
</style>
</head>

<body>
<div class="container">

<h1>PIŠKVORKY</h1>

<div id="menu">
<input id="nameInput" placeholder="Tvé jméno">
<button onclick="showOnline()">🌐 ONLINE</button>
</div>

<div id="online" style="display:none">
<button id="createBtn" onclick="createRoom()">Vytvořit místnost</button>
<input id="roomCode" placeholder="4místný kód">
<button id="joinBtn" onclick="joinRoom()">Připojit se</button>
<button id="startBtn" onclick="startGame()">▶ START</button>
</div>

<div class="status" id="status"></div>
<div class="board" id="board" style="display:none"></div>

</div>

<script>
/* ===== ELEMENTY ===== */
const menu = document.getElementById("menu");
const online = document.getElementById("online");
const nameInput = document.getElementById("nameInput");
const boardDiv = document.getElementById("board");
const statusDiv = document.getElementById("status");
const startBtn = document.getElementById("startBtn");
const roomCodeInput = document.getElementById("roomCode");
const createBtn = document.getElementById("createBtn");
const joinBtn = document.getElementById("joinBtn");

/* ===== STAV ===== */
let board = [];
let myTurn = false;
let mySymbol = "";
let enemySymbol = "";
let peer = null;
let conn = null;
let isHost = false;

/* ===== UI ===== */
function showOnline(){
if(!nameInput.value.trim()) return alert("Zadej jméno");
menu.style.display="none";
online.style.display="block";
}

/* ===== HRA ===== */
function initBoard(){
board=["","","","","","","","",""];
boardDiv.innerHTML="";
for(let i=0;i<9;i++){
const c=document.createElement("div");
c.className="cell";
c.onclick=()=>play(i);
boardDiv.appendChild(c);
}
}

function play(i){
if(!myTurn || board[i]) return;
board[i]=mySymbol;
update();
if(check(mySymbol)){end("Vyhrál jsi 🎉");return;}
if(draw()){end("Remíza");return;}
myTurn=false;
conn.send({type:"move",board});
statusDiv.textContent="Soupeř je na tahu";
}

function update(){
document.querySelectorAll(".cell")
.forEach((c,i)=>c.textContent=board[i]);
}

function receive(data){
if(data.type==="start"){
boardDiv.style.display="grid";
initBoard();
myTurn = !isHost;
statusDiv.textContent = myTurn ? "Jsi na tahu" : "Soupeř je na tahu";
return;
}

if(data.type==="move"){
board=data.board;
update();
if(check(enemySymbol)){end("Prohrál jsi 💀");return;}
if(draw()){end("Remíza");return;}
myTurn=true;
statusDiv.textContent="Jsi na tahu";
}
}

function check(s){
const w=[[0,1,2],[3,4,5],[6,7,8],
[0,3,6],[1,4,7],[2,5,8],[0,4,8],[2,4,6]];
return w.some(a=>a.every(i=>board[i]===s));
}

function draw(){return board.every(x=>x);}

function end(text){
statusDiv.textContent=text;
myTurn=false;
}

/* ===== ONLINE ===== */
function generateCode(){
return Math.floor(1000+Math.random()*9000).toString();
}

function createRoom(){
isHost=true;
mySymbol="X"; enemySymbol="O";

const code=generateCode();
peer=new Peer(code,{host:"0.peerjs.com",port:443,secure:true});

createBtn.style.display="none";
joinBtn.style.display="none";
roomCodeInput.style.display="none";

statusDiv.textContent="Kód místnosti: "+code;

peer.on("connection",c=>{
conn=c;
conn.on("data",receive);
statusDiv.textContent="Soupeř se připojil ✔";
startBtn.style.display="block";
});
}

function joinRoom(){
const code=roomCodeInput.value.trim();
if(!code) return alert("Zadej kód");

isHost=false;
mySymbol="O"; enemySymbol="X";

peer=new Peer({host:"0.peerjs.com",port:443,secure:true});
peer.on("open",()=>{
conn=peer.connect(code);
conn.on("open",()=>{
statusDiv.textContent="Připojeno – čekáš na START";
});
conn.on("data",receive);
});
}

function startGame(){
startBtn.style.display="none";
boardDiv.style.display="grid";
initBoard();
myTurn=true;
statusDiv.textContent="Jsi na tahu";
conn.send({type:"start"});
}
</script>
</body>
</html>
