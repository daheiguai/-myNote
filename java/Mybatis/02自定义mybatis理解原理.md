# 自定义mybatis

现在我要面试，缺项目，急切要学会用，然后写个项目出来。没时间自己理解mybatis再写一个出来。而且，就算面试要问mybatis的底层原理，我可以专门看mybatis底层原理的资料，这篇不写了。

## 一、把mybatis依赖删除

在pom.xml中吧mybatis的依赖删除，我们要自己写个mybatis

![](/img/02_自定义mybatis0.png)

我们会自己实现测试类中调用的方法

```java
public class MybatisTest {
    public static void main(String[] args) throws IOException {
//        读取配置文件
        InputStream in = Resources.getResourceAsStream("SqlMapConfig.xml");
//        创建SqlSessionFactory工厂
        SqlSessionFactoryBuilder builder = new SqlSessionFactoryBuilder();
        SqlSessionFactory factory = builder.build(in);
//        使用工厂生产SqlSession对象
        SqlSession session = factory.openSession();
//        使用SqlSession创建dao接口的代理对象
        UserDao userDao = session.getMapper(UserDao.class);
//        使用d代理对象执行方法
        List<User> users = userDao.findAll();
        for (User user:users){
            System.out.println(user);
        }
//        释放资源
        session.close();
        in.close();
    }
}
```

## 二、Resources

![](/img/02_自定义mybatis1.png)

创建目录，写入resources如下

```java
public class Resources {

    /**
     * 根据传入的参数，获取一个字节输入流
     * @param filePath
     * @return
     */
    public static InputStream getResourceAsStream(String filePath){
        return Resources.class.getClassLoader().getResourceAsStream(filePath);
    }
}
```

## 三、SqlSession

