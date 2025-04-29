# Creational Patterns
## Factory Method

### 1) **When do I use it?**  
Use the **Factory Method** pattern when you need to create objects with flexibility and want to avoid specifying their exact class in the code.

### 2) **What does it look like?**  
The **Factory Method** pattern typically involves a creation method that returns objects declared as an interface or abstract class, even though the actual instance is of a concrete type. This approach allows you to decouple the client code from specific implementations.

### 3) **Code example:**  
```cs
using System;
using System.Collections.Generic;

namespace DesignPatterns.Factory.Method
{
    public enum Color
    {
        Red, Blue, Green, Magenta, Purple
    }

    public enum VehicleType
    {
        Car, Motorbike
    }

    public abstract class Vehicle
    {
        protected Vehicle() { }

        public abstract VehicleType Type { get; }
        public int Wheels { get; set; }
        public Color Color { get; set; }

        public override string ToString() => $"Vehicle Type: {Type}\nWheels: {Wheels}\nColor: {Color}";
    }

    public class Car : Vehicle
    {
        public override VehicleType Type => VehicleType.Car;
    }

    public class Motorbike : Vehicle
    {
        public override VehicleType Type => VehicleType.Motorbike;
    }

    // Creator (abstract)
    public abstract class VehicleFactory
    {
        // Factory Method
        public abstract Vehicle CreateVehicle(Color color);
    }

    public class CarFactory : VehicleFactory
    {
        public override Vehicle CreateVehicle(Color color)
        {
            return new Car { Wheels = 4, Color = color };
        }
    }

    // Concrete Creator for Motorbike
    public class MotorbikeFactory : VehicleFactory
    {
        public override Vehicle CreateVehicle(Color color)
        {
            return new Motorbike { Wheels = 2, Color = color };
        }
    }

    class Program
    {
        static void Main(string[] args)
        {
            VehicleFactory factory;

            // Build a car.
            factory = new CarFactory();

            Console.WriteLine(factory.CreateVehicle(Color.Red).ToString());
            Console.WriteLine("-----------");

            // Build a motorbike.
            factory = new MotorbikeFactory();

            Console.WriteLine(factory.CreateVehicle(Color.Magenta).ToString());

            Console.ReadKey();
        }
    }
}
```

## Abstract Factory
### 1) **When do I use it?**  
Use the **Abstract Factory** pattern when you need to create families of related objects that are designed to work together (without specifying their concrete classes).  
It helps ensure consistency among products that belong to the same family and lets you easily switch between entire product sets.

### 2) **What does it look like?**  
The **Abstract Factory** pattern typically involves:

- An **abstract factory interface** that declares methods for creating each type of related product.
- One or more **concrete factories** that implement these methods to produce specific variants of the products.
- Each product type is usually represented by its own interface or abstract class.

### 3) **Code example:**  
```cs
using System;
using System.Collections.Generic;

namespace DesignPatterns.Factory.Abstract
{
    public enum Color
    {
        Red, Blue, Green, Magenta, Purple
    }

    public enum VehicleType
    {
        Car, Motorbike
    }

    public abstract class Vehicle
    {
        protected Vehicle() { }

        public abstract VehicleType Type { get; }
        public int Wheels { get; set; }
        public Color Color { get; set; }
        public string Description { get; set; } = string.Empty;

        public override string ToString() => $"Vehicle Type: {Type}\nWheels: {Wheels}\nColor: {Color}\n{Description}";
    }

    public class Car : Vehicle
    {
        public override VehicleType Type => VehicleType.Car;
    }

    public class Motorbike : Vehicle
    {
        public override VehicleType Type => VehicleType.Motorbike;
    }

    // Abstract Factory
    public interface IVehicleFactory
    {
        Vehicle CreateCar(Color color);
        Vehicle CreateMotorbike(Color color);
    }

    // Concrete Factory: Sport vehicles
    public class SportVehicleFactory : IVehicleFactory
    {
        public Vehicle CreateCar(Color color)
        {
            return new Car { Wheels = 4, Color = color, Description = "High-performance sport car with aerodynamic design." };
        }

        public Vehicle CreateMotorbike(Color color)
        {
            return new Motorbike { Wheels = 2, Color = color, Description = "Lightweight racing motorbike with turbo engine." };
        }
    }

    // Concrete Factory: Urban vehicles
    public class UrbanVehicleFactory : IVehicleFactory
    {
        public Vehicle CreateCar(Color color)
        {
            return new Car { Wheels = 4, Color = color, Description = "Compact urban car designed for fuel efficiency and city driving." };
        }

        public Vehicle CreateMotorbike(Color color)
        {
            return new Motorbike { Wheels = 2, Color = color, Description = "Electric motorbike ideal for commuting and short distances." };
        }
    }

    class Program
    {
        static void Main(string[] args)
        {
            IVehicleFactory factory;

            // Create a SportVehicle Factory.
            factory = new SportVehicleFactory();

            Console.WriteLine("--- Sport Vehicles ---\n");
            Console.WriteLine(factory.CreateCar(Color.Red));
            Console.WriteLine("-----------");
            Console.WriteLine(factory.CreateMotorbike(Color.Magenta));
            Console.WriteLine("\n===========\n");

            // Create an UrbanVehicle Factory.
            factory = new UrbanVehicleFactory();

            Console.WriteLine("--- Urban Vehicles ---\n");
            Console.WriteLine(factory.CreateCar(Color.Red));
            Console.WriteLine("-----------");
            Console.WriteLine(factory.CreateMotorbike(Color.Magenta));

            Console.ReadKey();
        }
    }
}
```

## Builder
### 1) **When do I use it?**  
Use the **Builder** pattern when you need to construct an object that requires many configuration options or complex setup.

### 2) **What does it look like?**  
The **Builder** pattern typically involves a class with one method to create the final object and multiple methods to configure its properties.  
These configuration methods usually support method chaining, like:  
`builder.SetValueA(0).SetValueB(true).Create()`.

