<!DOCTYPE html>
<html lang="sv">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Dijkstra Dashboard - Grupp 1</title>
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
        }

        body {
            background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
            min-height: 100vh;
            padding: 20px;
        }

        .dashboard-container {
            max-width: 1200px;
            margin: 0 auto;
            background: white;
            border-radius: 15px;
            box-shadow: 0 20px 40px rgba(0,0,0,0.1);
            overflow: hidden;
        }

        .header {
            background: linear-gradient(135deg, #2c3e50 0%, #3498db 100%);
            color: white;
            padding: 30px;
            text-align: center;
        }

        .header h1 {
            font-size: 2.5em;
            margin-bottom: 10px;
        }

        .nav {
            background: #34495e;
            padding: 15px;
            display: flex;
            justify-content: center;
            gap: 20px;
        }

        .nav button {
            background: #3498db;
            color: white;
            border: none;
            padding: 12px 25px;
            border-radius: 25px;
            cursor: pointer;
            font-size: 1em;
            transition: all 0.3s ease;
        }

        .nav button:hover {
            background: #2980b9;
            transform: translateY(-2px);
        }

        .main-content {
            padding: 30px;
            display: grid;
            grid-template-columns: 1fr 1fr;
            gap: 30px;
        }

        .card {
            background: #f8f9fa;
            border-radius: 10px;
            padding: 25px;
            border-left: 5px solid #3498db;
        }

        .card h2 {
            color: #2c3e50;
            margin-bottom: 20px;
            font-size: 1.5em;
        }

        .algorithm-visualizer {
            grid-column: 1 / -1;
        }

        .code-example {
            background: #2c3e50;
            color: #ecf0f1;
            padding: 20px;
            border-radius: 8px;
            margin: 15px 0;
            font-family: 'Courier New', monospace;
            overflow-x: auto;
        }

        .stats-grid {
            display: grid;
            grid-template-columns: repeat(3, 1fr);
            gap: 20px;
            margin: 20px 0;
        }

        .stat-card {
            background: white;
            padding: 20px;
            border-radius: 10px;
            text-align: center;
            box-shadow: 0 5px 15px rgba(0,0,0,0.1);
            border-top: 4px solid #3498db;
        }

        .stat-card h3 {
            color: #2c3e50;
            font-size: 2em;
            margin-bottom: 10px;
        }

        .team-table {
            width: 100%;
            border-collapse: collapse;
            margin-top: 20px;
        }

        .team-table th,
        .team-table td {
            padding: 12px;
            text-align: left;
            border-bottom: 1px solid #ddd;
        }

        .team-table th {
            background: #34495e;
            color: white;
        }

        .team-table tr:hover {
            background: #f5f5f5;
        }

        .btn {
            padding: 8px 15px;
            border: none;
            border-radius: 5px;
            cursor: pointer;
            margin: 0 5px;
        }

        .btn-edit {
            background: #f39c12;
            color: white;
        }

        .btn-delete {
            background: #e74c3c;
            color: white;
        }

        .demo-section {
            background: #e8f4fc;
            padding: 20px;
            border-radius: 8px;
            margin: 15px 0;
        }

        .step-visualization {
            background: #2c3e50;
            color: white;
            padding: 15px;
            border-radius: 8px;
            margin: 10px 0;
        }
    </style>
</head>
<body>
    <div class="dashboard-container">
        <div class="header">
            <h1>🚀 Dijkstra Algorithm Dashboard</h1>
            <p>Grupp 1 - Professionell Visualisering av Kortaste Väg Algoritmen</p>
        </div>

        <nav class="nav">
            <button onclick="showSection('algorithms')">📊 Algorithm Visualizer</button>
            <button onclick="showSection('team')">👥 Team Dashboard</button>
            <button onclick="showSection('implementation')">💻 Min Implementation</button>
        </nav>

        <div class="main-content">
            <!-- ALGORITHM VISUALIZER SECTION -->
            <div id="algorithms" class="section algorithm-visualizer">
                <div class="card">
                    <h2>🔍 Dijkstra Algorithm - Steg för Steg</h2>
                    
                    <div class="stats-grid">
                        <div class="stat-card">
                            <h3>5</h3>
                            <p>Servrar i Nätverk</p>
                        </div>
                        <div class="stat-card">
                            <h3>13</h3>
                            <p>Kortaste Väg (ms)</p>
                        </div>
                        <div class="stat-card">
                            <h3>100%</h3>
                            <p>Optimering</p>
                        </div>
                    </div>

                    <div class="code-example">
                        <h4>Vårt Server-Nätverk:</h4>
                        <pre>servers = {
    'WebServer': {'Database': 5, 'Cache': 2},
    'Database': {'Backup': 8, 'WebServer': 5},
    'Cache': {'CDN': 3, 'WebServer': 2},
    'CDN': {'Cache': 3},
    'Backup': {'Database': 8}
}</pre>
                    </div>

                    <div class="demo-section">
                        <h4>🎯 Dijkstra Steg-för-Steg:</h4>
                        <div class="step-visualization">
                            <p><strong>Start:</strong> WebServer (avstånd 0)</p>
                            <p><strong>Steg 1:</strong> Hitta grannar → Database=5, Cache=2</p>
                            <p><strong>Steg 2:</strong> Välj Cache → hitta CDN=5</p>
                            <p><strong>Steg 3:</strong> Välj Database → hitta Backup=13</p>
                            <p><strong>Resultat:</strong> Alla kortaste vägar funna! ✅</p>
                        </div>
                    </div>
                </div>
            </div>

            <!-- TEAM DASHBOARD SECTION -->
            <div id="team" class="section" style="display: none;">
                <div class="card">
                    <h2>👥 Team Dashboard - Grupp 1</h2>
                    
                    <div class="stats-grid">
                        <div class="stat-card">
                            <h3>6</h3>
                            <p>Team Medlemmar</p>
                        </div>
                        <div class="stat-card">
                            <h3>100%</h3>
                            <p>Projekt Framsteg</p>
                        </div>
                        <div class="stat-card">
                            <h3>5</h3>
                            <p>Implementerade Algoritmer</p>
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
                </div>
            </div>

            <!-- IMPLEMENTATION SECTION -->
            <div id="implementation" class="section" style="display: none;">
                <div class="card">
                    <h2>💻 Min Implementation - Kaled Osman</h2>
                    
                    <div class="code-example">
                        <h4>Dijkstra Python Implementation:</h4>
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

                    <div class="demo-section">
                        <h4>🎯 Praktisk Användning:</h4>
                        <ul>
                            <li>✅ Optimera datavägar mellan servrar</li>
                            <li>✅ Snabbare åtkomst för alla teammedlemmar</li>
                            <li>✅ Live-övervakning av nätverksprestanda</li>
                            <li>✅ Effektiv resursanvändning i molnet</li>
                        </ul>
                    </div>
                </div>
            </div>
        </div>
    </div>

    <script>
        function showSection(sectionName) {
            // Dölj alla sektioner
            document.querySelectorAll('.section').forEach(section => {
                section.style.display = 'none';
            });
            
            // Visa vald sektion
            document.getElementById(sectionName).style.display = 'block';
        }

        // Visa algoritm-sektionen som standard
        showSection('algorithms');
    </script>
</body>
</html>
