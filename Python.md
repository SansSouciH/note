# Python

## 数据类型

基本数据类型
> Number、String、List、Tuple、Set、Dictionary
>
> 其中：
>
> 不可变数据：Number、String、Tuple
>
> 可变数据：List、Set、Dictionary

type()：返回变量的类型、不会认为子类是一种父类类型
isinstance()：会认为子类是一种父类类型

### Number
> int、float、bool、complex（复数）

### String
> 由''或者""括起来

```python
str='hello12'
print(str)                #打印
print(str[0:-3])          #第一个字符到倒数第二个字符
print(str[0])             #第一个字符
print(str[1:3])           #第二个字符到第三个字符
print(str[2:])            #第三个字符开始打印完
print(str * 2)            #打印两次str
```

### bool
> bool是int的子类、True等价于1、False等价于0

```python
print(bool(0))
print(bool(''))
print(bool([]))
print(True or False)
print(True and False)
print(not True)
```

### List
> []中可包含：Number、String、List、Tuple、Set、Dictionary类型。用','隔开  
> 列表截取语法格式：变量[头下标:尾下标]

```python
list = ['hello', 123, 2.12, 'run']
list2 = [99, 'abc', [1,2,3], (4,5,6), {7,8,9}, {'name':'zhangsan', 'age':99}]  
#number\\string\\list\\tuple\\set\\dictionay
tinylist = [123, 'hello']
print(list)
print(list[0])                 #打印第一个元素
print(list[1:2])               #打印表中第二个元素(和String的截取打印一样)
print(tinylist * 3)            #打印表两次
print(list + tinylist)         #两个表合起来打印
print(list2)
```

**常用函数**  
* append(x)  
* insert(i, x)  
* pop([i])：移除并返回指定位置i的元素，默认为末尾元素  
* remove(x)：移除值为x的元素  
* clear()：移除列表中的所有元素  
* index(x)：返回列表中第一个值为x的元素的索引  
* count(x)：返回列表中x值出现的次数  
* sort()：对列表排序  
* reverse()：反转列表  
* copy()：复制列表  
* len()：返回列表长度  
* max()\\min()：返回列表中最大最小值  

### Tuple
> ()中可包含Number、String、Tuple、List、Set、Dictionary。用','隔开，和List的区别在于不可修改数据内容

```python
tuple1 = ('hello', 123, 2.12, 'run')
tinytuple = ('hihi', 123)
print(tuple1)
print(tuple1[0])                #打印第一个元组元素
print(tuple1[1:2])              #打印第二个元组元素
print(tinytuple * 3)           #打印两次元组
print(tinytuple + tuple1)       #两个元组合起来打印
```

**常用函数**  
* index(x)：返回元组中第一个值为x的索引  
* count(x)：返回元素x在元组中出现的次数  
* len()：返回元组长度  
* max()\\min()：元组中最大值最小值  

### Set
> {}中可包括Number、String、Set、Tuple。  
> 特点：  
> 1. 无序  
> 2. 不重复  
> 3. 数据类型唯一  
> 4. 可算差集、交集、并集  

**常用函数**  
* add(x)  
* remove(x)：移除指定元素x，不存在x时报错  
* discard(x)：移除指定元素x，不存在x时不报错  
* pop()：移除并返回集合中最后一个元素  
* union(other_set)：返回两集合的并集，或使用逻辑运算符'|'  
* intersection(other_set)：返回两集合交集，或使用逻辑运算符'&'  
* difference(other_set)：返回存在第一个集合但并不在第二个集合的元素  
* symmetric_difference(other_set)：或使用逻辑运算符'^'。返回两个集合中仅存在于一个集合中的元素的集合。  

```python
set1 = {'a','b','c','d','e'}
print(set1)
set2 = {}
print(set2)
a = {'defghijk'}
b = {'ghijk'}
print(b - a)        #b和a的差集
print(a | b)        #a和b的并集
print(a & b)        #a和b的交集。没有就set()表示空
print(a ^ b)        #a和b不同时存在的元素
```

### Dictionary
> 格式：{key:value}。键值必须是不可变类型（Tuple、Number、String），值可以是任意元素

**常用函数**  
* get(key)：不存在则返回None  
* pop(key)：移除并返回键值为key的元素  
* popitem()：移除并返回字典中的一对键值（末尾对）  
* update(dict2)：将字典dict2的键值对更新到字典中  
* setdefault(key,defualt=None)：返回键key对应的值，如果key不存在则加入key并设置None为key的默认值  
* `keys()`：返回所有键的视图  
* `values()`：返回所有值的视图  
* `items()`：返回所有键值对的视图  
* `clear()`：移除字典中所有元素  
* `copy()`：复制字典  
* `len()`：返回键值对数量  

