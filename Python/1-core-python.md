# Python Reference Guide: 1. Core Python

> **Optimize for**: Data Analytics, Risk Analytics, Finance, Banking, Data Science

---

## Variables & Data Types

### Definition
Variables store data values. Python is dynamically typed (infers types automatically).

### Syntax
```python
variable_name = value
```

### Basic Example
```python
# String
customer_name = "John Smith"

# Integer
account_balance = 150000

# Float (decimal)
interest_rate = 0.045

# Boolean
is_active = True

# Check type
print(type(account_balance))  # <class 'int'>
print(type(interest_rate))    # <class 'float'>
```

### Intermediate Example
```python
# Type conversion
balance_str = "100000"
balance_int = int(balance_str)  # Convert string to int
balance_float = float(balance_str)  # Convert to float

# Multiple assignment
x, y, z = 10, 20, 30

# Unpacking
data = [100, 200, 300]
a, b, c = data

# Constants (convention: UPPERCASE)
ANNUAL_INTEREST_RATE = 0.05
MAX_LOAN_AMOUNT = 1000000
```

### Advanced Example
```python
# Dynamic typing example
value = 100  # int
print(type(value))  # <class 'int'>

value = "100"  # string
print(type(value))  # <class 'str'>

# Type hints (Python 3.5+) - for better code documentation
def calculate_interest(principal: float, rate: float) -> float:
    """Calculate compound interest."""
    return principal * (1 + rate)

# None - represents absence of value
result = None
if result is None:
    print("No result yet")
```

### Common Mistakes

❌ **Not understanding mutable vs immutable types**
```python
# Strings are immutable
s = "hello"
s[0] = "H"  # ERROR! Can't modify string directly

# But lists are mutable
list1 = [1, 2, 3]
list1[0] = 10  # OK - modifies the list
```

✅ **Understand the difference**
```python
# Immutable: int, float, str, tuple
# Mutable: list, dict, set
```

### Best Practices

✅ **Use descriptive variable names**
```python
# BAD
a = 150000
i = 0.045

# GOOD
account_balance = 150000
annual_interest_rate = 0.045
```

✅ **Use type hints for clarity**
```python
def process_transaction(amount: float, customer_id: int) -> bool:
    pass
```

✅ **Follow naming conventions**
- Variables: `lowercase_with_underscores`
- Constants: `UPPERCASE_WITH_UNDERSCORES`
- Classes: `PascalCase`

---

## Lists

### Definition
Ordered, mutable collection of items. Allows duplicates and mixed types.

### Syntax
```python
my_list = [item1, item2, item3]
```

### Basic Example
```python
# Create a list
customers = ["John", "Jane", "Bob"]
balances = [100000, 250000, 75000]
mixed = [100, "text", 3.14, True]
empty_list = []

# Access by index (0-based)
first = customers[0]  # "John"
last = customers[-1]  # "Bob" (negative index from end)

# Slice
first_two = customers[0:2]  # ["John", "Jane"]
```

### Intermediate Example
```python
# Modify lists
accounts = ["checking", "savings", "money market"]

# Add item
accounts.append("investment")  # ["checking", "savings", "money market", "investment"]

# Insert at position
accounts.insert(1, "credit card")  # Insert at index 1

# Remove
accounts.remove("credit card")  # Remove specific item
removed = accounts.pop()  # Remove and return last item
removed = accounts.pop(0)  # Remove and return first item

# Extend (add multiple items)
accounts.extend(["brokerage", "retirement"])

# Sort
balances = [100, 50, 200, 75]
balances.sort()  # [50, 75, 100, 200]
balances_reversed = sorted(balances, reverse=True)  # [200, 100, 75, 50]
```

### Advanced Example
```python
# List of dictionaries - common in data processing
customers = [
    {"id": 1, "name": "John", "balance": 100000},
    {"id": 2, "name": "Jane", "balance": 250000},
    {"id": 3, "name": "Bob", "balance": 75000}
]

# List comprehension (covered in detail below)
names = [c["name"] for c in customers]
# ["John", "Jane", "Bob"]

# Filter with comprehension
high_balance = [c for c in customers if c["balance"] > 100000]
# [{"id": 2, "name": "Jane", "balance": 250000}]

# Transform with comprehension
balances_doubled = [c["balance"] * 2 for c in customers]
# [200000, 500000, 150000]
```

