# Python Seminar 1: Basic Data Structures

```python
# Lists can contain any objects
x = [1, 2, 'tango', [4, 5]]

# ... including themselves (rarely necessary in practice)
x[3] = x

# Lists are ordered collections
[1, 2] == [2, 1] # returns False

# Dictionaries are unordered collections, accessed by key
y = {'blue': 10, 'red': 20}
y == {'red': 20, 'blue': 10} # returns True
y['red'] = 100
# etc

# You can have dictionaries and lists arbitrary nested
y  = {'a': 1, ' b': [13, 'zzz', {}]}

# Creating lists and dictionaries sequentially
x = []
x.append(1)
x.append(5)

y = {}
y['andrei'] = 100
y['michiel'] = 100

# Iterating over
for el in x:
    print(el)

for k,v in y.items():
    print(k)
    print(v)
```

## Exercises

```python
db=[{'name':'andrei', 'cash':30, 'gender':'m'},
    {'name':'karen', 'cash':5, 'gender':'f'},
    {'name':'benedikt', 'cash':100, 'gender':'m'}]

# Beginners:
# Obtain {'m':65, 'f':5}

r = {}
for row in db:
    r[row['gender']] = []

for row in db:
    r[row['gender']].append(row['cash'])

for k,v in r.items():
    r[k] = sum(v)/len(v)

print(r)

# Alternatively, with list comprehensions
r = {row['gender']:[] for row in db}
[r[row['gender']].append(row['cash']) for row in db]
r =  {k:sum(v)/len(v) for k,v in r.items()}
print(r)


# Intermediate:
# Do a two-liner using groupby from itertools
# This is more complex, but better corresponds to the logic of what we
# actually want to do
from itertools import groupby
from statistics import mean

db = sorted(db, key = lambda r: r['gender'])
r = {k:mean([r['cash'] for r in v]) for k,v in groupby(db, lambda r: r['gender'])}

# Easiest solution, in this particular case:
import pandas as pd

db = pd.DataFrame(db)
r = db.groupby('gender')['cash'].apply(mean)
```
