<!doctype html>
<html lang="en">
<head>
  <meta charset="utf-8"/>
  <meta name="viewport" content="width=device-width,initial-scale=1"/>
  <title>Security Chaos Engineering Dashboard — Updated</title>
  <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
  <script src="https://cdn.tailwindcss.com"></script>
  <style>
    .theme-transition{transition:all .25s ease}
    .active-section{background:linear-gradient(135deg,#3b82f6,#1d4ed8);color:#fff}
    .online-indicator{width:8px;height:8px;background:#10B981;border-radius:50%;animation:pulse 2s infinite}
    @keyframes pulse{0%{transform:scale(1);opacity:1}50%{transform:scale(1.2);opacity:.7}100%{transform:scale(1);opacity:1}}
    .modal{display:none;position:fixed;inset:0;background:rgba(0,0,0,.5);z-index:1000;align-items:center;justify-content:center}
    .modal.active{display:flex}
    .section-content{display:none}
    .section-content.active{display:block}
    .file-preview{max-width:100%;height:auto;border-radius:8px}
    .typing-dot{width:6px;height:6px;background:#c7c7c7;border-radius:50%;display:inline-block;margin-right:4px}
    .notif-badge{background:#ef4444;color:#fff;padding:2px 6px;border-radius:999px;font-size:12px}
  </style>
</head>
<body class="bg-gray-50 dark:bg-gray-900 theme-transition text-gray-800">

<!-- LOGIN MODAL -->
<div id="loginModal" class="modal active">
  <div class="bg-white dark:bg-gray-800 p-6 rounded-2xl w-full max-w-md mx-4 shadow-2xl">
    <h2 class="text-2xl font-bold mb-4 text-center">Login to Dashboard</h2>
    <form id="loginForm" class="space-y-4">
      <div>
        <label class="block text-sm font-medium mb-2">Name</label>
        <select id="userSelect" class="w-full p-3 border rounded-xl dark:bg-gray-700 dark:text-white">
          <!-- options injected by JS -->
        </select>
      </div>
      <div class="flex gap-3">
        <button class="flex-1 bg-blue-600 hover:bg-blue-700 text-white py-3 rounded-xl" type="submit">Login</button>
        <button id="loginCancel" class="flex-1 bg-gray-500 hover:bg-gray-600 text-white py-3 rounded-xl" type="button">Cancel</button>
      </div>
      <p class="text-sm text-gray-500">Select your name to login. Admins have extra controls.</p>
    </form>
  </div>
</div>

<!-- ADD CONTENT MODAL -->
<div id="addContentModal" class="modal">
  <div class="bg-white dark:bg-gray-800 p-6 rounded-2xl w-full max-w-2xl mx-4 shadow-2xl">
    <h3 id="addContentTitle" class="text-xl font-bold mb-3">Add Content</h3>
    <form id="addContentForm" class="space-y-4">
      <input id="contentSection" type="hidden" />
      <input id="contentKey" type="hidden" />
      <div>
        <label class="block text-sm mb-1">Title</label>
        <input id="contentTitle" required class="w-full p-3 border rounded-xl dark:bg-gray-700 dark:text-white" />
      </div>
      <div>
        <label class="block text-sm mb-1">Content</label>
        <textarea id="contentDescription" required class="w-full p-3 border rounded-xl dark:bg-gray-700 dark:text-white h-32"></textarea>
      </div>
      <div>
        <label class="block text-sm mb-1">Attach file (optional)</label>
        <input id="contentFile" type="file" class="w-full" />
        <div id="filePreview" class="mt-2"></div>
      </div>
      <div class="flex gap-3">
        <button type="submit" class="flex-1 bg-green-600 hover:bg-green-700 text-white py-3 rounded-xl">Save</button>
        <button id="cancelAdd" type="button" class="flex-1 bg-gray-500 hover:bg-gray-600 text-white py-3 rounded-xl">Cancel</button>
      </div>
    </form>
  </div>
</div>

<!-- CHAT MODAL -->
<div id="chatModal" class="modal">
  <div class="bg-white dark:bg-gray-800 rounded-2xl w-full max-w-[720px] p-4 mx-4 shadow-2xl">
    <div class="flex items-center justify-between mb-3">
      <h3 class="text-lg font-bold">Team Chat <span id="unreadBadge" class="notif-badge ml-2 hidden">0</span></h3>
      <div class="flex items-center gap-2">
        <span id="typingIndicator" class="text-sm text-gray-500 hidden"><span class="typing-dot"></span><span class="typing-dot"></span><span class="typing-dot"></span>Typing...</span>
        <button id="closeChat" class="text-gray-500"><i class="fas fa-times"></i></button>
      </div>
    </div>
    <div id="chatMessages" class="h-64 overflow-y-auto p-3 border rounded-lg bg-gray-50 dark:bg-gray-900"></div>
    <div class="flex gap-2 mt-3">
      <input id="chatInput" placeholder="Type a message" class="flex-1 p-2 border rounded-lg dark:bg-gray-800 dark:text-white" />
      <button id="sendChat" class="bg-blue-600 hover:bg-blue-700 text-white px-4 py-2 rounded-lg"><i class="fas fa-paper-plane"></i></button>
    </div>
  </div>
</div>

<!-- MAIN LAYOUT -->
<div class="flex min-h-screen">
  <aside class="w-80 bg-gray-50 dark:bg-gray-800 p-6 flex-shrink-0">
    <div class="mb-6">
      <h1 class="text-2xl font-bold text-blue-600">Security Chaos Engineering</h1>
      <p class="text-sm text-gray-600 dark:text-gray-400">Group 1 Dashboard</p>
    </div>

    <div class="mb-4 p-3 bg-gradient-to-r from-green-500 to-emerald-600 text-white rounded-lg">
      <div class="flex items-center justify-between">
        <div class="flex items-center gap-2"><span class="online-indicator"></span><span class="font-semibold">TEAM WORKING</span></div>
        <i class="fas fa-bolt"></i>
      </div>
      <div class="text-xs mt-1">All changes are saved to Firebase</div>
    </div>

    <div class="mb-4 bg-white dark:bg-gray-700 p-4 rounded-xl shadow border-2 border-blue-200 dark:border-blue-800">
      <div class="flex items-center gap-3 mb-3">
        <div id="userAvatar" class="w-10 h-10 rounded-full bg-gradient-to-r from-blue-500 to-purple-500 text-white flex items-center justify-center font-bold">Y</div>
        <div>
          <div id="userName" class="font-semibold">You</div>
          <div id="userRole" class="text-xs text-gray-500">Select your name to login</div>
        </div>
      </div>
      <button id="openChatBtn" class="w-full bg-blue-600 hover:bg-blue-700 text-white py-2 rounded-lg mb-2">Team Chat</button>
      <button id="logoutBtn" class="w-full bg-red-600 hover:bg-red-700 text-white py-2 rounded-lg">Logout</button>
    </div>

    <div class="mb-4 p-4 rounded-xl bg-gradient-to-br from-blue-50 to-green-50 dark:from-gray-800 dark:to-gray-700 border-2 border-green-200 dark:border-green-800">
      <h3 class="font-semibold mb-2">Team Members</h3>
      <div id="teamList" class="space-y-2 text-sm"></div>
    </div>

    <div class="mb-4">
      <input id="searchContent" placeholder="Search content..." class="w-full p-3 border rounded-xl dark:bg-gray-700 dark:text-white" />
    </div>

    <nav class="space-y-2 mb-6">
      <button class="section-btn w-full text-left p-3 rounded-xl active-section" data-section="overview">Overview</button>
      <button class="section-btn w-full text-left p-3 rounded-xl" data-section="project-documentation">Project Documentation</button>
      <button class="section-btn w-full text-left p-3 rounded-xl" data-section="team-collaboration">Team Collaboration</button>
      <button class="section-btn w-full text-left p-3 rounded-xl" data-section="team-updates">Team Updates</button>
      <button class="section-btn w-full text-left p-3 rounded-xl" data-section="ai-assistant">AI Assistant</button>
      <button class="section-btn w-full text-left p-3 rounded-xl" data-section="practical-tasks">Practical Tasks</button>
      <button class="section-btn w-full text-left p-3 rounded-xl" data-section="implementation">Implementation</button>
      <button class="section-btn w-full text-left p-3 rounded-xl" data-section="research">Research</button>
      <button class="section-btn w-full text-left p-3 rounded-xl" data-section="resources">Resources</button>
      <button class="section-btn w-full text-left p-3 rounded-xl" data-section="security-testing">Security Testing</button>
      <button class="section-btn w-full text-left p-3 rounded-xl" data-section="monitoring-analytics">Monitoring & Analytics</button>
    </nav>

    <div class="mt-auto">
      <button id="toggleTheme" class="w-full p-3 rounded-xl bg-gray-100 dark:bg-gray-700">Toggle Theme</button>
      <div id="adminPanel" class="mt-3 hidden space-y-2">
        <h4 class="font-semibold">Admin Panel</h4>
        <button id="clearAllContent" class="w-full p-2 bg-red-600 text-white rounded-lg">Delete All Content</button>
        <button id="clearChat" class="w-full p-2 bg-red-500 text-white rounded-lg">Clear Chat</button>
        <div>
          <label class="block text-xs mt-2">Block user</label>
          <select id="blockUserSelect" class="w-full p-2 border rounded"></select>
          <div class="flex gap-2 mt-2">
            <button id="blockUserBtn" class="flex-1 p-2 bg-yellow-600 text-white rounded">Block</button>
            <button id="unblockUserBtn" class="flex-1 p-2 bg-green-600 text-white rounded">Unblock</button>
          </div>
        </div>
      </div>
    </div>
  </aside>

  <main class="flex-1 p-6">
    <header class="mb-6">
      <h1 class="text-3xl font-bold">Security Chaos Engineering Dashboard</h1>
      <p class="text-sm text-gray-500">Group 1 — Team Collaboration</p>
      <div id="activityLogPreview" class="mt-3 text-xs text-gray-500"></div>
    </header>

    <!-- Sections -->
    <section id="overview" class="section-content active">
      <div class="bg-white dark:bg-gray-800 p-4 rounded-2xl shadow">
        <div class="flex justify-between items-center mb-3">
          <h2 class="font-bold text-xl">Overview</h2>
          <div class="flex gap-2">
            <button class="bg-green-600 text-white px-3 py-2 rounded add-content" data-section="overview">Add</button>
            <button id="refreshBtn" class="bg-gray-200 px-3 py-2 rounded"><i class="fas fa-sync"></i></button>
          </div>
        </div>
        <div id="overview-content" class="space-y-3"></div>
      </div>
    </section>

    <!-- Create other sections similarly -->
    <section id="project-documentation" class="section-content">
      <div class="bg-white dark:bg-gray-800 p-4 rounded-2xl shadow">
        <div class="flex justify-between items-center mb-3">
          <h2 class="font-bold text-xl">Project Documentation</h2>
          <button class="bg-green-600 text-white px-3 py-2 rounded add-content" data-section="project-documentation">Add</button>
        </div>
        <div id="project-documentation-content" class="space-y-3"></div>
      </div>
    </section>

    <section id="team-collaboration" class="section-content">
      <div class="bg-white dark:bg-gray-800 p-4 rounded-2xl shadow">
        <div class="flex justify-between items-center mb-3">
          <h2 class="font-bold text-xl">Team Collaboration</h2>
          <button class="bg-green-600 text-white px-3 py-2 rounded add-content" data-section="team-collaboration">Add</button>
        </div>
        <div id="team-collaboration-content" class="space-y-3"></div>
      </div>
    </section>

    <section id="team-updates" class="section-content">
      <div class="bg-white dark:bg-gray-800 p-4 rounded-2xl shadow">
        <div class="flex justify-between items-center mb-3">
          <h2 class="font-bold text-xl">Team Updates</h2>
          <button class="bg-green-600 text-white px-3 py-2 rounded add-content" data-section="team-updates">Add</button>
        </div>
        <div id="team-updates-content" class="space-y-3"></div>
      </div>
    </section>

    <section id="ai-assistant" class="section-content">
      <div class="bg-white dark:bg-gray-800 p-4 rounded-2xl shadow">
        <div class="flex justify-between items-center mb-3">
          <h2 class="font-bold text-xl">AI Assistant</h2>
          <button class="bg-green-600 text-white px-3 py-2 rounded add-content" data-section="ai-assistant">Add</button>
        </div>
        <div id="ai-assistant-content" class="space-y-3"></div>
      </div>
    </section>

    <section id="practical-tasks" class="section-content">
      <div class="bg-white dark:bg-gray-800 p-4 rounded-2xl shadow">
        <div class="flex justify-between items-center mb-3">
          <h2 class="font-bold text-xl">Practical Tasks</h2>
          <button class="bg-green-600 text-white px-3 py-2 rounded add-content" data-section="practical-tasks">Add</button>
        </div>
        <div id="practical-tasks-content" class="space-y-3"></div>
      </div>
    </section>

    <section id="implementation" class="section-content">
      <div class="bg-white dark:bg-gray-800 p-4 rounded-2xl shadow">
        <div class="flex justify-between items-center mb-3">
          <h2 class="font-bold text-xl">Implementation</h2>
          <button class="bg-green-600 text-white px-3 py-2 rounded add-content" data-section="implementation">Add</button>
        </div>
        <div id="implementation-content" class="space-y-3"></div>
      </div>
    </section>

    <section id="research" class="section-content">
      <div class="bg-white dark:bg-gray-800 p-4 rounded-2xl shadow">
        <div class="flex justify-between items-center mb-3">
          <h2 class="font-bold text-xl">Research</h2>
          <button class="bg-green-600 text-white px-3 py-2 rounded add-content" data-section="research">Add</button>
        </div>
        <div id="research-content" class="space-y-3"></div>
      </div>
    </section>

    <section id="resources" class="section-content">
      <div class="bg-white dark:bg-gray-800 p-4 rounded-2xl shadow">
        <div class="flex justify-between items-center mb-3">
          <h2 class="font-bold text-xl">Resources</h2>
          <button class="bg-green-600 text-white px-3 py-2 rounded add-content" data-section="resources">Add</button>
        </div>
        <div id="resources-content" class="space-y-3"></div>
      </div>
    </section>

    <section id="security-testing" class="section-content">
      <div class="bg-white dark:bg-gray-800 p-4 rounded-2xl shadow">
        <div class="flex justify-between items-center mb-3">
          <h2 class="font-bold text-xl">Security Testing</h2>
          <button class="bg-green-600 text-white px-3 py-2 rounded add-content" data-section="security-testing">Add</button>
        </div>
        <div id="security-testing-content" class="space-y-3"></div>
      </div>
    </section>

    <section id="monitoring-analytics" class="section-content">
      <div class="bg-white dark:bg-gray-800 p-4 rounded-2xl shadow">
        <div class="flex justify-between items-center mb-3">
          <h2 class="font-bold text-xl">Monitoring & Analytics</h2>
          <button class="bg-green-600 text-white px-3 py-2 rounded add-content" data-section="monitoring-analytics">Add</button>
        </div>
        <div id="monitoring-analytics-content" class="space-y-3"></div>
      </div>
    </section>

  </main>
</div>

<!-- Firebase SDKs (compat) -->
<script src="https://www.gstatic.com/firebasejs/9.22.1/firebase-app-compat.js"></script>
<script src="https://www.gstatic.com/firebasejs/9.22.1/firebase-database-compat.js"></script>
<script src="https://www.gstatic.com/firebasejs/9.22.1/firebase-storage-compat.js"></script>

<script>
/* ---------------------------
   Firebase config (your project)
   --------------------------- */
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
firebase.initializeApp(firebaseConfig);
const db = firebase.database();
const storage = firebase.storage();

/* ---------------------------
   App state and users
   --------------------------- */
const teamMembers = [
  { name: 'Kaled Osman', role: 'Developer', avatar: 'K', isAdmin:true },
  { name: 'Marcus Tibell', role: 'Engineer', avatar: 'M', isAdmin:false },
  { name: 'Jens Annell', role: 'Analyst', avatar: 'J', isAdmin:false },
  { name: 'Fahad Hussain', role: 'Researcher', avatar: 'F', isAdmin:false },
  { name: 'Luwam', role: 'Designer', avatar: 'L', isAdmin:false },
  { name: 'Stefan Österberg', role: 'Architect', avatar: 'S', isAdmin:false },
  { name: 'Najmaddin', role: 'Security Expert', avatar: 'N', isAdmin:false }
];
let currentUser = null;
let listenersStarted = false;
let unreadCount = 0;

/* ---------------------------
   DOM helpers
   --------------------------- */
const $ = id => document.getElementById(id);
function escapeHtml(s){ return String(s).replaceAll('&','&amp;').replaceAll('<','&lt;').replaceAll('>','&gt;').replaceAll('"','&quot;').replaceAll("'",'&#039;'); }

/* ---------------------------
   Init UI
   --------------------------- */
function initUI(){
  // populate userSelect & team list & block select
  const userSelect = $('userSelect');
  const teamList = $('teamList');
  const blockSelect = $('blockUserSelect');
  userSelect.innerHTML = '';
  blockSelect.innerHTML = '';
  teamList.innerHTML = '';

  teamMembers.forEach(u=>{
    const opt = document.createElement('option'); opt.value = u.name; opt.textContent = u.name; userSelect.appendChild(opt);
    const opt2 = document.createElement('option'); opt2.value = u.name; opt2.textContent = u.name; blockSelect.appendChild(opt2);
    const div = document.createElement('div'); div.className='flex items-center gap-2';
    div.innerHTML = `<div class="w-8 h-8 rounded-full bg-gradient-to-r from-blue-500 to-purple-500 text-white flex items-center justify-center font-bold">${escapeHtml(u.avatar)}</div>
                     <div><div class="font-medium">${escapeHtml(u.name)}</div><div class="text-xs text-gray-500">${escapeHtml(u.role)}</div></div>
                     <div id="presence-${u.name.replaceAll(' ','-')}" class="ml-auto text-xs text-gray-400">offline</div>`;
    teamList.appendChild(div);
  });

  // section buttons
  document.querySelectorAll('.section-btn').forEach(b=>{
    b.addEventListener('click', ()=>{
      document.querySelectorAll('.section-btn').forEach(x=>x.classList.remove('active-section'));
      b.classList.add('active-section');
      const sec = b.dataset.section;
      document.querySelectorAll('.section-content').forEach(s=>s.classList.remove('active'));
      const el = document.getElementById(sec);
      if(el) el.classList.add('active');
    });
  });

  // add-content buttons
  document.querySelectorAll('.add-content').forEach(btn=>{
    btn.addEventListener('click', ()=> openAddModal(btn.dataset.section));
  });

  document.querySelectorAll('.add-content-btn').forEach(btn=>{
    btn.addEventListener('click', ()=> openAddModal(btn.dataset.section));
  });

  $('cancelAdd').addEventListener('click', closeAddModal);
  $('loginForm').addEventListener('submit', handleLogin);
  $('loginCancel').addEventListener('click', ()=>{ if(!currentUser) $('loginModal').classList.add('active'); else $('loginModal').classList.remove('active'); });
  $('logoutBtn').addEventListener('click', logout);
  $('openChatBtn').addEventListener('click', ()=>{ if(!currentUser){ alert('Please login'); return; } openChat(); });
  $('closeChat').addEventListener('click', ()=>$('chatModal').classList.remove('active'));
  $('sendChat').addEventListener('click', sendChat);
  $('chatInput').addEventListener('keypress', e=>{ if(e.key==='Enter') sendChat(); else sendTypingSignal(); });
  $('toggleTheme').addEventListener('click', toggleTheme);
  $('addContentForm').addEventListener('submit', submitContentForm);
  $('searchContent').addEventListener('input', e=> renderAllContent(e.target.value));
  $('refreshBtn').addEventListener('click', ()=> renderAllContent($('searchContent').value));
  $('clearAllContent').addEventListener('click', adminClearAllContent);
  $('clearChat').addEventListener('click', adminClearChat);
  $('blockUserBtn').addEventListener('click', ()=> adminBlockUser(true));
  $('unblockUserBtn').addEventListener('click', ()=> adminBlockUser(false));
}

/* ---------------------------
   Login / Presence / Blocking
   --------------------------- */
function handleLogin(e){
  e.preventDefault();
  const name = $('userSelect').value;
  const user = teamMembers.find(t=>t.name===name);
  if(!user){ alert('Unknown user'); return; }
  currentUser = user;
  localStorage.setItem('current-user', JSON.stringify(currentUser));
  $('loginModal').classList.remove('active');
  $('userName').textContent = currentUser.name;
  $('userRole').textContent = currentUser.role;
  $('userAvatar').textContent = currentUser.avatar;
  if(currentUser.isAdmin) $('adminPanel').classList.remove('hidden'); else $('adminPanel').classList.add('hidden');
  startRealtime();
  setPresence(true);
  window.addEventListener('beforeunload', ()=> setPresence(false));
}

// presence system
const presenceRef = db.ref('presence');
function setPresence(isOnline){
  if(!currentUser) return;
  const node = presenceRef.child(sanitizeKey(currentUser.name));
  node.set({ online: isOnline, lastSeen: Date.now(), name: currentUser.name });
  node.onDisconnect().set({ online:false, lastSeen: Date.now(), name: currentUser.name });
}

// blocklist helpers
function adminBlockUser(shouldBlock){
  const user = $('blockUserSelect').value;
  if(!user) return alert('Choose user');
  const blkRef = db.ref('meta/blockedUsers');
  blkRef.once('value').then(snap=>{
    const list = snap.val() || {};
    if(shouldBlock) list[user] = true; else delete list[user];
    blkRef.set(list);
    alert(shouldBlock? 'User blocked':'User unblocked');
  });
}
function isBlocked(userName, cb){
  db.ref('meta/blockedUsers').once('value').then(snap=>{
    const list = snap.val() || {};
    cb(!!list[userName]);
  });
}

/* ---------------------------
   Start realtime listeners
   --------------------------- */
function startRealtime(){
  if(listenersStarted) return;
  listenersStarted = true;

  // presence listener
  presenceRef.on('value', snap=>{
    const val = snap.val() || {};
    teamMembers.forEach(u=>{
      const key = sanitizeKey(u.name);
      const info = val[key];
      const el = $('presence-'+u.name.replaceAll(' ','-'));
      if(el){
        if(info && info.online) el.textContent = 'online'; else {
          el.textContent = 'offline';
        }
      }
    });
  });

  // typing listener
  db.ref('typing').on('value', snap=>{
    const val = snap.val() || {};
    let someoneTyping = false;
    Object.keys(val).forEach(k=>{
      if(val[k] && k !== sanitizeKey(currentUser ? currentUser.name : '')) someoneTyping = true;
    });
    $('typingIndicator').style.display = someoneTyping ? 'inline-block' : 'none';
  });

  // chat listener
  db.ref('chat').on('value', snapshot=>{
    renderChat(snapshot);
  });

  // content listener (all sections)
  const sections = ['overview','project-documentation','team-collaboration','team-updates','ai-assistant','practical-tasks','implementation','research','resources','security-testing','monitoring-analytics'];
  sections.forEach(sec=>{
    db.ref('content/'+sec).on('value', snap=>{
      renderSectionContent(sec, snap);
    });
  });

  // activity log preview (latest 5)
  db.ref('activity').limitToLast(8).on('value', snap=>{
    const arr = [];
    snap.forEach(c=> arr.push(c.val()));
    const el = $('activityLogPreview');
    el.innerHTML = arr.reverse().slice(0,5).map(i=>`${escapeHtml(i.text)} — ${new Date(i.ts).toLocaleTimeString()}`).join(' · ');
  });

  // blocked users update
  db.ref('meta/blockedUsers').on('value', snap=>{
    // no-op here; checks are performed on write attempts
  });

  // unread badge logic: whenever chat updates and chat modal closed increase unreadCount if sender != me
  db.ref('chat').on('child_added', snap=>{
    const msg = snap.val();
    if(!msg) return;
    if(!document.querySelector('#chatModal').classList.contains('active')){
      if(!currentUser || msg.user !== currentUser.name){
        unreadCount++;
        $('unreadBadge').textContent = unreadCount;
        $('unreadBadge').classList.remove('hidden');
        notifyBrowser('New chat', `${msg.user}: ${msg.text}`);
      }
    }
  });
}

/* ---------------------------
   Chat rendering & actions
   --------------------------- */
function renderChat(snapshot){
  const box = $('chatMessages');
  box.innerHTML = '';
  if(!snapshot.exists()){ box.innerHTML = '<div class="text-gray-500">No messages</div>'; return; }
  snapshot.forEach(child=>{
    const k = child.key; const msg = child.val();
    const mine = currentUser && msg.user === currentUser.name;
    const blocked = msg.blocked;
    const wrapper = document.createElement('div');
    wrapper.className = 'mb-2 p-3 rounded-lg';
    wrapper.style.maxWidth = '75%';
    wrapper.style.marginLeft = mine ? 'auto' : '0';
    wrapper.style.background = mine ? '#1f6feb' : '#e5e7eb';
    wrapper.style.color = mine ? 'white' : (document.documentElement.classList.contains('dark') ? 'white' : 'black');
    wrapper.innerHTML = `
      <div class="flex items-start justify-between gap-2">
        <div>
          <div class="font-semibold text-sm">${escapeHtml(msg.user)} ${blocked?'<span class="text-xs text-red-600 ml-2">[blocked]</span>':''}</div>
          <div class="break-words mt-1">${escapeHtml(msg.text)}</div>
          ${msg.fileUrl? `<div class="mt-2"><a class="underline text-sm" href="${escapeHtml(msg.fileUrl)}" target="_blank">Attachment: ${escapeHtml(msg.fileName||'file')}</a></div>`: ''}
          <div class="text-xs opacity-70 mt-1">${new Date(msg.timestamp).toLocaleString()}</div>
        </div>
        <div class="flex flex-col items-end gap-2">
          ${ (mine || (currentUser && currentUser.isAdmin)) ? `<button class="text-xs text-red-600" data-key="${k}" onclick="deleteChatMessage(this.dataset.key)">Delete</button>` : '' }
        </div>
      </div>
    `;
    box.appendChild(wrapper);
  });

  box.scrollTop = box.scrollHeight;
}

/* send chat */
let typingTimeout = null;
function sendTypingSignal(){
  if(!currentUser) return;
  const key = sanitizeKey(currentUser.name);
  const tRef = db.ref('typing/'+key);
  tRef.set(true);
  if(typingTimeout) clearTimeout(typingTimeout);
  typingTimeout = setTimeout(()=> tRef.set(false), 2000);
}

/* send message with optional file */
function sendChat(){
  if(!currentUser){ alert('Please login'); return; }
  const text = $('chatInput').value.trim();
  if(!text && !$('chatInput').files) return;
  isBlocked(currentUser.name, blocked=>{
    if(blocked){ alert('You are blocked from posting'); return; }
    const fileInput = null; // chat file not implemented; attachments via content
    const payload = { user: currentUser.name, text, timestamp: Date.now() };
    db.ref('chat').push(payload).then(()=>{
      $('chatInput').value = '';
      // activity log
      pushActivity(`${currentUser.name} sent a chat message`);
    }).catch(err=> console.error(err));
  });
}

/* delete chat message */
function deleteChatMessage(key){
  if(!currentUser) return alert('Login required');
  db.ref('chat/'+key).once('value').then(snap=>{
    const m = snap.val();
    if(!m) return;
    if(m.user === currentUser.name || currentUser.isAdmin){
      db.ref('chat/'+key).remove();
      pushActivity(`${currentUser.name} deleted a chat message`);
    } else alert('No permission');
  });
}

/* ---------------------------
   Content rendering & actions
   --------------------------- */
function renderSectionContent(section, snapshot){
  const container = $(section+'-content');
  if(!container) return;
  container.innerHTML = '';
  if(!snapshot.exists()){
    container.innerHTML = `<div class="text-gray-500 p-6">No content yet</div>`; return;
  }
  const search = $('searchContent').value.trim().toLowerCase();
  snapshot.forEach(child=>{
    const key = child.key; const item = child.val();
    const title = item.title||''; const desc = item.description||'';
    if(search){
      const hay = (title+' '+desc+' '+(item.author||'')).toLowerCase();
      if(!hay.includes(search)) return;
    }
    const el = document.createElement('div');
    el.className = 'p-4 border rounded-lg bg-white dark:bg-gray-800';
    el.innerHTML = `
      <div class="flex justify-between items-start gap-3">
        <div style="max-width:80%">
          <h3 class="font-semibold">${escapeHtml(title)}</h3>
          <p class="text-sm mt-2 whitespace-pre-line">${escapeHtml(desc)}</p>
          ${ item.fileUrl ? `<div class="mt-2"><a href="${escapeHtml(item.fileUrl)}" target="_blank" class="underline">Attachment: ${escapeHtml(item.fileName||'file')}</a></div>` : '' }
          <div class="text-xs text-gray-500 mt-2">by ${escapeHtml(item.author)} — ${new Date(item.date).toLocaleString()}</div>
        </div>
        <div class="flex flex-col gap-2">
          ${ ((currentUser && (currentUser.name===item.author)) || (currentUser && currentUser.isAdmin)) ? `<button class="text-sm text-blue-600" data-key="${key}" data-section="${section}" onclick="editContent(this.dataset.section,this.dataset.key)">Edit</button>` : '' }
          ${ ((currentUser && (currentUser.name===item.author)) || (currentUser && currentUser.isAdmin)) ? `<button class="text-sm text-red-600" data-key="${key}" data-section="${section}" onclick="deleteContent(this.dataset.section,this.dataset.key)">Delete</button>` : '' }
        </div>
      </div>
    `;
    container.appendChild(el);
  });
}

/* open add modal */
function openAddModal(section){
  if(!currentUser){ alert('Please login'); return; }
  isBlocked(currentUser.name, blocked => {
    if(blocked){ alert('You are blocked from posting'); return; }
    $('contentSection').value = section;
    $('contentKey').value = '';
    $('contentTitle').value = '';
    $('contentDescription').value = '';
    $('filePreview').innerHTML = '';
    $('addContentTitle').textContent = 'Add Content to ' + section;
    $('addContentModal').classList.add('active');
  });
}

/* submit content form (create or edit) with optional file */
function submitContentForm(e){
  e.preventDefault();
  if(!currentUser) return alert('Login required');
  isBlocked(currentUser.name, blocked=>{
    if(blocked) return alert('Blocked from posting');
    const section = $('contentSection').value;
    const key = $('contentKey').value;
    const title = $('contentTitle').value.trim();
    const desc = $('contentDescription').value.trim();
    const fileInput = $('contentFile');
    const pushContent = (fileMeta)=> {
      const payload = {
        title, description: desc, author: currentUser.name, date: Date.now(),
        fileUrl: fileMeta ? fileMeta.url : null, fileName: fileMeta ? fileMeta.name : null
      };
      if(key){ // update
        db.ref('content/'+section+'/'+key).set(payload).then(()=> {
          pushActivity(`${currentUser.name} edited content in ${section}`);
          $('addContentModal').classList.remove('active');
        });
      } else {
        db.ref('content/'+section).push(payload).then(()=> {
          pushActivity(`${currentUser.name} added content to ${section}`);
          $('addContentModal').classList.remove('active');
        });
      }
    };

    if(fileInput && fileInput.files && fileInput.files.length>0){
      const f = fileInput.files[0];
      const storageRef = storage.ref().child('uploads/'+Date.now()+'_'+f.name);
      const uploadTask = storageRef.put(f);
      uploadTask.on('state_changed', ()=>{}, err=>{ console.error(err); alert('Upload failed'); }, ()=>{
        uploadTask.snapshot.ref.getDownloadURL().then(url=>{
          pushContent({ url, name: f.name });
        });
      });
    } else {
      pushContent(null);
    }
  });
}

/* edit content: prefill modal */
function editContent(section,key){
  db.ref('content/'+section+'/'+key).once('value').then(snap=>{
    const item = snap.val();
    if(!item) return alert('Item not found');
    if(!(currentUser && (currentUser.name===item.author || currentUser.isAdmin))) return alert('No permission');
    $('contentSection').value = section;
    $('contentKey').value = key;
    $('contentTitle').value = item.title||'';
    $('contentDescription').value = item.description||'';
    if(item.fileUrl) $('filePreview').innerHTML = `<a href="${escapeHtml(item.fileUrl)}" target="_blank" class="underline">${escapeHtml(item.fileName||'file')}</a>`;
    else $('filePreview').innerHTML = '';
    $('addContentTitle').textContent = 'Edit Content';
    $('addContentModal').classList.add('active');
  });
}

/* delete content */
function deleteContent(section,key){
  if(!currentUser) return alert('Login required');
  db.ref('content/'+section+'/'+key).once('value').then(snap=>{
    const item = snap.val();
    if(!item) return;
    if(item.author === currentUser.name || currentUser.isAdmin){
      db.ref('content/'+section+'/'+key).remove().then(()=>{ pushActivity(`${currentUser.name} deleted content in ${section}`); });
    } else alert('No permission to delete');
  });
}

/* ---------------------------
   Activity log
   --------------------------- */
function pushActivity(text){
  db.ref('activity').push({ text, ts: Date.now() });
}

/* ---------------------------
   Admin actions
   --------------------------- */
function adminClearAllContent(){
  if(!currentUser || !currentUser.isAdmin) return alert('Admin only');
  if(!confirm('Delete all content?')) return;
  db.ref('content').remove().then(()=> pushActivity(`${currentUser.name} cleared all content`));
}
function adminClearChat(){
  if(!currentUser || !currentUser.isAdmin) return alert('Admin only');
  if(!confirm('Delete all chat?')) return;
  db.ref('chat').remove().then(()=> pushActivity(`${currentUser.name} cleared chat`));
}

/* ---------------------------
   Misc helpers
   --------------------------- */
function sanitizeKey(s){ return String(s).replaceAll('.','_').replaceAll('$','_').replaceAll('#','_').replaceAll('/','_').replaceAll('[','_').replaceAll(']','_'); }

function openChat(){ $('chatModal').classList.add('active'); unreadCount = 0; $('unreadBadge').classList.add('hidden'); }

function closeAddModal(){ $('addContentModal').classList.remove('active'); }

function logout(){ if(currentUser) setPresence(false); currentUser=null; localStorage.removeItem('current-user'); $('userName').textContent='You'; $('userRole').textContent='Select your name to login'; $('userAvatar').textContent='Y'; $('loginModal').classList.add('active'); }

function toggleTheme(){ const h=document.documentElement; if(h.classList.contains('dark')){ h.classList.remove('dark'); localStorage.setItem('theme','light'); } else { h.classList.add('dark'); localStorage.setItem('theme','dark'); } }

/* Browser Notifications */
function notifyBrowser(title, body){
  if(!("Notification" in window)) return;
  if(Notification.permission === "granted") new Notification(title, { body });
  else if(Notification.permission !== "denied") Notification.requestPermission().then(p=>{ if(p==="granted") new Notification(title,{body}); });
}

/* render all content currently by fetching once (to support search) */
function renderAllContent(search=''){
  const sections = ['overview','project-documentation','team-collaboration','team-updates','ai-assistant','practical-tasks','implementation','research','resources','security-testing','monitoring-analytics'];
  sections.forEach(sec=>{
    db.ref('content/'+sec).once('value').then(snap=> renderSectionContent(sec, snap));
  });
}

/* ---------------------------
   Initial load: restore user & init
   --------------------------- */
document.addEventListener('DOMContentLoaded', ()=>{
  initUI();
  const saved = localStorage.getItem('current-user');
  if(saved){ currentUser = JSON.parse(saved); $('userName').textContent = currentUser.name; $('userRole').textContent = currentUser.role; $('userAvatar').textContent = currentUser.avatar; if(currentUser.isAdmin) $('adminPanel').classList.remove('hidden'); $('loginModal').classList.remove('active'); startRealtime(); setPresence(true); }
  const theme = localStorage.getItem('theme'); if(theme==='dark') document.documentElement.classList.add('dark');
  // allow notifications
  if(Notification.permission !== "granted") Notification.requestPermission();
  // load initial content
  renderAllContent();
});

/* ---------------------------
   File input preview (simple)
   --------------------------- */
$('contentFile')?.addEventListener('change', function(){
  const f = this.files[0];
  if(!f) return $('filePreview').innerHTML='';
  if(f.type.startsWith('image/')){
    const url = URL.createObjectURL(f);
    $('filePreview').innerHTML = `<img src="${url}" class="file-preview" />`;
  } else {
    $('filePreview').innerHTML = `<div class="text-sm">${escapeHtml(f.name)}</div>`;
  }
});
</script>
</body>
</html>
