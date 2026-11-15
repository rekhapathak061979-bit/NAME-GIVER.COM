<!DOCTYPE html>
<html lang="en" class="dark">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>NEXUS // Tactical Esports & Gaming Utility HUD</title>
  <!-- Tailwind CSS via CDN -->
  <script src="https://cdn.tailwindcss.com"></script>
  <!-- Google Fonts: Rajdhani, Inter & JetBrains Mono for Cyberpunk Telemetry -->
  <link rel="preconnect" href="https://fonts.googleapis.com" />
  <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin />
  <link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;500;600;700&family=JetBrains+Mono:wght@400;500;700&family=Rajdhani:wght@600;700&display=swap" rel="stylesheet" />

  <script>
    tailwind.config = {
      darkMode: 'class',
      theme: {
        extend: {
          colors: {
            obsidian: '#090a0f',
            panel: '#11141d',
            panelHover: '#161b27',
            neonCyan: '#00f0ff',
            neonCrimson: '#ff2a6d',
            neonEmerald: '#05ffa1',
            neonAmber: '#ffb800',
            borderDim: 'rgba(0, 240, 255, 0.14)',
          },
          fontFamily: {
            sans: ['Inter', 'system-ui', 'sans-serif'],
            display: ['Rajdhani', 'sans-serif'],
            mono: ['JetBrains Mono', 'monospace'],
          },
        },
      },
    };
  </script>

  <style>
    /* Custom Scrollbar & Cyberpunk Micro-Styling */
    :root {
      --bg-obsidian: #090a0f;
      --bg-panel: #11141d;
      --cyan: #00f0ff;
      --crimson: #ff2a6d;
    }

    * {
      box-sizing: border-box;
    }

    body {
      background-color: var(--bg-obsidian);
      color: #e2e8f0;
      font-family: 'Inter', sans-serif;
      overflow-x: hidden;
      background-image:
        radial-gradient(circle at 15% 10%, rgba(0, 240, 255, 0.06) 0%, transparent 38%),
        radial-gradient(circle at 85% 85%, rgba(255, 42, 109, 0.06) 0%, transparent 38%),
        linear-gradient(rgba(255, 255, 255, 0.015) 1px, transparent 1px),
        linear-gradient(90deg, rgba(255, 255, 255, 0.015) 1px, transparent 1px);
      background-size: 100% 100%, 100% 100%, 32px 32px, 32px 32px;
    }

    /* Glassmorphic Tactical Panel */
    .cyber-card {
      background: rgba(17, 20, 29, 0.86);
      backdrop-filter: blur(14px);
      -webkit-backdrop-filter: blur(14px);
      border: 1px solid rgba(0, 240, 255, 0.15);
      box-shadow: 0 10px 30px -10px rgba(0, 0, 0, 0.75), inset 0 1px 0 0 rgba(255, 255, 255, 0.04);
      transition: border-color 0.22s ease, box-shadow 0.22s ease, transform 0.22s ease;
    }

    .cyber-card:hover {
      border-color: rgba(0, 240, 255, 0.35);
      box-shadow: 0 14px 36px -10px rgba(0, 240, 255, 0.1), inset 0 1px 0 0 rgba(0, 240, 255, 0.08);
    }

    /* Custom Range Sliders */
    input[type="range"] {
      -webkit-appearance: none;
      appearance: none;
      background: rgba(255, 255, 255, 0.08);
      height: 6px;
      border-radius: 999px;
      outline: none;
    }

    input[type="range"]::-webkit-slider-thumb {
      -webkit-appearance: none;
      appearance: none;
      width: 16px;
      height: 16px;
      border-radius: 50%;
      background: #00f0ff;
      cursor: pointer;
      border: 2px solid #090a0f;
      box-shadow: 0 0 10px rgba(0, 240, 255, 0.8);
      transition: transform 0.15s ease;
    }

    input[type="range"]::-webkit-slider-thumb:hover {
      transform: scale(1.18);
    }

    /* Custom Scrollbars */
    ::-webkit-scrollbar {
      width: 6px;
      height: 6px;
    }
    ::-webkit-scrollbar-track {
      background: #090a0f;
    }
    ::-webkit-scrollbar-thumb {
      background: #1e2536;
      border-radius: 999px;
    }
    ::-webkit-scrollbar-thumb:hover {
      background: #00f0ff;
    }

    /* Neon Glow Utilities */
    .glow-cyan {
      box-shadow: 0 0 18px rgba(0, 240, 255, 0.25);
    }
    .glow-crimson {
      box-shadow: 0 0 18px rgba(255, 42, 109, 0.28);
    }

    /* Canvas responsive container */
    .canvas-wrap {
      position: relative;
      width: 100%;
      overflow: hidden;
      border-radius: 0.75rem;
    }
    .canvas-wrap canvas {
      display: block;
      width: 100%;
      height: 100%;
    }

    @keyframes pulseBeacon {
      0%, 100% { opacity: 1; transform: scale(1); }
      50% { opacity: 0.45; transform: scale(0.85); }
    }
    .beacon-dot {
      animation: pulseBeacon 1.8s infinite ease-in-out;
    }
  </style>
