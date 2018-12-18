# Python Seminar 8: Tournament

Consider the following game:

P1\P2 |    C    |    C'    |    D
------|---------|----------|----------
  C   |  0, 0   |  -2, 1   |  -5, 2
  C'  |  1, -2  |  -1, -1  |  -4, 1
  D   |  2, -5  |  1, -4   |  -3, -3

This game is an extension of a standard prisoners' dilemma game. Each player can play not only Cooperate and Deviate, but also an intermediate option which we denote C'. By convention, the first number is the payoff to the first player. E.g., if Player 1 plays C' and Player 2 plays D, then Player 1 gets -4 and Player 2 gets 1.

We consider a repeated setting where the above game is played 100 times. You goal is to write a python script that chooses which action to play each period given the history of past interactions. Total profits are discounted profits from all the plays, with a discount factor of 0.99. We will play the game in a round-robin fashion, i.e. each team is pitted against each other team. The final winner is the team that scores most.

Below is the code we will use to play the game, with some example strategies.

```python
# Load packages
import numpy as np
import pandas as pd

# Define single stage payoff matrix
game = np.array(
    [[0, -2, -5],
     [1, -1, -4],
     [2,  1, -3]])

# Define discount factor
delta = 0.99

# Define number of periods
periods = 100

# Game engine

def rotate(lst, n):
    '''Prepare arguments for the n-th player'''
    if len(lst) == 0:
        return None
    else:
        return np.roll(np.array(lst), n, axis = 1)

def play_turn(t, actions, profits, p1, p2, d1, d2):
    '''Play a single round'''
    a1 = p1(t, rotate(actions, 0), rotate(profits, 0), d1)
    a2 = p2(t, rotate(actions, 1), rotate(profits, 1), d2)
    p1 = game[a1, a2]
    p2 = game[a2, a1]
    actions.append((a1, a2))
    profits.append((p1, p2))

def play_game(p1, p2):
    '''Play one game'''
    actions = []
    profits = []
    d1, d2 = {}, {}
    for t in range(periods):
        play_turn(t, actions, profits, p1, p2, d1, d2)
    profits = np.array(profits)
    return np.sum(profits.T*delta**np.arange(periods), axis = 1)

def pretty(players, pm):
    '''Put statistics into a pandas dataset'''
    total = np.sum(pm, axis = 1)
    s = np.argsort(total)[::-1]
    names = np.array([p.__name__ for p in players])
    pm = pm[s][:,s]
    names = names[s]
    result = pd.DataFrame(pm, columns = names, index = names)
    result['total'] = np.sum(result, axis = 1)
    return result

def play_tournament(players):
    '''Play round-robin tournament'''
    size = len(players)
    pm = np.full((size, size), 0.0)
    for i in range(size):
        for j in range(i):
           profits = play_game(players[i], players[j])
           pm[i, j] = profits[0]
           pm[j, i] = profits[1]
    return pretty(players, pm)

# Players' strategies (examples)

# 't' is the turn number, from 0 (first turn) till 19 (last turn)
# 'actions' is a numpy array with past actions
# actions[:,0] gives own past actions
# actions[:,1] gives those of the opponent
# Similarly for 'profits'
# 'data' is a dictionary for user-defined data

def andrei(t, actions, profits, data):
    '''Andrei plays cooperatively'''
    my_action = 0
    return my_action

def freek(t, actions, profits, data):
    '''Freek plays aggressively'''
    my_action = 2
    return my_action

def minke(t, actions, profits, data):
    '''Minke plays tit-for-tat'''
    if t == 0: # If first turn
        my_action = 0
    else:
        my_action = actions[-1, 1] # Opponents last action
    return my_action

def fien(t, actions, profits, data):
    '''Fien uses level zero reinforcement learning'''
    c = 2
    q = data.setdefault('Q', np.mean(game, axis = 1))
    n = data.setdefault('N', np.full(3, 0))
    if t > 0:
        a = actions[-1, 0] # My past action
        q[a] = q[a] + 1/n[a]*(profits[-1, 0] - q[a])
    my_action = np.argmax(q + c*np.sqrt(np.log(t+1)/np.maximum(n, 1e-8)))
    n[my_action] = 1 + n[my_action]
    return my_action

# Tournament
# Collect all strategies
# This needs to be re-evaluated each time the respective function changes
players = [andrei, freek, minke, fien]

# Play tournament
result = play_tournament(players)
# Freek wins

# What if andrei did not play?
players = [freek, minke, fien]
result = play_tournament(players)
# Freek loses
```
