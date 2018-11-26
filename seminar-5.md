# Python Seminar 5: Pandas

```python
import numpy as np
import pandas as pd

# Pandas objects always get aligned on their indices
income = pd.Series([100, 200, 205], index = [2000, 2001, 2002])
consumption = pd.Series([80, 100, 100], index = [2001, 2002, 2003])
ratio = consumption/income # Years got aligned

# If no indices are given explicitly, implicit indices are constructed
income = pd.Series([100, 200, 205, np.nan])
consumption = pd.Series([np.nan, 80, 100, 100])

# Indices are preserved if rows are dropped, etc.
income = income[income > 100]
ratio = consumption/income
# Note that the above operation is permissible
# despite unequal lengths, due to index alignment

# We can stack series to obtain a data frame
income = pd.Series([100, 200, 205], index = [2000, 2001, 2002], name = 'income')
consumption = pd.Series([80, 100, 100], index = [2001, 2002, 2003], name = 'consumption')
df = pd.concat((income, consumption), axis = 1)

# Alternatively, we can use data frames and "merge"
income = pd.DataFrame({'year': [2000, 2001, 2002], 'income': [100, 200, 205]})
consumption = pd.DataFrame({'year': [2001, 2002, 2003], 'consumption': [80, 100, 100]})
df = pd.merge(income, consumption, how = 'outer')

# Element access
df['ratio'] = df['consumption']/df['income']
# or
df['ratio'] = df.consumption/df.income
# At each operation, devision, assignment, the indices get aligned
```

## Exercises

```python
land = pd.DataFrame({'country': ['FR', 'DE', 'IT'], 'size': [200, 250, 180]})
ts = pd.DataFrame({'year': [1, 2], 'weather': [100, 102]})
prod = pd.DataFrame({'year': [1, 2, 1, 2, 1, 2],
                     'country': ['FR', 'FR', 'DE', 'DE', 'IT', 'IT'],
                     'wine': [234, 324, 11, 123, 34, 234]})

# 1) Create combined dataset
df = pd.merge(pd.merge(prod, land), ts)

# 2) Use "groupby" to compute total yearly wine production
total = df.groupby('year').wine.sum()

# 3) Add results to the dataset (try "reset_index")
total.name = 'total'
df = pd.merge(df, total.reset_index())

# 4) Compute yearly change in production (try "shift")
# If there are no gaps in years
# Also, you might need to sort on years first
df['change_a'] = df.wine - df.groupby('country').wine.shift(1)

# If there are gaps in years
ldf = df[['year', 'country', 'wine']].copy()
ldf = ldf.rename(columns = {'wine': 'lwine'})
ldf.year = ldf.year + 1
df = pd.merge(df, ldf, how = 'left')
df['change_b'] = df.wine - df.lwine
```
