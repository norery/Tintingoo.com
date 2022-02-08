---
title: python Tips
author: Tintingo
date: '2022-02-08'
slug: python-tips
show_toc: true
toc_depth: 3
categories:
  - 编程
  - Python
tags:
  - python
---

## 1. 函数式编程

函数式编程（Functional Programming），又称泛函编程，是一种编程范型。简单来讲，函数式编程是一种“广播式”的编程，用于科学计算中，会显得特别简洁方便。
在 Python 中，函数式编程主要通过以下几个函数来实现：
+ `lambda()`
+ `map()`
+ `reduce()`
+ `filter()`

在泛函编程之前，让我们先来看一看好用的“列表解析”功能(也叫做推导式)。它能够简化我们对列表内元素逐一进行操作的代码：

```python
a = [1,2,3]
b = []
for i in a:
    b.append(i + 2)
b

Out: [3, 4, 5]
```

使用“列表解析”，上面的代码可以简化成：

```python
a = [1,2,3]
b = [i + 2 for i in a];    b

Out:[3, 4, 5]
```

使用这样的方式可以让我们的代码很直观，看起来也更优雅可读了。接下来进行函数式编程的讲解。

### 1.1 匿名函数（Lambda）
匿名函数是一种通过单个语句生成函数的方式，其结果是返回值。匿名函数使用 lambda 关键字定义，该关键字仅表达“我们声明一个匿名函数”的意思。

使用 lambda 表达式的时候，在心里默念：输入 x（: 左边的变量），输出 x + 2（: 右边的表达式）：


```python
y = lambda x: x+2
```

#### a. 案例 1:
匿名函数在数据分析中非常方便，因为在很多案例中 **数据变形函数** 都可以作为函数的参数。匿名函数代码量小（也更为清晰），将它作为参数进行传值， 比写一个完整的函数或者 **将匿名函数赋值给局部变量** 更好：


```python
def apply_to_list(some_list, f):
    return [f(x) for x in some_list]
ints = [4, 0, 1, 5, 6]
apply_to_list(ints, lambda x: x * 2)

Out: [8, 0, 2, 10, 12]
```

值得一说的是，以上函数也可以用“列表解析”简单实现，但是使用上面的方法，我们能够简单地将一个自定义操作符传递给 apply_to_list 函数。


```python
y = [x * 2 for x in ints];    y

Out: [8, 0, 2, 10, 12]
```

通过这个例子，知道了我们可以把 lambda 表达式定义为 f，然后用 f(x) 将 x 作为参数传入到匿名函数中。

#### b. 案例 2:
假如想要根据字符串中不同字母的数量对一个字符串集合进行排序，可以将一个匿名函数传给列表的 sort 方法：


```python
strings = ['foo','card','bareo','aaaaa','abab']
strings.sort(key=lambda x:len(set(list(x))))
strings

Out: ['aaaaa', 'foo', 'abab', 'card', 'bareo']
```

输入一个 `x`，将输出的 `len(set(list(x)))` 作为参数输入到 `strings` 的 `sort` 方法中。

Q：输入一个 `x`，但是这个 `x` 是什么，在哪里输入了？看样子 `x` 是 `strings` 的每一个元素。

A：原因出在 `sort` 的 `key` 上面：

> key 指定一个具有一个参数的函数，用于从每个列表元素中提取比较键(例如，key=str.lower)。与列表中每个项对应的键计算一次，然后用于整个排序过程。

也就是说 `key` 会把 `strings` 的每一个元素拿到 `key` 的函数里进行运算，然后将计算的结果用于排序。

到这里我们明确了，lambda 表达式就是一个没有显式的 `__name__` 属性的函数，如果没有给它传参，那么这个函数是不会有什么效果的。

接下来，我们将 lambda 和 map 函数结合，写出高效的代码。
### 1.2 map() 序列遍历函数
一句话概括：对于 `map(f,sequence)`, map 函数可以将一个函数 (f) 应用到一个序列 (sequence) 上，使这个函数对这个序列的元素分别生效。

