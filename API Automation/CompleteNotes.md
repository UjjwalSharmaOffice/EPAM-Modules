# Completes Note

## The Complete REST Assured Master Syllabus

### Module 1: Foundation & Architecture

* **The API Testing Landscape:** Advantages of REST Assured over manual testing in Postman, and comparing their workflows side-by-side.
* **Environment Configuration:** How to set up and configure REST Assured in a Java project (Maven) for secure API testing.
* **The BDD Syntax:** Understanding the architectural purpose behind the `Given()`, `When()`, and `Then()` structure.

### Module 2: Core API Automation

* **Basic Interactions:** Writing automation scripts for fundamental HTTP methods (GET, POST, PUT, DELETE).
* **Response Validation:** Deep dive into validating status codes, headers, and extracting dynamic body content.
* **Logging & Debugging:** *[Added Topic]* Implementing request and response logging to troubleshoot complex API calls.

### Module 3: Reusability & Data-Driven Frameworks

* **Parameters & Optimization:** How query and path parameters improve efficiency in automated frameworks.
* **Specifications:** *[Added Topic]* Using `RequestSpecification` and `ResponseSpecification` to eliminate redundant code across tests.
* **Data-Driven Testing:** Running automated tests with multiple dynamic data sets.

### Module 4: Object Mapping (Serialization & Deserialization)

* **The Concept:** Why we convert Java objects to JSON (serialization) and parse JSON responses into Java objects (deserialization).
* **Data Models:** Creating and testing POJOs (Plain Old Java Objects) to represent API payloads.
* **Efficiency:** Creating reusable methods for object mapping to drastically enhance test coverage and maintainability.

### Module 5: Securing the Gates (Authentication Mechanisms)

* **Authentication Overview:** Evaluating Basic Authentication, Bearer Tokens, and API Keys.
* **Postman vs. Code:** Applying authentication manually in Postman and translating that into automated REST Assured scripts.
* **OAuth 2.0 Deep Dive:** Mastering the Authorization flow, token generation, and the token refresh mechanism.
* **Security Standards:** Best practices for managing authentication and maintaining strict security protocols while testing.

### Module 6: Contract Testing via JSON Schema Validation

* **The Purpose:** Why schema validation is critical for ensuring data integrity and contract compliance in API responses.
* **Anatomy of a Schema:** Understanding the exact structure and rules defined within a JSON schema.
* **Implementation:** Creating and applying JSON schemas to validate highly dynamic and nested API responses.
* **Automation:** Validating API responses against predefined schemas using REST Assured's validation libraries.

## Module 1: Foundation & Architecture

### 1. The API Testing Landscape: Postman vs. REST Assured

Before writing code, it is crucial to understand *why* we choose REST Assured when a visually intuitive tool like Postman exists.

* **Postman** is a GUI-based tool excellent for exploratory testing, rapid debugging, and manual validation. It allows you to visually construct requests and inspect responses.
* **REST Assured** is a Java-based Domain Specific Language (DSL). It operates entirely through code and integrates seamlessly with Java testing frameworks (like JUnit or TestNG) and build tools (like Maven or Gradle).

**The Enterprise Application:** When building software at scale, or when proving your engineering capabilities in technical assessments, manual testing workflows are insufficient. An enterprise CI/CD pipeline requires tests that are version-controlled, highly modular, and capable of executing automatically every time new code is merged. Postman struggles to scale for massive, code-heavy automation frameworks. REST Assured, being pure Java, allows you to utilize Object-Oriented Programming (OOP) principles, loops, conditionally driven logic, and data structures to build a highly scalable, automated testing architecture.

---

### 2. Environment Configuration (Maven Setup)

To use REST Assured, we need to bring its libraries into our Java project. We do this using Maven, a powerful dependency management tool. Instead of downloading JAR files manually, Maven fetches the exact versions we need and ensures all internal dependencies are compatible.

The latest major release is **REST Assured 6.0.0** (which requires Java 17 or higher). We will pair it with **JUnit 5** to execute our tests.

Here is the exact `pom.xml` configuration you need to set up your environment:

```xml
<dependencies>
    <dependency>
        <groupId>io.rest-assured</groupId>
        <artifactId>rest-assured</artifactId>
        <version>6.0.0</version>
        <scope>test</scope>
    </dependency>

    <dependency>
        <groupId>org.junit.jupiter</groupId>
        <artifactId>junit-jupiter-api</artifactId>
        <version>5.10.1</version>
        <scope>test</scope>
    </dependency>
</dependencies>

```

---

### 3. The BDD Syntax: Given, When, Then

REST Assured is built heavily on **Behavior-Driven Development (BDD)** principles. This means the code is designed to read like plain, logical English. This architecture separates every API call into three distinct phases: `given()`, `when()`, and `then()`.

