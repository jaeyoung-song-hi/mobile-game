<html lang="ko">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, user-scalable=no">
    <title>급똥 탈출! v2 모던</title>
    <link rel="preconnect" href="https://fonts.googleapis.com">
    <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
    <link href="https://fonts.googleapis.com/css2?family=Jua&display=swap" rel="stylesheet">
    <style>
        :root {
            --bg-color: #F3F4F6;
            --primary-color: #3B82F6;
            --secondary-color: #10B981;
            --danger-color: #EF4444;
            --text-color: #1F2937;
            --border-color: #D1D5DB;
            --home-bg: #FEFCE8;
            --street-bg: #E0F2FE;
        }
        * { box-sizing: border-box; }
        body {
            font-family: 'Jua', sans-serif;
            background-color: var(--bg-color);
            color: var(--text-color);
            margin: 0; padding: 0;
            display: flex; justify-content: center; align-items: center;
            height: 100vh; overflow: hidden;
            touch-action: none;
        }
        #game-container {
            width: 100%; max-width: 420px; height: 100%;
            background: white; border-radius: 20px;
            box-shadow: 0 10px 25px rgba(0,0,0,0.1);
            display: flex; flex-direction: column; position: relative;
        }
        header {
            padding: 15px; background: var(--primary-color);
            color: #fff; text-align: center;
            border-bottom: 5px solid rgba(0,0,0,0.1);
        }
        header h1 { margin:0; font-size:1.8em; }
        #status-bar {
            display:flex; justify-content:space-around;
            padding:10px; background:#f9fafb; font-size:1.1em;
        }
        #game-area { flex:1; position:relative; overflow:hidden;
            border-top:1px solid var(--border-color);
            border-bottom:1px solid var(--border-color);
        }
        .scene { position:absolute; width:100%; height:100%; }
        #home-scene { background:var(--home-bg); }
        #street-scene { background:var(--street-bg); }
        .game-object { position:absolute; display:flex;
            justify-content:center; align-items:center; font-size:36px;
        }
        #player {
            width:40px; height:40px; background:#FBBF24;
            border:3px solid #F59E0B; border-radius:50%;
            transition: top 0.1s linear, left 0.1s linear;
        }
        /* Home objects */
        #fridge { top:20px; right:20px; font-size:50px; }
        #bed    { bottom:20px; left:20px; font-size:50px; }
        #treadmill { bottom:20px; right:20px; font-size:50px; }
        #door   { top:20px; left:20px; font-size:50px; }
        /* Street objects */
        .obstacle {
            position:absolute; width:30px; height:60px;
            background:#9CA3AF; border-radius:8px;
            animation: fall infinite linear;
        }
        @keyframes fall {
            from { transform: translateY(-80px); }
            to   { transform: translateY(100vh); }
        }
        #toilet { font-size:40px; width:50px; height:50px; }
        #timer-container { width:90%; margin:15px auto;
            background:var(--border-color); border-radius:10px;
        }
        #timer-bar { height:25px; width:100%;
            background:var(--secondary-color); border-radius:10px;
            transition: width 0.5s linear, background-color 0.5s;
        }
        #controls {
            display:grid; grid-template-columns:repeat(3,1fr);
            grid-template-rows:repeat(2,1fr); gap:10px;
            padding:20px; background:#f9fafb;
        }
        .control-btn {
            font-size:2em; padding:15px; background:#fff;
            border:none; border-radius:15px;
            box-shadow:0 4px 6px rgba(0,0,0,0.1);
            color:var(--primary-color); cursor:pointer;
            user-select:none;
        }
        .control-btn:active { transform:translateY(2px);
            box-shadow:0 2px 3px rgba(0,0,0,0.1);
        }
        #up    { grid-column:2; grid-row:1; }
        #left  { grid-column:1; grid-row:2; }
        #right { grid-column:3; grid-row:2; }
        #down  { grid-column:2; grid-row:2; }
        /* Modal */
        #modal { position:absolute; top:0; left:0;
            width:100%; height:100%; background:rgba(0,0,0,0.6);
            display:flex; justify-content:center; align-items:center;
            z-index:100; }
        .modal-content {
            background:#fff; padding:20px; border-radius:20px;
            width:85%; max-width:380px; text-align:center;
            box-shadow:0 10px 25px rgba(0,0,0,0.2);
        }
        .modal-content h2 { margin-top:0; font-size:1.8em; }
        .modal-content p { font-size:1.1em; margin-bottom:20px; }
        .choice-buttons { display:flex; flex-direction:column; gap:10px; }
        .modal-button {
            padding:12px; border:none; border-radius:12px;
            font-size:1.1em; font-family:'Jua',sans-serif;
            cursor:pointer; transition:background 0.2s;
        }
        .modal-button.primary { background:var(--primary-color); color:#fff; }
        .modal-button.primary:hover { background:#2563EB; }
        .modal-button.cancel { background:#6B7280; color:#fff; }
        .modal-button.cancel:hover  { background:#4B5563; }
        .hidden { display:none; }
    </style>
</head>
<body>
    <div id="game-container">
        <header><h1>급똥 탈출! v2</h1></header>
        <div id="status-bar">
            <span>레벨: <span id="level">1</span></span>
            <span>점수: <span id="score">0</span></span>
        </div>
        <div id="game-area">
            <div id="home-scene" class="scene"></div>
            <div id="street-scene" class="scene hidden"></div>
            <div id="player" class="game-object">😊</div>
        </div>
        <div id="timer-container"><div id="timer-bar"></div></div>
        <div id="controls">
            <button class="control-btn" id="up">▲</button>
            <button class="control-btn" id="left">◀</button>
            <button class="control-btn" id="down">▼</button>
            <button class="control-btn" id="right">▶</button>
        </div>
        <div id="modal" class="hidden">
            <div class="modal-content">
                <h2 id="modal-title"></h2>
                <p id="modal-message"></p>
                <div id="modal-buttons" class="choice-buttons"></div>
            </div>
        </div>
        <!-- Sound effects -->
        <audio id="sfx-start" src="assets/sounds/start.mp3" preload="auto"></audio>
        <audio id="sfx-success" src="assets/sounds/success.mp3" preload="auto"></audio>
        <audio id="sfx-fail" src="assets/sounds/fail.mp3" preload="auto"></audio>
    </div>
    <script>
    (function(){
        // 요소 캐싱
        const elems = {
            player: document.getElementById('player'),
            homeScene: document.getElementById('home-scene'),
            streetScene: document.getElementById('street-scene'),
            levelDisplay: document.getElementById('level'),
            scoreDisplay: document.getElementById('score'),
            timerBar: document.getElementById('timer-bar'),
            modal: document.getElementById('modal'),
            modalTitle: document.getElementById('modal-title'),
            modalMessage: document.getElementById('modal-message'),
            modalButtons: document.getElementById('modal-buttons'),
            sounds: {
                start: document.getElementById('sfx-start'),
                success: document.getElementById('sfx-success'),
                fail: document.getElementById('sfx-fail')
            }
        };
        let state = {
            phase: 'PREP', level:1, score:0,
            pos:{x:0,y:0}, active:false,
            timer:null, time:0, max:0
        };

        const settings = {
            speed: 12,
            baseTime:25,
            weatherPenalty:{ '따뜻함':0,'쌀쌀함':2,'추움':3}
        };
        const choices = {
            food: {'일상식 🍚':0,'기름진 음식 🍗':3,'매운 음식 🌶️':4,'찬 음식 🍦':2,'상한 음식 🤢':6},
            condition: {'푹 쉬기 😊':0,'스트레스 🤯':3,'운동 🏃💨':5}
        };
        function showModal(title,msg,buttons){
            elems.modalTitle.textContent=title;
            elems.modalMessage.innerHTML=msg;
            elems.modalButtons.innerHTML='';
            buttons.forEach(b=>{
                const btn=document.createElement('button');
                btn.textContent=b.text;
                btn.className=`modal-button ${b.cls||'primary'}`;
                btn.onclick=()=>{ elems.modal.classList.add('hidden'); b.cb(); };
                elems.modalButtons.appendChild(btn);
            });
            elems.modal.classList.remove('hidden');
        }
        function updateUI(){
            elems.levelDisplay.textContent=state.level;
            elems.scoreDisplay.textContent=state.score;
        }
        function placePlayer(center=true){
            const area=elems.player.parentNode.getBoundingClientRect();
            state.pos.x = center? (area.width/2-20): state.pos.x;
            state.pos.y = center? (area.height/2-20): state.pos.y;
            elems.player.style.left = state.pos.x+'px';
            elems.player.style.top  = state.pos.y+'px';
        }
        function clearObstacles(){
            elems.streetScene.querySelectorAll('.obstacle').forEach(o=>o.remove());
        }
        function spawnObstacles(){
            clearObstacles();
            const count = Math.min(10, state.level*2);
            const area = elems.streetScene.getBoundingClientRect();
            for(let i=0;i<count;i++){
                const obs = document.createElement('div');
                obs.className='obstacle';
                const sizeW=30, sizeH=60;
                obs.style.left = Math.random()*(area.width-sizeW)+'px';
                obs.style.top  = -Math.random()*area.height+'px';
                obs.style.animationDuration = (3+Math.random()*2-state.level*0.1)+'s';
                elems.streetScene.appendChild(obs);
            }
        }
        function startPrep(){
            state.phase='PREP'; state.active=true;
            clearInterval(state.timer);
            clearObstacles();
            elems.streetScene.classList.add('hidden');
            elems.homeScene.classList.remove('hidden');
            placePlayer(true); updateUI();
            elems.timerBar.style.width='100%'; elems.timerBar.style.background='var(--secondary-color)';
            elems.sounds.start.play();
        }
        function startUrgency(){
            state.phase='URGENCY'; state.active=false;
            elems.sounds.start.currentTime=0;
            elems.homeScene.classList.add('hidden');
            elems.streetScene.classList.remove('hidden');
            // 시간 계산
            const weatherKeys=Object.keys(settings.weatherPenalty);
            const weather=weatherKeys[Math.floor(Math.random()*weatherKeys.length)];
            state.max = Math.max(5, settings.baseTime - state.level*0.8 - state.food - state.cond - settings.weatherPenalty[weather]);
            state.time = state.max;
            // 장애물
            spawnObstacles();
            placePlayer(false);
            updateUI();
            showModal('으악!', `배가..! 화장실까지 가야해! (날씨: ${weather})`, [{text:'달려!',cb:()=>runTimer()}]);
        }
        function runTimer(){
            state.active=true;
            clearInterval(state.timer);
            state.timer = setInterval(()=>{
                state.time -= 0.1;
                const pct = (state.time/state.max)*100;
                elems.timerBar.style.width = pct+'%';
                if(pct<50) elems.timerBar.style.background='#FBBF24';
                if(pct<25) elems.timerBar.style.background='var(--danger-color)';
                if(state.time<=0) return endGame();
                // 장애물 충돌 체크
                document.querySelectorAll('.obstacle').forEach(obs=>{
                    if(obs.getBoundingClientRect && elems.player.getBoundingClientRect && !(obs.removed)){
                        const r1=elems.player.getBoundingClientRect(), r2=obs.getBoundingClientRect();
                        if(!(r1.right<r2.left||r1.left>r2.right||r1.bottom<r2.top||r1.top>r2.bottom)){
                            obs.removed=true; obs.remove(); state.time -= 2; 
                        }
                    }
                });
            },100);
        }
        function endGame(){
            clearInterval(state.timer);
            state.active=false;
            elems.sounds.fail.play();
            elems.player.textContent='😱';
            showModal('게임 오버', `최종 점수: ${state.score}`, [{text:'다시',cb:init}]);
        }
        function completeLevel(){
            clearInterval(state.timer);
            state.active=false;
            elems.sounds.success.play();
            state.score += 100 + Math.floor(state.time*10);
            state.level++;
            elems.player.textContent='😌';
            showModal('성공!', '다음 레벨로 준비!',[{text:'계속',cb:startPrep}]);
        }
        function handlePrepCollisions(){
            const coords = {fr: '냉장고', bd:'침대', td:'운동기구', dr:'문'};
            Object.entries({fridge:['fr',choices.food], bed:['bd',choices.condition], treadmill:['td',choices.condition], door:['dr',null]})
            .forEach(([id,[key,ch]])=>{
                const obj = document.getElementById(id);
                const pRect = elems.player.getBoundingClientRect();
                const oRect = obj.getBoundingClientRect();
                if(!(pRect.right<oRect.left||pRect.left>oRect.right||pRect.bottom<oRect.top||pRect.top>oRect.bottom)){
                    state.active=false;
                    if(id==='door'){
                        showModal('외출','나갈까?',[{text:'나가자',cb:startUrgency},{text:'취소',cls:'cancel',cb:()=>state.active=true}]);
                    } else {
                        const list = Object.entries(ch).map(([text,pen])=>({text,cb:()=>{ if(key==='fr') state.food=pen; else state.cond=pen; state.active=true;}}));
                        list.push({text:'건너뛰기',cls:'cancel',cb:()=>state.active=true});
                        showModal(coords[id],coords[id]+' 선택',[...list]);
                    }
                }
            });
        }
        function move(dx,dy){
            if(!state.active) return;
            const area=elems.gameArea||document.getElementById('game-area').getBoundingClientRect();
            let nx=state.pos.x+dx, ny=state.pos.y+dy;
            const pr=elems.player.getBoundingClientRect();
            if(nx<0) nx=0; if(ny<0) ny=0;
            if(nx+pr.width>area.width) nx=area.width-pr.width;
            if(ny+pr.height>area.height) ny=area.height-pr.height;
            state.pos.x=nx; state.pos.y=ny;
            elems.player.style.left=nx+'px'; elems.player.style.top=ny+'px';
            if(state.phase==='PREP') handlePrepCollisions();
            else if(state.phase==='URGENCY'){
                const to= document.getElementById('toilet');
                const r1=elems.player.getBoundingClientRect(), r2=to.getBoundingClientRect();
                if(!(r1.right<r2.left||r1.left>r2.right||r1.bottom<r2.top||r1.top>r2.bottom)) completeLevel();
            }
        }
        // 키보드 & 버튼
        const keys = {'ArrowUp':()=>move(0,-settings.speed),'ArrowDown':()=>move(0,settings.speed),'ArrowLeft':()=>move(-settings.speed,0),'ArrowRight':()=>move(settings.speed,0)};
        document.addEventListener('keydown',e=>{ if(keys[e.key]){e.preventDefault(); keys[e.key]();}});
        ['up','down','left','right'].forEach(dir=>{
            document.getElementById(dir).addEventListener('pointerdown',()=>keys['Arrow'+dir.charAt(0).toUpperCase()+dir.slice(1)]());
        });
        // 터치 스와이프
        let tStart={x:0,y:0};
        document.addEventListener('touchstart',e=>{ tStart.x=e.changedTouches[0].clientX; tStart.y=e.changedTouches[0].clientY; });
        document.addEventListener('touchend',e=>{
            const dx=e.changedTouches[0].clientX - tStart.x;
            const dy=e.changedTouches[0].clientY - tStart.y;
            if(Math.abs(dx)>Math.abs(dy)) dx>30?move(settings.speed,0): dx<-30?move(-settings.speed,0):null;
            else dy>30?move(0,settings.speed): dy<-30?move(0,-settings.speed):null;
        });
        // 초기화
        function init(){ state={phase:'PREP',level:1,score:0,pos:{},active:false}; initHome(); }
        function initHome(){ state.food=state.cond=0; showModal('게임 설명','음식·컨디션 조절 후 화장실까지 가세요!',[{text:'시작',cb:startPrep}]); }
        initHome();
    })();
    </script>
</body>
</html>
