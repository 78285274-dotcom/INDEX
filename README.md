# INDEX<!DOCTYPE html>
<html lang="es" class="h-full">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Desafío Matemático - Ágora Educación</title>
    
    <!-- Tailwind CSS CDN -->
    <script src="https://cdn.tailwindcss.com"></script>
    
    <!-- Font Awesome Icons -->
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    
    <!-- Google Fonts -->
    <link rel="preconnect" href="https://fonts.googleapis.com">
    <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
    <link href="https://fonts.googleapis.com/css2?family=Fredoka:wght@400;500;600;700&family=Outfit:wght@300;400;600;800&display=swap" rel="stylesheet">

    <!-- Canvas Confetti Library -->
    <script src="https://cdn.jsdelivr.net/npm/canvas-confetti@1.6.0/dist/confetti.browser.min.js"></script>

    <script>
        tailwind.config = {
            theme: {
                extend: {
                    colors: {
                        agora: {
                            navy: '#0F172A',
                            green: '#10B981',
                            darkgreen: '#047857',
                            lightgreen: '#34D399',
                            orange: '#F97316',
                            darkorange: '#C2410C',
                            lightorange: '#FB923C',
                            amber: '#F59E0B'
                        }
                    },
                    fontFamily: {
                        heading: ['Fredoka', 'sans-serif'],
                        body: ['Outfit', 'sans-serif']
                    }
                }
            }
        }
    </script>

    <style>
        body {
            font-family: 'Outfit', sans-serif;
            user-select: none;
        }
        .font-heading {
            font-family: 'Fredoka', sans-serif;
        }
        
        .glass-card {
            background: rgba(255, 255, 255, 0.92);
            backdrop-filter: blur(12px);
            border: 1px solid rgba(255, 255, 255, 0.5);
        }

        .dark-glass {
            background: rgba(15, 23, 42, 0.92);
            backdrop-filter: blur(12px);
            border: 1px solid rgba(255, 255, 255, 0.1);
        }

        @keyframes bounce-subtle {
            0%, 100% { transform: translateY(0); }
            50% { transform: translateY(-6px); }
        }

        @keyframes pulse-glow {
            0%, 100% { box-shadow: 0 0 15px rgba(16, 185, 129, 0.3); }
            50% { box-shadow: 0 0 30px rgba(249, 115, 22, 0.5); }
        }

        .animate-bounce-subtle {
            animation: bounce-subtle 2s infinite ease-in-out;
        }

        .animate-glow {
            animation: pulse-glow 2s infinite;
        }

        .math-input-btn {
            transition: all 0.15s ease;
        }
        .math-input-btn:active {
            transform: scale(0.92);
        }

        /* Custom scrollbars */
        ::-webkit-scrollbar {
            width: 8px;
            height: 8px;
        }
        ::-webkit-scrollbar-track {
            background: #0f172a;
        }
        ::-webkit-scrollbar-thumb {
            background: #10b981;
            border-radius: 4px;
        }
        ::-webkit-scrollbar-thumb:hover {
            background: #f97316;
        }
    </style>
