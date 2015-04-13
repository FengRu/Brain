#Pandoc's markdown {#pandoc_markdown}

Pandoc扩展了John Gruber的markdown语法。在这篇文档中，阐述了pandoc扩展的markdown的语法规则，以及和标准markdown语法的区别。

通过使用 *markdown_strict* 代替 *markdown* 格式，可以禁用pandoc的语法扩展。可以对格式添加 *+EXTENSION* 来指定需要开启的扩展，通过对格式添加 *-EXTENSION* 来关闭对应的扩展。

如： *markdown_strict+footnotes* 表示在标准的markdown语法规则下开启footnotes扩展。同样的，可以在pandoc的扩展语法的markdown规则下禁用部分扩展，如果： *markdown-footnotes-pipe_tables* ，表示禁用了脚注和管线类型表格的扩展。

#哲学(Philosophy) {#philosophy}

Markdown 的设计目的是为了方便书写和阅读：

> A Markdown-formatted document should be publishable as-is, as plain text, without looking like it’s been marked up with tags or formatting instructions. – John Gruber

Pandoc的设计思想和Markdown的思想有些不同。Markdown的设计思想是为了生成HTML代码，但是Pandoc的设计目的是为了产生多种类型的文档。所以，当pandoc在对待内嵌html代码的情况，更多的是采用别的表示方式来表示HTML代码。

#段落(Paragraphs) {#paragraphs}

一个段落是由一行或者多行的文本组成的，通过一个或者多个空白行来分隔。换行符(Newlines)被认为是空格，如果需要断行，则需要在行尾添加 **至少2个空格**。

##扩展:escaped_line_break {#ext_escaped_line_break}

功能：

一个反斜杠加一个换行符也可以用来进行断行。注意：在网格类型的表格(grid table)的单元格中，只能用这种类型的断行符，因为后缀的空格会被忽略。

#标题(Headers) {#headers}

在markdown语法中，包含了两种类型的标题语法：Setext和atx

##Setext风格的标题 {#setext_style}

一个Setext风格的标题通过在文本下方添加=和-符号来表示不同等级的标题（只能表示两级）：

~~~

A level-one header
==================

A level-two header
------------------

~~~

这种类型的标题可以包含内联格式。

##Atx风格的标题 {#atx_style}

一个Atx风格的标题可以由1到6个#符号组成开始，接标题的文本，然后是可选的任意数目的#符号。其中开头的#符号的数目表示标题的等级，所以Atx风格的标题可以有6个等级。

~~~

## A level-two header

### A level-three header ###

~~~

和setext风格的标题类似，atx风格的标题也可以使用内联格式：

~~~

# A level-one header with a [link](/url) and *emphasis*

~~~

###扩展:blank_before_header {#ext_blank_before_header}

功能：

标准的markdown语法并不要求在标题前空一行。Pandoc对这个有要求（除了在文本的开头）。这么做是考虑到：可能会出现把#放在一行的开头用作包裹文本，而不是用于markdown语法，这容易产生错误。

##用于HTML，LaTex和ConText的标题标识符(identifiers) {#identifiers}

###扩展:header_attributes {#ext_header_attributes}

功能：

可以通过如下的语法对标题添加属性，放置在标题文本的最后：

~~~

