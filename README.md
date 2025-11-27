<!DOCTYPE html>
<html lang="sv">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Full-Stack Dashboard - Dijkstra</title>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.0/css/all.min.css">
    <style>
        :root {
            --primary: #3498db;
            --secondary: #2ecc71;
            --success: #27ae60;
            --warning: #f39c12;
            --accent: #e74c3c;
            --light: #f4f4f4;
        }

        body {
            font-family: Arial, sans-serif;
            margin: 0;
            background: #fff;
        }

        .section {
            padding: 2rem;
            display: none;
        }

        .section.active {
            display: block;
        }

        .section-header {
            display: flex;
            justify-content: space-between;
            align-items: center;
            margin-bottom: 1rem;
        }

        .btn {
            cursor: pointer;
            border: none;
            padding: 0.5rem 1rem;
            border-radius: 6px;
            font-size: 0.9rem;
        }

        .stats-container {
            display: flex;
            gap: 1rem;
            flex-wrap: wrap;
            margin-bottom: 2rem;
        }

        .stat-card {
            background: var(--light);
            padding: 1rem;
            border-radius: 8px;
            flex: 1;
            min-width: 150px;
            text-align: center;
        }

        .stat-number {
            font-size: 1.5rem;
            font-weight: bold;
        }

        .task-card, .attachment-item, .ai-message, .user-message {
            background: white;
            padding: 1.5rem;
            border-radius: 10px;
            margin: 1rem 0;
        }

        .progress-bar {
            background: #eee;
            border-radius: 6px;
            overflow: hidden;
            height: 10px;
            margin-top: 0.5rem;
        }

        .progress-fill {
            background: var(--success);
            width: 0%;
            height: 100%;
        }

        .ai-chat-container {
            max-height: 300px;
            overflow-y: auto;
            border: 1px solid #ccc;
            border-radius: 6px;
            padding: 1rem;
            margin-bottom: 1rem;
        }

        .chat-input {
            display: flex;
            gap: 0.5rem;
        }

        .chat-input input {
            flex: 1;
            padding: 10px;
            border-radius: 6px;
            border: 1px solid #ccc;
        }

        .notification {
            padding: 1rem;
            margin: 0.5rem;
            border-radius: 6px;
            display: flex;
            justify-content: space-between;
            align-items: center;
            color: white;
            font-weight: bold;
        }

        .notification.success { background: var(--success); }
        .notification.info { background: var(--primary); }
        .notification-close { background: none; border: none; color: white; cursor: pointer; }
    </style>
</head>
<body>

<!-- Notification container -->
<div id="notificationContainer" style="position: fixed; top: 1rem; right: 1rem; z-index: 9999;"></div>

<!-- Navigation -->
<nav style="display:flex; gap:10px; padding:1rem; background:var(--primary); color:white;">
    <div class="nav-tab active" data-target="hela-projektet">Hela Projektet</div>
    <div class="nav-tab" data-target="dokumentation">Dokumentation</div>
    <div class="nav-tab" data-target="team-collaboration">Team Collaboration</div>
    <div class="nav-tab" data-target="team-updates">Team Updates</div>
    <div class="nav-tab" data-target="ai-assistant">AI Assistant</div>
    <div class="nav-tab" data-target="practical-tasks">Practical Tasks</div>
</nav>

<!-- Hela Projektet Section -->
<section id="hela-projektet" class="section active">
    <div class="section-header">
        <h2>📁 HELA PROJEKTET - Dokumentation & Presentation</h2>
        <button class="btn" style="background: var(--primary); color: white;" onclick="showNotification('Documentation exported!', 'success')">
            <i class="fas fa-download"></i> Export
        </button>
    </div>

    <div style="background: var(--primary); color: white; padding: 1.5rem; border-radius: 8px; margin-bottom: 2rem;">
        <h3>🚀 PROJEKT-SAMMANFATTNING:</h3>
        <p>"Grupp 1 - Full-Stack Dashboard med Algorithm Visualizer"</p>
    </div>

    <div style="margin-bottom: 2rem;">
        <h3>📊 PROJEKT-ÖVERSIKT</h3>
        <h4>🎯 Projektmål</h4>
        <ul>
            <li>Bygga ett fullt funktionellt CRUD dashboard</li>
            <li>Integrera Algorithm Visualizer för Dijkstra</li>
            <li>Demonstrera full-stack utveckling</li>
        </ul>

        <h4>🛠 Teknisk Stack</h4>
        <ul>
            <li>Backend: Python Flask</li>
            <li>Frontend: HTML5, CSS3, JavaScript</li>
            <li>Styling: Modern CSS med gradients</li>
            <li>Data: JSON-baserad "databas"</li>
        </ul>
    </div>

    <div style="margin-bottom: 2rem;">
        <h3>✅ FUNKTIONALITET</h3>
        
        <h4>📋 Task 1: Dashboard (CRUD)</h4>
        <ul>
            <li>✅ CREATE - Lägg till nya anställda</li>
            <li>✅ READ - Visa data i tabell</li>
            <li>✅ UPDATE - Redigera med modal</li>
            <li>✅ DELETE - Radera med bekräftelse</li>
            <li>✅ Realtidsstatistik</li>
        </ul>

        <h4>🔍 Task 2: Algorithm Visualizer</h4>
        <ul>
            <li>✅ Dijkstra algorithm demo</li>
            <li>✅ Graf-visualisering</li>
            <li>✅ Steg-för-steg förklaring</li>
            <li>✅ Responsiv design</li>
        </ul>

        <h4>🎨 Task 3: Design & UX</h4>
        <ul>
            <li>✅ Modern design med gradients</li>
            <li>✅ Responsiv för alla enheter</li>
            <li>✅ Professionell navigation</li>
            <li>✅ Användarvänliga formulär</li>
        </ul>
    </div>
