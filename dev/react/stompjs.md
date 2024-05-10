# StompJS

## Introduction
- When we need to push data from the server to the frontend, we can use WebSocket, which provides fast bi-directional communication. With WebSocket, we only need to open up a single long-lived conection, and messages can be exchanged instantly. With polling, the client would have to open a connection and make requests at regular time interval. This is inefficient and not suitable for real-time applications chat, stock trading, or sports update. With polling, resources are wasted because connections can be opened and closed even when there is no new data from the backend.

## What is STOMP
- STOMP is a Simple Text Oriented Messaging Protocol (also supports binary data). It offers an easier way to send and receives messages over WebSocket, since you don't have to deal with the lower level WebSocket implmentation.
- StompJS is a JavaScript implementation of the STOMP protocol. 

## Spring backend
- The Spring backend configures a message broker and defines the endpoint mappings that clients connect to using STOMP over WebSockets.
  - It could be simple in-memory broker like the Spring built-in Simple Broker, or a full-fledged one like RabbitMQ.
  - In our Spring app, we use the built-in SimpleBroker.

## Queue vs. Topic
- Use a queue if a message is user-specific, and you want the message to be consumed by only one user.
- Use a Topic if you want to broadcast a message so that all users receive the same message.

## Code

### Spring

1. Configure STOMP over WebSocket using the built-in SimpleBroker. The brokerURL is ```/gs-guide-websocket```, and the destinationPrefix is ```/topic```. Make sure to enable CORS with ```.setAllowedOriginPatterns("*")```.
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

2. Use SimpMessagingTemplate to send messages to a specific topic
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

3. Trigger the sendNotification() method via a scheduled task
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

### React

1. Use the ```@stomp/stompjs``` library to communicate with the backend via STOMP over WebSocket
2. On component mount, create a stompJS Client with ```'ws://localhost:8080/gs-guide-websocket'``` as the brokerURL. The websocket server will be listening for connections. Then, subscribe to the ```'/topic/notifications'``` topic after the connection has been established. In the ```subscribe()``` method, the callback method gets called every time there's a new message pushed to the topic. You can also use ```publish()``` to send a message to the websocket server.
```
import { Client } from '@stomp/stompjs';

useEffect(() => {
    let stompClient = stompClientRef.current;
    stompClient = new Client({
      brokerURL: 'ws://localhost:8080/gs-guide-websocket',
      // debug: (val: string) => {
      //   console.log('STOMP:', val);
      // },
      reconnectDelay: 5000,
      heartbeatIncoming: 4000,
      heartbeatOutgoing: 4000,
    });

    stompClient.onConnect = (frame) => {
      // Subscribe to the topic
      stompClient.subscribe('/topic/notifications', (message) => {
        const newMessage = JSON.parse(message.body);
        // setMessages(prevMessages => [...prevMessages, newMessage]);
        setLatestMessage(newMessage);
      })

      // stompClient.publish({
      //   destination: '/app/hello',
      //   body: ''
      // })
    }

    stompClient.onWebSocketError = (error) => {
      console.error('Error with websocket', error);
    };
    
    stompClient.onStompError = (frame) => {
      console.error('Broker reported error: ' + frame.headers['message']);
      console.error('Additional details: ' + frame.body);
    };

    stompClient.activate();

    return () => {
      stompClient.deactivate(); // Disconnect
    }
  }, [])
```

- **brokerURL:** This is the URL to the STOMP broker. The brokerURL directly points to the WebSocket endpoint where the STOMP broker is accessible (e.g., ws://example.com/stomp).
- **reconnectDelay:** This is the delay in milliseconds before attempting to reconnect after a connection failure. Setting it to 5000 means that the client will attempt to reconnect every 5 seconds.
- **heartbeatIncoming:** This sets the maximum allowed time in milliseconds between receiving messages from the server. If the heartbeat frames are not received within this interval, the connection may be considered broken. 4000 milliseconds here means 4 seconds.
- **heartbeatOutgoing:** This sets the interval in milliseconds at which the client will send heartbeat frames to the server to indicate that it is alive. This is also set to 4000 milliseconds or 4 seconds.
