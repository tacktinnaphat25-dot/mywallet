# mywallet
mywallet
<!DOCTYPE html>
<html lang="th">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>ติณห์ภัทร & นัฐณวรรณ Wallet Pro</title>
    <link href="https://googleapis.com" rel="stylesheet">
    <style>
        :root { --bg: #f4f7fe; --card: #ffffff; --tack: #3b82f6; --noon: #ec4899; --success: #10b981; --danger: #ef4444; --warning: #f59e0b; --dark: #1e293b; }
        body { font-family: 'Kanit', sans-serif; background-color: var(--bg); margin: 0; padding: 10px; color: var(--dark); -webkit-tap-highlight-color: transparent; }
        
        /* Layout */
        .main-wrapper { display: flex; flex-direction: column; gap: 15px; max-width: 1200px; margin: auto; }
        @media (min-width: 1000px) { .main-wrapper { display: grid; grid-template-columns: 380px 1fr; } }

        /* Dashboard */
        .summary-card { background: var(--dark); color: white; padding: 20px; border-radius: 25px; text-align: center; box-shadow: 0 10px 20px rgba(0,0,0,0.15); }
        .balance-grid { display: grid; grid-template-columns: 1fr 1fr; gap: 12px; margin-bottom: 15px; }
        .balance-box { background: rgba(255,255,255,0.05); padding: 15px; border-radius: 20px; border-top: 4px solid var(--tack); }
        .balance-box.noon { border-top-color: var(--noon); }
        .box-label { font-size: 0.75rem; opacity: 0.7; margin-bottom: 5px; display: block; }
        .box-amt { font-size: 1.2rem; font-weight: 600; overflow-wrap: break-word; }
        .total-info { border-top: 1px solid rgba(255,255,255,0.1); padding-top: 15px; display: grid; grid-template-columns: 1fr 1fr; }

        /* Forms - ปรับให้กดง่ายขึ้นบนมือถือ */
        .card { background: var(--card); border-radius: 20px; padding: 20px; box-shadow: 0 4px 6px rgba(0,0,0,0.05); }
        .sec-title { font-size: 1rem; font-weight: 600; margin-bottom: 15px; border-left: 5px solid var(--tack); padding-left: 10px; }
        
        .input-group { margin-bottom: 15px; }
        .input-group label { display: block; font-size: 0.8rem; color: #64748b; margin-bottom: 6px; }
        
        input, select { 
            width: 100%; height: 48px; /* เพิ่มความสูงให้กดง่าย */
            padding: 0 15px; border: 1px solid #e2e8f0; border-radius: 12px; 
            font-size: 1rem; background: #f8fafc; font-family: inherit; 
            box-sizing: border-box; display: block;
        }

        .grid-inputs { display: grid; grid-template-columns: 1fr 1fr; gap: 10px; }

        .btn-save { 
            width: 100%; height: 55px; background: var(--dark); color: white; 
            border: none; border-radius: 15px; font-size: 1.1rem; font-weight: 600; 
            cursor: pointer; margin-top: 10px; -webkit-appearance: none;
        }
        .btn-update { background: var(--warning) !important; color: var(--dark) !important; }

        /* History */
        .history-container { display: flex; flex-direction: column; gap: 15px; }
        @media (min-width: 700px) { .history-container { display: grid; grid-template-columns: 1fr 1fr; } }
        
        .column-header { padding: 12px; border-radius: 15px; margin-bottom: 10px; text-align: center; color: white; font-weight: 600; }
        .header-tack { background: var(--tack); }
        .header-noon { background: var(--noon); }
        .header-save { background: var(--warning); color: var(--dark); }

        .item { 
            background: white; border-radius: 18px; padding: 15px; margin-bottom: 10px; 
            display: flex; justify-content: space-between; align-items: center; 
            box-shadow: 0 2px 5px rgba(0,0,0,0.03); 
        }
        .amt-plus { color: var(--success); font-weight: 600; }
        .amt-minus { color: var(--danger); font-weight: 600; }
        .amt-save { color: var(--warning); font-weight: 600; }
        
        .action-btns { display: flex; gap: 8px; margin-top: 8px; }
        .btn-action { border: none; background: #f1f5f9; padding: 6px 12px; border-radius: 8px; font-size: 0.75rem; color: #64748b; }

        .progress-bg { background: #e2e8f0; height: 10px; border-radius: 5px; overflow: hidden; margin: 10px 0; }
        .progress-fill { height: 100%; transition: 0.5s; width: 0%; }
    </style>
</head>
<body>

<div class="main-wrapper">
    <!-- ฝั่งควบคุมและสรุปผล -->
    <div class="control-side">
        <div class="summary-card">
            <div class="balance-grid">
                <div class="balance-box">
                    <span class="box-label">💵 ติณห์ภัทร</span>
                    <div class="box-amt" id="tackBalance">฿0.00</div>
                </div>
                <div class="balance-box noon">
                    <span class="box-label">💵 นัฐณวรรณ</span>
                    <div class="box-amt" id="noonBalance">฿0.00</div>
                </div>
            </div>
            <div class="total-info">
                <div><span class="box-label">💰 เงินออม</span><div id="totalSavings" style="color:var(--warning)">฿0</div></div>
                <div><span class="box-label">🌎 รวมทั้งหมด</span><div id="netTotal">฿0</div></div>
            </div>
        </div>

        <div class="card">
            <div class="sec-title">งบประมาณเดือนนี้</div>
            <div style="display:flex; justify-content:space-between; align-items:center; margin-bottom:10px;">
                <button onclick="adjustJointBudget(-1000)" style="width:40px; height:40px; border-radius:10px; border:none;">-</button>
                <strong id="jointBudgetTxt" style="font-size:1.2rem">฿0</strong>
                <button onclick="adjustJointBudget(1000)" style="width:40px; height:40px; border-radius:10px; border:none;">+</button>
            </div>
            <div class="progress-bg"><div id="jointProgressBar" class="progress-fill"></div></div>
            <div style="display:flex; justify-content:space-between; font-size:0.8rem;">
                <span id="jointSpentTxt" style="color:var(--danger)">ใช้ไป: ฿0</span>
                <span id="jointRemainingTxt" style="color:var(--success)">คงเหลือ: ฿0</span>
            </div>
        </div>

        <div class="card">
            <div class="sec-title" id="formTitle">บันทึกรายการ</div>
            <input type="hidden" id="editId">
            
            <div class="input-group">
                <label>วันที่-เวลา</label>
                <input type="datetime-local" id="datetime">
            </div>

            <div class="input-group">
                <label>หมวดหมู่</label>
                <select id="desc">
                    <option>ค่าอาหาร</option><option>ค่าน้ำมัน</option><option>ค่ารถ</option>
                    <option>ค่าใช้จ่ายส่วนตัว</option><option>เงินเดือน</option><option>เงินออม (พิเศษ)</option>
                </select>
            </div>

            <div class="grid-inputs">
                <div class="input-group">
                    <label>จำนวนเงิน</label>
                    <input type="number" id="amount" placeholder="0.00" inputmode="decimal">
                </div>
                <div class="input-group">
                    <label>เจ้าของ</label>
                    <select id="owner"><option>ติณห์ภัทร</option><option>นัฐณวรรณ</option></select>
                </div>
            </div>

            <div class="input-group">
                <label>ประเภทรายการ</label>
                <select id="type">
                    <option value="expense">รายจ่าย (-)</option>
                    <option value="income">รายรับ (+)</option>
                    <option value="save_in">➡️ นำเข้าเงินออม</option>
                    <option value="save_out">⬅️ นำออกเงินออม</option>
                </select>
            </div>

            <button class="btn-save" id="saveBtn" onclick="saveTransaction()">บันทึกข้อมูล</button>
            <button id="cancelBtn" onclick="resetForm()" style="display:none; width:100%; background:none; border:none; color:#666; margin-top:15px; text-decoration:underline;">ยกเลิกแก้ไข</button>
        </div>
    </div>

    <!-- ฝั่งประวัติ -->
    <div class="history-side">
        <div class="history-container">
            <div>
                <div class="column-header header-tack">ติณห์ภัทร</div>
                <div id="historyTack"></div>
            </div>
            <div>
                <div class="column-header header-noon">นัฐณวรรณ</div>
                <div id="historyNoon"></div>
            </div>
        </div>
        <div style="margin-top:20px;">
            <div class="column-header header-save">📂 บัญชีเงินออม</div>
            <div id="historySavings"></div>
        </div>
    </div>
</div>

<script>
    let transactions = JSON.parse(localStorage.getItem('walletData')) || [];
    let jointBudget = JSON.parse(localStorage.getItem('jointBudget')) || 20000;
    let isEditing = false;

    // แก้ไขปัญหาการกดบนมือถือ
    function setNow() {
        const now = new Date();
        now.setMinutes(now.getMinutes() - now.getTimezoneOffset());
        document.getElementById('datetime').value = now.toISOString().slice(0, 16);
    }

    function saveTransaction() {
        const desc = document.getElementById('desc').value;
        const amountInput = document.getElementById('amount');
        const amount = parseFloat(amountInput.value);
        const owner = document.getElementById('owner').value;
        const type = document.getElementById('type').value;
        const dt = document.getElementById('datetime').value;
        const editId = document.getElementById('editId').value;

        if (isNaN(amount) || !dt) {
            alert('กรุณากรอกข้อมูลให้ครบถ้วน');
            return;
        }

        if (isEditing) {
            const idx = transactions.findIndex(t => t.id == editId);
            transactions[idx] = { ...transactions[idx], desc, amount, type, owner, date: new Date(dt).toISOString() };
        } else {
            transactions.unshift({ id: Date.now(), desc, amount, type, owner, date: new Date(dt).toISOString() });
        }

        localStorage.setItem('walletData', JSON.stringify(transactions));
        resetForm();
        updateUI();
    }

    function editItem(id) {
        const item = transactions.find(t => t.id == id);
        if (!item) return;
        isEditing = true;
        document.getElementById('editId').value = item.id;
        document.getElementById('desc').value = item.desc;
        document.getElementById('amount').value = item.amount;
        document.getElementById('owner').value = item.owner;
        document.getElementById('type').value = item.type;
        const d = new Date(item.date);
        d.setMinutes(d.getMinutes() - d.getTimezoneOffset());
        document.getElementById('datetime').value = d.toISOString().slice(0, 16);
        
        document.getElementById('formTitle').innerText = "แก้ไขข้อมูล";
        document.getElementById('saveBtn').innerText = "อัปเดตข้อมูล";
        document.getElementById('saveBtn').classList.add('btn-update');
        document.getElementById('cancelBtn').style.display = "block";
        window.scrollTo({ top: 0, behavior: 'smooth' });
    }

    function resetForm() {
        isEditing = false;
        document.getElementById('editId').value = "";
        document.getElementById('amount').value = "";
        document.getElementById('formTitle').innerText = "บันทึกรายการ";
        document.getElementById('saveBtn').innerText = "บันทึกข้อมูล";
        document.getElementById('saveBtn').classList.remove('btn-update');
        document.getElementById('cancelBtn').style.display = "none";
        setNow();
    }

    function deleteItem(id) {
        if(confirm("ลบรายการนี้ใช่ไหม?")) {
            transactions = transactions.filter(t => t.id !== id);
            localStorage.setItem('walletData', JSON.stringify(transactions));
            updateUI();
        }
    }

    function updateUI() {
        const hTack = document.getElementById('historyTack'), hNoon = document.getElementById('historyNoon'), hSave = document.getElementById('historySavings');
        const curM = new Date().getMonth(), curY = new Date().getFullYear();
        let totals = { tack: 0, noon: 0, save: 0, spentM: 0 };
        
        hTack.innerHTML = ''; hNoon.innerHTML = ''; hSave.innerHTML = '';

        transactions.forEach(item => {
            const d = new Date(item.date);
            const isThisMonth = d.getMonth() === curM && d.getFullYear() === curY;
            let val = (item.type==='income'||item.type==='save_out') ? item.amount : -item.amount;

            if (item.type === 'save_in') totals.save += item.amount;
            if (item.type === 'save_out') totals.save -= item.amount;
            if (item.owner === 'ติณห์ภัทร') totals.tack += val; else totals.noon += val;
            if (isThisMonth && item.type === 'expense') totals.spentM += item.amount;

            const div = document.createElement('div');
            div.className = 'item';
            div.innerHTML = `
                <div style="flex:1">
                    <div style="font-weight:600; font-size:0.9rem">${item.desc}</div>
                    <div style="font-size:0.7rem; color:#999">${d.toLocaleDateString('th-TH',{day:'2-digit',month:'short',hour:'2-digit',minute:'2-digit'})}</div>
                </div>
                <div style="text-align:right">
                    <div class="${item.type.startsWith('save')?'amt-save':(val>0?'amt-plus':'amt-minus')}">${val>0?'+':''}${item.amount.toLocaleString()}</div>
                    <div class="action-btns">
                        <button class="btn-action" onclick="editItem(${item.id})">แก้ไข</button>
                        <button class="btn-action" onclick="deleteItem(${item.id})">ลบ</button>
                    </div>
                </div>`;
            
            if (item.type.startsWith('save')) hSave.appendChild(div);
            else if (item.owner === 'ติณห์ภัทร') hTack.appendChild(div);
            else hNoon.appendChild(div);
        });

        document.getElementById('tackBalance').innerText = `฿${totals.tack.toLocaleString()}`;
        document.getElementById('noonBalance').innerText = `฿${totals.noon.toLocaleString()}`;
        document.getElementById('totalSavings').innerText = `฿${totals.save.toLocaleString()}`;
        document.getElementById('netTotal').innerText = `฿${(totals.tack + totals.noon + totals.save).toLocaleString()}`;

        const remain = jointBudget - totals.spentM;
        const p = jointBudget > 0 ? Math.min((totals.spentM/jointBudget)*100, 100) : 0;
        document.getElementById('jointBudgetTxt').innerText = `฿${jointBudget.toLocaleString()}`;
        document.getElementById('jointSpentTxt').innerText = `ใช้ไป: ฿${totals.spentM.toLocaleString()}`;
        document.getElementById('jointRemainingTxt').innerText = `คงเหลือ: ฿${remain.toLocaleString()}`;
        document.getElementById('jointProgressBar').style.width = p + '%';
    }

    function adjustJointBudget(v) { jointBudget=Math.max(0, jointBudget+v); localStorage.setItem('jointBudget', jointBudget); updateUI(); }
    
    // ระบบกดปุ่ม Enter บนมือถือ
    document.addEventListener('keypress', (e) => {
        if(e.key === 'Enter') {
            if(document.activeElement.id === 'amount' || document.activeElement.tagName === 'SELECT') {
                saveTransaction();
            }
        }
    });

    setNow();
    updateUI();
</script>
</body>
</html>
