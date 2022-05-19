# import导入模块的一些问题

----

## 一、import 搜索路径

#### 1.路径搜索

![img](img/模块导入1.png)

> - 从上面列出的目录里依次查找要导入的模块文件
> - '' 表示当前路径
> - 列表中的路径的先后顺序代表了python解释器在搜索模块时的先后顺序

#### 2.程序执行时添加新的模块路径

```py
sys.path.append('/home/itcast/xxx')
sys.path.insert(0, '/home/itcast/xxx')  # 可以确保先搜索这个路径
```

```py
In [37]: sys.path.insert(0,"/home/python/xxxx")
In [38]: sys.path
Out[38]: 
['/home/python/xxxx',
 '',
 '/usr/bin',
 '/usr/lib/python35.zip',
 '/usr/lib/python3.5',
 '/usr/lib/python3.5/plat-x86_64-linux-gnu',
 '/usr/lib/python3.5/lib-dynload',
 '/usr/local/lib/python3.5/dist-packages',
 '/usr/lib/python3/dist-packages',
 '/usr/lib/python3/dist-packages/IPython/extensions',
 '/home/python/.ipython']
```

---

## 二、重新导入模块

模块被导入后，`import module`不能重新导入模块，重新导入需用`reload`

![img](img/模块导入2.png)
![img](img/模块导入3.png)
![img](img/模块导入4.png)
![img](img/模块导入5.png)

---

## 三、多模块开发时的注意点

import something语法：

> 如果接下来 改变了something模块中 的变量值 那么 其他模块再导入这个模块
> 
> 得到的变量值会是改变后的



建议使用from something  import  something2:

> somthing2相当于创建了一个新的 变量     指向了  something.something2的【值】
> 
> 之后其他模块再导入something模块不会有影响

![img](img/模块导入6.png)
![img](img/模块导入7.png)

## 四、导入不同目录下的模块

目录只有包含一个叫做 _\_init__.py 的文件才会被认作是一个包，主要是为了避免一些滥俗的名字（比如叫做 string）不小心的影响搜索路径中的有效模块。

最简单的情况，放一个空的 :file:__init__.py就可以了。当然这个文件中也可以包含一些初始化代码或者为（将在后面介绍的） __all__变量赋值。

用户可以每次只导入一个包里面的特定模块

此外需要用到sys.path.append(path)命令。

