### 1.函数传参
python的变量是一个指向一块内存区域的引用，这块内存区域保存的了变量的值，类型，大小等信息。
在函数传参时，传的是引用，所以当传进函数的参数在函数中重新赋值时，这个参数就指向了另一块内存区域，原来的变量不变。比如：
```
a = 1

def foo(b):
    print("刚传入的变量指向的地址:",id(b))       #id()方法可以用来查看变量指向的内存地址
    b = 2
    print("传入变量的值改变后变量指向的地址:",id(b))

foo(a)
print("原变量的地址:",id(a))
```

```
(test_pipenv-hhC387KC) [dy@dy-pc demo]$ python demo.py
刚传入的变量指向的地址: 139628035668608
传入变量的值改变后变量指向的地址: 139628035668640
原变量的地址: 139628035668608
```

### 2.自省
运行时能够获得对象的类型：
type():获取变量类型
isinstance()：判断两个变量是否同一类型
issubclass()：判断父子关系
hasattr()：判断实例是否有参数
getattr()：获取实例中的参数
dir()：列出实例的方法及属性
```
l = []
i = 1
t = ()
d = {}

print(type(l), type(i), type(t), type(d))
print(isinstance(l, object))            #l是一个list，也是object的子类，和object同类
print(issubclass(dict, object))         #dict是object的子类
print(dir(l))                           #列出l的方法及属性
print(hasattr(l, "__dir__"))
```

### 3.类变量和实例变量
可以用`classname.类变量`的方法改变类变量，这种方法会使每个实例中的类变量都改变。
```
class Test():
    class_num = 0       #类变量

    def __init__(self):
        self.instance_num = 0   #实例变量

print("Test：", "类变量", Test.class_num)
t = Test()
t1 = Test()
print("实例t：", "类变量", t.class_num, "实例变量", t.instance_num)
print("实例t1：", "类变量", t1.class_num, "实例变量", t1.instance_num)
Test.class_num +=1             #在实例t中改变类变量
print("改变类变量后：")
print("实例t：", "类变量", t.class_num, "实例变量", t.instance_num)
print("实例t1：", "类变量", t1.class_num, "实例变量", t1.instance_num)
print("Test：", "类变量", Test.class_num)
```
### 4.静态方法，类方法
self是实例的绑定，cls是类的绑定。
实例方法中传入的第一个参数必须是`self`，调用时`Instance.func(args)`这样调用，实际上是`func(Instance, args)`。
类方法中传入的地一个参数必须是`cls`（名字可以自定义），cls是类的绑定，类方法中地一个参数必须传入类本身，调用时`Class.func(args)`或`Instance.func(args)`，实际上是`func(Class, args)`。
静态方法就很随意，没有传参限制，调用时`Class.func(args)`或`Instance.func(args)`，实际上是`func(args)`。

```
class Test(object):
    num = 0

    @staticmethod
    def static_func():
        Test.num += 1
        print("Test.num:", Test.num)
    
    @classmethod
    def class_func(cls):
        cls.num += 1
        print("cls.num:", cls.num)

t = Test()
print("t.num:", t.num, "Test.num:", Test.num)
t.static_func()
t.class_func()
```

```
(test_pipenv-hhC387KC) [dy@dy-pc demo]$ python demo.py
t.num: 0 Test.num: 0
Test.num: 1
cls.num: 2
```
### 5.列表生成/推导式和字典推导/生成式
列表推导
```
>>> [x*x for x in range(1, 11)]
[1, 4, 9, 16, 25, 36, 49, 64, 81, 100]
```
字典推导
```
>>> d1 = {'a':1, 'b':2, 'c':3}
>>> {v:k for k,v in d1.items()}
{1: 'a', 2: 'b', 3: 'c'}
```
### 6.元类mateclass
类是元类的一个实例，可以使用`type()`方法查看：

类的类型是type
```
>>> type(object)
<class 'type'>
```
实例的类型是类
```
>>> o = object()
>>> type(o)
<class 'object'>
```
`type()`方法不仅可以获取对象的类型，也可以创建一个类，只需这样`type(类名,(父类集合),{方法集合})`。
```
>>> def func(self):
...     print("hello")
... 
>>> Hello = type('Hello', (object,), {'hello':func})
>>> h = Hello()
>>> h.hello()
hello
```
所有的类都从`object`派生，所有的元类从`type`派生，创建一个元类：
```
class ListMateClass(type):
    
    # cls：类的对象
    # name：类名
    # bases：父类集合
    # attrs：方法集合
    def __new__(cls, name, bases, attrs):
        print("类名：", name)
        print("父类：", bases)
        print("方法：", attrs)
        return type.__new__(cls, name, bases, attrs)

class MyList(list, metaclass=ListMateClass):
    pass

L = MyList()
```
```
(test_pipenv-hhC387KC) [dy@dy-pc al]$ python ListMateClass.py
类名： MyList
父类： (<class 'list'>,)
方法： {'__module__': '__main__', '__qualname__': 'MyList'}
```

### 7.ORM（Object Relational Mapping，对象关系映射）
数据库中会用到
### 8.%与format
```
>>> a = 1
>>> t = (1,2,3)
>>> "%s" %a         # %字符串格式化数字
'1'
>>> "%s" %t         # %字符串格式化元组，由于后面的%后面的类型原本就应该是一个元组，所以引号内应该要有3个%s
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
TypeError: not all arguments converted during string formatting
>>> "%s %s %s" %t   # 引号内应该要有3个%s，分别对应元组t的三个元素
'1 2 3'
>>> "%s" %(t,)      # 传元组参数要这样
'(1, 2, 3)'
>>> "{arg}".format(arg=a)
'1'
>>> "{arg}".format(arg=t)   # 用format就没有前面%的问题
'(1, 2, 3)'
```
### 9.迭代器与生成器
#### 迭代器
判断一个对象是否可迭代
```
>>> from collections import Iterable
>>> isinstance([], Iterable)
True
```
然而可迭代的对象不一定是迭代器，可以被next()函数调用并不断返回下一个值的对象称为迭代器：Iterator。
```
>>> from collections import Iterator
>>> isinstance((x for x in range(10)), Iterator)
True
```
#### 生成器其实就是一个推导式，可以根据之前的数据推导出后面的数据，eg：
```
>>> def gen():
...     i = 1
...     while i < 10:
...             i +=1
...             yield i
... 
>>> gen()
<generator object gen at 0x7f6206b0b888>
>>> g = gen()
>>> next(g)
2
>>> next(g)
3
>>> next(g)
4
```








