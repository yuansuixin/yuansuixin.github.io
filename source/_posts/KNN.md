---
title: 机器学习算法剖析----KNN近邻算法
date: 2018-04-13 19:35:40
tags: 机器学习算法
---




# K-近邻算法（KNN）

K nearest neighbour

- 说明：本文采用的是jupyter-notebook环境，我直接贴的IPython代码，小伙伴们要到jypyter-notebook上运行哟！:smile:
- 案例下载[案例](https://github.com/yuansuixin/k-Nearest-Neighbor "案例")

## 0、导引

### 如何进行电影分类
众所周知，电影可以按照题材分类，然而题材本身是如何定义的?由谁来判定某部电影属于哪
个题材?也就是说同一题材的电影具有哪些公共特征?这些都是在进行电影分类时必须要考虑的问
题。没有哪个电影人会说自己制作的电影和以前的某部电影类似，但我们确实知道每部电影在风格
上的确有可能会和同题材的电影相近。那么动作片具有哪些共有特征，使得动作片之间非常类似，
而与爱情片存在着明显的差别呢？动作片中也会存在接吻镜头，爱情片中也会存在打斗场景，我们
不能单纯依靠是否存在打斗或者亲吻来判断影片的类型。但是爱情片中的亲吻镜头更多，动作片中
的打斗场景也更频繁，基于此类场景在某部电影中出现的次数可以用来进行电影分类。

本章介绍第一个机器学习算法：K-近邻算法，它非常有效而且易于掌握。

## 1、k-近邻算法原理

简单地说，K-近邻算法采用测量不同特征值之间的距离方法进行分类。

- 优点：精度高、对异常值不敏感、无数据输入假定。
- 缺点：时间复杂度高、空间复杂度高。
- 适用数据范围：数值型和标称型。

### 工作原理

存在一个样本数据集合，也称作训练样本集，并且样本集中每个数据都存在标签，即我们知道样本集中每一数据
与所属分类的对应关系。输人没有标签的新数据后，将新数据的每个特征与样本集中数据对应的
特征进行比较，然后算法提取样本集中特征最相似数据（最近邻）的分类标签。一般来说，我们
只选择样本数据集中前K个最相似的数据，这就是K-近邻算法中K的出处,通常*K是不大于20的整数。
最后 ，选择K个最相似数据中出现次数最多的分类，作为新数据的分类*。

回到前面电影分类的例子，使用K-近邻算法分类爱情片和动作片。有人曾经统计过很多电影的打斗镜头和接吻镜头，下图显示了6部电影的打斗和接吻次数。假如有一部未看过的电影，如何确定它是爱情片还是动作片呢？我们可以使用K-近邻算法来解决这个问题。

![Untitled-1-2018413192352](http://p693ase25.bkt.clouddn.com/Untitled-1-2018413192352.PNG)


首先我们需要知道这个未知电影存在多少个打斗镜头和接吻镜头，上图中问号位置是该未知电影出现的镜头数图形化展示，具体数字参见下表。
![Untitled-1-201841319243](http://p693ase25.bkt.clouddn.com/Untitled-1-201841319243.PNG)



即使不知道未知电影属于哪种类型，我们也可以通过某种方法计算出来。首先计算未知电影与样本集中其他电影的距离，如图所示。

![Untitled-1-2018413192410](http://p693ase25.bkt.clouddn.com/Untitled-1-2018413192410.PNG)



现在我们得到了样本集中所有电影与未知电影的距离，按照距离递增排序，可以找到K个距
离最近的电影。假定k=3，则三个最靠近的电影依次是California Man、He's Not Really into Dudes、Beautiful Woman。K-近邻算法按照距离最近的三部电影的类型，决定未知电影的类型，而这三部电影全是爱情片，因此我们判定未知电影是爱情片。

### 欧几里得距离(Euclidean Distance)

欧氏距离是最常见的距离度量，衡量的是多维空间中各个点之间的绝对距离。公式如下：

![Untitled-1-2018413192416](http://p693ase25.bkt.clouddn.com/Untitled-1-2018413192416.png)

## 2、在scikit-learn库中使用k-近邻算法

- 分类问题：from sklearn.neighbors import KNeighborsClassifier

- 回归问题：from sklearn.neighbors import KNeighborsRegressor

### 0）一个最简单的例子

身高、体重、鞋子尺码数据对应性别


```python
# 处理分类问题
from sklearn.neighbors import KNeighborsClassifier
# 处理回归问题
from sklearn.neighbors import KNeighborsRegressor
```


```python
# 构建KNN分类器对象
# n_neighbors应设置为一个奇数，表示距离预测样本最近的n个样本点
knnclf = KNeighborsClassifier(n_neighbors=3)
```


```python
import numpy as np
import pandas as pd
from pandas import Series,DataFrame
```


```python
# X_train必须是一个列向量（二维数组）
X_train = np.array([[19,1],[2,18],[25,1],[24,3],[3,17]])
y_train = np.array(['动作','爱情','动作','动作','爱情'])
display(X_train,y_train)
```


    array([[19,  1],
           [ 2, 18],
           [25,  1],
           [24,  3],
           [ 3, 17]])



    array(['动作', '爱情', '动作', '动作', '爱情'], dtype='<U2')



```python
# 训练分类器模型
knnclf.fit(X_train,y_train)
```



    KNeighborsClassifier(algorithm='auto', leaf_size=30, metric='minkowski',
               metric_params=None, n_jobs=1, n_neighbors=3, p=2,
               weights='uniform')




```python
X_test = np.array([[13,10],[5,10]])
knnclf.predict(X_test)
```




    array(['动作', '爱情'], dtype='<U2')



### 1）用于分类

导包，机器学习的算法KNN、数据鸢尾花

```python
# load_iris是机器学习库提供给我们研究算法的数据
from sklearn.datasets import load_iris
```

获取训练样本


```python
iris = load_iris()
iris
```




    {'DESCR': 'Iris Plants Database\n====================\n\nNotes\n-----\nData Set Characteristics:\n    :Number of Instances: 150 (50 in each of three classes)\n    :Number of Attributes: 4 numeric, predictive attributes and the class\n    :Attribute Information:\n        - sepal length in cm\n        - sepal width in cm\n        - petal length in cm\n        - petal width in cm\n        - class:\n                - Iris-Setosa\n                - Iris-Versicolour\n                - Iris-Virginica\n    :Summary Statistics:\n\n    ============== ==== ==== ======= ===== ====================\n                    Min  Max   Mean    SD   Class Correlation\n    ============== ==== ==== ======= ===== ====================\n    sepal length:   4.3  7.9   5.84   0.83    0.7826\n    sepal width:    2.0  4.4   3.05   0.43   -0.4194\n    petal length:   1.0  6.9   3.76   1.76    0.9490  (high!)\n    petal width:    0.1  2.5   1.20  0.76     0.9565  (high!)\n    ============== ==== ==== ======= ===== ====================\n\n    :Missing Attribute Values: None\n    :Class Distribution: 33.3% for each of 3 classes.\n    :Creator: R.A. Fisher\n    :Donor: Michael Marshall (MARSHALL%PLU@io.arc.nasa.gov)\n    :Date: July, 1988\n\nThis is a copy of UCI ML iris datasets.\nhttp://archive.ics.uci.edu/ml/datasets/Iris\n\nThe famous Iris database, first used by Sir R.A Fisher\n\nThis is perhaps the best known database to be found in the\npattern recognition literature.  Fisher\'s paper is a classic in the field and\nis referenced frequently to this day.  (See Duda & Hart, for example.)  The\ndata set contains 3 classes of 50 instances each, where each class refers to a\ntype of iris plant.  One class is linearly separable from the other 2; the\nlatter are NOT linearly separable from each other.\n\nReferences\n----------\n   - Fisher,R.A. "The use of multiple measurements in taxonomic problems"\n     Annual Eugenics, 7, Part II, 179-188 (1936); also in "Contributions to\n     Mathematical Statistics" (John Wiley, NY, 1950).\n   - Duda,R.O., & Hart,P.E. (1973) Pattern Classification and Scene Analysis.\n     (Q327.D83) John Wiley & Sons.  ISBN 0-471-22361-1.  See page 218.\n   - Dasarathy, B.V. (1980) "Nosing Around the Neighborhood: A New System\n     Structure and Classification Rule for Recognition in Partially Exposed\n     Environments".  IEEE Transactions on Pattern Analysis and Machine\n     Intelligence, Vol. PAMI-2, No. 1, 67-71.\n   - Gates, G.W. (1972) "The Reduced Nearest Neighbor Rule".  IEEE Transactions\n     on Information Theory, May 1972, 431-433.\n   - See also: 1988 MLC Proceedings, 54-64.  Cheeseman et al"s AUTOCLASS II\n     conceptual clustering system finds 3 classes in the data.\n   - Many, many more ...\n',
     'data': array([[5.1, 3.5, 1.4, 0.2],
            [4.9, 3. , 1.4, 0.2],
            [4.7, 3.2, 1.3, 0.2],
            [4.6, 3.1, 1.5, 0.2],
            [5. , 3.6, 1.4, 0.2],
            [5.4, 3.9, 1.7, 0.4],
            [4.6, 3.4, 1.4, 0.3],
            [5. , 3.4, 1.5, 0.2],
            [4.4, 2.9, 1.4, 0.2],
            [4.9, 3.1, 1.5, 0.1],
            [5.4, 3.7, 1.5, 0.2],
            [4.8, 3.4, 1.6, 0.2],
            [4.8, 3. , 1.4, 0.1],
            [4.3, 3. , 1.1, 0.1],
            [5.8, 4. , 1.2, 0.2],
            [5.7, 4.4, 1.5, 0.4],
            [5.4, 3.9, 1.3, 0.4],
            [5.1, 3.5, 1.4, 0.3],
            [5.7, 3.8, 1.7, 0.3],
            [5.1, 3.8, 1.5, 0.3],
            [5.4, 3.4, 1.7, 0.2],
            [5.1, 3.7, 1.5, 0.4],
            [4.6, 3.6, 1. , 0.2],
            [5.1, 3.3, 1.7, 0.5],
            [4.8, 3.4, 1.9, 0.2],
            [5. , 3. , 1.6, 0.2],
            [5. , 3.4, 1.6, 0.4],
            [5.2, 3.5, 1.5, 0.2],
            [5.2, 3.4, 1.4, 0.2],
            [4.7, 3.2, 1.6, 0.2],
            [4.8, 3.1, 1.6, 0.2],
            [5.4, 3.4, 1.5, 0.4],
            [5.2, 4.1, 1.5, 0.1],
            [5.5, 4.2, 1.4, 0.2],
            [4.9, 3.1, 1.5, 0.1],
            [5. , 3.2, 1.2, 0.2],
            [5.5, 3.5, 1.3, 0.2],
            [4.9, 3.1, 1.5, 0.1],
            [4.4, 3. , 1.3, 0.2],
            [5.1, 3.4, 1.5, 0.2],
            [5. , 3.5, 1.3, 0.3],
            [4.5, 2.3, 1.3, 0.3],
            [4.4, 3.2, 1.3, 0.2],
            [5. , 3.5, 1.6, 0.6],
            [5.1, 3.8, 1.9, 0.4],
            [4.8, 3. , 1.4, 0.3],
            [5.1, 3.8, 1.6, 0.2],
            [4.6, 3.2, 1.4, 0.2],
            [5.3, 3.7, 1.5, 0.2],
            [5. , 3.3, 1.4, 0.2],
            [7. , 3.2, 4.7, 1.4],
            [6.4, 3.2, 4.5, 1.5],
            [6.9, 3.1, 4.9, 1.5],
            [5.5, 2.3, 4. , 1.3],
            [6.5, 2.8, 4.6, 1.5],
            [5.7, 2.8, 4.5, 1.3],
            [6.3, 3.3, 4.7, 1.6],
            [4.9, 2.4, 3.3, 1. ],
            [6.6, 2.9, 4.6, 1.3],
            [5.2, 2.7, 3.9, 1.4],
            [5. , 2. , 3.5, 1. ],
            [5.9, 3. , 4.2, 1.5],
            [6. , 2.2, 4. , 1. ],
            [6.1, 2.9, 4.7, 1.4],
            [5.6, 2.9, 3.6, 1.3],
            [6.7, 3.1, 4.4, 1.4],
            [5.6, 3. , 4.5, 1.5],
            [5.8, 2.7, 4.1, 1. ],
            [6.2, 2.2, 4.5, 1.5],
            [5.6, 2.5, 3.9, 1.1],
            [5.9, 3.2, 4.8, 1.8],
            [6.1, 2.8, 4. , 1.3],
            [6.3, 2.5, 4.9, 1.5],
            [6.1, 2.8, 4.7, 1.2],
            [6.4, 2.9, 4.3, 1.3],
            [6.6, 3. , 4.4, 1.4],
            [6.8, 2.8, 4.8, 1.4],
            [6.7, 3. , 5. , 1.7],
            [6. , 2.9, 4.5, 1.5],
            [5.7, 2.6, 3.5, 1. ],
            [5.5, 2.4, 3.8, 1.1],
            [5.5, 2.4, 3.7, 1. ],
            [5.8, 2.7, 3.9, 1.2],
            [6. , 2.7, 5.1, 1.6],
            [5.4, 3. , 4.5, 1.5],
            [6. , 3.4, 4.5, 1.6],
            [6.7, 3.1, 4.7, 1.5],
            [6.3, 2.3, 4.4, 1.3],
            [5.6, 3. , 4.1, 1.3],
            [5.5, 2.5, 4. , 1.3],
            [5.5, 2.6, 4.4, 1.2],
            [6.1, 3. , 4.6, 1.4],
            [5.8, 2.6, 4. , 1.2],
            [5. , 2.3, 3.3, 1. ],
            [5.6, 2.7, 4.2, 1.3],
            [5.7, 3. , 4.2, 1.2],
            [5.7, 2.9, 4.2, 1.3],
            [6.2, 2.9, 4.3, 1.3],
            [5.1, 2.5, 3. , 1.1],
            [5.7, 2.8, 4.1, 1.3],
            [6.3, 3.3, 6. , 2.5],
            [5.8, 2.7, 5.1, 1.9],
            [7.1, 3. , 5.9, 2.1],
            [6.3, 2.9, 5.6, 1.8],
            [6.5, 3. , 5.8, 2.2],
            [7.6, 3. , 6.6, 2.1],
            [4.9, 2.5, 4.5, 1.7],
            [7.3, 2.9, 6.3, 1.8],
            [6.7, 2.5, 5.8, 1.8],
            [7.2, 3.6, 6.1, 2.5],
            [6.5, 3.2, 5.1, 2. ],
            [6.4, 2.7, 5.3, 1.9],
            [6.8, 3. , 5.5, 2.1],
            [5.7, 2.5, 5. , 2. ],
            [5.8, 2.8, 5.1, 2.4],
            [6.4, 3.2, 5.3, 2.3],
            [6.5, 3. , 5.5, 1.8],
            [7.7, 3.8, 6.7, 2.2],
            [7.7, 2.6, 6.9, 2.3],
            [6. , 2.2, 5. , 1.5],
            [6.9, 3.2, 5.7, 2.3],
            [5.6, 2.8, 4.9, 2. ],
            [7.7, 2.8, 6.7, 2. ],
            [6.3, 2.7, 4.9, 1.8],
            [6.7, 3.3, 5.7, 2.1],
            [7.2, 3.2, 6. , 1.8],
            [6.2, 2.8, 4.8, 1.8],
            [6.1, 3. , 4.9, 1.8],
            [6.4, 2.8, 5.6, 2.1],
            [7.2, 3. , 5.8, 1.6],
            [7.4, 2.8, 6.1, 1.9],
            [7.9, 3.8, 6.4, 2. ],
            [6.4, 2.8, 5.6, 2.2],
            [6.3, 2.8, 5.1, 1.5],
            [6.1, 2.6, 5.6, 1.4],
            [7.7, 3. , 6.1, 2.3],
            [6.3, 3.4, 5.6, 2.4],
            [6.4, 3.1, 5.5, 1.8],
            [6. , 3. , 4.8, 1.8],
            [6.9, 3.1, 5.4, 2.1],
            [6.7, 3.1, 5.6, 2.4],
            [6.9, 3.1, 5.1, 2.3],
            [5.8, 2.7, 5.1, 1.9],
            [6.8, 3.2, 5.9, 2.3],
            [6.7, 3.3, 5.7, 2.5],
            [6.7, 3. , 5.2, 2.3],
            [6.3, 2.5, 5. , 1.9],
            [6.5, 3. , 5.2, 2. ],
            [6.2, 3.4, 5.4, 2.3],
            [5.9, 3. , 5.1, 1.8]]),
     'feature_names': ['sepal length (cm)',
      'sepal width (cm)',
      'petal length (cm)',
      'petal width (cm)'],
     'target': array([0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0,
            0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0,
            0, 0, 0, 0, 0, 0, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1,
            1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1,
            1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2,
            2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2,
            2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2]),
     'target_names': array(['setosa', 'versicolor', 'virginica'], dtype='<U10')}




```python
data = iris.data
target = iris.target
target_names = iris.target_names
feature_names = iris.feature_names
```


```python
features = DataFrame(data=data,columns = feature_names)
features.head()
```

```python
features.iloc[:,0].std()
```


    0.828066127977863
```python
features.iloc[:,2].std()
```




    1.7644204199522626
```python
features.iloc[:,1].std()
```


    0.4335943113621737
```python
features.iloc[:,3].std()
```




    0.7631607417008411




```python
# samples(训练集、测试集)
X_train = features.iloc[:130,2:4]
y_train = target[:130]

# 测试集(验证训练模型的准确度)
X_test = features.iloc[130:,2:4]
y_test = target[130:]
```


```python
display(X_train.shape,y_train.shape,X_test.shape,y_test.shape)
```


    (130, 2)



    (130,)



    (20, 2)



    (20,)



```python
target_names
```




    array(['setosa', 'versicolor', 'virginica'], dtype='<U10')




```python
# （离散性的、标称型）目标值是不参与运算的，所以不是必须要转换成数字的格式
target
```




    array([0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0,
           0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0,
           0, 0, 0, 0, 0, 0, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1,
           1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1,
           1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2,
           2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2,
           2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2])



绘制图形


```python
import matplotlib.pyplot as plt
%matplotlib inline
samples = features.iloc[:,2:4]

# 展示真实数据的分类情况
plt.scatter(samples.iloc[:,0],samples.iloc[:,1],c=target)
```


    ---------------------------------------------------------------------------

    NameError                                 Traceback (most recent call last)

    <ipython-input-2-feaf3557f814> in <module>()
          1 import matplotlib.pyplot as plt
          2 get_ipython().run_line_magic('matplotlib', 'inline')
    ----> 3 samples = features.iloc[:,2:4]
          4 
          5 # 展示真实数据的分类情况


    NameError: name 'features' is not defined


定义KNN分类器


```python
knnclf = KNeighborsClassifier(n_neighbors=5)
```

第一步，训练数据


```python
knnclf.fit(X_train,y_train)
```




    KNeighborsClassifier(algorithm='auto', leaf_size=30, metric='minkowski',
               metric_params=None, n_jobs=1, n_neighbors=5, p=2,
               weights='uniform')



第二步预测数据：，所预测的数据，自己创造，就是上面所显示图片的背景点  

生成预测数据


```python
# 模型准确度的评估
# 1. 测试样本及应该是随机的
# 2. 测试样本集数量不能太小
y_ = knnclf.predict(X_test)
```


```python
y_test
```


    array([2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2])
```python
y_
```


    array([2, 2, 2, 1, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 2])




```python
# 获取所有预测点（满屏幕的点）
xmin,xmax = samples.iloc[:,0].min(),samples.iloc[:,0].max()
ymin,ymax = samples.iloc[:,1].min(),samples.iloc[:,1].max()

x = np.linspace(xmin,xmax,100)
y = np.linspace(ymin,ymax,100)

xx,yy = np.meshgrid(x,y)

X_test = np.c_[xx.ravel(),yy.ravel()]
```

对数据进行预测


```python
y_ = knnclf.predict(X_test)
```

显示数据


```python
from matplotlib.colors import ListedColormap

cmap = ListedColormap(['#aa00ff','#00aaff','#ffaa00'])
```


```python
# 展示预测数据的分类情况
plt.scatter(X_test[:,0],X_test[:,1],c=y_,cmap=cmap)
# 展示真实数据的分类情况
plt.scatter(samples.iloc[:,0],samples.iloc[:,1],c=target)
```




    <matplotlib.collections.PathCollection at 0x189b2990>




![Untitled-1-201841319285](http://p693ase25.bkt.clouddn.com/Untitled-1-201841319285.png)


### 2）用于回归  
回归用于对趋势的预测

导包


```python
from sklearn.neighbors import KNeighborsRegressor
```

生成样本数据


```python
# 生成一组符合正弦分布的数据
x = np.linspace(-np.pi,np.pi,40)
y = np.sin(x)
# 原始数据的分布规律
plt.scatter(x,y)
```




    <matplotlib.collections.PathCollection at 0x19be92b0>







![Untitled-1-2018413192811](http://p693ase25.bkt.clouddn.com/Untitled-1-2018413192811.png)




```python
noise = np.random.random(size=20) - 0.5
noise
```




    array([-0.01419036, -0.05222776,  0.4114977 , -0.48535771,  0.4725629 ,
            0.49193969, -0.4352523 , -0.48704335,  0.39377464, -0.32509247,
            0.09969959, -0.10353899,  0.35402717,  0.09005099, -0.32349592,
           -0.41517568,  0.13719123,  0.40893228,  0.25830619,  0.00900481])




```python
y[::2] += noise
```


```python
plt.scatter(x,y)
```




    <matplotlib.collections.PathCollection at 0x18594890>







![Untitled-1-2018413192816](http://p693ase25.bkt.clouddn.com/Untitled-1-2018413192816.png)


生成测试数据的结果


```python
X_train = x.reshape(-1,1)
y_train = y
```


```python
knn = KNeighborsRegressor(n_neighbors=7)
knn.fit(X_train,y_train)

X_test = np.linspace(-np.pi,np.pi,100).reshape(-1,1)
y_ = knn.predict(X_test)

plt.plot(X_test,y_,color='green')
plt.scatter(X_train,y_train,color='orange')

# 过拟合
# 欠拟合
# 
```




    <matplotlib.collections.PathCollection at 0x19b872b0>






![Untitled-1-2018413192821](http://p693ase25.bkt.clouddn.com/Untitled-1-2018413192821.png)


第一步：生成模型，并训练数据

第二步：使用模型，预测数据

绘图显示数据

### 练习
人类动作识别  
步行，上楼，下楼，坐着，站立和躺着  

![Untitled-1-2018413193022](http://p693ase25.bkt.clouddn.com/Untitled-1-2018413193022.jpg)
数据采集每个人在腰部穿着智能手机，进行了六个活动（步行，上楼，下楼，坐着，站立和躺着）。采用嵌入式加速度计和陀螺仪，以50Hz的恒定速度捕获3轴线性加速度和3轴角速度，来获取数据

导入数据


```python
X_train = np.load('x_train.npy')
y_train = np.load('y_train.npy')

X_test = np.load('x_test.npy')
y_test = np.load('y_test.npy')

display(X_train.shape,y_train.shape,X_test.shape,y_test.shape)
```


    (7352, 561)

    (7352,)

    (2947, 561)

    (2947,)

```python
DataFrame(X_train).head(
```

```python
Series(y_train).unique()
```


    array([5, 4, 6, 1, 3, 2], dtype=int64)
```python
label = {1:'WALKING', 
         2:'WALKING UPSTAIRS', 
         3:'WALKING DOWNSTAIRS',
         4:'SITTING', 
         5:'STANDING', 
         6:'LAYING'}
```

获取数据


```python
# 调整算法参数只能对算法调优，不能决定算法的高度
knnclf = KNeighborsClassifier(n_neighbors=9)
```


```python
# KNN在训练的时候，是不进行运算的（距离）
knnclf.fit(X_train,y_train)
```




    KNeighborsClassifier(algorithm='auto', leaf_size=30, metric='minkowski',
               metric_params=None, n_jobs=1, n_neighbors=9, p=2,
               weights='uniform')




```python
# KNN在进行预测的时候，才开始计算训练集和测试集中样本点之间的距离
y_ = knnclf.predict(X_test[:1000])

# 分类算法的评分就是这么算的
(y_ == y_test[:1000]).sum()/y_.size
```




    0.922




```python
# 计算分类算法的评分
knnclf.score(X_test[:500],y_test[:500])
```

    0.928

```python
plt.figure(figsize=(16,8))
colors = ['red','yellow','blue','green','cyan','purple']
for i in range(1,7):
    plt.subplot(2,3,i)
    title = label[i]
    Series(X_train[y_train==i][0]).plot(color=colors[i-1],title=title)
```


![Untitled-1-2018413192827](http://p693ase25.bkt.clouddn.com/Untitled-1-2018413192827.png)