* **`given()` — The Setup:** This is where you prepare everything the request needs before it is sent. You define your authentication credentials, headers, query parameters, and the JSON payload (body) here.
* **`when()` — The Action:** This is the trigger. You specify the HTTP verb (GET, POST, PUT, DELETE) and the target URL/endpoint.
* **`then()` — The Validation:** This is where you assert the outcome. You check if the status code is correct (e.g., 200 OK), measure the response time, and validate that the response body contains the expected data.

#### The Magic of Static Imports

Because REST Assured relies heavily on its own internal methods, you must use **static imports**. This allows you to call `given()` directly, rather than typing `RestAssured.given()` every single time, keeping your code exceptionally clean.

Here is the architectural skeleton in Java. Note that this is not hitting a live endpoint yet; it is purely to demonstrate the structural flow you will use for every script moving forward.

```java
// Essential static imports for REST Assured
import static io.restassured.RestAssured.*;
import org.junit.jupiter.api.Test;

public class ApiTestStructure {

    @Test
    public void structuralBddFlow() {
        
        // The BDD Flow
        given()
            // SETUP PHASE:
            // e.g., .header("Content-Type", "application/json")
            // e.g., .body(jsonPayload)
                
        .when()
            // ACTION PHASE:
            // e.g., .post("https://api.example.com/users")
                
        .then();
            // VALIDATION PHASE:
            // e.g., .statusCode(201)
            // e.g., .log().all()
    }
}

```

This clean separation is why REST Assured is the industry standard for Java API testing. It makes tests immediately readable to any engineer who reviews your code.

---
### Module 2: Core API Automation (Part 1)

To integrate TestNG, we simply replace the JUnit dependency in the `pom.xml` with the TestNG dependency. TestNG is highly favored in the industry for automation because of its advanced data provider capabilities and parallel execution features, which we will leverage later.

```xml
<dependency>
    <groupId>org.testng</groupId>
    <artifactId>testng</artifactId>
    <version>7.8.0</version>
    <scope>test</scope>
</dependency>

```

We will use a free, publicly available mock API called Reqres (`https://reqres.in`) for all our live code examples. You can copy, paste, and run these directly in your IDE.

---

### 1. Visibility First: Logging & Debugging

Before making any requests, it is critical to know how to see the traffic. When an API test fails, you need to know exactly what was sent to the server and exactly what the server sent back.

REST Assured provides the `log()` method, which can be attached to both the request (`given()`) and the response (`then()`).

* `.log().all()` attached to `given()` prints the request headers, URI, and body to your console.
* `.log().all()` attached to `then()` prints the response status code, response headers, and response body.

This is your primary debugging tool.

---

### 2. The GET Request: Fetching Data

**The Concept:** A GET request is the simplest HTTP method. Its sole purpose is to retrieve data from a specific resource on the server. Because it only asks for data, a GET request generally does not have a "body" (payload). You are simply hitting a URL.

**The Implementation:** We will fetch a specific user from the mock API and validate that the server returns a `200 OK` status code.

```java
import org.testng.annotations.Test;
import static io.restassured.RestAssured.*;

public class GetRequestTest {

    @Test
    public void fetchSingleUserAndLog() {
        
        // Target endpoint: Fetching user with ID 2
        String endpoint = "https://reqres.in/api/users/2";

        given()
            // We want to see what we are sending (though minimal for GET)
            .log().all() 
        .when()
            .get(endpoint)
        .then()
            // We want to see the response from the server
            .log().all() 
            // Assert that the server successfully processed the request
            .statusCode(200); 
    }
}

```

---

### 3. The POST Request: Creating Data

**The Concept:** A POST request is used to send data to the server to create a new resource. Because we are transmitting data, we must provide two crucial things in the `given()` phase:

1. **The Payload (Body):** The actual data we want to send, typically in JSON format.
2. **The Content-Type Header:** We must explicitly tell the server what format our data is in. If we send JSON but do not define the `Content-Type` as `application/json`, the server might reject the request with a `415 Unsupported Media Type` or `400 Bad Request` error.

**The Implementation:** We will send a JSON string containing a user's name and job title to create a new user. For a successful creation, standard REST architecture returns a `201 Created` status code.

```java
import org.testng.annotations.Test;
import static io.restassured.RestAssured.*;

public class PostRequestTest {

    @Test
    public void createNewUser() {
        
        String endpoint = "https://reqres.in/api/users";
        
        // In Module 4, we will learn how to do this properly with POJOs.
        // For now, we use a simple Java String to represent the JSON payload.
        String jsonBody = "{\n" +
                "    \"name\": \"Ujjwal\",\n" +
                "    \"job\": \"Automation Engineer\"\n" +
                "}";

        given()
            // 1. Tell the server we are sending JSON
            .header("Content-Type", "application/json") 
            // 2. Attach the payload
            .body(jsonBody) 
            .log().all()
        .when()
            .post(endpoint)
        .then()
            .log().all()
            // Assert that the resource was successfully created
            .statusCode(201); 
    }
}

```

