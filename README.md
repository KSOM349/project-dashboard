<!DOCTYPE html>
<html lang="sv" dir="ltr">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Smart Project Dashboard - Grupp 1</title>
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
            position: relative;
        }

        header h1 {
            font-size: 2.5rem;
            margin-bottom: 0.5rem;
        }

        header p {
            font-size: 1.2rem;
            opacity: 0.9;
        }

        /* Notification System */
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

        /* AI Recommendations */
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

        /* Forms */
        .form-group {
            margin-bottom: 1rem;
        }

        .form-group label {
            display: block;
            margin-bottom: 0.5rem;
            font-weight: 600;
            color: var(--dark);
        }

        .form-group input,
        .form-group textarea,
        .form-group select {
            width: 100%;
            padding: 12px;
            border: 2px solid var(--light);
            border-radius: 8px;
            font-size: 1rem;
            transition: border-color 0.3s ease;
        }

        .form-group input:focus,
        .form-group textarea:focus,
        .form-group select:focus {
            outline: none;
            border-color: var(--secondary);
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
            
            .notification {
                min-width: 250px;
                right: 10px;
            }
        }
    </style>
</head>
<body>
    <!-- Notification System -->
    <div class="notification-container" id="notificationContainer"></div>

    <div class="container">
        <header>
            <h1>🚀 Smart Project Dashboard - Grupp 1</h1>
            <p>AI-Powered Team Collaboration Platform</p>
            <div style="margin-top: 1rem; font-size: 0.9rem; opacity: 0.8;">
                <i class="fas fa-robot"></i> AI Assistant • <i class="fas fa-bell"></i> Smart Notifications • <i class="fas fa-chart-line"></i> Live Analytics
            </div>
        </header>

        <div class="nav-tabs">
            <div class="nav-tab active" data-target="dashboard-overview">
                <i class="fas fa-tachometer-alt"></i> Dashboard
            </div>
            <div class="nav-tab" data-target="algorithm-visualizer">
                <i class="fas fa-project-diagram"></i> Algorithm Visualizer
            </div>
            <div class="nav-tab" data-target="team-collaboration">
                <i class="fas fa-users"></i> Team Collaboration
            </div>
            <div class="nav-tab" data-target="ai-assistant">
                <i class="fas fa-robot"></i> AI Assistant
            </div>
            <div class="nav-tab" data-target="project-analytics">
                <i class="fas fa-chart-bar"></i> Analytics
            </div>
        </div>

        <!-- Dashboard Overview -->
        <section id="dashboard-overview" class="section active">
            <div class="section-header">
                <h2>📊 Project Overview</h2>
                <div style="display: flex; gap: 1rem;">
                    <button class="btn" style="background: var(--success); color: white;" onclick="showNotification('Project data refreshed!', 'success')">
                        <i class="fas fa-sync-alt"></i> Refresh
                    </button>
                </div>
            </div>
            
            <div class="stats-container">
                <div class="stat-card">
                    <div class="stat-number">6</div>
                    <div class="stat-label">Team Members</div>
                </div>
                <div class="stat-card">
                    <div class="stat-number">12</div>
                    <div class="stat-label">Completed Tasks</div>
                </div>
                <div class="stat-card">
                    <div class="stat-number">3</div>
                    <div class="stat-label">In Progress</div>
                </div>
                <div class="stat-card">
                    <div class="stat-number">94%</div>
                    <div class="stat-label">Project Health</div>
                </div>
            </div>

            <!-- AI Recommendations -->
            <div class="ai-recommendation">
                <h3><i class="fas fa-lightbulb"></i> AI Recommendations</h3>
                <div class="recommendation-item">
                    <strong>🚀 Optimize Dijkstra Performance</strong>
                    <p>Consider implementing Fibonacci heap for better time complexity</p>
                </div>
                <div class="recommendation-item">
                    <strong>📚 Schedule Code Review</strong>
                    <p>Team members: Kaled & Stefan available tomorrow</p>
                </div>
            </div>
        </section>

        <!-- Algorithm Visualizer -->
        <section id="algorithm-visualizer" class="section">
            <div class="section-header">
                <h2>📊 Algorithm Visualizer</h2>
            </div>
            
            <div class="stats-container">
                <div class="stat-card">
                    <div class="stat-number">5</div>
                    <div class="stat-label">Network Servers</div>
                </div>
                <div class="stat-card">
                    <div class="stat-number">13ms</div>
                    <div class="stat-label">Shortest Path</div>
                </div>
                <div class="stat-card">
                    <div class="stat-number">100%</div>
                    <div class="stat-label">Optimization</div>
                </div>
            </div>

            <div class="code-container">
                <pre>// Smart Dijkstra Implementation