如果将 lambda 匿名函数作为 map 里的这个函数 f，那么就可以将这两个强大的工具相结合了。
#### a. 案例 1
需求：将 list 的每一个元素都加上 2，得到新的列表：


```python
a = [1, 2, 3]
#### 方法一：将 map 函数与 lambda 表达式相结合
b = map(lambda x: x + 2, a);
# 在 python3 里，map 函数仅仅是创建一个带运行的命令容器，只有在其他函数调用它的时候才返回结果，所以需要再次转换成序列才行
b = tuple(b) ; b

Out: (3, 4, 5)
```

#### b. 案例 2:
`map()` 也接受多参数的函数，如：将 `a`，`b` 两个列表对应的元素相乘，把结果返回给列表 `c`：


```python
c = list(map(lambda x,y: x*y,a,b)); c

Out: [3, 8, 15]
```

值得一提的是，上面 map 和 lambda 组合的功能，用“列表解析”也是可以完成的：


```python
b = [i + 2 for i in a];  
c = [i * j for i,j in zip(a,b)];  
print('b:',b)
print('c:',c)

Out: b:  [3, 4, 5]
     c:  [3, 8, 15]
```

Q：有了列表解析，为什么还要有 map() 命令呢？

A：其实列表解析虽然代码简短，但是本质上还是 for 命令，而 python 的 for 命令效率不高。但是 map() 函数实现了相同的功能，并且效率更高。原则上来说，它的循环命令相当于 C 语言。

### 1.3 reduce() 序列递归函数
它有点像 map()函数，但是 map() 函数是逐一遍历，而 reduce() 函数用于递归计算。
#### a. 案例 1
计算 n 的阶乘：


```python
from functools import reduce
n = 5
reduce(lambda x,y: x*y, range(1, n+1))

Out:  120
```

让我们来分析一下发生了什么：

+ `range(1, n+1)` 相当于给出了一个列表，元素是 1～n 这 n 个整数。
+ `lambda x,y: x*y` 构造了一个二元函数，返回两个参数的乘积。
+ `reduce()` 命令先将列表的前两个元素作为函数的参数进行运算，然后将运算结果与第三个数字作为函数的参数 ... 再将运算结果和第四个数字作为函数的参数... 直到列表最后，返回最终结果。

如果用循环命令，那就要这样写：


```python
s = 1
n = 5
for i in range(1, n+1):
    s = s * i
s

Out: 120
```

### 1.4 filter() 列表过滤器函数
顾名思义，它是一个过滤器，用来筛选出列表中符合条件的元素。 --- 这个在做列表筛选的时候是很有用的!

先来看一个案例演示：
#### a. 案例 1：过滤出列表中大于 5 小于 8 的数字


```python
b = filter(lambda x: x > 5 and x < 8, range(10))
b = list(b)  # python3.x 需要转换一下，理由同 map()
b

Out: [6, 7]
```

我们来看看发生了什么：
+ 使用 `filter()` 函数首先需要一个返回值为 bool 型的函数，如 `lambda x: x > 5 and x < 8` 定义了一个函数，判断 x 是否大于 5 且小于 8。
+ 然后将这个函数作用到 `range(10)` 的每一个元素中，如果为 True，则“挑出那个元素”，最后将满足条件的所有元素组成一个列表返回。

如果使用列表解析，则写成：


```python
b = [i for i in range(10) if i > 5 and i < 8]
b 

Out: [6, 7]
```

可以看到列表解析也并不比 `filter()` 函数复杂，但是后者的运算速度要比 python 内置的循环快得多。

## 2. python 小技巧

### 2.1 F-Strings
在 python3.6 以上，F-Strings 提供了一种简便的方法，可以将 Python 表达式嵌入字符串文本中进行格式化。

