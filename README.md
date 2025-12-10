<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width,initial-scale=1" />
    <title>Security Chaos Engineering Dashboard</title>

    <!-- icons & tailwind -->
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <script src="https://cdn.tailwindcss.com"></script>

    <!-- Firebase SDK (compat) -->
    <script src="https://www.gstatic.com/firebasejs/9.22.1/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.1/firebase-database-compat.js"></script>

    <style>
        /* --- Background and general layout --- */
        :root {
            --overlay: linear-gradient(180deg, rgba(0,0,0,0.45), rgba(0,0,0,0.6));
        }
        html,body { height:100%; margin:0; font-family: Inter, system-ui, -apple-system, "Segoe UI", Roboto, "Helvetica Neue", Arial; }
        body {
            background: url('bg.jpg') center/cover no-repeat fixed;
            background-color: #0b1220;
            color: #e6eef8;
        }
        /* overlay to ensure text readability */
        .page-overlay {
            min-height:100vh;
            background: var(--overlay);
            backdrop-filter: blur(0.6px);
        }

        /* small visual tweaks */
        .card {
            background: linear-gradient(180deg, rgba(255,255,255,0.03), rgba(255,255,255,0.02));
            border: 1px solid rgba(255,255,255,0.06);
            box-shadow: 0 6px 18px rgba(2,6,23,0.6);
        }

        /* hide the original add button inside Practical Tasks area (we removed in HTML, but this is safety) */
        .hide-add-practical { display: none !important; }

        /* details summary style */
        details summary {
            list-style: none;
            cursor: pointer;
        }
        details[open] summary::after {
            content: "▲";
            float: right;
        }
        details summary::after {
            content: "▼";
            float: right;
        }

        /* responsive tweaks */
        @media (max-width: 900px) {
            .sidebar { display: none; }
            .content-area { padding: 1rem; }
        }
    </style>
</head>
<body>
    <div class="page-overlay">
        <div class="flex">
            <!-- Sidebar (kept compact) -->
            <aside class="sidebar w-80 p-6 border-r border-white/5">
                <h1 class="text-2xl font-bold text-white mb-2">Security Chaos Engineering</h1>
                <p class="text-sm text-white/80 mb-4">Group 1 Dashboard</p>

                <div class="mb-6">
                    <div class="p-3 rounded-lg card">
                        <div class="flex items-center gap-3">
                            <div class="w-10 h-10 rounded-full bg-gradient-to-r from-blue-500 to-purple-500 flex items-center justify-center font-bold">G1</div>
                            <div>
                                <div id="userName" class="font-semibold">You</div>
                                <div id="userRole" class="text-xs text-white/70">Active</div>
                            </div>
                        </div>
                    </div>
                </div>

                <nav class="space-y-2">
                    <button class="w-full text-left p-3 rounded-lg bg-gradient-to-r from-blue-600 to-indigo-600 font-medium">Overview</button>
                    <button class="w-full text-left p-3 rounded-lg hover:bg-white/5">Project Documentation</button>
                    <button class="w-full text-left p-3 rounded-lg hover:bg-white/5">Team Collaboration</button>
                    <button class="w-full text-left p-3 rounded-lg hover:bg-white/5">Team Updates</button>
                    <button class="w-full text-left p-3 rounded-lg hover:bg-white/5">AI Assistant</button>
                    <button class="w-full text-left p-3 rounded-lg bg-white/5">Practical Tasks</button>
                    <button class="w-full text-left p-3 rounded-lg hover:bg-white/5">Implementation</button>
                    <button class="w-full text-left p-3 rounded-lg hover:bg-white/5">Research</button>
                    <button class="w-full text-left p-3 rounded-lg hover:bg-white/5">Resources</button>
                    <button class="w-full text-left p-3 rounded-lg hover:bg-white/5">Security Testing</button>
                    <button class="w-full text-left p-3 rounded-lg hover:bg-white/5">Monitoring & Analytics</button>
                </nav>

                <div class="mt-6 text-sm text-white/70">
                    <div class="mb-2 font-semibold">Team Members</div>
                    <ul class="space-y-2">
                        <li>Marcus</li>
                        <li>Jens</li>
                        <li>Fahad</li>
                        <li>Stefan</li>
                        <li>Kaled</li>
                        <li class="text-white/60">Luwam (not active)</li>
                    </ul>
                </div>
            </aside>

            <!-- Main content -->
            <main class="content-area flex-1 p-10">
                <header class="mb-8">
                    <h2 class="text-3xl font-bold mb-2">Practical Tasks — Group 1</h2>
                    <p class="text-white/80">All tasks are collected here. Click to expand each card for full details.</p>
                </header>

                <!-- Practical Tasks Section -->
                <section id="practical-tasks" class="mb-8">
                    <div class="p-6 rounded-2xl card">
                        <!-- Swedish thank-you (as requested): title starts with two stars -->
                        <div class="mb-4">
                            <div class="text-sm text-white/70 mb-1">** Projektledare: Daniel, Najmaddin & Shahen</div>
                            <div class="text-white/90">
                                Vi i Grupp 1 vill rikta ett stort tack till vår projektledare för tålamodet, stödet och de tydliga förklaringarna under hela projektet. Vi har lärt oss mycket av varje uppgift och utvecklats både tekniskt och som ett lag.
                            </div>
                            <div class="mt-3 text-sm text-white/70">Tack även till hela teamet för ett fantastiskt samarbete: Marcus, Jens, Fahad, Stefan och Kaled.</div>
                        </div>

                        <!-- Tasks container -->
                        <div class="grid grid-cols-1 md:grid-cols-2 gap-6">
                            <!-- Task 01 -->
                            <article class="p-4 rounded-lg card">
                                <h3 class="font-semibold text-lg mb-1">Task 01 – Debug a Broken Feature</h3>
                                <div class="text-xs text-white/70 mb-2">Team: Marcus, Jens, Fahad, Stefan, Kaled</div>
                                <p class="text-white/80 mb-3">Short: Identify a bug in a given code snippet, fix it and explain the approach.</p>

                                <details>
                                    <summary class="font-medium">Open full report (English)</summary>
                                    <div class="mt-3 text-white/90 space-y-2">
                                        <p><strong>Problem:</strong> A discount calculation function used percentage value as multiplier causing incorrect final price.</p>
                                        <p><strong>Fix:</strong> Convert percent to decimal before multiplying.</p>
                                        <pre class="bg-black/40 p-3 rounded text-sm overflow-auto">def calculate_discount(price, discount_percent):
    discount = price * (discount_percent / 100)
    final_price = price - discount
    return final_price
