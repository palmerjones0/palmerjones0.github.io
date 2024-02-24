---
title: "What If? Fantasy Football in 1966"
excerpt_separator: "<!--more-->"
categories:
  - blog
  - sports
tags:
  - sports
  - fantasyfootball
  - football
  - Python
  - pandas
  - jupyter notebook
  - analytics
  - history
  - What If?
date: 2024-02-15 10:00:00 EST
timezone: America/New_York
search: true
toc: true
---

While Fantasy Football may seem like a mainstay to football fans today, it didn’t really gain widespread popularity until the late 1990s and early 2000s. In fact, the now popular Points Per Reception (PPR) scoring format wasn’t even introduced until 2003, when it was conceived by the “Tout Wars” leagues as an experimental format. I can’t help but wonder, what would fantasy football have looked like in the very first season of the Super Bowl era?

In this post I’m going to assemble the best starting lineup for a fantasy football team in 1966 using a half-PPR format, while also discussing some history about the 1966 NFL season.

## A Bit of History
On June 8th, 1966 the NFL’s 14 teams and the 8 teams of the AFL announced an impending merger. While this merger immediately combined the recruitment of players between the 2 leagues, regular season matchups between NFL and AFL opponents wouldn’t occur until 1970 when the leagues merged completely. However, the league officials did agree to host a championship game between the winner of each league at a neutral site. This game between the Green Bay Packers of the NFL and Kansas City Chiefs of the AFL was played on January 15th, 1976 at the Los Angeles Memorial Coliseum. Led by their legendary coach Vince Lombardi (for whom the winning trophy is now named), the Packers won convincing 35-10 in front of a crowd of 61,946, cementing their legacy as the inaugural winners of what we now call the Super Bowl.

## Finding the Best Possible Team

### Scoring
I’m going to be using the settings of my personal fantasy football league hosted on the Sleeper platform. This is a half-point per reception league, where each team can start 1 QB, 2 RBs, 2 WRs, 1 TE, and 3 flex players (can be any combination of RBs, WRs, and TEs). This format balances the scoring of today’s pass-heavy NFL very well, although I suspect that the run-first style of most 1960s teams will result in at least 2 of the 3 flex spots being taken by RBs. Toggle the menu below to see the detailed scoring settings. 
<br><br>
I didn’t include defenses because I didn’t want to have to comb through week-by-week data for this analysis. Many of the members of my fantasy league have lobbied to not include defenses in our league anyway, so I guess this is a nod to them!

| Statistic | Score |
| ----------- | ----------- |
| Passing Yards | + 0.04 |
| Passing TDs | + 4 |
| Interceptions | - 2 |
| Rushing Yards | + 0.1 |
| Rushing TDs | + 6 |
| Fumbles | - 2 |
| Receptions | + 0.5 |
| Receiving Yards | + 0.1 |
| Receiving TDs | + 6 |
| 2 Point Conversion (Pass, Run, or Catch) | + 2 |

Quick note, my dataset doesn’t differentiate between fumbles lost vs recovered, so I’m treating any fumble as a fumble lost for simplicity. Also, the listed positions in 1966 are very different than today (split end, flanker, etc.), so I manually mapped these to what I would consider the closest modern-day equivalents. It’d be interesting to think about how fantasy leagues would’ve been structured in 1966 with all of these different positions! Lastly, I was not able to find any data on pick-6s thrown, so I’ll be omitting those from this analysis.

### Side-note About NFL vs AFL Scoring
While preparing the research for this post, I came across a couple wrinkles in the scoring between the NFL and AFL. First off, while the NFL didn’t implement the 2-point conversion until the 1994 season, it had been in use by the AFL from their conception in 1960 up until their completed merger with the NFL in 1970. This gives a slight edge to the 9 AFL players who successfully completed a 2-point conversion in 1966, including one scored by… Tony Romeo? That’s another rabbit-hole for this Cowboys fan to dive down later.
Another quirk of the complete separation between the two leagues in 1966 is that the AFL only had 9 teams compared to the 15 teams of the NFL (the NFL and AFL each added an expansion team in 1966, the Atlanta Falcons and Miami Dolphins, respectively). This means that although every team played 14 games over a 16 week season, the AFL teams had more repeat matchups. I’m not sure if this would have an impact on scoring.
Lastly, it’s worth noting that the upstart AFL had a reputation for outsized spending on star players instead of more evenly spending across the entire team. I believe this could result in those star AFL players having more statistical success given they played against worse overall competition throughout the season, although this premise is difficult to test given that there was only one cross-league matchup the entire year! Still, I’d be surprised if our starting lineup doesn’t end up featuring a QB and at least 1 WR from the AFL.

### Performing the Analysis
I used stats provided by pro-football-reference.com to perform this analysis. The website allows you to export the season statistics by relevant area (passing, rushing, etc.) which makes it fairly straightforward to use in python. There was a bit of pre-processing required on the data,  but this was all easy to do in python using the pandas package. I ended up doing all of the heavy lifting in a Jupyter notebook, and saving the results for each position as csv files. Take a look at the notebook with markdown comments here, or use the dropdown below to look at the python block (I removed the markdown sections because they don’t render well on this page). I’ll give a rundown of the results, but if you want to take a look for yourself or use this for your own analysis you can find the csv files on my github page.

