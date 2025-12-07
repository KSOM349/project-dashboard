<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="utf-8" />
  <meta name="viewport" content="width=device-width,initial-scale=1" />
  <title>Security Chaos Engineering Dashboard — Realtime</title>
  <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
  <script src="https://cdn.tailwindcss.com"></script>
  <style>
    .theme-transition { transition: all 0.25s ease; }
    .active-section { background: linear-gradient(135deg,#3b82f6,#1d4ed8); color: white; }
    .online-indicator { width: 8px; height: 8px; background: #10B981; border-radius: 50%; animation: pulse 2s infinite; }
    @keyframes pulse { 0%{transform:scale(1);opacity:1}50%{transform:scale(1.2);opacity:0.7}100%{transform:scale(1);opacity:1}}
    .modal { display:none; position:fixed; inset:0; background:rgba(0,0,0,0.5); z-index:1000; align-items:center; justify-content:center; }
    .modal.active { display:flex; }
    .section-content { display:none; }
    .section-content.active { display:block; }
    .loading { display:inline-block; width:20px; height:20px; border:3px solid #f3f3f3; border-top:3px solid #3498db; border-radius:50%; animation:spin 1s linear infinite; }
    @keyframes spin {0%{transform:rotate(0)}100%{transform:rotate(360deg)}}
  </style>
</head>
<body class="bg-gray-50 dark:bg-gray-900 theme-transition text-gray-800">

  <!-- Login Modal -->
  <div id="loginModal" class="modal active">
    <div class="bg-white dark:bg-gray-800 p-8 rounded-2xl max-w-md w-full mx-4 shadow-2xl">
      <h2 class="text-2xl font-bold mb-6 text-center">Login to Dashboard</h2>
      <form id="loginForm" class="space-y-4">
        <div>
          <label class="block text-sm font-medium mb-2">Name</label>
          <select id="userSelect" class="w-full p-3 border rounded-xl dark:bg-gray-700 dark:text-white">
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
          <button type="submit" class="flex-1 bg-blue-600 hover:bg-blue-700 text-white py-3 rounded-xl">Login</button>
          <button type="button" id="loginCancel" class="flex-1 bg-gray-500 hover:bg-gray-600 text-white py-3 rounded-xl">Cancel</button>
        </div>
        <p class="text-sm text-gray-500 mt-2">Select your name and press Login. Chat and content are shared with the team.</p>
      </form>
    </div>
  </div>

  <!-- Add Content Modal -->
  <div id="addContentModal" class="modal">
    <div class="bg-white dark:bg-gray-800 p-8 rounded-2xl max-w-2xl w-full mx-4 shadow-2xl">
      <h2 class="text-2xl font-bold mb-4 text-center">Add Content</h2>
      <form id="addContentForm" class="space-y-4">
        <input type="hidden" id="contentSection" />
        <div>
          <label class="block text-sm font-medium mb-2">Title</label>
          <input id="contentTitle" required class="w-full p-3 border rounded-xl dark:bg-gray-700 dark:text-white" />
        </div>
        <div>
          <label class="block text-sm font-medium mb-2">Content</label>
          <textarea id="contentDescription" required class="w-full p-3 border rounded-xl dark:bg-gray-700 dark:text-white h-40"></textarea>
        </div>
        <div class="flex gap-3">
          <button type="submit" class="flex-1 bg-green-600 hover:bg-green-700 text-white py-3 rounded-xl">Save for Everyone</button>
          <button type="button" id="cancelAddContent" class="flex-1 bg-gray-500 hover:bg-gray-600 text-white py-3 rounded-xl">Cancel</button>
        </div>
      </form>
    </div>
  </div>

  <!-- Chat Modal -->
  <div id="chatModal" class="modal">
    <div class="bg-white dark:bg-gray-800 p-6 rounded-2xl mx-4 shadow-2xl w-[500px] max-w-[90vw]">
      <div class="flex justify-between items-center mb-4">
        <h2 class="text-xl font-bold flex items-center gap-2"><span class="online-indicator"></span> Team Chat</h2>
        <button id="closeChatBtn" class="text-gray-500 hover:text-gray-700"><i class="fas fa-times"></i></button>
      </div>
      <div id="chatMessages" class="overflow-y-auto mb-4 p-3 border rounded-lg bg-gray-50 dark:bg-gray-900" style="height:300px;">
        <div class="text-center text-gray-500">Start chatting with your team!</div>
      </div>
      <div class="flex gap-2">
        <input id="chatInput" placeholder="Type message..." class="flex-1 p-2 border rounded-lg dark:bg-gray-800 dark:text-white" />
        <button id="sendChatBtn" class="bg-blue-600 hover:bg-blue-700 text-white px-4 py-2 rounded-lg"><i class="fas fa-paper-plane"></i></button>
      </div>
    </div>
  </div>

  <!-- Main Layout -->
  <div class="flex min-h-screen">
    <!-- Sidebar -->
    <aside class="w-80 bg-gray-50 dark:bg-gray-800 flex-shrink-0 p-6">
      <div class="mb-8">
        <h1 class="text-2xl font-bold text-blue-600">Security Chaos Engineering</h1>
        <p class="text-gray-600 dark:text-gray-400 mt-2">Group 1 Dashboard</p>
        <div class="mt-3 p-3 bg-gradient-to-r from-green-500 to-emerald-600 text-white rounded-lg shadow-lg">
          <div class="flex items-center justify-between">
            <div class="flex items-center gap-2">
              <span class="online-indicator"></span>
              <span class="font-semibold">TEAM WORKING</span>
            </div>
            <i class="fas fa-bolt"></i>
          </div>
          <div class="text-xs opacity-90 mt-1">All changes are saved to Firebase</div>
        </div>
      </div>

      <!-- User Card -->
      <div class="w-full mb-6">
        <div class="bg-white dark:bg-gray-700 p-4 rounded-xl shadow-lg border-2 border-blue-200 dark:border-blue-800">
          <div class="flex items-center space-x-3 mb-3">
            <div class="relative">
              <div id="userAvatar" class="w-10 h-10 bg-gradient-to-r from-blue-500 to-purple-500 rounded-full flex items-center justify-center text-white font-bold">Y</div>
              <span class="online-indicator absolute -top-1 -right-1 border-2 border-white dark:border-gray-800"></span>
            </div>
            <div class="flex-1 min-w-0">
              <p id="userName" class="font-semibold text-gray-800 dark:text-white truncate">You</p>
              <p id="userRole" class="text-sm text-gray-600 dark:text-gray-400 truncate">Select your name to login</p>
            </div>
          </div>
          <button id="openChatBtn" class="w-full bg-blue-600 hover:bg-blue-700 text-white py-3 rounded-lg"><i class="fas fa-comments mr-2"></i>Team Chat</button>
          <button id="logoutBtn" class="w-full bg-red-600 hover:bg-red-700 text-white py-3 rounded-lg mt-2"><i class="fas fa-sign-out-alt mr-2"></i>Logout</button>
        </div>
      </div>

      <!-- Team Members -->
      <div class="mb-6">
        <div class="p-4 rounded-xl shadow-lg border-2 border-green-200 dark:border-green-800 bg-gradient-to-br from-blue-50 to-green-50 dark:from-gray-800 dark:to-gray-700">
          <h3 class="font-semibold mb-3 flex items-center gap-2"><span class="online-indicator"></span> Team Members</h3>
          <div id="teamList" class="space-y-2"></div>
        </div>
      </div>

      <!-- Navigation -->
      <nav class="space-y-2 mb-8">
        <button class="section-btn w-full text-left p-4 rounded-xl theme-transition active-section" data-section="overview"><i class="fas fa-home mr-3"></i>Overview</button>
        <button class="section-btn w-full text-left p-4 rounded-xl theme-transition hover:bg-gray-100 dark:hover:bg-gray-700" data-section="project-documentation"><i class="fas fa-folder mr-3"></i>Project Documentation</button>
        <button class="section-btn w-full text-left p-4 rounded-xl theme-transition hover:bg-gray-100 dark:hover:bg-gray-700" data-section="team-collaboration"><i class="fas fa-users mr-3"></i>Team Collaboration</button>
        <button class="section-btn w-full text-left p-4 rounded-xl theme-transition hover:bg-gray-100 dark:hover:bg-gray-700" data-section="team-updates"><i class="fas fa-bullhorn mr-3"></i>Team Updates</button>
        <button class="section-btn w-full text-left p-4 rounded-xl theme-transition hover:bg-gray-100 dark:hover:bg-gray-700" data-section="ai-assistant"><i class="fas fa-robot mr-3"></i>AI Assistant</button>
        <button class="section-btn w-full text-left p-4 rounded-xl theme-transition hover:bg-gray-100 dark:hover:bg-gray-700" data-section="practical-tasks"><i class="fas fa-tasks mr-3"></i>Practical Tasks</button>
        <button class="section-btn w-full text-left p-4 rounded-xl theme-transition hover:bg-gray-100 dark:hover:bg-gray-700" data-section="implementation"><i class="fas fa-code mr-3"></i>Implementation</button>
        <button class="section-btn w-full text-left p-4 rounded-xl theme-transition hover:bg-gray-100 dark:hover:bg-gray-700" data-section="research"><i class="fas fa-search mr-3"></i>Research</button>
        <button class="section-btn w-full text-left p-4 rounded-xl theme-transition hover:bg-gray-100 dark:hover:bg-gray-700" data-section="resources"><i class="fas fa-book mr-3"></i>Resources</button>
        <button class="section-btn w-full text-left p-4 rounded-xl theme-transition hover:bg-gray-100 dark:hover:bg-gray-700" data-section="security-testing"><i class="fas fa-shield-alt mr-3"></i>Security Testing</button>
        <button class="section-btn w-full text-left p-4 rounded-xl theme-transition hover:bg-gray-100 dark:hover:bg-gray-700" data-section="monitoring-analytics"><i class="fas fa-chart-line mr-3"></i>Monitoring & Analytics</button>
      </nav>

      <div class="mt-auto">
        <button id="toggleThemeBtn" class="w-full p-4 rounded-xl theme-transition bg-gray-100 dark:bg-gray-700 text-gray-700 dark:text-gray-300 flex items-center justify-center">
          <i class="fas fa-moon mr-3"></i>Toggle Theme
        </button>
      </div>
    </aside>

    <!-- Main Content -->
    <main class="flex-1 p-8">
      <header class="mb-8">
        <h1 class="text-3xl font-bold">Security Chaos Engineering Dashboard</h1>
        <p class="text-gray-600 dark:text-gray-400">Group 1 - Team Collaboration Platform</p>
      </header>

      <!-- Overview Section -->
      <section id="overview" class="section-content active">
        <div class="bg-white dark:bg-gray-800 p-8 rounded-2xl shadow-lg">
          <div class="flex justify-between items-center mb-6">
            <h2 class="text-2xl font-bold">Overview</h2>
            <button id="addOverviewBtn" class="bg-green-600 hover:bg-green-700 text-white px-4 py-2 rounded-xl"><i class="fas fa-plus mr-2"></i>Add Content</button>
          </div>
          <div id="overview-content" class="space-y-4"></div>
        </div>
      </section>

      <!-- Project Documentation -->
      <section id="project-documentation" class="section-content">
        <div class="bg-white dark:bg-gray-800 p-8 rounded-2xl shadow-lg">
          <div class="flex justify-between items-center mb-6">
            <h2 class="text-2xl font-bold">Project Documentation</h2>
            <button class="add-content-btn bg-green-600 hover:bg-green-700 text-white px-4 py-2 rounded-xl" data-section="project-documentation"><i class="fas fa-plus mr-2"></i>Add Content</button>
          </div>
          <div id="project-documentation-content" class="space-y-4"></div>
        </div>
      </section>

      <!-- Team Collaboration -->
      <section id="team-collaboration" class="section-content">
        <div class="bg-white dark:bg-gray-800 p-8 rounded-2xl shadow-lg">
          <div class="flex justify-between items-center mb-6">
            <h2 class="text-2xl font-bold">Team Collaboration</h2>
            <button class="add-content-btn bg-green-600 hover:bg-green-700 text-white px-4 py-2 rounded-xl" data-section="team-collaboration"><i class="fas fa-plus mr-2"></i>Add Content</button>
          </div>
          <div id="team-collaboration-content" class="space-y-4"></div>
        </div>
      </section>

      <!-- Team Updates -->
      <section id="team-updates" class="section-content">
        <div class="bg-white dark:bg-gray-800 p-8 rounded-2xl shadow-lg">
          <div class="flex justify-between items-center mb-6">
            <h2 class="text-2xl font-bold">Team Updates</h2>
            <button class="add-content-btn bg-green-600 hover:bg-green-700 text-white px-4 py-2 rounded-xl" data-section="team-updates"><i class="fas fa-plus mr-2"></i>Add Content</button>
          </div>
          <div id="team-updates-content" class="space-y-4"></div>
        </div>
      </section>

      <!-- AI Assistant -->
      <section id="ai-assistant" class="section-content">
        <div class="bg-white dark:bg-gray-800 p-8 rounded-2xl shadow-lg">
          <div class="flex justify-between items-center mb-6">
            <h2 class="text-2xl font-bold">AI Assistant</h2>
            <button class="add-content-btn bg-green-600 hover:bg-green-700 text-white px-4 py-2 rounded-xl" data-section="ai-assistant"><i class="fas fa-plus mr-2"></i>Add Content</button>
          </div>
          <div id="ai-assistant-content" class="space-y-4"></div>
        </div>
      </section>

      <!-- Practical Tasks -->
      <section id="practical-tasks" class="section-content">
        <div class="bg-white dark:bg-gray-800 p-8 rounded-2xl shadow-lg">
          <div class="flex justify-between items-center mb-6">
            <h2 class="text-2xl font-bold">Practical Tasks</h2>
            <button class="add-content-btn bg-green-600 hover:bg-green-700 text-white px-4 py-2 rounded-xl" data-section="practical-tasks"><i class="fas fa-plus mr-2"></i>Add Content</button>
          </div>
          <div id="practical-tasks-content" class="space-y-4"></div>
        </div>
      </section>

      <!-- Implementation -->
      <section id="implementation" class="section-content">
        <div class="bg-white dark:bg-gray-800 p-8 rounded-2xl shadow-lg">
          <div class="flex justify-between items-center mb-6">
            <h2 class="text-2xl font-bold">Implementation</h2>
            <button class="add-content-btn bg-green-600 hover:bg-green-700 text-white px-4 py-2 rounded-xl" data-section="implementation"><i class="fas fa-plus mr-2"></i>Add Content</button>
          </div>
          <div id="implementation-content" class="space-y-4"></div>
        </div>
      </section>

      <!-- Research -->
      <section id="research" class="section-content">
        <div class="bg-white dark:bg-gray-800 p-8 rounded-2xl shadow-lg">
          <div class="flex justify-between items-center mb-6">
            <h2 class="text-2xl font-bold">Research</h2>
            <button class="add-content-btn bg-green-600 hover:bg-green-700 text-white px-4 py-2 rounded-xl" data-section="research"><i class="fas fa-plus mr-2"></i>Add Content</button>
          </div>
          <div id="research-content" class="space-y-4"></div>
        </div>
      </section>

      <!-- Resources -->
      <section id="resources" class="section-content">
        <div class="bg-white dark:bg-gray-800 p-8 rounded-2xl shadow-lg">
          <div class="flex justify-between items-center mb-6">
            <h2 class="text-2xl font-bold">Resources</h2>
            <button class="add-content-btn bg-green-600 hover:bg-green-700 text-white px-4 py-2 rounded-xl" data-section="resources"><i class="fas fa-plus mr-2"></i>Add Content</button>
          </div>
          <div id="resources-content" class="space-y-4"></div>
        </div>
      </section>

      <!-- Security Testing -->
      <section id="security-testing" class="section-content">
        <div class="bg-white dark:bg-gray-800 p-8 rounded-2xl shadow-lg">
          <div class="flex justify-between items-center mb-6">
            <h2 class="text-2xl font-bold">Security Testing</h2>
            <button class="add-content-btn bg-green-600 hover:bg-green-700 text-white px-4 py-2 rounded-xl" data-section="security-testing"><i class="fas fa-plus mr-2"></i>Add Content</button>
          </div>
          <div id="security-testing-content" class="space-y-4"></div>
        </div>
      </section>

      <!-- Monitoring & Analytics -->
      <section id="monitoring-analytics" class="section-content">
        <div class="bg-white dark:bg-gray-800 p-8 rounded-2xl shadow-lg">
          <div class="flex justify-between items-center mb-6">
            <h2 class="text-2xl font-bold">Monitoring & Analytics</h2>
            <button class="add-content-btn bg-green-600 hover:bg-green-700 text-white px-4 py-2 rounded-xl" data-section="monitoring-analytics"><i class="fas fa-plus mr-2"></i>Add Content</button>
          </div>
          <div id="monitoring-analytics-content" class="space-y-4"></div>
        </div>
      </section>

    </main>
  </div>

  <!-- Firebase SDK (compat) -->
  <script src="https://www.gstatic.com/firebasejs/9.22.1/firebase-app-compat.js"></script>
  <script src="https://www.gstatic.com/firebasejs/9.22.1/firebase-database-compat.js"></script>

  <script>
    /******************************************************************
     * Firebase configuration (YOUR project values are already inserted)
     * These values come from your Firebase console as you provided.
     ******************************************************************/
    const firebaseConfig = {
      apiKey: "AIzaSyDJsZ4LZVrBucavpTdhXbKxyE_BFeZFFKs",
      authDomain: "fir-console-df3e9.firebaseapp.com",
      databaseURL: "https://fir-console-df3e9-default-rtdb.europe-west1.firebasedatabase.app",
      projectId: "fir-console-df3e9",
      storageBucket: "fir-console-df3e9.firebasestorage.app",
      messagingSenderId: "750795336412",
      appId: "1:750795336412:web:abfd0c06941a9418abe219",
      measurementId: "G-EVZ9F72HMD"
    };

    // Initialize Firebase
    firebase.initializeApp(firebaseConfig);
    const db = firebase.database();

    /******************************************************************
     * App state
     ******************************************************************/
    const teamMembers = [
      { name: 'Kaled Osman', role: 'Developer', avatar: 'K' },
      { name: 'Marcus Tibell', role: 'Engineer', avatar: 'M' },
      { name: 'Jens Annell', role: 'Analyst', avatar: 'J' },
      { name: 'Fahad Hussain', role: 'Researcher', avatar: 'F' },
      { name: 'Luwam', role: 'Designer', avatar: 'L' },
      { name: 'Stefan Österberg', role: 'Architect', avatar: 'S' },
      { name: 'Najmaddin', role: 'Security Expert', avatar: 'N' }
    ];
    let currentUser = null;

    /******************************************************************
     * Helper: DOM shorteners
     ******************************************************************/
    const $ = (id) => document.getElementById(id);

    /******************************************************************
     * Initialize UI & event listeners
     ******************************************************************/
    function initializeTeamList() {
      const teamList = $('teamList');
      teamList.innerHTML = '';
      teamMembers.forEach(member => {
        const div = document.createElement('div');
        div.className = 'flex items-center space-x-3 p-2 bg-white dark:bg-gray-700 rounded-lg';
        div.innerHTML = `
          <div class="relative">
            <div class="w-8 h-8 bg-gradient-to-r from-blue-500 to-purple-500 rounded-full flex items-center justify-center text-white text-sm font-bold">${member.avatar}</div>
            <span class="online-indicator absolute -top-1 -right-1 border-2 border-white dark:border-gray-700"></span>
          </div>
          <div class="flex-1">
            <div class="font-medium text-gray-800 dark:text-white text-sm">${member.name}</div>
            <div class="text-xs text-gray-600 dark:text-gray-400">${member.role}</div>
          </div>
        `;
        teamList.appendChild(div);
      });
    }

    function updateUserUI() {
      if (currentUser) {
        $('userName').textContent = currentUser.name;
        $('userRole').textContent = currentUser.role;
        $('userAvatar').textContent = currentUser.avatar;
      }
    }

    // Login form
    $('loginForm').addEventListener('submit', function(e) {
      e.preventDefault();
      const selected = $('userSelect').value;
      const user = teamMembers.find(m => m.name === selected);
      if (user) {
        currentUser = user;
        localStorage.setItem('current-user', JSON.stringify(currentUser)); // optional persistence
        $('loginModal').classList.remove('active');
        updateUserUI();
        // Load initial data
        startRealtimeListeners();
        alert(`Welcome ${user.name}!`);
      }
    });

    // Cancel login - hide modal only if a current user exists
    $('loginCancel').addEventListener('click', () => {
      if (!currentUser) {
        // keep modal open if no user
        $('loginModal').classList.add('active');
      } else {
        $('loginModal').classList.remove('active');
      }
    });

    // Logout
    $('logoutBtn').addEventListener('click', () => {
      currentUser = null;
      localStorage.removeItem('current-user');
      $('userName').textContent = 'You';
      $('userRole').textContent = 'Select your name to login';
      $('userAvatar').textContent = 'Y';
      $('loginModal').classList.add('active');
    });

    // Chat open/close
    $('openChatBtn').addEventListener('click', () => {
      if (!currentUser) { alert('Please login first!'); return; }
      $('chatModal').classList.add('active');
      focusChatInput();
    });
    $('closeChatBtn').addEventListener('click', () => $('chatModal').classList.remove('active'));
    $('sendChatBtn').addEventListener('click', sendChatMessage);
    $('chatInput').addEventListener('keypress', function(e) { if (e.key === 'Enter') sendChatMessage(); });

    // Add content buttons
    document.querySelectorAll('.add-content-btn, #addOverviewBtn').forEach(btn => {
      btn.addEventListener('click', (ev) => {
        const section = ev.currentTarget.getAttribute('data-section') || 'overview';
        openAddContentModal(section);
      });
    });
    $('cancelAddContent').addEventListener('click', closeAddContentModal);
    $('addContentForm').addEventListener('submit', function(e) {
      e.preventDefault();
      const section = $('contentSection').value;
      const title = $('contentTitle').value.trim();
      const description = $('contentDescription').value.trim();
      if (!title || !description) return alert('Please fill title and content');
      saveContentToFirebase(section, title, description);
      closeAddContentModal();
      $('contentTitle').value = '';
      $('contentDescription').value = '';
    });

    // Theme toggle
    $('toggleThemeBtn').addEventListener('click', toggleTheme);

    // Section navigation
    document.querySelectorAll('.section-btn').forEach(btn => {
      btn.addEventListener('click', function() {
        document.querySelectorAll('.section-btn').forEach(b => b.classList.remove('active-section'));
        this.classList.add('active-section');
        const section = this.getAttribute('data-section');
        document.querySelectorAll('.section-content').forEach(s => s.classList.remove('active'));
        const el = document.getElementById(section);
        if (el) el.classList.add('active');
      });
    });

    // Save theme & user if present
    document.addEventListener('DOMContentLoaded', () => {
      initializeTeamList();
      const savedTheme = localStorage.getItem('theme');
      if (savedTheme === 'dark') document.documentElement.classList.add('dark');
      const savedUser = localStorage.getItem('current-user');
      if (savedUser) {
        currentUser = JSON.parse(savedUser);
        updateUserUI();
        $('loginModal').classList.remove('active');
        startRealtimeListeners();
      }
    });

    /******************************************************************
     * Realtime features: chat & content
     ******************************************************************/
    let listenersStarted = false;

    function startRealtimeListeners() {
      if (listenersStarted) return;
      listenersStarted = true;

      // CHAT: real-time listener
      db.ref('chat').on('value', snapshot => {
        const chatBox = $('chatMessages');
        chatBox.innerHTML = '';
        if (!snapshot.exists()) {
          chatBox.innerHTML = `<div class="text-center text-gray-500">No messages yet.</div>`;
          return;
        }
        snapshot.forEach(child => {
          const msg = child.val();
          const isMy = currentUser && msg.user === currentUser.name;
          const wrapper = document.createElement('div');
          wrapper.className = `p-3 mb-2 rounded-lg max-w-[80%] ${isMy ? 'bg-blue-600 text-white ml-auto' : 'bg-gray-200 dark:bg-gray-700 dark:text-white'}`;
          wrapper.innerHTML = `
            <div class="font-semibold text-sm">${escapeHtml(msg.user)}</div>
            <div class="break-words">${escapeHtml(msg.text)}</div>
            <div class="text-xs opacity-70 mt-1">${new Date(msg.timestamp).toLocaleString()}</div>
          `;
          chatBox.appendChild(wrapper);
        });
        chatBox.scrollTop = chatBox.scrollHeight;
      });

      // CONTENT: listen for all sections under /content
      const sections = [
        'overview', 'project-documentation', 'team-collaboration', 'team-updates',
        'ai-assistant', 'practical-tasks', 'implementation', 'research',
        'resources', 'security-testing', 'monitoring-analytics'
      ];

      sections.forEach(section => {
        db.ref('content/' + section).on('value', snapshot => {
          const container = $(section + '-content');
          if (!container) return;
          container.innerHTML = '';
          if (!snapshot.exists()) {
            container.innerHTML = `
              <div class="text-center p-8 text-gray-500">
                <i class="fas fa-inbox text-4xl mb-4"></i>
                <p>No content yet. Add something to share with the team.</p>
              </div>`;
            return;
          }
          snapshot.forEach(child => {
            const item = child.val();
            const card = document.createElement('div');
            card.className = 'p-6 border border-gray-200 dark:border-gray-700 rounded-2xl bg-white dark:bg-gray-800 mb-4';
            card.innerHTML = `
              <h3 class="text-xl font-semibold mb-2">${escapeHtml(item.title)}</h3>
              <p class="text-gray-700 dark:text-gray-300 mb-4 whitespace-pre-line">${escapeHtml(item.description)}</p>
              <div class="flex justify-between items-center text-sm text-gray-600 dark:text-gray-400">
                <span><i class="fas fa-user mr-1"></i> ${escapeHtml(item.author)}</span>
                <span><i class="fas fa-clock mr-1"></i> ${new Date(item.date).toLocaleString()}</span>
              </div>
            `;
            container.appendChild(card);
          });
        });
      });
    }

    /******************************************************************
     * Save content to Firebase
     ******************************************************************/
    function saveContentToFirebase(section, title, description) {
      if (!currentUser) { alert('Please login first'); return; }
      const item = {
        section,
        title,
        description,
        author: currentUser.name,
        date: Date.now()
      };
      db.ref('content/' + section).push(item)
      .then(() => {
        // success
      })
      .catch(err => {
        console.error('Failed to save content:', err);
        alert('Failed to save content');
      });
    }

    /******************************************************************
     * Chat: send message to Firebase
     ******************************************************************/
    function sendChatMessage() {
      if (!currentUser) { alert('Please login first'); return; }
      const input = $('chatInput');
      const text = input.value.trim();
      if (!text) return;
      const message = {
        user: currentUser.name,
        text,
        timestamp: Date.now()
      };
      db.ref('chat').push(message)
      .then(() => {
        input.value = '';
      })
      .catch(err => {
        console.error('Chat send error:', err);
        alert('Message send failed');
      });
    }

    /******************************************************************
     * Add content modal helpers
     ******************************************************************/
    function openAddContentModal(section) {
      if (!currentUser) { alert('Please login first'); return; }
      $('contentSection').value = section;
      $('addContentModal').classList.add('active');
      $('contentTitle').focus();
    }
    function closeAddContentModal() { $('addContentModal').classList.remove('active'); }

    /******************************************************************
     * Utilities
     ******************************************************************/
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

    function focusChatInput() { setTimeout(()=> $('chatInput').focus(), 100); }

    // Basic HTML escaping to avoid accidental injection (we trust team but keep safe)
    function escapeHtml(unsafe) {
      return String(unsafe)
        .replaceAll('&','&amp;')
        .replaceAll('<','&lt;')
        .replaceAll('>','&gt;')
        .replaceAll('"','&quot;')
        .replaceAll("'",'&#039;');
    }
  </script>
</body>
</html>
