<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="utf-8" />
  <meta name="viewport" content="width=device-width,initial-scale=1" />
  <title>Security Chaos Engineering Dashboard — Group 1</title>

  <!-- Tailwind + FontAwesome -->
  <script src="https://cdn.tailwindcss.com"></script>
  <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css"/>

  <!-- Firebase compat -->
  <script src="https://www.gstatic.com/firebasejs/9.22.1/firebase-app-compat.js"></script>
  <script src="https://www.gstatic.com/firebasejs/9.22.1/firebase-database-compat.js"></script>
  <script src="https://www.gstatic.com/firebasejs/9.22.1/firebase-storage-compat.js"></script>

  <style>
    :root{
      --overlay: rgba(4,6,15,0.55);
      --card-bg: rgba(255,255,255,0.9);
    }
    html,body,#app{height:100%;margin:0}
    body{
      font-family:Inter,ui-sans-serif,system-ui,-apple-system,Segoe UI,Roboto,"Helvetica Neue",Arial;
      background:#0b1020;
      color:#111827;
    }

    /* Full-screen background image */
    body::before{
      content:"";
      position:fixed;
      inset:0;
      background-image: url('bg.jpg');
      background-size:cover;
      background-position:center;
      background-repeat:no-repeat;
      z-index:-3;
      filter: saturate(.95) contrast(.9) brightness(.65);
    }
    /* dark overlay so text remains readable */
    body::after{
      content:"";
      position:fixed;
      inset:0;
      background: linear-gradient(180deg, rgba(2,6,23,0.45), rgba(2,6,23,0.6));
      z-index:-2;
    }

    .glass {
      background: rgba(255,255,255,0.92);
      backdrop-filter: blur(6px);
    }
    .glass-dark {
      background: rgba(17,24,39,0.7);
      color: #fff;
      backdrop-filter: blur(6px);
    }

    .sidebar {
      min-width:260px;
      max-width:320px;
    }

    /* responsive adjustments */
    @media (max-width:900px){
      .sidebar{width:100%;max-width:none;min-width:0}
      .layout{flex-direction:column}
    }

    .section-card { border-radius:16px; box-shadow:0 6px 20px rgba(2,6,23,0.25); }
    .task-content pre { white-space:pre-wrap; word-wrap:break-word; }

  </style>
