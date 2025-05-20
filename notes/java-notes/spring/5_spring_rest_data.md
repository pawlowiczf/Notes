---
title: Spring Exceptions
layout: default
---

W celu uproszczenia pisania tego samego kodu w endpointach dla każdego repozytorium, można skorzystać z wtyczki `spring data rest`, która automatycznie, na podstawie danego repozytorium, wystawia endpointy. Domyślnie robi to pod ścieżką liczby mnogiej nazwy encji - przykładowo mająć encję `Employees`, endpointy będą wystawione pod: `/employees/`. 

Jeśli chcemy zmienić ten domyślny URI, to dodajemy adnotację `@RepositoryRestResource(path = "members")`. Można także dodać bazowy przedrostek w URI, konfiguruje się to już w pliku `application.properties`: `spring.data.rest.base-path=api`. 

Wtyczka dodaje też wiele innych opcji, np. sortowanie po danym polu encji: `/employees?sort=lastName,desc`. 