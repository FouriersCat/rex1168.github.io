---
youku_id: 
youtube_id: 
qq_video_id: 
b_av: 
b_page: 1
title: Python文件操作
description: "."
chapter: 1
category: python-basic
post-headings:
author-link: #
no-video: true
publish-date: 2019-08-25
thumbnail: /static/img/course_cover-small/python.png
index: 5
---



&emsp;&emsp;在python的使用中，我们经常会对文件进行读写，本节介绍几种常用的文件读写方式和如何提高读写效率。

### 1. 一般数据读写

***'r'和‘rb'的区别***

&emsp;&emsp;用python中的write()和read()函数来实现简单的读写文件操作。
```
path = './data/'
fid = open('data0.txt','w')
fid.write('abcdedg')
fid.close()
fid = open('data0.txt','r')
a = fid.read()
fid.close()
print(a)
```

文件使用方式标识：
 - 'r':默认值，表示从文件读取数据。
 - 'w':表示要向文件写入数据，并截断以前的内容
 - 'a':表示要向文件写入数据，添加到当前内容尾部
 - 'r+':表示对文件进行可读写操作（删除以前的所有数据）
 - 'r+a'：表示对文件可进行读写操作（添加到当前文件尾部）
 - 'rb':表示要读写二进制数据

&emsp;&emsp;这里重点说一下'r'和'rb'的区别，'rb'是表示读取二进制文件，用'r'的方式进行读文件操作时，直到读到文档结束符（EOF）才算读取到文件最后，Python会认为字节\x1A转换成的字符为文档结束符（EOF）。所以如果我们的数据文件中存在'0x1a'这个数据，那读到这里Python就认为结束，后面的数据就不再读了。而用'rb’则没有这个问题，会一直读到文件结束。
&emsp;&emsp;所以，如果是纯数据文件，我们就选择为'rb'形式，如果不是纯数据文件，就选择为'r'的形式。

     

### 2. pickle操作    
&emsp;&emsp;pickle提供了一个简单的持久化功能。可以将对象以文件的形式存放在磁盘上。python中几乎所有的数据类型（列表，字典，集合，类等）都可以用pickle来序列化，

&emsp;&emsp;pickle序列化后的数据，可读性差，人一般无法识别。
```
import pickle
# 写字典
a = {'a1':[1,2,3],
     'a2':[4,5,6]}
pkl_file = open(path +'data1.pkl','wb')
pickle.dump(a, pkl_file)
pkl_file.close()
    
pkl_file = open(path + 'data1.pkl','rb')
b = pickle.load(pkl_file)
print(b)
# 写数据文件
a = [gauss(1,2) for i in range(100)]

pkl_file = open(path + 'data.pkl', 'wb')
pickle.dump(a, pkl_file)
pkl_file.close()
    
pkl_file = open(path + 'data.pkl','rb')
b = pickle.load(pkl_file)
pkl_file.close()
print(b)
    
# 写多个对象
pkl_file = open(path +'data2.pkl', 'wb')
#时间明显减少，说明写numpy数组比列表要快很多
pickle.dump(np.array(a), pkl_file) 
pickle.dump(np.array(a)*2, pkl_file)
pkl_file.close()
    
pkl_file = open(path + 'data2.pkl', 'rb')
# 按fifo的方式进行存储，dump了几次，就可以load几次
b1 = pickle.load(pkl_file)
b2 = pickle.load(pkl_file)
pkl_file.close()

# 用字典的方式一起写入
pkl_file = open(path + 'data3.pkl','wb')
data = pickle.dump({'b1':b1,'b2':b2},pkl_file)
pkl_file.close()
    
pkl_file = open(path + 'data3.pkl', 'rb')
b3 = pickle.
```
### 3. csv操作
&emsp;&emsp;逗号分隔值（Comma-Separated Values，CSV，有时也称为字符分隔值，因为分隔字符也可以不是逗号），其文件以纯文本形式存储表格数据（数字和文本）。可以用notepad直接打开，也可以用Excel打开。
```
rows = 100
a = np.random.standard_normal((rows, 3)).round(5)
t0 = pd.date_range(start = '2010/6/1', periods = rows, freq = 'H')
csv_file = open(path + 'data.csv', 'w')
header = 'data, no1, no2, no3\n'
csv_file.write(header)

for t,(no1,no2,no3) in zip(t0,a):
    s = '%s,%f,%f,%f\n' % (t,no1, no2, no3)
    csv_file.write(s)
csv_file.close() 
    
csv_file = open(path + 'data.csv','r')
# b = csv_file.read() #全部读出
# b1 = csv_file.readlines()#全部读出
b2 = csv_file.readline() #读一行
csv_file.close()
```

### 4. sqlite操作
&emsp;&emsp;python还可以对数据库直接进行操作，如创建、修改、读取等。
```
import sqlite3 as sq3 
conn = sq3.connect(path + 'data.db')
query = 'CREATE TABLE Members (Name varchar(20), Age int, Height int)'
conn.execute(query)
conn.commit()
    
conn.execute("INSERT INTO Members(Name, Age, Height) VALUES('Jim',20,180)")
conn.execute("INSERT INTO Members(Name, Age, Height) VALUES('John',24,178)")
conn.execute("INSERT INTO Members(Name, Age, Height) VALUES('Penny',18,168)")
conn.execute("INSERT INTO Members(Name, Age, Height) VALUES('Harry',30,175)")
conn.commit()

a = conn.execute('SELECT * FROM Members').fetchmany(2)
b = conn.execute('select * from members').fetchall()
c = conn.execute('SELECT * FROM Members')
c.fetchone()
c.fetchone()
conn.close()
```

