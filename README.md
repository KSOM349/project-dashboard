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

                <!-- Team Updates Section -->
                <div class="section-content card p-8 mb-8 rounded-2xl shadow-lg hidden" id="team-updates">
                    <h2 class="text-3xl font-bold mb-6 text-gray-800 dark:text-white">Team Updates & Tasks</h2>
                    
                    <div class="space-y-8">
                        <div class="p-6 border border-gray-200 dark:border-gray-700 rounded-2xl bg-white dark:bg-gray-800">
                            <h3 class="text-xl font-semibold mb-4 text-gray-800 dark:text-white">Add Your Update</h3>
                            <form id="team-update-form" class="space-y-4">
                                <div class="grid md:grid-cols-2 gap-4">
                                    <div>
                                        <label class="block text-sm font-medium mb-2 text-gray-700 dark:text-gray-300">Your Name:</label>
                                        <select class="w-full p-3 border border-gray-300 dark:border-gray-600 rounded-xl focus:ring-2 focus:ring-blue-500 focus:border-blue-500 dark:bg-gray-700 dark:text-white">
                                            <option>Kaled Osman</option>
                                            <option>Fahad Hussain</option>
                                            <option>Stefan Österberg</option>
                                            <option>Marcus Tibell</option>
                                            <option>Jens Annell</option>
                                            <option>Luwam</option>
                                        </select>
                                    </div>
                                    <div>
                                        <label class="block text-sm font-medium mb-2 text-gray-700 dark:text-gray-300">Status:</label>
                                        <select class="w-full p-3 border border-gray-300 dark:border-gray-600 rounded-xl focus:ring-2 focus:ring-blue-500 focus:border-blue-500 dark:bg-gray-700 dark:text-white">
                                            <option>Completed</option>
                                            <option>In Progress</option>
                                            <option>Planned</option>
                                            <option>Blocked</option>
                                        </select>
                                    </div>
                                </div>
                                <div>
                                    <label class="block text-sm font-medium mb-2 text-gray-700 dark:text-gray-300">Task Description:</label>
                                    <input type="text" class="w-full p-3 border border-gray-300 dark:border-gray-600 rounded-xl focus:ring-2 focus:ring-blue-500 focus:border-blue-500 dark:bg-gray-700 dark:text-white" placeholder="What have you done?">
                                </div>
                                <div>
                                    <label class="block text-sm font-medium mb-2 text-gray-700 dark:text-gray-300">Details:</label>
                                    <textarea class="w-full p-3 border border-gray-300 dark:border-gray-600 rounded-xl focus:ring-2 focus:ring-blue-500 focus:border-blue-500 dark:bg-gray-700 dark:text-white h-32" placeholder="Describe your work..."></textarea>
                                </div>
                                <button type="submit" class="w-full bg-green-600 hover:bg-green-700 text-white py-4 rounded-xl transition-all shadow-lg font-medium">
                                    <i class="fas fa-share mr-3"></i> Publish Update
                                </button>
                            </form>
                        </div>
                    </div>
                </div>

                <!-- Project Documentation Section -->
                <div class="section-content card p-8 mb-8 rounded-2xl shadow-lg hidden" id="project-documentation">
                    <h2 class="text-3xl font-bold mb-6 text-gray-800 dark:text-white">Project Documentation</h2>
                    <div class="space-y-4">
                        <div class="p-4 border border-gray-200 dark:border-gray-700 rounded-xl">
                            <h3 class="font-semibold mb-2 text-gray-800 dark:text-white">Complete Project Documentation</h3>
                            <p class="text-gray-600 dark:text-gray-400">Detailed documentation about the Security Chaos Engineering project.</p>
                        </div>
                    </div>
                </div>

                <!-- Team Collaboration Section -->
                <div class="section-content card p-8 mb-8 rounded-2xl shadow-lg hidden" id="team-collaboration">
                    <h2 class="text-3xl font-bold mb-6 text-gray-800 dark:text-white">Team Collaboration</h2>
                    <div class="space-y-4">
                        <div class="p-4 border border-gray-200 dark:border-gray-700 rounded-xl">
                            <h3 class="font-semibold mb-2 text-gray-800 dark:text-white">Team Tasks & Assignments</h3>
                            <p class="text-gray-600 dark:text-gray-400">Collaborate with your team members on various tasks.</p>
                        </div>
                    </div>
                </div>

                <!-- AI Assistant Section -->
                <div class="section-content card p-8 mb-8 rounded-2xl shadow-lg hidden" id="ai-assistant">
                    <h2 class="text-3xl font-bold mb-6 text-gray-800 dark:text-white">AI Assistant</h2>
                    <div class="space-y-4">
                        <div class="p-4 border border-gray-200 dark:border-gray-700 rounded-xl">
                            <h3 class="font-semibold mb-2 text-gray-800 dark:text-white">Security Chaos Engineering Assistant</h3>
                            <p class="text-gray-600 dark:text-gray-400">Get help with Security Chaos Engineering concepts and implementations.</p>
                        </div>
                    </div>
                </div>

                <!-- Practical Tasks Section -->
                <div class="section-content card p-8 mb-8 rounded-2xl shadow-lg hidden" id="practical-tasks">
                    <h2 class="text-3xl font-bold mb-6 text-gray-800 dark:text-white">Practical Tasks</h2>
                    <div class="space-y-4">
                        <div class="p-4 border border-gray-200 dark:border-gray-700 rounded-xl">
                            <h3 class="font-semibold mb-2 text-gray-800 dark:text-white">Project Tasks & Assignments</h3>
                            <p class="text-gray-600 dark:text-gray-400">Manage and track your project tasks.</p>
                        </div>
                    </div>
                </div>

                <!-- Algorithm Visualizer Section -->
                <div class="section-content card p-8 mb-8 rounded-2xl shadow-lg hidden" id="algorithm-visualizer">
                    <h2 class="text-3xl font-bold mb-6 text-gray-800 dark:text-white">Algorithm Visualizer</h2>
                    <div class="space-y-4">
                        <div class="p-4 border border-gray-200 dark:border-gray-700 rounded-xl">
                            <h3 class="font-semibold mb-2 text-gray-800 dark:text-white">Dijkstra Algorithm Visualization</h3>
                            <p class="text-gray-600 dark:text-gray-400">Visualize and understand the Dijkstra algorithm.</p>
                        </div>
                    </div>
                </div>

                <!-- My Implementation Section -->
                <div class="section-content card p-8 mb-8 rounded-2xl shadow-lg hidden" id="my-implementation">
                    <h2 class="text-3xl font-bold mb-6 text-gray-800 dark:text-white">My Implementation</h2>
                    <div class="space-y-4">
                        <div class="p-4 border border-gray-200 dark:border-gray-700 rounded-xl">
                            <h3 class="font-semibold mb-2 text-gray-800 dark:text-white">Personal Contributions</h3>
                            <p class="text-gray-600 dark:text-gray-400">Track your personal contributions to the project.</p>
                        </div>
                    </div>
                </div>

                <!-- Team Dashboard Section -->
                <div class="section-content card p-8 mb-8 rounded-2xl shadow-lg hidden" id="team-dashboard">
                    <h2 class="text-3xl font-bold mb-6 text-gray-800 dark:text-white">Team Dashboard</h2>
                    <div class="space-y-4">
                        <div class="p-4 border border-gray-200 dark:border-gray-700 rounded-xl">
                            <h3 class="font-semibold mb-2 text-gray-800 dark:text-white">Team Performance & Metrics</h3>
                            <p class="text-gray-600 dark:text-gray-400">Monitor team performance and project metrics.</p>
                        </div>
                    </div>
                </div>

                <!-- Dijkstra Algorithm Section -->
                <div class="section-content card p-8 mb-8 rounded-2xl shadow-lg hidden" id="dijkstra-algorithm">
                    <h2 class="text-3xl font-bold mb-6 text-gray-800 dark:text-white">Dijkstra Algorithm</h2>
                    <div class="space-y-4">
                        <div class="p-4 border border-gray-200 dark:border-gray-700 rounded-xl">
                            <h3 class="font-semibold mb-2 text-gray-800 dark:text-white">Algorithm Details & Implementation</h3>
                            <p class="text-gray-600 dark:text-gray-400">Learn about the Dijkstra algorithm and its implementation.</p>
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
            
            // Update all text colors for better readability
            updateTextColors(newTheme);
            
            showNotification('Theme changed to ' + newTheme + ' mode', 'success');
        }

        // Update text colors for better readability
        function updateTextColors(theme) {
            const textElements = document.querySelectorAll('.text-gray-600, .text-gray-700, .text-gray-800');
            textElements.forEach(element => {
                if (theme === 'dark') {
                    element.classList.remove('text-gray-600', 'text-gray-700', 'text-gray-800');
                    element.classList.add('text-gray-300');
                } else {
                    element.classList.remove('text-gray-300');
                    // Restore original classes based on content type
                    if (element.classList.contains('original-gray-600')) {
                        element.classList.add('text-gray-600');
                    } else if (element.classList.contains('original-gray-700')) {
                        element.classList.add('text-gray-700');
                    } else {
                        element.classList.add('text-gray-800');
                    }
                }
            });
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

        // Team updates functionality
        document.getElementById('team-update-form').addEventListener('submit', function(e) {
            e.preventDefault();
            showNotification('Update published successfully!', 'success');
            this.reset();
        });

        // Login form handling
        document.getElementById('loginForm').addEventListener('submit', function(e) {
            e.preventDefault();
            showNotification('Login successful! Welcome to the team dashboard.', 'success');
            closeLogin();
        });

        // Store original text classes for light mode
        function storeOriginalTextClasses() {
            const textElements = document.querySelectorAll('.text-gray-600, .text-gray-700, .text-gray-800');
            textElements.forEach(element => {
                if (element.classList.contains('text-gray-600')) {
                    element.classList.add('original-gray-600');
                } else if (element.classList.contains('text-gray-700')) {
                    element.classList.add('original-gray-700');
                } else if (element.classList.contains('text-gray-800')) {
                    element.classList.add('original-gray-800');
                }
            });
        }

        // Initialize the dashboard
        document.addEventListener('DOMContentLoaded', function() {
            // Store original text classes
            storeOriginalTextClasses();
            
            // Set initial theme
            const savedTheme = localStorage.getItem('theme') || 'light';
            if (savedTheme === 'dark') {
                document.body.setAttribute('data-theme', 'dark');
                updateTextColors('dark');
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
