# Java LongPolling sample

**Long Polling**: Long polling is a technique where the client makes an HTTP request to the server, and the server keeps the connection open until new data is available

Once new data is available, the server responds to the request, and the client immediately sends another request to keep the connection open

To create a simple Java application that demonstrates the long polling technique, you'll need to set up both a server and a client. The server will wait for a certain condition

or data to be available before responding to the client's request. The client, after receiving the response, will immediately make another request to keep the connection open

Here's a basic example using Spring Boot for the server side to handle HTTP requests and a simple Java client using HttpURLConnection for making HTTP requests

## 1. Server Code (Spring Boot)

Setup Spring Boot Application. First, create a Spring Boot application for your server

You can use **Spring Initializr** (https://start.spring.io/) to bootstrap a new project with '**Spring Web**' dependency

Create a controller for handling long polling requests:

```java
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RestController;
import java.util.concurrent.CompletableFuture;
import java.util.concurrent.ExecutionException;

@RestController
public class LongPollingController {

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

## 2. Java Client for Long Polling

Now, let's create a simple **Java client** that sends requests to the server and waits for the response:

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

Run the Java client by executing the LongPollingClient class

The server will hold each client request for about 10 seconds (simulating data availability delay) before responding. Once the client receives the response, it immediately sends another request, demonstrating the long polling technique

Remember, this is a basic example for demonstration purposes. In a real-world application, you should consider error handling, efficient resource use, and scalability

