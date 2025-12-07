<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="utf-8" />
  <meta name="viewport" content="width=device-width,initial-scale=1" />
  <title>Security Chaos Engineering Dashboard — Realtime</title>

  <!-- TailwindCSS -->
  <script src="https://cdn.tailwindcss.com"></script>

  <!-- Icons -->
  <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">

  <!-- Styles -->
  <style>
    .theme-transition { transition: all 0.25s ease; }
    .active-section { background: linear-gradient(135deg, #3b82f6, #1d4ed8); color: white; }
    .online { color: #10B981; font-weight: bold; }
    .offline { color: #ef4444; font-weight: bold; }
    .modal { display:none; position:fixed; inset:0; background:rgba(0,0,0,0.5); z-index:1000; align-items:center; justify-content:center; }
    .modal.active { display:flex; }
    .section-content { display:none; }
    .section-content.active { display:block; }
  </style>
</head>

<body class="bg-gray-50 dark:bg-gray-900 text-gray-800 theme-transition">

<!-- LOGIN MODAL -->
<div id="loginModal" class="modal active">
  <div class="bg-white dark:bg-gray-800 p-8 rounded-2xl max-w-md w-full mx-4 shadow-xl">
    <h2 class="text-2xl font-bold text-center mb-6">Login to Dashboard</h2>
    <form id="loginForm" class="space-y-4">
      <div>
        <label class="text-sm font-medium">Choose User</label>
        <select id="userSelect" class="w-full p-3 border rounded-lg dark:bg-gray-700 dark:text-white">
          <option value="Kaled Osman">Kaled Osman</option>
          <option value="Marcus Tibell">Marcus Tibell</option>
          <option value="Jens Annell">Jens Annell</option>
          <option value="Fahad Hussain">Fahad Hussain</option>
          <option value="Luwam">Luwam</option>
          <option value="Stefan Österberg">Stefan Österberg</option>
          <option value="Najmaddin">Najmaddin</option>
        </select>
      </div>

      <button type="submit" class="w-full py-3 bg-blue-600 text-white rounded-lg hover:bg-blue-700">
        Login
      </button>
    </form>
  </div>
</div>

<!-- CHAT MODAL -->
<div id="chatModal" class="modal">
  <div class="bg-white dark:bg-gray-800 p-6 rounded-2xl w-[450px] max-w-[95vw] shadow-xl">
    <div class="flex justify-between items-center mb-4">
      <h2 class="text-xl font-bold">Team Chat</h2>
      <button id="closeChat" class="text-gray-400 hover:text-gray-600"><i class="fas fa-times"></i></button>
    </div>

    <div id="chatMessages" class="h-72 overflow-y-auto p-3 border rounded-lg bg-gray-100 dark:bg-gray-900">
      <p class="text-center text-gray-500 mt-10">No messages yet</p>
    </div>

    <div class="flex gap-2 mt-4">
      <input id="chatInput" class="flex-1 p-2 border rounded-lg dark:bg-gray-700 dark:text-white" placeholder="Write a message...">
      <button id="sendChat" class="px-4 py-2 bg-blue-600 text-white rounded-lg"><i class="fas fa-paper-plane"></i></button>
    </div>
  </div>
</div>

<!-- ADD CONTENT MODAL -->
<div id="addContentModal" class="modal">
  <div class="bg-white dark:bg-gray-800 p-8 rounded-2xl w-full max-w-xl shadow-xl">
    <h2 class="text-2xl font-bold mb-4 text-center">Add Content</h2>

    <form id="addContentForm" class="space-y-4">
      <input type="hidden" id="contentSection">

      <div>
        <label class="text-sm font-medium">Title</label>
        <input id="contentTitle" class="w-full p-3 border rounded-lg dark:bg-gray-700 dark:text-white" required>
      </div>

      <div>
        <label class="text-sm font-medium">Content</label>
        <textarea id="contentDescription" class="w-full p-3 border rounded-lg h-32 dark:bg-gray-700 dark:text-white" required></textarea>
      </div>

      <button type="submit" class="w-full py-3 bg-green-600 text-white rounded-lg hover:bg-green-700">
        Save Content
      </button>

      <button type="button" id="cancelAddContent" class="w-full py-3 mt-2 bg-gray-500 text-white rounded-lg hover:bg-gray-600">
        Cancel
      </button>
    </form>
  </div>
</div>

<!-- MAIN LAYOUT -->
<div class="flex min-h-screen">

  <!-- SIDEBAR -->
  <aside class="w-80 bg-gray-100 dark:bg-gray-800 p-6 shadow-lg">

    <h1 class="text-2xl font-bold text-blue-600 mb-1">Security Chaos Engineering</h1>
    <p class="text-gray-600 dark:text-gray-400 mb-6">Group 1 Dashboard</p>

    <!-- USER CARD -->
    <div class="p-4 bg-white dark:bg-gray-700 rounded-lg shadow mb-6">
      <p id="userName" class="font-bold text-lg">Not logged in</p>
      <p id="userRole" class="text-gray-500 dark:text-gray-300 text-sm">Select user to login</p>

      <button id="openChat" class="w-full py-2 bg-blue-600 text-white rounded-lg mt-3">
        <i class="fas fa-comments mr-2"></i>Chat
      </button>

      <button id="logoutBtn" class="w-full py-2 bg-red-600 text-white rounded-lg mt-2">
        Logout
      </button>
    </div>

    <!-- SEARCH -->
    <input id="searchInput" placeholder="Search content..." class="w-full p-3 border rounded-lg mb-6 dark:bg-gray-700 dark:text-white">

    <!-- TEAM LIST -->
    <div>
      <h3 class="font-bold mb-2">Team Members</h3>
      <div id="teamList" class="space-y-3"></div>
    </div>

    <!-- SECTIONS NAV -->
    <nav class="mt-6 space-y-2">
      <button class="section-btn active-section w-full text-left p-3 rounded-lg" data-section="overview">Overview</button>
      <button class="section-btn w-full text-left p-3 rounded-lg" data-section="project-documentation">Project Documentation</button>
      <button class="section-btn w-full text-left p-3 rounded-lg" data-section="team-collaboration">Team Collaboration</button>
      <button class="section-btn w-full text-left p-3 rounded-lg" data-section="team-updates">Team Updates</button>
      <button class="section-btn w-full text-left p-3 rounded-lg" data-section="ai-assistant">AI Assistant</button>
      <button class="section-btn w-full text-left p-3 rounded-lg" data-section="practical-tasks">Practical Tasks</button>
      <button class="section-btn w-full text-left p-3 rounded-lg" data-section="implementation">Implementation</button>
      <button class="section-btn w-full text-left p-3 rounded-lg" data-section="research">Research</button>
      <button class="section-btn w-full text-left p-3 rounded-lg" data-section="resources">Resources</button>
      <button class="section-btn w-full text-left p-3 rounded-lg" data-section="security-testing">Security Testing</button>
      <button class="section-btn w-full text-left p-3 rounded-lg" data-section="monitoring-analytics">Monitoring & Analytics</button>
    </nav>

    <!-- THEME TOGGLE -->
    <button id="toggleTheme" class="w-full mt-6 py-2 bg-gray-300 dark:bg-gray-700 rounded-lg">
      Toggle Theme
    </button>

    <!-- ADMIN PANEL -->
    <div class="mt-6 p-4 bg-white dark:bg-gray-700 rounded-lg shadow">
      <h3 class="font-bold mb-3">Admin Panel</h3>

      <button id="clearContentBtn" class="w-full py-2 bg-red-500 text-white rounded-lg mb-2">Delete All Content</button>
      <button id="clearChatBtn" class="w-full py-2 bg-red-500 text-white rounded-lg mb-2">Clear Chat</button>

      <h4 class="font-bold mt-3 mb-2">Block User</h4>
      <div id="blockUserList" class="space-y-2"></div>
    </div>

  </aside>
  <!-- Firebase SDK -->
<script src="https://www.gstatic.com/firebasejs/9.22.1/firebase-app-compat.js"></script>
<script src="https://www.gstatic.com/firebasejs/9.22.1/firebase-database-compat.js"></script>

<script>
/* -------------------------------------------
   Firebase Config (YOUR REAL VALUES)
--------------------------------------------*/
const firebaseConfig = {
  apiKey: "AIzaSyDJsZ4LZVrBucavpTdhXbKxyE_BFeZFFKs",
  authDomain: "fir-console-df3e9.firebaseapp.com",
  databaseURL: "https://fir-console-df3e9-default-rtdb.europe-west1.firebasedatabase.app",
  projectId: "fir-console-df3e9",
  storageBucket: "fir-console-df3e9.firebasestorage.app",
  messagingSenderId: "750795336412",
  appId: "1:750795336412:web:abfd0c06941a9418abe219"
};

// Initialize Firebase
firebase.initializeApp(firebaseConfig);
const db = firebase.database();

/* -------------------------------------------
   TEAM DATA
--------------------------------------------*/
const teamMembers = [
  { name: "Kaled Osman", role: "Developer", avatar: "K" },
  { name: "Marcus Tibell", role: "Engineer", avatar: "M" },
  { name: "Jens Annell", role: "Analyst", avatar: "J" },
  { name: "Fahad Hussain", role: "Researcher", avatar: "F" },
  { name: "Luwam", role: "Designer", avatar: "L" },
  { name: "Stefan Österberg", role: "Architect", avatar: "S" },
  { name: "Najmaddin", role: "Security Expert", avatar: "N" }
];

let currentUser = null;
let blockedUsers = {};

/* -------------------------------------------
   DOM SHORTCUT
--------------------------------------------*/
const $ = id => document.getElementById(id);

/* -------------------------------------------
   LOGIN
--------------------------------------------*/
$("loginForm").addEventListener("submit", e => {
  e.preventDefault();

  const name = $("userSelect").value;
  currentUser = teamMembers.find(x => x.name === name);

  $("userName").textContent = currentUser.name;
  $("userRole").textContent = currentUser.role;

  $("loginModal").classList.remove("active");

  setOnlineStatus(true);
  loadBlockedUsers();
  loadTeamPresence();
  startChatListener();
  startContentListeners();
});

/* -------------------------------------------
   LOGOUT
--------------------------------------------*/
$("logoutBtn").addEventListener("click", () => {
  if (!currentUser) return;

  setOnlineStatus(false);
  currentUser = null;

  $("userName").textContent = "Not logged in";
  $("userRole").textContent = "Select user to login";
  $("loginModal").classList.add("active");
});

/* -------------------------------------------
   ONLINE PRESENCE
--------------------------------------------*/
function setOnlineStatus(status) {
  if (!currentUser) return;

  db.ref("presence/" + currentUser.name).set({
    online: status,
    timestamp: Date.now()
  });
}

function loadTeamPresence() {
  const container = $("teamList");
  container.innerHTML = "";

  teamMembers.forEach(member => {
    const div = document.createElement("div");
    div.id = "presence-" + member.name.replace(/\s+/g, "");
    div.className = "flex justify-between items-center p-2 bg-white dark:bg-gray-700 rounded-lg";

    div.innerHTML = `
      <div>
        <p class="font-bold">${member.name}</p>
        <p class="text-sm text-gray-500">${member.role}</p>
      </div>
      <span class="status text-sm offline">offline</span>
    `;

    container.appendChild(div);
  });

  db.ref("presence").on("value", snap => {
    const data = snap.val() || {};
    Object.keys(data).forEach(user => {
      const id = "presence-" + user.replace(/\s+/g, "");
      const box = document.getElementById(id);
      if (!box) return;

      const statusEl = box.querySelector(".status");
      if (data[user].online) {
        statusEl.textContent = "online";
        statusEl.classList.remove("offline");
        statusEl.classList.add("online");
      } else {
        statusEl.textContent = "offline";
        statusEl.classList.remove("online");
        statusEl.classList.add("offline");
      }
    });
  });
}

/* -------------------------------------------
   BLOCK USER SYSTEM
--------------------------------------------*/
function loadBlockedUsers() {
  db.ref("blocked").on("value", snap => {
    blockedUsers = snap.val() || {};
    updateBlockListUI();
  });
}

function updateBlockListUI() {
  const container = $("blockUserList");
  container.innerHTML = "";

  teamMembers.forEach(user => {
    const isBlocked = blockedUsers[user.name] === true;

    const div = document.createElement("div");
    div.className = "flex justify-between items-center p-2 bg-gray-200 dark:bg-gray-600 rounded";

    div.innerHTML = `
      <span>${user.name}</span>
      <button class="px-3 py-1 text-white rounded ${isBlocked ? "bg-green-600" : "bg-red-600"}"
        onclick="toggleBlockUser('${user.name}')">
        ${isBlocked ? "Unblock" : "Block"}
      </button>
    `;

    container.appendChild(div);
  });
}

function toggleBlockUser(name) {
  const newState = !(blockedUsers[name] === true);
  db.ref("blocked/" + name).set(newState);
}

/* -------------------------------------------
   SEARCH
--------------------------------------------*/
$("searchInput").addEventListener("input", () => {
  const value = $("searchInput").value.toLowerCase();

  document.querySelectorAll(".content-card").forEach(card => {
    const text = card.innerText.toLowerCase();
    card.style.display = text.includes(value) ? "block" : "none";
  });
});

/* -------------------------------------------
   CHAT
--------------------------------------------*/
$("openChat").addEventListener("click", () => {
  $("chatModal").classList.add("active");
});

$("closeChat").addEventListener("click", () => {
  $("chatModal").classList.remove("active");
});

$("sendChat").addEventListener("click", sendChatMessage);
$("chatInput").addEventListener("keypress", e => {
  if (e.key === "Enter") sendChatMessage();
});

function sendChatMessage() {
  if (!currentUser) return;
  if (blockedUsers[currentUser.name]) return alert("You are blocked.");

  const text = $("chatInput").value.trim();
  if (!text) return;

  const msg = {
    user: currentUser.name,
    text,
    timestamp: Date.now()
  };

  db.ref("chat").push(msg);
  $("chatInput").value = "";
}

function startChatListener() {
  db.ref("chat").on("value", snap => {
    const box = $("chatMessages");
    box.innerHTML = "";

    snap.forEach(msgSnap => {
      const msg = msgSnap.val();

      const div = document.createElement("div");
      div.className = "content-card p-3 mb-2 rounded bg-gray-200 dark:bg-gray-700";

      div.innerHTML = `
        <p class="font-bold">${msg.user}</p>
        <p>${msg.text}</p>
        <small>${new Date(msg.timestamp).toLocaleString()}</small>
      `;

      box.appendChild(div);
    });

    box.scrollTop = box.scrollHeight;
  });
}

/* -------------------------------------------
   REALTIME CONTENT
--------------------------------------------*/
function startContentListeners() {
  const sections = [
    "overview","project-documentation","team-collaboration","team-updates",
    "ai-assistant","practical-tasks","implementation","research",
    "resources","security-testing","monitoring-analytics"
  ];

  sections.forEach(section => {
    db.ref("content/" + section).on("value", snap => {
      const container = document.getElementById(section);
      if (!container) return;

      const contentBox = container.querySelector(".space-y-4") || container;

      contentBox.innerHTML = `
        <button class="addBtn bg-green-600 text-white px-4 py-2 rounded" onclick="openAddContent('${section}')">
          + Add Content
        </button>
      `;

      snap.forEach(itemSnap => {
        const item = itemSnap.val();
        const key = itemSnap.key;

        const div = document.createElement("div");
        div.className = "content-card p-4 bg-white dark:bg-gray-800 rounded shadow border dark:border-gray-600";

        div.innerHTML = `
          <h3 class="font-bold text-xl">${item.title}</h3>
          <p class="mt-2">${item.description}</p>

          <div class="flex justify-between text-sm text-gray-500 mt-3">
            <span>${item.author}</span>
            <span>${new Date(item.date).toLocaleString()}</span>
          </div>

          <div class="mt-3 flex gap-2">
            <button onclick="editContent('${section}','${key}','${item.title}','${item.description.replace(/'/g, "\\'")}')" class="px-3 py-1 bg-blue-600 text-white rounded">Edit</button>
            <button onclick="deleteContent('${section}','${key}')" class="px-3 py-1 bg-red-600 text-white rounded">Delete</button>
          </div>
        `;

        contentBox.appendChild(div);
      });
    });
  });
}

/* ADD CONTENT */
function openAddContent(section) {
  $("contentSection").value = section;
  $("addContentModal").classList.add("active");
}

$("cancelAddContent").addEventListener("click", () => {
  $("addContentModal").classList.remove("active");
});

$("addContentForm").addEventListener("submit", e => {
  e.preventDefault();

  const section = $("contentSection").value;
  const title = $("contentTitle").value.trim();
  const desc = $("contentDescription").value.trim();

  if (!title || !desc) return;

  db.ref("content/" + section).push({
    title,
    description: desc,
    author: currentUser.name,
    date: Date.now()
  });

  $("addContentModal").classList.remove("active");
  $("contentTitle").value = "";
  $("contentDescription").value = "";
});

/* EDIT CONTENT */
function editContent(section, key, oldTitle, oldDesc) {
  const newTitle = prompt("New title:", oldTitle);
  const newDesc = prompt("New content:", oldDesc);

  if (newTitle && newDesc) {
    db.ref(`content/${section}/${key}`).update({
      title: newTitle,
      description: newDesc
    });
  }
}

/* DELETE CONTENT */
function deleteContent(section, key) {
  if (confirm("Delete this content?")) {
    db.ref(`content/${section}/${key}`).remove();
  }
}

/* -------------------------------------------
   ADMIN PANEL ACTIONS
--------------------------------------------*/
$("clearChatBtn").addEventListener("click", () => {
  if (!confirm("Clear all chat messages?")) return;
  db.ref("chat").remove();
});

$("clearContentBtn").addEventListener("click", () => {
  if (!confirm("Delete ALL content?")) return;
  db.ref("content").remove();
});

/* -------------------------------------------
   THEME
--------------------------------------------*/
$("toggleTheme").addEventListener("click", () => {
  document.documentElement.classList.toggle("dark");
});
</script>

</body>
</html>
