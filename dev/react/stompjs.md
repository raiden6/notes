# StompJS

## What is STOMP
- STOMP is a Simple Text Oriented Messaging Protocol (also supports binary data)
- It is a subprotocol on top of WebSocket

## Components
- The Spring backend configures a message broker and defines the endpoint mappings that clients connect to using STOMP over WebSockets.
  - It could be simple in-memory broker or a full-fledged on like RabbitMQ.
  - In out Spring app, we use the built-in Simple Broker.

## Queue vs. Topic
- Use a queue if you want to a message is user-specific, and you want the message to be consumed by only one user.
- Use a Topic if you want to broadcast a message so that all users receive the same message.


## Code

### Spring

1. Configure STOMP over WebSocket using the built-in Simple Broker. The brokerURL is ```/gs-guide-websocket```, and the destinationPrefix is ```/topic```. Make sure to enable CORS with ```.setAllowedOriginPatterns("*")```.
```
import org.springframework.context.annotation.Configuration;
import org.springframework.messaging.simp.config.MessageBrokerRegistry;
import org.springframework.web.socket.config.annotation.EnableWebSocketMessageBroker;
import org.springframework.web.socket.config.annotation.StompEndpointRegistry;
import org.springframework.web.socket.config.annotation.WebSocketMessageBrokerConfigurer;

@Configuration
@EnableWebSocketMessageBroker
public class WebSocketConfig implements WebSocketMessageBrokerConfigurer {

  @Override
  public void configureMessageBroker(MessageBrokerRegistry config) {
    config.enableSimpleBroker("/topic");
    config.setApplicationDestinationPrefixes("/app");
  }

  @Override
  public void registerStompEndpoints(StompEndpointRegistry registry) {
    registry.addEndpoint("/gs-guide-websocket").setAllowedOriginPatterns("*");
  }

}
```

2. Use SimpMessagingTemplate to send messages subscribed to a specific topic
```
import org.springframework.messaging.simp.SimpMessagingTemplate;
import org.springframework.stereotype.Controller;

import com.home.mockbackend.domain.Greeting;

@Controller
public class NotificationController {
	
  private final SimpMessagingTemplate template;

  public NotificationController(SimpMessagingTemplate template) {
      this.template = template;
  }
  
  public void sendNotification() {
      String content = "This is a notification message from the server!";
      template.convertAndSend("/topic/notifications", new Greeting(content));
  }

}
```

3. Trigger the the sendNotification() method via a scheduled task
```
@SpringBootApplication
@EnableScheduling
public class MockBackendApplication {

  public static void main(String[] args) {
    SpringApplication.run(MockBackendApplication.class, args);
  }

  @Autowired
  private NotificationController notificationController;
  
  @Scheduled(fixedRate = 5000) // runs every 5000 milliseconds (5 seconds)
  public void scheduledNotification() {
    notificationController.sendNotification();
  }

}
```
