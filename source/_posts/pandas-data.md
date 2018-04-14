---
title: 数据分析----pandas数据处理
date: 2018-02-13 22:10:14
tags:
---




## 1、删除重复元素[]

使用duplicated()函数检测重复的行，返回元素为布尔类型的Series对象，每个元素对应一行，如果该行不是第一次出现，则元素为True

*   使用drop_duplicates()函数删除重复的行

*   使用duplicate()函数查看重复的行

如果使用``pd.concat([df1,df2],axis = 1)``生成新的DataFrame，新的df中columns相同，使用duplicate()和drop_duplicates()都会出问题

2. 映射
  映射的含义：创建一个映射关系列表，把values元素和一个特定的标签或者字符串绑定

包含三种操作：

- replace()函数：替换元素
- 最重要：map()函数：新建一列
- rename()函数：替换索引

### 1) replace()函数：替换元素
使用replace()函数，对values进行替换操作

Series替换操作
- 单值替换
    - 普通替换
    - 字典替换
- 多值替换
    - 列表替换
    - 字典替换（推荐）

Series参数说明：
- method：对指定的值使用相邻的值填充
- limit：设定填充次数

DataFrame替换操作
- 单值替换
    - 普通替换
    - 按列指定单值替换{列标签：替换值}
- 多值替换
    - 列表替换
    - 单字典替换（推荐）

**注意**：DataFrame中，无法使用method和limit参数

### 2) map()函数：新建一列

- map()可以映射新一列数据
- map()中可以使用lambd表达式
- map()中可以使用方法，可以是自定义的方法
  **注意:** 
- map()中不能使用sum之类的函数，for循环
- map(字典) 字典的键要足以匹配所有的数据，否则出现NaN
### 3)transform()和map()类似
- transform()参数只能是函数，不能是字典
### 4) rename()函数：替换索引
仍然是新建一个字典
使用rename()函数替换行索引
- mapper替换所有索引
- index 替换行索引
- columns 替换列索引
- level 指定多维索引的维度

## 3. 使用聚合操作对数据异常值检测和过滤
- 使用describe()函数查看每一列的描述性统计量

- 使用std()函数可以求得DataFrame对象每一列的标准差，默认是求列

- 根据每一列或行的标准差，对DataFrame元素进行过滤。

- 借助any()或all()函数, 测试是否有True，有一个或以上返回True，反之返回False

- 对每一列应用筛选条件,去除标准差太大的数据

- 删除特定索引``df.drop(labels,inplace = True)``


## 4. 排序
使用.take()函数排序
- take()函数接受一个索引列表，用数字表示
- eg:df.take([1,3,4,2,5])
  可以借助np.random.permutation()函数随机排序

随机抽样
当DataFrame规模足够大时，直接使用np.random.randint()函数，就配合take()函数实现随机抽样

### 5. 数据分类处理【重点】
数据聚合是数据处理的最后一步，通常是要使每一个数组生成一个单一的数值。

数据分类处理：

- 分组：先把数据分为几组
  用函数处理：为不同组的数据应用不同的函数以转换数据
- 合并：把不同组得到的结果合并起来
  数据分类处理的核心：

 - groupby()函数
 - groups属性查看分组情况

```
df = DataFrame({'item':['苹果','香蕉','橘子','香蕉','橘子','苹果'], 'price':[4,3,3,2.5,4,2], 'color':['red','yellow','yellow','green','green','green'], 'weight':[12,20,50,30,20,44]})
```

根据item分组,查看结果
获取weight的总和
把总和跟df进行merge合并
使用列表进行多列分组，得到的结果是多层级索引

```
假设菜市场张大妈在卖菜，有以下属性：
菜品(item)：萝卜，白菜，辣椒，冬瓜
颜色(color)：白，青，红
重量(weight)
价格(price)

以属性为列索引，创建一个DataFrame对象df
data = {
    '菜品':['萝卜','萝卜','白菜','辣椒','白菜','辣椒','萝卜','冬瓜'],
    'color':['white','red','white','red','green','green','green','green'],
    'price':[2,1.5,1,4,3,5,8,2.3],
    'weight':[100,50,30,50,36,80,75,40],
}
df = DataFrame(data=data)
对df进行聚合操作，求出颜色为白色的价格总和
df.groupby('color')['price'].sum().loc['white']
对df进行聚合操作，求出萝卜的所有重量(包括白萝卜，胡萝卜，青萝卜）以及平均价格
total_weight = DataFrame(df.groupby('菜品')['weight'].sum())
mean_price = DataFrame(df.groupby('菜品')['price'].mean())
使用merge合并总重量及平均价格
table1 = pd.merge(df,mean_price,left_on='菜品',right_index=True,suffixes=['','_mean'])
pd.merge(table1,total_weight,left_on='菜品',right_index=True,suffixes=['','_sum']).rename(columns={'weight_sum':'toal'})

```


## 6.0 高级数据聚合
使用groupby分组后，也可以使用transform和apply提供自定义函数实现更多的运算
- df.groupby('item')['price'].sum() <==> df.groupby('item')['price'].apply(sum)
- transform和apply都会进行运算，在transform或者apply中传入函数名即可
- transform和apply也可以传入一个lambda表达式
  **注意**
- transform 会自动匹配列索引返回值，不去重
- apply 会根据分组情况返回值，去重


[数据处理IPython下载](https://github.com/yuansuixin/learning_data "数据处理")

[Apple公司相关数据分析](https://github.com/yuansuixin/Apple-Stock-DataAnalysis "Apple公司相关数据分析")

[美国竞选数据分析](https://github.com/yuansuixin/USA-Election-DataAnalysis "美国竞选数据分析")