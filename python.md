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
