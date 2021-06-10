# Multi-Indexing, or Hierarchical Indexing, and Levels

Sometimes data will be indexed by more than one key. Take this fruit inventory, for example: 

```
>>> data = pd.Series([4, 5, 7, 3, 2, 1, 5, 6], index=[['apple', 'apple', 'apple', 'banana', 'banana', 'berries', 'berries', 'berries'], ['sweet', 'tart', 'sour', 'sweet', 'sweet', 'tart', 'sweet', 'sour']])
>>> data
apple    sweet    4
         tart     5
         sour     7
banana   sweet    3
         sweet    2
berries  tart     1
         sweet    5
         sour     6
dtype: int64
# check out the indexes!
>>> data.index
MultiIndex([(  'apple', 'sweet'),
            (  'apple',  'tart'),
            (  'apple',  'sour'),
            ( 'banana', 'sweet'),
            ( 'banana', 'sweet'),
            ('berries',  'tart'),
            ('berries', 'sweet'),
            ('berries',  'sour')],
           )
>>> 
```
Note the MultiIndex on the Series. We have apples that are sweet, tart, and sour. Bananas that are sweet, but maybe two different varieties? Who knows. And berries that are tart, sweet, and sour. 

What other kinds of situations would it be convenient to use multiple indexes? How about populations? The main index level (level 0) could be the state, and then the next level would be county, and the next level would be city, and you would see the information at the city level. Or district level. Or like in St. Louis, we have wards. You can have as many levels of indexing as you'd like. 

