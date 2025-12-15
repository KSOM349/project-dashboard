<!DOCTYPE html>
<html lang="sv" dir="ltr">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Hela Projektet - Komplett Dashboard</title>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <style>
        :root {
            --primary: #2c3e50;
            --secondary: #3498db;
            --accent: #e74c3c;
            --success: #27ae60;
            --warning: #f39c12;
            --light: #ecf0f1;
            --dark: #2c3e50;
        }

        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
        }

        body {
            background: #f8f9fa;
            min-height: 100vh;
            color: var(--dark);
        }

        .container {
            max-width: 1400px;
            margin: 0 auto;
            padding: 20px;
        }

        header {
            background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
            color: white;
            padding: 2rem;
            border-radius: 15px;
            margin-bottom: 2rem;
            text-align: center;
        }

        header h1 {
            font-size: 2.5rem;
            margin-bottom: 0.5rem;
        }

        .nav-tabs {
            display: flex;
            background: white;
            border-radius: 12px;
            padding: 10px;
            margin-bottom: 2rem;
            flex-wrap: wrap;
            justify-content: center;
            gap: 10px;
        }

        .nav-tab {
            padding: 12px 24px;
            border-radius: 8px;
            cursor: pointer;
            transition: all 0.3s ease;
            font-weight: 600;
            display: flex;
            align-items: center;
            gap: 8px;
        }

        .nav-tab:hover {
            background: var(--light);
        }

        .nav-tab.active {
            background: var(--secondary);
            color: white;
        }

        .section {
            display: none;
            background: white;
            border-radius: 15px;
            padding: 2rem;
            margin-bottom: 2rem;
        }

        .section.active {
            display: block;
            animation: fadeIn 0.5s ease;
        }

        @keyframes fadeIn {
            from { opacity: 0; transform: translateY(20px); }
            to { opacity: 1; transform: translateY(0); }
        }

        .section-header {
            margin-bottom: 1.5rem;
            padding-bottom: 1rem;
            border-bottom: 2px solid var(--light);
        }

        .section-header h2 {
            font-size: 1.8rem;
            color: var(--primary);
        }

        .stats-container {
            display: grid;
            grid-template-columns: repeat(auto-fit, minmax(200px, 1fr));
            gap: 1.5rem;
            margin-bottom: 2rem;
        }

        .stat-card {
            background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
            color: white;
            padding: 1.5rem;
            border-radius: 12px;
            text-align: center;
        }

        .stat-number {
            font-size: 2.5rem;
            font-weight: bold;
            margin-bottom: 0.5rem;
        }

        .code-container {
            background: #2d2d2d;
            color: #f8f8f2;
            padding: 1.5rem;
            border-radius: 8px;
            margin: 1.5rem 0;
            overflow-x: auto;
            font-family: 'Courier New', monospace;
        }

        .team-table {
            width: 100%;
            border-collapse: collapse;
            margin-top: 1rem;
        }

        .team-table th,
        .team-table td {
            padding: 12px 15px;
            text-align: left;
            border-bottom: 1px solid var(--light);
        }

        .team-table th {
            background: var(--primary);
            color: white;
        }

        .btn {
            padding: 8px 16px;
            border: none;
            border-radius: 6px;
            cursor: pointer;
            font-weight: 600;
            transition: all 0.3s ease;
        }

        .btn-edit {
            background: var(--warning);
            color: white;
        }

        .btn-delete {
            background: var(--accent);
            color: white;
        }

        .ai-chat-container {
            background: #f8f9fa;
            border-radius: 12px;
            padding: 1.5rem;
            margin-top: 1rem;
        }

        .ai-message {
            background: white;
            padding: 1rem;
            margin: 0.5rem 0;
            border-radius: 8px;
            border-left: 4px solid var(--secondary);
        }

        .user-message {
            background: var(--secondary);
            color: white;
            padding: 1rem;
            margin: 0.5rem 0;
            border-radius: 8px;
        }

        .chat-input {
            display: flex;
            gap: 10px;
            margin-top: 1rem;
        }

        .chat-input input {
            flex: 1;
            padding: 12px;
            border: 2px solid var(--light);
            border-radius: 8px;
        }

        @media (max-width: 768px) {
            .nav-tabs {
                flex-direction: column;
            }
            .stats-container {
                grid-template-columns: 1fr;
            }
        }
    </style>
