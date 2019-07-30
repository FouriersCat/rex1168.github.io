---
youku_id: 
youtube_id: 
qq_video_id: 
b_av: 
b_page: 1
title: pandas应用整理
description: "."
chapter: 1
category: python-data-proc
post-headings:
author-link: #
no-video: true
publish-date: 2019-07-07
thumbnail: /static/img/course_cover-small/python-data-proc.png
index: 2
---


首先导入pandas库
```python
import pandas as pd
```

## Series 

pandas中包含Series和DataFrame，首先来看Series

**创建Series**
```python
sr = pd.Series([1,2,3,4])
```
也可以在创建的时候指定index
```python
sr = pd.Series([1,2,3,4], index=['a','b','c','d]')
```

用字典创建Series对象
```python
scores = {'xiaohong':80, 'xiaoming':85, 'xiaoli':95}
sr2 = pd.Series(scores)
>>xiaohong    80
  xiaoli      95
  xiaoming    85
dtype: int64
```

创建时指定index，且index与字典不匹配
```python
names = ['xiaohong', 'xiaoli', 'xiaozhang', 'xiaoming']
scores = {'xiaohong':80, 'xiaoming':85, 'xiaoli':95}
sr3 = pd.Series(scores, index=names)
>>
xiaohong     80.0
xiaoli       95.0
xiaozhang     NaN
xiaoming     85.0
dtype: float64
```

**索引**
```python
sr3[1]
>>95.0
```

修改index值
```python
sr3.index = range(1,5)
```

**Series运算**

四则运算时会根据index的值对相应的数据进行运算，结果是浮点数
```python
sr1 = pd.Series([1,2,3,4],['a','b','c','d'])
sr2 = pd.Series([1,5,8,9],['a','c','e','f'])
sr2 - sr1
>>
a    0.0
b    NaN
c    2.0
d    NaN
e    NaN
f    NaN
dtype: float64

sr2/sr1
>>
a    1.000000
b         NaN
c    1.666667
d         NaN
e         NaN
f         NaN
dtype: float64
```

## DataFrame

**创建DataFrame**
```python
df = pd.DataFrame([[1.0,1],[2.0,2],[3.0,3],[4.0,4]],columns=['floats','ints'])
df.index = ['a','b','c','d']
>>
   floats  ints
a     1.0     1
b     2.0     2
c     3.0     3
d     4.0     4
```

也可以在创建时直接指定index
```python
df1 = pd.DataFrame([[1.0,1],[2.0,2],[3.0,3],[4.0,4]],columns=['floats','ints'], index=['a','b','c','d'])
```


创建随机矩阵的DataFrame
```python
df1 = pd.DataFrame(np.random.randn(5,4), columns=['a','b','c','d'], index=range(1,6))
```

用dict来创建DataFrame
```python
dic1 = {'ints':[1,2,3,4], 'floats':[1.0,2.0,3.0,4.0]}
df2 = pd.DataFrame(dic1, index=['a','b','c','d'])
>>>
   floats  ints
a     1.0     1
b     2.0     2
c     3.0     3
d     4.0     4
```


**索引**

访问列向量
```python
df['ints']
>>a    1
  b    2
  c    3
  d    4
Name: ints, dtype: int64
```

访问行向量（即index）要使用`ix`
```python
df.ix[['a','c']]
>>   floats  ints
a     1.0     1
c     3.0     3
```

也可以使用`loc`或`iloc`来访问index或某个固定位置，其中`loc`是访问index或columns的名称，而`iloc`访问的是序号
```python
df.loc['a']
df.loc['a','ints']
df.iloc[2]
df.iloc[2,1]
```


**重置 DataFrame 的索引**

