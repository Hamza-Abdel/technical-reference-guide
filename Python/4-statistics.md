# Python Reference Guide: 4. Statistics

> **Optimize for**: Data Analytics, Risk Analytics, Finance, Banking, Data Science

---

## Hypothesis Testing

### T-Test

```python
from scipy import stats
import numpy as np

# One-sample t-test
# H0: Mean = 100
data = np.array([98, 102, 100, 99, 101, 103, 97, 102])
t_stat, p_value = stats.ttest_1samp(data, 100)
print(f"t-statistic: {t_stat:.4f}")
print(f"p-value: {p_value:.4f}")
if p_value < 0.05:
    print("Reject H0: Mean is significantly different from 100")
else:
    print("Fail to reject H0")

# Two-sample t-test
group1 = np.array([10, 12, 11, 13, 10])
group2 = np.array([15, 16, 14, 17, 15])
t_stat, p_value = stats.ttest_ind(group1, group2)
print(f"p-value: {p_value:.4f}")

# Paired t-test
before = np.array([100, 102, 98, 101, 99])
after = np.array([105, 107, 102, 106, 104])
t_stat, p_value = stats.ttest_rel(before, after)
```

### ANOVA

```python
# One-way ANOVA
group1 = np.array([10, 12, 11, 13, 10])
group2 = np.array([15, 16, 14, 17, 15])
group3 = np.array([20, 22, 21, 23, 20])

f_stat, p_value = stats.f_oneway(group1, group2, group3)
print(f"F-statistic: {f_stat:.4f}")
print(f"p-value: {p_value:.4f}")
if p_value < 0.05:
    print("Significant difference between groups")
```

---

## Correlation

### Example
```python
import pandas as pd
from scipy import stats

df = pd.DataFrame({
    "returns": [0.02, 0.015, 0.03, -0.01, 0.025],
    "risk": [0.05, 0.04, 0.06, 0.03, 0.055]
})

# Pearson correlation
corr, p_value = stats.pearsonr(df["returns"], df["risk"])
print(f"Correlation: {corr:.4f}")
print(f"p-value: {p_value:.4f}")

# Spearman correlation (rank-based, for non-normal data)
corr, p_value = stats.spearmanr(df["returns"], df["risk"])

# Correlation matrix
corr_matrix = df.corr()
print(corr_matrix)
```

---

## Confidence Intervals

### Example
```python
from scipy import stats
import numpy as np

data = np.array([100, 102, 98, 101, 99, 103, 97, 102])
n = len(data)
mean = np.mean(data)
std_error = stats.sem(data)  # Standard error of mean

# 95% confidence interval
ci = stats.t.interval(0.95, n-1, loc=mean, scale=std_error)
print(f"95% CI: [{ci[0]:.2f}, {ci[1]:.2f}]")

# Bootstrap confidence interval
from scipy.stats import bootstrap

def statistic(x, axis):
    return np.mean(x, axis=axis)

res = bootstrap((data,), statistic, n_resamples=10000, confidence_level=0.95)
print(f"Bootstrap 95% CI: [{res.confidence_interval.low:.2f}, {res.confidence_interval.high:.2f}]")
```

