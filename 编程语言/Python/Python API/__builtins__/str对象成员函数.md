# str对象成员函数

~~~

 >>> help(str)

~~~

**strObj.capitalize()**
:   把字符串的第一个字符大写

**strObj.center(width)**
:   返回一个原字符串居中,并使用空格填充至长度width的新字符串

**strObj.count(str,beg=0,end=len(sample_string))**
:   返回str在strObj里面出现的次数，如果beg或者end指定则返回指定范围内str出现的次数

**strObj.decode(encoding='UTF-8',errors='strict')**
:   以encoding指定的编码格式解码sample_string，如果出错默认报一个ValueError的异常，除非errors指定的是'ignore'或者'replace'

**strObj.encode(encoding='UTF-8',errors='strict')**
:   以encoding指定的编码格式编码sample_string，如果出错默认报一个ValueError的异常，除非errors指定的是'ignore'或者'replace'\[a\]

**strObj.endswith(obj,beg=0,end=len(sample_string))**
:   检查字符串是否以obj结束，如果beg或者end指定则检查指定的范围内是否以obj结束，如果是，返回True,否则返回False.\[b,e\]

**strObj.expandtabs(tabsize=8)	
:   把字符串strObj中的tab符号转为空格，默认的空格数tabsize是8.

**strObj.find(str,beg=0,end=len(sample_string))**
:   检测str是否包含在strObj中，如果beg和end指定范围，则检查是否包含在指定范围内，如果是返回开始的索引值，否则返回-1

**strObj.index(str,beg=0,end=len(sample_string))**
:   跟find()方法一样，只不过如果str不在strObj中会报一个异常.

**strObj.isalnum()**	
:   如果strObj至少有一个字符并且所有字符都是字母或数字则返回True,否则返回False()\[a,b,c\]

**strObj.isalpha()**	
:   如果strObj至少有一个字符并且所有字符都是字母则返回True,否则返回False\[a,b,c\]

**strObj.isdecimal()**	
:   如果strObj只包含十进制数字则返回True否则返回False.\[b,c,d\]

**strObj.isdigit()**
:   如果strObj只包含数字则返回True否则返回False.\[b,c\]

**strObj.islower()**
:   如果strObj中包含至少一个区分大小写的字符，并且所有这些(区分大小写的)字符都是小写，则返回True，否则返回False\[b,c\]

**strObj.isnumeric()**
:   如果strObj中只包含数字字符，则返回True，否则返回False\[b,c,d\]

**strObj.isspace()**	
:   如果strObj中只包含空格，则返回True，否则返回False.\[b,c\]

**strObj.istitle()**
:   如果strObj是标题化的(见title())则返回True，否则返回False\[b,c\]

**strObj.isupper()**	
:   如果strObj中包含至少一个区分大小写的字符，并且所有这些(区分大小写的)字符都是大写，则返回True，否则返回False\[b,c\]

**strObj.join(seq)**	
:   Merges(concatenates)以strObj作为分隔符，将seq中所有的元素(字符串表示)合并为一个新的字符串

**strObj.ljust(width)**	
:   返回一个原字符串左对齐,并使用空格填充至长度width的新字符串

**strObj.lower()**
:   转换strObj中所有大写字符为小写.

**strObj.lstrip()**
:   截掉strObj左边的空格

**strObj.partition(str)**
:   有点像find()和split()的结合体,从str出现的第一个位置起,把字符串strObj分成一个3元素的元组(string_pre_str,str,string_post_str),如果strObj中不包含str则string_pre_str的值为这个字符串，而后面两个位置的值为空字符串。\[e\]

**strObj.replace(str1,str2,num=strObj.count(str1))**
:   把strObj中的str2替换成str2,如果num指定,则替换不超过num次.

**strObj.rfind(str,beg=0,end=len(strObj))**	
:   类似于find()函数，不过是从右边开始查找.

**strObj.rindex(str,beg=0,end=len(strObj))**
:   类似于index()，不过是从右边开始.

**strObj.rjust(width)**
:   返回一个原字符串右对齐,并使用空格填充至长度width的新字符串

**strObj.rpartition(str)**
:   类似于partition()函数,不过是从右边开始查找.\[e\]

**strObj.rstrip()**
:   删除strObj字符串末尾的空格.

**strObj.split(str=" ",num=strObj.count(str))**
:   以str为分隔符从左往右分割strObj，如果num有指定值，则仅分隔num个子字符串。

**strObj.splitlines(num=strObj.count('\n'))**
:   按照行分隔，返回一个包含各行作为元素的列表，如果num指定则仅切片num个行.[b,e]

**strObj.startswith(obj,beg=0,end=len(strObj))**
:   检查字符串是否是以obj开头，是则返回True，否则返回False。如果beg和end指定值，则在指定范围内检查\[b,e\]

**strObj.strip([obj])**
:   在strObj上执行lstrip()和rstrip()

**strObj.swapcase()**
:   翻转strObj中的大小写

**strObj.title()**
:   返回"标题化"的strObj,就是说所有单词都是以大写开始，其余字母均为小写(见istitle())\[b,c\]

**strObj.translate(str,del="")**
:   根据str给出的表(包含256个字符)转换strObj的字符,要过滤掉的字符放到del参数中

**strObj.upper()**
:   转换strObj中的小写字母为大写

**strObj.zfill(width)**
:   返回长度为width的字符串，原字符串strObj右对齐，前面填充0


a.Python1.6 中只适用于 Unicode 字符串，2.0 中适用于所有字符串	    
b.1.5.2 版本中 string 模块没有该方法	    
c.在 Jython2.1 有	
d.仅对 Unicode 字符串有效	 
e.Python2.5 或者以上版本	

