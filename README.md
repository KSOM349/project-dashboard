<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Security Chaos Engineering Dashboard</title>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <script src="https://cdn.tailwindcss.com"></script>
    <style>
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
            background: rgba(0, 0, 0, 0.5); z-index: 1000; align-items: center; justify-content: center; 
        }
        .modal.active { display: flex; }
        .section-content { display: none; }
        .section-content.active { display: block; }
    </style>
</head>
<body class="bg-gray-50 dark:bg-gray-900 theme-transition">
    <!-- Add Content Modal -->
    <div class="modal" id="addContentModal">
        <div class="bg-white dark:bg-gray-800 p-8 rounded-2xl max-w-2xl w-full mx-4 shadow-2xl">
            <h2 class="text-2xl font-bold mb-6 text-center text-gray-800 dark:text-white">Add New Content</h2>
            <form id="addContentForm" class="space-y-4">
                <input type="hidden" id="contentSection">
                <input type="hidden" id="editContentId">
                <div>
                    <label class="block text-sm font-medium mb-2 text-gray-700 dark:text-gray-300">Title</label>
                    <input type="text" id="contentTitle" class="w-full p-3 border border-gray-300 dark:border-gray-600 rounded-xl focus:ring-2 focus:ring-blue-500 focus:border-blue-500 dark:bg-gray-700 dark:text-white" required>
                </div>
                <div>
                    <label class="block text-sm font-medium mb-2 text-gray-700 dark:text-gray-300">Content</label>
                    <textarea id="contentDescription" class="w-full p-3 border border-gray-300 dark:border-gray-600 rounded-xl focus:ring-2 focus:ring-blue-500 focus:border-blue-500 dark:bg-gray-700 dark:text-white h-40" required></textarea>
                </div>
                <div>
                    <label class="block text-sm font-medium mb-2 text-gray-700 dark:text-gray-300">Author</label>
                    <select id="contentAuthor" class="w-full p-3 border border-gray-300 dark:border-gray-600 rounded-xl focus:ring-2 focus:ring-blue-500 focus:border-blue-500 dark:bg-gray-700 dark:text-white">
                        <option value="Kaled Osman">Kaled Osman</option>
                        <option value="Fahad Hussain">Fahad Hussain</option>
                        <option value="Stefan Österberg">Stefan Österberg</option>
                        <option value="Marcus Tibell">Marcus Tibell</option>
                        <option value="Jens Annell">Jens Annell</option>
                        <option value="Luwam">Luwam</option>
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
                    <span class="online-indicator"></span>
                    Team Chat
                </h2>
                <button onclick="closeTeamChat()" class="text-gray-500 hover:text-gray-700">
                    <i class="fas fa-times"></i>
                </button>
            </div>
            <div id="chatMessages" class="overflow-y-auto mb-4 p-3 border border-gray-300 rounded-lg bg-gray-50 dark:bg-gray-900" style="height: 300px;">
                <div class="text-center text-gray-500 text-sm py-4">
                    <i class="fas fa-comments text-xl mb-2 block"></i>
                    Start chatting with your team!
                </div>
            </div>
            <div class="flex gap-2">
                <input type="text" id="chatInput" placeholder="Type message..." class="flex-1 p-2 border border-gray-300 rounded-lg dark:bg-gray-800 dark:text-white">
                <button onclick="sendChatMessage()" class="bg-blue-600 hover:bg-blue-700 text-white px-4 py-2 rounded-lg">
                    <i class="fas fa-paper-plane"></i>
                </button>
            </div>
        </div>
    </div>

    <!-- Main Layout -->
    <div class="flex min-h-screen">
        <!-- Sidebar -->
        <div class="w-80 bg-gray-50 dark:bg-gray-800 flex-shrink-0 p-6">
            <div class="mb-8">
                <h1 class="text-2xl font-bold text-blue-600 dark:text-blue-400">Security Chaos Engineering</h1>
                <p class="text-gray-600 dark:text-gray-400 mt-2">Group 1 Dashboard</p>
                
                <!-- Live Status -->
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
            
            <!-- User Info -->
            <div class="w-full mb-6">
                <div class="bg-white dark:bg-gray-700 p-4 rounded-xl shadow-lg border-2 border-blue-200 dark:border-blue-800">
                    <div class="flex items-center space-x-3 mb-3">
                        <div class="relative">
                            <div class="w-10 h-10 bg-gradient-to-r from-blue-500 to-purple-500 rounded-full flex items-center justify-center text-white font-bold">Y</div>
                            <span class="online-indicator absolute -top-1 -right-1 border-2 border-white dark:border-gray-800"></span>
                        </div>
                        <div class="flex-1 min-w-0">
                            <p class="font-semibold text-gray-800 dark:text-white truncate">You</p>
                            <p class="text-sm text-gray-600 dark:text-gray-400 truncate">Active User</p>
                        </div>
                    </div>
                    <button onclick="openTeamChat()" class="w-full bg-blue-600 hover:bg-blue-700 text-white py-3 rounded-lg transition-all">
                        <i class="fas fa-comments mr-2"></i>Team Chat
                    </button>
                </div>
            </div>

            <!-- Live Team Members -->
            <div class="mb-6">
                <div class="bg-gradient-to-br from-blue-50 to-green-50 dark:from-gray-800 dark:to-gray-700 p-4 rounded-xl shadow-lg border-2 border-green-200 dark:border-green-800">
                    <h3 class="font-semibold mb-3 text-gray-800 dark:text-white flex items-center gap-2">
                        <span class="online-indicator"></span>
                        Team Members
                    </h3>
                    <div id="liveTeamList" class="space-y-2 max-h-40 overflow-y-auto">
                        <!-- Team members will appear here -->
                    </div>
                </div>
            </div>

            <!-- Navigation - 11 SECTIONS -->
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

            <!-- Theme Toggle -->
            <div class="mt-auto">
                <button onclick="toggleTheme()" class="w-full p-4 rounded-xl theme-transition bg-gray-100 hover:bg-gray-200 dark:bg-gray-700 dark:hover:bg-gray-600 text-gray-700 dark:text-gray-300 flex items-center justify-center font-medium">
                    <i class="fas fa-moon mr-3"></i>Toggle Theme
                </button>
            </div>
        </div>

        <!-- Main Content -->
        <div class="flex-1 p-8">
            <header class="mb-8">
                <h1 class="text-3xl font-bold text-gray-800 dark:text-white">Security Chaos Engineering Dashboard</h1>
                <p class="text-gray-600 dark:text-gray-400">Group 1 - Real-time Team Collaboration</p>
            </header>

            <!-- Overview Section -->
            <div class="section-content active" id="overview">
                <div class="bg-white dark:bg-gray-800 p-8 rounded-2xl shadow-lg">
                    <div class="flex justify-between items-center mb-6">
                        <h2 class="text-2xl font-bold text-gray-800 dark:text-white">Overview</h2>
                        <button onclick="openAddContent('overview')" class="bg-green-600 hover:bg-green-700 text-white px-4 py-2 rounded-xl transition-all">
                            <i class="fas fa-plus mr-2"></i>Add Content
                        </button>
                    </div>
                    <div id="overview-content" class="space-y-4 mb-8">
                        <!-- Content will be loaded here -->
                    </div>
                    
                    <div class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-4 gap-6 mb-8">
                        <div class="text-center p-6 bg-blue-500 text-white rounded-2xl shadow-lg">
                            <div class="text-3xl font-bold mb-2">6</div>
                            <div>Team Members</div>
                        </div>
                        <div class="text-center p-6 bg-green-500 text-white rounded-2xl shadow-lg">
                            <div class="text-3xl font-bold mb-2">11</div>
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

            <!-- Project Documentation Section -->
            <div class="section-content" id="project-documentation">
                <div class="bg-white dark:bg-gray-800 p-8 rounded-2xl shadow-lg">
                    <div class="flex justify-between items-center mb-6">
                        <h2 class="text-2xl font-bold text-gray-800 dark:text-white">Project Documentation</h2>
                        <button onclick="openAddContent('project-documentation')" class="bg-green-600 hover:bg-green-700 text-white px-4 py-2 rounded-xl transition-all">
                            <i class="fas fa-plus mr-2"></i>Add Content
                        </button>
                    </div>
                    <div id="project-documentation-content" class="space-y-4">
                        <!-- Content will be loaded here -->
                    </div>
                </div>
            </div>

            <!-- Team Collaboration Section -->
            <div class="section-content" id="team-collaboration">
                <div class="bg-white dark:bg-gray-800 p-8 rounded-2xl shadow-lg">
                    <div class="flex justify-between items-center mb-6">
                        <h2 class="text-2xl font-bold text-gray-800 dark:text-white">Team Collaboration</h2>
                        <button onclick="openAddContent('team-collaboration')" class="bg-green-600 hover:bg-green-700 text-white px-4 py-2 rounded-xl transition-all">
                            <i class="fas fa-plus mr-2"></i>Add Content
                        </button>
                    </div>
                    <div id="team-collaboration-content" class="space-y-4"></div>
                </div>
            </div>

            <!-- Team Updates Section -->
            <div class="section-content" id="team-updates">
                <div class="bg-white dark:bg-gray-800 p-8 rounded-2xl shadow-lg">
                    <div class="flex justify-between items-center mb-6">
                        <h2 class="text-2xl font-bold text-gray-800 dark:text-white">Team Updates</h2>
                        <button onclick="openAddContent('team-updates')" class="bg-green-600 hover:bg-green-700 text-white px-4 py-2 rounded-xl transition-all">
                            <i class="fas fa-plus mr-2"></i>Add Content
                        </button>
                    </div>
                    <div id="team-updates-content" class="space-y-4"></div>
                </div>
            </div>
                                </div>
                </div>
            </div>
        </div>
    </div>

    <script>
        // ... JavaScript code ...
    </script>
</body>
</html>