只需在大括号内有变量或表达式的字符串前面加一个小写或大写的 'f' 即可。


```python
name = 'vde'
age = '25'
print(f'my name is {name}, and I am {age} years old')

Out: my name is vde, and I am 25 years old
```

F-Strings 是一种很棒的格式化字符串新方法，此法得出的结果可读性更强，更简洁，不易出错。

### 2.2 enumerate 追踪序列索引
在遍历一个序列时，我们往往需要在知道它的每个元素值的同时，知道当前元素的索引。此时我们就可以用 `enumerate` 追踪序列的索引。
#### 用法示意
```python
for i, value in enumerate(collection):
    # 使用值做点事
```
接下来我们使用 `enumerate` 构建一个字典，将元素值作为字典的键，索引值作为字典的值（当然也可以反过来）。


```python
li = ['one', 'two', 'three']
mapping = {}
for i, v in enumerate(li):
    mapping[v] = i
mapping

Out: {'one': 0, 'two': 1, 'three': 2}
```

### 2.3 柯里化：部分参数应用

柯里化指的是通过部分参数应用的方式**从已有的函数中衍生出新的函数**。

例如我们有一个不重要的函数，其功能是将两个数加在一起：

```python
def add_numbers(x, y):
	return x + y
```

使用这个函数，我们可以衍生出一个只有一个变量的新函数，`add_five`, 可以给 x 参数加上 5。

`add_five = lambda y: add_numbers(x, 5)`

第一个参数对于函数 `add_numbers` 就是柯里化了（部分参数应用了）。

+ 内建的 functools 模块可以使用 `pratial` 函数简化这种处理：

```python
from functools import partial
add_five = partial(add_numbers, b = 5)  ## 也可以通过 a=5 来指定固定值到底是哪个
```

### 2.4 zip()
`zip()` 将列表、元组或其他序列的元素配对，新建一个**元组构成的列表**：

```python
In [9]: seq1 = [1, 2, 3]
   ...: seq2 = ['one', 'two', 'three']
   ...: zipped = zip(seq1, seq2)

In [10]: zipped  ## 先生成的是一个 zip 对象，要 list 以后才能转换成列表显示出来。
Out[10]: <zip at 0x2070ad95b88>

In [11]: list(zipped)
Out[11]: [(1, 'one'), (2, 'two'), (3, 'three')]
```

zip() 可以处理任意长度的序列，不等长的话，生成列表的长度由最短的序列决定，多出来的部分就直接舍弃了。

```python
In [13]: list(zip(seq1,seq2,seq3))
Out[13]: [(1, 'one', False), (2, 'two', True)]  #3 和 'three' 直接被舍弃了
```

#### 应用场景：

1. 同时遍历多个序列，有时会和`enumerate()`同时使用:

    ```python
    In [14]: for i , (a, b) in enumerate(zip(seq1, seq2)):
        ...:     print('{0}: {1}, {2}'.format(i, a, b))
        ...:
    0: 1, one
    1: 2, two
    2: 3, three
    ```
2. 从序列生成字典：

    如果有两个序列想在字典中按元素配对，以下两种方式均可以实现，但明显第二种更简单：

    a) 使用 zip 遍历两个序列进行配对
    
    ```python
    In [20]: mapping = {}
    
    In [21]: for key, value in zip(tup1, tup2):
        ...:     mapping[key] = value
        ...:
    
    In [22]: mapping
    Out[22]: {'one': 1, 'two': 2, 'three': 3}
    ```

    b) 直接生成
    
    由于字典本质上是 2- 元组（含有 2 个元素的元组）的集合，字典是可以接受一个 2- 元组的列表作为参数的：
    
    ```python
    In [23]: mapping = dict(zip(range(5), reversed(range(5))));  mapping
    Out[24]: {0: 4, 1: 3, 2: 2, 3: 1, 4: 0}
    ```

