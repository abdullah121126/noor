<!DOCTYPE html>
<html lang="ar">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>نموذج تسجيل البلاغات</title>
    <style>
        body { font-family: Arial, sans-serif; direction: rtl; text-align: right; }
        form, #savedReportsContainer { width: 50%; margin: auto; padding: 20px; border: 1px solid #ccc; border-radius: 10px; }
        label, select, input, textarea { display: block; width: 100%; margin-bottom: 10px; }
        button { background-color: #0DA9A6; color: white; padding: 10px; border: none; cursor: pointer; margin-top: 10px; }
        button:hover { background-color: #099B8E; }
        .source-options { display: flex; gap: 10px; justify-content: center; margin-bottom: 10px; }
        .source-option { cursor: pointer; padding: 10px; border: 1px solid #ccc; border-radius: 10px; text-align: center; width: 80px; }
        .source-option img { width: 40px; height: 40px; display: block; margin: auto; }
        .selected { background-color: #d4edda; border-color: #28a745; }
        table { width: 100%; border-collapse: collapse; margin-top: 20px; background-color: #15445A; color: white; }
        th, td { border: 1px solid #ccc; padding: 8px; text-align: center; }
        th { background-color: #12394D; }
        #savedReportsContainer { display: none; }
        footer { text-align: center; margin-top: 20px; padding: 10px; background-color: #f1f1f1; }
        h2, h3 { color: #3D7EB9; }
        label, #savedReportsContainer h3 { color: #3D7EB9; }
        select, input, textarea { background-color: #f0f4f8; border-radius: 5px; }
    </style>
</head>
<body>
    <h2 style="text-align: center;">نموذج تسجيل البلاغات</h2>
    <form id="reportForm">
        <label>تاريخ ووقت البلاغ:</label>
        <input type="datetime-local" id="reportDateTime" readonly>

        <label>نوع البلاغ:</label>
        <div class="source-options">
            <div class="source-option" data-value="الهاتف" onclick="selectSource(this)">
                <img src="https://cdn-icons-png.flaticon.com/512/724/724664.png" alt="الهاتف">
                <span>الهاتف</span>
            </div>
            <div class="source-option" data-value="نظام نور" onclick="selectSource(this)">
                <img src="https://cdn-icons-png.flaticon.com/512/3178/3178282.png" alt="نظام نور">
                <span>نظام نور</span>
            </div>
            <div class="source-option" data-value="WhatsApp" onclick="selectSource(this)">
                <img src="https://cdn-icons-png.flaticon.com/512/733/733585.png" alt="WhatsApp">
                <span>WhatsApp</span>
            </div>
            <div class="source-option" data-value="تواصل" onclick="selectSource(this)">
                <img src="https://cdn-icons-png.flaticon.com/512/3602/3602123.png" alt="تواصل">
                <span>تواصل</span>
            </div>
            <div class="source-option" data-value="البريد" onclick="selectSource(this)">
                <img src="https://cdn-icons-png.flaticon.com/512/732/732200.png" alt="البريد">
                <span>البريد</span>
            </div>
        </div>
        <input type="hidden" id="reportSource" required>

        <label>تفاصيل البلاغ:</label>
        <textarea id="reportDetails"></textarea>

        <label>حالة البلاغ:</label>
        <select id="reportStatus">
            <option value="تحت الاجراء">تحت الإجراء</option>
            <option value="مغلق">مغلق</option>
            <option value="بحاجة للمتابعة">بحاجة للمتابعة</option>
        </select>

        <button type="submit">تسجيل البلاغ</button>
    </form>

    <label for="sourceFilter">فلتر البلاغات حسب المصدر:</label>
    <select id="sourceFilter" onchange="filterReports()">
        <option value="">كل البلاغات</option>
        <option value="الهاتف">الهاتف</option>
        <option value="نظام نور">نظام نور</option>
        <option value="WhatsApp">WhatsApp</option>
        <option value="تواصل">تواصل</option>
        <option value="البريد">البريد</option>
    </select>

    <button onclick="toggleSavedReports()">عرض البلاغات المحفوظة</button>
    <div id="savedReportsContainer">
        <h3 style="text-align: center;">البلاغات المحفوظة</h3>
        <table id="savedReports">
            <thead>
                <tr>
                    <th>#</th>
                    <th>التاريخ والوقت</th>
                    <th>المصدر</th>
                    <th>التفاصيل</th>
                    <th>الحالة</th>
                    <th>إجراء</th>
                </tr>
            </thead>
            <tbody></tbody>
        </table>
    </div>
    <button onclick="printReports()">طباعة البلاغات</button>

    <footer>
        <p>تقنية المعلومات - نظام نور</p>
        <p>الرئيس: عبدالملك بن عبدالله الشعيبي</p>
    </footer>

    <script>
        function selectSource(element) {
            const sourceValue = element.getAttribute('data-value');
            const sourceField = document.getElementById('reportSource');
            sourceField.value = sourceValue;

            document.querySelectorAll('.source-option').forEach(option => {
                option.classList.remove('selected');
            });
            element.classList.add('selected');
        }

        document.getElementById('reportForm').addEventListener('submit', function(event) {
            event.preventDefault();
            const dateTime = new Date().toLocaleString();
            const source = document.getElementById('reportSource').value;
            const details = document.getElementById('reportDetails').value;
            const status = document.getElementById('reportStatus').value;

            if (!source) {
                alert('يرجى اختيار مصدر البلاغ');
                return;
            }

            const savedReports = JSON.parse(localStorage.getItem('reports')) || [];
            savedReports.push({ dateTime, source, details, status });
            localStorage.setItem('reports', JSON.stringify(savedReports));

            alert('تم تسجيل البلاغ بنجاح');
        });

        function loadSavedReports() {
            const savedReports = JSON.parse(localStorage.getItem('reports')) || [];
            const tableBody = document.getElementById('savedReports').getElementsByTagName('tbody')[0];
            tableBody.innerHTML = '';

            savedReports.forEach((report, index) => {
                const newRow = tableBody.insertRow();
                newRow.insertCell(0).textContent = index + 1;
                newRow.insertCell(1).textContent = report.dateTime;
                newRow.insertCell(2).textContent = report.source;
                newRow.insertCell(3).textContent = report.details;
                newRow.insertCell(4).textContent = report.status;
                const deleteCell = newRow.insertCell(5);
                const deleteButton = document.createElement('button');
                deleteButton.textContent = 'حذف';
                deleteButton.classList.add('deleteButton');
                deleteButton.onclick = function() {
                    deleteReport(index);
                };
                deleteCell.appendChild(deleteButton);
            });
        }

        function deleteReport(index) {
            const savedReports = JSON.parse(localStorage.getItem('reports')) || [];
            savedReports.splice(index, 1);
            localStorage.setItem('reports', JSON.stringify(savedReports));
            loadSavedReports();
            alert('تم حذف البلاغ بنجاح');
        }

        function toggleSavedReports() {
            const container = document.getElementById('savedReportsContainer');
            container.style.display = container.style.display === 'none' ? 'block' : 'none';
            if (container.style.display === 'block') {
                loadSavedReports();
            }
        }

        function filterReports() {
            const filterValue = document.getElementById('sourceFilter').value;
            const savedReports = JSON.parse(localStorage.getItem('reports')) || [];
            const filteredReports = savedReports.filter(report => {
                return !filterValue || report.source === filterValue;
            });
            
            const tableBody = document.getElementById('savedReports').getElementsByTagName('tbody')[0];
            tableBody.innerHTML = '';

            filteredReports.forEach((report, index) => {
                const newRow = tableBody.insertRow();
                newRow.insertCell(0).textContent = index + 1;
                newRow.insertCell(1).textContent = report.dateTime;
                newRow.insertCell(2).textContent = report.source;
                newRow.insertCell(3).textContent = report.details;
                newRow.insertCell(4).textContent = report.status;
                const deleteCell = newRow.insertCell(5);
                const deleteButton = document.createElement('button');
                deleteButton.textContent = 'حذف';
                deleteButton.classList.add('deleteButton');
                deleteButton.onclick = function() {
                    deleteReport(index);
                };
                deleteCell.appendChild(deleteButton);
            });
        }

        function printReports() {
            const savedReports = JSON.parse(localStorage.getItem('reports')) || [];
            const table = document.getElementById('savedReports');
            const newWindow = window.open('', '', 'height=800,width=1000');
            newWindow.document.write('<html><head><title>طباعة البلاغات</title></head><body>');
            newWindow.document.write(table.outerHTML);
            newWindow.document.write('</body></html>');
            newWindow.document.close();
            newWindow.print();
        }
    </script>
</body>
</html>
