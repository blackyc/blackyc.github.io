---
layout: post
title:  "python notes"
date:   2021-05-24 18:10:00 +0800
tags: python base
color: rgb(65,242,53)
cover: '../assets/test.png'
subtitle: 'python language basics by yako33!'
---

[TOC]

##  python基础

###  pycharm设置

file setting中 editor设置 font 24 code style中设置tab size 4 indent缩进4

### 文件头

```python
#!/usr/bin/python
```

linux下系统的用法，告诉操作系统，使用/usr/bin/python下的

```python
#!/usr/bin/env python
```

这种用法是为了防止操作系统用户没有将 python 装在默认的 /usr/bin 路径里。当系统看到这一行的时候，首先会到 env 设置里查找 python 的安装路径，再调用对应路径下的解释器程序完成操作。

如果没有设置env 这里的python路径就等于是写死了路径

windows下没有，忽略

###  print

在python2.x中使用python3.x的print函数导入**__future__**,包的作用是禁用python2的语句，采用python3的print

```python
from __future__ import print_function
```

其他同理，future具有很多其他的包division/absolute_import

###  python保留字

| and      | exec    | not    |
| -------- | ------- | ------ |
| assert   | finally | or     |
| break    | for     | pass   |
| class    | from    | print  |
| continue | global  | raise  |
| def      | if      | return |
| del      | import  | try    |
| elif     | in      | while  |
| else     | is      | with   |
| except   | lambda  | yield  |

多行语句

使用  \   来多行

```
total = item_one + \
        item_two + \
        item_three
```

###  单行注释多行注释

#单行注释

```
'''
多行注释
'''
"""
多行注释
"""
```

###  变量赋值

python支持int long float complex

####  变量赋值切片

```python
print str #完整
print str[0] #输出第一个字符串
print str[2:5] #输出字符串第三个到第六个
print str[2:] #输出第三个开始
print str*2 #输出字符串两次
print str + "TEST" #输出连接字符串
```

####  元组

tuple,元组无法更新，但是列表是允许更新的

```
print tuple = ('run','786',678,'jokne',70.2)
```

元组中只包含一个元素时，需要在元素后面添加逗号来消除歧义

tuple=(50,)

元组可以连接,和复制

tup3=tup1+tup2

tup3=tup1 * 3               tup3=(1,2,3,1,2,3,1,2,3)

元组的方法（元组没有列表中的增、删、改的操作，只有查的操作

tuple.index(obj)  /  tuple.count(obj)

可以使用List转化tuple

tuple[0]即可访问

del tuple即可删除

tuple支持索引，L[-2]截断L[1:]

内置函数cmp(tup1,tup2)  计算元素个数len(tuple),max()计算最大值，min()最小值,tuple(seq)转换列表为元组

####  字典

dict  = {}键值对，成对存在

```
dict['one'] = "This is one"
print(dict['one'])  #输出键为'one' 的值
console This is one
```

tinydict = {'name': 'runoob','code':6734, 'dept': 'sales'}

print tinydict.keys()  #输出所有键

print tinydict.values()    #输出所有值

dict.update(dict2)  #更新字典

dict.items()  以列表返回可遍历的（键，值）元组数组

### 运算符

次方 a**b

向下取整 9//2 = 4

### 条件

if elif eles

###  循环语句

while    for    while中使用for   break continue 跳出该循环，执行下一次循环, pass语句



###  更新列表

list.append('google')  #添加元素

###  日期

import time

time.time()  #值为时间戳

time.localtime(time.time())

格式化成2016-03-20 11:45:39形式 

print time.strftime("%Y-%m-%d %H:%M:%S", time.localtime())  

格式化成Sat Mar 28 22:24:24 2016形式 

print time.strftime("%a %b %d %H:%M:%S %Y", time.localtime())     

将格式字符串转换为时间戳 a = "Sat Mar 28 22:24:24 2016" 

print time.mktime(time.strptime(a,"%a %b %d %H:%M:%S %Y"))

```
time.sleep()推迟线程
```



###  函数

def function( parameters ):

​	return []

在 python 中，strings, tuples, 和 numbers 是不可更改的对象，而 list,dict 等则是可以修改的对象。

只能说在传递中，传不可变对象和可变对象

```python
def change(a):
    a=10
b=2
change(b)
print(b)
2
```

局部变量a不能赋值b，传不可变对象的时候，a b指向了同一个int对象

传可变对象list，采用自带更新方法append即可

```python
def change(mylist):
    mylist.append(1,2,3)
    return
mylist=[4,5,6]
change(mylist)
```

参数

```
def printme(str):
	return
```

调用函数，需要填入参数

默认参数

```
def printme(age=50):
	return
```

如果没有传递，则为默认参数

lambda是一个表达式

sum = lambda arg1,arg2:arg1+arg2

### 语法糖

就相当于汉语里的成语,用更简练的言语表达较复杂的含义。在得到广泛接受的情况之下，可以提升交流的效率,语法糖就是为了避免coder出现错误并提高效率的语法层面的一种优雅的解决方案。

```js
for (var i = 0; i < 5; i++){
    ... 
}
```

就是while的语法糖 

```js
var i=0;
while (i < 5){
    ...
    i++;
}
```

y+=x是y=y+x的语法糖

