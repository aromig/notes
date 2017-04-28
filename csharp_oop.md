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

## Overloading

Multiple methods that have the same name but different parameters (of different datatypes and/or number of parameters).

```
static void printVariable(string thing) {
  Console.WriteLine(thing);
}

static void printVariable(int thing) {
  Console.WriteLine(thing);
}
```

## Stack vs Heap (static vs dynamic memory allocation)

* Values -> Stack
* References (such as objects) -> Heap

## Encapsulation

Encapsulation is more than combining members (such as fields and methods) in a class. Its purpose is more about "protecting" members of a class and the ability to hide information from other parts of the program.

Fields & methods are accessible:
* pubilc - outside of the class
* private - only from within the class
* protected - from within the class __AND__ derived classes
* sealed - only from within the class __AND__ prevents inheritence

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

## Constructors

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

## Deconstructors

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

And we would like to add two Box objects to make a bigger Box. We're thinking it should work like ```Box b3 = b1 + 2;```

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

