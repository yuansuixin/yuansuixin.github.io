---
title: jupyter（IPython）相关知识点
date: 2018-02-03 09:18:44
tags:
---



## 一、启动程序

执行以下命令：
>jupyter notebook

[NotebookApp] Serving notebooks from local directory: /home/nanfengpo

[NotebookApp] 0 active kernels 

[NotebookApp] The IPython Notebook is running at: http://localhost:8888/

[NotebookApp] Use Control-C to stop this server and shut down all kernels (twice to skip confirmation).

注意以下几点：
- 打开地址为当前bash的目录，默认的根目录
- 浏览器地址为http://localhost:8888/
- 通过control -C终止jupyter程序

几个基本操作：
- 双击D：删除当前cell
- 单击M：转为markdown文档
- markdown文档下运行变为预览模式


## 二、IPython的帮助文档
#### 1.使用help（）
通过以下命令来获得帮助文档：
>help(len)

Help on built-in function len in module builtins:

len(obj, /)
    Return the number of items in a container.
```
help(len)
```
#### 2.使用？
> len？

还可以应用到自定义的变量和自定义的函数上来返回帮助文档

此外，使用两个??可以把函数的源代码显示出来

#### 3.tab自动补全

## 三、IPython魔法命令
#### 1. 运行外部的Python文件
使用下面命令运行外部python文件（默认是当前目录，最好加上绝对路径）
> %run *.py
    
例如在当前目录下有一个myscript.py文件：
```
def square(x):
    """square a number"""
    return x ** 2

for N in range(1, 4):
    print(N, "squared is", square(N))
```
我们可以通过下面命令执行它：
> %run myscript.py

尤其要注意的是，当我们使用魔法命令执行了一个外部文件时，该文件的函数就能在当前会话中使用
>square(5)

#### 2.运行计时
用下面命令计算statement的运行时间：
> %time statement

用下面命令计算statement的平均运行时间：   
> %timeit statement

timeit会多次运行statement，最后得到一个更为精准的预期运行时间

可以使用两个百分号来测试多行代码的平均运行时间：
```
%%timeit
statement1
statement2
statement3
```

记住：
- %time一般用于耗时长的代码段
- %timeit一般用于耗时短的代码段


#### 3. 查看当前会话中的所有变量和函数
快速查看当前会话的所有变量与函数名称：
>%who

查看当前会话的所有变量与函数名称的详细信息：
>%whos

返回一个字符串列表，里面元素是当前会话的所有变量与函数名称：
>%who_ls

#### 4.执行Linux指令
在Linux指令之前加上  <font size = 5 color = green>!</font>，即可在ipython当中执行Linux指令。
注意，会将标准输出以字符串形式返回

#### 5. 更多的魔法命令
列出所有魔法命令
>lsmagic

## 四、IPython输入输出历史
#### 1. 使用in、out调用输入输出历史
In返回一个字符串列表，里面是所有输入命令的字符串
Out返回一个含有输出的命令的序号及其输出组成的字典
两者皆可以通过索引获取元素
#### 2. 使用下划线表示输出
"_"表示上一个输出
"_2"表示Out[2]

## 五、jupyter-notebook的快捷键

[jupyter-notebook快捷键](https://yuansuixin.github.io/archives/2018/04/ "快捷键")