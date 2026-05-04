```html
<!DOCTYPE html>
<html lang="ar" dir="rtl">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>نظام الجرد القضائي - مصرف الأنبار</title>
    <!-- Tailwind CSS for modern UI -->
    <script src="https://cdn.tailwindcss.com"></script>
    <!-- Cairo Font from Google -->
    <link href="https://fonts.googleapis.com/css2?family=Cairo:wght@400;600;700;900&display=swap" rel="stylesheet">
    <!-- Font Awesome Icons -->
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <style>
        body { font-family: 'Cairo', sans-serif; background-color: #f1f5f9; }
        .glass-header { background: linear-gradient(135deg, #0f172a 0%, #1e293b 100%); }
        .case-card { transition: all 0.25s cubic-bezier(0.4, 0, 0.2, 1); border-radius: 1.5rem; }
        .case-card:active { transform: scale(0.97); }
        .status-badge { font-size: 10px; padding: 2px 10px; border-radius: 20px; font-weight: 800; }
        .no-scrollbar::-webkit-scrollbar { display: none; }
        @media print { .no-print { display: none; } }
    </style>
</head>
<body class="pb-24">

    <!-- Header Section -->
    <header class="glass-header text-white px-6 pt-12 pb-24 rounded-b-[3.5rem] shadow-2xl sticky top-0 z-50">
        <div class="flex justify-between items-center mb-8">
            <div class="flex items-center gap-4">
                <div class="bg-amber-400 p-3 rounded-2xl shadow-lg shadow-amber-400/20">
                    <i class="fas fa-balance-scale text-slate-900 text-xl"></i>
                </div>
                <div>
                    <h1 class="text-xl font-black tracking-tight">نظام الجرد الرقمي</h1>
                    <p class="text-[10px] text-slate-400 font-bold uppercase tracking-widest">قسم الشؤون القانونية - مصرف الأنبار</p>
                </div>
            </div>
            <div class="flex gap-2 no-print">
                <button onclick="window.print()" class="bg-white/10 p-3 rounded-2xl hover:bg-white/20">
                    <i class="fas fa-file-pdf"></i>
                </button>
            </div>
        </div>

        <!-- Search Engine -->
        <div class="relative no-print">
            <i class="fas fa-search absolute right-5 top-4.5 text-slate-500"></i>
            <input type="text" id="mainSearch" oninput="runFilter()" placeholder="بحث برقم الدعوى، الخصم، أو تفاصيل الإجراء..." 
                class="w-full bg-slate-800/60 border border-slate-700/50 rounded-2xl py-4 pr-14 pl-6 text-white placeholder-slate-500 focus:ring-2 focus:ring-amber-500 outline-none shadow-inner backdrop-blur-md">
        </div>
    </header>

    <main class="px-5 -mt-12 relative z-10">
        <!-- Dashboard Stats -->
        <div class="flex gap-4 overflow-x-auto pb-4 no-scrollbar mb-6">
            <div class="bg-white p-5 rounded-[2rem] min-w-[140px] shadow-sm border border-slate-100">
                <p class="text-[10px] font-bold text-slate-400 uppercase mb-1">إجمالي الدعاوي</p>
                <p class="text-2xl font-black text-slate-800" id="totalCount">0</p>
            </div>
            <div class="bg-rose-50 p-5 rounded-[2rem] min-w-[140px] shadow-sm border border-rose-100">
                <p class="text-[10px] font-bold text-rose-400 uppercase mb-1">تنبيهات حرجة</p>
                <p class="text-2xl font-black text-rose-600" id="urgentCount">0</p>
            </div>
            <div class="bg-emerald-50 p-5 rounded-[2rem] min-w-[140px] shadow-sm border border-emerald-100">
                <p class="text-[10px] font-bold text-emerald-400 uppercase mb-1">دعاوي محسومة</p>
                <p class="text-2xl font-black text-emerald-600" id="closedCount">0</p>
            </div>
        </div>

        <!-- Category Tabs -->
        <div class="flex gap-2 mb-6 overflow-x-auto no-scrollbar py-1 no-print">
            <button onclick="setCategory('الكل')" class="cat-btn bg-slate-900 text-white px-8 py-3 rounded-2xl text-xs font-bold shadow-lg transition-all">الكل</button>
            <button onclick="setCategory('نزاهة')" class="cat-btn bg-white text-slate-500 border border-slate-200 px-8 py-3 rounded-2xl text-xs font-bold shadow-sm transition-all">نزاهة</button>
            <button onclick="setCategory('استدلال')" class="cat-btn bg-white text-slate-500 border border-slate-200 px-8 py-3 rounded-2xl text-xs font-bold shadow-sm transition-all">استدلال</button>
            <button onclick="setCategory('عقارات')" class="cat-btn bg-white text-slate-500 border border-slate-200 px-8 py-3 rounded-2xl text-xs font-bold shadow-sm transition-all">عقارات</button>
        </div>

        <!-- Dynamic List -->
        <div id="cardsContainer" class="space-y-4">
            <!-- Items injected by JS -->
        </div>
    </main>

    <!-- Detailed Modal -->
    <div id="modalOverlay" class="fixed inset-0 bg-slate-900/80 z-[100] hidden items-end justify-center backdrop-blur-sm p-4 no-print">
        <div class="bg-white w-full max-w-lg rounded-[3rem] p-8 max-h-[85vh] overflow-y-auto shadow-2xl relative animate-in slide-in-from-bottom duration-300">
            <div class="w-16 h-1.5 bg-slate-100 rounded-full mx-auto mb-10"></div>
            <div id="modalBody"></div>
            <button onclick="closeModal()" class="w-full mt-10 bg-slate-900 text-white py-5 rounded-[1.5rem] font-bold text-sm shadow-xl active:scale-95 transition-all">إغلاق وتخطي</button>
        </div>
    </div>

    <!-- Navigation Bar -->
    <nav class="fixed bottom-0 w-full bg-white/90 backdrop-blur-lg border-t border-slate-100 h-24 px-12 flex justify-between items-center z-40 no-print">
        <div class="flex flex-col items-center text-slate-900">
            <i class="fas fa-house-chimney text-xl mb-1.5"></i>
            <span class="text-[9px] font-black uppercase tracking-tighter">الرئيسية</span>
        </div>
        <div class="flex flex-col items-center text-slate-300">
            <i class="fas fa-file-signature text-xl mb-1.5"></i>
            <span class="text-[9px] font-black uppercase tracking-tighter">التقارير</span>
        </div>
        <div class="flex flex-col items-center text-slate-300">
            <i class="fas fa-user-gear text-xl mb-1.5"></i>
            <span class="text-[9px] font-black uppercase tracking-tighter">الإعدادات</span>
        </div>
    </nav>

    <script>
        // قاعدة البيانات الشاملة المستخرجة من ملفات الجرد الخاصة بك
        const masterData = [
            { id: 1, type: "نزاهة", caseNum: "196 ق17/ 2021", topic: "المخالفات ضد فرع الخالدية المؤشرة من قبل الرقابة المالية", defendant: "إدارة المصرف الفرعي", damage: "19,937,000,000", agency: "مكتب تحقيق الانبار", status: "قيد التحقيق", days: 8, lastAction: "تم مفاتحة محكمة التحقيق المختصة بقضايا النزاهة لبيان مصير الدعوى بتاريخ 27/10/2025" },
            { id: 2, type: "نزاهة", caseNum: "21 ق17/ 2025", topic: "قروض سيارات فرع عنه - مخالفات إدارية جسيمة", defendant: "سهام رشاد حسن ، جميل عبدالغفور محسن", damage: "10,687,521,200", agency: "مركز شرطة الولاء", status: "محالة", days: -48, lastAction: "مفاتحة مركز شرطة الولاء بكتابنا ذي العدد ق382/ 1959 لبيان مصير الدعوى" },
            { id: 3, type: "نزاهة", caseNum: "22 ق17/ 2020", topic: "الصك المحرف من حساب دائرة صحة الانبار المرقم 0010151", defendant: "خالد سليمان، محمد شهاب، وآخرون", damage: "غير محدد", agency: "مكتب تحقيق النزاهة", status: "محسومة", days: -48, lastAction: "تمييز قرار الشمول بالعفو بتاريخ 2/9/2025" },
            { id: 4, type: "عقارات", caseNum: "رهن 8", topic: "العقارات المرهونة لصالح المصرف - رهن تأميني درجة أولى", defendant: "مشعل إبراهيم عدوان", damage: "قيد التخمين", agency: "المصرف الرئيسي", status: "مفتوحة", days: -45980, lastAction: "متابعة سند الرهن العقاري والإجراءات القانونية مع التسجيل العقاري" },
            { id: 5, type: "استدلال", caseNum: "استدلال/4782", topic: "متابعة المركبة المحجوزة المرقمة 4782 م", defendant: "مجهول", damage: "0", agency: "مكتب المفتش العام", status: "قيد المتابعة", days: 12, lastAction: "جمع المعلومات الأولية والتحري عن مكان تواجد المركبة" },
            { id: 6, type: "نزاهة", caseNum: "19 ق17/ 2019", topic: "صرف صكين محرفين لصالح جهات مجهولة", defendant: "موظفين في حسابات الصحة", damage: "5,400,000,000", agency: "محكمة تحقيق النزاهة", status: "قيد التحقيق", days: 15, lastAction: "إعادة تدوين أقوال الممثل القانوني" }
        ];

        let currentCat = 'الكل';

        function runFilter() {
            const container = document.getElementById('cardsContainer');
            const searchVal = document.getElementById('mainSearch').value.toLowerCase();
            container.innerHTML = '';
            
            let stats = { total: 0, urgent: 0, closed: 0 };

            masterData.forEach(item => {
                const searchMatch = item.caseNum.toLowerCase().includes(searchVal) || 
                                   item.topic.toLowerCase().includes(searchVal) || 
                                   item.defendant.toLowerCase().includes(searchVal);
                const catMatch = currentCat === 'الكل' || item.type === currentCat;

                if (searchMatch && catMatch) {
                    stats.total++;
                    if (item.days < 0) stats.urgent++;
                    if (item.status === 'محسومة') stats.closed++;

                    const card = `
                        <div onclick="openItem(${item.id})" class="case-card bg-white p-6 shadow-sm border border-slate-100 relative overflow-hidden active:bg-slate-50">
                            ${item.days < 0 ? '<div class="absolute top-0 right-0 w-3 h-full bg-rose-500 shadow-lg"></div>' : ''}
                            <div class="flex justify-between items-center mb-3">
                                <span class="bg-indigo-50 text-indigo-700 text-[9px] font-black px-3 py-1 rounded-lg uppercase">${item.type}</span>
                                <span class="text-[10px] font-bold ${item.days < 0 ? 'text-rose-600' : 'text-slate-400'}">
                                    <i class="far fa-clock ml-1"></i> ${Math.abs(item.days)} يوم ${item.days < 0 ? 'تأخير' : 'متبقي'}
                                </span>
                            </div>
                            <h3 class="font-bold text-slate-900 text-sm mb-2 leading-tight">${item.caseNum}</h3>
                            <p class="text-slate-500 text-[11px] mb-4 line-clamp-2 leading-relaxed">${item.topic}</p>
                            <div class="pt-4 border-t border-slate-50 flex justify-between items-center">
                                <div class="flex items-center gap-2">
                                    <div class="w-2 h-2 rounded-full ${item.status === 'محسومة' ? 'bg-emerald-500' : 'bg-amber-400 animate-pulse'}"></div>
                                    <span class="text-[10px] text-slate-400 font-bold">${item.agency}</span>
                                </div>
                                <i class="fas fa-chevron-left text-slate-200 text-xs"></i>
                            </div>
                        </div>
                    `;
                    container.innerHTML += card;
                }
            });

            document.getElementById('totalCount').innerText = stats.total;
            document.getElementById('urgentCount').innerText = stats.urgent;
            document.getElementById('closedCount').innerText = stats.closed;
        }

        function openItem(id) {
            const item = masterData.find(x => x.id === id);
            const body = document.getElementById('modalBody');
            body.innerHTML = `
                <div class="text-right">
                    <span class="bg-amber-100 text-amber-700 text-[10px] font-black px-4 py-1.5 rounded-full mb-4 inline-block uppercase">سجل قضائي رسمي</span>
                    <h2 class="text-2xl font-black text-slate-900 mb-3 leading-tight">${item.caseNum}</h2>
                    <p class="text-slate-500 text-sm mb-10 leading-relaxed font-semibold">${item.topic}</p>
                    
                    <div class="space-y-8">
                        <div class="flex items-start gap-5">
                            <div class="bg-blue-50 p-4 rounded-2xl text-blue-600"><i class="fas fa-user-shield"></i></div>
                            <div>
                                <p class="text-slate-400 text-[10px] font-bold uppercase mb-1">الطرف المشكو منه</p>
                                <p class="text-slate-800 text-sm font-bold">${item.defendant}</p>
                            </div>
                        </div>
                        <div class="flex items-start gap-5">
                            <div class="bg-emerald-50 p-4 rounded-2xl text-emerald-600"><i class="fas fa-landmark"></i></div>
                            <div>
                                <p class="text-slate-400 text-[10px] font-bold uppercase mb-1">المحكمة / الجهة المعنية</p>
                                <p class="text-slate-800 text-sm font-bold">${item.agency}</p>
                            </div>
                        </div>
                        <div class="flex items-start gap-5">
                            <div class="bg-slate-50 p-4 rounded-2xl text-slate-700"><i class="fas fa-history"></i></div>
                            <div>
                                <p class="text-slate-400 text-[10px] font-bold uppercase mb-1">آخر إجراء موثق</p>
                                <p class="text-slate-800 text-xs font-bold leading-relaxed">${item.lastAction}</p>
                            </div>
                        </div>
                        
                        <div class="bg-slate-900 p-8 rounded-[2.5rem] text-white flex justify-between items-center mt-12 shadow-2xl">
                            <div>
                                <p class="text-slate-400 text-[10px] font-black mb-1 uppercase tracking-widest opacity-60">مبلغ الضرر</p>
                                <p class="text-amber-400 text-2xl font-black">${item.damage} <span class="text-[10px] font-normal text-white">د.ع</span></p>
                            </div>
                            <div class="text-left">
                                <p class="text-slate-400 text-[10px] font-black mb-1 uppercase text-left opacity-60">الوضعية</p>
                                <span class="bg-white/10 px-4 py-2 rounded-2xl text-xs font-bold border border-white/10">${item.status}</span>
                            </div>
                        </div>
                    </div>
                </div>
            `;
            document.getElementById('modalOverlay').style.display = 'flex';
        }

        function closeModal() { document.getElementById('modalOverlay').style.display = 'none'; }

        function setCategory(cat) {
            currentCat = cat;
            document.querySelectorAll('.cat-btn').forEach(btn => {
                btn.className = "cat-btn bg-white text-slate-500 border border-slate-200 px-8 py-3 rounded-2xl text-xs font-bold shadow-sm transition-all";
            });
            event.target.className = "cat-btn bg-slate-900 text-white px-8 py-3 rounded-2xl text-xs font-bold shadow-lg transition-all";
            runFilter();
        }

        window.onload = runFilter;
    </script>
</body>
</html>

```
 -
