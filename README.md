<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0"/>
    <title>Security Chaos Engineering Dashboard</title>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <script src="https://cdn.tailwindcss.com"></script>

    <!-- Firebase SDK (compat) -->
    <script src="https://www.gstatic.com/firebasejs/9.22.1/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.1/firebase-database-compat.js"></script>
    <!-- Storage compat -->
    <script src="https://www.gstatic.com/firebasejs/9.22.1/firebase-storage-compat.js"></script>

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
        .pdf-thumb {
            border-radius: 8px;
            padding: 8px;
            background: #f8fafc;
        }
    </style>
    <style>
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

    <!-- Upload PDF Modal -->
    <div class="modal" id="uploadPdfModal">
        <div class="bg-white dark:bg-gray-800 p-6 rounded-2xl max-w-2xl w-full mx-4 shadow-2xl">
            <div class="flex justify-between items-center mb-4">
                <h2 class="text-2xl font-bold text-gray-800 dark:text-white">Upload PDF</h2>
                <button onclick="closePdfUpload()" class="text-gray-500 hover:text-gray-700"><i class="fas fa-times"></i></button>
            </div>

            <form id="uploadPdfForm" class="space-y-4">
                <div>
                    <label class="block text-sm font-medium mb-2 text-gray-700 dark:text-gray-300">Title</label>
                    <input id="pdfTitle" type="text" class="w-full p-3 border border-gray-300 rounded-xl dark:border-gray-600 dark:bg-gray-700 dark:text-white" required>
                </div>

                <div>
                    <label class="block text-sm font-medium mb-2 text-gray-700 dark:text-gray-300">Description</label>
                    <textarea id="pdfDescription" rows="4" class="w-full p-3 border border-gray-300 rounded-xl dark:border-gray-600 dark:bg-gray-700 dark:text-white"></textarea>
                </div>

                <div>
                    <label class="block text-sm font-medium mb-2 text-gray-700 dark:text-gray-300">Choose PDF</label>
                    <input id="pdfFile" type="file" accept="application/pdf" class="block w-full text-sm text-gray-700" required />
                </div>

                <div class="grid grid-cols-2 gap-3">
                    <button id="startUploadBtn" type="button" class="bg-blue-600 hover:bg-blue-700 text-white py-3 rounded-xl">Upload</button>
                    <button type="button" onclick="closePdfUpload()" class="bg-gray-500 hover:bg-gray-600 text-white py-3 rounded-xl">Cancel</button>
                </div>

                <div id="uploadStatus" class="text-sm text-gray-600 mt-2 hidden">
                    <div id="progressLabel">Uploading... 0%</div>
                    <div class="w-full bg-gray-200 rounded-full h-2 mt-2 overflow-hidden">
                        <div id="progressBar" style="width:0%;" class="h-2 bg-blue-600"></div>
                    </div>
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

            <!-- (other sections same as before — each contains a container with id "<section>-content") -->
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

            <!-- Practical Tasks: added Upload PDF button -->
            <div class="section-content" id="practical-tasks">
                <div class="bg-white dark:bg-gray-800 p-8 rounded-2xl shadow-lg">
                    <div class="flex justify-between items-center mb-6">
                        <h2 class="text-2xl font-bold text-gray-800 dark:text-white">Practical Tasks</h2>
                        <div class="flex gap-3">
                            <button onclick="openAddContent('practical-tasks')" class="bg-green-600 hover:bg-green-700 text-white px-4 py-2 rounded-xl transition-all">
                                <i class="fas fa-plus mr-2"></i>Add Content
                            </button>
                            <!-- Upload PDF button -->
                            <button onclick="openPdfUpload()" class="bg-indigo-600 hover:bg-indigo-700 text-white px-4 py-2 rounded-xl transition-all">
                                <i class="fas fa-file-pdf mr-2"></i>Upload PDF
                            </button>
                        </div>
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

    <!-- ---------- SCRIPT: Firebase + Real-time logic + Storage ---------- -->
    <script>
    (function(){
        // ---------- Configuration ----------
        const DATABASE_URL = 'https://fir-console-df3e9-default-rtdb.europe-west1.firebasedatabase.app';
        const firebaseConfig = {
            apiKey: "AIzaSyDJsZ4LZVrBucavpTdhXbKxyE_BFeZFFKs",
            authDomain: "fir-console-df3e9.firebaseapp.com",
            databaseURL: DATABASE_URL,
            projectId: "fir-console-df3e9",
            storageBucket: "fir-console-df3e9.appspot.com", // ensure storage bucket set
            messagingSenderId: "750795336412",
            appId: "1:750795336412:web:abfd0c06941a9418abe219"
        };

        // Initialize Firebase app (safe to call multiple times)
        try {
            if (!firebase.apps.length) firebase.initializeApp(firebaseConfig);
        } catch (e) {
            console.warn('Firebase init warning:', e);
        }
        const db = firebase.database();
        const storage = firebase.storage(); // compat storage

        // ---------- Helpers ----------
        const sections = [
            'overview','project-documentation','team-collaboration','team-updates',
            'ai-assistant','practical-tasks','implementation','research',
            'resources','security-testing','monitoring-analytics'
        ];
        const $ = id => document.getElementById(id);

        // Basic escape for text rendering
        function escapeHtml(s = '') {
            return String(s)
                .replaceAll('&','&amp;').replaceAll('<','&lt;').replaceAll('>','&gt;')
                .replaceAll('"','&quot;').replaceAll("'",'&#039;');
        }

        // ---------- Chat system ----------
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
                    console.error('Failed writing chat to Firebase', e);
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
                this.lastRead = Date.now();
                this.unread = 0;
                this.updateUnread();
            },
            close() {
                $('teamChatModal').classList.remove('active');
            },
            isOpen() {
                return $('teamChatModal').classList.contains('active');
            },
            updateUnread() {
                const u = $('unreadCount');
                if (!u) return;
                if (this.unread > 0) {
                    u.textContent = this.unread;
                    u.classList.remove('hidden');
                } else {
                    u.classList.add('hidden');
                }
            }
        };

        // ---------- Content system (Realtime) ----------
        const contentSystem = {
            // Render one section's snapshot
            renderSection(section, snapshot) {
                const container = $(`${section}-content`);
                if (!container) return;
                container.innerHTML = '';
                if (!snapshot || !snapshot.exists()) {
                    container.innerHTML = `
                        <div class="text-center p-8 text-gray-500 dark:text-gray-400">
                            <i class="fas fa-inbox text-4xl mb-4"></i>
                            <p>No content added yet. Be the first to share something!</p>
                        </div>`;
                    return;
                }
                // Build ordered list — sort by date desc
                const items = [];
                snapshot.forEach(child => {
                    const item = child.val();
                    item._key = child.key;
                    items.push(item);
                });
                items.sort((a,b) => new Date(b.date) - new Date(a.date));

                items.forEach(item => {
                    const id = item._key;
                    const el = document.createElement('div');
                    el.className = 'p-6 border border-gray-200 dark:border-gray-700 rounded-2xl bg-white dark:bg-gray-800 mb-4';
                    // build inner HTML; if item has pdfUrl, show PDF card with open link
                    let bodyHTML = '';
                    if (item.pdfUrl) {
                        bodyHTML = `
                            <div class="flex flex-col md:flex-row md:items-center gap-4">
                                <div class="pdf-thumb flex items-center justify-center w-full md:w-48">
                                    <div class="flex items-center gap-3">
                                        <i class="fas fa-file-pdf fa-2x text-red-600"></i>
                                        <div class="text-sm">
                                            <div class="font-medium">${escapeHtml(item.pdfName || item.title || 'PDF')}</div>
                                            <div class="text-xs text-gray-500">${new Date(item.date).toLocaleString()}</div>
                                        </div>
                                    </div>
                                </div>
                                <div class="flex-1">
                                    <p class="text-gray-700 dark:text-gray-300 mb-4 whitespace-pre-line">${escapeHtml(item.description || '')}</p>
                                    <div class="flex gap-2 items-center">
                                        <a href="${item.pdfUrl}" target="_blank" rel="noopener" class="bg-blue-600 hover:bg-blue-700 text-white px-4 py-2 rounded-lg">Open PDF</a>
                                        <a href="${item.pdfUrl}" download class="bg-gray-200 hover:bg-gray-300 text-gray-800 px-4 py-2 rounded-lg">Download</a>
                                        ${isCurrentUserAdmin() ? `<button data-id="${id}" data-section="${section}" class="del-pdf-btn bg-red-600 hover:bg-red-700 text-white px-3 py-2 rounded-lg">Delete</button>` : ''}
                                        <span class="text-sm text-gray-500 ml-auto"><i class="fas fa-user mr-1"></i>${escapeHtml(item.author || 'Unknown')}</span>
                                    </div>
                                </div>
                            </div>
                        `;
                    } else {
                        bodyHTML = `<p class="text-gray-700 dark:text-gray-300 mb-4 whitespace-pre-line">${escapeHtml(item.description)}</p>`;
                    }

                    el.innerHTML = `
                        <div class="flex justify-between items-start mb-4">
                            <h3 class="text-xl font-semibold text-gray-900 dark:text-white">${escapeHtml(item.title)}</h3>
                            <div class="flex space-x-2">
                                <button data-id="${id}" data-section="${section}" class="edit-btn text-blue-600 hover:text-blue-800 dark:text-blue-400"><i class="fas fa-edit"></i></button>
                                <button data-id="${id}" data-section="${section}" class="del-btn text-red-600 hover:text-red-800 dark:text-red-400"><i class="fas fa-trash"></i></button>
                            </div>
                        </div>
                        ${bodyHTML}
                        <div class="flex justify-between items-center text-sm text-gray-600 dark:text-gray-400 mt-4">
                            <span><i class="fas fa-clock mr-1"></i> ${new Date(item.date).toLocaleDateString()}</span>
                        </div>
                    `;
                    container.appendChild(el);
                });

                // attach handlers (edit/delete)
                container.querySelectorAll('.edit-btn').forEach(b => {
                    b.addEventListener('click', (e) => {
                        const id = b.getAttribute('data-id');
                        const sec = b.getAttribute('data-section');
                        openEditModalFor(sec, id);
                    });
                });
                container.querySelectorAll('.del-btn').forEach(b => {
                    b.addEventListener('click', (e) => {
                        const id = b.getAttribute('data-id');
                        const sec = b.getAttribute('data-section');
                        deleteContent(sec, id);
                    });
                });
                // delete pdf buttons
                container.querySelectorAll('.del-pdf-btn').forEach(b => {
                    b.addEventListener('click', (e) => {
                        const id = b.getAttribute('data-id');
                        const sec = b.getAttribute('data-section');
                        deletePdfEntry(sec, id);
                    });
                });
            },
            listenAll() {
                sections.forEach(sec => {
                    const ref = db.ref(`content/${sec}`);
                    ref.off();
                    ref.on('value', snap => this.renderSection(sec, snap));
                });
            },
            pushContent(section, payload) {
                return db.ref(`content/${section}`).push(payload);
            },
            updateContent(section, id, payload) {
                return db.ref(`content/${section}/${id}`).update(payload);
            },
            removeContent(section, id) {
                return db.ref(`content/${section}/${id}`).remove();
            }
        };

        // ---------- UI: open add / edit ----------
        function openAddContent(section) {
            $('contentSection').value = section;
            $('editContentId').value = '';
            $('contentTitle').value = '';
            $('contentDescription').value = '';
            const saved = JSON.parse(localStorage.getItem('currentUser'));
            if (saved && saved.name) $('contentAuthor').value = saved.name;
            $('addContentModal').classList.add('active');
        }
        function closeAddContent() { $('addContentModal').classList.remove('active'); }

        // open edit modal and prefill with Firebase data
        function openEditModalFor(section, id) {
            db.ref(`content/${section}/${id}`).once('value').then(snap => {
                const item = snap.val();
                if (!item) return alert('Item not found');
                $('contentSection').value = section;
                $('editContentId').value = id;
                $('contentTitle').value = item.title || '';
                $('contentDescription').value = item.description || '';
                $('contentAuthor').value = item.author || (JSON.parse(localStorage.getItem('currentUser')) || {}).name || 'Unknown';
                $('addContentModal').classList.add('active');
            }).catch(err => {
                console.error(err);
                alert('Failed to load item for edit');
            });
        }

        // Add / Edit submit
        document.getElementById('addContentForm').addEventListener('submit', function(e){
            e.preventDefault();
            const section = $('contentSection').value;
            const editId = $('editContentId').value;
            const title = $('contentTitle').value.trim();
            const description = $('contentDescription').value.trim();
            const author = $('contentAuthor').value;
            if (!title || !description) return alert('Fill title and content');

            const payload = { title, description, author, date: new Date().toISOString() };
            if (editId) {
                contentSystem.updateContent(section, editId, payload)
                .then(()=> { closeAddContent(); }).catch(err=> { console.error(err); alert('Update failed'); });
            } else {
                contentSystem.pushContent(section, payload)
                .then(()=> { closeAddContent(); }).catch(err=> { console.error(err); alert('Save failed'); });
            }
        });

        // delete content
        function deleteContent(section, id) {
            if (!confirm('Are you sure you want to delete this content?')) return;
            contentSystem.removeContent(section, id).catch(err => { console.error(err); alert('Delete failed'); });
        }

        // ---------- Admin utilities ----------
        function clearAllContentAdmin() {
            if (!confirm('Delete all content for all sections?')) return;
            const updates = {};
            sections.forEach(sec => updates[`content/${sec}`] = null);
            db.ref().update(updates).then(()=> { alert('All content cleared'); }).catch(err => { console.error(err); alert('Failed to clear content'); });
        }
        function clearChatAdmin() {
            if (!confirm('Clear all chat messages?')) return;
            db.ref('team-chat').remove().then(()=> {
                const list = $('chatMessages');
                if (list) list.innerHTML = '<div class="text-center text-gray-500 text-sm py-4"><i class="fas fa-comments text-xl mb-2 block"></i> Chat cleared</div>';
            }).catch(err => { console.error(err); alert('Failed to clear chat'); });
        }

        // ---------- Login system (keeps your demo local logic) ----------
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
                if (this.currentUser) {
                    this.updateUIForLoggedIn();
                    adminSystem.tryInit();
                }
                document.getElementById('loginForm').addEventListener('submit', (e) => {
                    e.preventDefault(); this.attemptLogin();
                });
            },
            attemptLogin() {
                const email = $('loginEmail').value.trim();
                const password = $('loginPassword').value.trim();
                const found = this.users.find(u => u.email === email && u.password === password);
                if (!found) { alert('Invalid credentials'); return; }
                this.currentUser = found;
                localStorage.setItem('currentUser', JSON.stringify(found));
                this.updateUIForLoggedIn();
                chat.currentUser = found.name;
                $('loginModal').style.display = 'none';
                adminSystem.tryInit();
            },
            logout() {
                this.currentUser = null;
                localStorage.removeItem('currentUser');
                $('loginModal').style.display = 'flex';
                $('userName').textContent = 'You';
                $('userRole').textContent = 'Active User';
                $('userAvatar').textContent = 'Y';
                chat.currentUser = 'You';
            },
            updateUIForLoggedIn() {
                const u = this.currentUser;
                if (!u) return;
                $('userName').textContent = u.name;
                $('userRole').textContent = u.role;
                $('userAvatar').textContent = u.avatar || u.name.charAt(0);
                adminSystem.tryInit();
            }
        };

        // Helper to check admin (kaled)
        function isCurrentUserAdmin() {
            const cur = JSON.parse(localStorage.getItem('currentUser')) || null;
            return cur && cur.email === 'kaled@team.com';
        }

        // ---------- Admin system ----------
        const adminSystem = {
            tryInit() {
                const cur = JSON.parse(localStorage.getItem('currentUser')) || null;
                if (cur && cur.email === 'kaled@team.com') {
                    this.renderAdmin();
                } else {
                    const panel = $('adminPanel');
                    if (panel) panel.innerHTML = '';
                }
            },
            renderAdmin() {
                const panel = $('adminPanel');
                if (!panel) return;
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

        // ---------- Team members UI ----------
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
            const liveTeamList = $('liveTeamList');
            if (!liveTeamList) return;
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

        // ---------- PDF Upload Logic ----------
        function openPdfUpload() {
            // default author
            const saved = JSON.parse(localStorage.getItem('currentUser'));
            const author = saved && saved.name ? saved.name : 'Unknown';
            $('pdfTitle').value = '';
            $('pdfDescription').value = '';
            $('pdfFile').value = '';
            $('uploadStatus').classList.add('hidden');
            $('uploadPdfModal').classList.add('active');
        }
        function closePdfUpload() { $('uploadPdfModal').classList.remove('active'); }

        // start upload
        document.getElementById('startUploadBtn').addEventListener('click', async () => {
            const fileInput = $('pdfFile');
            const title = $('pdfTitle').value.trim();
            const description = $('pdfDescription').value.trim();
            if (!title) { alert('Please enter a title'); return; }
            if (!fileInput.files || fileInput.files.length === 0) { alert('Please select a PDF file'); return; }
            const file = fileInput.files[0];
            if (file.type !== 'application/pdf') { alert('Only PDFs are allowed'); return; }

            // prepare metadata
            const timestamp = Date.now();
            const fileNameSafe = `${timestamp}_${file.name.replace(/\s+/g,'_')}`;
            const storageRef = storage.ref().child(`pdfs/${fileNameSafe}`);

            // UI
            $('uploadStatus').classList.remove('hidden');
            const progressBar = $('progressBar');
            const progressLabel = $('progressLabel');
            progressBar.style.width = '0%';
            progressLabel.textContent = 'Uploading... 0%';

            try {
                const uploadTask = storageRef.put(file);
                uploadTask.on('state_changed',
                    (snapshot) => {
                        const pct = Math.round((snapshot.bytesTransferred / snapshot.totalBytes) * 100);
                        progressBar.style.width = pct + '%';
                        progressLabel.textContent = `Uploading... ${pct}%`;
                    },
                    (err) => {
                        console.error('Upload failed', err);
                        alert('Upload failed: ' + err.message);
                    },
                    async () => {
                        const url = await storageRef.getDownloadURL();
                        // store metadata in realtime DB under content/practical-tasks
                        const author = (JSON.parse(localStorage.getItem('currentUser')) || {}).name || 'Unknown';
                        const payload = {
                            title,
                            description: description || '',
                            author,
                            date: new Date().toISOString(),
                            pdfUrl: url,
                            pdfName: file.name,
                            storagePath: `pdfs/${fileNameSafe}`
                        };
                        await contentSystem.pushContent('practical-tasks', payload);
                        progressBar.style.width = '100%';
                        progressLabel.textContent = 'Upload complete';
                        setTimeout(() => {
                            closePdfUpload();
                        }, 700);
                    }
                );
            } catch (e) {
                console.error(e);
                alert('Upload error: ' + e.message);
            }
        });

        // Delete PDF entry (DB + Storage) - admin only
        async function deletePdfEntry(section, id) {
            if (!isCurrentUserAdmin()) { alert('Only admin can delete PDFs'); return; }
            if (!confirm('Delete PDF and storage file?')) return;
            try {
                const snap = await db.ref(`content/${section}/${id}`).once('value');
                const item = snap.val();
                if (item && item.storagePath) {
                    await storage.ref().child(item.storagePath).delete();
                }
                await db.ref(`content/${section}/${id}`).remove();
                alert('PDF removed');
            } catch (e) {
                console.error(e);
                alert('Failed to delete PDF: ' + e.message);
            }
        }

        // ---------- Initialize default content in Firebase if empty (only once) ----------
        function seedDefaultContentIfEmpty() {
            const ref = db.ref('content/overview');
            ref.once('value').then(snap => {
                if (snap.exists()) return; // already seeded
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

        // ---------- initialize listeners and UI ----------
        function initAll() {
            loginSystem.init();
            chat.init();
            contentSystem.listenAll();
            showTeamMembers();
            seedDefaultContentIfEmpty();

            document.querySelectorAll('.section-btn').forEach(btn => {
                btn.addEventListener('click', function() {
                    document.querySelectorAll('.section-btn').forEach(b => b.classList.remove('active-section'));
                    this.classList.add('active-section');
                    document.querySelectorAll('.section-content').forEach(s => s.classList.remove('active'));
                    const id = this.getAttribute('data-section');
                    const el = document.getElementById(id);
                    if (el) el.classList.add('active');
                });
            });

            const chatInput = $('chatInput');
            if (chatInput) chatInput.addEventListener('keypress', (e) => { if (e.key === 'Enter') chat.send(); });
            const sendBtn = $('sendChatButton'); if (sendBtn) sendBtn.addEventListener('click', () => chat.send());
            const openChatBtns = document.querySelectorAll('[onclick="openTeamChat()"]'); openChatBtns.forEach(b => b.addEventListener('click', () => chat.open()));
        }

        document.addEventListener('DOMContentLoaded', () => {
            initAll();
        });

        // expose functions globally for onclick attributes
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
        window.openPdfUpload = openPdfUpload;
        window.closePdfUpload = closePdfUpload;
    })();
    </script>
</body>
</html>