```python
# 列表、字典、集合不可作为Dictionary类型的键值
my_dict = {
    1:"hello",
    "name":"lisi",
    (1,2):[1,2,3],
    True:{1,2,3,4},
    3.14:{"age",13},
    None:"unknown"
}
print(my_dict)
```

### bytes
> 不可变的二进制序列，bytes元素为0-255的整数值，默认使用UTF-8编码格式  
> 常用于处理二进制文件：图像、音频、视频、网络编程  

```python
x = bytes("hello bytes", encoding="utf-8")
y = b"hello"
print(type(x))
print(type(y))

a = b'p'
print(ord(a))   #ord()函数将字符转换为对应整数值
```

### 类型转换
* 隐式转换：自动完成
* 显式转换：使用类型函数完成

**在不同数据类型计算中，较低的数据类型会隐式转换为较高的数据类型**  
1. 整数 + 浮点数 = 浮点数
2. Number类型不能和String类型进行运算符计算，但可以将String用类型函数转换为Number后进行计算

类型转换的函数有：
int()、float()、str()、list()、tuple()、set()、dict()、ord()等

## 基础语法

### 运算符
计算运算符、比较运算符、赋值运算符、位运算符、成员运算符、身份运算符
| 运算符                          | 描述                                       |
| ------------------------------- | ------------------------------------------ |
| +、-、*、/、%                   | 加、减、乘、除、取余                       |
| **、//                          | 取幂（2**3结果为8）、整除                  |
| ==、!=、>、<、>=、<=            | 等、不等、大于小于、大于等于、小于等于     |
| =、+=、-=、*=、/=、%=、**=、//= | 赋值、加等、减等、除等、余等、幂等、整除等 |
| &、\|、^、~、<<、>>             | 位运算符                                   |
| and、or、not                    | 逻辑运算符                                 |
| in、not in                      | 成员运算符                                 |
| is、is not                      | 身份运算符                                 |

### 条件判断
> if、elif、else。在python3.10后加入match...case

```python
def http_error(status):
    match status:
        case 400:
            return "bad request"
        case 404:
            return "not found"
        case _:
            return "something's wrong"

mystatus=400
print(http_error(mystatus))
```

### 循环
> while、while...else；for、for...else；range()函数、

```python
n=0
while(n<5):
    n+=1
print(n)
```

```python
list=[7,6,5,4,34,23,1,12,3]
for i in list:
    print(i, end=',')
print()
print("----------")
for num in range(2,5):
    print(num)
```

### 推导式
> 集合变为列表、字典、集合、元组  
> * 列表推导式：[变量 for 变量 in collection if condition]  
> * 字典推导式：{key:key_value for value in collection if condition}  
> * 集合推导式：{变量 for 变量 in collection if 条件}  
> * 元组推导式：(变量 for 变量 in collection if condition)    #注意：元组推导式返回的是生成器对象（只消耗一次），用tuple(a)函数将生成器对象转换为元组  

```python
# 列表推导式
multiples = [i for i in range(30) if i % 3 == 0]
print(multiples)
#----------------------------
names = ['Bob','Tom','alice','Jerry','Wendy','Smith']
new_names = [name.upper()for name in names if len(name)>3]
print(new_names)
```

```python
# 字典推导式
dic = {a:a**2 for a in range(1,4)}
print(dic)
```

```python
# 集合推导式
setnew = {a**2 for a in range(2,6) if a != 3}
print(setnew)
```

```python
# 元组推导式
my_tuple = (a**2 for a in range(1,6) if a != 3)
print("生成器对象",end=':')
print(my_tuple)
t = tuple(my_tuple)
print(t)
```

### 迭代器与生成器
> 生成器相当于迭代器：1.惰性求值（适合大数据和无限序列）；2.节省内存（不会一次性执行完所有代码）；  
> * iter()：获取一个对象的迭代器，只要实现了`__iter__()`和`__next__()`方法的对象都可用于迭代  
> * next()：从迭代器中获取下一个元素，没有下一个元素则会抛出StopIteration的异常  
> * yield：关键字，当函数包含这个关键字时，函数就成为了生成器函数，返回的对象是一个生成器对象  
>   * next()方法调用生成器执行到yiled关键字并返回生成器对象，下次调用next时继续执行yiled后面的代码  

```python
# 基本数据类型的迭代
list_obj = [1, 2, 3]
tuple_obj = (4, 5, 6)
dict_obj = {'a': 1, 'b': 2, 'c': 3}
set_obj = {7, 8, 9}
str_obj = "abcd"

# list_iter = iter(list_obj)
list_iter = list_obj.__iter__()   #list_obj.__iter__()和iter(list_obj)是等价的
tuple_iter = iter(tuple_obj)
dict_iter = iter(dict_obj)   # 迭代字典的键
set_iter = iter(set_obj)
str_iter = iter(str_obj)

print(list_iter.__next__())   #list_iter.__next__() 和 next(list_iter)的作用一样
print(next(tuple_iter))
print(next(dict_iter))
print(next(set_iter))
print(next(str_iter))
```

