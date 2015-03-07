# Chapter 8 {#chapter_8}

## 条件表达式(三元运算符)

在Python 2.5前没有提供条件表达式，可以使用"(x < y and [x] or \[y\])\[0\]"代替，在Python 2.5和更新版本中，可以使用如下语句：

~~~

x if x < y else y

~~~

## for语句

### 内建函数range()

Python 提供了两种不同的方法来调用 range() . 完整语法要求提供两个或三个整数参数:

~~~

range(start, end, step=1) 

~~~

range()还有两种简略的语法格式:  

~~~

range(end) 
range(start, end)  

~~~

start 默认为 0 , step 默认为 1。

### xrange()内建函数

xrange() 类似 range() , 不过当你有一个很大的范围列表时, xrange() 可能更为适合, 因为它不会在内存里创建列表的完整拷贝.它的性能远高出 range(), 因为它不生成整个列表。

在Python 2中，range()返回一个列表，xrange()返回一个迭代器，而在Python 3中，range()和xrange()合并，xragne()不存在了，而ragne()返回的是一个迭代器。

## Python中的else语句

在Python中可以在while和for循环中使用else语句，在循环中使用时, else子句只在循环完成后执行, 当有break语句执行时也会跳过else块

~~~

for i in range(10):
    print i
else:
    print 'end'

while i < 10:
    print i
    i += 1
else:
    print 'end'

~~~

## 迭代器和 iter() 函数

迭代器是在版本 2.2 被加入 Python 的, 它为类序列对象提供了一个类序列的接口。

根本上说, 迭代器就是有一个**next()**方法的对象, 而不是通过索引来计数。当你或是一个循环机制(例如 for 语句)需要下一个项时, 调用迭代器的**next()**方法就可以获得它. 条目全部取出后, 会引发一个**StopIteration**异常, 这并不表示错误发生, 只是告诉外部调用者, 迭代完成
不过, 迭代器也有一些限制. 例如不能向后移动, 不能回到开始, 也不能复制一个迭代器. 如果要再次(或者是同时)迭代同个对象, 只能去创建另一个迭代器对象。

**reversed()**内建函数将返回一个反序访问的迭代器. **enumerate()** 内建函数同样也返回迭代器. 另外两个新的内建函数, **any()** 和 **all()** , 在 Python 2.5 中新增, 如果迭代器中某个(any())/所有条目(all)的值都为布尔真时，函数的返回值为真。

### 使用迭代器

字典和文件是另外两个可迭代的 Python 数据类型. 字典的迭代器会遍历它的键(keys)。另外, Python 还引进了三个新的内建字典方法来生成迭代器: 

* myDict.iterkeys() - 通过 keys 迭代 
* myDict.itervalues() - 通过values迭代
* myDicit.iteritems() - 通过key/value对来迭代

文件对象生成的迭代器会自动调用readline()方法

### 可变对象和迭代器

一个序列的迭代器只是记录当前到达第多少个元素, 所以如果在迭代时改变了元素, 更新会立即反映到所迭代的条目上. 在迭代字典的 key 时, 绝对不能改变这个字典. 使用字典的 keys() 方法是可以的, 因为keys() 返回一个独立于字典的列表. 而迭代器是与实际对象绑定在一起的, 它将不会继续执行下去。

### 创建迭代器

对一个对象调用 iter() 就可以得到它的迭代器. 它的语法如下: 

~~~

iter(obj) 
iter(func, sentinel) 

~~~

第一个函数，传递一个参数给iter(),它会检查你传递的是不是一个序列, 如果是, 则根据索引从0一直迭代到序列结束. 另一个创建迭代器的方法是使用类。 第二个函数，会传递两个参数给iter()，它会重复地调用func ,直到迭代器的下个值等于sentinel。

### 列表解析

列表解析是一个非常有用、简单、而且灵活的工具,可以用来动态地创建列表，它在Python 2.0中被加入。

列表解析的语法：\[expr for iter_var in iterable\]:

~~~

 >>> [i for i in range(10)]
 [0, 1, 2, 3, 4, 5, 6, 7, 8, 9]

~~~

结合if语句，列表解析还提供了一个扩展版本的语法:\[expr for iter_var in iterable if cond_expr\]：

~~~

 >>> [i for i in range(10) if i > 5]
 [6, 7, 8, 9]

~~~

### 生成器表达式

生成器表达式是列表解析的一个扩展。

在 Python 2.0 中我们加入了列表解析，在Python 版本 2.2 时被加入的另一个重要特性是生成器. 生成器是特定的函数, 允许你返回一个值, 然后"暂停"代码的执行, 稍后恢复。

列表解析的一个不足是必须生成所有的数据, 用以创建整个列表. 这可能对有大量数据的迭代器有负面效应. 生成器表达式通过结合列表解析和生成器解决了这个问题。

生成器表达式在 Python 2.4 被引入, 它与列表解析非常相似，而且它们的基本语法基本相同。不过它并不真正创建列表, 而是返回一个生成器，这个生成器在每次计算出一个条目后，把这个条目“产生”(yield)出来. 生成器表达式使用了"延迟计算"(lazy evaluation),  所以它在使用内存上更有效。

生成器表达式:(expr for iter_var in iterable if cond_expr)：

~~~

 >>> (i for i in range(10))
 <generator object <genexpr> at 0x02C02D50>

 >>> for i in (i for i in range(10)):
	 print i,

 0 1 2 3 4 5 6 7 8 9

~~~