function smartDijkstra(graph, start) {
    const distances = {};
    const previous = {};
    const nodes = new PriorityQueue();
    
    // Initialize distances
    for (let node in graph) {
        distances[node] = node === start ? 0 : Infinity;
        nodes.enqueue(node, distances[node]);
    }
    
    while (!nodes.isEmpty()) {
        const smallest = nodes.dequeue();
        
        if (distances[smallest] === Infinity) continue;
        
        for (let neighbor in graph[smallest]) {
            const alt = distances[smallest] + graph[smallest][neighbor];
            if (alt < distances[neighbor]) {
                distances[neighbor] = alt;
                previous[neighbor] = smallest;
                nodes.enqueue(neighbor, alt);
            }
        }
    }
    
    return { distances, previous };
}</pre>
            </div>

            <div style="background: var(--success); color: white; padding: 1rem; border-radius: 8px; margin-top: 1rem;">
                <h4>🎯 Live Algorithm Execution</h4>
                <div id="algorithm-steps">
                    <p>✅ Step 1: Initialize all nodes with infinite distance</p>
                    <p>✅ Step 2: Set start node distance to 0</p>
                    <p>🔄 Step 3: Processing neighbors...</p>
                </div>
            </div>
        </section>

        <!-- Team Collaboration -->
        <section id="team-collaboration" class="section">
            <div class="section-header">
                <h2>👥 Team Collaboration</h2>
                <button class="btn" style="background: var(--secondary); color: white;" onclick="showNotification('Team sync completed!', 'success')">
                    <i class="fas fa-sync"></i> Sync Team
                </button>
            </div>

            <div style="display: grid; grid-template-columns: 1fr 1fr; gap: 2rem; margin-top: 2rem;">
                <!-- Team Updates Form -->
                <div>
                    <h3>➕ Add Team Update</h3>
                    <form id="team-update-form" style="background: var(--light); padding: 1.5rem; border-radius: 12px;">
                        <div class="form-group">
                            <label>Your Name:</label>
                            <select id="update-author" class="form-control">
                                <option value="Kaled Osman">Kaled Osman</option>
                                <option value="Fahad Hussain">Fahad Hussain</option>
                                <option value="Stefan Österberg">Stefan Österberg</option>
                                <option value="Marcus Tibell">Marcus Tibell</option>
                                <option value="Jens Annell">Jens Annell</option>
                                <option value="Luwam">Luwam</option>
                            </select>
                        </div>
                        
                        <div class="form-group">
                            <label>Update Type:</label>
                            <select id="update-type" class="form-control">
                                <option value="progress">📈 Progress Update</option>
                                <option value="blocker">🚧 Blocker</option>
                                <option value="question">❓ Question</option>
                                <option value="achievement">🎉 Achievement</option>
                            </select>
                        </div>
                        
                        <div class="form-group">
                            <label>Message:</label>
                            <textarea id="update-message" placeholder="What's your update?" rows="4" class="form-control"></textarea>
                        </div>
                        
                        <button type="submit" class="btn" style="background: var(--success); color: white; width: 100%;">
                            <i class="fas fa-paper-plane"></i> Share Update
                        </button>
                    </form>
                </div>

                <!-- Team Activity -->
                <div>
                    <h3>📋 Team Activity</h3>
                    <div id="team-activity" style="max-height: 400px; overflow-y: auto;">
                        <div style="background: #f8f9fa; padding: 1rem; margin: 0.5rem 0; border-radius: 8px; border-left: 4px solid var(--success);">
                            <strong>Kaled Osman</strong> - <span style="color: var(--success);">Completed Dijkstra implementation</span>
                            <p style="margin: 0.5rem 0 0 0; color: #666;">Optimized the algorithm with priority queue</p>
                            <small style="color: #999;">2 hours ago</small>
                        </div>
                    </div>
                </div>
            </div>
        </section>

        <!-- AI Assistant -->
        <section id="ai-assistant" class="section">
            <div class="section-header">
                <h2>🤖 AI Assistant</h2>
                <div style="display: flex; gap: 1rem; align-items: center;">
                    <div style="background: var(--success); color: white; padding: 0.5rem 1rem; border-radius: 20px; font-size: 0.9rem;">
                        <i class="fas fa-circle"></i> Online
                    </div>
                </div>
            </div>

            <div style="display: grid; grid-template-columns: 1fr 1fr; gap: 2rem; margin-top: 2rem;">
                <!-- AI Chat -->
                <div>
                    <h3>💬 Ask AI Assistant</h3>
                    <div style="background: var(--light); padding: 1.5rem; border-radius: 12px; height: 300px; overflow-y: auto; margin-bottom: 1rem;" id="ai-chat">
                        <div style="background: white; padding: 1rem; margin: 0.5rem 0; border-radius: 8px;">
                            <strong>AI:</strong> Hello! I'm your project assistant. How can I help you today?
                        </div>
                    </div>
                    
                    <div style="display: flex; gap: 0.5rem;">
                        <input type="text" id="ai-input" placeholder="Ask me anything..." style="flex: 1; padding: 12px; border: 2px solid var(--light); border-radius: 8px;">
                        <button class="btn" style="background: var(--secondary); color: white;" onclick="askAI()">
                            <i class="fas fa-paper-plane"></i>
                        </button>
                    </div>
                </div>

                <!-- Smart Suggestions -->
                <div>
                    <h3>💡 Smart Suggestions</h3>
                    <div class="ai-recommendation">
                        <div class="recommendation-item">
                            <strong>📊 Code Quality Analysis</strong>
                            <p>Your Dijkstra implementation has 95% efficiency score</p>
                            <button class="btn" style="background: var(--warning); color: white; margin-top: 0.5rem; font-size: 0.8rem;">
                                View Details
                            </button>
                        </div>
                        
                        <div class="recommendation-item">
                            <strong>👥 Team Performance</strong>
                            <p>All team members are active and meeting deadlines</p>
                            <button class="btn" style="background: var(--success); color: white; margin-top: 0.5rem; font-size: 0.8rem;">
                                See Report
                            </button>
                        </div>
                    </div>
                </div>
            </div>
        </section>

        <!-- Project Analytics -->
        <section id="project-analytics" class="section">
            <div class="section-header">
                <h2>📈 Project Analytics</h2>
                <button class="btn" style="background: var(--primary); color: white;" onclick="generateReport()">
                    <i class="fas fa-download"></i> Export Report
                </button>
            </div>

            <div class="stats-container">
                <div class="stat-card">
                    <div class="stat-number">87%</div>
                    <div class="stat-label">Code Coverage</div>
                </div>
                <div class="stat-card">
                    <div class="stat-number">24/7</div>
                    <div class="stat-label">Uptime</div>
                </div>
                <div class="stat-card">
                    <div class="stat-number">15ms</div>
                    <div class="stat-label">Avg Response</div>
                </div>
                <div class="stat-card">
                    <div class="stat-number">99.9%</div>
                    <div class="stat-label">Reliability</div>
                </div>
            </div>

            <!-- Performance Metrics -->
            <div style="background: var(--light); padding: 1.5rem; border-radius: 12px; margin-top: 2rem;">
                <h3>📊 Performance Metrics</h3>
                <div style="display: grid; grid-template-columns: 1fr 1fr; gap: 1rem; margin-top: 1rem;">
                    <div>
                        <h4>Team Velocity</h4>
                        <div style="background: white; padding: 1rem; border-radius: 8px; text-align: center;">
                            <div style="font-size: 2rem; font-weight: bold; color: var(--success);">42</div>
                            <div style="color: #666;">points/week</div>
                        </div>
                    </div>
                    <div>
                        <h4>Task Completion</h4>
                        <div style="background: white; padding: 1rem; border-radius: 8px; text-align: center;">
                            <div style="font-size: 2rem; font-weight: bold; color: var(--secondary);">94%</div>
                            <div style="color: #666;">on time delivery</div>
                        </div>
                    </div>
                </div>
            </div>
        </section>
    </div>

    <script>
        // Notification System
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

        // Navigation System
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
                    
                    showNotification(`Switched to ${this.textContent.trim()}`, 'info');
                });
            });

            // Team Updates Form
            const updateForm = document.getElementById('team-update-form');
            if (updateForm) {
                updateForm.addEventListener('submit', function(event) {
                    event.preventDefault();
                    
                    const author = document.getElementById('update-author').value;
                    const type = document.getElementById('update-type').value;
                    const message = document.getElementById('update-message').value;
                    
                    const activityItem = document.createElement('div');
                    activityItem.style.background = '#f8f9fa';
                    activityItem.style.padding = '1rem';
                    activityItem.style.margin = '0.5rem 0';
                    activityItem.style.borderRadius = '8px';
                    activityItem.style.borderLeft = '4px solid var(--success)';
                    
                    activityItem.innerHTML = `
                        <strong>${author}</strong> - <span style="color: var(--success);">${getUpdateTypeText(type)}</span>
                        <p style="margin: 0.5rem 0 0 0; color: #666;">${message}</p>
                        <small style="color: #999;">Just now</small>
                    `;
                    
                    document.getElementById('team-activity').prepend(activityItem);
                    updateForm.reset();
                    
                    showNotification('Update shared with team!', 'success');
                });
            }

            // Simulate loading animations
            const statNumbers = document.querySelectorAll('.stat-number');
            statNumbers.forEach(stat => {
                const finalValue = stat.textContent;
                if (finalValue.includes('%') || finalValue.includes('ms')) {
                    return; // Skip already formatted values
                }
                
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

        function getUpdateTypeText(type) {
            const types = {
                'progress': '📈 Progress Update',
                'blocker': '🚧 Blocker Reported',
                'question': '❓ Question',
                'achievement': '🎉 Achievement'
            };
            return types[type] || 'Update';
        }

        // AI Assistant Functionality
        function askAI() {
            const input = document.getElementById('ai-input');
            const question = input.value.trim();
            
            if (!question) return;
            
            const chat = document.getElementById('ai-chat');
            
            // Add user question
            const userMsg = document.createElement('div');
            userMsg.style.background = 'var(--secondary)';
            userMsg.style.color = 'white';
            userMsg.style.padding = '1rem';
            userMsg.style.margin = '0.5rem 0';
            userMsg.style.borderRadius = '8px';
            userMsg.innerHTML = `<strong>You:</strong> ${question}`;
            chat.appendChild(userMsg);
            
            // Simulate AI response
            setTimeout(() => {
                const aiResponse = getAIResponse(question);
                const aiMsg = document.createElement('div');
                aiMsg.style.background = 'white';
                aiMsg.style.padding = '1rem';
                aiMsg.style.margin = '0.5rem 0';
                aiMsg.style.borderRadius = '8px';
                aiMsg.innerHTML = `<strong>AI:</strong> ${aiResponse}`;
                chat.appendChild(aiMsg);
                
                chat.scrollTop = chat.scrollHeight;
            }, 1000);
            
            input.value = '';
            chat.scrollTop = chat.scrollHeight;
        }

        function getAIResponse(question) {
            const responses = {
                'hello': 'Hello! How can I assist with your project today?',
                'help': 'I can help with code optimization, team coordination, and project analytics. What do you need?',
                'dijkstra': 'For Dijkstra optimization, consider using Fibonacci heaps for better performance with large graphs.',
                'team': 'Your team is performing well! All members are active and meeting their deadlines.',
                'report': 'I can generate a project report showing code quality, team performance, and timeline analytics.'
            };
            
            question = question.toLowerCase();
            for (let key in responses) {
                if (question.includes(key)) {
                    return responses[key];
                }
            }
            
            return "I understand you're asking about: " + question + ". I can help with project management, code optimization, and team coordination. Can you be more specific?";
        }

        function generateReport() {
            showNotification('Project report generated and downloaded!', 'success');
            // In a real app, this would generate and download a PDF report
        }

        // Show welcome notification
        setTimeout(() => {
            showNotification('Welcome to Smart Project Dashboard! AI assistant is ready to help.', 'info');
        }, 1000);
    </script>
</body>
</html>
