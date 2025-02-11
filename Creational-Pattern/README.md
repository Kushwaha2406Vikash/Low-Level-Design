# Singleton Pattern in Java

## The Singleton Pattern

The Singleton Pattern ensures that a class has only one instance and provides a global access point to it.

### 1. Use Cases of Singleton Pattern

Singleton is useful when you need one shared instance of a class, such as:

✅ **Logging System** → Ensure all classes log messages to the same instance.\
✅ **Database Connection** → Avoid multiple connections to the same database.\
✅ **Thread Pool** → Single point of thread management.\
✅ **Cache Management** → Global caching to avoid redundant data fetching.\
✅ **Configuration Manager** → Single instance managing application settings.

### 2. Singleton Pattern Class Diagram

```
        +------------------+
        |   Singleton      |  
        +------------------+
        | - instance: Singleton (static) |
        | - Singleton() (private) |
        +------------------+
        | + getInstance(): Singleton  |
        | + showMessage(): void       |
        +------------------+
```

### 3. Thread-Safe Singleton Using Double-Checked Locking

```java
class Singleton {
    private static volatile Singleton instance; // Volatile ensures thread safety

    private Singleton() {} // Private constructor prevents instantiation

    public static Singleton getInstance() {
        if (instance == null) { // First check
            synchronized (Singleton.class) { // Thread safety
                if (instance == null) { // Second check
                    instance = new Singleton();
                }
            }
        }
        return instance;
    }

    public void showMessage() {
        System.out.println("Singleton Instance Created!");
    }
}

// Client Code
public class SingletonDemo {
    public static void main(String[] args) {
        Singleton obj1 = Singleton.getInstance();
        obj1.showMessage();

        Singleton obj2 = Singleton.getInstance();
        System.out.println(obj1 == obj2); // Output: true (Both references point to the same instance)
    }
}
```

### 4. Explanation of the Implementation

✔ **Key Features:**

- **Private Constructor** → Prevents direct instantiation.
- **Static Instance Variable** → Stores the single instance.
- **getInstance() Method** → Returns the single instance, creating it only when needed.
- **Double-Checked Locking** → Ensures thread safety with minimal performance overhead.
- **Volatile Keyword** → Ensures visibility of instance changes across threads.

### 5. Bill Pugh Singleton (Best Practice - Uses Inner Static Helper Class)

```java
class Singleton {
    private Singleton() {}

    private static class SingletonHelper {
        private static final Singleton INSTANCE = new Singleton();
    }

    public static Singleton getInstance() {
        return SingletonHelper.INSTANCE;
    }
}
```

### 6. Summary

| Approach               | Lazy Initialization? | Thread Safe? | Performance                         |
| ---------------------- | -------------------- | ------------ | ----------------------------------- |
| Eager Initialization   | ❌ No                 | ✅ Yes        | ❌ Creates instance even if not used |
| Double-Checked Locking | ✅ Yes                | ✅ Yes        | ✅ Efficient                         |
| Bill Pugh Singleton    | ✅ Yes                | ✅ Yes        | ✅ Best practice                     |

---

# Factory Method Pattern in Java

## The Factory Method Pattern

The Factory Method Pattern provides an interface for creating objects without specifying their exact class. It lets subclasses decide which class to instantiate, promoting loose coupling.

### 1. Use Cases of Factory Method Pattern

The Factory Method Pattern is useful when:

✅ **Database Connections** → Different databases (MySQL, PostgreSQL) require different connection objects.\
✅ **Notification System** → Creating SMS, Email, or Push notifications without modifying client code.\
✅ **Logging Framework** → Different logging mechanisms (Console, File, Cloud).\
✅ **Shape/Document Editor** → Creating different shapes (Circle, Square) dynamically.\
✅ **Payment Processing** → Supporting multiple payment gateways like PayPal, Stripe, etc.

### 2. Factory Method Pattern Class Diagram

```
        +---------------------+
        |  Product (Interface)|
        +---------------------+
        | + createProduct()   |
        +---------------------+
                  ▲
                  |
        +---------------------+
        | ConcreteProductA    |
        | ConcreteProductB    |
        +---------------------+
        
        +---------------------+
        | Factory (Abstract)  |
        +---------------------+
        | + createProduct()   |
        +---------------------+
                  ▲
                  |
        +---------------------+
        | ConcreteFactoryA    |
        | ConcreteFactoryB    |
        +---------------------+
```

### 3. Java Implementation of Factory Method Pattern

