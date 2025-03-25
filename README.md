<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Fast Reaction Game</title>
    <style>
        body {
            display: flex;
            flex-direction: column;
            align-items: center;
            justify-content: center;
            height: 100vh;
            background-color: red;
            font-family: Arial, sans-serif;
            text-align: center;
            transition: background-color 0.2s;
            position: relative;
            overflow: hidden;
        }
        #message {
            font-size: 24px;
            color: white;
            margin-bottom: 20px;
        }
        #nameInput, #startGameBtn {
            font-size: 18px;
            padding: 10px;
            margin: 5px;
        }
        .button {
            position: absolute;
            padding: 10px 20px;
            font-size: 16px;
            border: none;
            background-color: #007BFF;
            color: white;
            border-radius: 5px;
            cursor: pointer;
            display: none;
        }
        #leaderboard {
            font-size: 20px;
            color: white;
            margin-top: 20px;
        }
        ol {
            padding: 0;
            list-style-position: inside;
            color: white;
        }
    </style>
</head>
<body>

<h2 id="message">Enter your name to start:</h2>
<input type="text" id="nameInput" placeholder="Your Name">
<button id="startGameBtn">Start Game</button>

<h3>Leaderboard (Top 10 Fastest Players)</h3>
<ol id="leaderboard"></ol>

<script>
    let startTime;
    let scores = JSON.parse(localStorage.getItem("leaderboardScores")) || [];
    let hasPlayed = localStorage.getItem("hasPlayed") === "true";
    let playerName = "";
    let buttons = [];
    let buttonIndex = 0;

    document.getElementById("startGameBtn").addEventListener("click", () => {
        if (hasPlayed) {
            alert("You can only play once!");
            return;
        }
        playerName = document.getElementById("nameInput").value.trim();
        if (playerName === "") {
            alert("Please enter your name!");
            return;
        }
        document.getElementById("nameInput").style.display = "none";
        document.getElementById("startGameBtn").style.display = "none";
        localStorage.setItem("hasPlayed", "true");
        hasPlayed = true;

        document.getElementById("message").innerText = "Are you ready?";
        setTimeout(() => {
            countdown(3);
        }, 1000);
    });

    function countdown(num) {
        if (num === 0) {
            startGame();
            return;
        }
        document.getElementById("message").innerText = num + "...";
        setTimeout(() => countdown(num - 1), 1000);
    }

    function createButtons() {
        for (let i = 0; i < 20; i++) {
            let btn = document.createElement("button");
            btn.classList.add("button");
            btn.innerText = "Click Me!";
            btn.addEventListener("click", () => handleClick(btn));
            document.body.appendChild(btn);
            buttons.push(btn);
        }
    }

    function positionButton(index) {
        if (index >= buttons.length) {
            let reactionTime = ((Date.now() - startTime) / 1000).toFixed(2);
            document.getElementById("message").innerText = `${playerName}, your time: ${reactionTime} seconds`;
            scores.push({ name: playerName, time: reactionTime });
            scores.sort((a, b) => a.time - b.time).slice(0, 10); // Keep only top 10
            localStorage.setItem("leaderboardScores", JSON.stringify(scores));
            updateLeaderboard();
            return;
        }

        let screenWidth = window.innerWidth;
        let screenHeight = window.innerHeight;
        let btn = buttons[index];

        let randomX = Math.random() * (screenWidth - 100);
        let randomY = Math.random() * (screenHeight - 50);
        btn.style.left = `${randomX}px`;
        btn.style.top = `${randomY}px`;
        btn.style.display = "block";
    }

    function handleClick(button) {
        button.style.display = "none";
        buttonIndex++;
        positionButton(buttonIndex);
    }

    function updateLeaderboard() {
        let leaderboard = document.getElementById("leaderboard");
        leaderboard.innerHTML = "";

        let sortedScores = scores.sort((a, b) => a.time - b.time).slice(0, 10);
        sortedScores.forEach((score, index) => {
            let li = document.createElement("li");
            li.textContent = `#${index + 1}: ${score.name} - ${score.time} seconds`;
            leaderboard.appendChild(li);
        });
    }

    function startGame() {
        document.body.style.backgroundColor = "red";
        document.getElementById("message").innerText = "Wait for green...";
        buttonIndex = 0;

        let randomDelay = Math.floor(Math.random() * 3000) + 2000;
        setTimeout(() => {
            document.body.style.backgroundColor = "green";
            document.getElementById("message").innerText = "Click all buttons!";
            startTime = Date.now();
            positionButton(buttonIndex);
        }, randomDelay);
    }

    createButtons();
    updateLeaderboard();
</script>

</body>
</html>

