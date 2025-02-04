---
layout: none
permalink: /out_of_conference_v2/
---

<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <meta name="description" content="Create Purdue's ideal football schedule by meeting objectives and maximizing profit">
  <title>Out of Conference Game</title>
  <style>
    :root {
      --primary-color: #007bff;
      --primary-hover: #0056b3;
      --background-color: #ffffff;
      --border-color: #000000;
      --text-color: #000000;
      --disabled-bg: #cccccc;
      --disabled-text: #666666;
      --table-border: #dddddd;
      --table-header-bg: #f4f4f4;
      --stone-bg: #f7f7f7;
      --shadow-color: rgba(0, 0, 0, 0.3);
      --overlay-bg: rgba(0, 0, 0, 0.5);
      --spacing-sm: 0.5rem;
      --spacing-md: 1rem;
      --spacing-lg: 1.5rem;
      --spacing-xl: 2rem;
      --border-radius: 0.625rem;
    }
    /* Modern Reset */
    *, *::before, *::after {
      box-sizing: border-box;
      margin: 0;
      padding: 0;
    }
    /* Base Styles */
    body {
      font-family: system-ui, -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, Oxygen, Ubuntu, Cantarell, sans-serif;
      line-height: 1.5;
      color: var(--text-color);
      background-color: var(--background-color);
    }
    /* Typography */
    h1 {
      font-size: clamp(1.5rem, 4vw, 2rem);
      text-align: center;
      margin-top: var(--spacing-lg);
    }
    .subtitle {
      text-align: center;
      font-size: clamp(0.875rem, 2vw, 1rem);
      margin-bottom: var(--spacing-lg);
      color: var(--text-color);
    }
    /* Layout */
    .container {
      display: grid;
      grid-template-columns: repeat(auto-fit, minmax(min(300px, 100%), 1fr));
      gap: var(--spacing-lg);
      padding: var(--spacing-lg);
      max-width: 1400px;
      margin: 0 auto;
    }
    /* Tiles */
    .tile {
      background-color: var(--background-color);
      border: 2px solid var(--border-color);
      border-radius: var(--border-radius);
      padding: var(--spacing-lg);
      box-shadow: 1px 2px 5px var(--shadow-color);
    }
    .tile h2 {
      font-size: 1.25rem;
      margin-bottom: var(--spacing-md);
    }
    /* Objectives List */
    #objectives-list {
      list-style: none;
      padding: 0;
    }
    #objectives-list li {
      margin-bottom: var(--spacing-sm);
    }
    #objectives-list input[type="checkbox"] {
      -webkit-appearance: none;
      -moz-appearance: none;
      appearance: none;
      width: 1.2rem;
      height: 1.2rem;
      margin-right: 0.5rem;
      border: 2px solid var(--primary-color);
      border-radius: 3px;
      background-color: white;
      display: inline-block;
      position: relative;
      cursor: pointer;
      transition: all 0.2s ease;
    }
    #objectives-list input[type="checkbox"]:checked {
      background-color: var(--primary-color);
      border-color: var(--primary-color);
    }
    #objectives-list input[type="checkbox"]:checked::after {
      content: "✔";
      font-size: 0.8rem;
      color: white;
      position: absolute;
      top: 50%;
      left: 50%;
      transform: translate(-50%, -50%);
    }
    #objectives-list input[type="checkbox"]:disabled {
      opacity: 1;
      cursor: not-allowed;
    }
    #objectives-list input[type="checkbox"]:checked:disabled {
      background-color: var(--primary-color);
      border-color: var(--primary-color);
      opacity: 1;
    }
    /* Table Styles */
    table {
      width: 100%;
      border-collapse: collapse;
      margin-top: var(--spacing-md);
    }
    th, td {
      border: 1px solid var(--table-border);
      padding: var(--spacing-sm);
      text-align: left;
    }
    th {
      background-color: var(--table-header-bg);
      font-weight: 600;
    }
    /* Set in Stone Games */
    .set-in-stone {
      background-color: var(--stone-bg);
      cursor: default;
    }
    /* Buttons */
    .simulate-button {
      grid-column: 1 / -1;
      width: 100%;
      max-width: 400px;
      margin: var(--spacing-md) auto;
      padding: var(--spacing-md);
      background-color: var(--primary-color);
      color: white;
      border: none;
      border-radius: var(--border-radius);
      cursor: pointer;
      font-size: 1rem;
      transition: background-color 0.2s ease;
    }
    .simulate-button:not(.container .simulate-button) {
      margin: var(--spacing-lg) auto;
    }
    .simulate-button:hover:not(:disabled) {
      background-color: var(--primary-hover);
    }
    .simulate-button:disabled {
      background-color: var(--disabled-bg);
      color: var(--disabled-text);
      cursor: not-allowed;
      opacity: 0.7;
    }
    .hc-button {
      font-size: 0.75rem;
      color: #aaa;
      background: none;
      border: 1px solid var(--table-border);
      border-radius: 5px;
      cursor: pointer;
      padding: 2px 5px;
      margin-left: var(--spacing-sm);
      transition: all 0.2s ease;
      display: none;
    }
    .hc-button.visible {
      display: inline-block;
    }
    .hc-button.selected {
      color: white;
      background-color: var(--primary-color);
      border-color: var(--primary-hover);
    }
    .hc-button:hover:not(.selected) {
      background-color: var(--table-header-bg);
    }
    /* Modal Dialog */
    dialog {
      border: none;
      border-radius: var(--border-radius);
      padding: 0;
      max-width: 90vw;
      width: 500px;
      background: var(--background-color);
      box-shadow: 0 4px 12px var(--shadow-color);
      position: fixed;
      top: 50%;
      left: 50%;
      transform: translate(-50%, -50%);
      margin: 0;
      max-height: 90vh;
      overflow-y: auto;
    }
    dialog::backdrop {
      background-color: var(--overlay-bg);
    }
    .modal-content {
      padding: var(--spacing-lg);
    }
    .modal-content h2 {
      margin-bottom: var(--spacing-lg);
      font-size: clamp(1.25rem, 3vw, 1.5rem);
      text-align: center;
    }
    /* Game Results Animation */
    .game-results {
      display: grid;
      gap: var(--spacing-sm);
      margin-bottom: var(--spacing-md);
    }
    .game-result {
      display: grid;
      grid-template-columns: 1fr auto;
      gap: var(--spacing-sm);
      padding: var(--spacing-sm);
      border: 1px solid var(--table-border);
      border-radius: var(--border-radius);
      opacity: 0;
      transform: translateY(10px);
      transition: opacity 0.3s ease, transform 0.3s ease;
    }
    .game-result.visible {
      opacity: 1;
      transform: translateY(0);
    }
    .game-result-team {
      font-size: clamp(0.875rem, 2.5vw, 1rem);
      font-weight: 500;
    }
    .game-result-outcome {
      font-weight: bold;
      padding: 0.25rem 0.5rem;
      border-radius: 4px;
      min-width: 3rem;
      text-align: center;
    }
    .game-result-outcome.win {
      background-color: #d4edda;
      color: #155724;
    }
    .game-result-outcome.loss {
      background-color: #f8d7da;
      color: #721c24;
    }
    .season-summary {
      background-color: var(--table-header-bg);
      padding: var(--spacing-md);
      border-radius: var(--border-radius);
      margin-top: var(--spacing-md);
      opacity: 0;
      transform: translateY(10px);
      transition: opacity 0.3s ease, transform 0.3s ease;
    }
    .name-input-form {
      margin-top: var(--spacing-md);
      display: grid;
      gap: var(--spacing-md);
    }
    .name-input-form input[type="text"] {
      padding: var(--spacing-sm);
      border: 2px solid var(--border-color);
      border-radius: var(--border-radius);
      font-size: 1rem;
      width: 100%;
    }
    .name-input-form input[type="text"]:focus {
      outline: none;
      border-color: var(--primary-color);
    }
    .name-input-form button {
      width: 100%;
      padding: var(--spacing-sm);
      background-color: var(--primary-color);
      color: white;
      border: none;
      border-radius: var(--border-radius);
      cursor: pointer;
      font-size: 1rem;
      transition: background-color 0.2s ease;
    }
    .name-input-form button:hover:not(:disabled) {
      background-color: var(--primary-hover);
    }
    .name-input-form button:disabled {
      background-color: var(--disabled-bg);
      color: var(--disabled-text);
      cursor: not-allowed;
    }
    .submit-success {
      color: #155724;
      background-color: #d4edda;
      padding: var(--spacing-sm);
      border-radius: var(--border-radius);
      margin-top: var(--spacing-sm);
      text-align: center;
    }
    .season-summary.visible {
      opacity: 1;
      transform: translateY(0);
    }
    .running-record {
      text-align: center;
      font-size: clamp(1rem, 2.5vw, 1.25rem);
      margin: var(--spacing-md) 0;
      font-weight: 500;
    }
    .modal-content button {
      display: block;
      width: 100%;
      max-width: 200px;
      margin: var(--spacing-md) auto 0;
      padding: var(--spacing-md);
      background-color: var(--primary-color);
      color: white;
      border: none;
      border-radius: var(--border-radius);
      cursor: pointer;
      font-size: 1rem;
      transition: background-color 0.2s ease;
    }
    .modal-content button:hover {
      background-color: var(--primary-hover);
    }
    @media (prefers-reduced-motion: reduce) {
      .game-result,
      .season-summary {
        transition: none;
        transform: none;
      }
    }
    /* Game Options */
    .game-option {
      border: 1px solid var(--table-border);
      border-radius: 5px;
      margin: var(--spacing-sm) 0;
      padding: var(--spacing-md);
      cursor: pointer;
      transition: background-color 0.2s ease;
    }
    .game-option:hover {
      background-color: var(--table-header-bg);
    }
    .game-option.selected-game {
      border: 2px solid var(--border-color);
      background-color: var(--stone-bg);
    }
    /* Info Bubble */
    .bubble {
      position: absolute;
      background-color: var(--background-color);
      border: 1px solid var(--border-color);
      border-radius: var(--border-radius);
      padding: var(--spacing-md);
      box-shadow: 0 4px 6px var(--shadow-color);
      z-index: 1000;
      font-size: 0.875rem;
      width: min(250px, 90vw);
      opacity: 0;
      transition: opacity 0.2s ease;
    }
    .bubble:not([hidden]) {
      opacity: 1;
    }
    .bubble strong {
      color: var(--primary-color);
      display: inline-block;
      width: 120px;
      margin-right: var(--spacing-sm);
    }
    .bubble br {
      margin-bottom: var(--spacing-sm);
      content: "";
      display: block;
    }
    /* Footer */
    .footer {
      font-size: 0.75rem;
      text-align: center;
      padding: var(--spacing-lg);
      color: gray;
    }
    /* Accessibility */
    @media (prefers-reduced-motion: reduce) {
      * {
        animation-duration: 0.01ms !important;
        animation-iteration-count: 1 !important;
        transition-duration: 0.01ms !important;
        scroll-behavior: auto !important;
      }
    }
    /* High Scores Section */
    .high-scores-section {
      grid-column: 1 / -1;
    }
    .collapse-toggle {
      width: 100%;
      text-align: left;
      border: none;
      background: none;
      padding: 0;
      cursor: pointer;
      display: flex;
      align-items: center;
      justify-content: space-between;
    }
    .collapse-toggle h2 {
      margin: 0;
    }
    #high-scores-content {
      margin-top: var(--spacing-md);
      height: 400px;
    }
    @media (max-width: 768px) {
      .collapse-toggle::after {
        content: '▼';
        transition: transform 0.3s ease;
      }
      .collapse-toggle[aria-expanded="false"]::after {
        transform: rotate(-90deg);
      }
      .collapse-toggle[aria-expanded="false"] + #high-scores-content {
        display: none;
      }
      .high-scores-section .collapse-toggle {
        aria-expanded: false;
      }
    }
    @media print {
      .simulate-button,
      .hc-button,
      dialog,
      .high-scores-section {
        display: none !important;
      }
      .tile {
        break-inside: avoid;
        border: 1px solid black;
        box-shadow: none;
      }
    }
    #home-button {
        display: block;
        width: fit-content; /* Ensures the width is just enough for its content plus padding */
        margin: 20px auto;  /* Centers the button and adds vertical spacing */
        padding: 9px 12px;
        background-color: #007bff;
        color: white;
        border: none;
        border-radius: 5px;
        cursor: pointer;
        font-family: Helvetica, sans-serif;
        font-size: 16px;
        text-decoration: none;
        transition: background-color 0.3s ease;
    }
    #home-button:hover {
        background-color: #0056b3;
    }
  </style>
