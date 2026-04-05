# Nandani-life
<!DOCTYPE html>
<html>
<head>
    <title>Data Entry Form</title>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/xlsx/0.17.3/xlsx.full.min.js"></script>
    <style>
        body { font-family: Arial, sans-serif; text-align: center; margin-top: 30px; }
        input, select, button { padding: 10px; margin: 5px; text-transform: uppercase; }
        table { width: 80%; margin: auto; border-collapse: collapse; margin-top: 20px; }
        th, td { border: 1px solid black; padding: 10px; text-align: center; }
        th { background: #f4f4f4; }
        button { cursor: pointer; }
    </style>
</head>
<body>

    <h1>Data Entry Form</h1>

    <input type="date" id="dateInput">
    <input type="text" id="villageInput" placeholder="Village">
    <input type="number" id="weightInput" placeholder="Weight">
    <input type="number" id="receiptInput" placeholder="Receipt No" oninput="validateReceipt()">

    <!-- Car dropdown -->
    <select id="carInput">
        <option value="">Select Car</option>
        <option value="GJ12BX1269">GJ12BX1269</option>
        <option value="GJ12BZ1269">GJ12BZ1269</option>
    </select>

    <br>
    <button onclick="addData()">Add</button>
    <button onclick="verifyPassword(clearData)">Clear All</button>
    <button onclick="verifyPassword(downloadExcel)">Download Excel</button>

    <h2>Saved Data</h2>
    <table>
        <thead>
            <tr>
                <th>Date</th>
                <th>Village</th>
                <th>Weight</th>
                <th>Receipt No</th>
                <th>Car No</th>
            </tr>
        </thead>
        <tbody id="dataList"></tbody>
    </table>

    <script>
        const PASSWORD = "samra3532";

        // Set today's date automatically
        function setTodayDate() {
            let today = new Date().toISOString().split('T')[0];
            document.getElementById("dateInput").value = today;
        }

        window.onload = function() {
            setTodayDate();

            let savedData = JSON.parse(localStorage.getItem("dataList")) || [];
            let table = document.getElementById("dataList");

            savedData.forEach(row => {
                let tr = document.createElement("tr");
                row.forEach(cell => {
                    let td = document.createElement("td");
                    td.textContent = cell;
                    tr.appendChild(td);
                });
                table.appendChild(tr);
            });
        };

        function addData() {
            let date = document.getElementById("dateInput").value;
            let village = document.getElementById("villageInput").value.toUpperCase();
            let weight = document.getElementById("weightInput").value;
            let receipt = document.getElementById("receiptInput").value;
            let car = document.getElementById("carInput").value;

            if (!date || !village || !weight || !receipt || !car) {
                alert("All fields are required!");
                return;
            }

            let table = document.getElementById("dataList");
            let tr = document.createElement("tr");

            [date, village, weight, receipt, car].forEach(text => {
                let td = document.createElement("td");
                td.textContent = text;
                tr.appendChild(td);
            });

            table.appendChild(tr);

            let savedData = JSON.parse(localStorage.getItem("dataList")) || [];
            savedData.push([date, village, weight, receipt, car]);
            localStorage.setItem("dataList", JSON.stringify(savedData));

            // Reset inputs (date stays today)
            document.getElementById("villageInput").value = "";
            document.getElementById("weightInput").value = "";
            document.getElementById("receiptInput").value = "";
            document.getElementById("carInput").value = "";
        }

        function validateReceipt() {
            let receiptInput = document.getElementById("receiptInput");
            receiptInput.value = receiptInput.value.replace(/\D/g, "");
        }

        function verifyPassword(callback) {
            let userInput = prompt("Enter password to proceed:");
            if (userInput === PASSWORD) {
                callback();
            } else {
                alert("Incorrect password!");
            }
        }

        function clearData() {
            localStorage.removeItem("dataList");
            document.getElementById("dataList").innerHTML = "";
            alert("All data has been cleared.");
        }

        function downloadExcel() {
            let savedData = JSON.parse(localStorage.getItem("dataList")) || [];
            if (savedData.length === 0) {
                alert("No data to download!");
                return;
            }

            let worksheet = XLSX.utils.aoa_to_sheet([["Date", "Village", "Weight", "Receipt No", "Car No"], ...savedData]);
            let workbook = XLSX.utils.book_new();
            XLSX.utils.book_append_sheet(workbook, worksheet, "Data");

            XLSX.writeFile(workbook, "data.xlsx");
        }
    </script>

</body>
</html>
