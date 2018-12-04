# Python Seminar 7: Regular Expressions

```python
# Load libraries
import re

# We will work with the following string
txt = "Andrei Dubovik bought 10 cucumbers and 20 bottles of vodka"

# A few standard string operations
# Substrings
print(txt[25:34]) # From 25 till (excluding) 34

# Splitting
print(txt.split(' ')[4])

# Regular expressions allow to formulate complex search patterns
# Let us start with a basic search string
m = re.search('cucumbers', txt)
print(m.group(0)) # We will talk about groups shortly

# '.' means any symbol
m = re.search('cuc.mbers', txt)
print(m.group(0)) # gives 'cucumbers'

m = re.search('cuc..bers', txt)
print(m.group(0)) # gives 'cucumbers'

# '+' means any preceding symbol one or more times
m = re.search('bot+les', txt) # will match 'botles', 'bottles', 'botttles', etc
print(m.group(0))

# '.' and '+' can be combined
m = re.search('cuc.+s', txt)
print(m.group(0)) # gives 'cucumbers and 20 bottles'
# Notice that regular expressions are greedy by default:
# the longest possible match was found

# '[a-z]' means any symbol between 'a' and 'z'
m = re.search('cuc[a-z]+s', txt)
print(m.group(0)) # gives 'cucumbers'

# '[^ ]' means any symbol but ' '
m = re.search('cuc[^ ]+s', txt)
print(m.group(0)) # also gives 'cucumbers'

# There are many more rules, see online documentation:
# https://docs.python.org/3/library/re.html

# In economics, regular expressions are often useful if we
# need to extract numeric information from strings
m = re.search('[0-9]+ cucumbers', txt)
print(m.group(0)) # gives '10 cucumbers'

# '()' can be used to denote subgroups in the match
m = re.search('([0-9]+) (cucumbers)', txt)
print(m.group(0)) # gives '10 cucumbers'
print(m.group(1)) # gives '10'
print(m.group(2)) # gives 'cucumbers'
```

## Exercises

```python
# Exercise: extract item names and respective counts given a string
# such as '... bought N ... and M ...'; collect the results into a dictionary
m1 = re.search('([0-9]+) (.+) and [0-9]', txt)
m2 = re.search('and ([0-9]+) (.+)', txt)
db = {m1.group(2):int(m1.group(1)), m2.group(2):int(m2.group(1))}
# db = {'bottles of vodka': 20, 'cucumbers': 10}

# Exercise, same idea but a different string layout:
# (Assume that Oxford comma is always observed)
# Hint: use findall
txt = "Mr. John bought 15 beers, 8 white rabbits, 10 carrots, and 1 casserole."

m = re.findall('([0-9]+) ([^,.]*)', txt)
db = {g[1]:int(g[0]) for g in m}
# db = {'beers': 15, 'carrots': 10, 'casserole': 1, 'white rabbits': 8}

# We can use 'findall' with the first example, but then the regular
# expression needs to be more elaborate
txt = "Andrei Dubovik bought 10 cucumbers and 20 bottles of vodka"
m = re.findall('([0-9]+) (.+?)(?: and|$)', txt)
db = {g[1]:int(g[0]) for g in m}
# '+?' means one or more occurances, non-greedy
# '$' means end of string
# '|' means either either
```
