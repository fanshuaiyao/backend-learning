![image-20241107210523729](spring框架.assets/image-20241107210523729.png)



**BeanFactory是延迟加载，ApplicationContext是立即加载**

![image-20241107210654420](spring框架.assets/image-20241107210654420.png)

![image-20241107211308236](spring框架.assets/image-20241107211308236.png)

![image-20241107212822246](spring框架.assets/image-20241107212822246.png)

# spring bean的作用域

| **作用域**    | **实例数量**                | **生命周期**             | **典型场景**                |
| ------------- | --------------------------- | ------------------------ | --------------------------- |
| `singleton`   | 容器中唯一                  | 随容器的生命周期         | 无状态的服务 Bean           |
| `prototype`   | 每次获取一个新实例          | 调用方管理               | 有状态的对象，例如用户输入  |
| `request`     | 每次 HTTP 请求一个新实例    | 请求结束时销毁           | 每个请求相关的数据处理 Bean |
| `session`     | 每个会话一个实例            | 会话结束时销毁           | 会话级别的数据共享          |
| `application` | 全局共享一个实例            | Web 应用生命周期         | 全局共享配置                |
| `websocket`   | 每个 WebSocket 会话一个实例 | WebSocket 会话结束时销毁 | WebSocket 数据处理          |

![image-20241107213611971](spring框架.assets/image-20241107213611971.png)

![image-20241107213952384](spring框架.assets/image-20241107213952384.png)

![image-20241107215805839](spring框架.assets/image-20241107215805839.png)

面试：

问题：在对象创建完毕之后，想要进行一些初始化操作，怎么弄？
回答：有两种方法，一种是设置init-method，指定初始化方法。还有一个就是你这个bean实现initialiazingBean这个接口，重写afterPropertiesSet方法。执行顺序如下：

![image-20241107220057316](spring框架.assets/image-20241107220057316.png)

![image-20241107220143516](spring框架.assets/image-20241107220143516.png)

**bean的实例化有两种方法，构造方法和工厂模式**

![image-20241107221027725](spring框架.assets/image-20241107221027725.png)

**FactoryBean接口规范了方法，当你想要获取bean的时候，实现这个接口，重写方法getObject，这样我们在配置文件中就不用去指定方法啦，而且是懒加载，当加载配置文件创建spring容器的时候，他是不会创建bean的，getbean的时候才能创建bean，此时他会把实例存在一个cache中。其实就是立即创建工厂，提前存储好需求，然后再延迟创建对象。**

![image-20241108095301328](spring框架.assets/image-20241108095301328.png)

![image-20241108095317650](spring框架.assets/image-20241108095317650.png)

注入List，先使用set方法注入，以方便spring调用这个方法为你注入，配置文件中这样配置：

普通属性![image-20241108095649117](spring框架.assets/image-20241108095649117.png)

引用属性

![image-20241108095928526](spring框架.assets/image-20241108095928526.png)

![image-20241108100006639](spring框架.assets/image-20241108100006639.png)

map、set、properties都是类似的

![image-20241108100603522](spring框架.assets/image-20241108100603522.png)

![image-20241108101059455](spring框架.assets/image-20241108101059455.png)

**引入第三方标签：导入依赖，然后再设置命名空间**

![image-20241108105048691](spring框架.assets/image-20241108105048691.png)

# spring原理篇

![image-20241108111106075](spring框架.assets/image-20241108111106075.png)

**BeanDefinitionMap维护的东西**，BeanDefinition主要就是对bean标签的东西进行封装，存入BeanDefinitionMap。

![image-20241108112018896](spring框架.assets/image-20241108112018896.png)

![image-20241108151648440](spring框架.assets/image-20241108151648440.png)

![image-20241108151805289](spring框架.assets/image-20241108151805289.png)

## spring后处理器

![image-20241108151854246](spring框架.assets/image-20241108151854246.png)

![image-20241108152035251](spring框架.assets/image-20241108152035251.png)

**在BeanFactory填充完毕后调用该方法，可以直接对填充好的map中的bean定义进行修改，例如讲userservice改为userdao** ，使用的方法是Bean工厂后处理器

![image-20241108152827934](spring框架.assets/image-20241108152827934.png)

**不仅能改，还能动态的添加Beandifinition **

![image-20241108154855575](spring框架.assets/image-20241108154855575.png)

### 案例

