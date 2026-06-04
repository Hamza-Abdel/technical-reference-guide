# Python Reference Guide: 6. Machine Learning

> **Optimize for**: Data Analytics, Risk Analytics, Finance, Banking, Data Science

---

## Train/Test Split

### Example
```python
from sklearn.model_selection import train_test_split
import numpy as np

# Create sample data
X = np.random.randn(1000, 5)  # 1000 samples, 5 features
y = np.random.randint(0, 2, 1000)  # Binary target

# Split into train/test (80/20)
X_train, X_test, y_train, y_test = train_test_split(X, y, 
                                                      test_size=0.2, 
                                                      random_state=42,
                                                      stratify=y)  # Preserve class distribution

print(f"Train size: {X_train.shape[0]}, Test size: {X_test.shape[0]}")

# Cross-validation
from sklearn.model_selection import cross_val_score
from sklearn.linear_model import LogisticRegression

model = LogisticRegression()
scores = cross_val_score(model, X, y, cv=5)  # 5-fold cross-validation
print(f"CV Scores: {scores}")
print(f"Mean CV Score: {scores.mean():.4f} (+/- {scores.std():.4f})")
```

---

## Regression

### Linear Regression

```python
from sklearn.linear_model import LinearRegression
from sklearn.metrics import mean_squared_error, r2_score
import numpy as np

# Sample data: predicting portfolio returns from risk factors
X = np.array([[0.05], [0.06], [0.04], [0.07], [0.05]])  # Risk factors
y = np.array([0.08, 0.10, 0.07, 0.12, 0.09])  # Returns

# Train model
model = LinearRegression()
model.fit(X, y)

# Predictions
y_pred = model.predict(X)

# Evaluation
mse = mean_squared_error(y, y_pred)
rmse = np.sqrt(mse)
r2 = r2_score(y, y_pred)

print(f"Coefficient: {model.coef_[0]:.4f}")
print(f"Intercept: {model.intercept_:.4f}")
print(f"RMSE: {rmse:.4f}")
print(f"R² Score: {r2:.4f}")

# Make predictions on new data
new_risk = np.array([[0.055]])
predicted_return = model.predict(new_risk)
print(f"Predicted return for risk 0.055: {predicted_return[0]:.4f}")
```

### Ridge & Lasso Regression

```python
from sklearn.linear_model import Ridge, Lasso

# Ridge (L2 regularization) - penalizes large coefficients
ridge_model = Ridge(alpha=1.0)
ridge_model.fit(X, y)

# Lasso (L1 regularization) - can shrink coefficients to zero
lasso_model = Lasso(alpha=0.1)
lasso_model.fit(X, y)

print(f"Ridge coefficients: {ridge_model.coef_}")
print(f"Lasso coefficients: {lasso_model.coef_}")
```

---

## Classification

### Logistic Regression

```python
from sklearn.linear_model import LogisticRegression
from sklearn.metrics import classification_report, confusion_matrix, roc_auc_score
import pandas as pd

# Sample: predicting loan default
X = np.random.randn(1000, 3)  # Features: credit score, income, debt ratio
y = np.random.randint(0, 2, 1000)  # Target: default (0) or no default (1)

# Train model
model = LogisticRegression(random_state=42)
model.fit(X, y)

# Predictions
y_pred = model.predict(X)
y_pred_proba = model.predict_proba(X)  # Probabilities

# Evaluation
print(classification_report(y, y_pred))
print(confusion_matrix(y, y_pred))
print(f"ROC-AUC Score: {roc_auc_score(y, y_pred_proba[:, 1]):.4f}")
```

### Decision Tree

```python
from sklearn.tree import DecisionTreeClassifier

dt_model = DecisionTreeClassifier(max_depth=3, random_state=42)
dt_model.fit(X, y)
y_pred = dt_model.predict(X)

print(f"Feature importances: {dt_model.feature_importances_}")
print(f"Accuracy: {dt_model.score(X, y):.4f}")
```

### Random Forest

```python
from sklearn.ensemble import RandomForestClassifier

rf_model = RandomForestClassifier(n_estimators=100, max_depth=5, random_state=42)
rf_model.fit(X, y)
y_pred = rf_model.predict(X)

print(f"Feature importances: {rf_model.feature_importances_}")
print(f"Accuracy: {rf_model.score(X, y):.4f}")
```

### Gradient Boosting

```python
from sklearn.ensemble import GradientBoostingClassifier

gb_model = GradientBoostingClassifier(n_estimators=100, learning_rate=0.1, random_state=42)
gb_model.fit(X, y)
y_pred = gb_model.predict(X)

print(f"Feature importances: {gb_model.feature_importances_}")
print(f"Accuracy: {gb_model.score(X, y):.4f}")
```

