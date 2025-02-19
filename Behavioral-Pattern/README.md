# **Command Pattern in Java**  

The **Command Pattern** is a **behavioral design pattern** that encapsulates a request as an object, allowing:
- **Decoupling** between sender and receiver.
- **Queueing, undo/redo operations, and logging of commands.**
- **Dynamic command execution at runtime.**

---

## **Real-World Example: Smart Home Automation (Light Control)**  
Imagine a **Smart Home System** where you control devices like lights, fans, and TVs using voice commands or a remote. Instead of directly invoking the methods of these devices, we **encapsulate actions as command objects**.

- Pressing "ON" on a remote sends a **TurnOnCommand** to the light.  
- Pressing "OFF" sends a **TurnOffCommand**.  
- If the system supports **Undo**, it reverses the last action.

---

## **Class Diagram**
```plaintext
            +----------------------+
            |      Command         | <-------------+
            |----------------------|              |
            | + execute()          |              |
            | + undo()             |              |
            +----------------------+              |
                      ▲                            |
                      |                            |
       +----------------------+      +----------------------+
       | TurnOnCommand         |      | TurnOffCommand       |
       |---------------------- |      |----------------------|
       | - light: Light        |      | - light: Light       |
       |---------------------- |      |----------------------|
       | + execute()           |      | + execute()          |
       | + undo()              |      | + undo()             |
       +----------------------+      +----------------------+
                      ▲                            ▲
                      |                            |
            +---------------------+      
            |       Light          |      
            |--------------------- |      
            | + turnOn()           |      
            | + turnOff()          |      
            +---------------------+      
```

---

## **Java Implementation**
```java
// Command Interface
interface Command {
    void execute();
    void undo();
}

// Receiver (Light)
class Light {
    public void turnOn() {
        System.out.println("Light is ON");
    }

    public void turnOff() {
        System.out.println("Light is OFF");
    }
}

// Concrete Command - Turn On Light
class TurnOnCommand implements Command {
    private Light light;

    public TurnOnCommand(Light light) {
        this.light = light;
    }

    @Override
    public void execute() {
        light.turnOn();
    }

    @Override
    public void undo() {
        light.turnOff();
    }
}

// Concrete Command - Turn Off Light
class TurnOffCommand implements Command {
    private Light light;

    public TurnOffCommand(Light light) {
        this.light = light;
    }

    @Override
    public void execute() {
        light.turnOff();
    }

    @Override
    public void undo() {
        light.turnOn();
    }
}

// Invoker (Remote Control)
class RemoteControl {
    private Command command;

    public void setCommand(Command command) {
        this.command = command;
    }

    public void pressButton() {
        command.execute();
    }

    public void pressUndo() {
        command.undo();
    }
}

// Client (User)
public class SmartHome {
    public static void main(String[] args) {
        Light livingRoomLight = new Light();

        Command turnOn = new TurnOnCommand(livingRoomLight);
        Command turnOff = new TurnOffCommand(livingRoomLight);

        RemoteControl remote = new RemoteControl();

        // Turn on the light
        remote.setCommand(turnOn);
        remote.pressButton(); // Output: Light is ON

        // Undo last operation
        remote.pressUndo(); // Output: Light is OFF

        // Turn off the light
        remote.setCommand(turnOff);
        remote.pressButton(); // Output: Light is OFF

        // Undo last operation
        remote.pressUndo(); // Output: Light is ON
    }
}
```

---

## **Output**
```plaintext
Light is ON
Light is OFF
Light is OFF
Light is ON
```

---

## **Use Case & Benefits**
### **Use Cases**
- ✅ **Smart Home Systems**: Automating home appliances like lights, fans, TVs, etc.  
- ✅ **GUI Buttons & Menus**: Each button click maps to a command (e.g., copy, paste).  
- ✅ **Game Controls**: Undo/Redo actions for player movements.  
- ✅ **Transaction Management**: Bank transactions can be reversed using undo.  

