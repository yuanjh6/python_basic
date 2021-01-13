# python实战01FFT快速傅里叶变换
本科小作业，快速傅里叶变换

python3.2

```
# Copyright (C) 2011-2012 YuanJh
# This program is free software: you can redistribute it and/or modify
# it under the terms of the GNU General Public License as published by
# the Free Software Foundation, either version 3 of the License, or
# (at your option) any later version.
#
# This program is distributed in the hope that it will be useful,
# but WITHOUT ANY WARRANTY; without even the implied warranty of
# MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE. See the
# GNU General Public License for more details.
#
# You should have received a copy of the GNU General Public License
# along with this program. If not, see <http://www.gnu.org/licenses/>.
 
"""yuanJh's fft and ifft module.
the detail about fft you can see "http://en.wikipedia.org/wiki/Fast_Fourier_transform"
Main functions:
  fft(a)
  ifft(a)
"""
from math import * 
from cmath import * 
import sys
from numpy import *
pi=3.1414926
 
#version infomation
__version__ = '1.0'
 
#################### function recursive FFT ####################
def RECURSIVE_FFT(a):
    "Use recursive method to realize FFT"
    n=len(a)
    if n==1:
        return a
    wn=complex(cos(-2*pi/n),sin(-2*pi/n))    #create the complex number wn=cos(-2pi/n)+sin(-2pi/n)i
    w=1
 
    a0=[]
    a1=[]
    for i in range(0,n-1,2):
        a0.append(a[i])        #pick up the even number
        a1.append(a[i+1])    #pick up the odd number 
 
    y0=RECURSIVE_FFT(a0)    #translate the even part
    y1=RECURSIVE_FFT(a1)    #translate the odd part
 
    y=[0 for i in range(n)]
    for k in range(0,int(n/2)):    #combine the even part and the odd part
        y[k]=y0[k]+w*y1[k]
        y[k+int(n/2)]=y0[k]-w*y1[k]
        w*=wn
    return y
#################### end function recursive FFT ####################
 
#################### function recursive IFFT ####################    
def RECURSIVE_IFFT(a):
    "Use recursive method to realize IFFT"
    n=len(a)
    if n==1:
        return a
    wn=complex(cos(2*pi/n),sin(2*pi/n))    #create the complex number wn=cos(2pi/n)+sin(2pi/n)i
    w=1
 
    a0=[]
    a1=[]
    for i in range(0,n-1,2):
        a0.append(a[i])        #pick up the even number
        a1.append(a[i+1])    #pick up the odd number
 
    y0=RECURSIVE_IFFT(a0)
    y1=RECURSIVE_IFFT(a1)
 
    y=[0 for i in range(n)]
    for k in range(0,int(n/2)):    #combine the even part and the odd part
        y[k]=y0[k]+w*y1[k]
        y[k+int(n/2)]=y0[k]-w*y1[k]
        w*=wn
    return y
#################### end function recursive IFFT ####################
 
#################### got data and verify the program ####################
a=input("please input the number (use ',' split):")
a=a.split(',')
n=len(a)
#tempn=ceil(log2(n))
#tempn=tempn**2
 
for i in range(0,n):
    a[i]=int(a[i])
 
#################### verify the FFT ####################
print ("the results of my program fft")
b=RECURSIVE_FFT(a)
for i in range(0,n):
    print ("%4d: %4.4f+%4.4fi"%(a[i],b[i].real,b[i].imag))
 
print ("the results of the numpy program fft")    #Use the function in the package numpy
b=fft.fft(a)
for i in range(0,n):
    print ("%4d: %4.4f+%4.4fi"%(a[i],b[i].real,b[i].imag))
 
#################### verify the IFFT ####################
#test the my ifft program is right or not by compare with the ifft function in numpy package
print ("the results of my program ifft")
lenb=len(b)
a=RECURSIVE_IFFT(b)
for i in range(0,n):
    print("%4.4f+%4.4fi: %4.4f+%4.4fi"%(b[i].real,b[i].imag,a[i].real/lenb,a[i].imag/lenb))
 
print ("the results of the numpy program ifft")
a=fft.ifft(b)
for i in range(0,n):
    print("%4.4f+%4.4fi: %4.4f+%4.4fi"%(b[i].real,b[i].imag,a[i].real,a[i].imag))
 
文件名：fft.py
实现语言：PYTHON
1，系统概述
利用递归的方法实现快速傅里叶变化（FFT）和快速傅里叶逆变换（IFFT）。
2，模块层次
主要包含三部分：
    1，递归实现快速傅里叶变换（FFT）。
    2，快速傅里叶逆变换（IFFT）。
    3，数据录入以及结果正确性验证。
3，函数声明表
def RECURSIVE_FFT(a):
    功能说明：递归实现快速傅里叶变换。
    参数说明：
        a:需要进行快速傅里叶变换的数据，以列表（类似C,JAVA中数组）形式存储。
def RECURSIVE_IFFT(a):
    功能说明：递归实现快速傅里叶逆变换，
    参数说明：
        a:需要进行快速傅里叶逆变换的数据，以列表（类似C,JAVA中数组）形式存储。
 
filename:fft.py
language:python
1,System overview 
Using recursive method to realize Fast Fourier Transform (FFT) and inverse Fast Fourier Transform (IFFT).
2,Module and level
Mainly includes three parts:
    1, The recursive Fast Fourier Transform(FFT).
    2, Inverse Fast Fourier Transform(IFFT).
    3, Got data and verify the results.
3,Function declaration table
def RECURSIVE_FFT (a):
    Function introduction:recursive realize Fast Fourier Transform.
    Parameters:
        a: the data need for Fast Fourier Transform store as a list (similar to C, JAVA the array).
def RECURSIVE_IFFT (a):
    Function introduction: recursive inverse Fast Fourier Transform.
    Parameters:
        a: the data need for inverse Fast Fourier Transform store as a list.

```