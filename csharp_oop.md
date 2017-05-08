###### *** work in progress

# OOP in C#

## Recursion

Method that calls itself. A common example is solving a factorial i.e. n!

```
static int Factorial(int num) {
  if (num == 1)
    return 1;
  return num * Factorial(num - 1);
}
// Factorial(3) = 3 * Factorial(2) = 3 * 2 * Factorial(1) = 3 * 2 * 1 = 6
```

It's very important to have a break-out condition (ex: ```if (num == 1) return 1; ```) so the recursive loop does not go on infinitely, causing an enormous resource drain and eventual crash.

## Overloading

Multiple methods that have the same name but different parameters (of different data types and/or number of parameters).

```
static void printVariable(string thing) {
  Console.WriteLine(thing);
}

static void printVariable(int thing) {
  Console.WriteLine(thing);
}
```

## Classes

A class is a collection of items such as fields & methods that share the same attributes and actions. It contains items that relate to each other in a logical fashion: attributes, actions, behaviors, etc.

```
public class Person {
  string name;
  int age;
  
  public void SayHello() {
    Console.WriteLine("Hello");
  }
}

static void Main(string[] args) {
  Person p = new Person();
  p.SayHello(); // outputs "Hello"
}
```

## Structs

A struct is a value type that is used to encapsulate small groups of related variables. It's like a class, but simpler.

Structs can:

* contain methods, properies, fields, etc.
* have constructors _with_ parameters.

This differs from a class as it _does not_ support inheritance, virtual methods, etc.

|Structs|Classes|
|-------|-------|
|small data structures|complex behavior or data|
|not intended to be modified after created|intended to be modified after created|

Think of structs as a __custom data type__!

```
struct Book {
  public string title;
  public double price;
  public sring author;
}

struct Point {
  public int x;
  public int y;
  public Point(int x, int y) {
    this.x = x;
    this.y = y;
  }
}

static void Main(string[] args) {
  Book myBook;
  myBook.title = "The Fellowship of the Rings";
  myBook.author = "J.R.R. Tolkien";
  myBook.price = 29.99;
  
  Point p = new Point(42, 69);
  
  Console.WriteLine(myBook.title); // outputs "The Fellowship of the Ring"
  Console.WriteLine(p.x); // outputs 42
}
```

## Value Types & Reference Types

Built-in data types (such as _string_, _int_, _double_, _structs_) are used to declare variabels that are __value types__. When a value type instance is created, a single space in memory is allocated to store its value. When the program accesses a value type, it's directly accessing its underlying data.

__Reference types__ (such as a class) are handled differently. An object is created in memory and the class instantiation is a reference to that.

```
Point p1 = new Point();       // Point is a *struct*, which allocates a value in memory
Form f1 = new Form();         // Form is a *class*, which allocates the object (new Form) in memory and a reference (f1)

Point p2 = p1;                // p2 is a copy of p1 but a seperate value in memory
Form f2 = f1;                 // f2 is a copy of the reference f1, still pointing to the first object that was created

Point myPoint = new Point(0, 0);  // a new value type variable
Form myForm = new Form();         // a new reference-type variable
Test(myPoint, myForm);            // Test() is a method defined below

void Test(Point p, Form f) {
  p.X = 100;                      // No effect on myPoint since p is a copy
  f.Text = "Hello, World";        // myForm.Text is changed to "Hello, World" since myForm and f point to the same object
  f = null;                       // f is now null (erased) but no effect on myForm; f is only a copy of the reference
}
```

However if the parameters are passed by reference (using the ref modifier), there are different results. _ref_ passes the original variable to the method, not a copy.

```
Point myPoint = new Point(0, 0);      // a new value type variable
Form myForm = new Form();             // a new reference-type variable
Test(ref myPoint, ref myForm);        // pass myPoint and myForm by reference

void Test (ref Point p, ref Form f); {
  p.X = 100;                          // myPoint.X is changed to 100
  f.Text = "Hello, World";            // myForm.Text is changed to "Hello, World"
  f = null;                           // Both f and myForm are null (erased)
}
```

### Memory Allocation

the .Net CLR (Common Language Runtime) allocates memory for objects in two places: the _stack_ and the _heap_.

The stack is a simple first-in last-out (FILO) memory structure. When a method is invoked, the CLR bookmarks the top of the stack, and then _pushes_ data onto the stack when it executes. When the method completes, the CLR resets back to its previous state, essentially _popping_ the method's memory allocations from it.

