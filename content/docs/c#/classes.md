---
weight: 2250
title: "Classes"
description: "Classes and Object Oriented Programming in C#"
icon: "article"
date: "2025-08-14T10:03:49+01:00"
lastmod: "2025-08-14T10:03:49+01:00"
draft: false
toc: true
---

## 1. OOP Basics

### 1.1. Inheritance

In C# it is not possible to inherit from several classes. So it's necessary to select the class to inherit from.

```csharp
class Character { }
class Wizard : Character { }
```

### 1.2. Encapsulation

Everything inside a class is encapsulated, and the amount of encapsulation depends on the scope:

- **Public**: the element is visible both inside and outside the class.
- **Private**: the element is visible only inside the class.
- **Protected**: the element is visible only inside the class and within the child classes hierarchy.

### 1.3. Polymorphism

Given the parent class and child class:

```csharp
class Car { }
class Toyota : Car { }
```

with polymorphism you can instantiate like this:

```csharp
Car toyota = new Toyota();
```

but not like this:
```csharp
Toyota toyota = new Car();
```
Polymorphism let you do this:

```csharp
public class Driver
{
    public void LogIn(Car car) { }
}

var john = New Driver();
john.LogIn(new Car(2025));
john.LogIn(new Toyota());
```
However:

```csharp
public class Driver
{
    public void LogIn(Toyota toyota) { }
}

var john = New Driver();
john.LogIn(new Car(2025)); // Compiler error
john.LogIn(new Toyota()); // Fine
```
## 2. Class definition

A class must first be defined and later on instantiated. A class is composed of methods and data.

### 2.1. Methods

A method is an action that can be invoked within the class. Three questions have to be made when declaring a method:

1. Can the action be performed outside the class or only inside the class?
2. Does the action returns a value?

    ```csharp
    public string hello ()
    {
        string message = "Hello world";
        return message;
    }
    ```

3. Does the action require certain information to run?

    ```csharp
    public void addition (int a, int b)
    {
        int result = a + b;
        GD.Print(result);
    }
    ```

### 2.2. Data

A data can be declared inside a class either via a property or via a member. The simple one is via a member:

#### Member

```csharp
public int purchaseYear;
public readonly int purchaseYear; // can only be assigned during instantiation
```

#### Property

Data that is exposed via a read method (`get`) and a write method (`set`).

```csharp
private int purchaseYear;
public int PurchaseYear
{
    get { return purchaseYear; }
    private set { purchaseYear = value; }
}
```

is the same as:

```csharp
public int purchaseYear { get; private set; }
```
If the property has a public write method (`set`), you can assign a value after the declaration:

```csharp
public int purchaseYear { get; set; } = 2025;
```

It is also possible to use an indexed property with the write method (`get`):

```csharp
public Car this[int lotNumber]
{
    get { ... }
}
```
## 3. Class instantiation

### 3.1. Constructor

All classes must have a constructor. If you don't define one, the C# compiler will use a default constructor:

```csharp
public Car() { }
```

We can define a constructor:

```csharp
public Car(int purchaseYear)
{
    PurchaseYear = purchaseYear;
}
```

In case of inheritance, the parent class constructor must be called first:

```csharp
public class Car
{
    public Car()
    {
        Console.WriteLine("Car constructor");
    }
}

public class Toyota : Car
{
    public Toyota()
    {
        Console.WriteLine("Toyota constructor");
    }
}
```

If the parent class constructor uses a paramenter, then the child class must fill that parameter by using the key word `base`:

```csharp
public class Car
{
    public Car(int purchaseYear)
    {
        Console.WriteLine("Car constructor");
    }
}

public class Toyota : Car
{
    public Toyota() : base(2025)
    {
        Console.WriteLine("Toyota constructor");
    }
}
```

### 3.2. New

To create the actual instance, the `new` keyword is used. Given the class constructor:

```csharp
public Car(int purchaseYear)
{
    PurchaseYear = purchaseYear;
}
```
you can create an instance:

```csharp
Car car = new Car(2025);
```
or

```csharp
Car car = new(2025);
```

If the class constructor has no parameters:

```csharp
Car car = new Car();
```
or

```csharp
Car car = new();
```

### 3.3. Object initializer

If the class has a constructor with no parameters, you can use an object initializer.

Given the class:

```csharp
public class Employee
{
    public string Name { get; set; }
    public string Surname { get; set; }
    public bool isVIP { get; private set; }
}
```

you can assign values to a newly created instance using an object initializer: 

```csharp
var employee = new Employee { Name = "John", Surname = "Doe" };
```
As you can see above, since the boolean value `isVip` is using a private write method (`set`), it can only be defined inside the class and not by using the object initializer.

After instantiation, you can change `Name` or `Surname` like this:

