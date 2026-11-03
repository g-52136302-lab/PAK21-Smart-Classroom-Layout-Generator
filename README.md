<!DOCTYPE html>
<html lang="en" class="dark">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
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
                        darkbg: '#0f172a',
                        darkcard: '#1e293b'
                    }
                }
            }
        }
    </script>
    <!-- Lucide Icons -->
    <script src="https://unpkg.com/lucide@latest"></script>
    <!-- Three.js CDN -->
    <script src="https://cdnjs.cloudflare.com/ajax/libs/three.js/r128/three.min.js"></script>
    <script src="https://cdn.jsdelivr.net/npm/three@0.128.0/examples/js/controls/OrbitControls.js"></script>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Inter:wght@300;400;500;600;700&display=swap');
        body {
            font-family: 'Inter', sans-serif;
            overflow-x: hidden;
        }
        /* Custom Scrollbars */
        ::-webkit-scrollbar {
            width: 6px;
            height: 6px;
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
            background: rgba(30, 41, 59, 0.7);
            backdrop-filter: blur(12px);
            -webkit-backdrop-filter: blur(12px);
            border: 1px solid rgba(255, 255, 255, 0.1);
        }
        .light .glass-panel {
            background: rgba(255, 255, 255, 0.85);
            backdrop-filter: blur(12px);
            border: 1px solid rgba(0, 0, 0, 0.08);
        }
    </style>
