# spring boot core code

## configuration
```java
@Configuration
@EnableWebSocketMessageBroker
public class WebSocketConfig implements WebSocketMessageBrokerConfigurer {

    @Override
    public void configureMessageBroker(MessageBrokerRegistry registry) {
        registry.enableSimpleBroker("/topic");
        registry.setApplicationDestinationPrefixes("/app");
        registry.setPathMatcher(new AntPathMatcher("."));
    }

    @Override
    public void registerStompEndpoints(StompEndpointRegistry registry) {
        registry.addEndpoint("/stomp-endpoint");
        registry.addEndpoint("/stomp-endpoint").setAllowedOrigins("*").withSockJS();
    }
}

```

## controller

```java
@Controller
public class WebSocketController {

    @MessageMapping("/hello")
    @SendTo("/topic/messages")
    public String sayHello(String msg){
        System.out.println(msg);
        return msg;
    }

}

```

## js core

```javascript
var socket = new SockJS('http://localhost:8080/stomp-endpoint');
    stompClient = Stomp.over(socket);
    stompClient.connect({}, function (frame) {
        setConnected(true);
        console.log('Connected: ' + frame);
        stompClient.subscribe('/topic/messages', function (response) {
            console.log(response)
            showGreeting(response);
        });
    });

```
> new SockJS() construct need `http` protocol instead of `ws`

## When send message ?
For example, after we finished payment recall we need to tell the runner to delivery.Like this:
```java
	@Resource
    private SimpMessagingTemplate simpMessagingTemplate;

	/**
     * payment recall
     */
    public void payNotify() {
        simpMessagingTemplate.convertAndSend("/topic/messages","hello websocket");// To notice frontend
        listenerBean.trigger();//To notice backend
    }

```

> If no client subscribe `/topic/messages`, `sayHello` will not work.