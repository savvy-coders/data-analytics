# pandas

![pandas](week2images/pandashi.jpeg)

We're switching gears a bit; NumPy taught us how to do math with ndarrays of single-type data (homogenous) efficiently, and next on our agenda is **pandas**. Pandas is another library for Python and is built on top of NumPy. Pandas is better suited for data of heterogenous types, and data in tables, or **dataframes**, as they call them in pandas. 

Remember MatPlotLib? Well, pandas provides **wrappers** around the matplotlib library, reducing the lines of code needed to use matploblib code. You may recall from your lesson on object-oriented programming the concept of **abstraction**, which aims to hide the bits that aren't needed, effectively reducing the complexity and therefore the lines of code. Well, that's what a wrapper does. You don't need to know how your coffee pot works, but that you can get some hot bean juice if you do x, y, and z steps. A wrapper is like the buttons on the coffee pot. 

Why is it called pandas? I don't mean to de-mystify it, and you can continue to picture in your mind cute little pandas typing away inside your computer, but it comes from *panel data*, an econometrics term you don't hear a lot and is the term for multidimensional structured datasets. (Please stop trying to insert bamboo into your floppy disk drive.)

Pandas has a wide range of capabilities beyond NumPy, especially when it comes to data structures and data manipulations needed to clean and analyze our data, but we'll still be using NumPy here and there. 

