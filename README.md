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
        .active-section { background: linear-gradient(135deg, #3b82f6, #1d4ed8); color: white; }
        .gradient-header { background: linear-gradient(135deg, #2563eb, #1e40af); }
        .online-indicator { width: 8px; height: 8px; background: #10B981; border-radius: 50%; animation: pulse 2s infinite; }
        @keyframes pulse { 
            0% { transform: scale(1); opacity: 1; }
            50% { transform: scale(1.2); opacity: 0.7; }
            100% { transform: scale(1); opacity: 1; }
        }
        .realtime-badge { background: linear-gradient(135deg, #10B981, #059669); color: white; padding: 4px 12px; border-radius: 20px; font-size: 0.75rem; font-weight: 600; }
        .modal { display: none; position: fixed; top: 0; left: 0; width: 100%; height: 100%; background: rgba(0, 0, 0, 0.5); z-index: 1000; align-items: center; justify-content: center; }
        .modal.active { display: flex; }
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
                    <input type="text" id="contentTitle" class="w-full p-3 border border-gray-300 dark:border-gray-600 rounded-xl focus:ring-2 focus:ring-blue-500 dark:bg-gray-700 dark:text-white" required>
                </div>
                <div>
                    <label class="block text-sm font-medium mb-2 text-gray-700 dark:text-gray-300">Content</label>
                    <textarea id="contentDescription" class="w-full p-3 border border-gray-300 dark:border-gray-600 rounded-xl focus:ring-2 focus:ring-blue-500 dark:bg-gray-700 dark:text-white h-40" required></textarea>
                </div>
                <div>
                    <label class="block text-sm font-medium mb-2 text-gray-700 dark:text-gray-300">Author</label>
                    <select id="contentAuthor" class="w-full p-3 border border-gray-300 dark:border-gray-600 rounded-xl focus:ring-2 focus:ring-blue-500 dark:bg-gray-700 dark:text-white">
                        <option value="Kaled Osman">Kaled Osman</option>
                        <option value="Fahad Hussain">Fahad Hussain</option>
                        <option value="Stefan Österberg">Stefan Österberg</option>
                        <option value="Marcus Tibell">Marcus Tibell</option>
                        <option value="Jens Annell">Jens Annell</option>
                        <option value="Luwam">Luwam</option>
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
        <div class="bg-white dark:bg-gray-800 p-4 rounded-xl mx-4 shadow-2xl" style="width: 500px; max-width: 90vw;">
            <div class="flex justify-between items-center mb-3">
                <h2 class="text-xl font-bold text-gray-800 dark:text-white flex items-center gap-2">
                    <span class="realtime-badge">
                        <span class="online-indicator"></span>
                        TEAM CHAT
                    </span>
                </h2>
                <button onclick="closeTeamChat()" class="text-gray-500 hover:text-gray-700">
                    <i class="fas fa-times"></i>
                </button>
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
        <div class="w-80 bg-gray-50 dark:bg-gray-800 flex-shrink-0 theme-transition p-6">
            <div class="mb-8">
                <h1 class="text-2xl font-bold text-blue-600 dark:text-blue-400">Security Chaos Engineering</h1>
                <p class="text-gray-600 dark:text-gray-400 mt-2">Group 1 Dashboard</p>
                
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
                            <div class="w-10 h-10 bg-gradient-to-r from-blue-500 to-purple-500 rounded-full flex items-center justify-center text-white font-bold">T</div>
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

            <!-- Navigation - 11 SECTIONS -->
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
                <button class="section-btn w-full text-left p-4 rounded-xl theme-transition hover:bg-gray-100 dark:hover:bg-gray-700 text-gray-700 dark:text-gray-300" data-section="security-testing">
                    <i class="fas fa-shield-alt mr-3 w-6 text-center"></i>Security Testing
                </button>
                <button class="section-btn w-full text-left p-4 rounded-xl theme-transition hover:bg-gray-100 dark:hover:bg-gray-700 text-gray-700 dark:text-gray-300" data-section="monitoring-analytics">
                    <i class="fas fa-chart-line mr-3 w-6 text-center"></i>Monitoring & Analytics
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
                        <div class="text-sm opacity-90">Welcome to the Team Dashboard</div>
                        <div class="flex items-center space-x-2 bg-white/30 px-4 py-2 rounded-full backdrop-blur-sm border-2 border-green-300">
                            <span class="online-indicator"></span>
                            <span class="text-sm font-semibold">LIVE MODE ACTIVE</span>
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
                <div class="section-content bg-white dark:bg-gray-800 p-8 mb-8 rounded-2xl shadow-lg" id="overview">
                    <div class="flex justify-between items-center mb-6">
                        <h2 class="text-3xl font-bold text-gray-800 dark:text-white">Project Overview</h2>
                        <button onclick="openAddContent('overview')" class="bg-green-600 hover:bg-green-700 text-white px-4 py-2 rounded-xl transition-all">
                            <i class="fas fa-plus mr-2"></i>Add Content
                        </button>
                    </div>
                    <div id="overview-content" class="space-y-4 mb-8"></div>
                    <div class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-4 gap-6">
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

                <!-- Other Sections (initially hidden) -->
                <div class="section-content bg-white dark:bg-gray-800 p-8 mb-8 rounded-2xl shadow-lg hidden" id="project-documentation">
                    <div class="flex justify-between items-center mb-6">
                        <h2 class="text-3xl font-bold text-gray-800 dark:text-white">Project Documentation</h2>
                        <button onclick="openAddContent('project-documentation')" class="bg-green-600 hover:bg-green-700 text-white px-4 py-2 rounded-xl transition-all">
                            <i class="fas fa-plus mr-2"></i>Add Content
                        </button>
                    </div>
                    <div id="project-documentation-content" class="space-y-4"></div>
                </div>

                <div class="section-content bg-white dark:bg-gray-800 p-8 mb-8 rounded-2xl shadow-lg hidden" id="team-collaboration">
                    <div class="flex justify-between items-center mb-6">
                        <h2 class="text-3xl font-bold text-gray-800 dark:text-white">Team Collaboration</h2>
                        <button onclick="openAddContent('team-collaboration')" class="bg-green-600 hover:bg-green-700 text-white px-4 py-2 rounded-xl transition-all">
                            <i class="fas fa-plus mr-2"></i>Add Content
                        </button>
                    </div>
                    <div id="team-collaboration-content" class="space-y-4"></div>
                </div>

                <div class="section-content bg-white dark:bg-gray-800 p-8 mb-8 rounded-2xl shadow-lg hidden" id="team-updates">
                    <div class="flex justify-between items-center mb-6">
                        <h2 class="text-3xl font-bold text-gray-800 dark:text-white">Team Updates</h2>
                        <button onclick="openAddContent('team-updates')" class="bg-green-600 hover:bg-green-700 text-white px-4 py-2 rounded-xl transition-all">
                            <i class="fas fa-plus mr-2"></i>Add Content
                        </button>
                    </div>
                    <div id="team-updates-content" class="space-y-4"></div>
                </div>

                <div class="section-content bg-white dark:bg-gray-800 p-8 mb-8 rounded-2xl shadow-lg hidden" id="ai-assistant">
                    <div class="flex justify-between items-center mb-6">
                        <h2 class="text-3xl font-bold text-gray-800 dark:text-white">AI Assistant</h2>
                        <button onclick="openAddContent('ai-assistant')" class="bg-green-600 hover:bg-green-700 text-white px-4 py-2 rounded-xl transition-all">
                            <i class="fas fa-plus mr-2"></i>Add Content
                        </button>
                    </div>
                    <div id="ai-assistant-content" class="space-y-4"></div>
                </div>

                <div class="section-content bg-white dark:bg-gray-800 p-8 mb-8 rounded-2xl shadow-lg hidden" id="practical-tasks">
                    <div class="flex justify-between items-center mb-6">
                        <h2 class="text-3xl font-bold text-gray-800 dark:text-white">Practical Tasks</h2>
                        <button onclick="openAddContent('practical-tasks')" class="bg-green-600 hover:bg-green-700 text-white px-4 py-2 rounded-xl transition-all">
                            <i class="fas fa-plus mr-2"></i>Add Content
                        </button>
                    </div>
                    <div id="practical-tasks-content" class="space-y-4"></div>
                </div>

                <div class="section-content bg-white dark:bg-gray-800 p-8 mb-8 rounded-2xl shadow-lg hidden" id="implementation">
                    <div class="flex justify-between items-center mb-6">
                        <h2 class="text-3xl font-bold text-gray-800 dark:text-white">Implementation</h2>
                        <button onclick="openAddContent('implementation')" class="bg-green-600 hover:bg-green-700 text-white px-4 py-2 rounded-xl transition-all">
                            <i class="fas fa-plus mr-2"></i>Add Content
                        </button>
                    </div>
                    <div id="implementation-content" class="space-y-4"></div>
                </div>

                <div class="section-content bg-white dark:bg-gray-800 p-8 mb-8 rounded-2xl shadow-lg hidden" id="research">
                    <div class="flex justify-between items-center mb-6">
                        <h2 class="text-3xl font-bold text-gray-800 dark:text-white">Research</h2>
                        <button onclick="openAddContent('research')" class="bg-green-600 hover:bg-green-700 text-white px-4 py-2 rounded-xl transition-all">
                            <i class="fas fa-plus mr-2"></i>Add Content
                        </button>
                    </div>
                    <div id="research-content" class="space-y-4"></div>
                </div>

                <div class="section-content bg-white dark:bg-gray-800 p-8 mb-8 rounded-2xl shadow-lg hidden" id="resources">
                    <div class="flex justify-between items-center mb-6">
                        <h2 class="text-3xl font-bold text-gray-800 dark:text-white">Resources</h2>
                        <button onclick="openAddContent('resources')" class="bg-green-600 hover:bg-green-700 text-white px-4 py-2 rounded-xl transition-all">
                            <i class="fas fa-plus mr-2"></i>Add Content
                        </button>
                    </div>
                    <div id="resources-content" class="space-y-4"></div>
                </div>

                <div class="section-content bg-white dark:bg-gray-800 p-8 mb-8 rounded-2xl shadow-lg hidden" id="security-testing">
                    <div class="flex justify-between items-center mb-6">
                        <h2 class="text-3xl font-bold text-gray-800 dark:text-white">Security Testing</h2>
                        <button onclick="openAddContent('security-testing')" class="bg-green-600 hover:bg-green-700 text-white px-4 py-2 rounded-xl transition-all">
                            <i class="fas fa-plus mr-2"></i>Add Content
                        </button>
                    </div>
                    <div id="security-testing-content" class="space-y-4"></div>
                </div>

                <div class="section-content bg-white dark:bg-gray-800 p-8 mb-8 rounded-2xl shadow-lg hidden" id="monitoring-analytics">
                    <div class="flex justify-between items-center mb-6">
                        <h2 class="text-3xl font-bold text-gray-800 dark:text-white">Monitoring & Analytics</h2>
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
    // TEAM MEMBERS DATA
    const teamMembers = [
        { id: 1, name: "Kaled Osman", role: "Team Lead", online: true },
        { id: 2, name: "Fahad Hussain", role: "Researcher", online: true },
        { id: 3, name: "Stefan Österberg", role: "Architect", online: true },
        { id: 4, name: "Marcus Tibell", role: "Engineer", online: true },
        { id: 5, name: "Jens Annell", role: "Analyst", online: true },
        { id: 6, name: "Luwam", role: "Designer", online: true }
    ];

    // PERMANENT CHAT SYSTEM
    class PermanentChatSystem {
        constructor() {
            this.chatKey = 'team-chat-messages';
            this.deletedKey = 'team-chat-deleted';
            this.currentUser = 'You';
            this.loadMessages();
        }

        loadMessages() {
            const messages = JSON.parse(localStorage.getItem(this.chatKey)) || [];
            const deletedMessages = JSON.parse(localStorage.getItem(this.deletedKey)) || [];
            
            const activeMessages = messages.filter(msg => 
                !deletedMessages.some(deleted => deleted.id === msg.id && deleted.user === this.currentUser)
            );
            
            this.displayMessages(activeMessages);
        }

        saveMessage(text) {
            const messages = JSON.parse(localStorage.getItem(this.chatKey)) || [];
            const newMessage = {
                id: Date.now().toString(),
                user: this.currentUser,
                text: text,
                timestamp: new Date().toLocaleTimeString()
            };
            messages.push(newMessage);
            localStorage.setItem(this.chatKey, JSON.stringify(messages));
            this.loadMessages();
        }

        deleteMessage(messageId) {
            const deletedMessages = JSON.parse(localStorage.getItem(this.deletedKey)) || [];
            deletedMessages.push({
                id: messageId,
                user: this.currentUser,
                deletedAt: new Date().toISOString()
            });
            localStorage.setItem(this.deletedKey, JSON.stringify(deletedMessages));
            this.loadMessages();
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
                        <button onclick="chatSystem.deleteMessage('${msg.id}')" 
                                class="absolute -top-2 -right-2 bg-red-500 text-white rounded-full w-5 h-5 text-xs opacity-0 group-hover:opacity-100 transition-opacity">
                            <i class="fas fa-times"></i>
                        </button>
                    ` : ''}
                `;
                chatMessages.appendChild(messageElement);
            });

            chatMessages.scrollTop = chatMessages.scrollHeight;
        }
    }

    // Initialize chat system
    const chatSystem = new PermanentChatSystem();

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
            chatSystem.saveMessage(message);
            input.value = '';
        }
    }

    // Handle Enter key in chat
    document.getElementById('chatInput')?.addEventListener('keypress', function(e) {
        if (e.key === 'Enter') {
            sendChatMessage();
        }
    });

    // SHOW TEAM MEMBERS
    function showTeamMembers() {
        const liveTeamList = document.getElementById('liveTeamList');
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

    // CONTENT MANAGEMENT SYSTEM
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
            const content = userContents[section].find(item => item.id === contentId);
            if (content) {
                document.getElementById('contentTitle').value = content.title;
                document.getElementById('contentDescription').value = content.description;
                document.getElementById('contentAuthor').value = content.author;
            }
        } else {
            document.getElementById('modalTitle').textContent = 'Add New Content';
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
            showNotification('Content deleted successfully!', 'success');
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
        const html = document.documentElement;
        if (html.classList.contains('dark')) {
            html.classList.remove('dark');
            localStorage.setItem('theme', 'light');
        } else {
            html.classList.add('dark');
            localStorage.setItem('theme', 'dark');
        }
        showNotification('Theme changed!', 'success');
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
        const savedTheme = localStorage.getItem('theme');
        if (savedTheme === 'dark') {
            document.documentElement.classList.add('dark');
        }

        setupNavigation();
        fetchSystemData();
        initializeAllContents();
        showTeamMembers(); // 👈 DENNA VISAR TEAM MEMBERS!
        
        showNotification('Welcome to the Team Dashboard! All features are active.', 'success');
    });
</script>