```python
pip install pandas

import pandas as pd
from functools import reduce #also need this tool for joining more than 2 dataframes at a time

# create a dataframe (or series) that has the columns with the scoring rules coeffecients
scoringCoefs = pd.DataFrame([[0.04, 4.0, 2.0, -2.0, 0.1, 6.0, -2.0, 0.5, 0.1, 6.0]],
                            columns=['PassYds', 'PassTD', '2PM', 'Int', 'RushYds', 'RushTD', 'Fmb', 'Rec', 'RecYds', ‘RecTD'])

# NFL Stats
NFL_Passing = pd.read_csv('NFL_passing_1966.csv')
NFL_Rushing = pd.read_csv('NFL_rushing_1966.csv')
NFL_Receiving = pd.read_csv('NFL_receiving_1966.csv')

# AFL Stats
AFL_Passing = pd.read_csv('AFL_passing_1966.csv')
AFL_Rushing = pd.read_csv('AFL_rushing_1966.csv')
AFL_Receiving = pd.read_csv('AFL_receiving_1966.csv')
AFL_Scoring = pd.read_csv('AFL_scoring_1966.csv') # this is only being used because the 2-point conversion stats don't show up on the other tables (only the AFL had a 2-point conversion in the rules in 1966)

# Combine the AFL stats onto the NFL stats (these were separated because in 1996 these were funtionally different leagues)
NFL_Passing = pd.concat([NFL_Passing, AFL_Passing])
NFL_Rushing = pd.concat([NFL_Rushing, AFL_Rushing])
NFL_Receiving = pd.concat([NFL_Receiving, AFL_Receiving])
NFL_Scoring = AFL_Scoring # only the AFL had a 2-point conversion in 1966, no need to combine with NFL

# Ok, before we merge these 4 dataframes together, let's just filter out the columns we really care about. This will make merging them a lot easier later.
# Note: We have to rename some columns because they have the same name between tables (like "yards" being used on passing and rushing).
NFL_Passing = NFL_Passing[["Player-additional", "Player", "Tm", "Age", "Pos", "G", "Cmp", "Att", "Yds", "TD", "Int"]]
NFL_Passing = NFL_Passing.rename(columns={"Att": "PassAtt", "Yds": "PassYds", "TD": "PassTD"})

NFL_Rushing = NFL_Rushing[["Player-additional", "Player", "Tm", "Age", "Pos", "G", "Att", "Yds", "TD", "Fmb"]]
NFL_Rushing = NFL_Rushing.rename(columns={"Att": "RushAtt", "Yds": "RushYds", "TD": "RushTD", "Fmb": "RushFmb"})

NFL_Receiving = NFL_Receiving[["Player-additional", "Player", "Tm", "Age", "Pos", "G", "Rec", "Yds", "TD", "Fmb"]]
NFL_Receiving = NFL_Receiving.rename(columns={"Yds": "RecYds", "TD": "RecTD", "Fmb": "RecFmb"})

NFL_Scoring = NFL_Scoring[["Player-additional", "Player", "Tm", "Age", "Pos", "G", "2PM"]]

# Finally, we'll combine these 4 dataframes into 1 dataframe with a merge
NFL_Player = reduce(lambda  left,right: pd.merge(left,right,on=['Player-additional', 'Player', 'Tm', 'Age', 'Pos', 'G'],
                                            how='outer', copy=False), [NFL_Passing, NFL_Rushing, NFL_Receiving, NFL_Scoring])

print(NFL_Player)

# Add a new column to the NFL_Player dataframe, and go row by row multiplying out the scores and adding them to the new column

# Adding new column
NFL_Player["Score"] = 0
NFL_Player = NFL_Player.fillna(0)

# for loop going row by row
for i in NFL_Player.index:
    
    NFL_Player.loc[i, 'Score'] += (NFL_Player.loc[i, 'PassYds'] * scoringCoefs.loc[0, 'PassYds'])
    NFL_Player.loc[i, 'Score'] += (NFL_Player.loc[i, 'PassTD'] * scoringCoefs.loc[0, 'PassTD'])
    NFL_Player.loc[i, 'Score'] += (NFL_Player.loc[i, '2PM'] * scoringCoefs.loc[0, '2PM'])
    NFL_Player.loc[i, 'Score'] += (NFL_Player.loc[i, 'Int'] * scoringCoefs.loc[0, 'Int'])
    NFL_Player.loc[i, 'Score'] += (NFL_Player.loc[i, 'RushYds'] * scoringCoefs.loc[0, 'RushYds'])
    NFL_Player.loc[i, 'Score'] += (NFL_Player.loc[i, 'RushTD'] * scoringCoefs.loc[0, 'RushTD'])
    NFL_Player.loc[i, 'Score'] += (max(NFL_Player.loc[i, 'RushFmb'], NFL_Player.loc[i, 'RecFmb']) * scoringCoefs.loc[0, 'Fmb'])
    NFL_Player.loc[i, 'Score'] += (NFL_Player.loc[i, 'Rec'] * scoringCoefs.loc[0, 'Rec'])
    NFL_Player.loc[i, 'Score'] += (NFL_Player.loc[i, 'RecYds'] * scoringCoefs.loc[0, 'RecYds'])
    NFL_Player.loc[i, 'Score'] += (NFL_Player.loc[i, 'RecTD'] * scoringCoefs.loc[0, 'RecTD'])
    
    # rounding to 2 decimal places
    NFL_Player.loc[i, 'Score'] = round(NFL_Player.loc[i, 'Score'],2)
    
    # changing positions to fit modern descriptions
    if NFL_Player.loc[i, 'Pos'] in ['FL', 'LE', 'SE', 'LE/TE', 'FL/HB']:
        NFL_Player.loc[i, 'Pos'] = 'WR'
    if NFL_Player.loc[i, 'Pos'] in ['E', 'TE/FL', 'TE/LE']:
        NFL_Player.loc[i, 'Pos'] = 'TE'
    if NFL_Player.loc[i, 'Pos'] in ['HB', 'FB', 'HB/FB', 'FB/RB']:
        NFL_Player.loc[i, 'Pos'] = 'RB'
    
    # printing
    print(NFL_Player.loc[i, 'Pos'], NFL_Player.loc[i, 'Score'], NFL_Player.loc[i, ‘Player'])

# Filter by position, and save results to CSV file

NFL_QBs = NFL_Player.query("Pos == 'QB'").sort_values(by=['Score'], ascending=False)
print(NFL_QBs)
NFL_QBs.to_csv('QBs_1966.csv', index=False)

NFL_RBs = NFL_Player.query("Pos == 'RB'").sort_values(by=['Score'], ascending=False)
print(NFL_RBs)
NFL_RBs.to_csv('RBs_1966.csv', index=False)

NFL_WRs = NFL_Player.query("Pos == 'WR'").sort_values(by=['Score'], ascending=False)
print(NFL_WRs)
NFL_WRs.to_csv('WRs_1966.csv', index=False)

NFL_TEs = NFL_Player.query("Pos == 'TE'").sort_values(by=['Score'], ascending=False)
print(NFL_TEs)
NFL_TEs.to_csv('TEs_1966.csv', index=False)
```



