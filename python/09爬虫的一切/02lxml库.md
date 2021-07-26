# lxml的使用

---

## 一、 lxml模块的入门使用

1. 导入lxml 的 etree 库 (导入没有提示不代表不能用)

```python
from lxml import etree
```

2. 利用etree.HTML，将字符串转化为Element对象,Element对象具有xpath的方法,返回结果的列表，能够接受bytes类型的数据和str类型的数据

```python
html = etree.HTML(text) 
ret_list = html.xpath("xpath字符串")
```

3. 把转化后的element对象转化为字符串，返回bytes类型结果 etree.tostring(element)  
   假设我们现有如下的html字符换，尝试对他进行操作

```python
<div> <ul> 
<li class="item-1"><a href="link1.html">first item</a></li> 
<li class="item-1"><a href="link2.html">second item</a></li> 
<li class="item-inactive"><a href="link3.html">third item</a></li> 
<li class="item-1"><a href="link4.html">fourth item</a></li> 
<li class="item-0"><a href="link5.html">fifth item</a> # 注意，此处缺少一个 </li> 闭合标签 
</ul> </div>
```

```python
from lxml import etree
text = ''' <div> <ul>         <li class="item-1"><a href="link1.html">first item</a></li>         <li class="item-1"><a href="link2.html">second item</a></li>         <li class="item-inactive"><a href="link3.html">third item</a></li>         <li class="item-1"><a href="link4.html">fourth item</a></li>         <li class="item-0"><a href="link5.html">fifth item</a>         </ul> </div> '''

html = etree.HTML(text)
print(type(html)) 

handeled_html_str = etree.tostring(html).decode()
print(handeled_html_str)
```

**输出为**

```python
<class 'lxml.etree._Element'>
<html><body><div> <ul> 
        <li class="item-1"><a href="link1.html">first item</a></li> 
        <li class="item-1"><a href="link2.html">second item</a></li> 
        <li class="item-inactive"><a href="link3.html">third item</a></li> 
        <li class="item-1"><a href="link4.html">fourth item</a></li> 
        <li class="item-0"><a href="link5.html">fifth item</a> 
        </li></ul> </div> </body></html>
```

可以发现，lxml确实能够把确实的标签补充完成，但是请注意：  
**lxml是人写的，很多时候由于网页不够规范，或者是lxml的bug，即使参考url地址对应的响应去提取数据，任然获取不到，这个时候我们需要使用etree.tostring的方法，观察etree到底把html转化成了什么样子，即根据转化后的html字符串去进行数据的提取。**

---

## 二、lxml的深入练习

> 接下来我们继续操作，假设每个class为item-1的li标签是1条新闻数据，如何把这条新闻数据组成一个字典

```python
from lxml import etree
text = ''' <div> <ul>         <li class="item-1"><a href="link1.html">first item</a></li>         <li class="item-1"><a href="link2.html">second item</a></li>         <li class="item-inactive"><a href="link3.html">third item</a></li>         <li class="item-1"><a href="link4.html">fourth item</a></li>         <li class="item-0"><a href="link5.html">fifth item</a>         </ul> </div> '''

html = etree.HTML(text)

#获取href的列表和title的列表
href_list = html.xpath("//li[@class='item-1']/a/@href")
title_list = html.xpath("//li[@class='item-1']/a/text()")

#组装成字典
for href in href_list:
    item = {}
    item["href"] = href
    item["title"] = title_list[href_list.index(href)]
    print(item)
```

**输出为**

```python
{'href': 'link1.html', 'title': 'first item'}
{'href': 'link2.html', 'title': 'second item'}
{'href': 'link4.html', 'title': 'fourth item'}
```

> 假设在某种情况下，某个新闻的href没有，那么会怎样呢？

```python
from lxml import etree
text = ''' <div> <ul>         <li class="item-1"><a>first item</a></li>         <li class="item-1"><a href="link2.html">second item</a></li>         <li class="item-inactive"><a href="link3.html">third item</a></li>         <li class="item-1"><a href="link4.html">fourth item</a></li>         <li class="item-0"><a href="link5.html">fifth item</a>         </ul> </div> '''
```

结果是

```python
{'href': 'link2.html', 'title': 'first item'}
{'href': 'link4.html', 'title': 'second item'}
```

数据的对应全部错了，这不是我们想要的，接下来通过2.3小节来解决这个问题

---

## 三、lxml模块的进阶使用

> 前面我们取到属性，或者是文本的时候，返回字符串 但是如果我们取到的是**一个节点**，返回什么呢?

**返回的是element对象，可以继续使用xpath方法，对此我们可以在后面的数据提取过程中：先根据某个标签进行分组，分组之后再进行数据的提取**

示例如下：

```python
from lxml import etree
text = ''' <div> <ul>
         <li class="item-1"><a>first item</a></li>
         <li class="item-1"><a href="link2.html">second item</a></li>
         <li class="item-inactive"><a href="link3.html">third item</a></li>
         <li class="item-1"><a href="link4.html">fourth item</a></li>
         <li class="item-0"><a href="link5.html">fifth item</a>
         </ul> </div> '''

html = etree.HTML(text)

li_list = html.xpath("//li[@class='item-1']")
print(li_list)
```

结果为：

```python
[<Element li at 0x11106cb48>, <Element li at 0x11106cb88>, <Element li at 0x11106cbc8>]
```

可以发现结果是一个element对象，这个对象能够继续使用xpath方法

先根据li标签进行分组，之后再进行数据的提取

```python
from lxml import etree
text = ''' <div> <ul>
         <li class="item-1"><a>first item</a></li>
         <li class="item-1"><a href="link2.html">second item</a></li>
         <li class="item-inactive"><a href="link3.html">third item</a></li>
         <li class="item-1"><a href="link4.html">fourth item</a></li>
         <li class="item-0"><a href="link5.html">fifth item</a>
         </ul> </div> '''

#根据li标签进行分组
html = etree.HTML(text)
li_list = html.xpath("//li[@class='item-1']")

#在每一组中继续进行数据的提取
for li in li_list:
    item = {}
    item["href"] = li.xpath("./a/@href")[0] if len(li.xpath("./a/@href"))>0 else None
    item["title"] = li.xpath("./a/text()")[0] if len(li.xpath("./a/text()"))>0 else None
    print(item)
```

结果是：

```python
{'href': None, 'title': 'first item'}
{'href': 'link2.html', 'title': 'second item'}
{'href': 'link4.html', 'title': 'fourth item'}
```

前面的代码中，进行数据提取需要判断，可能某些一面不存在数据的情况，对应的可以使用三元运算符来解决

以上提取数据的方式：先分组再提取，都会是我们进行数据的提取的主要方法