### Common Mistakes

❌ **Modifying list while iterating**
```python
accounts = ["checking", "savings", "investment"]
for account in accounts:
    if account == "investment":
        accounts.remove(account)  # DANGEROUS!
```

✅ **Create a copy to iterate**
```python
for account in accounts.copy():
    if account == "investment":
        accounts.remove(account)  # Safe
```

❌ **List reference vs copy confusion**
```python
list1 = [1, 2, 3]
list2 = list1  # Both reference same object!
list2.append(4)
print(list1)  # [1, 2, 3, 4] - list1 changed!
```

✅ **Explicitly copy when needed**
```python
list2 = list1.copy()  # Shallow copy
list2 = list(list1)   # Also shallow copy
list2.append(4)
print(list1)  # [1, 2, 3] - unchanged
```

### Best Practices

✅ **Use list methods efficiently**
```python
# Prefer built-in methods over manual loops
if "value" in my_list:  # Fast
    pass

index = my_list.index("value")  # Get index
count = my_list.count("value")   # Count occurrences
```

✅ **Use slicing for subsequences**
```python
my_list = [1, 2, 3, 4, 5]
first_three = my_list[:3]      # [1, 2, 3]
last_two = my_list[-2:]        # [4, 5]
every_second = my_list[::2]    # [1, 3, 5]
```

### Performance Considerations

⚡ List operations:
- `append()`: O(1) amortized
- `insert(0, x)`: O(n) - slow for large lists
- `pop()`: O(1) from end, O(n) from beginning
- Search: O(n) - use set for faster lookups

### Business Use Case

💼 **Customer Portfolio Analysis**:
```python
# List of customer transactions
transactions = [
    {"customer_id": 1, "amount": 50000, "date": "2024-01-15", "type": "deposit"},
    {"customer_id": 1, "amount": 10000, "date": "2024-01-20", "type": "withdrawal"},
    {"customer_id": 2, "amount": 100000, "date": "2024-01-18", "type": "deposit"},
]

# Find total deposits
total_deposits = sum([t["amount"] for t in transactions if t["type"] == "deposit"])

# Find all transactions for customer 1
cust1_txns = [t for t in transactions if t["customer_id"] == 1]
```

---

## Tuples

### Definition
Ordered, immutable collection. Cannot be modified after creation. Faster than lists.

### Syntax
```python
my_tuple = (item1, item2, item3)
```

### Basic Example
```python
# Create tuples
coordinates = (10.5, 20.3)
rgb_color = (255, 128, 0)
date_tuple = (2024, 1, 15)

# Single item tuple (note the comma!)
single = (42,)  # This is a tuple
single = (42)   # This is just an int!

# Access like lists
first = coordinates[0]  # 10.5
last = coordinates[-1]  # 20.3
```

### Intermediate Example
```python
# Tuple unpacking
x, y = (10, 20)
print(x, y)  # 10 20

# Useful for returning multiple values
def get_account_info():
    return ("savings", 150000, 0.025)  # account_type, balance, interest_rate

account_type, balance, rate = get_account_info()

# Tuples as dictionary keys (lists can't be keys)
portfolio_returns = {
    ("AAPL", "2024-Q1"): 0.15,
    ("MSFT", "2024-Q1"): 0.12,
}
```

### Advanced Example
```python
# Named tuples - more readable
from collections import namedtuple

# Define a named tuple
Account = namedtuple("Account", ["id", "balance", "interest_rate"])

# Create instances
acc1 = Account(id=1, balance=100000, interest_rate=0.045)
acc2 = Account(1, 100000, 0.045)  # Also works with positional args

# Access by name (more readable than index)
print(acc1.balance)  # 100000
print(acc1[1])       # 100000 - also works

# Convert to tuple
data = tuple(acc1)  # (1, 100000, 0.045)

# Create from list
acc_list = [2, 250000, 0.035]
acc3 = Account(*acc_list)  # Unpacking
```

### Common Mistakes

❌ **Trying to modify tuples**
```python
coords = (10, 20)
coords[0] = 15  # ERROR! 'tuple' object does not support item assignment
```

