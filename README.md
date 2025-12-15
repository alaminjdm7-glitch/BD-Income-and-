<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Telegram Task App</title>
    <!-- Telegram Web App SDK -->
    <script src="https://telegram.org/js/telegram-web-app.js"></script>
    <!-- FontAwesome for Icons -->
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.0.0/css/all.min.css">

    <style>
        :root {
            --primary-color: #6200ea;
            --secondary-color: #00c853;
            --background-color: #f3f4f6;
            --card-background: #ffffff;
            --text-color: #1f2937;
            --danger-color: #ff3d00;
        }

        body {
            font-family: 'Segoe UI', sans-serif;
            background-color: var(--background-color);
            margin: 0;
            padding-bottom: 80px; /* Space for footer */
            color: var(--text-color);
            user-select: none;
        }

        .container {
            max-width: 450px;
            margin: 0 auto;
            background-color: var(--card-background);
            min-height: 100vh;
            display: flex;
            flex-direction: column;
            box-shadow: 0 0 10px rgba(0,0,0,0.1);
        }

        /* Header */
        .header {
            display: flex;
            align-items: center;
            padding: 15px;
            background: linear-gradient(135deg, var(--primary-color), #3700b3);
            color: white;
            position: sticky;
            top: 0;
            z-index: 100;
        }
        
        .header-profile-pic {
            width: 45px;
            height: 45px;
            border-radius: 50%;
            margin-right: 12px;
            border: 2px solid #fff;
            background: #eee;
            display: flex;
            align-items: center;
            justify-content: center;
            color: #333;
        }

        .header-user-info h3 { margin: 0; font-size: 16px; font-weight: 600; }
        .header-user-info p { margin: 0; font-size: 12px; opacity: 0.9; }

        /* Content Area */
        .content { flex-grow: 1; padding: 20px; overflow-y: auto; }

        /* Dashboard Cards */
        .card {
            background: var(--card-background);
            border-radius: 15px;
            padding: 20px;
            margin-bottom: 20px;
            box-shadow: 0 4px 15px rgba(0,0,0,0.05);
            border: 1px solid #eee;
        }

        .balance-amount {
            font-size: 38px;
            font-weight: 800;
            color: var(--secondary-color);
            margin: 5px 0 15px 0;
            text-align: center;
        }

        /* Task Items */
        .task-item {
            display: flex;
            justify-content: space-between;
            align-items: center;
            padding: 15px;
            border: 1px solid #eee;
            border-radius: 12px;
            margin-bottom: 10px;
            background: #fff;
        }
        .task-info h4 { margin: 0; font-size: 15px; }
        .task-info p { margin: 5px 0 0; font-size: 12px; color: #666; }
        .task-reward { color: var(--secondary-color); font-weight: bold; }

        /* Notification Box */
        .msg-box {
            background: #f8f9fa;
            border-left: 4px solid var(--primary-color);
            padding: 15px;
            margin-bottom: 15px;
            border-radius: 8px;
        }
        .msg-header { display: flex; justify-content: space-between; margin-bottom: 5px; font-size: 12px; color: #888; }
        .msg-title { font-weight: bold; margin-bottom: 5px; display: block; }
        .credentials {
            background: #222;
            color: #0f0;
            padding: 10px;
            border-radius: 5px;
            font-family: monospace;
            margin-top: 5px;
            word-break: break-all;
        }

        /* Buttons */
        .btn-primary, .btn-apply {
            background: var(--primary-color);
            color: white;
            border: none;
            padding: 12px;
            border-radius: 8px;
            font-size: 14px;
            font-weight: 600;
            cursor: pointer;
            width: 100%;
            transition: 0.2s;
        }
        .btn-apply { width: auto; padding: 8px 16px; }
        .btn-apply:active { transform: scale(0.95); }
        .btn-pending { background: #ff9800; pointer-events: none; }

        /* Footer Navigation */
        .footer-nav {
            display: flex;
            justify-content: space-around;
            position: fixed;
            bottom: 0;
            width: 100%;
            max-width: 450px;
            background: white;
            border-top: 1px solid #eee;
            padding: 10px 0;
            z-index: 99;
            box-shadow: 0 -2px 10px rgba(0,0,0,0.05);
        }
        .nav-item {
            display: flex;
            flex-direction: column;
            align-items: center;
            color: #999;
            cursor: pointer;
            flex: 1;
            transition: 0.2s;
        }
        .nav-item.active { color: var(--primary-color); }
        .nav-icon { font-size: 20px; margin-bottom: 4px; }
        .nav-text { font-size: 11px; font-weight: 600; }

        /* Page Transitions */
        .page { display: none; animation: fadeIn 0.3s ease; }
        .page.active { display: block; }
        @keyframes fadeIn { from {opacity: 0; transform: translateY(5px);} to {opacity: 1; transform: translateY(0);} }

        /* Status Badge */
        .status-badge {
            padding: 4px 8px;
            border-radius: 4px;
            font-size: 11px;
            font-weight: bold;
        }
        .status-pending { background: #fff3e0; color: #ef6c00; }
        .status-success { background: #e8f5e9; color: #2e7d32; }

    </style>
</head>
<body>

<div class="container">
    
    <!-- HEADER -->
    <div class="header">
        <div class="header-profile-pic">
            <i class="fas fa-user"></i>
        </div>
        <div class="header-user-info">
            <h3 id="username">Guest User</h3>
            <p>ID: <span id="userid">Loading...</span></p>
        </div>
    </div>

    <!-- PAGE 1: DASHBOARD (HOME) -->
    <div id="home-page" class="page active">
        <div class="content">
            <div class="card" style="text-align: center;">
                <span style="color: #666; font-size: 14px;">Total Balance</span>
                <div class="balance-amount">$<span id="balance">12.50</span></div>
                <button class="btn-primary">Withdraw Funds</button>
            </div>

            <div class="card">
                <h3><i class="fas fa-chart-pie"></i> Statistics</h3>
                <div style="display: grid; grid-template-columns: 1fr 1fr; gap: 10px; margin-top: 10px;">
                    <div style="background:#f8f9fa; padding:10px; border-radius:8px; text-align:center;">
                        <div style="font-weight:bold; font-size:18px;">15</div>
                        <div style="font-size:11px; color:#777;">Tasks Done</div>
                    </div>
                    <div style="background:#f8f9fa; padding:10px; border-radius:8px; text-align:center;">
                        <div style="font-weight:bold; font-size:18px;">3</div>
                        <div style="font-size:11px; color:#777;">Pending</div>
                    </div>
                </div>
            </div>
        </div>
    </div>

    <!-- PAGE 2: TASKS (NEW FEATURE) -->
    <div id="tasks-page" class="page">
        <div class="content">
            <h3 style="margin-bottom: 15px;">Available Tasks</h3>
            
            <!-- Gmail Task 1 -->
            <div class="task-item">
                <div class="task-info">
                    <h4>Create New Gmail</h4>
                    <p>Register a fresh Gmail account</p>
                </div>
                <div>
                    <span class="task-reward">+$0.50</span>
                    <br>
                    <button class="btn-apply" onclick="applyTask(this, 'Create Gmail')">Apply</button>
                </div>
            </div>

            <!-- Gmail Task 2 -->
            <div class="task-item">
                <div class="task-info">
                    <h4>Recover Old Gmail</h4>
                    <p>Recover using provided number</p>
                </div>
                <div>
                    <span class="task-reward">+$0.80</span>
                    <br>
                    <button class="btn-apply" onclick="applyTask(this, 'Recover Gmail')">Apply</button>
                </div>
            </div>

        </div>
    </div>

    <!-- PAGE 3: INBOX (NEW FEATURE - Notifications) -->
    <div id="inbox-page" class="page">
        <div class="content">
            <h3 style="margin-bottom: 15px;">Notifications</h3>
            <p style="font-size: 12px; color: #777;">Here you will receive Gmails from Admin.</p>

            <!-- Simulated Admin Reply (Example) -->
            <div class="msg-box">
                <div class="msg-header">
                    <span><i class="fas fa-robot"></i> Admin System</span>
                    <span>10:30 AM</span>
                </div>
                <span class="msg-title">Task Approved!</span>
                <p style="font-size: 13px;">Here is the Gmail details for your requested task:</p>
                <div class="credentials">
                    Email: user_task01@gmail.com<br>
                    Pass: Tr@ck$$99
                </div>
                <div style="margin-top: 10px;">
                    <span class="status-badge status-success">Completed</span>
                </div>
            </div>

            <!-- Dynamic Requests will appear here -->
            <div id="request-list"></div>

        </div>
    </div>

    <!-- FOOTER NAV -->
    <div class="footer-nav">
        <div class="nav-item active" onclick="switchPage('home-page', this)">
            <div class="nav-icon"><i class="fas fa-home"></i></div>
            <div class="nav-text">Home</div>
        </div>
        <div class="nav-item" onclick="switchPage('tasks-page', this)">
            <div class="nav-icon"><i class="fas fa-tasks"></i></div>
            <div class="nav-text">Tasks</div>
        </div>
        <div class="nav-item" onclick="switchPage('inbox-page', this)">
            <div class="nav-icon"><i class="fas fa-envelope"></i></div>
            <div class="nav-text">Inbox</div>
        </div>
    </div>

</div>

<script>
    // Initialize Telegram WebApp
    const tg = window.Telegram.WebApp;
    tg.expand();

    // Set User Data
    if (tg.initDataUnsafe && tg.initDataUnsafe.user) {
        document.getElementById('username').innerText = tg.initDataUnsafe.user.first_name;
        document.getElementById('userid').innerText = tg.initDataUnsafe.user.id;
    }

    // Tab Switching Logic
    function switchPage(pageId, navElement) {
        // Hide all pages
        document.querySelectorAll('.page').forEach(page => page.classList.remove('active'));
        // Show selected page
        document.getElementById(pageId).classList.add('active');

        // Update nav active state
        document.querySelectorAll('.nav-item').forEach(item => item.classList.remove('active'));
        navElement.classList.add('active');
    }

    // Function to Apply for Task
    function applyTask(btn, taskName) {
        // 1. UI Feedback
        btn.innerText = "Pending...";
        btn.classList.add('btn-pending');
        btn.style.backgroundColor = "#ff9800";

        // 2. Simulate sending request to Inbox/Admin
        const requestList = document.getElementById('request-list');
        const time = new Date().toLocaleTimeString([], { hour: '2-digit', minute: '2-digit' });

        const newRequestHTML = `
            <div class="msg-box">
                <div class="msg-header">
                    <span>You</span>
                    <span>${time}</span>
                </div>
                <span class="msg-title">Request: ${taskName}</span>
                <p style="font-size: 13px;">Waiting for Admin to send credentials...</p>
                <div style="margin-top: 10px;">
                    <span class="status-badge status-pending">Processing</span>
                </div>
            </div>
        `;

        requestList.insertAdjacentHTML('afterbegin', newRequestHTML);

        // 3. Notify User
        tg.showPopup({
            title: 'Application Sent',
            message: 'Your request for ' + taskName + ' has been sent to the admin. Check Inbox for updates.',
            buttons: [{type: 'ok'}]
        });

        // NOTE FOR DEVELOPER:
        // In a real app, here you would use fetch() to send data to your bot/server
        // telegram.sendData(JSON.stringify({action: "request_gmail", task: taskName}));
    }

</script>

</body>
</html>