### **Benefits**
- ✔ **Decouples sender and receiver** (commands encapsulate logic).  
- ✔ **Supports undo/redo functionality**.  
- ✔ **Easy to extend** (new commands can be added without modifying existing code).  

_________________________________________________________________________________________________________________________________________________
# **Chain of Responsibility Pattern in Java**  

The **Chain of Responsibility (CoR) Pattern** allows multiple handlers to process a request sequentially until one of them handles it. This **decouples senders from receivers**, improving flexibility and maintainability.

---

## **Real-World Example: ATM Cash Withdrawal**  
Consider an **ATM** where a user requests cash. The system processes the withdrawal request in a **chain** of different handlers, each responsible for dispensing a specific denomination (`2000, 500, 200, 100`).

If the requested amount is 3700:
- `2000` handler dispenses **1 note** → Remaining: **1700**  
- `500` handler dispenses **3 notes** → Remaining: **200**  
- `200` handler dispenses **1 note** → Remaining: **0**  
- `100` handler is skipped since the amount is fully processed.

---

## **Class Diagram**
```plaintext
+-------------------+
|  CashHandler     |<------------------+
|-------------------|                   |
| - nextHandler    |                   |
|-------------------|                   |
| + setNext(handler)|                   |
| + handle(amount)  |                   |
+-------------------+                   |
       ▲                               |
       |                               |
+-------------------+  +-------------------+
|  TwoThousandHandler  |  |  FiveHundredHandler  |
|-------------------|  |-------------------|
| + handle(amount)  |  | + handle(amount)  |
+-------------------+  +-------------------+
       ▲                               ▲
       |                               |
+-------------------+  +-------------------+
|  TwoHundredHandler |  |  OneHundredHandler |
|-------------------|  |-------------------|
| + handle(amount)  |  | + handle(amount)  |
+-------------------+  +-------------------+
```

---

## **Java Implementation**
```java
// Abstract Handler
abstract class CashHandler {
    protected CashHandler nextHandler;

    public void setNextHandler(CashHandler nextHandler) {
        this.nextHandler = nextHandler;
    }

    public abstract void handle(int amount);
}

// Concrete Handler: Dispenses 2000 Notes
class TwoThousandHandler extends CashHandler {
    public void handle(int amount) {
        if (amount >= 2000) {
            int numNotes = amount / 2000;
            int remainder = amount % 2000;
            System.out.println("Dispensing " + numNotes + " x ₹2000 notes");
            if (remainder != 0 && nextHandler != null) {
                nextHandler.handle(remainder);
            }
        } else if (nextHandler != null) {
            nextHandler.handle(amount);
        }
    }
}

// Concrete Handler: Dispenses 500 Notes
class FiveHundredHandler extends CashHandler {
    public void handle(int amount) {
        if (amount >= 500) {
            int numNotes = amount / 500;
            int remainder = amount % 500;
            System.out.println("Dispensing " + numNotes + " x ₹500 notes");
            if (remainder != 0 && nextHandler != null) {
                nextHandler.handle(remainder);
            }
        } else if (nextHandler != null) {
            nextHandler.handle(amount);
        }
    }
}

// Concrete Handler: Dispenses 200 Notes
class TwoHundredHandler extends CashHandler {
    public void handle(int amount) {
        if (amount >= 200) {
            int numNotes = amount / 200;
            int remainder = amount % 200;
            System.out.println("Dispensing " + numNotes + " x ₹200 notes");
            if (remainder != 0 && nextHandler != null) {
                nextHandler.handle(remainder);
            }
        } else if (nextHandler != null) {
            nextHandler.handle(amount);
        }
    }
}

// Concrete Handler: Dispenses 100 Notes
class OneHundredHandler extends CashHandler {
    public void handle(int amount) {
        if (amount >= 100) {
            int numNotes = amount / 100;
            int remainder = amount % 100;
            System.out.println("Dispensing " + numNotes + " x ₹100 notes");
            if (remainder != 0 && nextHandler != null) {
                nextHandler.handle(remainder);
            }
        } else if (nextHandler != null) {
            nextHandler.handle(amount);
        }
    }
}

// ATM Class
public class ATM {
    private CashHandler chain;

    public ATM() {
        // Setting up chain of responsibility
        this.chain = new TwoThousandHandler();
        CashHandler fiveHundred = new FiveHundredHandler();
        CashHandler twoHundred = new TwoHundredHandler();
        CashHandler oneHundred = new OneHundredHandler();

        // Chain linking
        chain.setNextHandler(fiveHundred);
        fiveHundred.setNextHandler(twoHundred);
        twoHundred.setNextHandler(oneHundred);
    }

    public void withdraw(int amount) {
        if (amount % 100 != 0) {
            System.out.println("Amount should be in multiples of 100!");
            return;
        }
        chain.handle(amount);
    }

    public static void main(String[] args) {
        ATM atm = new ATM();
        atm.withdraw(3700); // Example Withdrawal
    }
}
```

