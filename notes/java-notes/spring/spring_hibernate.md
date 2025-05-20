---
title: Hibernate
layout: default
---

## Wprowadzenie
`Entity Manager` pozwala zarządzać bazą danych - URL, użytkownik, hasło definiujemy z pliku `application.properties`. Przykładowo: 
```
spring.datasource.url=jdbc:mysql://localhost:3306/students
spring.datasource.password=root
spring.datasource.username=root
```

```java
@Repository
public class StudentDAO implements IStudentDAO {
    EntityManager entityManager;

    @Autowired
    public StudentDAO(EntityManager entityManager) {
        this.entityManager = entityManager;
    }

    @Override
    @Transactional
    public void save(Student student) {
        entityManager.persist(student);
    }

    @Override
    public Student findById(Integer id) {
        Student student = entityManager.find(Student.class, id);
        System.out.println(student);
        return student;
    }
}
```
Wyjaśnienie adnotacji:
- `Repository` oznacza klasę DAO,
- `Transactional` oznacza, że zawartość funkcji wykona się w jednej transakcji,


## Aktualizowanie zawartości/wyszukiwanie
Wykonujemy: 
```java
TypedQuery<Student> query = entityManager.createQuery("FROM Student WHERE lastName='Doe'", Student.class);
query.getResultList();
```
```java
query = entityManager.createQuery("FROM Student WHERE lastName=:theData", Student.class);
query.setParameter("theData", "Kowalski")

// SELECT s FROM Student s WHERE s.firstName='Doe'
```

## Migracje 
```java
spring.jpa.hibernate.ddl-auto= // update, create
```

## JPARepository i własne implementacje DAO
JPARepository ułatwia i automatycznie generuje kod, do interakcji z bazą danych. Wystarczy implementować interfejs `JpaRepository`, podajać encję i typ klucza głównego.    

Jeśli chcemy dodać własne metody, tworzymy customowy interfejs, jego implementację i rozszerzamy bazowe repozytorium własnie o ten interfejs. Przykładowo:
```java
public interface UserRepositoryCustom {
    void doSomethingCustom();
}

public class UserRepositoryImpl implements UserRepositoryCustom {
    @PersistenceContext
    private EntityManager entityManager;

    @Override
    public void doSomethingCustom() {
        // Twoja implementacja z użyciem EntityManagera
    }
}

public interface UserRepository extends JpaRepository<User, Long>, UserRepositoryCustom {
}
```