</head>
<body>
    <div class="container">
        <header>
            <h1>🚀 Hela Projektet - Komplett Dashboard</h1>
            <p>Grupp 1 - Full-Stack Dashboard med Algorithm Visualizer</p>
        </header>

        <div class="nav-tabs">
            <div class="nav-tab active" data-target="algorithm-visualizer">
                <i class="fas fa-project-diagram"></i> 📊 Algorithm Visualizer
            </div>
            <div class="nav-tab" data-target="min-implementation">
                <i class="fas fa-code"></i> 💻 Min Implementation
            </div>
            <div class="nav-tab" data-target="team-dashboard">
                <i class="fas fa-users"></i> 👥 Team Dashboard
            </div>
            <div class="nav-tab" data-target="hela-projektet">
                <i class="fas fa-folder-open"></i> 📁 Hela Projektet
            </div>
            <div class="nav-tab" data-target="dijkstra-algorithm">
                <i class="fas fa-route"></i> 🔍 Dijkstra Algorithm
            </div>
            <div class="nav-tab" data-target="dokumentation">
                <i class="fas fa-book"></i> 📚 Dokumentation
            </div>
            <div class="nav-tab" data-target="team-collaboration">
                <i class="fas fa-tasks"></i> 📝 Team Collaboration
            </div>
            <div class="nav-tab" data-target="team-updates">
                <i class="fas fa-comments"></i> 👥 Team Updates
            </div>
            <div class="nav-tab" data-target="ai-assistant">
                <i class="fas fa-robot"></i> 🤖 AI Assistant
            </div>
        </div>

        <!-- Algorithm Visualizer Section -->
        <section id="algorithm-visualizer" class="section active">
            <div class="section-header">
                <h2>📊 Algorithm Visualizer</h2>
            </div>
            
            <div class="stats-container">
                <div class="stat-card">
                    <div class="stat-number">5</div>
                    <div class="stat-label">Servrar i Nätverk</div>
                </div>
                <div class="stat-card">
                    <div class="stat-number">13</div>
                    <div class="stat-label">Kortaste Väg (ms)</div>
                </div>
                <div class="stat-card">
                    <div class="stat-number">100%</div>
                    <div class="stat-label">Optimering</div>
                </div>
            </div>

            <div class="code-container">
                <pre>// Server Network Configuration
servers = {
    'WebServer': {'Database': 5, 'Cache': 2},
    'Database': {'Backup': 8, 'WebServer': 5},
    'Cache': {'CDN': 3, 'WebServer': 2},
    'CDN': {'Cache': 3},
    'Backup': {'Database': 8}
}</pre>
            </div>

            <div style="background: var(--success); color: white; padding: 1rem; border-radius: 8px; margin-top: 1rem;">
                <h4>🎯 Dijkstra Steg-för-Steg:</h4>
                <p>Start: WebServer (avstånd 0)</p>
                <p>Steg 1: Hitta grannar → Database=5, Cache=2</p>
                <p>Steg 2: Välj Cache → hitta CDN=5</p>
                <p>Steg 3: Välj Database → hitta Backup=13</p>
                <p><strong>Resultat: Alla kortaste vägar funna! ✅</strong></p>
            </div>
        </section>

        <!-- Min Implementation Section -->
        <section id="min-implementation" class="section">
            <div class="section-header">
                <h2>💻 Min Implementation - Kaled Osman</h2>
            </div>

            <div class="code-container">
                <pre>import heapq

def dijkstra_dashboard(graph, start):
    distances = {node: float('infinity') for node in graph}
    distances[start] = 0
    priority_queue = [(0, start)]
    
    while priority_queue:
        current_distance, current_node = heapq.heappop(priority_queue)
        
        if current_distance > distances[current_node]:
            continue
            
        for neighbor, weight in graph[current_node].items():
            distance = current_distance + weight
            
            if distance < distances[neighbor]:
                distances[neighbor] = distance
                heapq.heappush(priority_queue, (distance, neighbor))
                
    return distances

# Användning i vår dashboard
servers = {
    'WebServer': {'Database': 5, 'Cache': 2},
    'Database': {'Backup': 8},
    'Cache': {'CDN': 3}
}

