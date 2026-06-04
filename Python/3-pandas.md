# Python Reference Guide: 3. Pandas

> **Optimize for**: Data Analytics, Risk Analytics, Finance, Banking, Data Science

---

## DataFrames

### Definition
Pandas DataFrames are 2D labeled data structures with rows and columns, similar to Excel or SQL tables.

### Basic Example
```python
import pandas as pd
import numpy as np

# Create DataFrame from dictionary
data = {
    "customer_id": [1, 2, 3, 4, 5],
    "name": ["John", "Jane", "Bob", "Alice", "Charlie"],
    "balance": [100000, 250000, 75000, 200000, 150000],
    "risk_score": [0.45, 0.25, 0.75, 0.35, 0.55]
}

df = pd.DataFrame(data)
print(df)

# Create from list of dictionaries
data_list = [
    {"id": 1, "name": "John", "amount": 100},
    {"id": 2, "name": "Jane", "amount": 200}
]
df = pd.DataFrame(data_list)

# Create from numpy array
data_array = np.random.randn(3, 4)
df = pd.DataFrame(data_array, columns=["A", "B", "C", "D"])
```

### Intermediate Example
```python
# DataFrame properties and indexing
df = pd.DataFrame(data)

print(df.shape)      # (5, 4) - rows, columns
print(df.columns)    # Index of column names
print(df.dtypes)     # Data type of each column
print(df.index)      # Row index

# Access columns
names = df["name"]   # Series
names = df[["name", "balance"]]  # DataFrame (double brackets)

# Access rows
first_row = df.iloc[0]  # By position
customer_1 = df[df["customer_id"] == 1]  # By condition

# Basic statistics
print(df.describe())  # Summary statistics
print(df["balance"].mean())
print(df["balance"].std())
```

### Advanced Example
```python
# Multi-level operations
df["balance_tier"] = pd.cut(df["balance"], 
                             bins=[0, 100000, 200000, np.inf],
                             labels=["Low", "Medium", "High"])

# Apply functions
df["balance_in_thousands"] = df["balance"].apply(lambda x: x / 1000)

# Multiple conditions
high_balance_low_risk = df[(df["balance"] > 150000) & (df["risk_score"] < 0.5)]

# Value counts
print(df["balance_tier"].value_counts())
```

---

## Reading Files

### Example
```python
import pandas as pd

# Read CSV
df = pd.read_csv("transactions.csv")

# Read Excel
df = pd.read_excel("data.xlsx", sheet_name="Sheet1")

# Read SQL
import sqlalchemy
engine = sqlalchemy.create_engine("sqlite:///database.db")
df = pd.read_sql("SELECT * FROM transactions", engine)

# Read from URL
df = pd.read_csv("https://example.com/data.csv")

# Read with options
df = pd.read_csv("data.csv", 
                  sep=",",
                  header=0,
                  dtype={"id": int, "amount": float},
                  parse_dates=["date"],
                  na_values=["NA", "N/A", ""])
```

---

## Cleaning Data

### Missing Values

```python
# Detect missing values
print(df.isnull())      # Boolean mask
print(df.isnull().sum())  # Count per column
print(df.dropna())      # Drop rows with any NaN
print(df.dropna(how="all"))  # Drop all-NaN rows

# Fill missing values
df["age"].fillna(df["age"].mean(), inplace=True)  # Fill with mean
df["category"].fillna("Unknown", inplace=True)  # Fill with value
df.fillna(method="ffill", inplace=True)  # Forward fill
df.fillna(method="bfill", inplace=True)  # Backward fill
```

### Duplicates

```python
# Detect duplicates
print(df.duplicated())  # Boolean mask
print(df.duplicated(subset=["customer_id"]))  # Specific columns

# Remove duplicates
df_unique = df.drop_duplicates()  # All columns
df_unique = df.drop_duplicates(subset=["customer_id"], keep="first")
```

### Data Types

