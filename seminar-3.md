# Python Seminar 3: Numpy

```python
# Numpy is used for working with vectors, matrices and, in general, multidimensional
# arrays. For these operations Numpy provides a cleaner syntax, more efficient operation,
# and richer functionality than basic Python

# Basic python
x = [1, 2, 3, 4]
y = [10, 20, 30, 40]
z = [xi + yi for xi, yi in zip(x, y)]

# Numpy
import numpy as np
x = np.array([1, 2, 3, 4])
y = np.array([10, 20, 30, 40])
z = x + y

# Matrices
m = np.array([[1,2], [3,4]])

# Making a column vector from a row vector
xc = x[:, np.newaxis]
# or
xc = x.reshape(-1, 1) # -1 means the suitable number, see the documentation

# Broadcasting (arrays are "stretched" along missing dimensions)
m = xc + y

# But also x + 1 involves broadcasting of 1

# Numpy operations can be done over given axes
m = np.arange(24).reshape(2, 3, -1)
s = np.sum(m) # sum all elements
s = np.sum(m, axis = 0) # sum along 0 axis (hence, returns a matrix)
s = np.sum(m, axis = (0, 1)) # sum along axes 0 and 1 (hence, returns a vector)
```

## Exercises

```python
x = np.array([0,0,1])
y = np.array([0,1,0])

# Beginners: compute distance matrix
x2 = x.reshape(-1, 1)
y2 = y.reshape(-1, 1)
dm = np.sqrt((x - x2)**2 + (y - y2)**2)

# Intermediate:
# Probability that a shopper moves from shop A to
# shop B is proportional to the distance to that shop.
# Compute the stable allocation of shoppers.

# Transition matrix (must sum to 1 over columns)
tm = dm/np.sum(dm, axis = 0) # This is summation for each column

# Compute the stable state iteratively
no_iter = 10
s = np.ones(tm.shape[0])
s = s/np.sum(s)

for i in range(no_iter):
    s = np.dot(tm, s)

print(s)

# Use a bit of math knowledge to compute the stable state
w, v = np.linalg.eig(tm)
s = v[:, w == 1] # We need the eighevector corresponding to the unit eigenvalue
s = s/np.sum(s) # Numpy does L2 normalization, we want L1
print(s)
```