可以用 .reset_index()把DataFrame的索引重置为[0,1,2...]。之前的索引被保存成名为'index'的列。
```python
>>> df = pd.DataFrame(np.random.randn(5,4), ['A','B','C','D','E'],['W','X','Y','Z'])
>>> df
          W         X         Y         Z
A -1.295107 -0.908105 -1.541323  1.200612
B  0.395801 -0.272468 -0.702533 -0.594971
C  2.016240  0.733682  1.922792  0.096894
D -0.206112 -0.055755 -0.023129 -0.946827
E -0.737485 -0.777170  0.757850 -0.393240

>>> df.reset_index()
    index    W         X         Y         Z
0     A  0.381291  0.769049  1.384186  0.711498
1     B -0.012941 -0.652186 -1.714460 -0.904905
2     C  0.303953  1.027697  0.045510  0.058530
3     D  0.467826 -2.119408 -0.492461  0.135464
4     E  0.539113 -0.436789  1.232529  0.170452
```

用.set_index()表示设置某一列为索引，为df增加一列
```python
df['ID'] = ['a','b','c','d','e']
>>        X         Y         Z  ID
A  0.769049  1.384186  0.711498   a
B -0.652186 -1.714460 -0.904905   b
C  1.027697  0.045510  0.058530   c
D -2.119408 -0.492461  0.135464   d
E -0.436789  1.232529  0.170452   e
```
设置ID列为索引
```python
df.set_index('ID')
>>           
idx         X         Y         Z                          
a    0.769049  1.384186  0.711498
b   -0.652186 -1.714460 -0.904905
c    1.027697  0.045510  0.058530
d   -2.119408 -0.492461  0.135464
e   -0.436789  1.232529  0.170452
```
原来的index就直接被替换掉了，不会留备份。


**多级索引**

多级索引有点类似后面要讲的group

首先定义元素为元组的列表
```python
idx1 = ['odd','even','odd','even','odd','even']
idx2 = ['a','b','c','d','e','f']
idx = list(zip(idx1,idx2)
```

生成多级索引
```python
indx = pd.MultiIndex.from_tuples(idx)
>>MultiIndex(levels=[['even', 'odd'], ['a', 'b', 'c']],
           labels=[[1, 0, 1, 0, 1, 0], [0, 1, 2, 0, 1, 2]])
```

创建DataFrame
```python
df = pd.DataFrame(np.random.randn(6,4), index=indx, columns=['A','B','C','D'])
>>             A         B         C         D
odd  a  0.070774  0.698612  1.143667 -0.363108
even b  0.162158 -1.147405  1.408487 -0.676182
odd  c -0.308266  1.681370  1.414855 -1.591244
even a  0.263374 -0.756666 -1.037012 -0.142972
odd  b -1.771831  0.125370  1.061639 -0.778537
even c  0.588844 -1.052212 -0.927885  0.867231
```

获取数据
```python
df.loc['odd','a']
df.loc['odd'].loc['a']
```

可以看出，这两列索引是没有名字的，可以使用.index.name为他们添加名字
```python
df.index.names = ['idx1','idx2']
>>>
idx1 idx2      A         B         C         D 
odd  a     0.070774  0.698612  1.143667 -0.363108
even b     0.162158 -1.147405  1.408487 -0.676182
odd  c    -0.308266  1.681370  1.414855 -1.591244
even a     0.263374 -0.756666 -1.037012 -0.142972
odd  b    -1.771831  0.125370  1.061639 -0.778537
even c     0.588844 -1.052212 -0.927885  0.867231
```

有了索引列的名字后，可以用.xs来获取数据
```python
df.xs('a', level='idx2')
>>           A         B         C         D
idx1                                        
odd   0.070774  0.698612  1.143667 -0.363108
even  0.263374 -0.756666 -1.037012 -0.142972

df.xs('odd', level='idx1')
>>           A         B         C         D
idx2                                        
a     0.070774  0.698612  1.143667 -0.363108
c    -0.308266  1.681370  1.414855 -1.591244
b    -1.771831  0.125370  1.061639 -0.778537
```


**DataFrame的处理**
```python
df2.index
df2.columns
df2.ix['c']
df2.ix[df.index[1:3]]
df2.sum()
df2.apply(lambda x:x**2) #不会改变df2的内容
df2['str1'] = ['a1', 'b1', 'c1', 'd1'] # 增加一列 会改变df2的内容
```