## 3. 参数解包
首先要知道，解包的方式是：
+ 在列表或元组变量前，加一个 `*`
+ 在字典变量前，加两个 `*`

当需要对列表或元组中的参数进行解包以进行需要单独位置参数的函数调用时，Splat 或 Scatter 运算符 * 有时能发挥作用。


```python
## 理解何为解包：解包运算符接受了列表中的所有元素，并将它们作为参数传递。
a = [1,2]  # 列表或元组
b = {"name":'vde', 'age':25}  # 字典
print(*a)
# 但是不可直接 print(**b)，---- TypeError: 'name' is an invalid keyword argument for print()# print(**b)

Out: 1 2
```

以上代码打印出列表 a 中的每个元素，这些元素以空格分隔。等效于代码：`print(1,2,3,4,5,6)`

### 3.1 参数解包应用于函数的参数传递
参数解包在函数中经常使用，以将方法调用接收到的所有参数‘打包’到单个变量中。一个简单的例子：

```python
def f1(*argsss):
    print(argsss)
f1(1,2)
f1('one','two','three','four')

Out: (1, 2)
     ('one', 'two', 'three', 'four')
```

    


`*args` 可以将 args 中的所有参数解包并传递到函数中，所以可以接收无限量的参数。再来看一个综合的例子：


```python
def f2(*args, **kwargs):
    print(f'args: {args}')print(f'kwargs: {kwargs}')
```


```python
# 会把 a 和 b 一起作为一个元组传递给 *args，而 **kwargs 就接收不到参数了，所以输出为空。
f2(a,b)

Out: args: ([1, 2], {'name': 'vde', 'age': 25})
     kwargs: {}
```

```python
# 将 a 传递给 *args, 将 b 传递给 **kwargs，分别进行解包：
f2(*a,**b)

Out: args: (1, 2)
     kwargs: {'name': 'vde', 'age': 25}
```




```python
## 还原解包的过程，其实就相当于把参数分解以后，传入了函数中。
def f3(one, two, name, age):
    print(f'one: {one}')print(f'two: {two}')print(f'name: {name}')print(f'age: {age}')f3(*a,**b)

Out: one: 1
     two: 2
     name: vde
     age: 25
```

### 3.2 与 zip 配合对已”配对“的序列进行拆分
接下来详细讲解这个过程：

```python
aa = [('one',1),('two',2),('tree',3),('four',4)]  ;aa

Out: [('one', 1), ('two', 2), ('tree', 3), ('four', 4)]
```
    
1. 先用 `*` 对 aa 进行解包，解包为四个序列：

    ```python
    print(*aa)
    
    Out: ('one', 1) ('two', 2) ('tree', 3) ('four', 4)
    ```

2. 然后传递给 zip 函数，分别取四个序列的第一个元素拼在一起，再分别取四个序列的第二个元素拼在一起：

    ```python
    ab = list(zip(*aa))   ;ab
    
    Out: [('one', 'two', 'tree', 'four'), (1, 2, 3, 4)]
    ```

3. 用将列表 ab 的第一个元素和第二个元素的值分别传给 a 和 b：

    ```python
    a, b = ab
    print(f'a: {a}')print(f'b: {b}')Out: a: ('one', 'two', 'tree', 'four')
         b: (1, 2, 3, 4)
    ```

> 这个方法的思路是使用 zip(*aa) 将 aa 的行和列进行转置。

> + aa 本来 size 为 (4*2)，第 1 列为 one, two, three, four，第二列为 1, 2, 3, 4。
> + 而经过转置以后，size 变成 (2*4) 的了。第一行为 one, two, three, four，第二行为 1, 2, 3, 4。


## 4. 列表、集合和字典的推导式

### 4.1 列表推导式

+ 列表推导式是最受欢迎的 python 语言特性之一。

+ 它允许你过滤一个容器 (collection) 的元素 (val)，用一种简明的表达式转换(expr) 传递给过滤器 (if condition) 的元素，从而生成一个新的列表。转换前的每个元素为 val，转换后为 expr。

