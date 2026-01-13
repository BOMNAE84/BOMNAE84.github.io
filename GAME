<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>끝말잇기 한판 대결</title>
    
    <!-- PWA 및 모바일 앱 설정 -->
    <meta name="apple-mobile-web-app-capable" content="yes">
    <meta name="apple-mobile-web-app-status-bar-style" content="black-translucent">
    <meta name="theme-color" content="#4f46e5">
    <link rel="apple-touch-icon" href="https://cdn-icons-png.flaticon.com/512/3976/3976625.png">
    
    <!-- 매니페스트 (Data URI로 인라인 포함) -->
    <link rel="manifest" href="data:application/manifest+json;base64,eyJuYW1lIjoi64ql66eQ7J6h6riwIOuMgOqysCIsInNob3J0X25hbWUiOiLripXrp5DsnqHquLAiLCJzdGFydF91cmwiOiIuIiwiZGlzcGxheSI6InN0YW5kYWxvbmUiLCJiYWNrZ3JvdW5kX2NvbG9yIjoiI2ZkZjJmOCIsInRoZW1lX2NvbG9yIjoiIzRmNDZlNSIsImljb25zIjpbeyJzcmMiOiJodHRwczovL2NZG4taWNvbnMtcG5nLmZsYXRpY29uLmNvbS81MTIvMzk3Ni8zOTc2NjI1LnBuZyIsInNpemVzIjoiNTEyeDUxMiIsInR5cGUiOiJpbWFnZS9wbmcifV19">

    <script src="https://cdn.tailwindcss.com"></script>
    <link href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css" rel="stylesheet">
    <script src="https://cdn.jsdelivr.net/npm/canvas-confetti@1.6.0/dist/confetti.browser.min.js"></script>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Jua&family=Noto+Sans+KR:wght@500;700;900&display=swap');

        body {
            font-family: 'Noto Sans KR', sans-serif;
            background-color: #fdf2f8; /* 따뜻한 배경색 */
            overscroll-behavior-y: none; /* 스크롤 바운스 방지 */
            -webkit-tap-highlight-color: transparent; /* 터치 하이라이트 제거 */
        }

        .font-jua {
            font-family: 'Jua', sans-serif;
        }

        .scrollbar-hide::-webkit-scrollbar {
            display: none;
        }
        .scrollbar-hide {
            -ms-overflow-style: none;
            scrollbar-width: none;
        }

        @keyframes popIn {
            0% { transform: scale(0.8); opacity: 0; }
            100% { transform: scale(1); opacity: 1; }
        }
        .chat-bubble {
            animation: popIn 0.3s cubic-bezier(0.175, 0.885, 0.32, 1.275) forwards;
        }

        .timer-bar {
            transition: width 1s linear;
        }
        
        .char-card {
            transition: all 0.3s cubic-bezier(0.4, 0, 0.2, 1);
        }
        .char-card:hover {
            transform: translateY(-5px);
            box-shadow: 0 10px 25px -5px rgba(0, 0, 0, 0.1), 0 8px 10px -6px rgba(0, 0, 0, 0.1);
        }
        .char-card.selected {
            border-color: #4f46e5;
            background-color: #eef2ff;
            transform: scale(1.05);
        }

        @keyframes ripple {
            0% { box-shadow: 0 0 0 0 rgba(239, 68, 68, 0.4); }
            70% { box-shadow: 0 0 0 20px rgba(239, 68, 68, 0); }
            100% { box-shadow: 0 0 0 0 rgba(239, 68, 68, 0); }
        }
        .mic-active {
            animation: ripple 1.5s infinite;
            background-color: #ef4444 !important;
            color: white !important;
            border-color: #ef4444 !important;
        }

        /* 현재 턴 강조 효과 */
        .turn-active {
            border: 4px solid #fbbf24; /* 노란색 테두리 */
            background-color: rgba(251, 191, 36, 0.1);
            transform: scale(1.05);
        }
    </style>