We can also create an object with a MultiIndex with **pd.MultiIndex.from\_tuples()** ([or arrays, etc.](https://pandas.pydata.org/pandas-docs/stable/user_guide/advanced.html)--MultiIndex can be created from a list of arrays, an array of tuples, a set of iterables, or from a DataFrame):

```
>>> groceries = pd.DataFrame(data = {'Price': [2.30, 5.43, 1.20, 3.45, 5.23, 1.22, 2.34, 2.22]}, 
...     index = pd.MultiIndex.from_tuples([
... ('apples', 'sweet'), ('apples', 'tart'), ('apples', 'sour'), ('banana', 'sweet'), ('banana', 'sweet'), ('berries', 'tart'), ('berries', 'sweet'), ('berries', 'sour')], names=['fruit', 'taste']))
>>> groceries
               Price
fruit   taste       
apples  sweet   2.30
        tart    5.43
        sour    1.20
banana  sweet   3.45
        sweet   5.23
berries tart    1.22
        sweet   2.34
        sour    2.22
>>> 
```
This MultiIndex has two levels: Level 0 is the fruit level, and level 1 is the taste level. 

We can remove the MultiIndex and go back to using a RangeIndex with **reset_index()**

```
>>> groceries.reset_index(inplace=True)
>>> groceries
     fruit  taste  Price
0   apples  sweet   2.30
1   apples   tart   5.43
2   apples   sour   1.20
3   banana  sweet   3.45
4   banana  sweet   5.23
5  berries   tart   1.22
6  berries  sweet   2.34
7  berries   sour   2.22
```
Gah! I don't like it. We can recover our MultiIndex with **set_index()**:

```
>>> groceries.set_index(['fruit', 'taste'], inplace=True)
>>> groceries
               Price
fruit   taste       
apples  sweet   2.30
        tart    5.43
        sour    1.20
banana  sweet   3.45
        sweet   5.23
berries tart    1.22
        sweet   2.34
        sour    2.22
>>> 
```
MultiIndex from a list of arrays: 

```
>>> my_arrays = [["car", "car", "car", "train", "train", "train"], ["red", "blue", "green", "blue", "green", "blue"]]
>>> my_multi = pd.MultiIndex.from_arrays(my_arrays, names=("transport", "color"))
>>> my_multi
MultiIndex([(  'car',   'red'),
            (  'car',  'blue'),
            (  'car', 'green'),
            ('train',  'blue'),
            ('train', 'green'),
            ('train',  'blue')],
           names=['transport', 'color'])
>>> my_siris = pd.Series(np.arange(len(my_multi)), index=my_multi)
>>> my_siris
transport  color
car        red      0
           blue     1
           green    2
train      blue     3
           green    4
           blue     5
dtype: int64
>>> 
```
Or like this, with from_tuples():

```
>>> my_arrays = [["car", "car", "car", "train", "train", "train"], ["red", "blue", "green", "blue", "green", "blue"]]
>>> my_tuples = list(zip(*my_arrays))
>>> my_multi_index = pd.MultiIndex.from_tuples(my_tuples, names=["transport", "color"])
>>> my_multi_index
MultiIndex([(  'car',   'red'),
            (  'car',  'blue'),
            (  'car', 'green'),
            ('train',  'blue'),
            ('train', 'green'),
            ('train',  'blue')],
           names=['transport', 'color'])
>>> my_series = pd.Series(np.arange(6), index=my_multi_index)
>>> my_series
transport  color
car        red      0
           blue     1
           green    2
train      blue     3
           green    4
           blue     5
dtype: int64
>>> 
```
Note we had an extra step to zip the arrays. 

[More on Python's zip()](https://towardsdatascience.com/zip-function-in-python-da91c248385d), if interested. 

A MultiIndex from_product() will pair all the possible combinations of the provided iterables:

```
>>> my_its = [["dog", "cat", "fish", "rabbit"], ["a", "ab", "abc"]]
>>> my_mi = pd.MultiIndex.from_product(my_its, names=["pet", "categories"])
>>> my_mi
MultiIndex([(   'dog',   'a'),
            (   'dog',  'ab'),
            (   'dog', 'abc'),
            (   'cat',   'a'),
            (   'cat',  'ab'),
            (   'cat', 'abc'),
            (  'fish',   'a'),
            (  'fish',  'ab'),
            (  'fish', 'abc'),
            ('rabbit',   'a'),
            ('rabbit',  'ab'),
            ('rabbit', 'abc')],
           names=['pet', 'categories'])
>>> my_ser = pd.Series(np.arange(12), index=my_mi)
>>> my_ser
pet     categories
dog     a              0
        ab             1
        abc            2
cat     a              3
        ab             4
        abc            5
fish    a              6
        ab             7
        abc            8
rabbit  a              9
        ab            10
        abc           11
dtype: int64
>>> 
```
Where do you think from "product" came from? What is a product? 

We're multiplying, and the result of multiplication is called a product. So, 4 pets x 3 categories = 12 rows of unique indexes. 


The method **get_level_values()** can retrieve the values based on index location or name:

```
>>> my_multi.get_level_values(1)
Index(['red', 'blue', 'green', 'blue', 'green', 'blue'], dtype='object', name='color')
>>> my_multi.get_level_values('color')
Index(['red', 'blue', 'green', 'blue', 'green', 'blue'], dtype='object', name='color')
>>> 
```
To select rows in a MultiIndex, you can use .loc and pass in a list of tuples:

```
>>> groceries.loc[[('apples', 'sweet'), ('banana', 'sweet')]]
              Price
fruit  taste       
apples sweet   2.30
banana sweet   3.45
       sweet   5.23
>>> 
```
Or return all relevant rows:

```
>>> groceries.loc[['apples']]
              Price
fruit  taste       
apples sweet   2.30
       tart    5.43
       sour    1.20
>>> 
```
And if you wanted to select all rows where all tastes were sweet? There is a method call **xs()** that lets you specify a key and choose the level to search on:

```
>>> groceries.xs(key='sweet', level='taste')
         Price
fruit         
apples    2.30
banana    3.45
banana    5.23
berries   2.34
>>> 
```
SWEET!

Setting drop_level=False lets us keep the fruit and taste indexes in the result. Let's search again for "sweet":

```
>>> groceries.xs(key='sweet', level='taste', drop_level=False)
               Price
fruit   taste       
apples  sweet   2.30
banana  sweet   3.45
        sweet   5.23
berries sweet   2.34
>>> 
```
Yep, same results but we retained the taste index column. 

Either axis can have a hierarchical index, or both:

```
>>> df = pd.DataFrame(np.arange(12).reshape((4, 3)), 
... index = [['apple', 'apple', 'banana', 'banana'], [1, 1, 2, 2]],
... columns=[['red', 'red', 'yellow'], ['up', 'down', 'down']])
>>> df
         red      yellow
          up down   down
apple  1   0    1      2
       1   3    4      5
banana 2   6    7      8
       2   9   10     11
>>> 
>>> df.index
MultiIndex([( 'apple', 1),
            ( 'apple', 1),
            ('banana', 2),
            ('banana', 2)],
           )
>>> df.columns
MultiIndex([(   'red',   'up'),
            (   'red', 'down'),
            ('yellow', 'down')],
           )
>>> 
```

And we can select groups with partial indexing: 

```
# returns data for our one yellow column, and data for down (there is no 'up')
>>> df['yellow']
          down
apple  1     2
       1     5
banana 2     8
       2    11
>>> 
# returns data for our one red column, but data for both up and down
>>> df['red']
          up  down
apple  1   0     1
       1   3     4
banana 2   6     7
       2   9    10
>>> 
```

With .loc[]:

```
>>> groceries.loc[('apples', 'sweet')]
<stdin>:1: PerformanceWarning: indexing past lexsort depth may impact performance.
              Price
fruit  taste       
apples sweet    2.3
>>>
```
In theory the above example would list more than one column if .loc[] was called on a dataframe with more than one column. And, as shown below, if there is a specific column of interest, you could pass it:

```
# get a specific column, Price
>>> groceries.loc[('apples', 'sweet'), "Price"]
/Users/me/miniconda3/envs/dapenv/lib/python3.8/site-packages/pandas/core/indexing.py:889: PerformanceWarning: indexing past lexsort depth may impact performance.
  return self._getitem_tuple(key)
fruit   taste
apples  sweet    2.3
Name: Price, dtype: float64
>>>
```

A narrower .loc[] query to select a smaller subset:

```
>>> groceries.loc[[("apples", "sweet"), ("berries", "tart")]]
               Price
fruit   taste       
apples  sweet   2.30
berries tart    1.22
>>> 
```
More partial indexing:

```
>>> groceries['apples':'banana']
              Price
fruit  taste       
apples sweet   2.30
       tart    5.43
       sour    1.20
banana sweet   3.45
       sweet   5.23
>>> 
```
We can name our columns with **.index.names**:

```
>>> df
         red      yellow
          up down   down
apple  1   0    1      2
       1   3    4      5
banana 2   6    7      8
       2   9   10     11
>>> df.index.names = ['fruit', 'quantity']
>>> df
                red      yellow
                 up down   down
fruit  quantity                
apple  1          0    1      2
       1          3    4      5
banana 2          6    7      8
       2          9   10     11
>>> 
```
And we can name our columns with **.columns.names**:

```
>>> df.columns.names = ['color', 'trend']
>>> df
color           red      yellow
trend            up down   down
fruit  quantity                
apple  1          0    1      2
       1          3    4      5
banana 2          6    7      8
       2          9   10     11
>>> 
```
And we still get back the same information when indexing by columns, but with a little bit more info:

```
>>> df['red']
trend            up  down
fruit  quantity          
apple  1          0     1
       1          3     4
banana 2          6     7
       2          9    10
>>> 
```

### Reorder/Sort Index Levels

If you ever need to swap levels, you can do so with the **swaplevel()** method. It takes two level numbers (or names!), returning a new object to you with the levels in each others' places. 

```
>>> df
color           red      yellow
trend            up down   down
fruit  quantity                
apple  1          0    1      2
       1          3    4      5
banana 2          6    7      8
       2          9   10     11
>>> df.swaplevel("fruit", "quantity")
color           red      yellow
trend            up down   down
quantity fruit                 
1        apple    0    1      2
         apple    3    4      5
2        banana   6    7      8
         banana   9   10     11
>>> 
>>> df.swaplevel(0, 1)
color           red      yellow
trend            up down   down
quantity fruit                 
1        apple    0    1      2
         apple    3    4      5
2        banana   6    7      8
         banana   9   10     11
>>> groceries
               Price
fruit   taste       
apples  sweet   2.30
        tart    5.43
        sour    1.20
banana  sweet   3.45
        sweet   5.23
berries tart    1.22
        sweet   2.34
        sour    2.22
>>> groceries.swaplevel('fruit', 'taste')
               Price
taste fruit         
sweet apples    2.30
tart  apples    5.43
sour  apples    1.20
sweet banana    3.45
      banana    5.23
tart  berries   1.22
sweet berries   2.34
sour  berries   2.22
>>> 
```

We can use **sort_index(level)** to sort by level=0 (fruit) or by level=1 (quantity). Our dataframe 'df' is already sorted by level=0, and by level=1 because that's just how it was created:

```
>>> df
color           red      yellow
trend            up down   down
fruit  quantity                
apple  1          0    1      2
       1          3    4      5
banana 2          6    7      8
       2          9   10     11
>>> df.sort_index(level=0)
color           red      yellow
trend            up down   down
fruit  quantity                
apple  1          0    1      2
       1          3    4      5
banana 2          6    7      8
       2          9   10     11
>>> df.sort_index(level=1)
color           red      yellow
trend            up down   down
fruit  quantity                
apple  1          0    1      2
       1          3    4      5
banana 2          6    7      8
       2          9   10     11
```

Same goes for groceries and level=0:

```
>>> groceries.sort_index(level=0)
               Price
fruit   taste       
apples  sour    1.20
        sweet   2.30
        tart    5.43
banana  sweet   3.45
        sweet   5.23
berries sour    2.22
        sweet   2.34
        tart    1.22
>>>
```
Groceries sorted by level=1 sorts by taste, so we get a different view!

```
>>> groceries.sort_index(level=1)
               Price
fruit   taste       
apples  sour    1.20
berries sour    2.22
apples  sweet   2.30
banana  sweet   3.45
        sweet   5.23
berries sweet   2.34
apples  tart    5.43
berries tart    1.22
>>> 
```

Remember: If we use the method **swaplevel()**, the levels will be reversed:

```
>>> groceries.swaplevel(0, 1)
               Price
taste fruit         
sweet apples    2.30
tart  apples    5.43
sour  apples    1.20
sweet banana    3.45
      banana    5.23
tart  berries   1.22
sweet berries   2.34
sour  berries   2.22
>>> 
```
And then we can sort by index on that new view. Here we swapped taste and fruit, and then sort by fruit:

```
>>> groceries.swaplevel(0, 1).sort_index(level=1)
               Price
taste fruit         
sour  apples    1.20
sweet apples    2.30
tart  apples    5.43
sweet banana    3.45
      banana    5.23
sour  berries   2.22
sweet berries   2.34
tart  berries   1.22
>>> 
```

Here we swapped taste and fruit and sorted by taste:

```
>>> groceries.swaplevel(0, 1).sort_index(level=0)
               Price
taste fruit         
sour  apples    1.20
      berries   2.22
sweet apples    2.30
      banana    3.45
      banana    5.23
      berries   2.34
tart  apples    5.43
      berries   1.22
>>> 
```
We can do summary statistics by level, passing in an axis to aggregate on:

```
>>> groceries
               Price
fruit   taste       
apples  sweet   2.30
        tart    5.43
        sour    1.20
banana  sweet   3.45
        sweet   5.23
berries tart    1.22
        sweet   2.34
        sour    2.22
>>> groceries.sum(level=1)
       Price
taste       
sweet  13.32
tart    6.65
sour    3.42
>>> 
>>> groceries.sum(level=0, axis=0)
         Price
fruit         
apples    8.93
banana    8.68
berries   5.78
>>> 
>>> df.sum(level=1)
color    red      yellow
trend     up down   down
quantity                
1          3    5      7
2         15   17     19
>>> df.sum(level='quantity')
color    red      yellow
trend     up down   down
quantity                
1          3    5      7
2         15   17     19
>>>
>>> df.sum(level='color', axis=1)
color            red  yellow
fruit  quantity             
apple  1           1       2
       1           7       5
banana 2          13       8
       2          19      11
>>> 
```


### Group By and Aggregation with MultiIndex

Take a simple dataframe of some zoo info:

```
>>> dataf = pd.DataFrame({
... 'ayy':['elephant', 'giraffe', 'giraffe', 'elephant', 'elephant', 'giraffe', 'elephant'], 
... 'bee':[False, True, False, False, True, True, False],
... 'cee':[4.15, 5.21, 3.34, 7.32, 1.12, 2.11, 3.14],
... 'dee':[34, 45, 23, 43, 31, 15, 51]
... })
>>> dataf
        ayy    bee   cee  dee
0  elephant  False  4.15   34
1   giraffe   True  5.21   45
2   giraffe  False  3.34   23
3  elephant  False  7.32   43
4  elephant   True  1.12   31
5   giraffe   True  2.11   15
6  elephant  False  3.14   51
>>> 
```

We can turn arrange it to be a hierarchical-indexed dataframe with groupby and aggregation methods:

```
>>> zoo = dataf.groupby(by=['ayy', 'bee']).agg({'cee': ['sum'], 'dee': ['mean', 'max']})
>>> zoo
                  cee        dee    
                  sum       mean max
ayy      bee                        
elephant False  14.61  42.666667  51
         True    1.12  31.000000  31
giraffe  False   3.34  23.000000  23
         True    7.32  30.000000  45
>>> 
```

Employ our index and columns attributes to confirm the MultiIndex:

```
>>> zoo.index
MultiIndex([('elephant', False),
            ('elephant',  True),
            ( 'giraffe', False),
            ( 'giraffe',  True)],
           names=['ayy', 'bee'])
>>> zoo.columns
MultiIndex([('cee',  'sum'),
            ('dee', 'mean'),
            ('dee',  'max')],
           )
>>> 
```

Let's change the columns in zoo and create a zoo2:

```
>>> zoo2 = dataf.groupby(by=['ayy', 'bee']).agg({'cee': ['sum'], 'dee': ['sum', 'max']})
>>> zoo2
                  cee  dee    
                  sum  sum max
ayy      bee                  
elephant False  14.61  128  51
         True    1.12   31  31
giraffe  False   3.34   23  23
         True    7.32   60  45
>>> 
```

Our **.xs()** method can be used to isolate the columns with 'sum'. Tell the method to look at axis=1 and level=1:

```
>>> zoo2.xs(key='sum', axis=1, level=1)
                  cee  dee
ayy      bee              
elephant False  14.61  128
         True    1.12   31
giraffe  False   3.34   23
         True    7.32   60
# setting drop_level=False lets us keep the labels
>>> zoo2.xs(key='sum', axis=1, level=1, drop_level=False)
                  cee  dee
                  sum  sum
ayy      bee              
elephant False  14.61  128
         True    1.12   31
giraffe  False   3.34   23
         True    7.32   60
>>> 
```
And then we can select a specific column by passing it in the key:

```
# select specific column
>>> zoo2.xs(key='cee', axis=1, level=0, drop_level=False)
                  cee
                  sum
ayy      bee         
elephant False  14.61
         True    1.12
giraffe  False   3.34
         True    7.32
>>> 
```
