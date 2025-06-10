# ✅ What is Serialization?

**Serialization** is the process of converting a **Java object into a JSON string** so it can be sent over the internet (e.g., in HTTP responses).

* Jackson serializes the object’s **fields** or **getter method return values** into JSON properties.
* The result is a **valid JSON string**.
* By default, field names are used as keys unless customized with annotations.

---

# ✅ What is Deserialization?

**Deserialization** is the process of converting a **JSON string back into a Java object** by mapping JSON keys to fields or setter methods.

* Usually done on incoming HTTP request bodies.
* Allows data interchange between systems or components.

---

# 🔧 Jackson Annotations

---

## 🟩 Serialization Annotations

### 🔹 `@JsonGetter`

* **Purpose**: Marks a **method** as a custom **getter** during serialization.
* **Use Case**: Useful for computed values or custom naming logic.

#### ✅ Example:

```java
@JsonGetter("customName")
public String getName() {
    return this.name;
}
```

#### 🔑 Key Points:

* Method must:

  * Be non-static.
  * Have no arguments.
  * Return a serializable value.
* JSON property name is:

  * Inferred from the method name (`getName()` → `"name"`), or
  * Overridden via `@JsonGetter("customName")` or `@JsonProperty("customName")`.
* Avoid duplicate property names from multiple methods.

---

### 🔹 `@JsonProperty`

* **Purpose**: Renames fields or methods for **both serialization and deserialization**.

#### ✅ Example:

```java
@JsonProperty("full_name")
private String name;
```

* JSON will contain `"full_name"` instead of `"name"`.

---

### 🔹 `@JsonPropertyOrder`

* **Purpose**: Specifies the **order of properties** in serialized JSON output.
* **Applies to**: Class level.

#### ✅ Example:

```java
@JsonPropertyOrder({"id", "name", "email"})
public class User {
    private String name;
    private int id;
    private String email;
    private String phone;
}
```

#### 🔑 Key Points:

* Properties not listed follow:

  * Natural order (field declaration), or
  * Alphabetical order if `alphabetic = true`.

#### Alphabetical Option:

```java
@JsonPropertyOrder(value = {"id", "name"}, alphabetic = true)
```

---

### 🔹 `@JsonRawValue`

* **Purpose**: Injects a **pre-serialized JSON string** into output **without quoting or escaping**.
* **Where**: Field or method.
* **Use Cases**:

  * Already serialized JSON.
  * JavaScript functions to JS clients.

#### ✅ Example:

```java
public class Example {
    public String name = "Priya";

    @JsonRawValue
    public String rawData = "{\"score\": 100}";
}
```

#### ✅ Output:

```json
{
  "name": "Priya",
  "rawData": {"score": 100}
}
```

---

### 🔹 `@JsonRootName`

* **Purpose**: Specifies a **custom root name** for the entire JSON.
* **Applies to**: Class level.
* **Note**: Only works if `SerializationFeature.WRAP_ROOT_VALUE` is enabled.

#### ✅ Example:

```java
@JsonRootName("employee")
public class User {
    public String name = "Priya";
}
```

#### ✅ Output:

```json
{
  "employee": {
    "name": "Priya"
  }
}
```

---

### 🔹 `@JsonValue`

* **Purpose**: Marks a **method** whose return value will be used as the **entire JSON representation**.
* **Constraints**:

  * Must be non-static.
  * No parameters.
  * Return a **serializable object**, **not raw JSON**.
  * Only **one method per class** can be annotated.

#### ✅ Example:

```java
public class Role {
    private String role;

    public Role(String role) {
        this.role = role;
    }

    @JsonValue
    public String getRole() {
        return this.role;
    }
}
```

#### ✅ Output:

```json
"ADMIN"
```

---

### 🔹 `@JsonAnyGetter`

* **Purpose**: Used on a method that returns a `Map`, and its entries are **flattened into the root** JSON object.
* **Only one such method per class allowed.**

#### ✅ Example:

```java
public class Config {
    private Map<String, String> settings = new HashMap<>();

    @JsonAnyGetter
    public Map<String, String> getSettings() {
        return settings;
    }
}
```

#### ✅ Output:

```json
{
  "theme": "dark",
  "fontSize": "14px"
}
```

---

## 🟥 Deserialization Annotations

### 🔸 `@JsonSetter`

* **Purpose**: Marks a **setter method** for a specific JSON property.
* **When to Use**: JSON key doesn’t match the Java field name.

#### ✅ Example:

```java
public class User {
    private String username;

    @JsonSetter("user_name")
    public void setUsername(String username) {
        this.username = username;
    }
}
```

#### ✅ Input:

```json
{ "user_name": "priya" }
```

#### 🔑 Key Points:

* Method must:

  * Be non-static.
  * Accept a single argument.
* Alternative: `@JsonProperty`

---

### 🔸 `@JsonAnySetter`

* **Purpose**: Marks a **method** that handles unknown/dynamic JSON properties by storing them in a `Map`.

#### ✅ Example:

```java
public class CustomData {
    private Map<String, Object> extra = new HashMap<>();

    @JsonAnySetter
    public void add(String key, Object value) {
        extra.put(key, value);
    }
}
```

#### ✅ Input:

```json
{ "foo": "bar", "x": 42 }
```

#### ✅ Java Result:

```java
extra = { "foo" = "bar", "x" = 42 }
```

#### 🔑 Key Points:

* Method must:

  * Be non-static.
  * Accept **two arguments**: `key` and `value`.

---

### 🔸 `@JsonCreator`

* **Purpose**: Marks a **constructor or factory method** for use in deserialization.
* **Works with**: `@JsonProperty` to bind arguments.

#### ✅ Example:

