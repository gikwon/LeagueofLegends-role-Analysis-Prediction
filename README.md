# League of Legends Role Analysis & Prediction
https://gikwon.github.io/LeagueofLegends-role-Analysis-Prediction/

League of Legends, LoL for short, is a multiplayer online battle arena (MOBA) game where two teams of five players compete to destroy the other team's Nexus, a protected structure in their base..

Author: Gihyeon Kwon

## Introduction
In this project, I aim to break the project into two components:
1. Analysis of the research question
2. Prediction of a new prediction problem

Both utilizing the same dataset that will be introduced below.

### Introduction the Research Question
The dataset we are working with is a collection of key gameplay statistics and outcomes from a collection of competitive LoL matches collected by Oracle's Elixir. A single game is captured with 12 rows of data, first 5 consisting of the 5 players in one team and next 5 from the other team; the last 2 rows capture the overall statistic of each team. 

The 5 players in each team all have different roles, Top player, Jungle player, Middle player, Bottom player and Support player. Many have argued that the Middle position is the key role that 'carries' the team; however, others will argue that it is the Bottom position that 'carries'.

\**Note: This is due to Mid and Bottom positions ability to deal high damage and make the play that can change the game. Some may disagree however..*

So in the first part of our analysis, we will be analyzing the question: 
**Which role “carries” (is the key position) in their team more often: ADCs (Bot lanes) or Mid laners?**

### Introduction to the Data
The data we are working with contains 148,992 rows, which translates to 12,416 different matches. 

There are a total of 131 columns that describe each row but we will only be using a portion of them.
Here are some of the key columns we will be using and are good to have in mind:

Identifiers:
- `position`: Identifies the player with the role: 'top', 'jng', 'mid', 'bot', 'sup'

- `gameid`: A unique identifier for each game. As mentioned, there are 12 rows with the same `gameid`

- `side`: Identifies the team affiliation, one team as 'blue' and the other as 'red' 

Useful variables:
- `gamelength`: Shows the total game length in seconds

- `damageshare`: Shows proportion of damage done by a player relative to their team. 

- `earnedgoldshare`: Shows proportion of gold earned done by a player relative to their team. 

- `kills`: Total kills in game

- `deaths`: Total Deaths in game

- `assists`: Total Assists in game

- `firstbloodkill`: Binary variable that shows whether the player got the first kill of the game

- `firstbloodassist`: Binary variable that shows whether the player got the first assist of the game

- `dpm`: Damages done per minute

- `earned gpm`: Gold earned per minute

- `cspm`: Creep Score(CS) per minute. Every CS is gained from killing a minion or monster.

## Data Cleaning and Exploratory Data Analysis
### Data Cleaning
The column `datacompleteness` shows whether the data is complete, partially complete, or need to be ignored. I have only kept rows that are complete. Also since I won't being needing the 2 rows of the team statistics for each game, I have removed those 2 rows for every game. That left me with 105530 rows or 10553 games.

I have also changed the `result` column into binary type, which shows 1 if the team won the game and 0 if lost.

Then I have created some new columns that may be useful for the analysis:

- `gamelength_minutes`: This column represents the length of the game in minutes, calculated by dividing the `gamelength` column by 60.

- `kills_per_minute`: This column represents the number of kills per minute, calculated by dividing the `kills` column by `gamelength_minutes`.

- `assists_per_minute`: This column represents the number of assists per minute, calculated by dividing the `assists` column by `gamelength_minutes`.

- `deaths_per_minute`: This column represents the number of deaths per minute, calculated by dividing the `deaths` column by `gamelength_minutes`.

- `kd_ratio`: This column represents the kill-to-death ratio, calculated by dividing the `kills` column by the sum of the `deaths` column plus one.

- `kda_ratio`: This column represents the kill-death-assist ratio, calculated by dividing the sum of the `kills` and `assists` columns by the sum of the `deaths` column plus one.

- `kill_participation`: This column represents the kill participation rate, calculated by dividing the sum of the `kills` and `assists` columns by the sum of the `teamkills` column plus one.

- `team_kda`: This column represents the average KDA ratio of the team, calculated by grouping by `gameid` and `side`, and then taking the mean of the `kda_ratio` column within each group.

- `team_kd`: This column represents the average KD ratio of the team, calculated by grouping by `gameid` and `side`, and then taking the mean of the `kd_ratio` column within each group.