```csharp
employee.Name = "Alice";
```

## 4. Virtual methods

When a class inherits from another, the child class has naturally the methods and data from the parent class. Sometimes, however, the child class may redefine some methods from the parent class. In that case:

* On the parent class, the keyword `virtual` must be used before the method name.
* On the child class, the keyword `override` must be used before the method to be replaced from the parent class.

```csharp
public class Car
{
    public virtual void StartEngine()
    {
        Console.WriteLine("Starting up engine...");
    }
}

public class Toyota : Car
{
    public override void StartEngine()
    {
        Console.WriteLine("Toyota is starting up engine...");
    }
}
```

## 5. Abstract class

An **abstract class** in C# is a class that cannot be instantiated directly and is designed to serve as a base class for other classes. It is declared using the `abstract` keyword and can contain both abstract members (methods, properties, etc., without implementation) and non-abstract members (with implementation). Derived classes must implement all abstract members unless they are also abstract.

### Key Characteristics of an Abstract Class:
1. **Cannot be instantiated**: You cannot create an object of an abstract class using `new`.
2. **May contain abstract members**: These are declared without implementation, and derived classes must provide the implementation.
3. **Can contain non-abstract members**: These provide shared functionality for derived classes.
4. **Can include constructors**: These are used to initialize fields when derived classes are instantiated.
5. **Inheritance requirement**: Only classes that inherit from the abstract class can implement its abstract members.
6. **Can be extended**: Derived classes inherit from it using the `:` operator and must implement all abstract members or be abstract themselves.

### Syntax Example:
```csharp
public abstract class Animal
{
    public string Name { get; set; } // Non-abstract property
    public abstract void MakeSound(); // Abstract method (no implementation)
    
    public void Sleep() // Non-abstract method
    {
        Console.WriteLine($"{Name} is sleeping.");
    }
}

public class Dog : Animal
{
    public override void MakeSound() // Must implement abstract method
    {
        Console.WriteLine("Woof!");
    }
}
```

### When to Use an Abstract Class:
Use an abstract class when:
1. **Shared Base Functionality**: You want to provide common functionality (fields, methods, or properties) for a group of related classes while enforcing certain behaviors to be implemented by derived classes.
   - Example: A base `Shape` class with a common `Color` property and an abstract `CalculateArea()` method that each derived shape (e.g., `Circle`, `Rectangle`) must implement.
2. **Hierarchy Design**: You need to define a template for a class hierarchy where some methods are mandatory but their implementation varies.
   - Example: A `Vehicle` class with an abstract `StartEngine()` method, implemented differently for `Car` and `Motorcycle`.
3. **Prevent Instantiation**: You want to ensure the base class cannot be instantiated directly, as it represents a general concept rather than a concrete object.
   - Example: `Animal` as an abstract class, with specific animals like `Dog` or `Cat` as concrete implementations.
4. **Mix of Abstract and Concrete Members**: Unlike interfaces, abstract classes can include implemented methods, making them suitable when you want to combine enforced contracts with reusable code.

### Abstract Class vs. Interface:
- **Abstract Class**:
  - Can have both abstract and non-abstract members.
  - Supports fields, constructors, and access modifiers.
  - A class can inherit only one abstract class (single inheritance).
  - Use when you need shared implementation and a clear hierarchical relationship.
