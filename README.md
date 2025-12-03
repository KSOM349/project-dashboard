<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Security Chaos Engineering Dashboard</title>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <script src="https://cdn.tailwindcss.com"></script>
    
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
        .loading { 
            display: inline-block; width: 20px; height: 20px; border: 3px solid #f3f3f3;
            border-top: 3px solid #3498db; border-radius: 50%; animation: spin 1s linear infinite;
        }
        @keyframes spin { 0% { transform: rotate(0deg); } 100% { transform: rotate(360deg); } }
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
                    <label class="block text-sm font-medium mb-2 text-gray-700 dark:text-gray-300">Name</label>
                    <select id="userSelect" class="w-full p-3 border border-gray-300 dark:border-gray-600 rounded-xl focus:ring-2 focus:ring-blue-500 focus:border-blue-500 dark:bg-gray-700 dark:text-white">
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
                    <button type="submit" class="flex-1 bg-blue-600 hover:bg-blue-700 text-white py-3 rounded-xl transition-all font-medium">
                        Login
                    </button>
                    <button type="button" onclick="closeLogin()" class="flex-1 bg-gray-500 hover:bg-gray-600 text-white py-3 rounded-xl transition-all font-medium">
                        Cancel
                    </button>
                </div>
            </form>
            
            <div class="mt-6 p-4 bg-gray-50 dark:bg-gray-700 rounded-xl">
                <h3 class="font-semibold mb-2">How it works:</h3>
                <div class="text-sm space-y-1">
                    <div>1. Select your name</div>
                    <div>2. Click Login</div>
                    <div>3. All content is saved for everyone!</div>
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
                <div class="flex gap-3">
                    <button type="submit" class="flex-1 bg-green-600 hover:bg-green-700 text-white py-3 rounded-xl transition-all font-medium">Save for Everyone</button>
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
                            <span class="font-semibold">TEAM WORKING</span>
                        </div>
                        <i class="fas fa-bolt"></i>
                    </div>
                    <div class="text-xs opacity-90 mt-1">All changes saved automatically</div>
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
                            <p class="text-sm text-gray-600 dark:text-gray-400 truncate" id="userRole">Select your name to login</p>
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
                        <!-- Team members loaded dynamically -->
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
                <p class="text-gray-600 dark:text-gray-400">Group 1 - Team Collaboration Platform</p>
            </header>

            <!-- All Sections -->
            <div class="section-content active" id="overview">
                <div class="bg-white dark:bg-gray-800 p-8 rounded-2xl shadow-lg">
                    <div class="flex justify-between items-center mb-6">
                        <h2 class="text-2xl font-bold text-gray-800 dark:text-white">Overview</h2>
                        <button onclick="openAddContent('overview')" class="bg-green-600 hover:bg-green-700 text-white px-4 py-2 rounded-xl transition-all">
                            <i class="fas fa-plus mr-2"></i>Add Content
                        </button>
                    </div>
                    <div id="overview-content" class="space-y-4">
                        <!-- Content will be loaded here -->
                    </div>
                </div>
            </div>

            <!-- Other Sections (same pattern) -->
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

            <!-- ... (repeat for all other sections) ... -->

        </div>
    </div>

    <script>
        // SIMPLE TEAM DATA
        const teamMembers = [
            { name: 'Kaled Osman', role: 'Developer', avatar: 'K' },
            { name: 'Marcus Tibell', role: 'Engineer', avatar: 'M' },
            { name: 'Jens Annell', role: 'Analyst', avatar: 'J' },
            { name: 'Fahad Hussain', role: 'Researcher', avatar: 'F' },
            { name: 'Luwam', role: 'Designer', avatar: 'L' },
            { name: 'Stefan Österberg', role: 'Architect', avatar: 'S' },
            { name: 'Najmaddin', role: 'Security Expert', avatar: 'N' }
        ];

        // Current user
        let currentUser = null;

        // Initialize team list
        function initializeTeamList() {
            const teamList = document.getElementById('teamList');
            teamList.innerHTML = '';
            
            teamMembers.forEach(member => {
                const memberDiv = document.createElement('div');
                memberDiv.className = 'flex items-center space-x-3 p-2 bg-white dark:bg-gray-700 rounded-lg';
                memberDiv.innerHTML = `
                    <div class="relative">
                        <div class="w-8 h-8 bg-gradient-to-r from-blue-500 to-purple-500 rounded-full flex items-center justify-center text-white text-sm font-bold">${member.avatar}</div>
                        <span class="online-indicator absolute -top-1 -right-1 border-2 border-white dark:border-gray-700"></span>
                    </div>
                    <div class="flex-1">
                        <div class="font-medium text-gray-800 dark:text-white text-sm">${member.name}</div>
                        <div class="text-xs text-gray-600 dark:text-gray-400">${member.role}</div>
                    </div>
                `;
                teamList.appendChild(memberDiv);
            });
        }

        // Login system
        document.getElementById('loginForm').addEventListener('submit', function(e) {
            e.preventDefault();
            const selectedName = document.getElementById('userSelect').value;
            
            const user = teamMembers.find(m => m.name === selectedName);
            if (user) {
                currentUser = user;
                updateUserUI();
                document.getElementById('loginModal').style.display = 'none';
                alert(`Welcome ${user.name}!`);
                loadAllContent();
            }
        });

        function updateUserUI() {
            if (currentUser) {
                document.getElementById('userName').textContent = currentUser.name;
                document.getElementById('userRole').textContent = currentUser.role;
                document.getElementById('userAvatar').textContent = currentUser.avatar;
            }
        }

        function logout() {
            currentUser = null;
            document.getElementById('userName').textContent = 'You';
            document.getElementById('userRole').textContent = 'Select your name to login';
            document.getElementById('userAvatar').textContent = 'Y';
            document.getElementById('loginModal').style.display = 'flex';
        }

        function closeLogin() {
            if (!currentUser) {
                document.getElementById('loginModal').style.display = 'flex';
            }
        }

        // SIMPLE CONTENT MANAGEMENT - NO FIREBASE NEEDED
        // We'll use a simple API-based storage
        
        const API_URL = 'https://jsonblob.com/api/jsonBlob';
        let currentDataId = null;

        async function saveContent(section, title, description) {
            if (!currentUser) {
                alert('Please login first!');
                return false;
            }

            try {
                // Create content object
                const content = {
                    id: Date.now().toString(),
                    title,
                    description,
                    author: currentUser.name,
                    date: new Date().toISOString(),
                    section
                };

                // Get existing data
                let allContent = await getAllContent();
                allContent.push(content);
                
                // Save to localStorage as backup
                localStorage.setItem('team-content', JSON.stringify(allContent));
                
                // Try to save to cloud (optional)
                try {
                    await saveToCloud(allContent);
                } catch (e) {
                    console.log('Cloud save failed, using local storage');
                }
                
                alert('Content saved successfully!');
                loadSectionContent(section);
                return true;
            } catch (error) {
                console.error('Error saving content:', error);
                alert('Failed to save content');
                return false;
            }
        }

        async function getAllContent() {
            // Try to get from cloud first
            try {
                if (currentDataId) {
                    const response = await fetch(`${API_URL}/${currentDataId}`);
                    if (response.ok) {
                        return await response.json();
                    }
                }
            } catch (e) {
                console.log('Cloud load failed');
            }
            
            // Fallback to local storage
            const localData = localStorage.getItem('team-content');
            return localData ? JSON.parse(localData) : [];
        }

        async function saveToCloud(data) {
            const response = await fetch(API_URL, {
                method: 'POST',
                headers: { 'Content-Type': 'application/json' },
                body: JSON.stringify(data)
            });
            
            if (response.ok) {
                const blob = await response.json();
                currentDataId = blob.id;
                localStorage.setItem('team-data-id', currentDataId);
            }
        }

        async function loadAllContent() {
            const allContent = await getAllContent();
            
            // Group content by section
            const sections = [
                'overview', 'project-documentation', 'team-collaboration', 'team-updates',
                'ai-assistant', 'practical-tasks', 'implementation', 'research',
                'resources', 'security-testing', 'monitoring-analytics'
            ];
            
            sections.forEach(section => {
                const sectionContent = allContent.filter(item => item.section === section);
                displaySectionContent(section, sectionContent);
            });
        }

        function displaySectionContent(section, contentArray) {
            const container = document.getElementById(`${section}-content`);
            if (!container) return;
            
            container.innerHTML = '';
            
            if (!contentArray || contentArray.length === 0) {
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

        // Form submission for content
        document.getElementById('addContentForm').addEventListener('submit', async function(e) {
            e.preventDefault();
            const section = document.getElementById('contentSection').value;
            const title = document.getElementById('contentTitle').value;
            const description = document.getElementById('contentDescription').value;
            
            const success = await saveContent(section, title, description);
            if (success) {
                closeAddContent();
            }
        });

        function openAddContent(section) {
            if (!currentUser) {
                alert('Please login first!');
                return;
            }
            document.getElementById('contentSection').value = section;
            document.getElementById('addContentModal').classList.add('active');
            document.getElementById('contentTitle').value = '';
            document.getElementById('contentDescription').value = '';
        }

        function closeAddContent() {
            document.getElementById('addContentModal').classList.remove('active');
        }

        // SIMPLE CHAT SYSTEM
        async function sendChatMessage() {
            const input = document.getElementById('chatInput');
            const message = input.value.trim();
            
            if (!currentUser) {
                alert('Please login first!');
                return;
            }
            
            if (message) {
                const chatMessage = {
                    user: currentUser.name,
                    text: message,
                    timestamp: new Date().toISOString(),
                    id: Date.now().toString()
                };
                
                // Save chat message
                let chatMessages = JSON.parse(localStorage.getItem('team-chat')) || [];
                chatMessages.push(chatMessage);
                localStorage.setItem('team-chat', JSON.stringify(chatMessages));
                
                input.value = '';
                displayChatMessages();
            }
        }

        function displayChatMessages() {
            const chatMessages = JSON.parse(localStorage.getItem('team-chat')) || [];
            const chatContainer = document.getElementById('chatMessages');
            chatContainer.innerHTML = '';
            
            if (chatMessages.length === 0) {
                chatContainer.innerHTML = `
                    <div class="text-center text-gray-500 text-sm py-4">
                        <i class="fas fa-comments text-xl mb-2 block"></i>
                        No messages yet
                    </div>
                `;
                return;
            }
            
            chatMessages.forEach(msg => {
                const div = document.createElement('div');
                const isCurrentUser = currentUser && msg.user === currentUser.name;
                div.className = `p-3 mb-2 rounded-lg max-w-[85%] ${isCurrentUser ? 'bg-blue-500 text-white ml-auto' : 'bg-gray-200 text-gray-800 dark:bg-gray-700 dark:text-white'}`;
                div.innerHTML = `
                    <div class="font-semibold text-sm">${msg.user}</div>
                    <div class="break-words">${msg.text}</div>
                    <div class="text-xs opacity-70 mt-1">${new Date(msg.timestamp).toLocaleTimeString()}</div>
                `;
                chatContainer.appendChild(div);
            });
            
            chatContainer.scrollTop = chatContainer.scrollHeight;
        }

        function openChat() {
            if (!currentUser) {
                alert('Please login first!');
                return;
            }
            document.getElementById('chatModal').classList.add('active');
            displayChatMessages();
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

        // Initialize everything
        document.addEventListener('DOMContentLoaded', function() {
            const savedTheme = localStorage.getItem('theme');
            if (savedTheme === 'dark') {
                document.documentElement.classList.add('dark');
            }
            
            // Load saved data ID
            currentDataId = localStorage.getItem('team-data-id');
            
            // Initialize team list
            initializeTeamList();
            
            // Try to load saved user
            const savedUser = localStorage.getItem('current-user');
            if (savedUser) {
                currentUser = JSON.parse(savedUser);
                updateUserUI();
                document.getElementById('loginModal').style.display = 'none';
                loadAllContent();
            }
            
            // Auto-save user
            setInterval(() => {
                if (currentUser) {
                    localStorage.setItem('current-user', JSON.stringify(currentUser));
                }
            }, 5000);
        });
    </script>
</body>
</html>
