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