✅ **Create new tuple if needed**
```python
coords = (10, 20)
coords = (15, coords[1])  # Create new tuple
```

### Best Practices

✅ **Use tuples for immutable collections**
```python
# Better than list when data shouldn't change
RGB_BLACK = (0, 0, 0)
RGB_WHITE = (255, 255, 255)
```

✅ **Use named tuples for clarity**
```python
# Better than positional indexing
Transaction = namedtuple("Transaction", ["amount", "date", "status"])
txn = Transaction(5000, "2024-01-15", "complete")
print(txn.amount)  # Clear what data is
```

### Performance Considerations

⚡ Tuples are faster and use less memory than lists
⚡ Use tuples when you need immutability (e.g., dictionary keys)

---

## Dictionaries

### Definition
Unordered (Python 3.7+: insertion-ordered), mutable collection of key-value pairs.

### Syntax
```python
my_dict = {"key1": value1, "key2": value2}
```

### Basic Example
```python
# Create dictionaries
customer = {
    "id": 1,
    "name": "John Smith",
    "balance": 150000,
    "is_active": True
}

# Access values
name = customer["name"]  # "John Smith"
balance = customer.get("balance")  # 150000

# Safe access with default
account_type = customer.get("account_type", "checking")  # Returns "checking" if not found

# Check if key exists
if "id" in customer:
    print("Customer ID:", customer["id"])
```

### Intermediate Example
```python
# Modify dictionaries
employee = {"name": "Jane", "department": "Analytics"}

# Add key-value
employee["salary"] = 120000
employee["title"] = "Senior Analyst"

# Update multiple
employee.update({"salary": 130000, "level": 4})

# Remove
del employee["level"]
removed_value = employee.pop("title")  # Remove and return value

# Get all keys, values, items
keys = employee.keys()      # dict_keys(['name', 'department', ...])
values = employee.values()  # dict_values(['Jane', 'Analytics', ...])
items = employee.items()    # dict_items([('name', 'Jane'), ...])

# Iterate
for key, value in employee.items():
    print(f"{key}: {value}")
```

### Advanced Example
```python
# Nested dictionaries - common in data processing
portfolio = {
    "customer_1": {
        "name": "John",
        "holdings": {
            "AAPL": {"shares": 100, "price": 150.00},
            "MSFT": {"shares": 50, "price": 300.00}
        }
    },
    "customer_2": {
        "name": "Jane",
        "holdings": {
            "GOOGL": {"shares": 75, "price": 140.00}
        }
    }
}

# Access nested values
john_aapl_shares = portfolio["customer_1"]["holdings"]["AAPL"]["shares"]
john_aapl_value = portfolio["customer_1"]["holdings"]["AAPL"]["shares"] * \
                  portfolio["customer_1"]["holdings"]["AAPL"]["price"]

# Dictionary comprehension
stock_values = {
    stock: data["shares"] * data["price"]
    for stock, data in portfolio["customer_1"]["holdings"].items()
}
# {"AAPL": 15000.0, "MSFT": 15000.0}

# Flatten nested structure
all_holdings = {
    f"{cust_id}_{stock}": data["shares"] * data["price"]
    for cust_id, customer_data in portfolio.items()
    for stock, data in customer_data["holdings"].items()
}
```

### Common Mistakes

❌ **Assuming dictionary order before Python 3.7**
```python
# In Python < 3.7, order was not guaranteed
# In Python 3.7+, insertion order is guaranteed
```

❌ **KeyError on missing keys**
```python
data = {"a": 1, "b": 2}
value = data["c"]  # KeyError: 'c'
```

✅ **Use .get() for safe access**
```python
value = data.get("c")  # None (safe)
value = data.get("c", 0)  # 0 (default value)
```

### Best Practices

✅ **Use dictionaries for structured data**
```python
# Good - clear structure
customer = {"id": 1, "name": "John", "balance": 100000}

# Not ideal - vague
data = [1, "John", 100000]
```

✅ **Use .get() with defaults**
```python
risk_level = customer.get("risk_level", "medium")  # Safe
```

### Performance Considerations

⚡ Dictionary lookup: O(1) average
⚡ Dictionary keys must be hashable (immutable)
⚡ Use dict for fast lookups vs list

### Business Use Case