列表推导式的基本形式为：

`list_comp = [expr for val in collection if condition]`

这与下面的 for 循环是等价的：

```python
result = []
for val in collection:
    if condition:
        result.append(expr)
```

**举例 1：** 例如给定一个字符串列表，我们可以过滤出长度大于 2 的，并且将字母改为大写：

```python
In [25]: strings = ['a', 'as', 'bat', 'car', 'dove', 'python']

In [26]: [x.upper() for x in strings if len(x) > 2]
Out[26]: ['BAT', 'CAR', 'DOVE', 'PYTHON']
```

### 4.2 集合和字典的推导式

集合与字典的推导式是列表推导式的自然扩展，用相似的方式生成集合与字典。

+ 字典推导式如下所示：

  `dict_comp = {key-expr : value-expr for value in collection if condition}`

**举例 2：** 创建一个将字符串与其位置相匹配的字典作为字典推导式：

```python
In [29]: loc_mapping = {val:index for index, val in enumerate(strings)}

In [30]: loc_mapping
Out[30]: {'a': 0, 'as': 1, 'bat': 2, 'car': 3, 'dove': 4, 'python': 5}
```

+ 字典推导式如下所示：

  `set_comp = {expr for value in collection if condition}`

集合推导式看起来很像列表推导式，只是中括号变成了大括号。

**举例 3：** 如果有一个字符串的列表，假设我们想要一个集合，集合里包含列表中字符串的长度：

```python
In [27]: unique_len = {len(x) for x in strings}

In [28]: unique_len
Out[28]: {1, 2, 3, 4, 6}
```

我们也可以使用 `map` 函数进行更函数化、更简洁地表达：

```python
In [31]: set(map(len, strings))
Out[31]: {1, 2, 3, 4, 6}
```

## 5. 生成器

### 5.1 迭代器对象

#### 5.1.1 迭代的概念

上一次输出的结果为下一次输入的初始值，重复的过程称为迭代, 每次重复即**一次迭代**，并且每次迭代的结果是下一次迭代的初始值，注意循环不是迭代。

+ 为什么要有迭代器？

  对于没有索引的数据类型，必须提供一种不依赖索引的迭代方式。

#### 5.1.2 迭代器实现原理

通过一致的方式遍历序列的这个特性是通过 ** 迭代器协议 ** 来实现的。迭代器协议是一种令对象可遍历的通用方式。

迭代器就是一种用于上下文中（比如 for 循环）**向 python 解释器生成对象**的**对象**。

```python
In [74]: for i in dict1:
    ...:     print(i)
    ...:
a
b
c
```

在写下 `for i in dict1` 时，Python 的解释器就会首先尝试根据 dict1 生成一个**迭代器对象**。

其实在后台 for 语句对容器对象调用了 ` __iter()__` 函数, 该函数用于生成迭代器对象。`__iter__()` 会返回一个定义了 ` __next__()` 方法的迭代器对象，它在容器中逐个访问容器内元素，并在你调用 `next()` 方法的时候返回容器中的下一个值——迭代器通过 next()不断产出下一个元素直到迭代器耗尽。

任何实现了 `__iter__` 和 `__next__()` 方法的对象都是迭代器，`__iter__` 返回迭代器自身，`__next__` 返回容器中的下一个值，如果容器中没有更多元素了，则抛出 `StopIteration` 异常。

#### 5.1.3 判断对象是否可迭代：

原生函数 iter(instance) 可以判断某个对象是否可迭代，它的工作流程大概分为以下 3 个步骤：

- 检查对象 instance 是否实现了 `__iter__` 方法，并调用它获取返回的迭代器(iterator)。
- 如果对象没有实现 `__iter__` 方法，但是实现了 `__getitem__` 方法，Python 会生成一个迭代器。
- 如果上述都失败，则编译器则抛出 `TypeErro`r 错误，`‘xxx' Object is not iterable`。