</head>
<body>
  <div id="app" class="flex layout min-h-screen">

    <!-- SIDEBAR -->
    <aside class="sidebar p-6 glass-dark text-white flex flex-col gap-6">
      <div>
        <h1 class="text-2xl font-bold">Security Chaos Engineering</h1>
        <p class="text-sm opacity-80 mt-1">Group 1 Dashboard</p>
      </div>

      <div class="p-3 rounded-lg bg-black/20 flex items-center justify-between">
        <div>
          <div class="text-xs opacity-75">LIVE</div>
          <div class="font-semibold">Real-time collaboration</div>
        </div>
        <div class="w-8 h-8 rounded-full bg-emerald-400 ring-2 ring-white"></div>
      </div>

      <div class="bg-white/10 p-3 rounded-lg">
        <div class="flex items-center gap-3">
          <div id="userAvatar" class="w-10 h-10 rounded-full bg-slate-700 flex items-center justify-center font-bold">Y</div>
          <div>
            <div id="userName" class="font-semibold">You</div>
            <div id="userRole" class="text-xs opacity-80">Active User</div>
          </div>
        </div>
        <div class="mt-3">
          <button id="openChatBtn" class="w-full bg-emerald-500 text-white py-2 rounded-lg"><i class="fas fa-comments mr-2"></i>Team Chat</button>
        </div>
      </div>

      <div class="flex-1 overflow-auto">
        <nav class="flex flex-col gap-2 mt-2">
          <button class="nav-btn text-left p-3 rounded-lg section-card glass text-white active" data-section="overview"> <i class="fas fa-home mr-2"></i> Overview</button>
          <button class="nav-btn text-left p-3 rounded-lg section-card glass text-white" data-section="project-documentation"> <i class="fas fa-folder mr-2"></i> Project Documentation</button>
          <button class="nav-btn text-left p-3 rounded-lg section-card glass text-white" data-section="team-collaboration"> <i class="fas fa-users mr-2"></i> Team Collaboration</button>
          <button class="nav-btn text-left p-3 rounded-lg section-card glass text-white" data-section="team-updates"> <i class="fas fa-bullhorn mr-2"></i> Team Updates</button>
          <button class="nav-btn text-left p-3 rounded-lg section-card glass text-white" data-section="practical-tasks"> <i class="fas fa-tasks mr-2"></i> Practical Tasks</button>
          <button class="nav-btn text-left p-3 rounded-lg section-card glass text-white" data-section="pdf-library"> <i class="fas fa-file-pdf mr-2"></i> PDF Library</button>
          <button class="nav-btn text-left p-3 rounded-lg section-card glass text-white" data-section="resources"> <i class="fas fa-book mr-2"></i> Resources</button>
          <button class="nav-btn text-left p-3 rounded-lg section-card glass text-white" data-section="monitoring"> <i class="fas fa-chart-line mr-2"></i> Monitoring & Analytics</button>
        </nav>
      </div>

      <div class="text-xs opacity-80">Team Members</div>
      <div id="teamList" class="space-y-2">
        <!-- filled by JS -->
      </div>

      <div>
        <button id="logoutBtn" class="w-full bg-white/10 text-white py-2 rounded-lg">Logout</button>
      </div>
    </aside>

    <!-- MAIN -->
    <main class="flex-1 p-8 overflow-auto">
      <!-- Header -->
      <header class="mb-6 flex items-center justify-between">
        <div>
          <h2 class="text-3xl font-bold text-white">Security Chaos Engineering Dashboard</h2>
          <p class="text-sm text-white/80 mt-1">Group 1 — Real-time collaboration</p>
        </div>
        <div class="flex items-center gap-3">
          <div class="text-white/80">Signed in as <span id="signedUser">Guest</span></div>
        </div>
      </header>

      <!-- SECTIONS -->
      <section id="overview" class="section active section-card glass p-6 rounded-xl mb-6">
        <div class="flex justify-between items-center mb-4">
          <h3 class="text-xl font-semibold text-white">Overview</h3>
          <div class="flex gap-2">
            <button class="btn-add-content px-4 py-2 rounded-lg bg-emerald-500 text-white">Add Content</button>
            <button id="openPdfFromOverview" class="px-4 py-2 rounded-lg bg-red-600 text-white"><i class="fas fa-file-pdf mr-2"></i> Upload PDF</button>
          </div>
        </div>
        <div id="overviewContent" class="space-y-4 text-white/90">
          <div class="p-4 bg-black/30 rounded-lg">Welcome — content loaded from Firebase.</div>
        </div>
      </section>

      <section id="project-documentation" class="section section-card glass p-6 rounded-xl mb-6 hidden">
        <div class="flex justify-between items-center mb-4">
          <h3 class="text-xl font-semibold text-white">Project Documentation</h3>
          <button class="btn-add-content px-4 py-2 rounded-lg bg-emerald-500 text-white">Add Content</button>
        </div>
        <div id="docContent" class="space-y-4 text-white/90"></div>
      </section>

      <section id="team-collaboration" class="section section-card glass p-6 rounded-xl mb-6 hidden">
        <div class="flex justify-between items-center mb-4">
          <h3 class="text-xl font-semibold text-white">Team Collaboration</h3>
          <button class="btn-add-content px-4 py-2 rounded-lg bg-emerald-500 text-white">Add Content</button>
        </div>
        <div id="teamCollabContent" class="space-y-4 text-white/90"></div>
      </section>

      <section id="team-updates" class="section section-card glass p-6 rounded-xl mb-6 hidden">
        <div class="flex justify-between items-center mb-4">
          <h3 class="text-xl font-semibold text-white">Team Updates</h3>
          <button class="btn-add-content px-4 py-2 rounded-lg bg-emerald-500 text-white">Add Content</button>
        </div>
        <div id="teamUpdatesContent" class="space-y-4 text-white/90"></div>
      </section>

      <!-- Practical Tasks (List) -->
      <section id="practical-tasks" class="section section-card glass p-6 rounded-xl mb-6 hidden">
        <div class="flex justify-between items-center mb-4">
          <h3 class="text-xl font-semibold text-white">Practical Tasks</h3>
          <div class="flex gap-2">
            <button id="btnAddTask" class="px-4 py-2 rounded-lg bg-emerald-500 text-white">Add Task</button>
            <button id="btnUploadPdfPractical" class="px-4 py-2 rounded-lg bg-blue-600 text-white"><i class="fas fa-file-pdf mr-2"></i> Upload PDF</button>
          </div>
        </div>

        <div class="mb-4 text-white/90">Click a task to open its full page:</div>
        <div id="tasksList" class="grid grid-cols-1 md:grid-cols-2 gap-4">
          <!-- generated by JS -->
        </div>
      </section>

      <!-- Individual Task Pages (each hidden by default) -->
      <section id="task-01" class="section section-card glass p-6 rounded-xl mb-6 hidden">
        <div class="flex justify-between items-start mb-4">
          <h3 class="text-xl font-bold text-white">✅ TASK 01 – Debug a Broken Feature</h3>
          <div class="flex gap-2">
            <button class="btn-edit-task px-3 py-2 rounded-lg bg-yellow-500 text-white hidden">Edit</button>
            <button class="btn-delete-task px-3 py-2 rounded-lg bg-red-600 text-white hidden">Delete</button>
          </div>
        </div>
        <div class="task-content text-white/90">
          <!-- full detailed content (seeded) -->
          <pre id="task01Content" class="task-content pre text-sm bg-black/20 p-4 rounded-md"></pre>
        </div>
      </section>

      <section id="task-02" class="section section-card glass p-6 rounded-xl mb-6 hidden">
        <div class="flex justify-between items-start mb-4">
          <h3 class="text-xl font-bold text-white">✅ TASK 02 – Build a Mini Tool</h3>
          <div class="flex gap-2">
            <button class="btn-edit-task px-3 py-2 rounded-lg bg-yellow-500 text-white hidden">Edit</button>
            <button class="btn-delete-task px-3 py-2 rounded-lg bg-red-600 text-white hidden">Delete</button>
          </div>
        </div>
        <div class="task-content text-white/90">
          <pre id="task02Content" class="task-content pre text-sm bg-black/20 p-4 rounded-md"></pre>
        </div>
      </section>

      <section id="task-03" class="section section-card glass p-6 rounded-xl mb-6 hidden">
        <div class="flex justify-between items-start mb-4">
          <h3 class="text-xl font-bold text-white">✅ TASK 03 – Responsive Two-Column Web Component</h3>
          <div class="flex gap-2">
            <button class="btn-edit-task px-3 py-2 rounded-lg bg-yellow-500 text-white hidden">Edit</button>
            <button class="btn-delete-task px-3 py-2 rounded-lg bg-red-600 text-white hidden">Delete</button>
          </div>
        </div>
        <div class="task-content text-white/90">
          <pre id="task03Content" class="task-content pre text-sm bg-black/20 p-4 rounded-md"></pre>
        </div>
      </section>

      <section id="task-04" class="section section-card glass p-6 rounded-xl mb-6 hidden">
        <div class="flex justify-between items-start mb-4">
          <h3 class="text-xl font-bold text-white">✅ TASK 04 – Reverse Engineer a Result</h3>
          <div class="flex gap-2">
            <button class="btn-edit-task px-3 py-2 rounded-lg bg-yellow-500 text-white hidden">Edit</button>
            <button class="btn-delete-task px-3 py-2 rounded-lg bg-red-600 text-white hidden">Delete</button>
          </div>
        </div>
        <div class="task-content text-white/90">
          <pre id="task04Content" class="task-content pre text-sm bg-black/20 p-4 rounded-md"></pre>
        </div>
      </section>

      <section id="task-05" class="section section-card glass p-6 rounded-xl mb-6 hidden">
        <div class="flex justify-between items-start mb-4">
          <h3 class="text-xl font-bold text-white">✅ TASK 05 – XWiki LaTeX Math Rendering</h3>
          <div class="flex gap-2">
            <button class="btn-edit-task px-3 py-2 rounded-lg bg-yellow-500 text-white hidden">Edit</button>
            <button class="btn-delete-task px-3 py-2 rounded-lg bg-red-600 text-white hidden">Delete</button>
          </div>
        </div>
        <div class="task-content text-white/90">
          <pre id="task05Content" class="task-content pre text-sm bg-black/20 p-4 rounded-md"></pre>
        </div>
      </section>

      <!-- PDF Library -->
      <section id="pdf-library" class="section section-card glass p-6 rounded-xl mb-6 hidden">
        <div class="flex justify-between items-center mb-4">
          <h3 class="text-xl font-semibold text-white">PDF Library</h3>
          <div class="flex gap-2">
            <button id="btnUploadPdf" class="px-4 py-2 rounded-lg bg-blue-600 text-white"><i class="fas fa-upload mr-2"></i>Upload PDF</button>
            <button class="btn-add-content px-4 py-2 rounded-lg bg-emerald-500 text-white">Add Metadata</button>
          </div>
        </div>
        <div id="pdfContent" class="space-y-4"></div>
      </section>

      <!-- Resources & other sections placeholders -->
      <section id="resources" class="section section-card glass p-6 rounded-xl mb-6 hidden">
        <div class="flex justify-between items-center mb-4">
          <h3 class="text-xl font-semibold text-white">Resources</h3>
          <button class="btn-add-content px-4 py-2 rounded-lg bg-emerald-500 text-white">Add Content</button>
        </div>
        <div id="resourcesContent"></div>
      </section>

      <section id="monitoring" class="section section-card glass p-6 rounded-xl mb-6 hidden">
        <h3 class="text-xl font-semibold text-white mb-4">Monitoring & Analytics</h3>
        <div id="monitoringContent"></div>
      </section>

    </main>
  </div>

  <!-- MODALS -->
  <!-- Login modal (simple local demo) -->
  <div id="loginModal" class="fixed inset-0 flex items-center justify-center z-50" style="display:none">
    <div class="bg-white p-6 rounded-xl w-[520px] shadow-xl">
      <h3 class="text-lg font-bold mb-3">Login (Demo)</h3>
      <form id="loginForm">
        <label class="block mb-2 text-sm">Email</label>
        <input id="loginEmail" class="w-full p-2 mb-3 border rounded" required />
        <label class="block mb-2 text-sm">Password</label>
        <input id="loginPassword" type="password" class="w-full p-2 mb-3 border rounded" required />
        <div class="flex gap-2 justify-end">
          <button type="button" id="loginCancel" class="px-4 py-2 rounded bg-gray-200">Cancel</button>
          <button type="submit" class="px-4 py-2 rounded bg-emerald-500 text-white">Login</button>
        </div>
      </form>
    </div>
  </div>

  <!-- Add content modal -->
  <div id="addContentModal" class="fixed inset-0 hidden items-center justify-center z-50">
    <div class="bg-white p-6 rounded-xl w-[720px] shadow-xl">
      <h3 class="text-lg font-bold mb-3">Add Content</h3>
      <form id="addContentForm">
        <input id="contentSectionInput" type="hidden" />
        <label class="block text-sm">Title</label>
        <input id="contentTitleInput" class="w-full p-2 border rounded mb-2" required />
        <label class="block text-sm">Content / Description</label>
        <textarea id="contentDescInput" class="w-full p-2 border rounded mb-2" rows="6" required></textarea>
        <label class="block text-sm">Author</label>
        <input id="contentAuthorInput" class="w-full p-2 border rounded mb-3" required />
        <div class="flex justify-end gap-2">
          <button type="button" id="cancelAddContent" class="px-4 py-2 rounded bg-gray-200">Cancel</button>
          <button type="submit" class="px-4 py-2 rounded bg-emerald-500 text-white">Save</button>
        </div>
      </form>
    </div>
  </div>

  <!-- PDF upload modal -->
  <div id="pdfModal" class="fixed inset-0 hidden items-center justify-center z-50">
    <div class="bg-white p-6 rounded-xl w-[640px] shadow-xl">
      <h3 class="text-lg font-bold mb-3">Upload PDF</h3>
      <form id="pdfForm">
        <label class="block text-sm">Title</label>
        <input id="pdfTitleInput" class="w-full p-2 border rounded mb-2" required />
        <label class="block text-sm">Description</label>
        <textarea id="pdfDescInput" class="w-full p-2 border rounded mb-2" rows="3" required></textarea>
        <label class="block text-sm">Choose PDF</label>
        <input id="pdfFileInput" type="file" accept="application/pdf" class="w-full mb-3" required />
        <div class="flex justify-end gap-2">
          <button type="button" id="cancelPdf" class="px-4 py-2 rounded bg-gray-200">Cancel</button>
          <button type="submit" class="px-4 py-2 rounded bg-blue-600 text-white">Upload</button>
        </div>
        <div id="pdfProgress" class="mt-2 text-sm"></div>
      </form>
    </div>
  </div>

