<!DOCTYPE html>
<html lang="zh-Hant">
<head>
<meta charset="UTF-8" />
<title>FLIP 7 七翻天</title>
<meta name="viewport" content="width=device-width, initial-scale=1.0" />
<link href="https://fonts.googleapis.com/css2?family=Anton&family=Space+Mono:wght@400;700&family=Nunito:wght@400;600;700;800&display=swap" rel="stylesheet">
<style>
:root{
  --bg:#0b3b2c; --bg-deep:#062821; --panel:#0f4a39; --panel-2:#124f3d;
  --line:rgba(212,175,55,.25); --cream:#f6f1e1; --ink:#1c1a14;
  --gold:#d4af37; --gold-2:#f0d879; --danger:#d1453b;
  --freeze:#4d9ec2; --flip3:#e08a34; --second:#9a6fd6; --success:#4aa96c;
  --muted:#9fc3b1; --shadow:0 10px 30px rgba(0,0,0,.35);
  --current-glow:0 0 28px rgba(240,216,121,0.18);
  --ice-bg: linear-gradient(180deg, rgba(200,240,255,0.06), rgba(200,240,255,0.02));
}
*{box-sizing:border-box;}
html,body{margin:0;padding:0;height:100%;}
body{
  background:radial-gradient(ellipse at 50% -10%, #124f3d 0%, var(--bg) 45%, var(--bg-deep) 100%);
  font-family:'Nunito',sans-serif; color:var(--cream); overflow-x:hidden; min-height:100vh;
}
#app{position:relative; z-index:1; max-width:1180px; margin:0 auto; padding:14px 16px 40px;}
/* Top */
.topbar{display:flex;align-items:center;justify-content:space-between;padding:10px 18px;border-radius:16px;background:linear-gradient(180deg, rgba(0,0,0,.18), rgba(0,0,0,.05));border:1px solid var(--line);margin-bottom:14px;}
.logo{font-family:'Anton',sans-serif;font-size:32px;color:var(--gold-2);letter-spacing:2px}
.logo span{color:var(--cream)}
.meta{display:flex;gap:18px}
.meta-item{font-family:'Space Mono',monospace;font-size:13px;color:var(--muted)}
.ghost-btn{background:transparent;border:1px solid var(--gold);color:var(--gold-2);padding:8px 14px;border-radius:10px;cursor:pointer;font-weight:700}
/* Opponents row */
.opponents-row{display:flex;gap:12px;overflow-x:auto;padding:6px 2px 14px}
.opp-card{min-width:190px;flex:1 1 190px;background:linear-gradient(160deg,var(--panel-2),var(--panel));border:1px solid var(--line);border-radius:14px;padding:10px 12px;position:relative;transition:.25s}
.opp-card.inactive{opacity:.62}
.opp-card.current{border-color:var(--gold);box-shadow:var(--current-glow),0 8px 18px rgba(0,0,0,.35);transform:translateY(-4px);outline:1px solid rgba(240,216,121,0.06)}
.opp-header{display:flex;justify-content:space-between;align-items:center;margin-bottom:6px}
.opp-name{font-weight:800}
.opp-chips{display:flex;flex-wrap:wrap;gap:4px;min-height:30px;margin-bottom:6px}
.opp-scores{display:flex;justify-content:space-between;font-family:'Space Mono',monospace;font-size:11px;color:var(--muted)}
.empty-hint{font-size:11px;color:var(--muted);font-style:italic}

/* Opponent frozen overlay */
.opp-frozen{position:absolute;inset:6px;border-radius:12px;pointer-events:none;display:flex;align-items:center;justify-content:center;opacity:0;transform:scale(.98);transition:all .35s ease;background:var(--ice-bg);backdrop-filter:blur(2px);z-index:4}
.opp-frozen.show{opacity:1;transform:scale(1);box-shadow:0 10px 30px rgba(100,180,220,0.06)}
.opp-frozen .ice{width:78%;height:56%;background:radial-gradient(circle at 20% 20%, rgba(255,255,255,0.55) 0 6%, transparent 8%),radial-gradient(circle at 80% 40%, rgba(220,240,255,0.45) 0 6%, transparent 8%),linear-gradient(90deg, rgba(220,244,255,0.06), rgba(180,230,255,0.02));border-radius:6px;opacity:.95;animation:drift 3.8s linear infinite}@keyframes drift{0%{transform:translateY(-2px)}50%{transform:translateY(2px)}100%{transform:translateY(-2px)}}

/* Chips */
.chip{display:inline-flex;align-items:center;justify-content:center;min-width:26px;height:26px;padding:0 6px;border-radius:7px;font-family:'Space Mono',monospace;font-weight:700;font-size:13px;border:1px solid rgba(0,0,0,.25)}
.chip-number{background:var(--cream);color:var(--ink)}.chip-add{background:var(--success);color:#08260f}.chip-x2{background:var(--gold-2);color:#3a2a00}.chip-freeze{background:var(--freeze);color:#052733}.chip-flip_three{background:var(--flip3);color:#3a1e00}.chip-second_chance{background:var(--second);color:#20103a}.chip.voided{text-decoration:line-through;opacity:.45}.chip.used-shield{box-shadow:0 0 0 2px var(--second)}

/* Center */
.table-center{display:flex;flex-direction:column;align-items:center;gap:14px;padding:18px 10px 26px}
.flip-stage{perspective:1200px}
.flip-card{width:132px;height:184px;position:relative;transform-style:preserve-3d;transition:transform .5s cubic-bezier(.3,.6,.3,1)}
.flip-card.revealed{transform:rotateY(180deg)}
.card-face{position:absolute;inset:0;border-radius:14px;backface-visibility:hidden;display:flex;align-items:center;justify-content:center;flex-direction:column;box-shadow:var(--shadow);border:2px solid var(--gold)}
.card-back{background:repeating-linear-gradient(45deg,#0d4a38 0 8px,#0a3a2b 8px 16px);color:var(--gold-2);font-family:'Anton',sans-serif;font-size:22px}
.card-front{background:var(--cream);color:var(--ink);transform:rotateY(180deg);font-family:'Anton',sans-serif;font-size:48px}
.card-front .front-sub{font-family:'Nunito',sans-serif;font-size:11px;color:#6b6250;margin-top:2px}
.card-front.busted{background:#f6dcd9;color:var(--danger)}
.card-front.is-flip7{background:var(--gold-2)}

/* stage info & controls */
.turn-indicator{font-weight:800;font-size:16px;color:var(--gold-2);min-height:22px}
.round-score{font-family:'Space Mono',monospace;font-size:13px;color:var(--muted)}
.controls{display:flex;gap:14px}
.btn{border:none;padding:14px 30px;border-radius:14px;font-weight:800;font-size:16px;cursor:pointer;box-shadow:var(--shadow)}
.btn:disabled{opacity:.35;cursor:not-allowed}
.hit-btn{background:linear-gradient(180deg,var(--gold-2),var(--gold));color:#3a2a00}.stay-btn{background:linear-gradient(180deg,#3a5b4d,#264337);color:var(--cream);border:1px solid var(--line)}

/* Self panel */
.self-panel{background:linear-gradient(160deg,var(--panel-2),var(--panel));border:1px solid var(--gold);border-radius:16px;padding:14px 16px;margin-top:6px;transition:.25s;position:relative;min-height:84px}
.self-panel.current{box-shadow:var(--current-glow),0 12px 30px rgba(0,0,0,.45);transform:translateY(-4px);outline:2px solid rgba(240,216,121,0.08)}
.self-header{display:flex;justify-content:space-between;align-items:center;margin-bottom:10px}
.self-frozen{position:absolute;inset:8px;border-radius:12px;pointer-events:none;display:flex;align-items:center;justify-content:center;opacity:0;transform:scale(.98);transition:all .35s ease;background:var(--ice-bg);backdrop-filter:blur(2px);z-index:6}
.self-frozen.show{opacity:1;transform:scale(1);box-shadow:0 12px 34px rgba(80,160,220,0.08)}

/* action highlights */
.action-highlight{box-shadow:0 0 0 6px rgba(144,200,255,0.06),0 8px 20px rgba(0,0,0,0.35);transform:translateY(-6px);transition:all .28s ease}
.flip7-glow{box-shadow:0 0 34px rgba(240,216,121,0.25),0 12px 36px rgba(0,0,0,0.45);transform:translateY(-6px);transition:all .28s ease}

/* Log panel */
.log-panel{position:fixed;right:14px;top:100px;width:260px;max-height:60vh;background:rgba(6,40,33,.92);border:1px solid var(--line);border-radius:14px;display:flex;flex-direction:column;overflow:hidden;backdrop-filter:blur(4px);z-index:60;transition:all .22s ease}
.log-header{padding:8px 10px;display:flex;align-items:center;justify-content:space-between;color:var(--gold-2);font-weight:800;border-bottom:1px solid var(--line)}
.log-body{padding:8px 12px;overflow-y:auto;font-size:12px;color:var(--muted)}
.log-panel.minimized{width:52px;height:52px;right:16px;bottom:16px;top:auto;border-radius:50%;padding:6px;align-items:center;justify-content:center;box-shadow:var(--shadow)}
.log-panel.minimized .log-body{display:none}
.log-panel.minimized .log-header{padding:0;border-bottom:none;justify-content:center}
.log-min-btn{background:transparent;border:none;color:var(--gold-2);cursor:pointer;padding:6px;border-radius:8px}

/* floating toggle */
.log-toggle{display:flex;position:fixed;bottom:16px;right:16px;z-index:70;background:var(--gold);color:#3a2a00;border:none;border-radius:50%;width:52px;height:52px;font-size:20px;box-shadow:var(--shadow);cursor:pointer;align-items:center;justify-content:center}

/* modal & misc */
.modal-overlay{position:fixed;inset:0;background:rgba(4,20,16,.72);backdrop-filter:blur(3px);display:flex;align-items:center;justify-content:center;z-index:80;padding:16px}
.modal{background:linear-gradient(160deg,#12513f,#0c3b2d);border:1px solid var(--gold);border-radius:18px;padding:26px;max-width:640px;width:100%;box-shadow:var(--shadow);max-height:86vh;overflow-y:auto}
.mode-btn{padding:10px 14px;border-radius:10px;border:1px solid var(--line);background:rgba(255,255,255,.04);color:var(--cream);cursor:pointer;font-weight:800}
.mode-btn.selected{background:var(--gold);color:#3a2a00;border-color:var(--gold)}
.count-btn{width:44px;height:44px;border-radius:10px;border:1px solid var(--line);background:rgba(255,255,255,.04);color:var(--cream);cursor:pointer}
.count-btn.selected{background:var(--gold);color:#3a2a00;border-color:var(--gold)}
@media(max-width:760px){.log-panel{display:none}.log-toggle{display:flex}}
</style>
</head>
<body>
<div id="app">
  <header class="topbar">
    <h1 class="logo">FLIP<span>7</span></h1>
    <div class="meta">
      <div class="meta-item">第 <span id="roundNum">0</span> 局</div>
      <div class="meta-item">目標 <span id="targetScoreDisplay">200</span> 分</div>
      <div class="meta-item">牌庫剩 <span id="deckCount">0</span> 張</div>
    </div>
    <div class="topbar-actions">
      <button class="ghost-btn" id="rulesBtn">規則</button>
      <button class="ghost-btn" id="newGameBtn">重新開始</button>
    </div>
  </header>

  <section class="opponents-row" id="opponentsRow" aria-live="polite"></section>

  <section class="table-center">
    <div class="flip-stage">
      <div class="flip-card" id="flipCard">
        <div class="card-face card-back"><div>FLIP<br>7</div><small>七 翻 天</small></div>
        <div class="card-face card-front" id="cardFront"></div>
      </div>
    </div>

    <div class="stage-info">
      <div class="turn-indicator" id="turnIndicator">—</div>
      <div class="round-score">本輪分數：<span id="myRoundScore">0</span></div>
    </div>

    <div class="controls">
      <button id="hitBtn" class="btn hit-btn" disabled>翻牌 Hit</button>
      <button id="stayBtn" class="btn stay-btn" disabled>停止 Stay</button>
    </div>
  </section>

  <section class="self-panel" id="selfPanel">
    <div class="self-header">
      <span id="selfName">你</span>
      <span class="self-total">總分：<span id="selfTotal">0</span></span>
    </div>
    <div class="card-row" id="selfCards"></div>
    <div class="self-frozen" id="selfFrozen"><div class="ice"></div></div>
  </section>
</div>

<aside class="log-panel" id="logPanel" role="region" aria-label="遊戲紀錄">
  <div class="log-header">
    <div>📜 遊戲紀錄</div>
    <div><button class="log-min-btn" id="logMinimizeBtn" title="收合紀錄">—</button></div>
  </div>
  <div class="log-body" id="logBody"></div>
</aside>
<button class="log-toggle" id="logToggle" title="展開/收合遊戲紀錄">📜</button>

<!-- Setup Modal -->
<div id="setupModal" class="modal-overlay">
  <div class="modal">
    <h2>FLIP 7 七翻天</h2>
    <p>輪到你時可翻牌或停止。翻到重複數字爆牌；集滿 7 張不同數字可獲得額外 +15 分並結束本輪。</p>

    <div style="margin-top:12px">
      <div style="font-size:12px;font-weight:800;color:var(--muted);margin-bottom:8px">遊戲模式</div>
      <div id="modeBtns" style="display:flex;gap:8px">
        <button class="mode-btn selected" data-mode="computer">電腦陪玩</button>
        <button class="mode-btn" data-mode="human">真人遊戲</button>
      </div>
    </div>

    <div style="margin-top:12px">
      <div style="font-size:12px;font-weight:800;color:var(--muted);margin-bottom:8px">玩家人數</div>
      <div id="playerCountBtns" style="display:flex;gap:8px"></div>
    </div>

    <div id="namesArea" style="display:none;margin-top:12px">
      <div style="font-size:12px;font-weight:800;color:var(--muted);margin-bottom:8px">玩家名稱（真人遊戲）</div>
      <div id="editNames" style="display:flex;flex-direction:column;gap:8px"></div>
    </div>

    <div style="margin-top:12px">
      <div style="font-size:12px;font-weight:800;color:var(--muted);margin-bottom:8px">目標分數</div>
      <input id="targetScoreInput" type="number" value="200" min="50" step="10" style="padding:10px;border-radius:8px;border:1px solid var(--line);background:rgba(255,255,255,.04);color:var(--cream)">
    </div>

    <button id="startGameBtn" class="primary-btn" style="margin-top:14px">開始遊戲</button>
    <button id="rulesLinkBtn" class="ghost-btn" style="margin-top:8px">查看完整規則說明</button>
  </div>
</div>

<!-- Target modal -->
<div id="targetModal" class="modal-overlay" style="display:none">
  <div class="modal" style="max-width:420px">
    <h3 id="targetModalTitle"></h3>
    <div id="targetOptions" style="display:flex;flex-direction:column;gap:8px"></div>
  </div>
</div>

<!-- Round summary -->
<div id="roundSummaryModal" class="modal-overlay" style="display:none">
  <div class="modal">
    <h2>第 <span id="summaryRoundNum"></span> 局結算</h2>
    <table id="summaryTable" class="summary-table"></table>
    <button id="nextRoundBtn" class="primary-btn">下一局</button>
  </div>
</div>

<!-- Game over -->
<div id="gameOverModal" class="modal-overlay" style="display:none">
  <div class="modal">
    <h2 id="gameOverTitle">遊戲結束！</h2>
    <div id="finalScores"></div>
    <button id="playAgainBtn" class="primary-btn">再玩一次</button>
  </div>
</div>

<!-- Rules -->
<div id="rulesModal" class="modal-overlay" style="display:none">
  <div class="modal">
    <h2>遊戲規則</h2>
    <p><b>目標：</b>率先累積到目標分數（預設 200 分）者獲勝。</p>
    <p><b>回合：</b>每人依序選擇「翻牌」或「停止」。翻到重複數字爆牌；集滿 7 張不同數字可立即結束本輪並 +15 分。</p>
    <button id="closeRulesBtn" class="primary-btn">關閉</button>
  </div>
</div>

<script>
(function(){
"use strict";

/* ---------------- WebAudio synth ---------------- */
let audioCtx = null;
function ensureAudioContext(){ if(!audioCtx) audioCtx = new (window.AudioContext || window.webkitAudioContext)(); return audioCtx; }
function playTone(freq, type='sine', time=0.12, gain=0.12, when=0){
  try{
    const ctx = ensureAudioContext(); const t0 = ctx.currentTime + when; const osc = ctx.createOscillator(); const g = ctx.createGain();
    osc.type = type; osc.frequency.value = freq; g.gain.setValueAtTime(gain, t0); g.gain.exponentialRampToValueAtTime(0.0001, t0 + time);
    osc.connect(g); g.connect(ctx.destination); osc.start(t0); osc.stop(t0 + time + 0.02);
  }catch(e){}
}
function playSound(name){
  if(audioCtx && audioCtx.state==='suspended') audioCtx.resume().catch(()=>{});
  switch(name){
    case 'flip': playTone(880,'triangle',0.08,0.12); playTone(1320,'sine',0.08,0.06,0.01); break;
    case 'action': playTone(520,'sawtooth',0.14,0.12); playTone(660,'sine',0.12,0.06,0.02); break;
    case 'bust': playTone(120,'sine',0.26,0.18); break;
    case 'flip7': playTone(880,'sine',0.08,0.12,0); playTone(1040,'sine',0.08,0.10,0.09); playTone(1320,'sine',0.12,0.08,0.18); playTone(1760,'triangle',0.18,0.06,0.26); break;
    case 'hit': playTone(920,'square',0.06,0.08); break;
    case 'stay': playTone(440,'sine',0.10,0.09); break;
    case 'second_chance': playTone(980,'triangle',0.12,0.10); playTone(1240,'sine',0.08,0.06,0.06); break;
    case 'freeze': playTone(260,'sine',0.22,0.14); playTone(360,'sine',0.18,0.08,0.05); break;
    case 'flip_three': playTone(700,'sine',0.07,0.09); playTone(820,'sine',0.07,0.08,0.08); playTone(920,'sine',0.12,0.06,0.16); break;
    case 'modifier': playTone(620,'sine',0.10,0.10); playTone(760,'sine',0.08,0.06,0.06); break;
    case 'target': playTone(660,'sine',0.08,0.08); break;
    default: playTone(600,'sine',0.06,0.06);
  }
}

/* ---------------- State & Deck ---------------- */
const state = { players:[], deck:[], discard:[], dealerIndex:0, turnPointer:0, targetScore:200, roundNumber:0, running:false, mode:'computer', isDealing:false };
const BOT_NAMES=['阿翔','小美','老王','阿凱','珍妮','小林'];
const BOT_PERSONALITIES=['balanced','aggressive','cautious','balanced','aggressive','cautious'];

function shuffle(arr){ const a=arr.slice(); for(let i=a.length-1;i>0;i--){ const j=Math.floor(Math.random()*(i+1)); [a[i],a[j]]=[a[j],a[i]] } return a; }
function buildDeck(){ const cards=[]; let uid=0; for(let v=0;v<=12;v++){ const count=v===0?1:v; for(let k=0;k<count;k++) cards.push({uid:uid++,type:'number',value:v}); } [2,4,6,8,10].forEach(v=>cards.push({uid:uid++,type:'modifier',kind:'add',value:v})); cards.push({uid:uid++,type:'modifier',kind:'x2'}); for(let k=0;k<3;k++) cards.push({uid:uid++,type:'action',kind:'freeze'}); for(let k=0;k<3;k++) cards.push({uid:uid++,type:'action',kind:'flip_three'}); for(let k=0;k<3;k++) cards.push({uid:uid++,type:'action',kind:'second_chance'}); return shuffle(cards); }
function drawCard(){ if(state.deck.length===0){ if(state.discard.length===0) return null; state.deck = shuffle(state.discard); state.discard = []; log('🔄 牌庫已用盡，棄牌堆重新洗混！','log-good'); } return state.deck.pop(); }
function remainingCopies(value){ let n=0; for(const c of state.deck) if(c.type==='number'&&c.value===value) n++; for(const c of state.discard) if(c.type==='number'&&c.value===value) n++; for(const p of state.players) if(p.round&&p.round.numbers) for(const v of p.round.numbers) if(v===value) n--; return Math.max(0,n); }

/* ---------------- UI effect helper ---------------- */
function showActionEffect(playerIdx,effectType){
  setTimeout(()=>{
    if(playerIdx===0){
      const el = document.getElementById('selfPanel');
      if(!el) return;
      if(effectType==='freeze'){ el.classList.add('action-highlight'); setTimeout(()=>el.classList.remove('action-highlight'),900); }
      else if(effectType==='flip7'){ el.classList.add('flip7-glow'); setTimeout(()=>el.classList.remove('flip7-glow'),1200); }
      else { el.classList.add('action-highlight'); setTimeout(()=>el.classList.remove('action-highlight'),700); }
    } else {
      const row = document.getElementById('opponentsRow'); if(!row) return;
      const child = row.children[playerIdx-1]; if(!child) return;
      if(effectType==='freeze'){ child.classList.add('action-highlight'); setTimeout(()=>child.classList.remove('action-highlight'),900); }
      else if(effectType==='flip7'){ child.classList.add('flip7-glow'); setTimeout(()=>child.classList.remove('flip7-glow'),1200); }
      else { child.classList.add('action-highlight'); setTimeout(()=>child.classList.remove('action-highlight'),700); }
    }
  },60);
}

/* ---------------- Setup ---------------- */
let selectedMode='computer', selectedPlayerCount=4;
function initGame(mode,numPlayers,names,targetScore){
  state.mode=mode; state.players=[];
  if(mode==='computer'){
    state.players.push({id:0,name:(names&&names[0])?names[0]:'你',isBot:false,personality:null,total:0});
    for(let i=1;i<numPlayers;i++) state.players.push({id:i,name:BOT_NAMES[(i-1)%BOT_NAMES.length],isBot:true,personality:BOT_PERSONALITIES[(i-1)%BOT_PERSONALITIES.length],total:0});
  } else {
    for(let i=0;i<numPlayers;i++) state.players.push({id:i,name:(names&&names[i])?names[i]:`玩家${i+1}`,isBot:false,personality:null,total:0});
  }
  state.deck = buildDeck(); state.discard=[]; state.dealerIndex=0; state.roundNumber=0; state.targetScore=targetScore; state.running=true; clearLog(); startRound();
}

/* ---------------- Round flow (with fast-deal mode) ---------------- */
function seatOrderFrom(start){ const n=state.players.length; const arr=[]; for(let i=0;i<n;i++) arr.push((start+i)%n); return arr; }
function startRound(){
  state.roundNumber++;
  for(const p of state.players) p.round={numbers:[],modifiers:[],played:[],secondChance:false,busted:false,stayed:false,frozen:false,flip7:false,active:true,score:0};
  render();
  log(`—— 第 ${state.roundNumber} 局開始，發牌員：${state.players[state.dealerIndex].name} ——`);
  const order = seatOrderFrom((state.dealerIndex+1)%state.players.length);
  state.isDealing = true;
  setTimeout(()=> dealSequence(order,0), 60);
}
function dealSequence(order,i){
  if(i>=order.length){
    state.isDealing = false;
    for(let k=0;k<state.players.length;k++){ const idx=(state.dealerIndex+1+k)%state.players.length; if(state.players[idx].round.active){ state.turnPointer=idx; break; } }
    advanceTurn(); return;
  }
  const idx = order[i];
  if(!state.players[idx].round.active){ dealSequence(order,i+1); return; }
  drawAndAnimate(idx, ()=>{ dealSequence(order,i+1); });
}

// drawAndAnimate: use faster animations for opponents during initial dealing
function drawAndAnimate(pIdx,onResolved){
  try{
    const card = drawCard();
    if(!card){ onResolved(); return; }
    const fast = !!(state.isDealing && pIdx!==0);
    requestAnimationFrame(()=>{
      playFlipAnimation(card, ()=>{
        try{ resolveCard(pIdx, card, onResolved); } catch(err){ console.error('resolveCard error',err); try{ onResolved(); }catch(e){} }
      }, fast, pIdx);
    });
  }catch(err){ console.error('drawAndAnimate error', err); try{ onResolved(); }catch(e){} }
}

/* ---------------- Resolve card ---------------- */
function resolveCard(pIdx, card, done){
  const p = state.players[pIdx];
  p.round.played.push(card);
  render();

  if(card.type==='number'){
    if(p.round.numbers.includes(card.value)){
      if(p.round.secondChance){
        p.round.secondChance=false; card.voided=true; markSecondChanceUsed(p); playSound('second_chance'); showActionEffect(pIdx,'second_chance'); log(`♻️ ${p.name} 翻到重複的 ${card.value}，用「再來一次」化解了！`,'log-good'); render(); done();
      } else {
        p.round.busted=true; p.round.active=false; pulseStage('bust'); playSound('bust'); showActionEffect(pIdx,'bust'); log(`💥 ${p.name} 翻到重複的 ${card.value}，爆牌了！`,'log-bust'); render(); done();
      }
    } else {
      p.round.numbers.push(card.value); playSound('flip'); showActionEffect(pIdx,'flip'); log(`${p.name} 翻到數字 ${card.value}。`);
      if(p.round.numbers.length===7){ p.round.flip7=true; pulseStage('flip7'); playSound('flip7'); showActionEffect(pIdx,'flip7'); log(`🎉 ${p.name} 集滿 7 張不同數字，達成 FLIP 7！額外 +15 分！`,'log-flip7'); }
      render(); done();
    }
  } else if(card.type==='modifier'){
    p.round.modifiers.push(card); playSound('modifier'); showActionEffect(pIdx,'modifier'); log(`${p.name} 拿到調整牌 ${card.kind==='x2'?'×2':'+'+card.value}。`); render(); done();
  } else if(card.type==='action'){
    if(card.kind==='freeze') playSound('freeze');
    else if(card.kind==='flip_three') playSound('flip_three');
    else if(card.kind==='second_chance') playSound('second_chance');
    showActionEffect(pIdx, card.kind);

    if(card.kind==='freeze'){
      requestTarget(pIdx,'freeze',(targetIdx)=>{ if(targetIdx!==null){ applyFreeze(targetIdx); } else log('❄️ 凍結卡未指定目標，被棄置。'); render(); done(); });
    } else if(card.kind==='second_chance'){
      requestTarget(pIdx,'second_chance',(targetIdx)=>{ if(targetIdx===null){ log('♻️「再來一次」沒有合適的對象，被棄置。'); render(); done(); return; } state.players[targetIdx].round.secondChance=true; log(`♻️ ${state.players[targetIdx].name} 獲得「再來一次」的保護。`,'log-good'); render(); done(); });
    } else if(card.kind==='flip_three'){
      requestTarget(pIdx,'flip_three',(targetIdx)=>{ if(targetIdx===null){ log('⚡ 翻三張卡未指定目標，被棄置。'); render(); done(); return; } log(`⚡ ${state.players[targetIdx].name} 必須連續翻三張牌！`); doFlipThree(targetIdx,done); });
    }
  }
}
function markSecondChanceUsed(p){ for(let i=p.round.played.length-1;i>=0;i--){ const c=p.round.played[i]; if(c.type==='action'&&c.kind==='second_chance'&&!c.usedMark){ c.usedMark=true; break; } } }
function doFlipThree(targetIdx,done){
  let count=0; const pending=[];
  function step(){
    const p=state.players[targetIdx];
    if(count>=3||!p.round.active||p.round.flip7){ resolveNestedQueue(pending,done); return; }
    count++;
    const card=drawCard();
    if(!card){ resolveNestedQueue(pending,done); return; }
    const fast = !!(state.isDealing && targetIdx!==0);
    if(card.type==='action'&&(card.kind==='freeze'||card.kind==='flip_three')){
      playFlipAnimation(card, ()=>{ log(`(翻三張中) 翻到「${card.kind==='freeze'?'凍結':'翻三張'}」，將於三張翻完後處理。`); pending.push({sourceIdx:targetIdx,card}); step(); }, fast, targetIdx);
    } else {
      playFlipAnimation(card, ()=>{ resolveCard(targetIdx,card,step); }, fast, targetIdx);
    }
  }
  step();
}
function resolveNestedQueue(queueArr,done){ if(queueArr.length===0){ done(); return; } const item=queueArr.shift(); const p=state.players[item.sourceIdx]; if(!p.round.active){ resolveNestedQueue(queueArr,done); return; } playFlipAnimation(item.card, ()=>{ resolveCard(item.sourceIdx,item.card,()=>resolveNestedQueue(queueArr,done)); }, !!(state.isDealing && item.sourceIdx!==0), item.sourceIdx); }
function applyFreeze(targetIdx){ const p=state.players[targetIdx]; if(!p.round.active) return; p.round.frozen=true; p.round.stayed=true; p.round.active=false; p.round.score=computeLiveScore(p); log(`❄️ ${p.name} 被凍結，保留本輪 ${p.round.score} 分並退出本輪。`,'log-good'); }

/* ---------------- Target selection ---------------- */
function requestTarget(sourceIdx,kind,callback){
  const activeIdxs = state.players.map((p,i)=>i).filter(i=>state.players[i].round.active);
  let eligible = activeIdxs;
  if(kind==='second_chance'){ eligible = activeIdxs.filter(i=>!state.players[i].round.secondChance); if(eligible.length===0){ callback(null); return; } }
  const src=state.players[sourceIdx];
  if(!src.isBot){ openTargetModal(kind,eligible,callback); } else {
    let chosen=null;
    if(kind==='freeze'){ const others=eligible.filter(i=>i!==sourceIdx); chosen=others.length>0?others.reduce((a,b)=> computeLiveScore(state.players[b])>computeLiveScore(state.players[a])?b:a):sourceIdx; }
    else if(kind==='flip_three'){ const others=eligible.filter(i=>i!==sourceIdx); chosen=others.length>0?others.reduce((a,b)=> state.players[b].round.numbers.length>state.players[a].round.numbers.length?b:a):sourceIdx; }
    else chosen = eligible.includes(sourceIdx)?sourceIdx:eligible[0];
    callback(chosen);
  }
}
function openTargetModal(kind,eligibleIdxs,callback){
  const modal=document.getElementById('targetModal'), title=document.getElementById('targetModalTitle'), options=document.getElementById('targetOptions');
  const titles={freeze:'❄️ 選擇要「凍結」的對象', flip_three:'⚡ 選擇要「翻三張」的對象', second_chance:'♻️ 選擇要獲得「再來一次」的對象'};
  title.textContent=titles[kind]; options.innerHTML='';
  eligibleIdxs.forEach(idx=>{ const p=state.players[idx]; const btn=document.createElement('button'); btn.className='target-option-btn'; btn.style.display='flex'; btn.style.justifyContent='space-between'; btn.style.padding='10px'; btn.style.border='1px solid var(--line)'; btn.style.borderRadius='8px'; btn.style.background='rgba(255,255,255,0.03)'; btn.style.color='var(--cream)'; btn.innerHTML=`<span>${p.name}${idx===0?'（你）':''}</span><span style="font-family:Space Mono">${computeLiveScore(p)} 分</span>`; btn.onclick=()=>{ modal.style.display='none'; playSound('target'); callback(idx); }; options.appendChild(btn); });
  modal.style.display='flex';
}

/* ---------------- Turn loop ---------------- */
function advanceTurn(){ const n=state.players.length; let found=false; for(let i=1;i<=n;i++){ const idx=(state.turnPointer+i)%n; if(state.players[idx].round.active){ state.turnPointer=idx; beginPlayerTurn(idx); found=true; break; } } if(!found) endRound(); }
function beginPlayerTurn(idx){ render(); updateTurnIndicator(idx); const p=state.players[idx]; if(p.isBot){ disableControls(); // shorter thinking time for bots
    setTimeout(()=>{ const decision=botDecideHitStay(idx); if(decision==='hit') performHit(idx); else performStay(idx); }, 320 + Math.random()*380);
  } else enableControls(); }
function updateTurnIndicator(idx){ const p=state.players[idx]; document.getElementById('turnIndicator').textContent = p.isBot?`輪到 ${p.name} 思考中...`:`輪到 ${p.name}，翻牌還是停止？`; }
function performHit(pIdx){ if(audioCtx===null) ensureAudioContext(); playSound('hit'); disableControls(); drawAndAnimate(pIdx, ()=>{ checkRoundEndOrContinue(); }); }
function performStay(pIdx){ if(audioCtx===null) ensureAudioContext(); playSound('stay'); const p=state.players[pIdx]; p.round.stayed=true; p.round.active=false; p.round.score=computeLiveScore(p); log(`${p.name} 選擇停止，保留 ${p.round.score} 分。`); render(); checkRoundEndOrContinue(); }
function checkRoundEndOrContinue(){ const anyFlip7 = state.players.some(p=>p.round.flip7); const activeCount = state.players.filter(p=>p.round.active).length; if(anyFlip7||activeCount===0){ endRound(); return; } advanceTurn(); }

/* ---------------- Scoring & end round ---------------- */
function computeLiveScore(p){ if(p.round.busted) return 0; let base = p.round.numbers.reduce((a,b)=>a+b,0); const hasX2=p.round.modifiers.some(m=>m.kind==='x2'); if(hasX2) base*=2; const flat=p.round.modifiers.filter(m=>m.kind==='add').reduce((a,m)=>a+m.value,0); base+=flat; if(p.round.flip7) base+=15; return base; }
function endRound(){ disableControls(); document.getElementById('turnIndicator').textContent='本局結算中...'; for(const p of state.players){ if(p.round.busted) p.round.score=0; else if(!p.round.stayed && !p.round.frozen) p.round.score=computeLiveScore(p); p.round.active=false; p.total+=p.round.score; } render(); setTimeout(showRoundSummary,500); }
function showRoundSummary(){ document.getElementById('summaryRoundNum').textContent=state.roundNumber; const table=document.getElementById('summaryTable'); const maxScore=Math.max(...state.players.map(p=>p.round.score)); let html='<tr><th>玩家</th><th>本輪牌組</th><th>本輪分數</th><th>總分</th></tr>'; state.players.forEach(p=>{ const chips=p.round.played.map(c=>chipHTML(c)).join('')||'<span class="empty-hint">無</span>'; const isTop=p.round.score===maxScore && maxScore>0; html+=`<tr class="${isTop?'row-winner':''}"><td>${p.name}${p.id===0?'（你）':''}</td><td><div class="summary-chips">${chips}</div></td><td style="font-family:Space Mono">${p.round.score}</td><td style="font-family:Space Mono;color:var(--gold-2);font-weight:700">${p.total}</td></tr>`; }); table.innerHTML=html; document.getElementById('roundSummaryModal').style.display='flex'; }
function proceedAfterSummary(){ document.getElementById('roundSummaryModal').style.display='none'; const maxTotal=Math.max(...state.players.map(p=>p.total)); if(maxTotal>=state.targetScore){ const winners=state.players.filter(p=>p.total===maxTotal); if(winners.length===1){ showGameOver(); return; } log('🔥 分數平手！繼續延長賽...','log-good'); } nextRound(); }
function nextRound(){ for(const p of state.players) state.discard.push(...p.round.played); state.dealerIndex=(state.dealerIndex+1)%state.players.length; startRound(); }
function showGameOver(){ state.running=false; const sorted=state.players.slice().sort((a,b)=>b.total-a.total); const winner=sorted[0]; document.getElementById('gameOverTitle').textContent=`🏆 ${winner.name}${winner.id===0?'（你）':''} 獲勝！`; const finalDiv=document.getElementById('finalScores'); finalDiv.innerHTML=sorted.map((p,i)=>`<div style="display:flex;justify-content:space-between;padding:8px;border-radius:8px;background:rgba(255,255,255,0.02);margin-bottom:6px"><span>#${i+1} ${p.name}${p.id===0?'（你）':''}</span><span style="font-family:Space Mono;font-weight:700">${p.total} 分</span></div>`).join(''); document.getElementById('gameOverModal').style.display='flex'; }

/* ---------------- Bot AI ---------------- */
function botDecideHitStay(pIdx){ const p=state.players[pIdx]; const nums=p.round.numbers; if(nums.length===0) return 'hit'; const risky=nums.reduce((s,v)=>s+remainingCopies(v),0); const totalRemaining=Math.max(state.deck.length+state.discard.length,1); const bustProb=risky/totalRemaining; let threshold=0.45-nums.length*0.02; if(p.personality==='aggressive') threshold+=0.15; if(p.personality==='cautious') threshold-=0.15; if(nums.length===6) threshold+=0.12; const currentScore=computeLiveScore(p); if(p.total+currentScore>=state.targetScore-10) threshold-=0.1; threshold=Math.max(0.05,Math.min(0.9,threshold)); return bustProb < threshold ? 'hit' : 'stay'; }

/* ---------------- Visuals / render ---------------- */
function cardLabel(card){ if(card.type==='number') return card.value; if(card.type==='modifier') return card.kind==='x2'?'×2':'+'+card.value; if(card.type==='action'){ if(card.kind==='freeze') return '❄'; if(card.kind==='flip_three') return '⚡3'; if(card.kind==='second_chance') return '♻'; } return '?'; }
function cardSubLabel(card){ if(card.type==='number') return '數字牌'; if(card.type==='modifier') return card.kind==='x2'?'加倍':'加分'; if(card.type==='action'){ if(card.kind==='freeze') return '凍結'; if(card.kind==='flip_three') return '翻三張'; if(card.kind==='second_chance') return '再來一次'; } return ''; }
function cardClass(card){ if(card.type==='number') return 'chip-number'; if(card.type==='modifier') return card.kind==='x2'?'chip-x2':'chip-add'; return 'chip-'+card.kind; }
function chipHTML(card){ const voided=card.voided?' voided':''; const shield=card.usedMark?' used-shield':''; return `<span class="chip ${cardClass(card)}${voided}${shield}" title="${cardSubLabel(card)}">${cardLabel(card)}</span>`; }

function render(){ document.getElementById('roundNum').textContent=state.roundNumber; document.getElementById('targetScoreDisplay').textContent=state.targetScore; document.getElementById('deckCount').textContent=state.deck.length; renderOpponents(); renderSelf(); }
function statusBadgeHTML(p){ if(p.round.busted) return '<span class="badge badge-bust">爆牌</span>'; if(p.round.flip7) return '<span class="badge badge-flip7">FLIP 7!</span>'; if(p.round.frozen) return '<span class="badge badge-freeze">凍結</span>'; if(p.round.stayed) return '<span class="badge badge-stay">停止</span>'; if(p.round.active) return '<span class="badge badge-active">場上</span>'; return ''; }

function renderOpponents(){
  const row=document.getElementById('opponentsRow'); row.innerHTML='';
  state.players.slice(1).forEach((p,i)=>{
    const idx=i+1;
    const chips = p.round ? p.round.played.map(c=>chipHTML(c)).join('') : '';
    const score = p.round ? computeLiveScore(p) : 0;
    const isCurrent = state.running && state.turnPointer===idx && p.round && p.round.active;
    const div=document.createElement('div');
    div.className='opp-card' + ((p.round&&p.round.active)?'':' inactive') + (isCurrent?' current':'');
    div.innerHTML = `<div class="opp-header"><span class="opp-name">${p.name}</span>${p.round?statusBadgeHTML(p):''}</div><div class="opp-chips">${chips||'<span class="empty-hint">尚未翻牌</span>'}</div><div class="opp-scores"><span>本輪 ${score}</span><span class="opp-total">總分 ${p.total}</span></div><div class="opp-frozen ${p.round&&p.round.frozen?'show':''}"><div class="ice"></div></div>`;
    row.appendChild(div);
  });
}

function renderSelf(){ const p=state.players[0]; if(!p) return; const selfPanel=document.getElementById('selfPanel'); if(state.running && state.turnPointer===0 && p.round && p.round.active) selfPanel.classList.add('current'); else selfPanel.classList.remove('current'); document.getElementById('selfTotal').textContent=p.total; document.getElementById('myRoundScore').textContent=p.round?computeLiveScore(p):0; const chips = p.round ? p.round.played.map(c=>chipHTML(c)).join('') : ''; document.getElementById('selfCards').innerHTML = chips || '<span class="empty-hint">尚未翻牌，輪到你時按下「翻牌」開始！</span>'; const selfFrozen=document.getElementById('selfFrozen'); if(p.round && p.round.frozen) selfFrozen.classList.add('show'); else selfFrozen.classList.remove('show'); }

/* ---------------- Flip animation (fast-mode support) ---------------- */
/*
  playFlipAnimation(card, cb, fast, playerIdx)
  - fast: boolean 跳 shorter timeouts for quick dealing (used during initial dealing for bots)
  - playerIdx: optional for debugging / future per-player logic
*/
function playFlipAnimation(card, cb, fast=false, playerIdx=0){
  const stage=document.getElementById('flipCard'); const front=document.getElementById('cardFront');
  stage.classList.remove('revealed'); front.classList.remove('busted','is-flip7');
  if(fast){
    // quick update, minimal delay
    front.className = 'card-face card-front type-'+card.type;
    front.innerHTML = `<div>${cardLabel(card)}</div><div class="front-sub">${cardSubLabel(card)}</div>`;
    // no heavy reflow delay, call cb after short time
    playSound('flip');
    setTimeout(cb, 180);
    return;
  }
  // normal animation path
  setTimeout(()=>{
    front.className = 'card-face card-front type-'+card.type;
    front.innerHTML = `<div>${cardLabel(card)}</div><div class="front-sub">${cardSubLabel(card)}</div>`;
    void front.offsetWidth;
    stage.classList.add('revealed');
    playSound('flip');
    setTimeout(cb, 520);
  }, 260);
}
function pulseStage(type){ const el=document.getElementById('flipCard').parentElement; el.classList.remove('pulse-bust','pulse-flip7'); void el.offsetWidth; el.classList.add(type==='bust'?'pulse-bust':'pulse-flip7'); const front=document.getElementById('cardFront'); if(type==='bust') front.classList.add('busted'); if(type==='flip7') front.classList.add('is-flip7'); }

/* ---------------- Log ---------------- */
function log(msg,cls){ const body=document.getElementById('logBody'); const d=document.createElement('div'); if(cls) d.className=cls; d.textContent=msg; body.appendChild(d); body.scrollTop=body.scrollHeight; }
function clearLog(){ document.getElementById('logBody').innerHTML=''; }

/* ---------------- Init & events ---------------- */
document.addEventListener('DOMContentLoaded',()=>{
  const pc=document.getElementById('playerCountBtns');
  for(let i=2;i<=6;i++){ const b=document.createElement('button'); b.className='count-btn'; b.textContent=i; if(i===selectedPlayerCount) b.classList.add('selected'); b.onclick=()=>{ selectedPlayerCount=i; [...pc.children].forEach(c=>c.classList.remove('selected')); b.classList.add('selected'); maybeRenderNameInputs(); }; pc.appendChild(b); }
  const modeBtns=document.getElementById('modeBtns'); [...modeBtns.children].forEach(btn=>{ btn.onclick=()=>{ [...modeBtns.children].forEach(c=>c.classList.remove('selected')); btn.classList.add('selected'); selectedMode=btn.dataset.mode; maybeRenderNameInputs(); } });
  function maybeRenderNameInputs(){ const namesArea=document.getElementById('namesArea'), edit=document.getElementById('editNames'); edit.innerHTML=''; if(selectedMode==='human'){ namesArea.style.display='block'; for(let i=0;i<selectedPlayerCount;i++){ const inp=document.createElement('input'); inp.className='name-input'; inp.placeholder=`玩家 ${i+1} 名稱`; inp.value=`玩家${i+1}`; inp.style.padding='8px'; inp.style.borderRadius='8px'; inp.style.border='1px solid var(--line)'; inp.style.background='rgba(255,255,255,0.03)'; inp.style.color='var(--cream)'; edit.appendChild(inp);} } else namesArea.style.display='none'; }
  maybeRenderNameInputs();

  document.getElementById('startGameBtn').onclick=()=>{
    const targetScore=Math.max(50,parseInt(document.getElementById('targetScoreInput').value,10)||200);
    let names=null;
    if(selectedMode==='human') names = Array.from(document.querySelectorAll('#editNames .name-input')).map(i=> (i.value&&i.value.trim())?i.value.trim():i.placeholder);
    else names=['你'];
    document.getElementById('setupModal').style.display='none';
    ensureAudioContext();
    initGame(selectedMode,selectedPlayerCount,names,targetScore);
  };

  document.getElementById('hitBtn').onclick = ()=>performHit(0);
  document.getElementById('stayBtn').onclick = ()=>performStay(0);
  document.getElementById('nextRoundBtn').onclick = ()=>proceedAfterSummary();
  document.getElementById('playAgainBtn').onclick = ()=>{ document.getElementById('gameOverModal').style.display='none'; document.getElementById('setupModal').style.display='flex'; };
  document.getElementById('newGameBtn').onclick = ()=>{ document.getElementById('roundSummaryModal').style.display='none'; document.getElementById('gameOverModal').style.display='none'; document.getElementById('setupModal').style.display='flex'; };
  document.getElementById('rulesBtn').onclick = ()=>document.getElementById('rulesModal').style.display='flex';
  document.getElementById('rulesLinkBtn').onclick = ()=>document.getElementById('rulesModal').style.display='flex';
  document.getElementById('closeRulesBtn').onclick = ()=>document.getElementById('rulesModal').style.display='none';

  const logPanel=document.getElementById('logPanel'), logMinBtn=document.getElementById('logMinimizeBtn'), logToggle=document.getElementById('logToggle');
  let minimized=false;
  logMinBtn.onclick = ()=>{ minimized=!minimized; if(minimized) logPanel.classList.add('minimized'); else logPanel.classList.remove('minimized'); };
  logToggle.onclick = ()=>{ if(logPanel.classList.contains('minimized')){ logPanel.classList.remove('minimized'); minimized=false; } else { logPanel.classList.add('minimized'); minimized=true; } };

  document.getElementById('targetModal').addEventListener('click', (e)=>{ if(e.target.id==='targetModal') e.currentTarget.style.display='none'; });

  log('歡迎！按「開始遊戲」啟動（首次互動會啟動音訊）。');
});
})();
</script>
</body>
</html>
