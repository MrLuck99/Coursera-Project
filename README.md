# Coursera-Project

**Introduction**

For my Google Data Analytics Capstone Project, I chose track 2. I will be using the six phases of data analysis for this project (ask, prepare, process, analyze, share, & act). I used several tools to complete this project, specifically spreadsheets (excel), SQL (BigQuery) and R ( R Studio).

**Background**

In this case study scenario, I have been hired to analyze Major League Baseball odds data for FantasyAce, a website that provides projections, software tools and advice for fantasy sports and sports betting.

## Ask

**Business Question:** How accurate are lines set by sports books on Major League Baseball games?

Sportsbooks employ skilled handicappers with access to huge amounts of historic data in order to create lines in advance of various games and events. Then they adjust these odds in reaction to the money wagered. For example, a book may post an opening line for runs scored in a game at 8.5. As money is wagered, the lines may begin to change. Because the books keep track of every wager placed by every player, they know who the skilled, winning players are, and they will adjust their lines in reaction to these players. If enough money is wagered on the over by winning professional bettors, the book may adjust the line up to 9. This process creates efficient lines that leverage the historical data generated by the sportsbook, as well as the experience and skill of the handicappers they employ and the professional gamblers who wager on these lines. For this reason, FantasyAce uses these numbers to identify profitable betting opportunities, create more accurate player projections, and generally advise their customers on the sports betting landscape. However, we need to better understand the strengths and weaknesses of the various odds in order to improve our understanding of their predictive value and communicate this information to the customer. Specifically, we want to answer the following questions:

 - How often does the money line favorite cover? 
 - How often does the total favorite cover? 
 - How often does the run line odds favorite cover?
 
For the purpose of this analysis, we will define a cover as follows:

 - money line =  money line < opponent money line AND runs > opponent runs
 - over total = (runs + oppRuns) > total
 - under total = (runs + oppRuns) < total
 - Run line: (oppRuns - runs) < runLine

## Prepare

FantasyAce has provided me with Major League Baseball odds data from online sportsbook ActionSports.com. There are 10 separate csv files, one for each season from 2012 to 2021. The data has one row for each team involved in every regular season game. The columns consist of team, date, and the corresponding odds from the sports book. I uploaded each of these files to BigQuery.
The data does not include the game results, so I need to collect this information and attach it to the odds data. Specifically I need the number of runs scored by each team. I also need to calculate the total runs scored by both teams in a game, equal to runs plus opponent runs. I used an R package called “retrosheet” to obtain this game data. This package is a collection of tools used to import baseball data from the website retrosheet.org. The data was obtained free of charge from and is copyrighted by retrosheet. 

## Process

I used excel to open and work with the 10 odds data csv files. I created pivot tables and a plot for each file. 

- [2012](https://storage.googleapis.com/mlb_odds/odds2012.xlsx)
- [2013](https://storage.googleapis.com/mlb_odds/odds2012.xlsx)
- [2014](https://storage.googleapis.com/mlb_odds/odds2014.xlsx)
- [2015](https://storage.googleapis.com/mlb_odds/odds2014.xlsx)
- [2016](https://storage.googleapis.com/mlb_odds/odds2016.xlsx)
- [2017](https://storage.googleapis.com/mlb_odds/odds2016.xlsx)
- [2018](https://storage.googleapis.com/mlb_odds/odds2018.xlsx)
- [2019](https://storage.googleapis.com/mlb_odds/odds2018.xlsx)
- [2020](https://storage.googleapis.com/mlb_odds/odds2020.xlsx)
- [2021](https://storage.googleapis.com/mlb_odds/odds2020.xlsx)


I then uploaded these files to BigQuery. I used SQL to join these tables together and then uploaded these into R Studio. Click [here](https://github.com/MrLuck99/Coursera-Project/blob/main/SQL) to see the SQL query used to join the files.

I then used the “retrosheet” package to obtain the necessary game data. I saved this as "gameLogs". Click [here](https://github.com/MrLuck99/Coursera-Project/blob/main/retrosheet%20data) to see the r code used to fetch game data using the retrosheet package.

## Analyze

The game logs have data for both teams in each game in a single row.  The odds data contains a separate row for each team in every game, so I had to divide the game logs into separate rows for each team. In order to merge this data with the odds data, I completed the following steps:

  - separate rows into visiting and home team columns
  - change 'visiting' and 'home' team to columns to 'team' and 'opponent'
  - changed the team abbreviations
  - changed date column from integer to date format 
  - created game number column from double header info 
  - created 'season' column by extracting year from date column
  - selected columns needed for analysis
    
Click [here](https://github.com/MrLuck99/Coursera-Project/blob/main/edit%20retrosheet%20data) to view code.


I then merged this data with our odds data. I also created separate columns for: 

  - opponent money line
  - opponent run line
  - opponent run line odds
  - total runs (runs + opponent runs)

Click [here](https://github.com/MrLuck99/Coursera-Project/blob/main/merge%20odds%20and%20game%20logs) to view code.


Before I started my analysis, I used the summary() function to check the data for missing values.  There are some NA values in the runLine and runLineOdds columns that I need to account for later on.

This gave me a sample of 45,530 rows of data representing each team involved in 
22,765 games. I selected a sample row from our data to help explain each type of line. I then
started my analysis by calculating the count of each type of line, how often
the favorite covered this line, and the percentage of time the line covered.  I used this
data to create a summary table and a plot for each of our line types.


| Money Line | Count | Cover | Percentage |
| ------------ | ----------- | ------------ | ------------ |
| favorites | 22195 | 12938 | 58.3 |
| dogs | 22195 | 9256 |	41.7 |
| even | 1140 | - | - |


![moneyLinePlot33](moneyLinePlot33.png)

![moneyLinePlot1.1](moneyLinePlot1.1.png)

| Total | Count | Cover | Percentage |
| ------------ | ----------- | ------------ | ------------ |
| over | 9106 | 4377 | 48.1 |
| under | 9560 | 4738 |	49.6 |
| both | 18666 | 9115 | 48.4 |

![totalPlot1](totalPlot1.png)

![totalPlot2](totalPlot2.png)

| Run Line | Count | Favorites | Cover | Percentage | Dogs | Cover | Percentage | Total%
| ------------ | ----------- | ------------ | ------------ | ------------ | ------------ | ------------ | ------------ | ------------ |
| 1.5 | 17904 | 2344 | 1271 | 54.2 | 15098 | 6113 | 40.5 | 42.3 |
| 1.5	| 17904	| 15098 |	8985 |	59.5 |	2344 |	1073 |	45.8 |	57.7 |
| Total |	35808 |	17442 |	10256 |	58.8 |	17442 |	7186 |	41.2 |	50.0 |

![runLineOddsPlot9](runLinePlot9.png)

![runLineOddsPlot10](runLinePlot10.png)
## Share

Click [here](https://rpubs.com/ctreasure79/1123722) to view summary.

## Act
