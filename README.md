<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>webshell.app</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <style>
        @font-face {
            font-family: 'Product Sans';
            /* Base64 encoded font data from ProductSans-Regular.woff */
            src: url('data:application/font-woff;base64,d09GRgABAAAAAEgYAAoAAAAAigAAAQAAAAAAAAAAAAAAAAAAAAAAAAAAAABDRkYgAAAAAAGgAAABCAAAA4IZ2ZlZgAAAAgAAAD0AAAAUjhoZXJvAAAAAwAAAUgAAAAICfhdGhtdgAAAAQAAAFQAAAAFGhtdHMAAAAEAAABbAAAACRsb2NhAAAAAwAAAXQAAAAGbGjYQAAAAgAAAF8AAAACm1heHAAAAAEAAABjAAAACBuYW1lAAAAIAAAAZQAAAIgcG9zdAAAAAMAAAH4AAAAIHNvZnQAAAEAAAIQAAAACgABAAIAAAAAAAAAAAAAAAAAAQAABgB/BQADAAAAAAP/A+gAAAAAA/8D6AAAAAAAAAABAAEAAAAFAAMAAQAEAAAABAAEAAEAAAAFAAMAAQAEAAAAAQAAAAEAAAAAAQAEAAEAAAAAAQAABAAAAAEAADgAIAEAAgEBAAIBAgIBAwAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAD/9k=') format('woff');
            font-weight: normal;
            font-style: normal;
        }

        :root {
            --taskbar-height: 48px;
            /* --start-menu-width: 500px; */ /* Removed */
            /* --start-menu-height: 600px; */ /* Removed */

            /* Light Theme */
            --text-color: #1f1f1f;
            --text-color-light: #6b7280;
            --window-bg: rgba(242, 242, 242, 0.75);
            --taskbar-bg: rgba(242, 242, 242, 0.6);
            --start-menu-bg: rgba(225, 225, 225, 0.7); /* Keeping this var name for the search bg */
            --hover-bg: rgba(0, 0, 0, 0.08);
            --close-hover-bg: #e81123;
            --close-hover-text: white;
            --separator-bg: rgba(0,0,0,0.1);
            --accent-color: #0078d4;
            --accent-color-hover: #005a9e;

            --calc-btn-bg: rgba(255,255,255,0.2);
            --calc-btn-hover-bg: rgba(255,255,255,0.4);
            --calc-operator-bg: rgba(0,0,0,0.1);
            --calc-operator-hover-bg: rgba(0,0,0,0.2);
        }
        
        body.dark {
            /* Dark Theme */
            --text-color: #f3f4f6;
            --text-color-light: #9ca3af;
            --window-bg: rgba(32, 32, 32, 0.75);
            --taskbar-bg: rgba(32, 32, 32, 0.65);
            --start-menu-bg: rgba(42, 42, 42, 0.7);
            --hover-bg: rgba(255, 255, 255, 0.1);
            --separator-bg: rgba(255,255,255,0.1);
            
            --calc-btn-bg: rgba(255,255,255,0.1);
            --calc-btn-hover-bg: rgba(255,255,255,0.2);
            --calc-operator-bg: rgba(0,0,0,0.2);
            --calc-operator-hover-bg: rgba(0,0,0,0.3);
        }

        body {
            font-family: 'Product Sans', sans-serif;
            background-image: url('PASTE_YOUR_BASE64_ENCODED_IMAGE_HERE_IF_YOU_WANT_A_WALLPAPER)');
            background-size: cover;
            background-position: center;
            overflow: hidden;
            color: var(--text-color);
        }

        /* Mica/Acrylic Blur Effect */
        .blur-backdrop {
            backdrop-filter: blur(24px) saturate(180%);
            -webkit-backdrop-filter: blur(24px) saturate(180%);
        }

        .window-bg {
             background-color: var(--window-bg);
        }

        .taskbar-bg {
            background-color: var(--taskbar-bg);
        }
        
        .start-menu-bg {
             background-color: var(--start-menu-bg);
        }

        /* Window Styling */
        .window {
            min-width: 300px;
            min-height: 200px;
            box-shadow: 0 8px 32px 0 rgba(0, 0, 0, 0.2);
            border: 1px solid rgba(255, 255, 255, 0.18);
            
            transition: opacity 0.2s, transform 0.2s, border-radius 0.2s ease;
                        
            position: absolute;
            display: flex;
            flex-direction: column;
            transform: scale(1);
            opacity: 1;
            resize: both;
            overflow: auto;
        }

        .window.transitioning {
            transition: opacity 0.2s, transform 0.2s, border-radius 0.2s ease,
                        width 0.25s cubic-bezier(0.25, 1, 0.5, 1), 
                        height 0.25s cubic-bezier(0.25, 1, 0.5, 1), 
                        top 0.25s cubic-bezier(0.25, 1, 0.5, 1), 
                        left 0.25s cubic-bezier(0.25, 1, 0.5, 1);
        }
        
        .window.opening {
             transform: scale(0.95);
             opacity: 0;
        }

        .window.closing {
            animation: windowCloseAnimation 0.2s cubic-bezier(0.25, 1, 0.5, 1) forwards;
        }

        .window.maximized {
            left: 0 !important;
            top: 0 !important;
            width: 100% !important;
            height: calc(100% - var(--taskbar-height)) !important;
            border-radius: 0;
            box-shadow: none;
            resize: none; 
        }

        .window.minimized {
            transform: translateY(100px) scale(0.9);
            opacity: 0;
            pointer-events: none;
        }

        .title-bar {
            height: 36px;
            flex-shrink: 0;
            cursor: move;
        }

        .title-bar-controls button {
            width: 40px;
            height: 36px;
            transition: background-color 0.15s ease;
            display: flex;
            justify-content: center;
            align-items: center;
        }

        .title-bar-controls button:hover {
            background-color: var(--hover-bg);
        }
        
        .title-bar-controls button.close-btn:hover {
            background-color: var(--close-hover-bg);
            color: var(--close-hover-text);
        }

        .content-area {
            flex-grow: 1;
            overflow: hidden; 
        }
        
        /* Taskbar */
        #taskbar {
            height: var(--taskbar-height);
        }
        .taskbar-item::after {
            content: '';
            position: absolute;
            bottom: 2px;
            left: 50%;
            transform: translateX(-50%);
            width: 0;
            height: 3px;
            background-color: var(--accent-color);
            border-radius: 2px;
            transition: width 0.2s cubic-bezier(0.25, 1, 0.5, 1);
        }
        .taskbar-item.active::after {
            width: 20px;
        }
        
        /* New Spotlight Icon Style */
        .spotlight-icon {
            width: 18px;
            height: 18px;
            background-color: var(--text-color);
            border-radius: 50%;
        }

        /* --- New Search Menu Styles --- */
        #search-menu {
            position: fixed;
            width: 600px;
            max-width: 90vw;
            top: 20vh; /* Position from top */
            left: 50%;
            transform: translateX(-50%) scale(0.95);
            max-height: 400px;
            display: flex;
            flex-direction: column;
            box-shadow: 0 8px 32px 0 rgba(0, 0, 0, 0.2);
            border: 1px solid rgba(255, 255, 255, 0.18);
            opacity: 0;
            visibility: hidden;
            transition: transform 0.2s cubic-bezier(0.25, 1, 0.5, 1), opacity 0.2s ease, visibility 0.2s;
            transform-origin: top center;
        }
        #search-menu.show {
            transform: translateX(-50%) scale(1);
            opacity: 1;
            visibility: visible;
        }
        
        #search-input {
            width: 100%;
            padding: 16px 20px;
            font-size: 1.25rem;
            background-color: transparent;
            border: none;
            outline: none;
            border-bottom: 1px solid var(--separator-bg);
            flex-shrink: 0;
            color: var(--text-color);
        }
        #search-input::placeholder {
            color: var(--text-color-light);
        }
        
        #search-results {
            flex-grow: 1;
            overflow-y: auto;
            padding: 8px;
        }

        .search-result-item {
            display: flex;
            align-items: center;
            gap: 12px;
            padding: 12px;
            cursor: pointer;
            transition: background-color 0.15s ease;
        }
        .search-result-item:hover {
            background-color: var(--hover-bg);
        }
        /* New 'selected' style for keyboard navigation */
        .search-result-item.selected {
            background-color: var(--accent-color);
            color: white;
        }
        .search-result-item.selected .search-result-path {
            color: rgba(255, 255, 255, 0.7);
        }

        .search-result-icon {
            font-size: 1.5rem;
            width: 32px;
            text-align: center;
        }
        .search-result-details {
            display: flex;
            flex-direction: column;
            overflow: hidden;
        }
        .search-result-title {
            font-weight: 500;
            white-space: nowrap;
            overflow: hidden;
            text-overflow: ellipsis;
        }
        .search-result-path {
            font-size: 0.75rem;
            color: var(--text-color-light);
            white-space: nowrap;
            overflow: hidden;
            text-overflow: ellipsis;
        }


        /* Clock Flyout */
        #clock-flyout {
            width: 360px;
            bottom: calc(var(--taskbar-height) + 8px);
            right: 16px; 
            transform: scale(0.95) translateY(10px);
            opacity: 0;
            visibility: hidden;
            transition: transform 0.2s cubic-bezier(0.25, 1, 0.5, 1), opacity 0.2s ease, visibility 0.2s;
            transform-origin: bottom right;
        }
        #clock-flyout.show {
            transform: scale(1) translateY(0);
            opacity: 1;
            visibility: visible;
        }
        .calendar-day-header {
            font-size: 0.75rem;
            font-weight: 600;
            opacity: 0.7;
        }
        .calendar-day {
            width: 40px;
            height: 40px;
            display: flex;
            align-items: center;
            justify-content: center;
            font-size: 0.875rem;
            cursor: pointer;
            transition: background-color 0.15s ease;
        }
        .calendar-day:not(.other-month):hover {
            background-color: var(--hover-bg);
        }
        .calendar-day.other-month {
            opacity: 0.3;
        }
        .calendar-day.today {
            background-color: var(--accent-color);
            color: white;
            border-radius: 50%;
        }

        /* Context Menu */
        #context-menu {
             background-color: rgba(248, 248, 248, 0.7);
             transition: opacity 0.15s cubic-bezier(0.25, 1, 0.5, 1), transform 0.15s cubic-bezier(0.25, 1, 0.5, 1);
             min-width: 200px;
             transform: scale(0.95);
             opacity: 0;
             pointer-events: none;
             display: none;
        }
        body.dark #context-menu {
            background-color: rgba(42, 42, 42, 0.7);
        }

        #context-menu.show {
            transform: scale(1);
            opacity: 1;
            pointer-events: auto;
            display: block;
        }
         .context-menu-item {
             padding: 8px 16px;
             cursor: pointer;
             font-size: 14px;
             transition: background-color 0.15s ease;
         }
         #context-menu.show .context-menu-item,
         #context-menu.show .context-menu-separator {
            animation: contextFadeIn 0.3s cubic-bezier(0.25, 1, 0.5, 1) forwards;
            opacity: 0;
            transform: translateX(-5px);
         }

         .context-menu-item:hover {
             background-color: var(--hover-bg);
         }
         .context-menu-separator {
            height: 1px;
            background-color: var(--separator-bg);
            margin: 4px 0;
         }
        
        /* Keyframe Animations */
        @keyframes windowCloseAnimation {
            to {
                opacity: 0;
                transform: scale(0.92);
            }
        }

        @keyframes contextFadeIn {
            to {
                opacity: 1;
                transform: translateX(0);
            }
        }

        @keyframes itemClick {
            0%, 100% { transform: scale(1); }
            50% { transform: scale(0.9); }
        }
        .item-click-anim {
            animation: itemClick 0.3s cubic-bezier(0.25, 1, 0.5, 1);
        }


        /* Scrollbar styling */
        ::-webkit-scrollbar { width: 8px; height: 8px; }
        ::-webkit-scrollbar-track { background: transparent; }
        ::-webkit-scrollbar-thumb { background: rgba(0,0,0,0.2); border-radius: 4px; }
        body.dark ::-webkit-scrollbar-thumb { background: rgba(255,255,255,0.2); }
        ::-webkit-scrollbar-thumb:hover { background: rgba(0,0,0,0.4); }
        body.dark ::-webkit-scrollbar-thumb:hover { background: rgba(255,255,255,0.4); }
        /* Scrollbar for Search Menu */
        #search-results::-webkit-scrollbar-thumb { background: rgba(0,0,0,0.3); }
        body.dark #search-results::-webkit-scrollbar-thumb { background: rgba(255,255,255,0.3); }


        /* App-specific Styles */
        .calculator-grid { display: grid; grid-template-columns: repeat(4, 1fr); gap: 1px; }
        .calc-display { /* background-color: rgba(0,0,0,0.1); */ border-bottom: 1px solid var(--separator-bg); word-wrap: break-word; word-break: break-all; }
        .calc-btn { background-color: var(--calc-btn-bg); transition: background-color .15s ease; }
        .calc-btn:hover { background-color: var(--calc-btn-hover-bg); }
        .calc-btn.operator { background-color: var(--calc-operator-bg); }
        .calc-btn.operator:hover { background-color: var(--calc-operator-hover-bg); }
        .calc-btn.equals { background-color: var(--accent-color); color: white; }
        .calc-btn.equals:hover { background-color: var(--accent-color-hover); }

        .file-explorer { display: flex; height: 100%; }
        .files-sidebar { width: 200px; flex-shrink: 0; background-color: rgba(0,0,0,0.05); }
        body.dark .files-sidebar { background-color: rgba(255,255,255,0.05); }
        .files-main { flex-grow: 1; display: flex; flex-direction: column; }
        .files-breadcrumbs { flex-shrink: 0; }
        .files-grid-container { flex-grow: 1; overflow-y: auto; }

        .colors-app { display: flex; height: 100%; }
        .colors-toolbar { width: 60px; flex-shrink: 0; /* background-color: rgba(0,0,0,0.05); */ border-right: 1px solid var(--separator-bg); }
        body.dark .colors-toolbar { /* background-color: rgba(255,255,255,0.05); */ }
        .color-swatch { width: 24px; height: 24px; border-radius: 50%; cursor: pointer; border: 2px solid transparent; }
        .color-swatch.active { border-color: var(--accent-color); }
        .colors-canvas { 
            cursor: crosshair;
            min-width: 0;
        }

        .images-app { display: flex; justify-content: center; align-items: center; height: 100%; /* padding: 1rem; */ }
        .images-app img { max-width: 100%; max-height: 100%; object-fit: contain; }

        .zdeupload-app { padding: 2rem; text-align: center; }

        /* Minesweeper Styles */
        .minesweeper-app { display: flex; flex-direction: column; height: 100%; }
        .minesweeper-info {
            flex-shrink: 0;
            display: flex;
            justify-content: space-between;
            align-items: center;
            padding: 8px;
            /* background-color: rgba(0,0,0,0.05); */ /* <-- Removed this */
            border-bottom: 1px solid var(--separator-bg); /* <-- Added this for separation */
        }
        /* body.dark .minesweeper-info { background-color: rgba(255,255,255,0.05); } */ /* <-- Removed this */
        .minesweeper-info-box {
            font-family: 'Courier New', Courier, monospace;
            font-size: 1.25rem;
            font-weight: bold;
            padding: 4px 8px;
            background-color: rgba(0,0,0,0.1);
            min-width: 60px;
            text-align: center;
        }
        .minesweeper-reset-btn {
            width: 32px;
            height: 32px;
            font-size: 1.5rem;
            display: flex;
            align-items: center;
            justify-content: center;
            border: 2px solid transparent;
            background-color: var(--calc-btn-bg);
            cursor: pointer;
        }
        .minesweeper-reset-btn:hover { background-color: var(--calc-btn-hover-bg); }
        .minesweeper-grid {
            flex-grow: 1;
            display: grid;
            grid-template-columns: repeat(9, 1fr);
            grid-template-rows: repeat(9, 1fr);
            gap: 1px;
            background-color: var(--separator-bg);
            padding: 1px;
            overflow: hidden; /* Prevent weird resizing artifacts */
        }
        .minesweeper-cell {
            background-color: var(--calc-btn-bg);
            transition: background-color 0.15s ease;
            display: flex;
            align-items: center;
            justify-content: center;
            font-size: 0.875rem; /* 14px */
            font-weight: bold;
            cursor: pointer;
            user-select: none;
        }
        .minesweeper-cell:hover {
            background-color: var(--calc-btn-hover-bg);
        }
        .minesweeper-cell.revealed {
            background-color: rgba(0,0,0,0.05);
            cursor: default;
        }
        body.dark .minesweeper-cell.revealed {
            background-color: rgba(255,255,255,0.05);
        }
        .minesweeper-cell.mine {
            background-color: #e81123;
        }
        .minesweeper-cell[data-adjacent="1"] { color: #0078d4; }
        .minesweeper-cell[data-adjacent="2"] { color: #38a169; }
        .minesweeper-cell[data-adjacent="3"] { color: #e53e3e; }
        .minesweeper-cell[data-adjacent="4"] { color: #003a6e; }
        .minesweeper-cell[data-adjacent="5"] { color: #a13800; }
        .minesweeper-cell[data-adjacent="6"] { color: #3182ce; }
        .minesweeper-cell[data-adjacent="7"] { color: #000000; }
        .minesweeper-cell[data-adjacent="8"] { color: #6b7280; }


        /* Modal Dialog */
        #modal-backdrop { 
            background-color: rgba(0,0,0,0.5); 
            transition: opacity 0.2s ease;
            opacity: 0;
        }
        #modal-backdrop.show {
            opacity: 1;
        }
        #modal-box {
            transition: transform 0.2s cubic-bezier(0.25, 1, 0.5, 1), opacity 0.2s ease;
            transform: scale(0.95);
            opacity: 0;
        }
        #modal-backdrop.show #modal-box {
            transform: scale(1);
            opacity: 1;
        }

        .btn-default { 
            color: var(--text-color); 
            background-color: rgba(0,0,0,0.1); 
            transition: background-color 0.15s ease;
        }
        .btn-default:hover {
            background-color: rgba(0,0,0,0.2);
        }
        body.dark .btn-default {
            background-color: rgba(255,255,255,0.1); 
        }
        body.dark .btn-default:hover {
            background-color: rgba(255,255,255,0.2);
        }
        .btn-accent { background-color: var(--accent-color); color: white; }
        .btn-accent:hover { background-color: var(--accent-color-hover); }

        .btn-danger { background-color: #e81123; color: white; transition: background-color 0.15s ease; }
        .btn-danger:hover { background-color: #c50f1f; }

        /* Override all rounded corners */
        .rounded:not(.toggle-checkbox):not(.toggle-label):not(.color-swatch):not(.files-grid button):not(.calendar-day), 
        .rounded-lg:not(.toggle-checkbox):not(.toggle-label):not(.color-swatch), 
        .rounded-md:not(.toggle-checkbox):not(.toggle-label):not(.color-swatch), 
        .rounded-sm:not(.toggle-checkbox):not(.toggle-label):not(.color-swatch), 
        .rounded-full:not(.toggle-checkbox):not(.toggle-label):not(.color-swatch):not(.calendar-day),
        #search-menu, /* Added search menu */
        #context-menu, #modal-box, .window, #clock-flyout {
            border-radius: 0 !important;
        }
        .title-bar, .content-area {
            border-radius: 0 !important;
        }
        /* Allow rounded corners for file items */
        .files-grid button {
            border-radius: 4px !important;
        }
        .calendar-day.today {
            border-radius: 50% !important; /* Allow today's date to be round */
        }

    </style>
</head>
<body class="select-none">

    <main id="desktop" class="w-screen h-screen"></main>

    <div id="context-menu" class="fixed blur-backdrop shadow-lg p-1 z-[5000]"></div>
    
    <div id="modal-backdrop" class="fixed inset-0 z-[4000] hidden items-center justify-center">
        <div id="modal-box" class="blur-backdrop window-bg shadow-xl p-6 w-full max-w-sm">
            <h3 id="modal-title" class="text-lg font-bold mb-4">Modal Title</h3>
            <div id="modal-content" class="mb-6"></div>
            <div id="modal-buttons" class="flex justify-end gap-2"></div>
        </div>
    </div>

    <!-- Clock Flyout -->
    <div id="clock-flyout" class="fixed blur-backdrop start-menu-bg shadow-2xl p-6 z-[3000]">
        <div class="flyout-clock-time text-4xl font-light"></div>
        <div class="flyout-clock-date text-lg mb-4"></div>
        <div class="calendar-grid grid grid-cols-7 gap-1 text-center"></div>
    </div>

    <!-- --- New Search Menu HTML --- -->
    <div id="search-menu" class="fixed blur-backdrop start-menu-bg shadow-2xl z-[3000]">
        <input type="text" id="search-input" placeholder="Search apps and files...">
        <div id="search-results">
            <!-- Search results will be dynamically generated here -->
        </div>
    </div>


    <footer id="taskbar" class="fixed bottom-0 left-0 right-0 blur-backdrop taskbar-bg flex justify-start px-4 z-[2000]">
        <div class="flex items-center gap-2">
            <button id="start-btn" class="taskbar-item p-3 hover:bg-black/10 dark:hover:bg-white/10 transition-colors flex items-center justify-center">
                <!-- New Spotlight Icon -->
                <span class="spotlight-icon"></span>
            </button>
            
            <div id="pinned-apps" class="flex items-center gap-2">
                 </div>
             <div class="h-6 w-px bg-black/10 dark:bg-white/20 mx-1"></div>
            <div id="taskbar-apps" class="flex items-center gap-2"></div>
        </div>

        <!-- Clock Container -->
        <div id="clock-container" class="absolute right-0 top-0 h-full flex items-center px-4 hover:bg-black/10 dark:hover:bg-white/10 transition-colors cursor-pointer">
            <div id="clock" class="text-sm text-center"></div>
        </div>
    </footer>


    <script>
        document.addEventListener('DOMContentLoaded', () => {
            const desktop = document.getElementById('desktop');
            const taskbarApps = document.getElementById('taskbar-apps');
            const pinnedAppsContainer = document.getElementById('pinned-apps');
            const startBtn = document.getElementById('start-btn');
            
            // --- New Search Menu Elements ---
            const searchMenu = document.getElementById('search-menu');
            const searchInput = document.getElementById('search-input');
            const searchResults = document.getElementById('search-results');
            
            const clock = document.getElementById('clock');
            const contextMenu = document.getElementById('context-menu');
            const modalBackdrop = document.getElementById('modal-backdrop');

            // --- Clock Flyout Elements ---
            const clockContainer = document.getElementById('clock-container');
            const clockFlyout = document.getElementById('clock-flyout');
            const flyoutTime = clockFlyout.querySelector('.flyout-clock-time');
            const flyoutDate = clockFlyout.querySelector('.flyout-clock-date');
            const calendarGrid = clockFlyout.querySelector('.calendar-grid');
            
            let openWindows = {};
            let zIndexCounter = 100;
            let windowCounter = 0;
            let currentCalculation = '0';
            let virtualClipboard = ''; // Our new virtual clipboard
            let pinnedApps = new Set(); // <-- CHANGED: Removed default pinned apps
            
            let searchSelectionIndex = 0; // For search keyboard navigation

            // --- VFS (Virtual File System) ---
            const VFS_STORAGE_KEY = 'polarisVfs';
            // const HIDDEN_APPS_KEY = 'polarisHiddenApps'; // Removed
            
            // --- Removed Tile Layout Storage ---

            const defaultFileSystem = {
                'type': 'directory', 'children': {
                    'home': { 'type': 'directory', 'children': {
                            'user': { 'type': 'directory', 'children': {
                                    'Applications': { 'type': 'directory', 'children': {} }, // Added Applications dir
                                    'Documents': { 'type': 'directory', 'children': { 'report.docx': { 'type': 'file', 'content': 'This is a simulated Word document.' } } },
                                    'Downloads': { 'type': 'directory', 'children': {} },
                                    'ver.txt': { 'type': 'file', 'content': 'Last updated 11-17-25\nCore OS Polaris\nWeb UI 0.21.5\n\nThis project is at extremely early-stage!' }
                                }
                            }
                        }
                    },
                    'etc': { 'type': 'directory', 'children': { 'config.conf': { 'type': 'file', 'content': 'setting=true' } } },
                }
            };
            
            let fileSystem; // This will be our live, mutable VFS object

            function populateDefaultApps() {
                const appsDir = getFileSystemNode('/home/user/Applications');
                if (!appsDir || appsDir.type !== 'directory') {
                    console.error("Critical: /home/user/Applications not found.");
                    return;
                }
                
                Object.keys(appConfig).forEach(appId => {
                    // Don't create .app files for system apps
                    if (appId === 'preferences' || appId === 'files' || appId === 'search') return; // <-- ADDED 'search'
                    
                    const appFileName = `${appConfig[appId].title}.app`;
                    if (!appsDir.children[appFileName]) {
                        appsDir.children[appFileName] = {
                            type: 'app', // Special type
                            content: appId // Links to the appConfig key
                        };
                    }
                });
            }

            function saveVFSToLocalStorage() {
                try {
                    localStorage.setItem(VFS_STORAGE_KEY, JSON.stringify(fileSystem));
                } catch (e) {
                    console.error("Failed to save VFS to localStorage:", e);
                    showModal("Save Error", "Could not save file system changes. Storage might be full.", [{label: 'OK'}]);
                }
            }

            function initializeVFS() {
                const savedVfs = localStorage.getItem(VFS_STORAGE_KEY);
                if (savedVfs) {
                    try {
                        fileSystem = JSON.parse(savedVfs);
                        if (!fileSystem.type || !fileSystem.children) {
                           throw new Error("Invalid VFS structure.");
                        }
                    } catch (e) {
                        console.error("Error parsing VFS from localStorage, resetting:", e);
                        fileSystem = JSON.parse(JSON.stringify(defaultFileSystem)); // Deep copy
                    }
                } else {
                    fileSystem = JSON.parse(JSON.stringify(defaultFileSystem)); // Deep copy
                }
                
                // VFS Migration: Ensure /home/user/Applications exists
                let homeUser = getFileSystemNode('/home/user');
                if (homeUser && !homeUser.children['Applications']) {
                    console.log("Migrating VFS: Adding /Applications directory.");
                    homeUser.children['Applications'] = { type: 'directory', children: {} };
                }
                
                // --- VFS Cleanup: Remove orphaned SearchExperience.app ---
                let appsDir = getFileSystemNode('/home/user/Applications');
                if (appsDir && appsDir.children['SearchExperience.app']) {
                    console.log("VFS Cleanup: Removing old SearchExperience.app");
                    delete appsDir.children['SearchExperience.app'];
                }

                // Populate .app files
                populateDefaultApps();
                
                // Save any migrations or additions
                saveVFSToLocalStorage(); 
            }
            
            // --- Removed Hidden App functions ---
            
            const appConfig = {
                search: { // Renamed from startMenu
                    title: "SearchExperience", emoji: "🔍", multiInstance: false, content: null // Not a window
                },
                preferences: {
                    title: "Preferences", emoji: "⚙️", multiInstance: false,
                    content: `<div class="p-8">
                        <h1 class="text-2xl font-bold mb-4">Preferences</h1>
                        <div class="flex items-center justify-between py-2">
                            <span>Dark Mode</span>
                            <label id="dark-mode-toggle" class="relative inline-flex items-center cursor-pointer">
                                <input type="checkbox" id="dark-mode-checkbox" class="sr-only peer toggle-checkbox">
                                <div class="toggle-label w-10 h-5 bg-gray-300 rounded-full peer dark:bg-gray-600 peer-checked:after:translate-x-[20px] after:content-[''] after:absolute after:top-[2px] after:left-[2px] after:bg-white after:rounded-full after:h-4 after:w-4 after:transition-all peer-checked:bg-blue-600"></div>
                            </label>
                        </div>
                        <div class="flex items-center justify-between py-4 mt-4 border-t border-black/10 dark:border-white/10">
                            <div class="flex flex-col">
                                <span>Reset File System</span>
                                <span class="text-xs text-gray-500">Resets files and restores all apps.</span>
                            </div>
                            <button id="reset-vfs-btn" class="px-3 py-1 text-sm btn-default">Reset</button>
                        </div>
                    </div>`, width: '500px', height: '400px'
                },
                texts: {
                    title: "Texts", emoji: "📝", multiInstance: true,
                    content: `<div class="w-full h-full flex flex-col">
                        <textarea class="texts-textarea w-full h-full p-2 border-0 resize-none focus:outline-none bg-transparent font-mono text-sm"></textarea>
                    </div>`, width: '600px', height: '400px'
                },
                numbers: {
                    title: "Numbers", emoji: "🧮", multiInstance: true,
                    content: `<div class="flex flex-col h-full">
                        <div id="calc-display" class="calc-display text-4xl text-right p-4 flex items-end justify-end font-light">0</div>
                        <div class="calculator-grid flex-shrink-0 flex-grow">
                            <button onclick="clearCalc()" class="calc-btn operator text-xl">AC</button>
                            <button onclick="deleteLastChar()" class="calc-btn operator text-xl">DEL</button>
                            <button onclick="handleCalcInput('%')" class="calc-btn operator text-xl">%</button>
                            <button onclick="handleCalcInput('/')" class="calc-btn operator text-xl">÷</button>
                            <button onclick="handleCalcInput('7')" class="calc-btn text-xl">7</button>
                            <button onclick="handleCalcInput('8')" class="calc-btn text-xl">8</button>
                            <button onclick="handleCalcInput('9')" class="calc-btn text-xl">9</button>
                            <button onclick="handleCalcInput('*')" class="calc-btn operator text-xl">×</button>
                            <button onclick="handleCalcInput('4')" class="calc-btn text-xl">4</button>
                            <button onclick="handleCalcInput('5')" class="calc-btn text-xl">5</button>
                            <button onclick="handleCalcInput('6')" class="calc-btn text-xl">6</button>
                            <button onclick="handleCalcInput('-')" class="calc-btn operator text-xl">-</button>
                            <button onclick="handleCalcInput('1')" class="calc-btn text-xl">1</button>
                            <button onclick="handleCalcInput('2')" class="calc-btn text-xl">2</button>
                            <button onclick="handleCalcInput('3')" class="calc-btn text-xl">3</button>
                            <button onclick="handleCalcInput('+')" class="calc-btn operator text-xl">+</button>
                            <button onclick="handleCalcInput('0')" class="calc-btn text-xl col-span-2">0</button>
                            <button onclick="handleCalcInput('.')" class="calc-btn text-xl">.</button>
                            <button onclick="calculateResult()" class="calc-btn equals text-xl">=</button>
                        </div>
                    </div>`, width: '320px', height: '480px'
                },
                files: {
                    title: "Files", emoji: "📁", multiInstance: true,
                    content: `<div class="file-explorer">
                        <div class="files-sidebar p-2">
                           <button data-path="/home/user" class="w-full text-left p-2 hover:bg-black/10 dark:hover:bg-white/10">Home</button>
                           <button data-path="/home/user/Applications" class="w-full text-left p-2 hover:bg-black/10 dark:hover:bg-white/10">Applications</button>
                           <button data-path="/home/user/Documents" class="w-full text-left p-2 hover:bg-black/10 dark:hover:bg-white/10">Documents</button>
                           <button data-path="/home/user/Downloads" class="w-full text-left p-2 hover:bg-black/10 dark:hover:bg-white/10">Downloads</button>
                        </div>
                        <div class="files-main">
                            <div class="files-breadcrumbs p-2 border-b border-black/10 dark:border-white/10">/</div>
                            <div class="files-grid-container p-4">
                               <div class="files-grid grid grid-cols-4 gap-4"></div>
                            </div>
                        </div>
                    </div>`, width: '700px', height: '500px'
                },
                colors: {
                    title: "Colors", emoji: "🎨", multiInstance: true,
                    content: `<div class="colors-app">
                        <div class="colors-toolbar p-2 flex flex-col items-center gap-2">
                            <button data-color="#000000" class="color-swatch active" style="background-color: #000000;"></button>
                            <button data-color="#e53e3e" class="color-swatch" style="background-color: #e53e3e;"></button>
                            <button data-color="#38a169" class="color-swatch" style="background-color: #38a169;"></button>
                            <button data-color="#3182ce" class="color-swatch" style="background-color: #3182ce;"></button>
                            <button data-color="#f6e05e" class="color-swatch" style="background-color: #f6e05e;"></button>
                            <button class="mt-auto p-2 hover:bg-black/10 dark:hover:bg-white/10" onclick="clearCanvas(this)">Clear</button>
                        </div>
                        <canvas class="colors-canvas flex-grow bg-white"></canvas>
                    </div>`, width: '600px', height: '400px'
                },
                images: {
                    title: "Images", emoji: "🖼️", multiInstance: true,
                    content: `<div class="images-app"><img src="" alt="Image view"></div>`,
                    width: '600px', height: '400px'
                },
                zdeupload: {
                    title: "zDevUpload", emoji: "📤", multiInstance: false,
                    content: `<div class="zdeupload-app flex flex-col items-center justify-center h-full">
                        <h2 class="text-xl font-semibold mb-4">Upload a File</h2>
                        <p class="text-sm text-gray-500 mb-4">Upload .txt, .png, .jpg, or .gif files to the virtual file system.</p>
                        <input type="file" id="file-uploader" class="block w-full text-sm text-slate-500 file:mr-4 file:py-2 file:px-4 file:border-0 file:text-sm file:font-semibold file:bg-violet-50 file:text-violet-700 hover:file:bg-violet-100"/>
                    </div>`, width: '400px', height: '250px'
                },
                minesweeper: {
                    title: "Minesweeper", emoji: "💣", multiInstance: true,
                    content: `<div class="minesweeper-app">
                        <div class="minesweeper-info">
                            <div id="mine-count" class="minesweeper-info-box">10</div>
                            <button id="reset-game" class="minesweeper-reset-btn">🙂</button>
                            <div id="timer" class="minesweeper-info-box">0</div>
                        </div>
                        <div id="minesweeper-grid" class="minesweeper-grid"></div>
                    </div>`, width: '400px', height: '460px'
                }
            };

            function animateClick(element) {
                if (!element) return;
                element.classList.add('item-click-anim');
                element.addEventListener('animationend', () => {
                    element.classList.remove('item-click-anim');
                }, { once: true });
            }

            // --- Clock ---
            function updateClock() {
                const now = new Date();
                // Taskbar Clock
                const time = now.toLocaleTimeString([], { hour: '2-digit', minute: '2-digit' });
                const date = now.toLocaleDateString([], { month: 'numeric', day: 'numeric', year: '2-digit' });
                clock.innerHTML = `${time}<br>${date}`;
                
                // Flyout Clock
                if (clockFlyout.classList.contains('show')) {
                    const flyoutTimeStr = now.toLocaleTimeString('en-US', { hour: 'numeric', minute: '2-digit', second: '2-digit' });
                    const flyoutDateStr = now.toLocaleDateString('en-US', { weekday: 'long', month: 'long', day: 'numeric' });
                    flyoutTime.textContent = flyoutTimeStr;
                    flyoutDate.textContent = flyoutDateStr;
                }
            }
            setInterval(updateClock, 1000);
            updateClock();

            function renderCalendar(year, month) {
                calendarGrid.innerHTML = '';
                const today = new Date();
                today.setHours(0,0,0,0);
                
                const firstDayOfMonth = new Date(year, month, 1);
                const daysInMonth = new Date(year, month + 1, 0).getDate();
                const startDayOfWeek = firstDayOfMonth.getDay(); // 0 (Sun) - 6 (Sat)
                
                // Add day headers (S, M, T, W, T, F, S)
                const dayNames = ['S', 'M', 'T', 'W', 'T', 'F', 'S'];
                dayNames.forEach(day => {
                    const header = document.createElement('div');
                    header.className = 'calendar-day-header';
                    header.textContent = day;
                    calendarGrid.appendChild(header);
                });
                
                // Get days from previous month
                const daysInPrevMonth = new Date(year, month, 0).getDate();
                for (let i = 0; i < startDayOfWeek; i++) {
                    const day = document.createElement('div');
                    day.className = 'calendar-day other-month';
                    day.textContent = daysInPrevMonth - startDayOfWeek + i + 1;
                    calendarGrid.appendChild(day);
                }
                
                // Add days for current month
                for (let i = 1; i <= daysInMonth; i++) {
                    const day = document.createElement('div');
                    day.className = 'calendar-day';
                    day.textContent = i;
                    
                    const currentDate = new Date(year, month, i);
                    if (currentDate.getTime() === today.getTime()) {
                        day.classList.add('today');
                    }
                    calendarGrid.appendChild(day);
                }
                
                // Add days for next month
                const totalCells = 42; // 6 rows * 7 days
                const cellsFilled = startDayOfWeek + daysInMonth;
                const remainingCells = totalCells - cellsFilled;
                
                for (let i = 1; i <= remainingCells; i++) {
                    const day = document.createElement('div');
                    day.className = 'calendar-day other-month';
                    day.textContent = i;
                    calendarGrid.appendChild(day);
                }
            }
            
            // --- New Search Menu Logic ---
            startBtn.addEventListener('click', (e) => {
                e.stopPropagation();
                animateClick(e.currentTarget);
                
                // --- VFS CHECK ---
                // No longer check VFS for 'search', it's a system app
                // if (!checkAppVfsStatus('search')) { ... } // Removed
                // --- END VFS CHECK ---
                
                const isHidden = !searchMenu.classList.contains('show');
                searchMenu.classList.toggle('show');
                clockFlyout.classList.remove('show'); // Close clock flyout
                
                if (isHidden) {
                    // FIX: Add a short delay to allow the animation to complete
                    setTimeout(() => searchInput.focus(), 200); 
                    performSearch(''); // Show initial state
                } else {
                    searchInput.value = ''; // Clear search on close
                }
            });

            searchInput.addEventListener('input', (e) => performSearch(e.target.value));
            searchInput.addEventListener('keydown', handleSearchKeydown); // Added keydown listener
            
            function handleSearchKeydown(e) {
                const items = searchResults.querySelectorAll('.search-result-item');
                if (items.length === 0) return;

                switch (e.key) {
                    case 'ArrowDown':
                    // case 'Tab': // Allow Tab to navigate down
                        e.preventDefault();
                        searchSelectionIndex = (searchSelectionIndex + 1) % items.length;
                        updateSearchSelection(items);
                        break;
                    case 'ArrowUp':
                        e.preventDefault();
                        searchSelectionIndex = (searchSelectionIndex - 1 + items.length) % items.length;
                        updateSearchSelection(items);
                        break;
                    case 'Enter':
                        e.preventDefault();
                        const selectedItem = items[searchSelectionIndex];
                        if (selectedItem) {
                            selectedItem.click(); // Trigger the click handler
                        }
                        break;
                }
            }

            function updateSearchSelection(items) {
                items.forEach((item, index) => {
                    if (index === searchSelectionIndex) {
                        item.classList.add('selected');
                        // Scroll item into view
                        item.scrollIntoView({ block: 'nearest' });
                    } else {
                        item.classList.remove('selected');
                    }
                });
            }
            
            function performSearch(query) {
                searchResults.innerHTML = '';
                searchSelectionIndex = 0; // Reset selection index
                const lowerQuery = query.toLowerCase();
                let resultCount = 0;

                // Search Apps
                Object.keys(appConfig).forEach(appId => {
                    if (appId === 'search') return; // <-- BUG FIX: This line was commented out
                    
                    const config = appConfig[appId];
                    if (config.title.toLowerCase().includes(lowerQuery)) {
                        const item = document.createElement('div');
                        item.className = 'search-result-item';
                        item.innerHTML = `
                            <span class="search-result-icon">${config.emoji}</span>
                            <div class="search-result-details">
                                <span class="search-result-title">${config.title}</span>
                                <span class="search-result-path">Application</span>
                            </div>
                        `;
                        item.onclick = () => {
                            createWindow(appId);
                            searchMenu.classList.remove('show');
                            searchInput.value = '';
                        };
                        searchResults.appendChild(item);
                        resultCount++;
                    }
                });

                // Search Files
                function searchVFS(node, path) {
                    if (!node || !node.children) return;

                    Object.keys(node.children).forEach(name => {
                        const childNode = node.children[name];
                        const fullPath = `${path === '/' ? '' : path}/${name}`;
                        
                        // --- BUG FIX: Don't show SearchExperience.app in results ---
                        if (name === 'SearchExperience.app') return;

                        if (name.toLowerCase().includes(lowerQuery) && childNode.type !== 'app') {
                            let icon = '📄';
                            if (childNode.type === 'directory') icon = '📁';
                            else if (['.png', '.jpg', '.jpeg', '.gif'].some(ext => name.endsWith(ext))) icon = '🖼️';

                            const item = document.createElement('div');
                            item.className = 'search-result-item';
                            item.innerHTML = `
                                <span class="search-result-icon">${icon}</span>
                                <div class="search-result-details">
                                    <span class="search-result-title">${name}</span>
                                    <span class="search-result-path">${path}</span>
                                </div>
                            `;
                            
                            item.onclick = () => {
                                if (childNode.type === 'directory') {
                                    const filesWin = createWindow('files');
                                    // Wait a moment for window to be created, then render path
                                    setTimeout(() => renderFileSystem(filesWin, fullPath), 50);
                                } else if (name.endsWith('.txt') || name.endsWith('.conf')) {
                                    openFileInTexts(fullPath);
                                } else if (['.png', '.jpg', '.jpeg', '.gif'].some(ext => name.endsWith(ext))) {
                                    openImageInViewer(fullPath);
                                } else {
                                    // --- NEW UNKNOWN FILE HANDLER ---
                                    showModal('Open with...', `How do you want to open "${name}"?`, [
                                        { label: 'Cancel' },
                                        { label: 'Images', class: 'btn-accent', action: () => openImageInViewer(fullPath) },
                                        { label: 'Texts', class: 'btn-accent', action: () => openFileInTexts(fullPath) }
                                    ]);
                                }
                                searchMenu.classList.remove('show');
                                searchInput.value = '';
                            };
                            searchResults.appendChild(item);
                            resultCount++;
                        }

                        if (childNode.type === 'directory') {
                            searchVFS(childNode, fullPath);
                        }
                    });
                }
                
                if (lowerQuery.length > 0) { // Only search VFS if there's a query
                    searchVFS(getFileSystemNode('/'), '/');
                }

                if (resultCount === 0 && lowerQuery.length > 0) {
                    searchResults.innerHTML = `<div class="p-4 text-center text-gray-500">No results found for "${query}"</div>`;
                } else if (resultCount === 0 && lowerQuery.length === 0) {
                    searchResults.innerHTML = `<div class="p-4 text-center text-gray-500">Type to search for apps and files.</div>`;
                }
                
                // Auto-select the first item
                const items = searchResults.querySelectorAll('.search-result-item');
                if (items.length > 0) {
                    items[0].classList.add('selected');
                }
            }


            // --- Clock Flyout ---
            clockContainer.addEventListener('click', (e) => {
                e.stopPropagation();
                animateClick(e.currentTarget);
                
                // Update calendar and clock *before* showing
                if (!clockFlyout.classList.contains('show')) {
                    const now = new Date();
                    renderCalendar(now.getFullYear(), now.getMonth());
                    updateClock(); // Force update clocks
                }
                
                clockFlyout.classList.toggle('show');
                searchMenu.classList.remove('show'); // Close search menu
                searchInput.value = '';
            });
            
            // --- Global Click Handler & Dark Mode Toggle ---
            function handleGlobalKeydown(e) {
                // Don't trigger if typing in an input or search is already open
                if (e.target.tagName === 'INPUT' || e.target.tagName === 'TEXTAREA') return;
                
                const isSearchOpen = searchMenu.classList.contains('show');
                const isModalOpen = modalBackdrop.classList.contains('show');

                if (e.key === ' ' && !isSearchOpen && !isModalOpen) {
                    const hasOpenWindows = Object.values(openWindows).some(w => !w.minimized);
                    if (!hasOpenWindows) {
                        e.preventDefault(); // Prevent space from scrolling
                        startBtn.click(); // Open search
                    }
                }
            }
            
            document.body.addEventListener('click', (e) => {
                if (!searchMenu.contains(e.target) && !startBtn.contains(e.target)) {
                    searchMenu.classList.remove('show');
                    searchInput.value = '';
                }
                if (!clockFlyout.contains(e.target) && !clockContainer.contains(e.target)) {
                    clockFlyout.classList.remove('show');
                }
                
                contextMenu.classList.remove('show');
                
                if (e.target.closest('#dark-mode-toggle')) {
                    const checkbox = document.getElementById('dark-mode-checkbox');
                    if (checkbox) {
                        if (e.target.id === 'dark-mode-checkbox') {
                             toggleDarkMode();
                        } else {
                            setTimeout(() => toggleDarkMode(checkbox.checked), 0);
                        }
                    }
                }
            });
            
            document.body.addEventListener('keydown', handleGlobalKeydown); // Add global key listener

            function toggleDarkMode(forceState) {
                const isDarkMode = typeof forceState === 'boolean' ? forceState : document.body.classList.toggle('dark');
                if (typeof forceState === 'boolean') {
                    document.body.classList.toggle('dark', isDarkMode);
                }
                
                localStorage.setItem('theme', isDarkMode ? 'dark' : 'light');
                document.querySelectorAll('#dark-mode-checkbox').forEach(cb => cb.checked = isDarkMode);
            }

            function applyInitialTheme() {
                const savedTheme = localStorage.getItem('theme');
                if (savedTheme === 'dark') {
                    document.body.classList.add('dark');
                }
            }
            
            // --- Context Menu ---
            document.addEventListener('contextmenu', (e) => {
                e.preventDefault();
                const target = e.target;
                const menuItems = getContextMenuItems(target);
                if(menuItems.length > 0) {
                    showContextMenu(e.clientX, e.clientY, menuItems);
                }
            });

            function getContextMenuItems(target) {
                // Removed Start Menu context logic
                
                const pinnedApp = target.closest('#pinned-apps .app-launcher');
                if (pinnedApp) {
                    const appId = pinnedApp.dataset.appId;
                    const config = appConfig[appId];
                    return [
                        { label: 'Open', action: () => createWindow(appId) },
                        { label: `Unpin ${config.title} from taskbar`, action: () => unPinApp(appId) }
                    ];
                }
                
                const taskbarApp = target.closest('#taskbar-apps .taskbar-item');
                if(taskbarApp) {
                    const instanceId = taskbarApp.dataset.instanceId;
                    const winData = openWindows[instanceId];
                    if (!winData) return [];
                    
                    const runningInstances = Object.values(openWindows).filter(w => w.appId === winData.appId);
                    
                    const items = [
                        { label: winData.minimized ? 'Restore' : 'Minimize', action: () => {
                            if (winData.minimized) focusWindow(winData.element);
                            else minimizeWindow(winData.element);
                        }},
                        { label: 'Close', action: () => closeWindow(winData.element) }
                    ];
                    
                    if (runningInstances.length > 1) {
                         items.push({ label: `Close all ${runningInstances.length} instances`, action: () => {
                            runningInstances.forEach(inst => closeWindow(inst.element, true));
                         }});
                    }
                    return items;
                }
                
                const windowTarget = target.closest('.window');
                if (windowTarget && !windowTarget.dataset.appId) { // Check if it's a generic window part
                    return [
                        { label: 'Close', action: () => closeWindow(windowTarget) },
                        { label: 'Minimize', action: () => minimizeWindow(windowTarget) }
                    ];
                }
                
                if (windowTarget && windowTarget.dataset.appId === 'texts') {
                    return [
                        { label: 'Save', action: () => saveNotepadFile(windowTarget) },
                        { type: 'separator' },
                        
                        { 
                            label: 'Cut', 
                            action: () => {
                                const textarea = windowTarget.querySelector('.texts-textarea');
                                if (!textarea) return;
                                const start = textarea.selectionStart;
                                const end = textarea.selectionEnd;
                                if (start === end) return; // Nothing selected
                                
                                const selectedText = textarea.value.substring(start, end);
                                virtualClipboard = selectedText; // Store text
                                
                                // Remove text from textarea
                                textarea.value = textarea.value.substring(0, start) + textarea.value.substring(end);
                                // Move cursor
                                textarea.selectionStart = textarea.selectionEnd = start;
                                textarea.focus();
                            }
                        },
                        { 
                            label: 'Copy', 
                            action: () => {
                                const textarea = windowTarget.querySelector('.texts-textarea');
                                if (!textarea) return;
                                const start = textarea.selectionStart;
                                const end = textarea.selectionEnd;
                                if (start === end) return; // Nothing selected
                                
                                const selectedText = textarea.value.substring(start, end);
                                virtualClipboard = selectedText; // Store text
                                textarea.focus();
                            }
                        },
                        { 
                            label: 'Paste', 
                            disabled: virtualClipboard === '', // Disable if clipboard is empty
                            action: () => {
                                const textarea = windowTarget.querySelector('.texts-textarea');
                                if (!textarea || virtualClipboard === '') return;
                                
                                const text = virtualClipboard;
                                
                                // Insert text at current cursor position
                                const start = textarea.selectionStart;
                                const end = textarea.selectionEnd;
                                textarea.value = textarea.value.substring(0, start) + text + textarea.value.substring(end);
                                // Move cursor to end of pasted text
                                textarea.selectionStart = textarea.selectionEnd = start + text.length;
                                textarea.focus(); // Re-focus the textarea
                            }
                        }
                       ];
                }

                const fileItem = target.closest('.files-grid button');
                const filesWindow = target.closest('.window[data-app-id="files"]');

                if (fileItem && filesWindow) {
                    const filename = fileItem.dataset.filename;
                    const fileType = fileItem.dataset.type;
                    
                    return [
                        { label: 'Open', action: () => fileItem.click() },
                        { type: 'separator' },
                        { label: 'Rename', action: () => renameFileItem(filesWindow, filename) },
                        { label: 'Delete', action: () => deleteFileItem(filesWindow, filename) }
                    ];
                }

                if (filesWindow && target.closest('.files-grid-container')) {
                    return [
                        { label: 'New Folder', action: () => createNewItem(filesWindow, 'directory') },
                        { label: 'New Text File', action: () => createNewItem(filesWindow, 'file') },
                        { type: 'separator' },
                        { label: 'Refresh', action: () => {
                            const currentPath = filesWindow.dataset.currentPath || '/';
                            renderFileSystem(filesWindow, currentPath);
                        }}
                    ];
                }
                
                if (target.id === 'desktop') {
                    return [
                        { label: 'Reboot', action: () => location.reload() },
                        { label: 'Personalize', action: () => createWindow('preferences') }
                    ];
                }
                return [];
            }

            function showContextMenu(x, y, items) {
                contextMenu.innerHTML = '';
                items.forEach((item, index) => { 
                    if(item.type === 'separator') {
                         const separator = document.createElement('div');
                         separator.className = 'context-menu-separator';
                         separator.style.animationDelay = `${index * 0.02}s`; 
                         contextMenu.appendChild(separator);
                    } else {
                        const menuItem = document.createElement('button');
                        menuItem.className = 'context-menu-item w-full text-left';
                        menuItem.textContent = item.label;
                        menuItem.style.animationDelay = `${index * 0.02}s`; 
                        if(item.disabled) {
                            menuItem.disabled = true;
                            menuItem.classList.add('opacity-50', 'cursor-default');
                        } else {
                            menuItem.onclick = (e) => {
                               e.stopPropagation();
                               item.action();
                               contextMenu.classList.remove('show');
                            };
                        }
                        contextMenu.appendChild(menuItem);
                    }
                });

                contextMenu.style.visibility = 'hidden';
                contextMenu.style.display = 'block';
                const menuWidth = contextMenu.offsetWidth;
                const menuHeight = contextMenu.offsetHeight;
                
                let finalX = x;
                let finalY = y;
                let transformOrigin = 'top left';

                if (x + menuWidth > window.innerWidth) {
                    finalX = x - menuWidth;
                    transformOrigin = 'top right';
                }

                if (y + menuHeight > window.innerHeight) {
                    finalY = y - menuHeight;
                    transformOrigin = transformOrigin.replace('top', 'bottom');
                }
                
                contextMenu.style.transformOrigin = transformOrigin;
                contextMenu.style.left = `${finalX}px`;
                contextMenu.style.top = `${finalY}px`;
                contextMenu.style.visibility = '';
                
                requestAnimationFrame(() => contextMenu.classList.add('show'));
            }

            // --- Modal Dialog Logic ---
            function showModal(title, content, buttons) {
                modalBackdrop.querySelector('#modal-title').textContent = title;
                const contentEl = modalBackdrop.querySelector('#modal-content');
                contentEl.innerHTML = '';
                if (typeof content === 'string') {
                    contentEl.textContent = content;
                } else {
                    contentEl.appendChild(content);
                }
                
                const buttonsEl = modalBackdrop.querySelector('#modal-buttons');
                buttonsEl.innerHTML = '';
                buttons.forEach(btn => {
                    const button = document.createElement('button');
                    button.textContent = btn.label;
                    button.className = `px-4 py-2 text-sm font-medium ${btn.class || 'btn-default'}`;
                    button.onclick = () => {
                        // Special case for modal-within-modal (e.g., "File exists")
                        if (btn.label === 'OK' && buttons.length === 1 && btn.action === undefined) {
                           hideModal();
                           return;
                        }
                        
                        // Don't hide modal if the action returns false
                        let result;
                        if(btn.action) {
                           result = btn.action();
                        }
                        
                        if (result !== false) {
                            hideModal();
                        }
                    };
                    buttonsEl.appendChild(button);
                });
                
                modalBackdrop.style.display = 'flex';
                requestAnimationFrame(() => {
                    modalBackdrop.classList.add('show');
                });
            }

            function hideModal() {
                modalBackdrop.classList.remove('show');
                modalBackdrop.addEventListener('transitionend', () => {
                    if (!modalBackdrop.classList.contains('show')) {
                        modalBackdrop.style.display = 'none';
                    }
                }, { once: true });
            }

            // --- App Logic ---
            // Numbers (Calculator)
            window.updateCalcDisplay = () => {
                const calcWindow = Object.values(openWindows).find(w => w.appId === 'numbers' && w.element.style.zIndex == zIndexCounter);
                if(calcWindow) {
                    const display = calcWindow.element.querySelector('#calc-display');
                    if (display) display.textContent = currentCalculation || '0';
                }
            }
            window.handleCalcInput = (value) => {
                if (currentCalculation === '0' || currentCalculation === 'Error') currentCalculation = '';
                currentCalculation += value;
                updateCalcDisplay();
            }
            window.calculateResult = () => {
                try {
                    const expression = currentCalculation.replace(/×/g, '*').replace(/÷/g, '/');
                    currentCalculation = String(new Function('return ' + expression)());
                } catch (e) { currentCalculation = 'Error'; }
                updateCalcDisplay();
            }
            window.clearCalc = () => { currentCalculation = '0'; updateCalcDisplay(); }
            window.deleteLastChar = () => { currentCalculation = currentCalculation.slice(0, -1) || '0'; updateCalcDisplay(); }
            
            // Colors
            window.clearCanvas = (button) => {
                const canvas = button.closest('.colors-app').querySelector('.colors-canvas');
                const ctx = canvas.getContext('2d');
                ctx.clearRect(0, 0, canvas.width, canvas.height);
            }

            // --- Files & Texts Logic ---
            function getFileSystemNode(path) {
                if (!fileSystem) return null; // Guard against uninitialized VFS
                if (path === '/') return fileSystem; // Handle root
                return path.split('/').filter(p => p).reduce((node, part) => (node && node.type === 'directory' && node.children[part]) ? node.children[part] : null, fileSystem);
            }

            function renderFileSystem(win, path) {
                const node = getFileSystemNode(path);
                const grid = win.querySelector('.files-grid');
                const breadcrumbs = win.querySelector('.files-breadcrumbs');
                
                win.dataset.currentPath = path;

                if (!node || node.type !== 'directory' || !grid || !breadcrumbs) return;

                grid.innerHTML = '';
                Object.entries(node.children).forEach(([name, childNode]) => {
                    // --- HIDE SearchExperience.app ---
                    // if (name === 'SearchExperience.app') return; // No longer needed

                    const item = document.createElement('button');
                    item.dataset.filename = name;
                    item.dataset.type = childNode.type;
                    
                    item.className = 'flex flex-col items-center p-2 hover:bg-black/10 dark:hover:bg-white/10 text-center rounded relative'; // Added rounded/relative
                    
                    let icon = '📄'; // Default file
                    if (childNode.type === 'directory') {
                        icon = '📁';
                    } else if (childNode.type === 'app') {
                        // --- DYNAMIC APP ICONS ---
                        const appId = childNode.content;
                        icon = appConfig[appId] ? appConfig[appId].emoji : '🚀';
                    } else if (['.png', '.jpg', '.jpeg', '.gif'].some(ext => name.endsWith(ext))) {
                        icon = '🖼️';
                    }

                    item.innerHTML = `<div class="text-4xl pointer-events-none">${icon}</div><span class="text-xs mt-1 truncate w-full pointer-events-none">${name}</span>`;
                    
                    const fullPath = `${path === '/' ? '' : path}/${name}`;
                    
                    // Left click actions
                    if (childNode.type === 'directory') {
                        item.onclick = () => renderFileSystem(win, fullPath);
                    } else if (childNode.type === 'app') {
                        item.onclick = () => createWindow(childNode.content);
                    } else if (name.endsWith('.txt') || name.endsWith('.conf')) {
                        item.onclick = () => openFileInTexts(fullPath);
                    } else if (['.png', '.jpg', '.jpeg', '.gif'].some(ext => name.endsWith(ext))) {
                        item.onclick = () => openImageInViewer(fullPath);
                    } else {
                        // --- NEW UNKNOWN FILE HANDLER ---
                        item.onclick = () => {
                            showModal('Open with...', `How do you want to open "${name}"?`, [
                                { label: 'Cancel' },
                                { label: 'Images', class: 'btn-accent', action: () => openImageInViewer(fullPath) },
                                { label: 'Texts', class: 'btn-accent', action: () => openFileInTexts(fullPath) }
                            ]);
                        };
                        // --- END UNKNOWN FILE HANDLER ---
                    }
                    grid.appendChild(item);
                });

                // Breadcrumbs logic
                breadcrumbs.innerHTML = '';
                let currentPathStr = '';
                const parts = path.split('/').filter(p => p);
                const rootCrumb = document.createElement('button');
                rootCrumb.textContent = '/';
                rootCrumb.className = 'px-2 hover:underline';
                rootCrumb.dataset.path = '/';
                breadcrumbs.appendChild(rootCrumb);

                parts.forEach(part => {
                    currentPathStr += `/${part}`;
                    breadcrumbs.innerHTML += `<span class="px-1 opacity-50">></span>`;
                    const partCrumb = document.createElement('button');
                    partCrumb.textContent = part;
                    partCrumb.className = 'px-2 hover:underline';
                    partCrumb.dataset.path = currentPathStr;
                    breadcrumbs.appendChild(partCrumb);
                });
            }
            
            function openFileInTexts(path) {
                const fileNode = getFileSystemNode(path);
                if (!fileNode || fileNode.type !== 'file') return;
                
                // Check if 'texts' app is "installed"
                if (!checkAppVfsStatus('texts')) {
                     showModal(
                        'Application not found', 
                        `Cannot open "${path.split('/').pop()}" because the "Texts" application is not found.`, 
                        [{label: 'OK'}]
                    );
                    return;
                }
                
                const win = createWindow('texts');
                if(!win) return; 

                const textarea = win.querySelector('.texts-textarea');
                textarea.value = fileNode.content;
                win.dataset.filePath = path;
                win.dataset.originalContent = fileNode.content;
                win.querySelector('.title-bar .window-title').textContent = `${path.split('/').pop()} - Texts`;
            }
            
            function openImageInViewer(path) {
                const fileNode = getFileSystemNode(path);
                if (!fileNode || fileNode.type !== 'file') return;
                
                // Check if 'images' app is "installed"
                if (!checkAppVfsStatus('images')) {
                     showModal(
                        'Application not found', 
                        `Cannot open "${path.split('/').pop()}" because the "Images" application is not found.`, 
                        [{label: 'OK'}]
                    );
                    return;
                }
                
                const win = createWindow('images');
                if(!win) return; 
                // MODIFIED: Removed content check to allow force-parsing
                win.querySelector('.content-area img').src = fileNode.content; 
                win.querySelector('.title-bar .window-title').textContent = `${path.split('/').pop()} - Images`;
            }

            window.saveNotepadFile = (notepadWindow) => {
                const win = notepadWindow || Object.values(openWindows).find(w => w.element.style.zIndex == zIndexCounter && w.appId === 'texts')?.element;
                if (!win) return;

                const path = win.dataset.filePath;
                const textarea = win.querySelector('.texts-textarea');
                const newContent = textarea.value;

                if (path) { // Existing file
                    const parts = path.split('/');
                    const fileName = parts.pop();
                    const parentPath = parts.join('/') || '/';
                    const parentNode = getFileSystemNode(parentPath);
                    if (parentNode && parentNode.children[fileName]) {
                        parentNode.children[fileName].content = newContent;
                        win.dataset.originalContent = newContent;
                        saveVFSToLocalStorage(); // <-- Persist VFS
                    }
                } else { // New file
                    const input = document.createElement('input');
                    input.type = 'text';
                    input.placeholder = 'filename.txt';
                    input.className = 'w-full p-2 border bg-transparent';
                    showModal('Save As', input, [
                        { label: 'Cancel' },
                        { label: 'Save', class: 'btn-accent', action: () => {
                            let fileName = input.value.trim() || 'Untitled.txt';
                            if (!fileName.endsWith('.txt')) fileName += '.txt';
                            const homeNode = getFileSystemNode('/home/user/Documents'); // Save to Documents
                            homeNode.children[fileName] = { type: 'file', content: newContent };
                            const newPath = `/home/user/Documents/${fileName}`;
                            win.dataset.filePath = newPath;
                            win.dataset.originalContent = newContent;
                            win.querySelector('.title-bar .window-title').textContent = `${fileName} - Texts`;
                            saveVFSToLocalStorage(); // <-- Persist VFS
                        }}
                    ]);
                }
            }
            
            // --- New helper function to check VFS for app ---
            function checkAppVfsStatus(appId) {
                // --- MODIFIED: 'search' is now a system app ---
                if (appId === 'preferences' || appId === 'files' || appId === 'search') return true; 
                
                const appName = appConfig[appId].title;
                const appFileName = `${appName}.app`;
                const appNode = getFileSystemNode(`/home/user/Applications/${appFileName}`);
                
                return appNode && appNode.type === 'app' && appNode.content === appId;
            }

            // --- Window Management ---
            function createWindow(appId) {
                if (!appConfig[appId]) return null;
                
                // --- SPECIAL CASE FOR SEARCH ---
                if (appId === 'search') {
                    // No VFS check needed here anymore
                    searchMenu.classList.add('show');
                    setTimeout(() => searchInput.focus(), 200);
                    performSearch('');
                    clockFlyout.classList.remove('show');
                    return null; // Not a window, so return null
                }
                // --- END SPECIAL CASE ---
                
                // --- VFS App Check ---
                const isInstalled = checkAppVfsStatus(appId);
                if (!isInstalled) {
                    const appName = appConfig[appId].title;
                    const buttons = [ { label: 'OK' } ]; // Removed "Remove from Start"
                    showModal('Application not found', `The application "${appName}" (${appName}.app) could not be found in /home/user/Applications.`, buttons);
                    return null;
                }
                // --- End VFS App Check ---

                const config = appConfig[appId];

                if (!config.multiInstance) {
                    const existing = Object.values(openWindows).find(w => w.appId === appId);
                    if(existing) {
                        focusWindow(existing.element);
                        return existing.element;
                    }
                }

                const instanceId = `${appId}-${Date.now()}`;
                windowCounter++;

                const win = document.createElement('div');
                win.className = 'window blur-backdrop window-bg opening';

                win.style.width = config.width || '800px';
                win.style.height = config.height || '600px';
                win.style.left = `${100 + windowCounter * 20}px`;
                win.style.top = `${100 + windowCounter * 20}px`;
                win.dataset.appId = appId;
                win.dataset.instanceId = instanceId;
                
                win.innerHTML = `
                    <div class="title-bar flex justify-between items-center pl-3">
                        <div class="flex items-center gap-2">
                            <span class="text-base">${config.emoji}</span>
                            <span class="window-title text-sm font-medium">${config.title}</span>
                        </div>
                        <div class="title-bar-controls flex items-center">
                            <button class="minimize-btn"><svg xmlns="http://www.w3.org/2000/svg" width="12" height="12" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.5"><line x1="3" y1="12" x2="21" y2="12"></line></svg></button>
                            <button class="maximize-btn"><svg xmlns="http://www.w3.org/2000/svg" width="12" height="12" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.5"><rect x="3" y="3" width="18" height="18" rx="2" ry="2"></rect></svg></button>
                            <button class="close-btn"><svg xmlns="http://www.w3.org/2000/svg" width="12" height="12" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.5"><line x1="3" y1="3" x2="21" y2="21"></line><line x1="21" y1="3" x2="3" y2="21"></line></svg></button>
                        </div>
                    </div>
                    <div class="content-area">${config.content}</div>`;
                
                const taskbarIcon = document.createElement('button');
                taskbarIcon.className = 'taskbar-item p-3 hover:bg-black/10 dark:hover:bg-white/10 transition-colors relative';
                taskbarIcon.dataset.appId = appId;
                taskbarIcon.dataset.instanceId = instanceId;
                taskbarIcon.innerHTML = `<span class="text-xl leading-6">${config.emoji}</span>`;
                taskbarIcon.onclick = (e) => { 
                    animateClick(e.currentTarget);
                    const winData = openWindows[instanceId];
                    if (!winData) return;
                    const isFocused = !winData.minimized && winData.element.style.zIndex == zIndexCounter;
                    if (isFocused) minimizeWindow(winData.element);
                    else focusWindow(winData.element);
                };
                taskbarApps.appendChild(taskbarIcon);
                
                desktop.appendChild(win);
                openWindows[instanceId] = { appId, element: win, minimized: false, taskbarIcon };
                
                if (appId === 'preferences') {
                    const checkbox = win.querySelector('#dark-mode-checkbox');
                    if (checkbox) checkbox.checked = document.body.classList.contains('dark');
                    
                    const resetBtn = win.querySelector('#reset-vfs-btn');
                    if (resetBtn) {
                        resetBtn.onclick = () => {
                            showModal('Reset File System?', 
                                'All files and folders will be reset. This will restore any missing apps.',
                                [
                                    { label: 'Cancel' },
                                    { label: 'Reset', class: 'btn-danger', action: () => {
                                        fileSystem = JSON.parse(JSON.stringify(defaultFileSystem));
                                        populateDefaultApps(); // Re-populate .app files
                                        saveVFSToLocalStorage();
                                        
                                        // Removed hidden apps & tile layout reset
                                        
                                        // Refresh any open 'Files' windows
                                        Object.values(openWindows).forEach(w => {
                                            if (w.appId === 'files') {
                                                renderFileSystem(w.element, w.element.dataset.currentPath || '/');
                                            }
                                        });
                                    }}
                                ]
                            );
                        };
                    }
                } else if (appId === 'files') {
                    renderFileSystem(win, '/');
                    win.querySelector('.files-sidebar').addEventListener('click', (e) => {
                        if (e.target.tagName === 'BUTTON' && e.target.dataset.path) {
                            renderFileSystem(win, e.target.dataset.path);
                        }
                    });
                    win.querySelector('.files-breadcrumbs').addEventListener('click', (e) => {
                        if (e.target.tagName === 'BUTTON' && e.target.dataset.path) {
                            renderFileSystem(win, e.target.dataset.path);
                        }
                    });
                } else if (appId === 'texts') {
                    win.dataset.originalContent = win.querySelector('.texts-textarea').value;
                } else if (appId === 'colors') {
                    initColorsApp(win);
                } else if (appId === 'zdeupload') {
                    initZDevUpload(win);
                } else if (appId === 'minesweeper') {
                    initMinesweeperApp(win);
                }

                setupWindowInteractions(win);
                focusWindow(win);

                requestAnimationFrame(() => win.classList.remove('opening'));
                return win;
            }
            
            function closeWindow(win, force = false) {
                const instanceId = win.dataset.instanceId;
                const appId = win.dataset.appId;
                if (!win || !openWindows[instanceId]) return;
                
                if (openWindows[instanceId].resizeObserver) {
                    openWindows[instanceId].resizeObserver.disconnect();
                }

                const doClose = () => {
                    win.classList.add('closing');
                    win.addEventListener('animationend', () => {
                         if (openWindows[instanceId]) {
                            openWindows[instanceId].taskbarIcon.remove();
                            delete openWindows[instanceId];
                         }
                         if (win.parentElement) win.remove();
                    }, {once: true});
                };
                
                if (appId === 'texts' && !force) {
                    const textarea = win.querySelector('.texts-textarea');
                    const isDirty = textarea.value !== win.dataset.originalContent;
                    if (isDirty) {
                        showModal('Save changes?', 'Do you want to save your changes before closing?', [
                            {label: "Don't Save", action: doClose},
                            {label: "Cancel"},
                            {label: "Save", class: 'btn-accent', action: () => { saveNotepadFile(win); doClose(); }},
                        ]);
                        return;
                    }
                }
                
                doClose();
            }

            function setupWindowInteractions(win) {
                const titleBar = win.querySelector('.title-bar');
                
                titleBar.addEventListener('mousedown', (e) => {
                    if (e.target.closest('button') || win.classList.contains('maximized')) return;
                    
                    e.preventDefault();
                    
                    let isDragging = true;
                    const startX = e.clientX;
                    const startY = e.clientY;
                    const initialLeft = win.offsetLeft;
                    const initialTop = win.offsetTop;
                    
                    titleBar.style.cursor = 'grabbing';
                    focusWindow(win);

                    const onMouseMove = (e) => {
                        if (!isDragging) return;
                        const dx = e.clientX - startX;
                        const dy = e.clientY - startY;
                        
                        win.style.left = `${initialLeft + dx}px`;
                        win.style.top = `${initialTop + dy}px`;
                    };

                    const onMouseUp = () => {
                        isDragging = false;
                        titleBar.style.cursor = 'move';
                        document.removeEventListener('mousemove', onMouseMove);
                        document.removeEventListener('mouseup', onMouseUp);
                    };

                    document.addEventListener('mousemove', onMouseMove);
                    document.addEventListener('mouseup', onMouseUp);
                });

                win.querySelector('.close-btn').addEventListener('click', (e) => {
                    e.stopPropagation();
                    closeWindow(win);
                });

                win.querySelector('.minimize-btn').addEventListener('click', (e) => {
                    e.stopPropagation();
                    minimizeWindow(win);
                });

                win.querySelector('.maximize-btn').addEventListener('click', (e) => {
                    e.stopPropagation();
                    win.classList.add('transitioning');
                    
                    setTimeout(() => {
                        win.classList.remove('transitioning');
                    }, 250);

                    if (win.classList.contains('maximized')) {
                        win.classList.remove('maximized');
                        win.style.width = win.dataset.originalWidth;
                        win.style.height = win.dataset.originalHeight;
                        win.style.left = win.dataset.originalLeft;
                        win.style.top = win.dataset.originalTop;
                        win.style.resize = 'both';
                    } else {
                        win.dataset.originalWidth = win.style.width;
                        win.style.height = win.style.height;
                        win.dataset.originalLeft = win.style.left;
                        win.dataset.originalTop = win.style.top;
                        win.classList.add('maximized');
                        win.style.resize = 'none';
                    }
                });
                
                win.addEventListener('mousedown', () => focusWindow(win), true);
            }
            
            function focusWindow(win) {
                const instanceId = win.dataset.instanceId;
                if (openWindows[instanceId] && openWindows[instanceId].minimized) {
                    win.classList.remove('minimized');
                    openWindows[instanceId].minimized = false;
                }
                zIndexCounter++;
                win.style.zIndex = zIndexCounter;
                
                document.querySelectorAll('#taskbar-apps .taskbar-item').forEach(icon => icon.classList.remove('active'));
                if (openWindows[instanceId]) {
                    openWindows[instanceId].taskbarIcon.classList.add('active');
                }
            }

            function minimizeWindow(win) {
                const instanceId = win.dataset.instanceId;
                if(openWindows[instanceId]) {
                    win.classList.add('minimized');
                    openWindows[instanceId].minimized = true;
                    openWindows[instanceId].taskbarIcon.classList.remove('active');
                }
            }
            
            function pinApp(appId) {
                if (appConfig[appId] && !pinnedApps.has(appId)) {
                    pinnedApps.add(appId);
                    renderPinnedApps();
                }
            }
            function unPinApp(appId) {
                if (pinnedApps.has(appId)) {
                    pinnedApps.delete(appId);
                    renderPinnedApps();
                }
            }

            function renderPinnedApps() {
                pinnedAppsContainer.innerHTML = '';
                pinnedApps.forEach(appId => {
                    const config = appConfig[appId];
                    if (!config) {
                        console.warn(`Config for pinned app "${appId}" not found. Removing.`);
                        pinnedApps.delete(appId);
                        return;
                    }
                    const btn = document.createElement('button');
                    btn.className = 'app-launcher taskbar-item p-3 hover:bg-black/10 dark:hover:bg-white/10 transition-colors';
                    btn.dataset.appId = appId;
                    btn.innerHTML = `<span class="text-xl leading-6">${config.emoji}</span>`;
                    btn.onclick = (e) => { 
                        animateClick(e.currentTarget);
                        setTimeout(() => createWindow(appId), 100);
                    };
                    pinnedAppsContainer.appendChild(btn);
                });
            }
            
            // --- renderStartMenu is removed ---
            
            // --- New helper function to find all app IDs in a VFS node (recursive) ---
            function findAppIdsRecursive(node) {
                let ids = [];
                if (!node) return ids; // Safety check

                if (node.type === 'app') {
                    // Check if it's a valid app ID
                    if (appConfig[node.content]) {
                        ids.push(node.content);
                    }
                } else if (node.type === 'directory' && node.children) {
                    Object.values(node.children).forEach(child => {
                        ids = ids.concat(findAppIdsRecursive(child));
                    });
                }
                return ids;
            }

            // App initializers
            function initColorsApp(win) {
                const canvas = win.querySelector('.colors-canvas');
                const contentArea = win.querySelector('.content-area');
                const toolbar = win.querySelector('.colors-toolbar');
                const ctx = canvas.getContext('2d');
                let isDrawing = false;
                let lastX = 0;
                let lastY = 0;
                
                function resizeCanvas() {
                    const oldContent = ctx.getImageData(0, 0, canvas.width, canvas.height);
                    
                    canvas.width = canvas.offsetWidth;
                    canvas.height = canvas.offsetHeight;
                    
                    ctx.putImageData(oldContent, 0, 0);

                    const activeColor = toolbar.querySelector('.color-swatch.active')?.dataset.color || '#000000';
                    ctx.strokeStyle = activeColor;
                    ctx.lineWidth = 2;
                    ctx.lineJoin = 'round';
                    ctx.lineCap = 'round';
                }
                
                requestAnimationFrame(resizeCanvas);
                
                const resizeObserver = new ResizeObserver(resizeCanvas);
                resizeObserver.observe(contentArea);
                
                const instanceId = win.dataset.instanceId;
                if(openWindows[instanceId]) {
                    openWindows[instanceId].resizeObserver = resizeObserver;
                }
                
                toolbar.addEventListener('click', (e) => {
                    if (e.target.dataset.color) {
                        ctx.strokeStyle = e.target.dataset.color;
                        toolbar.querySelector('.color-swatch.active').classList.remove('active');
                        e.target.classList.add('active');
                    }
                });

                canvas.addEventListener('mousedown', (e) => { isDrawing = true; [lastX, lastY] = [e.offsetX, e.offsetY]; });
                canvas.addEventListener('mousemove', (e) => {
                    if (!isDrawing) return;
                    ctx.beginPath();
                    ctx.moveTo(lastX, lastY);
                    ctx.lineTo(e.offsetX, e.offsetY);
                    ctx.stroke();
                    [lastX, lastY] = [e.offsetX, e.offsetY];
                });
                canvas.addEventListener('mouseup', () => isDrawing = false);
                canvas.addEventListener('mouseleave', () => isDrawing = false);
            }

            function initZDevUpload(win) {
                const uploader = win.querySelector('#file-uploader');
                uploader.addEventListener('change', (e) => {
                    const file = e.target.files[0];
                    if (!file) return;

                    const reader = new FileReader();
                    const downloadsNode = getFileSystemNode('/home/user/Downloads');
                    
                    if (file.type.startsWith('image/')) {
                        reader.onload = (event) => {
                            downloadsNode.children[file.name] = { type: 'file', content: event.target.result };
                            saveVFSToLocalStorage();
                            openImageInViewer(`/home/user/Downloads/${file.name}`);
                        };
                        reader.readAsDataURL(file);
                    } else if (file.type === 'text/plain') {
                        reader.onload = (event) => {
                            downloadsNode.children[file.name] = { type: 'file', content: event.target.result };
                            saveVFSToLocalStorage();
                            openFileInTexts(`/home/user/Downloads/${file.name}`);
                        };
                        reader.readAsText(file);
                    } else {
                        showModal('Unsupported File', 'This file type is not supported.', [{label: 'OK'}]);
                    }
                    closeWindow(win, true);
                });
            }

            function initMinesweeperApp(win) {
                const GRID_SIZE = 9;
                const NUM_MINES = 10;

                const grid = win.querySelector('#minesweeper-grid');
                const mineCountDisplay = win.querySelector('#mine-count');
                const timerDisplay = win.querySelector('#timer');
                const resetBtn = win.querySelector('#reset-game');

                let board = [];
                let mineLocations = [];
                let flagsPlaced = 0;
                let timerInterval;
                let time = 0;
                let gameOver = false;
                let firstClick = true;

                function createBoard() {
                    board = Array.from({ length: GRID_SIZE }, () => 
                        Array.from({ length: GRID_SIZE }, () => ({
                            isMine: false,
                            isRevealed: false,
                            isFlagged: false,
                            adjacentMines: 0
                        }))
                    );
                    
                    grid.innerHTML = '';
                    grid.style.gridTemplateColumns = `repeat(${GRID_SIZE}, 1fr)`;
                    grid.style.gridTemplateRows = `repeat(${GRID_SIZE}, 1fr)`;

                    for (let r = 0; r < GRID_SIZE; r++) {
                        for (let c = 0; c < GRID_SIZE; c++) {
                            const cell = document.createElement('div');
                            cell.className = 'minesweeper-cell';
                            cell.dataset.row = r;
                            cell.dataset.col = c;
                            grid.appendChild(cell);
                        }
                    }
                }

                function plantMines(firstRow, firstCol) {
                    mineLocations = [];
                    let minesToPlant = NUM_MINES;

                    while (minesToPlant > 0) {
                        const r = Math.floor(Math.random() * GRID_SIZE);
                        const c = Math.floor(Math.random() * GRID_SIZE);

                        if ((r === firstRow && c === firstCol) || board[r][c].isMine) {
                            continue;
                        }

                        board[r][c].isMine = true;
                        mineLocations.push({r, c});
                        minesToPlant--;
                    }

                    for (let r = 0; r < GRID_SIZE; r++) {
                        for (let c = 0; c < GRID_SIZE; c++) {
                            if (board[r][c].isMine) continue;
                            board[r][c].adjacentMines = countAdjacentMines(r, c);
                        }
                    }
                }

                function countAdjacentMines(row, col) {
                    let count = 0;
                    for (let rOffset = -1; rOffset <= 1; rOffset++) {
                        for (let cOffset = -1; cOffset <= 1; cOffset++) {
                            if (rOffset === 0 && cOffset === 0) continue;
                            const nr = row + rOffset;
                            const nc = col + cOffset;
                            if (isValidCell(nr, nc) && board[nr][nc].isMine) {
                                count++;
                            }
                        }
                    }
                    return count;
                }

                function isValidCell(r, c) {
                    return r >= 0 && r < GRID_SIZE && c >= 0 && c < GRID_SIZE;
                }

                function startTimer() {
                    if (timerInterval) clearInterval(timerInterval);
                    time = 0;
                    timerDisplay.textContent = time;
                    timerInterval = setInterval(() => {
                        time++;
                        timerDisplay.textContent = time;
                    }, 1000);
                }

                function stopTimer() {
                    clearInterval(timerInterval);
                }

                function handleCellClick(e) {
                    if (gameOver) return;
                    
                    const cellEl = e.target.closest('.minesweeper-cell');
                    if (!cellEl) return;

                    const row = parseInt(cellEl.dataset.row);
                    const col = parseInt(cellEl.dataset.col);
                    const cellData = board[row][col];

                    if (cellData.isRevealed || cellData.isFlagged) return;

                    if (firstClick) {
                        plantMines(row, col);
                        startTimer();
                        firstClick = false;
                    }

                    if (cellData.isMine) {
                        endGame(false);
                        cellEl.classList.add('mine');
                    } else {
                        revealCell(row, col);
                        checkWin();
                    }
                }

                function handleCellRightClick(e) {
                    e.preventDefault();
                    if (gameOver) return;

                    const cellEl = e.target.closest('.minesweeper-cell');
                    if (!cellEl) return;

                    const row = parseInt(cellEl.dataset.row);
                    const col = parseInt(cellEl.dataset.col);
                    const cellData = board[row][col];

                    if (cellData.isRevealed) return;

                    cellData.isFlagged = !cellData.isFlagged;
                    if (cellData.isFlagged) {
                        cellEl.textContent = '🚩';
                        flagsPlaced++;
                    } else {
                        cellEl.textContent = '';
                        flagsPlaced--;
                    }
                    mineCountDisplay.textContent = NUM_MINES - flagsPlaced;
                }

                function revealCell(r, c) {
                    if (!isValidCell(r, c) || board[r][c].isRevealed) return;

                    const cellData = board[r][c];
                    const cellEl = grid.children[r * GRID_SIZE + c];

                    if (cellData.isFlagged) return;

                    cellData.isRevealed = true;
                    cellEl.classList.add('revealed');

                    if (cellData.adjacentMines > 0) {
                        cellEl.textContent = cellData.adjacentMines;
                        cellEl.dataset.adjacent = cellData.adjacentMines;
                    } else {
                        for (let rOffset = -1; rOffset <= 1; rOffset++) {
                            for (let cOffset = -1; cOffset <= 1; cOffset++) {
                                if (rOffset === 0 && cOffset === 0) continue;
                                revealCell(r + rOffset, c + cOffset);
                            }
                        }
                    }
                }

                function endGame(isWin) {
                    gameOver = true;
                    stopTimer();
                    resetBtn.textContent = isWin ? '😎' : '😵';

                    mineLocations.forEach(({r, c}) => {
                        const cellEl = grid.children[r * GRID_SIZE + c];
                        if (!board[r][c].isRevealed && !isWin) {
                            cellEl.classList.add('revealed');
                            cellEl.textContent = '💣';
                        }
                        if (board[r][c].isFlagged && !board[r][c].isMine) {
                             cellEl.style.backgroundColor = 'yellow';
                        }
                    });
                }
                
                function checkWin() {
                    let revealedCount = 0;
                    for (let r = 0; r < GRID_SIZE; r++) {
                        for (let c = 0; c < GRID_SIZE; c++) {
                            if (board[r][c].isRevealed) {
                                revealedCount++;
                            }
                        }
                    }
                    if (revealedCount === (GRID_SIZE * GRID_SIZE) - NUM_MINES) {
                        endGame(true);
                    }
                }

                function resetGame() {
                    gameOver = false;
                    firstClick = true;
                    flagsPlaced = 0;
                    time = 0;
                    stopTimer();
                    mineCountDisplay.textContent = NUM_MINES;
                    timerDisplay.textContent = 0;
                    resetBtn.textContent = '🙂';
                    createBoard();
                }
                
                createBoard();
                mineCountDisplay.textContent = NUM_MINES;
                timerDisplay.textContent = 0;
                
                grid.addEventListener('click', handleCellClick);
                grid.addEventListener('contextmenu', handleCellRightClick);
                resetBtn.addEventListener('click', resetGame);
            }

            // --- File System Operations ---
            function deleteFileItem(win, filename) {
                const currentPath = win.dataset.currentPath;
                const parentNode = getFileSystemNode(currentPath);
                
                // --- Prevent Deletion of SearchExperience.app ---
                // (No longer needed, it's not in the VFS)
                // if (filename === 'SearchExperience.app') { ... }
                
                if (parentNode && parentNode.children[filename]) {
                    showModal('Delete Item', `Are you sure you want to delete "${filename}"?`, [
                        { label: 'Cancel' },
                        { label: 'Delete', class: 'btn-danger', action: () => {
                            const itemToDelete = parentNode.children[filename];
                            
                            // --- App Deletion Logic ---
                            const appIdsToClose = findAppIdsRecursive(itemToDelete);
                            
                            // --- NEW START MENU CLOSE LOGIC ---
                            // (No longer needed, 'search' isn't in VFS)
                            // if (appIdsToClose.includes('search')) { ... }
                            // --- END NEW LOGIC ---

                            if (appIdsToClose.length > 0) {
                                console.log("Closing instances for deleted apps:", appIdsToClose);
                                Object.values(openWindows).forEach(w => {
                                    if (appIdsToClose.includes(w.appId)) {
                                        closeWindow(w.element, true); // Force close
                                    }
                                });
                            }
                            // --- End App Deletion Logic ---

                            delete parentNode.children[filename];
                            saveVFSToLocalStorage();
                            renderFileSystem(win, currentPath);
                        }}
                    ]);
                }
            }

            function renameFileItem(win, oldName) {
                const currentPath = win.dataset.currentPath;
                const parentNode = getFileSystemNode(currentPath);
                
                if (!parentNode) return;
                
                // --- Prevent Renaming of SearchExperience.app ---
                // (No longer needed)
                // if (oldName === 'SearchExperience.app') { ... }

                const input = document.createElement('input');
                input.type = 'text';
                input.value = oldName;
                input.className = 'w-full p-2 border bg-transparent';
                
                showModal('Rename', input, [
                    { label: 'Cancel' },
                    { label: 'Rename', class: 'btn-accent', action: () => {
                        const newName = input.value.trim();
                        if (!newName || newName === oldName) return;
                        
                        if (parentNode.children[newName]) {
                            showModal('Error', 'An item with that name already exists.', [{label: 'OK'}]);
                            return false; // Don't close modal
                        }
                        
                        parentNode.children[newName] = parentNode.children[oldName];
                        delete parentNode.children[oldName];
                        
                        saveVFSToLocalStorage();
                        renderFileSystem(win, currentPath);
                    }}
                ]);
            }

            function createNewItem(win, type) {
                const currentPath = win.dataset.currentPath;
                const parentNode = getFileSystemNode(currentPath);
                if (!parentNode) return;

                const input = document.createElement('input');
                const defaultName = type === 'directory' ? 'New Folder' : 'New Text.txt';
                input.type = 'text';
                input.value = defaultName;
                input.className = 'w-full p-2 border bg-transparent';
                
                setTimeout(() => input.select(), 50);

                showModal(type === 'directory' ? 'Create Folder' : 'Create File', input, [
                    { label: 'Cancel' },
                    { label: 'Create', class: 'btn-accent', action: () => {
                        let newName = input.value.trim();
                        if (!newName) {
                            newName = defaultName;
                        }
                        
                        if (parentNode.children[newName]) {
                            showModal('Error', 'An item with that name already exists.', [{label: 'OK'}]);
                            return false; // Don't close modal
                        }

                        if (type === 'directory') {
                            parentNode.children[newName] = { type: 'directory', children: {} };
                        } else {
                            let finalName = newName;
                            if (!finalName.includes('.')) finalName += '.txt';
                            parentNode.children[finalName] = { type: 'file', content: '' };
                        }
                        saveVFSToLocalStorage();
                        renderFileSystem(win, currentPath);
                    }}
                ]);
            }

            // --- Initial Render ---
            applyInitialTheme();
            initializeVFS();
            // loadHiddenApps(); // Removed
            // loadTileConfig(); // Removed
            renderPinnedApps();
            // renderStartMenu(); // Removed
            // createWindow('preferences'); // Not auto-opening preferences anymore
        });
    </script>

</body>
</html>
