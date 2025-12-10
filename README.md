<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="utf-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1" />
  <title>Security Chaos Engineering Dashboard</title>

  <!-- Tailwind via CDN -->
  <script src="https://cdn.tailwindcss.com"></script>
  <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">

  <!-- Firebase (compat) -->
  <script src="https://www.gstatic.com/firebasejs/9.22.1/firebase-app-compat.js"></script>
  <script src="https://www.gstatic.com/firebasejs/9.22.1/firebase-database-compat.js"></script>
  <script src="https://www.gstatic.com/firebasejs/9.22.1/firebase-storage-compat.js"></script>

  <style>
    :root{
      --overlay: rgba(2,6,23,0.55);
      --overlay-2: linear-gradient(180deg, rgba(2,6,23,0.45), rgba(2,6,23,0.65));
      --accent: linear-gradient(90deg,#06b6d4,#3b82f6);
    }

    html,body{height:100%; margin:0; font-family: Inter, system-ui, -apple-system, "Segoe UI", Roboto, "Helvetica Neue", Arial;}
    /* full-screen background */
    body {
      background: url('bg.jpg') center/cover no-repeat fixed;
      position: relative;
      color: #e6eef8;
    }
    /* overlay to darken image so text is readable */
    .bg-overlay {
      position: fixed; inset:0; z-index:-1;
      background: var(--overlay-2);
      mix-blend-mode: normal;
    }

    /* container */
    .layout { display:flex; min-height:100vh; gap:0; }

    /* sidebar */
    .sidebar {
      width:320px;
      max-width:28%;
      min-width:220px;
      background: rgba(8,11,26,0.55);
      backdrop-filter: blur(6px);
      border-right: 1px solid rgba(255,255,255,0.06);
      padding:24px;
      box-sizing:border-box;
      overflow:auto;
    }

    .sidebar .brand { color: white; font-weight:700; letter-spacing:0.2px; }
    .sidebar p { color: #cfe6ff; margin-top:6px; }

    .live-badge { background: linear-gradient(90deg,#10b981,#059669); color:white; padding:10px 12px; border-radius:12px; display:inline-flex; gap:8px; align-items:center; box-shadow: 0 6px 18px rgba(6,10,20,0.45); }

    /* nav */
    nav.section-list { margin-top:18px; display:flex; flex-direction:column; gap:10px; }
    .nav-btn {
      display:flex; gap:12px; align-items:center; padding:12px 14px;
      border-radius:14px; background: rgba(255,255,255,0.03); color:#eaf6ff; border: 1px solid rgba(255,255,255,0.03);
      cursor:pointer; text-align:left; font-weight:600;
    }
    .nav-btn i { width:22px; text-align:center; }
    .nav-btn:hover { transform:translateY(-2px); background: rgba(255,255,255,0.06); }

    .nav-btn.active { background: linear-gradient(90deg, rgba(59,130,246,0.18), rgba(3,105,161,0.12)); border-color: rgba(99,102,241,0.25); color: #ffffff; box-shadow: 0 6px 20px rgba(8,12,28,0.6); }

    /* main */
    .main { flex:1; padding:28px; overflow:auto; min-width: 0; }

    .card {
      background: linear-gradient(180deg, rgba(255,255,255,0.02), rgba(255,255,255,0.01));
      border: 1px solid rgba(255,255,255,0.04);
      padding:18px; border-radius:14px; color:#e6f0ff;
    }

    h1,h2,h3 { font-weight:700; color: #eaf6ff; }
    .title-accent { background: var(--accent); -webkit-background-clip: text; background-clip: text; color: transparent; font-weight:800; }

    /* content cards for lists */
    .content-item { border-radius:12px; padding:14px; margin-bottom:12px; background: rgba(255,255,255,0.03); border:1px solid rgba(255,255,255,0.03); }
    .content-meta { color: #bcd7ff; font-size:0.9rem; }

    /* truncated long text */
    .content-body { color:#dff0ff; white-space:pre-line; line-height:1.5; }

    /* small screens adjustments */
    @media (max-width: 1100px){
      .sidebar { width: 280px; }
    }
    @media (max-width: 820px){
      .layout { flex-direction:column; }
      .sidebar { width:100%; max-width:100%; border-right:none; border-bottom:1px solid rgba(255,255,255,0.04); }
      .main { padding:16px; }
    }

    .modal { display:none; position:fixed; inset:0; align-items:center; justify-content:center; z-index:60; }
    .modal.active { display:flex; }
    .modal-card { width:100%; max-width:920px; border-radius:14px; overflow:auto; }
    input, textarea, select { color:#07203a; }

    /* make edit/delete only visible when allowed */
    .action-btn { background:transparent; border:1px solid rgba(255,255,255,0.06); color:#d8f3ff; padding:8px 10px; border-radius:8px; cursor:pointer; }
    .action-btn:hover { transform:translateY(-2px); }

    /* smaller nav buttons for stacked look but readable */
    .nav-compact { display:flex; flex-direction:column; gap:10px; }
    .nav-compact .nav-btn { padding:10px; border-radius:12px; font-size:0.95rem; }

    /* show a thin vertical separator to right of sidebar */
    .sidebar-sep { border-right:1px solid rgba(255,255,255,0.03); }

    /* pdf list */
    .pdf-row { display:flex; justify-content:space-between; align-items:center; gap:12px; padding:12px; border-radius:10px; background: rgba(255,255,255,0.02); }
  </style>
</head>
<body>
  <div class="bg-overlay" aria-hidden="true"></div>

  <div class="layout">
    <!-- SIDEBAR -->
    <aside class="sidebar sidebar-sep" role="navigation">
      <div>
        <div class="brand text-2xl">Security Chaos Engineering</div>
        <p class="text-sm">Group 1 Dashboard · collaborative</p>

        <div style="margin-top:14px;" class="live-badge">
          <span style="width:10px;height:10px;border-radius:50%;background:#1ee3a7;display:inline-block;"></span>
          <span style="font-weight:700;">LIVE UPDATES ACTIVE</span>
        </div>
      </div>

      <div style="margin-top:18px" class="card">
        <div style="display:flex; gap:12px; align-items:center;">
          <div id="userAvatar" style="width:48px;height:48px;border-radius:10px;background:linear-gradient(90deg,#06b6d4,#3b82f6);display:flex;align-items:center;justify-content:center;color:white;font-weight:700;">Y</div>
          <div>
            <div id="userName" style="font-weight:800;color:#f1fbff;">You</div>
            <div id="userRole" style="color:#bcd7ff;font-size:0.9rem">Active User</div>
          </div>
        </div>
        <div style="margin-top:12px;display:flex;gap:8px;">
          <button onclick="openTeamChat()" class="live-badge" style="flex:1;justify-content:center;"><i class="fas fa-comments"></i> Team Chat</button>
        </div>
      </div>

      <div style="margin-top:12px;" class="card">
        <div style="font-weight:700;color:#eaf6ff;margin-bottom:8px;">Team Members</div>
        <div id="liveTeamList" style="display:flex;flex-direction:column;gap:8px;max-height:180px;overflow:auto;"></div>
      </div>

      <nav class="section-list nav-compact" style="margin-top:12px;">
        <!-- default active -->
        <button class="nav-btn active" data-section="overview"><i class="fas fa-home"></i> Overview</button>
        <button class="nav-btn" data-section="project-documentation"><i class="fas fa-folder"></i> Project Documentation</button>
        <button class="nav-btn" data-section="team-collaboration"><i class="fas fa-users"></i> Team Collaboration</button>
        <button class="nav-btn" data-section="team-updates"><i class="fas fa-bullhorn"></i> Team Updates</button>
        <button class="nav-btn" data-section="ai-assistant"><i class="fas fa-robot"></i> AI Assistant</button>
        <button class="nav-btn" data-section="practical-tasks"><i class="fas fa-tasks"></i> Practical Tasks (Summary)</button>

        <!-- separate actual tasks -->
        <div style="margin-top:6px;color:#cfe6ff;font-weight:700;">Practical Tasks (Open Org.)</div>
        <button class="nav-btn" data-section="task-01"><i class="fas fa-check-circle"></i> TASK 01 – Debug a Broken Feature</button>
        <button class="nav-btn" data-section="task-02"><i class="fas fa-toolbox"></i> TASK 02 – Build a Mini Tool</button>
        <button class="nav-btn" data-section="task-03"><i class="fas fa-columns"></i> TASK 03 – Responsive Component</button>
        <button class="nav-btn" data-section="task-04"><i class="fas fa-search"></i> TASK 04 – Reverse Engineer a Result</button>
        <button class="nav-btn" data-section="task-05"><i class="fas fa-file-code"></i> TASK 05 – XWiki LaTeX Math Rendering</button>

        <button class="nav-btn" data-section="pdf-library"><i class="fas fa-file-pdf"></i> PDF Library</button>
        <button class="nav-btn" data-section="implementation"><i class="fas fa-code"></i> Implementation</button>
        <button class="nav-btn" data-section="research"><i class="fas fa-search"></i> Research</button>
        <button class="nav-btn" data-section="resources"><i class="fas fa-book"></i> Resources</button>
        <button class="nav-btn" data-section="security-testing"><i class="fas fa-shield-alt"></i> Security Testing</button>
        <button class="nav-btn" data-section="monitoring-analytics"><i class="fas fa-chart-line"></i> Monitoring & Analytics</button>

      </nav>

      <div style="margin-top:18px;">
        <button onclick="toggleTheme()" class="nav-btn" style="justify-content:center"><i class="fas fa-moon"></i> Toggle Theme (alt)</button>
      </div>

      <div id="adminPanel" style="margin-top:12px;"></div>

    </aside>

    <!-- MAIN -->
    <main class="main">
      <header style="display:flex;justify-content:space-between;align-items:center;margin-bottom:18px;">
        <div>
          <h1 class="title-accent text-3xl">Security Chaos Engineering Dashboard</h1>
          <div style="color:#bcd7ff;margin-top:6px;">Group 1 — Real-time Team Collaboration</div>
        </div>
        <div style="display:flex;gap:10px;align-items:center;">
          <div class="card" style="padding:8px 12px;border-radius:10px;">
            <button onclick="openAddContent('overview')" class="action-btn"><i class="fas fa-plus"></i> Add Content</button>
          </div>
        </div>
      </header>

      <!-- Overview -->
      <section class="card section-content" id="overview" style="display:block;">
        <div style="display:flex;justify-content:space-between;align-items:center;margin-bottom:12px;">
          <h2>Overview</h2>
          <div style="display:flex;gap:8px;">
            <button onclick="openAddContent('overview')" class="action-btn"><i class="fas fa-plus"></i> Add</button>
            <button onclick="openPdfUpload()" class="action-btn"><i class="fas fa-file-pdf"></i> Upload PDF</button>
          </div>
        </div>
        <div id="overview-content" style="margin-bottom:14px;"></div>

        <div style="display:grid;grid-template-columns:repeat(auto-fit,minmax(180px,1fr));gap:12px;">
          <div class="card" style="text-align:center;">
            <div style="font-size:22px;font-weight:800;" id="stat-members">7</div>
            <div style="opacity:0.9;color:#cfe6ff;">Team Members</div>
          </div>
          <div class="card" style="text-align:center;">
            <div style="font-size:22px;font-weight:800;" id="stat-sections">14</div>
            <div style="opacity:0.9;color:#cfe6ff;">Sections</div>
          </div>
          <div class="card" style="text-align:center;">
            <div style="font-size:22px;font-weight:800;">✓</div>
            <div style="opacity:0.9;color:#cfe6ff;">Chat Working</div>
          </div>
          <div class="card" style="text-align:center;">
            <div style="font-size:22px;font-weight:800;">100%</div>
            <div style="opacity:0.9;color:#cfe6ff;">Functional</div>
          </div>
        </div>
      </section>

      <!-- project-documentation -->
      <section class="card section-content" id="project-documentation" style="display:none;">
        <div style="display:flex;justify-content:space-between;align-items:center;">
          <h2>Project Documentation</h2>
          <button onclick="openAddContent('project-documentation')" class="action-btn">Add</button>
        </div>
        <div id="project-documentation-content" style="margin-top:12px;"></div>
      </section>

      <!-- team-collaboration -->
      <section class="card section-content" id="team-collaboration" style="display:none;">
        <div style="display:flex;justify-content:space-between;align-items:center;">
          <h2>Team Collaboration</h2>
          <button onclick="openAddContent('team-collaboration')" class="action-btn">Add</button>
        </div>
        <div id="team-collaboration-content" style="margin-top:12px;"></div>
      </section>

      <!-- team-updates -->
      <section class="card section-content" id="team-updates" style="display:none;">
        <div style="display:flex;justify-content:space-between;align-items:center;">
          <h2>Team Updates</h2>
          <button onclick="openAddContent('team-updates')" class="action-btn">Add</button>
        </div>
        <div id="team-updates-content" style="margin-top:12px;"></div>
      </section>

      <!-- ai-assistant -->
      <section class="card section-content" id="ai-assistant" style="display:none;">
        <div style="display:flex;justify-content:space-between;align-items:center;">
          <h2>AI Assistant</h2>
          <button onclick="openAddContent('ai-assistant')" class="action-btn">Add</button>
        </div>
        <div id="ai-assistant-content" style="margin-top:12px;"></div>
      </section>

      <!-- practical-tasks summary (keeps a landing page) -->
      <section class="card section-content" id="practical-tasks" style="display:none;">
        <div style="display:flex;justify-content:space-between;align-items:center;">
          <h2>Practical Tasks — Summary</h2>
          <div style="display:flex;gap:8px;">
            <button onclick="openAddContent('practical-tasks')" class="action-btn">Add Summary</button>
            <button onclick="openPdfUpload()" class="action-btn"><i class="fas fa-file-pdf"></i> Upload PDF</button>
          </div>
        </div>
        <div style="margin-top:14px;">
          <div style="padding:12px;background:rgba(255,255,255,0.02);border-radius:10px;">
            <strong>NOTE:</strong> Each practical task has its own section (use sidebar). This page is a short landing overview.
          </div>
        </div>

        <div id="practical-tasks-content" style="margin-top:12px;"></div>
      </section>

      <!-- TASK SECTIONS (each task gets its own page) -->
      <section class="card section-content" id="task-01" style="display:none;">
        <div style="display:flex;justify-content:space-between;align-items:center;">
          <h2>TASK 01 – Debug a Broken Feature</h2>
          <div><button onclick="openAddContent('task-01')" class="action-btn">Add Note</button></div>
        </div>
        <div id="task-01-content" style="margin-top:12px;"></div>
      </section>

      <section class="card section-content" id="task-02" style="display:none;">
        <div style="display:flex;justify-content:space-between;align-items:center;">
          <h2>TASK 02 – Build a Mini Tool</h2>
          <div><button onclick="openAddContent('task-02')" class="action-btn">Add Note</button></div>
        </div>
        <div id="task-02-content" style="margin-top:12px;"></div>
      </section>

      <section class="card section-content" id="task-03" style="display:none;">
        <div style="display:flex;justify-content:space-between;align-items:center;">
          <h2>TASK 03 – Responsive Two-Column Web Component</h2>
          <div><button onclick="openAddContent('task-03')" class="action-btn">Add Note</button></div>
        </div>
        <div id="task-03-content" style="margin-top:12px;"></div>
      </section>

      <section class="card section-content" id="task-04" style="display:none;">
        <div style="display:flex;justify-content:space-between;align-items:center;">
          <h2>TASK 04 – Reverse Engineer a Result</h2>
          <div><button onclick="openAddContent('task-04')" class="action-btn">Add Note</button></div>
        </div>
        <div id="task-04-content" style="margin-top:12px;"></div>
      </section>

      <section class="card section-content" id="task-05" style="display:none;">
        <div style="display:flex;justify-content:space-between;align-items:center;">
          <h2>TASK 05 – XWiki LaTeX Math Rendering</h2>
          <div><button onclick="openAddContent('task-05')" class="action-btn">Add Note</button></div>
        </div>
        <div id="task-05-content" style="margin-top:12px;"></div>
      </section>

      <!-- PDF Library -->
      <section class="card section-content" id="pdf-library" style="display:none;">
        <div style="display:flex;justify-content:space-between;align-items:center;">
          <h2>PDF Library</h2>
          <div style="display:flex;gap:8px;">
            <button onclick="openPdfUpload()" class="action-btn"><i class="fas fa-upload"></i> Upload PDF</button>
            <button onclick="openAddContent('pdf-library')" class="action-btn"><i class="fas fa-plus"></i> Add Metadata</button>
          </div>
        </div>
        <div id="pdf-library-content" style="margin-top:12px;"></div>
      </section>

      <!-- implementation -->
      <section class="card section-content" id="implementation" style="display:none;">
        <div style="display:flex;justify-content:space-between;align-items:center;">
          <h2>Implementation</h2>
          <button onclick="openAddContent('implementation')" class="action-btn">Add</button>
        </div>
        <div id="implementation-content" style="margin-top:12px;"></div>
      </section>

      <!-- research -->
      <section class="card section-content" id="research" style="display:none;">
        <div style="display:flex;justify-content:space-between;align-items:center;">
          <h2>Research</h2>
          <button onclick="openAddContent('research')" class="action-btn">Add</button>
        </div>
        <div id="research-content" style="margin-top:12px;"></div>
      </section>

      <!-- resources -->
      <section class="card section-content" id="resources" style="display:none;">
        <div style="display:flex;justify-content:space-between;align-items:center;">
          <h2>Resources</h2>
          <button onclick="openAddContent('resources')" class="action-btn">Add</button>
        </div>
        <div id="resources-content" style="margin-top:12px;"></div>
      </section>

      <!-- security-testing -->
      <section class="card section-content" id="security-testing" style="display:none;">
        <div style="display:flex;justify-content:space-between;align-items:center;">
          <h2>Security Testing</h2>
          <button onclick="openAddContent('security-testing')" class="action-btn">Add</button>
        </div>
        <div id="security-testing-content" style="margin-top:12px;"></div>
      </section>

      <!-- monitoring-analytics -->
      <section class="card section-content" id="monitoring-analytics" style="display:none;">
        <div style="display:flex;justify-content:space-between;align-items:center;">
          <h2>Monitoring & Analytics</h2>
          <button onclick="openAddContent('monitoring-analytics')" class="action-btn">Add</button>
        </div>
        <div id="monitoring-analytics-content" style="margin-top:12px;"></div>
      </section>

    </main>
  </div>

  <!-- MODALS -->
  <div id="addContentModal" class="modal">
    <div class="modal-card card modal-card" style="max-width:720px;">
      <div style="display:flex;justify-content:space-between;align-items:center;">
        <h2 id="modalTitle">Add Content</h2>
        <button onclick="closeAddContent()" class="action-btn"><i class="fas fa-times"></i></button>
      </div>
      <form id="addContentForm" style="margin-top:12px;display:flex;flex-direction:column;gap:10px;padding:12px;">
        <input type="hidden" id="contentSection" />
        <input type="hidden" id="editContentId" />
        <div>
          <label style="font-weight:700;color:#cfe6ff;">Title</label>
          <input id="contentTitle" required class="w-full p-3 rounded-md" />
        </div>
        <div>
          <label style="font-weight:700;color:#cfe6ff;">Content</label>
          <textarea id="contentDescription" required class="w-full p-3 rounded-md" rows="10"></textarea>
        </div>
        <div>
          <label style="font-weight:700;color:#cfe6ff;">Author</label>
          <select id="contentAuthor" class="w-full p-3 rounded-md">
            <option>Kaled Osman</option>
            <option>Marcus Tibell</option>
            <option>Jens Annell</option>
            <option>Fahad Hussain</option>
            <option>Luwam</option>
            <option>Stefan Österberg</option>
            <option>Najmaddin</option>
          </select>
        </div>

        <div style="display:flex;gap:8px;">
          <button type="submit" class="action-btn" style="flex:1;background:linear-gradient(90deg,#06b6d4,#3b82f6);color:#002233;border:none;">Save</button>
          <button type="button" onclick="closeAddContent()" class="action-btn" style="flex:1;">Cancel</button>
        </div>
      </form>
    </div>
  </div>

  <!-- PDF Modal -->
  <div id="pdfUploadModal" class="modal">
    <div class="modal-card card" style="max-width:640px;">
      <div style="display:flex;justify-content:space-between;align-items:center;">
        <h2>Upload PDF</h2>
        <button onclick="closePdfUpload()" class="action-btn"><i class="fas fa-times"></i></button>
      </div>
      <form id="pdfUploadForm" style="margin-top:12px;display:flex;flex-direction:column;gap:10px;">
        <div>
          <label style="font-weight:700;color:#cfe6ff;">Title</label>
          <input id="pdfTitle" required class="w-full p-3 rounded-md" />
        </div>
        <div>
          <label style="font-weight:700;color:#cfe6ff;">Description</label>
          <textarea id="pdfDescription" required class="w-full p-3 rounded-md" rows="4"></textarea>
        </div>
        <div>
          <label style="font-weight:700;color:#cfe6ff;">Choose PDF</label>
          <input id="pdfFile" type="file" accept="application/pdf" />
        </div>
        <div style="display:flex;gap:8px;">
          <button type="submit" class="action-btn" style="flex:1;background:linear-gradient(90deg,#3b82f6,#06b6d4);color:white;border:none;">Upload</button>
          <button type="button" onclick="closePdfUpload()" class="action-btn" style="flex:1;">Cancel</button>
        </div>
        <div id="pdfUploadProgress" style="color:#cfe6ff;font-size:0.9rem;"></div>
      </form>
    </div>
  </div>

  <!-- Team Chat -->
  <div id="teamChatModal" class="modal">
    <div class="modal-card card" style="max-width:720px;">
      <div style="display:flex;justify-content:space-between;align-items:center;">
        <h3 style="display:flex;align-items:center;gap:8px;"><span style="width:10px;height:10px;border-radius:50%;background:#10b981;display:inline-block;"></span> Team Chat <span id="messageCount" style="background:#3b82f6;color:#003055;padding:4px 8px;border-radius:8px;font-size:0.9rem;margin-left:6px;">0</span></h3>
        <button onclick="closeTeamChat()" class="action-btn"><i class="fas fa-times"></i></button>
      </div>
      <div id="chatMessages" style="margin-top:10px;max-height:320px;overflow:auto;padding:8px;background:rgba(1,6,20,0.2);border-radius:8px;">
        <div style="text-align:center;color:#bcd7ff;padding:30px;"><i class="fas fa-comments fa-2x"></i><div>Loading chat messages...</div></div>
      </div>
      <div style="display:flex;gap:8px;margin-top:10px;">
        <input id="chatInput" placeholder="Type message..." class="p-3 rounded-md" style="flex:1;">
        <button id="sendChatButton" class="action-btn" style="background:linear-gradient(90deg,#06b6d4,#3b82f6);color:white;border:none;"><i class="fas fa-paper-plane"></i></button>
      </div>
    </div>
  </div>

  <!-- Login Modal (shown initially) -->
  <div id="loginModal" class="modal active" style="display:flex;align-items:center;justify-content:center;">
    <div class="modal-card card" style="max-width:520px;">
      <div style="display:flex;justify-content:space-between;align-items:center;">
        <h2>Login to Dashboard</h2>
      </div>
      <form id="loginForm" style="margin-top:12px;display:flex;flex-direction:column;gap:10px;">
        <div>
          <label style="font-weight:700;color:#cfe6ff;">Email</label>
          <input id="loginEmail" type="email" class="w-full p-3 rounded-md" required />
        </div>
        <div>
          <label style="font-weight:700;color:#cfe6ff;">Password</label>
          <input id="loginPassword" type="password" class="w-full p-3 rounded-md" required />
        </div>
        <div style="display:flex;gap:8px;">
          <button type="submit" class="action-btn" style="flex:1;background:linear-gradient(90deg,#06b6d4,#3b82f6);color:#002233;border:none;">Login</button>
          <button type="button" onclick="closeLogin()" class="action-btn" style="flex:1;">Cancel</button>
        </div>

        <div style="margin-top:8px;background:rgba(255,255,255,0.02);padding:8px;border-radius:8px;color:#cfe6ff;">
          <strong>Demo Accounts</strong>
          <div style="font-size:0.9rem;margin-top:6px;">
            kaled@team.com - kaled123<br>
            marcus@team.com - marcus123<br>
            jens@team.com - jens123<br>
            fahad@team.com - fahad123<br>
            luwam@team.com - luwam123<br>
            stefan@team.com - stefan123<br>
            najmaddin@team.com - najmaddin123
          </div>
        </div>
      </form>
    </div>
  </div>

  <!-- SCRIPTS -->
  <script>
  (function(){
    // ---------- Firebase config (from user) ----------
    const DATABASE_URL = 'https://fir-console-df3e9-default-rtdb.europe-west1.firebasedatabase.app';
    const firebaseConfig = {
      apiKey: "AIzaSyDJsZ4LZVrBucavpTdhXbKxyE_BFeZFFKs",
      authDomain: "fir-console-df3e9.firebaseapp.com",
      databaseURL: DATABASE_URL,
      projectId: "fir-console-df3e9",
      storageBucket: "fir-console-df3e9.firebasestorage.app",
      messagingSenderId: "750795336412",
      appId: "1:750795336412:web:abfd0c06941a9418abe219"
    };
    try { if (!firebase.apps.length) firebase.initializeApp(firebaseConfig); } catch(e){ console.warn(e); }
    const db = firebase.database();
    const storage = firebase.storage();

    // Helper
    const $ = id => document.getElementById(id);
    const sections = [
      'overview','project-documentation','team-collaboration','team-updates',
      'ai-assistant','practical-tasks','task-01','task-02','task-03','task-04','task-05',
      'implementation','research','resources','security-testing','monitoring-analytics','pdf-library'
    ];

    function escapeHtml(s=''){ return String(s).replaceAll('&','&amp;').replaceAll('<','&lt;').replaceAll('>','&gt;'); }

    // ---------- Chat ----------
    const chat = {
      currentUser: 'You', messageCount:0, unread:0, lastRead:Date.now(),
      init(){
        const saved = JSON.parse(localStorage.getItem('currentUser'));
        if (saved && saved.name) this.currentUser = saved.name;
        if ($('chatStatus')) $('chatStatus').style.background = '#10B981';
        this.listenMessages();
        const sendBtn = $('sendChatButton');
        if (sendBtn) sendBtn.addEventListener('click', ()=>this.send());
        const input = $('chatInput');
        if (input) input.addEventListener('keypress', (e)=>{ if (e.key==='Enter') this.send(); });
      },
      listenMessages(){
        const ref = db.ref('team-chat').limitToLast(200);
        ref.off();
        ref.on('child_added', snap =>{
          const msg = snap.val();
          this.display(msg);
          this.messageCount++;
          $('messageCount').textContent = this.messageCount;
          if (msg.timestamp > this.lastRead && !this.isOpen()) { this.unread++; }
        });
      },
      async send(){
        const input = $('chatInput');
        if (!input) return;
        const text = input.value.trim();
        if (!text) return;
        const payload = { user: this.currentUser || 'You', text, timestamp: Date.now() };
        try { await db.ref('team-chat').push(payload); input.value=''; this.lastRead=Date.now(); this.unread=0; } catch(e){ alert('Failed to send'); console.error(e); }
      },
      display(msg){
        const list = $('chatMessages');
        if (!list) return;
        const placeholder = list.querySelector('.text-center');
        if (placeholder) list.innerHTML='';
        const el = document.createElement('div');
        const mine = msg.user === this.currentUser;
        el.className = 'p-3 mb-2 rounded-lg';
        el.style.background = mine ? 'linear-gradient(90deg,#06b6d4,#3b82f6)' : 'rgba(255,255,255,0.02)';
        el.style.color = mine ? '#002233' : '#dff0ff';
        el.innerHTML = `<div style="font-weight:700">${escapeHtml(msg.user)}</div><div style="margin-top:6px">${escapeHtml(msg.text)}</div><div style="margin-top:6px;font-size:0.8rem;color:#bcd7ff;">${new Date(msg.timestamp).toLocaleTimeString()}</div>`;
        list.appendChild(el);
        list.scrollTop = list.scrollHeight;
      },
      open(){ $('teamChatModal').classList.add('active'); this.lastRead = Date.now(); this.unread = 0; },
      close(){ $('teamChatModal').classList.remove('active'); },
      isOpen(){ return $('teamChatModal').classList.contains('active'); }
    };

    // ---------- Login system ----------
    const loginSystem = {
      users: JSON.parse(localStorage.getItem('teamUsers')) || null,
      currentUser: JSON.parse(localStorage.getItem('currentUser')) || null,
      init(){
        if (!this.users) {
          const defaultUsers = [
            { id:1, name:"Kaled Osman", email:"kaled@team.com", password:"kaled123", role:"Project Lead", avatar:"K" },
            { id:2, name:"Marcus Tibell", email:"marcus@team.com", password:"marcus123", role:"Engineer", avatar:"M" },
            { id:3, name:"Jens Annell", email:"jens@team.com", password:"jens123", role:"Analyst", avatar:"J" },
            { id:4, name:"Fahad Hussain", email:"fahad@team.com", password:"fahad123", role:"Researcher", avatar:"F" },
            { id:5, name:"Luwam", email:"luwam@team.com", password:"luwam123", role:"Designer", avatar:"L" },
            { id:6, name:"Stefan Österberg", email:"stefan@team.com", password:"stefan123", role:"Architect", avatar:"S" },
            { id:7, name:"Najmaddin", email:"najmaddin@team.com", password:"najmaddin123", role:"Security Expert", avatar:"N" }
          ];
          this.users = defaultUsers;
          localStorage.setItem('teamUsers', JSON.stringify(defaultUsers));
        }
        if (this.currentUser) this.updateUIForLoggedIn();
        document.getElementById('loginForm').addEventListener('submit',(e)=>{ e.preventDefault(); this.attemptLogin(); });
      },
      attemptLogin(){
        const email = $('loginEmail').value.trim();
        const pass = $('loginPassword').value.trim();
        const found = this.users.find(u => u.email===email && u.password===pass);
        if (!found) { alert('Invalid credentials'); return; }
        this.currentUser = found;
        localStorage.setItem('currentUser', JSON.stringify(found));
        this.updateUIForLoggedIn();
        chat.currentUser = found.name;
        $('loginModal').classList.remove('active');
        adminSystem.tryInit();
      },
      logout(){
        this.currentUser = null;
        localStorage.removeItem('currentUser');
        $('loginModal').classList.add('active');
        $('userName').textContent = 'You';
        $('userRole').textContent = 'Active User';
        $('userAvatar').textContent = 'Y';
        chat.currentUser = 'You';
        adminSystem.tryInit();
      },
      updateUIForLoggedIn(){
        const u = this.currentUser;
        if (!u) return;
        $('userName').textContent = u.name;
        $('userRole').textContent = u.role;
        $('userAvatar').textContent = u.avatar || u.name.charAt(0);
      }
    };

    // ---------- Admin ----------
    const adminSystem = {
      tryInit(){
        const cur = JSON.parse(localStorage.getItem('currentUser')) || null;
        const panel = $('adminPanel');
        if (!panel) return;
        if (cur && cur.email === 'kaled@team.com') {
          panel.innerHTML = `<div style="margin-top:14px;" class="card">
            <div style="font-weight:800;color:#ffdcbf;">Admin Panel</div>
            <div style="display:flex;flex-direction:column;gap:8px;margin-top:8px;">
              <button id="adminClearContent" class="action-btn" style="background:#ef4444;color:white;border:none;">Delete All Content</button>
              <button id="adminClearChat" class="action-btn" style="background:#7c3aed;color:white;border:none;">Clear Chat</button>
            </div>
          </div>`;
          $('adminClearContent').addEventListener('click', clearAllContentAdmin);
          $('adminClearChat').addEventListener('click', clearChatAdmin);
        } else {
          panel.innerHTML = '';
        }
      }
    };

    // ---------- Content system ----------
    const contentSystem = {
      renderSection(section, snapshot){
        const container = $(section+'-content');
        if (!container) return;
        container.innerHTML = '';
        if (!snapshot || !snapshot.exists()){
          container.innerHTML = `<div style="padding:18px;color:#cfe6ff;">No content yet for <strong>${section}</strong>.</div>`;
          return;
        }
        snapshot.forEach(child=>{
          const id = child.key, item = child.val();
          const el = document.createElement('div'); el.className='content-item';
          // show actions only if author matches current user OR admin
          const cur = JSON.parse(localStorage.getItem('currentUser')) || {};
          const isAdmin = cur && cur.email === 'kaled@team.com';
          const isOwner = cur && item.author && item.author === cur.name;
          let actionsHtml = '';
          if (isOwner || isAdmin) {
            actionsHtml = `<div style="display:flex;gap:8px;">
              <button class="action-btn edit-btn" data-id="${id}" data-section="${section}"><i class="fas fa-edit"></i> Edit</button>
              <button class="action-btn del-btn" data-id="${id}" data-section="${section}" style="background:#ef4444;color:white;"><i class="fas fa-trash"></i> Delete</button>
            </div>`;
          }
          el.innerHTML = `
            <div style="display:flex;justify-content:space-between;align-items:start;">
              <div style="max-width:78%;">
                <div style="font-weight:800;font-size:1.05rem;color:transparent; background:linear-gradient(90deg,#06b6d4,#3b82f6);-webkit-background-clip:text;">${escapeHtml(item.title)}</div>
                <div class="content-body" style="margin-top:8px;">${escapeHtml(item.description)}</div>
                <div style="margin-top:10px;font-size:0.9rem;color:#bcd7ff;">
                  <i class="fas fa-user"></i> ${escapeHtml(item.author || 'Group 1')} &nbsp; • &nbsp; <i class="fas fa-calendar"></i> ${new Date(item.date).toLocaleDateString()}
                </div>
              </div>
              <div style="min-width:110px;">${actionsHtml}</div>
            </div>
          `;
          container.appendChild(el);
        });

        // add handlers
        container.querySelectorAll('.edit-btn').forEach(b=>b.addEventListener('click', ()=> openEditModalFor(b.getAttribute('data-section'), b.getAttribute('data-id'))));
        container.querySelectorAll('.del-btn').forEach(b=>b.addEventListener('click', ()=> deleteContent(b.getAttribute('data-section'), b.getAttribute('data-id'))));
      },
      listenAll(){
        sections.forEach(sec=>{
          const ref = db.ref(`content/${sec}`);
          ref.off();
          ref.on('value', snap => this.renderSection(sec, snap));
        });
      },
      pushContent(section, payload){ return db.ref(`content/${section}`).push(payload); },
      updateContent(section, id, payload){ return db.ref(`content/${section}/${id}`).update(payload); },
      removeContent(section, id){ return db.ref(`content/${section}/${id}`).remove(); }
    };

    // ---------- Add / Edit UI ----------
    function openAddContent(section){
      $('contentSection').value = section;
      $('editContentId').value = '';
      $('contentTitle').value = '';
      $('contentDescription').value = '';
      const cur = JSON.parse(localStorage.getItem('currentUser')) || null;
      if (cur) $('contentAuthor').value = cur.name;
      $('modalTitle').textContent = `Add Content — ${section}`;
      $('addContentModal').classList.add('active');
    }
    function closeAddContent(){ $('addContentModal').classList.remove('active'); }

    function openEditModalFor(section, id){
      db.ref(`content/${section}/${id}`).once('value').then(snap=>{
        const item = snap.val();
        if (!item) return alert('Item not found');
        $('contentSection').value = section;
        $('editContentId').value = id;
        $('contentTitle').value = item.title || '';
        $('contentDescription').value = item.description || '';
        $('contentAuthor').value = item.author || (JSON.parse(localStorage.getItem('currentUser')) || {}).name || 'Unknown';
        $('modalTitle').textContent = `Edit Content — ${section}`;
        $('addContentModal').classList.add('active');
      }).catch(err => { console.error(err); alert('Failed to load item for edit'); });
    }

    document.getElementById('addContentForm').addEventListener('submit', function(e){
      e.preventDefault();
      const section = $('contentSection').value;
      const editId = $('editContentId').value;
      const title = $('contentTitle').value.trim();
      const description = $('contentDescription').value.trim();
      const author = $('contentAuthor').value;
      if (!title || !description) return alert('Fill title and content');
      const payload = { title, description, author, date: new Date().toISOString() };
      if (editId) {
        // permission check: only owner or admin
        const cur = JSON.parse(localStorage.getItem('currentUser')) || {};
        db.ref(`content/${section}/${editId}`).once('value').then(snap=>{
          const item = snap.val() || {};
          const canEdit = (cur && (cur.email === 'kaled@team.com' || item.author === cur.name));
          if (!canEdit) return alert('You can only edit items you created.');
          contentSystem.updateContent(section, editId, payload).then(()=> closeAddContent()).catch(err=>{ console.error(err); alert('Update failed'); });
        });
      } else {
        contentSystem.pushContent(section, payload).then(()=> closeAddContent()).catch(err=>{ console.error(err); alert('Save failed'); });
      }
    });

    function deleteContent(section, id){
      if (!confirm('Are you sure you want to delete this content?')) return;
      // permission check
      const cur = JSON.parse(localStorage.getItem('currentUser')) || {};
      db.ref(`content/${section}/${id}`).once('value').then(snap=>{
        const item = snap.val() || {};
        const canDelete = (cur && (cur.email === 'kaled@team.com' || item.author === cur.name));
        if (!canDelete) return alert('You can only delete items you created.');
        contentSystem.removeContent(section, id).catch(err=>{ console.error(err); alert('Delete failed'); });
      });
    }

    // ---------- PDFs ----------
    function openPdfUpload(){ $('pdfUploadForm').reset(); $('pdfUploadProgress').textContent=''; $('pdfUploadModal').classList.add('active'); }
    function closePdfUpload(){ $('pdfUploadModal').classList.remove('active'); }

    document.getElementById('pdfUploadForm').addEventListener('submit', async function(e){
      e.preventDefault();
      const title = $('pdfTitle').value.trim();
      const desc = $('pdfDescription').value.trim();
      const fileInput = $('pdfFile');
      if (!title || !desc || !fileInput.files.length) return alert('Fill fields & choose PDF');
      const file = fileInput.files[0];
      if (file.type !== 'application/pdf') return alert('Please upload a PDF file');
      const ts = Date.now();
      const storageRef = storage.ref().child(`pdfs/${ts}_${file.name}`);
      const uploadTask = storageRef.put(file);
      $('pdfUploadProgress').textContent = 'Uploading... 0%';
      uploadTask.on('state_changed', snapshot=>{
        const percent = Math.floor(snapshot.bytesTransferred / snapshot.totalBytes * 100);
        $('pdfUploadProgress').textContent = `Uploading... ${percent}%`;
      }, err=>{
        console.error(err); alert('Upload failed');
      }, async ()=>{
        const url = await uploadTask.snapshot.ref.getDownloadURL();
        const cur = JSON.parse(localStorage.getItem('currentUser')) || {};
        const meta = { title, description: desc, url, storagePath: uploadTask.snapshot.ref.fullPath, date: new Date().toISOString(), author: cur.name || 'Group 1' };
        await db.ref('pdfs').push(meta);
        $('pdfUploadProgress').textContent = 'Upload complete';
        setTimeout(()=> closePdfUpload(), 800);
      });
    });

    function renderPdfLibrary(snapshot){
      const container = $('pdf-library-content'); if (!container) return;
      container.innerHTML = '';
      if (!snapshot || !snapshot.exists()){
        container.innerHTML = `<div style="padding:16px;color:#cfe6ff;"><i class="fas fa-file-pdf"></i> No PDFs yet. Use upload button.</div>`; return;
      }
      snapshot.forEach(child=>{
        const id = child.key, item = child.val();
        const row = document.createElement('div'); row.className = 'pdf-row';
        row.innerHTML = `<div style="flex:1;">
          <div style="font-weight:800;color:#eaf6ff;">${escapeHtml(item.title)}</div>
          <div style="color:#bcd7ff;font-size:0.95rem;">${escapeHtml(item.description)}</div>
          <div style="color:#9fc8ff;font-size:0.85rem;margin-top:6px;">${new Date(item.date).toLocaleDateString()} • ${escapeHtml(item.author || 'Group 1')}</div>
        </div>
        <div style="display:flex;flex-direction:column;gap:8px;">
          <a href="${item.url}" target="_blank" class="action-btn" style="background:linear-gradient(90deg,#06b6d4,#3b82f6);color:#002233;border:none;">Open</a>
          <button class="action-btn delete-pdf-btn" data-id="${id}" style="background:#ef4444;color:white;border:none;">Delete</button>
        </div>`;
        container.appendChild(row);
      });

      container.querySelectorAll('.delete-pdf-btn').forEach(btn=>{
        btn.addEventListener('click', async ()=>{
          const id = btn.getAttribute('data-id');
          if (!confirm('Delete this PDF?')) return;
          const cur = JSON.parse(localStorage.getItem('currentUser')) || {};
          if (cur.email !== 'kaled@team.com') { alert('Only admin (kaled) can delete PDFs'); return; }
          const snap = await db.ref(`pdfs/${id}`).once('value'); const meta = snap.val() || {};
          if (meta.storagePath) {
            try { await storage.ref(meta.storagePath).delete(); } catch(e){ console.warn('storage delete failed', e); }
          }
          await db.ref(`pdfs/${id}`).remove();
        });
      });
    }

    function listenPdfs(){
      const ref = db.ref('pdfs'); ref.off(); ref.on('value', snap => renderPdfLibrary(snap));
    }

    // ---------- Admin actions ----------
    function clearAllContentAdmin(){
      if (!confirm('Delete all content for all sections?')) return;
      const updates = {};
      sections.forEach(sec => updates[`content/${sec}`] = null);
      db.ref().update(updates).then(()=> alert('All content cleared')).catch(err=> { console.error(err); alert('Failed to clear content'); });
    }
    function clearChatAdmin(){
      if (!confirm('Clear all chat messages?')) return;
      db.ref('team-chat').remove().then(()=> {
        const list = $('chatMessages'); if (list) list.innerHTML = '<div style="padding:16px;color:#bcd7ff;text-align:center;">Chat cleared</div>';
      }).catch(err => { console.error(err); alert('Failed to clear chat'); });
    }

    // ---------- Team members UI ----------
    const teamMembers = [
      { name: 'Kaled Osman', role: 'Project Lead', avatar:'K' },
      { name: 'Marcus Tibell', role: 'Engineer', avatar:'M' },
      { name: 'Jens Annell', role: 'Analyst', avatar:'J' },
      { name: 'Fahad Hussain', role: 'Researcher', avatar:'F' },
      { name: 'Luwam', role:'Designer', avatar:'L' },
      { name: 'Stefan Österberg', role:'Architect', avatar:'S' },
      { name: 'Najmaddin', role:'Security Expert', avatar:'N' }
    ];
    function showTeamMembers(){
      const list = $('liveTeamList'); if (!list) return;
      list.innerHTML = '';
      teamMembers.forEach(m=>{
        const el = document.createElement('div'); el.style.display='flex'; el.style.gap='8px'; el.style.alignItems='center';
        el.innerHTML = `<div style="width:36px;height:36px;border-radius:8px;background:linear-gradient(90deg,#06b6d4,#3b82f6);display:flex;align-items:center;justify-content:center;color:#002233;font-weight:700;">${m.avatar}</div>
          <div style="flex:1;"><div style="font-weight:700;color:#eaf6ff;font-size:0.95rem;">${escapeHtml(m.name)}</div><div style="color:#bcd7ff;font-size:0.85rem;">${escapeHtml(m.role)}</div></div>`;
        list.appendChild(el);
      });
    }

    // ---------- Seed Practical Tasks as separate sections (only if empty) ----------
    async function seedTasksIfEmpty(){
      // check task-01 etc
      const checks = await Promise.all(['task-01','task-02','task-03','task-04','task-05'].map(k => db.ref(`content/${k}`).once('value')));
      const anyExists = checks.some(snap => snap.exists());
      if (anyExists) return;

      const teamNames = 'Marcus, Jens, Fahad, Stefan, Kaled';
      // long detailed content provided by user (trimmed slightly for readability)
      const t1 = `Group 1 – Report

1. What Was the Task?
The goal was to simulate a real debugging situation.
One team member provides a broken code snippet, and the team must:
Identify the bug
Explain why it happens
Fix the bug
Reflect on what was learned

This simulates real workplace debugging and collaborative problem-solving.

---

2. What Was the Broken Feature?
The provided code had logic issues and type errors that caused unexpected output.
Examples of common errors we analyzed:
Using wrong variable types
Incorrect comparison or calculation
Missing return values
Unhandled edge cases

The team identified the problematic line, tested variations, and confirmed the exact root cause.

---

3. How We Solved It
The debugging process followed these steps:
1. Reproduce the error
Run the code and observe the failing behavior.

2. Use print/log statements
Track variable states and isolate where the error occurs.

3. Check logic & data types
For example: "5" + 5 → causes unexpected concatenation.

4. Apply the fix
Correct the faulty line, adjust types, or fix the condition.

5. Test again
Confirm the code now produces correct output.

---

4. What We Learned
How to analyze errors systematically
How to replicate bugs to understand them
How small mistakes (e.g., wrong type) can break entire features
How debugging in a team is faster than debugging alone
Importance of clear communication

---

5. Summary
Task 01 helped Group 1 practice real-world debugging and develop a stronger technical workflow.
The team successfully identified the root cause, fixed the error, and documented the solution.
`;

      const t2 = `Group 1 – Report

1. What Was the Task?
Build a small, functional tool such as:
a weather fetcher
an email sender
a converter
or any simple interactive utility

The goal was not complexity, but learning:
API usage, Basic architecture, Input/Output flow

---

2. What We Built
Our mini project focused on understanding external APIs and simple UI logic:
Fetching data (like weather, JSON, stats)
Parsing the response
Displaying it to the user
Handling errors (e.g., invalid input or connection problems)

We built a simple tool that retrieved information from an API endpoint and displayed it neatly.

---

3. Technical Highlights
We learned how to call an API using fetch(), difference between JSON structure and local variables, error handling using try/catch and how to structure IO.

---

4. What We Learned
Real-world API usage, how to design small programs, importance of clean UI and readable output.

---

5. Summary
This task strengthened our ability to create practical tools quickly and prototype ideas.
`;

      const t3 = `Group 1 – Report

1. Purpose of the Task
Build a responsive web component that automatically changes layout based on screen size:
Two columns on desktop, One column on mobile.

This required understanding HTML structure, CSS Grid/Flexbox, media queries, and component reusability.

---

2. What We Implemented
A reusable component that:
Shows content side-by-side on large screens
Stacks vertically on mobile
Adjusts spacing and alignment

---

3. Key Technical Concepts
display:grid/flex, grid-template-columns, @media rules, gap and mobile-first design.

---

4. What We Learned
How to design components that adapt to any device and why responsiveness matters.
`;

      const t4 = `Group 1 – Report

1. What Was the Task?
A team member provides only the output, and the rest of the team must:
Guess what code produced that output
Rebuild a similar program
Explain the logic behind it

This teaches analytical thinking, pattern recognition, and reverse engineering.

---

2. Output Example
{
  "project": "FitnessTracker",
  "user": "Sara",
  "week": 12,
  "activities": [...]
}

We identified structure, arrays, patterns and rebuilt code that produces similar JSON.

---

3. What We Learned
How to analyze output and reconstruct logic, serialization, and team communication.
`;

      const t5 = `Group 1 – Report

1. Background
XWiki uses Markdown + MathJax. Problem: Markdown processes _ and * before LaTeX, causing math like $ x_1 + y_2 $ to break because _1 becomes italic.

---

2. Problem Explanation
The Markdown engine does not protect $...$ blocks so LaTeX is corrupted.

---

3. Proposed Solution
Implement three-step rendering pipeline:
Step 1 — Math Preprocessor: placeholder for each $...$.
Step 2 — Markdown Engine: process page normally.
Step 3 — Math Postprocessor: reinsert math and render with MathJax.

---

4. Why This Works
Protects math without changing XWiki core; works for inline and block math.

---

5. What We Learned
Rendering pipelines, text preprocessing and safe integration with existing renderers.
`;

      const tasks = [
        { title:'Task 01 - Debug a Broken Feature', description: t1, author: teamNames, date:new Date().toISOString() },
        { title:'Task 02 - Build a Mini Tool', description: t2, author: teamNames, date:new Date().toISOString() },
        { title:'Task 03 - Responsive Two-Column Web Component', description: t3, author: teamNames, date:new Date().toISOString() },
        { title:'Task 04 - Reverse Engineer a Result', description: t4, author: teamNames, date:new Date().toISOString() },
        { title:'Task 05 - XWiki LaTeX Math Rendering', description: t5, author: teamNames, date:new Date().toISOString() }
      ];

      // push each to its own section
      await db.ref('content/task-01').push(tasks[0]);
      await db.ref('content/task-02').push(tasks[1]);
      await db.ref('content/task-03').push(tasks[2]);
      await db.ref('content/task-04').push(tasks[3]);
      await db.ref('content/task-05').push(tasks[4]);
    }

    // ---------- init UI behaviors ----------
    function initUI(){
      // nav buttons
      document.querySelectorAll('.nav-btn').forEach(btn=>{
        btn.addEventListener('click', function(){
          document.querySelectorAll('.nav-btn').forEach(b=>b.classList.remove('active'));
          this.classList.add('active');
          document.querySelectorAll('.section-content').forEach(s => s.style.display = 'none');
          const id = this.getAttribute('data-section');
          const el = document.getElementById(id);
          if (el) el.style.display = 'block';
          // update counters
          document.getElementById('stat-sections').textContent = sections.length;
        });
      });

      // close modals on background click
      document.querySelectorAll('.modal').forEach(m=>{
        m.addEventListener('click', (e)=>{
          if (e.target === m) m.classList.remove('active');
        });
      });

      // open team chat buttons
      document.querySelectorAll('[onclick="openTeamChat()"]').forEach(b=>b.addEventListener('click', ()=>chat.open()));
    }

    // ---------- listen firebase and boot ----------
    async function boot(){
      loginSystem.init();
      chat.init();
      contentSystem.listenAll();
      listenPdfs();
      showTeamMembers();
      adminSystem.tryInit();
      initUI();
      await seedTasksIfEmpty().catch(console.error);
    }

    document.addEventListener('DOMContentLoaded', ()=> boot());

    // expose for inline onclicks
    window.openAddContent = openAddContent;
    window.closeAddContent = closeAddContent;
    window.openPdfUpload = openPdfUpload;
    window.closePdfUpload = closePdfUpload;
    window.openTeamChat = ()=> chat.open();
    window.closeTeamChat = ()=> chat.close();
    window.openEditModalFor = openEditModalFor;
    window.deleteContent = deleteContent;
    window.toggleTheme = function(){
      document.documentElement.classList.toggle('dark');
      alert('Theme toggle performed (visual effect depends on OS/browser).');
    };
    window.closeLogin = function(){ $('loginModal').classList.remove('active'); };

  })();window.addEventListener("load", () => {
    let accounts = localStorage.getItem("teamUsers");

    if (!accounts) {
        const defaultAccounts = [
            { name: "Kaled Osman", email: "kaled@team.com", password: "kaled123", role: "Project Lead" },
            { name: "Marcus Tibell", email: "marcus@team.com", password: "marcus123", role: "Engineer" },
            { name: "Jens Annell", email: "jens@team.com", password: "jens123", role: "Analyst" },
            { name: "Fahad Hussain", email: "fahad@team.com", password: "fahad123", role: "Researcher" },
            { name: "Luwam", email: "luwam@team.com", password: "luwam123", role: "Designer" },
            { name: "Stefan Österberg", email: "stefan@team.com", password: "stefan123", role: "Architect" },
            { name: "Najmaddin", email: "najmaddin@team.com", password: "najmaddin123", role: "Security Expert" }
        ];

        localStorage.setItem("teamUsers", JSON.stringify(defaultAccounts));
        console.log("Team accounts restored.");
    }
});

  </script>
</body>
</html>