- `kda_normal`: This column represents the normalized KDA ratio, calculated by dividing the `kda_ratio` column by the sum of the `team_kda` column plus one.

- `kd_normal`: This column represents the normalized KD ratio, calculated by dividing the `kd_ratio` column by the sum of the `team_kda` column plus one.

**The reason for creating these columns was to normalize the key metrics for each game. Since we will be comparing each metric across games that are different in game length. Ex. 10 kills in a 30 minute game isn't same as 10 kills in a 15 minute game**

Below is the first 5 rows of cleaned data.

| gameid                | datacompleteness   |   game |   patch |   participantid | side   | position   | playername   | playerid                                  | teamname                 | teamid                                  | champion   |   gamelength | result   |   kills |   deaths |   assists |   teamkills |   teamdeaths |   doublekills |   triplekills |   quadrakills |   pentakills |   firstblood |   firstbloodkill |   firstbloodassist |   firstbloodvictim |   team kpm |   ckpm |   firstdragon |   dragons |   opp_dragons |   elementaldrakes |   opp_elementaldrakes |   infernals |   mountains |   clouds |   oceans |   chemtechs |   hextechs |   dragons (type unknown) |   elders |   opp_elders |   firstherald |   heralds |   opp_heralds |   void_grubs |   opp_void_grubs |   firstbaron |   barons |   opp_barons |   firsttower |   towers |   opp_towers |   firstmidtower |   firsttothreetowers |   turretplates |   opp_turretplates |   inhibitors |   opp_inhibitors |   damagetochampions |     dpm |   damageshare |   damagetakenperminute |   damagemitigatedperminute |   wardsplaced |    wpm |   wardskilled |   wcpm |   controlwardsbought |   visionscore |   vspm |   totalgold |   earnedgold |   earned gpm |   earnedgoldshare |   goldspent |   gspd |   gpr |   total cs |   minionkills |   monsterkills |   monsterkillsownjungle |   monsterkillsenemyjungle |   cspm |   goldat10 |   xpat10 |   csat10 |   opp_goldat10 |   opp_xpat10 |   opp_csat10 |   golddiffat10 |   xpdiffat10 |   csdiffat10 |   killsat10 |   assistsat10 |   deathsat10 |   opp_killsat10 |   opp_assistsat10 |   opp_deathsat10 |   goldat15 |   xpat15 |   csat15 |   opp_goldat15 |   opp_xpat15 |   opp_csat15 |   golddiffat15 |   xpdiffat15 |   csdiffat15 |   killsat15 |   assistsat15 |   deathsat15 |   opp_killsat15 |   opp_assistsat15 |   opp_deathsat15 |   gamelength_minutes |   kills_per_minute |   assists_per_minute |   deaths_per_minute |   kd_ratio |   kda_ratio |   kill_participation |   team_kda |   team_kd |   kda_normal |   kd_normal |
|:----------------------|:-------------------|-------:|--------:|----------------:|:-------|:-----------|:-------------|:------------------------------------------|:-------------------------|:----------------------------------------|:-----------|-------------:|:---------|--------:|---------:|----------:|------------:|-------------:|--------------:|--------------:|--------------:|-------------:|-------------:|-----------------:|-------------------:|-------------------:|-----------:|-------:|--------------:|----------:|--------------:|------------------:|----------------------:|------------:|------------:|---------:|---------:|------------:|-----------:|-------------------------:|---------:|-------------:|--------------:|----------:|--------------:|-------------:|-----------------:|-------------:|---------:|-------------:|-------------:|---------:|-------------:|----------------:|---------------------:|---------------:|-------------------:|-------------:|-----------------:|--------------------:|--------:|--------------:|-----------------------:|---------------------------:|--------------:|-------:|--------------:|-------:|---------------------:|--------------:|-------:|------------:|-------------:|-------------:|------------------:|------------:|-------:|------:|-----------:|--------------:|---------------:|------------------------:|--------------------------:|-------:|-----------:|---------:|---------:|---------------:|-------------:|-------------:|---------------:|-------------:|-------------:|------------:|--------------:|-------------:|----------------:|------------------:|-----------------:|-----------:|---------:|---------:|---------------:|-------------:|-------------:|---------------:|-------------:|-------------:|------------:|--------------:|-------------:|----------------:|------------------:|-----------------:|---------------------:|-------------------:|---------------------:|--------------------:|-----------:|------------:|---------------------:|-----------:|----------:|-------------:|------------:|
| ESPORTSTMNT01_2690210 | complete           |      1 |   12.01 |               1 | Blue   | top        | Soboro       | oe:player:38e0af7278d6769d0c81d7c4b47ac1e | Fredit BRION Challengers | oe:team:68911b3329146587617ab2973106e23 | Renekton   |         1713 | False    |       2 |        3 |         2 |           9 |           19 |             0 |             0 |             0 |            0 |            0 |                0 |                  0 |                  0 |     0.3152 | 0.9807 |           nan |       nan |           nan |               nan |                   nan |         nan |         nan |      nan |      nan |         nan |        nan |                      nan |      nan |          nan |           nan |       nan |           nan |          nan |              nan |          nan |        0 |            0 |          nan |      nan |          nan |             nan |                  nan |            nan |                nan |            0 |                0 |               15768 | 552.294 |     0.278784  |               1072.4   |                    777.793 |             8 | 0.2802 |             6 | 0.2102 |                    5 |            26 | 0.9107 |       10934 |         7164 |      250.928 |          0.253859 |       10275 |    nan |   nan |        231 |           220 |             11 |                     nan |                       nan | 8.0911 |       3228 |     4909 |       89 |           3176 |         4953 |           81 |             52 |          -44 |            8 |           0 |             0 |            0 |               0 |                 0 |                0 |       5025 |     7560 |      135 |           4634 |         7215 |          121 |            391 |          345 |           14 |           0 |             1 |            0 |               0 |                 1 |                0 |                28.55 |          0.0700525 |            0.0700525 |           0.105079  |   0.5      |     1       |                  0.4 |    1.19333 |  0.413333 |     0.455927 |   0.227964  |
| ESPORTSTMNT01_2690210 | complete           |      1 |   12.01 |               2 | Blue   | jng        | Raptor       | oe:player:637ed20b1e41be1c51bd1a4cb211357 | Fredit BRION Challengers | oe:team:68911b3329146587617ab2973106e23 | Xin Zhao   |         1713 | False    |       2 |        5 |         6 |           9 |           19 |             0 |             0 |             0 |            0 |            1 |                0 |                  1 |                  0 |     0.3152 | 0.9807 |           nan |       nan |           nan |               nan |                   nan |         nan |         nan |      nan |      nan |         nan |        nan |                      nan |      nan |          nan |           nan |       nan |           nan |          nan |              nan |          nan |        0 |            0 |          nan |      nan |          nan |             nan |                  nan |            nan |                nan |            0 |                1 |               11765 | 412.084 |     0.208009  |                944.273 |                    650.158 |             6 | 0.2102 |            18 | 0.6305 |                    6 |            48 | 1.6813 |        9138 |         5368 |      188.021 |          0.19022  |        8750 |    nan |   nan |        148 |            33 |            115 |                     nan |                       nan | 5.1839 |       3429 |     3484 |       58 |           2944 |         3052 |           63 |            485 |          432 |           -5 |           1 |             2 |            0 |               0 |                 0 |                1 |       5366 |     5320 |       89 |           4825 |         5595 |          100 |            541 |         -275 |          -11 |           2 |             3 |            2 |               0 |                 5 |                1 |                28.55 |          0.0700525 |            0.210158  |           0.175131  |   0.333333 |     1.33333 |                  0.8 |    1.19333 |  0.413333 |     0.607903 |   0.151976  |
| ESPORTSTMNT01_2690210 | complete           |      1 |   12.01 |               3 | Blue   | mid        | Feisty       | oe:player:d1ae0e2f9f3ac1e0e0cdcb86504ca77 | Fredit BRION Challengers | oe:team:68911b3329146587617ab2973106e23 | LeBlanc    |         1713 | False    |       2 |        2 |         3 |           9 |           19 |             0 |             0 |             0 |            0 |            0 |                0 |                  0 |                  0 |     0.3152 | 0.9807 |           nan |       nan |           nan |               nan |                   nan |         nan |         nan |      nan |      nan |         nan |        nan |                      nan |      nan |          nan |           nan |       nan |           nan |          nan |              nan |          nan |        0 |            0 |          nan |      nan |          nan |             nan |                  nan |            nan |                nan |            0 |                0 |               14258 | 499.405 |     0.252086  |                581.646 |                    227.776 |            19 | 0.6655 |             7 | 0.2452 |                    7 |            29 | 1.0158 |        9715 |         5945 |      208.231 |          0.210665 |        8725 |    nan |   nan |        193 |           177 |             16 |                     nan |                       nan | 6.7601 |       3283 |     4556 |       81 |           3121 |         4485 |           81 |            162 |           71 |            0 |           0 |             1 |            0 |               0 |                 0 |                1 |       5118 |     6942 |      120 |           5593 |         6789 |          119 |           -475 |          153 |            1 |           0 |             3 |            0 |               3 |                 3 |                2 |                28.55 |          0.0700525 |            0.105079  |           0.0700525 |   0.666667 |     1.66667 |                  0.5 |    1.19333 |  0.413333 |     0.759878 |   0.303951  |
| ESPORTSTMNT01_2690210 | complete           |      1 |   12.01 |               4 | Blue   | bot        | Gamin        | oe:player:998b3e49b01ecc41eacc392477a98cf | Fredit BRION Challengers | oe:team:68911b3329146587617ab2973106e23 | Samira     |         1713 | False    |       2 |        4 |         2 |           9 |           19 |             0 |             0 |             0 |            0 |            1 |                0 |                  1 |                  0 |     0.3152 | 0.9807 |           nan |       nan |           nan |               nan |                   nan |         nan |         nan |      nan |      nan |         nan |        nan |                      nan |      nan |          nan |           nan |       nan |           nan |          nan |              nan |          nan |        0 |            0 |          nan |      nan |          nan |             nan |                  nan |            nan |                nan |            0 |                0 |               11106 | 389.002 |     0.196358  |                463.853 |                    218.879 |            12 | 0.4203 |             6 | 0.2102 |                    4 |            25 | 0.8757 |       10605 |         6835 |      239.405 |          0.242201 |       10425 |    nan |   nan |        226 |           208 |             18 |                     nan |                       nan | 7.9159 |       3600 |     3103 |       78 |           3304 |         2838 |           90 |            296 |          265 |          -12 |           1 |             1 |            0 |               0 |                 0 |                0 |       5461 |     4591 |      115 |           6254 |         5934 |          149 |           -793 |        -1343 |          -34 |           2 |             1 |            2 |               3 |                 3 |                0 |                28.55 |          0.0700525 |            0.0700525 |           0.140105  |   0.4      |     0.8     |                  0.4 |    1.19333 |  0.413333 |     0.364742 |   0.182371  |
| ESPORTSTMNT01_2690210 | complete           |      1 |   12.01 |               5 | Blue   | sup        | Loopy        | oe:player:e9741b3a238723ea6380ef2113fae63 | Fredit BRION Challengers | oe:team:68911b3329146587617ab2973106e23 | Leona      |         1713 | False    |       1 |        5 |         6 |           9 |           19 |             0 |             0 |             0 |            0 |            1 |                1 |                  0 |                  0 |     0.3152 | 0.9807 |           nan |       nan |           nan |               nan |                   nan |         nan |         nan |      nan |      nan |         nan |        nan |                      nan |      nan |          nan |           nan |       nan |           nan |          nan |              nan |          nan |        0 |            0 |          nan |      nan |          nan |             nan |                  nan |            nan |                nan |            0 |                0 |                3663 | 128.301 |     0.0647631 |                475.026 |                    490.123 |            29 | 1.0158 |            14 | 0.4904 |                   11 |            69 | 2.4168 |        6678 |         2908 |      101.856 |          0.103054 |        6395 |    nan |   nan |         42 |            42 |              0 |                     nan |                       nan | 1.4711 |       2678 |     2161 |       16 |           2150 |         2748 |           15 |            528 |         -587 |            1 |           1 |             1 |            0 |               0 |                 0 |                1 |       3836 |     3588 |       28 |           3393 |         4085 |           21 |            443 |         -497 |            7 |           1 |             2 |            2 |               0 |                 6 |                2 |                28.55 |          0.0350263 |            0.210158  |           0.175131  |   0.166667 |     1.16667 |                  0.7 |    1.19333 |  0.413333 |     0.531915 |   0.0759878 |

