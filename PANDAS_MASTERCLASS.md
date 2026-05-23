# Pandas Masterclass

> *"In the world of data science, mastering Pandas is like learning to read before writing poetry."*

---

## Module 1: Foundations

---

### 1.1 Core Concepts

> **Lecture Core Insight:** Pandas is the foundational data manipulation library for Python — it sits between raw NumPy arrays and serious analytical workflows, translating low-level array math into tabular, spreadsheet-like structures that humans natively understand. Think of it as **Excel on steroids with a Python brain**: every cell, row, and column is backed by fast C-level NumPy arrays, but you interact with it through clean, high-level commands.

#### 1. Engine Rules & Mechanics

| Concept | Rule |
|---|---|
| **Pandas Origin** | Built by Wes McKinney in 2008 at AQR Capital (a financial firm) to solve the lack of fast, flexible data analysis tools in Python |
| **Engine** | Built on top of NumPy — every Series and DataFrame stores its data in contiguous NumPy `ndarray` blocks under the hood |
| **Execution Model** | Eager execution (not lazy) — operations run immediately and produce results in-memory |
| **Copy-on-Write (CoW)** | Pandas 3.0 enables CoW by default. No operation modifies the original unless you explicitly assign. Chained operations create intermediate copies. |
| **Memory Strategy** | Each column in a DataFrame is a separate NumPy array — mixed types are supported at the column level (not row level) |

#### 2. Live Lecture Code Blueprint

```python
import pandas as pd
import numpy as np

# Pandas sits on top of NumPy — every column is a NumPy array
arr = np.array([10, 20, 30, 40, 50])
s = pd.Series(arr, name="sensor_readings")

# A DataFrame is a dictionary of Series (columns)
ecommerce_log = pd.DataFrame({
    "order_id": [1001, 1002, 1003, 1004, 1005],
    "customer": ["Alice", "Bob", "Charlie", "Diana", "Eve"],
    "amount": [250.50, 99.99, 150.00, 320.75, 45.00],
    "quantity": [2, 1, 3, 4, 1]
})
```

---

### 1.2 Essential Objects — Series & DataFrame

> **Lecture Core Insight:** The **Series** is a single column of data with labels (like a NumPy array with an index), while the **DataFrame** is a collection of Series sharing the same index. If a Series is a single column, a DataFrame is the full spreadsheet tab.

#### 1. Engine Rules & Mechanics

| Object | Structure | Analogy |
|---|---|---|
| **Series** | 1D labeled array — `values` (NumPy array) + `index` (labels) | A single column in Excel |
| **DataFrame** | 2D labeled structure — collection of Series sharing one index | The full Excel sheet |
| **Index** | Immutable, hashable, supports duplication (but alignment breaks if dupes exist) | The row numbers on the left |
| **dtype** | Each column has its own dtype; a DataFrame is NOT a single matrix | Column-level typing |

#### 2. Live Lecture Code Blueprint

```python
import pandas as pd
import numpy as np

# --- Series: a single column ---
user_scores = pd.Series([88, 92, 79, 85, 95], index=["Alice", "Bob", "Charlie", "Diana", "Eve"])
print(user_scores)

# --- DataFrame: a collection of Series ---
order_log = pd.DataFrame({
    "product":    ["Laptop", "Mouse", "Keyboard", "Monitor", "Headset"],
    "price":      [1200.00, 25.50, 75.00, 350.00, 89.99],
    "units_sold": [50, 200, 150, 80, 300]
}, index=["ORD-001", "ORD-002", "ORD-003", "ORD-004", "ORD-005"])

# Access a single column — returns a Series
prices = order_log["price"]

# Access the index
print(order_log.index)

# Access column names
print(order_log.columns)

# Underlying NumPy arrays
print(type(order_log.values))
```

---

### 1.3 Ingestion Basics

> **Lecture Core Insight:** Data doesn't magically appear in DataFrames — you build them from raw ingredients: Python lists, dictionaries, NumPy arrays, or external files. Every data scientist spends 60% of their time getting data *into* shape before analyzing it.

#### 1. Engine Rules & Mechanics

| Source | Method | Notes |
|---|---|---|
| **List of lists** | `pd.DataFrame(data, columns=...)` | Each inner list = one row |
| **Dict of lists** | `pd.DataFrame(dict)` | Keys become column names; values must be equal-length |
| **List of dicts** | `pd.DataFrame(records)` | Each dict = one row; keys = columns |
| **NumPy array** | `pd.DataFrame(arr, columns=...)` | Shape must be 2D |
| **From CSV** | `pd.read_csv("path.csv")` | Most common external source |
| **From Excel** | `pd.read_excel("path.xlsx")` | Requires `openpyxl` or `xlrd` |

#### 2. Live Lecture Code Blueprint

```python
import pandas as pd
import numpy as np

# --- From a list of lists (rows) ---
sales_rows = [
    [101, "Laptop", "Electronics", 1200.00],
    [102, "T-Shirt", "Clothing", 25.00],
    [103, "Blender", "Home", 89.99]
]
df1 = pd.DataFrame(sales_rows, columns=["id", "item", "category", "price"])

# --- From a dict of lists (columns) ---
sales_cols = {
    "id": [101, 102, 103],
    "item": ["Laptop", "T-Shirt", "Blender"],
    "category": ["Electronics", "Clothing", "Home"],
    "price": [1200.00, 25.00, 89.99]
}
df2 = pd.DataFrame(sales_cols)

# --- From a list of dicts (records) ---
sales_records = [
    {"id": 101, "item": "Laptop", "category": "Electronics", "price": 1200.00},
    {"id": 102, "item": "T-Shirt", "category": "Clothing", "price": 25.00},
    {"id": 103, "item": "Blender", "category": "Home", "price": 89.99}
]
df3 = pd.DataFrame(sales_records)

# --- From a NumPy array ---
arr = np.array([[101, 1200], [102, 25], [103, 89.99]])
df4 = pd.DataFrame(arr, columns=["id", "price"])

print(df1.shape)
```
---

### 1.4 Inspection Tooling

> **Lecture Core Insight:** Before touching a dataset, you must inspect it — the "look before you leap" of data science. These inspection methods are your eyes into the structure, size, shape, and health of your data.

#### 1. Engine Rules & Mechanics

| Method | What It Shows | When to Use |
|---|---|---|
| `.head(n)` | First `n` rows (default 5) | Quick sanity check on column contents |
| `.tail(n)` | Last `n` rows | Check the end of the data (e.g., recent timestamps) |
| `.sample(n)` | Random `n` rows | Get a representative peek without positional bias |
| `.shape` | (row_count, column_count) | Know the size immediately |
| `.columns` | List of all column names | Verify column naming and order |
| `.index` | The row index labels | Check if index is default RangeIndex or custom |
| `.info()` | dtypes, non-null counts, memory usage | THE most important health-check call |
| `.describe()` | 5-number summary + mean/std for numeric columns | Immediate statistical feel for the data |

#### 2. Live Lecture Code Blueprint

```python
import pandas as pd
import numpy as np

rng = np.random.default_rng(42)
n = 1000

analytics = pd.DataFrame({
    "user_id": range(1001, 1001 + n),
    "signup_date": pd.date_range("2023-01-01", periods=n, freq="D"),
    "age": rng.integers(18, 70, n),
    "spend": rng.uniform(5.0, 500.0, n).round(2),
    "loyalty_tier": rng.choice(["Bronze", "Silver", "Gold", "Platinum"], n),
    "is_active": rng.choice([True, False], n, p=[0.7, 0.3])
})

# Quick peeks
print(analytics.head(3))
print(analytics.tail(2))
print(analytics.sample(2, random_state=42))

# Structural queries
print(f"Shape: {analytics.shape}")
print(f"Columns: {analytics.columns.tolist()}")
print(f"Index: {analytics.index}")

# Health check
analytics.info()

# Statistical summary
print(analytics.describe())
```

---

### 1.5 The Tabular Schema

> **Lecture Core Insight:** A DataFrame has two axes — axis=0 (rows, index) and axis=1 (columns). Most operations default to axis=0 (row-wise). Understanding axes is the key to knowing whether an operation runs "down" or "across."

#### 1. Engine Rules & Mechanics

| Term | Definition |
|---|---|
| **Row** | A single observation — accessed by index label or position |
| **Column** | A feature/variable — accessed by column name or position |
| **axis=0** | Row direction — operations applied index-wise (default) |
| **axis=1** | Column direction — operations applied column-wise |
| **Index** | Row labels — can be integers, strings, datetime, or MultiIndex |
| **Schema** | The column names + dtypes of a DataFrame |

#### 2. Live Lecture Code Blueprint

```python
import pandas as pd
import numpy as np

schema_demo = pd.DataFrame({
    "sensor_id": [1, 2, 3, 4, 5],
    "temperature": [22.5, 24.0, 21.8, 23.5, 25.1],
    "humidity": [55, 60, 52, 58, 62],
    "location": ["Warehouse-A", "Warehouse-B", "Warehouse-A", "Warehouse-C", "Warehouse-B"]
})

# axis=0 operations: eliminate rows with any nulls
print(schema_demo.dropna(axis=0))

# axis=1 operations: eliminate columns with any nulls
print(schema_demo.dropna(axis=1))

# Sum across columns (axis=1) — row-wise total
schema_demo["temperature"] + schema_demo["humidity"]

# Sum down rows (axis=0) — column-wise total
print(schema_demo[["temperature", "humidity"]].sum(axis=0))
```

---

### 1.6 The Pandas Type System

> **Lecture Core Insight:** Every column in a DataFrame has a dtype — and that dtype determines memory usage, operation speed, and what you can do with that column. Choosing the right dtype is your first performance optimization.

#### 1. Engine Rules & Mechanics

| dtype | Memory Profile | Nullable? | Notes |
|---|---|---|---|
| `int64` | 8 bytes per value | No (use `Int64` nullable) | Default integer type |
| `float64` | 8 bytes per value | Yes (NaN) | Default float type |
| `object` | Variable (pointer overhead) | Yes | Holds Python objects (strings, mixed types) — SLOW |
| `string` | Dense string storage | Yes | Faster than `object` for text — use `string` not `object` |
| `bool` | 1 byte per value | No | For binary flags |
| `category` | n * code + k * value | Yes | Great for low-cardinality strings — saves huge memory |
| `datetime64[ns]` | 8 bytes per value | Yes | Nanosecond-precision timestamps |
| `Int64` (nullable) | 8 bytes + mask | Yes | Pandas nullable integer |
| `Sparse` | Only non-zero values stored | Yes | For arrays with many zeros/NaNs |

#### 2. Live Lecture Code Blueprint

```python
import pandas as pd
import numpy as np

type_demo = pd.DataFrame({
    "count":        pd.array([10, 20, None, 40, 50], dtype="Int64"),
    "score":        [95.5, 88.0, 72.3, None, 91.2],
    "name":         ["Alice", "Bob", "Charlie", "Diana", "Eve"],
    "name_string":  pd.array(["Alice", "Bob", "Charlie", "Diana", "Eve"], dtype="string"),
    "is_active":    [True, False, True, True, False],
    "tier":         pd.Categorical(["Gold", "Silver", "Gold", "Platinum", "Bronze"]),
    "signup":       pd.to_datetime(["2023-01-15", "2023-02-20", "2023-03-10", "2023-04-05", "2023-05-01"])
})

print(type_demo.dtypes)
print(type_demo.memory_usage(deep=True))
```

---

## Module 2: Selection

---

### 2.1 Column Navigation

> **Lecture Core Insight:** Selecting columns is the most basic data operation — a single bracket `df["col"]` returns a Series, while double brackets `df[["col1", "col2"]]` returns a DataFrame. The latter is always safer for consistent code.

#### 1. Engine Rules & Mechanics

| Syntax | Return Type | Behavior |
|---|---|---|
| `df["column"]` | Series | Single column access |
| `df.column_name` | Series | Attribute access — only works if column name is a valid Python identifier |
| `df[["col1", "col2"]]` | DataFrame | Column subset — returns new DataFrame (CoW) |
| `df[["col"]]` | DataFrame (1-column) | Single column as DataFrame |
| `df.filter(like="sub")` | DataFrame | Select columns by substring match |

#### 2. Live Lecture Code Blueprint

```python
import pandas as pd

products = pd.DataFrame({
    "product_id": [101, 102, 103, 104],
    "product_name": ["Widget A", "Widget B", "Gadget X", "Gadget Y"],
    "unit_price": [9.99, 14.99, 24.99, 39.99],
    "stock_qty": [150, 80, 200, 45],
    "is_available": [True, True, False, True]
})

# Single column — returns Series
prices = products["unit_price"]

# Double brackets — returns DataFrame
subset = products[["product_name", "unit_price"]]

# Attribute access (risky — avoid if names conflict with methods)
names_attr = products.product_name

# Filter by substring
price_cols = products.filter(like="price")
```

---

### 2.2 Explicit Label Alignment — `.loc`

> **Lecture Core Insight:** `.loc` is label-based selection — you pick rows and columns by their names, not their positions. It is **inclusive** on both row and column labels, unlike Python slicing which is exclusive on the end.

#### 1. Engine Rules & Mechanics

| Expression | Action |
|---|---|
| `df.loc["row_label"]` | Select a single row by index label — returns Series |
| `df.loc["row_a":"row_f"]` | Slice rows by label — **inclusive** of both ends |
| `df.loc[["a", "c", "e"]]` | Select specific rows by label list |
| `df.loc["a", "col1"]` | Single cell by row label and column name |
| `df.loc["a":"d", "col1":"col3"]` | Sub-rectangle: rows a-d, columns col1-col3 |
| `df.loc[boolean_mask]` | Filter by boolean condition (most common pattern) |

