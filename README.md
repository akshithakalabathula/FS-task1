<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>User Authentication</title>
    <style>
        body {
            font-family: sans-serif;
            display: flex;
            justify-content: center;
            align-items: center;
            height: 100vh;
            margin: 0;
            background-color: #f4f4f4;
        }

        .container {
            background-color: white;
            padding: 20px;
            border-radius: 8px;
            box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
            width: 350px;
        }

        h2 {
            text-align: center;
        }

        .form-group {
            margin-bottom: 15px;
        }

        label {
            display: block;
            margin-bottom: 5px;
        }

        input {
            width: calc(100% - 12px);
            padding: 8px;
            border: 1px solid #ccc;
            border-radius: 4px;
            box-sizing: border-box;
        }

        button {
            background-color: #007bff;
            color: white;
            padding: 10px 15px;
            border: none;
            border-radius: 4px;
            cursor: pointer;
            width: 100%;
        }

        .error-message {
            color: red;
            font-size: 0.9em;
            margin-top: 5px;
        }

        .protected-content {
            display: none; /* Initially hidden */
            margin-top: 20px;
            border: 1px solid #ddd;
            padding: 10px;
        }

        .logout-button {
            background-color: #dc3545; /* Red */
        }
    </style>
</head>
<body>
    <div class="container">
        <h2>Register / Login</h2>
        <form id="authForm">
            <div class="form-group">
                <label for="username">Username</label>
                <input type="text" id="username" name="username" required>
                <div id="usernameError" class="error-message"></div>
            </div>
            <div class="form-group">
                <label for="password">Password</label>
                <input type="password" id="password" name="password" required>
                <div id="passwordError" class="error-message"></div>
            </div>
            <div class="form-group">
                <button type="submit" id="submitButton">Register</button>
            </div>
            <div id="authMessage" class="error-message"></div>
        </form>
        <div class="protected-content" id="protectedContent">
            <h3>Protected Content</h3>
            <p>Welcome! You are now logged in.</p>
            <button id="logoutButton" class="logout-button">Logout</button>
        </div>
    </div>

    <script>
        const users = {}; // Simulated user database
        let sessionToken = null;

        function hashPassword(password) { // REPLACE WITH BCRYPT
            let hash = 0; for (let i = 0; i < password.length; i++) { hash = (hash << 5) - hash + password.charCodeAt(i); } return hash.toString();
        }
        function verifyPassword(password, hashedPassword) { return hashPassword(password) === hashedPassword; }
        function generateSessionToken(username) { return btoa(username + Date.now()); }
        function isAuthenticated(token) { return sessionToken === token; }

        document.getElementById('authForm').addEventListener('submit', function(event) {
            event.preventDefault();
            const username = document.getElementById('username').value;
            const password = document.getElementById('password').value;
            const usernameError = document.getElementById('usernameError');
            const passwordError = document.getElementById('passwordError');
            const authMessage = document.getElementById('authMessage');

            usernameError.textContent = ''; passwordError.textContent = ''; authMessage.textContent = '';

            if (!username) { usernameError.textContent = 'Username is required.'; return; }
            if (!password) { passwordError.textContent = 'Password is required.'; return; }

            const submitButton = document.getElementById('submitButton');
            const isRegister = submitButton.textContent === 'Register';

            if (isRegister) {
                if (users[username]) { authMessage.textContent = 'Username already exists.'; return; }
                users[username] = { password: hashPassword(password) };
                authMessage.textContent = 'Registration successful. Please login.';
                submitButton.textContent = 'Login';
            } else {
                if (!users[username] || !verifyPassword(password, users[username].password)) {
                    authMessage.textContent = 'Invalid username or password.'; return;
                }
                sessionToken = generateSessionToken(username);
                document.getElementById('protectedContent').style.display = 'block';
                document.getElementById('authForm').style.display = 'none';
            }
        });

        document.getElementById('logoutButton').addEventListener('click', function() {
            sessionToken = null;
            document.getElementById('protectedContent').style.display = 'none';
            document.getElementById('authForm').style.display = 'block';
            document.getElementById('submitButton').textContent = 'Register'; // Reset to register state
        });
    </script>
</body>
</html>
