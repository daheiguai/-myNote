## Django项目中创建APP

#### 1.创建APP

pycharm控制台输入

    python manage.py startapp 应用名

#### 2.注册APP（关联项目和APP）

项目名文件夹 中 的 settings.py

```py
# Application definition

INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    'magicbox', #创建的app   上面的是默认的，不必改动
]
```

---

## 运行服务器

    python manage.py runserver

---

## 连接数据库

项目名文件夹 中 的 settings.py

```py
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.mysql',
        'NAME': '',  # 数据库名,必须手动创建
        'USER': '',  # 账号
        'PASSWORD': '',  # 密码
        'HOST': '127.0.0.1',  # IP(本地地址也可以是localhost)
        'PORT': '3306',  # 端口
    }
}
```

#### 1.mysql数据库中创建定义的数据库

启动mysql

    mysqld --console　

a. 进入mysql

```undefined
mysql -u root -p
```

b. 创建数据库

```undefined
create database xxx charset=utf-8;
```

#### 2.配置数据库链接

a. 安装pymysql

```undefined
pip install pymysql
```

b. 在工程目录下的**init**.py文件中输入,完成数据库的驱动加载

```swift
import pymysql
pymysql.install_as_MySQLdb()
```
