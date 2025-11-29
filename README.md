<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Security Chaos Engineering Dashboard</title>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <script src="https://cdn.tailwindcss.com"></script>
    <style>
        :root {
            --primary: #2563eb;
            --primary-dark: #1d4ed8;
            --secondary: #1e40af;
            --bg-light: #ffffff;
            --bg-dark: #0f172a;
            --text-light: #1e293b;
            --text-dark: #f8fafc;
            --sidebar-light: #f8fafc;
            --sidebar-dark: #1e293b;
            --card-light: #ffffff;
            --card-dark: #334155;
        }

        .theme-transition {
            transition: all 0.3s ease;
        }

        [data-theme="light"] {
            --bg-color: var(--bg-light);
            --text-color: var(--text-light);
            --sidebar-bg: var(--sidebar-light);
            --card-bg: var(--card-light);
        }

        [data-theme="dark"] {
            --bg-color: var(--bg-dark);
            --text-color: var(--text-dark);
            --sidebar-bg: var(--sidebar-dark);
            --card-bg: var(--card-dark);
        }

        body {
            background-color: var(--bg-color);
            color: var(--text-color);
            font-family: 'Segoe UI', system-ui, sans-serif;
        }

        .sidebar {
            background-color: var(--sidebar-bg);
        }

        .card {
            background-color: var(--card-bg);
            border: 1px solid;
            border-color: var(--border-color);
        }

        .active-section {
            background: linear-gradient(135deg, #3b82f6, #1d4ed8);
            color: white;
            box-shadow: 0 4px 6px rgba(37, 99, 235, 0.2);
        }

        .gradient-header {
            background: linear-gradient(135deg, #2563eb, #1e40af);
        }

        .add-content-modal, .team-chat-modal {
            display: none;
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background: rgba(0, 0, 0, 0.5);
            z-index: 1000;
        }

        .add-content-modal.active, .team-chat-modal.active {
            display: flex;
            align-items: center;
            justify-content: center;
        }

        .user-content-item {
            transition: all 0.3s ease;
        }

        .user-content-item:hover {
            transform: translateY(-2px);
            box-shadow: 0 4px 12px rgba(0, 0, 0, 0.1);
        }

        [data-theme="dark"] .text-gray-800 {
            color: #f8fafc !important;
        }
        
        [data-theme="dark"] .text-gray-600 {
            color: #cbd5e1 !important;
        }
        
        [data-theme="dark"] .text-gray-700 {
            color: #e2e8f0 !important;
        }
        
        [data-theme="dark"] .border-gray-200 {
            border-color: #475569 !important;
        }
        
        [data-theme="dark"] .bg-white {
            background-color: #334155 !important;
        }
        
        [data-theme="dark"] .bg-gray-100 {
            background-color: #1e293b !important;
        }
        
        [data-theme="dark"] .bg-gray-50 {
            background-color: #1e293b !important;
        }

        .online-indicator {
            width: 10px;
            height: 10px;
            background: #10B981;
            border-radius: 50%;
            display: inline-block;
            margin-right: 8px;
            animation: pulse 2s infinite;
        }

        @keyframes pulse {
            0% { transform: scale(1); opacity: 1; }
            50% { transform: scale(1.2); opacity: 0.7; }
            100% { transform: scale(1); opacity: 1; }
        }

        .realtime-badge {
            background: linear-gradient(135deg, #10B981, #059669);
            color: white;
            padding: 4px 12px;
            border-radius: 20px;
            font-size: 0.75rem;
            font-weight: 600;
            display: inline-flex;
            align-items: center;
            gap: 6px;
        }

        .live-user-badge {
            display: inline-flex;
            align-items: center;
            background: #DCFCE7;
            color: #166534;
            padding: 6px 12px;
            border-radius: 20px;
            margin: 4px;
            font-size: 0.875rem;
            font-weight: 500;
        }
    </style>
</head>
<body class="theme-transition" data-theme="light">
    <!-- Add Content Modal -->
    <div class="add-content-modal" id="addContentModal">
        <div class="bg-white dark:bg-gray-800 p-8 rounded-2xl max-w-2xl w-full mx-4 shadow-2xl">
            <h2 class="text-2xl font-bold mb-6 text-center text-gray-800 dark:text-white" id="modalTitle">Add New Content</h2>
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
                    <button type="submit" class="flex-1 bg-green-600 hover:bg-green-700 text-white py-3 rounded-xl transition-all font-medium">
                        <span id="submitButtonText">Add Content</span>
                    </button>
                    <button type="button" onclick="closeAddContent()" class="flex-1 bg-gray-500 hover:bg-gray-600 text-white py-3 rounded-xl transition-all font-medium">
                        Cancel
                    </button>
                </div>
            </form>
        </div>
    </div>

    <!-- SMART Team Chat Modal -->
    <div class="team-chat-modal" id="teamChatModal">
        <div class="bg-white dark:bg-gray-800 p-4 rounded-xl mx-4 shadow-2xl" style="width: 500px; max-width: 90vw;">
            <div class="flex justify-between items-center mb-3">
                <h2 class="text-xl font-bold text-gray-800 dark:text-white flex items-center gap-2">
                    <span class="realtime-badge">
                        <span class="online-indicator"></span>
                        SMART CHAT
                    </span>
                </h2>
                <div class="flex gap-2">
                    <button onclick="showDeletedMessages()" class="text-blue-600 hover:text-blue-800 text-sm">
                        <i class="fas fa-trash-restore"></i> Ångra
                    </button>
                    <button onclick="closeTeamChat()" class="text-gray-500 hover:text-gray-700">
                        <i class="fas fa-times"></i>
                    </button>
                </div>
            </div>

            <!-- Chat Messages -->
            <div id="chatMessages" class="overflow-y-auto mb-3 p-3 border border-gray-300 rounded-lg bg-gray-50 dark:bg-gray-900" style="height: 300px;">
                <div class="text-center text-gray-500 text-sm py-4">
                    <i class="fas fa-comments text-xl mb-2 block"></i>
                    Start chatting with your team!
                </div>
            </div>

            <!-- Chat Input -->
            <div class="flex gap-2">
                <input type="text" id="chatInput" placeholder="Type message..." 
                       class="flex-1 p-2 border border-gray-300 rounded-lg dark:bg-gray-800 dark:text-white"
                       onkeypress="if(event.key === 'Enter') sendChatMessage()">
                <button onclick="sendChatMessage()" class="bg-blue-600 hover:bg-blue-700 text-white px-4 py-2 rounded-lg">
                    <i class="fas fa-paper-plane"></i>
                </button>
            </div>
        </div>
    </div>

    <!-- Main Layout -->
    <div class="flex min-h-screen">
        <!-- Sidebar -->
        <div class="sidebar w-80 flex-shrink-0 theme-transition p-6">
            <div class="mb-8">
                <h1 class="text-2xl font-bold text-blue-600 dark:text-blue-400">Security Chaos Engineering</h1>
                <p class="text-gray-600 dark:text-gray-400 mt-2">Group 1 Dashboard</p>
                
                <div id="realtimeStatus" class="mt-3 p-3 bg-gradient-to-r from-green-500 to-emerald-600 text-white rounded-lg shadow-lg">
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
            
            <!-- User Info Section -->
            <div class="w-full mb-6">
                <div class="bg-white dark:bg-gray-800 p-4 rounded-xl shadow-lg border-2 border-blue-200 dark:border-blue-800">
                    <div class="flex items-center space-x-3 mb-3">
                        <div class="relative">
                            <div class="w-10 h-10 bg-gradient-to-r from-blue-500 to-purple-500 rounded-full flex items-center justify-center text-white font-bold">
                                T
                            </div>
                            <span class="online-indicator absolute -top-1 -right-1 border-2 border-white dark:border-gray-800"></span>
                        </div>
                        <div class="flex-1 min-w-0">
                            <p class="font-semibold text-gray-800 dark:text-white truncate">Team Member</p>
                            <p class="text-sm text-gray-600 dark:text-gray-400 truncate">Active User</p>
                        </div>
                    </div>
                    <div class="space-y-2">
                        <button onclick="openTeamChat()" class="w-full bg-gradient-to-r from-green-600 to-blue-600 hover:from-green-700 hover:to-blue-700 text-white py-3 rounded-lg transition-all shadow-lg font-medium">
                            <i class="fas fa-comments mr-2"></i>Team Chat
                        </button>
                    </div>
                </div>
            </div>

            <!-- Live Team Activity -->
            <div id="liveTeamSection" class="mb-6">
                <div class="bg-gradient-to-br from-blue-50 to-green-50 dark:from-gray-800 dark:to-gray-700 p-4 rounded-xl shadow-lg border-2 border-green-200 dark:border-green-800">
                    <h3 class="font-semibold mb-3 text-gray-800 dark:text-white flex items-center gap-2">
                        <span class="online-indicator"></span>
                        Live Team Activity
                    </h3>
                    <div id="liveTeamList" class="space-y-2 max-h-40 overflow-y-auto">
                        <!-- Online team members will appear here -->
                    </div>
                    <div id="realtimeActivities" class="mt-3 space-y-2">
                        <!-- Real-time activities will appear here -->
                    </div>
                </div>
            </div>

            <!-- Navigation - ALL 9 SECTIONS -->
            <nav class="space-y-2 mb-8">
                <button class="section-btn w-full text-left p-4 rounded-xl theme-transition active-section" data-section="overview">
                    <i class="fas fa-home mr-3 w-6 text-center"></i>Overview
                </button>
                <button class="section-btn w-full text-left p-4 rounded-xl theme-transition hover:bg-gray-100 dark:hover:bg-gray-700 text-gray-700 dark:text-gray-300" data-section="project-documentation">
                    <i class="fas fa-folder mr-3 w-6 text-center"></i>Project Documentation
                </button>
                <button class="section-btn w-full text-left p-4 rounded-xl theme-transition hover:bg-gray-100 dark:hover:bg-gray-700 text-gray-700 dark:text-gray-300" data-section="team-collaboration">
                    <i class="fas fa-users mr-3 w-6 text-center"></i>Team Collaboration
                </button>
                <button class="section-btn w-full text-left p-4 rounded-xl theme-transition hover:bg-gray-100 dark:hover:bg-gray-700 text-gray-700 dark:text-gray-300" data-section="team-updates">
                    <i class="fas fa-bullhorn mr-3 w-6 text-center"></i>Team Updates
                </button>
                <button class="section-btn w-full text-left p-4 rounded-xl theme-transition hover:bg-gray-100 dark:hover:bg-gray-700 text-gray-700 dark:text-gray-300" data-section="ai-assistant">
                    <i class="fas fa-robot mr-3 w-6 text-center"></i>AI Assistant
                </button>
                <button class="section-btn w-full text-left p-4 rounded-xl theme-transition hover:bg-gray-100 dark:hover:bg-gray-700 text-gray-700 dark:text-gray-300" data-section="practical-tasks">
                    <i class="fas fa-tasks mr-3 w-6 text-center"></i>Practical Tasks
                </button>
                <button class="section-btn w-full text-left p-4 rounded-xl theme-transition hover:bg-gray-100 dark:hover:bg-gray-700 text-gray-700 dark:text-gray-300" data-section="implementation">
                    <i class="fas fa-code mr-3 w-6 text-center"></i>Implementation
                </button>
                <button class="section-btn w-full text-left p-4 rounded-xl theme-transition hover:bg-gray-100 dark:hover:bg-gray-700 text-gray-700 dark:text-gray-300" data-section="research">
                    <i class="fas fa-search mr-3 w-6 text-center"></i>Research
                </button>
                <button class="section-btn w-full text-left p-4 rounded-xl theme-transition hover:bg-gray-100 dark:hover:bg-gray-700 text-gray-700 dark:text-gray-300" data-section="resources">
                    <i class="fas fa-book mr-3 w-6 text-center"></i>Resources
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
        <div class="flex-1 bg-gradient-to-br from-blue-50 to-white dark:from-gray-900 dark:to-gray-800">
            <header class="gradient-header text-white p-6 shadow-lg">
                <div class="flex justify-between items-center">
                    <div>
                        <h1 class="text-3xl font-bold">Security Chaos Engineering Dashboard</h1>
                        <p class="mt-2 opacity-90">Group 1 - Full-Stack Dashboard with Real-time Data</p>
                    </div>
                    <div class="flex items-center space-x-4">
                        <div class="text-sm opacity-90" id="loginStatus">
                            Welcome to the Team Dashboard
                        </div>
                        <div id="liveActivity">
                            <div class="flex items-center space-x-2 bg-white/30 px-4 py-2 rounded-full backdrop-blur-sm border-2 border-green-300">
                                <span class="online-indicator"></span>
                                <span class="text-sm font-semibold">LIVE MODE ACTIVE</span>
                            </div>
                        </div>
                        <div class="hidden md:flex space-x-3">
                            <div class="text-center p-3 bg-white/20 rounded-xl backdrop-blur-sm">
                                <div class="text-lg font-bold">12%</div>
                                <div class="text-xs opacity-90">CPU</div>
                            </div>
                            <div class="text-center p-3 bg-white/20 rounded-xl backdrop-blur-sm">
                                <div class="text-lg font-bold">45%</div>
                                <div class="text-xs opacity-90">Memory</div>
                            </div>
                        </div>
                        <button onclick="fetchSystemData()" class="bg-white/20 hover:bg-white/30 text-white px-4 py-3 rounded-xl transition-all backdrop-blur-sm">
                            <i class="fas fa-sync-alt mr-2"></i>Refresh Data
                        </button>
                    </div>
                </div>
            </header>

            <!-- Content Sections -->
            <div class="p-8">
                <!-- Overview Section -->
                <div class="section-content card p-8 mb-8 rounded-2xl shadow-lg" id="overview">
                    <div class="flex justify-between items-center mb-6">
                        <h2 class="text-3xl font-bold text-gray-800 dark:text-white">Project Overview</h2>
                        <button onclick="openAddContent('overview')" class="bg-green-600 hover:bg-green-700 text-white px-4 py-2 rounded-xl transition-all">
                            <i class="fas fa-plus mr-2"></i>Add Content
                        </button>
                    </div>
                    
                    <!-- Live Activity Feed -->
                    <div id="liveActivityFeed" class="mb-6 p-6 bg-gradient-to-r from-blue-50 to-green-50 dark:from-blue-900/20 dark:to-green-900/20 rounded-2xl border-2 border-blue-200 dark:border-blue-800 shadow-lg">
                        <h3 class="font-semibold mb-4 text-gray-800 dark:text-white text-xl flex items-center gap-2">
                            <span class="realtime-badge">
                                <span class="online-indicator"></span>
                                LIVE
                            </span>
                            Team Activity Feed
                        </h3>
                        <div id="activityList" class="space-y-3">
                            <div class="text-center text-gray-500 dark:text-gray-400 py-4">
                                <i class="fas fa-bolt text-2xl mb-2"></i>
                                <div>Team activities will appear here in real-time</div>
                            </div>
                        </div>
                    </div>
                    
                    <div id="overview-content" class="space-y-4 mb-8"></div>
                                    <!-- Project Documentation Section -->
                <div class="section-content card p-8 mb-8 rounded-2xl shadow-lg hidden" id="project-documentation">
                    <div class="flex justify-between items-center mb-6">
                        <h2 class="text-3xl font-bold text-gray-800 dark:text-white">Project Documentation</h2>
                        <button onclick="openAddContent('project-documentation')" class="bg-green-600 hover:bg-green-700 text-white px-4 py-2 rounded-xl transition-all">
                            <i class="fas fa-plus mr-2"></i>Add Content
                        </button>
                    </div>
                    <div id="project-documentation-content" class="space-y-4 mb-8">
                        <div class="user-content-item p-6 border border-gray-200 dark:border-gray-700 rounded-2xl bg-white dark:bg-gray-800">
                            <h3 class="text-xl font-semibold text-gray-900 dark:text-white mb-4">Project Requirements</h3>
                            <p class="text-gray-700 dark:text-gray-300 mb-4">Document all project requirements and specifications for the Security Chaos Engineering dashboard.</p>
                            <div class="flex justify-between items-center text-sm text-gray-600 dark:text-gray-400">
                                <span><i class="fas fa-user mr-1"></i> Kaled Osman</span>
                                <span><i class="fas fa-clock mr-1"></i> ${new Date().toLocaleDateString('en-US')}</span>
                            </div>
                        </div>
                    </div>
                </div>

                <!-- Team Collaboration Section -->
                <div class="section-content card p-8 mb-8 rounded-2xl shadow-lg hidden" id="team-collaboration">
                    <div class="flex justify-between items-center mb-6">
                        <h2 class="text-3xl font-bold text-gray-800 dark:text-white">Team Collaboration</h2>
                        <button onclick="openAddContent('team-collaboration')" class="bg-green-600 hover:bg-green-700 text-white px-4 py-2 rounded-xl transition-all">
                            <i class="fas fa-plus mr-2"></i>Add Content
                        </button>
                    </div>
                    <div id="team-collaboration-content" class="space-y-4 mb-8">
                        <div class="user-content-item p-6 border border-gray-200 dark:border-gray-700 rounded-2xl bg-white dark:bg-gray-800">
                            <h3 class="text-xl font-semibold text-gray-900 dark:text-white mb-4">Weekly Team Meeting</h3>
                            <p class="text-gray-700 dark:text-gray-300 mb-4">Schedule: Every Monday 10:00 AM. Discuss progress, blockers, and next steps for the project.</p>
                            <div class="flex justify-between items-center text-sm text-gray-600 dark:text-gray-400">
                                <span><i class="fas fa-user mr-1"></i> Team</span>
                                <span><i class="fas fa-clock mr-1"></i> ${new Date().toLocaleDateString('en-US')}</span>
                            </div>
                        </div>
                    </div>
                </div>

                <!-- Team Updates Section -->
                <div class="section-content card p-8 mb-8 rounded-2xl shadow-lg hidden" id="team-updates">
                    <div class="flex justify-between items-center mb-6">
                        <h2 class="text-3xl font-bold text-gray-800 dark:text-white">Team Updates</h2>
                        <button onclick="openAddContent('team-updates')" class="bg-green-600 hover:bg-green-700 text-white px-4 py-2 rounded-xl transition-all">
                            <i class="fas fa-plus mr-2"></i>Add Content
                        </button>
                    </div>
                    <div id="team-updates-content" class="space-y-4 mb-8">
                        <div class="user-content-item p-6 border border-gray-200 dark:border-gray-700 rounded-2xl bg-white dark:bg-gray-800">
                            <h3 class="text-xl font-semibold text-gray-900 dark:text-white mb-4">Project Kickoff Completed</h3>
                            <p class="text-gray-700 dark:text-gray-300 mb-4">Successfully completed project kickoff. All team members are onboard and ready to start development.</p>
                            <div class="flex justify-between items-center text-sm text-gray-600 dark:text-gray-400">
                                <span><i class="fas fa-user mr-1"></i> Project Lead</span>
                                <span><i class="fas fa-clock mr-1"></i> ${new Date().toLocaleDateString('en-US')}</span>
                            </div>
                        </div>
                    </div>
                </div>

                <!-- AI Assistant Section -->
                <div class="section-content card p-8 mb-8 rounded-2xl shadow-lg hidden" id="ai-assistant">
                    <div class="flex justify-between items-center mb-6">
                        <h2 class="text-3xl font-bold text-gray-800 dark:text-white">AI Assistant</h2>
                        <button onclick="openAddContent('ai-assistant')" class="bg-green-600 hover:bg-green-700 text-white px-4 py-2 rounded-xl transition-all">
                            <i class="fas fa-plus mr-2"></i>Add Content
                        </button>
                    </div>
                    <div id="ai-assistant-content" class="space-y-4 mb-8">
                        <div class="user-content-item p-6 border border-gray-200 dark:border-gray-700 rounded-2xl bg-white dark:bg-gray-800">
                            <h3 class="text-xl font-semibold text-gray-900 dark:text-white mb-4">AI Integration Guide</h3>
                            <p class="text-gray-700 dark:text-gray-300 mb-4">Guide for integrating AI capabilities into the Security Chaos Engineering dashboard.</p>
                            <div class="flex justify-between items-center text-sm text-gray-600 dark:text-gray-400">
                                <span><i class="fas fa-user mr-1"></i> AI Team</span>
                                <span><i class="fas fa-clock mr-1"></i> ${new Date().toLocaleDateString('en-US')}</span>
                            </div>
                        </div>
                    </div>
                </div>

                <!-- Practical Tasks Section -->
                <div class="section-content card p-8 mb-8 rounded-2xl shadow-lg hidden" id="practical-tasks">
                    <div class="flex justify-between items-center mb-6">
                        <h2 class="text-3xl font-bold text-gray-800 dark:text-white">Practical Tasks</h2>
                        <button onclick="openAddContent('practical-tasks')" class="bg-green-600 hover:bg-green-700 text-white px-4 py-2 rounded-xl transition-all">
                            <i class="fas fa-plus mr-2"></i>Add Content
                        </button>
                    </div>
                    <div id="practical-tasks-content" class="space-y-4 mb-8">
                        <div class="user-content-item p-6 border border-gray-200 dark:border-gray-700 rounded-2xl bg-white dark:bg-gray-800">
                            <h3 class="text-xl font-semibold text-gray-900 dark:text-white mb-4">Setup Development Environment</h3>
                            <p class="text-gray-700 dark:text-gray-300 mb-4">Task: Setup local development environment with all necessary tools and dependencies.</p>
                            <div class="flex justify-between items-center text-sm text-gray-600 dark:text-gray-400">
                                <span><i class="fas fa-user mr-1"></i> Development Team</span>
                                <span><i class="fas fa-clock mr-1"></i> ${new Date().toLocaleDateString('en-US')}</span>
                            </div>
                        </div>
                    </div>
                </div>

                <!-- Implementation Section -->
                <div class="section-content card p-8 mb-8 rounded-2xl shadow-lg hidden" id="implementation">
                    <div class="flex justify-between items-center mb-6">
                        <h2 class="text-3xl font-bold text-gray-800 dark:text-white">Implementation</h2>
                        <button onclick="openAddContent('implementation')" class="bg-green-600 hover:bg-green-700 text-white px-4 py-2 rounded-xl transition-all">
                            <i class="fas fa-plus mr-2"></i>Add Content
                        </button>
                    </div>
                    <div id="implementation-content" class="space-y-4 mb-8">
                        <div class="user-content-item p-6 border border-gray-200 dark:border-gray-700 rounded-2xl bg-white dark:bg-gray-800">
                            <h3 class="text-xl font-semibold text-gray-900 dark:text-white mb-4">Frontend Architecture</h3>
                            <p class="text-gray-700 dark:text-gray-300 mb-4">Planning the frontend architecture using React/Vue.js with real-time updates.</p>
                            <div class="flex justify-between items-center text-sm text-gray-600 dark:text-gray-400">
                                <span><i class="fas fa-user mr-1"></i> Frontend Team</span>
                                <span><i class="fas fa-clock mr-1"></i> ${new Date().toLocaleDateString('en-US')}</span>
                            </div>
                        </div>
                    </div>
                </div>

                <!-- Research Section -->
                <div class="section-content card p-8 mb-8 rounded-2xl shadow-lg hidden" id="research">
                    <div class="flex justify-between items-center mb-6">
                        <h2 class="text-3xl font-bold text-gray-800 dark:text-white">Research</h2>
                        <button onclick="openAddContent('research')" class="bg-green-600 hover:bg-green-700 text-white px-4 py-2 rounded-xl transition-all">
                            <i class="fas fa-plus mr-2"></i>Add Content
                        </button>
                    </div>
                    <div id="research-content" class="space-y-4 mb-8">
                        <div class="user-content-item p-6 border border-gray-200 dark:border-gray-700 rounded-2xl bg-white dark:bg-gray-800">
                            <h3 class="text-xl font-semibold text-gray-900 dark:text-white mb-4">Security Chaos Engineering Best Practices</h3>
                            <p class="text-gray-700 dark:text-gray-300 mb-4">Research on implementing Security Chaos Engineering principles in cloud environments.</p>
                            <div class="flex justify-between items-center text-sm text-gray-600 dark:text-gray-400">
                                <span><i class="fas fa-user mr-1"></i> Research Team</span>
                                <span><i class="fas fa-clock mr-1"></i> ${new Date().toLocaleDateString('en-US')}</span>
                            </div>
                        </div>
                    </div>
                </div>

                <!-- Resources Section -->
                <div class="section-content card p-8 mb-8 rounded-2xl shadow-lg hidden" id="resources">
                    <div class="flex justify-between items-center mb-6">
                        <h2 class="text-3xl font-bold text-gray-800 dark:text-white">Resources</h2>
                        <button onclick="openAddContent('resources')" class="bg-green-600 hover:bg-green-700 text-white px-4 py-2 rounded-xl transition-all">
                            <i class="fas fa-plus mr-2"></i>Add Content
                        </button>
                    </div>
                    <div id="resources-content" class="space-y-4 mb-8">
                        <div class="user-content-item p-6 border border-gray-200 dark:border-gray-700 rounded-2xl bg-white dark:bg-gray-800">
                            <h3 class="text-xl font-semibold text-gray-900 dark:text-white mb-4">Useful Links & Documentation</h3>
                            <p class="text-gray-700 dark:text-gray-300 mb-4">Collection of useful resources, documentation, and reference materials for the project.</p>
                            <div class="flex justify-between items-center text-sm text-gray-600 dark:text-gray-400">
                                <span><i class="fas fa-user mr-1"></i> Documentation Team</span>
                                <span><i class="fas fa-clock mr-1"></i> ${new Date().toLocaleDateString('en-US')}</span>
                            </div>
                        </div>
                    </div>
                </div>
                    <div class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-4 gap-6 mb-8">
                        <div class="text-center p-6 bg-gradient-to-br from-blue-500 to-blue-600 text-white rounded-2xl shadow-lg">
                            <div class="text-3xl font-bold mb-2">8</div>
                            <div class="text-blue-100">Total Tasks</div>
                        </div>
                        <div class="text-center p-6 bg-gradient-to-br from-green-500 to-green-600 text-white rounded-2xl shadow-lg">
                            <div class="text-3xl font-bold mb-2">3</div>
                            <div class="text-green-100">Completed</div>
                        </div>
                        <div class="text-center p-6 bg-gradient-to-br from-yellow-500 to-yellow-600 text-white rounded-2xl shadow-lg">
                            <div class="text-3xl font-bold mb-2">2</div>
                            <div class="text-yellow-100">In Progress</div>
                        </div>
                        <div class="text-center p-6 bg-gradient-to-br from-purple-500 to-purple-600 text-white rounded-2xl shadow-lg">
                            <div class="text-3xl font-bold mb-2">6</div>
                            <div class="text-purple-100">Team Members</div>
                        </div>
                    </div>
                </div>

                <!-- Other sections would go here... -->
            </div>
        </div>
    </div>

    <script>
        // SMART CHAT SYSTEM
        class SmartChatSystem {
            constructor() {
                this.chatKey = 'team-chat-messages';
                this.deletedKey = 'team-chat-deleted';
                this.currentUser = 'You';
                this.loadMessages();
                this.startAutoRefresh();
            }

            loadMessages() {
                const messages = JSON.parse(localStorage.getItem(this.chatKey)) || [];
                const deletedMessages = JSON.parse(localStorage.getItem(this.deletedKey)) || [];
                
                const activeMessages = messages.filter(msg => 
                    !deletedMessages.some(deleted => 
                        deleted.id === msg.id && deleted.user === this.currentUser
                    )
                );
                
                this.displayMessages(activeMessages);
            }

            saveMessage(text) {
                const messages = JSON.parse(localStorage.getItem(this.chatKey)) || [];
                const newMessage = {
                    id: Date.now().toString(),
                    user: this.currentUser,
                    text: text,
                    timestamp: new Date().toLocaleTimeString(),
                    date: new Date().toISOString()
                };
                messages.push(newMessage);
                
                if (messages.length > 100) messages.shift();
                
                localStorage.setItem(this.chatKey, JSON.stringify(messages));
                this.loadMessages();
            }

            deleteMessage(messageId) {
                const deletedMessages = JSON.parse(localStorage.getItem(this.deletedKey)) || [];
                const messages = JSON.parse(localStorage.getItem(this.chatKey)) || [];
                
                const messageToDelete = messages.find(msg => msg.id === messageId);
                
                if (messageToDelete && messageToDelete.user === this.currentUser) {
                    deletedMessages.push({
                        id: messageId,
                        user: this.currentUser,
                        deletedAt: new Date().toISOString(),
                        originalMessage: messageToDelete
                    });
                    
                    localStorage.setItem(this.deletedKey, JSON.stringify(deletedMessages));
                    this.loadMessages();
                    this.showUndoNotification(messageId);
                }
            }

            restoreMessage(messageId) {
                const deletedMessages = JSON.parse(localStorage.getItem(this.deletedKey)) || [];
                const updatedDeleted = deletedMessages.filter(msg => msg.id !== messageId);
                
                localStorage.setItem(this.deletedKey, JSON.stringify(updatedDeleted));
                this.loadMessages();
            }

            showUndoNotification(messageId) {
                const notification = document.createElement('div');
                notification.className = 'fixed bottom-4 left-4 bg-gray-800 text-white p-3 rounded-lg shadow-lg z-50';
                notification.innerHTML = `
                    <div class="flex items-center gap-3">
                        <span>Meddelande raderat</span>
                        <button onclick="smartChatSystem.restoreMessage('${messageId}'); this.parentElement.parentElement.remove()" 
                                class="bg-blue-500 hover:bg-blue-600 text-white px-3 py-1 rounded text-sm">
                            Ångra
                        </button>
                        <button onclick="this.parentElement.parentElement.remove()" class="text-gray-300 hover:text-white">
                            <i class="fas fa-times"></i>
                        </button>
                    </div>
                `;
                document.body.appendChild(notification);
                
                setTimeout(() => {
                    if (notification.parentElement) {
                        notification.remove();
                    }
                }, 5000);
            }

            displayMessages(messages) {
                const chatMessages = document.getElementById('chatMessages');
                if (!chatMessages) return;

                chatMessages.innerHTML = '';
                
                if (messages.length === 0) {
                    chatMessages.innerHTML = `
                        <div class="text-center text-gray-500 text-sm py-4">
                            <i class="fas fa-comments text-xl mb-2 block"></i>
                            No messages yet. Start the conversation!
                        </div>
                    `;
                    return;
                }

                messages.forEach(msg => {
                    const messageElement = document.createElement('div');
                    const isOwnMessage = msg.user === this.currentUser;
                    messageElement.className = `p-3 mb-2 rounded-lg max-w-[85%] relative group ${isOwnMessage ? 'bg-blue-500 text-white ml-auto' : 'bg-gray-200 text-gray-800'}`;
                    
                    messageElement.innerHTML = `
                        <div class="font-semibold text-sm">${msg.user}</div>
                        <div class="break-words">${msg.text}</div>
                        <div class="text-xs opacity-70 mt-1">${msg.timestamp}</div>
                        ${isOwnMessage ? `
                            <button onclick="smartChatSystem.deleteMessage('${msg.id}')" 
                                    class="absolute -top-2 -right-2 bg-red-500 text-white rounded-full w-5 h-5 text-xs opacity-0 group-hover:opacity-100 transition-opacity">
                                <i class="fas fa-times"></i>
                            </button>
                        ` : ''}
                    `;
                    chatMessages.appendChild(messageElement);
                });

                chatMessages.scrollTop = chatMessages.scrollHeight;
            }

            showDeletedMessages() {
                const deletedMessages = JSON.parse(localStorage.getItem(this.deletedKey)) || [];
                const myDeleted = deletedMessages.filter(msg => msg.user === this.currentUser);
                
                if (myDeleted.length === 0) {
                    alert('Inga raderade meddelanden att återställa.');
                    return;
                }
                
                let message = 'Dina raderade meddelanden:\n\n';
                myDeleted.forEach((msg, index) => {
                    message += `${index + 1}. ${msg.originalMessage.text} (${new Date(msg.deletedAt).toLocaleTimeString()})\n`;
                });
                
                message += '\nVilket meddelande vill du återställa? (Ange nummer)';
                
                const choice = prompt(message);
                const index = parseInt(choice) - 1;
                
                if (index >= 0 && index < myDeleted.length) {
                    this.restoreMessage(myDeleted[index].id);
                }
            }

            startAutoRefresh() {
                setInterval(() => {
                    this.loadMessages();
                }, 2000);
            }
        }

        // Initialize the smart chat
        const smartChatSystem = new SmartChatSystem();

        // Chat Functions
        function openTeamChat() {
            document.getElementById('teamChatModal').classList.add('active');
        }

        function closeTeamChat() {
            document.getElementById('teamChatModal').classList.remove('active');
        }

        function sendChatMessage() {
            const input = document.getElementById('chatInput');
            const message = input.value.trim();
            
            if (message) {
                smartChatSystem.saveMessage(message);
                input.value = '';
            }
        }

        function showDeletedMessages() {
            smartChatSystem.showDeletedMessages();
        }

        // Handle Enter key in chat
        document.getElementById('chatInput')?.addEventListener('keypress', function(e) {
            if (e.key === 'Enter') {
                sendChatMessage();
            }
        });

        // Rest of your existing dashboard code...
        let userContents = JSON.parse(localStorage.getItem('userContents')) || {};

        function saveContents() {
            localStorage.setItem('userContents', JSON.stringify(userContents));
        }

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
            showNotification(contentId ? 'Content updated successfully!' : 'Content added successfully!', 'success');
        });

        function openAddContent(section, contentId = null) {
            document.getElementById('contentSection').value = section;
            document.getElementById('editContentId').value = contentId || '';
            
            if (contentId) {
                document.getElementById('modalTitle').textContent = 'Edit Content';
                document.getElementById('submitButtonText').textContent = 'Update Content';
                const content = userContents[section].find(item => item.id === contentId);
                if (content) {
                    document.getElementById('contentTitle').value = content.title;
                    document.getElementById('contentDescription').value = content.description;
                    document.getElementById('contentAuthor').value = content.author;
                }
            } else {
                document.getElementById('modalTitle').textContent = 'Add New Content';
                document.getElementById('submitButtonText').textContent = 'Add Content';
                document.getElementById('addContentForm').reset();
                document.getElementById('contentAuthor').value = 'Kaled Osman';
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
                contentElement.className = 'user-content-item p-6 border border-gray-200 dark:border-gray-700 rounded-2xl bg-white dark:bg-gray-800';
                contentElement.innerHTML = `
                    <div class="flex justify-between items-start mb-4">
                        <h3 class="text-xl font-semibold text-gray-900 dark:text-white">${content.title}</h3>
                        <div class="flex space-x-2">
                            <button onclick="editContent('${section}', '${content.id}')" class="text-blue-600 hover:text-blue-800 dark:text-blue-400 dark:hover:text-blue-300">
                                <i class="fas fa-edit"></i>
                            </button>
                            <button onclick="deleteContent('${section}', '${content.id}')" class="text-red-600 hover:text-red-800 dark:text-red-400 dark:hover:text-red-300">
                                <i class="fas fa-trash"></i>
                            </button>
                        </div>
                    </div>
                    <p class="text-gray-700 dark:text-gray-300 mb-4 whitespace-pre-line">${content.description}</p>
                    <div class="flex justify-between items-center text-sm text-gray-600 dark:text-gray-400">
                        <span><i class="fas fa-user mr-1"></i> ${content.author}</span>
                        <span><i class="fas fa-clock mr-1"></i> ${new Date(content.date).toLocaleDateString('en-US')}</span>
                    </div>
                    ${content.updated ? `
                    <div class="mt-2 text-xs text-gray-500 dark:text-gray-500">
                        <i class="fas fa-sync mr-1"></i> Updated: ${new Date(content.updated).toLocaleDateString('en-US')}
                    </div>
                    ` : ''}
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
                showNotification('Content deleted successfully!', 'success');
            }
        }

        function initializeAllContents() {
            const sections = [
                'overview', 
                'project-documentation', 
                'team-collaboration', 
                'team-updates', 
                'ai-assistant',
                'practical-tasks',
                'implementation', 
                'research',
                'resources'
            ];
            sections.forEach(section => {
                renderSectionContents(section);
            });
        }

        function showNotification(message, type = 'info') {
            const notification = document.createElement('div');
            const colors = {
                success: 'bg-green-500',
                error: 'bg-red-500',
                warning: 'bg-yellow-500',
                info: 'bg-blue-500'
            };

            notification.className = `fixed top-4 right-4 ${colors[type]} text-white px-6 py-3 rounded-lg shadow-lg z-50`;
            notification.innerHTML = `
                <div class="flex justify-between items-center">
                    <span>${message}</span>
                    <button onclick="this.parentElement.parentElement.remove()" class="ml-4">
                        <i class="fas fa-times"></i>
                    </button>
                </div>
            `;

            document.body.appendChild(notification);
            setTimeout(() => {
                if (notification.parentElement) {
                    notification.remove();
                }
            }, 5000);
        }

        function toggleTheme() {
            const body = document.body;
            const currentTheme = body.getAttribute('data-theme');
            const newTheme = currentTheme === 'dark' ? 'light' : 'dark';
            
            body.setAttribute('data-theme', newTheme);
            localStorage.setItem('theme', newTheme);
            
            if (newTheme === 'dark') {
                body.classList.add('dark');
            } else {
                body.classList.remove('dark');
            }
            
            showNotification('Theme changed to ' + newTheme + ' mode', 'success');
        }

        function setupNavigation() {
            document.querySelectorAll('.section-btn').forEach(btn => {
                btn.addEventListener('click', function() {
                    document.querySelectorAll('.section-btn').forEach(b => {
                        b.classList.remove('active-section');
                        b.classList.add('hover:bg-gray-100', 'dark:hover:bg-gray-700', 'text-gray-700', 'dark:text-gray-300');
                    });
                    this.classList.add('active-section');
                    this.classList.remove('hover:bg-gray-100', 'dark:hover:bg-gray-700', 'text-gray-700', 'dark:text-gray-300');
                    
                    const sectionId = this.dataset.section;
                    document.querySelectorAll('.section-content').forEach(section => {
                        section.classList.add('hidden');
                    });
                    document.getElementById(sectionId).classList.remove('hidden');
                });
            });
        }

        function fetchSystemData() {
            showNotification('Fetching system data...', 'info');
            setTimeout(() => {
                showNotification('System data updated!', 'success');
            }, 1000);
        }

        // Initialize the application
        document.addEventListener('DOMContentLoaded', function() {
            const savedTheme = localStorage.getItem('theme') || 'light';
            if (savedTheme === 'dark') {
                document.body.setAttribute('data-theme', 'dark');
                document.body.classList.add('dark');
            }

            setupNavigation();
            fetchSystemData();
            initializeAllContents();
            
            showNotification('Welcome to the Team Dashboard! Realtime features are active.', 'success');
        });

    </script>
</body>
</html>

