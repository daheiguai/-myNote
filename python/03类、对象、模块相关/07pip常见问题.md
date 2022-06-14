pycharm安装库到虚拟环境

```
cd ./venv/Scripts>activate
.....
pip install xxx
```

当pip失效时，可以试试

```
python -m pip install xxx
```

跟新pip

```
pip install --upgrade pip
```

当找不到PIP时，用python确保pip

```
python -m ensurepip
python -m pip install --upgrade pip
```

pip临时换源

```
pip install -i https://pypi.tuna.tsinghua.edu.cn/simple some-package
```





