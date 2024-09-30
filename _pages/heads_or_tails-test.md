---
layout: none
permalink: /heads_or_tails_test/
---

<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Flip a Coin</title>
    <style>
        body {
            display: flex;
            justify-content: center;
            align-items: center;
            height: 100vh;
            margin: 0;
            background-color: #f4f4f9;
            flex-direction: column;
        }
        #home-button {
            position: absolute;
            top: 20px;
            left: 20px;
            padding: 10px 20px;
            background-color: #007bff;
            color: white;
            border: none;
            border-radius: 5px;
            cursor: pointer;
            font-size: 16px;
            text-decoration: none;
            transition: background-color 0.3s ease;
        }
        #home-button:hover {
            background-color: #0056b3;
        }
        #coin {
            font-size: 50px;
            width: 100px;
            height: 100px;
            border-radius: 50%;
            background-color: #007bff;
            color: white;
            display: flex;
            justify-content: center;
            align-items: center;
            cursor: pointer;
            border: none;
            outline: none;
            transition: background-color 0.3s ease;
            margin-bottom: 20px;
        }
        #score {
            font-size: 20px;
            text-align: center;
            line-height: 1.5;
        }
        #leaderboard {
            position: absolute;
            top: 20px;
            right: 20px;
            background-color: white;
            padding: 10px;
            border: 2px solid #ccc;
            border-radius: 5px;
            width: 200px;
            font-size: 16px;
            box-shadow: 0 2px 5px rgba(0,0,0,0.1);
        }
        #leaderboard h3 {
            margin: 0;
            font-size: 18px;
            text-align: center;
        }
        #leaderboard ul {
            list-style: none;
            padding: 0;
            margin: 10px 0;
        }
        #leaderboard ul li {
            text-align: center;
            margin-bottom: 5px;
        }
    </style>
</head>
<body>

    <a href="/" id="home-button">Home</a>

    <button id="coin">Flip</button>
    <div id="score">
        <span id="current-streak">Current Streak: 0</span><br>
        <span id="high-score">High Score: 0</span>
    </div>

    <!-- Leaderboard section -->
    <div id="leaderboard">
        <h3>Leaderboard</h3>
        <ul id="leaderboard-list"></ul>
    </div>

    <script src="https://www.gstatic.com/firebasejs/9.0.0/firebase-app.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.0.0/firebase-firestore.js"></script>
    <script>
        // Firebase configuration
        const firebaseConfig = {
            apiKey: "AIzaSyBGae8deSU51k9rDow583pqvqN7vwPnjYA",
            authDomain: "heads-or-tails-8ba1c.firebaseapp.com",
            projectId: "heads-or-tails-8ba1c",
            storageBucket: "heads-or-tails-8ba1c.appspot.com",
            messagingSenderId: "1001684783591",
            appId: "1:1001684783591:web:e2b2b82e83180f565b2d42",
            measurementId: "G-RZZJCFC87X"
        };

        // Initialize Firebase
        firebase.initializeApp(firebaseConfig);
        const db = firebase.firestore();

        const coinButton = document.getElementById('coin');
        const currentStreakDisplay = document.getElementById('current-streak');
        const highScoreDisplay = document.getElementById('high-score');
        const leaderboardList = document.getElementById('leaderboard-list');

        let currentStreak = 0;
        let highScore = 0;

        // Function to update leaderboard
        function updateLeaderboard() {
          db.collection("highScores")
            .orderBy("score", "desc")
            .limit(5)
            .get()
            .then((querySnapshot) => {
              leaderboardList.innerHTML = ''; // Clear current leaderboard
              querySnapshot.forEach((doc) => {
                const score = doc.data().score;
                const listItem = document.createElement('li');
                listItem.textContent = score;
                leaderboardList.appendChild(listItem);
              });
            });
        }

        // Function to update global high score
        function updateGlobalHighScore(streak) {
          db.collection("highScores").add({
            score: streak,
            name: "Palmer",
            timestamp: firebase.firestore.FieldValue.serverTimestamp()
          }).then(() => {
            console.log("High score added!");
            updateLeaderboard(); // Update leaderboard after adding new high score
          }).catch((error) => {
            console.error("Error adding high score: ", error);
          });
        }

        // Get leaderboard on page load
        updateLeaderboard();

        coinButton.addEventListener('click', function() {
          const result = Math.random() < 0.5 ? 'H' : 'T';
          coinButton.textContent = result;

          if (result === 'H') {
            currentStreak++;
            coinButton.style.backgroundColor = '#28a745'; // Green for heads
          } else {
            currentStreak = 0;
            coinButton.style.backgroundColor = '#dc3545'; // Red for tails
          }

          if (currentStreak > highScore) {
            highScore = currentStreak;
          }

          // Update score display
          currentStreakDisplay.textContent = `Current Streak: ${currentStreak}`;
          highScoreDisplay.textContent = `High Score: ${highScore}`;

          // If high score is greater than global high score, update it
          if (currentStreak > highScore) {
            updateGlobalHighScore(currentStreak);
          }
        });
    </script>

</body>
</html>

