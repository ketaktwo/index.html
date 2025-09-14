<!DOCTYPE html>
<html lang="ar">
<head>
  <meta charset="UTF-8">
  <title>عرض Google Sheets متعددة</title>
  <style>
    body { font-family: Tahoma, sans-serif; margin: 20px; text-align: center; }
    input { padding: 6px; margin: 5px; width: 40%; }
    button { padding: 6px 12px; margin: 5px; }
    select { padding: 6px; margin: 10px; }
    iframe { width: 100%; height: 600px; margin-top: 20px; border: 1px solid #ccc; }
  </style>
</head>
<body>
  <h2>📊 عرض عدة Google Sheets</h2>

  <div>
    <input type="text" id="sheetName" placeholder="اسم الجدول">
    <input type="text" id="sheetUrl" placeholder="رابط Google Sheet">
    <button onclick="addSheet()">➕ إضافة</button>
  </div>

  <div>
    <label>اختر جدول: </label>
    <select id="sheetList" onchange="loadSelectedSheet()">
      <option value="">-- اختر --</option>
    </select>
    <button onclick="deleteSheet()">🗑️ حذف</button>
  </div>

  <iframe id="viewer" src=""></iframe>

  <script>
    let sheets = JSON.parse(localStorage.getItem("sheets")) || [];

    function saveSheets() {
      localStorage.setItem("sheets", JSON.stringify(sheets));
    }

    function renderList() {
      const select = document.getElementById("sheetList");
      select.innerHTML = '<option value="">-- اختر --</option>';
      sheets.forEach((s, i) => {
        const opt = document.createElement("option");
        opt.value = i;
        opt.textContent = s.name;
        select.appendChild(opt);
      });
    }

    function addSheet() {
      const name = document.getElementById("sheetName").value.trim();
      let url = document.getElementById("sheetUrl").value.trim();
      if (!name || !url) {
        alert("⚠️ من فضلك أدخل الاسم والرابط");
        return;
      }

      if (url.includes("/edit")) {
        url = url.replace("/edit", "/preview");
      }

      sheets.push({ name, url });
      saveSheets();
      renderList();
      document.getElementById("sheetName").value = "";
      document.getElementById("sheetUrl").value = "";
    }

    function loadSelectedSheet() {
      const idx = document.getElementById("sheetList").value;
      if (idx !== "") {
        document.getElementById("viewer").src = sheets[idx].url;
      }
    }

    function deleteSheet() {
      const idx = document.getElementById("sheetList").value;
      if (idx === "") {
        alert("⚠️ اختر جدول لحذفه");
        return;
      }
      if (confirm("هل تريد حذف الجدول؟")) {
        sheets.splice(idx, 1);
        saveSheets();
        renderList();
        document.getElementById("viewer").src = "";
      }
    }

    window.onload = function() {
      renderList();
    }
  </script>
</body>
</html>