如果使用df2.xx操作，则不会改变df2的内容,因为并没有对df2进行赋值；如果使用df2[]=xx，则会改变df2 的内容。

```python
df2['str2'] = pd.DataFrame(['a2','b2','d2','c2'],index = ['a','b','d','c']) #增加一列，并指定index
df2['str2'] = pd.Series(['a2','b2','d2','c2'],index = ['a','b','d','c']) #用Series的方式增加一列
df2['str3'] = pd.DataFrame(['a3','b3','c3'],index = ['a','b','c'])#未指定的位置是NaN

```


从现有的列创建新列
```python
df2['str4'] = df2['ints'] + df2['floats']
```


用字典的方式增加一行，ignore_index设置为True，则index恢复为0…n
```python
df2.append({'floats':5.0,'ints':5},ignore_index = True)
df2.append(pd.DataFrame({'floats':5.0,'ints':5},index = ['e'])) #增加一行，且该行的index为 e
```

删除DataFrame中的行/列

可以使用`.drop()`函数，在使用这个函数的时候，需要指定具体的删除方向，axis=0表示删除某行，axis=1表示删除某列。
```python
df2.drop('str1', axis=1)
df2.drop('c', axis=0)
```
如果想在使用df2.xx操作时更改df2的内容，可以如下操作
```python
df2 = df2.drop('str1', axis=1)
df2.drop('str1', inplace=True)
```


**条件筛选**
```python
df = pd.DataFrame(np.random.randn(5,4), ['A','B','C','D','E'],['W','X','Y','Z'])
>>    W         X         Y         Z
A  0.381291  0.769049  1.384186  0.711498
B -0.012941 -0.652186 -1.714460 -0.904905
C  0.303953  1.027697  0.045510  0.058530
D  0.467826 -2.119408 -0.492461  0.135464
E  0.539113 -0.436789  1.232529  0.170452
```

筛选df中W列大于0的数据
```python
df[df['W']>0]
>>      W         X         Y         Z
A  0.381291  0.769049  1.384186  0.711498
C  0.303953  1.027697  0.045510  0.058530
D  0.467826 -2.119408 -0.492461  0.135464
E  0.539113 -0.436789  1.232529  0.170452
```

该返回值是一个DataFrame，因此就可以继续操作

对筛选出来的df，只看其中的X和Y列
```python
df[df['W']>0][['X','Y']]
>>      X         Y
A  0.769049  1.384186
C  1.027697  0.045510
D -2.119408 -0.492461
E -0.436789  1.232529
```

用逻辑运算符(&  |)来筛选

同时选出W列和X列都大于0的数据
```python
df[(df['W']>0) & (df['X']>0)]
>>       W         X         Y         Z
A  0.381291  0.769049  1.384186  0.711498
C  0.303953  1.027697  0.045510  0.058530
```



**缺失值处理**

DataFrame中经常会出现值为NaN的情况，可以使用.dropna()丢弃这些值或者使用.fillna()来自动给这些空值填充数据。
```python
dic = {'A':[1,2,np.nan], 'B':[4,np.nan,6], 'C':[7,8,9]}
df = pd.DataFrame(dic)
>>   A    B  C
0  1.0  4.0  7
1  2.0  NaN  8
2  NaN  6.0  9
```

把包含NaN的行或列丢弃
```python
df.dropna()  #默认是把包含NaN的行丢弃
>>   A    B  C
0  1.0  4.0  7
```

把包含NaN的列丢弃
```python
df.dropna(axis=1)  #默认是axis=0
>> C
0  7
1  8
2  9
```

把全是NaN的行丢弃
```python
df.dropna(how='all') #默认是how='any'
```

把全是NaN的列丢弃
```python
df.dropna(how='all', axis=1)
```

用isnull()和notnull()返回bool值
```python
df.isnull()
>>  A      B      C
0  False  False  False
1  False   True  False
2   True  False  False

df.notnull()
>>     A      B     C
0   True   True  True
1   True  False  True
2  False   True  True
```