```python
# Convert data types
df["customer_id"] = df["customer_id"].astype(int)
df["date"] = pd.to_datetime(df["date"])
df["category"] = df["category"].astype("category")
```

---

## GroupBy

### Basic Example
```python
# Group and aggregate
group_summary = df.groupby("balance_tier")["balance"].agg(["count", "mean", "sum"])
print(group_summary)

# Multiple grouping
by_tier_risk = df.groupby(["balance_tier", "risk_score"]).size()
```

### Intermediate Example
```python
# Custom aggregation
agg_dict = {
    "balance": ["sum", "mean", "std"],
    "risk_score": ["mean", "max"],
    "customer_id": "count"
}
result = df.groupby("balance_tier").agg(agg_dict)

# Apply custom function
df.groupby("balance_tier")["balance"].transform(lambda x: (x - x.mean()) / x.std())
```

---

## Merge & Join

### Example
```python
# Two DataFrames
df1 = pd.DataFrame({"id": [1, 2, 3], "name": ["John", "Jane", "Bob"]})
df2 = pd.DataFrame({"id": [1, 2, 3], "balance": [100, 200, 300]})

# Inner join (only matching rows)
merged_inner = pd.merge(df1, df2, on="id", how="inner")

# Left join (all from left)
merged_left = pd.merge(df1, df2, on="id", how="left")

# Outer join (all from both)
merged_outer = pd.merge(df1, df2, on="id", how="outer")

# Concatenate vertically
df_combined = pd.concat([df1, df2], axis=0)  # Stack rows

# Concatenate horizontally
df_combined = pd.concat([df1, df2], axis=1)  # Add columns
```

---

## Pivot Tables

### Example
```python
# Create pivot table
pivot = pd.pivot_table(df, 
                       values="balance",
                       index="balance_tier",
                       columns="risk_score",
                       aggfunc="mean")
print(pivot)

# Multiple aggregations
pivot = pd.pivot_table(df,
                       values=["balance", "customer_id"],
                       index="balance_tier",
                       aggfunc={"balance": "sum", "customer_id": "count"})
```

---

## Reshaping

### Example
```python
# Stack (long format)
stacked = df.set_index(["customer_id", "name"]).stack()

# Unstack (wide format)
unstacked = stacked.unstack()

# Melt (unpivot)
melted = pd.melt(df, id_vars=["customer_id"], 
                 value_vars=["balance", "risk_score"],
                 var_name="metric", value_name="value")
```

---

## Outliers

### Example
```python
# IQR method
Q1 = df["balance"].quantile(0.25)
Q3 = df["balance"].quantile(0.75)
IQR = Q3 - Q1
lower_bound = Q1 - 1.5 * IQR
upper_bound = Q3 + 1.5 * IQR

outliers = df[(df["balance"] < lower_bound) | (df["balance"] > upper_bound)]
df_clean = df[(df["balance"] >= lower_bound) & (df["balance"] <= upper_bound)]

# Z-score method
from scipy import stats
z_scores = np.abs(stats.zscore(df["balance"]))
df_clean = df[z_scores < 3]  # Keep values within 3 standard deviations
```

---

## Feature Engineering

### Example
```python
# Create new features
df["balance_log"] = np.log(df["balance"])
df["balance_squared"] = df["balance"] ** 2
df["is_high_risk"] = (df["risk_score"] > 0.5).astype(int)

# Binning
df["balance_bin"] = pd.cut(df["balance"], bins=5, labels=["Very Low", "Low", "Medium", "High", "Very High"])

# Encoding categorical variables
df["risk_encoded"] = pd.factorize(df["risk_score"])[0]
df = pd.get_dummies(df, columns=["risk_score"], prefix="risk")

# Time-based features
df["date"] = pd.to_datetime(df["date"])
df["year"] = df["date"].dt.year
df["month"] = df["date"].dt.month
df["day_of_week"] = df["date"].dt.dayofweek
```

