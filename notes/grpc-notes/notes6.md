---
layout: default
title: Unary RPC - Java
---

Analogicznie, tworzymy klasę, która rozszerza bazową implementację serwisu. Następnie, możemy przejść do konfiguracji serwera.

## Uruchomienie i nasłuchiwanie przez serwer 
Tworzymy sobie nową klasę np. `LaptopServer`. Do rejestracji, używamy klasy `ServerBuilder`. Zróbmy to w konstruktorze:
```java
private final int port;
private final Server server; // server z klasy io.GRPC

public LaptopServer(int port, LaptopStore store) {
    this(ServerBuilder.forPort(port), port, store);
}
public LaptopServer(ServerBuilder<?> serverBuilder, int port, LaptopStore store) {
    this.port = port;

    LaptopService laptopService = new LaptopService(store);
    server = serverBuilder.addService(laptopService).build();
}
```

Trzeba także stworzyć funkcję obsługujące startowanie i zamykanie serwera oraz tzw. `shutdown hooks`:
```java
public void start() throws IOException {
    server.start();
    logger.info("server started on port " + port);

    Runtime.getRuntime().addShutdownHook(new Thread() {
        @Override
        public void run() {
            System.err.println("shut down gRPC server because JVM shuts down");
            try {
                LaptopServer.this.stop();
            } catch (InterruptedException e) {
                e.printStackTrace(System.err);
            }
            System.err.println("server shut down");
        }
    });
}

public void stop() throws InterruptedException {
    if (server != null) {
        server.shutdown().awaitTermination(30, TimeUnit.SECONDS);
    }
}

private void blockUntilShutdown() throws InterruptedException {
    if (server != null) {
        server.awaitTermination();
    }
}

public static void main(String[] args) throws Exception {
    LaptopServer server = new LaptopServer(8080, store);
    server.start();
    server.blockUntilShutdown();
}
```

**W Javie dostępne są także konteksty**

## Uruchomienie i połączenie klienta z serwerem 

```java
public class LaptopClient {
    private static final Logger logger = Logger.getLogger(LaptopClient.class.getName());

    private final ManagedChannel channel;
    private final LaptopServiceBlockingStub blockingStub;

    public LaptopClient(String host, int port) {
        channel = ManagedChannelBuilder.forAddress(host, port).usePlaintext().build();
        blockingStub = LaptopServiceGrpc.newBlockingStub(channel);
    }

    public void shutdown() throws InterruptedException {
        channel.shutdown().awaitTermination(5, TimeUnit.SECONDS);
    }

    public void createLaptop(Laptop laptop) {
        CreateLaptopRequest req = CreateLaptopRequest.newBuilder().setLaptop(laptop).build();
        CreateLaptopResponse rsp = CreateLaptopResponse.getDefaultInstance();

        // Można dodać catchowanie bardziej złożonych wyjątków z pomocą StatusRunTimeException!
        try {
            rsp = blockingStub.createLaptop(req);
        } catch (Exception e) {
            logger.log(Level.SEVERE, "request failed: " + e.getMessage());
        }

        logger.info("laptop created with ID: " + rsp.getId());
    }

    public static void main(String[] args) throws InterruptedException {
        Generator generator = new Generator();

        LaptopClient client = new LaptopClient("0.0.0.0", 8080);

        try {
            client.createLaptop(generator.NewLaptop());
        } finally {
            client.shutdown();
        }
    }
}
```