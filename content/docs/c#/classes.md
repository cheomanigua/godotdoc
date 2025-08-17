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

with polimorphism you can instantiate like this:

```csharp
Car toyota = new Toyota();
```

but not like this:
```csharp
Toyota toyota = new Car();
```
Poliphormism let you do this:

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
