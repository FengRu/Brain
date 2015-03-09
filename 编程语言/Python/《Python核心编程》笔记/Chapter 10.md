# Chapter 10 {#chapter_10}

## 异常

异常是因为程序出现了错误而在正常控制流以外采取的行为. 这个行为又分为两个阶段: 

* 首先是引起异常发生的错误
* 然后是检测和采取可能的措施阶段.

### Python中的异常

**NameError**
:   表示我们访问了一个没有初始化的变量

**ZeroDivisionError**
:   除数为零

**SyntaxError**
:   Python解释器语法错误(SyntaxError异常是唯一不是在运行时发生的异常)

**IndexError**
:   请求的索引超出序列范围

**KeyError**	
:   请求一个不存在的字典关键字

**IOError**
:   输入/输出错误

### 检测和处理异常

异常可以通过**try**语句来检测.任何在**try**语句块里的代码都会被监测,检查有无异常发生。

try语句有两种主要形式:

**try-except**和**try-finally.**这两个语句是互斥的,只能使用其中的一种.一个try语句可以对应一个或多个except子句,但只能对应一个finally子句,或是一个try-except-finally复合语句

~~~

try:
    some code
except SyntaxError, err:
    some handle code
else:
    if no exception occured, then execute code here

try:
    some code
finally:
    some handle code

try:
    some code
except SyntaxError, err:
except NameError as err:    #在Python 3中可以使用这种形式
    some handle code
finally:
    whatever exception occured, will execute code here

~~~

上面代码中的try-except-else语句中的else字句是可选的，表示当try语句块中没有出现异常的时候，才会去执行else中的代码。这个和try-except-finally语句是不同的，finally字句中的代码，无论try语句块中是否抛出异常，都会去执行。

#### try-finally语句

这个try-finally语句和try-except区别在于它不是用来捕捉异常的.作为替代,它常常用来维持一致的行为而无论异常是否发生无论try中是否有异常触发,finally代码段都会被执行。

当在try范围中产生一个异常时,(这里)会立即跳转到finally语句段。当finally中的所有代码都执行完毕后,会继续向上一层引发异常。

#### 异常处理机制

在程序运行时,解释器尝试执行try块里的所有代码,如果代码块完成后没有异常发生,执行流就会忽略except语句继续执行.而当except语句所指定的异常发生后,控制流立即跳转到对应的处理器(try子句的剩余语句将被忽略),如果在except字句中定义了异常，则异常的原因被保存在后面的对象中，可以在异常处理代码块中对保存的异常原因进行处理。

try语句块中异常发生点后的剩余语句永远不会到达(所以也永远不会执行).一旦一个异常被引发,就必须决定控制流下一步到达的位置.剩余代码将被忽略,解释器将搜索处理器,一旦找到,就开始执行处理器中的代码.如果没有找到合适的处理器,那么异常就向上移交给调用者去处理,这意味着堆栈框架立即回到之前的那个.如果在上层调用者也没找到对应处理器,该异常会继续被向上移交,直到找到合适处理器.如果到达最顶层仍然没有找到对应处理器,那么就认为这个异常是未处理的,Python解释器会显示出跟踪返回消息,然后退出。


#### 处理多个异常的except语句

可以在一个except子句里处理多个异常，except语句在处理多个异常时要求异常被放在一个元组里:

~~~

#Python 2.x
except (Exc1[, Exc2[, ... ExcN]])[, reason]: 
    suite_for_exceptions_Exc1_to_ExcN

#Python 3.x
except (Exc1[, Exc2[, ... ExcN]])[as reason]: 
    suite_for_exceptions_Exc1_to_ExcN

~~~

关于异常的一部分内容在Python2.5有了一些变化.异常被迁移到了new-style class上,这个类叫做 *BaseException* 异常的继承结构有了少许调整,*KeyboardInterrupt* 和 *SystemExit* 被从 *Exception* 里移出,和 *Exception* 平级:

###异常参数

异常也可以有参数,异常引发后它会被传递给异常处理器.当异常被引发后参数是作为附加帮助信息传递给异常处理器的.虽然异常原因是可选的,但标准内建异常提供至少一个参数,指示异常原因的一个字符串。

异常的参数可以在处理器里忽略,但Python提供了保存这个值的语法,把这个参数放在except语句后,接在要处理的异常后面.except语句的这个语法可以被扩展为：

~~~

#Python 2.x
except Exception[, reason]: 
    suite_for_Exception_with_Argument

#Python 3.x
except Exception[as reason]: 
    suite_for_Exception_with_Argument

~~~

reason是一个包含来自导致异常的代码的诊断信息的类实例.异常参数自身会组成一个元组,并存储为类实例(异常类的实例)的属性。reason是一个Exception类的实例。对于大多内建异常,也就是从StandardError派生的异常,这个元组只包含一个指示错误原因的字符串。无论reason只包含一个字符串或是由错误编号和字符串组成的元组,调用**str(reason)**总会返回一个良好可读的错误原因。



## 上下文管理

### with语句

