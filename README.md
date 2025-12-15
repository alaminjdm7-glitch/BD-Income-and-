<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>Pro Task App</title>
    <script src="https://telegram.org/js/telegram-web-app.js"></script>
    <script src="https://www.gstatic.com/firebasejs/8.10.0/firebase-app.js"></script>
    <script src="https://www.gstatic.com/firebasejs/8.10.0/firebase-database.js"></script>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Poppins:wght@300;400;600;700&display=swap');
        :root { --primary: #6c5ce7; --bg: #f1f2f6; --text: #2d3436; }
        body { font-family: 'Poppins', sans-serif; background: var(--bg); color: var(--text); margin: 0; padding-bottom: 80px; font-size: 13px; -webkit-tap-highlight-color: transparent; }

        #ban-screen { display: none; position: fixed; top:0; left:0; width:100%; height:100%; background: #fff; z-index: 9999; flex-direction: column; align-items: center; justify-content: center; text-align: center; }
        
        .header { background: linear-gradient(135deg, #6c5ce7, #a29bfe); padding: 20px 20px 50px; border-radius: 0 0 25px 25px; color: white; display: flex; align-items: center; }
        .user-pic { width: 45px; height: 45px; background: rgba(255,255,255,0.2); border-radius: 50%; display: flex; align-items: center; justify-content: center; margin-right: 10px; border: 2px solid rgba(255,255,255,0.5); }
        
        .stats-box { background: white; margin: -35px 15px 10px; padding: 15px; border-radius: 15px; box-shadow: 0 5px 15px rgba(0,0,0,0.05); display: grid; grid-template-columns: 1fr 1fr; gap: 10px; text-align: center; }
        .stat-item { background: #f8f9fa; padding: 10px; border-radius: 10px; }
        .stat-val { font-weight: 700; font-size: 16px; color: var(--primary); }
        .stat-label { font-size: 10px; color: #636e72; }

        .marquee-box { background: #fff3cd; color: #856404; padding: 8px; margin: 10px 15px; border-radius: 8px; font-size: 12px; display: flex; align-items: center; border: 1px solid #ffeeba; }
        .banner-img { width: calc(100% - 30px); margin: 0 15px 10px; border-radius: 10px; display: none; }

        .section-title { margin: 15px; font-weight: 600; color: #636e72; font-size: 14px; }
        .task-card { background: white; margin: 0 15px 10px; padding: 12px; border-radius: 12px; display: flex; justify-content: space-between; align-items: center; box-shadow: 0 2px 5px rgba(0,0,0,0.03); border-left: 4px solid var(--primary); }
        .btn-start { background: var(--primary); color: white; border: none; padding: 6px 15px; border-radius: 20px; font-weight: 600; font-size: 11px; cursor: pointer; }

        .leader-item { display: flex; justify-content: space-between; padding: 10px; border-bottom: 1px solid #eee; font-size: 12px; }
        .rank-1 { color: #f1c40f; font-weight: bold; } .rank-2 { color: #95a5a6; font-weight: bold; } .rank-3 { color: #d35400; font-weight: bold; }

        .nav { position: fixed; bottom: 0; width: 100%; background: white; display: flex; border-top: 1px solid #eee; padding: 10px 0; z-index: 100; }
        .nav-btn { flex: 1; text-align: center; color: #b2bec3; font-size: 10px; }
        .nav-btn.active { color: var(--primary); font-weight: bold; }
        .nav-btn i { font-size: 18px; display: block; margin-bottom: 3px; }

        .page { display: none; padding-bottom: 20px; } .page.active { display: block; }
        .loading-ph { text-align: center; color: #aaa; padding: 20px; }
        
        input, select { width:100%; padding:12px; margin-bottom:10px; border:1px solid #ddd; border-radius:8px; box-sizing: border-box; background: #fff; }
    </style>
</head>
<body>

    <div id="ban-screen">
        <i class="fas fa-ban" style="font-size: 50px; color: red;"></i>
        <h2>Access Denied</h2>
        <p>You have been banned.</p>
    </div>

    <div class="header">
        <div class="user-pic"><i class="fas fa-user"></i></div>
        <div>
            <div style="font-size: 16px; font-weight: 600;" id="u_name">Loading...</div>
            <div style="font-size: 11px; opacity: 0.8;">ID: <span id="u_id">---</span></div>
        </div>
    </div>

    <div class="stats-box">
        <div class="stat-item"><div class="stat-val">৳<span id="bal_main">0.00</span></div><div class="stat-label">Balance</div></div>
        <div class="stat-item"><div class="stat-val">৳<span id="bal_today">0.00</span></div><div class="stat-label">Today Earn</div></div>
        <div class="stat-item"><div class="stat-val" id="task_today">0</div><div class="stat-label">Today Tasks</div></div>
        <div class="stat-item"><div class="stat-val">৳<span id="wd_total">0.00</span></div><div class="stat-label">Total Cashout</div></div>
    </div>

    <div id="notice-area" class="marquee-box" style="display:none;">
        <i class="fas fa-bullhorn" style="margin-right:8px; color:#d35400;"></i>
        <marquee id="notice-text">Welcome!</marquee>
    </div>
    <img id="banner-area" src="" class="banner-img">

    <!-- HOME -->
    <div id="p-home" class="page active">
        <div class="section-title">Available Tasks</div>
        <div id="task-list"><div class="loading-ph">Loading...</div></div>
    </div>

    <!-- LEADERBOARD -->
    <div id="p-leader" class="page">
        <div class="section-title">🏆 Top Earners</div>
        <div style="background: white; margin: 0 15px; border-radius: 12px; padding: 10px;" id="leaderboard-list">
            <div class="loading-ph">Loading...</div>
        </div>
    </div>

    <!-- INBOX -->
    <div id="p-inbox" class="page">
        <div class="section-title">📩 Inbox & Notifications</div>
        <div id="inbox-list" style="padding: 0 15px;">Loading...</div>
    </div>

    <!-- WALLET -->
    <div id="p-profile" class="page">
        <div class="section-title">💸 Withdraw Funds</div>
        <div style="background: white; margin: 0 15px; padding: 20px; border-radius: 12px;">
            <label style="font-size:11px; font-weight:bold; color:#555;">Select Method:</label>
            <select id="wd_method">
                <option value="Bkash">bKash (বিকাশ)</option>
                <option value="Nagad">Nagad (নগদ)</option>
                <option value="Rocket">Rocket (রকেট)</option>
                <option value="Upay">Upay (উপায়)</option>
            </select>
            <input type="number" id="wd_number" placeholder="Enter Mobile Number (e.g. 017...)" >
            <input type="number" id="wd_amount" placeholder="Amount (Min 100)">
            <button onclick="requestWithdraw()" style="width:100%; background:var(--primary); color:white; padding:12px; border:none; border-radius:8px; font-weight:bold;">Withdraw Now</button>
        </div>
    </div>

    <div class="nav">
        <div class="nav-btn active" onclick="nav('p-home', this)"><i class="fas fa-home"></i>Home</div>
        <div class="nav-btn" onclick="nav('p-leader', this)"><i class="fas fa-trophy"></i>Top</div>
        <div class="nav-btn" onclick="nav('p-inbox', this)"><i class="fas fa-envelope"></i>Inbox</div>
        <div class="nav-btn" onclick="nav('p-profile', this)"><i class="fas fa-wallet"></i>Wallet</div>
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

        function initApp() {
            const userRef = db.ref('users/' + user.id);
            userRef.once('value', s => {
                if(!s.exists()) userRef.set({ name: user.first_name, balance: 0, total_withdraw: 0, banned: false });
            });

            userRef.on('value', snap => {
                const u = snap.val();
                if(!u) return;
                if(u.banned) document.getElementById('ban-screen').style.display = 'flex';
                
                // Daily Reset Check
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
                    if(s.banner) { document.getElementById('banner-area').style.display = 'block'; document.getElementById('banner-area').src = s.banner; }
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
                if(!snap.val()) { list.innerHTML = '<div class="loading-ph">No tasks available</div>'; return; }
                const tasks = snap.val();
                Object.keys(tasks).forEach(k => {
                    const t = tasks[k];
                    list.innerHTML += `
                    <div class="task-card">
                        <div><div style="font-weight:600;">${t.title}</div><div style="font-size:11px; color:#777;">${t.desc}</div></div>
                        <div style="text-align:right;"><div style="color:#00b894; font-weight:bold;">৳${t.price}</div>
                        <button class="btn-start" onclick="applyTask('${k}', '${t.title}', ${t.price})">Start</button></div>
                    </div>`;
                });
            });
        }

        function applyTask(id, title, price) {
            if(confirm(`Start ${title}? ৳${price}`)) {
                const reqId = Date.now();
                const data = { reqId, userId: user.id, userName: user.first_name, type: 'Task', taskName: title, price: price, status: 'Pending', timestamp: new Date().toLocaleString() };
                db.ref('admin_requests/'+reqId).set(data);
                db.ref('users/'+user.id+'/inbox/'+reqId).set(data);
                alert("Request Sent!");
            }
        }

        function loadLeaderboard() {
            db.ref('users').orderByChild('balance').limitToLast(10).on('value', snap => {
                const list = document.getElementById('leaderboard-list');
                list.innerHTML = '';
                let arr = [];
                snap.forEach(c => arr.push(c.val()));
                arr.reverse().forEach((u, i) => {
                    let r = i===0?'rank-1':(i===1?'rank-2':(i===2?'rank-3':''));
                    list.innerHTML += `<div class="leader-item"><span class="${r}">${i+1}. ${u.name}</span><span style="font-weight:bold;">৳${(u.balance||0).toFixed(0)}</span></div>`;
                });
            });
        }

        function loadInbox() {
            db.ref('users/' + user.id + '/inbox').limitToLast(30).on('value', snap => {
                const list = document.getElementById('inbox-list');
                list.innerHTML = '';
                if(!snap.val()) { list.innerHTML = '<p style="text-align:center;color:#aaa">Empty</p>'; return; }
                const msgs = Object.values(snap.val()).reverse();
                
                msgs.forEach(m => {
                    // Notification Style
                    if(m.type === 'Notification') {
                        list.innerHTML += `
                        <div style="background:#e3f2fd; padding:12px; margin-bottom:10px; border-radius:8px; border-left:4px solid #2196f3;">
                            <div style="font-size:10px; color:#666;">📢 Admin Notice • ${m.timestamp}</div>
                            <div style="font-size:13px; margin-top:5px; color:#333;">${m.message}</div>
                        </div>`;
                        return;
                    }

                    // Task/Withdraw Style
                    let color = m.status === 'Success' || m.status === 'Paid' ? '#00b894' : (m.status === 'Pending' ? '#e67e22' : 'red');
                    let reply = m.replyData ? `<div style="background:#333; color:#0f0; padding:5px; margin-top:5px; border-radius:4px; font-family:monospace; word-break:break-all;">${m.replyData}</div>` : '';
                    let details = m.type === 'Withdraw' ? `(${m.method}) ${m.number}` : m.taskName;

                    list.innerHTML += `
                    <div style="background:white; padding:10px; margin-bottom:10px; border-radius:8px; border-left:4px solid ${color}; box-shadow:0 2px 4px rgba(0,0,0,0.05);">
                        <div style="display:flex; justify-content:space-between; font-size:10px; color:#888;">
                            <span>${m.timestamp}</span>
                            <span style="font-weight:bold; color:${color}">${m.status}</span>
                        </div>
                        <div style="font-weight:600; margin-top:2px;">${m.type}: ${details}</div>
                        <div style="font-size:11px;">Amount: ৳${m.price || m.amount}</div>
                        ${reply}
                    </div>`;
                });
            });
        }

        function requestWithdraw() {
            const method = document.getElementById('wd_method').value;
            const number = document.getElementById('wd_number').value;
            const amount = parseFloat(document.getElementById('wd_amount').value);

            db.ref('users/' + user.id + '/balance').once('value', snap => {
                if((snap.val()||0) < amount) return alert("Insufficient Balance");
                if(!number || number.length < 10) return alert("Invalid Number");
                if(amount < 100) return alert("Min 100 Tk");

                const reqId = Date.now();
                const data = { reqId, userId: user.id, userName: user.first_name, type: 'Withdraw', method, number, amount, status: 'Pending', timestamp: new Date().toLocaleString() };
                db.ref('admin_requests/' + reqId).set(data);
                db.ref('users/' + user.id + '/inbox/' + reqId).set(data);
                alert("Withdraw Request Sent!");
                document.getElementById('wd_amount').value = '';
            });
        }

        function nav(page, el) {
            document.querySelectorAll('.page').forEach(p => p.classList.remove('active'));
            document.getElementById(page).classList.add('active');
            document.querySelectorAll('.nav-btn').forEach(b => b.classList.remove('active'));
            el.classList.add('active');
        }

        initApp();
    </script>
</body>
</html>
