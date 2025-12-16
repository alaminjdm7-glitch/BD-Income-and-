<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>Earn Wallet Pro</title>
    <script src="https://telegram.org/js/telegram-web-app.js"></script>
    <script src="https://www.gstatic.com/firebasejs/8.10.0/firebase-app.js"></script>
    <script src="https://www.gstatic.com/firebasejs/8.10.0/firebase-database.js"></script>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Inter:wght@400;500;600;700&display=swap');
        
        :root {
            --primary: #2563eb;
            --primary-dark: #1e40af;
            --success: #10b981;
            --warning: #f59e0b;
            --danger: #ef4444;
            --bg: #f3f4f6;
            --card: #ffffff;
            --text-main: #1f2937;
            --text-sub: #6b7280;
        }

        body {
            font-family: 'Inter', sans-serif;
            background: var(--bg);
            color: var(--text-main);
            margin: 0;
            padding-bottom: 90px;
            -webkit-tap-highlight-color: transparent;
        }

        /* SUPPORT FLOAT BTN */
        .support-float {
            position: fixed; bottom: 90px; right: 20px;
            background: #25D366; color: white;
            width: 50px; height: 50px; border-radius: 50%;
            display: flex; align-items: center; justify-content: center;
            box-shadow: 0 4px 15px rgba(37, 211, 102, 0.4);
            z-index: 900; font-size: 24px; cursor: pointer;
            transition: transform 0.2s;
        }
        .support-float:active { transform: scale(0.9); }

        /* HEADER */
        .header-pro {
            background: linear-gradient(135deg, #1e3a8a, #3b82f6);
            padding: 20px 20px 80px;
            border-bottom-left-radius: 30px;
            border-bottom-right-radius: 30px;
            color: white;
            position: relative;
        }
        .top-nav { display: flex; justify-content: space-between; align-items: center; }
        .user-block { display: flex; align-items: center; gap: 12px; }
        .u-avatar { 
            width: 48px; height: 48px; background: rgba(255,255,255,0.2); 
            border-radius: 50%; display: flex; align-items: center; justify-content: center; 
            font-size: 20px; border: 2px solid rgba(255,255,255,0.3);
        }

        /* BALANCE CARD */
        .balance-card {
            background: white; margin: -60px 20px 0; padding: 20px;
            border-radius: 20px; box-shadow: 0 10px 25px rgba(0,0,0,0.08);
            position: relative; z-index: 10;
        }
        .bal-label { font-size: 12px; color: var(--text-sub); text-transform: uppercase; letter-spacing: 0.5px; }
        .bal-value { font-size: 32px; font-weight: 800; color: var(--text-main); margin: 5px 0; }
        
        .quick-stats { display: flex; justify-content: space-between; margin-top: 15px; border-top: 1px solid #f0f0f0; padding-top: 15px; }
        .qs-item { text-align: center; width: 33%; }
        .qs-val { font-weight: 700; font-size: 14px; color: var(--primary); }
        .qs-lbl { font-size: 10px; color: var(--text-sub); }

        /* SECTIONS */
        .section-title { padding: 0 20px; margin-top: 25px; margin-bottom: 10px; font-weight: 700; font-size: 16px; color: var(--text-main); display: flex; align-items: center; gap: 8px; }
        
        /* TASK ITEM */
        .task-box {
            background: white; margin: 0 20px 12px; padding: 16px;
            border-radius: 16px; box-shadow: 0 2px 8px rgba(0,0,0,0.03);
            display: flex; justify-content: space-between; align-items: center;
            border: 1px solid #e5e7eb;
        }
        .btn-pro {
            background: var(--primary); color: white; border: none;
            padding: 8px 16px; border-radius: 8px; font-weight: 600; font-size: 12px;
        }

        /* HISTORY ITEM (PRO) */
        .hist-card {
            background: white; margin: 0 20px 10px; padding: 15px;
            border-radius: 12px; border: 1px solid #f3f4f6;
            display: flex; justify-content: space-between; align-items: center;
        }
        .h-left { display: flex; flex-direction: column; gap: 4px; }
        .h-title { font-weight: 600; font-size: 14px; }
        .h-date { font-size: 10px; color: var(--text-sub); }
        .h-right { text-align: right; }
        .h-amt { font-weight: 700; font-size: 14px; }
        .badge { padding: 4px 8px; border-radius: 4px; font-size: 10px; font-weight: 600; }
        .b-success { background: #d1fae5; color: #065f46; }
        .b-pending { background: #fef3c7; color: #92400e; }
        .b-fail { background: #fee2e2; color: #991b1b; }

        /* PROFILE STATS */
        .profile-grid {
            display: grid; grid-template-columns: 1fr 1fr; gap: 12px; padding: 0 20px;
        }
        .p-stat { background: white; padding: 15px; border-radius: 12px; text-align: center; box-shadow: 0 2px 5px rgba(0,0,0,0.02); }
        .p-val { font-size: 18px; font-weight: 700; color: var(--primary); }
        .p-lbl { font-size: 11px; color: var(--text-sub); margin-top: 2px; }

        /* RULES/ABOUT BOX */
        .rules-box {
            background: #fff; margin: 20px; padding: 20px; border-radius: 16px;
            border: 1px solid #e5e7eb;
        }
        .rules-content { font-size: 12px; color: var(--text-sub); line-height: 1.6; white-space: pre-line; }

        /* BOTTOM NAV */
        .btm-nav {
            position: fixed; bottom: 0; left: 0; width: 100%;
            background: white; padding: 12px 0;
            display: flex; justify-content: space-around;
            border-top: 1px solid #e5e7eb; z-index: 1000;
        }
        .nav-it { display: flex; flex-direction: column; align-items: center; color: var(--text-sub); font-size: 10px; cursor: pointer; }
        .nav-it i { font-size: 20px; margin-bottom: 4px; }
        .nav-it.active { color: var(--primary); }

        /* MODALS & HELPERS */
        .modal-ol { display: none; position: fixed; top: 0; left: 0; width: 100%; height: 100%; background: rgba(0,0,0,0.5); z-index: 2000; align-items: center; justify-content: center; }
        .modal-in { background: white; width: 85%; max-width: 320px; padding: 25px; border-radius: 16px; }
        input, select { width: 100%; padding: 12px; border: 1px solid #ddd; border-radius: 8px; margin-bottom: 10px; box-sizing: border-box; }
        
        .page { display: none; } .page.active { display: block; animation: fade 0.3s; }
        @keyframes fade { from { opacity: 0; } to { opacity: 1; } }
        
        .notice-bar { margin: 15px 20px 0; background: #fffbeb; color: #b45309; padding: 10px; border-radius: 8px; font-size: 12px; display: flex; align-items: center; }
    </style>
</head>
<body>

    <!-- SUPPORT BUTTON -->
    <a id="support-link" href="#" target="_blank" class="support-float"><i class="fab fa-whatsapp"></i></a>

    <!-- HEADER -->
    <div class="header-pro">
        <div class="top-nav">
            <div class="user-block">
                <div class="u-avatar"><i class="fas fa-user"></i></div>
                <div>
                    <div style="font-weight:700; font-size:16px;" id="u_name">...</div>
                    <div style="font-size:11px; opacity:0.8;">ID: <span id="u_id">...</span></div>
                </div>
            </div>
            <i class="fas fa-bell" style="font-size:20px; cursor:pointer;" onclick="go('p-hist')"></i>
        </div>
    </div>

    <!-- MAIN BALANCE -->
    <div class="balance-card">
        <div class="bal-label">Current Balance</div>
        <div class="bal-value">৳<span id="bal_main">0.00</span></div>
        <div class="quick-stats">
            <div class="qs-item"><div class="qs-val">৳<span id="bal_today">0</span></div><div class="qs-lbl">Today</div></div>
            <div class="qs-item"><div class="qs-val" id="task_pend">0</div><div class="qs-lbl">Pending</div></div>
            <div class="qs-item"><div class="qs-val">৳<span id="wd_pend">0</span></div><div class="qs-lbl">Wd. Pending</div></div>
        </div>
    </div>

    <!-- NOTICE -->
    <div id="notice-area" class="notice-bar" style="display:none;">
        <i class="fas fa-bullhorn" style="margin-right:10px;"></i>
        <marquee id="notice-text">Welcome to Earn Wallet Pro!</marquee>
    </div>

    <!-- PAGE 1: HOME (TASKS) -->
    <div id="p-home" class="page active">
        <div class="section-title"><i class="fas fa-briefcase"></i> Available Tasks</div>
        <div id="task-list"><div style="text-align:center; padding:20px; color:#aaa;">Loading...</div></div>
    </div>

    <!-- PAGE 2: HISTORY -->
    <div id="p-hist" class="page">
        <div class="section-title"><i class="fas fa-history"></i> Transaction History</div>
        <div id="hist-list" style="padding-bottom:20px;"></div>
    </div>

    <!-- PAGE 3: PROFILE -->
    <div id="p-prof" class="page">
        <div class="section-title"><i class="fas fa-chart-pie"></i> Your Statistics</div>
        
        <div class="profile-grid">
            <div class="p-stat"><div class="p-val" id="st-total-earn">৳0</div><div class="p-lbl">Lifetime Earn</div></div>
            <div class="p-stat"><div class="p-val" id="st-total-wd">৳0</div><div class="p-lbl">Total Withdrawn</div></div>
            <div class="p-stat"><div class="p-val" id="st-task-done">0</div><div class="p-lbl">Tasks Done</div></div>
            <div class="p-stat"><div class="p-val" id="st-task-rej">0</div><div class="p-lbl">Rejected</div></div>
        </div>

        <div class="section-title"><i class="fas fa-shield-alt"></i> About & Rules</div>
        <div class="rules-box">
            <h4 style="margin-top:0;">Terms of Service</h4>
            <div id="rules-content" class="rules-content">Loading...</div>
        </div>

        <div style="padding:20px;">
            <button onclick="go('p-wd')" style="width:100%; background:var(--primary); color:white; padding:15px; border:none; border-radius:12px; font-weight:bold;">Request Withdrawal</button>
        </div>
    </div>

    <!-- PAGE 4: WITHDRAW (Hidden by default, accessible via Profile) -->
    <div id="p-wd" class="page">
        <div class="section-title"><i class="fas fa-wallet"></i> Withdraw Funds</div>
        <div style="background:white; margin:0 20px; padding:25px; border-radius:16px;">
            <p style="text-align:center; color:#f59e0b; font-size:12px;">Min Withdraw: <b id="min-wd-txt">100</b> Tk</p>
            <select id="wd-method"><option>Bkash</option><option>Nagad</option><option>Rocket</option><option>Upay</option></select>
            <input type="number" id="wd-num" placeholder="017xxxxxxxx">
            <input type="number" id="wd-amt" placeholder="Amount">
            <button onclick="reqWd()" style="width:100%; background:var(--success); color:white; padding:12px; border:none; border-radius:8px; font-weight:600;">Confirm Withdraw</button>
        </div>
    </div>

    <!-- BOTTOM NAV -->
    <div class="btm-nav">
        <div class="nav-it active" onclick="go('p-home', this)"><i class="fas fa-home"></i>Home</div>
        <div class="nav-it" onclick="go('p-hist', this)"><i class="fas fa-list-alt"></i>History</div>
        <div class="nav-it" onclick="go('p-prof', this)"><i class="fas fa-user-circle"></i>Profile</div>
    </div>

    <!-- SUBMIT MODAL -->
    <div id="sub-modal" class="modal-ol">
        <div class="modal-in">
            <h3>Submit Work</h3>
            <input type="text" id="sm-email" placeholder="Gmail/Data">
            <input type="text" id="sm-pass" placeholder="Password/Info">
            <button onclick="doSub()" style="width:100%; background:var(--primary); color:white; padding:10px; border:none; border-radius:8px;">Submit</button>
            <button onclick="document.getElementById('sub-modal').style.display='none'" style="width:100%; margin-top:10px; background:#eee; border:none; padding:10px; border-radius:8px;">Cancel</button>
        </div>
    </div>

    <script>
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
        let user = tg.initDataUnsafe.user || { id: 112233, first_name: "Demo" };

        document.getElementById('u_name').innerText = user.first_name;
        document.getElementById('u_id').innerText = user.id;

        let minWd = 100, curTask = null;

        function init() {
            const ref = db.ref('users/' + user.id);
            ref.once('value', s => { if(!s.exists()) ref.set({ name: user.first_name, balance: 0, total_withdraw: 0, task_done: 0, task_rej: 0, today_earn: 0 }); });

            ref.on('value', s => {
                const u = s.val(); if(!u) return;
                // Stats Logic
                document.getElementById('bal_main').innerText = (u.balance||0).toFixed(2);
                document.getElementById('bal_today').innerText = (u.today_earn||0).toFixed(2);
                document.getElementById('wd_pend').innerText = (u.wd_pending||0).toFixed(2);
                document.getElementById('task_pend').innerText = u.task_pending||0;
                
                // Profile Stats
                document.getElementById('st-total-earn').innerText = "৳" + ((u.total_earn||0).toFixed(0));
                document.getElementById('st-total-wd').innerText = "৳" + ((u.total_withdraw||0).toFixed(0));
                document.getElementById('st-task-done').innerText = u.task_done||0;
                document.getElementById('st-task-rej').innerText = u.task_rej||0;
            });

            db.ref('app_settings').on('value', s => {
                const d = s.val();
                if(d) {
                    if(d.notice) { document.getElementById('notice-area').style.display='flex'; document.getElementById('notice-text').innerText=d.notice; }
                    if(d.min_withdraw) { minWd = parseInt(d.min_withdraw); document.getElementById('min-wd-txt').innerText=minWd; }
                    if(d.rules) document.getElementById('rules-content').innerText = d.rules;
                    if(d.support) document.getElementById('support-link').href = d.support;
                }
            });

            loadTasks();
            loadHistory();
        }

        // --- TASKS ---
        function loadTasks() {
            db.ref('tasks').on('value', s => {
                const l = document.getElementById('task-list'); l.innerHTML = '';
                if(!s.val()) { l.innerHTML='<div style="text-align:center;padding:20px;color:#aaa">No Tasks</div>'; return; }
                Object.keys(s.val()).forEach(k => {
                    const t = s.val()[k];
                    l.innerHTML += `
                    <div class="task-box">
                        <div>
                            <div style="font-weight:600;">${t.title}</div>
                            <div style="font-size:11px; color:#666;">${t.desc}</div>
                        </div>
                        <div style="text-align:right;">
                            <div style="color:var(--primary); font-weight:bold; font-size:14px;">৳${t.price}</div>
                            <button class="btn-pro" onclick="clkTask('${k}','${t.title}',${t.price},'${t.type}')">Start</button>
                        </div>
                    </div>`;
                });
            });
        }

        function clkTask(id, tit, pr, typ) {
            if(typ === 'submit') {
                curTask = {tit, pr};
                document.getElementById('sub-modal').style.display='flex';
            } else {
                if(confirm('Start Task?')) sendReq(tit, pr, 'normal', null);
            }
        }

        function doSub() {
            const e = document.getElementById('sm-email').value, p = document.getElementById('sm-pass').value;
            if(!e || !p) return alert("Fill All");
            sendReq(curTask.tit, curTask.pr, 'submit', `E:${e} P:${p}`);
            document.getElementById('sub-modal').style.display='none';
        }

        function sendReq(tit, pr, typ, dat) {
            const rid = Date.now();
            const p = { reqId: rid, userId: user.id, userName: user.first_name, type: 'Task', taskName: tit, price: pr, status: typ==='submit'?'Reviewing':'Pending', taskType: typ, replyData: dat, timestamp: new Date().toLocaleString() };
            db.ref('admin_requests/'+rid).set(p);
            db.ref('users/'+user.id+'/inbox/'+rid).set(p);
            db.ref('users/'+user.id).update({ task_pending: firebase.database.ServerValue.increment(1) });
            alert("Sent!");
        }

        // --- HISTORY ---
        function loadHistory() {
            db.ref('users/'+user.id+'/inbox').limitToLast(50).on('value', s => {
                const l = document.getElementById('hist-list'); l.innerHTML='';
                if(!s.val()) { l.innerHTML='<div style="text-align:center;color:#aaa">Empty</div>'; return; }
                const d = Object.values(s.val()).reverse();
                
                d.forEach(i => {
                    // Logic for badges
                    let badge = '', amt = '';
                    if(i.type === 'Withdraw') {
                        amt = `-৳${i.amount}`;
                        if(i.status === 'Paid') badge = `<span class="badge b-success">Payment Successful</span>`;
                        else if(i.status === 'Pending') badge = `<span class="badge b-pending">Processing</span>`;
                        else badge = `<span class="badge b-fail">Refunded</span>`;
                    } else if(i.type === 'Task') {
                        amt = `+৳${i.price}`;
                        if(i.status === 'Completed') badge = `<span class="badge b-success">Added</span>`;
                        else if(i.status === 'Rejected') badge = `<span class="badge b-fail">Rejected</span>`;
                        else badge = `<span class="badge b-pending">In Progress</span>`;
                    }

                    if(i.type !== 'Notification') {
                        l.innerHTML += `
                        <div class="hist-card">
                            <div class="h-left">
                                <span class="h-title">${i.type === 'Withdraw' ? 'Withdrawal' : i.taskName}</span>
                                <span class="h-date">${i.timestamp}</span>
                            </div>
                            <div class="h-right">
                                <span class="h-amt" style="color:${i.type==='Withdraw'?'#ef4444':'#10b981'}">${amt}</span><br>
                                ${badge}
                            </div>
                        </div>`;
                    }
                });
            });
        }

        // --- WITHDRAW ---
        function reqWd() {
            const m=document.getElementById('wd-method').value, n=document.getElementById('wd-num').value, a=parseFloat(document.getElementById('wd-amt').value);
            db.ref('users/'+user.id).transaction(u => {
                if(u && u.balance >= a && a >= minWd && n.length>9) { 
                    u.balance -= a; 
                    u.wd_pending = (u.wd_pending||0) + a;
                    return u; 
                }
                return;
            }, (err, comm) => {
                if(comm) {
                    const rid = Date.now();
                    const d = { reqId: rid, userId: user.id, userName: user.first_name, type: 'Withdraw', method: m, number: n, amount: a, status: 'Pending', timestamp: new Date().toLocaleString() };
                    db.ref('admin_requests/'+rid).set(d);
                    db.ref('users/'+user.id+'/inbox/'+rid).set(d);
                    alert("Withdrawal Pending!");
                    go('p-hist');
                } else alert("Insufficient Balance or Invalid Info");
            });
        }

        function go(p, el) {
            document.querySelectorAll('.page').forEach(x=>x.classList.remove('active'));
            document.getElementById(p).classList.add('active');
            if(el) {
                document.querySelectorAll('.nav-it').forEach(x=>x.classList.remove('active'));
                el.classList.add('active');
            }
        }

        init();
    </script>
</body>
</html>
