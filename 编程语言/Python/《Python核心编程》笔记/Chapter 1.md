#Chapter 1 {#chapter_1}

Python源文件通常用.py扩展名。当源文件被解释器加载或者显式地进行字节码编译的时候会被编译成字节码。由于调用解释器的方式不同，源文件会被编译成带有 *.pyc* 和 *.pyo* 扩展名的文件。

##Python命令选项

python命令的选项：
-d
:   提供调试输出

-O
:   生成优化的字节码(生成.pyo文件)

-S
:   不导入site模块以在启动时查找Python路径

-v
:   冗余输出

-m mod	
:   将一个模块以脚本形式运行

-Q opt	
:   除法选项

-c cmd
:   运行以命令行字符串形式提交的Python脚本

file
:   从给定的文件运行Python脚本

##直接运行Python文件

~~~

# python pythonfile.py

~~~


或者在python文件第一行添加

~~~

#! path/to/python

~~~

然后直接运行python文件(这适合在Unix衍生的系统中运行)
