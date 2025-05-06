---
title: Hibernate
layout: default
---

## Instalacja zależności 
Po stworzeniu projektu przez Mavena, należy dodać zależność Hibernate oraz sterownik np. PostgreSQL do pliku `pom.xml`:
```xml
<dependencies>
    <dependency>
        <groupId>org.postgresql</groupId>
        <artifactId>postgresql</artifactId>
        <version>42.7.4</version>
    </dependency>

    <dependency>
        <groupId>org.hibernate.orm</groupId>
        <artifactId>hibernate-core</artifactId>
        <version>7.0.0.Beta4</version>
    </dependency>
</dependencies>
```
Należy także stworzyć plik konfiguracyjny dla Hibernate w folderze `resources/hibernate.cfg.xml` - podać URL bazy danych, użytkownika, hasło i inne właściwości:
```xml
<hibernate-configuration xmlns="http://www.hibernate.org/xsd/orm/cfg">
    <session-factory>
        <property name="hibernate.connection.driver_class">org.postgresql.Driver</property>
        <property name="hibernate.connection.url">jdbc:postgresql://localhost:5432/DB_NAME</property>
        <property name="hibernate.connection.username">postgres</property>
        <property name="hibernate.connection.password">DB_PASSWORD</property>

        <property name="hibernate.hbm2ddl.auto">update/create</property>
        <property name="hibernate.show_sql">true</property>
    </session-factory>
</hibernate-configuration>
```
## Inicjalizacja połączenia i sesji
```java
SessionFactory factory = new Configuration()
        .addAnnotatedClass(com.example.CLASS_NAME.class)
        .configure()
        .buildSessionFactory();
```
```java
Transaction tx = null;
try (Session session = factory.openSession()) {
    tx = session.beginTransaction();
    session.persist(OBJECT);
    tx.commit();
} catch (org.hibernate.exception.ConstraintViolationException e) {
    if (tx != null) tx.rollback();
}
```
W celu odpytania bazy, możemy wykonać:
```java
Object object = session.byId(Object.class).getReference(ID); // lazy fetching
Object object = session.find(Object.class, ID); // eager fetching
```

Ostatecznie, należy zamknąć sesję i połączenie:
```java
session.close();
factory.close();
```