---

## **Output for `atm.withdraw(3700);`**
```plaintext
Dispensing 1 x ₹2000 notes
Dispensing 3 x ₹500 notes
Dispensing 1 x ₹200 notes
```

---

## **Use Case & Benefits**
### **Use Case**:  
- **ATM withdrawal systems**
- **Request validation pipelines (e.g., API middleware)**
- **Event processing systems (e.g., logging, authentication chains)**

### **Benefits**:  
- **Decouples request senders from receivers**
- **Improves code maintainability & scalability**
- **Allows dynamic addition of new handlers without modifying existing code**

__________________________________________________________________________________________________________________________________________________
# **Observer Pattern in Java**  

The **Observer Pattern** is a **behavioral design pattern** where an object (subject) maintains a list of dependents (observers) and notifies them of any state changes. This is useful for implementing event-driven architectures.

---

## **Real-World Example: Weather Monitoring System**  
Consider a **Weather Station** that updates multiple display units when weather conditions change. The displays **observe** the weather station for updates.

When the temperature changes:
- The **Weather Station** notifies all registered **Observers** (TV Display, Mobile App, Website) with the new temperature.
- Each **Observer** updates its display accordingly.

---

## **Class Diagram**
```plaintext
      +-------------------+
      |   Subject        |
      |-------------------|
      | + register()     |
      | + unregister()   |
      | + notifyObservers() |
      +-------------------+
              ▲
              |
      +-------------------+
      |  WeatherStation  |
      |-------------------|
      | - temperature    |
      | + setTemperature() |
      +-------------------+
              |
      +-------------------+
      |   Observer       |
      |-------------------|
      | + update()       |
      +-------------------+
              ▲
              |
  +-------------------+    +-------------------+    +-------------------+
  | TVDisplay        |    | MobileApp        |    | WebsiteDisplay    |
  |-------------------|    |-------------------|    |-------------------|
  | + update()       |    | + update()       |    | + update()       |
  +-------------------+    +-------------------+    +-------------------+
```

---

