<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0"/>
    <title>Security Chaos Engineering Dashboard</title>

    <!-- icons & tailwind -->
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <script src="https://cdn.tailwindcss.com"></script>

    <!-- Firebase SDK (compat) -->
    <script src="https://www.gstatic.com/firebasejs/9.22.1/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.1/firebase-database-compat.js"></script>

    <style>
        /* -------------------------
           Background hero image (full-screen)
           Put file bg.jpg in the same repo/folder
           ------------------------- */
        :root{
            --overlay-color: rgba(8,11,26,0.55);
            --card-bg: rgba(255,255,255,0.88);
            --card-bg-dark: rgba(17,24,39,0.7);
        }
        html, body { height: 100%; margin: 0; font-family: Inter, system-ui, -apple-system, "Segoe UI", Roboto, "Helvetica Neue", Arial; }
        body {
            /* background image file name must be bg.jpg (in repo root or same folder) */
            background: url("bg.jpg") no-repeat center center fixed;
            background-size: cover;
            color: #0f172a;
            -webkit-font-smoothing: antialiased;
            -moz-osx-font-smoothing: grayscale;
        }

        /* dark overlay so text is readable */
        .page-overlay {
            position: fixed;
            inset: 0;
            background: linear-gradient(180deg, rgba(2,6,23,0.55), rgba(2,6,23,0.6));
            pointer-events: none;
            z-index: 0;
        }

        /* main container sits above the overlay */
        .app {
            position: relative;
            z-index: 10;
            min-height: 100vh;
            display: flex;
        }

        /* translucent cards so bg is visible */
        .panel {
            background: rgba(255,255,255,0.9);
            border-radius: 12px;
            box-shadow: 0 6px 20px rgba(2,6,23,0.25);
            backdrop-filter: blur(6px);
        }
        .panel-dark {
            background: rgba(15,23,42,0.72);
            color: #e6eef8;
            backdrop-filter: blur(6px);
        }

        /* sidebar specific */
        .sidebar {
            width: 320px;
            flex-shrink: 0;
            padding: 28px;
        }

        .section-btn.active-section {
            background: linear-gradient(135deg,#3b82f6,#1d4ed8);
            color: white;
        }

        /* mobile */
        @media (max-width: 768px) {
            .sidebar { width: 100%; border-right: none; border-bottom: 1px solid rgba(255,255,255,0.06); }
            .app { flex-direction: column; }
        }

        /* small aesthetics */
        .online-indicator {
            width: 10px; height: 10px; border-radius: 9999px; background: #10B981; display:inline-block;
            box-shadow: 0 0 8px rgba(16,185,129,0.6);
        }

        .chat-container { max-height: 300px; overflow-y: auto; }
    </style>
</head>
<body>
    <!-- overlay -->
    <div class="page-overlay" aria-hidden="true"></div>

    <div class="app">
        <!-- Sidebar -->
        <aside class="sidebar panel">
            <div class="mb-6">
                <h1 class="text-2xl font-bold text-blue-700">Security Chaos Engineering</h1>
                <p class="text-sm text-slate-600">Group 1 Dashboard</p>
            </div>

            <div class="mb-6 panel p-4">
                <div class="flex items-center gap-3">
                    <div class="w-10 h-10 rounded-full bg-gradient-to-r from-sky-500 to-violet-600 text-white flex items-center justify-center font-bold" id="userAvatar">Y</div>
                    <div>
                        <div id="userName" class="font-semibold">You</div>
                        <div id="userRole" class="text-xs text-slate-500">Active User</div>
                    </div>
                    <div class="ml-auto flex items-center gap-2">
                        <span class="online-indicator" title="online"></span>
                    </div>
                </div>

                <button onclick="openTeamChat()" class="mt-4 w-full bg-blue-600 hover:bg-blue-700 text-white py-2 rounded-lg flex items-center justify-center gap-2">
                    <i class="fas fa-comments"></i> Team Chat
                </button>
            </div>

            <div class="mb-6 panel p-4">
                <h3 class="font-semibold mb-2">Team Members</h3>
                <div id="liveTeamList" class="space-y-2">
                    <!-- JS will populate -->
                </div>
            </div>

            <nav class="space-y-2">
                <button class="section-btn w-full text-left p-3 rounded-xl theme-transition active-section" data-section="overview"> <i class="fas fa-home mr-3"></i> Overview</button>
                <button class="section-btn w-full text-left p-3 rounded-xl theme-transition" data-section="practical-tasks"> <i class="fas fa-tasks mr-3"></i> Practical Tasks</button>
                <button class="section-btn w-full text-left p-3 rounded-xl theme-transition" data-section="resources"> <i class="fas fa-book mr-3"></i> Resources</button>
                <button class="section-btn w-full text-left p-3 rounded-xl theme-transition" data-section="implementation"> <i class="fas fa-code mr-3"></i> Implementation</button>
            </nav>

            <div class="mt-6">
                <button onclick="toggleTheme()" class="w-full p-3 rounded-xl bg-gray-100 hover:bg-gray-200"> <i class="fas fa-moon mr-2"></i> Toggle Theme</button>
            </div>
        </aside>

        <!-- Main content -->
        <main class="flex-1 p-8">
            <header class="mb-6">
                <h2 class="text-3xl font-bold text-white/95">Security Chaos Engineering Dashboard</h2>
                <p class="text-sm text-white/70">Group 1 — Real-time Team Collaboration</p>
            </header>

            <!-- Overview -->
            <section id="overview" class="panel p-6 mb-6">
                <div class="flex justify-between items-center mb-4">
                    <h3 class="text-xl font-semibold">Overview</h3>
                    <div>
                        <button onclick="openAddContent('overview')" class="bg-green-600 hover:bg-green-700 text-white px-4 py-2 rounded-lg"><i class="fas fa-plus mr-2"></i>Add Content</button>
                        <button onclick="openUploadPdf()" class="bg-red-600 hover:bg-red-700 text-white px-4 py-2 rounded-lg ml-2"><i class="fas fa-file-pdf mr-2"></i>Upload PDF</button>
                    </div>
                </div>
                <div id="overview-content">
                    <!-- content managed from firebase -->
                </div>

                <div class="grid grid-cols-1 md:grid-cols-3 gap-4 mt-6">
                    <div class="p-4 rounded-lg bg-blue-500 text-white">
                        <div class="text-2xl font-bold" id="stat-members">5</div>
                        <div class="text-sm">Team Members</div>
                    </div>
                    <div class="p-4 rounded-lg bg-green-500 text-white">
                        <div class="text-2xl font-bold" id="stat-sections">10</div>
                        <div class="text-sm">Sections</div>
                    </div>
                    <div class="p-4 rounded-lg bg-orange-500 text-white">
                        <div class="text-2xl font-bold">✓</div>
                        <div class="text-sm">Functional</div>
                    </div>
                </div>
            </section>

            <!-- Practical Tasks -->
            <section id="practical-tasks" class="section panel p-6 mb-6 hidden">
                <div class="flex justify-between items-center mb-4">
                    <h3 class="text-xl font-semibold">Practical Tasks</h3>
                    <button onclick="openAddContent('practical-tasks')" class="bg-green-600 hover:bg-green-700 text-white px-4 py-2 rounded-lg"><i class="fas fa-plus mr-2"></i>Add Task</button>
                </div>
                <div id="practical-tasks-content">
                    <!-- tasks inserted from Firebase -->
                </div>
            </section>

            <section id="resources" class="section panel p-6 mb-6 hidden">
                <h3 class="text-xl font-semibold mb-4">Resources</h3>
                <div id="resources-content"></div>
            </section>

            <section id="implementation" class="section panel p-6 mb-6 hidden">
                <h3 class="text-xl font-semibold mb-4">Implementation</h3>
                <div id="implementation-content"></div>
            </section>
        </main>
    </div>

    <!-- Modals (Add Content & Team Chat & Upload PDF) -->
    <div id="addContentModal" class="modal fixed inset-0 flex items-center justify-center z-50 hidden">
        <div class="bg-white p-6 rounded-xl w-full max-w-2xl">
            <h3 class="text-xl font-semibold mb-4">Add Content</h3>
            <form id="addContentForm" class="space-y-3">
                <input type="hidden" id="contentSection">
                <input type="hidden" id="editContentId">
                <div>
                    <label class="text-sm block mb-1">Title</label>
                    <input id="contentTitle" class="w-full p-3 border rounded-lg" required />
                </div>
                <div>
                    <label class="text-sm block mb-1">Description</label>
                    <textarea id="contentDescription" rows="6" class="w-full p-3 border rounded-lg" required></textarea>
                </div>
                <div class="flex gap-2">
                    <button type="submit" class="bg-green-600 text-white px-4 py-2 rounded-lg">Save</button>
                    <button type="button" onclick="closeAddContent()" class="bg-gray-300 px-4 py-2 rounded-lg">Cancel</button>
                </div>
            </form>
        </div>
    </div>

    <div id="teamChatModal" class="modal fixed inset-0 flex items-center justify-center z-50 hidden">
        <div class="bg-white p-6 rounded-xl w-full max-w-md">
            <div class="flex items-center justify-between mb-3">
                <h3 class="text-lg font-semibold">Team Chat <span id="messageCount" class="text-sm bg-blue-500 text-white px-2 py-1 rounded-full ml-2">0</span></h3>
                <button onclick="closeTeamChat()" class="text-gray-600"><i class="fas fa-times"></i></button>
            </div>
            <div id="chatMessages" class="chat-container p-3 border rounded-lg mb-3">
                <div class="text-center text-gray-500">Loading chat...</div>
            </div>
            <div class="flex gap-2">
                <input id="chatInput" class="flex-1 p-2 border rounded-lg" placeholder="Type a message..." />
                <button id="sendChatButton" class="bg-blue-600 text-white px-3 py-2 rounded-lg"><i class="fas fa-paper-plane"></i></button>
            </div>
        </div>
    </div>

    <div id="uploadPdfModal" class="modal fixed inset-0 flex items-center justify-center z-50 hidden">
        <div class="bg-white p-6 rounded-xl w-full max-w-lg">
            <h3 class="text-lg font-semibold mb-3">Upload PDF (demo)</h3>
            <form id="uploadPdfForm" class="space-y-3">
                <div>
                    <label class="text-sm block mb-1">Title</label>
                    <input id="pdfTitle" class="w-full p-2 border rounded-lg" />
                </div>
                <div>
                    <label class="text-sm block mb-1">Choose PDF</label>
                    <input id="pdfFile" type="file" accept="application/pdf" class="w-full" />
                </div>
                <div class="flex gap-2">
                    <button type="button" onclick="closeUploadPdf()" class="bg-gray-300 px-4 py-2 rounded-lg">Cancel</button>
                    <button type="button" onclick="fakeUploadPdf()" class="bg-blue-600 text-white px-4 py-2 rounded-lg">Upload (demo)</button>
                </div>
            </form>
        </div>
    </div>

    <script>
    (function(){
        // ---------- Firebase config (keep same as earlier in your project) ----------
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
        try { if (!firebase.apps.length) firebase.initializeApp(firebaseConfig); } catch(e) { console.warn(e); }
        const db = firebase.database();

        // small helpers
        const $ = id => document.getElementById(id);

        // UI: team members (static list)
        const teamMembers = [
            { name: 'Marcus', role: 'Engineer' },
            { name: 'Jens', role: 'Analyst' },
            { name: 'Fahad', role: 'Researcher' },
            { name: 'Stefan', role: 'Architect' },
            { name: 'Kaled', role: 'Developer' }
        ];
        function showTeamMembers(){
            const container = $('liveTeamList');
            container.innerHTML = '';
            teamMembers.forEach(m => {
                const el = document.createElement('div');
                el.className = 'flex items-center gap-3 p-2 rounded';
                el.innerHTML = `<div class="w-8 h-8 bg-gradient-to-r from-sky-500 to-indigo-600 text-white rounded-full flex items-center justify-center text-sm font-bold">${m.name.charAt(0)}</div>
                                <div><div class="font-medium text-sm">${m.name}</div><div class="text-xs text-slate-500">${m.role}</div></div>`;
                container.appendChild(el);
            });
        }

        // --------------------------------
        // Content real-time (basic)
        // --------------------------------
        const sections = ['overview','practical-tasks','resources','implementation'];
        function renderSection(section, snapshot){
            const container = $(`${section}-content`);
            if (!container) return;
            container.innerHTML = '';
            if (!snapshot || !snapshot.exists()){
                container.innerHTML = `<div class="text-sm text-slate-600">No content yet.</div>`;
                return;
            }
            snapshot.forEach(child => {
                const item = child.val();
                const box = document.createElement('div');
                box.className = 'p-4 mb-3 panel';
                box.innerHTML = `<h4 class="font-semibold mb-2">${escapeHtml(item.title)}</h4>
                                 <p class="text-sm text-slate-700">${escapeHtml(item.description)}</p>
                                 <div class="text-xs text-slate-500 mt-2">${new Date(item.date).toLocaleString()}</div>`;
                container.appendChild(box);
            });
        }
        function listenAll(){
            sections.forEach(sec => {
                const ref = db.ref(`content/${sec}`);
                ref.off();
                ref.on('value', snap => renderSection(sec, snap));
            });
        }
        function pushContent(section, payload){ return db.ref(`content/${section}`).push(payload); }
        function updateContent(section,id,payload){ return db.ref(`content/${section}/${id}`).update(payload); }

        // escape simple
        function escapeHtml(s=''){ return String(s).replaceAll('&','&amp;').replaceAll('<','&lt;').replaceAll('>','&gt;'); }

        // ---------- Chat simple ----------
        const chat = {
            currentUser: 'You',
            messageCount: 0,
            unread: 0,
            lastRead: Date.now(),
            init(){
                const saved = JSON.parse(localStorage.getItem('currentUser')) || null;
                if (saved) this.currentUser = saved.name || this.currentUser;
                this.listenMessages();
                $('sendChatButton').addEventListener('click', ()=> this.send());
                $('chatInput').addEventListener('keypress', (e)=> { if (e.key === 'Enter') this.send(); });
            },
            listenMessages(){
                const ref = db.ref('team-chat').limitToLast(200);
                ref.off();
                ref.on('child_added', snap => {
                    const msg = snap.val();
                    this.display(msg);
                    this.messageCount++;
                    $('messageCount').textContent = this.messageCount;
                });
            },
            async send(){
                const input = $('chatInput');
                const text = input.value.trim();
                if (!text) return;
                const payload = { user: this.currentUser, text, timestamp: Date.now() };
                try{ await db.ref('team-chat').push(payload); input.value = ''; } catch(e){ console.error(e); alert('Chat send failed'); }
            },
            display(msg){
                const list = $('chatMessages');
                if (!list) return;
                const el = document.createElement('div');
                el.className = 'mb-2';
                el.innerHTML = `<div class="text-xs text-slate-600 font-semibold">${escapeHtml(msg.user)}</div>
                                <div class="text-sm">${escapeHtml(msg.text)}</div>
                                <div class="text-xs text-slate-400">${new Date(msg.timestamp).toLocaleTimeString()}</div>`;
                list.appendChild(el);
                list.scrollTop = list.scrollHeight;
            },
            open(){ $('teamChatModal').classList.remove('hidden'); $('teamChatModal').classList.add('flex'); },
            close(){ $('teamChatModal').classList.add('hidden'); $('teamChatModal').classList.remove('flex'); }
        };

        // ---------- UI modals ----------
        window.openAddContent = function(section){
            $('contentSection').value = section;
            $('editContentId').value = '';
            $('contentTitle').value = '';
            $('contentDescription').value = '';
            const modal = $('addContentModal');
            modal.classList.remove('hidden'); modal.classList.add('flex');
        };
        window.closeAddContent = function(){ const m = $('addContentModal'); m.classList.add('hidden'); m.classList.remove('flex'); };

        // Add/edit form submit
        $('addContentForm').addEventListener('submit', function(e){
            e.preventDefault();
            const section = $('contentSection').value || 'overview';
            const editId = $('editContentId').value;
            const title = $('contentTitle').value.trim();
            const description = $('contentDescription').value.trim();
            if (!title || !description) { alert('Fill title + description'); return; }
            const payload = { title, description, author: (JSON.parse(localStorage.getItem('currentUser'))||{}).name || 'System', date: new Date().toISOString() };
            if (editId) {
                updateContent(section, editId, payload).then(()=> closeAddContent()).catch(e=> { console.error(e); alert('Update failed'); });
            } else {
                pushContent(section, payload).then(()=> closeAddContent()).catch(e=> { console.error(e); alert('Save failed'); });
            }
        });

        // upload pdf modal (demo)
        window.openUploadPdf = function(){ $('uploadPdfModal').classList.remove('hidden'); $('uploadPdfModal').classList.add('flex'); };
        window.closeUploadPdf = function(){ $('uploadPdfModal').classList.add('hidden'); $('uploadPdfModal').classList.remove('flex'); };
        window.fakeUploadPdf = function(){ alert('This demo does not store PDF to server. You can add upload logic or store to Firebase Storage.'); closeUploadPdf(); };

        // team chat open/close
        window.openTeamChat = () => chat.open();
        window.closeTeamChat = () => chat.close();

        // toggle sections
        document.querySelectorAll('.section-btn').forEach(btn => {
            btn.addEventListener('click', function(){
                document.querySelectorAll('.section-btn').forEach(b => b.classList.remove('active-section'));
                this.classList.add('active-section');
                const id = this.getAttribute('data-section');
                document.querySelectorAll('section').forEach(s => s.classList.add('hidden'));
                const el = document.getElementById(id);
                if (el) el.classList.remove('hidden');
            });
        });

        // login simulation (demo)
        const demoUser = { name: 'Kaled Osman', role: 'Developer', avatar: 'K' };
        localStorage.setItem('currentUser', JSON.stringify(demoUser));
        function applyUserUI(){
            const u = JSON.parse(localStorage.getItem('currentUser') || '{}');
            if (!u || !u.name) return;
            $('userName').textContent = u.name;
            $('userRole').textContent = u.role || 'Active';
            $('userAvatar').textContent = (u.avatar || u.name.charAt(0));
            chat.currentUser = u.name;
        }

        // init on DOMContentLoaded
        document.addEventListener('DOMContentLoaded', ()=>{
            showTeamMembers();
            applyUserUI();
            chat.init();
            listenAll();

            // seed minimal overview content once (if empty)
            db.ref('content/overview').once('value').then(snap => {
                if (!snap.exists()){
                    const defaults = [
                        { title: 'Welcome', description: 'Welcome to the Security Chaos Engineering Dashboard. Use Add Content to share notes & files with the team.', author: 'System', date: new Date().toISOString() }
                    ];
                    defaults.forEach(d => db.ref('content/overview').push(d));
                }
            }).catch(()=>{});
        });

        // small helper
        window.toggleTheme = function(){
            document.documentElement.classList.toggle('dark');
            alert('Theme toggle toggled (demo)');
        };

    })();
    </script>
</body>
</html>
