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

        .login-modal {
            display: none;
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background: rgba(0, 0, 0, 0.5);
            z-index: 1000;
        }

        .login-modal.active {
            display: flex;
            align-items: center;
            justify-content: center;
        }

        /* تحسين القراءة في الوضع الداكن */
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
    </style>
</head>
<body class="theme-transition" data-theme="light">
    <!-- Login Modal -->
    <div class="login-modal" id="loginModal">
        <div class="bg-white dark:bg-gray-800 p-8 rounded-2xl max-w-md w-full mx-4 shadow-2xl">
            <h2 class="text-2xl font-bold mb-6 text-center text-gray-800 dark:text-white">Team Login</h2>
            <form id="loginForm" class="space-y-4">
                <div>
                    <label class="block text-sm font-medium mb-2 text-gray-700 dark:text-gray-300">Username</label>
                    <input type="text" class="w-full p-3 border border-gray-300 dark:border-gray-600 rounded-xl focus:ring-2 focus:ring-blue-500 focus:border-blue-500 dark:bg-gray-700 dark:text-white" required>
                </div>
                <div>
                    <label class="block text-sm font-medium mb-2 text-gray-700 dark:text-gray-300">Password</label>
                    <input type="password" class="w-full p-3 border border-gray-300 dark:border-gray-600 rounded-xl focus:ring-2 focus:ring-blue-500 focus:border-blue-500 dark:bg-gray-700 dark:text-white" required>
                </div>
                <button type="submit" class="w-full bg-blue-600 hover:bg-blue-700 text-white py-3 rounded-xl transition-all font-medium">
                    Sign In
                </button>
            </form>
            <button onclick="closeLogin()" class="absolute top-4 right-4 text-gray-500 hover:text-gray-700 dark:text-gray-400 dark:hover:text-gray-200">
                <i class="fas fa-times text-xl"></i>
            </button>
        </div>
    </div>

    <!-- Main Layout -->
    <div class="flex min-h-screen">
        <!-- Sidebar -->
        <div class="sidebar w-80 flex-shrink-0 theme-transition p-6">
            <div class="mb-8">
                <h1 class="text-2xl font-bold text-blue-600 dark:text-blue-400">Security Chaos Engineering</h1>
                <p class="text-gray-600 dark:text-gray-400 mt-2">Group 1 Dashboard</p>
            </div>
            
            <!-- Login Button -->
            <button onclick="openLogin()" class="w-full mb-6 bg-blue-600 hover:bg-blue-700 text-white py-3 rounded-xl transition-all shadow-lg font-medium">
                <i class="fas fa-sign-in-alt mr-3"></i>Team Login
            </button>

            <!-- Navigation - جميع الأقسام الـ 11 -->
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
                <button class="section-btn w-full text-left p-4 rounded-xl theme-transition hover:bg-gray-100 dark:hover:bg-gray-700 text-gray-700 dark:text-gray-300" data-section="algorithm-visualizer">
                    <i class="fas fa-project-diagram mr-3 w-6 text-center"></i>Algorithm Visualizer
                </button>
                <button class="section-btn w-full text-left p-4 rounded-xl theme-transition hover:bg-gray-100 dark:hover:bg-gray-700 text-gray-700 dark:text-gray-300" data-section="my-implementation">
                    <i class="fas fa-code mr-3 w-6 text-center"></i>My Implementation
                </button>
                <button class="section-btn w-full text-left p-4 rounded-xl theme-transition hover:bg-gray-100 dark:hover:bg-gray-700 text-gray-700 dark:text-gray-300" data-section="team-dashboard">
                    <i class="fas fa-tachometer-alt mr-3 w-6 text-center"></i>Team Dashboard
                </button>
                <button class="section-btn w-full text-left p-4 rounded-xl theme-transition hover:bg-gray-100 dark:hover:bg-gray-700 text-gray-700 dark:text-gray-300" data-section="dijkstra-algorithm">
                    <i class="fas fa-network-wired mr-3 w-6 text-center"></i>Dijkstra Algorithm
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
            <!-- Header -->
            <header class="gradient-header text-white p-6 shadow-lg">
                <div class="flex justify-between items-center">
                    <div>
                        <h1 class="text-3xl font-bold">Security Chaos Engineering Dashboard</h1>
                        <p class="mt-2 opacity-90">Group 1 - Full-Stack Dashboard with Real-time Data</p>
                    </div>
                    <div class="flex items-center space-x-4">
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
                    <h2 class="text-3xl font-bold mb-6 text-gray-800 dark:text-white">Project Overview</h2>
                    
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
                    
                    <div class="p-6 bg-blue-50 dark:bg-blue-900/20 rounded-2xl mb-8 border border-blue-200 dark:border-blue-800">
                        <h3 class="text-xl font-semibold mb-3 text-blue-800 dark:text-blue-200">Project Summary</h3>
                        <p class="text-blue-700 dark:text-blue-300">"Group 1 - Full-Stack Dashboard with Algorithm Visualizer" - A comprehensive dashboard for Security Chaos Engineering and Cloud Networks.</p>
                    </div>

                    <div class="mb-6">
                        <h3 class="text-xl font-semibold mb-4 text-gray-800 dark:text-white">System Status</h3>
                        <div id="alerts-container" class="mb-6">
                            <div class="p-4 bg-green-100 dark:bg-green-900/20 text-green-800 dark:text-green-300 rounded-2xl border border-green-200 dark:border-green-800">
                                <i class="fas fa-check-circle mr-3"></i> All systems functioning normally
                            </div>
                        </div>
                        <button onclick="checkAlerts()" class="bg-gradient-to-r from-orange-500 to-orange-600 hover:from-orange-600 hover:to-orange-700 text-white py-3 px-6 rounded-xl transition-all shadow-lg">
                            <i class="fas fa-bell mr-3"></i> Check System Alerts
                        </button>
                    </div>
                </div>

                <!-- Project Documentation Section -->
                <div class="section-content card p-8 mb-8 rounded-2xl shadow-lg hidden" id="project-documentation">
                    <h2 class="text-3xl font-bold mb-4 text-gray-800 dark:text-white">Project Documentation - Graph Algorithms</h2>
                    <div class="space-y-4">
                        <h3 class="text-xl font-semibold text-gray-800 dark:text-white">By Group 1: Fahad Hussain, Stefan Österberg, Kaled Osman, Marcus Tibell, Jens Annell, Luwam</h3>
                        
                        <div class="p-4 border border-gray-200 dark:border-gray-700 rounded-xl">
                            <h4 class="font-semibold mb-2 text-gray-800 dark:text-white">Dijkstra's Algorithm</h4>
                            <p class="text-gray-600 dark:text-gray-400"><strong>The purpose of Dijkstra's algorithm</strong> is to find the shortest possible path between nodes in a weighted graph.</p>
                        </div>

                        <div class="p-4 border border-gray-200 dark:border-gray-700 rounded-xl">
                            <h4 class="font-semibold mb-2 text-gray-800 dark:text-white">Kaled - Why does the shortest path matter?</h4>
                            <p class="text-gray-600 dark:text-gray-400">"As mentioned earlier, the purpose of the algorithm is to find the shortest possible path, so why does the shortest possible path matter?"</p>
                        </div>

                        <div class="p-4 border border-gray-200 dark:border-gray-700 rounded-xl">
                            <h4 class="font-semibold mb-2 text-gray-800 dark:text-white">Fahad - Uses of shortest path</h4>
                            <p class="text-gray-600 dark:text-gray-400">"A common usage of the shortest path within our sphere of operation is for example, in networking, where speed and efficiency are crucial..."</p>
                        </div>
                    </div>
                </div>

                <!-- Team Collaboration Section -->
                <div class="section-content card p-8 mb-8 rounded-2xl shadow-lg hidden" id="team-collaboration">
                    <h2 class="text-3xl font-bold mb-4 text-gray-800 dark:text-white">Team Collaboration - Group Documentation</h2>
                    
                    <div class="grid md:grid-cols-2 gap-6">
                        <div class="p-4 border border-gray-200 dark:border-gray-700 rounded-xl">
                            <h3 class="font-semibold mb-3 text-gray-800 dark:text-white">Add New Task</h3>
                            <form id="assignment-form" class="space-y-3">
                                <div>
                                    <label class="block text-sm font-medium mb-1 text-gray-700 dark:text-gray-300">Task Name:</label>
                                    <input type="text" id="task-name" class="w-full p-2 border border-gray-300 dark:border-gray-600 rounded theme-transition dark:bg-gray-700 dark:text-white">
                                </div>
                                <div>
                                    <label class="block text-sm font-medium mb-1 text-gray-700 dark:text-gray-300">Description:</label>
                                    <textarea id="task-desc" class="w-full p-2 border border-gray-300 dark:border-gray-600 rounded theme-transition dark:bg-gray-700 dark:text-white h-24"></textarea>
                                </div>
                                <div>
                                    <label class="block text-sm font-medium mb-1 text-gray-700 dark:text-gray-300">Assignee:</label>
                                    <select id="task-assignee" class="w-full p-2 border border-gray-300 dark:border-gray-600 rounded theme-transition dark:bg-gray-700 dark:text-white">
                                        <option value="Kaled Osman">Kaled Osman</option>
                                        <option value="Fahad Hussain">Fahad Hussain</option>
                                        <option value="Stefan Österberg">Stefan Österberg</option>
                                        <option value="Marcus Tibell">Marcus Tibell</option>
                                        <option value="Jens Annell">Jens Annell</option>
                                        <option value="Luwam">Luwam</option>
                                    </select>
                                </div>
                                <button type="submit" class="w-full bg-green-600 hover:bg-green-700 text-white py-2 rounded theme-transition">
                                    Add Task
                                </button>
                            </form>
                        </div>

                        <div class="p-4 border border-gray-200 dark:border-gray-700 rounded-xl">
                            <h3 class="font-semibold mb-3 text-gray-800 dark:text-white">Current Tasks</h3>
                            <div id="assignments-list" class="space-y-3">
                                <div class="p-3 bg-gray-50 dark:bg-gray-800 rounded theme-transition">
                                    <strong class="text-gray-800 dark:text-white">Improve Dijkstra Algorithm</strong>
                                    <p class="text-sm text-gray-600 dark:text-gray-400 mt-1">Add performance improvements and complexity analysis</p>
                                    <small class="text-gray-600 dark:text-gray-400"><strong>Assignee:</strong> Kaled Osman</small>
                                </div>
                                <div class="p-3 bg-gray-50 dark:bg-gray-800 rounded theme-transition">
                                    <strong class="text-gray-800 dark:text-white">OSPF Protocols Research</strong>
                                    <p class="text-sm text-gray-600 dark:text-gray-400 mt-1">Study OSPF protocols and their applications in networks</p>
                                    <small class="text-gray-600 dark:text-gray-400"><strong>Assignee:</strong> Fahad Hussain</small>
                                </div>
                            </div>
                        </div>
                    </div>
                </div>

                <!-- Team Updates Section -->
                <div class="section-content card p-8 mb-8 rounded-2xl shadow-lg hidden" id="team-updates">
                    <h2 class="text-3xl font-bold mb-4 text-gray-800 dark:text-white">Team Updates & Tasks</h2>
                    
                    <div class="space-y-6">
                        <div class="p-4 border border-gray-200 dark:border-gray-700 rounded-xl">
                            <h3 class="font-semibold mb-3 text-gray-800 dark:text-white">Add Your Update</h3>
                            <form id="team-update-form" class="space-y-3">
                                <div class="grid md:grid-cols-2 gap-3">
                                    <div>
                                        <label class="block text-sm font-medium mb-1 text-gray-700 dark:text-gray-300">Your Name:</label>
                                        <select id="update-author" class="w-full p-2 border border-gray-300 dark:border-gray-600 rounded theme-transition dark:bg-gray-700 dark:text-white">
                                            <option value="Kaled Osman">Kaled Osman</option>
                                            <option value="Fahad Hussain">Fahad Hussain</option>
                                            <option value="Stefan Österberg">Stefan Österberg</option>
                                            <option value="Marcus Tibell">Marcus Tibell</option>
                                            <option value="Jens Annell">Jens Annell</option>
                                            <option value="Luwam">Luwam</option>
                                        </select>
                                    </div>
                                    <div>
                                        <label class="block text-sm font-medium mb-1 text-gray-700 dark:text-gray-300">Status:</label>
                                        <select id="update-status" class="w-full p-2 border border-gray-300 dark:border-gray-600 rounded theme-transition dark:bg-gray-700 dark:text-white">
                                            <option value="completed">Completed</option>
                                            <option value="in-progress">In Progress</option>
                                            <option value="planned">Planned</option>
                                            <option value="blocked">Blocked</option>
                                        </select>
                                    </div>
                                </div>
                                <div>
                                    <label class="block text-sm font-medium mb-1 text-gray-700 dark:text-gray-300">Task Description:</label>
                                    <input type="text" id="update-title" class="w-full p-2 border border-gray-300 dark:border-gray-600 rounded theme-transition dark:bg-gray-700 dark:text-white" placeholder="What have you done?">
                                </div>
                                <div>
                                    <label class="block text-sm font-medium mb-1 text-gray-700 dark:text-gray-300">Details:</label>
                                    <textarea id="update-details" class="w-full p-2 border border-gray-300 dark:border-gray-600 rounded theme-transition dark:bg-gray-700 dark:text-white h-24" placeholder="Describe your work..."></textarea>
                                </div>
                                <button type="submit" class="w-full bg-green-600 hover:bg-green-700 text-white py-2 rounded theme-transition">
                                    Publish Update
                                </button>
                            </form>
                        </div>

                        <div id="team-updates-list" class="space-y-4">
                            <!-- Team updates will be added here dynamically -->
                        </div>
                    </div>
                </div>

                <!-- AI Assistant Section -->
                <div class="section-content card p-8 mb-8 rounded-2xl shadow-lg hidden" id="ai-assistant">
                    <h2 class="text-3xl font-bold mb-4 text-gray-800 dark:text-white">AI Assistant - Security Chaos Engineering</h2>
                    
                    <div class="space-y-4">
                        <div class="ai-chat-container border border-gray-300 dark:border-gray-600 rounded-lg p-4 h-64 overflow-y-auto theme-transition">
                            <div class="p-3 bg-blue-50 dark:bg-blue-900/20 rounded-lg theme-transition mb-2">
                                <strong class="text-gray-800 dark:text-white">AI Assistant:</strong> Hello! I'm here to help you with Security Chaos Engineering and Cloud Networks. Ask me anything!
                            </div>
                        </div>

                        <div class="flex gap-2">
                            <input type="text" id="aiChatInput" class="flex-1 p-2 border border-gray-300 dark:border-gray-600 rounded theme-transition dark:bg-gray-700 dark:text-white" placeholder="Ask about Security Chaos Engineering or Cloud Networks...">
                            <button onclick="sendAIMessage()" class="bg-green-600 hover:bg-green-700 text-white px-4 py-2 rounded theme-transition">
                                Send
                            </button>
                        </div>

                        <div>
                            <h3 class="font-semibold mb-2 text-gray-800 dark:text-white">Quick Commands</h3>
                            <div class="flex flex-wrap gap-2">
                                <button onclick="quickQuestion('What is Security Chaos Engineering?')" class="bg-gray-100 hover:bg-gray-200 dark:bg-gray-800 dark:hover:bg-gray-700 px-3 py-2 rounded theme-transition text-gray-800 dark:text-white">
                                    Security Chaos Engineering
                                </button>
                                <button onclick="quickQuestion('How to apply Security Chaos Engineering in AWS?')" class="bg-gray-100 hover:bg-gray-200 dark:bg-gray-800 dark:hover:bg-gray-700 px-3 py-2 rounded theme-transition text-gray-800 dark:text-white">
                                    AWS Implementation
                                </button>
                                <button onclick="quickQuestion('What are the best tools for Security Chaos Engineering?')" class="bg-gray-100 hover:bg-gray-200 dark:bg-gray-800 dark:hover:bg-gray-700 px-3 py-2 rounded theme-transition text-gray-800 dark:text-white">
                                    Tools & Technologies
                                </button>
                            </div>
                        </div>
                    </div>
                </div>

                <!-- Practical Tasks Section -->
                <div class="section-content card p-8 mb-8 rounded-2xl shadow-lg hidden" id="practical-tasks">
                    <h2 class="text-3xl font-bold mb-4 text-gray-800 dark:text-white">Practical Tasks - Project Assignments</h2>
                    
                    <div class="flex justify-between items-center mb-6">
                        <div class="grid grid-cols-4 gap-4 flex-1">
                            <div class="text-center p-4 bg-blue-50 dark:bg-blue-900/30 rounded-lg theme-transition">
                                <div class="text-2xl font-bold">8</div>
                                <div class="text-sm text-gray-600 dark:text-gray-400">Total Tasks</div>
                            </div>
                            <div class="text-center p-4 bg-green-50 dark:bg-green-900/30 rounded-lg theme-transition">
                                <div class="text-2xl font-bold">3</div>
                                <div class="text-sm text-gray-600 dark:text-gray-400">Completed</div>
                            </div>
                            <div class="text-center p-4 bg-yellow-50 dark:bg-yellow-900/30 rounded-lg theme-transition">
                                <div class="text-2xl font-bold">2</div>
                                <div class="text-sm text-gray-600 dark:text-gray-400">In Progress</div>
                            </div>
                            <div class="text-center p-4 bg-purple-50 dark:bg-purple-900/30 rounded-lg theme-transition">
                                <div class="text-2xl font-bold">3</div>
                                <div class="text-sm text-gray-600 dark:text-gray-400">Pending</div>
                            </div>
                        </div>
                        <button onclick="addNewTask()" class="ml-4 bg-green-600 hover:bg-green-700 text-white px-4 py-2 rounded theme-transition">
                            New Task
                        </button>
                    </div>

                    <div id="tasks-list" class="space-y-3">
                        <!-- Tasks will be added here dynamically -->
                    </div>
                </div>

                <!-- Algorithm Visualizer Section -->
                <div class="section-content card p-8 mb-8 rounded-2xl shadow-lg hidden" id="algorithm-visualizer">
                    <h2 class="text-3xl font-bold mb-4 text-gray-800 dark:text-white">Algorithm Visualizer</h2>
                    
                    <div class="space-y-4">
                        <div class="grid grid-cols-3 gap-4">
                            <div class="text-center p-4 bg-gradient-to-r from-blue-500 to-purple-500 text-white rounded-lg">
                                <div class="text-2xl font-bold">5</div>
                                <div>Servers in Network</div>
                            </div>
                            <div class="text-center p-4 bg-gradient-to-r from-blue-500 to-purple-500 text-white rounded-lg">
                                <div class="text-2xl font-bold">13 ms</div>
                                <div>Shortest Path</div>
                            </div>
                            <div class="text-center p-4 bg-gradient-to-r from-blue-500 to-purple-500 text-white rounded-lg">
                                <div class="text-2xl font-bold">100%</div>
                                <div>Optimization</div>
                            </div>
                        </div>
                        
                        <div class="p-4 bg-gray-100 dark:bg-gray-800 rounded-lg">
                            <pre class="whitespace-pre-wrap dark:text-gray-300">// Server Network Configuration
