# spring boot core code

. configuration
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

. controller

```java
@Controller
public class WebSocketController {

    @MessageMapping("/hello")
    @SendTo("/topic/messages")
    public String sendSpecific(String msg){
        System.out.println(msg);
        return "hello websocket 1";
    }

}

```