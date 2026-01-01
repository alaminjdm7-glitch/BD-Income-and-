<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>VIP Earning App V6.2 Pro</title>
    <script src="https://telegram.org/js/telegram-web-app.js"></script>
    <script src="https://www.gstatic.com/firebasejs/8.10.0/firebase-app.js"></script>
    <script src="https://www.gstatic.com/firebasejs/8.10.0/firebase-database.js"></script>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Urbanist:wght@400;500;600;700;800&display=swap');
        
        :root {
            --primary: #6c5ce7; --secondary: #a29bfe; --success: #00b894; --warning: #fdcb6e;
            --danger: #ff7675; --dark: #2d3436; --light: #f5f6fa; --white: #ffffff; --gold: #d4af37;
        }

        body { font-family: 'Urbanist', sans-serif; background: var(--light); color: var(--dark); margin: 0; padding-bottom: 100px; -webkit-tap-highlight-color: transparent; overflow-x: hidden; }

        /* LOADER */
        #loader-screen { position: fixed; top: 0; left: 0; width: 100%; height: 100%; background: var(--white); z-index: 99999; display: flex; flex-direction: column; align-items: center; justify-content: center; transition: opacity 0.5s ease; }
        .spinner-box { width: 60px; height: 60px; border: 5px solid rgba(108, 92, 231, 0.1); border-top: 5px solid var(--primary); border-radius: 50%; animation: spin 1s linear infinite; }
        @keyframes spin { 0% { transform: rotate(0deg); } 100% { transform: rotate(360deg); } }
        .app-loading-text { margin-top: 20px; font-weight: 700; color: var(--primary); letter-spacing: 1px; animation: pulse 1.5s infinite; }

        /* MAINTENANCE */
        #maintenance-screen { display: none; position: fixed; top: 0; left: 0; width: 100%; height: 100%; background: var(--white); z-index: 99998; text-align: center; padding: 40px 20px; }
        .maintenance-icon { font-size: 60px; color: var(--danger); margin-bottom: 20px; }
        .maintenance-title { font-size: 24px; font-weight: 800; color: var(--dark); margin-bottom: 10px; }
        .timer-badge { display: inline-block; margin-top: 20px; padding: 10px 20px; background: #ffebee; color: var(--danger); border-radius: 30px; font-weight: bold; font-family: monospace; font-size: 16px; }

        /* MAIN APP */
        #main-app { display: none; }

        /* HEADER */
        .header-pro { background: linear-gradient(135deg, var(--primary), var(--secondary)); padding: 30px 25px 90px; border-radius: 0 0 40px 40px; color: white; position: relative; box-shadow: 0 10px 30px rgba(108, 92, 231, 0.3); }
        .top-nav { display: flex; justify-content: space-between; align-items: center; }
        .user-block { display: flex; align-items: center; gap: 15px; }
        .u-avatar { width: 52px; height: 52px; background: rgba(255,255,255,0.2); backdrop-filter: blur(5px); border-radius: 50%; display: flex; align-items: center; justify-content: center; font-size: 22px; border: 2px solid rgba(255,255,255,0.4); position: relative; }
        
        /* BADGES */
        .v-red { position: absolute; bottom: 0; right: 0; width: 16px; height: 16px; background: #ff4757; border: 2px solid #fff; border-radius: 50%; }
        .v-blue { position: absolute; bottom: 0; right: 0; width: 16px; height: 16px; background: #0984e3; border: 2px solid #fff; border-radius: 50%; }
        .v-gold { position: absolute; bottom: 0; right: 0; width: 18px; height: 18px; background: linear-gradient(45deg, #ffd700, #fdb931); border: 2px solid #fff; border-radius: 50%; display: flex; align-items: center; justify-content: center; font-size: 10px; color: #fff; }
        .red-dot { position: absolute; top: 0; right: 0; width: 10px; height: 10px; background: #ff4757; border-radius: 50%; border: 2px solid #fff; display: none; }

        /* BALANCE CARD */
        .balance-card { background: rgba(255,255,255,0.95); backdrop-filter: blur(10px); margin: -70px 20px 0; padding: 25px; border-radius: 25px; box-shadow: 0 15px 40px rgba(0,0,0,0.08); position: relative; z-index: 10; text-align: center; }
        .bal-label { font-size: 12px; color: #888; text-transform: uppercase; letter-spacing: 1px; font-weight: 600; }
        .bal-value { font-size: 38px; font-weight: 800; color: var(--primary); margin: 5px 0; display: block; }
        .quick-stats { display: flex; justify-content: space-between; margin-top: 20px; border-top: 1px solid #f0f0f0; padding-top: 15px; }
        .qs-item { text-align: center; width: 33%; }
        .qs-val { font-weight: 700; font-size: 15px; color: var(--dark); }
        .qs-lbl { font-size: 11px; color: #888; font-weight: 500; }

        /* VIP BANNER */
        .vip-banner { margin: 20px 20px 0; padding: 15px; background: linear-gradient(45deg, #1e272e, #000000); border-radius: 20px; color: #d4af37; display: flex; justify-content: space-between; align-items: center; box-shadow: 0 5px 15px rgba(0,0,0,0.2); border: 1px solid #d4af37; cursor: pointer; }

        /* SECTIONS */
        .section-title { padding: 0 25px; margin-top: 30px; margin-bottom: 15px; font-weight: 800; font-size: 17px; color: var(--dark); display: flex; align-items: center; gap: 10px; }
        
        /* PLAN ITEM */
        .plan-box { background: white; margin: 0 20px 15px; padding: 20px; border-radius: 20px; box-shadow: 0 5px 15px rgba(0,0,0,0.03); border: 1px solid #f0f0f0; display: flex; gap: 15px; position: relative; overflow: hidden; }
        .plan-img { width: 60px; height: 60px; border-radius: 12px; object-fit: cover; background: #eee; }
        .plan-info { flex: 1; }
        .plan-name { font-weight: 700; font-size: 16px; color: var(--dark); }
        .plan-roi { font-size: 12px; color: var(--success); font-weight: 600; margin-top: 4px; }
        .btn-buy { background: var(--dark); color: white; border: none; padding: 8px 16px; border-radius: 20px; font-weight: 700; font-size: 12px; cursor: pointer; }
        .btn-buy:disabled { background: #ccc; cursor: not-allowed; }

        /* TASK ITEM */
        .task-box { background: white; margin: 0 20px 15px; padding: 18px; border-radius: 20px; box-shadow: 0 5px 15px rgba(0,0,0,0.03); display: flex; justify-content: space-between; align-items: center; border: 1px solid #f0f0f0; transition: transform 0.2s; }
        .task-box:active { transform: scale(0.98); }
        .btn-pro { background: linear-gradient(135deg, var(--primary), var(--secondary)); color: white; border: none; padding: 10px 20px; border-radius: 30px; font-weight: 700; font-size: 12px; cursor: pointer; box-shadow: 0 4px 10px rgba(108, 92, 231, 0.3); }

        /* INBOX & HISTORY */
        .tabs-container { margin: 0 20px 15px; background: #e0e0e0; padding: 5px; border-radius: 15px; display: flex; }
        .tab-btn { flex: 1; border: none; background: transparent; padding: 12px; border-radius: 12px; font-weight: 700; font-size: 12px; color: #666; cursor: pointer; transition: 0.3s; }
        .tab-btn.active { background: white; color: var(--primary); box-shadow: 0 2px 8px rgba(0,0,0,0.1); }
        .hist-card { background: white; margin: 0 20px 12px; padding: 16px; border-radius: 16px; border-left: 5px solid #ccc; box-shadow: 0 3px 10px rgba(0,0,0,0.02); }
        .badge { padding: 4px 10px; border-radius: 6px; font-size: 10px; font-weight: 700; display: inline-block; margin-top: 5px; }
        .b-success { background: #e0f2f1; color: #00695c; } .b-pending { background: #fff3e0; color: #e65100; } .b-fail { background: #ffebee; color: #c62828; } .b-info { background: #e3f2fd; color: #1565c0; }

        /* PROFILE */
        .profile-grid { display: grid; grid-template-columns: 1fr 1fr; gap: 15px; padding: 0 20px; }
        .p-stat { background: white; padding: 15px; border-radius: 16px; text-align: center; box-shadow: 0 5px 15px rgba(0,0,0,0.03); }
        .p-val { font-size: 20px; font-weight: 800; color: var(--primary); }
        .rules-box { background: white; margin: 0 20px 20px; padding: 20px; border-radius: 20px; border: 1px solid #f0f0f0; box-shadow: 0 5px 20px rgba(0,0,0,0.03); }
        .rules-content { font-size: 13px; color: #555; line-height: 1.6; white-space: pre-line; }

        /* BOTTOM NAV */
        .btm-nav { position: fixed; bottom: 20px; left: 20px; right: 20px; background: #2d3436; padding: 15px 30px; display: flex; justify-content: space-between; border-radius: 25px; box-shadow: 0 10px 40px rgba(0,0,0,0.3); z-index: 1000; }
        .nav-it { color: #b2bec3; font-size: 22px; cursor: pointer; transition: 0.3s; }
        .nav-it.active { color: white; transform: translateY(-3px); }

        /* MODALS */
        #popup-notice { display: none; position: fixed; top: 0; left: 0; width: 100%; height: 100%; background: rgba(0,0,0,0.7); z-index: 10000; align-items: center; justify-content: center; backdrop-filter: blur(5px); }
        .popup-card { background: white; width: 85%; max-width: 320px; padding: 30px; border-radius: 25px; text-align: center; animation: zoomIn 0.3s ease; }
        @keyframes zoomIn { from { transform: scale(0.8); opacity: 0; } to { transform: scale(1); opacity: 1; } }

        .modal-ol { display: none; position: fixed; top: 0; left: 0; width: 100%; height: 100%; background: rgba(0,0,0,0.6); z-index: 2000; align-items: center; justify-content: center; }
        .modal-in { background: white; width: 85%; max-width: 320px; padding: 25px; border-radius: 20px; max-height: 80vh; overflow-y: auto; }
        input, select, textarea { width: 100%; padding: 14px; border: 1px solid #eee; border-radius: 12px; margin-bottom: 12px; background: #f9f9f9; box-sizing: border-box; outline: none; font-family: inherit; }
        
        .page { display: none; animation: fade 0.4s ease; } .page.active { display: block; }
        @keyframes fade { from { opacity: 0; transform: translateY(10px); } to { opacity: 1; transform: translateY(0); } }
        
        .notice-bar { margin: 15px 20px 0; background: #fff8e1; color: #f57f17; padding: 10px; border-radius: 12px; font-size: 12px; display: flex; align-items: center; font-weight: 600; border: 1px solid #ffe0b2; }
        .runtime-strip { background: #222; color: #0f0; font-size: 11px; padding: 5px 20px; text-align: center; font-family: monospace; font-weight: bold; }
        .vip-tag { font-size: 10px; background: gold; color: black; padding: 2px 6px; border-radius: 4px; font-weight: bold; margin-left: 5px; }
        
        /* ACTIVE PLAN CARD */
        .active-plan-card {
            background: linear-gradient(135deg, #d4af37, #b7950b);
            color: white; padding: 15px; margin: 0 20px 15px;
            border-radius: 15px; display: flex; align-items: center; justify-content: space-between;
            box-shadow: 0 5px 15px rgba(212, 175, 55, 0.3);
            animation: slideDown 0.5s ease;
        }
        @keyframes slideDown { from { transform: translateY(-20px); opacity: 0; } to { transform: translateY(0); opacity: 1; } }

        /* COPY BUTTON */
        .copy-btn { background: #eee; border: none; padding: 5px 10px; border-radius: 5px; font-size: 12px; cursor: pointer; margin-top: 5px; color: #333; }
        .copy-btn:active { background: #ddd; }
    </style>
</head>
<body>

    <!-- LOADING SCREEN -->
    <div id="loader-screen">
        <div class="spinner-box"></div>
        <div class="app-loading-text">INITIALIZING V6.2 PRO...</div>
    </div>

    <!-- MAINTENANCE SCREEN -->
    <div id="maintenance-screen">
        <i class="fas fa-tools maintenance-icon"></i>
        <div class="maintenance-title" id="maint-title">App is Closed</div>
        <div class="maintenance-msg" id="maint-msg">We are currently under maintenance.</div>
        <div id="maint-timer" class="timer-badge" style="display:none;">Opening in: 00:00:00</div>
    </div>

    <!-- MAIN APP WRAPPER -->
    <div id="main-app">
        <a id="support-link" href="#" target="_blank" class="support-float" style="position:fixed; bottom:110px; right:20px; background:#25D366; color:white; width:55px; height:55px; border-radius:50%; display:flex; align-items:center; justify-content:center; box-shadow:0 5px 20px rgba(37,211,102,0.4); z-index:900; font-size:28px; text-decoration:none;"><i class="fab fa-whatsapp"></i></a>

        <div class="runtime-strip">SERVER RUNTIME: <span id="runtime-display">Loading...</span></div>

        <!-- STARTUP NOTICE -->
        <div id="popup-notice">
            <div class="popup-card">
                <i class="fas fa-bell" style="font-size:45px; color:#fdcb6e; margin-bottom:15px;"></i>
                <h3 style="margin:0 0 10px 0; color:var(--dark);">Important Notice</h3>
                <p id="popup-text" style="font-size:13px; color:#666; line-height:1.5;">...</p>
                <button onclick="document.getElementById('popup-notice').style.display='none'" style="background:var(--primary); color:white; border:none; padding:12px 30px; border-radius:30px; font-weight:bold; margin-top:20px; cursor:pointer;">Okay</button>
            </div>
        </div>

        <!-- HEADER -->
        <div class="header-pro">
            <div class="top-nav">
                <div class="user-block">
                    <div class="u-avatar" id="u-avatar-container">
                        <i class="fas fa-user"></i>
                        <div id="u-verified-badge"></div>
                    </div>
                    <div>
                        <div style="font-weight:700; font-size:18px;" id="u_name">Loading... <span id="u-vip-tag" class="vip-tag" style="display:none;">VIP</span></div>
                        <div style="font-size:12px; opacity:0.8;">ID: <span id="u_id">...</span></div>
                    </div>
                </div>
                <div style="position:relative; cursor:pointer;" onclick="go('p-inbox', document.querySelectorAll('.nav-it')[2])">
                    <i class="fas fa-bell" style="font-size:24px;"></i>
                    <div class="red-dot" id="n-dot"></div>
                </div>
            </div>
        </div>

        <!-- BALANCE CARD -->
        <div class="balance-card">
            <div class="bal-label">Total Balance</div>
            <div class="bal-value">৳<span id="bal_main">0.00</span></div>
            <div class="quick-stats">
                <div class="qs-item"><div class="qs-val">৳<span id="bal_today">0</span></div><div class="qs-lbl">Today</div></div>
                <div class="qs-item"><div class="qs-val" id="task_pend">0</div><div class="qs-lbl">Pending</div></div>
                <div class="qs-item"><div class="qs-val">৳<span id="wd_pend">0</span></div><div class="qs-lbl">Processing</div></div>
            </div>
        </div>

        <!-- ACTIVE PLAN CARD -->
        <div id="active-plan-display" class="active-plan-card" style="display:none;">
            <div>
                <div style="font-size:12px; opacity:0.9;">Active Plan</div>
                <div style="font-weight:700; font-size:16px;" id="ap-name">Gold VIP</div>
            </div>
            <div style="text-align:right;">
                <div style="font-size:12px; opacity:0.9;">Next Income In</div>
                <div style="font-weight:700; font-size:14px;" id="ap-timer">--:--:--</div>
            </div>
        </div>

        <!-- VIP BANNER -->
        <div class="vip-banner" onclick="go('p-vip', document.querySelectorAll('.nav-it')[1])">
            <div>
                <div style="font-weight:800; font-size:18px;"><i class="fas fa-crown"></i> VIP Membership</div>
                <div style="font-size:11px; opacity:0.8;">Buy plans for daily auto income</div>
            </div>
            <i class="fas fa-chevron-right"></i>
        </div>

        <!-- NOTICE BAR -->
        <div id="notice-area" class="notice-bar" style="display:none;">
            <i class="fas fa-bullhorn" style="margin-right:10px;"></i>
            <marquee id="notice-text">Welcome!</marquee>
        </div>

        <!-- PAGE 1: HOME (TASKS) -->
        <div id="p-home" class="page active">
            <div class="section-title"><i class="fas fa-bolt" style="color:#f1c40f"></i> Available Tasks</div>
            <div id="task-list"><div style="text-align:center; padding:30px; color:#aaa;">Loading tasks...</div></div>
        </div>

        <!-- PAGE 2: VIP / PLANS -->
        <div id="p-vip" class="page">
            <div class="section-title"><i class="fas fa-gem" style="color:#d4af37"></i> Premium Plans</div>
            
            <!-- DEPOSIT INFO -->
            <div style="margin: 0 20px 15px; background: #e3f2fd; padding:15px; border-radius:15px; border:1px solid #bbdefb;">
                <div style="font-weight:bold; color:#1565c0; margin-bottom:5px;"><i class="fas fa-info-circle"></i> Verification Info</div>
                <div style="font-size:13px; color:#555;">To unlock VIP Plans & Withdrawals, you need to deposit.</div>
                <div style="margin-top:10px; font-size:14px;" id="dep-text-home">Loading Deposit Number...</div>
            </div>

            <div id="plans-list"></div>
        </div>

        <!-- PAGE 3: LEADERBOARD -->
        <div id="p-leader" class="page">
            <div class="section-title"><i class="fas fa-trophy" style="color:#fdcb6e"></i> Top Earners</div>
            <div id="leaderboard-list"></div>
        </div>

        <!-- PAGE 4: INBOX -->
        <div id="p-inbox" class="page">
            <div class="section-title"><i class="fas fa-envelope-open-text" style="color:#0984e3"></i> Inbox & Activity</div>
            <div class="tabs-container">
                <button class="tab-btn active" onclick="filterInbox('all', this)">🔔 Notices</button>
                <button class="tab-btn" onclick="filterInbox('task', this)">📝 Tasks</button>
                <button class="tab-btn" onclick="filterInbox('money', this)">💸 History</button>
            </div>
            <div id="inbox-list" style="padding-bottom:20px;"></div>
        </div>

        <!-- PAGE 5: PROFILE -->
        <div id="p-prof" class="page">
            <div class="section-title"><i class="fas fa-chart-pie" style="color:#6c5ce7"></i> Your Statistics</div>
            <div class="profile-grid">
                <div class="p-stat"><div class="p-val" id="st-total-earn">৳0</div><div class="qs-lbl">Lifetime Earn</div></div>
                <div class="p-stat"><div class="p-val" id="st-total-wd">৳0</div><div class="qs-lbl">Total Cashout</div></div>
                <div class="p-stat"><div class="p-val" id="st-task-done">0</div><div class="qs-lbl">Completed</div></div>
                <div class="p-stat"><div class="p-val" id="st-task-rej">0</div><div class="qs-lbl">Rejected</div></div>
            </div>

            <div class="section-title"><i class="fas fa-info-circle" style="color:#00b894"></i> About & Rules</div>
            <div class="rules-box">
                <div id="rules-content" class="rules-content">Loading rules...</div>
            </div>

            <div style="padding:0 20px;">
                <button onclick="checkBalanceAndOpenWd()" style="width:100%; background:var(--dark); color:white; padding:15px; border:none; border-radius:15px; font-weight:bold; font-size:16px;">Request Withdrawal <i class="fas fa-arrow-right"></i></button>
            </div>
        </div>

        <!-- BOTTOM NAV -->
        <div class="btm-nav">
            <i class="fas fa-home nav-it active" onclick="go('p-home', this)"></i>
            <i class="fas fa-crown nav-it" onclick="go('p-vip', this)"></i>
            <i class="fas fa-folder-open nav-it" onclick="go('p-inbox', this)"></i>
            <i class="fas fa-user-circle nav-it" onclick="go('p-prof', this)"></i>
        </div>

        <!-- TASK SUBMIT MODAL -->
        <div id="sub-modal" class="modal-ol">
            <div class="modal-in">
                <h3>📧 Submit Data</h3>
                <p style="font-size:11px; color:#aaa; margin-bottom:15px;">Enter details for this task.</p>
                <input type="text" id="sm-email" placeholder="Gmail / Username">
                <input type="text" id="sm-pass" placeholder="Password / Info">
                <button onclick="doSub()" style="width:100%; background:var(--primary); color:white; padding:12px; border:none; border-radius:12px; font-weight:bold;">Submit Now</button>
                <button onclick="document.getElementById('sub-modal').style.display='none'" style="width:100%; margin-top:10px; background:#f1f2f6; border:none; padding:12px; border-radius:12px; font-weight:bold;">Cancel</button>
            </div>
        </div>

        <!-- DEPOSIT MODAL -->
        <div id="dep-modal" class="modal-ol">
            <div class="modal-in">
                <h3>💳 Deposit for VIP</h3>
                <div id="dep-info" style="background:#f0f0f0; padding:15px; border-radius:10px; font-size:13px; margin-bottom:15px; border:1px dashed #ccc;">
                    Loading info...
                </div>
                
                <label style="font-size:12px; font-weight:bold; color:#333;">Amount (Tk)</label>
                <input type="number" id="dep-amount" placeholder="e.g. 500">
                
                <label style="font-size:12px; font-weight:bold; color:#333;">Transaction ID (TrxID)</label>
                <input type="text" id="dep-trx" placeholder="e.g. 8H3K...">

                <label style="font-size:12px; font-weight:bold; color:#333;">Sent From Number</label>
                <input type="number" id="dep-num" placeholder="e.g. 017...">

                <button onclick="reqDep()" style="width:100%; background:var(--warning); color:white; padding:12px; border:none; border-radius:12px; font-weight:bold;">Submit Deposit</button>
                <button onclick="document.getElementById('dep-modal').style.display='none'" style="width:100%; margin-top:10px; background:#f1f2f6; border:none; padding:12px; border-radius:12px; font-weight:bold;">Close</button>
            </div>
        </div>

        <!-- WITHDRAW MODAL -->
        <div id="wd-modal" class="modal-ol">
            <div class="modal-in">
                <h3>💸 Withdraw Funds</h3>
                <p style="font-size:12px; color:#f1c40f; margin-bottom:15px;">Min Amount: <b id="min-wd-txt">100</b> Tk</p>
                <div id="pm-container" style="display:flex; gap:10px; margin-bottom:15px; overflow-x:auto; padding-bottom:5px;"></div>
                <input type="hidden" id="selected-method">
                <input type="number" id="wd-num" placeholder="Wallet Number">
                <input type="number" id="wd-amt" placeholder="Amount">
                <button onclick="reqWd()" style="width:100%; background:var(--success); color:white; padding:12px; border:none; border-radius:12px; font-weight:bold;">Confirm Request</button>
                <button onclick="document.getElementById('wd-modal').style.display='none'" style="width:100%; margin-top:10px; background:#f1f2f6; border:none; padding:12px; border-radius:12px; font-weight:bold;">Close</button>
            </div>
        </div>
    </div>

    <script>
        const firebaseConfig = {
            apiKey: "AIzaSyC3uf_5gEMK_JjsmFW01ofRE-ESaXhio9I",
            authDomain: "taskbot-b6fe2.firebaseapp.com",
            databaseURL: "https://taskbot-b6fe2-default-rtdb.firebaseio.com",
            projectId: "taskbot-b6fe2",
            storageBucket: "taskbot-b6fe2.firebases.t.app",
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

        let minWd = 100, curTask = null, allInboxData = [], planInterval = null;
        let appSettings = {};
        
        // Store purchased plan keys to prevent re-buy
        let purchasedPlanKeys = []; 

        function init() {
            // 1. LOAD APP SETTINGS
            db.ref('app_settings').on('value', s => {
                const d = s.val();
                appSettings = d || {};
                if(d) {
                    if(d.appName) { document.title = d.appName; document.querySelector('.app-loading-text').innerText = d.appName.toUpperCase(); }
                    checkMaintenance(d);
                    if(d.notice) { document.getElementById('popup-notice').style.display='flex'; document.getElementById('popup-text').innerText=d.notice; }
                    if(d.min_withdraw) { minWd = parseInt(d.min_withdraw); document.getElementById('min-wd-txt').innerText=minWd; }
                    if(d.rules) document.getElementById('rules-content').innerText = d.rules;
                    if(d.support) document.getElementById('support-link').href = d.support;
                    
                    // Deposit Info with Copy Button
                    let depHtml = "Contact admin for deposit info.";
                    if(d.depositNumber) {
                        depHtml = `<div style="font-weight:bold; margin-bottom:5px;">Send Money to: <span style="color:#d63031">${d.depositNumber}</span></div>`;
                        depHtml += `<div>Method: ${d.depositMethod || 'Bkash'}</div>`;
                        depHtml += `<button class="copy-btn" onclick="copyToClip('${d.depositNumber}')"><i class="fas fa-copy"></i> Copy Number</button>`;
                    }
                    document.getElementById('dep-text-home').innerHTML = depHtml;
                    document.getElementById('dep-info').innerHTML = depHtml;
                }
            });

            // 2. LOAD USER DATA
            const ref = db.ref('users/' + user.id);
            ref.once('value', s => { 
                if(!s.exists()) ref.set({ 
                    name: user.first_name, balance: 0, total_withdraw: 0, task_done: 0, task_rej: 0, today_earn: 0, 
                    isVerified: false, verifyType: null, activePlan: null, planExpiry: 0, lastClaim: 0, purchasedPlans: []
                }); 
            });

            ref.on('value', s => {
                const u = s.val(); if(!u) return;
                
                // Update UI
                document.getElementById('bal_main').innerText = (u.balance||0).toFixed(2);
                document.getElementById('bal_today').innerText = (u.today_earn||0).toFixed(2);
                document.getElementById('wd_pend').innerText = (u.wd_pending||0).toFixed(2);
                document.getElementById('task_pend').innerText = u.task_pending||0;
                
                document.getElementById('st-total-earn').innerText = "৳" + ((u.total_earn||0).toFixed(0));
                document.getElementById('st-total-wd').innerText = "৳" + ((u.total_withdraw||0).toFixed(0));
                document.getElementById('st-task-done').innerText = u.task_done||0;
                document.getElementById('st-task-rej').innerText = u.task_rej||0;

                // Badges
                const vBadge = document.getElementById('u-verified-badge');
                const vTag = document.getElementById('u-vip-tag');
                if(u.isVerified) {
                    let badgeHTML = '';
                    if(u.verifyType === 'red') badgeHTML = `<div class="v-red"></div>`;
                    else if(u.verifyType === 'blue') badgeHTML = `<div class="v-blue"></div>`;
                    else if(u.verifyType === 'gold') badgeHTML = `<div class="v-gold"><i class="fas fa-check"></i></div>`;
                    vBadge.innerHTML = badgeHTML;
                    vTag.style.display = 'inline-block';
                } else {
                    vBadge.innerHTML = '';
                    vTag.style.display = 'none';
                }

                // Load purchased plans history
                purchasedPlanKeys = u.purchasedPlans || [];

                // Plan Logic
                if(u.activePlan && u.planExpiry > Date.now()) {
                    startPlanSystem(u);
                } else {
                    stopPlanSystem();
                    if(u.planExpiry && u.planExpiry <= Date.now() && u.activePlan) {
                        // Plan expired just now
                        db.ref('users/'+user.id).update({activePlan: null, planExpiry: 0});
                    }
                }
            });

            setTimeout(() => {
                const settings = db.ref('app_settings'); 
                settings.once('value').then(snap => {
                    const d = snap.val();
                    if(!d || !d.maintenance || d.maintenance.active !== true) {
                        document.getElementById('loader-screen').style.opacity = '0';
                        setTimeout(() => {
                            document.getElementById('loader-screen').style.display = 'none';
                            document.getElementById('main-app').style.display = 'block';
                        }, 500);
                    }
                });
            }, 2000);

            loadTasks();
            fetchInbox();
            loadLeaderboard();
            initRuntime();
            loadPaymentMethods();
            loadPlans();
        }

        function copyToClip(text) {
            navigator.clipboard.writeText(text).then(() => alert("Number Copied!")).catch(err => alert("Failed to copy"));
        }

        // --- AUTO INCOME SYSTEM ---
        function startPlanSystem(u) {
            const planId = u.activePlan;
            const expiry = u.planExpiry;
            const lastClaim = u.lastClaim || 0;
            const now = Date.now();

            // We try to get plan details from DB. If deleted, we use local cache or just keep timer running
            db.ref('plans/'+planId).once('value', ps => {
                const p = ps.val();
                document.getElementById('active-plan-display').style.display = 'flex';
                document.getElementById('ap-name').innerText = p ? p.name : "Active VIP Plan";

                const incomeInterval = 24 * 60 * 60 * 1000; 

                if (now - lastClaim >= incomeInterval) {
                    if(!window.isClaiming) {
                        window.isClaiming = true;
                        db.ref('users/'+user.id).transaction(userObj => {
                            if(userObj && userObj.activePlan === planId && userObj.planExpiry > now) {
                                const dailyInc = p ? p.dailyIncome : 0;
                                userObj.balance = (userObj.balance || 0) + dailyInc;
                                userObj.total_earn = (userObj.total_earn || 0) + dailyInc;
                                userObj.lastClaim = now;
                                return userObj;
                            }
                            return;
                        }, (error, committed) => {
                            window.isClaiming = false;
                            if(committed && p) {
                                // Optionally alert income, but silent is better for auto
                            }
                        });
                    }
                }

                const timeLeft = incomeInterval - (now - lastClaim);
                const h = Math.floor((timeLeft % (1000 * 60 * 60 * 24)) / (1000 * 60 * 60));
                const m = Math.floor((timeLeft % (1000 * 60 * 60)) / (1000 * 60));
                const s = Math.floor((timeLeft % (1000 * 60)) / 1000);
                document.getElementById('ap-timer').innerText = `${h}h ${m}m ${s}s`;
            });

            if(planInterval) clearInterval(planInterval);
            planInterval = setInterval(() => {
                db.ref('users/'+user.id).once('value', s => {
                    const u2 = s.val();
                    if(u2 && u2.activePlan === planId && u2.planExpiry > Date.now()) {
                        const n = Date.now();
                        const lc = u2.lastClaim || 0;
                        if(n - lc >= incomeInterval && !window.isClaiming) {
                            startPlanSystem(u2); 
                        } else {
                            const tl = incomeInterval - (n - lc);
                            const hr = Math.floor((tl % (1000 * 60 * 60 * 24)) / (1000 * 60 * 60));
                            const mn = Math.floor((tl % (1000 * 60 * 60)) / (1000 * 60));
                            const sc = Math.floor((tl % (1000 * 60)) / 1000);
                            const el = document.getElementById('ap-timer');
                            if(el) el.innerText = `${hr}h ${mn}m ${sc}s`;
                        }
                    } else {
                        stopPlanSystem();
                        document.getElementById('active-plan-display').style.display = 'none';
                    }
                });
            }, 1000);
        }

        function stopPlanSystem() {
            if(planInterval) clearInterval(planInterval);
            planInterval = null;
            document.getElementById('active-plan-display').style.display = 'none';
        }

        // --- PLAN LOGIC ---
        function loadPlans() {
            db.ref('plans').on('value', s => {
                const l = document.getElementById('plans-list'); l.innerHTML = '';
                const plans = s.val();
                if(!plans) { l.innerHTML = '<div style="text-align:center; padding:20px; color:#aaa">No plans available</div>'; return; }
                
                Object.keys(plans).forEach(k => {
                    const p = plans[k];
                    // CHECK IF ALREADY PURCHASED
                    let btnState = '';
                    let btnText = 'BUY';
                    let btnClick = `onclick="buyPlan('${k}')"`; // Default click

                    if (purchasedPlanKeys.includes(k)) {
                        btnState = 'disabled';
                        btnText = 'Purchased';
                        btnClick = ''; // Remove click
                    }

                    l.innerHTML += `
                    <div class="plan-box">
                        <img src="${p.image || 'https://picsum.photos/seed/vip/60/60'}" class="plan-img">
                        <div class="plan-info">
                            <div class="plan-name">${p.name}</div>
                            <div class="plan-roi">Daily: ৳${p.dailyIncome} • Valid: ${p.validity} Days</div>
                            <div style="font-size:11px; color:#888; margin-top:5px;">Price: ৳${p.price}</div>
                        </div>
                        <button class="btn-buy" ${btnState} ${btnClick}>${btnText}</button>
                    </div>`;
                });
            });
        }

        function buyPlan(planId) {
            db.ref('users/'+user.id).once('value', s => {
                const u = s.val();
                if(!u.isVerified) return alert("❌ You must be Verified (Deposit) to buy a VIP Plan.");
                if(u.activePlan) return alert("❌ You already have an active plan. Wait for it to expire.");
                
                // Double check list locally to be safe
                if (purchasedPlanKeys.includes(planId)) return alert("❌ You have already purchased this plan before.");

                db.ref('plans/'+planId).once('value', ps => {
                    const p = ps.val();
                    if(!p) return alert("❌ This plan is no longer available.");
                    if(u.balance < p.price) return alert("❌ Insufficient Balance. Please Deposit first.");

                    if(confirm(`Buy ${p.name} for ৳${p.price}?`)) {
                        db.ref('users/'+user.id+'/balance').transaction(b => (b||0) - p.price);
                        
                        const expiry = Date.now() + (p.validity * 24 * 60 * 60 * 1000);
                        
                        // Add to purchased list
                        purchasedPlanKeys.push(planId);

                        db.ref('users/'+user.id).update({
                            activePlan: planId,
                            planExpiry: expiry,
                            lastClaim: Date.now(),
                            purchasedPlans: purchasedPlanKeys
                        });
                        
                        alert("✅ Plan Activated! Income will be added automatically every 24 hours.");
                        loadPlans(); // Refresh UI to show button disabled
                    }
                });
            });
        }

        // --- DEPOSIT SYSTEM ---
        function reqDep() {
            const amt = parseFloat(document.getElementById('dep-amount').value);
            const trx = document.getElementById('dep-trx').value;
            const num = document.getElementById('dep-num').value;
            if(!amt || !trx || !num) return alert("Fill all fields");
            const rid = Date.now();
            const d = { reqId: rid, userId: user.id, userName: user.first_name, type: 'Deposit', amount: amt, trxId: trx, number: num, status: 'Pending', timestamp: new Date().toLocaleString() };
            db.ref('admin_requests/'+rid).set(d);
            db.ref('users/'+user.id+'/inbox/'+rid).set(d);
            alert("Deposit Request Sent!");
            document.getElementById('dep-modal').style.display='none';
        }

        // --- MAINTENANCE ---
        let maintInterval = null;
        function checkMaintenance(settings) {
            const maintDiv = document.getElementById('maintenance-screen');
            const mainDiv = document.getElementById('main-app');
            const timerDiv = document.getElementById('maint-timer');
            if(settings.maintenance && settings.maintenance.active === true) {
                maintDiv.style.display = 'block'; mainDiv.style.display = 'none'; document.getElementById('loader-screen').style.display = 'none'; 
                if(settings.maintenance.message) document.getElementById('maint-msg').innerText = settings.maintenance.message;
                if(settings.maintenance.title) document.getElementById('maint-title').innerText = settings.maintenance.title;
                if(settings.maintenance.endTime) {
                    timerDiv.style.display = 'inline-block';
                    if(maintInterval) clearInterval(maintInterval);
                    maintInterval = setInterval(() => {
                        const now = Date.now(); const end = parseInt(settings.maintenance.endTime); const diff = end - now;
                        if(diff <= 0) {
                            timerDiv.style.display = 'none'; maintDiv.style.display = 'none'; mainDiv.style.display = 'block';
                            db.ref('app_settings/maintenance').update({ active: false }); clearInterval(maintInterval);
                        } else {
                            const h = Math.floor(diff/3600000); const m = Math.floor((diff%3600000)/60000); const s = Math.floor((diff%60000)/1000);
                            timerDiv.innerText = `Opening in: ${h.toString().padStart(2,'0')}:${m.toString().padStart(2,'0')}:${s.toString().padStart(2,'0')}`;
                        }
                    }, 1000);
                }
            } else {
                maintDiv.style.display = 'none'; if(maintInterval) clearInterval(maintInterval);
            }
        }

        // --- RUNTIME ---
        function initRuntime() {
            db.ref('runtime').on('value', snap => {
                const r = snap.val(); const display = document.getElementById('runtime-display');
                if(!r || !r.running) { display.innerText = "OFFLINE"; if(window.rtInt) clearInterval(window.rtInt); return; }
                const startTime = r.startTime;
                if(window.rtInt) clearInterval(window.rtInt);
                window.rtInt = setInterval(() => {
                    const diff = Math.floor((Date.now() - startTime)/1000);
                    const h = Math.floor(diff/3600); const m = Math.floor((diff%3600)/60); const s = diff%60;
                    display.innerText = `${h.toString().padStart(2,'0')}:${m.toString().padStart(2,'0')}:${s.toString().padStart(2,'0')}`;
                }, 1000);
            });
        }

        // --- PAYMENT METHODS ---
        function loadPaymentMethods() {
            db.ref('app_settings/paymentMethods').once('value', s => {
                const div = document.getElementById('pm-container'); div.innerHTML = '';
                const methods = s.val();
                if(methods) {
                    Object.keys(methods).forEach(key => {
                        const url = methods[key];
                        const img = document.createElement('img');
                        img.src = url;
                        img.style = "height:40px; cursor:pointer; border:2px solid #eee; border-radius:8px; padding:2px;";
                        img.onclick = () => {
                            document.querySelectorAll('#pm-container img').forEach(i => i.style.borderColor='#eee');
                            img.style.borderColor = 'var(--primary)';
                            document.getElementById('selected-method').value = key;
                        };
                        div.appendChild(img);
                    });
                } else { div.innerHTML = '<span style="font-size:12px; color:#aaa;">No methods set</span>'; }
            });
        }

        function checkBalanceAndOpenWd() {
            db.ref('users/'+user.id).once('value', s => {
                const u = s.val();
                if(!u.isVerified) return alert("⚠️ Withdraw Locked: You must verify your account by depositing first.");
                const bal = u.balance || 0;
                if(bal < minWd) { alert(`⚠️ Warning: You need at least ৳${minWd} to withdraw. Current balance: ৳${bal.toFixed(2)}`); }
                else { document.getElementById('wd-modal').style.display='flex'; }
            });
        }

        function loadTasks() {
            db.ref('tasks').on('value', s => {
                const l = document.getElementById('task-list'); l.innerHTML = '';
                if(!s.val()) { l.innerHTML='<div style="text-align:center;padding:30px;color:#aaa">No active tasks</div>'; return; }
                Object.keys(s.val()).forEach(k => {
                    const t = s.val()[k];
                    const btnTxt = t.type === 'submit' ? 'Sell Now' : 'Start Task';
                    l.innerHTML += `
                    <div class="task-box">
                        <div><div style="font-weight:700; font-size:15px;">${t.title}</div><div style="font-size:11px; color:#888;">${t.desc}</div></div>
                        <div style="text-align:right;"><div style="color:var(--primary); font-weight:800; font-size:16px;">৳${t.price}</div><button class="btn-pro" onclick="clkTask('${k}','${t.title}',${t.price},'${t.type}')">${btnTxt}</button></div>
                    </div>`;
                });
            });
        }

        function clkTask(id, tit, pr, typ) {
            if(typ === 'submit') { curTask = {tit, pr}; document.getElementById('sub-modal').style.display='flex'; }
            else { if(confirm('Start Task?')) sendReq(tit, pr, 'normal', null); }
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
            alert("Request Sent!");
        }

        function fetchInbox() {
            db.ref('users/'+user.id+'/inbox').on('value', s => {
                if(!s.val()) { allInboxData = []; } else { allInboxData = Object.values(s.val()).reverse(); }
                const hasNoti = allInboxData.some(i => i.type === 'Notification');
                document.getElementById('n-dot').style.display = hasNoti ? 'block' : 'none';
                filterInbox('all', document.querySelectorAll('.tab-btn')[0]);
            });
        }

        function filterInbox(type, btn) {
            document.querySelectorAll('.tab-btn').forEach(b => b.classList.remove('active'));
            if(btn) btn.classList.add('active');
            const l = document.getElementById('inbox-list'); l.innerHTML = '';
            const filtered = allInboxData.filter(i => {
                if(type==='all') return i.type==='Notification';
                if(type==='task') return i.type==='Task';
                if(type==='money') return i.type==='Withdraw' || i.type==='Deposit';
            });
            if(filtered.length === 0) { l.innerHTML='<div style="text-align:center;padding:20px;color:#aaa">No Data</div>'; return; }
            filtered.forEach(i => {
                if(i.type === 'Notification') { l.innerHTML += `<div class="hist-card" style="border-left-color:#f1c40f"><div style="font-size:10px; color:#aaa;">🔔 Notice • ${i.timestamp}</div><div style="font-weight:600; margin-top:5px;">${i.message}</div></div>`; return; }
                let badge = '', border = '#ccc', action = '';
                if(i.type === 'Task') {
                    if(i.status === 'Completed') { badge='<span class="badge b-success">Added</span>'; border='#00b894'; }
                    else if(i.status === 'Rejected') { badge='<span class="badge b-fail">Rejected</span>'; border='#ff7675'; }
                    else if(i.status === 'InProgress') {
                        border='#3498db';
                        action=`<div style="margin-top:10px; padding:10px; background:#f1f2f6; border-radius:8px; font-family:monospace;">${i.replyData}</div><div style="margin-top:5px; display:flex; gap:5px;"><button onclick="actTask('${i.reqId}','Reviewing')" style="flex:1; background:#00b894; color:white; border:none; padding:5px; border-radius:5px;">Done</button><button onclick="actTask('${i.reqId}','Cancelled')" style="flex:1; background:#ff7675; color:white; border:none; padding:5px; border-radius:5px;">Cancel</button></div>`;
                    }
                    else badge='<span class="badge b-pending">Pending</span>';
                } else if(i.type === 'Withdraw') {
                    if(i.status === 'Paid') { badge='<span class="badge b-success">Payment Successful</span>'; border='#00b894'; }
                    else if(i.status === 'Rejected') { badge='<span class="badge b-fail">Refunded</span>'; border='#ff7675'; }
                    else { badge='<span class="badge b-pending">Pending</span>'; border='#f1c40f'; }
                } else if (i.type === 'Deposit') {
                     if(i.status === 'Approved') { badge='<span class="badge b-success">Verified</span>'; border='#00b894'; }
                     else { badge='<span class="badge b-pending">Checking</span>'; border='#f1c40f'; }
                }
                l.innerHTML += `
                <div class="hist-card" style="border-left-color:${border}">
                    <div style="display:flex; justify-content:space-between; font-size:10px; color:#aaa;"><span>${i.timestamp}</span><span>${i.type}</span></div>
                    <div style="font-weight:700; font-size:14px; margin-top:5px;">${i.taskName || i.method || 'Deposit'}</div>
                    <div style="font-size:12px; margin-top:2px;">Amount: ৳${i.price || i.amount}</div>
                    ${badge} ${action}
                </div>`;
            });
        }

        function actTask(rid, st) {
            if(!confirm(st+'?')) return;
            db.ref('users/'+user.id+'/inbox/'+rid).update({status: st});
            db.ref('admin_requests/'+rid).update({status: st});
        }

        function reqWd() {
            const m = document.getElementById('selected-method').value;
            const n = document.getElementById('wd-num').value;
            const a = parseFloat(document.getElementById('wd-amt').value);
            if(!m) return alert("Select Payment Method");
            db.ref('users/'+user.id).transaction(u => {
                if(u && u.balance >= a && a >= minWd && n.length>9) { u.balance -= a; u.wd_pending = (u.wd_pending||0) + a; return u; }
                return;
            }, (err, comm) => {
                if(comm) {
                    const rid = Date.now();
                    const d = { reqId: rid, userId: user.id, userName: user.first_name, type: 'Withdraw', method: m, number: n, amount: a, status: 'Pending', timestamp: new Date().toLocaleString() };
                    db.ref('admin_requests/'+rid).set(d);
                    db.ref('users/'+user.id+'/inbox/'+rid).set(d);
                    alert("Request Sent!"); document.getElementById('wd-modal').style.display='none';
                } else alert("Error: Check Balance or Min Amount");
            });
        }

        function loadLeaderboard() {
            db.ref('users').orderByChild('balance').limitToLast(10).on('value', snap => {
                const l = document.getElementById('leaderboard-list'); l.innerHTML = '';
                let arr=[]; snap.forEach(c=>arr.push(c.val())); arr.sort((a,b)=>b.balance-a.balance);
                arr.forEach((u,i) => {
                    let col = i===0?'#f1c40f':(i===1?'#b2bec3':(i===2?'#e17055':'#2d3436'));
                    l.innerHTML += `<div style="background:white; margin:0 20px 10px; padding:15px; border-radius:15px; display:flex; justify-content:space-between; align-items:center;"><span style="font-weight:800; color:${col}; width:30px;">#${i+1}</span> <span>${u.name}</span> <span style="font-weight:bold; color:var(--primary);">৳${u.balance.toFixed(0)}</span></div>`;
                });
            });
        }

        function go(p, el) {
            document.querySelectorAll('.page').forEach(x=>x.classList.remove('active'));
            document.getElementById(p).classList.add('active');
            if(el) { document.querySelectorAll('.nav-it').forEach(x=>x.classList.remove('active')); el.classList.add('active'); }
        }

        init();
    </script>
</body>
</html>