The heap is described better as a random mess of objects. This allows objects to be allocated and de-allocated in a random order. Memory managers and garbage collectors help keep this area of memory tidy.

Example:

```
void CreateTextBox() {
  string txt = "Hello World";
  TextBox myTextBox = new TextBox(txt);
}
```

|Stack|Heap |
|-----|-----|
|txt (containing "Hello World") |     |
|myTextBox reference|TextBox object (with properties such as Location, Size, Text)|

The stack is always used to store:
* The _reference_ portion of reference-type local variables and parameters (i.e. myTextBox reference)
* Value type local variables and method parameters (structs, int, string, bool, char, DateTime, etc)

The heap stores:
* The _content_ of reference type objects
* Anything structured inside a reference type object (e.g. variables inside of a class)

## Encapsulation

Encapsulation is the process of organizing related items (such as fields, properties, methods, etc) within a logical unit. This process is more than combining members in a class. Its purpose is more about "protecting" members of a class and the ability to hide information from other parts of the program. This can be implemented by using access modifiers. These modifiers define the scope and visibility of a class member.

Fields & methods are accessible:
* public - outside of the class
* private - only from within the class
* protected - from within the class __AND__ derived classes
* sealed - only from within the class __AND__ prevents inheritance

Fields that are to be accessed outside of the class should be private. They should be accessed/modified via a _property_ (get, set). This allows regulation of data, verification, and security.

```
class Person {
  private string name; // field
  private int age = 0;
  
  public string Name { // property
    get { return name; }
    set { name = value; }
  }
  
  public int Age {
    get { return age; }
    set {
      if (value > 0)
        age = value;
      }
    }
}

static void Main(string[] args) {
  Person p = new Person();
  p.Name = "Bob"; // uses .Name property, not .name field
  p.Age = 18;
  p.Age = -20;
  Console.WriteLine(p.Name); // Bob
  Console.WriteLine(p.Age); // 18
}
```

### Auto-implemented Properties

Fast and effective way to declare private members via properties if you do not need any custom logic. Do not need to declare the private field separately.

```
public string Name { get; set; }
```

### Abstraction

Abstraction is providing only essential information to the rest of the program and hiding the rest of it so it's only usable to the class itself. Seperation of concerns is a big deal in object oriented programming. Here we are separating code that relates to interface and implementation.

Abstraction and Encapsulation are related. Abstraction allows making relevant information visible and Encapsulation enables a programmer to implement the desired level of abstraction.

## Constructors & Deconstructors

### Constructors

Method that is defined within a class and is invoked _upon instantiation_. It has the exact same name as its class, has no return type, and is always public.

```
class Person {
  public string Name { get; set; }
  
  // this is the constuctor
  public Person() {
    Console.WriteLine("Hello there!");
  }
}
// Every time a Person object is created, "Hello there!" will print to the console.
```

Can be useful for setting initial values via parameters.

```
class Person {
  private string name;
  public Person(string nm) {
    name = nm;
  }
  public string getName() {
    return name;
  }
}

static void Main(string[] args) {
  Person p = new Person("Bob");
  Console.WriteLine(p.getName()); // Bob
}
```

### Deconstructors

Method that is defined within a class and is invoked when an object is destroyed. It has the exact same name as its class and is prefixed with a tilde (~).

Deconstructors can be useful for releasing resources such as closing files, releasing memory, etc.

```
class Person {
  ~Person() {
    // stuff to do when object is destroyed
  }
}
```

## Static

Static variables and methods belong only to the __class__, not the object that is created from the class. Within memory, there is __only one__ copy of the static member. This member must be accessed by the _class name_, not the object.

```
class Cat {
  public static int count = 0;
  public static void Meow() {
    Console.WriteLine("Meow");
  }
  public Cat() {
    count++;
  }
}

static void Main(string[] args) {
  Cat c1 = new Cat();
  Cat c2 = new Cat();
  Console.WriteLine(Cat.count); // outputs 2
  Cat.Meow();
}
```

Constant members (const) are static by definition.

Static classes can only contain static members and cannot be instantiated into an object. Common examples of static classes are the Main and Math classes.

## this

Use the __this__ keyboard inside a class to refer to the current instance of the class (the current object).

