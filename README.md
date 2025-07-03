# Data Cleaning Project 01
## Learn messy data cleaning with CSV messy data using Python and Pandas library

### Import Libraries
```
import pandas as pd
import numpy as np
import re
import seaborn as sns
import matplotlib.pyplot as plt
from datetime import datetime
import os

# Ensure output directory exists
if not os.path.exists('output'):
    os.makedirs('output')
```
### Import CSV File
```
df = pd.read_csv("Messy_Data.CSV")
df.head()
```
### Data Understanding
```
df.info()
```
### Identify and Handle Missing Data
```
print("Missing values before cleaning:")
print(df.isnull().sum())

# Impute Email with placeholder
df['Email'] = df['Email'].fillna('unknown@example.com')

# Impute Rating with median
df['Rating'] = df['Rating'].fillna(df['Rating'].median())

# Drop rows with missing Price (critical for analysis)
df.dropna(subset=['Price'], inplace=True)

print("\nMissing values after cleaning:")
print(df.isnull().sum())
```
### Remove Duplicates and Handle Outliers
```
# Remove duplicates based on CustomerID and OrderDate
df.drop_duplicates(subset=['CustomerID', 'OrderDate'], keep='first', inplace=True)
print(f"Number of rows after removing duplicates: {len(df)}")

# Handle outliers in Price using IQR

Q1 = df['Price'].quantile(0.25)
Q3 = df['Price'].quantile(0.75)
IQR = Q3 - Q1
lower_bound = Q1 - 1.5 * IQR
upper_bound = Q3 + 1.5 * IQR

# Cap outliers

df['Price'] = df['Price'].clip(lower=0, upper=upper_bound)
print(f"Price outliers capped. New min: {df['Price'].min()}, max: {df['Price'].max()}")
```
### Standardize Data Entries
```
# Standardize CustomerName to title case
df['CustomerName'] = df['CustomerName'].str.title()

# Validate and standardize Email
def validate_email(email):
    pattern = r'^[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,}$'
    return bool(re.match(pattern, email))

df['Email'] = df['Email'].apply(lambda x: x if validate_email(x) else 'invalid@example.com')

# Standardize OrderDate to YYYY-MM-DD
df['OrderDate'] = pd.to_datetime(df['OrderDate'], errors='coerce').dt.strftime('%Y-%m-%d')

# Correct ProductCategory misspellings
category_mapping = {'Electornics': 'Electronics', 'Clothng': 'Clothing'}
df['ProductCategory'] = df['ProductCategory'].replace(category_mapping)
```
### Save the cleaned dataset
```
df.to_csv('Messy_Data_Cleaned.csv', index=False)
print("\nCleaned dataset saved as 'Messy_Data_Cleaned.csv'")
print("\nSample of cleaned dataset:")
```
