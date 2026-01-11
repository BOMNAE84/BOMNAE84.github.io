title:
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>끝말잇기 대결 (모바일)</title>
    
    <!-- PWA 및 모바일 앱 설정 -->
    <meta name="apple-mobile-web-app-capable" content="yes">
    <meta name="apple-mobile-web-app-status-bar-style" content="black-translucent">
    <meta name="theme-color" content="#4f46e5">
    <link rel="apple-touch-icon" href="https://cdn-icons-png.flaticon.com/512/3976/3976625.png">
    
    <link rel="manifest" href="data:application/manifest+json;base64,eyJuYW1lIjoi64ql66eQ7J6h6riwIOuMgOqysCIsInNob3J0X25hbWUiOiLripXrp5DsnqHquLAiLCJzdGFydF91cmwiOiIuIiwiZGlzcGxheSI6InN0YW5kYWxvbmUiLCJiYWNrZ3JvdW5kX2NvbG9yIjoiI2ZkZjJmOCIsInRoZW1lX2NvbG9yIjoiIzRmNDZlNSIsImljb25zIjpbeyJzcmMiOiJodHRwczovL2NZG4taWNvbnMtcG5nLmZsYXRpY29uLmNvbS81MTIvMzk3Ni8zOTc2NjI1LnBuZyIsInNpemVzIjoiNTEyeDUxMiIsInR5cGUiOiJpbWFnZS9wbmcifV19">

    <script src="https://cdn.tailwindcss.com"></script>
    <link href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css" rel="stylesheet">
    <script src="https://cdn.jsdelivr.net/npm/canvas-confetti@1.6.0/dist/confetti.browser.min.js"></script>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Jua&family=Noto+Sans+KR:wght@500;700;900&display=swap');

        body {
            font-family: 'Noto Sans KR', sans-serif;
            background-color: #fdf2f8;
            /* 화면 스크롤 방지 (앱 느낌) */
            position: fixed;
            width: 100%;
            height: 100%;
            overflow: hidden;
            overscroll-behavior-y: none;
            -webkit-tap-highlight-color: transparent;
        }

        .font-jua { font-family: 'Jua', sans-serif; }

        .scrollbar-hide::-webkit-scrollbar { display: none; }
        .scrollbar-hide { -ms-overflow-style: none; scrollbar-width: none; }

        @keyframes popIn {
            0% { transform: scale(0.8); opacity: 0; }
            100% { transform: scale(1); opacity: 1; }
        }
        .chat-bubble { animation: popIn 0.3s cubic-bezier(0.175, 0.885, 0.32, 1.275) forwards; }

        .timer-bar { transition: width 1s linear; }
        
        .char-card { transition: all 0.2s; }
        .char-card:active { transform: scale(0.95); }
        .char-card.selected { border-color: #4f46e5; background-color: #eef2ff; transform: scale(1.02); }

        @keyframes ripple {
            0% { box-shadow: 0 0 0 0 rgba(239, 68, 68, 0.4); }
            70% { box-shadow: 0 0 0 15px rgba(239, 68, 68, 0); }
            100% { box-shadow: 0 0 0 0 rgba(239, 68, 68, 0); }
        }
        .mic-active {
            animation: ripple 1.5s infinite;
            background-color: #ef4444 !important;
            color: white !important;
            border-color: #ef4444 !important;
        }

        .turn-active {
            border: 3px solid #fbbf24;
            background-color: rgba(251, 191, 36, 0.1);
        }
    </style>
</head>
<body class="flex flex-col">

    <!-- 메인 컨테이너: 높이 100%를 꽉 채우고 flex-col로 배치 -->
    <div class="flex-1 flex flex-col w-full max-w-lg mx-auto bg-white shadow-2xl overflow-hidden h-full relative">
        
        <!-- 1. 헤더 (높이 최소화) -->
        <header class="bg-indigo-600 text-white p-2 shrink-0 shadow-md z-20">
            <div class="flex justify-between items-center mb-2 px-1">
                <div class="flex items-center gap-2">
                    <i class="fas fa-users text-xl"></i>
                    <h1 class="text-xl font-jua">끝말잇기 대결</h1>
                </div>
                <div class="flex gap-2">
                    <!-- 역대 최고 점수 -->
                    <div class="bg-indigo-800/60 px-2 py-0.5 rounded text-[10px] text-indigo-100 font-bold flex items-center">
                        최고: <span id="team-best-score" class="ml-1 text-white">0</span>
                    </div>
                    <button onclick="resetAll()" class="bg-indigo-500 hover:bg-indigo-700 p-1.5 rounded-full border border-indigo-400" title="리셋">
                        <i class="fas fa-redo text-sm"></i>
                    </button>
                </div>
            </div>
            
            <!-- 점수판 (컴팩트하게) -->
            <div class="grid grid-cols-3 gap-1 items-center bg-indigo-800/30 rounded-xl p-1.5">
                <div id="p1-score-box" class="flex flex-col items-center p-1 rounded-lg transition-all duration-300">
                    <div class="flex items-center gap-1 text-yellow-300 font-bold text-xs">
                        <i class="fas fa-user"></i> 1번
                    </div>
                    <div class="text-xl font-black font-jua"><span id="p1-score">0</span></div>
                </div>
                <div class="flex flex-col items-center justify-center border-x border-indigo-400/30 h-full">
                    <div class="text-indigo-200 font-bold text-[10px]">판 수</div>
                    <div class="text-lg font-black text-white"><span id="turn-count" class="text-yellow-300">1</span>/20</div>
                </div>
                <div id="p2-score-box" class="flex flex-col items-center p-1 rounded-lg transition-all duration-300">
                    <div class="flex items-center gap-1 text-green-300 font-bold text-xs">
                        <i class="fas fa-user"></i> 2번
                    </div>
                    <div class="text-xl font-black font-jua"><span id="p2-score">0</span></div>
                </div>
            </div>
        </header>

        <!-- 2. 게임 상태 바 (단어 표시) -->
        <div class="bg-orange-50 py-3 px-4 border-b border-orange-100 flex flex-col items-center justify-center shrink-0 z-10">
            <div class="bg-white px-6 py-2 rounded-xl shadow-sm border-2 border-indigo-200 flex items-center justify-center w-full">
                <div class="text-4xl font-jua text-indigo-900 tracking-wider truncate">
                    <span id="current-word-display" class="animate-pulse">시작!</span>
                </div>
            </div>
            <div class="w-full bg-gray-200 h-3 mt-2 rounded-full overflow-hidden border border-gray-300">
                <div id="timer-bar" class="bg-red-500 h-full timer-bar" style="width: 100%;"></div>
            </div>
        </div>

        <!-- 3. 채팅 영역 (유동적 높이 - 남는 공간 모두 차지) -->
        <div id="chat-container" class="flex-1 overflow-y-auto p-4 space-y-4 bg-white scrollbar-hide w-full">
            <div class="flex justify-center chat-bubble">
                <div class="bg-gray-100 text-gray-600 text-sm px-4 py-1.5 rounded-full font-bold">
                    두 분이서 즐겁게 게임하세요!
                </div>
            </div>
        </div>

        <!-- 4. 입력 탭 (높이 고정) -->
        <div class="flex border-t border-gray-200 shrink-0 h-10">
            <button onclick="setInputMode('keyboard')" id="tab-keyboard" class="flex-1 text-base font-black text-indigo-700 border-b-4 border-indigo-700 bg-white flex items-center justify-center gap-1 transition-colors">
                <i class="fas fa-keyboard text-lg"></i> 키보드
            </button>
            <button onclick="setInputMode('voice')" id="tab-voice" class="flex-1 text-base font-black text-gray-400 bg-gray-50 flex items-center justify-center gap-1 transition-colors">
                <i class="fas fa-microphone text-lg"></i> 말하기
            </button>
        </div>

        <!-- 5. 입력 영역 (컴팩트) -->
        <div class="p-3 bg-white shadow-[0_-5px_10px_rgba(0,0,0,0.02)] shrink-0 z-10">
            
            <!-- 키보드 모드 -->
            <div id="mode-keyboard" class="relative flex items-center">
                <input type="text" id="user-input" 
                    class="w-full bg-gray-50 text-gray-900 border-2 border-gray-300 rounded-full py-3 px-5 pr-14 focus:ring-2 focus:ring-indigo-500 focus:border-indigo-500 focus:bg-white transition outline-none text-xl font-bold placeholder-gray-400"
                    placeholder="단어 입력" autocomplete="off" disabled>
                <button id="send-btn" class="absolute right-1.5 bg-indigo-600 hover:bg-indigo-700 text-white w-10 h-10 rounded-full flex items-center justify-center transition shadow-md disabled:opacity-50 disabled:cursor-not-allowed" disabled>
                    <i class="fas fa-paper-plane text-base"></i>
                </button>
            </div>

            <!-- 음성 모드 -->
            <div id="mode-voice" class="hidden flex flex-col items-center justify-center gap-2 py-1">
                <div class="w-full h-10 flex items-center justify-center bg-indigo-50 rounded-lg border border-indigo-100 mb-1 px-2">
                    <div class="text-xl font-black text-indigo-900 text-center truncate w-full" id="voice-preview">...</div>
                </div>
                <div class="flex items-center gap-3">
                    <span class="text-xs font-bold text-gray-500">마이크를 켜면 유지됩니다</span>
                    <button id="mic-btn" onclick="toggleVoiceRecognition()" class="w-12 h-12 rounded-full bg-white text-indigo-600 border-2 border-indigo-200 hover:border-indigo-500 flex items-center justify-center text-2xl shadow-lg transition-all" disabled>
                        <i class="fas fa-microphone"></i>
                    </button>
                </div>
            </div>

            <p id="feedback-msg" class="text-center text-sm mt-1 h-5 font-bold truncate"></p>
        </div>

        <!-- 6. 하단 캐릭터 푸터 (높이 고정) -->
        <div id="character-footer" class="bg-orange-50 p-2 border-t border-orange-200 flex items-center justify-center gap-3 shrink-0 hidden h-16 safe-bottom">
            <div class="text-sm font-bold text-gray-500">순서</div>
            <div id="footer-char-icon" class="w-10 h-10 rounded-full bg-white flex items-center justify-center text-xl shadow-sm border-2 border-white">
                <i class="fas fa-user"></i>
            </div>
            <div class="flex flex-col items-start w-24">
                <span id="footer-player-label" class="text-[10px] text-indigo-600 font-extrabold leading-none mb-0.5">1번 선수</span>
                <span id="footer-char-name" class="text-lg font-jua text-gray-900 leading-none truncate w-full">선택 안함</span>
            </div>
            <div class="text-sm font-bold text-gray-500">차례!</div>
        </div>


        <!-- 캐릭터 선택 모달 (전체화면 오버레이) -->
        <div id="character-selection-modal" class="absolute inset-0 bg-white z-50 flex flex-col p-4 animate-[popIn_0.3s_ease-out] overflow-y-auto">
            <div class="text-center mb-4 mt-2 shrink-0">
                <h2 id="selection-title" class="text-3xl font-jua text-indigo-900 mb-1">1번 선수 선택</h2>
                <p class="text-gray-500 text-sm font-bold">마음에 드는 동물을 골라주세요</p>
            </div>

            <!-- 찬스 옵션 -->
            <div id="chance-option-container" class="w-full bg-yellow-50 p-3 rounded-xl border-2 border-yellow-300 shadow-sm mb-4 flex items-center gap-3 shrink-0">
                <input type="checkbox" id="chance-mode-toggle" class="w-6 h-6 text-indigo-600 rounded border-gray-300 focus:ring-indigo-500">
                <label for="chance-mode-toggle" class="flex-1">
                    <div class="text-lg font-black text-gray-800">점수 뺏기 찬스!</div>
                    <div class="text-xs text-gray-600 font-bold">15번째 판부터 8초 제한 & 점수 뺏기</div>
                </label>
            </div>

            <!-- 캐릭터 그리드 -->
            <div class="grid grid-cols-2 gap-3 w-full pb-8">
                <div onclick="selectCharacter('tiger')" class="char-card bg-white p-3 rounded-2xl border-2 border-gray-100 flex flex-col items-center gap-1 shadow-sm">
                    <div class="w-12 h-12 rounded-full bg-orange-100 flex items-center justify-center text-orange-600 text-2xl"><i class="fas fa-cat"></i></div>
                    <span class="font-bold text-base text-gray-800">호랑이</span>
                </div>
                <div onclick="selectCharacter('puppy')" class="char-card bg-white p-3 rounded-2xl border-2 border-gray-100 flex flex-col items-center gap-1 shadow-sm">
                    <div class="w-12 h-12 rounded-full bg-yellow-100 flex items-center justify-center text-yellow-700 text-2xl"><i class="fas fa-dog"></i></div>
                    <span class="font-bold text-base text-gray-800">강아지</span>
                </div>
                <div onclick="selectCharacter('bird')" class="char-card bg-white p-3 rounded-2xl border-2 border-gray-100 flex flex-col items-center gap-1 shadow-sm">
                    <div class="w-12 h-12 rounded-full bg-slate-200 flex items-center justify-center text-slate-700 text-2xl"><i class="fas fa-dove"></i></div>
                    <span class="font-bold text-base text-gray-800">까치</span>
                </div>
                <div onclick="selectCharacter('pig')" class="char-card bg-white p-3 rounded-2xl border-2 border-gray-100 flex flex-col items-center gap-1 shadow-sm">
                    <div class="w-12 h-12 rounded-full bg-pink-100 flex items-center justify-center text-pink-500 text-2xl"><i class="fas fa-piggy-bank"></i></div>
                    <span class="font-bold text-base text-gray-800">돼지</span>
                </div>
                 <div onclick="selectCharacter('dragon')" class="char-card bg-white p-3 rounded-2xl border-2 border-gray-100 flex flex-col items-center gap-1 shadow-sm">
                    <div class="w-12 h-12 rounded-full bg-teal-100 flex items-center justify-center text-teal-600 text-2xl"><i class="fas fa-dragon"></i></div>
                    <span class="font-bold text-base text-gray-800">청룡</span>
                </div>
                <div onclick="selectCharacter('fish')" class="char-card bg-white p-3 rounded-2xl border-2 border-gray-100 flex flex-col items-center gap-1 shadow-sm">
                    <div class="w-12 h-12 rounded-full bg-red-100 flex items-center justify-center text-red-500 text-2xl"><i class="fas fa-fish"></i></div>
                    <span class="font-bold text-base text-gray-800">금붕어</span>
                </div>
            </div>
        </div>

        <!-- 결과 모달 -->
        <div id="game-over-modal" class="absolute inset-0 bg-black/80 flex items-center justify-center z-50 hidden backdrop-blur-sm px-4">
            <div class="bg-white p-6 rounded-3xl shadow-2xl text-center w-full max-w-sm animate-[popIn_0.4s_ease-out]">
                <h2 id="winner-title" class="text-4xl font-jua text-indigo-900 mb-2">경기 종료!</h2>
                <div id="new-record-badge" class="hidden bg-yellow-400 text-yellow-900 font-black px-3 py-1 rounded-full mb-3 inline-block text-sm shadow-sm animate-pulse">🏆 신기록! 🏆</div>
                <div class="text-gray-500 font-bold mb-4 text-lg">합산: <span id="total-score-display" class="text-indigo-600 font-black text-2xl">0</span>점</div>
                <div class="flex justify-center gap-3 mb-6">
                    <div class="bg-yellow-50 p-3 rounded-xl w-1/2 border border-yellow-200">
                        <div class="text-sm font-bold text-gray-600">1번</div>
                        <div id="final-p1-score" class="text-2xl font-black text-indigo-700">0</div>
                    </div>
                    <div class="bg-green-50 p-3 rounded-xl w-1/2 border border-green-200">
                        <div class="text-sm font-bold text-gray-600">2번</div>
                        <div id="final-p2-score" class="text-2xl font-black text-indigo-700">0</div>
                    </div>
                </div>
                <button onclick="resetAll()" class="w-full bg-indigo-600 text-white py-4 rounded-xl font-black text-xl hover:bg-indigo-700 shadow-lg">다시 하기</button>
            </div>
        </div>
    </div>

    <script>
        // --- 데이터 ---
        const historyDictionary = new Set(["고조선","삼국시대","신라","고구려","백제","발해","고려","조선","임진왜란","일제강점기","광복","625전쟁","세종대왕","이순신","안중근","김구","유관순","독도","거북선","훈민정음","직지심체요절","팔만대장경","경복궁","불국사","석굴암","첨성대","다보탑","석가탑","수원화성","창덕궁","종묘","해인사","고인돌","빗살무늬토기","비파형동검","세형동검","청동기","철기","구석기","신석기","경주","서울","평양","개성","한양","웅진","사비","청해진","장보고","대조영","왕건","이성계","이방원","세조","연산군","광해군","정조","영조","대원군","명성황후","고종","순종","정약용","이황","이이","신사임당","김유신","강감찬","을지문덕","최무선","서희","윤관","로마","이집트","그리스","중국","일본","미국","프랑스","영국","독일","러시아","나폴레옹","히틀러","링컨","워싱턴","간디","처칠","루즈벨트","스탈린","진시황","공자","맹자","석가모니","예수","마호메트","십자군","르네상스","종교개혁","산업혁명","시민혁명","프랑스혁명","미국독립혁명","남북전쟁","1차세계대전","2차세계대전","냉전","유엔","나토","석기시대","중세","근대","현대","왕조","제국","식민지","독립","혁명","전쟁","화랑도","골품제"]);
        const characters = {
            'tiger': { name: '호랑이', icon: 'fa-cat', color: 'bg-orange-100', text: 'text-orange-800', border: 'border-orange-300' },
            'puppy': { name: '강아지', icon: 'fa-dog', color: 'bg-yellow-100', text: 'text-yellow-800', border: 'border-yellow-300' },
            'bird': { name: '까치', icon: 'fa-dove', color: 'bg-slate-200', text: 'text-slate-800', border: 'border-slate-400' },
            'pig': { name: '돼지', icon: 'fa-piggy-bank', color: 'bg-pink-100', text: 'text-pink-800', border: 'border-pink-300' },
            'dragon': { name: '청룡', icon: 'fa-dragon', color: 'bg-teal-100', text: 'text-teal-800', border: 'border-teal-300' },
            'fish': { name: '금붕어', icon: 'fa-fish', color: 'bg-red-100', text: 'text-red-800', border: 'border-red-300' }
        };

        // --- 변수 ---
        let currentWord="", usedWords=new Set(), p1Char=null, p2Char=null, p1Score=0, p2Score=0, currentPlayer=1, selectionPhase=1, turnCount=0, isChanceMode=false, isGameOver=false, timerInterval, currentGameTime=15, currentTimeLeft=15, inputMode='keyboard', recognition=null, isRecording=false, isProcessing=false;
        let teamHighScore = parseInt(localStorage.getItem('teamHighScore') || '0');
        const MAX_TURNS=20, CHANCE_GAME_TIME=8, DEFAULT_GAME_TIME=15;

        // --- DOM ---
        const userInput=document.getElementById('user-input'), sendBtn=document.getElementById('send-btn'), chatContainer=document.getElementById('chat-container'), currentWordDisplay=document.getElementById('current-word-display'), turnCountDisplay=document.getElementById('turn-count'), timerBar=document.getElementById('timer-bar'), feedbackMsg=document.getElementById('feedback-msg');
        const p1ScoreDisplay=document.getElementById('p1-score'), p2ScoreDisplay=document.getElementById('p2-score'), p1ScoreBox=document.getElementById('p1-score-box'), p2ScoreBox=document.getElementById('p2-score-box'), characterFooter=document.getElementById('character-footer'), footerCharIcon=document.getElementById('footer-char-icon'), footerCharName=document.getElementById('footer-char-name'), footerPlayerLabel=document.getElementById('footer-player-label'), teamBestScoreDisplay=document.getElementById('team-best-score');
        const gameOverModal=document.getElementById('game-over-modal'), charSelectionModal=document.getElementById('character-selection-modal'), selectionTitle=document.getElementById('selection-title'), chanceOptionContainer=document.getElementById('chance-option-container'), chanceModeToggle=document.getElementById('chance-mode-toggle'), winnerTitle=document.getElementById('winner-title'), finalP1Score=document.getElementById('final-p1-score'), finalP2Score=document.getElementById('final-p2-score'), totalScoreDisplay=document.getElementById('total-score-display'), newRecordBadge=document.getElementById('new-record-badge');
        const tabKeyboard=document.getElementById('tab-keyboard'), tabVoice=document.getElementById('tab-voice'), modeKeyboardDiv=document.getElementById('mode-keyboard'), modeVoiceDiv=document.getElementById('mode-voice'), micBtn=document.getElementById('mic-btn'), voiceStatus=document.getElementById('voice-status'), voicePreview=document.getElementById('voice-preview');

        function resetAll(){ p1Char=null; p2Char=null; selectionPhase=1; teamBestScoreDisplay.textContent=teamHighScore; selectionTitle.innerHTML="1번 선수 선택"; chanceOptionContainer.style.display="flex"; chanceModeToggle.checked=false; showCharacterSelection(); }
        function showCharacterSelection(){ isGameOver=true; clearInterval(timerInterval); if(recognition&&isRecording) recognition.stop(); gameOverModal.classList.add('hidden'); charSelectionModal.classList.remove('hidden'); }
        function selectCharacter(id){ const sel=characters[id]; if(selectionPhase===1){ p1Char=sel; isChanceMode=chanceModeToggle.checked; selectionPhase=2; selectionTitle.innerHTML="2번 선수 선택"; chanceOptionContainer.style.display="none"; charSelectionModal.scrollTop=0; } else { p2Char=sel; charSelectionModal.classList.add('hidden'); initGame(); } }
        function initGame(){ currentWord=""; usedWords.clear(); p1Score=0; p2Score=0; currentPlayer=1; turnCount=1; isGameOver=false; updateScoreBoard(); turnCountDisplay.textContent=turnCount; teamBestScoreDisplay.textContent=teamHighScore; chatContainer.innerHTML=''; const startWords=["끝말잇기","대한민국","보물지도","무지개","운동장"]; updateCurrentWord(startWords[Math.floor(Math.random()*startWords.length)],false); addSystemMessage(`'${currentWordDisplay.textContent}'(으)로 시작!`); characterFooter.classList.remove('hidden'); updateFooterUI(); userInput.disabled=false; sendBtn.disabled=false; micBtn.disabled=false; if(inputMode==='keyboard') userInput.focus(); if(inputMode==='voice') try{recognition.start()}catch(e){}; resetTimer(); }
        
        function handleTurn(){ if(isGameOver)return; const input=userInput.value.trim(); const fail=(m)=>{showFeedback(m,true);isProcessing=false;if(inputMode==='voice'&&!isRecording)try{recognition.start()}catch(e){}}; if(!input){isProcessing=false;return;} if(input.length<2){fail("두 글자 이상!");return;} if(!/^[가-힣]+$/.test(input)){fail("한글만!");return;} const lastChar=currentWord.slice(-1), firstChar=input[0], initialSound=getInitialSound(lastChar); if(firstChar!==lastChar && firstChar!==initialSound){showFeedback(`'${lastChar}'(으)로 시작!`,true);isProcessing=false;if(inputMode==='voice')setTimeout(()=>{if(!isRecording)try{recognition.start()}catch(e){}},1000);return;} if(usedWords.has(input)){fail("이미 쓴 단어!");return;} 
        let turnScore=10, feedbackText="", isSteal=false; if(historyDictionary.has(input)){turnScore+=5;feedbackText="보너스!";} if(isChanceMode&&turnCount>=15){isSteal=true;feedbackText=feedbackText?"보너스 & 뺏기!":"점수 뺏기!";} if(feedbackText)showFeedback(feedbackText,false); else showFeedback("",false); userInput.value=""; voicePreview.textContent="..."; addMessage(input,currentPlayer); updateCurrentWord(input,true);
        if(currentPlayer===1){p1Score+=turnScore; if(isSteal){const s=Math.min(p2Score,turnScore);p2Score-=s;p1Score+=s;}} else {p2Score+=turnScore; if(isSteal){const s=Math.min(p1Score,turnScore);p1Score-=s;p2Score+=s;}} updateScoreBoard();
        if(turnCount>=MAX_TURNS){handleGameOver();return;} turnCount++; turnCountDisplay.textContent=turnCount; currentPlayer=currentPlayer===1?2:1; updateFooterUI(); resetTimer(); isProcessing=false; if(inputMode==='keyboard')userInput.focus(); }

        function handleGameOver(reason){ isGameOver=true; clearInterval(timerInterval); userInput.disabled=true; sendBtn.disabled=true; micBtn.disabled=true; if(recognition)recognition.stop(); const total=p1Score+p2Score; finalP1Score.textContent=p1Score; finalP2Score.textContent=p2Score; totalScoreDisplay.textContent=total; let isNew=false; if(total>teamHighScore){teamHighScore=total;localStorage.setItem('teamHighScore',teamHighScore);isNew=true;newRecordBadge.classList.remove('hidden');}else{newRecordBadge.classList.add('hidden');}
        if(reason==="timeout"){winnerTitle.textContent=`${currentPlayer===1?'2번':'1번'} 선수 승리!`;winnerTitle.className="text-4xl font-jua text-red-600 mb-2";} else { if(isNew){winnerTitle.textContent="최고 기록 경신!";winnerTitle.className="text-3xl font-jua text-indigo-900 mb-2";triggerFireworks();} else { if(p1Score>p2Score){winnerTitle.textContent="1번 선수 우승!";triggerFireworks();} else if(p2Score>p1Score){winnerTitle.textContent="2번 선수 우승!";triggerFireworks();} else winnerTitle.textContent="무승부!"; winnerTitle.className="text-4xl font-jua text-indigo-900 mb-2";} } setTimeout(()=>gameOverModal.classList.remove('hidden'),500); }

        function updateScoreBoard(){ p1ScoreDisplay.textContent=p1Score; p2ScoreDisplay.textContent=p2Score; }
        function updateFooterUI(){ const char=currentPlayer===1?p1Char:p2Char, label=currentPlayer===1?"1번 선수":"2번 선수", bg=currentPlayer===1?"bg-yellow-100":"bg-green-100", border=currentPlayer===1?"border-yellow-300":"border-green-300", text=currentPlayer===1?"text-yellow-800":"text-green-800"; characterFooter.className=`${bg} p-2 border-t-4 ${border} flex items-center justify-center gap-3 shrink-0 hidden h-16 safe-bottom transition-colors duration-300 shadow-[0_-5px_20px_rgba(0,0,0,0.1)]`; footerCharIcon.innerHTML=`<i class="fas ${char.icon}"></i>`; footerCharIcon.className=`w-10 h-10 rounded-full bg-white flex items-center justify-center text-xl shadow-md border-2 ${char.border} ${char.text}`; footerPlayerLabel.textContent=label; footerPlayerLabel.className=`text-[10px] font-black leading-none mb-0.5 ${text}`; footerCharName.textContent=char.name; footerCharName.className=`text-lg font-jua ${char.text} leading-none truncate w-24`; if(currentPlayer===1){p1ScoreBox.classList.add('turn-active');p2ScoreBox.classList.remove('turn-active');}else{p2ScoreBox.classList.add('turn-active');p1ScoreBox.classList.remove('turn-active');} }
        function getInitialSound(c){ const m={'라':'나','락':'낙','란':'난','랄':'날','람':'남','랍':'납','랑':'낭','래':'내','랭':'냉','냑':'약','략':'약','냥':'양','량':'양','녀':'여','려':'여','녁':'역','력':'역','년':'연','련':'연','녈':'열','렬':'열','념':'염','렴':'염','녕':'영','령':'영','녜':'예','례':'예','로':'노','록':'녹','론':'논','롱':'농','뢰':'뇌','료':'요','룡':'용','루':'누','류':'유','륙':'육','륜':'윤','률':'율','륭':'융','르':'느','리':'이','린':'인','림':'임','립':'입'}; return m[c]||c; }
        function updateCurrentWord(w,h){ currentWord=w; if(h)usedWords.add(w); currentWordDisplay.textContent=w; currentWordDisplay.classList.remove('animate-pulse'); void currentWordDisplay.offsetWidth; currentWordDisplay.classList.add('animate-pulse'); }
        function addSystemMessage(t){ const d=document.createElement('div'); d.className='flex justify-center chat-bubble my-2'; d.innerHTML=`<div class="bg-gray-100 text-gray-700 text-sm px-4 py-1 rounded-full font-bold border border-gray-200">${t}</div>`; chatContainer.appendChild(d); chatContainer.scrollTop=chatContainer.scrollHeight; }
        function addMessage(t,p){ const d=document.createElement('div'); d.className=`flex ${p===1?'justify-end':'justify-start'} chat-bubble items-end gap-2`; const c=p===1?p1Char:p2Char; const bc=p===1?"bg-white border-2 border-yellow-400 text-yellow-900":"bg-white border-2 border-green-400 text-green-900"; const rc=p===1?"rounded-tr-none":"rounded-tl-none"; const ic=document.createElement('div'); ic.className=`w-8 h-8 rounded-full flex items-center justify-center text-sm shadow-sm border bg-white ${c.text} ${c.border} shrink-0 mb-1`; ic.innerHTML=`<i class="fas ${c.icon}"></i>`; const b=document.createElement('div'); b.className=`max-w-[75%] px-4 py-2 rounded-2xl shadow-sm text-base font-bold ${bc} ${rc}`; b.textContent=t; if(p===2){d.appendChild(ic);d.appendChild(b);}else{d.appendChild(b);d.appendChild(ic);} chatContainer.appendChild(d); chatContainer.scrollTop=chatContainer.scrollHeight; }
        function showFeedback(m,e){ feedbackMsg.textContent=m; if(e){feedbackMsg.className="text-center text-sm mt-1 h-5 font-bold text-red-500 animate-bounce";}else{feedbackMsg.className="text-center text-sm mt-1 h-5 font-bold text-blue-600 animate-pulse";} }
        function startTimer(){ clearInterval(timerInterval); if(turnCount>=15&&isChanceMode){currentGameTime=CHANCE_GAME_TIME;timerBar.parentElement.className="w-full bg-red-100 h-3 mt-2 rounded-full overflow-hidden border border-red-400 shadow-inner";}else{currentGameTime=DEFAULT_GAME_TIME;timerBar.parentElement.className="w-full bg-gray-200 h-3 mt-2 rounded-full overflow-hidden border border-gray-300";} currentTimeLeft=currentGameTime; timerBar.style.width='100%'; timerInterval=setInterval(()=>{if(isGameOver){clearInterval(timerInterval);return;} currentTimeLeft-=0.1; let p=(currentTimeLeft/currentGameTime)*100; timerBar.style.width=`${p}%`; if(currentTimeLeft<=0){clearInterval(timerInterval);handleGameOver("timeout");}},100); }
        function resetTimer(){ startTimer(); }
        function setInputMode(m){ inputMode=m; if(m==='keyboard'){modeKeyboardDiv.classList.remove('hidden');modeVoiceDiv.classList.add('hidden');tabKeyboard.className="flex-1 text-base font-black text-indigo-700 border-b-4 border-indigo-700 bg-white flex items-center justify-center gap-1 transition-colors";tabVoice.className="flex-1 text-base font-black text-gray-400 bg-gray-50 hover:text-gray-600 transition-colors flex items-center justify-center gap-1";if(recognition)recognition.stop();userInput.focus();}else{modeKeyboardDiv.classList.add('hidden');modeVoiceDiv.classList.remove('hidden');modeVoiceDiv.classList.add('flex');tabVoice.className="flex-1 text-base font-black text-indigo-700 border-b-4 border-indigo-700 bg-white flex items-center justify-center gap-1 transition-colors";tabKeyboard.className="flex-1 text-base font-black text-gray-400 bg-gray-50 hover:text-gray-600 transition-colors flex items-center justify-center gap-1";if(!isRecording)try{recognition.start()}catch(e){}} }
        function toggleVoiceRecognition(){ if(!recognition)return alert("음성 인식 불가"); if(isRecording)recognition.stop(); else try{recognition.start()}catch(e){} }
        if('SpeechRecognition'in window||'webkitSpeechRecognition'in window){ const SR=window.SpeechRecognition||window.webkitSpeechRecognition; recognition=new SR(); recognition.lang='ko-KR'; recognition.continuous=false; recognition.interimResults=false; recognition.onstart=()=>{isRecording=true;micBtn.classList.add('mic-active');voicePreview.textContent="듣고 있어요...";}; recognition.onend=()=>{isRecording=false;micBtn.classList.remove('mic-active');if(inputMode==='voice'&&!isGameOver&&!isProcessing)setTimeout(()=>{if(inputMode==='voice'&&!isGameOver&&!isProcessing&&!isRecording)try{recognition.start()}catch(e){}},300);else voicePreview.textContent="...";}; recognition.onresult=(e)=>{const t=e.results[0][0].transcript;voicePreview.textContent=t;userInput.value=t.replace(/\s/g,'');isProcessing=true;setTimeout(()=>handleTurn(),500);}; } else { tabVoice.style.display='none'; }
        function triggerFireworks(){ const d=5000, e=Date.now()+d, def={startVelocity:30,spread:360,ticks:60,zIndex:60,scalar:1.5}; const i=setInterval(()=>{const t=e-Date.now();if(t<=0)return clearInterval(i);const p=50*(t/d);confetti(Object.assign({},def,{particleCount:p,origin:{x:Math.random(),y:Math.random()-0.2}}));},250); }
        
        userInput.addEventListener('keypress',(e)=>{if(e.key==='Enter')handleTurn()}); sendBtn.addEventListener('click',handleTurn);
        window.onload=resetAll;
    </script>
</body>
</html>
