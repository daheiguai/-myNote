# requests库

> requests库的底层是urllib库

---

## 一、简单使用

#### 1. 最基本的GET请求可以直接用get方法

```py
response = requests.get("http://www.baidu.com/")

# 也可以这么写
# response = requests.request("get", "http://www.baidu.com/")
```

### 2. 添加 headers 和 查询参数

如果想添加 headers，可以传入`headers`参数来增加请求头中的headers信息。如果要将参数放在url中传递，可以利用 `params` 参数。

```py
import requests

#如同百度地址栏的东西一样输入
kw = {'wd':'长城'}

headers = {"User-Agent": "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/54.0.2840.99 Safari/537.36"}

# params 接收一个字典或者字符串的查询参数，字典类型自动转换为url编码，不需要urlencode()
# http://www.baidu.com/s?  ‘？’可要可不要 parms会自动补，有就不补
response = requests.get("http://www.baidu.com/s?", params = kw, headers = headers)

# 查看响应内容，response.text 返回的是Unicode格式的数据
print (response.text)

# 查看响应内容，response.content返回的字节流数据
print (response.content)

# 查看完整url地址
print (response.url)

# 查看响应头部字符编码
print (response.encoding)

# 查看响应码
print (response.status_code)

#还可以看发送码
#查看发送url地址
print (response.request.url) #有时候发送时 的url  和收到响应的url地址是不同的

#查看发送 请求头
print (response.request.headers)
```

运行结果

```
......

......

'http://www.baidu.com/s?wd=%E9%95%BF%E5%9F%8E'

'utf-8'

200

http://www.baidu.com/s?wd=%E7%BE%8E%E5%A5%B3

{'User-Agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/54.0.2840.99 Safari/537.36', 'Accept-Encoding': 'gzip, deflate', 'Accept': '*/*', 'Connection': 'keep-alive'}
```

> - 使用response.text 时，Requests 会基于 HTTP 响应的文本编码自动解码响应内容，大多数 Unicode 字符集都能被无缝地解码。
> 
> - 使用response.content 时，返回的是服务器响应数据的原始二进制字节流，可以用来保存图片等二进制文件。

> - 使用response.text 时，更换编码方式：写一行 response.encoding='gbk'
> 
> - 使用response.content 时，更换编码方式：写在后面response.content.endcode('utf8')

---

## 二、基本POST请求（data参数）

## 1. 最基本post方法

```py
response = requests.post("http://www.baidu.com/", data = data)
```

## 2. 传入data数据

对于 POST 请求来说，我们一般需要为它增加一些参数。那么最基本的传参方法可以利用 `data` 这个参数。

```py
import requests


#这个东西的去浏览器找  打开开发者模式  找到对应功能的页面
formdata = {
    "type":"AUTO",
    "i":"i love python",
    "doctype":"json",
    "xmlVersion":"1.8",
    "keyfrom":"fanyi.web",
    "ue":"UTF-8",
    "action":"FY_BY_ENTER",
    "typoResult":"true"
}

url = "http://fanyi.youdao.com/translate?smartresult=dict&smartresult=rule&smartresult=ugc&sessionFrom=null"

headers={ "User-Agent": "Mozilla/5.0 (Windows NT 10.0; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/51.0.2704.103 Safari/537.36"}

response = requests.post(url, data = formdata, headers = headers)

print (response.text)

# 如果是json文件可以直接显示
print (response.json())
```

运行结果

```
{"type":"EN2ZH_CN","errorCode":0,"elapsedTime":3,"translateResult":[[{"src":"i love python","tgt":"我喜欢python"}]],"smartResult":{"type":1,"entries":["","肆文","","","高德纳","",""]}}

{'type': 'EN2ZH_CN', 'errorCode': 0, 'elapsedTime': 3, 'translateResult': [[{'src': 'i love python', 'tgt': '我喜欢python'}]], 'smartResult': {'type': 1, 'entries': ['', '肆文', '', '', '高德纳', '', '']}}
```

---

## 三、其他请求

```py
1   requests.get(‘https://github.com/timeline.json’)   # GET请求
2   requests.post(“http://httpbin.org/post”)            # POST请求
3   requests.put(“http://httpbin.org/put”)            # PUT请求
4   requests.delete(“http://httpbin.org/delete”)     # DELETE请求
5   requests.head(“http://httpbin.org/get”)           # HEAD请求
6   requests.options(“http://httpbin.org/get” )      # OPTIONS请求
```

---

## 四、相应的内容

