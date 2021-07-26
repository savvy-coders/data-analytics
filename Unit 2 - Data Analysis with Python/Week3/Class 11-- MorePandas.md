# Series and DataFrames, cont'd. 

Tonight we'll visit some methods to manipulate Series and DataFrames, but mostly demonstrating with DataFrames. Keep in mind these methods can be used on Series as well. 

## Retrieving Values: Indexing options 

Consider the following DataFrame: 

```
>>> data
{'student': ['Jane', 'Delilah', 'Kyle', 'Sam', 'Elaine', 'Arthur', 'Thomas'], 'grade': [97, 56, 76, 85, 99, 100, 98], 'age': [13, 13, 13, 13, 13, 14, 13]}
>>> df5 = pd.DataFrame(data)
>>> df5
   student  grade  age
0     Jane     97   13
1  Delilah     56   13
2     Kyle     76   13
3      Sam     85   13
4   Elaine     99   13
5   Arthur    100   14
6   Thomas     98   13
```

We briefly looked at .loc[] last class to retrieve a row. Let's expand its use and look at other methods. 

How do we get the name of the student at index 0? We have several options:


```
>>> df5.loc[0]['student'] 
'Jane'
>>> df5.iloc[0][0]
'Jane'
>>> df5.at[0, 'student']
'Jane'
>>> df5.iat[0, 0]
'Jane'
```
Note the subtle differences with each method:

.loc[] works on the labels of your index to access a group of rows/colums.

.iloc[] works on the integer positions in your index to access a group of rows/columns.

.at[] lets us access a single value for a row/column label pair.

.iat[] lest us access a single value for a row/column pair by integer position.


Let's do a quick practice and use the above methods to retrieve the following 2 values: 

1. Thomas's grade

2. Arthur's age

Get help if you need it!

We can use these methods with indexing & slicing!


```
>>> df5.iloc[3, [2]]
age    13
Name: 3, dtype: object
>>> df5.loc[3, ['grade']]
grade    85
Name: 3, dtype: object
>>> df5.loc[:3]
   student  grade  age
0     Jane     97   13
1  Delilah     56   13
2     Kyle     76   13
3      Sam     85   13
>>> df5.loc[df5.age > 12]
   student  grade  age
0     Jane     97   13
1  Delilah     56   13
2     Kyle     76   13
3      Sam     85   13
4   Elaine     99   13
5   Arthur    100   14
6   Thomas     98   13
>>> df5.iloc[:, :2][df5.age > 12]
   student  grade
0     Jane     97
1  Delilah     56
2     Kyle     76
3      Sam     85
4   Elaine     99
5   Arthur    100
6   Thomas     98
>>> df5.iloc[:, :1][df5.age > 12]
   student
0     Jane
1  Delilah
2     Kyle
3      Sam
4   Elaine
5   Arthur
6   Thomas
>>> 
```

We can also use Indexing and Slicing methods as we used with Python, NumPy, and panda Series:

```
>>> data = pd.DataFrame(np.arange(16).reshape(4,4), index=['red', 'yellow', 'orange','green'], columns=['one', 'two', 'three', 'four'])
>>> data
        one  two  three  four
red       0    1      2     3
yellow    4    5      6     7
orange    8    9     10    11
green    12   13     14    15
>>> data['three']
red        2
yellow     6
orange    10
green     14
Name: three, dtype: int64
>>> data[['one', 'four']]
        one  four
red       0     3
yellow    4     7
orange    8    11
green    12    15
# provides row data
>>> data[:3]
        one  two  three  four
red       0    1      2     3
yellow    4    5      6     7
orange    8    9     10    11
# provides column data
>>> data['one']
red        0
yellow     4
orange     8
green     12
Name: one, dtype: int64
```

In summary:

**df[val]** Selects a column or sequence of columns from a DataFrame

**df.loc[val]** Selects a single row or subset of rows from a DataFrame by label

**df.loc[:, val]** Selects a single column or subset of columns by label

**df.iloc[val1, val2]** Selects both rows and columns by label

**df.iloc[where]** Selects single row or subset of rows from a DataFrame by integer position

**df.iloc[:, where]** Selects a single column or subset of coumns by integer position