#### 2. Live Lecture Code Blueprint

```python
import pandas as pd

sensor_data = pd.DataFrame({
    "timestamp": pd.date_range("2025-01-01", periods=5, freq="h"),
    "temp_c": [22.1, 23.4, 21.8, 24.0, 22.6],
    "pressure_psi": [30.1, 30.3, 29.8, 30.5, 30.0],
    "vibration": [0.12, 0.15, 0.09, 0.21, 0.11]
}, index=["S1", "S2", "S3", "S4", "S5"])

# Row by label
print(sensor_data.loc["S3"])

# Slice rows by label (inclusive)
print(sensor_data.loc["S2":"S4"])

# Specific rows and columns
print(sensor_data.loc[["S1", "S3", "S5"], ["temp_c", "vibration"]])

# By boolean mask
high_temp = sensor_data.loc[sensor_data["temp_c"] > 22.5]
```

---

### 2.3 Positional Navigation — `.iloc`

> **Lecture Core Insight:** `.iloc` is integer-position based — 0-indexed, and its slices follow Python-exclusive-end rules (unlike `.loc`). Use it when you care about position, not label.

#### 1. Engine Rules & Mechanics

| Expression | Action |
|---|---|
| `df.iloc[0]` | First row — returns Series |
| `df.iloc[2:5]` | Rows at positions 2, 3, 4 — **exclusive** of 5 |
| `df.iloc[[0, 2, 4]]` | Specific rows by position list |
| `df.iloc[0, 1]` | Single cell: row 0, column 1 |
| `df.iloc[:, 0:3]` | All rows, first 3 columns |
| `df.iloc[1:4, [0, 2]]` | Sub-rectangle with mixed slicing |

#### 2. Live Lecture Code Blueprint

```python
import pandas as pd

sales = pd.DataFrame({
    "date": pd.date_range("2025-06-01", periods=10, freq="D"),
    "revenue": [1200, 1500, 1100, 1800, 1600, 1400, 1900, 2100, 1700, 1550],
    "customers": [45, 52, 38, 63, 55, 48, 70, 78, 60, 51],
    "avg_order": [26.67, 28.85, 28.95, 28.57, 29.09, 29.17, 27.14, 26.92, 28.33, 30.39]
})

# First 3 rows, first 2 columns
print(sales.iloc[:3, :2])

# Every other row (step)
print(sales.iloc[::2])

# Last row
print(sales.iloc[-1])

# Specific cell
print(f"Revenue on day 5: {sales.iloc[4, 1]}")
```

---

### 2.4 High-Performance Scalar Extraction — `.at` / `.iat`

> **Lecture Core Insight:** When you need to grab or set a single value, `.at` (label-based) and `.iat` (position-based) are significantly faster than `.loc` or `.iloc` because they skip axis-alignment checks and return a scalar directly.

#### 1. Engine Rules & Mechanics

| Method | Based On | Speed | Use Case |
|---|---|---|---|
| `.at[row, col]` | Labels | Fastest for scalar | Single cell by name |
| `.iat[row, col]` | Positions | Fastest for scalar | Single cell by integer |
| `.loc[row, col]` | Labels | Slower (more overhead) | Subsets and slices |
| `.iloc[row, col]` | Positions | Slower (more overhead) | Subsets and slices |

#### 2. Live Lecture Code Blueprint

```python
import pandas as pd

inventory = pd.DataFrame({
    "warehouse": ["North", "South", "East", "West"],
    "widgets": [500, 320, 450, 200],
    "gadgets": [150, 280, 100, 310],
    "doodads": [800, 600, 750, 400]
}).set_index("warehouse")

# Fast get — label based
widgets_north = inventory.at["North", "widgets"]
print(f"Widgets in North warehouse: {widgets_north}")

# Fast get — position based (row 0, col 0)
widgets_north_iat = inventory.iat[0, 0]
print(f"Same value: {widgets_north_iat}")

# Fast set — avoids SettingWithCopyWarning
inventory.at["South", "gadgets"] = 300
inventory.iat[3, 2] = 420  # West, doodads
```

---

### 2.5 Boolean Masking Filters

> **Lecture Core Insight:** A boolean mask is a Series of True/False values that you pass into selection brackets. You combine conditions using `&` (and), `|` (or), and `~` (not) — each condition must be wrapped in parentheses because of Python operator precedence.

#### 1. Engine Rules & Mechanics

| Operator | Meaning | Example |
|---|---|---|
| `&` | Element-wise AND | `(df["age"] > 30) & (df["salary"] > 50000)` |
| `|` | Element-wise OR | `(df["dept"] == "Sales") | (df["dept"] == "Marketing")` |
| `~` | Element-wise NOT | `~(df["status"] == "inactive")` |
| `.isin()` | Membership check | `df["city"].isin(["NYC", "LA", "Chicago"])` |
| `.between()` | Inclusive range | `df["age"].between(25, 40)` |

**Must wrap each condition in `()` — always.**

#### 2. Live Lecture Code Blueprint

```python
import pandas as pd
import numpy as np

rng = np.random.default_rng(42)
employees = pd.DataFrame({
    "name": [f"Employee_{i}" for i in range(100)],
    "dept": rng.choice(["Engineering", "Sales", "Marketing", "HR", "Finance"], 100),
    "salary": rng.integers(40000, 150000, 100),
    "years_exp": rng.integers(1, 30, 100),
    "is_manager": rng.choice([True, False], 100, p=[0.2, 0.8])
})

# Single condition
eng_only = employees[employees["dept"] == "Engineering"]

# Compound condition — MUST use () around each
high_paid_eng = employees[
    (employees["dept"] == "Engineering") &
    (employees["salary"] > 90000)
]

# OR condition
sales_or_mktg = employees[
    (employees["dept"] == "Sales") |
    (employees["dept"] == "Marketing")
]

# NOT condition
non_managers = employees[~(employees["is_manager"])]

# Using .isin()
core_depts = employees[employees["dept"].isin(["Engineering", "Finance"])]

# Using .between()
mid_career = employees[employees["years_exp"].between(5, 15)]
```

---

### 2.6 Subset Mutation

> **Lecture Core Insight:** Modifying filtered rows requires explicit assignment. In Pandas 3.0 with CoW, chained assignments silently fail — you must modify the original DataFrame directly through `.loc` or `.iloc`.

#### 1. Engine Rules & Mechanics

| Pattern | Safe? | Notes |
|---|---|---|
| `df.loc[mask, "col"] = value` | Yes | Single assignment target — always works |
| `df["new_col"] = values` | Yes | Adding a new column |
| `df["col"] = np.where(mask, a, b)` | Yes | Conditional vectorized assignment |
| `df[df["x"] > 0]["y"] = 5` | No | Chained assignment — triggers CoW warning, no effect |
| `df["col"][mask] = value` | Risky | Chained — depends on whether CoW is enabled |

#### 2. Live Lecture Code Blueprint

```python
import pandas as pd
import numpy as np

orders = pd.DataFrame({
    "order_id": range(101, 111),
    "amount": [250.0, 400.0, 150.0, 600.0, 75.0, 320.0, 890.0, 45.0, 510.0, 200.0],
    "status": ["pending", "shipped", "pending", "delivered", "cancelled",
               "pending", "shipped", "cancelled", "pending", "delivered"]
})

# SAFE: flag high-value pending orders
high_value = (orders["amount"] > 300) & (orders["status"] == "pending")
orders.loc[high_value, "needs_review"] = True
orders.loc[~high_value, "needs_review"] = False

# SAFE: conditional update
orders.loc[orders["status"] == "cancelled", "amount"] = 0.0

# SAFE: vectorized conditional with np.where
orders["priority"] = np.where(
    (orders["amount"] > 500) & (orders["status"] != "cancelled"),
    "High", "Normal"
)
```

---

### 2.7 Index Optimization — `.set_index()` / `.reset_index()`

> **Lecture Core Insight:** The index is the row label — it's not just a row number. Setting a meaningful column as the index enables fast label-based lookups via `.loc`. `.reset_index()` turns the index back into a regular column.

#### 1. Engine Rules & Mechanics

| Method | Effect | Parameters |
|---|---|---|
| `df.set_index("col")` | Move column to index | `drop=True` (default, removes column), `append=False` |
| `df.reset_index()` | Move index to column | `drop=False` (default, keeps index as column) |
| `df.set_index(["col1", "col2"])` | MultiIndex from multiple columns | Creates MultiIndex |

#### 2. Live Lecture Code Blueprint

```python
import pandas as pd

transactions = pd.DataFrame({
    "txn_id": ["TXN-001", "TXN-002", "TXN-003", "TXN-004", "TXN-005"],
    "date": pd.date_range("2025-01-01", periods=5, freq="D"),
    "amount": [100.0, 250.0, 60.0, 400.0, 150.0],
    "merchant": ["Amazon", "Walmart", "Starbucks", "Best Buy", "Uber"]
})

# Set txn_id as the index
transactions = transactions.set_index("txn_id")
print(transactions)
# Fast lookup by transaction ID
print(transactions.loc["TXN-003"])

# Reset index back to default integer index
transactions = transactions.reset_index()
print(transactions)
```

---

### 2.8 Hierarchical Structures — MultiIndex

> **Lecture Core Insight:** A MultiIndex lets you have multiple levels of row labels (like grouped rows in Excel). It's the foundation for hierarchical data — think "year within department" or "store within region."

#### 1. Engine Rules & Mechanics

| Concept | Rule |
|---|---|
| **Creation** | `pd.MultiIndex.from_tuples()`, `pd.MultiIndex.from_product()`, or `df.set_index(["a", "b"])` |
| **Levels** | Each level is a distinct dimension of the index |
| **Slicing** | Use `.xs()` for cross-sectional access, or `.loc` with tuples |
| **Sorting** | Must be sorted for advanced slicing — use `.sort_index()` |
| **Unstack** | `.unstack()` moves an index level to column header |

#### 2. Live Lecture Code Blueprint

```python
import pandas as pd

sales_data = {
    ("North", "Jan"): 15000,
    ("North", "Feb"): 18000,
    ("North", "Mar"): 16500,
    ("South", "Jan"): 12000,
    ("South", "Feb"): 13500,
    ("South", "Mar"): 14000,
    ("East", "Jan"): 10000,
    ("East", "Feb"): 11500,
    ("East", "Mar"): 12000,
}

mi = pd.MultiIndex.from_tuples(sales_data.keys(), names=["Region", "Month"])
regional_sales = pd.Series(sales_data.values(), index=mi, name="Revenue")

print(regional_sales)
print(regional_sales["North"])

# Cross-section with .xs()
print(regional_sales.xs("Jan", level="Month"))

# Convert to DataFrame
df_sales = regional_sales.reset_index()
print(df_sales)
```

---

## Module 3: Cleaning

---

### 3.1 Missingness Auditing — `.isna()` / `.notna()`

> **Lecture Core Insight:** Before you can fix missing data, you must find it. `.isna()` returns a boolean mask of where nulls live; `.notna()` is its inverse. Sum them to count nulls per column.

#### 1. Engine Rules & Mechanics

| Method | Returns | Pattern |
|---|---|---|
| `df.isna()` | DataFrame of bools | `True` where value is null |
| `df.notna()` | DataFrame of bools | `True` where value is not null |
| `df.isna().sum()` | Series of ints | Null count per column |
| `df.isna().sum().sum()` | int | Total nulls in entire DataFrame |
| `df.isna().any(axis=1)` | Series of bools | Rows that contain any null |

#### 2. Live Lecture Code Blueprint

```python
import pandas as pd
import numpy as np

customer_db = pd.DataFrame({
    "customer_id": [1, 2, 3, 4, 5, 6],
    "name": ["Alice", "Bob", np.nan, "Diana", "Eve", np.nan],
    "email": ["alice@co.com", np.nan, "charlie@co.com", "diana@co.com", np.nan, np.nan],
    "age": [25, np.nan, 35, np.nan, 28, 45],
    "signup_date": pd.date_range("2025-01-01", periods=6, freq="D")
})
customer_db.loc[[3, 5], "signup_date"] = pd.NaT

print(customer_db.isna().sum())
print(f"Rows with any null: {customer_db.isna().any(axis=1).sum()}")
print(f"Total null cells: {customer_db.isna().sum().sum()}")
```

---

### 3.2 Null Elimination — `.dropna()`

> **Lecture Core Insight:** Sometimes the fastest fix for missing data is to delete the offending rows or columns. `.dropna()` removes any row (or column) that contains a null — but use this sparingly, as you might lose valuable signal.

#### 1. Engine Rules & Mechanics

| Parameter | Effect |
|---|---|
| `axis=0` | Drop rows with nulls (default) |
| `axis=1` | Drop columns with nulls |
| `how="any"` | Drop if ANY null is present (default) |
| `how="all"` | Drop only if ALL values are null in that row/col |
| `thresh=n` | Keep rows with at least `n` non-null values |
| `subset=["col1", "col2"]` | Only consider nulls in these columns |

#### 2. Live Lecture Code Blueprint

```python
import pandas as pd
import numpy as np

survey = pd.DataFrame({
    "respondent_id": [1, 2, 3, 4, 5],
    "age": [25, 34, np.nan, 29, 41],
    "income": [50000, np.nan, 75000, np.nan, 62000],
    "satisfaction": [4, 5, 3, np.nan, 4],
    "region": ["North", "South", np.nan, "East", "West"]
})

# Drop any row with at least one null
clean_any = survey.dropna()
print(f"Shape after dropna(how='any'): {clean_any.shape}")

# Drop rows where ALL values are null
clean_all = survey.dropna(how="all")

# Drop with threshold — keep rows with at least 3 non-nulls
clean_thresh = survey.dropna(thresh=3)

# Drop based on specific columns
clean_subset = survey.dropna(subset=["age", "income"])
```

