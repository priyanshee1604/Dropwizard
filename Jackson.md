
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





@JsonRawValue 
Used to inject already serialised values into JSON
It marks method or field whoes value should be included as literal string value of the property as is and without quoting or characters 
most commonly used for injecting already serialised JSON string, can also be used to parse JS function to JS clients


@JsonRootName
Specifies the root name for the serialization, this annotation is used to specify a name to use for the root level name for serialised object  by default the calss name i sused
class level annotation

@JsonValue
used to identify a method that returns the instaces of the json annotation.
marks the method that returns the seralisation of the ob object.
singature must be non void and argument free, normally be scaler but can also be any serialisable type scuh as map
jackson will not serialise itself but call the annotated method
only one method can be annotated in this way, if more than one then exception wil be thrown.
you cannot retunr a full json strign you need to retunr a serialisable object such as map.

@JsonAnyGetter
mark a method whoes return value is a map
flattens the map into key value pairs, brings key value upto the root level as then they are serialised.
only one allowed per class
reverse of @JsonAnySetter annotation


## Json Deserialization
mapping the properties of the JSON data string to an instance of a class

@JsonSetter
used to identify a method as a setter method for a specific property
useful when json data has a property name that doesnot quite match the property of the target
should be non static and should accept a single argument
during deserialisation the annotated method is called and the json property that is mapped to this method is identified by the name pass to the annotation as value
alternative @JsonProperty

@JsonAnySetter
used to identify a method as a setter method for mapper properties
marks method as setter method for map property
method should be non static and accept 2 arguments (key, value)
during de serialisation the annotated method is called, and properties and values of the Json string are passed to the method
method should implement code to add key value pairs to map


@JsonCreator
used to define constrcutor methods, used to mark constructor or factory method, used to create instances during deserialisation
often used in cooperation with @JsonProperty annotation to specify property names
used to deserialise the mismatch property name
