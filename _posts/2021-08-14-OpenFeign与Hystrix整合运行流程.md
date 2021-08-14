---
layout:     post
title:      "OpenFeign与Hystrix整合运行流程"
date:       2021-08-14
author:     "xdshent"
catalog: true
tags:
    - SprinCloud
---

# 依赖版本

```xml
<dependencyManagement>
   <dependencies>
      <dependency>
         <groupId>org.springframework.cloud</groupId>
         <artifactId>spring-cloud-dependencies</artifactId>
         <version>Hoxton.SR9</version>
         <scope>import</scope>
         <type>pom</type>
      </dependency>
   </dependencies>
</dependencyManagement>
```



# 开启Hystrix

```yaml
feign:
  hystrix:
    enabled: true
```

# Hystrix启动以及运行流程

![hystrix-process](/img/assets/spring-cloud/hystrix/hystrix-process.jpg)