\**Note: The columns will look messy here but I have kept many of the columns to use later in my analysis*

### Univariate Analysis

First I performed univariate analysis by looking at the distribution of the `damageshare` column.

<iframe
  src="assets/hist_dmg.html"
  width="1000"
  height="600"
  frameborder="0"
  style="border:0; margin:0; padding:0;"
></iframe>

We can see that the distribution is nearly normal. We do see some players with below 0.1 damage share and even above 0.4 damage share. Remember that damage share is out of 1 and the team of 5 make up a total of 1 damage share. This shows that there are some player that perform better than other players. Lets try to find which roles those players are.

### Bivariate Analysis

Here I performed bivariate analysis on the normalized KDA Ratio between mid position and bottom positions:

<iframe
  src="assets/Boxplot_KDA.html"
  width="1000"
  height="600"
  frameborder="0"
  style="border:0; margin:0; padding:0;"
></iframe>
The box plot shows the distribution of each position's normalzied KDA and we can see that the min, lower quantile median, and the upper quantile for bottom position is slighter higher than that of middle positions. 

### Interesting Aggregates

To compare other key performance metrics between mid and bot positions, I made a table showing the average and standard deviation values of the key metrics that contribute to being the 'key position':

|   ('kd_normal', 'mean') |   ('kd_normal', 'std') |   ('kda_normal', 'mean') |   ('kda_normal', 'std') |   ('kill_participation', 'mean') |   ('kill_participation', 'std') |   ('kills_per_minute', 'mean') |   ('kills_per_minute', 'std') |   ('assists_per_minute', 'mean') |   ('assists_per_minute', 'std') |   ('deaths_per_minute', 'mean') |   ('deaths_per_minute', 'std') |   ('dpm', 'mean') |   ('dpm', 'std') |   ('damageshare', 'mean') |   ('damageshare', 'std') |   ('earned gpm', 'mean') |   ('earned gpm', 'std') |   ('earnedgoldshare', 'mean') |   ('earnedgoldshare', 'std') |   ('cspm', 'mean') |   ('cspm', 'std') |   ('doublekills', 'mean') |   ('doublekills', 'std') |   ('triplekills', 'mean') |   ('triplekills', 'std') |   ('quadrakills', 'mean') |   ('quadrakills', 'std') |   ('pentakills', 'mean') |   ('pentakills', 'std') |   ('firstbloodkill', 'mean') |   ('firstbloodkill', 'std') |   ('firstbloodassist', 'mean') |   ('firstbloodassist', 'std') |
|------------------------:|-----------------------:|-------------------------:|------------------------:|---------------------------------:|--------------------------------:|-------------------------------:|------------------------------:|---------------------------------:|--------------------------------:|--------------------------------:|-------------------------------:|------------------:|-----------------:|--------------------------:|-------------------------:|-------------------------:|------------------------:|------------------------------:|-----------------------------:|-------------------:|------------------:|--------------------------:|-------------------------:|--------------------------:|-------------------------:|--------------------------:|-------------------------:|-------------------------:|------------------------:|-----------------------------:|----------------------------:|-------------------------------:|------------------------------:|
|                0.340107 |               0.268792 |                 0.76129  |                0.427727 |                         0.593615 |                        0.18175  |                       0.135902 |                     0.107636  |                         0.172267 |                        0.11775  |                       0.0831228 |                      0.0637238 |           558.396 |          222.932 |                  0.264946 |                0.0746089 |                  297.361 |                 70.1551 |                      0.260147 |                    0.0431674 |            8.72547 |           1.70364 |                   0.65858 |                 0.911422 |                 0.154932  |                 0.412628 |                 0.0322657 |                 0.184321 |               0.0065858  |               0.0814709 |                    0.116602  |                    0.320953 |                       0.114707 |                      0.318675 |
|                0.279429 |               0.234002 |                 0.723186 |                0.41992  |                         0.581186 |                        0.175269 |                       0.113512 |                     0.0913255 |                         0.185745 |                        0.125384 |                       0.0866246 |                      0.0638212 |           548.371 |          212.062 |                  0.261416 |                0.0737649 |                  267.443 |                 58.5731 |                      0.234372 |                    0.0353127 |            8.26828 |           1.17428 |                   0.41538 |                 0.707069 |                 0.0685587 |                 0.27081  |                 0.0103288 |                 0.104336 |               0.00161092 |               0.0401048 |                    0.0901166 |                    0.286355 |                       0.103288 |                      0.304342 |

