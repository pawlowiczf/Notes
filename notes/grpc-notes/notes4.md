---
layout: default
title: Serialization - Java
---

```java
laptop.getParserForType();

public static void WriteProtobufToBinaryFile(Message message, String filename) throws FileNotFoundException, IOException {
    try (FileOutputStream fos = new FileOutputStream(filename)) {
        message.writeTo(fos);
    }
}
public <T extends Message> T ReadProtobufFromBinaryFile(String filename, Parser<T> parser) throws FileNotFoundException, IOException {
    try (FileInputStream fis = new FileInputStream(filename)) {
        return parser.parseFrom(fis);
    }
}
```