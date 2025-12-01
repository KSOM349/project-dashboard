<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Security Chaos Engineering Dashboard</title>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <script src="https://cdn.tailwindcss.com"></script>
    
    <!-- إضافة Firebase -->
    <script src="https://www.gstatic.com/firebasejs/9.6.0/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.6.0/firebase-database-compat.js"></script>
    
    <style>
        .theme-transition { transition: all 0.3s ease; }
        .active-section { 
            background: linear-gradient(135deg, #3b82f6, #1d4ed8); 
            color: white; 
        }
        .online-indicator { 
            width: 8px; height: 8px; background: #10B981; border-radius: 50%; 
            animation: pulse 2s infinite; 
        }
        @keyframes pulse { 
            0% { transform: scale(1); opacity: 1; }
            50% { transform: scale(1.2); opacity: 0.7; }
            100% { transform: scale(1); opacity: 1; }
        }
        .modal { 
            display: none; position: fixed; top: 0; left: 0; width: 100%; height: 100%; 
            background: rgba(0, 0, 0, 0.5); z-index: 1000; align-items: center; justify-content: center; 
        }
        .modal.active { display: flex; }
        .section-content { display: none; }
        .section-content.active { display: block; }
        .new-message { animation: highlight 2s ease; }
        @keyframes highlight {
            0% { background-color: rgba(59, 130, 246, 0.3); }
            100% { background-color: transparent; }
        }
        .chat-container {
            max-height: 300px;
            overflow-y: auto;
        }
        
        /* تصميم متجاوب للجوال */
        @media (max-width: 768px) {
            .flex.min-h-screen {
                flex-direction: column !important;
            }
            
            .w-80 {
                width: 100% !important;
                border-right: none !important;
                border-bottom: 2px solid #3b82f6;
            }
            
            .flex-1.p-8 {
                padding: 1rem !important;
            }
            
            header.mb-8 {
                margin-bottom: 1.5rem !important;
            }
            
            header h1 {
                font-size: 1.5rem !important;
            }
            
            .grid-cols-4 {
                grid-template-columns: repeat(2, 1fr) !important;
                gap: 0.5rem !important;
            }
            
            nav.space-y-2 {
                display: grid;
                grid-template-columns: repeat(2, 1fr);
                gap: 0.5rem;
                margin-bottom: 1rem;
            }
            
            .section-btn {
                padding: 0.75rem !important;
                font-size: 0.8rem;
                text-align: center;
                display: flex;
                flex-direction: column;
                align-items: center;
                justify-content: center;
                height: 100%;
            }
            
            .section-btn i {
                margin-right: 0 !important;
                margin-bottom: 0.25rem;
                font-size: 1rem;
            }
            
            .bg-white.dark\\:bg-gray-800.p-8.rounded-2xl {
                padding: 1rem !important;
            }
            
            .modal > div {
                width: 95% !important;
                margin: 0.5rem !important;
                padding: 1rem !important;
                max-height: 90vh;
                overflow-y: auto;
            }
            
            .chat-container {
                max-height: 200px !important;
            }
            
            #teamChatModal > div {
                width: 95% !important;
                max-width: none !important;
            }
            
            /* تنسيق زر الشات في الجوال */
            button[onclick="openTeamChat()"] {
                position: fixed;
                bottom: 1rem;
                right: 1rem;
                width: 60px;
                height: 60px;
                border-radius: 50%;
                z-index: 100;
                box-shadow: 0 4px 12px rgba(0, 0, 0, 0.3);
            }
            
            button[onclick="openTeamChat()"] span {
                position: absolute;
                top: -5px;
                right: -5px;
            }
            
            button[onclick="openTeamChat()"] i {
                margin-right: 0 !important;
                font-size: 1.2rem;
            }
            
            button[onclick="openTeamChat()"] span:not(#unreadCount) {
                display: none !important;
            }
        }
        
        @media (max-width: 480px) {
            .grid-cols-4 {
                grid-template-columns: 1fr !important;
            }
            
            nav.space-y-2 {
                grid-template-columns: 1fr;
            }
            
            .section-btn {
                font-size: 0.75rem;
                padding: 0.6rem !important;
            }
            
            .text-2xl {
                font-size: 1.25rem !important;
            }
            
            .text-3xl {
                font-size: 1.5rem !important;
            }
            
            #liveTeamList {
                max-height: 150px !important;
            }
            
            /* تصغير حجم أعضاء الفريق في الجوال */
            .flex.items-center.space-x-3.p-2 {
                padding: 0.5rem !important;
            }
            
            .w-8.h-8 {
                width: 24px !important;
                height: 24px !important;
                font-size: 0.75rem !important;
            }
            
            .font-medium.text-gray-800 {
                font-size: 0.75rem !important;
            }
            
            .text-xs.text-gray-600 {
                font-size: 0.65rem !important;
            }
        }
        
        /* تنسيق للوضع الأفقي في الجوال */
        @media (max-height: 500px) and (orientation: landscape) {
            .flex.min-h-screen {
                flex-direction: row !important;
            }
            
            .w-80 {
                width: 250px !important;
                border-right: 1px solid #e5e7eb !important;
                border-bottom: none !important;
            }
            
            nav.space-y-2 {
                display: block !important;
            }
        }
    </style>
</head>
<body class="bg-gray-50 dark:bg-gray-900 theme-transition">
    <!-- ... باقي الكود كما هو ... -->
