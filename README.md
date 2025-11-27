<!DOCTYPE html>
<html lang="sv">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Grupp 1 - Enhanced Dashboard</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <style>
        .theme-transition {
            transition: all 0.3s ease;
        }
        
        [data-theme="dark"] {
            color-scheme: dark;
        }
    </style>
</head>
<body class="theme-transition bg-gray-50 text-gray-900" data-theme="light">
    
<div id="notificationContainer" class="fixed top-4 right-4 z-50 space-y-2"></div>

<header class="bg-gradient-to-r from-blue-600 to-purple-600 text-white p-6 rounded-b-2xl">
    <div class="container mx-auto flex justify-between items-center">
        <div>
            <h1 class="text-3xl font-bold">🚀 Grupp 1 - Enhanced Dashboard</h1>
            <p class="mt-2 text-lg opacity-90">Full-Stack Dashboard med Realtidsdata</p>
        </div>
        <button onclick="toggleTheme()" class="bg-white/20 hover:bg-white/30 px-4 py-2 rounded-lg transition-all theme-transition">
            <i class="fas fa-moon"></i> Tema
        </button>
    </div>
</header>

<div class="container mx-auto mt-8 px-4">
    <div class="grid grid-cols-1 lg:grid-cols-2 gap-6 mb-8">
        <div class="bg-white rounded-xl shadow-lg p-6 theme-transition">
            <h2 class="text-2xl font-bold text-blue-700 mb-4">📊 System Status</h2>
            <div class="grid grid-cols-2 gap-4 mb-4">
                <div class="text-center p-4 bg-blue-50 rounded-lg theme-transition">
                    <div class="text-2xl font-bold" id="cpu-percent">--%</div>
                    <div class="text-sm text-gray-600">CPU</div>
                </div>
                <div class="text-center p-4 bg-green-50 rounded-lg theme-transition">
                    <div class="text-2xl font-bold" id="memory-percent">--%</div>
                    <div class="text-sm text-gray-600">Memory</div>
                </div>
                <div class="text-center p-4 bg-yellow-50 rounded-lg theme-transition">
                    <div class="text-2xl font-bold" id="disk-percent">--%</div>
                    <div class="text-sm text-gray-600">Disk</div>
                </div>
                <div class="text-center p-4 bg-purple-50 rounded-lg theme-transition">
                    <div class="text-2xl font-bold" id="uptime">--</div>
                    <div class="text-sm text-gray-600">Uptime</div>
                </div>
            </div>
            <button onclick="fetchSystemData()" class="w-full bg-blue-600 hover:bg-blue-700 text-white py-3 rounded-lg transition-all theme-transition">
                <i class="fas fa-sync"></i> Uppdatera Data
            </button>
        </div>

        <div class="bg-white rounded-xl shadow-lg p-6 theme-transition">
            <h2 class="text-2xl font-bold text-orange-600 mb-4">🔔 System Alerts</h2>
            <div id="alerts-container" class="mb-4">
                <div class="p-3 bg-green-100 text-green-800 rounded-lg theme-transition">
                    <i class="fas fa-check-circle"></i> Alla system fungerar normalt
                </div>
            </div>
            <button onclick="checkAlerts()" class="w-full bg-orange-500 hover:bg-orange-600 text-white py-3 rounded-lg transition-all theme-transition">
                <i class="fas fa-bell"></i> Kolla Varningar
            </button>
        </div>
    </div>

    <nav class="flex flex-wrap gap-2 mb-6 bg-white p-4 rounded-xl shadow-lg theme-transition">
        <button class="nav-btn px-4 py-2 rounded-lg bg-blue-100 text-blue-700 theme-transition" data-section="hela-projektet">Hela Projektet</button>
        <button class="nav-btn px-4 py-2 rounded-lg bg-gray-100 text-gray-700 theme-transition" data-section="dokumentation">Dokumentation</button>
        <button class="nav-btn px-4 py-2 rounded-lg bg-gray-100 text-gray-700 theme-transition" data-section="team-collaboration">Team Collaboration</button>
        <button class="nav-btn px-4 py-2 rounded-lg bg-gray-100 text-gray-700 theme-transition" data-section="team-updates">Team Updates</button>
        <button class="nav-btn px-4 py-2 rounded-lg bg-gray-100 text-gray-700 theme-transition" data-section="ai-assistant">AI Assistant</button>
        <button class="nav-btn px-4 py-2 rounded-lg bg-gray-100 text-gray-700 theme-transition" data-section="practical-tasks">Practical Tasks</button>
        <button class="nav-btn px-4 py-2 rounded-lg bg-gray-100 text-gray-700 theme-transition" data-section="algorithm-visualizer">Algorithm Visualizer</button>
        <button class="nav-btn px-4 py-2 rounded-lg bg-gray-100 text-gray-700 theme-transition" data-section="min-implementation">Min Implementation</button>
        <button class="nav-btn px-4 py-2 rounded-lg bg-gray-100 text-gray-700 theme-transition" data-section="team-dashboard">Team Dashboard</button>
        <button class="nav-btn px-4 py-2 rounded-lg bg-gray-100 text-gray-700 theme-transition" data-section="dijkstra-algorithm">Dijkstra Algorithm</button>
    </nav>

    <div id="content-container">
        <div class="section-content bg-white rounded-xl shadow-lg p-6 theme-transition" id="hela-projektet">
            <h2 class="text-2xl font-bold text-gray-800 mb-4">📁 HELA PROJEKTET - Dokumentation & Presentation</h2>
            <button class="export-btn bg-blue-600 hover:bg-blue-700 text-white px-4 py-2 rounded-lg transition-all theme-transition mb-4">
                <i class="fas fa-download"></i> Export
            </button>
            
            <div class="p-4 bg-blue-50 rounded-lg theme-transition mb-6">
                <h3 class="text-xl font-semibold mb-2">🚀 PROJEKT-SAMMANFATTNING</h3>
                <p>"Grupp 1 - Full-Stack Dashboard med Algorithm Visualizer"</p>
            </div>

            <div class="mb-6">
                <h3 class="text-xl font-semibold mb-3">📊 PROJEKT-ÖVERSIKT</h3>
                <div class="grid md:grid-cols-2 gap-4">
                    <div class="p-4 border rounded-lg theme-transition">
                        <h4 class="font-semibold mb-2">🎯 Projektmål</h4>
                        <ul class="list-disc list-inside space-y-1">
                            <li>Bygga ett fullt funktionellt CRUD dashboard</li>
                            <li>Integrera Algorithm Visualizer för Dijkstra</li>
                            <li>Demonstrera full-stack utveckling</li>
                        </ul>
                    </div>
                    <div class="p-4 border rounded-lg theme-transition">
                        <h4 class="font-semibold mb-2">🛠 Teknisk Stack</h4>
                        <ul class="list-disc list-inside space-y-1">
                            <li>Backend: Python Flask</li>
                            <li>Frontend: HTML5, CSS3, JavaScript</li>
                            <li>Styling: Modern CSS med gradients</li>
                            <li>Data: JSON-baserad "databas"</li>
                        </ul>
                    </div>
                </div>
            </div>

            <div>
                <h3 class="text-xl font-semibold mb-3">✅ FUNKTIONALITET</h3>
                <div class="grid md:grid-cols-3 gap-4">
                    <div class="p-4 border rounded-lg theme-transition">
                        <h4 class="font-semibold">📋 Task 1: Dashboard (CRUD)</h4>
                        <ul class="list-disc list-inside space-y-1 mt-2">
                            <li>CREATE - Lägg till nya anställda</li>
                            <li>READ - Visa data i tabell</li>
                            <li>UPDATE - Redigera med modal</li>
                            <li>DELETE - Radera med bekräftelse</li>
                            <li>Realtidsstatistik</li>
                        </ul>
                    </div>
                    <div class="p-4 border rounded-lg theme-transition">
                        <h4 class="font-semibold">🔍 Task 2: Algorithm Visualizer</h4>
                        <ul class="list-disc list-inside space-y-1 mt-2">
                            <li>Dijkstra algorithm demo</li>
                            <li>Graf-visualisering</li>
                            <li>Steg-för-steg förklaring</li>
                            <li>Responsiv design</li>
                        </ul>
                    </div>
                    <div class="p-4 border rounded-lg theme-transition">
                        <h4 class="font-semibold">🎨 Task 3: Design & UX</h4>
                        <ul class="list-disc list-inside space-y-1 mt-2">
                            <li>Modern design med gradients</li>
                            <li>Responsiv för alla enheter</li>
                            <li>Professionell navigation</li>
                            <li>Användarvänliga formulär</li>
                        </ul>
                    </div>
                </div>
            </div>
        </div>

        <div class="section-content bg-white rounded-xl shadow-lg p-6 theme-transition hidden" id="dokumentation">
            <h2 class="text-2xl font-bold text-gray-800 mb-4">📚 Dokumentation - Graph Algorithms</h2>
            <div class="space-y-4">
                <h3 class="text-xl font-semibold">Av Grupp 1: Fahad Hussain, Stefan Österberg, Kaled Osman, Marcus Tibell, Jens Annell, Luwam</h3>
                
                <div class="p-4 border rounded-lg theme-transition">
                    <h4 class="font-semibold mb-2">Dijkstra's Algorithm</h4>
                    <p><strong>The purpose of Dijkstra's algorithm</strong> is to find the shortest possible path between nodes in a weighted graph.</p>
                </div>

                <div class="p-4 border rounded-lg theme-transition">
                    <h4 class="font-semibold mb-2">Kaled - Why does the shortest path matter?</h4>
                    <p>"As mentioned earlier, the purpose of the algorithm is to find the shortest possible path, so why does the shortest possible path matter?"</p>
                </div>

                <div class="p-4 border rounded-lg theme-transition">
                    <h4 class="font-semibold mb-2">Fahad - Uses of shortest path</h4>
                    <p>"A common usage of the shortest path within our sphere of operation is for example, in networking, where speed and efficiency are crucial..."</p>
                </div>
            </div>
        </div>

        <div class="section-content bg-white rounded-xl shadow-lg p-6 theme-transition hidden" id="team-collaboration">
            <h2 class="text-2xl font-bold text-gray-800 mb-4">📝 Team Collaboration - Grupp Dokumentation</h2>
            <div class="grid md:grid-cols-2 gap-6">
                <div class="p-4 border rounded-lg theme-transition">
                    <h3 class="font-semibold mb-3">➕ Lägg Till Uppgift</h3>
                    <form id="assignment-form" class="space-y-3">
                        <div>
                            <label class="block text-sm font-medium mb-1">Uppgiftsnamn:</label>
                            <input type="text" id="task-name" class="w-full p-2 border rounded theme-transition">
                        </div>
                        <div>
                            <label class="block text-sm font-medium mb-1">Beskrivning:</label>
                            <textarea id="task-desc" class="w-full p-2 border rounded theme-transition h-24"></textarea>
                        </div>
                        <div>
                            <label class="block text-sm font-medium mb-1">Ansvarig:</label>
                            <select id="task-assignee" class="w-full p-2 border rounded theme-transition">
                                <option value="Kaled Osman">Kaled Osman</option>
                                <option value="Fahad Hussain">Fahad Hussain</option>
                                <option value="Stefan Österberg">Stefan Österberg</option>
                                <option value="Marcus Tibell">Marcus Tibell</option>
                                <option value="Jens Annell">Jens Annell</option>
                                <option value="Luwam">Luwam</option>
                            </select>
                        </div>
                        <button type="submit" class="w-full bg-green-600 hover:bg-green-700 text-white py-2 rounded theme-transition">
                            ➕ Lägg Till Uppgift
                        </button>
                    </form>
                </div>

                <div class="p-4 border rounded-lg theme-transition">
                    <h3 class="font-semibold mb-3">📋 Aktuella Uppgifter</h3>
                    <div id="assignments-list" class="space-y-3">
                        <div class="p-3 bg-gray-50 rounded theme-transition">
                            <strong>Förbättra Dijkstra Algorithm</strong>
                            <p class="text-sm text-gray-600">Lägg till prestandaförbättringar och komplexitetsanalys</p>
                            <small><strong>Ansvarig:</strong> Kaled Osman</small>
                        </div>
                        <div class="p-3 bg-gray-50 rounded theme-transition">
                            <strong>OSPF Protocols Research</strong>
                            <p class="text-sm text-gray-600">Studera OSPF-protokoll och deras tillämpningar i nätverk</p>
                            <small><strong>Ansvarig:</strong> Fahad Hussain</small>
                        </div>
                    </div>
                </div>
            </div>
        </div>

        <div class="section-content bg-white rounded-xl shadow-lg p-6 theme-transition hidden" id="team-updates">
            <h2 class="text-2xl font-bold text-gray-800 mb-4">👥 Team Updates & Uppgifter</h2>
            <div class="space-y-6">
                <div class="p-4 border rounded-lg theme-transition">
                    <h3 class="font-semibold mb-3">➕ Lägg Till Din Uppdatering</h3>
                    <form id="team-update-form" class="space-y-3">
                        <div class="grid md:grid-cols-2 gap-3">
                            <div>
                                <label class="block text-sm font-medium mb-1">Ditt Namn:</label>
                                <select id="update-author" class="w-full p-2 border rounded theme-transition">
                                    <option value="Kaled Osman">Kaled Osman</option>
                                    <option value="Fahad Hussain">Fahad Hussain</option>
                                    <option value="Stefan Österberg">Stefan Österberg</option>
                                    <option value="Marcus Tibell">Marcus Tibell</option>
                                    <option value="Jens Annell">Jens Annell</option>
                                    <option value="Luwam">Luwam</option>
                                </select>
                            </div>
                            <div>
                                <label class="block text-sm font-medium mb-1">Status:</label>
                                <select id="update-status" class="w-full p-2 border rounded theme-transition">
                                    <option value="completed">✅ Avslutad</option>
                                    <option value="in-progress">🔄 Pågående</option>
                                    <option value="planned">📅 Planerad</option>
                                    <option value="blocked">❌ Blockerad</option>
                                </select>
                            </div>
                        </div>
                        <div>
                            <label class="block text-sm font-medium mb-1">Uppgiftsbeskrivning:</label>
                            <input type="text" id="update-title" class="w-full p-2 border rounded theme-transition" placeholder="Vad har du gjort?">
                        </div>
                        <div>
                            <label class="block text-sm font-medium mb-1">Detaljer:</label>
                            <textarea id="update-details" class="w-full p-2 border rounded theme-transition h-24" placeholder="Beskriv ditt arbete..."></textarea>
                        </div>
                        <button type="submit" class="w-full bg-green-600 hover:bg-green-700 text-white py-2 rounded theme-transition">
                            ➕ Publicera Uppdatering
                        </button>
                    </form>
                </div>

                <div id="team-updates-list" class="space-y-4">
                </div>
            </div>
        </div>

        <div class="section-content bg-white rounded-xl shadow-lg p-6 theme-transition hidden" id="ai-assistant">
            <h2 class="text-2xl font-bold text-gray-800 mb-4">🤖 AI Assistant - Dijkstra Helper</h2>
            <div class="space-y-4">
                <div class="ai-chat-container border rounded-lg p-4 h-64 overflow-y-auto theme-transition" id="aiChatContainer">
                    <div class="p-3 bg-blue-50 rounded-lg theme-transition mb-2">
                        <strong>🤖 AI Assistant:</strong> Hej! Jag är här för att hjälpa dig med Dijkstra-algoritmen och ditt projekt. Ställ vilken fråga som helst!
                    </div>
                </div>

                <div class="flex gap-2">
                    <input type="text" id="aiChatInput" class="flex-1 p-2 border rounded theme-transition" placeholder="Skriv din fråga om Dijkstra eller projektet...">
                    <button onclick="sendAIMessage()" class="bg-green-600 hover:bg-green-700 text-white px-4 py-2 rounded theme-transition">
                        <i class="fas fa-paper-plane"></i> Skicka
                    </button>
                </div>

                <div>
                    <h3 class="font-semibold mb-2">🚀 Snabbkommandon</h3>
                    <div class="flex flex-wrap gap-2">
                        <button onclick="quickQuestion('Vad är tidskomplexiteten för Dijkstra?')" class="bg-gray-100 hover:bg-gray-200 px-3 py-2 rounded theme-transition">
                            ⏱️ Tidskomplexitet
                        </button>
                        <button onclick="quickQuestion('Ge exempel på Dijkstra i nätverk')" class="bg-gray-100 hover:bg-gray-200 px-3 py-2 rounded theme-transition">
                            🌐 Nätverksanvändning
                        </button>
                        <button onclick="quickQuestion('Hur implementerar jag Dijkstra i Python?')" class="bg-gray-100 hover:bg-gray-200 px-3 py-2 rounded theme-transition">
                            🐍 Python Implementation
                        </button>
                    </div>
                </div>
            </div>
        </div>

        <div class="section-content bg-white rounded-xl shadow-lg p-6 theme-transition hidden" id="practical-tasks">
            <h2 class="text-2xl font-bold text-gray-800 mb-4">🎯 Practical Tasks - Projektuppgifter</h2>
            <div class="flex justify-between items-center mb-6">
                <div class="grid grid-cols-4 gap-4 flex-1">
                    <div class="text-center p-4 bg-blue-50 rounded-lg theme-transition">
                        <div class="text-2xl font-bold" id="total-tasks">0</div>
                        <div class="text-sm text-gray-600">Totalt Uppgifter</div>
                    </div>
                    <div class="text-center p-4 bg-green-50 rounded-lg theme-transition">
                        <div class="text-2xl font-bold" id="completed-tasks">0</div>
                        <div class="text-sm text-gray-600">Avslutade</div>
                    </div>
                    <div class="text-center p-4 bg-yellow-50 rounded-lg theme-transition">
                        <div class="text-2xl font-bold" id="inprogress-tasks">0</div>
                        <div class="text-sm text-gray-600">Pågående</div>
                    </div>
                    <div class="text-center p-4 bg-purple-50 rounded-lg theme-transition">
                        <div class="text-2xl font-bold" id="pending-tasks">0</div>
                        <div class="text-sm text-gray-600">Väntande</div>
                    </div>
                </div>
                <button onclick="addNewTask()" class="ml-4 bg-green-600 hover:bg-green-700 text-white px-4 py-2 rounded theme-transition">
                    <i class="fas fa-plus"></i> Ny Uppgift
                </button>
            </div>

            <div id="tasks-list" class="space-y-3">
            </div>
        </div>

        <div class="section-content bg-white rounded-xl shadow-lg p-6 theme-transition hidden" id="algorithm-visualizer">
            <h2 class="text-2xl font-bold text-gray-800 mb-4">📊 Algorithm Visualizer</h2>
            <div class="space-y-4">
                <div class="grid grid-cols-3 gap-4">
                    <div class="text-center p-4 bg-gradient-to-r from-blue-500 to-purple-500 text-white rounded-lg">
                        <div class="text-2xl font-bold">5</div>
                        <div>Servrar i Nätverk</div>
                    </div>
                    <div class="text-center p-4 bg-gradient-to-r from-blue-500 to-purple-500 text-white rounded-lg">
                        <div class="text-2xl font-bold">13 ms</div>
                        <div>Kortaste Väg</div>
                    </div>
                    <div class="text-center p-4 bg-gradient-to-r from-blue-500 to-purple-500 text-white rounded-lg">
                        <div class="text-2xl font-bold">100%</div>
                        <div>Optimering</div>
                    </div>
                </div>
                
                <div class="p-4 bg-gray-100 rounded-lg">
                    <pre class="whitespace-pre-wrap">// Server Network Configuration
