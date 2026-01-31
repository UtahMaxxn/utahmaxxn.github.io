
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>FreshKeep - Intelligent Food Tracker</title>
    
    <!-- Tailwind CSS -->
    <script src="https://cdn.tailwindcss.com"></script>
    
    <!-- Tesseract.js for OCR -->
    <script src="https://cdn.jsdelivr.net/npm/tesseract.js@5/dist/tesseract.min.js"></script>
    
    <!-- Google Fonts -->
    <link rel="preconnect" href="https://fonts.googleapis.com">
    <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
    <link href="https://fonts.googleapis.com/css2?family=Outfit:wght@300;400;500;600;700&family=Plus+Jakarta+Sans:wght@400;500;600;700&family=Noto+Sans+TC:wght@400;500;700&display=swap" rel="stylesheet">

    <script>
        tailwind.config = {
            darkMode: 'class',
            theme: {
                extend: {
                    fontFamily: {
                        sans: ['"Plus Jakarta Sans"', '"Noto Sans TC"', 'sans-serif'],
                        display: ['"Outfit"', '"Noto Sans TC"', 'sans-serif'],
                    },
                    colors: {
                        nature: {
                            50: '#f2fcf5',
                            100: '#e1f8e8',
                            200: '#c3eed0',
                            300: '#94e0ad',
                            400: '#5cc984',
                            500: '#35ae65',
                            600: '#258c4e',
                            700: '#216f40',
                            800: '#1f5836',
                            900: '#1a492e',
                        },
                        warning: {
                            400: '#fbbf24',
                            500: '#f59e0b',
                        },
                        danger: {
                            400: '#f87171',
                            500: '#ef4444',
                        }
                    },
                    animation: {
                        'fade-in': 'fadeIn 0.5s ease-out forwards',
                        'slide-up': 'slideUp 0.4s ease-out forwards',
                        'slide-in-right': 'slideInRight 0.3s ease-out forwards',
                        'bounce-slight': 'bounceSlight 2s infinite',
                        'pulse-slow': 'pulse 3s cubic-bezier(0.4, 0, 0.6, 1) infinite',
                    },
                    keyframes: {
                        fadeIn: {
                            '0%': { opacity: '0' },
                            '100%': { opacity: '1' },
                        },
                        slideUp: {
                            '0%': { opacity: '0', transform: 'translateY(20px)' },
                            '100%': { opacity: '1', transform: 'translateY(0)' },
                        },
                        slideInRight: {
                            '0%': { opacity: '0', transform: 'translateX(20px)' },
                            '100%': { opacity: '1', transform: 'translateX(0)' },
                        },
                        bounceSlight: {
                            '0%, 100%': { transform: 'translateY(-3%)' },
                            '50%': { transform: 'translateY(0)' },
                        }
                    }
                }
            }
        }
    </script>

    <style>
        /* Custom Styles for Harmonic UI */
        :root {
            --glass-bg: rgba(255, 255, 255, 0.75);
            --glass-border: rgba(255, 255, 255, 0.5);
            --glass-shadow: 0 8px 32px 0 rgba(31, 38, 135, 0.07);
        }

        .dark {
            --glass-bg: rgba(30, 41, 59, 0.75);
            --glass-border: rgba(255, 255, 255, 0.05);
            --glass-shadow: 0 8px 32px 0 rgba(0, 0, 0, 0.3);
        }

        body {
            background-color: #f8fafc;
            background-image: 
                radial-gradient(at 0% 0%, rgba(53, 174, 101, 0.15) 0px, transparent 50%),
                radial-gradient(at 100% 0%, rgba(245, 158, 11, 0.1) 0px, transparent 50%),
                radial-gradient(at 100% 100%, rgba(59, 130, 246, 0.1) 0px, transparent 50%);
            background-attachment: fixed;
            background-size: cover;
            min-height: 100vh;
        }

        .dark body {
            background-color: #0f172a;
            background-image: 
                radial-gradient(at 0% 0%, rgba(53, 174, 101, 0.1) 0px, transparent 50%),
                radial-gradient(at 100% 0%, rgba(245, 158, 11, 0.05) 0px, transparent 50%),
                radial-gradient(at 100% 100%, rgba(59, 130, 246, 0.05) 0px, transparent 50%);
        }

        /* Glassmorphism Classes */
        .glass-panel {
            background: var(--glass-bg);
            backdrop-filter: blur(12px);
            -webkit-backdrop-filter: blur(12px);
            border: 1px solid var(--glass-border);
            box-shadow: var(--glass-shadow);
        }

        .glass-input {
            background: rgba(255, 255, 255, 0.5);
            border: 1px solid rgba(0, 0, 0, 0.05);
            transition: all 0.3s ease;
        }
        .dark .glass-input {
            background: rgba(0, 0, 0, 0.2);
            border: 1px solid rgba(255, 255, 255, 0.05);
            color: white;
        }

        .glass-input:focus {
            background: rgba(255, 255, 255, 0.8);
            box-shadow: 0 0 0 2px rgba(53, 174, 101, 0.3);
            outline: none;
        }
        .dark .glass-input:focus {
            background: rgba(0, 0, 0, 0.4);
        }

        /* Custom Scrollbar */
        ::-webkit-scrollbar {
            width: 8px;
            height: 8px;
        }
        ::-webkit-scrollbar-track {
            background: transparent;
        }
        ::-webkit-scrollbar-thumb {
            background: rgba(156, 163, 175, 0.5);
            border-radius: 4px;
        }
        ::-webkit-scrollbar-thumb:hover {
            background: rgba(156, 163, 175, 0.8);
        }

        /* Card Hover Effects */
        .food-card {
            transition: transform 0.2s cubic-bezier(0.4, 0, 0.2, 1), 
                        box-shadow 0.2s cubic-bezier(0.4, 0, 0.2, 1);
        }
        .food-card:hover {
            transform: translateY(-4px);
            box-shadow: 0 12px 24px -8px rgba(0, 0, 0, 0.15);
        }

        /* Modal Transitions */
        .modal-overlay {
            opacity: 0;
            visibility: hidden;
            transition: opacity 0.3s ease, visibility 0.3s ease;
        }
        .modal-overlay.active {
            opacity: 1;
            visibility: visible;
        }
        .modal-content {
            transform: scale(0.95) translateY(10px);
            opacity: 0;
            transition: transform 0.3s cubic-bezier(0.34, 1.56, 0.64, 1), opacity 0.3s ease;
        }
        .modal-overlay.active .modal-content {
            transform: scale(1) translateY(0);
            opacity: 1;
        }

        /* Scanning Animation */
        .scan-line {
            position: absolute;
            top: 0;
            left: 0;
            width: 100%;
            height: 2px;
            background: #35ae65;
            box-shadow: 0 0 4px #35ae65;
            animation: scan 2s linear infinite;
        }
        @keyframes scan {
            0% { top: 0%; opacity: 0; }
            50% { opacity: 1; }
            100% { top: 100%; opacity: 0; }
        }

        /* Mobile Optimization */
        @media (max-width: 640px) {
            .mobile-hide { display: none; }
            .mobile-fab { 
                position: fixed;
                bottom: 2rem;
                right: 2rem;
                z-index: 50;
            }
        }
    </style>
