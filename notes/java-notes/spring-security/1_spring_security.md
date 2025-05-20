---
title: Spring Security Start
layout: default
---

## Instalacja dependencji 

Dodaj dependencję `spring boot starter security`. Automatycznie, wszystkie endpointy są zastrzeżone loginem i hasłem (hasło podawane jest w konsoli). Można podać własne dane logowania w pliku `application.properties`:
```java
spring.security.user.name = chomik123
spring.security.user.password = root123
```

## Tworzenie podstawowych ról w pamięci
W pliku konfiguracyjnym dodajemy:
```java
@Bean
public InMemoryUserDetailsManager userDetailsManager() {
    UserDetails john = User.builder()
            .username("john")
            .password("{noop}test123")
            .roles("EMPLOYEE")
            .build();
    UserDetails mary = User.builder()
            .username("mary")
            .password("{noop}test123")
            .roles("EMPLOYEE", "MANAGER")
            .build();
    UserDetails susan = User.builder()
            .username("susan")
            .password("{noop}test123")
            .roles("EMPLOYEE", "MANAGER", "ADMIN")
            .build();

    return new InMemoryUserDetailsManager(john, mary, susan);
}
```
W celu wysłania zapytania w `Postman`, dodajemy pola w `Basic auth`. 

## Ochrona API na podstawie ról 

```java
@Bean
public SecurityFilterChain filterChain(HttpSecurity http) throws Exception {
    http.authorizeHttpRequests(configurer -> {
        configurer
                .requestMatchers(HttpMethod.GET, "/api/employees").hasRole("EMPLOYEE")
                .requestMatchers(HttpMethod.GET, "/api/employees/**").hasRole("EMPLOYEE")
                .requestMatchers(HttpMethod.POST, "/api/employees").hasRole("MANAGER")
                .requestMatchers(HttpMethod.PUT, "/api/employees").hasRole("MANAGER")
                .requestMatchers(HttpMethod.DELETE, "/api/employees/**").hasRole("ADMIN");
                // hasAnyRole, 
    });

    http.httpBasic(Customizer.withDefaults());
    http.csrf(AbstractHttpConfigurer::disable);

    return http.build();
}
```