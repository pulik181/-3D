<!DOCTYPE html>
<html lang="ru">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>3D Солнечная Система - Земля и Луна</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <link href="https://fonts.googleapis.com/css2?family=Orbitron:wght@400;600;800;900&family=Rajdhani:wght@400;500;600;700&display=swap" rel="stylesheet">
    <script src="https://cdnjs.cloudflare.com/ajax/libs/three.js/r128/three.min.js"></script>
    <script src="https://cdn.jsdelivr.net/npm/three@0.128.0/examples/js/controls/OrbitControls.js"></script>
    <script src="https://unpkg.com/lucide@latest"></script>

    <style>
        body {
            margin: 0;
            padding: 0;
            overflow: hidden;
            background-color: #02040a;
            font-family: 'Rajdhani', sans-serif;
            color: #f3f4f6;
            user-select: none;
        }

        .font-orbitron {
            font-family: 'Orbitron', sans-serif;
        }

        /* Glassmorphism Styles */
        .glass-hud {
            background: rgba(10, 16, 30, 0.78);
            backdrop-filter: blur(20px);
            -webkit-backdrop-filter: blur(20px);
            border: 1px solid rgba(56, 189, 248, 0.25);
            box-shadow: 0 8px 32px 0 rgba(0, 0, 0, 0.75), inset 0 0 20px rgba(56, 189, 248, 0.08);
        }

        .glass-btn {
            background: rgba(15, 23, 42, 0.85);
            backdrop-filter: blur(12px);
            border: 1px solid rgba(255, 255, 255, 0.15);
            transition: all 0.3s cubic-bezier(0.4, 0, 0.2, 1);
        }

        .glass-btn:hover {
            background: rgba(56, 189, 248, 0.25);
            border-color: rgba(56, 189, 248, 0.7);
            box-shadow: 0 0 20px rgba(56, 189, 248, 0.5);
            transform: translateY(-2px);
        }

        .glass-btn.active {
            background: rgba(56, 189, 248, 0.35);
            border-color: #38bdf8;
            box-shadow: 0 0 25px rgba(56, 189, 248, 0.7);
        }

        /* Custom Scrollbars */
        ::-webkit-scrollbar {
            width: 4px;
            height: 4px;
        }
        ::-webkit-scrollbar-track {
            background: rgba(10, 16, 30, 0.6);
        }
        ::-webkit-scrollbar-thumb {
            background: #38bdf8;
            border-radius: 4px;
        }

        /* Glowing text accents */
        .glow-text-cyan {
            text-shadow: 0 0 12px rgba(56, 189, 248, 0.8), 0 0 24px rgba(56, 189, 248, 0.4);
        }

        input[type=range] {
            -webkit-appearance: none;
            background: rgba(255, 255, 255, 0.12);
            border-radius: 8px;
            height: 6px;
        }
        input[type=range]::-webkit-slider-thumb {
            -webkit-appearance: none;
            height: 16px;
            width: 16px;
            border-radius: 50%;
            background: #38bdf8;
            cursor: pointer;
            box-shadow: 0 0 12px #38bdf8;
            transition: transform 0.15s ease-in-out;
        }
        input[type=range]::-webkit-slider-thumb:hover {
            transform: scale(1.3);
        }

        #webgl-container {
            width: 100vw;
            height: 100vh;
            position: absolute;
            top: 0;
            left: 0;
            z-index: 1;
        }

        .ui-layer {
            position: absolute;
            z-index: 10;
            pointer-events: none;
        }

        .pointer-events-auto {
            pointer-events: auto;
        }
    </style>
