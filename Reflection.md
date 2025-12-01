#  **Mini Dependency Injection (DI) Container**

### *A Hands-On Reflection Project for Senior Java Engineers*

This project requires you to build a **lightweight DI container** (similar to Spring’s BeanFactory / ApplicationContext) using **Reflection + Annotations**.

You will implement:

* Component scanning
* Bean instantiation
* Constructor injection
* Field injection
* Singleton scope
* Circular dependency detection

This README serves as a **complete problem statement** + **step-by-step guide**.

---

# **1. Project Overview**

Modern Java frameworks (Spring, Micronaut, Quarkus) rely heavily on **runtime reflection** to identify and instantiate components, inject dependencies, and wire complete object graphs.

In this project, you will build a simplified DI system that mimics Spring’s core container features.

---

# **2. Learning Objectives**

After completing this project, you will:

### Understand how Spring DI works internally

### Master reflection:

* Class scanning
* Constructor discovery
* Field injection
* Annotation processing
* Method invocation

### Build dynamic object graphs at runtime

### Implement lightweight IoC with minimal code

### Understand bean lifecycle management

---

# **3. Features to Implement**

Your DI container must support:

---

## **3.1 Custom Annotations**

### `@Component`

Marks a class as a managed bean.

```java
@Retention(RUNTIME)
@Target(TYPE)
public @interface Component {}
```

---

### `@Inject`

Marks a dependency for injection.

```java
@Retention(RUNTIME)
@Target({FIELD, CONSTRUCTOR})
public @interface Inject {}
```

---

### `@Configuration` + `@Bean` (Optional / Bonus)

To register explicit bean creation methods.

---

## **3.2 Component Scanning**

Create a class scanner that:

* Loads a base package (e.g., `"com.example"`)
* Finds all classes annotated with `@Component`
* Registers them inside a BeanDefinition map

---

## **3.3 Bean Creation**

For each `@Component` class:

* Detect suitable constructor
* Use reflection to instantiate
* Support constructor injection

Example:

```java
@Component
public class OrderService {

    private final InventoryService inv;

    @Inject
    public OrderService(InventoryService inv) {
        this.inv = inv;
    }
}
```

---

## **3.4 Field Injection**

Allow:

```java
@Component
public class PaymentService {

    @Inject
    private Logger logger;
}
```

Process:

* Identify fields with `@Inject`
* Instantiate dependency
* Inject using reflection (`field.setAccessible(true)`)

---

## **3.5 Singleton Scope Management**

Store a **singleton instance** of each bean:

```
Map<Class<?>, Object> singletonCache;
```

Ensure:

* Beans are created once
* Dependencies refer to the same instance
* No duplicate instantiation

---

## **3.6 Circular Dependency Detection**

Example:

```
A depends on B  
B depends on A
```

Detect and throw:

```
CircularDependencyException
```

Hint: Maintain a **creatingStack**

---

## **3.7 Minimal “Container API”**

Container must provide:

### `getBean(Class<T> type)`

Returns a singleton bean of the requested type.

### `initialize(String basePackage)`

Starts component scanning & bean creation.

---

# **4. Project Structure (Recommended)**

```
mini-di/
  ├── src/main/java/
  │     ├── annotations/
  │     │     ├── Component.java
  │     │     ├── Inject.java
  │     │
  │     ├── core/
  │     │     ├── ApplicationContext.java
  │     │     ├── BeanDefinition.java
  │     │     ├── BeanFactory.java
  │     │     ├── ClassScanner.java
  │     │
  │     ├── examples/
  │     │     ├── App.java
  │     │     ├── OrderService.java
  │     │     ├── InventoryService.java
  │     │     ├── PaymentService.java
```

---

# **5. Step-by-Step Implementation Requirements**

## **Step 1: Create Annotations**

Implement:

* `@Component`
* `@Inject`

Everything must use `RetentionPolicy.RUNTIME`.

---

## **Step 2: Implement Class Scanner**

Your scanner must:

* Accept base package name
* Convert it into file path
* Read `.class` files dynamically
* Load class using `Class.forName`
* Filter classes annotated with `@Component`

---

## **Step 3: Build BeanDefinition Registry**

Create a registry:

```
Map<Class<?>, BeanDefinition>
```

Your `BeanDefinition` contains:

* Class<?> beanClass
* Constructor<?> injectableConstructor
* List<Field> injectableFields

---

## **Step 4: Implement BeanFactory**

Responsibilities:

### Create singleton instance

### Perform constructor injection

### Perform field injection

### Store bean in cache

### Detect circular references

Factory pseudocode:

```
Object createBean(Class<?> clazz):
   if (bean in cache) return cached bean
   if (clazz in currentlyCreatingStack) throw circular exception

   push clazz to stack
   create instance (constructor injection)
   set fields (field injection)
   save to cache
   remove clazz from stack

   return instance
```

---

## **Step 5: Build ApplicationContext**

High-level orchestrator:

* Trigger class scanning
* Register beans
* Instantiate singleton beans
* Provide `getBean()` API

Example:

```java
ApplicationContext context = new ApplicationContext("com.example");

OrderService os = context.getBean(OrderService.class);
```

---

# **6. Example Usage**

### Example Component Classes

```java
@Component
public class InventoryService {
    public String checkStock() {
        return "Stock checked!";
    }
}
```

---

```java
@Component
public class OrderService {

    private final InventoryService inventory;

    @Inject
    public OrderService(InventoryService inventory) {
        this.inventory = inventory;
    }
}
```

---

```java
@Component
public class PaymentService {

    @Inject
    private Logger logger;

    public void pay() {
        logger.log("Payment done.");
    }
}
```

---

### Running the App

```java
public static void main(String[] args) {
    ApplicationContext ctx = new ApplicationContext("com.example");

    OrderService os = ctx.getBean(OrderService.class);
    os.placeOrder();

    PaymentService ps = ctx.getBean(PaymentService.class);
    ps.pay();
}
```

---

# **7. Bonus Challenges**

### Challenge 1 — Add Prototype Scope

Support:

```java
@Component
@Scope("prototype")
public class RandomGenerator {}
```

---

### Challenge 2 — Add @PostConstruct

Call initialization methods automatically.

---

### Challenge 3 — Add Simple AOP

Wrap beans using **Dynamic Proxy** to add cross-cutting behaviors:

* Logging
* Timing
* Transactions

---

### Challenge 4 — Add MethodHandles

Use MethodHandles instead of reflection for getter/setter & constructor access.

---

# **8. Testing Requirements**

Write tests for:

* Constructor injection
* Field injection
* Singleton enforcement
* Circular dependency detection
* Component scanning
* Missing bean errors

---

# **9. What This Simulates in Real Frameworks**

This exercise builds intuition behind:

| Feature                       | Like in Spring                            |
| ----------------------------- | ----------------------------------------- |
| Component scanning            | ClassPathBeanDefinitionScanner            |
| Constructor Injection         | AutowiredAnnotationBeanPostProcessor      |
| BeanDefinition                | BeanDefinitionHolder                      |
| BeanFactory                   | DefaultListableBeanFactory                |
| Singleton Cache               | ConcurrentHashMap-based SingletonRegistry |
| Circular dependency detection | EarlySingletonObjects in Spring           |

You will truly understand how Spring Core works.
Just say:
**"Generate starter code for the mini DI container"**
