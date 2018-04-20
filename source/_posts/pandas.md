---
title: 数据分析-----pandas,数据加载，透视表和交叉表
date: 2017-10-15 16:49:26
tags:
---



# 数据分析知识点

- 数据加载
- 透视表和交叉表
- pandas中的绘图函数
- 数据加载



## pandas中的绘图函数


Series和DataFrame都有一个用于生成各类图表的plot方法。默认情况下，它们所生成的是线形图

## 线形图

简单的Series图表示例,plot()


```python
import numpy as np
import pandas as pd
from pandas import Series,DataFrame

import matplotlib.pyplot as plt
%matplotlib inline
```


```python
np.arange(2010,2018,1)
```


    array([2010, 2011, 2012, 2013, 2014, 2015, 2016, 2017])
```python
s = Series(index = np.arange(2010,2018,1),data = [100,110,90,300,700,2000,5000,10000])
```


```python
s.plot()
```


![这里写图片描述](http://img.blog.csdn.net/20180415162512105?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvcHJhaXJpZTk3/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)






```python
x = np.arange(-np.pi,np.pi,0.1)
y = np.sin(x) + 2*np.cos(x)
s1 = Series(index=x,data=y)
s1.plot(kind='line')
```



![这里写图片描述](http://img.blog.csdn.net/20180415162616104?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvcHJhaXJpZTk3/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)


简单的DataFrame图表示例,plot()

- 图例的位置可能会随着数据的不同而不同


```python
columns = ['dancer','lucy','mery']
index = ['first','second','third','fouth']
data = np.random.randint(0,150,size=(4,3))
df = DataFrame(data=data,index=index,columns=columns)
df.plot()
```


![这里写图片描述](http://img.blog.csdn.net/20180415162656413?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvcHJhaXJpZTk3/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)



```python
score = pd.read_excel('score.xls')
# 版面的绘制
score.plot()
# 图片保存
```


![这里写图片描述](http://img.blog.csdn.net/20180415162715890?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvcHJhaXJpZTk3/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)


## 柱状图

Series柱状图示例,kind = 'bar'/'barh'


```python
s = Series(index=['dancer','mery','lucy'],data=[100,98,60])
s.plot(kind='bar')
```

![这里写图片描述](http://img.blog.csdn.net/20180415162738014?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvcHJhaXJpZTk3/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

DataFrame柱状图示例


```python
data = np.random.randint(0,100,size=(5,3))
index = list('ABCDE')
columns = ['python','java','php']
df = DataFrame(data=data,index=index,columns=columns)
df.plot(kind='bar')
```


![这里写图片描述](http://img.blog.csdn.net/20180415162757152?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvcHJhaXJpZTk3/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)



```python
df.plot(kind='barh')
```


![这里写图片描述](http://img.blog.csdn.net/20180415162815107?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvcHJhaXJpZTk3/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)



```python
s.plot(kind='barh')
```

![这里写图片描述](http://img.blog.csdn.net/20180415162834581?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvcHJhaXJpZTk3/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

读取文件tips.csv，查看每天的聚会人数情况
每天各种聚会规模的比例  

求和并df.sum()，注意灵活使用axis


```python
tips = pd.read_csv('tips.csv')
tips
```




```python
# 把一列变成索引
data = tips.set_index('day')
# 把索引变成列
# tips.reset_index()
```


```python
data.plot(kind='bar')
```

![这里写图片描述](http://img.blog.csdn.net/20180415162858589?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvcHJhaXJpZTk3/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)



```python
# 计算每一行的和
index_sum = data.sum(axis=1)
```


```python
# 计算每天每种聚会人数所占比例
result = data.div(index_sum,axis=0)
result.plot(kind='bar')
```

![这里写图片描述](http://img.blog.csdn.net/20180415162926626?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvcHJhaXJpZTk3/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)


## 直方图

rondom生成随机数百分比直方图，调用hist方法

- 柱高表示数据的频数，柱宽表示各组数据的组距
- 参数bins可以设置直方图方柱的个数上限，越大柱宽越小，数据分组越细致
- 设置normed参数为True，可以把频数转换为概率


```python
s = Series(data = [1,2,1,1,3,5,6,7,9,9])
# 直方图会受到bins（数据分区的个数）影响很大
# 设置normed参数为True，直方图表示的是每一个数出现的概率
s.plot(kind='hist',bins=3,normed=True)
# 为了避免歧义，直方图会跟核密度估计函数一起使用
s.plot(kind='kde')
```

![这里写图片描述](http://img.blog.csdn.net/20180415162948510?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvcHJhaXJpZTk3/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)



```python
s.plot(kind='bar')
```

![这里写图片描述](http://img.blog.csdn.net/20180415163007100?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvcHJhaXJpZTk3/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)


kde图：核密度估计，用于弥补直方图由于参数bins设置的不合理导致的精度缺失问题

#### 练习

绘制一个由两个不同的标准正态分布组成的的双峰分布


```python
# 生成两个一维数组，符合正太分布，期望值分别为0和20
x1 = Series(np.random.normal(loc=0,scale=3,size=100))
x2 = Series(np.random.normal(loc=20,scale=5,size=100))
# 级联两个Series对象并重置索引
s = pd.concat((x1,x2),ignore_index=True)
s
```




    0       3.249873
    1      -2.589494
    2      -2.247292
    3      -2.127396
    4       0.521159
    5      -6.084171
    6       1.686359
    7      -2.565004
    8      -0.647332
    9       4.161788
    10     -2.924241
    11     -1.938118
    12      1.259324
    13      0.941385
    14     -4.316892
    15      0.085348
    16     -2.213873
    17     -0.809423
    18     -2.917220
    19     -2.672856
    20     -2.066799
    21      1.865616
    22     -1.852503
    23      3.604442
    24     -1.411063
    25      0.165386
    26     -1.337435
    27      3.754387
    28     -6.199200
    29      3.019002
             ...    
    170    22.595197
    171    23.492841
    172    22.738205
    173    14.255180
    174    22.229389
    175    19.190084
    176    22.902848
    177    14.744843
    178    18.963592
    179    29.699166
    180    14.663104
    181    26.470372
    182    24.512165
    183    27.617378
    184    12.798938
    185    18.160538
    186    18.083601
    187    22.163000
    188    17.011587
    189    22.897459
    190    26.887293
    191    15.718268
    192    21.277874
    193    18.267735
    194    18.242399
    195    27.474724
    196    34.169240
    197    22.959591
    198    21.693334
    199    21.558861
    Length: 200, dtype: float64




```python
s.plot(kind='hist',normed=True)
s.plot(kind='kde')
```

![这里写图片描述](http://img.blog.csdn.net/20180415163032405?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvcHJhaXJpZTk3/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)


## 散布图

散布图
散布图是观察两个一维数据数列之间的关系的有效方法,DataFrame对象可用 

使用方法：
设置kind = 'scatter'，给明标签columns


```python
data = np.random.normal(size=(1000,4))
columns = list('ABCD')
df = DataFrame(data=data,columns=columns)
df.plot(x='A',y='B',kind='scatter')
```

![这里写图片描述](http://img.blog.csdn.net/20180415163051051?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvcHJhaXJpZTk3/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

散布图矩阵，当有多个点时，两两点的关系  

使用函数：pd.plotting.scatter_matrix(),
- 参数diagnol：设置对角线的图像类型


```python
pd.plotting.scatter_matrix(df,diagonal='kde')
```




    array([[<matplotlib.axes._subplots.AxesSubplot object at 0x15A2BDF0>,
            <matplotlib.axes._subplots.AxesSubplot object at 0x156A1AF0>,
            <matplotlib.axes._subplots.AxesSubplot object at 0x155F4EB0>,
            <matplotlib.axes._subplots.AxesSubplot object at 0x13D37570>],
           [<matplotlib.axes._subplots.AxesSubplot object at 0x1562AED0>,
            <matplotlib.axes._subplots.AxesSubplot object at 0x1562AE90>,
            <matplotlib.axes._subplots.AxesSubplot object at 0x16EAA690>,
            <matplotlib.axes._subplots.AxesSubplot object at 0x16EF41F0>],
           [<matplotlib.axes._subplots.AxesSubplot object at 0x16F17230>,
            <matplotlib.axes._subplots.AxesSubplot object at 0x16F2CF70>,
            <matplotlib.axes._subplots.AxesSubplot object at 0x16F51C70>,
            <matplotlib.axes._subplots.AxesSubplot object at 0x16F799F0>],
           [<matplotlib.axes._subplots.AxesSubplot object at 0x16F9F890>,
            <matplotlib.axes._subplots.AxesSubplot object at 0x16F22710>,
            <matplotlib.axes._subplots.AxesSubplot object at 0x16FE28F0>,
            <matplotlib.axes._subplots.AxesSubplot object at 0x170068F0>]],
          dtype=object)


![这里写图片描述](http://img.blog.csdn.net/20180415163116532?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvcHJhaXJpZTk3/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)


## 数据加载



### pandas提供了一些用于将表格型数据读取为DataFrame对象的函数，期中read_csv和read_table这两个使用最多

首先查看文本文件

- 使用read_csv将其读入

- 使用read_table读取


```python
import numpy as np
import pandas as pd
from pandas import Series,DataFrame
```


```python
data = pd.read_csv('data/type-.txt',sep='-',header=None)
data.columns = ['汇源','果汁','鸟']
data
```


```python
table = pd.read_table('data/type_comma',sep=',',header=None)
table
```



使用read_excel()读取excel表格


```python
table = pd.read_excel('data/students.xlsx',sheet_name=1)
table
```



写入excel文件


```python
table.to_excel('dancer.xlsx')
```

读取sqlite文件


导包  import sqlite3 as sqlite3

- 连接数据库  
    sqlite3.connect('dbpath')

- 读取table内容  
    pd.read_sql("SQL语句", con)

- 写入数据库文件
    df对象.to_sql('dbpath',connection)

- 操作数据库
    connection.execute(SQL语句)


```python
import sqlite3 as sqlite3
```


```python
# SQL server  MongoDB  Redis
```


```python
connection = sqlite3.connect('data/weather_2012.sqlite')
```


```python
pd.read_sql('select * from weather_2012',connection)
```


```python
table.to_sql('dancer',connection)
```


```python
pd.read_sql('select * from dancer',connection)
```


```python
# drop table dancer
connection.execute('drop table dancer')<sqlite3.Cursor at 0x130953e0>
```

```python
pd.read_sql('select * from dancer',connection)
```


    --------------------------------------------------------------------------


设置行索引index_col

写入sql文件

使用read_csv读取url获取网络上的数据
url = 'https://raw.githubusercontent.com/datasets/investor-flow-of-funds-us/master/data/weekly.csv'


```python
url = 'https://raw.githubusercontent.com/datasets/investor-flow-of-funds-us/master/data/weekly.csv'

pd.read_csv(url)
```


## 透视表和交叉表





```python
import numpy as np
import pandas as pd
from pandas import Series,DataFrame
```


```python
# df = DataFrame({'sex':['man','man','women','women','man','women','man','women','women'],
#                'age':[15,23,25,17,35,57,24,31,22],
#                'smoke':[True,False,False,True,True,False,False,True,False],
#                'height':[168,179,181,166,173,178,188,190,160]})
df = pd.DataFrame({'foo': ['one','one','one','two','two','two'],
                       'bar': ['A', 'B', 'C', 'A', 'B', 'C'],
                       'baz': [1, 2, 3, 4, 5, 6]})
```


```python
df
```


```python
df.pivot_table(index='smoke',columns='sex',aggfunc=max)
```


```python
df.pivot(index='bar',columns='foo',values='baz')
```


### 透视表

各种电子表格程序和其他数据分析软件中一种常见的数据汇总工具。它根据一个或多个键对数据进行聚合，并根据行和列上的分组键将数据分配到各个矩形区域中

行分组透视表 设置index参数


```python
df.pivot(index='sex',columns=)
```



列分组透视表 设置columns参数


```python
from pandas import pivot_table
```


```python
df = DataFrame({'sex':['man','man','women','women','man','women','man','women','women'],
               'age':[15,23,25,17,35,57,24,31,22],
               'smoke':[True,False,False,True,True,False,False,True,False],
               'height':[168,179,181,166,173,178,188,190,160]})
```


```python
pivot_table(df,values='age',index=['sex'],columns=['smoke'],aggfunc='min')
```



行列分组的透视表  同时设定index、columns参数

aggfunc：设置应用在每个区域的聚合函数，默认值为np.mean

fill_value：替换结果中的缺失值

### 交叉表

是一种用于计算分组频率的特殊透视图,对数据进行汇总

pd.crosstab(index,colums)

- index:分组数据，交叉表的行索引
- columns:交叉表的列索引


```python
a = np.array(["foo", "foo", "foo", "foo", "bar", "bar","bar", "bar", "foo", "foo", "foo"], dtype=object)
b = np.array(["one", "one", "one", "two", "one", "one","one", "two", "two", "two", "one"], dtype=object)
c = np.array(["dull", "dull", "shiny", "dull", "dull", "shiny","shiny", "dull", "shiny", "shiny", "shiny"],dtype=object)
pd.crosstab(a, [b, c], rownames=['a'], colnames=['b', 'c'])
```




```python
table = pd.read_csv('stock2015-2016.csv')
```


```python
# 'AAPL', 'MSFT', 'GE', 'IBM', 'AA', 'DAL', 'UAL', 'PEP', 'KO'
```


```python
table['Ticker'].unique()
```




    array(['AAPL', 'MSFT', 'GE', 'IBM', 'AA', 'DAL', 'UAL', 'PEP', 'KO'],
          dtype=object)




```python
按照每天查看苹果股票的收盘价格
```




```python
adj_table = table.pivot(index='Date',columns='Ticker',values='Adj Close')
```


```python
import matplotlib.pyplot as plt
%matplotlib inline
adj_table.plot()
```
![这里写图片描述](http://img.blog.csdn.net/20180415164318157?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvcHJhaXJpZTk3/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)


```python
adj_table['AAPL'].plot()
```

![这里写图片描述](http://img.blog.csdn.net/20180415164336700?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvcHJhaXJpZTk3/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)



```python
table.head()
```



```python
adj_table.head()
```


```python
table.groupby(['Ticker','Date'])['Adj Close'].mean().unstack(level=0).head()
```


[相关数据及源码下载](https://github.com/yuansuixin/learn-data-analysis "下载")




