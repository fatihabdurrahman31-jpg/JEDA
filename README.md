<!DOCTYPE html>
<html lang="id">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>JEDA — Jeda Evaluatif Digital Asesmen</title>
  <script src="https://cdn.tailwindcss.com"></script>
  <link href="https://fonts.googleapis.com/css2?family=Inter:wght@300;400;500;600;700&display=swap" rel="stylesheet">
  <script src="https://unpkg.com/lucide@latest"></script>
  <style>
    :root{--bg:#020204;--glass-border:rgba(255,255,255,0.08);--cyan:#22d3ee;--cyan2:#06b6d4}
    *{margin:0;padding:0;box-sizing:border-box}
    body{font-family:'Inter',sans-serif;background:var(--bg);color:#fff;-webkit-font-smoothing:antialiased;overflow-x:hidden}
    .grid-bg{background-image:linear-gradient(rgba(255,255,255,0.015) 1px,transparent 1px),linear-gradient(90deg,rgba(255,255,255,0.015) 1px,transparent 1px);background-size:50px 50px}
    .glass{background:rgba(10,15,30,0.6);backdrop-filter:blur(20px);-webkit-backdrop-filter:blur(20px);border:1px solid var(--glass-border);box-shadow:0 0 0 1px rgba(255,255,255,0.04),0 20px 50px -12px rgba(0,0,0,0.5)}
    .glass-nav{background:rgba(3,5,8,0.7);backdrop-filter:blur(24px);-webkit-backdrop-filter:blur(24px);border-bottom:1px solid rgba(255,255,255,0.05)}
    .text-cyan-g{background:linear-gradient(135deg,#22d3ee,#06b6d4);-webkit-background-clip:text;-webkit-text-fill-color:transparent;background-clip:text}
    .text-gray-g{background:linear-gradient(to right,#94a3b8,#475569);-webkit-background-clip:text;-webkit-text-fill-color:transparent;background-clip:text}

    /* ambient */
    .amb{position:fixed;inset:0;z-index:0;pointer-events:none}
    .amb .o{position:absolute;border-radius:50%;filter:blur(100px)}
    .amb .o1{width:600px;height:600px;background:radial-gradient(circle,rgba(34,211,238,0.06),transparent 70%);top:-10%;left:-5%;animation:fl 25s ease-in-out infinite}
    .amb .o2{width:400px;height:400px;background:radial-gradient(circle,rgba(6,182,212,0.04),transparent 70%);bottom:-5%;right:-5%;animation:fl 30s ease-in-out infinite reverse}
    @keyframes fl{0%,100%{transform:translate(0,0) scale(1)}50%{transform:translate(25px,-15px) scale(1.04)}}

    /* tabs */
    .tab-btn{position:relative;transition:all .3s}
    .tab-btn::after{content:'';position:absolute;bottom:-1px;left:0;right:0;height:2px;background:transparent;transition:all .3s;border-radius:1px}
    .tab-btn.active{color:#fff}
    .tab-btn.active::after{background:#22d3ee;box-shadow:0 0 10px rgba(34,211,238,0.4)}
    .tab-btn:not(.active):hover{color:#94a3b8}

    /* lock overlay */
    .lock-overlay{position:absolute;inset:0;background:rgba(2,2,4,0.85);backdrop-filter:blur(8px);-webkit-backdrop-filter:blur(8px);border-radius:12px;display:flex;flex-direction:column;align-items:center;justify-content:center;gap:12px;transition:all .6s cubic-bezier(.16,1,.3,1);z-index:10}
    .lock-overlay.unlocked{opacity:0;pointer-events:none;transform:scale(1.05)}
    .lock-icon{width:48px;height:48px;border-radius:50%;display:flex;align-items:center;justify-content:center;background:rgba(239,68,68,0.1);border:1px solid rgba(239,68,68,0.2);animation:lock-pulse 2s ease-in-out infinite}
    @keyframes lock-pulse{0%,100%{box-shadow:0 0 0 0 rgba(239,68,68,0.15)}50%{box-shadow:0 0 0 12px rgba(239,68,68,0)}}
    .unlock-icon{width:48px;height:48px;border-radius:50%;display:flex;align-items:center;justify-content:center;background:rgba(34,211,238,0.1);border:1px solid rgba(34,211,238,0.2);animation:unlock-glow 1.5s ease-out forwards}
    @keyframes unlock-glow{0%{box-shadow:0 0 0 0 rgba(34,211,238,0.4)}100%{box-shadow:0 0 0 20px rgba(34,211,238,0)}}

    /* effort meter */
    .effort-bar{height:4px;border-radius:2px;background:rgba(255,255,255,0.06);overflow:hidden}
    .effort-fill{height:100%;border-radius:2px;transition:all .5s cubic-bezier(.16,1,.3,1);width:0%}

    /* timeline */
    .tl-line{position:absolute;left:15px;top:32px;bottom:0;width:1px;background:linear-gradient(to bottom,rgba(34,211,238,0.2),transparent)}
    .tl-dot{width:10px;height:10px;border-radius:50%;flex-shrink:0;position:relative;z-index:2}
    .tl-dot.cyan{background:#22d3ee;box-shadow:0 0 8px rgba(34,211,238,0.4)}
    .tl-dot.slate{background:#475569}
    .tl-dot.amber{background:#f59e0b;box-shadow:0 0 8px rgba(245,158,11,0.3)}

    /* calibration gauge */
    .gauge-ring{transition:stroke-dashoffset 1.5s cubic-bezier(.16,1,.3,1)}

    /* checkpoint badge */
    .cp-badge{display:inline-flex;align-items:center;gap:6px;padding:4px 10px;border-radius:6px;font-size:10px;font-weight:600;letter-spacing:0.05em;text-transform:uppercase}
    .cp-badge.pending{background:rgba(245,158,11,0.1);color:#f59e0b;border:1px solid rgba(245,158,11,0.2)}
    .cp-badge.verified{background:rgba(16,185,129,0.1);color:#10b981;border:1px solid rgba(16,185,129,0.2)}
    .cp-badge.blocked{background:rgba(239,68,68,0.1);color:#ef4444;border:1px solid rgba(239,68,68,0.2)}

    /* scenario toggle */
    .sc-btn{padding:8px 16px;border-radius:8px;font-size:12px;font-weight:500;transition:all .3s;cursor:pointer;border:1px solid rgba(255,255,255,0.08);background:transparent;color:#64748b}
    .sc-btn.active{background:rgba(34,211,238,0.08);border-color:rgba(34,211,238,0.25);color:#22d3ee}

    /* btns */
    .btn-p{background:linear-gradient(to right,#164e63,#115e59);box-shadow:0 0 18px rgba(34,211,238,0.18);transition:all .3s}
    .btn-p:hover:not(:disabled){transform:translateY(-1px);box-shadow:0 0 28px rgba(34,211,238,0.3)}
    .btn-p:disabled{opacity:.35;cursor:not-allowed;box-shadow:none;transform:none}
    .btn-s{background:rgba(255,255,255,0.04);border:1px solid rgba(255,255,255,0.1);transition:all .3s}
    .btn-s:hover{background:rgba(255,255,255,0.08)}
    .btn-d{background:rgba(239,68,68,0.1);border:1px solid rgba(239,68,68,0.15);color:#f87171;transition:all .3s}
    .btn-d:hover{background:rgba(239,68,68,0.15)}

    /* textarea */
    textarea{resize:none;field-sizing:content;min-height:60px;max-height:200px}
    textarea:focus{outline:none;border-color:rgba(34,211,238,0.3);box-shadow:0 0 0 3px rgba(34,211,238,0.08)}

    /* toast */
    .toast{position:fixed;top:80px;right:24px;z-index:9999;padding:14px 20px;border-radius:12px;font-size:13px;font-weight:500;display:flex;align-items:center;gap:10px;transform:translateX(120%);transition:transform .4s cubic-bezier(.16,1,.3,1);pointer-events:none}
    .toast.show{transform:translateX(0);pointer-events:auto}
    .toast.success{background:rgba(16,185,129,0.15);border:1px solid rgba(16,185,129,0.25);color:#10b981}
    .toast.warning{background:rgba(245,158,11,0.15);border:1px solid rgba(245,158,11,0.25);color:#f59e0b}
    .toast.error{background:rgba(239,68,68,0.15);border:1px solid rgba(239,68,68,0.25);color:#ef4444}
    .toast.info{background:rgba(34,211,238,0.15);border:1px solid rgba(34,211,238,0.25);color:#22d3ee}

    /* fade in */
    @keyframes fiu{from{opacity:0;transform:translateY(10px)}to{opacity:1;transform:translateY(0)}}
    .fiu{animation:fiu .4s ease-out forwards;opacity:0}

    /* scroll */
    ::-webkit-scrollbar{width:4px}::-webkit-scrollbar-track{background:transparent}::-webkit-scrollbar-thumb{background:rgba(255,255,255,0.08);border-radius:10px}

    /* calibration zones */
    .zone-over{color:#ef4444}
    .zone-good{color:#10b981}
    .zone-under{color:#f59e0b}

    /* reflection option */
    .ref-opt{padding:12px 16px;border-radius:10px;border:1px solid rgba(255,255,255,0.06);background:rgba(255,255,255,0.02);cursor:pointer;transition:all .3s;font-size:13px}
    .ref-opt:hover{border-color:rgba(34,211,238,0.2);background:rgba(34,211,238,0.04)}
    .ref-opt.selected{border-color:rgba(34,211,238,0.35);background:rgba(34,211,238,0.08)}
    .ref-opt.correct{border-color:rgba(16,185,129,0.4);background:rgba(16,185,129,0.08)}
    .ref-opt.wrong{border-color:rgba(239,68,68,0.3);background:rgba(239,68,68,0.06)}

    /* responsive */
    @media(max-width:768px){
      .tab-btn span.tab-label{display:none}
      .tab-btn{padding:8px 12px !important}
    }
  </style>
</head>
<body class="grid-bg">

<div class="amb"><div class="o o1"></div><div class="o o2"></div></div>

<!-- Toast -->
<div id="toast" class="toast"></div>

<!-- NAV -->
<nav class="glass-nav fixed top-0 left-0 right-0 z-50 h-14 flex items-center px-5">
  <div class="max-w-7xl mx-auto w-full flex items-center justify-between">
    <div class="flex items-center gap-3">
      <div class="w-8 h-8 rounded-lg flex items-center justify-center" style="background:linear-gradient(135deg,rgba(34,211,238,0.2),rgba(6,182,212,0.1));border:1px solid rgba(34,211,238,0.15)">
        <i data-lucide="pause-circle" class="w-4 h-4 text-cyan-400"></i>
      </div>
      <div>
        <span class="text-sm font-semibold tracking-tight">JEDA</span>
        <span class="hidden sm:inline text-[10px] text-slate-600 ml-2">Jeda Evaluatif Digital Asesmen</span>
      </div>
    </div>
    <div class="flex items-center gap-3">
      <div class="hidden md:flex items-center gap-1.5 px-3 py-1.5 rounded-lg" style="background:rgba(16,185,129,0.08);border:1px solid rgba(16,185,129,0.15)">
        <span class="w-1.5 h-1.5 rounded-full bg-emerald-400"></span>
        <span class="text-[10px] font-medium text-emerald-400">Prototipe Aktif</span>
      </div>
      <div class="flex items-center gap-1 px-3 py-1.5 rounded-lg" style="background:rgba(255,255,255,0.03);border:1px solid rgba(255,255,255,0.06)">
        <i data-lucide="user" class="w-3 h-3 text-slate-500"></i>
        <span class="text-[11px] text-slate-400 font-medium" id="userName">Raka</span>
      </div>
    </div>
  </div>
</nav>

<!-- MAIN -->
<main class="relative z-10 pt-14 min-h-screen flex flex-col">

  <!-- Scenario Bar -->
  <div class="px-5 py-4" style="border-bottom:1px solid rgba(255,255,255,0.05)">
    <div class="max-w-7xl mx-auto flex flex-col sm:flex-row items-start sm:items-center justify-between gap-3">
      <div>
        <p class="text-[10px] font-medium tracking-widest uppercase text-slate-600 mb-1">Skenario Simulasi</p>
        <p class="text-xs text-slate-400 font-light">Pilih konteks untuk menjalankan prototipe JEDA</p>
      </div>
      <div class="flex gap-2">
        <button class="sc-btn active" onclick="switchScenario('academic',this)">
          <span class="flex items-center gap-1.5"><i data-lucide="graduation-cap" class="w-3.5 h-3.5"></i> Akademik</span>
        </button>
        <button class="sc-btn" onclick="switchScenario('org',this)">
          <span class="flex items-center gap-1.5"><i data-lucide="building-2" class="w-3.5 h-3.5"></i> Organisasi</span>
        </button>
      </div>
    </div>
  </div>

  <!-- Tabs -->
  <div class="px-5" style="border-bottom:1px solid rgba(255,255,255,0.05)">
    <div class="max-w-7xl mx-auto flex gap-1 overflow-x-auto">
      <button class="tab-btn active flex items-center gap-2 px-4 py-3.5 text-sm font-medium text-slate-500" onclick="switchTab('J',this)">
        <span class="w-6 h-6 rounded-md flex items-center justify-center text-[10px] font-bold bg-cyan-400/10 text-cyan-400 border border-cyan-400/20">J</span>
        <span class="tab-label">Delegation Lock</span>
      </button>
      <button class="tab-btn flex items-center gap-2 px-4 py-3.5 text-sm font-medium text-slate-500" onclick="switchTab('E',this)">
        <span class="w-6 h-6 rounded-md flex items-center justify-center text-[10px] font-bold bg-cyan-400/10 text-cyan-400 border border-cyan-400/20">E</span>
        <span class="tab-label">Human Checkpoint</span>
      </button>
      <button class="tab-btn flex items-center gap-2 px-4 py-3.5 text-sm font-medium text-slate-500" onclick="switchTab('D',this)">
        <span class="w-6 h-6 rounded-md flex items-center justify-center text-[10px] font-bold bg-cyan-400/10 text-cyan-400 border border-cyan-400/20">D</span>
        <span class="tab-label">Process Trail</span>
      </button>
      <button class="tab-btn flex items-center gap-2 px-4 py-3.5 text-sm font-medium text-slate-500" onclick="switchTab('A',this)">
        <span class="w-6 h-6 rounded-md flex items-center justify-center text-[10px] font-bold bg-cyan-400/10 text-cyan-400 border border-cyan-400/20">A</span>
        <span class="tab-label">Reflection Loop</span>
      </button>
    </div>
  </div>

  <!-- Tab Content -->
  <div class="flex-1 px-5 py-6">
    <div class="max-w-7xl mx-auto">

      <!-- ========== MODUL J — DELEGATION LOCK ========== -->
      <div id="tab-J" class="tab-content">
        <!-- Header -->
        <div class="mb-6 fiu">
          <div class="flex items-center gap-3 mb-2">
            <div class="w-10 h-10 rounded-xl flex items-center justify-center" style="background:rgba(34,211,238,0.08);border:1px solid rgba(34,211,238,0.15)">
              <i data-lucide="lock" class="w-5 h-5 text-cyan-400"></i>
            </div>
            <div>
              <h2 class="text-lg font-medium">Modul Jeda — Delegation Lock</h2>
              <p class="text-xs text-slate-500 font-light">AI terkunci sampai kamu menulis kerangka jawaban manual terlebih dahulu</p>
            </div>
          </div>
        </div>

        <!-- Task Card -->
        <div class="glass rounded-xl p-5 mb-5 fiu" style="animation-delay:.1s">
          <div class="flex items-center gap-2 mb-3">
            <span class="cp-badge blocked"><i data-lucide="shield-alert" class="w-3 h-3"></i> Checkpoint Aktif</span>
            <span class="text-[10px] text-slate-600" id="taskContext">Konteks: Skripsi — Analisis Data</span>
          </div>
          <p class="text-sm font-medium text-white mb-2" id="taskTitle">Tugas: Pilih dan jelaskan uji statistik untuk penelitianmu</p>
          <p class="text-xs text-slate-400 font-light leading-relaxed" id="taskDesc">Sebelum bertanya kepada AI, tuliskan dulu pemahamanmu sendiri — selemah apa pun itu. Ini bukan dinilai benar atau salah, tapi untuk mengaktifkan proses berpikirmu sebelum menerima bantuan.</p>
        </div>

        <div class="grid grid-cols-1 lg:grid-cols-2 gap-5">
          <!-- Manual Input -->
          <div class="fiu" style="animation-delay:.15s">
            <div class="flex items-center justify-between mb-3">
              <p class="text-xs font-medium text-slate-400 flex items-center gap-1.5">
                <i data-lucide="pencil" class="w-3.5 h-3.5 text-cyan-400"></i> Jawaban Manual Kamu
              </p>
              <span class="text-[10px] text-slate-600" id="charCount">0 karakter</span>
            </div>
            <div class="effort-bar mb-3">
              <div id="effortFill" class="effort-fill" style="background:linear-gradient(to right,#164e63,#22d3ee)"></div>
            </div>
            <p class="text-[10px] text-slate-600 mb-2" id="effortLabel">Usaha kognitif: Belum terdeteksi</p>
            <textarea id="manualInput" class="w-full bg-white/[0.03] border border-white/[0.08] rounded-xl px-4 py-3 text-sm font-light text-white placeholder:text-slate-600 leading-relaxed" placeholder="Tuliskan pemahamanmu sendiri di sini...&#10;&#10;Contoh: &quot;Menurut saya uji yang cocok adalah... karena...&quot;" oninput="updateEffort()"></textarea>
            <div class="flex items-center gap-2 mt-3">
              <button id="submitManualBtn" disabled onclick="submitManual()" class="btn-p px-5 py-2.5 rounded-lg text-xs font-semibold text-white flex items-center gap-2">
                <i data-lucide="check" class="w-3.5 h-3.5"></i> Kirim & Buka Kunci AI
              </button>
              <button onclick="clearManual()" class="btn-s px-4 py-2.5 rounded-lg text-xs font-medium text-slate-400">Bersihkan</button>
            </div>
          </div>

          <!-- AI Response (Locked) -->
          <div class="fiu" style="animation-delay:.2s">
            <div class="flex items-center justify-between mb-3">
              <p class="text-xs font-medium text-slate-400 flex items-center gap-1.5">
                <i data-lucide="sparkles" class="w-3.5 h-3.5 text-cyan-400"></i> Respons AI
              </p>
              <span id="lockBadge" class="cp-badge blocked"><i data-lucide="lock" class="w-3 h-3"></i> Terkunci</span>
            </div>
            <div class="relative" style="min-height:200px">
              <div id="aiResponse" class="bg-white/[0.02] border border-white/[0.06] rounded-xl p-4 text-sm font-light text-slate-300 leading-relaxed" style="min-height:200px">
                <p class="text-slate-600 italic text-xs">Respons AI akan muncul di sini setelah kamu menyelesaikan checkpoint...</p>
              </div>
              <div id="lockOverlay" class="lock-overlay">
                <div class="lock-icon">
                  <i data-lucide="lock" class="w-5 h-5 text-red-400"></i>
                </div>
                <p class="text-xs font-medium text-red-400">AI Terkunci</p>
                <p class="text-[10px] text-slate-500 text-center max-w-[200px]">Tulis jawaban manual dulu untuk membuka kunci akses AI</p>
              </div>
            </div>
          </div>
        </div>

        <!-- Comparison (hidden initially) -->
        <div id="comparisonSection" class="mt-6 hidden">
          <div class="glass rounded-xl p-5">
            <p class="text-xs font-medium text-slate-400 mb-4 flex items-center gap-2">
              <i data-lucide="git-compare" class="w-4 h-4 text-cyan-400"></i> Perbandingan: Jawaban Kamu vs AI
            </p>
            <div class="grid grid-cols-1 md:grid-cols-2 gap-4">
              <div class="p-4 rounded-lg" style="background:rgba(255,255,255,0.02);border:1px solid rgba(255,255,255,0.06)">
                <p class="text-[10px] font-semibold text-slate-500 uppercase tracking-wider mb-2">Jawaban Manual</p>
                <p id="compManual" class="text-xs text-slate-300 font-light leading-relaxed"></p>
              </div>
              <div class="p-4 rounded-lg" style="background:rgba(34,211,238,0.03);border:1px solid rgba(34,211,238,0.08)">
                <p class="text-[10px] font-semibold text-cyan-400/70 uppercase tracking-wider mb-2">Respons AI</p>
                <p id="compAI" class="text-xs text-slate-300 font-light leading-relaxed"></p>
              </div>
            </div>
            <div class="mt-4 p-3 rounded-lg" style="background:rgba(34,211,238,0.04);border:1px solid rgba(34,211,238,0.1)">
              <p class="text-[11px] text-cyan-400/80 font-light leading-relaxed">
                <i data-lucide="info" class="w-3 h-3 inline mr-1"></i>
                <strong>Prinsip JEDA:</strong> AI bukan pengganti berpikirmu — ia penyempurna. Dengan menulis dulu, kamu sudah membentuk kerangka kognitif yang membuat respons AI berguna sebagai perbandingan, bukan sebagai pengganti pemahaman.
              </p>
            </div>
          </div>
        </div>
      </div>

      <!-- ========== MODUL E — HUMAN CHECKPOINT ========== -->
      <div id="tab-E" class="tab-content hidden">
        <div class="mb-6 fiu">
          <div class="flex items-center gap-3 mb-2">
            <div class="w-10 h-10 rounded-xl flex items-center justify-center" style="background:rgba(34,211,238,0.08);border:1px solid rgba(34,211,238,0.15)">
              <i data-lucide="shield-check" class="w-5 h-5 text-cyan-400"></i>
            </div>
            <div>
              <h2 class="text-lg font-medium">Modul Evaluasi — Human Checkpoint</h2>
              <p class="text-xs text-slate-500 font-light">Keputusan berdampak manusia lain wajib diverifikasi tatap muka sebelum disahkan</p>
            </div>
          </div>
        </div>

        <!-- Stats -->
        <div class="grid grid-cols-3 gap-4 mb-6 fiu" style="animation-delay:.1s">
          <div class="glass rounded-xl p-4 text-center">
            <p class="text-2xl font-medium text-white" id="hcTotal">5</p>
            <p class="text-[10px] text-slate-500 font-light">Total Keputusan</p>
          </div>
          <div class="glass rounded-xl p-4 text-center">
            <p class="text-2xl font-medium text-amber-400" id="hcPending">3</p>
            <p class="text-[10px] text-slate-500 font-light">Menunggu Verifikasi</p>
          </div>
          <div class="glass rounded-xl p-4 text-center">
            <p class="text-2xl font-medium text-emerald-400" id="hcVerified">2</p>
            <p class="text-[10px] text-slate-500 font-light">Terverifikasi</p>
          </div>
        </div>

        <!-- Decision List -->
        <div class="space-y-3" id="decisionList">
          <!-- populated by JS -->
        </div>
      </div>

      <!-- ========== MODUL D — PROCESS TRAIL ========== -->
      <div id="tab-D" class="tab-content hidden">
        <div class="mb-6 fiu">
          <div class="flex items-center gap-3 mb-2">
            <div class="w-10 h-10 rounded-xl flex items-center justify-center" style="background:rgba(34,211,238,0.08);border:1px solid rgba(34,211,238,0.15)">
              <i data-lucide="footprints" class="w-5 h-5 text-cyan-400"></i>
            </div>
            <div>
              <h2 class="text-lg font-medium">Modul Dokumentasi — Process Trail</h2>
              <p class="text-xs text-slate-500 font-light">Jejak berpikir yang terekam: draf, revisi, keraguan, dan alasan di balik setiap pilihan</p>
            </div>
          </div>
        </div>

        <!-- Trail Toggle -->
        <div class="flex items-center gap-3 mb-5 fiu" style="animation-delay:.1s">
          <button id="trailAutoBtn" class="sc-btn active" onclick="setTrailMode('auto',this)">Otomatis (Demo)</button>
          <button id="trailManualBtn" class="sc-btn" onclick="setTrailMode('manual',this)">Manual (Input Sendiri)</button>
        </div>

        <!-- Manual Input (hidden) -->
        <div id="trailManualInput" class="hidden mb-5">
          <div class="glass rounded-xl p-4">
            <div class="flex gap-2 mb-3">
              <button class="trail-type-btn sc-btn active" onclick="setTrailType('thought',this)" data-type="thought">
                <i data-lucide="lightbulb" class="w-3 h-3 inline mr-1"></i>Pemikiran
              </button>
              <button class="trail-type-btn sc-btn" onclick="setTrailType('doubt',this)" data-type="doubt">
                <i data-lucide="help-circle" class="w-3 h-3 inline mr-1"></i>Keraguan
              </button>
              <button class="trail-type-btn sc-btn" onclick="setTrailType('revision',this)" data-type="revision">
                <i data-lucide="refresh-cw" class="w-3 h-3 inline mr-1"></i>Revisi
              </button>
              <button class="trail-type-btn sc-btn" onclick="setTrailType('decision',this)" data-type="decision">
                <i data-lucide="check-circle" class="w-3 h-3 inline mr-1"></i>Keputusan
              </button>
            </div>
            <textarea id="trailInput" class="w-full bg-white/[0.03] border border-white/[0.08] rounded-lg px-3 py-2.5 text-sm font-light text-white placeholder:text-slate-600" placeholder="Tuliskan jejak berpikirmu..."></textarea>
            <button onclick="addTrailEntry()" class="btn-p px-4 py-2 rounded-lg text-xs font-semibold text-white mt-2">Tambah ke Jejak</button>
          </div>
        </div>

        <!-- Timeline -->
        <div class="glass rounded-xl p-5 fiu" style="animation-delay:.15s">
          <p class="text-xs font-medium text-slate-500 mb-5 flex items-center gap-2">
            <i data-lucide="activity" class="w-3.5 h-3.5 text-cyan-400"></i> Timeline Jejak Berpikir
            <span class="text-[10px] text-slate-600" id="trailCount">0 entri</span>
          </p>
          <div id="trailTimeline" class="relative pl-8 space-y-5">
            <p class="text-xs text-slate-600 italic" id="trailEmpty">Belum ada jejak berpikir. Mulai berpikir, dan jejaknya akan terekam di sini...</p>
          </div>
        </div>
      </div>

      <!-- ========== MODUL A — REFLECTION LOOP ========== -->
      <div id="tab-A" class="tab-content hidden">
        <div class="mb-6 fiu">
          <div class="flex items-center gap-3 mb-2">
            <div class="w-10 h-10 rounded-xl flex items-center justify-center" style="background:rgba(34,211,238,0.08);border:1px solid rgba(34,211,238,0.15)">
              <i data-lucide="rotate-ccw" class="w-5 h-5 text-cyan-400"></i>
            </div>
            <div>
              <h2 class="text-lg font-medium">Modul Asesmen — Reflection Loop</h2>
              <p class="text-xs text-slate-500 font-light">Spot check tanpa AI: jelaskan ulang pemahamanmu, lalu lihat selisih antara keyakinan dan kemampuan aktual</p>
            </div>
          </div>
        </div>

        <!-- Pre-quiz: Confidence -->
        <div id="reflectionPre" class="fiu" style="animation-delay:.1s">
          <div class="glass rounded-xl p-5 mb-5">
            <p class="text-xs font-medium text-slate-400 mb-1 flex items-center gap-2">
              <i data-lucide="brain" class="w-3.5 h-3.5 text-amber-400"></i> Langkah 1: Skor Keyakinan Diri
            </p>
            <p class="text-[11px] text-slate-500 font-light mb-4">Sebelum menjawab pertanyaan, seberapa yakin kamu dengan pemahamanmu tentang topik yang telah kamu kerjakan dengan bantuan AI?</p>
            <div class="flex items-center gap-4">
              <input type="range" id="confidenceSlider" min="0" max="100" value="50" class="flex-1 accent-cyan-400" oninput="updateConfidenceLabel()">
              <span id="confidenceLabel" class="text-lg font-medium text-white w-14 text-right">50%</span>
            </div>
            <div class="flex justify-between mt-1">
              <span class="text-[10px] text-slate-600">Sama sekali tidak yakin</span>
              <span class="text-[10px] text-slate-600">Sangat yakin</span>
            </div>
            <button onclick="startReflection()" class="btn-p px-5 py-2.5 rounded-lg text-xs font-semibold text-white mt-5 flex items-center gap-2">
              <i data-lucide="play" class="w-3.5 h-3.5"></i> Mulai Spot Check
            </button>
          </div>
        </div>

        <!-- Quiz -->
        <div id="reflectionQuiz" class="hidden">
          <div class="glass rounded-xl p-5 mb-5 fiu">
            <div class="flex items-center justify-between mb-4">
              <p class="text-xs font-medium text-slate-400 flex items-center gap-2">
                <i data-lucide="alert-triangle" class="w-3.5 h-3.5 text-red-400"></i> AI Dinonaktifkan untuk Sesi Ini
              </p>
              <span class="text-[10px] text-slate-600" id="quizProgress">Pertanyaan 1/3</span>
            </div>
            <div class="p-3 rounded-lg mb-4" style="background:rgba(239,68,68,0.05);border:1px solid rgba(239,68,68,0.1)">
              <p class="text-[10px] text-red-400/70 font-light">⚠️ Kamu tidak bisa menggunakan AI untuk menjawab. Ini adalah spot check — tujuannya mengukur pemahaman aslimu.</p>
            </div>
            <p class="text-sm font-medium text-white mb-4" id="quizQuestion"></p>
            <div class="space-y-2" id="quizOptions"></div>
          </div>
        </div>

        <!-- Results -->
        <div id="reflectionResult" class="hidden">
          <div class="glass rounded-xl p-6 fiu">
            <p class="text-xs font-medium text-slate-400 mb-6 flex items-center gap-2">
              <i data-lucide="bar-chart-3" class="w-3.5 h-3.5 text-cyan-400"></i> Hasil Indeks Kalibrasi Metakognitif
            </p>

            <!-- Gauge -->
            <div class="flex justify-center mb-6">
              <div class="relative w-48 h-48">
                <svg class="w-full h-full -rotate-90" viewBox="0 0 120 120">
                  <circle cx="60" cy="60" r="52" fill="none" stroke="rgba(255,255,255,0.04)" stroke-width="8"/>
                  <circle id="gaugeCircle" cx="60" cy="60" r="52" fill="none" stroke="#22d3ee" stroke-width="8" stroke-linecap="round" stroke-dasharray="326.73" stroke-dashoffset="326.73" class="gauge-ring"/>
                </svg>
                <div class="absolute inset-0 flex flex-col items-center justify-center">
                  <span id="gaugeValue" class="text-3xl font-medium text-white">—</span>
                  <span class="text-[10px] text-slate-500">Kalibrasi</span>
                </div>
              </div>
            </div>

            <!-- Breakdown -->
            <div class="grid grid-cols-1 md:grid-cols-3 gap-4 mb-6">
              <div class="p-4 rounded-lg text-center" style="background:rgba(255,255,255,0.02);border:1px solid rgba(255,255,255,0.06)">
                <p class="text-[10px] text-slate-500 mb-1">Keyakinan Diri</p>
                <p id="resConfidence" class="text-xl font-medium text-white">—</p>
              </div>
              <div class="p-4 rounded-lg text-center" style="background:rgba(255,255,255,0.02);border:1px solid rgba(255,255,255,0.06)">
                <p class="text-[10px] text-slate-500 mb-1">Kemampuan Aktual</p>
                <p id="resActual" class="text-xl font-medium text-white">—</p>
              </div>
              <div class="p-4 rounded-lg text-center" style="background:rgba(255,255,255,0.02);border:1px solid rgba(255,255,255,0.06)">
                <p class="text-[10px] text-slate-500 mb-1">Selisih (Gap)</p>
                <p id="resGap" class="text-xl font-medium">—</p>
              </div>
            </div>

            <!-- Interpretation -->
            <div id="resInterpretation" class="p-4 rounded-lg mb-5" style="border:1px solid rgba(255,255,255,0.06)">
            </div>

            <div class="flex gap-3">
              <button onclick="resetReflection()" class="btn-s px-5 py-2.5 rounded-lg text-xs font-medium text-slate-400 flex items-center gap-2">
                <i data-lucide="rotate-ccw" class="w-3.5 h-3.5"></i> Ulangi Spot Check
              </button>
            </div>
          </div>
        </div>
      </div>

    </div>
  </div>
</main>

<!-- Footer -->
<footer class="relative z-10 py-5 px-5" style="border-top:1px solid rgba(255,255,255,0.04)">
  <div class="max-w-7xl mx-auto flex flex-col sm:flex-row items-center justify-between gap-2">
    <p class="text-[10px] text-slate-600 font-light">JEDA — Prototipe Demonstrasi | Lomba Esai Nasional 2026</p>
    <p class="text-[10px] text-slate-600 font-light">Mgs. M. Fatih Abdurrahman — Fakultas Psikologi, UIN Raden Fatah Palembang</p>
  </div>
</footer>

<script>
// ============================================================
// DATA SKENARIO
// ============================================================
const SCENARIOS = {
  academic: {
    name: 'Raka',
    J: {
      context: 'Konteks: Skripsi — Analisis Data',
      title: 'Tugas: Pilih dan jelaskan uji statistik untuk penelitianmu',
      desc: 'Sebelum bertanya kepada AI, tuliskan dulu pemahamanmu sendiri — selemah apa pun itu. Ini bukan dinilai benar atau salah, tapi untuk mengaktifkan proses berpikirmu sebelum menerima bantuan.',
      aiResponse: 'Berdasarkan desain penelitianmu (perbandingan dua kelompok independen dengan skala pengukuran interval), uji yang tepat adalah **Independent Samples t-test**. Alasannya:\n\n1. Data berskala interval/rasio → memenuhi asumsi parametrik\n2. Dua kelompok independen (eksperimen vs kontrol) → bukan berpasangan\n3. Perlu cek asumsi normalitas (Shapiro-Wilk) dan homogenitas varian (Levene\'s test) sebelumnya\n\nAlternatif jika asumsi tidak terpenuhi: Mann-Whitney U Test (non-parametrik).\n\nPertimbangan: Jika sampel >30 per kelompok, t-test cukup robust terhadap pelanggaran normalitas (Berdasarkan Central Limit Theorem).'
    },
    E: [
      { id:1, title:'Penilaian Tugas Akhir Mahasiswa', impact:'Tinggi — menentukan kelulusan', source:'AI merekomendasikan skor berdasarkan rubrik otomatis', status:'pending', verifyType:'Wawancara singkat dengan dosen pembimbing' },
      { id:2, title:'Pembagian Kelompok Praktikum', impact:'Sedang — mempengaruhi pengalaman belajar', source:'Algoritma pembagian acak berdasarkan IPK', status:'verified', verifyType:'Sudah dikonfirmasi koordinator' },
      { id:3, title:'Rekomendasi Topik Penelitian', impact:'Tinggi — menentukan arah 6 bulan kerja', source:'AI menganalisis tren dan menyarankan 3 topik', status:'pending', verifyType:'Diskusi tatap muka dengan dosen pembimbing' },
      { id:4, title:'Evaluasi Kinerja Asisten Laboratorium', impact:'Tinggi — menentukan kontrak lanjutan', source:'AI meringkas feedback mahasiswa + log aktivitas', status:'pending', verifyType:'Wawancara langsung dengan kepala lab' },
      { id:5, title:'Jadwal Ujian Semua Mata Kuliah', impact:'Rendah — logistik administratif', source:'AI mengoptimalkan jadwal berdasarkan ketersediaan ruang', status:'verified', verifyType:'Tidak perlu verifikasi manual (risiko rendah)' },
    ],
    D: [
      { time:'09:14', type:'thought', text:'Saya perlu memutuskan uji statistik apa yang cocok untuk data skripsi saya. Dua kelompok, independen, data kuantitatif.' },
      { time:'09:16', type:'doubt', text:'Hmm, tapi apakah datanya benar-benar normal? Saya belum cek. Kalau tidak normal, apa yang terjadi?' },
      { time:'09:20', type:'thought', text:'Kalau dua kelompok independen, sepertinya t-test independen. Tapi saya lupa bedanya dengan paired t-test...' },
      { time:'09:23', type:'decision', text:'Saya akan tulis dulu pemahaman saya sebelum bertanya ke AI. Biar tahu seberapa jauh saya paham.' },
      { time:'09:28', type:'revision', text:'Setelah menulis, saya sadar saya tidak yakin soal asumsi normalitas. Ini penting tapi saya belum tahu cara ceknya.' },
      { time:'09:35', type:'thought', text:'AI menjelaskan perlu cek Shapiro-Wilk dan Levene\'s test. Saya baru paham sekarang bahwa t-test punya prasyarat yang harus dipenuhi dulu.' },
      { time:'09:40', type:'doubt', text:'Tapi kalau datanya tidak normal, apakah saya harus ganti uji sepenuhnya? Atau ada toleransi?' },
      { time:'09:45', type:'decision', text:'AI menyebutkan CLT untuk sampel >30. Sampel saya 45 per kelompok, jadi mungkin masih aman. Tapi tetap akan cek normalitas sebagai bukti.' },
    ],
    quiz: [
      { q: 'Apa perbedaan utama antara Independent Samples t-test dan Paired t-test?', opts: ['Independent untuk kelompok yang sama diukur dua kali, Paired untuk kelompok berbeda','Independent untuk dua kelompok berbeda yang tidak berpasangan, Paired untuk pengukuran berpasangan pada subjek yang sama','Tidak ada perbedaan, keduanya sama saja','Independent untuk data kualitatif, Paired untuk data kuantitatif'], correct:1 },
      { q: 'Mengapa asumsi normalitas penting sebelum melakukan t-test?', opts: ['Agar hasilnya bisa dipublikasikan di jurnal','Karena t-test adalah uji parametrik yang mengasumsikan data berdistribusi normal — jika dilanggar, hasil bisa tidak akurat','Karena normalitas menunjukkan data sudah bersih dari outlier','Hanya formalitas administratif di penelitian'], correct:1 },
      { q: 'Apa yang kamu lakukan jika asumsi homogenitas varian tidak terpenuhi?', opts: ['Langsung ganti ke uji kualitatif','Tetap pakai t-test karena tidak terlalu penting','Gunakan alternatif non-parametrik seperti Mann-Whitney U Test','Hapus data outlier sampai varians menjadi homogen'], correct:2 },
    ]
  },
  org: {
    name: 'Anda (Staf HR)',
    J: {
      context: 'Konteks: Rekrutmen — Evaluasi Kandidat',
      title: 'Tugas: Tentukan kandidat terbaik untuk posisi Team Lead berdasarkan data assessment',
      desc: 'AI telah menganalisis seluruh data kandidat. Tapi sebelum melihat rekomendasinya, tuliskan dulu penilaianmu sendiri berdasarkan apa yang kamu ingat dari proses wawancara.',
      aiResponse: 'Berdasarkan analisis multi-dimensi:\n\n**Rekomendasi: Kandidat B (Rina)**\n\n• Skor kompetensi teknis: 87/100 (tertinggi)\n• Skor kepemimpinan: 82/100\n• Feedback panel wawancara: 4.2/5.0\n• Cultural fit score: 78/100\n\n**Catatan penting:**\n- Kandidat A memiliki skor teknis lebih rendah (74) tapi cultural fit tertinggi (91) — pertimbangkan untuk peran berbeda\n- Kandidat C sangat kuat di technical (85) tapi feedback panel menunjukkan kekhawatiran soal komunikasi lintas-divisi\n\n**Peringatan:** Keputusan final TIDAK boleh sepenuhnya mengikuti rekomendasi ini. Gunakan sebagai salah satu input, bukan satu-satunya dasar.'
    },
    E: [
      { id:1, title:'Penilaian Kinerja Kuartalan', impact:'Tinggi — menentukan bonus dan promosi', source:'AI meringkas KPI + feedback atasan + feedback peer', status:'pending', verifyType:'Wawancara tatap muka dengan atasan langsung' },
      { id:2, title:'Rekomendasi Promosi Karyawan', impact:'Tinggi — mengubah jalur karier seseorang', source:'AI menganalisis track record + potensi pengembangan', status:'pending', verifyType:'Diskusi komite promosi + wawancara kandidat' },
      { id:3, title:'Penjadwalan Shift Mingguan', impact:'Rendah — logistik operasional', source:'AI mengoptimalkan berdasarkan ketersediaan & beban kerja', status:'verified', verifyType:'Tidak perlu verifikasi (risiko rendah)' },
      { id:4, title:'Keputusan PHK Karena Efisiensi', impact:'Sangat Tinggi — mata pencaharian seseorang', source:'AI menganalisis produktivitas + penggantian otomasi', status:'pending', verifyType:'Wajib: Panel review manusia multi-level + due process' },
      { id:5, title:'Pemetaan Training Needs', impact:'Sedang — alokasi anggaran pengembangan', source:'AI menganalisis gap kompetensi dari data kinerja', status:'verified', verifyType:'Sudah divalidasi HR Business Partner' },
    ],
    D: [
      { time:'10:00', type:'thought', text:'Saya sudah melakukan 3 sesi wawancara hari ini. Kandidat B menonjol di jawaban teknis, tapi Kandidat A lebih natural memimpin diskusi.' },
      { time:'10:05', type:'doubt', text:'Apakah saya terlalu terpengaruh first impression? Kandidat A memang karismatik, tapi apakah itu cukup untuk posisi Team Lead?' },
      { time:'10:10', type:'thought', text:'Saya perlu menuliskan penilaian saya sendiri dulu sebelum melihat data AI. Kalau saya lihat AI dulu, penilaian saya bisa bias.' },
      { time:'10:15', type:'decision', text:'Saya menilai Kandidat A lebih baik untuk kepemimpinan, Kandidat B lebih kuat di teknis. Tapi saya belum yakin mana yang lebih penting untuk role ini.' },
      { time:'10:22', type:'revision', text:'Setelah menulis, saya sadar saya tidak mempertimbangkan aspek cultural fit secara sistematis. Saya hanya mengandalkan "gut feeling".' },
      { time:'10:30', type:'thought', text:'AI menunjukkan data kuantitatif yang mendukung Kandidat B. Tapi saya khawatir soal komunikasi lintas-divisi yang terlewat AI.' },
      { time:'10:35', type:'doubt', text:'Ini persis masalahnya — AI bisa salah menilai aspek yang tidak terukur. Keputusan promosi tidak boleh hanya berdasarkan angka.' },
      { time:'10:40', type:'decision', text:'Saya akan merekomendasikan diskusi panel tambahan untuk Kandidat B, fokus pada kemampuan komunikasi. Data AI berguna, tapi bukan final.' },
    ],
    quiz: [
      { q: 'Mengapa penting menulis penilaian sendiri SEBELUM melihat rekomendasi AI?', opts: ['Agar terlihat profesional di depan atasan','Karena atasan meminta format tertentu','Untuk mencegah automation bias — agar penilaian manusia tidak terdistorsi oleh output AI','Karena AI selalu salah dan harus dibandingkan'], correct:2 },
      { q: 'Apa yang dimaksud dengan "automation bias" dalam konteks keputusan HR?', opts: ['Kecenderungan mengotomatisasi semua proses tanpa pikir panjang','Kecenderungan mempercayai rekomendasi otomatis tanpa scrutiny yang cukup','Kecenderungan AI membuat kesalahan sistematis','Kecenderungan karyawan menolak penggunaan AI'], correct:1 },
      { q: 'Dalam konteks JEDA, kapan seharusnya AI diizinkan membantu pengambilan keputusan?', opts: ['Selalu, karena AI lebih akurat','Tidak pernah, karena keputusan manusia selalu lebih baik','Setelah checkpoint manusia dilalui — sebagai input tambahan, bukan pengganti keputusan','Hanya untuk keputusan yang tidak penting'], correct:2 },
    ]
  }
};

let currentScenario = 'academic';
let currentTab = 'J';
let jLocked = true;
let trailMode = 'auto';
let trailType = 'thought';
let quizIndex = 0;
let quizScore = 0;
let confidenceValue = 50;
let trailEntries = [];

// ============================================================
// INIT
// ============================================================
document.addEventListener('DOMContentLoaded', () => {
  lucide.createIcons();
  loadScenario(currentScenario);
});

// ============================================================
// SCENARIO
// ============================================================
function switchScenario(key, btn) {
  currentScenario = key;
  document.querySelectorAll('.sc-btn').forEach(b => {
    if (b.parentElement === btn.parentElement) b.classList.remove('active');
  });
  btn.classList.add('active');
  loadScenario(key);
  showToast('Skenario diubah ke ' + (key === 'academic' ? 'Akademik' : 'Organisasi'), 'info');
}

function loadScenario(key) {
  const s = SCENARIOS[key];
  document.getElementById('userName').textContent = s.name;

  // Reset J
  jLocked = true;
  document.getElementById('taskContext').textContent = s.J.context;
  document.getElementById('taskTitle').textContent = s.J.title;
  document.getElementById('taskDesc').textContent = s.J.desc;
  document.getElementById('manualInput').value = '';
  document.getElementById('charCount').textContent = '0 karakter';
  document.getElementById('effortFill').style.width = '0%';
  document.getElementById('effortLabel').textContent = 'Usaha kognitif: Belum terdeteksi';
  document.getElementById('submitManualBtn').disabled = true;
  document.getElementById('aiResponse').innerHTML = '<p class="text-slate-600 italic text-xs">Respons AI akan muncul di sini setelah kamu menyelesaikan checkpoint...</p>';
  document.getElementById('lockBadge').className = 'cp-badge blocked';
  document.getElementById('lockBadge').innerHTML = '<i data-lucide="lock" class="w-3 h-3"></i> Terkunci';
  document.getElementById('lockOverlay').classList.remove('unlocked');
  document.getElementById('lockOverlay').querySelector('.lock-icon').innerHTML = '<i data-lucide="lock" class="w-5 h-5 text-red-400"></i>';
  document.getElementById('lockOverlay').querySelector('.lock-icon').className = 'lock-icon';
  document.getElementById('lockOverlay').querySelector('p:first-of-type').textContent = 'AI Terkunci';
  document.getElementById('lockOverlay').querySelector('p:first-of-type').className = 'text-xs font-medium text-red-400';
  document.getElementById('lockOverlay').querySelector('p:last-of-type').textContent = 'Tulis jawaban manual dulu untuk membuka kunci akses AI';
  document.getElementById('comparisonSection').classList.add('hidden');

  // Reset E
  renderDecisions(s.E);

  // Reset D
  trailEntries = [];
  if (trailMode === 'auto') {
    s.D.forEach((e,i) => setTimeout(() => addTrailEntryAuto(e), i * 400));
  } else {
    renderTrailTimeline();
  }

  // Reset A
  resetReflection();

  lucide.createIcons();
}

// ============================================================
// TABS
// ============================================================
function switchTab(tab, btn) {
  currentTab = tab;
  document.querySelectorAll('.tab-content').forEach(el => el.classList.add('hidden'));
  document.getElementById('tab-' + tab).classList.remove('hidden');
  document.querySelectorAll('.tab-btn').forEach(b => b.classList.remove('active'));
  btn.classList.add('active');
}

// ============================================================
// MODUL J — DELEGATION LOCK
// ============================================================
function updateEffort() {
  const val = document.getElementById('manualInput').value;
  const len = val.trim().length;
  document.getElementById('charCount').textContent = len + ' karakter';

  let pct = 0;
  let label = 'Usaha kognitif: Belum terdeteksi';
  if (len > 0 && len < 30) { pct = 15; label = 'Usaha kognitif: Sangat rendah — tulis lebih detail'; }
  else if (len >= 30 && len < 80) { pct = 40; label = 'Usaha kognitif: Rendah — mulai ada kerangka berpikir'; }
  else if (len >= 80 && len < 150) { pct = 70; label = 'Usaha kognitif: Sedang — cukup untuk membuka kunci'; }
  else if (len >= 150) { pct = 100; label = 'Usaha kognitif: Tinggi — proses berpikir teraktivasi'; }

  document.getElementById('effortFill').style.width = pct + '%';
  document.getElementById('effortLabel').textContent = label;
  document.getElementById('submitManualBtn').disabled = len < 30;
}

function submitManual() {
  if (jLocked) {
    const val = document.getElementById('manualInput').value.trim();
    if (val.length < 30) return;

    // Unlock animation
    jLocked = false;
    const overlay = document.getElementById('lockOverlay');
    overlay.querySelector('.lock-icon').className = 'unlock-icon';
    overlay.querySelector('.unlock-icon').innerHTML = '<i data-lucide="unlock" class="w-5 h-5 text-cyan-400"></i>';
    overlay.querySelector('p:first-of-type').textContent = 'AI Terbuka';
    overlay.querySelector('p:first-of-type').className = 'text-xs font-medium text-cyan-400';
    overlay.querySelector('p:last-of-type').textContent = 'Checkpoint berhasil dilalui';
    lucide.createIcons();

    setTimeout(() => {
      overlay.classList.add('unlocked');
      document.getElementById('lockBadge').className = 'cp-badge verified';
      document.getElementById('lockBadge').innerHTML = '<i data-lucide="unlock" class="w-3 h-3"></i> Terbuka';
      lucide.createIcons();

      // Show AI response
      const s = SCENARIOS[currentScenario];
      document.getElementById('aiResponse').innerHTML = formatText(s.J.aiResponse);

      // Show comparison
      document.getElementById('compManual').textContent = val;
      document.getElementById('compAI').innerHTML = formatText(s.J.aiResponse);
      document.getElementById('comparisonSection').classList.remove('hidden');

      showToast('Checkpoint berhasil! AI telah dibuka.', 'success');
    }, 800);

  }
}

function clearManual() {
  document.getElementById('manualInput').value = '';
  updateEffort();
}

// ============================================================
// MODUL E — HUMAN CHECKPOINT
// ============================================================
function renderDecisions(decisions) {
  const list = document.getElementById('decisionList');
  list.innerHTML = '';
  let pending = 0, verified = 0;

  decisions.forEach((d, i) => {
    if (d.status === 'pending') pending++;
    else verified++;
    const div = document.createElement('div');
    div.className = 'glass rounded-xl p-5 fiu';
    div.style.animationDelay = (i * 0.08) + 's';

    const isPending = d.status === 'pending';

    div.innerHTML = `
      <div class="flex items-start justify-between gap-4 mb-3">
        <div class="flex-1">
          <div class="flex items-center gap-2 mb-1.5 flex-wrap">
            <span class="cp-badge ${isPending ? 'pending' : 'verified'}">${isPending ? '⏳ Menunggu Verifikasi' : '✓ Terverifikasi'}</span>
            <span class="text-[10px] text-slate-600">IDR-${String(d.id).padStart(3,'0')}</span>
          </div>
          <p class="text-sm font-medium text-white">${d.title}</p>
        </div>
      </div>
      <div class="grid grid-cols-1 md:grid-cols-3 gap-3 mb-4">
        <div class="p-3 rounded-lg" style="background:rgba(255,255,255,0.02);border:1px solid rgba(255,255,255,0.05)">
          <p class="text-[10px] text-slate-600 mb-0.5">Tingkat Dampak</p>
          <p class="text-xs text-slate-300 font-light">${d.impact}</p>
        </div>
        <div class="p-3 rounded-lg" style="background:rgba(255,255,255,0.02);border:1px solid rgba(255,255,255,0.05)">
          <p class="text-[10px] text-slate-600 mb-0.5">Sumber Keputusan</p>
          <p class="text-xs text-slate-300 font-light">${d.source}</p>
        </div>
        <div class="p-3 rounded-lg" style="background:rgba(255,255,255,0.02);border:1px solid rgba(255,255,255,0.05)">
          <p class="text-[10px] text-slate-600 mb-0.5">Jenis Verifikasi</p>
          <p class="text-xs text-slate-300 font-light">${d.verifyType}</p>
        </div>
      </div>
      ${isPending ? `
        <div class="flex items-center gap-2">
          <button onclick="verifyDecision(this, ${d.id})" class="btn-p px-4 py-2 rounded-lg text-[11px] font-semibold text-white flex items-center gap-1.5">
            <i data-lucide="check" class="w-3 h-3"></i> Verifikasi Sekarang
          </button>
          <button onclick="blockDecision(this, ${d.id})" class="btn-d px-4 py-2 rounded-lg text-[11px] font-medium flex items-center gap-1.5">
            <i data-lucide="x" class="w-3 h-3"></i> Tolak & Eskalasi
          </button>
        </div>
      ` : `
        <div class="flex items-center gap-2 text-[11px] text-emerald-400/70 font-light">
          <i data-lucide="shield-check" class="w-3.5 h-3.5"></i> Keputusan telah disahkan melalui verifikasi manusia
        </div>
      `}
    `;
    list.appendChild(div);
  });

  document.getElementById('hcPending').textContent = pending;
  document.getElementById('hcVerified').textContent = verified;
  document.getElementById('hcTotal').textContent = decisions.length;
  lucide.createIcons();
}

function verifyDecision(btn, id) {
  const card = btn.closest('.glass');
  card.querySelector('.cp-badge').className = 'cp-badge verified';
  card.querySelector('.cp-badge').innerHTML = '✓ Terverifikasi';
  const actionsDiv = btn.parentElement;
  actionsDiv.innerHTML = '<div class="flex items-center gap-2 text-[11px] text-emerald-400/70 font-light"><i data-lucide="shield-check" class="w-3.5 h-3.5"></i> Keputusan telah disahkan melalui verifikasi manusia</div>';
  lucide.createIcons();

  let p = parseInt(document.getElementById('hcPending').textContent) - 1;
  let v = parseInt(document.getElementById('hcVerified').textContent) + 1;
  document.getElementById('hcPending').textContent = p;
  document.getElementById('hcVerified').textContent = v;
  showToast('Keputusan IDR-' + String(id).padStart(3,'0') + ' telah diverifikasi oleh manusia.', 'success');
}

function blockDecision(btn, id) {
  const card = btn.closest('.glass');
  card.querySelector('.cp-badge').className = 'cp-badge blocked';
  card.querySelector('.cp-badge').innerHTML = '✕ Ditolak — Dieskalasi';
  const actionsDiv = btn.parentElement;
  actionsDiv.innerHTML = '<div class="flex items-center gap-2 text-[11px] text-red-400/70 font-light"><i data-lucide="alert-triangle" class="w-3.5 h-3.5"></i> Keputusan ditolak dan dieskalasi ke level atas untuk review lebih lanjut</div>';
  lucide.createIcons();

  let p = parseInt(document.getElementById('hcPending').textContent) - 1;
  document.getElementById('hcPending').textContent = p;
  showToast('Keputusan ditolak. Rekomendasi AI tidak memenuhi standar — dieskalasi.', 'warning');
}

// ============================================================
// MODUL D — PROCESS TRAIL
// ============================================================
function setTrailMode(mode, btn) {
  trailMode = mode;
  document.getElementById('trailAutoBtn').classList.toggle('active', mode === 'auto');
  document.getElementById('trailManualBtn').classList.toggle('active', mode === 'manual');
  document.getElementById('trailManualInput').classList.toggle('hidden', mode === 'auto');

  if (mode === 'auto') {
    trailEntries = [];
    SCENARIOS[currentScenario].D.forEach((e,i) => setTimeout(() => addTrailEntryAuto(e), i * 400));
  } else {
    trailEntries = [];
    renderTrailTimeline();
  }
}

function setTrailType(type, btn) {
  trailType = type;
  document.querySelectorAll('.trail-type-btn').forEach(b => b.classList.remove('active'));
  btn.classList.add('active');
}

function addTrailEntry() {
  const text = document.getElementById('trailInput').value.trim();
  if (!text) return;
  const now = new Date();
  const time = String(now.getHours()).padStart(2,'0') + ':' + String(now.getMinutes()).padStart(2,'0');
  trailEntries.push({ time, type: trailType, text });
  document.getElementById('trailInput').value = '';
  renderTrailTimeline();
  showToast('Jejak berpikir ditambahkan.', 'info');
}

function addTrailEntryAuto(entry) {
  trailEntries.push(entry);
  renderTrailTimeline();
}

function renderTrailTimeline() {
  const container = document.getElementById('trailTimeline');
  document.getElementById('trailCount').textContent = trailEntries.length + ' entri';

  if (trailEntries.length === 0) {
    container.innerHTML = '<p class="text-xs text-slate-600 italic" id="trailEmpty">Belum ada jejak berpikir. Mulai berpikir, dan jejaknya akan terekam di sini...</p>';
    return;
  }

  const typeConfig = {
    thought: { dot: 'cyan', icon: 'lightbulb', color: 'text-cyan-400', label: 'Pemikiran', bg: 'rgba(34,211,238,0.04)', border: 'rgba(34,211,238,0.1)' },
    doubt: { dot: 'amber', icon: 'help-circle', color: 'text-amber-400', label: 'Keraguan', bg: 'rgba(245,158,11,0.04)', border: 'rgba(245,158,11,0.1)' },
    revision: { dot: 'cyan', icon: 'refresh-cw', color: 'text-cyan-400', label: 'Revisi', bg: 'rgba(34,211,238,0.04)', border: 'rgba(34,211,238,0.1)' },
    decision: { dot: 'slate', icon: 'check-circle', color: 'text-slate-300', label: 'Keputusan', bg: 'rgba(255,255,255,0.02)', border: 'rgba(255,255,255,0.08)' },
  };

  let html = '<div class="tl-line"></div>';
  trailEntries.forEach((e, i) => {
    const c = typeConfig[e.type] || typeConfig.thought;
    html += `
      <div class="flex gap-3 items-start fiu" style="animation-delay:${i*0.05}s">
        <div class="tl-dot ${c.dot} mt-1"></div>
        <div class="flex-1 pb-1">
          <div class="flex items-center gap-2 mb-1">
            <span class="text-[10px] font-medium ${c.color} flex items-center gap-1"><i data-lucide="${c.icon}" class="w-3 h-3"></i> ${c.label}</span>
            <span class="text-[10px] text-slate-600">${e.time}</span>
          </div>
          <div class="p-3 rounded-lg" style="background:${c.bg};border:1px solid ${c.border}">
            <p class="text-xs text-slate-300 font-light leading-relaxed">${e.text}</p>
          </div>
        </div>
      </div>
    `;
  });
  container.innerHTML = html;
  lucide.createIcons();
}

// ============================================================
// MODUL A — REFLECTION LOOP
// ============================================================
function updateConfidenceLabel() {
  confidenceValue = parseInt(document.getElementById('confidenceSlider').value);
  document.getElementById('confidenceLabel').textContent = confidenceValue + '%';
}

function startReflection() {
  document.getElementById('reflectionPre').classList.add('hidden');
  document.getElementById('reflectionQuiz').classList.remove('hidden');
  quizIndex = 0;
  quizScore = 0;
  renderQuizQuestion();
}

function renderQuizQuestion() {
  const s = SCENARIOS[currentScenario];
  const q = s.quiz[quizIndex];
  document.getElementById('quizProgress').textContent = `Pertanyaan ${quizIndex+1}/${s.quiz.length}`;
  document.getElementById('quizQuestion').textContent = q.q;

  const optsDiv = document.getElementById('quizOptions');
  optsDiv.innerHTML = '';
  q.opts.forEach((opt, i) => {
    const btn = document.createElement('button');
    btn.className = 'ref-opt w-full text-left';
    btn.textContent = opt;
    btn.onclick = () => selectAnswer(i, btn);
    optsDiv.appendChild(btn);
  });
}

function selectAnswer(idx, btn) {
  const s = SCENARIOS[currentScenario];
  const q = s.quiz[quizIndex];
  const opts = document.querySelectorAll('#quizOptions .ref-opt');

  opts.forEach((o, i) => {
    o.onclick = null;
    o.style.cursor = 'default';
    if (i === q.correct) o.classList.add('correct');
    if (i === idx && idx !== q.correct) o.classList.add('wrong');
  });

  if (idx === q.correct) quizScore++;

  setTimeout(() => {
    quizIndex++;
    if (quizIndex < s.quiz.length) {
      renderQuizQuestion();
    } else {
      showReflectionResult();
    }
  }, 1000);
}

function showReflectionResult() {
  document.getElementById('reflectionQuiz').classList.add('hidden');
  document.getElementById('reflectionResult').classList.remove('hidden');

  const s = SCENARIOS[currentScenario];
  const actual = Math.round((quizScore / s.quiz.length) * 100);
  const gap = confidenceValue - actual;
  const calibration = Math.max(0, 100 - Math.abs(gap));

  document.getElementById('resConfidence').textContent = confidenceValue + '%';
  document.getElementById('resActual').textContent = actual + '%';

  const gapEl = document.getElementById('resGap');
  gapEl.textContent = (gap > 0 ? '+' : '') + gap + '%';
  if (Math.abs(gap) <= 15) { gapEl.className = 'text-xl font-medium text-emerald-400'; }
  else if (gap > 15) { gapEl.className = 'text-xl font-medium text-red-400'; }
  else { gapEl.className = 'text-xl font-medium text-amber-400'; }

  // Gauge
  const circumference = 326.73;
  const offset = circumference - (calibration / 100) * circumference;
  const gauge = document.getElementById('gaugeCircle');
  gauge.style.strokeDashoffset = circumference;

  setTimeout(() => {
    gauge.style.strokeDashoffset = offset;
    let gaugeColor = '#10b981';
    if (calibration < 50) gaugeColor = '#ef4444';
    else if (calibration < 75) gaugeColor = '#f59e0b';
    gauge.style.stroke = gaugeColor;
  }, 100);

  document.getElementById('gaugeValue').textContent = calibration;
  document.getElementById('gaugeValue').style.color = calibration >= 75 ? '#10b981' : calibration >= 50 ? '#f59e0b' : '#ef4444';

  // Interpretation
  const interp = document.getElementById('resInterpretation');
  let interpHTML = '';
  if (Math.abs(gap) <= 15) {
    interp.style.background = 'rgba(16,185,129,0.05)';
    interp.style.borderColor = 'rgba(16,185,129,0.15)';
    interpHTML = `
      <p class="text-xs font-medium text-emerald-400 mb-2">✓ Kalibrasi Baik</p>
      <p class="text-[11px] text-slate-400 font-light leading-relaxed">Selisih antara keyakinan dan kemampuan aktualmu kecil (${gap > 0 ? '+' : ''}${gap}%). Artinya kamu cukup akurat menilai batas pemahamanmu sendiri — ini menunjukkan kesadaran metakognitif yang baik. Pemahamanmu kemungkinan bukan sekadar "pinjaman" dari AI.</p>
    `;
  } else if (gap > 15) {
    interp.style.background = 'rgba(239,68,68,0.05)';
    interp.style.borderColor = 'rgba(239,68,68,0.15)';
    interpHTML = `
      <p class="text-xs font-medium text-red-400 mb-2">⚠ Ilusi Kompetensi Terdeteksi</p>
      <p class="text-[11px] text-slate-400 font-light leading-relaxed">Kamu merasa yakin ${confidenceValue}%, tapi kemampuan aktualmu hanya ${actual}%. Selisih +${gap}% mengindikasikan kamu mungkin mengalami <strong class="text-white">processing fluency illusion</strong> — kelancaran membaca output AI terasa seperti pemahaman, padahal tidak. Ini tepatnya masalah yang JEDA cegah.</p>
    `;
  } else {
    interp.style.background = 'rgba(245,158,11,0.05)';
    interp.style.borderColor = 'rgba(245,158,11,0.15)';
    interpHTML = `
      <p class="text-xs font-medium text-amber-400 mb-2">↓ Underconfidence</p>
      <p class="text-[11px] text-slate-400 font-light leading-relaxed">Kamu merasa yakin ${confidenceValue}%, tapi kemampuan aktualmu ${actual}%. Kamu sebenarnya lebih paham dari yang kamu kira. Mungkin proses berpikir yang kamu lalui di Modul J memberikan fondasi yang lebih kuat dari yang kamu sadari.</p>
    `;
  }
  interp.innerHTML = interpHTML;
}

function resetReflection() {
  document.getElementById('reflectionPre').classList.remove('hidden');
  document.getElementById('reflectionQuiz').classList.add('hidden');
  document.getElementById('reflectionResult').classList.add('hidden');
  document.getElementById('confidenceSlider').value = 50;
  document.getElementById('confidenceLabel').textContent = '50%';
  confidenceValue = 50;
  quizIndex = 0;
  quizScore = 0;
}

// ============================================================
// UTILITIES
// ============================================================
function formatText(text) {
  let out = escapeHTML(text);
  out = out.replace(/\*\*(.*?)\*\*/g, '<strong class="font-medium text-white">$1</strong>');
  out = out.replace(/\n/g, '<br>');
  return out;
}

function escapeHTML(str) {
  const d = document.createElement('div');
  d.textContent = str;
  return d.innerHTML;
}

function showToast(msg, type = 'info') {
  const t = document.getElementById('toast');
  const icons = { success: 'check-circle', warning: 'alert-triangle', error: 'x-circle', info: 'info' };
  t.className = `toast ${type}`;
  t.innerHTML = `<i data-lucide="${icons[type]}" class="w-4 h-4 flex-shrink-0"></i> ${msg}`;
  lucide.createIcons();
  requestAnimationFrame(() => t.classList.add('show'));
  setTimeout(() => t.classList.remove('show'), 3500);
}
</script>
</body>
</html>
