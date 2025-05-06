---
title: REST
layout: default
---

## Wprowadzenie 

- `@RestController` - oznaczenie klasy, jako endpointu REST
- `@RestMapping("/v1")` - określenie prefixu URI danej klasy 
- `@GetMapping("/lecture")` - określenie funkcji handlującej zapytania GET 

## Zmienne w URI
```java
@GetMapping("/students/{studentID}")
public Student getStudent(@PathVariable int studentID) {}
```

## Wyjątki, błędy
W celu złapania i przesłania błędu, jako odpowiedzi należy:
- Zdefiniować własną klasę z odpowiedzią (np. zawiera pola status code, message), wraz getterami, setterami 
- Zdefiniować własny wyjątek, np. `StudentNotFoundException` (dziedziczy po RuntimeException)
- Poinformować kontroler REST, by dany błąd rozstrzygał w określony sposób. 

Przykład:
```java
@ExceptionHandler
public ResponseEntity<StudentErrorResponse> handleException(StudentNotFoundException exc) {
    StudentErrorResponse err = new ...;
    err.setStatus(...);
    err.setMessage(...);
    return new ResponseEntity<>(err, HttpStatus.NOT_FOUND);
}
```