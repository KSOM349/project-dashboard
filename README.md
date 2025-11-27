<!DOCTYPE html>
<html lang="ar" dir="rtl">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>نظام Security Chaos Engineering المتكامل - المجموعة 1</title>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
    <style>
        :root {
            --primary-color: #007AFF;
            --secondary-color: #5856D6;
            --success-color: #34C759;
            --warning-color: #FF9500;
            --danger-color: #FF3B30;
            --info-color: #5AC8FA;
            --bg-color: #FFFFFF;
            --sidebar-bg: #F2F2F7;
            --text-color: #000000;
            --text-secondary: #8E8E93;
            --border-color: #C6C6C8;
            --card-bg: #FFFFFF;
            --shadow: 0 2px 10px rgba(0,0,0,0.1);
        }

        [data-theme="dark"] {
            --bg-color: #000000;
            --sidebar-bg: #1C1C1E;
            --text-color: #FFFFFF;
            --text-secondary: #8E8E93;
            --border-color: #38383A;
            --card-bg: #1C1C1E;
            --shadow: 0 2px 10px rgba(255,255,255,0.1);
        }

        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
        }

        body {
            font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, sans-serif;
            background-color: var(--bg-color);
            color: var(--text-color);
            transition: all 0.3s ease;
            line-height: 1.6;
        }

        .container {
            display: flex;
            min-height: 100vh;
        }

        /* الشريط الجانبي */
        .sidebar {
            width: 280px;
            background-color: var(--sidebar-bg);
            padding: 20px;
            border-left: 1px solid var(--border-color);
            position: fixed;
            left: 0;
            top: 0;
            height: 100vh;
            overflow-y: auto;
            z-index: 1000;
        }

        .sidebar-header {
            padding-bottom: 20px;
            border-bottom: 1px solid var(--border-color);
            margin-bottom: 20px;
        }

        .sidebar-header h1 {
            font-size: 22px;
            font-weight: 600;
            margin-bottom: 5px;
        }

        .sidebar-header p {
            color: var(--text-secondary);
            font-size: 14px;
        }

        .nav-section {
            margin-bottom: 30px;
        }

        .nav-section h3 {
            font-size: 17px;
            font-weight: 600;
            margin-bottom: 15px;
            color: var(--text-secondary);
            text-transform: uppercase;
            letter-spacing: 0.5px;
        }

        .nav-item {
            display: flex;
            align-items: center;
            padding: 12px 15px;
            margin-bottom: 5px;
            border-radius: 10px;
            cursor: pointer;
            transition: all 0.2s ease;
            color: var(--text-color);
            text-decoration: none;
        }

        .nav-item:hover {
            background-color: rgba(0, 122, 255, 0.1);
        }

        .nav-item.active {
            background-color: var(--primary-color);
            color: white;
        }

        .nav-item i {
            margin-left: 10px;
            width: 20px;
            text-align: center;
        }

        /* المحتوى الرئيسي */
        .main-content {
            flex: 1;
            margin-right: 280px;
            padding: 30px;
        }

        .header {
            display: flex;
            justify-content: space-between;
            align-items: center;
            margin-bottom: 30px;
            padding-bottom: 20px;
            border-bottom: 1px solid var(--border-color);
        }

        .header h2 {
            font-size: 28px;
            font-weight: 700;
        }

        .header-actions {
            display: flex;
            gap: 15px;
            align-items: center;
        }

        .theme-toggle, .user-profile {
            background: none;
            border: none;
            color: var(--text-color);
            cursor: pointer;
            padding: 10px;
            border-radius: 50%;
            transition: background-color 0.2s ease;
        }

        .theme-toggle:hover, .user-profile:hover {
            background-color: var(--sidebar-bg);
        }

        /* الأقسام */
        .section {
            display: none;
            animation: fadeIn 0.3s ease;
        }

        .section.active {
            display: block;
        }

        @keyframes fadeIn {
            from { opacity: 0; transform: translateY(10px); }
            to { opacity: 1; transform: translateY(0); }
        }

        .card {
            background-color: var(--card-bg);
            border-radius: 12px;
            padding: 25px;
            margin-bottom: 20px;
            border: 1px solid var(--border-color);
            box-shadow: var(--shadow);
        }

        .card h3 {
            font-size: 20px;
            font-weight: 600;
            margin-bottom: 15px;
        }

        /* الشبكات والجريد */
        .grid-2 {
            display: grid;
            grid-template-columns: repeat(auto-fit, minmax(400px, 1fr));
            gap: 20px;
        }

        .grid-3 {
            display: grid;
            grid-template-columns: repeat(auto-fit, minmax(300px, 1fr));
            gap: 20px;
        }

        .grid-4 {
            display: grid;
            grid-template-columns: repeat(auto-fit, minmax(250px, 1fr));
            gap: 20px;
        }

        /* المساعد الذكي المحسن */
        .chat-container {
            max-width: 100%;
            margin: 0 auto;
        }

        .chat-header {
            display: flex;
            justify-content: space-between;
            align-items: center;
            margin-bottom: 20px;
        }

        .chat-messages {
            height: 400px;
            overflow-y: auto;
            border: 1px solid var(--border-color);
            border-radius: 12px;
            padding: 20px;
            margin-bottom: 20px;
            background-color: var(--card-bg);
        }

        .message {
            margin-bottom: 15px;
            padding: 12px 16px;
            border-radius: 18px;
            max-width: 80%;
            position: relative;
        }

        .user-message {
            background-color: var(--primary-color);
            color: white;
            margin-right: auto;
            border-bottom-right-radius: 4px;
        }

        .assistant-message {
            background-color: var(--sidebar-bg);
            color: var(--text-color);
            margin-left: auto;
            border-bottom-left-radius: 4px;
        }

        .message-time {
            font-size: 12px;
            color: var(--text-secondary);
            margin-top: 5px;
            text-align: right;
        }

        .chat-input-container {
            display: flex;
            gap: 10px;
            align-items: flex-end;
        }

        .chat-input {
            flex: 1;
            display: flex;
            flex-direction: column;
        }

        .chat-input textarea {
            width: 100%;
            padding: 12px 16px;
            border: 1px solid var(--border-color);
            border-radius: 20px;
            background-color: var(--card-bg);
            color: var(--text-color);
            font-size: 16px;
            resize: vertical;
            min-height: 60px;
            max-height: 120px;
            font-family: inherit;
        }

        .chat-actions {
            display: flex;
            justify-content: space-between;
            margin-top: 10px;
        }

        .chat-action-btn {
            background: none;
            border: none;
            color: var(--text-secondary);
            cursor: pointer;
            padding: 5px 10px;
            border-radius: 5px;
            transition: all 0.2s ease;
        }

        .chat-action-btn:hover {
            background-color: var(--sidebar-bg);
            color: var(--text-color);
        }

        .send-btn {
            background-color: var(--primary-color);
            color: white;
            border: none;
            padding: 15px 25px;
            border-radius: 20px;
            cursor: pointer;
            font-size: 16px;
            transition: background-color 0.2s ease;
            height: 60px;
        }

        .send-btn:hover {
            background-color: #0056CC;
        }

        .send-btn:disabled {
            background-color: var(--text-secondary);
            cursor: not-allowed;
        }

        /* محاكي SEC */
        .simulator-container {
            max-width: 100%;
        }

        .simulator-controls {
            display: grid;
            grid-template-columns: repeat(auto-fit, minmax(200px, 1fr));
            gap: 15px;
            margin-bottom: 20px;
        }

        .control-group {
            display: flex;
            flex-direction: column;
        }

        .control-group label {
            margin-bottom: 5px;
            font-weight: 500;
        }

        .control-group select, .control-group input {
            padding: 10px;
            border: 1px solid var(--border-color);
            border-radius: 8px;
            background-color: var(--card-bg);
            color: var(--text-color);
        }

        .simulator-results {
            margin-top: 20px;
        }

        .result-card {
            background: linear-gradient(135deg, var(--primary-color), var(--secondary-color));
            color: white;
            padding: 20px;
            border-radius: 12px;
            margin-bottom: 15px;
        }

        .result-card h4 {
            margin-bottom: 10px;
        }

        /* المنصة التعليمية */
        .courses-grid {
            display: grid;
            grid-template-columns: repeat(auto-fit, minmax(300px, 1fr));
            gap: 20px;
        }

        .course-card {
            background-color: var(--card-bg);
            border: 1px solid var(--border-color);
            border-radius: 12px;
            padding: 20px;
            transition: transform 0.2s ease;
            cursor: pointer;
        }

        .course-card:hover {
            transform: translateY(-5px);
        }

        .course-card h4 {
            color: var(--primary-color);
            margin-bottom: 10px;
        }

        .course-progress {
            height: 6px;
            background-color: var(--sidebar-bg);
            border-radius: 3px;
            margin: 10px 0;
            overflow: hidden;
        }

        .progress-bar {
            height: 100%;
            background-color: var(--success-color);
            border-radius: 3px;
        }

        /* أدوات المطورين */
        .tools-grid {
            display: grid;
            grid-template-columns: repeat(auto-fit, minmax(250px, 1fr));
            gap: 20px;
        }

        .tool-card {
            background-color: var(--card-bg);
            border: 1px solid var(--border-color);
            border-radius: 12px;
            padding: 20px;
            text-align: center;
            cursor: pointer;
            transition: all 0.2s ease;
        }

        .tool-card:hover {
            background-color: var(--primary-color);
            color: white;
        }

        .tool-card i {
            font-size: 40px;
            margin-bottom: 15px;
            color: var(--primary-color);
        }

        .tool-card:hover i {
            color: white;
        }

        /* الرسوم البيانية */
        .chart-container {
            background-color: var(--card-bg);
            border-radius: 12px;
            padding: 20px;
            margin-bottom: 20px;
            height: 300px;
        }

        /* المجتمع */
        .community-posts {
            max-width: 800px;
        }

        .post {
            background-color: var(--card-bg);
            border: 1px solid var(--border-color);
            border-radius: 12px;
            padding: 20px;
            margin-bottom: 20px;
        }

        .post-header {
            display: flex;
            align-items: center;
            margin-bottom: 15px;
        }

        .post-avatar {
            width: 40px;
            height: 40px;
            border-radius: 50%;
            background-color: var(--primary-color);
            margin-left: 10px;
            display: flex;
            align-items: center;
            justify-content: center;
            color: white;
            font-weight: bold;
        }

        .post-content {
            margin-bottom: 15px;
        }

        .post-actions {
            display: flex;
            gap: 15px;
        }

        .post-action {
            background: none;
            border: none;
            color: var(--text-secondary);
            cursor: pointer;
            display: flex;
            align-items: center;
            gap: 5px;
        }

        /* التكيف مع الشاشات الصغيرة */
        @media (max-width: 768px) {
            .container {
                flex-direction: column;
            }

            .sidebar {
                position: relative;
                width: 100%;
                height: auto;
                border-left: none;
                border-bottom: 1px solid var(--border-color);
            }

            .main-content {
                margin-right: 0;
                padding: 20px;
            }

            .nav-items {
                display: flex;
                flex-wrap: wrap;
                gap: 10px;
            }

            .nav-item {
                flex: 1;
                min-width: 120px;
                justify-content: center;
            }

            .grid-2, .grid-3, .grid-4 {
                grid-template-columns: 1fr;
            }

            .chat-input-container {
                flex-direction: column;
            }

            .send-btn {
                width: 100%;
                height: 50px;
            }
        }

        /* تحميل ورسوم متحركة */
        .loading {
            display: inline-block;
            width: 20px;
            height: 20px;
            border: 3px solid rgba(255,255,255,.3);
            border-radius: 50%;
            border-top-color: #fff;
            animation: spin 1s ease-in-out infinite;
        }

        @keyframes spin {
            to { transform: rotate(360deg); }
        }

        .pulse {
            animation: pulse 2s infinite;
        }

        @keyframes pulse {
            0% { opacity: 1; }
            50% { opacity: 0.5; }
            100% { opacity: 1; }
        }

        /* التنبيهات */
        .alert {
            padding: 15px;
            border-radius: 8px;
            margin-bottom: 20px;
            display: flex;
            align-items: center;
            gap: 10px;
        }

        .alert-success {
            background-color: rgba(52, 199, 89, 0.1);
            border: 1px solid var(--success-color);
            color: var(--success-color);
        }

        .alert-warning {
            background-color: rgba(255, 149, 0, 0.1);
            border: 1px solid var(--warning-color);
            color: var(--warning-color);
        }

        .alert-danger {
            background-color: rgba(255, 59, 48, 0.1);
            border: 1px solid var(--danger-color);
            color: var(--danger-color);
        }

        /* علامات التبويب */
        .tabs {
            display: flex;
            border-bottom: 1px solid var(--border-color);
            margin-bottom: 20px;
        }

        .tab {
            padding: 10px 20px;
            cursor: pointer;
            border-bottom: 3px solid transparent;
            transition: all 0.2s ease;
        }

        .tab.active {
            border-bottom-color: var(--primary-color);
            color: var(--primary-color);
        }

        .tab-content {
            display: none;
        }

        .tab-content.active {
            display: block;
        }
    </style>
