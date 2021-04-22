# More More Transformations

## Binning and Categoricals

![bins](week3images/bins.jpeg)


No, we're not throwing out our data!

We're separating it into "bins" for further analysis.

Suppose we have data on a city street, and we want to group them by block addresses (If you don't know how house numbers and addresses and city blocks work, ask a friend.):

```
>>> house_nos = [101, 104, 154, 202, 230, 288, 410, 444, 485, 501, 503, 554, 508, 605, 610, 612]
>>>
```
We will create "bins" for each grouping of house numbers by block:

```
>>> bins = [100, 200, 300, 400, 500, 600, 700]
```
Then we use the **cut()** method to create the actual groupings against our house_nos data. Pandas computes the proper bins and returns a **Categorical** object. A parentheses means the side is "open", while a bracket notes it is "closed" and exclusive. (You can change this by passing right=False). 

```
>>> blocks = pd.cut(house_nos, bins)
>>> blocks
[(100, 200], (100, 200], (100, 200], (200, 300], (200, 300], ..., (500, 600], (500, 600], (600, 700], (600, 700], (600, 700]]
Length: 16
Categories (6, interval[int64]): [(100, 200] < (200, 300] < (300, 400] < (400, 500] < (500, 600] <
                                  (600, 700]]
# pass right=False to change open/closed ends
>>> blocks_left = pd.cut(house_nos, bins, right=False)
>>> blocks_left
[[100, 200), [100, 200), [100, 200), [200, 300), [200, 300), ..., [500, 600), [500, 600), [600, 700), [600, 700), [600, 700)]
Length: 16
Categories (6, interval[int64]): [[100, 200) < [200, 300) < [300, 400) < [400, 500) < [500, 600) <
                                  [600, 700)]
>>> 
```

The brackets/parentheses come into play when you have data that is located on the border of your bins. It's important to decide where to set your brackets/parentheses to account for data you want to include/exclude in your bins, and which numbers you want your bins to start and end at. For example, house\_nos uses 101, and house\_nos2 uses 100:

```
>>> house_nos = [101, 104, 154, 202, 230, 288, 410, 444, 485, 501, 503, 554, 508, 605, 610, 612]
>>> blocks = pd.cut(house_nos, bins)
>>> blocks
[(100, 200], (100, 200], (100, 200], (200, 300], (200, 300], ..., (500, 600], (500, 600], (600, 700], (600, 700], (600, 700]]
Length: 16
Categories (6, interval[int64]): [(100, 200] < (200, 300] < (300, 400] < (400, 500] < (500, 600] <
                                  (600, 700]]
>>> house_nos2 = [100, 104, 154, 202, 230, 288, 410, 444, 485, 501, 503, 554, 508, 605, 610, 612]
>>> blocks2 = pd.cut(house_nos2, bins, right=False)
>>> blocks2
[[100, 200), [100, 200), [100, 200), [200, 300), [200, 300), ..., [500, 600), [500, 600), [600, 700), [600, 700), [600, 700)]
Length: 16
Categories (6, interval[int64]): [[100, 200) < [200, 300) < [300, 400) < [400, 500) < [500, 600) <
                                  [600, 700)]
>>> blocks2 = pd.cut(house_nos2, bins)
# if we don't set right=False to include 100 in house_nos2, it won't recognize it and will not bin it. 
>>> blocks2
[NaN, (100.0, 200.0], (100.0, 200.0], (200.0, 300.0], (200.0, 300.0], ..., (500, 600], (500, 600], (600, 700], (600, 700], (600, 700]]
Length: 16
Categories (6, interval[int64]): [(100, 200] < (200, 300] < (300, 400] < (400, 500] < (500, 600] <
                                  (600, 700]]
>>>
```

If you need help with this concept, [here is a good explanation](https://stackoverflow.com/questions/4396290/what-does-this-square-bracket-and-parenthesis-bracket-notation-mean-first1-last).

This categorical object is like an array of strings by bin name, and there is a categories array which specifies the category names. The **codes** attribute maps the bins to the data's location:

```
>>> blocks.codes
array([0, 0, 0, 1, 1, 1, 3, 3, 3, 4, 4, 4, 4, 5, 5, 5], dtype=int8)
>>> blocks.categories
IntervalIndex([(100, 200], (200, 300], (300, 400], (400, 500], (500, 600], (600, 700]],
              closed='right',
              dtype='interval[int64]')
>>>
```

The method **value_counts()** will arrange the data output from **cut**. 

```
>>> pd.value_counts(blocks)
(500, 600]    4
(100, 200]    3
(200, 300]    3
(400, 500]    3
(600, 700]    3
(300, 400]    0
dtype: int64
>>> 
```
We can supply names for our bins:

```
>>> block_names = ['100 Block', '200 Block', '300 Block', '400 Block', '500 Block', '600 Block']
>>> blocks = pd.cut(house_nos, bins, labels=block_names)
>>> blocks
['100 Block', '100 Block', '100 Block', '200 Block', '200 Block', ..., '500 Block', '500 Block', '600 Block', '600 Block', '600 Block']
Length: 16
Categories (6, object): ['100 Block' < '200 Block' < '300 Block' < '400 Block' < '500 Block' <
                         '600 Block']
>>> pd.value_counts(blocks)
500 Block    4
100 Block    3
200 Block    3
400 Block    3
600 Block    3
300 Block    0
dtype: int64
>>> 
```
If we pass an integer as a number of bins to cut instead of specifying our bin values, pandas creates bins of equal length based on the minimum and maximum data supplied. Here we created 6 bins--same number as above. Definitely **not** want we want in this case, but in others we may want to use it:

```
>>> blocks = pd.cut(house_nos, 6)
>>> blocks
[(100.489, 186.167], (100.489, 186.167], (100.489, 186.167], (186.167, 271.333], (186.167, 271.333], ..., (526.833, 612.0], (441.667, 526.833], (526.833, 612.0], (526.833, 612.0], (526.833, 612.0]]
Length: 16
Categories (6, interval[float64]): [(100.489, 186.167] < (186.167, 271.333] < (271.333, 356.5] <
                                    (356.5, 441.667] < (441.667, 526.833] < (526.833, 612.0]]
>>> 
```
We can specify the number of decimal places to use by passing a precision argument:

```
>>> blocks = pd.cut(house_nos, 6, precision=0)
>>> blocks
[(100.0, 186.0], (100.0, 186.0], (100.0, 186.0], (186.0, 271.0], (186.0, 271.0], ..., (527.0, 612.0], (442.0, 527.0], (527.0, 612.0], (527.0, 612.0], (527.0, 612.0]]
Length: 16
Categories (6, interval[float64]): [(100.0, 186.0] < (186.0, 271.0] < (271.0, 356.0] < (356.0, 442.0] <
                                    (442.0, 527.0] < (527.0, 612.0]]
>>> blocks = pd.cut(house_nos, 6, precision=2)
>>> blocks
[(100.49, 186.17], (100.49, 186.17], (100.49, 186.17], (186.17, 271.33], (186.17, 271.33], ..., (526.83, 612.0], (441.67, 526.83], (526.83, 612.0], (526.83, 612.0], (526.83, 612.0]]
Length: 16
Categories (6, interval[float64]): [(100.49, 186.17] < (186.17, 271.33] < (271.33, 356.5] <
                                    (356.5, 441.67] < (441.67, 526.83] < (526.83, 612.0]]
>>> 
```
The method **qcut()** is used to create sample quantiles. Let's create some random data:

```
>>> data = np.random.randn(100)
>>> data
array([-0.32400841, -1.52280819,  0.04501013, -0.99994629, -0.31924426,
        0.37189065,  0.99929997, -0.73794096,  0.97041124,  0.49715724,
        1.01551853, -0.03761385,  0.75780358, -2.06829838,  1.13844193,
        0.47441179, -0.29341704,  0.14795599,  0.67796084,  0.21230627,
       -1.74461937,  0.89018896,  1.02720134, -0.11727189, -0.77549764,
       -1.9747786 , -0.1268359 , -0.82121817,  1.03147008, -1.02782717,
       -1.62735633, -0.89408347, -1.59934142,  0.24754117, -1.6157458 ,
        0.38914768, -0.01013765,  0.15076673, -1.09237617, -0.23787894,
       -0.24486369, -0.27930958, -0.3296    ,  0.60439014, -0.03769448,
        0.76820741, -0.18571828, -0.23979398,  0.321963  ,  0.52616064,
        2.10412518,  0.27227953,  0.02714859, -0.55467452,  1.10515475,
        0.50896821, -0.63419158,  0.21129549, -0.25316874, -1.18751297,
       -1.8415422 ,  0.80995611,  0.37646367,  0.14651819, -2.3355404 ,
        0.38813388, -0.94715653, -0.33680387,  0.08915732, -0.5346794 ,
       -0.04324879, -1.20429346, -0.29749322, -0.3450622 ,  1.14269494,
        1.97921148,  0.74162538, -1.10768257,  0.96618443, -0.20186212,
        1.03877759, -0.23777374, -0.18572733,  0.41824584, -0.92190834,
        1.32529981, -0.84074883, -1.40857692,  0.39115892, -0.2449739 ,
        0.19215658, -1.70822545,  0.35259802,  1.15407886, -0.16579981,
       -1.82693989, -0.8921259 ,  1.14874582,  1.77666238, -0.37285536])
>>>
```
We have 100 values. If we separate it into 4 quartiles, it would look like this: 

```
>>> quarts = pd.qcut(data, 4)
>>> quarts = pd.qcut(data, 4)
>>> quarts
[(-0.747, -0.0803], (-2.3369999999999997, -0.747], (-0.0803, 0.5], (-2.3369999999999997, -0.747], (-0.747, -0.0803], ..., (-2.3369999999999997, -0.747], (-2.3369999999999997, -0.747], (0.5, 2.104], (0.5, 2.104], (-0.747, -0.0803]]
Length: 100
Categories (4, interval[float64]): [(-2.3369999999999997, -0.747] < (-0.747, -0.0803] < (-0.0803, 0.5] <
                                    (0.5, 2.104]]
>>> pd.value_counts(quarts)
(-2.3369999999999997, -0.747]    25
(-0.747, -0.0803]                25
(-0.0803, 0.5]                   25
(0.5, 2.104]                     25
dtype: int64
>>> 
```
**qcut()** sorted the list of data, and put it in bins of 25% increments (25 each, because we have 100 values). 

For quintiles, it will look a bit different: 

```
>>> quints = pd.qcut(data, 5)
>>> quints
[(-0.9, -0.245], (-2.3369999999999997, -0.9], (-0.245, 0.167], (-2.3369999999999997, -0.9], (-0.9, -0.245], ..., (-2.3369999999999997, -0.9], (-0.9, -0.245], (0.745, 2.104], (0.745, 2.104], (-0.9, -0.245]]
Length: 100
Categories (5, interval[float64]): [(-2.3369999999999997, -0.9] < (-0.9, -0.245] < (-0.245, 0.167] <
                                    (0.167, 0.745] < (0.745, 2.104]]
                                    
>>> pd.value_counts(quints)
(-2.3369999999999997, -0.9]    20
(-0.9, -0.245]                 20
(-0.245, 0.167]                20
(0.167, 0.745]                 20
(0.745, 2.104]                 20
dtype: int64
>>> 
```
Also, we can designate our own quantiles. Here we create quantiles by tenths, where 0 and 1 are exclusive: 

```
>>> tenths = pd.qcut(data, [0, 0.1, 0.2, 0.3, 0.4, 0.5, 0.6, 0.7, 0.8, 0.9, 1])
>>> tenths
[(-0.353, -0.245], (-1.53, -0.9], (-0.0803, 0.167], (-1.53, -0.9], (-0.353, -0.245], ..., (-2.3369999999999997, -1.53], (-0.9, -0.353], (1.032, 2.104], (1.032, 2.104], (-0.9, -0.353]]
Length: 100
Categories (10, interval[float64]): [(-2.3369999999999997, -1.53] < (-1.53, -0.9] < (-0.9, -0.353] <
                                     (-0.353, -0.245] ... (0.167, 0.388] < (0.388, 0.745] <
                                     (0.745, 1.032] < (1.032, 2.104]]
>>> pd.value_counts(tenths)
(-2.3369999999999997, -1.53]    10
(-1.53, -0.9]                   10
(-0.9, -0.353]                  10
(-0.353, -0.245]                10
(-0.245, -0.0803]               10
(-0.0803, 0.167]                10
(0.167, 0.388]                  10
(0.388, 0.745]                  10
(0.745, 1.032]                  10
(1.032, 2.104]                  10
dtype: int64
>>> 
```
Different groupings, but the values are all accounted for. 


Pandas has a [Categorical type](https://pandas.pydata.org/docs/reference/api/pandas.Categorical.html). Let's have some fun with it and create a Categorical:

```
>>> colors = pd.Categorical(values = ['red', 'orange', 'yellow', 'green'])
>>> colors
['red', 'orange', 'yellow', 'green']
Categories (4, object): ['green', 'orange', 'red', 'yellow']
```
It could be a Series, if you wanted: 

```
>>> pd.Series(colors)
0       red
1    orange
2    yellow
3     green
dtype: category
Categories (4, object): ['green', 'orange', 'red', 'yellow']
```

Think of "values" as what we have in stock, but categories is the potential of our inventory:

```
>>> colors = pd.Categorical(values = ['red', 'orange', 'yellow', 'green'], categories = ['red', 'orange', 'yellow', 'green', 'blue', 'indigo', 'violet'])
>>> colors
['red', 'orange', 'yellow', 'green']
Categories (7, object): ['red', 'orange', 'yellow', 'green', 'blue', 'indigo', 'violet']
```

We can apply order to it with ordered=True. Note this produces a '<' value in the Categories:

```
>>> colors = pd.Categorical(values = ['red', 'orange', 'yellow', 'green'], categories = ['red', 'orange', 'yellow', 'green', 'blue', 'indigo', 'violet'], ordered=True)
>>> colors
['red', 'orange', 'yellow', 'green']
Categories (7, object): ['red' < 'orange' < 'yellow' < 'green' < 'blue' < 'indigo' < 'violet']
```

The ordered parameter allows us to do value comparisons, via order in the rainbow, because that is the order we defined the categories in, and I happen to prefer violet the most. We get back a boolean array. And we can use bracket notation to return colors "less than" X color:

```
>>> colors < 'green'
array([ True,  True,  True, False])
>>> colors[colors < 'green']
['red', 'orange', 'yellow']
Categories (7, object): ['red' < 'orange' < 'yellow' < 'green' < 'blue' < 'indigo' < 'violet']
```

Note: We can't use .loc or .iloc on categoricals, however, we can create a Series with a CategoricalIndex and invoke .loc with a color:

```
>>> colorsSeries = pd.Series(data = [0,1,2,3], index = pd.CategoricalIndex(colors))
>>> colorsSeries
red       0
orange    1
yellow    2
green     3
dtype: int64
>>> colorsSeries.loc['green']
3
```
Pandas has **get_dummies()** which lets you convert categorical variables into indicator variables. It creates a DataFrame with a column for each distinct category. A '1' denotes an occurrence, while a '0' denotes otherwise. You might find this handy with machine learning modeling (see [one-hot encoding](https://stackabuse.com/one-hot-encoding-in-python-with-pandas-and-scikit-learn/)):

```
>>> pd.get_dummies(colors, prefix = 'color')
   color_red  color_orange  color_yellow  color_green  color_blue  color_indigo  color_violet
0          1             0             0            0           0             0             0
1          0             1             0            0           0             0             0
2          0             0             1            0           0             0             0
3          0             0             0            1           0             0             0
>>> 
```


## Finding Outliers

An outlier is an observation point that is distant from other observations. It's separate, in its own little world, different from the crowd... 

Find the outlier in this Series: 

```
>>> find_the_outlier = pd.Series([32, 34, 38, 34, 39, 30, 30, 12, 37, 36, 31])
>>> find_the_outlier
0     32
1     34
2     38
3     34
4     39
5     30
6     30
7     12
8     37
9     36
10    31
dtype: int64
>>> 
```
Visually, we can see the outlier is located at [7], with a value of 12, as all the other values are in the 30's. 

What if you had 10000 rows? No fun. Like finding a needle in a haystack, yeah?

Let's create a new DataFrame with some randos:

```
>>> data = pd.DataFrame(np.random.randn(100, 4))
>>> data
           0         1         2         3
0  -0.321632 -0.427145 -1.009175 -0.973797
1  -0.294169  0.583871  1.234381 -0.515687
2  -0.183937  0.428905 -0.572116 -0.569489
3  -0.779419 -0.912364  0.332823  0.616638
4  -0.160788 -0.017945 -0.829962 -0.737906
..       ...       ...       ...       ...
95 -1.923572  1.293795 -1.016655 -0.688947
96 -0.784359 -1.511373 -1.002354  1.187255
97 -0.472140 -0.235103 -2.217099 -0.141975
98 -1.645931 -1.033941 -1.211220  0.360548
99 -0.768655 -0.137259 -1.164590  0.309388

[100 rows x 4 columns]
```
Summary stats are as such:

```
>>> data.describe()
                0           1           2           3
count  100.000000  100.000000  100.000000  100.000000
mean    -0.146614    0.091677   -0.039047   -0.027260
std      0.917120    0.907456    1.093960    0.892748
min     -2.712822   -2.008352   -2.974402   -2.006483
25%     -0.742303   -0.485369   -0.945729   -0.596343
50%     -0.204105    0.172972    0.007032    0.040188
75%      0.527247    0.682168    0.661784    0.602633
max      1.858968    2.277504    2.563895    1.710362
```

We want to examine data in the second column:

```
>>> data[2]
0    -1.009175
1     1.234381
2    -0.572116
3     0.332823
4    -0.829962
        ...   
95   -1.016655
96   -1.002354
97   -2.217099
98   -1.211220
99   -1.164590
Name: 2, Length: 100, dtype: float64
```
Specifically, we want to see all values in column 2 that have an absolute value greater than 2:

```
>>> data[2][np.abs(data[2]) > 2]
21   -2.720974
42   -2.974402
59    2.563895
67    2.044969
97   -2.217099
Name: 2, dtype: float64
>>> 
```

That inline query looks messy. Let me break it down. Sometimes assigning variables can be helpful:

```
# assign the data in column 2 to 'column'
>>> column = data[2]
>>> column
0    -1.009175
1     1.234381
2    -0.572116
3     0.332823
4    -0.829962
        ...   
95   -1.016655
96   -1.002354
97   -2.217099
98   -1.211220
99   -1.164590
Name: 2, Length: 100, dtype: float64
# get the absolute values of column 2 (which is 'column') and assign it to abs_values
>>> abs_values = np.abs(column)
# query column 2 for the abs_values greater than 2
>>> column[abs_values > 2]
21   -2.720974
42   -2.974402
59    2.563895
67    2.044969
97   -2.217099
Name: 2, dtype: float64
>>> 
```
As long as you have an understanding of what operations are being performed, you'll get better at assigning variables and later doing it all on one line, on the fly, if necessary. 

We can find all rows in data that contain an absolute value greater than 2 by using **any()** on a boolean dataframe:

```
>>> data[(np.abs(data) > 2).any(1)]
           0         1         2         3
13 -2.712822  0.994223  0.365541 -0.337207
21  0.686969  2.277504 -2.720974  0.561383
22  0.977942  1.314448 -0.449585 -2.006483
30 -0.294158 -2.008352  1.400083  0.309287
42 -0.450023 -0.030895 -2.974402 -0.537476
50 -0.585595  2.018044 -1.145557 -0.961014
59 -1.035009 -0.280855  2.563895  0.401096
67  0.175444 -1.584844  2.044969 -1.563466
97 -0.472140 -0.235103 -2.217099 -0.141975
>>> 
```

(Sidenote, unrelated).. If you were interested in the counts per column:

```
>>> data[(np.abs(data) > 2) == False].count()
0    99
1    97
2    95
3    99
dtype: int64
>>> data[(np.abs(data) > 2) == True].count()
0    1
1    3
2    5
3    1
dtype: int64
```
Back to any()... We can change values based on the data returned from any(). First, let's see how we can use **.sign()** to produce 1 or -1 values based on whether the values in data are positive or negative: 

```
>>> np.sign(data)
      0    1    2    3
0  -1.0 -1.0 -1.0 -1.0
1  -1.0  1.0  1.0 -1.0
2  -1.0  1.0 -1.0 -1.0
3  -1.0 -1.0  1.0  1.0
4  -1.0 -1.0 -1.0 -1.0
..  ...  ...  ...  ...
95 -1.0  1.0 -1.0 -1.0
96 -1.0 -1.0 -1.0  1.0
97 -1.0 -1.0 -1.0 -1.0
98 -1.0 -1.0 -1.0  1.0
99 -1.0 -1.0 -1.0  1.0

[100 rows x 4 columns]
>>> 
```
If we wanted to not have any values over 2, we could use this to cap the values in data to 2:

```
>>> data[np.abs(data) > 2]
     0   1         2   3
0  NaN NaN       NaN NaN
1  NaN NaN       NaN NaN
2  NaN NaN       NaN NaN
3  NaN NaN       NaN NaN
4  NaN NaN       NaN NaN
..  ..  ..       ...  ..
95 NaN NaN       NaN NaN
96 NaN NaN       NaN NaN
97 NaN NaN -2.217099 NaN
98 NaN NaN       NaN NaN
99 NaN NaN       NaN NaN

[100 rows x 4 columns]
>>> data[np.abs(data) > 2] = np.sign(data) * 2
>>> data.describe()
                0           1           2           3
count  100.000000  100.000000  100.000000  100.000000
mean    -0.139486    0.088805   -0.026011   -0.027195
std      0.899575    0.900521    1.037875    0.892603
min     -2.000000   -2.000000   -2.000000   -2.000000
25%     -0.742303   -0.485369   -0.945729   -0.596343
50%     -0.204105    0.172972    0.007032    0.040188
75%      0.527247    0.682168    0.661784    0.602633
max      1.858968    2.000000    2.000000    1.710362
>>> 
# now there are zero values over 2
>>> data[(np.abs(data) > 2).any(1)]
Empty DataFrame
Columns: [0, 1, 2, 3]
Index: []
>>> 
```

Your own personal data set might have outliers via your own criteria, and you will have to come up with solutions to remove the row or replace the value as necessary. Hint: a good first pass at finding outliers might be to plot it and see it in a graphical representation... maybe a scatter plot or a box plot! 

If you are into advanced statistics, research ways to use zscores. 




