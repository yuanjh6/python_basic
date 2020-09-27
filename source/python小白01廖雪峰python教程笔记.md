# python小白01廖雪峰python教程笔记
## 高级特性
切片：前10个数，每两个取一个：  L[:10:2]：[0, 2, 4, 6, 8]  
可迭代对象的判断：  isinstance('abc', Iterable)  
list变成索引-元素对：for i, value in enumerate(['A', 'B', 'C']):print(i, value)  
列表生成式：一行语句生成list：[x * x for x in range(1, 11)]
生成器：一边循环一边计算的机制，称为生成器：generator。  
第一种方法很简单，只要把一个列表生成式的[]改成()，就创建了一个generator：g = (x * x for x in range(10))  
另一种方法。如果一个函数定义中包含yield关键字，那么这个函数就不再是一个普通函数，而是一个generator：  
f = fib(6)  
f   
<generator object fib at 0x104feaaa0>  

定义默认参数要牢记一点：默认参数必须指向不变对象！  
Python函数在定义的时候，默认参数L的值就被计算出来了，即[]，因为默认参数L也是一个变量，它指向对象[]，每次调用该函数，如果改变了L的内容，则下次调用时，默认参数的内容就变了，不再是函数定义时的[]了。  
Python函数返回值是单一值，元组  


## 函数式编程  
list(map(str, [1, 2, 3, 4, 5, 6, 7, 8, 9]))  
reduce(f, [x1, x2, x3, x4]) = f(f(f(x1, x2), x3), x4)  
reduce(fn, map(char2num, '13579'))  
13579  
reduce(lambda x, y: x * 10 + y, map(char2num, s))  
list(filter(not_empty, ['A', '', 'B', None, 'C', '  ']))  
sorted([36, 5, -12, 9, -21], key=abs)  
sorted(['bob', 'about', 'Zoo', 'Credit'], key=str.lower, reve rse=True)  

函数作为返回值  
返回闭包时牢记一点：返回函数不要引用任何循环变量，或者后续会发生变化的变量。   


匿名函数：list(map(lambda x: x * x, [1, 2, 3, 4, 5, 6, 7, 8, 9]))  
装饰器：含有参数装饰器  

```
import functools

def log(func):
    @functools.wraps(func)
    def wrapper(*args, **kw):
        print('call %s():' % func.__name__)
        return func(*args, **kw)
    return wrapper
    
import functools

def log(text):
    def decorator(func):
        @functools.wraps(func)
        def wrapper(*args, **kw):
            print('%s %s():' % (text, func.__name__))
            return func(*args, **kw)
        return wrapper
    return decorator
``` 
其他:unboundlocalerror,闭包,偏函数  

## 模块
```
>>> import sys
>>> sys.path.append('/Users/michael/my_py_scripts')
```
## 面向对象编程  
变量名类似__xxx__的，也就是以双下划线开头，并且以双下划线结尾的，是特殊变量，特殊变量是可以直接访问的，不是private变量，所以，不能用__name__、__score__这样的变量名。  
有些时候，你会看到以一个下划线开头的实例变量名，比如_name，这样的实例变量外部是可以访问的，但是，按照约定俗成的规定，当你看到这样的变量时，意思就是，“虽然我可以被访问，但是，请把我视为私有变量，不要随意访问”。  
但是强烈建议你不要这么干，因为不同版本的Python解释器可能会把__name改成不同的变量名。  
总的来说就是，Python本身没有任何机制阻止你干坏事，一切全靠自觉。  
表面上看，外部代码“成功”地设置了__name变量，但实际上这个__name变量和class内部的__name变量不是一个变量！内部的__name变量已经被Python解释器自动改成了_Student__name，而外部代码给bart新增了一个__name变量.  

type,isinstance  
isinstance([1, 2, 3], (list, tuple))  
hasattr(obj, 'x') # 有属性'x'吗？  
setattr(obj, 'y', 19) # 设置一个属性'y'  


## 面向对象高级编程  
s.name = 'Michael' # 动态给实例绑定一个属性  
from types import MethodType  
s.set_age = MethodType(set_age, s) # 给实例绑定一个方法  
Student.set_score = set_score  
```
class Student(object):  
    __slots__ = ('name', 'age') # 用tuple定义允许绑定的属性名称  
__slots__定义的属性仅对当前类实例起作用，对继承的子类是不起作用的：  

Python内置的@property装饰器就是负责把一个方法变成属性调用的
@property
def score(self):
    return self._score

@score.setter
def score(self, value):

@unique
class Weekday(Enum):
    Sun = 0 # Sun的value被设定为0
    Mon = 1
    Tue = 2
```
多重继承

```
__str__  
__repr__()  
__iter__  
__next__(self):  
__getitem__  
__getitem__()传入的参数可能是一个int，也可能是一个切片对象slice  
负数作处理  
如果把对象看成dict，__getitem__()的参数也可能是一个可以作key的object，例如str。  
与之对应的是__setitem__()方法，把对象视作list或dict来对集合赋值。最后，还有一个__delitem__()方法，用于删除某个元素。  
__getattr__  
__call__  
>>> s = Student('Michael')  
>>> s() # self参数不要传入  
My name is Michael.  
  
type()  
>>> Hello = type('Hello', (object,), dict(hello=fn)) # 创建Hello class  
>>> h = Hello()  
>>> h.hello()  
metaclass是Python面向对象里最难理解，也是最难使用的魔术代码。正常情况下，你不会碰到需要使用metaclass的情况，所以，以下内容看不懂也没关系，因为基本上你不会用到。  
  
错误，调试，单元测试，文档测试  
```


