<!doctype html>
<html lang="es">
<head>
  <meta charset="utf-8"/>
  <meta name="viewport" content="width=device-width,initial-scale=1,viewport-fit=cover"/>
  <title>Helix Repair — Multi-Niveles (Sprite)</title>
  <style>
    :root{
      --bg:#0b0f14; --panel:#101823cc; --panel2:#0e1520e6;
      --text:#e8eef7; --muted:#b7c2d3; --accent:#23c6d1;
      --warn:#d1a223; --bad:#d14a4a; --ok:#2fd16f;
      --shadow:0 10px 30px rgba(0,0,0,.35);
      --radius:16px; --mono: ui-monospace, SFMono-Regular, Menlo, Monaco, Consolas, "Liberation Mono","Courier New", monospace;
      --sans: ui-sans-serif, system-ui, -apple-system, Segoe UI, Roboto, Helvetica, Arial;
    }
    *{box-sizing:border-box}
    html,body{height:100%;margin:0;background:var(--bg);color:var(--text);font-family:var(--sans)}
    #app{position:relative;height:100%;width:100%;overflow:hidden}
    canvas{position:absolute;inset:0;width:100%;height:100%;display:block;background:radial-gradient(1200px 900px at 50% 40%, #101a26 0%, #070a0f 60%, #05070b 100%)}
    .hud{position:absolute;left:12px;right:12px;top:12px;display:flex;flex-direction:column;gap:10px;pointer-events:none}
    .hud-row{display:flex;gap:10px;align-items:stretch}
    .panel{pointer-events:none;background:var(--panel);border:1px solid rgba(35,198,209,.18);box-shadow:var(--shadow);border-radius:var(--radius);padding:10px 12px;backdrop-filter:blur(8px);min-width:190px}
    .panel.wide{flex:1;min-width:260px}
    .panel-title{font-weight:700;letter-spacing:.02em;font-size:12px;color:var(--muted);text-transform:uppercase}
    .panel-sub{margin-top:6px;font-size:12px;color:var(--muted)}
    .panel-sub.tiny{font-size:11px;line-height:1.2}
    .bar{margin-top:8px;height:10px;border-radius:999px;background:rgba(255,255,255,.08);overflow:hidden}
    .bar-fill{height:100%;width:100%;background:linear-gradient(90deg,var(--accent),rgba(35,198,209,.35));border-radius:999px}
    .grid4{display:grid;grid-template-columns:repeat(4,1fr);gap:8px;margin-top:8px}
    .k{font-size:11px;color:var(--muted);font-family:var(--mono)}
    .v{font-size:14px;font-weight:800;font-family:var(--mono)}
    .chips{display:flex;gap:8px;flex-wrap:wrap;margin-top:8px;pointer-events:auto}
    .chip{border-radius:999px;border:1px solid rgba(255,255,255,.14);background:rgba(255,255,255,.06);color:var(--text);padding:8px 10px;font-size:12px;cursor:pointer}
    .chip.primary{border-color:rgba(35,198,209,.5);background:rgba(35,198,209,.12)}
    .chip.active{outline:2px solid rgba(35,198,209,.55)}
    .inv{display:grid;grid-template-columns:repeat(2,1fr);gap:6px;margin-top:8px;font-family:var(--mono);font-size:12px}
    .evidence-mini{margin-top:8px;font-size:12px;color:var(--text);font-family:var(--mono)}
    .overlay{position:absolute;inset:0;display:flex;align-items:center;justify-content:center;background:rgba(0,0,0,.55);backdrop-filter:blur(6px);pointer-events:auto}
    .hidden{display:none!important}
    .modal{width:min(900px,calc(100vw - 28px));max-height:min(88vh,900px);overflow:auto;background:var(--panel2);border:1px solid rgba(35,198,209,.25);border-radius:24px;box-shadow:var(--shadow);padding:16px}
    .modal h2{margin:0 0 10px 0;font-size:18px}
    .modal p{margin:8px 0;color:var(--muted);line-height:1.45}
    .modal .row{display:flex;gap:12px;flex-wrap:wrap}
    .modal .col{flex:1;min-width:240px}
    .modal label{display:block;font-size:12px;color:var(--muted);margin:10px 0 4px}
    .modal input[type="range"]{width:100%}
    .modal .btnrow{display:flex;gap:10px;margin-top:12px;flex-wrap:wrap}
    .modal button{border-radius:14px;border:1px solid rgba(255,255,255,.14);background:rgba(255,255,255,.08);color:var(--text);padding:10px 12px;cursor:pointer;font-size:12px}
    .modal button.primary{border-color:rgba(35,198,209,.55);background:rgba(35,198,209,.16)}
    .modal button.danger{border-color:rgba(209,74,74,.55);background:rgba(209,74,74,.16)}
    .badge{display:inline-block;padding:4px 8px;border-radius:999px;border:1px solid rgba(255,255,255,.12);background:rgba(255,255,255,.06);font-size:12px;font-family:var(--mono)}
    .card{border-radius:18px;border:1px solid rgba(255,255,255,.10);background:rgba(255,255,255,.05);padding:12px}
    .card h3{margin:0 0 6px 0;font-size:14px}
    .card .meta{font-family:var(--mono);color:var(--muted);font-size:12px}
    .card .body{margin-top:8px;color:var(--muted);font-size:12px;line-height:1.35}
    hr{border:0;border-top:1px solid rgba(255,255,255,.10);margin:14px 0}
    .touch{position:absolute;inset:0;pointer-events:none}
    .joystick{pointer-events:auto;position:absolute;left:18px;bottom:18px;width:132px;height:132px;border-radius:999px;border:1px solid rgba(255,255,255,.14);background:rgba(255,255,255,.06)}
    .joystick .stick{position:absolute;left:50%;top:50%;width:56px;height:56px;transform:translate(-50%,-50%);border-radius:999px;border:1px solid rgba(35,198,209,.35);background:rgba(35,198,209,.12)}
    .touch-buttons{pointer-events:auto;position:absolute;right:18px;bottom:18px;display:grid;grid-template-columns:repeat(3,56px);gap:10px}
    .tbtn{width:56px;height:56px;border-radius:16px;border:1px solid rgba(255,255,255,.14);background:rgba(255,255,255,.08);color:var(--text);font-family:var(--mono);font-weight:800}
    @media (max-width: 860px){.hud{left:10px;right:10px;top:10px}.panel{min-width:160px}.grid4{grid-template-columns:repeat(2,1fr)}}
  </style>
</head>
<body>
<div id="app">
  <canvas id="game" aria-label="Juego Helix Repair" role="img"></canvas>

  <div id="hud" class="hud" aria-live="polite">
    <div class="hud-row">
      <div class="panel">
        <div class="panel-title">Integridad ADN</div>
        <div class="bar"><div id="dnaBar" class="bar-fill"></div></div>
        <div class="panel-sub"><span id="dnaPct">100%</span></div>
      </div>

      <div class="panel">
        <div class="panel-title">Energía Escáner</div>
        <div class="bar"><div id="scanBar" class="bar-fill"></div></div>
        <div class="panel-sub"><span id="scanPct">100%</span> · CD: <span id="scanCd">0.0s</span></div>
      </div>

      <div class="panel wide">
        <div class="panel-title">Cognición (modelo)</div>
        <div class="grid4">
          <div><div class="k">Atención</div><div id="cogAtt" class="v">100</div></div>
          <div><div class="k">Fatiga</div><div id="cogFat" class="v">0</div></div>
          <div><div class="k">Precisión</div><div id="cogPrec" class="v">100</div></div>
          <div><div class="k">Comprensión</div><div id="cogComp" class="v">0</div></div>
        </div>
        <div class="panel-sub tiny">
          Nota: afecta lectura/ensamble (ruido y tolerancia). No es determinismo genético.
        </div>
      </div>
    </div>

    <div class="hud-row">
      <div class="panel wide">
        <div class="panel-title">Capas + Herramientas</div>
        <div class="chips">
          <button id="layer1" class="chip">1 Estructural</button>
          <button id="layer2" class="chip">2 Química</button>
          <button id="layer3" class="chip">3 Informacional</button>
          <button id="scanBtn" class="chip primary">Q Escaneo (pulso)</button>
          <button id="pinBtn" class="chip">P Pin</button>
        </div>
        <div class="panel-sub">WASD/Flechas · Space salto · E interactuar · Q escanear · 1/2/3 · Esc pausa</div>
      </div>

      <div class="panel">
        <div class="panel-title">Inventario</div>
        <div class="inv">
          <div>A:<span id="invA">0</span></div>
          <div>T:<span id="invT">0</span></div>
          <div>C:<span id="invC">0</span></div>
          <div>G:<span id="invG">0</span></div>
          <div>Azúcar:<span id="invS">0</span></div>
          <div>Fosfato:<span id="invP">0</span></div>
        </div>
      </div>

      <div class="panel">
        <div class="panel-title">Estado</div>
        <div id="activeEvidence" class="evidence-mini">—</div>
        <div class="panel-sub">Niveles: 1→2→3 (Final). Evitá “nucleasas” y “radicales”.</div>
      </div>
    </div>
  </div>

  <div id="overlay" class="overlay hidden" aria-hidden="true">
    <div id="modal" class="modal" role="dialog" aria-modal="true" aria-label="Panel"></div>
  </div>

  <div id="touch" class="touch hidden" aria-hidden="true">
    <div id="joy" class="joystick"><div class="stick"></div></div>
    <div class="touch-buttons">
      <button id="tJump" class="tbtn">⤒</button>
      <button id="tAct" class="tbtn">E</button>
      <button id="tScan" class="tbtn">Q</button>
      <button id="tL1" class="tbtn">1</button>
      <button id="tL2" class="tbtn">2</button>
      <button id="tL3" class="tbtn">3</button>
    </div>
  </div>
</div>

<script>
(() => {
  /* ==========================================================
     HELIX REPAIR (Sprite) — Multi niveles + Enemigos + Final
     - Canvas 2D, offline, sin dependencias
     - Estilo sprite (pixel-art generado por código)
     - Enemigos tipo “peligros biológicos”: nucleasas / radicales
       (se EVITAN; el escáner (Q) puede “aturdir” breve si estás cerca)
     - 3 niveles: Segmento Alfa, Beta, Gamma (Final: crisis de replicación)
     ========================================================== */

  // ---------- config ----------
  const CFG = {
    maxDt: 0.05,
    gravity: 1650,
    tile: 32,          // unidad sprite/pixel
    worldScale: 1,     // (cambiable)
    player: { w: 16, h: 26, accel: 2800, maxSpeed: 330, friction: 0.86, jumpVel: 680, airControl: 0.65 },
    scan: { maxEnergy: 100, regen: 18, cost: 28, cooldown: 1.4, pulseSec: 0.65, pinMax: 1, stunRadius: 90, stunSec: 1.1 },
    cognition: { attMax: 100, fatMax: 100, precMax: 100, compMax: 100, fatPerSec: 0.55, fatPerFail: 6, attPenaltyEvidence: 9, precBase: 100, precFatFactor: 0.55, noiseBase: 0.22 },
    hydro: { baseDmg: 0.55 },
    enemy: { touchDmg: 10, knock: 320, iFrame: 0.9 },
    final: { stabilizeTarget: 75, timerSec: 55 } // final: mantener integridad y estabilizar en tiempo
  };

  // ---------- helpers ----------
  const clamp = (n,a,b)=>Math.max(a,Math.min(b,n));
  const lerp = (a,b,t)=>a+(b-a)*t;
  const rnd = (a,b)=>a+Math.random()*(b-a);
  const overlap = (a,b)=> a.x<b.x+b.w && a.x+a.w>b.x && a.y<b.y+b.h && a.y+a.h>b.y;
  const dist2 = (ax,ay,bx,by)=> (ax-bx)*(ax-bx) + (ay-by)*(ay-by);

  // ---------- DOM ----------
  const canvas = document.getElementById('game');
  const ctx = canvas.getContext('2d',{alpha:false});
  let DPR=1,W=0,H=0;
  function resize(){
    const r=canvas.getBoundingClientRect();
    DPR = Math.max(1, Math.min(3, window.devicePixelRatio||1));
    W = Math.floor(r.width*DPR);
    H = Math.floor(r.height*DPR);
    canvas.width=W; canvas.height=H;
    ctx.imageSmoothingEnabled=false; // sprite look
  }
  window.addEventListener('resize', resize, {passive:true});
  resize();

  const hud = {
    dnaBar: id('dnaBar'), dnaPct: id('dnaPct'),
    scanBar: id('scanBar'), scanPct: id('scanPct'), scanCd: id('scanCd'),
    cogAtt: id('cogAtt'), cogFat: id('cogFat'), cogPrec: id('cogPrec'), cogComp: id('cogComp'),
    invA: id('invA'), invT: id('invT'), invC: id('invC'), invG: id('invG'), invS: id('invS'), invP: id('invP'),
    active: id('activeEvidence'),
    overlay: id('overlay'), modal: id('modal'),
    layer1: id('layer1'), layer2: id('layer2'), layer3: id('layer3'),
    scanBtn: id('scanBtn'), pinBtn: id('pinBtn'),
  };
  function id(x){return document.getElementById(x);}

  function openOverlay(html){
    hud.modal.innerHTML=html;
    hud.overlay.classList.remove('hidden');
    hud.overlay.setAttribute('aria-hidden','false');
  }
  function closeOverlay(){
    hud.overlay.classList.add('hidden');
    hud.overlay.setAttribute('aria-hidden','true');
    hud.modal.innerHTML='';
  }

  // ---------- input ----------
  const keyDown=new Set(), keyPressed=new Set();
  window.addEventListener('keydown',(e)=>{
    if(!keyDown.has(e.key)) keyPressed.add(e.key);
    keyDown.add(e.key);
    if([' ','ArrowUp','ArrowDown','ArrowLeft','ArrowRight'].includes(e.key)) e.preventDefault();
  },{passive:false});
  window.addEventListener('keyup',(e)=>keyDown.delete(e.key),{passive:true});

  const BIND = {
    left:['ArrowLeft','a','A'], right:['ArrowRight','d','D'],
    jump:[' '], interact:['e','E'], scan:['q','Q'],
    l1:['1'], l2:['2'], l3:['3'], pause:['Escape'], pin:['p','P']
  };
  const any=(arr,set)=>arr.some(k=>set.has(k));
  const isDown=(a)=>any(BIND[a],keyDown);
  const wasPressed=(a)=>any(BIND[a],keyPressed);

  // touch
  const isTouch = ('ontouchstart' in window) || (navigator.maxTouchPoints||0)>0;
  const touchRoot=id('touch');
  const joyEl=id('joy'); const stickEl=joyEl.querySelector('.stick');
  const tJump=id('tJump'), tAct=id('tAct'), tScan=id('tScan'), tL1=id('tL1'), tL2=id('tL2'), tL3=id('tL3');
  touchRoot.classList.toggle('hidden', !isTouch);
  touchRoot.setAttribute('aria-hidden', (!isTouch).toString());
  const tBtn={jump:false,act:false,scan:false,l1:false,l2:false,l3:false};
  function bindBtn(el,k){
    const set=v=>tBtn[k]=v;
    el.addEventListener('touchstart',(e)=>{e.preventDefault();set(true)},{passive:false});
    el.addEventListener('touchend',()=>set(false),{passive:true});
    el.addEventListener('touchcancel',()=>set(false),{passive:true});
    el.addEventListener('mousedown',(e)=>{e.preventDefault();set(true)},{passive:false});
    window.addEventListener('mouseup',()=>set(false),{passive:true});
  }
  bindBtn(tJump,'jump'); bindBtn(tAct,'act'); bindBtn(tScan,'scan');
  bindBtn(tL1,'l1'); bindBtn(tL2,'l2'); bindBtn(tL3,'l3');

  const joy={active:false,id:null,cx:0,cy:0,dx:0,dy:0};
  const moveStick=(px,py)=>{ stickEl.style.transform=`translate(calc(-50% + ${px}px), calc(-50% + ${py}px))`; };
  const startJoy=(id)=>{
    const r=joyEl.getBoundingClientRect();
    joy.active=true; joy.id=id;
    joy.cx=r.left+r.width/2; joy.cy=r.top+r.height/2;
    joy.dx=0; joy.dy=0; moveStick(0,0);
  };
  const updJoy=(x,y)=>{
    if(!joy.active) return;
    const max=44;
    joy.dx=clamp((x-joy.cx)/max,-1,1);
    joy.dy=clamp((y-joy.cy)/max,-1,1);
    moveStick(joy.dx*max, joy.dy*max);
  };
  const endJoy=()=>{joy.active=false;joy.id=null;joy.dx=0;joy.dy=0;moveStick(0,0);};

  joyEl.addEventListener('touchstart',(e)=>{e.preventDefault(); const t=e.changedTouches[0]; startJoy(t.identifier); updJoy(t.clientX,t.clientY);},{passive:false});
  joyEl.addEventListener('touchmove',(e)=>{e.preventDefault(); const t=[...e.touches].find(tt=>tt.identifier===joy.id)||e.touches[0]; if(t) updJoy(t.clientX,t.clientY);},{passive:false});
  joyEl.addEventListener('touchend',(e)=>{e.preventDefault(); endJoy();},{passive:false});
  joyEl.addEventListener('touchcancel',(e)=>{e.preventDefault(); endJoy();},{passive:false});
  joyEl.addEventListener('mousedown',(e)=>{
    e.preventDefault(); startJoy('mouse'); updJoy(e.clientX,e.clientY);
    const mm=(ev)=>updJoy(ev.clientX,ev.clientY);
    const mu=()=>{window.removeEventListener('mousemove',mm);window.removeEventListener('mouseup',mu);endJoy();};
    window.addEventListener('mousemove',mm,{passive:true});
    window.addEventListener('mouseup',mu,{passive:true});
  },{passive:false});

  // ---------- sprite atlas (procedural pixel-art) ----------
  const Atlas = (() => {
    const s = 16; // tile sprite size base
    const cvs = document.createElement('canvas');
    cvs.width = 256; cvs.height = 128;
    const g = cvs.getContext('2d');
    g.imageSmoothingEnabled = false;

    function px(x,y,c){ g.fillStyle=c; g.fillRect(x,y,1,1); }
    function rect(x,y,w,h,c){ g.fillStyle=c; g.fillRect(x,y,w,h); }
    function outline(x,y,w,h,c){
      g.fillStyle=c;
      g.fillRect(x,y,w,1); g.fillRect(x,y+h-1,w,1);
      g.fillRect(x,y,1,h); g.fillRect(x+w-1,y,1,h);
    }

    // --- player frames (idle/walk 4) ---
    // style: nano-suit + visor
    function drawPlayer(x,y,step){
      rect(x,y,16,16,'rgba(0,0,0,0)');
      // body
      rect(x+5,y+4,6,9,'#e8eef7');
      outline(x+5,y+4,6,9,'#23c6d1');
      // visor
      rect(x+6,y+6,4,2,'#23c6d1');
      // legs
      const l = step%2===0 ? 1 : 0;
      rect(x+5,y+13,2,2,'#b7c2d3');
      rect(x+9,y+13,2,2,'#b7c2d3');
      // arm tool
      rect(x+11,y+9,2,1,'#d1a223');
      if(l){ rect(x+4,y+12,2,2,'#b7c2d3'); } else { rect(x+10,y+12,2,2,'#b7c2d3'); }
    }
    for(let i=0;i<4;i++) drawPlayer(0+i*16,0,i);

    // --- enemy: nuclease (patroller) ---
    function drawNuclease(x,y){
      rect(x,y,16,16,'rgba(0,0,0,0)');
      rect(x+3,y+5,10,7,'#d14a4a');
      outline(x+3,y+5,10,7,'#ffd2d2');
      rect(x+5,y+7,2,2,'#0b0f14');
      rect(x+9,y+7,2,2,'#0b0f14');
      rect(x+2,y+8,2,1,'#ffd2d2');
      rect(x+12,y+8,2,1,'#ffd2d2');
    }
    drawNuclease(0,16);

    // --- enemy: radical (floater) ---
    function drawRadical(x,y){
      rect(x,y,16,16,'rgba(0,0,0,0)');
      rect(x+6,y+6,4,4,'#d1a223');
      outline(x+6,y+6,4,4,'#fff2c2');
      rect(x+7,y+4,2,2,'#fff2c2');
      rect(x+7,y+10,2,2,'#fff2c2');
      rect(x+4,y+7,2,2,'#fff2c2');
      rect(x+10,y+7,2,2,'#fff2c2');
    }
    drawRadical(16,16);

    // --- collectibles ---
    function drawOrb(x,y,c1,c2){
      rect(x,y,16,16,'rgba(0,0,0,0)');
      rect(x+6,y+6,4,4,c1);
      outline(x+6,y+6,4,4,c2);
    }
    drawOrb(32,16,'#23c6d1','#bdf8ff'); // evidence
    drawOrb(48,16,'#d1a223','#fff2c2'); // component

    // --- station ---
    function drawStation(x,y){
      rect(x,y,16,16,'rgba(0,0,0,0)');
      rect(x+2,y+2,12,12,'#e8eef7');
      outline(x+2,y+2,12,12,'#23c6d1');
      rect(x+5,y+5,6,6,'rgba(35,198,209,.25)');
      outline(x+5,y+5,6,6,'#23c6d1');
    }
    drawStation(64,16);

    // --- exit ---
    function drawExit(x,y){
      rect(x,y,16,16,'rgba(0,0,0,0)');
      rect(x+3,y+2,10,12,'rgba(35,198,209,.18)');
      outline(x+3,y+2,10,12,'#23c6d1');
      rect(x+6,y+5,4,6,'rgba(35,198,209,.25)');
    }
    drawExit(80,16);

    return {
      img: cvs,
      // uvs
      player: (i)=>({sx:i*16, sy:0, sw:16, sh:16}),
      nuclease: {sx:0,sy:16,sw:16,sh:16},
      radical:  {sx:16,sy:16,sw:16,sh:16},
      evidence: {sx:32,sy:16,sw:16,sh:16},
      comp:     {sx:48,sy:16,sw:16,sh:16},
      station:  {sx:64,sy:16,sw:16,sh:16},
      exit:     {sx:80,sy:16,sw:16,sh:16},
    };
  })();

  // ---------- levels generator ----------
  // 3 niveles con plataformas helicoidales (topología tipo ADN), hidro-zonas, estaciones, enemigos y salida.
  function makeLevel(name, seed, difficulty){
    const rand = (() => {
      let t = seed>>>0;
      return ()=>{ t = (t*1664525 + 1013904223)>>>0; return (t/4294967296); };
    })();

    const bounds = {x:0,y:0,w:4800 + difficulty*600,h:1500};
    const spawn = {x:240, y:980};
    const platforms = [];
    const triggers = [];
    const collectibles = [];
    const hydroZones = [];
    const enemies = [];
    const stations = [];

    // base floor/walls
    platforms.push({id:'wallL',x:0,y:0,w:24,h:bounds.h,enabled:true});
    platforms.push({id:'wallR',x:bounds.w-24,y:0,w:24,h:bounds.h,enabled:true});
    platforms.push({id:'floor',x:0,y:1400,w:bounds.w,h:220,enabled:true});

    // helix-like lanes platforms
    let x = 120;
    let yBase = 1060;
    for(let i=0;i<10 + difficulty*2;i++){
      const w = 520 + Math.floor(rand()*260);
      const y = yBase + Math.floor(Math.sin((x*0.004) + i*0.35)*90) - Math.floor(rand()*30);
      platforms.push({id:`p${i}`,x,y,w,h:24,enabled:true});
      // occasional gaps/bridges
      if(i===4 || i===7){
        platforms.push({id:`bridge_${i}`,x:x+w+120,y:y-40,w:300,h:24,enabled: (i===4?false:true)});
      }
      x += w + 220;
      if(x > bounds.w - 800) break;
    }

    // hydro zones (increasing complexity)
    const hzCount = 2 + difficulty;
    for(let i=0;i<hzCount;i++){
      const zx = 700 + i* (900 + difficulty*120) + Math.floor(rand()*140);
      const zy = 780 + Math.floor(rand()*140);
      const zw = 520 + Math.floor(rand()*240);
      const zh = 320 + Math.floor(rand()*140);
      const ph = (6.0 + rand()*4.0) + (i===hzCount-1 ? (difficulty*0.35) : 0);
      const temperature = Math.floor(305 + rand()*35) + difficulty*3;
      const stress = Math.floor(25 + rand()*55) + difficulty*6;
      const humidityIndex = Math.floor(55 + rand()*35);
      const catalyst = rand() > (0.72 - difficulty*0.12);
      const baseDamagePerSec = (0.55 + i*0.2) + difficulty*0.12;
      hydroZones.push({id:`hz_${i}`,x:zx,y:zy,w:zw,h:zh,ph,temperature,catalyst,stress,humidityIndex,baseDamagePerSec});
    }

    // stations
    const st1 = {id:'st_01', name:`Estación — ${name} A`, x: 1550 + difficulty*150, y: 820, w: 120, h:120, default:{ph:7.2,temp:310,catalyst:true,stress:22}, unlockPlatformId:'bridge_4'};
    const st2 = {id:'st_02', name:`Estación — ${name} B`, x: bounds.w - 780, y: 820, w: 120, h:120, default:{ph:7.0,temp:312,catalyst:true,stress:30}, unlockPlatformId:null};
    stations.push(st1, st2);
    triggers.push({id:'tr_st1',type:'station',stationId:'st_01',x:st1.x,y:st1.y,w:st1.w,h:st1.h,fired:false});
    triggers.push({id:'tr_st2',type:'station',stationId:'st_02',x:st2.x,y:st2.y,w:st2.w,h:st2.h,fired:false});

    // tutorial only in level 1
    if(difficulty===0){
      triggers.push({id:'tr_tut',type:'tutorial',x:150,y:960,w:120,h:120,fired:false});
    }

    // exit
    const exit = {id:'exit', x: bounds.w-260, y: 860, w: 140, h: 200};
    triggers.push({id:'tr_exit',type:'exit',x:exit.x,y:exit.y,w:exit.w,h:exit.h,fired:false});

    // collectibles: components + evidence
    const comps = ['A','T','C','G','S','P'];
    for(let i=0;i<10 + difficulty*3;i++){
      const item = comps[Math.floor(rand()*comps.length)];
      collectibles.push({id:`c_${i}`,kind:'component',item,amount:1,x:400+i* (260+Math.floor(rand()*120)),y: (980 - (i%3)*70) - Math.floor(rand()*40),w:16,h:16,collected:false});
    }

    const evidCount = 10; // per run we can keep global; but per level add some
    for(let i=0;i<Math.min(5, 3+difficulty);i++){
      const ex = 780 + i*(800 + difficulty*60);
      const ev = makeEvidenceCard(name, difficulty, i);
      collectibles.push({id:`e_${difficulty}_${i}`,kind:'evidence',evidence:ev,x:ex,y:920 - i*20,w:16,h:16,collected:false});
    }

    // enemies
    // nucleases patrol on some platforms
    for(let i=0;i<4 + difficulty*2;i++){
      const px = 1000 + i*(760 + difficulty*80);
      const py = 980 - (i%3)*70;
      enemies.push({
        id:`n_${i}`, type:'nuclease',
        x:px, y:py, w:16, h:16,
        vx: (rand()>0.5?1:-1) * (70 + difficulty*18),
        minX:px-140, maxX:px+180,
        stunned:0
      });
    }
    // radicals float in hydro zones
    for(let i=0;i<2 + difficulty;i++){
      const z = hydroZones[Math.floor(rand()*hydroZones.length)];
      enemies.push({
        id:`r_${i}`, type:'radical',
        x: z.x + 80 + Math.floor(rand()*(z.w-160)),
        y: z.y + 80 + Math.floor(rand()*(z.h-160)),
        w:16, h:16,
        t: rand()*10,
        stunned:0
      });
    }

    return { name, bounds, spawn, platforms, triggers, collectibles, hydroZones, stations, exit, enemies };
  }

  function makeEvidenceCard(levelName, difficulty, idx){
    const pool = [
      ["EV-H2O","Hidrólisis: ruptura por agua","Microfracturas aumentan con humedad y pH desviado.","La protonación altera estabilidad y acelera ruptura.","Aproximar pH a neutro reduce la tasa de daño."],
      ["EV-PH","pH y enlaces","La estabilidad cae al alejarse de pH ~7.2.","Cambios de carga afectan enlaces y reactividad local.","pH controlado mejora ensamblaje y baja daño."],
      ["EV-T","Temperatura y cinética","Temperatura alta acelera degradación.","Mayor energía térmica eleva frecuencia de eventos de ruptura.","Cercano a 310K estabiliza sin frenar demasiado."],
      ["EV-STR","Estrés mecánico","Zonas curvas muestran fallas recurrentes.","Tensión incrementa probabilidad de ruptura del backbone.","Reducir estrés antes de reparar aumenta éxito."],
      ["EV-INFO","Información: coherencia","Pares incorrectos degradan integridad informacional.","Errores de emparejamiento crean inestabilidad funcional.","Capa informacional previene errores sistemáticos."],
      ["EV-COG","Cognición: carga","Múltiples estímulos elevan ruido percibido.","Carga cognitiva reduce precisión de lectura.","Administrar foco mejora resultados del escáner."]
    ];
    const base = pool[(idx + difficulty) % pool.length];
    const bonusMap = [
      ["SCAN_EFF","+energía escáner"],
      ["STABILITY","+integridad"],
      ["NOISE","−ruido (vía comprensión)"],
      ["MEM","+memoria de trabajo"]
    ];
    const b = bonusMap[(idx*3 + difficulty) % bonusMap.length];
    return {
      id: `${base[0]}-${levelName}-${idx}`,
      title: base[1],
      observation: base[2],
      hypothesis: base[3],
      conclusion: base[4],
      bonusCode: b[0],
      bonus: b[1]
    };
  }

  // ---------- game state ----------
  const LEVELS = [
    makeLevel("Segmento Alfa",  1337, 0),
    makeLevel("Segmento Beta",  7331, 1),
    makeLevel("Segmento Gamma", 4242, 2) // final
  ];

  let scene = 'menu'; // menu | play | pause | report | ending
  let levelIndex = 0;
  let L = null;

  const player = { x:0,y:0,vx:0,vy:0,w:CFG.player.w,h:CFG.player.h,onGround:false,facing:1,animT:0,iframe:0 };
  const cam = { x:0,y:0,lookAhead:110 };

  const scan = { energy:CFG.scan.maxEnergy, cd:0, layer:1, pulse:0, pins:[], lastAt:-999 };
  const cog = { att:100, fat:0, prec:100, comp:0, activeEvidenceCount:0, fails:0, time:0 };
  const inv = {A:0,T:0,C:0,G:0,S:0,P:0};
  let dnaIntegrity = 100;
  let statusText = "—";
  let evidenceLog = [];
  let evidenceActiveCount = 0;

  // final
  let finalTimer = CFG.final.timerSec;
  let finalDone = false;

  function loadLevel(i){
    levelIndex = i;
    L = LEVELS[levelIndex];

    // reset player/instruments
    player.x=L.spawn.x; player.y=L.spawn.y; player.vx=0; player.vy=0; player.onGround=false; player.facing=1; player.animT=0; player.iframe=0;
    cam.x=player.x; cam.y=player.y;

    scan.energy=CFG.scan.maxEnergy; scan.cd=0; scan.layer=1; scan.pulse=0; scan.pins=[]; scan.lastAt=-999;
    cog.att=100; cog.fat=0; cog.prec=100; cog.comp=0; cog.activeEvidenceCount=0; cog.fails=0; cog.time=0;

    // keep inventory across levels (progresión) pero suavizamos
    // (si querés hard reset, descomentá)
    // inv.A=inv.T=inv.C=inv.G=inv.S=inv.P=0;

    dnaIntegrity = clamp(dnaIntegrity, 60, 100); // carry but keep viable
    statusText = `${L.name} cargado.`;

    // final state
    if(levelIndex===2){ finalTimer = CFG.final.timerSec; finalDone=false; }

    // clear triggers/collectibles flags
    for(const t of L.triggers) t.fired=false;
    for(const c of L.collectibles) c.collected=false;
  }

  // ---------- ui hooks ----------
  hud.layer1.onclick=()=>setLayer(1);
  hud.layer2.onclick=()=>setLayer(2);
  hud.layer3.onclick=()=>setLayer(3);
  hud.scanBtn.onclick=()=>tryScan();
  hud.pinBtn.onclick=()=>togglePin();

  function setLayer(n){
    scan.layer = clamp(n,1,3);
    hud.layer1.classList.toggle('active', scan.layer===1);
    hud.layer2.classList.toggle('active', scan.layer===2);
    hud.layer3.classList.toggle('active', scan.layer===3);
  }
  setLayer(1);

  function updateHUD(){
    const p = clamp(dnaIntegrity,0,100);
    hud.dnaBar.style.width = p+'%';
    hud.dnaPct.textContent = p.toFixed(0)+'%';
    hud.dnaBar.style.background = p<35
      ? 'linear-gradient(90deg, rgba(209,74,74,1), rgba(209,74,74,.25))'
      : (p<65
        ? 'linear-gradient(90deg, rgba(209,162,35,1), rgba(209,162,35,.25))'
        : 'linear-gradient(90deg, rgba(35,198,209,1), rgba(35,198,209,.35))');

    const sp = clamp(scan.energy/CFG.scan.maxEnergy*100,0,100);
    hud.scanBar.style.width = sp+'%';
    hud.scanPct.textContent = sp.toFixed(0)+'%';
    hud.scanCd.textContent = scan.cd.toFixed(1)+'s';

    hud.cogAtt.textContent = cog.att.toFixed(0);
    hud.cogFat.textContent = cog.fat.toFixed(0);
    hud.cogPrec.textContent = cog.prec.toFixed(0);
    hud.cogComp.textContent = cog.comp.toFixed(0);

    hud.invA.textContent=inv.A; hud.invT.textContent=inv.T; hud.invC.textContent=inv.C;
    hud.invG.textContent=inv.G; hud.invS.textContent=inv.S; hud.invP.textContent=inv.P;

    hud.active.textContent = statusText;
  }

  // ---------- cognition / scan ----------
  function scanNoise(){
    const invAtt = 1 - (cog.att / CFG.cognition.attMax);
    const f = cog.fat / CFG.cognition.fatMax;
    return clamp(CFG.cognition.noiseBase + invAtt*0.38 + f*0.32, 0, 0.7);
  }
  function stepCognition(dt){
    cog.time += dt;
    cog.fat = clamp(cog.fat + CFG.cognition.fatPerSec*dt, 0, CFG.cognition.fatMax);
    const attPenalty = cog.activeEvidenceCount * CFG.cognition.attPenaltyEvidence;
    cog.att = clamp(CFG.cognition.attMax - attPenalty - cog.fat*0.25, 10, CFG.cognition.attMax);
    const prec = CFG.cognition.precBase - cog.fat*CFG.cognition.precFatFactor + cog.comp*0.18;
    cog.prec = clamp(prec, 35, CFG.cognition.precMax);
  }
  function onFail(){
    cog.fails++;
    cog.fat = clamp(cog.fat + CFG.cognition.fatPerFail, 0, CFG.cognition.fatMax);
  }
  function onSolve(){
    cog.comp = clamp(cog.comp + 8, 0, CFG.cognition.compMax);
    cog.fat = clamp(cog.fat - 4, 0, CFG.cognition.fatMax);
  }
  function stepScan(dt){
    scan.energy = clamp(scan.energy + CFG.scan.regen*dt, 0, CFG.scan.maxEnergy);
    scan.cd = Math.max(0, scan.cd - dt);
    scan.pulse = Math.max(0, scan.pulse - dt);
  }
  function tryScan(){
    if(scene!=='play') return false;
    if(scan.cd>0 || scan.energy<CFG.scan.cost) return false;
    scan.energy -= CFG.scan.cost;
    scan.cd = CFG.scan.cooldown;
    scan.pulse = CFG.scan.pulseSec;
    scan.lastAt = tNow;
    // stun enemies within radius
    const cx = player.x+player.w/2, cy = player.y+player.h/2;
    const r2 = CFG.scan.stunRadius*CFG.scan.stunRadius;
    for(const e of L.enemies){
      if(e.stunned>0) continue;
      const ex=e.x+e.w/2, ey=e.y+e.h/2;
      if(dist2(cx,cy,ex,ey) <= r2){
        e.stunned = CFG.scan.stunSec;
      }
    }
    statusText = `Pulso aplicado (capa ${scan.layer}).`;
    return true;
  }
  function togglePin(){
    if(scene!=='play') return;
    const cx = player.x+player.w/2, cy = player.y+player.h/2;
    if(scan.pins.length>=CFG.scan.pinMax){ scan.pins=[]; return; }
    scan.pins=[{x:cx,y:cy-64}];
  }
  function scanActive(){ return scan.pulse>0.01; }

  // ---------- level mechanics ----------
  function playerAABB(){ return {x:player.x,y:player.y,w:player.w,h:player.h}; }

  function resolvePlatforms(m){
    let onGround=false;
    // X
    m.x += m.vx;
    for(const s of L.platforms){
      if(s.enabled===false) continue;
      if(overlap(m,s)){
        if(m.vx>0) m.x = s.x - m.w;
        else if(m.vx<0) m.x = s.x + s.w;
        m.vx=0;
      }
    }
    // Y
    m.y += m.vy;
    for(const s of L.platforms){
      if(s.enabled===false) continue;
      if(overlap(m,s)){
        if(m.vy>0){ m.y = s.y - m.h; onGround=true; }
        else if(m.vy<0){ m.y = s.y + s.h; }
        m.vy=0;
      }
    }
    return onGround;
  }

  function stepPlayer(dt){
    const axJoy = isTouch ? joy.dx : 0;
    const left = isDown('left') || axJoy<-0.3;
    const right= isDown('right')|| axJoy> 0.3;

    const jumpPressed = wasPressed('jump') || (isTouch && tBtn.jump);
    const interactPressed = wasPressed('interact') || (isTouch && tBtn.act);
    const scanPressed = wasPressed('scan') || (isTouch && tBtn.scan);

    if(wasPressed('l1') || (isTouch && tBtn.l1)) setLayer(1);
    if(wasPressed('l2') || (isTouch && tBtn.l2)) setLayer(2);
    if(wasPressed('l3') || (isTouch && tBtn.l3)) setLayer(3);
    if(scanPressed) tryScan();
    if(wasPressed('pin')) togglePin();

    let ax=0;
    if(left) ax -= CFG.player.accel;
    if(right) ax += CFG.player.accel;

    const control = player.onGround ? 1.0 : CFG.player.airControl;
    player.vx += ax*dt*control;

    if(player.onGround && !left && !right){
      player.vx *= Math.pow(CFG.player.friction, dt*60);
    }
    player.vx = clamp(player.vx, -CFG.player.maxSpeed, CFG.player.maxSpeed);

    player.vy += CFG.gravity*dt;

    if(jumpPressed && player.onGround){
      player.vy = -CFG.player.jumpVel;
      player.onGround=false;
    }

    const mover = {x:player.x,y:player.y,w:player.w,h:player.h,vx:player.vx*dt,vy:player.vy*dt};
    const onG = resolvePlatforms(mover);
    player.x=mover.x; player.y=mover.y; player.onGround=onG;
    if(onG && player.vy>0) player.vy=0;

    if(Math.abs(player.vx)>10) player.facing = player.vx>0?1:-1;
    player.animT += dt * (Math.abs(player.vx)>30? 10 : 6);

    if(interactPressed){
      for(const tr of L.triggers){
        if(tr.type==='station' && overlap(playerAABB(), tr)){
          openStation(tr.stationId);
          return;
        }
      }
    }
  }

  function stepEnemies(dt){
    const pa = playerAABB();
    if(player.iframe>0) player.iframe = Math.max(0, player.iframe-dt);

    for(const e of L.enemies){
      if(e.stunned>0) e.stunned = Math.max(0, e.stunned-dt);

      if(e.type==='nuclease'){
        if(e.stunned<=0){
          e.x += e.vx*dt;
          if(e.x < e.minX){ e.x=e.minX; e.vx=Math.abs(e.vx); }
          if(e.x > e.maxX){ e.x=e.maxX; e.vx=-Math.abs(e.vx); }
        }
      } else if(e.type==='radical'){
        e.t += dt;
        if(e.stunned<=0){
          e.y += Math.sin(e.t*2.1)*22*dt;
          e.x += Math.cos(e.t*1.6)*16*dt;
        }
      }

      // collision with player: hazard contact (no "combate", es peligro)
      if(player.iframe<=0 && overlap(pa, e)){
        player.iframe = CFG.enemy.iFrame;
        dnaIntegrity = clamp(dnaIntegrity - CFG.enemy.touchDmg, 0, 100);
        onFail();
        // knockback
        player.vx = (player.x < e.x ? -1 : 1) * CFG.enemy.knock;
        player.vy = -420;
        statusText = e.type==='nuclease' ? "Contacto: nuclease (evitar / aturdir con Q)." : "Contacto: radical libre (evitar / aturdir con Q).";
      }
    }
  }

  function stepCollectibles(){
    const pa = playerAABB();
    for(const it of L.collectibles){
      if(it.collected) continue;
      if(overlap(pa, it)){
        it.collected=true;
        if(it.kind==='component'){
          inv[it.item] = (inv[it.item]||0) + (it.amount||1);
          statusText = `Recolectado: ${it.item}`;
        } else if(it.kind==='evidence'){
          evidenceLog.push(it.evidence);
          statusText = `Evidencia: ${it.evidence.title} (${it.evidence.bonus})`;
          applyEvidenceBonus(it.evidence.bonusCode);
        }
      }
    }
  }

  function applyEvidenceBonus(code){
    if(code==='SCAN_EFF'){
      scan.energy = Math.min(CFG.scan.maxEnergy, scan.energy + 14);
    } else if(code==='STABILITY'){
      dnaIntegrity = clamp(dnaIntegrity + 10, 0, 100);
    } else if(code==='NOISE'){
      cog.comp = clamp(cog.comp + 7, 0, 100);
    } else if(code==='MEM'){
      evidenceActiveCount = Math.max(0, evidenceActiveCount - 1);
    }
  }

  function stepTriggers(){
    const pa = playerAABB();
    for(const tr of L.triggers){
      if(tr.fired) continue;
      if(!overlap(pa,tr)) continue;

      if(tr.type==='tutorial'){
        tr.fired=true;
        showTutorial();
      }
      if(tr.type==='exit'){
        // condición: integridad o evidencia y (en final) objetivo
        if(levelIndex<2){
          if(dnaIntegrity>=55 || evidenceLog.length>=4 + levelIndex){
            tr.fired=true;
            statusText = "Salida habilitada. Avanzando…";
            nextLevel();
          } else {
            statusText = "Salida bloqueada: estabilizá el tramo o recolectá evidencia.";
          }
        } else {
          // final: requiere estabilizar y tiempo
          if(finalDone){
            tr.fired=true;
            showEnding();
          } else {
            statusText = "Final: estabilizá (≥75%) y completá protocolo en tiempo.";
          }
        }
      }
    }
  }

  // hydrolysis simulation (zone-based)
  function stepHydrolysis(dt){
    let dmg = CFG.hydro.baseDmg;
    const cx = player.x+player.w/2, cy=player.y+player.h/2;

    for(const z of L.hydroZones){
      if(cx>z.x && cx<z.x+z.w && cy>z.y && cy<z.y+z.h){
        const phTerm = Math.min(1, Math.abs(z.ph-7.2)/3.0);
        const tTerm  = Math.min(1, Math.abs(z.temperature-310)/40);
        const catTerm = z.catalyst ? 0.75 : 1.0;
        const stressTerm = clamp(z.stress/100,0,1);
        const humid = clamp(z.humidityIndex/100,0,1);
        const zone = (0.9*phTerm + 0.9*tTerm + 0.7*stressTerm + 0.6*humid) * 0.9 * catTerm;
        dmg += z.baseDamagePerSec + zone*1.5;
      }
    }

    // scan recently reduces effective damage slightly
    if((tNow - scan.lastAt) < 1.0) dmg *= 0.9;

    // final: timer pressure + slightly higher damage
    if(levelIndex===2){
      dmg *= 1.12;
      finalTimer = Math.max(0, finalTimer - dt);
      if(finalTimer<=0 && !finalDone){
        // fail: reset within level, but keep evidence
        onFail();
        dnaIntegrity = 40;
        player.x=L.spawn.x; player.y=L.spawn.y; player.vx=0; player.vy=0;
        finalTimer = CFG.final.timerSec;
        statusText = "Protocolo fallido: reinicio de emergencia del tramo final.";
      }
      if(dnaIntegrity >= CFG.final.stabilizeTarget && finalTimer > 0){
        // require “hold” stability for some seconds: use comprehension as proxy
        if(cog.comp >= 20 && evidenceLog.length >= 8){
          finalDone = true;
          statusText = "Protocolo final listo: dirigite a la salida.";
        }
      }
    }

    dnaIntegrity = clamp(dnaIntegrity - dmg*dt, 0, 100);
    if(dnaIntegrity<=0){
      onFail();
      dnaIntegrity = 45;
      player.x=L.spawn.x; player.y=L.spawn.y; player.vx=0; player.vy=0;
      statusText = "Integridad colapsó: reinicio de seguridad.";
    }
  }

  // camera follow
  function stepCamera(dt){
    const b=L.bounds;
    const tx = player.x + player.facing*cam.lookAhead;
    const ty = player.y - 50;
    const t = clamp(dt*6.2,0,1);
    cam.x = lerp(cam.x, tx, t);
    cam.y = lerp(cam.y, ty, t);
    const padX=520,padY=320;
    cam.x = clamp(cam.x, b.x+padX, b.x+b.w-padX);
    cam.y = clamp(cam.y, b.y+padY, b.y+b.h-padY);
  }

  // ---------- stations / repair UI ----------
  function openStation(stationId){
    const st = L.stations.find(s=>s.id===stationId);
    if(!st) return;

    const html = `
      <h2>Estación de reparación — ${st.name}</h2>
      <p>Ensamblaje realista simplificado: seleccioná par (A-T / C-G) y backbone (S+P). Ajustá pH/T/catalizador/estrés. Resultado depende de precisión (fatiga/atención).</p>

      <div class="row">
        <div class="col card">
          <h3>Inventario</h3>
          <div class="body" style="font-family:var(--mono)">
            A:${inv.A} · T:${inv.T} · C:${inv.C} · G:${inv.G}<br/>
            S:${inv.S} · P:${inv.P}
          </div>
          <hr/>
          <div class="body">
            <div>Precisión: <span class="badge">${cog.prec.toFixed(0)}</span></div>
            <div>Ruido escáner: <span class="badge">${(scanNoise()*100).toFixed(0)}%</span></div>
          </div>
        </div>

        <div class="col card">
          <h3>Ensamblaje</h3>
          <label>Par de bases</label>
          <select id="pair" style="width:100%;padding:10px;border-radius:12px;background:rgba(255,255,255,.06);border:1px solid rgba(255,255,255,.14);color:var(--text)">
            <option value="AT">A-T</option>
            <option value="CG">C-G</option>
          </select>

          <label><input id="backbone" type="checkbox" checked> Reconstruir backbone (S + P)</label>
          <hr/>
          <h3>Condiciones</h3>
          <label>pH (≈7.2)</label>
          <input id="ph" type="range" min="4.0" max="10.0" step="0.1" value="${st.default.ph}">
          <div class="meta">pH: <span id="phv">${st.default.ph.toFixed(1)}</span></div>

          <label>Temperatura (K) (≈310)</label>
          <input id="temp" type="range" min="280" max="340" step="1" value="${st.default.temp}">
          <div class="meta">Temp: <span id="tv">${st.default.temp}</span> K</div>

          <label><input id="cat" type="checkbox" ${st.default.catalyst?'checked':''}> Catalizador/enzima asistida</label>

          <label>Estrés (0–100)</label>
          <input id="stress" type="range" min="0" max="100" step="1" value="${st.default.stress}">
          <div class="meta">Estrés: <span id="sv">${st.default.stress}</span></div>

          <div class="btnrow">
            <button id="repair" class="primary">Aplicar reparación</button>
            <button id="close">Cerrar</button>
          </div>
          <div id="result" class="meta" style="margin-top:8px"></div>
        </div>
      </div>
    `;
    openOverlay(html);

    const $=(x)=>document.getElementById(x);
    const sync=()=>{$('phv').textContent=parseFloat($('ph').value).toFixed(1); $('tv').textContent=$('temp').value; $('sv').textContent=$('stress').value;};
    ['ph','temp','stress'].forEach(k=>$(k).addEventListener('input',sync,{passive:true}));
    sync();

    $('close').onclick=()=>{ closeOverlay(); };
    $('repair').onclick=()=>{
      const pair = $('pair').value;
      const backbone = $('backbone').checked;
      const ph = parseFloat($('ph').value);
      const temp = parseFloat($('temp').value);
      const catalyst = $('cat').checked;
      const stress = parseInt($('stress').value,10);

      // consume requirements
      const need = {A:0,T:0,C:0,G:0,S:0,P:0};
      if(pair==='AT'){need.A=1;need.T=1;} else {need.C=1;need.G=1;}
      if(backbone){need.S=1;need.P=1;}
      for(const k in need){
        if(need[k]>0 && inv[k]<need[k]){
          $('result').innerHTML = `<span class="badge" style="border-color:rgba(209,74,74,.55);background:rgba(209,74,74,.14)">FALTA</span> componente ${k}`;
          onFail(); dnaIntegrity = clamp(dnaIntegrity-4,0,100);
          return;
        }
      }

      // score
      const phScore = 1 - Math.min(1, Math.abs(ph-7.2)/2.8);
      const tScore  = 1 - Math.min(1, Math.abs(temp-310)/35);
      const catScore = catalyst ? 1 : 0.55;
      const stressScore = 1 - Math.min(1, stress/100);
      let score = (phScore*0.35 + tScore*0.35 + catScore*0.15 + stressScore*0.15);
      score *= (0.78 + 0.22*(cog.prec/100));
      if(temp<295) score*=0.92;
      // trade-off: demasiado conservador (muy baja T y muy bajo estrés) => ensamble más lento (modelado como score penalizado)
      if(temp<300 && stress<15) score*=0.93;

      // consume
      for(const k in need){ if(need[k]>0) inv[k]-=need[k]; }

      if(score>=0.72){
        onSolve();
        const gain = 16 + (cog.prec/100)*6 + (backbone?6:0);
        dnaIntegrity = clamp(dnaIntegrity + gain, 0, 100);

        // unlock bridge if any
        if(st.unlockPlatformId){
          const br = L.platforms.find(p=>p.id===st.unlockPlatformId);
          if(br) br.enabled = true;
        }

        $('result').innerHTML = `Resultado: <span class="badge">OK</span> Score ${score.toFixed(2)} · Integridad +${gain.toFixed(0)}%`;
        statusText = "Reparación exitosa: conectividad restaurada.";
      } else {
        onFail();
        dnaIntegrity = clamp(dnaIntegrity - 8, 0, 100);
        $('result').innerHTML = `Resultado: <span class="badge" style="border-color:rgba(209,74,74,.55);background:rgba(209,74,74,.14)">FALLO</span> Score ${score.toFixed(2)} · Ajustá condiciones/par/backbone`;
        statusText = "Reparación inestable: reintentar con mejor diagnóstico.";
      }
    };
  }

  // ---------- scenes ----------
  function showMenu(){
    scene='menu';
    closeOverlay();
    statusText="—";
    openOverlay(`
      <h2>HELIX REPAIR — Multi niveles</h2>
      <p>Juego 2D estilo sprite dentro de una doble hélice. Evitá peligros (nucleasas/radicales), usá escaneo (Q) para diagnosticar y <b>aturdir</b> cerca, y repará estaciones para estabilizar.</p>
      <div class="row">
        <div class="col card">
          <h3>Objetivo</h3>
          <div class="body">
            <ul>
              <li>Nivel 1: Segmento Alfa (tutorial + 2 peligros).</li>
              <li>Nivel 2: Segmento Beta (más hidrólisis + más enemigos).</li>
              <li>Nivel 3: Segmento Gamma (FINAL con temporizador de protocolo).</li>
            </ul>
          </div>
        </div>
        <div class="col card">
          <h3>Controles</h3>
          <div class="body">
            <div><span class="badge">WASD</span>/<span class="badge">Flechas</span> mover</div>
            <div><span class="badge">Space</span> salto</div>
            <div><span class="badge">E</span> estación</div>
            <div><span class="badge">Q</span> pulso (aturde cerca)</div>
            <div><span class="badge">1/2/3</span> capas</div>
            <div><span class="badge">Esc</span> pausa</div>
          </div>
          <div class="btnrow">
            <button id="start" class="primary">Iniciar</button>
            <button id="reset" class="danger">Reset run</button>
          </div>
        </div>
      </div>
    `);
    document.getElementById('start').onclick=()=>{ closeOverlay(); startGame(); };
    document.getElementById('reset').onclick=()=>{ hardReset(); closeOverlay(); startGame(); };
  }

  function showTutorial(){
    openOverlay(`
      <h2>Guía rápida (mostrar, no decir)</h2>
      <p>Acá la “violencia” no existe: los enemigos son <b>peligros biológicos</b> (nucleasas/radicales). Se evitan y el pulso de escaneo (Q) puede aturdirlos brevemente.</p>
      <div class="row">
        <div class="col card">
          <h3>Capas</h3>
          <div class="body">
            <b>1</b> Estructural: estaciones/salida/puentes<br/>
            <b>2</b> Química: zonas de hidrólisis (rojo)<br/>
            <b>3</b> Informacional: etiquetas/lectura (pistas)
          </div>
        </div>
        <div class="col card">
          <h3>Consejo</h3>
          <div class="body">
            Si bajás de 35% integridad, las colisiones se vuelven más peligrosas. Repará en estaciones para subir integridad y desbloquear puentes.
          </div>
        </div>
      </div>
      <div class="btnrow"><button id="ok" class="primary">Listo</button></div>
    `);
    document.getElementById('ok').onclick=closeOverlay;
  }

  function showPause(){
    scene='pause';
    openOverlay(`
      <h2>Pausa</h2>
      <p>Para volver: <span class="badge">Esc</span>.</p>
      <div class="row">
        <div class="col card">
          <h3>Estado</h3>
          <div class="body">
            Nivel: <span class="badge">${L.name}</span><br/>
            Integridad: <span class="badge">${dnaIntegrity.toFixed(0)}%</span><br/>
            Evidencia total: <span class="badge">${evidenceLog.length}</span><br/>
            Fallos: <span class="badge">${cog.fails}</span><br/>
            ${levelIndex===2 ? `Final: tiempo <span class="badge">${finalTimer.toFixed(0)}s</span> · objetivo ≥<span class="badge">${CFG.final.stabilizeTarget}%</span>` : ''}
          </div>
          <div class="btnrow">
            <button id="resume" class="primary">Continuar</button>
            <button id="menu" class="danger">Menú</button>
          </div>
        </div>
        <div class="col card">
          <h3>Tip rápido</h3>
          <div class="body">
            Usá Q cerca de peligros para aturdir y abrir paso. No es combate: es mitigación de riesgo.
          </div>
        </div>
      </div>
    `);
    document.getElementById('resume').onclick=()=>{ closeOverlay(); scene='play'; };
    document.getElementById('menu').onclick=()=>{ closeOverlay(); showMenu(); };
  }

  function showEnding(){
    scene='ending';
    openOverlay(`
      <h2>FINAL — Protocolo completado</h2>
      <p>El segmento Gamma quedó estabilizado. Reconstruiste conectividad y preservaste coherencia de lectura bajo presión (hidrólisis + peligros).</p>
      <div class="row">
        <div class="col card">
          <h3>Resultados</h3>
          <div class="body">
            Integridad final: <span class="badge">${dnaIntegrity.toFixed(0)}%</span><br/>
            Evidencia recolectada: <span class="badge">${evidenceLog.length}</span><br/>
            Fallos técnicos: <span class="badge">${cog.fails}</span><br/>
            Cognición: Atención <span class="badge">${cog.att.toFixed(0)}</span> · Fatiga <span class="badge">${cog.fat.toFixed(0)}</span> · Precisión <span class="badge">${cog.prec.toFixed(0)}</span> · Comprensión <span class="badge">${cog.comp.toFixed(0)}</span>
          </div>
        </div>
        <div class="col card">
          <h3>Informe breve</h3>
          <div class="body">
            La hidrólisis se modeló como degradación progresiva modulada por pH, temperatura, estrés y humedad.
            El pulso de escaneo redujo riesgo al aturdir peligros cercanos. La reparación por ensamblaje (bases + backbone)
            restauró continuidad estructural e informacional.
          </div>
        </div>
      </div>
      <hr/>
      <h3>Evidencias</h3>
      ${evidenceLog.slice(0,14).map(e=>`
        <div class="card" style="margin-top:10px">
          <h3>${e.title} <span class="badge">${e.id}</span></h3>
          <div class="meta">Bonus: ${e.bonus}</div>
          <div class="body"><b>Obs:</b> ${e.observation}<br/><b>Hip:</b> ${e.hypothesis}<br/><b>Conc:</b> ${e.conclusion}</div>
        </div>
      `).join('') || '<p>No recolectaste evidencias.</p>'}
      <div class="btnrow" style="margin-top:14px">
        <button id="again" class="primary">Reiniciar campaña</button>
        <button id="menu" class="danger">Menú</button>
      </div>
    `);
    document.getElementById('again').onclick=()=>{ hardReset(); closeOverlay(); startGame(); };
    document.getElementById('menu').onclick=()=>{ closeOverlay(); showMenu(); };
  }

  function nextLevel(){
    if(levelIndex < LEVELS.length-1){
      loadLevel(levelIndex+1);
      statusText = `Entrando a ${L.name}...`;
    } else {
      showEnding();
    }
  }

  function startGame(){
    scene='play';
    if(!L) loadLevel(0);
  }

  function hardReset(){
    evidenceLog = [];
    evidenceActiveCount = 0;
    inv.A=inv.T=inv.C=inv.G=inv.S=inv.P=0;
    dnaIntegrity = 100;
    loadLevel(0);
    statusText="Run reiniciado.";
  }

  // ---------- rendering ----------
  function worldToScreenSetup(){
    ctx.setTransform(1,0,0,1,0,0);
    ctx.clearRect(0,0,W,H);
    // letterbox with pixel look: draw world at DPR with nearest
    // We'll render in world pixels directly (scaled by DPR later)
    ctx.imageSmoothingEnabled = false;
  }

  function beginCamera(){
    ctx.save();
    ctx.translate(Math.floor(W/2), Math.floor(H/2));
    // world pixels scaled by DPR (keep crisp)
    ctx.scale(DPR, DPR);
    ctx.translate(Math.floor(-cam.x), Math.floor(-cam.y));
  }
  function endCamera(){ ctx.restore(); }

  function drawSprite(uv, x,y, scale=2){
    // scale: sprite pixel scale in world
    ctx.drawImage(Atlas.img, uv.sx,uv.sy,uv.sw,uv.sh, Math.floor(x),Math.floor(y), uv.sw*scale, uv.sh*scale);
  }

  function drawParallax(){
    // micro-particles
    ctx.save();
    ctx.setTransform(1,0,0,1,0,0);
    for(let i=0;i<70;i++){
      const t=(tNow*0.1+i)*17.3;
      const x=((Math.sin(t)*0.5+0.5)*W)|0;
      const y=((Math.cos(t*0.8)*0.5+0.5)*H)|0;
      ctx.fillStyle='rgba(232,238,247,0.05)';
      ctx.fillRect(x,y,1,1);
    }
    ctx.restore();
  }

  function drawHelixBackdrop(){
    // stylized helix lines (sprite-ish)
    const b=L.bounds;
    const midY=b.y + b.h*0.52;
    const t=tNow*0.55;

    ctx.save();
    ctx.lineCap='butt';
    ctx.lineWidth=2;
    for(let lane=0; lane<2; lane++){
      ctx.beginPath();
      for(let x=b.x; x<=b.x+b.w; x+=14){
        const ph=(x*0.012+t)+lane*Math.PI;
        const y=midY + Math.sin(ph)*120;
        if(x===b.x) ctx.moveTo(x,y); else ctx.lineTo(x,y);
      }
      ctx.strokeStyle = lane===0 ? 'rgba(255,255,255,0.10)' : 'rgba(255,255,255,0.07)';
      ctx.stroke();
    }

    // base-pairs rungs
    for(let x=b.x+40; x<b.x+b.w; x+=78){
      const ph=(x*0.012+t);
      const y1=midY + Math.sin(ph)*120;
      const y2=midY + Math.sin(ph+Math.PI)*120;
      ctx.strokeStyle='rgba(35,198,209,0.12)';
      ctx.beginPath(); ctx.moveTo(x,y1); ctx.lineTo(x,y2); ctx.stroke();
    }
    ctx.restore();
  }

  function drawPlatforms(){
    for(const p of L.platforms){
      if(p.enabled===false) continue;
      // pixel panel
      ctx.fillStyle='rgba(255,255,255,0.06)';
      ctx.fillRect(p.x,p.y,p.w,p.h);
      ctx.fillStyle='rgba(35,198,209,0.16)';
      // top edge
      ctx.fillRect(p.x,p.y,p.w,1);
    }
  }

  function drawZones(){
    if(!(scanActive() && scan.layer===2)) return;
    for(const z of L.hydroZones){
      ctx.fillStyle='rgba(209,74,74,0.12)';
      ctx.fillRect(z.x,z.y,z.w,z.h);
      ctx.fillStyle='rgba(209,74,74,0.45)';
      ctx.fillRect(z.x,z.y,z.w,1);
      ctx.fillRect(z.x,z.y,1,z.h);
      ctx.fillRect(z.x+z.w-1,z.y,1,z.h);
      ctx.fillRect(z.x,z.y+z.h-1,z.w,1);

      // label (sprite text)
      ctx.fillStyle='rgba(232,238,247,.8)';
      ctx.font='12px ui-monospace, Menlo, Consolas';
      ctx.fillText(`pH ${z.ph.toFixed(1)} · T ${z.temperature}K · estrés ${z.stress}`, z.x+6, z.y+14);
    }
  }

  function drawCollectibles(){
    for(const it of L.collectibles){
      if(it.collected) continue;
      const uv = it.kind==='evidence' ? Atlas.evidence : Atlas.comp;
      drawSprite(uv, it.x, it.y, 2);
    }
  }

  function drawTriggers(){
    if(!(scanActive() && scan.layer===1)) return;
    ctx.font='12px ui-monospace, Menlo, Consolas';
    for(const tr of L.triggers){
      ctx.fillStyle='rgba(35,198,209,0.20)';
      ctx.fillRect(tr.x,tr.y,tr.w,tr.h);
      ctx.fillStyle='rgba(35,198,209,0.65)';
      ctx.fillText(tr.type.toUpperCase(), tr.x+4, tr.y+14);
    }
  }

  function drawStationsAndExit(){
    // draw station sprites (always)
    for(const st of L.stations){
      drawSprite(Atlas.station, st.x, st.y, 2);
    }
    drawSprite(Atlas.exit, L.exit.x, L.exit.y, 2);
  }

  function drawEnemies(){
    for(const e of L.enemies){
      const uv = e.type==='nuclease' ? Atlas.nuclease : Atlas.radical;
      if(e.stunned>0){
        // blink when stunned
        if(((tNow*12)|0)%2===0) continue;
      }
      drawSprite(uv, e.x, e.y, 2);
    }
  }

  function drawPins(){
    for(const p of scan.pins){
      ctx.fillStyle='rgba(35,198,209,0.95)';
      ctx.fillRect(p.x-2,p.y-8,4,16);
      ctx.fillRect(p.x-8,p.y-2,16,4);
    }
  }

  function drawPlayer(){
    // pick frame
    const frame = (player.onGround && Math.abs(player.vx)>30) ? ((player.animT|0)%4) : 0;
    const uv = Atlas.player(frame);
    // shadow
    ctx.fillStyle='rgba(0,0,0,0.30)';
    ctx.fillRect(player.x+2, player.y+player.h+6, 24, 3);

    // sprite
    drawSprite(uv, player.x, player.y, 2);

    // i-frame indicator
    if(player.iframe>0){
      ctx.fillStyle='rgba(35,198,209,0.18)';
      ctx.fillRect(player.x-2,player.y-2, 36, 36);
    }

    // scan ring
    if(scanActive()){
      ctx.strokeStyle='rgba(35,198,209,0.22)';
      ctx.lineWidth=2;
      ctx.beginPath();
      ctx.arc(player.x+16, player.y+16, CFG.scan.stunRadius, 0, Math.PI*2);
      ctx.stroke();
    }
  }

  function drawFinalHUD(){
    if(levelIndex!==2) return;
    ctx.save();
    ctx.setTransform(1,0,0,1,0,0);
    ctx.font='12px ui-monospace, Menlo, Consolas';
    ctx.fillStyle='rgba(232,238,247,0.85)';
    const txt = finalDone
      ? `FINAL: protocolo listo → ir a la salida`
      : `FINAL: tiempo ${finalTimer.toFixed(0)}s · objetivo integridad ≥${CFG.final.stabilizeTarget}% · evidencia ≥8 · comprensión ≥20`;
    ctx.fillText(txt, 16, H-16);
    ctx.restore();
  }

  function drawMenuBackdrop(){
    ctx.save();
    ctx.setTransform(1,0,0,1,0,0);
    ctx.fillStyle='rgba(0,0,0,0.18)';
    ctx.fillRect(0,0,W,H);
    ctx.font='700 26px ui-sans-serif, system-ui';
    ctx.fillStyle='rgba(232,238,247,0.95)';
    ctx.textAlign='center';
    ctx.fillText('HELIX REPAIR (SPRITE)', W/2, H*0.25);
    ctx.font='600 14px ui-monospace, Menlo, Consolas';
    ctx.fillStyle='rgba(183,194,211,0.92)';
    ctx.fillText('3 niveles · peligros (nucleasas/radicales) · final con protocolo', W/2, H*0.25+28);
    ctx.textAlign='left';
    ctx.restore();
  }

  // ---------- vignette/noise (light) ----------
  function vignette(intensity=0.28){
    ctx.save();
    ctx.setTransform(1,0,0,1,0,0);
    const g = ctx.createRadialGradient(W/2,H/2,W*0.1, W/2,H/2,W*0.72);
    g.addColorStop(0,'rgba(0,0,0,0)');
    g.addColorStop(1,`rgba(0,0,0,${clamp(intensity,0,0.7)})`);
    ctx.fillStyle=g;
    ctx.fillRect(0,0,W,H);
    ctx.restore();
  }
  function noiseOverlay(amount){
    const a=clamp(amount,0,0.6);
    if(a<0.12) return;
    const w=140,h=80;
    const img=ctx.createImageData(w,h);
    for(let i=0;i<img.data.length;i+=4){
      const v=(Math.random()*255)|0;
      img.data[i]=v; img.data[i+1]=v; img.data[i+2]=v; img.data[i+3]=(a*255)|0;
    }
    ctx.save();
    ctx.setTransform(1,0,0,1,0,0);
    ctx.putImageData(img,0,0);
    ctx.globalCompositeOperation='overlay';
    ctx.drawImage(canvas,0,0,w,h,0,0,W,H);
    ctx.restore();
    ctx.globalCompositeOperation='source-over';
  }

  // ---------- gameplay flow ----------
  function doExitCheck(){
    // if player reaches exit region, trigger exit logic
    const pa = playerAABB();
    const ex = {x:L.exit.x,y:L.exit.y,w:32,h:32};
    if(overlap(pa, ex)){
      const tr = L.triggers.find(t=>t.type==='exit');
      if(tr && !tr.fired){
        tr.fired=true;
        // delegate to triggers
        // reset fired if blocked:
        tr.fired=false;
      }
    }
  }

  // ---------- main loop ----------
  let last = performance.now();
  let tNow = 0;

  function tick(now){
    const dt = clamp((now-last)/1000, 0, CFG.maxDt);
    last=now;
    tNow += dt;

    // menu overlay draws game in background if level loaded
    worldToScreenSetup();

    if(scene==='menu'){
      drawParallax();
      drawMenuBackdrop();
      // allow start on pointer
    }
    else {
      // game render
      drawParallax();
      beginCamera();
      drawHelixBackdrop();
      drawPlatforms();
      drawZones();
      drawStationsAndExit();
      drawCollectibles();
      drawEnemies();
      drawTriggers();
      drawPins();
      drawPlayer();
      endCamera();
      drawFinalHUD();
      vignette(0.30);
      noiseOverlay(scanNoise()*0.35);
    }

    if(scene==='play'){
      // pause
      if(wasPressed('pause')) showPause();

      // cognition/scan
      evidenceActiveCount = Math.min(3, evidenceLog.length);
      cog.activeEvidenceCount = evidenceActiveCount;
      stepCognition(dt);
      stepScan(dt);

      // simulate
      stepHydrolysis(dt);
      stepPlayer(dt);
      stepEnemies(dt);
      stepCollectibles();
      stepTriggers();
      stepCamera(dt);

      // final auto hint
      if(levelIndex===2 && !finalDone){
        if(dnaIntegrity>=CFG.final.stabilizeTarget && evidenceLog.length>=8 && cog.comp>=20){
          finalDone=true;
          statusText="Protocolo final listo: ir a salida.";
        }
      }

      // if touch layer buttons were pressed, make them one-shot
      tBtn.l1=tBtn.l2=tBtn.l3=false;

      updateHUD();
    }

    if(scene==='pause'){
      if(wasPressed('pause')){ closeOverlay(); scene='play'; }
      updateHUD();
    }

    // clear pressed
    keyPressed.clear();

    requestAnimationFrame(tick);
  }

  // ---------- trigger logic / exit / station / tutorial ----------
  function stepTriggers(){
    const pa = playerAABB();
    for(const tr of L.triggers){
      if(tr.fired) continue;
      if(!overlap(pa,tr)) continue;

      if(tr.type==='tutorial'){ tr.fired=true; showTutorial(); }
      if(tr.type==='station'){ /* handled by E */ }
      if(tr.type==='exit'){
        if(levelIndex<2){
          if(dnaIntegrity>=55 || evidenceLog.length>=4+levelIndex){
            tr.fired=true;
            nextLevel();
          } else {
            statusText="Salida bloqueada: estabilizar o recolectar evidencia.";
          }
        } else {
          if(finalDone){
            tr.fired=true;
            showEnding();
          } else {
            statusText="Final: estabilizar ≥75%, evidencia ≥8 y comprensión ≥20.";
          }
        }
      }
    }
  }

  // ---------- start / initial ----------
  function startCampaign(){
    loadLevel(0);
    closeOverlay();
    scene='play';
  }

  // click/tap starts from menu overlay too
  window.addEventListener('pointerdown', ()=>{
    if(scene==='menu'){
      // if overlay is open with Start button, let user click button; but tapping anywhere also starts
      // (avoid if user is scrolling modal)
    }
  }, {passive:true});

  // Put menu first
  showMenu();

  // ensure layer buttons reflect
  setLayer(1);

  // Start loop
  requestAnimationFrame(tick);

  // Start button in menu overlay already wired there, but also allow keyboard
  window.addEventListener('keydown', (e)=>{
    if(scene==='menu' && (e.key===' ' || e.key==='e' || e.key==='E')){
      // find start button if exists
      const b = document.getElementById('start');
      if(b) b.click();
      else startCampaign();
    }
  }, {passive:true});

  // If start button missing (shouldn't), fallback:
  window.addEventListener('pointerdown', ()=>{
    if(scene==='menu'){
      const b = document.getElementById('start');
      if(b) return; // user can click it
    }
  }, {passive:true});

})();
</script>
</body>
</html>