---

### 3.3 Imputation Strategies — `.fillna()`

> **Lecture Core Insight:** Rather than deleting nulls, you can fill them with reasonable substitutes — the mean, median, mode, or a forward/backward fill. `.fillna()` is the Swiss Army knife of missing-value treatment.

#### 1. Engine Rules & Mechanics

| Strategy | Method | Best For |
|---|---|---|
| **Constant fill** | `df.fillna(value)` | Categorical "Unknown", numeric 0 |
| **Mean/Median fill** | `df["col"].fillna(df["col"].mean())` | Skewed numeric data (use median) |
| **Forward fill** | `df.fillna(method="ffill")` | Time series — carry last value forward |
| **Backward fill** | `df.fillna(method="bfill")` | Time series — use next value backward |
| **Interpolate** | `df.interpolate()` | Linear interpolation for ordered data |

#### 2. Live Lecture Code Blueprint

```python
import pandas as pd
import numpy as np

sensor = pd.DataFrame({
    "timestamp": pd.date_range("2025-01-01 00:00", periods=10, freq="10min"),
    "temp": [22.1, 22.3, np.nan, 22.8, np.nan, np.nan, 23.0, 23.2, 23.1, 23.4],
    "humidity": [55, np.nan, 53, 52, np.nan, 54, 56, np.nan, 57, 58]
})

# Forward fill (carry last value forward)
sensor_ffill = sensor.fillna(method="ffill")

# Column-specific fill
sensor["temp"] = sensor["temp"].fillna(sensor["temp"].median())
sensor["humidity"] = sensor["humidity"].fillna(method="ffill")

# Interpolation for ordered data
sensor_interp = sensor.interpolate(method="linear")
```

---

### 3.4 Value Mapping — `.replace()`

> **Lecture Core Insight:** `.replace()` swaps arbitrary values in your data for other values. It's for one-to-one or many-to-one substitutions — fixing inconsistent labels, recoding categories, or scrubbing placeholder strings.

#### 1. Engine Rules & Mechanics

| Pattern | Effect |
|---|---|
| `df.replace(old, new)` | Replace single old value with new |
| `df.replace({old1: new1, old2: new2})` | Dict-based mapping |
| `df.replace({"col": {old: new}})` | Column-specific mapping |
| `df.replace([old1, old2], new)` | Many-to-one replacement |
| `df.replace(regex=pattern, value=new)` | Regex-based replacement |

#### 2. Live Lecture Code Blueprint

```python
import pandas as pd

messy = pd.DataFrame({
    "response_id": range(1, 7),
    "satisfaction": ["Very Satisfied", "satisfied", "Very Satisfied", "Dissatisfied", "very satisfied", "Satisfied"],
    "income_bracket": ["50-75k", "75-100k", "50-75k", "above 100k", "under 25k", "75-100k"],
    "region": ["NY", "CA", "ny", " TX", "CA  ", " ny"]
})

# Standardize satisfaction strings
messy["satisfaction"] = messy["satisfaction"].replace({
    "satisfied": "Satisfied",
    "very satisfied": "Very Satisfied",
    "Dissatisfied": "Dissatisfied"
})

# Map income brackets to codes
messy["income_code"] = messy["income_bracket"].replace({
    "under 25k": 1,
    "50-75k": 2,
    "75-100k": 3,
    "above 100k": 4
})
```

---

### 3.5 Deduplication Mechanics — `.duplicated()` / `.drop_duplicates()`

> **Lecture Core Insight:** Duplicates silently inflate your metrics. `.duplicated()` flags them; `.drop_duplicates()` removes them. Always check *which* columns define uniqueness before dropping.

#### 1. Engine Rules & Mechanics

| Parameter | Effect |
|---|---|
| `subset=["col1", "col2"]` | Check duplicates only on these columns |
| `keep="first"` | Keep first occurrence (default) |
| `keep="last"` | Keep last occurrence |
| `keep=False` | Drop ALL duplicates (keep none) |
| `.duplicated(keep=False)` | Flags ALL duplicated rows (not just the 2nd+) |

#### 2. Live Lecture Code Blueprint

```python
import pandas as pd

logs = pd.DataFrame({
    "user_id": [101, 102, 101, 103, 102, 104, 101],
    "action": ["login", "login", "purchase", "login", "purchase", "login", "login"],
    "timestamp": pd.date_range("2025-01-01 08:00", periods=7, freq="5min")
})

# Find duplicate rows
print(logs.duplicated())

# Drop exact duplicates
logs_deduped = logs.drop_duplicates()

# Check for duplicate user_id
logs_user_deduped = logs.drop_duplicates(subset=["user_id"], keep="first")

# See all rows that have a duplicate user_id
print(logs[logs.duplicated(subset=["user_id"], keep=False)])
```

---

### 3.6 Structural Refactoring — `.rename()`

> **Lecture Core Insight:** Column names and index labels are metadata — they should be clean, consistent, and informative. `.rename()` lets you rename columns and index entries without rebuilding the entire DataFrame.

#### 1. Engine Rules & Mechanics

| Parameter | Effect |
|---|---|
| `columns={"old": "new"}` | Rename columns via mapping |
| `index={"old": "new"}` | Rename index labels via mapping |
| `mapper=str.upper` | Apply function to all names |
| `axis=1` or `axis="columns"` | Apply mapper to columns |
| `axis=0` or `axis="index"` | Apply mapper to index |

#### 2. Live Lecture Code Blueprint

```python
import pandas as pd

raw = pd.DataFrame({
    "first_name": ["Alice", "Bob", "Charlie"],
    "last_name": ["Smith", "Jones", "Brown"],
    "annual_income": [75000, 62000, 95000],
    "years_of_experience_employed": [5, 3, 10]
})

# Rename to clean, standardized names
clean = raw.rename(columns={
    "first_name": "first",
    "last_name": "last",
    "annual_income": "income",
    "years_of_experience_employed": "years_exp"
})

# Apply transformation to all column names at once
lowercase = raw.rename(columns=str.lower)
```

---

### 3.7 Type Standardization — `.astype()` / `.convert_dtypes()`

> **Lecture Core Insight:** Pandas often guesses dtypes conservatively (e.g., strings become `object`). `.astype()` lets you explicitly set dtypes; `.convert_dtypes()` uses the modern nullable types (`Int64`, `string`) automatically.

#### 1. Engine Rules & Mechanics

| Method | Best For |
|---|---|
| `df["col"].astype("int64")` | Explicit type casting — may raise if nulls are present |
| `df["col"].astype("Int64")` | Nullable integer — safe for columns with NaNs |
| `df["col"].astype("category")` | Memory optimization for low-cardinality strings |
| `df.convert_dtypes()` | Automatic conversion to modern nullable types |
| `pd.to_numeric(series, errors="coerce")` | Force numeric — coerce errors to NaN |

#### 2. Live Lecture Code Blueprint

```python
import pandas as pd
import numpy as np

messy_types = pd.DataFrame({
    "id": ["100", "200", "300", "400"],
    "price": ["$12.99", "$24.50", "$9.99", "$39.99"],
    "quantity": ["5", "10", "3", "8"],
    "rating": [4.5, 3.8, None, 5.0],
    "category": ["Electronics", "Clothing", "Home", "Electronics"]
})

# Convert id to int
messy_types["id"] = messy_types["id"].astype("int64")

# Clean price strings and convert
messy_types["price"] = messy_types["price"].str.replace("$", "").astype("float64")

# Convert quantity to nullable Int64
messy_types["quantity"] = messy_types["quantity"].astype("Int64")

# Convert category to category dtype (saves memory)
messy_types["category"] = messy_types["category"].astype("category")

# Auto-convert with modern types
auto = messy_types.convert_dtypes()
print(auto.dtypes)
```

---

### 3.8 String Preprocessing

> **Lecture Core Insight:** Real-world text data is messy — inconsistent casing, leading/trailing spaces, and mis-spellings. The `.str` accessor gives you vectorized string operations across an entire column.

#### 1. Engine Rules & Mechanics

| Method | What It Does |
|---|---|
| `.str.strip()` | Remove leading and trailing whitespace |
| `.str.lower()` | Convert to lowercase |
| `.str.upper()` | Convert to uppercase |
| `.str.title()` | Capitalize first letter of each word |
| `.str.replace(a, b)` | Replace substring (not regex by default) |
| `.str.capitalize()` | First letter uppercase, rest lower |

#### 2. Live Lecture Code Blueprint

```python
import pandas as pd

raw_categories = pd.DataFrame({
    "product_id": [101, 102, 103, 104, 105],
    "category": ["  Electronics  ", "CLOTHING", "home", "  Electronics  ", "Home "],
    "color": ["  Red", "Blue  ", "  Green  ", "red", "BLUE"]
})

# Fix whitespace
raw_categories["category"] = raw_categories["category"].str.strip()
raw_categories["color"] = raw_categories["color"].str.strip()

# Standardize to title case
raw_categories["category"] = raw_categories["category"].str.title()
raw_categories["color"] = raw_categories["color"].str.title()

# Quick consistency check
print(raw_categories["category"].unique())
print(raw_categories["color"].unique())
```

---

### 3.9 Outlier Engineering — `.clip()` / Threshold Masks

> **Lecture Core Insight:** Outliers can wreck your summary statistics and models. `.clip()` caps values at a lower and upper bound — it's the fastest way to constrain extremes while keeping the data shape intact.

#### 1. Engine Rules & Mechanics

| Method | Effect |
|---|---|
| `df["col"].clip(lower=a, upper=b)` | Cap values between `a` and `b` |
| `df["col"].clip(lower=percentile)` | Floor at a specific value |
| `df["col"].clip(upper=percentile)` | Ceiling at a specific value |
| **IQR method** | Flag outliers via |value - median| > 1.5*IQR |

#### 2. Live Lecture Code Blueprint

```python
import pandas as pd
import numpy as np

rng = np.random.default_rng(42)
transactions = pd.DataFrame({
    "txn_id": range(1, 101),
    "amount": np.concatenate([
        rng.uniform(5, 200, 95),
        [2500, 3000, 4500, 100, 150]
    ])
})

# Using IQR method
Q1 = transactions["amount"].quantile(0.25)
Q3 = transactions["amount"].quantile(0.75)
IQR = Q3 - Q1
lower_bound = Q1 - 1.5 * IQR
upper_bound = Q3 + 1.5 * IQR

outliers = transactions[
    (transactions["amount"] < lower_bound) |
    (transactions["amount"] > upper_bound)
]
print(f"Outliers detected: {len(outliers)}")

# Clip to bounds
transactions["amount_clipped"] = transactions["amount"].clip(lower=lower_bound, upper=upper_bound)

# Explicit cap at 99th percentile
cap = transactions["amount"].quantile(0.99)
transactions["amount_capped"] = transactions["amount"].clip(upper=cap)
```

---

## Module 4: Transformation

---

### 4.1 Feature Generation

> **Lecture Core Insight:** New columns don't require loops — you create them by assigning the result of an expression to a new column name. The expression runs on every row simultaneously (vectorized).

#### 1. Engine Rules & Mechanics

| Pattern | Effect |
|---|---|
| `df["new"] = df["a"] + df["b"]` | Element-wise arithmetic creates new column |
| `df["new"] = df["a"] * 0.10` | Scalar broadcasting |
| `df["new"] = np.sqrt(df["a"])` | NumPy ufuncs work directly |
| `df.insert(pos, "name", values)` | Insert at specific column position |

#### 2. Live Lecture Code Blueprint

```python
import pandas as pd
import numpy as np

orders = pd.DataFrame({
    "order_id": range(1001, 1011),
    "item_price": [25.00, 49.99, 15.50, 99.00, 12.75, 34.50, 8.99, 200.00, 45.00, 75.00],
    "quantity": [3, 1, 5, 2, 4, 2, 10, 1, 3, 2],
    "tax_rate": [0.08, 0.08, 0.08, 0.10, 0.08, 0.10, 0.08, 0.10, 0.08, 0.08]
})

# Derived columns — vectorized arithmetic
orders["subtotal"] = orders["item_price"] * orders["quantity"]
orders["tax_amount"] = orders["subtotal"] * orders["tax_rate"]
orders["total"] = orders["subtotal"] + orders["tax_amount"]

# Using NumPy functions
orders["log_price"] = np.log(orders["item_price"])
orders["price_rounded"] = np.round(orders["item_price"], 0)
```

---

### 4.2 Vectorized Arithmetic

> **Lecture Core Insight:** Vectorized operations use underlying C loops — they are 10-100x faster than Python loops. Every arithmetic operation (`+`, `-`, `*`, `/`, `**`) broadcasts across the entire column in one pass.

#### 1. Engine Rules & Mechanics

| Operation | Speed | Example |
|---|---|---|
| `df["a"] + df["b"]` | Fast | Element-wise addition |
| `df["a"] * 2` | Fast | Scalar broadcast |
| `np.log(df["a"])` | Fast | NumPy ufunc — C level |
| `df["a"] > 5` | Fast | Element-wise boolean |
| Python `for` loop | Slow | Avoid at all costs |

#### 2. Live Lecture Code Blueprint