<script>
/* ========== Configuration (Firebase) ========== */
const firebaseConfig = {
  apiKey: "AIzaSyDJsZ4LZVrBucavpTdhXbKxyE_BFeZFFKs",
  authDomain: "fir-console-df3e9.firebaseapp.com",
  databaseURL: "https://fir-console-df3e9-default-rtdb.europe-west1.firebasedatabase.app",
  projectId: "fir-console-df3e9",
  storageBucket: "fir-console-df3e9.firebasestorage.app",
  messagingSenderId: "750795336412",
  appId: "1:750795336412:web:abfd0c06941a9418abe219"
};
try {
  if (!firebase.apps.length) firebase.initializeApp(firebaseConfig);
} catch(e){ console.warn(e); }
const db = firebase.database();
const storage = firebase.storage();

/* ========== Helper DOM ========= */
const $ = id => document.getElementById(id);
const navBtns = document.querySelectorAll('.nav-btn');
const sections = document.querySelectorAll('.section');

/* ========== Simple Demo Auth (localStorage) ========= */
const demoUsers = [
  {name:"Kaled Osman", email:"kaled@team.com", password:"kaled123", role:"Developer", avatar:"K"},
  {name:"Marcus Tibell", email:"marcus@team.com", password:"marcus123", role:"Engineer", avatar:"M"},
  {name:"Jens Annell", email:"jens@team.com", password:"jens123", role:"Analyst", avatar:"J"},
  {name:"Fahad Hussain", email:"fahad@team.com", password:"fahad123", role:"Researcher", avatar:"F"},
  {name:"Luwam", email:"luwam@team.com", password:"luwam123", role:"Designer", avatar:"L"},
  {name:"Stefan Österberg", email:"stefan@team.com", password:"stefan123", role:"Architect", avatar:"S"},
  {name:"Najmaddin", email:"najmaddin@team.com", password:"najmaddin123", role:"Security Expert", avatar:"N"}
];

