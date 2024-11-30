<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Local Storage Manager</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            background-color: #f4f4f9;
            margin: 0;
            padding: 0;
            animation: fadeIn 1s;
        }
        @keyframes fadeIn {
            from { opacity: 0; }
            to { opacity: 1; }
        }
        .container {
            max-width: 800px;
            margin: 50px auto;
            background: #ffffff;
            border-radius: 8px;
            box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
            padding: 20px;
            animation: slideIn 0.5s ease-out;
        }
        @keyframes slideIn {
            from { transform: translateY(-20px); opacity: 0; }
            to { transform: translateY(0); opacity: 1; }
        }
        h1 {
            text-align: center;
            color: #333;
        }
        .form-group {
            margin-bottom: 15px;
        }
        label {
            display: block;
            margin-bottom: 5px;
            font-weight: bold;
        }
        input[type="text"] {
            width: 100%;
            padding: 10px;
            border: 1px solid #ccc;
            border-radius: 5px;
        }
        button {
            display: block;
            width: 100%;
            padding: 10px;
            background-color: #007BFF;
            color: white;
            border: none;
            border-radius: 5px;
            cursor: pointer;
            font-size: 16px;
            transition: background-color 0.3s;
        }
        button:hover {
            background-color: #0056b3;
        }
        .search-bar {
            margin: 20px 0;
        }
        table {
            width: 100%;
            border-collapse: collapse;
            margin-top: 20px;
        }
        table th, table td {
            border: 1px solid #ddd;
            padding: 8px;
            text-align: center;
        }
        table th {
            background-color: #007BFF;
            color: white;
        }
        .actions button {
            margin: 0 5px;
        }
    </style>
</head>
<body>
    <div class="container">
        <h1>Local Storage Manager</h1>

        <div class="form-group">
            <label for="userId">User-ID</label>
            <input type="text" id="userId" placeholder="Enter User-ID">
        </div>
        <div class="form-group">
            <label for="userMobile">User-Mobile</label>
            <input type="text" id="userMobile" placeholder="Enter User-Mobile">
        </div>
        <div class="form-group">
            <label for="userName">User-Name</label>
            <input type="text" id="userName" placeholder="Enter User-Name">
        </div>
        <div class="form-group">
            <label for="projectName">Project Name</label>
            <input type="text" id="projectName" placeholder="Enter Project Name">
        </div>
        <button onclick="saveData()">Save Data</button>

        <div class="search-bar">
            <input type="text" id="searchQuery" placeholder="Search by User-ID or User-Mobile">
            <button onclick="searchData()">Search</button>
        </div>

        <table id="dataTable">
            <thead>
                <tr>
                    <th>User-ID</th>
                    <th>User-Mobile</th>
                    <th>User-Name</th>
                    <th>Project Name</th>
                    <th>Actions</th>
                </tr>
            </thead>
            <tbody>
                <!-- Data rows will be populated here -->
            </tbody>
        </table>
    </div>

    <script>
        function saveData() {
            const userId = document.getElementById('userId').value;
            const userMobile = document.getElementById('userMobile').value;
            const userName = document.getElementById('userName').value;
            const projectName = document.getElementById('projectName').value;

            if (!userId || !userMobile || !userName || !projectName) {
                alert('Please fill all fields');
                return;
            }

            if (localStorage.getItem(userId)) {
                alert('This User-ID already exists. Please use a unique User-ID.');
                return;
            }

            const userData = { userId, userMobile, userName, projectName };
            localStorage.setItem(userId, JSON.stringify(userData));

            alert('Data saved successfully!');
            clearInputs();
            loadTable();
        }

        function clearInputs() {
            document.getElementById('userId').value = '';
            document.getElementById('userMobile').value = '';
            document.getElementById('userName').value = '';
            document.getElementById('projectName').value = '';
        }

        function loadTable() {
            const tableBody = document.querySelector('#dataTable tbody');
            tableBody.innerHTML = '';

            for (let i = 0; i < localStorage.length; i++) {
                const key = localStorage.key(i);
                const data = JSON.parse(localStorage.getItem(key));

                const row = document.createElement('tr');
                row.innerHTML = `
                    <td>${data.userId}</td>
                    <td>${data.userMobile}</td>
                    <td>${data.userName}</td>
                    <td>${data.projectName}</td>
                    <td class="actions">
                        <button onclick="editData('${data.userId}')">Edit</button>
                        <button onclick="deleteData('${data.userId}')">Delete</button>
                    </td>
                `;
                tableBody.appendChild(row);
            }
        }

        function deleteData(userId) {
            if (confirm('Are you sure you want to delete this data?')) {
                localStorage.removeItem(userId);
                loadTable();
            }
        }

        function editData(userId) {
            const data = JSON.parse(localStorage.getItem(userId));

            document.getElementById('userId').value = data.userId;
            document.getElementById('userMobile').value = data.userMobile;
            document.getElementById('userName').value = data.userName;
            document.getElementById('projectName').value = data.projectName;

            deleteData(userId);
        }

        function searchData() {
            const query = document.getElementById('searchQuery').value.toLowerCase();
            const tableBody = document.querySelector('#dataTable tbody');
            tableBody.innerHTML = '';

            if (!query) {
                alert('Please enter a search query');
                loadTable();
                return;
            }

            for (let i = 0; i < localStorage.length; i++) {
                const key = localStorage.key(i);
                const data = JSON.parse(localStorage.getItem(key));

                if (data.userId.toLowerCase().includes(query) || data.userMobile.toLowerCase().includes(query)) {
                    const row = document.createElement('tr');
                    row.innerHTML = `
                        <td>${data.userId}</td>
                        <td>${data.userMobile}</td>
                        <td>${data.userName}</td>
                        <td>${data.projectName}</td>
                        <td class="actions">
                            <button onclick="editData('${data.userId}')">Edit</button>
                            <button onclick="deleteData('${data.userId}')">Delete</button>
                        </td>
                    `;
                    tableBody.appendChild(row);
                }
            }
        }

        window.onload = loadTable;
    </script>
</body>
</html>