```python
import pandas as pd
import numpy as np
import time

n = 1_000_000
df = pd.DataFrame({
    "a": np.random.uniform(0, 100, n),
    "b": np.random.uniform(0, 100, n)
})

# FAST — vectorized
t0 = time.perf_counter()
result_vector = df["a"] * df["b"] + df["a"]
t1 = time.perf_counter()

# SLOW — Python loop
t2 = time.perf_counter()
result_loop = pd.Series([0.0] * n)
for i in range(n):
    result_loop.iloc[i] = df["a"].iloc[i] * df["b"].iloc[i] + df["a"].iloc[i]
t3 = time.perf_counter()

print(f"Vectorized: {t1-t0:.4f}s")
print(f"Python loop: {t3-t2:.4f}s")
print(f"Speedup: {(t3-t2)/(t1-t0):.0f}x")
```

---

### 4.3 Functional Processing — `.apply()` / `.map()` / `.applymap()`

> **Lecture Core Insight:** These are escape hatches when vectorized operations don't exist — but they run Python loops under the hood. Use them only when you must. `.map()` is for Series value substitution, `.apply()` is for row/column functions.

#### 1. Engine Rules & Mechanics

| Method | Scope | Input | Output | When to Use |
|---|---|---|---|---|
| `.map(dict_or_func)` | Series (1 column) | Each scalar value | Transformed scalar | Value mapping, element-wise |
| `.apply(func, axis=0)` | DataFrame or Series | Series (column or row) | Series or scalar | Row/column aggregation or transformation |
| `.applymap(func)` | DataFrame (deprecated) | Each scalar | Transformed scalar | Replaced by `.map()` on DataFrame |

**Warning:** `.apply()` is ~10-50x slower than vectorized NumPy. Only use when no vectorized alternative exists.

#### 2. Live Lecture Code Blueprint

```python
import pandas as pd
import numpy as np

employees = pd.DataFrame({
    "name": ["Alice", "Bob", "Charlie", "Diana"],
    "salary": [75000, 62000, 95000, 88000],
    "start_year": [2018, 2020, 2015, 2019]
})

# .map() for Series value replacement
tier_map = {"Alice": "Senior", "Bob": "Mid", "Charlie": "Lead", "Diana": "Senior"}
employees["level"] = employees["name"].map(tier_map)

# .apply() with axis=1 for row-wise logic
def calc_bonus(row):
    years = 2025 - row["start_year"]
    base = row["salary"] * 0.10
    return base * (1 + years * 0.02)

employees["bonus"] = employees.apply(calc_bonus, axis=1)

# Prefer vectorized where possible — much faster
employees["years_worked"] = 2025 - employees["start_year"]
employees["bonus_vector"] = employees["salary"] * 0.10 * (1 + employees["years_worked"] * 0.02)
```

---

### 4.4 Ordering Mechanics — `.sort_values()` / `.sort_index()`

> **Lecture Core Insight:** Sorting is fundamental — `.sort_values()` orders by column contents, `.sort_index()` orders by the row labels. Use `ascending=` to flip direction and `na_position=` to control null placement.

#### 1. Engine Rules & Mechanics

| Parameter | Effect |
|---|---|
| `by="col"` | Sort by a single column |
| `by=["col1", "col2"]` | Sort by multiple columns |
| `ascending=True` | Ascending order (default) |
| `ascending=[True, False]` | Mixed direction for multi-column sort |
| `na_position="last"` | Put nulls at end (default) |
| `na_position="first"` | Put nulls at beginning |

#### 2. Live Lecture Code Blueprint

```python
import pandas as pd
import numpy as np

inventory = pd.DataFrame({
    "sku": ["A-100", "B-200", "A-101", "C-300", "A-102", "B-201"],
    "category": ["Electronics", "Books", "Electronics", "Clothing", "Electronics", "Books"],
    "price": [299.99, 19.99, 149.99, 59.99, None, 24.99],
    "stock": [50, 200, 150, 75, 0, 180]
})

# Sort by price ascending
inventory_sorted = inventory.sort_values("price")

# Sort by category, then price descending
inventory_sorted = inventory.sort_values(["category", "price"], ascending=[True, False])

# Sort index back to default
inventory_sorted = inventory_sorted.sort_index()

# Null handling
inventory_sorted = inventory.sort_values("price", na_position="first")
```

---

### 4.5 Schema Alignment

> **Lecture Core Insight:** You often need columns in a specific order (for CSV export, model input, or readability). Reorder columns by passing a list of column names in the desired order.

#### 1. Engine Rules & Mechanics

| Pattern | Effect |
|---|---|
| `df[["col3", "col1", "col2"]]` | Reorder and subset in one step |
| `df.reindex(columns=order_list)` | Reorder columns to match list — drops extras, fills missing with NaN |
| `df.filter(items=["a", "b"])` | Select by exact column names |
| `df.filter(regex="pattern")` | Select columns matching regex |

#### 2. Live Lecture Code Blueprint

```python
import pandas as pd

report = pd.DataFrame({
    "revenue": [150000, 180000, 120000],
    "quarter": ["Q1", "Q2", "Q3"],
    "costs": [95000, 110000, 80000],
    "profit": [55000, 70000, 40000],
    "region": ["North", "South", "East"]
})

# Reorder for a clean report
final_report = report[["quarter", "region", "revenue", "costs", "profit"]]

# Using reindex
final_report = report.reindex(columns=["region", "quarter", "revenue", "profit", "margin"])
```

---

### 4.6 Fluid Processing — `.assign()` / `.pipe()` / Method Chaining

> **Lecture Core Insight:** Method chaining lets you express a data pipeline as a single, readable chain of operations — no intermediate variables cluttering your namespace.

#### 1. Engine Rules & Mechanics

| Method | Effect |
|---|---|
| `df.assign(new_col=expr)` | Add or replace columns — accepts kwargs |
| `df.assign(col=lambda d: d["a"] + d["b"])` | Computed column using lambda |
| `df.pipe(func)` | Pass DataFrame to function `func` |
| **Chaining** | `df.op1().op2().op3()` — each returns DataFrame |

#### 2. Live Lecture Code Blueprint

```python
import pandas as pd
import numpy as np

orders_raw = pd.DataFrame({
    "order_id": [1, 2, 3, 4, 5],
    "price": [25.00, 49.99, 15.00, 99.00, 12.50],
    "qty": [3, 1, 5, 2, 4],
    "tax": [0.08, 0.08, 0.08, 0.10, 0.08]
})

def add_discount_flag(df, threshold=100):
    df["discount_flag"] = df["total"] > threshold
    return df

# Method chain
result = (
    orders_raw
    .assign(subtotal=lambda d: d["price"] * d["qty"])
    .assign(tax_amount=lambda d: d["subtotal"] * d["tax"])
    .assign(total=lambda d: d["subtotal"] + d["tax_amount"])
    .pipe(add_discount_flag, threshold=100)
    .assign(total_rounded=lambda d: d["total"].round(2))
    .sort_values("total", ascending=False)
)

print(result)
```

---

### 4.7 Frequency Auditing — `.value_counts()` / `.unique()` / `.nunique()`

> **Lecture Core Insight:** Understanding cardinality and frequency of categorical data is critical. `.value_counts()` gives you a full frequency table; `.unique()` lists distinct values; `.nunique()` gives you the count of distinct values.

#### 1. Engine Rules & Mechanics

| Method | Returns | Use Case |
|---|---|---|
| `df["col"].value_counts()` | Series (values as index, counts as values) | Full distribution table |
| `df["col"].value_counts(normalize=True)` | Series (proportions instead of counts) | Relative frequencies |
| `df["col"].unique()` | NumPy array | List of distinct values |
| `df["col"].nunique()` | int | Count of distinct values |

#### 2. Live Lecture Code Blueprint

```python
import pandas as pd
import numpy as np

rng = np.random.default_rng(42)
support_tickets = pd.DataFrame({
    "ticket_id": range(1, 1001),
    "priority": rng.choice(["Critical", "High", "Medium", "Low"], 1000, p=[0.05, 0.15, 0.50, 0.30]),
    "category": rng.choice(["Bug", "Feature Request", "Billing", "Account", "Other"], 1000),
    "agent": rng.choice(["Alice", "Bob", "Charlie", "Diana"], 1000)
})

# Full distribution
print(support_tickets["priority"].value_counts())

# Proportions
print(support_tickets["category"].value_counts(normalize=True).round(3))

# Distinct agents
print(support_tickets["agent"].unique())
print(f"Unique agents: {support_tickets['agent'].nunique()}")
```

---

### 4.8 Numerical Binning — `.cut()` / `.qcut()`

> **Lecture Core Insight:** Binning converts continuous numeric values into discrete buckets. Use `.cut()` when you define the bucket edges; use `.qcut()` when you want each bucket to have the same number of observations.

#### 1. Engine Rules & Mechanics

| Method | Bucket Strategy | Best For |
|---|---|---|
| `pd.cut(series, bins=n)` | Equal-width bins of `n` intervals | When bucket boundaries have intrinsic meaning |
| `pd.cut(series, bins=[0, 25, 50, 100])` | Custom boundaries | Domain-specific thresholds |
| `pd.qcut(series, q=4)` | Quartile-based (equal count per bin) | When you want balanced groups |

#### 2. Live Lecture Code Blueprint

```python
import pandas as pd
import numpy as np

rng = np.random.default_rng(42)
employees = pd.DataFrame({
    "emp_id": range(1, 101),
    "salary": np.concatenate([
        rng.uniform(35000, 50000, 25),
        rng.uniform(50000, 75000, 40),
        rng.uniform(75000, 100000, 25),
        rng.uniform(100000, 200000, 10)
    ])
})

# Equal-width bins with labels
employees["tier_cut"] = pd.cut(
    employees["salary"],
    bins=4,
    labels=["Entry", "Mid", "Senior", "Executive"]
)

# Quantile-based bins (equal count)
employees["tier_qcut"] = pd.qcut(
    employees["salary"],
    q=4,
    labels=["Q1", "Q2", "Q3", "Q4"]
)

print(employees["tier_cut"].value_counts())
print(employees["tier_qcut"].value_counts())

---

## Module 5: Aggregation

---

### 5.1 Split-Apply-Combine Framework

> **Lecture Core Insight:** The core GroupBy loop. **Split** the data into groups, **Apply** a function to each group, **Combine** results. This is the most powerful pattern in Pandas.

#### 1. Engine Rules & Mechanics

| Concept | Rule |
|---|---|
| `df.groupby("key")` | Creates a GroupBy object — lazy, no computation yet |
| Aggregation method | You must call `.sum()`, `.mean()`, etc. to trigger execution |
| Result index | Group keys become the new index |

#### 2. Live Lecture Code Blueprint

```python
import pandas as pd
import numpy as np

sales = pd.DataFrame({
    "store": np.random.choice(["NYC", "LA", "Chicago"], 50),
    "revenue": np.random.uniform(10, 500, 50)
})
print(sales.groupby("store")["revenue"].mean())
```

---

### 5.2 Grouping Formations

> **Lecture Core Insight:** Single vs multi-column groups. Group by one column or pass a list for hierarchical groupings.

#### 1. Engine Rules & Mechanics

| Pattern | Effect |
|---|---|
| `df.groupby("col")` | Single-column grouping |
| `df.groupby(["a", "b"])` | Multi-column grouping |

#### 2. Live Lecture Code Blueprint

```python
import pandas as pd
import numpy as np

df = pd.DataFrame({
    "region": np.random.choice(["North", "South"], 30),
    "store": np.random.choice(["A", "B"], 30),
    "sales": np.random.uniform(100, 1000, 30)
})
print(df.groupby(["region", "store"]).mean())
```

---

### 5.3 Reduction Functions

> **Lecture Core Insight:** Built-in aggregates. `.sum()`, `.mean()`, `.count()`, `.min()`, `.max()`, `.median()`, `.std()` — each reduces groups to a single scalar.

#### 2. Live Lecture Code Blueprint

```python
import pandas as pd
import numpy as np

df = pd.DataFrame({
    "product": np.random.choice(["A", "B"], 50),
    "qty": np.random.randint(1, 10, 50)
})
g = df.groupby("product")
print(g["qty"].agg(["sum", "mean", "std", "count"]))
```

---

### 5.4 Custom Aggregations — `.agg()`

> **Lecture Core Insight:** Multiple metrics at once. `.agg()` accepts a list, dict, or callable to compute multiple aggregates simultaneously.

#### 1. Engine Rules & Mechanics

| Pattern | Effect |
|---|---|
| `.agg(["mean", "sum"])` | List — multi-level columns |
| `.agg({"sales": "sum", "profit": "mean"})` | Dict — per-column functions |

#### 2. Live Lecture Code Blueprint

```python
import pandas as pd
import numpy as np

df = pd.DataFrame({
    "dept": np.random.choice(["Eng", "Sales"], 30),
    "salary": np.random.randint(50000, 150000, 30),
    "bonus": np.random.uniform(5000, 20000, 30)
})
print(df.groupby("dept").agg({"salary": ["mean", "std"], "bonus": "sum"}))
```

---

### 5.5 Transformation vs. Reduction

> **Lecture Core Insight:** Critical distinction. `.transform()` returns same shape as original; `.agg()` reduces each group to one row.

#### 1. Engine Rules & Mechanics

| Method | Output Shape | Use Case |
|---|---|---|
| `.agg(func)` | Reduced (n_groups rows) | Summary tables |
| `.transform(func)` | Same as original | Add group stats as column |

#### 2. Live Lecture Code Blueprint

```python
import pandas as pd
import numpy as np

df = pd.DataFrame({
    "dept": np.random.choice(["Eng", "Sales"], 10),
    "salary": np.random.randint(50000, 150000, 10)
})
df["dept_avg"] = df.groupby("dept")["salary"].transform("mean")
df["rel_to_avg"] = df["salary"] - df["dept_avg"]
print(df)
```

---

### 5.6 Structural Group Filters — `.filter()`

> **Lecture Core Insight:** Eliminate entire groups. `.filter()` removes groups based on a condition evaluated on the group as a whole.

#### 2. Live Lecture Code Blueprint

```python
import pandas as pd
import numpy as np

