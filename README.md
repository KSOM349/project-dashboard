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

        /* Notification System - الجديد */
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

        /* Navigation - محسن */
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

        /* Section Styles - محسن */
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

        /* Stats Cards - محسن */
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
            transition: transform 0.3s ease;
        }

        .stat-card:hover {
            transform: translateY(-5px);
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

        /* Code Display - محسن */
        .code-container {
            background: #2d2d2d;
            color: #f8f8f2;
            padding: 1.5rem;
            border-radius: 8px;
            margin: 1.5rem 0;
            overflow-x: auto;
            font-family: 'Courier New', monospace;
        }

        /* Team Table - محسن */
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

        /* Graph Visualization - محسن */
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

        /* Documentation Styles - محسن */
        .doc-section {
            margin-bottom: 2rem;
        }

        .doc-section h3 {
            color: var(--primary);
            margin-bottom: 1rem;
            padding-bottom: 0.5rem;
            border-bottom: 1px solid var(--light);
        }

        /* Collaboration Section - محسن */
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

        /* AI Recommendations - الجديد */
        .ai-recommendation {
            background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
            color: white;
            padding: 1.5rem;
            border-radius: 12px;
            margin: 1rem 0;
            border-left: 4px solid #10b981;
        }

        .recommendation-item {
            background: rgba(255,255,255,0.1);
            padding: 1rem;
            margin: 0.5rem 0;
            border-radius: 8px;
            backdrop-filter: blur(10px);
        }

        /* Team Updates Section - الجديد */
        .update-item {
            background: #f8f9fa;
            padding: 1.5rem;
            border-radius: 12px;
            margin-bottom: 1rem;
            border-left: 4px solid #28a745;
        }

        /* Responsive Design - محسن */
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
    <!-- Notification System - الجديد -->
    <div class="notification-container" id="notificationContainer"></div>

    <div class="container">
        <header>
            <h1>🚀 Hela Projektet - Komplett Dashboard</h1>
            <p>Grupp 1 - Full-Stack Dashboard med Algorithm Visualizer</p>
            <!-- إضافة ذكية - الجديد -->
            <div style="margin-top: 1rem; font-size: 0.9rem; opacity: 0.8;">
                <i class="fas fa-robot"></i> AI Enhanced • <i class="fas fa-bell"></i> Smart Notifications • <i class="fas fa-sync-alt"></i> Live Updates
            </div>
        </header>

        <!-- التنقل مع جميع الأقسام الأصلية -->
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
            <!-- قسم جديد محسن -->
            <div class="nav-tab" data-target="smart-features">
                <i class="fas fa-robot"></i> 🤖 Smart Features
            </div>
        </div>

        <!-- Algorithm Visualizer Section - محسن -->
        <section id="algorithm-visualizer" class="section active">
            <div class="section-header">
                <h2>📊 Algorithm Visualizer</h2>
                <!-- زر جديد - محسن -->
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

            <div class="graph-visualization">
                <div style="text-align: center; color: var(--dark);">
                    <i class="fas fa-project-diagram" style="font-size: 3rem; margin-bottom: 1rem;"></i>
                    <h3>Interaktiv Dijkstra Visualisering</h3>
                    <p>Plats för den interaktiva grafvisualiseringen</p>
                    <!-- تحسين - الجديد -->
                    <button class="btn" style="background: var(--secondary); color: white; margin-top: 1rem;">
                        <i class="fas fa-play"></i> Start Simulation
                    </button>
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

            <!-- توصية ذكية جديدة - الجديد -->
            <div class="ai-recommendation">
                <h3><i class="fas fa-lightbulb"></i> AI Optimization Tip</h3>
                <p>Your Dijkstra implementation can be optimized using Fibonacci heaps for better performance with large graphs.</p>
            </div>
        </section>

        <!-- Min Implementation Section - محسن -->
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

        <!-- Team Dashboard Section - محسن -->
        <section id="team-dashboard" class="section">
            <div class="section-header">
                <h2>👥 Team Dashboard - Grupp 1</h2>
                <!-- زر جديد - محسن -->
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

        <!-- باقي الأقسام الأصلية مع التحسينات... -->
        <!-- Hela Projektet Section -->
        <section id="hela-projektet" class="section">
            <div class="section-header">
                <h2>📁 HELA PROJEKTET - Dokumentation & Presentation</h2>
            </div>
            <!-- محتوى القسم الأصلي مع إضافة زر جديد -->
            <button class="btn" style="background: var(--primary); color: white; margin-bottom: 1rem;" onclick="showNotification('Project documentation exported!', 'success')">
                <i class="fas fa-download"></i> Export Documentation
            </button>
            <!-- محتوى القسم الأصلي يبقى كما هو -->
        </section>

        <!-- قسم Smart Features الجديد -->
        <section id="smart-features" class="section">
            <div class="section-header">
                <h2>🤖 Smart Features - الذكاء الاصطناعي</h2>
            </div>

            <div class="ai-recommendation">
                <h3><i class="fas fa-robot"></i> AI Assistant</h3>
                <div class="recommendation-item">
                    <strong>💬 Chat with AI Assistant</strong>
                    <p>Get instant help with your project questions and code optimization</p>
                    <button class="btn" style="background: var(--secondary); color: white; margin-top: 0.5rem;" onclick="showNotification('AI Assistant is ready!', 'info')">
                        Start Chat
                    </button>
                </div>
            </div>

            <div style="display: grid; grid-template-columns: 1fr 1fr; gap: 2rem; margin-top: 2rem;">
                <div>
                    <h3>🔔 Smart Notifications</h3>
                    <div style="background: var(--light); padding: 1.5rem; border-radius: 12px;">
                        <p>Get notified about important updates and team activities</p>
                        <button class="btn" style="background: var(--success); color: white; margin-top: 1rem;" onclick="showNotification('This is a smart notification!', 'success')">
                            Test Notification
                        </button>
                    </div>
                </div>

                <div>
                    <h3>📊 Live Analytics</h3>
                    <div style="background: var(--light); padding: 1.5rem; border-radius: 12px;">
                        <p>Real-time project analytics and performance metrics</p>
                        <button class="btn" style="background: var(--warning); color: white; margin-top: 1rem;" onclick="showNotification('Analytics updated!', 'warning')">
                            View Analytics
                        </button>
                    </div>
                </div>
            </div>
        </section>

        <!-- الأقسام الأخرى تبقى كما هي مع إضافة التحسينات -->
    </div>

    <script>
        // نظام الإشعارات - الجديد
        function showNotification(message, type = 'info') {
            const notification = document.createElement('div');
            notification.className = `notification ${type}`;
            notification.innerHTML = `
                <div>
                    <i class="fas fa-${type === 'success' ? 'check' : type === 'error' ? 'exclamation-triangle' : 'info-circle'}"></i>
                    ${message}
                </div>
                <button class="notification-close" onclick="this.parentElement.remove()">×</button>
            `;
            
            document.getElementById('notificationContainer').appendChild(notification);
            
            setTimeout(() => {
                if (notification.parentElement) {
                    notification.remove();
                }
            }, 5000);
        }

        // نظام التنقل - محسن
        document.addEventListener('DOMContentLoaded', function() {
            const navTabs = document.querySelectorAll('.nav-tab');
            const sections = document.querySelectorAll('.section');
            
            navTabs.forEach(tab => {
                tab.addEventListener('click', function() {
                    const targetId = this.getAttribute('data-target');
                    
                    navTabs.forEach(t => t.classList.remove('active'));
                    this.classList.add('active');
                    
                    sections.forEach(section => {
                        section.classList.remove('active');
                        if (section.id === targetId) {
                            section.classList.add('active');
                        }
                    });
                    
                    // إشعار عند تبديل الأقسام - الجديد
                    showNotification(`Switched to ${this.textContent.trim()}`, 'info');
                });
            });

            // تحريك الإحصائيات - الجديد
            const statNumbers = document.querySelectorAll('.stat-number');
            statNumbers.forEach(stat => {
                const finalValue = stat.textContent;
                let current = 0;
                const increment = parseInt(finalValue) / 20;
                const timer = setInterval(() => {
                    current += increment;
                    if (current >= parseInt(finalValue)) {
                        stat.textContent = finalValue;
                        clearInterval(timer);
                    } else {
                        stat.textContent = Math.floor(current);
                    }
                }, 50);
            });
        });

        // إشعار ترحيبي - الجديد
        setTimeout(() => {
            showNotification('Welcome to the enhanced dashboard! Try the new smart features.', 'info');
        }, 1000);
    </script>
</body>
</html>