Instructions for installation can be found [here](https://pandas.pydata.org/pandas-docs/stable/getting_started/install.html).

And then we start by naming our imports:

```
>>> import pandas as pd
>>> from pandas import Series, DataFrame
```

![ImportPandas](week2images/importpandas.png)

Whenever you see pd, we are referring to the pandas library. I also imported Series and Dataframe into the local namespace because they are frequently used, and now I can call them directly without typing pd.Series:

```
>>> my_obj = Series([4, 7, -2, -7])
>>> my_obj
0    4
1    7
2   -2
3   -7
dtype: int64
>>> my_obj = pd.Series([2, 5, 8, 3])
>>> my_obj
0    2
1    5
2    8
3    3
dtype: int64
```
Feel free to do whatever you prefer after you are comfortable with direct imports like this. But as we discussed before, "np" and "pd" are pretty standard naming conventions for NumPy and pandas. 

![pep8](week2images/PEP8.png)

## pandas Data Structures Part I: Series and Index

What we created above is a **series**, which is a 1D array-like object, similar to a NumPy 1D array, but has an associated array of index labels. Pandas actually stores data by using a NumPy 1D array! The panda Series is for arrays of data of a single type, and can be thought of a column of a spreadsheet. Documentation can be found [here](https://pandas.pydata.org/docs/reference/api/pandas.Series.html).

The most basic build of a series looks like this:

``` 
>>> s = pd.Series(data, index=index)
```
And "data" can be a Python dictionary, an ndarray, or even a scalar value. We can provide attributes such as name, dtype, shape, index, and values when we create a panda Series.

The easiest way to make a Series is from a list, of course. 

Here we have a panda series, with the Series index labels in the left column and the data on the right:

```
>>> my_obj = pd.Series([2, 5, 8, 3, 1, 0])
>>> print(my_obj)
0    2
1    5
2    8
3    3
4    1
5    0
dtype: int64
```

When creating a Series from a list of strings, etc., pay attention to the dtype of the object. Here we created one without specifying the dtype, so it gets read as an object. When we take the time to the tell the Series what data type is stored, it will be beneficial to us in the future. 

```
>>> series_string = pd.Series(['dog', 'cat', 'fish'])
>>> print(series_string)
0     dog
1     cat
2    fish
dtype: object

>>> series_string_2 = pd.Series(['dog', 'cat', 'fish'], dtype='string')
>>> print(series_string_2)
0     dog
1     cat
2    fish
dtype: string
>>> 
```


Numpy is very powerful and can be a building block for pandas, so you can see why it was important to master it first! Here is a Series made from a NumPy array: 

```
>>> import numpy as np
>>> np_series = pd.Series(np.random.randn(5))
>>> np_series
0    2.518028
1    1.018381
2   -0.488189
3   -0.249349
4   -0.166437
dtype: float64
```


Here is a series from a python dictionary:

```
>>> dict = {"r": 2, "d": 2, "c": 3, "p": 0}
>>> dict_series = pd.Series(dict)
>>> dict_series
r    2
d    2
c    3
p    0
dtype: int64
```

The default index label starts at 0, and goes up to "length - 1", but we can specify the label (should we choose) by supplying "index = ". Note: A supplied index must be the same length as the data. 
 
```
 >>> labeled_obj = pd.Series([4, 6, -1, -2], index = ['m', 'e', 't', 'q'])
>>> print(labeled_obj)
m    4
e    6
t   -1
q   -2
dtype: int64
```
When we create a series from a scalar value, the value repeats to the size of the provided index. 

```
# default is one value with one index
>>> pd.Series(5)
0    5
dtype: int64
# we can specify a range to determine the length of the series values
>>> pd.Series(5, index = range(5))
0    5
1    5
2    5
3    5
4    5
dtype: int64
>>> 
# we can define our index
>>> pd.Series(5, index=["a", "b", "c", "d", "e"])
a    5
b    5
c    5
d    5
e    5
dtype: int64
```
With any series, no matter the origin, if we don't like the default index labels, we can change them:

```
>>> s_street = pd.Series(5, index = range(5))
>>> s_street
0    5
1    5
2    5
3    5
4    5
dtype: int64
>>> s_street.index = ['Elmo', 'Cookie Monster', 'Big Bird', 'Oscar', 'Snuffy']
>>> s_street
Elmo              5
Cookie Monster    5
Big Bird          5
Oscar             5
Snuffy            5
dtype: int64
```

We can call the **index** and **values** attributes of each array object we created with methods of corresponding names (as well as many others you are already familiar with: ndim, size, shape, and many you haven't seen). A full list is [here in the documentation](https://pandas.pydata.org/pandas-docs/stable/reference/series.html). Give some a try to get practice:

```
>>> my_obj = pd.Series([2, 5, 8, 3, 1, 0])
>>> my_obj.values
array([2, 5, 8, 3, 1, 0])
>>> my_obj.index
RangeIndex(start=0, stop=6, step=1)

>>> labeled_obj = pd.Series([4, 6, -1, -2], index = ['m', 'e', 't', 'q'])
>>> labeled_obj.values
array([ 4,  6, -1, -2])
>>> labeled_obj.index
Index(['m', 'e', 't', 'q'], dtype='object')
>>> 
```

A clever way to retrieve the values of the index object:

```
>>> my_obj_index = my_obj.index
>>> my_obj_index
RangeIndex(start=0, stop=6, step=1)
>>> my_obj_index.values
array([0, 1, 2, 3, 4, 5])
```
Or we can stack the method calls without assigning the index to a variable (create index object, then call the method on the index object all in one line!):

```
>>> my_obj.index.values
array([0, 1, 2, 3, 4, 5])
```

We can give the Series object and its index object names: 

```
>>> my_obj = pd.Series([2, 5, 8, 3, 1, 0])
>>> my_obj.name = 'MY FIRST PANDA OBJECT!!'
>>> my_obj.index.name = 'PANDA!!'
>>> my_obj
PANDA!!
0    2
1    5
2    8
3    3
4    1
5    0
Name: MY FIRST PANDA OBJECT!!, dtype: int64
>>> 
```

We can check the name, dtype, shape, ndim, size, and uniqueness of index objects as well. [Here is a full list](https://pandas.pydata.org/pandas-docs/stable/reference/indexing.html).

```
>>> my_obj_index.dtype
dtype('int64')
>>> my_obj_index.shape
(6,)
>>> my_obj_index.is_unique
True
>>> my_obj_index.ndim
1
>>> my_obj_index.size
6
# OR by calling methods on the index objects
>>> my_obj.index.shape
(6,)
>>> my_obj.index.size
6
# etc.
```

And just as with NumPy, we can select single values (or a set of values) from the series with indexing:

```
>>> labeled_obj
m    4
e    6
t   -1
q   -2
dtype: int64
>>> labeled_obj['q']
-2
>>> labeled_obj[['q', 'm']]
q   -2
m    4
dtype: int64
>>> labeled_obj[[1, 3]]
e    6
q   -2
dtype: int64
>>> labeled_obj[labeled_obj < 2]
t   -1
q   -2
dtype: int64
>>> labeled_obj[1:2]
e    6
dtype: int64
>>> labeled_obj['e':'q']
e    6
t   -1
q   -2
dtype: int64
>>> my_obj
0    2
1    5
2    8
3    3
4    1
5    0
dtype: int64
>>> my_obj[3]
3
>>> my_obj[[3, 0, 1]]
3    3
0    2
1    5
dtype: int64
```
Indexing isn't the **best** way to do this, as we will see later, but is certainly an option. 

Returning NaN: If we are building a series object from a dictionary, as we did earlier, the resulting series will place the keys in the original order. However, we can play with it. We can rearrange the order to how we want it to display. Notice below that if we put a key in the index that has no value in the dictionary, we get "NaN". 

```
>>> dict = {"r": 2, "d": 2, "c": 3, "p": 0}
>>> dict_series = pd.Series(dict)
>>> dict_series
r    2
d    2
c    3
p    0
dtype: int64
>>> bots = ['r', 'y', 'c', 'p']
>>> new_series = pd.Series(dict, index = bots)
>>> new_series
r    2.0
y    NaN
c    3.0
p    0.0
dtype: float64 
>>> bots = ['r', 'y', 'p', 'c']
>>> new_series = pd.Series(dict, index = bots)
>>> new_series
r    2.0
y    NaN
p    0.0
c    3.0
dtype: float64
```

The panda functions **isnull** and **notnull** are used to detect missing data:

```
>>> pd.isnull(new_series)
r    False
y     True
p    False
c    False
dtype: bool
>>> pd.notnull(new_series)
r     True
y    False
p     True
c     True
dtype: bool
# using instance methods
>>> new_series.isnull()
r    False
y     True
p    False
c    False
dtype: bool
>>> new_series.notnull()
r     True
y    False
p     True
c     True
dtype: bool
```

We can perform mathematical operations, scalar operations, NumPy functions, etc. and the series will preserve the index-value relationship:

```
>>> dict_series * 2
r    4
d    4
c    6
p    0
dtype: int64
>>> dict_series > 1
r     True
d     True
c     True
p    False
dtype: bool
>>> dict_series[dict_series > 1]
r    2
d    2
c    3
dtype: int64
>>> np.mod(dict_series, 2)
r    0
d    0
c    1
p    0
dtype: int64
>>> 
```
When we perform operations on Series with misaligned indexes, the resulting Series will have an index label aligned by arithmetic operations. This is similar to a 'join' function used in databases (think SQL). Check out the outcome of the index labels of the following:
 
```
>>> dict_series
r    2
d    2
c    3
p    0
dtype: int64
>>> new_series
r    2.0
y    NaN
c    3.0
p    0.0
dtype: float64
>>> dict_series + new_series
c    6.0
d    NaN
p    0.0
r    4.0
y    NaN
dtype: float64
>>> 
```

Let's examine adding two Series a bit further to understand the underlying methods. 

Say we have the following Series, and add a scalar:

```
>>> monster = pd.Series([10, 20, 30, 40, 50])
>>> monster + 1
0    11
1    21
2    31
3    41
4    51
dtype: int64
>>> 
```

The behavior appears to have used broadcasting (remember that term we learned in with Numpy?) to add 1, element-wise, to our monster Series. 

What if we present that "1" value as a Series instead of an integer?

```
>>> monster + pd.Series(1)
0    11.0
1     NaN
2     NaN
3     NaN
4     NaN
dtype: float64
>>> 
```

When we create a Series, its size corresponds to the number of values we give it. So there wasn't a "1" to add to index values 1, 2, 3, and 4. Pandas only performs operations on Series with the same index label, and it this case, it was "0" as that is the index both Series had in common. 

Let's add two Series. 

```
>>> A = pd.Series([10, 20, 30, 40, 50])
>>> B = pd.Series([1, 2, 3, 4, 5])
>>> A + B
0    11
1    22
2    33
3    44
4    55
dtype: int64
```

It appears that the addition happened element-wise, yes?

Consider rearranging the index of A and performing the same operation:

```
>>> A.index = np.array([4, 3, 2, 1, 0])
>>> A + B
0    51
1    42
2    33
3    24
4    15
dtype: int64
>>> 
```

Why did that happen? Well, A has a new index, and the operations happen according to index label. 

```
# New index of A
>>> A
4    10
3    20
2    30
1    40
0    50
dtype: int64
>>> 
```

The same behavior happens whether is addition, subtraction, multiplication, division, etc. 

To avoid this behavior, you'll have to perform the operation at the NumPy level with **to_numpy()**: 

```
>>> A.to_numpy() + B.to_numpy()
array([11, 22, 33, 44, 55])
>>> 
```
And if you want it in the form of a Series:

```
>>> pd.Series(A.to_numpy() + B.to_numpy())
0    11
1    22
2    33
3    44
4    55
dtype: int64
>>> 
```

As you can see, dropping to the Numpy level will allow us to perform the operations element-wise. 

Things get crazy if we add a NumPy Array to a Series. Since we changed the index of A, we get a Series with the new index of A: 

```
>>> A + B.to_numpy()
4    11
3    22
2    33
1    44
0    55
dtype: int64
>>>
```



