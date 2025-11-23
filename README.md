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
            --gradient: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
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
            background: var(--gradient);
            color: white;
            padding: 2rem;
            border-radius: 15px;
            margin-bottom: 2rem;
            text-align: center;
            box-shadow: 0 8px 25px rgba(0,0,0,0.1);
        }

        header h1 {
            font-size: 2.5rem;
            margin-bottom: 0.5rem;
        }

        .notification-container {
            position: fixed;
            top: 20px;
            right: 20px;
            z-index: 1000;
        }

        .notification {
            background: var(--success);
            color: white;
            padding: 15px 20px;
            margin: 10px 0;
            border-radius: 10px;
            box-shadow: 0 4px 15px rgba(0,0,0,0.2);
            animation: slideInRight 0.3s ease;
            display: flex;
            align-items: center;
            justify-content: space-between;
            min-width: 300px;
        }

        @keyframes slideInRight {
            from { transform: translateX(100%); opacity: 0; }
            to { transform: translateX(0); opacity: 1; }
        }

        .notification.error { background: var(--accent); }
        .notification.warning { background: var(--warning); }
        .notification.info { background: var(--secondary); }

        .notification-close {
            background: none;
            border: none;
            color: white;
            font-size: 1.2rem;
            cursor: pointer;
            margin-left: 10px;
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
            box-shadow: 0 4px 15px rgba(0,0,0,0.1);
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
            transform: translateY(-2px);
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
            box-shadow: 0 4px 15px rgba(0,0,0,0.1);
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
            display: flex;
            justify-content: space-between;
            align-items: center;
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
            background: var(--gradient);
            color: white;
            padding: 1.5rem;
            border-radius: 12px;
            text-align: center;
            transition: transform 0.3s ease;
            box-shadow: 0 4px 15px rgba(0,0,0,0.1);
        }

        .stat-card:hover {
            transform: translateY(-5px);
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

        .team-table tr:hover {
            background: var(--light);
        }

        .btn {
            padding: 8px 16px;
            border: none;
            border-radius: 6px;
            cursor: pointer;
            font-weight: 600;
            transition: all 0.3s ease;
        }

        .btn:hover {
            transform: translateY(-2px);
            box-shadow: 0 4px 8px rgba(0,0,0,0.2);
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
            max-height: 400px;
            overflow-y: auto;
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
            font-size: 1rem;
        }

        .chat-input input:focus {
            outline: none;
            border-color: var(--secondary);
        }

        .task-item {
            background: #f8f9fa;
            padding: 1.5rem;
            margin: 1rem 0;
            border-radius: 10px;
            border-left: 4px solid var(--success);
        }

        .progress-bar {
            background: #e9ecef;
            border-radius: 10px;
            height: 10px;
            margin: 10px 0;
            overflow: hidden;
        }

        .progress-fill {
            background: var(--success);
            height: 100%;
            transition: width 0.3s ease;
        }

        .attachment-item {
            background: white;
            padding: 1rem;
            margin: 0.5rem 0;
            border-radius: 8px;
            border-left: 4px solid var(--secondary);
        }

        .task-list {
            margin-top: 1.5rem;
        }

        .task-card {
            background: white;
            border-radius: 10px;
            padding: 1.5rem;
            margin-bottom: 1rem;
            box-shadow: 0 2px 10px rgba(0,0,0,0.1);
            border-left: 4px solid var(--secondary);
        }

        .task-header {
            display: flex;
            justify-content: space-between;
            align-items: center;
            margin-bottom: 0.5rem;
        }

        .task-title {
            font-weight: bold;
            font-size: 1.1rem;
        }

        .task-priority {
            padding: 4px 8px;
            border-radius: 20px;
            font-size: 0.8rem;
            font-weight: bold;
        }

        .priority-high { background: var(--accent); color: white; }
        .priority-medium { background: var(--warning); color: white; }
        .priority-low { background: var(--success); color: white; }

        .task-meta {
            display: flex;
            gap: 1rem;
            font-size: 0.9rem;
            color: #666;
            margin-bottom: 0.5rem;
        }

        @media (max-width: 768px) {
            .nav-tabs {
                flex-direction: column;
            }
            .stats-container {
                grid-template-columns: 1fr;
            }
            .team-table {
                display: block;
                overflow-x: auto;
            }
            .notification {
                min-width: 250px;
                right: 10px;
            }
        }
    </style>
</head>
<body>
    <div class="notification-container" id="notificationContainer"></div>

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
            <div class="nav-tab" data-target="practical-tasks">
                <i class="fas fa-tasks"></i> 🎯 Practical Tasks
            </div>
        </div>

        <!-- Algorithm Visualizer Section -->
        <section id="algorithm-visualizer" class="section active">
            <div class="section-header">
                <h2>📊 Algorithm Visualizer</h2>
                <button class="btn" style="background: var(--success); color: white;" onclick="showNotification('Visualization refreshed!', 'success')">
                    <i class="fas fa-sync-alt"></i> Refresh
                </button>
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

            <div style="background: var(--light); height: 300px; border-radius: 12px; display: flex; align-items: center; justify-content: center; margin: 2rem 0; border: 2px dashed var(--secondary);">
                <div style="text-align: center; color: var(--dark);">
                    <i class="fas fa-project-diagram" style="font-size: 3rem; margin-bottom: 1rem;"></i>
                    <h3>Interaktiv Dijkstra Visualisering</h3>
                    <p>Plats för den interaktiva grafvisualiseringen</p>
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

            <div style="margin-top: 2rem;">
                <h3>🎤 Min Presentation – Kaled Osman</h3>
                <div style="background: var(--light); padding: 1.5rem; border-radius: 8px; margin-top: 1rem;">
                    <h4>Dijkstra och min dashboard</h4>
                    <p><strong>Introduktion:</strong> "Jag har gjort en plats där alla kan se hur det går. Precis som Dijkstra hittar den bästa vägen, visar min dashboard allas arbete på bästa sätt. Alla resultat syns direkt."</p>
                    
                    <h4>Algoritmen</h4>
                    <p>"Dijkstra är en algoritm som hittar kortaste vägen mellan punkter. Den används i GPS, internet och spel för att hitta snabbaste vägen."</p>
                    
                    <h4>Min Dashboard</h4>
                    <p>"Jag har byggt tre viktiga delar: Server Monitor, Azure Integration och Python Dashboard. Alla kan se sina resultat direkt på dashboarden!"</p>
                </div>
            </div>
        </section>

        <!-- Team Dashboard Section -->
        <section id="team-dashboard" class="section">
            <div class="section-header">
                <h2>👥 Team Dashboard - Grupp 1</h2>
                <button class="btn" style="background: var(--secondary); color: white;" onclick="showNotification('Team data synced!', 'success')">
                    <i class="fas fa-sync"></i> Sync Team
                </button>
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

            <div style="background: var(--success); color: white; padding: 1rem; border-radius: 8px; margin-top: 1rem;">
                <h4>🎯 Dijkstra Steg-för-Steg:</h4>
                <p>Start: WebServer (avstånd 0)</p>
                <p>Steg 1: Hitta grannar → Database=5, Cache=2</p>
                <p>Steg 2: Välj Cache → hitta CDN=5</p>
                <p>Steg 3: Välj Database → hitta Backup=13</p>
                <p><strong>Resultat: Alla kortaste vägar funna! ✅</strong></p>
            </div>

            <!-- المرفقات المحسنة -->
            <div style="margin-top: 2rem;">
                <h3>📎 المرفقات - Bifogade Filer</h3>
                <div style="display: grid; grid-template-columns: repeat(auto-fit, minmax(280px, 1fr)); gap: 1rem; margin-top: 1rem;">
                    <div class="attachment-item">
                        <h4>📄 Task 02 - Advances in Graph Algorithms</h4>
                        <p>Dokumentation om avancerade Dijkstra-algoritmer</p>
                        <div style="display: flex; gap: 10px; margin-top: 10px;">
                            <button class="btn" style="background: var(--secondary); color: white;">
                                <i class="fas fa-download"></i> Ladda Ned
                            </button>
                            <button class="btn" style="background: var(--success); color: white;">
                                <i class="fas fa-external-link-alt"></i> Öppna
                            </button>
                        </div>
                    </div>
                    <div class="attachment-item">
                        <h4>🌐 Dijkstra Advances Demo</h4>
                        <p>Interaktiv demo av Dijkstra-algoritmen</p>
                        <div style="display: flex; gap: 10px; margin-top: 10px;">
                            <button class="btn" style="background: var(--secondary); color: white;">
                                <i class="fas fa-download"></i> Ladda Ned
                            </button>
                            <button class="btn" style="background: var(--success); color: white;">
                                <i class="fas fa-external-link-alt"></i> Öppna Demo
                            </button>
                        </div>
                    </div>
                </div>
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

                <h4>Marcus - Dijkstra's Shortest Path</h4>
                <p>"The shortest path the algorithm finds is dependant on the source nodes relationships to other nodes. The starting node is where the algorithm draws its conclusion from..."</p>

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
                <div style="background: white; padding: 1.5rem; margin: 1rem 0; border-radius: 10px; border-left: 4px solid var(--success);">
                    <div style="display: flex; justify-content: between; align-items: center; margin-bottom: 0.5rem;">
                        <h4 style="margin: 0;">Dijkstra Dashboard Implementation</h4>
                        <span style="background: var(--success); color: white; padding: 4px 8px; border-radius: 20px; font-size: 0.8rem;">✅ Avslutad</span>
                    </div>
                    <p><strong>Kaled Osman</strong> - Jag har implementerat hela dashboarden med Dijkstra-algoritmen och gjort den responsiv för alla enheter.</p>
                    <small style="color: #666;">Publicerad: 2024-01-15</small>
                </div>

                <div style="background: white; padding: 1.5rem; margin: 1rem 0; border-radius: 10px; border-left: 4px solid var(--warning);">
                    <div style="display: flex; justify-content: between; align-items: center; margin-bottom: 0.5rem;">
                        <h4 style="margin: 0;">OSPF Protocol Research</h4>
                        <span style="background: var(--warning); color: white; padding: 4px 8px; border-radius: 20px; font-size: 0.8rem;">🔄 Pågående</span>
                    </div>
                    <p><strong>Fahad Hussain</strong> - Forskar om OSPF-protokoll och hur Dijkstra används i praktiska nätverkslösningar.</p>
                    <small style="color: #666;">Publicerad: 2024-01-14</small>
                </div>
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
                <div class="user-message">
                    <strong>👤 Du:</strong> Hur fungerar Dijkstra-algoritmen?
                </div>
                <div class="ai-message">
                    <strong>🤖 AI Assistant:</strong> Dijkstra-algoritmen hittar den kortaste vägen mellan noder i en graf med positiva vikter. Den använder en prioritetskö för att alltid expandera den nod med minst kända avstånd!
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
                </button>
            </div>

            <div class="stats-container">
                <div class="stat-card">
                    <div class="stat-number" id="total-tasks">8</div>
                    <div class="stat-label">Totalt Uppgifter</div>
                </div>
                <div class="stat-card">
                    <div class="stat-number" id="completed-tasks">3</div>
                    <div class="stat-label">Avslutade</div>
                </div>
                <div class="stat-card">
                    <div class="stat-number" id="inprogress-tasks">4</div>
                    <div class="stat-label">Pågående</div>
                </div>
                <div class="stat-card">
                    <div class="stat-number" id="pending-tasks">1</div>
                    <div class="stat-label">Väntande</div>
                </div>
            </div>

            <div class="task-list" id="tasks-list">
                <div class="task-card">
                    <div class="task-header">
                        <div class="task-title">Implementera Dijkstra Dashboard</div>
                        <span class="task-priority priority-high">Hög Prioritiet</span>
                    </div>
                    <div class="task-meta">
                        <span><i class="fas fa-user"></i> Kaled Osman</span>
                        <span><i class="fas fa-calendar"></i> 2024-01-20</span>
                    </div>
                    <p>Bygg hela dashboard med Dijkstra visualisering och responsiv design</p>
                    <div class="progress-bar">
                        <div class="progress-fill" style="width: 100%"></div>
                    </div>
                    <div style="display: flex; gap: 10px; margin-top: 10px;">
                        <button class="btn" style="background: var(--success); color: white;">
                            <i class="fas fa-check"></i> Markera klar
                        </button>
                        <button class="btn" style="background: var(--warning); color: white;">
                            <i class="fas fa-edit"></i> Redigera
                        </button>
                    </div>
                </div>

                <div class="task-card">
                    <div class="task-header">
                        <div class="task-title">OSPF Research & Documentation</div>
                        <span class="task-priority priority-medium">Medel Prioritiet</span>
                    </div>
                    <div class="task-meta">
                        <span><i class="fas fa-user"></i> Fahad Hussain</span>
                        <span><i class="fas fa-calendar"></i> 2024-01-25</span>
                    </div>
                    <p>Forskningsarbete om OSPF-protokoll och praktisk tillämpning av Dijkstra</p>
                    <div class="progress-bar">
                        <div class="progress-fill" style="width: 75%"></div>
                    </div>
                    <div style="display: flex; gap: 10px; margin-top: 10px;">
                        <button class="btn" style="background: var(--success); color: white;">
                            <i class="fas fa-check"></i> Markera klar
                        </button>
                        <button class="btn" style="background: var(--warning); color: white;">
                            <i class="fas fa-edit"></i> Redigera
                        </button>
                    </div>
                </div>
            </div>
        </section>

        <!-- JavaScript الكامل -->
        <script>
        // وظائف التنقل بين التبويبات
        document.querySelectorAll('.nav-tab').forEach(tab => {
            tab.addEventListener('click', () => {
                // إزالة النشاط من جميع التبويبات
                document.querySelectorAll('.nav-tab').forEach(t => t.classList.remove('active'));
                document.querySelectorAll('.section').forEach(s => s.classList.remove('active'));
                
                // إضافة النشاط للتبويب المحدد
                tab.classList.add('active');
                document.getElementById(tab.dataset.target).classList.add('active');
            });
        });

        // وظيفة عرض الإشعارات
        function showNotification(message, type = 'success') {
            const notificationContainer = document.getElementById('notificationContainer');
            const notification = document.createElement('div');
            notification.className = `notification ${type}`;
            notification.innerHTML = `
                <span>${message}</span>
                <button class="notification-close" onclick="this.parentElement.remove()">
                    <i class="fas fa-times"></i>
                </button>
            `;
            notificationContainer.appendChild(notification);
            
            // إزالة تلقائية بعد 5 ثواني
            setTimeout(() => {
                if (notification.parentElement) {
                    notification.remove();
                }
            }, 5000);
        }

        // وظائف المساعد الذكي
        function sendAIMessage() {
            const input = document.getElementById('aiChatInput');
            const message = input.value.trim();
            
            if (message) {
                const chatContainer = document.getElementById('aiChatContainer');
                
                // إضافة رسالة المستخدم
                const userMessage = document.createElement('div');
                userMessage.className = 'user-message';
                userMessage.innerHTML = `<strong>👤 Du:</strong> ${message}`;
                chatContainer.appendChild(userMessage);
                
                // محاكاة رد الذكاء الاصطناعي
                setTimeout(() => {
                    const aiResponse = getAIResponse(message);
                    const aiMessage = document.createElement('div');
                    aiMessage.className = 'ai-message';
                    aiMessage.innerHTML = `<strong>🤖 AI Assistant:</strong> ${aiResponse}`;
                    chatContainer.appendChild(aiMessage);
                    chatContainer.scrollTop = chatContainer.scrollHeight;
                }, 1000);
                
                input.value = '';
                chatContainer.scrollTop = chatContainer.scrollHeight;
            }
        }

        function quickQuestion(question) {
            document.getElementById('aiChatInput').value = question;
            sendAIMessage();
        }

        function getAIResponse(question) {
            const responses = {
                'tidskomplexitet': 'Dijkstra har tidskomplexitet O(V^2) med array eller O(E + V log V) med prioritetskö!',
                'nätverk': 'I nätverk används Dijkstra i OSPF-protokoll för att hitta kortaste vägar mellan routrar!',
                'python': 'I Python kan du implementera Dijkstra med heapq-modulen för effektiv prioritetskö!',
                'skillnad': 'Dijkstra hittar kortaste vägar, A* använder heuristik för att vara snabbare i sökning!'
            };
            
            const lowerQuestion = question.toLowerCase();
            for (const [key, response] of Object.entries(responses)) {
                if (lowerQuestion.includes(key)) {
                    return response;
                }
            }
            
            return 'Tack för din fråga om Dijkstra-algoritmen! Jag kan hjälpa dig med implementation, komplexitet eller praktiska användningsfall.';
        }

        // وظائف المهام
        function addNewTask() {
            showNotification('Ny uppgift skapad!', 'success');
        }

        // معالجة النماذج
        document.getElementById('assignment-form')?.addEventListener('submit', function(e) {
            e.preventDefault();
            showNotification('Uppgift tillagd!', 'success');
            this.reset();
        });

        document.getElementById('team-update-form')?.addEventListener('submit', function(e) {
            e.preventDefault();
            showNotification('Uppdatering publicerad!', 'success');
            this.reset();
        });

        // جعل الإدخال يعمل بالزر Enter
        document.getElementById('aiChatInput')?.addEventListener('keypress', function(e) {
            if (e.key === 'Enter') {
                sendAIMessage();
            }
        });

        // تهيئة أولية
        showNotification('Välkommen till Dashboard!', 'info');
        </script>
    </div>
</body>
</html>
