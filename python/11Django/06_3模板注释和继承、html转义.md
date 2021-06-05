# 注释

在模板中使用如下模板注释，这段代码不会被编译，不会输出到客户端；html注释只能注释html内容，不能注释模板语言。

1）单行注释语法如下：

```
{#...#}
```

注释可以包含任何模版代码，有效的或者无效的都可以。

```
{# { % if foo % }bar{ % else % } #}
```

2）多行注释使用comment标签，语法如下：

```
{%comment%}
...
{%endcomment%}
```

---

# 继承

模板继承和类的继承含义是一样的，主要是为了提高代码重用，减轻开发人员的工作量。

**典型应用：网站的头部、尾部信息。**

#### 父模板

如果发现在多个模板中某些内容相同，那就应该把这段内容定义到父模板中。

标签block：用于在父模板中预留区域，留给子模板填充差异性的内容，名字不能相同。 为了更好的可读性，建议给endblock标签写上名字，这个名字与对应的block名字相同。父模板中也可以使用上下文中传递过来的数据。

```
{%block 名称%}
预留区域，可以编写默认内容，也可以没有默认内容
{%endblock  名称%}
```

#### 子模板

标签extends：继承，写在子模板文件的第一行。

```
{% extends "父模板路径"%}
```

子模版不用填充父模版中的所有预留区域，如果子模版没有填充，则使用父模版定义的默认值。

填充父模板中指定名称的预留区域。

```
{%block 名称%}
实际填充内容
{{block.super}}用于获取父模板中block的内容  不写就不要了
{%endblock 名称%}
```

#### 示例

1）打开booktest/views.py文件，创建视图temp_inherit。

```
def temp_inherit(request):
    context={'title':'模板继承','list':BookInfo.objects.all()}
    return render(request,'booktest/temp_inherit.html',context)
```

2）打开booktest/urls.py文件，配置url。

```
    url(r'^temp_inherit/$', views.temp_inherit),
```

3）在templates下创建inherit_base.html。

```
<html>
<head>
    <title>{{title}}</title>
</head>
<body>
<h2>这是头</h2>
<hr>
{%block qu1%}
这是区域一，有默认值
{%endblock qu1%}
<hr>
{%block qu2%}
{%endblock qu2%}
<hr>
<h2>这是尾</h2>
</body>
</html>
```

4）在templates/booktest下创建temp_inherit.html。

```
{%extends 'booktest/inherit_base.html'%}
{%block qu2%}
<ul>
    {%for book in list%}
    <li>{{book.btitle}}</li>
    {%endfor%}
</ul>
{%endblock qu2%}
```

# HTML转义

模板对上下文传递的字符串进行输出时，会对以下字符自动转义。

```
小于号< 转换为 &lt;

大于号> 转换为 &gt;

单引号' 转换为 &#39;

双引号" 转换为 &quot;

与符号& 转换为 &amp;
```

#### 示例

1）打开booktest/views.py文件，创建视图html_escape。

```
def html_escape(request):
    context={'content':'<h1>hello world</h1>'}
    return render(request,'booktest/html_escape.html',context)
```

2）打开booktest/urls.py文件，配置url。

```
    url(r'^html_escape/$', views.html_escape),
```

3）在templates/booktest/目录下创建html_escape.html。

```
<html>
<head>
    <title>转义</title>
</head>
<body>
自动转义：{{content}}
</body>
</html>
```

4）运行服务器，在浏览器中输入如下网址。

```
http://127.0.0.1:8000/html_escape/
```

> 转义后标记代码不会被直接解释执行，而是被直接呈现，防止客户端通过嵌入js代码攻击网站.

浏览效果如下图:

![转义](F:\-myStudy\web\04Django\img\p4_1.png)

#### 关闭转义和 模板硬编码手动转义

过滤器escape可以实现对变量的html转义，默认模板就会转义，一般省略。

```
{{t1|escape}}
```

过滤器safe：禁用转义，告诉模板这个变量是安全的，可以解释执行。

```
{{data|safe}}
```

1）修改templates/booktest/html_escape.html代码如下。

```
<html>
<head>
    <title>转义</title>
</head>
<body>
自动转义：{{content}}
<hr>
过滤器safe关闭转义：{{content|safe}}
<hr>
标签autoescape关闭转义：
{%autoescape off%}
{{content}}
{%endautoescape%}
<hr>
模板硬编码不转义：{{data|default:'<b>hello</b>'}}
<hr>
模板硬编码手动转义：{{data|default:"&lt;b&gt;123&lt;/b&gt;"}}
</body>
</html>
```

2）刷新浏览器后效果如下图：

![转义](img/html转义.png)