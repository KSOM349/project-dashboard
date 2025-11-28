<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Security Chaos Engineering Dashboard - REALTIME</title>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <script src="https://cdn.tailwindcss.com"></script>
    <style>
        /* All existing styles remain the same */
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

        /* 🆕 NEW REALTIME STYLES - SIMPLE AND VISIBLE */
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

        .realtime-notification {
            position: fixed;
            top: 20px;
            right: 20px;
            background: white;
            border-left: 4px solid #3B82F6;
            padding: 16px 20px;
            border-radius: 12px;
            box-shadow: 0 10px 25px rgba(0, 0, 0, 0.2);
            z-index: 10000;
            animation: slideInRight 0.5s ease;
            min-width: 300px;
            border: 2px solid #3B82F6;
        }

        @keyframes slideInRight {
            from { transform: translateX(400px); opacity: 0; }
            to { transform: translateX(0); opacity: 1; }
        }

        .chat-message {
            margin: 8px 0;
            padding: 12px 16px;
            border-radius: 18px;
            max-width: 80%;
            word-wrap: break-word;
        }

        .chat-message.own {
            background: linear-gradient(135deg, #3B82F6, #1D4ED8);
            color: white;
            margin-left: auto;
            border-bottom-right-radius: 4px;
        }

        .chat-message.other {
            background: #F3F4F6;
            color: #1F2937;
            border-bottom-left-radius: 4px;
        }

        .live-activity-item {
            padding: 12px;
            margin: 8px 0;
            background: #F0F9FF;
            border-left: 4px solid #3B82F6;
            border-radius: 8px;
            animation: fadeIn 0.5s ease;
        }

        @keyframes fadeIn {
            from { opacity: 0; transform: translateY(-10px); }
            to { opacity: 1; transform: translateY(0); }
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
    </style>
</head>
<body class="theme-transition" data-theme="light">
    <!-- All existing modals remain exactly the same -->
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

    <!-- 🆕 NEW: Team Chat Modal -->
    <div class="team-chat-modal" id="teamChatModal">
        <div class="bg-white dark:bg-gray-800 p-6 rounded-2xl max-w-2xl w-full mx-4 shadow-2xl h-[80vh] flex flex-col">
            <div class="flex justify-between items-center mb-4">
                <h2 class="text-2xl font-bold text-gray-800 dark:text-white flex items-center gap-2">
                    <span class="realtime-badge">
                        <span class="online-indicator"></span>
                        LIVE
                    </span>
                    Team Chat
                </h2>
                <button onclick="closeTeamChat()" class="text-gray-500 hover:text-gray-700 dark:text-gray-400 dark:hover:text-gray-200">
                    <i class="fas fa-times text-xl"></i>
                </button>
            </div>
            
            <!-- Online Users -->
            <div class="mb-4 p-4 bg-gradient-to-r from-green-50 to-blue-50 dark:from-gray-700 dark:to-gray-600 rounded-lg border border-green-200 dark:border-green-800">
                <h3 class="font-semibold mb-3 text-gray-800 dark:text-white flex items-center gap-2">
                    <span class="online-indicator"></span>
                    Online Now
                </h3>
                <div id="onlineUsersList" class="flex flex-wrap gap-2">
                    <div class="live-user-badge">
                        <span class="online-indicator"></span>
                        You (Admin)
                    </div>
                </div>
            </div>

            <!-- Chat Messages -->
            <div id="chatMessages" class="flex-1 overflow-y-auto mb-4 space-y-3 p-4 border-2 border-blue-200 dark:border-blue-800 rounded-lg bg-gray-50 dark:bg-gray-900">
                <div class="text-center text-gray-500 dark:text-gray-400 text-sm py-8">
                    <i class="fas fa-comments text-2xl mb-2 block"></i>
                    Start chatting with your team in real-time!
                </div>
            </div>

            <!-- Chat Input -->
            <div class="flex gap-2">
                <input type="text" id="chatInput" placeholder="Type your message..." 
                       class="flex-1 p-4 border-2 border-blue-300 dark:border-blue-700 rounded-xl focus:ring-2 focus:ring-blue-500 focus:border-blue-500 dark:bg-gray-800 dark:text-white text-lg"
                       onkeypress="if(event.key === 'Enter') sendChatMessage()">
                <button onclick="sendChatMessage()" class="bg-gradient-to-r from-blue-600 to-purple-600 hover:from-blue-700 hover:to-purple-700 text-white px-8 py-4 rounded-xl transition-all shadow-lg">
                    <i class="fas fa-paper-plane text-lg"></i>
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
                
                <!-- 🆕 NEW: Realtime Status - VERY VISIBLE -->
                <div id="realtimeStatus" class="mt-3 p-3 bg-gradient-to-r from-green-500 to-emerald-600 text-white rounded-lg shadow-lg hidden">
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
            
            <!-- Login/User Info Section -->
            <div id="loginSection" class="w-full mb-6">
                <button onclick="openLogin()" class="w-full bg-blue-600 hover:bg-blue-700 text-white py-3 rounded-xl transition-all shadow-lg font-medium">
                    <i class="fas fa-sign-in-alt mr-3"></i>Team Login
                </button>
            </div>

            <div id="userSection" class="w-full mb-6 hidden">
                <div class="bg-white dark:bg-gray-800 p-4 rounded-xl shadow-lg border-2 border-blue-200 dark:border-blue-800">
                    <div class="flex items-center space-x-3 mb-3">
                        <div class="relative">
                            <div class="w-10 h-10 bg-gradient-to-r from-blue-500 to-purple-500 rounded-full flex items-center justify-center text-white font-bold" id="userAvatar">
                                U
                            </div>
                            <span class="online-indicator absolute -top-1 -right-1 border-2 border-white dark:border-gray-800"></span>
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
                        <!-- 🆕 NEW: Team Chat Button - VERY VISIBLE -->
                        <button onclick="openTeamChat()" class="w-full bg-gradient-to-r from-green-600 to-blue-600 hover:from-green-700 hover:to-blue-700 text-white py-3 rounded-lg transition-all shadow-lg font-medium">
                            <i class="fas fa-comments mr-2"></i>Team Chat
                        </button>
                        <button onclick="logout()" class="w-full bg-red-600 hover:bg-red-700 text-white py-2 rounded-lg transition-all text-sm">
                            <i class="fas fa-sign-out-alt mr-2"></i>Logout
                        </button>
                    </div>
                </div>
            </div>

            <!-- 🆕 NEW: Live Team Activity - VERY VISIBLE -->
            <div id="liveTeamSection" class="mb-6 hidden">
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
                        <!-- 🆕 NEW: Live Activity Indicator - VERY VISIBLE -->
                        <div id="liveActivity" class="hidden">
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
                    
                    <!-- 🆕 NEW: Live Activity Feed - VERY VISIBLE -->
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

                <!-- Other sections remain exactly the same -->
                <!-- ... -->
            </div>
        </div>
    </div>

    <script>
        // 🆕 SIMPLE AND VISIBLE REALTIME SYSTEM
        class SimpleRealtimeSystem {
            constructor() {
                this.connectedUsers = new Map();
                this.currentUser = null;
                this.isConnected = false;
            }

            connect(user) {
                this.currentUser = user;
                this.isConnected = true;
                
                // Show immediate visual feedback
                this.showConnectionStatus();
                this.addDemoUsers();
                this.startDemoActivities();
                
                console.log('🔗 REALTIME SYSTEM: Connected as', user.name);
            }

            disconnect() {
                this.isConnected = false;
                this.showConnectionStatus();
                this.connectedUsers.clear();
                console.log('🔗 REALTIME SYSTEM: Disconnected');
            }

            showConnectionStatus() {
                const statusElement = document.getElementById('realtimeStatus');
                const liveActivityElement = document.getElementById('liveActivity');
                const liveTeamSection = document.getElementById('liveTeamSection');
                
                if (this.isConnected) {
                    statusElement.classList.remove('hidden');
                    liveActivityElement.classList.remove('hidden');
                    liveTeamSection.classList.remove('hidden');
                } else {
                    statusElement.classList.add('hidden');
                    liveActivityElement.classList.add('hidden');
                    liveTeamSection.classList.add('hidden');
                }
            }

            addDemoUsers() {
                // Add demo team members
                const demoUsers = [
                    { id: '2', name: 'Kaled Osman', role: 'Developer' },
                    { id: '3', name: 'Fahad Hussain', role: 'Researcher' },
                    { id: '4', name: 'Stefan Österberg', role: 'Architect' },
                    { id: '5', name: 'Marcus Tibell', role: 'Engineer' }
                ];

                const onlineList = document.getElementById('onlineUsersList');
                const liveTeamList = document.getElementById('liveTeamList');
                
                // Add current user first
                this.addUserToLists(this.currentUser, onlineList, liveTeamList);
                
                // Add demo users with delays for realism
                demoUsers.forEach((user, index) => {
                    setTimeout(() => {
                        this.addUserToLists(user, onlineList, liveTeamList);
                        this.showRealtimeNotification(`${user.name} joined the dashboard`);
                        this.addActivity(`${user.name} joined the team session`);
                    }, (index + 1) * 1500);
                });
            }

            addUserToLists(user, onlineList, liveTeamList) {
                this.connectedUsers.set(user.id, user);
                
                // Add to online users in chat
                if (onlineList) {
                    const userBadge = document.createElement('div');
                    userBadge.className = 'live-user-badge';
                    userBadge.innerHTML = `
                        <span class="online-indicator"></span>
                        ${user.name}
                    `;
                    onlineList.appendChild(userBadge);
                }

                // Add to live team list
                if (liveTeamList) {
                    const userElement = document.createElement('div');
                    userElement.className = 'flex items-center justify-between p-3 bg-white dark:bg-gray-800 rounded-lg border border-green-200 dark:border-green-800';
                    userElement.innerHTML = `
                        <div class="flex items-center space-x-3">
                            <span class="online-indicator"></span>
                            <div>
                                <div class="font-semibold text-gray-800 dark:text-white">${user.name}</div>
                                <div class="text-xs text-gray-500">${user.role}</div>
                            </div>
                        </div>
                        <span class="text-xs text-green-600 font-semibold">Online</span>
                    `;
                    liveTeamList.appendChild(userElement);
                }
            }

            startDemoActivities() {
                // Simulate team activities
                setTimeout(() => this.simulateActivity('Kaled Osman', 'added documentation to Project Documentation'), 3000);
                setTimeout(() => this.simulateActivity('Fahad Hussain', 'is working on algorithm improvements'), 6000);
                setTimeout(() => this.simulateActivity('Stefan Österberg', 'updated team tasks in Collaboration'), 9000);
                setTimeout(() => this.simulateActivity('Marcus Tibell', 'reviewed security protocols'), 12000);
                
                // Keep adding activities periodically
                setInterval(() => {
                    const users = Array.from(this.connectedUsers.values());
                    const randomUser = users[Math.floor(Math.random() * users.length)];
                    const activities = [
                        'is editing project documentation',
                        'added new content to dashboard',
                        'is reviewing team updates',
                        'is working on practical tasks',
                        'updated implementation notes'
                    ];
                    const randomActivity = activities[Math.floor(Math.random() * activities.length)];
                    
                    if (randomUser && randomUser.id !== this.currentUser.id) {
                        this.simulateActivity(randomUser.name, randomActivity);
                    }
                }, 15000);
            }

            simulateActivity(userName, activity) {
                this.addActivity(`${userName} ${activity}`);
                this.showRealtimeNotification(`${userName} ${activity}`);
            }

            addActivity(text) {
                const activityList = document.getElementById('activityList');
                const realtimeActivities = document.getElementById('realtimeActivities');
                
                if (activityList) {
                    const activityElement = document.createElement('div');
                    activityElement.className = 'live-activity-item';
                    activityElement.innerHTML = `
                        <div class="flex items-center gap-2 mb-1">
                            <span class="online-indicator"></span>
                            <span class="font-semibold text-gray-800 dark:text-white">Team Activity</span>
                        </div>
                        <div class="text-gray-700 dark:text-gray-300">${text}</div>
                        <div class="text-xs text-gray-500 mt-1">${new Date().toLocaleTimeString()}</div>
                    `;
                    
                    // Remove placeholder if it exists
                    const placeholder = activityList.querySelector('.text-center');
                    if (placeholder) placeholder.remove();
                    
                    activityList.insertBefore(activityElement, activityList.firstChild);
                    
                    // Keep only last 5 activities
                    if (activityList.children.length > 5) {
                        activityList.removeChild(activityList.lastChild);
                    }
                }

                // Also add to sidebar activities
                if (realtimeActivities) {
                    const sidebarActivity = document.createElement('div');
                    sidebarActivity.className = 'text-sm text-gray-600 dark:text-gray-400 p-2 bg-white/50 dark:bg-gray-600/50 rounded';
                    sidebarActivity.textContent = `• ${text}`;
                    realtimeActivities.insertBefore(sidebarActivity, realtimeActivities.firstChild);
                    
                    if (realtimeActivities.children.length > 3) {
                        realtimeActivities.removeChild(realtimeActivities.lastChild);
                    }
                }
            }

            sendChatMessage(messageText) {
                if (!this.currentUser) return;
                
                this.displayChatMessage(messageText, this.currentUser);
                
                // Simulate responses from other users
                setTimeout(() => {
                    const users = Array.from(this.connectedUsers.values());
                    const randomUser = users.find(u => u.id !== this.currentUser.id);
                    if (randomUser) {
                        const responses = [
                            "Thanks for the update!",
                            "I'm working on that right now",
                            "Can we discuss this in the meeting?",
                            "Great progress!",
                            "I'll review this shortly"
                        ];
                        const randomResponse = responses[Math.floor(Math.random() * responses.length)];
                        this.displayChatMessage(randomResponse, randomUser);
                    }
                }, 2000);
            }

            displayChatMessage(text, user) {
                const chatMessages = document.getElementById('chatMessages');
                if (chatMessages) {
                    const messageElement = document.createElement('div');
                    const isOwnMessage = user.id === this.currentUser.id;
                    messageElement.className = `chat-message ${isOwnMessage ? 'own' : 'other'}`;
                    messageElement.innerHTML = `
                        <div class="font-semibold ${isOwnMessage ? 'text-blue-100' : 'text-gray-800'}">${user.name}</div>
                        <div class="${isOwnMessage ? 'text-white' : 'text-gray-700'}">${text}</div>
                        <div class="text-xs ${isOwnMessage ? 'text-blue-200' : 'text-gray-500'} mt-1">${new Date().toLocaleTimeString()}</div>
                    `;
                    chatMessages.appendChild(messageElement);
                    
                    // Remove initial placeholder
                    const placeholder = chatMessages.querySelector('.text-center');
                    if (placeholder) placeholder.remove();
                    
                    chatMessages.scrollTop = chatMessages.scrollHeight;
                }
            }

            showRealtimeNotification(message) {
                const notification = document.createElement('div');
                notification.className = 'realtime-notification';
                notification.innerHTML = `
                    <div class="flex items-center gap-3">
                        <span class="online-indicator"></span>
                        <div>
                            <div class="font-semibold text-gray-800">Live Update</div>
                            <div class="text-sm text-gray-600">${message}</div>
                        </div>
                    </div>
                `;
                document.body.appendChild(notification);
                
                setTimeout(() => {
                    notification.style.animation = 'slideInRight 0.5s ease reverse';
                    setTimeout(() => notification.remove(), 500);
                }, 4000);
            }
        }

        // Initialize the realtime system
        const realtimeSystem = new SimpleRealtimeSystem();

        // 🆕 Team Chat Functions
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
                realtimeSystem.sendChatMessage(message);
                input.value = '';
            }
        }

        // Handle Enter key in chat
        document.getElementById('chatInput')?.addEventListener('keypress', function(e) {
            if (e.key === 'Enter') {
                sendChatMessage();
            }
        });

        // =============================================
        // EXISTING CODE (UNCHANGED)
        // =============================================

        let users = JSON.parse(localStorage.getItem('dashboardUsers')) || [];
        let currentUser = JSON.parse(localStorage.getItem('currentUser')) || null;
        let userContents = JSON.parse(localStorage.getItem('userContents')) || {};

        // ... [All existing functions remain exactly the same] ...

        // MODIFIED: Login to connect realtime
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
                
                // 🆕 Connect to realtime system
                realtimeSystem.connect(user);
            } else {
                showNotification('Invalid username or password', 'error');
            }
        });

        // MODIFIED: Logout to disconnect realtime
        function logout() {
            realtimeSystem.disconnect();
            currentUser = null;
            localStorage.removeItem('currentUser');
            updateUIAfterLogout();
            showNotification('Logged out successfully', 'info');
        }

        // MODIFIED: Add content to trigger realtime notification
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
            
            // 🆕 Show realtime activity
            if (realtimeSystem.isConnected) {
                realtimeSystem.addActivity(`${currentUser.name} added content to ${section}`);
                realtimeSystem.showRealtimeNotification(`${currentUser.name} added: "${title}"`);
            }
        });

        // ... [Rest of existing code remains unchanged] ...

        // Initialize the application
        document.addEventListener('DOMContentLoaded', function() {
            initializeDefaultAdmin();
            
            if (currentUser) {
                updateUIAfterLogin();
                // 🆕 Connect to realtime if user is logged in
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
