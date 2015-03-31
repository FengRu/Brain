# 0x0 词法分析(Lexical analysis) {#lexical_analysis}

一个Python程序通过一个解析器(*parser*)被读入。输入到解析器的是通过词法分析器(*lexical analyzer*)生成的一系列记号(*tokens*)组成的文本流。本文介绍一个词法分析器是怎样将一个Python源文件变成由记号组成的文本流。

Python以Unicode编码格式读入源文件的文本，源文件的编码通过编码声明指定，默认是UTF-8编码格式([详细PEP 3120](http://www.python.org/dev/peps/pep-3120))。如果源文件在读入的时候不能被解码，则会抛出一个**SyntaxError**异常。

## 0x10 行结构(Line structure)

一个Python程序由一系列的逻辑行组成。

### 0x11 逻辑行(Logical lines)

逻辑行的结束是由一个**NEWLINE**标记(token)表示的。一条语句不能跨越逻辑行边界，除非**NEWLINE**在语法上被允许（比如：在混合语句之间可以出现**NEWLINE**标记）。一个逻辑行可以由一到多个物理行组成，多个物理行使用显式或隐式的行连接规则(*line joining rules*)。

### 0x12 物理行(Physical lines)

一个物理行由一系列的字符组成，以一个行结束符EOL(end-of-line)结束。在源文件中，可以使用任何平台上行结束符：Unix平台上使用ASCII字符LF作为行结束符，Windows平台使用ASCII字符序列CR LF作为行结束符，在老的Macintosh上，采用ASCII字符CR作为行结束符。所有的这些都是可以使用的，并且作用是等价的。

当Python被嵌入别的编程语言中，别的编程语言中如果需要传递字符串参数给Python API，对于换行符，需要使用标准的C字符串约定格式(换行符"\\n"表示ASCII字符LF，表示行结束符)。

### 0x13 注释(Comments)

注释由一个哈希字符 # 开始，这个哈希字符不是字符串字面值(literal)的一部分，一行注释以物理行结束符结束。一个注释表示一行逻辑行的结束，除非使用隐式行连接规则。注释在语法解析过程中被忽略。

~~~

#当一行的后面有注释，那么显式的行连接规则就不起作用了，添加的反斜杠都会被作为注释的一部分而失去作用

 >>> "hello" #comment\
 >>> "world"

#但是，当出现在隐式行连接规则中的时候，注释就不起作用了

 >>> print('hello' #comment
           "world")

~~~

### 0x14 编码声明(Encoding declarations)

如果一个注释出现在Python源文件的第一行或者第二行，并且可以被正则表达式 **coding[=:]\\s\*([-\\w.]+) **匹配，那么，这个注释就被用于作为这个源文件的编码声明。正则表达式匹配到的第一个分组声明了源文件的编码方式。编码声明表达式的格式如下：

~~~

# -*- coding: <encoding-name> -*-

~~~

这个编码格式的表示方式可以被GNU Emacs编辑器识别

~~~

# vim:fileencoding=<encoding-name>

~~~

这个编码格式的表示方式可以被Vim编辑器识别。

如果在源文件中定义编码声明，则默认的编码声明就是UTF-8。特别的，如果源文件的第一个字节是UTF-8字节序标记(b'\\xef\\xbb\\xbf')，则这个源文件的编码方式被声明为UTF-8格式(这种方式在微软的notepad中被支持)。

如果声明了一个编码声明，那么声明的编码的名字必须是可以被Python识别的。这个编码格式用于所有的词法分析，包括字符串字面值，注释，标识符的分析。编码声明必须独立在一行中。

### 0x15 显式行连接(Explicit line joining)

两个或多个物理行可以用反斜杠(\\)连接成一个逻辑行，当一个物理行以一个反斜杠结尾的时候，这个反斜杠不是一个字符串字面值的一部分，也不是一个注释的一部分，它表示和接下来的一行组合成一个逻辑行，反斜杠的作用是对后面的行结束符进行转义。

~~~python

if 1900 < year < 2100 and 1 <= month <= 12 \
   and 1 <= day <= 31 and 0 <= hour < 24 \
   and 0 <= minute < 60 and 0 <= second < 60:   # Looks like a valid date
        return 1

~~~

一个反斜杠如果在注释的结尾，则不会起到连接两行的作用。一个反斜杠不能用于连接一个标记(token)，除了作用于字符串字面值。除了字符串字面值以外，标记不能被通过一个反斜杠连接的两个物理行分隔。一个反斜杠如果不是出现在物理行的行尾，那么反斜杠只有是在字符串字面值中才是合法的。

### 0x16 隐式行连接(Implicit line joining)

出现在括号中（包括圆括号，方括号，大括号）的表达式可以跨多个物理行，而不用通过反斜杠来连接多个物理行。

~~~python

month_names = ['Januari', 'Februari', 'Maart',      # These are the
               'April',   'Mei',      'Juni',       # Dutch names
               'Juli',    'Augustus', 'September',  # for the months
               'Oktober', 'November', 'December']   # of the year
               
~~~

通过隐式行连接规则的连接的物理行可以包括注释，对这些物理行的缩进并没有要求。隐式行连接规则也可以作用在空白行上。在隐式行连接规则中，**NEWLINE**标记不会被生成。隐式行连接规则除了在括号中可以使用，也适用于三引号字符串中(triple-quoted)，但是在三引号中，物理行不能包含注释。

### 0x17 空白行(Blank lines)

一个只包含空格，制表符，tab符或者注释的逻辑行会被Python的词法分析器忽略(不会生成**NEWLINE**标记)。但是，在交互式输入中，对空白行的处理依赖于REPL(read-eval-print loop)的实现，在标准的交互式解释器中，一整行空白的逻辑行用于终止一个多行语句。

### 0x18 缩进(Indentation)

在逻辑行开头出现的空白符（空格和tab符）用于计算一行的缩进层次，缩进层次在Python中用于分隔语句块。

Tab符从左到右可以被替换为1到8个空格，这使得所有开头的这些字符，包括被替换的字符的个数是8的倍数。在逻辑行开头的非空格字符前的所有空格用于决定当前行的缩进层次。缩进是不能通过反斜杠来进行跨行的，在反斜杠前的所有空格将被用于计算当前行的缩进层次。

如果源代码中混合了tab和空格符，那么缩进会因为不一致而导致抛出一个**TabError**异常。

**跨平台兼容性：**因为非Unix平台的文本编辑器，在源文件中的缩进中可能会混合使用空格和tab符，并且，不同平台可能会显式限制缩进的级别。

一个水平制表符(formfeed)可能会出现在一行的开头，它会在计算缩进的时候被忽略，但是如果水平制表符出现在空格后面，则可能会导致未定义的效果(在一些情况下，这可能会导致空格数被计算成0)。

缩进层次被用于生成**INDENT**和**DEDENT**标记，通过栈来实现：

在读入源代码的第一行之前，将一个0压入栈中；这个压入的0将永远不会被弹出栈。后续压入这个栈的数字的值必须是严格递增的（自底向上），在每个逻辑行的开头，每一行的缩进级别会和栈顶的值进行比较。如果相等，则表示当前行是处在当前语句块中，不会发生什么操作。如果这个级别大于栈顶元素的值，则将这个级别压入栈中，并且生成一个**INDENT**标记。如果缩进级别小于栈顶的值，则这个缩进级别必须是和在栈中的某个值相等，在栈中的所有比当前缩进级别值大的值会被弹出栈，并且每弹出一个值，都会生成一个**DEDENT**标记。当处理到源文件的最后，会把还遗留在栈中的每个大于0的值依次出栈，并在每一次出栈后生成一个**DEDENT**标记。

如下的代码的缩进是正确的：

~~~python

def perm(l):
        # Compute the list of all permutations of l
    if len(l) <= 1:
                  return [l]
    r = []
    for i in range(len(l)):
             s = l[:i] + l[i+1:]
             p = perm(s)
             for x in p:
              r.append(l[i:i+1] + x)
    return r

~~~

而下方的代码，有缩进错误：

~~~python

 def perm(l):                       # error: first line indented
for i in range(len(l)):             # error: not indented
    s = l[:i] + l[i+1:]
        p = perm(l[:i] + l[i+1:])   # error: unexpected indent
        for x in p:
                r.append(l[i:i+1] + x)
            return r                # error: inconsistent dedent

~~~

在上面的代码中，前三个错误会被解析器判断出来，而最后一个错误，则会被词法分析器判断出来，因为这个return语句的缩进级别在当前的栈中并没有与之匹配的级别。

### 0x19 在标记间的空格(Whitespace between tokens)

空白符（空格，tab，水平制表符）除了可以出现在逻辑行的开头和字符串字面值中，也可以用于分隔标记。只有在两个标记之间连接后导致变成另外一个标记的情况下才会需要空格符来进行分隔。（比如：ab是一个标记，而a b则是两个标记）

### 0x20 其他标记(Other tokens)

除了**NEWLINE**，**INDENT**和**DEDENT**标记，还存在以下的这些标记： *标识符(identifiers)* ， *关键字(keywords)* ， *字面值(literals)* ， *操作符(operators)* 和 *分隔符(delimiters)* 。空白字符并不是标记，但是和分隔符担当同样的角色。标记通过贪心规则产生，一个标记由尽可能多的可以成为标记的符号组成。


### 0x30 标识符和关键字(Identifiers and keywords)

标识符（也被称为名字）通过如下的词法定义。

Python中的标识符的语法基于UAX-31 Unicode 标准附录（Unicode standard annex UAX-31），详细的描述定义在下方。具体可以查看[PEP 3131](https://www.python.org/dev/peps/pep-3131/)。

在ASCII的（U+0001~U+007F）范围内，组成Python 3的标识符的有效字符和Python 2.x是一致的：

* A-Z和a-z
* 下划线_
* 数字，但是不出现在第一个位置

Python 3.0引入了额外的字符，不在ASCII范围中。详细可以查看[PEP 3131](https://www.python.org/dev/peps/pep-3131/)。对于这些字符，分类方式采用包括在模块**unicodedata**模块中的Unicode Character Database的版本。

标识符的长度是没有现在，只要这个标识符符合如下的规则：

~~~

identifier   ::=  xid_start xid_continue*
id_start     ::=  <all characters in general categories Lu, Ll, Lt, Lm, Lo, Nl, the underscore, and characters with the Other_ID_Start property>
id_continue  ::=  <all characters in id_start, plus characters in the categories Mn, Mc, Nd, Pc and others with the Other_ID_Continue property>
xid_start    ::=  <all characters in id_start whose NFKC normalization is in "id_start xid_continue*">
xid_continue ::=  <all characters in id_continue whose NFKC normalization is in "id_continue*">

~~~

* Lu - uppercase letters
* Ll - lowercase letters
* Lt - titlecase letters
* Lm - modifier letters
* Lo - other letters
* Nl - letter numbers
* Mn - nonspacing marks
* Mc - spacing combining marks
* Nd - decimal numbers
* Pc - connector punctuations
* Other_ID_Start - explicit list of characters in PropList.txt to support backwards compatibility
* Other_ID_Continue - likewise

所有的标识符在解析的时候都会转换到的NFKC的形式，对于标识符的比较是基于NFKC的。

### 0x31 关键字(Keywords)

如下的标识符被作为保留字符，用作Python的关键字。这些单词不能作为普通的标识符使用。

~~~

False      class      finally    is         return
None       continue   for        lambda     try
True       def        from       nonlocal   while
and        del        global     not        with
as         elif       if         or         yield
assert     else       import     pass
break      except     in         raise

~~~

### 0x32 标识符的保留类别(Reserved classes of identifiers)

特定类型的标识符（除了关键字）具有特殊的意义。这些标识符具有前缀和后缀下划线。

\_\*
:	当执行导入语句 **from module import * ** 的时候，以下划线开头的名字不会被导入。但是，在交互式环境中，下划线代表的意思是，用于存储前一个语句的执行结果，这个名字在builtins模块中。如果不是在交互式环境中，则下划线不具有特殊的意义。

\_\_\*\_\_
:	系统自定义的名字。这些名字是由Python解释器定义和实现的。任何使用这种形式的标识符都是不提倡的。

\_\_*
:	类的私有成员，用于类定义中。这种类型的标识符会被重写来达到混淆的目的，使得名字在类中是私有的。具体可以查看[Identifiers(Names)](https://docs.python.org/3/reference/expressions.html#atom-identifiers)

### 0x40 字面值(Literals)

字面值是Python中的一些内建类型的常量值。

### 0x41 字符串和字节字面值(String and Bytes literals)

字符串的词法通过如下的方式描述：

~~~

stringliteral   ::=  [stringprefix](shortstring | longstring)
stringprefix    ::=  "r" | "u" | "R" | "U"
shortstring     ::=  "'" shortstringitem* "'" | '"' shortstringitem* '"'
longstring      ::=  "'''" longstringitem* "'''" | '"""' longstringitem* '"""'
shortstringitem ::=  shortstringchar | stringescapeseq
longstringitem  ::=  longstringchar | stringescapeseq
shortstringchar ::=  <any source character except "\" or newline or the quote>
longstringchar  ::=  <any source character except "\">
stringescapeseq ::=  "\" <any source character>

~~~

字节类型通过如下的方式描述：

~~~

bytesliteral   ::=  bytesprefix(shortbytes | longbytes)
bytesprefix    ::=  "b" | "B" | "br" | "Br" | "bR" | "BR" | "rb" | "rB" | "Rb" | "RB"
shortbytes     ::=  "'" shortbytesitem* "'" | '"' shortbytesitem* '"'
longbytes      ::=  "'''" longbytesitem* "'''" | '"""' longbytesitem* '"""'
shortbytesitem ::=  shortbyteschar | bytesescapeseq
longbytesitem  ::=  longbyteschar | bytesescapeseq
shortbyteschar ::=  <any ASCII character except "\" or newline or the quote>
longbyteschar  ::=  <any ASCII character except "\">
bytesescapeseq ::=  "\" <any ASCII character>

~~~

这里没有提及的一个语法上的限制是，空白字符不允许出现在stringprefix（byteprefix）和其他字符之间。源代码的编码方式通过编码声明指定，默认是UTF-8编码。

在一般情况下，字节和字面值都可以被单引号和双引号包裹，或者被三引号包裹。在引号中的特殊字符，可以用反斜杠进行转义，比如换行符，反斜杠自身或者引号。

字节类型的字面值通常有一个'b'或'B'前缀，这会产生一个字节类型的实例，而不是一个字符串类型。在字节类型中，只能包含128以内的ASCII字符。

在Python 3.3中，可以通过前缀'u'来兼容Python 2.x和Python 3.x代码。

字符串字面值和字节字面值都可以前缀'r'和'R'。这些字符字面值被称为原始字符串，并且会把反斜杠作为普通的字面值。所以在字符串中的特殊字符将不会有特殊用途。注意：Python 3.x中不支持'ur'前缀。

在Python 3.3中，'rb'前缀是和'br'前缀等价的。

在Python 3.3中，为了兼容Python 2.x，重新引入了unicode前缀'u'。

在三引号字符串字面值中，没有转义的换行符和引号不会被作为特殊字符，保留了它们的字面值。

除非'r'或'R'前缀被指定，否则这些转义字符的行为和标准的C语言中定义的类似。

和标准C中的定义不同的是，所有不能识别的转义字符如果出现在序列中，则将保留原样，不会被转义。

在原始字符串中，转义字符将失去作用。但是，在原始字符串中，不能以奇数个反斜杠结尾，**r"\"**是错误的。注意，在原始字符串中，在换行符前的反斜杠将不起作用，不能进行显式的行连接。

### 0x42 字符串字面值的连接(String literal concatenation)

多个以空白符分隔的字符串字面值或字节字面值会被连接成一个字符串字面值或字节字面值，即使他们采用不同的引号包裹。这个特性可以用于通过多行分隔长的字符串的时候减少对反斜杠的使用，甚至可以在每一行中添加注释。

~~~

"hello" "world" ==> "helloworld"

re.compile("[A-Za-z_]"       # letter or underscore
           "[A-Za-z0-9_]*"   # letter, digit or underscore
          )

~~~

注意：这个特性虽然是语法层面的，但是却是在编译期是实现的。如果要在执行过程中实现字符串的连接，则需要使用加号'+'来进行连接。

### 0x43 数字字面值(Numeric literals)

存在三种类型的数字字面值：整数，浮点数和虚数。Python没有复数字面值（复数可以通过一个实部加上一个虚部来得到）。注意，数字字面值没有符号，-1表示的是一个单目运算符-和一个数字1。

0x 43 整数字面值(Integer literals)

整数字面值通过如下的方式定义：

~~~

integer        ::=  decimalinteger | octinteger | hexinteger | bininteger
decimalinteger ::=  nonzerodigit digit* | "0"+
nonzerodigit   ::=  "1"..."9"
digit          ::=  "0"..."9"
octinteger     ::=  "0" ("o" | "O") octdigit+
hexinteger     ::=  "0" ("x" | "X") hexdigit+
bininteger     ::=  "0" ("b" | "B") bindigit+
octdigit       ::=  "0"..."7"
hexdigit       ::=  digit | "a"..."f" | "A"..."F"
bindigit       ::=  "0" | "1"

~~~

在Python中，对于整数的位数没有限制，这个长度取决于内存的大小。

注意，前缀0在非0的十进制数中是不允许的。这是为了防止和Python 3.0之前的C风格的八进制数发生歧义。

整数的一些例子：

~~~

7     2147483647                        0o177    0b100110111
3     79228162514264337593543950336     0o377    0x100000000
      79228162514264337593543950336              0xdeadbeef

~~~

0x 44 浮点数字面值(Floating point literals)

浮点数字面值通过如下的方式描述：

~~~

floatnumber   ::=  pointfloat | exponentfloat
pointfloat    ::=  [intpart] fraction | intpart "."
exponentfloat ::=  (intpart | pointfloat) exponent
intpart       ::=  digit+
fraction      ::=  "." digit+
exponent      ::=  ("e" | "E") ["+" | "-"] digit+

~~~

注意：整数部分和指数部分一般会以十进制的方式解析，比如：077e010会被解释成77e10。浮点数的范围依赖于实现。

如点数的一些例子：

~~~

3.14    10.    .001    1e100    3.14e-10    0e0

~~~

### 0x46 虚数字面值(Imaginary literals)

虚数字面值通过如下的方式描述：

~~~

imagnumber ::=  (floatnumber | intpart) ("j" | "J")

~~~

一个虚数字面值会产生一个实部为0.0的复数。复数可以通过一个浮点数表示的实部和一个虚数来表示。

虚数的一些例子：

~~~

3.14j   10.j    10j     .001j   1e100j  3.14e-10j

~~~

### 0x50 操作符(Operators)

如下的标记表示操作符：

~~~

+       -       *       **      /       //      %
<<      >>      &       |       ^       ~
<       >       <=      >=      ==      !=

~~~

### 0x60 分隔符(Delimiters)

如下的标记被作为分隔符：

~~~

(       )       [       ]       {       }
,       :       .       ;       @       =
+=      -=      *=      /=      //=     %=
&=      |=      ^=      >>=     <<=     **=

~~~

其中，点号也出现在虚数和浮点数中。三个点号组成的序列表示一个省略号字面值(ellipsis literal)。其中第二部分的赋值操作符，既是分隔符，也用于操作。

如下的几个字符在标记中有特殊的用途，或者对于词法分析器有特殊的用途：

~~~

'       "       #       \

~~~

如下的几个字符在Python中没有使用，这些字符如果出现在字符串和注释外面，会造成未定义的错误：

~~~

$       ?       `

~~~
