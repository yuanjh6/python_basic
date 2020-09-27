# python小白07wtfbook疑问和验证
What the f*ck Python! :https://github.com/leisurelicht/wtfpython-cn  
##  Deep down, we're all the same./本质上,我们都一样. 
```
class WTF:
  pass

```
Output:  
```
>>> WTF() == WTF() # 两个不同的对象应该不相等 ## 疑问:==本质是equals(__eq__)方法,2个无参对象，理论上应该相等吧,没有重写eq则默认id比对(也就是is比对)
False
>>> WTF() is WTF() # 也不相同
False
>>> hash(WTF()) == hash(WTF()) # 哈希值也应该不同
True
>>> id(WTF()) == id(WTF())
True
```
至于is和id理论上应该等结果，实际不同，原因在于  
```
class WTF(object):
  def __init__(self): print("I")
  def __del__(self): print("D")
```

Output:  
```
>>> WTF() is WTF()
I
I
D
D
False
>>> id(WTF()) == id(WTF())
I
D
I
D
True
```
正如你所看到的, 对象销毁的顺序是造成所有不同之处的原因.  

## For what?/为什么?
```
some_string = "wtf"
some_dict = {}
for i, some_dict[i] in enumerate(some_string):
    pass

```
Output:  
```
>>> some_dict # 创建了索引字典.
{0: 'w', 1: 't', 2: 'f'}


>>> i, some_dict[i] = (0, 'w')
>>> i, some_dict[i] = (1, 't')
>>> i, some_dict[i] = (2, 'f')
>>> some_dict
```
##  Evaluation time discrepancy/执行时机差异
```
array = [1, 8, 15]
g = (x for x in array if array.count(x) > 0)
array = [2, 8, 22]
Output:

>>> print(list(g))
[8]
```

```
array_1 = [1,2,3,4]
g1 = (x for x in array_1)
array_1 = [1,2,3,4,5]

array_2 = [1,2,3,4]
g2 = (x for x in array_2)
array_2[:] = [1,2,3,4,5]
Output:

>>> print(list(g1))
[1,2,3,4]

>>> print(list(g2))
[1,2,3,4,5]
```
在生成器表达式中, in 子句在声明时执行, 而条件子句则是在运行时执行.  
所以在运行前, array 已经被重新赋值为 [2, 8, 22], 因此对于之前的 1, 8 和 15, 只有 count(8) 的结果是大于 0 的, 所以生成器只会生 成 8.  
第二部分中 g1 和 g2 的输出差异则是由于变量 array_1 和 array_2 被重新赋值的方式导致的.  
在第一种情况下, array_1 被绑定到新对象 [1,2,3,4,5], 因为 in 子句是在声明时被执行的， 所以它仍然引用旧对象 [1,2,3,4](并没有被销毁).  
在第二种情况下, 对 array_2 的切片赋值将相同的旧对象 [1,2,3,4] 原地更新为 [1,2,3,4,5]. 因此 g2 和 array_2 仍然引用同一个对象  (这个对象现在已经更新为 [1,2,3,4,5]).  

##  is not what it is!/出人意料的is!
```
>>> a = 256
>>> b = 256
>>> a is b
True

>>> a = 257
>>> b = 257
>>> a is b
False

>>> a = 257; b = 257
>>> a is b
True
```
当你启动Python 的时候, 数值为 -5 到 256 的对象就已经被分配好了. 这些数字因为经常被使用, 所以会被提前准备好.  
 Python 通过这种创建小整数池的方式来避免小整数频繁的申请和销毁内存空间.  
引用自 https://docs.python.org/3/c-api/long.html  
当前的实现为-5到256之间的所有整数保留一个整数对象数组, 当你创建了一个该范围内的整数时, 你只需要返回现有对象的引用. 所以改变1的值是有可能的. 我怀疑这种行为在Python中是未定义行为. :-)  

当 a 和 b 在同一行中使用相同的值初始化时，会指向同一个对象.  
这是一种特别为交互式环境做的编译器优化. 当你在实时解释器中输入两行的时候, 他们会单独编译, 因此也会单独进行优化. 如果你在 .py 文件中尝试这个例子, 则不会看到相同的行为, 因为文件是一次性编译的.  

## A tic-tac-toe where X wins in the first attempt!/一蹴即至!
```
# 我们先初始化一个变量row
row = [""]*3 #row i['', '', '']
# 并创建一个变量board
board = [row]*3

```
Output:  
```
>>> board
[['', '', ''], ['', '', ''], ['', '', '']]
>>> board[0]
['', '', '']
>>> board[0][0]
''
>>> board[0][0] = "X"
>>> board
[['X', '', ''], ['X', '', ''], ['X', '', '']]
```

```
>>> board = [['']*3 for _ in range(3)]
>>> board[0][0] = "X"
>>> board
[['X', '', ''], ['', '', ''], ['', '', '']]
```

## The sticky output function/麻烦的输出
## not knot!/别纠结!
```
x = True
y = False
```
Output:  
```
>>> not x == y
True
>>> x == not y
  File "<input>", line 1
    x == not y
           ^
SyntaxError: invalid syntax
```
运算符的优先级会影响表达式的求值顺序, 而在 Python 中 == 运算符的优先级要高于 not 运算符.  
## Half triple-quoted strings/三个引号
```
>>> print('wtfpython''')
wtfpython
>>> print("wtfpython""")
wtfpython
>>> # 下面的语句会抛出 `SyntaxError` 异常
>>> # print('''wtfpython')
>>> # print("""wtfpython")
```

