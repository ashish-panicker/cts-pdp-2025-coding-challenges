# **Problem — Generic Graph Implementation With Type Constraints**

### **Problem Statement**

Design a generic directed graph:

```java
public class Graph<N extends Comparable<N>, W extends Number> { }
```

Where:

* `N`: node type

  * must be `Comparable<N>`
* `W`: edge weight type

  * must be a subtype of `Number`

Requirements:

1. Add nodes and weighted edges
2. Implement a generic method:

```java
public <T extends N> List<T> getNeighbours(T node);
```

3. Implement another method:

```java
public <T extends Number> double totalWeight();
```

### Difficulty areas:

* Generic class with 2 bounded type parameters
* Methods introducing their own type parameters
* Maintaining type-safety while supporting any comparable node type

# **Problem  — Create a Generic Cache With Expiration**

### **Problem Statement**

Create a generic in-memory cache class:

```java
public class Cache<K, V> { }
```

Requirements:

1. Keys should support **generic lower-bound search**:

   ```java
   V get(Object key);
   ```

2. Add a method:

```java
public void putAll(Map<? extends K, ? extends V> data)
```

3. Add a method that returns only values of a given subtype:

```java
public <T extends V> List<T> getValuesOfType(Class<T> type);
```

4. Handle type erasure by maintaining runtime metadata using:

* Class<T>
* Or a custom **TypeToken<T>**

### Concepts tested:

* Wildcards (`extends`, `super`)
* Dealing with type erasure safely
* Runtime type filtering (`instanceof`, `Class::isAssignableFrom`)
* Designing API with flexible generics

---

# **Problem — Build a Generic Serializer Framework**

### **Problem Statement**

Create a generic serializer:

```java
public interface Serializer<T> {
    byte[] serialize(T obj);
    T deserialize(byte[] data);
}
```

Then create:

1. A registry:

   ```java
   public class SerializerRegistry {
       <T> void register(Class<T> type, Serializer<? super T> serializer);
       <T> Serializer<T> get(Class<T> type);
   }
   ```

2. You must support:

```java
register(Number.class, serializerForNumber);
register(Integer.class, serializerForInteger);
```

and ensure correct resolution:

* Integer → Integer serializer
* Long → Number serializer, if Integer serializer unavailable

### Concepts tested:

* Lower-bounded generics (`? super T`)
* Runtime type lookup
* Handling type erasure in registry
* Generic type inference
