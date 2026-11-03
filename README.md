```html <!DOCTYPE html> <html lang="ms"> <head> <meta charset="UTF-8"> <meta name="viewport" content="width=device-width, initial-scale=1.0"> <title>Smart Classroom Layout Generator PAK-21</title> <!-- Tailwind CSS --> <script src="https://cdn.tailwindcss.com"></script> <!-- Three.js & OrbitControls --> <script src="https://cdnjs.cloudflare.com/ajax/libs/three.js/r128/three.min.js"></ script> <script src="https://cdn.jsdelivr.net/npm/three@0.128.0/examples/js/controls/ OrbitControls.js"></script> <style> @import url('https://fonts.googleapis.com/css2? family=Inter:wght@300;400;500;600;700&display=swap'); body { font-family: 'Inter', sans-serif; } /* Custom scrollbar */ ::-webkit-scrollbar { width: 6px; height: 6px; } ::-webkit-scrollbar-track { background: #f1f5f9; } ::-webkit-scrollbar-thumb { background: #cbd5e1; border-radius: 4px; } ::-webkit-scrollbar-thumb:hover { background: #94a3b8; } 

@media print { .no-print { display: none !important; } .print-only { display: block !important; } body { background: white; color: black; } } </style> </head> <body class="bg-slate-900 text-slate-100 min-h-screen flex flex-col font-sans overflow-x-hidden"> 

<!-- Top Navigation Header --> <header class="bg-slate-800/90 backdrop-blur border-b border-slate-700/80 px-4 py-3 sticky top-0 z-30 no-print"> <div class="max-w-7xl mx-auto flex flex-col md:flex-row items-center justifybetween gap-4"> <!-- Brand & Title --> <div class="flex items-center space-x-3"> <div class="bg-gradient-to-tr from-indigo-500 to-teal-400 p-2.5 rounded-xl shadow-lg shadow-indigo-500/20"> 

<svg class="w-6 h-6 text-white" fill="none" stroke="currentColor" viewBox="0 0 24 24"> 

<path stroke-linecap="round" stroke-linejoin="round" strokewidth="2" d="M19 21V5a2 2 0 00-2-2H7a2 2 0 00-2 2v16m14 0h2m-2 0h-5m-9 

0H3m2 0h5m0 0h4m-4 0V11m0 0h4m-4 0H7m0 0v10m0-10h4"></path> 

</svg> </div> <div> <h1 class="text-lg font-bold bg-gradient-to-r from-indigo-300 viateal-200 to-emerald-300 bg-clip-text text-transparent"> PAK-21 Smart Classroom Layout Generator </h1> <p class="text-xs text-slate-400">21 世纪智能教室空间布局与实 景生成器 • Malaysian Education Standard</p> </div> </div> <!-- Dimension & Room Setup --> <div class="flex items-center space-x-3 bg-slate-900/80 px-3 py-1.5 rounded-lg border border-slate-700/60 text-xs"> <div class="flex items-center space-x-1.5"> <span class="text-slate-400">Lebar (W):</span> <input type="number" id="roomWidth" value="10" min="4" max="20" class="w-12 bg-slate-800 border border-slate-600 rounded px-1.5 py-0.5 text-center text-teal-300 focus:outline-none focus:border-teal-400"> <span class="text-slate-500">m</span> </div> <span class="text-slate-600">×</span> <div class="flex items-center space-x-1.5"> <span class="text-slate-400">Panjang (L):</span> <input type="number" id="roomLength" value="8" min="4" max="20" class="w-12 bg-slate-800 border border-slate-600 rounded px-1.5 py-0.5 text-center text-teal-300 focus:outline-none focus:border-teal-400"> <span class="text-slate-500">m</span> </div> <button id="applyRoomSize" class="bg-indigo-600 hover:bgindigo-500 text-white px-2 py-1 rounded text-xs transition"> Set </button> </div> 

<!-- Header Action Controls --> <div class="flex items-center space-x-2"> <button id="clearBtn" class="px-3 py-1.5 bg-slate-700/80 hover:bgred-900/50 hover:text-red-300 text-slate-300 rounded-lg text-xs font-medium border border-slate-600/60 transition flex items-center space-x-1"> <svg class="w-3.5 h-3.5" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" strokewidth="2" d="M19 7l-.867 12.142A2 2 0 0116.138 21H7.862a2 2 0 01-1.995-1.858L5 7m5 4v6m4-6v6m1-10V4a1 1 0 00-1-1h-4a1 1 0 00-1 1v3M4 7h16"></path></svg> <span>Bersihkan</span> </button> 

<button id="open3DBtn" class="px-3.5 py-1.5 bg-gradient-to-r fromteal-600 to-emerald-600 hover:from-teal-500 hover:to-emerald-500 text-white rounded-lg text-xs font-semibold shadow-md shadow-teal-900/30 transition flex items-center space-x-1.5"> 

<svg class="w-4 h-4" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" strokewidth="2" d="M14 10l-2 1m0 0l-2-1m2 1v2.5M20 7l-8-4-8 4m16 0l-8 4m8-4v10l-8 4m0-10L4 7m8 4v10M4 7v10l8 4"></path></svg> 

<span>Visual 3D</span> 

</button> 

<button id="openAIBtn" class="px-3.5 py-1.5 bg-gradient-to-r fromindigo-600 to-purple-600 hover:from-indigo-500 hover:to-purple-500 text-white rounded-lg text-xs font-semibold shadow-md shadow-indigo-900/30 transition flex items-center space-x-1.5"> 

<svg class="w-4 h-4" fill="none" stroke="currentColor" 

viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" strokewidth="2" d="M9.663 17h4.673M12 3v1m6.364 1.636l-.707.707M21 12h-1M4 12H3m3.343-5.657l-.707-.707m2.828 9.9a5 5 0 117.072 0l-.548.547A3.374 3.374 0 0014 18.469V19a2 2 0 11-4 

0v-.531c0-.895-.356-1.754-.988-2.386l-.548-.547z"></path></svg> <span>Analisis AI</span> 

</button> 

<button id="printBtn" class="px-3 py-1.5 bg-slate-700/80 hover:bgslate-600 text-slate-200 rounded-lg text-xs font-medium border border-slate-600/60 transition flex items-center space-x-1"> 

<svg class="w-3.5 h-3.5" fill="none" stroke="currentColor" 

viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" strokewidth="2" d="M17 17h2a2 2 0 002-2v-4a2 2 0 00-2-2H5a2 2 0 00-2 2v4a2 2 0 002 2h2m2 4h6a2 2 0 002-2v-4a2 2 0 00-2-2H9a2 2 0 00-2 2v4a2 2 0 002 2zm8-12V5a2 2 0 00-2-2H9a2 2 0 00-2 2v4h10z"></path></svg> 

<span>Cetak</span> 

</button> </div> 

</div> 

</header> 

<!-- Main Workspace Container --> 

<main class="flex-1 max-w-7xl w-full mx-auto p-4 grid grid-cols-1 lg:grid-cols-12 gap-4 no-print"> 

<!-- Sidebar Panel (Presets & Furniture Tools) --> <aside class="lg:col-span-3 space-y-4 flex flex-col"> 

<!-- Tab Navigation for Sidebar --> <div class="bg-slate-800/80 rounded-xl p-1 border border-slate-700/70 

flex space-x-1 text-xs font-medium"> <button id="tabPresets" class="flex-1 py-1.5 rounded-lg bgindigo-600 text-white shadow font-semibold transition">Presets PAK-21</button> <button id="tabItems" class="flex-1 py-1.5 rounded-lg text-slate-400 hover:text-white transition">Katalog Perabot</button> </div> 

<!-- Presets Tab Container --> <div id="presetsPanel" class="bg-slate-800/60 rounded-xl border borderslate-700/70 p-3 space-y-2.5 overflow-y-auto max-h-[68vh]"> 

<h3 class="text-xs font-semibold text-slate-400 uppercase trackingwider px-1">Layout Susun Atur Standard</h3> 

<button onclick="loadPreset('group')" class="w-full text-left p-2.5 bgslate-800 hover:bg-slate-700/80 border border-slate-700 rounded-lg transition group"> 

<div class="font-semibold text-teal-300 text-xs flex justifybetween items-center"> 

<span>1. Kumpulan (Group Clusters)</span> <span class="text-[10px] bg-teal-950 text-teal-400 px-1.5 py-0.5 rounded border border-teal-800">Utama PAK-21</span> </div> 

<p class="text-[11px] text-slate-400 mt-1">Gugusan 4-6 meja menggalakkan perbincangan, kerja kolaboratif & komunikasi aktif.</p> </button> 

<button onclick="loadPreset('uShape')" class="w-full text-left p-2.5 bg-slate-800 hover:bg-slate-700/80 border border-slate-700 rounded-lg transition"> <div class="font-semibold text-indigo-300 text-xs">2. Bentuk U (U-Shape / Horseshoe)</div> <p class="text-[11px] text-slate-400 mt-1">Sesuai untuk pembentangan, demonstrasi, dan perbincangan terbuka seluruh kelas.</p> </button> 

<button onclick="loadPreset('pairs')" class="w-full text-left p-2.5 bgslate-800 hover:bg-slate-700/80 border border-slate-700 rounded-lg transition"> <div class="font-semibold text-cyan-300 text-xs">3. Berpasangan (Pairs / Think-Pair-Share)</div> <p class="text-[11px] text-slate-400 mt-1">Memudahkan aktiviti berpasangan, interaksi rakan sebaya dan fleksibiliti ruang.</p> </button> 

<button onclick="loadPreset('fishbowl')" class="w-full text-left p-2.5 bg-slate-800 hover:bg-slate-700/80 border border-slate-700 rounded-lg transition"> <div class="font-semibold text-emerald-300 text-xs">4. Fishbowl / Debat</div> 

<p class="text-[11px] text-slate-400 mt-1">Kumpulan tengah dikelilingi pemerhati luar. Sangat efektif untuk forum & debat.</p> 

</button> 

<button onclick="loadPreset('traditional')" class="w-full text-left p-2.5 bg-slate-800 hover:bg-slate-700/80 border border-slate-700 rounded-lg transition"> <div class="font-semibold text-amber-300 text-xs">5. Teater / Ceramah Tradisional</div> 

<p class="text-[11px] text-slate-400 mt-1">Susunan menghadap papan putih untuk peperiksaan atau pengajaran berpusatkan guru.</p> </button> </div> 

<!-- Items Catalog Tab Container (Hidden by Default) --> <div id="itemsPanel" class="hidden bg-slate-800/60 rounded-xl border border-slate-700/70 p-3 space-y-2 overflow-y-auto max-h-[68vh]"> 

<h3 class="text-xs font-semibold text-slate-400 uppercase trackingwider px-1">Klik untuk Tambah Item</h3> 

<div class="grid grid-cols-2 gap-2 text-xs"> <button onclick="addFurniture('deskSingle')" class="p-2 bgslate-800 hover:bg-slate-700 rounded-lg border border-slate-700 flex flex-col itemscenter justify-center text-center"> 

<div class="w-8 h-8 rounded bg-teal-600/30 border borderteal-500 mb-1 flex items-center justify-center font-bold text-teal-300">1p</div> <span>Meja Tunggal</span> </button> 

<button onclick="addFurniture('deskDouble')" class="p-2 bgslate-800 hover:bg-slate-700 rounded-lg border border-slate-700 flex flex-col itemscenter justify-center text-center"> 

<div class="w-12 h-8 rounded bg-cyan-600/30 border border-cyan-500 mb-1 flex items-center justify-center font-bold text-cyan-300">2p</div> 

<span>Meja Berdua</span> </button> <button onclick="addFurniture('deskHex')" class="p-2 bgslate-800 hover:bg-slate-700 rounded-lg border border-slate-700 flex flex-col itemscenter justify-center text-center"> 

<div class="w-10 h-10 rounded-full bg-emerald-600/30 border border-emerald-500 mb-1 flex items-center justify-center font-bold textemerald-300">Hex</div> 

<span>Meja Hexagon (4p)</span> </button> 

<button onclick="addFurniture('teacherDesk')" class="p-2 bgslate-800 hover:bg-slate-700 rounded-lg border border-slate-700 flex flex-col itemscenter justify-center text-center"> 

<div class="w-10 h-6 bg-amber-600/30 border borderamber-500 mb-1 flex items-center justify-center font-bold text-amber-300">Guru</div> 

<span>Meja Guru</span> 

</button> <button onclick="addFurniture('smartBoard')" class="p-2 bgslate-800 hover:bg-slate-700 rounded-lg border border-slate-700 flex flex-col itemscenter justify-center text-center"> 

<div class="w-12 h-3 bg-blue-600/40 border border-blue-400 mb-1 flex items-center justify-center font-bold text-blue-300 text-[10px]">Papan</div> 

<span>Papan Interaktif</span> 

</button> 

<button onclick="addFurniture('bookshelf')" class="p-2 bgslate-800 hover:bg-slate-700 rounded-lg border border-slate-700 flex flex-col itemscenter justify-center text-center"> 

<div class="w-10 h-4 bg-orange-800/40 border borderorange-500 mb-1 flex items-center justify-center font-bold text-orange-300 text[10px]">Rak</div> 

<span>Rak Buku / Storage</span> 

</button> 

<button onclick="addFurniture('beanBag')" class="p-2 bgslate-800 hover:bg-slate-700 rounded-lg border border-slate-700 flex flex-col itemscenter justify-center text-center"> 

<div class="w-8 h-8 rounded-full bg-purple-600/40 border border-purple-400 mb-1 flex items-center justify-center font-bold text-purple-300 text[10px]">Santai</div> 

<span>Sudut Santai (Beanbag)</span> 

</button> 

<button onclick="addFurniture('divider')" class="p-2 bg-slate-800 hover:bg-slate-700 rounded-lg border border-slate-700 flex flex-col items-center justify-center text-center"> 

<div class="w-12 h-1.5 bg-slate-500 border border-slate-300 mb-1"></div> 

<span>Pemisah / Screen</span> 

</button> </div> 

</div> 

<!-- Item Properties / Selected Item Settings --> 

<div id="selectionInspector" class="bg-slate-800/80 border borderslate-700/80 rounded-xl p-3 space-y-2 text-xs"> 

<div class="flex justify-between items-center border-b borderslate-700 pb-1.5"> 

<span class="font-semibold text-slate-300">Kawalan Item Dipilih</span> 

<span id="selectedItemLabel" class="text-teal-400 fontmono">Tiada dipilih</span> 

</div> 

<div class="space-y-2 pt-1"> 

<div> 

<div class="flex justify-between text-[11px] text-slate-400 mb-1"> 

<span>Sudut Putaran (Rotation):</span> <span id="rotVal" class="font-mono text-slate-200">0°</span> </div> <input type="range" id="itemRotation" min="0" max="360" value="0" class="w-full accent-teal-400 bg-slate-700 h-1.5 rounded" disabled> </div> 

<div class="flex space-x-2 pt-1"> <button id="btnRotate90" class="flex-1 bg-slate-700 hover:bg-slate-600 text-slate-200 py-1 rounded text-[11px] disabled:opacity-40" disabled> 

+90° Putar </button> <button id="btnDuplicate" class="flex-1 bg-slate-700 hover:bg-slate-600 text-slate-200 py-1 rounded text-[11px] disabled:opacity-40" disabled> 

Duplikasi </button> <button id="btnDelete" class="flex-1 bg-red-900/60 hover:bgred-800 text-red-200 py-1 rounded text-[11px] disabled:opacity-40" disabled> Padam </button> </div> </div> </div> 

</aside> 

<!-- Center Workspace Canvas & Realtime Metrics --> <section class="lg:col-span-9 flex flex-col space-y-3"> 

<!-- Canvas Container --> <div class="relative bg-slate-950 border border-slate-800 rounded-2xl overflow-hidden shadow-2xl flex-1 flex flex-col min-h-[480px]"> 

<!-- Canvas Toolbar Overlay --> <div class="absolute top-3 left-3 right-3 flex justify-between itemscenter pointer-events-none z-10"> 

<div class="bg-slate-900/90 backdrop-blur border borderslate-700/80 rounded-lg px-3 py-1.5 text-xs flex items-center space-x-3 pointerevents-auto shadow-md"> <label class="flex items-center space-x-1.5 cursor-pointer text-slate-300"> <input type="checkbox" id="toggleGrid" checked class="rounded border-slate-600 bg-slate-800 text-teal-500 focus:ring-0"> <span>Grid (0.5m)</span> </label> 

<span class="text-slate-700">|</span> <label class="flex items-center space-x-1.5 cursor-pointer text-slate-300"> <input type="checkbox" id="toggleSnap" checked class="rounded border-slate-600 bg-slate-800 text-teal-500 focus:ring-0"> <span>Snap Grid</span> </label> <span class="text-slate-700">|</span> <label class="flex items-center space-x-1.5 cursor-pointer text-slate-300"> <input type="checkbox" id="toggleVision" checked class="rounded border-slate-600 bg-slate-800 text-teal-500 focus:ring-0"> <span>Garis Pandangan</span> </label> </div> 

<div class="bg-slate-900/90 backdrop-blur border borderslate-700/80 rounded-lg px-2.5 py-1 text-[11px] text-slate-400 pointer-events-auto"> Tarik & Lepas untuk pindahkan • Drag handles to rotate </div> </div> <!-- 2D Canvas Area --> <div class="flex-1 w-full h-full flex items-center justify-center p-6 bgslate-950"> <canvas id="floorCanvas" class="bg-slate-900 rounded-lg shadow-inner cursor-crosshair border border-slate-800"></canvas> </div> </div> <!-- Live Metrics Dashboard Bar --> <div class="grid grid-cols-2 md:grid-cols-4 gap-3"> <div class="bg-slate-800/70 border border-slate-700/80 p-3 roundedxl flex items-center justify-between"> <div> <div class="text-[11px] text-slate-400 font-medium">Kapasiti Murid</div> <div id="metricCapacity" class="text-xl font-bold textteal-300">0 Kerusi</div> </div> <div class="p-2 bg-teal-950 text-teal-400 rounded-lg"> <svg class="w-5 h-5" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" strokewidth="2" d="M12 4.354a4 4 0 110 5.292M15 21H3v-1a6 6 0 0112 0v1zm0 0h6v-1a6 6 0 00-9-5.197M13 7a4 4 0 11-8 0 4 4 0 018 0z"></path></svg> </div> </div> 

<div class="bg-slate-800/70 border border-slate-700/80 p-3 roundedxl flex items-center justify-between"> 

<div> <div class="text-[11px] text-slate-400 font-medium">Skor Kolaborasi</div> 

<div id="metricCollab" class="text-xl font-bold textindigo-300">0%</div> </div> <div class="p-2 bg-indigo-950 text-indigo-400 rounded-lg"> <svg class="w-5 h-5" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" strokewidth="2" d="M17 20h5v-2a3 3 0 00-5.356-1.857M17 20H7m10 0v-2c0-.656-.1261.283-.356-1.857M7 20H2v-2a3 3 0 015.356-1.857M7 20v-2c0-.656.126-1.283.3561.857m0 0a5.002 5.002 0 019.288 0M15 7a3 3 0 11-6 0 3 3 0 016 0zm6 3a2 2 0 114 0 2 2 0 014 0zM7 10a2 2 0 11-4 0 2 2 0 014 0z"></path></svg> </div> </div> 

<div class="bg-slate-800/70 border border-slate-700/80 p-3 roundedxl flex items-center justify-between"> <div> <div class="text-[11px] text-slate-400 fontmedium">Kejelasan Pandangan</div> 

<div id="metricVision" class="text-xl font-bold textemerald-300">0%</div> </div> 

<div class="p-2 bg-emerald-950 text-emerald-400 rounded-lg"> <svg class="w-5 h-5" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" strokewidth="2" d="M15 12a3 3 0 11-6 0 3 3 0 016 0z"></path><path strokelinecap="round" stroke-linejoin="round" stroke-width="2" d="M2.458 12C3.732 7.943 7.523 5 12 5c4.478 0 8.268 2.943 9.542 7-1.274 4.057-5.064 7-9.542 7-4.477 0- 8.268-2.943-9.542-7z"></path></svg> 

</div> </div> 

<div class="bg-slate-800/70 border border-slate-700/80 p-3 roundedxl flex items-center justify-between"> <div> 

<div class="text-[11px] text-slate-400 fontmedium">Kelancaran Laluan</div> <div id="metricFlow" class="text-xl font-bold textamber-300">Baik</div> 

</div> 

<div class="p-2 bg-amber-950 text-amber-400 rounded-lg"> <svg class="w-5 h-5" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" strokewidth="2" d="M13 7h8m0 0v8m0-8l-8 8-4-4-6 6"></path></svg> 

</div> </div> </div> 

</section> </main> 

<!-- 3D Viewer Modal --> <div id="modal3D" class="fixed inset-0 bg-slate-950/80 backdrop-blur-md z-50 hidden flex flex-col no-print"> <div class="bg-slate-900 border-b border-slate-800 px-6 py-3 flex justifybetween items-center"> <div class="flex items-center space-x-2"> <div class="w-3 h-3 rounded-full bg-teal-400 animate-pulse"></div> <h2 class="text-sm font-bold text-slate-100">Visualisasi Realiti 3D Bilik Darjah (Three.js Engine)</h2> </div> <div class="flex items-center space-x-3"> <!-- Camera Preset Buttons --> <div class="bg-slate-800 rounded-lg p-1 border border-slate-700 flex text-xs"> <button onclick="setCameraView('orbit')" class="px-2.5 py-1 hover:bg-slate-700 rounded text-slate-300">Perspektif</button> <button onclick="setCameraView('top')" class="px-2.5 py-1 hover:bg-slate-700 rounded text-slate-300">Pandangan Atas</button> <button onclick="setCameraView('teacher')" class="px-2.5 py-1 hover:bg-slate-700 rounded text-slate-300">Mata Guru</button> </div> <button id="close3DBtn" class="text-slate-400 hover:text-white p-1 rounded-lg"> <svg class="w-6 h-6" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" strokewidth="2" d="M6 18L18 6M6 6l12 12"></path></svg> </button> </div> </div> 

<div class="flex-1 relative bg-black" id="container3D"> <!-- Canvas Three.js will be dynamically injected here --> <div class="absolute bottom-4 left-4 bg-slate-900/80 border borderslate-700 rounded-lg px-3 py-2 text-xs text-slate-300 pointer-events-none"> 💡 <span class="font-semibold">Kawalan 3D:</span> Klik kiri + Tarik untuk Putar | Klik kanan untuk Pan | Scroll untuk Zum. </div> </div> </div> 

<!-- AI Analysis Report Modal --> <div id="modalAI" class="fixed inset-0 bg-slate-950/80 backdrop-blur-md z-50 hidden flex items-center justify-center p-4 no-print"> 

<div class="bg-slate-900 border border-slate-700 rounded-2xl max-w-2xl w- full max-h-[85vh] flex flex-col shadow-2xl"> 

<div class="p-4 border-b border-slate-800 flex justify-between itemscenter"> 

<div class="flex items-center space-x-2"> 

<div class="p-1.5 bg-indigo-600/30 text-indigo-400 rounded-lg"> <svg class="w-5 h-5" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" strokewidth="2" d="M9.663 17h4.673M12 3v1m6.364 1.636l-.707.707M21 12h-1M4 12H3m3.343-5.657l-.707-.707m2.828 9.9a5 5 0 117.072 0l-.548.547A3.374 3.374 0 0014 18.469V19a2 2 0 11-4 0v-.531c0-.895-.356-1.754-.988-2.386l-.548-.547z"></path></svg> </div> <h2 class="text-base font-bold text-slate-100">Laporan Analisis & Cadangan AI PAK-21</h2> </div> <button id="closeAIBtn" class="text-slate-400 hover:text-white"> <svg class="w-6 h-6" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" strokewidth="2" d="M6 18L18 6M6 6l12 12"></path></svg> </button> </div> 

<div class="p-6 overflow-y-auto space-y-4 text-xs leading-relaxed textslate-300" id="aiReportContent"> <!-- AI Generated Content Hydrated by JS --> </div> 

<div class="p-4 border-t border-slate-800 flex justify-end"> <button id="closeAIBtn2" class="px-4 py-2 bg-indigo-600 hover:bgindigo-500 text-white rounded-lg text-xs font-semibold">Tutup Laporan</button> </div> </div> </div> 

<!-- Printable Report View Area (Hidden on screen, shown in window.print) --> <div id="printReportArea" class="hidden print-only p-8 bg-white text-black"> <div class="border-b-2 border-black pb-4 mb-6 flex justify-between itemscenter"> 

<div> 

<h1 class="text-2xl font-bold">Laporan Susun Atur Bilik Darjah Smart PAK-21</h1> 

<p class="text-sm text-gray-600">Jana secara automatik menerusi Smart Classroom Generator</p> </div> 

<div class="text-right text-xs text-gray-500"> <p>Tarikh: <span id="printDate"></span></p> <p>Standard PAK-21 KPM</p> </div> </div> <div class="grid grid-cols-2 gap-6 mb-6"> <div class="border p-4 rounded"> <h3 class="font-bold border-b pb-1 mb-2">Maklumat Ruang & Kapasiti</h3> <p>Dimensi Bilik: <span id="printDim">10m x 8m</span></p> <p>Kapasiti Murid: <span id="printCap">0</span> orang</p> <p>Anggaran Jumlah Perabot: <span id="printItems">0</span> unit</p> </div> <div class="border p-4 rounded"> <h3 class="font-bold border-b pb-1 mb-2">Penilaian Metrik PAK-21</h3> <p>Skor Kolaborasi: <span id="printCollab">0%</span></p> <p>Kadar Kejelasan Pandangan: <span id="printVision">0%</span></p> <p>Kelancaran Aliran Laluan: <span id="printFlow">Baik</span></p> </div> </div> <div class="border p-4 rounded mb-6"> <h3 class="font-bold border-b pb-2 mb-3">Ringkasan Analisis Pedagogi AI</h3> <div id="printAIContent" class="text-xs space-y-2"></div> </div> <div class="text-center text-xs text-gray-400 mt-12 border-t pt-4"> Dokumen ini dijana secara digital oleh Sistem Layout Generator Pembelajaran Abad Ke-21. </div> </div> <script> /* State & Global Variables */ let roomConfig = { width: 10, length: 8 }; // In Meters let furnitureList = []; let selectedId = null; const canvas = document.getElementById('floorCanvas'); const ctx = canvas.getContext('2d'); // Dragging & Interaction State 

let isDragging = false; let isRotating = false; let dragOffsetX = 0; let dragOffsetY = 0; 

// Visual Toggles let showGrid = true; let snapToGrid = true; let showVisionLines = true; 

/* Furniture Definitions Template */ const ITEM_TYPES = { deskSingle: { type: 'deskSingle', name: 'Meja Single', w: 0.7, h: 0.6, color: '#14b8a6', seats: 1 }, deskDouble: { type: 'deskDouble', name: 'Meja Double', w: 1.3, h: 0.6, color: '#06b6d4', seats: 2 }, 

deskHex: { type: 'deskHex', name: 'Meja Hexagon', w: 1.5, h: 1.5, color: '#10b981', seats: 4 }, teacherDesk: { type: 'teacherDesk', name: 'Meja Guru', w: 1.6, h: 0.8, color: '#f59e0b', seats: 0 }, smartBoard: { type: 'smartBoard', name: 'Papan Interaktif', w: 2.2, h: 0.2, color: '#3b82f6', seats: 0 }, bookshelf: { type: 'bookshelf', name: 'Rak Buku', w: 1.5, h: 0.5, color: '#d97706', seats: 0 }, beanBag: { type: 'beanBag', name: 'Beanbag Santai', w: 0.9, h: 0.9, color: '#a855f7', seats: 1 }, 

divider: { type: 'divider', name: 'Pemisah / Screen', w: 1.8, h: 0.15, color: '#64748b', seats: 0 } }; 

/* Initialization */ window.addEventListener('load', () => { resizeCanvas(); loadPreset('group'); // Default preset setupEventListeners(); animate2D(); }); 

window.addEventListener('resize', resizeCanvas); 

function resizeCanvas() { const container = canvas.parentElement; canvas.width = container.clientWidth - 30; canvas.height = container.clientHeight - 30; drawCanvas(); } 

- /* Convert meters to canvas pixels */ 

function getScale() { const scaleX = canvas.width / roomConfig.width; const scaleY = canvas.height / roomConfig.length; return Math.min(scaleX, scaleY) * 0.9; } function getOrigin() { const scale = getScale(); const pW = roomConfig.width * scale; const pH = roomConfig.length * scale; return { x: (canvas.width - pW) / 2, y: (canvas.height - pH) / 2, scale: scale }; } function loadPreset(type) { furnitureList = []; const rW = roomConfig.width; const rL = roomConfig.length; 

// Always add SmartBoard & Teacher Desk at top center furnitureList.push({ id: Date.now() + 1, ...ITEM_TYPES.smartBoard, x: rW / 2, y: 0.4, rotation: 0 }); furnitureList.push({ id: Date.now() + 2, ...ITEM_TYPES.teacherDesk, x: rW / 2 - 2.5, y: 1.2, rotation: 0 }); if (type === 'group') { // PAK-21 Clusters of 4-6 desks const clusters = [ { x: rW * 0.28, y: rL * 0.38 }, { x: rW * 0.72, y: rL * 0.38 }, { x: rW * 0.28, y: rL * 0.72 }, { x: rW * 0.72, y: rL * 0.72 } ]; clusters.forEach((c, idx) => { 

// Create group of 4 single desks face-to-face furnitureList.push({ id: Date.now() + idx * 10 + 1, ...ITEM_TYPES.deskDouble, x: c.x - 0.7, y: c.y, rotation: 90 }); furnitureList.push({ id: Date.now() + idx * 10 + 2, ...ITEM_TYPES.deskDouble, x: c.x + 0.7, y: c.y, rotation: 90 }); }); // Add soft corner reading area furnitureList.push({ id: Date.now() + 99, ...ITEM_TYPES.beanBag, x: 1.0, y: rL - 1.0, rotation: 0 }); furnitureList.push({ id: Date.now() + 100, ...ITEM_TYPES.bookshelf, x: 1.2, y: rL - 0.3, rotation: 0 }); 

} else if (type === 'uShape') { // U-Shape arrangement const startY = 2.5; for (let i = 0; i < 4; i++) { furnitureList.push({ id: Date.now() + i * 2, ...ITEM_TYPES.deskDouble, x: 1.8, y: startY + i * 1.2, rotation: 90 }); furnitureList.push({ id: Date.now() + i * 2 + 1, ...ITEM_TYPES.deskDouble, x: rW - 1.8, y: startY + i * 1.2, rotation: 90 }); } for (let j = 0; j < 3; j++) { furnitureList.push({ id: Date.now() + 20 + j, ...ITEM_TYPES.deskDouble, x: 3.2 + j * 1.8, y: rL - 1.2, rotation: 0 }); } } else if (type === 'pairs') { // Pair Work (Think-Pair-Share) for (let row = 0; row < 3; row++) { for (let col = 0; col < 3; col++) { furnitureList.push({ id: Date.now() + row * 10 + col, ...ITEM_TYPES.deskDouble, x: 2.2 + col * 2.8, y: 2.5 + row * 1.8, rotation: 0 }); } } } else if (type === 'traditional') { // Traditional Rows for (let row = 0; row < 4; row++) { for (let col = 0; col < 4; col++) { furnitureList.push({ id: Date.now() + row * 10 + col, ...ITEM_TYPES.deskSingle, x: 2.0 + col * 2.0, 

y: 2.5 + row * 1.4, rotation: 0 }); } } } else if (type === 'fishbowl') { // Inner Circle + Outer Row furnitureList.push({ id: Date.now() + 1, ...ITEM_TYPES.deskHex, x: rW / 2, y: rL / 2 + 0.5, rotation: 0 }); for (let i = 0; i < 4; i++) { furnitureList.push({ id: Date.now() + 10 + i, ...ITEM_TYPES.deskDouble, x: 1.8, y: 2.5 + i * 1.3, rotation: 90 }); furnitureList.push({ id: Date.now() + 20 + i, ...ITEM_TYPES.deskDouble, x: rW - 1.8, y: 2.5 + i * 1.3, rotation: 90 }); } } selectedId = null; updateInspector(); updateMetrics(); drawCanvas(); } function addFurniture(typeKey) { const template = ITEM_TYPES[typeKey]; if (!template) return; const item = { id: Date.now(), ...template, x: roomConfig.width / 2, y: roomConfig.length / 2, rotation: 0 }; furnitureList.push(item); selectedId = item.id; updateInspector(); updateMetrics(); drawCanvas(); } function drawCanvas() { ctx.clearRect(0, 0, canvas.width, canvas.height); const { x: originX, y: originY, scale } = getOrigin(); // Draw Room Outline const pWidth = roomConfig.width * scale; const pLength = roomConfig.length * scale; 

ctx.save(); ctx.translate(originX, originY); // Floor Background ctx.fillStyle = '#0f172a'; ctx.fillRect(0, 0, pWidth, pLength); // Grid Lines (0.5m) if (showGrid) { ctx.strokeStyle = '#1e293b'; ctx.lineWidth = 1; const gridSize = 0.5 * scale; for (let x = 0; x <= pWidth; x += gridSize) { ctx.beginPath(); ctx.moveTo(x, 0); ctx.lineTo(x, pLength); ctx.stroke(); } for (let y = 0; y <= pLength; y += gridSize) { ctx.beginPath(); ctx.moveTo(0, y); ctx.lineTo(pWidth, y); ctx.stroke(); } } // Room Walls ctx.strokeStyle = '#38bdf8'; ctx.lineWidth = 4; ctx.strokeRect(0, 0, pWidth, pLength); // Find Board position for Vision Lines const board = furnitureList.find(i => i.type === 'smartBoard'); // Render Furniture Items furnitureList.forEach(item => { const ix = item.x * scale; const iy = item.y * scale; const iw = item.w * scale; const ih = item.h * scale; ctx.save(); ctx.translate(ix, iy); ctx.rotate((item.rotation * Math.PI) / 180); const isSelected = item.id === selectedId; // Draw Item Silhouette ctx.fillStyle = item.color; ctx.globalAlpha = isSelected ? 0.9 : 0.75; if (item.type === 'deskHex' || item.type === 'beanBag') { 

ctx.beginPath(); ctx.arc(0, 0, iw / 2, 0, Math.PI * 2); ctx.fill(); } else { ctx.beginPath(); ctx.roundRect(-iw / 2, -ih / 2, iw, ih, 4); ctx.fill(); } // Selection Highlight & Handles if (isSelected) { ctx.strokeStyle = '#f43f5e'; ctx.lineWidth = 2; ctx.strokeRect(-iw / 2 - 3, -ih / 2 - 3, iw + 6, ih + 6); // Rotation Handle Indicator ctx.fillStyle = '#f43f5e'; ctx.beginPath(); ctx.arc(0, -ih / 2 - 12, 5, 0, Math.PI * 2); ctx.fill(); ctx.beginPath(); ctx.moveTo(0, -ih / 2); ctx.lineTo(0, -ih / 2 - 12); ctx.stroke(); } // Text Label on Item ctx.globalAlpha = 1.0; ctx.fillStyle = '#f **f** ff'; ctx.font = `${Math.max(9, scale * 0.25)}px Inter, sans-serif`; ctx.textAlign = 'center'; ctx.textBaseline = 'middle'; ctx.fillText(item.name, 0, 0); ctx.restore(); // Draw Vision Rays to Board if enabled if (showVisionLines && board && item.seats > 0) { ctx.strokeStyle = 'rgba(56, 189, 248, 0.15)'; ctx.lineWidth = 1; ctx.setLineDash([3, 3]); ctx.beginPath(); ctx.moveTo(ix, iy); ctx.lineTo(board.x * scale, board.y * scale); ctx.stroke(); ctx.setLineDash([]); } }); 

ctx.restore(); } 

function setupEventListeners() { 

// Tab switching 

document.getElementById('tabPresets').addEventListener('click', (e) => { document.getElementById('presetsPanel').classList.remove('hidden'); document.getElementById('itemsPanel').classList.add('hidden'); e.target.className = 'flex-1 py-1.5 rounded-lg bg-indigo-600 textwhite shadow font-semibold transition'; 

document.getElementById('tabItems').className = 'flex-1 py-1.5 rounded-lg text-slate-400 hover:text-white transition'; 

}); 

document.getElementById('tabItems').addEventListener('click', (e) => { document.getElementById('itemsPanel').classList.remove('hidden'); document.getElementById('presetsPanel').classList.add('hidden'); e.target.className = 'flex-1 py-1.5 rounded-lg bg-indigo-600 textwhite shadow font-semibold transition'; 

document.getElementById('tabPresets').className = 'flex-1 py-1.5 rounded-lg text-slate-400 hover:text-white transition'; 

}); 

// Toggles 

document.getElementById('toggleGrid').addEventListener('change', (e) => { showGrid = e.target.checked; drawCanvas(); }); 

document.getElementById('toggleSnap').addEventListener('change', (e) 

- => { snapToGrid = e.target.checked; }); 

document.getElementById('toggleVision').addEventListener('change', (e) => { showVisionLines = e.target.checked; drawCanvas(); }); 

// Room Resize 

document.getElementById('applyRoomSize').addEventListener('click', () => { 

roomConfig.width = parseFloat(document.getElementById('roomWidth').value) || 10; roomConfig.length = parseFloat(document.getElementById('roomLength').value) || 8; drawCanvas(); 

updateMetrics(); }); 

// Canvas Pointer Events 

canvas.addEventListener('mousedown', onMouseDown); canvas.addEventListener('mousemove', onMouseMove); window.addEventListener('mouseup', onMouseUp); 

// Selected Item Inspector Controls 

document.getElementById('itemRotation').addEventListener('input', (e) => { if (selectedId) { const item = furnitureList.find(i => i.id === selectedId); if (item) { item.rotation = parseInt(e.target.value); document.getElementById('rotVal').textContent = `$ {item.rotation}°`; drawCanvas(); } } }); document.getElementById('btnRotate90').addEventListener('click', () => { if (selectedId) { const item = furnitureList.find(i => i.id === selectedId); if (item) { item.rotation = (item.rotation + 90) % 360; document.getElementById('itemRotation').value = item.rotation; document.getElementById('rotVal').textContent = `$ {item.rotation}°`; drawCanvas(); } } }); document.getElementById('btnDuplicate').addEventListener('click', () => { if (selectedId) { const item = furnitureList.find(i => i.id === selectedId); if (item) { const newItem = { ...item, id: Date.now(), x: item.x + 0.5, y: item.y + 0.5 }; furnitureList.push(newItem); selectedId = newItem.id; updateInspector(); updateMetrics(); drawCanvas(); } } }); document.getElementById('btnDelete').addEventListener('click', () => { if (selectedId) { furnitureList = furnitureList.filter(i => i.id !== selectedId); selectedId = null; updateInspector(); updateMetrics(); 

drawCanvas(); } }); document.getElementById('clearBtn').addEventListener('click', () => { furnitureList = []; selectedId = null; updateInspector(); updateMetrics(); drawCanvas(); }); // Modal Controls document.getElementById('open3DBtn').addEventListener('click', open3DModal); document.getElementById('close3DBtn').addEventListener('click', () => document.getElementById('modal3D').classList.add('hidden')); document.getElementById('openAIBtn').addEventListener('click', openAIReport); document.getElementById('closeAIBtn').addEventListener('click', () => document.getElementById('modalAI').classList.add('hidden')); document.getElementById('closeAIBtn2').addEventListener('click', () => document.getElementById('modalAI').classList.add('hidden')); 

document.getElementById('printBtn').addEventListener('click', triggerPrint); } function getMousePos(e) { const rect = canvas.getBoundingClientRect(); const { x: originX, y: originY, scale } = getOrigin(); const mx = e.clientX - rect.left - originX; const my = e.clientY - rect.top - originY; return { x: mx / scale, y: my / scale }; } function onMouseDown(e) { const pos = getMousePos(e); let clickedItem = null; // Check hit test in reverse (top items first) for (let i = furnitureList.length - 1; i >= 0; i--) { const item = furnitureList[i]; const dx = Math.abs(pos.x - item.x); const dy = Math.abs(pos.y - item.y); if (dx <= item.w / 1.5 && dy <= item.h / 1.5) { clickedItem = item; 

break; } } if (clickedItem) { selectedId = clickedItem.id; isDragging = true; dragOffsetX = pos.x - clickedItem.x; dragOffsetY = pos.y - clickedItem.y; } else { selectedId = null; } updateInspector(); drawCanvas(); } 

function onMouseMove(e) { if (!isDragging || !selectedId) return; const pos = getMousePos(e); const item = furnitureList.find(i => i.id === selectedId); if (item) { let newX = pos.x - dragOffsetX; let newY = pos.y - dragOffsetY; 

if (snapToGrid) { newX = Math.round(newX * 2) / 2; newY = Math.round(newY * 2) / 2; } 

// Bound inside room 

item.x = Math.max(0.5, Math.min(roomConfig.width - 0.5, newX)); item.y = Math.max(0.5, Math.min(roomConfig.length - 0.5, newY)); 

drawCanvas(); } } 

function onMouseUp() { if (isDragging) { isDragging = false; updateMetrics(); } } 

function updateInspector() { const item = furnitureList.find(i => i.id === selectedId); const rotInput = document.getElementById('itemRotation'); 

const rotVal = document.getElementById('rotVal'); const label = document.getElementById('selectedItemLabel'); const btnRot = document.getElementById('btnRotate90'); const btnDup = document.getElementById('btnDuplicate'); const btnDel = document.getElementById('btnDelete'); 

if (item) { label.textContent = item.name; rotInput.disabled = false; rotInput.value = item.rotation; rotVal.textContent = `${item.rotation}°`; btnRot.disabled = false; btnDup.disabled = false; btnDel.disabled = false; } else { label.textContent = 'Tiada dipilih'; rotInput.disabled = true; rotInput.value = 0; rotVal.textContent = '0°'; btnRot.disabled = true; btnDup.disabled = true; btnDel.disabled = true; } } function updateMetrics() { let capacity = 0; let totalDesks = 0; furnitureList.forEach(item => { capacity += item.seats || 0; if (item.seats > 0) totalDesks++; }); // Collaboration Score Calculation based on cluster proximity let collabScore = 70; if (totalDesks > 0) { let closePairs = 0; for (let i = 0; i < furnitureList.length; i++) { for (let j = i + 1; j < furnitureList.length; j++) { const a = furnitureList[i]; const b = furnitureList[j]; if (a.seats > 0 && b.seats > 0) { const dist = Math.hypot(a.x - b.x, a.y - b.y); if (dist < 1.8) closePairs++; } } } 

collabScore = Math.min(98, Math.round(50 + (closePairs / totalDesks) * 35)); } 

// Vision Visibility Rate const board = furnitureList.find(i => i.type === 'smartBoard'); let visionRate = 85; if (board && totalDesks > 0) { let goodVisionCount = 0; furnitureList.forEach(item => { if (item.seats > 0) { const angle = Math.abs(Math.atan2(item.y - board.y, item.x - board.x) * (180 / Math.PI)); if (angle > 30 && angle < 150) goodVisionCount++; } }); visionRate = Math.round((goodVisionCount / totalDesks) * 100); } 

// Update UI Elements 

document.getElementById('metricCapacity').textContent = `${capacity} Murid`; 

document.getElementById('metricCollab').textContent = `${collabScore} %`; document.getElementById('metricVision').textContent = `${visionRate} %`; document.getElementById('metricFlow').textContent = totalDesks > 12 ? 'Sederhana' : 'Cemerlang'; } 

function animate2D() { drawCanvas(); requestAnimationFrame(animate2D); } 

let scene, camera, renderer, controls; let is3DInit = false; 

function open3DModal() { 

document.getElementById('modal3D').classList.remove('hidden'); if (!is3DInit) { init3DScene(); is3DInit = true; } else { build3DClassroom(); } } 

function init3DScene() { const container = document.getElementById('container3D'); scene = new THREE.Scene(); scene.background = new THREE.Color(0x0f172a); camera = new THREE.PerspectiveCamera(45, container.clientWidth / container.clientHeight, 0.1, 100); renderer = new THREE.WebGLRenderer({ antialias: true }); renderer.setSize(container.clientWidth, container.clientHeight); renderer.shadowMap.enabled = true; container.appendChild(renderer.domElement); 

controls = new THREE.OrbitControls(camera, renderer.domElement); controls.enableDamping = true; // Lights const ambientLight = new THREE.AmbientLight(0xf **f** ff, 0.6); scene.add(ambientLight); const dirLight = new THREE.DirectionalLight(0xf **f** ff, 0.8); dirLight.position.set(10, 20, 10); dirLight.castShadow = true; scene.add(dirLight); setCameraView('orbit'); build3DClassroom(); function renderLoop() { requestAnimationFrame(renderLoop); controls.update(); renderer.render(scene, camera); } renderLoop(); } function setCameraView(mode) { const rW = roomConfig.width; const rL = roomConfig.length; if (mode === 'top') { camera.position.set(rW / 2, 14, rL / 2); controls.target.set(rW / 2, 0, rL / 2); } else if (mode === 'teacher') { camera.position.set(rW / 2, 1.5, 0.5); controls.target.set(rW / 2, 1.0, rL / 2); } else { // Orbit / Perspective camera.position.set(rW * 1.2, 10, rL * 1.5); controls.target.set(rW / 2, 0, rL / 2); } 

controls.update(); } function build3DClassroom() { // Clear existing meshes while (scene.children.length > 0) { scene.remove(scene.children[0]); } // Re-add lights const ambientLight = new THREE.AmbientLight(0xf **f** ff, 0.6); scene.add(ambientLight); const dirLight = new THREE.DirectionalLight(0xf **f** ff, 0.8); dirLight.position.set(10, 20, 10); scene.add(dirLight); const rW = roomConfig.width; const rL = roomConfig.length; 

// Floor const floorGeo = new THREE.PlaneGeometry(rW, rL); const floorMat = new THREE.MeshStandardMaterial({ color: 0x1e293b, roughness: 0.8 }); const floor = new THREE.Mesh(floorGeo, floorMat); floor.rotation.x = -Math.PI / 2; floor.position.set(rW / 2, 0, rL / 2); scene.add(floor); // Grid Lines on Floor const gridHelper = new THREE.GridHelper(Math.max(rW, rL), Math.max(rW, rL), 0x38bdf8, 0x334155); gridHelper.position.set(rW / 2, 0.01, rL / 2); scene.add(gridHelper); // Furniture Objects furnitureList.forEach(item => { const group = new THREE.Group(); const colorHex = parseInt(item.color.replace('#', '0x')); if (item.type === 'smartBoard') { const boardGeo = new THREE.BoxGeometry(item.w, 1.2, 0.1); const boardMat = new THREE.MeshStandardMaterial({ color: 0x0284c7 }); const board = new THREE.Mesh(boardGeo, boardMat); board.position.y = 1.5; group.add(board); } else if (item.type === 'beanBag') { const bagGeo = new THREE.DodecahedronGeometry(item.w / 

2); const bagMat = new THREE.MeshStandardMaterial({ color: colorHex, roughness: 0.5 }); const bag = new THREE.Mesh(bagGeo, bagMat); bag.position.y = item.w / 3; group.add(bag); } else { // Standard Desks const deskGeo = new THREE.BoxGeometry(item.w, 0.7, item.h); const deskMat = new THREE.MeshStandardMaterial({ color: colorHex, roughness: 0.4 }); const desk = new THREE.Mesh(deskGeo, deskMat); desk.position.y = 0.35; group.add(desk); // Add Chairs for Desks with seats if (item.seats > 0) { for (let s = 0; s < item.seats; s++) { const chairGeo = new THREE.BoxGeometry(0.4, 0.4, 0.4); const chairMat = new THREE.MeshStandardMaterial({ color: 0x475569 }); const chair = new THREE.Mesh(chairGeo, chairMat); const offsetX = (s - (item.seats - 1) / 2) * 0.5; chair.position.set(offsetX, 0.2, item.h / 2 + 0.3); group.add(chair); } } } group.position.set(item.x, 0, item.y); group.rotation.y = -(item.rotation * Math.PI) / 180; scene.add(group); }); } function openAIReport() { const reportContent = document.getElementById('aiReportContent'); const totalCapacity = furnitureList.reduce((acc, i) => acc + (i.seats || 0), 0); 

const collabScore = document.getElementById('metricCollab').textContent; const visionRate = document.getElementById('metricVision').textContent; 

reportContent.innerHTML = ` <div class="bg-indigo-950/40 border border-indigo-800/60 p-3.5 rounded-xl"> <h4 class="font-bold text-indigo-300 mb-1 text-sm">💡 Rumusan 