function saveUsersToLocal(){
  if(!localStorage.getItem('teamUsers')) localStorage.setItem('teamUsers', JSON.stringify(demoUsers));
}
saveUsersToLocal();

function currentUser(){
  return JSON.parse(localStorage.getItem('currentUser') || 'null');
}
function setCurrentUser(u){
  localStorage.setItem('currentUser', JSON.stringify(u));
  refreshUIForUser();
}

/* ========== UI Initialization ========== */
function refreshUIForUser(){
  const u = currentUser();
  $('signedUser').textContent = u ? (u.name + ' • ' + (u.email || '')) : 'Guest';
  $('userName').textContent = u ? u.name : 'You';
  $('userAvatar').textContent = u ? (u.avatar || u.name.charAt(0)) : 'Y';
  $('userRole').textContent = u ? u.role : 'Active User';
  // admin panel rendering is handled server-side in earlier version; here just allow actions based on user
}
refreshUIForUser();

/* ========== Sidebar team list ========== */
function renderTeam(){
  const list = $('teamList');
  list.innerHTML = '';
  const users = JSON.parse(localStorage.getItem('teamUsers') || '[]');
  users.forEach(u=>{
    const div = document.createElement('div');
    div.className = 'flex items-center gap-3 bg-white/10 p-3 rounded';
    div.innerHTML = `<div class="w-8 h-8 rounded-full bg-slate-700 flex items-center justify-center font-bold">${u.avatar}</div><div class="text-sm">${u.name}<div class="text-xs opacity-80">${u.role}</div></div>`;
    list.appendChild(div);
  });
}
renderTeam();