{#identifier .class .class key=value key=value}
添加class属性可以有两种方式：.class方式和key=value方式。

~~~

因此，下面的标题都会包含一个标识符foo

~~~

# My header {#foo}

## My header {#foo}

My header {#foo}
---------

~~~

注意：虽然这个语法允许使用classes和键值对，但是这些都是用于输出Html或者基于Html格式的，如EPUB。而标识符用于LaTex，ConTeXt，Textile和AsciiDoc中的标签和链接。

标题如果使用了 *unnumbered* 类，那么即使指定了 *--number-sections* 选项，也不会被编号。或者在属性上下文中使用一个短线(-)来表示。：

~~~

# My header {-}

is just the same as

# My header {.unnumbered}

~~~

###扩展:auto_identifiers {#ext_auto_identifiers}

功能：

如果一个标题没有显式指定一个标识符属性，那么这个扩展可以根据标题的内容自动生成一个唯一的属性，通过对标题的内容进行推断：

* 剔除所有的格式信息，包括链接。
* 剔除所有的脚注(footnotes)。
* 剔除标点符号，下划线，短线(-)和句号除外。
* 替换所有的空格和换行符为短线(-)。
* 把所有的大写字符转换为小写字符。
* 如果标题不是由字符开始，则剔除所有非字符，直到标题是由字符开始的。
* 如果经过上述处理后没有文本剩下，则使用 *section* 作为标识符。

上面的规则，在大多数情况下，可以通过标题的内容推断出对应的标识符属性。但是，如果两个标题相等，则情况会不同：第一个出现的标题会按照上面的规则进行推断产生，后续的标题则会被添加上一个编号后缀 -N ，N从1开始进行编号。

注意：如果选项 *--section-divs* 被指定，那么每节文本都会被包裹在 *div* 标签中（或者在 *section* 标签中，如果指定了 *--html5* 选项）。并且这些属性会被添加到 *div* 或 *section* 标签中，而不是放到标题标签中。

###扩展:implicit_header_references {#ext_implicit_header_references}

功能：

Pandoc 默认为每一个标题都创建了引用，所以可以简写：

~~~

[header identifiers](#header-identifiers-in-html)

等价于

[header identifiers] or [header identifiers][] or 
[the section on header identifiers][header identifiers]

~~~

如果存在多个相同的标题，那么，只对第一个标题有效，对于其他标题，需要自己手动创建引用。

注意：和普通的引用链接不同，这些引用是大小写敏感的。如果手动创建了对应的引用，则自动创建的引用就不可用了。

#块引用(Block quotations) {#block_quotations}

Markdown采用email的方式来表示块引用。一个块引用由一个或多个段落，或者其他的块引用（嵌套）构成，每一行都是以 > 开头，并跟一个空格。（> 不必是一行的第一个字符，但是，在 > 前不能包含超过3个空格）。

~~~

 > This is a Block quote. This
 > paragraph has two lines.
 
 > 1. This is a list inside a block quote
 > 2. Second item

~~~

还有一个更加懒惰的方式是，每一个引用块只需要一个 < 就可以了。

~~~

 > This is a block quote. This 
 paragraph has two lines.

 > 1. This is a list inside a block quote.
 2. Second item

~~~

块引用可以嵌套使用：

~~~

 > This is a block quote.
 >
 > > A block quote within a block quote

~~~

##扩展:blank_before_blockquote {#ext_blank_before_blockquote}

功能：

标准的markdown语法不要求在一个引用块之前有一个空白行。Pandoc对这个有要求（除非在一个文档的开始处）。所以除非在 *markdown_strict* 模式下，下面的代码不会产生一个嵌套的引用块：

~~~

 > This is a block quote.
 >> Nested.

~~~

#代码块(Verbatim blocks) {#code_blocks}

##缩进代码块 {#indented_code_blocks}

一个代码块，如果缩进了一个tab或者4个空格，则被认为是一个代码块，这意味着，代码块中的特殊字符不会被做特殊用途，并且所有的空格和换行符都会保留。

~~~

    if (a > 3) {
	moveShip(5 * gravity, DOWN);
    }

~~~

在代码块前面的tab或4个空格，在输出成HTML格式的时候，会被丢弃。

注意：在代码块中的空白行，没有要求也缩进4个空格或一个tab。

#围栏代码块 {#fenced_code_blocks}

##扩展:fenced_code_blocks {#ext_fenced_code_blocks}

功能：

除了缩进代码块，Pandoc还支持 *围栏代码块(fenced code blocks)* 。以一行由三个或者更多的波浪线(~)或反引号(\`)组成的文本行开始，并且，以同样长度和类型的符号结束。包围着的文本则被认为是代码体，而且并不需要额外的缩进。

~~~~~~~

~~~~~~
if (a > 3) {
    moveShip(5 * gravity, DOWN);
}
~~~~~~

~~~~~~~

和普通的代码块一样，围栏代码块必须和包裹的文本之间有一个空白行。

如果代码中包含了一行波浪线或者反引号，则可以让包裹的文本行长度大于代码里的波浪线就可以了：

~~~~~~~~~~~~~~~

~~~~~~~~
~~~

code including tildes

~~~
~~~~~~

~~~~~~~~~~~~~~~

##扩展:fenced_code_attributes {#ext_fenced_code_attributes}

功能：

可以可选得给代码块添加属性，通过下面的语法：

~~~~~~~~~~

~~~ {#mycode .haskell .numberLines startFrom="100"}

qsort [] = []
qsort (x:xs) = qsrot (filter (< x) xs) ++ [x] ++
	       qsort (filter (>= x) xs)
~~~

~~~~~~~~~~

在上面的代码中， *mycode* 是标识符， *hashkell* 和 *numberLines* 是类，并且 *startFrom* 是一个值为100的属性。一些输出格式会利用这些属性信息来进行代码高亮。目前可以利用这些属性的输出格式是HTML和LaTeX。如果对于输出格式和代码语言可以进行高亮，则这个代码段就会高亮显示出来，并且包含行号。（可以通过命令 *pandoc --version* 来查看支持的语言）。如果不支持，则会显示如下的内容：

~~~

<pre id="mycode" class="haskell numberLines" startFrom="100">
    <code>
	...

    </code>
</pre>

~~~

或者采用更加精简的表示方式：

~~~~~~~~~~

```haskell
qsort [] = []
```

~~~~~~~~~~

上面的代码相当于是：

~~~~

``` {.haskell}
qsort [] = []
```

~~~~

为了阻止代码高亮，可以使用选项 *--no-highlight* 。如果需要设置高亮类型，则可以使用 *--highlight-style* 选项。

#行块(Line Block) {#line_block}

##扩展:line_blocks {#ext_line_blocks}

行块是一系列以竖线(|)和一个空格开头的文本行，行中的空格会在输出的结果中被保留。可以用于表示诗歌或者地址信息：

~~~

| The limerick packs laughs anatomical
| In space that is quite economical.
|    But the good ones I've seen
|    So seldom are clean
| And the clean ones so seldom are comical

| 200 Main St.
| Berkeley, CA 94718

~~~

文本行可以强制包裹(hard-wrapped)，但是被强制包裹的一行必须以空格开头：

~~~

| The Right Honorable Most Venerable and Righteous Samuel L.
  Constable, Jr.
| 200 Main St.
| Berkeley, CA 94718

~~~

#列表 {#lists}

## 子弹点列表(Bullet List) {#bullet_list}

一个子弹点列表是有一系列子弹项组成的。一个子弹点列表项以一个子弹点(\*,+,-)开头。

~~~

* one
* two
* three

~~~

上面的例子会产生一个紧凑的列表，如果想要产生一个相对宽松的列表，可以在列表项之间添加空格组成的文本行：

~~~

* one

* two

* three

~~~

子弹点标记不需要紧靠在行开头，可以适当的缩进1到3个空格，但是子弹点标记必须在后面紧跟一个空格。

列表项可以有多行，为了美观，每一行都应该和前一行缩进一致

~~~

* here is my first
  list item.
* and my second.

~~~

markdown允许偷懒：

~~~

* here is my first
list item.
* and my second.

~~~

## 4空格规则(The four-space rule) {#the_four_space_rule}

一个列表项可能包含多个段落和其他的块级别的内容。然而，随后跟的段落必须空一个空白行，并且在上一行的基础上缩进4个空格或者一个tab：

~~~

* First paragraph.
    
    Continued.

* Second paragraph. With a code block, which must be indented
  eight spaces:

    { code }

~~~

列表项中嵌套了列表，则嵌套的列表必须在上一个列表的基础上缩进一个空格或者一个tab，而他们之间的空白行是可选的。

~~~

* fruits
    + apples
        - macintosh
        - red delicious
    + pears
    + peaches
* vegetables
    + broccoli
    + chard

~~~

markdown允许使用更加懒的方式来书写列表，不用对接下来的文本行进行缩进。但是，如果在一个列表项中包含了多个文本段或者别的块，那么文本段或块的第一行必须缩进。

~~~

+ A lazy, lazy, list
item.

+ Another one; this looks
bad but is legal.

    Second paragraph of second
list item.

~~~

注意：虽然4空格规则作用于列表项的用法来自于markdown语法，但是markdown的实现 *Markdown.pl* 并不支持。所以Pandoc对于这种方式的语法，会给出和 *Markdown.pl* 不同的结果。

markdown语法规则中，对于4空格的规则只对列表项中的文本段和代码块有用，但是pandoc对列表项中的所有块都支持。

## 有序列表 {#orderd_list}

有序列表类似于子弹点列表，区别在于，有序列表是以枚举元素开始的。

在标准的markdown语法中，枚举元素是整数，后面跟一个点号和一个空格。至于枚举元素的值，markdown并不关心。所以，下面两种是一样的：

~~~

1.  one
2.  two
3.  three

~~~

~~~

5.  one
7.  two
1.  three

~~~

###扩展:fancy_lists {#ext_fancy_lists}

不像标准的markdown语法，Pandoc允许用大写字母，小写字母或者罗马数字来标记有序列表，列表标记可能会被括号包裹，或者在右边加一个右括号或者一个点号，并且和列表的文本部分间隔至少一个空格，如果列表标记是一个大写字母并且后跟一个点号，则至少间隔两个空格。

*fancy_lists* 扩展允许使用 *#* 符号来作为有序列表的标记，来代替数字。

~~~

#. one
#. two

~~~

###扩展:startnum #{ext_startnum}

对有序列表，Pandoc很关注标记的类型和标记的开始编号。Pandoc在输出的时候，都对这些内容尽可能的保留格式输出。因此，下面的例子会输出一个列表，编号格式为数字加右括号，从数字9开始，以及一个子列表，以小写的罗马数字开头：

~~~

9)  Ninth
10)  Tenth
11)  Eleventh
       i. subone
      ii. subtwo
     iii. subthree

~~~

Pandoc在碰到混用了多种类型的有序列表的时候，会把它们分为不同的独立的列表，所以下面的例子将会产生三个列表：

~~~

(2) Two
(5) Three
1.  Four
*   Five

~~~

如果需要使用默认的列表标记，采用 *#* 标记：

~~~

#.  one
#.  two
#.  three

~~~

## 定义列表(defintion lists) {#defintion_lists}

###扩展:definition_lists {#ext_definition_lists}

Pandoc支持定义列表，在采用 *PHP Markdown Extra* 语法的基础上添加了一些扩展。

~~~

Term 1

:   Definition 1

Term 2 with *inline markup*

:   Definition 2

        { some code, part of Definition 2 }

    Third paragraph of definition 2.

~~~

每一个需要被定义的名字必须在一行上，然后是可选的一个空白行，然后必须接一个或多个对这个名字的定义。定义的标记是冒号或者一个波浪线，并且标记可以缩进一个或两个空格。

每个名字可以有多个定义，而且每个定义可以包含一个或多个块元素（段落，代码块，列表等等），在定义中的块元素都需要在当前定义块的基础上缩进一个tab或者4个空格。定义的主体和定义的标记（冒号或波浪线）之间需要间隔4个空格。然而，可以使用偷懒的格式，除了第一行和内部嵌套的块元素以外，可以不缩进。

~~~

Term 1

:   Definition
with lazy continuation.

    Second paragraph of the definition.

~~~

如果在定义体之间留有空行（上面的例子），那么，定义的内容会被组织成段落。在一些格式输出中，为了紧凑，可以在定义体之间去掉空行来获得紧凑的格式：

~~~

Term 1
  ~ Definition 1

Term 2
  ~ Definition 2a
  ~ Definition 2b

~~~

注意：定义列表中两个定义（名字+定义体）之间的空行是必须的。

## 示例编号列表(numbered example lists) {#numbered_example_lists}

###扩展:example_lists {#ext_example_lists}

列表标记 **@** 用于连续的编号示例。列表的第一个以 **@** 开头的列表项别标记为‘1’，下一个别标记为‘2’，依次类推。可编号示例并不要求出现在一个列表中，在文本中的任何地方出现了以标记 **@** 开头的列表，都会从上一个结束的地方继续编号。

~~~

(@)  My first example will be numbered (1).
(@)  My second example will be numbered (2).

Explanation of examples.

(@)  My third example will be numbered (3).

~~~

示例编号可以包含标签，并且可以出现在文档的任何位置：

~~~

(@good)  This is a good example.

As (@good) illustrates, ...

~~~

标签可以是任何由字符，数字，下划线和短线组成的字符串。

## 紧凑的列表 {#compact_loose_lists}

Pandoc和 *Markdown.pl* 对于列表存在一些区别：

~~~

+   First
+   Second:
    -   Fee
    -   Fie
    -   Foe

+   Third

~~~

Pandoc会将上面的列表转换为一个紧凑列表（在"First"，"Second"和"Third"上没有"<p>"标签修饰。但是对于Makrdown，由于在"Third"前有一个空行，所以在"Second"和"Third"上会有<p>修饰。Pandoc遵循一个简单的规则：如果文本后接一个空白行，则认为该文本是一个段落。

## 结束一个列表 {#ending_a_list}

如果需要在列表后面放置一个缩进的代码块：

~~~

-   item one
-   item two

    { my code block }

~~~

Pandoc会认为 *{my code block}* 是属于列表 *item two* 的，而不会认为是一个代码块。

为了处理这种情况，可以在两部分内容之间放入一些不缩进的内容，比如HTML的注释，因为这些不会产生有效的输出：

~~~

-   item one
-   item two

<!-- end of list -->

    { my code block }

~~~

同样的，可以把连续的两个列表分开：

~~~

1.  one
2.  two
3.  three

<!-- -->

1.  uno
2.  dos
3.  tres

~~~

##水平线(Horizontal rules) {#horizontal_rules}

一个水平线可以由一行*，-，_符号组成，至少三个字符。并且可以用空格分隔。

~~~

*  *  *  *

---------------

~~~

#表格(Tables) {#tables}

Pandoc提供了4种表格，前三种表格适合等宽字体。第四种适合字体间距好看的字体。

##扩展:table_captions {#ext_table_captions}

Pandoc提供的所有4中表格中，都提供了对标题的支持，可以可选的提供表格的标题。一个表格的标题以"Table:"开头，或者只是":"开头，并且只能出现在表格的开头或结尾。

##扩展:simple_tables {#simple_tables}

简单表格如下定义：

~~~

Right       Left     Center     Default
-------     ------ ----------   -------
     12     12        12            12
    123     123       123          123
      1     1          1             1

Table:  Demonstration of simple table syntax.

~~~

表格的head和表格行必须都处在一行中，列对其的方式可以通过head和下方的短线行的相对位置来决定：

* 如果短线行和header右端对其，但是左端超过，则表示右对齐
* 如果短线行和header的左端对其，但是右端超过，则表示左对齐
* 如果短线行超过header的两端，则表示居中对齐
* 如果短线行和header的两端都对齐，则表示采用默认的对齐方式（一般是左对齐）

每一列的header可以省略，仅仅以短线行开始：

~~~

-------     ------ ----------   -------
     12     12        12             12
    123     123       123           123
      1     1          1              1
-------     ------ ----------   -------

~~~

当header被遗漏，表格的对齐方式就以第一行中每一列的内容和短线行的相对位置来决定。所以，上面每一列的对齐方式是：左对齐，右对齐，居中，右对齐。

##扩展:multiline_tables {#ext_multiline_tables}

多行表格允许header和表格行可以是多行文本组成，但是不支持单元格跨多列和多列。

~~~

-------------------------------------------------------------
 Centered   Default           Right Left
  Header    Aligned         Aligned Aligned
----------- ------- --------------- -------------------------
   First    row                12.0 Example of a row that
                                    spans multiple lines.

  Second    row                 5.0 Here's another one. Note
                                    the blank line between
                                    rows.
-------------------------------------------------------------

Table: Here's the caption. It, too, may span
multiple lines.

~~~

这种类型的表格和简单表格类似，但是又一下区别：

* 这种表格必须以一行短线行开始，在header之前
* 这种表格必须以一行短线结束，然后跟一个空白行
* 每一行必须以空白行分隔

在multiline表格中，表格宽度是依据markdown源代码的表格宽度的，所以，如果输出的宽度太小，可以通过修改markdown源代码中的宽度来改变输出的宽度。

multiline表格的标题也可以遗漏：

~~~

----------- ------- --------------- -------------------------
   First    row                12.0 Example of a row that
                                    spans multiple lines.

  Second    row                 5.0 Here's another one. Note
                                    the blank line between
                                    rows.
----------- ------- --------------- -------------------------

: Here's a multiline table without headers.

~~~

如果multiline表格只有一行，则表格行下方必须有一个空白行，然后才是短线行，否则会被解析成简单表格。

##扩展:grid_tables {#ext_grid_tables}

Grid表格如下：

~~~

: Sample grid table.

+---------------+---------------+--------------------+
| Fruit         | Price         | Advantages         |
+===============+===============+====================+
| Bananas       | $1.34         | - built-in wrapper |
|               |               | - bright color     |
+---------------+---------------+--------------------+
| Oranges       | $2.10         | - cures scurvy     |
|               |               | - tasty            |
+---------------+---------------+--------------------+

~~~

header和表格行之间通过等号(=)来分隔，如果表格没有表格头，则可以遗漏。表格的单元格可以包含任何块元素（多行段落，代码块，列表，等）。Grid表格不支持对齐，也不支持跨多行和多列。

##扩展:pipe_tables {#ext_pipe_tables}

Pipe表格如下：

~~~

| Right | Left | Default | Center |
|------:|:-----|---------|:------:|
|   12  |  12  |    12   |    12  |
|  123  |  123 |   123   |   123  |
|    1  |    1 |     1   |     1  |

  : Demonstration of pipe table syntax.

~~~

表格的每一行的开始和结束的管线可以省略，但是每一列之间的管线是必须的。表格中的冒号用于表示对齐方式，表格的头可以省略，但是分隔线必须存在，因为它用于定义对齐方式。

因为管线表示一列的边界，所以并不用对齐排列：

~~~

fruit| price
-----|-----:
apple|2.05
pear|1.37
orange|3.09

~~~

Pipe表格的单元格不能包含块元素，比如段落或列表，而且也不能跨多行。注意：在LaTeX/PDF的输出中，Pipe表格的单元格不会包裹，因为没有信息表示列宽是多少。如果需要对内容进行包裹，则需要使用multiline和grid表格。

Pandoc同样也支持如下的形式，通过Emacs的orgtbl-mode输出：

~~~

| One | Two   |
|-----+-------|
| my  | table |
| is  | nice  |

~~~

区别是，上面的方式用加号(+)来代替管线(|)。

# 元信息块(Metadata blocks) {#metadata_blocks}

## 扩展:pandoc_title_block {#ext_pandoc_title_block}

如果文件开头添加如下信息：

~~~

% title
% author(s) (separated by semicolons)
% date

~~~

则会生成特定于输出格式的元数据。元信息块可能包含一个标题，或者一个标题和一个作者信息，或者包含三者。如果需要只包含作者信息，而不包含标题信息，或者只包含标题和日期信息而不包含作者信息，则需要用空白代替不需要的部分

~~~

%
% Author

% My title
%
% June 15, 2006

~~~

标题可能会出现多行的情况，但是分行的文本必须缩进相同的空格。

~~~

% My title
  on multiple lines

~~~

如果元信息块包含多个作者，那么作者可以多行，但是必须缩进相同的空格，或者排列在一行中，中间用分号分隔，或者两者都有：

~~~

% Author One
  Author Two

% Author One; Author Two

% Author One;
  Author Two

~~~

date元信息必须在一行上。

元信息块可以包含内联格式，包括斜体，链接以及脚注等。
