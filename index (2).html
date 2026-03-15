<!DOCTYPE html>
<html lang="ar" dir="rtl">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>💬 شات لايف - دخول عبر Google</title>
    <!-- الخطوط -->
    <link href="https://fonts.googleapis.com/css2?family=Tajawal:wght@300;400;500;700;800&display=swap" rel="stylesheet">
    <!-- Firebase SDK -->
    <script src="https://www.gstatic.com/firebasejs/10.7.1/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/10.7.1/firebase-auth-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/10.7.1/firebase-database-compat.js"></script>
    <style>
        /* الأنماط الكاملة - نفس التصميم السابق مع إضافات بسيطة */
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
        @media (min-width: 1400px) {
            .app-container {
                height: 90vh;
            }
        }
        /* شاشة تسجيل الدخول */
        .login-screen {
            position: fixed;
            inset: 0;
            background: linear-gradient(135deg, #0f172a 0%, #1e1b4b 50%, #0f172a 100%);
            display: flex;
            align-items: center;
            justify-content: center;
            z-index: 9999;
            transition: opacity 0.5s, transform 0.5s;
        }
        .login-screen.hidden {
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
            max-width: 90%;
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
        .login-btn {
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
        }
        .login-btn:hover {
            transform: translateY(-2px);
            box-shadow: 0 8px 25px rgba(79, 70, 229, 0.4);
        }
        .login-btn:active {
            transform: translateY(0);
        }
        .login-btn:disabled {
            opacity: 0.5;
            cursor: not-allowed;
            transform: none;
        }
        /* شاشة إكمال الملف الشخصي */
        .profile-setup-screen {
            position: fixed;
            inset: 0;
            background: linear-gradient(135deg, #0f172a 0%, #1e1b4b 50%, #0f172a 100%);
            display: flex;
            align-items: center;
            justify-content: center;
            z-index: 10000;
        }
        .profile-setup-screen .login-box {
            max-width: 450px;
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
        #usernameAvailability {
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
        /* باقي الأنماط (الشريط الجانبي، منطقة الدردشة) من الكود السابق - تم حذفها للاختصار، لكن يجب تضمينها كاملة هنا */
        /* ... (سيتم إضافتها في الكود النهائي الكامل) ... */
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

    <!-- شاشة إكمال الملف الشخصي (تظهر بعد تسجيل الدخول لأول مرة) -->
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
                    <button class="login-btn" style="flex: 1; padding: 10px; margin:0;" onclick="selectImage()">اختر صورة</button>
                    <div class="image-preview" id="profilePreview">
                        <span>لا توجد</span>
                    </div>
                </div>
            </div>

            <button class="login-btn" onclick="completeProfile()" id="completeProfileBtn" disabled>إكمال التسجيل</button>
        </div>
    </div>

    <!-- الإشعار -->
    <div class="notification" id="notification">
        <div class="notif-avatar" id="notifAvatar"></div>
        <span id="notifText"></span>
    </div>

    <!-- التطبيق الرئيسي (الشريط الجانبي + الدردشة) - نفس الكود السابق -->
    <div class="app-container">
        <!-- الشريط الجانبي -->
        <div class="sidebar" id="sidebar">
            <!-- محتوى الشريط الجانبي مطابق للسابق مع تعديل عرض الصورة -->
            <!-- سنضيفه كاملاً هنا في الكود النهائي، لكن للاختصار أكتفي بالإشارة -->
        </div>
        <!-- منطقة الدردشة -->
        <div class="chat-main" id="chatMain">
            <!-- محتوى الدردشة -->
        </div>
    </div>

    <!-- الإيموجي بيكر -->
    <div class="emoji-picker" id="emojiPicker">
        <div class="emoji-grid" id="emojiGrid"></div>
    </div>

    <!-- نافذة إنشاء مجموعة -->
    <div class="modal-overlay" id="groupModal">
        <div class="modal-box">
            <h3>إنشاء مجموعة جديدة</h3>
            <input type="text" id="groupName" placeholder="اسم المجموعة">
            <div class="modal-actions">
                <button class="btn-primary" onclick="createGroup()">إنشاء</button>
                <button class="btn-secondary" onclick="closeGroupModal()">إلغاء</button>
            </div>
        </div>
    </div>

    <script>
        // ================== تهيئة Firebase ==================
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
        let currentChatType = 'user';
        let allUsers = {};
        let messagesListener = null;
        let typingTimeout = null;
        let lastMessagesCache = {};
        let selectedImageBase64 = null;
        let isUsernameAvailable = false;

        // عناصر DOM
        const loginScreen = document.getElementById('loginScreen');
        const profileSetupScreen = document.getElementById('profileSetupScreen');
        const welcomeScreen = document.getElementById('welcomeScreen');
        const chatHeader = document.getElementById('chatHeader');
        const messageInputArea = document.getElementById('messageInputArea');
        const messagesArea = document.getElementById('messagesArea');
        const contactsList = document.getElementById('contactsList');
        const myNameSpan = document.getElementById('myName');
        const myAvatar = document.getElementById('myAvatar'); // عنصر عرض الصورة
        const headerAvatar = document.getElementById('headerAvatar');
        const headerName = document.getElementById('headerName');
        const headerStatus = document.getElementById('headerStatus');
        const msgInput = document.getElementById('msgInput');
        const typingIndicator = document.getElementById('typingIndicator');
        const usernameInput = document.getElementById('setupUsername');
        const nameInput = document.getElementById('setupName');
        const usernameAvailability = document.getElementById('usernameAvailability');
        const completeBtn = document.getElementById('completeProfileBtn');
        const profilePreview = document.getElementById('profilePreview');

        // ================== دوال مساعدة ==================
        function showNotification(avatar, text) {
            const notif = document.getElementById('notification');
            document.getElementById('notifAvatar').textContent = avatar;
            document.getElementById('notifText').textContent = text;
            notif.classList.add('show');
            setTimeout(() => notif.classList.remove('show'), 3000);
        }

        function filterContacts() { /* نفس الكود القديم */ }
        function switchTab(btn, tab) { /* نفس الكود القديم */ }
        function renderContactsList(searchTerm = '', filter = 'all') {
            // مع تعديل عرض الصورة
            if (!allUsers || !currentUserId) return;
            let html = '';
            const usersArray = Object.entries(allUsers).filter(([uid, user]) => uid !== currentUserId);
            usersArray.forEach(([uid, user]) => {
                const matchesSearch = searchTerm === '' || user.name.toLowerCase().includes(searchTerm) || (user.username && user.username.toLowerCase().includes(searchTerm));
                if (!matchesSearch) return;
                if (filter === 'online' && !user.online) return;
                const statusClass = user.online ? 'online' : 'offline';
                const lastMsgData = lastMessagesCache[uid] || {};
                const lastMsgText = lastMsgData.text || 'لا توجد رسائل';
                const lastMsgTime = lastMsgData.timestamp ? new Date(lastMsgData.timestamp).toLocaleTimeString('ar-EG', { hour: '2-digit', minute: '2-digit' }) : '';
                const avatarHtml = user.avatar ? `<img src="${user.avatar}" style="width:100%;height:100%;border-radius:50%;object-fit:cover;">` : '😎';
                html += `<div class="contact-item" onclick="openChat('${uid}')">
                    <div class="contact-avatar">${avatarHtml}<span class="status-dot ${statusClass}"></span></div>
                    <div class="contact-info">
                        <div class="contact-name">${user.name} <span style="color:var(--text-light);font-size:11px;">@${user.username||''}</span></div>
                        <div class="contact-last-msg">${lastMsgText}</div>
                    </div>
                    <div class="contact-meta"><span class="msg-time">${lastMsgTime}</span></div>
                </div>`;
            });
            contactsList.innerHTML = html || '<div style="color: var(--text-light); text-align: center; padding: 20px;">لا يوجد مستخدمين</div>';
        }

        // ================== تسجيل الدخول عبر Google ==================
        async function loginWithGoogle() {
            const provider = new firebase.auth.GoogleAuthProvider();
            try {
                const result = await auth.signInWithPopup(provider);
                // user object
            } catch (error) {
                alert('فشل تسجيل الدخول: ' + error.message);
            }
        }

        // ================== مراقبة حالة المصادقة ==================
        auth.onAuthStateChanged(async (user) => {
            if (user) {
                currentUser = user;
                currentUserId = user.uid;

                // تحقق مما إذا كان المستخدم مكتمل البيانات
                const userDoc = await db.ref('users/' + user.uid).once('value');
                if (userDoc.exists()) {
                    // مستخدم مكتمل → نبدأ التطبيق
                    loginScreen.style.display = 'none';
                    profileSetupScreen.style.display = 'none';
                    initializeApp();
                } else {
                    // مستخدم جديد → نظهر شاشة إكمال الملف الشخصي
                    loginScreen.style.display = 'none';
                    profileSetupScreen.style.display = 'flex';
                    // نضع الاسم الظاهر المقترح من Google
                    document.getElementById('setupName').value = user.displayName || '';
                }
            } else {
                // لا يوجد مستخدم
                currentUser = null;
                currentUserId = null;
                loginScreen.style.display = 'flex';
                profileSetupScreen.style.display = 'none';
                // إخفاء واجهة الدردشة
                if (welcomeScreen) welcomeScreen.style.display = 'flex';
                if (chatHeader) chatHeader.style.display = 'none';
                if (messageInputArea) messageInputArea.style.display = 'none';
                if (messagesArea) messagesArea.innerHTML = '';
                if (messagesListener) messagesListener.off();
            }
        });

        // ================== التحقق من توفر اسم المستخدم ==================
        usernameInput.addEventListener('input', async function(e) {
            const username = e.target.value.trim().toLowerCase().replace(/\s+/g, '');
            if (!username) {
                usernameAvailability.innerHTML = '';
                isUsernameAvailable = false;
                completeBtn.disabled = true;
                return;
            }
            const usernameRegex = /^[a-z0-9_]+$/;
            if (!usernameRegex.test(username)) {
                usernameAvailability.innerHTML = '<span style="color: var(--red);">يُسمح بأحرف إنجليزية صغيرة وأرقام و _</span>';
                isUsernameAvailable = false;
                completeBtn.disabled = true;
                return;
            }
            const snapshot = await db.ref('usernames/' + username).once('value');
            if (snapshot.exists()) {
                usernameAvailability.innerHTML = '<span style="color: var(--red);">اسم المستخدم غير متاح</span>';
                isUsernameAvailable = false;
                completeBtn.disabled = true;
            } else {
                usernameAvailability.innerHTML = '<span style="color: var(--green);">✔️ متاح</span>';
                isUsernameAvailable = true;
                if (selectedImageBase64) completeBtn.disabled = false;
            }
        });

        // ================== اختيار صورة وتحويلها لـ Base64 ==================
        function selectImage() {
            const input = document.createElement('input');
            input.type = 'file';
            input.accept = 'image/*';
            input.onchange = (e) => {
                const file = e.target.files[0];
                if (file) {
                    const reader = new FileReader();
                    reader.onload = (event) => {
                        const base64 = event.target.result;
                        selectedImageBase64 = base64;
                        // عرض المعاينة
                        profilePreview.innerHTML = `<img src="${base64}">`;
                        if (isUsernameAvailable) completeBtn.disabled = false;
                    };
                    reader.readAsDataURL(file);
                }
            };
            input.click();
        }

        // ================== إكمال الملف الشخصي ==================
        async function completeProfile() {
            const username = usernameInput.value.trim().toLowerCase().replace(/\s+/g, '');
            const name = nameInput.value.trim();
            
            if (!username || !name || !selectedImageBase64) {
                alert('الرجاء إكمال جميع الحقول واختيار صورة');
                return;
            }

            completeBtn.disabled = true;
            completeBtn.textContent = 'جاري الحفظ...';

            try {
                // حفظ اسم المستخدم
                await db.ref('usernames/' + username).set(currentUserId);

                // حفظ بيانات المستخدم
                await db.ref('users/' + currentUserId).set({
                    name: name,
                    username: username,
                    avatar: selectedImageBase64,
                    email: currentUser.email,
                    online: true,
                    lastSeen: firebase.database.ServerValue.TIMESTAMP
                });

                // إخفاء شاشة الإعداد
                profileSetupScreen.style.display = 'none';
                initializeApp();

            } catch (error) {
                console.error(error);
                alert('فشل إكمال التسجيل: ' + error.message);
                completeBtn.disabled = false;
                completeBtn.textContent = 'إكمال التسجيل';
            }
        }

        // ================== تهيئة التطبيق الرئيسي ==================
        function initializeApp() {
            // تحميل جميع المستخدمين
            db.ref('users').on('value', (snapshot) => {
                allUsers = snapshot.val() || {};
                filterContacts();
                // تحديث الصورة الشخصية في الشريط الجانبي
                if (allUsers[currentUserId] && allUsers[currentUserId].avatar) {
                    myAvatar.innerHTML = `<img src="${allUsers[currentUserId].avatar}" style="width:100%;height:100%;border-radius:50%;object-fit:cover;">`;
                }
            });

            // تحميل آخر الرسائل
            db.ref('lastMessages/' + currentUserId).on('value', (snapshot) => {
                lastMessagesCache = snapshot.val() || {};
                filterContacts();
            });

            // الاستماع للرسائل الجديدة
            listenForNewMessages();

            // إظهار الواجهة الرئيسية
            welcomeScreen.style.display = 'flex';
            chatHeader.style.display = 'none';
            messageInputArea.style.display = 'none';
        }

        // ================== دوال الدردشة (من الكود القديم) ==================
        function openChat(otherUid) { /* ... */ }
        function sendMessage() { /* ... */ }
        function renderMessages(messagesObj) { /* ... */ }
        function onTyping() { /* ... */ }
        function listenForTyping() { /* ... */ }
        function listenForNewMessages() { /* ... */ }
        function goBack() { /* ... */ }
        function toggleEmojiPicker() { /* ... */ }
        function insertEmoji(emoji) { /* ... */ }
        function attachFile() { /* ... */ }
        function openNewGroupModal() { /* ... */ }
        function closeGroupModal() { /* ... */ }
        function createGroup() { /* ... */ }
        function openChatSettings() { /* ... */ }

        // عند تحميل الصفحة، نخفي شاشة الإعداد ونعرض شاشة الدخول
        window.onload = function() {
            loginScreen.style.display = 'flex';
            profileSetupScreen.style.display = 'none';
        };
    </script>
</body>
</html>
