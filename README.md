<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>TaskPay Ultra</title>
    <script src="https://telegram.org/js/telegram-web-app.js"></script>
    <script src="https://www.gstatic.com/firebasejs/8.10.0/firebase-app.js"></script>
    <script src="https://www.gstatic.com/firebasejs/8.10.0/firebase-database.js"></script>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@400;600;700&display=swap');
        
        :root { --primary: #4361ee; --secondary: #3f37c9; --bg: #f8f9fa; --text: #2b2d42; }
        body { font-family: 'Plus Jakarta Sans', sans-serif; background: var(--bg); color: var(--text); margin: 0; padding-bottom: 100px; -webkit-tap-highlight-color: transparent; }

        /* POPUP NOTICE MODAL */
        #popup-notice { display: none; position: fixed; top: 0; left: 0; width: 100%; height: 100%; background: rgba(0,0,0,0.8); z-index: 10000; align-items: center; justify-content: center; backdrop-filter: blur(5px); }
        .popup-box { background: white; width: 85%; max-width: 350px; padding: 25px; border-radius: 20px; text-align: center; animation: popUp 0.3s ease; position: relative; }
        .popup-close { margin-top: 15px; padding: 10px 30px; background: var(--primary); color: white; border: none; border-radius: 30px; font-weight: bold; cursor: pointer; }
        
        /* BAN & SUBMIT MODALS */
        #ban-screen { display: none; position: fixed; top:0; left:0; width:100%; height:100%; background: #fff; z-index: 9000; flex-direction: column; align-items: center; justify-content: center; text-align: center; }
        .modal-wrap { display: none; position: fixed; top: 0; left: 0; width: 100%; height: 100%; background: rgba(0,0,0,0.6); z-index: 2000; align-items: center; justify-content: center; }
        .modal-body { background: white; padding: 30px; border-radius: 25px; width: 80%; max-width: 350px; text-align: center; }

        /* HEADER */
        .header-bg { background: linear-gradient(135deg, #4361ee, #7209b7); padding: 25px 25px 90px 25px; border-radius: 0 0 35px 35px; color: white; position: relative; }
        .user-header { display: flex; align-items: center; justify-content: space-between; }
        .user-info { display: flex; align-items: center; gap: 15px; }
        .avatar { width: 50px; height: 50px; background: rgba(255,255,255,0.2); border-radius: 15px; display: flex; align-items: center; justify-content: center; font-size: 24px; border: 1px solid rgba(255,255,255,0.4); }
        
        /* NOTIFICATION BELL */
        .notif-btn { position: relative; cursor: pointer; font-size: 24px; }
        .notif-badge { position: absolute; top: -2px; right: -2px; width: 10px; height: 10px; background: #ff4757; border-radius: 50%; border: 2px solid #4361ee; display: none; }

        /* BALANCE & STATS */
        .balance-container { margin: -70px 20px 0; position: relative; z-index: 10; }
        .main-card { background: white; border-radius: 25px; padding: 20px; box-shadow: 0 15px 35px rgba(0,0,0,0.1); text-align: center; }
        .balance-val { font-size: 36px; font-weight: 800; color: var(--primary); display: block; margin: 5px 0; }
        .grid-stats { display: grid; grid-template-columns: 1fr 1fr 1fr; gap: 10px; margin-top: 15px; border-top: 1px solid #eee; padding-top: 15px; }
        .g-val { font-weight: 700; font-size: 14px; } .g-lbl { font-size: 10px; color: #888; }

        /* SECTIONS */
        .section-header { padding: 0 25px; margin-top: 25px; margin-bottom: 15px; font-weight: 700; font-size: 18px; display: flex; align-items: center; gap: 10px; }
        .task-card { background: white; margin: 0 20px 15px; padding: 18px; border-radius: 20px; box-shadow: 0 5px 20px rgba(0,0,0,0.03); display: flex; justify-content: space-between; align-items: center; border: 1px solid #f0f0f0; }
        .btn-grad { background: linear-gradient(135deg, var(--primary), var(--secondary)); color: white; border: none; padding: 10px 20px; border-radius: 30px; font-weight: 600; font-size: 12px; cursor: pointer; }
        
        /* INBOX */
        .msg-item { background: white; margin: 0 20px 12px; padding: 15px; border-radius: 15px; border-left: 5px solid #eee; box-shadow: 0 2px 10px rgba(0,0,0,0.02); }
        .btn-submit { background: #00b894; color: white; padding: 8px; width: 48%; border: none; border-radius: 8px; cursor: pointer; font-weight: bold; font-size: 11px; }
        .btn-cancel { background: #ff7675; color: white; padding: 8px; width: 48%; border: none; border-radius: 8px; cursor: pointer; font-weight: bold; font-size: 11px; }

        /* NAV */
        .nav-dock { position: fixed; bottom: 20px; left: 20px; right: 20px; background: #2b2d42; border-radius: 25px; padding: 15px 30px; display: flex; justify-content: space-between; box-shadow: 0 10px 30px rgba(43, 45, 66, 0.4); z-index: 100; }
        .nav-icon { color: rgba(255,255,255,0.5); font-size: 20px; cursor: pointer; transition: 0.3s; }
        .nav-icon.active { color: white; transform: scale(1.2); }

        .page { display: none; animation: slideUp 0.4s ease; } .page.active { display: block; }
        @keyframes slideUp { from { opacity: 0; transform: translateY(20px); } to { opacity: 1; transform: translateY(0); } }
        @keyframes popUp { from { transform: scale(0.8); opacity: 0; } to { transform: scale(1); opacity: 1; } }

        input, select { width: 100%; padding: 15px; background: #f8f9fa; border: 1px solid #eee; border-radius: 15px; margin-bottom: 10px; outline: none; box-sizing: border-box; }
        .banner-img { width: calc(100% - 40px); margin: 15px 20px 0; border-radius: 15px; display: none; }
    </style>
</head>
<body>

    <!-- POPUP NOTICE (First Screen) -->
    <div id="popup-notice">
        <div class="popup-box">
            <i class="fas fa-bullhorn" style="font-size: 40px; color: #f39c12; margin-bottom: 15px;"></i>
            <h3 style="margin: 0; color: #333;">Important Notice</h3>
            <p id="popup-text" style="color: #666; font-size: 13px; margin: 10px 0;">Loading...</p>
            <button class="popup-close" onclick="closePopup()">Got it!</button>
        </div>
    </div>

    <!-- BAN SCREEN -->
    <div id="ban-screen">
        <i class="fas fa-ban" style="font-size: 60px; color: #ff6b6b; margin-bottom: 20px;"></i>
        <h3>Access Restricted</h3>
    </div>

    <!-- SUBMIT MODAL -->
    <div id="submit-modal" class="modal-wrap">
        <div class="modal-body">
            <h3>📧 Submit Data</h3>
            <input type="email" id="sub-email" placeholder="Gmail Address">
            <input type="text" id="sub-pass" placeholder="Password">
            <div style="display:flex; gap:10px; margin-top:10px;">
                <button onclick="closeModal()" style="flex:1; padding:12px; border:none; border-radius:12px; background:#eee;">Cancel</button>
                <button onclick="confirmSubmitData()" style="flex:1; padding:12px; border:none; border-radius:12px; background:var(--primary); color:white;">Submit</button>
            </div>
        </div>
    </div>

    <!-- HEADER -->
    <div class="header-bg">
        <div class="user-header">
            <div class="user-info">
                <div class="avatar"><i class="fas fa-user"></i></div>
                <div>
                    <div style="font-size: 18px; font-weight: 700;" id="u_name">Loading...</div>
                    <div style="font-size: 12px; opacity: 0.8;">ID: <span id="u_id">...</span></div>
                </div>
            </div>
            <!-- Notification Icon with Badge -->
            <div class="notif-btn" onclick="nav('p-inbox', document.querySelectorAll('.nav-icon')[2])">
                <i class="fas fa-bell"></i>
                <div class="notif-badge" id="n-badge"></div>
            </div>
        </div>
    </div>

    <!-- STATS -->
    <div class="balance-container">
        <div class="main-card">
            <div style="font-size:12px; color:#888;">TOTAL BALANCE</div>
            <span class="balance-val">৳<span id="bal_main">0.00</span></span>
            <div class="grid-stats">
                <div class="g-item"><div class="g-val">৳<span id="bal_today">0</span></div><div class="g-lbl">Today</div></div>
                <div class="g-item"><div class="g-val" id="task_today">0</div><div class="g-lbl">Tasks</div></div>
                <div class="g-item"><div class="g-val">৳<span id="wd_total">0</span></div><div class="g-lbl">Withdrawn</div></div>
            </div>
        </div>
    </div>

    <img id="banner-area" class="banner-img" src="">

    <!-- PAGES -->
    <div id="p-home" class="page active">
        <div class="section-header"><i class="fas fa-fire" style="color:var(--primary)"></i> Tasks</div>
        <div id="task-list"></div>
    </div>

    <div id="p-leader" class="page">
        <div class="section-header"><i class="fas fa-crown" style="color:gold"></i> Top Earners</div>
        <div id="leaderboard-list"></div>
    </div>

    <div id="p-inbox" class="page">
        <div class="section-header"><i class="fas fa-history" style="color:#f39c12"></i> Inbox & History</div>
        <div id="inbox-list"></div>
    </div>

    <div id="p-profile" class="page">
        <div class="section-header"><i class="fas fa-wallet" style="color:#2ecc71"></i> Withdraw</div>
        <div style="background: white; margin: 0 20px; padding: 25px; border-radius: 25px;">
            <p style="text-align:center; color:#e67e22; font-size:12px; margin-bottom:15px;">Min Withdraw: <b id="min-wd-txt">100</b> Tk</p>
            <select id="wd_method"><option>Bkash</option><option>Nagad</option><option>Rocket</option><option>Upay</option></select>
            <input type="number" id="wd_number" placeholder="Mobile Number">
            <input type="number" id="wd_amount" placeholder="Amount">
            <button onclick="reqWd()" style="width:100%; background:var(--primary); color:white; padding:15px; border:none; border-radius:15px; font-weight:bold;">Withdraw Now</button>
        </div>
    </div>

    <div class="nav-dock">
        <i class="fas fa-home nav-icon active" onclick="nav('p-home', this)"></i>
        <i class="fas fa-chart-bar nav-icon" onclick="nav('p-leader', this)"></i>
        <i class="fas fa-bell nav-icon" onclick="nav('p-inbox', this)"></i>
        <i class="fas fa-wallet nav-icon" onclick="nav('p-profile', this)"></i>
    </div>

    <script>
        // CONFIG
        const firebaseConfig = {
            apiKey: "AIzaSyC3uf_5gEMK_JjsmFW01ofRE-ESaXhio9I",
            authDomain: "taskbot-b6fe2.firebaseapp.com",
            databaseURL: "https://taskbot-b6fe2-default-rtdb.firebaseio.com",
            projectId: "taskbot-b6fe2",
            storageBucket: "taskbot-b6fe2.firebasestorage.app",
            messagingSenderId: "187560047900",
            appId: "1:187560047900:web:f458a4f99e2e7d967f36d9",
            measurementId: "G-9GPFKEQJ53"
        };
        if (!firebase.apps.length) firebase.initializeApp(firebaseConfig);
        const db = firebase.database();

        const tg = window.Telegram.WebApp;
        try { tg.expand(); } catch(e){}
        let user = tg.initDataUnsafe.user || { id: 112233, first_name: "User" };

        document.getElementById('u_name').innerText = user.first_name;
        document.getElementById('u_id').innerText = user.id;

        let minWithdraw = 100;
        let activeTaskData = null;

        function initApp() {
            // User & Stats
            const userRef = db.ref('users/' + user.id);
            userRef.once('value', s => { if(!s.exists()) userRef.set({ name: user.first_name, balance: 0, total_withdraw: 0, banned: false }); });

            userRef.on('value', snap => {
                const u = snap.val(); if(!u) return;
                if(u.banned) document.getElementById('ban-screen').style.display = 'flex';
                
                const todayStr = new Date().toDateString();
                if(u.last_active !== todayStr) userRef.update({ last_active: todayStr, today_income: 0, today_tasks: 0 });

                document.getElementById('bal_main').innerText = (u.balance || 0).toFixed(2);
                document.getElementById('bal_today').innerText = (u.today_income || 0).toFixed(2);
                document.getElementById('task_today').innerText = (u.today_tasks || 0);
                document.getElementById('wd_total').innerText = (u.total_withdraw || 0).toFixed(2);
            });

            // Settings & Popup
            db.ref('app_settings').on('value', snap => {
                const s = snap.val();
                if(s) {
                    if(s.notice) { 
                        document.getElementById('popup-notice').style.display = 'flex'; // Show Popup on Load
                        document.getElementById('popup-text').innerText = s.notice;
                    }
                    if(s.banner) { document.getElementById('banner-area').style.display = 'block'; document.getElementById('banner-area').src = s.banner; }
                    if(s.min_withdraw) minWithdraw = parseInt(s.min_withdraw);
                }
            });

            loadTasks();
            loadLeaderboard();
            loadInbox();
        }

        function closePopup() { document.getElementById('popup-notice').style.display = 'none'; }

        // TASKS
        function loadTasks() {
            db.ref('tasks').on('value', snap => {
                const list = document.getElementById('task-list');
                list.innerHTML = '';
                if(!snap.val()) { list.innerHTML = '<div style="text-align:center;color:#ccc;padding:20px;">No Tasks</div>'; return; }
                const tasks = snap.val();
                Object.keys(tasks).forEach(k => {
                    const t = tasks[k];
                    const btnTxt = t.type === 'submit' ? 'Sell Now' : 'Start';
                    list.innerHTML += `
                    <div class="task-card">
                        <div>
                            <div style="font-weight:700;">${t.title}</div>
                            <div style="font-size:11px; color:#888;">${t.desc}</div>
                        </div>
                        <div style="text-align:right;">
                            <div style="color:var(--primary); font-weight:800; font-size:15px; margin-bottom:5px;">৳${t.price}</div>
                            <button class="btn-grad" onclick="initTask('${k}', '${t.title}', ${t.price}, '${t.type}')">${btnTxt}</button>
                        </div>
                    </div>`;
                });
            });
        }

        function initTask(id, title, price, type) {
            if(type === 'submit') {
                activeTaskData = { title, price };
                document.getElementById('submit-modal').style.display = 'flex';
            } else {
                if(confirm(`Apply for ${title}?`)) sendReq(title, price, 'normal', null);
            }
        }

        function confirmSubmitData() {
            const email = document.getElementById('sub-email').value;
            const pass = document.getElementById('sub-pass').value;
            if(!email || !pass) return alert("Fill data");
            sendReq(activeTaskData.title, activeTaskData.price, 'submit', `E: ${email} | P: ${pass}`);
            closeModal();
        }

        function sendReq(title, price, type, data) {
            const reqId = Date.now();
            const p = { reqId, userId: user.id, userName: user.first_name, type: 'Task', taskName: title, price, status: type==='submit'?'Reviewing':'Pending', taskType: type, replyData: data, timestamp: new Date().toLocaleString() };
            db.ref('admin_requests/'+reqId).set(p);
            db.ref('users/'+user.id+'/inbox/'+reqId).set(p);
            alert("Done!");
        }

        function closeModal() { document.getElementById('submit-modal').style.display = 'none'; }

        function submitDone(rid) { if(confirm("Done?")) { db.ref('admin_requests/'+rid).update({status:'Reviewing'}); db.ref('users/'+user.id+'/inbox/'+rid).update({status:'Reviewing'}); } }
        function cancelTask(rid) { if(confirm("Cancel?")) { db.ref('admin_requests/'+rid).update({status:'Cancelled'}); db.ref('users/'+user.id+'/inbox/'+rid).update({status:'Cancelled'}); } }

        // INBOX & BADGE
        function loadInbox() {
            db.ref('users/' + user.id + '/inbox').limitToLast(30).on('value', snap => {
                const list = document.getElementById('inbox-list');
                const badge = document.getElementById('n-badge');
                list.innerHTML = '';
                
                if(!snap.val()) { list.innerHTML = '<p style="text-align:center;color:#ccc">Empty</p>'; badge.style.display='none'; return; }
                const msgs = Object.values(snap.val()).reverse();
                
                // Badge Logic: Show if most recent message is Notification
                if(msgs.length > 0 && msgs[0].type === 'Notification') badge.style.display = 'block';
                else badge.style.display = 'none';

                msgs.forEach(m => {
                    if(m.type === 'Notification') {
                        list.innerHTML += `<div class="msg-item" style="border-left-color:#ff4757;"><div style="font-size:10px;">📢 Notice • ${m.timestamp}</div><div style="margin-top:5px;">${m.message}</div></div>`;
                        return;
                    }
                    
                    let color='#ccc', action='';
                    if(m.type==='Task') {
                        if(m.status==='InProgress') {
                            color='#3498db';
                            action = `<div style="margin-top:10px; border-top:1px dashed #eee; padding-top:10px;"><div style="background:#f8f9fa; padding:8px; border-radius:5px; font-family:monospace; margin-bottom:5px;">${m.replyData}</div><div style="display:flex; gap:5px;"><button class="btn-submit" onclick="submitDone('${m.reqId}')">Done</button><button class="btn-cancel" onclick="cancelTask('${m.reqId}')">Cancel</button></div></div>`;
                        } else if(m.status==='Completed') color='#00b894'; else if(m.status==='Reviewing') color='#9b59b6'; else color='#ff7675';
                    } else {
                        if(m.status==='Paid') color='#00b894'; else if(m.status==='Pending') color='#f39c12'; else color='#ff7675';
                    }

                    list.innerHTML += `<div class="msg-item" style="border-left-color:${color};"><div style="display:flex; justify-content:space-between; font-size:10px; color:#888;"><span>${m.timestamp}</span><span style="font-weight:bold; color:${color}">${m.status}</span></div><div style="font-weight:700; margin-top:5px;">${m.type==='Withdraw'?m.method:m.taskName}</div><div style="font-size:12px;">Amount: ৳${m.price||m.amount}</div>${action}</div>`;
                });
            });
        }

        function loadLeaderboard() {
            db.ref('users').orderByChild('balance').limitToLast(10).on('value', snap => {
                const list = document.getElementById('leaderboard-list');
                list.innerHTML = '';
                let arr=[]; snap.forEach(c=>arr.push(c.val())); arr.reverse();
                arr.forEach((u,i)=> list.innerHTML+=`<div style="background:white; margin:0 20px 10px; padding:15px; border-radius:15px; display:flex; justify-content:space-between;"><span>#${i+1} ${u.name}</span><span style="font-weight:bold; color:var(--primary);">৳${(u.balance||0).toFixed(0)}</span></div>`);
            });
        }

        function reqWd() {
            const method=document.getElementById('wd_method').value, number=document.getElementById('wd_number').value, amount=parseFloat(document.getElementById('wd_amount').value);
            // BANKING LOGIC: Check & Deduct Immediately
            db.ref('users/'+user.id).transaction(u => {
                if(u && u.balance >= amount && amount >= minWithdraw && number.length > 9) {
                    u.balance -= amount; // Deduct Balance Here
                    u.temp_wd_req = { method, number, amount, ts: Date.now() }; // Flag for success
                    return u;
                }
                return; // Abort if insufficient funds
            }, (error, committed, snapshot) => {
                if(committed) {
                    const reqId = Date.now();
                    const data = { reqId, userId: user.id, userName: user.first_name, type: 'Withdraw', method, number, amount, status: 'Pending', timestamp: new Date().toLocaleString() };
                    db.ref('admin_requests/'+reqId).set(data);
                    db.ref('users/'+user.id+'/inbox/'+reqId).set(data);
                    alert("Request Sent! Balance Deducted.");
                } else {
                    alert("Failed! Check Balance or Min Amount.");
                }
            });
        }

        function nav(page, el) {
            document.querySelectorAll('.page').forEach(p=>p.classList.remove('active'));
            document.getElementById(page).classList.add('active');
            document.querySelectorAll('.nav-icon').forEach(b=>b.classList.remove('active'));
            el.classList.add('active');
        }

        initApp();
    </script>
</body>
</html>
