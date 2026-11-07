<html lang="en" class="dark">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>Smart Classroom Layout Generator & PAK-21 Visualizer</title>
    <!-- Tailwind CSS CDN -->
    <script src="https://cdn.tailwindcss.com"></script>
    <script>
        tailwind.config = {
            darkMode: 'class',
            theme: {
                extend: {
                    colors: {
                        brand: {
                            50: '#f0fdf4',
                            100: '#dcfce7',
                            500: '#22c55e',
                            600: '#16a34a',
                            700: '#15803d',
                            900: '#14532d',
                        },
                        darkbg: '#0b0f19',
                        darkcard: '#151d30',
                        panelbg: '#1e293b'
                    }
                }
            }
        }
    </script>
    <!-- Lucide Icons & FontAwesome -->
    <script src="https://unpkg.com/lucide@latest"></script>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <!-- Three.js & OrbitControls -->
    <script src="https://cdnjs.cloudflare.com/ajax/libs/three.js/r128/three.min.js"></script>
    <script src="https://cdn.jsdelivr.net/npm/three@0.128.0/examples/js/controls/OrbitControls.js"></script>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Inter:wght@300;400;500;600;700&display=swap');
        
        html, body {
            width: 100%;
            height: 100%;
            margin: 0;
            padding: 0;
            overflow: hidden;
            font-family: 'Inter', sans-serif;
            user-select: none;
            -webkit-user-select: none;
        }

        /* Prevent layout shift on GitHub Pages */
        .viewport-container {
            height: 100vh;
            height: 100dvh;
        }

        /* Custom Scrollbar Styling */
        ::-webkit-scrollbar {
            width: 5px;
            height: 5px;
        }
        ::-webkit-scrollbar-track {
            background: rgba(15, 23, 42, 0.6);
        }
        ::-webkit-scrollbar-thumb {
            background: rgba(100, 116, 139, 0.5);
            border-radius: 4px;
        }
        ::-webkit-scrollbar-thumb:hover {
            background: rgba(148, 163, 184, 0.8);
        }

        .glass-panel {
            background: rgba(21, 29, 48, 0.88);
            backdrop-filter: blur(16px);
            -webkit-backdrop-filter: blur(16px);
            border: 1px solid rgba(255, 255, 255, 0.08);
        }

        /* Printable PDF Styling */
        @media print {
            body * {
                visibility: hidden;
            }
            #modalAiReport, #modalAiReport * {
                visibility: visible;
            }
            #modalAiReport {
                position: absolute;
                left: 0;
                top: 0;
                width: 100%;
                height: auto;
                background: white !important;
                color: black !important;
            }
            .no-print {
                display: none !important;
            }
        }
    </style>
