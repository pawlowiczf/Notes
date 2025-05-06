---
title: Spring Boot
layout: default
---

Link: [https://start.spring.io/](https://start.spring.io/)

## Auto-reload 
W celu automatycznego przeładowywania aplikacji, po wykryciu zmian, dodaj zależność do pliku `pom.xml`:
```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-devtools</artifactId>
</dependency>
```