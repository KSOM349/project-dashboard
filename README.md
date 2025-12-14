<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Security Chaos Engineering Dashboard</title>

<!-- Tailwind & Icons -->
<script src="https://cdn.tailwindcss.com"></script>
<link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.0/css/all.min.css">

<!-- Firebase -->
<script src="https://www.gstatic.com/firebasejs/9.22.1/firebase-app-compat.js"></script>
<script src="https://www.gstatic.com/firebasejs/9.22.1/firebase-database-compat.js"></script>

<style>
html,body{height:100%;margin:0;font-family:Inter,system-ui}
.bg-hero{
  position:fixed;inset:0;z-index:-10;
  background:url("assets/bg.jpg") center/cover no-repeat;
}
.bg-hero::after{
  content:"";position:absolute;inset:0;
  background:linear-gradient(180deg,rgba(5,10,30,.55),rgba(5,10,30,.75));
  backdrop-filter:blur(6px);
}
.section{display:none}
.section.active{display:block}
.card{
  background:rgba(255,255,255,.92);
  border-radius:16px;
  box-shadow:0 10px 30px rgba(0,0,0,.4)
}
</style>
</head>

<body class="text-gray-900">

<div class="bg-hero"></div>

<!-- LOGIN -->
<div id="loginModal" class="fixed inset-0 flex items-center justify-center bg-black/60 z-50">
  <div class="bg-white p-6 rounded-xl w-80">
    <h2 class="text-xl font-bold mb-4 text-center">Login</h2>
    <input id="email" placeholder="Email" class="w-full p-2 mb-2 border rounded">
    <input id="password" type="password" placeholder="Password" class="w-full p-2 mb-4 border rounded">
    <button onclick="login()" class="w-full bg-blue-600 text-white py-2 rounded">Login</button>
  </div>
</div>

<div class="flex min-h-screen">

<!-- SIDEBAR -->
<aside class="w-72 bg-black/50 text-white p-6 space-y-3">
  <h1 class="text-xl font-bold">Security Chaos</h1>
  <p class="text-sm opacity-80">Group 1 Dashboard</p>

  <nav class="space-y-1 mt-6">
    <button class="nav" data-sec="overview">Overview</button>
    <button class="nav" data-sec="project">Project Documentation</button>
    <button class="nav" data-sec="collaboration">Team Collaboration</button>
    <button class="nav" data-sec="updates">Team Updates</button>
    <button class="nav" data-sec="ai">AI Assistant</button>
    <button class="nav" data-sec="tasks">Practical Tasks</button>
    <button class="nav" data-sec="implementation">Implementation</button>
    <button class="nav" data-sec="research">Research</button>
    <button class="nav" data-sec="resources">Resources</button>
    <button class="nav" data-sec="security">Security Testing</button>
    <button class="nav" data-sec="monitoring">Monitoring & Analytics</button>
  </nav>
</aside>

<!-- CONTENT -->
<main class="flex-1 p-10 space-y-8 text-white">

<header>
  <h2 class="text-3xl font-bold">Security Chaos Engineering</h2>
  <p class="opacity-80">Real-time collaboration dashboard</p>
</header>

<!-- SECTIONS -->
<div id="overview" class="section active card p-6">
  <h3 class="text-2xl font-bold mb-4">Overview</h3>
  <p>This dashboard is used for group collaboration and task documentation.</p>
</div>

<div id="project" class="section card p-6"><h3 class="text-2xl font-bold">Project Documentation</h3></div>
<div id="collaboration" class="section card p-6"><h3 class="text-2xl font-bold">Team Collaboration</h3></div>
<div id="updates" class="section card p-6"><h3 class="text-2xl font-bold">Team Updates</h3></div>
<div id="ai" class="section card p-6"><h3 class="text-2xl font-bold">AI Assistant</h3></div>
<div id="tasks" class="section card p-6"><h3 class="text-2xl font-bold">Practical Tasks</h3></div>
<div id="implementation" class="section card p-6"><h3 class="text-2xl font-bold">Implementation</h3></div>
<div id="research" class="section card p-6"><h3 class="text-2xl font-bold">Research</h3></div>
<div id="resources" class="section card p-6"><h3 class="text-2xl font-bold">Resources</h3></div>
<div id="security" class="section card p-6"><h3 class="text-2xl font-bold">Security Testing</h3></div>
<div id="monitoring" class="section card p-6"><h3 class="text-2xl font-bold">Monitoring & Analytics</h3></div>

</main>
</div>

<script>
/* Firebase */
firebase.initializeApp({
  apiKey:"AIzaSyDJsZ4LZVrBucavpTdhXbKxyE_BFeZFFKs",
  databaseURL:"https://fir-console-df3e9-default-rtdb.europe-west1.firebasedatabase.app"
});

/* Login */
const users=[
 {email:"kaled@team.com",pass:"kaled123"},
 {email:"marcus@team.com",pass:"marcus123"},
 {email:"jens@team.com",pass:"jens123"},
 {email:"fahad@team.com",pass:"fahad123"},
 {email:"stefan@team.com",pass:"stefan123"}
];

function login(){
 const e=email.value,p=password.value;
 if(users.find(u=>u.email===e&&u.pass===p)){
  loginModal.style.display="none";
 }else alert("Wrong login");
}

/* Navigation */
document.querySelectorAll(".nav").forEach(b=>{
 b.onclick=()=>{
  document.querySelectorAll(".section").forEach(s=>s.classList.remove("active"));
  document.getElementById(b.dataset.sec).classList.add("active");
 }
});
</script>

</body>
</html>