</head>
<body class="bg-darkbg text-slate-100 viewport-container flex flex-col transition-colors duration-200 overflow-hidden">

    <!-- TOP HEADER / CONTROL BAR -->
    <header class="glass-panel sticky top-0 z-30 px-3 md:px-5 py-2.5 flex items-center justify-between gap-2 border-b border-slate-800 shrink-0">
        <!-- Logo & Branding -->
        <div class="flex items-center space-x-3">
            <div class="p-2 bg-gradient-to-tr from-emerald-600 to-teal-500 rounded-xl shadow-lg shadow-emerald-500/20 shrink-0">
                <i data-lucide="layout-grid" class="w-5 h-5 md:w-6 md:h-6 text-white"></i>
            </div>
            <div>
                <h1 class="font-bold text-sm md:text-base leading-tight text-white flex items-center gap-2">
                    PAK-21 Smart Classroom Layout
                    <span class="hidden sm:inline-block px-2 py-0.5 text-[10px] bg-emerald-500/20 text-emerald-400 border border-emerald-500/30 rounded-full font-mono">v3.0 Dual-Responsive</span>
                </h1>
                <p class="text-[10px] md:text-xs text-emerald-400 font-medium">Spatial Design, Pedagogy Metrics & AI Diagnostic Audit</p>
            </div>
        </div>

        <!-- Desktop Main Toolbar -->
        <div class="hidden lg:flex items-center gap-2.5 text-xs">
            <!-- Room Dimensions -->
            <div class="flex items-center space-x-2 bg-slate-800/90 px-3 py-1.5 rounded-xl border border-slate-700/70">
                <i data-lucide="ruler" class="w-4 h-4 text-emerald-400"></i>
                <span class="font-medium text-slate-300">Dim (m):</span>
                <input type="number" id="roomLength" value="10" min="5" max="25" class="w-12 bg-slate-900 text-center rounded py-0.5 border border-slate-700 text-xs focus:outline-none focus:border-emerald-500">
                <span class="text-slate-500">×</span>
                <input type="number" id="roomWidth" value="8" min="4" max="20" class="w-12 bg-slate-900 text-center rounded py-0.5 border border-slate-700 text-xs focus:outline-none focus:border-emerald-500">
            </div>

            <!-- Target Capacity -->
            <div class="flex items-center space-x-2 bg-slate-800/90 px-3 py-1.5 rounded-xl border border-slate-700/70">
                <i data-lucide="users" class="w-4 h-4 text-teal-400"></i>
                <span class="font-medium text-slate-300">Target Students:</span>
                <input type="number" id="targetStudents" value="30" min="5" max="60" class="w-12 bg-slate-900 text-center rounded py-0.5 border border-slate-700 text-xs focus:outline-none focus:border-emerald-500">
            </div>

            <!-- Undo / Redo Actions -->
            <div class="flex items-center gap-1 bg-slate-800/90 p-1 rounded-xl border border-slate-700/70">
                <button id="undoBtn" class="p-1.5 text-slate-300 hover:text-white hover:bg-slate-700 rounded-lg transition" title="Undo"><i data-lucide="undo" class="w-3.5 h-3.5"></i></button>
                <button id="redoBtn" class="p-1.5 text-slate-300 hover:text-white hover:bg-slate-700 rounded-lg transition" title="Redo"><i data-lucide="redo" class="w-3.5 h-3.5"></i></button>
            </div>

            <!-- Overlays & Toggles -->
            <div class="flex items-center gap-1 bg-slate-800/90 p-1 rounded-xl border border-slate-700/70">
                <button id="toggleSightlinesBtn" class="px-2.5 py-1 rounded-lg text-xs flex items-center gap-1.5 bg-emerald-600/30 text-emerald-300 border border-emerald-500/40 font-medium">
                    <i data-lucide="eye" class="w-3.5 h-3.5"></i> Sightlines
                </button>
                <button id="toggleFlowBtn" class="px-2.5 py-1 rounded-lg text-xs flex items-center gap-1.5 bg-teal-600/30 text-teal-300 border border-teal-500/40 font-medium">
                    <i data-lucide="footprints" class="w-3.5 h-3.5"></i> Flow Lines
                </button>
                <button id="toggleGridBtn" class="px-2.5 py-1 rounded-lg text-xs flex items-center gap-1.5 bg-indigo-600/30 text-indigo-300 border border-indigo-500/40 font-medium">
                    <i data-lucide="grid" class="w-3.5 h-3.5"></i> Snap Grid
                </button>
            </div>

            <!-- Primary Actions -->
            <div class="flex items-center gap-2">
                <button id="resetCanvasBtn" class="p-2 text-slate-400 hover:text-rose-400 hover:bg-slate-800 rounded-lg transition" title="Clear Canvas">
                    <i data-lucide="trash-2" class="w-4 h-4"></i>
                </button>
                <button id="exportImgBtn" class="p-2 text-slate-300 hover:text-white hover:bg-slate-800 rounded-lg transition" title="Export Image">
                    <i data-lucide="download" class="w-4 h-4"></i>
                </button>
                <button id="open3dBtn" class="px-3 py-1.5 bg-gradient-to-r from-blue-600 to-indigo-600 hover:from-blue-500 hover:to-indigo-500 text-white font-medium rounded-xl shadow-md flex items-center gap-1.5 transition">
                    <i data-lucide="box" class="w-4 h-4"></i> 3D Orbit
                </button>
                <button id="generateAiReportBtn" class="px-3.5 py-1.5 bg-gradient-to-r from-emerald-600 to-teal-600 hover:from-emerald-500 hover:to-teal-500 text-white font-semibold rounded-xl shadow-lg shadow-emerald-600/20 flex items-center gap-1.5 transition">
                    <i data-lucide="sparkles" class="w-4 h-4"></i> PAK-21 AI Audit
                </button>
            </div>
        </div>

        <!-- Mobile Header Actions -->
        <div class="flex lg:hidden items-center gap-2">
            <button id="mobileSettingsToggleBtn" class="p-2 text-slate-300 bg-slate-800/90 rounded-xl border border-slate-700">
                <i data-lucide="sliders-horizontal" class="w-4 h-4"></i>
            </button>
        </div>
    </header>

    <!-- MOBILE QUICK SETTINGS DRAWER -->
    <div id="mobileSettingsDrawer" class="hidden lg:hidden bg-slate-900 border-b border-slate-800 p-3 flex-col gap-3 text-xs z-20 shrink-0">
        <div class="flex justify-between items-center gap-2">
            <div class="flex items-center space-x-1.5">
                <i data-lucide="ruler" class="w-3.5 h-3.5 text-emerald-400"></i>
                <span class="text-slate-300">Dim (m):</span>
                <input type="number" id="roomLengthMob" value="10" min="5" max="25" class="w-10 bg-slate-800 text-center rounded py-0.5 border border-slate-700">
                <span class="text-slate-500">×</span>
                <input type="number" id="roomWidthMob" value="8" min="4" max="20" class="w-10 bg-slate-800 text-center rounded py-0.5 border border-slate-700">
            </div>
            <div class="flex items-center space-x-1.5">
                <i data-lucide="users" class="w-3.5 h-3.5 text-teal-400"></i>
                <span class="text-slate-300">Target:</span>
                <input type="number" id="targetStudentsMob" value="30" min="5" max="60" class="w-10 bg-slate-800 text-center rounded py-0.5 border border-slate-700">
            </div>
        </div>
        <div class="flex items-center justify-between gap-1 pt-2 border-t border-slate-800">
            <div class="flex gap-1">
                <button id="toggleSightlinesBtnMob" class="px-2 py-1 rounded-lg text-[10px] bg-emerald-600/30 text-emerald-300 border border-emerald-500/40">Sightlines</button>
                <button id="toggleFlowBtnMob" class="px-2 py-1 rounded-lg text-[10px] bg-teal-600/30 text-teal-300 border border-teal-500/40">Flow Lines</button>
            </div>
            <div class="flex items-center gap-1">
                <button id="undoBtnMob" class="p-1.5 text-slate-300 bg-slate-800 rounded-lg"><i data-lucide="undo" class="w-3.5 h-3.5"></i></button>
                <button id="redoBtnMob" class="p-1.5 text-slate-300 bg-slate-800 rounded-lg"><i data-lucide="redo" class="w-3.5 h-3.5"></i></button>
                <button id="exportImgBtnMob" class="p-1.5 text-slate-300 bg-slate-800 rounded-lg"><i data-lucide="download" class="w-3.5 h-3.5"></i></button>
                <button id="resetCanvasBtnMob" class="p-1.5 text-rose-400 bg-slate-800 rounded-lg"><i data-lucide="trash-2" class="w-3.5 h-3.5"></i></button>
            </div>
        </div>
    </div>

    <!-- MAIN APP CONTAINER -->
    <main class="flex-1 flex flex-col lg:flex-row overflow-hidden relative min-h-0 min-w-0">
        
        <!-- LEFT SIDEBAR: PRESETS & PALETTE (DESKTOP & MOBILE BOTTOM DRAWER) -->
        <aside id="leftSidebar" class="w-full lg:w-80 glass-panel border-r border-slate-800 flex flex-col h-full z-20 shrink-0 transition-all duration-300 hidden lg:flex min-h-0">
            <!-- Sidebar Navigation Tabs -->
            <div class="flex border-b border-slate-800 bg-slate-900/60 shrink-0">
                <button id="tabPresets" class="flex-1 py-3 text-xs font-semibold text-emerald-400 border-b-2 border-emerald-500 flex items-center justify-center gap-1.5">
                    <i data-lucide="layout-template" class="w-3.5 h-3.5"></i> PAK-21 Presets
                </button>
                <button id="tabPalette" class="flex-1 py-3 text-xs font-semibold text-slate-400 hover:text-slate-200 flex items-center justify-center gap-1.5">
                    <i data-lucide="armchair" class="w-3.5 h-3.5"></i> Furniture Palette
                </button>
            </div>

            <!-- Sidebar Scrollable Container -->
            <div class="flex-1 overflow-y-auto p-4 space-y-3 min-h-0">
                
                <!-- PRESETS PANEL -->
                <div id="panelPresets" class="space-y-3">
                    <p class="text-xs text-slate-400 leading-relaxed">Select a standard 21st Century Learning (PAK-21) layout model to auto-generate desk arrangements:</p>
                    
                    <button data-preset="kumpulan" class="preset-btn w-full p-3 rounded-xl bg-slate-800/80 hover:bg-emerald-950/40 border border-slate-700/80 hover:border-emerald-500/50 text-left transition group">
                        <div class="flex items-center justify-between mb-1">
                            <span class="font-semibold text-xs text-slate-200 group-hover:text-emerald-400 flex items-center gap-1.5">
                                <i data-lucide="users" class="w-4 h-4 text-emerald-400"></i> Kumpulan (Clusters)
                            </span>
                            <span class="text-[9px] px-2 py-0.5 bg-emerald-500/10 text-emerald-400 border border-emerald-500/20 rounded-full font-mono">High Collab</span>
                        </div>
                        <p class="text-[11px] text-slate-400">4-6 student desk clusters. Ideal for group work, problem-solving & discussion.</p>
                    </button>

                    <button data-preset="stesen" class="preset-btn w-full p-3 rounded-xl bg-slate-800/80 hover:bg-emerald-950/40 border border-slate-700/80 hover:border-emerald-500/50 text-left transition group">
                        <div class="flex items-center justify-between mb-1">
                            <span class="font-semibold text-xs text-slate-200 group-hover:text-emerald-400 flex items-center gap-1.5">
                                <i data-lucide="sparkles" class="w-4 h-4 text-cyan-400"></i> Stesen Pembelajaran
                            </span>
                            <span class="text-[9px] px-2 py-0.5 bg-cyan-500/10 text-cyan-400 border border-cyan-500/20 rounded-full font-mono">Stations</span>
                        </div>
                        <p class="text-[11px] text-slate-400">Learning stations, reading nook, & round project pods for active rotation.</p>
                    </button>

                    <button data-preset="horseshoe" class="preset-btn w-full p-3 rounded-xl bg-slate-800/80 hover:bg-emerald-950/40 border border-slate-700/80 hover:border-emerald-500/50 text-left transition group">
                        <div class="flex items-center justify-between mb-1">
                            <span class="font-semibold text-xs text-slate-200 group-hover:text-emerald-400 flex items-center gap-1.5">
                                <i data-lucide="u-turn-left" class="w-4 h-4 text-blue-400 rotate-90"></i> Horseshoe (U-Shape)
                            </span>
                            <span class="text-[9px] px-2 py-0.5 bg-blue-500/10 text-blue-400 border border-blue-500/20 rounded-full font-mono">Presentation</span>
                        </div>
                        <p class="text-[11px] text-slate-400">U-Shape layout. Maximizes presentation visibility and central interaction zone.</p>
                    </button>

                    <button data-preset="stadium" class="preset-btn w-full p-3 rounded-xl bg-slate-800/80 hover:bg-emerald-950/40 border border-slate-700/80 hover:border-emerald-500/50 text-left transition group">
                        <div class="flex items-center justify-between mb-1">
                            <span class="font-semibold text-xs text-slate-200 group-hover:text-emerald-400 flex items-center gap-1.5">
                                <i data-lucide="layers" class="w-4 h-4 text-purple-400"></i> Stadium / Angled
                            </span>
                            <span class="text-[9px] px-2 py-0.5 bg-purple-500/10 text-purple-400 border border-purple-500/20 rounded-full font-mono">Focused</span>
                        </div>
                        <p class="text-[11px] text-slate-400">Angled concentric rows facing center board. Balance of focus & teacher sightline.</p>
                    </button>

                    <button data-preset="traditional" class="preset-btn w-full p-3 rounded-xl bg-slate-800/80 hover:bg-emerald-950/40 border border-slate-700/80 hover:border-emerald-500/50 text-left transition group">
                        <div class="flex items-center justify-between mb-1">
                            <span class="font-semibold text-xs text-slate-200 group-hover:text-emerald-400 flex items-center gap-1.5">
                                <i data-lucide="rows" class="w-4 h-4 text-slate-400"></i> Traditional Rows
                            </span>
                            <span class="text-[9px] px-2 py-0.5 bg-slate-500/10 text-slate-400 border border-slate-500/20 rounded-full font-mono">Formal Exam</span>
                        </div>
                        <p class="text-[11px] text-slate-400">Standard linear seating grid. Best for formal individual examinations.</p>
                    </button>
                </div>

                <!-- PALETTE PANEL -->
                <div id="panelPalette" class="space-y-3 hidden">
                    <p class="text-xs text-slate-400 leading-relaxed">Click to add furniture elements onto the interactive canvas:</p>
                    
                    <div class="grid grid-cols-2 gap-2">
                        <button data-add="single-desk" class="add-item-btn p-2.5 bg-slate-800/80 hover:bg-slate-700 border border-slate-700 rounded-xl flex flex-col items-center justify-center text-center group transition">
                            <div class="w-7 h-7 rounded bg-emerald-500/20 border border-emerald-400 flex items-center justify-center mb-1 group-hover:scale-110 transition">
                                <i data-lucide="user" class="w-3.5 h-3.5 text-emerald-400"></i>
                            </div>
                            <span class="text-xs text-slate-300 font-medium">Single Desk</span>
                            <span class="text-[10px] text-slate-500 font-mono">0.7m × 0.6m</span>
                        </button>

                        <button data-add="double-desk" class="add-item-btn p-2.5 bg-slate-800/80 hover:bg-slate-700 border border-slate-700 rounded-xl flex flex-col items-center justify-center text-center group transition">
                            <div class="w-9 h-6 rounded bg-teal-500/20 border border-teal-400 flex items-center justify-center mb-1 group-hover:scale-110 transition">
                                <i data-lucide="users" class="w-3.5 h-3.5 text-teal-400"></i>
                            </div>
                            <span class="text-xs text-slate-300 font-medium">Double Desk</span>
                            <span class="text-[10px] text-slate-500 font-mono">1.3m × 0.6m</span>
                        </button>

                        <button data-add="trapezoid-desk" class="add-item-btn p-2.5 bg-slate-800/80 hover:bg-slate-700 border border-slate-700 rounded-xl flex flex-col items-center justify-center text-center group transition">
                            <div class="w-8 h-6 rounded bg-indigo-500/20 border border-indigo-400 flex items-center justify-center mb-1 group-hover:scale-110 transition">
                                <i data-lucide="shapes" class="w-3.5 h-3.5 text-indigo-400"></i>
                            </div>
                            <span class="text-xs text-slate-300 font-medium">Trapezoid Pod</span>
                            <span class="text-[10px] text-slate-500 font-mono">1.2m × 0.6m</span>
                        </button>

                        <button data-add="round-table" class="add-item-btn p-2.5 bg-slate-800/80 hover:bg-slate-700 border border-slate-700 rounded-xl flex flex-col items-center justify-center text-center group transition">
                            <div class="w-7 h-7 rounded-full bg-cyan-500/20 border border-cyan-400 flex items-center justify-center mb-1 group-hover:scale-110 transition">
                                <i data-lucide="disc" class="w-3.5 h-3.5 text-cyan-400"></i>
                            </div>
                            <span class="text-xs text-slate-300 font-medium">Round Table</span>
                            <span class="text-[10px] text-slate-500 font-mono">Ø 1.4m</span>
                        </button>

                        <button data-add="teacher-desk" class="add-item-btn p-2.5 bg-slate-800/80 hover:bg-slate-700 border border-slate-700 rounded-xl flex flex-col items-center justify-center text-center group transition">
                            <div class="w-9 h-6 rounded bg-amber-500/20 border border-amber-400 flex items-center justify-center mb-1 group-hover:scale-110 transition">
                                <i data-lucide="graduation-cap" class="w-3.5 h-3.5 text-amber-400"></i>
                            </div>
                            <span class="text-xs text-slate-300 font-medium">Teacher Station</span>
                            <span class="text-[10px] text-slate-500 font-mono">1.5m × 0.8m</span>
                        </button>

                        <button data-add="smartboard" class="add-item-btn p-2.5 bg-slate-800/80 hover:bg-slate-700 border border-slate-700 rounded-xl flex flex-col items-center justify-center text-center group transition">
                            <div class="w-10 h-3.5 rounded bg-indigo-500/20 border border-indigo-400 flex items-center justify-center mb-1 group-hover:scale-110 transition">
                                <i data-lucide="monitor" class="w-3 h-3 text-indigo-400"></i>
                            </div>
                            <span class="text-xs text-slate-300 font-medium">Smartboard</span>
                            <span class="text-[10px] text-slate-500 font-mono">2.2m × 0.25m</span>
                        </button>

                        <button data-add="bookshelf" class="add-item-btn p-2.5 bg-slate-800/80 hover:bg-slate-700 border border-slate-700 rounded-xl flex flex-col items-center justify-center text-center group transition">
                            <div class="w-9 h-4 rounded bg-purple-500/20 border border-purple-400 flex items-center justify-center mb-1 group-hover:scale-110 transition">
                                <i data-lucide="book-open" class="w-3 h-3 text-purple-400"></i>
                            </div>
                            <span class="text-xs text-slate-300 font-medium">Bookshelf</span>
                            <span class="text-[10px] text-slate-500 font-mono">1.2m × 0.4m</span>
                        </button>

                        <button data-add="presentation-mat" class="add-item-btn p-2.5 bg-slate-800/80 hover:bg-slate-700 border border-slate-700 rounded-xl flex flex-col items-center justify-center text-center group transition">
                            <div class="w-9 h-7 rounded bg-rose-500/20 border border-rose-400 flex items-center justify-center mb-1 group-hover:scale-110 transition">
                                <i data-lucide="presentation" class="w-3.5 h-3.5 text-rose-400"></i>
                            </div>
                            <span class="text-xs text-slate-300 font-medium">Reading Nook</span>
                            <span class="text-[10px] text-slate-500 font-mono">2.5m × 1.5m</span>
                        </button>
                    </div>
                </div>

            </div>
        </aside>

        <!-- CENTER WORKSPACE & CANVAS -->
        <section class="flex-1 bg-slate-950 relative flex flex-col justify-center items-center p-2 md:p-4 overflow-hidden min-h-0 min-w-0">
            <!-- Canvas HUD Overlay -->
            <div class="absolute top-3 left-3 md:top-5 md:left-5 z-10 glass-panel px-3 py-1.5 rounded-xl flex items-center gap-3 text-xs text-slate-300 shadow-xl pointer-events-none">
                <span class="flex items-center gap-1.5 font-medium">
                    <span class="w-2 h-2 rounded-full bg-emerald-400 animate-pulse"></span> Scale: 1m = <span id="scaleMeterVal">60</span>px
                </span>
                <span class="text-slate-600">|</span>
                <span id="canvasDimLabel" class="font-mono text-slate-400">10.0m x 8.0m</span>
            </div>

            <!-- Canvas Zoom Controls -->
            <div class="absolute top-3 right-3 md:top-5 md:right-5 z-10 flex gap-2">
                <button id="zoomInBtn" class="p-2 glass-panel hover:bg-slate-800 text-slate-300 rounded-xl shadow-lg"><i data-lucide="zoom-in" class="w-4 h-4"></i></button>
                <button id="zoomOutBtn" class="p-2 glass-panel hover:bg-slate-800 text-slate-300 rounded-xl shadow-lg"><i data-lucide="zoom-out" class="w-4 h-4"></i></button>
            </div>

            <!-- INTERACTIVE CANVAS CONTAINER -->
            <div id="canvasWrapper" class="relative shadow-2xl rounded-2xl overflow-hidden border border-slate-800 bg-slate-900 transition-all duration-300 max-w-full max-h-full flex items-center justify-center">
                <canvas id="classroomCanvas" class="block cursor-crosshair"></canvas>
            </div>
        </section>

        <!-- RIGHT SIDEBAR: INSPECTOR & LIVE METRICS (DESKTOP) -->
        <aside id="rightSidebar" class="w-full lg:w-80 glass-panel border-l border-slate-800 flex flex-col h-full z-20 shrink-0 hidden lg:flex min-h-0">
            <!-- Inspector / Selected Item Card -->
            <div class="p-4 border-b border-slate-800 space-y-3 shrink-0">
                <h3 class="text-xs font-bold text-emerald-400 uppercase tracking-wider flex items-center gap-1.5">
                    <i data-lucide="sliders" class="w-4 h-4"></i> Item Inspector
                </h3>
                
                <div id="inspectorPanel" class="space-y-3 hidden">
                    <div class="flex items-center justify-between">
                        <span id="selectedItemType" class="text-xs font-semibold text-slate-200">Single Desk</span>
                        <span class="text-[10px] font-mono bg-slate-800 px-2 py-0.5 rounded text-slate-400">Selected</span>
                    </div>

                    <!-- Rotation Slider -->
                    <div>
                        <div class="flex justify-between text-xs text-slate-300 mb-1">
                            <span>Rotation Angle</span>
                            <span id="rotateValDisplay" class="font-mono text-emerald-400">0°</span>
                        </div>
                        <input type="range" id="rotateSlider" min="0" max="345" step="15" value="0" class="w-full accent-emerald-500 bg-slate-900 rounded-lg cursor-pointer">
                    </div>

                    <!-- Layering & Custom Label -->
                    <div>
                        <label class="text-[11px] text-slate-400 block mb-1">Custom Label / Group #</label>
                        <input type="text" id="itemLabelInput" placeholder="e.g. Group 1 Desk" class="w-full bg-slate-900 border border-slate-700 text-xs px-2.5 py-1.5 rounded-lg text-slate-200 focus:outline-none focus:border-emerald-500">
                    </div>

                    <!-- Layer Actions -->
                    <div class="flex items-center gap-2">
                        <button id="bringFrontBtn" class="flex-1 py-1 bg-slate-800 hover:bg-slate-700 rounded text-[11px] text-slate-300">Front</button>
                        <button id="sendBackBtn" class="flex-1 py-1 bg-slate-800 hover:bg-slate-700 rounded text-[11px] text-slate-300">Back</button>
                    </div>

                    <!-- Action Controls -->
                    <div class="flex items-center justify-between gap-2 pt-1">
                        <button id="duplicateItemBtn" class="flex-1 py-1.5 bg-slate-800 hover:bg-slate-700 border border-slate-700 rounded-lg text-xs font-medium text-slate-200 flex items-center justify-center gap-1 transition">
                            <i data-lucide="copy" class="w-3.5 h-3.5"></i> Duplicate
                        </button>
                        <button id="deleteItemBtn" class="py-1.5 px-3 bg-rose-600/20 hover:bg-rose-600/40 text-rose-300 border border-rose-500/40 rounded-lg text-xs font-medium flex items-center justify-center gap-1 transition">
                            <i data-lucide="trash-2" class="w-3.5 h-3.5"></i> Delete
                        </button>
                    </div>
                </div>

                <div id="inspectorEmptyState" class="p-4 bg-slate-900/50 rounded-xl border border-slate-800 text-center">
                    <i data-lucide="mouse-pointer-click" class="w-6 h-6 text-slate-600 mx-auto mb-1"></i>
                    <p class="text-xs text-slate-500">Click any furniture item on the canvas to edit properties.</p>
                </div>
            </div>

            <!-- Live Analytics Panel -->
            <div class="flex-1 overflow-y-auto p-4 space-y-4 min-h-0">
                <h3 class="text-xs font-bold text-teal-400 uppercase tracking-wider flex items-center gap-1.5">
                    <i data-lucide="bar-chart-3" class="w-4 h-4"></i> Live PAK-21 Analytics
                </h3>

                <!-- Metric 1: Capacity -->
                <div class="p-3 bg-slate-900/80 rounded-xl border border-slate-800 space-y-1">
                    <div class="flex justify-between items-center text-xs">
                        <span class="text-slate-400 flex items-center gap-1"><i data-lucide="users" class="w-3.5 h-3.5 text-blue-400"></i> Capacity Utilization</span>
                        <span class="font-bold text-slate-200"><span id="metricCapacityCurrent">0</span> / <span id="metricCapacityTarget">30</span></span>
                    </div>
                    <div class="w-full bg-slate-800 rounded-full h-2 overflow-hidden mt-1">
                        <div id="metricCapacityBar" class="bg-blue-500 h-full rounded-full transition-all duration-300" style="width: 0%"></div>
                    </div>
                </div>

                <!-- Metric 2: Collaboration Index -->
                <div class="p-3 bg-slate-900/80 rounded-xl border border-slate-800 space-y-1">
                    <div class="flex justify-between items-center text-xs">
                        <span class="text-slate-400 flex items-center gap-1"><i data-lucide="share-2" class="w-3.5 h-3.5 text-emerald-400"></i> Collaboration Index</span>
                        <span id="metricCollabVal" class="font-bold text-emerald-400">0%</span>
                    </div>
                    <div class="w-full bg-slate-800 rounded-full h-2 overflow-hidden mt-1">
                        <div id="metricCollabBar" class="bg-emerald-500 h-full rounded-full transition-all duration-300" style="width: 0%"></div>
                    </div>
                </div>

                <!-- Metric 3: Sightline Visibility -->
                <div class="p-3 bg-slate-900/80 rounded-xl border border-slate-800 space-y-1">
                    <div class="flex justify-between items-center text-xs">
                        <span class="text-slate-400 flex items-center gap-1"><i data-lucide="eye" class="w-3.5 h-3.5 text-teal-400"></i> Teacher Sightline</span>
                        <span id="metricVisibilityVal" class="font-bold text-teal-400">95%</span>
                    </div>
                    <div class="w-full bg-slate-800 rounded-full h-2 overflow-hidden mt-1">
                        <div id="metricVisibilityBar" class="bg-teal-500 h-full rounded-full transition-all duration-300" style="width: 95%"></div>
                    </div>
                </div>

                <!-- Metric 4: Pathway Traffic Clearance -->
                <div class="p-3 bg-slate-900/80 rounded-xl border border-slate-800 space-y-1">
                    <div class="flex justify-between items-center text-xs">
                        <span class="text-slate-400 flex items-center gap-1"><i data-lucide="footprints" class="w-3.5 h-3.5 text-amber-400"></i> Pathway Clearance</span>
                        <span id="metricPathwayVal" class="font-bold text-amber-400">85%</span>
                    </div>
                    <div class="w-full bg-slate-800 rounded-full h-2 overflow-hidden mt-1">
                        <div id="metricPathwayBar" class="bg-amber-500 h-full rounded-full transition-all duration-300" style="width: 85%"></div>
                    </div>
                </div>
            </div>
        </aside>
    </main>

    <!-- MOBILE FLOATING QUICK ACTION BUTTONS -->
    <div class="lg:hidden fixed bottom-16 right-4 z-30 flex flex-col gap-2">
        <button id="mobileOpen3dBtn" class="p-3 bg-gradient-to-r from-blue-600 to-indigo-600 text-white rounded-full shadow-xl flex items-center justify-center">
            <i data-lucide="box" class="w-5 h-5"></i>
        </button>
        <button id="mobileGenerateAiReportBtn" class="p-3 bg-gradient-to-r from-emerald-600 to-teal-600 text-white rounded-full shadow-xl flex items-center justify-center">
            <i data-lucide="sparkles" class="w-5 h-5"></i>
        </button>
    </div>

    <!-- MOBILE BOTTOM NAVIGATION TAB BAR -->
    <nav class="lg:hidden fixed bottom-0 left-0 right-0 z-40 bg-slate-900/95 border-t border-slate-800 px-2 py-2 flex justify-around items-center glass-panel shrink-0">
        <button id="mobNavPresets" class="flex flex-col items-center text-emerald-400">
            <i data-lucide="layout-template" class="w-4 h-4"></i>
            <span class="text-[10px] mt-0.5">Presets</span>
        </button>
        <button id="mobNavPalette" class="flex flex-col items-center text-slate-400">
            <i data-lucide="armchair" class="w-4 h-4"></i>
            <span class="text-[10px] mt-0.5">Add Item</span>
        </button>
        <button id="mobNavInspector" class="flex flex-col items-center text-slate-400">
            <i data-lucide="sliders" class="w-4 h-4"></i>
            <span class="text-[10px] mt-0.5">Edit</span>
        </button>
        <button id="mobNavMetrics" class="flex flex-col items-center text-slate-400">
            <i data-lucide="bar-chart-3" class="w-4 h-4"></i>
            <span class="text-[10px] mt-0.5">Stats</span>
        </button>
    </nav>

    <!-- THREE.JS 3D SPATIAL ORBIT MODAL -->
    <div id="modal3d" class="fixed inset-0 z-50 bg-slate-950/90 backdrop-blur-md hidden flex flex-col items-center justify-center p-2 md:p-6">
        <div class="w-full max-w-6xl glass-panel rounded-2xl border border-slate-800 p-4 flex justify-between items-center z-10 shrink-0">
            <div class="flex items-center space-x-3">
                <div class="p-2 bg-indigo-500/20 text-indigo-400 rounded-xl"><i data-lucide="box" class="w-5 h-5"></i></div>
                <div>
                    <h2 class="text-sm md:text-base font-bold text-white">3D Spatial Room Simulation</h2>
                    <p class="text-[10px] md:text-xs text-indigo-300">Orbit controls enabled. Drag to rotate, pinch/scroll to zoom.</p>
                </div>
            </div>
            <button id="close3dBtn" class="p-2 bg-slate-800 hover:bg-rose-600/30 text-slate-300 hover:text-rose-400 border border-slate-700 rounded-xl transition">
                <i data-lucide="x" class="w-5 h-5"></i>
            </button>
        </div>

        <!-- 3D Canvas Viewport -->
        <div id="container3d" class="w-full max-w-6xl flex-1 my-3 rounded-2xl overflow-hidden border border-slate-800 shadow-2xl relative min-h-0">
            <div id="loading3d" class="absolute inset-0 flex items-center justify-center bg-slate-900 text-slate-300 font-medium text-xs">
                <i data-lucide="loader-2" class="w-6 h-6 animate-spin text-emerald-400 mr-2"></i> Rendering 3D Scene Mesh...
            </div>
        </div>
    </div>

    <!-- AI PAK-21 DIAGNOSTIC AUDIT REPORT MODAL -->
    <div id="modalAiReport" class="fixed inset-0 z-50 bg-slate-950/85 backdrop-blur-md hidden flex items-center justify-center p-3 md:p-6 overflow-y-auto">
        <div class="glass-panel w-full max-w-3xl rounded-2xl border border-slate-800 shadow-2xl overflow-hidden flex flex-col my-auto max-h-[90vh]">
            <!-- Modal Header -->
            <div class="bg-gradient-to-r from-emerald-900/40 via-teal-900/30 to-slate-900 p-5 border-b border-slate-800 flex items-center justify-between shrink-0">
                <div class="flex items-center space-x-3">
                    <div class="p-2.5 bg-emerald-500/20 border border-emerald-500/40 rounded-xl">
                        <i data-lucide="sparkles" class="w-6 h-6 text-emerald-400"></i>
                    </div>
                    <div>
                        <h2 class="text-base md:text-lg font-bold text-white">PAK-21 Pedagogical Audit & Diagnostic Report</h2>
                        <p class="text-xs text-emerald-300">Automated Spatial Efficiency & Active Learning Evaluation</p>
                    </div>
                </div>
                <button id="closeAiReportBtn" class="p-2 text-slate-400 hover:text-white hover:bg-slate-800 rounded-xl transition">
                    <i data-lucide="x" class="w-5 h-5"></i>
                </button>
            </div>

            <!-- Modal Dynamic Content -->
            <div id="reportContent" class="p-5 md:p-6 space-y-6 overflow-y-auto text-slate-200 text-xs md:text-sm min-h-0">
                <!-- Content injected via JavaScript -->
            </div>

            <!-- Modal Footer -->
            <div class="bg-slate-900/90 px-6 py-4 border-t border-slate-800 flex justify-between items-center no-print shrink-0">
                <span class="text-[11px] text-slate-500 hidden sm:inline">Based on Malaysian Ministry of Education PAK-21 Directives</span>
                <div class="flex space-x-3">
                    <button id="printReportBtn" class="px-4 py-2 bg-slate-800 hover:bg-slate-700 text-slate-200 font-medium text-xs rounded-xl flex items-center gap-2 border border-slate-700 transition">
                        <i data-lucide="printer" class="w-4 h-4"></i> Print Report
                    </button>
                    <button id="closeReportBtn" class="px-4 py-2 bg-emerald-600 hover:bg-emerald-500 text-white font-semibold text-xs rounded-xl shadow-lg shadow-emerald-600/20 transition">
                        Done
                    </button>
                </div>
            </div>
        </div>
    </div>

    <!-- APPLICATION CONTROLLER SCRIPT -->
    <script>
        /* ==========================================================================
           GLOBAL APP STATE & HISTORY ENGINE
           ========================================================================== */
        const state = {
            roomLengthMeters: 10,
            roomWidthMeters: 8,
            targetStudents: 30,
            scale: 60, // 1 Meter = 60 Physical Scale Base
            gridSnap: true,
            showSightlines: true,
            showFlow: true,
            items: [],
            selectedItemId: null,
            isDragging: false,
            dragOffset: { x: 0, y: 0 },
            zoomLevel: 1.0,
            history: [],
            historyIndex: -1
        };

        const ITEM_TYPES = {
            'single-desk': { width: 0.7, height: 0.6, color: '#3b82f6', label: 'Single Desk', capacity: 1, shape: 'rect' },
            'double-desk': { width: 1.3, height: 0.6, color: '#06b6d4', label: 'Double Desk', capacity: 2, shape: 'rect' },
            'trapezoid-desk': { width: 1.2, height: 0.6, color: '#6366f1', label: 'Trapezoid Pod', capacity: 2, shape: 'rect' },
            'round-table': { width: 1.4, height: 1.4, color: '#0ea5e9', label: 'Round Table', capacity: 4, shape: 'circle' },
            'teacher-desk': { width: 1.5, height: 0.8, color: '#f59e0b', label: 'Teacher Station', capacity: 0, shape: 'rect' },
            'smartboard': { width: 2.2, height: 0.25, color: '#4f46e5', label: 'Smartboard', capacity: 0, shape: 'rect' },
            'bookshelf': { width: 1.2, height: 0.4, color: '#a855f7', label: 'Bookshelf', capacity: 0, shape: 'rect' },
            'presentation-mat': { width: 2.5, height: 1.5, color: '#f43f5e', label: 'Reading Nook', capacity: 0, shape: 'rect' },
            'door': { width: 1.0, height: 0.2, color: '#64748b', label: 'Doorway', capacity: 0, shape: 'rect' }
        };

        let canvas, ctx;
        let threeRenderer, threeScene, threeCamera, threeControls;

        window.addEventListener('DOMContentLoaded', () => {
            initCanvas();
            bindEvents();
            loadPreset('kumpulan');
            lucide.createIcons();
        });

        function saveHistory() {
            if (state.historyIndex < state.history.length - 1) {
                state.history = state.history.slice(0, state.historyIndex + 1);
            }
            state.history.push(JSON.stringify(state.items));
            state.historyIndex++;
        }

        function undo() {
            if (state.historyIndex > 0) {
                state.historyIndex--;
                state.items = JSON.parse(state.history[state.historyIndex]);
                renderCanvas();
                updateMetrics();
            }
        }

        function redo() {
            if (state.historyIndex < state.history.length - 1) {
                state.historyIndex++;
                state.items = JSON.parse(state.history[state.historyIndex]);
                renderCanvas();
                updateMetrics();
            }
        }

        /* Pixel-perfect resolution dynamic scaling to prevent GitHub pages shift */
        function initCanvas() {
            canvas = document.getElementById('classroomCanvas');
            ctx = canvas.getContext('2d');
            updateCanvasSize();
            window.addEventListener('resize', updateCanvasSize);
        }

        function updateCanvasSize() {
            const lengthInput = document.getElementById('roomLength')?.value || 10;
            const widthInput = document.getElementById('roomWidth')?.value || 8;

            state.roomLengthMeters = parseFloat(lengthInput);
            state.roomWidthMeters = parseFloat(widthInput);

            const dpr = window.devicePixelRatio || 1;
            const cssWidth = state.roomLengthMeters * state.scale * state.zoomLevel;
            const cssHeight = state.roomWidthMeters * state.scale * state.zoomLevel;

            canvas.style.width = cssWidth + 'px';
            canvas.style.height = cssHeight + 'px';

            canvas.width = Math.floor(cssWidth * dpr);
            canvas.height = Math.floor(cssHeight * dpr);

            ctx.setTransform(1, 0, 0, 1, 0, 0);
            ctx.scale(dpr, dpr);

            document.getElementById('canvasDimLabel').innerText = `${state.roomLengthMeters.toFixed(1)}m x ${state.roomWidthMeters.toFixed(1)}m`;
            renderCanvas();
        }

        function bindEvents() {
            document.getElementById('roomLength')?.addEventListener('change', updateCanvasSize);
            document.getElementById('roomWidth')?.addEventListener('change', updateCanvasSize);
            document.getElementById('targetStudents')?.addEventListener('change', (e) => {
                state.targetStudents = parseInt(e.target.value) || 30;
                updateMetrics();
            });

            document.getElementById('undoBtn')?.addEventListener('click', undo);
            document.getElementById('redoBtn')?.addEventListener('click', redo);

            document.getElementById('toggleSightlinesBtn')?.addEventListener('click', () => {
                state.showSightlines = !state.showSightlines;
                renderCanvas();
            });
            document.getElementById('toggleFlowBtn')?.addEventListener('click', () => {
                state.showFlow = !state.showFlow;
                renderCanvas();
            });
            document.getElementById('toggleGridBtn')?.addEventListener('click', () => {
                state.gridSnap = !state.gridSnap;
            });

            document.getElementById('zoomInBtn')?.addEventListener('click', () => {
                state.zoomLevel = Math.min(1.8, state.zoomLevel + 0.1);
                updateCanvasSize();
            });
            document.getElementById('zoomOutBtn')?.addEventListener('click', () => {
                state.zoomLevel = Math.max(0.6, state.zoomLevel - 0.1);
                updateCanvasSize();
            });

            document.querySelectorAll('.preset-btn').forEach(btn => {
                btn.addEventListener('click', (e) => {
                    const preset = e.currentTarget.getAttribute('data-preset');
                    loadPreset(preset);
                });
            });

            document.querySelectorAll('.add-item-btn').forEach(btn => {
                btn.addEventListener('click', (e) => {
                    const itemType = e.currentTarget.getAttribute('data-add');
                    addItemToCanvas(itemType);
                });
            });

            canvas.addEventListener('mousedown', handleCanvasMouseDown);
            canvas.addEventListener('mousemove', handleCanvasMouseMove);
            window.addEventListener('mouseup', handleCanvasMouseUp);

            canvas.addEventListener('touchstart', handleTouchStart, { passive: false });
            canvas.addEventListener('touchmove', handleTouchMove, { passive: false });
            window.addEventListener('touchend', handleCanvasMouseUp);

            document.getElementById('rotateSlider')?.addEventListener('input', (e) => {
                if (state.selectedItemId === null) return;
                const item = state.items.find(i => i.id === state.selectedItemId);
                if (item) {
                    item.rotation = parseInt(e.target.value);
                    document.getElementById('rotateValDisplay').innerText = `${item.rotation}°`;
                    renderCanvas();
                }
            });

            document.getElementById('bringFrontBtn')?.addEventListener('click', () => {
                if (state.selectedItemId === null) return;
                const idx = state.items.findIndex(i => i.id === state.selectedItemId);
                if (idx > -1) {
                    const [item] = state.items.splice(idx, 1);
                    state.items.push(item);
                    renderCanvas();
                }
            });

            document.getElementById('sendBackBtn')?.addEventListener('click', () => {
                if (state.selectedItemId === null) return;
                const idx = state.items.findIndex(i => i.id === state.selectedItemId);
                if (idx > -1) {
                    const [item] = state.items.splice(idx, 1);
                    state.items.unshift(item);
                    renderCanvas();
                }
            });

            document.getElementById('itemLabelInput')?.addEventListener('input', (e) => {
                if (state.selectedItemId === null) return;
                const item = state.items.find(i => i.id === state.selectedItemId);
                if (item) {
                    item.customLabel = e.target.value;
                    renderCanvas();
                }
            });

            document.getElementById('duplicateItemBtn')?.addEventListener('click', duplicateSelectedItem);
            document.getElementById('deleteItemBtn')?.addEventListener('click', deleteSelectedItem);
            document.getElementById('resetCanvasBtn')?.addEventListener('click', () => {
                state.items = [];
                state.selectedItemId = null;
                hideInspector();
                renderCanvas();
                updateMetrics();
                saveHistory();
            });
            document.getElementById('exportImgBtn')?.addEventListener('click', exportCanvasImage);

            document.getElementById('open3dBtn')?.addEventListener('click', open3DModal);
            document.getElementById('close3dBtn')?.addEventListener('click', close3DModal);
            document.getElementById('mobileOpen3dBtn')?.addEventListener('click', open3DModal);

            document.getElementById('generateAiReportBtn')?.addEventListener('click', generateAiReport);
            document.getElementById('mobileGenerateAiReportBtn')?.addEventListener('click', generateAiReport);
            document.getElementById('closeAiReportBtn')?.addEventListener('click', () => document.getElementById('modalAiReport').classList.add('hidden'));
            document.getElementById('closeReportBtn')?.addEventListener('click', () => document.getElementById('modalAiReport').classList.add('hidden'));
            document.getElementById('printReportBtn')?.addEventListener('click', () => window.print());

            document.getElementById('tabPresets')?.addEventListener('click', () => switchSidebarTab('presets'));
            document.getElementById('tabPalette')?.addEventListener('click', () => switchSidebarTab('palette'));

            bindMobileNav();
        }

        function switchSidebarTab(tab) {
            const tabP = document.getElementById('tabPresets');
            const tabF = document.getElementById('tabPalette');
            const panelP = document.getElementById('panelPresets');
            const panelF = document.getElementById('panelPalette');

            panelP.classList.add('hidden');
            panelF.classList.add('hidden');

            if (tab === 'presets') {
                tabP.className = "flex-1 py-3 text-xs font-semibold text-emerald-400 border-b-2 border-emerald-500 flex items-center justify-center gap-1.5";
                tabF.className = "flex-1 py-3 text-xs font-semibold text-slate-400 hover:text-slate-200 flex items-center justify-center gap-1.5";
                panelP.classList.remove('hidden');
            } else {
                tabF.className = "flex-1 py-3 text-xs font-semibold text-emerald-400 border-b-2 border-emerald-500 flex items-center justify-center gap-1.5";
                tabP.className = "flex-1 py-3 text-xs font-semibold text-slate-400 hover:text-slate-200 flex items-center justify-center gap-1.5";
                panelF.classList.remove('hidden');
            }
        }

        function bindMobileNav() {
            const leftSidebar = document.getElementById('leftSidebar');
            const rightSidebar = document.getElementById('rightSidebar');
            const mobSettingsDrawer = document.getElementById('mobileSettingsDrawer');

            document.getElementById('mobileSettingsToggleBtn')?.addEventListener('click', () => {
                mobSettingsDrawer.classList.toggle('hidden');
                mobSettingsDrawer.classList.toggle('flex');
            });

            document.getElementById('mobNavPresets')?.addEventListener('click', () => {
                leftSidebar.classList.remove('hidden');
                rightSidebar.classList.add('hidden');
                switchSidebarTab('presets');
            });

            document.getElementById('mobNavPalette')?.addEventListener('click', () => {
                leftSidebar.classList.remove('hidden');
                rightSidebar.classList.add('hidden');
                switchSidebarTab('palette');
            });

            document.getElementById('mobNavInspector')?.addEventListener('click', () => {
                leftSidebar.classList.add('hidden');
                rightSidebar.classList.remove('hidden');
            });

            document.getElementById('mobNavMetrics')?.addEventListener('click', () => {
                leftSidebar.classList.add('hidden');
                rightSidebar.classList.remove('hidden');
            });
        }

        /* Presets loader */
        function loadPreset(presetName) {
            state.items = [];
            state.selectedItemId = null;
            hideInspector();

            const L = state.roomLengthMeters;
            const W = state.roomWidthMeters;

            state.items.push({ id: Date.now() + 1, type: 'smartboard', x: L / 2 - 1.1, y: 0.3, rotation: 0, customLabel: 'Front Smartboard' });
            state.items.push({ id: Date.now() + 2, type: 'teacher-desk', x: L / 2 - 0.75, y: 1.2, rotation: 0, customLabel: 'Teacher Desk' });
            state.items.push({ id: Date.now() + 3, type: 'door', x: 0.2, y: W - 0.5, rotation: 0, customLabel: 'Main Entrance' });
            state.items.push({ id: Date.now() + 4, type: 'bookshelf', x: L - 1.5, y: 0.3, rotation: 0, customLabel: 'Resource Shelf' });

            if (presetName === 'kumpulan') {
                const clusterPositions = [
                    { x: 1.8, y: 2.8 }, { x: 5.2, y: 2.8 }, { x: 7.8, y: 2.8 },
                    { x: 1.8, y: 5.4 }, { x: 5.2, y: 5.4 }, { x: 7.8, y: 5.4 }
                ];
                let groupNum = 1;
                clusterPositions.forEach(pos => {
                    if (pos.x + 1.5 < L && pos.y + 1.5 < W) {
                        state.items.push({ id: Date.now() + Math.random(), type: 'double-desk', x: pos.x, y: pos.y, rotation: 0, customLabel: `Group ${groupNum}` });
                        state.items.push({ id: Date.now() + Math.random(), type: 'double-desk', x: pos.x, y: pos.y + 0.7, rotation: 180, customLabel: `Group ${groupNum}` });
                        groupNum++;
                    }
                });
            } else if (presetName === 'stesen') {
                state.items.push({ id: Date.now() + Math.random(), type: 'round-table', x: 1.8, y: 3.0, rotation: 0, customLabel: 'Station 1' });
                state.items.push({ id: Date.now() + Math.random(), type: 'trapezoid-desk', x: L - 3.2, y: 3.0, rotation: 0, customLabel: 'Station 2' });
                state.items.push({ id: Date.now() + Math.random(), type: 'round-table', x: 1.8, y: 5.5, rotation: 0, customLabel: 'Station 3' });
                state.items.push({ id: Date.now() + Math.random(), type: 'presentation-mat', x: L / 2 - 1.25, y: 4.5, rotation: 0, customLabel: 'Reading Nook' });
            } else if (presetName === 'horseshoe') {
                state.items.push({ id: Date.now() + 5, type: 'presentation-mat', x: L / 2 - 1.25, y: 3.5, rotation: 0, customLabel: 'Stage Zone' });
                for (let i = 0; i < 4; i++) state.items.push({ id: Date.now() + Math.random(), type: 'double-desk', x: 1.5, y: 2.5 + i * 1.1, rotation: 90, customLabel: 'U-Wing L' });
                for (let i = 0; i < 4; i++) state.items.push({ id: Date.now() + Math.random(), type: 'double-desk', x: L - 2.0, y: 2.5 + i * 1.1, rotation: 270, customLabel: 'U-Wing R' });
                for (let i = 0; i < 3; i++) state.items.push({ id: Date.now() + Math.random(), type: 'double-desk', x: 2.8 + i * 1.5, y: W - 1.4, rotation: 0, customLabel: 'U-Base' });
            } else if (presetName === 'stadium') {
                for (let r = 0; r < 3; r++) {
                    const rowY = 2.8 + r * 1.4;
                    state.items.push({ id: Date.now() + Math.random(), type: 'double-desk', x: 1.5, y: rowY, rotation: 15, customLabel: `Row ${r+1}` });
                    state.items.push({ id: Date.now() + Math.random(), type: 'double-desk', x: L / 2 - 0.65, y: rowY, rotation: 0, customLabel: `Row ${r+1}` });
                    state.items.push({ id: Date.now() + Math.random(), type: 'double-desk', x: L - 2.8, y: rowY, rotation: 345, customLabel: `Row ${r+1}` });
                }
            } else if (presetName === 'traditional') {
                for (let r = 0; r < 4; r++) {
                    for (let c = 0; c < 4; c++) {
                        state.items.push({ id: Date.now() + Math.random(), type: 'double-desk', x: 1.5 + c * 1.8, y: 2.6 + r * 1.2, rotation: 0, customLabel: `Row ${r+1}` });
                    }
                }
            }

            renderCanvas();
            updateMetrics();
            saveHistory();
        }

        /* 2D Canvas rendering loop */
        function renderCanvas() {
            if (!ctx) return;
            const scale = state.scale * state.zoomLevel;

            ctx.fillStyle = '#0b0f19';
            ctx.fillRect(0, 0, state.roomLengthMeters * scale, state.roomWidthMeters * scale);

            ctx.strokeStyle = 'rgba(51, 65, 85, 0.4)';
            ctx.lineWidth = 1;

            for (let x = 0; x <= state.roomLengthMeters * scale; x += scale) {
                ctx.beginPath();
                ctx.moveTo(x, 0);
                ctx.lineTo(x, state.roomWidthMeters * scale);
                ctx.stroke();
            }
            for (let y = 0; y <= state.roomWidthMeters * scale; y += scale) {
                ctx.beginPath();
                ctx.moveTo(0, y);
                ctx.lineTo(state.roomLengthMeters * scale, y);
                ctx.stroke();
            }

            if (state.showFlow) drawPathwaysOverlay(scale);
            if (state.showSightlines) drawSightlinesOverlay(scale);

            state.items.forEach(item => drawFurnitureItem(item, scale));
        }

        function drawFurnitureItem(item, scale) {
            const def = ITEM_TYPES[item.type] || ITEM_TYPES['single-desk'];
            const widthPx = def.width * scale;
            const heightPx = def.height * scale;
            const xPx = item.x * scale;
            const yPx = item.y * scale;

            ctx.save();
            ctx.translate(xPx + widthPx / 2, yPx + heightPx / 2);
            ctx.rotate((item.rotation * Math.PI) / 180);

            const isSelected = (item.id === state.selectedItemId);

            if (isSelected) {
                ctx.shadowColor = '#22c55e';
                ctx.shadowBlur = 14;
            }

            ctx.fillStyle = item.type === 'presentation-mat' ? 'rgba(244, 63, 94, 0.18)' : def.color;
            ctx.strokeStyle = isSelected ? '#22c55e' : '#e2e8f0';
            ctx.lineWidth = isSelected ? 2.5 : 1.5;

            if (def.shape === 'circle') {
                ctx.beginPath();
                ctx.arc(0, 0, widthPx / 2, 0, Math.PI * 2);
                ctx.fill();
                ctx.stroke();
            } else {
                ctx.beginPath();
                ctx.roundRect(-widthPx / 2, -heightPx / 2, widthPx, heightPx, 6);
                ctx.fill();
                ctx.stroke();
            }

            ctx.shadowBlur = 0;
            ctx.fillStyle = '#ffffff';
            ctx.font = '600 10px Inter, sans-serif';
            ctx.textAlign = 'center';
            ctx.textBaseline = 'middle';
            ctx.fillText(item.customLabel || def.label, 0, 0);

            ctx.restore();
        }

        function drawSightlinesOverlay(scale) {
            const teacher = state.items.find(i => i.type === 'teacher-desk');
            if (!teacher) return;
            const tDef = ITEM_TYPES['teacher-desk'];
            const tx = (teacher.x + tDef.width / 2) * scale;
            const ty = (teacher.y + tDef.height / 2) * scale;

            ctx.save();
            ctx.fillStyle = 'rgba(20, 184, 166, 0.08)';
            ctx.strokeStyle = 'rgba(20, 184, 166, 0.35)';
            ctx.lineWidth = 1.5;

            ctx.beginPath();
            ctx.moveTo(tx, ty);
            ctx.arc(tx, ty, state.roomLengthMeters * scale * 0.9, Math.PI * 0.15, Math.PI * 0.85);
            ctx.closePath();
            ctx.fill();
            ctx.stroke();

            state.items.filter(i => ITEM_TYPES[i.type]?.capacity > 0).forEach(st => {
                const sDef = ITEM_TYPES[st.type];
                const sx = (st.x + sDef.width / 2) * scale;
                const sy = (st.y + sDef.height / 2) * scale;

                ctx.strokeStyle = 'rgba(20, 184, 166, 0.25)';
                ctx.setLineDash([4, 4]);
                ctx.beginPath();
                ctx.moveTo(tx, ty);
                ctx.lineTo(sx, sy);
                ctx.stroke();
                ctx.setLineDash([]);
            });

            ctx.restore();
        }

        function drawPathwaysOverlay(scale) {
            ctx.save();
            ctx.strokeStyle = 'rgba(251, 191, 36, 0.2)';
            ctx.lineWidth = 16;
            ctx.lineCap = 'round';
            const L = state.roomLengthMeters * scale;
            const W = state.roomWidthMeters * scale;

            ctx.beginPath();
            ctx.moveTo(L / 2, scale * 1.5);
            ctx.lineTo(L / 2, W - scale);
            ctx.moveTo(scale, W - scale);
            ctx.lineTo(L - scale, W - scale);
            ctx.stroke();
            ctx.restore();
        }

        function getItemAtCoords(x, y) {
            const scale = state.scale * state.zoomLevel;
            for (let i = state.items.length - 1; i >= 0; i--) {
                const item = state.items[i];
                const def = ITEM_TYPES[item.type] || ITEM_TYPES['single-desk'];
                const ix = item.x * scale;
                const iy = item.y * scale;
                const iw = def.width * scale;
                const ih = def.height * scale;

                if (x >= ix && x <= ix + iw && y >= iy && y <= iy + ih) return item;
            }
            return null;
        }

        function handleCanvasMouseDown(e) {
            const rect = canvas.getBoundingClientRect();
            const mouseX = e.clientX - rect.left;
            const mouseY = e.clientY - rect.top;

            const item = getItemAtCoords(mouseX, mouseY);
            if (item) {
                state.selectedItemId = item.id;
                state.isDragging = true;
                const scale = state.scale * state.zoomLevel;
                state.dragOffset = { x: mouseX - (item.x * scale), y: mouseY - (item.y * scale) };
                showInspector(item);
            } else {
                state.selectedItemId = null;
                hideInspector();
            }
            renderCanvas();
        }

        function handleCanvasMouseMove(e) {
            if (!state.isDragging || state.selectedItemId === null) return;
            const rect = canvas.getBoundingClientRect();
            const mouseX = e.clientX - rect.left;
            const mouseY = e.clientY - rect.top;
            const scale = state.scale * state.zoomLevel;

            let newX = (mouseX - state.dragOffset.x) / scale;
            let newY = (mouseY - state.dragOffset.y) / scale;

            if (state.gridSnap) {
                newX = Math.round(newX * 4) / 4;
                newY = Math.round(newY * 4) / 4;
            }

            const item = state.items.find(i => i.id === state.selectedItemId);
            if (item) {
                const def = ITEM_TYPES[item.type];
                item.x = Math.max(0, Math.min(state.roomLengthMeters - def.width, newX));
                item.y = Math.max(0, Math.min(state.roomWidthMeters - def.height, newY));
                renderCanvas();
                updateMetrics();
            }
        }

        function handleCanvasMouseUp() {
            if (state.isDragging) {
                state.isDragging = false;
                saveHistory();
            }
        }

        function handleTouchStart(e) {
            if (e.touches.length === 1) {
                const touch = e.touches[0];
                handleCanvasMouseDown({ clientX: touch.clientX, clientY: touch.clientY });
            }
        }

        function handleTouchMove(e) {
            if (e.touches.length === 1) {
                e.preventDefault();
                const touch = e.touches[0];
                handleCanvasMouseMove({ clientX: touch.clientX, clientY: touch.clientY });
            }
        }

        function addItemToCanvas(type) {
            const def = ITEM_TYPES[type];
            if (!def) return;

            const newItem = {
                id: Date.now(),
                type: type,
                x: state.roomLengthMeters / 2 - def.width / 2,
                y: state.roomWidthMeters / 2 - def.height / 2,
                rotation: 0,
                customLabel: def.label
            };

            state.items.push(newItem);
            state.selectedItemId = newItem.id;
            showInspector(newItem);
            renderCanvas();
            updateMetrics();
            saveHistory();
        }

        function duplicateSelectedItem() {
            if (state.selectedItemId === null) return;
            const current = state.items.find(i => i.id === state.selectedItemId);
            if (!current) return;

            const duplicate = {
                ...current,
                id: Date.now(),
                x: Math.min(state.roomLengthMeters - 1, current.x + 0.3),
                y: Math.min(state.roomWidthMeters - 1, current.y + 0.3)
            };

            state.items.push(duplicate);
            state.selectedItemId = duplicate.id;
            showInspector(duplicate);
            renderCanvas();
            updateMetrics();
            saveHistory();
        }

        function deleteSelectedItem() {
            if (state.selectedItemId === null) return;
            state.items = state.items.filter(i => i.id !== state.selectedItemId);
            state.selectedItemId = null;
            hideInspector();
            renderCanvas();
            updateMetrics();
            saveHistory();
        }

        function showInspector(item) {
            document.getElementById('inspectorPanel')?.classList.remove('hidden');
            document.getElementById('inspectorEmptyState')?.classList.add('hidden');
            document.getElementById('selectedItemType').innerText = ITEM_TYPES[item.type]?.label || item.type;
            document.getElementById('rotateSlider').value = item.rotation || 0;
            document.getElementById('rotateValDisplay').innerText = `${item.rotation || 0}°`;
            document.getElementById('itemLabelInput').value = item.customLabel || '';
        }

        function hideInspector() {
            document.getElementById('inspectorPanel')?.classList.add('hidden');
            document.getElementById('inspectorEmptyState')?.classList.remove('hidden');
        }

        function exportCanvasImage() {
            const link = document.createElement('a');
            link.download = `PAK21_Classroom_Layout_${Date.now()}.png`;
            link.href = canvas.toDataURL('image/png');
            link.click();
        }

        function updateMetrics() {
            let currentSeats = 0;
            let collabDesks = 0;
            let totalDesks = 0;

            state.items.forEach(item => {
                const cap = ITEM_TYPES[item.type]?.capacity || 0;
                currentSeats += cap;
                if (cap > 0) {
                    totalDesks++;
                    if (item.type === 'double-desk' || item.type === 'round-table' || item.type === 'trapezoid-desk') collabDesks++;
                }
            });

            const capBar = document.getElementById('metricCapacityBar');
            const capVal = document.getElementById('metricCapacityCurrent');
            const capTarget = document.getElementById('metricCapacityTarget');

            if (capVal) capVal.innerText = currentSeats;
            if (capTarget) capTarget.innerText = state.targetStudents;
            if (capBar) capBar.style.width = `${Math.min(100, (currentSeats / state.targetStudents) * 100)}%`;

            let collabPercent = totalDesks > 0 ? Math.round((collabDesks / totalDesks) * 100) : 0;
            document.getElementById('metricCollabVal').innerText = `${collabPercent}%`;
            document.getElementById('metricCollabBar').style.width = `${collabPercent}%`;

            const teacher = state.items.find(i => i.type === 'teacher-desk');
            let visibilityVal = teacher ? 95 : 40;
            document.getElementById('metricVisibilityVal').innerText = `${visibilityVal}%`;
            document.getElementById('metricVisibilityBar').style.width = `${visibilityVal}%`;

            let pathwayVal = Math.max(65, Math.min(98, 100 - (state.items.length * 1.5)));
            document.getElementById('metricPathwayVal').innerText = `${Math.round(pathwayVal)}%`;
            document.getElementById('metricPathwayBar').style.width = `${pathwayVal}%`;
        }

        /* 3D WebGL Spatial Orbit Modal */
        function open3DModal() {
            document.getElementById('modal3d').classList.remove('hidden');
            setTimeout(() => {
                init3DScene();
                document.getElementById('loading3d').classList.add('hidden');
            }, 100);
        }

        function close3DModal() {
            document.getElementById('modal3d').classList.add('hidden');
        }

        function init3DScene() {
            const container = document.getElementById('container3d');
            container.innerHTML = '';

            const width = container.clientWidth;
            const height = container.clientHeight;

            threeScene = new THREE.Scene();
            threeScene.background = new THREE.Color(0x0b0f19);

            threeCamera = new THREE.PerspectiveCamera(45, width / height, 0.1, 1000);
            threeCamera.position.set(state.roomLengthMeters / 2, 12, state.roomWidthMeters * 1.5);

            threeRenderer = new THREE.WebGLRenderer({ antialias: true });
            threeRenderer.setSize(width, height);
            threeRenderer.shadowMap.enabled = true;
            container.appendChild(threeRenderer.domElement);

            threeControls = new THREE.OrbitControls(threeCamera, threeRenderer.domElement);
            threeControls.target.set(state.roomLengthMeters / 2, 0, state.roomWidthMeters / 2);
            threeControls.update();

            const ambientLight = new THREE.AmbientLight(0xffffff, 0.6);
            threeScene.add(ambientLight);

            const dirLight = new THREE.DirectionalLight(0xffffff, 0.7);
            dirLight.position.set(10, 20, 15);
            dirLight.castShadow = true;
            threeScene.add(dirLight);

            const L = state.roomLengthMeters;
            const W = state.roomWidthMeters;

            const floorGeo = new THREE.PlaneGeometry(L, W);
            const floorMat = new THREE.MeshStandardMaterial({ color: 0x151d30, roughness: 0.8 });
            const floor = new THREE.Mesh(floorGeo, floorMat);
            floor.rotation.x = -Math.PI / 2;
            floor.position.set(L / 2, 0, W / 2);
            floor.receiveShadow = true;
            threeScene.add(floor);

            const gridHelper = new THREE.GridHelper(Math.max(L, W), Math.max(L, W), 0x3b82f6, 0x334155);
            gridHelper.position.set(L / 2, 0.01, W / 2);
            threeScene.add(gridHelper);

            state.items.forEach(item => {
                const def = ITEM_TYPES[item.type];
                if (!def) return;

                const colorHex = parseInt(def.color.replace('#', '0x'));
                const mat = new THREE.MeshStandardMaterial({ color: colorHex, roughness: 0.4 });

                let geo;
                let height3D = 0.7;
                if (item.type === 'smartboard') height3D = 1.2;
                if (item.type === 'presentation-mat') height3D = 0.02;

                if (def.shape === 'circle') {
                    geo = new THREE.CylinderGeometry(def.width / 2, def.width / 2, height3D, 16);
                } else {
                    geo = new THREE.BoxGeometry(def.width, height3D, def.height);
                }

                const mesh = new THREE.Mesh(geo, mat);
                mesh.castShadow = true;
                mesh.receiveShadow = true;

                mesh.position.set(item.x + def.width / 2, height3D / 2, item.y + def.height / 2);
                mesh.rotation.y = -(item.rotation * Math.PI) / 180;
                threeScene.add(mesh);
            });

            function animate3D() {
                if (document.getElementById('modal3d').classList.contains('hidden')) return;
                requestAnimationFrame(animate3D);
                threeControls.update();
                threeRenderer.render(threeScene, threeCamera);
            }
            animate3D();
        }

        /* Printable AI Diagnostic Report Modal */
        function generateAiReport() {
            const modal = document.getElementById('modalAiReport');
            const content = document.getElementById('reportContent');

            let seatCount = 0;
            state.items.forEach(i => seatCount += (ITEM_TYPES[i.type]?.capacity || 0));

            const hasSmartboard = state.items.some(i => i.type === 'smartboard');
            const hasStage = state.items.some(i => i.type === 'presentation-mat');
            const groupDesks = state.items.filter(i => i.type === 'double-desk' || i.type === 'round-table' || i.type === 'trapezoid-desk').length;

            let layoutTypeStr = groupDesks > 4 ? 'Flexible Collaborative Pods (Kumpulan)' : 'Linear Exam Seating';

            content.innerHTML = `
                <div class="grid grid-cols-3 gap-3 md:gap-4 mb-4">
                    <div class="p-3 bg-slate-900 rounded-xl border border-slate-800">
                        <span class="text-[10px] md:text-xs text-slate-400 block">Class Footprint</span>
                        <span class="text-sm md:text-base font-bold text-emerald-400 font-mono">${(state.roomLengthMeters * state.roomWidthMeters).toFixed(1)} m²</span>
                    </div>
                    <div class="p-3 bg-slate-900 rounded-xl border border-slate-800">
                        <span class="text-[10px] md:text-xs text-slate-400 block">Seats vs Target</span>
                        <span class="text-sm md:text-base font-bold text-teal-400 font-mono">${seatCount} / ${state.targetStudents}</span>
                    </div>
                    <div class="p-3 bg-slate-900 rounded-xl border border-slate-800">
                        <span class="text-[10px] md:text-xs text-slate-400 block">Identified Model</span>
                        <span class="text-xs md:text-sm font-bold text-indigo-400 truncate block">${layoutTypeStr}</span>
                    </div>
                </div>

                <div class="space-y-4">
                    <h3 class="font-bold text-slate-100 flex items-center gap-2 text-sm md:text-base">
                        <i data-lucide="check-circle-2" class="w-5 h-5 text-emerald-400"></i> PAK-21 Core Compliance Audit
                    </h3>

                    <div class="p-4 bg-slate-900/70 rounded-xl border border-slate-800 space-y-3">
                        <div class="flex items-start gap-3">
                            <div class="p-1.5 bg-emerald-500/20 text-emerald-400 rounded-lg mt-0.5"><i data-lucide="users" class="w-4 h-4"></i></div>
                            <div>
                                <h4 class="font-semibold text-xs text-slate-200">Collaborative Desk Configuration</h4>
                                <p class="text-xs text-slate-400 mt-0.5">Your layout contains ${groupDesks} active group pods. Supports PAK-21 Pembelajaran Kooperatif (Cooperative Learning) directives.</p>
                            </div>
                        </div>

                        <div class="flex items-start gap-3">
                            <div class="p-1.5 bg-blue-500/20 text-blue-400 rounded-lg mt-0.5"><i data-lucide="presentation" class="w-4 h-4"></i></div>
                            <div>
                                <h4 class="font-semibold text-xs text-slate-200">Student Presentation Zone</h4>
                                <p class="text-xs text-slate-400 mt-0.5">${hasStage ? 'Dedicated presentation space detected. Facilitates student showcase and gallery walks.' : 'Add a Stage Zone / Reading Nook element to encourage active presentation tasks.'}</p>
                            </div>
                        </div>

                        <div class="flex items-start gap-3">
                            <div class="p-1.5 bg-purple-500/20 text-purple-400 rounded-lg mt-0.5"><i data-lucide="eye" class="w-4 h-4"></i></div>
                            <div>
                                <h4 class="font-semibold text-xs text-slate-200">Visual Range & Sightlines</h4>
                                <p class="text-xs text-slate-400 mt-0.5">${hasSmartboard ? 'Smartboard position optimized for clear line-of-sight visual engagement.' : 'Position a primary Smartboard focal point along the main front wall.'}</p>
                            </div>
                        </div>
                    </div>

                    <h3 class="font-bold text-slate-100 flex items-center gap-2 text-sm md:text-base pt-2">
                        <i data-lucide="lightbulb" class="w-5 h-5 text-amber-400"></i> Actionable Pedagogical Recommendations
                    </h3>
                    <ul class="list-disc list-inside space-y-1 text-xs text-slate-300 bg-amber-950/20 p-4 rounded-xl border border-amber-500/20">
                        <li>Ensure perimeter clearance aisles are kept at a minimum of 1.2 meters to permit smooth rotation during activity stations.</li>
                        <li>Angle student desks no more than 45 degrees relative to the primary display board to avoid neck strain.</li>
                        <li>Keep resource shelves near classroom entry doorways for effortless distribution of learning materials.</li>
                    </ul>
                </div>
            `;

            lucide.createIcons();
            modal.classList.remove('hidden');
        }
    </script>
</body>
</html>