df = pd.DataFrame({
    "dept": np.random.choice(["A", "B", "C"], 30),
    "value": np.random.uniform(10, 100, 30)
})
filt = df.groupby("dept").filter(lambda g: g["value"].mean() > 50)
print(f"Kept {len(filt)} of {len(df)} rows")
```

---

### 5.7 Grouped Ordering — Rank Within Groups

> **Lecture Core Insight:** Local rankings. Compute ranks or pick top-N within each group independently — perfect for leaderboards or per-category best-sellers.

#### 2. Live Lecture Code Blueprint

```python
import pandas as pd
import numpy as np

df = pd.DataFrame({
    "store": np.random.choice(["NYC", "LA"], 20),
    "revenue": np.random.uniform(50, 500, 20)
})
df["rank"] = df.groupby("store")["revenue"].rank(ascending=False, method="dense")
print(df.sort_values(["store", "rank"]))
```

---

### 5.8 Contingency Tables — `pd.crosstab()`

> **Lecture Core Insight:** Fast frequency tables. `pd.crosstab()` computes frequency tables between two categorical variables — perfect for quick contingency analysis.

#### 1. Engine Rules & Mechanics

| Parameter | Effect |
|---|---|
| `index` | Row categories |
| `columns` | Column categories |
| `margins=True` | Add row/column totals |
| `normalize="index"` | Row-wise percentages |

#### 2. Live Lecture Code Blueprint

```python
import pandas as pd
import numpy as np

rng = np.random.default_rng(42)
s = pd.DataFrame({
    "gender": rng.choice(["M", "F"], 100),
    "satisfaction": rng.choice(["Low", "Med", "High"], 100)
})
ct = pd.crosstab(s["gender"], s["satisfaction"], margins=True)
print(ct)
```

---

## Module 6: Combining Data

---

### 6.1 Linear Merges — `pd.concat()`

> **Lecture Core Insight:** Stacking datasets. `pd.concat()` glues DataFrames along rows (axis=0) or columns (axis=1). Unlike merge, it stacks without key alignment.

#### 1. Engine Rules & Mechanics

| Parameter | Effect |
|---|---|
| `axis=0` | Stack rows (default) |
| `axis=1` | Stack columns (side-by-side) |
| `ignore_index=True` | Reset to default RangeIndex |
| `keys=["a", "b"]` | Add MultiIndex level for source tracking |

#### 2. Live Lecture Code Blueprint

```python
import pandas as pd

q1 = pd.DataFrame({"prod": ["A", "B"], "sales": [100, 200]})
q2 = pd.DataFrame({"prod": ["A", "C"], "sales": [150, 250]})
all_q = pd.concat([q1, q2], axis=0, ignore_index=True)
print(all_q)
```

---

### 6.2 Database Joins — `pd.merge()`

> **Lecture Core Insight:** Relational merging. `pd.merge()` combines DataFrames using keys like SQL JOINs. Specify the key column and join type.

#### 1. Engine Rules & Mechanics

| Parameter | Effect |
|---|---|
| `on="key"` | Column to join on (must exist in both) |
| `how="inner"` | Default — only matching keys |
| `suffixes=("_x", "_y")` | Handle overlapping column names |
| `validate="one_to_one"` | Check merge integrity |

#### 2. Live Lecture Code Blueprint

```python
import pandas as pd

orders = pd.DataFrame({"order_id": [1, 2], "cust_id": [101, 102], "amt": [250, 150]})
cust = pd.DataFrame({"cust_id": [101, 102], "name": ["Alice", "Bob"]})
merged = pd.merge(orders, cust, on="cust_id", how="left")
print(merged)
```

---

### 6.3 Classical Join Typologies

> **Lecture Core Insight:** Inner, Left, Right, Outer. The `how` parameter controls which keys appear: matching only (inner), preserve left keys, preserve right keys, or all keys (outer).

#### 1. Engine Rules & Mechanics

| Join Type | Result |
|---|---|
| `inner` | Only matching keys in both |
| `left` | All left keys; NaN where right missing |
| `right` | All right keys; NaN where left missing |
| `outer` | All keys from both; NaN where either missing |

#### 2. Live Lecture Code Blueprint

```python
import pandas as pd

left = pd.DataFrame({"id": [1, 2, 3], "x": ["a", "b", "c"]})
right = pd.DataFrame({"id": [2, 3, 4], "y": ["x", "y", "z"]})
for how in ["inner", "left", "right", "outer"]:
    r = pd.merge(left, right, on="id", how=how)
    print(f"{how}: ids={r['id'].tolist()}")
```

---

### 6.4 Key Targets — Columns vs Index

> **Lecture Core Insight:** Flexible key sources. Merge on columns (`on`, `left_on`, `right_on`) or on the index (`left_index`, `right_index`).

#### 2. Live Lecture Code Blueprint

```python
import pandas as pd

da = pd.DataFrame({"sku": ["A", "B"], "wh": ["North", "South"]}).set_index("sku")
db = pd.DataFrame({"code": ["A", "B"], "stock": [100, 50]})
m = pd.merge(da, db, left_index=True, right_on="code")
print(m)
```

---

### 6.5 Collision Mitigation — Suffixes & Validate

> **Lecture Core Insight:** Handle overlapping names. Use `suffixes` to disambiguate same-named columns. Use `validate` to check merge integrity.

#### 2. Live Lecture Code Blueprint

```python
import pandas as pd

l = pd.DataFrame({"id": [1, 2], "val": [10, 20], "status": ["a", "b"]})
r = pd.DataFrame({"id": [2, 3], "val": [200, 300], "status": ["x", "y"]})
m = pd.merge(l, r, on="id", how="outer", suffixes=("_L", "_R"))
print(m)
```

---

### 6.6 Index Combinations — `.join()`

> **Lecture Core Insight:** Convenience shortcut. `.join()` merges on the index by default — syntactic sugar for merge with left_index and right_index.

#### 2. Live Lecture Code Blueprint

```python
import pandas as pd

p = pd.DataFrame({"price": [10, 20]}, index=["A", "B"])
i = pd.DataFrame({"stock": [100, 50]}, index=["A", "B"])
c = p.join(i, how="inner")
print(c)
```

---

## Module 7: Reshaping

---

### 7.1 Wide vs. Long Formats

> **Lecture Core Insight:** Two fundamental shapes. **Wide** data has one row per subject with many columns (good for reporting). **Long** (tidy) data has one row per observation with key-value pairs (good for analysis and plotting). Pandas reshaping functions convert between these.

#### 1. Engine Rules & Mechanics

| Format | Structure | Pros / Cons |
|---|---|---|
| **Wide** | Each subject = one row; metrics = separate columns | Human-readable; bad for groupby/plotting |
| **Long** (Tidy) | Each observation = one row; variable names in one column, values in another | Machine-readable; great for groupby and seaborn |

#### 2. Live Lecture Code Blueprint

```python
import pandas as pd

# Wide format — one row per store, columns for each month
wide = pd.DataFrame({
    "store": ["NYC", "LA", "Chicago"],
    "Jan": [100, 80, 120],
    "Feb": [110, 90, 130],
    "Mar": [105, 85, 125]
})
print("Wide:")
print(wide)

# Long format — one row per store-month pair
long = wide.melt(id_vars=["store"], var_name="month", value_name="sales")
print("Long:")
print(long)
```

---

### 7.2 Basic Pivoting — `.pivot()`

> **Lecture Core Insight:** Long to wide. `.pivot()` rotates long data into wide format by spreading unique values of one column into separate columns. No aggregation — assumes one value per cell.

#### 1. Engine Rules & Mechanics

| Parameter | Effect |
|---|---|
| `index` | Column whose values become the new row index |
| `columns` | Column whose values become new column headers |
| `values` | Column to fill the pivoted cells |

> **Warning:** Duplicate index-column pairs cause an error. Use `pivot_table()` if duplicates exist.

#### 2. Live Lecture Code Blueprint

```python
import pandas as pd

long = pd.DataFrame({
    "store": ["NYC", "NYC", "LA", "LA"],
    "month": ["Jan", "Feb", "Jan", "Feb"],
    "sales": [100, 110, 80, 120]
})
wide = long.pivot(index="store", columns="month", values="sales")
print(wide)
```

---

### 7.3 Cross-Tabular Aggregation — `pd.pivot_table()`

> **Lecture Core Insight:** Excel-style pivot tables. `pd.pivot_table()` is like `.pivot()` but handles duplicates by aggregating — combining `groupby` with `.unstack()`. The most powerful reshaping tool for business reports.

#### 1. Engine Rules & Mechanics

| Parameter | Effect |
|---|---|
| `index` | Row grouping (can be a list) |
| `columns` | Column grouping (can be a list) |
| `values` | Column to aggregate |
| `aggfunc` | Aggregation function (default: `np.mean`) |
| `fill_value` | Value to fill missing combinations |
| `margins=True` | Add row/column totals |

#### 2. Live Lecture Code Blueprint

```python
import pandas as pd
import numpy as np

rng = np.random.default_rng(42)
sales = pd.DataFrame({
    "region": rng.choice(["North", "South"], 100),
    "product": rng.choice(["Widget", "Gadget", "Doodad"], 100),
    "sales": rng.uniform(50, 500, 100)
})
pt = pd.pivot_table(sales, index="region", columns="product",
                    values="sales", aggfunc="sum", margins=True)
print(pt)
```

---

### 7.4 Data Melting — `pd.melt()`

> **Lecture Core Insight:** Unpivoting wide to long. `pd.melt()` collapses wide tables into long, tidy format — the inverse of `.pivot()`. Specify `id_vars` (stay as-is) and columns to melt.

#### 1. Engine Rules & Mechanics

| Parameter | Effect |
|---|---|
| `id_vars` | Columns to keep fixed (identifiers) |
| `value_vars` | Columns to unpivot (default: all non-id) |
| `var_name` | Name for the new variable column |
| `value_name` | Name for the new value column |

#### 2. Live Lecture Code Blueprint

```python
import pandas as pd

wide = pd.DataFrame({
    "store": ["NYC", "LA"],
    "Q1": [300, 250],
    "Q2": [350, 280],
    "Q3": [320, 260]
})
long = pd.melt(wide, id_vars=["store"], var_name="quarter", value_name="revenue")
print(long)
```

---

### 7.5 Stack & Unstack

> **Lecture Core Insight:** Compress/expand hierarchies. `.stack()` pivots columns into rows (longer). `.unstack()` pivots rows into columns (wider). They move between levels of a MultiIndex directly.

#### 1. Engine Rules & Mechanics

| Method | Direction | Effect |
|---|---|---|
| `.stack()` | Columns → Rows | Pivots innermost column level into row index |
| `.unstack()` | Rows → Columns | Pivots innermost row index level into column headers |
| `.unstack(level=0)` | Rows → Columns | Specify which level to unstack |

#### 2. Live Lecture Code Blueprint

```python
import pandas as pd

mi = pd.MultiIndex.from_product([["NYC", "LA"], ["Jan", "Feb"]], names=["store", "month"])
df = pd.DataFrame({"sales": [100, 110, 80, 90]}, index=mi)
unstacked = df.unstack(level="month")
print(unstacked)
restacked = unstacked.stack(level="month")
print(restacked)
```

---

### 7.6 MultiIndex Restructuring

> **Lecture Core Insight:** Move levels freely. Use `.swaplevel()`, `.reorder_levels()`, and `.droplevel()` for complete control over hierarchical index structures.

#### 1. Engine Rules & Mechanics

| Method | Effect |
|---|---|
| `.swaplevel(i, j)` | Swap two index levels |
| `.reorder_levels(order)` | Rearrange level order |
| `.droplevel(level)` | Remove a level from the index |

#### 2. Live Lecture Code Blueprint

```python
import pandas as pd

mi = pd.MultiIndex.from_tuples([
    ("Electronics", "NYC"), ("Electronics", "LA"),
    ("Clothing", "NYC"), ("Clothing", "LA")
], names=["dept", "city"])
df = pd.DataFrame({"revenue": [500, 400, 300, 350]}, index=mi)
print(df.swaplevel("dept", "city"))
print(df.droplevel("dept"))
```

---

## Module 8: Text Data

---

### 8.1 String Dtypes — `object` vs `string`

> **Lecture Core Insight:** `object` is not ideal for text. The `object` dtype stores Python pointers (slow, memory-heavy). The `string` dtype stores strings densely and supports missing values natively. Always prefer `string` for text columns.

#### 1. Engine Rules & Mechanics

| dtype | Memory | Nullable | Speed |
|---|---|---|---|
| `object` | Pointer array (8 bytes + Python object) | Yes (None/NaN) | Slow |
| `string` | Contiguous character buffer | Yes (<NA>) | Fast |

#### 2. Live Lecture Code Blueprint

```python
import pandas as pd

# String columns as 'object' by default
df = pd.DataFrame({"name": ["Alice", "Bob", "Charlie"], "email": ["a@co.com", "b@co.com", "c@co.com"]})
print(df.dtypes)  # object

# Convert to 'string' dtype
df = df.astype({"name": "string", "email": "string"})
print(df.dtypes)
```

---

### 8.2 Formatting Mechanics

> **Lecture Core Insight:** Clean text formatting. Inconsistent casing and whitespace are the most common text issues. The `.str` accessor provides vectorized string operations across entire columns.

#### 1. Engine Rules & Mechanics

| Method | Effect | Example |
|---|---|---|
| `.str.lower()` | All lowercase | `"Alice"` → `"alice"` |
| `.str.upper()` | All uppercase | `"Alice"` → `"ALICE"` |
| `.str.strip()` | Remove surrounding whitespace | `" Alice "` → `"Alice"` |
| `.str.title()` | Capitalize each word | `"alice smith"` → `"Alice Smith"` |

#### 2. Live Lecture Code Blueprint

```python
import pandas as pd

