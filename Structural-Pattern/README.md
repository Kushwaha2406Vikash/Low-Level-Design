# Structural Design Patterns (Object Composition)

## Overview
A **Structural Design Pattern** defines how objects and classes are composed to form larger structures efficiently. It ensures flexibility and maintainability. Examples include:
- Adapter
- Bridge
- Composite
- Decorator
- Facade
- Flyweight
- Proxy

Each pattern addresses specific structural challenges in software design.

---

## Adapter Pattern

The **Adapter Pattern** is a structural design pattern that allows incompatible interfaces to work together. It acts as a bridge between two interfaces, converting one interface into another that the client expects.

### Components of Adapter Design Pattern
1. **Target Interface**: Defines the expected interface for the client.
2. **Adaptee**: The existing class/system with an incompatible interface.
3. **Adapter**: Bridges the target and adaptee by implementing the target interface and using an adaptee instance internally.
4. **Client**: Uses the target interface and remains unaware of the adaptee's details.

### Class Diagram
```
    +--------------------+
    | EnglishSpeaker     | (Adaptee: Existing API)
    | - speakEnglish()   |
    +--------------------+
             |
             |
             v
    +----------------------+
    | TranslatorAdapter    | (Adapter: Bridges APIs)
    | - communicate()      |
    +----------------------+
             ^
             |
             |
    +--------------------+
    | GermanSpeaker      | (Target: Expected API)
    | - communicate()    |
    +--------------------+
```

### Java Implementation
#### Step 1: Define Target Interface
```java
interface GermanSpeaker {
    void communicate(String words);
}
```
#### Step 2: Adaptee (Existing API)
```java
class EnglishSpeaker {
    void speakEnglish(String words) {
        System.out.println("Speaking in English: " + words);
    }
}
```
#### Step 3: Adapter
```java
class TranslatorAdapter implements GermanSpeaker {
    private EnglishSpeaker englishSpeaker;

    public TranslatorAdapter(EnglishSpeaker englishSpeaker) {
        this.englishSpeaker = englishSpeaker;
    }

    @Override
    public void communicate(String words) {
        String translatedWords = translateToEnglish(words);
        englishSpeaker.speakEnglish(translatedWords);
    }

    private String translateToEnglish(String germanWords) {
        if (germanWords.equalsIgnoreCase("Hallo")) {
            return "Hello";
        } else if (germanWords.equalsIgnoreCase("Wie geht es Ihnen?")) {
            return "How are you?";
        }
        return "Translation not available";
    }
}
```
#### Step 4: Client Code
```java
public class AdapterPatternDemo {
    public static void main(String[] args) {
        EnglishSpeaker englishPerson = new EnglishSpeaker();
        GermanSpeaker adapter = new TranslatorAdapter(englishPerson);

        adapter.communicate("Hallo");  // Translates to "Hello"
        adapter.communicate("Wie geht es Ihnen?");  // Translates to "How are you?"
    }
}
```
---

## Bridge Pattern

The **Bridge Pattern** separates an abstraction from its implementation so that both can evolve independently, reducing dependency and improving flexibility.

### Class Diagram
```
       ┌──────────────────┐
       │ RemoteControl   │  (Abstraction)
       ├──────────────────┤
       │ Device device    │  <──Bridge──> (Has a reference to Device)
       │ +turnOn()        │
       │ +turnOff()       │
       │ +setVolume()     │
       └────────▲─────────┘
                │
                │
  ┌────────────┴────────────┐
  │                         │
┌──────────────────┐  ┌──────────────────┐
│ BasicRemote      │  │ AdvancedRemote   │ (Refined Abstraction)
├──────────────────┤  ├──────────────────┤
│ +mute()         │  │ +enableVoice()    │
└──────────────────┘  └──────────────────┘
                ▲
                │
      ┌────────┴────────┐
      │  Device (Interface) │ (Implementor)
      ├──────────────────┤
      │ +turnOn()        │
      │ +turnOff()       │
      │ +setVolume()     │
      └────────▲─────────┘
               │
     ┌────────┴────────┐
     │                │
┌──────────────┐ ┌──────────────┐
│ TV           │ │ Radio        │ (Concrete Implementors)
├──────────────┤ ├──────────────┤
│ +turnOn()    │ │ +turnOn()    │
│ +turnOff()   │ │ +turnOff()   │
└──────────────┘ └──────────────┘
```

