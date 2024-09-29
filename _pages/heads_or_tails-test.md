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
        #score {
            font-size: 20px;
            text-align: center;
            line-height: 1.5;
        }
    </style>
</head>
<body>

    <a href="/" id="home-button">palmercjones.com</a> <!-- Home button -->
    
    <button id="coin">Flip</button>

    <br>

    <div id="score">
        <span id="current-streak">Current Streak: 0</span><br>
        <span id="high-score">High Score: 0</span>
    </div>

    <script>
        const coinButton = document.getElementById('coin');
        const currentStreakDisplay = document.getElementById('current-streak');
        const highScoreDisplay = document.getElementById('high-score');

        let currentStreak = 0;
        let highScore = 0;

        coinButton.addEventListener('click', function() {
            const result = Math.random() < 0.5 ? 'H' : 'T';
            coinButton.textContent = result;
            
            if (result == 'H') {
              currentStreak++;
              coinButton.style.backgroundColor = '#2bc26c';
            } else if ( result == 'T' ) {
              currentStreak = 0;
              coinButton.style.backgroundColor = '#c22b3a';
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