Python 提供隐式的字符串连接, 例如,  
```
>>> print("wtf" "python")
wtfpython
>>> print("wtf" "") # or "wtf"""
wtf
```
''' 和 """ 在 Python中也是字符串定界符, Python 解释器在先遇到三个引号的的时候会尝试再寻找三个终止引号作为定界符, 如果不存在则会导致 SyntaxError 异常.   

## Midnight time doesn't exist?/不存在的午夜?
在Python 3.5之前, 如果 datetime.time 对象存储的UTC的午夜时间(译: 就是 00:00), 那么它的布尔值会被认为是 False. 当使用 if obj: 语句来检查 obj 是否为 null 或者某些“空”值的时候, 很容易出错.  
## What's wrong with booleans?/布尔你咋了?
布尔值是 int 的子类   
```
>>> isinstance(True, int)
True
>>> isinstance(False, int)
True
```

## Class attributes and instance attributes/类属性和实例属性
```
class SomeClass:
    some_var = 15
    some_list = [5]
    another_list = [5]
    def __init__(self, x):
        self.some_var = x + 1
        self.some_list = self.some_list + [x]
        self.another_list += [x]
```
Output:  
```
>>> some_obj = SomeClass(420)
>>> some_obj.some_list
[5, 420]
>>> some_obj.another_list
[5, 420]
>>> another_obj = SomeClass(111)
>>> another_obj.some_list
[5, 111]
>>> another_obj.another_list
[5, 420, 111]
>>> another_obj.another_list is SomeClass.another_list
True
>>> another_obj.another_list is some_obj.another_list
True
```
类变量和实例变量在内部是通过类对象的字典来处理(译: 就是 __dict__ 属性). 如果在当前类的字典中找不到的话就去它的父类中寻找.  
+= 运算符会在原地修改可变对象, 而不是创建新对象. 因此, 在这种情况下, 修改一个实例的属性会影响其他实例和类属性.  
简单来说：**通过self实例方式访问类变量，是可读不可写的**，但**可通过list()append等引用方式绕过不可写限制**。实际尽可能**避免self方式访问实例变量，避免踩坑**。  
## Mutating the immutable!/强人所难
## Subclass relationships/子类关系
Output:  
```
>>> from collections import Hashable
>>> issubclass(list, object)
True
>>> issubclass(object, Hashable)
True
>>> issubclass(list, Hashable)
False
```
子类关系应该是可传递的, 对吧? (即, 如果 A 是 B 的子类, B 是 C 的子类, 那么 A 应该 是 C 的子类.)  

bulb 说明:  
Python 中的子类关系并不一定是传递的. 任何人都可以在元类中随意定义 __subclasscheck__.   
当 issubclass(cls, Hashable) 被调用时, 它只是在 cls 中寻找 __hash__ 方法或者从继承的父类中寻找 __hash__ 方法.  
由于 object is 可散列的(hashable), 但是 list 是不可散列的, 所以它打破了这种传递关系.   
在这里可以找到更详细的解释.   

## Let's see if you can guess this?/看看你能否猜到这一点?
```
a, b = a[b] = {}, 5
```
Output:  
```
>>> a
{5: ({...}, 5)}
```

## Section: Watch out for the landmines!/小心地雷!
Modifying a dictionary while iterating over it/迭代字典时的修改  
## Deleting a list item while iterating/迭代列表时删除元素
## Loop variables leaking out!/循环变量泄漏!
## Beware of default mutable arguments!/当心默认的可变参数!
##  Same operands, different story!/同人不同命!
a += b 并不总是与 a = a + b 表现相同. 类实现 op= 运算符的方式 也许 是不同的, 列表就是这样做的.  
表达式 a = a + [5,6,7,8] 会生成一个新列表, 并让 a 引用这个新列表, 同时保持 b 不变.  
表达式 a += [5,6,7,8] 实际上是使用的是 "extend" 函数, 所以 a 和 b 仍然指向已被修改的同一列表.  
## Be careful with chained operations/小心链式操作
```
形式上, 如果 a, b, c, ..., y, z 是表达式, 而 op1, op2, ..., opN 是比较运算符, 那么除了每个表达式最多只出现一次以外 a op1 b op2 c ... y opN z 就等于 a op1 b and b op2 c and ... y opN z.
```
##  Name resolution ignoring class scope/忽略类作用域的名称解析
1.  
```
x = 5
class SomeClass:
    x = 17
    y = (x for i in range(10))
```
Output:  
```
>>> list(SomeClass.y)[0]
5
```

2.  
```
x = 5
class SomeClass:
    x = 17
    y = [x for i in range(10)]
Output (Python 2.x):

>>> SomeClass.y[0]
17
```
Output (Python 3.x):  
```
>>> SomeClass.y[0]
5
```
bulb 说明:  
类定义中嵌套的作用域会忽略类内的名称绑定.  
生成器表达式有它自己的作用域.  
从 Python 3.X 开始, 列表推导式也有自己的作用域.  

## Needle in a Haystack/大海捞针
```
x, y = (0, 1) if True else None, None
```
Output:  
```
>>> x, y  # 期望的结果是 (0, 1)
((0, 1), None)
```
## Explicit typecast of strings/字符串的显式类型转换
```
a = float('inf')  
b = float('nan')  
c = float('-iNf')  # 这些字符串不区分大小写
d = float('nan')
```

join() 是一个字符串操作而不是列表操作. (第一次接触会觉得有点违反直觉)  
bulb 说明: 如果 join() 是字符串方法 那么它就可以处理任何可迭代的对象(列表，元组，迭代器). 如果它是列表方法, 则必须在每种类型中单独实现. 另外, 在 list 对象的通用API中实现一个专用于字符串的方法没有太大的意义.  
