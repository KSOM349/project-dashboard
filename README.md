<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="utf-8" />
    <meta name="viewport" content="width=device-width,initial-scale=1" />
    <title>Security Chaos Engineering Dashboard</title>

    <!-- Icons & Tailwind -->
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css" integrity="sha512-pbVfX1XQ6Ylq6Yd8m3QmFz3QwqgD56b3Qq6F1+0p1mR9e6k1t8QeKf2w==" crossorigin="anonymous" referrerpolicy="no-referrer" />
    <script src="https://cdn.tailwindcss.com"></script>

    <!-- Firebase (core libs only in head; storage/db scripts can be loaded later if you prefer) -->
    <script src="https://www.gstatic.com/firebasejs/9.22.1/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.1/firebase-database-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.1/firebase-storage-compat.js"></script>

    <style>
        /* =========================
           THEME + BACKGROUND STYLES
           Part 1: head + CSS + background
           ========================= */

        :root{
            --overlay-gradient: linear-gradient(180deg, rgba(8,11,26,0.45), rgba(8,11,26,0.6));
            --muted-text: rgba(248,250,252,0.9);
            --card-bg: rgba(255,255,255,0.95);
        }

        html, body {
            height: 100%;
            margin: 0;
            padding: 0;
            font-family: Inter, system-ui, -apple-system, "Segoe UI", Roboto, "Helvetica Neue", Arial, "Noto Naskh Arabic";
            -webkit-font-smoothing: antialiased;
            -moz-osx-font-smoothing: grayscale;
            background: #0b1220;
        }

        /* ===== FULLSCREEN BACKGROUND HERO =====
           Put your chosen image at assets/bg.jpg (relative path),
           or change the URL below to an absolute link.
        */
        .bg-hero {
            position: fixed;
            inset: 0;
            z-index: -50;
            background-image: url('assets/bg.jpg');
            background-size: cover;
            background-position: center center;
            background-repeat: no-repeat;
            transform: translateZ(0);
            will-change: transform;
            filter: saturate(1.02) contrast(1.02);
            transition: transform 0.25s ease-out;
        }

        /* dark translucent overlay + soft blur for readability */
        .bg-hero::after {
            content: "";
            position: absolute;
            inset: 0;
            background: var(--overlay-gradient);
            backdrop-filter: blur(6px) brightness(.55);
            -webkit-backdrop-filter: blur(6px) brightness(.55);
        }

        /* small parallax container to be moved by JS */
        .parallax {
            will-change: transform;
        }

        /* ===== GLOBAL UI HELPERS ===== */
        .theme-transition { transition: all 0.28s cubic-bezier(.2,.9,.2,1); }

        .active-section {
            background: linear-gradient(135deg, #3b82f6, #1d4ed8);
            color: white;
        }

        .online-indicator {
            width: 8px; height: 8px; background: #10B981; border-radius: 50%;
            animation: pulse 2s infinite;
            display: inline-block;
        }
        @keyframes pulse {
            0% { transform: scale(1); opacity: 1; }
            50% { transform: scale(1.2); opacity: .7; }
            100% { transform: scale(1); opacity: 1; }
        }

        .modal { display: none; position: fixed; inset: 0; background: rgba(0,0,0,.45); z-index: 60; align-items: center; justify-content: center; padding: 1rem; }
        .modal.active { display:flex; }

        .section-content { display: none; }
        .section-content.active { display: block; }

        /* cards / panels to be readable over the hero */
        .card {
            background: var(--card-bg);
            border-radius: 14px;
            box-shadow: 0 10px 30px rgba(2,6,23,0.45);
            color: #0f172a;
        }
        .card-compact {
            background: rgba(255,255,255,0.06);
            border-radius: 12px;
            color: var(--muted-text);
            border: 1px solid rgba(255,255,255,0.04);
        }

        /* chat list */
        .chat-container { max-height: 300px; overflow-y: auto; }

        /* scrollbar small tweak for modern browsers */
        ::-webkit-scrollbar { height: 8px; width: 8px; }
        ::-webkit-scrollbar-thumb { background: rgba(255,255,255,0.08); border-radius: 8px; }

        /* responsive tweaks */
        @media (max-width: 768px) {
            .w-80 { width: 100% !important; border-right: none !important; }
            .p-8 { padding: 1rem !important; }
        }

        /* readable headings over dark background (if you choose colored text) */
        .heading-hero {
            color: #E6EEF8; /* soft light-blue, visible on dark */
            text-shadow: 0 2px 12px rgba(2,6,23,0.6);
        }
        .muted {
            color: rgba(230,238,248,0.85);
        }

        /* small utility for "two-star" block the user wanted */
        .two-stars::before {
            content: "**";
            display:block;
            font-weight:700;
            margin-bottom: 6px;
            color: #f8fafc;
        }
    </style>
</head>
<body class="theme-transition">

    <!-- BACKGROUND HERO (parallax) -->
    <div id="bgHero" class="bg-hero parallax" aria-hidden="true"></div>

    <!--
      PART 1 END.
      الجزء الأول: انتهى (HEAD + CSS + الخلفية).
      الجزء 2 سيبدأ من هنا ويحتوي على الـ layout (sidebar, main content, modals HTML).
      الجزء 3 سيحتوي على كل JavaScript (Firebase init, chat, content system, pdf upload, permissions).
    -->

<!-- BACKGROUND HERO (parallax) -->
<div id="bgHero" class="bg-hero parallax" aria-hidden="true"></div>
<!-- ======================================= -->
<!-- LOGIN MODAL (appears before dashboard)   -->
<!-- ======================================= -->
<div id="loginModal" class="modal active">
    <div class="bg-white dark:bg-gray-800 p-8 rounded-2xl max-w-md w-full shadow-2xl">
        <h2 class="text-2xl font-bold text-center mb-6 text-gray-700 dark:text-gray-200">
            <i class="fas fa-user-lock mr-2"></i> Login to Dashboard
        </h2>

        <form id="loginForm" class="space-y-4">
            <div>
                <label class="text-sm font-medium text-gray-700 dark:text-gray-300">Email</label>
                <input id="loginEmail" type="email" class="w-full p-3 rounded-xl bg-gray-100 dark:bg-gray-700 text-gray-800 dark:text-gray-100" required>
            </div>

            <div>
                <label class="text-sm font-medium text-gray-700 dark:text-gray-300">Password</label>
                <input id="loginPassword" type="password" class="w-full p-3 rounded-xl bg-gray-100 dark:bg-gray-700 text-gray-800 dark:text-gray-100" required>
            </div>

            <button type="submit" class="w-full bg-blue-600 hover:bg-blue-700 text-white p-3 rounded-xl">
                Login
            </button>
        </form>

        <div class="mt-5 text-sm bg-gray-50 dark:bg-gray-700 p-3 rounded-xl">
            <div class="font-bold mb-1">Demo Accounts</div>
            <p>kaled@team.com / kaled123</p>
            <p>marcus@team.com / marcus123</p>
            <p>jens@team.com / jens123</p>
            <p>fahad@team.com / fahad123</p>
            <p>luwam@team.com / luwam123</p>
            <p>stefan@team.com / stefan123</p>
            <p>najmaddin@team.com / najmaddin123</p>
        </div>
    </div>
</div>

<!-- ======================================= -->
<!-- MAIN DASHBOARD LAYOUT                   -->
<!-- ======================================= -->
<div class="flex min-h-screen">

    <!-- ====================== SIDEBAR ====================== -->
    <aside class="w-80 bg-gray-900/60 text-white backdrop-blur-xl p-6 border-r border-white/10">

        <h1 class="text-xl font-bold mb-1">Security Chaos Engineering</h1>
        <p class="muted mb-6">Group 1 Dashboard</p>

        <!-- TEAM BLOCK -->
        <div class="bg-gray-800/60 p-4 rounded-xl shadow-md border border-white/10 mb-6">
            <div class="flex items-center space-x-3">
                <div id="userAvatar" class="w-10 h-10 rounded-full bg-blue-600 flex items-center justify-center font-bold text-white">U</div>
                <div>
                    <div id="userName" class="font-semibold">User</div>
                    <div id="userRole" class="text-xs text-gray-300">Active User</div>
                </div>
            </div>

            <button onclick="openTeamChat()" class="w-full bg-blue-600 hover:bg-blue-700 mt-4 p-2 rounded-lg flex items-center justify-center">
                <i class="fas fa-comments mr-2"></i> Team Chat
                <span id="unreadCount" class="ml-2 text-xs bg-red-500 px-2 py-1 rounded-full hidden">0</span>
            </button>
        </div>

        <!-- TEAM MEMBERS -->
        <div class="bg-gray-800/60 p-4 rounded-xl shadow-md border border-white/10 mb-6">
            <h3 class="font-semibold mb-3 flex items-center gap-2">
                <span class="online-indicator"></span> Team Members
            </h3>
            <div id="liveTeamList" class="space-y-2 max-h-40 overflow-y-auto"></div>
        </div>

        <!-- NAVIGATION BUTTONS -->
        <nav class="space-y-2">
            <button class="section-btn active-section w-full p-3 rounded-lg text-left" data-section="overview">
                <i class="fas fa-home mr-3"></i> Overview
            </button>

            <button class="section-btn w-full p-3 rounded-lg text-left" data-section="practical-tasks">
                <i class="fas fa-tasks mr-3"></i> Practical Tasks
            </button>

            <button class="section-btn w-full p-3 rounded-lg text-left" data-section="project-documentation">
                <i class="fas fa-folder mr-3"></i> Project Documentation
            </button>

            <button class="section-btn w-full p-3 rounded-lg text-left" data-section="team-updates">
                <i class="fas fa-bullhorn mr-3"></i> Team Updates
            </button>

            <button class="section-btn w-full p-3 rounded-lg text-left" data-section="resources">
                <i class="fas fa-book mr-3"></i> Resources
            </button>

            <button class="section-btn w-full p-3 rounded-lg text-left" data-section="files-section">
                <i class="fas fa-file-pdf mr-3"></i> PDF Files
            </button>

            <button onclick="toggleTheme()" class="w-full p-3 rounded-lg text-left bg-gray-800/50 mt-6">
                <i class="fas fa-moon mr-3"></i> Toggle Theme
            </button>
        </nav>

        <!-- ADMIN PANEL AUTO-INJECT HERE -->
        <div id="adminPanel" class="mt-6"></div>

    </aside>

    <!-- ====================== MAIN CONTENT ====================== -->
    <main class="flex-1 p-10 text-white">

        <!-- HEADER -->
        <header class="mb-10">
            <h1 class="text-4xl font-bold heading-hero">Security Chaos Engineering Dashboard</h1>
            <p class="muted">Real-time Collaboration Platform</p>
        </header>

        <!-- ========== SECTIONS ========== -->

        <!-- 1. OVERVIEW -->
        <section id="overview" class="section-content active">
            <div class="card p-8">
                <div class="flex justify-between items-center mb-4">
                    <h2 class="text-2xl font-bold">Overview</h2>
                    <button onclick="openAddContent('overview')" class="bg-green-600 px-4 py-2 rounded-lg">+ Add</button>
                </div>

                <div id="overview-content" class="space-y-4"></div>
            </div>
        </section>

        <!-- 2. PRACTICAL TASKS -->
        <section id="practical-tasks" class="section-content">
            <div class="card p-8">
                <div class="flex justify-between items-center mb-4">
                    <h2 class="text-2xl font-bold">Practical Tasks</h2>
                    <button onclick="openAddContent('practical-tasks')" class="bg-green-600 px-4 py-2 rounded-lg">+ Add</button>
                </div>
                <div id="practical-tasks-content" class="space-y-4"></div>
            </div>
        </section>

        <!-- 3. DOCUMENTATION -->
        <section id="project-documentation" class="section-content">
            <div class="card p-8">
                <div class="flex justify-between items-center mb-4">
                    <h2 class="text-2xl font-bold">Project Documentation</h2>
                    <button onclick="openAddContent('project-documentation')" class="bg-green-600 px-4 py-2 rounded-lg">+ Add</button>
                </div>
                <div id="project-documentation-content" class="space-y-4"></div>
            </div>
        </section>

        <!-- 4. TEAM UPDATES -->
        <section id="team-updates" class="section-content">
            <div class="card p-8">
                <div class="flex justify-between items-center mb-4">
                    <h2 class="text-2xl font-bold">Team Updates</h2>
                    <button onclick="openAddContent('team-updates')" class="bg-green-600 px-4 py-2 rounded-lg">+ Add</button>
                </div>
                <div id="team-updates-content" class="space-y-4"></div>
            </div>
        </section>

        <!-- 5. RESOURCES -->
        <section id="resources" class="section-content">
            <div class="card p-8">
                <div class="flex justify-between items-center mb-4">
                    <h2 class="text-2xl font-bold">Resources</h2>
                    <button onclick="openAddContent('resources')" class="bg-green-600 px-4 py-2 rounded-lg">+ Add</button>
                </div>
                <div id="resources-content" class="space-y-4"></div>
            </div>
        </section>

        <!-- 6. PDF FILES SECTION -->
        <section id="files-section" class="section-content">
            <div class="card p-8">
                <h2 class="text-2xl font-bold mb-4">Uploaded PDF Files</h2>

                <input id="pdfUpload" type="file" accept="application/pdf" class="mb-4 text-black" />

                <button onclick="uploadPDF()" class="bg-blue-600 px-4 py-2 rounded-lg mb-4">
                    Upload PDF
                </button>

                <div id="pdfList" class="space-y-2"></div>
            </div>
        </section>

    </main>
</div>
<!-- ================== PART 3: Full JS (Firebase, Chat, Content, PDFs, Permissions) ================== -->
<script>
(function(){
    /************* Firebase config *************/
    const DATABASE_URL = 'https://fir-console-df3e9-default-rtdb.europe-west1.firebasedatabase.app';
    const firebaseConfig = {
        apiKey: "AIzaSyDJsZ4LZVrBucavpTdhXbKxyE_BFeZFFKs",
        authDomain: "fir-console-df3e9.firebaseapp.com",
        databaseURL: DATABASE_URL,
        projectId: "fir-console-df3e9",
        storageBucket: "fir-console-df3e9.firebasestorage.app",
        messagingSenderId: "750795336412",
        appId: "1:750795336412:web:abfd0c06941a9418abe219"
    };
    try { if (!firebase.apps.length) firebase.initializeApp(firebaseConfig); } catch(e) { console.warn('Firebase init:', e); }
    const db = firebase.database();
    const storage = firebase.storage();

    /************* Helpers *************/
    const $ = id => document.getElementById(id);
    function escapeHtml(s=''){ return String(s).replaceAll('&','&amp;').replaceAll('<','&lt;').replaceAll('>','&gt;').replaceAll('"','&quot;').replaceAll("'",'&#039;'); }
    const sections = ['overview','practical-tasks','project-documentation','team-updates','resources','files-section'];

    /************* Login system (local demo accounts) *************/
    const loginSystem = {
        users: JSON.parse(localStorage.getItem('teamUsers')) || null,
        currentUser: JSON.parse(localStorage.getItem('currentUser')) || null,
        init() {
            if (!this.users) {
                const defaultUsers = [
                    { id:1, name:"Kaled Osman", email:"kaled@team.com", password:"kaled123", role:"Project Lead", avatar:"K" },
                    { id:2, name:"Marcus Tibell", email:"marcus@team.com", password:"marcus123", role:"Engineer", avatar:"M" },
                    { id:3, name:"Jens Annell", email:"jens@team.com", password:"jens123", role:"Analyst", avatar:"J" },
                    { id:4, name:"Fahad Hussain", email:"fahad@team.com", password:"fahad123", role:"Researcher", avatar:"F" },
                    { id:5, name:"Luwam", email:"luwam@team.com", password:"luwam123", role:"Designer", avatar:"L" },
                    { id:6, name:"Stefan Österberg", email:"stefan@team.com", password:"stefan123", role:"Architect", avatar:"S" },
                    { id:7, name:"Najmaddin", email:"najmaddin@team.com", password:"najmaddin123", role:"Security Expert", avatar:"N" }
                ];
                this.users = defaultUsers;
                localStorage.setItem('teamUsers', JSON.stringify(defaultUsers));
            }
            // wire form
            const loginForm = $('loginForm');
            if (loginForm) loginForm.addEventListener('submit', (e) => { e.preventDefault(); this.attemptLogin(); });
            // if someone already logged -> update UI
            if (this.currentUser) this.updateUIForLoggedIn();
        },
        attemptLogin() {
            const email = ($('loginEmail') || {}).value || '';
            const password = ($('loginPassword') || {}).value || '';
            const found = this.users.find(u => u.email === email && u.password === password);
            if (!found) { alert('Invalid credentials'); return; }
            this.currentUser = found;
            localStorage.setItem('currentUser', JSON.stringify(found));
            this.updateUIForLoggedIn();
            chat.currentUser = found.name;
            // close login modal
            const lm = $('loginModal');
            if (lm) lm.classList.remove('active');
            adminSystem.tryInit();
        },
        logout() {
            this.currentUser = null;
            localStorage.removeItem('currentUser');
            // show login again
            const lm = $('loginModal');
            if (lm) lm.classList.add('active');
            // reset UI placeholders
            if ($('userName')) $('userName').textContent = 'You';
            if ($('userRole')) $('userRole').textContent = 'Active User';
            if ($('userAvatar')) $('userAvatar').textContent = 'U';
            chat.currentUser = 'You';
            adminSystem.tryInit();
        },
        updateUIForLoggedIn() {
            const u = this.currentUser;
            if (!u) return;
            if ($('userName')) $('userName').textContent = u.name;
            if ($('userRole')) $('userRole').textContent = u.role;
            if ($('userAvatar')) $('userAvatar').textContent = u.avatar || u.name.charAt(0);
        }
    };

    /************* Admin panel (only Kaled) *************/
    const adminSystem = {
        tryInit() {
            const cur = JSON.parse(localStorage.getItem('currentUser')) || null;
            const panel = $('adminPanel');
            if (!panel) return;
            if (cur && cur.email === 'kaled@team.com') {
                panel.innerHTML = `
                    <div class="p-3 bg-white/6 rounded-lg text-sm">
                        <div class="font-semibold mb-2">Admin</div>
                        <button id="adminClearContent" class="w-full mb-2 p-2 bg-red-600 rounded">Delete all content</button>
                        <button id="adminClearChat" class="w-full p-2 bg-purple-600 rounded">Clear chat</button>
                    </div>`;
                setTimeout(()=> {
                    const a1 = $('adminClearContent'), a2 = $('adminClearChat');
                    if (a1) a1.addEventListener('click', clearAllContentAdmin);
                    if (a2) a2.addEventListener('click', clearChatAdmin);
                },50);
            } else {
                panel.innerHTML = '';
            }
        }
    };

    /************* Chat system *************/
    const chat = {
        currentUser: 'You',
        messageCount: 0,
        unread: 0,
        lastRead: Date.now(),
        init() {
            const saved = JSON.parse(localStorage.getItem('currentUser'));
            if (saved && saved.name) this.currentUser = saved.name;
            // color indicator
            const status = $('chatStatus'); if (status) status.style.background = '#10B981';
            // listen messages
            this.listenMessages();
            // wire send
            const sendBtn = $('sendChatButton');
            if (sendBtn) sendBtn.addEventListener('click', () => this.send());
            const input = $('chatInput');
            if (input) input.addEventListener('keypress', (e) => { if (e.key === 'Enter') this.send(); });
        },
        listenMessages() {
            const ref = db.ref('team-chat').limitToLast(500);
            ref.off();
            ref.on('child_added', snap => {
                const msg = snap.val();
                this.display(msg);
                this.messageCount++;
                if (msg.timestamp > this.lastRead && !this.isOpen()) { this.unread++; this.updateUnread(); }
            });
        },
        async send() {
            const input = $('chatInput');
            if (!input) return;
            const text = input.value.trim();
            if (!text) return;
            const payload = { user: this.currentUser || 'You', text, timestamp: Date.now() };
            try {
                await db.ref('team-chat').push(payload);
                input.value = '';
                this.lastRead = Date.now();
                this.unread = 0; this.updateUnread();
            } catch (err) { console.error('chat send', err); alert('Failed to send'); }
        },
        display(msg) {
            const list = $('chatMessages');
            if (!list) return;
            const placeholder = list.querySelector('.text-center');
            if (placeholder) list.innerHTML = '';
            const el = document.createElement('div');
            const mine = msg.user === this.currentUser;
            el.className = `p-3 mb-2 rounded-lg max-w-[85%] ${mine ? 'bg-blue-500 text-white ml-auto' : 'bg-white/8 text-white'}`;
            el.innerHTML = `<div class="text-sm font-semibold">${escapeHtml(msg.user)}</div>
                            <div class="break-words">${escapeHtml(msg.text)}</div>
                            <div class="text-xs opacity-70 mt-1">${new Date(msg.timestamp).toLocaleTimeString()}</div>`;
            list.appendChild(el);
            list.scrollTop = list.scrollHeight;
            const cnt = $('messageCount'); if (cnt) cnt.textContent = (parseInt(cnt.textContent||'0') + 1).toString();
        },
        open() { const modal = $('teamChatModal'); if (modal) modal.classList.add('active'); this.lastRead = Date.now(); this.unread = 0; this.updateUnread(); },
        close() { const modal = $('teamChatModal'); if (modal) modal.classList.remove('active'); },
        isOpen() { const modal = $('teamChatModal'); return modal && modal.classList.contains('active'); },
        updateUnread() { const u = $('unreadCount'); if (!u) return; if (this.unread>0) { u.textContent = this.unread; u.classList.remove('hidden'); } else u.classList.add('hidden'); }
    };

    /************* Content system (with per-author permissions) *************/
    const contentSystem = {
        listenAll() {
            sections.forEach(sec => {
                // skip files-section for content (handled separately)
                if (sec === 'files-section') return;
                const ref = db.ref(`content/${sec}`);
                ref.off();
                ref.on('value', snap => this.renderSection(sec, snap));
            });
        },
        renderSection(section, snapshot) {
            const container = $(`${section}-content`);
            if (!container) return;
            container.innerHTML = '';
            if (!snapshot || !snapshot.exists()) {
                container.innerHTML = `<div class="text-sm text-white/70 p-6"><i class="fas fa-inbox mr-2"></i>No content yet.</div>`;
                return;
            }
            snapshot.forEach(child => {
                const item = child.val(); const id = child.key;
                const card = document.createElement('div');
                card.className = 'mb-4 p-4 rounded-lg bg-white/6';
                // action buttons: only show edit/delete if current user equals author or admin
                const cur = JSON.parse(localStorage.getItem('currentUser')) || null;
                const currentName = (cur && cur.name) || loginSystem.currentUser?.name || loginSystem.currentUser?.name || 'You';
                const isAuthor = item.author === currentName;
                const isAdmin = cur && cur.email === 'kaled@team.com';
                const controls = [];
                if (isAuthor || isAdmin) controls.push(`<button data-id="${id}" data-sec="${section}" class="edit-btn p-2 rounded bg-green-600 text-sm">Edit</button>`);
                if (isAuthor || isAdmin) controls.push(`<button data-id="${id}" data-sec="${section}" class="del-btn p-2 rounded bg-red-600 text-sm">Delete</button>`);
                card.innerHTML = `
                    <div class="flex justify-between items-start">
                        <div>
                            <div class="font-semibold text-lg">${escapeHtml(item.title)}</div>
                            <div class="text-xs mt-1 text-white/70">${escapeHtml(item.author || 'Group 1')} • ${new Date(item.date).toLocaleDateString()}</div>
                        </div>
                        <div class="flex gap-2">${controls.join('')}</div>
                    </div>
                    <div class="mt-3 text-sm whitespace-pre-line">${escapeHtml(item.description)}</div>
                `;
                container.appendChild(card);
            });
            // attach handlers
            container.querySelectorAll('.edit-btn').forEach(btn => btn.addEventListener('click', (e) => {
                const id = btn.getAttribute('data-id'), sec = btn.getAttribute('data-sec');
                openEditModalFor(sec, id);
            }));
            container.querySelectorAll('.del-btn').forEach(btn => btn.addEventListener('click', (e) => {
                const id = btn.getAttribute('data-id'), sec = btn.getAttribute('data-sec');
                deleteContentWithPermission(sec, id);
            }));
        },
        pushContent(section, payload) { return db.ref(`content/${section}`).push(payload); },
        updateContent(section, id, payload) { return db.ref(`content/${section}/${id}`).update(payload); },
        removeContent(section, id) { return db.ref(`content/${section}/${id}`).remove(); }
    };

    /************* Add/Edit UI helpers *************/
    function openAddContent(section) {
        $('contentSection').value = section;
        $('editContentId').value = '';
        $('contentTitle').value = '';
        $('contentDescription').value = '';
        // default author from current user
        const cur = JSON.parse(localStorage.getItem('currentUser')) || null;
        if (cur && cur.name) $('contentAuthor').value = cur.name;
        $('addContentModal').classList.add('active');
    }
    function closeAddContent() { $('addContentModal').classList.remove('active'); }

    function openEditModalFor(section, id) {
        db.ref(`content/${section}/${id}`).once('value').then(snap => {
            const item = snap.val();
            if (!item) return alert('Item not found');
            // permission check
            const cur = JSON.parse(localStorage.getItem('currentUser')) || null;
            const currentName = (cur && cur.name) || 'You';
            if (item.author !== currentName && (!cur || cur.email !== 'kaled@team.com')) {
                return alert('You can only edit your own items (or be admin).');
            }
            $('contentSection').value = section;
            $('editContentId').value = id;
            $('contentTitle').value = item.title || '';
            $('contentDescription').value = item.description || '';
            $('contentAuthor').value = item.author || currentName;
            $('addContentModal').classList.add('active');
        }).catch(err => { console.error(err); alert('Failed to load'); });
    }

    // submit add/edit
    const addForm = $('addContentForm');
    if (addForm) addForm.addEventListener('submit', function(e){
        e.preventDefault();
        const section = $('contentSection').value;
        const editId = $('editContentId').value;
        const title = ($('contentTitle').value || '').trim();
        const description = ($('contentDescription').value || '').trim();
        const author = ($('contentAuthor').value || '').trim() || ((JSON.parse(localStorage.getItem('currentUser'))||{}).name || 'Group 1');
        if (!title || !description) return alert('Fill title and content');
        const payload = { title, description, author, date: new Date().toISOString() };
        if (editId) {
            // permission re-check before update
            db.ref(`content/${section}/${editId}`).once('value').then(snap => {
                const item = snap.val(); const cur = JSON.parse(localStorage.getItem('currentUser')) || null;
                const currentName = (cur && cur.name) || 'You';
                if (item.author !== currentName && (!cur || cur.email !== 'kaled@team.com')) {
                    alert('You cannot edit this item');
                    return;
                }
                contentSystem.updateContent(section, editId, payload).then(()=> closeAddContent()).catch(err => { console.error(err); alert('Update failed'); });
            });
        } else {
            contentSystem.pushContent(section, payload).then(()=> closeAddContent()).catch(err => { console.error(err); alert('Save failed'); });
        }
    });

    // delete with permission
    function deleteContentWithPermission(section, id) {
        db.ref(`content/${section}/${id}`).once('value').then(snap => {
            const item = snap.val();
            const cur = JSON.parse(localStorage.getItem('currentUser')) || null;
            const currentName = (cur && cur.name) || 'You';
            const isAdmin = cur && cur.email === 'kaled@team.com';
            if (!item) return alert('Not found');
            if (item.author !== currentName && !isAdmin) return alert('You can only delete your own items');
            if (!confirm('Delete this item?')) return;
            contentSystem.removeContent(section, id).catch(err => { console.error(err); alert('Delete failed'); });
        });
    }

    /************* Admin actions *************/
    function clearAllContentAdmin() {
        if (!confirm('Delete all content for all sections?')) return;
        const cur = JSON.parse(localStorage.getItem('currentUser')) || {};
        if (cur.email !== 'kaled@team.com') { alert('Only admin can do this'); return; }
        const updates = {};
        sections.forEach(s => { if (s !== 'files-section') updates[`content/${s}`] = null; });
        db.ref().update(updates).then(()=> alert('All content cleared')).catch(err => { console.error(err); alert('Failed'); });
    }
    function clearChatAdmin() {
        if (!confirm('Clear chat messages?')) return;
        const cur = JSON.parse(localStorage.getItem('currentUser')) || {};
        if (cur.email !== 'kaled@team.com') { alert('Only admin can do this'); return; }
        db.ref('team-chat').remove().then(()=> {
            const list = $('chatMessages'); if (list) list.innerHTML = '<div class="text-sm text-white/70 p-4">Chat cleared</div>';
        }).catch(err => { console.error(err); alert('Failed'); });
    }

    /************* PDF upload & list (Storage + DB) *************/
    function uploadPDF() {
        const input = $('pdfUpload');
        if (!input || !input.files || !input.files[0]) return alert('Choose a PDF first');
        const file = input.files[0];
        if (file.type !== 'application/pdf') return alert('Please select a PDF file');
        const ts = Date.now();
        const path = `pdfs/${ts}_${file.name}`;
        const storageRef = storage.ref().child(path);
        const uploadTask = storageRef.put(file);
        // progress feedback basic
        uploadTask.on('state_changed',
            (snap) => {
                const pct = Math.floor((snap.bytesTransferred / snap.totalBytes) * 100);
                console.log('Upload', pct + '%');
            },
            (err) => { console.error('upload',err); alert('Upload failed'); },
            async () => {
                const url = await uploadTask.snapshot.ref.getDownloadURL();
                const meta = { title: file.name, description: '', url, storagePath: path, date: new Date().toISOString(), author: (JSON.parse(localStorage.getItem('currentUser'))||{}).name || 'Group 1' };
                await db.ref('pdfs').push(meta);
                input.value = '';
                alert('Upload complete');
            }
        );
    }

    function renderPdfList(snapshot) {
        const list = $('pdfList'); if (!list) return;
        list.innerHTML = '';
        if (!snapshot || !snapshot.exists()) {
            list.innerHTML = '<div class="text-sm text-white/70 p-4">No PDFs yet</div>'; return;
        }
        snapshot.forEach(child => {
            const item = child.val(); const id = child.key;
            const el = document.createElement('div');
            el.className = 'p-3 rounded bg-white/6 flex justify-between items-center';
            el.innerHTML = `<div>
                                <div class="font-semibold">${escapeHtml(item.title)}</div>
                                <div class="text-xs text-white/60">${escapeHtml(item.author)} • ${new Date(item.date).toLocaleDateString()}</div>
                            </div>
                            <div class="flex gap-2">
                                <a href="${item.url}" target="_blank" class="p-2 bg-blue-600 rounded text-sm">Open</a>
                                <button data-id="${id}" class="del-pdf p-2 bg-red-600 rounded text-sm">Delete</button>
                            </div>`;
            list.appendChild(el);
        });
        // attach delete handlers (only admin)
        list.querySelectorAll('.del-pdf').forEach(b => b.addEventListener('click', async (e) => {
            const id = b.getAttribute('data-id');
            const cur = JSON.parse(localStorage.getItem('currentUser')) || {};
            if (cur.email !== 'kaled@team.com') { alert('Only admin can delete PDFs'); return; }
            if (!confirm('Delete this PDF?')) return;
            const snap = await db.ref(`pdfs/${id}`).once('value');
            const meta = snap.val();
            if (meta && meta.storagePath) {
                try { await storage.ref(meta.storagePath).delete(); } catch(err){ console.warn('Storage delete',err); }
            }
            await db.ref(`pdfs/${id}`).remove();
        }));
    }

    /************* Team members list *************/
    const teamMembers = [
        { name: 'Kaled Osman', role: 'Project Lead', avatar: 'K' },
        { name: 'Marcus Tibell', role: 'Engineer', avatar: 'M' },
        { name: 'Jens Annell', role: 'Analyst', avatar: 'J' },
        { name: 'Fahad Hussain', role: 'Researcher', avatar: 'F' },
        { name: 'Luwam', role: 'Designer', avatar: 'L' },
        { name: 'Stefan Österberg', role: 'Architect', avatar: 'S' },
        { name: 'Najmaddin', role: 'Security Expert', avatar: 'N' }
    ];
    function showTeamMembers() {
        const el = $('liveTeamList'); if (!el) return; el.innerHTML = '';
        teamMembers.forEach(m => {
            const row = document.createElement('div');
            row.className = 'flex items-center gap-3 p-2 rounded';
            row.innerHTML = `<div class="w-8 h-8 rounded-full bg-blue-600 flex items-center justify-center font-semibold">${m.avatar}</div>
                             <div class="text-sm">
                                <div class="font-medium">${escapeHtml(m.name)}</div>
                                <div class="text-xs text-white/60">${escapeHtml(m.role)}</div>
                             </div>`;
            el.appendChild(row);
        });
    }

    /************* Seed example content for practical tasks (only if empty) *************/
    async function seedPracticalTasksIfEmpty(){
        const ref = db.ref('content/practical-tasks');
        const snap = await ref.once('value');
        if (snap.exists()) return;
        const teamNames = 'Marcus, Jens, Fahad, Stefan, Kaled';
        const tasks = [
            {
                title: 'TASK 01 – Debug a Broken Feature',
                description: `Group 1 – Report\n\n1. What Was the Task?\nThe goal was to simulate a real debugging situation... (see task report in dashboard)`,
                author: teamNames,
                date: new Date().toISOString()
            },
            {
                title: 'TASK 02 – Build a Mini Tool',
                description: `Group 1 – Report\n\n1. What Was the Task?\nBuild a small functional tool...`,
                author: teamNames,
                date: new Date().toISOString()
            },
            {
                title: 'TASK 03 – Responsive Two-Column Web Component',
                description: `Group 1 – Report\n\nPurpose: build a responsive two-column component...`,
                author: teamNames,
                date: new Date().toISOString()
            }
        ];
        for (const t of tasks) await db.ref('content/practical-tasks').push(t);
    }

    /************* Listen (pdfs + content) *************/
    function listenAll() {
        // content listener
        contentSystem.listenAll();
        // pdfs
        const pdfRef = db.ref('pdfs');
        pdfRef.off();
        pdfRef.on('value', snap => renderPdfList(snap));
    }

    /************* UI: section switching *************/
    function wireSectionButtons() {
        document.querySelectorAll('.section-btn').forEach(btn => {
            btn.addEventListener('click', function(){
                document.querySelectorAll('.section-btn').forEach(b => b.classList.remove('active-section'));
                this.classList.add('active-section');
                document.querySelectorAll('.section-content').forEach(s => s.classList.remove('active'));
                const sec = this.getAttribute('data-section');
                const el = document.getElementById(sec);
                if (el) el.classList.add('active');
            });
        });
    }

    /************* Parallax background *************/
    function initParallax() {
        const bg = document.getElementById('bgHero');
        if (!bg) return;
        window.addEventListener('scroll', () => {
            const y = window.scrollY || 0;
            const translate = Math.min(60, Math.round(y * 0.12));
            bg.style.transform = `translateY(${translate}px) scale(1.03)`;
        });
    }

    /************* Restore default demo accounts if missing (safe helper) *************/
    window.addEventListener('load', () => {
        let accounts = localStorage.getItem('teamUsers');
        if (!accounts) {
            const defaultAccounts = [
                { name: "Kaled Osman", email: "kaled@team.com", password: "kaled123", role: "Project Lead" },
                { name: "Marcus Tibell", email: "marcus@team.com", password: "marcus123", role: "Engineer" },
                { name: "Jens Annell", email: "jens@team.com", password: "jens123", role: "Analyst" },
                { name: "Fahad Hussain", email: "fahad@team.com", password: "fahad123", role: "Researcher" },
                { name: "Luwam", email: "luwam@team.com", password: "luwam123", role: "Designer" },
                { name: "Stefan Österberg", email: "stefan@team.com", password: "stefan123", role: "Architect" },
                { name: "Najmaddin", email: "najmaddin@team.com", password: "najmaddin123", role: "Security Expert" }
            ];
            localStorage.setItem("teamUsers", JSON.stringify(defaultAccounts));
            console.log("Team accounts restored.");
        }
    });

    /************* Init everything *************/
    function initAll(){
        loginSystem.init();
        chat.init();
        showTeamMembers();
        wireSectionButtons();
        listenAll();
        seedPracticalTasksIfEmpty().catch(console.warn);
        initParallax();
        adminSystem.tryInit();
        // wire global modal close on outside click
        document.querySelectorAll('.modal').forEach(m => {
            m.addEventListener('click', (e) => { if (e.target === m) m.classList.remove('active'); });
        });
        // wire global close for team chat close button if exists
        // open team chat triggers are inline in HTML via onclick attributes -> they call window.openTeamChat below
    }

    document.addEventListener('DOMContentLoaded', initAll);

    /************* Expose some functions globally for inline onclicks *************/
    window.openAddContent = openAddContent;
    window.closeAddContent = closeAddContent;
    window.openTeamChat = () => chat.open();
    window.closeTeamChat = () => chat.close();
    window.openEditModalFor = openEditModalFor;
    window.deleteContent = deleteContentWithPermission;
    window.toggleTheme = function(){
        const html = document.documentElement;
        if (html.classList.contains('dark')) { html.classList.remove('dark'); localStorage.setItem('theme','light'); }
        else { html.classList.add('dark'); localStorage.setItem('theme','dark'); }
    };
    window.uploadPDF = uploadPDF;

})();
</script>
<!-- =============== END PART 3 =============== -->
