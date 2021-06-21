# git常用

---

## 账户登录和SSH秘钥生成

---

## 下载master文件

git clone  https://github.com/CKTim/BlueTooth.git

（https://github.com/CKTim/BlueTooth.git替换成你之前复制的地址）

---

## 上传

#### 2、进入master目录  右键git bush here

#### 3、git add .        （注：别忘记后面的.，此操作是把Test文件夹下面的文件都添加进来）

#### 4、git commit  -m  "提交信息"  （注：“提交信息”里面换成你需要，如“first commit”）

#### 5、git push -u origin master   （注：此操作目的是把本地仓库push到github上面，此步骤需要你输入帐号和密码）



#### 强制push本地仓库到远程 (这种情况不会进行merge, 强制push后远程文件可能会丢失 不建议使用此方法) $ git push -u origin master -f

