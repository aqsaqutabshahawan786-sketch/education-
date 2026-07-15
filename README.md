<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>English Coach App</title>
    <!-- Tailwind CSS for modern Purple and Mint layout -->
    <script src="https://jsdelivr.net"></script>
    <style>
        .chat-container { height: calc(100vh - 220px); }
    </style>
</head>
<body class="bg-gray-100 font-sans antialiased">

    <!-- ========================================================== -->
    <!-- 📅 DAY 1: DASHBOARD SCREEN (PRICING & CUSTOM SCENARIO) -->
    <!-- ========================================================== -->
    <div id="dashboard-screen" class="max-w-md mx-auto bg-white min-h-screen p-5 flex flex-col justify-between shadow-lg">
        <div>
            <div class="flex justify-between items-center mb-6">
                <div>
                    <h1 class="text-2xl font-bold text-gray-800">English Coach</h1>
                    <p class="text-sm text-gray-500">Ready for today's session?</p>
                </div>
            </div>

            <h2 class="text-xs font-bold text-gray-500 uppercase tracking-wider mb-3">Select Duration</h2>
            
            <!-- 4 Pricing Cards Grid Layout -->
            <div class="grid grid-cols-2 gap-4 mb-6">
                <div onclick="selectDuration(15, this)" class="duration-card p-4 bg-purple-900 text-white rounded-2xl border-2 border-purple-900 cursor-pointer shadow-md transition-all">
                    <p class="text-xs text-purple-200 font-medium">Express</p>
                    <p class="text-xl font-bold my-1">15 mins</p>
                    <span class="inline-block bg-emerald-400 text-white text-xs font-bold px-2 py-1 rounded-lg">$5.00</span>
                </div>
                <div onclick="selectDuration(30, this)" class="duration-card p-4 bg-gray-50 text-gray-800 rounded-2xl border-2 border-gray-200 cursor-pointer transition-all">
                    <p class="text-xs text-gray-500 font-medium">Power</p>
                    <p class="text-xl font-bold my-1">30 mins</p>
                    <span class="inline-block bg-green-600 text-white text-xs font-bold px-2 py-1 rounded-lg">$10.00</span>
                </div>
                <div onclick="selectDuration(45, this)" class="duration-card p-4 bg-gray-50 text-gray-800 rounded-2xl border-2 border-gray-200 cursor-pointer transition-all">
                    <p class="text-xs text-gray-500 font-medium">Deep Dive</p>
                    <p class="text-xl font-bold my-1">45 mins</p>
                    <span class="inline-block bg-green-600 text-white text-xs font-bold px-2 py-1 rounded-lg">$15.00</span>
                </div>
                <div onclick="selectDuration(60, this)" class="duration-card p-4 bg-gray-50 text-gray-800 rounded-2xl border-2 border-gray-200 cursor-pointer transition-all">
                    <p class="text-xs text-gray-500 font-medium">Mastery</p>
                    <p class="text-xl font-bold my-1">1 hour</p>
                    <span class="inline-block bg-green-600 text-white text-xs font-bold px-2 py-1 rounded-lg">$20.00</span>
                </div>
            </div>

            <h2 class="text-xs font-bold text-gray-500 uppercase tracking-wider mb-2">Focus Area</h2>
            <div class="relative">
                <label class="absolute -top-2 left-3 bg-white px-1 text-xs text-purple-700 font-medium">Custom Dialog Scenario</label>
                <textarea id="scenario-input" class="w-full p-3 border-2 border-gray-200 rounded-xl text-sm focus:outline-none focus:border-purple-900 resize-none h-24" placeholder="E.g., Asking for directions at a busy airport terminal..."></textarea>
            </div>
        </div>

        <button onclick="startSession()" class="w-full bg-purple-900 hover:bg-purple-800 text-white font-bold py-4 rounded-full text-center shadow-lg transition-all transform active:scale-95 cursor-pointer mt-4">
            Start Coaching Session >>
        </button>
    </div>

    <!-- ========================================================== -->
    <!-- 📅 DAY 2 & 3: COACHING CHAT SCREEN WITH LOCKING LOGIC -->
    <!-- ========================================================== -->
    <div id="chat-screen" class="max-w-md mx-auto bg-gray-50 min-h-screen flex flex-col justify-between shadow-lg hidden">
        <!-- App Header with Back Control and Reverse Timer -->
        <div class="bg-purple-900 text-white p-4 flex items-center justify-between shadow-md">
            <div class="flex items-center space-x-3">
                <button onclick="backToDashboard()" class="text-white hover:text-purple-200 focus:outline-none cursor-pointer">
                    <svg class="w-6 h-6" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M10 19l-7-7m0 0l7-7m-7 7h18"></path></svg>
                </button>
                <div>
                    <h2 id="chat-title-time" class="font-bold text-sm">Session: 15 mins</h2>
                </div>
            </div>
            <div id="timer-display" class="text-2xl font-bold text-emerald-400">15:00</div>
        </div>

        <!-- Dynamic Warning Indicator Block Text -->
        <div id="status-bar" class="bg-amber-50 text-amber-800 text-xs font-semibold p-2.html text-center border-b border-amber-200">
            Bolnay ya likhnay ke liye pehle START button dabayein.
        </div>

        <!-- 💬 DAY 3: WHATSAPP STYLE CHAT BUBBLES AREA -->
        <div id="chat-logs" class="chat-container p-4 overflow-y-auto flex flex-col space-y-3">
            <!-- AI Welcome Bubble -->
            <div class="flex justify-start">
                <div class="bg-white text-gray-800 p-3 rounded-2xl rounded-tl-none max-w-[80%] shadow-sm text-sm border border-gray-100 leading-relaxed">
                    Hello! Welcome to your coaching session. Press START to begin speaking.
                </div>
            </div>
        </div>

        <!-- ⚙️ DAY 2: CONTROL PANEL WITH BLOCK / LOCK ACTION -->
        <div class="p-3 bg-white border-t border-gray-200 flex items-center space-x-3">
            <!-- Toggle Start/Pause Button -->
            <button id="toggle-btn" onclick="toggleTimer()" class="bg-green-600 hover:bg-green-700 text-white text-xs font-bold px-4 py-3 rounded-xl transition-all cursor-pointer shadow-md">
                START
            </button>

            <!-- Locked Input Container System -->
            <div id="input-wrapper" class="flex-1 bg-gray-200 rounded-full px-4 py-1.5 flex items-center border border-gray-300 opacity-60">
                <input id="chat-input" type="text" disabled class="flex-1 bg-transparent border-none outline-none text-sm py-1.5 placeholder-gray-500" placeholder="⚠️ Locked (Press START)" onkeypress="handleKeyPress(event)">
                
                <!-- Action Buttons Block Trigger Controls -->
                <button id="send-btn" disabled onclick="sendTextMessage()" class="text-gray-400 p-1 ml-1 focus:outline-none">
                    <svg class="w-5 h-5" fill="currentColor" viewBox="0 0 20 20"><path d="M10.894 2.553a1 1 0 00-1.788 0l-7 14a1 1 0 001.169 1.409l5-1.429A1 1 0 009 15.571V11a1 1 0 112 0v4.571a1 1 0 00.725.962l5 1.428a1 1 0 001.17-1.408l-7-14z"></path></svg>
                </button>
                <button id="mic-btn" disabled onclick="simulateMicInput()" class="text-gray-400 p-1 ml-1 focus:outline-none">
                    <svg class="w-5 h-5" fill="currentColor" viewBox="0 0 20 20"><path fill-rule="evenodd" d="M7 4a3 3 0 016 0v4a3 3 0 11-6 0V4zm4 10.93A7.001 7.001 0 0017 8a1 1 0 10-2 0A5 5 0 015 8a1 1 0 00-2 0 7.001 7.001 0 006 6.93V17H6a1 1 0 100 2h8a1 1 0 100-2h-3v-2.07z" clip-rule="evenodd"></path></svg>
                </button>
            </div>
        </div>
    </div>

    <!-- ========================================================== -->
    <!-- ⚙️ APP LOGIC & ENGINE CONTROLS -->
    <!-- ========================================================== -->
    <script>
        let selectedMinutes = 15;
        let remainingSeconds = 15 * 60;
        let timerInterval = null;
        let isTimerRunning = false;

        function selectDuration(mins, element) {
            selectedMinutes = mins;
            document.querySelectorAll('.duration-card').forEach(card => {
                card.className = "duration-card p-4 bg-gray-50 text-gray-800 rounded-2xl border-2 border-gray-200 cursor-pointer transition-all";
                let badge = card.querySelector('span');
                if(badge) badge.className = "inline-block bg-green-600 text-white text-xs font-bold px-2 py-1 rounded-lg";
            });
            element.className = "duration-card p-4 bg-purple-900 text-white rounded-2xl border-2 border-purple-900 cursor-pointer shadow-md transition-all";
            let badge = element.querySelector('span');
            if(badge) badge.className = "inline-block bg-emerald-400 text-white text-xs font-bold px-2 py-1 rounded-lg";
        }

        function startSession() {
            remainingSeconds = selectedMinutes * 60;
            updateTimerDisplay();
            document.getElementById('chat-title-time').innerText = `Session: ${selectedMinutes} mins`;
            document.getElementById('dashboard-screen').classList.add('hidden');
            document.getElementById('chat-screen').classList.remove('hidden');
        }

        function backToDashboard() {
            clearInterval(timerInterval);
            isTimerRunning = false;
            resetControlsUI();
            document.getElementById('chat-screen').classList.add('hidden');
            document.getElementById('dashboard-screen').classList.remove('hidden');
        }

        function toggleTimer() {
            const toggleBtn = document.getElementById('toggle-btn');
            const statusBar = document.getElementById('status-bar');