```python
# 自定义类的迭代
class myIter:
    def __iter__(self):
        self.t = 1
        return self      # 返回该类的实例。因为该类实现了iter和next方法所以该对象也是一个迭代器对象，所以也可以说是返回了一个迭代器
    
    def __next__(self):
        self.t +=1
        print(self.t)

m = myIter()
mIter = iter(m)
next(mIter)
next(mIter)
```

```python
# 生成器yield
def countdown(n):
    while n>0:
        yield n             #next执行到yiled会暂停返回n，下次调用next时会接着执行下面的代码
        n -= 1
generator = countdown(5)
# generator = countdown(5).__iter__()  #这句代码和上一句的作用一模一样只是显示调用__iter__()的区别
print(next(generator))      #遇到yield就暂停并返回yield指定的对象
print(next(generator))
print(next(generator))

for item in generator:
    print(item)
```

### 函数
> 参数传递：python中类型属于对象，对象有不同类型的区别，而变量是没有类型的指针引用。  
>
> 不可变类型：Numbers，Tuple，String
> 可变类型；List、Set、Dictionary
>
> python 中一切都是对象，严格意义我们不能说值传递还是引用传递，我们应该说传不可变对象和传可变对象。

**一些常用内置函数**  
* id(对象名)：返回对象内存地址  

**参数类型**  
* 必须参数  
    * `def method(s)`：s为一个必须参数  
* 关键字参数：在调用参数时写明参数名称，可以在调用时无视定义参数顺序  
    * `def method(str1, str2)`：定义一个参数  
    * method(str2 = "这是一个关键字参数调用1", str1="这是一个关键字参数调用2")  
* 默认参数：如果没有传入参数则使用默认参数  
    * `def method(name, age = 18)`  
    * method("lisi")：没有传入age参数时，默认使用定义好的age=18  
* 不定长参数：想在调用函数时传入比当时定义函数时更多的参数使用  
    * `def method1(name, *ages)`：一个*表示传入tuple类型的参数  
    * method1("lisi", 18, 19, 20)  
    * `def method2(name, **value)`：两个*表示传入dict类型的参数  
    * method2("zhangsan", a = 1, b = 2, c = 3)  

**return 语句**  
`return [表达式]`：不带参数return一个None，返回多个对象时会被封装成一个tuple返回，多个变量接收时称”解包“元组，如果一个变量接收时接收整个tuple  
`def __getitem__(self, index) -> T_co: `：表示定义了一个名为 __getitem__ 的方法，该方法接受一个 index 参数，并且返回一个类型为 T_co 的对象。这里的 T_co 具体是什么类型取决于类的上下文  

### 面向对象

```python
# 普通参数、私有参数、（有参、无参）构造函数、实例函数、类函数、静态函数、继承、重写
class FatherClassName:
    name = ''
    age = -1
    __privateElement = '私有参数'     # 有__的私有参数不可被外部类调用
    def __init__(self, n, a):
        self.name = n
        self.age = a
    def get(self):
        print("名字%s，年龄%d"%(self.name, self.age))

    def myMethod(self):
        print("fatherClassName")

    @classmethod
    def myClassMethod(cls):
        print("myClassMethod")
    @staticmethod
    def myStaticMethod():
        print("myStaticMethod")
        
class SonClassName(FatherClassName):
    def __init__(self):
        super().__init__("zhangsan",20)
        super().get()
        print(super())
        print(self)
    def myMethod(self):
        print("sonClassName")
        self.__privateMethod()
    def __privateMethod(self):
        # 私有方法在被实例调用时也需要加上self参数
        print("这是一个私有方法")

    
f = FatherClassName("lisi", 18)
f.get()
f.myMethod()
FatherClassName.myClassMethod()
FatherClassName.myStaticMethod()
print("------------------------")
s = SonClassName()
s.get()
s.myMethod()
print("从子类调用父类的重写方法" ,end = '：')
super(SonClassName,s).myMethod()
```

**类的专有方法**（运算符也可重载）：  
* `__init__` : 构造函数，在生成对象时调用  
* `__iter__`；返回一个特殊迭代器对象，对象中实现了__next__()方法  
* `__next__`：python2中的，会返回下一个迭代器对象  
* `__del__` : 析构函数，释放对象时使用  
* `__repr__` : 打印，转换  
* `__setitem__` : 按照索引赋值  
* `__getitem__`: 按照索引获取值  
* `__len__`: 获得长度  
* `__cmp__`: 比较运算  
* `__call__`: 函数调用  
* `__add__`: 加运算  
* `__sub__`: 减运算  
* `__mul__`: 乘运算  
* `__truediv__`: 除运算  
* `__mod__`: 求余运算  
* `__pow__`: 乘方  

