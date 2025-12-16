<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0"/>
    <title>Security Chaos Engineering Dashboard</title>

    <!-- Icons + Tailwind -->
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <script src="https://cdn.tailwindcss.com"></script>

    <!-- Firebase SDK -->
    <script src="https://www.gstatic.com/firebasejs/9.22.1/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.1/firebase-database-compat.js"></script>

    <style>
        :root{
            --overlay-gradient: linear-gradient(180deg, rgba(8,11,26,0.45), rgba(8,11,26,0.75));
        }

        html,body…
[01:16, 2025-12-16] Kaled: <!-- ===============================
        MAIN LAYOUT
================================= -->
<div class="flex min-h-screen relative" style="z-index: 1;">

    <!-- ===============================
            SIDEBAR
    ================================= -->
    <aside class="w-80 bg-gray-900/70 backdrop-blur-xl text-white p-6 border-r border-gray-700">

        <!-- Dashboard Title -->
        <div class="mb-8">
            <h1 class="text-2xl font-bold text-blue-400">Security Chaos Engineering</h1>
            <p class="text-gray-300 mt-1">Group 1 Dashboard</p>
        </div>

        <!-- Logged-in User Box -->
        <div class="mb-6">
            <div class="bg-gray-800/60 p-4 rounded-xl shadow-lg border border-gray-700">
                <div class="f…
