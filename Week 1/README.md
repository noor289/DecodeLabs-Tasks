# Week 01 – Project 1: Data Cleaning & Preparation

## Overview
This project covers the foundation phase of Data Analytics: cleaning a raw dataset to ensure
data integrity before any analysis. The cleaning was performed using Python (Pandas) in a
Jupyter Notebook on an e-commerce orders dataset with 1,200 records and 14 columns.

---

## Dataset
**File:** `Dataset for Data Analytics.xlsx`  
**Records:** 1,200 rows | **Columns:** 14

| Column | Type | Notes |
|---|---|---|
| OrderID | object | Unique identifier |
| Date | datetime64 | Order date |
| CustomerID | object | Customer identifier |
| Product | object | Product name |
| Quantity | int64 | Units ordered |
| UnitPrice | float64 | Price per unit |
| ShippingAddress | object | Delivery address |
| PaymentMethod | object | Payment type |
| OrderStatus | object | Shipped / Cancelled / etc. |
| TrackingNumber | object | Shipment tracker |
| ItemsInCart | int64 | Cart size |
| CouponCode | object | **309 nulls found** |
| ReferralSource | object | Traffic source |
| TotalPrice | float64 | Order total |

---

## Cleaning Steps

### Step 1 — Import Libraries & Load Dataset
```python
import pandas as pd
df = pd.read_excel('Dataset for Data Analytics.xlsx')
```

### Step 2 — Explore the Data
```python
df.shape          # (1200, 14)
df.head()
df.info()
df.describe()
df.isnull().sum()
```
**Finding:** Only `CouponCode` had missing values — **309 nulls** out of 1,200 records.

---

### Step 3 — Handle Missing Values
```python
df['CouponCode'] = df['CouponCode'].fillna('None')
print(df['CouponCode'].isnull().sum())  # Output: 0
```
**Decision:** Filled with the string `"None"` to preserve all 309 records rather than deleting them.

---

### Step 4 — Check & Remove Duplicates
```python
print(df.duplicated().sum())           # Output: 0
print(df['OrderID'].duplicated().sum()) # Output: 0
df = df.drop_duplicates()
df = df.drop_duplicates(subset='OrderID')
```
**Finding:** No duplicate rows or duplicate OrderIDs found.

---

### Step 5 — Standardize Date Format
```python
df['Date'] = pd.to_datetime(df['Date'])
df['Date'] = df['Date'].dt.strftime('%Y-%m-%d')
```
**Result:** All dates converted to ISO 8601 standard (`YYYY-MM-DD`).

---

### Step 6 — Fix Decimal Precision
```python
df['UnitPrice'] = df['UnitPrice'].round(2)
df['TotalPrice'] = df['TotalPrice'].round(2)
```
**Result:** Both price columns standardized to 2 decimal places.

---

### Step 7 — Verify TotalPrice Accuracy
```python
df['Check'] = (df['Quantity'] * df['UnitPrice']).round(2)
mismatches = df[abs(df['Check'] - df['TotalPrice']) > 0.01]
print("Mismatches found:", len(mismatches))  # Output: 0
df['TotalPrice'] = df['Check']
df = df.drop(columns=['Check'])
```
**Result:** All 1,200 TotalPrice values verified as correct (Quantity × UnitPrice).

---

### Step 8 — Final Verification
```python
print(df.isnull().sum())               # All 0
print(df['OrderID'].duplicated().sum()) # 0
print(df.duplicated().sum())           # 0
print(len(df))                         # 1200
```
**All checks passed. Dataset is production-ready.**

---

### Step 9 — Save Cleaned Dataset
```python
df.to_excel('Cleaned_Dataset_Python.xlsx', index=False)
```
Output saved with two sheets: `Cleaned_Data` and `Change_Log`.

---

## Change Log

| Change ID | Column | Issue | Action | Impact | Status |
|---|---|---|---|---|---|
| CR001 | CouponCode | 309 null values | Filled with `"None"` | Preserved 309 records | ✅ Resolved |
| CR002 | UnitPrice, TotalPrice | Float precision issues | Rounded to 2 decimals | 1,200 records corrected | ✅ Resolved |
| CR003 | Date | Raw datetime format | Standardized to YYYY-MM-DD | 0% date format errors | ✅ Resolved |
| CR004 | TotalPrice | Accuracy check | Recalculated as Qty × UnitPrice | 0 mismatches found | ✅ Verified |
| CR005 | OrderID | Duplicate check | Confirmed all IDs unique | 0 duplicates found | ✅ Verified |

---

## Tools Used
- **Language:** Python 3
- **Libraries:** Pandas, openpyxl
- **Environment:** Jupyter Notebook