💼 **Customer Risk Profile Storage**:
```python
customers = {
    1: {"name": "John", "risk_score": 0.45, "credit_score": 720},
    2: {"name": "Jane", "risk_score": 0.25, "credit_score": 800},
    3: {"name": "Bob", "risk_score": 0.75, "credit_score": 600}
}

# Find high-risk customers
high_risk = {cid: data for cid, data in customers.items() 
             if data["risk_score"] > 0.5}
```

---

## Sets

### Definition
Unordered, mutable collection of unique items. No duplicates.

### Syntax
```python
my_set = {item1, item2, item3}
```

### Basic Example
```python
# Create sets
countries = {"USA", "Canada", "Mexico", "USA"}  # {"USA", "Canada", "Mexico"}
product_ids = {100, 200, 300}
empty_set = set()  # Must use set() for empty, not {}

# Add items
countries.add("Brazil")

# Remove items
countries.discard("USA")  # No error if not found
countries.remove("Canada")  # Error if not found

# Check membership
if "USA" in countries:
    print("USA is in the set")
```

### Intermediate Example
```python
# Set operations (union, intersection, difference)
active_customers = {1, 2, 3, 4, 5}
traded_customers = {3, 4, 5, 6, 7}

# Union (all unique items from both)
all_customers = active_customers | traded_customers  # {1, 2, 3, 4, 5, 6, 7}
all_customers = active_customers.union(traded_customers)

# Intersection (items in both)
both = active_customers & traded_customers  # {3, 4, 5}
both = active_customers.intersection(traded_customers)

# Difference (in first but not second)
only_active = active_customers - traded_customers  # {1, 2}
only_active = active_customers.difference(traded_customers)

# Symmetric difference (in either but not both)
different = active_customers ^ traded_customers  # {1, 2, 6, 7}
```

### Advanced Example
```python
# Remove duplicates from list
transactions = [100, 200, 150, 100, 200, 300, 150]
unique_amounts = list(set(transactions))  # [200, 100, 300, 150]

# Find common elements
list1 = [1, 2, 3, 4, 5]
list2 = [3, 4, 5, 6, 7]
common = list(set(list1) & set(list2))  # [3, 4, 5]

# Set comprehension
squares = {x**2 for x in range(1, 6)}  # {1, 4, 9, 16, 25}
```

### Common Mistakes

❌ **Using {} for empty set**
```python
empty = {}  # This is an empty DICT, not a set!
type(empty)  # <class 'dict'>
```

✅ **Use set() for empty set**
```python
empty = set()  # Correct empty set
```

### Best Practices

✅ **Use sets for fast membership testing**
```python
# Bad - O(n)
if item in my_list:
    pass

# Good - O(1)
if item in my_set:
    pass
```

✅ **Use sets for deduplication**
```python
unique_ids = set(customer_list)
```

### Performance Considerations

⚡ Set membership test: O(1)
⚡ List membership test: O(n)
⚡ Use sets when you need fast lookups

### Business Use Case

💼 **Duplicate Detection**:
```python
# Find duplicate transaction IDs
all_txn_ids = [1001, 1002, 1001, 1003, 1002, 1004]
unique_txns = set(all_txn_ids)
duplicate_count = len(all_txn_ids) - len(unique_txns)  # 2
```

---

## Loops

### Definition
Repeats code block for each item or while condition is true.

### For Loops

#### Basic Example
```python
# Iterate over list
balances = [100, 200, 300, 400, 500]
for balance in balances:
    interest = balance * 0.05
    print(f"Balance: {balance}, Interest: {interest}")

# Iterate with index
for i, balance in enumerate(balances):
    print(f"Index {i}: {balance}")
```

#### Intermediate Example
```python
# Iterate over dictionary
customer = {"id": 1, "name": "John", "balance": 100000}

for key, value in customer.items():
    print(f"{key}: {value}")

# Iterate with range
for i in range(1, 6):  # 1 to 5
    print(i)

# Iterate over multiple lists together
names = ["John", "Jane", "Bob"]
ages = [30, 28, 35]
for name, age in zip(names, ages):
    print(f"{name} is {age} years old")
```

