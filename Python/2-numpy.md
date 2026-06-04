# Python Reference Guide: 2. NumPy

> **Optimize for**: Data Analytics, Risk Analytics, Finance, Banking, Data Science

---

## Arrays

### Definition
NumPy arrays are n-dimensional arrays for efficient numerical computing. Foundation for pandas and scikit-learn.

### Syntax
```python
import numpy as np
array = np.array([items])
```

### Basic Example
```python
import numpy as np

# Create arrays
array_1d = np.array([1, 2, 3, 4, 5])
array_2d = np.array([[1, 2, 3], [4, 5, 6]])
array_3d = np.array([[[1, 2], [3, 4]], [[5, 6], [7, 8]]])

# Check shape and type
print(array_1d.shape)  # (5,)
print(array_2d.shape)  # (2, 3)
print(array_2d.dtype)  # int64

# Create special arrays
zeros = np.zeros((3, 3))  # 3x3 matrix of zeros
ones = np.ones((2, 4))    # 2x4 matrix of ones
identity = np.eye(3)      # 3x3 identity matrix
range_array = np.arange(0, 10, 2)  # [0, 2, 4, 6, 8]
linspace = np.linspace(0, 1, 5)    # [0, 0.25, 0.5, 0.75, 1]
```

### Intermediate Example
```python
# Random arrays
np.random.seed(42)  # For reproducibility
random_array = np.random.rand(3, 4)  # Random 3x4 array [0, 1)
random_normal = np.random.randn(1000)  # Normal distribution
random_integers = np.random.randint(1, 100, 50)  # Random integers 1-99

# Array properties
array = np.array([1, 2, 3, 4, 5])
print(f"Size: {array.size}")  # 5
print(f"Ndim: {array.ndim}")  # 1
print(f"Dtype: {array.dtype}")  # int64

# Reshaping
array = np.arange(12)
reshaped = array.reshape(3, 4)  # 3x4 matrix
flattened = reshaped.flatten()  # Back to 1D
ravel = reshaped.ravel()  # Also 1D (view, not copy)
```

### Advanced Example
```python
# Broadcasting - automatic expansion for operations
a = np.array([[1, 2, 3], [4, 5, 6]])  # Shape (2, 3)
b = np.array([10, 20, 30])  # Shape (3,)
result = a + b  # b is broadcasted to match a's shape
# [[11, 22, 33],
#  [14, 25, 36]]

# Stacking arrays
array1 = np.array([1, 2, 3])
array2 = np.array([4, 5, 6])
v_stack = np.vstack([array1, array2])  # Vertical stack
h_stack = np.hstack([array1, array2])  # Horizontal stack

# Advanced indexing
matrix = np.arange(12).reshape(3, 4)
print(matrix[1, 2])  # Element at row 1, col 2: 6
print(matrix[:, 1])  # All rows, col 1: [1, 5, 9]
print(matrix[1, :])  # Row 1, all cols: [4, 5, 6, 7]
mask = matrix > 5
print(matrix[mask])  # Elements > 5: [6, 7, 8, 9, 10, 11]
```

### Common Mistakes

❌ **Not understanding array copies vs views**
```python
array = np.array([1, 2, 3])
view = array[:]  # This is a view, not a copy
view[0] = 100
print(array)  # [100, 2, 3] - Original changed!
```

✅ **Explicitly copy when needed**
```python
copy = array.copy()  # Create independent copy
copy[0] = 100
print(array)  # [1, 2, 3] - Unchanged
```

---

## Indexing & Slicing

### Basic Example
```python
array = np.array([10, 20, 30, 40, 50])

# Simple indexing
print(array[0])    # 10
print(array[-1])   # 50
print(array[-2])   # 40

# Slicing
print(array[1:4])     # [20, 30, 40]
print(array[:3])      # [10, 20, 30]
print(array[2:])      # [30, 40, 50]
print(array[::2])     # [10, 30, 50] - every 2nd element
print(array[::-1])    # [50, 40, 30, 20, 10] - reversed
```

