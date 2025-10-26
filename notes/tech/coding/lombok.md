---
title: Lombok
date: 2025-10-12
---
# Lombok

## Lombok vs Java records

Java Records and Project Lombok both aim to reduce boilerplate code in Java, but they do so with different philosophies and capabilities.

**Java Records** are a feature of the Java language (since JDK 16) designed to be simple, transparent carriers for immutable data. Think of them as a streamlined way to create data-centric classes.

**Project Lombok** is a third-party library that uses annotations to generate boilerplate code for regular Java classes at compile-time. It's more flexible and feature-rich than Records.

Here’s a breakdown of their differences and when to use each:

### Java Records 📜

Records are ideal when you need a simple, immutable object to hold data. They are concise and a core part of the Java language, so they don't require any extra dependencies.

**Key Features:**

* **Immutable by Default:** All fields are `private` and `final`, and there are no setters.
* **Concise Syntax:** You declare the fields in the record header, and the compiler automatically generates:
  * A canonical constructor (that takes all the fields).
  * `public` accessor methods (e.g., `name()` instead of `getName()`).
  * Implementations of `equals()`, `hashCode()`, and `toString()`.
* **No Inheritance:** Records cannot extend other classes.

**Example of a Java Record:**

```java
public record User(String username, String email) {}
```

-----

### Project Lombok ⚙️

Lombok is a powerful tool for reducing boilerplate in a wide variety of situations, offering more flexibility and customization than Records.

**Key Features:**

* **Flexible Annotations:** You can pick and choose which boilerplate to generate using annotations like `@Getter`, `@Setter`, `@ToString`, `@EqualsAndHashCode`, `@NoArgsConstructor`, `@AllArgsConstructor`, and more.
* **Mutability Control:** You can create both mutable and immutable classes. For mutable classes, you can use `@Data` or a combination of `@Getter` and `@Setter`. For immutable classes, `@Value` is a good choice.
* **Builder Pattern:** The `@Builder` annotation is a standout feature, allowing you to easily create complex objects with a fluent API.
* **Works with Existing Classes:** You can add Lombok annotations to any class, including those that extend other classes.

**Example of a Lombok Class:**

```java
import lombok.Data;

@Data // Generates getters, setters, toString(), equals(), and hashCode()
public class User {
    private String username;
    private String email;
}
```

-----

## Use Cases: When to Use Which?

### Use Java Records For:

* **Data Transfer Objects (DTOs):** Records are perfect for transferring data between different layers of your application, like from a service to a REST controller.
* **API Responses:** When returning data from an API, records provide a simple and immutable structure.
* **Configuration Properties:** Their immutability makes them great for holding configuration data that shouldn't change.
* **Simple Data Aggregates:** When you need to group a few pieces of data together, like for a map key or a return value from a method.

### Use Lombok For:

* **JPA Entities:** JPA requires a no-argument constructor and mutable fields, which records don't provide. Lombok's `@Entity`, `@Getter`, `@Setter`, and `@NoArgsConstructor` annotations are well-suited for this.
* **Mutable Classes:** When you need objects whose state can change over time, Lombok's `@Setter` is the way to go.
* **Complex Object Creation:** For classes with many fields, especially optional ones, Lombok's `@Builder` is much cleaner than a long constructor.
* **More Control and Customization:** If you need to customize the generated code, like excluding certain fields from `toString()` or `equals()`, Lombok provides the necessary options.

### Quick Comparison Table

| Feature | Java Records | Project Lombok |
| :--- | :--- | :--- |
| **Type** | Native language feature | Third-party library |
| **Immutability** | Immutable by default | Can be mutable or immutable |
| **Boilerplate** | Automatically generates constructor, accessors, `equals()`, `hashCode()`, `toString()` | Generates code based on annotations |
| **Flexibility** | Less flexible, all-or-nothing | Highly flexible and customizable |
| **Builder Pattern**| Not included | `@Builder` annotation |
| **Inheritance** | Cannot extend classes | Can extend classes |
| **Best For** | DTOs, API responses, simple immutable data | JPA entities, mutable classes, complex objects |

## `@Data` annotation

You should use Lombok's `@Data` annotation sparingly, mostly for simple, non-critical data containers like those in tests or internal prototypes. Avoid it in production code, especially for JPA entities or any class where you need to control mutability and behavior carefully.