servers = {
    'WebServer': {'Database': 5, 'Cache': 2},
    'Database': {'Backup': 8, 'WebServer': 5},
    'Cache': {'CDN': 3, 'WebServer': 2},
    'CDN': {'Cache': 3},
    'Backup': {'Database': 8}
}</pre>
                        </div>
                        
                        <div class="p-4 bg-green-500 text-white rounded-lg">
                            <h4 class="font-bold">Dijkstra Step-by-Step:</h4>
                            <p>Start: WebServer (0)</p>
                            <p>Step 1: Find neighbors → Database=5, Cache=2</p>
                            <p>Step 2: Select Cache → find CDN=5</p>
                            <p>Step 3: Select Database → find Backup=13</p>
                            <p class="font-bold">Result: All shortest paths found! ✅</p>
                        </div>
                    </div>
                </div>

                <!-- My Implementation Section -->
                <div class="section-content card p-8 mb-8 rounded-2xl shadow-lg hidden" id="my-implementation">
                    <h2 class="text-3xl font-bold mb-4 text-gray-800 dark:text-white">My Implementation</h2>
                    
                    <div class="space-y-4">
                        <div class="p-4 border border-gray-200 dark:border-gray-700 rounded-xl">
                            <h3 class="font-semibold mb-2 text-gray-800 dark:text-white">Backend Implementation</h3>
                            <p class="text-gray-600 dark:text-gray-400">Python Flask server with REST API endpoints for system monitoring.</p>
                        </div>
                        <div class="p-4 border border-gray-200 dark:border-gray-700 rounded-xl">
                            <h3 class="font-semibold mb-2 text-gray-800 dark:text-white">Frontend Implementation</h3>
                            <p class="text-gray-600 dark:text-gray-400">Modern dashboard implementation with real-time data updates.</p>
                        </div>
                    </div>
                </div>

                <!-- Team Dashboard Section -->
                <div class="section-content card p-8 mb-8 rounded-2xl shadow-lg hidden" id="team-dashboard">
                    <h2 class="text-3xl font-bold mb-4 text-gray-800 dark:text-white">Team Dashboard</h2>
                    
                    <div class="grid grid-cols-2 md:grid-cols-3 gap-4 mb-6">
                        <div class="text-center p-4 border border-gray-200 dark:border-gray-700 rounded-xl">
                            <div class="text-2xl font-bold">6</div>
                            <div class="text-gray-600 dark:text-gray-400">Team Members</div>
                        </div>
                        <div class="text-center p-4 border border-gray-200 dark:border-gray-700 rounded-xl">
                            <div class="text-2xl font-bold">12</div>
                            <div class="text-gray-600 dark:text-gray-400">Completed Tasks</div>
                        </div>
                        <div class="text-center p-4 border border-gray-200 dark:border-gray-700 rounded-xl">
                            <div class="text-2xl font-bold">85%</div>
                            <div class="text-gray-600 dark:text-gray-400">Project Progress</div>
                        </div>
                    </div>
                    
                    <div class="p-4 border border-gray-200 dark:border-gray-700 rounded-xl">
                        <h3 class="font-semibold mb-3 text-gray-800 dark:text-white">Team Members</h3>
                        <ul class="list-disc list-inside space-y-2 text-gray-600 dark:text-gray-400">
                            <li>Kaled Osman - Team Lead & Backend Developer</li>
                            <li>Fahad Hussain - Network Specialist</li>
                            <li>Stefan Österberg - Frontend Developer</li>
                            <li>Marcus Tibell - Security Engineer</li>
                            <li>Jens Annell - DevOps Engineer</li>
                            <li>Luwam - UI/UX Designer</li>
                        </ul>
                    </div>
                </div>

                <!-- Dijkstra Algorithm Section -->
                <div class="section-content card p-8 mb-8 rounded-2xl shadow-lg hidden" id="dijkstra-algorithm">
                    <h2 class="text-3xl font-bold mb-4 text-gray-800 dark:text-white">Dijkstra Algorithm</h2>
                    
                    <div class="space-y-4">
                        <div class="p-4 border border-gray-200 dark:border-gray-700 rounded-xl">
                            <h3 class="font-semibold mb-2 text-gray-800 dark:text-white">Algorithm Explanation</h3>
                            <p class="text-gray-600 dark:text-gray-400">Dijkstra's algorithm finds the shortest path between nodes in a graph.</p>
                        </div>
                        <div class="p-4 border border-gray-200 dark:border-gray-700 rounded-xl">
                            <h3 class="font-semibold mb-2 text-gray-800 dark:text-white">Implementation Steps</h3>
                            <ol class="list-decimal list-inside space-y-1 text-gray-600 dark:text-gray-400">
                                <li>Initialize distances and visited nodes</li>
                                <li>Select node with smallest distance</li>
                                <li>Update neighbor distances</li>
                                <li>Repeat until all nodes visited</li>
                            </ol>
                        </div>
                    </div>
                </div>
            </div>
        </div>
    </div>

    <script>
        // جميع الدوال والوظائف السابقة محفوظة هنا
        // Theme management
        function toggleTheme() {
            const body = document.body;
            const currentTheme = body.getAttribute('data-theme');
            const newTheme = currentTheme === 'dark' ? 'light' : 'dark';
            
            body.setAttribute('data-theme', newTheme);
            localStorage.setItem('theme', newTheme);
            showNotification('Theme changed to ' + newTheme + ' mode', 'success');
        }

        // Login modal functions
        function openLogin() {
            document.getElementById('loginModal').classList.add('active');
        }

        function closeLogin() {
            document.getElementById('loginModal').classList.remove('active');
        }

        // Notification system
        function showNotification(message, type = 'info') {
            // Create notification element
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

        // Navigation system
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

        // System data fetching
        async function fetchSystemData() {
            showNotification('Fetching system data...', 'info');
            
            setTimeout(() => {
                showNotification('System data updated!', 'success');
            }, 1000);
        }

        // Alert system
        function checkAlerts() {
            showNotification('Checking system alerts...', 'info');
            
            const alertsContainer = document.getElementById('alerts-container');
            alertsContainer.innerHTML = `
                <div class="p-4 bg-green-100 dark:bg-green-900/20 text-green-800 dark:text-green-300 rounded-2xl border border-green-200 dark:border-green-800">
                    <i class="fas fa-check-circle mr-3"></i> No critical alerts found
                </div>
            `;
            
            showNotification('Alert check completed', 'success');
        }

        // Team Collaboration functionality
        document.getElementById('assignment-form').addEventListener('submit', function(e) {
            e.preventDefault();
            const name = document.getElementById('task-name').value;
            const desc = document.getElementById('task-desc').value;
            const assignee = document.getElementById('task-assignee').value;
            
            if (name && desc) {
                const assignmentsList = document.getElementById('assignments-list');
                const newAssignment = document.createElement('div');
                newAssignment.className = 'p-3 bg-gray-50 dark:bg-gray-800 rounded theme-transition';
                newAssignment.innerHTML = `
                    <strong class="text-gray-800 dark:text-white">${name}</strong>
                    <p class="text-sm text-gray-600 dark:text-gray-400 mt-1">${desc}</p>
                    <small class="text-gray-600 dark:text-gray-400"><strong>Assignee:</strong> ${assignee}</small>
                `;
                assignmentsList.appendChild(newAssignment);
                
                this.reset();
                showNotification('Task added!', 'success');
            }
        });

        // Team updates functionality
        document.getElementById('team-update-form').addEventListener('submit', function(e) {
            e.preventDefault();
            const author = document.getElementById('update-author').value;
            const status = document.getElementById('update-status').value;
            const title = document.getElementById('update-title').value;
            const details = document.getElementById('update-details').value;

            if (title && details) {
                addTeamUpdate(author, status, title, details);
                this.reset();
            }
        });

        function addTeamUpdate(author, status, title, details) {
            const updatesList = document.getElementById('team-updates-list');
            const statusColors = {
                'completed': '#10B981',
                'in-progress': '#F59E0B', 
                'planned': '#3B82F6',
                'blocked': '#EF4444'
            };

            const updateHTML = `
                <div class="p-4 border border-gray-200 dark:border-gray-700 rounded-xl" style="border-left: 4px solid ${statusColors[status]}">
                    <div class="flex justify-between items-center mb-2">
                        <h4 class="font-semibold text-gray-800 dark:text-white">${title}</h4>
                        <span class="px-2 py-1 rounded text-white text-sm" style="background: ${statusColors[status]}">
                            ${status === 'completed' ? '✅' : status === 'in-progress' ? '🔄' : status === 'planned' ? '📅' : '❌'} 
                            ${status}
                        </span>
                    </div>
                    <p class="text-gray-600 dark:text-gray-400"><strong>${author}</strong> - ${details}</p>
                    <small class="text-gray-500">${new Date().toLocaleDateString('en-US')}</small>
                </div>
            `;

            updatesList.insertAdjacentHTML('afterbegin', updateHTML);
            showNotification('Update published!', 'success');
        }

        // AI Assistant functionality
        function sendAIMessage() {
            const input = document.getElementById('aiChatInput');
            const message = input.value.trim();
            if (!message) return;

            const chatContainer = document.getElementById('aiChatContainer');
            const userMsg = document.createElement('div');
            userMsg.className = 'p-3 bg-gray-100 dark:bg-gray-800 rounded-lg theme-transition mb-2';
            userMsg.innerHTML = `<strong class="text-gray-800 dark:text-white">You:</strong> ${message}`;
            chatContainer.appendChild(userMsg);

            setTimeout(() => {
                const aiMsg = document.createElement('div');
                aiMsg.className = 'p-3 bg-blue-50 dark:bg-blue-900/20 rounded-lg theme-transition mb-2';
                aiMsg.innerHTML = `<strong class="text-gray-800 dark:text-white">AI Assistant:</strong> ${getAIResponse(message)}`;
                chatContainer.appendChild(aiMsg);
                chatContainer.scrollTop = chatContainer.scrollHeight;
            }, 1000);

            input.value = '';
            chatContainer.scrollTop = chatContainer.scrollHeight;
        }

        function quickQuestion(question) {
            document.getElementById('aiChatInput').value = question;
            sendAIMessage();
        }

        function getAIResponse(question) {
            const responses = {
                'security chaos engineering': 'Security Chaos Engineering is the discipline of experimenting on a system in order to build confidence in the system\'s capability to withstand turbulent conditions in production.',
                'aws': 'In AWS, you can implement Security Chaos Engineering using services like AWS Fault Injection Simulator, CloudWatch, and Lambda functions.',
                'tools': 'Popular tools for Security Chaos Engineering include Gremlin, ChaosToolkit, and Simian Army with security integrations.',
                'implementation': 'To implement Security Chaos Engineering, start with identifying critical services, define failure hypotheses, run controlled experiments, and analyze results.',
                'cloud networks': 'Cloud networks in AWS, Azure, and GCP provide scalable infrastructure where Security Chaos Engineering can test resilience and security controls.',
                'difference': 'While Chaos Engineering tests system resilience, Security Chaos Engineering specifically tests security controls and incident response capabilities.'
            };

            const q = question.toLowerCase();
            for (const [key, response] of Object.entries(responses)) {
                if (q.includes(key)) return response;
            }
            return 'I can help you with Security Chaos Engineering concepts, cloud network implementations, and best practices!';
        }

        // Practical Tasks functionality
        function addNewTask() {
            const taskName = prompt('Task name:');
            if (!taskName) return;

            const tasksList = document.getElementById('tasks-list');
            const taskId = Date.now();
            
            const taskHTML = `
                <div class="p-4 border border-gray-200 dark:border-gray-700 rounded-xl" id="task-${taskId}">
                    <div class="flex justify-between items-start mb-2">
                        <h4 class="font-semibold text-gray-800 dark:text-white">${taskName}</h4>
                        <button onclick="removeTask(${taskId})" class="text-red-500 hover:text-red-700">
                            <i class="fas fa-trash"></i>
                        </button>
                    </div>
                    <p class="text-gray-600 dark:text-gray-400 mb-2">New task</p>
                    <div class="w-full bg-gray-200 rounded-full h-2 mb-2">
                        <div class="bg-green-500 h-2 rounded-full" style="width: 0%" id="progress-${taskId}"></div>
                    </div>
                    <div class="flex justify-between items-center">
                        <span class="text-sm text-gray-500">Assignee: Kaled Osman</span>
                        <button onclick="completeTask(${taskId})" class="bg-green-500 hover:bg-green-600 text-white px-3 py-1 rounded text-sm">
                            Mark as complete
                        </button>
                    </div>
                </div>
            `;
            
            tasksList.insertAdjacentHTML('beforeend', taskHTML);
            showNotification('New task added!', 'success');
        }

        function removeTask(taskId) {
            const taskElement = document.getElementById(`task-${taskId}`);
            if (taskElement && confirm('Do you want to remove this task?')) {
                taskElement.remove();
                showNotification('Task removed', 'info');
            }
        }

        function completeTask(taskId) {
            const taskElement = document.getElementById(`task-${taskId}`);
            const progressElement = document.getElementById(`progress-${taskId}`);
            if (taskElement && progressElement) {
                progressElement.style.width = '100%';
                taskElement.querySelector('button').disabled = true;
                taskElement.querySelector('button').textContent = 'Completed ✓';
                taskElement.querySelector('button').classList.remove('bg-green-500', 'hover:bg-green-600');
                taskElement.querySelector('button').classList.add('bg-gray-400');
                showNotification('Task marked as completed!', 'success');
            }
        }

        // Login form handling
        document.getElementById('loginForm').addEventListener('submit', function(e) {
            e.preventDefault();
            showNotification('Login successful! Welcome to the team dashboard.', 'success');
            closeLogin();
        });

        // Initialize the dashboard
        document.addEventListener('DOMContentLoaded', function() {
            // Set initial theme
            const savedTheme = localStorage.getItem('theme') || 'light';
            if (savedTheme === 'dark') {
                document.body.setAttribute('data-theme', 'dark');
            }

            // Setup navigation
            setupNavigation();
            
            // Initialize system data
            fetchSystemData();
            
            // Show welcome notification
            showNotification('Welcome to Security Chaos Engineering Dashboard!', 'info');
        });
    </script>
</body>
</html>
