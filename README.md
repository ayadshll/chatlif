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
        /* جميع الأنماط (كما هي في الرد السابق) - أضعها كاملة هنا للاختصار */
        /* ... (نفس الـ styles السابقة) ... */
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
                    <button class="login-btn" style="flex: 1; padding: 0.7rem;" onclick="selectImage('setup')">اختر صورة</button>
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
                    <button class="login-btn" style="flex: 1; padding: 0.7rem;" onclick="selectImage('edit')">اختر صورة جديدة</button>
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

    <!-- نافذة إنشاء مجموعة -->
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
        let selectedImageBase64 = null;
        let isUsernameAvailable = false;

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

        // تحسين دالة filterContacts مع سجلات للمساعدة في التصحيح
        function filterContacts() {
            if (!searchInput) return;
            const searchTerm = searchInput.value.trim().toLowerCase();
            const activeTab = document.querySelector('.tab-btn.active').textContent.includes('الكل') ? 'all' :
                              document.querySelector('.tab-btn.active').textContent.includes('متصل') ? 'online' : 'groups';
            console.log('Filtering with term:', searchTerm, 'tab:', activeTab);
            renderContactsList(searchTerm, activeTab);
        }

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

        // ================== التحقق من توفر اسم المستخدم ==================
        if (setupUsername) {
            setupUsername.addEventListener('input', async function(e) {
                const username = e.target.value.trim().toLowerCase().replace(/\s+/g, '');
                await checkUsernameAvailability(username, 'setup');
            });
        }

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
            // إذا كان في وضع التعديل والاسم هو نفسه الاسم الحالي، نعتبره متاحاً
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

        // ================== إكمال الملف الشخصي ==================
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

        // ================== فتح وتعديل البروفايل (تم إصلاحها) ==================
        function openProfileEdit() {
            console.log('openProfileEdit called, currentUserId:', currentUserId);
            if (!currentUserId) {
                alert('يجب تسجيل الدخول أولاً');
                return;
            }
            if (!allUsers[currentUserId]) {
                console.log('User data not loaded yet, allUsers:', allUsers);
                alert('بيانات المستخدم لم تحمل بعد، حاول مرة أخرى');
                return;
            }
            const user = allUsers[currentUserId];
            editUsername.value = user.username || '';
            editName.value = user.name || '';
            if (user.avatar) {
                editProfilePreview.innerHTML = `<img src="${user.avatar}">`;
            } else {
                editProfilePreview.innerHTML = '<span>الصورة الحالية</span>';
            }
            selectedImageBase64 = null;
            profileEditScreen.style.display = 'flex';
            if (sidebar) sidebar.classList.remove('show');
        }

        function closeProfileEdit() {
            profileEditScreen.style.display = 'none';
        }

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
                if (oldUsername !== newUsername) {
                    const snapshot = await db.ref('usernames/' + newUsername).once('value');
                    if (snapshot.exists() && snapshot.val() !== currentUserId) {
                        alert('اسم المستخدم غير متاح');
                        saveProfileBtn.disabled = false;
                        saveProfileBtn.textContent = 'حفظ التغييرات';
                        return;
                    }
                    await db.ref('usernames/' + oldUsername).remove();
                    await db.ref('usernames/' + newUsername).set(currentUserId);
                }
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
                console.log('allUsers updated:', allUsers);
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
            if (!contactsList || !allUsers || !currentUserId) {
                console.log('renderContactsList: missing data', { contactsList, allUsers, currentUserId });
                return;
            }
            console.log('Rendering contacts, allUsers:', allUsers, 'currentUserId:', currentUserId);
            let html = '';
            const usersArray = Object.entries(allUsers).filter(([uid, user]) => uid !== currentUserId);
            console.log('usersArray:', usersArray);
            
            usersArray.forEach(([uid, user]) => {
                // تحسين البحث: البحث في الاسم واسم المستخدم
                const nameMatch = user.name && user.name.toLowerCase().includes(searchTerm);
                const usernameMatch = user.username && user.username.toLowerCase().includes(searchTerm);
                const matchesSearch = searchTerm === '' || nameMatch || usernameMatch;
                
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
            console.log('Contacts list updated, HTML length:', html.length);
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

        // ================== تحسين المجموعات ==================
        function openNewGroupModal() { 
            const modal = document.getElementById('groupModal');
            if (modal) modal.classList.add('show'); 
        }
        function closeGroupModal() { 
            const modal = document.getElementById('groupModal');
            if (modal) modal.classList.remove('show'); 
        }
        function createGroup() {
            const groupName = document.getElementById('groupName').value.trim();
            if (!groupName) { 
                alert('الرجاء إدخال اسم المجموعة'); 
                return; 
            }
            // يمكنك لاحقاً إضافة منطق حقيقي لإنشاء المجموعات
            alert('سيتم تفعيل المجموعات قريباً، شكراً لاهتمامك!');
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

        window.onload = function() {
            loginScreen.style.display = 'flex';
            profileSetupScreen.style.display = 'none';
            profileEditScreen.style.display = 'none';
        };

        async function logout() {
            if (currentUserId) {
                await db.ref('users/' + currentUserId).update({ online: false });
            }
            await auth.signOut();
        }
    </script>
</body>
</html>