</head>
<body class="h-screen flex flex-col items-center justify-center bg-orange-50">

    <!-- 게임 컨테이너 -->
    <div class="w-full max-w-md h-full md:h-[100%] bg-white md:rounded-3xl shadow-2xl flex flex-col overflow-hidden relative">
        
        <!-- 헤더 (점수판) -->
        <header class="bg-indigo-600 text-white p-4 shrink-0 shadow-md safe-top">
            <!-- 상단 타이틀 & 설정 -->
            <div class="flex justify-between items-center mb-4">
                <div class="flex items-center gap-2">
                    <i class="fas fa-users text-3xl"></i>
                    <h1 class="text-3xl font-jua">끝말잇기 대결</h1>
                </div>
                <button onclick="resetAll()" class="bg-indigo-500 hover:bg-indigo-700 p-2 rounded-full border border-indigo-400" title="처음부터 다시">
                    <i class="fas fa-redo text-xl"></i>
                </button>
            </div>
            
            <!-- 2인용 점수판 -->
            <div class="grid grid-cols-3 gap-2 items-center bg-indigo-800/30 rounded-2xl p-2">
                <!-- 1번 선수 점수 -->
                <div id="p1-score-box" class="flex flex-col items-center p-2 rounded-xl transition-all duration-300">
                    <div class="flex items-center gap-1 text-yellow-300 font-bold text-lg mb-1">
                        <i class="fas fa-user"></i> 1번 선수
                    </div>
                    <div class="text-3xl font-black font-jua"><span id="p1-score">0</span>점</div>
                </div>

                <!-- 진행 상황 (중앙) -->
                <div class="flex flex-col items-center justify-center border-x border-indigo-400/30 h-full">
                    <div class="text-indigo-200 font-bold text-sm mb-1">판 수</div>
                    <div class="text-2xl font-black text-white"><span id="turn-count" class="text-yellow-300">1</span>/20</div>
                    <!-- 역대 최고 점수 표시 -->
                    <div class="mt-1 bg-indigo-900/50 px-2 py-0.5 rounded text-xs text-indigo-100 font-bold">
                        최고 합산: <span id="team-best-score">0</span>
                    </div>
                </div>

                <!-- 2번 선수 점수 -->
                <div id="p2-score-box" class="flex flex-col items-center p-2 rounded-xl transition-all duration-300">
                    <div class="flex items-center gap-1 text-green-300 font-bold text-lg mb-1">
                        <i class="fas fa-user"></i> 2번 선수
                    </div>
                    <div class="text-3xl font-black font-jua"><span id="p2-score">0</span>점</div>
                </div>
            </div>
        </header>

        <!-- 게임 상태 바 (단어 표시) -->
        <div class="bg-orange-50 py-6 px-4 border-b border-orange-100 flex flex-col items-center justify-center sticky top-0 z-0 shrink-0">
            <p class="text-gray-500 text-lg font-bold mb-1">이어갈 단어</p>
            
            <!-- 배경색이 있는 단어 박스 -->
            <div class="bg-white px-10 py-4 rounded-2xl shadow-md border-4 border-indigo-200 my-2">
                <!-- 글자 크기 증가: text-6xl md:text-7xl -->
                <div class="text-6xl md:text-7xl font-jua text-indigo-900 tracking-wider flex items-center gap-2">
                    <span id="current-word-display" class="animate-pulse">시작!</span>
                </div>
            </div>

            <div class="w-full bg-gray-200 h-5 mt-3 rounded-full overflow-hidden border border-gray-300">
                <div id="timer-bar" class="bg-red-500 h-full timer-bar" style="width: 100%;"></div>
            </div>
        </div>

        <!-- 채팅 영역 -->
        <div id="chat-container" class="flex-1 overflow-y-auto p-5 space-y-6 bg-white scrollbar-hide relative">
            <div class="flex justify-center chat-bubble">
                <div class="bg-gray-100 text-gray-600 text-lg px-6 py-2 rounded-full font-bold">
                    두 분이서 즐겁게 게임하세요!
                </div>
            </div>
        </div>

        <!-- 입력 방식 탭 -->
        <div class="flex border-t-2 border-gray-100 shrink-0 mt-4">
            <button onclick="setInputMode('keyboard')" id="tab-keyboard" class="flex-1 py-4 text-xl font-black text-indigo-700 border-b-4 border-indigo-700 bg-white transition-colors gap-2 flex items-center justify-center touch-manipulation">
                <i class="fas fa-keyboard text-2xl"></i> 키보드
            </button>
            <button onclick="setInputMode('voice')" id="tab-voice" class="flex-1 py-4 text-xl font-black text-gray-400 bg-gray-50 hover:text-gray-600 transition-colors gap-2 flex items-center justify-center touch-manipulation">
                <i class="fas fa-microphone text-2xl"></i> 말하기
            </button>
        </div>

        <!-- 입력 영역 -->
        <div class="pt-10 pb-6 px-6 bg-white shadow-[0_-5px_20px_rgba(0,0,0,0.05)] shrink-0 safe-bottom">
            
            <!-- 키보드 모드 -->
            <div id="mode-keyboard" class="relative">
                <!-- 입력 글씨 크기 조정: text-3xl (이전 요청 반영) -> text-2xl (이번 요청으로 살짝 축소) -->
                <input type="text" id="user-input" 
                    class="w-full bg-gray-100 text-gray-900 border-4 border-gray-300 rounded-full py-5 px-8 pr-20 focus:ring-4 focus:ring-indigo-500 focus:border-indigo-500 focus:bg-white transition outline-none text-2xl font-bold placeholder-gray-400"
                    placeholder="단어 입력" autocomplete="off" disabled>
                <button id="send-btn" class="absolute right-3 top-3 bg-indigo-600 hover:bg-indigo-700 text-white w-16 h-16 rounded-full flex items-center justify-center transition shadow-lg disabled:opacity-50 disabled:cursor-not-allowed touch-manipulation" disabled>
                    <i class="fas fa-paper-plane text-2xl"></i>
                </button>
            </div>

            <!-- 음성 모드 -->
            <div id="mode-voice" class="hidden flex flex-col items-center justify-center gap-6 py-2">
                <div class="text-gray-600 text-xl font-bold" id="voice-status">마이크를 켜고 말씀하세요</div>
                
                <!-- 음성 미리보기 (가려지지 않게 위로) -->
                <div class="w-full min-h-[4.5rem] flex items-center justify-center bg-indigo-50 rounded-2xl border-2 border-indigo-100 p-2 mb-2">
                    <div class="text-4xl font-black text-indigo-900 text-center break-keep leading-tight" id="voice-preview"></div>
                </div>

                <button id="mic-btn" onclick="toggleVoiceRecognition()" class="w-24 h-24 rounded-full bg-white text-indigo-600 border-4 border-indigo-200 hover:border-indigo-500 flex items-center justify-center text-5xl shadow-xl transition-all transform hover:scale-105 touch-manipulation" disabled>
                    <i class="fas fa-microphone"></i>
                </button>
            </div>

            <p id="feedback-msg" class="text-center text-2xl mt-4 h-8 font-black"></p>
        </div>

        <!-- 하단 캐릭터 푸터 (현재 순서 표시) -->
        <div id="character-footer" class="bg-orange-100 p-4 border-t-2 border-orange-200 flex items-center justify-center gap-4 shrink-0 hidden shadow-[0_-5px_15px_rgba(0,0,0,0.05)] transition-colors duration-500 safe-bottom">
            <div class="text-xl font-bold text-gray-600 mr-2">지금은</div>
            <div id="footer-char-icon" class="w-16 h-16 rounded-full bg-white flex items-center justify-center text-4xl shadow-md border-4 border-white transition-transform duration-300">
                <i class="fas fa-user"></i>
            </div>
            <div class="flex flex-col items-start">
                <span id="footer-player-label" class="text-sm text-indigo-600 font-extrabold mb-0">1번 선수</span>
                <span id="footer-char-name" class="text-3xl font-jua text-gray-900 leading-none">선택 안함</span>
            </div>
            <div class="text-xl font-bold text-gray-600 ml-2">차례!</div>
        </div>


        <!-- 캐릭터 선택 모달 -->
        <div id="character-selection-modal" class="absolute inset-0 bg-white z-50 flex flex-col items-center justify-start p-6 animate-[popIn_0.3s_ease-out] overflow-y-auto">
            <div class="text-center mb-6 mt-4">
                <h2 id="selection-title" class="text-4xl font-jua text-indigo-900 mb-2">1번 선수<br>캐릭터 선택</h2>
                <p class="text-gray-500 text-lg font-bold">마음에 드는 동물을 골라주세요</p>
            </div>

            <!-- 옵션 선택: 찬스 모드 (처음에만 표시) -->
            <div id="chance-option-container" class="w-full max-w-md bg-yellow-50 p-5 rounded-3xl border-4 border-yellow-300 shadow-md mb-8 flex items-center gap-4">
                <input type="checkbox" id="chance-mode-toggle" class="w-8 h-8 text-indigo-600 rounded border-gray-300 focus:ring-indigo-500 cursor-pointer">
                <label for="chance-mode-toggle" class="cursor-pointer select-none flex-1">
                    <div class="text-2xl font-black text-gray-800">점수 뺏기 찬스!</div>
                    <div class="text-lg text-gray-600 font-bold mt-1 leading-snug">15번째 판부터<br>8초 제한 & 점수 뺏기!</div>
                </label>
            </div>

            <div class="grid grid-cols-2 gap-4 w-full max-w-md mb-10 pb-10">
                <!-- 1. 호랑이 -->
                <div onclick="selectCharacter('tiger')" class="char-card cursor-pointer bg-white p-4 rounded-3xl border-4 border-gray-100 flex flex-col items-center gap-2 group shadow-sm">
                    <div class="w-20 h-20 rounded-full bg-orange-100 flex items-center justify-center text-orange-600 text-4xl">
                        <i class="fas fa-cat"></i>
                    </div>
                    <span class="font-bold text-2xl text-gray-800">호랑이</span>
                </div>
                
                <!-- 2. 강아지 -->
                <div onclick="selectCharacter('puppy')" class="char-card cursor-pointer bg-white p-4 rounded-3xl border-4 border-gray-100 flex flex-col items-center gap-2 group shadow-sm">
                    <div class="w-20 h-20 rounded-full bg-yellow-100 flex items-center justify-center text-yellow-700 text-4xl">
                        <i class="fas fa-dog"></i>
                    </div>
                    <span class="font-bold text-2xl text-gray-800">강아지</span>
                </div>
                
                <!-- 3. 까치 -->
                <div onclick="selectCharacter('bird')" class="char-card cursor-pointer bg-white p-4 rounded-3xl border-4 border-gray-100 flex flex-col items-center gap-2 group shadow-sm">
                    <div class="w-20 h-20 rounded-full bg-slate-200 flex items-center justify-center text-slate-700 text-4xl">
                        <i class="fas fa-dove"></i>
                    </div>
                    <span class="font-bold text-2xl text-gray-800">까치</span>
                </div>
                
                <!-- 4. 돼지 -->
                <div onclick="selectCharacter('pig')" class="char-card cursor-pointer bg-white p-4 rounded-3xl border-4 border-gray-100 flex flex-col items-center gap-2 group shadow-sm">
                    <div class="w-20 h-20 rounded-full bg-pink-100 flex items-center justify-center text-pink-500 text-4xl">
                        <i class="fas fa-piggy-bank"></i>
                    </div>
                    <span class="font-bold text-2xl text-gray-800">돼지</span>
                </div>
                 
                 <!-- 5. 청룡 -->
                 <div onclick="selectCharacter('dragon')" class="char-card cursor-pointer bg-white p-4 rounded-3xl border-4 border-gray-100 flex flex-col items-center gap-2 group shadow-sm">
                    <div class="w-20 h-20 rounded-full bg-teal-100 flex items-center justify-center text-teal-600 text-4xl">
                        <i class="fas fa-dragon"></i>
                    </div>
                    <span class="font-bold text-2xl text-gray-800">청룡</span>
                </div>

                <!-- 6. 금붕어 -->
                <div onclick="selectCharacter('fish')" class="char-card cursor-pointer bg-white p-4 rounded-3xl border-4 border-gray-100 flex flex-col items-center gap-2 group shadow-sm">
                    <div class="w-20 h-20 rounded-full bg-red-100 flex items-center justify-center text-red-500 text-4xl">
                        <i class="fas fa-fish"></i>
                    </div>
                    <span class="font-bold text-2xl text-gray-800">금붕어</span>
                </div>
            </div>
        </div>

        <!-- 결과 모달 -->
        <div id="game-over-modal" class="absolute inset-0 bg-black/80 flex items-center justify-center z-50 hidden backdrop-blur-sm">
            <div class="bg-white p-8 rounded-3xl shadow-2xl text-center w-[90%] max-w-md animate-[popIn_0.4s_ease-out]">
                <h2 id="winner-title" class="text-5xl font-jua text-indigo-900 mb-2">경기 종료!</h2>
                
                <!-- 합산 점수 및 신기록 표시 -->
                <div id="new-record-badge" class="hidden bg-yellow-400 text-yellow-900 font-black px-4 py-1 rounded-full mb-4 inline-block text-lg shadow-sm animate-pulse">
                    🏆 역대 최고 점수 경신! 🏆
                </div>

                <div class="text-gray-500 font-bold mb-6 text-xl">
                    두 분 합산 점수: <span id="total-score-display" class="text-indigo-600 font-black text-3xl">0</span>점
                </div>
                
                <div class="flex justify-center gap-4 mb-8">
                    <!-- 1번 결과 -->
                    <div class="bg-yellow-50 p-4 rounded-2xl w-1/2 border-2 border-yellow-200">
                        <div class="text-xl font-bold text-gray-600 mb-2">1번 선수</div>
                        <div id="final-p1-score" class="text-4xl font-black text-indigo-700">0</div>
                    </div>
                    <!-- 2번 결과 -->
                    <div class="bg-green-50 p-4 rounded-2xl w-1/2 border-2 border-green-200">
                        <div class="text-xl font-bold text-gray-600 mb-2">2번 선수</div>
                        <div id="final-p2-score" class="text-4xl font-black text-indigo-700">0</div>
                    </div>
                </div>

                <button onclick="resetAll()" class="w-full bg-indigo-600 text-white py-6 rounded-2xl font-black text-3xl hover:bg-indigo-700 transition shadow-lg transform hover:scale-105">
                    다시 하기
                </button>
            </div>
        </div>
    </div>

    <script>
        // --- 게임 데이터 ---
        const historyDictionary = new Set([
            "고조선", "삼국시대", "신라", "고구려", "백제", "발해", "고려", "조선", "임진왜란", "일제강점기", "광복", "625전쟁", 
            "세종대왕", "이순신", "안중근", "김구", "유관순", "독도", "거북선", "훈민정음", "직지심체요절", "팔만대장경", 
            "경복궁", "불국사", "석굴암", "첨성대", "다보탑", "석가탑", "수원화성", "창덕궁", "종묘", "해인사", 
            "고인돌", "빗살무늬토기", "비파형동검", "세형동검", "청동기", "철기", "구석기", "신석기", 
            "경주", "서울", "평양", "개성", "한양", "웅진", "사비", "청해진", "장보고", "대조영", "왕건", "이성계", 
            "이방원", "세조", "연산군", "광해군", "정조", "영조", "대원군", "명성황후", "고종", "순종", 
            "정약용", "이황", "이이", "신사임당", "김유신", "강감찬", "을지문덕", "최무선", "서희", "윤관", 
            "로마", "이집트", "그리스", "중국", "일본", "미국", "프랑스", "영국", "독일", "러시아", 
            "나폴레옹", "히틀러", "링컨", "워싱턴", "간디", "처칠", "루즈벨트", "스탈린", "진시황", "공자", "맹자", 
            "석가모니", "예수", "마호메트", "십자군", "르네상스", "종교개혁", "산업혁명", "시민혁명", 
            "프랑스혁명", "미국독립혁명", "남북전쟁", "1차세계대전", "2차세계대전", "냉전", "유엔", "나토", 
            "석기시대", "중세", "근대", "현대", "왕조", "제국", "식민지", "독립", "혁명", "전쟁", "화랑도", "골품제"
        ]);

        // 캐릭터 데이터 (어르신 맞춤형)
        const characters = {
            'tiger': { name: '호랑이', icon: 'fa-cat', color: 'bg-orange-100', text: 'text-orange-800', border: 'border-orange-300' },
            'puppy': { name: '강아지', icon: 'fa-dog', color: 'bg-yellow-100', text: 'text-yellow-800', border: 'border-yellow-300' },
            'bird': { name: '까치', icon: 'fa-dove', color: 'bg-slate-200', text: 'text-slate-800', border: 'border-slate-400' },
            'pig': { name: '돼지', icon: 'fa-piggy-bank', color: 'bg-pink-100', text: 'text-pink-800', border: 'border-pink-300' },
            'dragon': { name: '청룡', icon: 'fa-dragon', color: 'bg-teal-100', text: 'text-teal-800', border: 'border-teal-300' },
            'fish': { name: '금붕어', icon: 'fa-fish', color: 'bg-red-100', text: 'text-red-800', border: 'border-red-300' }
        };

        // --- 게임 상태 변수 ---
        let currentWord = "";
        let usedWords = new Set();
        
        // 2인 플레이 변수
        let p1Char = null;
        let p2Char = null;
        let p1Score = 0;
        let p2Score = 0;
        let currentPlayer = 1; // 1 or 2
        let selectionPhase = 1; // 1: P1선택, 2: P2선택

        let turnCount = 0;
        const MAX_TURNS = 20;
        let isChanceMode = false;
        
        // 최고 점수 저장 (LocalStorage)
        let teamHighScore = parseInt(localStorage.getItem('teamHighScore') || '0');

        let isGameOver = false;
        let timerInterval;
        const DEFAULT_GAME_TIME = 15;
        const CHANCE_GAME_TIME = 8; // 5초 -> 8초로 변경
        let currentGameTime = DEFAULT_GAME_TIME;
        let currentTimeLeft = DEFAULT_GAME_TIME;
        
        let inputMode = 'keyboard';
        let recognition = null;
        let isRecording = false;
        let isProcessing = false;

        // DOM 요소
        const userInput = document.getElementById('user-input');
        const sendBtn = document.getElementById('send-btn');
        const chatContainer = document.getElementById('chat-container');
        const currentWordDisplay = document.getElementById('current-word-display');
        const turnCountDisplay = document.getElementById('turn-count');
        const timerBar = document.getElementById('timer-bar');
        const feedbackMsg = document.getElementById('feedback-msg');
        
        // 점수판 & 푸터
        const p1ScoreDisplay = document.getElementById('p1-score');
        const p2ScoreDisplay = document.getElementById('p2-score');
        const p1ScoreBox = document.getElementById('p1-score-box');
        const p2ScoreBox = document.getElementById('p2-score-box');
        const characterFooter = document.getElementById('character-footer');
        const footerCharIcon = document.getElementById('footer-char-icon');
        const footerCharName = document.getElementById('footer-char-name');
        const footerPlayerLabel = document.getElementById('footer-player-label');
        const teamBestScoreDisplay = document.getElementById('team-best-score');

        // 모달
        const gameOverModal = document.getElementById('game-over-modal');
        const charSelectionModal = document.getElementById('character-selection-modal');
        const selectionTitle = document.getElementById('selection-title');
        const chanceOptionContainer = document.getElementById('chance-option-container');
        const chanceModeToggle = document.getElementById('chance-mode-toggle');
        const winnerTitle = document.getElementById('winner-title');
        const finalP1Score = document.getElementById('final-p1-score');
        const finalP2Score = document.getElementById('final-p2-score');
        const totalScoreDisplay = document.getElementById('total-score-display');
        const newRecordBadge = document.getElementById('new-record-badge');

        // 입력 탭
        const tabKeyboard = document.getElementById('tab-keyboard');
        const tabVoice = document.getElementById('tab-voice');
        const modeKeyboardDiv = document.getElementById('mode-keyboard');
        const modeVoiceDiv = document.getElementById('mode-voice');
        const micBtn = document.getElementById('mic-btn');
        const voiceStatus = document.getElementById('voice-status');
        const voicePreview = document.getElementById('voice-preview');

        // --- 초기화 함수 (완전 리셋) ---
        function resetAll() {
            p1Char = null;
            p2Char = null;
            selectionPhase = 1;
            
            // 점수판 업데이트
            teamBestScoreDisplay.textContent = teamHighScore;

            // 선택창 리셋
            selectionTitle.innerHTML = "1번 선수<br>캐릭터 선택";
            chanceOptionContainer.style.display = "flex"; // 찬스 옵션 보이기
            chanceModeToggle.checked = false;
            
            showCharacterSelection();
        }

        function showCharacterSelection() {
            isGameOver = true;
            clearInterval(timerInterval);
            if (recognition && isRecording) recognition.stop();
            
            gameOverModal.classList.add('hidden');
            charSelectionModal.classList.remove('hidden');
        }

        function selectCharacter(charId) {
            const selected = characters[charId];
            
            if (selectionPhase === 1) {
                p1Char = selected;
                isChanceMode = chanceModeToggle.checked; // 1번 선택 시 옵션 저장
                
                // 2번 선택 준비
                selectionPhase = 2;
                selectionTitle.innerHTML = "2번 선수<br>캐릭터 선택";
                chanceOptionContainer.style.display = "none"; // 옵션 숨김
                
                // 스크롤 위로
                charSelectionModal.scrollTop = 0;
            } else {
                p2Char = selected;
                charSelectionModal.classList.add('hidden');
                initGame(); // 게임 시작
            }
        }

        // --- 게임 시작 ---
        function initGame() {
            currentWord = "";
            usedWords.clear();
            p1Score = 0;
            p2Score = 0;
            currentPlayer = 1;
            turnCount = 1;
            isGameOver = false;
            
            // 점수판 초기화
            updateScoreBoard();
            turnCountDisplay.textContent = turnCount;
            teamBestScoreDisplay.textContent = teamHighScore;
            
            // 채팅창 리셋
            chatContainer.innerHTML = '';
            const startWords = ["끝말잇기", "대한민국", "보물지도", "무지개", "운동장"];
            const randomStart = startWords[Math.floor(Math.random() * startWords.length)];
            
            updateCurrentWord(randomStart, false);
            addSystemMessage(`'${randomStart}'(으)로 시작합니다!`);
            
            // 하단 푸터 보이기
            characterFooter.classList.remove('hidden');
            updateFooterUI();

            // 입력 활성화
            userInput.disabled = false;
            sendBtn.disabled = false;
            micBtn.disabled = false;
            
            if (inputMode === 'keyboard') userInput.focus();
            if (inputMode === 'voice') try { recognition.start(); } catch(e){}

            // 타이머 시작
            resetTimer();
        }

        // --- 턴 관리 로직 ---
        function handleTurn() {
            if (isGameOver) return;

            const input = userInput.value.trim();
            const fail = (msg) => {
                showFeedback(msg, true);
                isProcessing = false;
                if(inputMode === 'voice' && !isRecording) try{recognition.start()}catch(e){}
            };

            if (!input) { isProcessing = false; return; }
            if (input.length < 2) { fail("두 글자 이상!"); return; }
            if (!/^[가-힣]+$/.test(input)) { fail("한글만 돼요!"); return; }

            const lastChar = currentWord[currentWord.length - 1];
            const firstChar = input[0];
            const initialSound = getInitialSound(lastChar);

            if (firstChar !== lastChar && firstChar !== initialSound) {
                showFeedback(`'${lastChar}'(으)로 시작!`, true);
                isProcessing = false;
                if(inputMode === 'voice') setTimeout(() => {if(!isRecording) try{recognition.start()}catch(e){}}, 1000);
                return;
            }

            if (usedWords.has(input)) {
                fail("이미 쓴 단어!");
                return;
            }

            // --- 정답 처리 ---
            let turnScore = 10;
            let feedbackText = "";
            let isSteal = false;

            if (historyDictionary.has(input)) {
                turnScore += 5;
                feedbackText = "역사 용어 보너스!";
            }

            if (isChanceMode && turnCount >= 15) {
                isSteal = true;
                feedbackText = feedbackText ? "보너스 & 뺏기 성공!" : "점수 뺏기 성공!";
            }

            if(feedbackText) showFeedback(feedbackText, false);
            else showFeedback("", false);

            userInput.value = "";
            voicePreview.textContent = "";

            addMessage(input, currentPlayer);
            updateCurrentWord(input, true);
            
            // 점수 반영
            if (currentPlayer === 1) {
                p1Score += turnScore;
                if (isSteal) {
                    const stolen = Math.min(p2Score, turnScore); // 점수만큼 뺏기
                    p2Score -= stolen;
                    p1Score += stolen; // 뺏어오기 (상대 감점 + 내 추가 점수)
                }
            } else {
                p2Score += turnScore;
                if (isSteal) {
                    const stolen = Math.min(p1Score, turnScore);
                    p1Score -= stolen;
                    p2Score += stolen;
                }
            }
            updateScoreBoard();

            // 턴 종료 확인
            if (turnCount >= MAX_TURNS) {
                handleGameOver();
                return;
            }

            // 다음 턴 준비
            turnCount++;
            turnCountDisplay.textContent = turnCount;
            currentPlayer = currentPlayer === 1 ? 2 : 1; // 선수 교체
            
            updateFooterUI();
            resetTimer();
            isProcessing = false;
            
            if (inputMode === 'keyboard') userInput.focus();
            // 음성 모드면 계속 듣기 (재시작 로직은 onend에서 처리되지만 확실히 하기 위해)
        }

        function handleGameOver(reason) {
            isGameOver = true;
            clearInterval(timerInterval);
            userInput.disabled = true;
            sendBtn.disabled = true;
            micBtn.disabled = true;
            if(recognition) recognition.stop();

            // 합산 점수 및 결과 표시
            const totalScore = p1Score + p2Score;
            finalP1Score.textContent = p1Score;
            finalP2Score.textContent = p2Score;
            totalScoreDisplay.textContent = totalScore;

            let isNewRecord = false;
            // 최고 기록 경신 체크 (합산 점수)
            if (totalScore > teamHighScore) {
                teamHighScore = totalScore;
                localStorage.setItem('teamHighScore', teamHighScore);
                isNewRecord = true;
                newRecordBadge.classList.remove('hidden');
            } else {
                newRecordBadge.classList.add('hidden');
            }

            if (reason === "timeout") {
                winnerTitle.textContent = `${currentPlayer === 1 ? '2번' : '1번'} 선수 승리!`;
                winnerTitle.className = "text-5xl font-jua text-red-600 mb-2";
            } else {
                // 20턴 종료 후
                if (isNewRecord) {
                    // 신기록이면 공동 승리 (협동 승리)
                    winnerTitle.textContent = "최고 기록 경신! 모두의 승리!";
                    winnerTitle.className = "text-4xl font-jua text-indigo-900 mb-2 leading-tight";
                    triggerFireworks();
                } else {
                    // 일반 승패 (개인전)
                    if (p1Score > p2Score) {
                        winnerTitle.textContent = "1번 선수 우승!";
                        winnerTitle.className = "text-5xl font-jua text-indigo-900 mb-2";
                        triggerFireworks();
                    } else if (p2Score > p1Score) {
                        winnerTitle.textContent = "2번 선수 우승!";
                        winnerTitle.className = "text-5xl font-jua text-indigo-900 mb-2";
                        triggerFireworks();
                    } else {
                        winnerTitle.textContent = "무승부!";
                        winnerTitle.className = "text-5xl font-jua text-gray-700 mb-2";
                    }
                }
            }

            setTimeout(() => gameOverModal.classList.remove('hidden'), 500);
        }

        // --- UI 업데이트 ---
        function updateScoreBoard() {
            p1ScoreDisplay.textContent = p1Score;
            p2ScoreDisplay.textContent = p2Score;
        }

        function updateFooterUI() {
            const char = currentPlayer === 1 ? p1Char : p2Char;
            const label = currentPlayer === 1 ? "1번 선수" : "2번 선수";
            const bgColor = currentPlayer === 1 ? "bg-yellow-100" : "bg-green-100";
            const borderColor = currentPlayer === 1 ? "border-yellow-300" : "border-green-300";
            const textColor = currentPlayer === 1 ? "text-yellow-800" : "text-green-800";

            // 푸터 스타일 변경
            characterFooter.className = `${bgColor} p-4 border-t-4 ${borderColor} flex items-center justify-center gap-4 shrink-0 transition-colors duration-300 shadow-[0_-5px_20px_rgba(0,0,0,0.1)] safe-bottom`;
            
            footerCharIcon.innerHTML = `<i class="fas ${char.icon}"></i>`;
            footerCharIcon.className = `w-16 h-16 rounded-full bg-white flex items-center justify-center text-4xl shadow-md border-4 ${char.border} ${char.text} transition-all`;
            
            footerPlayerLabel.textContent = label;
            footerPlayerLabel.className = `text-lg font-black mb-0 ${textColor}`;
            
            footerCharName.textContent = char.name;
            footerCharName.className = `text-4xl font-jua ${char.text} leading-none`;

            // 점수판 강조 효과
            if (currentPlayer === 1) {
                p1ScoreBox.classList.add('turn-active');
                p2ScoreBox.classList.remove('turn-active');
            } else {
                p2ScoreBox.classList.add('turn-active');
                p1ScoreBox.classList.remove('turn-active');
            }
        }

        // --- 두음법칙 ---
        function getInitialSound(char) {
            const map = {
                '라': '나', '락': '낙', '란': '난', '랄': '날', '람': '남', '랍': '납', '랑': '낭',
                '래': '내', '랭': '냉', '냑': '약', '략': '약', '냥': '양', '량': '양', '녀': '여', '려': '여',
                '녁': '역', '력': '역', '년': '연', '련': '연', '녈': '열', '렬': '열', '념': '염', '렴': '염',
                '녕': '영', '령': '영', '녜': '예', '례': '예', '로': '노', '록': '녹', '론': '논', '롱': '농',
                '뢰': '뇌', '료': '요', '룡': '용', '루': '누', '류': '유', '륙': '육', '륜': '윤', '률': '율',
                '륭': '융', '르': '느', '리': '이', '린': '인', '림': '임', '립': '입'
            };
            return map[char] || char;
        }

        // --- 유틸리티 ---
        function updateCurrentWord(word, addToHistory) {
            currentWord = word;
            if (addToHistory) usedWords.add(word);
            currentWordDisplay.textContent = word;
            currentWordDisplay.classList.remove('animate-pulse');
            void currentWordDisplay.offsetWidth; 
            currentWordDisplay.classList.add('animate-pulse');
        }

        function addSystemMessage(text) {
            const div = document.createElement('div');
            div.className = 'flex justify-center chat-bubble my-4';
            div.innerHTML = `<div class="bg-gray-100 text-gray-700 text-lg px-6 py-2 rounded-full font-bold shadow-sm border border-gray-200">${text}</div>`;
            chatContainer.appendChild(div);
            chatContainer.scrollTop = chatContainer.scrollHeight;
        }

        function addMessage(text, player) {
            const div = document.createElement('div');
            // 1번: 오른쪽(나처럼), 2번: 왼쪽(상대처럼)
            div.className = `flex ${player === 1 ? 'justify-end' : 'justify-start'} chat-bubble items-end gap-2`;
            
            const char = player === 1 ? p1Char : p2Char;
            const bubbleColor = player === 1 
                ? "bg-white border-4 border-yellow-400 text-yellow-900" 
                : "bg-white border-4 border-green-400 text-green-900";
            const roundClass = player === 1 ? "rounded-tr-none" : "rounded-tl-none";

            // 아이콘 (말풍선 옆)
            const iconDiv = document.createElement('div');
            iconDiv.className = `w-12 h-12 rounded-full flex items-center justify-center text-2xl shadow-sm border-2 bg-white ${char.text} ${char.border} shrink-0 mb-1`;
            iconDiv.innerHTML = `<i class="fas ${char.icon}"></i>`;

            const bubble = document.createElement('div');
            // 글씨 크기 조정: text-2xl
            bubble.className = `max-w-[75%] px-6 py-3 rounded-3xl shadow-md text-2xl font-bold ${bubbleColor} ${roundClass}`;
            bubble.textContent = text;

            if (player === 2) {
                div.appendChild(iconDiv);
                div.appendChild(bubble);
            } else {
                div.appendChild(bubble);
                div.appendChild(iconDiv);
            }

            chatContainer.appendChild(div);
            chatContainer.scrollTop = chatContainer.scrollHeight;
        }

        function showFeedback(msg, isError) {
            feedbackMsg.textContent = msg;
            if (isError) {
                feedbackMsg.className = "text-center text-2xl mt-3 h-8 font-black text-red-500 animate-bounce";
            } else {
                feedbackMsg.className = "text-center text-2xl mt-3 h-8 font-black text-blue-600 animate-pulse";
            }
        }

        // --- 타이머 ---
        function startTimer() {
            clearInterval(timerInterval);
            
            // 찬스 모드 체크 (15턴 이상이고 찬스모드 켜짐)
            if (turnCount >= 15 && isChanceMode) {
                currentGameTime = CHANCE_GAME_TIME;
                timerBar.parentElement.className = "w-full bg-red-100 h-5 mt-3 rounded-full overflow-hidden border-2 border-red-400 shadow-inner";
            } else {
                currentGameTime = DEFAULT_GAME_TIME;
                timerBar.parentElement.className = "w-full bg-gray-200 h-5 mt-3 rounded-full overflow-hidden border border-gray-300";
            }

            currentTimeLeft = currentGameTime;
            timerBar.style.width = '100%';
            
            timerInterval = setInterval(() => {
                if (isGameOver) { clearInterval(timerInterval); return; }

                currentTimeLeft -= 0.1;
                let percent = (currentTimeLeft / currentGameTime) * 100;
                timerBar.style.width = `${percent}%`;

                if (currentTimeLeft <= 0) {
                    clearInterval(timerInterval);
                    handleGameOver("timeout"); // 시간 초과 패배
                }
            }, 100);
        }

        function resetTimer() { startTimer(); }

        // --- 입력 모드 ---
        function setInputMode(mode) {
            inputMode = mode;
            if (mode === 'keyboard') {
                modeKeyboardDiv.classList.remove('hidden');
                modeVoiceDiv.classList.add('hidden');
                tabKeyboard.className = "flex-1 py-4 text-xl font-black text-indigo-700 border-b-4 border-indigo-700 bg-white flex items-center justify-center gap-2";
                tabVoice.className = "flex-1 py-4 text-xl font-black text-gray-400 bg-gray-50 hover:text-gray-600 flex items-center justify-center gap-2";
                if(recognition) recognition.stop();
                userInput.focus();
            } else {
                modeKeyboardDiv.classList.add('hidden');
                modeVoiceDiv.classList.remove('hidden');
                modeVoiceDiv.classList.add('flex');
                tabVoice.className = "flex-1 py-4 text-xl font-black text-indigo-700 border-b-4 border-indigo-700 bg-white flex items-center justify-center gap-2";
                tabKeyboard.className = "flex-1 py-4 text-xl font-black text-gray-400 bg-gray-50 hover:text-gray-600 flex items-center justify-center gap-2";
                if(!isRecording) try { recognition.start(); } catch(e) {}
            }
        }

        // --- 음성 인식 ---
        function toggleVoiceRecognition() {
            if (!recognition) return alert("이 브라우저는 음성 인식을 지원하지 않습니다.");
            if (isRecording) recognition.stop();
            else try { recognition.start(); } catch(e) {}
        }

        if ('SpeechRecognition' in window || 'webkitSpeechRecognition' in window) {
            const SpeechRecognition = window.SpeechRecognition || window.webkitSpeechRecognition;
            recognition = new SpeechRecognition();
            recognition.lang = 'ko-KR';
            recognition.continuous = false;
            recognition.interimResults = false;

            recognition.onstart = function() {
                isRecording = true;
                micBtn.classList.add('mic-active');
                voiceStatus.textContent = "듣고 있어요...";
                voiceStatus.className = "text-red-500 text-xl font-bold animate-pulse";
            };
            recognition.onend = function() {
                isRecording = false;
                micBtn.classList.remove('mic-active');
                if (inputMode === 'voice' && !isGameOver && !isProcessing) {
                    setTimeout(() => {
                        if (inputMode === 'voice' && !isGameOver && !isProcessing && !isRecording) try { recognition.start(); } catch(e) {}
                    }, 300);
                } else {
                    voiceStatus.textContent = "마이크를 켜고 말씀하세요";
                    voiceStatus.className = "text-gray-600 text-xl font-bold";
                }
            };
            recognition.onresult = function(event) {
                const transcript = event.results[0][0].transcript;
                voicePreview.textContent = transcript;
                userInput.value = transcript.replace(/\s/g, '');
                isProcessing = true;
                setTimeout(() => handleTurn(), 500);
            };
        } else {
            tabVoice.style.display = 'none';
        }

        // --- 폭죽 ---
        function triggerFireworks() {
            const duration = 5 * 1000;
            const animationEnd = Date.now() + duration;
            const defaults = { startVelocity: 30, spread: 360, ticks: 60, zIndex: 60, scalar: 1.5 };
            const interval = setInterval(function() {
                const timeLeft = animationEnd - Date.now();
                if (timeLeft <= 0) return clearInterval(interval);
                const particleCount = 50 * (timeLeft / duration);
                confetti(Object.assign({}, defaults, { particleCount, origin: { x: Math.random(), y: Math.random() - 0.2 } }));
            }, 250);
        }

        // --- 이벤트 ---
        userInput.addEventListener('keypress', (e) => { if (e.key === 'Enter') handleTurn(); });
        sendBtn.addEventListener('click', handleTurn);
        
        window.onload = resetAll;

    </script>
</body>
</html>