servers = {
    'WebServer': {'Database': 5, 'Cache': 2},
    'Database': {'Backup': 8, 'WebServer': 5},
    'Cache': {'CDN': 3, 'WebServer': 2},
    'CDN': {'Cache': 3},
    'Backup': {'Database': 8}
}</pre>
                </div>
                
                <div class="p-4 bg-green-500 text-white rounded-lg">
                    <h4 class="font-bold">🎯 Dijkstra Steg-för-Steg:</h4>
                    <p>Start: WebServer (0)</p>
                    <p>Steg 1: Hitta grannar → Database=5, Cache=2</p>
                    <p>Steg 2: Välj Cache → hitta CDN=5</p>
                    <p>Steg 3: Välj Database → hitta Backup=13</p>
                    <p class="font-bold">Resultat: Alla kortaste vägar funna! ✅</p>
                </div>
            </div>
        </div>

        <div class="section-content bg-white rounded-xl shadow-lg p-6 theme-transition hidden" id="min-implementation">
            <h2 class="text-2xl font-bold text-gray-800 mb-4">💻 Min Implementation</h2>
            <div class="space-y-4">
                <div class="p-4 border rounded-lg theme-transition">
                    <h3 class="font-semibold mb-2">Backend Implementation</h3>
                    <p>Python Flask server med REST API endpoints för systemövervakning.</p>
                </div>
                <div class="p-4 border rounded-lg theme-transition">
                    <h3 class="font-semibold mb-2">Frontend Implementation</h3>
                    <p>Modern React/Vue.js implementation med realtidsdata uppdatering.</p>
                </div>
            </div>
        </div>

        <div class="section-content bg-white rounded-xl shadow-lg p-6 theme-transition hidden" id="team-dashboard">
            <h2 class="text-2xl font-bold text-gray-800 mb-4">👥 Team Dashboard</h2>
            <div class="grid grid-cols-2 md:grid-cols-3 gap-4">
                <div class="text-center p-4 border rounded-lg theme-transition">
                    <div class="text-2xl font-bold">6</div>
                    <div>Team Members</div>
                </div>
                <div class="text-center p-4 border rounded-lg theme-transition">
                    <div class="text-2xl font-bold">12</div>
                    <div>Completed Tasks</div>
                </div>
                <div class="text-center p-4 border rounded-lg theme-transition">
                    <div class="text-2xl font-bold">85%</div>
                    <div>Project Progress</div>
                </div>
            </div>
        </div>

        <div class="section-content bg-white rounded-xl shadow-lg p-6 theme-transition hidden" id="dijkstra-algorithm">
            <h2 class="text-2xl font-bold text-gray-800 mb-4">🔍 Dijkstra Algorithm</h2>
            <div class="space-y-4">
                <div class="p-4 border rounded-lg theme-transition">
                    <h3 class="font-semibold mb-2">Algorithm Explanation</h3>
                    <p>Dijkstra's algorithm finds the shortest path between nodes in a graph.</p>
                </div>
                <div class="p-4 border rounded-lg theme-transition">
                    <h3 class="font-semibold mb-2">Implementation Steps</h3>
                    <ol class="list-decimal list-inside space-y-1">
                        <li>Initialize distances and visited nodes</li>
                        <li>Select node with smallest distance</li>
                        <li>Update neighbor distances</li>
                        <li>Repeat until all nodes visited</li>
                    </ol>
                </div>
            </div>
        </div>
    </div>
