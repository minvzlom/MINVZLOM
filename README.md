<!DOCTYPE html>
<html lang="tg">
<head>
<meta charset="utf-8" />
<meta name="viewport" content="width=device-width, initial-scale=1" />
<title>VZLOM MINES</title>

<!-- Шрифтҳо -->
<link rel="preconnect" href="https://fonts.googleapis.com">
<link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
<link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;700;800&family=Russo+One&display=swap" rel="stylesheet">

<style>
  :root { --size: 50px; --gap: 5px; }
  body {
    background:#0b1220; color:#e5e7eb;
    font-family: "Inter", system-ui, -apple-system, "Segoe UI", Roboto, Arial, sans-serif;
    text-align:center; margin:0; padding:12px;
  }

  /* Сарлавҳа */
  .brand {
    margin: 0 0 10px;
    font-family: "Russo One", "Inter", sans-serif;
    font-size: 24px; letter-spacing: 1px; text-transform: uppercase;
    background: linear-gradient(90deg, #22c55e, #60a5fa);
    -webkit-background-clip: text; background-clip: text; color: transparent;
    text-shadow: 0 6px 18px rgba(34,197,94,0.25), 0 4px 14px rgba(96,165,250,0.22);
  }

  /* Контроли боло: вуруд + таймер */
  .top-controls{ display:flex; align-items:center; justify-content:center; gap:10px; flex-wrap:wrap; }
  .labelX{ font-weight:800; opacity:.9; font-family:"Russo One","Inter",sans-serif; }

  /* Қуттии вориди ✖️ — хурдтар */
  .inputCell{
    width:45px; height:45px; display:grid; place-items:center;
    background:#1f2937; color:#e5e7eb; border-radius:8px; position:relative; overflow:hidden;
    border:2px solid rgba(34,197,94,0.45);
    box-shadow:0 0 8px rgba(34,197,94,0.25), 0 0 3px rgba(34,197,94,0.22) inset;
    font-family:"Russo One","Inter",sans-serif;
  }
  .inputCell input{
    width:100%; height:100%; border:0; outline:none; background:transparent; color:#fff;
    font-size:20px; font-weight:800; text-align:center; font-family:"Russo One","Inter",sans-serif;
  }
  .inputCell input::-webkit-outer-spin-button,
  .inputCell input::-webkit-inner-spin-button{ -webkit-appearance:none; margin:0; }

  .pill{
    min-width:80px; padding:8px 12px; border-radius:999px;
    background:#0b1220; border:1px solid rgba(255,255,255,0.12);
    font-weight:800; font-family:"Russo One","Inter",sans-serif;
  }

  /* Рамкаи умумӣ ва грид */
  .frame{
    display:inline-block; padding:var(--gap); margin-top:10px;
    border-radius:14px; border:2px solid rgba(255,255,255,0.18);
    background:rgba(255,255,255,0.03);
    box-shadow:0 8px 18px rgba(0,0,0,0.4), inset 0 1px 0 rgba(255,255,255,0.06);
  }
  .grid{
    display:grid; grid-template-columns:repeat(5,var(--size)); gap:var(--gap);
    justify-content:center;
  }

  /* Ҳуҷайраҳо: хурдтар + свет */
  .cell{
    width:var(--size); height:var(--size); border-radius:8px;
    display:grid; place-items:center; font-size:26px; font-weight:800;
    background:#1f2937; color:#ffffff; cursor:pointer; position:relative; overflow:hidden;
    border:2px solid rgba(34,197,94,0.35);
    box-shadow: 0 0 8px rgba(34,197,94,0.22), 0 0 3px rgba(34,197,94,0.18) inset;
    transition: background .45s ease, color .45s ease, box-shadow .45s ease, border-color .45s ease, transform .14s ease, opacity .45s ease;
    font-family:"Russo One","Inter",sans-serif;
  }
  .cell:active{ transform:scale(0.98); }

  /* ⭐️ кушода — пушти сиёҳ */
  .cell.revealed{
    background:#000; color:#facc15; cursor:default;
    border-color:rgba(34,197,94,0.45);
    box-shadow:0 0 10px rgba(34,197,94,0.25), 0 0 5px rgba(34,197,94,0.22) inset;
  }
  /* 💥 кушода — пушти ⬛️ (сиёҳ), аломат худи эмодзи */
  .cell.mine.revealed{
    background:#111;                /* ⬛️ */
    font-size:32px;                 /* каме калонтар */
  }

  /* Эффекти “шикастани шиша” */
  .cell::before, .cell::after{
    content:""; position:absolute; inset:0; pointer-events:none; opacity:0;
    background:linear-gradient(135deg, rgba(255,255,255,0.20) 0%, rgba(255,255,255,0.06) 60%, rgba(255,255,255,0) 100%);
    mix-blend-mode:screen;
  }
  .cell.shatter::before{ animation:crackLeft 900ms ease-out forwards; }
  .cell.shatter::after { animation:crackRight 900ms ease-out forwards; }
  @keyframes crackLeft{ 0%{opacity:.95;} 100%{opacity:0; transform:translate(-32px,-32px);} }
  @keyframes crackRight{ 0%{opacity:.95;} 100%{opacity:0; transform:translate(32px,32px);} }

  /* Кушодани мулоим */
  .soft-enter{ opacity:.15; transform:scale(0.96); }
  .soft-enter.revealed{ opacity:1; transform:none; }

  #status{ margin-top:8px; font-weight:700; min-height:22px; }

  /* Тугмаҳо — хурдтар */
  .bottom-area{ margin-top:12px; display:flex; flex-direction:column; gap:8px; align-items:center; }
  button.app{
    padding:10px 16px; border-radius:10px; border:none; width:min(340px,92vw);
    font-size:14px; font-weight:800; letter-spacing:.3px; cursor:pointer;
    transition: transform .06s ease, opacity .2s ease;
    font-family:"Russo One","Inter",sans-serif;
  }
  #signalBtn{ background:#16a34a; color:#fff; box-shadow:0 6px 14px rgba(22,163,74,0.35); }
  #newSignalBtn{ background:#334155; color:#e5e7eb; box-shadow:0 6px 14px rgba(2,6,23,0.35); }
  button.app:active{ transform:translateY(1px) scale(0.99); }
  button.app[disabled]{ opacity:.45; pointer-events:none; }
</style>
</head>
<body>
  <h2 class="brand">VZLOM MINES</h2>

  <div class="top-controls">
    <span class="labelX">✖️:</span>
    <div class="inputCell" title="Шумораи ✖️">
      <input id="mineCount" type="number" min="0" max="24" value="4" inputmode="numeric" />
    </div>
    <div id="timer" class="pill">⏳ 15</div>
  </div>

  <div class="frame">
    <div id="grid" class="grid" role="grid" aria-label="Майдон 5 ба 5"></div>
  </div>

  <div id="status"></div>

  <div class="bottom-area">
    <button id="signalBtn" class="app">ПОЛУЧИТ СИГНАЛ</button>
    <button id="newSignalBtn" class="app" disabled>НОВИ СИГНАЛ</button>
  </div>

<script>
const R=5, C=5;
let board=[], minesPlaced=false;
let countdownId=null, remaining=15;

const gridEl   = document.getElementById("grid");
const statusEl = document.getElementById("status");
const mineInput= document.getElementById("mineCount");
const timerEl  = document.getElementById("timer");
const signalBtn= document.getElementById("signalBtn");
const newBtn   = document.getElementById("newSignalBtn");

/* Ёридиҳандагон */
function clamp(x,a,b){ return Math.min(b, Math.max(a, x)); }
function getM(){
  const v = parseInt(mineInput.value || "0", 10);
  return clamp(isNaN(v)?0:v, 0, R*C-1);
}
function setButtons({signal=true, novi=false}){ signalBtn.disabled=!signal; newBtn.disabled=!novi; }
function resetTimerDisplay(){ remaining=15; timerEl.textContent="⏳ 15"; }

/* Ивентҳо */
mineInput.addEventListener('input', ()=>{ mineInput.value=String(getM()); newGame(); });

/* Оғози бозӣ */
function newGame(){
  if (countdownId) { clearInterval(countdownId); countdownId=null; }
  resetTimerDisplay();
  statusEl.textContent="";
  minesPlaced=false; setButtons({signal:true, novi:false});
  board=Array.from({length:R*C},()=>({mine:false, revealed:false}));
  gridEl.innerHTML="";
  for(let i=0;i<R*C;i++){
    const cell=document.createElement("div");
    cell.className="cell";
    cell.dataset.i=i;
    cell.onclick=()=>revealOne(i);
    gridEl.appendChild(cell);
  }
}

/* Ҷойкунии минаҳо (тақсимоти тасодуфӣ) */
function placeMines(){
  const M=getM();
  const idxs=[...Array(R*C).keys()];
  for(let i=idxs.length-1;i>0;i--){
    const j=Math.floor(Math.random()*(i+1));
    [idxs[i],idxs[j]]=[idxs[j],idxs[i]];
  }
  for(let k=0;k<M;k++){ board[idxs[k]].mine=true; }
  minesPlaced=true;
}

/* Кушодани як ҳуҷайра (оҳиста + “шикастан”) */
function revealOne(i){
  const cell=gridEl.children[i];
  const o=board[i];
  if(o.revealed) return;
  if(!minesPlaced) placeMines();

  cell.classList.add('soft-enter');
  requestAnimationFrame(()=>{
    o.revealed=true;
    cell.classList.add('revealed','shatter');
    if (o.mine){
      cell.classList.add('mine');
      cell.textContent='💥';
    } else {
      cell.textContent='⭐️';
    }
    setTimeout(()=>cell.classList.remove('shatter'), 900);
    setTimeout(()=>cell.classList.remove('soft-enter'), 500);
  });
}

/* Кушодани якбора (сигнал) */
function revealAllSmooth(){
  if(!minesPlaced) placeMines();
  for(let i=0;i<R*C;i++){
    const cell=gridEl.children[i];
    const o=board[i];
    if(!o.revealed){ cell.classList.add('soft-enter'); }
  }
  requestAnimationFrame(()=>{
    for(let i=0;i<R*C;i++){
      const cell=gridEl.children[i];
      const o=board[i];
      if(!o.revealed){
        o.revealed=true;
        cell.classList.add('revealed','shatter');
        cell.textContent = o.mine ? '💥' : '⭐️';
        if (o.mine) cell.classList.add('mine');
        setTimeout(()=>cell.classList.remove('shatter'), 900);
        setTimeout(()=>cell.classList.remove('soft-enter'), 500);
      }
    }
  });
}

/* Таймер 15 → 0 ва идоракунии тугмаҳо */
function startCountdown(){
  if (countdownId) clearInterval(countdownId);
  remaining=15; timerEl.textContent=`⏳ ${remaining}`;
  countdownId=setInterval(()=>{
    remaining--; timerEl.textContent=`⏳ ${remaining}`;
    if (remaining<=0){
      clearInterval(countdownId); countdownId=null;
      timerEl.textContent="⏳ 0";
      setButtons({signal:false, novi:true}); // ҳоло танҳо «НОВИ СИГНАЛ» фаъол
    }
  },1000);
}

/* Тугмаҳо */
signalBtn.addEventListener('click', ()=>{
  revealAllSmooth();
  setButtons({signal:false, novi:false}); // то 0 шудан — ҳарду ғайрифаол
  startCountdown();
  statusEl.textContent = `📡 Сигнал қабул шуд — ✖️: ${getM()} дона.`;
});

newBtn.addEventListener('click', ()=>{
  newGame();                           // бозӣ аз нав
  setButtons({signal:true, novi:false});
});

newGame();
</script>
</body>
</html>
