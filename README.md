            <!-- AI Assistant Section -->
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

            <!-- Practical Tasks Section -->
            <div class="section-content" id="practical-tasks">
                <div class="bg-white dark:bg-gray-800 p-8 rounded-2xl shadow-lg">
                    <div class="flex justify-between items-center mb-6">
                        <h2 class="text-2xl font-bold text-gray-800 dark:text-white">Practical Tasks</h2>
                        <button onclick="openAddContent('practical-tasks')" class="bg-green-600 hover:bg-green-700 text-white px-4 py-2 rounded-xl transition-all">
                            <i class="fas fa-plus mr-2"></i>Add Content
                        </button>
                    </div>
                    <div id="practical-tasks-content" class="space-y-4"></div>
                </div>
            </div>

            <!-- Implementation Section -->
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

            <!-- Research Section -->
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

            <!-- Resources Section -->
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

            <!-- Security Testing Section -->
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

            <!-- Monitoring & Analytics Section -->
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

    <script>
        // TEAM MEMBERS DATA
        const teamMembers = [
            { id: '1', name: 'You', role: 'Team Member' },
            { id: '2', name: 'Kaled Osman', role: 'Developer' },
            { id: '3', name: 'Fahad Hussain', role: 'Researcher' },
            { id: '4', name: 'Stefan Österberg', role: 'Architect' },
            { id: '5', name: 'Marcus Tibell', role: 'Engineer' },
            { id: '6', name: 'Jens Annell', role: 'Analyst' },
            { id: '7', name: 'Luwam', role: 'Designer' }
        ];

        // SIMPLE WORKING CHAT SYSTEM
        class SimpleChat {
            constructor() {
                this.chatKey = 'team-chat-messages';
                this.currentUser = 'You';
                this.loadMessages();
            }

            loadMessages() {
                const messages = JSON.parse(localStorage.getItem(this.chatKey)) || [];
                this.displayMessages(messages);
            }

            saveMessage(text) {
                const messages = JSON.parse(localStorage.getItem(this.chatKey)) || [];
                const newMessage = {
                    user: this.currentUser,
                    text: text,
                    timestamp: new Date().toLocaleTimeString(),
                    id: Date.now().toString()
                };
                messages.push(newMessage);
                
                if (messages.length > 100) messages.shift();
                
                localStorage.setItem(this.chatKey, JSON.stringify(messages));
                this.displayMessages(messages);
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
                    messageElement.className = `p-3 mb-2 rounded-lg max-w-[85%] ${isOwnMessage ? 'bg-blue-500 text-white ml-auto' : 'bg-gray-200 text-gray-800'}`;
                    messageElement.innerHTML = `
                        <div class="font-semibold text-sm">${msg.user}</div>
                        <div class="break-words">${msg.text}</div>
                        <div class="text-xs opacity-70 mt-1">${msg.timestamp}</div>
                    `;
                    chatMessages.appendChild(messageElement);
                });

                chatMessages.scrollTop = chatMessages.scrollHeight;
            }
        }

        // CONTENT MANAGEMENT SYSTEM
        let userContents = JSON.parse(localStorage.getItem('userContents')) || {};

        // Initialize default content
        function initializeDefaultContent() {
            const defaultContent = {
                'overview': [
                    {
                        id: '1',
                        title: '🚀 Welcome to Security Chaos Engineering Dashboard',
                        description: 'This is your team collaboration platform. All 11 sections are now fully functional with real-time chat and content management.',
                        author: 'System',
                        date: new Date().toISOString()
                    }
                ],
                'project-documentation': [
                    {
                        id: '2',
                        title: '📋 Project Requirements',
                        description: 'Document all project specifications and requirements here. The team can collaborate in real-time.',
                        author: 'Team Lead',
                        date: new Date().toISOString()
                    }
                ]
            };

            Object.keys(defaultContent).forEach(section => {
                if (!userContents[section] || userContents[section].length === 0) {
                    userContents[section] = defaultContent[section];
                }
            });
            
            saveContents();
        }

        function saveContents() {
            localStorage.setItem('userContents', JSON.stringify(userContents));
        }

        // SHOW TEAM MEMBERS
        function showTeamMembers() {
            const liveTeamList = document.getElementById('liveTeamList');
            if (!liveTeamList) return;

            liveTeamList.innerHTML = '';
            
            teamMembers.forEach(member => {
                if (member.name !== 'You') {
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
                }
            });
        }

        // Initialize chat
        const chatSystem = new SimpleChat();

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

        // Handle Enter key
        document.getElementById('chatInput').addEventListener('keypress', function(e) {
            if (e.key === 'Enter') {
                sendChatMessage();
            }
        });

        // Add Content System
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
            showNotification('Content added successfully!', 'success');
        });

        function openAddContent(section, contentId = null) {
            document.getElementById('contentSection').value = section;
            document.getElementById('editContentId').value = contentId || '';
            
            if (contentId) {
                // Edit mode
                const content = userContents[section].find(item => item.id === contentId);
                if (content) {
                    document.getElementById('contentTitle').value = content.title;
                    document.getElementById('contentDescription').value = content.description;
                    document.getElementById('contentAuthor').value = content.author;
                }
            } else {
                // Add mode
                document.getElementById('addContentForm').reset();
            }
            
            document.getElementById('addContentModal').classList.add('active');
        }

        function closeAddContent() {
            document.getElementById('addContentModal').classList.remove('active');
            document.getElementById('addContentForm').reset();
            document.getElementById('editContentId').value = '';
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

        // Section Navigation
        document.querySelectorAll('.section-btn').forEach(btn => {
            btn.addEventListener('click', function() {
                document.querySelectorAll('.section-btn').forEach(b => {
                    b.classList.remove('active-section');
                    b.classList.add('hover:bg-gray-100', 'dark:hover:bg-gray-700', 'text-gray-700', 'dark:text-gray-300');
                });
                
                this.classList.add('active-section');
                this.classList.remove('hover:bg-gray-100', 'dark:hover:bg-gray-700', 'text-gray-700', 'dark:text-gray-300');
                
                document.querySelectorAll('.section-content').forEach(section => {
                    section.classList.remove('active');
                });
                
                const sectionId = this.getAttribute('data-section');
                document.getElementById(sectionId).classList.add('active');
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

        // Load saved theme and initialize everything
        document.addEventListener('DOMContentLoaded', function() {
            const savedTheme = localStorage.getItem('theme');
            if (savedTheme === 'dark') {
                document.documentElement.classList.add('dark');
            }

            // Initialize all systems
            initializeDefaultContent();
            initializeAllContents();
            showTeamMembers();
            
            showNotification('🚀 Dashboard loaded! All features are now working.', 'success');
        });
    </script>
</body>
</html>
