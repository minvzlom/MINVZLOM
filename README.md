<!DOCTYPE html>
<html lang="tg">
<head>
<meta charset="utf-8" />
<meta name="viewport" content="width=device-width, initial-scale=1" />
<title>VZLOM MINES</title>

<link rel="preconnect" href="https://fonts.googleapis.com">
<link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
<link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;700;800&family=Russo+One&display=swap" rel="stylesheet">

<style>
  :root { --size: 72px; --gap: 8px; }
  body {
    background:#0b1220; color:#e5e7eb;
    font-family: "Inter", system-ui, -apple-system, "Segoe UI", Roboto, Arial, sans-serif;
    text-align:center; margin:0; padding:16px;
  }

  .brand {
    margin: 0 0 12px;
    font-family: "Russo One", "Inter", sans-serif;
    font-size: 30px; text-transform: uppercase;
    background: linear-gradient(90deg, #22c55e, #60a5fa);
    -webkit-background-clip: text; color: transparent;
    text-shadow: 0 8px 24px rgba(34,197,94,0.25), 0 6px 20px rgba(96,165,250,0.22);
  }

  .top-controls { display:flex; align-items:center; justify-content:center; gap:12px; flex-wrap:wrap; }
  .labelX { font-weight:800; opacity:.9; font-family:"Russo One", "Inter", sans-serif; }

  .inputCell {
    width: 52px; height: 52px;
    display:grid; place-items:center; background:#1f2937; color:#e5e7eb;
    border-radius:10px; border:2px solid rgba(34,197,94,0.45);
    box-shadow: 0 0 10px rgba(34,197,94,0.25);
    font-family:"Russo One", "Inter", sans-serif;
  }
  .inputCell input {
    width:100%; height:100%; border:0; outline:none; background:transparent; color:#fff;
    font-size:26px; font-weight:800; text-align:center;
    font-family:"Russo One", "Inter", sans-serif;
  }

  .pill {
    min-width:96px; padding:10px 14px; border-radius:999px;
    background:#0b1220; border:1px solid rgba(255,255,255,0.12);
    font-weight:800; font-family:"Russo One", "Inter", sans-serif;
  }

  .frame {
    display: inline-block; padding: var(--gap); margin-top: 12px;
    border-radius: 18px; border: 2px solid rgba(255,255,255,0.18);
    background: rgba(255,255,255,0.03);
  }

  .grid {
    display:grid; grid-template-columns:repeat(5,var(--size)); gap:var(--gap);
    justify-content:center;
  }

  .cell {
    width:var(--size); height:var(--size); border-radius:12px;
    display:grid; place-items:center; font-size:34px; font-weight:800;
    background:#1f2937; color:#ffffff; cursor:pointer; position:relative;
    border:2px solid rgba(34,197,94,0.35);
    transition: background .45s ease, color .45s ease;
    font-family:"Russo One", "Inter", sans-serif;
  }

  .cell.revealed { background:#000; color:#facc15; cursor:default; }
  .cell.mine.revealed {
    background:#111;   /* ⬛️ */
    font-size:42px;
  }

  /* эффектҳои шикастан */
  .cell::before, .cell::after{
    content:""; position:absolute; inset:0; pointer-events:none; opacity:0;
    background:linear-gradient(135deg, rgba(255,255,255,0.20) 0%, rgba(255,255,255,0.06) 60%, rgba(255,255,255,0) 100%);
    mix-blend-mode:screen;
  }
  .cell.shatter::before { animation: crackLeft 900ms ease-out forwards; }
  .cell.shatter::after  { animation: crackRight 900ms ease-out forwards; }
  @keyframes crackLeft { 0%{opacity:.95;} 100%{opacity:0; transform:translate(-42px,-42px);} }
  @keyframes crackRight{ 0%{opacity:.95;} 100%{opacity:0; transform:translate(42px,42px);} }

  .soft-enter { opacity:.15; transform:scale(0.98); }
  .soft-enter.revealed { opacity:1; transform:none; }

  #status { margin-top:12px; font-weight:700; }

  .bottom-area { margin-top:14px; display:flex; flex-direction:column; gap:10px; align-items:center; }
  button.app {
    padding:14px 20px; border-radius:12px; border:none; width:min(420px,94vw);
    font-size:16px; font-weight:800; cursor:pointer;
    font-family:"Russo One", "Inter", sans-serif;
  }
  #signalBtn { background:#16a34a; color:#fff; }
  #newSignalBtn { background:#334155; color:#e5e7eb; }
  button.app[disabled] { opacity:.45; }
</style>
</head>
<body>
  <h2 class="brand">VZLOM MINES</h2>
  <div class="top-controls">
    <span class="labelX">✖️:</span>
    <div class="inputCell"><input id="mineCount" type="number" min="0" max="24" value="4"/></div>
    <div id="timer" class="pill">⏳ 15</div>
  </div>
  <div class="frame"><div id="grid" class="grid"></div></div>
  <div id="status"></div>
  <div class="bottom-area">
    <button id="signalBtn" class="app">ПОЛУЧИТ СИГНАЛ</button>
    <button id="newSignalBtn" class="app" disabled>НОВИ СИГНАЛ</button>
  </div>

<script>
const R=5,C=5; let board=[],minesPlaced=false; let countdownId=null,remain=15;
const grid=document.getElementById("grid"),statusEl=document.getElementById("status");
const mineInput=document.getElementById("mineCount"),timerEl=document.getElementById("timer");
const signalBtn=document.getElementById("signalBtn"),newBtn=document.getElementById("newSignalBtn");

function getM(){ return Math.min(Math.max(parseInt(mineInput.value||"0"),0),R*C-1); }
function resetTimer(){ remain=15; timerEl.textContent="⏳ 15"; }
function setBtns(sig,nov){ signalBtn.disabled=!sig; newBtn.disabled=!nov; }

function newGame(){
  if(countdownId){clearInterval(countdownId);} resetTimer();
  minesPlaced=false; setBtns(true,false); statusEl.textContent="";
  board=Array.from({length:R*C},()=>({mine:false,revealed:false}));
  grid.innerHTML="";
  for(let i=0;i<R*C;i++){
    const d=document.createElement("div"); d.className="cell"; d.dataset.i=i;
    d.onclick=()=>revealOne(i); grid.appendChild(d);
  }
}

function placeM(){ const M=getM(); const idxs=[...Array(R*C).keys()];
  for(let i=idxs.length-1;i>0;i--){const j=Math.floor(Math.random()*(i+1));[idxs[i],idxs[j]]=[idxs[j],idxs[i]];}
  for(let k=0;k<M;k++){board[idxs[k]].mine=true;} minesPlaced=true; }

function revealOne(i){
  const cell=grid.children[i],o=board[i]; if(o.revealed) return;
  if(!minesPlaced) placeM();
  cell.classList.add('soft-enter');
  requestAnimationFrame(()=>{
    o.revealed=true; cell.classList.add('revealed','shatter');
    if(o.mine){ cell.classList.add('mine'); cell.textContent='💥'; }
    else{ cell.textContent='⭐️'; }
    setTimeout(()=>cell.classList.remove('shatter'),900);
    setTimeout(()=>cell.classList.remove('soft-enter'),500);
  });
}

function revealAll(){
  if(!minesPlaced) placeM();
  for(let i=0;i<R*C;i++){
    const cell=grid.children[i],o=board[i];
    if(!o.revealed){ cell.classList.add('soft-enter'); }
  }
  requestAnimationFrame(()=>{
    for(let i=0;i<R*C;i++){
      const cell=grid.children[i],o=board[i];
      if(!o.revealed){ o.revealed=true; cell.classList.add('revealed','shatter');
        if(o.mine){ cell.classList.add('mine'); cell.textContent='💥'; }
        else{ cell.textContent='⭐️'; }
        setTimeout(()=>cell.classList.remove('shatter'),900);
        setTimeout(()=>cell.classList.remove('soft-enter'),500);
      }
    }
  });
}

function startCountdown(){
  if(countdownId) clearInterval(countdownId);
  remain=15; timerEl.textContent="⏳ 15";
  countdownId=setInterval(()=>{
    remain--; timerEl.textContent=`⏳ ${remain}`;
    if(remain<=0){clearInterval(countdownId);countdownId=null;
      setBtns(false,true);}
  },1000);
}

signalBtn.onclick=()=>{ revealAll(); setBtns(false,false); startCountdown(); };
newBtn.onclick=()=>{ newGame(); };

mineInput.oninput=()=>{ mineInput.value=getM(); newGame(); };

newGame();
</script>
</body>
</html>
