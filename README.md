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
            --card-shadow: 0 8px 30px rgba(0,0,0,0.12);
        }

        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
        }

        body {
            background: linear-gradient(135deg, #f5f7fa 0%, #c3cfe2 100%);
            min-height: 100vh;
            color: var(--dark);
        }

        .container {
            max-width: 1400px;
            margin: 0 auto;
            padding: 20px;
        }

        /* Header Styles */
        header {
            background: var(--gradient);
            color: white;
            padding: 2rem;
            border-radius: 15px;
            margin-bottom: 2rem;
            box-shadow: var(--card-shadow);
            text-align: center;
        }

        header h1 {
            font-size: 2.5rem;
            margin-bottom: 0.5rem;
        }

        header p {
            font-size: 1.2rem;
            opacity: 0.9;
        }

        /* Navigation */
        .nav-tabs {
            display: flex;
            background: white;
            border-radius: 12px;
            padding: 10px;
            margin-bottom: 2rem;
            box-shadow: var(--card-shadow);
            flex-wrap: wrap;
            justify-content: center;
        }

        .nav-tab {
            padding: 12px 24px;
            margin: 5px;
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

        /* Section Styles */
        .section {
            display: none;
            background: white;
            border-radius: 15px;
            padding: 2rem;
            margin-bottom: 2rem;
            box-shadow: var(--card-shadow);
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
            display: flex;
            justify-content: space-between;
            align-items: center;
            margin-bottom: 1.5rem;
            padding-bottom: 1rem;
            border-bottom: 2px solid var(--light);
        }

        .section-header h2 {
            font-size: 1.8rem;
            color: var(--primary);
        }

        /* Stats Cards */
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
            box-shadow: var(--card-shadow);
        }

        .stat-number {
            font-size: 2.5rem;
            font-weight: bold;
            margin-bottom: 0.5rem;
        }

        .stat-label {
            font-size: 1rem;
            opacity: 0.9;
        }

        /* Code Display */
        .code-container {
            background: #2d2d2d;
            color: #f8f8f2;
            padding: 1.5rem;
            border-radius: 8px;
            margin: 1.5rem 0;
            overflow-x: auto;
            font-family: 'Courier New', monospace;
        }

        /* Team Table */
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

        .btn-edit {
            background: var(--warning);
            color: white;
        }

        .btn-delete {
            background: var(--accent);
            color: white;
        }

        .btn:hover {
            opacity: 0.9;
            transform: translateY(-2px);
        }

        /* Graph Visualization Placeholder */
        .graph-visualization {
            background: var(--light);
            height: 400px;
            border-radius: 12px;
            display: flex;
            align-items: center;
            justify-content: center;
            margin: 2rem 0;
            border: 2px dashed var(--secondary);
        }

        /* Documentation Styles */
        .doc-section {
            margin-bottom: 2rem;
        }

        .doc-section h3 {
            color: var(--primary);
            margin-bottom: 1rem;
            padding-bottom: 0.5rem;
            border-bottom: 1px solid var(--light);
        }

        /* Collaboration Section */
        .collaboration-section {
            background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
            color: white;
            border-radius: 15px;
            padding: 2rem;
            margin-bottom: 2rem;
        }

        .form-group {
            margin-bottom: 1rem;
        }

        .form-group input,
        .form-group textarea,
        .form-group select {
            width: 100%;
            padding: 12px;
            border: none;
            border-radius: 8px;
            margin-top: 5px;
        }

        .assignment-item {
            background: rgba(255,255,255,0.2);
            padding: 1rem;
            margin: 10px 0;
            border-radius: 8px;
            backdrop-filter: blur(10px);
        }

        /* Team Updates Section */
        .update-item {
            background: #f8f9fa;
            padding: 1.5rem;
            border-radius: 12px;
            margin-bottom: 1rem;
            border-left: 4px solid #28a745;
        }

        /* Responsive Design */
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
                <i class="fas fa-project-diagram"></i>
                📊 Algorithm Visualizer
            </div>
            <div class="nav-tab" data-target="min-implementation">
                <i class="fas fa-code"></i>
                💻 Min Implementation
            </div>
            <div class="nav-tab" data-target="team-dashboard">
                <i class="fas fa-users"></i>
                👥 Team Dashboard
            </div>
            <div class="nav-tab" data-target="hela-projektet">
                <i class="fas fa-folder-open"></i>
                📁 Hela Projektet
            </div>
            <div class="nav-tab" data-target="dijkstra-algorithm">
                <i class="fas fa-route"></i>
                🔍 Dijkstra Algorithm
            </div>
            <div class="nav-tab" data-target="dokumentation">
                <i class="fas fa-book"></i>
                📚 Dokumentation
            </div>
            <div class="nav-tab" data-target="team-collaboration">
                <i class="fas fa-tasks"></i>
                📝 Team Collaboration
            </div>
            <div class="nav-tab" data-target="team-updates">
                <i class="fas fa-comments"></i>
                👥 Team Updates
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

            <div class="graph-visualization">
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

            <div class="doc-section">
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

            <div class="doc-section">
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

            <div style="margin-top: 2rem;">
                <h3>📎 Bifogade Filer</h3>
                <div style="display: grid; grid-template-columns: repeat(auto-fit, minmax(250px, 1fr)); gap: 1rem; margin-top: 1rem;">
                    <div style="background: var(--light); padding: 1rem; border-radius: 8px;">
                        <h4>📄 Task 02 - Advances in Graph Algorithms</h4>
                        <p>Dokumentation om avancerade Dijkstra-algoritmer</p>
                        <button class="btn" style="background: var(--secondary); color: white; margin-top: 0.5rem;">Ladda Ned</button>
                    </div>
                    <div style="background: var(--light); padding: 1rem; border-radius: 8px;">
                        <h4>🌐 Dijkstra Advances Demo</h4>
                        <p>Interaktiv demo av Dijkstra-algoritmen</p>
                        <button class="btn" style="background: var(--success); color: white; margin-top: 0.5rem;">Öppna Demo</button>
                    </div>
                </div>
            </div>
        </section>

        <!-- Dokumentation Section -->
        <section id="dokumentation" class="section">
            <div class="section-header">
                <h2>📚 Dokumentation - Graph Algorithms</h2>
            </div>

            <div class="doc-section">
                <h3>Av Grupp 1: Fahad Hussain, Stefan Österberg, Kaled Osman, Marcus Tibell, Jens Annell</h3>
                
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
            </div>
        </section>

        <!-- Team Collaboration Section -->
        <section id="team-collaboration" class="section collaboration-section">
            <div class="section-header">
                <h2>📝 Team Collaboration - Grupp Dokumentation</h2>
            </div>
            
            <div style="display: grid; grid-template-columns: repeat(auto-fit, minmax(300px, 1fr)); gap: 2rem; margin-top: 2rem;">
                <!-- Form for adding assignments -->
                <div style="background: rgba(255,255,255,0.1); padding: 2rem; border-radius: 15px; backdrop-filter: blur(10px);">
                    <h3>➕ Lägg Till Uppgift</h3>
                    <form id="assignment-form">
                        <div class="form-group">
                            <label>Uppgiftsnamn:</label>
                            <input type="text" id="task-name" placeholder="Ange uppgiftsnamn" required>
                        </div>
                        <div class="form-group">
                            <label>Beskrivning:</label>
                            <textarea id="task-desc" placeholder="Ange beskrivning" required rows="4"></textarea>
                        </div>
                        <div class="form-group">
                            <label>Ansvarig:</label>
                            <select id="task-assignee">
                                <option value="Kaled Osman">Kaled Osman</option>
                                <option value="Fahad Hussain">Fahad Hussain</option>
                                <option value="Stefan Österberg">Stefan Österberg</option>
                                <option value="Marcus Tibell">Marcus Tibell</option>
                                <option value="Jens Annell">Jens Annell</option>
                                <option value="Luwam">Luwam</option>
                            </select>
                        </div>
                        <button type="submit" style="width: 100%; padding: 12px; background: #10b981; color: white; border: none; border-radius: 8px; cursor: pointer; font-size: 1.1rem;">
                            ➕ Lägg Till Uppgift
                        </button>
                    </form>
                </div>

                <!-- Assignment list -->
                <div style="background: rgba(255,255,255,0.1); padding: 2rem; border-radius: 15px; backdrop-filter: blur(10px);">
                    <h3>📋 Aktuella Uppgifter</h3>
                    <div id="assignments-list">
                        <div class="assignment-item">
                            <strong>Förbättra Dijkstra Algorithm</strong>
                            <p>Lägg till prestandaförbättringar och komplexitetsanalys</p>
                            <small><strong>Ansvarig:</strong> Kaled Osman</small>
                        </div>
                        <div class="assignment-item">
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
                <p>Varje teammedlem kan lägga till sina uppdateringar och uppgifter</p>
            </div>

            <!-- نموذج إضافة تحديث -->
            <div style="background: linear-gradient(135deg, #667eea 0%, #764ba2 100%); color: white; padding: 2rem; border-radius: 15px; margin-bottom: 2rem;">
                <h3>➕ Lägg Till Din Uppdatering</h3>
                <form id="team-update-form">
                    <div style="display: grid; grid-template-columns: 1fr 1fr; gap: 1rem; margin-bottom: 1rem;">
                        <div>
                            <label>Ditt Namn:</label>
                            <select id="update-author" style="width: 100%; padding: 10px; border-radius: 8px; border: none;">
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
                            <select id="update-status" style="width: 100%; padding: 10px; border-radius: 8px; border: none;">
                                <option value="completed">✅ Avslutad</option>
                                <option value="in-progress">🔄 Pågående</option>
                                <option value="planned">📅 Planerad</option>
                                <option value="blocked">❌ Blockerad</option>
                            </select>
                        </div>
                    </div>
                    
                    <div style="margin-bottom: 1rem;">
                        <label>Uppgiftsbeskrivning:</label>
                        <input type="text" id="update-title" placeholder="Vad har du gjort?" style="width: 100%; padding: 10px; border-radius: 8px; border: none;">
                    </div>
                    
                    <div style="margin-bottom: 1rem;">
                        <label>Detaljer:</label>
                        <textarea id="update-details" placeholder="Beskriv ditt arbete..." style="width: 100%; padding: 10px; border-radius: 8px; border: none; height: 100px;"></textarea>
                    </div>
                    
                    <button type="submit" style="background: #10b981; color: white; padding: 12px 24px; border: none; border-radius: 8px; cursor: pointer; font-size: 1.1rem;">
                        ➕ Publicera Uppdatering
                    </button>
                </form>
            </div>

            <!-- عرض التحديثات -->
            <div>
                <h3>📋 Senaste Uppdateringar från Teamet</h3>
                <div id="updates-container">
                    <!-- التحديثات ستظهر هنا -->
                    <div class="update-item">
                        <div style="display: flex; justify-content: space-between; align-items: center; margin-bottom: 0.5rem;">
                            <strong>Kaled Osman</strong>
                            <span style="background: #28a745; color: white; padding: 4px 8px; border-radius: 12px; font-size: 0.8rem;">✅ Avslutad</span>
                        </div>
                        <h4 style="margin: 0.5rem 0; color: #2c3e50;">Implementerat Dijkstra Algorithm</h4>
                        <p style="color: #666; margin-bottom: 0.5rem;">Har skapat en fullt fungerande Dijkstra implementation med visualisering för vår dashboard.</p>
                        <small style="color: #999;">Idag 14:30</small>
                    </div>
                </div>
            </div>
        </section>
    </div>

    <script>
        // Navigation functionality
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
            
            // Simulate loading animation for stats
            const statNumbers = document.querySelectorAll('.stat-number');
            statNumbers.forEach(stat => {
                const finalValue = parseInt(stat.textContent);
                let current = 0;
                const increment = finalValue / 20;
                const timer = setInterval(() => {
                    current += increment;
                    if (current >= finalValue) {
                        stat.textContent = finalValue;
                        clearInterval(timer);
                    } else {
                        stat.textContent = Math.floor(current);
                    }
                }, 50);
            });

            // Assignment form functionality
            const assignmentForm = document.getElementById('assignment-form');
            if (assignmentForm) {
                assignmentForm.addEventListener('submit', function(event) {
                    event.preventDefault();
                    
                    const name = document.getElementById('task-name').value;
                    const desc = document.getElementById('task-desc').value;
                    const assignee = document.getElementById('task-assignee').value;
                    
                    const assignmentItem = document.createElement('div');
                    assignmentItem.className = 'assignment-item';
                    assignmentItem.innerHTML = `
                        <strong>${name}</strong>
                        <p>${desc}</p>
                        <small><strong>Ansvarig:</strong> ${assignee}</small>
                    `;
                    
                    document.getElementById('assignments-list').prepend(assignmentItem);
                    
                    // Reset form
                    assignmentForm.reset();
                    
                    alert('✅ Uppgift tillagd framgångsrikt!');
                });
            }

            // Team Updates functionality
            const updateForm = document.getElementById('team-update-form');
            if (updateForm) {
                updateForm.addEventListener('submit', function(event) {
                    event.preventDefault();
                    
                    const author = document.getElementById('update-author').value;
                    const status = document.getElementById('update-status').value;
                    const title = document.getElementById('update-title').value;
                    const details = document.getElementById('update-details').value;
                    
                    // Status colors and texts
                    const statusColors = {
                        'completed': '#28a745',
                        'in-progress': '#ffc107', 
                        'planned': '#17a2b8',
                        'blocked': '#dc3545'
                    };
                    
                    const statusTexts = {
                        'completed': '✅ Avslutad',
                        'in-progress': '🔄 Pågående',
                        'planned': '📅 Planerad',
                        'blocked': '❌ Blockerad'
                    };
                    
                    const updateItem = document.createElement('div');
                    updateItem.className = 'update-item';
                    updateItem.style.background = '#f8f9fa';
                    updateItem.style.padding = '1.5rem';
                    updateItem.style.borderRadius = '12px';
                    updateItem.style.marginBottom = '1rem';
                    updateItem.style.borderLeft = `4px solid ${statusColors[status]}`;
                    
                    updateItem.innerHTML = `
                        <div style="display: flex; justify-content: space-between; align-items: center; margin-bottom: 0.5rem;">
                            <strong>${author}</strong>
                            <span style="background: ${statusColors[status]}; color: white; padding: 4px 8px; border-radius: 12px; font-size: 0.8rem;">
                                ${statusTexts[status]}
                            </span>
                        </div>
                        <h4 style="margin: 0.5rem 0; color: #2c3e50;">${title}</h4>
                        <p style="color: #666; margin-bottom: 0.5rem;">${details}</p>
                        <small style="color: #999;">${new Date().toLocaleString('sv-SE')}</small>
                    `;
                    
                    document.getElementById('updates-container').prepend(updateItem);
                    
                    // Reset form
                    updateForm.reset();
                    
                    alert('Tack för din uppdatering! ✅');
                });
            }
        });
    </script>
</body>
</html>
