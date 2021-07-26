# axios

## 一、基本使用

#### 1.导入官方链接

```html
<script src="https://unpkg.com/axios/dist/axios.min.js"></script>
```

#### 2.使用方法模板

- 第一个返回函数是响应，
- 当出现错误时 第二个返回函数是错误信息

get

```javascript
axios.get(地址?查询字符串).then(function(response){},function(err){})
```

```javascript
axios.get(地址?key=value&key2=value2).then(function(response){},function(err){})
```

post

```javascript
axios.post(地址,{ley:value,key2:value}).then(function(response){},function(err){})
```

#### 3.测试例子

后端接口介绍

- get请求能获取到笑话

```
https://autumnfish.cn/api/joke/list?num=1
```

- 输入这个url浏览器能获得内容

```
{"msg":"获取1条笑话","jokes":["小时候被妈妈打，我就哭，我妈妈说：“不准哭，再哭就继续打。” 有次我妈妈又打我，我为了少受皮肉之苦，就硬忍着不哭 结果我妈妈说：“脾气还挺倔，有脾气是吧？”然后把我打到哭"]}
```

vue测试代码

```html
<!DOCTYPE html>
<html>
	<head>
		<meta charset="utf-8">
		<title>ohye</title>
	</head>
<body>
	<div id="hh">
		<input type="button" value="hhh" class="getJoke" />
	</div>
	<script src="https://unpkg.com/axios/dist/axios.min.js"></script>
	<script type="text/javascript">
		document.querySelector(".getJoke").onclick = function(){
			axios.get("https://autumnfish.cn/api/joke/list?num=1")
			.then(function(response){
				console.log(response);
			},function(err){
				console.log(err);
			})
		}
	</script>
</body>
</html>

```

- post请求接口

必须输入username参数来注册

```
https://autumnfish.cn/api/user/reg
```

vue演示

```html
<!DOCTYPE html>
<html>
	<head>
		<meta charset="utf-8">
		<title>ohye</title>
	</head>
<body>
	<div id="hh">
		<input type="button" value="hhh" class="reg" />
	</div>
	<script src="https://unpkg.com/axios/dist/axios.min.js"></script>
	<script type="text/javascript">
		document.querySelector(".reg").onclick = function(){
			axios.post("https://autumnfish.cn/api/user/reg",{username:"wdnm"})
			.then(function(response){
				console.log(response);
			},function(err){
				console.log(err);
			})
		}
	</script>
</body>
</html>
```

## 二、vue+axios

先用这段代码说明一个问题

axios里面无法使用this对象

```html
<!DOCTYPE html>
<html>
	<head>
		<meta charset="utf-8">
		<title>ohye</title>
	</head>
<body>
	<div id="hh">
		<input type="button" value="hhh" @click="getJoke" />
	</div>
	<script src="https://cdn.jsdelivr.net/npm/vue@2.6.14/dist/vue.js"></script>
	<script src="https://unpkg.com/axios/dist/axios.min.js"></script>
	<script type="text/javascript">
	var app = new Vue({
		el:"#hh",
		data:{
			joke:"hhhhhhh"
		},
		methods:{
			getJoke:function(){
				console.log(this.joke);
				axios.get("https://autumnfish.cn/api/joke/list?num=1")
				.then(function(response){
					console.log(response.data);
					console.log(this.joke);
				},function(err){
					console.log(err);
				})
			}
		}
		
	})
	</script>
</body>
</html>

```

结果

```
09:51:32.254 hhhhhhh at index.html:21
09:51:33.281 undefined at index.html:25
09:51:33.303 {"jokes":{"0":"某美女发现口红太重，拿湿纸巾擦拭后扔到路上。一老头拣起，端详半天突然醒悟，追上说：姑娘，这超薄的就是容易掉呀！","length":1},"msg":"获取1条笑话"} at index.html:24
```

得到结论：

- response.data获得response中的数据内容
- console.log(this.joke);在aixios外部能访问到定义的函树，但是在axios函数内部this会失效
- 可能应为axios里面也有一个this代表本身对象吧

解决方法

```
var that = this
```

最终代码

```html
<!DOCTYPE html>
<html>
	<head>
		<meta charset="utf-8">
		<title>ohye</title>
	</head>
<body>
	<div id="hh">
		<input type="button" value="hhh" @click="getJoke" />
		<p>{{joke}}</p>
	</div>
	<script src="https://cdn.jsdelivr.net/npm/vue@2.6.14/dist/vue.js"></script>
	<script src="https://unpkg.com/axios/dist/axios.min.js"></script>
	<script type="text/javascript">
	var app = new Vue({
		el:"#hh",
		data:{
			joke:"hhhhhhh"
		},
		methods:{
			getJoke:function(){
				var that = this;
				axios.get("https://autumnfish.cn/api/joke/list?num=1")
				.then(function(response){
					console.log(response.data);
					console.log(that.joke);
					that.joke=response.data["jokes"]["0"];
				},function(err){
					console.log(err);
				})
			}
		}
		
	})
	</script>
</body>
</html>
```