填充NaN的值
```python
df.fillna(10)
>>    A     B  C
0   1.0   4.0  7
1   2.0  10.0  8
2  10.0   6.0  9
```


**describe()功能**
```python
>>> df = pd.DataFrame(np.random.standard_normal((5,4)))
>>> df
          0         1         2         3
0 -2.352762  1.649683 -0.491568  0.783590
1  1.096748 -0.356617  0.473834  1.545410
2 -0.218845 -0.592712 -1.065075  0.779286
3 -0.887451  0.175354 -0.078951 -1.050216
4  0.029647 -0.244575 -0.317775  0.871875

>>> df.describe()
              0         1         2         3
count  5.000000  5.000000  5.000000  5.000000
mean  -0.466533  0.126227 -0.295907  0.585989
std    1.273423  0.895941  0.563461  0.968994
min   -2.352762 -0.592712 -1.065075 -1.050216
25%   -0.887451 -0.356617 -0.491568  0.779286
50%   -0.218845 -0.244575 -0.317775  0.783590
75%    0.029647  0.175354 -0.078951  0.871875
max    1.096748  1.649683  0.473834  1.545410
```

**concat**

默认是按行就行concat
```python
>>> df1 = pd.DataFrame(np.random.standard_normal((3,4)))
>>> df2 = pd.DataFrame(np.random.standard_normal((3,4)))
>>> df3 = pd.DataFrame(np.random.standard_normal((3,4)))
>>> pd.concat([df1,df2,df3])
          0         1         2         3
0  1.186311  0.903929 -0.090899  1.192270
1  0.073337  0.477155  0.804332 -0.027136
2  0.465125 -0.604937  0.854352 -0.798596
0 -0.993978  0.785300 -1.693622  0.388309
1 -0.680629 -2.018552 -0.108504  1.056134
2  0.445300 -1.106076 -0.073527  1.528935
0  1.305873 -1.066145  0.858643 -0.155658
1  0.852059 -0.854179 -0.827516 -0.801528
2  0.986987  0.593490  1.668858  1.452601
```

也可以指定按列进行concat
```python
>>> pd.concat([df1,df2],axis=1)
          0         1         2         3         0         1         2        3       
0  1.186311  0.903929 -0.090899  1.192270 -0.993978  0.785300 -1.693622 0.388309  
1  0.073337  0.477155  0.804332 -0.027136 -0.680629 -2.018552 -0.108504 1.056134  
2  0.465125 -0.604937  0.854352 -0.798596  0.445300 -1.106076 -0.073527 1.528935   
```

**merge**

contcat是直接把两个DataFrame按行或列拼起来，而merge则是以某一列为准进行合并
```python
>>> df1 = pd.DataFrame({'ID':['K1','K2','K3','K4'],'A':[1,2,3,4],'B':[5,6,7,8]})
>>> df1
   A  B  ID
0  1  5  K1
1  2  6  K2
2  3  7  K3
3  4  8  K4

df2 = pd.DataFrame({'ID':['K1','K2','K3','K4'],'A':[10,20,30,40],'B':[50,60,70,80]})
>>> df2
    A   B  ID
0  10  50  K1
1  20  60  K2
2  30  70  K3
3  40  80  K4

>>> pd.merge(df1, df2, how='inner', on='ID')  #inner表示取交集，outer表示取并集
   A_x  B_x  ID  A_y  B_y
0    1    5  K1   10   50
1    2    6  K2   20   60
2    3    7  K3   30   70
3    4    8  K4   40   80
```


**join**

join是用索引当做基准，而不是某一列
```python
df2.join(pd.DataFrame([11,12,13,14,15],index = ['a','b','c','d','e'],columns = ['bigger']))
>>   floats  ints strs str2 str3  bigger
a     1.0     1   a1   a2   a3      11
b     2.0     2   b1   b2   b3      12
c     3.0     3   c1   c2   c3      13
d     4.0     4   d1   d2  NaN      14

df2.join(pd.DataFrame([11,12,13,14,15],index = ['a','b','c','d','e'],columns = ['bigger']),how = 'outer')
>>   floats  ints strs str2 str3  bigger
a     1.0   1.0   a1   a2   a3      11
b     2.0   2.0   b1   b2   b3      12
c     3.0   3.0   c1   c2   c3      13
d     4.0   4.0   d1   d2  NaN      14
e     NaN   NaN  NaN  NaN  NaN      15
```



