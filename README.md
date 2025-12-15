<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8" />
<meta name="viewport" content="width=device-width, initial-scale=1.0"/>
<title>Security Chaos Engineering Dashboard</title>

<!-- Tailwind + Icons -->
<script src="https://cdn.tailwindcss.com"></script>
<link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.0/css/all.min.css"/>

<!-- Firebase -->
<script src="https://www.gstatic.com/firebasejs/9.22.1/firebase-app-compat.js"></script>
<script src="https://www.gstatic.com/firebasejs/9.22.1/firebase-database-compat.js"></script>

<style>
/* ===== BACKGROUND (100% WORKING) ===== */
body::before {
  content: "";
  position: fixed;
  inset: 0;
  background: url("bg.jpg") center/cover no-repeat;
  filter: brightness(0.55);
  z-index: -1;
}

/* ===== UI ===== */
.card {
  background: rgba(255,255,255,0.12);
  backdrop-filter: blur(10px);
  border-radius: 18px;
  padding: 24px;
}
.section-title {
  font-size: 26px;
  font-weight: 700;
}
.btn {
  background: #2563eb;
  padding: 10px 16px;
  border-radius: 10px;
}
.btn:hover { background:#1d4ed8; }
.chat-me { background:#2563eb; color:white; margin-left:auto; }
.chat-other { background:rgba(255,255,255,.2); }
.chat-msg { padding:10px; border-radius:12px; margin-bottom:8px; max-width:70%; }
</style>
</head>

<body class="text-white">

<div class="max-w-7xl mx-auto p-10 space-y-10">

<!-- HEADER -->
<div>
  <h1 class="text-4xl font-bold">Security Chaos Engineering Dashboard</h1>
  <p class="text-gray-300">Group 1 – Real-time Collaboration Platform</p>
</div>

<!-- TEAM INFO -->
<div class="card grid grid-cols-2 md:grid-cols-4 gap-6 text-center">
  <div><b>Group 1</b><br>Marcus • Jens • Fahad • Stefan • Kaled • Luwam</div>
  <div><b>6</b><br>Team Members</div>
  <div><b>100%</b><br>Progress</div>
  <div><b>Chat</b><br>Realtime</div>
</div>

<!-- PRACTICAL TASKS -->
<div class="card">
  <div class="section-title mb-4">Practical Tasks</div>
  <button class="btn mb-4" onclick="addTask()">+ Add Task</button>
  <div id="tasks"></div>
</div>

<!-- TEAM UPDATES -->
<div class="card">
  <div class="section-title mb-4">Team Updates</div>
  <div>Group 1 – Marcus • Jens • Fahad • Stefan • Kaled • Luwam</div>
</div>

<!-- DOCUMENTATION -->
<div class="card">
  <div class="section-title mb-4">Documentation</div>
  <div>Project Docs – Algorithm Visualizer – Dijkstra</div>
</div>

<!-- CHAT -->
<div class="card">
  <div class="section-title mb-4">Team Chat</div>
  <div id="chat" class="h-64 overflow-y-auto mb-4"></div>
  <div class="flex gap-2">
    <input id="msg" class="flex-1 p-2 rounded bg-gray-800" placeholder="Type message...">
    <button class="btn" onclick="sendMsg()">Send</button>
  </div>
</div>

</div>

<script>
/* ===== FIREBASE ===== */
firebase.initializeApp({
  apiKey: "AIzaSyDJsZ4LZVrBucavpTdhXbKxyE_BFeZFFKs",
  authDomain: "fir-console-df3e9.firebaseapp.com",
  databaseURL: "https://fir-console-df3e9-default-rtdb.europe-west1.firebasedatabase.app",
  projectId: "fir-console-df3e9",
});
const db = firebase.database();

/* ===== CHAT (FIXED) ===== */
function sendMsg(){
  const input = document.getElementById("msg");
  if(!input.value) return;
  db.ref("chat").push({
    user:"Group 1",
    text:input.value,
    time:Date.now()
  });
  input.value="";
}

db.ref("chat").on("child_added",snap=>{
  const m=snap.val();
  const div=document.createElement("div");
  div.className="chat-msg chat-other";
  div.innerHTML=`<b>${m.user}:</b> ${m.text}`;
  document.getElementById("chat").appendChild(div);
});

/* ===== TASKS ===== */
function addTask(){
  const t=prompt("Task name?");
  if(!t) return;
  db.ref("tasks").push({title:t,date:new Date().toLocaleString()});
}

db.ref("tasks").on("value",snap=>{
  const box=document.getElementById("tasks");
  box.innerHTML="";
  snap.forEach(s=>{
    const d=s.val();
    box.innerHTML+=`<div class="p-3 bg-gray-800 rounded mb-2">${d.title}<br><small>${d.date}</small></div>`;
  });
});
</script>

</body>
</html>
