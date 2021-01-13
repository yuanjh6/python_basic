# python实战02异常报错
## 百度自然语言处理报错：UnicodeEncodeError: 'gbk' codec can't encode character '\U0001f602' in posit
原因：ubuntu机器编码utf8，接口尝试用gbk解析，在对str转换格式时报错

解决：比如title变量.str(title.encode("GBK",'ignore'))


## 解决方案ObjectOfTypeXXisNotJSONSerializable
对象的部分字段不支持序列化.

01,

```
print(json.dumps(obj.__dict__, indent=4, sort_keys=True, default=str))
参考：https://stackoverflow.com/questions/57671281/python-json-dumps-unable-to-ignore-non-serializable-objects#comment101791277_57671370
```
02,

```
def set_default(obj):
    if isinstance(obj, set):
        return list(obj)
    raise TypeError

result = json.dumps(yourdata, default=set_default)

参考：http://www.ojit.com/article/95292
```

03,
```
result=eval(repr(results))  
json = simplejson.dumps({'results':result,'retrieveStyle': 'distRetrieve', 'status': 'ok'})  
参考；https://blog.csdn.net/qq_20373723/article/details/68961099
```

04,

```
import json

class Object:
    def toJSON(self):
        return json.dumps(self, default=lambda o: o.__dict__,
            sort_keys=True, indent=4)
参考：https://www.codenong.com/3768895/
```

05,

```
import json

class FileItem:
    def __init__(self, fname):
        self.fname = fname

    def __repr__(self):
        return json.dumps(self.__dict__)
参考：https://www.codenong.com/3768895/
```

最终推荐方案:
```
json.dumps([x.__dict__ for x in all_results], default=str)
```
## 解决方案UnicodeEncodeError
又遇到报错：

UnicodeEncodeError: 'ascii' codec can't encode character u'\uff08' in position 13: ordinal not in range(128)

这个问题遇到多次了了，但都是通过第一个方案解决了，但这次貌似不行了。最终采用了方案三，顺便整理下网上其他方案

第一种方案（90%情况下，大部分帖子都是这个）

一般报错到代码都是自己写到代码，代码上添加

```
import sys
reload(sys)
sys.setdefaultencoding('utf-8')
```

第二种方案，引用到包出现错误（未解决我的问题，但有人提到过这种处理方案）

在python的lib\site-packages文件夹下新建一个sitecustomize.py

cat sitecustomize.py #添加如下内容，设置编码为utf8

```
#encoding=utf8  
import sys
reload(sys) 
sys.setdefaultencoding('utf8')
```
参考：https://www.cnblogs.com/kevingrace/p/5893121.html


第三种方案 
进入python终端，执行如下命令

import sys, codecs, locale; print str(sys.stdout.encoding);

是否时utf8（ubunut系统）

如果不是，比如我的是这个

'ANSI_X3.4-1968'

则修改环境变量PYTHONIOENCODING为utf8

执行：export PYTHONIOENCODING=utf-8


## 解决方案ImportErrorlibta_lib
原始问题：

```
>>> import talib
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
  File "/root/anaconda3/envs/vnpy27/lib/python2.7/site-packages/talib/__init__.py", line 4, in <module>
    from . import common
ImportError: libta_lib.so.0: cannot open shared object file: No such file or directory

```
附录：官方的安装方法
```
pip install TA-Lib
参考：https://github.com/mrjbq7/ta-lib
```

### 方案01,export
参考：https://github.com/mrjbq7/ta-lib/issues/6

```
export LD_LIBRARY_PATH=/usr/local/lib:$LD_LIBRARY_PATH  

等价做法：
$ LD_LIBRARY_PATH="/usr/local/lib:$LD_LIBRARY_PATH" python
>>> import talib
```
### 方案02,源代码安装
参考：https://stackoverflow.com/questions/45406361/importerror-libta-lib-so-0-cannot-open-shared-object-file-no-such-file-or-dir

https://ideaorchard.wordpress.com/2015/01/16/installing-ta-lib-ubuntu/


```
I had the same issue. See below for what I did to fix it.

installing

wget http://prdownloads.sourceforge.net/ta-lib/ta-lib-0.4.0-src.tar.gz
tar -xzf ta-lib-0.4.0-src.tar.gz
cd ta-lib/
./configure --prefix=/usr
make
Sudo make install
pip install numpy
If you don't have it installed

pip install TA-Lib 
if you do have it installed

pip install --upgrade --force-reinstall TA-Lib
```
### 方案3，conda安装（这个一般比较好使）

```
sudo chmod -R 777 anaconda3
conda install -chttps://conda.anaconda.org/quantopian ta-lib
```

### 方案4，conda 安装talib，冲突的numpy自动卸载
之后pip卸载numpy，再pip自动安装numpy，安装时指定版本==1.12.0


## python绘制k线图(蜡烛图)报错 No module named 'matplotlib.finance
使用python绘制蜡烛图报错：No module named 'matplotlib.finance

部分版本移除了finance模块，需要独立安装

安装命令：pip install git+https://github.com/matplotlib/mpl_finance.git

其他参考：https://blog.csdn.net/u014281392/article/details/73611624

https://www.jb51.net/article/140799.htm



## 坑map函数不执行
常用series.map()函数，回头使用map(lamba x:func(x),listxx)，容易忘记map是生成式，如果不调用next不会触发实际运行，所以需要联用list(map(xx,yy))才有意义。


## 坑文件读写方式和乱码问题

java中文件读写打开为“rw”，但python中“rw”并非有效配置，会报错

```
ValueError: must have exactly one of create/read/write/append mode
```
使用'r+'代替'rw'读写模式.

其次读写同一文件时，如果写入从文件开头覆盖式写入则需要按照如下方式

```
content = ''.join(f.readlines())
content.replace(' ','')
f.seek(0)
f.truncate()
f.seek(0)
f.write(content)
```