From this table we can see all average values except assists_per_minute are higher for the bottom position.
*But bottom position's standard deviation values seem higher than middle position's in most columns..

## Assessment of Missingness

### NMAR Analysis
In the data, I believe the columns `ban1`, `ban2`, `ban3`, `ban4`, `ban5` are all **Not Missing at Random (NMAR).** 

Before the start of every game, there is a phase to ban a champion (game character) so that the oppossing team
and their own team cannot select that specific champion.

The missingness of those `ban` columns imply that no *bans* were made. Meaning the value of the `ban` columns are missing because the players made the decision to not ban any champion. Making the missingness of the columns dependent of themselves. 

Additional column required to make the columns **Missing at Random (MAR)** would be a binary variable that indicates all players have banned a champion. 

*As these ideas above were introduced in the example from a previous quarter, I decided to make a hypothetical situation:*

If these data points were self-reported, I believe the column `damageshare` will be **Not Missing at Random (NMAR).** This is because players with extremly high or extremely low damageshare values will opt to not report their damage share values.

Additional column to make the `damageshare` **Missing at Random (MAR)** would be a variable that shows the player's rank. Although unrealistic in this data as its a professional league data, hypothetically, lower ranks in the game will be less likely to report their `damageshare` values

### Missingness Dependency
In this sections I will be testing if the columns `barons` is dependent of any other columns.
*The `barons` columns was chosen as it was the column with the most missing values besides some columns that were entirely missing (columns that have values only to the team rows)