</head>
<body class="overflow-hidden bg-slate-950">

    <div id="webgl-container"></div>

    <div class="ui-layer inset-0 flex flex-col justify-between p-4 md:p-6">
        
        <!-- Header Bar -->
        <div class="flex flex-wrap items-center justify-between gap-4">
            <div class="glass-hud px-5 py-3 rounded-2xl flex items-center gap-3 pointer-events-auto border-l-4 border-l-cyan-400">
                <i data-lucide="orbit" class="w-8 h-8 text-cyan-400 animate-spin-slow"></i>
                <div>
                    <h1 class="font-orbitron text-lg md:text-xl font-black tracking-wider text-white flex items-center gap-2">
                        СОЛНЕЧНАЯ СИСТЕМА <span class="text-[10px] px-2 py-0.5 rounded bg-cyan-500/20 text-cyan-300 border border-cyan-500/40 font-sans tracking-normal">ЗЕМЛЯ & ЛУНА 3D</span>
                    </h1>
                    <p class="text-xs text-slate-400">Интерактивный симулятор с детальной орбитой Луны</p>
                </div>
            </div>

            <!-- Time Display & Sound Control -->
            <div class="glass-hud px-5 py-2.5 rounded-2xl flex items-center gap-5 pointer-events-auto">
                <div class="flex items-center gap-3">
                    <i data-lucide="clock" class="w-5 h-5 text-cyan-400"></i>
                    <div>
                        <div class="text-[10px] text-slate-400 uppercase tracking-widest">Системное Время</div>
                        <div id="cosmic-date" class="font-orbitron text-sm md:text-base font-bold text-cyan-300 glow-text-cyan">2026-09-21 12:00</div>
                    </div>
                </div>

                <div class="h-7 w-px bg-slate-700/60"></div>

                <div class="flex items-center gap-3">
                    <i data-lucide="gauge" class="w-5 h-5 text-amber-400"></i>
                    <div class="hidden sm:block">
                        <div class="text-[10px] text-slate-400 uppercase tracking-widest">Скорость</div>
                        <div id="speed-indicator" class="font-orbitron text-xs font-semibold text-amber-300">1x (Норм)</div>
                    </div>
                </div>

                <div class="h-7 w-px bg-slate-700/60"></div>

                <button id="audio-toggle-btn" class="glass-btn p-2 rounded-xl text-slate-300 hover:text-cyan-400" title="Космический Эмбиент (Звук)">
                    <i id="audio-icon" data-lucide="volume-x" class="w-5 h-5"></i>
                </button>
            </div>
        </div>

        <!-- Right Celestial Navigation Sidebar -->
        <div class="absolute right-4 top-20 bottom-36 w-16 md:w-60 glass-hud rounded-2xl p-3 pointer-events-auto flex flex-col justify-between overflow-hidden">
            <div class="space-y-2">
                <div class="text-xs font-orbitron text-slate-400 uppercase tracking-wider px-1 hidden md:flex items-center justify-between">
                    <span>Планеты & Луна</span>
                    <i data-lucide="compass" class="w-3.5 h-3.5 text-cyan-400"></i>
                </div>

                <div class="relative hidden md:block">
                    <input type="text" id="planet-search" placeholder="Поиск объекта..." class="w-full bg-slate-900/80 border border-slate-700/80 rounded-xl px-3 py-1.5 text-xs text-slate-200 focus:outline-none focus:border-cyan-400">
                    <i data-lucide="search" class="w-3.5 h-3.5 text-slate-400 absolute right-3 top-2.5"></i>
                </div>
            </div>

            <!-- Dynamic Planet List -->
            <div class="flex flex-col gap-1.5 overflow-y-auto my-2 pr-1" id="planet-buttons-list"></div>

            <div class="space-y-1.5 pt-2 border-t border-slate-700/50">
                <button id="focus-earth-moon-btn" class="w-full glass-btn py-2 rounded-xl text-xs font-semibold flex items-center justify-center gap-2 text-cyan-300 border-cyan-500/40">
                    <i data-lucide="moon" class="w-4 h-4 text-slate-200"></i>
                    <span class="hidden md:inline">Фокус: Земля & Луна</span>
                </button>
                <button id="cinematic-tour-btn" class="w-full glass-btn py-2 rounded-xl text-xs font-semibold flex items-center justify-center gap-2 text-amber-300 border-amber-500/30">
                    <i data-lucide="sparkles" class="w-4 h-4 text-amber-400"></i>
                    <span class="hidden md:inline">Кино-Тур</span>
                </button>
                <button id="reset-cam-btn" class="w-full glass-btn py-2 rounded-xl text-xs font-semibold flex items-center justify-center gap-2 text-slate-300">
                    <i data-lucide="sun" class="w-4 h-4"></i>
                    <span class="hidden md:inline">Общий Вид</span>
                </button>
            </div>
        </div>

        <!-- Left HUD Info Card -->
        <div id="planet-info-panel" class="absolute left-4 top-20 bottom-36 w-80 md:w-96 glass-hud rounded-2xl p-5 pointer-events-auto flex-col justify-between transition-all duration-500 transform -translate-x-[120%] opacity-0 flex">
            <div>
                <div class="flex items-center justify-between pb-3 border-b border-slate-700/60">
                    <div class="flex items-center gap-3">
                        <div id="info-color-indicator" class="w-4 h-4 rounded-full shadow-lg ring-2 ring-white/20"></div>
                        <h2 id="info-name" class="font-orbitron text-2xl font-black text-white tracking-wider">Земля</h2>
                    </div>
                    <button id="close-info-btn" class="p-1 rounded-lg hover:bg-slate-700/50 text-slate-400 hover:text-white">
                        <i data-lucide="x" class="w-5 h-5"></i>
                    </button>
                </div>

                <div class="py-2.5 text-xs text-cyan-300/90 font-semibold uppercase tracking-wider flex items-center gap-2" id="info-type">
                    <i data-lucide="shield-alert" class="w-3.5 h-3.5"></i> Планета земной группы
                </div>

                <p id="info-description" class="text-xs md:text-sm text-slate-300 leading-relaxed mb-4">
                    Описание небесного тела.
                </p>

                <!-- Stats Grid -->
                <div class="grid grid-cols-2 gap-2 my-2">
                    <div class="bg-slate-900/80 p-2.5 rounded-xl border border-slate-800">
                        <span class="text-[10px] text-slate-400 block uppercase">Диаметр</span>
                        <span id="info-diameter" class="font-orbitron text-xs font-semibold text-white">12 742 км</span>
                    </div>
                    <div class="bg-slate-900/80 p-2.5 rounded-xl border border-slate-800">
                        <span class="text-[10px] text-slate-400 block uppercase">Расст. от Солнца</span>
                        <span id="info-distance" class="font-orbitron text-xs font-semibold text-white">149.6 млн км</span>
                    </div>
                    <div class="bg-slate-900/80 p-2.5 rounded-xl border border-slate-800">
                        <span class="text-[10px] text-slate-400 block uppercase">Период обращения</span>
                        <span id="info-period" class="font-orbitron text-xs font-semibold text-white">365.25 дней</span>
                    </div>
                    <div class="bg-slate-900/80 p-2.5 rounded-xl border border-slate-800">
                        <span class="text-[10px] text-slate-400 block uppercase">Температура</span>
                        <span id="info-temp" class="font-orbitron text-xs font-semibold text-amber-300">+15 °C</span>
                    </div>
                    <div class="bg-slate-900/80 p-2.5 rounded-xl border border-slate-800">
                        <span class="text-[10px] text-slate-400 block uppercase">Гравитация</span>
                        <span id="info-gravity" class="font-orbitron text-xs font-semibold text-white">9.81 м/с²</span>
                    </div>
                    <div class="bg-slate-900/80 p-2.5 rounded-xl border border-slate-800">
                        <span class="text-[10px] text-slate-400 block uppercase">Спутники</span>
                        <span id="info-moons" class="font-orbitron text-xs font-semibold text-white">1 (Луна)</span>
                    </div>
                </div>

                <div class="mt-3 bg-cyan-950/30 border border-cyan-500/20 p-2.5 rounded-xl">
                    <span class="text-[10px] text-cyan-400 font-semibold uppercase flex items-center gap-1 mb-1">
                        <i data-lucide="info" class="w-3 h-3"></i> Космический Факт
                    </span>
                    <p id="info-fact" class="text-xs text-slate-300 italic">Единственная известная планета с активной тектоникой плит.</p>
                </div>
            </div>

            <div class="pt-3 border-t border-slate-700/50 flex gap-2">
                <button id="focus-planet-btn" class="flex-1 glass-btn py-2 rounded-xl text-xs font-semibold text-cyan-300 flex items-center justify-center gap-2">
                    <i data-lucide="crosshair" class="w-4 h-4"></i> Следить за объектом
                </button>
            </div>
        </div>

        <!-- Bottom Controls and Carousel -->
        <div class="flex flex-col gap-3 pointer-events-auto">
            <div class="glass-hud px-4 py-2 rounded-2xl flex items-center gap-2 overflow-x-auto justify-start md:justify-center" id="planet-carousel"></div>

            <div class="flex flex-col sm:flex-row items-center justify-between gap-4">
                <div class="glass-hud px-5 py-2.5 rounded-2xl flex items-center gap-4 w-full sm:w-auto justify-between sm:justify-start">
                    <div class="flex items-center gap-2">
                        <button id="btn-rewind" class="glass-btn p-2 rounded-xl text-slate-200" title="-10x скорость">
                            <i data-lucide="rewind" class="w-4 h-4"></i>
                        </button>
                        <button id="btn-play-pause" class="glass-btn p-2.5 rounded-xl text-cyan-300" title="Пауза / Старт">
                            <i id="play-icon" data-lucide="pause" class="w-5 h-5"></i>
                        </button>
                        <button id="btn-ffwd" class="glass-btn p-2 rounded-xl text-slate-200" title="+10x скорость">
                            <i data-lucide="fast-forward" class="w-4 h-4"></i>
                        </button>
                    </div>

                    <div class="h-6 w-px bg-slate-700/60"></div>

                    <div class="flex items-center gap-3 w-36 md:w-52">
                        <i data-lucide="zap" class="w-4 h-4 text-amber-400 shrink-0"></i>
                        <input type="range" id="time-speed-slider" min="-100" max="100" value="1" step="1" class="w-full cursor-pointer">
                    </div>
                </div>

                <div class="glass-hud px-5 py-2.5 rounded-2xl flex items-center gap-3 w-full sm:w-auto justify-center">
                    <button id="toggle-orbits-btn" class="glass-btn active px-3 py-1.5 rounded-xl text-xs font-semibold flex items-center gap-2 text-slate-200">
                        <i data-lucide="circle-dot" class="w-4 h-4 text-cyan-400"></i>
                        <span>Орбиты</span>
                    </button>
                    <button id="toggle-clouds-btn" class="glass-btn active px-3 py-1.5 rounded-xl text-xs font-semibold flex items-center gap-2 text-slate-200">
                        <i data-lucide="cloud" class="w-4 h-4 text-cyan-400"></i>
                        <span>Облака</span>
                    </button>
                    <button id="toggle-grid-btn" class="glass-btn px-3 py-1.5 rounded-xl text-xs font-semibold flex items-center gap-2 text-slate-200">
                        <i data-lucide="grid" class="w-4 h-4 text-cyan-400"></i>
                        <span>Сетка</span>
                    </button>
                </div>
            </div>
        </div>

    </div>

    <script>
        lucide.createIcons();

        // Planet & Moon Dataset
        const CELESTIAL_DATA = [
            {
                id: 'sun',
                name: 'Солнце',
                type: 'Жёлтый карлик (Звезда)',
                color: '#ffcc00',
                radius: 14.0,
                distance: 0,
                speed: 0,
                rotationSpeed: 0.002,
                diameter: '1 392 700 км',
                sunDistance: '0 км',
                period: '25-35 дней',
                temp: '+5 500 °C',
                gravity: '274 м/с²',
                moons: '8 планет',
                fact: 'Солнце вырабатывает энергию путем термоядерного синтеза, превращая 4 миллиона тонн материи в свет каждую секунду.',
                description: 'Центральная звезда нашей системы, содержащая 99.86% всей её массы.'
            },
            {
                id: 'mercury',
                name: 'Меркурий',
                type: 'Планета земной группы',
                color: '#a6a6a6',
                radius: 1.2,
                distance: 24,
                speed: 0.04,
                rotationSpeed: 0.005,
                diameter: '4 879 км',
                sunDistance: '57.9 млн км',
                period: '88 дней',
                temp: '-180 / +430 °C',
                gravity: '3.7 м/с²',
                moons: '0',
                fact: 'Один день на Меркурии длится около 176 земных суток.',
                description: 'Ближайшая к Солнцу и самая маленькая планета с изрытой кратерами поверхностью.'
            },
            {
                id: 'venus',
                name: 'Венера',
                type: 'Планета земной группы',
                color: '#e3bb76',
                radius: 2.2,
                distance: 38,
                speed: 0.015,
                rotationSpeed: -0.002,
                hasAtmosphereHalo: true,
                haloColor: 0xffaa44,
                diameter: '12 104 км',
                sunDistance: '108.2 млн км',
                period: '225 дней',
                temp: '+464 °C',
                gravity: '8.87 м/с²',
                moons: '0',
                fact: 'Венера вращается в обратную сторону по сравнению с большинством планет.',
                description: 'Самая горячая планета благодаря плотному парниковому эффекту из углекислого газа.'
            },
            {
                id: 'earth',
                name: 'Земля',
                type: 'Планета земной группы',
                color: '#2b82c5',
                radius: 2.6,
                distance: 52,
                speed: 0.01,
                rotationSpeed: 0.02,
                hasAtmosphereHalo: true,
                haloColor: 0x0088ff,
                diameter: '12 742 км',
                sunDistance: '149.6 млн км',
                period: '365.25 дней',
                temp: '+15 °C',
                gravity: '9.81 м/с²',
                moons: '1 (Луна)',
                fact: 'Земля — единственная известная планета с жидкой водой и жизнью.',
                description: 'Океанический дом человечества с богатой азотно-кислородной атмосферой.'
            },
            {
                id: 'moon',
                name: 'Луна',
                type: 'Естественный спутник Земли',
                color: '#d1d5db',
                radius: 0.9,
                distance: 52, // Attached to Earth position dynamically
                speed: 0.01,
                rotationSpeed: 0.01,
                diameter: '3 474 км',
                sunDistance: '149.6 млн км',
                period: '27.3 дней',
                temp: '-130 / +120 °C',
                gravity: '1.62 м/с²',
                moons: 'Спутник Земли',
                fact: 'Луна вызывает приливы и отливы в океанах Земли своей гравитацией.',
                description: 'Единственный естественный спутник Земли с покрытой реголитом поверхностью.'
            },
            {
                id: 'mars',
                name: 'Марс',
                type: 'Планета земной группы',
                color: '#c84b31',
                radius: 1.7,
                distance: 70,
                speed: 0.008,
                rotationSpeed: 0.018,
                hasAtmosphereHalo: true,
                haloColor: 0xff4422,
                diameter: '6 779 км',
                sunDistance: '227.9 млн км',
                period: '687 дней',
                temp: '-63 °C',
                gravity: '3.72 м/с²',
                moons: '2 (Фобос, Деймос)',
                fact: 'На Марсе находится вулкан Олимп — высочайшая гора в Солнечной системе (21.9 км).',
                description: 'Красная пустынная планета со следами древних рек и гигантскими каньонами.'
            },
            {
                id: 'jupiter',
                name: 'Юпитер',
                type: 'Газовый гигант',
                color: '#b07f35',
                radius: 6.0,
                distance: 96,
                speed: 0.004,
                rotationSpeed: 0.04,
                diameter: '139 820 км',
                sunDistance: '778.5 млн км',
                period: '11.86 лет',
                temp: '-110 °C',
                gravity: '24.79 м/с²',
                moons: '95',
                fact: 'Большое Красное Пятно — это гигантский ураган, бушующий на Юпитере более 300 лет.',
                description: 'Царь планет, защищающий внутреннюю систему от комет своим мощным притяжением.'
            },
            {
                id: 'saturn',
                name: 'Сатурн',
                type: 'Газовый гигант',
                color: '#e2bf7d',
                radius: 5.0,
                distance: 125,
                speed: 0.003,
                rotationSpeed: 0.038,
                hasRings: true,
                diameter: '116 460 км',
                sunDistance: '1.43 млрд км',
                period: '29.45 лет',
                temp: '-140 °C',
                gravity: '10.44 м/с²',
                moons: '146',
                fact: 'Плотность Сатурна меньше плотности воды — он мог бы плавать в гигантском океане.',
                description: 'Властелин колец из миллиардов ледяных и каменных частиц.'
            },
            {
                id: 'uranus',
                name: 'Уран',
                type: 'Ледяной гигант',
                color: '#4b70dd',
                radius: 3.4,
                distance: 152,
                speed: 0.002,
                rotationSpeed: -0.025,
                hasRings: true,
                diameter: '50 724 км',
                sunDistance: '2.87 млрд км',
                period: '84 года',
                temp: '-195 °C',
                gravity: '8.69 м/с²',
                moons: '28',
                fact: 'Уран вращается «на боку» — наклон его оси составляет почти 98 градусов.',
                description: 'Ледяной гигант с изумрудно-голубой метановой атмосферой.'
            },
            {
                id: 'neptune',
                name: 'Нептун',
                type: 'Ледяной гигант',
                color: '#274687',
                radius: 3.3,
                distance: 180,
                speed: 0.001,
                rotationSpeed: 0.028,
                diameter: '49 244 км',
                sunDistance: '4.50 млрд км',
                period: '164.8 лет',
                temp: '-200 °C',
                gravity: '11.15 м/с²',
                moons: '16',
                fact: 'Скорость ветра на Нептуне достигает сверхзвуковых 2100 км/ч.',
                description: 'Самая дальняя планета, окутанная глубокими синими облаками и ураганами.'
            }
        ];

        // Core Variables
        let scene, camera, renderer, controls;
        let celestialObjects = {};
        let orbitLines = [];
        let earthCloudsMesh, earthMoonSystem, moonMesh, moonOrbitLine;
        let showClouds = true;

        // Audio & Time State
        let audioCtx = null, synthOsc = null, isAudioPlaying = false;
        let isPaused = false;
        let timeSpeed = 1;
        let currentDate = new Date(2026, 8, 21, 12, 0, 0);

        // Camera Tracking
        let targetObject = null;
        let isCinematicTour = false;
        let tourIndex = 0, tourTimer = null;

        let showOrbits = true, showGrid = false, gridHelper;
        let moonAngle = 0;

        function initAudioSynth() {
            try {
                const AudioContext = window.AudioContext || window.webkitAudioContext;
                audioCtx = new AudioContext();

                synthOsc = audioCtx.createOscillator();
                const gainNode = audioCtx.createGain();
                const filter = audioCtx.createBiquadFilter();

                synthOsc.type = 'sine';
                synthOsc.frequency.setValueAtTime(55, audioCtx.currentTime);
                filter.type = 'lowpass';
                filter.frequency.setValueAtTime(220, audioCtx.currentTime);

                gainNode.gain.setValueAtTime(0.08, audioCtx.currentTime);

                synthOsc.connect(filter);
                filter.connect(gainNode);
                gainNode.connect(audioCtx.destination);

                synthOsc.start();
                isAudioPlaying = true;
                updateAudioIcon();
            } catch(e) {
                console.warn('Audio API not supported', e);
            }
        }

        function toggleAudio() {
            if (!audioCtx) {
                initAudioSynth();
                return;
            }
            if (audioCtx.state === 'suspended') {
                audioCtx.resume();
                isAudioPlaying = true;
            } else if (audioCtx.state === 'running') {
                audioCtx.suspend();
                isAudioPlaying = false;
            }
            updateAudioIcon();
        }

        function updateAudioIcon() {
            const icon = document.getElementById('audio-icon');
            icon.setAttribute('data-lucide', isAudioPlaying ? 'volume-2' : 'volume-x');
            lucide.createIcons();
        }

        function createProceduralTexture(type, baseHex) {
            const canvas = document.createElement('canvas');
            canvas.width = 1024;
            canvas.height = 512;
            const ctx = canvas.getContext('2d');

            ctx.fillStyle = baseHex;
            ctx.fillRect(0, 0, canvas.width, canvas.height);

            if (type === 'sun') {
                for (let i = 0; i < 8000; i++) {
                    ctx.fillStyle = Math.random() > 0.4 ? '#ffcc00' : '#ff3300';
                    ctx.beginPath();
                    ctx.arc(Math.random() * canvas.width, Math.random() * canvas.height, Math.random() * 8 + 2, 0, Math.PI * 2);
                    ctx.fill();
                }
            } else if (type === 'earth') {
                ctx.fillStyle = '#1e3a8a';
                ctx.fillRect(0, 0, canvas.width, canvas.height);
                ctx.fillStyle = '#15803d';
                for (let i = 0; i < 45; i++) {
                    ctx.beginPath();
                    ctx.arc(Math.random() * canvas.width, Math.random() * canvas.height, Math.random() * 80 + 20, 0, Math.PI * 2);
                    ctx.fill();
                }
            } else if (type === 'moon') {
                ctx.fillStyle = '#9ca3af';
                ctx.fillRect(0, 0, canvas.width, canvas.height);
                ctx.fillStyle = '#4b5563';
                for (let i = 0; i < 5000; i++) {
                    ctx.beginPath();
                    ctx.arc(Math.random() * canvas.width, Math.random() * canvas.height, Math.random() * 6 + 1, 0, Math.PI * 2);
                    ctx.fill();
                }
            } else if (type === 'jupiter') {
                for (let y = 0; y < canvas.height; y++) {
                    ctx.fillStyle = (y % 40 < 20) ? '#c29b62' : '#8c6139';
                    ctx.fillRect(0, y, canvas.width, 1);
                }
                ctx.fillStyle = '#b91c1c';
                ctx.beginPath();
                ctx.ellipse(canvas.width * 0.6, canvas.height * 0.65, 50, 30, 0, 0, Math.PI * 2);
                ctx.fill();
            } else if (type === 'mars') {
                for (let i = 0; i < 3000; i++) {
                    ctx.fillStyle = `rgba(0,0,0,${Math.random() * 0.25})`;
                    ctx.beginPath();
                    ctx.arc(Math.random() * canvas.width, Math.random() * canvas.height, Math.random() * 6, 0, Math.PI * 2);
                    ctx.fill();
                }
                ctx.fillStyle = '#ffffff';
                ctx.fillRect(0, 0, canvas.width, 30);
                ctx.fillRect(0, canvas.height - 30, canvas.width, 30);
            } else {
                for (let i = 0; i < 4000; i++) {
                    ctx.fillStyle = `rgba(0, 0, 0, ${Math.random() * 0.2})`;
                    ctx.beginPath();
                    ctx.arc(Math.random() * canvas.width, Math.random() * canvas.height, Math.random() * 5, 0, Math.PI * 2);
                    ctx.fill();
                }
            }

            return new THREE.CanvasTexture(canvas);
        }

        function initScene() {
            const container = document.getElementById('webgl-container');

            scene = new THREE.Scene();
            scene.fog = new THREE.FogExp2(0x02040a, 0.0012);

            camera = new THREE.PerspectiveCamera(45, window.innerWidth / window.innerHeight, 0.1, 2500);
            camera.position.set(0, 90, 240);

            renderer = new THREE.WebGLRenderer({ antialias: true, alpha: true, powerPreference: "high-performance" });
            renderer.setSize(window.innerWidth, window.innerHeight);
            renderer.setPixelRatio(Math.min(window.devicePixelRatio, 2));
            renderer.shadowMap.enabled = true;
            renderer.shadowMap.type = THREE.PCFSoftShadowMap;
            renderer.toneMapping = THREE.ACESFilmicToneMapping;
            container.appendChild(renderer.domElement);

            controls = new THREE.OrbitControls(camera, renderer.domElement);
            controls.enableDamping = true;
            controls.dampingFactor = 0.05;
            controls.maxDistance = 800;
            controls.minDistance = 3;

            const ambientLight = new THREE.AmbientLight(0x475569, 1.4);
            scene.add(ambientLight);

            const sunLight = new THREE.PointLight(0xffffff, 3.2, 1400, 0.1);
            sunLight.castShadow = true;
            sunLight.shadow.mapSize.width = 2048;
            sunLight.shadow.mapSize.height = 2048;
            scene.add(sunLight);

            createStarfieldAndNebula();

            gridHelper = new THREE.GridHelper(500, 100, 0x38bdf8, 0x1e293b);
            gridHelper.position.y = -15;
            gridHelper.visible = false;
            scene.add(gridHelper);

            buildSolarSystem();

            window.addEventListener('resize', onWindowResize);
            setupRaycaster();
        }

        function createStarfieldAndNebula() {
            const count = 10000;
            const geometry = new THREE.BufferGeometry();
            const positions = new Float32Array(count * 3);
            const colors = new Float32Array(count * 3);

            for (let i = 0; i < count; i++) {
                const r = 600 + Math.random() * 600;
                const theta = Math.random() * Math.PI * 2;
                const phi = Math.acos((Math.random() * 2) - 1);

                positions[i * 3] = r * Math.sin(phi) * Math.cos(theta);
                positions[i * 3 + 1] = r * Math.sin(phi) * Math.sin(theta);
                positions[i * 3 + 2] = r * Math.cos(phi);

                const color = new THREE.Color();
                const starType = Math.random();
                if (starType > 0.85) color.setHSL(0.55, 0.9, 0.85);
                else if (starType > 0.7) color.setHSL(0.08, 0.9, 0.85);
                else color.setHSL(0, 0, 0.95);

                colors[i * 3] = color.r;
                colors[i * 3 + 1] = color.g;
                colors[i * 3 + 2] = color.b;
            }

            geometry.setAttribute('position', new THREE.BufferAttribute(positions, 3));
            geometry.setAttribute('color', new THREE.BufferAttribute(colors, 3));

            const material = new THREE.PointsMaterial({
                size: 1.3,
                vertexColors: true,
                transparent: true,
                opacity: 0.85
            });

            scene.add(new THREE.Points(geometry, material));
        }

        function buildSolarSystem() {
            CELESTIAL_DATA.forEach(data => {
                if (data.id === 'moon') return; // Handled dynamically inside Earth system

                const pivot = new THREE.Group();
                scene.add(pivot);

                let mesh;
                if (data.id === 'sun') {
                    const geom = new THREE.SphereGeometry(data.radius, 64, 64);
                    const mat = new THREE.MeshBasicMaterial({
                        map: createProceduralTexture('sun', data.color),
                        color: new THREE.Color(data.color)
                    });
                    mesh = new THREE.Mesh(geom, mat);

                    const coronaGeom = new THREE.SphereGeometry(data.radius * 1.3, 32, 32);
                    const coronaMat = new THREE.MeshBasicMaterial({
                        color: new THREE.Color(0xffaa00),
                        transparent: true,
                        opacity: 0.25,
                        side: THREE.BackSide
                    });
                    mesh.add(new THREE.Mesh(coronaGeom, coronaMat));

                } else {
                    const geom = new THREE.SphereGeometry(data.radius, 64, 64);
                    const mat = new THREE.MeshStandardMaterial({
                        map: createProceduralTexture(data.id, data.color),
                        roughness: 0.7,
                        metalness: 0.1
                    });
                    mesh = new THREE.Mesh(geom, mat);
                    mesh.castShadow = true;
                    mesh.receiveShadow = true;
                    mesh.position.x = data.distance;

                    if (data.hasAtmosphereHalo) {
                        const haloGeom = new THREE.SphereGeometry(data.radius * 1.12, 32, 32);
                        const haloMat = new THREE.MeshBasicMaterial({
                            color: new THREE.Color(data.haloColor),
                            transparent: true,
                            opacity: 0.18,
                            side: THREE.BackSide
                        });
                        mesh.add(new THREE.Mesh(haloGeom, haloMat));
                    }

                    // Build Earth-Moon System
                    if (data.id === 'earth') {
                        earthMoonSystem = new THREE.Group();
                        mesh.add(earthMoonSystem);

                        // Earth Cloud Layer
                        const cloudGeom = new THREE.SphereGeometry(data.radius * 1.03, 32, 32);
                        const cloudCanvas = document.createElement('canvas');
                        cloudCanvas.width = 512;
                        cloudCanvas.height = 256;
                        const cctx = cloudCanvas.getContext('2d');
                        cctx.fillStyle = '#ffffff';
                        for (let i = 0; i < 50; i++) {
                            cctx.beginPath();
                            cctx.arc(Math.random() * 512, Math.random() * 256, Math.random() * 30 + 10, 0, Math.PI * 2);
                            cctx.fill();
                        }
                        const cloudMat = new THREE.MeshStandardMaterial({
                            map: new THREE.CanvasTexture(cloudCanvas),
                            transparent: true,
                            opacity: 0.4
                        });
                        earthCloudsMesh = new THREE.Mesh(cloudGeom, cloudMat);
                        mesh.add(earthCloudsMesh);

                        // Moon Mesh construction
                        const moonData = CELESTIAL_DATA.find(d => d.id === 'moon');
                        const moonGeom = new THREE.SphereGeometry(moonData.radius, 32, 32);
                        const moonMat = new THREE.MeshStandardMaterial({
                            map: createProceduralTexture('moon', moonData.color),
                            roughness: 0.9,
                            metalness: 0.05
                        });
                        moonMesh = new THREE.Mesh(moonGeom, moonMat);
                        moonMesh.castShadow = true;
                        moonMesh.receiveShadow = true;
                        moonMesh.position.x = 6.5; // Visual distance from Earth
                        earthMoonSystem.add(moonMesh);

                        // Moon Orbit Ring Path around Earth
                        const moonPoints = [];
                        for (let i = 0; i <= 64; i++) {
                            const theta = (i / 64) * Math.PI * 2;
                            moonPoints.push(new THREE.Vector3(Math.cos(theta) * 6.5, 0, Math.sin(theta) * 6.5));
                        }
                        const moonOrbitGeom = new THREE.BufferGeometry().setFromPoints(moonPoints);
                        moonOrbitLine = new THREE.LineLoop(moonOrbitGeom, new THREE.LineBasicMaterial({
                            color: 0xffffff,
                            transparent: true,
                            opacity: 0.35
                        }));
                        earthMoonSystem.add(moonOrbitLine);

                        // Register Moon into Object List
                        celestialObjects['moon'] = {
                            pivot: earthMoonSystem,
                            mesh: moonMesh,
                            angle: 0,
                            data: moonData
                        };
                    }

                    if (data.hasRings) {
                        const ringGeom = new THREE.RingGeometry(data.radius * 1.4, data.radius * 2.5, 64);
                        const ringMat = new THREE.MeshStandardMaterial({
                            color: new THREE.Color(data.color),
                            side: THREE.DoubleSide,
                            transparent: true,
                            opacity: 0.75
                        });
                        const ringMesh = new THREE.Mesh(ringGeom, ringMat);
                        ringMesh.rotation.x = Math.PI / 2.2;
                        mesh.add(ringMesh);
                    }

                    createOrbitLine(data.distance);
                }

                mesh.userData = { id: data.id, name: data.name };
                pivot.add(mesh);

                celestialObjects[data.id] = {
                    pivot: pivot,
                    mesh: mesh,
                    angle: Math.random() * Math.PI * 2,
                    data: data
                };
            });

            populateUIElements();
        }

        function createOrbitLine(distance) {
            const points = [];
            const segments = 128;
            for (let i = 0; i <= segments; i++) {
                const theta = (i / segments) * Math.PI * 2;
                points.push(new THREE.Vector3(Math.cos(theta) * distance, 0, Math.sin(theta) * distance));
            }

            const geometry = new THREE.BufferGeometry().setFromPoints(points);
            const material = new THREE.LineBasicMaterial({
                color: 0x38bdf8,
                transparent: true,
                opacity: 0.18
            });

            const orbitLine = new THREE.LineLoop(geometry, material);
            scene.add(orbitLine);
            orbitLines.push(orbitLine);
        }

        function populateUIElements() {
            const listContainer = document.getElementById('planet-buttons-list');
            const carouselContainer = document.getElementById('planet-carousel');
            
            listContainer.innerHTML = '';
            carouselContainer.innerHTML = '';

            CELESTIAL_DATA.forEach(planet => {
                const btn = document.createElement('button');
                btn.className = `glass-btn w-full p-2 rounded-xl text-left flex items-center gap-2.5 transition-all text-xs font-semibold hover:border-cyan-400`;
                btn.id = `btn-planet-${planet.id}`;
                btn.innerHTML = `
                    <div class="w-3 h-3 rounded-full shrink-0 shadow-md" style="background-color: ${planet.color}"></div>
                    <span class="text-slate-200 hidden md:inline truncate">${planet.name}</span>
                `;
                btn.addEventListener('click', () => selectPlanet(planet.id));
                listContainer.appendChild(btn);

                const carBtn = document.createElement('button');
                carBtn.className = `glass-btn px-3 py-1.5 rounded-xl text-xs font-semibold flex items-center gap-2 shrink-0 border border-slate-700/60 hover:border-cyan-400`;
                carBtn.id = `car-planet-${planet.id}`;
                carBtn.innerHTML = `
                    <div class="w-2.5 h-2.5 rounded-full" style="background-color: ${planet.color}"></div>
                    <span class="text-slate-200">${planet.name}</span>
                `;
                carBtn.addEventListener('click', () => selectPlanet(planet.id));
                carouselContainer.appendChild(carBtn);
            });
        }

        function selectPlanet(planetId) {
            if (isCinematicTour) stopCinematicTour();

            document.querySelectorAll('#planet-buttons-list button, #planet-carousel button').forEach(b => b.classList.remove('active'));
            const activeNav = document.getElementById(`btn-planet-${planetId}`);
            const activeCar = document.getElementById(`car-planet-${planetId}`);
            if (activeNav) activeNav.classList.add('active');
            if (activeCar) activeCar.classList.add('active');

            const celestial = celestialObjects[planetId];
            if (!celestial) return;

            targetObject = celestial;

            const infoPanel = document.getElementById('planet-info-panel');
            const data = celestial.data;

            document.getElementById('info-name').innerText = data.name;
            document.getElementById('info-type').innerText = data.type;
            document.getElementById('info-description').innerText = data.description;
            document.getElementById('info-diameter').innerText = data.diameter;
            document.getElementById('info-distance').innerText = data.sunDistance;
            document.getElementById('info-period').innerText = data.period;
            document.getElementById('info-temp').innerText = data.temp;
            document.getElementById('info-gravity').innerText = data.gravity;
            document.getElementById('info-moons').innerText = data.moons;
            document.getElementById('info-fact').innerText = data.fact;
            document.getElementById('info-color-indicator').style.backgroundColor = data.color;

            infoPanel.classList.remove('-translate-x-[120%]', 'opacity-0');
            infoPanel.classList.add('translate-x-0', 'opacity-100');
        }

        function resetCameraFocus() {
            targetObject = null;
            if (isCinematicTour) stopCinematicTour();

            document.querySelectorAll('#planet-buttons-list button, #planet-carousel button').forEach(b => b.classList.remove('active'));
            
            const infoPanel = document.getElementById('planet-info-panel');
            infoPanel.classList.add('-translate-x-[120%]', 'opacity-0');
            infoPanel.classList.remove('translate-x-0', 'opacity-100');

            const targetPos = new THREE.Vector3(0, 0, 0);
            const targetCamPos = new THREE.Vector3(0, 90, 240);

            let t = 0;
            function animateReset() {
                if (t < 1) {
                    t += 0.04;
                    controls.target.lerp(targetPos, 0.1);
                    camera.position.lerp(targetCamPos, 0.08);
                    controls.update();
                    requestAnimationFrame(animateReset);
                }
            }
            animateReset();
        }

        function focusEarthAndMoon() {
            selectPlanet('earth');
        }

        function startCinematicTour() {
            isCinematicTour = true;
            tourIndex = 0;
            document.getElementById('cinematic-tour-btn').classList.add('active');

            function nextTourStep() {
                if (!isCinematicTour) return;
                const planet = CELESTIAL_DATA[tourIndex];
                selectPlanet(planet.id);
                tourIndex = (tourIndex + 1) % CELESTIAL_DATA.length;
                tourTimer = setTimeout(nextTourStep, 6000);
            }

            nextTourStep();
        }

        function stopCinematicTour() {
            isCinematicTour = false;
            if (tourTimer) clearTimeout(tourTimer);
            document.getElementById('cinematic-tour-btn').classList.remove('active');
        }

        function setupRaycaster() {
            const raycaster = new THREE.Raycaster();
            const mouse = new THREE.Vector2();

            window.addEventListener('click', (e) => {
                if (e.target.closest('.pointer-events-auto')) return;

                mouse.x = (e.clientX / window.innerWidth) * 2 - 1;
                mouse.y = -(e.clientY / window.innerHeight) * 2 + 1;

                raycaster.setFromCamera(mouse, camera);
                const intersects = raycaster.intersectObjects(scene.children, true);

                if (intersects.length > 0) {
                    let clickedMesh = intersects[0].object;
                    while (clickedMesh && !clickedMesh.userData?.id && clickedMesh.parent) {
                        clickedMesh = clickedMesh.parent;
                    }
                    if (clickedMesh && clickedMesh.userData?.id) {
                        selectPlanet(clickedMesh.userData.id);
                    }
                }
            });
        }

        function setupEventListeners() {
            document.getElementById('audio-toggle-btn').addEventListener('click', toggleAudio);

            const playPauseBtn = document.getElementById('btn-play-pause');
            const playIcon = document.getElementById('play-icon');
            playPauseBtn.addEventListener('click', () => {
                isPaused = !isPaused;
                playIcon.setAttribute('data-lucide', isPaused ? 'play' : 'pause');
                lucide.createIcons();
                playPauseBtn.classList.toggle('active', !isPaused);
            });

            document.getElementById('btn-rewind').addEventListener('click', () => {
                const slider = document.getElementById('time-speed-slider');
                slider.value = Math.max(-100, parseInt(slider.value) - 10);
                updateTimeSpeed(slider.value);
            });

            document.getElementById('btn-ffwd').addEventListener('click', () => {
                const slider = document.getElementById('time-speed-slider');
                slider.value = Math.min(100, parseInt(slider.value) + 10);
                updateTimeSpeed(slider.value);
            });

            const speedSlider = document.getElementById('time-speed-slider');
            speedSlider.addEventListener('input', (e) => updateTimeSpeed(e.target.value));

            const orbitsBtn = document.getElementById('toggle-orbits-btn');
            orbitsBtn.addEventListener('click', () => {
                showOrbits = !showOrbits;
                orbitsBtn.classList.toggle('active', showOrbits);
                orbitLines.forEach(line => line.visible = showOrbits);
                if (moonOrbitLine) moonOrbitLine.visible = showOrbits;
            });

            const cloudsBtn = document.getElementById('toggle-clouds-btn');
            cloudsBtn.addEventListener('click', () => {
                showClouds = !showClouds;
                cloudsBtn.classList.toggle('active', showClouds);
                if (earthCloudsMesh) earthCloudsMesh.visible = showClouds;
            });

            const gridBtn = document.getElementById('toggle-grid-btn');
            gridBtn.addEventListener('click', () => {
                showGrid = !showGrid;
                gridBtn.classList.toggle('active', showGrid);
                gridHelper.visible = showGrid;
            });

            document.getElementById('cinematic-tour-btn').addEventListener('click', () => {
                if (isCinematicTour) stopCinematicTour();
                else startCinematicTour();
            });

            document.getElementById('focus-earth-moon-btn').addEventListener('click', focusEarthAndMoon);
            document.getElementById('close-info-btn').addEventListener('click', resetCameraFocus);
            document.getElementById('reset-cam-btn').addEventListener('click', resetCameraFocus);
            document.getElementById('focus-planet-btn').addEventListener('click', () => {
                if (targetObject) {
                    controls.target.copy(targetObject.mesh.getWorldPosition(new THREE.Vector3()));
                }
            });

            document.getElementById('planet-search').addEventListener('input', (e) => {
                const query = e.target.value.toLowerCase();
                CELESTIAL_DATA.forEach(planet => {
                    const btn = document.getElementById(`btn-planet-${planet.id}`);
                    if (btn) {
                        btn.style.display = planet.name.toLowerCase().includes(query) ? 'flex' : 'none';
                    }
                });
            });
        }

        function updateTimeSpeed(value) {
            timeSpeed = parseFloat(value);
            const indicator = document.getElementById('speed-indicator');
            indicator.innerText = `${timeSpeed}x ${timeSpeed < 0 ? '(Назад)' : ''}`;
        }

        function onWindowResize() {
            camera.aspect = window.innerWidth / window.innerHeight;
            camera.updateProjectionMatrix();
            renderer.setSize(window.innerWidth, window.innerHeight);
        }

        function animate() {
            requestAnimationFrame(animate);

            if (!isPaused) {
                const deltaMs = timeSpeed * 1000 * 60 * 60 * 3;
                currentDate = new Date(currentDate.getTime() + deltaMs);
                document.getElementById('cosmic-date').innerText = currentDate.toISOString().replace('T', ' ').substring(0, 16);

                Object.keys(celestialObjects).forEach(key => {
                    if (key === 'moon') return;

                    const obj = celestialObjects[key];
                    const data = obj.data;

                    if (data.speed !== 0) {
                        obj.angle += data.speed * 0.04 * timeSpeed;
                        obj.mesh.position.x = Math.cos(obj.angle) * data.distance;
                        obj.mesh.position.z = Math.sin(obj.angle) * data.distance;
                    }

                    obj.mesh.rotation.y += data.rotationSpeed * (timeSpeed > 0 ? 1 : -1);
                });

                // Moon Orbit animation around Earth
                if (earthMoonSystem && moonMesh) {
                    moonAngle += 0.03 * timeSpeed;
                    moonMesh.position.x = Math.cos(moonAngle) * 6.5;
                    moonMesh.position.z = Math.sin(moonAngle) * 6.5;
                    moonMesh.rotation.y += 0.01 * timeSpeed;
                }

                if (earthCloudsMesh && showClouds) {
                    earthCloudsMesh.rotation.y += 0.005;
                }
            }

            // Camera lerping to active target
            if (targetObject) {
                const worldPos = new THREE.Vector3();
                targetObject.mesh.getWorldPosition(worldPos);

                controls.target.lerp(worldPos, 0.06);

                const offsetDist = targetObject.data.radius * 4.2 + 18;
                const desiredCamPos = new THREE.Vector3()
                    .copy(worldPos)
                    .add(new THREE.Vector3(0, offsetDist * 0.4, offsetDist));

                camera.position.lerp(desiredCamPos, 0.04);
            }

            controls.update();
            renderer.render(scene, camera);
        }

        window.onload = function() {
            initScene();
            setupEventListeners();
            animate();
        };
    </script>
</body>
</html>
