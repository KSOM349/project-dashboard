<!DOCTYPE html>
<html lang="sv" dir="ltr">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Hela Projektet - Komplett Dashboard</title>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <script src="https://cdn.tailwindcss.com"></script>
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

        /* NYA FÖRBÄTTRINGAR */
        .online-indicator {
            width: 8px;
            height: 8px;
            background: #10B981;
            border-radius: 50%;
            animation: pulse 2s infinite;
        }

        @keyframes pulse {
            0% { transform: scale(1); opacity: 1; }
            50% { transform: scale(1.2); opacity: 0.7; }
            100% { transform: scale(1); opacity: 1; }
        }

        .team-member-card {
            display: flex;
            align-items: center;
            gap: 12px;
            padding: 10px;
            background: white;
            border-radius: 8px;
            margin-bottom: 8px;
            box-shadow: 0 2px 4px rgba(0,0,0,0.1);
        }

        .member-avatar {
            width: 32px;
            height: 32px;
            border-radius: 50%;
            background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
            display: flex;
            align-items: center;
            justify-content: center;
            color: white;
            font-weight: bold;
            font-size: 14px;
        }

        .add-content-btn {
            background: #10B981;
            color: white;
            border: none;
            padding: 10px 20px;
            border-radius: 8px;
            cursor: pointer;
            font-weight: 600;
            transition: all 0.3s ease;
        }

        .add-content-btn:hover {
            background: #059669;
            transform: translateY(-2px);
        }
    </style>
