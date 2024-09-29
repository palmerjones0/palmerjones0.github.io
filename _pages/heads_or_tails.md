---
layout: none
permalink: /heads_or_tails/
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
        }
        #score {
            font-size: 20px;
        }
    </style>
</head>
<body>

    <button id="coin">Flip</button>

    <br>

    <div id="score">
        Current Streak: 0 | High Score: 0
    </div>

    <script>
        const coinButton = document.getElementById('coin');
        const scoreDisplay = document.getElementById('score');

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
            scoreDisplay.textContent = `Current Streak: ${currentStreak} | High Score: ${highScore}`;
        });
    </script>

</body>
</html>

