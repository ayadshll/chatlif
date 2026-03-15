<!DOCTYPE html>
<html lang="ar" dir="rtl">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=yes">
    <title>💬 شات لايف - الدردشة الفورية</title>
    <!-- الخطوط -->
    <link href="https://fonts.googleapis.com/css2?family=Tajawal:wght@300;400;500;700;800&display=swap" rel="stylesheet">
    <!-- Firebase SDK -->
    <script src="https://www.gstatic.com/firebasejs/10.7.1/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/10.7.1/firebase-auth-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/10.7.1/firebase-database-compat.js"></script>
    <style>
        /* نفس الأنماط الأساسية مع إضافات للجوال والبروفايل */
        :root {
            --primary: #4f46e5;
            --primary-light: #6366f1;
            --primary-dark: #3730a3;
            --bg-dark: #0f172a;
            --bg-sidebar: #1e293b;
            --bg-chat: #f1f5f9;
            --bg-white: #ffffff;
            --text-primary: #0f172a;
            --text-secondary: #64748b;
            --text-light: #94a3b8;
            --sent-bubble: linear-gradient(135deg, #4f46e5, #7c3aed);
            --received-bubble: #ffffff;
            --green: #22c55e;
            --red: #ef4444;
            --orange: #f59e0b;
            --shadow: 0 4px 20px rgba(0,0,0,0.08);
        }
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
        }
        body {
            font-family: 'Tajawal', sans-serif;
            background: var(--bg-dark);
            height: 100vh;
            display: flex;
            align-items: center;
            justify-content: center;
            overflow: hidden;
        }
        .app-container {
            width: 100%;
            height: 100vh;
            max-width: 1400px;
            max-height: 900px;
            display: flex;
            border-radius: 20px;
            overflow: hidden;
            box-shadow: 0 25px 60px rgba(0,0,0,0.5);
        }
        @media (max-width: 768px) {
            .app-container {
                border-radius: 0;
                height: 100vh;
                max-height: none;
            }
        }
        /* شاشات الدخول والإعداد */
        .login-screen, .profile-setup-screen, .profile-edit-screen {
            position: fixed;
            inset: 0;
            background: linear-gradient(135deg, #0f172a 0%, #1e1b4b 50%, #0f172a 100%);
            display: flex;
            align-items: center;
            justify-content: center;
            z-index: 9999;
            transition: opacity 0.5s, transform 0.5s;
            overflow-y: auto;
            padding: 20px;
        }
        .login-screen.hidden, .profile-setup-screen.hidden, .profile-edit-screen.hidden {
            opacity: 0;
            transform: scale(1.05);
            pointer-events: none;
        }
        .login-box {
            background: rgba(255,255,255,0.05);
            backdrop-filter: blur(20px);
            border: 1px solid rgba(255,255,255,0.1);
            border-radius: 24px;
            padding: 50px 40px;
            width: 420px;
            max-width: 100%;
            text-align: center;
        }
        .login-box .logo {
            font-size: 60px;
            margin-bottom: 10px;
        }
        .login-box h1 {
            color: #fff;
            font-size: 28px;
            font-weight: 800;
            margin-bottom: 8px;
        }
        .login-box p {
            color: var(--text-light);
            font-size: 15px;
            margin-bottom: 35px;
        }
        .login-btn, .action-btn {
            width: 100%;
            padding: 15px;
            background: var(--sent-bubble);
            border: none;
            border-radius: 14px;
            color: #fff;
            font-family: 'Tajawal', sans-serif;
            font-size: 17px;
            font-weight: 700;
            cursor: pointer;
            transition: all 0.3s;
            display: flex;
            align-items: center;
            justify-content: center;
            gap: 10px;
            margin-top: 10px;
        }
        .login-btn:hover, .action-btn:hover {
            transform: translateY(-2px);
            box-shadow: 0 8px 25px rgba(79, 70, 229, 0.4);
        }
        .login-btn:active, .action-btn:active {
            transform: translateY(0);
        }
        .login-btn:disabled, .action-btn:disabled {
            opacity: 0.5;
            cursor: not-allowed;
            transform: none;
        }
        .input-group {
            margin-bottom: 18px;
            text-align: right;
        }
        .input-group label {
            color: var(--text-light);
            font-size: 13px;
            display: block;
            margin-bottom: 6px;
            font-weight: 500;
        }
        .input-group input {
            width: 100%;
            padding: 14px 18px;
            background: rgba(255,255,255,0.08);
            border: 2px solid rgba(255,255,255,0.08);
            border-radius: 14px;
            color: #fff;
            font-family: 'Tajawal', sans-serif;
            font-size: 15px;
            outline: none;
            transition: all 0.3s;
        }
        .input-group input:focus {
            border-color: var(--primary);
            background: rgba(255,255,255,0.12);
        }
        .input-group input::placeholder {
            color: rgba(255,255,255,0.25);
        }
        #usernameAvailability, #editUsernameAvailability {
            font-size: 12px;
            margin-top: 5px;
            text-align: right;
        }
        .image-picker {
            display: flex;
            align-items: center;
            gap: 15px;
            margin: 15px 0;
        }
        .image-preview {
            width: 70px;
            height: 70px;
            border-radius: 50%;
            background: rgba(255,255,255,0.1);
            display: flex;
            align-items: center;
            justify-content: center;
            overflow: hidden;
        }
        .image-preview img {
            width: 100%;
            height: 100%;
            object-fit: cover;
        }
        .image-preview span {
            color: var(--text-light);
            font-size: 12px;
        }
        /* أنماط الشريط الجانبي والدردشة */
        .sidebar {
            width: 360px;
            background: var(--bg-sidebar);
            display: flex;
            flex-direction: column;
            flex-shrink: 0;
            transition: transform 0.3s ease;
        }
        @media (max-width: 768px) {
            .sidebar {
                position: absolute;
                right: 0;
                top: 0;
                bottom: 0;
                width: 85%;
                max-width: 300px;
                z-index: 100;
                transform: translateX(100%);
            }
            .sidebar.show {
                transform: translateX(0);
            }
        }
        .sidebar-header {
            padding: 22px 22px 12px;
            display: flex;
            align-items: center;
            justify-content: space-between;
        }
        .sidebar-header .user-profile {
            display: flex;
            align-items: center;
            gap: 12px;
            cursor: pointer;
        }
        .sidebar-header .user-avatar {
            width: 42px;
            height: 42px;
            background: var(--primary);
            border-radius: 50%;
            display: flex;
            align-items: center;
            justify-content: center;
            font-size: 20px;
            overflow: hidden;
        }
        .sidebar-header .user-avatar img {
            width: 100%;
            height: 100%;
            object-fit: cover;
        }
        .sidebar-header .user-name {
            color: #fff;
            font-size: 17px;
            font-weight: 700;
        }
        .sidebar-header .user-status-text {
            color: var(--green);
            font-size: 12px;
        }
        .sidebar-actions {
            display: flex;
            gap: 5px;
        }
        .sidebar-actions button {
            background: rgba(255,255,255,0.06);
            border: none;
            color: var(--text-light);
            width: 36px;
            height: 36px;
            border-radius: 10px;
            cursor: pointer;
            font-size: 16px;
            transition: all 0.3s;
        }
        .sidebar-actions button:hover {
            background: rgba(255,255,255,0.12);
            color: #fff;
        }
        .search-container {
            padding: 12px 22px;
        }
        .search-wrapper {
            position: relative;
        }
        .search-container input {
            width: 100%;
            padding: 11px 42px 11px 16px;
            background: rgba(255,255,255,0.06);
            border: 1px solid rgba(255,255,255,0.06);
            border-radius: 12px;
            color: #fff;
            font-family: 'Tajawal', sans-serif;
            font-size: 14px;
            outline: none;
            transition: all 0.3s;
        }
        .search-container input:focus {
            border-color: var(--primary);
            background: rgba(255,255,255,0.1);
        }
        .search-icon {
            position: absolute;
            right: 14px;
            top: 50%;
            transform: translateY(-50%);
            color: var(--text-light);
            font-size: 14px;
            pointer-events: none;
        }
        .tabs {
            display: flex;
            padding: 0 22px;
            gap: 5px;
            margin-bottom: 8px;
        }
        .tab-btn {
            flex: 1;
            padding: 8px;
            background: transparent;
            border: none;
            border-radius: 10px;
            color: var(--text-light);
            font-family: 'Tajawal', sans-serif;
            font-size: 13px;
            font-weight: 600;
            cursor: pointer;
            transition: all 0.3s;
        }
        .tab-btn.active {
            background: rgba(79, 70, 229, 0.2);
            color: var(--primary-light);
        }
        .tab-btn:hover:not(.active) {
            background: rgba(255,255,255,0.05);
        }
        .contacts-list {
            flex: 1;
            overflow-y: auto;
            padding: 4px 12px;
        }
        .contact-item {
            display: flex;
            align-items: center;
            padding: 12px 14px;
            border-radius: 14px;
            cursor: pointer;
            transition: all 0.25s ease;
            margin-bottom: 2px;
        }
        .contact-item:hover {
            background: rgba(255,255,255,0.05);
        }
        .contact-item.active {
            background: rgba(79, 70, 229, 0.15);
        }
        .contact-avatar {
            width: 50px;
            height: 50px;
            border-radius: 50%;
            display: flex;
            align-items: center;
            justify-content: center;
            font-size: 22px;
            margin-left: 14px;
            flex-shrink: 0;
            position: relative;
            overflow: hidden;
        }
        .contact-avatar img {
            width: 100%;
            height: 100%;
            object-fit: cover;
        }
        .contact-avatar .status-dot {
            position: absolute;
            bottom: 1px;
            right: 1px;
            width: 13px;
            height: 13px;
            border-radius: 50%;
            border: 2.5px solid var(--bg-sidebar);
        }
        .status-dot.online { background: var(--green); }
        .status-dot.offline { background: var(--text-light); }
        .contact-info {
            flex: 1;
            min-width: 0;
        }
        .contact-info .contact-name {
            color: #e2e8f0;
            font-size: 15px;
            font-weight: 600;
            margin-bottom: 3px;
        }
        .contact-info .contact-last-msg {
            color: var(--text-light);
            font-size: 13px;
            white-space: nowrap;
            overflow: hidden;
            text-overflow: ellipsis;
        }
        .contact-meta {
            display: flex;
            flex-direction: column;
            align-items: flex-start;
            gap: 6px;
            flex-shrink: 0;
        }
        .contact-meta .msg-time {
            color: var(--text-light);
            font-size: 11px;
        }
        .contact-meta .unread-badge {
            background: var(--primary);
            color: #fff;
            font-size: 11px;
            font-weight: 700;
            min-width: 20px;
            height: 20px;
            border-radius: 10px;
            display: flex;
            align-items: center;
            justify-content: center;
            padding: 0 6px;
        }
        /* منطقة الدردشة الرئيسية */
        .chat-main {
            flex: 1;
            display: flex;
            flex-direction: column;
            background: var(--bg-chat);
            position: relative;
        }
        .welcome-screen {
            flex: 1;
            display: flex;
            flex-direction: column;
            align-items: center;
            justify-content: center;
            padding: 40px;
            text-align: center;
        }
        .welcome-screen .welcome-icon {
            font-size: 90px;
            margin-bottom: 20px;
            animation: float 3s ease-in-out infinite;
        }
        @keyframes float {
            0%, 100% { transform: translateY(0); }
            50% { transform: translateY(-10px); }
        }
        .welcome-screen h2 {
            font-size: 26px;
            color: var(--text-primary);
            margin-bottom: 10px;
            font-weight: 800;
        }
        .welcome-screen p {
            color: var(--text-secondary);
            font-size: 16px;
            max-width: 400px;
            line-height: 1.7;
        }
        .chat-header {
            padding: 14px 24px;
            background: var(--bg-white);
            display: flex;
            align-items: center;
            box-shadow: 0 2px 10px rgba(0,0,0,0.04);
            z-index: 10;
        }
        .chat-header .back-btn {
            display: none;
            background: none;
            border: none;
            font-size: 20px;
            cursor: pointer;
            margin-left: 10px;
            color: var(--text-secondary);
            padding: 5px;
        }
        @media (max-width: 768px) {
            .chat-header .back-btn {
                display: block;
            }
        }
        .chat-header .menu-btn {
            display: none;
            background: none;
            border: none;
            font-size: 20px;
            cursor: pointer;
            margin-right: 10px;
            color: var(--text-secondary);
            padding: 5px;
        }
        @media (max-width: 768px) {
            .chat-header .menu-btn {
                display: block;
            }
        }
        .chat-header .header-avatar {
            width: 44px;
            height: 44px;
            border-radius: 50%;
            display: flex;
            align-items: center;
            justify-content: center;
            font-size: 20px;
            margin-left: 14px;
            overflow: hidden;
        }
        .chat-header .header-avatar img {
            width: 100%;
            height: 100%;
            object-fit: cover;
        }
        .chat-header .header-info {
            flex: 1;
        }
        .chat-header .header-info .header-name {
            font-size: 16px;
            font-weight: 700;
            color: var(--text-primary);
        }
        .chat-header .header-info .header-status {
            font-size: 12px;
            font-weight: 500;
        }
        .header-status.online { color: var(--green); }
        .header-status.offline { color: var(--text-light); }
        .chat-header .header-buttons {
            display: flex;
            gap: 6px;
        }
        .chat-header .header-buttons button {
            background: var(--bg-chat);
            border: none;
            width: 40px;
            height: 40px;
            border-radius: 12px;
            cursor: pointer;
            font-size: 17px;
            transition: all 0.3s;
            color: var(--text-secondary);
        }
        .chat-header .header-buttons button:hover {
            background: #e2e8f0;
        }
        .messages-area {
            flex: 1;
            overflow-y: auto;
            padding: 24px;
            display: flex;
            flex-direction: column;
            gap: 6px;
        }
        .date-separator {
            text-align: center;
            margin: 16px 0;
        }
        .date-separator span {
            background: rgba(0,0,0,0.06);
            color: var(--text-secondary);
            padding: 6px 18px;
            border-radius: 20px;
            font-size: 12px;
            font-weight: 600;
        }
        .message-wrapper {
            display: flex;
            flex-direction: column;
            animation: msgSlide 0.35s ease-out;
        }
        @keyframes msgSlide {
            from { opacity: 0; transform: translateY(12px); }
            to { opacity: 1; transform: translateY(0); }
        }
        .message-wrapper.sent {
            align-items: flex-end;
        }
        .message-wrapper.received {
            align-items: flex-start;
        }
        .message-bubble {
            max-width: 60%;
            padding: 11px 16px;
            border-radius: 20px;
            font-size: 15px;
            line-height: 1.7;
            position: relative;
            word-wrap: break-word;
        }
        @media (max-width: 768px) {
            .message-bubble {
                max-width: 80%;
            }
        }
        .message-wrapper.sent .message-bubble {
            background: var(--sent-bubble);
            color: #fff;
            border-bottom-left-radius: 6px;
            box-shadow: 0 3px 12px rgba(79, 70, 229, 0.25);
        }
        .message-wrapper.received .message-bubble {
            background: var(--bg-white);
            color: var(--text-primary);
            border-bottom-right-radius: 6px;
            box-shadow: 0 2px 8px rgba(0,0,0,0.05);
        }
        .message-bubble .sender-name {
            font-size: 12px;
            font-weight: 700;
            margin-bottom: 3px;
            display: block;
        }
        .message-wrapper.received .sender-name {
            color: var(--primary);
        }
        .msg-time {
            font-size: 10px;
            margin-top: 4px;
            display: flex;
            align-items: center;
            gap: 4px;
        }
        .message-wrapper.sent .msg-time {
            color: rgba(255,255,255,0.7);
            justify-content: flex-start;
        }
        .message-wrapper.received .msg-time {
            color: var(--text-light);
        }
        .typing-bubble {
            display: none;
            align-self: flex-start;
            background: var(--bg-white);
            padding: 14px 20px;
            border-radius: 20px;
            border-bottom-right-radius: 6px;
            box-shadow: 0 2px 8px rgba(0,0,0,0.05);
        }
        .typing-bubble.show {
            display: flex;
            align-items: center;
            gap: 5px;
        }
        .typing-bubble .dot {
            width: 8px;
            height: 8px;
            background: var(--text-light);
            border-radius: 50%;
            animation: bounce 1.4s infinite;
        }
        .typing-bubble .dot:nth-child(2) { animation-delay: 0.15s; }
        .typing-bubble .dot:nth-child(3) { animation-delay: 0.3s; }
        @keyframes bounce {
            0%, 60%, 100% { transform: translateY(0); }
            30% { transform: translateY(-10px); }
        }
        .message-input-area {
            padding: 16px 24px;
            background: var(--bg-white);
            display: flex;
            align-items: center;
            gap: 10px;
            box-shadow: 0 -2px 10px rgba(0,0,0,0.03);
        }
        .input-actions {
            display: flex;
            gap: 4px;
        }
        .input-actions button {
            background: none;
            border: none;
            font-size: 20px;
            cursor: pointer;
            padding: 6px;
            border-radius: 8px;
            transition: all 0.3s;
            color: var(--text-secondary);
        }
        .input-actions button:hover {
            background: var(--bg-chat);
        }
        .message-input-area .msg-input {
            flex: 1;
            padding: 12px 20px;
            background: var(--bg-chat);
            border: 2px solid transparent;
            border-radius: 25px;
            font-family: 'Tajawal', sans-serif;
            font-size: 15px;
            outline: none;
            transition: all 0.3s;
            color: var(--text-primary);
        }
        .msg-input:focus {
            border-color: var(--primary);
            background: #fff;
        }
        .send-btn {
            width: 48px;
            height: 48px;
            background: var(--sent-bubble);
            border: none;
            border-radius: 50%;
            color: #fff;
            font-size: 18px;
            cursor: pointer;
            transition: all 0.3s;
            display: flex;
            align-items: center;
            justify-content: center;
            box-shadow: 0 4px 15px rgba(79, 70, 229, 0.35);
            flex-shrink: 0;
        }
        .send-btn:hover {
            transform: scale(1.08);
            box-shadow: 0 6px 20px rgba(79, 70, 229, 0.45);
        }
        .emoji-picker {
            display: none;
            position: absolute;
            bottom: 80px;
            right: 24px;
            background: var(--bg-white);
            border-radius: 16px;
            box-shadow: 0 10px 40px rgba(0,0,0,0.15);
            padding: 16px;
            width: 320px;
            z-index: 50;
        }
        .emoji-picker.show {
            display: block;
            animation: slideUp 0.3s ease;
        }
        @keyframes slideUp {
            from { opacity: 0; transform: translateY(10px); }
            to { opacity: 1; transform: translateY(0); }
        }
        .emoji-picker .emoji-grid {
            display: grid;
            grid-template-columns: repeat(8, 1fr);
            gap: 4px;
            max-height: 200px;
            overflow-y: auto;
        }
        .emoji-item {
            font-size: 22px;
            padding: 6px;
            cursor: pointer;
            border-radius: 8px;
            text-align: center;
            transition: all 0.2s;
        }
        .emoji-item:hover {
            background: var(--bg-chat);
            transform: scale(1.2);
        }
        /* نافذة إنشاء مجموعة محسنة */
        .modal-overlay {
            display: none;
            position: fixed;
            inset: 0;
            background: rgba(0,0,0,0.6);
            backdrop-filter: blur(5px);
            z-index: 1000;
            align-items: center;
            justify-content: center;
            padding: 20px;
        }
        .modal-overlay.show {
            display: flex;
        }
        .modal-box {
            background: var(--bg-white);
            border-radius: 24px;
            padding: 30px;
            width: 400px;
            max-width: 100%;
            animation: slideUp 0.3s ease;
            box-shadow: 0 20px 60px rgba(0,0,0,0.3);
        }
        .modal-box h3 {
            font-size: 22px;
            margin-bottom: 20px;
            color: var(--text-primary);
            text-align: center;
        }
        .modal-box input {
            width: 100%;
            padding: 14px 16px;
            border: 2px solid #e2e8f0;
            border-radius: 16px;
            font-family: 'Tajawal', sans-serif;
            font-size: 15px;
            outline: none;
            margin-bottom: 20px;
            transition: border-color 0.3s;
        }
        .modal-box input:focus {
            border-color: var(--primary);
        }
        .modal-actions {
            display: flex;
            gap: 12px;
            justify-content: center;
        }
        .modal-actions button {
            padding: 12px 28px;
            border-radius: 14px;
            font-family: 'Tajawal', sans-serif;
            font-size: 15px;
            font-weight: 600;
            cursor: pointer;
            transition: all 0.3s;
            border: none;
            flex: 1;
        }
        .modal-actions .btn-primary {
            background: var(--primary);
            color: #fff;
        }
        .modal-actions .btn-primary:hover {
            background: var(--primary-dark);
            transform: translateY(-2px);
        }
        .modal-actions .btn-secondary {
            background: var(--bg-chat);
            color: var(--text-secondary);
        }
        .modal-actions .btn-secondary:hover {
            background: #e2e8f0;
        }
        /* إشعار */
        .notification {
            position: fixed;
            top: 20px;
            left: 50%;
            transform: translateX(-50%) translateY(-100px);
            background: var(--bg-white);
            padding: 14px 24px;
            border-radius: 14px;
            box-shadow: 0 10px 30px rgba(0,0,0,0.15);
            display: flex;
            align-items: center;
            gap: 12px;
            z-index: 9999;
            transition: transform 0.4s ease;
            font-size: 14px;
            font-weight: 600;
            color: var(--text-primary);
        }
        .notification.show {
            transform: translateX(-50%) translateY(0);
        }
        .notification .notif-avatar {
            width: 36px;
            height: 36px;
            border-radius: 50%;
            display: flex;
            align-items: center;
            justify-content: center;
            font-size: 16px;
            overflow: hidden;
        }
        .notification .notif-avatar img {
            width: 100%;
            height: 100%;
            object-fit: cover;
        }
        /* صفحة تعديل البروفايل */
        .profile-edit-screen .login-box {
            max-width: 450px;
        }
        .close-btn {
            background: transparent;
            border: 2px solid rgba(255,255,255,0.2);
            color: #fff;
            padding: 10px;
            border-radius: 12px;
            cursor: pointer;
            font-size: 16px;
            margin-top: 10px;
            transition: all 0.3s;
        }
        .close-btn:hover {
            background: rgba(255,255,255,0.1);
        }
    </style>