Permutation to test dependency:

- **Null Hypothesis**: Missingness of `barons` column is not dependent of another column
- **Alternative Hypothesis**: Missingness of `barons` column is dependent of another column

Two-Sided Test:
- **Test Statistic**: K-S Test statistics
- Using significant value of 0.01

I tested on every single numerical columns in the dataset that were some what relevant to the analysis. But I have removed columns that were entirely missing or for columns where the values are missing when `barons` is also missing.

After running permutation test with K-S Test Statistics, here were the p-values of each columns:

|                          |        value |
|:-------------------------|-------------:|
| cspm                     | 4.78471e-231 |
| minionkills              | 1.10212e-110 |
| ckpm                     | 1.83726e-32  |
| wardskilled              | 2.16284e-14  |
| wcpm                     | 1.43256e-12  |
| gamelength               | 6.91995e-12  |
| team kpm                 | 1.07775e-11  |
| visionscore              | 2.21896e-09  |
| wardsplaced              | 2.14885e-08  |
| teamkills                | 5.54046e-08  |
| vspm                     | 1.77632e-07  |
| teamdeaths               | 2.01857e-07  |
| total cs                 | 3.21105e-07  |
| wpm                      | 4.23441e-07  |
| assistsat15              | 3.93962e-05  |
| opp_assistsat15          | 3.93962e-05  |
| controlwardsbought       | 0.000142926  |
| dpm                      | 0.000603061  |
| opp_csat15               | 0.000603397  |
| csat15                   | 0.000603397  |
| goldspent                | 0.000719199  |
| deathsat15               | 0.000728653  |
| opp_deathsat15           | 0.000728653  |
| totalgold                | 0.00158642   |
| damagetakenperminute     | 0.00174941   |
| monsterkills             | 0.00396119   |
| assists                  | 0.00491397   |
| deaths                   | 0.00662544   |
| assistsat10              | 0.00684363   |
| opp_assistsat10          | 0.00684363   |
| earnedgold               | 0.00707355   |
| opp_csat10               | 0.0145844    |
| csat10                   | 0.0145844    |
| opp_deathsat10           | 0.0709659    |
| deathsat10               | 0.0709659    |
| damagetochampions        | 0.0934137    |
| opp_killsat15            | 0.0939419    |
| killsat15                | 0.0939419    |
| opp_xpat15               | 0.116452     |
| xpat15                   | 0.116452     |
| damageshare              | 0.154135     |
| opp_killsat10            | 0.191679     |
| killsat10                | 0.191679     |
| damagemitigatedperminute | 0.199235     |
| earnedgoldshare          | 0.300979     |
| doublekills              | 0.316264     |
| kills                    | 0.555616     |
| earned gpm               | 0.701027     |
| goldat15                 | 0.727167     |
| opp_goldat15             | 0.727167     |
| xpdiffat10               | 0.782376     |
| goldat10                 | 0.812143     |
| opp_goldat10             | 0.812143     |
| xpat10                   | 0.834574     |
| opp_xpat10               | 0.834574     |
| golddiffat15             | 0.936079     |
| golddiffat10             | 0.95877      |
| csdiffat15               | 0.994139     |
| csdiffat10               | 0.998037     |
| xpdiffat15               | 0.998591     |
| firstbloodassist         | 0.999789     |
| firstblood               | 0.999789     |
| quadrakills              | 1            |
| firstbloodkill           | 1            |
| triplekills              | 1            |
| pentakills               | 1            |
| firstbloodvictim         | 1            |

