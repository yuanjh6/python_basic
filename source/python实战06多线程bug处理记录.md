# python实战06多线程bug处理记录
多线程bug处理记录

```
Thread(target=func02)
while True:
    dataA=DataA()
    dataA.data=[[[xx,yy]]]
    xxx,
    yyy,
    zzz,
    dataA.data=[[[xx,yy]],[[ff,zz]]]
    assert len(dataA.data)>1,error data#标记01
    self.queue.put(dataA)


func02():
    dataA=self.queue.get()
    assert len(dataA.data)>1,error data#标记02

```
问题:func02获取的data有时为一维的，而理论上应该是二维的

添加标记01,标记02后发现，标记01是ok的（说明存放时的确是二维的)，但标记02却偶然有报错(偶尔，而非绝对)

修改测试01,queue不支持多维数据存放?不大可能，经过独立测试，的确没有问题，可以存放

修改测试02,将多线程改为顺序执行，问题不存在=>说明是多线程导致的问题

当然最终也发现问题了，这也是**多线程最常出现的问题，就是“以为没共享的数据，其实共享了**”在博客[python进阶12并发之八多线程与数据同步](https://hexo.yuanjh.cn/hexo/347e85fb/)中也强调过，但不经意间依然会掉坑里。

其实对于此类问题，最好的调试方式是，出问题的是queue，那么就**打印所有操作quque的地方**，就可以发现queue其实会在2个线程中被操作，即使queue本身的存取是原子的，由于存放的数据是引用类型，所以queue临近的代码“上下文”，其实都属于“临界区”。需要“特殊防范”。

最简单的处理方式是self.queue.put(copy.deepcopy(dataA))，这样就彻底隔离了不同线程的数据，避免了一些隐患。


