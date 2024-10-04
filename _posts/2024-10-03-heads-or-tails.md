---
title: "Heads or Tails Game"
layout: single
excerpt_separator: "<!--more-->"
categories:
  - blog
  - games
tags:
  - games
  - firebase
  - html
  - javascript
date: 2024-10-03 10:00:00 EST
timezone: America/New_York
search: true
toc: true
---

I wanted to find out if it was possible to make a browser-based game hosted on this site, so I decided to make a proof of concept. To keep it simple, I decided the "game" would be flipping a coin! See how many heads you can get in a row by **playing the game [here](/pages/heads_or_tails.md)!**

## About the Game

This logic for the game is all defined using some simple **html** and **javascript**. In order to remove the styling that comes with this site's blog template, I just needed to wrap the html file in a markdown (.md) file with the header:
'''html
---
layout: none
permalink: /heads_or_tails/
---
'''

The tricky part of this project was getting the leaderboard functioning. Since this is a static site, there isn't any way to have a server in the traditional sense. Instead, I just connected to a firestore database. All of the data going to/from the database is handled client-side to work with the static site functionality.
<br>
If you like this type of project or have an idea for a different game, let me know in the comments! Or check out more games by filtering blog posts by "games".