![image-20241108160218472](spring框架.assets/image-20241108160218472.png)



**原理就是先扫描包中的哪个类在使用注解，将其存放起来，然后通过spring工厂后处理的BeanFactoryPostProcessor去把这个类搞成一个beanDefinition，在加入到beanDefinitionMap中，使得可以进一步创建这个bean的实例。**这个东西在bean对象创建之前，主要是操作beanDefinition的。下面这个bean后处理器是在beanDefinition变成bean对象之后，在存放到单例池之前对bean对象进行一些操作。

![image-20241108180011300](spring框架.assets/image-20241108180011300.png)

![image-20241109214015459](spring框架.assets/image-20241109214015459.png)

# bean的生命周期

![image-20241109214200479](spring框架.assets/image-20241109214200479.png)

![image-20241109214241494](spring框架.assets/image-20241109214241494.png)

注入的信息也会封装到beanDefinition中

![image-20241109215255964](spring框架.assets/image-20241109215255964.png)

![image-20241109215409695](spring框架.assets/image-20241109215409695.png)

单向注入的时候，当注入对象不存在时，先去创建被注入的对象，再回头注入。

## 循环依赖或者循环引用的问题

![image-20241109220403906](spring框架.assets/image-20241109220403906.png)

![image-20241109220639022](spring框架.assets/image-20241109220639022.png)

如何解决？

创建好的bean是一个半成品，先存到一个另外一个map，不存单例池。但时候先去单例池找，找不到去哪个map找哪个半成品，给他引用就行。

spirng使用**三级缓存来解决循环依赖/循环引用这个问题的**。

![image-20241109221048199](spring框架.assets/image-20241109221048199.png)

**bean实例化后哪个半成品直接扔到三级缓存，等着别人引用。且是包装的**

二级缓存存放被被人引用的bean

一级缓存存放已经创建好的bean

**不同级别的缓存是存储不同阶段的bean**

![image-20241109222126630](spring框架.assets/image-20241109222126630.png)

![image-20241109222340278](spring框架.assets/image-20241109222340278.png)

![image-20241109222547396](spring框架.assets/image-20241109222547396.png)

# spring IoC 整体流程图

![image-20241109222800742](spring框架.assets/image-20241109222800742.png)

ioc容器通过BeanDefinitionRead读取bean标签，把bean标签的信息封装成一个BeanDefinition，这个对象是标签信息对象。这些对象存储在一个BeanDefinitionMap中，sprin给会遍历里面的每一个BeanDefinition对象，取出来里面的信息，比如类名，通过反射创建对象。再把这些对象存放到一个单例池中。更详细的讲，再将BeanDefinition对象存储到BeanDefinitonMap中后，要进行后处理，进行BeanFactoryPostProcessor，在这里我们可以对某个BeanDefinition进行修改，或者注册一个BeanDefinition，起到一个扩展的作用。之后就是将BeanDefinition转成bean实例对象。再存入单例池之前还需要经过一个bean后处理。也就是beanPostProcessor，一个befor和after，在这里我们可以在这里做一些扩展，比如进行一些对象增强，例如AOP就是在这完成的。最后就是存放到单例池之中了。

而bean的生命周期也就是从BenaDefinition转成Bean实例对象开始的，先是实例化对象、初始化对象、完成阶段、使用阶段、销毁阶段。其中最主要的就是初始化阶段，在其中完成了一些属性注入、init方法调用、和后处理方法的调用。

# spring注解

![image-20241109230629134](spring框架.assets/image-20241109230629134.png)

![image-20241110134449611](spring框架.assets/image-20241110134449611.png)

![image-20241110134754877](spring框架.assets/image-20241110134754877.png)

## @component注解的衍生

![image-20241110134855539](spring框架.assets/image-20241110134855539.png)

## 依赖注入

![image-20241110135118793](spring框架.assets/image-20241110135118793.png)

```java
@Autowired  
private UserDao userDao;

//根据类型注入,如果有多个类型一样的bean，就根据名字来找，就是注入之下的这个名字，如果名字也找不到就报错。
```

如果使用Autowired还非得想使用名字匹配，就得配合**@Qualifier**使用，作用是根据名称注入相应的bean

```
@Resource  默认情况下@Resource按照名称注入，如果没有显式声明名称则按照类型进行注入
```

## 非自定义bean注解

![image-20241110141633109](spring框架.assets/image-20241110141633109.png)

