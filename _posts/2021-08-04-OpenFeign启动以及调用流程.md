---
layout:     post
title:      "OpenFeign启动以及调用流程"
date:       2021-08-04
author:     "xdshent"
catalog: true
tags:
    - SpringCloud
---

## 依赖版本

```xml
<dependencyManagement>
    <dependencies>
       <dependency>
          <groupId>org.springframework.cloud</groupId>
          <artifactId>spring-cloud-dependencies</artifactId>
          <version>2020.0.3</version>
          <scope>import</scope>
          <type>pom</type>
        </dependency>
    </dependencies>
</dependencyManagement>
```



## 整体流程

![OpenFeign整体流程](/img/assets/spring-cloud/open-feign/open-feign-process.jpg)



## 重试机制

图中标粉部分为feign的重试机制, 需要设置如下才可生效

由 `FeignLoadBalancerAutoConfiguration` -> `DefaultFeignLoadBalancerConfiguration`  条件注解以及注释可知需要做如下配置:

* 引入`spring-retry`

  ```xml
  <dependency>
     <groupId>org.springframework.retry</groupId>
     <artifactId>spring-retry</artifactId>
  </dependency>
  ```

* 启动`retry`

  ```yaml
  spring:
    cloud:
      loadbalancer:
        retry:
          enabled: true
          max-retries-on-same-service-instance: 1 #对同一实例的重试次数
          max-retries-on-next-service-instance: 1 #对其他实例的重试次数
          retryable-status-codes:
            - 200 #测试触发重试机制的http状态码, 200本为正常, 这里测试用
  ```

  > 由日志可知重试次数总共4次(1次当前实例正常调用 + 1次当前实例重试调用 + 1次其他实例正常调用 + 1次其他实例重试调用)

  ![重试次数日志](/img/assets/spring-cloud/open-feign/retry-log.png)

* retry细节

  > 由`BlockingLoadBalancedRetryPolicy`中的`canRetry`方法可知只有当请求是`GET`或者设置了`retryOnAllOperations`为`true`时才会进行重试, 猜测在HTTP协议中GET方法的语义为幂等, 而其他方法不是幂等不适合重试

  ![retry细节](/img/assets/spring-cloud/open-feign/can-retry.png)