messy = pd.DataFrame({
    "name": ["  alice  ", "BOB", "  charlie "],
    "city": ["new york", "  LOS ANGELES  ", "Chicago"]
})
messy["name"] = messy["name"].str.strip().str.title()
messy["city"] = messy["city"].str.strip().str.title()
print(messy)
```

---

### 8.3 Substring Auditing

> **Lecture Core Insight:** Find text patterns. `.str.contains()` checks if a substring exists in each element. `.str.startswith()` and `.str.endswith()` check prefixes/suffixes. All return boolean masks for filtering.

#### 1. Engine Rules & Mechanics

| Method | Returns | Example |
|---|---|---|
| `.str.contains(pat, case=False)` | Boolean mask | Find rows containing "error" |
| `.str.startswith(pat)` | Boolean mask | Find rows starting with "2025" |
| `.str.endswith(pat)` | Boolean mask | Find emails ending with ".com" |

#### 2. Live Lecture Code Blueprint

```python
import pandas as pd

logs = pd.DataFrame({
    "log_id": range(1, 6),
    "message": [
        "Connection timeout error",
        "User login successful",
        "Database connection error",
        "Payment processed",
        "Authentication error"
    ]
})
errors = logs[logs["message"].str.contains("error", case=False)]
print(errors)
```

---

### 8.4 Vectorized Corrections — `.str.replace()`

> **Lecture Core Insight:** Fast text substitutions. `.str.replace()` performs element-wise substring replacement across an entire column. Unlike `.replace()`, it works on string content directly.

#### 1. Engine Rules & Mechanics

| Parameter | Effect |
|---|---|
| `pat` | Pattern (string or regex) to find |
| `repl` | Replacement string |
| `regex=False` | Literal string replacement (default) |

#### 2. Live Lecture Code Blueprint

```python
import pandas as pd

raw = pd.DataFrame({
    "product_code": ["PRD-001", "PRD-002", "PRD-003"],
    "price_str": ["$9.99", "$24.50", "$39.99"]
})
raw["price"] = raw["price_str"].str.replace("$", "").astype("float64")
print(raw[["product_code", "price"]])
```

---

### 8.5 Token Extraction — `.str.split()`

> **Lecture Core Insight:** Split strings into components. `.str.split()` breaks strings on a delimiter into multiple columns. Perfect for parsing structured text like names, addresses, or log formats.

#### 1. Engine Rules & Mechanics

| Parameter | Effect |
|---|---|
| `pat` | Delimiter string (default: whitespace) |
| `n` | Max number of splits |
| `expand=True` | Return as DataFrame instead of Series of lists |

#### 2. Live Lecture Code Blueprint

```python
import pandas as pd

people = pd.DataFrame({
    "full_name": ["Alice Smith", "Bob Jones", "Charlie Brown"],
    "email": ["alice@example.com", "bob@example.com", "charlie@example.com"]
})
name_parts = people["full_name"].str.split(" ", expand=True)
name_parts.columns = ["first", "last"]
print(name_parts)
```

---

### 8.6 Regular Expressions

> **Lecture Core Insight:** Powerful pattern matching. Regex enables complex pattern extraction — phone numbers, zip codes, or any structured text. Use `.str.extract()` to pull matched groups into new columns.

#### 1. Engine Rules & Mechanics

| Method | Effect |
|---|---|
| `.str.contains(regex, regex=True)` | Check if pattern exists |
| `.str.extract(r"(pattern)")` | Extract first match of capture group |
| `.str.extractall(r"(p1)(p2)")` | Extract all matches (MultiIndex) |
| `.str.replace(regex, repl, regex=True)` | Regex-based substitution |

#### 2. Live Lecture Code Blueprint

```python
import pandas as pd

records = pd.DataFrame({
    "raw": [
        "Order #12345: $299.99",
        "Order #67890: $49.99",
        "Order #11121: $999.00"
    ]
})
# Extract order number and amount using regex
extracted = records["raw"].str.extract(r"Order #(\d+): \$(\d+\.\d{2})")
extracted.columns = ["order_num", "amount"]
print(extracted)
```

---

## Module 9: Time Series

---

### 9.1 Temporal Conversion — `pd.to_datetime()`

> **Lecture Core Insight:** Strings to timestamps. `pd.to_datetime()` parses messy date strings into proper `datetime64[ns]` objects. It handles many common formats automatically — no manual string parsing needed.

#### 1. Engine Rules & Mechanics

| Parameter | Effect |
|---|---|
| `format="%Y-%m-%d"` | Explicit format string (faster, avoids ambiguity) |
| `errors="coerce"` | Set unparseable dates to NaT instead of raising |
| `dayfirst=True` | Interpret DD/MM before MM/DD |
| `infer_datetime_format=True` | Auto-detect format (slower but flexible) |

#### 2. Live Lecture Code Blueprint

```python
import pandas as pd

messy_dates = pd.Series([
    "2025-01-15",
    "01/16/2025",
    "Jan 17, 2025",
    "2025-01-18 14:30:00",
    "not-a-date"
])
parsed = pd.to_datetime(messy_dates, errors="coerce")
print(parsed)
print(parsed.dtype)  # datetime64[ns]
```

---

### 9.2 DatetimeIndex

> **Lecture Core Insight:** Time-aware indexing. A `DatetimeIndex` makes your DataFrame time-aware — enabling date-based slicing, resampling, and time-based operations. Set it with `set_index()` after converting to datetime.

#### 1. Engine Rules & Mechanics

- Create via `pd.date_range(start, end, periods, freq)`
- Set as index: `df.set_index(pd.to_datetime(df["date_col"]))`
- Benefits: time slicing, resampling, time-based arithmetic
- Sorting: DatetimeIndex must be sorted for some operations

#### 2. Live Lecture Code Blueprint

```python
import pandas as pd

# Create a date range
dates = pd.date_range(start="2025-01-01", periods=10, freq="D")
ts = pd.DataFrame({
    "value": [10, 15, 12, 18, 20, 17, 22, 25, 19, 21]
}, index=dates)
print(ts)
print(f"Index type: {ts.index.dtype}")
```

---

### 9.3 The `.dt` Accessor

> **Lecture Core Insight:** Extract date components. `.dt` gives access to datetime properties — year, month, day, dayofweek, hour, etc. It's the vectorized way to pull date parts without slow loops.

#### 1. Engine Rules & Mechanics

| Property | Returns | Example Value |
|---|---|---|
| `.dt.year` | Year as integer | 2025 |
| `.dt.month` | Month (1-12) | 1 |
| `.dt.day` | Day of month | 15 |
| `.dt.dayofweek` | Day of week (0=Mon) | 2 |
| `.dt.hour` | Hour (0-23) | 14 |

#### 2. Live Lecture Code Blueprint

```python
import pandas as pd

df = pd.DataFrame({
    "timestamp": pd.date_range("2025-01-01", periods=5, freq="D"),
    "value": [10, 20, 30, 40, 50]
})
df["year"] = df["timestamp"].dt.year
df["month"] = df["timestamp"].dt.month
df["day"] = df["timestamp"].dt.day
df["day_name"] = df["timestamp"].dt.day_name()
print(df)
```

---

### 9.4 Chronological Slicing

> **Lecture Core Insight:** Filter by date range. With a DatetimeIndex, you can use string-based slicing: `df["2025-01"]` selects January 2025. `df["2025-01-01":"2025-01-15"]` selects a range. No boolean masks needed.

#### 1. Engine Rules & Mechanics

- Partial string match: `df["2025-01"]` = all of January 2025
- Range slicing: `df["2025-01-01":"2025-01-15"]` inclusive on both ends
- Year: `df["2025"]` = all of 2025
- Requires sorted DatetimeIndex

#### 2. Live Lecture Code Blueprint

```python
import pandas as pd
import numpy as np

rng = np.random.default_rng(42)
dates = pd.date_range("2025-01-01", "2025-12-31", freq="D")
df = pd.DataFrame({"sales": rng.uniform(100, 500, len(dates))}, index=dates)

# All of January
jan = df["2025-01"]
print(f"January rows: {len(jan)}")

# Specific range
range_slice = df["2025-03-15":"2025-04-15"]
print(f"Range rows: {len(range_slice)}")
```

---

### 9.5 Temporal Resampling — `.resample()`

> **Lecture Core Insight:** Change frequency. `.resample()` aggregates time series data to a lower frequency (e.g., daily to monthly). It's like `groupby` for time-based bins.

#### 1. Engine Rules & Mechanics

| Rule | Frequency | Example |
|---|---|---|
| `"D"` | Daily | Resample daily to weekly |
| `"W"` | Weekly (ends Sunday) | `df.resample("W").mean()` |
| `"M"` | Month end | `df.resample("M").sum()` |
| `"Q"` | Quarter end | `df.resample("Q").mean()` |
| `"Y"` | Year end | `df.resample("Y").sum()` |

#### 2. Live Lecture Code Blueprint

```python
import pandas as pd
import numpy as np

rng = np.random.default_rng(42)
dates = pd.date_range("2025-01-01", "2025-12-31", freq="D")
daily = pd.DataFrame({"sales": rng.uniform(100, 500, len(dates))}, index=dates)

# Monthly totals
monthly = daily.resample("M").sum()
print(monthly.head())

# Quarterly averages
quarterly = daily.resample("Q").mean()
print(quarterly)
```

---

### 9.6 Lag Engineering — `.shift()`

> **Lecture Core Insight:** Create lagged features. `.shift()` moves data forward or backward along the time axis. Positive shifts create lag features (yesterday's value); negative shifts create lead features (tomorrow's value).

#### 1. Engine Rules & Mechanics

| Shift | Effect | Use Case |
|---|---|---|
| `.shift(1)` | Previous value | Lag-1 feature for time series models |
| `.shift(-1)` | Next value | Lead feature (what comes next) |
| `.diff()` | Value - previous value | Stationarity transformation |
| `.pct_change()` | Percent change from previous | Return calculation |

#### 2. Live Lecture Code Blueprint

```python
import pandas as pd

stock = pd.DataFrame({
    "date": pd.date_range("2025-01-01", periods=10, freq="D"),
    "price": [100, 102, 101, 105, 108, 107, 110, 112, 115, 113]
})
stock["price_lag1"] = stock["price"].shift(1)
stock["daily_return"] = stock["price"].pct_change()
stock["price_diff"] = stock["price"].diff()
print(stock)
```

---

### 9.7 Window Computations — `.rolling()` / `.expanding()`

> **Lecture Core Insight:** Streaming statistics. `.rolling(n)` calculates metrics over a sliding window of n observations. `.expanding()` uses all data up to the current point. Essential for smoothing and trend detection.

#### 1. Engine Rules & Mechanics

| Method | Window Type | Example |
|---|---|---|
| `.rolling(window=7).mean()` | Sliding window of 7 | 7-day moving average |
| `.rolling(7, min_periods=1)` | Allow partial windows | Handle beginning of series |
| `.expanding().mean()` | Cumulative from start | Running average |

#### 2. Live Lecture Code Blueprint

```python
import pandas as pd
import numpy as np

rng = np.random.default_rng(42)
dates = pd.date_range("2025-01-01", periods=30, freq="D")
df = pd.DataFrame({"value": 100 + rng.normal(0, 5, 30).cumsum()}, index=dates)

df["ma_7"] = df["value"].rolling(window=7).mean()
df["expanding_mean"] = df["value"].expanding().mean()
print(df.head(10))
```

---

### 9.8 Localization & Time Zones

> **Lecture Core Insight:** Time zone control. `.tz_localize()` assigns a time zone to naive timestamps. `.tz_convert()` converts between time zones. Essential for global data pipelines with users in multiple regions.

#### 1. Engine Rules & Mechanics

- `.tz_localize("UTC")` — assign time zone to naive timestamps
- `.tz_convert("US/Eastern")` — convert from one time zone to another
- Timestamps must be naive before localizing, aware before converting
- Common zones: `"UTC"`, `"US/Eastern"`, `"US/Pacific"`, `"Europe/London"`

#### 2. Live Lecture Code Blueprint

```python
import pandas as pd

# Naive timestamps
ts = pd.Series(pd.date_range("2025-01-01 12:00", periods=3, freq="h"))
print("Naive:", ts)

# Localize to UTC
ts_utc = ts.dt.tz_localize("UTC")
print("UTC:", ts_utc)

# Convert to Eastern Time
ts_eastern = ts_utc.dt.tz_convert("US/Eastern")
print("Eastern:", ts_eastern)
```

---

## Module 10: Input & Output

---

### 10.1 CSV Ingestion — `pd.read_csv()`

> **Lecture Core Insight:** The most common import. `pd.read_csv()` is the workhorse of data ingestion. It handles headers, delimiters, encoding, missing values, and type inference — but you often need to tweak parameters for real-world files.

#### 1. Engine Rules & Mechanics

| Parameter | Effect |
|---|---|
| `filepath_or_buffer` | Path, URL, or file-like object |
| `sep=","` | Delimiter (use `"\t"` for TSV) |
| `header=0` | Row to use as column names (None = no header) |
| `index_col` | Column(s) to use as row index |
| `usecols` | Column subset to load (saves memory) |
| `dtype={"col": "int32"}` | Explicit dtypes (saves memory) |
| `parse_dates=["col"]` | Auto-parse date columns |
| `na_values=["NA", "?"]` | Extra strings to treat as NaN |
| `encoding="utf-8"` | File encoding (try `"latin1"` for Windows files) |

#### 2. Live Lecture Code Blueprint

```python
import pandas as pd

