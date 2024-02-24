# Fantasy Football in 1966

## Introduction
This is a quick project that was used in a [post](https://www.palmercjones.com) on [my website](https://www.palmercjones.com), the premise is "If fantasy football was popularized in 1966, what would the best starting roster be?" Go check out the post if you want to know some more of the history of the NFL/AFL in 1966 and the idea behind this analysis. If you're just here for the data science then continue on below!

## Tools needed for this analysis
I used the [pro-football-reference.com](https://www.pro-football-reference.com/years/1966/) database, ~~which is conveniently accessible in python through the [sportsipy](https://github.com/davidjkrause/sportsipy) package (see below). This package doesn't have great documentation for beginners, I recommend asking ChatGPT for help in determining what data is available. Also, I had issues with it not being maintained very well as of February 2024, I ended up using a fork made by [davidjkrause](https://github.com/davidjkrause). Huge thanks to David!~~ Update: pro-football-reference.com has instituted a limit on the number of requests you can make per minute, which basically makes it unusable for the purpose of ths project. A much more straightforward approach was to simply go to the website itself and download the relevant data as csv files. These are available on my github, the original licenses and fair use policies apply from pro-football-reference.com. For scoring, I used the setting from my personal league which is hosted through [Sleeper](https://sleeper.com/) (This is a half-point per reception league).

#### Install Pandas
We will be using pandas to work with our data, let's go ahead and install and import that now.


```python
pip install pandas
```

    Requirement already satisfied: pandas in /Users/palmerjones/opt/anaconda3/lib/python3.9/site-packages (2.2.0)
    Requirement already satisfied: numpy<2,>=1.22.4 in /Users/palmerjones/opt/anaconda3/lib/python3.9/site-packages (from pandas) (1.26.4)
    Requirement already satisfied: pytz>=2020.1 in /Users/palmerjones/opt/anaconda3/lib/python3.9/site-packages (from pandas) (2022.1)
    Requirement already satisfied: python-dateutil>=2.8.2 in /Users/palmerjones/opt/anaconda3/lib/python3.9/site-packages (from pandas) (2.8.2)
    Requirement already satisfied: tzdata>=2022.7 in /Users/palmerjones/opt/anaconda3/lib/python3.9/site-packages (from pandas) (2023.2)
    Requirement already satisfied: six>=1.5 in /Users/palmerjones/opt/anaconda3/lib/python3.9/site-packages (from python-dateutil>=2.8.2->pandas) (1.16.0)
    Note: you may need to restart the kernel to use updated packages.



```python
import pandas as pd
from functools import reduce #also need this tool for joining more than 2 dataframes at a time
```

    /Users/palmerjones/opt/anaconda3/lib/python3.9/site-packages/pandas/core/computation/expressions.py:21: UserWarning: Pandas requires version '2.8.4' or newer of 'numexpr' (version '2.8.3' currently installed).
      from pandas.core.computation.check import NUMEXPR_INSTALLED
    /Users/palmerjones/opt/anaconda3/lib/python3.9/site-packages/pandas/core/arrays/masked.py:60: UserWarning: Pandas requires version '1.3.6' or newer of 'bottleneck' (version '1.3.5' currently installed).
      from pandas.core import (


#### Defining the scoring system
Let's define all of the stats that we care about and their respective scoring attributes:
- (+0.04) Passing Yard
- (+4.0) Passing TD
- (+2.0) 2-Point Conversion (pass, run, or reception)
- (-2.0) Pass Intercepted
- ~~(-1.0) Pick 6 Thrown~~ *wasn't able to find data on pick 6s*
- (+0.1) Rushing Yard
- (+6.0) Rushing TD
- (-2.0) Fumble Lost
<br> *I was only able to find the total number of fumbles (not specifically lost) and this also counts special teams fumbles. Best I could do!*
- (+0.5) Reception
- (+0.1) Receiving Yard
- (+6.0) Receiving TD
- ~~(+6.0) Defensive TD~~
- ~~(+10.0) 0 Points Allowed~~
- ~~(+6.0) 1-6 Points Allowed~~
- ~~(+4.0) 7-13 Points Allowed~~
- ~~(+2.0) 14-20 Points Allowed~~
- ~~(+0.0) 21-27 Points Allowed~~
- ~~(-1.0) 28-34 Points Allowed~~
- ~~(-4.0) 35+ Points Allowed~~
- ~~(+1.0) Sack~~
- ~~(+2.0) Interception~~
- ~~(+2.0) Fumble Recovery~~
- ~~(+2.0) Safety~~
- ~~(+1.0) Forced Fumble~~
- ~~(+2.0) Blocked Kick~~
- ~~(+6.0) Fumble Recovery TD~~
<br>
*Removed the defensive stats because I decided not do all the week-by-week calculations for defenses*

Now let's load this data into a dataframe. This will serve as a good reference point for how we want to structure our player data later on.


```python
# create a dataframe (or series) that has the columns with the scoring rules coeffecients
scoringCoefs = pd.DataFrame([[0.04, 4.0, 2.0, -2.0, 0.1, 6.0, -2.0, 0.5, 0.1, 6.0]],
                            columns=['PassYds', 'PassTD', '2PM', 'Int', 'RushYds', 'RushTD', 'Fmb', 'Rec', 'RecYds', 'RecTD'])
```

#### Player Data
Let's the data that was manually scraped from pro-football-reference.com. Note, the NFL and AFL data was in separate pages so we'll need to merge them.


```python
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

```

        Player-additional           Player   Tm  Age Pos   G   Cmp  PassAtt  \
    0            AlleDu00      Duane Allen  CHI   29  TE  11   NaN      NaN   
    1            AlliJi00      Jim Allison  SDG   23  RB  14   NaN      NaN   
    2            AlwoLa00  Lance Alworth*+  SDG   26  FL  13   NaN      NaN   
    3            AndeBi00    Bill Anderson  GNB   30  TE  10   NaN      NaN   
    4            AndeDo00   Donny Anderson  GNB   23  RB  14   NaN      NaN   
    ..                ...              ...  ...  ...  ..  ..   ...      ...   
    381          WoodDi00        Dick Wood  MIA   30  QB  14  83.0    230.0   
    382          WoodGa00        Gary Wood  NYG   24  QB  14  81.0    170.0   
    383          WoodTo01   Tom Woodeshick  PHI   25  RB  14   NaN      NaN   
    384          WrigLo20    Lonnie Wright  DEN   21  SS  14   NaN      NaN   
    385          YewcTo00       Tom Yewcic  BOS   34   P   7   NaN      NaN   
    
         PassYds  PassTD   Int  RushAtt  RushYds  RushTD  RushFmb   Rec  RecYds  \
    0        NaN     NaN   NaN      NaN      NaN     NaN      NaN   3.0    28.0   
    1        NaN     NaN   NaN     31.0    213.0     2.0      1.0  12.0    99.0   
    2        NaN     NaN   NaN      3.0     10.0     0.0      0.0  73.0  1383.0   
    3        NaN     NaN   NaN      NaN      NaN     NaN      NaN   2.0    14.0   
    4        NaN     NaN   NaN     25.0    104.0     2.0      3.0   2.0    33.0   
    ..       ...     ...   ...      ...      ...     ...      ...   ...     ...   
    381    993.0     4.0  14.0      5.0      6.0     1.0      2.0   NaN     NaN   
    382   1142.0     6.0  13.0     28.0    196.0     3.0      4.0   NaN     NaN   
    383      NaN     NaN   NaN     85.0    330.0     4.0      2.0  10.0   118.0   
    384      NaN     NaN   NaN      NaN      NaN     NaN      NaN   1.0    -2.0   
    385      NaN     NaN   NaN      1.0     -5.0     0.0      1.0   NaN     NaN   
    
         RecTD  RecFmb  2PM  
    0      0.0     0.0  NaN  
    1      0.0     1.0  NaN  
    2     13.0     0.0  NaN  
    3      0.0     0.0  NaN  
    4      0.0     3.0  NaN  
    ..     ...     ...  ...  
    381    NaN     NaN  NaN  
    382    NaN     NaN  NaN  
    383    1.0     2.0  NaN  
    384    0.0     0.0  NaN  
    385    NaN     NaN  NaN  
    
    [386 rows x 20 columns]



```python
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
    print(NFL_Player.loc[i, 'Pos'], NFL_Player.loc[i, 'Score'], NFL_Player.loc[i, 'Player'])
    


```

    TE 4.3 Duane Allen
    RB 47.2 Jim Allison
    WR 253.8 Lance Alworth*+
    TE 2.4 Bill Anderson
    RB 20.7 Donny Anderson
    TE 42.5 Taz Anderson
    TE 67.5 Fred Arbanas+
    RB 27.0 Jon Arnett
    RB 128.7 Willie Asbury
    RB -3.0 Pervis Atkins
    LHB 112.9 Joe Auer
    RCB/FS 0.0 Bill Baird
    K 1.5 Sam Baker
    WR 112.8 Gary Ballman
    RB 3.4 Pete Banaszak
    RB 10.1 Billy Ray Barnes
    WR 27.3 Gary Barnes
    RB 5.0 Tom Barrington
    RB 189.9 Dick Bass*
    WR 16.0 Glenn Bass
    QB 46.32 Pete Beathard
    LLB 0.0 Bobby Bell*+
    RB 14.7 Joe Bellino
    QB 3.8 Bob Berry
    WR 148.6 Raymond Berry
    RDE 0.0 Verlon Biggs*
    WR 49.7 Fred Biletnikoff
    TE -0.4 Charlie Bivins
    QB 90.66 George Blanda
    RB 64.4 Sid Blanks
    RB 92.8 Emerson Boozer*
    RLB 0.0 John Bramlett*
    QB 35.46 Zeke Bratkowski
    QB 144.2 John Brodie
    K 0.0 Tommy Brooker
    RB 165.3 Bill Brown
    RB 136.4 Timmy Brown
    RB 3.1 Charlie Bryant
    QB 69.72 Rudy Bukich
    RB 27.2 Amos Bullocks
    RB 52.04 Ronnie Bull
    WR 150.8 Chris Burford
    WR 54.8 Vern Burke
    RB 177.5 Bobby Burnett*
    WR 1.4 John Burrell
    RB 121.46 Ode Burrell
    RB 32.7 Cannonball Butler
    RCB 0.0 Butch Byrd*+
    TE 62.6 Billy Cannon
    RB 16.8 Bob Cappadona
    WR 125.1 Gino Cappelletti*
    RB 136.1 Wray Carlton*
    TE 36.9 Reggie Carolan
    TE 89.8 Preston Carpenter
    RB 28.0 Rick Casares
    WR 98.2 Bernie Casey
    P 3.3 Don Chandler
    RB 8.3 George Chesser
    QB 44.9 Max Choboian
    QB 22.34 Dennis Claridge
    TE 77.4 Frank Clarke
    SS 0.0 Hagood Clarke
    LB/MLB 0.0 Doug Cline
    RB 126.92 Bert Coan
    RB 119.9 Junior Coffey
    WR 70.6 Gail Cogdill
    WR 9.3 Angelo Coia
    TE 34.4 Jim Colclough
    WR 198.4 Gary Collins*
    QB 35.18 Jack Concannon
    MLB 0.0 Dan Conners*
    WR 65.8 Bobby Joe Conrad
    TE 71.5 Paul Costa*
    MLB -0.2 Vince Costello
    DB 5.2 Claude Crabb
    WR 2.3 Eric Crabtree
    RB 38.1 Willis Crenshaw
    TE 36.7 Bobby Crespino
    WR 86.8 Bobby Crockett
    TE 17.8 Bill Cronin
    RB 119.24 John David Crow
    QB 29.86 Gary Cuozzo
    TE 148.1 Carroll Dale
    RB 209.3 Clem Daniels*+
    QB 11.7 Cotton Davidson
    K 4.3 Tommy Davis
    QB 191.78 Len Dawson*+
    TE 108.5 Al Denson
    WR 38.2 Buddy Dial
    TE 65.8 Mike Ditka
    RB 126.7 Hewritt Dixon*
    WR 51.7 Boyd Dowler
    WR 113.3 Elbert Dubenion
    LDT 0.0 Jim Dunaway*+
    RCB 0.0 Speedy Duncan*
    RB 4.2 Perry Lee Dunn
    K 0.0 Mike Eischeid
    WR 56.8 Larry Elkins
    LLB 0.0 Tom Erlandson*
    DE 0.0 Earl Faison
    RB 17.4 Bobby Felts
    TE 43.3 Charley Ferguson
    WR 120.7 Paul Flatley*
    TE 63.6 Marv Fleming
    QB 180.52 Tom Flores*
    RB 88.2 Gene Foster
    WR 1.3 Jason Franci
    WR 213.4 Charley Frazier*
    TE 30.9 Willie Frazier
    QB 125.2 Roman Gabriel
    WR 85.5 Billy Gambrell
    WR 110.9 Gary Garrison
    RB 3.3 J.D. Garrett
    RB 136.5 Larry Garron
    RB 137.1 Mike Garrett*
    RB 15.0 Walt Garrison
    RB 68.4 Prentice Gautt
    WR 66.9 Pete Gent
    TE 6.7 Jim Gibbons
    RB 47.7 Cookie Gilchrist
    QB 7.26 Scotty Glacken
    LE/FL 35.2 Ron Goodwin
    WR 26.7 Dick Gordon
    RB 18.0 Jim Grabowski
    WR 116.8 Art Graham
    SS 0.0 Kenny Graham+
    RB 67.2 Hoyle Granger
    QB 0.68 Charlie Green
    RB 192.0 Ernie Green*
    RB 118.0 Earl Gros
    K -0.28 Lou Groza
    QB 191.04 John Hadl
    RB 63.5 Roger Hagberg
    WR 48.6 Tom Hall
    RB 4.0 Charlie Harraway
    QB 1.16 Jim Hart
    WR 98.1 Alex Hawkins
    WR 15.3 Ben Hawkins
    WR 227.1 Bob Hayes*+
    RB 56.6 Wendell Hayes
    RB 93.4 Abner Haynes
    WR 12.7 Steve Heckard
    WR 15.1 John Henderson
    WR 62.8 Charley Hennigan
    WR 45.4 Fred Hill
    RB 39.8 Jerry Hill
    QB 24.64 King Hill
    TE 105.3 John Hilton
    RB 64.08 Dick Hoak
    RB 74.4 Paul Hornung
    LLB 7.5 Jim Houston
    QB 4.52 John Huarte
    FS 0.0 Jim Hudson
    QB 12.72 Buddy Humphrey
    LDT 0.0 Jim Lee Hunt*
    WR 3.3 Tom Hutchinson
    C 0.2 Ken Iman
    QB 0.6 George Izo
    WR 53.9 Frank Jackson
    RB 11.5 Jim Jackson
    RB 41.2 Allen Jacobs
    LCB 0.0 Tom Janik
    FS 0.0 Pete Jaquess
    WR 114.8 Roy Jefferson
    TE 2.8 Tony Jeter
    RB 43.3 Billy Joe
    QB 81.26 Charley Johnson
    TE 6.7 Curley Johnson
    RB 59.6 John Henry Johnson
    LT 0.6 Mitch Johnson
    QB 90.0 Randy Johnson
    RB 0.3 Rudy Johnson
    WR 178.7 Homer Jones
    WR 91.7 Jim Jones
    RB 15.7 Les Josephson
    QB 193.76 Sonny Jurgensen*
    RB 0.2 Joe Kantor
    RB 260.7 Leroy Kelly*+
    RB -0.7 Mike Kellogg
    QB 139.04 Jack Kemp*
    QB 37.52 Tom Kennedy
    P 1.88 Jon Kilgore
    QB 3.66 Billy Kilmer
    RB 13.76 Phil King
    TE 57.5 Dave Kocourek
    RB 42.1 Dave Kopay
    RB 16.9 Ernie Koy
    TE 28.6 Kent Kramer
    TE 61.7 Ron Kramer
    K 0.0 Gary Kroner
    TE 4.8 Jake Kupp
    RB 44.7 Ralph Kurek
    TE 97.0 Pete Lammons
    QB 30.56 Daryle Lamonica
    RB 44.64 Izzy Lang
    QB -3.22 Jacky Lee
    RB 11.7 Dick Leftridge
    RB 12.0 Darrell Lester
    RB 32.26 Dan Lewis
    RB 36.9 Gary Lewis
    RB 77.64 Keith Lincoln
    RB 61.6 Jim Lindsey
    RB 0.4 Mike Lind
    WR 8.3 Bob Long
    RB 50.9 Joe Don Looney
    RB 80.5 Tony Lorick
    P 2.2 Billy Lothridge
    RB 87.4 Paul Lowe
    K 0.0 Booth Lusteg
    TE 96.7 Jacque MacKinnon*
    TE 161.3 John Mackey*+
    WR 5.9 Bill Malinchak
    RB 0.5 Joe Marconi
    RB 68.4 Amos Marsh
    TE 47.5 Bill Martin
    RB 36.9 Tommy Mason
    RB 83.7 Bill Mathis
    RB 22.1 Ollie Matson
    RB 92.3 Tom Matte
    WR 0.5 Wes Matthews
    WR 138.0 Don Maynard
    WR 3.8 Fred Mazurek
    RB 142.0 Curtis McClinton*
    QB 27.92 John McCormick
    WR 106.9 Tommy McDonald
    WR 34.4 Kay McFarland
    TE 17.1 Max McGee
    TE 41.2 Marlin McKeever*
    TE 63.4 Bob McLeod
    RB 7.9 Bruce McLenna
    WR 22.4 Clifton McNeil
    FS 0.7 Eddie Meador*
    WR 1.6 Pep Menefee
    RB 55.4 Chuck Mercein
    K 0.0 Mike Mercer
    QB 222.4 Don Meredith*
    QB -1.84 Ron Meyer
    K 5.5 Gene Mingo
    QB 31.66 George Mira
    WR 194.44 Bobby Mitchell
    RB 56.8 Charley Mitchell
    TE 47.6 Tom Mitchell
    RCB 0.0 Willie Mitchell
    RB 71.4 Lenny Moore
    RB 123.3 Tom Moore
    TE 2.5 Doug Moreau
    TE 56.8 Milt Morin
    QB 43.4 Earl Morrall
    WR 168.9 Joe Morrison
    WR 7.4 Johnny Morris
    QB 24.0 Craig Morton
    QB 17.66 Bill Munson
    QB -2.0 Tom Myers
    QB 171.36 Joe Namath
    RB 212.1 Jim Nance*+
    QB 70.68 Bill Nelsen
    QB 13.9 Jim Ninowski
    QB 22.46 Terry Nofsinger
    WR 36.9 Karl Noonan
    TE 20.4 Pettis Norman
    WR 7.0 Don Norton
    QB 1.88 Rick Norton
    RB 139.8 Tom Nowatzke
    RG 0.7 Joe O'Donnell
    WR 98.3 Jimmy Orr
    RB 70.0 Dave Osborn
    QB 143.04 Babe Parilli*
    WR 160.3 Dave Parks*
    RB 153.2 Don Perkins*
    WR 89.4 Red Phillips
    RB 1.2 Brian Piccolo
    RB 3.7 Nick Pietrosante
    RB 150.3 Elijah Pitts
    WR 1.6 Frank Pitts
    QB 29.62 Milt Plum
    TE 19.1 Bob Poole
    WR 7.9 Bucky Pope
    WR 195.1 Art Powell*
    RB 13.1 Sammy Price
    WR 0.8 Bill Rademacher
    QB -2.5 Larry Rakestraw
    WR 42.3 Sonny Randle
    RB 5.9 Ron Rector
    LLB 1.4 Rick Redman
    RB 237.82 Dan Reeves
    FS 9.7 Mel Renfro*
    WR 0.0 Lance Rentzel
    TE 121.3 Pete Retzlaff
    QB 9.82 Jerry Rhome
    TE 13.5 Pat Richter
    WR 43.6 Willie Richardson
    RB 24.7 Preston Ridlehuber
    WR 76.9 Bo Roberson
    WR -3.1 Walter Roberts
    FS 0.0 Johnny Robinson*+
    WR 27.1 John Roderick
    RB 132.5 Johnny Roland*
    TE 7.6 Tony Romeo
    QB -0.4 Tobin Rote
    WR 23.0 Ed Rutkowski
    QB 212.56 Frank Ryan*
    WR 169.4 George Sauer*
    RB 241.12 Gale Sayers*+
    RB 6.9 Charlie Scales
    WR 85.0 Bob Scarpitto*
    SS 0.0 Goldie Sellers
    WR 13.3 Bob Sherlag
    QB -3.0 Dick Shiner
    RB 5.1 Roy Shivers
    WR 3.4 Del Shofner
    RB 3.2 Randy Schultz
    RB 15.8 Les Shy
    RB 1.3 Jimmy Sidle
    WR 13.8 Jerry Simmons
    QB 7.96 Mickey Slaughter
    QB -0.16 Steve Sloan
    RB 15.4 Allen Smith
    RB 9.4 Bobby Smith
    K 0.0 Fletcher Smith
    RB 7.5 J.D. Smith
    TE 120.3 Jackie Smith*
    TE 131.6 Jerry Smith
    TE 42.8 Ralph Smith
    QB 53.06 Ron Smith
    RB 37.8 Mark Smolinski
    QB 56.2 Norm Snead
    RB 165.0 Matt Snell*
    WR 98.4 Jack Snow
    LG/RG 1.2 Rick Sortun
    RB 39.4 Jack Spikes
    QB 148.68 Bart Starr*+
    TE 57.0 Monty Stickles
    RB 22.7 Jim Stiger
    QB 28.7 John Stofa
    RB 2.0 Donnie Stone
    SS 1.7 Jerry Stovall*
    RLB 0.0 Mike Stratton*+
    WR 186.1 Pat Studstill*+
    QB 76.26 Karl Sweetan
    QB 13.08 Mike Taliaferro
    QB 184.04 Fran Tarkenton
    HB/LE 252.1 Charley Taylor*
    RB 152.1 Jim Taylor
    WR 68.3 Lionel Taylor
    WR 208.0 Otis Taylor*+
    QB 32.1 Steve Tensi
    TE 113.8 Aaron Thomas
    RB 9.3 Gene Thomas
    RB 50.0 Steve Thurlow
    RB -3.4 Jim Todd
    WR 24.4 Larry Todd
    RB 17.8 Charley Tolar
    RB 4.1 Bill Triplett
    TE 45.9 Billy Truax
    QB 127.9 Don Trull
    WR 11.0 Bake Turner
    K 0.0 Jim Turner
    WR 17.8 Howard Twilley
    QB 150.32 Johnny Unitas*
    K 0.0 Dick Van Raaphorst*
    QB 1.78 Ron Vander Kelen
    K 2.3 Danny Villanueva
    QB 1.16 Billy Wade
    P 6.2 Bobby Walden
    WR 3.0 Willie Walker
    WR 122.1 Paul Warfield
    DB 0.0 Charley Warner
    LCB 0.0 Jimmy Warren*
    WR -3.1 Sammy Weir
    RCB 0.0 Dick Westmoreland
    TE 88.7 Jim Whalen
    RB 99.0 Ernie Wheelwright
    RB 78.3 A.D. Whitfield
    WR 11.8 J.R. Wilburn
    RB -1.8 Jeff Williams
    RB 160.4 Ken Willard*
    TE 16.2 Butch Wilson
    QB 40.26 George Wilson
    P 3.9 Jerrel Wilson
    RCB 0.0 Nemiah Wilson
    WR 22.5 Dick Witcher
    QB 30.32 Dick Wood
    QB 73.28 Gary Wood
    RB 75.8 Tom Woodeshick
    SS 0.3 Lonnie Wright
    P -2.5 Tom Yewcic


    /var/folders/w3/vc71t6x11q1d2h5r3c6ggq3r0000gn/T/ipykernel_40296/4276745264.py:17: FutureWarning: Setting an item of incompatible dtype is deprecated and will raise an error in a future version of pandas. Value '1.5' has dtype incompatible with int64, please explicitly cast to a compatible dtype first.
      NFL_Player.loc[i, 'Score'] += (NFL_Player.loc[i, 'Rec'] * scoringCoefs.loc[0, 'Rec'])



```python
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

        Player-additional            Player   Tm  Age Pos   G    Cmp  PassAtt  \
    235          MereDo00     Don Meredith*  DAL   28  QB  13  177.0    344.0   
    300          RyanFr00       Frank Ryan*  CLE   30  QB  14  200.0    382.0   
    175          JurgSo00  Sonny Jurgensen*  WAS   32  QB  14  254.0    436.0   
    86           DawsLe00      Len Dawson*+  KAN   31  QB  14  159.0    284.0   
    131          HadlJo00         John Hadl  SDG   26  QB  14  200.0    375.0   
    ..                ...               ...  ...  ...  ..  ..    ...      ...   
    236          MeyeRo00         Ron Meyer  PIT   22  QB   4    7.0     19.0   
    252          MyerTo00         Tom Myers  DET   23  QB   1    0.0      1.0   
    280          RakeLa00   Larry Rakestraw  CHI   24  QB   1    0.0      0.0   
    307          ShinDi00       Dick Shiner  WAS   24  QB  14    0.0      5.0   
    195          LeexJa00         Jacky Lee  HOU   27  QB   8    4.0      8.0   
    
         PassYds  PassTD  ...  RushAtt  RushYds  RushTD  RushFmb  Rec  RecYds  \
    235   2805.0    24.0  ...     38.0    242.0     5.0      8.0  0.0     0.0   
    300   2974.0    29.0  ...     36.0    156.0     0.0      5.0  0.0     0.0   
    175   3209.0    28.0  ...     12.0     14.0     0.0      5.0  0.0     0.0   
    86    2527.0    26.0  ...     24.0    167.0     0.0      5.0  0.0     0.0   
    131   2846.0    23.0  ...     38.0     95.0     2.0      4.0  2.0   -13.0   
    ..       ...     ...  ...      ...      ...     ...      ...  ...     ...   
    236     59.0     0.0  ...      1.0     -2.0     0.0      1.0  0.0     0.0   
    252      0.0     0.0  ...      0.0      0.0     0.0      0.0  0.0     0.0   
    280      0.0     0.0  ...      1.0     -5.0     0.0      1.0  0.0     0.0   
    307      0.0     0.0  ...      1.0     10.0     0.0      1.0  0.0     0.0   
    195     27.0     0.0  ...      1.0     -3.0     0.0      1.0  0.0     0.0   
    
         RecTD  RecFmb  2PM   Score  
    235    0.0     0.0  0.0  222.40  
    300    0.0     0.0  0.0  212.56  
    175    0.0     0.0  0.0  193.76  
    86     0.0     0.0  0.0  191.78  
    131    0.0     4.0  0.0  191.04  
    ..     ...     ...  ...     ...  
    236    0.0     0.0  0.0   -1.84  
    252    0.0     0.0  0.0   -2.00  
    280    0.0     0.0  0.0   -2.50  
    307    0.0     0.0  0.0   -3.00  
    195    0.0     0.0  0.0   -3.22  
    
    [67 rows x 21 columns]
        Player-additional          Player   Tm  Age Pos   G  Cmp  PassAtt  \
    177          KellLe00   Leroy Kelly*+  CLE   24  RB  14  0.0      1.0   
    302          SayeGa00   Gale Sayers*+  CHI   23  RB  14  2.0      6.0   
    284          ReevDa00      Dan Reeves  DAL   22  RB  14  3.0      6.0   
    254          NancJi00     Jim Nance*+  BOS   24  RB  14  0.0      0.0   
    83           DaniCl00  Clem Daniels*+  OAK   29  RB  14  0.0      3.0   
    ..                ...             ...  ...  ...  ..  ..  ...      ...   
    176          KantJo00      Joe Kantor  WAS   24  RB   4  0.0      0.0   
    178          KellMi01    Mike Kellogg  DEN   24  RB   8  0.0      0.0   
    374          WillJe01   Jeff Williams  MIN   23  RB   3  0.0      0.0   
    9            AtkiPe00   Pervis Atkins  OAK   31  RB  14  0.0      0.0   
    349          ToddJi00        Jim Todd  DET   23  RB  10  0.0      0.0   
    
         PassYds  PassTD  ...  RushAtt  RushYds  RushTD  RushFmb   Rec  RecYds  \
    177      0.0     0.0  ...    209.0   1141.0    15.0      1.0  32.0   366.0   
    302     58.0     0.0  ...    229.0   1231.0     8.0      2.0  34.0   447.0   
    284     48.0     0.0  ...    175.0    757.0     8.0      6.0  41.0   557.0   
    254      0.0     0.0  ...    299.0   1458.0    11.0      7.0   8.0   103.0   
    83       0.0     0.0  ...    204.0    801.0     7.0      7.0  40.0   652.0   
    ..       ...     ...  ...      ...      ...     ...      ...   ...     ...   
    176      0.0     0.0  ...      1.0      2.0     0.0      0.0   0.0     0.0   
    178      0.0     0.0  ...      6.0      3.0     0.0      1.0   1.0     5.0   
    374      0.0     0.0  ...      1.0      2.0     0.0      1.0   0.0     0.0   
    9        0.0     0.0  ...     14.0     10.0     0.0      2.0   0.0     0.0   
    349      0.0     0.0  ...      2.0      6.0     0.0      2.0   0.0     0.0   
    
         RecTD  RecFmb  2PM   Score  
    177    1.0     1.0  0.0  260.70  
    302    2.0     2.0  0.0  241.12  
    284    8.0     6.0  0.0  237.82  
    254    0.0     7.0  0.0  212.10  
    83     3.0     7.0  0.0  209.30  
    ..     ...     ...  ...     ...  
    176    0.0     0.0  0.0    0.20  
    178    0.0     1.0  0.0   -0.70  
    374    0.0     0.0  0.0   -1.80  
    9      0.0     0.0  0.0   -3.00  
    349    0.0     0.0  0.0   -3.40  
    
    [123 rows x 21 columns]
        Player-additional            Player   Tm  Age Pos   G  Cmp  PassAtt  \
    2            AlwoLa00   Lance Alworth*+  SDG   26  WR  13  0.0      0.0   
    138          HayeBo00       Bob Hayes*+  DAL   24  WR  14  0.0      0.0   
    107          FrazCh00  Charley Frazier*  HOU   27  WR  14  0.0      0.0   
    344          TaylOt00     Otis Taylor*+  KAN   24  WR  14  0.0      1.0   
    68           CollGa00     Gary Collins*  CLE   26  WR  14  0.0      0.0   
    ..                ...               ...  ...  ...  ..  ..  ...      ...   
    279          RadeBi00   Bill Rademacher  NYJ   24  WR   4  0.0      0.0   
    219          MattWe00      Wes Matthews  MIA   23  WR   4  0.0      0.0   
    286          RentLa00     Lance Rentzel  MIN   23  WR   9  0.0      0.0   
    293          RobeWa00    Walter Roberts  CLE   24  WR  14  0.0      0.0   
    368          WeirSa00        Sammy Weir  NYJ   25  WR  11  0.0      0.0   
    
         PassYds  PassTD  ...  RushAtt  RushYds  RushTD  RushFmb   Rec  RecYds  \
    2        0.0     0.0  ...      3.0     10.0     0.0      0.0  73.0  1383.0   
    138      0.0     0.0  ...      1.0     -1.0     0.0      3.0  64.0  1232.0   
    107      0.0     0.0  ...      0.0      0.0     0.0      0.0  57.0  1129.0   
    344      0.0     0.0  ...      2.0     33.0     0.0      0.0  58.0  1297.0   
    68       0.0     0.0  ...      2.0     38.0     0.0      0.0  56.0   946.0   
    ..       ...     ...  ...      ...      ...     ...      ...   ...     ...   
    279      0.0     0.0  ...      0.0      0.0     0.0      0.0   1.0     3.0   
    219      0.0     0.0  ...      0.0      0.0     0.0      0.0   1.0    20.0   
    286      0.0     0.0  ...      0.0      0.0     0.0      0.0   2.0    10.0   
    293      0.0     0.0  ...      0.0      0.0     0.0      0.0   2.0    19.0   
    368      0.0     0.0  ...      0.0      0.0     0.0      0.0   1.0     4.0   
    
         RecTD  RecFmb  2PM  Score  
    2     13.0     0.0  0.0  253.8  
    138   13.0     3.0  0.0  227.1  
    107   12.0     0.0  0.0  213.4  
    344    8.0     0.0  0.0  208.0  
    68    12.0     0.0  0.0  198.4  
    ..     ...     ...  ...    ...  
    279    0.0     0.0  0.0    0.8  
    219    0.0     1.0  0.0    0.5  
    286    0.0     1.0  0.0    0.0  
    293    0.0     3.0  0.0   -3.1  
    368    0.0     2.0  0.0   -3.1  
    
    [87 rows x 21 columns]
        Player-additional             Player   Tm  Age Pos   G  Cmp  PassAtt  \
    210          MackJo00      John Mackey*+  BAL   25  TE  14  0.0      0.0   
    82           DaleCa00       Carroll Dale  GNB   28  TE  14  0.0      0.0   
    321          SmitJe01        Jerry Smith  WAS   23  TE  14  0.0      0.0   
    287          RetzPe00      Pete Retzlaff  PHI   35  TE  14  0.0      0.0   
    320          SmitJa00      Jackie Smith*  STL   26  TE  14  0.0      0.0   
    346          ThomAa00       Aaron Thomas  NYG   29  TE  14  0.0      0.0   
    87           DensAl00          Al Denson  DEN   24  TE  14  0.0      0.0   
    147          HiltJo00        John Hilton  PIT   24  TE  14  0.0      0.0   
    192          LammPe00       Pete Lammons  NYJ   23  TE  14  0.0      0.0   
    209          MacKJa00  Jacque MacKinnon*  SDG   28  TE  14  0.0      0.0   
    53           CarpPr00  Preston Carpenter  2TM   32  TE  13  0.0      0.0   
    370          WhalJi00         Jim Whalen  BOS   23  TE  14  0.0      0.0   
    60           ClarFr00       Frank Clarke  DAL   32  TE  14  0.0      0.0   
    72           CostPa00        Paul Costa*  BUF   25  TE  14  0.0      0.0   
    6            ArbaFr00      Fred Arbanas+  KAN   27  TE  14  0.0      0.0   
    89           DitkMi00         Mike Ditka  CHI   27  TE  14  0.0      0.0   
    103          FlemMa00       Marv Fleming  GNB   24  TE  14  0.0      0.0   
    228          McLeBo00         Bob McLeod  HOU   28  TE  14  0.0      0.0   
    48           CannBi00       Billy Cannon  OAK   29  TE  14  0.0      0.0   
    188          KramRo00         Ron Kramer  DET   31  TE  14  0.0      0.0   
    184          KocoDa00      Dave Kocourek  MIA   29  TE  14  0.0      0.0   
    331          SticMo00     Monty Stickles  SFO   28  TE  14  0.0      0.0   
    246          MoriMi00         Milt Morin  CLE   24  TE  11  0.0      0.0   
    241          MitcTo00       Tom Mitchell  OAK   22  TE  14  0.0      0.0   
    214          MartBi00        Bill Martin  ATL   24  TE  14  0.0      0.0   
    353          TruaBi00        Billy Truax  RAM   23  TE  14  0.0      0.0   
    101          FergCh00   Charley Ferguson  BUF   27  TE  14  0.0      0.0   
    322          SmitRa00        Ralph Smith  CLE   28  TE  14  0.0      0.0   
    5            AndeTa00       Taz Anderson  ATL   28  TE   8  0.0      0.0   
    227          McKeMa00   Marlin McKeever*  RAM   26  TE  11  0.0      0.0   
    52           CaroRe00     Reggie Carolan  KAN   27  TE  14  0.0      0.0   
    77           CresBo00     Bobby Crespino  NYG   28  TE  14  0.0      0.0   
    67           ColcJi00      Jim Colclough  BOS   30  TE  14  0.0      0.0   
    108          FrazWi00     Willie Frazier  SDG   24  TE  14  0.0      0.0   
    187          KramKe00        Kent Kramer  SFO   22  TE  14  0.0      0.0   
    259          NormPe00      Pettis Norman  DAL   27  TE  14  0.0      0.0   
    275          PoolBo00          Bob Poole  HOU   25  TE  14  0.0      0.0   
    79           CronBi00        Bill Cronin  MIA   23  TE  14  0.0      0.0   
    226          McGeMa00          Max McGee  GNB   34  TE  12  0.0      0.0   
    376          WilsBu00       Butch Wilson  BAL   25  TE  14  0.0      0.0   
    289          RichPa00        Pat Richter  WAS   25  TE  14  0.0      0.0   
    297          RomeTo00         Tony Romeo  BOS   28  TE  14  0.0      0.0   
    167          JohnCu20     Curley Johnson  NYJ   31  TE  14  0.0      0.0   
    118          GibbJi00        Jim Gibbons  DET   30  TE   7  0.0      0.0   
    190          KuppJa00          Jake Kupp  WAS   25  TE  14  0.0      0.0   
    0            AlleDu00        Duane Allen  CHI   29  TE  11  0.0      0.0   
    164          JeteTo00         Tony Jeter  PIT   22  TE   9  0.0      0.0   
    245          MoreDo00        Doug Moreau  MIA   21  TE   3  0.0      0.0   
    3            AndeBi00      Bill Anderson  GNB   30  TE  10  0.0      0.0   
    27           BiviCh00     Charlie Bivins  CHI   28  TE  14  0.0      0.0   
    
         PassYds  PassTD  ...  RushAtt  RushYds  RushTD  RushFmb   Rec  RecYds  \
    210      0.0     0.0  ...      1.0     -6.0     0.0      0.0  50.0   829.0   
    82       0.0     0.0  ...      0.0      0.0     0.0      0.0  37.0   876.0   
    321      0.0     0.0  ...      0.0      0.0     0.0      0.0  54.0   686.0   
    287      0.0     0.0  ...      0.0      0.0     0.0      0.0  40.0   653.0   
    320      0.0     0.0  ...      1.0      8.0     0.0      1.0  45.0   810.0   
    346      0.0     0.0  ...      0.0      0.0     0.0      0.0  43.0   683.0   
    87       0.0     0.0  ...      0.0      0.0     0.0      0.0  36.0   725.0   
    147      0.0     0.0  ...      0.0      0.0     0.0      0.0  46.0   603.0   
    192      0.0     0.0  ...      0.0      0.0     0.0      0.0  41.0   565.0   
    209      0.0     0.0  ...      0.0      0.0     0.0      0.0  26.0   477.0   
    53       0.0     0.0  ...      1.0    -10.0     0.0      0.0  30.0   518.0   
    370      0.0     0.0  ...      0.0      0.0     0.0      0.0  29.0   502.0   
    60       0.0     0.0  ...      8.0     49.0     0.0      0.0  26.0   355.0   
    72       0.0     0.0  ...      0.0      0.0     0.0      0.0  27.0   400.0   
    6        0.0     0.0  ...      0.0      0.0     0.0      0.0  22.0   305.0   
    89       0.0     0.0  ...      0.0      0.0     0.0      0.0  32.0   378.0   
    103      0.0     0.0  ...      0.0      0.0     0.0      0.0  31.0   361.0   
    228      0.0     0.0  ...      0.0      0.0     0.0      0.0  23.0   339.0   
    48       0.0     0.0  ...      0.0      0.0     0.0      0.0  14.0   436.0   
    188      0.0     0.0  ...      0.0      0.0     0.0      0.0  37.0   432.0   
    184      0.0     0.0  ...      0.0      0.0     0.0      0.0  27.0   320.0   
    331      0.0     0.0  ...      0.0      0.0     0.0      0.0  27.0   315.0   
    246      0.0     0.0  ...      0.0      0.0     0.0      0.0  23.0   333.0   
    241      0.0     0.0  ...      0.0      0.0     0.0      0.0  23.0   301.0   
    214      0.0     0.0  ...      0.0      0.0     0.0      0.0  29.0   330.0   
    353      0.0     0.0  ...      0.0      0.0     0.0      0.0  29.0   314.0   
    101      0.0     0.0  ...      0.0      0.0     0.0      0.0  16.0   293.0   
    322      0.0     0.0  ...      0.0      0.0     0.0      0.0  13.0   183.0   
    5        0.0     0.0  ...      0.0      0.0     0.0      0.0  10.0   195.0   
    227      0.0     0.0  ...      0.0      0.0     0.0      0.0  23.0   277.0   
    52       0.0     0.0  ...      0.0      0.0     0.0      0.0   7.0   154.0   
    77       0.0     0.0  ...      0.0      0.0     0.0      0.0  16.0   167.0   
    67       0.0     0.0  ...      0.0      0.0     0.0      0.0  16.0   284.0   
    108      0.0     0.0  ...      0.0      0.0     0.0      0.0   9.0   144.0   
    187      0.0     0.0  ...      0.0      0.0     0.0      0.0   5.0    81.0   
    259      0.0     0.0  ...      0.0      0.0     0.0      0.0  12.0   144.0   
    275      0.0     0.0  ...      0.0      0.0     0.0      0.0  12.0   131.0   
    79       0.0     0.0  ...      0.0      0.0     0.0      0.0   7.0    83.0   
    226      0.0     0.0  ...      0.0      0.0     0.0      0.0   4.0    91.0   
    376      0.0     0.0  ...      0.0      0.0     0.0      0.0   3.0    27.0   
    289      0.0     0.0  ...      0.0      0.0     0.0      0.0   7.0   100.0   
    297      0.0     0.0  ...      0.0      0.0     0.0      0.0   2.0    46.0   
    167      0.0     0.0  ...      2.0     24.0     0.0      2.0   1.0    18.0   
    118      0.0     0.0  ...      0.0      0.0     0.0      0.0   1.0     2.0   
    190      0.0     0.0  ...      0.0      0.0     0.0      0.0   4.0    28.0   
    0        0.0     0.0  ...      0.0      0.0     0.0      0.0   3.0    28.0   
    164      0.0     0.0  ...      0.0      0.0     0.0      0.0   2.0    18.0   
    245      0.0     0.0  ...      0.0      0.0     0.0      0.0   2.0    15.0   
    3        0.0     0.0  ...      0.0      0.0     0.0      0.0   2.0    14.0   
    27       0.0     0.0  ...      0.0      0.0     0.0      0.0   2.0     6.0   
    
         RecTD  RecFmb  2PM  Score  
    210    9.0     0.0  0.0  161.3  
    82     7.0     0.0  0.0  148.1  
    321    6.0     0.0  0.0  131.6  
    287    6.0     0.0  0.0  121.3  
    320    3.0     1.0  0.0  120.3  
    346    4.0     0.0  0.0  113.8  
    87     3.0     0.0  0.0  108.5  
    147    4.0     1.0  0.0  105.3  
    192    4.0     2.0  0.0   97.0  
    209    6.0     0.0  0.0   96.7  
    53     4.0     0.0  0.0   89.8  
    370    4.0     0.0  0.0   88.7  
    60     4.0     0.0  0.0   77.4  
    72     3.0     0.0  0.0   71.5  
    6      4.0     0.0  1.0   67.5  
    89     2.0     0.0  0.0   65.8  
    103    2.0     0.0  0.0   63.6  
    228    3.0     0.0  0.0   63.4  
    48     2.0     0.0  0.0   62.6  
    188    0.0     0.0  0.0   61.7  
    184    2.0     0.0  0.0   57.5  
    331    2.0     0.0  0.0   57.0  
    246    3.0     3.0  0.0   56.8  
    241    1.0     0.0  0.0   47.6  
    214    0.0     0.0  0.0   47.5  
    353    0.0     0.0  0.0   45.9  
    101    1.0     0.0  0.0   43.3  
    322    3.0     0.0  0.0   42.8  
    5      3.0     0.0  0.0   42.5  
    227    1.0     2.0  0.0   41.2  
    52     3.0     0.0  0.0   36.9  
    77     2.0     0.0  0.0   36.7  
    67     0.0     1.0  0.0   34.4  
    108    2.0     0.0  0.0   30.9  
    187    3.0     0.0  0.0   28.6  
    259    0.0     0.0  0.0   20.4  
    275    0.0     0.0  0.0   19.1  
    79     1.0     0.0  0.0   17.8  
    226    1.0     0.0  0.0   17.1  
    376    2.0     0.0  0.0   16.2  
    289    0.0     0.0  0.0   13.5  
    297    0.0     0.0  1.0    7.6  
    167    1.0     2.0  0.0    6.7  
    118    1.0     0.0  0.0    6.7  
    190    0.0     0.0  0.0    4.8  
    0      0.0     0.0  0.0    4.3  
    164    0.0     0.0  0.0    2.8  
    245    0.0     0.0  0.0    2.5  
    3      0.0     0.0  0.0    2.4  
    27     0.0     1.0  0.0   -0.4  
    
    [50 rows x 21 columns]

