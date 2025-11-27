<!DOCTYPE html>
<html lang="sv">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Enhanced Dashboard - Grupp 1</title>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.0/css/all.min.css">
    <style>
        :root {
            --primary: #007AFF;
            --secondary: #5856D6;
            --success: #34C759;
            --warning: #FF9500;
            --danger: #FF3B30;
            --background: #FFFFFF;
            --text: #000000;
            --card-bg: #F2F2F7;
            --border: #C6C6C8;
        }

        [data-theme="dark"] {
            --background: #000000;
            --text: #FFFFFF;
            --card-bg: #1C1C1E;
            --border: #38383A;
        }

        body {
            font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', sans-serif;
            margin: 0;
            background: var(--background);
            color: var(--text);
            transition: all 0.3s ease;
        }

        .container {
            max-width: 1200px;
            margin: 0 auto;
            padding: 20px;
        }

        .header {
            display: flex;
            justify-content: space-between;
            align-items: center;
            padding: 20px 0;
            border-bottom: 1px solid var(--border);
        }

        .theme-toggle {
            background: none;
            border: 1px solid var(--border);
            color: var(--text);
            padding: 10px 15px;
            border-radius: 8px;
            cursor: pointer;
        }

        .grid {
            display: grid;
            grid-template-columns: repeat(auto-fit, minmax(300px, 1fr));
            gap: 20px;
            margin: 30px 0;
        }

        .card {
            background: var(--card-bg);
            padding: 20px;
            border-radius: 12px;
            border: 1px solid var(--border);
        }

        .btn {
            background: var(--primary);
            color: white;
            border: none;
            padding: 12px 20px;
            border-radius: 8px;
            cursor: pointer;
            font-size: 16px;
            transition: opacity 0.2s;
        }

        .btn:hover {
            opacity: 0.8;
        }

        .btn:active {
            opacity: 0.6;
        }

        .stats {
            display: grid;
            grid-template-columns: repeat(2, 1fr);
            gap: 15px;
            margin: 20px 0;
        }

        .stat-item {
            text-align: center;
            padding: 15px;
            background: var(--background);
            border-radius: 8px;
            border: 1px solid var(--border);
        }

        @media (max-width: 768px) {
            .grid {
                grid-template-columns: 1fr;
            }
            
            .stats {
                grid-template-columns: 1fr;
            }
        }
    </style>
</head>
<body>
    <div class="container">
        <div class="header">
            <h1>🚀 Enhanced Dashboard - Grupp 1</h1>
            <button class="theme-toggle" onclick="toggleTheme()">
                <i class="fas fa-moon"></i> Tema
            </button>
        </div>

        <div class="grid">
            <div class="card">
                <h2>📊 System Status</h2>
                <div class="stats">
                    <div class="stat-item">
                        <div class="stat-value">95%</div>
                        <div class="stat-label">CPU</div>
                    </div>
                    <div class="stat-item">
                        <div class="stat-value">78%</div>
                        <div class="stat-label">Memory</div>
                    </div>
                    <div class="stat-item">
                        <div class="stat-value">45%</div>
                        <div class="stat-label">Disk</div>
                    </div>
                    <div class="stat-item">
                        <div class="stat-value">2h 15m</div>
                        <div class="stat-label">Uptime</div>
                    </div>
                </div>
                <button class="btn" onclick="showNotification('System data updated!', 'success')">
                    <i class="fas fa-sync"></i> Update Data
                </button>
            </div>

            <div class="card">
                <h2>🔔 Alerts</h2>
                <div id="alerts-container">
                    <div class="alert-item" style="color: var(--success);">
                        <i class="fas fa-check-circle"></i> All systems normal
                    </div>
                </div>
                <button class="btn" onclick="checkAlerts()" style="background: var(--warning);">
                    <i class="fas fa-bell"></i> Check Alerts
                </button>
            </div>
        </div>

        <div class="card">
            <h2>🤖 AI Assistant</h2>
            <div class="chat-container">
                <div class="message ai-message">
                    <strong>AI:</strong> Hello! I'm here to help with your project.
                </div>
            </div>
            <input type="text" id="chat-input" placeholder="Ask about Dijkstra algorithm..." style="width: 100%; padding: 10px; margin: 10px 0; border: 1px solid var(--border); border-radius: 8px; background: var(--background); color: var(--text);">
            <button class="btn" onclick="sendMessage()" style="background: var(--secondary);">
                <i class="fas fa-paper-plane"></i> Send
            </button>
        </div>
    </div>

    <script>
        // Theme management
        function toggleTheme() {
            const currentTheme = document.body.getAttribute('data-theme');
            const newTheme = currentTheme === 'dark' ? 'light' : 'dark';
            document.body.setAttribute('data-theme', newTheme);
            localStorage.setItem('theme', newTheme);
        }

        // Load saved theme
        const savedTheme = localStorage.getItem('theme') || 'light';
        document.body.setAttribute('data-theme', savedTheme);

        // Notification system
        function showNotification(message, type = 'info') {
            alert(message); // Simple alert for now - can be enhanced with toast notifications
        }

        // Interactive functions
        function checkAlerts() {
            showNotification('Checking system alerts...', 'info');
            // Simulate API call
            setTimeout(() => {
                document.getElementById('alerts-container').innerHTML = `
                    <div class="alert-item" style="color: var(--success);">
                        <i class="fas fa-check-circle"></i> No critical alerts found
                    </div>
                `;
            }, 1000);
        }

        function sendMessage() {
            const input = document.getElementById('chat-input');
            const message = input.value.trim();
            
            if (message) {
                showNotification('Message sent to AI assistant', 'success');
                input.value = '';
                
                // Simulate AI response
                setTimeout(() => {
                    showNotification('AI: I received your message about: ' + message, 'info');
                }, 1500);
            }
        }

        // Make all buttons responsive
        document.querySelectorAll('.btn').forEach(btn => {
            btn.addEventListener('click', function() {
                this.style.transform = 'scale(0.95)';
                setTimeout(() => {
                    this.style.transform = 'scale(1)';
                }, 150);
            });
        });
    </script>
</body>
</html>
