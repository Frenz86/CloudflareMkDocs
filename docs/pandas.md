# this is my pandas 

1. Quick Examples of Drop Rows With Condition in Pandas
If you are in a hurry, below are some quick examples of pandas dropping/removing/deleting rows with condition(s).


## Quick Examples

#Using drop() to delete rows based on column value
df.drop(df[df['Fee'] >= 24000].index, inplace = True)

## Remove rows
df2 = df[df.Fee >= 24000]

# If you have space in column name
# Specify column name with in single quotes
df2 = df[df['column name']]

# Using loc
df2 = df.loc[df["Fee"] >= 24000 ]

# Delect rows based on multiple column value
df2 = df[ (df['Fee'] >= 22000) & (df['Discount'] == 2300)]

# Drop rows with None/NaN
df2 = df[df.Discount.notnull()]
Python
Let’s create a DataFrame with a few rows and columns and execute some examples to learn how to drop the DataFrame rows. Our DataFrame contains column names Courses, Fee, Duration, and Discount.


# Create pandas DataFrame
import pandas as pd
import numpy as np
technologies = {
    'Courses':["Spark","PySpark","Hadoop","Python"],
    'Fee' :[22000,25000,np.nan,24000],
    'Duration':['30day',None,'55days',np.nan],
    'Discount':[1000,2300,1000,np.nan]
          }
df = pd.DataFrame(technologies)
print(df)
Python
Yields below output.


   Courses      Fee Duration  Discount
0    Spark  22000.0    30day    1000.0
1  PySpark  25000.0     None    2300.0
2   Hadoop      NaN   55days    1000.0
3   Python  24000.0      NaN       NaN
Bash
2. Using DataFrame.drop() to Drop Rows with Condition
drop() method takes several params that help you to delete rows from DataFrame by checking condition. When condition expression satisfies it returns True which actually removes the rows.


df.drop(df[df['Fee'] >= 24000].index, inplace = True)
print(df)
Python
Yields below output.


  Courses      Fee Duration  Discount
0   Spark  22000.0    30day    1000.0
2  Hadoop      NaN   55days    1000.0
Bash
After removing rows, it is always recommended to reset the row index.

2. Using loc[] to Drop Rows by Condition