### Intermediate Example
```python
# 2D indexing
matrix = np.arange(12).reshape(3, 4)

# Element access
print(matrix[1, 2])      # 6
print(matrix[0, :])      # [0, 1, 2, 3] - Row 0
print(matrix[:, 1])      # [1, 5, 9] - Column 1

# Submatrix
print(matrix[0:2, 1:3])  # [[1, 2], [5, 6]]

# Boolean indexing
mask = matrix > 5
print(matrix[mask])  # [6, 7, 8, 9, 10, 11]

# Fancy indexing
indices = np.array([0, 2, 1])
print(matrix[indices])  # Rows 0, 2, 1
```

### Advanced Example
```python
# Multi-dimensional boolean indexing
matrix = np.arange(12).reshape(3, 4)

# Complex condition
mask = (matrix > 3) & (matrix < 10)
print(matrix[mask])  # [4, 5, 6, 7, 8, 9]

# np.where for conditional selection
result = np.where(matrix > 5, "high", "low")
print(result)
# [['low' 'low' 'low' 'low']
#  ['low' 'high' 'high' 'high']
#  ['high' 'high' 'high' 'high']]

# Advanced slicing with steps
array = np.arange(20)
every_3rd = array[::3]  # [0, 3, 6, 9, 12, 15, 18]
reverse_every_2nd = array[-1::-2]  # [19, 17, 15, ...]
```

---

## Vectorization

### Definition
Performing operations on entire arrays without explicit loops. Much faster than Python loops.

### Basic Example
```python
import numpy as np

# Vectorized operations (fast)
balances = np.array([100, 200, 300, 400, 500])
interest_rate = 0.05
balances_with_interest = balances * (1 + interest_rate)
print(balances_with_interest)  # [105. 210. 315. 420. 525.]

# Equivalent loop (slow)
balances_loop = []
for b in balances:
    balances_loop.append(b * (1 + interest_rate))
```

### Intermediate Example
```python
# Element-wise operations
a = np.array([1, 2, 3])
b = np.array([4, 5, 6])

print(a + b)     # [5, 7, 9]
print(a * b)     # [4, 10, 18]
print(a ** b)    # [1, 32, 729]
print(np.sqrt(a))  # [1, 1.414, 1.732]
print(np.exp(a))   # [2.718, 7.389, 20.086]
print(np.log(a))   # [0, 0.693, 1.099]
```

### Advanced Example
```python
# Portfolio returns calculation
prices_t0 = np.array([100, 150, 200, 50])  # Initial prices
prices_t1 = np.array([105, 140, 220, 48])  # Final prices

# Vectorized calculation
returns = (prices_t1 - prices_t0) / prices_t0
print(returns)  # [0.05, -0.067, 0.1, -0.04]

# Cumulative returns
cumulative_returns = np.cumprod(1 + returns) - 1
print(cumulative_returns)  # [0.05, -0.067, 0.1, -0.04]

# Performance comparison
import timeit

# Vectorized (fast)
def vectorized():
    return (prices_t1 - prices_t0) / prices_t0

# Loop (slow)
def loop_based():
    return np.array([(prices_t1[i] - prices_t0[i]) / prices_t0[i] 
                     for i in range(len(prices_t0))])

print(f"Vectorized: {timeit.timeit(vectorized, number=100000)}")
print(f"Loop: {timeit.timeit(loop_based, number=100000)}")
# Vectorized is typically 10-100x faster
```

---

## Broadcasting

### Definition
Automatic expansion of arrays to compatible shapes for element-wise operations.

### Rules
1. If arrays have different numbers of dimensions, pad the smaller with 1s
2. For each dimension, sizes must match or one must be 1
3. Dimension with size 1 is stretched to match the other