```py
r.encoding                       #获取当前的编码
r.encoding = 'utf-8'             #设置编码
r.text                           #以encoding解析返回内容。字符串方式的响应体，会自动根据响应头部的字符编码进行解码。
r.content                        #以字节形式（二进制）返回。字节方式的响应体，会自动为你解码 gzip 和 deflate 压缩。

r.headers                        #以字典对象存储服务器响应头，但是这个字典比较特殊，字典键不区分大小写，若键不存在则返回None

r.status_code                     #响应状态码
r.raw                             #返回原始响应体，也就是 urllib 的 response 对象，使用 r.raw.read()   
r.ok                              # 查看r.ok的布尔值便可以知道是否登陆成功
 #*特殊方法*#
r.json()                         #Requests中内置的JSON解码器，以json形式返回,前提返回的内容确保是json格式的，不然解析出错会抛异常
r.raise_for_status()             #失败请求(非200响应)抛出异常
```

---

## 五、代理（proxies参数）

> 很多网站有免费的代理 ，可以拿来测试 requests可以设置时间，超时的代理可以筛选掉 下面的地方用来存几个网站
> 
> 码农很忙

如果需要使用代理，你可以通过为任意请求方法提供 `proxies` 参数来配置单个请求：

```py
import requests

# 根据协议类型，选择不同的代理
proxies = {
    #只写一个   或者创建列表随机取
  "http": "http://12.34.56.79:9527",
  "https": "http://12.34.56.79:9527",
}

response = requests.get("http://www.baidu.com", proxies = proxies)
print response.text
```

也可以通过本地环境变量 `HTTP_PROXY` 和 `HTTPS_PROXY` 来配置代理：

```py
export HTTP_PROXY="http://12.34.56.79:9527"
export HTTPS_PROXY="https://12.34.56.79:9527"
```

### 私密代理验证（特定格式） 和 Web客户端验证（auth 参数）

#### 私密代理

```py
import requests

# 如果代理需要使用HTTP Basic Auth，可以使用下面这种格式：
proxy = { "http": "mr_mao_hacker:sffqry9r@61.158.163.130:16816" }

response = requests.get("http://www.baidu.com", proxies = proxy)

print (response.text)
```

#### web客户端验证

如果是Web客户端验证，需要添加 auth = (账户名, 密码)

```py
import requests

auth=('test', '123456')

response = requests.get('http://192.168.199.107', auth = auth)

print (response.text)
```

--- 

## 六、Cookies 和 Sission

> 实现登陆的三种方式  
> 
> cookies字符串 放到headers里面
> 
> 构造cookies字典 和headers并列 的cookies参数
> 
> session
> 
> 具体方式如下

### Cookies

如果一个响应中包含了cookie，那么我们可以利用 cookies参数拿到：

```py
import requests

response = requests.get("http://www.baidu.com/")

# 7\. 返回CookieJar对象:
cookiejar = response.cookies

# 8\. 将CookieJar转为字典：
cookiedict = requests.utils.dict_from_cookiejar(cookiejar)

print (cookiejar)

print (cookiedict)
```

运行结果：

```
<RequestsCookieJar[<Cookie BDORZ=27315 for .baidu.com/>]>

{'BDORZ': '27315'}
```

### cookies登陆1

```py
# coding=utf-8
import requests

headers = {
    "User-Agent": "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_13_2) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/63.0.3239.84 Safari/537.36",
    #cookies字符串   放到headers里面
    "Cookie":"anonymid=j3jxk555-nrn0wh; _r01_=1; _ga=GA1.2.1274811859.1497951251; _de=BF09EE3A28DED52E6B65F6A4705D973F1383380866D39FF5; ln_uact=mr_mao_hacker@163.com; depovince=BJ; jebecookies=54f5d0fd-9299-4bb4-801c-eefa4fd3012b|||||; JSESSIONID=abcI6TfWH4N4t_aWJnvdw; ick_login=4be198ce-1f9c-4eab-971d-48abfda70a50; p=0cbee3304bce1ede82a56e901916d0949; first_login_flag=1; ln_hurl=http://hdn.xnimg.cn/photos/hdn421/20171230/1635/main_JQzq_ae7b0000a8791986.jpg; t=79bdd322e760beae79c0b511b8c92a6b9; societyguester=79bdd322e760beae79c0b511b8c92a6b9; id=327550029; xnsid=2ac9a5d8; loginfrom=syshome; ch_id=10016; wp_fold=0"
}



r = requests.get("http://www.renren.com/327550029/profile",headers=headers)

#保存页面
with open("renren2.html","w",encoding="utf-8") as f:
    f.write(r.content.decode())
```

### cookies登陆2