## IO编程  
```
with open('/path/to/file', 'r') as f:  
    print(f.read())  
	f.write('Hello, world!')  
	f.close()  
StringIO()  
f = BytesIO()  
f.write('中文'.encode('utf-8'))  
print(f.getvalue())  

>>> import os  
>>> os.name # 操作系统类型  
>>> os.uname()  
>>> os.environ  
>>> os.environ.get('PATH')  
os.path.abspath('.')  
os.path.join('/Users/michael', 'testdir')  
os.mkdir('/Users/michael/testdir')  
os.rmdir('/Users/michael/testdir')  
os.path.split('/Users/michael/testdir/file.txt')  
os.path.splitext()  
os.rename('test.txt', 'test.py')  
os.remove('test.py')  
[x for x in os.listdir('.') if os.path.isdir(x)]  
[x for x in os.listdir('.') if os.path.isfile(x) and os.path.splitext(x)[1]=='.py']  
  
pickle.dumps(d)  
>>> f = open('dump.txt', 'wb')  
>>> pickle.dump(d, f)  
	d = pickle.load(f)  
>>> f.close()  
>
print(json.dumps(s, default=lambda obj: obj.__dict__))  
```
  
## 多进程  
```
pid = os.fork()  
if pid == 0:  
    print('I am child process (%s) and my parent is %s.' % (os.getpid(), os.getppid()))  
else:  
    print('I (%s) just created a child process (%s).' % (os.getpid(), pid))  
p = Process(target=run_proc, args=('test',))  
print('Child process will start.')  
p.start()  
p.join()  
  
p = Pool(4)  
for i in range(5):  
	p.apply_async(long_time_task, args=(i,))  
print('Waiting for all subprocesses done...')  
p.close()  
p.join()  
```  


## 多线程  
实例  
```  
import time, threading  

/# 新线程执行的代码:  
def loop():  
    print('thread %s is running...' % threading.current_thread().name)  
    n = 0  
    while n < 5:  
        n = n + 1  
        print('thread %s >>> %s' % (threading.current_thread().name, n))  
        time.sleep(1)  
    print('thread %s ended.' % threading.current_thread().name)  
  
print('thread %s is running...' % threading.current_thread().name)  
t = threading.Thread(target=loop, name='LoopThread')  
t.start()  
t.join()  
print('thread %s ended.' % threading.current_thread().name)  
```  
  
**Lock**  
多进程中，同一个变量，各自有一份拷贝存在于每个进程中，互不影响，  
多线程中，所有变量都由所有线程共享，所以，任何一个变量都可以被任何一个线程修改，因此，线程之间共享数据最大的危险在于多个线程同时改一个变量，把内容给改乱了。  
代码  
```  
balance = 0  
lock = threading.Lock()  
  
def run_thread(n):  
    for i in range(100000):  
        # 先要获取锁:  
        lock.acquire()  
        try:  
            # 放心地改吧:  
            change_it(n)  
        finally:  
            # 改完了一定要释放锁:  
            lock.release()  
```  
当多个线程同时执行lock.acquire()时，只有一个线程能成功地获取锁，然后继续执行代码，其他线程就继续等待直到获得锁为止。  
获得锁的线程用完后一定要释放锁，否则那些苦苦等待锁的线程将永远等待下去，成为死线程。所以我们用try...finally来确保锁一定会  
  
**GIL锁**  
Python的线程虽然是真正的线程，但解释器执行代码时，有一个GIL锁：Global Interpreter Lock，任何Python线程执行前，必须先获得GIL锁，然后，每执行100条字节码，解释器就自动释放GIL锁，让别的线程有机会执行。这个GIL全局锁实际上把所有线程的执行代码都给上了锁，所以，多线程在Python中只能交替执行，即使100个线程跑在100核CPU上，也只能用到1个核。

## 进程 vs. 线程  
多进程模式最大的优点就是稳定性高，因为一个子进程崩溃了，不会影响主进程和其他子进程。（当然主进程挂了所有进程就全挂了，但是Master进程只负责分配任务，挂掉的概率低）著名的Apache最早就是采用多进程模式。  
多进程模式的缺点是创建进程的代价大，在Unix/Linux系统下，用fork调用还行，在Windows下创建进程开销巨大。另外，操作系统能同时运行的进程数也是有限的，在内存和CPU的限制下，如果有几千个进程同时运行，操作系统连调度都会成问题。  
多线程模式通常比多进程快一点，但是也快不到哪去，而且，多线程模式致命的缺点就是任何一个线程挂掉都可能直接造成整个进程崩溃，因为所有线程共享进程的内存。在Windows上，如果一个线程执行的代码出了问题，你经常可以看到这样的提示：“该程序执行了非法操作，即将关闭”，其实往往是某个线程出了问题，但是操作系统会强制结束整个进程。  
  
**线程切换**  
多任务一旦多到一个限度，就会消耗掉系统所有的资源，结果效率急剧下降，所有任务都做不好。  
  
**计算密集型 vs. IO密集型**  
是否采用多任务的第二个考虑是任务的类型。我们可以把任务分为计算密集型和IO密集型。  
  
**异步IO**  
考虑到CPU和IO之间巨大的速度差异，一个任务在执行的过程中大部分时间都在等待IO操作，单进程单线程模型会导致别的任务无法并行执行，因此，我们才需要多进程模型或者多线程模型来支持多任务并发执行。  