I have found that the smallest p_value is for the column `cspm`.

<iframe
  src="assets/cspm_ks.html"
  width="1000"
  height="600"
  frameborder="0"
  style="border:0; margin:0; padding:0;"
></iframe>

As the p_value of cspm is 4.78471e-231, which is less than 0.01, we reject the null hypothesis of: 
Missingness of `barons` column is not dependent of another column.

This means that the `barons` column has high chance of being dependent on another column which is `cspm`

This is interesting as the two variables don't seem correlated what could this mean for us regarding the research question?
- If the missingness of `barons` is dependent on `cspm`, it might indicate a bias in data collection. For example, games or players with higher `cspm` might be better tracked or recorded, leading to fewer missing values in `barons`..
- `cspm` is a measure of how effectively a player can kill creeps, which is a proxy for farm efficiency and overall game performance. If `barons` are missing more frequently in games with low `cspm`, it might suggest that lower-performing games are not as thoroughly recorded or analyzed, possibly due to lack of interest or perceived importance..


Then we take a look at a column that is `barons` is not dependent on, which is `firstbloodkill`, it's p-value is 1, meaning we fail to reject the Null.

<iframe
  src="assets/firstbloodkill_ks.html"
  width="1000"
  height="600"
  frameborder="0"
  style="border:0; margin:0; padding:0;"
