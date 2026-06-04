# Python Reference Guide: 7. Time Series

> **Optimize for**: Data Analytics, Risk Analytics, Finance, Banking, Data Science

---

## Resampling

### Example
```python
import pandas as pd
import numpy as np
from datetime import datetime, timedelta

# Create daily time series
dates = pd.date_range('2024-01-01', periods=252, freq='D')
prices = 100 + np.cumsum(np.random.randn(252) * 2)
df = pd.DataFrame({'price': prices}, index=dates)

# Resample to different frequencies
weekly = df.resample('W').last()  # Last price of each week
monthly = df.resample('M').mean()  # Average price per month
quarterly = df.resample('Q').agg({'price': ['open', 'high', 'low', 'close']})

print(f"Daily data points: {len(df)}")
print(f"Weekly data points: {len(weekly)}")
print(f"Monthly data points: {len(monthly)}")
```

---

## Rolling Windows

### Example
```python
# Simple moving average
df['SMA_20'] = df['price'].rolling(window=20).mean()
df['SMA_50'] = df['price'].rolling(window=50).mean()

# Exponential moving average
df['EMA_20'] = df['price'].ewm(span=20, adjust=False).mean()

# Rolling standard deviation
df['volatility'] = df['price'].rolling(window=20).std()

# Rolling correlation
df['returns'] = df['price'].pct_change()
df['returns_corr'] = df['returns'].rolling(window=20).corr(df['returns'].shift(1))

# Rolling maximum drawdown
running_max = df['price'].expanding().max()
df['max_drawdown'] = (df['price'] - running_max) / running_max
```

---

## Lag Features

### Example
```python
# Create lagged features
df['price_lag1'] = df['price'].shift(1)  # Previous day price
df['price_lag5'] = df['price'].shift(5)  # Price 5 days ago

# Return lags
df['returns'] = df['price'].pct_change()
df['returns_lag1'] = df['returns'].shift(1)
df['returns_lag2'] = df['returns'].shift(2)

# Differencing (for stationarity)
df['price_diff'] = df['price'].diff()  # First difference
df['price_diff2'] = df['price'].diff().diff()  # Second difference

# Remove NaN from lag features
df = df.dropna()
```

---

## Forecasting

### ARIMA

```python
from statsmodels.tsa.arima.model import ARIMA
from statsmodels.graphics.tsaplots import plot_acf, plot_pacf

# Check stationarity
from statsmodels.tsa.stattools import adfuller
result = adfuller(df['price'])
print(f"ADF statistic: {result[0]:.4f}")
print(f"p-value: {result[1]:.4f}")
if result[1] > 0.05:
    print("Data is non-stationary, need differencing")

# ARIMA model
arima_model = ARIMA(df['price'], order=(1, 1, 1))
arima_fit = arima_model.fit()

# Forecast
forecast = arima_fit.get_forecast(steps=10)
forecast_df = forecast.conf_int()
print(forecast_df)
```

### Exponential Smoothing

```python
from statsmodels.tsa.holtwinters import ExponentialSmoothing

# Simple exponential smoothing
ses_model = ExponentialSmoothing(df['price'], trend='add', seasonal=None)
ses_fit = ses_model.fit()
forecast = ses_fit.get_forecast(steps=10)
print(forecast.predicted_mean)

# Holt-Winters (with trend and seasonality)
hw_model = ExponentialSmoothing(df['price'], trend='add', seasonal='add', seasonal_periods=12)
hw_fit = hw_model.fit()
```

### Prophet

```python
from prophet import Prophet

# Prepare data for Prophet
df_prophet = df[['price']].reset_index()
df_prophet.columns = ['ds', 'y']

# Create and fit model
model = Prophet()
model.fit(df_prophet)

# Make forecast
future = model.make_future_dataframe(periods=30)
forecast = model.predict(future)
print(forecast[['ds', 'yhat', 'yhat_lower', 'yhat_upper']].tail())

# Plot
model.plot(forecast)
```

