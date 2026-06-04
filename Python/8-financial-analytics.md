# Python Reference Guide: 8. Financial Analytics

> **Optimize for**: Data Analytics, Risk Analytics, Finance, Banking, Data Science

---

## Portfolio Returns

### Example
```python
import numpy as np
import pandas as pd

# Portfolio data
assets = ['AAPL', 'MSFT', 'GOOGL', 'AMZN']
weights = np.array([0.25, 0.30, 0.25, 0.20])  # Portfolio weights
returns = np.array([0.12, 0.15, 0.10, 0.18])  # Individual returns

# Portfolio return
portfolio_return = np.sum(weights * returns)
print(f"Portfolio Return: {portfolio_return:.4f}")

# Covariance matrix
returns_data = np.array([
    [0.10, 0.12, 0.08],  # AAPL daily returns
    [0.11, 0.14, 0.09],  # MSFT daily returns
    [0.09, 0.10, 0.07]   # GOOGL daily returns
])
cov_matrix = np.cov(returns_data)
print(f"Covariance Matrix:\n{cov_matrix}")

# Portfolio volatility
portfolio_variance = np.dot(weights[:3], np.dot(cov_matrix, weights[:3]))
portfolio_std = np.sqrt(portfolio_variance)
print(f"Portfolio Volatility: {portfolio_std:.4f}")
```

---

## Risk Metrics

### Value at Risk (VaR)

```python
# Historical VaR
returns = np.random.normal(0.0005, 0.02, 1000)
var_95 = np.percentile(returns, 5)  # 5th percentile
var_99 = np.percentile(returns, 1)  # 1st percentile

print(f"95% VaR: {var_95:.4f}")
print(f"99% VaR: {var_99:.4f}")
print(f"Interpretation: 5% chance of losing more than {abs(var_95):.2%} in a day")

# Parametric VaR (assuming normal distribution)
mean_return = np.mean(returns)
std_return = np.std(returns)
var_95_parametric = mean_return - 1.645 * std_return  # 95% confidence
print(f"95% Parametric VaR: {var_95_parametric:.4f}")
```

### Expected Shortfall (CVaR)

```python
# Expected Shortfall = Average of returns worse than VaR
var_threshold = np.percentile(returns, 5)
expected_shortfall = np.mean(returns[returns < var_threshold])
print(f"Expected Shortfall (95%): {expected_shortfall:.4f}")
```

### Sharpe Ratio

```python
# Sharpe Ratio = (Return - Risk-Free Rate) / Volatility
annual_return = np.mean(returns) * 252
annual_volatility = np.std(returns) * np.sqrt(252)
risk_free_rate = 0.02  # 2% annual

sharpe_ratio = (annual_return - risk_free_rate) / annual_volatility
print(f"Sharpe Ratio: {sharpe_ratio:.4f}")
print(f"Interpretation: {sharpe_ratio:.2f} units of excess return per unit of risk")
```

### Sortino Ratio

```python
# Sortino Ratio = (Return - Risk-Free Rate) / Downside Volatility
downside_returns = returns[returns < 0]
downside_volatility = np.std(downside_returns) * np.sqrt(252)

sortino_ratio = (annual_return - risk_free_rate) / downside_volatility
print(f"Sortino Ratio: {sortino_ratio:.4f}")
```

### Maximum Drawdown

```python
# Maximum Drawdown = Largest peak-to-trough decline
prices = 100 * np.cumprod(1 + returns)
running_max = np.maximum.accumulate(prices)
drawdown = (prices - running_max) / running_max
max_drawdown = np.min(drawdown)

print(f"Maximum Drawdown: {max_drawdown:.2%}")
```

---

## Scenario Analysis

### Example
```python
# Base case
base_return = 0.08
base_risk = 0.15

# Scenarios
scenarios = {
    'Bull Market': {'return': 0.15, 'probability': 0.25},
    'Base Case': {'return': 0.08, 'probability': 0.50},
    'Bear Market': {'return': -0.10, 'probability': 0.25}
}

# Expected return
expected_return = sum(s['return'] * s['probability'] for s in scenarios.values())
print(f"Expected Return: {expected_return:.4f}")

# Standard deviation
variance = sum(s['probability'] * (s['return'] - expected_return)**2 
               for s in scenarios.values())
std_dev = np.sqrt(variance)
print(f"Standard Deviation: {std_dev:.4f}")

# Risk metrics by scenario
for scenario, data in scenarios.items():
    prob = data['probability']
    ret = data['return']
    print(f"{scenario}: {ret:.2%} (probability: {prob:.0%})")
```

### Stress Testing

```python
# Portfolio under stress scenarios
portfolio_value = 1000000
stress_factors = {
    'Interest Rate +1%': -0.02,
    'Market Crash -10%': -0.10,
    'Volatility Spike': -0.05,
    'Credit Spread Widening': -0.03
}

print(f"Base Portfolio Value: ${portfolio_value:,.0f}")
for scenario, factor in stress_factors.items():
    stressed_value = portfolio_value * (1 + factor)
    loss = portfolio_value - stressed_value
    print(f"{scenario}: ${stressed_value:,.0f} (Loss: ${loss:,.0f})")
```

---

## Credit Risk

### Probability of Default (PD) Model

```python
from sklearn.linear_model import LogisticRegression

# Features: credit score, income, debt ratio, age
X = np.array([
    [750, 100000, 0.30, 35],
    [650, 60000, 0.50, 28],
    [700, 80000, 0.40, 42],
    [600, 50000, 0.60, 25]
])

# Target: default (1) or no default (0)
y = np.array([0, 1, 0, 1])

# Train PD model
pd_model = LogisticRegression(random_state=42)
pd_model.fit(X, y)

# Predict PD for new customer
new_customer = np.array([[720, 90000, 0.35, 38]])
pd = pd_model.predict_proba(new_customer)[0, 1]
print(f"Probability of Default: {pd:.2%}")
```

### Expected Loss

```python
# Expected Loss = PD * LGD * EAD
# PD: Probability of Default
# LGD: Loss Given Default (typically 40-50% for unsecured)
# EAD: Exposure at Default

PD = 0.05  # 5% probability of default
LGD = 0.45  # 45% loss if default
EAD = 100000  # $100K exposure

expected_loss = PD * LGD * EAD
print(f"Expected Loss: ${expected_loss:,.0f}")
print(f"Expected Loss %: {expected_loss/EAD:.2%}")
```

