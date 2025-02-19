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





