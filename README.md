<!DOCTYPE html>
<html lang="ja" class="light">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>カウントダウンタイマー</title>
  
  <!-- Tailwind CSS -->
  <script src="https://cdn.tailwindcss.com"></script>
  <script>
    tailwind.config = {
      darkMode: 'class',
      theme: {
        extend: {
          fontFamily: {
            sans: [
              'Hiragino Sans',
              'Hiragino Kaku Gothic ProN',
              'Yu Gothic UI',
              'Meiryo',
              'Noto Sans JP',
              'Inter',
              '-apple-system',
              'sans-serif'
            ],
            mono: ['SF Mono', 'Roboto Mono', 'Consolas', 'monospace']
          },
          colors: {
            warm: {
              50: '#FDFBF7',
              100: '#F7F4EE',
              200: '#EFEAE1',
              300: '#E0D7C8',
              400: '#C7B9A3',
              500: '#A8967E',
              600: '#8A7761',
              700: '#6C5C4A',
              800: '#4F4235',
              900: '#322A21',
              950: '#1C1611'
            },
            accent: {
              50: '#F0F7FF',
              100: '#E0EFFE',
              500: '#3B82F6',
              600: '#2563EB',
              700: '#1D4ED8'
            }
          },
          boxShadow: {
            'soft': '0 10px 30px -5px rgba(0, 0, 0, 0.05), 0 4px 12px -2px rgba(0, 0, 0, 0.025)',
            'card': '0 2px 10px rgba(0, 0, 0, 0.03), 0 20px 25px -5px rgba(0, 0, 0, 0.05)',
            'modal': '0 25px 50px -12px rgba(0, 0, 0, 0.15)'
          }
        }
      }
    }
  </script>

  <!-- Lucide Icons -->
  <script src="https://unpkg.com/lucide@latest"></script>

  <style>
    /* Smooth transition for theme toggle */
    body {
      transition: background-color 0.3s ease, color 0.3s ease;
    }
    
    /* Subtle flip transition animation */
    .time-num-card {
      transition: transform 0.2s ease, opacity 0.2s ease;
    }
  </style>