</pre>
                                        <p><strong>What we learned:</strong> Always validate numeric assumptions, use tests for edge cases, and document fixes.</p>
                                    </div>
                                </details>
                            </article>

                            <!-- Task 02 -->
                            <article class="p-4 rounded-lg card">
                                <h3 class="font-semibold text-lg mb-1">Task 02 – Build a Mini Tool (Weather App)</h3>
                                <div class="text-xs text-white/70 mb-2">Team: Marcus, Jens, Fahad, Stefan, Kaled</div>
                                <p class="text-white/80 mb-3">Short: Implemented a small CLI weather application demonstrating API consumption, data parsing and user-friendly output.</p>

                                <details>
                                    <summary class="font-medium">Open full report (English)</summary>
                                    <div class="mt-3 text-white/90 space-y-2">
                                        <p><strong>Overview:</strong> The tool uses OpenWeatherMap (or Open-Meteo) API, handles network errors and formats data.</p>
                                        <p><strong>Key code (example):</strong></p>
                                        <pre class="bg-black/40 p-3 rounded text-sm overflow-auto">import requests

def get_weather(city, api_key):
    params = {'q': city, 'appid': api_key, 'units': 'metric'}
    r = requests.get("http://api.openweathermap.org/data/2.5/weather", params=params, timeout=10)
    r.raise_for_status()
    return r.json()
