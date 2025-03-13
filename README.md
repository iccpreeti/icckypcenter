<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Student Registration Form</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            background-color: #e8f4f8;
            display: flex;
            justify-content: center;
            align-items: center;
            flex-direction: column;
            height: 100vh;
        }
        .container {
            background-color: #fff;
            padding: 20px;
            border-radius: 10px;
            box-shadow: 0 0 15px rgba(0, 0, 0, 0.1);
            width: 400px;
            text-align: center;
        }
        input, select {
            width: 100%;
            padding: 10px;
            margin: 10px 0;
            border: 1px solid #ccc;
            border-radius: 5px;
        }
        button {
            width: 100%;
            padding: 12px;
            background-color: #4CAF50;
            color: white;
            border: none;
            border-radius: 5px;
            cursor: pointer;
            font-size: 16px;
        }
        button:hover {
            background-color: #45a049;
        }
        .admin-container {
            display: none;
            margin-top: 20px;
        }
        table {
            width: 100%;
            border-collapse: collapse;
            margin-top: 20px;
        }
        table, th, td {
            border: 1px solid #ccc;
            text-align: left;
        }
        th, td {
            padding: 8px;
        }
        th {
            background-color: #f2f2f2;
        }
    </style>
    <script>
        function validateMobile() {
            let mobile = document.getElementById('mobile').value;
            let regex = /^[6-9]\d{9}$/;
            if (!regex.test(mobile)) {
                alert("Please enter a valid 10-digit mobile number.");
                return false;
            }
            return true;
        }

        function getLocation() {
            if (navigator.geolocation) {
                navigator.geolocation.getCurrentPosition(function(position) {
                    let lat = position.coords.latitude;
                    let lon = position.coords.longitude;
                    let locationLink = `https://www.google.com/maps?q=${lat},${lon}`;
                    document.getElementById('location').value = locationLink;
                }, function() {
                    alert("Unable to retrieve location.");
                });
            } else {
                alert("Geolocation is not supported by this browser.");
            }
        }

        function saveData(event) {
            event.preventDefault();
            if (!validateMobile()) return;

            let name = document.getElementById('name').value;
            let mobile = document.getElementById('mobile').value;
            let father = document.getElementById('father').value;
            let batch = document.getElementById('batch').value;
            let location = document.getElementById('location').value;

            let studentData = JSON.parse(localStorage.getItem('students')) || [];

            if (studentData.some(student => student.mobile === mobile)) {
                alert("This mobile number is already used.");
                return;
            }

            studentData.push({ name, mobile, father, batch, location });
            localStorage.setItem('students', JSON.stringify(studentData));
            alert('Student information saved successfully!');
        }

        function adminLogin() {
            // Show password input section when admin login button is clicked
            document.getElementById('admin-password-section').style.display = 'block';
        }

        function authenticateAdmin() {
            let password = document.getElementById('admin-password').value;
            if (password === "admin123") {
                document.querySelector('.admin-container').style.display = 'block';
                displayStudentData();
            } else {
                alert("Incorrect password!");
            }
        }

        function displayStudentData() {
            let studentData = JSON.parse(localStorage.getItem('students')) || [];
            let tableBody = document.getElementById('student-table-body');
            tableBody.innerHTML = "";
            studentData.forEach((student, index) => {
                let row = `<tr>
                    <td>${student.name}</td>
                    <td>${student.mobile}</td>
                    <td>${student.father}</td>
                    <td>${student.batch}</td>
                    <td><a href="${student.location}" target="_blank">View on Map</a></td>
                    <td><button onclick="deleteStudent(${index})">Delete</button></td>
                </tr>`;
                tableBody.innerHTML += row;
            });
        }

        function deleteStudent(index) {
            let studentData = JSON.parse(localStorage.getItem('students')) || [];
            studentData.splice(index, 1);
            localStorage.setItem('students', JSON.stringify(studentData));
            displayStudentData();
        }

        function togglePasswordVisibility() {
            let passwordField = document.getElementById('admin-password');
            let toggleCheckbox = document.getElementById('show-password');
            if (toggleCheckbox.checked) {
                passwordField.type = 'text';
            } else {
                passwordField.type = 'password';
            }
        }

        function adminLogout() {
            // Hide admin panel and clear the password input
            document.querySelector('.admin-container').style.display = 'none';
            document.getElementById('admin-password').value = '';
            document.getElementById('admin-password-section').style.display = 'none';
        }
    </script>
</head>
<body>

    <div class="container">
        <h2>Student Registration Form</h2>
        <form onsubmit="saveData(event)">
            <label for="name">Full Name:</label>
            <input type="text" id="name" required>
            
            <label for="mobile">Mobile Number:</label>
            <input type="tel" id="mobile" required>
            
            <label for="father">Father's Name:</label>
            <input type="text" id="father" required>
            
            <label for="batch">Batch Month:</label>
            <select id="batch" required>
                <option value="January">January</option>
                <option value="February">February</option>
                <option value="March">March</option>
                <option value="April">April</option>
                <option value="May">May</option>
                <option value="June">June</option>
                <option value="July">July</option>
                <option value="August">August</option>
                <option value="September">September</option>
                <option value="October">October</option>
                <option value="November">November</option>
                <option value="December">December</option>
            </select>
            
            <input type="hidden" id="location">
            <button type="button" onclick="getLocation()">Get Current Location</button>
            <button type="submit">Submit</button>
        </form>
    </div>

    <button onclick="adminLogin()">Admin Login</button>

    <!-- Admin password input section -->
    <div id="admin-password-section" style="display: none; text-align: center;">
        <h3>Enter Admin Password</h3>
        <input type="password" id="admin-password" required>
        <label>
            <input type="checkbox" id="show-password" onclick="togglePasswordVisibility()"> Show Password
        </label>
        <br><br>
        <button onclick="authenticateAdmin()">Login</button>
    </div>

    <!-- Admin Panel -->
    <div class="admin-container">
        <h2>Student Information</h2>
        <button onclick="adminLogout()">Logout</button> <!-- Admin Logout Button -->
        <table>
            <thead>
                <tr>
                    <th>Name</th>
                    <th>Mobile</th>
                    <th>Father's Name</th>
                    <th>Batch</th>
                    <th>Location</th>
                    <th>Action</th>
                </tr>
            </thead>
            <tbody id="student-table-body"></tbody>
        </table>
    </div>

</body>
</html>
