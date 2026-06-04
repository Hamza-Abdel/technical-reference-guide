# Python Reference Guide: 9. Production Code

> **Optimize for**: Data Analytics, Risk Analytics, Finance, Banking, Data Science

---

## Logging

### Basic Example
```python
import logging

# Configure logging
logging.basicConfig(
    level=logging.DEBUG,
    format='%(asctime)s - %(name)s - %(levelname)s - %(message)s',
    handlers=[
        logging.FileHandler('app.log'),
        logging.StreamHandler()
    ]
)

logger = logging.getLogger(__name__)

# Log messages
logger.debug("Debug message")
logger.info("Processing transaction")
logger.warning("High risk transaction detected")
logger.error("Failed to process payment")
logger.critical("System failure")
```

### Advanced Example
```python
import logging.config
import json

# Configuration from file
with open('logging_config.json') as f:
    config = json.load(f)
    logging.config.dictConfig(config)

logger = logging.getLogger(__name__)

# Context logging
def process_customer(customer_id):
    extra = {'customer_id': customer_id}
    logger.info(f"Processing customer", extra=extra)
    try:
        # Processing logic
        logger.info(f"Customer processed successfully", extra=extra)
    except Exception as e:
        logger.error(f"Error processing customer: {e}", extra=extra)
```

---

## Error Handling

### Example
```python
# Try-Except-Finally
try:
    result = 10 / 0
except ZeroDivisionError as e:
    print(f"Error: {e}")
except Exception as e:
    print(f"Unexpected error: {e}")
finally:
    print("Cleanup code")

# Custom exceptions
class InsufficientFundsError(Exception):
    def __init__(self, required, available):
        self.required = required
        self.available = available
        super().__init__(f"Required: {required}, Available: {available}")

def withdraw(amount, balance):
    if amount > balance:
        raise InsufficientFundsError(amount, balance)
    return balance - amount

# Using custom exception
try:
    new_balance = withdraw(1000, 500)
except InsufficientFundsError as e:
    print(f"Transaction failed: {e}")
```

---

## Configuration Files

### YAML Configuration

```python
import yaml

# Load configuration
with open('config.yaml') as f:
    config = yaml.safe_load(f)

db_config = config['database']
model_config = config['models']

# config.yaml
# database:
#   host: localhost
#   port: 5432
#   name: analytics
# 
# models:
#   risk:
#     algorithm: logistic_regression
#     threshold: 0.5
```

### Environment Variables

```python
import os
from dotenv import load_dotenv

# Load from .env file
load_dotenv()

# Access environment variables
db_host = os.getenv('DB_HOST', 'localhost')
db_port = os.getenv('DB_PORT', 5432)
api_key = os.getenv('API_KEY')

if not api_key:
    raise ValueError("API_KEY not set")
```

---

## Virtual Environments

### Setup

```bash
# Create virtual environment
python -m venv venv

# Activate (Linux/Mac)
source venv/bin/activate

# Activate (Windows)
venv\Scripts\activate

# Install packages
pip install pandas numpy scikit-learn

# Generate requirements
pip freeze > requirements.txt

# Install from requirements
pip install -r requirements.txt

# Deactivate
deactivate
```

### requirements.txt Example
```
pandas==2.0.0
numpy==1.24.0
scikit-learn==1.3.0
scipy==1.11.0
matplotlib==3.7.0
seaborn==0.12.0
plotly==5.15.0
statsmodels==0.14.0
pytest==7.4.0
python-dotenv==1.0.0
pyyaml==6.0
```

---

## Project Structure

```
project/
├── data/
│   ├── raw/
│   ├── processed/
│   └── output/
├── src/
│   ├── __init__.py
│   ├── data_loader.py
│   ├── preprocessing.py
│   ├── models.py
│   └── utils.py
├── tests/
│   ├── test_data_loader.py
│   ├── test_preprocessing.py
│   └── test_models.py
├── notebooks/
│   ├── 01_exploration.ipynb
│   └── 02_modeling.ipynb
├── config/
│   ├── config.yaml
│   └── logging_config.json
├── requirements.txt
├── README.md
└── main.py
```

---

## Unit Testing

### Example

```python
import unittest
import numpy as np

class TestCalculations(unittest.TestCase):
    def test_portfolio_return(self):
        weights = np.array([0.5, 0.5])
        returns = np.array([0.10, 0.08])
        expected = 0.09
        result = np.dot(weights, returns)
        self.assertAlmostEqual(result, expected, places=5)
    
    def test_portfolio_return_raises(self):
        weights = np.array([0.5, 0.6])  # Doesn't sum to 1
        with self.assertRaises(ValueError):
            if not np.isclose(weights.sum(), 1.0):
                raise ValueError("Weights must sum to 1")

if __name__ == '__main__':
    unittest.main()
```

---

## Performance Profiling

### Example

```python
import cProfile
import pstats
from io import StringIO

def slow_calculation():
    total = 0
    for i in range(1000000):
        total += i
    return total

# Profile code
profiler = cProfile.Profile()
profiler.enable()

result = slow_calculation()

profiler.disable()
stats = pstats.Stats(profiler, stream=StringIO())
stats.strip_dirs()
stats.sort_stats('cumulative')
stats.print_stats()

# Or use timeit
import timeit
time = timeit.timeit(slow_calculation, number=1)
print(f"Time: {time:.4f} seconds")
```

