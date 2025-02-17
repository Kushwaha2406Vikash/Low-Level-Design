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

