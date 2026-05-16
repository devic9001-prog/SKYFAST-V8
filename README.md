<!DOCTYPE html>
<html lang="ar" dir="rtl">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>نظام إدارة Prince Office - نسخة ربط NBTEL</title>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.0.0/css/all.min.css">
    <style>
        :root {
            --primary: #00d2ff; --success: #4ade80; --danger: #ff4d4d; --warning: #fbbf24;
            --card-bg: rgba(30, 41, 59, 0.7);
        }
        * { margin: 0; padding: 0; box-sizing: border-box; font-family: 'Segoe UI', Tahoma, sans-serif; }
        body { background: radial-gradient(circle at top right, #1e293b, #0f172a); color: #f8fafc; min-height: 100vh; display: flex; justify-content: center; align-items: center; }
        #admin-panel { display: none; width: 100%; max-width: 1400px; margin: 20px; align-self: flex-start; }
        header { display: flex; justify-content: space-between; align-items: center; margin-bottom: 25px; padding: 15px 20px; background: var(--card-bg); border-radius: 12px; }
        .office-name { cursor: pointer; padding: 5px 10px; border-radius: 8px; font-weight: bold; }
        .stats-grid, .packages-grid { display: grid; grid-template-columns: repeat(auto-fit, minmax(180px, 1fr)); gap: 15px; margin-bottom: 20px; }
        .stat-card { background: var(--card-bg); padding: 15px; border-radius: 16px; text-align: center; border: 1px solid rgba(255,255,255,0.1); }
        .content-grid { display: grid; grid-template-columns: 350px 1fr; gap: 20px; }
        .card { background: var(--card-bg); backdrop-filter: blur(10px); padding: 20px; border-radius: 16px; border: 1px solid rgba(255,255,255,0.1); }
        .input-group { margin-bottom: 15px; text-align: right; }
        input, select { width: 100%; padding: 10px; border-radius: 8px; border: 1px solid rgba(255,255,255,0.1); background: rgba(15, 23, 42, 0.5); color: white; outline: none; }
        .btn-main { width: 100%; padding: 12px; border: none; border-radius: 8px; background: var(--primary); color: #0f172a; font-weight: bold; cursor: pointer; margin-top: 5px; }
        .btn-action { border: none; padding: 6px 10px; border-radius: 5px; cursor: pointer; font-weight: bold; font-size: 0.75rem; margin-left: 2px; }
        .btn-pay { background: var(--success); color: #0f172a; }
        .btn-renew { background: var(--primary); color: #0f172a; }
        .btn-edit { background: var(--warning); color: #0f172a; }
        .btn-archive { background: #64748b; color: white; }
        .btn-delete { color: var(--danger); background: none; border: none; cursor: pointer; font-size: 1.1rem; }
        table { width: 100%; border-collapse: collapse; }
        th { background: rgba(255,255,255,0.05); padding: 12px; text-align: right; color: var(--primary); font-size: 0.85rem; }
        td { padding: 12px; border-bottom: 1px solid rgba(255,255,255,0.05); font-size: 0.85rem; }
        .paid-info { font-size: 0.7rem; color: var(--success); display: block; margin-top: 4px; }
        .status-expired { border-right: 5px solid var(--danger); background: rgba(255, 77, 77, 0.1); }
        .status-warning { border-right: 5px solid var(--warning); background: rgba(251, 191, 36, 0.1); }
        #login-page { width: 100%; display: flex; justify-content: center; }
        .login-card { background: var(--card-bg); backdrop-filter: blur(15px); padding: 40px; border-radius: 20px; border: 1px solid rgba(255,255,255,0.1); width: 380px; text-align: center; }
    </style>
</head>
<body>

    <div id="login-page">
        <div class="login-card">
            <h2 style="margin-bottom: 30px; color: var(--primary);">SKYFAST - NBTEL</h2>
            <input type="text" id="user-input" value="ameer" placeholder="اسم المستخدم" style="margin-bottom:15px">
            <input type="password" id="pass-input" value="sky708090" placeholder="كلمة المرور" style="margin-bottom:20px">
            <button class="btn-main" onclick="checkLogin()">دخول للنظام</button>
        </div>
    </div>

    <div id="admin-panel">
        <header>
            <div>
                <h2 class="office-name" id="displayOfficeName" onclick="editOfficeName()"></h2>
                <select id="monthFilter" onchange="sync()"></select>
            </div>
            <div style="display: flex; gap: 8px;">
                <button onclick="archiveAllPaid()" class="btn-action btn-archive"><i class="fas fa-archive"></i> أرشفة المسدد</button>
                <button onclick="exportToCSV()" style="background: #107c41; color: white; padding: 8px 15px; border-radius: 8px; border:none; cursor:pointer;"><i class="fas fa-file-excel"></i> تصدير</button>
                <button onclick="location.reload()" style="background: var(--danger); color: white; padding: 8px 15px; border-radius: 8px; border:none; cursor:pointer;">خروج</button>
            </div>
        </header>

        <div class="stats-grid">
            <div class="stat-card"><label>إجمالي الشهر</label><div id="totalSum">0</div></div>
            <div class="stat-card"><label>المسدد</label><div id="paidSum" style="color: var(--success);">0</div></div>
            <div class="stat-card"><label>المتبقي</label><div id="debtSum" style="color: var(--danger);">0</div></div>
            <div class="stat-card"><label>المشتركين</label><div id="subCount">0</div></div>
        </div>

        <div class="packages-grid">
            <div class="stat-card"><label>NB1</label><div id="cnt-NB1">0</div></div>
            <div class="stat-card"><label>NBMAX</label><div id="cnt-NBMAX">0</div></div>
            <div class="stat-card"><label>NB2</label><div id="cnt-NB2">0</div></div>
            <div class="stat-card"><label>NB3</label><div id="cnt-NB3">0</div></div>
        </div>

        <div class="content-grid">
            <aside class="card">
                <h3 id="formTitle" style="margin-bottom: 20px; color: var(--primary);">إضافة مشترك</h3>
                <form id="mainForm">
                    <input type="hidden" id="editIndex" value="-1">
                    <div class="input-group"><label>التسلسل</label><input type="number" id="s_id" required></div>
                    <div class="input-group"><label>اسم المشترك</label><input type="text" id="s_name" required></div>
                    <div class="input-group"><label>نوع الاشتراك</label>
                        <select id="s_type" onchange="updatePriceDisplay()" required>
                            <option value="">اختر...</option>
                            <option value="NB1">NB1</option><option value="NBMAX">NBMAX</option>
                            <option value="NB2">NB2</option><option value="NB3">NB3</option>
                        </select>
                    </div>
                    <div class="input-group"><label>السعر</label><input type="text" id="s_price" readonly></div>
                    <div class="input-group"><label>تاريخ التفعيل</label><input type="date" id="s_start" required></div>
                    <div class="input-group"><label>تاريخ الانتهاء</label><input type="date" id="s_end" required></div>
                    <button type="button" id="saveBtn" onclick="saveData()" class="btn-main">حفظ البيانات</button>
                    <button type="button" id="cancelEditBtn" onclick="resetForm()" class="btn-main" style="display:none; background:#64748b">إلغاء التعديل</button>
                </form>
            </aside>

            <main class="card">
                <input type="text" id="searchBar" onkeyup="renderTable()" placeholder="بحث بالاسم أو التسلسل..." style="margin-bottom: 15px;">
                <div style="overflow-x: auto;">
                    <table>
                        <thead>
                            <tr><th>ت</th><th>الاسم</th><th>النوع</th><th>الانتهاء</th><th>السعر</th><th>الإجراءات</th><th>حذف</th></tr>
                        </thead>
                        <tbody id="tableBody"></tbody>
                    </table>
                </div>
            </main>
        </div>
    </div>

<script>
    const prices = { "NB1": 25000, "NBMAX": 35000, "NB2": 45000, "NB3": 55000 };
    let subscribers = JSON.parse(localStorage.getItem('prince_data_v6')) || [];
    let archive = JSON.parse(localStorage.getItem('prince_archive_v6')) || [];
    let officeName = localStorage.getItem('office_name') || "إدارة مكتب الأمير";

    // دالة استقبال البيانات التلقائية القادمة من منظومة NBTEL
    function checkIncomingData() {
        const urlParams = new URLSearchParams(window.location.search);
        if (urlParams.has('nbtel_user')) {
            const user = urlParams.get('nbtel_user');
            let rawPackage = urlParams.get('nbtel_pkg') || '';
            let expDate = urlParams.get('nbtel_exp') || '';

            // تحويل اسم الباقة ليتطابق مع نظامك (مثال: NB MAX-MSL يصبح NBMAX)
            let finalPackage = "NB1";
            if(rawPackage.includes("MAX")) finalPackage = "NBMAX";
            else if(rawPackage.includes("2")) finalPackage = "NB2";
            else if(rawPackage.includes("3")) finalPackage = "NB3";

            // تنسيق التاريخ القادم من المنظومة ليتناسب مع حقل التاريخ
            let startDate = new Date().toISOString().split('T')[0];
            let endDate = expDate ? expDate.split(' ')[0] : '';

            const currentMonth = document.getElementById('monthFilter').value;
            
            // منع التكرار في نفس الشهر
            const exists = subscribers.some(s => s.name === user && s.month === currentMonth);
            if (!exists) {
                subscribers.push({
                    id: (subscribers.length + 1).toString(),
                    name: user,
                    type: finalPackage,
                    price: prices[finalPackage],
                    start: startDate,
                    end: endDate,
                    isPaid: true,
                    paidAt: "تفعيل كارت NBTEL",
                    month: currentMonth
                });
                localStorage.setItem('prince_data_v6', JSON.stringify(subscribers));
                alert(`تم سحب المشترك (${user}) بنجاح وتأكيد تفعيله باقة ${finalPackage}`);
                // مسح البيانات من الرابط للحفاظ على نظافة الصفحة
                window.history.replaceState({}, document.title, window.location.pathname);
            }
        }
    }

    document.getElementById('s_start').addEventListener('change', function() {
        if(this.value) {
            const d = new Date(this.value); d.setDate(d.getDate() + 30);
            document.getElementById('s_end').value = d.toISOString().split('T')[0];
        }
    });

    function checkLogin() {
        if(document.getElementById('user-input').value === "ameer" && document.getElementById('pass-input').value === "sky708090") {
            document.getElementById('login-page').style.display = 'none';
            document.getElementById('admin-panel').style.display = 'block';
            document.getElementById('displayOfficeName').innerHTML = `<i class="fas fa-edit"></i> ${officeName}`;
            setupMonthFilter(); sync(); checkIncomingData();
        } else alert("خطأ!");
    }

    function setupMonthFilter() {
        const filter = document.getElementById('monthFilter');
        const now = new Date();
        for(let i = -3; i <= 3; i++) {
            let d = new Date(now.getFullYear(), now.getMonth() + i, 1);
            let opt = new Option(`${d.getMonth()+1}-${d.getFullYear()}`, `${d.getFullYear()}-${d.getMonth()+1}`);
            if(i === 0) opt.selected = true;
            filter.add(opt);
        }
    }

    function saveData() {
        const type = document.getElementById('s_type').value;
        const editIdx = parseInt(document.getElementById('editIndex').value);
        const sub = {
            id: document.getElementById('s_id').value,
            name: document.getElementById('s_name').value,
            type: type, price: prices[type],
            start: document.getElementById('s_start').value,
            end: document.getElementById('s_end').value,
            isPaid: editIdx !== -1 ? subscribers[editIdx].isPaid : false,
            paidAt: editIdx !== -1 ? subscribers[editIdx].paidAt : null,
            month: document.getElementById('monthFilter').value
        };
        if(editIdx === -1) subscribers.push(sub); else subscribers[editIdx] = sub;
        sync(); resetForm();
    }

    function pay(index) {
        const now = new Date();
        const dateStr = now.toLocaleDateString('ar-IQ') + " " + now.toLocaleTimeString('ar-IQ', {hour: '2-digit', minute:'2-digit'});
        subscribers[index].isPaid = true;
        subscribers[index].paidAt = dateStr;
        sync();
    }

    function renderTable() {
        const term = document.getElementById('searchBar').value.toLowerCase();
        const monthKey = document.getElementById('monthFilter').value;
        const tbody = document.getElementById('tableBody');
        tbody.innerHTML = '';

        subscribers.filter(s => s.month === monthKey && (s.name.toLowerCase().includes(term) || s.id.includes(term)))
        .forEach((s) => {
            const realIdx = subscribers.indexOf(s);
            const daysLeft = Math.ceil((new Date(s.end) - new Date()) / (1000*60*60*24));
            const row = document.createElement('tr');
            if(daysLeft < 0) row.className = 'status-expired'; else if(daysLeft <= 3) row.className = 'status-warning';

            row.innerHTML = `
                <td>${s.id}</td>
                <td style="font-weight:bold">${s.name}</td>
                <td>${s.type}</td>
                <td>${s.end}</td>
                <td>${s.price.toLocaleString()}</td>
                <td>
                    ${s.isPaid ? `<span style="color:var(--success); font-weight:bold;">مسدد ✔</span><span class="paid-info">${s.paidAt}</span>` : `<button class="btn-action btn-pay" onclick="pay(${realIdx})">دفع</button>`}
                    <button class="btn-action btn-renew" onclick="renew(${realIdx})">تجديد</button>
                    <button class="btn-action btn-edit" onclick="editSub(${realIdx})"><i class="fas fa-edit"></i></button>
                </td>
                <td><button class="btn-delete" onclick="remove(${realIdx})"><i class="fas fa-trash"></i></button></td>
            `;
            tbody.appendChild(row);
        });
    }

    function sync() {
        localStorage.setItem('prince_data_v6', JSON.stringify(subscribers));
        localStorage.setItem('prince_archive_v6', JSON.stringify(archive));
        renderTable(); updateStats();
    }

    function editSub(index) {
        const s = subscribers[index]; document.getElementById('editIndex').value = index;
        document.getElementById('s_id').value = s.id; document.getElementById('s_name').value = s.name;
        document.getElementById('s_type').value = s.type; document.getElementById('s_start').value = s.start;
        document.getElementById('s_end').value = s.end; updatePriceDisplay();
        document.getElementById('formTitle').innerText = "تعديل مشترك"; document.getElementById('saveBtn').innerText = "تحديث";
        document.getElementById('cancelEditBtn').style.display = "block";
    }
    function resetForm() { document.getElementById('mainForm').reset(); document.getElementById('editIndex').value = "-1"; document.getElementById('formTitle').innerText = "إضافة مشترك"; document.getElementById('saveBtn').innerText = "حفظ"; document.getElementById('cancelEditBtn').style.display = "none"; }
    function updatePriceDisplay() { const t = document.getElementById('s_type').value; if(t) document.getElementById('s_price').value = prices[t].toLocaleString() + " د.ع"; }
    function remove(i) { if(confirm("حذف؟")) { subscribers.splice(i, 1); sync(); } }
    function editOfficeName() { const n = prompt("الاسم الجديد:", officeName); if(n) { officeName = n; localStorage.setItem('office_name', n); document.getElementById('displayOfficeName').innerHTML = `<i class="fas fa-edit"></i> ${officeName}`; } }
    function renew(i) { const old = subscribers[i]; const d = new Date(old.end); d.setDate(d.getDate() + 30); const nextM = new Date(old.end); nextM.setMonth(nextM.getMonth() + 1); subscribers.push({...old, start: old.end, end: d.toISOString().split('T')[0], isPaid: false, paidAt: null, month: `${nextM.getFullYear()}-${nextM.getMonth()+1}`}); alert("تم التجديد!"); sync(); }
    function updateStats() {
        const m = document.getElementById('monthFilter').value; const cur = subscribers.filter(s => s.month === m);
        const total = cur.reduce((a, b) => a + b.price, 0); const paid = cur.filter(s => s.isPaid).reduce((a, b) => a + b.price, 0);
        document.getElementById('totalSum').innerText = total.toLocaleString() + " د.ع"; document.getElementById('paidSum').innerText = paid.toLocaleString() + " د.ع";
        document.getElementById('debtSum').innerText = (total - paid).toLocaleString() + " د.ع"; document.getElementById('subCount').innerText = cur.length;
        const counts = { "NB1": 0, "NBMAX": 0, "NB2": 0, "NB3": 0 }; cur.forEach(s => { if(counts.hasOwnProperty(s.type)) counts[s.type]++; });
        Object.keys(counts).forEach(k => document.getElementById('cnt-'+k).innerText = counts[k]);
    }
    function archiveAllPaid() { const m = document.getElementById('monthFilter').value; if(!confirm("أرشفة المسددين؟")) return; exportToCSV(); archive.push(...subscribers.filter(s => s.month === m && s.isPaid)); subscribers = subscribers.filter(s => !(s.month === m && s.isPaid)); sync(); }
    function exportToCSV() {
        const m = document.getElementById('monthFilter').value; let csv = "\ufeffت,الاسم,النوع,الانتهاء,السعر,المسدد,وقت التسديد\n";
        subscribers.filter(s => s.month === m).forEach(s => { csv += `${s.id},${s.name},${s.type},${s.end},${s.price},${s.isPaid?'نعم':'لا'},${s.paidAt||''}\n`; });
        const blob = new Blob([csv], { type: 'text/csv;charset=utf-8;' }); const link = document.createElement("a"); link.href = URL.createObjectURL(blob); link.download = `حسابات_${officeName}_${m}.csv`; link.click();
    }
</script>
</body>
</html>