### 3) **Code example:**  
```cs
using System;
using System.Collections.Generic;

namespace DesignPatterns.Builder
{
    public enum Color
    {
        Red, Blue, Green, Magenta, Purple
    }

    public enum VehicleType
    {
        Car, Motorbike
    }

    public class Vehicle
    {
        public VehicleType Type { get; set; }
        public int Wheels { get; set; }
        public Color Color { get; set; }

        public override string ToString() => $"Vehicle Type: {Type}\nWheels: {Wheels}\nColor: {Color}";
    }

    public interface IVehicleBuilder
    {
        IVehicleBuilder Reset();
        IVehicleBuilder SetWheels();
        IVehicleBuilder SetColor(Color color);
        Vehicle GetResult();
    }

    public class CarBuilder : IVehicleBuilder
    {
        private Vehicle _vehicle = new();

        public CarBuilder() => Reset();

        public IVehicleBuilder Reset()
        {
            _vehicle = new Vehicle { Type = VehicleType.Car };
            return this;
        }

        public IVehicleBuilder SetWheels()
        {
            _vehicle.Wheels = 4;
            return this;
        }

        public IVehicleBuilder SetColor(Color color)
        {
            _vehicle.Color = color;
            return this;
        }

        public Vehicle GetResult()
        {
            var result = _vehicle;
            Reset(); // optional
            return result;
        }
    }

    public class MotorbikeBuilder : IVehicleBuilder
    {
        private Vehicle _vehicle = new();

        public MotorbikeBuilder() => Reset();

        public IVehicleBuilder Reset()
        {
            _vehicle = new Vehicle { Type = VehicleType.Motorbike };
            return this;
        }

        public IVehicleBuilder SetWheels()
        {
            _vehicle.Wheels = 2;
            return this;
        }

        public IVehicleBuilder SetColor(Color color)
        {
            _vehicle.Color = color;
            return this;
        }

        public Vehicle GetResult()
        {
            var result = _vehicle;
            Reset(); // optional
            return result;
        }
    }

    public class Director
    {
        private IVehicleBuilder? _builder;

        public IVehicleBuilder Builder
        {
            set { _builder = value; }
        }

        public void BuildVehicle(Color color) => _builder?.SetWheels().SetColor(color);
    }

    class Program
    {
        static void Main(string[] args)
        {
            var director = new Director();

            // Build a car.
            var carBuilder = new CarBuilder();
            director.Builder = carBuilder;
            director.BuildVehicle(Color.Red);

            Console.WriteLine(carBuilder.GetResult().ToString());
            Console.WriteLine("-----------");

            // Build a motorbike.
            var motorbikeBuilder = new MotorbikeBuilder();
            director.Builder = motorbikeBuilder;
            director.BuildVehicle(Color.Magenta);

            Console.WriteLine(motorbikeBuilder.GetResult().ToString());
            Console.WriteLine("-----------");

            // The Builder pattern can be used without a Director class as well.
            Console.WriteLine("Custom car:");
            carBuilder.SetWheels();
            carBuilder.SetColor(Color.Purple);

            Console.WriteLine(carBuilder.GetResult().ToString());

            Console.ReadKey();
        }
    }
}
```

## Prototype
### 1) **When do I use it?**  
Use the **Prototype** pattern when you need to create duplicates of existing objects without depending on their specific classes. This is especially useful when object creation is costly or complex. In C#, this is commonly achieved using the *ICloneable* interface or *a custom Clone method*.

### 2) **What does it look like?**  
The **Prototype** pattern is typically recognized by the presence of *Clone*, *Copy*, or *similar methods used to duplicate objects*.

### 3) **Code example:**  
```cs
using System;
using System.Collections.Generic;

namespace DesignPatterns.Factory.Abstract
{
    public enum Color
    {
        Red, Blue, Green, Magenta, Purple
    }

    public enum VehicleType
    {
        Car, Motorbike
    }

    public class Brand
    {
        public string Name { get; set; }
        public string Country { get; set; }

        public Brand(string name, string country)
        {
            Name = name;
            Country = country;
        }

        public Brand Clone()
        {
            return new Brand(Name, Country);
        }

        public override string ToString() => $"{Name} ({Country})";
    }

    public abstract class Vehicle : ICloneable
    {
        public abstract VehicleType Type { get; }
        public int Wheels { get; set; }
        public Color Color { get; set; }
        public string Description { get; set; } = string.Empty;
        public Brand? Brand { get; set; }

        // Shallow Clone
        public virtual object Clone()
        {
            return MemberwiseClone();
        }

        // Deep Clone (to be overridden if needed)
        public abstract Vehicle DeepClone();

        public override string ToString() =>
            $"Type: {Type}\nWheels: {Wheels}\nColor: {Color}\nDescription: {Description}\nBrand: {Brand}";
    }

    public class Car : Vehicle
    {
        public override VehicleType Type => VehicleType.Car;

        public override Vehicle DeepClone()
        {
            return new Car
            {
                Wheels = this.Wheels,
                Color = this.Color,
                Description = this.Description,
                Brand = this.Brand?.Clone()
            };
        }
    }

    public class Motorbike : Vehicle
    {
        public override VehicleType Type => VehicleType.Motorbike;

        public override Vehicle DeepClone()
        {
            return new Motorbike
            {
                Wheels = this.Wheels,
                Color = this.Color,
                Description = this.Description,
                Brand = this.Brand?.Clone()
            };
        }
    }

    class Program
    {
        static void Main(string[] args)
        {
            var originalCar = new Car
            {
                Wheels = 4,
                Color = Color.Red,
                Description = "Sporty Coupe",
                Brand = new Brand("Volkswagen", "Germany")
            };

            var originalBike = new Motorbike
            {
                Wheels = 2,
                Color = Color.Green,
                Description = "Off-road Dirt Bike",
                Brand = new Brand("Yamaha", "Japan")
            };

            var shallowCar = (Car)originalCar.Clone();
            var deepBike = (Motorbike)originalBike.DeepClone();

            // Mutate original's brand
            originalCar.Brand.Name = "Ford";
            originalBike.Brand.Country = "USA";

            Console.WriteLine("--- Original Car ---");
            Console.WriteLine(originalCar);
            Console.WriteLine("\n--- Shallow Cloned Car ---");
            Console.WriteLine(shallowCar); // Same brand reference

            Console.WriteLine("\n--- Original Bike ---");
            Console.WriteLine(originalBike);
            Console.WriteLine("\n--- Deep Cloned Bike ---");
            Console.WriteLine(deepBike); // Independent brand

            Console.ReadKey();
        }
    }
}
```

## Singleton
### 1) **When do I use it?**  
Use the **Singleton** pattern **only** when:

- You truly need a **single shared instance** across the application.
- The class is **stateless** or managing a global resource (e.g., a logger, configuration manager).
- It's managed through **Dependency Injection** (e.g., using `AddSingleton()` in ASP.NET Core).

Reasons to **avoid** using it (anti-pattern):

- Breaks dependency injection (enforcing tight coupling).
- Can lead to concurrency issues.
- Makes unit testing hard to implement.

### 2) **What does it look like?**  
A **Singleton** class ensures only one instance is created and provides global access to it. It's very often used in systems based on some legacy code. In such cases, Adapters make legacy code work with modern classes.