**with**语句在Python2.6中正式启用,在Python2.6中,with成为关键字.如果在Python2.5使用with语句,必须用**from \_\_future\_\_ import with_statement**来导入它。

with语句用于：保证共享的资源的唯一分配,并在任务结束的时候释放它，并掩盖了处理的细节。

### with 语法的基本用法：

~~~

with context_expr [as var]: 
    with_suite

~~~

它仅能工作于支持上下文管理协议(context management protocol)的对象。意味着只有内建了"上下文管理"的对象可以和with一起工作。

### 部分支持该协议的对象

* file 
* decimal.Context 
* thread.LockType 
* threading.Lock 
* threading.RLock 
* threading.Condition 
* threading.Semaphore 
* threading.BoundedSemaphore

### 上下文管理协议

#### 上下文表达式(context_expr)/上下文管理器

~~~

with context_expr [as var]: 
    with_suite

~~~

当with语句执行时,便执行上下文表达式(context_expr)来获得一个上下文管理器.上下文管理器的职责是提供一个上下文对象.这是通过调用\_\_context\_\_()方法来实现的.该方法返回一个上下文对象,用于在with语句块中处理细节.有点需要注意的是上下文对象本身就可以是上下文管理器.所以context_expr既可以为一个真正的上下文管理器,也可以是一个可以自我管理的上下文对象.在后一种情况时,上下文对象仍然有\_\_context\_\_()方法,返回其自身。

一旦我们获得了上下文对象,就会调用它的\_\_enter()\_\_方法.它将完成with语句块执行前的所有准备工作.with行的语法中有一个可选的as声明变量跟随在context_expr之后.如果提供提供了变量,就以\_\_enter()\_\_返回的内容来赋值，否则,丢弃返回值。

执行了with语句块.当with语句块执行结束,无论是正常结束还是由于异常,都会调用上下文对象的\_\_exit()\_\_方法.\_\_exit\_\_()有三个参数.如果with语句块正常结束,三个参数全部是None.如果发生异常,三个参数的值的分别等于调用**sys.exc_info()**函数以后返回的三个值:异常类的类型对象,异常实例,和traceback跟踪对象。

因为上下文管理器主要作用于共享资源,\_\_enter()\_\_和\_\_exit()\_\_方法基本是处理需要分配和释放资源的低层次工作。

为了帮助编写对象的上下文管理器,有一个**contextlib**模块,包含了实用的修饰器,可以用在函数对象上而不用去操心关于\_\_context\_\_(),\_\_enter()\_\_,\_\_enter()\_\_,\_\_exit()\_\_这些方法的实现.

## 触发异常

除了处理解释器抛出的异常，也可以在需要的时候自己抛出异常。可以通过**raise**语句实现。

### raise语句

rasie一般的用法:

~~~

raise [SomeException [, args [, traceback]]]

~~~

**SomeExcpetion**是触发异常的名字，可以是异常实例或者是异常类。

args是一个可选参数,来传给异常.这可以是一个单独的对象也可以是一个对象的元组.当异常发生时,异常的参数总是作为一个元组传入.如果args原本就是元组,那么就将其传给异常去处理;如果args是一个单独的对象,就生成只有一个元素的元组

traceback是可选的(实际上很少用它)参数,如果有的话,则是当异常触发时新生成的一个用于异常-正常化(exception—normally)的追踪(traceback)对象。当想重新引发异常时,第三个参数很有用(可以用来区分先前和当前的位置).如果没有这个参数,就填写None.

如果SomeException是一个实例,我们就无需进行实例化了.这种情况下,不能有额外的参数，或者只能是None。

不含任何参数的raise语句结构是在Python1.5中新引进的,会引发当前代码块(codeblock)最近触发的一个异常.如果之前没有异常触发,会因为没可以有重新触发的异常而生成一个TypeError异常.

#### raise语句的用法

~~~

#触发一个异常,从exclass生成一个实例(不含任何异常参数)

raise exclass

#同上,除了现在不是类，通过函数调用操作符作用于类名生成一个新的exclass实例,同样也没有异常参数

raise exclass()

#同上,但同时提供的异常参数args,可以是一个参数也可以元组

raise exclass,args
raise exclass(args)

#同上,但提供一个追踪(traceback)对象tb供使用

raise exclass,args,tb

#通过实例触发异常(通常是exclass的实例)，如果实例是exclass的子类实例,那么这个新异常的类型会是子类的类型(而不是exclass);如果实例既不是exclass的实例也不是exclass子类的实例,那么会复制此实例为异常参数去生成一个新的exclass实例.

raise exclass,instance

#通过实例触发异常:异常类型是实例的类型;等价于raise

raise instance
instance.__class__,instance.

#(1.5新增)重新触发前一个异常,如果之前没有异常,触发TypeError.

raise 

~~~

## 断言

断言是一句表达式的值必须等于True，否则会触发异常，发生异常也意味着表达式为假.这些工作类似于C语言预处理器中assert宏,但在Python中它们在运行时构建(与之相对的是编译期判别)。断言通过assert语句实现,在1.5版中引入。

### 断言语句

