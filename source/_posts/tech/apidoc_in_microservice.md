---
title: Api Doc in Microservice Project
date: 2025-05-20 00:00:00
tags: mircoservice
categories: tech
---



**微服务文档聚合配置模板（基于 Spring Boot 2.7.6 + Spring Cloud Gateway + Knife4j 4.3.0 + Nacos）**：

------

## 🧱 1. 父项目 `pom.xml` 中版本管理

```xml
<properties>
    <spring-boot.version>2.7.6</spring-boot.version>
    <spring-cloud.version>2021.0.6</spring-cloud.version>
    <knife4j.version>4.3.0</knife4j.version>
</properties>

<dependencyManagement>
    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-dependencies</artifactId>
            <version>${spring-boot.version}</version>
            <type>pom</type>
            <scope>import</scope>
        </dependency>

        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-dependencies</artifactId>
            <version>${spring-cloud.version}</version>
            <type>pom</type>
            <scope>import</scope>
        </dependency>

        <dependency>
            <groupId>com.github.xiaoymin</groupId>
            <artifactId>knife4j-gateway-spring-boot-starter</artifactId>
            <version>${knife4j.version}</version>
        </dependency>
    </dependencies>
</dependencyManagement>
```

------

## 🚪 2. Gateway 项目依赖

```xml
<dependencies>
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-gateway</artifactId>
    </dependency>

    <dependency>
        <groupId>com.alibaba.cloud</groupId>
        <artifactId>spring-cloud-starter-alibaba-nacos-discovery</artifactId>
    </dependency>

    <dependency>
        <groupId>com.github.xiaoymin</groupId>
        <artifactId>knife4j-gateway-spring-boot-starter</artifactId>
    </dependency>
</dependencies>
```

------

## ⚙️ 3. Gateway `application.yml` 配置（核心）

```yml
server:
  port: 8080

spring:
  application:
    name: pyoj-backend-gateway
  cloud:
    nacos:
      discovery:
        server-addr: 127.0.0.1:8848
    gateway:
      routes:
        - id: pyoj-backend-user-service
          uri: lb://pyoj-backend-user-service
          predicates:
            - Path=/api/user/**
        - id: pyoj-backend-question-service
          uri: lb://pyoj-backend-question-service
          predicates:
            - Path=/api/question/**
        - id: pyoj-backend-submit-service
          uri: lb://pyoj-backend-submit-service
          predicates:
            - Path=/api/submit/**
        - id: pyoj-backend-judge-service
          uri: lb://pyoj-backend-judge-service
          predicates:
            - Path=/api/judge/**

knife4j:
  gateway:
    enabled: true
    strategy: manual
    routes:
      - name: 用户服务
        service-name: pyoj-backend-user-service
        url: /api/user/v3/api-docs
      - name: 题目服务
        service-name: pyoj-backend-question-service
        url: /api/question/v3/api-docs
      - name: 提交服务
        service-name: pyoj-backend-submit-service
        url: /api/submit/v3/api-docs
      - name: 判题服务
        service-name: pyoj-backend-judge-service
        url: /api/judge/v3/api-docs
```

------

## 📦 4. 子服务通用依赖（user / question / submit / judge）

```xml
<!-- OpenAPI 文档生成 -->
<dependency>
    <groupId>org.springdoc</groupId>
    <artifactId>springdoc-openapi-ui</artifactId>
</dependency>

<!-- Knife4j 增强支持（可选） -->
<dependency>
    <groupId>com.github.xiaoymin</groupId>
    <artifactId>knife4j-openapi3-spring-boot-starter</artifactId>
</dependency>
```

------

## 📝 5. 子服务通用配置（application.yml）

```yaml
springdoc:
  api-docs:
    enabled: true
  swagger-ui:
    enabled: true

knife4j:
  enable: true

# 若需要 context-path，需配合 gateway 路由写好前缀
server:
  port: 8081
  servlet:
    context-path: /api/user  # 不同服务改不同前缀
```

------

## 📍 6. 访问地址

- 聚合入口（统一访问）：

  ```
  http://localhost:8080/knife4j
  ```

- 单服务 Swagger UI：

  ```
  http://localhost:8081/api/user/swagger-ui/index.html # openapi
  http://localhost:8081/api/user/doc.html  # Knife4j UI
  ```

------

## ✅ 附：快速排错建议

| 问题             | 原因                                | 解决                                           |
| ---------------- | ----------------------------------- | ---------------------------------------------- |
| 文档请求异常     | gateway 未配置 route                | 补上该服务的 route                             |
| 聚合失败         | manual 模式未写正确 url             | 补 `/api/**/v3/api-docs` 路径                  |
| Knife4j 页面白屏 | controller 参数未加注解 / path 错误 | 加 `@RequestParam`, 避免 `@RequestMapping("")` |
| 无法识别接口     | 没加 `springdoc-openapi-ui`         | 所有服务都要加 openapi 依赖                    |

**注**: 对于内部api(XXXInnerController), 可以打上

```
@io.swagger.v3.oas.annotations.Hidden
```

注解, 使其不暴露在接口文档中. 