### 3) **Code example:**  
```cs
using System;
using System.Threading;

namespace DesignPatterns.Singleton
{
    // This Singleton implementation is called "double check lock" (lazy initialization).
    public class Singleton
    {
        private static Singleton? _instance;

        // Will be used to synchronize threads during first access to the Singleton.
        private static readonly object _lock = new object();

        public static Singleton GetInstance(string value)
        {
            if (_instance == null)
            {
                // Thread locks here, checks whether the instance is initialized already:
                // no: it initializes the singleton now.
                // yes: it's already initialized no need to create a new instance.
                lock (_lock)
                {
                    if (_instance == null)
                    {
                        _instance = new Singleton();
                        _instance.Value = value;
                    }
                }
            }
            return _instance;
        }

        // Just to test it works.
        public string Value { get; set; } = string.Empty;
    }

    class Program
    {
        static void Main(string[] args)
        {
            Console.WriteLine($"If the output is the same values, then the singleton was reused. \nResult:\n");

            Thread process1 = new(() => TestSingleton("Instance1"));
            Thread process2 = new(() => TestSingleton("Instance2"));

            process1.Start();
            process2.Start();

            process1.Join();
            process2.Join();

            Console.ReadKey();
        }

        public static void TestSingleton(string value)
        {
            Singleton singleton = Singleton.GetInstance(value);
            Console.WriteLine(singleton.Value);
        }
    }
}
```

# Structural Patterns
## Adapter

### 1) **When do I use it?**  
Use the **Adapter** pattern when you have two incompatible interfaces that need to work together. It's especially useful when integrating legacy code or third-party libraries into your current system.

### 2) **What does it look like?**  
The **Adapter** pattern typically involves a class that wraps an existing object and implements a target interface. The adapter receives calls through the target interface, translates them as needed, and delegates the work to the wrapped (adaptee) object. 
This often involves converting data formats or method calls.

### 3) **Code example:**  
```cs
using System;
using System.Collections.Generic;

namespace DesignPatterns.Adapter
{
   public interface IPaymentProcessor
    {
        bool ProcessPayment(decimal amount, string currency);
    }


    public class StripeApi
    {
        public bool MakePayment(string amountWithCurrency)
        {
            Console.WriteLine($"Stripe processing: {amountWithCurrency}");
            return true;
        }
    }

    public class StripePaymentAdapter : IPaymentProcessor
    {
        private readonly StripeApi _stripeApi;

        public StripePaymentAdapter(StripeApi stripeApi)
        {
            _stripeApi = stripeApi;
        }

        public bool ProcessPayment(decimal amount, string currency)
        {
            string formattedAmount = $"{amount:F2} {currency.ToUpper()}";
            return _stripeApi.MakePayment(formattedAmount);
        }
    }

    class Program
    {
        static void Main(string[] args)
        {
            StripeApi stripe = new StripeApi();
            IPaymentProcessor processor = new StripePaymentAdapter(stripe);

            processor.ProcessPayment(29.99m, "euro");

            Console.ReadKey();
        }
    }
}
```

## Bridge

### 1) **When do I use it?**  
Use the **Bridge** pattern when you want to separate an abstraction from its implementation so that both can evolve independently.
It's especially useful when dealing with multiple variations of something, like having a high-level abstraction for data access that can support *different database servers or platforms underneath*.

### 2) **What does it look like?**  
At a high level, it involves:

- Abstraction: Defines the interface and holds a reference to the implementation.
- Refined Abstraction: A subclass of the abstraction that adds more behavior.
- Implementor: Defines the interface for implementation classes.
- ConcreteImplementor: Actual implementations of the interface.

### 3) **Code example:**  
```cs
using System;
using System.Collections.Generic;

namespace DesignPatterns.Bridge
{

    // Implementor
    public interface IDatabaseDriver
    {
        void Connect();
    }

    // ConcreteImplementors
    public class SqlServerDriver : IDatabaseDriver
    {
        public void Connect()
        {
            Console.WriteLine("Connecting to SQL Server...");
        }
    }

    public class MySqlDriver : IDatabaseDriver
    {
        public void Connect()
        {
            Console.WriteLine("Connecting to MySQL...");
        }
    }

    // Abstraction
    public abstract class DataAccess
    {
        protected IDatabaseDriver _driver;

        protected DataAccess(IDatabaseDriver driver)
        {
            _driver = driver;
        }

        public abstract void OpenConnection();
    }

    // Refined Abstraction
    public class CustomerDataAccess : DataAccess
    {
        public CustomerDataAccess(IDatabaseDriver driver) : base(driver) { }

        public override void OpenConnection()
        {
            Console.WriteLine("Opening connection for Customer data...");
            _driver.Connect();
        }
    }

    class Program
    {
        static void Main(string[] args)
        {
            var sqlAccess = new CustomerDataAccess(new SqlServerDriver());
            sqlAccess.OpenConnection();

            Console.WriteLine("------------------------------");

            var mySqlAccess = new CustomerDataAccess(new MySqlDriver());
            mySqlAccess.OpenConnection();

            Console.ReadKey();
        }
    }
}
```

## Composite

### 1) **When do I use it?**  
Use the **Composite** pattern when you need to work with hierarchical tree structures where individual objects and groups of objects should be treated uniformly.

It is ideal for scenarios where:

- You want to treat leaves and composites (containers) the same way.
- You need to recursively perform operations across a structure (like rendering UI elements, calculating size, or applying transformations).
- You want to build and manipulate complex structures while keeping client code simple.

### 2) **What does it look like?**  
The **Composite** pattern typically involves:

- A component interface that defines the common operations for both simple and complex objects.
- Leaf classes that implement the component interface and represent end objects.
- Composite classes that also implement the interface and manage child components (which can be leaves or other composites).

### 3) **Code example:**  
```cs
using System;
using System.Collections.Generic;

namespace DesignPatterns.Composite
{
    // Component
    abstract class UIElement
    {
        public string Name { get; }

        protected UIElement(string name)
        {
            Name = name;
        }

        public abstract void Render(int depth = 0);

        public virtual void Add(UIElement child)
        {
            throw new NotImplementedException();
        }

        public virtual void Remove(UIElement child)
        {
            throw new NotImplementedException();
        }
    }

    // Leaf - Button
    class Button : UIElement
    {
        public Button(string name) : base(name) { }

        public override void Render(int depth = 0)
        {
            Console.WriteLine($"{new string(' ', depth)}Button: {Name}");
        }
    }

    // Leaf - Label
    class Label : UIElement
    {
        public Label(string name) : base(name) { }

        public override void Render(int depth = 0)
        {
            Console.WriteLine($"{new string(' ', depth)}Label: {Name}");
        }
    }

    // Composite - Panel (can contain other UI elements)
    class Panel : UIElement
    {
        private readonly List<UIElement> _children = new();

        public Panel(string name) : base(name) { }

        public override void Add(UIElement child) { 
            _children.Add(child);
        }

        public override void Remove(UIElement child) { 
            _children.Remove(child); 
        }

        public override void Render(int depth = 0)
        {
            Console.WriteLine($"{new string(' ', depth)}Panel: {Name}");
            foreach (var child in _children)
            {
                child.Render(depth + 2);
            }
        }
    }

    class Program
    {
        static void Main(string[] args)
        {
            // Create individual UI elements
            var button1 = new Button("Submit");
            var button2 = new Button("Cancel");
            var label = new Label("Welcome!");

            // Create containers
            var mainPanel = new Panel("MainPanel");
            var footerPanel = new Panel("Footer");

            // Compose the UI tree
            mainPanel.Add(label);
            mainPanel.Add(button1);
            footerPanel.Add(button2);
            mainPanel.Add(footerPanel);

            // Render the UI
            Console.WriteLine("Rendering UI:");
            mainPanel.Render();

            Console.ReadKey();
        }
    }
}
```

