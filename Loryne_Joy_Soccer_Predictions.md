# 📊 Predicting 2024/25 Premier League Win Probabilities Based on 2023/24 Data


This estimates the win probabilities for Premier League teams in the **2024/25 season**, using their performance in the **2023/24 season**.

I assumed that each team's win behavior follows a **Bernoulli distribution**, and use a **Binomial model** to estimate the likelihood of winning a given number of games in the next season.



```python
# to import libraries
import requests
import pandas as pd
from scipy.stats import binom
import matplotlib.pyplot as plt
import seaborn as sns

```


```python
# Setting the API Parameters
API_KEY = 'a98db8dfe04d29cbb45dbb09c2d6f8bb'
BASE_URL = 'https://v3.football.api-sports.io'
HEADERS = {'x-apisports-key': API_KEY}

league_id = 39  # Premier League
season = 2023   # Use last season to build prediction

```


```python
# to get the Premier League 2023/2024 Season Fixtures:
params = {'league': league_id, 'season': season}
response = requests.get(f'{BASE_URL}/fixtures', headers=HEADERS, params=params)
fixtures = response.json()['response']
print(f"Fixtures found: {len(fixtures)}")

```

    Fixtures found: 380



```python
# to build the results DataFrame
# by extracting the winners only from matches that are complete (FT).
matches = []
for match in fixtures:
    if match['fixture']['status']['short'] == 'FT':
        home = match['teams']['home']['name']
        away = match['teams']['away']['name']
        winner = match['teams']['home']['winner']
        if winner is None:
            result = 'Draw'
        elif winner:
            result = home
        else:
            result = away
        matches.append({'home': home, 'away': away, 'winner': result})

df = pd.DataFrame(matches)
df.head()

```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>home</th>
      <th>away</th>
      <th>winner</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Burnley</td>
      <td>Manchester City</td>
      <td>Manchester City</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Arsenal</td>
      <td>Nottingham Forest</td>
      <td>Arsenal</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Bournemouth</td>
      <td>West Ham</td>
      <td>Draw</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Everton</td>
      <td>Fulham</td>
      <td>Fulham</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Brighton</td>
      <td>Luton</td>
      <td>Brighton</td>
    </tr>
  </tbody>
</table>
</div>




```python
# To Count the wins & matches per Team

# list all unique teams:
teams = pd.unique(df[['home', 'away']].values.ravel())

#this initializes win and match counters
team_wins = {team: 0 for team in teams}
team_matches = {team: 0 for team in teams}

#to count the wins & total matches:
for _, row in df.iterrows():
    if row['winner'] != 'Draw':
        team_wins[row['winner']] += 1
    team_matches[row['home']] += 1
    team_matches[row['away']] += 1

# To create a Dataframe for win stats:
win_df = pd.DataFrame({
    'Team': list(teams),
    'Wins': [team_wins[t] for t in teams],
    'Matches': [team_matches[t] for t in teams]
})

# To calculate win probabilities in Bernoulli:
win_df['Historical_Win_Prob'] = win_df['Wins'] / win_df['Matches']
win_df.sort_values(by='Historical_Win_Prob', ascending=False, inplace=True)
win_df.reset_index(drop=True, inplace=True)
win_df.head()

```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Team</th>
      <th>Wins</th>
      <th>Matches</th>
      <th>Historical_Win_Prob</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Manchester City</td>
      <td>28</td>
      <td>38</td>
      <td>0.736842</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Arsenal</td>
      <td>28</td>
      <td>38</td>
      <td>0.736842</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Liverpool</td>
      <td>24</td>
      <td>38</td>
      <td>0.631579</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Tottenham</td>
      <td>20</td>
      <td>38</td>
      <td>0.526316</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Aston Villa</td>
      <td>20</td>
      <td>38</td>
      <td>0.526316</td>
    </tr>
  </tbody>
</table>
</div>



## 📈 Predicting Win Outcomes for 2024/25 Season (Based on 2023/24 Win Rates)