/* ========== Navigation behavior ========== */
navBtns.forEach(b=>{
  b.addEventListener('click', ()=>{
    navBtns.forEach(x=>x.classList.remove('active'));
    b.classList.add('active');
    const id = b.dataset.section;
    sections.forEach(s=> s.id===id ? s.classList.remove('hidden') : s.classList.add('hidden'));
    // show chosen section
    document.querySelectorAll('.section').forEach(sec=>{
      if(sec.id===id) sec.classList.remove('hidden'); else sec.classList.add('hidden');
    });
  });
});
// default show overview
document.querySelectorAll('.section').forEach(s => { if(s.id!=='overview') s.classList.add('hidden'); });

/* ========== Chat modal (simple) ========= */
$('openChatBtn').addEventListener('click', ()=>{
  alert('Team chat opens (use Firebase console / chat in app).'); // keep simple for demo
});

/* ========== Add content modal handling ========== */
document.querySelectorAll('.btn-add-content').forEach(btn=>{
  btn.addEventListener('click', ()=>{
    $('contentSectionInput').value = (btn.closest('.section') || $('overview')).id;
    $('contentTitleInput').value = '';
    $('contentDescInput').value = '';
    const u = currentUser();
    $('contentAuthorInput').value = u ? u.name : 'Anonymous';
    $('addContentModal').style.display = 'flex';
  });
});
$('cancelAddContent').addEventListener('click', ()=> $('addContentModal').style.display = 'none');

$('addContentForm').addEventListener('submit', async function(e){
  e.preventDefault();
  const section = $('contentSectionInput').value || 'overview';
  const title = $('contentTitleInput').value.trim();
  const desc = $('contentDescInput').value.trim();
  const author = $('contentAuthorInput').value.trim() || (currentUser()? currentUser().name : 'Anonymous');
  if(!title||!desc) return alert('Fill title & content');
  const payload = { title, description: desc, author, date: new Date().toISOString() };
  try{
    await db.ref(`content/${section}`).push(payload);
    $('addContentModal').style.display = 'none';
    alert('Saved');
  }catch(err){ console.error(err); alert('Save failed'); }
});

/* ========== PDF modal handling ========== */
const openPdfBtns = [ 'btnUploadPdf', 'btnUploadPdfPractical', 'openPdfFromOverview' ];
openPdfBtns.forEach(id=>{
  const el = $(id);
  if(el) el.addEventListener('click', ()=> { $('pdfModal').style.display = 'flex'; });
});
$('cancelPdf').addEventListener('click', ()=> $('pdfModal').style.display = 'none');

$('pdfForm').addEventListener('submit', function(e){
  e.preventDefault();
  const title = $('pdfTitleInput').value.trim();
  const desc = $('pdfDescInput').value.trim();
  const f = $('pdfFileInput').files[0];
  if(!title||!desc||!f) return alert('All fields');
  if(f.type !== 'application/pdf') return alert('Please choose a PDF file');
  // upload to Firebase Storage
  const ts = Date.now();
  const path = `pdfs/${ts}_${f.name}`;
  const up = storage.ref(path).put(f);
  $('pdfProgress').textContent = 'Uploading 0%';
  up.on('state_changed', snap=>{
    const pct = Math.round((snap.bytesTransferred/snap.totalBytes)*100);
    $('pdfProgress').textContent = `Uploading ${pct}%`;
  }, err=>{
    console.error(err); alert('Upload failed');
  }, async ()=>{
    const url = await up.snapshot.ref.getDownloadURL();
    const meta = { title, description: desc, url, storagePath: path, date: new Date().toISOString(), author: (currentUser()||{}).name || 'Anonymous' };
    await db.ref('pdfs').push(meta);
    $('pdfModal').style.display = 'none';
    $('pdfProgress').textContent = '';
    alert('PDF uploaded');
  });
});

/* ========== Content rendering + permission logic ========== */
function escapeHtml(s=''){ return String(s).replaceAll('&','&amp;').replaceAll('<','&lt;').replaceAll('>','&gt;'); }