## Decorator

### 1) **When do I use it?**  
Use the **Decorator** pattern when you want to add responsibilities or behavior to individual objects dynamically, without modifying their code.

It is ideal for scenarios where:

- You need to extend functionality at runtime without affecting other instances.
- You want to follow the Single Responsibility Principle — functionality can be divided between classes with unique concerns.
- You need to avoid bloated class hierarchies that come from subclassing everything for every possible feature combination.

### 2) **What does it look like?**  
The **Decorator** pattern typically involves:

- A component interface that defines the base behavior.
- A concrete component that implements the base behavior.
- An abstract decorator class that implements the interface and wraps a component.
- Concrete decorators that extend the behavior of the component by adding their own logic before or after delegating to the wrapped object.

### 3) **Code example:**  
```cs
using System;
using System.Collections.Generic;

namespace DesignPatterns.Composite
{
    // Component
    public interface ITextBox
    {
        void Render();
    }

    // Concrete Component
    public class BasicTextBox : ITextBox
    {
        public void Render()
        {
            Console.WriteLine("Rendering basic TextBox");
        }
    }

    // Base Decorator
    public abstract class TextBoxDecorator : ITextBox
    {
        protected ITextBox _innerTextBox;

        public TextBoxDecorator(ITextBox textBox)
        {
            _innerTextBox = textBox;
        }

        public virtual void Render()
        {
            _innerTextBox.Render();
        }
    }

    // Concrete Decorator - adds scrollbars
    public class ScrollDecorator : TextBoxDecorator
    {
        public ScrollDecorator(ITextBox textBox) : base(textBox) { }

        public override void Render()
        {
            _innerTextBox.Render();
            Console.WriteLine(" → Adding scrollbars");
        }
    }

    // Concrete Decorator - adds border
    public class BorderDecorator : TextBoxDecorator
    {
        public BorderDecorator(ITextBox textBox) : base(textBox) { }

        public override void Render()
        {
            Console.WriteLine(" → Drawing border");
            _innerTextBox.Render();
        }
    }

    class Program
    {
        static void Main(string[] args)
        {
            ITextBox simpleBox = new BasicTextBox();
            ITextBox borderedBox = new BorderDecorator(simpleBox);
            ITextBox scrollableBorderedBox = new ScrollDecorator(borderedBox);

            Console.WriteLine("Rendering decorated TextBox:");
            scrollableBorderedBox.Render();

            Console.ReadKey();
        }
    }
}
```

## Facade

### 1) **When do I use it?**  
Use the **Facade** pattern when you want to provide a simple interface to a complex subsystem.

It's great for situations where:

- You want to hide complexity from the client code.
- You are working with multiple components or classes that have a lot of setup or interdependencies.
- You want to decouple client code from subsystem details to improve *maintainability* and *flexibility*.

### 2) **What does it look like?**  
The **Facade** pattern typically involves:

- A Facade class that exposes simplified methods to perform high-level tasks.
- A set of subsystems or classes that do the real work behind the scenes.
- The client interacts with the facade, not the individual subsystems.

### 3) **Code example:**  
```cs
using System;
using System.Collections.Generic;

namespace DesignPatterns.Facade
{
    // Subsystem classes
    public class DVDPlayer
    {
        public void On() => Console.WriteLine("DVD Player is ON");
        public void Play(string movie) => Console.WriteLine($"Playing movie: {movie}");
    }

    public class Projector
    {
        public void On() => Console.WriteLine("Projector is ON");
        public void SetInput(string source) => Console.WriteLine($"Projector input set to: {source}");
    }

    public class SurroundSound
    {
        public void On() => Console.WriteLine("Surround Sound is ON");
        public void SetVolume(int level) => Console.WriteLine($"Volume set to: {level}");
    }

    // Facade (basically a class that organizes complexity)
    public class HomeTheaterFacade
    {
        private DVDPlayer _dvd;
        private Projector _projector;
        private SurroundSound _sound;

        public HomeTheaterFacade(DVDPlayer dvd, Projector projector, SurroundSound sound)
        {
            _dvd = dvd;
            _projector = projector;
            _sound = sound;
        }

        public void WatchMovie(string movie)
        {
            Console.WriteLine("[Starting movie night!]");
            _dvd.On();
            _projector.On();
            _projector.SetInput("DVD");
            _sound.On();
            _sound.SetVolume(10);
            _dvd.Play(movie);
        }
    }

    class Program
    {
        static void Main(string[] args)
        {
            var dvd = new DVDPlayer();
            var projector = new Projector();
            var sound = new SurroundSound();

            var homeTheater = new HomeTheaterFacade(dvd, projector, sound);
            homeTheater.WatchMovie("Inception");

            Console.ReadKey();
        }
    }
}
```

## Flyweight

### 1) **When do I use it?**  
Use the **Flyweight** pattern when you need to efficiently support a large number of fine-grained objects that share common state.

It is ideal for scenarios where:

- You want to minimize memory usage by sharing as much data as possible.
- You have many objects with identical or similar data that can be extrinsically passed instead of stored.
- Performance and resource usage are critical, such as in rendering large numbers of UI elements, game objects, or characters.

### 2) **What does it look like?**  
The **Flyweight** pattern typically involves:

- A shared *Flyweight* object that contains intrinsic (shared) state.
- Extrinsic state is passed in externally during method calls and is not stored in the *Flyweight*.
- A *FlyweightFactory* that ensures shared instances are reused instead of recreated.
- Clients that use the *Flyweight* objects by supplying external context as needed.

