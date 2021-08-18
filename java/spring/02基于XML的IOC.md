- IOC—Inversion of Control，即“控制反转”，不是什么技术，而是一种设计思想。在Java开发中，Ioc意味着将你设计好的对象交给容器控制，而不是传统的在你的对象内部直接控制。

-  DI—Dependency Injection，即“依赖注入”：是组件之间依赖关系由容器在运行期决定，形象的说，即由容器动态的将某个依赖关系注入到组件之中。依赖注入的目的并非为软件系统带来更多功能，而是为了提升组件重用的频率，并为系统搭建一个灵活、可扩展的平台。通过依赖注入机制，我们只需要通过简单的配置，而无需任何代码就可指定目标需要的资源，完成自身的业务逻辑，而不需要关心具体的资源来自何处，由谁实现。

## 一、创建bean

#### 1.使用无参默认构造函数创建

```xml
<bean id="accountService" class="com.aaa.service.impl.AccountServiceImpl"></bean>
```

若无默认构造函数则创建失败，如

```java
public AccountServiceImpl(String str){
        System.out.println("1");
}
```

#### 2.使用普通工厂中的方法创建对象

```xml
<bean id="instanceFactory" class="com.aaa.factory.InstanceFactory"></bean>
<bean id="accountService" factory-bean="instanceFactory" factory-method="getAccountService"></bean>
```

使用InstanceFactory类中的方法返回值创建对象

```java
public class InstanceFactory {
    public AccountService getAccountService(){
        return new AccountServiceImpl();
    }
}
```

#### 3.使用工厂中的静态方法创建对象

```xml
    <bean id="accountService" class="com.aaa.factory.StaticFactory" factory-method="getAccountService"></bean>
```

## 二、bean作用范围

通过scope来设置作用范围

```xml
<bean id="..." class="..." scope="singleton"></bean>
```

1、Singleton:单例的，是默认值（每次new返回同一个对象）

2、Prototype:多例的，这样两次获取的对象不是同一个。

3、Request:作用于web应用的请求范围

4、Session: 作用于web应用的会话范围

5、Global-session: 作用于集群环境的会话范围（全局会话范围），当不是集群环境时，它就是session

## 三、bean生命周期（重点面试题之后细索）

## 四、注入依赖

```xml
<!-- spring中的依赖注入
        依赖注入：
            Dependency Injection
        IOC的作用：
            降低程序间的耦合（依赖关系）
        依赖关系的管理：
            以后都交给spring来维护
        在当前类需要用到其他类的对象，由spring为我们提供，我们只需要在配置文件中说明
        依赖关系的维护：
            就称之为依赖注入。
         依赖注入：
            能注入的数据：有三类
                基本类型和String
                其他bean类型（在配置文件中或者注解配置过的bean）
                复杂类型/集合类型
             注入的方式：有三种
                第一种：使用构造函数提供
                第二种：使用set方法提供
                第三种：使用注解提供（明天的内容）
     -->


    <!--构造函数注入：
        使用的标签:constructor-arg
        标签出现的位置：bean标签的内部
        标签中的属性
            type：用于指定要注入的数据的数据类型，该数据类型也是构造函数中某个或某些参数的类型
            index：用于指定要注入的数据给构造函数中指定索引位置的参数赋值。索引的位置是从0开始
            name：用于指定给构造函数中指定名称的参数赋值   常用的
            =============以上三个用于指定给构造函数中哪个参数赋值===============================
            value：用于提供基本类型和String类型的数据
            ref：用于指定其他的bean类型数据。它指的就是在spring的Ioc核心容器中出现过的bean对象

        优势：
            在获取bean对象时，注入数据是必须的操作，否则对象无法创建成功。
        弊端：
            改变了bean对象的实例化方式，使我们在创建对象时，如果用不到这些数据，也必须提供。
    -->
    <bean id="accountService" class="com.itheima.service.impl.AccountServiceImpl">
        <constructor-arg name="name" value="泰斯特"></constructor-arg>
        <constructor-arg name="age" value="18"></constructor-arg>
        <constructor-arg name="birthday" ref="now"></constructor-arg>
    </bean>

    <!-- 配置一个日期对象 -->
    <bean id="now" class="java.util.Date"></bean>



    <!-- set方法注入                更常用的方式
        涉及的标签：property
        出现的位置：bean标签的内部
        标签的属性
            name：用于指定注入时所调用的set方法名称
            value：用于提供基本类型和String类型的数据
            ref：用于指定其他的bean类型数据。它指的就是在spring的Ioc核心容器中出现过的bean对象
        优势：
            创建对象时没有明确的限制，可以直接使用默认构造函数
        弊端：
            如果有某个成员必须有值，则获取对象是有可能set方法没有执行。
    -->
    <bean id="accountService2" class="com.itheima.service.impl.AccountServiceImpl2">
        <property name="name" value="TEST" ></property>
        <property name="age" value="21"></property>
        <property name="birthday" ref="now"></property>
    </bean>


    <!-- 复杂类型的注入/集合类型的注入
        用于给List结构集合注入的标签：
            list array set
        用于个Map结构集合注入的标签:
            map  props
        结构相同，标签可以互换，所以只要记住一个list和一个map就行了
    -->
    <bean id="accountService3" class="com.itheima.service.impl.AccountServiceImpl3">
        <property name="myStrs">
            <set>
                <value>AAA</value>
                <value>BBB</value>
                <value>CCC</value>
            </set>
        </property>

        <property name="myList">
            <array>
                <value>AAA</value>
                <value>BBB</value>
                <value>CCC</value>
            </array>
        </property>

        <property name="mySet">
            <list>
                <value>AAA</value>
                <value>BBB</value>
                <value>CCC</value>
            </list>
        </property>

        <property name="myMap">
            <props>
                <prop key="testC">ccc</prop>
                <prop key="testD">ddd</prop>
            </props>
        </property>

        <property name="myProps">
            <map>
                <entry key="testA" value="aaa"></entry>
                <entry key="testB">
                    <value>BBB</value>
                </entry>
            </map>
        </property>
    </bean>
```