### Java Implementation
#### Step 1: Define Implementor Interface
```java
interface Device {
    void turnOn();
    void turnOff();
    void setVolume(int volume);
}
```
#### Step 2: Concrete Implementations
```java
class TV implements Device {
    private int volume = 50;

    @Override
    public void turnOn() {
        System.out.println("TV is turned ON");
    }

    @Override
    public void turnOff() {
        System.out.println("TV is turned OFF");
    }

    @Override
    public void setVolume(int volume) {
        this.volume = volume;
        System.out.println("TV volume set to " + this.volume);
    }
}

class Radio implements Device {
    private int volume = 30;

    @Override
    public void turnOn() {
        System.out.println("Radio is turned ON");
    }

    @Override
    public void turnOff() {
        System.out.println("Radio is turned OFF");
    }

    @Override
    public void setVolume(int volume) {
        this.volume = volume;
        System.out.println("Radio volume set to " + this.volume);
    }
}
```
#### Step 3: Abstraction
```java
abstract class RemoteControl {
    protected Device device;

    public RemoteControl(Device device) {
        this.device = device;
    }

    public void turnOn() {
        device.turnOn();
    }

    public void turnOff() {
        device.turnOff();
    }

    public void setVolume(int volume) {
        device.setVolume(volume);
    }
}
```
#### Step 4: Refined Abstractions
```java
class BasicRemote extends RemoteControl {
    public BasicRemote(Device device) {
        super(device);
    }

    public void mute() {
        System.out.println("Muting device...");
        device.setVolume(0);
    }
}

class AdvancedRemote extends RemoteControl {
    public AdvancedRemote(Device device) {
        super(device);
    }

    public void enableVoiceControl() {
        System.out.println("Voice control enabled.");
    }
}
```
#### Step 5: Test the Implementation
```java
public class BridgePatternExample {
    public static void main(String[] args) {
        Device tv = new TV();
        RemoteControl basicRemoteForTV = new BasicRemote(tv);
        basicRemoteForTV.turnOn();
        basicRemoteForTV.setVolume(60);
        ((BasicRemote) basicRemoteForTV).mute();
        basicRemoteForTV.turnOff();
    }
}
```
#### When to Use the Bridge Pattern?
- When you need to decouple abstraction and implementation to allow independent variations.
- When you have multiple orthogonal dimensions of a class hierarchy (e.g., different devices and different remotes).
- When you want to avoid an explosion of subclasses due to multiple combinations of functionalities.

- _______________________________________________________________________________________________________________________________

  # Design Patterns in Java

## Decorator Pattern

### Overview
The **Decorator Pattern** is a structural design pattern that allows adding new behavior to an object dynamically without altering its structure. It wraps an object to provide additional functionality while keeping the original object intact.

### Use Case
Imagine a coffee shop system where we have a basic coffee, and we want to add customizations (e.g., milk, sugar, or whipped cream). Instead of creating multiple subclasses for each combination, the Decorator Pattern enables adding these customizations dynamically.

### Class Diagram
```
            ┌──────────────────────┐
            │    Coffee (Component) │  (Abstract Component)
            ├──────────────────────┤
            │ +cost(): double       │
            └─────────────▲──────────┘
                          │
          ┌──────────────┴─────────────┐
          │                            │
    ┌──────────────────┐        ┌─────────────────┐
    │   SimpleCoffee  │        │  MilkDecorator  │  (Concrete Decorators)
    ├──────────────────┤        ├─────────────────┤
    │ +cost(): double │        │ +cost(): double │
    └──────────────────┘        └─────────────────┘
            │                          │
  ┌─────────┴─────────┐       ┌────────┴─────────┐
  │   SugarDecorator │       │ WhipDecorator    │ (Concrete Decorators)
  ├──────────────────┤       ├─────────────────┤
  │ +cost(): double │       │ +cost(): double │
  └──────────────────┘       └─────────────────┘
```

