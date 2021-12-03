# pyinstaller

## 操作

cmd cd 到模块所在目录

常用命令和参数

```
cmd:  pyinstaller -w -D main.py   加-w不要黑窗口  -D默认生成exe和资源目录
cmd:  pyinstaller -w -F main.py   -F没有任何目录结构 全部打包成exe
```

黑窗口的作用

- 当双击exe报错时会闪退，可以把exe拖到cmd中，就能查看错误信息

打包exe后找不到第三方库

```
pyinstaller example.py -F -p C:/python/lib/site-packages
```

## 参数大全

通用参数

| 参数名           | 描述                                                     | 说明                                                         |
| ---------------- | -------------------------------------------------------- | ------------------------------------------------------------ |
| -h               | 显示帮助                                                 | 无                                                           |
| -v               | 显示版本号                                               | 无                                                           |
| –distpath        | 生成文件放在哪里                                         | 默认：当前目录的dist文件夹内                                 |
| –workpath        | 生成过程中的中间文件放在哪里                             | 默认：当前目录的build文件夹内                                |
| -y               | 如果dist文件夹内已经存在生成文件，则不询问用户，直接覆盖 | 默认：询问是否覆盖                                           |
| –upx-dir UPX_DIR | 指定upx工具的目录                                        | 默认：execution path                                         |
| -a               | 不包含unicode支持                                        | 默认：尽可能支持unicode                                      |
| –clean           | 在本次编译开始时，清空上一次编译生成的各种文件           | 默认：不清除                                                 |
| –log-level LEVEL | 控制编译时pyi打印的信息                                  | 一共有6个等级，由低到高分别为TRACE DEBUG INFO(默认) WARN ERROR CRITICAL。也就是默认清空下，不打印TRACE和DEBUG信息 |

与生成结果有关的参数

| 参数名    | 描述                          | 说明                                                         |
| --------- | ----------------------------- | ------------------------------------------------------------ |
| -D        | 生成one-folder的程序（默认）  | 生成结果是一个目录，各种第三方依赖、资源和exe同时存储在该目录 |
| -F        | 生成one-file的程序            | 生成结果是一个exe文件，所有的第三方依赖、资源和代码均被打包进该exe内 |
| –specpath | 指定.spec文件的存储路径       | 默认：当前目录                                               |
| -n        | 生成的.exe文件和.spec的文件名 | 默认：用户脚本的名称，即main.py和main.spec                   |

指定打包哪些资源、代码

| 参数名                | 描述                                       | 说明                                                         |
| --------------------- | ------------------------------------------ | ------------------------------------------------------------ |
| –add-data             | 打包额外资源                               | 用法：pyinstaller [main.py](http://main.py/) --add-data=src;dest。windows以;分割，linux以:分割 |
| –add-binary           | 打包额外的代码                             | 用法：同–add-data。与–add-data不同的是，用binary添加的文件，pyi会分析它引用的文件并把它们一同添加进来 |
| -p                    | 指定额外的import路径，类似于使用PYTHONPATH | 参见PYTHONPATH                                               |
| –hidden-import        | 打包额外py库                               | pyi在分析过程中，有些import没有正确分析出来，运行时会报import error，这时可以使用该参数 |
| –additional-hooks-dir | 指定用户的hook目录                         | hook用法参见其他，系统hook在PyInstaller\hooks目录下          |
| –runtime-hook         | 指定用户runtime-hook                       | 如果设置了此参数，则runtime-hook会在运行main.py之前被运行    |
| –exclude-module       | 需要排除的module                           | pyi会分析出很多相互关联的库，但是某些库对用户来说是没用的，可以用这个参数排除这些库，有助于减少生成文件的大小 |
| –key                  | pyi会存储字节码，指定加密字节码的key       | 16位的字符串                                                 |



Windows和Mac特有的参数

| 参数名 | 描述               | 说明                                                 |
| ------ | ------------------ | ---------------------------------------------------- |
| -c     | 显示命令行窗口     | 与-w相反，默认含有此参数                             |
| -w     | 不显示命令行窗口   | 编写GUI程序时使用此参数有用。                        |
| -i     | 为main.exe指定图标 | pyinstaller -i beauty.ico [main.py](http://main.py/) |

生成参数

| 参数名 | 描述                                                 | 说明                              |
| ------ | ---------------------------------------------------- | --------------------------------- |
| -d     | 执行生成的main.exe时，会输出pyi的一些log，有助于查错 | 默认：不输出pyi的log              |
| -s     | 优化符号表                                           | 原文明确表示不建议在windows上使用 |
| –noupx | 强制不使用upx                                        | 默认：尽可能使用。                |

其他

| 参数名          | 描述                 | 说明                   |
| --------------- | -------------------- | ---------------------- |
| –runtime-tmpdir | 指定运行时的临时目录 | 默认：使用系统临时目录 |

## 之前的理解



py  转 exe

1 .安装pyinstaller   cmd 输入 pip install pyinstaller

2.在cmd   cd到项目文件目录

3.cmd 命令    pyi-makespec main.py    main.py是主程序 

4.得到一个main.spec文件，打开编辑

5.a = Analysis(['MTLGUI01.py','MTL.py','MTLGUI02.py','User_Agent_list.py'],            这里是其他自己写的依赖文件，py结尾  第三方包不需要

             pathex=['E:\\Python\\MyPy\\exeForMTL'],

             binaries=[],

             datas=[('E:\Python\MyPy\exeForMTL','exeForMTL')],            这里写资源  其他非py，如图片文档等  第一项是路径  第二项是放东西的                                                                                                                    文件夹名   必须和路径的一样（可以写多个）

             hiddenimports=[],

             hookspath=[],

             runtime_hooks=[],

             excludes=[],

             win_no_prefer_redirects=False,

             win_private_assemblies=False,

             cipher=block_cipher,

             noarchive=False)

6.保存 执行cmd  pyinstaller -D main.py

7.东西会出现在dist文件夹里

8.双击exe程序报错一闪而过  用手机录像截图看错误信息，此时把文件拖进cmd窗口是能正常运行  但是双击会报错

9.错误信息是   img不存在src地址目录

10.原因分析  我文件地址用的是绝对路径       self.qkl = Image.open("巧克力.jpg")

        这个图片已经导入好了（因为可以在cmd窗口拖进去运行并且看到图片），理论上资源已经存在了   但是py代码执行过程会因为出错而停止运行

11.解决方法   （1）我吧巧克力图片放到exe的相对路径下 ，解决了

                        （2）思路   吧img 在代码中改为 绝对路径  但是感觉不够顶  因为你生成exe就是为了在别的电脑运行 他的电脑跟你绝对路径不一样

                    （3）思路 我还没试过  吧相对路径代码 用try  cach包起来  不管异常  应该也能正常运行------>这个方法试了  能正常运行，但真的没                            有图片

        最后建议  手动创建个img文件夹  改代码  把图片放在img文件夹里

其他 补充    上面的方法双击程序会自带一个命令行窗口  cmd  pyinstaller -w -D main.py  加上一个-w可以让那个窗口消失  但是那个窗口是报错窗口 所以确认没有错误以后再  重新生成吧   -D换成-F就去掉多余的依赖  只有exe和图片

关于更改exe图标
