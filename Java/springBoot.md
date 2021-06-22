## SpringBoot的自动装配

微服务架构



---



狂神说笔记

https://mp.weixin.qq.com/s?__biz=Mzg2NTAzMTExNg%3D%3D&chksm=ce6107fcf9168eeaa5381228dad0e888ffc03401bc51e4bc7637bd46604b8e6e468cc8b43956&idx=1&mid=2247483743&scene=21&sn=431a5acfb0e5d6898d59c6a4cb6389e7#wechat_redirect

---



### 自动配置原理

pom.xml

- spring-boot-dependencies:核心依赖在父工程中
- 我们在写或者引入一些SpringBoot依赖的时候，不需要指定版本，就因为有这些版本仓库

启动器

-

```
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter</artifactId>
        </dependency>
```



- 启动器;说白了就是启动场景，比如spring-boot-starter-web,他就会帮我们自动导入web环境所有的依赖
- springboot会将所有的功能场景，都变成一个个启动器
- 我们需要使用什么功能，找到对应的启动器即可

主程序

![image-20210517171407757](springBoot.assets/image-20210517171407757.png)

注解：

SpringBoot下的所有资源都要被

```java
@SpringBootConfiguration:springboot的配置
  -@Cnfiguration:Spring配置类
  -@Component:说明这也是一个Spring的组件
@EnableAutoConfiguration：自动配置
  -@AutoConfigurationPackage：自动配置包
  		-@Import(AutoConfigurationPackages.Registrar.class) 自动配置包注册
  -@Import(AutoConfigurationImportSelector.class)：自动配置导入选择
  
  //获取所有的配置
List<String> configurations = getCandidateConfigurations(annotationMetadata, attributes);

	protected List<String> getCandidateConfigurations(AnnotationMetadata metadata, AnnotationAttributes attributes) {
		List<String> configurations = SpringFactoriesLoader.loadFactoryNames(getSpringFactoriesLoaderFactoryClass(),
				getBeanClassLoader());
		Assert.notEmpty(configurations, "No auto configuration classes found in META-INF/spring.factories. If you "
				+ "are using a custom packaging, make sure that file is correct.");
		return configurations;
	}

```

```
META-INF/spring.factories:自动配置的核心文件

Properties properties = PropertiesLoaderUtils.loadProperties(resource);
@ConditionalOnXXX:如果这里面的条件都生效，才会导入包
```

总结：SpringBoot所有自动配置都是在启动的时候扫描并加载;Spring.factory所有的自动配置类都在这里面，但是不一定生效，要判断条件是否成立，只要导入了对应的start,就有对应的启动器，有了启动器，我们自动装配就会失效，然后就配置成功！

1. springboot在启动的时候，在类路径下、META-INF/spring.factories获取指定的值
2. 将这些自动配置的类导入容器，自动配置就会生效，帮我进行自动配置
3. 以前我们需要自动配置的东西，现在springboot帮我们做了
4. 整个javaEE，解决方案和自动配置的东西都在spring-boot-autoconfigure-2.2.0.RELEASE.jar这个包下
5. 他会把所有需要导入的组件，以类名的方式返回，这些组件就会被添加到容器
6. 容器中也会存在非常多的xxxAutoConfiguration的文件（@Bean）,就是这些类给容器中导入了这个场景需要的所有组件；并自动配置，@Configuration,javaConfig

关于SpringBoot，谈谈你的理解

- 自动装配
- run()

配置文件优先级：

![image-20210518172549078](springBoot.assets/image-20210518172549078.png)

1.项目文件下的config

2.项目下的config

3.类路径文件下的config

4.类路径下的config



SpringBoot的多环境配置，可以选择激活哪一个配置文件

配置文件到底能写多少--spring.factories

根据条件配置

![image-20210519155422484](springBoot.assets/image-20210519155422484.png)

application.yml（在这个配置文件中能配置的东西，都存在一个固有的规律  配置文件绑定）

xxxAutoConfiguration：默认值，向容器中自动配置组件

 xxxProperties和配置文件绑定，我们就可以使用自定义的配置了！

xxxProperties:自动配置类，装配配置文件中自定义的一些内容！

![image-20210519172350799](springBoot.assets/image-20210519172350799.png)

静态资源

资源路径配置

![image-20210519175518554](springBoot.assets/image-20210519175518554.png)

优先级:resource >static>public

![image-20210519175422099](springBoot.assets/image-20210519175422099.png)

总结：

1.在springboot,我们可以使用以下方式处理静态资源

- webjars localhost:8080/webjars/
- public,static,/**

首页如何定制

dispatch 

## SpringSecurity(安全)

认证权限

- 功能权限
- 访问权限
- 菜单权限
- 拦截器、过滤器，大量的原生代码--冗余

RedisTemplates序列化

序列化配置

![](springBoot.assets/image-20210526133027079.png)

![image-20210526133010845](springBoot.assets/image-20210526133010845.png)

默认的序列化方式是JDK序列化，我们可能会使用Json来序列化！

在企业中，我们所有的pojo都会被序列化，默认是jdk序列化，如需要使用其他序列化方式，则需要重写RedisConfig

```java
/**
 * redis配置
 *
 * @author harbour
 */
@Configuration
@EnableCaching
public class RedisConfig extends CachingConfigurerSupport {
  @Bean
  @SuppressWarnings(value = {"unchecked", "rawtypes"})
  public RedisTemplate<Object, Object> redisTemplate(RedisConnectionFactory connectionFactory) {
    RedisTemplate<Object, Object> template = new RedisTemplate<>();
    template.setConnectionFactory(connectionFactory);
   //序列化配置
    FastJson2JsonRedisSerializer serializer = new FastJson2JsonRedisSerializer(Object.class);

    ObjectMapper mapper = new ObjectMapper();
    mapper.setVisibility(PropertyAccessor.ALL, JsonAutoDetect.Visibility.ANY);
    mapper.activateDefaultTyping(
        LaissezFaireSubTypeValidator.instance,
        ObjectMapper.DefaultTyping.NON_FINAL,
        JsonTypeInfo.As.PROPERTY);
    serializer.setObjectMapper(mapper);

    template.setValueSerializer(serializer);
    // 使用StringRedisSerializer来序列化和反序列化redis的key值
    template.setKeySerializer(new StringRedisSerializer());
    template.afterPropertiesSet();
    return template;
  }
}
```

分布式架构

HTTP和RPC

RPC两个核心模块：通信和序列化

Dubbo:RPC框架

![image-20210526165058706](springBoot.assets/image-20210526165058706.png)

async：异步

sync:同步

![image-20210526165641315](springBoot.assets/image-20210526165641315.png)

