#### Advanced Example
```python
# Nested loops
matrix = [
    [1, 2, 3],
    [4, 5, 6],
    [7, 8, 9]
]

# Iterate through 2D structure
for row in matrix:
    for value in row:
        print(value, end=" ")
    print()  # New line

# List comprehension (more Pythonic)
squared = [x**2 for x in range(1, 11)]
# [1, 4, 9, 16, 25, 36, 49, 64, 81, 100]

# Nested comprehension
transposed = [[row[i] for row in matrix] for i in range(3)]
```

### While Loops

#### Basic Example
```python
# While loop
balance = 10000
month = 0
monthly_rate = 0.004  # 0.4% monthly

while balance < 20000:
    balance = balance * (1 + monthly_rate)
    month += 1

print(f"Reached target in {month} months")

# While with break
while True:
    user_input = input("Enter 'quit' to exit: ")
    if user_input.lower() == "quit":
        break
    print(f"You entered: {user_input}")
```

#### Advanced Example
```python
# While with continue
count = 0
while count < 10:
    count += 1
    if count % 2 == 0:
        continue  # Skip even numbers
    print(count)  # Prints 1, 3, 5, 7, 9

# While with else (executes if loop completes normally)
balance = 0
while balance < 1000:
    balance += 100
else:
    print("Balance goal reached")  # Executed
```

### Common Mistakes

❌ **Infinite loops**
```python
while True:
    # Forgot to update condition
    print("Loop forever")
```

✅ **Always update loop variable**
```python
while balance < 1000:
    balance += 100  # Update condition variable
```

### Best Practices

✅ **Prefer for loops over while when possible**
```python
# Good
for item in items:
    process(item)

# Avoid unless necessary
i = 0
while i < len(items):
    process(items[i])
    i += 1
```

✅ **Use list comprehensions for transformations**
```python
# Good - Pythonic
balances_with_interest = [b * 1.05 for b in balances]

# Avoid - verbose
result = []
for b in balances:
    result.append(b * 1.05)
balances_with_interest = result
```

### Performance Considerations

⚡ List comprehension is faster than loops
⚡ Avoid modifying collections during iteration
⚡ Use `enumerate()` instead of range(len())

---

## Functions

### Definition
Reusable block of code that performs a specific task.

### Syntax
```python
def function_name(parameters):
    """Docstring explaining the function."""
    # Function body
    return result
```

### Basic Example
```python
# Simple function
def greet(name):
    """Greet a person by name."""
    return f"Hello, {name}!"

print(greet("John"))  # Hello, John!

# Function with multiple parameters
def calculate_interest(principal, rate, years):
    """Calculate compound interest."""
    return principal * (1 + rate) ** years

final_amount = calculate_interest(10000, 0.05, 3)
print(final_amount)  # 11576.25
```

### Intermediate Example
```python
# Default parameters
def process_transaction(amount, fee_rate=0.01, currency="USD"):
    """Process a transaction with optional fee."""
    fee = amount * fee_rate
    total = amount + fee
    return {"amount": amount, "fee": fee, "total": total, "currency": currency}

print(process_transaction(1000))  # Fee 1% (default)
print(process_transaction(1000, 0.02))  # Fee 2%
print(process_transaction(1000, currency="EUR"))  # Different currency

# Variable arguments
def sum_all(*args):
    """Sum any number of arguments."""
    return sum(args)

print(sum_all(1, 2, 3, 4, 5))  # 15

# Keyword arguments
def create_account(**kwargs):
    """Create account with any number of properties."""
    return kwargs

print(create_account(name="John", balance=100000, rate=0.05))
# {'name': 'John', 'balance': 100000, 'rate': 0.05}
```

### Advanced Example
```python
# Decorators - functions that modify other functions
def timer(func):
    """Decorator to measure execution time."""
    import time
    def wrapper(*args, **kwargs):
        start = time.time()
        result = func(*args, **kwargs)
        end = time.time()
        print(f"Function {func.__name__} took {end - start:.4f} seconds")
        return result
    return wrapper

@timer
def slow_calculation():
    """A function that takes time."""
    return sum(range(1000000))

slow_calculation()  # Prints execution time

# Type hints and annotations
from typing import List, Dict, Optional

def analyze_customer(customer_id: int, transactions: List[Dict]) -> Optional[Dict]:
    """
    Analyze customer transactions.
    
    Args:
        customer_id: The customer's ID
        transactions: List of transaction dictionaries
        
    Returns:
        Dictionary with analysis results or None if no transactions
    """
    if not transactions:
        return None
    
    total = sum(t["amount"] for t in transactions)
    return {"customer_id": customer_id, "total_amount": total}
```

