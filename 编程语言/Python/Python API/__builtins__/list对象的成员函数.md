# 列表类型的内建函数：

~~~

 >>> help(list)

~~~

**list.append(obj)**	
:   向列表中添加一个对象obj

**list.count(obj)**
:   返回一个对象obj在列表中出现的次数

**list.extend(seq)**
:   把序列seq的内容添加到列表中。从 2.2 开始,extend()方法的参数支持任何可迭代对象,在 2.2 之前,它的参数必须是序列对象,而在 1.6 之前它的参数必须是列表对象.\[a\]

**list.index(obj,i=0,j=len(list))**
:   返回list[k]==obj的k值,并且k的范围在i<=k<j;否则引发ValueError异常，如果不存在obj，则出错。

**list.insert(index,obj)**
:   在索引量为index的位置插入对象obj.

**list.pop(index=-1)**
:   删除并返回指定位置的对象,默认是最后一个对象\[a\]

**list.remove(obj)**	
:   从列表中删除对象obj

**list.reverse()**
:   原地翻转列表

**list.sort(func=None,key=None,reverse=False)**
:   以指定的方式排序列表中的成员,如果func和key参数指定,则按照指定的方式比较各个元素,如果reverse标志被置为True,则列表以反序排列.它默认的排序算法是归并排序(或者说"timsort")的衍生算法,时间复杂度是 O(log(n!))\[b\]

a.Python1.5.2加入的特性.    
b.key和reverse特性在Python2.4中新添.

注意：
那些可以改变对象值的可变对象的方法是没有返回值的!
在使用可变对象的方法如 sort(),extend()和 reverse()的时候要注意,这些操作会在列表中原地执行操作,也就是说现有的列表内容会被改变,但是没有返回值!与之相反,字符串方法确实有返回值。因为字符串是不可变的，不可变对象的方法是不能改变它们的值的,所以它们必须返回一个新的对象.
如果确实需要返回一个对象,那么可以使用Python2.4以后加入的reversed()和sorted()内建函数.它们像列表的方法一样工作,不同的是它们可以用做表达式,因为它们返回一个对象.同时原来的那个列表还是那个列表,没有改变,而你得到的是一个新的对象.
