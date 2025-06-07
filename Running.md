## ✅ 1. **Ensure Your Project Has This Structure**

A basic Dropwizard project has:

```
src/
├── main/
│   ├── java/
│   │   └── your/package/
│   │       ├── YourApplication.java     ← Main class
│   │       └── YourConfiguration.java   ← Configuration class
│   └── resources/
│       └── config.yml                   ← YAML configuration file
pom.xml
```

---

## ✅ 2. **Identify Your `Application` Class**

Make sure you have a class that extends `io.dropwizard.Application`. For example:

```java
public class HelloWorldApplication extends Application<HelloWorldConfiguration> {
    public static void main(String[] args) throws Exception {
        new HelloWorldApplication().run(args);
    }

    @Override
    public void run(HelloWorldConfiguration config, Environment env) {
        // Register resources, etc.
    }
}
```

---

## ✅ 3. **Create a YAML Config File**

This is the `config.yml` file where you define server settings like port, application name, logging, etc.

Example `config.yml`:

```yaml
server:
  applicationConnectors:
    - type: http
      port: 8080
  adminConnectors:
    - type: http
      port: 8081

logging:
  level: INFO
  loggers:
    your.package.name: DEBUG
```

Place this under: `src/main/resources/config.yml`

---

## ✅ 4. **Build the Project**

Open your terminal in the project root and run:

```bash
mvn clean package
```

After successful build, a **JAR file** will be created in:

```
target/your-app-name.jar
```

---

## ✅ 5. **Run the Application**

Use this command:

```bash
java -jar target/your-app-name.jar server config.yml
```

### Breakdown:

* `java -jar` → Runs your JAR
* `your-app-name.jar` → Replace with the actual JAR name built by Maven
* `server` → Tells Dropwizard to start the HTTP server
* `config.yml` → Points to your configuration file

---

## ✅ 6. **Verify It's Running**

Check your terminal — you should see output like:

```
INFO [main] io.dropwizard.Application: Starting HelloWorldApplication
INFO [main] org.eclipse.jetty.server.Server: Started @2345ms
```

Then go to:

* [http://localhost:8080](http://localhost:8080) → Main API port
* [http://localhost:8081](http://localhost:8081) → Admin port (metrics, health checks)