#### Step 1: Create a Product Interface

```java
interface Notification {
    void notifyUser();
}
```

#### Step 2: Create Concrete Implementations of the Product

```java
class EmailNotification implements Notification {
    public void notifyUser() {
        System.out.println("Sending an Email Notification");
    }
}

class SMSNotification implements Notification {
    public void notifyUser() {
        System.out.println("Sending an SMS Notification");
    }
}

class PushNotification implements Notification {
    public void notifyUser() {
        System.out.println("Sending a Push Notification");
    }
}
```

#### Step 3: Create an Abstract Factory Class

```java
abstract class NotificationFactory {
    abstract Notification createNotification();
}
```

#### Step 4: Implement Concrete Factories

```java
class EmailNotificationFactory extends NotificationFactory {
    public Notification createNotification() {
        return new EmailNotification();
    }
}

class SMSNotificationFactory extends NotificationFactory {
    public Notification createNotification() {
        return new SMSNotification();
    }
}

class PushNotificationFactory extends NotificationFactory {
    public Notification createNotification() {
        return new PushNotification();
    }
}
```

#### Step 5: Create a Client to Use the Factory

```java
public class FactoryMethodDemo {
    public static void main(String[] args) {
        NotificationFactory factory;

        factory = new EmailNotificationFactory();
        Notification email = factory.createNotification();
        email.notifyUser();

        factory = new SMSNotificationFactory();
        Notification sms = factory.createNotification();
        sms.notifyUser();

        factory = new PushNotificationFactory();
        Notification push = factory.createNotification();
        push.notifyUser();
    }
}
```

### 4. Simplified Factory Implementation

```java
class NotificationFactory {
    public static Notification createNotification(String type) {
        switch (type) {
            case "EMAIL": return new EmailNotification();
            case "SMS": return new SMSNotification();
            case "PUSH": return new PushNotification();
            default: throw new IllegalArgumentException("Unknown notification type");
        }
    }
}

// Client
public class SimpleFactoryDemo {
    public static void main(String[] args) {
        Notification notification = NotificationFactory.createNotification("EMAIL");
        notification.notifyUser();
    }
}
```

# Abstract Factory Pattern in Java

The **Abstract Factory Pattern** provides an interface for creating families of related objects without specifying their concrete classes. It is used when multiple factories need to create objects belonging to different categories.

## 1. Use Cases of Abstract Factory Pattern

- **Cross-Platform UI Toolkits** → Creating UI elements (Buttons, Checkboxes) that work across different OS (Windows, Mac, Linux).
- **Database Connection Factory** → Supporting multiple databases (MySQL, PostgreSQL, MongoDB) without modifying client code.
- **Theme-Based UI Factories** → Generating UI components based on different themes (Dark Theme, Light Theme).
- **Cloud Service Providers** → Factory for different cloud services (AWS, Azure, Google Cloud).

## 2. Abstract Factory Pattern Class Diagram
```
        +----------------------+
        | AbstractFactory      |  (Abstract)
        +----------------------+
        | + createButton()     |
        | + createCheckbox()   |
        +----------------------+
                  ▲
                  │
     ----------------------------------
     |                                |
+----------------------+     +----------------------+
| WindowsFactory      |     | MacFactory          |
+----------------------+     +----------------------+
| + createButton()    |     | + createButton()    |
| + createCheckbox()  |     | + createCheckbox()  |
+----------------------+     +----------------------+
     |                                |
+----------------------+     +----------------------+
| WindowsButton       |     | MacButton           |
+----------------------+     +----------------------+
| + render()          |     | + render()          |
+----------------------+     +----------------------+
```

## 3. Java Implementation of Abstract Factory Pattern

### Step 1: Define Abstract Product Interfaces
```java
// Abstract Product - Button
interface Button {
    void render();
}

// Abstract Product - Checkbox
interface Checkbox {
    void render();
}
```

### Step 2: Create Concrete Product Implementations
```java
// Concrete Product - Windows Button
class WindowsButton implements Button {
    public void render() {
        System.out.println("Rendering Windows Button");
    }
}

// Concrete Product - Mac Button
class MacButton implements Button {
    public void render() {
        System.out.println("Rendering Mac Button");
    }
}

// Concrete Product - Windows Checkbox
class WindowsCheckbox implements Checkbox {
    public void render() {
        System.out.println("Rendering Windows Checkbox");
    }
}

// Concrete Product - Mac Checkbox
class MacCheckbox implements Checkbox {
    public void render() {
        System.out.println("Rendering Mac Checkbox");
    }
}
```