**df.iloc[where_i, where_j]** Select both rows and columns by integer position

**df.at[label_i, label_j]** Select a single scalar value by row and column label

**df.iat[i, j]** Select a single scalar value by row and column integer positions


As a practical example, let's look at the tricksy problem of eliminating NaN values when performing math operations we saw in our Series lesson:

```
>>> monster = pd.Series([10, 20, 30, 40, 50])
>>> monster
0    10
1    20
2    30
3    40
4    50
dtype: int64
>>> creature = pd.Series([100, 200], index = [4, 3])
>>> creature
4    100
3    200
dtype: int64
>>> monster + creature
0      NaN
1      NaN
2      NaN
3    240.0
4    150.0
dtype: float64
>>> 
```

First, we would select the values of monster contained in the creature Series index values:

```
>>> monster.loc[creature.index]
4    50
3    40
dtype: int64
>>>
```

Then we would += creature to add creature to those values, giving monster new values.

```
>>> monster.loc[creature.index] += creature
>>> print(monster)
0     10
1     20
2     30
3    240
4    150
dtype: int64
>>> 
```

There is also the **values** attribute you can call on a df, which returns a 2D ndarray:

```
>>> df5.values
array([['Jane', 97, 13],
       ['Delilah', 56, 13],
       ['Kyle', 76, 13],
       ['Sam', 85, 13],
       ['Elaine', 99, 13],
       ['Arthur', 100, 14],
       ['Thomas', 98, 13]], dtype=object)
>>> 
```
 

## Retrieving Rows

What if we want all of the info on Jane? She is in row 0, so we can do the following:
 
```
>>> df5.iloc[0]
student    Jane
grade        97
age          13
Name: 0, dtype: object
>>> 
```

Try it yourself, but get the data for Elaine. 

## Retrieving Columns

Of course, if we wanted to do an average of the grades, we would need to gather all the grades, first. Let's do it!

```
>>> df5.loc[:,'grade']
0     97
1     56
2     76
3     85
4     99
5    100
6     98
Name: grade, dtype: int64
>>> 
```
Practice getting column data by getting the names of the students. 

## Set Your Own Index

When we created df5, the index was automatically assigned to be a numerical range spanning the size of your df. In some situations that may not be helpful or convenient. We have the ability to reassign the index column with df.set_index().

```
>>> df5
   student  grade  age
0     Jane     97   13
1  Delilah     56   13
2     Kyle     76   13
3      Sam     85   13
4   Elaine     99   13
5   Arthur    100   14
6   Thomas     98   13
>>> df5_new = df5.set_index('student')
>>> df5_new
         grade  age
student            
Jane        97   13
Delilah     56   13
Kyle        76   13
Sam         85   13
Elaine      99   13
Arthur     100   14
Thomas      98   13
>>>
```

Great news! Setting the index is reversible. Don't like it? Change it back with df.reset_index().

```
>>> df5_new
         grade  age
student            
Jane        97   13
Delilah     56   13
Kyle        76   13
Sam         85   13
Elaine      99   13
Arthur     100   14
Thomas      98   13
>>> df5_new.reset_index()
   student  grade  age
0     Jane     97   13
1  Delilah     56   13
2     Kyle     76   13
3      Sam     85   13
4   Elaine     99   13
5   Arthur    100   14
6   Thomas     98   13
>>> 
```
The method .reindex() allows us to create a new object with the data realigned to the new index: 

```
>>> df5
student info  student  height  grade  age
studentID                                
0                Jane      56     97   13
1             Delilah      62     56   13
2                Kyle      67     76   13
3                 Sam      54     85   13
4              Elaine      78     99   13
5              Arthur      76    100   14
6              Thomas      56     98   13
7               Steve      57     97   14
>>> df5_index = df5.reindex([1, 2, 3, 4, 5, 6, 7, 0])
>>> df5_index
student info  student  height  grade  age
studentID                                
1             Delilah      62     56   13
2                Kyle      67     76   13
3                 Sam      54     85   13
4              Elaine      78     99   13
5              Arthur      76    100   14
6              Thomas      56     98   13
7               Steve      57     97   14
0                Jane      56     97   13
>>> 
```
The columns can be reindexed with the columns keyword:


