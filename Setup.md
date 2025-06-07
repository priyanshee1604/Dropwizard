### 🧰 **What is This Section About?**

This part helps you set up a **Dropwizard project using Maven** (which is the recommended build tool).

You’ll:

* Set up Maven to manage your Dropwizard dependencies.
* Choose from 3 ways to start your project.
* Get your project ready for writing Java code.

---

### ✅ **Why Maven?**

Dropwizard officially supports **Maven** out of the box.

You **can use** other build tools like:

* Gradle
* Ant/Ivy
* SBT
* Buildr
  …but Dropwizard documentation and examples use **Maven**, so it's easiest to follow along using Maven.

If you’re new to Maven, you can read ["Maven: The Complete Reference"](https://books.sonatype.com/mvnref-book/reference/) for deeper understanding — but it’s optional.

---

### 🚀 **3 Ways to Create a New Dropwizard Project**

1. **Using Archetype (recommended for new projects)**
   This creates a skeleton Dropwizard project for you automatically:

   ```bash
   mvn archetype:generate \
     -DarchetypeGroupId=io.dropwizard.archetypes \
     -DarchetypeArtifactId=java-simple \
     -DarchetypeVersion=4.0.14
   ```

   > Replace `4.0.14` with the current Dropwizard version you want to use.

2. **Check the Example Project**

   * Dropwizard has a [dropwizard-example](https://github.com/dropwizard/dropwizard/tree/main/dropwizard-example) on GitHub.
   * You can clone it and see how a real Dropwizard app is structured.

3. **Add Dropwizard to Your Existing Project**

   * This is the manual way.
   * Useful if you already have a Java project and want to **add Dropwizard to it**.

---

### 🧩 **Tutorial: Manual Setup in Existing Maven Project**

If you’re not using the archetype and want to set it up yourself:

#### Step 1: Add BOM (Bill of Materials)

This ensures all your Dropwizard dependencies use the **same version**, automatically.

In your `pom.xml`, add this inside `<dependencyManagement>`:

```xml
<dependencyManagement>
    <dependencies>
        <dependency>
            <groupId>io.dropwizard</groupId>
            <artifactId>dropwizard-bom</artifactId>
            <version>4.0.14</version>
            <type>pom</type>
            <scope>import</scope>
        </dependency>
    </dependencies>
</dependencyManagement>
```

#### Step 2: Add Dropwizard Core

Now include the actual Dropwizard library in `<dependencies>`:

```xml
<dependencies>
    <dependency>
        <groupId>io.dropwizard</groupId>
        <artifactId>dropwizard-core</artifactId>
    </dependency>
</dependencies>
```

---

### 💡 What’s a BOM?

A BOM (Bill of Materials) helps **avoid version conflicts**.
Instead of specifying versions for every Dropwizard dependency manually, you specify **just once** in the BOM, and all other related components automatically match that version.

---

### 🏁 Final Result

Now your `pom.xml` is set up to:

* Use Dropwizard version 4.0.14
* Use `dropwizard-core` to get started
* Avoid version mismatch issues with other Dropwizard modules

You’re ready to **start writing Java code** using Dropwizard!
