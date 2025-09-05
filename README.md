# 2048-game
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8" />
<meta name="viewport" content="width=device-width, initial-scale=1.0"/>
<title>2048 — LeafZuya Edition</title>
<style>
  :root{
    --green:#00a86b;
    --green-dark:#007a4c;
    --blue:#1e88e5;
    --blue-deep:#0e5aa6;
    --panel: rgba(255,255,255,0.12);
    --tile-shadow: 0 8px 18px rgba(0,0,0,0.25);
  }

  /* Background gradient + soft glow */
  body{
    margin:0;
    min-height:100vh;
    font-family: system-ui, Arial, sans-serif;
    color:#fff;
    background:
      radial-gradient(1200px 700px at 15% 85%, rgba(0,255,170,0.25), transparent 60%),
      radial-gradient(900px 600px at 85% 10%, rgba(0,170,255,0.22), transparent 60%),
      linear-gradient(135deg, #00995a, #0078c2);
    display:flex;
    align-items:center;
    justify-content:center;
    overflow:hidden;
    user-select:none;
    -webkit-tap-highlight-color: transparent;
  }

  /* Decorative floating gradient beams */
  .beam{
    position:absolute; inset:auto;
    width:60vmax; height:60vmax;
    filter: blur(60px);
    opacity:.18;
    pointer-events:none;
    z-index:0;
  }
  .beam.green{
    background: radial-gradient(circle at 30% 30%, #35ffb3, transparent 40%);
    top:-20vmax; left:-20vmax;
    animation: drift1 18s linear infinite;
  }
  .beam.blue{
    background: radial-gradient(circle at 70% 70%, #49b2ff, transparent 45%);
    bottom:-25vmax; right:-25vmax;
    animation: drift2 22s linear infinite;
  }
  @keyframes drift1{ from{transform:rotate(0deg)} to{transform:rotate(360deg)} }
  @keyframes drift2{ from{transform:rotate(360deg)} to{transform:rotate(0deg)} }

  /* Fixed corner emojis */
  .corner-emoji{
    position:fixed; z-index:5; font-size:42px; opacity:.95; filter: drop-shadow(0 6px 8px rgba(0,0,0,.25));
  }
  .corner-emoji.leaf{ top:10px; right:14px; }
  .corner-emoji.ice{ bottom:12px; left:14px; }

  /* Container */
  .wrap{
    position:relative;
    z-index:2;
    text-align:center;
    padding:18px 16px 26px;
    width:min(92vw, 540px);
    backdrop-filter: blur(6px);
    background: linear-gradient(180deg, rgba(255,255,255,0.12), rgba(255,255,255,0.08));
    border:1px solid rgba(255,255,255,0.15);
    border-radius:20px;
    box-shadow: 0 20px 40px rgba(0,0,0,0.35);
  }

  h1{
    margin:0 0 6px;
    font-weight:800;
    letter-spacing:.5px;
    text-shadow:0 3px 10px rgba(0,0,0,.35);
  }
  .subtitle{opacity:.9; margin-bottom:14px;}

  /* Score bar */
  .bar{
    display:flex; gap:10px; justify-content:center; align-items:center; flex-wrap:wrap;
    margin:10px 0 16px;
  }
  .badge{
    background: linear-gradient(145deg, rgba(30, 136, 229, 0.25), rgba(0, 168, 107, 0.25));
    border:1px solid rgba(255,255,255,0.25);
    border-radius:14px;
    padding:10px 14px;
    min-width:120px;
    box-shadow: inset 0 2px 8px rgba(255,255,255,0.06), 0 10px 18px rgba(0,0,0,0.25);
  }
  .badge b{ display:block; font-size:14px; opacity:.9; margin-bottom:6px;}
  .badge .val{ font-size:22px; font-weight:800;}

  .actions{ display:flex; gap:10px; justify-content:center; flex-wrap:wrap; }
  .btn{
    cursor:pointer;
    border:none;
    color:#fff;
    padding:10px 14px;
    border-radius:12px;
    font-weight:700;
    letter-spacing:.3px;
    background: linear-gradient(135deg, var(--green), var(--blue));
    box-shadow: 0 10px 16px rgba(0,0,0,.25);
    transition: transform .06s ease, filter .2s ease;
  }
  .btn:active{ transform: scale(.98); filter: brightness(1.05); }

  /* Board */
  .board{
    margin:16px auto 6px;
    width:min(86vw, 440px);
    aspect-ratio:1/1;
    background: linear-gradient(180deg, rgba(255,255,255,0.09), rgba(0,0,0,0.1));
    border:1px solid rgba(255,255,255,0.18);
    border-radius:16px;
    padding:10px;
    box-shadow: inset 0 10px 25px rgba(0,0,0,0.25), 0 25px 40px rgba(0,0,0,0.35);
    display:grid;
    grid-template-columns: repeat(4, 1fr);
    grid-gap:10px;
    position:relative;
  }

  .cell{
    background: linear-gradient(180deg, rgba(255,255,255,0.06), rgba(0,0,0,0.12));
    border-radius:12px;
    border:1px solid rgba(255,255,255,0.12);
    position:relative; overflow:hidden;
  }

  .tile{
    position:absolute;
    inset:0;
    margin:auto;
    width:100%; height:100%;
    display:flex; align-items:center; justify-content:center;
    font-weight:900; font-size: clamp(20px, 5vw, 34px);
    border-radius:12px;
    color:#073b4c;
    box-shadow: var(--tile-shadow);
    transition: transform .12s ease;
  }
  .tile.pop{ animation: pop .12s ease; }
  @keyframes pop{ from{transform: scale(.6)} to{transform: scale(1)} }

  /* Tile color themes (gradient green-blue, darker as value grows) */
  .v2   { background: linear-gradient(135deg, #c7ffe7, #cfeaff); }
  .v4   { background: linear-gradient(135deg, #aaffd7, #bfe1ff); }
  .v8   { background: linear-gradient(135deg, #8cf2c7, #a6d6ff); color:#05303a;}
  .v16  { background: linear-gradient(135deg, #6be4b5, #8cc9ff); color:#042933;}
  .v32  { background: linear-gradient(135deg, #45d7a4, #6dbbff); color:#031f28;}
  .v64  { background: linear-gradient(135deg, #25c991, #4eadff); color:#031a22;}
  .v128 { background: linear-gradient(135deg, #12b47f, #2f9fff); color:#fff;}
  .v256 { background: linear-gradient(135deg, #0fa06f, #1c8ff3); color:#fff;}
  .v512 { background: linear-gradient(135deg, #0c8f64, #157fdb); color:#fff;}
  .v1024{ background: linear-gradient(135deg, #0a7d58, #106ec4); color:#fff;}
  .v2048{ background: linear-gradient(135deg, #086b4c, #0c5ea9); color:#fff; text-shadow: 0 2px 6px rgba(0,0,0,.35); }

  .note{ opacity:.9; margin-top:10px; font-size:.95rem; }

  /* Falling emojis (leaves & ice) */
  .fall{
    position:fixed; top:-60px;
    font-size:28px; opacity:.8;
    animation: fall 7.5s linear forwards;
    filter: drop-shadow(0 8px 12px rgba(0,0,0,.28));
    pointer-events:none; z-index:1;
  }
  @keyframes fall{
    0%{ transform: translateY(-60px) rotate(0deg); opacity:.0 }
    10%{ opacity:.9 }
    100%{ transform: translateY(110vh) rotate(360deg); opacity:.0 }
  }

  /* Game over / won banner */
  .banner{
    position:absolute; inset:0;
    background: rgba(0,0,0,.55);
    display:none; align-items:center; justify-content:center;
    color:#fff; font-weight:800; font-size: clamp(20px, 4vw, 34px);
    text-shadow: 0 8px 18px rgba(0,0,0,.55);
    border-radius:16px;
    backdrop-filter: blur(2px);
    z-index:3;
  }
  .banner.show{ display:flex; }
</style>
</head>
<body>

  <!-- Background glow layers -->
  <div class="beam green"></div>
  <div class="beam blue"></div>

  <!-- Corner fixed emojis -->
  <div class="corner-emoji leaf">🍃</div>
  <div class="corner-emoji ice">🧊</div>

  <div class="wrap">
    <h1>2048 — LeafZuya</h1>
    <div class="subtitle">Gabungkan angka sampai <b>2048</b>! (Panah keyboard / swipe)</div>

    <div class="bar">
      <div class="badge"><b>SCORE</b><div class="val" id="score">0</div></div>
      <div class="badge"><b>BEST</b><div class="val" id="best">0</div></div>
      <div class="actions">
        <button class="btn" id="newGame">New Game</button>
        <button class="btn" id="undoBtn" title="Undo sekali langkah">Undo</button>
      </div>
    </div>

    <div class="board" id="board">
      <!-- 16 cells -->
      <div class="cell"></div><div class="cell"></div><div class="cell"></div><div class="cell"></div>
      <div class="cell"></div><div class="cell"></div><div class="cell"></div><div class="cell"></div>
      <div class="cell"></div><div class="cell"></div><div class="cell"></div><div class="cell"></div>
      <div class="cell"></div><div class="cell"></div><div class="cell"></div><div class="cell"></div>

      <div class="banner" id="banner"></div>
    </div>

    <div class="note">Tip: sapu layar (atas/bawah/kiri/kanan) di HP. ⬆️⬇️⬅️➡️</div>
  </div>

<script>
(() => {
  const boardEl = document.getElementById('board');
  const scoreEl = document.getElementById('score');
  const bestEl  = document.getElementById('best');
  const banner  = document.getElementById('banner');
  const newBtn  = document.getElementById('newGame');
  const undoBtn = document.getElementById('undoBtn');

  const N = 4;
  let grid, score, best, previousState=null;

  best = +localStorage.getItem('lz_2048_best') || 0;
  bestEl.textContent = best;

  function emptyGrid(){ return Array.from({length:N}, () => Array(N).fill(0)); }

  function reset(){
    grid = emptyGrid();
    score = 0;
    updateScore();
    addRandomTile();
    addRandomTile();
    render(true);
    banner.classList.remove('show');
  }

  function updateScore(){
    scoreEl.textContent = score;
    if(score>best){
      best = score;
      bestEl.textContent = best;
      localStorage.setItem('lz_2048_best', best);
    }
  }

  function getEmpty(){
    const empty=[];
    for(let r=0;r<N;r++)for(let c=0;c<N;c++) if(!grid[r][c]) empty.push([r,c]);
    return empty;
  }

  function addRandomTile(){
    const empty = getEmpty();
    if(!empty.length) return false;
    const [r,c] = empty[Math.floor(Math.random()*empty.length)];
    grid[r][c] = Math.random()<0.9 ? 2 : 4;
    return true;
  }

  function cloneGrid(g=grid){ return g.map(row=>row.slice()); }

  function compress(row){
    const arr = row.filter(v=>v);
    while(arr.length<N) arr.push(0);
    return arr;
  }
  function merge(row){
    for(let i=0;i<N-1;i++){
      if(row[i]!==0 && row[i]===row[i+1]){
        row[i]*=2;
        score += row[i];
        row[i+1]=0;
        i++;
      }
    }
    return row;
  }

  function moveLeft(){
    let moved=false;
    for(let r=0;r<N;r++){
      let row = grid[r].slice();
      row = compress(row);
      row = merge(row);
      row = compress(row);
      if(row.toString()!==grid[r].toString()) moved=true;
      grid[r] = row;
    }
    return moved;
  }
  function reverse(row){ return row.slice().reverse(); }
  function transpose(g){
    const t = emptyGrid();
    for(let r=0;r<N;r++) for(let c=0;c<N;c++) t[c][r]=g[r][c];
    return t;
  }
  function moveRight(){
    grid = grid.map(row => reverse(row));
    const moved = moveLeft();
    grid = grid.map(row => reverse(row));
    return moved;
  }
  function moveUp(){
    grid = transpose(grid);
    const moved = moveLeft();
    grid = transpose(grid);
    return moved;
  }
  function moveDown(){
    grid = transpose(grid);
    const moved = moveRight();
    grid = transpose(grid);
    return moved;
  }

  function canMove(){
    if(getEmpty().length) return true;
    for(let r=0;r<N;r++){
      for(let c=0;c<N;c++){
        const v = grid[r][c];
        if((c+1<N && grid[r][c+1]===v) || (r+1<N && grid[r+1][c]===v)) return true;
      }
    }
    return false;
  }

  function render(pop=false){
    // clear tiles
    const cells = [...boardEl.querySelectorAll('.cell')];
    cells.forEach(cell=>{ cell.innerHTML=''; });

    for(let r=0;r<N;r++){
      for(let c=0;c<N;c++){
        const v = grid[r][c];
        if(!v) continue;
        const tile = document.createElement('div');
        tile.className = `tile v${v} ${pop?'pop':''}`;
        tile.textContent = v;
        // place into the correct cell (index r*4 + c)
        cells[r*N + c].appendChild(tile);
      }
    }
    updateScore();
  }

  function setStateForUndo(){
    previousState = {
      grid: cloneGrid(),
      score
    };
  }

  function undo(){
    if(!previousState) return;
    grid = cloneGrid(previousState.grid);
    score = previousState.score;
    previousState = null;
    render();
  }

  function act(dir){
    setStateForUndo();
    let moved=false;
    if(dir==='L') moved = moveLeft();
    if(dir==='R') moved = moveRight();
    if(dir==='U') moved = moveUp();
    if(dir==='D') moved = moveDown();

    if(!moved){ previousState=null; return; }

    addRandomTile();
    render(true);

    // Win?
    if(grid.flat().some(v=>v===2048)){
      banner.textContent = 'YOU WIN! 🎉';
      banner.classList.add('show');
    } else if(!canMove()){
      banner.textContent = 'GAME OVER 😵';
      banner.classList.add('show');
    }
  }

  // Controls: keyboard
  window.addEventListener('keydown', (e)=>{
    if(banner.classList.contains('show')) return;
    const k = e.key;
    if(['ArrowLeft','a','A','h','H'].includes(k)) act('L');
    else if(['ArrowRight','d','D','l','L'].includes(k)) act('R');
    else if(['ArrowUp','w','W','k','K'].includes(k)) act('U');
    else if(['ArrowDown','s','S','j','J'].includes(k)) act('D');
  }, {passive:true});

  // Controls: swipe
  let sx=0, sy=0, ex=0, ey=0;
  boardEl.addEventListener('touchstart', e=>{
    const t=e.changedTouches[0]; sx=t.clientX; sy=t.clientY;
  }, {passive:true});
  boardEl.addEventListener('touchend', e=>{
    const t=e.changedTouches[0]; ex=t.clientX; ey=t.clientY;
    const dx=ex-sx, dy=ey-sy;
    if(Math.max(Math.abs(dx), Math.abs(dy))<30) return;
    if(Math.abs(dx)>Math.abs(dy)) act(dx>0?'R':'L');
    else act(dy>0?'D':'U');
  }, {passive:true});

  // UI
  newBtn.addEventListener('click', reset);
  undoBtn.addEventListener('click', undo);
  banner.addEventListener('click', ()=>{ banner.classList.remove('show'); });

  // Falling emojis generator
  const emojis = ['🍃','🌱','🧊','❄️'];
  function dropEmoji(){
    const span = document.createElement('div');
    span.className='fall';
    span.textContent = emojis[Math.floor(Math.random()*emojis.length)];
    span.style.left = Math.random()*100 + 'vw';
    span.style.fontSize = (24 + Math.random()*18) + 'px';
    span.style.animationDuration = (6.5 + Math.random()*3) + 's';
    document.body.appendChild(span);
    setTimeout(()=>span.remove(), 11000);
  }
  setInterval(dropEmoji, 900);

  // Init
  reset();
})();
</script>
</body>
</html>
