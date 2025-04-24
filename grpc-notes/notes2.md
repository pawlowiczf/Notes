---
layout: default
title: GRPC SETUP Java
---

## Ustawienia początkowe - Java
Podobnie, jak w Go, musimy pobrać pluginy Protoc dla Javy i dodać biblioteki obsługujące gRPC w Javie. Potrzebne wpisy możemy znaleźć, wyszukując frazy: `java grpc github`, czy `protobuf gradle plugin`. W pliku `build.gradle` dodajemy:
```
dependencies {
    implementation("com.google.protobuf:protobuf-java:4.30.2")

    runtimeOnly 'io.grpc:grpc-netty-shaded:1.72.0'
    implementation 'io.grpc:grpc-protobuf:1.72.0'
    implementation 'io.grpc:grpc-stub:1.72.0'
}

sourceSets {
    main {
        java {
            srcDirs 'build/generated/source/proto/main/grpc'
            srcDirs 'build/generated/source/proto/main/java'
        }
    }
}

protobuf {
    protoc {
        artifact = "com.google.protobuf:protoc:3.25.5"
    }
    plugins {
        grpc {
            artifact = 'io.grpc:protoc-gen-grpc-java:1.72.0'
        }
    }
    generateProtoTasks {
        all()*.plugins {
            grpc {}
        }
    }
}
```

W plikach Proto możemy dodać opcję:
```
// Nazwa pakietu dla wygenerowanych plików Java
option java_package = "com.pcbook.pb";

// Dzieli wygenerowane pliki, aby były czytelniesze 
option java_multiple_files = true; 
```

Po kliknięciu opcji `Build` projektu, wszystkie pliki `.proto`, **umieszczone w folderze `src/main/proto`** zostaną automatycznie skompilowane i umieszczone w odpowiednich folderach. 

<hr style="margin-bottom: 13px;" />

W celu zmiany domyślnego folderu z plikami `.proto` dodaj do `build.gradle`:
```
sourceSets {
    main {
        java {
            srcDirs 'build/generated/source/proto/main/grpc'
            srcDirs 'build/generated/source/proto/main/java'
        }
        proto {
            srcDir 'src/main/proto2'  // ← tu podajesz własny katalog z .proto
        }
    }
}
```

## Tworzenie przykładowych obiektów (messages) w Javie 
Wygenerowany kod jest dość bogaty - umożliwia łatwe tworzenie obiektów, z pomocą wzorca `Builder`.

```java
public Keyboard NewKeyboard() {
    return Keyboard.newBuilder()
        .setLayout(randomKeyboardLayout())
        .setBacklit(rand.nextBoolean())
        .build();
}
```

```java
// Obiekt Timestamp Proto, z biblioteki google.Protobuf
private Timestamp timestampNow() {
    Instant now = Instant.now();
    return Timestamp.newBuilder()
        .setSeconds(now.getEpochSecond())
        .setNanos(now.getNano())
        .build();
}
```