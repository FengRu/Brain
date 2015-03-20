# Tkinter NoteBook {#tkinter_notebook}

## Tkinter 简介

Tkinter(TK interface)模块是Tk GUI toolkit的标准Python接口。

Tk和Tkinter在大多数的Unix平台，Macintosh系统和Windows系统上都得到广泛的支持，从8.0版本开始，Tk支持UI外观的本地化。

Tkinter包含了一系列的模块，Tk接口是通过一个称为**\_tkinter**的二进制扩展模块来提供支持的。这个模块包含了Tk的底层接口，应用开发人员应该避免直接使用这些接口。一般都是通过动态链接库（DLL）的方式提供，或者直接静态链接到Python解释器中。

公共接口通过一系列的Python模块提供。其中最重要的模块是**Tkinter**模块，为了使用**Tkinter**，需要导入这个模块：

~~~

import Tkinter

~~~

或者直接导入到当前的名字空间中：

~~~

from Tkinter import *

~~~

**Tkinter**模块只是导入了widget类和一些关联的常量，所以可以直接采用from-import的方式来安全的导入。

## 一个 Hello World 的例子

~~~

from tkinter import *

root = Tk()

w = Label(root, text="Hello, world!")
w.pack()

root.mainloop()

~~~

为了初始化Tkinter，需要先创建一个root widget，这是一个普通的窗口。每个Tkinter程序应该都只能有一个root widget，并且必须是在所有的widget创建之前被创建：

~~~

root = Tk()

~~~

然后创建一个**Label** widget，作为root widget的子widget：

~~~

w = Label(root, text = "Hello, world!")
w.pack()

~~~

一个**Label** widget可以显示文本和图片，在这里只是演示了怎样显示文本。

然后，我们调用pack()方法，这表示告诉这个**Label**对象，调整这个widget的尺寸，使得可以放下这些文本，并且使得它可见。但是这个时候窗口还没有显示出来，我们需要进入Tkinter的事件循环以后才会出现窗口：

~~~

root.mainloop()

~~~

现在，程序将一直存在，直到我们关闭窗口。事件循环不只是处理来自用户触发的事件，同时也处理来自窗口系统提交的事件。它也同时处理Tkinter自己的操作队列，比如几何管理（通过pack()方法）和显示更新。

## 又一个 Hello World 程序

~~~

from tkinter import *

class App:

    def __init__(self, master):

        frame = Frame(master)
        frame.pack()

        self.button = Button(
            frame, text="QUIT", fg="red", command=frame.quit
            )
        self.button.pack(side=LEFT)

        self.hi_there = Button(frame, text="Hello", command=self.say_hi)
        self.hi_there.pack(side=LEFT)

    def say_hi(self):
        print "hi there, everyone!"

root = Tk()

app = App(root)

root.mainloop()
root.destroy() # optional

~~~

上诉代码执行以后，当点击右边的hello按钮，会在控制台中显示"hi there, everyone!"的信息。点击Quit，则会退出程序。

在一些Python开发环境中执行上面的代码会出现问题，这是由于一些开发环境使用Tkinter自身，而mainloop()调用和quit()调用是开发环境期望的行为，而有些环境，如果没有显式添加destroy()，则可能会导致崩溃。

在程序中的构造函数中，创建了一个**Frame** widget对象，一个frame是一个简单的容器，用于包含别的widget。

frame widget被创建在一个局部变量中，当构造函数返回以后，frame局部变量就离开了作用域，被销毁了。但是，frame局部变量引用的对象还是存在的，在tkinter中，并不需要保持对widget实例的引用，tkinter自己维护了一个widget tree（通过每个子widget实例的children属性），所以一个widget实例在应用程序的最后一个引用销毁前，都是存在的。而销毁一个对象的引用，需要显式调用destroy方法。但是如果你需要在创建完一个对象后继续对这个对象进行处理，则需要保留对这个对象的引用。

## Tkinter Classes

### Widget Classes

Tkinter支持15个核心的widgets：

Button
:   一个简单的按钮控件，用于执行一个命令或者其他的操作。

Canvas
:   结构化的图形，这个控件用于绘图，以及定制widget。

Checkbutton
:   代表一个可以拥有两个值的变量，通过点击按钮来切换这两个值。

Entry
:   一个文本入口域

Frame
:   一个容器控件，frame可以又有一个边框和背景，用于组织其他的控件。

Label
:   显示一个文本或图片

Listbox
:   显示一个列表，listbox可以被配置成一个radiobutton或者是checklist

Menu
:   一个菜单面板，用于实现下拉和弹出菜单

Menubutton
:   一个菜单按钮，用于实现下拉菜单

Message
:   显示文本，和label 控件类似，但是可以在给定的宽度内对文本进行包裹

Radiobutton
:   代表一个变量的值，可以取多个值中的一个。点击这个按钮设置其中的一个值。

Scale
:   允许通过拖拽一个滑条来设置一个实数值

Scrollbar
:   标准的滚动条，用于canvas，entry，listbox和text控件。

Text
:   格式化文本输出，允许用多种样式来显示和编辑文本，也支持内嵌图片和窗口。

Toplevel
:   一个容器控件，作为一个独立的顶层窗口。

在Python 2.3(Tk 8.4)中，下方的这些包被加入进来：

LabelFrame
:   一个Frame控件，可以包含边框和标题。

PanedWindow
:   一个容器控件，用于在一个可控制大小的容器中组织子控件。

Spinbox
:   一个实体控件，用于在一个范围内或者一个有序集合中选择值。

注意：在Tkinter中没有控件是类继承体系的，所有的控件在继承树中都是兄弟关系。