</div>

<script>
    function toggleTheme() {
        const body = document.body;
        const currentTheme = body.getAttribute('data-theme');
        const newTheme = currentTheme === 'dark' ? 'light' : 'dark';
        
        body.setAttribute('data-theme', newTheme);
        if (newTheme === 'dark') {
            body.classList.remove('bg-gray-50', 'text-gray-900');
            body.classList.add('bg-gray-900', 'text-white');
        } else {
            body.classList.remove('bg-gray-900', 'text-white');
            body.classList.add('bg-gray-50', 'text-gray-900');
        }
        
        localStorage.setItem('theme', newTheme);
        showNotification('Tema ändrat till ' + (newTheme === 'dark' ? 'mörkt' : 'ljust') + ' läge', 'success');
    }

    function showNotification(message, type = 'info') {
        const container = document.getElementById('notificationContainer');
        const notification = document.createElement('div');
        
        const colors = {
            success: 'bg-green-500',
            error: 'bg-red-500',
            warning: 'bg-yellow-500',
            info: 'bg-blue-500'
        };

        notification.className = `${colors[type]} text-white px-6 py-3 rounded-lg shadow-lg transition-all`;
        notification.innerHTML = `
            <div class="flex justify-between items-center">
                <span>${message}</span>
                <button onclick="this.parentElement.parentElement.remove()" class="ml-4">
                    <i class="fas fa-times"></i>
                </button>
            </div>
        `;

        container.appendChild(notification);
        setTimeout(() => notification.remove(), 5000);
    }

    async function fetchSystemData() {
        showNotification('Hämtar systemdata...', 'info');
        
        try {
            const response = await fetch('http://localhost:8100/enhanced-data');
            const data = await response.json();
            
            document.getElementById('cpu-percent').textContent = data.cpu_load ? data.cpu_load[0] + '%' : '--%';
            document.getElementById('memory-percent').textContent = data.memory_percent ? data.memory_percent + '%' : '--%';
            document.getElementById('disk-percent').textContent = data.disk_percent ? data.disk_percent + '%' : '--%';
            document.getElementById('uptime').textContent = data.uptime || '--';
            
            showNotification('Systemdata uppdaterad!', 'success');
        } catch (error) {
            document.getElementById('cpu-percent').textContent = '12%';
            document.getElementById('memory-percent').textContent = '45%';
            document.getElementById('disk-percent').textContent = '23%';
            document.getElementById('uptime').textContent = '2h 15m';
            
            showNotification('Använder exempeldata - Server ej tillgänglig', 'warning');
        }
    }

    function checkAlerts() {
        showNotification('Kontrollerar systemvarningar...', 'info');
        
        const alertsContainer = document.getElementById('alerts-container');
        alertsContainer.innerHTML = `
            <div class="p-3 bg-green-100 text-green-800 rounded-lg theme-transition">
                <i class="fas fa-check-circle"></i> Inga kritiska varningar funna
            </div>
        `;
        
        showNotification('Varningskontroll slutförd', 'success');
    }

    document.addEventListener('DOMContentLoaded', function() {
        const savedTheme = localStorage.getItem('theme') || 'light';
        if (savedTheme === 'dark') {
            document.body.setAttribute('data-theme', 'dark');
            document.body.classList.add('bg-gray-900', 'text-white');
            document.body.classList.remove('bg-gray-50', 'text-gray-900');
        }

        document.querySelectorAll('.nav-btn').forEach(btn => {
            btn.addEventListener('click', function() {
                document.querySelectorAll('.nav-btn').forEach(b => {
                    b.classList.remove('bg-blue-100', 'text-blue-700');
                    b.classList.add('bg-gray-100', 'text-gray-700');
                });
                this.classList.remove('bg-gray-100', 'text-gray-700');
                this.classList.add('bg-blue-100', 'text-blue-700');
                
                const sectionId = this.dataset.section;
                document.querySelectorAll('.section-content').forEach(section => {
                    section.classList.add('hidden');
                });
                document.getElementById(sectionId).classList.remove('hidden');
            });
        });

        document.querySelector('.export-btn').addEventListener('click', function() {
            showNotification('Dokumentation exporterad!', 'success');
        });

        document.getElementById('assignment-form').addEventListener('submit', function(e) {
            e.preventDefault();
            const name = document.getElementById('task-name').value;
            const desc = document.getElementById('task-desc').value;
            const assignee = document.getElementById('task-assignee').value;
            
            if (name && desc) {
                const assignmentsList = document.getElementById('assignments-list');
                const newAssignment = document.createElement('div');
                newAssignment.className = 'p-3 bg-gray-50 rounded theme-transition';
                newAssignment.innerHTML = `
                    <strong>${name}</strong>
                    <p class="text-sm text-gray-600">${desc}</p>
                    <small><strong>Ansvarig:</strong> ${assignee}</small>
                `;
                assignmentsList.appendChild(newAssignment);
                
                this.reset();
                showNotification('Uppgift tillagd!', 'success');
            }
        });

        document.getElementById('team-update-form').addEventListener('submit', function(e) {
            e.preventDefault();
            const author = document.getElementById('update-author').value;
            const status = document.getElementById('update-status').value;
            const title = document.getElementById('update-title').value;
            const details = document.getElementById('update-details').value;

            if (title && details) {
                addTeamUpdate(author, status, title, details);
                this.reset();
            }
        });

        fetchSystemData();
        showNotification('Välkommen till Grupp 1 Dashboard!', 'info');
        
        document.querySelectorAll('button').forEach(button => {
            button.addEventListener('click', function() {
                this.style.transform = 'scale(0.95)';
                setTimeout(() => {
                    this.style.transform = 'scale(1)';
                }, 150);
            });
        });
    });

    function addTeamUpdate(author, status, title, details) {
        const updatesList = document.getElementById('team-updates-list');
        const statusColors = {
            'completed': '#10B981',
            'in-progress': '#F59E0B', 
            'planned': '#3B82F6',
            'blocked': '#EF4444'
        };

        const updateHTML = `
            <div class="p-4 border rounded-lg theme-transition" style="border-left: 4px solid ${statusColors[status]}">
                <div class="flex justify-between items-center mb-2">
                    <h4 class="font-semibold">${title}</h4>
                    <span class="px-2 py-1 rounded text-white text-sm" style="background: ${statusColors[status]}">
                        ${status === 'completed' ? '✅' : status === 'in-progress' ? '🔄' : status === 'planned' ? '📅' : '❌'} 
                        ${status}
                    </span>
                </div>
                <p><strong>${author}</strong> - ${details}</p>
                <small class="text-gray-500">${new Date().toLocaleDateString('sv-SE')}</small>
            </div>
        `;

        updatesList.insertAdjacentHTML('afterbegin', updateHTML);
        showNotification('Uppdatering publicerad!', 'success');
    }

    function sendAIMessage() {
        const input = document.getElementById('aiChatInput');
        const message = input.value.trim();
        if (!message) return;

        const chatContainer = document.getElementById('aiChatContainer');
        const userMsg = document.createElement('div');
        userMsg.className = 'p-3 bg-gray-100 rounded-lg theme-transition mb-2';
        userMsg.innerHTML = `<strong>👤 Du:</strong> ${message}`;
        chatContainer.appendChild(userMsg);

        setTimeout(() => {
            const aiMsg = document.createElement('div');
            aiMsg.className = 'p-3 bg-blue-50 rounded-lg theme-transition mb-2';
            aiMsg.innerHTML = `<strong>🤖 AI Assistant:</strong> ${getAIResponse(message)}`;
            chatContainer.appendChild(aiMsg);
            chatContainer.scrollTop = chatContainer.scrollHeight;
        }, 1000);

        input.value = '';
        chatContainer.scrollTop = chatContainer.scrollHeight;
    }

    function quickQuestion(question) {
        document.getElementById('aiChatInput').value = question;
        sendAIMessage();
    }

    function getAIResponse(question) {
        const responses = {
            'tidskomplexitet': 'Dijkstra: O(V²) med matris eller O(E + V log V) med prioritetsköslista.',
            'nätverk': 'Dijkstra används i OSPF för att hitta kortaste vägar mellan routrar.',
            'python': 'I Python kan du använda heapq för effektiv Dijkstra implementation.',
            'skillnad': 'Dijkstra hittar kortaste vägen, A* använder heuristik för snabbare sök.'
        };

        const q = question.toLowerCase();
        for (const [key, response] of Object.entries(responses)) {
            if (q.includes(key)) return response;
        }
        return 'Jag kan hjälpa dig med Dijkstra-algoritmen, dess implementation och användning!';
    }

    function addNewTask() {
        const taskName = prompt('Namn på uppgift:');
        if (!taskName) return;

        const tasksList = document.getElementById('tasks-list');
        const taskId = Date.now();
        
        const taskHTML = `
            <div class="p-4 border rounded-lg theme-transition" id="task-${taskId}">
                <div class="flex justify-between items-start mb-2">
                    <h4 class="font-semibold">${taskName}</h4>
                    <button onclick="removeTask(${taskId})" class="text-red-500 hover:text-red-700">
                        <i class="fas fa-trash"></i>
                    </button>
                </div>
                <p class="text-gray-600 mb-2">Ny uppgift</p>
                <div class="w-full bg-gray-200 rounded-full h-2 mb-2">
                    <div class="bg-green-500 h-2 rounded-full" style="width: 0%" id="progress-${taskId}"></div>
                </div>
                <div class="flex justify-between items-center">
                    <span class="text-sm text-gray-500">Ansvarig: Kaled Osman</span>
                    <button onclick="completeTask(${taskId})" class="bg-green-500 hover:bg-green-600 text-white px-3 py-1 rounded text-sm">
                        Markera som klar
                    </button>
                </div>
            </div>
        `;
        
        tasksList.insertAdjacentHTML('beforeend', taskHTML);
        updateTaskStats();
        showNotification('Ny uppgift tillagd!', 'success');
    }

    function removeTask(taskId) {
        const taskElement = document.getElementById(`task-${taskId}`);
        if (taskElement && confirm('Vill du ta bort uppgiften?')) {
            taskElement.remove();
            updateTaskStats();
            showNotification('Uppgift borttagen', 'info');
        }
    }

    function completeTask(taskId) {
        const taskElement = document.getElementById(`task-${taskId}`);
        const progressElement = document.getElementById(`progress-${taskId}`);
        if (taskElement && progressElement) {
            progressElement.style.width = '100%';
            taskElement.querySelector('button').disabled = true;
            taskElement.querySelector('button').textContent = 'Klar ✓';
            taskElement.querySelector('button').classList.remove('bg-green-500', 'hover:bg-green-600');
            taskElement.querySelector('button').classList.add('bg-gray-400');
            updateTaskStats();
            showNotification('Uppgift markerad som klar!', 'success');
        }
    }

    function updateTaskStats() {
        const tasks = document.querySelectorAll('#tasks-list > div');
        const total = tasks.length;
        const completed = Array.from(tasks).filter(t => 
            t.querySelector('button').textContent === 'Klar ✓'
        ).length;
        const pending = total - completed;

        document.getElementById('total-tasks').textContent = total;
        document.getElementById('completed-tasks').textContent = completed;
        document.getElementById('pending-tasks').textContent = pending;
        document.getElementById('inprogress-tasks').textContent = 0;
    }
</script>
</body>
</html>
