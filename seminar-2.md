# Python Seminar 2: Functional Programming

```python
# Let us start with a silly example:

x = [2001, 2002, 2003, 2004]

# The task is to shift all the years by one:

for i in range(len(x)):
    x[i] = x[i] + 1

print(x)

# Suppose we have another vector:

y = [1, 2, 3, 4, 5]

# We face the same task, so let's just copy paste the code:

for i in range(len(y)):
    y[i] = y[i] + 1

print(y)

# We have two pieces of similar code in two different places now.
# Maintenance costs are roughly twice as large.
# To avoid code duplication we use functions.

def shift(v):
    for i in range(len(v)):
        v[i] = v[i] + 1
    return v

x = [2001, 2002, 2003, 2004]
print(shift(x))

y = [1, 2, 3, 4, 5]
print(shift(y))

# If we want to make further changes to the code, there is a single place to do it.
# Say, we want to be able to shift the years by an arbitrary offset:

def shift(v, offset = 1):
    for i in range(len(v)):
        v[i] = v[i] + offset
    return v

x = [2001, 2002, 2003, 2004]
print(shift(x, 2))

# But there is a problem with the code:

x = [2001, 2002, 2003, 2004]
print(shift(x, 1)) # we want [2002, 2003, 2004, 2005]
print(shift(x, -1)) # we want [2000, 2001, 2002, 2003]

# We got unexpected results because our function changes its arguments.
# Almost always, it is possible to write functions that do not change its arguments.
# Doing so makes it easier to reason about the behaviour of the code and avoids
# hard to debug issues.

def shift(v, offset = 1):
    r = []
    for e in v:
        r.append(e + offset)
    return r

x = [2001, 2002, 2003, 2004]
print(shift(x, 1))
print(shift(x, -1))

# Or, with list comprehension:

def shift(v, offset = 1):
    return [e + offset for e in v]

# In practice, in this particular case, we might simply use numpy:

import numpy as np
x = np.array([2001, 2002, 2003, 2004])
print(x + 1)
```

## Exercises

```python
shops = [['Minke', 0, 0],
         ['CPuB', 0, 1],
         ['noname', 1, 0]]

# Make a list of shop pairs no more than 1.2 units apart; use at least three functions.

from math import sqrt

def pairs(s):
    r = []
    for i in range(len(s)):
        for j in range(i):
            r.append([s[i], s[j]])
    return r

def dist(p):
    return sqrt((p[0][1] - p[1][1])**2 + (p[0][2] - p[1][2])**2)

def filter(s, maxdist):
    r = []
    for p in pairs(s):
        if dist(p) < maxdist:
            r.append(p)
    return r

print(filter(shops, 1.2))

# Or, with list comprehensions:

def pairs(s):
    return [[s[i], s[j]] for i in range(len(s)) for j in range(i)]

def dist(p):
    return sqrt((p[0][1] - p[1][1])**2 + (p[0][2] - p[1][2])**2)

def filter(s, maxdist):
    return [p for p in pairs(s) if dist(p) < maxdist]

print(filter(shops, 1.2))

# Finally, we can use iterators to make this operation memory efficient

def pairs(s):
    for i in range(len(s)):
        for j in range(i):
            yield [s[i], s[j]]

def dist(p):
    return sqrt((p[0][1] - p[1][1])**2 + (p[0][2] - p[1][2])**2)

def filter(s, maxdist):
    for p in pairs(s):
        if dist(p) < maxdist:
            yield p

print(list(filter(shops, 1.2)))

# P.S. This operation can be made efficient for thousands of shops with use of spatial indexing
```