### Support Vector Machine

```python
from sklearn.svm import SVC

svm_model = SVC(kernel="rbf", C=1.0, random_state=42)
svm_model.fit(X, y)
y_pred = svm_model.predict(X)

print(f"Accuracy: {svm_model.score(X, y):.4f}")
```

---

## Clustering

### K-Means

```python
from sklearn.cluster import KMeans
from sklearn.preprocessing import StandardScaler

# Sample: segmenting customers
X = np.random.randn(1000, 2) * 100 + 100  # Random customer features

# Standardize features
scaler = StandardScaler()
X_scaled = scaler.fit_transform(X)

# Apply K-Means
kmeans = KMeans(n_clusters=3, random_state=42, n_init=10)
cluster_labels = kmeans.fit_predict(X_scaled)

print(f"Cluster centers: {kmeans.cluster_centers_}")
print(f"Cluster sizes: {np.bincount(cluster_labels)}")
```

### Hierarchical Clustering

```python
from sklearn.cluster import AgglomerativeClustering

ward_model = AgglomerativeClustering(n_clusters=3, linkage="ward")
cluster_labels = ward_model.fit_predict(X_scaled)

print(f"Cluster sizes: {np.bincount(cluster_labels)}")
```

### DBSCAN

```python
from sklearn.cluster import DBSCAN

dbscan = DBSCAN(eps=0.5, min_samples=5)
cluster_labels = dbscan.fit_predict(X_scaled)

print(f"Number of clusters: {len(set(cluster_labels)) - (1 if -1 in cluster_labels else 0)}")
print(f"Number of noise points: {sum(cluster_labels == -1)}")
```

---

## Model Evaluation

### Metrics

```python
from sklearn.metrics import (
    accuracy_score, precision_score, recall_score, f1_score,
    confusion_matrix, roc_curve, auc, roc_auc_score
)
import matplotlib.pyplot as plt

# Classification metrics
accuracy = accuracy_score(y_true, y_pred)
precision = precision_score(y_true, y_pred)
recall = recall_score(y_true, y_pred)
f1 = f1_score(y_true, y_pred)

print(f"Accuracy: {accuracy:.4f}")
print(f"Precision: {precision:.4f}")
print(f"Recall: {recall:.4f}")
print(f"F1 Score: {f1:.4f}")

# ROC Curve
fpr, tpr, thresholds = roc_curve(y_true, y_pred_proba)
roc_auc = auc(fpr, tpr)

plt.plot(fpr, tpr, label=f"AUC = {roc_auc:.3f}")
plt.xlabel("False Positive Rate")
plt.ylabel("True Positive Rate")
plt.title("ROC Curve")
plt.legend()
plt.show()
```

---

## Pipeline

### Example

```python
from sklearn.pipeline import Pipeline
from sklearn.preprocessing import StandardScaler
from sklearn.linear_model import LogisticRegression

# Create pipeline
pipeline = Pipeline([
    ('scaler', StandardScaler()),
    ('model', LogisticRegression(random_state=42))
])

# Train pipeline
pipeline.fit(X_train, y_train)

# Predictions
y_pred = pipeline.predict(X_test)
accuracy = pipeline.score(X_test, y_test)
print(f"Accuracy: {accuracy:.4f}")
```

---

## Hyperparameter Tuning

### Grid Search

```python
from sklearn.model_selection import GridSearchCV
from sklearn.ensemble import RandomForestClassifier

# Define parameter grid
param_grid = {
    'n_estimators': [50, 100, 200],
    'max_depth': [3, 5, 10],
    'min_samples_split': [2, 5, 10]
}

# Grid search
grid_search = GridSearchCV(RandomForestClassifier(random_state=42),
                          param_grid, cv=5, n_jobs=-1)
grid_search.fit(X_train, y_train)

print(f"Best parameters: {grid_search.best_params_}")
print(f"Best CV score: {grid_search.best_score_:.4f}")

# Use best model
best_model = grid_search.best_estimator_
y_pred = best_model.predict(X_test)
```

### Random Search

```python
from sklearn.model_selection import RandomizedSearchCV
from scipy.stats import randint

param_dist = {
    'n_estimators': randint(50, 200),
    'max_depth': randint(1, 20)
}

random_search = RandomizedSearchCV(RandomForestClassifier(random_state=42),
                                  param_dist, n_iter=10, cv=5, random_state=42)
random_search.fit(X_train, y_train)
```