</head>
<body>
    <div class="container">
        <!-- الشريط الجانبي -->
        <nav class="sidebar">
            <div class="sidebar-header">
                <h1>Security Chaos Engineering</h1>
                <p>نظام متكامل للهندسة الفوضوية الآمنة</p>
            </div>

            <div class="nav-section">
                <h3>التنقل الرئيسي</h3>
                <div class="nav-item active" data-section="dashboard">
                    <i class="fas fa-tachometer-alt"></i> لوحة التحكم
                </div>
                <div class="nav-item" data-section="assistant">
                    <i class="fas fa-robot"></i> المساعد الذكي المتقدم
                </div>
                <div class="nav-item" data-section="simulator">
                    <i class="fas fa-vial"></i> محاكي SEC
                </div>
            </div>

            <div class="nav-section">
                <h3>التعليم والتدريب</h3>
                <div class="nav-item" data-section="learning">
                    <i class="fas fa-graduation-cap"></i> المنصة التعليمية
                </div>
                <div class="nav-item" data-section="courses">
                    <i class="fas fa-book"></i> الدورات المتخصصة
                </div>
                <div class="nav-item" data-section="certifications">
                    <i class="fas fa-certificate"></i> الشهادات
                </div>
            </div>

            <div class="nav-section">
                <h3>أدوات المطورين</h3>
                <div class="nav-item" data-section="tools">
                    <i class="fas fa-tools"></i> الأدوات المساعدة
                </div>
                <div class="nav-item" data-section="cloud">
                    <i class="fas fa-cloud"></i> تكامل السحابة
                </div>
                <div class="nav-item" data-section="analytics">
                    <i class="fas fa-chart-bar"></i> التحليلات
                </div>
            </div>

            <div class="nav-section">
                <h3>المجتمع والدعم</h3>
                <div class="nav-item" data-section="community">
                    <i class="fas fa-users"></i> المجتمع
                </div>
                <div class="nav-item" data-section="documentation">
                    <i class="fas fa-file-alt"></i> التوثيق الشامل
                </div>
                <div class="nav-item" data-section="support">
                    <i class="fas fa-life-ring"></i> الدعم الفني
                </div>
            </div>
        </nav>

        <!-- المحتوى الرئيسي -->
        <main class="main-content">
            <div class="header">
                <h2 id="pageTitle">لوحة تحكم Security Chaos Engineering</h2>
                <div class="header-actions">
                    <button class="theme-toggle" id="themeToggle">
                        <i class="fas fa-moon"></i>
                    </button>
                    <button class="user-profile" id="userProfile">
                        <i class="fas fa-user"></i>
                    </button>
                </div>
            </div>

            <!-- قسم لوحة التحكم -->
            <section id="dashboard" class="section active">
                <div class="alert alert-info">
                    <i class="fas fa-info-circle"></i>
                    مرحباً بك في النظام المتكامل لـ Security Chaos Engineering
                </div>

                <div class="grid-3">
                    <div class="card">
                        <h3><i class="fas fa-robot"></i> المساعد الذكي</h3>
                        <p>مساعد متقدم متخصص في SEC والشبكات السحابية</p>
                        <button class="nav-item" data-section="assistant" style="margin-top: 15px; width: 100%;">
                            <i class="fas fa-arrow-right"></i> الانتقال للمساعد
                        </button>
                    </div>

                    <div class="card">
                        <h3><i class="fas fa-vial"></i> محاكي التجارب</h3>
                        <p>محاكاة تجارب SEC في بيئات افتراضية</p>
                        <button class="nav-item" data-section="simulator" style="margin-top: 15px; width: 100%;">
                            <i class="fas fa-arrow-right"></i> بدء المحاكاة
                        </button>
                    </div>

                    <div class="card">
                        <h3><i class="fas fa-graduation-cap"></i> التعلم</h3>
                        <p>دورات تعليمية متخصصة في SEC</p>
                        <button class="nav-item" data-section="learning" style="margin-top: 15px; width: 100%;">
                            <i class="fas fa-arrow-right"></i> بدء التعلم
                        </button>
                    </div>
                </div>

                <div class="card">
                    <h3>إحصائيات النظام</h3>
                    <div class="chart-container">
                        <canvas id="dashboardChart"></canvas>
                    </div>
                </div>
            </section>

            <!-- قسم المساعد الذكي المتقدم -->
            <section id="assistant" class="section">
                <div class="card">
                    <div class="chat-header">
                        <h3><i class="fas fa-robot"></i> المساعد الذكي المتقدم</h3>
                        <div>
                            <button class="chat-action-btn" id="clearChat">
                                <i class="fas fa-trash"></i> مسح المحادثة
                            </button>
                            <button class="chat-action-btn" id="exportChat">
                                <i class="fas fa-download"></i> تصدير
                            </button>
                        </div>
                    </div>

                    <div class="chat-messages" id="chatMessages">
                        <div class="message assistant-message">
                            <div>مرحباً! أنا مساعدك المتخصص في Security Chaos Engineering والشبكات السحابية. كيف يمكنني مساعدتك اليوم؟</div>
                            <div class="message-time" id="currentTime"></div>
                        </div>
                    </div>

                    <div class="chat-input-container">
                        <div class="chat-input">
                            <textarea id="userInput" placeholder="اكتب سؤالك هنا عن Security Chaos Engineering أو الشبكات السحابية..." rows="3"></textarea>
                            <div class="chat-actions">
                                <div>
                                    <button class="chat-action-btn" id="attachFile">
                                        <i class="fas fa-paperclip"></i> إرفاق ملف
                                    </button>
                                    <button class="chat-action-btn" id="voiceInput">
                                        <i class="fas fa-microphone"></i> مدخل صوتي
                                    </button>
                                </div>
                                <span id="charCount">0/1000</span>
                            </div>
                        </div>
                        <button class="send-btn" onclick="sendMessage()" id="sendBtn">
                            <i class="fas fa-paper-plane"></i> إرسال
                        </button>
                    </div>
                </div>

                <div class="grid-2">
                    <div class="card">
                        <h4>الأسئلة الشائعة</h4>
                        <div class="faq-list">
                            <div class="faq-item" onclick="insertQuestion('ما هو Security Chaos Engineering؟')">
                                ما هو Security Chaos Engineering؟
                            </div>
                            <div class="faq-item" onclick="insertQuestion('كيف أطبق SEC في AWS؟')">
                                كيف أطبق SEC في AWS؟
                            </div>
                            <div class="faq-item" onclick="insertQuestion('ما أفضل أدوات SEC؟')">
                                ما أفضل أدوات SEC؟
                            </div>
                            <div class="faq-item" onclick="insertQuestion('كيفة أقيس فعالية SEC؟')">
                                كيفة أقيس فعالية SEC؟
                            </div>
                        </div>
                    </div>

                    <div class="card">
                        <h4>المحادثات الحديثة</h4>
                        <div id="recentChats">
                            <div class="chat-preview">لا توجد محادثات سابقة</div>
                        </div>
                    </div>
                </div>
            </section>

            <!-- قسم محاكي SEC -->
            <section id="simulator" class="section">
                <div class="card">
                    <h3><i class="fas fa-vial"></i> محاكي Security Chaos Engineering</h3>
                    <p>محاكاة تجارب SEC في بيئات افتراضية آمنة</p>
                </div>

                <div class="simulator-container">
                    <div class="tabs">
                        <div class="tab active" data-tab="aws">AWS</div>
                        <div class="tab" data-tab="azure">Azure</div>
                        <div class="tab" data-tab="gcp">Google Cloud</div>
                        <div class="tab" data-tab="custom">مخصص</div>
                    </div>

                    <div class="tab-content active" id="aws-tab">
                        <div class="simulator-controls">
                            <div class="control-group">
                                <label>نوع التجربة</label>
                                <select id="experimentType">
                                    <option value="latency">محاكاة التأخير</option>
                                    <option value="failure">فشل الخدمة</option>
                                    <option value="security">اختبار الأمان</option>
                                    <option value="load">اختبار الحمل</option>
                                </select>
                            </div>

                            <div class="control-group">
                                <label>الخدمة المستهدفة</label>
                                <select id="targetService">
                                    <option value="ec2">EC2</option>
                                    <option value="s3">S3</option>
                                    <option value="lambda">Lambda</option>
                                    <option value="rds">RDS</option>
                                </select>
                            </div>

                            <div class="control-group">
                                <label>شدة التجربة</label>
                                <select id="experimentIntensity">
                                    <option value="low">منخفض</option>
                                    <option value="medium">متوسط</option>
                                    <option value="high">مرتفع</option>
                                </select>
                            </div>

                            <div class="control-group">
                                <label>مدة التجربة (ثواني)</label>
                                <input type="number" id="experimentDuration" value="30" min="5" max="300">
                            </div>
                        </div>

                        <button class="send-btn" onclick="runExperiment()" style="width: 100%;">
                            <i class="fas fa-play"></i> تشغيل التجربة
                        </button>

                        <div class="simulator-results" id="simulatorResults" style="display: none;">
                            <div class="result-card">
                                <h4>نتائج التجربة</h4>
                                <div id="experimentResults"></div>
                            </div>
                        </div>
                    </div>
                </div>

                <div class="grid-2" style="margin-top: 30px;">
                    <div class="card">
                        <h4>قوالب جاهزة</h4>
                        <div class="templates-list">
                            <div class="template-item" onclick="loadTemplate('ec2-failure')">
                                <h5>فشل instance EC2</h5>
                                <p>محاكاة فشل مفاجئ لـ EC2 instance</p>
                            </div>
                            <div class="template-item" onclick="loadTemplate('s3-latency')">
                                <h5>تأخير S3</h5>
                                <p>محاكاة تأخير في خدمة S3</p>
                            </div>
                            <div class="template-item" onclick="loadTemplate('lambda-timeout')">
                                <h5>انتهاء وقت Lambda</h5>
                                <p>محاكاة انتهاء وقت تنفيذ Lambda function</p>
                            </div>
                        </div>
                    </div>

                    <div class="card">
                        <h4>الإحصائيات</h4>
                        <div class="chart-container">
                            <canvas id="simulatorChart"></canvas>
                        </div>
                    </div>
                </div>
            </section>

            <!-- قسم المنصة التعليمية -->
            <section id="learning" class="section">
                <div class="card">
                    <h3><i class="fas fa-graduation-cap"></i> المنصة التعليمية</h3>
                    <p>دورات متخصصة في Security Chaos Engineering</p>
                </div>

                <div class="courses-grid">
                    <div class="course-card" onclick="startCourse('sec-fundamentals')">
                        <h4>أساسيات SEC</h4>
                        <p>المفاهيم الأساسية للهندسة الفوضوية الآمنة</p>
                        <div class="course-progress">
                            <div class="progress-bar" style="width: 0%"></div>
                        </div>
                        <small>0% مكتمل</small>
                    </div>

                    <div class="course-card" onclick="startCourse('aws-sec')">
                        <h4>SEC في AWS</h4>
                        <p>تطبيق SEC في بيئة Amazon Web Services</p>
                        <div class="course-progress">
                            <div class="progress-bar" style="width: 0%"></div>
                        </div>
                        <small>0% مكتمل</small>
                    </div>

                    <div class="course-card" onclick="startCourse('tools-mastery')">
                        <h4>إتقان الأدوات</h4>
                        <p>Chaos Monkey, Gremlin, وأدوات أخرى</p>
                        <div class="course-progress">
                            <div class="progress-bar" style="width: 0%"></div>
                        </div>
                        <small>0% مكتمل</small>
                    </div>

                    <div class="course-card" onclick="startCourse('advanced-patterns')">
                        <h4>أنماط متقدمة</h4>
                        <p>أنماط وتصميمات متقدمة في SEC</p>
                        <div class="course-progress">
                            <div class="progress-bar" style="width: 0%"></div>
                        </div>
                        <small>0% مكتمل</small>
                    </div>
                </div>

                <div class="card">
                    <h4>التقدم التعليمي</h4>
                    <div class="chart-container">
                        <canvas id="learningChart"></canvas>
                    </div>
                </div>
            </section>

            <!-- قسم أدوات المطورين -->
            <section id="tools" class="section">
                <div class="card">
                    <h3><i class="fas fa-tools"></i> أدوات المطورين</h3>
                    <p>مجموعة أدوات مساعدة لتطوير أنظمة SEC</p>
                </div>

                <div class="tools-grid">
                    <div class="tool-card" onclick="openTool('policy-generator')">
                        <i class="fas fa-shield-alt"></i>
                        <h4>مولد السياسات</h4>
                        <p>إنشاء سياسات أمان للسحابة</p>
                    </div>

                    <div class="tool-card" onclick="openTool('cost-calculator')">
                        <i class="fas fa-calculator"></i>
                        <h4>حساب التكاليف</h4>
                        <p>تقدير تكاليف السحابة</p>
                    </div>

                    <div class="tool-card" onclick="openTool('risk-analyzer')">
                        <i class="fas fa-chart-line"></i>
                        <h4>محلل المخاطر</h4>
                        <p>تحليل مخاطر النظام</p>
                    </div>

                    <div class="tool-card" onclick="openTool('resilience-scorer')">
                        <i class="fas fa-bolt"></i>
                        <h4>مقياس المرونة</h4>
                        <p>قياس مرونة الأنظمة</p>
                    </div>
                </div>

                <div class="grid-2" style="margin-top: 30px;">
                    <div class="card">
                        <h4>أداة إنشاء السياسات</h4>
                        <div class="policy-generator">
                            <div class="control-group">
                                <label>نوع السياسة</label>
                                <select id="policyType">
                                    <option value="iam">IAM Policy</option>
                                    <option value="s3">S3 Bucket Policy</option>
                                    <option value="security-group">Security Group</option>
                                </select>
                            </div>
                            <button class="send-btn" onclick="generatePolicy()" style="width: 100%; margin-top: 15px;">
                                <i class="fas fa-magic"></i> إنشاء السياسة
                            </button>
                            <div id="policyOutput" style="margin-top: 15px; display: none;">
                                <textarea id="generatedPolicy" rows="10" style="width: 100%; font-family: monospace;"></textarea>
                            </div>
                        </div>
                    </div>

                    <div class="card">
                        <h4>حساب تكاليف السحابة</h4>
                        <div class="cost-calculator">
                            <div class="control-group">
                                <label>عدد الخوادم</label>
                                <input type="number" id="serverCount" value="1" min="1">
                            </div>
                            <div class="control-group">
                                <label>سعر الخادم الشهري ($)</label>
                                <input type="number" id="serverPrice" value="50" min="1">
                            </div>
                            <button class="send-btn" onclick="calculateCost()" style="width: 100%; margin-top: 15px;">
                                <i class="fas fa-calculator"></i> حساب التكلفة
                            </button>
                            <div id="costOutput" style="margin-top: 15px; display: none;">
                                <h4>التكلفة الشهرية: <span id="monthlyCost">$0</span></h4>
                                <h4>التكلفة السنوية: <span id="yearlyCost">$0</span></h4>
                            </div>
                        </div>
                    </div>
                </div>
            </section>

            <!-- أقسام أخرى (سيتم إضافتها) -->
            <section id="community" class="section">
                <div class="card">
                    <h3>المجتمع</h3>
                    <p>قريباً...</p>
                </div>
            </section>

            <section id="documentation" class="section">
                <div class="card">
                    <h3>التوثيق الشامل</h3>
                    <p>قريباً...</p>
                </div>
            </section>

            <!-- باقي الأقسام... -->
        </main>
    </div>

    <script>
        // الكود الكامل سيأتي في الجزء التالي بسبب حدود الطول
        // سيحتوي على جميع الوظائف والميزات المطلوبة
    </script>
</body>
</html>