### 5. numpy的IO
&emsp;&emsp;作为最常用的python库numpy当然也会提供文件读写操作，而且如果跟其他方式的IO相比，numpy提供的IO操作速度要快很多。
```
tm = np.arange('2010-01-01 09:30:00', '2010-01-03 15:00:00', dtype = 'datetime64[m]')
len(tm)

dt = np.dtype([('Time','datetime64[m]'),('No1','f'),('No2','f'),('No3','f')])
data = np.zeros(len(tm), dtype = dt)

data['Time'] = tm
data['No1'] = np.random.standard_normal((len(tm))).round(4)
data['No2'] = np.random.standard_normal((len(tm))).round(4)
data['No3'] = np.random.standard_normal((len(tm))).round(4)

# 用numpy的方式要快很多
np.save(path + 'np_data', data)
a = np.load(path + 'np.data.npy')
```

### 6. pandas的IO
&emsp;&emsp;数据库在某种程度上看起来很类似pandas的DataFrame，而pandas中也提供了对数据的处理的函数。
```
# pandas的SQLIITE
import sqlite3 as sq3 
conn = sq3.connect(path + 'data1.db')
query = 'CREATE TABLE Num (No1 real, No2 real, No3 real)'
conn.execute(query)
conn.commit()
    
num = np.random.standard_normal((100,3)).round(4)
conn.executemany("INSERT INTO Num VALUES(?,?,?)",num)
conn.commit()
conn.close()
    
import pandas.io.sql as pds 
conn = sq3.connect(path + 'data1.db')
data = pds.read_sql('select * from Num', conn)
print(data.head())
conn.close()
```
&emsp;&emsp;pandas的HDF文件操作：    
&emsp;&emsp;HDF格式非常适合保存层次化的结构数据集合，是用于存储和分发科学数据的一种自我描述、多对象文件格式。HDF是由美国国家超级计算应用中心NCSA(全称：National Center for Supercomputing Application）创建的，为了满足各种领域研究需求而研制的一种能高效存储和分发科学数据的新型数据格式。HDF可以表示出科学数据存储和分布的许多必要条件。
```
h5s = pd.HDFStore(path+'data.h5s','w')
h5s['data'] = data
h5s.close()
    
h5s = pd.HDFStore(path+'data.h5s','r')
temp = h5s['data']
h5s.close()
```
pandas对csv和excel文件的操作：
```
# pandas读写csv文件
data.to_csv(path +　'data1.csv')
pd.read_csv(path + 'data1.csv')
    
# pandas读写excel
data.to_excel(path + 'data2.xlsx')
pd.read_excel(path + 'data2.xlsx').cumsum.plot()
```

### 7. pytable操作
&emsp;&emsp;pytable也是经常用到的文件读写的python库，后面两节的数据压缩和外存计算都是用的pytable库。    
下面先来看不进行压缩的操作。
```
import tables as tb
    
# 不进行压缩
fid = tb.open_file('data.h5','w')
data = {'No1':tb.IntCol(pos = 1),
        'No2':tb.IntCol(pos = 2),
        'No3':tb.FloatCol(pos = 3)}
rows = 100
filt = tb.Filters(complevel = 0) #不进行压缩
tab = fid.create_table('/','data_rand', data, title = 'rand of data', expectedrows = rows, filters = filt)
row_idx = tab.row
rand_int = np.random.randint(0,rows,size = (rows,2))
rand_float = np.random.standard_normal((rows,1))
for i in range(rows):
    row_idx['No1'] = rand_int[i,0]
    row_idx['No2'] = rand_int[i,1]
    row_idx['No3'] = rand_float[i]
    row_idx.append()  #将前面定义的三个添加到row_idx中
tab.flush() #类似于前面的commit
    
a = tab.read()
fid.close()
```

### 8. 数据压缩操作

&emsp;&emsp;将上面的程序稍作修改，执行完成后对比产生的两个文件的大小。
```
# 压缩
fid = tb.open_file('data.h5c','w')
data = {'No1':tb.IntCol(pos = 1),
        'No2':tb.IntCol(pos = 2),
        'No3':tb.FloatCol(pos = 3)}
rows = 100
filt = tb.Filters(complevel = 4，complib='blosc') #不进行压缩
tab = fid.create_table('/','data_rand', data, title = 'rand of data', expectedrows = rows, filters = filt)
row_idx = tab.row
rand_int = np.random.randint(0,rows,size = (rows,2))
rand_float = np.random.standard_normal((rows,1))
for i in range(rows):
    row_idx['No1'] = rand_int[i,0]
    row_idx['No2'] = rand_int[i,1]
    row_idx['No3'] = rand_float[i]
    row_idx.append()  #将前面定义的三个添加到row_idx中
tab.flush() #类似于前面的commit
    
a = tab.read()
fid.close()
```
### 9. 内存外计算
&emsp;&emsp;当数据文件比较大时，我们的计算机内存空间不够用，就可以尝试在硬盘上直接对文件进行操作。
```
fid = tb.open_file('Earray.h5','w')
n = 100
ary = tb.createEArray('/','rand_float',atom = tb.Float64Atom(),shape = (0,n)) #第一维是可变的，第二维固定为n
rand_float = np.random.standard_normal((n,n))
for i in range(n):
    ary.append(rand_float)

ary.flush()
ary.size_on_disk

res = fid.createEArray('/','res',atom = tb.Float64Atom(),shape = (0,n))
expr = tb.Expr('rand_float**2')
expr.set_output(res,append_mode = True)
expr.eval()
```








