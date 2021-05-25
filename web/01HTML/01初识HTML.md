# 初识HTML

---

### web标准

> 是w3c（万维网联盟）组织和其他标准化组织制定的

内容：

结构、表现和行为 ---->HTML、CSS、JavaScript

### HTML

HTML --->Hyper Text Markup Language(超文本标记语言)

是标记语言而不是编程语言，标记语言是一套标记标签

### 超文本：

                    1插入声音、动画、图片、多媒体内容等（超越文本限制）

                    2从一个页面跳转到另一个界面（超级链接文本 ）

### 骨架标签：

```html
<html>          ---->页面最大的标签，根标签

 <head>     --->文档的头部，head标签中必须设置title标签

 <title></title>   --->文档标题，就是页面的网页标题

 </head>

 <body>   ----->文档主体，页面中的大部分东西

 </body>
```

</html>

骨架标签例子：我的第一个HTML

<html>

```html
 <head> 

 <title>My first html</title> 

 </head>

 <body>

 Welcom Baby

 </body>
```

</html>

### 空元素<br />

只有一个标签   其他大部分是双标签，有头有尾

### 文档类型<!DOCTYPE>：

在最前面（HTML标签前），作用是告知浏览器HTML哪种规范（即哪个HTML版本）

<!DOCTYPE html>

<!DOCTYPE>就是使用HTML5的规范的意思

### 页面语言lang：

<html lang = "en">放在html标签里，用于指定语言    一般有en（英语），zh-CN（中文）当然还有法语、日语等

只是告诉浏览器这个页面大部分是用什么语言写的，有个作用是让提示是否翻译，下边用什么语言怎样写都行

### 字符集（character set）：

utf-8（世界通用）、gb2312（简体中文，6763个汉字）、BIG5（繁体中文，港澳台地区使用较多）、GBK（全部中文字符，是GB2312的扩展，加入繁体字，兼容GB2312）

用法：<meta charset="UTF-8">（尽量写大写格式"UTF-8",把"-"写出来），这个标签包含在head标签里面，和title标签并列