function renderSectionFromSnapshot(section, snapshot){
  const containerId = {
    'overview':'overviewContent',
    'project-documentation':'docContent',
    'team-collaboration':'teamCollabContent',
    'team-updates':'teamUpdatesContent',
    'practical-tasks':'tasksList',
    'resources':'resourcesContent'
  }[section] || null;
  if(!containerId) return;
  const cont = $(containerId);
  cont.innerHTML = '';
  if(!snapshot || !snapshot.exists()){
    cont.innerHTML = `<div class="p-4 bg-black/20 rounded text-white/80">No content yet</div>`;
    return;
  }
  // For practical-tasks we render cards that link to task pages. Other sections render full cards.
  snapshot.forEach(child=>{
    const id = child.key;
    const item = child.val();
    if(section === 'practical-tasks'){
      const card = document.createElement('div');
      card.className = 'p-4 bg-black/20 rounded text-white';
      card.innerHTML = `<div class="font-semibold">${escapeHtml(item.title)}</div>
        <div class="text-sm opacity-80 mt-1">${escapeHtml(item.description.substring(0,220))}...</div>
        <div class="flex justify-between items-center mt-3">
          <div class="text-xs opacity-70">${escapeHtml(item.author)} • ${new Date(item.date).toLocaleDateString()}</div>
          <div class="flex gap-2">
            <button class="open-task btn-open" data-id="${id}">Open</button>
            <!-- author-specific edit/delete shown in full page -->
          </div>
        </div>`;
      cont.appendChild(card);
      // open task binds
      card.querySelector('.open-task').addEventListener('click', ()=> openTaskFull(id, item));
    } else {
      const card = document.createElement('div');
      card.className = 'p-4 bg-black/20 rounded text-white';
      card.innerHTML = `<div class="flex justify-between"><div><div class="font-semibold">${escapeHtml(item.title)}</div><div class="text-xs opacity-80">${escapeHtml(item.author)}</div></div>
      <div class="text-xs opacity-70">${new Date(item.date).toLocaleDateString()}</div></div>
      <div class="mt-3 text-sm opacity-85">${escapeHtml(item.description)}</div>
      <div class="mt-3 flex gap-2">
        <button class="btn-edit small" data-id="${id}" data-section="${section}">Edit</button>
        <button class="btn-del small" data-id="${id}" data-section="${section}">Delete</button>
      </div>`;
      cont.appendChild(card);

      // attach edit/delete with permission checks
      const editBtn = card.querySelector('.btn-edit');
      const delBtn = card.querySelector('.btn-del');
      editBtn.addEventListener('click', ()=> openEditContent(section, id));
      delBtn.addEventListener('click', ()=> attemptDelete(section, id, item));
      // only show edit/delete if current user is author or admin
      const cur = currentUser();
      if(!(cur && (cur.name === item.author || cur.email==='kaled@team.com'))){
        editBtn.style.display='none'; delBtn.style.display='none';
      }
    }
  });
}

/* load all sections */
const trackedSections = ['overview','project-documentation','team-collaboration','team-updates','practical-tasks','resources'];
trackedSections.forEach(sec=>{
  const ref = db.ref(`content/${sec}`);
  ref.off();
  ref.on('value', snap => renderSectionFromSnapshot(sec, snap));
});

/* ========== PDFs rendering ========= */
function renderPDFs(snap){
  const c = $('pdfContent');
  c.innerHTML = '';
  if(!snap || !snap.exists()){
    c.innerHTML = `<div class="p-4 bg-black/20 rounded text-white/80">No PDFs yet</div>`;
    return;
  }
  snap.forEach(child=>{
    const id = child.key; const item = child.val();
    const div = document.createElement('div');
    div.className = 'p-3 bg-black/20 rounded flex justify-between items-start text-white';
    div.innerHTML = `<div><div class="font-semibold">${escapeHtml(item.title)}</div><div class="text-sm opacity-80">${escapeHtml(item.description)}</div><div class="text-xs opacity-60 mt-2">${escapeHtml(item.author)} • ${new Date(item.date).toLocaleDateString()}</div></div>
      <div class="flex flex-col gap-2">
        <a href="${item.url}" target="_blank" class="px-3 py-1 rounded bg-blue-600 text-white text-sm"><i class="fas fa-eye mr-1"></i>Open</a>
        <button class="del-pdf px-3 py-1 rounded bg-red-600 text-white text-sm">Delete</button>
      </div>`;
    c.appendChild(div);
    const del = div.querySelector('.del-pdf');
    del.addEventListener('click', async ()=>{
      const cur = currentUser();
      if(!cur || cur.email !== 'kaled@team.com'){ alert('Only admin (kaled) can delete PDFs'); return; }
      if(!confirm('Delete this PDF?')) return;
      // delete from storage and db
      try{
        if(item.storagePath) await storage.ref(item.storagePath).delete();
      }catch(err){ console.warn('storage delete failed',err); }
      await db.ref(`pdfs/${id}`).remove();
    });
  });
}
db.ref('pdfs').on('value', snap => renderPDFs(snap));