</pre>
                                        <p><strong>What we learned:</strong> API integration, error handling, and presentation of external data.</p>
                                    </div>
                                </details>
                            </article>

                            <!-- Task 03 -->
                            <article class="p-4 rounded-lg card">
                                <h3 class="font-semibold text-lg mb-1">Task 03 – Responsive Two-Column Web Component</h3>
                                <div class="text-xs text-white/70 mb-2">Team: Marcus, Jens, Fahad, Stefan, Kaled</div>
                                <p class="text-white/80 mb-3">Short: Built a reusable web component (custom element) that swaps between single/double column layouts, with ResizeObserver and internationalization.</p>

                                <details>
                                    <summary class="font-medium">Open full report (English)</summary>
                                    <div class="mt-3 text-white/90 space-y-2">
                                        <p><strong>Highlights:</strong> Shadow DOM encapsulation, attributes for threshold and animation duration, events for 'layout-change'.</p>
                                        <p><strong>What we learned:</strong> How to make framework-agnostic components, performance strategies, and accessibility.</p>
                                    </div>
                                </details>
                            </article>

                            <!-- Task 04 -->
                            <article class="p-4 rounded-lg card">
                                <h3 class="font-semibold text-lg mb-1">Task 04 – Reverse Engineer a Result</h3>
                                <div class="text-xs text-white/70 mb-2">Team: Marcus, Jens, Fahad, Stefan, Kaled</div>
                                <p class="text-white/80 mb-3">Short: Given an output artifact, deduce and reconstruct the code that produced it. Applied debugging, pattern recognition and testing.</p>

                                <details>
                                    <summary class="font-medium">Open full report (English)</summary>
                                    <div class="mt-3 text-white/90 space-y-2">
                                        <p><strong>Approach:</strong> Break output into parts, hypothesize transformations, implement candidate code and verify.</p>
                                        <p><strong>What we learned:</strong> Reverse engineering techniques, careful unit testing, and documenting hypotheses.</p>
                                    </div>
                                </details>
                            </article>

                            <!-- Task 05 -->
                            <article class="p-4 rounded-lg card">
                                <h3 class="font-semibold text-lg mb-1">Task 05 – XWiki LaTeX Math Rendering</h3>
                                <div class="text-xs text-white/70 mb-2">Team: Marcus, Jens, Fahad, Stefan, Kaled</div>
                                <p class="text-white/80 mb-3">Short: Investigated Markdown interfering with LaTeX math in XWiki and implemented a preprocessor/postprocessor strategy to protect math regions.</p>

                                <details>
                                    <summary class="font-medium">Open full report (English)</summary>
                                    <div class="mt-3 text-white/90 space-y-2">
                                        <p><strong>Problem:</strong> Markdown emphasis markers (e.g., _ and *) break LaTeX inside $...$ before MathJax runs.</p>
                                        <p><strong>Solution:</strong> Add a Math Preprocessor to replace math with placeholders, run Markdown, then Math Postprocessor to restore expressions for MathJax.</p>
                                        <p><strong>Pipeline:</strong> Raw XWiki Text → [Math Preprocessor] → Markdown Engine → [Math Postprocessor] → MathJax</p>
                                        <p><strong>What we learned:</strong> Rendering order issues, safe preprocessing patterns, compatibility without changing core markdown engine.</p>
                                    </div>
                                </details>
                            </article>
                        </div>
                    </div>
                </section>

                <!-- Files area (single independent page within the same dashboard UI) -->
                <section id="files" class="mt-8">
                    <div class="p-6 rounded-2xl card">
                        <h3 class="font-semibold text-lg mb-2">Files & PDFs</h3>
                        <p class="text-white/80 mb-4">All Task PDFs can be downloaded from here. Add your files to the server and update the links below.</p>

                        <ul class="space-y-3 text-white/90">
                            <li><a class="underline" href="#" id="pdf-task01">Task 01 - Hetzner Backup (PDF)</a></li>
                            <li><a class="underline" href="#" id="pdf-task02">Task 02 - Advances in Graph Algorithms (PDF)</a></li>
                            <li><a class="underline" href="#" id="pdf-task03">Task 03 - Responsive Component (PDF)</a></li>
                            <li><a class="underline" href="#" id="pdf-task04">Task 04 - Extracting rules from ML models (PDF)</a></li>
                            <li><a class="underline" href="#" id="pdf-task05">Task 05 - XWiki Math Rendering (PDF)</a></li>
                        </ul>

                        <p class="text-sm text-white/70 mt-3">Note: replace link hrefs with the actual hosted file URLs (GitHub Pages, Drive direct links, or Firebase storage URLs).</p>
                    </div>
                </section>

            </main>
        </div>
    </div>

    <!-- (Keep original Firebase + chat + content scripts if you want them active)
         For safety we keep these scripts minimal and unchanged from your original,
         but omitted here for brevity. If you want the full original JS re-attached
         (chat + real-time content), I can paste it below and we will integrate it. -->

    <script>
        // Small client-side helpers: wire demo PDF links (update these URLs to your hosted PDFs)
        // Replace '#' with real URLs (GitHub raw, Drive direct, or your hosting)
        document.getElementById('pdf-task01').href = '#'; // e.g. 'https://raw.githubusercontent.com/.../Task 01 - Hetzner Backup.pdf'
        document.getElementById('pdf-task02').href = '#';
        document.getElementById('pdf-task03').href = '#';
        document.getElementById('pdf-task04').href = '#';
        document.getElementById('pdf-task05').href = '#';
    </script>
</body>
</html>