## **Java Implementation**
```java
// Observer Interface
interface Observer {
    void update(float temperature);
}

// Subject Interface
interface Subject {
    void register(Observer observer);
    void unregister(Observer observer);
    void notifyObservers();
}

// Concrete Subject: WeatherStation
class WeatherStation implements Subject {
    private List<Observer> observers = new ArrayList<>();
    private float temperature;

    public void setTemperature(float temperature) {
        this.temperature = temperature;
        notifyObservers();
    }

    @Override
    public void register(Observer observer) {
        observers.add(observer);
    }

    @Override
    public void unregister(Observer observer) {
        observers.remove(observer);
    }

    @Override
    public void notifyObservers() {
        for (Observer observer : observers) {
            observer.update(temperature);
        }
    }
}

// Concrete Observer: TV Display
class TVDisplay implements Observer {
    @Override
    public void update(float temperature) {
        System.out.println("TV Display: Temperature updated to " + temperature + "°C");
    }
}

// Concrete Observer: Mobile App
class MobileApp implements Observer {
    @Override
    public void update(float temperature) {
        System.out.println("Mobile App: Temperature updated to " + temperature + "°C");
    }
}

// Concrete Observer: Website Display
class WebsiteDisplay implements Observer {
    @Override
    public void update(float temperature) {
        System.out.println("Website Display: Temperature updated to " + temperature + "°C");
    }
}

// Client Code
public class WeatherMonitoringSystem {
    public static void main(String[] args) {
        WeatherStation station = new WeatherStation();

        Observer tvDisplay = new TVDisplay();
        Observer mobileApp = new MobileApp();
        Observer websiteDisplay = new WebsiteDisplay();

        station.register(tvDisplay);
        station.register(mobileApp);
        station.register(websiteDisplay);

        station.setTemperature(25.5f);
        station.setTemperature(30.0f);
    }
}
```

---

## **Output**
```plaintext
TV Display: Temperature updated to 25.5°C
Mobile App: Temperature updated to 25.5°C
Website Display: Temperature updated to 25.5°C
TV Display: Temperature updated to 30.0°C
Mobile App: Temperature updated to 30.0°C
Website Display: Temperature updated to 30.0°C
```

---

## **Use Case & Benefits**
### **Use Cases**
- **Weather Monitoring Systems** (Multiple displays observing a weather station)
- **Stock Market Applications** (Investors tracking stock price changes)
- **Notification Systems** (Subscribers receiving updates from a news portal)
- **GUI Event Listeners** (UI components reacting to user inputs)

### **Benefits**
- **Decouples subject from observers**
- **Supports dynamic subscribers**
- **Provides real-time event-driven updates**

___________________________________________________________________________________________________________________________________________________
# **Mediator Pattern in Java**  

The **Mediator Pattern** is a **behavioral design pattern** that defines an object (Mediator) to centralize communication between multiple objects (Colleagues). This reduces direct dependencies and promotes loose coupling.

---

## **Real-World Example: Air Traffic Control (ATC) System**  
Consider an **Air Traffic Control (ATC) Tower**, which manages communication between multiple aircraft. Instead of aircraft communicating directly with each other, they interact with ATC, which ensures smooth coordination and avoids conflicts.

When an aircraft requests landing:
- It **sends a request** to the **ATC Mediator**.
- The **ATC Mediator** checks runway availability and approves/rejects landing.
- If approved, the **ATC Mediator** notifies all other aircraft to hold or proceed accordingly.

---

## **Class Diagram**
```plaintext
      +-------------------+
      |   Mediator       |
      |-------------------|
      | + notify()       |
      +-------------------+
              ▲
              |
      +-------------------+
      |  ATCMediator     |
      |-------------------|
      | - airplanes      |
      | + register()     |
      | + notify()       |
      +-------------------+
              ▲
              |
  +-------------------+    +-------------------+
  | Airplane         |    | Airplane         |
  |-------------------|    |-------------------|
  | + requestLanding()|    | + requestLanding()|
  | + receiveMessage()|    | + receiveMessage()|
  +-------------------+    +-------------------+
```

---

## **Java Implementation**
```java
import java.util.ArrayList;
import java.util.List;

// Mediator Interface
interface Mediator {
    void notify(String message, Airplane sender);
}

// Concrete Mediator: ATC Tower
class ATCMediator implements Mediator {
    private List<Airplane> airplanes = new ArrayList<>();

    public void register(Airplane airplane) {
        airplanes.add(airplane);
    }

    @Override
    public void notify(String message, Airplane sender) {
        for (Airplane airplane : airplanes) {
            if (airplane != sender) {
                airplane.receiveMessage(message);
            }
        }
    }
}

// Colleague: Airplane
class Airplane {
    private String id;
    private Mediator atc;

    public Airplane(String id, Mediator atc) {
        this.id = id;
        this.atc = atc;
    }

    public void requestLanding() {
        System.out.println(id + " requesting landing.");
        atc.notify(id + " is requesting landing.", this);
    }

    public void receiveMessage(String message) {
        System.out.println(id + " received: " + message);
    }
}

// Client Code
public class MediatorPatternDemo {
    public static void main(String[] args) {
        ATCMediator atc = new ATCMediator();

        Airplane airplane1 = new Airplane("Flight 101", atc);
        Airplane airplane2 = new Airplane("Flight 202", atc);

        atc.register(airplane1);
        atc.register(airplane2);

        airplane1.requestLanding();
    }
}
```