```
>>> df5_index = df5.reindex(columns = stats)
>>> df5_index
student info  student  grade  age  height
studentID                                
0                Jane     97   13      56
1             Delilah     56   13      62
2                Kyle     76   13      67
3                 Sam     85   13      54
4              Elaine     99   13      78
5              Arthur    100   14      76
6              Thomas     98   13      56
7               Steve     97   14      57
>>> 
```

Want to name your index? We can do that too, with the name attribute.

```
>>> df5
   student  grade  age
0     Jane     97   13
1  Delilah     56   13
2     Kyle     76   13
3      Sam     85   13
4   Elaine     99   13
5   Arthur    100   14
6   Thomas     98   13
>>> df5.index.name = 'studentID'
>>> df5
           student  grade  age
studentID                     
0             Jane     97   13
1          Delilah     56   13
2             Kyle     76   13
3              Sam     85   13
4           Elaine     99   13
5           Arthur    100   14
6           Thomas     98   13
>>> 
```
There is also a name attribute for columns:

```
>>> df5
           student  grade  age
studentID                     
0             Jane     97   13
1          Delilah     56   13
2             Kyle     76   13
3              Sam     85   13
4           Elaine     99   13
5           Arthur    100   14
6           Thomas     98   13
>>> df5.columns.name = 'student info'
>>> df5
student info  student  grade  age
studentID                        
0                Jane     97   13
1             Delilah     56   13
2                Kyle     76   13
3                 Sam     85   13
4              Elaine     99   13
5              Arthur    100   14
6              Thomas     98   13
>>> 
```

## Add a Row

We have a new student, Steve, who also took the test, so we need to add a row to our df. 

```
>>> df5.loc[7] = ['Steve', 97, 14]
>>> df5
student info  student  grade  age
studentID                        
0                Jane     97   13
1             Delilah     56   13
2                Kyle     76   13
3                 Sam     85   13
4              Elaine     99   13
5              Arthur    100   14
6              Thomas     98   13
7               Steve     97   14
>>> 
```

What do you think would happen if we assigned Steve to .loc[2] instead of .loc[7]?

```
>>> df5
student info  student  grade  age
studentID                        
0                Jane     97   13
1             Delilah     56   13
2                Kyle     76   13
3                 Sam     85   13
4              Elaine     99   13
5              Arthur    100   14
6              Thomas     98   13
7               Steve     97   14
>>> df5.loc[2] = ['Steve', 97, 14]
>>> df5
student info  student  grade  age
studentID                        
0                Jane     97   13
1             Delilah     56   13
2               Steve     97   14
3                 Sam     85   13
4              Elaine     99   13
5              Arthur    100   14
6              Thomas     98   13
7               Steve     97   14
>>> 
```
Dangit, we overwrote Kyle's info. We didn't want to do that! I hope we made a backup somewhere... 

There is also an append function to add a row, or an entire df: 

```
>>> df6 = pd.DataFrame({"grade":[87], "age":[13], "student":['Rex'], "height":[76]})
>>> df3
   grade  age  student height
0     97   13     Jane    NaN
1     56   13  Delilah    NaN
2     76   13     Kyle    NaN
3     85   13      Sam    NaN
4     99   13   Elaine    NaN
5    100   14   Arthur    NaN
6     98   13   Thomas    NaN
>>> df6
   grade  age student  height
0     87   13     Rex      76
>>> df3.append(df6)
   grade  age  student height
0     97   13     Jane    NaN
1     56   13  Delilah    NaN
2     76   13     Kyle    NaN
3     85   13      Sam    NaN
4     99   13   Elaine    NaN
5    100   14   Arthur    NaN
6     98   13   Thomas    NaN
0     87   13      Rex     76
>>> 
```
Pay attention to the index when using .append(). You can set "ingore_index" to True to avoid maintaining the index from the original df. In this case, pretend we needed the row to be assigned a new index number. 

```
>>> df3.append(df6, ignore_index=True)
   grade  age  student height
0     97   13     Jane    NaN
1     56   13  Delilah    NaN
2     76   13     Kyle    NaN
3     85   13      Sam    NaN
4     99   13   Elaine    NaN
5    100   14   Arthur    NaN
6     98   13   Thomas    NaN
7     87   13      Rex     76
>>> 
 ```
