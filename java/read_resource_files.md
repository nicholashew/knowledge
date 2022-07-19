# Read Resource Files

## Files in resources folder using Maven Project

In Maven Project, the files located in the src/main/resources
```
|__src\
      |__main\*.java
      |
      |__resources\
             |__config.json
             |__others.json
```

```java
package com.example.resources;

public class Main {
  public static void main(String[] args)
  {
    String config = getResourceAsString("config.json");    
    System.out.println(config);
  }
  
  public static String getResourceAsString(String fileName) throws IOException {
    InputStream inputStream = getResourceAsStream(fileName);

    String text = new BufferedReader(
      new InputStreamReader(inputStream, StandardCharsets.UTF_8))
        .lines()
        .collect(Collectors.joining("\n"));

    return text;
  }

  public static InputStream getResourceAsStream(String fileName) {
    // The class loader that loaded the class
    ClassLoader classLoader = Main.class.getClassLoader();
    InputStream inputStream = classLoader.getResourceAsStream(fileName);

    // the stream holding the file content
    if (inputStream == null) {
      throw new IllegalArgumentException("file not found! " + fileName);
    } else {
      return inputStream;
    }
  }
}
```