---

## **Output**
```plaintext
Flight 101 requesting landing.
Flight 202 received: Flight 101 is requesting landing.
```

---

## **Use Case & Benefits**
### **Use Cases**
- **Air Traffic Control Systems** (Managing aircraft communications)
- **Chat Room Applications** (Users communicating through a central server)
- **Smart Home Systems** (Devices interacting through a hub)
- **Workflow Automation** (Centralized event handling)

### **Benefits**
- **Reduces direct dependencies** between interacting objects
- **Improves maintainability and flexibility**
- **Centralizes communication** for better control

_____________________________________________________________________________________________________________________________
# **Iterator Pattern in Java**  

The **Iterator Pattern** is a **behavioral design pattern** that provides a way to access elements of a collection sequentially without exposing its underlying representation. It helps in iterating over different data structures uniformly.

---

## **Real-World Example: Book Collection in a Library**  
Consider a **Library Book Collection**, where a user needs to browse books one by one without knowing the internal storage mechanism.

When a user requests books:
- The **Iterator** provides access to books one at a time.
- The **BookCollection** manages books and provides an iterator for traversal.
- The **User** can iterate over the collection without knowing how books are stored.

---

## **Class Diagram**
```plaintext
      +-------------------+
      |   Iterator       |
      |-------------------|
      | + hasNext()      |
      | + next()         |
      +-------------------+
              ▲
              |
      +-------------------+
      | BookIterator     |
      |-------------------|
      | - books[]        |
      | - index         |
      | + hasNext()      |
      | + next()         |
      +-------------------+
              ▲
              |
      +-------------------+
      |  Iterable        |
      |-------------------|
      | + createIterator()|
      +-------------------+
              ▲
              |
      +-------------------+
      |  BookCollection  |
      |-------------------|
      | - books[]        |
      | + createIterator()|
      +-------------------+
```

---

## **Java Implementation**
```java
import java.util.ArrayList;
import java.util.List;

// Iterator Interface
interface Iterator {
    boolean hasNext();
    Object next();
}

// Concrete Iterator: BookIterator
class BookIterator implements Iterator {
    private List<String> books;
    private int index;

    public BookIterator(List<String> books) {
        this.books = books;
        this.index = 0;
    }

    @Override
    public boolean hasNext() {
        return index < books.size();
    }

    @Override
    public Object next() {
        if (this.hasNext()) {
            return books.get(index++);
        }
        return null;
    }
}

// Iterable Interface
interface IterableCollection {
    Iterator createIterator();
}

// Concrete Collection: BookCollection
class BookCollection implements IterableCollection {
    private List<String> books = new ArrayList<>();

    public void addBook(String book) {
        books.add(book);
    }

    @Override
    public Iterator createIterator() {
        return new BookIterator(books);
    }
}

// Client Code
public class IteratorPatternDemo {
    public static void main(String[] args) {
        BookCollection library = new BookCollection();
        library.addBook("Design Patterns");
        library.addBook("Clean Code");
        library.addBook("Refactoring");

        Iterator iterator = library.createIterator();
        while (iterator.hasNext()) {
            System.out.println("Book: " + iterator.next());
        }
    }
}
```

---

## **Output**
```plaintext
Book: Design Patterns
Book: Clean Code
Book: Refactoring
```

---

