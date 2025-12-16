<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>TaskPay Premium</title> <!-- Clean Title -->
    <script src="https://telegram.org/js/telegram-web-app.js"></script>
    <script src="https://www.gstatic.com/firebasejs/8.10.0/firebase-app.js"></script>
    <script src="https://www.gstatic.com/firebasejs/8.10.0/firebase-database.js"></script>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@400;500;600;700&display=swap');
        
        :root {
            --primary: #4361ee;
            --secondary: #3f37c9;
            --accent: #f72585;
            --bg: #f8f9fa;
            --surface: #ffffff;
            --text-main: #2b2d42;
            --text-sub: #8d99ae;
        }

        body {
            font-family: 'Plus Jakarta Sans', sans-serif;
            background: var(--bg);
            color: var(--text-main);
            margin: 0;
            padding-bottom: 100px;
            -webkit-tap-highlight-color: transparent;
            overflow-x: hidden;
        }

        /* Loading Screen */
        #loader {
            position: fixed; top: 0; left: 0; width: 100%; height: 100%;
            background: white; z-index: 99999; display: flex;
            align-items: center; justify-content: center; flex-direction: column;
        }
        .spinner { width: 40px; height: 40px; border: 4px solid #f3f3f3; border-top: 4px solid var(--primary); border-radius: 50%; animation: spin 1s linear infinite; }
        @keyframes spin { 0% { transform: rotate(0deg); } 100% { transform: rotate(360deg); } }

        /* Ban Screen */
        #ban-screen { display: none; position: fixed; top:0; left:0; width:100%; height:100%; background: #fff; z-index: 9000; flex-direction: column; align-items: center; justify-content: center; text-align: center; }

        /* HEADER DESIGN */
        .header-bg {
            background: linear-gradient(135deg, #4361ee, #7209b7);
            padding: 25px 25px 90px 25px;
            border-bottom-left-radius: 35px;
            border-bottom-right-radius: 35px;
            color: white;
            box-shadow: 0 10px 30px rgba(67, 97, 238, 0.3);
            position: relative;
        }

        .user-header { display: flex; align-items: center; justify-content: space-between; }
        .user-info { display: flex; align-items: center; gap: 15px; }
        .avatar { 
            width: 50px; height: 50px; 
            background: rgba(255,255,255,0.2); 
            border-radius: 15px; display: flex; align-items: center; justify-content: center; 
            font-size: 24px; border: 1px solid rgba(255,255,255,0.4);
            backdrop-filter: blur(5px);
        }

        /* FLOATING BALANCE CARD */
        .balance-container {
            margin: -70px 20px 0;
            position: relative;
            z-index: 10;
        }
        .main-card {
            background: rgba(255, 255, 255, 0.9);
            backdrop-filter: blur(20px);
            border-radius: 25px;
            padding: 20px;
            box-shadow: 0 15px 35px rgba(0,0,0,0.1);
            border: 1px solid white;
            text-align: center;
        }
        .balance-title { font-size: 12px; color: var(--text-sub); text-transform: uppercase; letter-spacing: 1px; }
        .balance-val { font-size: 36px; font-weight: 800; color: var(--primary); margin: 5px 0; }
        
        .grid-stats {
            display: grid; grid-template-columns: 1fr 1fr 1fr; gap: 10px; margin-top: 15px;
            border-top: 1px solid #eee; padding-top: 15px;
        }
        .g-item { text-align: center; }
        .g-val { font-weight: 700; font-size: 14px; color: var(--text-main); }
        .g-lbl { font-size: 10px; color: var(--text-sub); }

        /* TABS & PAGES */
        .page { display: none; animation: slideUp 0.4s cubic-bezier(0.165, 0.84, 0.44, 1); }
        .page.active { display: block; }
        @keyframes slideUp { from { opacity: 0; transform: translateY(20px); } to { opacity: 1; transform: translateY(0); } }

        .section-header { padding: 0 25px; margin-top: 25px; margin-bottom: 15px; font-weight: 700; font-size: 18px; display: flex; align-items: center; gap: 10px; }

        /* TASK CARD */
        .task-card {
            background: white; margin: 0 20px 15px; padding: 18px;
            border-radius: 20px;
            box-shadow: 0 5px 20px rgba(0,0,0,0.03);
            display: flex; justify-content: space-between; align-items: center;
            border: 1px solid #f0f0f0;
            transition: transform 0.2s;
        }
        .task-card:active { transform: scale(0.98); }
        .task-info h4 { margin: 0; font-size: 15px; color: var(--text-main); }
        .task-info p { margin: 4px 0 0; font-size: 11px; color: var(--text-sub); }
        
        .btn-grad {
            background: linear-gradient(135deg, var(--primary), var(--secondary));
            color: white; border: none; padding: 10px 20px; border-radius: 30px;
            font-weight: 600; font-size: 12px; cursor: pointer;
            box-shadow: 0 4px 15px rgba(67, 97, 238, 0.3);
        }

        /* INBOX & LISTS */
        .msg-item {
            background: white; margin: 0 20px 12px; padding: 15px;
            border-radius: 15px; border-left: 5px solid #eee;
            box-shadow: 0 2px 10px rgba(0,0,0,0.02);
        }
        .status-badge { padding: 4px 10px; border-radius: 20px; font-size: 10px; font-weight: bold; float: right; }

        /* BOTTOM NAV */
        .nav-dock {
            position: fixed; bottom: 20px; left: 20px; right: 20px;
            background: #2b2d42;
            border-radius: 25px;
            padding: 15px 30px;
            display: flex; justify-content: space-between;
            box-shadow: 0 10px 30px rgba(43, 45, 66, 0.4);
            z-index: 100;
        }
        .nav-icon { color: rgba(255,255,255,0.5); font-size: 20px; cursor: pointer; transition: 0.3s; }
        .nav-icon.active { color: white; transform: scale(1.2); }

        /* MODAL */
        .modal-wrap { display: none; position: fixed; top: 0; left: 0; width: 100%; height: 100%; background: rgba(0,0,0,0.6); z-index: 2000; align-items: center; justify-content: center; backdrop-filter: blur(5px); }
        .modal-body { background: white; padding: 30px; border-radius: 25px; width: 80%; max-width: 350px; text-align: center; box-shadow: 0 20px 40px rgba(0,0,0,0.2); }
        
        input, select { width: 100%; padding: 15px; background: #f8f9fa; border: 1px solid #eee; border-radius: 15px; margin-bottom: 10px; outline: none; box-sizing: border-box; font-family: inherit; }
        
        .notice-bar { margin: 20px 20px 0; background: #fff3cd; color: #856404; padding: 10px; border-radius: 12px; font-size: 12px; display: flex; align-items: center; }
        .banner-img { width: calc(100% - 40px); margin: 15px 20px 0; border-radius: 15px; display: none; }
    </style>
</head>
<body>

    <!-- Loader -->
    <div id="loader"><div class="spinner"></div><p style="margin-top:10px; font-size:12px; color:#666;">Loading App...</p></div>

    <!-- Ban Screen -->
    <div id="ban-screen">
        <i class="fas fa-ban" style="font-size: 60px; color: var(--accent); margin-bottom: 20px;"></i>
        <h3>Access Restricted</h3>
        <p style="color: #666; font-size: 13px;">Please contact support for help.</p>
    </div>

    <!-- SUBMIT MODAL -->
    <div id="submit-modal" class="modal-wrap">
        <div class="modal-body">
            <h3>📧 Submit Details</h3>
            <p style="font-size:12px; color:#888; margin-bottom:20px;">Provide login info for the task.</p>
            <input type="email" id="sub-email" placeholder="Gmail Address">
            <input type="text" id="sub-pass" placeholder="Password">
            <div style="display:flex; gap:10px; margin-top:10px;">
                <button onclick="closeModal()" style="flex:1; padding:12px; border:none; border-radius:12px; background:#eee; font-weight:600;">Cancel</button>
                <button onclick="confirmSubmitData()" style="flex:1; padding:12px; border:none; border-radius:12px; background:var(--primary); color:white; font-weight:600;">Submit</button>
            </div>
        </div>
    </div>

    <!-- HEADER -->
    <div class="header-bg">
        <div class="user-header">
            <div class="user-info">
                <div class="avatar"><i class="fas fa-user"></i></div>
                <div>
                    <div style="font-size: 18px; font-weight: 700;" id="u_name">Guest</div>
                    <div style="font-size: 12px; opacity: 0.8;">ID: <span id="u_id">...</span></div>
                </div>
            </div>
            <i class="fas fa-bell" style="font-size: 20px; opacity: 0.8;"></i>
        </div>
    </div>

    <!-- BALANCE CARD -->
    <div class="balance-container">
        <div class="main-card">
            <div class="balance-title">Total Balance</div>
            <div class="balance-val">৳<span id="bal_main">0.00</span></div>
            
            <div class="grid-stats">
                <div class="g-item">
                    <div class="g-val">৳<span id="bal_today">0</span></div>
                    <div class="g-lbl">Today</div>
                </div>
                <div class="g-item">
                    <div class="g-val" id="task_today">0</div>
                    <div class="g-lbl">Tasks</div>
                </div>
                <div class="g-item">
                    <div class="g-val">৳<span id="wd_total">0</span></div>
                    <div class="g-lbl">Cashout</div>
                </div>
            </div>
        </div>
    </div>

    <!-- DYNAMIC NOTICE & BANNER -->
    <div id="notice-area" class="notice-bar" style="display:none;">
        <i class="fas fa-bullhorn" style="margin-right:10px;"></i>
        <marquee id="notice-text">Welcome!</marquee>
    </div>
    <img id="banner-area" class="banner-img" src="">

    <!-- PAGE 1: TASKS -->
    <div id="p-home" class="page active">
        <div class="section-header"><i class="fas fa-layer-group" style="color:var(--primary)"></i> Tasks</div>
        <div id="task-list"></div>
    </div>

    <!-- PAGE 2: LEADERBOARD -->
    <div id="p-leader" class="page">
        <div class="section-header"><i class="fas fa-crown" style="color:gold"></i> Top Leaders</div>
        <div id="leaderboard-list"></div>
    </div>

    <!-- PAGE 3: HISTORY -->
    <div id="p-inbox" class="page">
        <div class="section-header"><i class="fas fa-history" style="color:var(--accent)"></i> Activity</div>
        <div id="inbox-list"></div>
    </div>

    <!-- PAGE 4: WALLET -->
    <div id="p-profile" class="page">
        <div class="section-header"><i class="fas fa-credit-card" style="color:var(--secondary)"></i> Withdraw</div>
        <div style="background: white; margin: 0 20px; padding: 25px; border-radius: 25px; box-shadow: 0 5px 20px rgba(0,0,0,0.05);">
            <div style="text-align:center; margin-bottom:20px;">
                <span style="font-size:12px; color:#888;">Available to withdraw</span>
                <h2 style="margin:5px 0; color:var(--primary);">৳<span id="wd_avail">0.00</span></h2>
            </div>
            
            <select id="wd_method">
                <option value="Bkash">bKash</option>
                <option value="Nagad">Nagad</option>
                <option value="Rocket">Rocket</option>
                <option value="Upay">Upay</option>
            </select>
            <input type="number" id="wd_number" placeholder="017xxxxxxxx">
            <input type="number" id="wd_amount" placeholder="Amount (Min 100)">
            <button onclick="reqWd()" style="width:100%; background:var(--primary); color:white; padding:15px; border:none; border-radius:15px; font-weight:bold; font-size:16px; margin-top:10px;">Request Payment</button>
        </div>
    </div>

    <!-- BOTTOM NAV -->
    <div class="nav-dock">
        <i class="fas fa-home nav-icon active" onclick="nav('p-home', this)"></i>
        <i class="fas fa-chart-bar nav-icon" onclick="nav('p-leader', this)"></i>
        <i class="fas fa-clock nav-icon" onclick="nav('p-inbox', this)"></i>
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
        let user = tg.initDataUnsafe.user || { id: 112233, first_name: "TestUser" };

        document.getElementById('u_name').innerText = user.first_name;
        document.getElementById('u_id').innerText = user.id;

        let minWithdraw = 100;
        let activeTaskData = null;

        function initApp() {
            const userRef = db.ref('users/' + user.id);
            userRef.once('value', s => {
                if(!s.exists()) userRef.set({ name: user.first_name, balance: 0, total_withdraw: 0, banned: false });
                document.getElementById('loader').style.display = 'none'; // Hide loader
            });

            userRef.on('value', snap => {
                const u = snap.val(); if(!u) return;
                if(u.banned) document.getElementById('ban-screen').style.display = 'flex';
                
                const todayStr = new Date().toDateString();
                if(u.last_active !== todayStr) userRef.update({ last_active: todayStr, today_income: 0, today_tasks: 0 });

                document.getElementById('bal_main').innerText = (u.balance || 0).toFixed(2);
                document.getElementById('wd_avail').innerText = (u.balance || 0).toFixed(2);
                document.getElementById('bal_today').innerText = (u.today_income || 0).toFixed(2);
                document.getElementById('task_today').innerText = (u.today_tasks || 0);
                document.getElementById('wd_total').innerText = (u.total_withdraw || 0).toFixed(2);
            });

            db.ref('app_settings').on('value', snap => {
                const s = snap.val();
                if(s) {
                    if(s.notice) { document.getElementById('notice-area').style.display = 'flex'; document.getElementById('notice-text').innerText = s.notice; }
                    if(s.banner) { document.getElementById('banner-area').style.display = 'block'; document.getElementById('banner-area').src = s.banner; }
                    if(s.min_withdraw) minWithdraw = parseInt(s.min_withdraw);
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
                if(!snap.val()) { list.innerHTML = '<div style="text-align:center;color:#ccc;padding:30px;">No active tasks</div>'; return; }
                const tasks = snap.val();
                Object.keys(tasks).forEach(k => {
                    const t = tasks[k];
                    const btnText = t.type === 'submit' ? 'Sell Account' : 'Start Task';
                    const badge = t.type === 'submit' ? '<i class="fas fa-upload"></i> Input Data' : '<i class="fas fa-download"></i> Get Data';
                    
                    list.innerHTML += `
                    <div class="task-card">
                        <div class="task-info">
                            <h4>${t.title}</h4>
                            <p>${t.desc}</p>
                            <span style="font-size:10px; background:#f0f0f0; padding:2px 8px; border-radius:10px; color:#666;">${badge}</span>
                        </div>
                        <div style="text-align:right;">
                            <div style="color:var(--primary); font-weight:800; font-size:16px; margin-bottom:8px;">৳${t.price}</div>
                            <button class="btn-grad" onclick="initTask('${k}', '${t.title}', ${t.price}, '${t.type}')">${btnText}</button>
                        </div>
                    </div>`;
                });
            });
        }

        function initTask(id, title, price, type) {
            if(type === 'submit') {
                activeTaskData = { title, price };
                document.getElementById('submit-modal').style.display = 'flex';
                document.getElementById('sub-email').value = '';
                document.getElementById('sub-pass').value = '';
            } else {
                if(confirm(`Apply for ${title}?`)) {
                    sendReq(title, price, 'normal', null);
                }
            }
        }

        function confirmSubmitData() {
            const email = document.getElementById('sub-email').value;
            const pass = document.getElementById('sub-pass').value;
            if(!email || !pass) return alert("Fill all fields");
            sendReq(activeTaskData.title, activeTaskData.price, 'submit', `Email: ${email} | Pass: ${pass}`);
            closeModal();
        }

        function sendReq(title, price, type, data) {
            const reqId = Date.now();
            const payload = { 
                reqId, userId: user.id, userName: user.first_name, 
                type: 'Task', taskName: title, price: price, 
                status: type === 'submit' ? 'Reviewing' : 'Pending', 
                taskType: type,
                replyData: data,
                timestamp: new Date().toLocaleString() 
            };
            db.ref('admin_requests/'+reqId).set(payload);
            db.ref('users/'+user.id+'/inbox/'+reqId).set(payload);
            alert("Request Submitted!");
        }

        function closeModal() { document.getElementById('submit-modal').style.display = 'none'; }

        function submitDone(reqId) {
            if(confirm("Mark as Completed?")) {
                db.ref('admin_requests/'+reqId).update({status: 'Reviewing'});
                db.ref('users/'+user.id+'/inbox/'+reqId).update({status: 'Reviewing'});
            }
        }

        function cancelTask(reqId) {
            if(confirm("Cancel Task?")) {
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
                    let color = i===0?'#f1c40f':(i===1?'#bdc3c7':(i===2?'#e67e22':'#2b2d42'));
                    list.innerHTML += `
                    <div style="background:white; margin:0 20px 10px; padding:15px; border-radius:15px; display:flex; justify-content:space-between; align-items:center;">
                        <div style="display:flex; align-items:center; gap:10px;">
                            <span style="font-weight:900; font-size:18px; color:${color}; width:30px;">#${i+1}</span>
                            <span style="font-weight:600;">${u.name}</span>
                        </div>
                        <span style="font-weight:bold; color:var(--primary);">৳${(u.balance||0).toFixed(0)}</span>
                    </div>`;
                });
            });
        }

        function loadInbox() {
            db.ref('users/' + user.id + '/inbox').limitToLast(30).on('value', snap => {
                const list = document.getElementById('inbox-list');
                list.innerHTML = '';
                if(!snap.val()) { list.innerHTML = '<p style="text-align:center;color:#ccc;margin-top:20px;">No History</p>'; return; }
                const msgs = Object.values(snap.val()).reverse();
                
                msgs.forEach(m => {
                    if(m.type === 'Notification') {
                        list.innerHTML += `<div class="msg-item" style="border-left-color:var(--accent);">
                            <div style="font-size:10px; color:#888;">System Notice • ${m.timestamp}</div>
                            <div style="margin-top:5px; font-weight:500;">${m.message}</div>
                        </div>`;
                        return;
                    }

                    let color = '#ccc';
                    let action = '';

                    if(m.type === 'Task') {
                        if(m.status === 'InProgress') {
                            color = '#3498db';
                            action = `<div style="margin-top:10px; padding-top:10px; border-top:1px dashed #eee;">
                                <div style="background:#f8f9fa; padding:10px; border-radius:8px; font-family:monospace; margin-bottom:10px; border:1px solid #eee;">${m.replyData}</div>
                                <div style="display:flex; gap:10px;">
                                    <button onclick="submitDone('${m.reqId}')" style="flex:1; padding:8px; background:#00b894; color:white; border:none; border-radius:8px;">Done</button>
                                    <button onclick="cancelTask('${m.reqId}')" style="flex:1; padding:8px; background:#ff7675; color:white; border:none; border-radius:8px;">Cancel</button>
                                </div>
                            </div>`;
                        } else if(m.status === 'Completed') color = '#00b894';
                        else if(m.status === 'Rejected' || m.status === 'Cancelled') color = '#ff7675';
                        else if(m.status === 'Reviewing') color = '#9b59b6';
                    } else if(m.type === 'Withdraw') {
                        if(m.status === 'Paid') color = '#00b894';
                        else if(m.status === 'Pending') color = '#f39c12';
                    }

                    list.innerHTML += `
                    <div class="msg-item" style="border-left-color:${color};">
                        <div style="display:flex; justify-content:space-between; font-size:10px; color:#888;">
                            <span>${m.timestamp}</span>
                            <span style="font-weight:bold; color:${color}">${m.status}</span>
                        </div>
                        <div style="font-weight:700; font-size:14px; margin-top:5px;">${m.type === 'Withdraw' ? `Cashout: ${m.method}` : m.taskName}</div>
                        <div style="font-size:12px; color:#666;">Amount: ৳${m.price || m.amount}</div>
                        ${action}
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
                alert("Withdrawal Pending!");
            });
        }

        function nav(page, el) {
            document.querySelectorAll('.page').forEach(p => p.classList.remove('active'));
            document.getElementById(page).classList.add('active');
            document.querySelectorAll('.nav-icon').forEach(b => b.classList.remove('active'));
            el.classList.add('active');
        }

        initApp();
    </script>
</body>
</html>
