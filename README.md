<!DOCTYPE html>
<html lang="sv">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Full-Stack Dashboard - Förbättrad</title>
<link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.0/css/all.min.css">
<style>
:root {
    --primary: #3498db;
    --secondary: #2ecc71;
    --success: #27ae60;
    --warning: #f39c12;
    --accent: #e74c3c;
    --light: #f5f5f5;
}

body { font-family: Arial, sans-serif; margin:0; padding:0; background:#fafafa; }
.section { padding:2rem; display:none; }
.section.active { display:block; }
.section-header { display:flex; justify-content:space-between; align-items:center; margin-bottom:1rem; }
.section-header h2 { margin:0; }
.btn { cursor:pointer; padding:0.5rem 1rem; border:none; border-radius:5px; font-size:0.9rem; }
.stats-container { display:flex; gap:1rem; flex-wrap:wrap; margin-bottom:1rem; }
.stat-card { background:white; padding:1rem; border-radius:8px; flex:1; min-width:120px; text-align:center; box-shadow:0 2px 5px rgba(0,0,0,0.1); }
.stat-number { font-size:1.5rem; font-weight:bold; }
.task-card { background:white; padding:1rem; border-radius:10px; margin-bottom:1rem; border-left:4px solid var(--accent); box-shadow:0 2px 5px rgba(0,0,0,0.05); }
.task-header { display:flex; justify-content:space-between; align-items:center; margin-bottom:0.5rem; }
.task-title { font-weight:bold; }
.progress-bar { background:#ddd; height:10px; border-radius:5px; overflow:hidden; margin-top:0.5rem; }
.progress-fill { background:var(--success); width:0%; height:100%; transition:0.5s; }
.notification { position:fixed; top:10px; right:10px; padding:1rem; background:var(--success); color:white; border-radius:6px; margin-bottom:0.5rem; display:flex; justify-content:space-between; align-items:center; min-width:200px; box-shadow:0 2px 5px rgba(0,0,0,0.2); }
.notification.info { background:#3498db; }
.notification.warning { background:#f39c12; }
.notification .notification-close { background:none; border:none; color:white; font-size:1rem; cursor:pointer; margin-left:0.5rem; }
.ai-chat-container { max-height:300px; overflow-y:auto; background:#f0f0f0; padding:1rem; border-radius:8px; margin-bottom:1rem; }
.ai-message, .user-message { margin-bottom:0.5rem; padding:0.5rem 1rem; border-radius:8px; }
.ai-message { background:#3498db; color:white; }
.user-message { background:#ecf0f1; color:#333; text-align:right; }
.chat-input { display:flex; gap:0.5rem; }
.nav-tab { cursor:pointer; padding:0.5rem 1rem; border-radius:5px; background:#ddd; margin-right:0.5rem; display:inline-block; }
.nav-tab.active { background:var(--primary); color:white; }
</style>
</head>
<body>

<div id="notificationContainer"></div>

<!-- Navigation -->
<div style="padding:1rem; background:#eee;">
    <span class="nav-tab active" data-target="hela-projektet">Hela Projektet</span>
    <span class="nav-tab" data-target="dijkstra-algorithm">Dijkstra</span>
    <span class="nav-tab" data-target="dokumentation">Dokumentation</span>
    <span class="nav-tab" data-target="team-collaboration">Team</span>
    <span class="nav-tab" data-target="team-updates">Updates</span>
    <span class="nav-tab" data-target="practical-tasks">Tasks</span>
    <span class="nav-tab" data-target="ai-assistant">AI</span>
</div>

<!-- HELA PROJEKTET -->
<section id="hela-projektet" class="section active">
    <div class="section-header">
        <h2>📁 HELA PROJEKTET</h2>
        <button class="btn" style="background: var(--primary); color:white;" onclick="showNotification('Dokumentation exporterad!', 'success')">
            <i class="fas fa-download"></i> Export
        </button>
    </div>
    <div style="background:var(--primary); color:white; padding:1rem; border-radius:8px; margin-bottom:1rem;">
        <h3>🚀 Sammanfattning</h3>
        <p>Grupp 1 - Full-Stack Dashboard med Algorithm Visualizer</p>
    </div>
    <h4>Projektmål</h4>
    <ul>
        <li>CRUD Dashboard</li>
        <li>Algorithm Visualizer (Dijkstra)</li>
        <li>Full-Stack Utveckling</li>
    </ul>
    <h4>Teknisk Stack</h4>
    <ul>
        <li>Backend: Python Flask</li>
        <li>Frontend: HTML/CSS/JS</li>
        <li>Data: JSON</li>
    </ul>
</section>

<!-- DIJKSTRA -->
<section id="dijkstra-algorithm" class="section">
    <div class="section-header">
        <h2>🔍 Dijkstra Algorithm - Steg för Steg</h2>
    </div>
    <div class="stats-container">
        <div class="stat-card"><div class="stat-number">5</div><div class="stat-label">Servrar</div></div>
        <div class="stat-card"><div class="stat-number">13</div><div class="stat-label">Kortaste Väg (ms)</div></div>
        <div class="stat-card"><div class="stat-number">100%</div><div class="stat-label">Optimering</div></div>
    </div>
    <pre>// Server nätverk
servers = {
'WebServer': {'Database':5,'Cache':2},
'Database': {'Backup':8,'WebServer':5},
'Cache': {'CDN':3,'WebServer':2},
'CDN': {'Cache':3},
'Backup': {'Database':8}
}</pre>
    <div style="background: var(--success); color:white; padding:1rem; border-radius:8px; margin-top:1rem;">
        <p>Start: WebServer (0)</p>
        <p>Steg 1: Grannar → Database=5, Cache=2</p>
        <p>Steg 2: Välj Cache → hitta CDN=5</p>
        <p>Steg 3: Välj Database → hitta Backup=13</p>
        <strong>Resultat: Kortaste vägar funna ✅</strong>
    </div>
</section>

<!-- Dokumentation -->
<section id="dokumentation" class="section">
    <div class="section-header">
        <h2>📚 Dokumentation</h2>
    </div>
    <h4>Av Grupp 1</h4>
    <p>Fahad, Stefan, Kaled, Marcus, Jens, Luwam</p>
    <p>Dijkstra används för kortaste väg i nätverk, spel och design.</p>
</section>

<!-- Team Collaboration -->
<section id="team-collaboration" class="section">
    <div class="section-header"><h2>📝 Team Collaboration</h2></div>
    <div style="display:grid; grid-template-columns:1fr 1fr; gap:1rem;">
        <div style="background:var(--light); padding:1rem; border-radius:8px;">
            <h4>➕ Lägg till uppgift</h4>
            <form id="assignment-form">
                <input type="text" id="task-name" placeholder="Uppgiftsnamn" style="width:100%; padding:0.5rem; margin-bottom:0.5rem;">
                <textarea id="task-desc" placeholder="Beskrivning" style="width:100%; padding:0.5rem; margin-bottom:0.5rem;"></textarea>
                <select id="task-assignee" style="width:100%; padding:0.5rem; margin-bottom:0.5rem;">
                    <option value="Kaled Osman">Kaled Osman</option>
                    <option value="Fahad Hussain">Fahad Hussain</option>
                </select>
                <button class="btn" style="background:var(--success); color:white; width:100%;">➕ Lägg till</button>
            </form>
        </div>
        <div style="background:var(--light); padding:1rem; border-radius:8px;">
            <h4>📋 Aktuella uppgifter</h4>
            <div id="assignments-list"></div>
        </div>
    </div>
</section>

<!-- Team Updates -->
<section id="team-updates" class="section">
    <div class="section-header"><h2>👥 Team Updates</h2></div>
    <div style="background:var(--light); padding:1rem; border-radius:8px;">
        <form id="team-update-form">
            <input type="text" id="update-title" placeholder="Titel" style="width:100%; padding:0.5rem; margin-bottom:0.5rem;">
            <textarea id="update-details" placeholder="Detaljer" style="width:100%; padding:0.5rem; margin-bottom:0.5rem;"></textarea>
            <select id="update-status" style="width:100%; padding:0.5rem; margin-bottom:0.5rem;">
                <option value="completed">✅ Avslutad</option>
                <option value="in-progress">🔄 Pågående</option>
                <option value="planned">📅 Planerad</option>
                <option value="blocked">❌ Blockerad</option>
            </select>
            <button class="btn" style="background:var(--success); color:white; width:100%;">➕ Publicera</button>
        </form>
    </div>
    <div id="team-updates-list"></div>
</section>

<!-- Practical Tasks -->
<section id="practical-tasks" class="section">
    <div class="section-header"><h2>🎯 Tasks</h2>
        <button class="btn" style="background:var(--success); color:white;" onclick="addNewTask()">➕ Ny Uppgift</button>
    </div>
    <div class="stats-container">
        <div class="stat-card"><div class="stat-number" id="total-tasks">0</div><div class="stat-label">Totalt</div></div>
        <div class="stat-card"><div class="stat-number" id="completed-tasks">0</div><div class="stat-label">Avslutade</div></div>
        <div class="stat-card"><div class="stat-number" id="inprogress-tasks">0</div><div class="stat-label">Pågående</div></div>
        <div class="stat-card"><div class="stat-number" id="pending-tasks">0</div><div class="stat-label">Väntande</div></div>
    </div>
    <div id="tasks-list"></div>
</section>

<!-- AI Assistant -->
<section id="ai-assistant" class="section">
    <div class="section-header"><h2>🤖 AI Assistant</h2></div>
    <div class="ai-chat-container" id="aiChatContainer"></div>
    <div class="chat-input">
        <input type="text" id="aiChatInput" placeholder="Fråga AI...">
        <button class="btn" style="background:var(--secondary); color:white;" onclick="sendAIMessage()"><i class="fas fa-paper-plane"></i> Skicka</button>
    </div>
</section>

<script>
// Notifications
function showNotification(msg,type='success'){
    const container=document.getElementById('notificationContainer');
    const note=document.createElement('div');
    note.className='notification '+type;
    note.innerHTML=`<span>${msg}</span><button class="notification-close" onclick="this.parentElement.remove()"><i class="fas fa-times"></i></button>`;
    container.appendChild(note);
    setTimeout(()=>note.remove(),5000);
}

// Navigation
document.querySelectorAll('.nav-tab').forEach(tab=>{
    tab.addEventListener('click',()=>{
        document.querySelectorAll('.nav-tab,.section').forEach(el=>el.classList.remove('active'));
        tab.classList.add('active');
        document.getElementById(tab.dataset.target).classList.add('active');
    });
});

// Tasks
function addNewTask(){
    const name=prompt('Uppgiftsnamn:');
    if(!name)return;
    const list=document.getElementById('tasks-list');
    const task=document.createElement('div');
    task.className='task-card';
    task.innerHTML=`<div class="task-header"><div class="task-title">${name}</div><span class="task-priority priority-medium">Medel</span></div>
        <div class="task-meta"><span><i class="fas fa-user"></i> Kaled Osman</span><span><i class="fas fa-calendar"></i> ${new Date().toISOString().split('T')[0]}</span></div>
        <p>Ny uppgift</p><div class="progress-bar"><div class="progress-fill" style="width:0%"></div></div>
        <div style="display:flex; gap:10px; margin-top:10px;">
        <button class="btn complete-btn" style="background:#27ae60;color:white;"><i class="fas fa-check"></i> Klar</button>
        <button class="btn edit-btn" style="background:#f39c12;color:white;"><i class="fas fa-edit"></i> Redigera</button>
        <button class="btn delete-btn" style="background:#e74c3c;color:white;"><i class="fas fa-trash"></i> Radera</button></div>`;
    list.appendChild(task);
    addTaskEventListeners(task);
    updateTaskStats();
}
function addTaskEventListeners(t){
    t.querySelector('.complete-btn').addEventListener('click',()=>{t.querySelector('.progress-fill').style.width='100%';showNotification('Uppgift klar!','success');updateTaskStats();});
    t.querySelector('.edit-btn').addEventListener('click',()=>{const newT=prompt('Ny titel:',t.querySelector('.task-title').textContent);if(newT)t.querySelector('.task-title').textContent=newT;});
    t.querySelector('.delete-btn').addEventListener('click',()=>{if(confirm('Radera uppgift?')){t.remove();updateTaskStats();}});
}
function updateTaskStats(){
    const tasks=document.querySelectorAll('.task-card');
    const total=tasks.length;
    const completed=[...tasks].filter(t=>t.querySelector('.progress-fill').style.width==='100%').length;
    const inprogress=[...tasks].filter(t=>{const w=t.querySelector('.progress-fill').style.width;return w!=='100%'&&w!=='0%';}).length;
    const pending=total-completed-inprogress;
    document.getElementById('total-tasks').textContent=total;
    document.getElementById('completed-tasks').textContent=completed;
    document.getElementById('inprogress-tasks').textContent=inprogress;
    document.getElementById('pending-tasks').textContent=pending;
}

// AI
function sendAIMessage(){
    const input=document.getElementById('aiChatInput'); const msg=input.value.trim(); if(!msg)return;
    const chat=document.getElementById('aiChatContainer');
    const userMsg=document.createElement('div'); userMsg.className='user-message'; userMsg.innerHTML=`<strong>👤 Du:</strong> ${msg}`; chat.appendChild(userMsg);
    setTimeout(()=>{ const ai=document.createElement('div'); ai.className='ai-message'; ai.innerHTML=`<strong>🤖 AI:</strong> ${getAIResponse(msg)}`; chat.appendChild(ai); chat.scrollTop=chat.scrollHeight;},500);
    input.value=''; chat.scrollTop=chat.scrollHeight;
}
function getAIResponse(q){q=q.toLowerCase(); if(q.includes('tidskomplexitet'))return 'O(V²) matris eller O(E+V log V) med prioritetskö.'; if(q.includes('python'))return 'Använd heapq i Python.'; if(q.includes('nätverk'))return 'OSPF använder Dijkstra för kortaste väg.'; if(q.includes('skillnad'))return 'Dijkstra för kortaste väg, A* använder heuristik.'; return 'Jag kan hjälpa dig med Dijkstra, uppgifter och praktiska tips!';}

document.addEventListener('DOMContentLoaded',()=>{showNotification('Välkommen!','info');});

</script>
</body>
</html>
// ===================== Mohammed Feedback =====================
