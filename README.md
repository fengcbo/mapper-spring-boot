通用mapper是mybatis的一个插件，它可以极大的方便开发人员。可以随意的按照自己的需要选择通用方法，还可以很方便的开发自己的通用方法。
使用方式请参考官方文档。官方github地址：http://git.oschina.net/free/Mapper
***
本人原来项目中使用spring整合通用mapper一直未发现大的问题，最近项目引入spring boot，整合通用mapper时使用的是通用mapper作者提供的starter，使用过程中遇到了一个大坑。
```
在Configuration中使用mapper时，会遇到通用mapper为初始化而无法使用通用mapper提供的方法问题。
```
***

这个问题也曾在github上询问作者，但作者也未曾给出解决方案。为了解决这个问题，本人查阅了很多资料，发现问题出现在 Configuration 和 AutoConfiguration 的加载顺序上。
后来又仔细阅读了作者实现starter的方式，发现作者在 **MapperAutoConfiguration** 使用 **PostConstruct** 来实现通用mapper的初始化，由于一般情况下 Configuration 的加载会早于 AutoConfiguration 的加载，
所以在 Configuration 中无法使用通用mapper的方法。
```
1. 关于 Configuration 和 AutoConfiguration的加载顺序请参考：https://github.com/chanjarster/spring-boot-all-callbacks
2. 作者的另一个开源项目的start(https://github.com/pagehelper/pagehelper-spring-boot)也有这个问题
```

***

经过长时间的研究发现在作者的starter上无法(也许是本人才疏学浅，如有解决方案，还请大神指点)解决上面的问题，所以决定重写通用mapper的starter。
使用方式和作者的starter相同，只是MapperScan不在使用mybatis提供的org.mybatis.spring.annotation.MapperScan，而是使用自己定义的com.ourziu.mybatis.autoconfigure.MapperScan
```java
@SpringBootApplication
@EnableWebSecurity
@EnableGlobalMethodSecurity(prePostEnabled = true)
@MapperScan("com.ourziu.admin.mapper")
public class AdminApplication {

    public static void main(String[] args) {
        SpringApplication.run(AdminApplication.class, args);
    }
}
```

如果有问题请发邮件给我： chuanbo.feng@gmail.com