# python实战03为list实现find方法
 string类型的话可用find方法去查找字符串位置：

```
a_list.find('a')
```
如果找到则返回第一个匹配的位置，如果没找到则返回-1，而如果通过index方法去查找的话，没找到的话会报错。

如果我们希望在list中也使用find呢？

## 方法1,独立函数法
```
def list_find(item_list, find_item):
    if find_item in item_list:
        return item_list.index(find_item)
    return -1

item_list=[1,2,3]
print(list_find(item_list,1),list_find(item_list,4))
```
缺点：代码太多，麻烦


## 方法2,if三元表达式(本质同上)
```
item_list.index(find_item) if find_item in item_list else -1
```
优点：简单，明了

缺点:item_list在上面出现两次，想想一下，如果item_list是一个比较长表达式的结果(或者函数结果)，则会导致代码过长，且会执行2次　

## 方法3,next(利用迭代器遍历的第二个参数)
```
next((item for item in item_list if item==find_item ),-1)
```
缺点：如果对迭代器不熟悉，不大好理解

优点：扩展性好，if后面的条件可以不只是相等，可支持更为复杂的逻辑判断


## 方法4,list元素bool类型
```
''.join(map(str, map(int, item_list))).find(str(int(True)))
```
简单容易理解


## 参考
python中list的五种查找方法：https://blog.csdn.net/qq_31747765/article/details/80944227

python list 查找与过滤方法整合（查找第一个匹配项:next，重复时想要所有的索引:enumerate）：https://blog.csdn.net/qq997843911/article/details/93855706

