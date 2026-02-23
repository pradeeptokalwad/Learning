
#what is reactive RestTemplate vs WebClient?

RestTemplate vs WebClient
Synchronous, blocking client | Asynchronous, non‑blocking client
Each request blocks the thread until completion | Uses reactive programming (Project Reactor) with Mono/Flux
Simple, traditional applications where blocking is acceptable | High‑throughput, reactive applications needing scalability
Still usable, but deprecated in favor of WebClient |	Recommended for new projects
Direct objects (e.g., User)	| Reactive types (Mono, Flux)

# RestTemplate (Blocking):
  - The thread waits until the response is received.
    RestTemplate restTemplate = new RestTemplate();
    User user = restTemplate.getForObject("https://api.example.com/users/1", User.class);
    System.out.println(user.getName());

# WebClient (Reactive):
  - The call is non‑blocking; the thread can continue doing other work while waiting for the response.
  
  WebClient client = WebClient.create("https://api.example.com");
  Mono<User> userMono = client.get()
      .uri("/users/1")
      .retrieve()
      .bodyToMono(User.class);
  
  userMono.subscribe(user -> System.out.println(user.getName()));

# Flow: 
https://copilot.microsoft.com/shares/VAbfFBZRtMEWyYgaqkUC8

1. Client → Controller

  A client sends a request.
  
  The @RestController receives it and returns a Mono (single result) or Flux (stream).

2. Controller → Service

  The controller delegates to the service layer.
  
  The service may call repositories or external APIs.
  
  It can return either Mono or Flux depending on the data.

3. Service → Repository

  Repository queries (e.g., reactive database calls) often return Flux for multiple rows or Mono for a single row.

4. Response → Client

  The reactive pipeline completes asynchronously.
  
  The client receives the response once the stream emits values.


# JSON Mapping:

  public class Response {
      @JsonProperty("token_value")
      private String token;
  
      @JsonProperty("username")
      private String name;
  
      @JsonProperty("isValid")
      private boolean validity;
  }

# Manual Mapping:
  Mono<Response> response = client.get()
    .uri("/auth")
    .retrieve()
    .bodyToMono(JsonNode.class)
    .map(json -> new AuthResponse(
        json.get("token").asText(),
        json.get("username").asText(),
        json.get("isValid").asBoolean()
    ));

# Auto mapping:(JSON keys match your model’s field names then mapping will happen automatically)

  WebClient client = WebClient.create("https://api.example.com");
  
  Mono<AuthResponse> response = client.get()
      .uri("/auth")
      .retrieve()
      .bodyToMono(AuthResponse.class);