## 三、springboot+axios

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>狗子云笔记-登陆</title>
    <link rel = "stylesheet" type = "text/css" href = "css/login.css">
</head>
<body>

<div class="loginbg" ></div>

<p class="login_title">狗子云笔记</p>

<div :class="loginstyle" @click="makeme" id="login">

    <form  method="post">
        <div class="login2"><h4>登&nbsp;陆</h4></div>
        <div class="login3"><p>{{info}}</p></div>
        <div class="login3">账&nbsp;号：<input type="text"  class="text" v-model="userID"></div>
        <div class="login3">密&nbsp;码：<input type="password" class="text" v-model="password"></div>
        <div class="login3">
            &nbsp;&nbsp;&nbsp;
            <input type="button"  value="登 陆" class="but" @click="submit">
            &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
            <input type="button" value="注 册"  onclick="location='register.jsp'" class="but" >
        </div>
    </form>
</div>

<script src="https://cdn.jsdelivr.net/npm/vue@2.6.14/dist/vue.js"></script>
<script src="https://unpkg.com/axios/dist/axios.min.js"></script>
<script type="text/javascript">
// 登陆透明
// 发送登陆请求
	var app = new Vue({
		el:"#login",
		data:{
			loginstyle:"login1",
			userID:"ddd",
			password:"111",
			info:""
		},
		methods:{
		// 透明前端变换
			makeme:function(){
				this.loginstyle = "login1chang";
			},
			// 提交表单
			submit:function(){
				var that = this;
				axios({
				   method: 'post',
				   url: 'http://localhost:2635/login',
				   data: {
				      userID: this.userID,
				      password: this.password
				   },
				   // 改变数据成form springboot能接受的形式
				   transformRequest: [
				      function (data) {
				         let ret = ''
				         for (let it in data) {
				            ret += encodeURIComponent(it) + '=' + encodeURIComponent(data[it]) + '&'
				         }
				         ret = ret.substring(0, ret.lastIndexOf('&'));
				         return ret
				      }
				    ],
					// 改变数据成springboot能接受的form形式
				    headers: {
				       'Content-Type': 'application/x-www-form-urlencoded'
				    }
				})
				.then(function(response){
					that.info = response.data.wdn;
					console.log(response);
				},function(err){
					console.log(err);
				})
			}
		}
	})
	
</script>


</body>
</html>
```

## 四、外部js调用vue方法

在vue内部

```
export default {
   data() {
       return {
           title: '测试',
       }
   },
    mounted() {
        // 将vue中的方法赋值给window
       window.selectUser = this.selectUser;
    },
    methods: {
        selectUser() {
           console.log('selectUser');
        },
    }
}
  
```

## 五、html参数中使用变量

```
	<div class="left-myplace" >
		我的空间
		<div v-for="item in spaces">
			<hr>
			<ul>
				<li class="aspace" @dblclick="spacein(item.spaceID)">
					{{item.spaceName}}
				</li>
			</ul>
		</div>
		<hr>
		废纸篓
		<hr>
	</div>
```

## 六vue与thymeleaf结合使用注意事项

```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <link rel = "stylesheet" type = "text/css" href = "css/home.css">
</head>
<body>
<div id ="left">
	<div class='left-userinfo'>
		<p th:text='${username}'></p>
	</div>
	<div class="left-myplace" >
		我的空间
		<div th:each="space:${spaces}">
			<hr>
			<ul>
				<li class="aspace" th:@dblclick="|spacein(${space.spaceID})|" th:text="${space.spaceName}">
				</li>
			</ul>
		</div>
		<hr>
		废纸篓
		<hr>
	</div>
</div>
<script src="js/vue.js"></script>
<script src="https://unpkg.com/axios/dist/axios.min.js"></script>
<script type="text/javascript">
var app = new Vue({
	el:"#left",
	methods:{
		spacein:function(spaceID){
			window.location.href='showNoteBooks?spaceID='+spaceID;
		}
	}
})
</script>
</body>
</html>
```

## 七、自动点击

```
<ul style="display: none">
  <li v-for="item in webAddress">
    <a :href="item.link" target="showHere" @click='showIframe' v-trigger>{{item.name}}</a>
  </li>
</ul>

//自动触发点击事件
directives:{
      trigger:{
        inserted(el,binging){
          console.log("自动触发事件")
          el.click()
        }
      }
},
```

## 八、设置延迟

```
     methods:{
            openmd:function(mdID){
                this.ehref = this.ehref+mdID;
                this.sleep(3000).then(()=>{
                    //code 添加三秒后执行的逻辑
                    alert("fucku");
                    document.getElementById("showEa").click();
                })
            },
            sleep:function (ms) {
                return new Promise(resolve =>
                setTimeout(resolve, ms))
            }
        }
```