### Step 3: Define the Abstract Factory Interface
```java
// Abstract Factory Interface
interface GUIFactory {
    Button createButton();
    Checkbox createCheckbox();
}
```

### Step 4: Implement Concrete Factories
```java
// Concrete Factory - Windows Factory
class WindowsFactory implements GUIFactory {
    public Button createButton() {
        return new WindowsButton();
    }

    public Checkbox createCheckbox() {
        return new WindowsCheckbox();
    }
}

// Concrete Factory - Mac Factory
class MacFactory implements GUIFactory {
    public Button createButton() {
        return new MacButton();
    }

    public Checkbox createCheckbox() {
        return new MacCheckbox();
    }
}
```

### Step 5: Create a Client to Use the Abstract Factory
```java
// Client Code
public class AbstractFactoryDemo {
    public static void main(String[] args) {
        GUIFactory factory;
        
        // Creating Windows UI Components
        factory = new WindowsFactory();
        Button winButton = factory.createButton();
        Checkbox winCheckbox = factory.createCheckbox();
        winButton.render();
        winCheckbox.render();

        // Creating Mac UI Components
        factory = new MacFactory();
        Button macButton = factory.createButton();
        Checkbox macCheckbox = factory.createCheckbox();
        macButton.render();
        macCheckbox.render();
    }
}
```

---

# Builder Pattern in Java

The **Builder Pattern** is a creational design pattern used to construct complex objects step-by-step.

## 1. Use Cases of Builder Pattern

- **Creating Immutable Objects** → Ensures immutability in objects like `StringBuilder` and `java.time.LocalDate`.
- **Large Constructor Arguments** → Simplifies object construction with many optional parameters.
- **Complex Object Creation** → Used in constructing hierarchical objects (e.g., HTML documents, JSON responses).
- **Configurable Object Creation** → Used in frameworks like Spring and Hibernate.
- **HTTP Requests/Responses** → Used in APIs like `OkHttpClient`, `Retrofit`.

## 2. Builder Pattern Class Diagram
```
        +----------------------+
        |   Product (Car)      |  
        +----------------------+
        | - engine: String     |
        | - seats: int         |
        | - GPS: boolean       |
        +----------------------+
                  ▲
                  │
        +----------------------+
        |    CarBuilder        |  (Abstract Builder)
        +----------------------+
        | + setEngine()        |
        | + setSeats()         |
        | + setGPS()           |
        | + build()            |
        +----------------------+
                  ▲
                  │
     ---------------------------------
     |                               |
+----------------------+     +----------------------+
|   SportCarBuilder    |     |    SUVCarBuilder    |
+----------------------+     +----------------------+
```

## 3. Java Implementation of Builder Pattern

### Step 1: Create the Product Class
```java
// Define the Product Class (Immutable Car)
class Car {
    private String engine;
    private int seats;
    private boolean GPS;

    public Car(String engine, int seats, boolean GPS) {
        this.engine = engine;
        this.seats = seats;
        this.GPS = GPS;
    }

    public void showSpecifications() {
        System.out.println("Engine: " + engine + ", Seats: " + seats + ", GPS: " + GPS);
    }
}
```

### Step 2: Define the Builder Interface
```java
// Builder Interface
interface CarBuilder {
    CarBuilder setEngine(String engine);
    CarBuilder setSeats(int seats);
    CarBuilder setGPS(boolean GPS);
    Car build();
}
```

### Step 3: Implement Concrete Builders
```java
// Concrete Builder - Sport Car Builder
class SportCarBuilder implements CarBuilder {
    private String engine;
    private int seats;
    private boolean GPS;

    public CarBuilder setEngine(String engine) {
        this.engine = engine;
        return this;
    }
```

### Step 4: Create a Director Class (Optional)
```java
// Director Class (Optional)
class Director {
    public Car constructSportsCar(CarBuilder builder) {
        return builder.setEngine("V8").setSeats(2).setGPS(true).build();
    }
}
```

### Step 5: Client Code
```java
// Client Code
public class BuilderPatternDemo {
    public static void main(String[] args) {
        Director director = new Director();
        CarBuilder sportCarBuilder = new SportCarBuilder();
        Car sportsCar = director.constructSportsCar(sportCarBuilder);
        sportsCar.showSpecifications();
    }
}
```

# Prototype Pattern in Java