</section>

<!-- Dokumentation Section -->
<section id="dokumentation" class="section">
    <div class="section-header">
        <h2>📚 Dokumentation - Graph Algorithms</h2>
    </div>

    <div style="margin-bottom: 2rem;">
        <h3>Av Grupp 1: Fahad Hussain, Stefan Österberg, Kaled Osman, Marcus Tibell, Jens Annell, Luwam</h3>
        
        <h4>Dijkstra's Algorithm</h4>
        <p><strong>The purpose of Dijkstra's algorithm</strong> is to find the shortest possible path between nodes in a weighted graph, which could for example, be representative of:</p>
        <ul>
            <li>Road Networks</li>
            <li>Shortest path in OSPF Networks</li>
            <li>Pathing in Video Games</li>
            <li>Microchip Design</li>
        </ul>

        <h4>Kaled - Why does the shortest path matter?</h4>
        <p>"As mentioned earlier, the purpose of the algorithm is to find the shortest possible path, so why does the shortest possible path matter? When does the shortest path matter, when doesn't it matter, and why?"</p>

        <h4>Fahad - Uses of shortest path</h4>
        <p>"A common usage of the shortest path within our sphere of operation is for example, in networking, where speed and efficiency are crucial to the fluid operation of the network..."</p>

        <h4>Jens - Related Algorithms & Problems</h4>
        <p>"Dijkstra's original algorithm can be modified and extended, for example: At times it may be desirable to get a LESS than optimal mathematical solution..."</p>

        <h4>Stefan - Link-State & Dijkstra's real world use</h4>
        <p>"In link-state routing, every router maintains a detailed view of the entire network topology, giving it a clear picture of how all nodes are interconnected..."</p>

        <h4>Luwam - Testing & Validation</h4>
        <p>"Proper testing of Dijkstra's algorithm involves validating edge cases, performance with large graphs, and ensuring correctness across different network topologies..."</p>
    </div>
</section>

<!-- Team Collaboration Section -->
<section id="team-collaboration" class="section">
    <div class="section-header">
        <h2>📝 Team Collaboration - Grupp Dokumentation</h2>
    </div>
    
    <div style="display: grid; grid-template-columns: 1fr 1fr; gap: 2rem; margin-top: 2rem;">
        <div style="background: var(--light); padding: 1.5rem; border-radius: 12px;">
            <h3>➕ Lägg Till Uppgift</h3>
            <form id="assignment-form">
                <div style="margin-bottom: 1rem;">
                    <label>Uppgiftsnamn:</label>
                    <input type="text" id="task-name" style="width: 100%; padding: 10px; border: 1px solid #ccc; border-radius: 6px;">
                </div>
                <div style="margin-bottom: 1rem;">
                    <label>Beskrivning:</label>
                    <textarea id="task-desc" style="width: 100%; padding: 10px; border: 1px solid #ccc; border-radius: 6px; height: 100px;"></textarea>
                </div>
                <div style="margin-bottom: 1rem;">
                    <label>Ansvarig:</label>
                    <select id="task-assignee" style="width: 100%; padding: 10px; border: 1px solid #ccc; border-radius: 6px;">
                        <option value="Kaled Osman">Kaled Osman</option>
                        <option value="Fahad Hussain">Fahad Hussain</option>
                        <option value="Stefan Österberg">Stefan Österberg</option>
                        <option value="Marcus Tibell">Marcus Tibell</option>
                        <option value="Jens Annell">Jens Annell</option>
                        <option value="Luwam">Luwam</option>
                    </select>
                </div>
                <button type="submit" class="btn" style="background: var(--success); color: white; width: 100%;">
                    ➕ Lägg Till Uppgift
                </button>
            </form>
        </div>

        <div style="background: var(--light); padding: 1.5rem; border-radius: 12px;">
            <h3>📋 Aktuella Uppgifter</h3>
            <div id="assignments-list">
                <div style="background: white; padding: 1rem; margin: 0.5rem 0; border-radius: 8px;">
                    <strong>Förbättra Dijkstra Algorithm</strong>
                    <p>Lägg till prestandaförbättringar och komplexitetsanalys</p>
                    <small><strong>Ansvarig:</strong> Kaled Osman</small>
                </div>
                <div style="background: white; padding: 1rem; margin: 0.5rem 0; border-radius: 8px;">
                    <strong>OSPF Protocols Research</strong>
                    <p>Studera OSPF-protokoll och deras tillämpningar i nätverk</p>
                    <small><strong>Ansvarig:</strong> Fahad Hussain</small>
                </div>
            </div>
        </div>
    </div>