### 3) **Code example:**  
```cs
using System;
using System.Collections.Generic;

namespace DesignPatterns.Flyweight
{
    // The Flyweight
    public interface ITree
    {
        void Display(int x, int y);
    }

    // The Concrete Flyweight
    public class TreeType : ITree
    {
        private string name;
        private string color;
        private string texture;

        public TreeType(string name, string color, string texture)
        {
            this.name = name;
            this.color = color;
            this.texture = texture;
        }

        public void Display(int x, int y)
        {
            Console.WriteLine($"Drawing tree '{name}' with color {color} and texture {texture} at ({x}, {y})");
        }
    }

    // The Flyweight Factory
    public class TreeFactory
    {
        private Dictionary<string, TreeType> _treeTypes = new();

        public TreeType GetTreeType(string name, string color, string texture)
        {
            string key = $"{name}_{color}_{texture}";

            if (!_treeTypes.ContainsKey(key))
            {
                _treeTypes[key] = new TreeType(name, color, texture);
            }

            return _treeTypes[key];
        }
    }

    // Client
    public class Forest
    {
        private List<(int x, int y, ITree tree)> _trees = new();
        private TreeFactory _factory = new();

        public void PlantTree(int x, int y, string name, string color, string texture)
        {
            var type = _factory.GetTreeType(name, color, texture);
            _trees.Add((x, y, type));
        }

        public void Display()
        {
            foreach (var (x, y, tree) in _trees)
            {
                tree.Display(x, y);
            }
        }
    }


    class Program
    {
        static void Main(string[] args)
        {
            Forest forest = new Forest();

            // Plant a large number of trees using shared TreeTypes
            forest.PlantTree(1, 1, "Oak", "Green", "Rough");
            forest.PlantTree(2, 3, "Oak", "Green", "Rough");
            forest.PlantTree(4, 5, "Pine", "Dark Green", "Smooth");
            forest.PlantTree(5, 1, "Pine", "Dark Green", "Smooth");
            forest.PlantTree(6, 2, "Birch", "Light Green", "Striped");

            // Display all trees
            forest.Display();

            Console.ReadKey();
        }
    }
}
```

## Proxy

### 1) **When do I use it?**  
Use the **Proxy** pattern when you want to control access to another object or resource.

It is ideal for scenarios where:

- You want to add access control (e.g., authentication, permission checks).
- You want to delay or manage the creation of an expensive object (lazy initialization or caching).
- You want to log, monitor, or add additional logic before delegating to the real object.
- You want to provide a simplified or restricted interface to a complex or sensitive subsystem.

### 2) **What does it look like?**  
The **Proxy** pattern typically involves:

- A subject interface that both the real object and the proxy implement.
- A real object that performs the actual operation.
- A proxy class that controls access to the real object by implementing the same interface and optionally adding logic like access control, logging, or lazy loading.

### 3) **Code example:**  
```cs
using System;
using System.Collections.Generic;

namespace DesignPatterns.Proxy
{

    // Subject interface
    public interface IFileAccessor
    {
        void ReadFile(string fileName);
    }

    // Real Subject
    public class RealFileAccessor : IFileAccessor
    {
        public void ReadFile(string fileName)
        {
            Console.WriteLine($"[ACCESS GRANTED] Reading file: {fileName}");
        }
    }

    // Proxy
    public class FileAccessorProxy : IFileAccessor
    {
        private readonly RealFileAccessor _realFileAccessor = new();
        private readonly string _userRole;

        public FileAccessorProxy(string userRole)
        {
            _userRole = userRole;
        }

        public void ReadFile(string fileName)
        {
            if (_userRole == "Admin")
                _realFileAccessor.ReadFile(fileName);
            else
                Console.WriteLine($"[ACCESS DENIED] User with role '{_userRole}' is not authorized to read the file.");
        }
    }

    class Program
    {
        static void Main(string[] args)
        {
            IFileAccessor adminAccess = new FileAccessorProxy("Admin");
            IFileAccessor guestAccess = new FileAccessorProxy("Guest");

            adminAccess.ReadFile("report.txt");   // Access granted
            guestAccess.ReadFile("report.txt");   // Access denied

            Console.ReadKey();
        }
    }
}
```

# Behavioral Patterns
## Chain of Responsibility

### 1) **When do I use it?**  
Use the **Chain of Responsibility** pattern when you want to pass a request along a chain of potential handlers until one of them handles it.  
It's ideal for scenarios where you don't know in advance which object should handle a request, or you want to decouple senders and receivers.

Common use cases:

- Event handling systems
- Logging frameworks
- Validation or authentication pipelines

### 2) **What does it look like?**  
The **Chain of Responsibility** pattern involves a base handler interface or abstract class with a method to handle requests and a reference to the next handler.
Each concrete handler decides either to handle the request or pass it along the chain.

### 3) **Code example:**  
```cs
using System;
using System.Collections.Generic;

namespace DesignPatterns.ChainOfResponsibility
{

    // Step 1: Define the handler interface or abstract class
    public abstract class Handler
    {
        protected Handler _next;

        public void SetNext(Handler next)
        {
            _next = next;
        }

        public abstract void Handle(string request);
    }

    // Step 2: Concrete Handlers
    public class AuthHandler : Handler
    {
        public override void Handle(string request)
        {
            if (request == "auth")
                Console.WriteLine("AuthHandler: Handling authentication.");
            else
                _next?.Handle(request);
        }
    }

    public class LogHandler : Handler
    {
        public override void Handle(string request)
        {
            if (request == "log")
                Console.WriteLine("LogHandler: Handling logging.");
            else
                _next?.Handle(request);
        }
    }

    public class DataHandler : Handler
    {
        public override void Handle(string request)
        {
            if (request == "data")
                Console.WriteLine("DataHandler: Handling data processing.");
            else
                _next?.Handle(request);
        }
    }

    class Program
    {
        static void Main()
        {
            // Chain: Auth -> Log -> Data
            var auth = new AuthHandler();
            var log = new LogHandler();
            var data = new DataHandler();

            auth.SetNext(log);
            log.SetNext(data);

            // Send requests
            auth.Handle("auth");   // Handled by AuthHandler
            auth.Handle("log");    // Passed to LogHandler
            auth.Handle("data");   // Passed down to DataHandler
            auth.Handle("unknown"); // Not handled

            Console.ReadKey();
        }
    }
}
```

## Command

### 1) **When do I use it?**  
Use the **Command** pattern when you need to encapsulate requests as objects, allowing you to parameterize methods with different requests, queue or log operations, and support undoable actions.

It's especially useful when:

- You want to decouple the object that sends a request from the one that handles it.
- You want to implement features like undo/redo, macros, or job queues.

### 2) **What does it look like?**  
The **Command** pattern typically involves:

- A Command interface with an Execute() method.
- Concrete command classes that implement the interface and call the appropriate method(s) on a receiver object.
- An Invoker that stores and triggers commands.
- A Receiver that knows how to perform the operations.