断言语句等价于:如果断言成功不采取任何措施,否则触发**AssertionError**异常。

assert的语法:

~~~

assert expression[, arguments]

~~~

**AssertionError**异常和其他的异常一样可以用try-except语句块捕捉,但是如果没有捕捉,它将终止程序运行而且提供一个traceback。

## 标准异常

Python内建异常:

**BaseException**
:   所有异常的基类[a]

**SystemExit**
:   python解释器请求退出[b]

**KeyboardInterrupt**
:   用户中断执行(通常是输入^C)[c]

**Exception**
:   常规错误的基类[d]

**StopIteration**
:   迭代器没有更多的值[e]

**GeneratorExit**
:   生成器(generator)发生异常来通知退出[a]

**SystemExit**
:   Python解释器请求退出[h]

**StandardError**
:   所有的内建标准异常的基类[g]

**ArithmeticError**
:   所有数值计算错误的基类[d]

**FloatingPointError**
:   浮点计算错误[d]

**OverflowError**
:   数值运算超出最大限制

**ZeroDivisionError**
:   除(或取模)零(所有数据类型)

**AssertionError**
:   断言语句失败[d]

**AttributeError**
:   对象没有这个属性

**EOFError**
:   没有内建输入,到达EOF标记

**EnvironmentError**
:   操作系统错误的基类[d]

**IOError**
:   输入/输出操作失败

**OSError**
:   操作系统错误[d]

**WindowsError**
:   Windows系统调用失败[h]

**ImportError**
:   导入模块/对象失败

**LookupError**
:   无效数据查询的基类[d]

**IndexError**
:   序列中没有没有此索引(index)

**KeyError**
:   映射中没有这个键

**MemoryError**
:   内存溢出错误(对于Python解释器不是致命的)

**NameError**
:   未声明/初始化对象(没有属性)

**UnboundLocalErrorh**
:   访问未初始化的本地变量

**ReferenceError**
:   弱引用(Weakreference)试图访问已经垃圾回收了的对象[e]

**RuntimeError**
:   一般的运行时错误

**NotImplementedError**
:   尚未实现的方法[d]

**SyntaxErrorPython**
:   语法错误

**IndentationError**
:   缩进错误[g]

**TabError**
:   Tab和空格混用[g]

**SystemError**
:   一般的解释器系统错误

**TypeError**
:   对类型无效的操作

**ValueError**
:   传入无效的参数

**UnicodeError**
:   Unicode相关的错误[h]

**UnicodeDecodeError**
:   Unicode解码时的错误[i]

**UnicodeEncodeError**
:   Unicode编码时错误[i]

**UnicodeTranslateError**
:   Unicode转换时错误[f]

**Warning**
:   警告的基类[j]

**DeprecationWarning**
:   关于被弃用的特征的警告[j]

**FutureWarning**
:   关于构造将来语义会有改变的警告[i]

**OverflowWarning**
:   旧的关于自动提升为长整型(long)的警告[k]

**PendingDeprecationWarning**
:   关于特性将会被废弃的警告[i]

**RuntimeWarning**
:   可疑的运行时行为(runtimebehavior)的警告[j]

**SyntaxWarning**
:   可疑的语法的警告[j]

**UserWarning**
:   用户代码生成的警告[j]

a.Python2.5新增	    
b.在Python2.5前,Exception的子类SystemExit	
c.在Python2.5前,StandardError的子类KeyboardInterrupt	    
d.Python1.5新增,用基于类的异常来替代字符串	
e.Python2.2新增	    
f.Python1.6新增	    
g.Python2.0新增	    
h.Python1.6新增	    
i.Python2.3新增	    
j.Python2.1新增	    
k.Python2.2新增,但在Python2.4时移除	

所有的标准/内建异常都是从根异常派生的.目前,有3个直接从BaseException派生的异常子类**:SystemExit** ,**KeyboardInterrupt**和**Exception**，其他的所有的内建异常都是Exception的子类。

Python2.5中**SystemExit**和**KeyboardInterrupt**从Exception的继承中移到BaseException的继承下.这样可以允许如**except Exception**的语句捕获所有非控制程序退出的异常。

从Python1.5到Python2.4.x,异常是标准的类,在这之前,他们是字符串.从Python2.5开始,不再支持构建基于字符串的异常并且被正式的弃用。

## 异常和sys模块

另一种获取异常信息的途径是通过sys模块中exc_info()函数，此功能提供了一个3元组(3-tuple)的信息,多于单纯用异常参数所能获得的信息。

从sys.exc_info()得到的元组：

* exc_type: 异常类 
* exc_value: 异常类的实例 
* exc_traceback: 追踪(traceback)对象,是一个新增的追踪(traceback)对象.这一对象提供了的发生异常的上下文.它包含诸如代码的执行帧,异常发生时的行号等信息。

## 相关模块

**exceptions**	
:   内建异常(永远不用导入这个模块) 

**contextlib**	
:   为使用 with 语句的上下文对象工具 

**sys**	
:   包含各种异常相关的对象和函数(见 sys.ex*)