```py
# coding=utf-8
import requests

headers = {
    "User-Agent": "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_13_2) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/63.0.3239.84 Safari/537.36",
}

#构造cookies字典  和headers并列 的cookies参数 
cookies="anonymid=j3jxk555-nrn0wh; _r01_=1; _ga=GA1.2.1274811859.1497951251; _de=BF09EE3A28DED52E6B65F6A4705D973F1383380866D39FF5; ln_uact=mr_mao_hacker@163.com; depovince=BJ; jebecookies=54f5d0fd-9299-4bb4-801c-eefa4fd3012b|||||; JSESSIONID=abcI6TfWH4N4t_aWJnvdw; ick_login=4be198ce-1f9c-4eab-971d-48abfda70a50; p=0cbee3304bce1ede82a56e901916d0949; first_login_flag=1; ln_hurl=http://hdn.xnimg.cn/photos/hdn421/20171230/1635/main_JQzq_ae7b0000a8791986.jpg; t=79bdd322e760beae79c0b511b8c92a6b9; societyguester=79bdd322e760beae79c0b511b8c92a6b9; id=327550029; xnsid=2ac9a5d8; loginfrom=syshome; ch_id=10016; wp_fold=0"
cookies = {i.split("=")[0]:i.split("=")[1] for i in cookies.split("; ")}
print(cookies)

r = requests.get("http://www.renren.com/327550029/profile",headers=headers,cookies=cookies)

#保存页面
with open("renren3.html","w",encoding="utf-8") as f:
    f.write(r.content.decode())
```

### session

在 requests 里，session对象是一个非常常用的对象，这个对象代表一次用户会话：从客户端浏览器连接服务器开始，到客户端浏览器与服务器断开。

会话能让我们在跨请求时候保持某些参数，比如在同一个 Session 实例发出的所有请求之间保持 cookie 。

#### 实现人人网登录

```py
import requests

# 1\. 创建session对象，可以保存Cookie值
ssion = requests.session()

# 2\. 处理 headers
headers = {"User-Agent": "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/54.0.2840.99 Safari/537.36"}

# 3\. 需要登录的用户名和密码
data = {"email":"mr_mao_hacker@163.com", "password":"alarmchime"}  

# 4\. 发送附带用户名和密码的请求，并获取登录后的Cookie值，保存在ssion里
ssion.post("http://www.renren.com/PLogin.do", data = data)

# 5\. ssion包含用户登录后的Cookie值，可以直接访问那些登录后才可以访问的页面
response = ssion.get("http://www.renren.com/410043129/profile")

# 6\. 打印响应内容
print (response.text)
```

---

## 七、保存图片

```py
response = requests.get('xxxx.jpg')

with opne('a.png','wb') as f:
    f.write(response.context)
    f.close
```

---

## 八、处理HTTPS请求 SSL证书验证

Requests也可以为HTTPS请求验证SSL证书：

- 要想检查某个主机的SSL证书，你可以使用 verify 参数（也可以不写）

```py
import requests
response = requests.get("https://www.baidu.com/", verify=True)

# 也可以省略不写
# response = requests.get("https://www.baidu.com/")
print (r.text)
```

运行结果：

```
<!DOCTYPE html>
<!--STATUS OK--><html> <head><meta http-equiv=content-type
content=text/html;charset=utf-8><meta http-equiv=X-UA-Compatible
content=IE=Edge>百度一下，你就知道 ....
```

- 如果SSL证书验证不通过，或者不信任服务器的安全证书，则会报出SSLError，据说 12306 证书是自己做的：

来测试一下：

```py
import requests
response = requests.get("https://www.12306.cn/mormhweb/")
print (response.text)
```

果然：

`SSLError: ("bad handshake: Error([('SSL routines', 'ssl3_get_server_certificate', 'certificate verify failed')],)",)`

**如果我们想跳过 12306 的证书验证，把 verify 设置为 False 就可以正常请求了。**

```py
r = requests.get("https://www.12306.cn/mormhweb/", verify = False)
```

----

## 九、设置超时

```py
r = requests.get('url',timeout=1)           #设置秒数超时，仅对于连接有效
```

---

## 十、上传文件

使用request模块，也可以上传文件，文件的类型会自动进行处理：

```py
import requests
 
url = 'http://127.0.0.1:8080/upload'
files = {'file': open('/home/rxf/test.jpg', 'rb')}
#files = {'file': ('report.jpg', open('/home/lyb/sjzl.mpg', 'rb'))}     #显式的设置文件名
 
r = requests.post(url, files=files)
print(r.text)
```

request更加方便的是，可以把字符串当作文件进行上传：

```py
import requests
 
url = 'http://127.0.0.1:8080/upload'
files = {'file': ('test.txt', b'Hello Requests.')}     #必需显式的设置文件名
 
r = requests.post(url, files=files)
print(r.text)
```
