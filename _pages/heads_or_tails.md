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
    </style>
</head>
<body>

    <button id="coin">Flip</button>

    <script>
        const coinButton = document.getElementById('coin');

        coinButton.addEventListener('click', function() {
            const result = Math.random() < 0.5 ? 'H' : 'T';
            coinButton.textContent = result;
        });
    </script>

</body>
</html>