</head>
<body class="min-h-screen flex flex-col lg:flex-row selection:bg-neonCyan selection:text-obsidian">

  <!-- Toast Notification Container -->
  <div id="toast-container" class="fixed bottom-4 right-4 z-50 flex flex-col gap-2 max-w-sm w-full pointer-events-none px-4 sm:px-0"></div>

  <!-- Mobile Top Header Bar -->
  <header class="lg:hidden sticky top-0 z-40 bg-obsidian/90 backdrop-blur-md border-b border-cyan-500/20 px-4 py-3 flex items-center justify-between">
    <div class="flex items-center gap-2.5">
      <div class="w-8 h-8 rounded-lg bg-cyan-500/15 border border-neonCyan flex items-center justify-center text-neonCyan font-display font-bold text-lg shadow-[0_0_12px_rgba(0,240,255,0.3)]">
        NX
      </div>
      <div>
        <h1 class="font-display font-bold tracking-wider text-white text-lg leading-none">NEXUS<span class="text-neonCyan">//HUD</span></h1>
        <span class="font-mono text-[10px] text-slate-400">ESPORTS TELEMETRY v4.2</span>
      </div>
    </div>
    <button id="mobile-menu-btn" aria-label="Toggle Navigation" class="p-2.5 rounded-lg bg-panel border border-cyan-500/25 text-neonCyan hover:bg-cyan-500/10 active:scale-95 transition">
      <svg class="w-5 h-5" fill="none" stroke="currentColor" stroke-width="2" viewBox="0 0 24 24">
        <path stroke-linecap="round" stroke-linejoin="round" d="M4 6h16M4 12h16M4 18h16" />
      </svg>
    </button>
  </header>

  <!-- Sidebar Navigation -->
  <aside id="sidebar" class="fixed inset-y-0 left-0 z-40 w-64 bg-panel/95 backdrop-blur-xl border-r border-cyan-500/15 transform -translate-x-full lg:translate-x-0 lg:static lg:flex lg:flex-col justify-between transition-transform duration-200 ease-out">
    <div class="p-5">
      <!-- Brand Identity -->
      <div class="hidden lg:flex items-center gap-3 pb-6 border-b border-slate-800/80">
        <div class="w-10 h-10 rounded-xl bg-gradient-to-br from-cyan-500/20 to-rose-500/20 border border-neonCyan/60 flex items-center justify-center text-neonCyan font-display font-bold text-xl glow-cyan">
          NX
        </div>
        <div>
          <h1 class="font-display font-bold tracking-wider text-white text-xl leading-none">NEXUS<span class="text-neonCyan">//HUD</span></h1>
          <p class="font-mono text-[11px] text-slate-400 mt-1">TACTICAL UTILITY SUITE</p>
        </div>
      </div>

      <!-- Navigation Links -->
      <nav class="mt-4 lg:mt-6 flex flex-col gap-1.5" aria-label="Utility Modules">
        <p class="px-3 text-[10px] font-mono uppercase tracking-widest text-slate-500 mb-1">Command Views</p>

        <button data-view="all" class="nav-btn active flex items-center gap-3 px-3.5 py-2.5 rounded-xl text-sm font-medium transition-all bg-cyan-500/15 text-neonCyan border border-cyan-500/30">
          <svg class="w-4 h-4 shrink-0" fill="none" stroke="currentColor" stroke-width="2" viewBox="0 0 24 24"><rect x="3" y="3" width="7" height="7" rx="1"/><rect x="14" y="3" width="7" height="7" rx="1"/><rect x="14" y="14" width="7" height="7" rx="1"/><rect x="3" y="14" width="7" height="7" rx="1"/></svg>
          <span>Command Bento Grid</span>
        </button>

        <button data-view="aim" class="nav-btn flex items-center gap-3 px-3.5 py-2.5 rounded-xl text-sm font-medium text-slate-400 hover:text-white hover:bg-white/5 border border-transparent transition-all">
          <svg class="w-4 h-4 shrink-0 text-neonCrimson" fill="none" stroke="currentColor" stroke-width="2" viewBox="0 0 24 24"><circle cx="12" cy="12" r="9"/><circle cx="12" cy="12" r="3"/><path d="M12 2v3m0 14v3M2 12h3m14 0h3"/></svg>
          <span>Aim &amp; Reflex Trainer</span>
        </button>

        <button data-view="sens" class="nav-btn flex items-center gap-3 px-3.5 py-2.5 rounded-xl text-sm font-medium text-slate-400 hover:text-white hover:bg-white/5 border border-transparent transition-all">
          <svg class="w-4 h-4 shrink-0 text-neonCyan" fill="none" stroke="currentColor" stroke-width="2" viewBox="0 0 24 24"><path d="M12 2v20M2 12h20M4.93 4.93l14.14 14.14M19.07 4.93L4.93 19.07"/></svg>
          <span>eDPI &amp; Sens Converter</span>
        </button>

        <button data-view="perf" class="nav-btn flex items-center gap-3 px-3.5 py-2.5 rounded-xl text-sm font-medium text-slate-400 hover:text-white hover:bg-white/5 border border-transparent transition-all">
          <svg class="w-4 h-4 shrink-0 text-neonEmerald" fill="none" stroke="currentColor" stroke-width="2" viewBox="0 0 24 24"><polyline points="22 12 18 12 15 21 9 3 6 12 2 12"/></svg>
          <span>FPS &amp; Latency Monitor</span>
        </button>

        <button data-view="crosshair" class="nav-btn flex items-center gap-3 px-3.5 py-2.5 rounded-xl text-sm font-medium text-slate-400 hover:text-white hover:bg-white/5 border border-transparent transition-all">
          <svg class="w-4 h-4 shrink-0 text-neonAmber" fill="none" stroke="currentColor" stroke-width="2" viewBox="0 0 24 24"><path d="M12 5v14M5 12h14"/></svg>
          <span>Crosshair Studio</span>
        </button>

        <button data-view="loadouts" class="nav-btn flex items-center gap-3 px-3.5 py-2.5 rounded-xl text-sm font-medium text-slate-400 hover:text-white hover:bg-white/5 border border-transparent transition-all">
          <svg class="w-4 h-4 shrink-0 text-purple-400" fill="none" stroke="currentColor" stroke-width="2" viewBox="0 0 24 24"><path d="M14 2H6a2 2 0 0 0-2 2v16a2 2 0 0 0 2 2h12a2 2 0 0 0 2-2V8z"/><polyline points="14 2 14 8 20 8"/><line x1="16" y1="13" x2="8" y2="13"/><line x1="16" y1="17" x2="8" y2="17"/></svg>
          <span>Tactical Loadouts &amp; Binds</span>
        </button>
      </nav>
    </div>

    <!-- Bottom Hardware Telemetry Card -->
    <div class="p-4 m-4 rounded-xl bg-obsidian/90 border border-slate-800/90">
      <div class="flex items-center justify-between mb-2">
        <span class="font-mono text-[11px] text-slate-400">ENGINE STATUS</span>
        <span class="flex items-center gap-1.5 font-mono text-[11px] text-neonEmerald">
          <span class="w-2 h-2 rounded-full bg-neonEmerald beacon-dot"></span> ONLINE
        </span>
      </div>
      <div class="space-y-1.5 font-mono text-xs">
        <div class="flex justify-between">
          <span class="text-slate-500">CORES / THREADS</span>
          <span id="sys-cores" class="text-slate-200">--</span>
        </div>
        <div class="flex justify-between">
          <span class="text-slate-500">POLL RATE EST.</span>
          <span id="sys-hz" class="text-neonCyan">1000 Hz</span>
        </div>
        <div class="flex justify-between">
          <span class="text-slate-500">STORAGE SYNC</span>
          <span class="text-neonEmerald">LOCAL // OK</span>
        </div>
      </div>
    </div>
  </aside>

  <!-- Sidebar Backdrop Overlay for Mobile -->
  <div id="sidebar-backdrop" class="fixed inset-0 bg-black/70 backdrop-blur-xs z-30 hidden lg:hidden"></div>

  <!-- Main Workspace Container -->
  <main class="flex-1 flex flex-col min-w-0">

    <!-- Live System Telemetry Top Bar -->
    <div class="sticky top-0 z-20 bg-obsidian/85 backdrop-blur-md border-b border-cyan-500/15 px-4 sm:px-6 py-3 flex flex-wrap items-center justify-between gap-3">
      <div class="flex items-center gap-2">
        <span class="px-2 py-0.5 rounded bg-cyan-500/10 border border-cyan-500/30 font-mono text-[11px] text-neonCyan">SYS.HUD</span>
        <h2 id="active-view-title" class="font-display font-bold text-base sm:text-lg tracking-wide text-white uppercase">Command Bento Grid // All Modules</h2>
      </div>

      <!-- Live Telemetry Pills -->
      <div class="flex flex-wrap items-center gap-2 sm:gap-3 font-mono text-xs">
        <div class="px-3 py-1.5 rounded-lg bg-panel border border-slate-800 flex items-center gap-2">
          <span class="text-slate-400">FPS:</span>
          <span id="hud-fps" class="text-neonEmerald font-bold">60</span>
        </div>
        <div class="px-3 py-1.5 rounded-lg bg-panel border border-slate-800 flex items-center gap-2">
          <span class="text-slate-400">PING:</span>
          <span id="hud-ping" class="text-neonCyan font-bold">24 ms</span>
        </div>
        <div class="hidden sm:flex px-3 py-1.5 rounded-lg bg-panel border border-slate-800 items-center gap-2">
          <span class="text-slate-400">LOCAL:</span>
          <span id="hud-clock" class="text-slate-200 font-semibold">00:00:00</span>
        </div>
      </div>
    </div>

    <!-- Bento Grid Dashboard Modules -->
    <div id="dashboard-grid" class="p-4 sm:p-6 grid grid-cols-1 xl:grid-cols-12 gap-5 max-w-[1600px] w-full mx-auto">

      <!-- ===================================================================
           MODULE 1: AIM & REFLEX TRAINER (Canvas Interactive Target Game)
      ==================================================================== -->
      <section data-module="aim" class="module-card xl:col-span-7 cyber-card rounded-2xl p-5 flex flex-col justify-between">
        <div class="flex flex-wrap items-center justify-between gap-3 mb-4">
          <div>
            <div class="flex items-center gap-2">
              <span class="w-2 h-2 rounded-full bg-neonCrimson shadow-[0_0_8px_#ff2a6d]"></span>
              <h3 class="font-display font-bold text-xl tracking-wide text-white uppercase">Aim &amp; Reflex Trainer</h3>
            </div>
            <p class="text-xs text-slate-400 mt-0.5">Precision flick &amp; reaction micro-drill with sub-millisecond tracking</p>
          </div>

          <!-- High Score Badge -->
          <div class="px-3 py-1.5 rounded-xl bg-rose-500/10 border border-rose-500/30 font-mono text-xs flex items-center gap-2">
            <span class="text-rose-300">BEST SCORE:</span>
            <span id="aim-highscore" class="text-neonCrimson font-bold text-sm">0</span>
          </div>
        </div>

        <!-- Drill Configuration Controls -->
        <div class="grid grid-cols-1 sm:grid-cols-3 gap-3 mb-4 bg-obsidian/70 p-3 rounded-xl border border-slate-800/80">
          <div>
            <div class="flex justify-between font-mono text-[11px] text-slate-400 mb-1">
              <span>TARGET SIZE</span>
              <span id="aim-size-val" class="text-neonCyan">28px</span>
            </div>
            <input id="aim-size" type="range" min="14" max="48" value="28" class="w-full" />
          </div>

          <div>
            <div class="flex justify-between font-mono text-[11px] text-slate-400 mb-1">
              <span>SPAWN PACE</span>
              <span id="aim-speed-val" class="text-neonCyan">Normal</span>
            </div>
            <select id="aim-speed" class="w-full bg-panel border border-slate-700 rounded-lg px-2.5 py-1 font-mono text-xs text-white focus:border-neonCyan outline-none">
              <option value="0">Instant (On Hit)</option>
              <option value="1200" selected>Normal (1.2s)</option>
              <option value="850">Fast (0.85s)</option>
              <option value="600">Insane (0.60s)</option>
            </select>
          </div>

          <div>
            <div class="flex justify-between font-mono text-[11px] text-slate-400 mb-1">
              <span>DURATION</span>
              <span class="text-neonCyan">TIMER</span>
            </div>
            <div class="grid grid-cols-3 gap-1 font-mono text-xs" id="aim-duration-group">
              <button type="button" data-dur="15" class="aim-dur-btn py-1 rounded-lg bg-panel border border-slate-700 text-slate-300 hover:border-neonCyan">15s</button>
              <button type="button" data-dur="30" class="aim-dur-btn active py-1 rounded-lg bg-cyan-500/20 border border-neonCyan text-neonCyan font-bold">30s</button>
              <button type="button" data-dur="60" class="aim-dur-btn py-1 rounded-lg bg-panel border border-slate-700 text-slate-300 hover:border-neonCyan">60s</button>
            </div>
          </div>
        </div>

        <!-- Live Telemetry Stats Strip -->
        <div class="grid grid-cols-2 sm:grid-cols-5 gap-2 mb-3 font-mono text-xs">
          <div class="bg-obsidian/80 border border-slate-800 rounded-xl p-2.5 text-center">
            <div class="text-[10px] text-slate-500">TIME LEFT</div>
            <div id="aim-time" class="text-base font-bold text-white mt-0.5">30.0s</div>
          </div>
          <div class="bg-obsidian/80 border border-slate-800 rounded-xl p-2.5 text-center">
            <div class="text-[10px] text-slate-500">HITS</div>
            <div id="aim-hits" class="text-base font-bold text-neonEmerald mt-0.5">0</div>
          </div>
          <div class="bg-obsidian/80 border border-slate-800 rounded-xl p-2.5 text-center">
            <div class="text-[10px] text-slate-500">MISSES</div>
            <div id="aim-misses" class="text-base font-bold text-neonCrimson mt-0.5">0</div>
          </div>
          <div class="bg-obsidian/80 border border-slate-800 rounded-xl p-2.5 text-center">
            <div class="text-[10px] text-slate-500">ACCURACY</div>
            <div id="aim-acc" class="text-base font-bold text-neonCyan mt-0.5">100%</div>
          </div>
          <div class="col-span-2 sm:col-span-1 bg-obsidian/80 border border-slate-800 rounded-xl p-2.5 text-center">
            <div class="text-[10px] text-slate-500">AVG REACT</div>
            <div id="aim-react" class="text-base font-bold text-neonAmber mt-0.5">-- ms</div>
          </div>
        </div>

        <!-- Interactive Aim Arena Canvas -->
        <div class="canvas-wrap h-72 sm:h-80 bg-[#07080c] border border-cyan-500/25 relative select-none">
          <canvas id="aim-canvas" class="cursor-crosshair"></canvas>

          <!-- Overlay Start / Summary Screen -->
          <div id="aim-overlay" class="absolute inset-0 bg-obsidian/80 backdrop-blur-xs flex flex-col items-center justify-center p-4 text-center">
            <div id="aim-summary" class="hidden mb-4 p-3 rounded-xl bg-panel/90 border border-cyan-500/30 max-w-xs w-full">
              <div class="font-mono text-xs text-neonCyan uppercase tracking-wider">DRILL COMPLETE</div>
              <div id="aim-final-score" class="font-display font-bold text-3xl text-white my-1">0 PTS</div>
              <div id="aim-final-meta" class="font-mono text-[11px] text-slate-400">Accuracy: 0% • Avg: 0ms</div>
            </div>
            <button id="aim-start-btn" class="px-6 py-3 rounded-xl bg-gradient-to-r from-cyan-500 to-neonCyan text-obsidian font-display font-bold text-lg tracking-wider uppercase shadow-[0_0_25px_rgba(0,240,255,0.45)] hover:brightness-110 active:scale-95 transition">
              START AIM DRILL
            </button>
            <p class="font-mono text-[11px] text-slate-400 mt-2.5">Click or tap targets immediately as they materialize</p>
          </div>
        </div>
      </section>

      <!-- ===================================================================
           MODULE 2: eDPI & SENSITIVITY CONVERTER
      ==================================================================== -->
      <section data-module="sens" class="module-card xl:col-span-5 cyber-card rounded-2xl p-5 flex flex-col justify-between">
        <div>
          <div class="flex items-center justify-between gap-2 mb-4">
            <div>
              <div class="flex items-center gap-2">
                <span class="w-2 h-2 rounded-full bg-neonCyan shadow-[0_0_8px_#00f0ff]"></span>
                <h3 class="font-display font-bold text-xl tracking-wide text-white uppercase">eDPI &amp; Sens Converter</h3>
              </div>
              <p class="text-xs text-sla