resultat = dijkstra_dashboard(servers, 'WebServer')
print("Kortaste avstånd:", resultat)</pre>
            </div>
        </section>

        <!-- Team Dashboard Section -->
        <section id="team-dashboard" class="section">
            <div class="section-header">
                <h2>👥 Team Dashboard - Grupp 1</h2>
            </div>

            <div class="stats-container">
                <div class="stat-card">
                    <div class="stat-number">6</div>
                    <div class="stat-label">Team Medlemmar</div>
                </div>
                <div class="stat-card">
                    <div class="stat-number">100%</div>
                    <div class="stat-label">Projekt Framsteg</div>
                </div>
                <div class="stat-card">
                    <div class="stat-number">5</div>
                    <div class="stat-label">Implementerade Algoritmer</div>
                </div>
            </div>

            <table class="team-table">
                <thead>
                    <tr>
                        <th>ID</th>
                        <th>Namn</th>
                        <th>Roll</th>
                        <th>Avdelning</th>
                        <th>Bidrag</th>
                        <th>Åtgärder</th>
                    </tr>
                </thead>
                <tbody>
                    <tr>
                        <td>1</td>
                        <td>Kaled Osman</td>
                        <td>Dijkstra Implementation</td>
                        <td>Algoritmer</td>
                        <td>Dashboard & Kod</td>
                        <td>
                            <button class="btn btn-edit">Redigera</button>
                            <button class="btn btn-delete">Radera</button>
                        </td>
                    </tr>
                    <tr>
                        <td>2</td>
                        <td>Fahad Hussain</td>
                        <td>Nätverksanalys</td>
                        <td>Nätverk</td>
                        <td>OSPF Research</td>
                        <td>
                            <button class="btn btn-edit">Redigera</button>
                            <button class="btn btn-delete">Radera</button>
                        </td>
                    </tr>
                    <tr>
                        <td>3</td>
                        <td>Stefan Österberg</td>
                        <td>Algoritmteori</td>
                        <td>Forskning</td>
                        <td>Dijkstra Theory</td>
                        <td>
                            <button class="btn btn-edit">Redigera</button>
                            <button class="btn btn-delete">Radera</button>
                        </td>
                    </tr>
                    <tr>
                        <td>4</td>
                        <td>Marcus Tibell</td>
                        <td>Användningsfall</td>
                        <td>Applikation</td>
                        <td>Real-world Use Cases</td>
                        <td>
                            <button class="btn btn-edit">Redigera</button>
                            <button class="btn btn-delete">Radera</button>
                        </td>
                    </tr>
                    <tr>
                        <td>5</td>
                        <td>Jens Annell</td>
                        <td>Kodimplementation</td>
                        <td>Utveckling</td>
                        <td>Python Code</td>
                        <td>
                            <button class="btn btn-edit">Redigera</button>
                            <button class="btn btn-delete">Radera</button>
                        </td>
                    </tr>
                    <tr>
                        <td>6</td>
                        <td>Luwam</td>
                        <td>Test & Validering</td>
                        <td>Kvalitet</td>
                        <td>Algorithm Testing</td>
                        <td>
                            <button class="btn btn-edit">Redigera</button>
                            <button class="btn btn-delete">Radera</button>
                        </td>
                    </tr>
                </tbody>
            </table>
        </section>

        <!-- Hela Projektet Section -->
        <section id="hela-projektet" class="section">
            <div class="section-header">
                <h2>📁 HELA PROJEKTET - Dokumentation & Presentation</h2>
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
            </div>
        </section>

        <!-- Dijkstra Algorithm Section -->
        <section id="dijkstra-algorithm" class="section">
            <div class="section-header">
                <h2>🔍 Dijkstra Algorithm - Steg för Steg</h2>
            </div>

            <div class="stats-container">
                <div class="stat-card">
                    <div class="stat-number">5</div>
                    <div class="stat-label">Servrar i Nätverk</div>
                </div>
                <div class="stat-card">
                    <div class="stat-number">13</div>
                    <div class="stat-label">Kortaste Väg (ms)</div>
                </div>
                <div class="stat-card">
                    <div class="stat-number">100%</div>
                    <div class="stat-label">Optimering</div>
                </div>
            </div>

            <div class="code-container">
                <pre>// Vårt Server-Nätverk:
