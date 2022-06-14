

---

# 配置源

## yum源与更新

1. 进入yum源目录
   ```
   cd /etc/yum.repos.d
   ```

  ls查看当前yum源目录里的文件：

2. 将“CentOS-Base.repo”重命名备份为“CentOS-Base.repo.old”
   ```
   mv CentOS-Base.repo CentOS-Base.repo.old
   ```

3. 从阿里云下载yum源
   ```
   wget http://mirrors.aliyun.com/repo/Centos-7.repo
   ```

4. 将新的源改名为“CentOS-Base.repo”
   ```
   mv Centos-7.repo CentOS-Base.repo
   ```

5. 让yum重新缓存新的数据
   ```
   yum clean all
   yum makecache
   yum update -y
   ```

## Centos7安装常用的东西

### 安装nodejs

1. 如果此步骤完成后，node -v 和 npm -v都有了，则安装成功

   ```
   yum install -y nodejs
   ```

2. 如果只有node -v :

   ```
   yum install -y npm
   ```

3. 安装cnpm（如有需要）

   ```
   npm install cnpm -g --registry=https://registry.npm.taobao.org
   ```

### 安装git

```
yum -y install git
```

### 安装并运行redis

```
yum -y install redis && redis-server --daemonize yes
```



## Ubuntu源更新

    apt-get update
    
    apt-get upgrade

---

# sudo

#### 1.语句前加上sudo 就是以管理员身份执行

#### 2.su - root 切换成root模式

    su - root
    
    变成普通模式
    
    su - daheiguai(普通用户名)

上面切换如果出现鉴定故障

     su
    
    密码：
    
    su: 鉴定故障

就要改密码，可以和改成和原来一样，但是要经过改密码的过程

    sudo passwd root

#### 3.在进入系统时用root 用户名登陆   可以直接文件管理器各种操作

#### 4.把目录下某文件的管理权限转让给某个用户

    chown daheiguai find.txt

---

# 基本文件夹操作

#### 显示当前目录下所有文件

    ls

#### 目录切换

    回到主目录
    
    cd
    
    到下级目录
    
    cd /目录名/目录名 

---

# 文件创建删除

#### 创建文件夹

    mkdir 文件夹名

#### 创建文件

    touch test.c

---

#### 重命名

```
mv A B
```

#### 删除

```
rmdir MyDocuments
```

如果目录非空会报错，用下面这个

```
rm -rf MyDocuments/
```



<<<<<<< HEAD
# 移动复制



=======
>>>>>>> 4c1daf1517bc0b775688e358fe8f0767efb10fcb
# vim 基本操作

#### 打开文件

    vim 文件名
    
    vim /文件夹名/文件夹名/文件名

#### 编辑

按下 i 键 

左下角显示【插入】可以正常编辑文件

按下insert 左下角显示【替换】  就是换字

编辑好之后 按下 esc

然后按下  "  : "冒号 出现输入框 输入wq保存并退出  输入q！退出不保存

# 命令行输出后台进行到日志



命令1（记录所有日志）： nohup python -u tornado_server.py > test.log 2>&1 &
命令2（只记录错误日志）：nohup python -u tornado_server.py >/dev/null 2>error.log 2>&1 &
命令3（不记录任何日志）：nohup python -u tornado_server.py >/dev/null 2>&1 &

一、命令解释：
nohup 不挂断的意思
python tornado_server.py tornado服务的启动脚本

-u 代表程序不启用缓存，也就是把输出直接放到log中，没这个参数的话，log文件的生成会有延迟

p.log 将输出日志保存到这个log中

> 2>1 2与> 结合代表错误重定向，而1则代表错误重定向到一个文件1，而不代表标准输出；
>
> 2>&1 换成2>&1，&与1结合就代表标准输出了，就变成错误重定向到标准输出.
>
> & 最后一个& ，代表该命令在后台执行

PS: 命令运行后显示进程号，还有使用了nohup之后，很多人就这样不管了，其实这样有可能在当前账户非正常退出或者结束的时候，命令还是自己结束了。所以在使用nohup命令后台运行命令之后，需要使用exit正常退出当前账户，这样才能保证命令一直在后台运行。

[1]   2880
二、 其它相关命令
查看 nohub 命令下运行的所有后台进程：jobs

查看后台运行的所有进程：ps -aux

查看后台运行的所有python 进程：

```
ps aux |grep python
#或者
ps -ef | grep python
```

停止进程 kill -9 [进程id]

# SSR脚本

```
wget -N --no-check-certificate https://raw.githubusercontent.com/ToyoDAdoubi/doubi/master/ssr.sh && chmod +x ssr.sh && bash ssr.sh
```