```
class Person {
  private string name;
  public Person(string name) {
    this.name = name; // use this to not be confused with the private member
  }
}
```

## readonly

Use the __readonly__ modifier to prevent a member of a class from being modified after construction.

Readonly and constants differ in 3 major ways:

* A constant must be initialized when it is declared. A readonly field does not need to be.
* A readonly field value can be changed in a constructor.
* A readonly field can be assigned a value that is a result of a calculation. Constants cannot.

## Indexers

An indexers allows objects to be indexed like an array. Declaration happens similar to a property except that indexer accessors require an index.

```
class Group {
  private string[] names = new string[10];
  
  public string this[int index] {
    get { return names[index]; }
    set { names[index] = value; }
  }
}

static void Main(string[] args) {
  Group g = new Group();
  g[0] = "Frodo";
  g[1] = "Samwise";
  g[2] = "Meriadoc";
  g[3] = "Peregrin";
  
  Console.WriteLine(g[1]); // Samwise
}
```

## Operator Overloading

Say we have this class:

```
class Box {
  public int Height { get; set; }
  public int Width { get; set; }
  public Box(int h, int w) {
    Height = h;
    Width = w;
  }
}

static void Main(string[] args) {
  Box b1 = new Box(14, 3);
  Box b2 = new Box(5, 7);
}
```

And we would like to add two Box objects to make a bigger Box. We're thinking it should work like ```Box b3 = b1 + b2;``` and have the combined heights and widths of the 2 boxes.

We can do so by creating a special method called an __overloaded operator__. The keyword _operator_ is used followed by the symbol for the operator being defined (+, -, *, /). Even comparison operators can be overloaded.

This example will be be overloading the + operator:

```
class Box {
  public int Height { get; set; }
  public int Width { get; set; }
  public Box(int h, int w) {
    Height = h;
    Width = w;
  }
}

public static Box operator+(Box a, Box b) {
  int h = a.Height + b.Height;
  int w = a.Width + a.Width;
  Box result = new Box(h, w);
  return result;
}

static void Main(string[] args) {
  Box b1 = new Box(14, 3);
  Box b2 = new Box(5, 7);
  Box b3 = b1 + b2;
  
  Console.WriteLine(b3.Height); // 19
  Console.WriteLine(b3.Width); // 10
}
```

## Inheritance

Inheritance refers to when a class is defined based on another class and it inherits fields & methods from its parent class.

| Base Class | Derived Class |
|:----------:|:-------------:|
| base class features|base class features|
|            | derived class features |

Inheritance allows the derived class to reuse code from the base class without needing to rewrite it.

```
class Animal {
  public int Legs { get; set; }
  public int Age { get; set; }
}

class Dog : Animal {
  public Dog() {
    Legs = 4;
  }
  
  public void Bark() {
    Console.WriteLine("Woof!");
  }
}
```

### Constructor/Deconstructor order for a derived class

1. base constructor
2. derived constructor
3. derived deconstructor
4. base deconstructor

## Polymorphism

> pol·y·mor·phism /ˌpälēˈmôrfizəm/ noun:
>
> _the condition of occurring in several different forms._

__Technical Definition__: Polymorphism occurs when there is a hierarchy of classes and they are related through inheritance from a common base class. A call to a member method will cause a different implementation to be executed depending on the type of object that invokes the method.

__Plain English__: A single method can have a number of different implementations, based on the object that calls it.

### keywords when defining polymorphic methods

__virtual__ - used on the base class method

__override__ - used on the derived class methods

```
class Hero {
  public virtual void Weapon() {
    Console.WriteLine("Fisticuffs");
  }
}

class Rogue : Hero {
  public override void Weapon() {
    Console.WriteLine("Daggers");
  }
}

class Archer : Hero {
  public override void Weapon() {
    Console.WriteLine("Bow & Arrows");
  }
}

class Warrior : Hero {
  public override void Weapon() {
    Console.WriteLine("Sword");
  }
}

static void Main(stringp[ args) {
  Hero new_avatar = new Rogue();
  new_avatar.Weapon(); // outputs "Daggers"
}
```

The use of polymorphism may not seem apparent right away but without it, these different methods would end up being overloaded within the base class to account for every derived class. The methods belong in the derived classes, not the base. It's more about concise and semantic code that will be maintainable.

Using the example above, the different Weapon() methods cannot be placed (overloaded) in the base Hero class. Not all heroes know how to use all weapons, only those who trained with them - hence the derived classes for each specialty.

