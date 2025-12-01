<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Security Chaos Engineering Dashboard</title>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <script src="https://cdn.tailwindcss.com"></script>
    
    <!-- إضافة Firebase -->
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
        .new-message { animation: highlight 2s ease; }
        @keyframes highlight {
            0% { background-color: rgba(59, 130, 246, 0.3); }
            100% { background-color: transparent; }
        }
        .chat-container {
            max-height: 300px;
            overflow-y: auto;
        }
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
                <button onclick="sendChatMessage()" class="bg-blue-600 hover:bg-blue-700 text-white px-4 py-2 rounded-lg">
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

    <!-- Forgot Password Modal -->
    <div class="modal" id="forgotPasswordModal">
        <div class="bg-white dark:bg-gray-800 p-8 rounded-2xl max-w-md w-full mx-4 shadow-2xl">
            <h2 class="text-2xl font-bold mb-6 text-center text-gray-800 dark:text-white">
                <i class="fas fa-key mr-2"></i>Password Recovery
            </h2>
            
            <form id="forgotPasswordForm" class="space-y-4">
                <div>
                    <label class="block text-sm font-medium mb-2 text-gray-700 dark:text-gray-300">Email Address</label>
                    <input type="email" id="recoveryEmail" class="w-full p-3 border border-gray-300 dark:border-gray-600 rounded-xl focus:ring-2 focus:ring-blue-500 focus:border-blue-500 dark:bg-gray-700 dark:text-white" required>
                </div>
                
                <div class="flex gap-3">
                    <button type="submit" class="flex-1 bg-green-600 hover:bg-green-700 text-white py-3 rounded-xl transition-all font-medium">
                        Send Recovery Link
                    </button>
                    <button type="button" onclick="closeForgotPassword()" class="flex-1 bg-gray-500 hover:bg-gray-600 text-white py-3 rounded-xl transition-all font-medium">
                        Back
                    </button>
                </div>
            </form>
        </div>
    </div>

    <!-- Main Layout -->
    <div class="flex min-h-screen">
        <!-- Sidebar -->
        <div class="w-80 bg-gray-50 dark:bg-gray-800 flex-shrink-0 p-6 border-r border-gray-200 dark:border-gray-700">
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

            <!-- Admin Panel -->
            <div id="adminPanel"></div>

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

            <!-- Theme Toggle -->
            <div class="mt-auto">
                <button onclick="toggleTheme()" class="w-full p-4 rounded-xl theme-transition bg-gray-100 hover:bg-gray-200 dark:bg-gray-700 dark:hover:bg-gray-600 text-gray-700 dark:text-gray-300 flex items-center justify-center font-medium">
                    <i class="fas fa-moon mr-3"></i>Toggle Theme
                </button>
            </div>
        </div>

        <!-- Main Content -->
        <div class="flex-1 p-8 overflow-auto">
            <header class="mb-8">
                <h1 class="text-3xl font-bold text-gray-800 dark:text-white">Security Chaos Engineering Dashboard</h1>
                <p class="text-gray-600 dark:text-gray-400">Group 1 - Real-time Team Collaboration</p>
            </header>

            <!-- Overview Section -->
            <div class="section-content active" id="overview">
                <div class="bg-white dark:bg-gray-800 p-8 rounded-2xl shadow-lg mb-6">
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
                            <div class="text-3xl font-bold mb-2">7</div>
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

            <!-- Other Sections -->
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
        // 🔥 إعدادات Firebase
        const firebaseConfig = {
            apiKey: "AIzaSyDJsZ4LZVrBucavpTdhXbKxyE_BFeZFFKs",
            authDomain: "fir-console-df3e9.firebaseapp.com",
            projectId: "fir-console-df3e9",
            storageBucket: "fir-console-df3e9.firebasestorage.app",
            messagingSenderId: "750795336412",
            appId: "1:750795336412:web:abfd0c06941a9418abe219"
        };

        // TEAM MEMBERS DATA - بالترتيب المطلوب مع إضافة Najmaddin
        const teamMembers = [
            { id: '1', name: 'You', role: 'Team Member' },
            { id: '2', name: 'Kaled Osman', role: 'Developer' },
            { id: '3', name: 'Marcus Tibell', role: 'Engineer' },
            { id: '4', name: 'Jens Annell', role: 'Analyst' },
            { id: '5', name: 'Fahad Hussain', role: 'Researcher' },
            { id: '6', name: 'Luwam', role: 'Designer' },
            { id: '7', name: 'Stefan Österberg', role: 'Architect' },
            { id: '8', name: 'Najmaddin', role: 'Security Expert' }
        ];

        // 🔄 نظام الدردشة الحقيقي مع Firebase
        class RealTimeChat {
            constructor() {
                this.currentUser = 'You';
                this.isConnected = false;
                this.messageCount = 0;
                this.unreadCount = 0;
                this.lastReadTime = Date.now();
                this.initFirebase();
            }

            initFirebase() {
                try {
                    firebase.initializeApp(firebaseConfig);
                    this.database = firebase.database();
                    this.isConnected = true;
                    console.log('✅ Firebase connected successfully!');
                    this.updateChatStatus(true);
                    this.setupChatListener();
                } catch (error) {
                    console.log('❌ Firebase connection failed, using local storage');
                    this.isConnected = false;
                    this.updateChatStatus(false);
                    this.setupLocalChat();
                }
            }

            updateChatStatus(connected) {
                const statusElement = document.getElementById('chatStatus');
                if (statusElement) {
                    statusElement.style.background = connected ? '#10B981' : '#EF4444';
                    statusElement.title = connected ? 'Connected to Firebase' : 'Using Local Storage';
                }
            }

            setupChatListener() {
                if (!this.isConnected) return;
                
                const chatRef = this.database.ref('team-chat');
                chatRef.on('child_added', (snapshot) => {
                    const message = snapshot.val();
                    this.displayMessage(message);
                    this.messageCount++;
                    this.updateMessageCount();
                    
                    // إشعار بالرسائل الجديدة
                    if (message.timestamp > this.lastReadTime && !this.isChatOpen()) {
                        this.unreadCount++;
                        this.updateUnreadCount();
                    }
                });
            }

            setupLocalChat() {
                const messages = JSON.parse(localStorage.getItem('team-chat-messages')) || [];
                messages.forEach(msg => this.displayMessage(msg));
                this.messageCount = messages.length;
                this.updateMessageCount();
            }

            async saveMessage(text) {
                const message = {
                    user: this.currentUser,
                    text: text,
                    timestamp: Date.now(),
                    id: Date.now().toString()
                };

                if (this.isConnected) {
                    // حفظ في Firebase
                    try {
                        await this.database.ref('team-chat').push(message);
                        return true;
                    } catch (error) {
                        console.error('Firebase error:', error);
                        return false;
                    }
                } else {
                    // حفظ في localStorage إذا فشل الاتصال
                    this.saveToLocalStorage(message);
                    return true;
                }
            }

            saveToLocalStorage(message) {
                const messages = JSON.parse(localStorage.getItem('team-chat-messages')) || [];
                messages.push(message);
                if (messages.length > 100) messages.shift();
                localStorage.setItem('team-chat-messages', JSON.stringify(messages));
                this.displayMessage(message);
                this.messageCount++;
                this.updateMessageCount();
            }

            displayMessage(message) {
                const chatMessages = document.getElementById('chatMessages');
                if (!chatMessages) return;

                // إزالة رسالة التحميل إذا كانت موجودة
                if (chatMessages.querySelector('.text-center')) {
                    chatMessages.innerHTML = '';
                }

                const messageElement = document.createElement('div');
                const isOwnMessage = message.user === this.currentUser;
                messageElement.className = `p-3 mb-2 rounded-lg max-w-[85%] ${isOwnMessage ? 'bg-blue-500 text-white ml-auto' : 'bg-gray-200 text-gray-800 dark:bg-gray-700 dark:text-white'} new-message`;
                messageElement.innerHTML = `
                    <div class="font-semibold text-sm">${message.user}</div>
                    <div class="break-words">${message.text}</div>
                    <div class="text-xs opacity-70 mt-1">${new Date(message.timestamp).toLocaleTimeString()}</div>
                `;
                chatMessages.appendChild(messageElement);
                chatMessages.scrollTop = chatMessages.scrollHeight;
            }

            updateMessageCount() {
                const countElement = document.getElementById('messageCount');
                if (countElement) {
                    countElement.textContent = this.messageCount;
                }
            }

            updateUnreadCount() {
                const unreadElement = document.getElementById('unreadCount');
                if (unreadElement) {
                    if (this.unreadCount > 0) {
                        unreadElement.textContent = this.unreadCount;
                        unreadElement.classList.remove('hidden');
                    } else {
                        unreadElement.classList.add('hidden');
                    }
                }
            }

            isChatOpen() {
                const modal = document.getElementById('teamChatModal');
                return modal && modal.classList.contains('active');
            }

            markAsRead() {
                this.unreadCount = 0;
                this.lastReadTime = Date.now();
                this.updateUnreadCount();
            }
        }

        // CONTENT MANAGEMENT SYSTEM
        let userContents = JSON.parse(localStorage.getItem('userContents')) || {};

        // LOGIN SYSTEM
        class LoginSystem {
            constructor() {
                this.users = JSON.parse(localStorage.getItem('teamUsers')) || this.createDefaultUsers();
                this.currentUser = JSON.parse(localStorage.getItem('currentUser')) || null;
                this.init();
            }

            createDefaultUsers() {
                const defaultUsers = [
                    { id: 1, name: "Kaled Osman", email: "kaled@team.com", password: "kaled123", role: "Developer", avatar: "K" },
                    { id: 2, name: "Marcus Tibell", email: "marcus@team.com", password: "marcus123", role: "Engineer", avatar: "M" },
                    { id: 3, name: "Jens Annell", email: "jens@team.com", password: "jens123", role: "Analyst", avatar: "J" },
                    { id: 4, name: "Fahad Hussain", email: "fahad@team.com", password: "fahad123", role: "Researcher", avatar: "F" },
                    { id: 5, name: "Luwam", email: "luwam@team.com", password: "luwam123", role: "Designer", avatar: "L" },
                    { id: 6, name: "Stefan Österberg", email: "stefan@team.com", password: "stefan123", role: "Architect", avatar: "S" },
                    { id: 7, name: "Najmaddin", email: "najmaddin@team.com", password: "najmaddin123", role: "Security Expert", avatar: "N" }
                ];
                localStorage.setItem('teamUsers', JSON.stringify(defaultUsers));
                return defaultUsers;
            }

            init() {
                if (this.currentUser) {
                    this.hideLoginModal();
                    this.updateUIForLoggedInUser();
                }

                this.setupEventListeners();
            }

            setupEventListeners() {
                document.getElementById('loginForm').addEventListener('submit', (e) => {
                    e.preventDefault();
                    this.login();
                });

                document.getElementById('forgotPasswordForm').addEventListener('submit', (e) => {
                    e.preventDefault();
                    this.recoverPassword();
                });
            }

            login() {
                const email = document.getElementById('loginEmail').value;
                const password = document.getElementById('loginPassword').value;

                const user = this.users.find(u => u.email === email && u.password === password);
                
                if (user) {
                    this.currentUser = user;
                    localStorage.setItem('currentUser', JSON.stringify(user));
                    this.hideLoginModal();
                    this.updateUIForLoggedInUser();
                    this.showNotification(`Welcome ${user.name}! Login successful`, 'success');
                    
                    // تحديث اسم المستخدم في الدردشة
                    chatSystem.currentUser = user.name;
                    
                    // Initialize admin system if user is admin
                    if (user.email === 'kaled@team.com') {
                        adminSystem.init();
                    }
                } else {
                    this.showNotification('Invalid email or password', 'error');
                }
            }

            logout() {
                this.currentUser = null;
                localStorage.removeItem('currentUser');
                this.showLoginModal();
                this.updateUIForLoggedOutUser();
                this.showNotification('Logged out successfully', 'info');
                chatSystem.currentUser = 'You';
            }

            recoverPassword() {
                const email = document.getElementById('recoveryEmail').value;
                const user = this.users.find(u => u.email === email);
                
                if (user) {
                    this.showNotification(`Password recovery link sent to ${email}`, 'success');
                    this.closeForgotPassword();
                } else {
                    this.showNotification('Email not found in system', 'error');
                }
            }

            showLoginModal() {
                document.getElementById('loginModal').style.display = 'flex';
            }

            hideLoginModal() {
                document.getElementById('loginModal').style.display = 'none';
            }

            updateUIForLoggedInUser() {
                const userName = document.getElementById('userName');
                const userRole = document.getElementById('userRole');
                const userInfo = document.querySelector('.bg-white.dark\\:bg-gray-700.p-4.rounded-xl');
                
                if (userName) userName.textContent = this.currentUser.name;
                if (userRole) userRole.textContent = this.currentUser.role;
                
                if (userInfo) {
                    userInfo.innerHTML = `
                        <div class="flex items-center space-x-3 mb-3">
                            <div class="relative">
                                <div class="w-10 h-10 bg-gradient-to-r from-blue-500 to-purple-500 rounded-full flex items-center justify-center text-white font-bold">
                                    ${this.currentUser.avatar}
                                </div>
                                <span class="online-indicator absolute -top-1 -right-1 border-2 border-white dark:border-gray-800"></span>
                            </div>
                            <div class="flex-1 min-w-0">
                                <p class="font-semibold text-gray-800 dark:text-white truncate">${this.currentUser.name}</p>
                                <p class="text-sm text-gray-600 dark:text-gray-400 truncate">${this.currentUser.role}</p>
                            </div>
                        </div>
                        <div class="space-y-2">
                            <button onclick="openTeamChat()" class="w-full bg-blue-600 hover:bg-blue-700 text-white py-2 rounded-lg transition-all flex items-center justify-center">
                                <i class="fas fa-comments mr-2"></i>Team Chat
                                <span id="unreadCount" class="ml-2 bg-red-500 text-white text-xs px-2 py-1 rounded-full hidden">0</span>
                            </button>
                            <button onclick="loginSystem.logout()" class="w-full bg-red-600 hover:bg-red-700 text-white py-2 rounded-lg transition-all">
                                <i class="fas fa-sign-out-alt mr-2"></i>Logout
                            </button>
                        </div>
                    `;
                }
            }

            updateUIForLoggedOutUser() {
                const userName = document.getElementById('userName');
                const userRole = document.getElementById('userRole');
                const userInfo = document.querySelector('.bg-white.dark\\:bg-gray-700.p-4.rounded-xl');
                
                if (userName) userName.textContent = 'You';
                if (userRole) userRole.textContent = 'Active User';
                
                if (userInfo) {
                    userInfo.innerHTML = `
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
                        <button onclick="openTeamChat()" class="w-full bg-blue-600 hover:bg-blue-700 text-white py-3 rounded-lg transition-all flex items-center justify-center">
                            <i class="fas fa-comments mr-2"></i>Team Chat
                            <span id="unreadCount" class="ml-2 bg-red-500 text-white text-xs px-2 py-1 rounded-full hidden">0</span>
                        </button>
                    `;
                }
            }

            showNotification(message, type = 'info') {
                alert(message);
            }
        }

        // ADMIN SYSTEM
        class AdminSystem {
            constructor() {
                this.adminUser = 'kaled@team.com';
                this.currentUser = JSON.parse(localStorage.getItem('currentUser')) || null;
            }

            init() {
                if (this.isAdmin()) {
                    this.addAdminControls();
                }
            }

            isAdmin() {
                return this.currentUser && this.currentUser.email === this.adminUser;
            }

            addAdminControls() {
                const adminPanel = document.getElementById('adminPanel');
                if (adminPanel) {
                    adminPanel.innerHTML = `
                        <div class="mt-6 p-4 bg-yellow-100 dark:bg-yellow-900 border border-yellow-400 rounded-xl">
                            <h3 class="font-semibold mb-2 text-yellow-800 dark:text-yellow-200 flex items-center gap-2">
                                <i class="fas fa-crown"></i>
                                Admin Panel
                            </h3>
                            <div class="space-y-2">
                                <button onclick="adminSystem.manageUsers()" class="w-full bg-yellow-600 hover:bg-yellow-700 text-white py-2 rounded-lg transition-all text-sm">
                                    <i class="fas fa-users-cog mr-2"></i>Manage Users
                                </button>
                                <button onclick="adminSystem.deleteAllContent()" class="w-full bg-red-600 hover:bg-red-700 text-white py-2 rounded-lg transition-all text-sm">
                                    <i class="fas fa-trash mr-2"></i>Delete All Content
                                </button>
                                <button onclick="adminSystem.clearChat()" class="w-full bg-purple-600 hover:bg-purple-700 text-white py-2 rounded-lg transition-all text-sm">
                                    <i class="fas fa-comments mr-2"></i>Clear Chat
                                </button>
                            </div>
                        </div>
                    `;
                }
            }

            manageUsers() {
                alert('Admin Feature: User Management - Only Kaled can access this!');
            }

            deleteAllContent() {
                if (confirm('🚨 ARE YOU SURE? This will delete ALL content from ALL sections!')) {
                    localStorage.removeItem('userContents');
                    userContents = {};
                    initializeAllContents();
                    alert('All content has been deleted by admin');
                }
            }

            clearChat() {
                if (confirm('Clear all chat messages?')) {
                    if (chatSystem.isConnected) {
                        chatSystem.database.ref('team-chat').remove();
                    } else {
                        localStorage.removeItem('team-chat-messages');
                    }
                    document.getElementById('chatMessages').innerHTML = `
                        <div class="text-center text-gray-500 text-sm py-4">
                            <i class="fas fa-comments text-xl mb-2 block"></i>
                            Chat cleared by admin
                        </div>
                    `;
                    chatSystem.messageCount = 0;
                    chatSystem.updateMessageCount();
                }
            }
        }

        // Initialize systems
        const chatSystem = new RealTimeChat();
        const loginSystem = new LoginSystem();
        const adminSystem = new AdminSystem();

        // Initialize default content
        function initializeDefaultContent() {
            const defaultContent = {
                'overview': [
                    {
                        id: '1',
                        title: '🚀 Welcome to Security Chaos Engineering Dashboard',
                        description: 'This is your team collaboration platform. All 11 sections are now fully functional with real-time chat and content management.',
                        author: 'System',
                        date: new Date().toISOString()
                    }
                ],
                'team-collaboration': [
                    {
                        id: '2',
                        title: 'Team Collaboration Guidelines',
                        description: 'This section is for coordinating team efforts. Share meeting notes, assign tasks, and track progress on our security chaos engineering project.',
                        author: 'Kaled Osman',
                        date: new Date().toISOString()
                    }
                ],
                'team-updates': [
                    {
                        id: '3',
                        title: 'Weekly Status Update',
                        description: 'Team meeting scheduled for Friday at 2 PM. Please come prepared with updates on your assigned tasks.',
                        author: 'Fahad Hussain',
                        date: new Date().toISOString()
                    }
                ],
                'ai-assistant': [
                    {
                        id: '4',
                        title: 'AI Assistant Integration',
                        description: 'We are exploring AI tools to help with security analysis and automated testing. Share your findings here.',
                        author: 'Stefan Österberg',
                        date: new Date().toISOString()
                    }
                ],
                'practical-tasks': [
                    {
                        id: '5',
                        title: 'Current Task List',
                        description: '1. Implement chaos testing framework\n2. Document security protocols\n3. Create automated testing scripts\n4. Review system vulnerabilities',
                        author: 'Marcus Tibell',
                        date: new Date().toISOString()
                    }
                ],
                'implementation': [
                    {
                        id: '6',
                        title: 'Implementation Roadmap',
                        description: 'Phase 1: Environment setup\nPhase 2: Tool integration\nPhase 3: Testing framework\nPhase 4: Documentation',
                        author: 'Jens Annell',
                        date: new Date().toISOString()
                    }
                ],
                'research': [
                    {
                        id: '7',
                        title: 'Research Findings',
                        description: 'Initial research shows that chaos engineering principles can significantly improve security resilience when properly implemented.',
                        author: 'Luwam',
                        date: new Date().toISOString()
                    }
                ],
                'resources': [
                    {
                        id: '8',
                        title: 'Useful Resources',
                        description: '- Chaos Engineering Book\n- Security Testing Tools\n- Industry Best Practices\n- Academic Papers',
                        author: 'Kaled Osman',
                        date: new Date().toISOString()
                    }
                ],
                'security-testing': [
                    {
                        id: '9',
                        title: 'Security Testing Approach',
                        description: 'We will implement both automated and manual security testing, focusing on identifying vulnerabilities through controlled chaos experiments.',
                        author: 'Fahad Hussain',
                        date: new Date().toISOString()
                    }
                ],
                'monitoring-analytics': [
                    {
                        id: '10',
                        title: 'Monitoring Strategy',
                        description: 'Real-time monitoring of system behavior during chaos experiments will help us understand security weaknesses and improve resilience.',
                        author: 'Stefan Österberg',
                        date: new Date().toISOString()
                    }
                ]
            };

            Object.keys(defaultContent).forEach(section => {
                if (!userContents[section] || userContents[section].length === 0) {
                    userContents[section] = defaultContent[section];
                }
            });
            
            saveContents();
        }

        function saveContents() {
            localStorage.setItem('userContents', JSON.stringify(userContents));
        }

        // SHOW TEAM MEMBERS
        function showTeamMembers() {
            const liveTeamList = document.getElementById('liveTeamList');
            if (!liveTeamList) return;

            liveTeamList.innerHTML = '';
            
            teamMembers.forEach(member => {
                if (member.name !== 'You') {
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
                }
            });
        }

        // Chat Functions
        function openTeamChat() {
            document.getElementById('teamChatModal').classList.add('active');
            chatSystem.markAsRead();
        }

        function closeTeamChat() {
            document.getElementById('teamChatModal').classList.remove('active');
        }

        async function sendChatMessage() {
            const input = document.getElementById('chatInput');
            const message = input.value.trim();
            
            if (message) {
                const success = await chatSystem.saveMessage(message);
                if (success) {
                    input.value = '';
                } else {
                    alert('Failed to send message. Please try again.');
                }
            }
        }

        // Handle Enter key in chat
        document.getElementById('chatInput').addEventListener('keypress', function(e) {
            if (e.key === 'Enter') {
                sendChatMessage();
            }
        });

        // Add Content System
        document.getElementById('addContentForm').addEventListener('submit', function(e) {
            e.preventDefault();
            
            const section = document.getElementById('contentSection').value;
            const contentId = document.getElementById('editContentId').value;
            const title = document.getElementById('contentTitle').value;
            const description = document.getElementById('contentDescription').value;
            const author = document.getElementById('contentAuthor').value;
            
            if (!userContents[section]) {
                userContents[section] = [];
            }
            
            let content;
            if (contentId) {
                const index = userContents[section].findIndex(item => item.id === contentId);
                if (index !== -1) {
                    content = {
                        id: contentId,
                        title,
                        description,
                        author,
                        date: userContents[section][index].date,
                        updated: new Date().toISOString()
                    };
                    userContents[section][index] = content;
                }
            } else {
                content = {
                    id: Date.now().toString(),
                    title,
                    description,
                    author,
                    date: new Date().toISOString(),
                    updated: null
                };
                userContents[section].push(content);
            }
            
            saveContents();
            renderSectionContents(section);
            closeAddContent();
            alert('Content added successfully!');
        });

        function openAddContent(section, contentId = null) {
            document.getElementById('contentSection').value = section;
            document.getElementById('editContentId').value = contentId || '';
            
            if (contentId) {
                const content = userContents[section].find(item => item.id === contentId);
                if (content) {
                    document.getElementById('contentTitle').value = content.title;
                    document.getElementById('contentDescription').value = content.description;
                    document.getElementById('contentAuthor').value = content.author;
                }
            } else {
                document.getElementById('addContentForm').reset();
            }
            
            document.getElementById('addContentModal').classList.add('active');
        }

        function closeAddContent() {
            document.getElementById('addContentModal').classList.remove('active');
        }

        function renderSectionContents(section) {
            const container = document.getElementById(`${section}-content`);
            if (!container) return;
            
            container.innerHTML = '';
            
            if (!userContents[section] || userContents[section].length === 0) {
                container.innerHTML = `
                    <div class="text-center p-8 text-gray-500 dark:text-gray-400">
                        <i class="fas fa-inbox text-4xl mb-4"></i>
                        <p>No content added yet. Be the first to share something!</p>
                    </div>
                `;
                return;
            }
            
            userContents[section].forEach(content => {
                const contentElement = document.createElement('div');
                contentElement.className = 'p-6 border border-gray-200 dark:border-gray-700 rounded-2xl bg-white dark:bg-gray-800 mb-4';
                contentElement.innerHTML = `
                    <div class="flex justify-between items-start mb-4">
                        <h3 class="text-xl font-semibold text-gray-900 dark:text-white">${content.title}</h3>
                        <div class="flex space-x-2">
                            <button onclick="editContent('${section}', '${content.id}')" class="text-blue-600 hover:text-blue-800 dark:text-blue-400">
                                <i class="fas fa-edit"></i>
                            </button>
                            <button onclick="deleteContent('${section}', '${content.id}')" class="text-red-600 hover:text-red-800 dark:text-red-400">
                                <i class="fas fa-trash"></i>
                            </button>
                        </div>
                    </div>
                    <p class="text-gray-700 dark:text-gray-300 mb-4 whitespace-pre-line">${content.description}</p>
                    <div class="flex justify-between items-center text-sm text-gray-600 dark:text-gray-400">
                        <span><i class="fas fa-user mr-1"></i> ${content.author}</span>
                        <span><i class="fas fa-clock mr-1"></i> ${new Date(content.date).toLocaleDateString()}</span>
                    </div>
                `;
                container.appendChild(contentElement);
            });
        }

        function editContent(section, contentId) {
            openAddContent(section, contentId);
        }

        function deleteContent(section, contentId) {
            if (confirm('Are you sure you want to delete this content?')) {
                userContents[section] = userContents[section].filter(item => item.id !== contentId);
                saveContents();
                renderSectionContents(section);
                alert('Content deleted successfully!');
            }
        }

        function initializeAllContents() {
            const sections = [
                'overview', 'project-documentation', 'team-collaboration', 'team-updates',
                'ai-assistant', 'practical-tasks', 'implementation', 'research',
                'resources', 'security-testing', 'monitoring-analytics'
            ];
            sections.forEach(section => {
                renderSectionContents(section);
            });
        }

        // Section Navigation
        document.querySelectorAll('.section-btn').forEach(btn => {
            btn.addEventListener('click', function() {
                document.querySelectorAll('.section-btn').forEach(b => {
                    b.classList.remove('active-section');
                    b.classList.add('hover:bg-gray-100', 'dark:hover:bg-gray-700', 'text-gray-700', 'dark:text-gray-300');
                });
                
                this.classList.add('active-section');
                this.classList.remove('hover:bg-gray-100', 'dark:hover:bg-gray-700', 'text-gray-700', 'dark:text-gray-300');
                
                document.querySelectorAll('.section-content').forEach(section => {
                    section.classList.remove('active');
                });
                
                const sectionId = this.getAttribute('data-section');
                document.getElementById(sectionId).classList.add('active');
            });
        });

        // Login helper functions
        function showForgotPassword() {
            document.getElementById('loginModal').style.display = 'none';
            document.getElementById('forgotPasswordModal').style.display = 'flex';
        }

        function closeForgotPassword() {
            document.getElementById('forgotPasswordModal').style.display = 'none';
            document.getElementById('loginModal').style.display = 'flex';
        }

        function closeLogin() {
            document.getElementById('loginModal').style.display = 'none';
        }

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

        // Load saved theme and initialize everything
        document.addEventListener('DOMContentLoaded', function() {
            const savedTheme = localStorage.getItem('theme');
            if (savedTheme === 'dark') {
                document.documentElement.classList.add('dark');
            }

            // Initialize all systems
            initializeDefaultContent();
            initializeAllContents();
            showTeamMembers();
            
            // Initialize admin system if user is already logged in as admin
            if (loginSystem.currentUser && loginSystem.currentUser.email === 'kaled@team.com') {
                adminSystem.init();
            }
        });
    </script>
</body>
</html>
