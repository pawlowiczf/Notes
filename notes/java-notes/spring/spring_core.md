---
title: Spring Core
layout: default
---

## Wstrzykiwanie zależości przez konstruktor
W celu oznaczenia klasy, jako kandydata do wstrzyknięcia, należy oznaczyć go adnotacją `@Component`. Przykładowo:
```
@Component 
@Lazy
public class MercedesMechanic implements Mechanic {
    @Override 
    public String fixCar() {
        return "Fixing Mercedes";
    }
}
```
Adnotacja `@Lazy` powoduje, że obiekt jest inicjalizowany, dopiero, gdy jest potrzebny. 

Następnie, gdy potrzebujemy obiekt tej klasy w innej, tworzymy konstruktor i oznaczamy go adnotacją `@Autowired`. 
```
@Autowired 
public DemoController(Mechanic mechanic)
```

## Wstrzykiwanie zależności przez setter
To samo, ale wstrzykujemy przez settery:
```
@Autowired
void setMechanic(Mechanic mechanic) {
    this.mechanic = mechanic;
}
```

## Problem niejednoznaczności 

### Adnotacja `@Qualifier`
Co jeśli, mamy więcej implementacji danego interfejsu? Którą klasę wstrzyknąć? Możemy być dokładniejsi i napisać:
```
@Autowired
void setCoach(@Qualifier("mercedesMechanic") Mechanic mechanic) {}
```
Podany string to `beanID` - taki sam, jak nazwa klasy, ale z małej litery. 

### Adnotacja `@Primary`
Możemy implementację danego interfejsu oznaczyć adnotacją `Primary` - taka adnotacja może być tyko jedna. Taka klasa będzie domyślnie wstrzykiwana, w przypadku braku jawnego określenia innej. 

## Bean Scope 
Domyślnie, Spring tworzy jedną instację każdego komponentu - Singleton. Możemy to zmodyfikować, np. aby przy każdym wstrzyknięciu, tworzył nowy obiekt.
```
@Component
@Scope(ConfigurableBeanFactory.SCOPE_SINGLETON) // only one instance
@Scope(ConfigurableBeanFactory.SCOPE_PROTOTYPE) // many instances
```

## PostKonstruktor i Dekonstruktor 
Po inicjalizacji Beana, możemy wykonać jeszcze własną inicjalizację, np. połączenie z bazą danych. Możemy także, przed wyłączeniem apliacji, takie połączenie zakończyć. 
```java
@PostConstruct
public void init() {}

@PreDestroy
public void destroy() {}
```

## `@Configuration` i `@Bean` 
Czasami cieżko tworzyć klasy z adnotacją `@Component`, gdy np. używamy zewnętrznych bibliotek. Możemy stworzyć własną klasę konfiguracyjną, stworzyć metodę z adnotacją `@Bean` i w tej metodzie zaimplementować funkcjonalności, zwracajać ostatecznie dany obiekt. 
```java
@Configuration
public class SportConfig {
    @Bean
    public Coach swimCoach() {
        // dowolna konfiguracja, logika
        return new SwimCoach();
    }
}
```