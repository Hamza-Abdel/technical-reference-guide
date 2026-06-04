# Python Reference Guide: 5. Visualization

> **Optimize for**: Data Analytics, Risk Analytics, Finance, Banking, Data Science

---

## Matplotlib

### Basic Example
```python
import matplotlib.pyplot as plt
import numpy as np

# Simple line plot
x = np.linspace(0, 10, 100)
y = np.sin(x)

plt.figure(figsize=(10, 6))
plt.plot(x, y, label="sin(x)", color="blue", linewidth=2)
plt.xlabel("X Axis")
plt.ylabel("Y Axis")
plt.title("Sine Wave")
plt.legend()
plt.grid(True, alpha=0.3)
plt.show()

# Subplot
fig, axes = plt.subplots(2, 2, figsize=(12, 10))
axes[0, 0].plot(x, np.sin(x))
axes[0, 1].plot(x, np.cos(x))
axes[1, 0].scatter(x, np.sin(x))
axes[1, 1].bar(range(10), np.random.rand(10))
plt.tight_layout()
plt.show()
```

### Intermediate Example
```python
# Histogram
data = np.random.normal(100, 15, 1000)
plt.hist(data, bins=30, color="skyblue", edgecolor="black", alpha=0.7)
plt.xlabel("Value")
plt.ylabel("Frequency")
plt.title("Distribution of Returns")
plt.show()

# Box plot
data1 = np.random.normal(100, 15, 100)
data2 = np.random.normal(110, 20, 100)
plt.boxplot([data1, data2], labels=["Group 1", "Group 2"])
plt.ylabel("Values")
plt.show()
```

### Financial Example
```python
# Cumulative returns
returns = np.array([0.02, 0.015, 0.03, -0.01, 0.025, 0.01, -0.005])
cum_returns = np.cumprod(1 + returns) - 1
days = np.arange(len(cum_returns))

plt.figure(figsize=(12, 6))
plt.plot(days, cum_returns, marker="o", linewidth=2, label="Cumulative Return")
plt.fill_between(days, cum_returns, alpha=0.3)
plt.xlabel("Days")
plt.ylabel("Cumulative Return")
plt.title("Portfolio Performance")
plt.grid(True, alpha=0.3)
plt.legend()
plt.show()
```

---

## Seaborn

### Basic Example
```python
import seaborn as sns
import pandas as pd

df = pd.DataFrame({
    "returns": np.random.normal(0.01, 0.02, 100),
    "risk": np.random.uniform(0.01, 0.1, 100)
})

# Scatter plot with regression line
sns.regplot(x="risk", y="returns", data=df)
plt.title("Return vs Risk")
plt.show()

# Distribution plot
sns.histplot(df["returns"], kde=True)
plt.show()
```

### Advanced Example
```python
# Heatmap for correlation matrix
corr_matrix = df.corr()
sns.heatmap(corr_matrix, annot=True, cmap="coolwarm", center=0)
plt.show()

# Pairplot
sns.pairplot(df)
plt.show()
```

---

## Plotly (Interactive)

### Example
```python
import plotly.graph_objects as go
import plotly.express as px

# Interactive line plot
fig = go.Figure()
fig.add_trace(go.Scatter(x=days, y=cum_returns, mode="lines+markers", name="Returns"))
fig.update_layout(title="Interactive Portfolio Performance",
                 xaxis_title="Days",
                 yaxis_title="Cumulative Return")
fig.show()

# Interactive scatter plot
fig = px.scatter(df, x="risk", y="returns", title="Risk vs Return",
                trendline="ols")
fig.show()
```

