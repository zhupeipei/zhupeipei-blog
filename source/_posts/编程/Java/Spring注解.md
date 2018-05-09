---
title: Spring注解
date: 2018/4/20 16:28:01
tags:
categories: [编程, Java]
---

1. @ConfigurationProperties
可以将配置文件封装为实体类。

有时候有这样子的情景，我们想把配置文件的信息，读取并自动封装成实体类，这样子，我们在代码里面使用就轻松方便多了，这时候，我们就可以使用@ConfigurationProperties，它可以把同类的配置信息自动封装成实体类

首先在配置文件里面，这些信息是这样子滴
```
connection.username=admin
connection.password=kyjufskifas2jsfs
connection.remoteAddress=192.168.1.1
```
这时候我们可以定义一个实体类在装载配置文件信息
```
@Component
@ConfigurationProperties(prefix="connection")
public class ConnectionSettings {

    private String username;
    private String remoteAddress;
    private String password ;

    public String getUsername() {
        return username;
    }
    public void setUsername(String username) {
        this.username = username;
    }
    public String getRemoteAddress() {
        return remoteAddress;
    }
    public void setRemoteAddress(String remoteAddress) {
        this.remoteAddress = remoteAddress;
    }
    public String getPassword() {
        return password;
    }
    public void setPassword(String password) {
        this.password = password;
    }

}
```
我们还可以把@ConfigurationProperties还可以直接定义在@bean的注解上，这是bean实体类就不用@Component和@ConfigurationProperties了
```
@SpringBootApplication
public class DemoApplication{

    //...

    @Bean
    @ConfigurationProperties(prefix = "connection")
    public ConnectionSettings connectionSettings(){
        return new ConnectionSettings();

    }

    public static void main(String[] args) {
        SpringApplication.run(DemoApplication.class, args);
    }
}
```
然后我们需要使用的时候就直接这样子注入
```
@RestController
@RequestMapping("/task")
public class TaskController {

@Autowired ConnectionSettings conn;

@RequestMapping(value = {"/",""})
public String hellTask(){
    String userName = conn.getUsername();     
    return "hello task !!";
}
```
如果发现@ConfigurationPropertie不生效，有可能是项目的目录结构问题，你可以通过@EnableConfigurationProperties(ConnectionSettings.class)来明确指定需要用哪个实体类来装载配置信息。

2. @RestControllerEndpoint 和 @ControllerEndpoint
这两个可以用于自定义的actuator的端点
配置如下：
```
@Component
@RestControllerEndpoint(id = "example")
public class ExampleRestControllerEndpoint {

	@GetMapping("/echo")
	public ResponseEntity<String> echo() {
		return ResponseEntity.ok().header("echo", "as").body("cvb");
	}

}
```