></iframe>

There isn't too much interpretation to provide here, we can assume that `barons`'s missingness is not d`firtbloodkill`.

## Hypothesis Testing
To conclude the 1st part of the project, I will be performing a hypothesis test, a permutation test to be exact, to answer the research queastion: **Which role “carries” (is the key position) in their team more often: ADCs (Bot lanes) or Mid laners?**


I have engineered a new column `kda_dmg`, which is `kda_normal` *  `damageshare`.
The reason for this variable is that a KDA will not be enough to tell us if the player carried the game. The player can have high KDA but low `damageshare`, which implies that were stealing kills.
Multiplying the two key metric columns together will give us a better idea for the metric that shows who carries the team.

- **Null Hypothesis** : The normalized KDA times Damage Share for ADCs (bot lane) is less than or equal to the normalized KDA times Damage Share for Mid laners.

- **Alternative Hypothesis**: The normalized KDA times Damage Share for ADCs (bot lane) is greater than the normalized KDA times Damage Share for Mid laners.


One sided test:
-**Test Statistics**: Difference in means
- Using significant value of 0.01

<iframe
  src="assets/normalKDA_perm.html"
  width="1000"
  height="600"
  frameborder="0"
  style="border:0; margin:0; padding:0;"
></iframe>

With observed statistic, calculated by Bottom position's Normal KDA average minus Middle position;s Normal KDA average, of 0.0381, we find a P-Value of 0.0.

We reject the null hypothesis, meaning there is strong evidence that Bottom positions have higher Normalized KDA ratios. Which implies that Bottom is the key role in the team and carries most of the time.

## Framing a Prediction Problem

Now for the 2nd part of the project!

I will be looking into **predicting the player's role given their post-game statistics.**
In more detail, as there are five different positions in each team (top, jng, mid, bot, sup) can we predict what the players' roles were given their post-game data. 

Since we are predicting a players role, this is a classification problem. More formally, this is a multiclass classification predicting the response variable `position`.

This classification can be helpful in several ways:
- Identifying these roles from statistics can provide insights into how well a player performed their assigned role. 
- Also help in assessing the overall team dynamics and coordination. 
- Help in identifying trends and patterns in gameplay. This can be valuable for game developers, analysts, and researchers studying the evolution of playstyles and strategies in the game.

As for the metric, I will be utilizing *Accuracy* as my performance metric. The reason is that the predicting variable `position` is evenly distributed to all five of the roles. This is because each game has 5 unique positions, making the data for `position` balanced. Which makes *Accuracy* a good metric for my model.
 
For both of my training and testing sets, I have used 75/25 split, which were utlizied through both of the models to ensure seen and unseen data remains the same and to compare final model performance to baseline model performance.

