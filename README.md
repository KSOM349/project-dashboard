<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Security Chaos Engineering Dashboard</title>

<!-- Tailwind + Icons -->
<script src="https://cdn.tailwindcss.com"></script>
<link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css"/>

<!-- Firebase -->
<script src="https://www.gstatic.com/firebasejs/9.22.1/firebase-app-compat.js"></script>
<script src="https://www.gstatic.com/firebasejs/9.22.1/firebase-database-compat.js"></script>

<style>
html,body{margin:0;height:100%;font-family:Inter,Arial}
.bg-hero{
  position:fixed;inset:0;z-index:-10;
  background:url('bg.jpg') center/cover no-repeat;
  filter:brightness(.55);
}
.card{background:rgba(255,255,255,.12);backdrop-filter:blur(10px);border-radius:16px}
.active{background:#2563eb;color:white}
</style>
</head>

<body class="text-white">

<!-- BACKGROUND -->
<div class="bg-hero"></div>

<!-- LOGIN -->
<div id="login" class="fixed inset-0 bg-black/60 flex items-center justify-center">
  <form class="card p-8 w-96" onsubmit="loginUser(event)">
    <h2 class="text-2xl mb-4 font-bold text-center">Login</h2>
    <input id="email" class="w-full p-2 mb-3 rounded text-black" placeholder="Email" required>
    <input id="password" type="password" class="w-full p-2 mb-4 rounded text-black" placeholder="Password" required>
    <button class="w-full bg-blue-600 p-2 rounded">Login</button>
    <p class="text-sm mt-3 text-center opacity-80">kaled@team.com / kaled123</p>
  </form>
</div>

<!-- APP -->
<div id="app" class="hidden flex min-h-screen">

<!-- SIDEBAR -->
<aside class="w-72 p-6 bg-black/50">
  <h1 class="text-xl font-bold mb-6">Security Chaos</h1>

  <div id="userBox" class="card p-4 mb-6">
    <div id="userName" class="font-bold"></div>
    <div id="userRole" class="text-sm opacity-80"></div>
  </div>

  <nav class="space-y-2">
    <button class="nav active w-full p-2 rounded" onclick="show('overview')">Overview</button>
    <button class="nav w-full p-2 rounded" onclick="show('docs')">Documentation</button>
    <button class="nav w-full p-2 rounded" onclick="show('tasks')">Practical Tasks</button>
    <button class="nav w-full p-2 rounded" onclick="show('collab')">Collaboration</button>
    <button class="nav w-full p-2 rounded" onclick="show('updates')">Updates</button>
    <button class="nav w-full p-2 rounded" onclick="show('ai')">AI Assistant</button>
    <button class="nav w-full p-2 rounded" onclick="show('impl')">Implementation</button>
    <button class="nav w-full p-2 rounded" onclick="show('research')">Research</button>
    <button class="nav w-full p-2 rounded" onclick="show('resources')">Resources</button>
    <button class="nav w-full p-2 rounded" onclick="show('security')">Security Testing</button>
    <button class="nav w-full p-2 rounded" onclick="show('monitor')">Monitoring</button>
  </nav>
</aside>

<!-- MAIN -->
<main class="flex-1 p-10">
  <h1 class="text-3xl font-bold mb-6">Security Chaos Engineering Dashboard</h1>

  <!-- 11 SECTIONS -->
  <section id="overview" class="section card p-6">Overview Content</section>
  <section id="docs" class="section card p-6 hidden">Documentation</section>
  <section id="tasks" class="section card p-6 hidden">Practical Tasks</section>
  <section id="collab" class="section card p-6 hidden">Team Collaboration</section>
  <section id="updates" class="section card p-6 hidden">Team Updates</section>
  <section id="ai" class="section card p-6 hidden">AI Assistant</section>
  <section id="impl" class="section card p-6 hidden">Implementation</section>
  <section id="research" class="section card p-6 hidden">Research</section>
  <section id="resources" class="section card p-6 hidden">Resources</section>
  <section id="security" class="section card p-6 hidden">Security Testing</section>
  <section id="monitor" class="section card p-6 hidden">Monitoring & Analytics</section>
</main>

</div>

<script>
/* USERS */
const users=[
 {name:"Kaled Osman",email:"kaled@team.com",password:"kaled123",role:"Admin"},
 {name:"Marcus Tibell",email:"marcus@team.com",password:"marcus123",role:"Engineer"},
 {name:"Jens Annell",email:"jens@team.com",password:"jens123",role:"Analyst"}
];

/* LOGIN */
function loginUser(e){
 e.preventDefault();
 const email=email.value,pass=password.value;
 const u=users.find(x=>x.email===email&&x.password===pass);
 if(!u)return alert("Wrong login");
 localStorage.user=JSON.stringify(u);
 init();
}

/* INIT */
function init(){
 const u=JSON.parse(localStorage.user||"null");
 if(!u)return;
 login.classList.add("hidden");
 app.classList.remove("hidden");
 userName.textContent=u.name;
 userRole.textContent=u.role;
}
init();

/* NAV */
function show(id){
 document.querySelectorAll(".section").forEach(s=>s.classList.add("hidden"));
 document.getElementById(id).classList.remove("hidden");
 document.querySelectorAll(".nav").forEach(b=>b.classList.remove("active"));
 event.target.classList.add("active");
}
</script>

</body>
</html>
