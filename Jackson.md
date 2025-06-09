
### ✅ What is Serialization?

**Serialization** is the process of converting a **Java object into a JSON string** so that it can be sent over the network, typically in an HTTP response.

* Jackson serializes the object's fields (or getter method return values) into JSON properties.
* The output is always a **valid JSON string**.
* By default, field names are used as JSON keys unless annotations override them.

---

### ✅ What is Deserialization?

**Deserialization** is the reverse process: it takes a JSON string (usually received in an HTTP request body) and converts it back into a Java object by mapping JSON keys to Java fields or setter methods.

---

## 🔧 Jackson Annotations

### 📍 `@JsonGetter`

The `@JsonGetter` annotation is used on a **method** to mark it as the JSON property **getter** during serialization.

#### Usage:

```java
@JsonGetter("customName")
public String getName() {
    return this.name;
}
```

#### Key Points:

* Marks a method as the source of a JSON property.
* The method:

  * Must be non-static.
  * Should take no arguments.
  * Should return the value to be serialized.
* The name of the JSON property is:

  * Derived from the method name (e.g., `getName()` becomes `"name"`) by default.
  * Can be customized using `@JsonGetter("customName")` or `@JsonProperty("customName")`.
* Avoid conflicts by ensuring that no two methods produce the same JSON property name.

#### Why use it?

* Useful when you want **custom logic** in the getter (e.g., computed values).
* Helpful for **renaming** properties during serialization without altering field names.

---

### 📍 `@JsonProperty`

While `@JsonGetter` is used on methods, `@JsonProperty` can be applied on **fields or methods** and controls both **serialization and deserialization**.

#### Example:

```java
@JsonProperty("full_name")
private String name;
```

* It renames the JSON key to `"full_name"` while binding to the `name` field in Java.
* Useful when your API contracts require specific naming.

---

### 📍 `@JsonPropertyOrder`

The `@JsonPropertyOrder` annotation is used at the **class level** to define the **ordering of properties** during serialization.

#### Example:

```java
@JsonPropertyOrder({"id", "name", "email"})
public class User {
    private String name;
    private int id;
    private String email;
    private String phone; // will appear after the above three, ordered alphabetically if alphabetic=true
}
```

#### Key Points:

* Specifies the **explicit order** in which properties should appear in the resulting JSON.
* Any properties **not listed** in the annotation will follow their natural order **or** be sorted **alphabetically** if `alphabetic = true`.
* Helps ensure consistent and predictable property order, especially for API responses or when comparing serialized JSON (e.g., in tests).

#### Alphabetical Ordering:

You can enable alphabetical sorting for properties not listed explicitly:

```java
@JsonPropertyOrder(value = {"id", "name"}, alphabetic = true)
```

---

### Summary Table

| Annotation           | Target       | Purpose                                                    | Direction     |
| -------------------- | ------------ | ---------------------------------------------------------- | ------------- |
| `@JsonGetter`        | Method       | Use method as source for a property key                    | Serialization |
| `@JsonProperty`      | Field/Method | Rename property for both serialization and deserialization | Both          |
| `@JsonPropertyOrder` | Class        | Set order of properties in serialized JSON                 | Serialization |

---

### 🔄 Serialization and Deserialization in Spring Boot using Jackson

Spring Boot internally uses **Jackson**, a powerful JSON processor library, to handle **serialization** and **deserialization** of Java objects.

---