## Abstract Classes & Methods

Polymorphism can be taken a step further and the base class can be considered _abstract_, where there is no implmentation. The implementation must be defined in the derived classes.

Abstract classes & methods:

* cannot be instantiated
* may incldue concrete & abstract members
* must have their implementations defined in the _derived_ classes for __all__ inherited abstract members.

Why do this? Because in some situations, there is no meaningful need for the virtual method to have a separate definition in the base class. The class is declared as abstract to provide a _template_. This also prevents accidental coding issues when working on a team. Only define a base class as abstract if it is never meant to be used as an object itself.

```
abstract class Shape {
  public abstract void Draw();
}

class Circle : Shape {
  public override void Draw() {
    // draw circle stuff
  }
}

class Rectangle : Shape {
  public override void Draw() {
    // draw rectangle stuff
  }
}

static void Main(string[] args) {
  Shape c = new Circle();
  c.Draw();
  // draws circle stuff according to the derived class
}
```

## Interfaces

An interface is a completely abstract class, containing only abstract properties and methods (no fields/variables). It's common to preface an interface name with a capital letter I.

An interface simply describes what a class should do. The class implementing the interface must define how to accomplish the behaviors.

```
public interface IShape {
  void Draw();
}

class Circle : IShape {
  public void Draw() {
    // draw circular stuff
  }
}

static void Main(string[] args) {
  IShape c = new Circle();
  c.Draw();
}
```

A class can inherit from just one base class but it can implement _multiple_ interfaces. So by using interfaces you can include behavior from multiple sources in a class. ```class A : IShape, IAnimal ```

## Nested Classes

Classes can also be members of another class.

```
class Car {
  string model;
  public Car(string mdl) {
    model = mdl;
    Motor m = new Motor();
  }
  public class Motor {
    // more definitions
  }
}
```

Sometimes this is needed for semantics and separation of concerns which in turns makes maintenance and debugging easier in the future.

## Namespaces

Namespaces are scopes of a program that contain sets of related objects. All .Net framework classes are organized into namespaces and custom namespaces can be created as well. When referencing a class, it can be specified either by its fully qualified name (namespace.class) ``` System.Console.WriteLine("Hello"); ``` or it can be shortened to just the class name if the namespace is specified at the beginning of the source code ``` using System; ``` then later ``` Console.WriteLine("Hello"); ```.

## Enums

Enumerations are types that contain a set of named constants in an indexed list. The indices are consecutive but the numbers can be changed somewhat.

```
enum Days { Sun, Mon, Tue, Wed, Thu, Fri, Sat };
// 0, 1, 2, 3, 4, 5, 6
enum Days { Sun, Mon, Tue = 4, Wed, Thu, Fri, Sat };
// 0, 1, 4, 5, 6, 7, 8

int x = (int)Days.Thu;
Console.WriteLine(x); // outputs 6
```

## Generic Methods

Generic methods allow a method to be used with multiple data types. They can be used in place of overloading methods, resulting in less code to write & maintain.

Instead of writing:

```
static void Swap(ref int a, ref int b) {
  int temp = a;
  a = b;
  b = temp;
}

static void Swap(ref string a, ref string b) {
  string temp = a;
  a = b;
  b = temp;
}
```

We can write:

```
static void Swap<T>(ref T a, ref T b) {
  T temp = a;
  a = b;
  b = temp;
}
```

T is a __generic__ type. When calling the method the T is replaced by a matching data type within the angle brackets.

```
Swap<int>(ref x, ref y);

Swap<string>(ref x, ref y);
```

Multiple generic types can be specified to a method, seperated with a comma. ``` MethodName<T,U>( ... ) ```

## Generic Classes

Classes can be generic along the same lines of thinking. A common use for a generic class is for defining collections of items, where adding & removing items are performed in the same way regardless of data type.

A common generic class is a collection called a stack, where items are __pushed__ (added to the top of the stack) and __popped__ (removed from the top of the stack). This is usually referred to as LIFO (Last In, First Out) since the item that is removed on a pop operation is the last one that was recently added. The .Net Framework has this Stack<T> class built-in under the System.Collections.Generic namespace.