[01:17, 2025-12-16] Kaled: <!-- ---------- SCRIPT: Firebase + Real-time logic + PDF handling (PART 3) ---------- -->
<script>
(function(){
    // ---------- Configuration ----------
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

    // Initialize Firebase (safe if already initialized)
    try { if (!firebase.apps.length) firebase.initializeApp(firebaseConfig); } catch(e){ console.warn('Firebase init warning:', e); }
    const db = firebase.database();
    const storage = firebase.storage ? firebase.storage() : null;

    // ---------- Helpers ----------
    const sections = [
        'overview','project-documentation','team-collaboration','team-updates',
        'ai-assistant','practical-tasks','implementation','research',
        'resources','security-testing','monitoring-analytics','pdf-library'
    ];
    const $ = id => document.getElementById(id);

    function escapeHtml(s = '') {
        return String(s)
            .replaceAll('&','&amp;').replaceAll('<','&lt;').replaceAll('>','&gt;')
            .replaceAll('"','&quot;').replaceAll("'",'&#039;');
    }

    // ---------- Auth / Login (local demo) ----------
    const loginSystem = {
        users: JSON.parse(localStorage.getItem('teamUsers')) || null,
        currentUser: JSON.parse(localStorage.getItem('currentUser')) || null,
        init() {
            if (!this.users) {
                const defaultUsers = [
                    { id: 1, name: "Kaled Osman", email: "kaled@team.com", password: "kaled123", role: "Project Lead", avatar: "K" },
                    { id: 2, name: "Marcus Tibell", email: "marcus@team.com", password: "marcus123", role: "Engineer", avatar: "M" },
                    { id: 3, name: "Jens Annell", email: "jens@team.com", password: "jens123", role: "Analyst", avatar: "J" },
                    { id: 4, name: "Fahad Hussain", email: "fahad@team.com", password: "fahad123", role: "Researcher", avatar: "F" },
                    { id: 5, name: "Luwam", email: "luwam@team.com", password: "luwam123", role: "Designer", avatar: "L" },
                    { id: 6, name: "Stefan Österberg", email: "stefan@team.com", password: "stefan123", role: "Architect", avatar: "S" },
                    { id: 7, name: "Najmaddin", email: "najmaddin@team.com", password: "najmaddin123", role: "Security Expert", avatar: "N" }
                ];
                this.users = defaultUsers;
                localStorage.setItem('teamUsers', JSON.stringify(defaultUsers));
            }

            // restore login state
            if (this.currentUser) this.updateUIForLoggedIn();

            // attach login handler
            const form = $('loginForm');
            if (form) form.addEventListener('submit', (e) => { e.preventDefault(); this.attemptLogin(); });
        },
        attemptLogin() {
            const email = ($('loginEmail') && $('loginEmail').value.trim()) || '';
            const password = ($('loginPassword') && $('loginPassword').value.trim()) || '';
            const found = this.users.find(u => u.email === email && u.password === password);
            if (!found) { alert('Invalid credentials'); return; }
            this.currentUser = found;
            localStorage.setItem('currentUser', JSON.stringify(found));
            this.updateUIForLoggedIn();
            chat.currentUser = found.name;
            if ($('loginModal')) $('loginModal').style.display = 'none';
            adminSystem.tryInit();
        },
        logout() {
            this.currentUser = null;
            localStorage.removeItem('currentUser');
            if ($('loginModal')) $('loginModal').style.display = 'flex';
            if ($('userName')) $('userName').textContent = 'You';
            if ($('userRole')) $('userRole').textContent = 'Active User';
            if ($('userAvatar')) $('userAvatar').textContent = 'Y';
            chat.currentUser = 'You';
            adminSystem.tryInit();
        },
        updateUIForLoggedIn() {
            const u = this.currentUser; if (!u) return;
            if ($('userName')) $('userName').textContent = u.name;
            if ($('userRole')) $('userRole').textContent = u.role;
            if ($('userAvatar')) $('userAvatar').textContent = u.avatar || u.name.charAt(0);
        }
    };

    // ---------- Admin system ----------
    const adminSystem = {
        tryInit() {
            const cur = JSON.parse(localStorage.getItem('currentUser')) || null;
            if (cur && cur.email === 'kaled@team.com') this.renderAdmin();
            else { const panel = $('adminPanel'); if (panel) panel.innerHTML = ''; }
        },
        renderAdmin() {
            const panel = $('adminPanel');
            if (!panel) return;
            panel.innerHTML = `
                <div class="mt-6 p-4 bg-yellow-100 dark:bg-yellow-900 border border-yellow-400 rounded-xl">
                    <h3 class="font-semibold mb-2 text-yellow-800 dark:text-yellow-200 flex items-center gap-2">
                        <i class="fas fa-crown"></i> Admin Panel
                    </h3>
                    <div class="space-y-2">
                        <button id="adminClearContent" class="w-full bg-red-600 hover:bg-red-700 text-white py-2 rounded-lg">Delete All Content</button>
                        <button id="adminClearChat" class="w-full bg-purple-600 hover:bg-purple-700 text-white py-2 rounded-lg">Clear Chat</button>
                    </div>
                </div>
            `;
            document.getElementById('adminClearContent').addEventListener('click', clearAllContentAdmin);
            document.getElementById('adminClearChat').addEventListener('click', clearChatAdmin);
        }
    };

    // ---------- Team members UI ----------
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
        const liveTeamList = $('liveTeamList'); if (!liveTeamList) return;
        liveTeamList.innerHTML = '';
        teamMembers.forEach(member => {
            const memberElement = document.createElement('div');
            memberElement.className = 'flex items-center space-x-3 p-2 bg-gray-800/60 rounded-lg';
            memberElement.innerHTML = `
                <div class="relative">
                    <div class="w-8 h-8 bg-gradient-to-r from-blue-500 to-purple-500 rounded-full flex items-center justify-center text-white text-sm font-bold">
                        ${member.name.charAt(0)}
                    </div>
                    <span class="online-indicator absolute -top-1 -right-1 border-2 border-gray-800"></span>
                </div>
                <div class="flex-1">
                    <div class="font-medium text-white text-sm">${member.name}</div>
                    <div class="text-xs text-gray-300">${member.role}</div>
                </div>
            `;
            liveTeamList.appendChild(memberElement);
        });
    }

    // ---------- Chat system ----------
    const chat = {
        currentUser: 'You',
        messageCount: 0,
        unread: 0,
        lastRead: Date.now(),
        init() {
            const saved = JSON.parse(localStorage.getItem('currentUser')); if (saved && saved.name) this.currentUser = saved.name;
            if ($('chatStatus')) $('chatStatus').style.background = '#10B981';
            this.listenMessages();
            const sendBtn = $('sendChatButton'); if (sendBtn) sendBtn.addEventListener('click', ()=> this.send());
            const chatInput = $('chatInput'); if (chatInput) chatInput.addEventListener('keypress', (e)=>{ if (e.key==='Enter') this.send(); });
        },
        listenMessages() {
            const ref = db.ref('team-chat').limitToLast(200);
            ref.off();
            ref.on('child_added', snap => {
                const msg = snap.val();
                this.display(msg);
                this.messageCount++;
                if (msg.timestamp > this.lastRead && !this.isOpen()) {
                    this.unread++; this.updateUnread();
                }
            });
        },
        async send() {
            const input = $('chatInput'); if (!input) return;
            const text = input.value.trim(); if (!text) return;
            const payload = { user: this.currentUser || 'You', text, timestamp: Date.now() };
            try {
                await db.ref('team-chat').push(payload);
                input.value = '';
                this.lastRead = Date.now();
                this.unread = 0;
                this.updateUnread();
            } catch (e) { console.error('chat send failed', e); alert('Failed to send message'); }
        },
        display(msg) {
            const list = $('chatMessages'); if (!list) return;
            const placeholder = list.querySelector('.text-center'); if (placeholder) list.innerHTML = '';
            const el = document.createElement('div');
            const mine = msg.user === this.currentUser;
            el.className = p-3 mb-2 rounded-lg max-w-[85%] ${mine ? 'bg-blue-500 text-white ml-auto' : 'bg-gray-700 text-white'};
            el.innerHTML = <div class="font-semibold text-sm">${escapeHtml(msg.user)}</div><div class="break-words mt-1">${escapeHtml(msg.text)}</div><div class="text-xs opacity-70 mt-2">${new Date(msg.timestamp).toLocaleTimeString()}</div>;
            list.appendChild(el);
            list.scrollTop = list.scrollHeight;
            const cnt = $('messageCount'); if (cnt) cnt.textContent = (parseInt(cnt.textContent||'0')+1).toString();
        },
        open() { if ($('teamChatModal')) $('teamChatModal').classList.add('active'); this.lastRead = Date.now(); this.unread = 0; this.updateUnread(); },
        close() { if ($('teamChatModal')) $('teamChatModal').classList.remove('active'); },
        isOpen() { return $('teamChatModal') && $('teamChatModal').classList.contains('active'); },
        updateUnread() { const u = $('unreadCount'); if (!u) return; if (this.unread>0) { u.textContent = this.unread; u.classList.remove('hidden'); } else { u.classList.add('hidden'); } }
    };

    // ---------- Content system (Realtime) with per-user edit/delete ----------
    const contentSystem = {
        renderSection(section, snapshot) {
            const container = $(${section}-content);
            if (!container) return;
            container.innerHTML = '';
            if (!snapshot || !snapshot.exists()) {
                container.innerHTML = <div class="text-center p-8 text-gray-300"><i class="fas fa-inbox text-4xl mb-4"></i><p>No content added yet.</p></div>;
                return;
            }
            snapshot.forEach(child => {
                const item = child.val(); const id = child.key;
                const cur = JSON.parse(localStorage.getItem('currentUser')) || {};
                // create element
                const el = document.createElement('div');
                el.className = 'p-6 mb-4 rounded-xl bg-gray-800/60 border border-gray-700';
                // Determine permission: canEdit if author equals currentUser.name OR current user is admin (kaled)
                const canEdit = cur.name === item.author || (cur.email === 'kaled@team.com');
                // render header with conditional edit/delete
                el.innerHTML = `
                    <div class="flex justify-between items-start mb-3">
                        <div>
                            <h3 class="text-lg font-semibold text-white">${escapeHtml(item.title)}</h3>
                            <div class="text-xs text-gray-300 mt-1">${escapeHtml(item.author || 'Group 1')} • ${new Date(item.date).toLocaleDateString()}</div>
                        </div>
                        <div class="flex items-center gap-2">
                            ${ canEdit ? <button data-id="${id}" data-section="${section}" class="edit-btn px-2 py-1 rounded-md bg-blue-600 text-white text-sm"><i class="fas fa-edit"></i></button> : ''}
                            ${ canEdit ? <button data-id="${id}" data-section="${section}" class="del-btn px-2 py-1 rounded-md bg-red-600 text-white text-sm"><i class="fas fa-trash"></i></button> : ''}
                        </div>
                    </div>
                    <div class="text-gray-200 whitespace-pre-line">${escapeHtml(item.description)}</div>
                `;
                container.appendChild(el);
            });

            // Attach handlers for the buttons that exist
            container.querySelectorAll('.edit-btn').forEach(b => b.addEventListener('click', () => {
                const id = b.getAttribute('data-id'); const sec = b.getAttribute('data-section'); openEditModalFor(sec, id);
            }));
            container.querySelectorAll('.del-btn').forEach(b => b.addEventListener('click', () => {
                const id = b.getAttribute('data-id'); const sec = b.getAttribute('data-section'); deleteContent(sec, id);
            }));
        },
        listenAll() {
            sections.forEach(sec => {
                const ref = db.ref(content/${sec}); ref.off();
                ref.on('value', snap => this.renderSection(sec, snap));
            });
        },
        pushContent(section, payload) { return db.ref(content/${section}).push(payload); },
        updateContent(section, id, payload) { return db.ref(content/${section}/${id}).update(payload); },
        removeContent(section, id) { return db.ref(content/${section}/${id}).remove(); }
    };

    // ---------- UI: Add / Edit modals ----------
    function openAddContent(section) {
        if (!$('addContentModal')) return;
        $('contentSection').value = section;
        $('editContentId').value = '';
        $('contentTitle').value = '';
        $('contentDescription').value = '';
        const saved = JSON.parse(localStorage.getItem('currentUser'));
        if (saved && saved.name && $('contentAuthor')) $('contentAuthor').value = saved.name;
        $('addContentModal').classList.add('active');
    }
    function closeAddContent() { if ($('addContentModal')) $('addContentModal').classList.remove('active'); }

    function openEditModalFor(section, id) {
        db.ref(content/${section}/${id}).once('value').then(snap => {
            const item = snap.val();
            if (!item) return alert('Item not found');
            $('contentSection').value = section;
            $('editContentId').value = id;
            $('contentTitle').value = item.title || '';
            $('contentDescription').value = item.description || '';
            $('contentAuthor').value = item.author || (JSON.parse(localStorage.getItem('currentUser'))||{}).name || 'Unknown';
            $('addContentModal').classList.add('active');
        }).catch(err => { console.error(err); alert('Failed to load item for edit'); });
    }

    // submit add/edit
    const addForm = $('addContentForm');
    if (addForm) {
        addForm.addEventListener('submit', function(e){
            e.preventDefault();
            const section = $('contentSection').value;
            const editId = $('editContentId').value;
            const title = $('contentTitle').value.trim();
            const description = $('contentDescription').value.trim();
            const author = $('contentAuthor').value;
            if (!title || !description) return alert('Fill title and content');
            const payload = { title, description, author, date: new Date().toISOString() };
            if (editId) {
                // Check permission before update: only original author or admin can update
                db.ref(content/${section}/${editId}).once('value').then(snap => {
                    const item = snap.val() || {};
                    const cur = JSON.parse(localStorage.getItem('currentUser')) || {};
                    if (item.author === (cur.name) || cur.email === 'kaled@team.com') {
                        contentSystem.updateContent(section, editId, payload).then(()=> { closeAddContent(); }).catch(err=> { console.error(err); alert('Update failed'); });
                    } else {
                        alert('You do not have permission to edit this item');
                    }
                });
            } else {
                contentSystem.pushContent(section, payload).then(()=> { closeAddContent(); }).catch(err=> { console.error(err); alert('Save failed'); });
            }
        });
    }

    // delete with permission check: only author or admin
    function deleteContent(section, id) {
        if (!confirm('Are you sure you want to delete this content?')) return;
        db.ref(content/${section}/${id}).once('value').then(snap => {
            const item = snap.val() || {};
            const cur = JSON.parse(localStorage.getItem('currentUser')) || {};
            if (item.author === (cur.name) || cur.email === 'kaled@team.com') {
                contentSystem.removeContent(section, id).catch(err => { console.error(err); alert('Delete failed'); });
            } else {
                alert('You do not have permission to delete this item');
            }
        });
    }

    // ---------- PDF Library: upload + list (requires firebase.storage) ----------
    function openPdfUpload() { if ($('pdfUploadForm')) $('pdfUploadForm').reset(); if ($('pdfUploadProgress')) $('pdfUploadProgress').textContent = ''; if ($('pdfUploadModal')) $('pdfUploadModal').classList.add('active'); }
    function closePdfUpload() { if ($('pdfUploadModal')) $('pdfUploadModal').classList.remove('active'); }

    if ($('pdfUploadForm')) {
        $('pdfUploadForm').addEventListener('submit', async function(e){
            e.preventDefault();
            if (!storage) { alert('Storage not available'); return; }
            const title = $('pdfTitle').value.trim(); const desc = $('pdfDescription').value.trim();
            const fileInput = $('pdfFile'); if (!fileInput || !fileInput.files.length) return alert('Choose a PDF');
            const file = fileInput.files[0]; if (file.type !== 'application/pdf') return alert('Only PDF allowed');

            const timestamp = Date.now();
            const storageRef = storage.ref().child(pdfs/${timestamp}_${file.name});
            const uploadTask = storageRef.put(file);
            if ($('pdfUploadProgress')) $('pdfUploadProgress').textContent = 'Uploading... 0%';

            uploadTask.on('state_changed',
                (snapshot) => {
                    const percent = Math.floor((snapshot.bytesTransferred / snapshot.totalBytes) * 100);
                    if ($('pdfUploadProgress')) $('pdfUploadProgress').textContent = Uploading... ${percent}%;
                },
                (error) => { console.error('Upload failed', error); alert('Upload failed'); },
                async () => {
                    const url = await uploadTask.snapshot.ref.getDownloadURL();
                    const meta = { title, description: desc, url, storagePath: uploadTask.snapshot.ref.fullPath, date: new Date().toISOString(), author: (JSON.parse(localStorage.getItem('currentUser')) || {}).name || 'Group 1' };
                    await db.ref('pdfs').push(meta);
                    if ($('pdfUploadProgress')) $('pdfUploadProgress').textContent = 'Upload complete';
                    closePdfUpload();
                }
            );
        });
    }

    function renderPdfLibrary(snapshot) {
        const container = $('pdf-library-content'); if (!container) return;
        container.innerHTML = '';
        if (!snapshot || !snapshot.exists()) {
            container.innerHTML = <div class="text-center p-6 text-gray-300"><i class="fas fa-file-pdf text-4xl mb-3"></i>No PDFs yet</div>;
            return;
        }
        snapshot.forEach(child => {
            const item = child.val(); const id = child.key;
            const el = document.createElement('div');
            el.className = 'p-4 mb-3 rounded-lg bg-gray-800/60 border border-gray-700 flex justify-between items-start';
            el.innerHTML = `
                <div>
                    <div class="font-semibold text-white">${escapeHtml(item.title)}</div>
                    <div class="text-sm text-gray-300 mt-1">${escapeHtml(item.description)}</div>
                    <div class="text-xs text-gray-400 mt-2">${new Date(item.date).toLocaleDateString()} • ${escapeHtml(item.author||'Group 1')}</div>
                </div>
                <div class="flex flex-col items-end gap-2">
                    <a href="${item.url}" target="_blank" class="bg-blue-600 px-3 py-2 rounded text-white text-sm"><i class="fas fa-eye mr-1"></i>Open</a>
                    <button data-id="${id}" class="delete-pdf-btn bg-red-600 px-3 py-2 rounded text-white text-sm">Delete</button>
                </div>
            `;
            container.appendChild(el);
        });

        // attach delete handlers
        container.querySelectorAll('.delete-pdf-btn').forEach(b => b.addEventListener('click', async (e) => {
            const id = b.getAttribute('data-id');
            if (!confirm('Delete this PDF?')) return;
            const cur = JSON.parse(localStorage.getItem('currentUser')) || {};
            if (cur.email !== 'kaled@team.com') { alert('Only admin (kaled) can delete PDFs'); return; }
            const snapshot = await db.ref(pdfs/${id}).once('value'); const meta = snapshot.val();
            if (meta && meta.storagePath && storage) {
                try { await storage.ref(meta.storagePath).delete(); } catch (err) { console.warn('Storage delete failed', err); }
            }
            await db.ref(pdfs/${id}).remove();
        }));
    }

    function listenPdfs() {
        const ref = db.ref('pdfs'); ref.off(); ref.on('value', snap => renderPdfLibrary(snap));
    }

    // ---------- Admin utilities ----------
    function clearAllContentAdmin() {
        if (!confirm('Delete all content for all sections?')) return;
        const updates = {};
        sections.forEach(sec => updates[content/${sec}] = null);
        db.ref().update(updates).then(()=> alert('All content cleared')).catch(err=> { console.error(err); alert('Failed to clear content'); });
    }
    function clearChatAdmin() {
        if (!confirm('Clear all chat messages?')) return;
        db.ref('team-chat').remove().then(()=> {
            const list = $('chatMessages'); if (list) list.innerHTML = '<div class="text-center text-gray-300 p-4"><i class="fas fa-comments"></i> Chat cleared</div>';
        }).catch(err=> { console.error(err); alert('Failed to clear chat'); });
    }

    // ---------- Seeding Practical Tasks (only once) ----------
    async function seedPracticalTasksIfEmpty() {
        const ref = db.ref('content/practical-tasks');
        const snap = await ref.once('value');
        if (snap.exists()) return;
        const teamNames = 'Marcus, Jens, Fahad, Stefan, Kaled';
        const tasks = [
            {
                title: 'TASK 01 – Debug a Broken Feature',
                description:
`1. What Was the Task?
The goal was to simulate a real debugging situation...
(see full report in Dashboard)`,
                author: teamNames,
                date: new Date().toISOString()
            },
            {
                title: 'TASK 02 – Build a Mini Tool',
                description: 'Built a small tool to fetch data from an API, parse and present results. Focused on error handling and clean UX.',
                author: teamNames,
                date: new Date().toISOString()
            },
            {
                title: 'TASK 03 – Responsive Two-Column Web Component',
                description: 'Created a responsive component (two columns desktop, one column mobile). Tested across breakpoints.',
                author: teamNames,
                date: new Date().toISOString()
            },
            {
                title: 'TASK 04 – Reverse Engineer a Result',
                description: 'Given outputs we deduced structure and rebuilt a scripted generator that matches the format.',
                author: teamNames,
                date: new Date().toISOString()
            },
            {
                title: 'TASK 05 – XWiki LaTeX Math Rendering',
                description: 'Proposed preprocessor/postprocessor pipeline to protect LaTeX regions from Markdown.',
                author: teamNames,
                date: new Date().toISOString()
            }
        ];
        for (const t of tasks) {
            await db.ref('content/practical-tasks').push(t);
        }
    }

    // ---------- Seed default content if empty ----------
    function seedDefaultContentIfEmpty() {
        const ref = db.ref('content/overview');
        ref.once('value').then(snap => {
            if (snap.exists()) return;
            const defaults = {
                overview: {
                    welcome: {
                        title: '🚀 Welcome to Security Chaos Engineering Dashboard',
                        description: 'This is your team collaboration platform. Use Add Content to share with your team.',
                        author: 'System',
                        date: new Date().toISOString()
                    }
                },
                'team-collaboration': {
                    guidelines: {
                        title: 'Team Collaboration Guidelines',
                        description: 'Share meeting notes and tasks here.',
                        author: 'Kaled Osman',
                        date: new Date().toISOString()
                    }
                }
            };
            Object.keys(defaults).forEach(sec => {
                const secRef = db.ref(content/${sec});
                Object.keys(defaults[sec]).forEach(key => secRef.push(defaults[sec][key]));
            });
        }).catch(err => console.error('Seed check failed', err));
    }

    // ---------- INIT: listeners + UI wiring ----------
    function initAll() {
        loginSystem.init();
        chat.init();
        contentSystem.listenAll();
        listenPdfs();
        showTeamMembers();
        seedDefaultContentIfEmpty();
        seedPracticalTasksIfEmpty().catch(console.error);

        // section nav
        document.querySelectorAll('.section-btn').forEach(btn => {
            btn.addEventListener('click', function() {
                document.querySelectorAll('.section-btn').forEach(b=>b.classList.remove('active-section'));
                this.classList.add('active-section');
                document.querySelectorAll('.section-content').forEach(s=>s.classList.remove('active'));
                const id = this.getAttribute('data-section'); const el = document.getElementById(id);
                if (el) el.classList.add('active');
            });
        });

        // attach open chat buttons
        document.querySelectorAll('[onclick="openTeamChat()"]').forEach(b => b.addEventListener('click', ()=> chat.open()));

        // attach openPdfUpload if exists
        window.openPdfUpload = openPdfUpload;
        window.closePdfUpload = closePdfUpload;
        window.openAddContent = openAddContent;
        window.openEditModalFor = openEditModalFor;
        window.deleteContent = deleteContent;
        window.openTeamChat = () => chat.open();
        window.closeTeamChat = () => chat.close();
        window.sendChatMessage = () => chat.send();
        window.clearAllContentAdmin = clearAllContentAdmin;
        window.clearChatAdmin = clearChatAdmin;
        window.closeAddContent = closeAddContent;

        // parallax
        const bg = document.getElementById('bgHero');
        window.addEventListener('scroll', () => {
            const y = window.scrollY || 0;
            const translate = Math.min(60, Math.round(y * 0.12));
            if (bg) bg.style.transform = translateY(${translate}px) scale(1.03);
        });
    }

    // ---------- DOM ready ----------
    document.addEventListener('DOMContentLoaded', () => { initAll(); });

})();
</script>