### 3) **Code example:**  
```cs
using System;
using System.Collections.Generic;

namespace DesignPatterns.Command
{

    // Step 1: The Command interface
    public interface ICommand
    {
        void Execute();
    }

    // Step 2: The Receiver class
    public class Light
    {
        public void TurnOn() => Console.WriteLine("Light is ON");
        public void TurnOff() => Console.WriteLine("Light is OFF");
    }

    // Step 3: Concrete Commands
    public class TurnOnCommand : ICommand
    {
        private Light _light;

        public TurnOnCommand(Light light)
        {
            _light = light;
        }

        public void Execute()
        {
            _light.TurnOn();
        }
    }

    public class TurnOffCommand : ICommand
    {
        private Light _light;

        public TurnOffCommand(Light light)
        {
            _light = light;
        }

        public void Execute()
        {
            _light.TurnOff();
        }
    }

    // Step 4: The Invoker class
    public class RemoteControl
    {
        private ICommand _command;

        public void SetCommand(ICommand command)
        {
            _command = command;
        }

        public void PressButton()
        {
            _command.Execute();
        }
    }

    class Program
    {
        static void Main()
        {
            var light = new Light();
            var turnOn = new TurnOnCommand(light);
            var turnOff = new TurnOffCommand(light);

            var remote = new RemoteControl();

            remote.SetCommand(turnOn);
            remote.PressButton(); // Light is ON

            remote.SetCommand(turnOff);
            remote.PressButton(); // Light is OFF

            Console.ReadKey();
        }
    }
}
```

## Iterator

### 1) **When do I use it?**  
Use the **Iterator** pattern when you want to provide a standard way to sequentially access elements of a collection without exposing its internal structure.

It's helpful when:

- You need multiple ways to traverse a collection (e.g., forward, backward).
- You want to decouple iteration logic from the collection's logic.
- You want a unified way to iterate over different kinds of collections (arrays, lists, trees, etc.). (e.g. XmlReader)

### 2) **What does it look like?**  
The **Iterator** pattern typically involves:

- An Iterator interface defining methods like HasNext() and Next().
- Concrete iterators implementing this interface.
- A Collection (or Aggregate) interface with a method to create the iterator.
- Concrete collections that implement the interface and return a corresponding iterator.

### 3) **Code example:**  
```cs
using System;
using System.Collections.Generic;

namespace DesignPatterns.Iterator
{
    // Step 1: Iterator interface
    public interface IIterator<T>
    {
        bool HasNext();
        T Next();
    }

    // Step 2: Aggregate interface
    public interface IAggregate<T>
    {
        IIterator<T> CreateIterator();
    }

    // Step 3: Concrete collection
    public class NameRepository : IAggregate<string>
    {
        private List<string> _names = new List<string> { "Alice", "Bob", "Charlie" };

        public IIterator<string> CreateIterator()
        {
            return new NameIterator(_names);
        }
    }

    // Step 4: Concrete iterator
    public class NameIterator : IIterator<string>
    {
        private readonly List<string> _names;
        private int _index = 0;

        public NameIterator(List<string> names)
        {
            _names = names;
        }

        public bool HasNext()
        {
            return _index < _names.Count;
        }

        public string Next()
        {
            return _names[_index++];
        }
    }

    class Program
    {
        static void Main()
        {
            var repository = new NameRepository();
            var iterator = repository.CreateIterator();

            while (iterator.HasNext())
            {
                Console.WriteLine(iterator.Next());
            }

            Console.ReadKey();
        }
    }
}
```

## Mediator

### 1) **When do I use it?**  
Use the **Mediator** pattern when you have a set of objects that communicate in complex, chaotic ways, and you want to centralize their communication to reduce direct dependencies between them.

It's useful when:

- You want to simplify many-to-many relationships between objects.
- You want to avoid tightly coupling classes together.
- You want to control or coordinate complex interactions in one place.

### 2) **What does it look like?**  
The **Mediator** pattern typically involves:

- A Mediator interface that defines how components communicate. 
- Concrete Mediator classes that handle communication logic.
- Components (colleagues) that send messages through the mediator instead of communicating directly with each other.

### 3) **Code example:**  
```cs
using System;
using System.Collections.Generic;

namespace DesignPatterns.Mediator
{
    // Step 1: Mediator interface
    interface IMediator
    {
        void Notify(object sender, string ev);
    }

    // Step 2: Base component
    abstract class BaseComponent
    {
        protected IMediator? _mediator;

        public void SetMediator(IMediator mediator)
        {
            _mediator = mediator;
        }
    }

    // Step 3: Concrete components
    class Button : BaseComponent
    {
        public void Click()
        {
            Console.WriteLine("Button clicked.");
            _mediator?.Notify(this, "Click");
        }
    }

    class TextBox : BaseComponent
    {
        public void ShowMessage(string message)
        {
            Console.WriteLine($"TextBox displays: {message}");
        }
    }

    // Step 4: Concrete Mediator
    class DialogMediator : IMediator
    {
        private Button _button;
        private TextBox _textBox;

        public DialogMediator(Button button, TextBox textBox)
        {
            _button = button;
            _textBox = textBox;

            _button.SetMediator(this);
            _textBox.SetMediator(this);
        }

        public void Notify(object sender, string ev)
        {
            if (sender == _button && ev == "Click")
            {
                _textBox.ShowMessage("Button was clicked!");
            }
        }
    }

    // Step 5: Client code
    class Program
    {
        static void Main()
        {
            var button = new Button();
            var textBox = new TextBox();

            _ = new DialogMediator(button, textBox);

            button.Click(); // Will trigger the TextBox to display a message
        }
    }

}
```

## Memento

### 1) **When do I use it?**  
Use the **Memento** pattern when you need to capture and restore an object's state without exposing its internal details.

It's ideal when:

- You want to provide undo or rollback functionality.
- You need to save checkpoints in an object's history.
- You want to keep objects encapsulated but still support history management.

### 2) **What does it look like?**  
The **Memento** pattern involves:

- A *Memento* class that stores the internal state of the originator object.
- An *Originator* class that creates and uses the memento.
- A *Caretaker* class that keeps track of mementos but never touches their contents.

### 3) **Code example:**  
```cs
using System;
using System.Collections.Generic;

namespace DesignPatterns.Mediator
{
    // Memento - stores the state
    class Memento
    {
        public string State { get; }

        public Memento(string state)
        {
            State = state;
        }
    }

    // Originator - Editor that saves/restores its state
    class Editor
    {
        private string _text = "";

        public void Type(string words)
        {
            _text += words;
        }

        public string GetContent()
        {
            return _text;
        }

        public Memento Save()
        {
            return new Memento(_text);
        }

        public void Restore(Memento memento)
        {
            _text = memento.State;
        }
    }

    // Caretaker - manages undo/redo
    class EditorHistory
    {
        private Stack<Memento> _undoStack = new();
        private Stack<Memento> _redoStack = new();
        private Editor _editor;

        public EditorHistory(Editor editor)
        {
            _editor = editor;
        }

        public void Backup()
        {
            _undoStack.Push(_editor.Save());
            _redoStack.Clear();
        }

        public void Undo()
        {
            if (_undoStack.Count > 1)
            {
                _redoStack.Push(_undoStack.Pop()); // Move current to redo
                var memento = _undoStack.Peek(); // Peek the previous
                _editor.Restore(memento);
            }
            else
            {
                Console.WriteLine("Nothing to undo.");
            }
        }

        public void Redo()
        {
            if (_redoStack.Count > 0)
            {
                var memento = _redoStack.Pop();
                _undoStack.Push(memento);
                _editor.Restore(memento);
            }
            else
            {
                Console.WriteLine("Nothing to redo.");
            }
        }
    }

    // Client
    class Program
    {
        static void Main()
        {
            var editor = new Editor();
            var history = new EditorHistory(editor);

            editor.Type("Hello, ");
            history.Backup(); // Save BEFORE typing new

            editor.Type("World!");
            history.Backup(); // Save BEFORE typing new

            Console.WriteLine("Current Content: " + editor.GetContent()); // Hello, World!

            history.Undo();
            Console.WriteLine("After Undo: " + editor.GetContent()); // Hello, 

            history.Undo();
            Console.WriteLine("After Undo again: " + editor.GetContent()); // (Empty)

            history.Redo();
            Console.WriteLine("After Redo: " + editor.GetContent()); // Hello,

            Console.ReadKey();
        }
    }
}
```

