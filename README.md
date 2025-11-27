<!DOCTYPE html>
<html lang="ar" dir="rtl">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>نظام Security Chaos Engineering - المجموعة 1</title>
    <style>
        :root {
            --primary-color: #007AFF;
            --secondary-color: #5856D6;
            --success-color: #34C759;
            --warning-color: #FF9500;
            --danger-color: #FF3B30;
            --bg-color: #FFFFFF;
            --sidebar-bg: #F2F2F7;
            --text-color: #000000;
            --text-secondary: #8E8E93;
            --border-color: #C6C6C8;
            --card-bg: #FFFFFF;
        }

        [data-theme="dark"] {
            --bg-color: #000000;
            --sidebar-bg: #1C1C1E;
            --text-color: #FFFFFF;
            --text-secondary: #8E8E93;
            --border-color: #38383A;
            --card-bg: #1C1C1E;
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
            justify-content: between;
            align-items: center;
            margin-bottom: 30px;
            padding-bottom: 20px;
            border-bottom: 1px solid var(--border-color);
        }

        .header h2 {
            font-size: 28px;
            font-weight: 700;
        }

        .theme-toggle {
            background: none;
            border: none;
            color: var(--text-color);
            cursor: pointer;
            padding: 10px;
            border-radius: 50%;
            transition: background-color 0.2s ease;
        }

        .theme-toggle:hover {
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
        }

        .card h3 {
            font-size: 20px;
            font-weight: 600;
            margin-bottom: 15px;
        }

        /* المساعد الذكي */
        .chat-container {
            max-width: 800px;
            margin: 0 auto;
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

        .chat-input {
            display: flex;
            gap: 10px;
        }

        .chat-input input {
            flex: 1;
            padding: 12px 16px;
            border: 1px solid var(--border-color);
            border-radius: 20px;
            background-color: var(--card-bg);
            color: var(--text-color);
            font-size: 16px;
        }

        .chat-input button {
            background-color: var(--primary-color);
            color: white;
            border: none;
            padding: 12px 24px;
            border-radius: 20px;
            cursor: pointer;
            font-size: 16px;
            transition: background-color 0.2s ease;
        }

        .chat-input button:hover {
            background-color: #0056CC;
        }

        /* التوثيق */
        .docs-grid {
            display: grid;
            grid-template-columns: repeat(auto-fit, minmax(300px, 1fr));
            gap: 20px;
            margin-top: 20px;
        }

        .doc-card {
            background-color: var(--card-bg);
            border: 1px solid var(--border-color);
            border-radius: 12px;
            padding: 20px;
            transition: transform 0.2s ease;
        }

        .doc-card:hover {
            transform: translateY(-2px);
        }

        .doc-card h4 {
            font-size: 18px;
            margin-bottom: 10px;
            color: var(--primary-color);
        }

        /* التقنيات */
        .tech-grid {
            display: grid;
            grid-template-columns: repeat(auto-fit, minmax(250px, 1fr));
            gap: 20px;
            margin-top: 20px;
        }

        .tech-item {
            background: linear-gradient(135deg, var(--primary-color), var(--secondary-color));
            color: white;
            padding: 25px;
            border-radius: 12px;
            text-align: center;
        }

        .tech-item h4 {
            font-size: 18px;
            margin-bottom: 10px;
        }

        /* دراسات الحالة */
        .case-study {
            background-color: var(--card-bg);
            border: 1px solid var(--border-color);
            border-radius: 12px;
            padding: 25px;
            margin-bottom: 20px;
        }

        .case-study h4 {
            color: var(--primary-color);
            margin-bottom: 10px;
        }

        /* التوثيق الكامل */
        .full-docs {
            max-width: 900px;
            margin: 0 auto;
        }

        .doc-section {
            margin-bottom: 40px;
        }

        .doc-section h3 {
            color: var(--primary-color);
            margin-bottom: 15px;
            padding-bottom: 10px;
            border-bottom: 2px solid var(--primary-color);
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
                    <i>📊</i> لوحة التحكم
                </div>
                <div class="nav-item" data-section="assistant">
                    <i>🤖</i> المساعد الذكي
                </div>
                <div class="nav-item" data-section="documentation">
                    <i>📚</i> التوثيق
                </div>
            </div>

            <div class="nav-section">
                <h3>التقنيات والأدوات</h3>
                <div class="nav-item" data-section="technologies">
                    <i>⚙️</i> التقنيات المستخدمة
                </div>
                <div class="nav-item" data-section="case-studies">
                    <i>🔬</i> دراسات الحالة
                </div>
            </div>

            <div class="nav-section">
                <h3>المشروع الكامل</h3>
                <div class="nav-item" data-section="full-project">
                    <i>📁</i> المشروع الكامل - التوثيق
                </div>
            </div>
        </nav>

        <!-- المحتوى الرئيسي -->
        <main class="main-content">
            <div class="header">
                <h2>لوحة تحكم Security Chaos Engineering</h2>
                <button class="theme-toggle" id="themeToggle">
                    تبديل الوضع
                </button>
            </div>

            <!-- قسم لوحة التحكم -->
            <section id="dashboard" class="section active">
                <div class="card">
                    <h3>مرحباً بك في نظام Security Chaos Engineering</h3>
                    <p>هذا النظام يقدم حلولاً متكاملة للهندسة الفوضوية الآمنة في بيئات السحابة الإلكترونية.</p>
                </div>

                <div class="card">
                    <h3>نظرة عامة</h3>
                    <p>Security Chaos Engineering هو تخصص يهدف إلى بناء أنظمة مرنة وقادرة على تحمل الهجمات من خلال محاكاة الظروف الفوضوية في بيئات الإنتاج.</p>
                </div>

                <div class="docs-grid">
                    <div class="doc-card">
                        <h4>المساعد الذكي</h4>
                        <p>مساعد متخصص في Security Chaos Engineering والشبكات السحابية</p>
                    </div>
                    <div class="doc-card">
                        <h4>التوثيق الشامل</h4>
                        <p>جميع الوثائق والمصادر المتعلقة بالمشروع</p>
                    </div>
                    <div class="doc-card">
                        <h4>التقنيات المستخدمة</h4>
                        <p>أدوات ومنهجيات Security Chaos Engineering</p>
                    </div>
                </div>
            </section>

            <!-- قسم المساعد الذكي -->
            <section id="assistant" class="section">
                <div class="card">
                    <h3>المساعد الذكي المتخصص</h3>
                    <p>مساعد ذكي متخصص في Security Chaos Engineering والشبكات السحابية. يمكنه الإجابة على أسئلتك في هذه المجالات.</p>
                </div>

                <div class="chat-container">
                    <div class="chat-messages" id="chatMessages">
                        <div class="message assistant-message">
                            مرحباً! أنا مساعدك المتخصص في Security Chaos Engineering والشبكات السحابية. كيف يمكنني مساعدتك؟
                        </div>
                    </div>
                    <div class="chat-input">
                        <input type="text" id="userInput" placeholder="اكتب سؤالك هنا عن Security Chaos Engineering أو الشبكات السحابية...">
                        <button onclick="sendMessage()">إرسال</button>
                    </div>
                </div>
            </section>

            <!-- قسم التوثيق -->
            <section id="documentation" class="section">
                <div class="card">
                    <h3>توثيق Security Chaos Engineering</h3>
                    <p>الوثائق والمصادر التعليمية المتعلقة بالهندسة الفوضوية الآمنة.</p>
                </div>

                <div class="docs-grid">
                    <div class="doc-card">
                        <h4>المفاهيم الأساسية</h4>
                        <p>تعريف Security Chaos Engineering ومبادئه الأساسية</p>
                    </div>
                    <div class="doc-card">
                        <h4>أدوات التنفيذ</h4>
                        <p>Chaos Monkey, Gremlin, وأدوات أخرى</p>
                    </div>
                    <div class="doc-card">
                        <h4>أفضل الممارسات</h4>
                        <p>كيفية تطبيق SEC بشكل فعال</p>
                    </div>
                    <div class="doc-card">
                        <h4>دراسات الحالة</h4>
                        <p>تطبيقات عملية من شركات رائدة</p>
                    </div>
                </div>
            </section>

            <!-- قسم التقنيات -->
            <section id="technologies" class="section">
                <div class="card">
                    <h3>التقنيات والأدوات</h3>
                    <p>أهم التقنيات والأدوات المستخدمة في Security Chaos Engineering.</p>
                </div>

                <div class="tech-grid">
                    <div class="tech-item">
                        <h4>Chaos Monkey</h4>
                        <p>أداة Netflix لاختبار مرونة الأنظمة</p>
                    </div>
                    <div class="tech-item">
                        <h4>Gremlin</h4>
                        <p>منصة متكاملة لاختبار الفوضى</p>
                    </div>
                    <div class="tech-item">
                        <h4>AWS Fault Injection</h4>
                        <p>خدمة AWS لمحاكاة الأعطال</p>
                    </div>
                    <div class="tech-item">
                        <h4>Chaos Toolkit</h4>
                        <p>إطار عمل مفتوح المصدر</p>
                    </div>
                </div>
            </section>

            <!-- قسم دراسات الحالة -->
            <section id="case-studies" class="section">
                <div class="card">
                    <h3>دراسات الحالة</h3>
                    <p>تطبيقات عملية لـ Security Chaos Engineering في شركات عالمية.</p>
                </div>

                <div class="case-study">
                    <h4>Netflix - Chaos Engineering</h4>
                    <p>كيف تستخدم Netflix الهندسة الفوضوية لضمان مرونة أنظمتها</p>
                </div>

                <div class="case-study">
                    <h4>Amazon AWS</h4>
                    <p>تطبيق مفاهيم SEC في خدمات AWS</p>
                </div>

                <div class="case-study">
                    <h4>Microsoft Azure</h4>
                    <p>استراتيجيات SEC في بيئة Azure السحابية</p>
                </div>
            </section>

            <!-- قسم المشروع الكامل -->
            <section id="full-project" class="section">
                <div class="full-docs">
                    <div class="card">
                        <h3>المشروع الكامل - التوثيق الشامل</h3>
                        <p>جميع أقسام المشروع في مكان واحد.</p>
                    </div>

                    <div class="doc-section">
                        <h3>مقدمة في Security Chaos Engineering</h3>
                        <p>Security Chaos Engineering هو نهج استباقي لبناء أنظمة قادرة على تحمل الهجمات والظروف غير المتوقعة.</p>
                    </div>

                    <div class="doc-section">
                        <h3>الأدوات والتقنيات</h3>
                        <p>تشمل الأدوات الرئيسية: Chaos Monkey, Gremlin, AWS Fault Injection Service, Chaos Toolkit.</p>
                    </div>

                    <div class="doc-section">
                        <h3>أفضل الممارسات</h3>
                        <p>ابدأ في بيئات غير إنتاجية، استخدم حوادث سابقة كمصدر للتجارب، وثق النتائج.</p>
                    </div>

                    <div class="doc-section">
                        <h3>التكامل مع السحابة</h3>
                        <p>كيفية تطبيق SEC في بيئات AWS, Azure, Google Cloud.</p>
                    </div>
                </div>
            </section>
        </main>
    </div>

    <script>
        // إدارة التنقل بين الأقسام
        document.querySelectorAll('.nav-item').forEach(item => {
            item.addEventListener('click', function() {
                // إزالة النشاط من جميع العناصر
                document.querySelectorAll('.nav-item').forEach(nav => {
                    nav.classList.remove('active');
                });
                // إخفاء جميع الأقسام
                document.querySelectorAll('.section').forEach(section => {
                    section.classList.remove('active');
                });
                
                // تفعيل العنصر الحالي
                this.classList.add('active');
                // إظهار القسم المحدد
                const sectionId = this.getAttribute('data-section');
                document.getElementById(sectionId).classList.add('active');
            });
        });

        // تبديل الوضع الداكن
        const themeToggle = document.getElementById('themeToggle');
        themeToggle.addEventListener('click', function() {
            const currentTheme = document.documentElement.getAttribute('data-theme');
            if (currentTheme === 'dark') {
                document.documentElement.removeAttribute('data-theme');
                themeToggle.textContent = 'تبديل الوضع';
            } else {
                document.documentElement.setAttribute('data-theme', 'dark');
                themeToggle.textContent = 'تبديل الوضع';
            }
        });

        // المساعد الذكي
        function sendMessage() {
            const userInput = document.getElementById('userInput');
            const chatMessages = document.getElementById('chatMessages');
            const message = userInput.value.trim();

            if (message === '') return;

            // إضافة رسالة المستخدم
            const userMessage = document.createElement('div');
            userMessage.className = 'message user-message';
            userMessage.textContent = message;
            chatMessages.appendChild(userMessage);

            // مسح حقل الإدخال
            userInput.value = '';

            // إضافة رسالة المساعد (محاكاة)
            setTimeout(() => {
                const assistantMessage = document.createElement('div');
                assistantMessage.className = 'message assistant-message';
                assistantMessage.textContent = generateResponse(message);
                chatMessages.appendChild(assistantMessage);
                chatMessages.scrollTop = chatMessages.scrollHeight;
            }, 1000);

            chatMessages.scrollTop = chatMessages.scrollHeight;
        }

        function generateResponse(message) {
            const lowerMessage = message.toLowerCase();
            
            // الردود المتعلقة بـ Security Chaos Engineering
            if (lowerMessage.includes('chaos') || lowerMessage.includes('فوض')) {
                return 'Security Chaos Engineering هو منهجية لبناء أنظمة مرنة من خلال محاكاة الظروف الفوضوية في بيئات الإنتاج. يساعد في اكتشاف نقاط الضعف قبل أن يستغلها المهاجمون.';
            }
            
            if (lowerMessage.includes('netflix') || lowerMessage.includes('نيتفليكس')) {
                return 'Netflix هي الرائدة في هذا المجال مع أدوات مثل Chaos Monkey التي تقوم بإيقاف خوادم الإنتاج عشوائياً لاختبار مرونة النظام.';
            }
            
            if (lowerMessage.includes('aws') || lowerMessage.includes('سحابة')) {
                return 'في AWS، يمكن استخدام خدمات مثل Fault Injection Simulator لمحاكاة الأعطال واختبار مرونة التطبيقات السحابية.';
            }
            
            if (lowerMessage.includes('أدوات') || lowerMessage.includes('tools')) {
                return 'أهم أدوات Security Chaos Engineering تشمل: Chaos Monkey, Gremlin, Chaos Toolkit, AWS FIS, Azure Chaos Studio.';
            }
            
            if (lowerMessage.includes('ممارسات') || lowerMessage.includes('best practices')) {
                return 'أفضل الممارسات: ابدأ في بيئات غير إنتاجية، خطط للتجارب بعناية، وثق النتائج، واجعل التجارب قابلة للتكرار.';
            }

            // الردود العامة
            return 'أنا متخصص في Security Chaos Engineering والشبكات السحابية. يمكنني مساعدتك في مواضيع مثل أدوات SEC، التطبيق في السحابة، ودراسات الحالة العملية.';
        }

        // السماح بالإرسال بالزر Enter
        document.getElementById('userInput').addEventListener('keypress', function(e) {
            if (e.key === 'Enter') {
                sendMessage();
            }
        });

        // الكشف التلقائي عن وضع النظام
        if (window.matchMedia && window.matchMedia('(prefers-color-scheme: dark)').matches) {
            document.documentElement.setAttribute('data-theme', 'dark');
        }
    </script>
</body>
</html>
