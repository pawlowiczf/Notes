---
layout: default
title: Unary RPC - Go
---
## Tworzenie interfejsu serwera i klienta 

Tworzymy serwis RPC w pliku np. `laptop_service.proto`:
```proto
message CreateLaptopRequest {
    Laptop laptop = 1; 
}

message CreateLaptopResponse {
    string id = 1;
}

service LaptopService {
    rpc CreateLaptop(CreateLaptopRequest) returns (CreateLaptopResponse) {};
}
```

Musimy stworzyć serwer, który:
1. Implementuje interfejs wygenerowanego serwisu 
2. Embeduje `pb.UnimplementedLaptopServiceServer` dla kompatybilności 

Do zwracania błędów używa się pakietów `status` oraz `codes`. Przykładowo:
```go
return nil, status.Errorf(codes.InvalidArgument, "laptop ID is not a valid UUID: %v", err)
return nil, status.Errorf(codes.Internal, ...)
```

## Uruchomienie i nasłuchiwanie przez serwer 
Schemat wygląda następująco:
1. Stwórz instację struktury, która implementuje interfejs stworzonego serwisu RPC 
2. Stwórz serwer GRPC  
3. Zarejestruj strukturę do serwera GRPC 
4. Stwórz obiekt `Listener`, który nasłuchuje na danym porcie 
5. Przekaż serwerowi GRPC, aby wykorzystał danego `Listenera` 

Przykładowy kod:
```go
func main() {
	laptopServer := service.NewLaptopServer(service.NewInMemoryLaptopStore())
	grpcServer := grpc.NewServer()
	pb.RegisterLaptopServiceServer(grpcServer, laptopServer)

	listener, err := net.Listen("tcp", "0.0.0.0:8080")
	err = grpcServer.Serve(listener)
}

```

## Uruchomienie i połączenie klienta z serwerem 
Schemat wygląda następująco:
1. Stwórz połączenie z serwerem GRPC, na danym adresie 
2. Stwórz klienta, który będzie się z tym serwerem łączył 
3. Wyślij odpowiednie zapytania 

Przykładowy kod:
```go
func main() {
	conn, err := grpc.NewClient(
        "0.0.0.0:8080",
        grpc.WithTransportCredentials(insecure.NewCredentials())
    )

	laptopClient := pb.NewLaptopServiceClient(conn)

	laptop := sample.NewLaptop()
	req := &pb.CreateLaptopRequest{
		Laptop: laptop,
	}

	rsp, err := laptopClient.CreateLaptop(context.Background(), req)
}
```

## Możliwe ulepszenia 
<hr style="margin-bottom: 13px;">
Używaj RWMutex do częstych operacji odczytu i rzadkich operacji zapisu. 

<hr style="margin-bottom: 13px;">
Wykorzystuj `context.WithTimeout` w kliencie:
```go
ctx, cancel = context.WithTimeout(context.Background(), 5 * time.Second)
defer cancel()
```

W serwerze możemy wtedy sprawdzić, czy kontekst został anulowany lub został przekroczony ustawiony czas:    
```go
// Sytacja, gdy program klienta zostanie np. przerwany (wykona się cancel())
if ctx.Err() == context.Canceled {
    ...
}

// Sytuacja, gdy czas zostanie przekroczony 
if ctx.Err() == context.DeadlineExceeded {

}
```
<hr style="margin-bottom: 13px;">