</head>
<body class="h-full bg-slate-900 text-slate-800 flex flex-col min-h-screen selection:bg-emerald-500 selection:text-white">

    <!-- TOP NAVIGATION BAR -->
    <header class="bg-slate-900 border-b border-emerald-900/50 text-white px-4 py-3 sticky top-0 z-40 shadow-lg">
        <div class="max-w-6xl mx-auto flex items-center justify-between">
            <!-- Brand Logo -->
            <div class="flex items-center gap-3 cursor-pointer" onclick="game.goHome()">
                <div class="w-10 h-10 rounded-xl bg-gradient-to-tr from-emerald-600 to-orange-500 flex items-center justify-center text-white font-bold text-xl shadow-md shadow-emerald-500/20">
                    <i class="fa-solid fa-graduation-cap"></i>
                </div>
                <div>
                    <h1 class="font-heading font-bold text-lg md:text-xl tracking-wide leading-none text-white">
                        ÁGORA <span class="text-orange-400 font-normal text-sm md:text-base">.edu.pe</span>
                    </h1>
                    <p class="text-xs text-emerald-400/80">Desafío Matemático</p>
                </div>
            </div>

            <!-- Global Player Status HUD (Visible during gameplay) -->
            <div id="hud-bar" class="hidden flex items-center gap-2 sm:gap-6 bg-slate-800/80 px-3 sm:px-4 py-1.5 rounded-full border border-emerald-500/30">
                <div class="flex items-center gap-1.5 text-amber-400">
                    <i class="fa-solid fa-trophy text-sm"></i>
                    <span id="hud-score" class="font-heading font-bold text-sm sm:text-base">0</span>
                </div>
                <div class="flex items-center gap-1.5 text-orange-400">
                    <i class="fa-solid fa-fire text-sm"></i>
                    <span id="hud-streak" class="font-heading font-bold text-sm sm:text-base">x0</span>
                </div>
                <div class="flex items-center gap-1.5 text-emerald-400">
                    <i class="fa-solid fa-star text-sm"></i>
                    <span id="hud-stars" class="font-heading font-bold text-sm sm:text-base">0</span>
                </div>
            </div>

            <!-- Header Action Controls -->
            <div class="flex items-center gap-2">
                <button onclick="scratchpad.toggle()" id="btn-scratchpad" title="Pizarra de borrador" class="w-9 h-9 rounded-lg bg-slate-800 hover:bg-slate-700 text-emerald-400 hover:text-white flex items-center justify-center transition border border-emerald-500/30">
                    <i class="fa-solid fa-pen-to-square"></i>
                </button>
                <button onclick="audio.toggleMute()" id="btn-audio" title="Activar/Desactivar Sonido" class="w-9 h-9 rounded-lg bg-slate-800 hover:bg-slate-700 text-slate-300 hover:text-white flex items-center justify-center transition border border-slate-700">
                    <i class="fa-solid fa-volume-high" id="audio-icon"></i>
                </button>
            </div>
        </div>
    </header>

    <!-- MAIN CONTENT CONTAINER -->
    <main class="flex-grow max-w-6xl w-full mx-auto p-4 flex flex-col justify-center relative">

        <!-- VIEW 1: HOME MENU -->
        <section id="view-home" class="space-y-8 py-6">
            <!-- Hero Banner -->
            <div class="relative overflow-hidden rounded-3xl bg-gradient-to-br from-emerald-950 via-slate-900 to-orange-950 p-6 md:p-10 border border-emerald-500/30 shadow-2xl text-white">
                <div class="absolute -right-10 -bottom-10 w-72 h-72 bg-emerald-500/10 rounded-full blur-3xl pointer-events-none"></div>
                <div class="absolute -left-10 -top-10 w-72 h-72 bg-orange-500/10 rounded-full blur-3xl pointer-events-none"></div>

                <div class="relative z-10 max-w-2xl space-y-4">
                    <span class="inline-flex items-center gap-2 px-3 py-1 rounded-full bg-emerald-500/10 text-emerald-300 border border-emerald-500/30 text-xs font-semibold uppercase tracking-wider">
                        <i class="fa-solid fa-certificate"></i> Plataforma Académica Ágora
                    </span>
                    <h2 class="text-3xl md:text-5xl font-heading font-extrabold text-white leading-tight">
                        Demuestra tu Poder <span class="text-transparent bg-clip-text bg-gradient-to-r from-emerald-400 to-orange-400">Matemático</span>
                    </h2>
                    <p class="text-slate-300 text-sm md:text-base leading-relaxed">
                        Bienvenido a la plataforma oficial de retos de <strong>agora.edu.pe</strong>. Resuelve problemas numéricos, vence a los jefes del conocimiento, acumula puntos y obtén tu certificado digital.
                    </p>
                    
                    <!-- Player Name Input -->
                    <div class="pt-2 flex flex-col sm:flex-row items-stretch gap-3 max-w-md">
                        <input type="text" id="player-name-input" placeholder="Ingresa tu nombre completo..." class="px-4 py-3 rounded-xl bg-slate-800/90 text-white border border-slate-700 focus:outline-none focus:border-emerald-400 font-medium text-sm flex-grow placeholder-slate-500 shadow-inner">
                        <button onclick="game.savePlayerName()" class="px-6 py-3 bg-gradient-to-r from-emerald-500 to-orange-500 hover:from-emerald-400 hover:to-orange-400 text-white font-heading font-bold rounded-xl shadow-lg shadow-emerald-500/20 transition transform active:scale-95 flex items-center justify-center gap-2">
                            <i class="fa-solid fa-user-check"></i> Registrar
                        </button>
                    </div>
                </div>
            </div>

            <!-- Game Mode Cards Grid -->
            <div class="grid grid-cols-1 md:grid-cols-3 gap-6">
                <!-- Mode 1: Academic Levels -->
                <div onclick="game.startMode('adventure')" class="group relative bg-slate-800/80 hover:bg-slate-800 rounded-2xl p-6 border border-emerald-500/30 hover:border-emerald-400 transition duration-300 cursor-pointer flex flex-col justify-between shadow-xl hover:-translate-y-1">
                    <div class="space-y-4">
                        <div class="w-14 h-14 rounded-2xl bg-emerald-500/10 text-emerald-400 border border-emerald-500/30 flex items-center justify-center text-2xl group-hover:scale-110 transition duration-300">
                            <i class="fa-solid fa-map-location-dot"></i>
                        </div>
                        <div>
                            <h3 class="font-heading font-bold text-xl text-white group-hover:text-emerald-400 transition">Modo Aventura</h3>
                            <p class="text-slate-400 text-sm mt-1">Supera 8 niveles progresivos: Aritmética, Álgebra, Geometría y Porcentajes.</p>
                        </div>
                    </div>
                    <div class="mt-6 flex items-center justify-between text-xs font-semibold text-emerald-400 pt-4 border-t border-slate-700/50">
                        <span>8 Niveles • Modo Principal</span>
                        <i class="fa-solid fa-arrow-right group-hover:translate-x-1 transition"></i>
                    </div>
                </div>

                <!-- Mode 2: Boss Battle -->
                <div onclick="game.startMode('boss')" class="group relative bg-slate-800/80 hover:bg-slate-800 rounded-2xl p-6 border border-orange-500/30 hover:border-orange-400 transition duration-300 cursor-pointer flex flex-col justify-between shadow-xl hover:-translate-y-1">
                    <div class="space-y-4">
                        <div class="w-14 h-14 rounded-2xl bg-orange-500/10 text-orange-400 border border-orange-500/30 flex items-center justify-center text-2xl group-hover:scale-110 transition duration-300">
                            <i class="fa-solid fa-dragon"></i>
                        </div>
                        <div>
                            <h3 class="font-heading font-bold text-xl text-white group-hover:text-orange-400 transition">Batalla de Jefes</h3>
                            <p class="text-slate-400 text-sm mt-1">Ataca a los guardianes matemáticos resolviendo acertijos rápidos antes de que se agote tu salud.</p>
                        </div>
                    </div>
                    <div class="mt-6 flex items-center justify-between text-xs font-semibold text-orange-400 pt-4 border-t border-slate-700/50">
                        <span>3 Jefes Académicos</span>
                        <i class="fa-solid fa-arrow-right group-hover:translate-x-1 transition"></i>
                    </div>
                </div>

                <!-- Mode 3: Time Attack -->
                <div onclick="game.startMode('speed')" class="group relative bg-slate-800/80 hover:bg-slate-800 rounded-2xl p-6 border border-amber-500/30 hover:border-amber-400 transition duration-300 cursor-pointer flex flex-col justify-between shadow-xl hover:-translate-y-1">
                    <div class="space-y-4">
                        <div class="w-14 h-14 rounded-2xl bg-amber-500/10 text-amber-400 border border-amber-500/30 flex items-center justify-center text-2xl group-hover:scale-110 transition duration-300">
                            <i class="fa-solid fa-stopwatch"></i>
                        </div>
                        <div>
                            <h3 class="font-heading font-bold text-xl text-white group-hover:text-amber-400 transition">Contrarreloj 60s</h3>
                            <p class="text-slate-400 text-sm mt-1">¿Cuántas operaciones puedes resolver en 1 minuto? Pon a prueba tu agilidad mental.</p>
                        </div>
                    </div>
                    <div class="mt-6 flex items-center justify-between text-xs font-semibold text-amber-400 pt-4 border-t border-slate-700/50">
                        <span>Máxima Velocidad</span>
                        <i class="fa-solid fa-arrow-right group-hover:translate-x-1 transition"></i>
                    </div>
                </div>
            </div>

            <!-- Badges & Statistics Panel -->
            <div class="bg-slate-800/60 rounded-2xl p-6 border border-slate-700 flex flex-col md:flex-row items-center justify-between gap-6">
                <div class="flex items-center gap-4">
                    <div class="w-12 h-12 rounded-xl bg-emerald-500/20 text-emerald-400 flex items-center justify-center text-2xl">
                        <i class="fa-solid fa-award"></i>
                    </div>
                    <div>
                        <h4 class="font-heading font-bold text-white text-lg">Tu Progreso Académico</h4>
                        <p class="text-xs text-slate-400" id="stat-summary-text">Crea tu nombre para registrar tus logros e insignias.</p>
                    </div>
                </div>
                <div class="flex items-center gap-3">
                    <button onclick="game.showCertificateModal()" class="px-5 py-2.5 bg-emerald-600/20 hover:bg-emerald-600/30 text-emerald-300 rounded-xl text-xs font-bold transition flex items-center gap-2 border border-emerald-500/30">
                        <i class="fa-solid fa-file-certificate text-emerald-400"></i> Ver Certificado
                    </button>
                    <button onclick="game.showStatsModal()" class="px-5 py-2.5 bg-orange-600/20 hover:bg-orange-600/30 text-orange-300 rounded-xl text-xs font-bold transition flex items-center gap-2 border border-orange-500/30">
                        <i class="fa-solid fa-chart-pie"></i> Estadísticas
                    </button>
                </div>
            </div>
        </section>

        <!-- VIEW 2: GAMEPLAY CONTAINER -->
        <section id="view-game" class="hidden max-w-3xl mx-auto w-full space-y-6 py-4">
            
            <!-- Game Level Header / Timer Bar -->
            <div class="bg-slate-800/90 rounded-2xl p-4 border border-slate-700 shadow-lg flex items-center justify-between gap-4">
                <div class="flex items-center gap-3">
                    <button onclick="game.confirmExit()" class="w-9 h-9 rounded-xl bg-slate-700 hover:bg-slate-600 text-slate-300 flex items-center justify-center transition">
                        <i class="fa-solid fa-arrow-left"></i>
                    </button>
                    <div>
                        <span id="game-category-tag" class="text-[10px] uppercase font-bold text-orange-400 tracking-wider">Aritmética</span>
                        <h3 id="game-title-text" class="font-heading font-bold text-white text-base md:text-lg leading-none">Nivel 1: Sumas Básicas</h3>
                    </div>
                </div>

                <!-- Timer / Boss HP Bar container -->
                <div id="game-timer-container" class="flex items-center gap-2 bg-slate-900 px-4 py-2 rounded-xl border border-emerald-500/30">
                    <i class="fa-solid fa-clock text-orange-400 text-sm"></i>
                    <span id="game-timer-text" class="font-heading font-bold text-white text-lg">00:30</span>
                </div>
            </div>

            <!-- Boss HUD Display (Only visible in Boss Mode) -->
            <div id="boss-hud" class="hidden bg-slate-900/90 rounded-2xl p-5 border border-orange-500/30 space-y-3 relative overflow-hidden">
                <div class="flex items-center justify-between">
                    <div class="flex items-center gap-3">
                        <div id="boss-avatar" class="w-12 h-12 rounded-xl bg-orange-600/20 border border-orange-500/40 text-orange-400 flex items-center justify-center text-2xl animate-bounce-subtle">
                            <i class="fa-solid fa-robot"></i>
                        </div>
                        <div>
                            <h4 id="boss-name" class="font-heading font-bold text-white text-base">Minotauro de las Ecuaciones</h4>
                            <p class="text-xs text-orange-300">Resuelve para infligir daño</p>
                        </div>
                    </div>
                    <div class="text-right">
                        <span id="boss-hp-text" class="font-heading font-bold text-orange-400 text-sm">100 / 100 HP</span>
                    </div>
                </div>
                <!-- Boss Health Bar -->
                <div class="w-full h-3 bg-slate-800 rounded-full overflow-hidden p-0.5 border border-slate-700">
                    <div id="boss-hp-bar" class="h-full bg-gradient-to-r from-orange-500 to-amber-400 rounded-full transition-all duration-300" style="width: 100%;"></div>
                </div>
            </div>

            <!-- Problem Card -->
            <div class="relative bg-slate-800/90 rounded-3xl p-6 md:p-8 border border-slate-700/80 shadow-2xl space-y-6 text-center">
                <!-- Question Counter & Powerups -->
                <div class="flex items-center justify-between text-xs text-slate-400 pb-2 border-b border-slate-700/60">
                    <span id="question-progress-text">Pregunta 1 de 10</span>
                    
                    <!-- Power-up Buttons -->
                    <div class="flex items-center gap-2">
                        <button onclick="game.usePowerup('fifty')" id="btn-powerup-fifty" title="50/50: Elimina dos respuestas incorrectas" class="px-2.5 py-1 rounded-lg bg-slate-700 hover:bg-slate-600 text-orange-400 font-bold flex items-center gap-1 transition border border-slate-600">
                            <i class="fa-solid fa-wand-magic-sparkles text-xs"></i> 50/50
                        </button>
                        <button onclick="game.usePowerup('time')" id="btn-powerup-time" title="+15 Segundos extra" class="px-2.5 py-1 rounded-lg bg-slate-700 hover:bg-slate-600 text-emerald-400 font-bold flex items-center gap-1 transition border border-slate-600">
                            <i class="fa-solid fa-hourglass-plus text-xs"></i> +15s
                        </button>
                    </div>
                </div>

                <!-- Math Problem Display Box -->
                <div class="py-6 px-4 rounded-2xl bg-slate-900/80 border border-emerald-500/20 min-h-[140px] flex flex-col items-center justify-center shadow-inner relative overflow-hidden">
                    <div id="problem-context" class="text-xs font-semibold text-emerald-400 mb-2 uppercase tracking-wider">Calcula la siguiente expresión:</div>
                    <div id="problem-text" class="font-heading font-extrabold text-3xl md:text-5xl text-white tracking-wide">
                        12 + 15 = ?
                    </div>
                    <div id="problem-hint" class="text-xs text-orange-300/90 mt-3 hidden italic">Pista: Suma primero las decenas y luego las unidades.</div>
                </div>

                <!-- Options / Direct Input Grid -->
                <div id="options-container" class="grid grid-cols-1 sm:grid-cols-2 gap-4 pt-2">
                    <!-- Option buttons will be rendered dynamically here -->
                </div>

                <!-- Direct Numeric Numpad Input -->
                <div id="numpad-container" class="hidden max-w-xs mx-auto space-y-3 pt-2">
                    <div class="relative">
                        <input type="text" id="numpad-input" readonly placeholder="Tu respuesta..." class="w-full py-3 px-4 rounded-xl bg-slate-900 text-center font-heading font-bold text-2xl text-emerald-400 border border-emerald-500/40 focus:outline-none">
                        <button onclick="game.clearNumpad()" class="absolute right-3 top-1/2 -translate-y-1/2 text-slate-400 hover:text-white p-1">
                            <i class="fa-solid fa-backspace"></i>
                        </button>
                    </div>
                    <div class="grid grid-cols-3 gap-2">
                        <button onclick="game.pressNumpad('1')" class="py-3 rounded-xl bg-slate-700 hover:bg-slate-600 font-heading text-xl text-white math-input-btn">1</button>
                        <button onclick="game.pressNumpad('2')" class="py-3 rounded-xl bg-slate-700 hover:bg-slate-600 font-heading text-xl text-white math-input-btn">2</button>
                        <button onclick="game.pressNumpad('3')" class="py-3 rounded-xl bg-slate-700 hover:bg-slate-600 font-heading text-xl text-white math-input-btn">3</button>
                        <button onclick="game.pressNumpad('4')" class="py-3 rounded-xl bg-slate-700 hover:bg-slate-600 font-heading text-xl text-white math-input-btn">4</button>
                        <button onclick="game.pressNumpad('5')" class="py-3 rounded-xl bg-slate-700 hover:bg-slate-600 font-heading text-xl text-white math-input-btn">5</button>
                        <button onclick="game.pressNumpad('6')" class="py-3 rounded-xl bg-slate-700 hover:bg-slate-600 font-heading text-xl text-white math-input-btn">6</button>
                        <button onclick="game.pressNumpad('7')" class="py-3 rounded-xl bg-slate-700 hover:bg-slate-600 font-heading text-xl text-white math-input-btn">7</button>
                        <button onclick="game.pressNumpad('8')" class="py-3 rounded-xl bg-slate-700 hover:bg-slate-600 font-heading text-xl text-white math-input-btn">8</button>
                        <button onclick="game.pressNumpad('9')" class="py-3 rounded-xl bg-slate-700 hover:bg-slate-600 font-heading text-xl text-white math-input-btn">9</button>
                        <button onclick="game.pressNumpad('-')" class="py-3 rounded-xl bg-slate-700 hover:bg-slate-600 font-heading text-xl text-white math-input-btn">-</button>
                        <button onclick="game.pressNumpad('0')" class="py-3 rounded-xl bg-slate-700 hover:bg-slate-600 font-heading text-xl text-white math-input-btn">0</button>
                        <button onclick="game.submitNumpad()" class="py-3 rounded-xl bg-emerald-600 hover:bg-emerald-500 font-heading text-xl text-white math-input-btn">
                            <i class="fa-solid fa-check"></i>
                        </button>
                    </div>
                </div>

            </div>
        </section>

        <!-- SCRATCHPAD DRAWING OVERLAY -->
        <div id="scratchpad-modal" class="hidden fixed inset-0 z-50 bg-slate-950/80 backdrop-blur-md flex items-center justify-center p-4">
            <div class="bg-slate-900 rounded-3xl border border-slate-700 w-full max-w-2xl overflow-hidden flex flex-col shadow-2xl">
                <!-- Scratchpad Header -->
                <div class="px-6 py-4 bg-slate-800 border-b border-slate-700 flex items-center justify-between">
                    <div class="flex items-center gap-2 text-emerald-400 font-heading font-bold text-lg">
                        <i class="fa-solid fa-pen-nib"></i> Pizarra Virtual de Borrador
                    </div>
                    <div class="flex items-center gap-2">
                        <button onclick="scratchpad.clear()" class="px-3 py-1.5 rounded-lg bg-slate-700 hover:bg-slate-600 text-xs text-white font-semibold transition">
                            <i class="fa-solid fa-trash-can mr-1"></i> Limpiar
                        </button>
                        <button onclick="scratchpad.toggle()" class="w-8 h-8 rounded-lg bg-slate-700 hover:bg-slate-600 text-slate-300 flex items-center justify-center">
                            <i class="fa-solid fa-xmark"></i>
                        </button>
                    </div>
                </div>
                <!-- Canvas Area -->
                <div class="p-4 bg-slate-950 flex-grow flex items-center justify-center relative touch-none">
                    <canvas id="scratch-canvas" class="w-full h-80 bg-slate-900 rounded-xl cursor-crosshair border border-slate-800"></canvas>
                </div>
                <!-- Color & Size Toolbar -->
                <div class="px-6 py-3 bg-slate-800 border-t border-slate-700 flex items-center justify-between text-xs">
                    <div class="flex items-center gap-3">
                        <span class="text-slate-400">Color:</span>
                        <button onclick="scratchpad.setColor('#10b981')" class="w-6 h-6 rounded-full bg-emerald-400 ring-2 ring-white/20"></button>
                        <button onclick="scratchpad.setColor('#f97316')" class="w-6 h-6 rounded-full bg-orange-500 ring-2 ring-white/20"></button>
                        <button onclick="scratchpad.setColor('#facc15')" class="w-6 h-6 rounded-full bg-yellow-400 ring-2 ring-white/20"></button>
                        <button onclick="scratchpad.setColor('#ffffff')" class="w-6 h-6 rounded-full bg-white ring-2 ring-white/20"></button>
                    </div>
                    <p class="text-slate-500">Usa el ratón o el dedo para realizar tus cálculos libres</p>
                </div>
            </div>
        </div>

        <!-- VIEW 3: RESULTS & CERTIFICATE MODAL -->
        <div id="results-modal" class="hidden fixed inset-0 z-50 bg-slate-950/85 backdrop-blur-md flex items-center justify-center p-4 overflow-y-auto">
            <div class="bg-slate-900 rounded-3xl border border-emerald-500/30 w-full max-w-xl p-6 md:p-8 space-y-6 shadow-2xl text-center relative my-8">
                
                <!-- Victory/Defeat Icon -->
                <div class="w-20 h-20 rounded-3xl bg-gradient-to-tr from-emerald-500 to-orange-500 mx-auto flex items-center justify-center text-4xl text-white shadow-xl shadow-emerald-500/20 animate-bounce-subtle">
                    <i class="fa-solid fa-trophy" id="result-icon"></i>
                </div>

                <div>
                    <h3 id="result-title" class="font-heading font-extrabold text-2xl md:text-3xl text-white">¡Nivel Completado!</h3>
                    <p id="result-subtitle" class="text-slate-400 text-sm mt-1">Has demostrado excelentes destrezas matemáticas.</p>
                </div>

                <!-- Stars Display -->
                <div class="flex items-center justify-center gap-2 text-3xl text-amber-400 py-2" id="result-stars">
                    <i class="fa-solid fa-star"></i>
                    <i class="fa-solid fa-star"></i>
                    <i class="fa-solid fa-star"></i>
                </div>

                <!-- Summary Breakdown Cards -->
                <div class="grid grid-cols-3 gap-3 pt-2">
                    <div class="bg-slate-800/80 p-3 rounded-2xl border border-slate-700">
                        <span class="text-[10px] uppercase text-slate-400 font-bold block">Puntaje</span>
                        <span id="res-score" class="font-heading font-bold text-xl text-amber-400">1250</span>
                    </div>
                    <div class="bg-slate-800/80 p-3 rounded-2xl border border-slate-700">
                        <span class="text-[10px] uppercase text-slate-400 font-bold block">Precisión</span>
                        <span id="res-accuracy" class="font-heading font-bold text-xl text-emerald-400">90%</span>
                    </div>
                    <div class="bg-slate-800/80 p-3 rounded-2xl border border-slate-700">
                        <span class="text-[10px] uppercase text-slate-400 font-bold block">Racha Máx</span>
                        <span id="res-streak" class="font-heading font-bold text-xl text-orange-400">x5</span>
                    </div>
                </div>

                <!-- Action Buttons -->
                <div class="flex flex-col sm:flex-row items-center justify-center gap-3 pt-4">
                    <button onclick="game.retryCurrentLevel()" class="w-full sm:w-auto px-6 py-3 rounded-xl bg-slate-800 hover:bg-slate-700 text-white font-heading font-bold transition flex items-center justify-center gap-2 border border-slate-700">
                        <i class="fa-solid fa-rotate-right"></i> Reintentar
                    </button>
                    <button onclick="game.nextLevelOrMenu()" class="w-full sm:w-auto px-6 py-3 rounded-xl bg-gradient-to-r from-emerald-500 to-orange-500 hover:from-emerald-400 hover:to-orange-400 text-white font-heading font-bold transition shadow-lg shadow-emerald-500/20 flex items-center justify-center gap-2">
                        <i class="fa-solid fa-play"></i> Continuar
                    </button>
                </div>
            </div>
        </div>

        <!-- CERTIFICATE PREVIEW MODAL -->
        <div id="certificate-modal" class="hidden fixed inset-0 z-50 bg-slate-950/90 backdrop-blur-md flex items-center justify-center p-4 overflow-y-auto">
            <div class="bg-slate-900 rounded-3xl border border-slate-700 w-full max-w-3xl p-6 md:p-8 space-y-6 shadow-2xl relative my-8">
                <button onclick="game.hideCertificateModal()" class="absolute right-6 top-6 w-9 h-9 rounded-xl bg-slate-800 text-slate-400 hover:text-white flex items-center justify-center">
                    <i class="fa-solid fa-xmark"></i>
                </button>

                <!-- Certificate Canvas / Card -->
                <div id="certificate-card" class="bg-slate-50 text-slate-900 rounded-2xl p-8 border-8 border-emerald-700 shadow-2xl text-center space-y-6 relative overflow-hidden font-body">
                    <div class="border-2 border-dashed border-emerald-600 p-6 rounded-xl space-y-4">
                        <div class="flex items-center justify-center gap-2 text-emerald-800 font-heading font-bold text-2xl tracking-wide">
                            <i class="fa-solid fa-graduation-cap text-3xl text-orange-500"></i> ÁGORA .edu.pe
                        </div>
                        <h2 class="text-3xl md:text-4xl font-heading font-extrabold text-slate-900 tracking-wider">CERTIFICADO DE EXCELENCIA</h2>
                        <p class="text-xs uppercase tracking-widest text-slate-500 font-bold">Otorgado a través de la plataforma educativa Ágora</p>

                        <div class="py-4">
                            <p class="text-sm text-slate-600">Se otorga el presente reconocimiento a:</p>
                            <h3 id="cert-player-name" class="text-2xl md:text-3xl font-heading font-bold text-emerald-900 my-2 underline decoration-orange-500 decoration-2">Estudiante Ágora</h3>
                            <p class="text-sm text-slate-600 max-w-md mx-auto">
                                Por demostrar alto rendimiento, velocidad mental y resolución efectiva de problemas en el <strong class="text-emerald-800">Desafío Matemático Ágora</strong>.
                            </p>
                        </div>

                        <div class="flex justify-between items-end pt-6 border-t border-slate-300 text-xs text-slate-600">
                            <div>
                                <p class="font-bold text-slate-800" id="cert-date">Fecha: 12/10/2026</p>
                                <p class="text-[10px] text-slate-500">ID: AGORA-MATH-2026</p>
                            </div>
                            <div class="w-16 h-16 rounded-full bg-gradient-to-tr from-emerald-600 to-orange-500 text-white flex items-center justify-center font-heading text-xs text-center font-bold shadow-md">
                                SELLO<br>ÁGORA
                            </div>
                            <div>
                                <p class="font-bold text-slate-800">agora.edu.pe</p>
                                <p class="text-[10px] text-slate-500">Plataforma Académica</p>
                            </div>
                        </div>
                    </div>
                </div>

                <div class="flex justify-end gap-3">
                    <button onclick="window.print()" class="px-6 py-2.5 rounded-xl bg-emerald-600 hover:bg-emerald-500 text-white font-heading font-bold text-sm transition flex items-center gap-2">
                        <i class="fa-solid fa-print"></i> Imprimir / Guardar PDF
                    </button>
                </div>
            </div>
        </div>

        <!-- CUSTOM NOTIFICATION DIALOG MODAL (REPLACES ALERT & CONFIRM) -->
        <div id="notice-modal" class="hidden fixed inset-0 z-50 bg-slate-950/80 backdrop-blur-md flex items-center justify-center p-4">
            <div class="bg-slate-900 rounded-2xl border border-emerald-500/40 p-6 max-w-md w-full space-y-4 text-center shadow-2xl">
                <div class="w-12 h-12 rounded-xl bg-emerald-500/20 text-emerald-400 flex items-center justify-center text-2xl mx-auto">
                    <i class="fa-solid fa-circle-info" id="notice-icon"></i>
                </div>
                <h4 id="notice-title" class="font-heading font-bold text-lg text-white">Notificación Ágora</h4>
                <p id="notice-message" class="text-sm text-slate-300 leading-relaxed"></p>
                <div class="flex justify-center gap-3 pt-2" id="notice-actions">
                    <button onclick="game.closeNotice()" class="px-6 py-2.5 bg-emerald-600 hover:bg-emerald-500 text-white font-heading font-bold rounded-xl text-xs transition">Aceptar</button>
                </div>
            </div>
        </div>

    </main>

    <!-- FOOTER -->
    <footer class="bg-slate-950 border-t border-slate-800 text-slate-500 text-xs py-4 px-4 text-center">
        <p>© 2026 <strong>agora.edu.pe</strong> — Institución Educativa. Todos los derechos reservados.</p>
    </footer>

    <script>
        class Scratchpad {
            constructor() {
                this.canvas = document.getElementById('scratch-canvas');
                this.ctx = this.canvas.getContext('2d');
                this.isDrawing = false;
                this.color = '#10b981';
                this.setupCanvas();
            }

            setupCanvas() {
                const resize = () => {
                    const rect = this.canvas.getBoundingClientRect();
                    this.canvas.width = rect.width;
                    this.canvas.height = rect.height;
                    this.ctx.lineCap = 'round';
                    this.ctx.lineJoin = 'round';
                    this.ctx.lineWidth = 3;
                    this.ctx.strokeStyle = this.color;
                };
                window.addEventListener('resize', resize);
                setTimeout(resize, 100);

                const getPos = (e) => {
                    const rect = this.canvas.getBoundingClientRect();
                    const clientX = e.touches ? e.touches[0].clientX : e.clientX;
                    const clientY = e.touches ? e.touches[0].clientY : e.clientY;
                    return {
                        x: clientX - rect.left,
                        y: clientY - rect.top
                    };
                };

                const start = (e) => {
                    this.isDrawing = true;
                    const pos = getPos(e);
                    this.ctx.beginPath();
                    this.ctx.moveTo(pos.x, pos.y);
                };

                const move = (e) => {
                    if (!this.isDrawing) return;
                    const pos = getPos(e);
                    this.ctx.lineTo(pos.x, pos.y);
                    this.ctx.stroke();
                };

                const stop = () => {
                    this.isDrawing = false;
                };

                this.canvas.addEventListener('mousedown', start);
                this.canvas.addEventListener('mousemove', move);
                this.canvas.addEventListener('mouseup', stop);
                this.canvas.addEventListener('mouseleave', stop);

                this.canvas.addEventListener('touchstart', start, { passive: true });
                this.canvas.addEventListener('touchmove', move, { passive: true });
                this.canvas.addEventListener('touchend', stop);
            }

            setColor(newColor) {
                this.color = newColor;
                this.ctx.strokeStyle = newColor;
            }

            clear() {
                this.ctx.clearRect(0, 0, this.canvas.width, this.canvas.height);
            }

            toggle() {
                const modal = document.getElementById('scratchpad-modal');
                modal.classList.toggle('hidden');
            }
        }

        const scratchpad = new Scratchpad();
    </script>

    <script>
        const LEVELS_DATA = [
            { id: 1, name: 'Aritmética Básica: Sumas', cat: 'Aritmética', type: 'choices', maxTime: 25 },
            { id: 2, name: 'Restas y Multiplicación', cat: 'Aritmética', type: 'choices', maxTime: 25 },
            { id: 3, name: 'Operaciones Combinadas', cat: 'Aritmética', type: 'choices', maxTime: 30 },
            { id: 4, name: 'Álgebra: Ecuaciones Simples', cat: 'Álgebra', type: 'numpad', maxTime: 35 },
            { id: 5, name: 'Porcentajes Rápidos', cat: 'Aritmética Comercial', type: 'choices', maxTime: 30 },
            { id: 6, name: 'Geometría: Áreas Básicas', cat: 'Geometría', type: 'choices', maxTime: 35 },
            { id: 7, name: 'Fracciones y Decimales', cat: 'Aritmética Avanzada', type: 'choices', maxTime: 35 },
            { id: 8, name: 'Desafío del Sabio Ágora', cat: 'Lógica Ágora', type: 'numpad', maxTime: 40 }
        ];

        const BOSSES_DATA = [
            { name: 'Guardián del Cero', hp: 100, icon: 'fa-solid fa-ghost' },
            { name: 'Esfinge de los Decimales', hp: 150, icon: 'fa-solid fa-dragon' },
            { name: 'Tirano de las Ecuaciones', hp: 200, icon: 'fa-solid fa-robot' }
        ];

        class AgoraGame {
            constructor() {
                this.playerName = localStorage.getItem('agora_player_name') || 'Estudiante Ágora';
                this.score = 0;
                this.streak = 0;
                this.maxStreak = 0;
                this.stars = 0;
                this.currentMode = 'adventure'; // 'adventure', 'boss', 'speed'
                this.currentLevelIdx = 0;
                this.currentQuestionIdx = 0;
                this.totalQuestions = 8;
                this.correctAnswersCount = 0;
                this.timer = null;
                this.timeLeft = 0;
                this.currentProblem = null;
                this.bossHp = 100;
                this.bossMaxHp = 100;

                this.initUI();
            }

            initUI() {
                document.getElementById('player-name-input').value = this.playerName === 'Estudiante Ágora' ? '' : this.playerName;
                this.updateStatSummary();
            }

            savePlayerName() {
                const val = document.getElementById('player-name-input').value.trim();
                if (val) {
                    this.playerName = val;
                    localStorage.setItem('agora_player_name', val);
                    this.updateStatSummary();
                    this.showMessage('¡Excelente!', 'Nombre de estudiante registrado correctamente.');
                }
            }

            updateStatSummary() {
                document.getElementById('stat-summary-text').innerText = `Jugador: ${this.playerName} | Puntos totales: ${this.score}`;
            }

            generateProblem(levelIdx) {
                let text = '', answer = 0, options = [], hint = '';

                switch (levelIdx % 8) {
                    case 0: {
                        const a = Math.floor(Math.random() * 40) + 10;
                        const b = Math.floor(Math.random() * 40) + 10;
                        answer = a + b;
                        text = `${a} + ${b} = ?`;
                        hint = 'Suma unidades con unidades y decenas con decenas.';
                        break;
                    }
                    case 1: {
                        if (Math.random() > 0.5) {
                            const a = Math.floor(Math.random() * 50) + 20;
                            const b = Math.floor(Math.random() * 20) + 5;
                            answer = a - b;
                            text = `${a} - ${b} = ?`;
                        } else {
                            const a = Math.floor(Math.random() * 9) + 2;
                            const b = Math.floor(Math.random() * 9) + 2;
                            answer = a * b;
                            text = `${a} × ${b} = ?`;
                        }
                        break;
                    }
                    case 2: {
                        const a = Math.floor(Math.random() * 10) + 2;
                        const b = Math.floor(Math.random() * 5) + 2;
                        const c = Math.floor(Math.random() * 10) + 1;
                        answer = (a * b) + c;
                        text = `(${a} × ${b}) + ${c} = ?`;
                        break;
                    }
                    case 3: {
                        const x = Math.floor(Math.random() * 15) + 3;
                        const a = Math.floor(Math.random() * 20) + 5;
                        const b = x + a;
                        answer = x;
                        text = `Hallar X:  X + ${a} = ${b}`;
                        hint = `Resta ${a} a ambos lados de la ecuación.`;
                        break;
                    }
                    case 4: {
                        const percents = [10, 20, 25, 50];
                        const p = percents[Math.floor(Math.random() * percents.length)];
                        const val = (Math.floor(Math.random() * 10) + 1) * 20;
                        answer = (p / 100) * val;
                        text = `¿Cuánto es el ${p}% de ${val}?`;
                        break;
                    }
                    case 5: {
                        const base = Math.floor(Math.random() * 8) + 3;
                        const height = Math.floor(Math.random() * 6) + 2;
                        if (Math.random() > 0.5) {
                            answer = base * height;
                            text = `Área de Rectángulo: Base = ${base} cm, Altura = ${height} cm. ¿Área?`;
                        } else {
                            answer = (base * height) / 2;
                            text = `Área de Triángulo: Base = ${base*2} cm, Altura = ${height} cm. ¿Área?`;
                        }
                        break;
                    }
                    case 6: {
                        const denominator = 4;
                        const num1 = Math.floor(Math.random() * 3) + 1;
                        const num2 = Math.floor(Math.random() * 3) + 1;
                        answer = num1 + num2;
                        text = `¿Cuánto es ${num1}/${denominator} + ${num2}/${denominator}? (Escribe solo el numerador final)`;
                        break;
                    }
                    case 7: {
                        const start = Math.floor(Math.random() * 5) + 2;
                        const step = Math.floor(Math.random() * 4) + 2;
                        answer = start + step * 3;
                        text = `Serie numérica: ${start}, ${start+step}, ${start+step*2}, __?`;
                        break;
                    }
                }

                options = [answer];
                while (options.length < 4) {
                    const offset = (Math.floor(Math.random() * 10) + 1) * (Math.random() > 0.5 ? 1 : -1);
                    const wrong = answer + offset;
                    if (!options.includes(wrong) && wrong >= 0) {
                        options.push(wrong);
                    }
                }
                options.sort(() => Math.random() - 0.5);

                return { text, answer, options, hint };
            }

            startMode(mode) {
                this.currentMode = mode;
                this.currentLevelIdx = 0;
                this.currentQuestionIdx = 0;
                this.correctAnswersCount = 0;
                this.streak = 0;
                this.bossHp = 100;
                this.bossMaxHp = 100;

                document.getElementById('view-home').classList.add('hidden');
                document.getElementById('view-game').classList.remove('hidden');
                document.getElementById('hud-bar').classList.remove('hidden');

                if (mode === 'boss') {
                    document.getElementById('boss-hud').classList.remove('hidden');
                } else {
                    document.getElementById('boss-hud').classList.add('hidden');
                }

                this.loadNextQuestion();
            }

            loadNextQuestion() {
                clearInterval(this.timer);

                const currentLevel = LEVELS_DATA[this.currentLevelIdx % LEVELS_DATA.length];
                document.getElementById('game-category-tag').innerText = currentLevel.cat;
                
                if (this.currentMode === 'boss') {
                    const boss = BOSSES_DATA[this.currentLevelIdx % BOSSES_DATA.length];
                    document.getElementById('game-title-text').innerText = `Enfrentamiento: ${boss.name}`;
                    document.getElementById('boss-name').innerText = boss.name;
                } else if (this.currentMode === 'speed') {
                    document.getElementById('game-title-text').innerText = 'Modo Contrarreloj 60s';
                } else {
                    document.getElementById('game-title-text').innerText = `Nivel ${this.currentLevelIdx + 1}: ${currentLevel.name}`;
                }

                document.getElementById('question-progress-text').innerText = `Pregunta ${this.currentQuestionIdx + 1} de ${this.totalQuestions}`;

                // Generate math problem
                this.currentProblem = this.generateProblem(this.currentLevelIdx);
                document.getElementById('problem-text').innerText = this.currentProblem.text;
                
                if (this.currentProblem.hint) {
                    document.getElementById('problem-hint').innerText = this.currentProblem.hint;
                    document.getElementById('problem-hint').classList.remove('hidden');
                } else {
                    document.getElementById('problem-hint').classList.add('hidden');
                }

                // Render Options with Green/Orange styling
                const optionsContainer = document.getElementById('options-container');
                const numpadContainer = document.getElementById('numpad-container');

                if (currentLevel.type === 'numpad') {
                    optionsContainer.classList.add('hidden');
                    numpadContainer.classList.remove('hidden');
                    document.getElementById('numpad-input').value = '';
                } else {
                    numpadContainer.classList.add('hidden');
                    optionsContainer.classList.remove('hidden');
                    optionsContainer.innerHTML = '';

                    this.currentProblem.options.forEach(opt => {
                        const btn = document.createElement('button');
                        btn.className = 'py-4 px-6 rounded-2xl bg-slate-700/80 hover:bg-emerald-600 hover:border-emerald-400 text-white font-heading font-bold text-xl md:text-2xl border border-slate-600 transition shadow-md active:scale-95 flex items-center justify-center';
                        btn.innerText = opt;
                        btn.onclick = () => this.checkAnswer(opt, btn);
                        optionsContainer.appendChild(btn);
                    });
                }

                // Start Timer
                this.timeLeft = currentLevel.maxTime;
                this.updateTimerDisplay();
                this.timer = setInterval(() => {
                    this.timeLeft--;
                    this.updateTimerDisplay();
                    if (this.timeLeft <= 0) {
                        clearInterval(this.timer);
                        this.handleTimeOut();
                    }
                }, 1000);
            }

            updateTimerDisplay() {
                const secs = this.timeLeft < 10 ? `0${this.timeLeft}` : this.timeLeft;
                document.getElementById('game-timer-text').innerText = `00:${secs}`;
            }

            pressNumpad(val) {
                const input = document.getElementById('numpad-input');
                if (input.value.length < 6) {
                    input.value += val;
                }
            }

            clearNumpad() {
                document.getElementById('numpad-input').value = '';
            }

            submitNumpad() {
                const val = parseInt(document.getElementById('numpad-input').value);
                if (!isNaN(val)) {
                    this.checkAnswer(val, null);
                }
            }

            checkAnswer(userAns, buttonElem) {
                clearInterval(this.timer);

                const isCorrect = userAns === this.currentProblem.answer;

                if (isCorrect) {
                    audio.playCorrect();
                    if (buttonElem) {
                        buttonElem.className = 'py-4 px-6 rounded-2xl bg-emerald-600 text-white font-heading font-bold text-xl md:text-2xl border border-emerald-400 animate-pulse';
                    }
                    
                    this.correctAnswersCount++;
                    this.streak++;
                    if (this.streak > this.maxStreak) this.maxStreak = this.streak;

                    const gainedScore = 100 + (this.streak * 20);
                    this.score += gainedScore;

                    if (this.currentMode === 'boss') {
                        this.bossHp = Math.max(0, this.bossHp - 25);
                        document.getElementById('boss-hp-bar').style.width = `${(this.bossHp / this.bossMaxHp) * 100}%`;
                        document.getElementById('boss-hp-text').innerText = `${this.bossHp} / ${this.bossMaxHp} HP`;
                    }
                } else {
                    audio.playWrong();
                    if (buttonElem) {
                        buttonElem.className = 'py-4 px-6 rounded-2xl bg-orange-600 text-white font-heading font-bold text-xl md:text-2xl border border-orange-400';
                    }
                    this.streak = 0;
                }

                this.updateHUD();

                setTimeout(() => {
                    this.currentQuestionIdx++;
                    if (this.currentQuestionIdx >= this.totalQuestions || (this.currentMode === 'boss' && this.bossHp <= 0)) {
                        this.finishLevel();
                    } else {
                        this.loadNextQuestion();
                    }
                }, 800);
            }

            handleTimeOut() {
                audio.playWrong();
                this.streak = 0;
                this.updateHUD();
                this.currentQuestionIdx++;
                if (this.currentQuestionIdx >= this.totalQuestions) {
                    this.finishLevel();
                } else {
                    this.loadNextQuestion();
                }
            }

            usePowerup(type) {
                if (type === 'fifty') {
                    const btns = document.querySelectorAll('#options-container button');
                    let removed = 0;
                    btns.forEach(btn => {
                        if (parseInt(btn.innerText) !== this.currentProblem.answer && removed < 2) {
                            btn.style.visibility = 'hidden';
                            removed++;
                        }
                    });
                    document.getElementById('btn-powerup-fifty').disabled = true;
                    document.getElementById('btn-powerup-fifty').classList.add('opacity-40');
                } else if (type === 'time') {
                    this.timeLeft += 15;
                    this.updateTimerDisplay();
                    document.getElementById('btn-powerup-time').disabled = true;
                    document.getElementById('btn-powerup-time').classList.add('opacity-40');
                }
            }

            updateHUD() {
                document.getElementById('hud-score').innerText = this.score;
                document.getElementById('hud-streak').innerText = `x${this.streak}`;
                document.getElementById('hud-stars').innerText = this.stars;
            }

            finishLevel() {
                audio.playLevelUp();
                
                confetti({
                    particleCount: 80,
                    spread: 70,
                    origin: { y: 0.6 }
                });

                const accuracy = Math.round((this.correctAnswersCount / this.totalQuestions) * 100);
                let awardedStars = 1;
                if (accuracy >= 70) awardedStars = 2;
                if (accuracy >= 90) awardedStars = 3;
                this.stars += awardedStars;

                document.getElementById('res-score').innerText = this.score;
                document.getElementById('res-accuracy').innerText = `${accuracy}%`;
                document.getElementById('res-streak').innerText = `x${this.maxStreak}`;

                const starsContainer = document.getElementById('result-stars');
                starsContainer.innerHTML = '';
                for (let i = 0; i < 3; i++) {
                    const star = document.createElement('i');
                    star.className = i < awardedStars ? 'fa-solid fa-star text-amber-400' : 'fa-regular fa-star text-slate-600';
                    starsContainer.appendChild(star);
                }

                document.getElementById('results-modal').classList.remove('hidden');
            }

            retryCurrentLevel() {
                document.getElementById('results-modal').classList.add('hidden');
                this.currentQuestionIdx = 0;
                this.correctAnswersCount = 0;
                this.loadNextQuestion();
            }

            nextLevelOrMenu() {
                document.getElementById('results-modal').classList.add('hidden');
                this.currentLevelIdx++;
                this.currentQuestionIdx = 0;
                this.correctAnswersCount = 0;
                
                if (this.currentLevelIdx < LEVELS_DATA.length) {
                    this.loadNextQuestion();
                } else {
                    this.goHome();
                }
            }

            goHome() {
                clearInterval(this.timer);
                document.getElementById('view-game').classList.add('hidden');
                document.getElementById('results-modal').classList.add('hidden');
                document.getElementById('hud-bar').classList.add('hidden');
                document.getElementById('view-home').classList.remove('hidden');
                this.updateStatSummary();
            }

            confirmExit() {
                this.showConfirm('Regresar al Menú', '¿Deseas salir al menú principal? Se perderá el progreso de esta partida.', () => {
                    this.goHome();
                });
            }

            showCertificateModal() {
                document.getElementById('cert-player-name').innerText = this.playerName;
                const date = new Date().toLocaleDateString('es-PE');
                document.getElementById('cert-date').innerText = `Fecha: ${date}`;
                document.getElementById('certificate-modal').classList.remove('hidden');
            }

            hideCertificateModal() {
                document.getElementById('certificate-modal').classList.add('hidden');
            }

            showStatsModal() {
                this.showMessage('Estadísticas del Estudiante', `Jugador: ${this.playerName}\nPuntaje acumulado: ${this.score} pts\nEstrellas obtenidas: ${this.stars} ⭐`);
            }

            showMessage(title, msg) {
                document.getElementById('notice-title').innerText = title;
                document.getElementById('notice-message').innerText = msg;
                document.getElementById('notice-actions').innerHTML = `
                    <button onclick="game.closeNotice()" class="px-6 py-2.5 bg-emerald-600 hover:bg-emerald-500 text-white font-heading font-bold rounded-xl text-xs transition">Aceptar</button>
                `;
                document.getElementById('notice-modal').classList.remove('hidden');
            }

            showConfirm(title, msg, onConfirm) {
                document.getElementById('notice-title').innerText = title;
                document.getElementById('notice-message').innerText = msg;
                document.getElementById('notice-actions').innerHTML = `
                    <button onclick="game.closeNotice()" class="px-4 py-2 bg-slate-700 hover:bg-slate-600 text-white font-heading font-bold rounded-xl text-xs transition">Cancelar</button>
                    <button id="notice-confirm-btn" class="px-5 py-2 bg-orange-600 hover:bg-orange-500 text-white font-heading font-bold rounded-xl text-xs transition">Confirmar</button>
                `;
                document.getElementById('notice-confirm-btn').onclick = () => {
                    this.closeNotice();
                    onConfirm();
                };
                document.getElementById('notice-modal').classList.remove('hidden');
            }

            closeNotice() {
                document.getElementById('notice-modal').classList.add('hidden');
            }
        }

        const game = new AgoraGame();
    </script>
</body>
</html>
