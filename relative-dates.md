# Computing relative dates in pandas


```python
import pandas as pd
import numpy as np
from pandas._testing import makeTimeDataFrame
```


```python
# Dummy dataframe with date as index
count = 10000
f_count = 10
frames = []
for _ in range(f_count):
    frames.append(makeTimeDataFrame(count))
    
df = pd.concat(frames, sort = False)

# Add a random ID-column
df['ID'] = np.random.randint(1, 4, df.shape[0])
# Drop unnecessary columns
df = df.drop(['A', 'B', 'C', 'D'], axis = 1)
df.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>ID</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>2000-01-03</th>
      <td>1</td>
    </tr>
    <tr>
      <th>2000-01-04</th>
      <td>2</td>
    </tr>
    <tr>
      <th>2000-01-05</th>
      <td>2</td>
    </tr>
    <tr>
      <th>2000-01-06</th>
      <td>2</td>
    </tr>
    <tr>
      <th>2000-01-07</th>
      <td>3</td>
    </tr>
  </tbody>
</table>
</div>




```python
# Drop 30% of the rows
df = df.sample(frac = .7)
len(df)
```




    70000




```python
# put the date in it's own column
df['date'] = df.index

# reset the index
df = df.reset_index(drop = True)
df.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>ID</th>
      <th>date</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>2</td>
      <td>2001-08-08</td>
    </tr>
    <tr>
      <th>1</th>
      <td>1</td>
      <td>2032-06-17</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2</td>
      <td>2001-08-02</td>
    </tr>
    <tr>
      <th>3</th>
      <td>2</td>
      <td>2004-11-29</td>
    </tr>
    <tr>
      <th>4</th>
      <td>3</td>
      <td>2022-01-26</td>
    </tr>
  </tbody>
</table>
</div>




```python
df = df.sort_values(by = ['date'])
```


```python
lookup = {}

def do_date_differences(row, col, date_col = 'date'):
    if row[col] in lookup:
        first = lookup[row[col]]
    else:
        first = df.loc[df[col].isin([row[col]])].iloc[0][date_col]
        lookup[row[col]] = first
    second = row[date_col]
    diff = second - first
    return diff


# axis = 1, for rows
df['difference'] = df.apply(lambda row: do_date_differences(row, 'ID', 'date'), axis=1)
df
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>ID</th>
      <th>date</th>
      <th>difference</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>51869</th>
      <td>2</td>
      <td>2000-01-03</td>
      <td>0 days</td>
    </tr>
    <tr>
      <th>3532</th>
      <td>1</td>
      <td>2000-01-03</td>
      <td>0 days</td>
    </tr>
    <tr>
      <th>51257</th>
      <td>1</td>
      <td>2000-01-03</td>
      <td>0 days</td>
    </tr>
    <tr>
      <th>61486</th>
      <td>3</td>
      <td>2000-01-03</td>
      <td>0 days</td>
    </tr>
    <tr>
      <th>24548</th>
      <td>3</td>
      <td>2000-01-03</td>
      <td>0 days</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>24049</th>
      <td>2</td>
      <td>2038-04-30</td>
      <td>13997 days</td>
    </tr>
    <tr>
      <th>47491</th>
      <td>2</td>
      <td>2038-04-30</td>
      <td>13997 days</td>
    </tr>
    <tr>
      <th>53578</th>
      <td>1</td>
      <td>2038-04-30</td>
      <td>13997 days</td>
    </tr>
    <tr>
      <th>60701</th>
      <td>2</td>
      <td>2038-04-30</td>
      <td>13997 days</td>
    </tr>
    <tr>
      <th>40003</th>
      <td>2</td>
      <td>2038-04-30</td>
      <td>13997 days</td>
    </tr>
  </tbody>
</table>
<p>70000 rows × 3 columns</p>
</div>




```python
#lookup
```


```python
df[df['ID'] == 1 ]
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>ID</th>
      <th>date</th>
      <th>difference</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>3532</th>
      <td>1</td>
      <td>2000-01-03</td>
      <td>0 days</td>
    </tr>
    <tr>
      <th>51257</th>
      <td>1</td>
      <td>2000-01-03</td>
      <td>0 days</td>
    </tr>
    <tr>
      <th>104</th>
      <td>1</td>
      <td>2000-01-03</td>
      <td>0 days</td>
    </tr>
    <tr>
      <th>68130</th>
      <td>1</td>
      <td>2000-01-04</td>
      <td>1 days</td>
    </tr>
    <tr>
      <th>7255</th>
      <td>1</td>
      <td>2000-01-04</td>
      <td>1 days</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>28199</th>
      <td>1</td>
      <td>2038-04-29</td>
      <td>13996 days</td>
    </tr>
    <tr>
      <th>57530</th>
      <td>1</td>
      <td>2038-04-30</td>
      <td>13997 days</td>
    </tr>
    <tr>
      <th>22120</th>
      <td>1</td>
      <td>2038-04-30</td>
      <td>13997 days</td>
    </tr>
    <tr>
      <th>33104</th>
      <td>1</td>
      <td>2038-04-30</td>
      <td>13997 days</td>
    </tr>
    <tr>
      <th>53578</th>
      <td>1</td>
      <td>2038-04-30</td>
      <td>13997 days</td>
    </tr>
  </tbody>
</table>
<p>23108 rows × 3 columns</p>
</div>




```python
df[df['difference'] > '15 days']
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>ID</th>
      <th>date</th>
      <th>difference</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>10236</th>
      <td>2</td>
      <td>2000-01-19</td>
      <td>16 days</td>
    </tr>
    <tr>
      <th>4779</th>
      <td>2</td>
      <td>2000-01-19</td>
      <td>16 days</td>
    </tr>
    <tr>
      <th>31512</th>
      <td>1</td>
      <td>2000-01-19</td>
      <td>16 days</td>
    </tr>
    <tr>
      <th>68589</th>
      <td>3</td>
      <td>2000-01-19</td>
      <td>16 days</td>
    </tr>
    <tr>
      <th>50030</th>
      <td>3</td>
      <td>2000-01-19</td>
      <td>16 days</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>24049</th>
      <td>2</td>
      <td>2038-04-30</td>
      <td>13997 days</td>
    </tr>
    <tr>
      <th>47491</th>
      <td>2</td>
      <td>2038-04-30</td>
      <td>13997 days</td>
    </tr>
    <tr>
      <th>53578</th>
      <td>1</td>
      <td>2038-04-30</td>
      <td>13997 days</td>
    </tr>
    <tr>
      <th>60701</th>
      <td>2</td>
      <td>2038-04-30</td>
      <td>13997 days</td>
    </tr>
    <tr>
      <th>40003</th>
      <td>2</td>
      <td>2038-04-30</td>
      <td>13997 days</td>
    </tr>
  </tbody>
</table>
<p>69909 rows × 3 columns</p>
</div>




```python
df.info()
```

    <class 'pandas.core.frame.DataFrame'>
    Int64Index: 70000 entries, 51869 to 40003
    Data columns (total 3 columns):
     #   Column      Non-Null Count  Dtype          
    ---  ------      --------------  -----          
     0   ID          70000 non-null  int64          
     1   date        70000 non-null  datetime64[ns] 
     2   difference  70000 non-null  timedelta64[ns]
    dtypes: datetime64[ns](1), int64(1), timedelta64[ns](1)
    memory usage: 2.1 MB