The core issue with `@Data` is that it's a blunt instrument that prioritizes convenience over clarity and safety.

-----

### ✅ When to Use `@Data`

Use `@Data` in situations where encapsulation and fine-grained control are not a primary concern:

* **Rapid Prototyping:** When you're quickly building a proof-of-concept and just need a simple data-holding class to get started.

* **Unit Tests:** It's great for creating simple, mutable test objects where you need to set various fields to test a specific scenario.

* **Simple, Internal DTOs:** For Data Transfer Objects that are only used internally within a microservice and don't represent a core domain object, `@Data` can be acceptable, though an immutable `@Value` or Java Record is often a better choice.

Java

```java
// Good use case: A simple, temporary data holder for a test
@Data
class UserTestPayload {
    private String username;
    private List<String> permissions;
}
```

-----

### 🚫 When to AVOID `@Data`

For most real-world application code, you should avoid `@Data`:

* **JPA Entities:** This is the most critical place to avoid `@Data`. It causes several well-known issues (see gotchas below).

* **Immutable Objects:** `@Data` creates setters, making your objects mutable. For objects that shouldn't change after creation, use **`@Value`** or a **Java Record**.

* **Public APIs:** Exposing setters for all fields creates a "leaky" and overly permissive API. You lose control over how your object's state can be modified.

* **Complex Domain Objects:** Core business objects should have carefully designed methods that enforce business rules, not generic setters that allow any field to be changed at any time.

-----

### ⚠️ The Gotchas: Hidden Dangers of `@Data`

`@Data` is a bundle of five other annotations: `@Getter`, `@Setter`, `@ToString`, `@EqualsAndHashCode`, and `@RequiredArgsConstructor`. The problems arise from the default behavior of these annotations when used together blindly.

#### 1. Unintended Mutability

The biggest gotcha is that **`@Data` adds a public setter for every field**. This breaks encapsulation, a core principle of object-oriented programming.1 It allows any other part of your code to modify the object's state directly, which can lead to bugs that are hard to track down.

#### 2. `@EqualsAndHashCode` with JPA Entities

Using the default `equals()` and `hashCode()` on a JPA entity is dangerous.

* **Lazy Loading:** If the methods access a lazy-loaded relationship field (e.g., a `@OneToMany` list), it can trigger a database query and potentially a `LazyInitializationException` if the session is closed.

* **Primary Keys:** The generated methods use all fields. If an entity's primary key (`id`) is `null` before it's saved, it can't be reliably found in a `Set` or `Map`.

#### 3. `@ToString` and Circular Dependencies

If you have a bidirectional relationship (e.g., an `Order` has a `Customer`, and the `Customer` has a list of `Orders`), calling `toString()` on either object will create an infinite loop, resulting in a **`StackOverflowError`**. 😱

Java

```java
// This will cause a StackOverflowError if you use @Data on both classes
class Order {
    private Customer customer;
}

class Customer {
    private List<Order> orders;
}
```

#### 4. No No-Arg Constructor

Many frameworks, like Jackson (for JSON) and JPA, require a no-argument constructor to create instances of a class. `@Data` does **not** provide one if you have any `final` fields. You would have to add `@NoArgsConstructor` manually.

### What to Use Instead

Instead of the all-or-nothing `@Data`, be explicit. This makes your code clearer and safer.

**For a typical mutable JPA Entity:**

Java

```java
import lombok.*;

@Getter
@Setter
@ToString
@NoArgsConstructor // Required by JPA
@AllArgsConstructor
@Entity
public class User {
    @Id
    private Long id;
    private String name;

    @ToString.Exclude // Prevents circular dependency issues
    @OneToMany(mappedBy = "user")
    private List<Post> posts;

    // Manually implement or use @EqualsAndHashCode(of = "id")
    @Override
    public boolean equals(Object o) {
        // ... implementation using only the 'id' field
    }

    @Override
    public int hashCode() {
        // ... implementation using only the 'id' field
    }
}
```

**For an immutable DTO:**