## **Use Case & Benefits**
### **Use Cases**
- **Library Systems** (Iterating over books, magazines, or articles)
- **Social Media Feeds** (Scrolling through posts sequentially)
- **Music Playlists** (Navigating songs in a playlist)
- **E-commerce Catalogs** (Browsing product lists)

### **Benefits**
- **Encapsulates iteration logic**
- **Provides uniform traversal over different collections**
- **Supports multiple traversal strategies**
- **Simplifies collection access without exposing internal details**

_________________________________________________________________________________________________________________________________________________

# **Visitor Pattern in Java**  

The **Visitor Pattern** is a **behavioral design pattern** that allows adding new operations to existing object structures without modifying them. It achieves this by using a separate visitor class that implements the new behavior.

---

## **Real-World Example: Shopping Cart System**  
Consider a **Shopping Cart** where different types of items (Books, Electronics) have different discount calculations.

When a customer checks out:
- The **Visitor** calculates the total cost with applicable discounts.
- The **Cart Items** (Book, Electronics) accept the visitor.
- The **Visitor** implements the discount logic without modifying the item classes.

---

## **Class Diagram**
```plaintext
      +------------------+
      |    Visitor      |
      |------------------|
      | + visit(Book)    |
      | + visit(Electronic) |
      +------------------+
              ▲
              |
      +------------------+
      |  ConcreteVisitor |
      |------------------|
      | + visit(Book)    |
      | + visit(Electronic) |
      +------------------+
              ▲
              |
      +------------------+
      |   Visitable     |
      |------------------|
      | + accept(Visitor) |
      +------------------+
              ▲
              |
  +----------------+    +----------------+
  |    Book       |    |  Electronic    |
  |----------------|    |----------------|
  | - price       |    | - price        |
  | + accept()    |    | + accept()     |
  +----------------+    +----------------+
```

---

## **Java Implementation**
```java
import java.util.ArrayList;
import java.util.List;

// Visitor Interface
interface Visitor {
    void visit(Book book);
    void visit(Electronic electronic);
}

// Concrete Visitor: Discount Calculator
class DiscountCalculator implements Visitor {
    @Override
    public void visit(Book book) {
        System.out.println("Book Discounted Price: " + (book.getPrice() * 0.9));
    }

    @Override
    public void visit(Electronic electronic) {
        System.out.println("Electronic Discounted Price: " + (electronic.getPrice() * 0.8));
    }
}

// Visitable Interface
interface Visitable {
    void accept(Visitor visitor);
}

// Concrete Element: Book
class Book implements Visitable {
    private double price;

    public Book(double price) {
        this.price = price;
    }

    public double getPrice() {
        return price;
    }

    @Override
    public void accept(Visitor visitor) {
        visitor.visit(this);
    }
}

// Concrete Element: Electronic
class Electronic implements Visitable {
    private double price;

    public Electronic(double price) {
        this.price = price;
    }

    public double getPrice() {
        return price;
    }

    @Override
    public void accept(Visitor visitor) {
        visitor.visit(this);
    }
}

// Client Code
public class VisitorPatternDemo {
    public static void main(String[] args) {
        List<Visitable> cart = new ArrayList<>();
        cart.add(new Book(100));
        cart.add(new Electronic(200));

        Visitor discountCalculator = new DiscountCalculator();
        for (Visitable item : cart) {
            item.accept(discountCalculator);
        }
    }
}
```

---

## **Output**
```plaintext
Book Discounted Price: 90.0
Electronic Discounted Price: 160.0
```

---

## **Use Case & Benefits**
### **Use Cases**
- **Shopping Cart Systems** (Applying different discounts per item type)
- **Tax Calculations** (Different tax rules per product category)
- **Compilers** (Traversing and analyzing abstract syntax trees)
- **Insurance Systems** (Different policy evaluations per user type)

### **Benefits**
- **Separates operations from object structures**
- **Easily adds new operations without modifying objects**
- **Promotes Open-Closed Principle**