```python
In [75]: dict_iterator = iter(dict1)

In [76]: dict_iterator
Out[76]: <dict_keyiterator at 0x2070b1dd4f8>
```

#### 5.1.4 接收迭代器对象的函数和方法

大多数以列表或列表型对象为参数的方法都可以接收任意的迭代器对象。

1. 内建方法：`min, max, sum`
2. 类型构造函数（工厂函数）：`list(), str(), tuple()`

### 5.2 生成器

生成器（Generator）是创建迭代器的简单而强大的工具。它们写起来就像是正规的函数，会自动创建 `iter()` 和 `next()` 方法，只是在需要返回数据的时候使用 `yield ` 语句。每次 `next()` 被调用时，生成器会返回它脱离的位置（它记忆语句最后一次执行的位置和所有的数据值）。

Python 中的生成器主要分为两种类型：

1. 生成器函数 (generator function) 返回得到的生成器(包含 yield 关键字的函数)。

2. 生成器表达式 (generator expression) 返回得到的生成器。

#### 5.2.1 生成器函数

如需创建一个生成器，只需要在函数中返回关键字 return 替换为 yield 关键字：

```python
def squares(n = 10):
    for i in range(1, n+1):
        yield i*2
```

当你实际调用生成器时，代码并不会立即执行，它只是返回了得到的生成器：

```python
In [77]: gen = squares()In [78]: gen
Out[78]: <generator object squares at Ox7fbbd5ab4570>
```

直到你请求生成器中的元素时，它才会执行它的代码：

```python
In [79]: for x in gen:
  .....: print(x, end = ' ')
Out[79]: 1 4 9 16 25 36 49 64 81 100
```

#### 5.2.2 生成器表达式

使用生成器表达式来创建生成器更为简单，只需要将列表推导式中的中括号替换成小括号即可。

```python
In [80]: gen = (x ** 2 for x in range(100)); gen
Out[81]: <generator object <genexpr> at 0x000002070B168E58>
```

上面的代码和下面的更为复杂的生成器是等价的：

```python
In [82]: def make_gen():
    ...:     for x in range(100):
    ...:         yield x ** 2
In [83]: gen = make_gen(); gen
Out[84]: <generator object make_gen at 0x000002070AF4E570>
```

在很多情况下，生成器表达式可以作为函数参数用于替代列表推导式：

```python
In [85]: sum([x ** 2 for x in range(100)])  ### 列表推导式作为 sum()的参数
Out[85]: 328350  

In [86]: sum(x ** 2 for x in range(100))	### 生成器表达式作为 sum()的参数
Out[86]: 328350

In [87]: dict([(i , i**2) for i in range(5)])	### 列表推导式作为 dict()的参数
Out[87]: {0: 0, 1: 1, 2: 4, 3: 9, 4: 16}

In [88]: dict((i , i**2) for i in range(5))		### 生成器表达式作为 dict()的参数
Out[88]: {0: 0, 1: 1, 2: 4, 3: 9, 4: 16}
```

###  5.3 `itertools` 模块

标准库中的 `itertools` 模块是适用于大多数数据算法的生成器集合。

#### 5.3.1 groupby 函数

`groupby` 可以根据任意的序列和一个函数，通过函数的返回值对序列中连续的元素进行分组。

根据每一个独一的 key 生成 `（key, sub-iterator）` 元组

```python
In [89]: import itertools

In [90]: first_letter_func = lambda x : x[0]

In [91]: names = ['Alan', 'Adam', 'Wes', 'Will', 'Albert', 'Steven']

In [93]: for letter, names in itertools.groupby(names, first_letter_func):
    ...:     print(letter, list(names))     ## names is a generator
    ...:
A ['Alan', 'Adam']
W ['Wes', 'Will']
A ['Albert']
S ['Steven']
```