## Results

Without further ado, let’s take a look at our starting roster…

### Starting Roster
| Position | Player | Score | Team | Age | Season Accolades |
| ----------- | ----------- | ----------- | ----------- | ----------- | ----------- |
| QB | Don Meredith | 222.4 | Dallas Cowboys | 28 | Pro Bowl, 5 rushing TDs (2nd among QBs) |
| RB | Leroy Kelly | 260.7 | Cleveland Browns | 24 | First Team All-Pro, Pro Bowl, 15 rushing TDs (1st) |
| RB | Gale Sayers | 241.1 | Chicago Bears | 23 | First Team All-Pro, Pro Bowl |
| WR | Lance Alworth | 253.8 | San Diego Chargers | 26 | First Team All-Pro, Pro Bowl, 13 receiving TDs (T-1st), 1383 receiving yards (1st) |
| WR | Bob Hayes | 227.1 | Dallas Cowboys | 24 | First Team All-Pro, Pro Bowl, 13 receiving TDs (T-1st) |
| TE | John Mackey | 161.3 | Baltimore Colts | 25 | First Team All-Pro, Pro Bowl |
| *RB* | Dan Reeves | 237.8 | Dallas Cowboys | 22 | 8 receiving TDs (most among RBs) |
| *WR* | Charley Frazier | 213.4 | Houston Oilers | 27 | Pro Bowl |
| *RB* | Jim Nance | 212.1 | Boston Patriots | 24 | First Team All-Pro, Pro Bowl |

### Conclusions
Well, I was wrong about having an AFL quarterback. I thought Joe Namath would be the runaway QB1, but he didn’t have nearly enough touchdowns to compliment his impressive 3379 passing yards. Don Meredith was a bit of a surprise, considering he doesn’t lead in any passing categories. It was Don’s rushing acumen that allowed him to sneak past the next-closest QB, Frank Ryan. He wasn’t the only Cowboys whose cross-position stats helped him earn a spot, Dan Reeves was also greatly helped by 8 receiving TDs. At least there were 2 AFL WRs included in Lance Alworth and Charley Frazier.
<br><br>
It’s also interesting to notice how dramatically the role of the TE has changed since 1966. John Mackey was far and away the lowest scorer on our starting squad. His 161.3 points would only put him at #12 if he was on the receivers list!
<br><br>
This was a really interesting analysis to put together, I’d be interested to try other years before or after 1966, or even expand into college football! Thanks for taking the time to read this post. If you liked it please share with others! You’re welcome to freely use this for a future project, just please include a reference or shoutout to this page, and understand that the base data came from pro-football-reference.com so their license still applies as well.