### Java Implementation
#### Step 1: Abstract Component (Coffee)
```java
interface Coffee {
    double cost();
}
```
#### Step 2: Concrete Component (SimpleCoffee)
```java
class SimpleCoffee implements Coffee {
    @Override
    public double cost() {
        return 5.0;  // Basic cost of a coffee
    }
}
```
#### Step 3: Abstract Decorator (CoffeeDecorator)
```java
abstract class CoffeeDecorator implements Coffee {
    protected Coffee decoratedCoffee;

    public CoffeeDecorator(Coffee coffee) {
        this.decoratedCoffee = coffee;
    }

    public double cost() {
        return decoratedCoffee.cost();  // Delegates the cost calculation
    }
}
```
#### Step 4: Concrete Decorators (MilkDecorator, SugarDecorator, WhipDecorator)
```java
class MilkDecorator extends CoffeeDecorator {
    public MilkDecorator(Coffee coffee) {
        super(coffee);
    }

    @Override
    public double cost() {
        return decoratedCoffee.cost() + 1.5;  // Add cost for milk
    }
}

class SugarDecorator extends CoffeeDecorator {
    public SugarDecorator(Coffee coffee) {
        super(coffee);
    }

    @Override
    public double cost() {
        return decoratedCoffee.cost() + 0.5;  // Add cost for sugar
    }
}

class WhipDecorator extends CoffeeDecorator {
    public WhipDecorator(Coffee coffee) {
        super(coffee);
    }

    @Override
    public double cost() {
        return decoratedCoffee.cost() + 2.0;  // Add cost for whipped cream
    }
}
```
#### Step 5: Test the Implementation
```java
public class DecoratorPatternExample {
    public static void main(String[] args) {
        Coffee simpleCoffee = new SimpleCoffee();
        System.out.println("Simple Coffee cost: $" + simpleCoffee.cost());

        // Adding milk and sugar dynamically using decorators
        Coffee milkCoffee = new MilkDecorator(simpleCoffee);
        System.out.println("Coffee with Milk cost: $" + milkCoffee.cost());

        Coffee milkSugarCoffee = new SugarDecorator(milkCoffee);
        System.out.println("Coffee with Milk and Sugar cost: $" + milkSugarCoffee.cost());

        Coffee fullyLoadedCoffee = new WhipDecorator(milkSugarCoffee);
        System.out.println("Fully Loaded Coffee cost: $" + fullyLoadedCoffee.cost());
    }
}
```

### When to Use the Decorator Pattern?
- When you need to add responsibilities to objects dynamically and flexibly.
- When you want to avoid large inheritance trees and prefer a combination of behaviors.
- When you need to extend functionality without altering the original object.

---

## Facade Pattern

### Overview
The **Facade Pattern** is a structural design pattern that provides a simplified interface to a complex subsystem. It hides the complexities of the system and provides a higher-level interface that makes it easier to interact with the subsystem.

### Use Case
Consider a home theater system with multiple subsystems like a DVD player, projector, sound system, and lights. Instead of requiring the user to interact with each component individually, we can create a **Facade** that provides a simple interface to control the entire system with one method.

### Class Diagram
```
                ┌─────────────────────┐
                │     HomeTheater     │  (Facade)
                ├─────────────────────┤
                │ +watchMovie()        │
                │ +endMovie()          │
                └─────────────▲───────┘
                              │
          ┌──────────────────┴────────────────┐
          │                  │                │
   ┌──────────────┐   ┌────────────────┐   ┌────────────┐
   │ DVDPlayer    │   │ Projector      │   │ SoundSystem │  (Subsystems)
   ├──────────────┤   ├────────────────┤   ├────────────┤
   │ +on()        │   │ +on()          │   │ +on()      │
   │ +play()      │   │ +setBrightness()│   │ +setVolume()│
   │ +off()       │   │ +off()         │   │ +off()     │
   └──────────────┘   └────────────────┘   └────────────┘
```

