# SpringBoot

官方文档https://docs.spring.io/spring-boot/redirect.html?page=using#using.build-systems.starters

## 快速入门

```shell
# clean后package执行springboot打包后的jar文件；
# 在外部修改springboot的配置文件：在.jar包下创建application.properties
java -jar xxxxxxx.jar
```

* maven创建项目 -> 引入springboot父程序依赖和相关依赖 -> 创建@SpringbootApplication指定的主程序 -> 创建业务 -> 利用继承springboot的maven打包插件 -> 运行java -jar xxxxxx.jar文件

> 自动配置：maven中导入了spring-boot-starter（springboot的场景启动器），starter中继续导入了spring-boot-autoconfigure包中导入了全场景的依赖包，但不是所有场景的依赖包都会被激活（导入哪个包就自动配置哪个依赖）。
>
> * @SpringBootApplication(scanBasePackages = "com.demo01")：启动器指定扫描指定包
> * application.properties中的所有配置都会被springboot绑定到**指定的类属性**中

---

**常用注解**：

* 组件注册（给ioc容器中注册自定义组件）
  * @Configuration：替代spring时代的xml配置文件，一般加在指定类上并且用@Bean将指定方法加入到ioc中。
  * @SpringBootConfiguration：由springboot定义的配置注解，和@Configuration作用一样
  * @Bean：将方法返回的对象加入到ioc中默认单实例、@Scope("prototype")：配置加入ioc的对象为多实例对象
  * @Import(xxxxxx.class)：给容器中导入指定的组件，组件名为全类名。一般用@Bean导入自定义方法返回的类对象，@Import用的比较少
  * @Controller、@Service、@Repository、@Component、@ComponentScans
* 条件注解@ConditionalOnXxx
  * @ConditionalOnClass：如果类路径中存在这个类，则触发指定行为
  * @ConditionalOnMissingClass：如果类路径中不存在这个类，则触发指定行为
  * @ConditionalOnBean：如果容器中存在这个Bean，则触发指定行为
  * @ConditionalOnMissingBean：如果容器中不存在这个Bean，则触发指定行为
* 属性绑定
  * @ConfigurationProperties(prefix="xxx")：声明组件和配置文件的哪些属性进行绑定
  * @EnableConfigurationProperties(User.class)：当user没有加入ioc中时手动开启自动配置属性绑定。默认将没有加组件注解的user加入到ioc容器中。一般用于导入三方写好的组件进行属性绑定
* 网络请求
  * @DateTimeFormat(pattern = "yyyy-MM-dd HH:mm:ss"：传递LocalDateTime类
  * @RequestParam(name = "age")：获取URL中的参数并绑定到函数对应名称的形参中
  * @PathVariable(name = "id")：动态将URL中的路径参数绑定到函数对应名称的形参中，当URL路径参数和形参变量名相同时可自动绑定
  * @RequestBody：将浏览器发送请求体中的JSON串动态绑定到实体对象中


> 简单参数（@RequestParam）、实体参数（@RequestParam）、数组集合参数（@RequestParam）、日期参数（@DateTimeFormate）、JSON参数（@RequestBody）、路径参数（@PathVariable）




## 其他

> Spring的作用：
>
> 1. IOC容器自动配置和管理Bean
> 2. AOP：实现面向切面编程，解耦重复代码
> 3. JDBC：实现声明式事务管理
> 4. Boot：实现SpringBoot简化配置和约定大于配置的项目开发
>
> 创建SpringBoot、Maven添加需要的依赖、设置配置文件、进入开发

* @Value
* @Bean
* @Import
* @RestController = @ResponseBody + @Controller
* @Controller、@Service、@Repository、@Configuration、@Component
* @Constraint
* @RestControllerAdvice = @ControllerAdvice+@ResponseBody
* @ExceptionHandler({Exception.class})
* @CrossOrigin
* @RequestParam
* @DateTimeFormate
* @RequestBody

---

**Bean的注册条件**

* @ConditionalOnProperty(prefix="类名",name={"属性名1","属性名2"})：配置文件中存在对应的属性，才声明该bean
* @ConditionalOnMissingBean(类名.class)：不存在当前类型的bean时，才声明该bean
* @ConditionalOnClass(name = "全类名")：当环境存在指定的这个类时，才声明该bean

**自动配置原理**

> @SpringApplication注解组合EnableAutoConfigration注解。EnableAutoConfiguration注解组合了Import注解，导入了AutoConfigurationSelector类。实现接口中的selectImports方法最终通过流读取META-INF目录下后缀名为imports的文件，读取文件中的全类名后加载自动配置。在springboot2.7以前读取spring.factories文件