### Lambda
lambda语法格式：`x = lambda arguments: expression`

```python
f = lambda a,b=3:a+b
print(f(2))
```

### 装饰器
`@decorator_name应用在函数、类中方法上`：1.函数；2.方法；3.静态方法；4.类方法；5.生成器函数；6.异步函数（3.5后）

**del关键字用法**

```python
# 删除变量
a = 10
del a

# 删除列表中的元素
l = [1,2,3,4,5]
del l[2]  #删除下标为2的数
del l   # 删除整个列表

# 删除切片
ll = [1,2,3,4,5]
del ll[1:3]   #删除下标为1，2的数

# 删除键值对
d = {'a':1, 'b':2, 'c':3}
del d['b']  # 删除键为b的字典值
```

### 模块
* import module1,module2,module3：引入不同的包  
* from ... import module1：从模块中导入一个指定的部分（函数、方法、属性）到当前命名空间中  
* __name__属性：用该属性可以使该程序块仅在该模块自身运行时执行  
* dir(moduleName)：找到模块内定义的所有名称，以字符串形式返回  

name属性示例代码  
```python
#!/usr/bin/python3
# Filename: using_name.py

if __name__ == '__main__':
   print('程序自身在运行')
else:
   print('我来自另一模块')
```

### 输入输出
* %操作符输出（老版输出方法）：print("my name is %s and i am %d years old"%(name, age))  
* str.format()方法输出：print("my name is {name} and i am {age} years old".format(name, age))  
* (3.6版本后)f-strings输出方法：print(f"my name is {name} i am {age} years old")  

### File
* 基本语法格式：`open(file, mode='r')`  
* 完整语法格式：`open(file, mode='r', buffering=-1, encoding=None, errors=None, newline=None, closefd=True, opener=None)`  
    * file：必须，文件相对路径或绝对路径  
    * mode：可选，文件打开模式  
    * buffering：设置缓冲  
    * encoding：解析格式（一般utf-8）  
    * errors：报错级别  
    * newline：区分换行符  
    * closefd：传入的file参数类型  
    * opener：设置自定义开启器，开启器的返回值必须是一个打开的文件描述符  

 **file中常用的函数**：  
* close()：关闭文件  
* flush()：刷新缓冲  
* read([size])：读取指定字节数，若未给定或为负则读取所有  
* readline([size])：读取整行，包括换行符'\\n'  
* readlines([sizeint])：sizeint>0时返回总和大约为sizeint字节的行，实际读取值可能比sizeint大所以需要填充缓冲区buffering  
* write(str)：将str写入文件中，返回字符串长度  
* writelines(sequence)：向文件中写入一个序列字符串列表  

---
| 模式       | r    | r+   | w    | w+   | a    | a+   |
| ---------- | ---- | ---- | ---- | ---- | ---- | ---- |
| 读         | +    | +    |      | +    |      | +    |
| 写         |      | +    | +    | +    | +    | +    |
| 创建       |      |      | +    | +    | +    | +    |
| 覆盖       |      |      | +    | +    |      |      |
| 指针在开始 | +    | +    | +    | +    |      |      |
| 指针在结尾 |      |      |      |      | +    | +    |

### 错误和异常
```python
#语法格式
try:
    pass         #执行代码...遇到异常进入except，没有异常进入else，最后进finally
except (RuntimeError, TypeError, NameError):        # except可以处理多个异常多个异常组成一个元组
    print('打印异常信息')
else:
    print("没有任何异常时执行的代码")
finally:
    print("不管有没有异常都会执行的代码")

# raise关键字触发异常类似Java中的throw
x = 10
if x > 5:
    raise Exception('x不能大于5,x的值为{}'.format(x))   # raise必须带上Exception参数该参数必须是Exception的子类
```

```python
try:
    print("try...")
except (RuntimeError):
    print("RuntimeError...")
else:
    print("else...")
finally:
    print("finally...")
```

### 作用域
* global关键字：将局部变量变为全局变量  
* nonlocal：将内部局部变量作用域向外拓展一层  

### 标准库模块
* os：提供与操作系统交互的函数，如：创建、移动、删除文件和目录，访问环境变量等  
* sys：python解释器和系统相关的功能  
* time：处理时间函数，获取当花钱时间、格式化日期和时间、计时  
* datetime：高级日期和时间处理函数  
* random：生成随机整数、浮点数、序列等  
* math：处理数学函数，三角函数、对数函数、指数函数、常数  
* re：处理正则表达式，用于文本搜索、替换、分割  
* json：对象转换为json，json解析为对象  
* urllib：访问网页 + 处理URL，下载文件、发送post请求、处理cookies  