*Lastly, the time of prediction is at the very end of the game, meaning I'll have all the game data in hand to utlize in the models.*

## Baseline Model

For my models, I have engineered a few more features that can be potentially be useful:
- `kda_15`: KDA ratio at 15 minutes. It is calculated as (kills at 15 minutes + assists at 15 minutes) / (deaths at 15 minutes + 1).

- `kd_15`: Kill/Death ratio at 15 minutes. It is calculated as kills at 15 minutes / (deaths at 15 minutes + 1).

- `kda_10`: KDA ratio at 10 minutes. It is calculated as (kills at 10 minutes + assists at 10 minutes) / (deaths at 10 minutes + 1).

- `kd_10`: Kill/Death ratio at 10 minutes. It is calculated as kills at 10 minutes / (deaths at 10 minutes + 1).

- `monster_kpm`: Monster kills per minute. It is calculated as monster kills / game length in minutes.

- `vision_pm`: Vision score per minute. It is calculated as vision score / game length in minutes.

The features I used were:
- `position_encoded`: Nominal
- `cspm`: Quantitative
- `kda_normal`: Quantitative
- `xpat15`: Quantitative
- `monster_kpm`: Quantitative
- `damageshare`: Quantitative
- `firstbloodkill`: : Nominal
- `dpm`: Quantitative
- `goldat15`: Quantitative
- `vision_pm`: Quantitative

The `position_encoded` is the outcome variable that I created using **label_encoder** on `position`.
The rest will be used to train the model.

I used a *ColumnTransformer* to Simple Mean Impute all the columns and then put it in pipeline as preprocessor with **RandomForestClassifier**

Baseline model performance results:
- Train set *accuracy* of 0.999987: 99.9987% of data predicted correctly.
- Test set *accuracy* of 0.8466057: 84.66057% of data predicted correctly.

I believe that my model is definitely good for it being a baseline model. However, I believe there are more possible encodings and better hyperparameters to test to gain better results.

## Final Model
For my final model, I have standard scaled these columns: `cspm`, `dpm`, `monster_kpm`.
Although they are in units of 'per minute', I thought that still standardizing these values will help further.
For ex. (The damage per second in a long game will be slightly lower than an action packed shorter game)

In addition to these 3 new features, I used *GridSearchCV* to find hyperparameters that would best predict the `position_encoded` variable in my *RandomForestClassifier*

These were the values I used to Grid Search the best parameters:
Max_depth using:
- 'classifier__max_depth': [2, 3, 4, 5, 7, 10, 13, 15, 18, 20, 25, 30, None]
- Reason is to control the complexity, which ultimately helps control overfitting the data.

Minimum samples split using:
- 'classifier__min_samples_split': [2, 5, 10, 20, 50, 100, 200]
- Also control overfitting of the data and improves generalization of the model.

Final model performance results:
- Best parameters found: {'classifier__max_depth': 30, 'classifier__min_samples_split': 10}
- Best cross-validation Training set *accuracy*: 0.9998: 99.98% of data predicted correctly.
- Test set *accuracy*: 0.9997: 99.97% of data predicted correctly.

The Test set *accuracy* increased by roughly 15.31 percentage points.This is a huge improvement and our final model predicting 99.97% of the test data on a muliclass classification is great accomplishment!

## Fairness Analysis

To analyze the fairness of my model, I decided to split the data into two groups, one where their `damageshare` is below median and other as above median.

I will be running a permutation test on these two groups on the predicted `position_encoded`to test the fairness of the model. Will still be using *accurancy* as the metric.

- Group X: Below median damaging players
- Group Y: Above median damaging players

Permutation test based on `damageshare`
 - Median `damageshare` around 0.20(roughly: 0.198), which makes sense as there are 5 players: 1/5
    - Classifying *low damage* as below median `damageshare`
    - *high damage* as above median`damageshare`


- Null Hypothesis: Our model is fair. Its accuracy for low damage share players and high damage share players are roughly the same, and any differences are due to random chance.
 
- Alternative Hypothesis: Our model is unfair. Its accuracy for high damage share players are higher then the accuracy of the model for low damage share players

Fairness Analysis permutation test results:
- *P-Value*: 0.5279
- We fail to reject the null.

**Meaning there is strong evidence that our Final model is *fair* across the low damage players and high damage players.**