### Examples
```python
import numpy as np

# Example 1: 1D + scalar
array = np.array([1, 2, 3, 4, 5])
result = array + 10  # Scalar broadcast to [10, 10, 10, 10, 10]
print(result)  # [11, 12, 13, 14, 15]

# Example 2: (3, 1) + (3,)
matrix = np.array([[1], [2], [3]])  # Shape (3, 1)
vector = np.array([10, 20, 30])     # Shape (3,)
result = matrix + vector  # Broadcast to (3, 3)
# [[11, 21, 31]
#  [12, 22, 32]
#  [13, 23, 33]]

# Example 3: (1, 3) + (3,) -> (1, 3)
row = np.array([[1, 2, 3]])  # Shape (1, 3)
vector = np.array([10, 20, 30])  # Shape (3,)
result = row + vector  # [[11, 22, 33]]

# Business case: Risk-adjusted returns
returns = np.array([0.05, 0.10, 0.08])  # Shape (3,)
risk_adjustment = np.array([[0.9], [0.95], [1.0]])  # Shape (3, 1)
adjusted_returns = returns * risk_adjustment  # Broadcasting (3, 3)
```

---

## Statistical Functions

### Basic Example
```python
import numpy as np

data = np.array([1, 2, 3, 4, 5, 100])  # Note the outlier

# Basic statistics
print(np.mean(data))      # 19.167 - average
print(np.median(data))    # 3.5 - middle value
print(np.std(data))       # 39.66 - standard deviation
print(np.var(data))       # 1573.14 - variance
print(np.min(data))       # 1
print(np.max(data))       # 100
print(np.sum(data))       # 115
```

### Intermediate Example
```python
# Percentiles
data = np.array([1, 2, 3, 4, 5, 6, 7, 8, 9, 10])
print(np.percentile(data, 25))  # 3.25 - 25th percentile (Q1)
print(np.percentile(data, 50))  # 5.5 - 50th percentile (median)
print(np.percentile(data, 75))  # 7.75 - 75th percentile (Q3)
print(np.percentile(data, 95))  # 9.55 - 95th percentile

# Quantile (same as percentile but 0-1 scale)
print(np.quantile(data, 0.25))  # 3.25
print(np.quantile(data, 0.95))  # 9.55

# IQR (Interquartile Range) for outlier detection
Q1 = np.percentile(data, 25)
Q3 = np.percentile(data, 75)
IQR = Q3 - Q1
lower_bound = Q1 - 1.5 * IQR
upper_bound = Q3 + 1.5 * IQR
outliers = data[(data < lower_bound) | (data > upper_bound)]
```

### Advanced Example
```python
# Risk metrics for financial data
returns = np.array([0.02, 0.015, 0.03, -0.01, 0.025, 0.01, -0.005])

# Annualized return (252 trading days)
annual_return = np.mean(returns) * 252
print(f"Annual return: {annual_return:.4f}")

# Annualized volatility
annual_volatility = np.std(returns) * np.sqrt(252)
print(f"Annual volatility: {annual_volatility:.4f}")

# Sharpe Ratio (assuming 2% risk-free rate)
risk_free_rate = 0.02
sharpe_ratio = (annual_return - risk_free_rate) / annual_volatility
print(f"Sharpe Ratio: {sharpe_ratio:.4f}")

# Cumulative returns
cum_returns = np.cumprod(1 + returns) - 1
max_drawdown = np.min(cum_returns)  # Worst cumulative return
print(f"Max Drawdown: {max_drawdown:.4f}")

# Value at Risk (VaR) at 95% confidence
var_95 = np.percentile(returns, 5)  # 5th percentile
print(f"VaR (95%): {var_95:.4f}")
```

---

## Common Mistakes & Best Practices

### Mistake 1: Not Understanding Array Dtypes
```python
❌ array = np.array([1, 2, 3])
print(array.dtype)  # int64
print(array / 2)    # [0, 1, 1] - Integer division!

✅ array = np.array([1, 2, 3], dtype=float)
print(array / 2)    # [0.5, 1., 1.5]
```

### Mistake 2: Performance with Python Lists
```python
❌ result = []
for i in range(1000000):
    result.append(i * 2)  # Very slow

✅ result = np.arange(1000000) * 2  # Fast
```

### Best Practices
✅ Use vectorized operations instead of loops
✅ Specify dtype when creating arrays if precision matters
✅ Use broadcasting to avoid unnecessary loops
✅ Use appropriate statistical functions for analysis

