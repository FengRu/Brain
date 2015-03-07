# 映射类型内建方法

~~~

 >>> help(dict)

~~~


**dict.clear()**
:   删除字典中所有元素\[a\]

**dict.copy()**
:   返回字典(浅复制)的一个副本\[a\]

**dict.fromkeys(seq,val=None)**
:   创建并返回一个新字典，以seq中的元素做该字典的键，val做该字典中所有键对应的初始值(如果不提供此值，则默认为None)\[c\]

**dict.get(key,default=None)**	
:   通过字典dict中的键key,返回它对应的值value，如果字典中不存在此键，则返回default的值(注意，参数default的默认值为None)\[a\]

**dict.has_key(key)**	
:   如果键(key)在字典中存在，返回True，否则返回False.在Python2.2版本引入in和not in后，此方法几乎已废弃不用了，但仍提供一个可工作的接口，在python3中去掉了该方法。

**dict.items()**
:   返回一个包含字典中(键,值)对元组的列表

**dict.keys()**
:   返回一个包含字典中键的列表

**dict.iter()**
:   方法iteritems(),iterkeys(),itervalues()与它们对应的非迭代方法一样，不同的是它们返回一个迭代子，而不是一个列表。\[d\](在python 3中没有该内建方法)

**dict.pop(key[,default])** 
    :			和方法get()相似，如果字典中key键存在，删除并返回dict[key]，如果key键不存在，且没有给出default的值，引发KeyError异常。\[c\]

**dict.setdefault(key,default=None)**
:   和方法set()相似，如果字典中不存在key键，由dict\[key\]=default为它赋值。\[e\]

**dict.update(dict2)**	
:   将字典dict2的键-值对添加到字典dict[a]

**dict.values()**	
:   返回一个包含字典中所有值的列表

a.Python1.5新增	    
c.Python2.3新增	    
d.Python2.2新增	    
e.Python2.0新增	    

keys()方法很有用，它返回一个包含字典中所有键的列表，此方法可以与 for 循环一起使用来获取字典中的值。但是，它返回的元素是没有顺序的(和哈希表中的键(keys)一样)，在 Python 2.4 版本以前，只能调用字典的 keys()方法获得键的列表，然后调用列表的sort()方法得到一个有序可遍历的列表。现在特别为迭代子设计了一个名为sorted()的内建函数，它返回一个有序的迭代子
