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

        .notification-bubble {
            position: absolute;
            top: 2px;  /* Adjusts position from the top */
            right: 2px; /* Adjusts position from the right */
            background-color: rgb(0, 160, 0); /* Makes the bubble green */
            color: white; /* White text inside the bubble */
            padding: 4px 8px; /* Adds padding inside the bubble */
            border-radius: 20px; /* Makes the bubble rounded */
            font-size: 10px; /* Font size for the text */
            font-weight: bold;
            font-family: Helvetica, sans-serif;
            box-shadow: 0px 4px 6px rgba(0, 0, 0, 0.1); /* Adds a slight shadow for effect */
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
                    <div class="notification-bubble" id="heads-or-tails-plays">0 Plays</div>
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

<script src="https://www.gstatic.com/firebasejs/9.0.0/firebase-app.js"></script>
<script src="https://www.gstatic.com/firebasejs/9.0.0/firebase-firestore.js"></script>
<script>
    // Firebase configuration for head-or-tails
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

    // Function to get the page view count
    function getPageViewCount() {
      const pageViewsRef = db.collection('pageViews').doc('views');
      pageViewsRef.get()
      .then((doc) => {
        if (doc.exists) {
          const count = doc.data().count;  // Get the 'count' field value
          document.getElementById('heads-or-tails-plays').innerText = `${count} Plays`;
        } else {
          console.log("No such document!");
        }
      })
      .catch((error) => {
        console.error("Error getting document: ", error);
      });
    }

    // Call this function when the page loads
    window.onload = function() {
      getPageViewCount();
    };
</script>
</body>
</html>

