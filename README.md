<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Jeewan City - Neighborhood App</title>
    <style>
        :root { --primary: #00a884; --bg: #f0f2f5; --text: #1c1e21; --secondary: #65676b; --border: #dddfe2; --urgent: #d93025; --buy-sell: #1877f2; }
        body { font-family: 'Segoe UI', Helvetica, Arial, sans-serif; background: var(--bg); color: var(--text); margin: 0; display: flex; justify-content: center; }
        
        /* Loader */
        #loader { position: fixed; inset: 0; background: white; z-index: 9999; display: flex; flex-direction: column; align-items: center; justify-content: center; }
        .spinner { width: 40px; height: 40px; border: 4px solid #f3f3f3; border-top: 4px solid var(--primary); border-radius: 50%; animation: spin 1s linear infinite; }
        @keyframes spin { 0% { transform: rotate(0deg); } 100% { transform: rotate(360deg); } }

        /* Button Spinner */
        .btn-spinner {
            display: inline-block;
            width: 14px;
            height: 14px;
            border: 2px solid rgba(255,255,255,.3);
            border-radius: 50%;
            border-top-color: #fff;
            animation: spin 1s ease-in-out infinite;
            margin-right: 8px;
            vertical-align: middle;
        }

        /* Layout */
        #main-shell { display: none; width: 100%; max-width: 550px; min-height: 100vh; background: var(--bg); padding-bottom: 50px; }
        .nav { background: white; padding: 12px 16px; display: flex; justify-content: space-between; align-items: center; position: sticky; top: 0; z-index: 100; box-shadow: 0 2px 4px rgba(0,0,0,0.08); }
        .logo { color: var(--primary); font-weight: bold; font-size: 22px; cursor: pointer; }
        .nav-profile-img { width: 38px; height: 38px; border-radius: 50%; cursor: pointer; object-fit: cover; border: 2px solid #eee; }

        /* Auth UI */
        .auth-screen { position: fixed; inset: 0; background: white; z-index: 2000; display: none; padding: 30px; text-align: center; overflow-y: auto; }
        .auth-card { max-width: 400px; margin: 20px auto; position: relative; }
        .auth-card h1 { margin-bottom: 10px; color: var(--primary); }
        .auth-card input { width: 100%; padding: 12px; margin: 8px 0; border: 1px solid #ddd; border-radius: 8px; box-sizing: border-box; font-size: 14px; }
        .auth-toggle { margin-top: 20px; color: var(--secondary); font-size: 14px; cursor: pointer; }
        .auth-toggle b { color: var(--primary); }

        /* Filter Tabs */
        .filter-tabs { background: white; padding: 10px; display: flex; gap: 8px; overflow-x: auto; white-space: nowrap; border-bottom: 1px solid var(--border); }
        .tab { padding: 8px 16px; border-radius: 20px; background: #f0f2f5; font-size: 13px; font-weight: 600; cursor: pointer; border: none; }
        .tab.active { background: var(--primary); color: white; }

        /* Search Bar */
        .search-bar { background: white; padding: 10px 16px; }
        .search-inner { background: #f0f2f5; border-radius: 20px; display: flex; align-items: center; padding: 0 12px; }
        .search-inner input { background: transparent; border: none; padding: 10px; width: 100%; outline: none; font-size: 14px; }

        /* Composer */
        .composer { background: white; margin: 16px; padding: 16px; border-radius: 12px; box-shadow: 0 1px 3px rgba(0,0,0,0.1); }
        .cat-selector { display: flex; gap: 5px; margin-bottom: 12px; overflow-x: auto; }
        .cat-chip { flex: 1; min-width: 70px; padding: 6px; border-radius: 6px; border: 1px solid var(--border); font-size: 11px; font-weight: bold; text-align: center; cursor: pointer; background: white; }
        .cat-chip.selected { background: var(--primary); color: white; border-color: var(--primary); }
        .cat-chip.selected[data-val="Urgent"] { background: var(--urgent); border-color: var(--urgent); }

        .composer-top { display: flex; gap: 12px; margin-bottom: 12px; }
        .composer-dp { width: 42px; height: 42px; border-radius: 50%; object-fit: cover; background: #eee; }
        .composer textarea { flex: 1; border: none; background: #f0f2f5; padding: 12px; border-radius: 18px; resize: none; font-size: 15px; outline: none; min-height: 40px; }
        
        /* Post Card */
        .post-card { background: white; margin-bottom: 12px; padding: 14px; border-radius: 12px; box-shadow: 0 1px 2px rgba(0,0,0,0.1); position: relative; }
        .badge { position: absolute; top: 14px; right: 40px; font-size: 10px; font-weight: bold; padding: 3px 8px; border-radius: 4px; text-transform: uppercase; }
        .badge-urgent { background: #ffebe9; color: var(--urgent); }
        .badge-sell { background: #e7f3ff; color: var(--buy-sell); }
        .badge-buy { background: #e7f3ff; color: #1098ad; }
        .badge-general { background: #f0f2f5; color: var(--secondary); }

        .delete-btn { position: absolute; top: 10px; right: 10px; border: none; background: #fff5f5; color: #ff4d4f; cursor: pointer; font-size: 16px; padding: 6px; border-radius: 50%; display: flex; align-items: center; justify-content: center; transition: 0.2s; border: 1px solid #ffccc7; }
        .delete-btn:hover { background: #ff4d4f; color: white; }

        .post-img { width: calc(100% + 28px); margin: 10px -14px; display: block; max-height: 450px; object-fit: cover; }
        
        .post-actions { display: flex; border-top: 1px solid var(--border); border-bottom: 1px solid var(--border); margin: 10px 0; padding: 5px 0; }
        .action-btn { flex: 1; background: none; border: none; padding: 8px; color: var(--secondary); font-weight: 600; cursor: pointer; display: flex; align-items: center; justify-content: center; gap: 6px; border-radius: 50px; }
        .action-btn.liked { color: var(--primary); }

        .comments-section { display: none; margin-top: 10px; background: #f9f9f9; padding: 10px; border-radius: 8px; }
        .comment-item { display: flex; gap: 8px; margin-bottom: 10px; font-size: 13.5px; }
        .comment-item img { width: 30px; height: 30px; border-radius: 50%; }
        .comment-bubble { background: #ebedf0; padding: 8px 12px; border-radius: 15px; flex: 1; }
        .comment-input-row { display: flex; gap: 8px; margin-top: 10px; }
        .comment-input-row input { flex: 1; border-radius: 20px; border: 1px solid var(--border); padding: 8px 15px; outline: none; }

        .contact-row { display: flex; gap: 10px; margin-top: 10px; }
        .c-btn { flex: 1; padding: 10px; border-radius: 8px; text-decoration: none; text-align: center; font-weight: bold; font-size: 14px; }
        .c-call { background: #e7f3ff; color: #1877f2; }
        .c-wa { background: #25d366; color: white; }

        /* Modals */
        .modal { position: fixed; inset: 0; background: white; z-index: 1000; display: none; padding: 20px; overflow-y: auto; }
        .primary-btn { background: var(--primary); color: white; border: none; padding: 14px; border-radius: 10px; width: 100%; font-weight: bold; cursor: pointer; transition: 0.2s; display: flex; align-items: center; justify-content: center; }
        .primary-btn:active { opacity: 0.8; }
        .primary-btn:disabled { background: #ccc; cursor: not-allowed; }
        .logout-btn { background: #f0f2f5; color: #d93025; border: 1px solid #d93025; padding: 12px; border-radius: 10px; width: 100%; font-weight: bold; cursor: pointer; margin-top: 20px; }

        .guest-warning { background: #fffbe6; border: 1px solid #ffe58f; padding: 12px; border-radius: 8px; margin: 16px; font-size: 14px; text-align: center; }
        .guest-warning button { background: var(--primary); color: white; border: none; padding: 5px 12px; border-radius: 4px; cursor: pointer; margin-left: 8px; }

        /* Profile Edit */
        .profile-edit-container { text-align: center; margin-bottom: 20px; position: relative; width: 100px; margin: 0 auto 20px; }
        .profile-edit-img { width: 100px; height: 100px; border-radius: 50%; object-fit: cover; border: 3px solid var(--primary); }
        .edit-overlay { position: absolute; bottom: 0; right: 0; background: var(--primary); color: white; border-radius: 50%; width: 30px; height: 30px; display: flex; align-items: center; justify-content: center; font-size: 16px; cursor: pointer; border: 2px solid white; }
    </style>
</head>
<body>

    <div id="loader"><div class="spinner"></div><p>Connecting to Neighborhood...</p></div>

    <!-- Combined Auth Screen -->
    <div id="auth-screen" class="auth-screen">
        <div class="auth-card">
            <h1 id="auth-title">🏡 Jeewan City </h1>
            <p id="auth-desc">Connect With Your Neighborhood</p>
            
            <div id="auth-inputs">
                <!-- Basic Login Fields -->
                <input type="email" id="auth-email" placeholder="Email Address">
                <input type="password" id="auth-pass" placeholder="Password">
                
                <!-- Registration Specific Fields (Shown only during Sign Up) -->
                <div id="reg-fields" style="display:none;">
                    <input type="text" id="reg-name" placeholder="Full Name">
                    <input type="text" id="reg-addr" placeholder="Address (House/Street)">
                    <input type="tel" id="reg-phone" placeholder="WhatsApp Number">
                </div>

                <button id="auth-btn" class="primary-btn" style="margin-top:10px;" onclick="handleAuth()">
                    <span id="auth-btn-text">Login</span>
                </button>
            </div>

            <div class="auth-toggle" id="auth-toggle-btn" onclick="toggleAuthMode()">
                Don't have an account? <b>Join Now</b>
            </div>
            
            <p style="margin-top:20px; color:var(--secondary); font-size:14px; cursor:pointer;" onclick="enterGuestMode()">Continue as Guest</p>
        </div>
    </div>

    <!-- Main Content -->
    <div id="main-shell">
        <div class="nav">
            <div class="logo" onclick="location.reload()">🏡 Jeewan City </div>
            <img id="my-nav-avatar" class="nav-profile-img" src="https://cdn-icons-png.flaticon.com/512/149/149071.png" onclick="handleProfileClick()">
        </div>
        <div class="search-bar">
    <div class="search-inner">
        <input type="text" id="search-input" placeholder="Search ..." oninput="runSearch()">
        <button class="search-btn" onclick="runSearch()">🔍</button>
    </div>
</div>

        <div id="guest-banner" class="guest-warning" style="display:none;">
            Viewing as <b>Guest</b>. You cannot post or like. 
            <button onclick="exitGuestMode()">Join Now</button>
        </div>

        <div class="filter-tabs">
            <button class="tab active" onclick="filterFeed('All', this)">All</button>
            <button class="tab" onclick="filterFeed('Urgent', this)">Urgent 🚨</button>
            <button class="tab" onclick="filterFeed('Sell', this)">Sell 🏷️</button>
            <button class="tab" onclick="filterFeed('Buy', this)">Buy 🤝</button>
            <button class="tab" onclick="filterFeed('General', this)">General</button>
        </div>

        <!-- Composer -->
        <div id="composer-ui" class="composer">
            <div class="cat-selector">
                <div class="cat-chip selected" data-val="General" onclick="selectCat(this)">General</div>
                <div class="cat-chip" data-val="Urgent" onclick="selectCat(this)">Urgent 🚨</div>
                <div class="cat-chip" data-val="Sell" onclick="selectCat(this)">Sell 🏷️</div>
                <div class="cat-chip" data-val="Buy" onclick="selectCat(this)">Buy 🤝</div>
            </div>
            <div class="composer-top">
                <img id="my-comp-avatar" class="composer-dp">
                <textarea id="post-text" placeholder="What's happening in the street?"></textarea>
            </div>
            <div id="preview-box" style="display:none; position:relative; margin-bottom:10px;">
                <img id="preview-img" style="width:100%; border-radius:10px;">
                <button onclick="clearImage()" style="position:absolute; top:5px; right:5px; background:rgba(0,0,0,0.5); color:white; border:none; border-radius:50%; width:24px; height:24px;">✕</button>
            </div>
<div style="display:flex; justify-content:space-between; align-items:center;">
    <label style="background: var(--primary); color: white; padding: 8px 15px; border-radius: 10px; font-weight: bold; font-size: 14px; cursor: pointer; display: flex; align-items: center; gap: 5px;">
        📷 Upload Photo <input type="file" hidden accept="image/*" onchange="handleImage(this)">
    </label>
                <button class="primary-btn" id="post-btn" style="width:auto; padding:8px 25px;" onclick="sendPost()">Post</button>
            </div>
        </div>

        <div id="feed-list" style="padding:0 16px 20px;"></div>
    </div>

    <!-- Settings Modal -->
    <div id="settings-modal" class="modal">
        <div style="max-width:400px; margin: 0 auto;">
            <div style="display:flex; justify-content:space-between; margin-bottom:20px;">
                <h2>Settings</h2>
                <button onclick="closeSettings()" style="border:none; background:none; font-size:24px;">✕</button>
            </div>

            <div class="profile-edit-container">
                <img id="edit-avatar-preview" class="profile-edit-img" src="https://cdn-icons-png.flaticon.com/512/149/149071.png">
                <label class="edit-overlay">
                    📷 <input type="file" hidden accept="image/*" onchange="handleProfileImage(this)">
                </label>
            </div>
            
            <label style="font-size:12px; font-weight:bold; color:var(--secondary);">FULL NAME</label>
            <input type="text" id="edit-name" style="width:100%; padding:12px; margin-bottom:10px; border:1px solid #ddd; border-radius:8px;">
            
            <label style="font-size:12px; font-weight:bold; color:var(--secondary);">ADDRESS</label>
            <input type="text" id="edit-addr" style="width:100%; padding:12px; margin-bottom:10px; border:1px solid #ddd; border-radius:8px;">
            
            <label style="font-size:12px; font-weight:bold; color:var(--secondary);">WHATSAPP</label>
            <input type="tel" id="edit-phone" style="width:100%; padding:12px; margin-bottom:15px; border:1px solid #ddd; border-radius:8px;">
            
            <button class="primary-btn" id="save-settings-btn" onclick="saveSettings()">Save Changes</button>
            <button class="logout-btn" onclick="handleLogout()">Logout Account</button>
        </div>
    </div>

    <script type="module">
        import { initializeApp } from "https://www.gstatic.com/firebasejs/11.6.1/firebase-app.js";
        import { getAuth, signInWithEmailAndPassword, createUserWithEmailAndPassword, onAuthStateChanged, signOut } from "https://www.gstatic.com/firebasejs/11.6.1/firebase-auth.js";
        import { getFirestore, collection, doc, setDoc, addDoc, onSnapshot, getDoc, updateDoc, arrayUnion, arrayRemove, deleteDoc } from "https://www.gstatic.com/firebasejs/11.6.1/firebase-firestore.js";

        const firebaseConfig = {
            apiKey: "AIzaSyAfe5oPP5mCuGYeHAERzW94jiTQcuwcpTE",
            authDomain: "city-99ef0.firebaseapp.com",
            projectId: "city-99ef0",
            storageBucket: "city-99ef0.firebasestorage.app",
            messagingSenderId: "487718648638",
            appId: "1:487718648638:web:f6c0bb7798de722bd44a6f"
        };

        const app = initializeApp(firebaseConfig);
        const auth = getAuth(app);
        const db = getFirestore(app);
        const appId = 'mohalla_v6_email';

        let user = null, myData = null, allPosts = [], selectedBase64 = "", profileBase64 = "", currentCategory = "General", currentFilter = "All", isGuest = false, isSignUp = false;

        function setBtnLoading(btnId, isLoading, defaultText) {
            const btn = document.getElementById(btnId);
            const span = btn.querySelector('span') || btn;
            if (isLoading) {
                btn.disabled = true;
                btn.innerHTML = `<span class="btn-spinner"></span> Processing...`;
            } else {
                btn.disabled = false;
                btn.innerHTML = defaultText;
            }
        }

        function toBase64(file, size, cb) {
            const reader = new FileReader();
            reader.onload = (e) => {
                const img = new Image();
                img.onload = () => {
                    const canvas = document.createElement('canvas');
                    let w = img.width, h = img.height;
                    if(w > size) { h *= size/w; w = size; }
                    canvas.width = w; canvas.height = h;
                    canvas.getContext('2d').drawImage(img, 0, 0, w, h);
                    cb(canvas.toDataURL('image/jpeg', 0.7));
                };
                img.src = e.target.result;
            };
            reader.readAsDataURL(file);
        }

        onAuthStateChanged(auth, async (u) => {
            if(u) {
                user = u;
                try {
                    const snap = await getDoc(doc(db, 'artifacts', appId, 'users', user.uid, 'profile', 'data'));
                    if(snap.exists()) {
                        myData = snap.data();
                        isGuest = false;
                        showApp();
                    } else {
                        if(!isGuest) {
                            document.getElementById('auth-screen').style.display = 'block';
                            document.getElementById('reg-fields').style.display = 'block';
                            document.getElementById('auth-btn-text').innerText = "Create Profile";
                            document.getElementById('loader').style.display = 'none';
                        }
                    }
                } catch(e) {
                    console.error("Firebase Error:", e);
                    enterGuestMode();
                }
            } else {
                document.getElementById('auth-screen').style.display = 'block';
                document.getElementById('loader').style.display = 'none';
            }
        });

        window.toggleAuthMode = () => {
            isSignUp = !isSignUp;
            document.getElementById('reg-fields').style.display = isSignUp ? 'block' : 'none';
            const btnText = isSignUp ? 'Join Neighborhood' : 'Login';
            document.getElementById('auth-btn-text').innerText = btnText;
            document.getElementById('auth-title').innerText = isSignUp ? 'Join Neighborhood' : '🏡 Mohalla Connect';
            document.getElementById('auth-toggle-btn').innerHTML = isSignUp 
                ? 'Already have an account? <b>Login</b>' 
                : "Don't have an account? <b>Join Now</b>";
        };

        window.handleAuth = async () => {
            const email = document.getElementById('auth-email').value;
            const pass = document.getElementById('auth-pass').value;
            const btnLabel = document.getElementById('auth-btn-text').innerText;

            if(!email || !pass) return alert("Email and Password are required.");

            setBtnLoading('auth-btn', true, btnLabel);

            try {
                if(isSignUp) {
                    const name = document.getElementById('reg-name').value;
                    const addr = document.getElementById('reg-addr').value;
                    const phone = document.getElementById('reg-phone').value;
                    
                    if(!name || !addr || !phone) {
                        setBtnLoading('auth-btn', false, btnLabel);
                        return alert("Please fill all profile details.");
                    }

                    const cred = await createUserWithEmailAndPassword(auth, email, pass);
                    user = cred.user;
                    
                    myData = { name, address: addr, phone, avatar: "https://cdn-icons-png.flaticon.com/512/149/149071.png", uid: user.uid };
                    await setDoc(doc(db, 'artifacts', appId, 'users', user.uid, 'profile', 'data'), myData);
                    showApp();
                } else {
                    if(user && btnLabel === "Create Profile") {
                        const name = document.getElementById('reg-name').value;
                        const addr = document.getElementById('reg-addr').value;
                        const phone = document.getElementById('reg-phone').value;
                        if(!name || !addr || !phone) {
                            setBtnLoading('auth-btn', false, btnLabel);
                            return alert("Please fill all details.");
                        }
                        
                        myData = { name, address: addr, phone, avatar: "https://cdn-icons-png.flaticon.com/512/149/149071.png", uid: user.uid };
                        await setDoc(doc(db, 'artifacts', appId, 'users', user.uid, 'profile', 'data'), myData);
                        showApp();
                    } else {
                        await signInWithEmailAndPassword(auth, email, pass);
                    }
                }
            } catch(e) {
                alert(e.message);
                setBtnLoading('auth-btn', false, btnLabel);
            }
        };

        window.enterGuestMode = () => {
            isGuest = true;
            myData = { name: "Guest", avatar: "https://cdn-icons-png.flaticon.com/512/149/149071.png", uid: "guest" };
            showApp();
        };

        window.exitGuestMode = () => {
            isGuest = false;
            document.getElementById('main-shell').style.display = 'none';
            document.getElementById('auth-screen').style.display = 'block';
        };

        function showApp() {
            setTimeout(() => {
                document.getElementById('loader').style.display = 'none';
                document.getElementById('main-shell').style.display = 'block';
                document.getElementById('auth-screen').style.display = 'none';
                
                if(isGuest) {
                    document.getElementById('composer-ui').style.display = 'none';
                    document.getElementById('guest-banner').style.display = 'block';
                } else {
                    document.getElementById('composer-ui').style.display = 'block';
                    document.getElementById('guest-banner').style.display = 'none';
                }

                updateProfileVisuals();
                listenToFeed();
            }, 300);
        }

        function updateProfileVisuals() {
            document.getElementById('my-nav-avatar').src = myData.avatar;
            if(!isGuest) {
                document.getElementById('my-comp-avatar').src = myData.avatar;
                document.getElementById('edit-avatar-preview').src = myData.avatar;
            }
        }

        window.handleProfileClick = () => {
            if(isGuest) exitGuestMode();
            else openSettings();
        };

        window.selectCat = (el) => {
            document.querySelectorAll('.cat-chip').forEach(c => c.classList.remove('selected'));
            el.classList.add('selected');
            currentCategory = el.dataset.val;
        };

        window.handleImage = (input) => {
            if(input.files[0]) toBase64(input.files[0], 800, (b64) => {
                selectedBase64 = b64;
                document.getElementById('preview-img').src = b64;
                document.getElementById('preview-box').style.display = 'block';
            });
        };

        window.handleProfileImage = (input) => {
            if(input.files[0]) toBase64(input.files[0], 300, (b64) => {
                profileBase64 = b64;
                document.getElementById('edit-avatar-preview').src = b64;
            });
        };

        window.clearImage = () => {
            selectedBase64 = "";
            document.getElementById('preview-box').style.display = 'none';
        };

        window.sendPost = async () => {
            if(isGuest) return;
            const txt = document.getElementById('post-text').value;
            if(!txt && !selectedBase64) return;
            
            setBtnLoading('post-btn', true, 'Post');

            try {
                await addDoc(collection(db, 'artifacts', appId, 'public', 'data', 'posts'), {
                    author: myData.name,
                    location: myData.address,
                    phone: myData.phone,
                    text: txt,
                    image: selectedBase64,
                    avatar: myData.avatar,
                    uid: user.uid,
                    category: currentCategory,
                    likes: [],
                    comments: [],
                    timestamp: Date.now()
                });
                document.getElementById('post-text').value = "";
                clearImage();
            } catch(e) { console.error(e); }
            setBtnLoading('post-btn', false, 'Post');
        };

        function listenToFeed() {
            onSnapshot(collection(db, 'artifacts', appId, 'public', 'data', 'posts'), (snap) => {
                allPosts = [];
                snap.forEach(d => allPosts.push({ id: d.id, ...d.data() }));
                allPosts.sort((a,b) => b.timestamp - a.timestamp);
                applyFilter();
            }, (err) => console.log(err));
        }

        window.filterFeed = (cat, btn) => {
            currentFilter = cat;
            document.querySelectorAll('.tab').forEach(t => t.classList.remove('active'));
            btn.classList.add('active');
            applyFilter();
        };

        function applyFilter() {
            let filtered = allPosts;
            if(currentFilter !== 'All') filtered = allPosts.filter(p => p.category === currentFilter);
            renderPosts(filtered);
        }

        function renderPosts(data) {
            const feed = document.getElementById('feed-list');
            feed.innerHTML = "";
            data.forEach(p => {
                const isLiked = !isGuest && p.likes?.includes(user?.uid);
                const isMine = !isGuest && (p.uid === user?.uid);
                const div = document.createElement('div');
                div.className = "post-card";
                div.innerHTML = `
                    <span class="badge badge-${p.category.toLowerCase()}">${p.category}</span>
                    ${isMine ? `<button class="delete-btn" onclick="deleteMyPost('${p.id}')">🗑️</button>` : ''}
                    <div style="display:flex;gap:10px;margin-bottom:10px;align-items:center;">
                        <img src="${p.avatar}" style="width:38px;height:38px;border-radius:50%;object-fit:cover;background:#eee;">
                        <div><b>${p.author}</b><br><small style="color:var(--primary);">📍 ${p.location}</small></div>
                    </div>
                    <div style="white-space:pre-wrap; margin:10px 0;">${p.text}</div>
                    ${p.image ? `<img src="${p.image}" class="post-img">` : ''}
                    
                    <div class="post-actions">
                        <button class="action-btn ${isLiked ? 'liked' : ''}" onclick="toggleLike('${p.id}', ${isLiked})">
                            ${isLiked ? '❤️' : '🤍'} Like (${p.likes?.length || 0})
                        </button>
                        <button class="action-btn" onclick="toggleComments('${p.id}')">
                            💬 Comment (${p.comments?.length || 0})
                        </button>
                    </div>

                    <div id="comments-${p.id}" class="comments-section">
                        <div id="list-${p.id}">
                            ${(p.comments || []).map(c => `
                                <div class="comment-item">
                                    <img src="${c.avatar}" style="background:#eee;">
                                    <div class="comment-bubble"><b>${c.name}</b><br>${c.text}</div>
                                </div>
                            `).join('')}
                        </div>
                        ${!isGuest ? `
                            <div class="comment-input-row">
                                <input type="text" id="input-${p.id}" placeholder="Write a comment...">
                                <button onclick="addComment('${p.id}')" style="background:var(--primary); color:white; border:none; border-radius:15px; padding:5px 12px; cursor:pointer;">Send</button>
                            </div>
                        ` : ''}
                    </div>

                    <div class="contact-row">
                        <a href="tel:${p.phone}" class="c-btn c-call">📞 Call</a>
                        <a href="https://wa.me/${p.phone.replace(/\+/g,'')}" target="_blank" class="c-btn c-wa">WhatsApp</a>
                    </div>
                `;
                feed.appendChild(div);
            });
        }

        window.deleteMyPost = async (id) => {
            if(confirm("Permanently delete this post?")) {
                await deleteDoc(doc(db, 'artifacts', appId, 'public', 'data', 'posts', id));
            }
        };

        window.toggleLike = async (id, liked) => {
            if(isGuest) return exitGuestMode();
            const ref = doc(db, 'artifacts', appId, 'public', 'data', 'posts', id);
            await updateDoc(ref, { likes: liked ? arrayRemove(user.uid) : arrayUnion(user.uid) });
        };

        window.toggleComments = (id) => {
            const el = document.getElementById(`comments-${id}`);
            el.style.display = el.style.display === 'block' ? 'none' : 'block';
        };

        window.addComment = async (id) => {
            if(isGuest) return;
            const input = document.getElementById(`input-${id}`);
            if(!input.value) return;
            const ref = doc(db, 'artifacts', appId, 'public', 'data', 'posts', id);
            await updateDoc(ref, {
                comments: arrayUnion({ uid: user.uid, name: myData.name, avatar: myData.avatar, text: input.value, time: Date.now() })
            });
            input.value = "";
        };

        window.runSearch = () => {
            const val = document.getElementById('search-input').value.toLowerCase();
            const filtered = allPosts.filter(p => p.author.toLowerCase().includes(val) || p.text.toLowerCase().includes(val) || p.location.toLowerCase().includes(val));
            renderPosts(filtered);
        };

        window.openSettings = () => {
            document.getElementById('settings-modal').style.display = 'block';
            document.getElementById('edit-name').value = myData.name;
            document.getElementById('edit-addr').value = myData.address;
            document.getElementById('edit-phone').value = myData.phone;
            profileBase64 = myData.avatar;
        };
        window.closeSettings = () => document.getElementById('settings-modal').style.display = 'none';

        window.saveSettings = async () => {
            setBtnLoading('save-settings-btn', true, 'Save Changes');
            const n = document.getElementById('edit-name').value;
            const a = document.getElementById('edit-addr').value;
            const p = document.getElementById('edit-phone').value;
            const updates = { name: n, address: a, phone: p, avatar: profileBase64 || myData.avatar };
            try {
                await updateDoc(doc(db, 'artifacts', appId, 'users', user.uid, 'profile', 'data'), updates);
                myData = { ...myData, ...updates };
                updateProfileVisuals();
                closeSettings();
            } catch(e) { alert("Error saving profile."); }
            setBtnLoading('save-settings-btn', false, 'Save Changes');
        };

        window.handleLogout = async () => {
            if(confirm("Log out?")) {
                await signOut(auth);
                location.reload();
            }
        };
    </script>
</body>
</html>
