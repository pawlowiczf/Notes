---
title: Basic database operations/connections
layout: default
---

## Instalacja sterownika MySQL 
Wejdź na repozytorium Maven i znajdź odpowiednią wersję sterownika MySQL - następnie dodaj ją do pliku `build.gradle`. Przykładowo:
```java
implementation("com.mysql:mysql-connector-j:9.2.0")
```

W celu integracji z bazą danych, zdefiniuj URL: `"jdbc:mysql://127.0.0.1:3306/university"` i ustanów połączenie:
```java
try (Connection connection = DriverManager.getConnection(CONN_STRING, "USER", "PASSWORD")) {
    System.out.println("Connected to database");
} catch (SQLException e) {
    throw new RuntimeException(e);
}
```
## Wykonywanie prostych zapytań
Do stworzenia zapytania wykorzystujemy klasę `Statement statement = connection.newStatement()` - taki obiekt trzeba zamknąć na końcu, dlatego warto umieścić go w `try-with-resources`. Wynik zapytania możemy przypisać do klasy `ResultSet resultSet = statement.executeQuery(query)`. Przykładowo:
```java
ResultSetMetaData md = resultSet.getMetaData();
for (int a = 1; a <= md.getColumnCount(); a++) {
    System.out.println(md.getColumnName(a) + ": " + md.getColumnTypeName(a));
}

while (resultSet.next()) {
    int id = resultSet.getInt("id");
    String firstName = resultSet.getString("first_name");
    String lastName = resultSet.getString("last_name");
    System.out.println(id + " " + firstName + " " + lastName);
}
```

## Inny sposób połączenia - więcej kontroli
```java
MysqlDataSource dataSource = new MysqlDataSource();
dataSource.setServerName("localhost");
dataSource.setDatabaseName("university");
dataSource.setPort(3306);
dataSource.setContinueBatchOnError(false);
Connection conn = dataSource.getConnection("USER", "PASSWORD")
```