</head>
<body class="min-h-screen bg-warm-50 text-warm-900 dark:bg-slate-950 dark:text-slate-100 font-sans selection:bg-warm-200 dark:selection:bg-slate-800 flex flex-col justify-between antialiased">

  <!-- Header Bar -->
  <header class="w-full border-b border-warm-200/60 dark:border-slate-800/80 bg-warm-50/80 dark:bg-slate-950/80 backdrop-blur-md sticky top-0 z-30 transition-colors">
    <div class="max-w-5xl mx-auto px-4 sm:px-6 h-16 flex items-center justify-between">
      
      <!-- Brand / Logo -->
      <button onclick="openConfigModal()" class="flex items-center space-x-2.5 text-warm-800 dark:text-slate-200 hover:opacity-80 transition group text-left">
        <div class="w-9 h-9 rounded-xl bg-warm-200/70 dark:bg-slate-800 flex items-center justify-center text-warm-700 dark:text-slate-300 group-hover:scale-105 transition-transform">
          <i data-lucide="hourglass" class="w-5 h-5"></i>
        </div>
        <div>
          <span class="font-bold text-base tracking-tight block">タイマー</span>
          <span class="text-[10px] text-warm-500 dark:text-slate-400 block -mt-0.5">カウントダウン & 共有</span>
        </div>
      </button>

      <!-- Action Tools -->
      <div class="flex items-center space-x-2 sm:space-x-3">
        <!-- New / Edit Timer -->
        <button onclick="openConfigModal()" class="px-3.5 py-1.5 sm:px-4 sm:py-2 text-xs sm:text-sm font-medium bg-white dark:bg-slate-900 text-warm-800 dark:text-slate-200 rounded-xl border border-warm-200 dark:border-slate-800 shadow-sm hover:bg-warm-100/50 dark:hover:bg-slate-800 transition flex items-center space-x-1.5">
          <i data-lucide="edit-3" class="w-4 h-4 text-warm-600 dark:text-slate-400"></i>
          <span class="hidden sm:inline">タイマー編集</span>
          <span class="sm:hidden">編集</span>
        </button>

        <!-- Share Button -->
        <button onclick="openShareModal()" class="px-3.5 py-1.5 sm:px-4 sm:py-2 text-xs sm:text-sm font-medium bg-warm-800 hover:bg-warm-900 dark:bg-slate-100 dark:hover:bg-white text-white dark:text-slate-900 rounded-xl shadow-sm transition flex items-center space-x-1.5">
          <i data-lucide="share-2" class="w-4 h-4"></i>
          <span>URL共有</span>
        </button>

        <!-- Dark / Light Mode Toggle -->
        <button onclick="toggleDarkMode()" title="表示モード切替" class="p-2 text-warm-600 dark:text-slate-400 hover:bg-warm-200/50 dark:hover:bg-slate-800 rounded-xl transition">
          <i data-lucide="sun" class="w-5 h-5 hidden dark:block"></i>
          <i data-lucide="moon" class="w-5 h-5 block dark:hidden"></i>
        </button>
      </div>
    </div>
  </header>

  <!-- Main Countdown View -->
  <main class="flex-grow flex flex-col items-center justify-center p-4 sm:p-6 lg:p-8">
    <div id="mainContainer" class="w-full max-w-4xl mx-auto flex flex-col items-center justify-center py-8 sm:py-16">
      
      <!-- Event Header / Title -->
      <div class="text-center mb-10 sm:mb-14 max-w-2xl px-4">
        <!-- Target Date Sub-badge -->
        <div id="targetBadge" class="inline-flex items-center px-3.5 py-1 rounded-full text-xs sm:text-sm font-medium bg-warm-200/50 dark:bg-slate-800/60 text-warm-700 dark:text-slate-300 border border-warm-300/40 dark:border-slate-700/50 mb-4">
          <i data-lucide="calendar" class="w-3.5 h-3.5 mr-2 text-warm-500 dark:text-slate-400"></i>
          <span id="displayTargetDate">--年--月--日 --:--</span>
        </div>

        <!-- Main Display Title -->
        <h1 id="displayTitle" class="text-3xl sm:text-5xl lg:text-6xl font-extrabold tracking-tight text-warm-900 dark:text-white leading-tight break-words">
          <!-- Dynamically populated -->
        </h1>
      </div>

      <!-- Main Number Cards (Grid) -->
      <div class="grid grid-cols-2 sm:grid-cols-4 gap-3 sm:gap-6 lg:gap-8 w-full max-w-3xl px-2">
        
        <!-- Days Card -->
        <div class="bg-white dark:bg-slate-900/90 border border-warm-200/80 dark:border-slate-800 rounded-2xl sm:rounded-3xl p-4 sm:p-6 text-center shadow-card transition-all duration-300 flex flex-col justify-center items-center">
          <span id="daysVal" class="text-4xl sm:text-6xl lg:text-7xl font-bold font-mono tracking-tight text-warm-900 dark:text-slate-50">00</span>
          <span class="mt-2 text-xs sm:text-sm font-medium text-warm-500 dark:text-slate-400">日</span>
        </div>

        <!-- Hours Card -->
        <div class="bg-white dark:bg-slate-900/90 border border-warm-200/80 dark:border-slate-800 rounded-2xl sm:rounded-3xl p-4 sm:p-6 text-center shadow-card transition-all duration-300 flex flex-col justify-center items-center">
          <span id="hoursVal" class="text-4xl sm:text-6xl lg:text-7xl font-bold font-mono tracking-tight text-warm-900 dark:text-slate-50">00</span>
          <span class="mt-2 text-xs sm:text-sm font-medium text-warm-500 dark:text-slate-400">時間</span>
        </div>

        <!-- Minutes Card -->
        <div class="bg-white dark:bg-slate-900/90 border border-warm-200/80 dark:border-slate-800 rounded-2xl sm:rounded-3xl p-4 sm:p-6 text-center shadow-card transition-all duration-300 flex flex-col justify-center items-center">
          <span id="minsVal" class="text-4xl sm:text-6xl lg:text-7xl font-bold font-mono tracking-tight text-warm-900 dark:text-slate-50">00</span>
          <span class="mt-2 text-xs sm:text-sm font-medium text-warm-500 dark:text-slate-400">分</span>
        </div>

        <!-- Seconds Card -->
        <div class="bg-white dark:bg-slate-900/90 border border-warm-200/80 dark:border-slate-800 rounded-2xl sm:rounded-3xl p-4 sm:p-6 text-center shadow-card transition-all duration-300 flex flex-col justify-center items-center">
          <span id="secsVal" class="text-4xl sm:text-6xl lg:text-7xl font-bold font-mono tracking-tight text-warm-900 dark:text-slate-50">00</span>
          <span class="mt-2 text-xs sm:text-sm font-medium text-warm-500 dark:text-slate-400">秒</span>
        </div>

      </div>

      <!-- Time Up Message Banner (Hidden by default) -->
      <div id="timeUpBanner" class="hidden mt-10 p-6 sm:p-8 rounded-3xl bg-warm-200/60 dark:bg-slate-900 border border-warm-300/60 dark:border-slate-800 text-center max-w-lg w-full shadow-soft transition-all">
        <div class="w-12 h-12 bg-warm-800 dark:bg-slate-100 text-white dark:text-slate-900 rounded-full flex items-center justify-center mx-auto mb-3">
          <i data-lucide="check" class="w-6 h-6"></i>
        </div>
        <h2 class="text-2xl font-bold text-warm-900 dark:text-white">時間になりました！</h2>
        <p class="text-sm text-warm-600 dark:text-slate-400 mt-1">設定された目標日時に到達しました。</p>
      </div>

      <!-- Quick Action Options -->
      <div class="mt-12 flex flex-wrap justify-center gap-3">
        <button onclick="openConfigModal()" class="px-5 py-2.5 rounded-2xl bg-white dark:bg-slate-900 hover:bg-warm-100/60 dark:hover:bg-slate-800 border border-warm-200 dark:border-slate-800 text-sm font-medium text-warm-800 dark:text-slate-200 shadow-sm transition flex items-center space-x-2">
          <i data-lucide="sliders" class="w-4 h-4 text-warm-600 dark:text-slate-400"></i>
          <span>設定を変更</span>
        </button>
        <button onclick="openShareModal()" class="px-5 py-2.5 rounded-2xl bg-white dark:bg-slate-900 hover:bg-warm-100/60 dark:hover:bg-slate-800 border border-warm-200 dark:border-slate-800 text-sm font-medium text-warm-800 dark:text-slate-200 shadow-sm transition flex items-center space-x-2">
          <i data-lucide="link" class="w-4 h-4 text-warm-600 dark:text-slate-400"></i>
          <span>URLを共有</span>
        </button>
      </div>

    </div>
  </main>

  <!-- Timer Setup / Edit Modal Dialog -->
  <div id="configModal" class="fixed inset-0 z-50 flex items-center justify-center p-4 bg-warm-950/30 dark:bg-slate-950/70 backdrop-blur-sm opacity-0 pointer-events-none transition-all duration-300">
    <div class="bg-white dark:bg-slate-900 border border-warm-200/80 dark:border-slate-800 rounded-3xl w-full max-w-lg p-6 sm:p-8 shadow-modal transform scale-95 transition-all duration-300">
      
      <!-- Modal Header -->
      <div class="flex items-center justify-between border-b border-warm-100 dark:border-slate-800 pb-4 mb-6">
        <div>
          <h2 class="text-xl font-bold text-warm-900 dark:text-white">タイマー設定</h2>
          <p class="text-xs text-warm-500 dark:text-slate-400 mt-0.5">カウントダウンする目標と日時を入力してください</p>
        </div>
        <button onclick="closeConfigModal()" class="p-2 text-warm-400 hover:text-warm-700 dark:text-slate-500 dark:hover:text-slate-200 rounded-xl hover:bg-warm-100 dark:hover:bg-slate-800 transition">
          <i data-lucide="x" class="w-5 h-5"></i>
        </button>
      </div>

      <!-- Form -->
      <form id="timerForm" onsubmit="handleFormSubmit(event)" class="space-y-6">
        
        <!-- Title Input -->
        <div>
          <label class="block text-sm font-semibold text-warm-800 dark:text-slate-200 mb-2">タイトル / イベント名</label>
          <input type="text" id="inputTitle" placeholder="タイトルを入力 (例: 試験, 発表会)" class="w-full px-4 py-3 rounded-2xl bg-warm-50/50 dark:bg-slate-800/50 border border-warm-200 dark:border-slate-700 text-warm-900 dark:text-slate-100 placeholder-warm-400 dark:placeholder-slate-500 focus:outline-none focus:ring-2 focus:ring-warm-400 dark:focus:ring-slate-600 transition">
          <p class="text-[11px] text-warm-500 dark:text-slate-400 mt-1.5">※ 末尾に「まで」が付いていない場合は自動的に追加されます（例: 「試験」→「試験まで」）</p>
        </div>

        <!-- Target Date & Time -->
        <div>
          <label class="block text-sm font-semibold text-warm-800 dark:text-slate-200 mb-2">目標日時</label>
          <input type="datetime-local" id="inputDateTime" required class="w-full px-4 py-3 rounded-2xl bg-warm-50/50 dark:bg-slate-800/50 border border-warm-200 dark:border-slate-700 text-warm-900 dark:text-slate-100 focus:outline-none focus:ring-2 focus:ring-warm-400 dark:focus:ring-slate-600 transition">
        </div>

        <!-- Quick Presets -->
        <div>
          <span class="block text-xs font-semibold text-warm-500 dark:text-slate-400 uppercase tracking-wider mb-2">クイック追加</span>
          <div class="grid grid-cols-4 gap-2">
            <button type="button" onclick="addPresetTime(10, 'minutes')" class="py-2 px-3 text-xs font-medium bg-warm-100/70 hover:bg-warm-200/70 dark:bg-slate-800 dark:hover:bg-slate-700 text-warm-800 dark:text-slate-200 rounded-xl transition">+10分</button>
            <button type="button" onclick="addPresetTime(1, 'hours')" class="py-2 px-3 text-xs font-medium bg-warm-100/70 hover:bg-warm-200/70 dark:bg-slate-800 dark:hover:bg-slate-700 text-warm-800 dark:text-slate-200 rounded-xl transition">+1時間</button>
            <button type="button" onclick="addPresetTime(1, 'days')" class="py-2 px-3 text-xs font-medium bg-warm-100/70 hover:bg-warm-200/70 dark:bg-slate-800 dark:hover:bg-slate-700 text-warm-800 dark:text-slate-200 rounded-xl transition">+1日</button>
            <button type="button" onclick="addPresetTime(7, 'days')" class="py-2 px-3 text-xs font-medium bg-warm-100/70 hover:bg-warm-200/70 dark:bg-slate-800 dark:hover:bg-slate-700 text-warm-800 dark:text-slate-200 rounded-xl transition">+1週間</button>
          </div>
        </div>

        <!-- Audio Toggle Option -->
        <div class="flex items-center justify-between border-t border-warm-100 dark:border-slate-800 pt-4">
          <div>
            <span class="text-sm font-semibold text-warm-800 dark:text-slate-200 block">到達時の通知音</span>
            <span class="text-xs text-warm-500 dark:text-slate-400 block">カウント終了時にやさしい通知音を再生</span>
          </div>
          <label class="relative inline-flex items-center cursor-pointer">
            <input type="checkbox" id="audioToggle" class="sr-only peer" checked>
            <div class="w-11 h-6 bg-warm-200 dark:bg-slate-700 peer-focus:outline-none rounded-full peer peer-checked:after:translate-x-full peer-checked:after:border-white after:content-[''] after:absolute after:top-[2px] after:left-[2px] after:bg-white after:border-gray-300 after:border after:rounded-full after:h-5 after:w-5 after:transition-all peer-checked:bg-warm-800 dark:peer-checked:bg-slate-200"></div>
          </label>
        </div>

        <!-- Submit Button -->
        <div class="pt-2">
          <button type="submit" class="w-full py-3.5 px-6 rounded-2xl bg-warm-900 hover:bg-warm-800 dark:bg-slate-100 dark:hover:bg-white text-white dark:text-slate-900 font-bold text-sm shadow-soft transition">
            タイマーを開始
          </button>
        </div>

      </form>
    </div>
  </div>

  <!-- Share URL Modal Dialog -->
  <div id="shareModal" class="fixed inset-0 z-50 flex items-center justify-center p-4 bg-warm-950/30 dark:bg-slate-950/70 backdrop-blur-sm opacity-0 pointer-events-none transition-all duration-300">
    <div class="bg-white dark:bg-slate-900 border border-warm-200/80 dark:border-slate-800 rounded-3xl w-full max-w-md p-6 sm:p-8 shadow-modal transform scale-95 transition-all duration-300">
      
      <div class="flex items-center justify-between border-b border-warm-100 dark:border-slate-800 pb-4 mb-6">
        <div>
          <h2 class="text-xl font-bold text-warm-900 dark:text-white">URLを共有</h2>
          <p class="text-xs text-warm-500 dark:text-slate-400 mt-0.5">設定したカウントダウンを他の人に送ることができます</p>
        </div>
        <button onclick="closeShareModal()" class="p-2 text-warm-400 hover:text-warm-700 dark:text-slate-500 dark:hover:text-slate-200 rounded-xl hover:bg-warm-100 dark:hover:bg-slate-800 transition">
          <i data-lucide="x" class="w-5 h-5"></i>
        </button>
      </div>

      <!-- Copy Box -->
      <div class="space-y-4">
        <div class="flex items-center space-x-2">
          <input type="text" id="shareUrlInput" readonly class="w-full px-4 py-3 rounded-2xl bg-warm-50/50 dark:bg-slate-800/50 border border-warm-200 dark:border-slate-700 text-warm-800 dark:text-slate-200 text-xs font-mono focus:outline-none select-all">
          <button onclick="copyShareUrl()" id="copyBtn" class="px-5 py-3 rounded-2xl bg-warm-900 hover:bg-warm-800 dark:bg-slate-100 dark:hover:bg-white text-white dark:text-slate-900 font-bold text-xs transition flex items-center space-x-1.5 whitespace-nowrap shadow-sm">
            <i data-lucide="copy" class="w-4 h-4"></i>
            <span id="copyBtnText">URLをコピー</span>
          </button>
        </div>

        <div class="p-3.5 rounded-2xl bg-warm-100/50 dark:bg-slate-800/40 border border-warm-200/50 dark:border-slate-800 flex items-start space-x-2.5">
          <i data-lucide="info" class="w-4 h-4 text-warm-500 dark:text-slate-400 mt-0.5 flex-shrink-0"></i>
          <p class="text-xs text-warm-600 dark:text-slate-400 leading-relaxed">
            このリンクを開いた人は、同じタイトルと目標日時でカウントダウンタイマーを閲覧できます。
          </p>
        </div>
      </div>

    </div>
  </div>

  <!-- Footer -->
  <footer class="w-full border-t border-warm-200/50 dark:border-slate-800/60 py-6 text-center text-xs text-warm-500 dark:text-slate-500">
    <p>カウントダウンタイマー &copy; 2026</p>
  </footer>

  <!-- Main JavaScript Logic -->
  <script>
    // Global Application State
    let timerState = {
      title: "",
      targetIso: "",
      audioAlert: true
    };

    let timerInterval = null;
    let audioContext = null;

    // Helper: Normalize title so it ALWAYS ends with "まで"
    function formatTitle(rawTitle) {
      if (!rawTitle || rawTitle.trim() === "") {
        return "目標日時まで";
      }
      const trimmed = rawTitle.trim();
      return trimmed.endsWith("まで") ? trimmed : `${trimmed}まで`;
    }

    // App Startup
    window.onload = function() {
      lucide.createIcons();
      
      const hasUrlParams = parseUrlParams();

      if (!hasUrlParams) {
        // First visit with no shared URL params -> Open setup dialog directly
        setDefaultTargetDate();
        document.getElementById('inputTitle').value = ""; // Empty title default
        openConfigModal();
      } else {
        syncStateToUI();
        startCountdown();
      }
    };

    // Parse URL Hash Parameters (#title=...&target=...)
    function parseUrlParams() {
      const hash = window.location.hash.substring(1);
      if (!hash) return false;

      const params = new URLSearchParams(hash);
      const titleParam = params.get('title');
      const targetParam = params.get('target');

      if (!targetParam) return false;

      if (titleParam) {
        timerState.title = formatTitle(decodeURIComponent(titleParam));
      } else {
        timerState.title = "目標日時まで";
      }

      const targetDate = new Date(isNaN(targetParam) ? targetParam : parseInt(targetParam));
      if (isNaN(targetDate.getTime())) return false;

      timerState.targetIso = targetDate.toISOString();
      return true;
    }

    // Default target date: 24 hours from now
    function setDefaultTargetDate() {
      const future = new Date();
      future.setHours(future.getHours() + 24);
      timerState.targetIso = future.toISOString();
    }

    // Sync State to UI Components
    function syncStateToUI() {
      const currentTitle = formatTitle(timerState.title);
      timerState.title = currentTitle;

      // Update Page Display Title
      document.getElementById('displayTitle').innerText = currentTitle;

      // Format Date String (Japanese format)
      const targetDate = new Date(timerState.targetIso);
      const year = targetDate.getFullYear();
      const month = String(targetDate.getMonth() + 1).padStart(2, '0');
      const day = String(targetDate.getDate()).padStart(2, '0');
      const hours = String(targetDate.getHours()).padStart(2, '0');
      const minutes = String(targetDate.getMinutes()).padStart(2, '0');

      document.getElementById('displayTargetDate').innerText = `${year}年${month}月${day}日 ${hours}:${minutes}`;

      // Sync Input Fields
      const inputTitleEl = document.getElementById('inputTitle');
      if (timerState.title && timerState.title !== "目標日時まで") {
        inputTitleEl.value = timerState.title.replace(/まで$/, '');
      }

      // Format datetime-local
      const localIso = new Date(targetDate.getTime() - targetDate.getTimezoneOffset() * 60000).toISOString().slice(0, 16);
      document.getElementById('inputDateTime').value = localIso;
    }

    // Start Countdown Loop
    function startCountdown() {
      if (timerInterval) clearInterval(timerInterval);

      function update() {
        const now = new Date().getTime();
        const target = new Date(timerState.targetIso).getTime();
        const diff = target - now;

        const timeUpBanner = document.getElementById('timeUpBanner');

        if (diff <= 0) {
          document.getElementById('daysVal').innerText = "00";
          document.getElementById('hoursVal').innerText = "00";
          document.getElementById('minsVal').innerText = "00";
          document.getElementById('secsVal').innerText = "00";

          document.title = `【完了】${timerState.title}`;

          if (timeUpBanner.classList.contains('hidden')) {
            timeUpBanner.classList.remove('hidden');
            if (timerState.audioAlert) {
              playChimeSound();
            }
          }
          return;
        } else {
          timeUpBanner.classList.add('hidden');
        }

        // Calculate time units
        const days = Math.floor(diff / (1000 * 60 * 60 * 24));
        const hours = Math.floor((diff % (1000 * 60 * 60 * 24)) / (1000 * 60 * 60));
        const mins = Math.floor((diff % (1000 * 60 * 60)) / (1000 * 60));
        const secs = Math.floor((diff % (1000 * 60)) / 1000);

        document.getElementById('daysVal').innerText = String(days).padStart(2, '0');
        document.getElementById('hoursVal').innerText = String(hours).padStart(2, '0');
        document.getElementById('minsVal').innerText = String(mins).padStart(2, '0');
        document.getElementById('secsVal').innerText = String(secs).padStart(2, '0');

        // Dynamic Browser Tab Title Update
        document.title = `${days}日 ${hours}:${String(mins).padStart(2, '0')}:${String(secs).padStart(2, '0')} - ${timerState.title}`;
      }

      update();
      timerInterval = setInterval(update, 1000);
    }

    // Preset Time Addition Helpers
    function addPresetTime(amount, unit) {
      const dtInput = document.getElementById('inputDateTime');
      let baseDate = dtInput.value ? new Date(dtInput.value) : new Date();
      if (isNaN(baseDate.getTime())) baseDate = new Date();

      if (unit === 'minutes') {
        baseDate.setMinutes(baseDate.getMinutes() + amount);
      } else if (unit === 'hours') {
        baseDate.setHours(baseDate.getHours() + amount);
      } else if (unit === 'days') {
        baseDate.setDate(baseDate.getDate() + amount);
      }

      const localIso = new Date(baseDate.getTime() - baseDate.getTimezoneOffset() * 60000).toISOString().slice(0, 16);
      dtInput.value = localIso;
    }

    // Form Submission Handler
    function handleFormSubmit(e) {
      e.preventDefault();

      const rawTitle = document.getElementById('inputTitle').value;
      const dateTimeVal = document.getElementById('inputDateTime').value;
      const audioVal = document.getElementById('audioToggle').checked;

      if (!dateTimeVal) return;

      const targetDate = new Date(dateTimeVal);

      timerState.title = formatTitle(rawTitle);
      timerState.targetIso = targetDate.toISOString();
      timerState.audioAlert = audioVal;

      updateUrlHash();
      syncStateToUI();
      startCountdown();
      closeConfigModal();
    }

    // Update URL Hash (#title=...&target=...)
    function updateUrlHash() {
      const params = new URLSearchParams();
      params.set('title', encodeURIComponent(timerState.title));
      params.set('target', new Date(timerState.targetIso).getTime());

      window.history.replaceState(null, '', '#' + params.toString());
    }

    // Modal Control Functions
    function openConfigModal() {
      const modal = document.getElementById('configModal');
      const container = modal.firstElementChild;
      modal.classList.remove('opacity-0', 'pointer-events-none');
      container.classList.remove('scale-95');
      container.classList.add('scale-100');
    }

    function closeConfigModal() {
      const modal = document.getElementById('configModal');
      const container = modal.firstElementChild;
      modal.classList.add('opacity-0', 'pointer-events-none');
      container.classList.remove('scale-100');
      container.classList.add('scale-95');
    }

    function openShareModal() {
      updateUrlHash();
      const shareUrlInput = document.getElementById('shareUrlInput');
      shareUrlInput.value = window.location.href;

      const modal = document.getElementById('shareModal');
      const container = modal.firstElementChild;
      modal.classList.remove('opacity-0', 'pointer-events-none');
      container.classList.remove('scale-95');
      container.classList.add('scale-100');
    }

    function closeShareModal() {
      const modal = document.getElementById('shareModal');
      const container = modal.firstElementChild;
      modal.classList.add('opacity-0', 'pointer-events-none');
      container.classList.remove('scale-100');
      container.classList.add('scale-95');
    }

    // Copy URL to Clipboard
    function copyShareUrl() {
      const shareUrlInput = document.getElementById('shareUrlInput');
      shareUrlInput.select();

      try {
        document.execCommand('copy');
        const btnText = document.getElementById('copyBtnText');
        btnText.innerText = 'コピーしました！';
        setTimeout(() => {
          btnText.innerText = 'URLをコピー';
        }, 2000);
      } catch (err) {
        console.error('Copy failed: ', err);
      }
    }

    // Dark Mode Toggle
    function toggleDarkMode() {
      const html = document.documentElement;
      html.classList.toggle('dark');
    }

    // Play Gentle Audio Chime when timer reaches zero
    function playChimeSound() {
      try {
        if (!audioContext) {
          audioContext = new (window.AudioContext || window.webkitAudioContext)();
        }

        const now = audioContext.currentTime;
        const osc = audioContext.createOscillator();
        const gain = audioContext.createGain();

        // Soft bell-like frequency sequence
        osc.type = 'sine';
        osc.frequency.setValueAtTime(523.25, now); // C5
        osc.frequency.exponentialRampToValueAtTime(659.25, now + 0.3); // E5
        osc.frequency.exponentialRampToValueAtTime(783.99, now + 0.6); // G5

        gain.gain.setValueAtTime(0.2, now);
        gain.gain.exponentialRampToValueAtTime(0.001, now + 1.5);

        osc.connect(gain);
        gain.connect(audioContext.destination);

        osc.start(now);
        osc.stop(now + 1.5);
      } catch (e) {
        console.warn('Audio tone could not be played:', e);
      }
    }
  </script>
</body>
</html>
