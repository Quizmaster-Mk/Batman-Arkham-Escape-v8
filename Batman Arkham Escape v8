<!DOCTYPE html>
<html lang="fr">
<head>
<meta charset="utf-8" />
<meta name="viewport" content="width=device-width,initial-scale=1" />
<title>Batman – Arkham Escape V7</title>
<style>
  :root{
    --bg:#07070a; --panel:#121318; --muted:#9aa0a6; --accent:#1abc9c;
    --danger:#ff6b6b; --crit:#ffd24d; --text:#e6eef0;
  }
  *{box-sizing:border-box}
  body{
    margin:0; font-family:Inter,system-ui,Arial; background:linear-gradient(180deg,#020204,#0b0f12);
    color:var(--text); display:flex; justify-content:center; padding:18px;
  }
  .wrap{ width:640px; max-width:98%; background:var(--panel); border-radius:12px; padding:16px; border:1px solid rgba(255,255,255,0.03); box-shadow: 0 10px 40px rgba(0,0,0,.7);}
  header{text-align:center}
  h1{margin:6px 0; font-size:20px; color:var(--accent)}
  .row{display:flex; gap:12px; align-items:flex-start; margin-top:8px}
  .col{flex:1}
  .panel{background:rgba(255,255,255,0.02); padding:10px; border-radius:8px;}
  .avatar{font-size:56px; display:inline-block; width:90px; height:90px; line-height:90px; text-align:center; vertical-align:middle; transition:transform .25s ease, filter .2s ease;}
  .avatar.hit{ animation:hitAnim .32s ease; }
  @keyframes hitAnim{ 0%{ transform:scale(1);} 50%{ transform:scale(1.18);} 100%{ transform:scale(1);} }

  .hpbar{height:14px;background:#1b1d20;border-radius:10px;overflow:hidden;border:1px solid rgba(255,255,255,.03)}
  .hpfill{height:100%; transition:width .35s linear; background:linear-gradient(90deg,#2ecc71,#1abc9c)}
  .hpfill.enemy{ background:linear-gradient(90deg,#ff6b6b,#ff4d4d) }

  button{background:#0f1316;color:var(--text);border:1px solid rgba(255,255,255,.03); padding:8px 12px;border-radius:8px; cursor:pointer; font-weight:700}
  button:hover{ transform:translateY(-2px); border-color:var(--accent); color:var(--accent) }

  #actions{margin-top:10px; display:flex; gap:8px; flex-wrap:wrap; justify-content:center}
  #log{ margin-top:12px; background:rgba(255,255,255,0.02); padding:10px; border-radius:8px; height:160px; overflow:auto; font-size:14px; color:var(--muted) }

  .floating-dmg{ position:fixed; font-weight:900; pointer-events:none; text-shadow:0 6px 18px rgba(0,0,0,.6); transform:translate(-50%,-50%); animation:floatUp 900ms ease-out forwards; }
  @keyframes floatUp{ from{ opacity:1; transform:translate(-50%,0) scale(1);} to{ opacity:0; transform:translate(-50%,-60px) scale(1.05);} }

  .small{ font-size:13px; color:var(--muted) }
  .inventory{ display:flex; gap:6px; flex-wrap:wrap; justify-content:center; margin-top:8px }
  .item{ background:#0f1316; padding:6px 8px; border-radius:8px; border:1px solid rgba(255,255,255,0.03); cursor:pointer }
</style>
</head>
<body>
<div class="wrap" role="application" aria-label="Batman Arkham Escape">
  <header><h1>🦇 Batman — Arkham Escape V7</h1></header>

  <!-- Écran de démarrage -->
  <div id="startPanel" class="panel" style="margin-top:12px;">
    <div class="small">Choisis la difficulté pour démarrer :</div>
    <div style="margin-top:8px;">
      <button onclick="menuStart('facile')">😃 Facile</button>
      <button onclick="menuStart('normal')">😐 Normal</button>
      <button onclick="menuStart('difficile')">😈 Difficile</button>
    </div>
  </div>

  <!-- Jeu -->
  <div id="gamePanel" class="panel hidden" style="margin-top:12px;">
    <div class="row">
      <div class="col" style="max-width:260px">
        <div class="small">BATMAN</div>
        <div id="playerAvatar" class="avatar">🦇</div>
        <div class="small" id="playerPVtxt">PV: 40 / 40</div>
        <div class="hpbar" style="margin-top:6px"><div id="playerHP" class="hpfill" style="width:100%"></div></div>
        <div class="small" style="margin-top:8px">Objets</div>
        <div id="inventory" class="inventory"><div class="small muted">Aucun</div></div>
      </div>

      <div class="col">
        <div id="sceneText" class="small muted">Choisis une sortie :</div>
        <div style="margin-top:10px" id="choiceButtons">
          <button onclick="chooseDoor('gauche')">🚪 Porte gauche</button>
          <button onclick="chooseDoor('droite')">🚪 Porte droite</button>
          <button onclick="chooseDoor('ascenseur')">⬆️ Ascenseur</button>
        </div>

        <div id="encounterArea" style="margin-top:8px; display:none">
          <div style="display:flex; align-items:center; gap:10px; justify-content:center">
            <div id="enemyAvatar" class="avatar">👤</div>
            <div style="text-align:left">
              <div id="enemyName" class="small">Ennemi</div>
              <div class="small" id="enemyPVtxt">PV: 0 / 0</div>
              <div class="hpbar" style="margin-top:6px"><div id="enemyHP" class="hpfill enemy" style="width:0%"></div></div>
            </div>
          </div>
          <div id="actions" style="margin-top:12px"></div>
        </div>
      </div>
    </div>
    <div id="log" aria-live="polite"></div>
  </div>

  <!-- Fin -->
  <div id="endPanel" class="panel hidden" style="margin-top:12px; text-align:center">
    <h3 id="endTitle"></h3>
    <div class="small" id="endText"></div>
    <div style="margin-top:12px">
      <button onclick="resetAll()">🔄 Rejouer</button>
    </div>
  </div>
</div>

<script>
let difficulty = 'normal';
let batman = null;
let encounters = 0;
let currentEnemy = null;
let combatInProgress = false;
let riddleIndex = 0;
let riddlesByDiff = {
  facile: [
    {q:"Je peux être brisé sans être touché, qui suis-je ?",answers:["Un secret","Un verre","Un cœur"],correct:0},
    {q:"Je suis toujours devant toi mais on ne peut me voir, qui suis-je ?",answers:["L'avenir","Un miroir","Le vent"],correct:0},
    {q:"Plus je prends de la place, moins on me voit, qui suis-je ?",answers:["L'obscurité","La fumée","La poussière"],correct:0}
  ],
  normal: [
    {q:"Plus je sèche, plus je deviens mouillé. Qui suis-je ?",answers:["Une serviette","Une éponge","Une pluie"],correct:0},
    {q:"Je grandis sans être vivant, j’ai besoin d’air. Qui suis-je ?",answers:["Le feu","Une ombre","Une vague"],correct:0},
    {q:"Je voyage autour du monde tout en restant dans un coin. Qui suis-je ?",answers:["Un timbre","Un oiseau","Une boussole"],correct:0}
  ],
  difficile: [
    {q:"On me prend avant de me donner. Qui suis-je ?",answers:["Une photo","Un cadeau","Un billet"],correct:0},
    {q:"Je parle sans bouche, j’entends sans oreilles. Qui suis-je ?",answers:["Un écho","Un fantôme","Un secret"],correct:0},
    {q:"Moins j’existe, plus vous me voyez. Qui suis-je ?",answers:["Un trou","Une étoile","L'ombre"],correct:0}
  ]
};
let riddles = [];

function $(id){return document.getElementById(id);}
function rand(min,max){return Math.floor(Math.random()*(max-min+1))+min;}
function log(msg){const d=$('log');const el=document.createElement('div');el.innerHTML=msg;d.appendChild(el);d.scrollTop=d.scrollHeight;}
function spawnFloatingAt(element,txt,color='white'){const r=element.getBoundingClientRect();const f=document.createElement('div');f.className='floating-dmg';f.textContent=txt;f.style.left=(r.left+r.width/2)+'px';f.style.top=(r.top+8)+'px';f.style.color=color;document.body.appendChild(f);setTimeout(()=>f.remove(),900);}
function animateStrike(attackerEl,targetEl,cb){if(!attackerEl||!targetEl){if(cb)cb();return;}const orig=attackerEl.style.transform||'';const aRect=attackerEl.getBoundingClientRect();const tRect=targetEl.getBoundingClientRect();const dx=(tRect.left+tRect.width/2)-(aRect.left+aRect.width/2);const dy=(tRect.top+tRect.height/2)-(aRect.top+aRect.height/2);attackerEl.style.transition='transform 260ms ease';attackerEl.style.transform=`translate(${dx*0.68}px, ${dy*0.08}px) scale(1.06)`;setTimeout(()=>{attackerEl.style.transform=orig;targetEl.classList.add('hit');setTimeout(()=>targetEl.classList.remove('hit'),330);if(cb)setTimeout(cb,300);},300);}

function menuStart(diff){
  difficulty=diff;
  batman={pv:(diff==='facile'?50:(diff==='difficile'?30:40)),max:(diff==='facile'?50:(diff==='difficile'?30:40)),items:[],hasEvade:false,batarang:false};
  encounters=0;currentEnemy=null;combatInProgress=false;riddleIndex=0;
  riddles=[...riddlesByDiff[diff]];
  $('startPanel').classList.add('hidden');
  $('gamePanel').classList.remove('hidden');
  $('log').innerHTML='';updateUI();
  log(`🔹 Difficulté ${diff.toUpperCase()} sélectionnée. Batman s'évade...`);
}

function updateUI(){
  $('playerPVtxt').textContent=`PV: ${batman.pv} / ${batman.max}`;
  $('playerHP').style.width=Math.max(0,(batman.pv/batman.max*100))+'%';
  const inv=$('inventory');inv.innerHTML='';
  if(batman.items.length===0) inv.innerHTML='<div class="small">Aucun</div>';
  else batman.items.forEach((it,idx)=>{const d=document.createElement('div');d.className='item';d.textContent=it;d.onclick=()=>useItem(idx);inv.appendChild(d);});
  if(currentEnemy&&currentEnemy.type==='combat'){$('encounterArea').style.display='block';$('enemyAvatar').textContent=currentEnemy.emoji;$('enemyName').textContent=currentEnemy.name;$('enemyPVtxt').textContent=`PV: ${currentEnemy.pv} / ${currentEnemy.max}`;$('enemyHP').style.width=Math.max(0,(currentEnemy.pv/currentEnemy.max*100))+'%';}else{$('encounterArea').style.display='none';}
}

function chooseDoor(dir){
  $('choiceButtons').style.display='none';
  setTimeout(()=>{
    if(dir==='gauche'){currentEnemy={type:'combat',id:'joker',name:'Joker',emoji:'🤡',pv:20,max:20,dmgRange:[2,5]};}
    else if(dir==='droite'){currentEnemy={type:'combat',id:'bane',name:'Bane',emoji:'💪',pv:25,max:25,dmgRange:[3,7]};}
    else{currentEnemy={type:'riddle',id:'riddler',name:"L'Homme-Mystère",emoji:'🧩'};}
    proceedEncounter();
  },450);
}

function proceedEncounter(){
  if(currentEnemy.type==='combat'){
    $('sceneText').textContent=`Tu rencontres ${currentEnemy.name} !`;
    $('actions').innerHTML=`<button onclick="playerAttack('fast')">⚡ Rapide (3–5)</button><button onclick="playerAttack('heavy')">💥 Lourde (5–9)</button><button onclick="playerEvade()">🌀 Esquive</button>`;
    updateUI();
  } else { startRiddleSequence(); }
}

function playerAttack(kind){
  if(combatInProgress)return;combatInProgress=true;
  let dmg=0;if(kind==='fast')dmg=rand(3,5);if(kind==='heavy'){if(Math.random()<0.25){log('❌ Attaque ratée');setTimeout(()=>{enemyTurn();combatInProgress=false;},420);return;}dmg=rand(5,9);}
  const playerEl=$('playerAvatar'),enemyEl=$('enemyAvatar');
  animateStrike(playerEl,enemyEl,()=>{currentEnemy.pv=Math.max(0,currentEnemy.pv-dmg);spawnFloatingAt(enemyEl,`-${dmg}`,'#ffcc66');log(`🦇 Batman inflige ${dmg} PV !`);updateUI();if(currentEnemy.pv<=0){setTimeout(()=>{victory();combatInProgress=false;},420);}else{setTimeout(()=>{enemyTurn();combatInProgress=false;},500);}});
}
function playerEvade(){batman.hasEvade=true;log('🌀 Esquive préparée');setTimeout(()=>enemyTurn(),420);}
function enemyTurn(){if(!currentEnemy||currentEnemy.type!=='combat')return;if(batman.hasEvade){log('🛡️ Attaque évitée');batman.hasEvade=false;return;}let dmg=rand(currentEnemy.dmgRange[0],currentEnemy.dmgRange[1]);animateStrike($('enemyAvatar'),$('playerAvatar'),()=>{batman.pv=Math.max(0,batman.pv-dmg);spawnFloatingAt($('playerAvatar'),`-${dmg}`,'#ff6b6b');log(`${currentEnemy.name} inflige ${dmg} PV !`);updateUI();if(batman.pv<=0)defeat();});}

function victory(){combatInProgress=false;encounters++;batman.items.push('Trousse');log('🎁 Victoire ! Tu trouves une Trousse');updateUI();if(encounters>=2){showEnd(true);return;}currentEnemy=null;$('choiceButtons').style.display='block';}
function defeat(){showEnd(false);}
function useItem(i){const it=batman.items[i];if(it==='Trousse'){const heal=10;batman.pv=Math.min(batman.max,batman.pv+heal);log(`💉 +${heal} PV`);}batman.items.splice(i,1);updateUI();}

function startRiddleSequence(){riddleIndex=0;showRiddle();}
function showRiddle(){if(riddleIndex>=riddles.length){log('✅ Énigmes résolues');victory();return;}const r=riddles[riddleIndex];$('sceneText').textContent=r.q;$('actions').innerHTML=r.answers.map((a,i)=>`<button onclick="answerRiddle(${i})">${a}</button>`).join('');}
function answerRiddle(i){const r=riddles[riddleIndex];const penalty=(difficulty==='facile'?3:(difficulty==='difficile'?8:5));if(i===r.correct){log('✅ Bonne réponse');}else{batman.pv=Math.max(0,batman.pv-penalty);spawnFloatingAt($('playerAvatar'),`-${penalty}`,'#ff6b6b');log(`❌ Mauvaise réponse (-${penalty} PV)`);updateUI();if(batman.pv<=0){defeat();return;}}riddleIndex++;setTimeout(()=>showRiddle(),450);}

function showEnd(won){$('gamePanel').classList.add('hidden');$('endPanel').classList.remove('hidden');$('endTitle').textContent=won?'🚨 Succès : Évasion !':'☠️ Défaite';$('endText').textContent=won?'Batman atteint le toit et s\'échappe.':'Batman est capturé...';}
function resetAll(){$('endPanel').classList.add('hidden');$('startPanel').classList.remove('hidden');$('gamePanel').classList.add('hidden');}
</script>
</body>
</html>