# Cadangan Pedagogi PAK-21</h4> 

<p>Susun atur semasa menyokong pembelajaran berasaskan aktiviti dengan skor kolaborasi <span class="text-teal-300 font-bold">${collabScore} </span>. Kapasiti optimum kelas ini adalah <span class="text-teal-300 font-bold">$ {totalCapacity} orang murid</span>.</p> 

</div> 

<div class="space-y-3"> 

<div class="border-l-2 border-teal-400 pl-3"> <h5 class="font-semibold text-slate-200">1. Pembelajaran Aktif & Kerja Kumpulan</h5> 

<p class="text-slate-400 text-[11px] mt-0.5">Pengelompokan meja memudahkan kaedah <em>Think-Pair-Share</em> dan perbincangan koperatif. Murid boleh berinteraksi tanpa halangan fizikal.</p> 

</div> 

<div class="border-l-2 border-indigo-400 pl-3"> 

<h5 class="font-semibold text-slate-200">2. Aliran Laluan & Akses Guru (Ergonomik)</h5> 

<p class="text-slate-400 text-[11px] mt-0.5">Laluan fleksibel membolehkan guru bergerak sebagai fasilitator ke setiap kumpulan murid dengan kadar kejelasan pandangan skrin sebanyak <span class="text-indigo-300 fontsemibold">${visionRate}</span>.</p> 

