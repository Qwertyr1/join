<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>GitHub Auth Game</title>
  <style>
    body {
      font-family: Arial, sans-serif;
      text-align: center;
      background-color: #f5f5f5;
    }
    #login, #game {
      display: none;
    }
    button {
      padding: 10px 20px;
      background-color: #28a745;
      color: white;
      border: none;
      cursor: pointer;
      font-size: 16px;
    }
    #game {
      margin-top: 20px;
    }
  </style>
</head>
<body>
  <h1>Welcome to the GitHub Auth Game!</h1>
  <div id="login">
    <p>Please log in with GitHub to play the game:</p>
    <button onclick="loginWithGitHub()">Log in with GitHub</button>
  </div>
  
  <div id="game">
    <p>Hi, <span id="username"></span>! Let’s play a game:</p>
    <button onclick="playGame()">Click me to play</button>
    <p id="result"></p>
  </div>

  <script>
    const clientId = 'YOUR_GITHUB_CLIENT_ID';
    const redirectUri = 'YOUR_REDIRECT_URI';  // e.g., 'http://localhost:8000'
    
    // Check if a GitHub code is present in URL (OAuth callback)
    const urlParams = new URLSearchParams(window.location.search);
    const code = urlParams.get('code');

    if (code) {
      fetchGitHubToken(code);
    } else {
      document.getElementById('login').style.display = 'block';
    }

    function loginWithGitHub() {
      const authUrl = `https://github.com/login/oauth/authorize?client_id=${clientId}&redirect_uri=${encodeURIComponent(redirectUri)}`;
      window.location.href = authUrl;
    }

    async function fetchGitHubToken(code) {
      const response = await fetch('https://your-backend-server.com/github-oauth', {
        method: 'POST',
        headers: {
          'Content-Type': 'application/json'
        },
        body: JSON.stringify({ code })
      });

      const data = await response.json();
      if (data.access_token) {
        fetchGitHubUser(data.access_token);
      } else {
        console.error('GitHub authentication failed.');
      }
    }

    async function fetchGitHubUser(token) {
      const response = await fetch('https://api.github.com/user', {
        headers: {
          Authorization: `token ${token}`
        }
      });
      const userData = await response.json();
      document.getElementById('username').textContent = userData.login;
      document.getElementById('login').style.display = 'none';
      document.getElementById('game').style.display = 'block';
    }

    function playGame() {
      const result = Math.random() < 0.5 ? "You win!" : "You lose!";
      document.getElementById('result').textContent = result;
    }
  </script>
</body>
</html>

