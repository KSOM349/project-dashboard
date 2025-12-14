<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0"/>
    <title>Security Chaos Engineering Dashboard</title>

    <!-- Tailwind + Icons -->
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <script src="https://cdn.tailwindcss.com"></script>

    <!-- Custom CSS -->
    <link rel="stylesheet" href="style.css">

</head>
<body class="bg-gray-900 text-white">

    <!-- Background -->
    <div id="bgHero" class="bg-hero"></div>

    <!-- Main Dashboard Container -->
    <div id="app"></div>

    <!-- Firebase -->
    <script src="https://www.gstatic.com/firebasejs/9.22.1/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.1/firebase-database-compat.js"></script>

    <!-- App Logic -->
    <script src="app.js"></script>

</body>
</html>
/* ===== FULLSCREEN BACKGROUND ===== */
.bg-hero {
    position: fixed;
    inset: 0;
    z-index: -10;
    background-image: url('bg.jpg');  /* ← ضع هنا الصورة */
    background-size: cover;
    background-position: center;
    filter: brightness(0.55) blur(1px);
}

/* Dashboard Layout Fixes */
.card {
    background: rgba(255,255,255,0.12);
    border-radius: 18px;
    padding: 24px;
    backdrop-filter: blur(10px);
}

.section-title {
    font-size: 28px;
    font-weight: 700;
}

/* Buttons */
.btn {
    padding: 12px 18px;
    background: #2563eb;
    border-radius: 10px;
    color: white;
}

.btn:hover {
    background: #1d4ed8;
}

/* Chat Bubble */
.chat-msg {
    padding: 10px;
    border-radius: 12px;
    margin-bottom: 8px;
}

.chat-me {
    background: #2563eb;
    margin-left: auto;
    color: white;
}

.chat-other {
    background: rgba(255,255,255,0.2);
}
/* ============ Firebase Init ============ */
const firebaseConfig = {
    apiKey: "AIzaSyDJsZ4LZVrBucavpTdhXbKxyE_BFeZFFKs",
    authDomain: "fir-console-df3e9.firebaseapp.com",
    databaseURL: "https://fir-console-df3e9-default-rtdb.europe-west1.firebasedatabase.app",
    projectId: "fir-console-df3e9",
    storageBucket: "fir-console-df3e9.appspot.com",
    messagingSenderId: "750795336412",
    appId: "1:750795336412:web:abfd0c06941a9418abe219"
};

firebase.initializeApp(firebaseConfig);
const db = firebase.database();

/* ============ Default Users (Login) ============ */
const defaultUsers = [
    { name: "Kaled Osman", email: "kaled@team.com", password: "kaled123" },
    { name: "Marcus Tibell", email: "marcus@team.com", password: "marcus123" },
    { name: "Jens Annell", email: "jens@team.com", password: "jens123" },
    { name: "Fahad Hussain", email: "fahad@team.com", password: "fahad123" },
    { name: "Luwam", email: "luwam@team.com", password: "luwam123" },
    { name: "Stefan Österberg", email: "stefan@team.com", password: "stefan123" },
    { name: "Najmaddin", email: "najmaddin@team.com", password: "najmaddin123" }
];

if (!localStorage.getItem("teamUsers")) {
    localStorage.setItem("teamUsers", JSON.stringify(defaultUsers));
}

/* ============ Render Dashboard ============ */
document.getElementById("app").innerHTML = `
    <div class="p-10 max-w-6xl mx-auto">

        <h1 class="text-4xl font-bold mb-4">Security Chaos Engineering Dashboard</h1>
        <p class="text-gray-300 mb-10">Group 1 — Real-time Collaboration Platform</p>

        <!-- Section: PDF Upload -->
        <div class="card mb-10">
            <div class="section-title mb-4">Uploaded PDF Files</div>
            <input type="file" id="pdfInput">
            <button class="btn mt-2" onclick="uploadPDF()">Upload PDF</button>
            <div id="pdfList" class="mt-4"></div>
        </div>

        <!-- Section: Practical Tasks -->
        <div class="card mb-10">
            <div class="section-title mb-4">Practical Tasks</div>
            <button class="btn" onclick="addTask()">Add Task</button>
            <div id="taskList" class="mt-4"></div>
        </div>

        <!-- Section: Team Chat -->
        <div class="card">
            <div class="section-title mb-4">Team Chat</div>
            <div id="chatMessages" class="h-60 overflow-y-auto mb-4"></div>
            <div class="flex gap-2">
                <input id="chatInput" class="flex-1 p-2 rounded bg-gray-800" placeholder="Type...">
                <button class="btn" onclick="sendMsg()">Send</button>
            </div>
        </div>

    </div>
`;

/* ============ Chat ============ */
function sendMsg() {
    const text = document.getElementById("chatInput").value.trim();
    if (!text) return;

    db.ref("chat").push({
        user: "You",
        text,
        time: Date.now()
    });

    document.getElementById("chatInput").value = "";
}

db.ref("chat").on("child_added", snap => {
    const m = snap.val();

    const div = document.createElement("div");
    div.className = `chat-msg ${m.user === "You" ? "chat-me" : "chat-other"}`;
    div.innerHTML = `<strong>${m.user}:</strong> ${m.text}`;

    document.getElementById("chatMessages").appendChild(div);
});

/* ============ Practical Tasks ============ */
function addTask() {
    const title = prompt("Enter Task Title:");
    if (!title) return;

    db.ref("tasks").push({
        title,
        date: new Date().toISOString()
    });
}

db.ref("tasks").on("value", snap => {
    const list = document.getElementById("taskList");
    list.innerHTML = "";

    snap.forEach(task => {
        const t = task.val();
        const div = document.createElement("div");
        div.className = "p-4 bg-gray-800 rounded mb-2";
        div.innerHTML = `
            <div class="font-bold">${t.title}</div>
            <div class="text-sm text-gray-400">${t.date}</div>
        `;
        list.appendChild(div);
    });
});

/* ============ PDF Upload ============ */
function uploadPDF() {
    const file = document.getElementById("pdfInput").files[0];
    if (!file) return alert("No file selected!");

    db.ref("pdfs").push({
        name: file.name,
        uploaded: new Date().toISOString()
    });

    alert("PDF saved (metadata only — GitHub Pages doesn't support file uploads).");
}

db.ref("pdfs").on("value", snap => {
    const list = document.getElementById("pdfList");
    list.innerHTML = "";

    snap.forEach(pdf => {
        const f = pdf.val();
        const div = document.createElement("div");
        div.className = "p-3 bg-gray-800 rounded mb-2";
        div.innerHTML = `${f.name} — <span class="text-gray-400">${f.uploaded}</span>`;
        list.appendChild(div);
    });
});
