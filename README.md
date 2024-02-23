# Java LongPolling sample

**Long Polling**: Long polling is a technique where the client makes an HTTP request to the server, and the server keeps the connection open until new data is available

Once new data is available, the server responds to the request, and the client immediately sends another request to keep the connection open

To create a simple Java application that demonstrates the long polling technique, you'll need to set up both a server and a client. The server will wait for a certain condition

or data to be available before responding to the client's request. The client, after receiving the response, will immediately make another request to keep the connection open

Here's a basic example using Spring Boot for the server side to handle HTTP requests and a simple Java client using HttpURLConnection for making HTTP requests

## 1. Server Code (Spring Boot)

First, create a Spring Boot application for your server

```css
long-polling-server/
├── src/
│   ├── main/
│   │   ├── java/
│   │   │   └── com/
│   │   │       └── example/
│   │   │           └── longpolling/
│   │   │               ├── LongPollingServerApplication.java
│   │   │               └── LongPollingController.java
│   │   └── resources/
│   │       └── application.properties
└── pom.xml
```

You can use **Spring Initializr** (https://start.spring.io/) to bootstrap a new project with '**Spring Web**' dependency

First, let's create the **server application**

**pom.xml for Server**

```
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>com.example</groupId>
    <artifactId>long-polling-server</artifactId>
    <version>1.0-SNAPSHOT</version>
    <packaging>jar</packaging>

    <name>long-polling-server</name>
    <description>Demo project for Spring Boot</description>

    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.5.2</version>
        <relativePath/> <!-- lookup parent from repository -->
    </parent>

    <properties>
        <java.version>11</java.version>
    </properties>

    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
    </dependencies>

    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
            </plugin>
        </plugins>
    </build>
</project>
```




Save this as **LongPollingController.java** in your Spring Boot project under the **src/main/java/com/example/demo** directory (or adjust the package name as per your setup):

 **LongPollingController.java**

```java
package com.example.demo;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RestController;
import java.util.concurrent.CompletableFuture;
import java.util.concurrent.ExecutionException;

@SpringBootApplication
public class DemoApplication {

    public static void main(String[] args) {
        SpringApplication.run(DemoApplication.class, args);
    }
}

@RestController
class LongPollingController {

    @GetMapping("/long-polling")
    public String longPollingRequest() throws InterruptedException, ExecutionException {
        CompletableFuture<String> futureData = getData();
        // Wait for data to become available
        return futureData.get(); // This blocks the thread until data is available
    }

    private CompletableFuture<String> getData() {
        // Simulate data fetching or processing delay
        return CompletableFuture.supplyAsync(() -> {
            try {
                Thread.sleep(10000); // Simulate a delay
            } catch (InterruptedException e) {
                Thread.currentThread().interrupt();
            }
            return "New data available!";
        });
    }
}
```

This controller simulates a delay (e.g., waiting for new data) and then returns a response

In a real scenario, this **delay** represents the **server waiting for new data to become available**

Make sure to update the **package name (com.example.demo)** to match your project's structure

This file contains both the main application class and the controller for handling long polling

## 2. Java Client for Long Polling

Now, let's create a simple **Java client** that sends requests to the server and waits for the response:

**LongPollingClient.java**

```java
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.net.HttpURLConnection;
import java.net.URL;

public class LongPollingClient {

    public static void main(String[] args) throws Exception {
        final String urlString = "http://localhost:8080/long-polling";
        while (true) {
            URL url = new URL(urlString);
            HttpURLConnection con = (HttpURLConnection) url.openConnection();
            con.setRequestMethod("GET");

            int status = con.getResponseCode();
            if (status == HttpURLConnection.HTTP_OK) {
                BufferedReader in = new BufferedReader(new InputStreamReader(con.getInputStream()));
                String inputLine;
                StringBuffer content = new StringBuffer();
                while ((inputLine = in.readLine()) != null) {
                    content.append(inputLine);
                }
                in.close();
                System.out.println("Server response: " + content.toString());
            } else {
                System.out.println("Error: Failed to get the response from server.");
            }
            con.disconnect();

            // Immediately send another request
        }
    }
}
```

This client repeatedly sends GET requests to the server's /long-polling endpoint and prints out the server's response

## 3. Running the Example

Start the Spring Boot application by running the main class from your IDE or using the command line (./mvnw spring-boot:run if you're using Maven)

Run the Java client by executing the **LongPollingClient class**

The server will hold each client request for about 10 seconds (simulating data availability delay) before responding

Once the client receives the response, it immediately sends another request, demonstrating the long polling technique

Remember, this is a basic example for demonstration purposes

In a real-world application, you should consider error handling, efficient resource use, and scalability

**Server Application**: Import the server application into your IDE as a Maven or Gradle project, depending on what you chose when setting up the Spring Boot application

Run the DemoApplication class to start the server

**Client Application**: Compile and run the **LongPollingClient.java** using your IDE or the command line

Ensure the server application is running before you start the client

This setup demonstrates a basic long polling mechanism

The client continuously sends requests to the server and waits for a response

The server, upon receiving a request, simulates a delay to represent waiting for new data and then responds, triggering the client to immediately send another request

