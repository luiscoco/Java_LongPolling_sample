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




## 2. 