</section>

<!-- Team Updates Section -->
<section id="team-updates" class="section">
    <div class="section-header">
        <h2>👥 Team Updates & Uppgifter</h2>
    </div>

    <div style="background: var(--light); padding: 1.5rem; border-radius: 12px; margin-bottom: 2rem;">
        <h3>➕ Lägg Till Din Uppdatering</h3>
        <form id="team-update-form">
            <div style="display: grid; grid-template-columns: 1fr 1fr; gap: 1rem; margin-bottom: 1rem;">
                <div>
                    <label>Ditt Namn:</label>
                    <select id="update-author" style="width: 100%; padding: 10px; border: 1px solid #ccc; border-radius: 6px;">
                        <option value="Kaled Osman">Kaled Osman</option>
                        <option value="Fahad Hussain">Fahad Hussain</option>
                        <option value="Stefan Österberg">Stefan Österberg</option>
                        <option value="Marcus Tibell">Marcus Tibell</option>
                        <option value="Jens Annell">Jens Annell</option>
                        <option value="Luwam">Luwam</option>
                    </select>
                </div>
                <div>
                    <label>Status:</label>
                    <select id="update-status" style="width: 100%; padding: 10px; border: 1px solid #ccc; border-radius: 6px;">
                        <option value="completed">✅ Avslutad</option>
                        <option value="in-progress">🔄 Pågående</option>
                        <option value="planned">📅 Planerad</option>
                        <option value="blocked">❌ Blockerad</option>
                    </select>
                </div>
            </div>
            
            <div style="margin-bottom: 1rem;">
                <label>Uppgiftsbeskrivning:</label>
                <input type="text" id="update-title" placeholder="Vad har du gjort?" style="width: 100%; padding: 10px; border: 1px solid #ccc; border-radius: 6px;">
            </div>
            
            <div style="margin-bottom: 1rem;">
                <label>Detaljer:</label>
                <textarea id="update-details" placeholder="Beskriv ditt arbete..." style="width: 100%; padding: 10px; border: 1px solid #ccc; border-radius: 6px; height: 100px;"></textarea>
            </div>
            
            <button type="submit" class="btn" style="background: var(--success); color: white; width: 100%;">
                ➕ Publicera Uppdatering
            </button>
        </form>
    </div>

    <div id="team-updates-list">
        <!-- Dynamiska uppdateringar hamnar här -->
    </div>
</section>

<!-- AI Assistant Section -->
<section id="ai-assistant" class="section">
    <div class="section-header">
        <h2>🤖 AI Assistant - Dijkstra Helper</h2>
    </div>

    <div class="ai-chat-container" id="aiChatContainer">
        <div class="ai-message">
            <strong>🤖 AI Assistant:</strong> Hej! Jag är här för att hjälpa dig med Dijkstra-algoritmen och ditt projekt. Ställ vilken fråga som helst!
        </div>
    </div>

    <div class="chat-input">
        <input type="text" id="aiChatInput" placeholder="Skriv din fråga om Dijkstra eller projektet...">
        <button class="btn" style="background: var(--secondary); color: white;" onclick="sendAIMessage()">
            <i class="fas fa-paper-plane"></i> Skicka
        </button>
    </div>

    <div style="margin-top: 2rem;">
        <h3>🚀 Snabbkommandon</h3>
        <div style="display: flex; gap: 10px; flex-wrap: wrap; margin-top: 1rem;">
            <button class="btn" style="background: var(--light);" onclick="quickQuestion('Vad är tidskomplexiteten för Dijkstra?')">
                ⏱️ Tidskomplexitet
            </button>
            <button class="btn" style="background: var(--light);" onclick="quickQuestion('Ge exempel på Dijkstra i nätverk')">
                🌐 Nätverksanvändning
            </button>
            <button class="btn" style="background: var(--light);" onclick="quickQuestion('Hur implementerar jag Dijkstra i Python?')">
                🐍 Python Implementation
            </button>
            <button class="btn" style="background: var(--light);" onclick="quickQuestion('Vad är skillnaden mellan Dijkstra och A*?')">
                🔍 Dijkstra vs A*
            </button>
        </div>
    </div>
</section>

<!-- Practical Tasks Section -->
<section id="practical-tasks" class="section">
    <div class="section-header">
        <h2>🎯 Practical Tasks - Projektuppgifter</h2>
        <button class="btn" style="background: var(--success); color: white;" onclick="addNewTask()">
            <i class="fas fa-plus"></i> Ny Uppgift
       
