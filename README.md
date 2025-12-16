transparent; }

        # hidden; }
        .task-card::before { content: ''; position: absolute; left: 0; top: 0; bottom: 0; width: 4px; background: var(--primary); }
        .btn-start { background: linear-gradient(135deg, var(--primary), var(--secondary)); color: white; border: none; padding: 8px 18px; border-radius: 25px; font-weight: 600; font-size: 11px; cursor: pointer; }

        /* INBOX & OTHERS */
        .msg-card { background: white; margin: 0 20px 10px; padding: 12px; border-radius: 12px; box-shadow: 0 2px 10px rgba(0,0,0,0.03); border: 1px solid #eee; }
        .status-badge { padding: 3px 8px; border-radius: 6px; font-size: 10px; font-weight: bold; }
        .btn-cancel { background: #ff7675; color: white; padding: 8px; width: 48%; border: none; border-radius: 8px; cursor: pointer; font-weight: bold; font-size: 11px; }
        .btn-submit { background: #00b894; color: white; padding: 8px; width: 48%; border: none; border-radius: 8px; cursor: pointer; font-weight: bold; font-size: 11px; }

        /* NAV */
        .nav-bar { position: fixed; bottom: 15px; left: 15px; right: 15px; background: rgba(255,255,255,0.95); backdrop-filter: blur(10px); border-radius: 20px; padding: 12px 20px; display: flex; justify-content: space-between; box-shadow: 0 10px 40px rgba(0,0,0,0.1); z-index: 100; }
        .nav-item { text-align: center; color: #b2bec3; font-size: 10px; cursor: pointer; transition: 0.3s; }
        .nav-item.active { color: var(--primary); transform: translateY(-3px); }

        .page { display: none; animation: fadeIn 0.4s ease; } .page.active { display: block; }
        @keyframes fadeIn { from { opacity: 0; transform: translateY(10px); } to { opacity: 1; transform: translateY(0); } }
        
        input, select, textarea { width: 100%; padding: 14px; margin-bottom: 12px; border: 1px solid #eee; border-radius: 12px; background: #f9f9f9; box-sizing: border-box; font-family: inherit; }
        .marquee-container { margin: 15px 20px; background: #fff8e1; color: #f39c12; padding: 8px 12px; border-radius: 10px; font-size: 12px; display: flex; align-items: center; }
        .banner-box { margin: 0 20px; border-radius: 12px; overflow: hidden; display: none; }
        .banner-box img { width: 100%; display: block; }
    </style>
</head>
<body>

    <div id="ban-screen">
        <i class="fas fa-user-lock" style="font-size: 60px; color: #ff6b6b; margin-bottom: 20px;"></i>
        <h2>Restricted</h2><p>Contact Support</p>
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
        // FIREBASE CONFIG (নিচের কোড ঠিক থাকবে, শুধু আপনার কনফিগ বসাবেন যদি দরকার হয়)
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
                    const btnText = t.type === 'submit' ? 'Start Selling' : 'Start Task';
                    list.innerHTML += `
                    <div class="task-card">
                        <div>
                            <div style="font-weight:700; font-size:14px;">${t.title}</div>
                            <div style="font-size:11px; color:#95a5a6;">${t.desc}</div>
                            <span style="font-size:10px; background:#eee; padding:2px 5px; border-radius:4px;">${t.type==='submit'?'You Submit Data':'Admin Gives Data'}</span>
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
            // BUYING TASK (User provides data)
            if(type === 'submit') {
                const userData = prompt(`Enter Gmail Details for "${title}"\nFormat: Email:Password`);
                if(userData && userData.length > 5) {
                    const reqId = Date.now();
                    const data = { 
                        reqId, userId: user.id, userName: user.first_name, 
                        type: 'Task', taskName: title, price: price, 
                        status: 'Reviewing', // Directly to review
                        replyData: userData, // User's data
                        taskType: 'submit',
                        timestamp: new Date().toLocaleString() 
                    };
                    db.ref('admin_requests/'+reqId).set(data);
                    db.ref('users/'+user.id+'/inbox/'+reqId).set(data);
                    alert("Submitted! Admin will check and pay.");
                }
            } 
            // NORMAL TASK (Admin provides data)
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

        function submitDone(reqId) {
            if(confirm("Completed?")) {
                db.ref('admin_requests/'+reqId).update({status: 'Reviewing'});
                db.ref('users/'+user.id+'/inbox/'+reqId).update({status: 'Reviewing'});
            }
        }

        function cancelTask(reqId) {
            if(confirm("Cancel this task? You can apply for a new one.")) {
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
                arr.sort((a, b) => (b.balance || 0) - (a.balance || 0)); // Fix sorting

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
            db.ref('users/' + user.id + '/inbox').limitToLast(30).on('value', snap => {
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
                        if (m.status === 'InProgress') { // Normal task waiting for user to do
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

                    list.innerHTML += `
                    <div class="msg-card" style="border-left:4px solid ${statusColor};">
                        <div style="display:flex; justify-content:space-between; font-size:10px; color:#b2bec3;">
                            <span>${m.timestamp}</span>
                            <span style="font-weight:bold; color:${statusColor}">${m.status}</span>
                        </div>
                        <div style="font-weight:700; font-size:14px; margin-top:4px;">${m.type}: ${m.taskName || m.method}</div>
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