</head>
<body>
    <div class="container">
        <header>
            <h1>🚀 Hela Projektet - Komplett Dashboard</h1>
            <p>Grupp 1 - Full-Stack Dashboard med Algorithm Visualizer</p>
            
            <!-- LIVE STATUS -->
            <div style="background: rgba(255,255,255,0.2); padding: 1rem; border-radius: 10px; margin-top: 1rem; backdrop-filter: blur(10px);">
                <div style="display: flex; align-items: center; justify-content: center; gap: 10px;">
                    <span class="online-indicator"></span>
                    <strong>LIVE UPDATES ACTIVE</strong>
                    <span class="online-indicator"></span>
                </div>
                <p style="margin-top: 5px; font-size: 0.9rem;">Real-time team collaboration enabled</p>
            </div>
        </header>

        <!-- TEAM MEMBERS SIDEBAR -->
        <div style="display: grid; grid-template-columns: 300px 1fr; gap: 2rem; margin-bottom: 2rem;">
            <!-- Sidebar -->
            <div style="background: white; border-radius: 15px; padding: 1.5rem; box-shadow: var(--card-shadow); height: fit-content;">
                <h3 style="color: var(--primary); margin-bottom: 1rem; display: flex; align-items: center; gap: 8px;">
                    <span class="online-indicator"></span>
                    Team Members
                </h3>
                
                <div id="liveTeamList">
                    <!-- Team members will be loaded here -->
                </div>

                <!-- Quick Actions -->
                <div style="margin-top: 2rem; padding-top: 1rem; border-top: 2px solid var(--light);">
                    <h4 style="color: var(--primary); margin-bottom: 1rem;">Quick Actions</h4>
                    <button class="add-content-btn" style="width: 100%; margin-bottom: 0.5rem;" onclick="showAddContentModal()">
                        <i class="fas fa-plus"></i> Add Content
                    </button>
                    <button class="add-content-btn" style="width: 100%; background: #3B82F6;" onclick="showTeamChat()">
                        <i class="fas fa-comments"></i> Team Chat
                    </button>
                </div>
            </div>

            <!-- Main Content -->
            <div>
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
                        <button class="add-content-btn" onclick="openAddContent('algorithm-visualizer')">
                            <i class="fas fa-plus"></i> Add Content
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

                    <div id="algorithm-content" class="space-y-4">
                        <!-- Dynamic content will appear here -->
                    </div>
                </section>

                <!-- Min Implementation Section -->
                <section id="min-implementation" class="section">
                    <div class="section-header">
                        <h2>💻 Min Implementation - Kaled Osman</h2>
                        <button class="add-content-btn" onclick="openAddContent('min-implementation')">
                            <i class="fas fa-plus"></i> Add Content
                        </button>
                    </div>

                    <div id="implementation-content">
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
                    </div>
                </section>

                <!-- Ytterligare sektioner... (samma struktur med Add Content knappar) -->

            </div>
        </div>
    </div>

    <!-- MODALS -->
    <div id="addContentModal" style="display: none; position: fixed; top: 0; left: 0; width: 100%; height: 100%; background: rgba(0,0,0,0.5); z-index: 1000; align-items: center; justify-content: center;">
        <div style="background: white; padding: 2rem; border-radius: 15px; max-width: 500px; width: 90%;">
            <h3 style="margin-bottom: 1rem;">Add New Content</h3>
            <form id="addContentForm">
                <input type="hidden" id="contentSection">
                <div class="form-group">
                    <label>Title:</label>
                    <input type="text" id="contentTitle" required>
                </div>
                <div class="form-group">
                    <label>Content:</label>
                    <textarea id="contentDescription" required rows="4"></textarea>
                </div>
                <div class="form-group">
                    <label>Author:</label>
                    <select id="contentAuthor">
                        <option value="Kaled Osman">Kaled Osman</option>
                        <option value="Fahad Hussain">Fahad Hussain</option>
                        <option value="Stefan Österberg">Stefan Österberg</option>
                        <option value="Marcus Tibell">Marcus Tibell</option>
                        <option value="Jens Annell">Jens Annell</option>
                        <option value="Luwam">Luwam</option>
                    </select>
                </div>
                <div style="display: flex; gap: 10px; margin-top: 1rem;">
                    <button type="submit" class="add-content-btn" style="flex: 1;">Add Content</button>
                    <button type="button" onclick="closeAddContent()" style="flex: 1; background: #6B7280; color: white; border: none; padding: 10px; border-radius: 8px; cursor: pointer;">Cancel</button>
                </div>
            </form>
        </div>
    </div>

    <script>
        // TEAM MEMBERS DATA
        const teamMembers = [
            { id: '1', name: 'You', role: 'Team Member' },
            { id: '2', name: 'Kaled Osman', role: 'Developer' },
            { id: '3', name: 'Fahad Hussain', role: 'Researcher' },
            { id: '4', name: 'Stefan Österberg', role: 'Architect' },
            { id: '5', name: 'Marcus Tibell', role: 'Engineer' },
            { id: '6', name: 'Jens Annell', role: 'Analyst' },
            { id: '7', name: 'Luwam', role: 'Designer' }
        ];

        // CONTENT MANAGEMENT
        let userContents = JSON.parse(localStorage.getItem('userContents')) || {};

        // Initialize when page loads
        document.addEventListener('DOMContentLoaded', function() {
            initializeNavigation();
            showTeamMembers();
            initializeDefaultContent();
            initializeAllContents();
        });

        function initializeNavigation() {
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
        }

        function showTeamMembers() {
            const liveTeamList = document.getElementById('liveTeamList');
            if (!liveTeamList) return;

            liveTeamList.innerHTML = '';
            
            teamMembers.forEach(member => {
                const memberElement = document.createElement('div');
                memberElement.className = 'team-member-card';
                memberElement.innerHTML = `
                    <div class="member-avatar">${member.name.charAt(0)}</div>
                    <div style="flex: 1;">
                        <div style="font-weight: 600;">${member.name}</div>
                        <div style="font-size: 0.8rem; color: #666;">${member.role}</div>
                    </div>
                    <span class="online-indicator"></span>
                `;
                liveTeamList.appendChild(memberElement);
            });
        }

        function initializeDefaultContent() {
            const defaultContent = {
                'algorithm-visualizer': [
                    {
                        id: '1',
                        title: '🚀 Welcome to Algorithm Visualizer',
                        description: 'This is where we visualize Dijkstra algorithm step by step.',
                        author: 'System',
                        date: new Date().toISOString()
                    }
                ]
            };

            Object.keys(defaultContent).forEach(section => {
                if (!userContents[section] || userContents[section].length === 0) {
                    userContents[section] = defaultContent[section];
                }
            });
            
            saveContents();
        }

        function saveContents() {
            localStorage.setItem('userContents', JSON.stringify(userContents));
        }

        function openAddContent(section) {
            document.getElementById('contentSection').value = section;
            document.getElementById('addContentModal').style.display = 'flex';
        }

        function closeAddContent() {
            document.getElementById('addContentModal').style.display = 'none';
        }

        function initializeAllContents() {
            // Initialize content for all sections
            Object.keys(userContents).forEach(section => {
                renderSectionContents(section);
            });
        }

        function renderSectionContents(section) {
            const container = document.getElementById(`${section}-content`);
            if (!container) return;
            
            if (!userContents[section] || userContents[section].length === 0) {
                container.innerHTML = '<p>No content yet. Add some content!</p>';
                return;
            }
            
            userContents[section].forEach(content => {
                const contentElement = document.createElement('div');
                contentElement.style.background = '#f8f9fa';
                contentElement.style.padding = '1rem';
                contentElement.style.borderRadius = '8px';
                contentElement.style.marginBottom = '1rem';
                contentElement.innerHTML = `
                    <h4>${content.title}</h4>
                    <p>${content.description}</p>
                    <small>By ${content.author} on ${new Date(content.date).toLocaleDateString()}</small>
                `;
                container.appendChild(contentElement);
            });
        }

        // Add Content Form
        document.getElementById('addContentForm').addEventListener('submit', function(e) {
            e.preventDefault();
            
            const section = document.getElementById('contentSection').value;
            const title = document.getElementById('contentTitle').value;
            const description = document.getElementById('contentDescription').value;
            const author = document.getElementById('contentAuthor').value;
            
            if (!userContents[section]) {
                userContents[section] = [];
            }
            
            const content = {
                id: Date.now().toString(),
                title,
                description,
                author,
                date: new Date().toISOString()
            };
            
            userContents[section].push(content);
            saveContents();
            renderSectionContents(section);
            closeAddContent();
            
            alert('Content added successfully!');
        });

        function showAddContentModal() {
            document.getElementById('addContentModal').style.display = 'flex';
        }

        function showTeamChat() {
            alert('Team Chat feature coming soon!');
        }
    </script>
</body>
</html>