### Common Mistakes

❌ **Mutable default arguments**
```python
def add_customer(name, data=[]):
    data.append(name)  # DANGEROUS!
    return data

print(add_customer("John"))   # ['John']
print(add_customer("Jane"))   # ['John', 'Jane'] - shared list!
```

✅ **Use None as default for mutable arguments**
```python
def add_customer(name, data=None):
    if data is None:
        data = []
    data.append(name)
    return data
```

### Best Practices

✅ **Write clear docstrings**
```python
def calculate_loan_payment(principal: float, rate: float, years: int) -> float:
    """
    Calculate monthly loan payment using the formula:
    M = P * [r(1+r)^n] / [(1+r)^n - 1]
    
    Args:
        principal: Loan amount in dollars
        rate: Annual interest rate as decimal (e.g., 0.05 for 5%)
        years: Loan term in years
        
    Returns:
        Monthly payment amount
        
    Raises:
        ValueError: If principal or years <= 0, or rate < 0
    """
    if principal <= 0 or years <= 0 or rate < 0:
        raise ValueError("Invalid input parameters")
    
    monthly_rate = rate / 12
    num_payments = years * 12
    payment = principal * (monthly_rate * (1 + monthly_rate)**num_payments) / \
              ((1 + monthly_rate)**num_payments - 1)
    return payment
```

✅ **Use type hints**
✅ **Keep functions focused on one task**
✅ **Return early to reduce nesting**

### Performance Considerations

