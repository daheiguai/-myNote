# property属性

----- 

## 一、property属性

 什么是property属性

>  一种用起来像是使用的实例属性一样的特殊属性，可以对应于某个方法

![img](img/pro属性1.png)

property属性的定义和调用要注意一下几点：

> - 定义时，在实例方法的基础上添加 @property 装饰器；并且仅有一个self参数
> 
> - 调用时，无需括号

- ```py
    方法：foo_obj.func()
    property属性：foo_obj.prop
  ```

property属性的有两种方式

> - 装饰器 即：在方法上应用装饰器
> - 类属性 即：在类中定义值为property对象的类属性

#### 1.装饰器方式

在类的实例方法上应用@property装饰器

Python中的类有`经典类`和`新式类`，`新式类`的属性比`经典类`的属性丰富。（ 如果类继object，那么该类是新式类 ）

**1)经典类，具有一种@property装饰器**

```py
# ############### 定义 ###############    
class Goods:
    @property
    def price(self):
        return "laowang"
# ############### 调用 ###############
obj = Goods()
result = obj.price  # 自动执行 @property 修饰的 price 方法，并获取方法的返回值
print(result)
```

**2)新式类，具有三种@property装饰器**

```py
#coding=utf-8
# ############### 定义 ###############
class Goods:
    """python3中默认继承object类
        以python2、3执行此程序的结果不同，因为只有在python3中才有@xxx.setter  @xxx.deleter
    """
    @property
    def price(self):
        print('@property')

    @price.setter  # 如果不需要重新赋值或者删除   下面这两个可以不写
    def price(self, value):
        print('@price.setter')

    @price.deleter
    def price(self):
        print('@price.deleter')

# ############### 调用 ###############
obj = Goods()
obj.price          # 自动执行 @property 修饰的 price 方法，并获取方法的返回值
obj.price = 123    # 自动执行 @price.setter 修饰的 price 方法，并将  123 赋值给方法的参数
del obj.price      # 自动执行 @price.deleter 修饰的 price 方法  
```

#### 2.类属性方式，创建值为property对象的类属性

当使用类属性的方式创建property属性时，`经典类`和`新式类`无区别

```py
class Foo:
    def get_bar(self):
        return 'laowang'

    BAR = property(get_bar)

obj = Foo()
reuslt = obj.BAR  # 自动调用get_bar方法，并获取方法的返回值
print(reuslt)

'''property方法中有个四个参数

第一个参数是方法名，调用 对象.属性 时自动触发执行方法
第二个参数是方法名，调用 对象.属性 ＝ XXX 时自动触发执行方法
第三个参数是方法名，调用 del 对象.属性 时自动触发执行方法
第四个参数是字符串，调用 对象.属性.__doc__ ，此参数是该属性的描述信息'''
```

全例子

```py
#coding=utf-8
class Foo(object):
    def get_bar(self):
        print("getter...")
        return 'laowang'

    def set_bar(self, value): 
        """必须两个参数"""
        print("setter...")
        return 'set value' + value

    def del_bar(self):
        print("deleter...")
        return 'laowang'

    BAR = property(get_bar, set_bar, del_bar, "description...")

obj = Foo()

obj.BAR  # 自动调用第一个参数中定义的方法：get_bar
obj.BAR = "alex"  # 自动调用第二个参数中定义的方法：set_bar方法，并将“alex”当作参数传入
desc = Foo.BAR.__doc__  # 自动获取第四个参数中设置的值：description...
print(desc)
del obj.BAR  # 自动调用第三个参数中定义的方法：del_bar方法
```

#### 3.Django框架中应用了property属性（了解）

WEB框架 Django 的视图中 request.POST 就是使用的类属性的方式创建的属性

```py
class WSGIRequest(http.HttpRequest):
    def __init__(self, environ):
        script_name = get_script_name(environ)
        path_info = get_path_info(environ)
        if not path_info:
            # Sometimes PATH_INFO exists, but is empty (e.g. accessing
            # the SCRIPT_NAME URL without a trailing slash). We really need to
            # operate as if they'd requested '/'. Not amazingly nice to force
            # the path like this, but should be harmless.
            path_info = '/'
        self.environ = environ
        self.path_info = path_info
        self.path = '%s/%s' % (script_name.rstrip('/'), path_info.lstrip('/'))
        self.META = environ
        self.META['PATH_INFO'] = path_info
        self.META['SCRIPT_NAME'] = script_name
        self.method = environ['REQUEST_METHOD'].upper()
        _, content_params = cgi.parse_header(environ.get('CONTENT_TYPE', ''))
        if 'charset' in content_params:
            try:
                codecs.lookup(content_params['charset'])
            except LookupError:
                pass
            else:
                self.encoding = content_params['charset']
        self._post_parse_error = False
        try:
            content_length = int(environ.get('CONTENT_LENGTH'))
        except (ValueError, TypeError):
            content_length = 0
        self._stream = LimitedStream(self.environ['wsgi.input'], content_length)
        self._read_started = False
        self.resolver_match = None

    def _get_scheme(self):
        return self.environ.get('wsgi.url_scheme')

    def _get_request(self):
        warnings.warn('`request.REQUEST` is deprecated, use `request.GET` or '
                      '`request.POST` instead.', RemovedInDjango19Warning, 2)
        if not hasattr(self, '_request'):
            self._request = datastructures.MergeDict(self.POST, self.GET)
        return self._request

    @cached_property
    def GET(self):
        # The WSGI spec says 'QUERY_STRING' may be absent.
        raw_query_string = get_bytes_from_wsgi(self.environ, 'QUERY_STRING', '')
        return http.QueryDict(raw_query_string, encoding=self._encoding)

    # ############### 看这里看这里  ###############
    def _get_post(self):
        if not hasattr(self, '_post'):
            self._load_post_and_files()
        return self._post

    # ############### 看这里看这里  ###############
    def _set_post(self, post):
        self._post = post

    @cached_property
    def COOKIES(self):
        raw_cookie = get_str_from_wsgi(self.environ, 'HTTP_COOKIE', '')
        return http.parse_cookie(raw_cookie)

    def _get_files(self):
        if not hasattr(self, '_files'):
            self._load_post_and_files()
        return self._files

    # ############### 看这里看这里  ###############
    POST = property(_get_post, _set_post)

    FILES = property(_get_files)
    REQUEST = property(_get_request
```

#### 4. __slots__魔法

需要注意的是__slots__的限定只对当前类的对象生效，对子类并不起任何作用。

```py
class Person(object):
 # 限定Person对象只能绑定_name, _age和_gender属性
 __slots__ = ('_name', '_age', '_gender')
 def __init__(self, name, age):
 self._name = name
 self._age = age

@property
def name(self):
    return self._name

@property
def age(self):
    return self._age

@age.setter
def age(self, age):
    self._age = age
```



property总结

> - 定义property属性共有两种方式，分别是【装饰器】和【类属性】，而【装饰器】方式针对经典类和新式类又有所不同。
> - 通过使用property属性，能够简化调用者在获取数据的流程
