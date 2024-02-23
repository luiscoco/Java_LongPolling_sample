# Java LongPolling sample

The source code for this sample is available in this github repo: https://github.com/luiscoco/Java_LongPolling_sample

**Long Polling**: Long polling is a technique where the client makes an HTTP request to the server, and the server keeps the connection open until new data is available

Once new data is available, the server responds to the request, and the client immediately sends another request to keep the connection open

To create a simple Java application that demonstrates the long polling technique, you'll need to set up both a server and a client

The server will wait for a certain condition  or data to be available before responding to the client's request. The client, after receiving the response, will immediately make another request to keep the connection open

Here's a basic example using Spring Boot for the server side to handle HTTP requests and a simple Java client using HttpURLConnection for making HTTP requests

## 1. Create the Server (Spring Boot application) with VSCode

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

And we select the Java version

![image](https://github.com/luiscoco/Java_LongPolling_sample-/assets/32194879/24632591-3abb-4208-b755-064a108f5207)

In the dependencies we select **Spring Web** and press enter

![image](https://github.com/luiscoco/Java_LongPolling_sample-/assets/32194879/c8c4e8a3-1e50-44ee-b3ef-4828a4798822)

We have to select the folder where to place our project

![image](https://github.com/luiscoco/Java_LongPolling_sample-/assets/32194879/2992a513-503f-4b24-8213-5d89d07cd36d)

We press the **Open** button to start the project in another VSCode instance

![image](https://github.com/luiscoco/Java_LongPolling_sample-/assets/32194879/69979e80-e734-49b2-9a8e-02acc929223d)

This is the default server project files and folders structure

![image](https://github.com/luiscoco/Java_LongPolling_sample-/assets/32194879/73867784-bd2b-435f-971c-52659c775d6e)

Now we have to create the server project files

![image](https://github.com/luiscoco/Java_LongPolling_sample-/assets/32194879/7ac4314c-5077-4e4d-9172-447811e088e1)

First, let's input the files codeç

**LongPollingServerApplication.java**

```java
package com.example.longpollingserver;

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
package com.example.longpollingserver;

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

We also input the **pom.xml** file with the libraries dependencies

**pom.xml for Server**

```
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
	<modelVersion>4.0.0</modelVersion>
	<parent>
		<groupId>org.springframework.boot</groupId>
		<artifactId>spring-boot-starter-parent</artifactId>
		<version>3.2.3</version>
		<relativePath/> <!-- lookup parent from repository -->
	</parent>
	<groupId>com.example</groupId>
	<artifactId>long-polling-server</artifactId>
	<version>0.0.1-SNAPSHOT</version>
	<name>long-polling-server</name>
	<description>Demo project for Spring Boot</description>
	<properties>
		<java.version>21</java.version>
	</properties>
	<dependencies>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-web</artifactId>
		</dependency>

		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-test</artifactId>
			<scope>test</scope>
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
# Server port (optional, default is 8080)
server.port=8081

# Context path (optional, default is "/")
#server.servlet.context-path=/
```

## 2. Java Client for Long Polling

We create the client application following similar procedure as detailed in section 1 for the server

This is the project structure

![image](https://github.com/luiscoco/Java_LongPolling_sample/assets/32194879/9065a8c2-46d0-4546-8cc8-eb1a32e4a9c3)

For a simple standalone Java application like this client, you might **not need a pom.xml** unless you plan to manage dependencies (e.g., for parsing JSON responses, etc.)

The client example provided doesn't require external libraries

Now, let's create a simple **Java client** that sends requests to the server and waits for the response.

Here's the complete **LongPollingClientApplication.java** code for the client application that continuously sends long-polling requests to the server and processes the server's responses

**LongPollingClientApplication.java**

```java
package com.example.longpollingclient;

import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.net.HttpURLConnection;
import java.net.URL;

public class LongPollingClientApplication {

    public static void main(String[] args) {
        final String urlString = "http://localhost:8081/long-polling";
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

**Server Application**: Import the server application into your IDE as a Maven or Gradle project, depending on what you chose when setting up the Spring Boot application

Run the DemoApplication class to start the server

**Client Application**: Compile and run the **LongPollingClient.java** using your IDE or the command line

Ensure the server application is running before you start the client

This setup demonstrates a basic long polling mechanism

The client continuously sends requests to the server and waits for a response

The server, upon receiving a request, simulates a delay to represent waiting for new data and then responds, triggering the client to immediately send another request

In a real-world application, you should consider error handling, efficient resource use, and scalability

### 3.1. Running the Server

We click in the left side menu the **Spring Boot Dashboard** button

![image](https://github.com/luiscoco/Java_LongPolling_sample/assets/32194879/b15ffbc8-d650-4d67-9110-98bc00de7856)

We press the run button

![image](https://github.com/luiscoco/Java_LongPolling_sample/assets/32194879/414e9156-a299-41e4-a85b-a689bcd37dca)

We can see the server was started in port 8081 HTTP

![image](https://github.com/luiscoco/Java_LongPolling_sample/assets/32194879/02ec480c-71af-4ea3-a76e-7e2f99554ca7)

### 3.2. Running the Client

After running the server application we can proceed to run the client application

![image](https://github.com/luiscoco/Java_LongPolling_sample/assets/32194879/f9d63649-c471-4196-a7b1-fbdd0398a49b)

We confirm we received the data from the server

![image](https://github.com/luiscoco/Java_LongPolling_sample/assets/32194879/de2fe087-a234-42b8-b86d-4ab614ed9841)
