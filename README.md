<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0"/>
    <title>Security Chaos Engineering Dashboard</title>

    <!-- icons & tailwind -->
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <script src="https://cdn.tailwindcss.com"></script>

    <!-- Firebase SDK (compat) -->
    <script src="https://www.gstatic.com/firebasejs/9.22.1/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.1/firebase-database-compat.js"></script>

    <style>
        /* ===== Background hero image (full-screen) =====
           Save your image as assets/bg.jpg (or update URL below)
        */
        :root{
            --overlay-color: rgba(8,11,26,0.55);
            --overlay-gradient: linear-gradient(180deg, rgba(8,11,26,0.45), rgba(8,11,26,0.6));
        }
        html,body { height:100%; margin:0; padding:0; font-family: Inter, system-ui, -apple-system, "Segoe UI", Roboto, "Helvetica Neue", Arial; }

        /* background image layer */
        .bg-hero {
            position: fixed;
            inset: 0;
            z-index: -100;
            background-image: url('assets/bg.jpg');
            background-size: cover;
            background-position: center center;
            background-repeat: no-repeat;
            transform: translateZ(0);
            will-change: transform;
            filter: saturate(1.02) contrast(1.02);
        }

        /* dark blur overlay for readability */
        .bg-hero::after {
            content:'';
            position:absolute;
            inset:0;
            backdrop-filter: blur(6px) brightness(.55);
            -webkit-backdrop-filter: blur(6px) brightness(.55);
            background: var(--overlay-gradient);
        }

        /* container main */
        .theme-transition { transition: all 0.3s ease; }

        .active-section { 
            background: linear-gradient(135deg, #3b82f6, #1d4ed8); 
            color: white; 
        }
        .online-indicator { 
            width: 8px; height: 8px; background: #10B981; border-radius: 50%; 
            animation: pulse 2s infinite; 
        }
        @keyframes pulse { 
            0% { transform: scale(1); opacity: 1; }
            50% { transform: scale(1.2); opacity: 0.7; }
            100% { transform: scale(1); opacity: 1; }
        }

        .modal { 
            display: none; position: fixed; top: 0; left: 0; width: 100%; height: 100%; 
            background: rgba(0, 0, 0, 0.4); z-index: 1000; align-items: center; justify-content: center; 
        }
        .modal.active { display: flex; }

        .section-content { display: none; }
        .section-content.active { display: block; }

        .chat-container {
            max-height: 300px;
            overflow-y: auto;
        }

        /* Mobile tweaks */
        @media (max-width: 768px) {
            .flex.min-h-screen { flex-direction: column; }
            .w-80 { width: 100% !important; border-right: none !important; border-bottom: 1px solid #e5e7eb; }
            .flex-1.p-8 { padding: 1rem !important; }
            .grid-cols-1 { grid-template-columns: 1fr !important; }
            nav.space-y-2 { display: flex; flex-wrap: wrap; gap: 0.5rem; }
            .section-btn { padding: 0.75rem !important; font-size: 0.875rem; flex: 1 0 auto; text-align: center; }
            .section-btn i { margin-right: 0.25rem !important; }
            .modal > div { margin: 0.5rem !important; max-height: 90vh; overflow-y: auto; }
            .chat-container { max-height: 200px !important; }
        }
        @media (max-width: 480px) {
            .text-2xl, .text-3xl { font-size: 1.5rem !important; }
            .p-8 { padding: 1rem !important; }
            .section-btn { font-size: 0.75rem; padding: 0.5rem !important; }
            .section-btn i { display: none; }
        }

        /* card shadows tweak to match dark background */
        .card {
            background: rgba(255,255,255,0.92);
            border-radius: 16px;
            box-shadow: 0 8px 30px rgba(3,7,18,0.35);
        }
        .card-dark {
            background: rgba(17,24,39,0.6);
            border-radius: 12px;
            color: #f8fafc;
            box-shadow: 0 6px 20px rgba(2,6,23,0.6);
        }

        /* small parallax - we move background a bit on scroll */
        .parallax {
            transform: translateY(0px);
            will-change: transform;
        }
    </style>
</head>
<body class="bg-gray-50 dark:bg-gray-900 theme-transition">

    <!-- fixed background hero (place image in assets/bg.jpg) -->
    <div class="bg-hero parallax" id="bgHero" aria-hidden="true"></div>

    <!-- Add Content Modal -->
    <div class="modal" id="addContentModal">
        <div class="bg-white dark:bg-gray-800 p-8 rounded-2xl max-w-2xl w-full mx-4 shadow-2xl">
            <h2 class="text-2xl font-bold mb-6 text-center text-gray-800 dark:text-white">Add New Content</h2>
            <form id="addContentForm" class="space-y-4">
                <input type="hidden" id="contentSection">
                <input type="hidden" id="editContentId">
                <div>
                    <label class="block text-sm font-medium mb-2 text-gray-700 dark:text-gray-300">Title</label>
                    <input type="text" id="contentTitle" class="w-full p-3 border border-gray-300 dark:border-gray-600 rounded-xl focus:ring-2 focus:ring-blue-500 dark:bg-gray-700 dark:text-white" required>
                </div>
                <div>
                    <label class="block text-sm font-medium mb-2 text-gray-700 dark:text-gray-300">Content</label>
                    <textarea id="contentDescription" class="w-full p-3 border border-gray-300 dark:border-gray-600 rounded-xl focus:ring-2 focus:ring-blue-500 dark:bg-gray-700 dark:text-white h-40" required></textarea>
                </div>
                <div>
                    <label class="block text-sm font-medium mb-2 text-gray-700 dark:text-gray-300">Author</label>
                    <select id="contentAuthor" class="w-full p-3 border border-gray-300 dark:border-gray-600 rounded-xl focus:ring-2 focus:ring-blue-500 dark:bg-gray-700 dark:text-white">
                        <option value="Kaled Osman">Kaled Osman</option>
                        <option value="Marcus Tibell">Marcus Tibell</option>
                        <option value="Jens Annell">Jens Annell</option>
                        <option value="Fahad Hussain">Fahad Hussain</option>
                        <option value="Luwam">Luwam</option>
                        <option value="Stefan Österberg">Stefan Österberg</option>
                        <option value="Najmaddin">Najmaddin</option>
                    </select>
                </div>
                <div class="flex gap-3">
                    <button type="submit" class="flex-1 bg-green-600 hover:bg-green-700 text-white py-3 rounded-xl transition-all font-medium">Add Content</button>
                    <button type="button" onclick="closeAddContent()" class="flex-1 bg-gray-500 hover:bg-gray-600 text-white py-3 rounded-xl transition-all font-medium">Cancel</button>
                </div>
            </form>
        </div>
    </div>

    <!-- Team Chat Modal -->
    <div class="modal" id="teamChatModal">
        <div class="bg-white dark:bg-gray-800 p-6 rounded-2xl mx-4 shadow-2xl" style="width: 500px; max-width: 90vw;">
            <div class="flex justify-between items-center mb-4">
                <h2 class="text-xl font-bold text-gray-800 dark:text-white flex items-center gap-2">
                    <span id="chatStatus" class="online-indicator"></span>
                    Team Chat <span id="messageCount" class="bg-blue-500 text-white text-xs px-2 py-1 rounded-full">0</span>
                </h2>
                <button onclick="closeTeamChat()" class="text-gray-500 hover:text-gray-700">
                    <i class="fas fa-times"></i>
                </button>
            </div>
            <div id="chatMessages" class="chat-container mb-4 p-3 border border-gray-300 rounded-lg bg-gray-50 dark:bg-gray-900">
                <div class="text-center text-gray-500 text-sm py-4">
                    <i class="fas fa-comments text-xl mb-2 block"></i>
                    Loading chat messages...
                </div>
            </div>
            <div class="flex gap-2">
                <input type="text" id="chatInput" placeholder="Type message..." class="flex-1 p-2 border border-gray-300 rounded-lg dark:bg-gray-800 dark:text-white">
                <button id="sendChatButton" class="bg-blue-600 hover:bg-blue-700 text-white px-4 py-2 rounded-lg">
                    <i class="fas fa-paper-plane"></i>
                </button>
            </div>
        </div>
    </div>

    <!-- Login Modal -->
    <div class="modal" id="loginModal" style="display: flex;">
        <div class="bg-white dark:bg-gray-800 p-8 rounded-2xl max-w-md w-full mx-4 shadow-2xl">
            <h2 class="text-2xl font-bold mb-6 text-center text-gray-800 dark:text-white">
                <i class="fas fa-user-lock mr-2"></i>Login to Dashboard
            </h2>
            <form id="loginForm" class="space-y-4">
                <div>
                    <label class="block text-sm font-medium mb-2 text-gray-700 dark:text-gray-300">Email</label>
                    <input type="email" id="loginEmail" class="w-full p-3 border border-gray-300 dark:border-gray-600 rounded-xl focus:ring-2 focus:ring-blue-500 dark:bg-gray-700 dark:text-white" required>
                </div>
                <div>
                    <label class="block text-sm font-medium mb-2 text-gray-700 dark:text-gray-300">Password</label>
                    <input type="password" id="loginPassword" class="w-full p-3 border border-gray-300 dark:border-gray-600 rounded-xl focus:ring-2 focus:ring-blue-500 dark:bg-gray-700 dark:text-white" required>
                </div>
                <div class="flex gap-3">
                    <button type="submit" class="flex-1 bg-blue-600 hover:bg-blue-700 text-white py-3 rounded-xl transition-all font-medium">Login</button>
                    <button type="button" onclick="closeLogin()" class="flex-1 bg-gray-500 hover:bg-gray-600 text-white py-3 rounded-xl transition-all font-medium">Cancel</button>
                </div>
            </form>
            <div class="mt-6 p-4 bg-gray-50 dark:bg-gray-700 rounded-xl">
                <h3 class="font-semibold mb-2">Demo Accounts:</h3>
                <div class="text-sm space-y-1">
                    <div>kaled@team.com - kaled123</div>
                    <div>marcus@team.com - marcus123</div>
                    <div>jens@team.com - jens123</div>
                    <div>fahad@team.com - fahad123</div>
                    <div>luwam@team.com - luwam123</div>
                    <div>stefan@team.com - stefan123</div>
                    <div>najmaddin@team.com - najmaddin123</div>
                </div>
            </div>
        </div>
    </div>

    <!-- Main Layout -->
    <div class="flex min-h-screen relative" style="z-index:1;">
        <!-- Sidebar -->
        <div class="w-80 bg-gray-50 dark:bg-gray-800 flex-shrink-0 p-6 border-r border-gray-200 dark:border-gray-700">
            <div class="mb-8">
                <h1 class="text-2xl font-bold text-blue-600 dark:text-blue-400">Security Chaos Engineering</h1>
                <p class="text-gray-600 dark:text-gray-400 mt-2">Group 1 Dashboard</p>
                <div class="mt-3 p-3 bg-gradient-to-r from-green-500 to-emerald-600 text-white rounded-lg shadow-lg">
                    <div class="flex items-center justify-between">
                        <div class="flex items-center gap-2">
                            <span class="online-indicator"></span>
                            <span class="font-semibold">LIVE UPDATES ACTIVE</span>
                        </div>
                        <i class="fas fa-bolt"></i>
                    </div>
                    <div class="text-xs opacity-90 mt-1">Real-time collaboration enabled</div>
                </div>
            </div>
            <div class="w-full mb-6">
                <div class="bg-white dark:bg-gray-700 p-4 rounded-xl shadow-lg border-2 border-blue-200 dark:border-blue-800">
                    <div class="flex items-center space-x-3 mb-3">
                        <div class="relative">
                            <div id="userAvatar" class="w-10 h-10 bg-gradient-to-r from-blue-500 to-purple-500 rounded-full flex items-center justify-center text-white font-bold">Y</div>
                            <span class="online-indicator absolute -top-1 -right-1 border-2 border-white dark:border-gray-800"></span>
                        </div>
                        <div class="flex-1 min-w-0">
                            <p class="font-semibold text-gray-800 dark:text-white truncate" id="userName">You</p>
                            <p class="text-sm text-gray-600 dark:text-gray-400 truncate" id="userRole">Active User</p>
                        </div>
                    </div>
                    <button onclick="openTeamChat()" class="w-full bg-blue-600 hover:bg-blue-700 text-white py-3 rounded-lg transition-all flex items-center justify-center">
                        <i class="fas fa-comments mr-2"></i>Team Chat
                        <span id="unreadCount" class="ml-2 bg-red-500 text-white text-xs px-2 py-1 rounded-full hidden">0</span>
                    </button>
                </div>
            </div>

            <div class="mb-6">
                <div class="bg-gradient-to-br from-blue-50 to-green-50 dark:from-gray-800 dark:to-gray-700 p-4 rounded-xl shadow-lg border-2 border-green-200 dark:border-green-800">
                    <h3 class="font-semibold mb-3 text-gray-800 dark:text-white flex items-center gap-2">
                        <span class="online-indicator"></span>
                        Team Members
                    </h3>
                    <div id="liveTeamList" class="space-y-2 max-h-40 overflow-y-auto"></div>
                </div>
            </div>

            <div id="adminPanel"></div>

            <nav class="space-y-2 mb-8">
                <button class="section-btn w-full text-left p-4 rounded-xl theme-transition active-section" data-section="overview">
                    <i class="fas fa-home mr-3"></i>Overview
                </button>
                <button class="section-btn w-full text-left p-4 rounded-xl theme-transition hover:bg-gray-100 dark:hover:bg-gray-700 text-gray-700 dark:text-gray-300" data-section="project-documentation">
                    <i class="fas fa-folder mr-3"></i>Project Documentation
                </button>
                <button class="section-btn w-full text-left p-4 rounded-xl theme-transition hover:bg-gray-100 dark:hover:bg-gray-700 text-gray-700 dark:text-gray-300" data-section="team-collaboration">
                    <i class="fas fa-users mr-3"></i>Team Collaboration
                </button>
                <button class="section-btn w-full text-left p-4 rounded-xl theme-transition hover:bg-gray-100 dark:hover:bg-gray-700 text-gray-700 dark:text-gray-300" data-section="team-updates">
                    <i class="fas fa-bullhorn mr-3"></i>Team Updates
                </button>
                <button class="section-btn w-full text-left p-4 rounded-xl theme-transition hover:bg-gray-100 dark:hover:bg-gray-700 text-gray-700 dark:text-gray-300" data-section="ai-assistant">
                    <i class="fas fa-robot mr-3"></i>AI Assistant
                </button>
                <button class="section-btn w-full text-left p-4 rounded-xl theme-transition hover:bg-gray-100 dark:hover:bg-gray-700 text-gray-700 dark:text-gray-300" data-section="practical-tasks">
                    <i class="fas fa-tasks mr-3"></i>Practical Tasks
                </button>
                <button class="section-btn w-full text-left p-4 rounded-xl theme-transition hover:bg-gray-100 dark:hover:bg-gray-700 text-gray-700 dark:text-gray-300" data-section="implementation">
                    <i class="fas fa-code mr-3"></i>Implementation
                </button>
                <button class="section-btn w-full text-left p-4 rounded-xl theme-transition hover:bg-gray-100 dark:hover:bg-gray-700 text-gray-700 dark:text-gray-300" data-section="research">
                    <i class="fas fa-search mr-3"></i>Research
                </button>
                <button class="section-btn w-full text-left p-4 rounded-xl theme-transition hover:bg-gray-100 dark:hover:bg-gray-700 text-gray-700 dark:text-gray-300" data-section="resources">
                    <i class="fas fa-book mr-3"></i>Resources
                </button>
                <button class="section-btn w-full text-left p-4 rounded-xl theme-transition hover:bg-gray-100 dark:hover:bg-gray-700 text-gray-700 dark:text-gray-300" data-section="security-testing">
                    <i class="fas fa-shield-alt mr-3"></i>Security Testing
                </button>
                <button class="section-btn w-full text-left p-4 rounded-xl theme-transition hover:bg-gray-100 dark:hover:bg-gray-700 text-gray-700 dark:text-gray-300" data-section="monitoring-analytics">
                    <i class="fas fa-chart-line mr-3"></i>Monitoring & Analytics
                </button>
            </nav>

            <div class="mt-auto">
                <button onclick="toggleTheme()" class="w-full p-4 rounded-xl theme-transition bg-gray-100 hover:bg-gray-200 dark:bg-gray-700 dark:hover:bg-gray-600 text-gray-700 dark:text-gray-300 flex items-center justify-center font-medium">
                    <i class="fas fa-moon mr-3"></i>Toggle Theme
                </button>
            </div>
        </div>

        <!-- Main Content -->
        <div class="flex-1 p-8 overflow-auto">
            <header class="mb-8">
                <h1 class="text-3xl font-bold text-gray-100 dark:text-white">Security Chaos Engineering Dashboard</h1>
                <p class="text-gray-200 dark:text-gray-300">Group 1 - Real-time Team Collaboration</p>
            </header>

            <!-- Overview Section -->
            <div class="section-content active" id="overview">
                <div class="card p-8 rounded-2xl shadow-lg mb-6">
                    <div class="flex justify-between items-center mb-6">
                        <h2 class="text-2xl font-bold text-gray-900 dark:text-white">Overview</h2>
                        <button onclick="openAddContent('overview')" class="bg-green-600 hover:bg-green-700 text-white px-4 py-2 rounded-xl transition-all">
                            <i class="fas fa-plus mr-2"></i>Add Content
                        </button>
                    </div>
                    <div id="overview-content" class="space-y-4 mb-8"></div>

                    <div class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-4 gap-6 mb-8">
                        <div class="text-center p-6 bg-blue-500 text-white rounded-2xl shadow-lg">
                            <div class="text-3xl font-bold mb-2" id="stat-members">7</div>
                            <div>Team Members</div>
                        </div>
                        <div class="text-center p-6 bg-green-500 text-white rounded-2xl shadow-lg">
                            <div class="text-3xl font-bold mb-2" id="stat-sections">11</div>
                            <div>Sections</div>
                        </div>
                        <div class="text-center p-6 bg-purple-500 text-white rounded-2xl shadow-lg">
                            <div class="text-3xl font-bold mb-2">✓</div>
                            <div>Chat Working</div>
                        </div>
                        <div class="text-center p-6 bg-orange-500 text-white rounded-2xl shadow-lg">
                            <div class="text-3xl font-bold mb-2">100%</div>
                            <div>Functional</div>
                        </div>
                    </div>
                </div>
            </div>

            <!-- other sections -->
            <div class="section-content" id="project-documentation">
                <div class="card p-8 rounded-2xl shadow-lg">
                    <div class="flex justify-between items-center mb-6">
                        <h2 class="text-2xl font-bold text-gray-900 dark:text-white">Project Documentation</h2>
                        <button onclick="openAddContent('project-documentation')" class="bg-green-600 hover:bg-green-700 text-white px-4 py-2 rounded-xl transition-all">
                            <i class="fas fa-plus mr-2"></i>Add Content
                        </button>
                    </div>
                    <div id="project-documentation-content" class="space-y-4"></div>
                </div>
            </div>

            <div class="section-content" id="team-collaboration">
                <div class="card p-8 rounded-2xl shadow-lg">
                    <div class="flex justify-between items-center mb-6">
                        <h2 class="text-2xl font-bold text-gray-900 dark:text-white">Team Collaboration</h2>
                        <button onclick="openAddContent('team-collaboration')" class="bg-green-600 hover:bg-green-700 text-white px-4 py-2 rounded-xl transition-all">
                            <i class="fas fa-plus mr-2"></i>Add Content
                        </button>
                    </div>
                    <div id="team-collaboration-content" class="space-y-4"></div>
                </div>
            </div>

            <div class="section-content" id="team-updates">
                <div class="card p-8 rounded-2xl shadow-lg">
                    <div class="flex justify-between items-center mb-6">
                        <h2 class="text-2xl font-bold text-gray-900 dark:text-white">Team Updates</h2>
                        <button onclick="openAddContent('team-updates')" class="bg-green-600 hover:bg-green-700 text-white px-4 py-2 rounded-xl transition-all">
                            <i class="fas fa-plus mr-2"></i>Add Content
                        </button>
                    </div>
                    <div id="team-updates-content" class="space-y-4"></div>
                </div>
            </div>

            <div class="section-content" id="ai-assistant">
                <div class="card p-8 rounded-2xl shadow-lg">
                    <div class="flex justify-between items-center mb-6">
                        <h2 class="text-2xl font-bold text-gray-900 dark:text-white">AI Assistant</h2>
                        <button onclick="openAddContent('ai-assistant')" class="bg-green-600 hover:bg-green-700 text-white px-4 py-2 rounded-xl transition-all">
                            <i class="fas fa-plus mr-2"></i>Add Content
                        </button>
                    </div>
                    <div id="ai-assistant-content" class="space-y-4"></div>
                </div>
            </div>

            <div class="section-content" id="practical-tasks">
                <div class="card p-8 rounded-2xl shadow-lg">
                    <div class="flex justify-between items-center mb-6">
                        <h2 class="text-2xl font-bold text-gray-900 dark:text-white">Practical Tasks</h2>
                        <button onclick="openAddContent('practical-tasks')" class="bg-green-600 hover:bg-green-700 text-white px-4 py-2 rounded-xl transition-all">
                            <i class="fas fa-plus mr-2"></i>Add Content
                        </button>
                    </div>
                    <div id="practical-tasks-content" class="space-y-4"></div>
                </div>
            </div>

            <div class="section-content" id="implementation">
                <div class="card p-8 rounded-2xl shadow-lg">
                    <div class="flex justify-between items-center mb-6">
                        <h2 class="text-2xl font-bold text-gray-900 dark:text-white">Implementation</h2>
                        <button onclick="openAddContent('implementation')" class="bg-green-600 hover:bg-green-700 text-white px-4 py-2 rounded-xl transition-all">
                            <i class="fas fa-plus mr-2"></i>Add Content
                        </button>
                    </div>
                    <div id="implementation-content" class="space-y-4"></div>
                </div>
            </div>

            <div class="section-content" id="research">
                <div class="card p-8 rounded-2xl shadow-lg">
                    <div class="flex justify-between items-center mb-6">
                        <h2 class="text-2xl font-bold text-gray-900 dark:text-white">Research</h2>
                        <button onclick="openAddContent('research')" class="bg-green-600 hover:bg-green-700 text-white px-4 py-2 rounded-xl transition-all">
                            <i class="fas fa-plus mr-2"></i>Add Content
                        </button>
                    </div>
                    <div id="research-content" class="space-y-4"></div>
                </div>
            </div>

            <div class="section-content" id="resources">
                <div class="card p-8 rounded-2xl shadow-lg">
                    <div class="flex justify-between items-center mb-6">
                        <h2 class="text-2xl font-bold text-gray-900 dark:text-white">Resources</h2>
                        <button onclick="openAddContent('resources')" class="bg-green-600 hover:bg-green-700 text-white px-4 py-2 rounded-xl transition-all">
                            <i class="fas fa-plus mr-2"></i>Add Content
                        </button>
                    </div>
                    <div id="resources-content" class="space-y-4"></div>
                </div>
            </div>

            <div class="section-content" id="security-testing">
                <div class="card p-8 rounded-2xl shadow-lg">
                    <div class="flex justify-between items-center mb-6">
                        <h2 class="text-2xl font-bold text-gray-900 dark:text-white">Security Testing</h2>
                        <button onclick="openAddContent('security-testing')" class="bg-green-600 hover:bg-green-700 text-white px-4 py-2 rounded-xl transition-all">
                            <i class="fas fa-plus mr-2"></i>Add Content
                        </button>
                    </div>
                    <div id="security-testing-content" class="space-y-4"></div>
                </div>
            </div>

            <div class="section-content" id="monitoring-analytics">
                <div class="card p-8 rounded-2xl shadow-lg">
                    <div class="flex justify-between items-center mb-6">
                        <h2 class="text-2xl font-bold text-gray-900 dark:text-white">Monitoring & Analytics</h2>
                        <button onclick="openAddContent('monitoring-analytics')" class="bg-green-600 hover:bg-green-700 text-white px-4 py-2 rounded-xl transition-all">
                            <i class="fas fa-plus mr-2"></i>Add Content
                        </button>
                    </div>
                    <div id="monitoring-analytics-content" class="space-y-4"></div>
                </div>
            </div>
        </div>
    </div>

    <!-- ---------- SCRIPT: Firebase + Real-time logic (kept same as original) ---------- -->
    <script>
    (function(){
        const DATABASE_URL = 'https://fir-console-df3e9-default-rtdb.europe-west1.firebasedatabase.app';
        const firebaseConfig = {
            apiKey: "AIzaSyDJsZ4LZVrBucavpTdhXbKxyE_BFeZFFKs",
            authDomain: "fir-console-df3e9.firebaseapp.com",
            databaseURL: DATABASE_URL,
            projectId: "fir-console-df3e9",
            storageBucket: "fir-console-df3e9.firebasestorage.app",
            messagingSenderId: "750795336412",
            appId: "1:750795336412:web:abfd0c06941a9418abe219"
        };
        try { if (!firebase.apps.length) firebase.initializeApp(firebaseConfig); } catch(e){ console.warn('Firebase init warning:', e); }
        const db = firebase.database();

        const sections = [
            'overview','project-documentation','team-collaboration','team-updates',
            'ai-assistant','practical-tasks','implementation','research',
            'resources','security-testing','monitoring-analytics'
        ];
        const $ = id => document.getElementById(id);

        function escapeHtml(s = '') {
            return String(s)
                .replaceAll('&','&amp;').replaceAll('<','&lt;').replaceAll('>','&gt;')
                .replaceAll('"','&quot;').replaceAll("'",'&#039;');
        }

        const chat = {
            currentUser: 'You',
            connected: false,
            messageCount: 0,
            unread: 0,
            lastRead: Date.now(),
            init() {
                const saved = JSON.parse(localStorage.getItem('currentUser'));
                if (saved && saved.name) this.currentUser = saved.name;
                this.connected = true;
                if ($('chatStatus')) $('chatStatus').style.background = '#10B981';
                this.listenMessages();
                const sendBtn = $('sendChatButton');
                if (sendBtn) sendBtn.addEventListener('click', () => this.send());
            },
            listenMessages() {
                const ref = db.ref('team-chat').limitToLast(200);
                ref.off();
                ref.on('child_added', snap => {
                    const msg = snap.val();
                    this.display(msg);
                    this.messageCount++;
                    if (msg.timestamp > this.lastRead && !this.isOpen()) {
                        this.unread++;
                        this.updateUnread();
                    }
                });
            },
            async send() {
                const input = $('chatInput');
                if (!input) return;
                const text = input.value.trim();
                if (!text) return;
                const payload = { user: this.currentUser || 'You', text, timestamp: Date.now() };
                try {
                    await db.ref('team-chat').push(payload);
                    input.value = '';
                    this.lastRead = Date.now();
                    this.unread = 0;
                    this.updateUnread();
                } catch (e) {
                    console.error('Failed writing chat', e);
                    alert('Failed to send message to server');
                }
            },
            display(msg) {
                const list = $('chatMessages');
                if (!list) return;
                const placeholder = list.querySelector('.text-center');
                if (placeholder) list.innerHTML = '';
                const el = document.createElement('div');
                const mine = msg.user === this.currentUser;
                el.className = `p-3 mb-2 rounded-lg max-w-[85%] ${mine ? 'bg-blue-500 text-white ml-auto' : 'bg-gray-200 text-gray-800 dark:bg-gray-700 dark:text-white'}`;
                el.innerHTML = `<div class="font-semibold text-sm">${escapeHtml(msg.user)}</div><div class="break-words">${escapeHtml(msg.text)}</div><div class="text-xs opacity-70 mt-1">${new Date(msg.timestamp).toLocaleTimeString()}</div>`;
                list.appendChild(el);
                list.scrollTop = list.scrollHeight;
                const cnt = $('messageCount');
                if (cnt) cnt.textContent = (parseInt(cnt.textContent || '0') + 1).toString();
            },
            open() {
                $('teamChatModal').classList.add('active');
                this.lastRead = Date.now(); this.unread = 0; this.updateUnread();
            },
            close() { $('teamChatModal').classList.remove('active'); },
            isOpen() { return $('teamChatModal').classList.contains('active'); },
            updateUnread() {
                const u = $('unreadCount');
                if (!u) return;
                if (this.unread > 0) { u.textContent = this.unread; u.classList.remove('hidden'); } else { u.classList.add('hidden'); }
            }
        };

        const contentSystem = {
            renderSection(section, snapshot) {
                const container = $(`${section}-content`);
                if (!container) return;
                container.innerHTML = '';
                if (!snapshot || !snapshot.exists()) {
                    container.innerHTML = `<div class="text-center p-8 text-gray-500 dark:text-gray-400"><i class="fas fa-inbox text-4xl mb-4"></i><p>No content added yet. Be the first to share something!</p></div>`;
                    return;
                }
                snapshot.forEach(child => {
                    const item = child.val(); const id = child.key;
                    const el = document.createElement('div');
                    el.className = 'p-6 border border-gray-200 dark:border-gray-700 rounded-2xl bg-white dark:bg-gray-800 mb-4';
                    el.innerHTML = `
                        <div class="flex justify-between items-start mb-4">
                            <h3 class="text-xl font-semibold text-gray-900 dark:text-white">${escapeHtml(item.title)}</h3>
                            <div class="flex space-x-2">
                                <button data-id="${id}" data-section="${section}" class="edit-btn text-blue-600 hover:text-blue-800 dark:text-blue-400"><i class="fas fa-edit"></i></button>
                                <button data-id="${id}" data-section="${section}" class="del-btn text-red-600 hover:text-red-800 dark:text-red-400"><i class="fas fa-trash"></i></button>
                            </div>
                        </div>
                        <p class="text-gray-700 dark:text-gray-300 mb-4 whitespace-pre-line">${escapeHtml(item.description)}</p>
                        <div class="flex justify-between items-center text-sm text-gray-600 dark:text-gray-400">
                            <span><i class="fas fa-user mr-1"></i> ${escapeHtml(item.author)}</span>
                            <span><i class="fas fa-clock mr-1"></i> ${new Date(item.date).toLocaleDateString()}</span>
                        </div>
                    `;
                    container.appendChild(el);
                });

                container.querySelectorAll('.edit-btn').forEach(b => {
                    b.addEventListener('click', () => {
                        const id = b.getAttribute('data-id'); const sec = b.getAttribute('data-section'); openEditModalFor(sec,id);
                    });
                });
                container.querySelectorAll('.del-btn').forEach(b => {
                    b.addEventListener('click', () => {
                        const id = b.getAttribute('data-id'); const sec = b.getAttribute('data-section'); deleteContent(sec,id);
                    });
                });
            },
            listenAll() {
                sections.forEach(sec => {
                    const ref = db.ref(`content/${sec}`); ref.off(); ref.on('value', snap => this.renderSection(sec,snap));
                });
            },
            pushContent(section,payload){ return db.ref(`content/${section}`).push(payload); },
            updateContent(section,id,payload){ return db.ref(`content/${section}/${id}`).update(payload); },
            removeContent(section,id){ return db.ref(`content/${section}/${id}`).remove(); }
        };

        function openAddContent(section){ $('contentSection').value = section; $('editContentId').value=''; $('contentTitle').value=''; $('contentDescription').value=''; const saved=JSON.parse(localStorage.getItem('currentUser')); if(saved&&saved.name)$('contentAuthor').value=saved.name; $('addContentModal').classList.add('active'); }
        function closeAddContent(){ $('addContentModal').classList.remove('active'); }

        function openEditModalFor(section,id){
            db.ref(`content/${section}/${id}`).once('value').then(snap => {
                const item = snap.val(); if(!item) return alert('Item not found');
                $('contentSection').value=section; $('editContentId').value=id; $('contentTitle').value=item.title||''; $('contentDescription').value=item.description||''; $('contentAuthor').value=item.author||(JSON.parse(localStorage.getItem('currentUser'))||{}).name||'Unknown'; $('addContentModal').classList.add('active');
            }).catch(err => { console.error(err); alert('Failed to load item for edit'); });
        }

        document.getElementById('addContentForm').addEventListener('submit', function(e){
            e.preventDefault();
            const section=$('contentSection').value; const editId=$('editContentId').value; const title=$('contentTitle').value.trim(); const description=$('contentDescription').value.trim(); const author=$('contentAuthor').value;
            if(!title||!description) return alert('Fill title and content');
            const payload={ title, description, author, date: new Date().toISOString() };
            if(editId){ contentSystem.updateContent(section,editId,payload).then(()=>closeAddContent()).catch(err=>{console.error(err);alert('Update failed');}); }
            else { contentSystem.pushContent(section,payload).then(()=>closeAddContent()).catch(err=>{console.error(err);alert('Save failed');}); }
        });

        function deleteContent(section,id){ if(!confirm('Are you sure you want to delete this content?')) return; contentSystem.removeContent(section,id).catch(err=>{console.error(err);alert('Delete failed');}); }

        function clearAllContentAdmin(){ if(!confirm('Delete all content for all sections?')) return; const updates={}; sections.forEach(sec=>updates[`content/${sec}`]=null); db.ref().update(updates).then(()=>alert('All content cleared')).catch(err=>{console.error(err);alert('Failed to clear content');}); }
        function clearChatAdmin(){ if(!confirm('Clear all chat messages?')) return; db.ref('team-chat').remove().then(()=>{ const list=$('chatMessages'); if(list) list.innerHTML='<div class="text-center text-gray-500 text-sm py-4"><i class="fas fa-comments text-xl mb-2 block"></i> Chat cleared</div>'; }).catch(err=>{console.error(err);alert('Failed to clear chat');}); }

        const loginSystem = {
            users: JSON.parse(localStorage.getItem('teamUsers')) || null,
            currentUser: JSON.parse(localStorage.getItem('currentUser')) || null,
            init() {
                if (!this.users) {
                    const defaultUsers = [
                        { id: 1, name: "Kaled Osman", email: "kaled@team.com", password: "kaled123", role: "Developer", avatar: "K" },
                        { id: 2, name: "Marcus Tibell", email: "marcus@team.com", password: "marcus123", role: "Engineer", avatar: "M" },
                        { id: 3, name: "Jens Annell", email: "jens@team.com", password: "jens123", role: "Analyst", avatar: "J" },
                        { id: 4, name: "Fahad Hussain", email: "fahad@team.com", password: "fahad123", role: "Researcher", avatar: "F" },
                        { id: 5, name: "Luwam", email: "luwam@team.com", password: "luwam123", role: "Designer", avatar: "L" },
                        { id: 6, name: "Stefan Österberg", email: "stefan@team.com", password: "stefan123", role: "Architect", avatar: "S" },
                        { id: 7, name: "Najmaddin", email: "najmaddin@team.com", password: "najmaddin123", role: "Security Expert", avatar: "N" }
                    ];
                    this.users = defaultUsers;
                    localStorage.setItem('teamUsers', JSON.stringify(defaultUsers));
                }
                if (this.currentUser) { this.updateUIForLoggedIn(); adminSystem.tryInit(); }
                document.getElementById('loginForm').addEventListener('submit', (e) => { e.preventDefault(); this.attemptLogin(); });
            },
            attemptLogin() {
                const email = $('loginEmail').value.trim(); const password = $('loginPassword').value.trim();
                const found = this.users.find(u => u.email === email && u.password === password);
                if (!found) { alert('Invalid credentials'); return; }
                this.currentUser = found; localStorage.setItem('currentUser', JSON.stringify(found)); this.updateUIForLoggedIn(); chat.currentUser = found.name; $('loginModal').style.display = 'none'; adminSystem.tryInit();
            },
            logout() { this.currentUser = null; localStorage.removeItem('currentUser'); $('loginModal').style.display = 'flex'; $('userName').textContent = 'You'; $('userRole').textContent = 'Active User'; $('userAvatar').textContent = 'Y'; chat.currentUser = 'You'; },
            updateUIForLoggedIn() { const u = this.currentUser; if (!u) return; $('userName').textContent = u.name; $('userRole').textContent = u.role; $('userAvatar').textContent = u.avatar || u.name.charAt(0); adminSystem.tryInit(); }
        };

        const adminSystem = {
            tryInit() {
                const cur = JSON.parse(localStorage.getItem('currentUser')) || null;
                if (cur && cur.email === 'kaled@team.com') { this.renderAdmin(); }
            },
            renderAdmin() {
                const panel = $('adminPanel'); if (!panel) return;
                panel.innerHTML = `
                    <div class="mt-6 p-4 bg-yellow-100 dark:bg-yellow-900 border border-yellow-400 rounded-xl">
                        <h3 class="font-semibold mb-2 text-yellow-800 dark:text-yellow-200 flex items-center gap-2">
                            <i class="fas fa-crown"></i> Admin Panel
                        </h3>
                        <div class="space-y-2">
                            <button id="adminClearContent" class="w-full bg-red-600 hover:bg-red-700 text-white py-2 rounded-lg">Delete All Content</button>
                            <button id="adminClearChat" class="w-full bg-purple-600 hover:bg-purple-700 text-white py-2 rounded-lg">Clear Chat</button>
                        </div>
                    </div>
                `;
                document.getElementById('adminClearContent').addEventListener('click', clearAllContentAdmin);
                document.getElementById('adminClearChat').addEventListener('click', clearChatAdmin);
            }
        };

        const teamMembers = [
            { name: 'Kaled Osman', role: 'Developer', avatar: 'K' },
            { name: 'Marcus Tibell', role: 'Engineer', avatar: 'M' },
            { name: 'Jens Annell', role: 'Analyst', avatar: 'J' },
            { name: 'Fahad Hussain', role: 'Researcher', avatar: 'F' },
            { name: 'Luwam', role: 'Designer', avatar: 'L' },
            { name: 'Stefan Österberg', role: 'Architect', avatar: 'S' },
            { name: 'Najmaddin', role: 'Security Expert', avatar: 'N' }
        ];
        function showTeamMembers() {
            const liveTeamList = $('liveTeamList'); if (!liveTeamList) return;
            liveTeamList.innerHTML = '';
            teamMembers.forEach(member => {
                const memberElement = document.createElement('div');
                memberElement.className = 'flex items-center space-x-3 p-2 bg-white dark:bg-gray-700 rounded-lg';
                memberElement.innerHTML = `
                    <div class="relative">
                        <div class="w-8 h-8 bg-gradient-to-r from-blue-500 to-purple-500 rounded-full flex items-center justify-center text-white text-sm font-bold">
                            ${member.name.charAt(0)}
                        </div>
                        <span class="online-indicator absolute -top-1 -right-1 border-2 border-white dark:border-gray-700"></span>
                    </div>
                    <div class="flex-1">
                        <div class="font-medium text-gray-800 dark:text-white text-sm">${member.name}</div>
                        <div class="text-xs text-gray-600 dark:text-gray-400">${member.role}</div>
                    </div>
                `;
                liveTeamList.appendChild(memberElement);
            });
        }

        function seedDefaultContentIfEmpty() {
            const ref = db.ref('content/overview');
            ref.once('value').then(snap => {
                if (snap.exists()) return;
                const defaults = {
                    overview: {
                        welcome: {
                            title: '🚀 Welcome to Security Chaos Engineering Dashboard',
                            description: 'This is your team collaboration platform. Use Add Content to share with your team.',
                            author: 'System',
                            date: new Date().toISOString()
                        }
                    },
                    'team-collaboration': {
                        'guidelines': {
                            title: 'Team Collaboration Guidelines',
                            description: 'Share meeting notes and tasks here.',
                            author: 'Kaled Osman',
                            date: new Date().toISOString()
                        }
                    }
                };
                Object.keys(defaults).forEach(sec => {
                    const secRef = db.ref(`content/${sec}`);
                    Object.keys(defaults[sec]).forEach(key => {
                        secRef.push(defaults[sec][key]);
                    });
                });
            }).catch(err => console.error('Seed check failed', err));
        }

        function initAll() {
            loginSystem.init(); chat.init(); contentSystem.listenAll(); showTeamMembers(); seedDefaultContentIfEmpty();

            document.querySelectorAll('.section-btn').forEach(btn => {
                btn.addEventListener('click', function() {
                    document.querySelectorAll('.section-btn').forEach(b => b.classList.remove('active-section'));
                    this.classList.add('active-section');
                    document.querySelectorAll('.section-content').forEach(s => s.classList.remove('active'));
                    const id = this.getAttribute('data-section'); const el = document.getElementById(id);
                    if (el) el.classList.add('active');
                });
            });

            const chatInput = $('chatInput');
            if (chatInput) chatInput.addEventListener('keypress', (e) => { if (e.key === 'Enter') chat.send(); });
            const sendBtn = $('sendChatButton'); if (sendBtn) sendBtn.addEventListener('click', () => chat.send());
            const openChatBtns = document.querySelectorAll('[onclick="openTeamChat()"]'); openChatBtns.forEach(b => b.addEventListener('click', () => chat.open()));
        }

        document.addEventListener('DOMContentLoaded', () => { initAll(); });

        // expose functions globally
        window.openAddContent = openAddContent;
        window.openTeamChat = () => chat.open();
        window.closeTeamChat = () => chat.close();
        window.sendChatMessage = () => chat.send();
        window.openEditModalFor = openEditModalFor;
        window.deleteContent = deleteContent;
        window.toggleTheme = function() {
            const html = document.documentElement;
            if (html.classList.contains('dark')) { html.classList.remove('dark'); localStorage.setItem('theme','light'); }
            else { html.classList.add('dark'); localStorage.setItem('theme','dark'); }
        };
        window.clearAllContentAdmin = clearAllContentAdmin;
        window.clearChatAdmin = clearChatAdmin;
        window.closeAddContent = closeAddContent;

        // === Parallax effect: move background slightly on scroll ===
        const bg = document.getElementById('bgHero');
        let lastScroll = 0;
        window.addEventListener('scroll', () => {
            const y = window.scrollY;
            // small translate to create depth
            const translate = Math.min(60, Math.round(y * 0.12));
            if (bg) bg.style.transform = `translateY(${translate}px) scale(1.03)`;
        });
    })();
    </script>
</body>
</html>