</head>
<body>

    <!-- شاشة تسجيل الدخول عبر Google -->
    <div class="login-screen" id="loginScreen">
        <div class="login-box">
            <div class="logo">💬</div>
            <h1>شات لايف</h1>
            <p>سجل دخولك بحساب Google للبدء</p>
            <button class="login-btn" onclick="loginWithGoogle()">
                <span>تسجيل الدخول عبر Google</span>
                <img src="https://www.gstatic.com/firebasejs/ui/2.0.0/images/auth/google.svg" width="20" style="background: white; border-radius: 50%; padding: 2px;">
            </button>
        </div>
    </div>

    <!-- شاشة إكمال الملف الشخصي (للمستخدم الجديد) -->
    <div class="profile-setup-screen" id="profileSetupScreen" style="display: none;">
        <div class="login-box">
            <div class="logo">📝</div>
            <h1>أكمل ملفك الشخصي</h1>
            <p>اختر اسم مستخدم فريد واسمك الظاهر وصورتك</p>

            <div class="input-group">
                <label>👤 اسم المستخدم (فريد)</label>
                <input type="text" id="setupUsername" placeholder="مثال: ahmed123" maxlength="20" dir="ltr">
                <div id="usernameAvailability"></div>
            </div>

            <div class="input-group">
                <label>📝 الاسم الظاهر</label>
                <input type="text" id="setupName" placeholder="مثال: أحمد" maxlength="20">
            </div>

            <div class="input-group">
                <label>🖼️ الصورة الشخصية</label>
                <div class="image-picker">
                    <button class="login-btn" style="flex: 1; padding: 10px; margin:0;" onclick="selectImage('setup')">اختر صورة</button>
                    <div class="image-preview" id="profilePreview">
                        <span>لا توجد</span>
                    </div>
                </div>
            </div>

            <button class="login-btn" onclick="completeProfile()" id="completeProfileBtn" disabled>إكمال التسجيل</button>
        </div>
    </div>

    <!-- شاشة تعديل البروفايل -->
    <div class="profile-edit-screen" id="profileEditScreen" style="display: none;">
        <div class="login-box">
            <div class="logo">✏️</div>
            <h1>تعديل الملف الشخصي</h1>
            <p>يمكنك تعديل بياناتك هنا</p>

            <div class="input-group">
                <label>👤 اسم المستخدم (فريد)</label>
                <input type="text" id="editUsername" placeholder="اسم المستخدم" maxlength="20" dir="ltr">
                <div id="editUsernameAvailability"></div>
            </div>

            <div class="input-group">
                <label>📝 الاسم الظاهر</label>
                <input type="text" id="editName" placeholder="الاسم الظاهر" maxlength="20">
            </div>

            <div class="input-group">
                <label>🖼️ الصورة الشخصية</label>
                <div class="image-picker">
                    <button class="login-btn" style="flex: 1; padding: 10px; margin:0;" onclick="selectImage('edit')">اختر صورة جديدة</button>
                    <div class="image-preview" id="editProfilePreview">
                        <span>الصورة الحالية</span>
                    </div>
                </div>
            </div>

            <button class="login-btn" onclick="saveProfileChanges()" id="saveProfileBtn">حفظ التغييرات</button>
            <button class="close-btn" onclick="closeProfileEdit()">إلغاء</button>
        </div>
    </div>

    <!-- الإشعار -->
    <div class="notification" id="notification">
        <div class="notif-avatar" id="notifAvatar"></div>
        <span id="notifText"></span>
    </div>

    <!-- التطبيق الرئيسي -->
    <div class="app-container">
        <!-- الشريط الجانبي -->
        <div class="sidebar" id="sidebar">
            <div class="sidebar-header">
                <div class="user-profile" onclick="openProfileEdit()">
                    <div class="user-avatar" id="myAvatar">😎</div>
                    <div>
                        <div class="user-name" id="myName">أنا</div>
                        <div class="user-status-text">● متصل</div>
                    </div>
                </div>
                <div class="sidebar-actions">
                    <button onclick="openNewGroupModal()" title="مجموعة جديدة">👥</button>
                    <button onclick="logout()" title="تسجيل الخروج">🚪</button>
                </div>
            </div>
            <div class="search-container">
                <div class="search-wrapper">
                    <span class="search-icon">🔍</span>
                    <input type="text" placeholder="البحث..." id="searchInput" oninput="filterContacts()">
                </div>
            </div>
            <div class="tabs">
                <button class="tab-btn active" onclick="switchTab(this, 'all')">الكل</button>
                <button class="tab-btn" onclick="switchTab(this, 'online')">متصل</button>
                <button class="tab-btn" onclick="switchTab(this, 'groups')">مجموعات</button>
            </div>
            <div class="contacts-list" id="contactsList"></div>
        </div>

        <!-- منطقة الدردشة -->
        <div class="chat-main" id="chatMain">
            <div class="welcome-screen" id="welcomeScreen">
                <div class="welcome-icon">💬</div>
                <h2>مرحباً بك في شات لايف</h2>
                <p>اختر محادثة من القائمة أو ابدأ محادثة جديدة مع أصدقائك</p>
            </div>
            <div class="chat-header" id="chatHeader" style="display: none;">
                <button class="back-btn" onclick="goBack()">→</button>
                <button class="menu-btn" onclick="toggleSidebar()">☰</button>
                <div class="header-avatar" id="headerAvatar"></div>
                <div class="header-info">
                    <div class="header-name" id="headerName"></div>
                    <div class="header-status" id="headerStatus"></div>
                </div>
                <div class="header-buttons">
                    <button onclick="toggleEmojiPicker()">😊</button>
                    <button onclick="openChatSettings()">⚙️</button>
                </div>
            </div>
            <div class="messages-area" id="messagesArea"></div>
            <div class="typing-bubble" id="typingIndicator">
                <span class="dot"></span>
                <span class="dot"></span>
                <span class="dot"></span>
            </div>
            <div class="message-input-area" id="messageInputArea" style="display: none;">
                <div class="input-actions">
                    <button onclick="toggleEmojiPicker()">😊</button>
                    <button onclick="attachFile()">📎</button>
                </div>
                <input type="text" class="msg-input" id="msgInput" placeholder="اكتب رسالتك هنا..." onkeypress="if(event.key==='Enter') sendMessage()" oninput="onTyping()">
                <button class="send-btn" onclick="sendMessage()">➤</button>
            </div>
        </div>
    </div>

    <!-- الإيموجي بيكر -->
    <div class="emoji-picker" id="emojiPicker">
        <div class="emoji-grid" id="emojiGrid"></div>
    </div>

    <!-- نافذة إنشاء مجموعة (محسنة) -->
    <div class="modal-overlay" id="groupModal">
        <div class="modal-box">
            <h3>🔹 إنشاء مجموعة جديدة</h3>
            <input type="text" id="groupName" placeholder="أدخل اسم المجموعة">
            <div class="modal-actions">
                <button class="btn-primary" onclick="createGroup()">إنشاء</button>
                <button class="btn-secondary" onclick="closeGroupModal()">إلغاء</button>
            </div>
        </div>
    </div>

    <script>
        // ================== إعدادات Firebase ==================
        const firebaseConfig = {
            apiKey: "AIzaSyAbg5oNdSLC76uzhaDDopF_uy9qIlF7YA4",
            authDomain: "test-f4ed9.firebaseapp.com",
            databaseURL: "https://test-f4ed9-default-rtdb.firebaseio.com",
            projectId: "test-f4ed9",
            storageBucket: "test-f4ed9.firebasestorage.app",
            messagingSenderId: "885610633192",
            appId: "1:885610633192:web:11d3f32f6008bb590d765d",
            measurementId: "G-3N1VZFQBN7"
        };
        firebase.initializeApp(firebaseConfig);
        const auth = firebase.auth();
        const db = firebase.database();

        // ================== المتغيرات العامة ==================
        let currentUser = null;
        let currentUserId = null;
        let currentChatId = null;
        let allUsers = {};
        let messagesListener = null;
        let typingTimeout = null;
        let lastMessagesCache = {};
        let selectedImageBase64 = null; // للصورة المختارة (تستخدم في الإعداد والتعديل)
        let isUsernameAvailable = false;
        let editMode = 'setup'; // 'setup' أو 'edit'

        // عناصر DOM
        const loginScreen = document.getElementById('loginScreen');
        const profileSetupScreen = document.getElementById('profileSetupScreen');
        const profileEditScreen = document.getElementById('profileEditScreen');
        const welcomeScreen = document.getElementById('welcomeScreen');
        const chatHeader = document.getElementById('chatHeader');
        const messageInputArea = document.getElementById('messageInputArea');
        const messagesArea = document.getElementById('messagesArea');
        const contactsList = document.getElementById('contactsList');
        const myNameSpan = document.getElementById('myName');
        const myAvatar = document.getElementById('myAvatar');
        const headerAvatar = document.getElementById('headerAvatar');
        const headerName = document.getElementById('headerName');
        const headerStatus = document.getElementById('headerStatus');
        const msgInput = document.getElementById('msgInput');
        const typingIndicator = document.getElementById('typingIndicator');
        const searchInput = document.getElementById('searchInput');
        const notif = document.getElementById('notification');
        const notifAvatar = document.getElementById('notifAvatar');
        const notifText = document.getElementById('notifText');
        const sidebar = document.getElementById('sidebar');

        // عناصر شاشة الإعداد
        const setupUsername = document.getElementById('setupUsername');
        const setupName = document.getElementById('setupName');
        const usernameAvailability = document.getElementById('usernameAvailability');
        const completeBtn = document.getElementById('completeProfileBtn');
        const profilePreview = document.getElementById('profilePreview');

        // عناصر شاشة التعديل
        const editUsername = document.getElementById('editUsername');
        const editName = document.getElementById('editName');
        const editUsernameAvailability = document.getElementById('editUsernameAvailability');
        const saveProfileBtn = document.getElementById('saveProfileBtn');
        const editProfilePreview = document.getElementById('editProfilePreview');

        // ================== دوال مساعدة ==================
        function showNotification(avatar, text) {
            if (notif && notifAvatar && notifText) {
                if (avatar && avatar.startsWith('data:image')) {
                    notifAvatar.innerHTML = `<img src="${avatar}" style="width:100%;height:100%;border-radius:50%;object-fit:cover;">`;
                } else {
                    notifAvatar.textContent = avatar || '👤';
                }
                notifText.textContent = text;
                notif.classList.add('show');
                setTimeout(() => notif.classList.remove('show'), 3000);
            }
        }

        function switchTab(btn, tab) {
            document.querySelectorAll('.tab-btn').forEach(b => b.classList.remove('active'));
            btn.classList.add('active');
            filterContacts();
        }

        function filterContacts() {
            if (!searchInput) return;
            const searchTerm = searchInput.value.trim().toLowerCase();
            const activeTab = document.querySelector('.tab-btn.active').textContent.includes('الكل') ? 'all' :
                              document.querySelector('.tab-btn.active').textContent.includes('متصل') ? 'online' : 'groups';
            renderContactsList(searchTerm, activeTab);
        }

        // عرض/إخفاء القائمة الجانبية في الجوال
        function toggleSidebar() {
            if (sidebar) sidebar.classList.toggle('show');
        }

        // ================== تسجيل الدخول عبر Google ==================
        async function loginWithGoogle() {
            try {
                if (!auth) throw new Error('Firebase Auth not initialized');
                const provider = new firebase.auth.GoogleAuthProvider();
                await auth.signInWithPopup(provider);
            } catch (error) {
                console.error('Google Sign-In Error:', error);
                if (error.code === 'auth/popup-closed-by-user') {
                    alert('تم إغلاق نافذة تسجيل الدخول، حاول مرة أخرى.');
                } else if (error.code === 'auth/unauthorized-domain') {
                    alert('هذا النطاق غير مسموح به. الرجاء إضافة النطاق (ayadshll.github.io) في Firebase Console.');
                } else if (error.code === 'auth/argument-error') {
                    alert('خطأ في تمرير المعاملات. يرجى التحقق من إعدادات Firebase وتحديث الصفحة.');
                } else {
                    alert('فشل تسجيل الدخول: ' + error.message);
                }
            }
        }

        // ================== مراقبة حالة المصادقة ==================
        auth.onAuthStateChanged(async (user) => {
            if (user) {
                currentUser = user;
                currentUserId = user.uid;

                const userDoc = await db.ref('users/' + user.uid).once('value');
                if (userDoc.exists()) {
                    // مستخدم مكتمل
                    loginScreen.style.display = 'none';
                    profileSetupScreen.style.display = 'none';
                    profileEditScreen.style.display = 'none';
                    initializeApp();
                } else {
                    // مستخدم جديد
                    loginScreen.style.display = 'none';
                    profileSetupScreen.style.display = 'flex';
                    profileEditScreen.style.display = 'none';
                    if (setupName) setupName.value = user.displayName || '';
                }
            } else {
                currentUser = null;
                currentUserId = null;
                loginScreen.style.display = 'flex';
                profileSetupScreen.style.display = 'none';
                profileEditScreen.style.display = 'none';
                welcomeScreen.style.display = 'flex';
                chatHeader.style.display = 'none';
                messageInputArea.style.display = 'none';
                messagesArea.innerHTML = '';
                if (messagesListener) messagesListener.off();
                if (sidebar) sidebar.classList.remove('show');
            }
        });

        // ================== التحقق من توفر اسم المستخدم (للإعداد) ==================
        if (setupUsername) {
            setupUsername.addEventListener('input', async function(e) {
                const username = e.target.value.trim().toLowerCase().replace(/\s+/g, '');
                await checkUsernameAvailability(username, 'setup');
            });
        }

        // التحقق من توفر اسم المستخدم (للتعديل)
        if (editUsername) {
            editUsername.addEventListener('input', async function(e) {
                const username = e.target.value.trim().toLowerCase().replace(/\s+/g, '');
                await checkUsernameAvailability(username, 'edit');
            });
        }

        async function checkUsernameAvailability(username, mode) {
            const availabilityDiv = mode === 'setup' ? usernameAvailability : editUsernameAvailability;
            const btn = mode === 'setup' ? completeBtn : saveProfileBtn;
            if (!username) {
                if (availabilityDiv) availabilityDiv.innerHTML = '';
                if (mode === 'setup') isUsernameAvailable = false;
                if (btn) btn.disabled = true;
                return;
            }
            const usernameRegex = /^[a-z0-9_]+$/;
            if (!usernameRegex.test(username)) {
                if (availabilityDiv) availabilityDiv.innerHTML = '<span style="color: #ef4444;">يُسمح بأحرف إنجليزية صغيرة وأرقام و _</span>';
                if (mode === 'setup') isUsernameAvailable = false;
                if (btn) btn.disabled = true;
                return;
            }
            // إذا كان في وضع التعديل، يجب السماح بنفس اسم المستخدم الحالي
            if (mode === 'edit' && allUsers[currentUserId] && allUsers[currentUserId].username === username) {
                if (availabilityDiv) availabilityDiv.innerHTML = '<span style="color: #22c55e;">✔️ اسمك الحالي</span>';
                if (btn) btn.disabled = false;
                return;
            }
            const snapshot = await db.ref('usernames/' + username).once('value');
            if (snapshot.exists() && snapshot.val() !== currentUserId) {
                if (availabilityDiv) availabilityDiv.innerHTML = '<span style="color: #ef4444;">اسم المستخدم غير متاح</span>';
                if (mode === 'setup') isUsernameAvailable = false;
                if (btn) btn.disabled = true;
            } else {
                if (availabilityDiv) availabilityDiv.innerHTML = '<span style="color: #22c55e;">✔️ متاح</span>';
                if (mode === 'setup') isUsernameAvailable = true;
                if (btn) btn.disabled = false;
            }
        }

        // ================== اختيار الصورة وتحويلها لـ Base64 ==================
        function selectImage(mode) {
            const input = document.createElement('input');
            input.type = 'file';
            input.accept = 'image/*';
            input.onchange = (e) => {
                const file = e.target.files[0];
                if (file) {
                    const reader = new FileReader();
                    reader.onload = (event) => {
                        selectedImageBase64 = event.target.result;
                        if (mode === 'setup' && profilePreview) {
                            profilePreview.innerHTML = `<img src="${selectedImageBase64}">`;
                            if (isUsernameAvailable && completeBtn) completeBtn.disabled = false;
                        } else if (mode === 'edit' && editProfilePreview) {
                            editProfilePreview.innerHTML = `<img src="${selectedImageBase64}">`;
                        }
                    };
                    reader.readAsDataURL(file);
                }
            };
            input.click();
        }

        // ================== إكمال الملف الشخصي (للمستخدم الجديد) ==================
        async function completeProfile() {
            if (!setupUsername || !setupName || !selectedImageBase64) {
                alert('الرجاء إكمال جميع الحقول واختيار صورة');
                return;
            }
            const username = setupUsername.value.trim().toLowerCase().replace(/\s+/g, '');
            const name = setupName.value.trim();
            if (!username || !name) {
                alert('الرجاء إكمال جميع الحقول');
                return;
            }
            completeBtn.disabled = true;
            completeBtn.textContent = 'جاري الحفظ...';

            try {
                await db.ref('usernames/' + username).set(currentUserId);
                await db.ref('users/' + currentUserId).set({
                    name: name,
                    username: username,
                    avatar: selectedImageBase64,
                    email: currentUser.email,
                    online: true,
                    lastSeen: firebase.database.ServerValue.TIMESTAMP
                });
                profileSetupScreen.style.display = 'none';
                initializeApp();
            } catch (error) {
                console.error(error);
                alert('فشل إكمال التسجيل: ' + error.message);
                completeBtn.disabled = false;
                completeBtn.textContent = 'إكمال التسجيل';
            }
        }

        // ================== فتح صفحة تعديل البروفايل ==================
        function openProfileEdit() {
            if (!allUsers[currentUserId]) return;
            const user = allUsers[currentUserId];
            editUsername.value = user.username || '';
            editName.value = user.name || '';
            if (user.avatar) {
                editProfilePreview.innerHTML = `<img src="${user.avatar}">`;
            } else {
                editProfilePreview.innerHTML = '<span>الصورة الحالية</span>';
            }
            selectedImageBase64 = null; // إعادة تعيين الصورة المختارة
            profileEditScreen.style.display = 'flex';
            // إخفاء الشريط الجانبي في الجوال
            if (sidebar) sidebar.classList.remove('show');
        }

        function closeProfileEdit() {
            profileEditScreen.style.display = 'none';
        }

        // ================== حفظ تغييرات البروفايل ==================
        async function saveProfileChanges() {
            const newUsername = editUsername.value.trim().toLowerCase().replace(/\s+/g, '');
            const newName = editName.value.trim();
            if (!newUsername || !newName) {
                alert('الرجاء إكمال جميع الحقول');
                return;
            }
            saveProfileBtn.disabled = true;
            saveProfileBtn.textContent = 'جاري الحفظ...';

            try {
                const oldUsername = allUsers[currentUserId].username;
                // إذا تغير اسم المستخدم، حدث عقدة usernames
                if (oldUsername !== newUsername) {
                    // تحقق مرة أخرى من توفر الاسم الجديد (لأنه قد يكون تغير بعد التحقق)
                    const snapshot = await db.ref('usernames/' + newUsername).once('value');
                    if (snapshot.exists() && snapshot.val() !== currentUserId) {
                        alert('اسم المستخدم غير متاح');
                        saveProfileBtn.disabled = false;
                        saveProfileBtn.textContent = 'حفظ التغييرات';
                        return;
                    }
                    // حذف الاسم القديم وإضافة الجديد
                    await db.ref('usernames/' + oldUsername).remove();
                    await db.ref('usernames/' + newUsername).set(currentUserId);
                }

                // تحديث بيانات المستخدم
                const updates = {
                    name: newName,
                    username: newUsername
                };
                if (selectedImageBase64) {
                    updates.avatar = selectedImageBase64;
                }
                await db.ref('users/' + currentUserId).update(updates);

                profileEditScreen.style.display = 'none';
                showNotification('✅', 'تم تحديث الملف الشخصي');
            } catch (error) {
                console.error(error);
                alert('فشل حفظ التغييرات: ' + error.message);
            } finally {
                saveProfileBtn.disabled = false;
                saveProfileBtn.textContent = 'حفظ التغييرات';
            }
        }

        // ================== تهيئة التطبيق الرئيسي ==================
        function initializeApp() {
            db.ref('users').on('value', (snapshot) => {
                allUsers = snapshot.val() || {};
                filterContacts();
                if (myAvatar && allUsers[currentUserId]) {
                    if (allUsers[currentUserId].avatar) {
                        myAvatar.innerHTML = `<img src="${allUsers[currentUserId].avatar}" style="width:100%;height:100%;border-radius:50%;object-fit:cover;">`;
                    } else {
                        myAvatar.innerHTML = '😎';
                    }
                }
                if (myNameSpan && allUsers[currentUserId]) {
                    myNameSpan.textContent = allUsers[currentUserId].name || 'أنا';
                }
            });

            if (currentUserId) {
                db.ref('lastMessages/' + currentUserId).on('value', (snapshot) => {
                    lastMessagesCache = snapshot.val() || {};
                    filterContacts();
                });
            }

            listenForNewMessages();
            welcomeScreen.style.display = 'flex';
            chatHeader.style.display = 'none';
            messageInputArea.style.display = 'none';
        }

        // ================== دوال الدردشة ==================
        function renderContactsList(searchTerm = '', filter = 'all') {
            if (!contactsList || !allUsers || !currentUserId) return;
            let html = '';
            const usersArray = Object.entries(allUsers).filter(([uid, user]) => uid !== currentUserId);
            usersArray.forEach(([uid, user]) => {
                const matchesSearch = searchTerm === '' || 
                    (user.name && user.name.toLowerCase().includes(searchTerm)) || 
                    (user.username && user.username.toLowerCase().includes(searchTerm));
                if (!matchesSearch) return;
                if (filter === 'online' && !user.online) return;
                if (filter === 'groups') return; // تجاهل المجموعات حالياً
                const statusClass = user.online ? 'online' : 'offline';
                const lastMsgData = lastMessagesCache[uid] || {};
                const lastMsgText = lastMsgData.text || 'لا توجد رسائل';
                const lastMsgTime = lastMsgData.timestamp ? new Date(lastMsgData.timestamp).toLocaleTimeString('ar-EG', { hour: '2-digit', minute: '2-digit' }) : '';
                const avatarHtml = user.avatar ? `<img src="${user.avatar}" style="width:100%;height:100%;border-radius:50%;object-fit:cover;">` : '😎';
                html += `<div class="contact-item" onclick="openChat('${uid}')">
                    <div class="contact-avatar">${avatarHtml}<span class="status-dot ${statusClass}"></span></div>
                    <div class="contact-info">
                        <div class="contact-name">${user.name || 'مستخدم'} <span style="color:var(--text-light);font-size:11px;">@${user.username||''}</span></div>
                        <div class="contact-last-msg">${lastMsgText}</div>
                    </div>
                    <div class="contact-meta"><span class="msg-time">${lastMsgTime}</span></div>
                </div>`;
            });
            contactsList.innerHTML = html || '<div style="color: var(--text-light); text-align: center; padding: 20px;">لا يوجد مستخدمين</div>';
        }

        function openChat(otherUid) {
            if (!currentUserId || !allUsers[otherUid]) return;
            currentChatId = otherUid;
            welcomeScreen.style.display = 'none';
            chatHeader.style.display = 'flex';
            messageInputArea.style.display = 'flex';

            const otherUser = allUsers[otherUid];
            if (otherUser) {
                if (headerAvatar) {
                    headerAvatar.innerHTML = otherUser.avatar ? `<img src="${otherUser.avatar}" style="width:44px;height:44px;border-radius:50%;object-fit:cover;">` : '😎';
                }
                if (headerName) headerName.textContent = otherUser.name || 'مستخدم';
                if (headerStatus) {
                    headerStatus.textContent = otherUser.online ? 'متصل' : 'غير متصل';
                    headerStatus.className = 'header-status ' + (otherUser.online ? 'online' : 'offline');
                }
            }
            if (messagesListener) messagesListener.off();
            const messagesRef = db.ref('messages');
            const query = messagesRef.orderByChild('timestamp').limitToLast(50);
            messagesListener = query.on('value', (snapshot) => {
                renderMessages(snapshot.val() || {});
            });
            listenForTyping();
            // في الجوال، إخفاء الشريط الجانبي بعد فتح الدردشة
            if (sidebar) sidebar.classList.remove('show');
        }

        function renderMessages(messagesObj) {
            if (!messagesArea || !currentChatId || !currentUserId) return;
            const messagesArray = Object.entries(messagesObj)
                .map(([key, msg]) => ({ id: key, ...msg }))
                .filter(msg => (msg.senderId === currentUserId && msg.receiverId === currentChatId) ||
                               (msg.senderId === currentChatId && msg.receiverId === currentUserId))
                .sort((a, b) => a.timestamp - b.timestamp);
            if (messagesArray.length === 0) {
                messagesArea.innerHTML = '<div style="text-align: center; color: var(--text-light); padding: 20px;">لا توجد رسائل بعد</div>';
                return;
            }
            let html = '';
            let lastDate = null;
            messagesArray.forEach(msg => {
                const msgDate = new Date(msg.timestamp);
                const dateStr = msgDate.toLocaleDateString('ar-EG');
                const timeStr = msgDate.toLocaleTimeString('ar-EG', { hour: '2-digit', minute: '2-digit' });
                if (dateStr !== lastDate) {
                    html += `<div class="date-separator"><span>${dateStr}</span></div>`;
                    lastDate = dateStr;
                }
                const isSent = msg.senderId === currentUserId;
                const senderName = isSent ? '' : (allUsers[msg.senderId]?.name || 'مستخدم');
                html += `<div class="message-wrapper ${isSent ? 'sent' : 'received'}">
                    <div class="message-bubble">
                        ${!isSent ? `<span class="sender-name">${senderName}</span>` : ''}
                        ${msg.text}
                        <div class="msg-time"><span>${timeStr}</span>${isSent ? '<span class="msg-check">✓✓</span>' : ''}</div>
                    </div>
                </div>`;
            });
            messagesArea.innerHTML = html;
            messagesArea.scrollTop = messagesArea.scrollHeight;
        }

        async function sendMessage() {
            if (!msgInput || !currentChatId || !currentUserId) return;
            const text = msgInput.value.trim();
            if (!text) return;
            const message = {
                senderId: currentUserId,
                receiverId: currentChatId,
                text: text,
                timestamp: firebase.database.ServerValue.TIMESTAMP,
                type: 'text'
            };
            try {
                await db.ref('messages').push(message);
                msgInput.value = '';
                if (currentUserId && currentChatId) {
                    await db.ref('lastMessages/' + currentUserId + '/' + currentChatId).set({ text: text, timestamp: firebase.database.ServerValue.TIMESTAMP });
                    await db.ref('lastMessages/' + currentChatId + '/' + currentUserId).set({ text: text, timestamp: firebase.database.ServerValue.TIMESTAMP });
                }
                if (typingTimeout) clearTimeout(typingTimeout);
                if (currentChatId && currentUserId) {
                    await db.ref('typing/' + currentChatId + '/' + currentUserId).remove();
                }
            } catch (error) {
                console.error(error);
                alert('فشل إرسال الرسالة');
            }
        }

        function onTyping() {
            if (!currentChatId || !currentUserId) return;
            db.ref('typing/' + currentChatId + '/' + currentUserId).set(true);
            if (typingTimeout) clearTimeout(typingTimeout);
            typingTimeout = setTimeout(() => {
                db.ref('typing/' + currentChatId + '/' + currentUserId).remove();
            }, 2000);
        }

        function listenForTyping() {
            if (!currentChatId || !currentUserId || !typingIndicator) return;
            db.ref('typing/' + currentChatId).on('value', (snapshot) => {
                const typingData = snapshot.val() || {};
                const isOtherTyping = Object.keys(typingData).some(uid => uid !== currentUserId);
                if (isOtherTyping) {
                    typingIndicator.classList.add('show');
                } else {
                    typingIndicator.classList.remove('show');
                }
            });
        }

        function listenForNewMessages() {
            if (!currentUserId) return;
            db.ref('messages').orderByChild('timestamp').limitToLast(1).on('child_added', (snapshot) => {
                const msg = snapshot.val();
                if (msg.receiverId === currentUserId && msg.senderId !== currentUserId) {
                    const sender = allUsers[msg.senderId];
                    if (sender) showNotification(sender.avatar || '👤', `رسالة جديدة من ${sender.name}`);
                }
            });
        }

        function goBack() {
            welcomeScreen.style.display = 'flex';
            chatHeader.style.display = 'none';
            messageInputArea.style.display = 'none';
            messagesArea.innerHTML = '';
            if (messagesListener) {
                messagesListener.off();
                messagesListener = null;
            }
            currentChatId = null;
            // في الجوال، إظهار الشريط الجانبي
            if (sidebar) sidebar.classList.add('show');
        }

        function toggleEmojiPicker() {
            const picker = document.getElementById('emojiPicker');
            if (!picker) return;
            picker.classList.toggle('show');
            if (picker.classList.contains('show')) {
                const grid = document.getElementById('emojiGrid');
                if (grid && grid.children.length === 0) {
                    const emojis = ['😊', '😂', '❤️', '👍', '🎉', '🔥', '😢', '😡', '😍', '🥺', '😎', '🤔', '👏', '🙏', '💪', '🍕'];
                    grid.innerHTML = emojis.map(e => `<div class="emoji-item" onclick="insertEmoji('${e}')">${e}</div>`).join('');
                }
            }
        }

        function insertEmoji(emoji) {
            if (msgInput) msgInput.value += emoji;
            toggleEmojiPicker();
        }

        function attachFile() { alert('خاصية إرفاق الملفات قيد التطوير'); }

        // نافذة إنشاء مجموعة
        function openNewGroupModal() {
            document.getElementById('groupModal').classList.add('show');
        }
        function closeGroupModal() {
            document.getElementById('groupModal').classList.remove('show');
        }
        function createGroup() {
            const groupName = document.getElementById('groupName').value.trim();
            if (!groupName) {
                alert('الرجاء إدخال اسم المجموعة');
                return;
            }
            alert('سيتم تفعيل المجموعات قريباً');
            closeGroupModal();
        }

        function openChatSettings() { alert('إعدادات المحادثة قيد التطوير'); }

        // إغلاق الإيموجي بيكر عند النقر خارجها
        window.onclick = function(event) {
            const picker = document.getElementById('emojiPicker');
            if (picker && !event.target.closest('.input-actions button') && !event.target.closest('.emoji-picker')) {
                picker.classList.remove('show');
            }
        };

        // بداية التطبيق
        window.onload = function() {
            loginScreen.style.display = 'flex';
            profileSetupScreen.style.display = 'none';
            profileEditScreen.style.display = 'none';
        };

        // تسجيل الخروج
        async function logout() {
            if (currentUserId) {
                await db.ref('users/' + currentUserId).update({ online: false });
            }
            await auth.signOut();
        }
    </script>
</body>
</html>
