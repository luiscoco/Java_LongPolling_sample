# Java LongPolling sample

**Long Polling**: Long polling is a technique where the client makes an HTTP request to the server, and the server keeps the connection open until new data is available

Once new data is available, the server responds to the request, and the client immediately sends another request to keep the connection open

To create a simple Java application that demonstrates the long polling technique, you'll need to set up both a server and a client. The server will wait for a certain condition

or data to be available before responding to the client's request. The client, after receiving the response, will immediately make another request to keep the connection open

Here's a basic example using Spring Boot for the server side to handle HTTP requests and a simple Java client using HttpURLConnection for making HTTP requests

## 1. Server Code (Spring Boot)

First, create a Spring Boot application for your server in VSCode

We create a new folder for placing our servera application and we open the folder with VSCode

![image](https://github.com/luiscoco/Java_LongPolling_sample-/assets/32194879/bc2a8a79-fe39-4a61-904b-c2a7008eeeac)

We press **Ctrl+Shift+P** and select ****

![image](https://github.com/luiscoco/Java_LongPolling_sample-/assets/32194879/fd985093-5ac7-4942-a7fb-5c5d6f4ce70d)

We select the SpringBoot version

![image](https://github.com/luiscoco/Java_LongPolling_sample-/assets/32194879/37496917-5407-4712-85c1-ccbf7938b7f9)

We also input the project language

![image](https://github.com/luiscoco/Java_LongPolling_sample-/assets/32194879/856655f2-528e-4a67-9673-2f47ba028591)

We input the Group Id for the project 

![image](https://github.com/luiscoco/Java_LongPolling_sample-/assets/32194879/646a6680-0bf5-40b2-ae91-daa70157bcee)

We finally input the Artifact Id and press enter

![image](https://github.com/luiscoco/Java_LongPolling_sample-/assets/32194879/72c2e2a0-9c71-40f7-9b28-2c89d16017c0)

We also input the packaging type

![image](https://github.com/luiscoco/Java_LongPolling_sample-/assets/32194879/59e838ab-6002-4893-b298-1531f49814d3)



This is the server project files and folders structure

![image](https://github.com/luiscoco/Java_LongPolling_sample-/assets/32194879/7ac4314c-5077-4e4d-9172-447811e088e1)


You can use **Spring Initializr** (https://start.spring.io/) to bootstrap a new project with '**Spring Web**' dependency

First, let's create the **server application**

**LongPollingServerApplication.java**

```java
package com.example.longpolling;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
public class LongPollingServerApplication {
    public static void main(String[] args) {
        SpringApplication.run(LongPollingServerApplication.class, args);
    }
}
```

Now we input the controller file:

 **LongPollingController.java**

```java
package com.example.longpolling;

import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RestController;
import java.util.concurrent.CompletableFuture;
import java.util.concurrent.ExecutionException;

@RestController
public class LongPollingController {

    @GetMapping("/long-polling")
    public String longPollingRequest() throws InterruptedException, ExecutionException {
        CompletableFuture<String> futureData = getData();
        return futureData.get();
    }

    private CompletableFuture<String> getData() {
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

We also input the **pom.xml** file with the libraries dependencies

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

We also can provide the **application.properties** file

```

```


## 2. Java Client for Long Polling

This is the client project files and folders structure:

```css
long-polling-client/
├── src/
│   └── com/
│       └── example/
│           └── longpolling/
│               └── LongPollingClient.java
└── pom.xml (Optional, if you decide to use Maven for managing dependencies)
```

For a simple standalone Java application like this client, you might **not need a pom.xml** unless you plan to manage dependencies (e.g., for parsing JSON responses, etc.)

The client example provided doesn't require external libraries

Now, let's create a simple **Java client** that sends requests to the server and waits for the response.

Here's the complete **LongPollingClient.java** code for the client application that continuously sends long-polling requests to the server and processes the server's responses

**LongPollingClient.java**

```java
package com.example.longpolling;

import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.net.HttpURLConnection;
import java.net.URL;

public class LongPollingClient {

    public static void main(String[] args) {
        final String urlString = "http://localhost:8080/long-polling";
        while (true) {
            try {
                URL url = new URL(urlString);
                HttpURLConnection con = (HttpURLConnection) url.openConnection();
                con.setRequestMethod("GET");

                int status = con.getResponseCode();
                if (status == HttpURLConnection.HTTP_OK) {
                    BufferedReader in = new BufferedReader(new InputStreamReader(con.getInputStream()));
                    StringBuilder content = new StringBuilder();
                    String inputLine;
                    while ((inputLine = in.readLine()) != null) {
                        content.append(inputLine);
                    }
                    in.close();
                    System.out.println("Server response: " + content.toString());
                } else {
                    System.out.println("Error: Failed to get the response from server.");
                }
                con.disconnect();
            } catch (Exception e) {
                e.printStackTrace();
                // In case of an error, you might want to add a delay before retrying
                try {
                    Thread.sleep(5000); // Wait for 5 seconds before retrying
                } catch (InterruptedException ie) {
                    Thread.currentThread().interrupt();
                }
            }

            // Implement a backoff strategy or a condition to break the loop as needed
        }
    }
}
```

This client application will continuously send GET requests to the specified server endpoint (http://localhost:8080/long-polling)

It waits for the server to respond with "New data available!" (or any other server response) before immediately sending another request, effectively demonstrating the long-polling technique

Remember to handle errors gracefully and consider implementing a backoff strategy to avoid overwhelming the server or the network. This example immediately retries after receiving a response,

and in case of an error, it waits for 5 seconds before retrying, which is a basic strategy to manage continuous requests

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