|LIFO Example, credit Wikipedia|
|:----------------------------:|
|![LIFO Example, credit Wikipedia](https://raw.githubusercontent.com/aromig/notes/master/images/Lifo_stack.png "LIFO Example, credit Wikipedia")|

While the below example is not the Stack<> class exactly (and far from a perfect implementation), it should show what we're talking about.

```
public class Stack<T> {
  readonly int max_Size;
  int index = 0;
  T[] items;
  
  public Stack() : this(100) {} // default max_Size of 100
  public Stack(int size) {
    max_Size = size;
    items = new T(max_Size);
  }
  
  public void Push(T item) {  // adds item to top of stack
    if (index >= max_Size)
      throw new StackOverflowException();
    items[index++] = item; // adds item to stack and then increments its actual size
  }
  
  public T Pop() {  // returns item at top of stack, and removes it from stack
    index--;
    if (index >= 0) {
      return items[index];
    } else {
      index = 0;
      throw new InvalidOperationExeption("Stack is empty, cannot Pop");
    }
  }
  
  public T Peek() { // returns item at top of stack, but leaves it there
    return items[index - 1];
  }
  
  public T Get(int idx) { // returns specified index of stack
    return items[idx];
  }
}

static void Main(string[] args) {
  Stack<string> StarWarsMovies = new Stack<string>(9); // creates a new stack of strings that can contain 9 elements
  
  StarWarsMovies.Push("The Phantom Menace");
  StarWarsMovies.Push("Attack of the Clones");
  StarWarsMovies.Push("Revenge of the Sith");
  StarWarsMovies.Push("Rogue One");
  StarWarsMovies.Push("A New Hope");
  StarWarsMovies.Push("The Empire Strikes Back");
  StarWarsMovies.Push("Return of the Jedi");
  StarWarsMovies.Push("The Force Awakens");
  StarWarsMovies.Push("The Last Jedi");
  
  StarWarsMovies.Peek();  // returns "The Last Jedi"
  StarWarsMovies.Pop();   // returns "The Last Jedi"
  StarWarsMovies.Peek();  // returns "The Force Awakens"
  StarWarsMovies.Get(3);  // returns "Rogue One"
}
```

Remember the Point struct from above?

```
struct Point {
  public int x;
  public int y;
  public Point(int x, int y) {
    this.x = x;
    this.y = y;
  }
}
```

Use a Generic struct instead:

```
public struct Point<T> {
  public T X;
  public T Y;
}

// ...
Point<int> intPoint;
intPoint.X = 1;
intPoint.Y = 2;

Point<double> dblPoint;
dblPoint.X = 1.2;
dblPoint.Y = 3.4;
```

Now it's not limited to just integers.

### Advantages of Generics (Methods & Classes)

* Safety: Type checking is done at compile time instead of run-time, allowing bugs to be caught before release.
* Maintainability: Much easier to maintain one version of the method/class than multiple overloads. This lets one set of code to be used for any data type, includong future types.
* Efficiency; The actual code for data typed versions of a generic is done on demand (ones that are actually called) instead of multiple typed versions that may not be used.

## Collections

As said above, there are collections of generic classes like Stack<>. Another common one is List<>, also under the System.Collections.Generic namespace.

|List<>.         | Function                       |
|---------------:|--------------------------------|
|.Add()          | Adds an item at the end of List|
|.Clear()        | Removes all items from List.   |
|.Contains()     | Checks for an item (bool).     |
|.Count()        | Returns number of items in List|
|.Insert()       | Adds an item at specified index|

```
List<string> colors = new List<string>();
colors.Add("Red");
colors.Add("Green");
colors.Add("Blue");

Console.WriteLine(colors.Count()); // outputs 3
foreach (var color in colors) {
  Console.WriteLine(color);
  /* outputs:
       Red
       Green
       Blue
  */
}
```

Other Collections:

* Dictionary<TKey, TValue> - key/value pairs
* Queue<> - FIFO (First In, First Out)
* Stack<> - LIFO (Last In, First Out)

## Files

__namespace__: using System.IO;

|File.           | Function                       |
|---------------:|--------------------------------|
|.WriteAllText() | Writes/Overwrites file with content|
|.ReadAllText()  | Reads a file into a string |
|.AppendAlltext()| Appends text to the end of a file |
|.Create()       | Creates a new file in a specified location |
|.Delete()       | Deletes a specified file |
|.Copy()         | Copies a file to a new location |
|.Move()         | Moves a file to a new location |
|.Exists()       | Checks to see if a file exists (bool) |