## Observer

### 1) **When do I use it?**  
Use the **Observer** pattern when you want one object (the Subject) to automatically notify multiple other objects (Observers) when its state changes.

It's ideal when:

- You want to build a publish/subscribe system.
- Many parts of your system should react to state changes in another part.
- You want to decouple objects — so the subject doesn't need to know the specifics of the observers.

### 2) **What does it look like?**  
The **Observer** pattern typically involves:

- A Subject that maintains a list of observers and notifies them of any changes.
- Observer interfaces that define how observers respond to updates.
- Concrete Observer classes that subscribe to the subject.

### 3) **Code example:**  

## State

### 1) **When do I use it?**  
Use the **State** pattern when an object should change its behavior when its internal state changes.

This pattern is useful when:

- You want to avoid large conditionals (like if/else or switch statements).
- An object's behavior depends on its state, and it should transition between these states seamlessly.
- You want to encapsulate state-specific behavior into separate classes, rather than cluttering a class with many conditional statements.

### 2) **What does it look like?**  
The **State** pattern typically involves:

- Dividing behavior based on an object's state and encapsulating it in separate state objects.
- The object (Context) delegating specific actions to a state object, which handles the action.
- The state object changing the context's state when necessary, leading to different behavior.

### 3) **Code example:**  
```cs
using System;

namespace DesignPatterns.State
{
    using System;

    // Step 1: State Interface
    interface ICharacterState
    {
        void HandleAction(GameCharacter character);
    }

    // Step 2: Concrete States

    class IdleState : ICharacterState
    {
        public void HandleAction(GameCharacter character)
        {
            Console.WriteLine("Character is idle.");
            // Transition to Running or Jumping state
            character.State = new RunningState(); // For example, automatically transition to running
        }
    }

    class RunningState : ICharacterState
    {
        public void HandleAction(GameCharacter character)
        {
            Console.WriteLine("Character is running!");
            // Transition to Jumping or Attacking state
            character.State = new JumpingState(); // For example, character decides to jump
        }
    }

    class JumpingState : ICharacterState
    {
        public void HandleAction(GameCharacter character)
        {
            Console.WriteLine("Character is jumping!");
            // After jumping, character can either attack or go idle
            character.State = new AttackingState(); // For example, character starts attacking mid-jump
        }
    }

    class AttackingState : ICharacterState
    {
        public void HandleAction(GameCharacter character)
        {
            Console.WriteLine("Character is attacking!");
            // After attacking, character can return to idle
            character.State = new IdleState(); // After the attack, character goes back to idle
        }
    }

    // Step 3: Context - Game Character
    class GameCharacter
    {
        public ICharacterState State { get; set; }

        public GameCharacter()
        {
            // Initial state is Idle
            State = new IdleState();
        }

        public void PerformAction()
        {
            State.HandleAction(this); // Delegate the action to the current state
        }
    }

    // Step 4: Client
    class Program
    {
        static void Main()
        {
            var character = new GameCharacter();

            // Simulate character actions and state transitions
            character.PerformAction(); // Idle -> Running
            character.PerformAction(); // Running -> Jumping
            character.PerformAction(); // Jumping -> Attacking
            character.PerformAction(); // Attacking -> Idle
            character.PerformAction(); // Idle -> Running (loop starts over)

            Console.ReadKey();
        }
    }
}
```

## Strategy

### 1) **When do I use it?**  
Use the **Strategy** pattern when you need to:

- Define a family of algorithms (or behaviors) and make them interchangeable.
- Allow the behavior to be selected at runtime.
- Avoid multiple conditionals and make the system more flexible by allowing the behavior to vary independently from the context.

### 2) **What does it look like?**  
- The Context (e.g., GameCharacter) will hold a reference to a Strategy object.
- The Strategy is an interface (or abstract class) with different concrete implementations.
- The Context delegates the task to the current Strategy.

### 3) **Code example:**  
```cs
using System;

namespace DesignPatterns.Strategy
{
    // Step 1: Define the Strategy interface
    interface IAttackStrategy
    {
        void Attack();
    }

    // Step 2: Concrete Strategies (Different attack behaviors)

    class MeleeAttackStrategy : IAttackStrategy
    {
        public void Attack()
        {
            Console.WriteLine("Character performs a melee attack with a sword!");
        }
    }

    class RangedAttackStrategy : IAttackStrategy
    {
        public void Attack()
        {
            Console.WriteLine("Character shoots an arrow from a bow!");
        }
    }

    class MagicAttackStrategy : IAttackStrategy
    {
        public void Attack()
        {
            Console.WriteLine("Character casts a fireball!");
        }
    }

    // Step 3: Context - Game Character
    class GameCharacter
    {
        private IAttackStrategy _attackStrategy;

        public GameCharacter(IAttackStrategy attackStrategy)
        {
            _attackStrategy = attackStrategy;
        }

        // Set a different strategy at runtime
        public void SetAttackStrategy(IAttackStrategy attackStrategy)
        {
            _attackStrategy = attackStrategy;
        }

        // Delegate the attack behavior to the current strategy
        public void PerformAttack()
        {
            _attackStrategy.Attack();
        }
    }

    // Step 4: Client
    class Program
    {
        static void Main()
        {
            // Create a new character with an initial attack strategy (Melee Attack)
            var character = new GameCharacter(new MeleeAttackStrategy());

            // Perform the current attack
            character.PerformAttack(); // Melee attack

            // Switch to a ranged attack
            character.SetAttackStrategy(new RangedAttackStrategy());
            character.PerformAttack(); // Ranged attack

            // Switch to a magic attack
            character.SetAttackStrategy(new MagicAttackStrategy());
            character.PerformAttack(); // Magic attack

            Console.ReadKey();
        }
    }
}
```

## Template Method

