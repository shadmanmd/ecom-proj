# SPRING BOOT NOTES

## Spring MVC Architecture

M - Model → To declare the models like data & business logic

V - View → Returns what the user sees, html or json

C - Controller → interacts with the end user

In REST-based Spring Boot applications, the View layer is often replaced by JSON responses, and the frontend is handled separately.

M - Model

S - Service → Part of Model (business logic)

C - Controller

R - Repository → Part of Model (data access)

## Real world Flow

Client (React / Postman)

↓

Controller (@RestController)

↓

Service (business logic)

↓

Repository (DB access)

↓

Database

## Spring Web Annotations
### 1. Controller Layer Annotations

> @**Controller** - mark a class as a web controller that handles HTTP requests and returns a view (like HTML)
> 
> @**RestController** - web controller that handles HTTP requests and returns JSON (for backend APIs); generally preferred over @Controller

### 2. Request Mapping Annotations
> @**RequestMapping** - marks a function as a route to handle generic i.e. all GET, POST, PUT, DELETE, PATCH request; NOT PREFERRED
> 
> @**GetMapping** - route to handle GET request
> 
> @**PostMapping** | @**PutMapping** | @**DeleteMapping** | @**PatchMapping**

### 3. Parameter Binding Annotations
> @**RequestParam**  - to fetch query parameters
> 
> @**PathVariable** - to fetch URL params - '/product/{id}'
> 
> @**RequestBody** - JSON / Java object - mostly used in POST requests

```java
// Example

import org.springframework.web.bind.annotation.*;

@GetMapping("/search")
public String search(@RequestParam String name) {
}

@GetMapping("/users/{id")
public String getUser(@PathVariable int id) {
}

@PostMapping("/users")
public String createUser(@RequestBody User user) {
}
```

### 4. Response Control Annotations
> @**ResponseBody** - returns JSON ; already included in @RestController
>
> @**ResponseStatus** - sets HTTP status manually ; @ResponseStatus(HttpStatus.CREATED)

```java
// Example

@GetMapping("/hello")
@ResponseBody
public String hello() {
    return "Hello World"; // returned directly as HTTP response
}

@PostMapping("/users")
@ResponseStatus(HttpStatus.CREATED)
public User createUser(@RequestBody User user) {
    return user; // status 201 + response body
}
```

### 5. Exception Handling Annotations
> @**ExceptionHandler** - Handles specific exceptions in a controller (or globally with advice).
> 
> @**ControllerAdvice** - Provides global exception handling across all controllers.
> 
> @**RestControllerAdvice** - Same as @ControllerAdvice but returns responses directly as JSON (no view).
> 
```java
// Example

@ExceptionHandler(RuntimeException.class)
@ResponseBody
public String handleRuntimeException(RuntimeException ex) {
    return "Something went wrong: " + ex.getMessage();
}

@ControllerAdvice
public class GlobalHandler {

    @ExceptionHandler(Exception.class)
    @ResponseBody
    public String handleAll(Exception ex) {
        return "Global error: " + ex.getMessage();
    }
}

@RestControllerAdvice
public class GlobalRestHandler {

    @ExceptionHandler(IllegalArgumentException.class)
    public String handleIllegalArg(IllegalArgumentException ex) {
        return "Invalid input: " + ex.getMessage();
    }
}
```

### 6. Miscellaneous Annotations
> 
> @**CrossOrigin** - placed on top of Controllers to avoid CORS error in accessing the APIs

## Spring JPA (Java Persistence API)
### Annotations

> @**Repository** - marks an interface as a repository; this interface will extend the JpaRepository and the JPA functions will be used via this interface
> 
> @**Entity** - marks a class as a table
> 
> @**Id** - sets an attribute as a primary key
> 
> @**GeneratedValue**(strategy = GenerationType.IDENTITY) - Tells JPA to let the database auto-generate the primary key using its identity/auto-increment column.

```java
import jakarta.persistence.Entity;
import jakarta.persistence.Id;

// Example

// model / Product.java
@Entity
public class Product {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private String id;

    private String name;
}

// repo / ProductRepo.java
@Repository
public interface ProductRepo extends JpaRepository<Product, Integer> {
}

// Here in JpaRepository<Product, Integer> --> Product is the model name and Integer is the data type of the primary key
```

## Dependency Injection
// Will write here
> @**Autowired** - For Value injection - See ProductController.java

## Lombok

Lombok is a dependency that helps to reduce boilerplate code by writing just annotations instead of constructors, getter & setter methods.

### Lombok Annotations
> 
> @**Data** - Generates getters, setters, toString(), equals(), and hashCode() automatically for a class.
> @**AllArgsConstructor** - Generates a constructor with parameters for all fields in the class.
> 
> @**NoArgsConstructor** - Generates a default (no-argument) constructor for the class.
> 

## Connecting to DB
```text
// application.properties

// H2 DB is an in-memory Database that can be accessed on the browser
spring.datasource.url=jdbc:h2:mem:example
// Syntax --> jdbc:<db-type>:<connection-details>

// For Postgres
spring.datasource.url=jdbc:postgresql://localhost:5432/mydb
// Syntax --> jdbc:postgresql://<host>:<port>/<database>
```