### Java Implementation
#### Step 1: Subsystems (DVDPlayer, Projector, SoundSystem)
```java
class DVDPlayer {
    public void on() { System.out.println("DVD Player is On."); }
    public void play() { System.out.println("DVD Player is Playing."); }
    public void off() { System.out.println("DVD Player is Off."); }
}

class Projector {
    public void on() { System.out.println("Projector is On."); }
    public void setBrightness() { System.out.println("Projector brightness set to high."); }
    public void off() { System.out.println("Projector is Off."); }
}

class SoundSystem {
    public void on() { System.out.println("Sound System is On."); }
    public void setVolume() { System.out.println("Sound System volume set to max."); }
    public void off() { System.out.println("Sound System is Off."); }
}
```
#### Step 2: Facade (HomeTheater)
```java
class HomeTheater {
    private DVDPlayer dvdPlayer;
    private Projector projector;
    private SoundSystem soundSystem;

    public HomeTheater(DVDPlayer dvdPlayer, Projector projector, SoundSystem soundSystem) {
        this.dvdPlayer = dvdPlayer;
        this.projector = projector;
        this.soundSystem = soundSystem;
    }
```
#### Step 3: Test the Implementation
```java
public class FacadePatternExample {
    public static void main(String[] args) {
        HomeTheater homeTheater = new HomeTheater(new DVDPlayer(), new Projector(), new SoundSystem());
        homeTheater.watchMovie();
        homeTheater.endMovie();
    }
}
```

#### Output
- Get ready to watch a movie...
- DVD Player is On.
- DVD Player is Playing.
- Projector is On.
- Projector brightness set to high.
- Sound System is On.
- Sound System volume set to max.

- Shutting down the home theater...
- DVD Player is Off.
- Projector is Off.
 - Sound System is Off.


### When to Use the Facade Pattern?
- When you want to simplify interactions with a complex subsystem.
- When you want to provide a unified interface to a set of interfaces in a subsystem.
- When you want to hide the complexities of the subsystem and provide a higher-level abstraction.

# Design Patterns: Flyweight and Proxy

## Flyweight Pattern

### Overview
The **Flyweight Pattern** is a structural design pattern that reduces memory usage by sharing common objects instead of creating new ones for every instance. It is useful when an application needs to create a large number of similar objects.

### Use Case
Imagine a text editor where each character in a document is an object. Without optimization, creating a new object for each character would consume a lot of memory. Using the Flyweight Pattern, we can reuse character objects instead of creating new ones, significantly improving efficiency.

### Class Diagram
```
           ┌───────────────────┐
           │   Flyweight       │  (Interface for shared objects)
           ├───────────────────┤
           │ +display(x, y)    │
           └─────────▲─────────┘
                     │
       ┌────────────┴─────────────┐
       │                          │
┌──────────────┐        ┌───────────────────┐
│ Character    │        │ FlyweightFactory  │  (Manages Flyweights)
│ (Concrete)   │        ├───────────────────┤
├──────────────┤        │ +getCharacter(c)  │
│ -symbol: char│        └───────────────────┘
│ +display(x,y)│        
└──────────────┘  
```

### Java Implementation

#### Step 1: Flyweight Interface
```java
interface CharacterFlyweight {
    void display(int x, int y);
}
```

#### Step 2: Concrete Flyweight Class (Reusable Character Objects)
```java
class CharacterConcrete implements CharacterFlyweight {
    private final char symbol;

    public CharacterConcrete(char symbol) {
        this.symbol = symbol;
    }

    @Override
    public void display(int x, int y) {
        System.out.println("Character: " + symbol + " displayed at (" + x + ", " + y + ")");
    }
}
```