</div> 

<div class="border-l-2 border-emerald-400 pl-3"> <h5 class="font-semibold text-slate-200">3. Cadangan Penambahbaikan Ruang</h5> 

<ul class="list-disc list-inside text-slate-400 text-[11px] mt-1 space-y-1"> 

<li>Pastikan zon membaca/santai dilengkapi rak bahan bacaan bertaraf PAK-21.</li> 

<li>Sediakan ruang sekurang-kurangnya 1.2m di sekeliling papan interaktif untuk aktiviti pembentangan murid.</li> </ul> </div> </div> `; 

document.getElementById('modalAI').classList.remove('hidden'); } 

function triggerPrint() { const now = new Date(); document.getElementById('printDate').textContent = now.toLocaleDateString('ms-MY'); 

document.getElementById('printDim').textContent = `${roomConfig.width} m x ${roomConfig.length}m`; 

document.getElementById('printCap').textContent = document.getElementById('metricCapacity').textContent; document.getElementById('printItems').textContent = furnitureList.length; document.getElementById('printCollab').textContent = document.getElementById('metricCollab').textContent; document.getElementById('printVision').textContent = document.getElementById('metricVision').textContent; document.getElementById('printFlow').textContent = document.getElementById('metricFlow').textContent; 

// Copy AI analysis to print template openAIReport(); document.getElementById('printAIContent').innerHTML = document.getElementById('aiReportContent').innerHTML; document.getElementById('modalAI').classList.add('hidden'); 

window.print(); } </script> </body> </html> ``` 

