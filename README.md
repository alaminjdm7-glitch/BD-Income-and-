<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>Earn & Tasks</title>
    <script src="https://telegram.org/js/telegram-web-app.js"></script>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    
    <!-- Firebase SDK -->
    <script src="https://www.gstatic.com/firebasejs/8.10.0/firebase-app.js"></script>
    <script src="https://www.gstatic.com/firebasejs/8.10.0/firebase-database.js"></script>

    <style>
        @import url('https://fonts.googleapis.com/css2?family=Poppins:wght@300;400;600;700&display=swap');

        :root {
            --primary-gradient: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
            --bg-color: #f0f2f5;
            --text-dark: #2d3748;
            --white: #ffffff;
            --success: #00b894;
            --pending: #fdcb6e;
        }

        body {
            font-family: 'Poppins', sans-serif;
            background-color: var(--bg-color);
            margin: 0;
            padding-bottom: 80px;
            color: var(--text-dark);
            -webkit-tap-highlight-color: transparent;
            font-size: 13px;
        }

        .header {
            background: var(--primary-gradient);
            padding: 20px 20px 45px 20px;
            border-radius: 0 0 25px 25px;
            color: var(--white);
            display: flex;
            align-items: center;
            box-shadow: 0 4px 15px rgba(118, 75, 162, 0.3);
        }

        .profile-img {
            width: 45px; height: 45px;
            background: rgba(255,255,255,0.2);
            border-radius: 50%;
            display: flex; align-items: center; justify-content: center;
            font-size: 20px; border: 2px solid rgba(255,255,255,0.5); margin-right: 12px;
        }

        .balance-card {
            background: var(--white);
            margin: -30px 20px 15px 20px;
            padding: 15px;
            border-radius: 15px;
            box-shadow: 0 8px 20px rgba(0,0,0,0.06);
            text-align: center;
            position: relative;
        }
        .balance-amount { font-size: 32px; font-weight: 700; color: #764ba2; }

        .container { padding: 0 15px; animation: fadeIn 0.4s ease; }
        
        .card {
            background: var(--white);
            border-radius: 12px;
            padding: 12px;
            margin-bottom: 10px;
            display: flex; justify-content: space-between; align-items: center;
            box-shadow: 0 2px 5px rgba(0,0,0,0.03);
            border-left: 4px solid #764ba2;
        }
        
        .btn-action {
            background: var(--primary-gradient);
            color: white; border: none; padding: 8px 15px; border-radius: 8px;
            font-size: 11px; font-weight: 600; cursor: pointer;
        }

        .msg-box {
            background: white; padding: 12px; margin-bottom: 10px; border-radius: 10px;
            border: 1px solid #eee; position: relative;
        }
        .msg-header { display: flex; justify-content: space-between; margin-bottom: 5px; font-size: 11px; color: #888; }
        .reply-box {
            background: #2d3436; color: #55efc4; padding: 10px; margin-top: 8px;
            border-radius: 6px; font-family: monospace; font-size: 12px; word-break: break-all;
            user-select: text;
        }
        .status-badge { padding: 2px 6px; border-radius: 4px; font-size: 10px; color: white; }
        .bg-pending { background: var(--pending); color: #333; }
        .bg-success { background: var(--success); }

        .bottom-nav {
            position: fixed; bottom: 0; width: 100%; background: white;
            display: flex; justify-content: space-around; padding: 10px 0;
            border-radius: 20px 20px 0 0; box-shadow: 0 -5px 20px rgba(0,0,0,0.05); z-index: 100;
        }
        .nav-item { text-align: center; color: #a0aec0; cursor: pointer; flex: 1; transition: 0.2s; }
        .nav-item.active { color: #764ba2; transform: translateY(-3px); }
        .nav-icon { font-size: 20px; margin-bottom: 3px; display: block; }
        
        .page { display: none; }
        .page.active { display: block; }
        @keyframes fadeIn { from {opacity:0; transform: translateY(10px);} to {opacity:1; transform: translateY(0);} }

        .form-control {
            width: 100%; padding: 12px; border: 1px solid #ddd;
            border-radius: 10px; background: #f9f9f9; box-sizing: border-box; margin-bottom: 10px;
        }
    </style>
</head>
<body>

    <div class="header">
        <div class="profile-img"><i class="fas fa-user-circle"></i></div>
        <div>
            <h3 style="margin:0; font-size:16px;" id="u_name">Guest</h3>
            <p style="margin:0; font-size:11px; opacity:0.8;">ID: <span id="u_id">...</span></p>
        </div>
    </div>

    <div class="balance-card">
        <div style="font-size:11px; color:#777; text-transform:uppercase;">Current Balance</div>
        <div class="balance-amount">৳<span id="balance">0.00</span></div>
    </div>

    <div id="page-tasks" class="page container active">
        <h4 style="margin:10px 0; color:#555;">Available Tasks</h4>
        
        <div class="card">
            <div>
                <h4 style="margin:0; font-size:14px;">Create Gmail</h4>
                <p style="margin:2px 0 0; font-size:11px; color:#666;">Create new fresh account</p>
            </div>
            <div>
                <span style="color:#00b894; font-weight:bold; font-size:12px;">৳15.00</span><br>
                <button class="btn-action" onclick="applyTask('Create Gmail', 15)">Start</button>
            </div>
        </div>

        <div class="card">
            <div>
                <h4 style="margin:0; font-size:14px;">Recover Gmail</h4>
                <p style="margin:2px 0 0; font-size:11px; color:#666;">Old account recovery</p>
            </div>
            <div>
                <span style="color:#00b894; font-weight:bold; font-size:12px;">৳20.00</span><br>
                <button class="btn-action" onclick="applyTask('Recover Gmail', 20)">Start</button>
            </div>
        </div>
    </div>

    <div id="page-inbox" class="page container">
        <h4 style="margin:10px 0; color:#555;">My History & Inbox</h4>
        <div id="inbox-list">
            <div style="text-align:center; padding:20px; color:#aaa;">Loading history...</div>
        </div>
    </div>

    <div id="page-withdraw" class="page container">
        <h4 style="margin:10px 0; color:#555;">Withdraw Money</h4>
        <div style="background:white; padding:20px; border-radius:15px; box-shadow:0 5px 15px rgba(0,0,0,0.05);">
            <select id="wd_method" class="form-control">
                <option value="bKash">bKash</option>
                <option value="Nagad">Nagad</option>
                <option value="Rocket">Rocket</option>
            </select>
            <input type="number" id="wd_number" class="form-control" placeholder="Mobile Number">
            <input type="number" id="wd_amount" class="form-control" placeholder="Amount (Min 100)">
            <button class="btn-action" style="width:100%; padding:14px; font-size:14px;" onclick="requestWithdraw()">Confirm Withdraw</button>
        </div>
    </div>

    <div class="bottom-nav">
        <div class="nav-item active" onclick="switchTab('page-tasks', this)">
            <i class="nav-icon fas fa-tasks"></i> Tasks
        </div>
        <div class="nav-item" onclick="switchTab('page-inbox', this)">
            <i class="nav-icon fas fa-envelope"></i> Inbox
        </div>
        <div class="nav-item" onclick="switchTab('page-withdraw', this)">
            <i class="nav-icon fas fa-wallet"></i> Wallet
        </div>
    </div>

    <script>
        // ✅ আপনার দেওয়া Firebase Config এখানে বসিয়ে দেওয়া হয়েছে
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
        
        if (!firebase.apps.length) {
            firebase.initializeApp(firebaseConfig);
        }
        const db = firebase.database();

        const tg = window.Telegram.WebApp;
        tg.expand();
        
        // ডেমো ইউজার (Telegram ছাড়া টেস্ট করার জন্য)
        let user = tg.initDataUnsafe.user || { id: 12345678, first_name: "TestUser", username: "tester" };
        
        document.getElementById('u_name').innerText = user.first_name;
        document.getElementById('u_id').innerText = user.id;

        function initUser() {
            db.ref('users/' + user.id).once('value', (snapshot) => {
                if (!snapshot.exists()) {
                    db.ref('users/' + user.id).set({
                        name: user.first_name,
                        balance: 0,
                        joined: new Date().toISOString()
                    });
                }
            });
            db.ref('users/' + user.id + '/balance').on('value', (snap) => {
                const bal = snap.val() || 0;
                document.getElementById('balance').innerText = parseFloat(bal).toFixed(2);
            });
            loadInbox();
        }

        function applyTask(taskName, price) {
            tg.showPopup({
                title: 'Start Task?',
                message: 'Admin will send you the Gmail details in this App Inbox.',
                buttons: [{type: 'ok', id: 'ok'}, {type: 'cancel'}]
            }, function(btn) {
                if (btn === 'ok') {
                    const reqId = Date.now();
                    const reqData = {
                        reqId: reqId,
                        userId: user.id,
                        userName: user.first_name,
                        type: 'Task',
                        taskName: taskName,
                        price: price,
                        status: 'Pending',
                        timestamp: new Date().toLocaleString()
                    };

                    db.ref('admin_requests/' + reqId).set(reqData);
                    db.ref('users/' + user.id + '/inbox/' + reqId).set(reqData);

                    tg.showAlert("Request Sent! Check Inbox.");
                }
            });
        }

        function requestWithdraw() {
            const method = document.getElementById('wd_method').value;
            const number = document.getElementById('wd_number').value;
            const amount = parseFloat(document.getElementById('wd_amount').value);
            
            db.ref('users/' + user.id + '/balance').once('value', (snap) => {
                const currentBal = snap.val() || 0;

                if (!number || amount < 100) {
                    tg.showAlert("Invalid Number or Minimum 100 Tk.");
                    return;
                }
                if (currentBal < amount) {
                    tg.showAlert("Insufficient Balance!");
                    return;
                }

                const reqId = Date.now();
                const reqData = {
                    reqId: reqId,
                    userId: user.id,
                    userName: user.first_name,
                    type: 'Withdraw',
                    method: method,
                    number: number,
                    amount: amount,
                    status: 'Pending',
                    timestamp: new Date().toLocaleString()
                };

                db.ref('admin_requests/' + reqId).set(reqData);
                db.ref('users/' + user.id + '/inbox/' + reqId).set(reqData);

                tg.showAlert("Withdrawal Request Submitted!");
                document.getElementById('wd_amount').value = "";
            });
        }

        function loadInbox() {
            db.ref('users/' + user.id + '/inbox').on('value', (snapshot) => {
                const list = document.getElementById('inbox-list');
                list.innerHTML = '';
                const data = snapshot.val();

                if (!data) {
                    list.innerHTML = '<div style="text-align:center; margin-top:30px; color:#aaa">No activity yet.</div>';
                    return;
                }

                Object.values(data).reverse().forEach(item => {
                    let badgeClass = item.status === 'Pending' ? 'bg-pending' : 'bg-success';
                    let title = item.type === 'Task' ? item.taskName : `Withdraw (${item.method})`;
                    let amountTxt = item.type === 'Task' ? `Reward: ৳${item.price}` : `Amount: ৳${item.amount}`;
                    
                    let replyHtml = '';
                    if (item.replyData) {
                        replyHtml = `
                        <div class="reply-box">
                            <i class="fas fa-key"></i> Credentials:<br>
                            ${item.replyData}
                        </div>`;
                    }

                    list.innerHTML += `
                    <div class="msg-box">
                        <div class="msg-header">
                            <span>${item.timestamp}</span>
                            <span class="status-badge ${badgeClass}">${item.status}</span>
                        </div>
                        <div style="display:flex; justify-content:space-between; align-items:center;">
                            <strong>${title}</strong>
                            <span style="font-size:11px; color:#666;">${amountTxt}</span>
                        </div>
                        ${replyHtml}
                    </div>`;
                });
            });
        }

        function switchTab(id, el) {
            document.querySelectorAll('.page').forEach(p => p.classList.remove('active'));
            document.getElementById(id).classList.add('active');
            document.querySelectorAll('.nav-item').forEach(i => i.classList.remove('active'));
            el.classList.add('active');
        }

        initUser();

    </script>
</body>
</html>