/* ========== Task full pages logic (openTaskFull) ========== */
async function openTaskFull(taskId, taskItemHint){
  // find full data by searching content/practical-tasks for the key
  const snap = await db.ref('content/practical-tasks').once('value');
  if(!snap.exists()) return alert('Task not found');
  const itemSnap = snap.child(taskId);
  if(!itemSnap.exists()) return alert('Task removed');
  const item = itemSnap.val();
  // fill the appropriate task page by mapping title -> id (we will use title to select)
  const title = (item.title || '').toLowerCase();
  // choose which task page to show based on title keywords
  const mapping = { 'debug':'task-01', 'mini tool':'task-02','responsive':'task-03','reverse':'task-04','xwiki':'task-05' };
  let dest = 'task-01';
  if(title.includes('debug')) dest='task-01';
  else if(title.includes('mini')||title.includes('weather')) dest='task-02';
  else if(title.includes('responsive')||title.includes('web component')) dest='task-03';
  else if(title.includes('reverse')||title.includes('reverse engineer')) dest='task-04';
  else if(title.includes('xwiki')||title.includes('latex')) dest='task-05';

  // show only selected page
  document.querySelectorAll('.section').forEach(s => s.classList.add('hidden'));
  const page = $(dest);
  if(!page) return alert('Page missing');
  page.classList.remove('hidden');
  // fill content into corresponding pre element
  const pre = page.querySelector('pre');
  if(pre) pre.textContent = item.description + '\n\nAuthor: ' + item.author;
  // show edit/delete buttons if current user is author or admin
  const cur = currentUser();
  const deleteBtn = page.querySelector('.btn-delete-task');
  const editBtn = page.querySelector('.btn-edit-task');
  if(cur && (cur.name === item.author || cur.email === 'kaled@team.com')){
    deleteBtn.style.display = 'inline-block'; editBtn.style.display = 'inline-block';
    // bind actions
    deleteBtn.onclick = async ()=>{
      if(!confirm('Delete this task?')) return;
      await db.ref(`content/practical-tasks/${taskId}`).remove();
      alert('Deleted');
      // go back to practical tasks list
      document.querySelectorAll('.section').forEach(s => s.classList.add('hidden'));
      $('practical-tasks').classList.remove('hidden');
    };
    editBtn.onclick = ()=>{
      // open add content modal for editing (we'll load values and then update)
      $('contentSectionInput').value = 'practical-tasks';
      $('contentTitleInput').value = item.title;
      $('contentDescInput').value = item.description;
      $('contentAuthorInput').value = item.author;
      $('addContentModal').style.display = 'flex';
      // on submit will push a new item; for real edit we need id - implement simple flow:
      // store edit id
      $('addContentForm').onsubmit = async function(e){
        e.preventDefault();
        const newTitle = $('contentTitleInput').value.trim();
        const newDesc = $('contentDescInput').value.trim();
        const newAuthor = $('contentAuthorInput').value.trim();
        if(!newTitle||!newDesc) return alert('Fill fields');
        await db.ref(`content/practical-tasks/${taskId}`).update({ title:newTitle, description:newDesc, author:newAuthor, date:new Date().toISOString() });
        $('addContentModal').style.display = 'none';
        // restore handler
        location.reload();
      };
    };
  } else {
    deleteBtn.style.display='none'; editBtn.style.display='none';
  }
}

/* ========== Edit content for generic sections ========= */
async function openEditContent(section, id){
  const snap = await db.ref(`content/${section}/${id}`).once('value');
  if(!snap.exists()) return alert('Not found');
  const item = snap.val();
  $('contentSectionInput').value = section;
  $('contentTitleInput').value = item.title;
  $('contentDescInput').value = item.description;
  $('contentAuthorInput').value = item.author;
  $('addContentModal').style.display = 'flex';
  // override submit to update that id
  $('addContentForm').onsubmit = async function(e){
    e.preventDefault();
    const title = $('contentTitleInput').value.trim();
    const desc = $('contentDescInput').value.trim();
    const author = $('contentAuthorInput').value.trim();
    if(!title||!desc) return alert('Fill fields');
    await db.ref(`content/${section}/${id}`).update({ title, description: desc, author, date: new Date().toISOString() });
    $('addContentModal').style.display = 'none';
    location.reload();
  };
}

/* Delete attempt with permission check */
async function attemptDelete(section, id, item){
  const cur = currentUser();
  if(!(cur && (cur.name===item.author || cur.email==='kaled@team.com'))){ alert('You can only delete your own items'); return; }
  if(!confirm('Delete this content?')) return;
  await db.ref(`content/${section}/${id}`).remove();
  alert('Deleted');
}

