# python入门03int的四舍五入
默认：向下取整。

正常情况下 int(5.5) 结果为5


```
5.4 “四舍五入”结果为：5，int(5.4+0.5) == 5  
5.6 “四舍五入”结果为：6，int(5.6+0.5) == 6  
``` 
另一种方式round函数  
round() 函数作用就是，返回浮点数x的四舍五入值。  

```
> round( x [, n]  )
```
参数x，n均为数值表达式，返回值为x的四舍五入值。n为保留的小数位数，不加n则只保留x四舍五入后的整数部分。

```
>>> round(2.3) 2

>>> round(2.45, 1) 2.5

```
推荐第二种，可读性好