- **Interface**:
  - Contains only method signatures, properties, or events (no implementation until default implementations in C# 8.0+).
  - No fields or constructors.
  - A class can implement multiple interfaces.
  - Use for defining contracts without shared implementation.

### Example Use Case:
Suppose you're building a game with different types of characters (e.g., `Warrior`, `Mage`). You can use an abstract class `Character` to define common properties like `Health` and `Name`, and an abstract method `Attack()` that each character type implements differently.

```csharp
public abstract class Character
{
    public string Name { get; set; }
    public int Health { get; set; }

    public abstract void Attack();
}

public class Warrior : Character
{
    public override void Attack()
    {
        Console.WriteLine($"{Name} swings a sword!");
    }
}

public class Mage : Character
{
    public override void Attack()
    {
        Console.WriteLine($"{Name} casts a fireball!");
    }
}
```

### When Not to Use:
- If you only need a contract without shared implementation, use an **interface** instead.
- If you need multiple inheritance, interfaces are better, as C# does not support multiple class inheritance.
- If the class can be instantiated on its own, it should not be abstract.

In summary, use abstract classes when you need a base class with shared functionality and want to enforce specific behaviors in derived classes while preventing direct instantiation.



## 6. Interface

In C#, an **interface** is a contract that defines a set of methods, properties, events, or indexers that a class or struct must implement, without providing any implementation details. It’s like a blueprint that ensures any class implementing the interface provides specific functionality.

There are no scopes inside an interface, everything is public by convention.

### Key Characteristics of an Interface
- **Syntax**: Defined using the `interface` keyword, e.g., `public interface IMyInterface`.
- **Members**: Can include method signatures, properties, events, or indexers, but no implementation (prior to C# 8.0; default implementations were added in C# 8.0).
- **No Fields**: Interfaces cannot contain fields or constructors.
- **Multiple Inheritance**: A class can implement multiple interfaces, unlike class inheritance, which is single in C#.
- **Access Modifier**: Interface members are implicitly public and cannot have access modifiers like `private` or `protected`.

### Example
```csharp
public interface IVehicle
{
    void Start();
    void Stop();
    int Speed { get; }
}

public class Car : IVehicle
{
    public int Speed { get; private set; }
    public void Start() { Console.WriteLine("Car started."); }
    public void Stop() { Console.WriteLine("Car stopped."); }
}

public class Truck : IVehicle
{
    public int Speed { get; private set; }
    public void Start() { Console.WriteLine("Truck started."); }
    public void Stop() { Console.WriteLine("Truck stopped."); }
}

public class RemoteControl
{
    public void StartEngine(IVehicle vehicle)
    {
        vehicle.Start();
    }
}

var remoteControl = new RemoteControl();
var truck = New Truck();
remoteControl.StartEngine(truck); // will print "Truck started."
```
#### Inheritance

Inheritance is possible between interfaces:

```csharp
public interface IAutomaticGear
{
    void changeGear(string gear);
}

public interface IVehicle : IAutomaticGear
{
    void Start();
}

public class Car : IVehicle
{
    public void Start() { Console.WriteLine("Car started."); }
    public void changeGear(string gear) { Console.WriteLine(gear); }
}
```

#### Polymorphism

Polymorphism is possible with interfaces:

```csharp
public void CheckList(IAutomaticGear augear) { }

var car = new Car();
CheckList(car);
```

### When to Use an Interface
Use interfaces when you want to:
1. **Define a Contract**: Ensure that different classes implement the same set of methods or properties, promoting consistency (e.g., `IEnumerable` for collections).
2. **Enable Polymorphism**: Allow different classes to be treated uniformly through a common interface type, regardless of their specific implementation.
3. **Support Loose Coupling**: Reduce dependencies between components by programming to an interface rather than a concrete class.
4. **Facilitate Dependency Injection**: Interfaces make it easier to swap implementations (e.g., in testing or modular design).
5. **Allow Multiple Inheritance**: Since C# doesn’t support multiple class inheritance, interfaces provide a way to achieve similar flexibility.
6. **Standardize Behavior Across Unrelated Classes**: For example, a `Car` and a `Bicycle` can both implement `IVehicle` despite having no common base class.

### Practical Scenarios
- **Framework Design**: Libraries like .NET use interfaces extensively (e.g., `IDisposable` for resource cleanup, `IComparable` for sorting).
- **Unit Testing**: Interfaces allow mocking dependencies (e.g., `IRepository` for a data layer).
- **Extensibility**: Enable future implementations without modifying existing code (e.g., plugin systems).
- **Cross-Cutting Concerns**: Standardize behavior like logging or authentication across unrelated components.

### When *Not* to Use an Interface
- **Implementation Details Required**: If you need shared implementation logic, consider an **abstract class** instead, as interfaces (pre-C# 8.0) cannot provide implementation.
- **Tight Coupling Acceptable**: If classes are closely related and share a common base with behavior, a base class might be simpler.
- **Premature Abstraction**: Avoid creating interfaces for every class unless there’s a clear need, as it can overcomplicate the design.

### Example with Dependency Injection
```csharp
public interface ILogger
{
    void Log(string message);
}

public class ConsoleLogger : ILogger
{
    public void Log(string message)
    {
        Console.WriteLine(message);
    }
}

public class App
{
    private readonly ILogger _logger;

    public App(ILogger logger)
    {
        _logger = logger;
    }

    public void Run()
    {
        _logger.Log("Application running.");
    }
}
```

Here, `App` depends on the `ILogger` interface, making it easy to swap `ConsoleLogger` with, say, a `FileLogger` without changing `App`’s code.

### Key Considerations
- **C# 8.0+ Default Implementations**: Interfaces can now include default method implementations, but use this sparingly to avoid blurring the line between interfaces and abstract classes.
- **Naming**: By convention, interface names start with `I` (e.g., `IList`, `IDisposable`).
- **Explicit Implementation**: A class can implement an interface explicitly to avoid polluting its public API (e.g., `void IVehicle.Start()`).

In summary, use interfaces to define contracts for behavior, promote flexibility, and enable polymorphism, especially in scenarios requiring loose coupling or multiple implementations of the same functionality.