When we use .append(), columns not in the original df are added as new columns and the new cells are populated with NaN value. 

```
>>> df6 = pd.DataFrame({"grade":[87], "age":[13], "student":['Rex'], "height":[76], "hometown":["St. Louis"]})
>>> df3.append(df6)
   grade  age  student height   hometown
0     97   13     Jane    NaN        NaN
1     56   13  Delilah    NaN        NaN
2     76   13     Kyle    NaN        NaN
3     85   13      Sam    NaN        NaN
4     99   13   Elaine    NaN        NaN
5    100   14   Arthur    NaN        NaN
6     98   13   Thomas    NaN        NaN
0     87   13      Rex     76  St. Louis
>>> 
```
In a way, we just added a column! But let's look at more efficient ways to do that. 


## Add a column

We can append a column by adding a Series to an existing DataFrame using .loc[]:

```
>>> df5.loc[:,'height'] = pd.Series([56, 62, 67, 54, 78, 76, 56, 57])
>>> df5
student info  student  grade  age  height
studentID                                
0                Jane     97   13      56
1             Delilah     56   13      62
2                Kyle     76   13      67
3                 Sam     85   13      54
4              Elaine     99   13      78
5              Arthur    100   14      76
6              Thomas     98   13      56
7               Steve     97   14      57
>>> 
```
We can declare a list and convert it into a column:

```
>>> hometowns = ['Chicago', 'Minneapolis', 'Cairo', 'Vancouver', 'St. Louis', 'Seattle', 'Carbondale', 'Los Angeles']
>>> df5['birthplace'] = hometowns
>>> df5
student info  student  height  grade  age   birthplace
studentID                                             
0                Jane      56     97   13      Chicago
1             Delilah      62     56   13  Minneapolis
2                Kyle      67     76   13        Cairo
3                 Sam      54     85   13    Vancouver
4              Elaine      78     99   13    St. Louis
5              Arthur      76    100   14      Seattle
6              Thomas      56     98   13   Carbondale
7               Steve      57     97   14  Los Angeles
>>> 
```

We can use .assign() to return a new object with all original columns in addition to new ones:

```
>>> df5_towns = df5.assign(birthplace = hometowns)
>>> df5_towns
student info  student  height  grade  age   birthplace
studentID                                             
0                Jane      56     97   13      Chicago
1             Delilah      62     56   13  Minneapolis
2                Kyle      67     76   13        Cairo
3                 Sam      54     85   13    Vancouver
4              Elaine      78     99   13    St. Louis
5              Arthur      76    100   14      Seattle
6              Thomas      56     98   13   Carbondale
7               Steve      57     97   14  Los Angeles
>>> 
```

If appending (adding it to the end) is not what we want to do, we can instead use df.insert(), and place the column in a desired index. 

```
# insert(loc, column name, data, allow_duplicates)
>>> df5.insert(1, 'height', height, False)
>>> df5
student info  student  height  grade  age
studentID                                
0                Jane      56     97   13
1             Delilah      62     56   13
2                Kyle      67     76   13
3                 Sam      54     85   13
4              Elaine      78     99   13
5              Arthur      76    100   14
6              Thomas      56     98   13
7               Steve      57     97   14
>>> 
```
By far the easiest and most recommended method is to use .loc[], but as you have probably noticed there are multiple ways to accomplish the manipulation you need.

You can also combine columns to form one new column. It wouldn't be a real scenario, but as an example you could combine grade and age to create a column "silly" by doing the following: 

```
>>> df3
   grade  age  student  height
0     97   13     Jane      56
1     56   13  Delilah      57
2     76   13     Kyle      58
3     85   13      Sam      59
4     99   13   Elaine      60
5    100   14   Arthur      61
6     98   13   Thomas      62
>>> df3['silly'] = df3.grade + df3.age
>>> df3
   grade  age  student  height  silly
0     97   13     Jane      56    110
1     56   13  Delilah      57     69
2     76   13     Kyle      58     89
3     85   13      Sam      59     98
4     99   13   Elaine      60    112
5    100   14   Arthur      61    114
6     98   13   Thomas      62    111
>>> 
```

