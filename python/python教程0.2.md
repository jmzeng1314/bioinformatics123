# 一. 概要
## 	1. 学习目标
1. 能够完成简单的格式转换工作
2. 了解进一步学习编程的途径


## 	2. 推荐资料
### 		1. 入门
Python的教程实在是太多了，这是三个质量比较高的，选一个看就可以了。

1. [Python教程 - 廖雪峰的官方网站](https://www.liaoxuefeng.com/wiki/0014316089557264a6b348958f449949df42a6d3a2e542c000)
2. [Python 基础教程 | 菜鸟教程](http://www.runoob.com/python3/python3-tutorial.html)
3. [简明 Python 教程](http://www.kuqin.com/abyteofpython_cn/)


如果觉得不过瘾可以在（[免费的编程书籍#Python - Github](https://github.com/justjavac/free-programming-books-zh_CN#python)）找到更多的资料。

下面这篇文章强烈建议要看，学会使用搜索引擎能够帮助你解决大部分的编程问题。

* **[编程初学者如何使用搜索引擎](https://zhuanlan.zhihu.com/p/20683456)**

  ### 	2. 进阶
1. 查缺补漏，最好的材料就是python官方的Tutorial，虽然说是tutorial，但是不建议完全没基础的人去看，第一是英文的问题，第二是讲解的东西略多，不适合以**生信入门为目的**的学习，作为补充还是非常不错的材料。
    - [The Python Tutorial — Python 3.6.3 documentation](https://docs.python.org/3.6/tutorial/index.html)
    - [Python入门（Python3.2/中文PDF）](https://att.liam0205.me/attachment/Python/The_Python_Tutorial_zh-cn.pdf)

2. 编程规范，平常编程坚持使用Pycharm，注意各种波浪线的提示，改正自己的编码习惯。
    - [PEP8(中文)](http://www.open-open.com/lib/view/open1433813937629.html)
    - [Google 开源项目编码规范(中文)](http://zh-google-styleguide.readthedocs.io/en/latest/google-python-styleguide/)

3. Biopython，一个针对生信的Python的第三方库。Biopython比较完善，有自己的tutorial ，中文版对应版本的较为落后。Biopython的体量比较大，建议有一定基础之后再研究。
    - [Tutorial - Biopython.org](http://biopython.org/DIST/docs/tutorial/Tutorial.html)
    - [中文版](https://github.com/bigwiv/Biopython-cn)



# 二. Python 介绍
## 为什么生物信息要学习编程
> 生物信息无非是调用几个包跑一下数据

入门的初级阶段大家都会有类似的想法，但是很快就会遇到天花板，**生信中的文件格式千奇百怪**，找到的教程和手上的数据对不上的时候就不知道该怎么办了，**花很多时间求医问药，不如自己动手丰衣足食。**

> 减少重复机械的劳动

![gvng](http://7xtgqe.com1.z0.glb.clouddn.com/gvng.jpg)
随着任务规模的扩大，自动化的优势就体现出来了。


## 	1. Python 之禅
```text
>>> import this
The Zen of Python, by Tim Peters

Beautiful is better than ugly.
优美胜于丑陋（Python 以编写优美的代码为目标）
Explicit is better than implicit.
明了胜于晦涩（优美的代码应当是明了的，命名规范，风格相似）
Simple is better than complex.
简洁胜于复杂（优美的代码应当是简洁的，不要有复杂的内部实现）
Complex is better than complicated.
复杂胜于凌乱（如果复杂不可避免，那代码间也不能有难懂的关系，要保持接口简洁）
Flat is better than nested.
扁平胜于嵌套（优美的代码应当是扁平的，不能有太多的嵌套）
Sparse is better than dense.
间隔胜于紧凑（优美的代码有适当的间隔，不要奢望一行代码解决问题）
Readability counts.
可读性很重要（优美的代码是可读的）
Special cases aren't special enough to break the rules.
Although practicality beats purity.
即便假借特例的实用性之名，也不可违背这些规则（这些规则至高无上）
Errors should never pass silently.
Unless explicitly silenced.
不要包容所有错误，除非你确定需要这样做（精准地捕获异常，不写 except:pass 风格的代码）
In the face of ambiguity, refuse the temptation to guess.
当存在多种可能，不要尝试去猜测
There should be one-- and preferably only one --obvious way to do it.
而是尽量找一种，最好是唯一一种明显的解决方案（如果不确定，就用穷举法）
Although that way may not be obvious at first unless you're Dutch.
虽然这并不容易，因为你不是 Python 之父（这里的 Dutch 是指 Guido ）
Now is better than never.
Although never is often better than *right* now.
做也许好过不做，但不假思索就动手还不如不做（动手之前要细思量）
If the implementation is hard to explain, it's a bad idea.
If the implementation is easy to explain, it may be a good idea.
如果你无法向人描述你的方案，那肯定不是一个好方案；反之亦然（方案测评标准）
Namespaces are one honking great idea -- let's do more of those!
命名空间是一种绝妙的理念，我们应当多加利用（倡导与号召）
```

## 	2. Why Python
Python简单易学，标准库强大，开发效率高。

扩展阅读：
[在生物信息学领域，Python 和 Perl 谁更强大、易用、代表着未来的发展方向？](https://www.zhihu.com/question/20091346)
## 	3. 安装开发环境
解释器：Python3.6
IDE/Editor：Pycharm Community or VScode

## 	4. 第一个 Python 程序

```python
#!/usr/bin/env python
# coding=utf-8
# author: hyacz<hyacz@foxmail.com>
# 2017-10-19 22:00
#
# 第一个Python文件

print('Hello World')
```

# 三. Python 基础
## 	1. 基础语法
### 		1. 交互式 & 脚本
python 有两种使用方式，第一种是交互式命令行，第二种是编写Python脚本运行。
交互式命令行适合不需要保存的小任务，比如试验一下某个函数怎么使用。
脚本适合完成需要保存的正常任务。
![](http://7xtgqe.com1.z0.glb.clouddn.com/15084313093544.jpg)

### 		2. 标识符
所谓标识符就是你起名字的东西（变量、函数、文件名……）

* 第一个字符必须是字母表中字母或下划线'_'。
* 标识符的其他的部分有字母、数字和下划线组成。
* 标识符对大小写敏感。

规范：
变量和函数使用下划线命名法，即全部小写，每个单词用下划线隔开。

```python
my_var = 1.0

def my_function():
    pass
```

### 		3. 保留字

保留字就是Python自己保留下来不能用作标识符的词，不需要刻意记忆，在Pycharm中编程时，错误使用保留字会变色有提示。

```python
>>> import keyword
>>> keyword.kwlist
['False', 'None', 'True', 'and', 'as', 'assert', 'break', 'class', 'continue', 'def', 'del', 'elif', 'else', 'except', 'finally', 'for', 'from', 'global', 'if', 'import', 'in', 'is', 'lambda', 'nonlocal', 'not', 'or', 'pass', 'raise', 'return', 'try', 'while', 'with', 'yield']
```

###      4. 缩进
python 使用缩进来区分代码级别，常规会使用4个空格作为缩进，在python中输入Tab会自动转换成4个空格。单独使用Tab是可以的，但是不能Tab和空格混用，这样会出现：

```text
IndentationError: unindent does not match any outer indentation level
```

> 以冒号结尾的行之后需要增加一级缩进

### 		5. 注释
python使用`#`作为注释，单行注释会使用`# `作为开头。
多行注释一般用于函数的注释：

```python
def my_function():
    """
    我的第一个函数注释
    第二行内容
        1. 要点一
    """
    pass
```

## 	2. 数据类型
### 		1. int / float
常用的两种数字类型，int指的是整数，float指的是浮点数。

> float 可以用科学计数法表示，比如 1e-4

### 		2. str

> 字符串是Python中非常强大的一个数据类型，也是我们学习的重点。


```python
# -----------------------
# Section 1. 引号
# -----------------------

# 单引号字符串
print('Hello World')

# 双引号字符串中的单引号是普通字符
print("doesn't")

# 单引号字符串中的双引号是普通字符
print('"Yes," he said.')


# -----------------------
# Section 2. 转义与特殊字符
# -----------------------

# 对双引号字符串中的双引号进行转义
print("\"Yes,\" he said.")

# 对单引号字符串中的单引号进行转义
print('doesn\'t')
print('"Isn\'t," she said.')

# 错误的转义
print('C:\some\name')

# 使用裸字符串，忽略所有转义
print(r'C:\some\name')

# 注：常见的特殊字符转义如下：
# \t 制表符
# \n 换行符


# -----------------------
# Section 3. 字符串运算
# -----------------------

# 乘法与加法
print(3 * 'un' + 'ium')     # 输出：unununium

# 与变量拼接
prefix = 'Py'
print(prefix + 'thon')      # 输出：Python


# -----------------------
# Section 4. 索引与切片
# -----------------------

"""
 +---+---+---+---+---+---+
 | P | y | t | h | o | n |
 +---+---+---+---+---+---+
 0   1   2   3   4   5   6
-6  -5  -4  -3  -2  -1
"""

s = 'Python'

# 索引
print(s[1])     # 'y'
print(s[-3])    # 'h'

# 切片
print(s[1:3])   # 'yt'
print(s[1:])    # 'ython'
print(s[:3])    # 'Pyt'
```

### 		3. bool

布尔值，分为 True 和 False

### 		4. list

```python
squares = [1, 4, 9, 16, 25]

# -----------------------
# Section 1. 索引
# -----------------------
print(squares[0])       # 1
print(squares[-1])      # 25
print(squares[-3:])     # [9, 16, 25]


# -----------------------
# Section 2. 追加元素
# -----------------------

squares = squares + [36, 49, 64, 81, 100]     # [1, 4, 9, 16, 25, 36, 49, 64, 81, 100]
squares.append(36)


# -----------------------
# Section 2. 修改
# -----------------------

# 1，2，3，4，5的三次方，这里有一个错误，4的三次方是64，而不是65
cubes = [1, 8, 27, 65, 125]

# 修改错误的值
cubes[3] = 64

letters = ['a', 'b', 'c', 'd', 'e', 'f', 'g']

# 修改多个值
letters[2:5] = ['C', 'D', 'E']      # ['a', 'b', 'C', 'D', 'E', 'f', 'g']

# 清空list
letters[:] = []
```

### 		5. dict

```python
tel = {'jack': 4098, 'sape': 4139}

# 取出一个键值对
tel['sape']


# 添加或者更新一个键值对
tel['guido'] = 4127     # {'sape': 4139, 'guido': 4127, 'jack': 4098}

# 判断一个键是否在字典里
if 'guido' in tel:
    pass

# 删除一个键值对
del tel['sape']

# 取出所有的键
list(tel.keys())

# 遍历字典
knights = {'gallahad': 'the pure', 'robin': 'the brave'}
for k, v in knights.items():
    print(k, v)

knights = {'gallahad': 'the pure', 'robin': 'the brave'}
for k in knights:
    print(k, knights[k])
```

## 	3. 控制流
### 		1. if / else
![](http://7xtgqe.com1.z0.glb.clouddn.com/15084271081152.jpg)

```python
# if / elif /else
# 两种可能性的选择结构
condition = True
if condition:
    print('Hello Bioinformatics')
else:
    print('Hello World')

# 超过两种可能性的选择结构
minor_allele = 'A'
if minor_allele == 'A':
    print("minor allele is 'A'")
elif minor_allele == 'T':
    print("minor allele is 'T'")
elif minor_allele == 'G':
    print("minor allele is 'G'")
else:
    print("minor allele is 'C'")
```
### 		2. while
![](http://7xtgqe.com1.z0.glb.clouddn.com/15084271235439.jpg)

```python
# while
# 当…… / 直到……
count = 0
while count < 3:
    print('Hello World!')
    count = count + 1   # 等价于 count += 1
```

### 		3. for
![](http://7xtgqe.com1.z0.glb.clouddn.com/15084271594331.jpg)

```python
# for
# 依次处理
my_list = [1, 1.0, False, 'Hello World']
for item in my_list:
    print(item)
```

### 		4. break
![](http://7xtgqe.com1.z0.glb.clouddn.com/15084272250160.jpg)

```python
# break
# 跳出
my_list = [1, 1.0, False, 'Hello World']
for item in my_list:
    print(item)
    if item is 1.0:
        break
```

### 		5. continue
![](http://7xtgqe.com1.z0.glb.clouddn.com/15084273371459.jpg)

```python
# continue
# 继续，跳过后续循环体
my_list = [1, 1.0, False, 'Hello World']
for item in my_list:
    if item is 1.0:
        continue
    print(item)
```

### 		6. pass

```python
# pass
# 占位符，多用于占位，后续再补代码
if condition:
    pass    # TODO: 补全代码
```
![](http://7xtgqe.com1.z0.glb.clouddn.com/15084332182482.jpg)
在 Pycharm 的 TODO 窗格中查看自己留下了哪些 TODO
## 	4. I/O 操作
### 		1. 什么是 I/O
I/O 就是指 Input and Output，包括和键盘的输入，屏幕的输出，网络的输入输出，文件的读写。
### 		2. 读写文件

```python
# 简单读取文件
with open('../data/DNA.txt') as f:
    lines = f.readlines()
    print(lines)


# 读取大文件逐行处理
with open('../data/DNA_1.txt') as f:
    count = 0
    for line in f:
        count += 1
    print(count)

# 写文件
with open('new_file.txt', 'w') as f:
    f.write('Hello World')
    f.writelines(['Hello World', 'Hello Again'])
```

## 	5. 模块化编程
单文件

```python
def process_1(p):
    """
    过程1
    :param p:一个参数
    :return: 一个list
    """
    return [p, p]


def process_2(p):
    """
    过程2
    :param p:另一个参数
    :return: 
    """
    return p + 1


if __name__ == '__main__':  # 这是文件主入口，在 Pycharm 中可以直接输入 main 按下 tab 键得到
    # 处理输入
    p = 1

    # 过程1
    result_1 = process_1(p)

    # 过程2
    for i in result_1:
        result_2 = process_2(i)
        print(result_2)
```

多文件

```python
# 文件1，process.py
def process_1(p):
    """
    过程1
    :param p:一个参数
    :return: 一个list
    """
    return [p, p]


def process_2(p):
    """
    过程2
    :param p:另一个参数
    :return: 
    """
    return p + 1
```

```python
# 文件2，main.py
import process

if __name__ == '__main__':  # 这是文件主入口，在 Pycharm 中可以直接输入 main 按下 tab 键得到
    # 处理输入
    p = 1

    # 过程1
    result_1 = process.process_1(p)

    # 过程2
    for i in result_1:
        result_2 = process.process_2(i)
        print(result_2)
```

## 	6. Debug 技巧
编程就是与Bug战斗的过程，编写的程序难免会出现问题，接下来就介绍一下调试的方法。
### 		1. 暴力调试
暴力调试，顾名思义，就是简单粗暴的调试方法，是一切编程语言通用的方式，也就是print！
当你不清楚程序为什么运行的和你想的不一样的时候，就在每一个值发生变化的地方加一个`print`这样一个个的去查看变量的值。
### 		2. 断点调试
![](http://7xtgqe.com1.z0.glb.clouddn.com/15085549040580.jpg)
![](http://7xtgqe.com1.z0.glb.clouddn.com/15085550664453.jpg)
![](http://7xtgqe.com1.z0.glb.clouddn.com/15085552169064.jpg)

## 7. 安装管理模块
### 		1. pip
pip是python自带的包管理工具，安装命令为

```
pip install <somepackage>
```

### 		2. 镜像源

```
pip install -i https://pypi.tuna.tsinghua.edu.cn/simple <somepackage>
```

## 四. Python 进阶展望
### 	1. 虚拟环境部署与软件包管理
1. virtualenv

2. requirements.txt

  ​

### 2.异常处理

```python
>>> 10 * (1/0)
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
ZeroDivisionError: division by zero
```
在这里输出分为2部分，一部分是`Traceback`这部分显示的是调用堆栈，告诉你问题出在何处。另一部分是`Exception`这部分告诉你问题是什么。
遇到报错时，只需要把类似`ZeroDivisionError: division by zero`这样的语句复制到搜索引擎里面去搜索就可以了。

#### 捕获异常

```python
while True:
    try:
        x = int(input("Please enter a number: "))
        break
    except ValueError:
        print("Oops!  That was no valid number.  Try again...")
```
`try...except`句式用来捕获异常并进行处理。

### 	3. PEP
这里我先向大家灌输一个概念，对于计算机领域来说，很多东西是先有纸面上的`规范`，再有实际上可以使用的对应`实现`，只要你遵循 Python 的的规范，你也可以写一个自己的 Python 解释器。

PEP （即 Python Enhancement Proposals）就是一系列增强标准，比如 PEP8 提出了建议使用的 Python 代码风格，PEP257提出了文档字符串（docstring）的书写规范。还有一些有关于添加新的语言特性的提案。

### 	4. Biopython
Biopython的特点包括解析各种生物信息学格式的文件(BLAST， Clustalw， FASTA， Genbank...)，访问在线的服务器(NCBI，Expasy...)，常见和不那么常见程序的接口(Clustalw， DSSP，MSMS...)，标准的序列类，各 种收集的模块，KD树数据结构等等，还有一些文档。