---

Switching strictly to `https://jsonplaceholder.typicode.com`. This is an excellent, highly reliable mock API for testing JSON structures.

### Module 2: Core API Automation (Part 2)

We have covered fetching (GET) and creating (POST) data. Now we will complete the CRUD lifecycle with updating (PUT) and removing (DELETE) data, followed by the most critical part of API testing: deeply validating the exact contents of the server's response.

---

### 4. The PUT Request: Updating Data

**The Concept:** A PUT request is used to completely replace or update an existing resource on the server. Just like a POST request, a PUT request requires a payload (the new data) and a `Content-Type` header so the server knows how to parse the incoming data. The key difference is the target: POST targets a collection (e.g., `/posts` to make a new one), while PUT targets a specific existing item (e.g., `/posts/1` to update post #1).

**The Implementation:** We will update the post with ID `1`. Standard REST APIs typically return a `200 OK` when an update is successful and echo the updated data back.

```java
import org.testng.annotations.Test;
import static io.restassured.RestAssured.*;

public class PutRequestTest {

    @Test
    public void updateExistingPost() {
        
        // Target endpoint: specifically pointing to post ID 1
        String endpoint = "https://jsonplaceholder.typicode.com/posts/1";
        
        // The updated payload
        String updatedJsonBody = "{\n" +
                "    \"id\": 1,\n" +
                "    \"title\": \"Updated Automation Framework\",\n" +
                "    \"body\": \"This is the newly updated content via REST Assured\",\n" +
                "    \"userId\": 1\n" +
                "}";

        given()
            .header("Content-Type", "application/json")
            .body(updatedJsonBody)
            .log().all()
        .when()
            .put(endpoint)
        .then()
            .log().all()
            // Asserting the update was successful
            .statusCode(200);
    }
}

```

---

### 5. The DELETE Request: Removing Data

**The Concept:** A DELETE request instructs the server to remove a specific resource. It generally does not require a payload or a `Content-Type` header because you are not sending data; you are simply providing the exact URL of the resource to be destroyed.

**The Implementation:** We will send a DELETE command to post ID `1`. Depending on the API's design, a successful deletion usually returns a `200 OK` (with a success message), a `202 Accepted` (if the deletion is queued), or a `204 No Content` (if it was deleted and the server intentionally returns no body). `jsonplaceholder` returns `200 OK`.

```java
import org.testng.annotations.Test;
import static io.restassured.RestAssured.*;

public class DeleteRequestTest {

    @Test
    public void deletePost() {
        
        String endpoint = "https://jsonplaceholder.typicode.com/posts/1";

        given()
            .log().all()
        .when()
            .delete(endpoint)
        .then()
            .log().all()
            // Asserting the deletion was acknowledged
            .statusCode(200);
    }
}

```

---

### 6. Deep Response Validation & Data Extraction

**The Concept:** Checking status codes is only 10% of API testing. The real automation happens when you parse the JSON response body and mathematically assert that the data returned is exactly what you expect. If an API returns a `200 OK` but gives you the wrong user's data, the test must fail.

To navigate through a JSON response, REST Assured uses **JSONPath**. This is exactly like XPath for XML or DOM traversal, but explicitly for JSON trees.

There are two primary ways to handle response data in REST Assured:

1. **Inline Validation (Hamcrest Matchers):** Asserting the value directly inside the `then()` block. This is fast and readable.
2. **Extraction:** Pulling the data out of the JSON response and storing it in a Java variable (like a `String` or `int`) so you can pass it to a subsequent API call (e.g., extracting an ID from a POST response to use in a GET request).

**The Implementation:** We will perform a GET request, validate the data inline using Hamcrest, and then extract a specific value into a Java variable.

*Note: To use inline validation, you must add the static import for Hamcrest Matchers.*

```java
import org.testng.annotations.Test;
import static io.restassured.RestAssured.*;
// Essential import for inline assertions (equalTo, hasItems, etc.)
import static org.hamcrest.Matchers.*; 

public class ResponseValidationTest {

    @Test
    public void validateAndExtractData() {
        
        String endpoint = "https://jsonplaceholder.typicode.com/posts/1";

        // --- METHOD 1: INLINE VALIDATION ---
        given()
        .when()
            .get(endpoint)
        .then()
            // Validate status code
            .statusCode(200)
            // Navigate the JSON tree using JSONPath and assert values
            .body("userId", equalTo(1))
            .body("id", equalTo(1))
            .body("title", equalTo("sunt aut facere repellat provident occaecati excepturi optio reprehenderit"));


        // --- METHOD 2: EXTRACTION TO JAVA VARIABLES ---
        // We use .extract().path("jsonPath") to pull the data out.
        
        int extractedUserId = 
            given()
            .when()
                .get(endpoint)
            .then()
                .extract()
                .path("userId"); // Pulls the value of "userId" from the JSON

        String extractedTitle = 
            given()
            .when()
                .get(endpoint)
            .then()
                .extract()
                .path("title");

        // Now you hold these values in standard Java variables 
        // and can use them anywhere in your framework.
        System.out.println("Extracted User ID: " + extractedUserId);
        System.out.println("Extracted Title: " + extractedTitle);
    }
}

```

### Module 3: Reusability & Data-Driven Frameworks

As your test suite grows from 5 tests to 500 tests, hardcoding URLs, repeating headers, and writing the same assertions over and over becomes unmanageable. This module focuses entirely on optimizing your code using DRY (Don't Repeat Yourself) principles and separating your test data from your test logic.

---

### 1. Parameters & Optimization: Path vs. Query

**The Concept:** APIs use parameters to make a single endpoint dynamic. Instead of building a unique URL for every single piece of data, servers use variables.

* **Path Parameters:** Used to identify a *specific* resource. They are part of the actual URL path (e.g., `/posts/1`).
* **Query Parameters:** Used to *filter*, sort, or search within a collection of resources. They appear at the end of the URL after a question mark (e.g., `/comments?postId=1`).

**The Implementation:** REST Assured provides `.pathParam()` and `.queryParam()` to handle these cleanly. This prevents you from having to do messy String concatenations (like `endpoint + "/" + id`).

```java
import org.testng.annotations.Test;
import static io.restassured.RestAssured.*;
import static org.hamcrest.Matchers.*;

public class ParametersTest {

    @Test
    public void usePathAndQueryParameters() {
        
        // Notice the {userId} placeholder in the URL. 
        // REST Assured will look for a pathParam matching this exact name.
        String endpoint = "https://jsonplaceholder.typicode.com/users/{userId}/posts";

        given()
            // 1. Path Parameter: Replaces {userId} with '2'
            .pathParam("userId", 2)
            
            // 2. Query Parameter: Appends '?id=15' to the end of the resolved URL
            .queryParam("id", 15)
            
            .log().all()
        .when()
            .get(endpoint)
        .then()
            .log().all()
            .statusCode(200)
            // Asserting that the post we got back actually belongs to user 2
            .body("[0].userId", equalTo(2))
            // Asserting that the specific post ID is 15
            .body("[0].id", equalTo(15));
    }
}

```

---

### 2. Specifications: Request & Response Builders

**The Concept:** In an enterprise framework, every single request to your API might require the same base URL, the same `Content-Type` header, and the same API key. Similarly, every successful response might need to be validated for a `200 OK` status code and a specific response time.

Instead of writing `.baseUri(...)` and `.header(...)` in every `@Test`, we bundle these common setups into a **Specification**.

* `RequestSpecification`: Bundles everything that goes into the `given()` phase.
* `ResponseSpecification`: Bundles everything that goes into the `then()` phase.

**The Implementation:** We use `RequestSpecBuilder` and `ResponseSpecBuilder` to construct these bundles once, usually in a base setup class, and then inject them into our tests.

```java
import io.restassured.builder.RequestSpecBuilder;
import io.restassured.builder.ResponseSpecBuilder;
import io.restassured.specification.RequestSpecification;
import io.restassured.specification.ResponseSpecification;
import org.testng.annotations.BeforeClass;
import org.testng.annotations.Test;
import static io.restassured.RestAssured.*;
import static org.hamcrest.Matchers.*;

public class SpecificationTest {

    // Declare the specifications at the class level
    RequestSpecification requestSpec;
    ResponseSpecification responseSpec;

    @BeforeClass
    public void setupSpecifications() {
        // Build the common Request Setup
        requestSpec = new RequestSpecBuilder()
                .setBaseUri("https://jsonplaceholder.typicode.com")
                .addHeader("Content-Type", "application/json")
                .build();

        // Build the common Response Validation
        responseSpec = new ResponseSpecBuilder()
                .expectStatusCode(200)
                // We can even enforce that all responses must be under 3000 milliseconds
                .expectResponseTime(lessThan(3000L)) 
                .build();
    }

    @Test
    public void testWithSpecifications() {
        // Notice how incredibly clean the actual test becomes!
        // We inject the specs directly into given() and then()
        given()
            .spec(requestSpec)
            .log().all()
        .when()
            // We only need to provide the specific route now, not the full URL
            .get("/posts/1")
        .then()
            .spec(responseSpec)
            .log().all()
            .body("id", equalTo(1));
    }
}

```

---

### 3. Data-Driven Testing (with TestNG)

**The Concept:** Imagine you need to test the `/posts` endpoint with 50 different sets of data. Writing 50 `@Test` methods is a terrible practice. **Data-Driven Testing (DDT)** allows you to write the API logic exactly once, and feed an array of data into it. TestNG achieves this using the `@DataProvider` annotation.

**The Implementation:** We will create a Data Provider method that returns a 2D Array of Objects (`Object[][]`). TestNG will automatically execute our test method multiple times, injecting a new row of data from the array each time.

```java
import org.testng.annotations.DataProvider;
import org.testng.annotations.Test;
import static io.restassured.RestAssured.*;
import static org.hamcrest.Matchers.*;

public class DataDrivenTest {

    // 1. The Data Provider: Holds our test data sets
    @DataProvider(name = "postData")
    public Object[][] createPostData() {
        return new Object[][] {
            // Title, Body, UserId
            {"Title One", "Body for first post", 1},
            {"Title Two", "Body for second post", 2},
            {"Title Three", "Body for third post", 3}
        };
    }

    // 2. The Test: Receives the data using the dataProvider attribute
    @Test(dataProvider = "postData")
    public void createMultiplePosts(String title, String body, int userId) {
        
        String endpoint = "https://jsonplaceholder.typicode.com/posts";

        // Constructing the JSON payload dynamically using the provided variables
        String jsonPayload = "{\n" +
                "    \"title\": \"" + title + "\",\n" +
                "    \"body\": \"" + body + "\",\n" +
                "    \"userId\": " + userId + "\n" +
                "}";

        given()
            .header("Content-Type", "application/json")
            .body(jsonPayload)
        .when()
            .post(endpoint)
        .then()
            .statusCode(201)
            // Asserting that the API correctly accepted and returned our dynamic data
            .body("title", equalTo(title))
            .body("userId", equalTo(userId));
    }
}

```
### Module 4: Object Mapping (Serialization & Deserialization)

Up until this point, we have been building our JSON payloads using Java `String` concatenation. In a production environment, manipulating massive, nested JSON strings manually is highly prone to syntax errors (like missing quotes or commas) and completely unmaintainable.

To solve this, the industry standard is to use **Object Mapping**.

### 1. The Concept

* **Serialization (Java $\rightarrow$ JSON):** The process of taking a Java object (which exists in your computer's memory) and converting it into a JSON formatted stream of text so it can be sent over the network in an HTTP request body (POST/PUT).
* **Deserialization (JSON $\rightarrow$ Java):** The exact reverse. Taking the JSON text response from the server and converting it back into a usable Java object so you can easily validate its fields using standard Java methods.

**The "Why":** By using Object Mapping, you gain **type safety**. Your IDE will catch errors before you even run the code. You get auto-completion for your JSON fields, and you can reuse these objects across hundreds of tests.

---

### 2. The Setup: Adding a Data Binder

REST Assured does not serialize/deserialize natively; it delegates this task to a library. The most popular library in the Java ecosystem for this is **Jackson**.

Add this dependency to your `pom.xml` so REST Assured can map objects behind the scenes:

```xml
<dependency>
    <groupId>com.fasterxml.jackson.core</groupId>
    <artifactId>jackson-databind</artifactId>
    <version>2.16.0</version>
</dependency>

```

---

### 3. The Data Model (POJO)

A **POJO** (Plain Old Java Object) is a simple class that acts as a blueprint for your JSON.

If we look at the JSON returned by `https://jsonplaceholder.typicode.com/posts/1`, it looks like this:

```json
{
  "userId": 1,
  "id": 1,
  "title": "sunt aut facere...",
  "body": "quia et suscipit..."
}

```

We map this directly to a Java class. The variable names in the Java class must exactly match the keys in the JSON.

Here is the `Post` POJO:

```java
public class Post {
    
    // Private fields representing the JSON keys
    private int userId;
    private int id; // The server generates this, but we include it for deserialization
    private String title;
    private String body;

    // Default constructor (Required by Jackson for Deserialization)
    public Post() {
    }

    // Parameterized constructor (Useful for Serialization/Creating new posts)
    public Post(int userId, String title, String body) {
        this.userId = userId;
        this.title = title;
        this.body = body;
    }

    // Getters and Setters (Required by Jackson to access and write to the fields)
    public int getUserId() { return userId; }
    public void setUserId(int userId) { this.userId = userId; }

    public int getId() { return id; }
    public void setId(int id) { this.id = id; }

    public String getTitle() { return title; }
    public void setTitle(String title) { this.title = title; }

    public String getBody() { return body; }
    public void setBody(String body) { this.body = body; }
}

```

---

### 4. Serialization (POSTing an Object)

Now, instead of writing a messy JSON String, we simply instantiate our `Post` class and pass the object directly into the `.body()` method. REST Assured and Jackson will automatically translate the object into JSON before sending it.

```java
import org.testng.annotations.Test;
import static io.restassured.RestAssured.*;

public class SerializationTest {

    @Test
    public void serializeJavaObjectToJson() {
        
        String endpoint = "https://jsonplaceholder.typicode.com/posts";

        // 1. Create the Java Object using our POJO
        Post newPost = new Post(10, "Automated Object Mapping", "This body was generated from a POJO.");

        given()
            .header("Content-Type", "application/json")
            // 2. Pass the object directly. Jackson handles the serialization seamlessly.
            .body(newPost)
            .log().all()
        .when()
            .post(endpoint)
        .then()
            .log().all()
            .statusCode(201);
    }
}

```

---

### 5. Deserialization (Extracting to an Object)

Instead of using JSONPath to extract individual strings and integers one by one (which is tedious for large responses), we can pull the *entire* response directly into a Java object using the `.as(Class)` method.

Once it is a Java object, we use standard TestNG Assertions (`Assert.assertEquals`) and our POJO's getter methods to validate the data. This is heavily preferred in enterprise automation.

```java
import org.testng.Assert;
import org.testng.annotations.Test;
import static io.restassured.RestAssured.*;

public class DeserializationTest {

    @Test
    public void deserializeJsonToJavaObject() {
        
        String endpoint = "https://jsonplaceholder.typicode.com/posts/1";

        // 1. Hit the endpoint and map the response DIRECTLY to our Post class
        Post extractedPost = 
            given()
            .when()
                .get(endpoint)
            .then()
                .statusCode(200)
                .extract()
                // 2. The magic happens here: JSON is mapped to the Post object
                .as(Post.class); 

        // 3. Validate using pure Java and TestNG Asserts
        // We know post 1 belongs to userId 1
        Assert.assertEquals(extractedPost.getUserId(), 1, "User ID mismatch!"); 
        
        // We ensure the title is not null and has content
        Assert.assertNotNull(extractedPost.getTitle(), "Title should not be null");
        
        System.out.println("Successfully deserialized post title: " + extractedPost.getTitle());
    }
}

```

### 6. Handling Arrays of Objects

Often, an API returns a list of items (e.g., getting all posts via `/posts`). You can easily deserialize a JSON array into a Java Array of objects.

```java
import org.testng.Assert;
import org.testng.annotations.Test;
import static io.restassured.RestAssured.*;

public class DeserializationArrayTest {

    @Test
    public void deserializeJsonArray() {
        
        String endpoint = "https://jsonplaceholder.typicode.com/posts";

        // Map the JSON array directly to a Java Array of Post objects
        Post[] allPosts = 
            given()
            .when()
                .get(endpoint)
            .then()
                .statusCode(200)
                .extract()
                // Use the Array class type
                .as(Post[].class); 

        // Now you can use standard Java array operations
        Assert.assertTrue(allPosts.length > 0, "The posts array should not be empty");
        
        System.out.println("Total posts fetched: " + allPosts.length);
        System.out.println("Title of the 5th post: " + allPosts[4].getTitle());
    }
}

```

### Module 5: Securing the Gates (Authentication Mechanisms)

In the real world, APIs do not leave their endpoints completely open. They contain sensitive user data, financial records, or proprietary systems. Authentication is the process of proving *who* you are, and Authorization is the process of verifying *what* you are allowed to do.

REST Assured has built-in mechanisms to handle all modern security protocols seamlessly.

---

### 1. The Concept: Postman vs. Automated Code

* **In Postman:** You typically go to the "Authorization" tab, select a type (like Basic Auth or Bearer Token) from a dropdown menu, paste your credentials, and Postman automatically formats and injects the hidden headers for you behind the scenes.
* **In REST Assured:** We must explicitly define these authorization methods in the `given()` phase. REST Assured will then construct the exact same HTTP headers that Postman builds before transmitting the request over the network.

---

### 2. Basic Authentication

**The Concept:** Basic Authentication is the simplest form of API security. It requires a standard username and password. When you send these credentials, the client (REST Assured) takes the string `username:password`, encodes it using Base64, and places it into an HTTP header named `Authorization` with the prefix `Basic `.

**The Implementation:** REST Assured provides a dedicated `.auth().basic()` method so you do not have to handle the Base64 encoding yourself. We will use Postman's public echo server, which is explicitly designed to test Basic Auth.

```java
import org.testng.annotations.Test;
import static io.restassured.RestAssured.*;
import static org.hamcrest.Matchers.*;

public class BasicAuthTest {

    @Test
    public void testBasicAuthentication() {
        
        // This endpoint expects username "postman" and password "password"
        String endpoint = "https://postman-echo.com/basic-auth";

        given()
            // Applying Basic Auth credentials
            .auth().preemptive().basic("postman", "password")
            .log().all()
        .when()
            .get(endpoint)
        .then()
            .log().all()
            // If auth fails, this would return 401 Unauthorized. 
            // We assert 200 OK to prove our credentials worked.
            .statusCode(200)
            .body("authenticated", equalTo(true));
    }
}

```

*Note on `.preemptive()`: By default, REST Assured uses "challenge" authentication (it waits for the server to ask for credentials via a 401 response, then sends them). `preemptive()` forces REST Assured to send the credentials on the very first request, saving network time and avoiding unnecessary 401 errors.*

---

### 3. Bearer Tokens & API Keys

**The Concept:** * **Bearer Tokens:** Instead of sending a username and password with every single request (which is a massive security risk), modern applications usually require you to log in once. The server then gives you a long, encrypted string called a Token (often a JWT - JSON Web Token). You "bear" this token in the header of all subsequent requests.

* **API Keys:** Similar to tokens, but usually long-lived and assigned to a developer or application rather than a specific user session. They are often passed in headers (e.g., `x-api-key`).

**The Implementation:** Both of these are fundamentally just manipulating HTTP headers.

```java
import org.testng.annotations.Test;
import static io.restassured.RestAssured.*;

public class TokenAuthTest {

    @Test
    public void testBearerToken() {
        
        String endpoint = "https://httpbin.org/bearer";
        String myToken = "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.dummy_token_payload";

        given()
            // Standard HTTP format: Authorization: Bearer <token>
            .header("Authorization", "Bearer " + myToken)
            .log().all()
        .when()
            .get(endpoint)
        .then()
            .log().all()
            .statusCode(200);
    }

    @Test
    public void testApiKeyInHeader() {
        
        // Example structure for an API Key header validation
        String endpoint = "https://api.example.com/secure-data";
        String apiKey = "live_51H...secret_key";

        given()
            // Custom header usually defined by the API documentation
            .header("x-api-key", apiKey)
        .when()
            .get(endpoint)
        .then()
            // Depending on the mock/live server used
            .statusCode(200); 
    }
}

```

---

### 4. OAuth 2.0 Deep Dive

**The Concept:** OAuth 2.0 is the industry gold standard. It is not an authentication protocol, but an *authorization framework*. If you have ever clicked "Log in with Google" or "Log in with GitHub" on a third-party website, you have used OAuth 2.0.

Instead of giving your password to a third-party application, OAuth follows this flow:

1. **Authorization Request:** You ask the Authorization Server (e.g., Google) for permission.
2. **Authorization Grant:** The user logs in and grants permission.
3. **Access Token Request:** Your automated script takes that grant and asks for an Access Token.
4. **Resource Request:** You use that Access Token to hit the actual API endpoints.

**Automating OAuth in REST Assured:**
Because OAuth 2.0 requires dynamic token generation (tokens expire rapidly, often in 15-60 minutes), hardcoding a token in your framework will cause tests to fail tomorrow.

The industry best practice is to automate the token retrieval *before* your test runs, extract the token, and inject it into your API calls.

```java
import org.testng.annotations.BeforeClass;
import org.testng.annotations.Test;
import static io.restassured.RestAssured.*;

public class OAuth2Test {

    private String validAccessToken;

    @BeforeClass
    public void generateOAuthToken() {
        // Step 1: Hit the Authorization Server to dynamically get a fresh token
        // (This replaces the manual Postman "Get New Access Token" button)
        
        String authServerUrl = "https://oauth2.googleapis.com/token";
        
        // These values are securely provided by the API provider
        String clientId = "your_client_id";
        String clientSecret = "your_client_secret";
        String refreshToken = "your_refresh_token";

        // Extract the token dynamically
        validAccessToken = 
            given()
                .formParam("client_id", clientId)
                .formParam("client_secret", clientSecret)
                .formParam("refresh_token", refreshToken)
                .formParam("grant_type", "refresh_token")
            .when()
                .post(authServerUrl)
            .then()
                .statusCode(200)
                .extract()
                .path("access_token");
                
        System.out.println("Generated fresh token for test run.");
    }

    @Test
    public void useOAuthTokenToFetchData() {
        // Step 2: Use the fresh, dynamically generated token to access the secure resource
        String secureEndpoint = "https://www.googleapis.com/drive/v3/files";

        given()
            // REST Assured provides a direct method for OAuth2
            .auth().oauth2(validAccessToken)
        .when()
            .get(secureEndpoint)
        .then()
            .statusCode(200);
    }
}

```

---

### 5. Security Best Practices in Automation

When building enterprise automation frameworks, follow these absolute rules:

1. **Never Hardcode Secrets:** Never commit passwords, API keys, or client secrets in your Java code. Use environment variables, a `config.properties` file ignored by Git, or a secrets manager (like AWS Secrets Manager or HashiCorp Vault).
2. **Dynamic Tokens over Static:** Always build a `@BeforeClass` or `@BeforeSuite` method to dynamically fetch and refresh OAuth tokens before running the test suite. If a token expires halfway through a 500-test execution suite, your tests will generate false negatives (failing because of auth, not because of a bug).
3. **Scope Minimization:** If your test only needs to read data, ensure the API key or OAuth token generated only has "Read" scopes, not "Admin" or "Write" permissions, to limit the blast radius if the credential leaks.

### Module 6: Contract Testing via JSON Schema Validation

We have covered validating specific data points (e.g., asserting that `userId` equals `1`). However, in the real world, API data is highly dynamic. If you query a list of newly registered users, the IDs, names, and timestamps will change every single second. You cannot hardcode assertions for data that constantly shifts.

This is where **Contract Testing** and **JSON Schema Validation** become critical.

### 1. The Purpose: Testing the Shape, Not the Value

**The Concept:** Instead of asking, *"Is the user's name Ujjwal?"*, Schema Validation asks, *"Did the server return a 'name' field, and is its value a String?"*

A JSON Schema acts as a binding contract between the backend developers (who build the API) and the automation engineers (who test it). It ensures data integrity. If a backend developer accidentally changes the `id` field from an `integer` to a `string`, or completely removes the `email` field, your schema validation test will instantly catch the contract breach, even if the API still returns a `200 OK`.

---

### 2. The Setup: Adding the Validator Library

REST Assured requires a specific, separate module to perform schema validation. You must add the `json-schema-validator` dependency to your `pom.xml`. It is vital that the version matches your core REST Assured version exactly.

```xml
<dependency>
    <groupId>io.rest-assured</groupId>
    <artifactId>json-schema-validator</artifactId>
    <version>6.0.0</version>
    <scope>test</scope>
</dependency>

```

---

### 3. Anatomy of a JSON Schema

Before automating, we need the schema itself. Let's look at a standard response from our reliable endpoint: `https://jsonplaceholder.typicode.com/posts/1`

**The JSON Response:**

```json
{
  "userId": 1,
  "id": 1,
  "title": "sunt aut facere...",
  "body": "quia et suscipit..."
}

```

**The JSON Schema:**
To create a schema, you define the data `type` for every single field and specify which fields are absolutely `required`. You save this as a `.json` file (e.g., `post-schema.json`). In a standard Maven Java project, this file must be placed in the `src/test/resources` directory so the compiler can easily find it during test execution.

Here is the exact schema for the post response:

```json
{
  "$schema": "http://json-schema.org/draft-04/schema#",
  "type": "object",
  "properties": {
    "userId": {
      "type": "integer"
    },
    "id": {
      "type": "integer"
    },
    "title": {
      "type": "string"
    },
    "body": {
      "type": "string"
    }
  },
  "required": [
    "userId",
    "id",
    "title",
    "body"
  ]
}

```

Notice the `required` array at the bottom. If the API returns a response missing the `title` field, the schema validator will throw an exception and fail the test, protecting your application from null pointer exceptions down the line.

---

### 4. Implementation: Automating Schema Validation

**The Implementation:** Now we write the Java code. We will hit the endpoint and, instead of extracting values or asserting specific strings, we will pass the entire response body through the `matchesJsonSchemaInClasspath()` method.

REST Assured will automatically scan the `src/test/resources` folder for your file, read the rules, and validate the incoming JSON against them.

```java
import org.testng.annotations.Test;
import static io.restassured.RestAssured.*;
// Essential static import for the Schema Validator module
import static io.restassured.module.jsv.JsonSchemaValidator.matchesJsonSchemaInClasspath;

public class SchemaValidationTest {

    @Test
    public void validatePostResponseAgainstSchema() {
        
        String endpoint = "https://jsonplaceholder.typicode.com/posts/1";

        given()
            .log().all()
        .when()
            .get(endpoint)
        .then()
            .log().all()
            .statusCode(200)
            // This single line performs the complete structural validation.
            // It verifies data types, required fields, and the overall JSON hierarchy.
            .body(matchesJsonSchemaInClasspath("post-schema.json"));
    }
}

```

### 5. Handling Complex, Nested Responses

If your API returns a nested JSON object (an object inside an object), the schema simply nests the `properties` block. Schema validation handles infinitely deep JSON structures effortlessly.

When you build enterprise frameworks, you will typically maintain a dedicated folder within `src/test/resources` (e.g., `src/test/resources/schemas/`) containing dozens of `.json` files representing every single contract your application relies upon.

---