# Reading a CSV with common options
df = pd.read_csv(
    "sales_data.csv",
    sep=",",
    header=0,
    index_col="order_id",
    parse_dates=["order_date"],
    dtype={"customer_id": "Int32", "amount": "float32"},
    na_values=["NA", "Missing", ""]
)
print(df.info())
```

---

### 10.2 Spreadsheet Ingestion — `pd.read_excel()`

> **Lecture Core Insight:** Business workbooks. `pd.read_excel()` reads Excel files (.xlsx, .xls). It can read specific sheets, cell ranges, and handle merged cells. Requires `openpyxl` (for .xlsx) or `xlrd` (for .xls).

#### 1. Engine Rules & Mechanics

| Parameter | Effect |
|---|---|
| `sheet_name="Sheet1"` | Sheet to read (can be name, index, or list) |
| `header=0` | Row to use as column names |
| `usecols="A:C"` | Column range (Excel-style lettering) |
| `skiprows=n` | Skip first n rows |
| `engine="openpyxl"` | Engine for .xlsx files |

#### 2. Live Lecture Code Blueprint

```python
import pandas as pd

# Read a specific sheet (requires openpyxl)
df = pd.read_excel(
    "quarterly_report.xlsx",
    sheet_name="Sales Data",
    header=0,
    usecols="A:F",
    parse_dates=["Date"]
)
print(df.columns.tolist())

# Read all sheets into a dict of DataFrames
all_sheets = pd.read_excel("workbook.xlsx", sheet_name=None)
print(f"Sheets: {list(all_sheets.keys())}")
```

---

### 10.3 Modern Binary Formats — JSON & Parquet

> **Lecture Core Insight:** Fast, dense formats. JSON is ubiquitous for web APIs. Parquet is a columnar binary format that is 10-100x faster for large datasets and compresses extremely well.

#### 1. Engine Rules & Mechanics

| Format | Method | Best For |
|---|---|---|
| JSON | `pd.read_json()` | Web APIs, logs, flexible schemas |
| JSON Lines | `pd.read_json(lines=True)` | Streaming/line-delimited JSON |
| Parquet | `pd.read_parquet()` | Large analytical datasets, columnar access |

#### 2. Live Lecture Code Blueprint

```python
import pandas as pd

# Example with JSON string
json_str = '[{"id": 1, "name": "Alice"}, {"id": 2, "name": "Bob"}]'
df_json = pd.read_json(json_str)
print(df_json)

# Reading Parquet (requires pyarrow or fastparquet)
# df = pd.read_parquet("analytics.parquet")
```

---

### 10.4 Persistent Exporting

> **Lecture Core Insight:** Saving results. Export cleaned DataFrames to CSV (universal), Excel (business reporting), or Parquet (fast, compressed storage for pipelines).

#### 1. Engine Rules & Mechanics

| Method | Key Parameters |
|---|---|
| `df.to_csv("file.csv")` | `index=False`, `encoding="utf-8"`, `sep=","` |
| `df.to_excel("file.xlsx")` | `sheet_name="Sheet1"`, `index=False` |
| `df.to_parquet("file.parquet")` | `compression="snappy"`, `index=False` |
| `df.to_json("file.json")` | `orient="records"`, `lines=True` |

#### 2. Live Lecture Code Blueprint

```python
import pandas as pd

df = pd.DataFrame({
    "id": [1, 2, 3],
    "name": ["Alice", "Bob", "Charlie"],
    "score": [95.5, 88.0, 72.3]
})

# Export to CSV (most common)
df.to_csv("results.csv", index=False, encoding="utf-8")

# Export to JSON
df.to_json("results.json", orient="records", indent=2)
print("Exported to CSV and JSON")
```

---

### 10.5 Ingestion Debugging

> **Lecture Core Insight:** When read fails. Real-world files have bad delimiters, wrong encoding, header errors, and unparsed null fields. These parameters fix most ingestion issues.

#### 1. Engine Rules & Mechanics

| Problem | Solution |
|---|---|
| Wrong delimiter | `sep=";"` or `delim_whitespace=True` |
| Encoding errors | `encoding="latin1"` or `encoding="cp1252"` |
| No header row | `header=None, names=["a","b","c"]` |
| Extra whitespace | `skipinitialspace=True` |
| Wrong null markers | `na_values=["N/A", "null", "-"]` |
| Comments in file | `comment="#"` |
| Quoting issues | `quoting=1` (csv.QUOTE_ALL) |

#### 2. Live Lecture Code Blueprint

```python
import pandas as pd

# Common troubleshooting combo
# df = pd.read_csv(
#     "messy_file.csv",
#     sep=";",
#     encoding="latin1",
#     header=None,
#     names=["id", "name", "date", "amount"],
#     na_values=["N/A", "null", "-"],
#     skipinitialspace=True,
#     parse_dates=["date"]
# )
print("See commented code above for troubleshooting patterns")
```

---

### 10.6 Large File Strategies

> **Lecture Core Insight:** Handling big data. When files don't fit in memory: load only needed columns (`usecols`), use chunked reading (`chunksize`), specify explicit dtypes to save memory, or use `low_memory=False` to avoid mixed type inference.

#### 1. Engine Rules & Mechanics

| Strategy | Effect |
|---|---|
| `usecols=["id", "value"]` | Only load needed columns — huge memory savings |
| `dtype={"col": "int32"}` | Use smaller dtypes (int32 vs int64) |
| `chunksize=10000` | Return iterator yielding DataFrames of 10k rows each |
| `low_memory=False` | Read whole file to infer types (avoid mixed type warnings) |

#### 2. Live Lecture Code Blueprint

```python
import pandas as pd

# Chunked reading — process in pieces
# chunks = pd.read_csv("huge_file.csv", chunksize=50000, usecols=["id", "value"])
# results = []
# for chunk in chunks:
#     results.append(chunk.groupby("id").sum())
# final = pd.concat(results).groupby(level=0).sum()

# Memory-efficient reading
# df = pd.read_csv(
#     "large_dataset.csv",
#     usecols=["customer_id", "amount", "date"],
#     dtype={"customer_id": "Int32", "amount": "float32"},
#     parse_dates=["date"]
# )
print("Chunked processing pattern shown above")
```

---

## Module 11: Visualization

---

### 11.1 Embedded Engine — `.plot()`

> **Lecture Core Insight:** Instant plots from DataFrames. `df.plot()` wraps Matplotlib and produces quick charts directly from your data — no manual figure/axis setup needed. It's the fastest way to visually explore data.

#### 1. Engine Rules & Mechanics

| Parameter | Effect |
|---|---|
| `kind="line"` | Chart type (default) |
| `x="col", y="col"` | Columns for axes |
| `figsize=(w, h)` | Figure dimensions in inches |
| `title="text"` | Chart title |
| `grid=True` | Show grid lines |

#### 2. Live Lecture Code Blueprint

```python
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt

rng = np.random.default_rng(42)
dates = pd.date_range("2025-01-01", periods=50, freq="D")
df = pd.DataFrame({
    "date": dates,
    "sales": 100 + rng.normal(0, 10, 50).cumsum(),
    "expenses": 80 + rng.normal(0, 5, 50).cumsum()
}).set_index("date")

# Quick line plot
df.plot(kind="line", figsize=(10, 5), title="Sales vs Expenses", grid=True)
plt.tight_layout()
plt.show()
```

---

### 11.2 Chart Typologies

> **Lecture Core Insight:** Match chart to data. `.plot()` supports `line`, `bar`, `hist`, `box`, `scatter`, `area`, `pie`, and `kde`. Each reveals different aspects of your data.

#### 1. Engine Rules & Mechanics

| kind | Best For |
|---|---|
| `"line"` | Time series, trends |
| `"bar"` | Categorical comparisons, rankings |
| `"hist"` | Distribution of numeric values |
| `"box"` | Outlier detection, spread comparison |
| `"scatter"` | Relationship between two numeric variables |
| `"area"` | Stacked composition over time |

#### 2. Live Lecture Code Blueprint

```python
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt

rng = np.random.default_rng(42)
df = pd.DataFrame({
    "category": ["A", "B", "C", "D"],
    "values": [10, 25, 15, 30],
    "scores": rng.normal(50, 15, 100)
})

# Bar chart
df.plot(kind="bar", x="category", y="values", title="Category Totals")
plt.show()

# Histogram
df["scores"].plot(kind="hist", bins=20, title="Score Distribution")
plt.show()
```

---

### 11.3 Framework Selection

> **Lecture Core Insight:** Pandas vs Matplotlib vs Seaborn. Use `.plot()` for quick exploration. Use **Matplotlib** directly when you need fine-grained control. Use **Seaborn** for statistical visualizations and beautiful defaults with less code.

#### 1. Engine Rules & Mechanics

| Library | When to Use | Pros |
|---|---|---|
| Pandas `.plot()` | Quick EDA, single-line charts | Zero setup, DataFrame-native |
| Matplotlib | Custom layouts, publication quality | Complete control over every element |
| Seaborn | Statistical plots, multi-faceted data | Beautiful defaults, handles groupby |

#### 2. Live Lecture Code Blueprint

```python
import pandas as pd
import matplotlib.pyplot as plt
import seaborn as sns

rng = np.random.default_rng(42)
df = pd.DataFrame({
    "dept": rng.choice(["Eng", "Sales", "HR"], 100),
    "salary": rng.integers(40000, 150000, 100)
})

# Pandas quick plot
df.boxplot(column="salary", by="dept")
plt.suptitle("")
plt.show()

# Seaborn (more polished, requires import)
sns.boxplot(data=df, x="dept", y="salary")
plt.title("Salary Distribution by Department")
plt.show()
```

---

### 11.4 Visual Auditing

> **Lecture Core Insight:** Data quality through visuals. Use native visualizations to flag distribution shapes, identify outliers, track missing values, and understand data patterns quickly before diving into statistics.

#### 1. Engine Rules & Mechanics

- **Histogram** — detect skew, multimodality, outliers
- **Box plot** — find outliers (points beyond whiskers)
- **Heatmap** — visualize missing data patterns
- **Scatter plot** — detect relationships and anomalies
- **Time series plot** — spot trends, seasonality, breaks

#### 2. Live Lecture Code Blueprint

```python
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt

rng = np.random.default_rng(42)
df = pd.DataFrame({
    "normal": rng.normal(50, 10, 1000),
    "skewed": rng.exponential(2, 1000),
    "has_outliers": np.concatenate([
        rng.normal(50, 5, 990),
        [150, 200, 250, 300, 350, 400, 450, 500, 550, 600]
    ])
})

fig, axes = plt.subplots(1, 3, figsize=(15, 4))
df["normal"].plot(kind="hist", bins=30, ax=axes[0], title="Normal")
df["skewed"].plot(kind="hist", bins=30, ax=axes[1], title="Skewed")
df["has_outliers"].plot(kind="box", ax=axes[2], title="With Outliers")
plt.tight_layout()
plt.show()
print("Visual auditing reveals distributions and outliers at a glance")
```

---

## Module 12: Performance

---

### 12.1 Loop Avoidance

> **Lecture Core Insight:** Loops are death. Python `for` loops over DataFrames are 10-100x slower than vectorized operations. Every loop over rows is a missed opportunity for C-speed execution. The golden rule: if you're writing a loop in Pandas, stop and find the vectorized way.

#### 1. Engine Rules & Mechanics

| Approach | Relative Speed | Notes |
|---|---|---|
| Vectorized (`df["a"] + df["b"]`) | 1x (fastest) | Runs C-level NumPy loops |
| `.apply()` | ~10-50x slower | Python loop per row/column |
| `.iterrows()` | ~100-500x slower | Extremely slow — avoid |
| Python `for` loop | ~100-1000x slower | Multiple Python overheads per iteration |

#### 2. Live Lecture Code Blueprint

```python
import pandas as pd
import numpy as np
import time

n = 1_000_000
df = pd.DataFrame({
    "a": np.random.uniform(0, 100, n),
    "b": np.random.uniform(0, 100, n)
})

# Vectorized — 1x speed
t0 = time.perf_counter()
res_vec = df["a"] * df["b"] + df["a"]
t1 = time.perf_counter()

# .iterrows() — extremely slow
t2 = time.perf_counter()
res_iter = []
for idx, row in df.iterrows():
    res_iter.append(row["a"] * row["b"] + row["a"])
t3 = time.perf_counter()

print(f"Vectorized: {t1-t0:.4f}s | iterrows: {t3-t2:.4f}s | Ratio: {(t3-t2)/(t1-t0):.0f}x")
```

---

### 12.2 Optimization Rules

> **Lecture Core Insight:** Minimize overhead. The fastest code is no code at all — use built-in methods. `.apply()` is convenient but expensive. Filter first, then compute (reduce data before operations). Use categoricals for strings.

#### 1. Engine Rules & Mechanics

- **Prefer built-in methods** — `.sum()` over `.apply(sum)`
- **Filter before compute:** operate on subsets, not full DataFrames
- **Use categoricals:** `astype("category")` for low-cardinality strings
- **Avoid `.apply()` with axis=1:** ~50x slower than vectorized column ops
- **Use `inplace=False` (default):** no performance benefit to inplace=True
- **NumPy functions** like `np.where()`, `np.select()` are faster than `.apply()`

#### 2. Live Lecture Code Blueprint

```python
import pandas as pd
import numpy as np
import time

n = 100_000
df = pd.DataFrame({
    "category": np.random.choice(["A", "B", "C"], n),
    "value": np.random.uniform(0, 100, n)
})