Combining columns to form a new column would provide value in other situations, but not with this dataset. 


## Transpose

Swapping rows and columns is easy, and similar to transposing with NumPy arrays:

```
>>> df
        student  grade  age  height
red        Jane     97   13     NaN
orange  Delilah     56   13     NaN
yellow     Kyle     76   13     NaN
green       Sam     85   13     NaN
blue     Elaine     99   13     NaN
indigo   Arthur    100   14     NaN
violet   Thomas     98   13     NaN
>>> df.T
          red   orange yellow green    blue  indigo  violet
student  Jane  Delilah   Kyle   Sam  Elaine  Arthur  Thomas
grade      97       56     76    85      99     100      98
age        13       13     13    13      13      14      13
height    NaN      NaN    NaN   NaN     NaN     NaN     NaN
>>> 
```
## Drop

Return a view or assign a new object with the indicated value(s) removed from an axis:

```
>>> df5
   student  grade  age
0     Jane     97   13
1  Delilah     56   13
2     Kyle     76   13
3      Sam     85   13
4   Elaine     99   13
5   Arthur    100   14
6   Thomas     98   13
# drop a row
>>> df5.drop(6)
   student  grade  age
0     Jane     97   13
1  Delilah     56   13
2     Kyle     76   13
3      Sam     85   13
4   Elaine     99   13
5   Arthur    100   14
# drop two rows
>>> df5.drop([5, 4])
   student  grade  age
0     Jane     97   13
1  Delilah     56   13
2     Kyle     76   13
3      Sam     85   13
6   Thomas     98   13
>>> 
```
We already discussed how to use **del** to eliminate a column, but we can also use **drop**, when specifying the axis:

```
# drop a column. must specify which axis, or "columns"
>>> df5
   student  grade  age
0     Jane     97   13
1  Delilah     56   13
2     Kyle     76   13
3      Sam     85   13
4   Elaine     99   13
5   Arthur    100   14
6   Thomas     98   13
>>> df5.drop('age', axis=1)
   student  grade
0     Jane     97
1  Delilah     56
2     Kyle     76
3      Sam     85
4   Elaine     99
5   Arthur    100
6   Thomas     98
>>> df5
   student  grade  age
0     Jane     97   13
1  Delilah     56   13
2     Kyle     76   13
3      Sam     85   13
4   Elaine     99   13
5   Arthur    100   14
6   Thomas     98   13
>>> df5.drop('age', axis='columns')
   student  grade
0     Jane     97
1  Delilah     56
2     Kyle     76
3      Sam     85
4   Elaine     99
5   Arthur    100
6   Thomas     98
>>> 
```

## View and Copy
 
Just like working with NumPy, we need to be cognizant of when we are creating views vs. copies with Series and Dataframes. Remember that copies are separate instances of the data, and a view is just creating a way to access or look at the original data. Problems can arise when you modify a view and forget that any modifications cause changes to the original DataFrame.

To demonstrate the nuances, let's begin with a simple DataFrame:

```
>>> dframe = pd.DataFrame({'a':[1, 2, 3, 4, 5], 'b':[10, 20, 30, 40, 50]})
>>> dframe
   a   b
0  1  10
1  2  20
2  3  30
3  4  40
4  5  50
```

We can pull out the Series 'a' and store it in a variable, v1:

```
>>> v1 = dframe['a']
>>> v1
0    1
1    2
2    3
3    4
4    5
Name: a, dtype: int64
>>>
```

If we modify v1, we also modify our original dframe. 

```
>>> v1.iloc[1] = 222
>>> v1
0      1
1    222
2      3
3      4
4      5
Name: a, dtype: int64
>>> dframe
     a   b
0    1  10
1  222  20
2    3  30
3    4  40
4    5  50
>>>
```

Let's extract 'b' and add 1, and assign it to v2 (Our current version of dframe is unchanged.):

```
>>> v2 = dframe['b'] + 1
>>> v2
0    11
1    21
2    31
3    41
4    51
Name: b, dtype: int64
>>> dframe
     a   b
0    1  10
1  222  20
2    3  30
3    4  40
4    5  50
>>>
```