### 1) **When do I use it?**  
Use the **Template Method** pattern when you have a series of steps in an algorithm, but you want to allow subclasses to define specific steps of the algorithm, while keeping the overall structure intact.

This pattern is useful when:

- You have an algorithm with a fixed structure, but some steps need to vary depending on the subclass.
- You want to avoid code duplication for common steps, while providing flexibility for subclasses to implement unique behavior.
- You want to define a skeleton for an algorithm, where subclasses can override specific steps without changing the overall algorithm.

### 2) **What does it look like?**  
The **Template Method** pattern typically involves:

- A base class defining a template method (e.g., PrepareRecipe), which outlines the high-level steps.
- Some of these steps are abstract and are implemented by subclasses (e.g., Brew and AddCondiments).
- The common steps (like boiling water, pouring into a cup) are implemented in the base class.

### 3) **Code example:**  
```cs
using System;

namespace DesignPatterns.TemplateMethod
{
    // Step 1: Define an Abstract Class with the Template Method
    abstract class Beverage
    {
        // Template method defining the skeleton of the algorithm
        public void PrepareRecipe()
        {
            BoilWater();
            Brew();
            PourInCup();
            AddCondiments();
        }

        // Common step implemented in the base class
        private void BoilWater()
        {
            Console.WriteLine("Boiling water...");
        }

        private void PourInCup()
        {
            Console.WriteLine("Pouring into cup...");
        }

        // Abstract steps to be implemented by subclasses
        protected abstract void Brew();
        protected abstract void AddCondiments();
    }

    // Step 2: Concrete Subclasses for Specific Beverages

    class Tea : Beverage
    {
        protected override void Brew()
        {
            Console.WriteLine("Steeping the tea...");
        }

        protected override void AddCondiments()
        {
            Console.WriteLine("Adding lemon...");
        }
    }

    class Coffee : Beverage
    {
        protected override void Brew()
        {
            Console.WriteLine("Dripping coffee through filter...");
        }

        protected override void AddCondiments()
        {
            Console.WriteLine("Adding sugar and milk...");
        }
    }

    // Step 3: Client Code

    class Program
    {
        static void Main()
        {
            Beverage tea = new Tea();
            Console.WriteLine("Making tea:");
            tea.PrepareRecipe();  // Executes the template method

            Console.WriteLine();

            Beverage coffee = new Coffee();
            Console.WriteLine("Making coffee:");
            coffee.PrepareRecipe();  // Executes the template method

            Console.ReadKey();
        }
    }
}
```

## Visitor

### 1) **When do I use it?**  
Use the **Visitor** pattern when you have an object structure (like a collection of objects) that needs to be processed in multiple ways, and you want to avoid adding new methods to these classes. 

This pattern is useful when:

- You want to perform operations on a group of objects without changing their classes.
- You need to extend the functionality of a class structure without modifying its source code.
- You have a complex object structure that needs to be processed differently by various operations.

### 2) **What does it look like?**  
The **Visitor** pattern typically involves:

- A visitor interface with a visit method for each type of element (class) in the object structure.
- Element classes (the classes in the object structure) that accept visitors and allow them to perform operations on themselves.
- Concrete visitors that implement the visitor interface and define the specific actions for each element.

### 3) **Code example:**  
```cs
using System;
using System.Collections.Generic;

namespace DesignPatterns.Visitor
{
    // Step 1: Define the Document Element Interface (IElement)
    public interface IDocumentElement
    {
        void Accept(IDocumentVisitor visitor);
    }

    public class Paragraph : IDocumentElement
    {
        public string Text { get; set; }

        public Paragraph(string text)
        {
            Text = text;
        }

        public void Accept(IDocumentVisitor visitor)
        {
            visitor.Visit(this);
        }
    }

    public class Image : IDocumentElement
    {
        public string Url { get; set; }

        public Image(string url)
        {
            Url = url;
        }

        public void Accept(IDocumentVisitor visitor)
        {
            visitor.Visit(this);
        }
    }

    public class Table : IDocumentElement
    {
        public string Title { get; set; }

        public Table(string title)
        {
            Title = title;
        }

        public void Accept(IDocumentVisitor visitor)
        {
            visitor.Visit(this);
        }
    }

    // Step 2: Define the Visitor Interface (IDocumentVisitor)
    public interface IDocumentVisitor
    {
        void Visit(Paragraph paragraph);
        void Visit(Image image);
        void Visit(Table table);
    }

    // Step 3: Define Concrete Visitors (RenderVisitor and SyntaxCheckVisitor)

    // Concrete Visitor 1: RenderVisitor - renders the document in HTML format
    public class RenderVisitor : IDocumentVisitor
    {
        public void Visit(Paragraph paragraph)
        {
            Console.WriteLine($"<p>{paragraph.Text}</p>");
        }

        public void Visit(Image image)
        {
            Console.WriteLine($"<img src=\"{image.Url}\" />");
        }

        public void Visit(Table table)
        {
            Console.WriteLine($"<table><caption>{table.Title}</caption></table>");
        }
    }

    // Concrete Visitor 2: SyntaxCheckVisitor - performs syntax checks on the document elements
    public class SyntaxCheckVisitor : IDocumentVisitor
    {
        public void Visit(Paragraph paragraph)
        {
            if (string.IsNullOrWhiteSpace(paragraph.Text))
                Console.WriteLine("Error: Paragraph is empty.");
            else
                Console.WriteLine("Paragraph syntax is correct.");
        }

        public void Visit(Image image)
        {
            if (string.IsNullOrWhiteSpace(image.Url))
                Console.WriteLine("Error: Image URL is missing.");
            else
                Console.WriteLine("Image syntax is correct.");
        }

        public void Visit(Table table)
        {
            if (string.IsNullOrWhiteSpace(table.Title))
                Console.WriteLine("Error: Table title is missing.");
            else
                Console.WriteLine("Table syntax is correct.");
        }
    }
    
    // Step 4: Client Code - Using the Visitor Pattern
    class Program
    {
        static void Main()
        {
            // Create a document with various elements
            List<IDocumentElement> document =
            [
                new Paragraph("This is a paragraph."),
                new Image("https://example.com/image.jpg"),
                new Table("Table of Contents")
            ];

            // Create visitors for rendering and syntax checking
            IDocumentVisitor renderVisitor = new RenderVisitor();
            IDocumentVisitor syntaxCheckVisitor = new SyntaxCheckVisitor();

            // Render the document (HTML format)
            Console.WriteLine("Rendering document:");
            foreach (var element in document)
            {
                element.Accept(renderVisitor);  // Each element accepts the visitor to be rendered
            }

            Console.WriteLine();

            // Perform syntax check on the document
            Console.WriteLine("Checking document syntax:");
            foreach (var element in document)
            {
                element.Accept(syntaxCheckVisitor);  // Each element accepts the visitor for syntax check
            }

            Console.ReadKey();
        }
    }
}
```