⚡ Function calls have overhead - avoid in tight loops
⚡ Use built-in functions (they're implemented in C)
⚡ Cache expensive function results

---

## Lambda Functions

### Definition
Anonymous functions defined with `lambda`. Used for small, simple operations.

### Syntax
```python
lambda arguments: expression
```

### Basic Example
```python
# Simple lambda
square = lambda x: x ** 2
print(square(5))  # 25

# Lambda with multiple arguments
add = lambda x, y: x + y
print(add(10, 20))  # 30

# Use in map
balances = [100, 200, 300, 400]
balances_with_interest = list(map(lambda b: b * 1.05, balances))
print(balances_with_interest)  # [105.0, 210.0, 315.0, 420.0]
```

### Intermediate Example
```python
# Lambda with filter
transactions = [100, 50, 200, 75, 300, 25]
large_txns = list(filter(lambda t: t > 100, transactions))
print(large_txns)  # [200, 300]

# Lambda with sorted
customers = [
    {"name": "John", "balance": 100000},
    {"name": "Jane", "balance": 250000},
    {"name": "Bob", "balance": 75000}
]

# Sort by balance
sorted_customers = sorted(customers, key=lambda c: c["balance"], reverse=True)
print(sorted_customers[0]["name"])  # Jane
```

### Advanced Example
```python
# Lambda with reduce
from functools import reduce

values = [1, 2, 3, 4, 5]
total = reduce(lambda x, y: x + y, values)
print(total)  # 15

# Multiple operations
results = list(map(
    lambda x: {"value": x, "squared": x**2, "cubed": x**3},
    range(1, 4)
))
print(results)
# [{'value': 1, 'squared': 1, 'cubed': 1},
#  {'value': 2, 'squared': 4, 'cubed': 8},
#  {'value': 3, 'squared': 9, 'cubed': 27}]
```

### Common Mistakes

❌ **Using lambda for complex logic**
```python
# Too complex for lambda
process = lambda x: x * 2 if x > 100 else x * 3 if x > 50 else x
```

✅ **Use regular functions for complex logic**
```python
def process(x):
    if x > 100:
        return x * 2
    elif x > 50:
        return x * 3
    else:
        return x
```

### Best Practices

✅ **Use lambda for simple, one-line operations**
✅ **Prefer list comprehensions over map/filter with lambda**
```python
# Good
result = [x * 2 for x in items if x > 100]

# Less Pythonic
result = list(filter(lambda x: x > 100, map(lambda x: x * 2, items)))
```

---

## List Comprehensions

### Definition
Concise syntax to create lists by applying an operation to each item.

### Syntax
```python
[expression for item in iterable if condition]
```

### Basic Example
```python
# Square numbers 1-10
squares = [x**2 for x in range(1, 11)]
print(squares)  # [1, 4, 9, 16, 25, 36, 49, 64, 81, 100]

# Double each balance
balances = [100, 200, 300]
doubled = [b * 2 for b in balances]
print(doubled)  # [200, 400, 600]

# Convert strings to integers
string_numbers = ["100", "200", "300"]
numbers = [int(x) for x in string_numbers]
print(numbers)  # [100, 200, 300]
```

### Intermediate Example
```python
# With condition (filter)
numbers = range(1, 11)
even_squares = [x**2 for x in numbers if x % 2 == 0]
print(even_squares)  # [4, 16, 36, 64, 100]

# Extract from dictionaries
customers = [
    {"id": 1, "name": "John", "balance": 100000},
    {"id": 2, "name": "Jane", "balance": 250000},
    {"id": 3, "name": "Bob", "balance": 75000}
]

# Get all names
names = [c["name"] for c in customers]
# ["John", "Jane", "Bob"]

# Get names of customers with balance > 100000
high_balance_names = [c["name"] for c in customers if c["balance"] > 100000]
# ["Jane"]
```

### Advanced Example
```python
# Nested comprehension
matrix = [[1, 2, 3], [4, 5, 6], [7, 8, 9]]

# Flatten matrix
flattened = [x for row in matrix for x in row]
print(flattened)  # [1, 2, 3, 4, 5, 6, 7, 8, 9]

# Conditional transformation
values = range(1, 6)
result = [x*2 if x % 2 == 0 else x*3 for x in values]
print(result)  # [3, 4, 9, 8, 15]

# Dictionary comprehension
square_dict = {x: x**2 for x in range(1, 6)}
print(square_dict)  # {1: 1, 2: 4, 3: 9, 4: 16, 5: 25}

# Set comprehension
square_set = {x**2 for x in range(1, 6)}
print(square_set)  # {1, 4, 9, 16, 25}
```

### Common Mistakes

❌ **Too complex logic in comprehension**
```python
# Hard to read
result = [
    x**2 if x % 2 == 0 else x**3 if x % 3 == 0 else x 
    for x in range(1, 100) 
    if x > 10 and x < 50 and x not in [25, 30, 35]
]
```

✅ **Use functions for complex logic**
```python
def transform(x):
    if x % 2 == 0:
        return x ** 2
    elif x % 3 == 0:
        return x ** 3
    else:
        return x

result = [transform(x) for x in range(1, 100) 
          if 10 < x < 50 and x not in [25, 30, 35]]
```

### Best Practices

✅ **Keep comprehensions simple and readable**
✅ **Use parentheses for readability**
```python
result = [
    x * 2
    for x in numbers
    if x > 100
]
```

✅ **Dictionary and set comprehensions work similarly**
```python
dict_result = {key: value for key, value in pairs if condition}
set_result = {x for x in items if condition}
```

### Performance Considerations

⚡ List comprehension is faster than loops
⚡ More memory efficient than map/filter
⚡ Pythonic and widely understood

---

## Summary Table: Core Python

| Concept | Type | Mutable | Use Case | Performance |
|---------|------|---------|----------|-------------|
| int, float, str | Scalar | No | Individual values | N/A |
| list | Collection | Yes | Ordered, duplicates OK | Append O(1), Insert O(n) |
| tuple | Collection | No | Immutable collections | Faster than list |
| dict | Mapping | Yes | Key-value pairs | Lookup O(1) |
| set | Collection | Yes | Unique items | Lookup O(1) |
| loop (for) | Control | - | Iterate collections | Fast |
| loop (while) | Control | - | Conditional iteration | Depends |
| function | Callable | - | Reusable code | Overhead |
| lambda | Callable | - | Simple operations | Overhead |
| comprehension | Syntax | - | Transform collections | Fastest |

---

## Interview Questions

### Basic (1-10)
1. What's the difference between lists and tuples?
2. How do you create an empty dictionary?
3. What does `enumerate()` do in a for loop?
4. Explain the difference between mutable and immutable objects.
5. How would you reverse a list?
6. What's the purpose of the `zip()` function?
7. Can you use a list as a dictionary key? Why or why not?
8. What does `range()` return?
9. How do you check if a value exists in a list?
10. Explain the difference between `.append()` and `.extend()`.

### Intermediate (11-30)
11. What's a list comprehension and what are its advantages?
12. How would you remove duplicates from a list while preserving order?
13. Explain the difference between `remove()`, `pop()`, and `del`.
14. What does the `*args` and `**kwargs` syntax do?
15. How would you flatten a nested list?
16. What's the difference between shallow and deep copy?
17. How do you iterate over a dictionary?
18. What's a lambda function and when would you use it?
19. Explain the difference between `sorted()` and `.sort()`.
20. How would you swap two variables without using a temporary variable?

### Advanced (21-50)
21. What's a generator and how does it differ from a list?
22. Explain list comprehension with nested loops.
23. How would you create a dictionary from two lists?
24. What are named tuples and when would you use them?
25. Explain function decorators and provide an example.
26. What's the difference between `globals()` and `locals()`?
27. How would you create a function that returns multiple values?
28. What's the `__name__` variable used for?
29. Explain variable scoping in Python (local, global, nonlocal).
30. How would you handle optional parameters in a function?
31. What's a closure and how is it useful?
32. Explain the difference between identity (is) and equality (==).
33. How would you create a multiline string?
34. What's string formatting and what are the different methods?
35. How would you convert between different data types?
36. Explain the purpose of the `with` statement.
37. What's the difference between `is` and `==` when comparing to None?
38. How would you create a recursive function?
39. What's memoization and how would you implement it?
40. Explain how Python handles memory and garbage collection.

### Advanced Questions (41-50)
41. What's the Global Interpreter Lock (GIL) and how does it affect multithreading?
42. How would you create a class method vs instance method vs static method?
43. Explain metaclasses and their use cases.
44. What's the difference between `__str__` and `__repr__`?
45. How would you implement operator overloading in a class?
46. Explain the MRO (Method Resolution Order) in inheritance.
47. What's a property decorator and when would you use it?
48. How would you create a singleton class?
49. Explain context managers and the context manager protocol.
50. What's the difference between `__init__` and `__new__`?

---

## Practice Exercises

### Exercise 1: Data Transformation
You have a list of customer dictionaries with balances. Calculate the total balance, average balance, and find customers above average.

```python
customers = [
    {"id": 1, "name": "John", "balance": 100000},
    {"id": 2, "name": "Jane", "balance": 250000},
    {"id": 3, "name": "Bob", "balance": 75000},
    {"id": 4, "name": "Alice", "balance": 200000}
]

# TODO: Calculate total, average, and above-average customers
```

### Exercise 2: List Comprehension
Convert the above to a single-line solution using list comprehension.

### Exercise 3: Function Design
Write a function that:
- Takes a list of transactions
- Filters for transactions over a threshold
- Applies a fee
- Returns a dictionary with summary statistics

### Exercise 4: Nested Data
Work with nested customer data including accounts and transactions. Extract all transactions over $10,000.

### Exercise 5: Dictionary Operations
Given customer data, create a dictionary keyed by customer ID with values being their total transaction amount.

---

## Common Mistakes & Best Practices

### Mistake 1: Not Understanding Variable Assignment
```python
# Lists are references
list1 = [1, 2, 3]
list2 = list1
list2.append(4)
print(list1)  # [1, 2, 3, 4] - Both changed!
```

**Fix**: Create explicit copies
```python
list2 = list1.copy()  # Shallow copy
import copy
list2 = copy.deepcopy(list1)  # Deep copy for nested structures
```

### Mistake 2: Index Out of Range
```python
items = [1, 2, 3]
print(items[5])  # IndexError!
```

**Fix**: Check length or use safe access
```python
if len(items) > 5:
    print(items[5])
    
print(items[5:6])  # Returns [] if out of range (safe)
```

### Mistake 3: Mutable Default Arguments
```python
def add_item(item, list=[]):
    list.append(item)
    return list

print(add_item(1))  # [1]
print(add_item(2))  # [1, 2] - Shared!
```

**Fix**: Use None
```python
def add_item(item, list=None):
    if list is None:
        list = []
    list.append(item)
    return list
```
