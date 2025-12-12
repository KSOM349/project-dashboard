<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="utf-8" />
    <meta name="viewport" content="width=device-width,initial-scale=1" />
    <title>Security Chaos Engineering Dashboard</title>

    <!-- Icons & Tailwind -->
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css" integrity="sha512-pbVfX1XQ6Ylq6Yd8m3QmFz3QwqgD56b3Qq6F1+0p1mR9e6k1t8QeKf2w==" crossorigin="anonymous" referrerpolicy="no-referrer" />
    <script src="https://cdn.tailwindcss.com"></script>

    <!-- Firebase (core libs only in head; storage/db scripts can be loaded later if you prefer) -->
    <script src="https://www.gstatic.com/firebasejs/9.22.1/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.1/firebase-database-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.1/firebase-storage-compat.js"></script>

    <style>
        /* =========================
           THEME + BACKGROUND STYLES
           Part 1: head + CSS + background
           ========================= */

        :root{
            --overlay-gradient: linear-gradient(180deg, rgba(8,11,26,0.45), rgba(8,11,26,0.6));
            --muted-text: rgba(248,250,252,0.9);
            --card-bg: rgba(255,255,255,0.95);
        }

        html, body {
            height: 100%;
            margin: 0;
            padding: 0;
            font-family: Inter, system-ui, -apple-system, "Segoe UI", Roboto, "Helvetica Neue", Arial, "Noto Naskh Arabic";
            -webkit-font-smoothing: antialiased;
            -moz-osx-font-smoothing: grayscale;
            background: #0b1220;
        }

        /* ===== FULLSCREEN BACKGROUND HERO =====
           Put your chosen image at assets/bg.jpg (relative path),
           or change the URL below to an absolute link.
        */
        .bg-hero {
            position: fixed;
            inset: 0;
            z-index: -50;
            background-image: url('assets/bg.jpg');
            background-size: cover;
            background-position: center center;
            background-repeat: no-repeat;
            transform: translateZ(0);
            will-change: transform;
            filter: saturate(1.02) contrast(1.02);
            transition: transform 0.25s ease-out;
        }

        /* dark translucent overlay + soft blur for readability */
        .bg-hero::after {
            content: "";
            position: absolute;
            inset: 0;
            background: var(--overlay-gradient);
            backdrop-filter: blur(6px) brightness(.55);
            -webkit-backdrop-filter: blur(6px) brightness(.55);
        }

        /* small parallax container to be moved by JS */
        .parallax {
            will-change: transform;
        }

        /* ===== GLOBAL UI HELPERS ===== */
        .theme-transition { transition: all 0.28s cubic-bezier(.2,.9,.2,1); }

        .active-section {
            background: linear-gradient(135deg, #3b82f6, #1d4ed8);
            color: white;
        }

        .online-indicator {
            width: 8px; height: 8px; background: #10B981; border-radius: 50%;
            animation: pulse 2s infinite;
            display: inline-block;
        }
        @keyframes pulse {
            0% { transform: scale(1); opacity: 1; }
            50% { transform: scale(1.2); opacity: .7; }
            100% { transform: scale(1); opacity: 1; }
        }

        .modal { display: none; position: fixed; inset: 0; background: rgba(0,0,0,.45); z-index: 60; align-items: center; justify-content: center; padding: 1rem; }
        .modal.active { display:flex; }

        .section-content { display: none; }
        .section-content.active { display: block; }

        /* cards / panels to be readable over the hero */
        .card {
            background: var(--card-bg);
            border-radius: 14px;
            box-shadow: 0 10px 30px rgba(2,6,23,0.45);
            color: #0f172a;
        }
        .card-compact {
            background: rgba(255,255,255,0.06);
            border-radius: 12px;
            color: var(--muted-text);
            border: 1px solid rgba(255,255,255,0.04);
        }

        /* chat list */
        .chat-container { max-height: 300px; overflow-y: auto; }

        /* scrollbar small tweak for modern browsers */
        ::-webkit-scrollbar { height: 8px; width: 8px; }
        ::-webkit-scrollbar-thumb { background: rgba(255,255,255,0.08); border-radius: 8px; }

        /* responsive tweaks */
        @media (max-width: 768px) {
            .w-80 { width: 100% !important; border-right: none !important; }
            .p-8 { padding: 1rem !important; }
        }

        /* readable headings over dark background (if you choose colored text) */
        .heading-hero {
            color: #E6EEF8; /* soft light-blue, visible on dark */
            text-shadow: 0 2px 12px rgba(2,6,23,0.6);
        }
        .muted {
            color: rgba(230,238,248,0.85);
        }

        /* small utility for "two-star" block the user wanted */
        .two-stars::before {
            content: "**";
            display:block;
            font-weight:700;
            margin-bottom: 6px;
            color: #f8fafc;
        }
    </style>
</head>
<body class="theme-transition">

    <!-- BACKGROUND HERO (parallax) -->
    <div id="bgHero" class="bg-hero parallax" aria-hidden="true"></div>

    <!--
      PART 1 END.
      الجزء الأول: انتهى (HEAD + CSS + الخلفية).
      الجزء 2 سيبدأ من هنا ويحتوي على الـ layout (sidebar, main content, modals HTML).
      الجزء 3 سيحتوي على كل JavaScript (Firebase init, chat, content system, pdf upload, permissions).
    -->

