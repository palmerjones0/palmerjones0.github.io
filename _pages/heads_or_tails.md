---
layout: none
permalink: /heads_or_tails/
---

<html lang="en">
<head>
    <!-- Google tag (gtag.js) -->
    <script async src="https://www.googletagmanager.com/gtag/js?id=G-RZZJCFC87X"></script>
    <script>
      window.dataLayer = window.dataLayer || [];
      function gtag(){dataLayer.push(arguments);}
      gtag('js', new Date());
    
      gtag('config', 'G-RZZJCFC87X');
    </script>
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
            left: 10px;
            padding: 9px 12px;
            background-color: #007bff;
            color: white;
            border: none;
            border-radius: 5px;
            cursor: pointer;
            font-family: Helvetica;
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
            margin-top: 100px;
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
            font-family: Helvetica;
            text-align: center;
            line-height: 1.5;
        }
        #leaderboard {
            position: absolute;
            top: 20px;
            right: 10px;
            background-color: white;
            padding: 6px;
            border: 2px solid #ccc;
            border-radius: 8px;
            width: 120px;
            font-size: 14px;
            font-family: Helvetica;
            /*box-shadow: 0 2px 5px rgba(0,0,0,0.1);*/
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
            text-align: left;
            margin-bottom: 2px;
            margin-left: 4px;
        }
        #result {
            margin-top: 20px;
            font-size: 24px;
    }

    /* The Modal (background) */
    #nameModal {
      display: none; /* Hidden by default */
      position: fixed; /* Stay in place */
      z-index: 1; /* Sit on top */
      left: 0;
      top: 0;
      width: 100%; /* Full width */
      height: 100%; /* Full height */
      background-color: rgba(0, 0, 0, 0.5); /* Black with opacity */
    }

    /* Modal Content (The Popup Box) */
    #modalContent {
      background-color: white;
      margin: 15% auto; /* 15% from the top and centered */
      padding: 20px;
      border: 1px solid #888;
      width: 300px; /* Could be more or less, depending on screen size */
    }

    /* Input Field */
    #nameInput {
      width: 100%;
      padding: 10px;
      margin-bottom: 10px;
      font-size: 16px;
    }

    /* Submit Button */
    #submitName {
      padding: 10px 20px;
      font-size: 16px;
      cursor: pointer;
      background-color: #4CAF50;
      color: white;
      border: none;
      border-radius: 5px;
    }

    </style>
</head>
<body>

    <a href="/" id="home-button">palmercjones.com</a>

    <button id="coin">Flip</button>
    <div id="score">
        <span id="current-streak">Current Streak: 0</span><br>
        <span id="high-score">High Score: 0</span>
    </div>

    <!-- The Modal -->
    <div id="nameModal">
        <div id="modalContent">
        <h3>New High Score! Enter Your Name:</h3>
        <input type="text" id="nameInput" placeholder="Enter your name">
        <button id="submitName">Enter</button>
        </div>
    </div>

    <!-- Leaderboard section -->
    <div id="leaderboard">
        <h3>Leaderboard</h3>
        <ul id="leaderboard-list"></ul>
    </div>

    <!-- Firebase v8 SDKs -->
    <script src="https://www.gstatic.com/firebasejs/8.10.0/firebase-app.js"></script>
    <script src="https://www.gstatic.com/firebasejs/8.10.0/firebase-firestore.js"></script>


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
        const resultDiv = document.getElementById('result');
        const nameModal = document.getElementById('nameModal');
        const nameInput = document.getElementById('nameInput');
        const submitName = document.getElementById('submitName');

        let currentStreak = 0;
        let highScore = 0;
        let minScore = 0;

        // Function to show the modal
        function showNameModal() {
            nameModal.style.display = 'block';
        }

        // Function to hide the modal
        function hideNameModal() {
            nameModal.style.display = 'none';
        }

        // When the user clicks the submit button
        submitName.addEventListener('click', () => {
            const userName = nameInput.value;
            if (userName) {
                console.log(`Name entered: ${userName}`);
                // Here, you would submit the name and score to Firebase
                updateGlobalHighScore(currentStreak, userName)
                currentStreak = 0;
                hideNameModal();
            }
        });

        // Function to update leaderboard
        function updateLeaderboard() {
          db.collection("highScores")
            .orderBy("score", "desc")
            .limit(10)
            .get()
            .then((querySnapshot) => {
              leaderboardList.innerHTML = ''; // Clear current leaderboard
              querySnapshot.forEach((doc) => {
                const score = doc.data().score;
                const name = doc.data().name;
                const listItem = document.createElement('li');
                listItem.textContent = score + ' - ' + name;
                leaderboardList.appendChild(listItem);
                minScore = score;
              });
            });
        }

        // Function to update global high score
        function updateGlobalHighScore(streak, nickname) {
          db.collection("highScores").add({
            score: streak,
            name: nickname,
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
            // If high score is greater than global high score, update it
            if (currentStreak > minScore) {
                showNameModal();
            } else {
                currentStreak = 0;
            }
            coinButton.style.backgroundColor = '#dc3545'; // Red for tails
          }

          if (currentStreak > highScore) {
            highScore = currentStreak;
          }

          // Update score display
          currentStreakDisplay.textContent = `Current Streak: ${currentStreak}`;
          highScoreDisplay.textContent = `High Score: ${highScore}`;

          
          
        });
    </script>

</body>
</html>

