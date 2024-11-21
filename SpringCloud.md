# SpringCloud

## 服务注册与发现

### Consul

```xml
<!--SpringCloud consul discovery-->
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-consul-discovery</artifactId>
</dependency>
```

常用注解：

* @EnableDiscoveryClient



**配置文件：bootstrap.tml**

```yaml
# 专门配置consul文件，bootstrap.yml优先级高于application.yml
spring:
  application:
    name: cloud-payment-service
  cloud:
  	# 注册中心
    consul:
      host: localhost
      port: 8500
      discovery:
        service-name: ${spring.application.name}
      config:
      	# 设置配置文件名称例payment-service；默认文件格式为YAML
        profile-separator: '-'
        format: YAML

```

### Nacos



## 服务调用和负载均衡

### LoadBalancer

### OpenFeign

* @EnableFeignClients、@FeignClient、@LoadBalanced

**OpenFeign配置文件**

```yaml
openfeign:
      compression:
        request:
          enabled: true
          # 最小触发压缩大小
          min-request-size: 2048
          # 触发压缩的数据类型
          mime-types: text/xml,application/xml,application/json
        response:
          enabled: true
      # 打开Apache HttpClient5配置开启
      httpclient:
        hc5:
          enabled: true
      # 全局Feign配置
      client:
        config:
#          default:
#            # 连接超时时间
#            connect-timeout: 3000
#            # 执行超时时间
#            read-timeout: 3000
          # 配置指定服务的连接超时时间和执行超时时间
          cloud-payment-service:
            connect-timeout: 5000
            read-timeout: 5000
# feign日志以什么级别监控哪个接口
logging:
  level:
    org:
      hm:
        cloud:
          api:
            org.hm.cloud.api.PayFeignApi: debug
```

## 分布式事务

### Seata



## 服务熔断和降级

### Circuit Breaker

```xml
<!--resilience4j-circuitbreaker-->
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-circuitbreaker-resilience4j</artifactId>
</dependency>
<!-- 由于断路保护等需要AOP实现，所以必须导入AOP包 -->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-aop</artifactId>
</dependency>
```

> 断路器有三种普通状态，两种特殊状态：CLOSED、OPEN、HALF_OPEN、DISABLED、FORCED_OPEN

* CLOSED（关闭）：断路器关闭状态
* OPEN（打开）：断路器打开状态，拒绝所有请求
* HALF_OPEN（半开）：OPEN后触发请求测试，测试阶段为HALF_OPEN状态，测试请求通过后断路器恢复为CLOSED状态。否则进入断路器OPEN状态，此时拒绝所有请求访问
* DISABLED（始终允许访问）：相当于完全关闭断路器
* FORCED_OPEN（始终拒绝访问）：相当于始终打开断路器，拒绝任何请求访问

### Sentinel





## 服务链路追踪

### Sleuth



## 服务网关

### GateWay

> API网关服务，提供统一的API路由管理方式。客户端=>Nginx=>GateWay=>Service1

**三大核心**

* Route（路由）：判断请求路由是否存在
* Predicate（断言）：路由成功后判断是否符合匹配条件
* Filter（过滤）：在请求被路由前或之后对请求进行修改

```xml
<!--gateway-->
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-gateway</artifactId>
</dependency>
```

**配置文件**

```yaml
spring:
  application:
    name: cloud-gateway
  cloud:
    consul:
      host: localhost
      port: 8500
      discovery:
        prefer-ip-address: true
        service-name: ${spring.application.name}
    gateway:
      routes:
        - id: pay_routh1        # 路由id，要求唯一
          uri: http://localhost:8001   # 匹配后提供服务的路由地址
          predicates:
            - Path=/pay/gateway/get/**  # 断言，路径匹配的进行路由
```





## 分布式事务管理

### Seata







# 其他

* 创建Maven创建项目使用Java17。父工程使用pom打包
* dependencyManagement：父工程声明依赖并不引入依赖，在子工程中引入依赖。
* properties：用于声明自定义变量名称${mysql.version}
* IDEA配置File Encoding \ Java Compiler \ File Type隐藏
* Maven配置skip test
* Docker 配置 MySQL8.0.26

```sql
CREATE DATABASE db2024
USE db2024
DROP TABLE IF EXISTS `t_pay`;
CREATE TABLE `t_pay` (
     `id` INT(10) UNSIGNED NOT NULL AUTO_INCREMENT,
     `pay_no` VARCHAR(50) NOT NULL COMMENT '支付流水号',
     `order_no` VARCHAR(50) NOT NULL COMMENT '订单流水号',
     `user_id` INT(10) DEFAULT '1' COMMENT '用户账号ID',
     `amount` DECIMAL(8,2) NOT NULL DEFAULT '9.9' COMMENT '交易金额',
     `deleted` TINYINT(4) UNSIGNED NOT NULL DEFAULT '0' COMMENT '删除标志，默认0不删除，1删除',
     `create_time` TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP COMMENT '创建时间',
     `update_time` TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP COMMENT '更新时间',
     PRIMARY KEY (`id`)
) ENGINE=INNODB AUTO_INCREMENT=1 DEFAULT CHARSET=utf8mb4 COMMENT='支付交易表';
INSERT INTO t_pay(pay_no,order_no) VALUES('pay17203699','6544bafb424a');
SELECT * FROM t_pay;
```

## consul作用：服务注册发现

> 下载地址：https://developer.hashicorp.com/consul/install
>
> 集成SpringCloud地址：https://spring.io/projects/spring-cloud-consul

```shell
# 在下载consul文件夹下运行cmd
consul --version
# 显示版本号则成功
# 运行consul
consul agent -dev

# 浏览器中输入url进入可视化界面
http://localhost:8500/ui/dc1/services
```

将Consul在windows中注册为后台服务

```shell
@echo.服务启动.......
@echo off
@sc create Consul binpath="E:\MyProject\learn\Java\02_consul_1.17.1_windows_386\consul.exe agent -server -ui -bind=127.0.0.1 -client=0.0.0.0 -bootstrap-expect 1 -data-dir E:\MyProject\learn\Java\02_consul_1.17.1_windows_386\mydata "
@net start Consul
@sc config Consul start= AUTO
@echo.Consul start is OK.......success
@pause
```

## LoadBalancer：负载均衡

> 负责客户端的负载均衡（Nginx是服务端负载均衡）

```xml
<!--loadbalancer：实现客户端负载均衡，轮询、随机分发、权重等负载均衡方法-->
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-loadbalancer</artifactId>
</dependency>
```

* @LoadBalanced：对RestTemplate进行负载均衡

```java
@Configuration
public class RestTemplateConfig {
    //@LoadBalanced：专门给RestTemplate赋予负载均衡的能力，是在请求发送前添加一个interceptor拦截器，有随即分发、轮询、权重等负载均衡方法
    /**
     * Spring Cloud LoadBalancer：支持RestTemplate+服务发现；客户端负载均衡（本地对访问请求的负载均衡）；Nginx负责服务端的负载均衡
     * */
    @Bean
    @LoadBalanced
    public RestTemplate restTemplate(){
        return new RestTemplate();
    }
}
```

## OpenFeign：服务接口调用

> 配合注册中心使用，创建接口和添加注解来调用远程服务，同样支持负载均衡，集成Sentinal服务降级、熔断

```java
//1.引入OpenFeign依赖；2.入口类添加@EnableFeignClients注解；3.创建调用远程服务的接口Api类并添加@FeignClient注解
```

* @FeignClient(value = "服务名")：服务消费者调用添加了该注解的远程微服务接口
