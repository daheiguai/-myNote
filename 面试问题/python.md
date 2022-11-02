# 基础

## 数组

## 字典

## lambda表达式

冒号前，传入的是参数，后面是计算式子

```python
test = lambda x,y: x+y

test(1,1) #  2

#就使用方法而言，和下面这个函数等效
def test2(x,y):
    return x+y

test2(1,1) # 2 
```



# 迭代器生成器

# 单例模式

# 闭包装饰器

## 闭包

### 1. 函数引用

函数也是个对象   把括号去掉后就是他的引用

```py
def test1():
    print("--- in test1 func----")

# 调用函数
test1()

# 引用函数
ret = test1    

print(id(ret))
print(id(test1))

#通过引用调用函数
ret()
```

运行结果:

```
--- in test1 func----
140212571149040
140212571149040
--- in test1 func----
```

### 2. 什么是闭包

```py
# 定义一个函数
def test(number):

    # 在函数内部再定义一个函数，并且这个函数用到了外边函数的变量，
    #那么将这个函数以及用到的一些变量称之为闭包
    #定义函数只要不调用  就不会执行
    def test_in(number_in):
        print("in test_in 函数, number_in is %d" % number_in)
        return number+number_in
    # 其实这里返回的就是闭包的结果
    return test_in


# 给test函数赋值，这个20就是给参数number
ret = test(20)

# 注意这里的100其实给参数number_in
print(ret(100))

#注 意这里的200其实给参数number_in
print(ret(200))
```

运行结果 ：

```
in test_in 函数, number_in is 100
120

in test_in 函数, number_in is 200
220
```

### 3. 看一个闭包的实际例子：

```py
def line_conf(a, b):
    def line(x):
        return a*x + b
    return line

line1 = line_conf(1, 1)
line2 = line_conf(4, 5)
print(line1(5))
print(line2(5))
```

这个例子中，函数line与变量a,b构成闭包。在创建闭包的时候，我们通过line_conf的参数a,b说明了这两个变量的取值，这样，我们就确定了函数的最终形式(y = x + 1和y = 4x + 5)。我们只需要变换参数a,b，就可以获得不同的直线表达函数。由此，我们可以看到，闭包也具有提高代码可复用性的作用。

如果没有闭包，我们需要每次创建直线函数的时候同时说明a,b,x。这样，我们就需要更多的参数传递，也减少了代码的可移植性。

注意点:

由于闭包引用了外部函数的局部变量，则外部函数的局部变量没有及时释放，消耗内存

## 装饰器

### 装饰器(decorator)功能

1. 引入日志
2. 函数执行时间统计
3. 执行函数前预备处理
4. 执行函数后清理功能
5. 权限校验等场景
6. 缓存

### 简单装饰器

#### 1.如何使用

```py
# 1.装饰器的格式   是一个闭包
# 2.装饰器需要传入调用它的函数
# 3.装饰器的闭包函数处理   前置操作---调用函数----后置操作
# 4.装饰器最后返回闭包函数，，，，，也就是说，装饰器通过闭包，把调用函数拼 在一起
def testYou(func):   
    def call_func():
        print("检验确认身份")
        func()
    return call_func

@testYou    #---->调用装饰器   
def qiandao():
    print("成功签到")


qiandao()
```

 结果

```py
D:\forLea\pyProg\fistOfAll\Scripts\python.exe D:/forLea/pyProg/fuck.py
检验确认身份
成功签到
```

#### 2.原理

通过一个闭包函数改变函数的引用，在函数前、后处理一些事情

```py
def testYou(func):   #装饰器的格式   是一个闭包
    def call_func():
        print("检验确认身份")
        func()
    return call_func

 #@testYou
def qiandao():
    print("成功签到")


qiandao = testYou(qiandao)
qiandao()
```

### 被装饰的函数有 参数

```py
def testYou(func):   #装饰器的格式   是一个闭包
    def call_func(a,b):   
        print("检验确认身份")
        func(a,b)
    return call_func

@testYou    #---->调用装饰器
def qiandao(a,b):
    print("成功签到,第%d月第%d次"%(a,b))


qiandao(1,2)
```

    D:\forLea\pyProg\fistOfAll\Scripts\python.exe D:/forLea/pyProg/fuck.py
    检验确认身份
    成功签到,第1月第2次
    
    进程已结束，退出代码 0

### 装饰的函数有不定长参数

```py
def testYou(func):  
    def call_func(*args,**kwargs):  
        print("检验确认身份")
        func(*args,**kwargs)
    return call_func

@testYou    #---->调用装饰器
def qiandao(a,b,*args,**kwargs):
    print("成功签到,第%d月第%d次"%(a,b))
    print(*args,**kwargs)


qiandao(1,2,3,4,'hhhh','cccc','wdnmd')
```

    D:\forLea\pyProg\fistOfAll\Scripts\python.exe D:/forLea/pyProg/fuck.py
    检验确认身份
    成功签到,第1月第2次
    3 4 hhhh cccc wdnmd
    
    进程已结束，退出代码 0

### 返回值

```py
def testYou(func):  
    def call_func():
        print("检验确认身份")
        return func() #返回调用函数
    return call_func

@testYou    #---->调用装饰器
def qiandao():
    print("成功签到")
    return 'wdnmd'


say = qiandao()
print(say)
```

    D:\forLea\pyProg\fistOfAll\Scripts\python.exe D:/forLea/pyProg/fuck.py
    检验确认身份
    成功签到
    wdnmd
    
    进程已结束，退出代码 0

### 多个装饰器

```py
def eatnm(func):
    print('准备柠檬中')
    def call_func():
        print('吃柠檬')
        func()   #-----> 调用testYou中的  call_func
    return call_func   #----->   第二次（最终）   qiandao = 它

def testYou(func):   
    print('开始确认身份')
    def call_func():
        print("确认成功")
        func()     
    return call_func   #----->第一次(开始)   qiandao = 它  但是这个函数还没被调用

@eatnm
@testYou    #---->调用装饰器
def qiandao():
    print("成功签到")


qiandao()  #---->  调用了  eatnm中的call_func
```

    D:\forLea\pyProg\fistOfAll\Scripts\python.exe D:/forLea/pyProg/fuck.py
    开始确认身份
    准备柠檬中
    吃柠檬
    确认成功
    成功签到
    
    进程已结束，退出代码 0

# GC垃圾回收

## 引用计数器

引用计数是python的主要垃圾回收方式，它是**不可以取消**的

当Python创建一个对象的时候， 底层的C语言的对象会拥有python对象和该对象的引用计数

当该对象被引用时，属于该对象的引用计数会增加

当该对象被取消引用时， 属于该对象引用计数会减少

当该对象的引用计数为0时，该对象所使用的的内存将被释放

## 标记清楚法

## 分代标记

## 三色标记

## 循环引用



# 并发

## 多线程

## 线程池

## 协程

