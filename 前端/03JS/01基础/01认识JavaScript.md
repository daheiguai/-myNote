# 认识JavaScript

> 数据存储单位
> 
> - 位(bit)： 1bit 可以保存一个 0 或者 1 （最小的存储单位）
> - 字节(Byte)：1B = 8b
> - 千字节(KB)：1KB = 1024B
> - 兆字节(MB)：1MB = 1024KB
> - 吉字节(GB): 1GB = 1024MB
> - 太字节(TB): 1TB = 1024GB

## 一、初识JavaScript

### 1 JavaScript 是什么

![](images\图片7.png)

- JavaScript 是世界上最流行的语言之一，是一种运行在客户端的脚本语言 （Script 是脚本的意思）

- 脚本语言：不需要编译，运行过程中由 js 解释器( js 引擎）逐行来进行解释并执行

- 现在也可以基于 Node.js 技术进行服务器端编程
  
    ![](images\图片8.png)

### 2 JavaScript的作用

- 表单动态校验（密码强度检测）  （ JS 产生最初的目的 ）
- 网页特效
- 服务端开发(Node.js)
- 桌面程序(Electron)
- App(Cordova) 
- 控制硬件-物联网(Ruff)
- 游戏开发(cocos2d-js)

### 3 HTML/CSS/JS 的关系

![](images\图片9.png)

### 4 浏览器执行 JS 简介

**浏览器分成两部分：渲染引擎和 JS 引擎**

![](images\neihe.png)

        浏览器本身并不会执行JS代码，而是通过内置 JavaScript 引擎(解释器) 来执行 JS 代码 。JS 引擎执行代码时逐行解释每一句源码（转换为机器语言），然后由计算机去执行，所以 JavaScript 语言归为脚本语言，会逐行解释执行。

![](images\图片10.png)

### 5 JS 的组成

![](images\图片11.png)

1. #### **ECMAScript**
   
       ​        ECMAScript 是由ECMA 国际（ 原欧洲计算机制造商协会）进行标准化的一门编程语言，这种语言在万维网上应用广泛，它往往被称为 JavaScript或 JScript，但实际上后两者是 ECMAScript 语言的实现和扩展。
   
   ![](images\图片12.png)
   
   ​        ECMAScript：规定了JS的编程语法和基础核心知识，是所有浏览器厂商共同遵守的一套JS语法工业标准。
   
   更多参看MDN: [MDN手册](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/JavaScript_technologies_overview)

2. #### **DOM——文档对象模型**
   
       ​        **文档对象模型**（DocumentObject Model，简称DOM），是W3C组织推荐的处理可扩展标记语言的标准编程接口。通过 DOM 提供的接口可以对页面上的各种元素进行操作（大小、位置、颜色等）

3. #### **BOM——浏览器对象模型**
   
       ​        **浏览器对象模型**(Browser Object Model，简称BOM) 是指浏览器对象模型，它提供了独立于内容的、可以与浏览器窗口进行互动的对象结构。通过BOM可以操作浏览器窗口，比如弹出框、控制浏览器跳转、获取分辨率等。

# 
