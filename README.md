<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>Earn Money & Tasks</title>
    <!-- Telegram Web App SDK -->
    <script src="https://telegram.org/js/telegram-web-app.js"></script>
    <!-- FontAwesome Icons -->
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">

    <style>
        @import url('https://fonts.googleapis.com/css2?family=Poppins:wght@300;400;600;700&display=swap');

        :root {
            /* আকর্ষণীয় কালার গ্রেডিয়েন্ট */
            --primary-gradient: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
            --card-bg: #ffffff;
            --bg-color: #f0f2f5;
            --text-dark: #2d3748;
            --text-light: #718096;
            --success: #48bb78;
            --danger: #f56565;
        }

        body {
            font-family: 'Poppins', sans-serif;
            background-color: var(--bg-color);
            margin: 0;
            padding-bottom: 80px;
            color: var(--text-dark);
            -webkit-tap-highlight-color: transparent;
        }

        /* হেডার ডিজাইন */
        .header {
            background: var(--primary-gradient);
            padding: 20px 20px 45px 20px;
            border-radius: 0 0 30px 30px;
            color: white;
            box-shadow: 0 4px 15px rgba(118, 75, 162, 0.3);
            display: flex;
            align-items: center;
        }

        .profile-img {
            width: 50px;
            height: 50px;
            background: rgba(255,255,255,0.2);
            border-radius: 50%;
            display: flex;
            align-items: center;
            justify-content: center;
            font-size: 24px;
            border: 2px solid rgba(255,255,255,0.5);
            margin-right: 15px;
        }

        .user-details h2 { margin: 0; font-size: 18px; font-weight: 600; }
        .user-details p { margin: 0; font-size: 12px; opacity: 0.9; }

        /* ব্যালেন্স কার্ড */
        .balance-card {
            background: var(--card-bg);
            margin: -30px 20px 20px 20px;
            padding: 20px;
            border-radius: 20px;
            box-shadow: 0 10px 25px rgba(0,0,0,0.08);
            text-align: center;
            position: relative;
        }

        .balance-title { font-size: 13px; color: var(--text-light); text-transform: uppercase; letter-spacing: 1px; }
        .balance-amount { 
            font-size: 38px; 
            font-weight: 700; 
            color: #764ba2; 
            margin: 5px 0;
        }
        .currency { font-size: 22px; color: #a0aec0; }

        /* কন্টেইনার */
        .container { padding: 0 20px; animation: fadeIn 0.4s ease; }

        /* টাস্ক কার্ড */
        .task-card {
            background: var(--card-bg);
            border-radius: 16px;
            padding: 15px;
            margin-bottom: 15px;
            box-shadow: 0 4px 6px rgba(0,0,0,0.03);
            display: flex;
            justify-content: space-between;
            align-items: center;
            border-left: 5px solid #764ba2;
            transition: transform 0.2s;
        }
        .task-card:active { transform: scale(0.98); }

        .task-icon {
            width: 42px; height: 42px;
            background: #f3e8ff;
            color: #764ba2;
            border-radius: 12px;
            display: flex; align-items: center; justify-content: center;
            margin-right: 12px;
            font-size: 18px;
        }
        .task-content h4 { margin: 0; font-size: 15px; color: var(--text-dark); }
        .task-content p { margin: 2px 0 0; font-size: 11px; color: var(--text-light); }
        
        .btn-action {
            background: var(--primary-gradient);
            color: white;
            border: none;
            padding: 8px 18px;
            border-radius: 8px;
            font-size: 12px;
            font-weight: 600;
            cursor: pointer;
            box-shadow: 0 2px 5px rgba(118, 75, 162, 0.2);
        }

        /* উইথড্র ফর্ম */
        .withdraw-box {
            background: white;
            padding: 25px;
            border-radius: 20px;
            box-shadow: 0 5px 15px rgba(0,0,0,0.05);
        }
        .form-group { margin-bottom: 15px; }
        .form-label { display: block; font-size: 13px; color: var(--text-dark); margin-bottom: 8px; font-weight: 600; }
        .form-control {
            width: 100%;
            padding: 14px;
            border: 1px solid #e2e8f0;
            border-radius: 12px;
            font-size: 15px;
            background: #f8fafc;
            box-sizing: border-box;
            font-family: inherit;
            transition: 0.3s;
        }
        .form-control:focus { outline: none; border-color: #764ba2; background: white; }
        select.form-control { appearance: none; }

        .btn-withdraw {
            width: 100%;
            padding: 16px;
            background: var(--success);
            color: white;
            border: none;
            border-radius: 12px;
            font-size: 16px;
            font-weight: 600;
            cursor: pointer;
            margin-top: 10px;
            box-shadow: 0 4px 10px rgba(72, 187, 120, 0.3);
            display: flex;
            align-items: center;
            justify-content: center;
            gap: 10px;
        }
        .btn-withdraw:active { transform: scale(0.98); }

        /* নিচেন নেভিগেশন */
        .bottom-nav {
            position: fixed;
            bottom: 0; left: 0; right: 0;
            background: white;
            padding: 12px 20px;
            display: flex;
            justify-content: space-around;
            border-radius: 25px 25px 0 0;
            box-shadow: 0 -5px 20px rgba(0,0,0,0.05);
            z-index: 100;
        }
        .nav-item {
            text-align: center;
            color: #a0aec0;
            cursor: pointer;
            transition: 0.3s;
            flex: 1;
        }
        .nav-item.active { color: #764ba2; transform: translateY(-5px); }
        .nav-icon { font-size: 20px; margin-bottom: 4px; display: block; }
        .nav-text { font-size: 11px; font-weight: 600; }

        /* পেজ ট্রানজিশন */
        .page { display: none; }
        .page.active { display: block; }
        @keyframes fadeIn { from {opacity:0; transform: translateY(10px);} to {opacity:1; transform: translateY(0);} }

        /* নোটিফিকেশন টোস্ট */
        .toast {
            position: fixed; top: 20px; left: 50%; transform: translateX(-50%);
            background: #333; color: white;
            padding: 10px 20px; border-radius: 30px;
            font-size: 12px; z-index: 9999; display: none;
            box-shadow: 0 5px 15px rgba(0,0,0,0.2);
        }
    </style>
</head>
<body>

    <!-- কনফিগারেশন এরিয়া (আপনার দেওয়া তথ্য বসানো হয়েছে) -->
    <script>
        const CONFIG = {
            BOT_TOKEN: '8439942678:AAEUHHv3iH0BCiX0qoPr-xU11mNtx0fKwtc', // আপনার দেওয়া বট টোকেন
            ADMIN_ID: '8188773875', // আপনার দেওয়া অ্যাডমিন আইডি
            CURRENCY: '৳'
        };
    </script>

    <div id="toast" class="toast"><i class="fas fa-check-circle"></i> Request Sent!</div>

    <!-- HEADER -->
    <div class="header">
        <div class="profile-img"><i class="fas fa-user-astronaut"></i></div>
        <div class="user-details">
            <h2 id="user_name">Guest</h2>
            <p>ID: <span id="user_id">Checking...</span></p>
        </div>
    </div>

    <!-- BALANCE CARD -->
    <div class="balance-card">
        <div class="balance-title">TOTAL EARNINGS</div>
        <div class="balance-amount"><span class="currency">৳</span><span id="balance">150.00</span></div>
        <p style="font-size: 11px; color: #718096; margin: 0; display: flex; align-items: center; justify-content: center; gap: 5px;">
            <i class="fas fa-wallet" style="color: #48bb78;"></i> Min Withdraw: ৳100
        </p>
    </div>

    <!-- PAGE 1: TASKS (হোম পেজ) -->
    <div id="page-tasks" class="page container active">
        <div style="display: flex; justify-content: space-between; align-items: center; margin: 10px 0 15px;">
            <h3 style="font-size: 16px; margin: 0;">Task List</h3>
            <span style="font-size: 12px; color: #764ba2; background: #f3e8ff; padding: 4px 8px; border-radius: 10px;">Live</span>
        </div>
        
        <!-- Gmail Task 1 -->
        <div class="task-card">
            <div style="display: flex; align-items: center;">
                <div class="task-icon"><i class="fab fa-google"></i></div>
                <div class="task-content">
                    <h4>Create New Gmail</h4>
                    <p>Reward: <span style="color: #48bb78; font-weight: bold;">৳15.00</span></p>
                </div>
            </div>
            <button class="btn-action" onclick="applyTask('Create New Gmail', 15)">Start</button>
        </div>

        <!-- Gmail Task 2 -->
        <div class="task-card">
            <div style="display: flex; align-items: center;">
                <div class="task-icon"><i class="fas fa-history"></i></div>
                <div class="task-content">
                    <h4>Recover Old Gmail</h4>
                    <p>Reward: <span style="color: #48bb78; font-weight: bold;">৳20.00</span></p>
                </div>
            </div>
            <button class="btn-action" onclick="applyTask('Recover Old Gmail', 20)">Start</button>
        </div>

        <!-- Gmail Task 3 -->
        <div class="task-card">
            <div style="display: flex; align-items: center;">
                <div class="task-icon"><i class="fas fa-envelope-open-text"></i></div>
                <div class="task-content">
                    <h4>Fresh USA Gmail</h4>
                    <p>Reward: <span style="color: #48bb78; font-weight: bold;">৳25.00</span></p>
                </div>
            </div>
            <button class="btn-action" onclick="applyTask('USA Gmail', 25)">Start</button>
        </div>
        
        <!-- Instruction Box -->
        <div style="background: linear-gradient(to right, #ebf8ff, #fff); padding: 15px; border-radius: 12px; border: 1px solid #bee3f8; margin-top: 20px; display: flex; gap: 10px;">
            <i class="fas fa-info-circle" style="color: #3182ce; font-size: 20px; margin-top: 2px;"></i>
            <div>
                <p style="margin: 0; font-size: 12px; color: #2b6cb0; font-weight: 600;">কিভাবে কাজ করবেন?</p>
                <p style="margin: 5px 0 0; font-size: 11px; color: #4a5568;">
                    Start বাটনে ক্লিক করুন। অ্যাডমিন আপনার টেলিগ্রাম ইনবক্সে জিমেইল ডিটেইলস পাঠাবেন। কাজ শেষে জমা দিন।
                </p>
            </div>
        </div>
    </div>

    <!-- PAGE 2: WITHDRAW (টাকা তোলা) -->
    <div id="page-withdraw" class="page container">
        <h3 style="margin: 10px 0 15px; font-size: 16px;">Withdraw Money</h3>
        
        <div class="withdraw-box">
            <div class="form-group">
                <label class="form-label">Payment Method</label>
                <div style="position: relative;">
                    <select id="wd_method" class="form-control">
                        <option value="bKash">bKash (বিকাশ)</option>
                        <option value="Nagad">Nagad (নগদ)</option>
                        <option value="Rocket">Rocket (রকেট)</option>
                        <option value="Upay">Upay (উপায়)</option>
                    </select>
                    <i class="fas fa-chevron-down" style="position: absolute; right: 15px; top: 18px; color: #a0aec0; pointer-events: none;"></i>
                </div>
            </div>

            <div class="form-group">
                <label class="form-label">Wallet Number</label>
                <input type="tel" id="wd_number" class="form-control" placeholder="017xxxxxxxx">
            </div>

            <div class="form-group">
                <label class="form-label">Amount (Min ৳100)</label>
                <input type="number" id="wd_amount" class="form-control" placeholder="Example: 150">
            </div>

            <button class="btn-withdraw" onclick="submitWithdraw()">
                <span>Confirm Request</span>
                <i class="fas fa-paper-plane"></i>
            </button>
        </div>
    </div>

    <!-- BOTTOM NAV -->
    <div class="bottom-nav">
        <div class="nav-item active" onclick="switchTab('page-tasks', this)">
            <i class="nav-icon fas fa-tasks"></i>
            <span class="nav-text">Tasks</span>
        </div>
        <div class="nav-item" onclick="switchTab('page-withdraw', this)">
            <i class="nav-icon fas fa-wallet"></i>
            <span class="nav-text">Withdraw</span>
        </div>
    </div>

    <script>
        // --- টেলিগ্রাম সেটআপ ---
        const tg = window.Telegram.WebApp;
        tg.expand(); // অ্যাপ ফুল স্ক্রিন করার জন্য

        // ইউজারের তথ্য নেওয়া
        let user = {
            id: 'Unknown',
            first_name: 'Guest',
            username: 'N/A'
        };

        if (tg.initDataUnsafe && tg.initDataUnsafe.user) {
            user = tg.initDataUnsafe.user;
            document.getElementById('user_name').innerText = user.first_name;
            document.getElementById('user_id').innerText = user.id;
        }

        // --- মেইন ফাংশন ---

        // ১. অ্যাডমিনের কাছে মেসেজ পাঠানোর ফাংশন
        function sendToAdmin(message) {
            const url = `https://api.telegram.org/bot${CONFIG.BOT_TOKEN}/sendMessage`;
            
            const payload = {
                chat_id: CONFIG.ADMIN_ID,
                text: message,
                parse_mode: 'HTML'
            };

            fetch(url, {
                method: 'POST',
                headers: {'Content-Type': 'application/json'},
                body: JSON.stringify(payload)
            })
            .then(response => response.json())
            .then(data => {
                if(data.ok) {
                    showToast("Success! Admin Notified.");
                } else {
                    console.error(data);
                    showToast("Error sending request.");
                }
            })
            .catch(error => {
                console.error('Error:', error);
                showToast("Check Internet Connection");
            });
        }

        // ২. টাস্ক অ্যাপ্লাই ফাংশন
        function applyTask(taskName, price) {
            // কনফার্মেশন পপ-আপ
            tg.showPopup({
                title: 'Confirm Task',
                message: `Do you want to start "${taskName}" for ৳${price}?`,
                buttons: [
                    {id: 'cancel', type: 'cancel'},
                    {id: 'ok', type: 'ok'}
                ]
            }, function(btnId) {
                if (btnId === 'ok') {
                    // মেসেজ ফরম্যাট
                    const msg = `
<b>🚀 NEW TASK REQUEST</b>
➖➖➖➖➖➖➖➖➖➖
👤 <b>Name:</b> ${user.first_name}
🆔 <b>User ID:</b> <code>${user.id}</code>
📝 <b>Task:</b> ${taskName}
💰 <b>Reward:</b> ৳${price}
➖➖➖➖➖➖➖➖➖➖
<i>Tip: Reply to this user with the Gmail & Password.</i>
`;
                    sendToAdmin(msg);
                }
            });
        }

        // ৩. উইথড্র ফাংশন
        function submitWithdraw() {
            const method = document.getElementById('wd_method').value;
            const number = document.getElementById('wd_number').value;
            const amount = parseFloat(document.getElementById('wd_amount').value);
            const currentBalance = parseFloat(document.getElementById('balance').innerText);

            // ভ্যালিডেশন (চেক করা সব ঠিক আছে কিনা)
            if (!number || number.length < 11) {
                tg.showAlert("Please enter a valid 11 digit mobile number.");
                return;
            }
            if (!amount || amount < 100) {
                tg.showAlert("Minimum withdrawal amount is 100 Taka.");
                return;
            }
            if (amount > currentBalance) {
                tg.showAlert("Insufficient Balance! You don't have enough money.");
                return;
            }

            // মেসেজ রেডি করা
            const msg = `
<b>💸 WITHDRAWAL REQUEST</b>
➖➖➖➖➖➖➖➖➖➖
👤 <b>Name:</b> ${user.first_name}
🆔 <b>User ID:</b> <code>${user.id}</code>
🏦 <b>Method:</b> ${method}
📱 <b>Number:</b> <code>${number}</code>
💰 <b>Amount:</b> ৳${amount}
➖➖➖➖➖➖➖➖➖➖
<i>Please check balance and send money manually.</i>
`;
            
            // অ্যাডমিনকে পাঠানো
            sendToAdmin(msg);

            // ব্যালেন্স কমানো (লোকালি)
            document.getElementById('balance').innerText = (currentBalance - amount).toFixed(2);
            
            // সাকসেস মেসেজ
            tg.showPopup({
                title: 'Request Submitted!',
                message: `Withdrawal of ৳${amount} via ${method} is pending approval.`,
                buttons: [{type: 'ok'}]
            });
            
            // ইনপুট খালি করা
            document.getElementById('wd_number').value = '';
            document.getElementById('wd_amount').value = '';
        }

        // --- ডিজাইন ফাংশন ---
        function switchTab(pageId, btn) {
            // সব পেজ লুকানো
            document.querySelectorAll('.page').forEach(p => p.classList.remove('active'));
            // সিলেক্ট করা পেজ দেখানো
            document.getElementById(pageId).classList.add('active');
            
            // সব বাটন থেকে এক্টিভ কালার সরানো
            document.querySelectorAll('.nav-item').forEach(n => n.classList.remove('active'));
            // বর্তমান বাটনে কালার দেওয়া
            btn.classList.add('active');
        }

        function showToast(text) {
            const toast = document.getElementById('toast');
            toast.innerHTML = `<i class="fas fa-bell"></i> ${text}`;
            toast.style.display = 'block';
            setTimeout(() => toast.style.display = 'none', 3000);
        }

    </script>
</body>
</html>
