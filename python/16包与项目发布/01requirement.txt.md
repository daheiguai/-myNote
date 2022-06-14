如何生成 requirements.txt 
进入项目根目录，执行以下命令

pip3 freeze > requirements.txt

这一会报一个 warning


意思是：最好通过 python 去调用 pip，所以建议敲下面的命令去生成

python.exe -m pip freeze > requirements.txt

使用 requirement.txt 安装第三方库
pip3 install -r requirement.txt