# SLOW: apply with lambda
t0 = time.perf_counter()
df["result_slow"] = df.apply(lambda r: r["value"] * 2 if r["category"] == "A" else r["value"], axis=1)
t1 = time.perf_counter()

# FAST: vectorized with np.where
t2 = time.perf_counter()
df["result_fast"] = np.where(df["category"] == "A", df["value"] * 2, df["value"])
t3 = time.perf_counter()

print(f"apply: {t1-t0:.4f}s | np.where: {t3-t2:.4f}s | Speedup: {(t1-t0)/(t3-t2):.0f}x")
```

---

### 12.3 Memory Auditing — `.memory_usage()`

> **Lecture Core Insight:** Know your memory. `df.memory_usage(deep=True)` reveals memory consumption per column. Use it to identify memory hogs — typically `object` columns with strings.

#### 1. Engine Rules & Mechanics

- `.memory_usage()` — shallow memory (excludes strings in object columns)
- `.memory_usage(deep=True)` — deep memory (includes string contents)
- `.info(memory_usage="deep")` — `.info()` with deep memory report
- Object columns can use 10-100x more memory with `deep=True`

#### 2. Live Lecture Code Blueprint

```python
import pandas as pd
import numpy as np

df = pd.DataFrame({
    "id": range(10000),
    "name": [f"User_{i}" for i in range(10000)],
    "score": np.random.uniform(0, 100, 10000)
})
print("Shallow:")
print(df.memory_usage())
print("\nDeep:")
print(df.memory_usage(deep=True))
print(f"\nTotal shallow: {df.memory_usage().sum() / 1024:.1f} KB")
print(f"Total deep: {df.memory_usage(deep=True).sum() / 1024:.1f} KB")
```

---

### 12.4 Storage Minimization

> **Lecture Core Insight:** Smaller dtypes = less memory. Drastically reduce DataFrame footprints by using smaller numeric types (`int32`, `float32`), categoricals for low-cardinality strings, and nullable types when needed.

#### 1. Engine Rules & Mechanics

| Optimization | Memory Saved | Example |
|---|---|---|
| int64 → int32 | 50% | `df.astype({"id": "int32"})` |
| float64 → float32 | 50% | Often sufficient precision |
| object → category | ~90% for low-cardinality | `df["dept"].astype("category")` |
| object → string | ~30-50% | Modern string dtype is denser |

#### 2. Live Lecture Code Blueprint

```python
import pandas as pd
import numpy as np

n = 100000
big = pd.DataFrame({
    "id": range(n),
    "category": np.random.choice(["Electronics", "Clothing", "Home", "Books", "Sports"], n),
    "price": np.random.uniform(5, 500, n),
    "quantity": np.random.randint(1, 100, n, dtype=np.int32)
})
mem_before = big.memory_usage(deep=True).sum()

# Optimize
big["id"] = big["id"].astype("int32")
big["category"] = big["category"].astype("category")
big["price"] = big["price"].astype("float32")

mem_after = big.memory_usage(deep=True).sum()
print(f"Before: {mem_before/1024:.1f} KB | After: {mem_after/1024:.1f} KB | Saved: {(mem_before-mem_after)/1024:.1f} KB")
```

---

### 12.5 Scale Architecture

> **Lecture Core Insight:** Beyond RAM. When data exceeds physical memory: use chunked processing, specify column dtypes, sample first, or use libraries like Dask/Modin for out-of-core processing. Always profile memory before scaling.

#### 1. Engine Rules & Mechanics

- **Profile first:** `.info(memory_usage="deep")` to estimate full size
- **Load less:** `usecols` to skip unnecessary columns
- **Chunk it:** `chunksize=50000` for streaming processing
- **Sample during exploration:** `pd.read_csv(..., nrows=10000)` for EDA
- **Downcast aggressively:** smaller dtypes = 2-4x memory savings
- **Consider alternatives:** Dask, Polars, or Modin for truly big data

#### 2. Live Lecture Code Blueprint

```python
import pandas as pd

# Scale strategy: profile, then load efficiently
#
# 1. Sample first to understand schema
# sample = pd.read_csv("big_file.csv", nrows=1000)
# print(sample.info())
#
# 2. Load with optimized types
# df = pd.read_csv(
#     "big_file.csv",
#     usecols=["id", "category", "value"],
#     dtype={"id": "int32", "category": "category", "value": "float32"},
#     parse_dates=["date"] if needed
# )
#
# 3. Or process in chunks
# chunks = pd.read_csv("big_file.csv", chunksize=100000)
# results = [chunk.groupby("category").sum() for chunk in chunks]
# final = pd.concat(results).groupby(level=0).sum()

print("Scale architecture pattern — sample, optimize, chunk")
```

---

### 12.6 Copy-on-Write Paradigm

> **Lecture Core Insight:** CoW changes memory behavior. In Pandas 3.0+, Copy-on-Write is default. Selection operations don't copy data until you modify the result. This saves memory and avoids subtle bugs, but changes how you think about assignments.

#### 1. Engine Rules & Mechanics

- Selection creates a **lazy reference** — no data is copied until you write
- Modifying a selection does **not** affect the original (unlike NumPy views)
- `df[df["x"] > 0]["y"] = 5` — silent no-op under CoW
- Use `.copy()` explicitly when you need an independent copy
- CoW eliminates `SettingWithCopyWarning` entirely
- Disable with: `pd.set_option("mode.copy_on_write", False)`

#### 2. Live Lecture Code Blueprint

```python
import pandas as pd

# CoW behavior in Pandas 3.0+
df = pd.DataFrame({"x": [1, 2, 3], "y": [10, 20, 30]})

# Create a subset — no copy happens yet (lazy)
subset = df[["x"]]

# Modify the subset — triggers a copy, original IS NOT modified
subset["x"] = [100, 200, 300]

print("Original df unchanged:")
print(df)
print("\nSubset is independent:")
print(subset)
```

---

## Module 13: Edge Cases

---

### 13.1 Assignment Gotchas — Avoiding `SettingWithCopy`

> **Lecture Core Insight:** Master assignments to avoid bugs. `SettingWithCopyWarning` plagued older Pandas versions. With CoW (Pandas 3.0+), chained assignments silently do nothing. The fix is always the same: use `.loc` for all filtered assignments.

#### 1. Engine Rules & Mechanics

| Pattern | CoW Behavior | Recommendation |
|---|---|---|
| `df.loc[mask, "col"] = val` | Works correctly | **Always use this** |
| `df["col"][mask] = val` | Silent no-op | Never use — chained indexing |
| `df[df["a"] > 0]["b"] = 5` | Silent no-op | Never use — double chained |
| `df["new"] = series` | Works correctly | Fine for new columns |

#### 2. Live Lecture Code Blueprint

```python
import pandas as pd

df = pd.DataFrame({
    "group": ["A", "A", "B", "B", "C"],
    "value": [10, 20, 30, 40, 50]
})

# RIGHT: single .loc target
df.loc[df["group"] == "A", "value"] = 999

# WRONG: chained — does nothing under CoW
# df[df["group"] == "B"]["value"] = 888  # silent no-op!

print(df)
```

---

### 13.2 Automatic Index Alignment

> **Lecture Core Insight:** Pandas aligns on index automatically. When you add, compare, or combine Series with different index labels, Pandas matches values by index — not by position. This is powerful but can cause unexpected NaN results if indices don't match.

#### 1. Engine Rules & Mechanics

- Operations between Series align on index labels, not position
- Labels in one but not the other produce NaN in the result
- This applies to arithmetic, comparisons, and `.merge()`
- Use `.reset_index(drop=True)` to force positional alignment
- Use `.reindex()` to explicitly control alignment

#### 2. Live Lecture Code Blueprint

```python
import pandas as pd

s1 = pd.Series([100, 200, 300], index=["a", "b", "c"])
s2 = pd.Series([10, 20, 30], index=["b", "c", "d"])

# Index alignment: only 'b' and 'c' match, 'a' and 'd' become NaN
result = s1 + s2
print(result)

# Force positional alignment by resetting index
positional = s1.reset_index(drop=True) + s2.reset_index(drop=True)
print("Positional:", positional)
```

---

### 13.3 Nullable Types — `Int64`, `boolean`

> **Lecture Core Insight:** Modern types handle missing data. Traditional `int64` cannot hold NaN. Nullable types like `Int64` (capital I) and `boolean` support missing values natively while preserving the type — no silent conversion to float.

#### 1. Engine Rules & Mechanics

| Type | Nullable? | Missing Value | Conversion |
|---|---|---|---|
| `int64` | No | N/A (float64 forced) | `.astype("int64")` |
| `Int64` | Yes | `pd.NA` | `.astype("Int64")` |
| `bool` | No | N/A | `.astype(bool)` |
| `boolean` | Yes | `pd.NA` | `.astype("boolean")` |
| `string` | Yes | `pd.NA` | `.astype("string")` |

#### 2. Live Lecture Code Blueprint

```python
import pandas as pd
import numpy as np

df = pd.DataFrame({
    "count_legacy": pd.array([1, 2, np.nan, 4]),  # becomes float64
    "count_nullable": pd.array([1, 2, pd.NA, 4], dtype="Int64"),  # stays Int64
    "flag": pd.array([True, False, pd.NA, True], dtype="boolean")
})
print(df.dtypes)
print(df)
```

---

### 13.4 Categorical Nuances

> **Lecture Core Insight:** Categories have quirks. `category` dtype saves memory for low-cardinality strings, but sorting follows category order (not alphabetical by default). Unused categories persist unless dropped. Adding new values requires updating categories.

#### 1. Engine Rules & Mechanics

- **Memory:** stores codes (integers) + categories mapping — huge savings for repeated strings
- **Sorting:** follows category order, not alphabetical — set `ordered=True` for meaningful order
- **Unused categories:** persist after filtering — use `.cat.remove_unused_categories()`
- **Adding values:** new values not in categories become NaN unless you add to categories first
- **Conversion:** `df["col"].astype("category")` automatically creates categories

#### 2. Live Lecture Code Blueprint

```python
import pandas as pd

df = pd.DataFrame({
    "tier": pd.Categorical(
        ["Gold", "Silver", "Gold", "Bronze"],
        categories=["Bronze", "Silver", "Gold", "Platinum"],
        ordered=True
    )
})

print("Sorted by category order:")
print(df.sort_values("tier"))

# Remove unused categories (Platinum isn't in the data)
df["tier"] = df["tier"].cat.remove_unused_categories()
print("\nCategories:", df["tier"].cat.categories.tolist())
```

---

### 13.5 Sparse Representations

> **Lecture Core Insight:** Efficient for sparse data. When most values are the same (often 0 or NaN), sparse storage only stores the non-default values and their positions. This can reduce memory by 10-100x for wide, sparse arrays.

#### 1. Engine Rules & Mechanics

- Convert via: `df.astype(pd.SparseDtype("float64", fill_value=0))`
- Operations work transparently — sparse is an implementation detail
- Best for: one-hot encodings, presence/absence matrices, wide categorical data
- Memory: only stores non-fill values + indices
- Warning: some operations may densify (materialize all values) — check memory

#### 2. Live Lecture Code Blueprint

```python
import pandas as pd
import numpy as np

# Create a wide DataFrame with mostly zeros
n_rows, n_cols = 1000, 100
data = np.zeros((n_rows, n_cols))
# Add some non-zero values (5% density)
rng = np.random.default_rng(42)
non_zero = rng.choice(n_rows * n_cols, size=5000, replace=False)
data.ravel()[non_zero] = 1

dense = pd.DataFrame(data, columns=[f"col_{i}" for i in range(n_cols)])
sparse = dense.astype(pd.SparseDtype("int8", fill_value=0))

mem_dense = dense.memory_usage(deep=True).sum()
mem_sparse = sparse.memory_usage(deep=True).sum()
print(f"Dense: {mem_dense/1024:.1f} KB | Sparse: {mem_sparse/1024:.1f} KB | Ratio: {mem_dense/mem_sparse:.0f}x")
```

---

### 13.6 Workflow Diagnostics — Debugging Pipelines

> **Lecture Core Insight:** Predictable debugging. When a pipeline breaks, follow a systematic diagnostic routine: inspect types, check for nulls, verify index alignment, test with small samples, and use `.pipe()` with logging for complex chains.

#### 1. Engine Rules & Mechanics

- **Check types first:** `df.dtypes` — unexpected object dtypes cause many bugs
- **Check nulls:** `df.isna().sum()` — nulls propagate silently in arithmetic
- **Check index:** `df.index` — duplicate index causes alignment surprises
- **Test with sample:** `df.head(100)` before running on full data
- **Method chaining debug:** insert `.pipe(lambda x: print(x.shape))` or use `.pipe(print)`
- **Reproduce with seed:** `np.random.seed(42)` for deterministic debugging

#### 2. Live Lecture Code Blueprint

```python
import pandas as pd
import numpy as np

# Diagnostic pipeline with debugging checkpoints
def diagnose(df, msg=""):
    print(f"--- {msg} ---")
    print(f"Shape: {df.shape}")
    print(f"Nulls:\n{df.isna().sum()}")
    print(f"Types:\n{df.dtypes}")
    return df

# Build pipeline with diagnostic breaks
rng = np.random.default_rng(42)
raw = pd.DataFrame({
    "id": range(100),
    "group": rng.choice(["A", "B", "C"], 100),
    "value": rng.uniform(10, 100, 100)
})
result = (
    raw
    .pipe(diagnose, msg="After load")
    .groupby("group")
    .agg({"value": ["mean", "std", "count"]})
)
print("\nFinal result:")
print(result)
```
```