/* ========== Seed default practical tasks (if empty) ========== */
async function seedTasks(){
  const ref = db.ref('content/practical-tasks');
  const snap = await ref.once('value');
  if(snap.exists()) return;
  const team = 'Marcus, Jens, Fahad, Stefan, Kaled';
  const tasks = [
    {
      title: 'Task 01 - Debug a Broken Feature',
      description: `Group 1 – Report

1. What Was the Task?

The goal was to simulate a real debugging situation.
One team member provides a broken code snippet, and the team must:

Identify the bug

Explain why it happens

Fix the bug

Reflect on what was learned

---

2. What Was the Broken Feature?
The provided code had logic issues and type errors that caused unexpected output.
Examples of common errors we analyzed:

Using wrong variable types
Incorrect comparison or calculation
Missing return values
Unhandled edge cases

The team identified the problematic line, tested variations, and confirmed the exact root cause.

---

3. How We Solved It
The debugging process followed these steps:

1. Reproduce the error
Run the code and observe the failing behavior.

2. Use print/log statements
Track variable states and isolate where the error occurs.

3. Check logic & data types
For example: "5" + 5 → causes unexpected concatenation.

4. Apply the fix
Correct the faulty line, adjust types, or fix the condition.

5. Test again
Confirm the code now produces correct output.

---

4. What We Learned
How to analyze errors systematically
How to replicate bugs to understand them
How small mistakes (e.g., wrong type) can break entire features
How debugging in a team is faster than debugging alone
Importance of clear communication

---

5. Summary
Task 01 helped Group 1 practice real-world debugging and develop a stronger technical workflow.
The team successfully identified the root cause, fixed the error, and documented the solution.`,
      author: team,
      date: new Date().toISOString()
    },
    {
      title: 'Task 02 - Build a Mini Tool (Weather App)',
      description: `Group 1 – Report

1. What Was the Task?
Build a small, functional tool such as a weather fetcher, email sender, converter, or any simple interactive utility.

2. What We Built
Our mini project focused on understanding external APIs and simple UI logic:
Fetching data (like weather, JSON, stats)
Parsing the response
Displaying it to the user
Handling errors (e.g., invalid input or connection problems)
We built a simple tool that retrieved information from an API endpoint and displayed it neatly.

3. Technical Highlights
We learned:
How to call an API using fetch()
Difference between JSON structure and local variables
Error handling using try/catch
How to structure input and output logic cleanly
How to test tools in the browser console

4. What We Learned
Real-world API usage
How to design small but functional programs
Importance of clean UI and readable output`,
      author: team,
      date: new Date().toISOString()
    },
    {
      title: 'Task 03 - Responsive Two-Column Web Component',
      description: `Group 1 – Report

1. Purpose of the Task
Build a responsive web component that automatically changes layout based on screen size:
Two columns on desktop
One column on mobile

2. What We Implemented
We built a reusable component that:
Shows content side-by-side on large screens
Stacks the content vertically on small screens
Adjusts spacing, padding, and alignment
Works inside any website page

3. Key Technical Concepts We Used
display: grid or flex
grid-template-columns
@media rules
Gap and spacing techniques

4. What We Learned
How to design components that adapt to any device
Why responsiveness is essential in modern web apps`,
      author: team,
      date: new Date().toISOString()
    },
    {
      title: 'Task 04 - Reverse Engineer a Result',
      description: `Group 1 – Report

1. What Was the Task?
A team member provides only the output, and the rest of the team must:
Guess what code produced that output
Rebuild a similar program
Explain the logic behind it

2. Output Example
We were given outputs similar to JSON structures. We worked to identify keys, arrays, and patterns.

3. How We Solved It
We worked backwards by identifying keys and patterns then rebuilt a script that generates similar structured output.

4. What We Learned
How to analyze output and reconstruct the logic behind it
How serialization works
Why reverse engineering is used in testing and debugging`,
      author: team,
      date: new Date().toISOString()
    },
    {
      title: 'Task 05 - XWiki LaTeX Math Rendering',
      description: `Group 1 – Report

1. Background
XWiki uses Markdown + MathJax.
Problem: Markdown processes _ and * before LaTeX, causing math breakage.

2. Problem Explanation
The Markdown engine does not protect $...$ blocks.

3. Proposed Solution
Implement a three-step rendering pipeline:
Step 1 — Math Preprocessor (replace math blocks with placeholders)
Step 2 — Markdown Engine (process page)
Step 3 — Math Postprocessor (reinsert math blocks and run MathJax)

4. Why This Works
No need to modify core; math is protected; works for inline and block math.`,
      author: team,
      date: new Date().toISOString()
    }
  ];
  for(const t of tasks) await db.ref('content/practical-tasks').push(t);
}
seedTasks().catch(console.error);

/* ========== PDFs & content snapshots already have listeners above ========== */

/* ========== Simple login modal logic ========== */
document.addEventListener('click', (ev)=>{
  // open login modal when clicking signedUser or avatar
  if(ev.target.id==='userAvatar' || ev.target.id==='signedUser'){ $('loginModal').style.display='flex'; }
});
$('loginForm').addEventListener('submit', function(e){
  e.preventDefault();
  const email = $('loginEmail').value.trim();
  const pass = $('loginPassword').value.trim();
  const users = JSON.parse(localStorage.getItem('teamUsers')||'[]');
  const found = users.find(u => u.email===email && u.password===pass);
  if(!found) return alert('Invalid credentials');
  setCurrentUser(found);
  $('loginModal').style.display='none';
  location.reload();
});
$('loginCancel').addEventListener('click', ()=> $('loginModal').style.display='none');

/* ========== Populate tasks list (listen) ========== */
db.ref('content/practical-tasks').on('value', snap=>{
  const cont = $('tasksList');
  cont.innerHTML = '';
  if(!snap || !snap.exists()){ cont.innerHTML = '<div class="p-4 bg-black/20 rounded text-white/80">No tasks yet</div>'; return; }
  snap.forEach(child=>{
    const id = child.key, it = child.val();
    const card = document.createElement('div');
    card.className = 'p-4 bg-black/20 rounded text-white';
    card.innerHTML = `<div class="font-semibold">${escapeHtml(it.title)}</div><div class="text-sm opacity-80 mt-1">${escapeHtml(it.author)}</div>
      <div class="mt-3 text-xs opacity-70">${new Date(it.date).toLocaleDateString()}</div>
      <div class="mt-3 flex gap-2"><button class="openBtn px-3 py-1 rounded bg-blue-600">Open</button></div>`;
    cont.appendChild(card);
    card.querySelector('.openBtn').addEventListener('click', ()=> openTaskFull(id, it));
  });
});

/* ========== Render generic content sections initially ========== */
trackedSections.forEach(sec=>{
  db.ref(`content/${sec}`).once('value').then(snap => renderSectionFromSnapshot(sec, snap));
});
db.ref('pdfs').once('value').then(snap=> renderPDFs(snap));

/* Final UI refresh */
refreshUIForUser();

</script>
</body>
</html>