</head>
<body class="bg-slate-50 dark:bg-slate-900 text-slate-800 dark:text-slate-100 antialiased selection:bg-nature-500 selection:text-white transition-colors duration-500">

    <!-- App Container -->
    <div id="app" class="max-w-7xl mx-auto p-4 sm:p-6 lg:p-8 min-h-screen flex flex-col gap-6">
        
        <!-- Header Section -->
        <header class="flex flex-col md:flex-row justify-between items-center gap-4 animate-fade-in relative z-40">
            <div class="flex items-center gap-4">
                <div class="bg-nature-500 text-white p-3 rounded-2xl shadow-lg shadow-nature-500/30">
                    <svg xmlns="http://www.w3.org/2000/svg" width="32" height="32" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
                        <path d="M12 22s8-4 8-10V5l-8-3-8 3v7c0 6 8 10 8 10z"/>
                        <path d="M12 8v4"/>
                        <path d="M12 16h.01"/>
                    </svg>
                </div>
                <div>
                    <h1 class="text-3xl font-display font-bold bg-clip-text text-transparent bg-gradient-to-r from-nature-700 to-nature-500 dark:from-nature-400 dark:to-nature-200" data-i18n="app_title">
                        FreshKeep
                    </h1>
                    <p class="text-slate-500 dark:text-slate-400 text-sm font-medium" id="current-date">Loading date...</p>
                </div>
            </div>

            <div class="flex items-center gap-3 w-full md:w-auto justify-end">
                <!-- Lang Toggle -->
                <button onclick="app.toggleLang()" class="p-3 rounded-xl glass-panel hover:bg-white/50 dark:hover:bg-slate-700/50 transition-colors font-bold text-xs w-12 h-12 flex items-center justify-center">
                    <span id="lang-display">繁中</span>
                </button>

                <!-- Theme Toggle -->
                <button id="theme-toggle" class="p-3 rounded-xl glass-panel hover:bg-white/50 dark:hover:bg-slate-700/50 transition-colors group" aria-label="Toggle Dark Mode">
                    <svg id="sun-icon" class="hidden dark:block w-5 h-5 text-amber-400" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M12 3v1m0 16v1m9-9h-1M4 12H3m15.364 6.364l-.707-.707M6.343 6.343l-.707-.707m12.728 0l-.707.707M6.343 17.657l-.707.707M16 12a4 4 0 11-8 0 4 4 0 018 0z"></path></svg>
                    <svg id="moon-icon" class="block dark:hidden w-5 h-5 text-slate-600 group-hover:text-nature-600" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M20.354 24.354A9 9 0 018.646 3.646 9.003 9.003 0 0012 21a9.003 9.003 0 008.354-5.646z"></path></svg>
                </button>

                <!-- Notifications -->
                <div class="relative z-50">
                    <button id="notification-btn" class="p-3 rounded-xl glass-panel hover:bg-white/50 dark:hover:bg-slate-700/50 transition-colors relative">
                        <svg class="w-5 h-5 text-slate-600 dark:text-slate-300" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M15 17h5l-1.405-1.405A2.032 2.032 0 0118 14.158V11a6.002 6.002 0 00-4-5.659V5a2 2 0 10-4 0v.341C7.67 6.165 6 8.388 6 11v3.159c0 .538-.214 1.055-.595 1.436L4 17h5m6 0v1a3 3 0 11-6 0v-1m6 0H9"></path></svg>
                        <span id="notification-badge" class="absolute top-2 right-2 w-2.5 h-2.5 bg-danger-500 rounded-full border-2 border-white dark:border-slate-800 hidden animate-pulse-slow"></span>
                    </button>
                    <!-- Notification Dropdown -->
                    <div id="notification-dropdown" class="absolute right-0 mt-4 w-80 glass-panel rounded-2xl shadow-xl p-4 hidden transform origin-top-right transition-all">
                        <div class="flex justify-between items-center mb-3">
                            <h3 class="font-bold text-slate-800 dark:text-white" data-i18n="notifications">Notifications</h3>
                            <button class="text-xs text-nature-600 hover:text-nature-700 font-medium" onclick="app.clearNotifications()" data-i18n="clear_all">Clear all</button>
                        </div>
                        <ul id="notification-list" class="space-y-2 max-h-64 overflow-y-auto pr-1">
                            <li class="text-center text-sm text-slate-400 py-4" data-i18n="no_alerts">No new alerts</li>
                        </ul>
                    </div>
                </div>

                <!-- Add Button (Desktop) -->
                <button onclick="app.openModal()" class="hidden md:flex items-center gap-2 bg-nature-600 hover:bg-nature-700 text-white px-5 py-3 rounded-xl font-medium transition-all shadow-lg shadow-nature-600/20 active:scale-95">
                    <svg class="w-5 h-5" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M12 4v16m8-8H4"></path></svg>
                    <span data-i18n="add_item">Add Item</span>
                </button>
            </div>
        </header>

        <!-- Stats Overview -->
        <section class="grid grid-cols-1 md:grid-cols-3 gap-6 animate-slide-up" style="animation-delay: 0.1s;">
            <!-- Total Items -->
            <div class="glass-panel p-6 rounded-2xl flex items-center gap-4 relative overflow-hidden group">
                <div class="absolute right-0 top-0 w-24 h-24 bg-nature-100 dark:bg-nature-900/30 rounded-full -mr-6 -mt-6 transition-transform group-hover:scale-110"></div>
                <div class="p-3 bg-blue-100 dark:bg-blue-900/30 text-blue-600 dark:text-blue-400 rounded-xl">
                    <svg class="w-8 h-8" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M20 7l-8-4-8 4m16 0l-8 4m8-4v10l-8 4m0-10L4 7m8 4v10M4 7v10l8 4"></path></svg>
                </div>
                <div>
                    <p class="text-sm text-slate-500 dark:text-slate-400 font-medium" data-i18n="total_inventory">Total Inventory</p>
                    <h2 class="text-3xl font-display font-bold text-slate-800 dark:text-white" id="stat-total">0</h2>
                </div>
            </div>

            <!-- Fresh Items -->
            <div class="glass-panel p-6 rounded-2xl flex items-center gap-4 relative overflow-hidden group">
                <div class="absolute right-0 top-0 w-24 h-24 bg-green-100 dark:bg-green-900/30 rounded-full -mr-6 -mt-6 transition-transform group-hover:scale-110"></div>
                <div class="p-3 bg-nature-100 dark:bg-nature-900/30 text-nature-600 dark:text-nature-400 rounded-xl">
                    <svg class="w-8 h-8" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M9 12l2 2 4-4m6 2a9 9 0 11-18 0 9 9 0 0118 0z"></path></svg>
                </div>
                <div>
                    <p class="text-sm text-slate-500 dark:text-slate-400 font-medium" data-i18n="good_standing">In Good Standing</p>
                    <h2 class="text-3xl font-display font-bold text-slate-800 dark:text-white" id="stat-fresh">0</h2>
                </div>
            </div>

            <!-- Expiring Soon -->
            <div class="glass-panel p-6 rounded-2xl flex items-center gap-4 relative overflow-hidden group border-l-4 border-warning-400">
                <div class="absolute right-0 top-0 w-24 h-24 bg-orange-100 dark:bg-orange-900/30 rounded-full -mr-6 -mt-6 transition-transform group-hover:scale-110"></div>
                <div class="p-3 bg-orange-100 dark:bg-orange-900/30 text-orange-600 dark:text-orange-400 rounded-xl">
                    <svg class="w-8 h-8 animate-bounce-slight" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M12 9v2m0 4h.01m-6.938 4h13.856c1.54 0 2.502-1.667 1.732-3L13.732 4c-.77-1.333-2.694-1.333-3.464 0L3.34 16c-.77 1.333.192 3 1.732 3z"></path></svg>
                </div>
                <div>
                    <p class="text-sm text-slate-500 dark:text-slate-400 font-medium" data-i18n="action_required">Action Required</p>
                    <h2 class="text-3xl font-display font-bold text-slate-800 dark:text-white" id="stat-expiring">0</h2>
                </div>
            </div>
        </section>

        <!-- Main Content Area -->
        <main class="flex-1 grid grid-cols-1 lg:grid-cols-4 gap-6 animate-slide-up" style="animation-delay: 0.2s;">
            
            <!-- Filters & Categories Sidebar -->
            <aside class="lg:col-span-1 space-y-4">
                <div class="glass-panel p-5 rounded-2xl sticky top-4 z-30">
                    <h3 class="text-lg font-bold mb-4 text-slate-800 dark:text-white flex items-center gap-2">
                        <svg class="w-5 h-5 text-nature-500" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M3 4a1 1 0 011-1h16a1 1 0 011 1v2.586a1 1 0 01-.293.707l-6.414 6.414a1 1 0 00-.293.707V17l-4 4v-6.586a1 1 0 00-.293-.707L3.293 7.293A1 1 0 013 6.586V4z"></path></svg>
                        <span data-i18n="filters">Filters</span>
                    </h3>
                    
                    <div class="space-y-2">
                        <button onclick="app.setFilter('all')" class="filter-btn active w-full text-left px-4 py-3 rounded-xl text-sm font-medium transition-colors hover:bg-slate-100 dark:hover:bg-slate-800 flex justify-between items-center group">
                            <span data-i18n="filter_all">All Items</span>
                            <span class="bg-slate-200 dark:bg-slate-700 text-slate-600 dark:text-slate-300 py-0.5 px-2 rounded-lg text-xs" id="count-all">0</span>
                        </button>
                        <button onclick="app.setFilter('expiring')" class="filter-btn w-full text-left px-4 py-3 rounded-xl text-sm font-medium transition-colors hover:bg-orange-50 dark:hover:bg-orange-900/20 text-slate-600 dark:text-slate-400 flex justify-between items-center group">
                            <span class="group-hover:text-orange-600 dark:group-hover:text-orange-400" data-i18n="filter_expiring">Expiring Soon</span>
                            <span class="bg-orange-100 dark:bg-orange-900/40 text-orange-600 dark:text-orange-400 py-0.5 px-2 rounded-lg text-xs" id="count-expiring">0</span>
                        </button>
                        <button onclick="app.setFilter('expired')" class="filter-btn w-full text-left px-4 py-3 rounded-xl text-sm font-medium transition-colors hover:bg-red-50 dark:hover:bg-red-900/20 text-slate-600 dark:text-slate-400 flex justify-between items-center group">
                            <span class="group-hover:text-red-600 dark:group-hover:text-red-400" data-i18n="filter_expired">Expired</span>
                            <span class="bg-red-100 dark:bg-red-900/40 text-red-600 dark:text-red-400 py-0.5 px-2 rounded-lg text-xs" id="count-expired">0</span>
                        </button>
                        <button onclick="app.setFilter('history')" class="filter-btn w-full text-left px-4 py-3 rounded-xl text-sm font-medium transition-colors hover:bg-purple-50 dark:hover:bg-purple-900/20 text-slate-600 dark:text-slate-400 flex justify-between items-center group">
                            <span class="group-hover:text-purple-600 dark:group-hover:text-purple-400" data-i18n="filter_history">History / Consumed</span>
                            <svg class="w-4 h-4 text-slate-400" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M12 8v4l3 3m6-3a9 9 0 11-18 0 9 9 0 0118 0z"></path></svg>
                        </button>
                    </div>

                    <hr class="my-4 border-slate-200 dark:border-slate-700">
                    
                    <h4 class="text-xs font-bold text-slate-400 uppercase tracking-wider mb-3" data-i18n="sort_by">Sort By</h4>
                    <select id="sort-select" onchange="app.sortItems(this.value)" class="w-full glass-input p-2.5 rounded-xl text-sm">
                        <option value="date-asc" data-i18n="sort_date_asc">Date (Soonest First)</option>
                        <option value="date-desc" data-i18n="sort_date_desc">Date (Latest First)</option>
                        <option value="name-asc" data-i18n="sort_name_asc">Name (A-Z)</option>
                        <option value="category-asc" data-i18n="sort_category">Category</option>
                    </select>
                </div>
            </aside>

            <!-- Food List Section -->
            <section class="lg:col-span-3">
                <!-- Empty State -->
                <div id="empty-state" class="hidden glass-panel rounded-2xl p-12 text-center flex flex-col items-center justify-center min-h-[400px]">
                    <div class="w-32 h-32 bg-slate-50 dark:bg-slate-800 rounded-full flex items-center justify-center mb-6">
                        <svg class="w-16 h-16 text-slate-300 dark:text-slate-600" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="1.5" d="M19 11H5m14 0a2 2 0 012 2v6a2 2 0 01-2 2H5a2 2 0 01-2-2v-6a2 2 0 012-2m14 0V9a2 2 0 00-2-2M5 11V9a2 2 0 012-2m0 0V5a2 2 0 012-2h6a2 2 0 012 2v2M7 7h10"></path></svg>
                    </div>
                    <h3 class="text-xl font-display font-bold text-slate-800 dark:text-white mb-2" data-i18n="empty_title">Pantry is Empty</h3>
                    <p class="text-slate-500 dark:text-slate-400 max-w-sm mb-8" data-i18n="empty_desc">Start tracking your food to reduce waste and save money. Add your first item now!</p>
                    <button onclick="app.openModal()" class="bg-nature-600 hover:bg-nature-700 text-white px-6 py-3 rounded-xl font-medium transition-all shadow-lg hover:shadow-xl hover:-translate-y-1">
                        <span data-i18n="add_first_item">Add First Item</span>
                    </button>
                    <button onclick="app.loadMockData()" class="mt-4 text-sm text-nature-600 hover:text-nature-700 underline">
                        <span data-i18n="load_demo">Load Demo Data</span>
                    </button>
                </div>

                <!-- Grid/List View -->
                <div id="food-grid" class="grid grid-cols-1 md:grid-cols-2 xl:grid-cols-3 gap-4">
                    <!-- Items injected by JS -->
                </div>
            </section>
        </main>
    </div>

    <!-- Floating Action Button (Mobile) -->
    <button onclick="app.openModal()" class="mobile-fab md:hidden bg-nature-600 hover:bg-nature-700 text-white p-4 rounded-full shadow-lg shadow-nature-600/40 active:scale-90 transition-transform">
        <svg class="w-6 h-6" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M12 4v16m8-8H4"></path></svg>
    </button>

    <!-- Add Item Modal -->
    <div id="add-modal" class="modal-overlay fixed inset-0 z-50 flex items-center justify-center p-4">
        <div class="absolute inset-0 bg-slate-900/60 backdrop-blur-sm" onclick="app.closeModal()"></div>
        <div class="modal-content glass-panel w-full max-w-md rounded-3xl p-0 relative bg-white dark:bg-slate-800 shadow-2xl overflow-hidden flex flex-col max-h-[90vh]">
            
            <!-- Modal Header / Tabs -->
            <div class="flex border-b border-slate-100 dark:border-slate-700">
                <button onclick="app.switchTab('manual')" id="tab-manual" class="flex-1 py-4 text-sm font-bold text-nature-600 border-b-2 border-nature-600 transition-colors">
                    Manual Entry
                </button>
                <button onclick="app.switchTab('scan')" id="tab-scan" class="flex-1 py-4 text-sm font-bold text-slate-400 hover:text-slate-600 dark:hover:text-slate-200 border-b-2 border-transparent transition-colors flex items-center justify-center gap-2">
                    <svg class="w-4 h-4" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M3 9a2 2 0 012-2h.93a2 2 0 001.664-.89l.812-1.22A2 2 0 0110.07 4h3.86a2 2 0 011.664.89l.812 1.22A2 2 0 0018.07 7H19a2 2 0 012 2v9a2 2 0 01-2 2H5a2 2 0 01-2-2V9z"></path><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M15 13a3 3 0 11-6 0 3 3 0 016 0z"></path></svg>
                    AI Scan
                </button>
            </div>

            <button onclick="app.closeModal()" class="absolute top-3 right-3 p-2 text-slate-400 hover:text-slate-600 dark:hover:text-slate-200 transition-colors z-10">
                <svg class="w-5 h-5" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M6 18L18 6M6 6l12 12"></path></svg>
            </button>
            
            <div class="p-6 sm:p-8 overflow-y-auto custom-scrollbar">
                
                <!-- Manual Entry Form -->
                <div id="view-manual">
                    <h2 class="text-2xl font-display font-bold mb-1 text-slate-800 dark:text-white" data-i18n="add_provision">Add Provision</h2>
                    <p class="text-sm text-slate-500 dark:text-slate-400 mb-6" data-i18n="add_desc">Enter details to track freshness.</p>
                    
                    <form id="add-form" onsubmit="app.handleFormSubmit(event)" class="space-y-4">
                        <input type="hidden" id="item-id">
                        
                        <div class="space-y-1">
                            <label class="text-xs font-bold uppercase tracking-wider text-slate-500 ml-1" data-i18n="label_name">Product Name</label>
                            <input type="text" id="inp-name" required placeholder="e.g., Organic Milk" class="w-full glass-input px-4 py-3 rounded-xl text-slate-800 dark:text-white placeholder-slate-400">
                        </div>

                        <div class="grid grid-cols-2 gap-4">
                            <div class="space-y-1">
                                <label class="text-xs font-bold uppercase tracking-wider text-slate-500 ml-1" data-i18n="label_category">Category</label>
                                <div class="relative">
                                    <select id="inp-category" class="w-full glass-input px-4 py-3 rounded-xl text-slate-800 dark:text-white appearance-none cursor-pointer">
                                        <option value="dairy" data-i18n="cat_dairy">Dairy & Eggs</option>
                                        <option value="fruit" data-i18n="cat_fruit">Fruit & Veg</option>
                                        <option value="meat" data-i18n="cat_meat">Meat & Fish</option>
                                        <option value="grain" data-i18n="cat_grain">Grains & Bread</option>
                                        <option value="drink" data-i18n="cat_drink">Beverages</option>
                                        <option value="snack" data-i18n="cat_snack">Snacks</option>
                                        <option value="frozen" data-i18n="cat_frozen">Frozen</option>
                                        <option value="canned" data-i18n="cat_canned">Pantry & Canned</option>
                                        <option value="condiment" data-i18n="cat_condiment">Condiments</option>
                                        <option value="other" data-i18n="cat_other">Other</option>
                                    </select>
                                    <div class="absolute right-3 top-1/2 -translate-y-1/2 pointer-events-none text-slate-400">
                                        <svg class="w-4 h-4" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M19 9l-7 7-7-7"></path></svg>
                                    </div>
                                </div>
                            </div>
                            <div class="space-y-1">
                                <label class="text-xs font-bold uppercase tracking-wider text-slate-500 ml-1" data-i18n="label_qty">Quantity</label>
                                <input type="number" id="inp-quantity" value="1" min="1" class="w-full glass-input px-4 py-3 rounded-xl text-slate-800 dark:text-white">
                            </div>
                        </div>

                        <div class="grid grid-cols-2 gap-4">
                            <div class="space-y-1">
                                <label class="text-xs font-bold uppercase tracking-wider text-slate-500 ml-1" data-i18n="label_date">Best Before Date</label>
                                <input type="date" id="inp-date" required class="w-full glass-input px-4 py-3 rounded-xl text-slate-800 dark:text-white">
                            </div>
                             <div class="space-y-1">
                                <label class="text-xs font-bold uppercase tracking-wider text-slate-500 ml-1" data-i18n="label_price">Price</label>
                                <input type="number" id="inp-price" step="0.01" min="0" placeholder="0.00" class="w-full glass-input px-4 py-3 rounded-xl text-slate-800 dark:text-white">
                            </div>
                        </div>

                        <div class="space-y-1">
                            <label class="text-xs font-bold uppercase tracking-wider text-slate-500 ml-1" data-i18n="label_note">Notes (Optional)</label>
                            <textarea id="inp-note" rows="2" placeholder="Brand, store, storage info..." class="w-full glass-input px-4 py-3 rounded-xl text-slate-800 dark:text-white placeholder-slate-400 resize-none"></textarea>
                        </div>

                        <div class="pt-2">
                            <button type="submit" class="w-full bg-gradient-to-r from-nature-600 to-nature-500 hover:from-nature-700 hover:to-nature-600 text-white font-bold py-3.5 rounded-xl shadow-lg shadow-nature-500/30 transform transition hover:-translate-y-0.5 active:translate-y-0">
                                <span data-i18n="btn_register">Register Item</span>
                            </button>
                        </div>
                    </form>
                </div>

                <!-- AI Scan View -->
                <div id="view-scan" class="hidden space-y-4">
                    <!-- API Settings -->
                    <details class="group bg-slate-50 dark:bg-slate-900/50 rounded-xl p-3 text-sm">
                        <summary class="cursor-pointer font-bold text-slate-500 flex justify-between items-center">
                            <span>⚙️ AI Configuration</span>
                            <svg class="w-4 h-4 transition-transform group-open:rotate-180" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M19 9l-7 7-7-7"></path></svg>
                        </summary>
                        <div class="mt-3 space-y-3">
                            <div>
                                <label class="block mb-1 font-bold text-xs text-slate-400">API Key</label>
                                <input type="password" id="apiKey" value="sk-F6BSwzZu9hsba8uR0P5FKaaiYMEKpFBG01rLWG2rwyqvmRCQ" class="w-full glass-input px-3 py-2 rounded-lg text-slate-800 dark:text-white">
                            </div>
                            <div>
                                <label class="block mb-1 font-bold text-xs text-slate-400">Provider URL</label>
                                <input type="text" id="apiUrl" value="https://api.chatanywhere.tech/v1/chat/completions" class="w-full glass-input px-3 py-2 rounded-lg text-slate-800 dark:text-white">
                            </div>
                            <div>
                                <label class="block mb-1 font-bold text-xs text-slate-400">Model Name</label>
                                <input type="text" id="modelName" value="gpt-5-mini" class="w-full glass-input px-3 py-2 rounded-lg text-slate-800 dark:text-white">
                            </div>
                        </div>
                    </details>

                    <!-- File Upload -->
                    <div class="relative border-2 border-dashed border-slate-300 dark:border-slate-600 rounded-2xl p-6 text-center hover:bg-slate-50 dark:hover:bg-slate-800/50 transition-colors" id="drop-zone">
                        <input type="file" id="fileInput" accept="image/*" capture="environment" class="absolute inset-0 w-full h-full opacity-0 cursor-pointer z-10">
                        <div id="upload-placeholder">
                            <div class="w-12 h-12 bg-nature-100 dark:bg-nature-900/30 text-nature-600 rounded-full flex items-center justify-center mx-auto mb-3">
                                <svg class="w-6 h-6" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M3 9a2 2 0 012-2h.93a2 2 0 001.664-.89l.812-1.22A2 2 0 0110.07 4h3.86a2 2 0 011.664.89l.812 1.22A2 2 0 0018.07 7H19a2 2 0 012 2v9a2 2 0 01-2 2H5a2 2 0 01-2-2V9z"></path><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M15 13a3 3 0 11-6 0 3 3 0 016 0z"></path></svg>
                            </div>
                            <h3 class="font-bold text-slate-700 dark:text-slate-300">Tap to Scan Receipt</h3>
                            <p class="text-xs text-slate-400 mt-1">Supports JPG, PNG (Max 5MB)</p>
                        </div>
                        <div id="preview-container" class="hidden relative rounded-lg overflow-hidden max-h-48">
                            <img id="preview-img" class="w-full object-cover">
                            <div id="scan-overlay" class="absolute inset-0 hidden">
                                <div class="scan-line"></div>
                            </div>
                        </div>
                    </div>

                    <!-- Status -->
                    <div id="scan-status" class="text-center text-sm font-medium text-slate-500 italic min-h-[20px]"></div>

                    <!-- Scan Results List -->
                    <div id="scan-results" class="hidden space-y-3">
                        <div class="flex justify-between items-center">
                            <h3 class="font-bold text-slate-800 dark:text-white text-sm">Detected Items</h3>
                            <span class="text-xs bg-nature-100 text-nature-700 px-2 py-1 rounded-md" id="scan-count">0 items</span>
                        </div>
                        <div id="detected-items-list" class="space-y-2 max-h-48 overflow-y-auto pr-1"></div>
                        <button onclick="app.importScannedItems()" class="w-full bg-nature-600 hover:bg-nature-700 text-white font-bold py-3 rounded-xl shadow-lg shadow-nature-500/30">
                            Import All Items
                        </button>
                    </div>
                </div>

            </div>
        </div>
    </div>

    <!-- Toast Container -->
    <div id="toast-container" class="fixed bottom-4 left-1/2 -translate-x-1/2 z-[60] flex flex-col gap-2 pointer-events-none w-full max-w-sm px-4"></div>

    <!-- JavaScript Logic -->
    <script>
        /**
         * FreshKeep Logic
         * Uses Classes for modularity and maintainability.
         */

        // Dictionaries
        const Translations = {
            en: {
                app_title: "FreshKeep",
                total_inventory: "Total Inventory",
                good_standing: "In Good Standing",
                action_required: "Action Required",
                filters: "Filters",
                filter_all: "All Items",
                filter_expiring: "Expiring Soon",
                filter_expired: "Expired",
                filter_history: "History / Consumed",
                sort_by: "Sort By",
                sort_date_asc: "Date (Soonest First)",
                sort_date_desc: "Date (Latest First)",
                sort_name_asc: "Name (A-Z)",
                sort_category: "Category",
                empty_title: "Pantry is Empty",
                empty_desc: "Start tracking your food to reduce waste and save money. Add your first item now!",
                add_first_item: "Add First Item",
                load_demo: "Load Demo Data",
                notifications: "Notifications",
                clear_all: "Clear all",
                no_alerts: "No new alerts",
                add_item: "Add Item",
                add_provision: "Add Provision",
                add_desc: "Enter details to track freshness.",
                label_name: "Product Name",
                label_category: "Category",
                label_qty: "Quantity",
                label_date: "Best Before Date",
                label_price: "Price",
                label_note: "Notes (Optional)",
                btn_register: "Register Item",
                cat_dairy: "Dairy & Eggs",
                cat_fruit: "Fruit & Veg",
                cat_meat: "Meat & Fish",
                cat_grain: "Grains & Bread",
                cat_drink: "Beverages",
                cat_snack: "Snacks",
                cat_frozen: "Frozen",
                cat_canned: "Pantry & Canned",
                cat_condiment: "Condiments",
                cat_other: "Other",
                unit: "unit(s)",
                status_expired: "Expired",
                status_fresh: "Fresh",
                status_today: "Expires today",
                status_warning: "Expires in",
                days: "days",
                days_left: "days left",
                days_ago: "days ago",
                msg_added: "added successfully!",
                msg_removed: "Item removed",
                msg_consumed: "Consumed",
                msg_fully_consumed: "Fully consumed",
                undo: "Undo",
                history_consumed: "Consumed on",
                history_deleted: "Deleted on"
            },
            zh: {
                app_title: "FreshKeep - 鮮度管家",
                total_inventory: "庫存總數",
                good_standing: "狀態良好",
                action_required: "需要注意",
                filters: "篩選",
                filter_all: "所有項目",
                filter_expiring: "即將過期",
                filter_expired: "已過期",
                filter_history: "歷史記錄 / 已消耗",
                sort_by: "排序方式",
                sort_date_asc: "日期 (由近到遠)",
                sort_date_desc: "日期 (由遠到近)",
                sort_name_asc: "名稱 (A-Z)",
                sort_category: "類別",
                empty_title: "庫存是空的",
                empty_desc: "開始追蹤食物，減少浪費並省錢。立即新增您的第一個項目！",
                add_first_item: "新增第一個項目",
                load_demo: "載入範例資料",
                notifications: "通知",
                clear_all: "清除全部",
                no_alerts: "沒有新通知",
                add_item: "新增項目",
                add_provision: "新增庫存",
                add_desc: "輸入詳細資訊以追蹤鮮度。",
                label_name: "產品名稱",
                label_category: "類別",
                label_qty: "數量",
                label_date: "有效期限",
                label_price: "價格",
                label_note: "備註 (選填)",
                btn_register: "註冊項目",
                cat_dairy: "乳製品與蛋類",
                cat_fruit: "水果與蔬菜",
                cat_meat: "肉類與魚類",
                cat_grain: "穀物與麵包",
                cat_drink: "飲料",
                cat_snack: "零食",
                cat_frozen: "冷凍食品",
                cat_canned: "罐頭與乾貨",
                cat_condiment: "調味品",
                cat_other: "其他",
                unit: "個",
                status_expired: "已過期",
                status_fresh: "新鮮",
                status_today: "今天過期",
                status_warning: "還有",
                days: "天",
                days_left: "天過期",
                days_ago: "天前過期",
                msg_added: "新增成功！",
                msg_removed: "項目已移除",
                msg_consumed: "已消耗",
                msg_fully_consumed: "已完全消耗",
                undo: "復原",
                history_consumed: "消耗於",
                history_deleted: "刪除於"
            }
        };

        // Utility Functions
        const Utils = {
            generateId: () => Math.random().toString(36).substr(2, 9),
            
            formatDate: (dateStr, lang) => {
                const options = { year: 'numeric', month: 'short', day: 'numeric' };
                // Use zh-TW for zh, en-US for en
                const locale = lang === 'zh' ? 'zh-TW' : 'en-US';
                return new Date(dateStr).toLocaleDateString(locale, options);
            },

            getDaysRemaining: (dateStr) => {
                const today = new Date();
                today.setHours(0, 0, 0, 0);
                const target = new Date(dateStr);
                target.setHours(0, 0, 0, 0);
                const diffTime = target - today;
                return Math.ceil(diffTime / (1000 * 60 * 60 * 24));
            },

            getStatus: (days) => {
                if (days < 0) return 'expired';
                if (days === 0) return 'today';
                if (days <= 3) return 'warning';
                return 'fresh';
            },

            getCategoryIcon: (cat) => {
                const map = {
                    'dairy': '<svg class="w-6 h-6" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path d="M20.59 13.41l-7.17 7.17a2 2 0 0 1-2.83 0L2 12V2h10l8.59 8.59a2 2 0 0 1 0 2.82z"></path><line x1="7" y1="7" x2="7.01" y2="7"></line></svg>',
                    'fruit': '<svg class="w-6 h-6" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path d="M12 20a8 8 0 1 0 0-16 8 8 0 0 0 0 16z"></path><path d="M12 2v2"></path><path d="M12 22v-2"></path><path d="M2 12h2"></path><path d="M20 12h2"></path><path d="M4.93 4.93l1.41 1.41"></path><path d="M17.66 17.66l1.41 1.41"></path><path d="M4.93 19.07l1.41-1.41"></path><path d="M17.66 6.34l1.41-1.41"></path></svg>',
                    'meat': '<svg class="w-6 h-6" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path d="M19 3v12h-5c-.001-.265.05-1.05-.5-1.5c-1.5-1.5-4-1.5-5.5 0S6.5 13.5 6 15c-.5.5-.499 1.235-.5 1.5H1v4h22v-4h-2.5c-1 0-2-.5-2.5-1.5l-2.5-2.5"></path></svg>',
                    'grain': '<svg class="w-6 h-6" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path d="M6 8a4 4 0 0 1 4-4h4a4 4 0 0 1 4 4v12H6Z"></path><path d="M2 12h20"></path><path d="M10 4v4"></path><path d="M14 4v4"></path></svg>',
                    'drink': '<svg class="w-6 h-6" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path d="M17 5V3H7v2c0 2.21 2.5 4 4.5 4H12c2 0 4.5-1.79 4.5-4Z"></path><path d="M17 11h-1v-2h1c1 0 2 .9 2 2v6c0 1.1-.9 2-2 2H7c-1.1 0-2-.9-2-2v-6c0-1.1.9-2 2-2h1v2H7"></path></svg>',
                    'snack': '<svg class="w-6 h-6" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path d="M12 2L2 7l10 5 10-5-10-5z"></path><path d="M2 17l10 5 10-5M2 12l10 5 10-5"></path></svg>',
                    'frozen': '<svg class="w-6 h-6" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M12 3v18m-9-9h18m-2.5-6.5l-13 13m0-13l13 13"></path></svg>',
                    'canned': '<svg class="w-6 h-6" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M4 8V4m0 0h16v4m-16 0v12a2 2 0 002 2h12a2 2 0 002-2V8m-16 0h16"></path></svg>',
                    'condiment': '<svg class="w-6 h-6" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M19.428 15.428a2 2 0 00-1.022-.547l-2.387-.477a6 6 0 00-3.86.517l-.318.158a6 6 0 01-3.86.517L6.05 15.21a2 2 0 00-1.806.547M8 4h8l-1 1v5.172a2 2 0 00.586 1.414l5 5c1.26 1.26.367 3.414-1.415 3.414H4.828c-1.782 0-2.674-2.154-1.414-3.414l5-5A2 2 0 009 10.172V5L8 4z"></path></svg>',
                    'other': '<svg class="w-6 h-6" fill="none" stroke="currentColor" viewBox="0 0 24 24"><rect x="3" y="3" width="7" height="7"></rect><rect x="14" y="3" width="7" height="7"></rect><rect x="14" y="14" width="7" height="7"></rect><rect x="3" y="14" width="7" height="7"></rect></svg>'
                };
                return map[cat] || map['other'];
            }
        };

        // Notification Service
        class NotificationService {
            constructor(appInstance) {
                this.app = appInstance;
                this.alerts = [];
                this.badge = document.getElementById('notification-badge');
                this.list = document.getElementById('notification-list');
                this.container = document.getElementById('toast-container');
                this.isOpen = false;

                document.getElementById('notification-btn').addEventListener('click', (e) => {
                    e.stopPropagation();
                    this.toggleDropdown();
                });
                
                document.addEventListener('click', (e) => {
                    const dropdown = document.getElementById('notification-dropdown');
                    const btn = document.getElementById('notification-btn');
                    if (!dropdown.contains(e.target) && !btn.contains(e.target)) {
                        dropdown.classList.add('hidden');
                        this.isOpen = false;
                    }
                });
            }

            addAlert(item, type) {
                const alert = {
                    id: Utils.generateId(),
                    item: item,
                    type: type,
                    timestamp: new Date()
                };
                this.alerts.unshift(alert);
                this.updateBadge();
                this.renderDropdown();
            }

            updateBadge() {
                if (this.alerts.length > 0) {
                    this.badge.classList.remove('hidden');
                } else {
                    this.badge.classList.add('hidden');
                }
            }

            toggleDropdown() {
                const dropdown = document.getElementById('notification-dropdown');
                this.isOpen = !this.isOpen;
                if (this.isOpen) {
                    dropdown.classList.remove('hidden');
                    this.renderDropdown();
                } else {
                    dropdown.classList.add('hidden');
                }
            }

            renderDropdown() {
                const t = Translations[this.app.lang];
                if (this.alerts.length === 0) {
                    this.list.innerHTML = `<li class="text-center text-sm text-slate-400 py-4">${t.no_alerts}</li>`;
                    return;
                }

                this.list.innerHTML = this.alerts.map(alert => {
                    let msg = '';
                    const days = Utils.getDaysRemaining(alert.item.expiryDate);
                    if (alert.type === 'expired') {
                        msg = `<b>${alert.item.name}</b> ${t.status_expired} ${t.status_today}`; 
                    } else {
                        msg = `<b>${alert.item.name}</b> ${t.status_warning} ${days} ${t.days}`;
                    }

                    return `
                    <li class="p-3 mb-2 rounded-lg bg-white dark:bg-slate-700/50 border border-slate-100 dark:border-slate-600 shadow-sm flex gap-3 items-start">
                        <div class="mt-1 w-2 h-2 rounded-full ${alert.type === 'expired' ? 'bg-red-500' : 'bg-orange-500'} flex-shrink-0"></div>
                        <div>
                            <p class="text-sm text-slate-700 dark:text-slate-200">${msg}</p>
                            <span class="text-xs text-slate-400 block mt-1">${alert.timestamp.toLocaleTimeString([], {hour: '2-digit', minute:'2-digit'})}</span>
                        </div>
                    </li>
                `}).join('');
            }

            clear() {
                this.alerts = [];
                this.updateBadge();
                this.renderDropdown();
            }

            showToast(message, type = 'success', undoCallback = null) {
                const t = Translations[this.app.lang];
                const toastId = 'toast-' + Utils.generateId();
                const toast = document.createElement('div');
                const colors = type === 'success' ? 'bg-nature-600' : (type === 'info' ? 'bg-slate-700' : 'bg-danger-500');
                
                let undoHtml = '';
                if (undoCallback) {
                    undoHtml = `
                        <button id="undo-${toastId}" class="ml-auto bg-white/20 hover:bg-white/30 text-white text-xs px-3 py-1.5 rounded-lg font-bold transition-colors">
                            ${t.undo}
                        </button>
                    `;
                }

                toast.id = toastId;
                toast.className = `pointer-events-auto flex items-center gap-3 ${colors} text-white px-5 py-4 rounded-xl shadow-xl transform transition-all duration-300 translate-y-20 opacity-0 w-full`;
                toast.innerHTML = `
                    <div class="flex items-center gap-2 flex-grow">
                        <svg class="w-5 h-5 flex-shrink-0" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="${type === 'success' ? 'M5 13l4 4L19 7' : 'M12 9v2m0 4h.01m-6.938 4h13.856c1.54 0 2.502-1.667 1.732-3L13.732 4c-.77-1.333-2.694-1.333-3.464 0L3.34 16c-.77 1.333.192 3 1.732 3z'}"></path></svg>
                        <span class="font-medium text-sm">${message}</span>
                    </div>
                    ${undoHtml}
                `;

                this.container.appendChild(toast);

                if (undoCallback) {
                    document.getElementById(`undo-${toastId}`).addEventListener('click', () => {
                        undoCallback();
                        toast.classList.add('opacity-0', 'translate-y-2'); // Fade out immediate
                        setTimeout(() => toast.remove(), 300);
                    });
                }

                requestAnimationFrame(() => {
                    toast.classList.remove('translate-y-20', 'opacity-0');
                });

                setTimeout(() => {
                    if (document.body.contains(toast)) {
                        toast.classList.add('translate-y-20', 'opacity-0');
                        setTimeout(() => toast.remove(), 300);
                    }
                }, 4000);
            }
        }

        // Main App Class
        class App {
            constructor() {
                this.lang = 'zh'; // Default Language Traditional Chinese
                
                // Initialize from LocalStorage or default empty
                const storedItems = localStorage.getItem('freshkeep_items');
                this.items = storedItems ? JSON.parse(storedItems) : [];
                
                const storedHistory = localStorage.getItem('freshkeep_history');
                this.history = storedHistory ? JSON.parse(storedHistory) : [];
                // Rehydrate dates in history
                this.history.forEach(h => h.actionDate = new Date(h.actionDate));

                this.currentFilter = 'all';
                this.currentSort = 'date-asc';
                this.notif = new NotificationService(this);
                this.scannedItems = []; // Temp storage for AI scan
                
                document.getElementById('inp-date').valueAsDate = new Date();
                this.updateDateDisplay();
                this.initTheme();
                this.updateUIText(); // Apply initial language

                // Setup File Input Listener
                document.getElementById('fileInput').addEventListener('change', (e) => this.handleFileScan(e));
                
                this.render(); // Initial render
            }

            saveData() {
                localStorage.setItem('freshkeep_items', JSON.stringify(this.items));
                localStorage.setItem('freshkeep_history', JSON.stringify(this.history));
            }

            updateDateDisplay() {
                const locale = this.lang === 'zh' ? 'zh-TW' : 'en-US';
                document.getElementById('current-date').innerText = new Date().toLocaleDateString(locale, { weekday: 'long', year: 'numeric', month: 'long', day: 'numeric' });
            }

            toggleLang() {
                this.lang = this.lang === 'en' ? 'zh' : 'en';
                document.getElementById('lang-display').innerText = this.lang === 'en' ? 'EN' : '繁中';
                this.updateUIText();
                this.updateDateDisplay();
                this.render(); 
            }

            updateUIText() {
                const t = Translations[this.lang];
                document.querySelectorAll('[data-i18n]').forEach(el => {
                    const key = el.getAttribute('data-i18n');
                    if (t[key]) {
                        if (el.tagName === 'INPUT' || el.tagName === 'TEXTAREA') {
                            el.placeholder = t[key];
                        } else {
                            el.innerText = t[key];
                        }
                    }
                });
                const select = document.getElementById('inp-category');
                Array.from(select.options).forEach(opt => {
                    const key = 'cat_' + opt.value;
                    if(t[key]) opt.text = t[key];
                });
            }

            initTheme() {
                const toggleBtn = document.getElementById('theme-toggle');
                if (window.matchMedia && window.matchMedia('(prefers-color-scheme: dark)').matches) {
                    document.documentElement.classList.add('dark');
                }
                toggleBtn.addEventListener('click', () => {
                    document.documentElement.classList.toggle('dark');
                });
            }

            addItem(item, isUndo = false) {
                if (!isUndo) {
                    // Try to find an identical existing item to merge
                    const existingItem = this.items.find(i => 
                        i.name.trim().toLowerCase() === item.name.trim().toLowerCase() &&
                        i.category === item.category &&
                        i.expiryDate.split('T')[0] === item.expiryDate.split('T')[0] && // Compare date part of ISO string
                        i.price === item.price
                    );

                    if (existingItem) {
                        existingItem.quantity = Number(existingItem.quantity) + Number(item.quantity);
                        this.processNotifications(existingItem);
                        this.saveData();
                        this.notif.showToast(`${item.name} quantity updated (+${item.quantity})`);
                        this.render();
                        return;
                    }
                }

                this.items.push(item);
                this.saveData();
                if (!isUndo) {
                    this.processNotifications(item);
                    this.notif.showToast(`${item.name} ${Translations[this.lang].msg_added}`);
                }
                this.render();
            }

            consumeItem(id) {
                const itemIndex = this.items.findIndex(i => i.id === id);
                if (itemIndex === -1) return;
                const item = this.items[itemIndex];
                const t = Translations[this.lang];

                // Always record 1 unit consumed history regardless of total qty
                this.addToHistory(item, 'consumed', 1);

                if (item.quantity > 1) {
                    item.quantity--;
                    this.saveData();
                    this.render();
                    this.notif.showToast(`1 ${t.unit} ${item.name} ${t.msg_consumed}`, 'success');
                } else {
                    this.items.splice(itemIndex, 1);
                    this.saveData();
                    this.render();
                    this.notif.showToast(
                        `${item.name} ${t.msg_fully_consumed}`, 
                        'success', 
                        () => this.undoAction(item)
                    );
                }
            }

            deleteItem(id) {
                const itemIndex = this.items.findIndex(i => i.id === id);
                if (itemIndex === -1) return;
                const item = this.items[itemIndex];
                this.items.splice(itemIndex, 1);
                this.addToHistory(item, 'deleted');
                this.saveData();
                this.render();
                const t = Translations[this.lang];
                this.notif.showToast(
                    `${t.msg_removed}`, 
                    'info',
                    () => this.undoAction(item)
                );
            }

            addToHistory(item, action, qty = 0) {
                this.history.unshift({
                    ...item,
                    quantity: qty > 0 ? qty : item.quantity,
                    action: action,
                    actionDate: new Date()
                });
                this.saveData();
            }

            undoAction(item) {
                this.items.push(item);
                if (this.history.length > 0 && this.history[0].id === item.id) {
                    this.history.shift();
                }
                this.saveData();
                this.render();
            }

            processNotifications(item) {
                const days = Utils.getDaysRemaining(item.expiryDate);
                if (days <= 3 && days >= 0) {
                    this.notif.addAlert(item, 'warning');
                } else if (days < 0) {
                    this.notif.addAlert(item, 'expired');
                }
            }

            clearNotifications() {
                this.notif.clear();
            }

            loadMockData() {
                const mockItems = [
                    { id: '1', name: '希臘優格', category: 'dairy', expiryDate: new Date(Date.now() + 2 * 24 * 60 * 60 * 1000).toISOString(), quantity: 2, price: 4.50, note: '低脂' },
                    { id: '2', name: '雞胸肉', category: 'meat', expiryDate: new Date(Date.now() - 1 * 24 * 60 * 60 * 1000).toISOString(), quantity: 1, price: 8.99, note: '如不使用請冷凍' },
                    { id: '3', name: '酪梨', category: 'fruit', expiryDate: new Date(Date.now() + 5 * 24 * 60 * 60 * 1000).toISOString(), quantity: 3, price: 1.25, note: '熟' },
                    { id: '4', name: '酸種麵包', category: 'grain', expiryDate: new Date(Date.now() + 1 * 24 * 60 * 60 * 1000).toISOString(), quantity: 1, price: 5.49, note: '' },
                    { id: '5', name: '柳橙汁', category: 'drink', expiryDate: new Date(Date.now() + 14 * 24 * 60 * 60 * 1000).toISOString(), quantity: 1, price: 3.99, note: '未開封' },
                    { id: '6', name: '罐裝黑豆', category: 'canned', expiryDate: new Date(Date.now() + 300 * 24 * 60 * 60 * 1000).toISOString(), quantity: 4, price: 0.99, note: '' },
                    { id: '7', name: '醬油', category: 'condiment', expiryDate: new Date(Date.now() + 100 * 24 * 60 * 60 * 1000).toISOString(), quantity: 1, price: 6.50, note: '' },
                ];
                
                // Clear existing
                this.items = [];
                mockItems.forEach(i => {
                    this.items.push(i);
                    this.processNotifications(i);
                });
                this.saveData(); // Save to local storage
                this.render();
                this.notif.showToast(this.lang === 'en' ? 'Demo data loaded' : '範例資料已載入');
            }

            setFilter(filter) {
                this.currentFilter = filter;
                document.querySelectorAll('.filter-btn').forEach(btn => btn.classList.remove('active', 'bg-slate-100', 'dark:bg-slate-800'));
                const btns = document.querySelectorAll('.filter-btn');
                if (filter === 'all') btns[0].classList.add('bg-slate-100', 'dark:bg-slate-800');
                if (filter === 'expiring') btns[1].classList.add('bg-slate-100', 'dark:bg-slate-800');
                if (filter === 'expired') btns[2].classList.add('bg-slate-100', 'dark:bg-slate-800');
                if (filter === 'history') btns[3].classList.add('bg-slate-100', 'dark:bg-slate-800');
                this.render();
            }

            sortItems(criteria) {
                this.currentSort = criteria;
                this.render();
            }

            getFilteredAndSortedItems() {
                if (this.currentFilter === 'history') return [...this.history]; 

                let filtered = this.items;

                if (this.currentFilter === 'expiring') {
                    filtered = filtered.filter(i => {
                        const days = Utils.getDaysRemaining(i.expiryDate);
                        return days >= 0 && days <= 3;
                    });
                } else if (this.currentFilter === 'expired') {
                    filtered = filtered.filter(i => Utils.getDaysRemaining(i.expiryDate) < 0);
                }

                return filtered.sort((a, b) => {
                    const dateA = new Date(a.expiryDate);
                    const dateB = new Date(b.expiryDate);
                    if (this.currentSort === 'date-asc') return dateA - dateB;
                    if (this.currentSort === 'date-desc') return dateB - dateA;
                    if (this.currentSort === 'name-asc') return a.name.localeCompare(b.name);
                    if (this.currentSort === 'category-asc') return a.category.localeCompare(b.category);
                    return 0;
                });
            }

            render() {
                const t = Translations[this.lang];
                const total = this.items.length;
                const expired = this.items.filter(i => Utils.getDaysRemaining(i.expiryDate) < 0).length;
                const expiring = this.items.filter(i => {
                    const d = Utils.getDaysRemaining(i.expiryDate);
                    return d >= 0 && d <= 3;
                }).length;
                const fresh = total - expired - expiring;

                document.getElementById('stat-total').innerText = total;
                document.getElementById('stat-fresh').innerText = fresh;
                document.getElementById('stat-expiring').innerText = expiring;

                document.getElementById('count-all').innerText = total;
                document.getElementById('count-expiring').innerText = expiring;
                document.getElementById('count-expired').innerText = expired;

                const listContainer = document.getElementById('food-grid');
                const emptyState = document.getElementById('empty-state');
                const displayItems = this.getFilteredAndSortedItems();

                listContainer.innerHTML = '';

                // Change layout based on filter
                if (this.currentFilter === 'history') {
                    listContainer.className = 'flex flex-col gap-3'; // List View
                } else {
                    listContainer.className = 'grid grid-cols-1 md:grid-cols-2 xl:grid-cols-3 gap-4'; // Grid View
                }

                if (displayItems.length === 0 && this.currentFilter !== 'history') {
                    listContainer.classList.add('hidden');
                    emptyState.classList.remove('hidden');
                } else {
                    listContainer.classList.remove('hidden');
                    emptyState.classList.add('hidden');

                    if (this.currentFilter === 'history' && displayItems.length === 0) {
                        listContainer.innerHTML = `<div class="col-span-full text-center text-slate-400 py-10">${t.no_alerts || 'No history'}</div>`;
                        return;
                    }

                    displayItems.forEach((item, index) => {
                        if (this.currentFilter === 'history') {
                            this.renderHistoryCard(item, index, listContainer);
                        } else {
                            this.renderActiveCard(item, index, listContainer);
                        }
                    });
                }
            }

            renderHistoryCard(item, index, container) {
                const t = Translations[this.lang];
                const el = document.createElement('div');
                el.className = `food-card glass-panel p-4 rounded-xl flex items-center justify-between border-l-4 border-slate-300 dark:border-slate-600`;
                
                const actionText = item.action === 'consumed' ? t.history_consumed : t.history_deleted;
                const actionColor = item.action === 'consumed' ? 'text-green-600 dark:text-green-400' : 'text-red-600 dark:text-red-400';
                
                el.innerHTML = `
                    <div class="flex items-center gap-4">
                        <div class="p-2 rounded-lg bg-slate-100 dark:bg-slate-700 text-slate-500">
                            ${Utils.getCategoryIcon(item.category)}
                        </div>
                        <div>
                            <h3 class="font-bold text-slate-700 dark:text-slate-300">${item.name}</h3>
                            <p class="text-xs text-slate-400">${actionText} ${item.actionDate.toLocaleDateString()} • ${item.quantity} ${t.unit}</p>
                        </div>
                    </div>
                    <span class="text-xs font-bold px-2 py-1 rounded bg-slate-100 dark:bg-slate-800 ${actionColor}">
                        ${item.action === 'consumed' ? t.msg_consumed : t.msg_removed}
                    </span>
                `;
                container.appendChild(el);
            }

            renderActiveCard(item, index, container) {
                const t = Translations[this.lang];
                const daysLeft = Utils.getDaysRemaining(item.expiryDate);
                const status = Utils.getStatus(daysLeft);
                
                let statusColor = 'bg-nature-500';
                let statusText = `${daysLeft} ${t.days_left}`;
                let cardBorder = 'border-transparent';

                if (status === 'expired') {
                    statusColor = 'bg-danger-500';
                    statusText = `${t.status_expired} ${Math.abs(daysLeft)} ${t.days_ago}`;
                    cardBorder = 'border-red-500/50';
                } else if (status === 'today') {
                    statusColor = 'bg-warning-500';
                    statusText = t.status_today;
                    cardBorder = 'border-orange-500/50';
                } else if (status === 'warning') {
                    statusColor = 'bg-warning-400';
                    statusText = `${t.status_warning} ${daysLeft} ${t.days}`;
                    cardBorder = 'border-orange-300/50';
                } else {
                    statusText = `${daysLeft} ${t.days_left}`;
                }

                const catColors = {
                    dairy: 'bg-blue-100 text-blue-600 dark:bg-blue-900/30 dark:text-blue-400',
                    fruit: 'bg-green-100 text-green-600 dark:bg-green-900/30 dark:text-green-400',
                    meat: 'bg-red-100 text-red-600 dark:bg-red-900/30 dark:text-red-400',
                    grain: 'bg-yellow-100 text-yellow-600 dark:bg-yellow-900/30 dark:text-yellow-400',
                    drink: 'bg-purple-100 text-purple-600 dark:bg-purple-900/30 dark:text-purple-400',
                    snack: 'bg-pink-100 text-pink-600 dark:bg-pink-900/30 dark:text-pink-400',
                    frozen: 'bg-cyan-100 text-cyan-600 dark:bg-cyan-900/30 dark:text-cyan-400',
                    canned: 'bg-stone-100 text-stone-600 dark:bg-stone-900/30 dark:text-stone-400',
                    condiment: 'bg-amber-100 text-amber-600 dark:bg-amber-900/30 dark:text-amber-400',
                    other: 'bg-gray-100 text-gray-600 dark:bg-gray-700 dark:text-gray-400'
                };
                const catClass = catColors[item.category] || catColors['other'];
                const priceDisplay = item.price ? `<span class="ml-auto text-sm font-bold text-slate-600 dark:text-slate-300 bg-white/50 dark:bg-black/20 px-2 py-1 rounded-lg">$${Number(item.price).toFixed(2)}</span>` : '';

                const el = document.createElement('div');
                el.className = `food-card glass-panel p-5 rounded-2xl relative group border ${cardBorder} animate-slide-up cursor-pointer hover:border-nature-400 transition-all`;
                el.style.animationDelay = `${index * 0.05}s`;
                el.onclick = () => app.editItem(item.id);
                
                el.innerHTML = `
                    <div class="flex justify-between items-start mb-4">
                        <div class="p-3 rounded-xl ${catClass}">
                            ${Utils.getCategoryIcon(item.category)}
                        </div>
                        <div class="flex flex-col items-end gap-2">
                            <span class="inline-flex items-center gap-1.5 px-2.5 py-1 rounded-full text-xs font-semibold ${status === 'expired' ? 'bg-red-100 text-red-700' : (status === 'warning' || status === 'today' ? 'bg-orange-100 text-orange-700' : 'bg-green-100 text-nature-700')}">
                                <span class="w-1.5 h-1.5 rounded-full ${status === 'expired' ? 'bg-red-500' : (status === 'warning' || status === 'today' ? 'bg-orange-500' : 'bg-nature-500')}"></span>
                                ${status === 'expired' ? t.status_expired : t.status_fresh}
                            </span>
                            ${priceDisplay}
                        </div>
                    </div>
                    
                    <h3 class="text-lg font-bold text-slate-800 dark:text-white truncate" title="${item.name}">${item.name}</h3>
                    
                    <div class="flex items-center gap-2 text-sm text-slate-500 dark:text-slate-400 mt-1 mb-4">
                        <svg class="w-4 h-4" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M8 7V3m8 4V3m-9 8h10M5 21h14a2 2 0 002-2V7a2 2 0 00-2-2H5a2 2 0 00-2 2v12a2 2 0 002 2z"></path></svg>
                        <span>${Utils.formatDate(item.expiryDate, this.lang)}</span>
                        <span class="mx-1">•</span>
                        <span class="${status === 'expired' ? 'text-red-500 font-medium' : (status==='warning'?'text-orange-500 font-medium':'')}">${statusText}</span>
                    </div>

                    <div class="border-t border-slate-100 dark:border-slate-700 pt-3 flex justify-between items-center">
                        <span class="text-xs text-slate-400 font-medium bg-slate-100 dark:bg-slate-700/50 px-2 py-1 rounded-md">${item.quantity} ${t.unit}</span>
                        
                        <div class="flex items-center gap-1">
                            <button onclick="event.stopPropagation(); app.consumeItem('${item.id}')" class="text-slate-400 hover:text-nature-600 transition-colors p-2 rounded-lg hover:bg-nature-50 dark:hover:bg-nature-900/20" title="Consume">
                                <svg class="w-5 h-5" fill="currentColor" viewBox="0 0 24 24"><path d="M6,3V9a3,3,0,0,0,3,3v9a1,1,0,0,0,2,0V12a3,3,0,0,0,3-3V3a1,1,0,0,0-2,0V8a1,1,0,0,1-2,0V3A1,1,0,0,0,6,3Zm14,0a1,1,0,0,0-1,1V21a1,1,0,0,0,2,0V4A1,1,0,0,0,20,3Z"/></svg>
                            </button>
                            <button onclick="event.stopPropagation(); app.deleteItem('${item.id}')" class="text-slate-400 hover:text-red-500 transition-colors p-2 rounded-lg hover:bg-red-50 dark:hover:bg-red-900/20" title="Remove">
                                <svg class="w-5 h-5" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M19 7l-.867 12.142A2 2 0 0116.138 21H7.862a2 2 0 01-1.995-1.858L5 7m5 4v6m4-6v6m1-10V4a1 1 0 00-1-1h-4a1 1 0 00-1 1v3M4 7h16"></path></svg>
                            </button>
                        </div>
                    </div>
                    
                    ${item.note ? `<div class="mt-2 text-xs text-slate-400 italic truncate border-l-2 border-slate-200 pl-2">${item.note}</div>` : ''}
                `;
                container.appendChild(el);
            }

            // --- Form & Modal Handling ---
            openModal() {
                const modal = document.getElementById('add-modal');
                modal.classList.add('active');
                this.switchTab('manual'); // Default to manual
            }

            closeModal() {
                const modal = document.getElementById('add-modal');
                modal.classList.remove('active');
                document.getElementById('add-form').reset();
                document.getElementById('item-id').value = ''; // Clear ID
                document.getElementById('inp-date').valueAsDate = new Date();
                
                // Reset Scan UI
                this.scannedItems = [];
                document.getElementById('preview-container').classList.add('hidden');
                document.getElementById('upload-placeholder').classList.remove('hidden');
                document.getElementById('fileInput').value = '';
                document.getElementById('scan-results').classList.add('hidden');
                document.getElementById('scan-status').innerText = '';
                document.getElementById('scan-overlay').classList.add('hidden');
            }

            switchTab(tab) {
                const manualView = document.getElementById('view-manual');
                const scanView = document.getElementById('view-scan');
                const btnManual = document.getElementById('tab-manual');
                const btnScan = document.getElementById('tab-scan');

                if (tab === 'manual') {
                    manualView.classList.remove('hidden');
                    scanView.classList.add('hidden');
                    btnManual.classList.add('text-nature-600', 'border-nature-600');
                    btnManual.classList.remove('text-slate-400', 'border-transparent');
                    btnScan.classList.remove('text-nature-600', 'border-nature-600');
                    btnScan.classList.add('text-slate-400', 'border-transparent');
                } else {
                    manualView.classList.add('hidden');
                    scanView.classList.remove('hidden');
                    btnScan.classList.add('text-nature-600', 'border-nature-600');
                    btnScan.classList.remove('text-slate-400', 'border-transparent');
                    btnManual.classList.remove('text-nature-600', 'border-nature-600');
                    btnManual.classList.add('text-slate-400', 'border-transparent');
                }
            }

            editItem(id) {
                const item = this.items.find(i => i.id === id);
                if (!item) return;

                // Open modal first
                this.openModal();
                
                // Populate fields
                document.getElementById('item-id').value = item.id;
                document.getElementById('inp-name').value = item.name;
                document.getElementById('inp-category').value = item.category;
                
                // Handle date conversion for input
                const dateObj = new Date(item.expiryDate);
                const yyyy = dateObj.getFullYear();
                const mm = String(dateObj.getMonth() + 1).padStart(2, '0');
                const dd = String(dateObj.getDate()).padStart(2, '0');
                document.getElementById('inp-date').value = `${yyyy}-${mm}-${dd}`;
                
                document.getElementById('inp-quantity').value = item.quantity;
                document.getElementById('inp-price').value = item.price || '';
                document.getElementById('inp-note').value = item.note || '';
            }

            handleFormSubmit(e) {
                e.preventDefault();
                const id = document.getElementById('item-id').value;
                const name = document.getElementById('inp-name').value;
                const category = document.getElementById('inp-category').value;
                const date = document.getElementById('inp-date').value;
                const quantity = document.getElementById('inp-quantity').value;
                const price = document.getElementById('inp-price').value;
                const note = document.getElementById('inp-note').value;

                if (!name || !date) return;

                const itemData = {
                    name,
                    category,
                    expiryDate: new Date(date).toISOString(),
                    quantity,
                    price: price ? parseFloat(price) : null,
                    note
                };

                if (id) {
                    // Update existing
                    const idx = this.items.findIndex(i => i.id === id);
                    if (idx > -1) {
                        this.items[idx] = { ...this.items[idx], ...itemData };
                        this.saveData();
                        this.processNotifications(this.items[idx]); // Update notifs if date changed
                        this.render();
                        this.notif.showToast('Item updated', 'success');
                    }
                } else {
                    // New item
                    itemData.id = Utils.generateId();
                    this.addItem(itemData);
                }

                this.closeModal();
            }

            // --- AI Scanning Logic ---
            updateScanStatus(msg) {
                document.getElementById('scan-status').innerText = msg;
            }

            async handleFileScan(e) {
                const file = e.target.files[0];
                if (!file) return;

                // UI Reset
                const previewImg = document.getElementById('preview-img');
                const previewContainer = document.getElementById('preview-container');
                const uploadPlaceholder = document.getElementById('upload-placeholder');
                const scanOverlay = document.getElementById('scan-overlay');
                
                previewImg.src = URL.createObjectURL(file);
                previewContainer.classList.remove('hidden');
                uploadPlaceholder.classList.add('hidden');
                scanOverlay.classList.remove('hidden'); // Start scan line animation

                this.updateScanStatus("Reading receipt text (OCR)...");

                try {
                    // 1. Tesseract OCR
                    const worker = await Tesseract.createWorker('eng+chi_tra');
                    const ret = await worker.recognize(file);
                    const extractedText = ret.data.text;
                    await worker.terminate();

                    if (!extractedText.trim()) throw new Error("No text found.");
                    
                    console.log("OCR:", extractedText);
                    this.updateScanStatus("Analyzing with AI...");

                    // 2. AI Processing
                    const jsonData = await this.processWithAI(extractedText);
                    
                    // 3. Process Result
                    this.scannedItems = jsonData.items.map(i => {
                        const days = i.estimated_expiry_days || 7;
                        const date = new Date();
                        date.setDate(date.getDate() + days);
                        
                        return {
                            id: Utils.generateId(),
                            name: i.product_name,
                            category: i.category || 'other', // From AI or default
                            quantity: i.quantity || 1,
                            price: i.price_each || null,
                            expiryDate: date.toISOString().split('T')[0] // Keep as YYYY-MM-DD for input
                        };
                    });

                    this.renderScannedItems();
                    this.updateScanStatus("");
                    scanOverlay.classList.add('hidden'); // Stop animation

                } catch (err) {
                    console.error(err);
                    this.updateScanStatus("Error: " + err.message);
                    scanOverlay.classList.add('hidden');
                }
            }

            async processWithAI(ocrText) {
                const apiKey = document.getElementById('apiKey').value.trim();
                const apiUrl = document.getElementById('apiUrl').value.trim();
                const model = document.getElementById('modelName').value.trim();

                if (!apiKey) throw new Error("API Key missing.");

                const systemPrompt = "You are a helpful API that converts receipt text into JSON.";
                const userPrompt = `
                    I have OCR text from a receipt. Please extract the items and return a JSON object with a single key 'items' which is an array of objects.
                    Each object must have these keys:
                    - "product_name" (string)
                    - "quantity" (number, default to 1 if not specified)
                    - "price_each" (number, extract price per unit. IMPORTANT: If quantity > 1, verify this is the UNIT price, NOT the line total. If only total is shown, divide total by quantity.)
                    - "category" (string, choose exactly one that fits best from: dairy, fruit, meat, grain, drink, snack, frozen, canned, condiment, other)
                    - "estimated_expiry_days" (number, reasonable estimate of days until expiration from today based on product type, e.g. fresh milk=7, canned beans=365, meat=3)

                    OCR TEXT (may contain English/Chinese):
                    """
                    ${ocrText}
                    """

                    Return ONLY the raw JSON. Do not include markdown formatting.
                `;

                const payload = {
                    model: model,
                    messages: [
                        { role: "system", content: systemPrompt },
                        { role: "user", content: userPrompt }
                    ],
                    temperature: 0.2
                };

                const response = await fetch(apiUrl, {
                    method: "POST",
                    headers: {
                        "Content-Type": "application/json",
                        "Authorization": `Bearer ${apiKey}`
                    },
                    body: JSON.stringify(payload)
                });

                if (!response.ok) {
                    const txt = await response.text();
                    throw new Error(`AI Error (${response.status})`);
                }

                const data = await response.json();
                const content = data.choices?.[0]?.message?.content || "";
                const cleanedContent = content.replace(/```json/g, '').replace(/```/g, '').trim();
                return JSON.parse(cleanedContent);
            }

            renderScannedItems() {
                const container = document.getElementById('detected-items-list');
                const resultsArea = document.getElementById('scan-results');
                
                container.innerHTML = '';
                document.getElementById('scan-count').innerText = `${this.scannedItems.length} items`;
                resultsArea.classList.remove('hidden');

                if (this.scannedItems.length === 0) {
                    container.innerHTML = '<div class="text-center text-sm text-slate-400">No items found.</div>';
                    return;
                }

                this.scannedItems.forEach((item, idx) => {
                    const el = document.createElement('div');
                    el.className = 'flex items-center gap-3 bg-white dark:bg-slate-700 p-2 rounded-lg border border-slate-100 dark:border-slate-600';
                    el.innerHTML = `
                        <div class="text-nature-600">${Utils.getCategoryIcon(item.category)}</div>
                        <div class="flex-1 min-w-0">
                            <input type="text" value="${item.name}" onchange="app.updateScannedItem('${item.id}', 'name', this.value)" class="bg-transparent font-bold text-sm w-full focus:outline-none dark:text-white">
                            <div class="flex gap-2 text-xs text-slate-400 mt-1">
                                <span>Qty: <input type="number" value="${item.quantity}" class="w-8 bg-transparent text-center border-b border-slate-300 focus:outline-none" onchange="app.updateScannedItem('${item.id}', 'quantity', this.value)"></span>
                                <span>$: <input type="number" value="${item.price || ''}" step="0.01" class="w-12 bg-transparent text-center border-b border-slate-300 focus:outline-none" placeholder="0.00" onchange="app.updateScannedItem('${item.id}', 'price', this.value)"></span>
                                <input type="date" value="${item.expiryDate}" class="bg-transparent border-b border-slate-300 focus:outline-none w-24 text-center" onchange="app.updateScannedItem('${item.id}', 'expiryDate', this.value)">
                                <select onchange="app.updateScannedItem('${item.id}', 'category', this.value)" class="bg-transparent border-none p-0 text-xs w-16">
                                    <option value="dairy" ${item.category=='dairy'?'selected':''}>Dairy</option>
                                    <option value="fruit" ${item.category=='fruit'?'selected':''}>Fruit</option>
                                    <option value="meat" ${item.category=='meat'?'selected':''}>Meat</option>
                                    <option value="grain" ${item.category=='grain'?'selected':''}>Grain</option>
                                    <option value="drink" ${item.category=='drink'?'selected':''}>Drink</option>
                                    <option value="snack" ${item.category=='snack'?'selected':''}>Snack</option>
                                    <option value="frozen" ${item.category=='frozen'?'selected':''}>Frozen</option>
                                    <option value="canned" ${item.category=='canned'?'selected':''}>Canned</option>
                                    <option value="condiment" ${item.category=='condiment'?'selected':''}>Condiments</option>
                                    <option value="other" ${item.category=='other'?'selected':''}>Other</option>
                                </select>
                            </div>
                        </div>
                        <button onclick="app.removeScannedItem('${item.id}')" class="text-slate-400 hover:text-red-500">×</button>
                    `;
                    container.appendChild(el);
                });
            }

            updateScannedItem(id, field, val) {
                const idx = this.scannedItems.findIndex(i => i.id === id);
                if (idx > -1) {
                    if (field === 'price') {
                         this.scannedItems[idx][field] = val ? parseFloat(val) : null;
                    } else {
                         this.scannedItems[idx][field] = val;
                    }
                }
            }

            removeScannedItem(id) {
                this.scannedItems = this.scannedItems.filter(i => i.id !== id);
                this.renderScannedItems();
            }

            importScannedItems() {
                this.scannedItems.forEach(item => {
                    // Finalize item structure
                    item.note = ''; // Reset note as price is now a real field
                    // Ensure ISO format for consistency
                    if (item.expiryDate && !item.expiryDate.includes('T')) {
                        item.expiryDate = new Date(item.expiryDate).toISOString();
                    }
                    this.addItem(item);
                });
                this.closeModal();
                this.notif.showToast(`Imported ${this.scannedItems.length} items from receipt`, 'success');
            }
        }

        // Initialize App
        const app = new App();
        window.app = app;
        // Auto-load removed as requested. User can load demo data manually.

    </script>
</body>
</html>