Any change we make to v2 does not affect our dframe values.

```
>>> v2.iloc[0] = 0
>>> v2
0     0
1    21
2    31
3    41
4    51
Name: b, dtype: int64
>>> dframe
     a   b
0    1  10
1  222  20
2    3  30
3    4  40
4    5  50
>>>
```

Next up is to create seemingly identical v1 and v2. First, we will use a list of indices to set v1 equal to the first two rows of dframe:

```
>>> v1 = dframe.iloc[[0, 1]]
>>> v1
     a   b
0    1  10
1  222  20
>>>
```

With v2, we will use slicing to select the first two rows:

```
>>> v2 = dframe.iloc[:2]
>>> v2
     a   b
0    1  10
1  222  20
>>>
```

Let's attempt to modify v1:

```
>>> v1.iloc[1] = 333
/Users/melissapabst/miniconda3/envs/dapenv/lib/python3.8/site-packages/pandas/core/indexing.py:1637: SettingWithCopyWarning: 
A value is trying to be set on a copy of a slice from a DataFrame

See the caveats in the documentation: https://pandas.pydata.org/pandas-docs/stable/user_guide/indexing.html#returning-a-view-versus-a-copy
  self._setitem_single_block(indexer, value, name)
/Users/melissapabst/miniconda3/envs/dapenv/lib/python3.8/site-packages/pandas/core/indexing.py:692: SettingWithCopyWarning: 
A value is trying to be set on a copy of a slice from a DataFrame

See the caveats in the documentation: https://pandas.pydata.org/pandas-docs/stable/user_guide/indexing.html#returning-a-view-versus-a-copy
  iloc._setitem_with_indexer(indexer, value, self.name)
>>> v1
     a    b
0    1   10
1  333  333
>>> dframe
     a   b
0    1  10
1  222  20
2    3  30
3    4  40
4    5  50
>>>
```

OH NO! We got a warning! "A value is trying to be set on a copy of a slice from a DataFrame". Of course, dframe is unchanged because v1 was a COPY. 

Let's modify v2 in the same way: 

```
>>> v2.iloc[1] = 333
/Users/melissapabst/miniconda3/envs/dapenv/lib/python3.8/site-packages/pandas/core/indexing.py:1637: SettingWithCopyWarning: 
A value is trying to be set on a copy of a slice from a DataFrame

See the caveats in the documentation: https://pandas.pydata.org/pandas-docs/stable/user_guide/indexing.html#returning-a-view-versus-a-copy
  self._setitem_single_block(indexer, value, name)
/Users/melissapabst/miniconda3/envs/dapenv/lib/python3.8/site-packages/pandas/core/indexing.py:692: SettingWithCopyWarning: 
A value is trying to be set on a copy of a slice from a DataFrame

See the caveats in the documentation: https://pandas.pydata.org/pandas-docs/stable/user_guide/indexing.html#returning-a-view-versus-a-copy
  iloc._setitem_with_indexer(indexer, value, self.name)
 >>> v2
     a    b
0    1   10
1  333  333
>>> dframe
     a    b
0    1   10
1  333  333
2    3   30
3    4   40
4    5   50
>>> 
```

OH NO! We got a warning again. "A value is trying to be set on a copy of a slice from a DataFrame" BUT this time as v2 was changed, our dframe was also changed. This means v2 is a VIEW, or a reference, and v2 is simply pointing to dframe. 


Using **copy()** explicitly will help us stay on track and only modify a copy. 

```
>>> v3 = dframe.iloc[:2].copy()
>>> v3
     a    b
0    1   10
1  333  333
>>> 
```

You can then modify v3 all you want and it will DEFINITELY not affect the original dframe. 

```
>>> dframe
     a    b
0    1   10
1  333  333
2    3   30
3    4   40
4    5   50
>>> v3.iloc[1] = 444
>>> v3
     a    b
0    1   10
1  444  444
>>> dframe
     a    b
0    1   10
1  333  333
2    3   30
3    4   40
4    5   50
>>> 
```

Our dframe goes unchanged when you modify a copy. If it's a view, well, then I hope you intended to change the data. 