**Group功能**

```python
df['odd_even'] = ['odd','even','odd','even']
>>
              No1       No2       No3     odd_even
2001-01-31  1.141374 -0.551720  0.620375      odd
2001-02-28  2.199942 -0.238879 -1.436660     even
2001-03-31 -0.641994 -1.881851 -0.032356      odd
2001-04-30  1.016377 -0.742390  1.107901     even

group = df.groupby('odd_even')
group.mean()
>>              No1       No2       No3
  odd_even                              
  even      1.608159 -0.490634 -0.164379
  odd       0.249690 -1.216786  0.294010
```

**去掉重复的值**

使用unique(),就像是取集合一样，只剩下没有重复的值

使用nunique()，可以计算不重复元素的个数
```python
>>> df = pd.DataFrame({'A':[1,2,3,4],'B':[10,20,20,30]})
>>> df
   A   B
0  1  10
1  2  20
2  3  20
3  4  30
>>> df['B'].unique()
array([10, 20, 30], dtype=int64)
>>> df['B'].nunique()
3
```

**排序**
```python
>>> df = pd.DataFrame(np.random.randn(3,4),columns=['A','B','C','D'])
>>> df
          A         B         C         D
0  0.312312 -0.930245 -0.561074  0.166258
1 -0.544118  0.041781 -1.892215  0.116129
2  0.185481  0.813738 -0.256534  0.486070

>>> df.sort_values('A')
          A         B         C         D
1 -0.544118  0.041781 -1.892215  0.116129
2  0.185481  0.813738 -0.256534  0.486070
0  0.312312 -0.930245 -0.561074  0.166258
```



## pandas与numpy的转换

用pandas虽然方便，但pandas确实太难了，在某些应用中，可以把pandas转成numpy进行相互转换，提高处理速度和易操作性。

把numpy转成pandas
```python
np1 = np.random.standard_normal((4,3)).round(6)
df = pd.DataFrame(np1)
>>     0         1         2
0  1.517890 -0.232167 -1.017305
1 -1.199564 -1.452224  0.566375
2 -0.141712  1.548404 -0.788755
3 -0.334226  1.532771  0.296157
```
对df进行简单处理
```python
df.columns = [['No1', 'No2', 'No3']]
dates = pd.date_range('2001-1-1', periods=4, freq='M')
df.index = dates
df.sum()
df.mean()
df.cumsum()
df.describe()
np.sqrt(df)
df.cumsum().plot(lw=2.0) #可直接画图
```

把pandas转成numpy
```python
np2 = np.array(df)
>>array([[1., 1.],
       [2., 2.],
       [3., 3.],
       [4., 4.]])
```


## 文件读写

**csv文件**

对于大数据的处理，不可能像上面那样手动创建一个DataFrame，往往是通过csv文件导入。读取csv文件的方式：
```python
data = pd.read_csv(‘data.csv’)
```
导出成csv文件：
```python
data.to_csv(‘new.csv’)
```

如果把一个文件先读进来在写入到另一个文件，会发现两个文件大小不一样，打开也会发现新导出的文件多了一列。即index列，这是因为在读入文件时，会默认添加index列，序列为0,1,2...

如果数据文件中已经有index，可以自行指定，这样就不会再增加一列了。
```python
data = pd.read_csv('data.csv', index_col = ['idx'])
```

**excel文件**

跟csv文件的读写非常类似

```python
pd.read_excel('data.xlsx', sheet_name='Sheet1')
```
需要注意的是，pandas只能读取excel中的数据，其他对象，比如图片公式等是不能读入的。上面函数参数sheet_name='Sheet1'表示只读取Sheet1工作表中的内容。

写文件
```python
data.to_excel('new.xlsx', sheet_name='Sheet1')
```