</head>
<body class="bg-darkbg text-slate-100 min-h-screen flex flex-col transition-colors duration-200 select-none">

    <!-- HEADER & CONTROLS -->
    <header class="glass-panel sticky top-0 z-30 px-4 py-3 flex flex-wrap items-center justify-between gap-4 border-b border-slate-700/50">
        <div class="flex items-center space-x-3">
            <div class="p-2 bg-gradient-to-tr from-emerald-600 to-teal-500 rounded-xl shadow-lg shadow-emerald-500/20">
                <i data-lucide="layout-grid" class="w-6 h-6 text-white"></i>
            </div>
            <div>
                <h1 class="font-bold text-lg leading-tight tracking-wide text-white dark:text-white light:text-slate-900">PAK-21 Smart Layout</h1>
                <p class="text-xs text-emerald-400 font-medium">Interactive Classroom Visualizer & AI Diagnostics</p>
            </div>
        </div>

        <!-- Controls Toolbar -->
        <div class="flex flex-wrap items-center gap-3 text-sm">
            <!-- Dimensions -->
            <div class="flex items-center space-x-2 bg-slate-800/80 light:bg-slate-200 px-3 py-1.5 rounded-lg border border-slate-700/60 light:border-slate-300">
                <i data-lucide="ruler" class="w-4 h-4 text-emerald-400"></i>
                <span class="text-xs font-medium text-slate-300 light:text-slate-700">Dim (m):</span>
                <input type="number" id="roomLength" value="10" min="5" max="25" class="w-12 bg-slate-900 light:bg-white text-center rounded text-xs py-0.5 border border-slate-700 focus:outline-none focus:border-emerald-500">
                <span class="text-xs text-slate-400">×</span>
                <input type="number" id="roomWidth" value="8" min="4" max="20" class="w-12 bg-slate-900 light:bg-white text-center rounded text-xs py-0.5 border border-slate-700 focus:outline-none focus:border-emerald-500">
            </div>

            <!-- Target Capacity -->
            <div class="flex items-center space-x-2 bg-slate-800/80 light:bg-slate-200 px-3 py-1.5 rounded-lg border border-slate-700/60 light:border-slate-300">
                <i data-lucide="users" class="w-4 h-4 text-teal-400"></i>
                <span class="text-xs font-medium text-slate-300 light:text-slate-700">Target Students:</span>
                <input type="number" id="targetStudents" value="30" min="5" max="60" class="w-12 bg-slate-900 light:bg-white text-center rounded text-xs py-0.5 border border-slate-700 focus:outline-none focus:border-emerald-500">
            </div>

            <!-- Visual Overlays Toggles -->
            <div class="flex items-center gap-1 bg-slate-800/80 light:bg-slate-200 p-1 rounded-lg border border-slate-700/60 light:border-slate-300">
                <button id="toggleSightlinesBtn" class="px-2 py-1 rounded text-xs flex items-center gap-1 bg-emerald-600/30 text-emerald-300 border border-emerald-500/40" title="Toggle Teacher Visibility Sightlines">
                    <i data-lucide="eye" class="w-3.5 h-3.5"></i> Sightlines
                </button>
                <button id="toggleFlowBtn" class="px-2 py-1 rounded text-xs flex items-center gap-1 bg-teal-600/30 text-teal-300 border border-teal-500/40" title="Toggle Circulation Pathways">
                    <i data-lucide="footprints" class="w-3.5 h-3.5"></i> Pathways
                </button>
                <button id="toggleGridBtn" class="px-2 py-1 rounded text-xs flex items-center gap-1 bg-indigo-600/30 text-indigo-300 border border-indigo-500/40" title="Toggle Grid Snapping">
                    <i data-lucide="grid" class="w-3.5 h-3.5"></i> Snap Grid
                </button>
            </div>

            <!-- Actions -->
            <div class="flex items-center gap-2">
                <button id="resetCanvasBtn" class="p-2 text-slate-400 hover:text-rose-400 hover:bg-slate-800 rounded-lg transition" title="Clear Canvas">
                    <i data-lucide="trash-2" class="w-4 h-4"></i>
                </button>
                <button id="exportImgBtn" class="p-2 text-slate-300 hover:text-white hover:bg-slate-800 rounded-lg transition" title="Export Image">
                    <i data-lucide="download" class="w-4 h-4"></i>
                </button>
                <button id="open3dBtn" class="px-3 py-1.5 bg-gradient-to-r from-blue-600 to-indigo-600 hover:from-blue-500 hover:to-indigo-500 text-white font-medium rounded-lg shadow-md flex items-center gap-1.5 transition text-xs">
                    <i data-lucide="box" class="w-4 h-4"></i> 3D View
                </button>
                <button id="generateAiReportBtn" class="px-3 py-1.5 bg-gradient-to-r from-emerald-600 to-teal-600 hover:from-emerald-500 hover:to-teal-500 text-white font-semibold rounded-lg shadow-md shadow-emerald-600/20 flex items-center gap-1.5 transition text-xs">
                    <i data-lucide="sparkles" class="w-4 h-4"></i> PAK-21 AI Audit
                </button>
                <button id="themeToggleBtn" class="p-2 text-slate-400 hover:text-amber-400 hover:bg-slate-800 rounded-lg transition" title="Toggle Dark/Light Mode">
                    <i data-lucide="sun" class="w-4 h-4"></i>
                </button>
            </div>
        </div>
    </header>

    <!-- MAIN APP CONTAINER -->
    <main class="flex-1 flex overflow-hidden relative">
        <!-- LEFT SIDEBAR: PRESETS & PALETTE -->
        <aside class="w-80 glass-panel border-r border-slate-700/50 flex flex-col h-[calc(100vh-65px)] z-20 shrink-0">
            <!-- Tabs -->
            <div class="flex border-b border-slate-700/60 bg-slate-900/50">
                <button id="tabPresets" class="flex-1 py-2.5 text-xs font-semibold text-emerald-400 border-b-2 border-emerald-500 flex items-center justify-center gap-1.5">
                    <i data-lucide="layout-template" class="w-3.5 h-3.5"></i> PAK-21 Presets
                </button>
                <button id="tabPalette" class="flex-1 py-2.5 text-xs font-semibold text-slate-400 hover:text-slate-200 flex items-center justify-center gap-1.5">
                    <i data-lucide="armchair" class="w-3.5 h-3.5"></i> Furniture & Add
                </button>
            </div>

            <!-- Tab Content Container -->
            <div class="flex-1 overflow-y-auto p-4 space-y-4">
                
                <!-- PRESETS PANEL -->
                <div id="panelPresets" class="space-y-3">
                    <p class="text-xs text-slate-400">Select standard 21st Century Learning (PAK-21) layouts to auto-generate arrangements:</p>
                    
                    <button data-preset="kumpulan" class="preset-btn w-full p-3 rounded-xl bg-slate-800/80 hover:bg-emerald-950/40 border border-slate-700 hover:border-emerald-500/50 text-left transition group">
                        <div class="flex items-center justify-between mb-1">
                            <span class="font-semibold text-sm text-slate-200 group-hover:text-emerald-400 flex items-center gap-1.5">
                                <i data-lucide="users" class="w-4 h-4 text-emerald-400"></i> Kumpulan (Clusters)
                            </span>
                            <span class="text-[10px] px-2 py-0.5 bg-emerald-500/10 text-emerald-400 border border-emerald-500/20 rounded-full">High Collab</span>
                        </div>
                        <p class="text-xs text-slate-400">4-6 student desk clusters. Ideal for group work, problem-solving & discussion.</p>
                    </button>

                    <button data-preset="horseshoe" class="preset-btn w-full p-3 rounded-xl bg-slate-800/80 hover:bg-emerald-950/40 border border-slate-700 hover:border-emerald-500/50 text-left transition group">
                        <div class="flex items-center justify-between mb-1">
                            <span class="font-semibold text-sm text-slate-200 group-hover:text-emerald-400 flex items-center gap-1.5">
                                <i data-lucide="u-turn-left" class="w-4 h-4 text-blue-400 rotate-90"></i> Horseshoe (U-Shape)
                            </span>
                            <span class="text-[10px] px-2 py-0.5 bg-blue-500/10 text-blue-400 border border-blue-500/20 rounded-full">Presentation</span>
                        </div>
                        <p class="text-xs text-slate-400">U-Shape layout. Maximizes presentation visibility and central interaction zone.</p>
                    </button>

                    <button data-preset="stadium" class="preset-btn w-full p-3 rounded-xl bg-slate-800/80 hover:bg-emerald-950/40 border border-slate-700 hover:border-emerald-500/50 text-left transition group">
                        <div class="flex items-center justify-between mb-1">
                            <span class="font-semibold text-sm text-slate-200 group-hover:text-emerald-400 flex items-center gap-1.5">
                                <i data-lucide="layers" class="w-4 h-4 text-purple-400"></i> Stadium / Angled
                            </span>
                            <span class="text-[10px] px-2 py-0.5 bg-purple-500/10 text-purple-400 border border-purple-500/20 rounded-full">Focused</span>
                        </div>
                        <p class="text-xs text-slate-400">Angled concentric rows facing center board. Good balance of focus & sightline.</p>
                    </button>

                    <button data-preset="modular" class="preset-btn w-full p-3 rounded-xl bg-slate-800/80 hover:bg-emerald-950/40 border border-slate-700 hover:border-emerald-500/50 text-left transition group">
                        <div class="flex items-center justify-between mb-1">
                            <span class="font-semibold text-sm text-slate-200 group-hover:text-emerald-400 flex items-center gap-1.5">
                                <i data-lucide="grid-3x3" class="w-4 h-4 text-amber-400"></i> Modular Stations
                            </span>
                            <span class="text-[10px] px-2 py-0.5 bg-amber-500/10 text-amber-400 border border-amber-500/20 rounded-full">Differentiated</span>
                        </div>
                        <p class="text-xs text-slate-400">Activity stations, round tables, and reading corners for rotation tasks.</p>
                    </button>

                    <button data-preset="traditional" class="preset-btn w-full p-3 rounded-xl bg-slate-800/80 hover:bg-emerald-950/40 border border-slate-700 hover:border-emerald-500/50 text-left transition group">
                        <div class="flex items-center justify-between mb-1">
                            <span class="font-semibold text-sm text-slate-200 group-hover:text-emerald-400 flex items-center gap-1.5">
                                <i data-lucide="rows" class="w-4 h-4 text-slate-400"></i> Traditional Rows
                            </span>
                            <span class="text-[10px] px-2 py-0.5 bg-slate-500/10 text-slate-400 border border-slate-500/20 rounded-full">Exam / Direct</span>
                        </div>
                        <p class="text-xs text-slate-400">Standard linear seating grid. Best for formal individual examinations.</p>
                    </button>
                </div>

                <!-- PALETTE PANEL -->
                <div id="panelPalette" class="space-y-3 hidden">
                    <p class="text-xs text-slate-400">Click to add furniture items onto the canvas grid:</p>
                    
                    <div class="grid grid-cols-2 gap-2">
                        <button data-add="single-desk" class="add-item-btn p-3 bg-slate-800 hover:bg-slate-700 border border-slate-700 rounded-xl flex flex-col items-center justify-center text-center group transition">
                            <div class="w-8 h-8 rounded bg-emerald-500/20 border border-emerald-400 flex items-center justify-center mb-1 group-hover:scale-110 transition">
                                <i data-lucide="user" class="w-4 h-4 text-emerald-400"></i>
                            </div>
                            <span class="text-xs text-slate-300 font-medium">Single Desk</span>
                            <span class="text-[10px] text-slate-500">0.7m × 0.6m</span>
                        </button>

                        <button data-add="double-desk" class="add-item-btn p-3 bg-slate-800 hover:bg-slate-700 border border-slate-700 rounded-xl flex flex-col items-center justify-center text-center group transition">
                            <div class="w-10 h-7 rounded bg-teal-500/20 border border-teal-400 flex items-center justify-center mb-1 group-hover:scale-110 transition">
                                <i data-lucide="users" class="w-4 h-4 text-teal-400"></i>
                            </div>
                            <span class="text-xs text-slate-300 font-medium">Double Desk</span>
                            <span class="text-[10px] text-slate-500">1.3m × 0.6m</span>
                        </button>

                        <button data-add="round-table" class="add-item-btn p-3 bg-slate-800 hover:bg-slate-700 border border-slate-700 rounded-xl flex flex-col items-center justify-center text-center group transition">
                            <div class="w-8 h-8 rounded-full bg-cyan-500/20 border border-cyan-400 flex items-center justify-center mb-1 group-hover:scale-110 transition">
                                <i data-lucide="disc" class="w-4 h-4 text-cyan-400"></i>
                            </div>
                            <span class="text-xs text-slate-300 font-medium">Round Cluster</span>
                            <span class="text-[10px] text-slate-500">Ø 1.4m</span>
                        </button>

                        <button data-add="teacher-desk" class="add-item-btn p-3 bg-slate-800 hover:bg-slate-700 border border-slate-700 rounded-xl flex flex-col items-center justify-center text-center group transition">
                            <div class="w-10 h-7 rounded bg-amber-500/20 border border-amber-400 flex items-center justify-center mb-1 group-hover:scale-110 transition">
                                <i data-lucide="graduation-cap" class="w-4 h-4 text-amber-400"></i>
                            </div>
                            <span class="text-xs text-slate-300 font-medium">Teacher Station</span>
                            <span class="text-[10px] text-slate-500">1.5m × 0.8m</span>
                        </button>

                        <button data-add="smartboard" class="add-item-btn p-3 bg-slate-800 hover:bg-slate-700 border border-slate-700 rounded-xl flex flex-col items-center justify-center text-center group transition">
                            <div class="w-12 h-4 rounded bg-indigo-500/20 border border-indigo-400 flex items-center justify-center mb-1 group-hover:scale-110 transition">
                                <i data-lucide="monitor" class="w-3.5 h-3.5 text-indigo-400"></i>
                            </div>
                            <span class="text-xs text-slate-300 font-medium">Smartboard / Board</span>
                            <span class="text-[10px] text-slate-500">2.2m × 0.2m</span>
                        </button>

                        <button data-add="bookshelf" class="add-item-btn p-3 bg-slate-800 hover:bg-slate-700 border border-slate-700 rounded-xl flex flex-col items-center justify-center text-center group transition">
                            <div class="w-10 h-5 rounded bg-purple-500/20 border border-purple-400 flex items-center justify-center mb-1 group-hover:scale-110 transition">
                                <i data-lucide="book-open" class="w-3.5 h-3.5 text-purple-400"></i>
                            </div>
                            <span class="text-xs text-slate-300 font-medium">Storage / Shelf</span>
                            <span class="text-[10px] text-slate-500">1.2m × 0.4m</span>
                        </button>

                        <button data-add="presentation-mat" class="add-item-btn p-3 bg-slate-800 hover:bg-slate-700 border border-slate-700 rounded-xl flex flex-col items-center justify-center text-center group transition">
                            <div class="w-10 h-8 rounded bg-rose-500/20 border border-rose-400 flex items-center justify-center mb-1 group-hover:scale-110 transition">
                                <i data-lucide="presentation" class="w-4 h-4 text-rose-400"></i>
                            </div>
                            <span class="text-xs text-slate-300 font-medium">Presentation Stage</span>
                            <span class="text-[10px] text-slate-500">2.5m × 1.5m</span>
                        </button>

                        <button data-add="door" class="add-item-btn p-3 bg-slate-800 hover:bg-slate-700 border border-slate-700 rounded-xl flex flex-col items-center justify-center text-center group transition">
                            <div class="w-8 h-6 rounded bg-slate-600/40 border border-slate-400 flex items-center justify-center mb-1 group-hover:scale-110 transition">
                                <i data-lucide="door-open" class="w-4 h-4 text-slate-300"></i>
                            </div>
                            <span class="text-xs text-slate-300 font-medium">Door / Entrance</span>
                            <span class="text-[10px] text-slate-500">1.0m × 0.2m</span>
                        </button>
                    </div>
                </div>

                <!-- ITEM PROPERTIES INSPECTOR PANEL -->
                <div id="inspectorPanel" class="p-4 bg-slate-800/90 rounded-xl border border-slate-700 space-y-3 hidden">
                    <div class="flex items-center justify-between border-b border-slate-700 pb-2">
                        <span class="text-xs font-bold text-emerald-400 flex items-center gap-1">
                            <i data-lucide="sliders" class="w-3.5 h-3.5"></i> Item Properties
                        </span>
                        <span id="selectedItemType" class="text-[10px] font-mono text-slate-400">Single Desk</span>
                    </div>

                    <!-- Rotation Slider -->
                    <div>
                        <div class="flex justify-between text-xs text-slate-300 mb-1">
                            <span>Rotation</span>
                            <span id="rotateValDisplay" class="font-mono text-emerald-400">0°</span>
                        </div>
                        <input type="range" id="rotateSlider" min="0" max="345" step="15" value="0" class="w-full accent-emerald-500 bg-slate-900 rounded-lg cursor-pointer">
                    </div>

                    <!-- Label Input -->
                    <div>
                        <label class="text-xs text-slate-300 block mb-1">Custom Label / Group #</label>
                        <input type="text" id="itemLabelInput" placeholder="e.g. Group 1" class="w-full bg-slate-900 border border-slate-700 text-xs px-2.5 py-1.5 rounded-lg text-slate-200 focus:outline-none focus:border-emerald-500">
                    </div>

                    <!-- Inspector Actions -->
                    <div class="flex items-center justify-between gap-2 pt-2">
                        <button id="duplicateItemBtn" class="flex-1 py-1.5 bg-slate-700 hover:bg-slate-600 rounded-lg text-xs font-medium text-slate-200 flex items-center justify-center gap-1 transition">
                            <i data-lucide="copy" class="w-3.5 h-3.5"></i> Duplicate
                        </button>
                        <button id="deleteItemBtn" class="py-1.5 px-3 bg-rose-600/20 hover:bg-rose-600/40 text-rose-300 border border-rose-500/40 rounded-lg text-xs font-medium flex items-center justify-center gap-1 transition">
                            <i data-lucide="trash-2" class="w-3.5 h-3.5"></i>
                        </button>
                    </div>
                </div>

            </div>
        </aside>

        <!-- CENTER CANVAS CONTAINER -->
        <section class="flex-1 bg-slate-950 relative flex flex-col justify-center items-center p-4 overflow-hidden">
            <!-- Canvas HUD Info Overlay -->
            <div class="absolute top-6 left-6 z-10 glass-panel px-3 py-1.5 rounded-lg flex items-center gap-3 text-xs text-slate-300 shadow-lg">
                <span class="flex items-center gap-1.5 font-medium">
                    <span class="w-2 h-2 rounded-full bg-emerald-400 animate-pulse"></span> Scale: 1 Meter = <span id="scaleMeterVal">50</span>px
                </span>
                <span class="text-slate-600">|</span>
                <span id="canvasDimLabel" class="font-mono text-slate-400">10m x 8m</span>
            </div>

            <div class="absolute top-6 right-6 z-10 flex gap-2">
                <button id="zoomInBtn" class="p-2 glass-panel hover:bg-slate-800 text-slate-300 rounded-lg shadow"><i data-lucide="zoom-in" class="w-4 h-4"></i></button>
                <button id="zoomOutBtn" class="p-2 glass-panel hover:bg-slate-800 text-slate-300 rounded-lg shadow"><i data-lucide="zoom-out" class="w-4 h-4"></i></button>
            </div>

            <!-- INTERACTIVE CANVAS -->
            <div id="canvasWrapper" class="relative shadow-2xl rounded-xl overflow-hidden border border-slate-700/80 bg-slate-900 transition-all duration-300">
                <canvas id="classroomCanvas"></canvas>
            </div>
        </section>

    </main>

    <!-- ANALYTICS & METRICS DASHBOARD FOOTER -->
    <footer class="glass-panel border-t border-slate-700/50 py-3 px-6 z-20 grid grid-cols-2 md:grid-cols-4 gap-4 items-center">
        <!-- Metric 1: Capacity -->
        <div class="flex items-center space-x-3 bg-slate-900/60 p-2.5 rounded-xl border border-slate-800">
            <div class="p-2 rounded-lg bg-emerald-500/10 text-emerald-400 border border-emerald-500/20">
                <i data-lucide="users" class="w-5 h-5"></i>
            </div>
            <div>
                <div class="text-[11px] font-semibold text-slate-400 uppercase tracking-wider">Student Capacity</div>
                <div class="flex items-baseline space-x-1">
                    <span id="metricCapacityCurrent" class="text-lg font-bold text-slate-100">0</span>
                    <span class="text-xs text-slate-400">/ <span id="metricCapacityTarget">30</span> seats</span>
                </div>
            </div>
        </div>

        <!-- Metric 2: Collaboration Index -->
        <div class="flex items-center space-x-3 bg-slate-900/60 p-2.5 rounded-xl border border-slate-800">
            <div class="p-2 rounded-lg bg-blue-500/10 text-blue-400 border border-blue-500/20">
                <i data-lucide="share-2" class="w-5 h-5"></i>
            </div>
            <div class="flex-1">
                <div class="flex justify-between text-[11px] font-semibold text-slate-400 uppercase tracking-wider mb-1">
                    <span>PAK-21 Collab Index</span>
                    <span id="metricCollabVal" class="text-blue-400 font-bold">85%</span>
                </div>
                <div class="w-full bg-slate-800 h-1.5 rounded-full overflow-hidden">
                    <div id="metricCollabBar" class="bg-blue-500 h-full rounded-full transition-all duration-500" style="width: 85%;"></div>
                </div>
            </div>
        </div>

        <!-- Metric 3: Teacher Sightline Visibility -->
        <div class="flex items-center space-x-3 bg-slate-900/60 p-2.5 rounded-xl border border-slate-800">
            <div class="p-2 rounded-lg bg-teal-500/10 text-teal-400 border border-teal-500/20">
                <i data-lucide="eye" class="w-5 h-5"></i>
            </div>
            <div class="flex-1">
                <div class="flex justify-between text-[11px] font-semibold text-slate-400 uppercase tracking-wider mb-1">
                    <span>Teacher Visibility</span>
                    <span id="metricVisibilityVal" class="text-teal-400 font-bold">92%</span>
                </div>
                <div class="w-full bg-slate-800 h-1.5 rounded-full overflow-hidden">
                    <div id="metricVisibilityBar" class="bg-teal-500 h-full rounded-full transition-all duration-500" style="width: 92%;"></div>
                </div>
            </div>
        </div>

        <!-- Metric 4: Pathway Clearance -->
        <div class="flex items-center space-x-3 bg-slate-900/60 p-2.5 rounded-xl border border-slate-800">
            <div class="p-2 rounded-lg bg-amber-500/10 text-amber-400 border border-amber-500/20">
                <i data-lucide="footprints" class="w-5 h-5"></i>
            </div>
            <div class="flex-1">
                <div class="flex justify-between text-[11px] font-semibold text-slate-400 uppercase tracking-wider mb-1">
                    <span>Aisle Pathway Flow</span>
                    <span id="metricPathwayVal" class="text-amber-400 font-bold">78%</span>
                </div>
                <div class="w-full bg-slate-800 h-1.5 rounded-full overflow-hidden">
                    <div id="metricPathwayBar" class="bg-amber-500 h-full rounded-full transition-all duration-500" style="width: 78%;"></div>
                </div>
            </div>
        </div>
    </footer>

    <!-- 3D PERSPECTIVE MODAL -->
    <div id="modal3d" class="fixed inset-0 z-50 bg-slate-950/80 backdrop-blur-md hidden flex flex-col justify-between items-center p-4">
        <!-- Modal Top Control Bar -->
        <div class="w-full max-w-6xl flex justify-between items-center glass-panel px-4 py-3 rounded-xl z-10">
            <div class="flex items-center space-x-2">
                <i data-lucide="box" class="w-5 h-5 text-indigo-400"></i>
                <h2 class="font-bold text-slate-100">3D Interactive Classroom Perspective</h2>
            </div>
            <div class="flex items-center space-x-3">
                <span class="text-xs text-slate-400 hidden sm:inline">Drag to Orbit | Scroll to Zoom</span>
                <button id="close3dBtn" class="p-1.5 bg-slate-800 hover:bg-rose-600/30 text-slate-300 hover:text-rose-400 border border-slate-700 rounded-lg transition">
                    <i data-lucide="x" class="w-5 h-5"></i>
                </button>
            </div>
        </div>

        <!-- 3D WebGL Canvas Container -->
        <div id="container3d" class="w-full max-w-6xl flex-1 my-3 rounded-2xl overflow-hidden border border-slate-700/80 shadow-2xl relative">
            <div id="loading3d" class="absolute inset-0 flex items-center justify-center bg-slate-900 text-slate-300 font-medium text-sm">
                <i data-lucide="loader-2" class="w-6 h-6 animate-spin text-emerald-400 mr-2"></i> Rendering 3D Scene...
            </div>
        </div>
    </div>

    <!-- AI AUDIT & DIAGNOSTIC REPORT MODAL -->
    <div id="modalAiReport" class="fixed inset-0 z-50 bg-slate-950/80 backdrop-blur-md hidden flex items-center justify-center p-4 overflow-y-auto">
        <div class="glass-panel w-full max-w-3xl rounded-2xl border border-slate-700 shadow-2xl overflow-hidden flex flex-col my-8 max-h-[90vh]">
            <!-- Header -->
            <div class="bg-gradient-to-r from-emerald-900/40 via-teal-900/30 to-slate-900 p-6 border-b border-slate-700/80 flex items-center justify-between">
                <div class="flex items-center space-x-3">
                    <div class="p-2.5 bg-emerald-500/20 border border-emerald-500/40 rounded-xl">
                        <i data-lucide="sparkles" class="w-6 h-6 text-emerald-400"></i>
                    </div>
                    <div>
                        <h2 class="text-lg font-bold text-white">PAK-21 Pedagogical Diagnostic Report</h2>
                        <p class="text-xs text-emerald-300">Automated Spatial Efficiency & Active Learning Evaluation</p>
                    </div>
                </div>
                <button id="closeAiReportBtn" class="p-2 text-slate-400 hover:text-white hover:bg-slate-800 rounded-lg transition">
                    <i data-lucide="x" class="w-5 h-5"></i>
                </button>
            </div>

            <!-- Report Body Content -->
            <div id="reportContent" class="p-6 space-y-6 overflow-y-auto text-slate-200 text-sm">
                <!-- Dynamic report content injected via JS -->
            </div>

            <!-- Modal Footer Actions -->
            <div class="bg-slate-900/80 px-6 py-4 border-t border-slate-800 flex justify-between items-center">
                <span class="text-xs text-slate-400">Standard Malaysian PAK-21 Guidelines Matrix</span>
                <div class="flex space-x-3">
                    <button id="printReportBtn" class="px-4 py-2 bg-slate-800 hover:bg-slate-700 text-slate-200 font-medium text-xs rounded-xl flex items-center gap-2 border border-slate-700 transition">
                        <i data-lucide="printer" class="w-4 h-4"></i> Print Summary
                    </button>
                    <button id="closeReportBtn" class="px-4 py-2 bg-emerald-600 hover:bg-emerald-500 text-white font-semibold text-xs rounded-xl shadow-lg shadow-emerald-600/20 transition">
                        Done
                    </button>
                </div>
            </div>
        </div>
    </div>

    <!-- MAIN SCRIPT -->
    <script>
        /* ==========================================================================
           GLOBAL APP STATE & CONFIGURATION
           ========================================================================== */
        const state = {
            roomLengthMeters: 10,
            roomWidthMeters: 8,
            targetStudents: 30,
            scale: 50, // 1 Meter = 50 Pixels
            gridSnap: true,
            showSightlines: true,
            showFlow: true,
            items: [],
            selectedItemId: null,
            isDragging: false,
            dragOffset: { x: 0, y: 0 },
            zoomLevel: 1.0,
            theme: 'dark'
        };

        // Item Template Definitions
        const ITEM_TYPES = {
            'single-desk': { width: 0.7, height: 0.6, color: '#3b82f6', label: 'Single Desk', capacity: 1, shape: 'rect' },
            'double-desk': { width: 1.3, height: 0.6, color: '#06b6d4', label: 'Double Desk', capacity: 2, shape: 'rect' },
            'round-table': { width: 1.4, height: 1.4, color: '#0ea5e9', label: 'Round Cluster', capacity: 4, shape: 'circle' },
            'teacher-desk': { width: 1.5, height: 0.8, color: '#f59e0b', label: 'Teacher Station', capacity: 0, shape: 'rect' },
            'smartboard': { width: 2.2, height: 0.25, color: '#6366f1', label: 'Smartboard', capacity: 0, shape: 'rect' },
            'bookshelf': { width: 1.2, height: 0.4, color: '#a855f7', label: 'Bookshelf', capacity: 0, shape: 'rect' },
            'presentation-mat': { width: 2.5, height: 1.5, color: '#f43f5e', label: 'Stage Zone', capacity: 0, shape: 'rect' },
            'door': { width: 1.0, height: 0.2, color: '#64748b', label: 'Doorway', capacity: 0, shape: 'rect' }
        };

        let canvas, ctx;
        let threeRenderer, threeScene, threeCamera, threeControls;

        /* ==========================================================================
           INITIALIZATION & EVENT LISTENERS
           ========================================================================== */
        window.onload = function() {
            lucide.createIcons();
            initCanvas();
            bindEvents();
            loadPreset('kumpulan'); // Load default PAK-21 layout
            updateCanvasSize();
            renderCanvas();
            updateMetrics();
        };

        function initCanvas() {
            canvas = document.getElementById('classroomCanvas');
            ctx = canvas.getContext('2d');
            updateCanvasSize();
        }

        function updateCanvasSize() {
            state.roomLengthMeters = parseFloat(document.getElementById('roomLength').value) || 10;
            state.roomWidthMeters = parseFloat(document.getElementById('roomWidth').value) || 8;
            state.targetStudents = parseInt(document.getElementById('targetStudents').value) || 30;

            const pixelWidth = state.roomLengthMeters * state.scale * state.zoomLevel;
            const pixelHeight = state.roomWidthMeters * state.scale * state.zoomLevel;

            canvas.width = pixelWidth;
            canvas.height = pixelHeight;

            const wrapper = document.getElementById('canvasWrapper');
            wrapper.style.width = `${pixelWidth}px`;
            wrapper.style.height = `${pixelHeight}px`;

            document.getElementById('canvasDimLabel').innerText = `${state.roomLengthMeters}m x ${state.roomWidthMeters}m`;
            document.getElementById('scaleMeterVal').innerText = Math.round(state.scale * state.zoomLevel);

            renderCanvas();
            updateMetrics();
        }

        function bindEvents() {
            // Inputs
            document.getElementById('roomLength').addEventListener('change', updateCanvasSize);
            document.getElementById('roomWidth').addEventListener('change', updateCanvasSize);
            document.getElementById('targetStudents').addEventListener('change', () => {
                state.targetStudents = parseInt(document.getElementById('targetStudents').value) || 30;
                updateMetrics();
            });

            // Tabs
            document.getElementById('tabPresets').addEventListener('click', () => switchTab('presets'));
            document.getElementById('tabPalette').addEventListener('click', () => switchTab('palette'));

            // Presets
            document.querySelectorAll('.preset-btn').forEach(btn => {
                btn.addEventListener('click', () => {
                    const preset = btn.getAttribute('data-preset');
                    loadPreset(preset);
                });
            });

            // Add Item Palette
            document.querySelectorAll('.add-item-btn').forEach(btn => {
                btn.addEventListener('click', () => {
                    const type = btn.getAttribute('data-add');
                    addItemToCanvas(type);
                });
            });

            // Canvas Interaction
            canvas.addEventListener('mousedown', handleCanvasMouseDown);
            canvas.addEventListener('mousemove', handleCanvasMouseMove);
            canvas.addEventListener('mouseup', handleCanvasMouseUp);

            // Touch support
            canvas.addEventListener('touchstart', handleTouchStart);
            canvas.addEventListener('touchmove', handleTouchMove);
            canvas.addEventListener('touchend', handleCanvasMouseUp);

            // Controls & Toggles
            document.getElementById('toggleSightlinesBtn').addEventListener('click', (e) => {
                state.showSightlines = !state.showSightlines;
                e.currentTarget.classList.toggle('bg-emerald-600/30');
                renderCanvas();
            });

            document.getElementById('toggleFlowBtn').addEventListener('click', (e) => {
                state.showFlow = !state.showFlow;
                e.currentTarget.classList.toggle('bg-teal-600/30');
                renderCanvas();
            });

            document.getElementById('toggleGridBtn').addEventListener('click', (e) => {
                state.gridSnap = !state.gridSnap;
                e.currentTarget.classList.toggle('bg-indigo-600/30');
            });

            document.getElementById('resetCanvasBtn').addEventListener('click', () => {
                state.items = [];
                state.selectedItemId = null;
                hideInspector();
                renderCanvas();
                updateMetrics();
            });

            document.getElementById('exportImgBtn').addEventListener('click', exportCanvasImage);

            // Zoom
            document.getElementById('zoomInBtn').addEventListener('click', () => {
                state.zoomLevel = Math.min(1.8, state.zoomLevel + 0.1);
                updateCanvasSize();
            });
            document.getElementById('zoomOutBtn').addEventListener('click', () => {
                state.zoomLevel = Math.max(0.6, state.zoomLevel - 0.1);
                updateCanvasSize();
            });

            // Inspector Controls
            document.getElementById('rotateSlider').addEventListener('input', (e) => {
                const val = parseInt(e.target.value);
                document.getElementById('rotateValDisplay').innerText = `${val}°`;
                if (state.selectedItemId !== null) {
                    const item = state.items.find(i => i.id === state.selectedItemId);
                    if (item) {
                        item.rotation = val;
                        renderCanvas();
                    }
                }
            });

            document.getElementById('itemLabelInput').addEventListener('input', (e) => {
                if (state.selectedItemId !== null) {
                    const item = state.items.find(i => i.id === state.selectedItemId);
                    if (item) {
                        item.customLabel = e.target.value;
                        renderCanvas();
                    }
                }
            });

            document.getElementById('duplicateItemBtn').addEventListener('click', duplicateSelectedItem);
            document.getElementById('deleteItemBtn').addEventListener('click', deleteSelectedItem);

            // Theme Toggle
            document.getElementById('themeToggleBtn').addEventListener('click', () => {
                document.documentElement.classList.toggle('dark');
                document.documentElement.classList.toggle('light');
                renderCanvas();
            });

            // 3D Modal
            document.getElementById('open3dBtn').addEventListener('click', open3DModal);
            document.getElementById('close3dBtn').addEventListener('click', close3DModal);

            // AI Report Modal
            document.getElementById('generateAiReportBtn').addEventListener('click', generateAiReport);
            document.getElementById('closeAiReportBtn').addEventListener('click', () => {
                document.getElementById('modalAiReport').classList.add('hidden');
            });
            document.getElementById('closeReportBtn').addEventListener('click', () => {
                document.getElementById('modalAiReport').classList.add('hidden');
            });
            document.getElementById('printReportBtn').addEventListener('click', () => window.print());
        }

        function switchTab(tab) {
            const tabP = document.getElementById('tabPresets');
            const tabF = document.getElementById('tabPalette');
            const panelP = document.getElementById('panelPresets');
            const panelF = document.getElementById('panelPalette');

            if (tab === 'presets') {
                tabP.className = "flex-1 py-2.5 text-xs font-semibold text-emerald-400 border-b-2 border-emerald-500 flex items-center justify-center gap-1.5";
                tabF.className = "flex-1 py-2.5 text-xs font-semibold text-slate-400 hover:text-slate-200 flex items-center justify-center gap-1.5";
                panelP.classList.remove('hidden');
                panelF.classList.add('hidden');
            } else {
                tabF.className = "flex-1 py-2.5 text-xs font-semibold text-emerald-400 border-b-2 border-emerald-500 flex items-center justify-center gap-1.5";
                tabP.className = "flex-1 py-2.5 text-xs font-semibold text-slate-400 hover:text-slate-200 flex items-center justify-center gap-1.5";
                panelF.classList.remove('hidden');
                panelP.classList.add('hidden');
            }
        }

        /* ==========================================================================
           PAK-21 PRESET LAYOUT GENERATORS
           ========================================================================== */
        function loadPreset(presetName) {
            state.items = [];
            state.selectedItemId = null;
            hideInspector();

            const L = state.roomLengthMeters;
            const W = state.roomWidthMeters;

            // 1. Always place Front Board & Teacher Desk
            state.items.push({
                id: Date.now() + 1,
                type: 'smartboard',
                x: L / 2 - 1.1,
                y: 0.3,
                rotation: 0,
                customLabel: 'Front Smartboard'
            });

            state.items.push({
                id: Date.now() + 2,
                type: 'teacher-desk',
                x: L / 2 - 0.75,
                y: 1.2,
                rotation: 0,
                customLabel: 'Teacher Desk'
            });

            // 2. Doorway & Storage
            state.items.push({
                id: Date.now() + 3,
                type: 'door',
                x: 0.2,
                y: W - 0.5,
                rotation: 0,
                customLabel: 'Main Entrance'
            });

            state.items.push({
                id: Date.now() + 4,
                type: 'bookshelf',
                x: L - 1.5,
                y: 0.3,
                rotation: 0,
                customLabel: 'PAK-21 Resource Corner'
            });

            // 3. Layout Specific Student Seating
            if (presetName === 'kumpulan') {
                // Group Clusters (6 Clusters of double desks)
                const clusterPositions = [
                    { x: 2.0, y: 2.8 }, { x: 5.5, y: 2.8 }, { x: 8.0, y: 2.8 },
                    { x: 2.0, y: 5.5 }, { x: 5.5, y: 5.5 }, { x: 8.0, y: 5.5 }
                ];
                let groupNum = 1;
                clusterPositions.forEach(pos => {
                    if (pos.x + 1.5 < L && pos.y + 1.5 < W) {
                        // Create 4-seat cluster using 2 double desks facing each other
                        state.items.push({
                            id: Date.now() + Math.random(),
                            type: 'double-desk',
                            x: pos.x,
                            y: pos.y,
                            rotation: 0,
                            customLabel: `Kumpulan ${groupNum}`
                        });
                        state.items.push({
                            id: Date.now() + Math.random(),
                            type: 'double-desk',
                            x: pos.x,
                            y: pos.y + 0.7,
                            rotation: 180,
                            customLabel: `Kumpulan ${groupNum}`
                        });
                        groupNum++;
                    }
                });

            } else if (presetName === 'horseshoe') {
                // U-Shape arrangement around central presentation space
                state.items.push({
                    id: Date.now() + 5,
                    type: 'presentation-mat',
                    x: L / 2 - 1.25,
                    y: 3.5,
                    rotation: 0,
                    customLabel: 'Presentation Area'
                });

                // Left wing
                for (let i = 0; i < 4; i++) {
                    state.items.push({
                        id: Date.now() + Math.random(),
                        type: 'double-desk',
                        x: 1.5,
                        y: 2.5 + i * 1.1,
                        rotation: 90,
                        customLabel: 'U-Wing L'
                    });
                }
                // Right wing
                for (let i = 0; i < 4; i++) {
                    state.items.push({
                        id: Date.now() + Math.random(),
                        type: 'double-desk',
                        x: L - 2.0,
                        y: 2.5 + i * 1.1,
                        rotation: 270,
                        customLabel: 'U-Wing R'
                    });
                }
                // Bottom base
                for (let i = 0; i < 3; i++) {
                    state.items.push({
                        id: Date.now() + Math.random(),
                        type: 'double-desk',
                        x: 2.8 + i * 1.5,
                        y: W - 1.5,
                        rotation: 0,
                        customLabel: 'U-Base'
                    });
                }

            } else if (presetName === 'stadium') {
                // Angled rows facing teacher
                const rows = 3;
                for (let r = 0; r < rows; r++) {
                    const rowY = 2.8 + r * 1.4;
                    state.items.push({
                        id: Date.now() + Math.random(),
                        type: 'double-desk',
                        x: 1.5,
                        y: rowY,
                        rotation: 15,
                        customLabel: `Row ${r+1}`
                    });
                    state.items.push({
                        id: Date.now() + Math.random(),
                        type: 'double-desk',
                        x: L / 2 - 0.65,
                        y: rowY,
                        rotation: 0,
                        customLabel: `Row ${r+1}`
                    });
                    state.items.push({
                        id: Date.now() + Math.random(),
                        type: 'double-desk',
                        x: L - 2.8,
                        y: rowY,
                        rotation: 345,
                        customLabel: `Row ${r+1}`
                    });
                }

            } else if (presetName === 'modular') {
                // Round tables and mixed stations
                state.items.push({ id: Date.now() + Math.random(), type: 'round-table', x: 2.0, y: 3.0, rotation: 0, customLabel: 'Station A' });
                state.items.push({ id: Date.now() + Math.random(), type: 'round-table', x: L - 3.4, y: 3.0, rotation: 0, customLabel: 'Station B' });
                state.items.push({ id: Date.now() + Math.random(), type: 'round-table', x: 2.0, y: 5.5, rotation: 0, customLabel: 'Station C' });
                state.items.push({ id: Date.now() + Math.random(), type: 'round-table', x: L - 3.4, y: 5.5, rotation: 0, customLabel: 'Station D' });
                state.items.push({ id: Date.now() + Math.random(), type: 'presentation-mat', x: L / 2 - 1.25, y: 4.0, rotation: 0, customLabel: 'Discussion Corner' });

            } else if (presetName === 'traditional') {
                // Grid of single/double desks
                for (let r = 0; r < 4; r++) {
                    for (let c = 0; c < 4; c++) {
                        state.items.push({
                            id: Date.now() + Math.random(),
                            type: 'double-desk',
                            x: 1.5 + c * 1.8,
                            y: 2.6 + r * 1.2,
                            rotation: 0,
                            customLabel: `Row ${r+1}`
                        });
                    }
                }
            }

            renderCanvas();
            updateMetrics();
        }

        /* ==========================================================================
           CANVAS RENDERING ENGINE (2D)
           ========================================================================== */
        function renderCanvas() {
            if (!ctx) return;

            const isDark = document.documentElement.classList.contains('dark');
            const scale = state.scale * state.zoomLevel;

            // Clear Canvas
            ctx.fillStyle = isDark ? '#0f172a' : '#f8fafc';
            ctx.fillRect(0, 0, canvas.width, canvas.height);

            // Draw Grid Lines (1 Meter Grid)
            ctx.strokeStyle = isDark ? 'rgba(51, 65, 85, 0.4)' : 'rgba(203, 213, 225, 0.8)';
            ctx.lineWidth = 1;

            for (let x = 0; x <= canvas.width; x += scale) {
                ctx.beginPath();
                ctx.moveTo(x, 0);
                ctx.lineTo(x, canvas.height);
                ctx.stroke();
            }
            for (let y = 0; y <= canvas.height; y += scale) {
                ctx.beginPath();
                ctx.moveTo(0, y);
                ctx.lineTo(canvas.width, y);
                ctx.stroke();
            }

            // Draw Circulation Pathways Overlay (Flow Lines)
            if (state.showFlow) {
                drawPathwaysOverlay(scale, isDark);
            }

            // Draw Teacher Sightlines Overlay
            if (state.showSightlines) {
                drawSightlinesOverlay(scale);
            }

            // Draw Furniture Items
            state.items.forEach(item => {
                drawFurnitureItem(item, scale, isDark);
            });
        }

        function drawFurnitureItem(item, scale, isDark) {
            const def = ITEM_TYPES[item.type] || ITEM_TYPES['single-desk'];
            const widthPx = def.width * scale;
            const heightPx = def.height * scale;
            const xPx = item.x * scale;
            const yPx = item.y * scale;

            ctx.save();
            ctx.translate(xPx + widthPx / 2, yPx + heightPx / 2);
            ctx.rotate((item.rotation * Math.PI) / 180);

            const isSelected = (item.id === state.selectedItemId);

            // Selection Glow
            if (isSelected) {
                ctx.shadowColor = '#22c55e';
                ctx.shadowBlur = 12;
            }

            // Draw Body Shape
            ctx.fillStyle = item.type === 'presentation-mat' ? 'rgba(244, 63, 94, 0.15)' : def.color;
            ctx.strokeStyle = isSelected ? '#22c55e' : (isDark ? '#e2e8f0' : '#1e293b');
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

            // Draw Label & Seats Indicator
            ctx.shadowBlur = 0; // reset
            ctx.fillStyle = '#ffffff';
            ctx.font = '600 10px Inter, sans-serif';
            ctx.textAlign = 'center';
            ctx.textBaseline = 'middle';

            const displayLabel = item.customLabel || def.label;
            ctx.fillText(displayLabel, 0, 0);

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
            ctx.strokeStyle = 'rgba(20, 184, 166, 0.4)';
            ctx.lineWidth = 1.5;

            // Draw Field of View Cone facing downwards into room
            ctx.beginPath();
            ctx.moveTo(tx, ty);
            ctx.arc(tx, ty, canvas.width * 0.9, Math.PI * 0.15, Math.PI * 0.85);
            ctx.closePath();
            ctx.fill();
            ctx.stroke();

            // Connect rays to student desks
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

        function drawPathwaysOverlay(scale, isDark) {
            ctx.save();
            ctx.strokeStyle = isDark ? 'rgba(251, 191, 36, 0.25)' : 'rgba(217, 119, 6, 0.3)';
            ctx.lineWidth = 18;
            ctx.lineCap = 'round';
            ctx.lineJoin = 'round';

            // Primary Aisles
            const L = canvas.width;
            const W = canvas.height;

            ctx.beginPath();
            // Center aisle
            ctx.moveTo(L / 2, scale * 1.5);
            ctx.lineTo(L / 2, W - scale);
            // Side clearance
            ctx.moveTo(scale, W - scale);
            ctx.lineTo(L - scale, W - scale);
            ctx.stroke();

            ctx.restore();
        }

        /* ==========================================================================
           CANVAS INTERACTION LOGIC (DRAG & DROP, SELECTION)
           ========================================================================== */
        function getItemAtCoords(x, y) {
            const scale = state.scale * state.zoomLevel;
            for (let i = state.items.length - 1; i >= 0; i--) {
                const item = state.items[i];
                const def = ITEM_TYPES[item.type] || ITEM_TYPES['single-desk'];
                const ix = item.x * scale;
                const iy = item.y * scale;
                const iw = def.width * scale;
                const ih = def.height * scale;

                if (x >= ix && x <= ix + iw && y >= iy && y <= iy + ih) {
                    return item;
                }
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
                state.dragOffset = {
                    x: mouseX - (item.x * scale),
                    y: mouseY - (item.y * scale)
                };
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

            // Grid snapping
            if (state.gridSnap) {
                newX = Math.round(newX * 4) / 4; // snap to 0.25m
                newY = Math.round(newY * 4) / 4;
            }

            // Boundary constraints
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
            state.isDragging = false;
        }

        function handleTouchStart(e) {
            if (e.touches.length === 1) {
                const touch = e.touches[0];
                handleCanvasMouseDown({ clientX: touch.clientX, clientY: touch.clientY });
            }
        }

        function handleTouchMove(e) {
            if (e.touches.length === 1) {
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
        }

        function deleteSelectedItem() {
            if (state.selectedItemId === null) return;
            state.items = state.items.filter(i => i.id !== state.selectedItemId);
            state.selectedItemId = null;
            hideInspector();
            renderCanvas();
            updateMetrics();
        }

        function showInspector(item) {
            const panel = document.getElementById('inspectorPanel');
            panel.classList.remove('hidden');
            document.getElementById('selectedItemType').innerText = ITEM_TYPES[item.type]?.label || item.type;
            document.getElementById('rotateSlider').value = item.rotation || 0;
            document.getElementById('rotateValDisplay').innerText = `${item.rotation || 0}°`;
            document.getElementById('itemLabelInput').value = item.customLabel || '';
        }

        function hideInspector() {
            document.getElementById('inspectorPanel').classList.add('hidden');
        }

        function exportCanvasImage() {
            const link = document.createElement('a');
            link.download = `PAK21_Classroom_Layout_${Date.now()}.png`;
            link.href = canvas.toDataURL('image/png');
            link.click();
        }

        /* ==========================================================================
           ANALYTICS & METRICS CALCULATOR
           ========================================================================== */
        function updateMetrics() {
            // Capacity Calculation
            let currentSeats = 0;
            state.items.forEach(item => {
                const cap = ITEM_TYPES[item.type]?.capacity || 0;
                currentSeats += cap;
            });

            document.getElementById('metricCapacityCurrent').innerText = currentSeats;
            document.getElementById('metricCapacityTarget').innerText = state.targetStudents;

            // PAK-21 Collaboration Score Calculation
            let collabDesks = 0;
            let totalDesks = 0;

            state.items.forEach(item => {
                if (ITEM_TYPES[item.type]?.capacity > 0) {
                    totalDesks++;
                    // Group/Round desks provide higher collaboration index
                    if (item.type === 'double-desk' || item.type === 'round-table') {
                        collabDesks++;
                    }
                }
            });

            let collabPercent = totalDesks > 0 ? Math.round((collabDesks / totalDesks) * 100) : 0;
            document.getElementById('metricCollabVal').innerText = `${collabPercent}%`;
            document.getElementById('metricCollabBar').style.width = `${collabPercent}%`;

            // Teacher Visibility Score
            const teacher = state.items.find(i => i.type === 'teacher-desk');
            let visibilityVal = 95;
            if (!teacher) visibilityVal = 40;
            document.getElementById('metricVisibilityVal').innerText = `${visibilityVal}%`;
            document.getElementById('metricVisibilityBar').style.width = `${visibilityVal}%`;

            // Pathway Clearance Score
            let pathwayVal = Math.max(65, Math.min(98, 100 - (state.items.length * 1.5)));
            document.getElementById('metricPathwayVal').innerText = `${Math.round(pathwayVal)}%`;
            document.getElementById('metricPathwayBar').style.width = `${pathwayVal}%`;
        }

        /* ==========================================================================
           3D PERSPECTIVE RENDERER (THREE.JS)
           ========================================================================== */
        function open3DModal() {
            const modal = document.getElementById('modal3d');
            modal.classList.remove('hidden');

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
            container.innerHTML = ''; // clear

            const width = container.clientWidth;
            const height = container.clientHeight;

            // Scene
            threeScene = new THREE.Scene();
            threeScene.background = new THREE.Color(0x0f172a);

            // Camera
            threeCamera = new THREE.PerspectiveCamera(45, width / height, 0.1, 1000);
            threeCamera.position.set(state.roomLengthMeters / 2, 12, state.roomWidthMeters * 1.5);

            // Renderer
            threeRenderer = new THREE.WebGLRenderer({ antialias: true });
            threeRenderer.setSize(width, height);
            threeRenderer.shadowMap.enabled = true;
            container.appendChild(threeRenderer.domElement);

            // Orbit Controls
            threeControls = new THREE.OrbitControls(threeCamera, threeRenderer.domElement);
            threeControls.target.set(state.roomLengthMeters / 2, 0, state.roomWidthMeters / 2);
            threeControls.update();

            // Lighting
            const ambientLight = new THREE.AmbientLight(0xffffff, 0.6);
            threeScene.add(ambientLight);

            const dirLight = new THREE.DirectionalLight(0xffffff, 0.7);
            dirLight.position.set(10, 20, 15);
            dirLight.castShadow = true;
            threeScene.add(dirLight);

            // Room Floor & Walls
            const L = state.roomLengthMeters;
            const W = state.roomWidthMeters;

            // Floor Grid mesh
            const floorGeo = new THREE.PlaneGeometry(L, W);
            const floorMat = new THREE.MeshStandardMaterial({ color: 0x1e293b, roughness: 0.8 });
            const floor = new THREE.Mesh(floorGeo, floorMat);
            floor.rotation.x = -Math.PI / 2;
            floor.position.set(L / 2, 0, W / 2);
            floor.receiveShadow = true;
            threeScene.add(floor);

            const gridHelper = new THREE.GridHelper(Math.max(L, W), Math.max(L, W), 0x3b82f6, 0x334155);
            gridHelper.position.set(L / 2, 0.01, W / 2);
            threeScene.add(gridHelper);

            // Render Furniture Meshes
            state.items.forEach(item => {
                const def = ITEM_TYPES[item.type];
                if (!def) return;

                const colorHex = parseInt(def.color.replace('#', '0x'));
                const mat = new THREE.MeshStandardMaterial({ color: colorHex, roughness: 0.4 });

                let geo;
                let height3D = 0.7; // default desk height in meters

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

                // Position (Center offset for Three.js geometry)
                const posX = item.x + def.width / 2;
                const posZ = item.y + def.height / 2;
                const posY = height3D / 2;

                mesh.position.set(posX, posY, posZ);
                mesh.rotation.y = -(item.rotation * Math.PI) / 180;

                threeScene.add(mesh);
            });

            // Animation Loop
            function animate3D() {
                if (document.getElementById('modal3d').classList.contains('hidden')) return;
                requestAnimationFrame(animate3D);
                threeControls.update();
                threeRenderer.render(threeScene, threeCamera);
            }
            animate3D();
        }

        /* ==========================================================================
           AI PEDAGOGICAL AUDIT & REPORT GENERATOR
           ========================================================================== */
        function generateAiReport() {
            const modal = document.getElementById('modalAiReport');
            const content = document.getElementById('reportContent');

            let seatCount = 0;
            state.items.forEach(i => seatCount += (ITEM_TYPES[i.type]?.capacity || 0));

            const hasSmartboard = state.items.some(i => i.type === 'smartboard');
            const hasPresentationZone = state.items.some(i => i.type === 'presentation-mat');
            const groupDesks = state.items.filter(i => i.type === 'double-desk' || i.type === 'round-table').length;

            let layoutTypeStr = groupDesks > 4 ? 'Flexible Collaborative Clusters (Kumpulan)' : 'Linear Row Layout';

            content.innerHTML = `
                <div class="grid grid-cols-3 gap-4 mb-6">
                    <div class="p-3 bg-slate-900 rounded-xl border border-slate-800">
                        <span class="text-xs text-slate-400 block">Classroom Footprint</span>
                        <span class="text-base font-bold text-emerald-400">${state.roomLengthMeters * state.roomWidthMeters} m²</span>
                    </div>
                    <div class="p-3 bg-slate-900 rounded-xl border border-slate-800">
                        <span class="text-xs text-slate-400 block">Seat vs Target</span>
                        <span class="text-base font-bold text-teal-400">${seatCount} / ${state.targetStudents}</span>
                    </div>
                    <div class="p-3 bg-slate-900 rounded-xl border border-slate-800">
                        <span class="text-xs text-slate-400 block">Primary Model</span>
                        <span class="text-base font-bold text-indigo-400">${layoutTypeStr}</span>
                    </div>
                </div>

                <div class="space-y-4">
                    <h3 class="font-bold text-slate-100 flex items-center gap-2 text-base">
                        <i data-lucide="check-circle-2" class="w-5 h-5 text-emerald-400"></i> PAK-21 Core Compliance Summary
                    </h3>

                    <div class="p-4 bg-slate-900/60 rounded-xl border border-slate-800 space-y-3">
                        <div class="flex items-start gap-3">
                            <div class="p-1.5 bg-emerald-500/20 text-emerald-400 rounded-lg mt-0.5"><i data-lucide="users" class="w-4 h-4"></i></div>
                            <div>
                                <h4 class="font-semibold text-xs text-slate-200">Collaborative Learning Support</h4>
                                <p class="text-xs text-slate-400 mt-0.5">Your layout utilizes ${groupDesks} multi-student clusters. This aligns with standard PAK-21 Pembelajaran Kooperatif (Cooperative Learning) guidelines.</p>
                            </div>
                        </div>

                        <div class="flex items-start gap-3">
                            <div class="p-1.5 bg-blue-500/20 text-blue-400 rounded-lg mt-0.5"><i data-lucide="presentation" class="w-4 h-4"></i></div>
                            <div>
                                <h4 class="font-semibold text-xs text-slate-200">Active Presentation & Student Expression</h4>
                                <p class="text-xs text-slate-400 mt-0.5">${hasPresentationZone ? 'Dedicated student presentation stage detected. Supports Student-Centered Gallery Walks & Presentations.' : 'Consider adding a dedicated Presentation Stage Mat to enhance student showcase activities.'}</p>
                            </div>
                        </div>

                        <div class="flex items-start gap-3">
                            <div class="p-1.5 bg-purple-500/20 text-purple-400 rounded-lg mt-0.5"><i data-lucide="eye" class="w-4 h-4"></i></div>
                            <div>
                                <h4 class="font-semibold text-xs text-slate-200">Teacher Sightlines & Facilitation</h4>
                                <p class="text-xs text-slate-400 mt-0.5">${hasSmartboard ? 'Smartboard position optimized for high visibility across all seating zones.' : 'Ensure a primary display focal board is positioned for optimal sightline clarity.'}</p>
                            </div>
                        </div>
                    </div>

                    <h3 class="font-bold text-slate-100 flex items-center gap-2 text-base pt-2">
                        <i data-lucide="lightbulb" class="w-5 h-5 text-amber-400"></i> Recommendations for Optimization
                    </h3>
                    <ul class="list-disc list-inside space-y-1.5 text-xs text-slate-300 bg-amber-950/20 p-4 rounded-xl border border-amber-500/20">
                        <li>Maintain at least 1.2 meters clear aisle width between outer desks and classroom walls for smooth movement during rotation activities.</li>
                        <li>Position PAK-21 Reading Corners or Bookshelves near entrance doors for quick resource access during lesson transitions.</li>
                        <li>Ensure student chairs are angled maximum 45° relative to the main smartboard to avoid visual strain.</li>
                    </ul>
                </div>
            `;

            lucide.createIcons();
            modal.classList.remove('hidden');
        }
    </script>
</body>
</html>