```python
# assume a 38 game season
n = 38  # total matches in a season
k = 20  # threshold for high performance : target to win 20 or more games

# A function created to calculate P(X ≥ k)
def predict_next_season_win_prob(team_name, k, n):
    p = win_df[win_df['Team'] == team_name]['Historical_Win_Prob'].values[0]
    return 1 - binom.cdf(k - 1, n, p)

# To add a new column for the probability
win_df[f'P(Win ≥ {k}/{n})_Predicted'] = win_df['Team'].apply(lambda t: predict_next_season_win_prob(t, k, n))
win_df[['Team', 'Wins', 'Matches', 'Historical_Win_Prob', f'P(Win ≥ {k}/{n})_Predicted']]

```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Team</th>
      <th>Wins</th>
      <th>Matches</th>
      <th>Historical_Win_Prob</th>
      <th>P(Win ≥ 20/38)_Predicted</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Manchester City</td>
      <td>28</td>
      <td>38</td>
      <td>0.736842</td>
      <td>9.984756e-01</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Arsenal</td>
      <td>28</td>
      <td>38</td>
      <td>0.736842</td>
      <td>9.984756e-01</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Liverpool</td>
      <td>24</td>
      <td>38</td>
      <td>0.631579</td>
      <td>9.331216e-01</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Tottenham</td>
      <td>20</td>
      <td>38</td>
      <td>0.526316</td>
      <td>5.654862e-01</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Aston Villa</td>
      <td>20</td>
      <td>38</td>
      <td>0.526316</td>
      <td>5.654862e-01</td>
    </tr>
    <tr>
      <th>5</th>
      <td>Chelsea</td>
      <td>18</td>
      <td>38</td>
      <td>0.473684</td>
      <td>3.125301e-01</td>
    </tr>
    <tr>
      <th>6</th>
      <td>Newcastle</td>
      <td>18</td>
      <td>38</td>
      <td>0.473684</td>
      <td>3.125301e-01</td>
    </tr>
    <tr>
      <th>7</th>
      <td>Manchester United</td>
      <td>18</td>
      <td>38</td>
      <td>0.473684</td>
      <td>3.125301e-01</td>
    </tr>
    <tr>
      <th>8</th>
      <td>West Ham</td>
      <td>14</td>
      <td>38</td>
      <td>0.368421</td>
      <td>3.399319e-02</td>
    </tr>
    <tr>
      <th>9</th>
      <td>Crystal Palace</td>
      <td>13</td>
      <td>38</td>
      <td>0.342105</td>
      <td>1.483543e-02</td>
    </tr>
    <tr>
      <th>10</th>
      <td>Wolves</td>
      <td>13</td>
      <td>38</td>
      <td>0.342105</td>
      <td>1.483543e-02</td>
    </tr>
    <tr>
      <th>11</th>
      <td>Fulham</td>
      <td>13</td>
      <td>38</td>
      <td>0.342105</td>
      <td>1.483543e-02</td>
    </tr>
    <tr>
      <th>12</th>
      <td>Everton</td>
      <td>13</td>
      <td>38</td>
      <td>0.342105</td>
      <td>1.483543e-02</td>
    </tr>
    <tr>
      <th>13</th>
      <td>Bournemouth</td>
      <td>13</td>
      <td>38</td>
      <td>0.342105</td>
      <td>1.483543e-02</td>
    </tr>
    <tr>
      <th>14</th>
      <td>Brighton</td>
      <td>12</td>
      <td>38</td>
      <td>0.315789</td>
      <td>5.641358e-03</td>
    </tr>
    <tr>
      <th>15</th>
      <td>Brentford</td>
      <td>10</td>
      <td>38</td>
      <td>0.263158</td>
      <td>4.950162e-04</td>
    </tr>
    <tr>
      <th>16</th>
      <td>Nottingham Forest</td>
      <td>9</td>
      <td>38</td>
      <td>0.236842</td>
      <td>1.075458e-04</td>
    </tr>
    <tr>
      <th>17</th>
      <td>Luton</td>
      <td>6</td>
      <td>38</td>
      <td>0.157895</td>
      <td>1.677306e-07</td>
    </tr>
    <tr>
      <th>18</th>
      <td>Burnley</td>
      <td>5</td>
      <td>38</td>
      <td>0.131579</td>
      <td>7.352472e-09</td>
    </tr>
    <tr>
      <th>19</th>
      <td>Sheffield Utd</td>
      <td>3</td>
      <td>38</td>
      <td>0.078947</td>
      <td>7.290835e-13</td>
    </tr>
  </tbody>
</table>
</div>




```python
# To visualize the Win Probabilities (Seaborn & Matplotlib):

plt.figure(figsize=(12, 6))
sns.barplot(x='Historical_Win_Prob', y='Team', data=win_df, palette='crest')
plt.title('Predicted Win Probability per Team (2024/25) Based on 2023/24 Performance')
plt.xlabel('Estimated Win Probability')
plt.ylabel('Team')
plt.grid(True)
plt.tight_layout()
plt.show()

```

    C:\Users\Loryne\AppData\Local\Temp\ipykernel_34812\1911727344.py:2: FutureWarning: 
    
    Passing `palette` without assigning `hue` is deprecated and will be removed in v0.14.0. Assign the `y` variable to `hue` and set `legend=False` for the same effect.
    
      sns.barplot(x='Historical_Win_Prob', y='Team', data=win_df, palette='crest')



    
![png](output_9_1.png)
    



---

### ✅ Summary
- Win_Probability shows past performance (how often they win).

- P(Win ≥ 20/38) predicts how likely they are to win 20+ games in a season, based on a Binomial model.

- I used historical match results from 2023/24 to estimate win probabilities for the 2024/25 season.


