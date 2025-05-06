---
layout: default
title: GRPC SETUP Go
---

## Ustawienia początkowe - Go 

Należy pobrać i zainstalować kompilator `Protoc`, następnie pluginy dla języka Go:
```
go install google.golang.org/protobuf/cmd/protoc-gen-go@latest
go install google.golang.org/grpc/cmd/protoc-gen-go-grpc@latest
```

Trzeba także pobrać biblioteki do obsługi GRPC w Go:
```
go get google.golang.org/grpc
```

## Tworzenie podstawowych plików Proto
Pliki możemy umieścić np. w folderze `/proto`. Przykładowy plik wygląda następująco:
```proto
syntax = "proto3";

// Nazwa paczki protokołu - identyfikacja plików Proto
package pb;

// Nazwa pakietu dla wygenerowanych plików Go
option go_package = "[project_name]/pb";

message CPU {
    string brand = 1; 
    string name  = 2;
    uint32 number_cores   = 3; 
    uint32 number_threads = 4;
    double min_ghz = 5;    
    double max_ghz = 6;
}
```


Do kompilacji plików używamy polecenia:
```
protoc 
    --proto_path=proto // względna lokalizacja plików Proto 
    --go_out=pb --go_opt=paths=source_relative \ // miejsce generowania plików wiadomości
    --go-grpc_out=pb --go-grpc_opt=paths=source_relative \ // miejsce generowania interfejsu klienta i serwera 
    proto/*.proto // lokalizacja plików .proto
```

## Zaawansowane struktury w plikach Proto 

```proto
import "google/protobuf/timestamp.proto";

message Laptop {
    repeated GPU gpus = 6; 
    repeated Storage storages = 7;
    
    oneof weight {
        double weight_kg = 10;
        double weight_lb = 11;
    }

    google.protobuf.Timestamp updated_at = 14; 
}
```

Aby później użyć pakietu `time` w Go, musimy przekonwertować tę strukturę na `timestamp` (więcej informacji pod: `protobuf golang github`)
```
import "google.golang.org/protobuf/types/known/timestamppb"
updated_at := timestamppb.New(time.Now())
```

Ogólnie, wszystkie rzeczy związane z `Protoc` są właśnie do znalezienia w tym repozytorium. 