servers = {
    'WebServer': {'Database': 5, 'Cache': 2},
    'Database': {'Backup': 8, 'WebServer': 5},
    'Cache': {'CDN': 3, 'WebServer': 2},
    'CDN': {'Cache': 3},
    'Backup': {'Database': 8}
}</pre>
            </div>
        </section>

        <!-- Dokumentation Section -->
        <section id="dokumentation" class="section">
            <div class="section-header">
                <h2>📚 Dokumentation - Graph Algorithms</h2>
            </div>

            <div style="margin-bottom: 2rem;">
                <h3>Av Grupp 1: Fahad Hussain, Stefan Österberg, Kaled Osman, Marcus Tibell, Jens Annell</h3>
                
                <h4>Dijkstra's Algorithm</h4>
                <p><strong>The purpose of Dijkstra's algorithm</strong> is to find the shortest possible path between nodes in a weighted graph.</p>
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
                            <input type="text" style="width: 100%; padding: 10px; border: 1px solid #ccc; border-radius: 6px;">
                        </div>
                        <div style="margin-bottom: 1rem;">
                            <label>Beskrivning:</label>
                            <textarea style="width: 100%; padding: 10px; border: 1px solid #ccc; border-radius: 6px; height: 100px;"></textarea>
                        </div>
                        <button type="submit" class="btn" style="background: var(--success); color: white; width: 100%;">
                            ➕ Lägg Till Uppgift
                        </button>
                    </form>
                </div>

                <div style="background: var(--light); padding: 1.5rem; border-radius: 12px;">
                    <h3>📋 Aktuella Uppgifter</h3>
                    <div>
                        <div style="background: white; padding: 1rem; margin: 0.5rem 0; border-radius: 8px;">
                            <strong>Förbättra Dijkstra Algorithm</strong>
                            <p>Lägg till prestandaförbättringar</p>
                            <small><strong>Ansvarig:</strong> Kaled Osman</small>
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
                            <select style="width: 100%; padding: 10px; border: 1px solid #ccc; border-radius: 6px;">
                                <option>Kaled Osman</option>
                                <option>Fahad Hussain</option>
                                <option>Stefan Österberg</option>
                            </select>
                        </div>
                        <div>
                            <label>Status:</label>
                            <select style="width: 100%; padding: 10px; border: 1px solid #ccc; border-radius: 6px;">
                                <option>✅ Avslutad</option>
                                <option>🔄 Pågående</option>
                                <option>📅 Planerad</option>
                            </select>
                        </div>
                    </div>
                    
                    <div style="margin-bottom: 1rem;">
                        <label>Uppgiftsbeskrivning:</label>
                        <input type="text" placeholder="Vad har du gjort?" style="width: 100%; padding: 10px; border: 1px solid #ccc; border-radius: 6px;">
                    </div>
                    
                    <button type="submit" class="btn" style="background: var(--success); color: white; width: 100%;">
                        ➕ Publicera Uppdatering
                    </button>
                </form>
            </div>

            <div>
                <h3>📋 Senaste Uppdateringar</h3>
                <div>
                    <div style="background: white; padding: 1rem; margin: 0.5rem 0; border-radius: 8px; border-left: 4px solid var(--success);">
                        <strong>Kaled Osman</strong> - <span style="color: var(--success);">✅ Avslutad</span>
                        <p style="margin: 0.5rem 0;">Implementerat Dijkstra Algorithm</p>
                        <small style="color: #666;">Idag 14:30</small>
                    </div>
                </div>
            </div>
        </section>

        <!-- AI Assistant Section -->
        <section id="ai-assistant" class="section">
            <div class="section-header">
                <h2>🤖 AI Assistant - Smart Helper</h2>
            </div>

            <div class="ai-chat-container">
                <div id="ai-chat-messages">
                    <div class="ai-message">
                        <strong>AI Assistant:</strong> Hej! Jag är här för att hjälpa dig med ditt projekt. Du kan fråga mig om Dijkstra algoritmer, team samarbete, eller något annat!
                    </div>
                </div>

                <div class="chat-input">
                    <input type="text" id="ai-input" placeholder="Ställ din fråga här...">
                    <button class="btn" style="background: var(--secondary); color: white;" onclick="askAI()">
                        <i class="fas fa-paper-plane"></i> Skicka
                    </button>
                </div>
            </div>

            <div style="margin-top: 2rem;">
                <h3>💡 Snabbhjälp</h3>
                <div style="display: grid; grid-template-columns: repeat(auto-fit, minmax(200px, 1fr)); gap: 1rem;">
                    <button class="btn" style="background: var(--light);" onclick="quickQuestion('Hur förbättrar jag Dijkstra prestanda?')">
                        🚀 Dijkstra Optimering
                    </button>
                    <button class="btn" style="background: var(--light);" onclick="quickQuestion('Hur lägger jag till en ny teammedlem?')">
                        👥 Team Management
                    </button>
                    <button class="btn" style="background: var(--light);" onclick="quickQuestion('Vad är skillnaden mellan Dijkstra och A*?')">
                        🔍 Algorithm Jämförelse
                    </button>
                </div>
            </div>
        </section>
    </div>

    <script>
        // Navigation System
        document.addEventListener('DOMContentLoaded', function() {
            const navTabs = document.querySelectorAll('.nav-tab');
            const sections = document.querySelectorAll('.section');
            
            navTabs.forEach(tab => {
                tab.addEventListener('click', function() {
                    const targetId = this.getAttribute('data-target');
                    
                    // Update active tab
                    navTabs.forEach(t => t.classList.remove('active'));
                    this.classList.add('active');
                    
                    // Show target section
                    sections.forEach(section => {
                        section.classList.remove('active');
                        if (section.id === targetId) {
                            section.classList.add('active');
                        }
                    });
                });
            });

            // Team Updates Form
            const updateForm = document.getElementById('team-update-form');
            if (updateForm) {
                updateForm.addEventListener('submit', function(event) {
                    event.preventDefault();
                    alert('Tack för din uppdatering! Den har sparats.');
                    this.reset();
                });
            }

            // Assignment Form
            const assignmentForm = document.getElementById('assignment-form');
            if (assignmentForm) {
                assignmentForm.addEventListener('submit', function(event) {
                    event.preventDefault();
                    alert('Uppgiften har lagts till!');
                    this.reset();
                });
            }
        });

        // AI Assistant Functions
        function askAI() {
            const input = document.getElementById('ai-input');
            const question = input.value.trim();
            
            if (!question) return;
            
            addMessage(question, 'user');
            input.value = '';
            
            // Simulate AI thinking
            setTimeout(() => {
                const response = generateAIResponse(question);
                addMessage(response, 'ai');
            }, 1000);
        }

        function quickQuestion(question) {
            document.getElementById('ai-input').value = question;
            askAI();
        }

        function addMessage(text, sender) {
            const chatContainer = document.getElementById('ai-chat-messages');
            const messageDiv = document.createElement('div');
            
            if (sender === 'user') {
                messageDiv.className = 'user-message';
                messageDiv.innerHTML = `<strong>Du:</strong> ${text}`;
            } else {
                messageDiv.className = 'ai-message';
                messageDiv.innerHTML = `<strong>AI Assistant:</strong> ${text}`;
            }
            
            chatContainer.appendChild(messageDiv);
            chatContainer.scrollTop = chatContainer.scrollHeight;
        }

        function generateAIResponse(question) {
            const responses = {
                'dijkstra': 'För att förbättra Dijkstra prestanda kan du använda en min-heap istället för en lista. Detta reducerar tidskomplexiteten från O(V^2) till O(E log V).',
                'team': 'För att lägga till en ny teammedlem, gå till Team Dashboard och klicka på "Lägg till ny". Fyll i deras information och roll.',
                'algorithm': 'Dijkstra hittar kortaste vägen från en startnod till alla andra, medan A* använder heuristik för att fokusera på mål noden.',
                'performance': 'För bättre prestanda i din dashboard, överväg att använda virtual scrolling för stora datamängder och memoization för beräkningar.',
                'default': 'Jag kan hjälpa dig med Dijkstra algoritmer, team samarbete, kod optimering, och projekt management. Var mer specifik så ger jag bättre svar!'
            };

            question = question.toLowerCase();
            
            if (question.includes('dijkstra') || question.includes('prestanda') || question.includes('optimering')) {
                return responses.dijkstra;
            } else if (question.includes('team') || question.includes('medlem') || question.includes('lägg till')) {
                return responses.team;
            } else if (question.includes('algorithm') || question.includes('jämförelse') || question.includes('skillnad')) {
                return responses.algorithm;
            } else if (question.includes('prestanda') || question.includes('snabb') || question.includes('optimera')) {
                return responses.performance;
            } else {
                return responses.default;
            }
        }
    </script>
</body>
</html>
