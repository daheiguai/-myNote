## 1.udp接受数据

```py
#coding=utf-8

from socket import *

# 1. 创建udp套接字
udp_socket = socket(AF_INET, SOCK_DGRAM)

# 2. 准备接收方的地址# '192.168.1.103'表示目的ip地址# 8080表示目的端口

dest_addr = ('192.168.1.103', 8080) # 注意 是元组，ip是字符串，端口是数字

# 3. 从键盘获取数据

send_data = input("请输入要发送的数据:")

# 4. 发送数据到指定的电脑上的指定程序中

#发送的是二进制 byte 类型

#str要encode 或者 udp_socket.sendto(b"哈哈哈", dest_addr)

udp_socket.sendto(send_data.encode('utf-8'), dest_addr)

# 5. 关闭套接字

udp_socket.close()
```

## 2. udp网络程序-发送、接收数据

```py
#coding=utf-8

from socket import *

# 1. 创建udp套接字

udp_socket = socket(AF_INET, SOCK_DGRAM)

# 2. 准备接收方的地址

dest_addr = ('192.168.236.129', 8080)

# 3. 从键盘获取数据

send_data = input("请输入要发送的数据:")

# 4. 发送数据到指定的电脑上

udp_socket.sendto(send_data.encode('utf-8'), dest_addr)

# 5. 等待接收对方发送的数据

recv_data = udp_socket.recvfrom(1024) # 1024表示本次接收的最大字节数

# 6. 显示对方发送的数据# 接收到的数据recv_data是一个元组# 第1个元素是对方发送的数据# 第2个元素是对方的ip和端口

#传过来的byte类型 要 decode windows输入的字一般是gbk

print(recv_data[0].decode('gbk'))

print(recv_data[1])

# 7. 关闭套接字

udp_socket.close()
```

## 3. udp绑定端口

```py
#coding=utf-8

from socket import *

# 1. 创建套接字

udp_socket = socket(AF_INET, SOCK_DGRAM)

# 2. 绑定本地的相关信息，如果一个网络程序不绑定，则系统会随机分配

local_addr = ('', 7788) # ip地址和端口号，ip一般不用写，表示本机的任何一个ip

udp_socket.bind(local_addr)

# 3. 等待接收对方发送的数据

recv_data = udp_socket.recvfrom(1024) # 1024表示本次接收的最大字节数

# 4. 显示接收到的数据

print(recv_data[0].decode('gbk'))

# 5. 关闭套接字

udp_socket.close()
```

#### 4、关于qq

  QQ不绑定端口  绑定的话  登陆多个QQ时     只能发送到一个进程。 所以 不同的  QQ在 电脑 登陆  会获得不同的  端口。QQ依靠的是腾讯服务器   转发消息  到指定的 QQ ，QQ客户端在  登陆 时  会把自己的 随机端口 告诉  服务器。如果  没有 服务器  端和端 的通讯必须绑定端口