The **Prototype Pattern** is a creational design pattern used when object creation is costly, and we need to create multiple copies of an object efficiently. Instead of creating new instances, it clones existing ones.

## 1. Use Cases of Prototype Pattern

✅ **Memory Optimization** → Avoids expensive object creation (e.g., deep-copying game characters or assets).  
✅ **Performance Improvement** → Efficient for objects that require complex initialization (e.g., database connections, configurations).  
✅ **Undo/History Mechanism** → Used in text editors and version control systems.  
✅ **Cloning Complex Objects** → Helpful when objects have many fields or nested objects (e.g., deep cloning in JSON/XML parsers).  
✅ **Distributed Systems** → Reduces overhead by sending object copies instead of constructing new instances.  

## 2. Prototype Pattern Class Diagram

```
        +----------------------+
        |   Prototype         |  (Interface)
        +----------------------+
        | + clone() : Object  |
        +----------------------+
                  ▲
                  │
     ---------------------------------
     |                               |
+----------------------+     +----------------------+
|   ConcretePrototypeA |     | ConcretePrototypeB  |
+----------------------+     +----------------------+
| - field1            |     | - field2             |
| - field2            |     | - field3             |
| + clone()           |     | + clone()            |
+----------------------+     +----------------------+
```

## 3. Java Implementation of Prototype Pattern

### Step 1: Define the Prototype Interface

```java
// Step 1: Prototype Interface
interface Prototype extends Cloneable {
    Prototype clone();
}
```

### Step 2: Create Concrete Prototype Classes

```java
// Step 2: Concrete Prototype - Employee
class Employee implements Prototype {
    private String name;
    private String department;

    public Employee(String name, String department) {
        this.name = name;
        this.department = department;
    }

    @Override
    public Prototype clone() {
        return new Employee(name, department);
    }

    public void showDetails() {
        System.out.println("Employee: " + name + ", Department: " + department);
    }
}

// Step 2: Concrete Prototype - Project
class Project implements Prototype {
    private String projectName;
    private int duration;

    public Project(String projectName, int duration) {
        this.projectName = projectName;
        this.duration = duration;
    }

    @Override
    public Prototype clone() {
        return new Project(projectName, duration);
    }

    public void showDetails() {
        System.out.println("Project: " + projectName + ", Duration: " + duration + " months");
    }
}
```

### Step 3: Client Code for Cloning Objects

```java
// Step 3: Client Code
public class PrototypePatternDemo {
    public static void main(String[] args) {
        // Create Original Objects
        Employee emp1 = new Employee("John Doe", "Engineering");
        Project project1 = new Project("AI Research", 12);

        // Clone Objects
        Employee emp2 = (Employee) emp1.clone();
        Project project2 = (Project) project1.clone();

        // Display Details
        emp1.showDetails();
        emp2.showDetails();
        project1.showDetails();
        project2.showDetails();
    }
}
```

## 4. Key Features

- **Prototype Interface (`Prototype`)** → Declares the `clone()` method for cloning objects.
- **Concrete Prototypes (`Employee`, `Project`)** → Implements `clone()` to return a new instance with the same attributes.
- **Client (`PrototypePatternDemo`)** → Uses `clone()` instead of creating new objects manually.

## 5. Cloning Mechanisms

### ✅ Shallow Cloning (Default)

Copies primitive fields but shares references for objects.

```java
@Override
public Prototype clone() {
    try {
        return (Prototype) super.clone(); // Shallow Copy
    } catch (CloneNotSupportedException e) {
        throw new RuntimeException(e);
    }
}
```

🔹 **Issue**: Changes in one clone affect the other if they share reference types.

### ✅ Deep Cloning (Manual Cloning of Nested Objects)

Each object is recursively cloned to avoid reference issues.

```java
@Override
public Prototype clone() {
    return new Employee(new String(name), new String(department)); // Deep Copy
}
```

## 6. Real-World Examples of Prototype Pattern

| **Use Case**               | **Explanation**                                       |
|----------------------------|-----------------------------------------------------|
| **Document Cloning**       | Google Docs duplicating templates                  |
| **Game Object Cloning**    | Character skins or enemy AI duplication            |
| **Database Query Results** | Cloning fetched objects for caching                |
| **AI Model Instances**     | Copying AI models with pretrained weights          |
| **Version Control Systems** | Git branches and commit snapshots                 |

---

This concludes the explanation of the **Prototype Pattern in Java**. 🚀 Happy Coding! 🎯