```java
public class Person {
    private String fullName;

    @JsonCreator
    public Person(@JsonProperty("name") String fullName) {
        this.fullName = fullName;
    }
}
```

#### ✅ Input:

```json
{ "name": "Priya Sisodiya" }
```

---

### 🔸 `@JacksonInject`

* **Purpose**: Marks a field for **external value injection** during deserialization.
* **Values not provided in JSON**, but through **ObjectMapper**.

#### ✅ Example:

```java
public class Report {
    @JacksonInject("reportId")
    public String id;

    public String content;
}
```

#### ✅ ObjectMapper Configuration:

```java
ObjectMapper mapper = new ObjectMapper();
InjectableValues.Std inject = new InjectableValues.Std();
inject.addValue("reportId", "ID-123");
mapper.setInjectableValues(inject);
```

#### ✅ Input:

```json
{ "content": "Some text" }
```

---

## 📌 Summary Table

### ✅ Serialization Annotations

| Annotation           | Target       | Description                                 |
| -------------------- | ------------ | ------------------------------------------- |
| `@JsonGetter`        | Method       | Custom getter method for property name      |
| `@JsonProperty`      | Field/Method | Rename field/method for JSON property name  |
| `@JsonPropertyOrder` | Class        | Define order of properties in JSON          |
| `@JsonRawValue`      | Field/Method | Inject pre-serialized JSON string           |
| `@JsonRootName`      | Class        | Set root name for the object                |
| `@JsonValue`         | Method       | Use method's return as the full JSON        |
| `@JsonAnyGetter`     | Method       | Flatten Map into root level JSON properties |

---

### 🚀 Deserialization Annotations

| Annotation       | Target             | Description                                         |
| ---------------- | ------------------ | --------------------------------------------------- |
| `@JsonSetter`    | Method             | Specify method for custom property mapping          |
| `@JsonAnySetter` | Method             | Handle unknown JSON properties using a map          |
| `@JsonCreator`   | Constructor/Method | Specify how to create object during deserialization |
| `@JacksonInject` | Field              | Inject external values during deserialization       |

---

### 🔄 Serialization and Deserialization in Spring Boot using Jackson

Spring Boot internally uses **Jackson**, a powerful JSON processor library, to handle **serialization** and **deserialization** of Java objects.
Great questions! Let's break this down clearly:

---

## 🔍 What does this mean?

> *“The `@RestController` (or `@ResponseBody`) annotation tells Spring to skip the view resolver and directly write the object to the body.”*

### ✨ In Traditional MVC:

* You return a **view name** (like a JSP, Thymeleaf HTML page).
* Spring uses a **view resolver** to render that view.

```java
@Controller
public class PageController {
    @GetMapping("/hello")
    public String helloPage(Model model) {
        model.addAttribute("name", "Priyanshee");
        return "hello";  // This will resolve to hello.html or hello.jsp
    }
}
```

### 🧪 In REST APIs:

* You don't return HTML pages.
* You return **data** (e.g., JSON, XML) in the **response body**.
* You want Spring to serialize the object (Java → JSON) and write it **directly to the HTTP response body**.

That’s what `@RestController` or `@ResponseBody` does.

```java
@RestController
public class ApiController {
    @GetMapping("/hello")
    public Map<String, String> hello() {
        return Map.of("message", "Hi Priyanshee");
    }
}
```

Internally:
👉 `@RestController = @Controller + @ResponseBody`
So Spring skips rendering any view and **writes serialized data (like JSON)** straight into the HTTP response.

---

## 🌐 Can We Only Send Serialized Data (like JSON) in HTTP?

No — HTTP is **format-agnostic**. You can send **any type of data** over HTTP. Here are some common ones:

| Format          | Description                                      | MIME Type                  |
| --------------- | ------------------------------------------------ | -------------------------- |
| **JSON**        | Most common for APIs. Easy to read, lightweight. | `application/json`         |
| **XML**         | Older standard. Still used in many systems.      | `application/xml`          |
| **HTML**        | For webpages (view rendering).                   | `text/html`                |
| **Plain Text**  | For simple string messages.                      | `text/plain`               |
| **CSV**         | For spreadsheet-style data.                      | `text/csv`                 |
| **PDF**         | For documents.                                   | `application/pdf`          |
| **Image Files** | For media files (JPG, PNG, etc).                 | `image/jpeg`, `image/png`  |
| **Binary Data** | For any raw file (ZIP, audio, video).            | `application/octet-stream` |

You just need to set the right **Content-Type** header and format the response accordingly.

---

## ✅ In Spring Boot: You Can Control the Format

### 🎯 Example 1: Return Plain Text

```java
@GetMapping(value = "/text", produces = "text/plain")
public String getPlainText() {
    return "This is plain text!";
}
```

### 🎯 Example 2: Return XML (if Jackson XML or JAXB is configured)

```java
@GetMapping(value = "/student", produces = "application/xml")
public Student getStudent() {
    return new Student(1, "Priyanshee", "CSE");
}
```

### 🎯 Example 3: Return File (PDF, CSV, etc.)

```java
@GetMapping("/download")
public ResponseEntity<Resource> downloadFile() {
    File file = new File("report.pdf");
    Resource resource = new FileSystemResource(file);

    return ResponseEntity.ok()
        .contentType(MediaType.APPLICATION_PDF)
        .header(HttpHeaders.CONTENT_DISPOSITION, "attachment; filename=report.pdf")
        .body(resource);
}
```

---

## 🧠 Summary

* `@RestController` skips HTML rendering and directly writes data (like JSON) into the response.
* You can send **many formats** over HTTP — JSON is just the most common.
* Spring Boot lets you control the response format using headers and `produces =` attributes.