### 继承
Car类是父类，Aodi是子类继承于Car,继承也可以设置其他方法,代码只需做少部分改进,类遵循驼峰命名，方法小写，私有方法加下划线
```python
#!/usr/bin/python
# -*- coding: utf-8 -*-
import sys

class Car(object):
    def run(self):
        print("我是车")

class Aodi(Car):
    def run(self):
        print("奥迪车")

class Bmw(Car):
    def run(self):
        print("宝马车")

   def price(self):
       print("50万")

c = Car()
d = Aodi()
f = Bmw()
c.run()
d.run()
f.run()
f.price()
```

派生类，调用初始化Init方法，

```
#!/usr/bin/python
# -*- coding: utf-8 -*-
import sys

class Car(object):
    def __init__(self,color="绿色"):
        Car.color = color
        
    def run(self):
        print(f"我是{Car.color}的车")

class Aodi(Car):
    def __init__(self):
        print("我是奥迪车")
    
c = Car()
d = Aodi()

c.run()
```

#### 超类
super() 是用来解决多重继承问题的，直接用类名调用父类方法在使用单继承的时候没问题，但是如果使用多继承，会涉及到查找顺序（MRO）、重复调用（钻石继承）等种种问题。
```
class Aplus(object):
    def test(self):
        print("A")

class A(Aplus):
    def test(self):
        super().test()

t = A()
t.test()
```

### 装饰器

装饰器

我们可以使用@property装饰器来创建**只读属性**，@property装饰器会将**方法**转换为相同名称的**只读属性**,可以与所定义的属性配合使用，这样可以防止属性被修改。

由于python进行属性的定义时，没办法设置私有属性，因此要通过@property的方法来进行设置。这样可以隐藏属性名，让用户进行使用的时候无法随意修改。

```python
#设置class奥特曼
class AoteMan:
    #初始化属性
    def __init__(self,name):
        self._name = name

    #设置get方法获取属性_name
    def get_name(self):
        return self._name
    
	#设置set方法修改_name属性
    def set_name(self,name):
        self._name = name

p = AoteMan('迪迦')
#new对象，传入值，迪迦，走到init初始化
print(p.get_name())
#用get方法获取值
p.set_name('泰罗')
#set方法更新值
print(p.get_name())
```

用装饰器改变方法为属性

```python
class AoteMan:
    def __init__(self,name):
        self._name = name
	## 利用property装饰器将获取name方法转换为获取对象的属性
    @property
    def get_name(self):
        return self._name
    ## 利用property装饰器将设置name方法转换为获取对象的属性
    @get_name.setter
    def set_name(self,name):
        self._name = name
        
p = AoteMan('迪迦')
print(p.get_name)   ## 原 p.get_name()  , 现 p.get_name，调用属性
p.set_name = '泰罗' ## 原 p.set_name('泰罗')  ,现 p.set_name = '泰罗'
print(p.get_name)
```

装饰器setter

```python
class Test():
    @property
    def name(self):
        print('is running here 1')
        return self.title
    @name.setter
    def name(self,value):
        print('is running here 2')
        self.title = value
    
t = Test()
t.name = '123'
print(t.name)


is running here 2
is running here 1
123
```


### 开包闭包

if else写法

```
if (strategy.equals("fast")) {
  // 快速执行
} else if (strategy.equals("normal")) {
  // 正常执行
} else if (strategy.equals("smooth")) {
  // 平滑执行
} else if (strategy.equals("slow")) {
  // 慢慢执行
}
```

多态替换，python中没有interface类，直接使用抽象类和抽象方法重写即可,以**java**为例

```
interface Strategy {
  void run() throws Exception;
}

class FastStrategy implements Strategy {
    @Override
    void run() throws Exception {
        // 快速执行逻辑
    }
}

class NormalStrategy implements Strategy {
    @Override
    void run() throws Exception {
        // 正常执行逻辑
    }
}

class SmoothStrategy implements Strategy {
    @Override
    void run() throws Exception {
        // 平滑执行逻辑
    }
}

class SlowStrategy implements Strategy {
    @Override
    void run() throws Exception {
        // 慢速执行逻辑
    }
}
```

#### 数组
```
int getDays(int month){
    if (month == 1)  return 31;
    if (month == 2)  return 29;
    if (month == 3)  return 31;
    if (month == 4)  return 30;
    if (month == 5)  return 31;
    if (month == 6)  return 30;
    if (month == 7)  return 31;
    if (month == 8)  return 31;
    if (month == 9)  return 30;
    if (month == 10)  return 31;
    if (month == 11)  return 30;
    if (month == 12)  return 31;
}
```

```
int monthDays[12] = {31, 29, 31, 30, 31, 30, 31, 31, 30, 31, 30, 31};
int getDays(int month){
    return monthDays[--month];
}
```


#### 上下文管理器

```python
with open('test.txt',mode='rb') as f:
	data = f.read()
	print(data)
```
fopen中，如果不是使用with时，则需要再用完文件后进行关闭，以恢复资源，而如果使用上下文，则在with后运行完毕的代码将自动回收资源，即使异常也会关闭程序，但它，没有try的异常捕获

其原理在于，包含方法`__enter__()` 和` __exit__()`，支持该协议对象要实现这两个方法。也就是说，如果是自定义类则需要包含`__init__`以及上述2种，假设其中具有run方法

```python
with MyClass('test') as mc:
	mc.run()
```

