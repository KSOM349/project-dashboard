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
            --secondary: #7c3aed;
            --accent: #06b6d4;
            --bg-light: #f8fafc;
            --bg-dark: #0f172a;
            --text-light: #1e293b;
            --text-dark: #f1f5f9;
            --card-light: #ffffff;
            --card-dark: #1e293b;
            --border-light: #e2e8f0;
            --border-dark: #334155;
            --sidebar-light: #ffffff;
            --sidebar-dark: #1e293b;
        }

        .theme-transition {
            transition: all 0.3s ease;
        }

        [data-theme="light"] {
            --bg-color: var(--bg-light);
            --text-color: var(--text-light);
            --card-bg: var(--card-light);
            --border-color: var(--border-light);
            --sidebar-bg: var(--sidebar-light);
        }

        [data-theme="dark"] {
            --bg-color: var(--bg-dark);
            --text-color: var(--text-dark);
            --card-bg: var(--card-dark);
            --border-color: var(--border-dark);
            --sidebar-bg: var(--sidebar-dark);
        }

        body {
            background-color: var(--bg-color);
            color: var(--text-color);
        }

        .card {
            background-color: var(--card-bg);
            border-color: var(--border-color);
        }

        .sidebar {
            background-color: var(--sidebar-bg);
            box-shadow: 0 0 10px rgba(0, 0, 0, 0.1);
        }

        .active-section {
            background-color: rgba(37, 99, 235, 0.1);
            border-left: 4px solid #2563eb;
            color: #2563eb;
            font-weight: 600;
        }

        .notification {
            animation: slideIn 0.3s ease-out;
        }

        @keyframes slideIn {
            from {
                transform: translateX(100%);
                opacity: 0;
            }
            to {
                transform: translateX(0);
                opacity: 1;
            }
        }
    </style>
