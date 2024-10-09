---
layout: none
permalink: /games-by-palmer/
---

<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Games by Palmer</title>
    <style>
        /* General layout */
        body {
            font-family: Helvetica, sans-serif;
            background-color: #abd6f3;
            text-decoration: none;
        }

        .container {
            display: flex;
            flex-wrap: wrap;
            gap: 5vw;
            justify-content: center;
            padding: 20px 0px;
            text-decoration: none;
        }

        /* Default (desktop) size */
        .tile {
            width: min(40vw,250px);
            text-align: center;
            position: relative;
            border-radius: min(2vw,12.5px); /* Adjust the value as needed */
            overflow: hidden; /* Ensures the images inside follow the rounded corners */
            text-decoration: none;
            background-color: #205dec;
        }

        .tile-image {
            position: relative;
            width: 100%;
            height: min(30vw,187px);
            overflow: hidden;
            text-decoration: none;
        }

        .tile-image img {
            width: 100%;
            height: 100%;
            display: block;
            object-fit: cover;
            transition: opacity 0.5s ease;
            text-decoration: none;
        }

        /* Initially hide the animated GIF */
        .tile-image .animated {
            position: absolute;
            top: 0;
            left: 0;
            opacity: 0;              /* Hide the GIF initially */
            z-index: 1;               /* Ensure it stacks above the static image */
            transition: opacity 0.5s ease;
            text-decoration: none;
        }

        /* Show GIF on hover */
        .tile-image:hover .animated {
            opacity: 1;               /* Fade in the GIF */
        }

        /* Fade out the static image on hover */
        .tile-image:hover .static {
            opacity: 0;               /* Fade out the static image */
            transition: opacity 0.5s ease;
        }

        h3 {
            margin-top: 10px;
            font-size: 16px;
            text-decoration: none !important;
            color: #e2eef5;
        }

        /* Adding shadow effect */
        .tile {
            box-shadow: 0 4px 8px rgba(0, 0, 0, 0.2);
            transition: box-shadow 0.3s ease;
            text-decoration: none;
        }

        .tile:hover {
            box-shadow: 0 8px 16px rgba(0, 0, 0, 0.3);
            text-decoration: none;
        }
    </style>
</head>
<script>
  gtag('event', 'play', {
  'event_category': 'Game',
  'event_label': 'heads-or-tails', // Adjust this label for each game
  'value': 1
});

</script>
<body>
    <div class="container">
        <div class="tile">
            <a href="/heads_or_tails">
                <div class="tile-image">
                    <img src="/assets/images/heads-or-tails.jpg" alt="Game 1" class="static">
                    <img src="/assets/images/heads-or-tails.gif" alt="Game 1" class="animated">
                </div>
                <h3>Heads or Tails</h3>
            </a>
        </div>

        <!-- More game tiles -->
    </div>

    <script>
        // Optional JavaScript to load GIF on hover
        document.querySelectorAll('.tile-image').forEach(tile => {
            tile.addEventListener('mouseenter', function() {
                let staticImg = tile.querySelector('.static');
                let animatedImg = tile.querySelector('.animated');
                animatedImg.src = animatedImg.getAttribute('data-src'); // Load GIF if using data-src
            });
        });
    </script>
</body>
</html>
