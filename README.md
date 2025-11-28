<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Security Chaos Engineering Dashboard</title>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <script src="https://cdn.tailwindcss.com"></script>
    <style>
        /* الأنماط السابقة تبقى كما هي */
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

        .login-modal, .add-content-modal, .register-modal, .user-management-modal {
            display: none;
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background: rgba(0, 0, 0, 0.5);
            z-index: 1000;
        }

        .login-modal.active, .add-content-modal.active, .register-modal.active, .user-management-modal.active {
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

        /* إصلاح مشكلة التباين في الوضع الداكن */
        [data-theme="dark"] {
            color: #f8fafc !important;
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
        
        [data-theme="dark"] .text-gray-500 {
            color: #94a3b8 !important;
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

        /* تحسين النص في الوضع الداكن */
        .dark-text-fix {
            color: #f8fafc !important;
        }
        
        .dark-bg-fix {
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
                <button onclick="showRegister()" class="text-blue-600 hover:text-blue-800 dark:text-blue-400 dark:hover:text-blue-300 text-sm">
                    Don't have an account? Register here
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
                <div>
                    <label class="block text-sm font-medium mb-2 text-gray-700 dark:text-gray-300">Role</label>
                    <select id="registerRole" class="w-full p-3 border border-gray-300 dark:border-gray-600 rounded-xl focus:ring-2 focus:ring-blue-500 focus:border-blue-500 dark:bg-gray-700 dark:text-white">
                        <option value="user">Team Member</option>
                        <option value="admin">Admin</option>
                    </select>
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

    <!-- User Management Modal (للمسؤولين فقط) -->
    <div class="user-management-modal" id="userManagementModal">
        <div class="bg-white dark:bg-gray-800 p-8 rounded-2xl max-w-4xl w-full mx-4 shadow-2xl max-h-[80vh] overflow-y-auto">
            <h2 class="text-2xl font-bold mb-6 text-center text-gray-800 dark:text-white">User Management</h2>
            <div class="space-y-4">
                <div id="usersList" class="space-y-3">
                    <!-- سيتم عرض المستخدمين هنا -->
                </div>
            </div>
            <button onclick="closeUserManagement()" class="absolute top-4 right-4 text-gray-500 hover:text-gray-700 dark:text-gray-400 dark:hover:text-gray-200">
                <i class="fas fa-times text-xl"></i>
            </button>
        </div>
    </div>

    <!-- باقي الـ Modals والأقسام تبقى كما هي -->
    <div class="add-content-modal" id="addContentModal">
        <!-- ... نفس المحتوى السابق ... -->
    </div>

    <!-- Main Layout -->
    <div class="flex min-h-screen">
        <!-- Sidebar -->
        <div class="sidebar w-80 flex-shrink-0 theme-transition p-6">
            <div class="mb-8">
                <h1 class="text-2xl font-bold text-blue-600 dark:text-blue-400">Security Chaos Engineering</h1>
                <p class="text-gray-600 dark:text-gray-400 mt-2">Group 1 Dashboard</p>
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
                        <button onclick="logout()" class="w-full bg-red-600 hover:bg-red-700 text-white py-2 rounded-lg transition-all text-sm">
                            <i class="fas fa-sign-out-alt mr-2"></i>Logout
                        </button>
                    </div>
                </div>
            </div>

            <!-- Navigation -->
            <nav class="space-y-2 mb-8">
                <!-- نفس الأزرار السابقة -->
                <button class="section-btn w-full text-left p-4 rounded-xl theme-transition active-section" data-section="overview">
                    <i class="fas fa-home mr-3 w-6 text-center"></i>Overview
                </button>
                <!-- ... باقي الأزرار ... -->
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
                <!-- جميع الأقسام تبقى كما هي -->
                <!-- ... -->
            </div>
        </div>
    </div>

    <script>
        // نظام إدارة المستخدمين
        let users = JSON.parse(localStorage.getItem('dashboardUsers')) || [];
        let currentUser = JSON.parse(localStorage.getItem('currentUser')) || null;

        // إنشاء مستخدم مسؤول افتراضي إذا لم يكن موجوداً
        if (users.length === 0) {
            const defaultAdmin = {
                id: '1',
                name: 'System Admin',
                username: 'admin',
                email: 'admin@dashboard.com',
                password: 'admin123', // في الواقع يجب تشفير كلمة المرور
                role: 'admin',
                createdAt: new Date().toISOString(),
                isActive: true
            };
            users.push(defaultAdmin);
            localStorage.setItem('dashboardUsers', JSON.stringify(users));
        }

        // حفظ المستخدمين
        function saveUsers() {
            localStorage.setItem('dashboardUsers', JSON.stringify(users));
        }

        // تسجيل الدخول
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
            } else {
                showNotification('Invalid username or password', 'error');
            }
        });

        // التسجيل
        document.getElementById('registerForm').addEventListener('submit', function(e) {
            e.preventDefault();
            const name = document.getElementById('registerName').value;
            const username = document.getElementById('registerUsername').value;
            const email = document.getElementById('registerEmail').value;
            const password = document.getElementById('registerPassword').value;
            const confirmPassword = document.getElementById('registerConfirmPassword').value;
            const role = document.getElementById('registerRole').value;

            // التحقق من صحة البيانات
            if (password !== confirmPassword) {
                showNotification('Passwords do not match', 'error');
                return;
            }

            if (users.find(u => u.username === username)) {
                showNotification('Username already exists', 'error');
                return;
            }

            if (users.find(u => u.email === email)) {
                showNotification('Email already exists', 'error');
                return;
            }

            // إنشاء مستخدم جديد
            const newUser = {
                id: Date.now().toString(),
                name,
                username,
                email,
                password, // في الواقع يجب تشفير كلمة المرور
                role,
                createdAt: new Date().toISOString(),
                isActive: true
            };

            users.push(newUser);
            saveUsers();
            
            showNotification('Account created successfully! Please login.', 'success');
            closeRegister();
            showLogin();
        });

        // تسجيل الخروج
        function logout() {
            currentUser = null;
            localStorage.removeItem('currentUser');
            updateUIAfterLogout();
            showNotification('Logged out successfully', 'info');
        }

        // تحديث واجهة المستخدم بعد تسجيل الدخول
        function updateUIAfterLogin() {
            document.getElementById('loginSection').classList.add('hidden');
            document.getElementById('userSection').classList.remove('hidden');
            document.getElementById('userName').textContent = currentUser.name;
            document.getElementById('userRole').textContent = currentUser.role === 'admin' ? 'Administrator' : 'Team Member';
            document.getElementById('userAvatar').textContent = currentUser.name.charAt(0).toUpperCase();
            document.getElementById('loginStatus').textContent = `Welcome, ${currentUser.name}`;

            // إظهار زر إدارة المستخدمين للمسؤولين فقط
            if (currentUser.role === 'admin') {
                document.getElementById('adminButton').classList.remove('hidden');
            }
        }

        // تحديث واجهة المستخدم بعد تسجيل الخروج
        function updateUIAfterLogout() {
            document.getElementById('loginSection').classList.remove('hidden');
            document.getElementById('userSection').classList.add('hidden');
            document.getElementById('loginStatus').textContent = 'Please login to access all features';
            document.getElementById('adminButton').classList.add('hidden');
        }

        // إدارة المستخدمين (للمسؤولين فقط)
        function openUserManagement() {
            if (currentUser && currentUser.role === 'admin') {
                renderUsersList();
                document.getElementById('userManagementModal').classList.add('active');
            }
        }

        function closeUserManagement() {
            document.getElementById('userManagementModal').classList.remove('active');
        }

        function renderUsersList() {
            const usersList = document.getElementById('usersList');
            usersList.innerHTML = '';

            users.forEach(user => {
                const userElement = document.createElement('div');
                userElement.className = 'p-4 border border-gray-200 dark:border-gray-700 rounded-xl bg-white dark:bg-gray-800';
                userElement.innerHTML = `
                    <div class="flex justify-between items-center">
                        <div class="flex items-center space-x-3">
                            <div class="w-8 h-8 ${user.role === 'admin' ? 'user-role-admin' : 'user-role-user'} rounded-full flex items-center justify-center text-white font-bold text-sm">
                                ${user.name.charAt(0).toUpperCase()}
                            </div>
                            <div>
                                <h4 class="font-semibold text-gray-800 dark:text-white">${user.name}</h4>
                                <p class="text-sm text-gray-600 dark:text-gray-400">@${user.username} • ${user.role}</p>
                            </div>
                        </div>
                        <div class="flex space-x-2">
                            <button onclick="toggleUserStatus('${user.id}')" class="px-3 py-1 rounded text-sm ${
                                user.isActive ? 'bg-red-100 text-red-700 hover:bg-red-200' : 'bg-green-100 text-green-700 hover:bg-green-200'
                            }">
                                ${user.isActive ? 'Deactivate' : 'Activate'}
                            </button>
                            ${user.id !== currentUser.id ? `
                            <button onclick="deleteUser('${user.id}')" class="px-3 py-1 bg-red-600 text-white rounded text-sm hover:bg-red-700">
                                Delete
                            </button>
                            ` : ''}
                        </div>
                    </div>
                    <div class="mt-2 text-xs text-gray-500 dark:text-gray-400">
                        Created: ${new Date(user.createdAt).toLocaleDateString()}
                    </div>
                `;
                usersList.appendChild(userElement);
            });
        }

        function toggleUserStatus(userId) {
            const user = users.find(u => u.id === userId);
            if (user) {
                user.isActive = !user.isActive;
                saveUsers();
                renderUsersList();
                showNotification(`User ${user.isActive ? 'activated' : 'deactivated'} successfully`, 'success');
            }
        }

        function deleteUser(userId) {
            if (confirm('Are you sure you want to delete this user? This action cannot be undone.')) {
                users = users.filter(u => u.id !== userId);
                saveUsers();
                renderUsersList();
                showNotification('User deleted successfully', 'success');
            }
        }

        // دوال فتح وإغلاق النوافذ
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

        function showLogin() {
            closeRegister();
            document.getElementById('loginModal').classList.add('active');
        }

        // التحقق من حالة تسجيل الدخول عند تحميل الصفحة
        document.addEventListener('DOMContentLoaded', function() {
            if (currentUser) {
                updateUIAfterLogin();
            }

            // باقي كود التهيئة...
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

        // باقي الدوال تبقى كما هي (نظام المحتوى، التصميم، إلخ)
        // ... [كل الكود السابق يبقى كما هو] ...

        // نظام إدارة المحتوى
        let userContents = JSON.parse(localStorage.getItem('userContents')) || {};

        function saveContents() {
            localStorage.setItem('userContents', JSON.stringify(userContents));
        }

        function openAddContent(section, contentId = null) {
            // التحقق من تسجيل الدخول قبل السماح بإضافة محتوى
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
                // تعيين المستخدم الحالي ككاتب افتراضي
                document.getElementById('contentAuthor').value = currentUser.name;
            }
            
            document.getElementById('addContentModal').classList.add('active');
        }

        // باقي دوال نظام المحتوى تبقى كما هي...

        // دوال الإشعارات والتصميم تبقى كما هي...
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

        // ... [باقي الدوال] ...
    </script>
</body>
</html>