#### Step 3: Flyweight Factory (Manages Flyweight Objects)
```java
import java.util.HashMap;
import java.util.Map;

class FlyweightFactory {
    private static final Map<Character, CharacterFlyweight> characterMap = new HashMap<>();

    public static CharacterFlyweight getCharacter(char symbol) {
        if (!characterMap.containsKey(symbol)) {
            characterMap.put(symbol, new CharacterConcrete(symbol));
            System.out.println("Creating new CharacterFlyweight for: " + symbol);
        }
        return characterMap.get(symbol);
    }
}
```

#### Step 4: Client Code (Using Flyweight Pattern)
```java
public class FlyweightPatternExample {
    public static void main(String[] args) {
        String document = "HELLO WORLD";

        int x = 0, y = 0;
        for (char c : document.toCharArray()) {
            CharacterFlyweight character = FlyweightFactory.getCharacter(c);
            character.display(x, y);
            x += 10; // Move right for next character
        }
    }
}
```

### When to Use the Flyweight Pattern?
- When an application needs to create a large number of similar objects.
- When memory optimization is critical.
- When objects have intrinsic (shared) and extrinsic (unique) states.

---

## Proxy Pattern

### Overview
The **Proxy Pattern** is a structural design pattern that provides a placeholder or surrogate for another object. It controls access to the original object, adding extra functionality like lazy initialization, security, logging, or caching without modifying the real object.

### Use Case
Imagine a bank ATM system where accessing an account requires verification before performing transactions. Instead of allowing direct access to the account, a proxy ensures that authentication is performed before granting access.

### Class Diagram
```
               ┌───────────────┐
               │  BankAccount  │  (Subject Interface)
               ├───────────────┤
               │ +withdraw()    │
               └───────▲───────┘
                       │
         ┌─────────────┴─────────────┐
         │                           │
 ┌────────────────┐        ┌──────────────────┐
 │ RealAccount    │        │ AccountProxy     │ (Proxy)
 │ (Actual Object)│        │ (Controls Access)│
 ├────────────────┤        ├──────────────────┤
 │ +withdraw()    │        │ +withdraw()      │
 └────────────────┘        └──────────────────┘
```

### Java Implementation

#### Step 1: Subject Interface (BankAccount)
```java
interface BankAccount {
    void withdraw(double amount);
}
```

#### Step 2: Real Subject (RealAccount - Actual Bank Account)
```java
class RealAccount implements BankAccount {
    private double balance;

    public RealAccount(double initialBalance) {
        this.balance = initialBalance;
    }

    @Override
    public void withdraw(double amount) {
        if (amount <= balance) {
            balance -= amount;
            System.out.println("Withdrawal of $" + amount + " successful. Remaining balance: $" + balance);
        } else {
            System.out.println("Insufficient funds! Current balance: $" + balance);
        }
    }
}
```

#### Step 3: Proxy Class (AccountProxy - Adds Security Check)
```java
class AccountProxy implements BankAccount {
    private RealAccount realAccount;
    private String accountHolder;

    public AccountProxy(String accountHolder, double initialBalance) {
        this.accountHolder = accountHolder;
        this.realAccount = new RealAccount(initialBalance);
    }

    private boolean authenticate(String user) {
        return this.accountHolder.equals(user);
    }

    @Override
    public void withdraw(double amount) {
        String user = "JohnDoe";  // Simulating user input
        if (authenticate(user)) {
            System.out.println("Authentication successful for " + user);
            realAccount.withdraw(amount);
        } else {
            System.out.println("Authentication failed! Access denied.");
        }
    }
}
```

#### Step 4: Test the Implementation
```java
public class ProxyPatternExample {
    public static void main(String[] args) {
        BankAccount myAccount = new AccountProxy("JohnDoe", 1000.0);

        myAccount.withdraw(200.0);
        myAccount.withdraw(900.0);  // Should show insufficient funds
    }
}
```

### When to Use the Proxy Pattern?
- When you need to control access to an object.
- When adding lazy initialization to avoid expensive object creation.
- When adding logging, security, or caching without modifying the real object.

