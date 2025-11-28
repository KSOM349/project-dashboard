<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Security Chaos Engineering Dashboard - Realtime</title>
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

        .login-modal, .add-content-modal, .register-modal, .forgot-password-modal, .user-management-modal, .team-chat-modal {
            display: none;
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background: rgba(0, 0, 0, 0.5);
            z-index: 1000;
        }

        .login-modal.active, .add-content-modal.active, .register-modal.active, .forgot-password-modal.active, .user-management-modal.active, .team-chat-modal.active {
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

        .user-role-admin {
            background: linear-gradient(135deg, #f59e0b, #d97706);
            color: white;
        }

        .user-role-user {
            background: linear-gradient(135deg, #10b981, #059669);
            color: white;
        }

        /* NEW: Realtime Features */
        .online-indicator {
            width: 8px;
            height: 8px;
            background: #10B981;
            border-radius: 50%;
            display: inline-block;
            margin-right: 8px;
            animation: pulse 2s infinite;
        }

        @keyframes pulse {
            0% { opacity: 1; }
            50% { opacity: 0.5; }
            100% { opacity: 1; }
        }

        .realtime-notification {
            position: fixed;
            top: 20px;
            right: 20px;
            background: white;
            border-left: 4px solid #3B82F6;
            padding: 12px 16px;
            border-radius: 8px;
            box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
            z-index: 1000;
            animation: slideIn 0.3s ease;
        }

        @keyframes slideIn {
            from { transform: translateX(100%); }
            to { transform: translateX(0); }
        }

        .chat-message {
            margin: 8px 0;
            padding: 8px 12px;
            border-radius: 12px;
            max-width: 70%;
        }

        .chat-message.own {
            background: #3B82F6;
            color: white;
            margin-left: auto;
        }

        .chat-message.other {
            background: #F3F4F6;
            color: #1F2937;
        }

        .live-activity {
            border-left: 3px solid #10B981;
            padding-left: 12px;
            margin: 4px 0;
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
                    <input type="text" id="loginUsername" class="w-full p-3 border border-gray-300 dark:border-gray-600 rounded-xl focus:ring-2 focus:ring-blue-500 focus:border-blue-500 dark:bg-gray-700 dark:text-white" required>
                </div>
                <div>
                    <label class="block text-sm font-medium mb-2 text-gray-700 dark:text-gray-300">Password</label>
                    <input type="password" id="loginPassword" class="w-full p-3 border border-gray-300 dark:border-gray-600 rounded-xl focus:ring-2 focus:ring-blue-500 focus:border-blue-500 dark:bg-gray-700 dark:text-white" required>
                </div>
                <button type="submit" class="w-full bg-blue-600 hover:bg-blue-700 text-white py-3 rounded-xl transition-all font-medium">
                    Sign In
                </button>
            </form>
            <div class="mt-4 text-center">
                <button onclick="showRegister()" class="text-blue-600 hover:text-blue-800 dark:text-blue-400 dark:hover:text-blue-300 text-sm mr-2">
                    Create Account
                </button>
                <button onclick="showForgotPassword()" class="text-blue-600 hover:text-blue-800 dark:text-blue-400 dark:hover:text-blue-300 text-sm">
                    Forgot Password?
                </button>
            </div>
            <button onclick="closeLogin()" class="absolute top-4 right-4 text-gray-500 hover:text-gray-700 dark:text-gray-400 dark:hover:text-gray-200">
                <i class="fas fa-times text-xl"></i>
            </button>
        </div>
    </div>

    <!-- Register Modal -->
    <div class="register-modal" id="registerModal">
        <div class="bg-white dark:bg-gray-800 p-8 rounded-2xl max-w-md w-full mx-4 shadow-2xl">
            <h2 class="text-2xl font-bold mb-6 text-center text-gray-800 dark:text-white">Create Account</h2>
            <form id="registerForm" class="space-y-4">
                <div>
                    <label class="block text-sm font-medium mb-2 text-gray-700 dark:text-gray-300">Full Name</label>
                    <input type="text" id="registerName" class="w-full p-3 border border-gray-300 dark:border-gray-600 rounded-xl focus:ring-2 focus:ring-blue-500 focus:border-blue-500 dark:bg-gray-700 dark:text-white" required>
                </div>
                <div>
                    <label class="block text-sm font-medium mb-2 text-gray-700 dark:text-gray-300">Username</label>
                    <input type="text" id="registerUsername" class="w-full p-3 border border-gray-300 dark:border-gray-600 rounded-xl focus:ring-2 focus:ring-blue-500 focus:border-blue-500 dark:bg-gray-700 dark:text-white" required>
                </div>
                <div>
                    <label class="block text-sm font-medium mb-2 text-gray-700 dark:text-gray-300">Email</label>
                    <input type="email" id="registerEmail" class="w-full p-3 border border-gray-300 dark:border-gray-600 rounded-xl focus:ring-2 focus:ring-blue-500 focus:border-blue-500 dark:bg-gray-700 dark:text-white" required>
                </div>
                <div>
                    <label class="block text-sm font-medium mb-2 text-gray-700 dark:text-gray-300">Password</label>
                    <input type="password" id="registerPassword" class="w-full p-3 border border-gray-300 dark:border-gray-600 rounded-xl focus:ring-2 focus:ring-blue-500 focus:border-blue-500 dark:bg-gray-700 dark:text-white" required>
                </div>
                <div>
                    <label class="block text-sm font-medium mb-2 text-gray-700 dark:text-gray-300">Confirm Password</label>
                    <input type="password" id="registerConfirmPassword" class="w-full p-3 border border-gray-300 dark:border-gray-600 rounded-xl focus:ring-2 focus:ring-blue-500 focus:border-blue-500 dark:bg-gray-700 dark:text-white" required>
                </div>
                <button type="submit" class="w-full bg-green-600 hover:bg-green-700 text-white py-3 rounded-xl transition-all font-medium">
                    Create Account
                </button>
            </form>
            <div class="mt-4 text-center">
                <button onclick="showLogin()" class="text-blue-600 hover:text-blue-800 dark:text-blue-400 dark:hover:text-blue-300 text-sm">
                    Already have an account? Sign in
                </button>
            </div>
            <button onclick="closeRegister()" class="absolute top-4 right-4 text-gray-500 hover:text-gray-700 dark:text-gray-400 dark:hover:text-gray-200">
                <i class="fas fa-times text-xl"></i>
            </button>
        </div>
    </div>

    <!-- Forgot Password Modal -->
    <div class="forgot-password-modal" id="forgotPasswordModal">
        <div class="bg-white dark:bg-gray-800 p-8 rounded-2xl max-w-md w-full mx-4 shadow-2xl">
            <h2 class="text-2xl font-bold mb-6 text-center text-gray-800 dark:text-white">Reset Password</h2>
            <form id="forgotPasswordForm" class="space-y-4">
                <div>
                    <label class="block text-sm font-medium mb-2 text-gray-700 dark:text-gray-300">Email Address</label>
                    <input type="email" id="resetEmail" class="w-full p-3 border border-gray-300 dark:border-gray-600 rounded-xl focus:ring-2 focus:ring-blue-500 focus:border-blue-500 dark:bg-gray-700 dark:text-white" required>
                </div>
                <button type="submit" class="w-full bg-purple-600 hover:bg-purple-700 text-white py-3 rounded-xl transition-all font-medium">
                    Send Reset Link
                </button>
            </form>
            <div class="mt-4 text-center">
                <button onclick="showLogin()" class="text-blue-600 hover:text-blue-800 dark:text-blue-400 dark:hover:text-blue-300 text-sm">
                    Back to Login
                </button>
            </div>
            <button onclick="closeForgotPassword()" class="absolute top-4 right-4 text-gray-500 hover:text-gray-700 dark:text-gray-400 dark:hover:text-gray-200">
                <i class="fas fa-times text-xl"></i>
            </button>
        </div>
    </div>

    <!-- User Management Modal -->
    <div class="user-management-modal" id="userManagementModal">
        <div class="bg-white dark:bg-gray-800 p-8 rounded-2xl max-w-4xl w-full mx-4 shadow-2xl max-h-[80vh] overflow-y-auto">
            <h2 class="text-2xl font-bold mb-6 text-center text-gray-800 dark:text-white">User Management</h2>
            <div class="space-y-4">
                <div id="usersList" class="space-y-3">
                    <!-- Users will be displayed here -->
                </div>
            </div>
            <button onclick="closeUserManagement()" class="absolute top-4 right-4 text-gray-500 hover:text-gray-700 dark:text-gray-400 dark:hover:text-gray-200">
                <i class="fas fa-times text-xl"></i>
            </button>
        </div>
    </div>

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

    <!-- NEW: Team Chat Modal -->
    <div class="team-chat-modal" id="teamChatModal">
        <div class="bg-white dark:bg-gray-800 p-6 rounded-2xl max-w-2xl w-full mx-4 shadow-2xl h-[80vh] flex flex-col">
            <div class="flex justify-between items-center mb-4">
                <h2 class="text-2xl font-bold text-gray-800 dark:text-white">Team Chat</h2>
                <button onclick="closeTeamChat()" class="text-gray-500 hover:text-gray-700 dark:text-gray-400 dark:hover:text-gray-200">
                    <i class="fas fa-times text-xl"></i>
                </button>
            </div>
            
            <!-- Online Users -->
            <div class="mb-4 p-3 bg-gray-50 dark:bg-gray-700 rounded-lg">
                <h3 class="font-semibold mb-2 text-gray-800 dark:text-white">Online Now</h3>
                <div id="onlineUsersList" class="flex flex-wrap gap-2">
                    <!-- Online users will appear here -->
                </div>
            </div>

            <!-- Chat Messages -->
            <div id="chatMessages" class="flex-1 overflow-y-auto mb-4 space-y-2 p-2 border border-gray-200 dark:border-gray-600 rounded-lg">
                <div class="text-center text-gray-500 dark:text-gray-400 text-sm">
                    Start chatting with your team...
                </div>
            </div>

            <!-- Chat Input -->
            <div class="flex gap-2">
                <input type="text" id="chatInput" placeholder="Type your message..." 
                       class="flex-1 p-3 border border-gray-300 dark:border-gray-600 rounded-lg focus:ring-2 focus:ring-blue-500 dark:bg-gray-700 dark:text-white">
                <button onclick="sendChatMessage()" class="bg-blue-600 hover:bg-blue-700 text-white px-6 py-3 rounded-lg transition-all">
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
                
                <!-- NEW: Realtime Status -->
                <div id="realtimeStatus" class="mt-2 text-xs text-green-600 dark:text-green-400 hidden">
                    <i class="fas fa-circle mr-1"></i> Live Updates Active
                </div>
            </div>
            
            <!-- Login/User Info Section -->
            <div id="loginSection" class="w-full mb-6">
                <button onclick="openLogin()" class="w-full bg-blue-600 hover:bg-blue-700 text-white py-3 rounded-xl transition-all shadow-lg font-medium">
                    <i class="fas fa-sign-in-alt mr-3"></i>Team Login
                </button>
            </div>

            <div id="userSection" class="w-full mb-6 hidden">
                <div class="bg-white dark:bg-gray-800 p-4 rounded-xl shadow-lg">
                    <div class="flex items-center space-x-3 mb-3">
                        <div class="w-10 h-10 bg-gradient-to-r from-blue-500 to-purple-500 rounded-full flex items-center justify-center text-white font-bold" id="userAvatar">
                            U
                        </div>
                        <div class="flex-1 min-w-0">
                            <p class="font-semibold text-gray-800 dark:text-white truncate" id="userName">User</p>
                            <p class="text-sm text-gray-600 dark:text-gray-400 truncate" id="userRole">Role</p>
                        </div>
                    </div>
                    <div class="space-y-2">
                        <button onclick="openUserManagement()" id="adminButton" class="w-full bg-yellow-600 hover:bg-yellow-700 text-white py-2 rounded-lg transition-all text-sm hidden">
                            <i class="fas fa-users-cog mr-2"></i>Manage Users
                        </button>
                        <button onclick="openTeamChat()" class="w-full bg-green-600 hover:bg-green-700 text-white py-2 rounded-lg transition-all text-sm">
                            <i class="fas fa-comments mr-2"></i>Team Chat
                        </button>
                        <button onclick="logout()" class="w-full bg-red-600 hover:bg-red-700 text-white py-2 rounded-lg transition-all text-sm">
                            <i class="fas fa-sign-out-alt mr-2"></i>Logout
                        </button>
                    </div>
                </div>
            </div>

            <!-- NEW: Live Team Activity -->
            <div id="liveTeamSection" class="mb-6 hidden">
                <div class="bg-white dark:bg-gray-800 p-4 rounded-xl shadow-lg">
                    <h3 class="font-semibold mb-3 text-gray-800 dark:text-white flex items-center">
                        <span class="online-indicator"></span>
                        Live Team
                    </h3>
                    <div id="liveTeamList" class="space-y-2">
                        <!-- Online team members will appear here -->
                    </div>
                </div>
            </div>

            <!-- Navigation -->
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
                            Please login to access all features
                        </div>
                        <!-- NEW: Live Activity Indicator -->
                        <div id="liveActivity" class="hidden">
                            <div class="flex items-center space-x-2 bg-white/20 px-3 py-1 rounded-full">
                                <span class="online-indicator"></span>
                                <span class="text-sm">Live</span>
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
                    
                    <!-- NEW: Live Activity Feed -->
                    <div id="liveActivityFeed" class="mb-6 p-4 bg-blue-50 dark:bg-blue-900/20 rounded-xl hidden">
                        <h3 class="font-semibold mb-3 text-gray-800 dark:text-white">Live Team Activity</h3>
                        <div id="activityList" class="space-y-2">
                            <!-- Live activities will appear here -->
                        </div>
                    </div>
                    
                    <div id="overview-content" class="space-y-4 mb-8"></div>
                    
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

                <!-- Other sections -->
                <div class="section-content card p-8 mb-8 rounded-2xl shadow-lg hidden" id="project-documentation">
                    <div class="flex justify-between items-center mb-6">
                        <h2 class="text-3xl font-bold text-gray-800 dark:text-white">Project Documentation</h2>
                        <button onclick="openAddContent('project-documentation')" class="bg-green-600 hover:bg-green-700 text-white px-4 py-2 rounded-xl transition-all">
                            <i class="fas fa-plus mr-2"></i>Add Content
                        </button>
                    </div>
                    <div id="project-documentation-content" class="space-y-4 mb-8"></div>
                </div>

                <div class="section-content card p-8 mb-8 rounded-2xl shadow-lg hidden" id="team-collaboration">
                    <div class="flex justify-between items-center mb-6">
                        <h2 class="text-3xl font-bold text-gray-800 dark:text-white">Team Collaboration</h2>
                        <button onclick="openAddContent('team-collaboration')" class="bg-green-600 hover:bg-green-700 text-white px-4 py-2 rounded-xl transition-all">
                            <i class="fas fa-plus mr-2"></i>Add Content
                        </button>
                    </div>
                    <div id="team-collaboration-content" class="space-y-4 mb-8"></div>
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
                            </div>
                        </div>
                    </div>
                </div>

                <div class="section-content card p-8 mb-8 rounded-2xl shadow-lg hidden" id="team-updates">
                    <div class="flex justify-between items-center mb-6">
                        <h2 class="text-3xl font-bold text-gray-800 dark:text-white">Team Updates</h2>
                        <button onclick="openAddContent('team-updates')" class="bg-green-600 hover:bg-green-700 text-white px-4 py-2 rounded-xl transition-all">
                            <i class="fas fa-plus mr-2"></i>Add Content
                        </button>
                    </div>
                    <div id="team-updates-content" class="space-y-4 mb-8"></div>
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
                        <div id="team-updates-list" class="space-y-4"></div>
                    </div>
                </div>

                <div class="section-content card p-8 mb-8 rounded-2xl shadow-lg hidden" id="ai-assistant">
                    <div class="flex justify-between items-center mb-6">
                        <h2 class="text-3xl font-bold text-gray-800 dark:text-white">AI Assistant</h2>
                        <button onclick="openAddContent('ai-assistant')" class="bg-green-600 hover:bg-green-700 text-white px-4 py-2 rounded-xl transition-all">
                            <i class="fas fa-plus mr-2"></i>Add Content
                        </button>
                    </div>
                    <div id="ai-assistant-content" class="space-y-4 mb-8"></div>
                    <div class="space-y-4">
                        <div class="ai-chat-container border border-gray-300 dark:border-gray-600 rounded-lg p-4 h-64 overflow-y-auto theme-transition bg-white dark:bg-gray-800">
                            <div class="p-3 bg-blue-50 dark:bg-blue-900/20 rounded-lg theme-transition mb-2">
                                <strong class="text-gray-800 dark:text-white">AI Assistant:</strong> 
                                <span class="text-gray-700 dark:text-gray-300">Hello! I'm here to help you with Security Chaos Engineering and Cloud Networks. Ask me anything!</span>
                            </div>
                        </div>

                        <div class="flex gap-2">
                            <input type="text" id="aiChatInput" class="flex-1 p-2 border border-gray-300 dark:border-gray-600 rounded theme-transition bg-white dark:bg-gray-700 text-gray-800 dark:text-white" placeholder="Ask about Security Chaos Engineering or Cloud Networks...">
                            <button onclick="sendAIMessage()" class="bg-green-600 hover:bg-green-700 text-white px-4 py-2 rounded theme-transition">
                                <i class="fas fa-paper-plane mr-2"></i>Send
                            </button>
                        </div>

                        <div>
                            <h3 class="font-semibold mb-2 text-gray-800 dark:text-white">Quick Commands</h3>
                            <div class="flex flex-wrap gap-2">
                                <button onclick="quickQuestion('What is Security Chaos Engineering?')" class="bg-gray-100 hover:bg-gray-200 dark:bg-gray-700 dark:hover:bg-gray-600 px-3 py-2 rounded theme-transition text-gray-800 dark:text-white">
                                    Security Chaos Engineering
                                </button>
                                <button onclick="quickQuestion('How to apply Security Chaos Engineering in AWS?')" class="bg-gray-100 hover:bg-gray-200 dark:bg-gray-700 dark:hover:bg-gray-600 px-3 py-2 rounded theme-transition text-gray-800 dark:text-white">
                                    AWS Implementation
                                </button>
                            </div>
                        </div>
                    </div>
                </div>
            </div>
        </div>
    </div>

    <script>
        // =============================================
        // NEW: REALTIME SYSTEM
        // =============================================
        
        class RealtimeSystem {
            constructor() {
                this.connectedUsers = new Map();
                this.currentUser = null;
                this.simulatedWebSocket = null;
                this.activityHistory = [];
            }

            connect(user) {
                this.currentUser = user;
                
                this.simulatedWebSocket = {
                    send: (data) => {
                        console.log('WebSocket send:', data);
                        this.handleIncomingMessage(data);
                    },
                    close: () => {
                        this.disconnect();
                    }
                };

                this.broadcastUserJoin(user);
                this.updateUIForRealtime();
                
                this.simulateOtherUsers();
            }

            disconnect() {
                if (this.currentUser) {
                    this.broadcastUserLeave(this.currentUser);
                }
                this.simulatedWebSocket = null;
                this.updateUIForRealtime();
            }

            broadcastUserJoin(user) {
                const message = {
                    type: 'user_join',
                    user: user,
                    timestamp: new Date().toISOString()
                };
                this.broadcastMessage(message);
            }

            broadcastUserLeave(user) {
                const message = {
                    type: 'user_leave', 
                    user: user,
                    timestamp: new Date().toISOString()
                };
                this.broadcastMessage(message);
            }

            broadcastContentUpdate(content) {
                const message = {
                    type: 'content_update',
                    content: content,
                    user: this.currentUser,
                    timestamp: new Date().toISOString()
                };
                this.broadcastMessage(message);
            }

            broadcastChatMessage(messageText) {
                const message = {
                    type: 'chat_message',
                    text: messageText,
                    user: this.currentUser,
                    timestamp: new Date().toISOString()
                };
                this.broadcastMessage(message);
            }

            broadcastMessage(message) {
                this.activityHistory.push(message);
                if (this.activityHistory.length > 50) {
                    this.activityHistory.shift();
                }

                this.handleIncomingMessage(message);
                console.log('Broadcasting:', message);
            }

            handleIncomingMessage(message) {
                switch (message.type) {
                    case 'user_join':
                        this.handleUserJoin(message.user);
                        break;
                    case 'user_leave':
                        this.handleUserLeave(message.user);
                        break;
                    case 'content_update':
                        this.handleContentUpdate(message.content, message.user);
                        break;
                    case 'chat_message':
                        this.handleChatMessage(message.text, message.user);
                        break;
                }
            }

            handleUserJoin(user) {
                this.connectedUsers.set(user.id, user);
                this.updateOnlineUsersList();
                this.addActivity(`${user.name} joined the dashboard`, 'user_join');
                showRealtimeNotification(`${user.name} joined the dashboard`);
            }

            handleUserLeave(user) {
                this.connectedUsers.delete(user.id);
                this.updateOnlineUsersList();
                this.addActivity(`${user.name} left the dashboard`, 'user_leave');
            }

            handleContentUpdate(content, user) {
                if (user.id !== this.currentUser.id) {
                    this.addActivity(`${user.name} added content to ${content.section}`, 'content_update');
                    renderSectionContents(content.section);
                    showRealtimeNotification(`${user.name} added content to ${content.section}`);
                }
            }

            handleChatMessage(text, user) {
                this.displayChatMessage(text, user);
            }

            updateOnlineUsersList() {
                const onlineList = document.getElementById('onlineUsersList');
                const liveTeamList = document.getElementById('liveTeamList');
                
                if (onlineList) {
                    onlineList.innerHTML = '';
                    this.connectedUsers.forEach(user => {
                        const userElement = document.createElement('div');
                        userElement.className = 'flex items-center space-x-2 bg-green-100 dark:bg-green-900 px-3 py-1 rounded-full';
                        userElement.innerHTML = `
                            <span class="online-indicator"></span>
                            <span class="text-sm font-medium">${user.name}</span>
                        `;
                        onlineList.appendChild(userElement);
                    });
                }

                if (liveTeamList) {
                    liveTeamList.innerHTML = '';
                    this.connectedUsers.forEach(user => {
                        const userElement = document.createElement('div');
                        userElement.className = 'flex items-center justify-between p-2 rounded-lg bg-gray-50 dark:bg-gray-700';
                        userElement.innerHTML = `
                            <div class="flex items-center space-x-2">
                                <span class="online-indicator"></span>
                                <span class="font-medium">${user.name}</span>
                            </div>
                            <span class="text-xs text-gray-500">Active now</span>
                        `;
                        liveTeamList.appendChild(userElement);
                    });
                }
            }

            addActivity(text, type) {
                const activityList = document.getElementById('activityList');
                if (activityList) {
                    const activityElement = document.createElement('div');
                    activityElement.className = 'live-activity';
                    activityElement.innerHTML = `
                        <div class="text-sm text-gray-600 dark:text-gray-300">${text}</div>
                        <div class="text-xs text-gray-400">Just now</div>
                    `;
                    activityList.insertBefore(activityElement, activityList.firstChild);
                    
                    document.getElementById('liveActivityFeed').classList.remove('hidden');
                }
            }

            displayChatMessage(text, user) {
                const chatMessages = document.getElementById('chatMessages');
                if (chatMessages) {
                    const messageElement = document.createElement('div');
                    const isOwnMessage = user.id === this.currentUser.id;
                    messageElement.className = `chat-message ${isOwnMessage ? 'own' : 'other'}`;
                    messageElement.innerHTML = `
                        <div class="font-semibold">${user.name}</div>
                        <div>${text}</div>
                        <div class="text-xs opacity-70 mt-1">${new Date().toLocaleTimeString()}</div>
                    `;
                    chatMessages.appendChild(messageElement);
                    chatMessages.scrollTop = chatMessages.scrollHeight;
                }
            }

            updateUIForRealtime() {
                const isConnected = this.simulatedWebSocket !== null;
                
                document.getElementById('realtimeStatus').classList.toggle('hidden', !isConnected);
                document.getElementById('liveActivity').classList.toggle('hidden', !isConnected);
                document.getElementById('liveTeamSection').classList.toggle('hidden', !isConnected);
                
                if (isConnected) {
                    this.updateOnlineUsersList();
                }
            }

            simulateOtherUsers() {
                const demoUsers = [
                    { id: '2', name: 'Kaled Osman', role: 'user' },
                    { id: '3', name: 'Fahad Hussain', role: 'user' },
                    { id: '4', name: 'Stefan Österberg', role: 'user' }
                ];

                demoUsers.forEach((user, index) => {
                    setTimeout(() => {
                        this.handleUserJoin(user);
                    }, (index + 1) * 2000);
                });

                setTimeout(() => {
                    this.addActivity('Kaled Osman added content to Project Documentation', 'content_update');
                }, 5000);

                setTimeout(() => {
                    this.addActivity('Fahad Hussain is working on Team Collaboration', 'user_activity');
                }, 8000);
            }
        }

        const realtimeSystem = new RealtimeSystem();

        function showRealtimeNotification(message) {
            const notification = document.createElement('div');
            notification.className = 'realtime-notification';
            notification.innerHTML = `
                <div class="flex items-center space-x-2">
                    <span class="online-indicator"></span>
                    <span class="font-medium">${message}</span>
                </div>
            `;
            document.body.appendChild(notification);
            
            setTimeout(() => {
                notification.remove();
            }, 5000);
        }

        function openTeamChat() {
            if (!currentUser) {
                showNotification('Please login to use team chat', 'error');
                return;
            }
            document.getElementById('teamChatModal').classList.add('active');
        }

        function closeTeamChat() {
            document.getElementById('teamChatModal').classList.remove('active');
        }

        function sendChatMessage() {
            const input = document.getElementById('chatInput');
            const message = input.value.trim();
            
            if (message && currentUser) {
                realtimeSystem.broadcastChatMessage(message);
                input.value = '';
            }
        }

        // =============================================
        // EXISTING CODE (UNCHANGED)
        // =============================================

        let users = JSON.parse(localStorage.getItem('dashboardUsers')) || [];
        let currentUser = JSON.parse(localStorage.getItem('currentUser')) || null;
        let userContents = JSON.parse(localStorage.getItem('userContents')) || {};

        function initializeDefaultAdmin() {
            if (users.length === 0) {
                const defaultAdmin = {
                    id: '1',
                    name: 'System Admin',
                    username: 'admin',
                    email: 'admin@dashboard.com',
                    password: 'admin123',
                    role: 'admin',
                    createdAt: new Date().toISOString(),
                    isActive: true
                };
                users.push(defaultAdmin);
                localStorage.setItem('dashboardUsers', JSON.stringify(users));
            }
        }

        function saveUsers() {
            localStorage.setItem('dashboardUsers', JSON.stringify(users));
        }

        function saveContents() {
            localStorage.setItem('userContents', JSON.stringify(userContents));
        }

        // MODIFIED: Login with realtime
        document.getElementById('loginForm').addEventListener('submit', function(e) {
            e.preventDefault();
            const username = document.getElementById('loginUsername').value;
            const password = document.getElementById('loginPassword').value;

            const user = users.find(u => u.username === username && u.password === password && u.isActive);
            
            if (user) {
                currentUser = user;
                localStorage.setItem('currentUser', JSON.stringify(user));
                updateUIAfterLogin();
                closeLogin();
                showNotification(`Welcome back, ${user.name}!`, 'success');
                
                // NEW: Connect to realtime
                realtimeSystem.connect(user);
            } else {
                showNotification('Invalid username or password', 'error');
            }
        });

        // MODIFIED: Logout with realtime
        function logout() {
            realtimeSystem.disconnect();
            currentUser = null;
            localStorage.removeItem('currentUser');
            updateUIAfterLogout();
            showNotification('Logged out successfully', 'info');
        }

        // MODIFIED: Add content with realtime
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
            
            // NEW: Broadcast to other users
            if (realtimeSystem && currentUser) {
                realtimeSystem.broadcastContentUpdate({
                    ...content,
                    section: section
                });
            }
        });

        // Rest of existing functions...
        function updateUIAfterLogin() {
            document.getElementById('loginSection').classList.add('hidden');
            document.getElementById('userSection').classList.remove('hidden');
            document.getElementById('userName').textContent = currentUser.name;
            document.getElementById('userRole').textContent = currentUser.role === 'admin' ? 'Administrator' : 'Team Member';
            document.getElementById('userAvatar').textContent = currentUser.name.charAt(0).toUpperCase();
            document.getElementById('loginStatus').textContent = `Welcome, ${currentUser.name}`;

            if (currentUser.role === 'admin') {
                document.getElementById('adminButton').classList.remove('hidden');
            }
        }

        function updateUIAfterLogout() {
            document.getElementById('loginSection').classList.remove('hidden');
            document.getElementById('userSection').classList.add('hidden');
            document.getElementById('loginStatus').textContent = 'Please login to access all features';
            document.getElementById('adminButton').classList.add('hidden');
        }

        function openLogin() {
            document.getElementById('loginModal').classList.add('active');
        }

        function closeLogin() {
            document.getElementById('loginModal').classList.remove('active');
            document.getElementById('loginForm').reset();
        }

        function showRegister() {
            closeLogin();
            document.getElementById('registerModal').classList.add('active');
        }

        function closeRegister() {
            document.getElementById('registerModal').classList.remove('active');
            document.getElementById('registerForm').reset();
        }

        function showForgotPassword() {
            closeLogin();
            document.getElementById('forgotPasswordModal').classList.add('active');
        }

        function closeForgotPassword() {
            document.getElementById('forgotPasswordModal').classList.remove('active');
            document.getElementById('forgotPasswordForm').reset();
        }

        function showLogin() {
            closeRegister();
            closeForgotPassword();
            document.getElementById('loginModal').classList.add('active');
        }

        function openAddContent(section, contentId = null) {
            if (!currentUser) {
                showNotification('Please login to add content', 'error');
                openLogin();
                return;
            }

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
                document.getElementById('contentAuthor').value = currentUser.name;
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
            const sections = ['overview', 'project-documentation', 'team-collaboration', 'team-updates', 'ai-assistant'];
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

        // Initialize
        document.addEventListener('DOMContentLoaded', function() {
            initializeDefaultAdmin();
            
            if (currentUser) {
                updateUIAfterLogin();
                realtimeSystem.connect(currentUser);
            }

            const savedTheme = localStorage.getItem('theme') || 'light';
            if (savedTheme === 'dark') {
                document.body.setAttribute('data-theme', 'dark');
                document.body.classList.add('dark');
            }

            setupNavigation();
            fetchSystemData();
            initializeAllContents();
            
            if (!currentUser) {
                showNotification('Please login to access all dashboard features', 'info');
            }
        });

    </script>
</body>
</html>