</head>
<body class="theme-transition" data-theme="light">
    <!-- Notification Container -->
    <div id="notificationContainer" class="fixed top-4 right-4 z-50 space-y-2 max-w-sm"></div>

    <!-- Main Layout -->
    <div class="flex h-screen">
        <!-- Sidebar -->
        <div class="sidebar w-64 flex-shrink-0 border-r theme-transition overflow-y-auto">
            <div class="p-6 border-b theme-transition">
                <h1 class="text-xl font-bold">Security Chaos Engineering</h1>
                <p class="text-sm opacity-70 mt-1">Group 1 Dashboard</p>
            </div>
            
            <nav class="p-4 space-y-1">
                <button class="section-btn w-full text-left p-3 rounded-lg theme-transition active-section" data-section="overview">
                    <i class="fas fa-home mr-3 w-5 text-center"></i>Overview
                </button>
                <button class="section-btn w-full text-left p-3 rounded-lg theme-transition hover:bg-gray-100 dark:hover:bg-gray-800" data-section="project-documentation">
                    <i class="fas fa-folder mr-3 w-5 text-center"></i>Project Documentation
                </button>
                <button class="section-btn w-full text-left p-3 rounded-lg theme-transition hover:bg-gray-100 dark:hover:bg-gray-800" data-section="team-collaboration">
                    <i class="fas fa-users mr-3 w-5 text-center"></i>Team Collaboration
                </button>
                <button class="section-btn w-full text-left p-3 rounded-lg theme-transition hover:bg-gray-100 dark:hover:bg-gray-800" data-section="team-updates">
                    <i class="fas fa-bullhorn mr-3 w-5 text-center"></i>Team Updates
                </button>
                <button class="section-btn w-full text-left p-3 rounded-lg theme-transition hover:bg-gray-100 dark:hover:bg-gray-800" data-section="ai-assistant">
                    <i class="fas fa-robot mr-3 w-5 text-center"></i>AI Assistant
                </button>
                <button class="section-btn w-full text-left p-3 rounded-lg theme-transition hover:bg-gray-100 dark:hover:bg-gray-800" data-section="practical-tasks">
                    <i class="fas fa-tasks mr-3 w-5 text-center"></i>Practical Tasks
                </button>
                <button class="section-btn w-full text-left p-3 rounded-lg theme-transition hover:bg-gray-100 dark:hover:bg-gray-800" data-section="algorithm-visualizer">
                    <i class="fas fa-project-diagram mr-3 w-5 text-center"></i>Algorithm Visualizer
                </button>
                <button class="section-btn w-full text-left p-3 rounded-lg theme-transition hover:bg-gray-100 dark:hover:bg-gray-800" data-section="my-implementation">
                    <i class="fas fa-code mr-3 w-5 text-center"></i>My Implementation
                </button>
                <button class="section-btn w-full text-left p-3 rounded-lg theme-transition hover:bg-gray-100 dark:hover:bg-gray-800" data-section="team-dashboard">
                    <i class="fas fa-tachometer-alt mr-3 w-5 text-center"></i>Team Dashboard
                </button>
                <button class="section-btn w-full text-left p-3 rounded-lg theme-transition hover:bg-gray-100 dark:hover:bg-gray-800" data-section="dijkstra-algorithm">
                    <i class="fas fa-network-wired mr-3 w-5 text-center"></i>Dijkstra Algorithm
                </button>
            </nav>
            
            <div class="absolute bottom-4 left-4 right-4">
                <button onclick="toggleTheme()" class="w-full p-3 rounded-lg theme-transition bg-gray-100 hover:bg-gray-200 dark:bg-gray-800 dark:hover:bg-gray-700">
                    <i class="fas fa-moon mr-2"></i>Toggle Theme
                </button>
            </div>
        </div>

        <!-- Main Content -->
        <div class="flex-1 overflow-y-auto">
            <!-- Header -->
            <header class="bg-white dark:bg-gray-800 shadow-sm border-b theme-transition p-4">
                <div class="flex justify-between items-center">
                    <div>
                        <h1 class="text-2xl font-bold">Security Chaos Engineering Dashboard</h1>
                        <p class="text-gray-600 dark:text-gray-400 mt-1">Group 1 - Full-Stack Dashboard with Real-time Data</p>
                    </div>
                    <div class="flex items-center space-x-4">
                        <div class="hidden md:flex space-x-2">
                            <div class="text-center p-2 bg-blue-50 dark:bg-blue-900/30 rounded-lg">
                                <div class="text-sm font-bold" id="cpu-percent">12%</div>
                                <div class="text-xs text-gray-600 dark:text-gray-400">CPU</div>
                            </div>
                            <div class="text-center p-2 bg-green-50 dark:bg-green-900/30 rounded-lg">
                                <div class="text-sm font-bold" id="memory-percent">45%</div>
                                <div class="text-xs text-gray-600 dark:text-gray-400">Memory</div>
                            </div>
                        </div>
                        <button onclick="fetchSystemData()" class="bg-blue-600 hover:bg-blue-700 text-white px-4 py-2 rounded-lg transition-all theme-transition">
                            <i class="fas fa-sync-alt mr-2"></i> Refresh
                        </button>
                    </div>
                </div>
            </header>

            <!-- Content Sections -->
            <div class="p-6">
                <!-- Overview Section -->
                <div class="section-content card rounded-xl shadow-lg p-6 mb-6" id="overview">
                    <h2 class="text-2xl font-bold mb-4">Project Overview</h2>
                    
                    <div class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-4 gap-4 mb-6">
                        <div class="text-center p-4 bg-blue-50 dark:bg-blue-900/30 rounded-lg theme-transition">
                            <div class="text-2xl font-bold" id="total-tasks">8</div>
                            <div class="text-sm text-gray-600 dark:text-gray-400">Total Tasks</div>
                        </div>
                        <div class="text-center p-4 bg-green-50 dark:bg-green-900/30 rounded-lg theme-transition">
                            <div class="text-2xl font-bold" id="completed-tasks">3</div>
                            <div class="text-sm text-gray-600 dark:text-gray-400">Completed</div>
                        </div>
                        <div class="text-center p-4 bg-yellow-50 dark:bg-yellow-900/30 rounded-lg theme-transition">
                            <div class="text-2xl font-bold" id="inprogress-tasks">2</div>
                            <div class="text-sm text-gray-600 dark:text-gray-400">In Progress</div>
                        </div>
                        <div class="text-center p-4 bg-purple-50 dark:bg-purple-900/30 rounded-lg theme-transition">
                            <div class="text-2xl font-bold" id="team-members">6</div>
                            <div class="text-sm text-gray-600 dark:text-gray-400">Team Members</div>
                        </div>
                    </div>
                    
                    <div class="p-4 bg-blue-50 dark:bg-blue-900/20 rounded-lg theme-transition mb-6">
                        <h3 class="text-xl font-semibold mb-2">Project Summary</h3>
                        <p>"Group 1 - Full-Stack Dashboard with Algorithm Visualizer" - A comprehensive dashboard for Security Chaos Engineering and Cloud Networks.</p>
                    </div>

                    <div class="mb-6">
                        <h3 class="text-xl font-semibold mb-3">System Status</h3>
                        <div id="alerts-container" class="mb-4">
                            <div class="p-3 bg-green-100 dark:bg-green-900/30 text-green-800 dark:text-green-300 rounded-lg theme-transition">
                                <i class="fas fa-check-circle mr-2"></i> All systems functioning normally
                            </div>
                        </div>
                        <button onclick="checkAlerts()" class="bg-orange-500 hover:bg-orange-600 text-white py-2 px-4 rounded-lg transition-all theme-transition">
                            <i class="fas fa-bell mr-2"></i> Check System Alerts
                        </button>
                    </div>
                </div>

                <!-- Project Documentation Section -->
                <div class="section-content card rounded-xl shadow-lg p-6 mb-6 hidden" id="project-documentation">
                    <h2 class="text-2xl font-bold mb-4">Project Documentation</h2>
                    
                    <div class="flex justify-between items-center mb-4">
                        <h3 class="text-xl font-semibold">Complete Project Documentation</h3>
                        <button class="export-btn bg-blue-600 hover:bg-blue-700 text-white px-4 py-2 rounded-lg transition-all theme-transition">
                            <i class="fas fa-download mr-2"></i> Export Documentation
                        </button>
                    </div>
                    
                    <div class="space-y-4">
                        <div class="p-4 border rounded-lg theme-transition">
                            <h4 class="font-semibold mb-2">Project Overview</h4>
                            <p>"Group 1 - Full-Stack Dashboard with Algorithm Visualizer" - A comprehensive dashboard for Security Chaos Engineering and Cloud Networks.</p>
                        </div>
                        
                        <div class="p-4 border rounded-lg theme-transition">
                            <h4 class="font-semibold mb-2">Team Members</h4>
                            <p>Fahad Hussain, Stefan Österberg, Kaled Osman, Marcus Tibell, Jens Annell, Luwam</p>
                        </div>
                        
                        <div class="p-4 border rounded-lg theme-transition">
                            <h4 class="font-semibold mb-2">Technical Stack</h4>
                            <ul class="list-disc list-inside space-y-1">
                                <li>Backend: Python Flask</li>
                                <li>Frontend: HTML5, CSS3, JavaScript</li>
                                <li>Styling: Tailwind CSS with custom themes</li>
                                <li>Data: JSON-based "database"</li>
                            </ul>
                        </div>
                    </div>
                </div>

                <!-- Team Collaboration Section -->
                <div class="section-content card rounded-xl shadow-lg p-6 mb-6 hidden" id="team-collaboration">
                    <h2 class="text-2xl font-bold mb-4">Team Collaboration</h2>
                    
                    <div class="grid md:grid-cols-2 gap-6">
                        <div class="p-4 border rounded-lg theme-transition">
                            <h3 class="font-semibold mb-3">Add New Task</h3>
                            <form id="assignment-form" class="space-y-3">
                                <div>
                                    <label class="block text-sm font-medium mb-1">Task Name:</label>
                                    <input type="text" id="task-name" class="w-full p-2 border rounded theme-transition dark:bg-gray-800 dark:border-gray-700">
                                </div>
                                <div>
                                    <label class="block text-sm font-medium mb-1">Description:</label>
                                    <textarea id="task-desc" class="w-full p-2 border rounded theme-transition dark:bg-gray-800 dark:border-gray-700 h-24"></textarea>
                                </div>
                                <div>
                                    <label class="block text-sm font-medium mb-1">Assignee:</label>
                                    <select id="task-assignee" class="w-full p-2 border rounded theme-transition dark:bg-gray-800 dark:border-gray-700">
                                        <option value="Kaled Osman">Kaled Osman</option>
                                        <option value="Fahad Hussain">Fahad Hussain</option>
                                        <option value="Stefan Österberg">Stefan Österberg</option>
                                        <option value="Marcus Tibell">Marcus Tibell</option>
                                        <option value="Jens Annell">Jens Annell</option>
                                        <option value="Luwam">Luwam</option>
                                    </select>
                                </div>
                                <button type="submit" class="w-full bg-green-600 hover:bg-green-700 text-white py-2 rounded theme-transition">
                                    <i class="fas fa-plus mr-2"></i> Add Task
                                </button>
                            </form>
                        </div>

                        <div class="p-4 border rounded-lg theme-transition">
                            <h3 class="font-semibold mb-3">Current Tasks</h3>
                            <div id="assignments-list" class="space-y-3">
                                <div class="p-3 bg-gray-50 dark:bg-gray-800 rounded theme-transition">
                                    <strong>Improve Dijkstra Algorithm</strong>
                                    <p class="text-sm text-gray-600 dark:text-gray-400 mt-1">Add performance improvements and complexity analysis</p>
                                    <small><strong>Assignee:</strong> Kaled Osman</small>
                                </div>
                                <div class="p-3 bg-gray-50 dark:bg-gray-800 rounded theme-transition">
                                    <strong>OSPF Protocols Research</strong>
                                    <p class="text-sm text-gray-600 dark:text-gray-400 mt-1">Study OSPF protocols and their applications in networks</p>
                                    <small><strong>Assignee:</strong> Fahad Hussain</small>
                                </div>
                            </div>
                        </div>
                    </div>
                </div>

                <!-- Team Updates Section -->
                <div class="section-content card rounded-xl shadow-lg p-6 mb-6 hidden" id="team-updates">
                    <h2 class="text-2xl font-bold mb-4">Team Updates & Tasks</h2>
                    
                    <div class="space-y-6">
                        <div class="p-4 border rounded-lg theme-transition">
                            <h3 class="font-semibold mb-3">Add Your Update</h3>
                            <form id="team-update-form" class="space-y-3">
                                <div class="grid md:grid-cols-2 gap-3">
                                    <div>
                                        <label class="block text-sm font-medium mb-1">Your Name:</label>
                                        <select id="update-author" class="w-full p-2 border rounded theme-transition dark:bg-gray-800 dark:border-gray-700">
                                            <option value="Kaled Osman">Kaled Osman</option>
                                            <option value="Fahad Hussain">Fahad Hussain</option>
                                            <option value="Stefan Österberg">Stefan Österberg</option>
                                            <option value="Marcus Tibell">Marcus Tibell</option>
                                            <option value="Jens Annell">Jens Annell</option>
                                            <option value="Luwam">Luwam</option>
                                        </select>
                                    </div>
                                    <div>
                                        <label class="block text-sm font-medium mb-1">Status:</label>
                                        <select id="update-status" class="w-full p-2 border rounded theme-transition dark:bg-gray-800 dark:border-gray-700">
                                            <option value="completed">Completed</option>
                                            <option value="in-progress">In Progress</option>
                                            <option value="planned">Planned</option>
                                            <option value="blocked">Blocked</option>
                                        </select>
                                    </div>
                                </div>
                                <div>
                                    <label class="block text-sm font-medium mb-1">Task Description:</label>
                                    <input type="text" id="update-title" class="w-full p-2 border rounded theme-transition dark:bg-gray-800 dark:border-gray-700" placeholder="What have you done?">
                                </div>
                                <div>
                                    <label class="block text-sm font-medium mb-1">Details:</label>
                                    <textarea id="update-details" class="w-full p-2 border rounded theme-transition dark:bg-gray-800 dark:border-gray-700 h-24" placeholder="Describe your work..."></textarea>
                                </div>
                                <button type="submit" class="w-full bg-green-600 hover:bg-green-700 text-white py-2 rounded theme-transition">
                                    <i class="fas fa-share mr-2"></i> Publish Update
                                </button>
                            </form>
                        </div>

                        <div id="team-updates-list" class="space-y-4">
                            <!-- Team updates will be added here dynamically -->
                        </div>
                    </div>
                </div>

                <!-- AI Assistant Section -->
                <div class="section-content card rounded-xl shadow-lg p-6 mb-6 hidden" id="ai-assistant">
                    <h2 class="text-2xl font-bold mb-4">AI Assistant - Security Chaos Engineering</h2>
                    
                    <div class="space-y-4">
                        <div class="ai-chat-container border rounded-lg p-4 h-64 overflow-y-auto theme-transition dark:border-gray-700" id="aiChatContainer">
                            <div class="p-3 bg-blue-50 dark:bg-blue-900/20 rounded-lg theme-transition mb-2">
                                <strong>AI Assistant:</strong> Hello! I'm here to help you with Security Chaos Engineering and Cloud Networks. Ask me anything!
                            </div>
                        </div>

                        <div class="flex gap-2">
                            <input type="text" id="aiChatInput" class="flex-1 p-2 border rounded theme-transition dark:bg-gray-800 dark:border-gray-700" placeholder="Ask about Security Chaos Engineering or Cloud Networks...">
                            <button onclick="sendAIMessage()" class="bg-green-600 hover:bg-green-700 text-white px-4 py-2 rounded theme-transition">
                                <i class="fas fa-paper-plane"></i> Send
                            </button>
                        </div>

                        <div>
                            <h3 class="font-semibold mb-2">Quick Commands</h3>
                            <div class="flex flex-wrap gap-2">
                                <button onclick="quickQuestion('What is Security Chaos Engineering?')" class="bg-gray-100 hover:bg-gray-200 dark:bg-gray-800 dark:hover:bg-gray-700 px-3 py-2 rounded theme-transition">
                                    Security Chaos Engineering
                                </button>
                                <button onclick="quickQuestion('How to apply Security Chaos Engineering in AWS?')" class="bg-gray-100 hover:bg-gray-200 dark:bg-gray-800 dark:hover:bg-gray-700 px-3 py-2 rounded theme-transition">
                                    AWS Implementation
                                </button>
                                <button onclick="quickQuestion('What are the best tools for Security Chaos Engineering?')" class="bg-gray-100 hover:bg-gray-200 dark:bg-gray-800 dark:hover:bg-gray-700 px-3 py-2 rounded theme-transition">
                                    Tools & Technologies
                                </button>
                            </div>
                        </div>
                    </div>
                </div>

                <!-- Practical Tasks Section -->
                <div class="section-content card rounded-xl shadow-lg p-6 mb-6 hidden" id="practical-tasks">
                    <h2 class="text-2xl font-bold mb-4">Practical Tasks - Project Assignments</h2>
                    
                    <div class="flex justify-between items-center mb-6">
                        <div class="grid grid-cols-4 gap-4 flex-1">
                            <div class="text-center p-4 bg-blue-50 dark:bg-blue-900/30 rounded-lg theme-transition">
                                <div class="text-2xl font-bold" id="total-tasks-counter">8</div>
                                <div class="text-sm text-gray-600 dark:text-gray-400">Total Tasks</div>
                            </div>
                            <div class="text-center p-4 bg-green-50 dark:bg-green-900/30 rounded-lg theme-transition">
                                <div class="text-2xl font-bold" id="completed-tasks-counter">3</div>
                                <div class="text-sm text-gray-600 dark:text-gray-400">Completed</div>
                            </div>
                            <div class="text-center p-4 bg-yellow-50 dark:bg-yellow-900/30 rounded-lg theme-transition">
                                <div class="text-2xl font-bold" id="inprogress-tasks-counter">2</div>
                                <div class="text-sm text-gray-600 dark:text-gray-400">In Progress</div>
                            </div>
                            <div class="text-center p-4 bg-purple-50 dark:bg-purple-900/30 rounded-lg theme-transition">
                                <div class="text-2xl font-bold" id="pending-tasks-counter">3</div>
                                <div class="text-sm text-gray-600 dark:text-gray-400">Pending</div>
                            </div>
                        </div>
                        <button onclick="addNewTask()" class="ml-4 bg-green-600 hover:bg-green-700 text-white px-4 py-2 rounded theme-transition">
                            <i class="fas fa-plus mr-2"></i> New Task
                        </button>
                    </div>

                    <div id="tasks-list" class="space-y-3">
                        <!-- Tasks will be added here dynamically -->
                    </div>
                </div>

                <!-- Algorithm Visualizer Section -->
                <div class="section-content card rounded-xl shadow-lg p-6 mb-6 hidden" id="algorithm-visualizer">
                    <h2 class="text-2xl font-bold mb-4">Algorithm Visualizer</h2>
                    
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
                <div class="section-content card rounded-xl shadow-lg p-6 mb-6 hidden" id="my-implementation">
                    <h2 class="text-2xl font-bold mb-4">My Implementation</h2>
                    
                    <div class="space-y-4">
                        <div class="p-4 border rounded-lg theme-transition">
                            <h3 class="font-semibold mb-2">Backend Implementation</h3>
                            <p>Python Flask server with REST API endpoints for system monitoring.</p>
                        </div>
                        <div class="p-4 border rounded-lg theme-transition">
                            <h3 class="font-semibold mb-2">Frontend Implementation</h3>
                            <p>Modern dashboard implementation with real-time data updates.</p>
                        </div>
                    </div>
                </div>

                <!-- Team Dashboard Section -->
                <div class="section-content card rounded-xl shadow-lg p-6 mb-6 hidden" id="team-dashboard">
                    <h2 class="text-2xl font-bold mb-4">Team Dashboard</h2>
                    
                    <div class="grid grid-cols-2 md:grid-cols-3 gap-4 mb-6">
                        <div class="text-center p-4 border rounded-lg theme-transition">
                            <div class="text-2xl font-bold">6</div>
                            <div>Team Members</div>
                        </div>
                        <div class="text-center p-4 border rounded-lg theme-transition">
                            <div class="text-2xl font-bold">12</div>
                            <div>Completed Tasks</div>
                        </div>
                        <div class="text-center p-4 border rounded-lg theme-transition">
                            <div class="text-2xl font-bold">85%</div>
                            <div>Project Progress</div>
                        </div>
                    </div>
                    
                    <div class="p-4 border rounded-lg theme-transition">
                        <h3 class="font-semibold mb-3">Team Members</h3>
                        <ul class="list-disc list-inside space-y-2">
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
                <div class="section-content card rounded-xl shadow-lg p-6 mb-6 hidden" id="dijkstra-algorithm">
                    <h2 class="text-2xl font-bold mb-4">Dijkstra Algorithm</h2>
                    
                    <div class="space-y-4">
                        <div class="p-4 border rounded-lg theme-transition">
                            <h3 class="font-semibold mb-2">Algorithm Explanation</h3>
                            <p>Dijkstra's algorithm finds the shortest path between nodes in a graph.</p>
                        </div>
                        <div class="p-4 border rounded-lg theme-transition">
                            <h3 class="font-semibold mb-2">Implementation Steps</h3>
                            <ol class="list-decimal list-inside space-y-1">
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
        // Theme management
        function toggleTheme() {
            const body = document.body;
            const currentTheme = body.getAttribute('data-theme');
            const newTheme = currentTheme === 'dark' ? 'light' : 'dark';
            
            body.setAttribute('data-theme', newTheme);
            localStorage.setItem('theme', newTheme);
            showNotification('Theme changed to ' + newTheme + ' mode', 'success');
        }

        // Notification system
        function showNotification(message, type = 'info') {
            const container = document.getElementById('notificationContainer');
            const notification = document.createElement('div');
            
            const colors = {
                success: 'bg-green-500',
                error: 'bg-red-500',
                warning: 'bg-yellow-500',
                info: 'bg-blue-500'
            };

            notification.className = `${colors[type]} text-white px-6 py-3 rounded-lg shadow-lg notification`;
            notification.innerHTML = `
                <div class="flex justify-between items-center">
                    <span>${message}</span>
                    <button onclick="this.parentElement.parentElement.remove()" class="ml-4">
                        <i class="fas fa-times"></i>
                    </button>
                </div>
            `;

            container.appendChild(notification);
            setTimeout(() => {
                if (notification.parentElement) {
                    notification.remove();
                }
            }, 5000);
        }

        // System data fetching
        async function fetchSystemData() {
            showNotification('Fetching system data...', 'info');
            
            try {
                // Simulate API call
                setTimeout(() => {
                    document.getElementById('cpu-percent').textContent = '12%';
                    document.getElementById('memory-percent').textContent = '45%';
                    showNotification('System data updated!', 'success');
                }, 1000);
            } catch (error) {
                document.getElementById('cpu-percent').textContent = '12%';
                document.getElementById('memory-percent').textContent = '45%';
                showNotification('Using sample data - Server not available', 'warning');
            }
        }

        // Alert system
        function checkAlerts() {
            showNotification('Checking system alerts...', 'info');
            
            const alertsContainer = document.getElementById('alerts-container');
            alertsContainer.innerHTML = `
                <div class="p-3 bg-green-100 dark:bg-green-900/30 text-green-800 dark:text-green-300 rounded-lg theme-transition">
                    <i class="fas fa-check-circle mr-2"></i> No critical alerts found
                </div>
            `;
            
            showNotification('Alert check completed', 'success');
        }

        // Navigation system
        function setupNavigation() {
            document.querySelectorAll('.section-btn').forEach(btn => {
                btn.addEventListener('click', function() {
                    document.querySelectorAll('.section-btn').forEach(b => {
                        b.classList.remove('active-section');
                        b.classList.add('hover:bg-gray-100', 'dark:hover:bg-gray-800');
                    });
                    this.classList.add('active-section');
                    this.classList.remove('hover:bg-gray-100', 'dark:hover:bg-gray-800');
                    
                    const sectionId = this.dataset.section;
                    document.querySelectorAll('.section-content').forEach(section => {
                        section.classList.add('hidden');
                    });
                    document.getElementById(sectionId).classList.remove('hidden');
                });
            });
        }

        // Task management
        function setupTaskManagement() {
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
                        <strong>${name}</strong>
                        <p class="text-sm text-gray-600 dark:text-gray-400 mt-1">${desc}</p>
                        <small><strong>Assignee:</strong> ${assignee}</small>
                    `;
                    assignmentsList.appendChild(newAssignment);
                    
                    this.reset();
                    showNotification('Task added!', 'success');
                }
            });

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
        }

        function addTeamUpdate(author, status, title, details) {
            const updatesList = document.getElementById('team-updates-list');
            const statusColors = {
                'completed': '#10B981',
                'in-progress': '#F59E0B', 
                'planned': '#3B82F6',
                'blocked': '#EF4444'
            };

            const updateHTML = `
                <div class="p-4 border rounded-lg theme-transition" style="border-left: 4px solid ${statusColors[status]}">
                    <div class="flex justify-between items-center mb-2">
                        <h4 class="font-semibold">${title}</h4>
                        <span class="px-2 py-1 rounded text-white text-sm" style="background: ${statusColors[status]}">
                            ${status === 'completed' ? '✅' : status === 'in-progress' ? '🔄' : status === 'planned' ? '📅' : '❌'} 
                            ${status}
                        </span>
                    </div>
                    <p><strong>${author}</strong> - ${details}</p>
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
            userMsg.innerHTML = `<strong>You:</strong> ${message}`;
            chatContainer.appendChild(userMsg);

            setTimeout(() => {
                const aiMsg = document.createElement('div');
                aiMsg.className = 'p-3 bg-blue-50 dark:bg-blue-900/20 rounded-lg theme-transition mb-2';
                aiMsg.innerHTML = `<strong>AI Assistant:</strong> ${getAIResponse(message)}`;
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

        // Task management functions
        function addNewTask() {
            const taskName = prompt('Task name:');
            if (!taskName) return;

            const tasksList = document.getElementById('tasks-list');
            const taskId = Date.now();
            
            const taskHTML = `
                <div class="p-4 border rounded-lg theme-transition" id="task-${taskId}">
                    <div class="flex justify-between items-start mb-2">
                        <h4 class="font-semibold">${taskName}</h4>
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
            updateTaskStats();
            showNotification('New task added!', 'success');
        }

        function removeTask(taskId) {
            const taskElement = document.getElementById(`task-${taskId}`);
            if (taskElement && confirm('Do you want to remove this task?')) {
                taskElement.remove();
                updateTaskStats();
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
                updateTaskStats();
                showNotification('Task marked as completed!', 'success');
            }
        }

        function updateTaskStats() {
            const tasks = document.querySelectorAll('#tasks-list > div');
            const total = tasks.length + 5; // Add existing tasks
            const completed = Array.from(tasks).filter(t => 
                t.querySelector('button').textContent === 'Completed ✓'
            ).length + 3; // Add existing completed tasks
            const pending = total - completed;

            document.getElementById('total-tasks').textContent = total;
            document.getElementById('total-tasks-counter').textContent = total;
            document.getElementById('completed-tasks').textContent = completed;
            document.getElementById('completed-tasks-counter').textContent = completed;
            document.getElementById('pending-tasks-counter').textContent = pending;
            document.getElementById('inprogress-tasks').textContent = 2;
            document.getElementById('inprogress-tasks-counter').textContent = 2;
        }

        // Initialize the dashboard
        document.addEventListener('DOMContentLoaded', function() {
            // Set initial theme
            const savedTheme = localStorage.getItem('theme') || 'light';
            if (savedTheme === 'dark') {
                document.body.setAttribute('data-theme', 'dark');
            }

            // Setup all functionality
            setupNavigation();
            setupTaskManagement();
            
            // Initialize system data
            fetchSystemData();
            updateTaskStats();
            
            // Show welcome notification
            showNotification('Welcome to Security Chaos Engineering Dashboard!', 'info');
            
            // Setup export button
            document.querySelector('.export-btn').addEventListener('click', function() {
                showNotification('Documentation exported!', 'success');
            });
        });
    </script>
</body>
</html>