由于@bean在方法上，所以必须让这个方法的类让spring管理，所以这个类必须加@component，让spring管理。

![image-20241110142728119](spring框架.assets/image-20241110142728119.png)

## bean的配置类注解

**使用配置类取缔配置文件**

![image-20241110144135113](spring框架.assets/image-20241110144135113.png)

![image-20241110144147799](spring框架.assets/image-20241110144147799.png)

![image-20241110144202254](spring框架.assets/image-20241110144202254.png)

## spring其他注解

![image-20241110144300987](spring框架.assets/image-20241110144300987.png)

![image-20241110144458979](spring框架.assets/image-20241110144458979.png)

## Spring注解的解析原理

**@Component注解的原理**

![image-20241110145058245](spring框架.assets/image-20241110145058245.png)

* xml扫描的注解：bean是直接注册到BeanDefinitionMap中，并没有用到beanFactoryPostProcessor 这个后处理拓展点，他是在spring容器创建时就直接扫描到，注册到map中。
* 注解方式的扫描：这种方式是通过**bean工厂的后处理**方式和**bean后处理**来进行注册到BeanDefinitionMap中。component是在形成对象之前的哪个bean工厂处理器进行处理，注入到map中。（@Autowired 是注入属性，所以在形成对象之后，存到单例池之前的那个beanpost后处理器来进行属性注入。）

两种方式地层用的扫描注解的方法是一样的

![image-20241110151524741](spring框架.assets/image-20241110151524741.png)

# AOP

![image-20241110153403746](spring框架.assets/image-20241110153403746.png)

## AOP思想

![image-20241110153516212](spring框架.assets/image-20241110153516212.png)

![image-20241110153635556](spring框架.assets/image-20241110153635556.png)

**对方法进行横向抽取**，A为目标，B为增强。

模拟代码中，我们需要准备一个目标对象，一个增强对象，一个友目标对像通过BeanPostProcessor后处理器得到的代理对象。

![image-20241110171249932](spring框架.assets/image-20241110171249932.png)

切面：通知/增强  ＋ 切点

![image-20241110171538770](spring框架.assets/image-20241110171538770.png)

![image-20241110171700910](spring框架.assets/image-20241110171700910.png)

 

## springAop

![image-20241110174258038](spring框架.assets/image-20241110174258038.png)

## 动态代理

![image-20241110193520283](spring框架.assets/image-20241110193520283.png)

​    ![image-20241110193959007](spring框架.assets/image-20241110193959007.png)

## 基于注解配置的Aop

还是通过注解更换xml标签

![image-20241110200228449](spring框架.assets/image-20241110200228449.png)

![image-20241110203730771](spring框架.assets/image-20241110203730771.png)

# Spring 整和WEB

![image-20241113204302638](spring框架.assets/image-20241113204302638.png)

在服务器启动的时候执行一些操作可以再servlet的init方法中或者，在服务器启动时，servletContext对象就会创建，有一个Listener监听器在监听它，他一创建就会执行监听器的方法，逻辑代码可以写到哪里去。

![image-20241113221355027](spring框架.assets/image-20241113221355027.png)

前端控制器有默认的组件，如果你没有配置就用默认的组件。

## spring MVC的请求处理

![image-20241113222137184](spring框架.assets/image-20241113222137184.png)

@RequestMapping是映射路径来用的。放在类上访问路径会多一个层级。

## mvc接受数据

当参数不匹配时需要手动映射：username --> name

`http://localhost/param?username=zs&age=18`

![image-20241113225159928](spring框架.assets/image-20241113225159928.png)

接受多个参数：可以用数组

![image-20241113230148045](spring框架.assets/image-20241113230148045.png)

![image-20241113230322630](spring框架.assets/image-20241113230322630.png)

springmvc框架自动封装参数为对象，底层使用反射（set方法）

![image-20241113230602285](spring框架.assets/image-20241113230602285.png)

## SpringMVC 拦截器

![image-20241114135144384](spring框架.assets/image-20241114135144384.png)

filter是web工程的东西，只要是一个web工程就一定会有filter，而拦截器不同，他是sprinfMVC中的东西。

![image-20241114135408205](spring框架.assets/image-20241114135408205.png)

![image-20241114185424176](spring框架.assets/image-20241114185424176.png)

![image-20241114185537487](spring框架.assets/image-20241114185537487.png)

