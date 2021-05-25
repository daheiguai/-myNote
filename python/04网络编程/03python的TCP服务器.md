#### 1.Tcp客户端构建流程

```py
from socket import *

# 创建socket

tcp_client_socket = socket(AF_INET, SOCK_STREAM)

# 目的信息

server_ip = input("请输入服务器ip:")

server_port = int(input("请输入服务器port:"))

# 链接服务器

tcp_client_socket.connect((server_ip, server_port))

# 提示用户输入数据

send_data = input("请输入要发送的数据：")

tcp_client_socket.send(send_data.encode("gbk"))            #udp是sendto

# 接收对方发送过来的数据，最大接收1024个字节

recvData = tcp_client_socket.recv(1024)                #udp收到的是元组

print('接收到的数据为:', recvData.decode('gbk'))

# 关闭套接字

tcp_client_socket.close()


```

#### 2.tcp服务器

```py
from socket import *

# 创建socket

tcp_server_socket = socket(AF_INET, SOCK_STREAM)

# 本地信息

address = ('', 7788)

# 绑定

tcp_server_socket.bind(address)

# 使用socket创建的套接字默认的属性是主动的，使用listen将其变为被动的，这样就可以接收别人的链接了

tcp_server_socket.listen(128)

# 如果有新的客户端来链接服务器，那么就产生一个新的套接字专门为这个客户端服务# client_socket用来为这个客户端服务

# tcp_server_socket就可以省下来专门等待其他新客户端的链接

#得到一个元组 （和客户端连接的 新socket ， 客户端地址）

client_socket, clientAddr = tcp_server_socket.accept()

# 接收对方发送过来的数据

recv_data = client_socket.recv(1024) # 接收1024个字节

print('接收到的数据为:', recv_data.decode('gbk'))

# 发送一些数据到客户端

client_socket.send("thank you !".encode('gbk'))

# 关闭为这个客户端服务的套接字，只要关闭了，就意味着为不能再为这个客户端服务了，如果还需要服务，只能再次重新连接

client_socket.close()
```

---

#### tcp注意点

1. tcp服务器一般情况下都需要绑定，否则客户端找不到这个服务器

2. tcp客户端一般不绑定，因为是主动链接服务器，所以只要确定好服务器的ip、port等信息就好，本地客户端可以随机

3. tcp服务器中通过listen可以将socket创建出来的主动套接字变为被动的，这是做tcp服务器时必须要做的

4. 当客户端需要链接服务器时，就需要使用connect进行链接，udp是不需要链接的而是直接发送，但是tcp必须先链接，只有链接成功才能通信

5. 当一个tcp客户端连接服务器时，服务器端会有1个新的套接字，这个套接字用来标记这个客户端，单独为这个客户端服务

6. listen后的套接字是被动套接字，用来接收新的客户端的链接请求的，而accept返回的新套接字是标记这个新客户端的

7. 关闭listen后的套接字意味着被动套接字关闭了，会导致新的客户端不能够链接服务器，但是之前已经链接成功的客户端正常通信。

8. 关闭accept返回的套接字意味着这个客户端已经服务完毕

9. 当客户端的套接字调用close后，服务器端会recv解堵塞，并且返回的长度为0，因此服务器可以通过返回数据的长度来区别客户端是否已经下线----->close 会 发送一断空字符

### 

## tcp长连接和短连接

TCP在真正的读写操作之前，server与client之间必须建立一个连接，

当读写操作完成后，双方不再需要这个连接时它们可以释放这个连接，

连接的建立通过三次握手，释放则需要四次握手，

所以说每个连接的建立都是需要资源消耗和时间消耗的。

## 1. TCP短连接

模拟一种TCP短连接的情况:

1. client 向 server 发起连接请求

2. server 接到请求，双方建立连接

3. client 向 server 发送消息

4. server 回应 client

5. 一次读写完成，此时双方任何一个都可以发起 close 操作

在步骤5中，一般都是 client 先发起 close 操作。当然也不排除有特殊的情况。

从上面的描述看，短连接一般只会在 client/server 间传递一次读写操作！

## 2. TCP长连接

再模拟一种长连接的情况:

1. client 向 server 发起连接

2. server 接到请求，双方建立连接

3. client 向 server 发送消息

4. server 回应 client

5. 一次读写完成，连接不关闭

6. 后续读写操作...

7. 长时间操作之后client发起关闭请求

## 3. TCP长/短连接操作过程

### 3.1 短连接的操作步骤是：

建立连接——数据传输——关闭连接...建立连接——数据传输——关闭连接

### 3.2 长连接的操作步骤是：

建立连接——数据传输...（保持连接）...数据传输——关闭连接

## 4. TCP长/短连接的优点和缺点

- 长连接可以省去较多的TCP建立和关闭的操作，减少浪费，节约时间。
  
  对于频繁请求资源的客户来说，较适用长连接。

- client与server之间的连接如果一直不关闭的话，会存在一个问题，
  
  随着客户端连接越来越多，server早晚有扛不住的时候，这时候server端需要采取一些策略，
  
  如关闭一些长时间没有读写事件发生的连接，这样可以避免一些恶意连接导致server端服务受损；
  
  如果条件再允许就可以以客户端机器为颗粒度，限制每个客户端的最大长连接数，
  
  这样可以完全避免某个蛋疼的客户端连累后端服务。

- 短连接对于服务器来说管理较为简单，存在的连接都是有用的连接，不需要额外的控制手段。

- 但如果客户请求频繁，将在TCP的建立和关闭操作上浪费时间和带宽。
