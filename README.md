<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Security Chaos Engineering Dashboard</title>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <script src="https://cdn.tailwindcss.com"></script>
    
    <!-- Firebase -->
    <script src="https://www.gstatic.com/firebasejs/9.6.0/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.6.0/firebase-database-compat.js"></script>
    
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
    <!-- Login Modal -->
    <div class="modal" id="loginModal" style="display: flex;">
        <div class="bg-white dark:bg-gray-800 p-8 rounded-2xl max-w-md w-full mx-4 shadow-2xl">
            <h2 class="text-2xl font-bold mb-6 text-center text-gray-800 dark:text-white">
                <i class="fas fa-user-lock mr-2"></i>Login to Dashboard
            </h2>
            
            <form id="loginForm" class="space-y-4">
                <div>
                    <label class="block text-sm font-medium mb-2 text-gray-700 dark:text-gray-300">Email</label>
                    <input type="email" id="loginEmail" class="w-full p-3 border border-gray-300 dark:border-gray-600 rounded-xl focus:ring-2 focus:ring-blue-500 focus:border-blue-500 dark:bg-gray-700 dark:text-white" required>
                </div>
                
                <div>
                    <label class="block text-sm font-medium mb-2 text-gray-700 dark:text-gray-300">Password</label>
                    <input type="password" id="loginPassword" class="w-full p-3 border border-gray-300 dark:border-gray-600 rounded-xl focus:ring-2 focus:ring-blue-500 focus:border-blue-500 dark:bg-gray-700 dark:text-white" required>
                </div>
                
                <div class="flex items-center justify-between">
                    <button type="button" onclick="showForgotPassword()" class="text-sm text-blue-600 hover:text-blue-800">
                        Forgot Password?
                    </button>
                </div>
                
                <div class="flex gap-3">
                    <button type="submit" class="flex-1 bg-blue-600 hover:bg-blue-700 text-white py-3 rounded-xl transition-all font-medium">
                        Login
                    </button>
                    <button type="button" onclick="closeLogin()" class="flex-1 bg-gray-500 hover:bg-gray-600 text-white py-3 rounded-xl transition-all font-medium">
                        Cancel
                    </button>
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

    <!-- Add Content Modal -->
    <div class="modal" id="addContentModal">
        <div class="bg-white dark:bg-gray-800 p-8 rounded-2xl max-w-2xl w-full mx-4 shadow-2xl">
            <h2 class="text-2xl font-bold mb-6 text-center text-gray-800 dark:text-white">Add Content</h2>
            <form id="addContentForm" class="space-y-4">
                <input type="hidden" id="contentSection">
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
                        <option value="Marcus Tibell">Marcus Tibell</option>
                        <option value="Jens Annell">Jens Annell</option>
                        <option value="Fahad Hussain">Fahad Hussain</option>
                        <option value="Luwam">Luwam</option>
                        <option value="Stefan Österberg">Stefan Österberg</option>
                        <option value="Najmaddin">Najmaddin</option>
                    </select>
                </div>
                <div class="flex gap-3">
                    <button type="submit" class="flex-1 bg-green-600 hover:bg-green-700 text-white py-3 rounded-xl transition-all font-medium">Save</button>
                    <button type="button" onclick="closeAddContent()" class="flex-1 bg-gray-500 hover:bg-gray-600 text-white py-3 rounded-xl transition-all font-medium">Cancel</button>
                </div>
            </form>
        </div>
    </div>

    <!-- Chat Modal -->
    <div class="modal" id="chatModal">
        <div class="bg-white dark:bg-gray-800 p-6 rounded-2xl mx-4 shadow-2xl" style="width: 500px; max-width: 90vw;">
            <div class="flex justify-between items-center mb-4">
                <h2 class="text-xl font-bold text-gray-800 dark:text-white flex items-center gap-2">
                    <span class="online-indicator"></span>
                    Team Chat
                </h2>
                <button onclick="closeChat()" class="text-gray-500 hover:text-gray-700">
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
                            <div class="w-10 h-10 bg-gradient-to-r from-blue-500 to-purple-500 rounded-full flex items-center justify-center text-white font-bold" id="userAvatar">Y</div>
                            <span class="online-indicator absolute -top-1 -right-1 border-2 border-white dark:border-gray-800"></span>
                        </div>
                        <div class="flex-1 min-w-0">
                            <p class="font-semibold text-gray-800 dark:text-white truncate" id="userName">You</p>
                            <p class="text-sm text-gray-600 dark:text-gray-400 truncate" id="userRole">Active User</p>
                        </div>
                    </div>
                    <button onclick="openChat()" class="w-full bg-blue-600 hover:bg-blue-700 text-white py-3 rounded-lg transition-all">
                        <i class="fas fa-comments mr-2"></i>Team Chat
                    </button>
                    <button onclick="logout()" class="w-full bg-red-600 hover:bg-red-700 text-white py-3 rounded-lg transition-all mt-2">
                        <i class="fas fa-sign-out-alt mr-2"></i>Logout
                    </button>
                </div>
            </div>

            <!-- Team Members -->
            <div class="mb-6">
                <div class="bg-gradient-to-br from-blue-50 to-green-50 dark:from-gray-800 dark:to-gray-700 p-4 rounded-xl shadow-lg border-2 border-green-200 dark:border-green-800">
                    <h3 class="font-semibold mb-3 text-gray-800 dark:text-white flex items-center gap-2">
                        <span class="online-indicator"></span>
                        Team Members
                    </h3>
                    <div id="teamList" class="space-y-2">
                        <div class="flex items-center space-x-3 p-2 bg-white dark:bg-gray-700 rounded-lg">
                            <div class="relative">
                                <div class="w-8 h-8 bg-gradient-to-r from-blue-500 to-purple-500 rounded-full flex items-center justify-center text-white text-sm font-bold">K</div>
                                <span class="online-indicator absolute -top-1 -right-1 border-2 border-white dark:border-gray-700"></span>
                            </div>
                            <div class="flex-1">
                                <div class="font-medium text-gray-800 dark:text-white text-sm">Kaled Osman</div>
                                <div class="text-xs text-gray-600 dark:text-gray-400">Developer</div>
                            </div>
                        </div>
                        <div class="flex items-center space-x-3 p-2 bg-white dark:bg-gray-700 rounded-lg">
                            <div class="relative">
                                <div class="w-8 h-8 bg-gradient-to-r from-blue-500 to-purple-500 rounded-full flex items-center justify-center text-white text-sm font-bold">M</div>
                                <span class="online-indicator absolute -top-1 -right-1 border-2 border-white dark:border-gray-700"></span>
                            </div>
                            <div class="flex-1">
                                <div class="font-medium text-gray-800 dark:text-white text-sm">Marcus Tibell</div>
                                <div class="text-xs text-gray-600 dark:text-gray-400">Engineer</div>
                            </div>
                        </div>
                        <div class="flex items-center space-x-3 p-2 bg-white dark:bg-gray-700 rounded-lg">
                            <div class="relative">
                                <div class="w-8 h-8 bg-gradient-to-r from-blue-500 to-purple-500 rounded-full flex items-center justify-center text-white text-sm font-bold">J</div>
                                <span class="online-indicator absolute -top-1 -right-1 border-2 border-white dark:border-gray-700"></span>
                            </div>
                            <div class="flex-1">
                                <div class="font-medium text-gray-800 dark:text-white text-sm">Jens Annell</div>
                                <div class="text-xs text-gray-600 dark:text-gray-400">Analyst</div>
                            </div>
                        </div>
                        <div class="flex items-center space-x-3 p-2 bg-white dark:bg-gray-700 rounded-lg">
                            <div class="relative">
                                <div class="w-8 h-8 bg-gradient-to-r from-blue-500 to-purple-500 rounded-full flex items-center justify-center text-white text-sm font-bold">F</div>
                                <span class="online-indicator absolute -top-1 -right-1 border-2 border-white dark:border-gray-700"></span>
                            </div>
                            <div class="flex-1">
                                <div class="font-medium text-gray-800 dark:text-white text-sm">Fahad Hussain</div>
                                <div class="text-xs text-gray-600 dark:text-gray-400">Researcher</div>
                            </div>
                        </div>
                        <div class="flex items-center space-x-3 p-2 bg-white dark:bg-gray-700 rounded-lg">
                            <div class="relative">
                                <div class="w-8 h-8 bg-gradient-to-r from-blue-500 to-purple-500 rounded-full flex items-center justify-center text-white text-sm font-bold">L</div>
                                <span class="online-indicator absolute -top-1 -right-1 border-2 border-white dark:border-gray-700"></span>
                            </div>
                            <div class="flex-1">
                                <div class="font-medium text-gray-800 dark:text-white text-sm">Luwam</div>
                                <div class="text-xs text-gray-600 dark:text-gray-400">Designer</div>
                            </div>
                        </div>
                        <div class="flex items-center space-x-3 p-2 bg-white dark:bg-gray-700 rounded-lg">
                            <div class="relative">
                                <div class="w-8 h-8 bg-gradient-to-r from-blue-500 to-purple-500 rounded-full flex items-center justify-center text-white text-sm font-bold">S</div>
                                <span class="online-indicator absolute -top-1 -right-1 border-2 border-white dark:border-gray-700"></span>
                            </div>
                            <div class="flex-1">
                                <div class="font-medium text-gray-800 dark:text-white text-sm">Stefan Österberg</div>
                                <div class="text-xs text-gray-600 dark:text-gray-400">Architect</div>
                            </div>
                        </div>
                        <div class="flex items-center space-x-3 p-2 bg-white dark:bg-gray-700 rounded-lg">
                            <div class="relative">
                                <div class="w-8 h-8 bg-gradient-to-r from-blue-500 to-purple-500 rounded-full flex items-center justify-center text-white text-sm font-bold">N</div>
                                <span class="online-indicator absolute -top-1 -right-1 border-2 border-white dark:border-gray-700"></span>
                            </div>
                            <div class="flex-1">
                                <div class="font-medium text-gray-800 dark:text-white text-sm">Najmaddin</div>
                                <div class="text-xs text-gray-600 dark:text-gray-400">Security Expert</div>
                            </div>
                        </div>
                    </div>
                </div>
            </div>

            <!-- Navigation -->
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
        <div class="flex-1 p-8">
            <header class="mb-8">
                <h1 class="text-3xl font-bold text-gray-800 dark:text-white">Security Chaos Engineering Dashboard</h1>
                <p class="text-gray-600 dark:text-gray-400">Group 1 - Real-time Team Collaboration</p>
            </header>

            <!-- Overview -->
            <div class="section-content active" id="overview">
                <div class="bg-white dark:bg-gray-800 p-8 rounded-2xl shadow-lg">
                    <div class="flex justify-between items-center mb-6">
                        <h2 class="text-2xl font-bold text-gray-800 dark:text-white">Overview</h2>
                        <button onclick="openAddContent('overview')" class="bg-green-600 hover:bg-green-700 text-white px-4 py-2 rounded-xl transition-all">
                            <i class="fas fa-plus mr-2"></i>Add Content
                        </button>
                    </div>
                    <div id="overview-content" class="space-y-4">
                        <!-- Content loaded from Firebase -->
                    </div>
                </div>
            </div>

            <!-- Project Documentation -->
            <div class="section-content" id="project-documentation">
                <div class="bg-white dark:bg-gray-800 p-8 rounded-2xl shadow-lg">
                    <div class="flex justify-between items-center mb-6">
                        <h2 class="text-2xl font-bold text-gray-800 dark:text-white">Project Documentation</h2>
                        <button onclick="openAddContent('project-documentation')" class="bg-green-600 hover:bg-green-700 text-white px-4 py-2 rounded-xl transition-all">
                            <i class="fas fa-plus mr-2"></i>Add Content
                        </button>
                    </div>
                    <div id="project-documentation-content" class="space-y-4"></div>
                </div>
            </div>

            <!-- Team Collaboration -->
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

            <!-- Team Updates -->
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

            <!-- AI Assistant -->
            <div class="section-content" id="ai-assistant">
                <div class="bg-white dark:bg-gray-800 p-8 rounded-2xl shadow-lg">
                    <div class="flex justify-between items-center mb-6">
                        <h2 class="text-2xl font-bold text-gray-800 dark:text-white">AI Assistant</h2>
                        <button onclick="openAddContent('ai-assistant')" class="bg-green-600 hover:bg-green-700 text-white px-4 py-2 rounded-xl transition-all">
                            <i class="fas fa-plus mr-2"></i>Add Content
                        </button>
                    </div>
                    <div id="ai-assistant-content" class="space-y-4"></div>
                </div>
            </div>

            <!-- Practical Tasks -->
            <div class="section-content" id="practical-tasks">
                <div class="bg-white dark:bg-gray-800 p-8 rounded-2xl shadow-lg">
                    <div class="flex justify-between items-center mb-6">
                        <h2 class="text-2xl font-bold text-gray-800 dark:text-white">Practical Tasks</h2>
                        <button onclick="openAddContent('practical-tasks')" class="bg-green-600 hover:bg-green-700 text-white px-4 py-2 rounded-xl transition-all">
                            <i class="fas fa-plus mr-2"></i>Add Content
                        </button>
                    </div>
                    <div id="practical-tasks-content" class="space-y-4"></div>
                </div>
            </div>

            <!-- Implementation -->
            <div class="section-content" id="implementation">
                <div class="bg-white dark:bg-gray-800 p-8 rounded-2xl shadow-lg">
                    <div class="flex justify-between items-center mb-6">
                        <h2 class="text-2xl font-bold text-gray-800 dark:text-white">Implementation</h2>
                        <button onclick="openAddContent('implementation')" class="bg-green-600 hover:bg-green-700 text-white px-4 py-2 rounded-xl transition-all">
                            <i class="fas fa-plus mr-2"></i>Add Content
                        </button>
                    </div>
                    <div id="implementation-content" class="space-y-4"></div>
                </div>
            </div>

            <!-- Research -->
            <div class="section-content" id="research">
                <div class="bg-white dark:bg-gray-800 p-8 rounded-2xl shadow-lg">
                    <div class="flex justify-between items-center mb-6">
                        <h2 class="text-2xl font-bold text-gray-800 dark:text-white">Research</h2>
                        <button onclick="openAddContent('research')" class="bg-green-600 hover:bg-green-700 text-white px-4 py-2 rounded-xl transition-all">
                            <i class="fas fa-plus mr-2"></i>Add Content
                        </button>
                    </div>
                    <div id="research-content" class="space-y-4"></div>
                </div>
            </div>

            <!-- Resources -->
            <div class="section-content" id="resources">
                <div class="bg-white dark:bg-gray-800 p-8 rounded-2xl shadow-lg">
                    <div class="flex justify-between items-center mb-6">
                        <h2 class="text-2xl font-bold text-gray-800 dark:text-white">Resources</h2>
                        <button onclick="openAddContent('resources')" class="bg-green-600 hover:bg-green-700 text-white px-4 py-2 rounded-xl transition-all">
                            <i class="fas fa-plus mr-2"></i>Add Content
                        </button>
                    </div>
                    <div id="resources-content" class="space-y-4"></div>
                </div>
            </div>

            <!-- Security Testing -->
            <div class="section-content" id="security-testing">
                <div class="bg-white dark:bg-gray-800 p-8 rounded-2xl shadow-lg">
                    <div class="flex justify-between items-center mb-6">
                        <h2 class="text-2xl font-bold text-gray-800 dark:text-white">Security Testing</h2>
                        <button onclick="openAddContent('security-testing')" class="bg-green-600 hover:bg-green-700 text-white px-4 py-2 rounded-xl transition-all">
                            <i class="fas fa-plus mr-2"></i>Add Content
                        </button>
                    </div>
                    <div id="security-testing-content" class="space-y-4"></div>
                </div>
            </div>

            <!-- Monitoring & Analytics -->
            <div class="section-content" id="monitoring-analytics">
                <div class="bg-white dark:bg-gray-800 p-8 rounded-2xl shadow-lg">
                    <div class="flex justify-between items-center mb-6">
                        <h2 class="text-2xl font-bold text-gray-800 dark:text-white">Monitoring & Analytics</h2>
                        <button onclick="openAddContent('monitoring-analytics')" class="bg-green-600 hover:bg-green-700 text-white px-4 py-2 rounded-xl transition-all">
                            <i class="fas fa-plus mr-2"></i>Add Content
                        </button>
                    </div>
                    <div id="monitoring-analytics-content" class="space-y-4"></div>
                </div>
            </div>
        </div>
    </div>

    <script>
        // Firebase Configuration - Use your credentials
        const firebaseConfig = {
            apiKey: "AIzaSyDJsZ4LZVrBucavpTdhXbKxyE_BFeZFFKs",
            authDomain: "fir-console-df3e9.firebaseapp.com",
            projectId: "fir-console-df3e9",
            storageBucket: "fir-console-df3e9.firebasestorage.app",
            messagingSenderId: "750795336412",
            appId: "1:750795336412:web:abfd0c06941a9418abe219"
        };

        // Initialize Firebase
        let firebaseApp;
        let database;
        let currentUser = { name: 'You', role: 'Active User', avatar: 'Y' };

        try {
            firebaseApp = firebase.initializeApp(firebaseConfig);
            database = firebase.database();
            console.log('Firebase initialized successfully');
        } catch (error) {
            console.log('Firebase initialization failed:', error);
        }

        // Users data
        const users = [
            { id: 1, name: "Kaled Osman", email: "kaled@team.com", password: "kaled123", role: "Developer", avatar: "K" },
            { id: 2, name: "Marcus Tibell", email: "marcus@team.com", password: "marcus123", role: "Engineer", avatar: "M" },
            { id: 3, name: "Jens Annell", email: "jens@team.com", password: "jens123", role: "Analyst", avatar: "J" },
            { id: 4, name: "Fahad Hussain", email: "fahad@team.com", password: "fahad123", role: "Researcher", avatar: "F" },
            { id: 5, name: "Luwam", email: "luwam@team.com", password: "luwam123", role: "Designer", avatar: "L" },
            { id: 6, name: "Stefan Österberg", email: "stefan@team.com", password: "stefan123", role: "Architect", avatar: "S" },
            { id: 7, name: "Najmaddin", email: "najmaddin@team.com", password: "najmaddin123", role: "Security Expert", avatar: "N" }
        ];

        // Load saved user
        const savedUser = localStorage.getItem('dashboard_user');
        if (savedUser) {
            currentUser = JSON.parse(savedUser);
            updateUserUI();
            document.getElementById('loginModal').style.display = 'none';
        }

        // Login Form
        document.getElementById('loginForm').addEventListener('submit', function(e) {
            e.preventDefault();
            const email = document.getElementById('loginEmail').value;
            const password = document.getElementById('loginPassword').value;
            
            const user = users.find(u => u.email === email && u.password === password);
            if (user) {
                currentUser = user;
                localStorage.setItem('dashboard_user', JSON.stringify(user));
                updateUserUI();
                document.getElementById('loginModal').style.display = 'none';
                alert(`Welcome ${user.name}!`);
            } else {
                alert('Invalid email or password');
            }
        });

        function updateUserUI() {
            document.getElementById('userName').textContent = currentUser.name;
            document.getElementById('userRole').textContent = currentUser.role;
            document.getElementById('userAvatar').textContent = currentUser.avatar;
        }

        function logout() {
            currentUser = { name: 'You', role: 'Active User', avatar: 'Y' };
            localStorage.removeItem('dashboard_user');
            updateUserUI();
            document.getElementById('loginModal').style.display = 'flex';
        }

        function closeLogin() {
            document.getElementById('loginModal').style.display = 'none';
        }

        // Content Management
        async function loadSectionContent(section) {
            if (!database) return;
            
            try {
                const snapshot = await database.ref(`content/${section}`).once('value');
                const content = snapshot.val() || [];
                displaySectionContent(section, content);
            } catch (error) {
                console.error('Error loading content:', error);
            }
        }

        function displaySectionContent(section, contentArray) {
            const container = document.getElementById(`${section}-content`);
            if (!container) return;
            
            container.innerHTML = '';
            
            if (contentArray.length === 0) {
                container.innerHTML = `
                    <div class="text-center p-8 text-gray-500 dark:text-gray-400">
                        <i class="fas fa-inbox text-4xl mb-4"></i>
                        <p>No content added yet. Be the first to share something!</p>
                    </div>
                `;
                return;
            }
            
            contentArray.forEach(item => {
                const div = document.createElement('div');
                div.className = 'p-6 border border-gray-200 dark:border-gray-700 rounded-2xl bg-white dark:bg-gray-800 mb-4';
                div.innerHTML = `
                    <h3 class="text-xl font-semibold text-gray-900 dark:text-white mb-2">${item.title}</h3>
                    <p class="text-gray-700 dark:text-gray-300 mb-4 whitespace-pre-line">${item.description}</p>
                    <div class="flex justify-between items-center text-sm text-gray-600 dark:text-gray-400">
                        <span><i class="fas fa-user mr-1"></i> ${item.author}</span>
                        <span><i class="fas fa-clock mr-1"></i> ${new Date(item.date).toLocaleDateString()}</span>
                    </div>
                `;
                container.appendChild(div);
            });
        }

        async function saveContent(section, title, description, author) {
            if (!database) {
                alert('Firebase not connected. Content saved locally only.');
                return;
            }
            
            try {
                const snapshot = await database.ref(`content/${section}`).once('value');
                const existingContent = snapshot.val() || [];
                
                const newContent = {
                    id: Date.now().toString(),
                    title,
                    description,
                    author,
                    date: new Date().toISOString()
                };
                
                existingContent.push(newContent);
                await database.ref(`content/${section}`).set(existingContent);
                
                alert('Content saved successfully! All team members can see it.');
                loadSectionContent(section);
                return true;
            } catch (error) {
                console.error('Error saving content:', error);
                alert('Failed to save content. Please try again.');
                return false;
            }
        }

        // Form submission for content
        document.getElementById('addContentForm').addEventListener('submit', async function(e) {
            e.preventDefault();
            const section = document.getElementById('contentSection').value;
            const title = document.getElementById('contentTitle').value;
            const description = document.getElementById('contentDescription').value;
            const author = document.getElementById('contentAuthor').value;
            
            const success = await saveContent(section, title, description, author);
            if (success) {
                closeAddContent();
            }
        });

        function openAddContent(section) {
            document.getElementById('contentSection').value = section;
            document.getElementById('addContentModal').classList.add('active');
            document.getElementById('contentTitle').value = '';
            document.getElementById('contentDescription').value = '';
            document.getElementById('contentAuthor').value = currentUser.name;
        }

        function closeAddContent() {
            document.getElementById('addContentModal').classList.remove('active');
        }

        // Chat System
        async function sendChatMessage() {
            const input = document.getElementById('chatInput');
            const message = input.value.trim();
            
            if (message && database) {
                try {
                    const chatMessage = {
                        user: currentUser.name,
                        text: message,
                        timestamp: new Date().toISOString(),
                        id: Date.now().toString()
                    };
                    
                    await database.ref('chat').push(chatMessage);
                    input.value = '';
                } catch (error) {
                    console.error('Error sending message:', error);
                    alert('Failed to send message');
                }
            }
        }

        function loadChatMessages() {
            if (!database) return;
            
            database.ref('chat').on('value', (snapshot) => {
                const messages = snapshot.val();
                const chatContainer = document.getElementById('chatMessages');
                chatContainer.innerHTML = '';
                
                if (!messages) {
                    chatContainer.innerHTML = `
                        <div class="text-center text-gray-500 text-sm py-4">
                            <i class="fas fa-comments text-xl mb-2 block"></i>
                            No messages yet
                        </div>
                    `;
                    return;
                }
                
                const messagesArray = Object.values(messages);
                messagesArray.sort((a, b) => new Date(a.timestamp) - new Date(b.timestamp));
                
                messagesArray.forEach(msg => {
                    const div = document.createElement('div');
                    const isCurrentUser = msg.user === currentUser.name;
                    div.className = `p-3 mb-2 rounded-lg max-w-[85%] ${isCurrentUser ? 'bg-blue-500 text-white ml-auto' : 'bg-gray-200 text-gray-800 dark:bg-gray-700 dark:text-white'}`;
                    div.innerHTML = `
                        <div class="font-semibold text-sm">${msg.user}</div>
                        <div class="break-words">${msg.text}</div>
                        <div class="text-xs opacity-70 mt-1">${new Date(msg.timestamp).toLocaleTimeString()}</div>
                    `;
                    chatContainer.appendChild(div);
                });
                
                chatContainer.scrollTop = chatContainer.scrollHeight;
            });
        }

        function openChat() {
            document.getElementById('chatModal').classList.add('active');
            loadChatMessages();
        }

        function closeChat() {
            document.getElementById('chatModal').classList.remove('active');
        }

        // Enter key in chat
        document.getElementById('chatInput').addEventListener('keypress', function(e) {
            if (e.key === 'Enter') {
                sendChatMessage();
            }
        });

        // Section Navigation
        document.querySelectorAll('.section-btn').forEach(btn => {
            btn.addEventListener('click', function() {
                const section = this.getAttribute('data-section');
                
                // Update active button
                document.querySelectorAll('.section-btn').forEach(b => {
                    b.classList.remove('active-section');
                    b.classList.add('hover:bg-gray-100', 'dark:hover:bg-gray-700', 'text-gray-700', 'dark:text-gray-300');
                });
                this.classList.add('active-section');
                this.classList.remove('hover:bg-gray-100', 'dark:hover:bg-gray-700', 'text-gray-700', 'dark:text-gray-300');
                
                // Show active section
                document.querySelectorAll('.section-content').forEach(content => {
                    content.classList.remove('active');
                });
                document.getElementById(section).classList.add('active');
                
                // Load content for this section
                loadSectionContent(section);
            });
        });

        // Dark mode toggle
        function toggleTheme() {
            const html = document.documentElement;
            if (html.classList.contains('dark')) {
                html.classList.remove('dark');
                localStorage.setItem('theme', 'light');
            } else {
                html.classList.add('dark');
                localStorage.setItem('theme', 'dark');
            }
        }

        // Initialize
        document.addEventListener('DOMContentLoaded', function() {
            const savedTheme = localStorage.getItem('theme');
            if (savedTheme === 'dark') {
                document.documentElement.classList.add('dark');
            }
            
            // Load overview content initially
            loadSectionContent('overview');
            
            // Setup real-time listeners for all sections
            if (database) {
                const sections = [
                    'overview', 'project-documentation', 'team-collaboration', 'team-updates',
                    'ai-assistant', 'practical-tasks', 'implementation', 'research',
                    'resources', 'security-testing', 'monitoring-analytics'
                ];
                
                sections.forEach(section => {
                    database.ref(`content/${section}`).on('value', (snapshot) => {
                        const content = snapshot.val() || [];
                        displaySectionContent(section, content);
                    });
                });
            }
        });
    </script>
</body>
</html>
