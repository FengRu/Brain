# 集合类型内建方法 

~~~

 >>> help(set)
 >>> help(frozenset)

~~~

适用所有集合的方法

**s.issubset(t)**
:   如果s是t的子集，则返回 True,否则返回 False 

**s.issuperset(t)**
:   如果t是s的超集，则返回 True,否则返回 False 

**s.union(t)**
:   返回一个新集合，该集合 是s和t的并集 

**s.intersection(t)**
:   返回一个新集合，该集合是s和t的交集 

**s.difference(t)**
:   返回一个新集合，该集合是 s 的成员，但不是 t 的成员 

**s.symmetric_difference(t)**
:   返回一个新集合，该集合是 s 或 t 的成员，但不是s和t共有的成员 

**s.copy()**
:   返回一个新集合，它是集合 s 的浅复制 

仅适用于可变集合的方法

**s.update(t)**
:   用 t 中的元素修改 s, 即，s 现在包含s或t的成员 

**s.intersection_update(t)**
:   s中的成员是共同属 于s和t的元素。 

**s.difference_update(t)**
:   s中的成员是属于 s 但不包含在 t 中的元素 

**s.symmetric_difference_update(t)**
:   s中的成员更新为那些包含在s或t中，但不是s和t共有的元素 

**s.add(obj)**
:   在集合 s 中添加对象 obj 

**s.remove(obj)**
:   从集合 s 中删除对象 obj；如果 obj 不是集合 s 中的元素(obj not in s)，将引发 KeyError 错误 

**s.discard(obj)**
:   如果 obj 是集合 s 中的元素，从集合 s 中删除对象 obj； 

**s.pop()**
:   删除集合 s 中的任意一个对象，并返回它 

**s.clear()**
:   删除集合 s 中的所有元素

如果是接受参数的方法，参数必须是可哈希的。

## 操作符和内建方法比较

很多内建的方法几乎和操作符等价，它们之间有一个重要区别:当用操作符时，操作符两边的操作数必须是集合。在使用内建方法时，对象也可以是迭代类型的。