Use a **Java Record** (if you're on JDK 16+) or Lombok's **`@Value`**.

Java

```java
// Using a Java Record (preferred)
public record UserDto(Long id, String name) {}

// Using Lombok's @Value
@Value
public class UserDto {
    Long id;
    String name;
}
```

## Lombok Best Practices and Pitfalls

Using Lombok annotations in Java is a great way to reduce boilerplate code, but it's important to use them thoughtfully to avoid hidden problems. The best practice is to be **explicit and intentional** rather than using catch-all annotations.

Here are the best practices and common pitfalls to avoid.

-----

## ✅ Best Practices

### 1. Prefer Specific Annotations Over `@Data`

Instead of using the all-in-one `@Data` annotation, favor using more specific ones. This gives you finer control and makes your class's intent clearer.

* **Use:** `@Getter`, `@ToString`, `@EqualsAndHashCode`, `@RequiredArgsConstructor`

* **Why:** `@Data` includes `@Setter` for all fields, which can unintentionally make your objects mutable. It also includes `@EqualsAndHashCode`, which can cause issues with JPA entities (more on that below).1 Being explicit forces you to think about whether a field should be modifiable.

### 2. Embrace Immutability with `@Value`

For data transfer objects (DTOs) or any class that shouldn't change after creation, **`@Value`** is your best friend. It's the immutable equivalent of `@Data`.

* **What it does:** It makes all fields `private` and `final` by default and generates getters, `toString()`, `equals()`, `hashCode()`, and an all-args constructor. No setters are generated. 🚀

* **Example:**

  Java

  ```java
  import lombok.Value;

  @Value // Creates an immutable class
  public class UserProfile {
      String username;
      String email;
  }
  ```

### 3. Use `@RequiredArgsConstructor` for Dependency Injection

When using `final` fields (a good practice for required dependencies), **`@RequiredArgsConstructor`** is the perfect fit. It generates a constructor only for `final` fields and fields marked with `@NonNull`. This is the standard pattern for constructor-based dependency injection.

* **Example:**

  Java

  ```java
  import lombok.RequiredArgsConstructor;
  import org.springframework.stereotype.Service;

  @Service
  @RequiredArgsConstructor // Creates a constructor for final fields
  public class UserService {
      private final UserRepository userRepository; // Injected by the constructor
      private final EmailService emailService;
  }
  ```

### 4. Use `@Builder` for Complex Objects

When a class has many fields, especially optional ones, a builder pattern is much cleaner than a massive constructor. **`@Builder`** generates the builder implementation for you.

* **Tip:** Combine it with `@Value` for a fluent and immutable object creation experience.

### 5. Standardize Logging with `@Slf4j`

Instead of manually instantiating a logger in every class, use a logging annotation. **`@Slf4j`** is the most popular choice.

* **What it does:** It generates a `private static final` logger field named `log`.

* **Example:**

  Java

  ```java
  import lombok.extern.slf4j.Slf4j;

  @Slf4j
  public class MyService {
      public void doSomething() {
          log.info("Doing something important...");
      }
  }
  ```

-----

## ⚠️ Pitfalls to Avoid

### 1. The `@Data` Overuse Problem

As mentioned, `@Data` is convenient but dangerous.

* **Pitfall:** It generates public setters for every field, breaking encapsulation and making objects mutable by default. This can lead to unpredictable state changes in your application.

* **How to Avoid:** **Don't use `@Data`**. Be intentional. Use `@Getter` and only add `@Setter` on the specific fields that truly need to be changed after object creation. For immutable objects, use `@Value`.

### 2. `@EqualsAndHashCode` with JPA/Hibernate Entities

This is one of the most common and serious pitfalls. 😵

* **Pitfall:** JPA entities have a primary key (like `id`) that is often `null` until the entity is saved to the database. If `equals()` and `hashCode()` include this `id`, an unsaved entity will have a different hash code than a saved one. This can break behavior when using them in `Set`s or as keys in a `Map`. Furthermore, if the methods access lazy-loaded collections, they can trigger a `LazyInitializationException`.

* **How to Avoid:**

  * **Exclude related entities:** Use `@EqualsAndHashCode.Exclude` on any fields that represent relationships (e.g., `@OneToMany` collections).

  * **Use a business key:** If you have a stable, unique field that isn't the generated ID (like a `UUID` or `email`), you can tell Lombok to use only that: `@EqualsAndHashCode(of = "uuid")`.

  * **Implement manually:** For JPA entities, sometimes it's safest to write your own `equals()` and `hashCode()` methods that rely only on the primary key, with proper `null` checks.

### 3. `@ToString` with Bidirectional Relationships

* **Pitfall:** If you have two classes that reference each other (e.g., an `Order` class with a `Customer` field, and a `Customer` class with a `List<Order>`), calling `toString()` on one will call `toString()` on the other, which then calls it back on the first, leading to an infinite loop and a **`StackOverflowError`**. 😱

* **How to Avoid:** On the "many" side of the relationship (the back-reference), exclude the field from the `toString()` method.

  * **Example:** In the `Customer` class:

    Java

    ```java
    import lombok.ToString;
    import java.util.List;

    @ToString
    public class Customer {
        private Long id;
        private String name;

        @ToString.Exclude // Prevents the infinite loop!
        private List<Order> orders;
    }
    ```

### 4. `@Builder` with Collections

* **Pitfall:** By default, if you use `@Builder` on a class with a collection field and don't explicitly set it, the field will be `null` after calling `.build()`. This can easily lead to `NullPointerException`s later.

* **How to Avoid:** Initialize the collection field directly and annotate it with **`@Builder.Default`**.

  * **Example:**

    Java

    ```java
    import lombok.Builder;
    import java.util.ArrayList;
    import java.util.List;

    @Builder
    public class Team {
        @Builder.Default // Ensures the list is never null
        private List<String> members = new ArrayList<>();
    }

    // Now this is safe:
    Team team = Team.builder().build(); // team.getMembers() is an empty list, not null.
    ```

  * **Bonus Tip:** Use the **`@Singular`** annotation for a nicer builder API for collections (`.member("John")` instead of `.members(List.of("John"))`).

## Lombok and Code Analyzers

You primarily address code scanner issues with Lombok by ensuring the generated code is properly **excluded from analysis and coverage reports** and by making sure the scanner can **resolve all class dependencies**, including the Lombok library itself.

Here is how you can configure SonarQube (the most common scanner facing this issue) to understand Lombok annotations:

### 1. Configure Lombok for Exclusion

The most common issue is that scanners like SonarQube's built-in analysis or the JaCoCo coverage tool will flag Lombok-generated methods (like getters, setters, constructors) as un-covered or report code smells like "unused private fields."

You can fix this by instructing Lombok to annotate its generated code so that other tools can ignore it.

* **Create a `lombok.config` file:** Place this file in your project's root directory (or in `src/main/java/` if you only want it applied to the main source set).

* **Add the configuration:** Include the following line in the file:

  Properties

  ```java
  lombok.addLombokGeneratedAnnotation = true
  ```

  This setting tells Lombok to add the `$@lombok.Generated` annotation to all its generated methods. Modern versions of **JaCoCo** and **SonarQube's Java analyzer** are configured to automatically ignore code annotated with `$@Generated` or `$@lombok.Generated$` for code coverage and certain code smell rules.

* **Optional Cleanup:** You may also include:

  Properties

  ```java
  config.stopBubbling = true
  ```

  This prevents Lombok from searching for other `lombok.config` files in parent directories.

### 2. Ensure Lombok is Available to the Analyzer

Static analyzers need to correctly build the Abstract Syntax Tree (AST) of your code. For code using Lombok, this often requires the analyzer to have access to the **Lombok JAR** when performing the analysis.

* **For Maven/Gradle users:** When running the SonarQube analysis, you often need to ensure the **Lombok dependency is included in the project's libraries or classpath** that SonarQube examines.

  * **Maven:** If you're using `sonar:sonar`, the process often picks up the necessary artifacts. If you encounter "unused private field" issues (S1068), it may indicate that the analyzer can't see the generated code. A common fix is to explicitly point SonarQube to the Lombok JAR:

    XML

    ```xml
    <properties>
        <sonar.java.libraries>path/to/lombok-*.jar</sonar.java.libraries>
        </properties>
    ```

    A more robust solution in a Maven/CI environment is to use the `maven-dependency-plugin` to copy `lombok.jar` to a known location, and then point `sonar.java.libraries` to that location.

  * **Gradle:** Similarly, ensure your `build.gradle` configuration for SonarQube/JaCoCo is correctly referencing the compiled classes and dependencies. Often, adding the following can help if issues persist:

    Groovy

    ```groovy
    // Example for Gradle (paths may vary)
    property 'sonar.java.libraries', fileTree('path/to/your/lombok-jar').include('*.jar').getAsPath()
    ```

The specific configuration details can depend heavily on your build tool (Maven/Gradle) and the exact version of SonarQube and its Java analyzer, but the two steps above are the core principles for compatibility.