</head>
<body>
  <header>
    <h1>Out of Conference</h1>
    <p class="subtitle">Create Purdue's ideal football schedule by meeting all of the Objectives and maximizing Profit!</p>
  </header>
  <main class="container">
    <!-- Schedule Section -->
    <section class="tile" aria-labelledby="schedule-heading">
      <h2 id="schedule-heading">Schedule</h2>
      <table aria-label="Football Schedule">
        <thead>
          <tr>
            <th scope="col">Week</th>
            <th scope="col">Opponent</th>
          </tr>
        </thead>
        <tbody id="schedule-body">
          <!-- Weeks will be populated by JS -->
        </tbody>
      </table>
    </section>
    <!-- Objectives Section -->
    <section class="tile" aria-labelledby="objectives-heading">
      <h2 id="objectives-heading">Objectives</h2>
      <ul id="objectives-list">
        <li><label><input type="checkbox" id="obj-schedule-12" disabled> Schedule 12 games</label></li>
        <li><label><input type="checkbox" id="obj-6-home-games" disabled> 6 or more home games</label></li>
        <li><label><input type="checkbox" id="obj-no-3-road" disabled> No 3 consecutive road games</label></li>
        <li><label><input type="checkbox" id="obj-2-instate" disabled> Play 2 or more in-state opponents</label></li>
        <li><label><input type="checkbox" id="obj-1-fcs" disabled> 1 or fewer FCS opponents</label></li>
        <li><label><input type="checkbox" id="obj-3-plane" disabled> 3 or fewer plane travel games</label></li>
        <li><label><input type="checkbox" id="obj-homecoming" disabled> Pick homecoming (+10% win prob.)</label></li>
      </ul>
    </section>
    <!-- Results Section -->
    <section class="tile" aria-labelledby="preview-heading">
      <h2 id="preview-heading">Preview</h2>
      <p>Profit: <strong id="total-profit">$0.0M</strong></p>
      <p>Expected Wins: <strong id="expected-wins">0.0</strong></p>
    </section>
    <!-- High Scores Section -->
    <section class="tile high-scores-section" aria-labelledby="high-scores-heading">
      <button class="collapse-toggle" aria-expanded="true" aria-controls="high-scores-content">
        <h2 id="high-scores-heading">High Scores</h2>
      </button>
      <div id="high-scores-content">
        <canvas id="high-scores-chart"></canvas>
      </div>
    </section>
    <button class="simulate-button" disabled aria-label="Simulate Season">Simulate Season</button>
  </main>
  <dialog id="game-modal" aria-labelledby="modal-heading">
    <div class="modal-content">
      <h2 id="modal-heading">Select a Game</h2>
      <div id="game-options" role="listbox" tabindex="0">
        <!-- Game options will be dynamically added here -->
      </div>
      <button id="close-modal" aria-label="Close game selection">Close</button>
    </div>
  </dialog>
  <div id="info-bubble" class="bubble" role="tooltip" hidden></div>
  <footer class="footer">
    <p>Version 2.0 &nbsp; | &nbsp; Palmer Jones 2024</p>
    <p>This game is not endorsed by, affiliated with, or sponsored by any of the schools or institutions referenced within. All school names are used for informational purposes only. Any trademarks or intellectual property associated with these schools remain the property of their respective owners.</p>
  </footer>
  <a href="/" id="home-button">palmercjones.com</a>
  <!-- External Libraries -->
  <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
  <script src="https://www.gstatic.com/firebasejs/10.7.1/firebase-app-compat.js"></script>
  <script src="https://www.gstatic.com/firebasejs/10.7.1/firebase-firestore-compat.js"></script>
  <!-- Combined Module Code -->
  <script type="module">
    // ===================== Firebase Section =====================
    const firebaseConfig = {
      apiKey: "AIzaSyDB4YKuJyBbPtQRg9vtoxFmuILAi_x_vbA",
      authDomain: "out-of-conference-game.firebaseapp.com",
      projectId: "out-of-conference-game",
      storageBucket: "out-of-conference-game.firebasestorage.app",
      messagingSenderId: "520736595883",
      appId: "1:520736595883:web:5b7d0f5f3d41dce6a27cee",
      measurementId: "G-Q5BM808KE5"
    };
    try {
      firebase.initializeApp(firebaseConfig);
    } catch (e) {
      console.warn("Firebase initialization error.");
    }
    let db;
    try {
      db = firebase.firestore();
    } catch (e) {
      console.warn("Firestore initialization error.");
    }
    const incrementPageViews = async () => {
      if (localStorage.getItem('pageViewCounted')) return true;
      try {
        const viewsRef = db.collection('pageViews').doc('views');
        await viewsRef.set({
          count: firebase.firestore.FieldValue.increment(1)
        }, { merge: true });
        localStorage.setItem('pageViewCounted', 'true');
        return true;
      } catch (e) {
        console.error("Error incrementing page views:", e);
        return false;
      }
    };
    // Ban a user if their score is abusive.
    const banUser = async (name) => {
      try {
        await db.collection('bannedUsers').add({ name });
        return true;
      } catch (e) {
        console.error("Error banning user:", e);
        return false;
      }
    };
    // getHighScores now filters out scores from banned users.
    const getHighScores = async () => {
      try {
        const scoreSnap = await db.collection('highScores').get();
        let scores = scoreSnap.docs.map(doc => doc.data());
        const bannedSnap = await db.collection('bannedUsers').get();
        const bannedNames = bannedSnap.docs.map(doc => doc.data().name);
        scores = scores.filter(score => !bannedNames.includes(score.name));
        return scores;
      } catch (e) {
        console.error("Error fetching high scores:", e);
        return [];
      }
    };
    const saveGameResult = async (result) => {
      try {
        await db.collection('highScores').add(result);
        return true;
      } catch (e) {
        console.error("Error saving game result:", e);
        return false;
      }
    };
    // ===================== End Firebase Section =====================
    
    // ===================== Game Data =====================
    const gameOptions = [
      { week: 0, teamName: "Hawaii", teamLocation: "Honolulu, HI", gameLocation: "Away", profit: "$1.5M", winPercentage: "70%", travelMethod: "Plane" },
      { week: 1, teamName: "UNLV", teamLocation: "Las Vegas, NV", gameLocation: "Home", profit: "$1.4M", winPercentage: "50%", travelMethod: "N/A" },
      { week: 1, teamName: "Notre Dame", teamLocation: "South Bend, IN", gameLocation: "Away", profit: "$4.0M", winPercentage: "10%", travelMethod: "Bus" },
      { week: 1, teamName: "Indiana State (FCS)", teamLocation: "Terre Haute, IN", gameLocation: "Home", profit: "$1.0M", winPercentage: "90%", travelMethod: "N/A" },
      { week: 1, teamName: "Boston College", teamLocation: "Boston, MA", gameLocation: "Home", profit: "$2.0M", winPercentage: "50%", travelMethod: "N/A" },
      { week: 2, teamName: "Kentucky", teamLocation: "Lexington, KY", gameLocation: "Away", profit: "$2.8M", winPercentage: "50%", travelMethod: "Bus" },
      { week: 2, teamName: "Cincinnati", teamLocation: "Cincinnati, OH", gameLocation: "Away", profit: "$1.8M", winPercentage: "40%", travelMethod: "Bus" },
      { week: 2, teamName: "Butler", teamLocation: "Indianapolis, IN", gameLocation: "Neutral", profit: "$1.1M", winPercentage: "90%", travelMethod: "Bus" },
      { week: 2, teamName: "Ball State", teamLocation: "Muncie, IN", gameLocation: "Home", profit: "$1.2M", winPercentage: "80%", travelMethod: "N/A" },
      { week: 6, teamName: "Eastern Kentucky (FCS)", teamLocation: "Richmond, KY", gameLocation: "Home", profit: "$0.6M", winPercentage: "90%", travelMethod: "N/A" },
      { week: 6, teamName: "Virginia Tech", teamLocation: "Blacksburg, VA", gameLocation: "Away", profit: "$2.0M", winPercentage: "40%", travelMethod: "Plane" },
      { week: 6, teamName: "Louisville", teamLocation: "Louisville, KY", gameLocation: "Away", profit: "$2.0M", winPercentage: "30%", travelMethod: "Bus" },
      { week: 6, teamName: "Northern Illinois", teamLocation: "DePaul, IL", gameLocation: "Home", profit: "$1.1M", winPercentage: "70%", travelMethod: "N/A" },
      { week: 8, teamName: "Valparaiso (FCS)", teamLocation: "Valparaiso, IN", gameLocation: "Home", profit: "$0.7M", winPercentage: "90%", travelMethod: "N/A" },
      { week: 8, teamName: "BYU", teamLocation: "Provo, UT", gameLocation: "Away", profit: "$2.2M", winPercentage: "30%", travelMethod: "Plane" },
      { week: 8, teamName: "Western Kentucky", teamLocation: "Bowling Green, KY", gameLocation: "Home", profit: "$0.6M", winPercentage: "80%", travelMethod: "N/A" },
      { week: 8, teamName: "Liberty", teamLocation: "Lynchburg, VA", gameLocation: "Away", profit: "$0.6M", winPercentage: "70%", travelMethod: "Plane" },
      { week: 11, teamName: "Ohio University", teamLocation: "Athens, OH", gameLocation: "Home", profit: "$0.6M", winPercentage: "80%", travelMethod: "N/A" },
      { week: 11, teamName: "Evansville", teamLocation: "Evansville, IN", gameLocation: "Home", profit: "$0.5M", winPercentage: "90%", travelMethod: "N/A" },
      { week: 11, teamName: "UCONN", teamLocation: "East Hartford, CT", gameLocation: "Away", profit: "$2.0M", winPercentage: "80%", travelMethod: "Plane" },
      { week: 11, teamName: "Miami (OH)", teamLocation: "Oxford, OH", gameLocation: "Away", profit: "$1.0M", winPercentage: "70%", travelMethod: "Bus" },
      { week: 11, teamName: "Southern Illinois (FCS)", teamLocation: "Carbondale, IL", gameLocation: "Home", profit: "$0.6M", winPercentage: "90%", travelMethod: "N/A" }
    ];
    const setInStoneGames = [
      { week: 3, teamName: "Northwestern", teamLocation: "Evanston, IL", gameLocation: "Away", profit: "$0.7M", winPercentage: "60%", travelMethod: "Bus" },
      { week: 4, teamName: "Michigan", teamLocation: "West Lafayette, IN", gameLocation: "Home", profit: "$2.2M", winPercentage: "20%", travelMethod: "N/A" },
      { week: 5, teamName: "Rutgers", teamLocation: "West Lafayette, IN", gameLocation: "Home", profit: "$0.5M", winPercentage: "60%", travelMethod: "N/A" },
      { week: 7, teamName: "Washington", teamLocation: "Seattle, WA", gameLocation: "Away", profit: "$0.8M", winPercentage: "40%", travelMethod: "Plane" },
      { week: 9, teamName: "Illinois", teamLocation: "West Lafayette, IN", gameLocation: "Home", profit: "$1.0M", winPercentage: "50%", travelMethod: "N/A" },
      { week: 10, teamName: "Ohio State", teamLocation: "Columbus, OH", gameLocation: "Away", profit: "$1.5M", winPercentage: "10%", travelMethod: "Bus" },
      { week: 12, teamName: "USC", teamLocation: "West Lafayette, IN", gameLocation: "Home", profit: "$2.0M", winPercentage: "40%", travelMethod: "N/A" },
      { week: 13, teamName: "Minnesota", teamLocation: "Minneapolis, MN", gameLocation: "Away", profit: "$0.7M", winPercentage: "50%", travelMethod: "Plane" },
      { week: 14, teamName: "Indiana", teamLocation: "West Lafayette, IN", gameLocation: "Home", profit: "$1.5M", winPercentage: "60%", travelMethod: "N/A" }
    ];
    const inStateTeams = ["Indiana", "Notre Dame", "Indiana State", "Butler", "Ball State", "Valparaiso", "Taylor", "Evansville", "Marian", "Indianapolis"];
    const TOTAL_WEEKS = 15;
    // ===================== End Game Data =====================
    
    // ===================== Schedule Class =====================
    class Schedule {
      constructor() {
        this.schedule = new Array(TOTAL_WEEKS).fill(null);
        this.homecomingWeek = null;
        setInStoneGames.forEach(game => {
          this.schedule[game.week] = { ...game, isSetInStone: true };
        });
      }
      getGame(week) { return this.schedule[week]; }
      setGame(week, game) {
        if (this.schedule[week]?.isSetInStone)
          throw new Error('Cannot modify set-in-stone games');
        this.schedule[week] = game ? { ...game, isSetInStone: false } : null;
        this.getStats();
      }
      setBye(week) {
        if (this.schedule[week]?.isSetInStone)
          throw new Error('Cannot modify set-in-stone games');
        this.schedule[week] = {
          week,
          teamName: 'BYE',
          gameLocation: null,
          profit: '$0.0M',
          winPercentage: '0%',
          travelMethod: null,
          isSetInStone: false
        };
        this.getStats();
      }
      setHomecoming(week) {
        const game = this.schedule[week];
        if (!game || game.gameLocation !== 'Home')
          throw new Error('Homecoming must be a home game');
        if (this.homecomingWeek !== null && this.homecomingWeek !== week) {
          const prevGame = this.schedule[this.homecomingWeek];
          if (prevGame) {
            prevGame.isHomecoming = false;
            prevGame.winPercentage = prevGame.originalWinPercentage || prevGame.winPercentage;
          }
        }
        game.isHomecoming = true;
        if (!game.originalWinPercentage)
          game.originalWinPercentage = game.winPercentage;
        const currentWinPercent = parseInt(game.winPercentage);
        game.winPercentage = `${Math.min(100, currentWinPercent + 10)}%`;
        this.homecomingWeek = week;
      }
      clearHomecoming() {
        if (this.homecomingWeek !== null) {
          const game = this.schedule[this.homecomingWeek];
          if (game) {
            game.isHomecoming = false;
            game.winPercentage = game.originalWinPercentage || game.winPercentage;
            delete game.originalWinPercentage;
          }
          this.homecomingWeek = null;
        }
      }
      getAvailableGames(week) {
        if (this.schedule[week]?.isSetInStone)
          return [];
        return gameOptions.filter(game => game.week === week);
      }
      getTotalProfit() {
        return this.schedule.reduce((total, game) => {
          if (!game || game.teamName === 'BYE') return total;
          const profit = parseFloat(game.profit.replace(/[^\d.-]/g, ''));
          return total + profit;
        }, 0);
      }
      getExpectedWins() {
        return this.schedule.reduce((total, game) => {
          if (!game || game.teamName === 'BYE') return total;
          const winPercent = parseInt(game.winPercentage.replace('%', '')) / 100;
          return total + winPercent;
        }, 0);
      }
      resetSchedule() {
        this.schedule = this.schedule.map(game => game?.isSetInStone ? game : null);
        this.clearHomecoming();
      }
      getStats() {
        const stats = { totalGames: 0, homeGames: 0, awayGames: 0, neutralGames: 0, fcsGames: 0, planeGames: 0, maxConsecutiveAwayGames: 0, hasHomecoming: this.homecomingWeek !== null };
        let currentAwayStreak = 0;
        this.schedule.forEach(game => {
          if (!game || game.teamName === 'BYE') return;
          stats.totalGames++;
          if (game.gameLocation === 'Home') { stats.homeGames++; currentAwayStreak = 0; }
          else if (game.gameLocation === 'Away') { stats.awayGames++; currentAwayStreak++; }
          else if (game.gameLocation === 'Neutral') { stats.neutralGames++; currentAwayStreak++; }
          stats.maxConsecutiveAwayGames = Math.max(stats.maxConsecutiveAwayGames, currentAwayStreak);
          if (game.teamName.includes('(FCS)')) stats.fcsGames++;
          if (game.travelMethod === 'Plane') stats.planeGames++;
        });
        return stats;
      }
    }
    const schedule = new Schedule();
    // ===================== End Schedule Class =====================
    
    // ===================== Objectives Class =====================
    class Objectives {
      checkSchedule12Games() { return schedule.getStats().totalGames === 12; }
      check6HomeGames() { return schedule.getStats().homeGames >= 6; }
      checkNo3ConsecutiveRoad() { return schedule.getStats().maxConsecutiveAwayGames < 3; }
      check2InStateOpponents() {
        let inStateCount = 0;
        for (let i = 0; i < schedule.schedule.length; i++) {
          const game = schedule.getGame(i);
          if (!game || game.teamName === 'BYE') continue;
          if (inStateTeams.some(team => game.teamName.toLowerCase().includes(team.toLowerCase())))
            inStateCount++;
        }
        return inStateCount >= 2;
      }
      check1FCSOpponent() { return schedule.getStats().fcsGames <= 1; }
      check3PlaneTravelGames() { return schedule.getStats().planeGames <= 3; }
      checkHomecomingSelected() { return schedule.homecomingWeek !== null; }
      checkAll() {
        return {
          schedule12: this.checkSchedule12Games(),
          home6: this.check6HomeGames(),
          no3Road: this.checkNo3ConsecutiveRoad(),
          inState2: this.check2InStateOpponents(),
          fcs1: this.check1FCSOpponent(),
          plane3: this.check3PlaneTravelGames(),
          homecoming: this.checkHomecomingSelected()
        };
      }
      areAllObjectivesMet() {
        const results = this.checkAll();
        return Object.values(results).every(result => result === true);
      }
      getObjectivesSummary() {
        const results = this.checkAll();
        return {
          schedule12: { met: results.schedule12, description: 'Schedule 12 games', current: schedule.getStats().totalGames, target: 12 },
          home6: { met: results.home6, description: '6 or more home games', current: schedule.getStats().homeGames, target: 6 },
          no3Road: { met: results.no3Road, description: 'No 3 consecutive road games', current: schedule.getStats().maxConsecutiveAwayGames, target: '< 3' },
          inState2: { met: results.inState2, description: 'Play 2 or more in-state opponents', current: this.getInStateCount(), target: 2 },
          fcs1: { met: results.fcs1, description: '1 or fewer FCS opponents', current: schedule.getStats().fcsGames, target: '≤ 1' },
          plane3: { met: results.plane3, description: '3 or fewer plane travel games', current: schedule.getStats().planeGames, target: '≤ 3' },
          homecoming: { met: results.homecoming, description: 'Pick homecoming', current: schedule.homecomingWeek !== null ? 'Selected' : 'Not Selected', target: 'Selected' }
        };
      }
      getInStateCount() {
        let count = 0;
        schedule.schedule.forEach(game => {
          if (!game || game.teamName === 'BYE') return;
          if (inStateTeams.some(team => game.teamName.toLowerCase().includes(team.toLowerCase())))
            count++;
        });
        return count;
      }
    }
    const objectives = new Objectives();
    // ===================== End Objectives Class =====================
    
    // ===================== UI Class =====================
    class UI {
      constructor() {
        this.checkboxMapping = {
          'obj-schedule-12': 'schedule12',
          'obj-6-home-games': 'home6',
          'obj-no-3-road': 'no3Road',
          'obj-2-instate': 'inState2',
          'obj-1-fcs': 'fcs1',
          'obj-3-plane': 'plane3',
          'obj-homecoming': 'homecoming'
        };
        this.scheduleBody = document.getElementById('schedule-body');
        this.gameModal = document.getElementById('game-modal');
        this.gameOptions = document.getElementById('game-options');
        this.closeModalBtn = document.getElementById('close-modal');
        this.infoBubble = document.getElementById('info-bubble');
        this.simulateButton = document.querySelector('.simulate-button');
        this.totalProfitElement = document.getElementById('total-profit');
        this.highScoresChart = null;
        this.closeModalBtn.addEventListener('click', () => this.closeModal());
        this.gameModal.addEventListener('click', (e) => { if (e.target === this.gameModal) this.closeModal(); });
        this.selectedRow = null;
        this.initializeSchedule();
        this.initializeObjectivesUI();
        this.initializeSimulation();
        this.initializeHighScores();
        this.initializeCollapsible();
        this.updateStats();
      }
      initializeSchedule() {
        for (let week = 0; week < TOTAL_WEEKS; week++) {
          const game = schedule.getGame(week);
          const row = this.createScheduleRow(week, game);
          this.scheduleBody.appendChild(row);
        }
      }
      createScheduleRow(week, game) {
        const row = document.createElement('tr');
        row.id = `week-${week}`;
        if (game?.isSetInStone) row.classList.add('set-in-stone');
        const weekCell = document.createElement('td');
        weekCell.textContent = week;
        const gameCell = document.createElement('td');
        this.updateGameCell(gameCell, game);
        if (game?.isSetInStone)
          row.addEventListener('click', (e) => this.showGameStats(e, game));
        else
          row.addEventListener('click', () => this.openGameSelection(week));
        row.appendChild(weekCell);
        row.appendChild(gameCell);
        return row;
      }
      updateGameCell(cell, game) {
        if (!game || game.teamName === 'BYE') {
          cell.textContent = game?.teamName || 'OPEN';
          return;
        }
        const locationPrefix = game.gameLocation === 'Home' ? 'vs ' : game.gameLocation === 'Away' ? '@ ' : 'vs ';
        const icon = game.travelMethod === 'Plane' ? '✈️' : game.gameLocation === 'Home' ? '🏠' : '🚌';
        cell.textContent = `${locationPrefix}${game.teamName} ${icon}`;
        const hcButton = document.createElement('button');
        hcButton.className = 'hc-button';
        hcButton.textContent = 'HC';
        if (game.isHomecoming) hcButton.classList.add('selected');
        hcButton.addEventListener('click', (e) => { e.stopPropagation(); this.handleHomecomingSelection(game, hcButton); });
        cell.appendChild(hcButton);
        if (game.gameLocation === 'Home') {
          const weekNum = parseInt(game.week);
          const prevGame = weekNum > 0 ? schedule.getGame(weekNum - 1) : null;
          if (!prevGame || prevGame.gameLocation === 'Home')
            hcButton.style.display = 'none';
          else
            hcButton.classList.add('visible');
        } else {
          hcButton.style.display = 'none';
        }
        cell.dataset.week = game.week;
        cell.dataset.opponent = game.teamName;
        cell.dataset.profit = game.profit;
        cell.dataset.probability = game.winPercentage;
        cell.dataset.location = game.teamLocation;
      }
      openGameSelection(week) {
        this.selectedRow = document.getElementById(`week-${week}`);
        this.gameOptions.innerHTML = '';
        const currentGame = schedule.getGame(week);
        if (currentGame && currentGame.teamName !== 'BYE')
          this.addGameOption(currentGame, true);
        schedule.getAvailableGames(week).forEach(game => {
          this.addGameOption(game);
        });
        const byeOption = document.createElement('div');
        byeOption.className = 'game-option';
        byeOption.innerHTML = '<strong>Mark as BYE</strong>';
        byeOption.addEventListener('click', () => { schedule.setBye(week); this.updateUI(); this.closeModal(); });
        this.gameOptions.appendChild(byeOption);
        this.gameModal.showModal();
      }
      addGameOption(game, isCurrentSelection = false) {
        const option = document.createElement('div');
        option.className = 'game-option';
        if (isCurrentSelection) option.classList.add('selected-game');
        option.innerHTML = `
          <div><strong>Team:</strong> ${game.teamName}</div>
          <div><strong>Location:</strong> ${game.gameLocation}</div>
          ${game.gameLocation !== 'Home' ? `<div><strong>Travel:</strong> ${game.travelMethod}</div>` : ''}
          <div><strong>Profit:</strong> ${game.profit}</div>
          <div><strong>Win %:</strong> ${game.winPercentage}</div>
        `;
        option.addEventListener('click', () => { schedule.setGame(game.week, game); this.updateUI(); this.closeModal(); });
        this.gameOptions.appendChild(option);
      }
      showGameStats(event, game) {
        let bubble = document.getElementById('stats-bubble');
        if (!bubble) {
          bubble = document.createElement('div');
          bubble.id = 'stats-bubble';
          bubble.className = 'bubble';
          document.body.appendChild(bubble);
        }
        bubble.innerHTML = `<strong>Win Probability:</strong>${game.winPercentage}<br><strong>Profit:</strong>${game.profit}`;
        const row = event.target.closest('tr');
        const rect = row.getBoundingClientRect();
        let top = rect.bottom + window.scrollY + 5;
        let left = rect.left + window.scrollX;
        let wrapper = document.getElementById('bubble-wrapper');
        if (!wrapper) {
          wrapper = document.createElement('div');
          wrapper.id = 'bubble-wrapper';
          wrapper.style.position = 'relative';
          document.body.appendChild(wrapper);
        }
        if (bubble.parentElement !== wrapper)
          wrapper.appendChild(bubble);
        wrapper.style.position = 'absolute';
        wrapper.style.top = top + 'px';
        wrapper.style.left = '0';
        wrapper.style.width = '100%';
        bubble.hidden = false;
        const bubbleRect = bubble.getBoundingClientRect();
        const viewportWidth = document.documentElement.clientWidth;
        if (left + bubbleRect.width > viewportWidth)
          left = viewportWidth - bubbleRect.width - 10;
        bubble.style.top = '0';
        bubble.style.left = left + 'px';
        const closeBubble = (e) => {
          if (!bubble.contains(e.target) && !row.contains(e.target)) {
            bubble.hidden = true;
            wrapper.style.display = 'none';
            document.removeEventListener('click', closeBubble);
          }
        };
        const handleMouseOut = (e) => {
          if (!row.contains(e.relatedTarget)) {
            bubble.hidden = true;
            wrapper.style.display = 'none';
            row.removeEventListener('mouseout', handleMouseOut);
            document.removeEventListener('click', closeBubble);
          }
        };
        document.removeEventListener('click', closeBubble);
        row.removeEventListener('mouseout', handleMouseOut);
        wrapper.style.display = 'block';
        setTimeout(() => {
          document.addEventListener('click', closeBubble);
          row.addEventListener('mouseout', handleMouseOut);
        }, 0);
      }
      closeModal() { this.gameModal.close(); this.selectedRow = null; }
      handleHomecomingSelection(game, button) {
        if (button.classList.contains('selected')) {
          schedule.clearHomecoming();
          button.classList.remove('selected');
        } else {
          try {
            schedule.setHomecoming(game.week);
            document.querySelectorAll('.hc-button').forEach(btn => { btn.classList.remove('selected'); });
            button.classList.add('selected');
          } catch (e) {
            console.error("Error setting homecoming:", e);
          }
        }
        this.updateUI();
      }
      updateHCButtonVisibility() {
        Array.from(this.scheduleBody.children).forEach((row, index) => {
          const game = schedule.getGame(index);
          if (!game || game.teamName === 'BYE') return;
          const hcButton = row.querySelector('.hc-button');
          if (!hcButton) return;
          if (game.gameLocation === 'Home') {
            const prevGame = index > 0 ? schedule.getGame(index - 1) : null;
            if (!prevGame || prevGame.gameLocation === 'Home')
              hcButton.style.display = 'none';
            else
              hcButton.classList.add('visible');
          } else {
            hcButton.style.display = 'none';
          }
        });
      }
      async initializeHighScores() {
        const scores = await getHighScores();
        // Compute colors: if a score's name matches the saved user, color purple;
        // the most recent such score will be red.
        const savedName = localStorage.getItem('playerName') || "";
        let mostRecentIndex = -1;
        let mostRecentTimestamp = null;
        scores.forEach((score, i) => {
          if (score.name === savedName && score.timestamp) {
            let ts = (score.timestamp.toMillis) ? score.timestamp.toMillis() : new Date(score.timestamp).getTime();
            if (mostRecentTimestamp === null || ts > mostRecentTimestamp) {
              mostRecentTimestamp = ts;
              mostRecentIndex = i;
            }
          }
        });
        const pointBackgroundColors = scores.map((score, i) => {
          if (score.name === savedName) {
            return (i === mostRecentIndex) ? "rgba(255, 0, 0, 0.5)" : "rgba(128, 0, 128, 0.5)";
          }
          return "rgba(0, 123, 255, 0.5)";
        });
        const pointBorderColors = scores.map((score, i) => {
          if (score.name === savedName) {
            return (i === mostRecentIndex) ? "rgba(255, 0, 0, 1)" : "rgba(128, 0, 128, 1)";
          }
          return "rgba(0, 123, 255, 1)";
        });
        const ctx = document.getElementById('high-scores-chart').getContext('2d');
        if (this.highScoresChart) { this.highScoresChart.destroy(); }
        this.highScoresChart = new Chart(ctx, {
          type: 'scatter',
          data: {
            datasets: [{
              data: scores.map(score => ({ x: score.wins, y: score.profit })),
              pointBackgroundColor: pointBackgroundColors,
              pointBorderColor: pointBorderColors,
              borderWidth: 1,
              pointRadius: 6,
              pointHoverRadius: 8
            }]
          },
          options: {
            responsive: true,
            maintainAspectRatio: false,
            scales: {
              x: { title: { display: true, text: 'Wins' }, min: 0, max: 12, ticks: { stepSize: 1 } },
              y: { title: { display: true, text: 'Profit (Millions $)' } }
            },
            plugins: {
              legend: { display: false },
              tooltip: {
                callbacks: {
                  label: (context) => {
                    const score = scores[context.dataIndex];
                    return `${score.name}: ${score.wins} wins, $${score.profit.toFixed(1)}M`;
                  }
                }
              }
            }
          }
        });
      }
      initializeCollapsible() {
        const toggle = document.querySelector('.collapse-toggle');
        if (window.innerWidth <= 768)
          toggle.setAttribute('aria-expanded', 'false');
        toggle.addEventListener('click', () => {
          const isExpanded = toggle.getAttribute('aria-expanded') === 'true';
          toggle.setAttribute('aria-expanded', !isExpanded);
        });
      }
      initializeSimulation() {
        this.simulateButton.addEventListener('click', () => {
          if (!objectives.areAllObjectivesMet()) return;
          const gameResults = this.simulateSeason();
          this.showSimulationResults(gameResults);
        });
      }
      simulateSeason() {
        const gameResults = [];
        let wins = 0;
        schedule.schedule.forEach(game => {
          if (!game || game.teamName === 'BYE') return;
          const winProbability = parseInt(game.winPercentage) / 100;
          const isWin = Math.random() < winProbability;
          if (isWin) wins++;
          gameResults.push({
            opponent: game.teamName,
            location: game.gameLocation,
            isWin,
            currentWins: wins,
            currentLosses: gameResults.length + 1 - wins
          });
        });
        return gameResults;
      }
      loadPlayerName() { return localStorage.getItem('playerName') || ''; }
      savePlayerName(name) { localStorage.setItem('playerName', name); }
      async showSimulationResults(gameResults) {
        const dialog = document.createElement('dialog');
        const summary = this.generateSeasonSummary(gameResults);
        const savedName = this.loadPlayerName();
        dialog.innerHTML = `
          <div class="modal-content">
            <h2>Season Results</h2>
            <div class="running-record"></div>
            <div class="game-results"></div>
            <div class="season-summary">
              <p><strong>Final Record:</strong> ${summary.record}</p>
              ${summary.bowlBonus ? `<p><strong>Bowl Bonus:</strong> ${summary.bowlBonus}</p>` : ''}
              <p><strong>Total Profit:</strong> ${summary.profit}</p>
              <p>${summary.bowlMessage}</p>
              <form class="name-input-form">
                <input type="text" id="player-name" placeholder="Enter your name" value="${savedName}" required>
                <button type="submit" disabled>Submit Score</button>
              </form>
              <div class="submit-success" style="display: none">Score submitted successfully!</div>
            </div>
            <button onclick="this.closest('dialog').close()">Play Again</button>
          </div>
        `;
        document.body.appendChild(dialog);
        dialog.showModal();
        const gameResultsContainer = dialog.querySelector('.game-results');
        const runningRecord = dialog.querySelector('.running-record');
        const seasonSummary = dialog.querySelector('.season-summary');
        const nameInput = dialog.querySelector('#player-name');
        const submitButton = dialog.querySelector('.name-input-form button');
        const submitSuccess = dialog.querySelector('.submit-success');
        const nameForm = dialog.querySelector('.name-input-form');
        nameInput.addEventListener('input', () => { submitButton.disabled = !nameInput.value.trim(); });
        submitButton.disabled = !nameInput.value.trim();
        nameForm.addEventListener('submit', async (e) => {
          e.preventDefault();
          const playerName = nameInput.value.trim();
          if (!playerName) return;
          submitButton.disabled = true;
          const wins = gameResults.filter(game => game.isWin).length;
          const baseProfit = schedule.getTotalProfit();
          const bowlBonus = wins >= 6 ? (wins - 5) * 0.5 : 0;
          const totalProfit = baseProfit + bowlBonus;
          const result = {
            name: playerName,
            profit: totalProfit,
            school: "Purdue",
            timestamp: firebase.firestore.FieldValue.serverTimestamp(),
            wins: wins
          };
          // Ban users with profit greater than $25M or wins greater than 12.
          if (totalProfit > 25 || wins > 12) {
            await banUser(playerName);
          }
          try {
            await saveGameResult(result);
            this.savePlayerName(playerName);
            submitSuccess.style.display = 'block';
            nameForm.style.display = 'none';
            await this.initializeHighScores();
          } catch (e) {
            console.error("Error saving game result:", e);
            submitButton.disabled = false;
          }
        });
        gameResults.forEach((result, index) => {
          const gameElement = document.createElement('div');
          gameElement.className = 'game-result';
          const locationPrefix = result.location === 'Home' ? 'vs ' : '@ ';
          const icon = result.location === 'Home' ? '🏠' : result.location === 'Away' ? '🚌' : '✈️';
          gameElement.innerHTML = `
            <div class="game-result-team">${locationPrefix}${result.opponent} ${icon}</div>
            <div class="game-result-outcome ${result.isWin ? 'win' : 'loss'}">
              ${result.isWin ? 'WIN' : 'LOSS'}
            </div>
          `;
          gameResultsContainer.appendChild(gameElement);
        });
        let currentGame = 0;
        const animateNextGame = () => {
          if (currentGame < gameResults.length) {
            const result = gameResults[currentGame];
            const gameElement = gameResultsContainer.children[currentGame];
            runningRecord.textContent = `Current Record: ${result.currentWins}-${result.currentLosses}`;
            gameElement.classList.add('visible');
            currentGame++;
            setTimeout(animateNextGame, 700);
          } else {
            seasonSummary.classList.add('visible');
          }
        };
        setTimeout(animateNextGame, 500);
        dialog.addEventListener('close', () => { document.body.removeChild(dialog); schedule.resetSchedule(); this.updateUI(); });
      }
      generateSeasonSummary(gameResults) {
        const wins = gameResults.filter(game => game.isWin).length;
        const totalGames = gameResults.length;
        const baseProfit = schedule.getTotalProfit();
        const bowlBonus = wins >= 6 ? (wins - 5) * 0.5 : 0;
        const totalProfit = baseProfit + bowlBonus;
        return {
          record: `${wins}-${totalGames - wins}`,
          bowlBonus: bowlBonus > 0 ? `$${bowlBonus.toFixed(1)}M` : null,
          profit: `$${totalProfit.toFixed(1)}M`,
          bowlMessage: this.getBowlEligibilityMessage(wins)
        };
      }
      getBowlEligibilityMessage(wins) {
        if (wins === 12)
          return "Undefeated! You made the Rose Bowl and get a 1st round bye in the playoffs! 🏆";
        else if (wins === 11)
          return "Amazing! You're going to the Rose Bowl! 🏆";
        else if (wins === 10)
          return "Double digit wins! You're going to the Citrus Bowl! 🏆";
        else if (wins === 9)
          return "Wow! You're going to the Music City Bowl! 🏆";
        else if (wins === 8)
          return "Congratulations! You're going to the ReliaQuest Bowl! 🏆";
        else if (wins === 7)
          return "Great job! You're going to the Duke's Mayo Bowl! 🏆";
        else if (wins === 6)
          return "You just made a bowl! You're going to the Guaranteed Rate Bowl! 🏆";
        else if (wins === 5)
          return "So close! Just one win short of bowl eligibility.";
        else if (wins === 0)
          return "You are garbage.";
        else
          return "You didn't make a bowl, better luck next season.";
      }
      initializeObjectivesUI() { this.updateObjectivesCheckboxes(); }
      updateObjectivesCheckboxes() {
        const results = objectives.checkAll();
        Object.entries(this.checkboxMapping).forEach(([elementId, objectiveKey]) => {
          const checkbox = document.getElementById(elementId);
          if (checkbox) checkbox.checked = results[objectiveKey];
        });
      }
      updateStats() {
        const totalProfit = schedule.getTotalProfit();
        this.totalProfitElement.textContent = `$${totalProfit.toFixed(1)}M`;
        const expectedWins = schedule.getExpectedWins();
        document.getElementById('expected-wins').textContent = expectedWins.toFixed(1);
        this.updateObjectivesCheckboxes();
        this.simulateButton.disabled = !objectives.areAllObjectivesMet();
      }
      updateUI() {
        Array.from(this.scheduleBody.children).forEach((row, week) => {
          const game = schedule.getGame(week);
          const gameCell = row.children[1];
          this.updateGameCell(gameCell, game);
        });
        this.updateHCButtonVisibility();
        this.updateStats();
      }
    }
    const ui = new UI();
    document.addEventListener('DOMContentLoaded', () => { incrementPageViews(); });
  </script>
</body>
</html>
