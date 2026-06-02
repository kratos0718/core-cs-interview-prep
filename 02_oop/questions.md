# Object-Oriented Programming — 60 Questions (basic → advanced)

⭐ = very frequently asked. Examples lean on Java/Python/C++ but the concepts are universal.

---

## A. Core concepts & the 4 pillars

**1. ⭐ What is Object-Oriented Programming?**
A programming paradigm that organizes code around **objects** — bundles of data (attributes) and behavior (methods) — rather than around functions and logic alone. It models real-world entities. The four pillars: Encapsulation, Abstraction, Inheritance, Polymorphism.

**2. ⭐ Class vs Object?**
A **class** is a blueprint/template defining attributes and methods. An **object** is a concrete instance of a class with actual values. `Car` is the class; *your specific red Honda* is an object. One class → many objects.

**3. ⭐ What is Encapsulation?**
Bundling data and the methods that operate on it into a single unit (class), and **restricting direct access** to the internal data (via private fields + public getters/setters). Benefits: protects data integrity, hides internal representation, allows changing internals without breaking users. Analogy: a capsule — you use the medicine without seeing its chemistry.

**4. ⭐ What is Abstraction?**
Hiding complex implementation details and exposing only the essential features/interface. You know *what* a method does, not *how*. Example: you call `car.start()` without knowing the ignition logic. Achieved via abstract classes and interfaces.

**5. Encapsulation vs Abstraction (the classic trap)?**
**Abstraction** is about *design* — hiding complexity, showing only what's relevant (the "what"). **Encapsulation** is about *implementation* — bundling data+methods and controlling access (the "how it's protected"). Abstraction solves *design-level* hiding; encapsulation solves *data-level* hiding. They often work together.

**6. ⭐ What is Inheritance?**
A mechanism where a class (child/subclass) acquires the properties and methods of another class (parent/superclass), enabling code reuse and an "is-a" relationship. A `Dog` *is an* `Animal`, so `Dog` inherits from `Animal`. The child can add or override behavior.

**7. ⭐ What is Polymorphism?**
"Many forms" — the ability of the same interface/method name to behave differently based on the object or arguments. Two types: **compile-time** (overloading) and **runtime** (overriding). Example: `area()` works on `Circle` and `Square` but computes differently.

**8. ⭐ Overloading vs Overriding?**
**Overloading** (compile-time/static polymorphism): same method name, *different parameters*, in the *same class*. Resolved at compile time. **Overriding** (runtime/dynamic polymorphism): a subclass provides a *new implementation* of a method with the *same signature* as the parent. Resolved at runtime based on the actual object.

**9. Static vs Dynamic (compile-time vs run-time) polymorphism?**
**Static**: the call is resolved at compile time (method overloading, operator overloading). **Dynamic**: resolved at runtime via the actual object's type (method overriding, using virtual functions / dynamic dispatch).

**10. What is an abstract class?**
A class that cannot be instantiated and is meant to be subclassed. It can have both abstract methods (no body — subclasses must implement) and concrete methods (with implementation). Use it to share common code + force a contract.

**11. ⭐ Abstract class vs Interface?**
An **abstract class** can have method implementations + state (fields) and represents an "is-a" with shared code; a class extends only **one**. An **interface** is a pure contract (traditionally only method signatures, no state) representing a "can-do" capability; a class can implement **many**. Use an abstract class for shared base behavior, an interface for capabilities. (Note: modern Java interfaces allow default methods, blurring this.)

**12. What is an interface?**
A contract specifying *what* methods a class must provide, without *how*. Any class implementing it guarantees those methods exist. Enables polymorphism and decoupling — code can depend on the interface, not a concrete class.

**13. ⭐ What is a constructor?**
A special method automatically called when an object is created, used to initialize its state. Same name as the class (Java/C++) or `__init__` (Python). No return type. If you don't define one, a default (no-arg) constructor is provided.

**14. Types of constructors?**
**Default** (no arguments, auto-provided if none defined), **parameterized** (takes arguments to set initial values), and **copy constructor** (creates a new object as a copy of an existing one — common in C++).

**15. What is a destructor?**
A method called when an object is destroyed, to release resources (memory, files). C++ has explicit destructors (`~ClassName`). Java/Python rely on garbage collection instead, with optional finalizers/`__del__` (discouraged).

**16. What is `this` / `self`?**
A reference to the current object inside its own methods — used to access the object's own attributes/methods, especially to disambiguate from parameters of the same name. `this` in Java/C++, `self` in Python.

---

## B. Inheritance deep-dive

**17. ⭐ Types of inheritance?**
**Single** (one parent), **Multilevel** (A→B→C chain), **Hierarchical** (one parent, many children), **Multiple** (one child, many parents — Java forbids it for classes, Python allows it), and **Hybrid** (a combination).

**18. ⭐ Why doesn't Java support multiple inheritance (of classes)?**
To avoid the **Diamond Problem**: if class D inherits from B and C, which both inherit from A and override the same method, D is ambiguous about which version to use. Java forbids multiple class inheritance to prevent this, but allows multiple *interfaces* (which carry no conflicting implementation, traditionally).

**19. What is the Diamond Problem?**
An ambiguity in multiple inheritance where a class inherits two implementations of the same method through two paths from a common ancestor (forming a diamond shape A→B, A→C, B&C→D). The compiler/runtime can't decide which to use. C++ resolves it with virtual inheritance; Python with the MRO (Method Resolution Order).

**20. What is method resolution order (MRO)?**
The order in which Python searches base classes for a method in multiple inheritance, using the C3 linearization algorithm. It guarantees a consistent, predictable order so the Diamond Problem is resolved deterministically. Check with `ClassName.__mro__`.

**21. Is-a vs Has-a relationship?**
**Is-a** = inheritance (a `Dog` *is an* `Animal`). **Has-a** = composition (a `Car` *has an* `Engine`). Prefer **composition over inheritance** when there isn't a true "is-a" relationship — it's more flexible and avoids deep, brittle hierarchies.

**22. ⭐ Composition vs Inheritance — which to prefer and why?**
**Composition** (building objects from other objects) is usually preferred over **inheritance** because it's more flexible: you can change behavior at runtime, avoid tight coupling to a parent's implementation, and dodge fragile deep hierarchies. Famous guideline: "favor composition over inheritance." Use inheritance only for genuine is-a relationships.

**23. What is the `super` keyword?**
Refers to the parent class — used to call the parent's constructor or overridden methods from the child. e.g. `super().__init__()` (Python) or `super.method()` (Java). Ensures the parent's initialization/behavior runs.

**24. Can a constructor be inherited?**
No — constructors are not inherited. But a subclass's constructor can (and often must) call the parent's constructor via `super()` to initialize inherited fields.

**25. What is method hiding (static methods)?**
If a subclass defines a static method with the same signature as a parent's static method, it **hides** rather than overrides it — the version called depends on the *reference type*, not the object (no dynamic dispatch for statics). A subtle gotcha.

---

## C. Encapsulation, access, and more

**26. ⭐ Access modifiers — explain.**
Control visibility of class members. **public**: accessible everywhere. **private**: only within the same class. **protected**: within the class + subclasses (+ same package in Java). **default/package-private** (Java): within the same package. Python uses convention: `_x` (protected), `__x` (name-mangled "private").

**27. What are getters and setters? Why use them?**
Methods to read (getter) and modify (setter) private fields. They enforce encapsulation: you can add validation, make a field read-only, log access, or change internal representation — all without breaking callers who use the methods.

**28. Static vs Instance members?**
**Instance** members belong to each object (each object has its own copy). **Static** members belong to the class itself (one shared copy across all objects), accessed via the class name. A static counter shared by all instances is the classic example.

**29. What is a static method?**
A method belonging to the class, not any instance — it can't access instance fields/`this`, only static data. Called via the class name. Used for utility functions that don't need object state (e.g. `Math.sqrt`).

**30. ⭐ What is a virtual function?**
(C++) A member function declared `virtual` in a base class, designed to be overridden, enabling **runtime polymorphism** via dynamic dispatch — the actual object's version is called through a base pointer/reference. In Java, methods are virtual by default. Without it, the base version would be called statically.

**31. What is a pure virtual function / abstract method?**
A virtual function with no implementation in the base class (`= 0` in C++, `abstract` in Java, `@abstractmethod` in Python). It makes the class abstract and forces subclasses to implement it. Defines a contract.

**32. What is the vtable (virtual table)?**
A per-class table of pointers to its virtual functions. Each object of a class with virtual functions holds a hidden pointer (vptr) to its class's vtable. At runtime, a virtual call looks up the right function via the vtable — that's how dynamic dispatch works under the hood.

**33. What is operator overloading?**
Defining how operators (`+`, `==`, `<<`) behave for user-defined types. e.g. making `v1 + v2` add two `Vector` objects. A form of compile-time polymorphism. Supported in C++/Python (`__add__`), not Java.

---

## D. Advanced / design

**34. ⭐ What are the SOLID principles?**
Five design principles for maintainable OOP: **S**ingle Responsibility (a class has one reason to change), **O**pen/Closed (open for extension, closed for modification), **L**iskov Substitution (subtypes must be usable wherever the base type is), **I**nterface Segregation (many small interfaces > one fat one), **D**ependency Inversion (depend on abstractions, not concretions).

**35. Explain the Single Responsibility Principle.**
A class should have only one reason to change — i.e., one job/responsibility. A class that handles both database access and report formatting violates it; split them. Makes code easier to understand, test, and modify.

**36. Explain the Liskov Substitution Principle.**
Objects of a subclass should be substitutable for their superclass without breaking the program. If `Square` inherits from `Rectangle` but breaks the assumption that width/height are independent, it violates LSP. A subtype must honor the base type's contract.

**37. What is the Open/Closed Principle?**
Software entities should be open for *extension* but closed for *modification* — you add new behavior by adding new code (e.g. a new subclass), not by editing existing, tested code. Achieved via abstraction/polymorphism.

**38. ⭐ What is a design pattern? Name a few.**
A reusable, proven solution to a common design problem. **Creational** (Singleton, Factory, Builder) — object creation. **Structural** (Adapter, Decorator, Facade) — object composition. **Behavioral** (Observer, Strategy, Iterator) — object interaction.

**39. ⭐ Explain the Singleton pattern.**
Ensures a class has only **one instance** and provides a global access point to it (e.g. a config or logging object). Implemented with a private constructor + a static method returning the single instance. Careful with thread-safety. Criticized for being a global, hard to test.

**40. Explain the Factory pattern.**
Provides a method to create objects without exposing the instantiation logic to the client — the factory decides which concrete class to instantiate based on input. Decouples object creation from use. e.g. `ShapeFactory.create("circle")`.

**41. Explain the Observer pattern.**
Defines a one-to-many dependency: when one object (subject) changes state, all its dependents (observers) are notified automatically. Basis of event systems / pub-sub. e.g. UI elements updating when data changes.

**42. Explain the Strategy pattern.**
Defines a family of interchangeable algorithms, encapsulates each, and makes them swappable at runtime. e.g. a `Sorter` that can use different `SortStrategy` objects. Favors composition over inheritance for varying behavior.

**43. Explain the Decorator pattern.**
Attaches additional responsibilities to an object dynamically by wrapping it, without changing its class. e.g. wrapping a `Coffee` with `MilkDecorator`, `SugarDecorator`. A flexible alternative to subclassing for extending behavior.

**44. What is coupling and cohesion?**
**Coupling**: how dependent modules are on each other — you want **low coupling** (changes don't ripple). **Cohesion**: how focused a module is on a single purpose — you want **high cohesion** (a class does one thing well). Good design = low coupling, high cohesion.

**45. What is the DRY principle?**
"Don't Repeat Yourself" — every piece of knowledge should have a single, authoritative representation. Duplicated code/logic is a maintenance hazard; extract it into a function/class.

**46. What is the YAGNI principle?**
"You Aren't Gonna Need It" — don't build features or abstractions until they're actually needed. Avoids over-engineering.

---

## E. Language-mechanics & gotchas

**47. ⭐ Pass by value vs Pass by reference?**
**Pass by value**: the function gets a *copy* of the argument; changes don't affect the original. **Pass by reference**: the function gets a *reference* to the original; changes affect it. Java is always pass-by-value (but for objects, the *value* is a reference, so you can mutate the object — a common confusion). Python passes object references by value ("pass by object reference").

**48. What is a shallow copy vs deep copy?**
**Shallow copy**: copies the object but its nested objects are *shared* (copied references) — changing a nested object affects both. **Deep copy**: recursively copies everything, fully independent. Matters for mutable nested structures.

**49. What is the difference between `==` and `equals()` (or `is`)?**
`==` compares *references* (do both point to the same object?) in Java; `equals()` compares *values/content* (override it to define equality). In Python, `==` compares values, `is` compares identity (same object). The fresher trap: comparing strings/objects with `==` when you mean content.

**50. What is method overriding's relationship to `hashCode`/`equals`?**
If you override `equals()`, you must also override `hashCode()` so equal objects have equal hash codes — otherwise they behave incorrectly in hash-based collections (HashMap/HashSet). A classic Java contract.

**51. What is an immutable object? Example?**
An object whose state can't change after creation (e.g. Java `String`, Python `tuple`/`str`). Benefits: thread-safe (no synchronization needed), safe to share/cache, no surprising mutations. To "change" it, you create a new object.

**52. ⭐ Why is String immutable in Java?**
For security (used in class loading, network connections), thread-safety (shareable without locks), caching (the String pool reuses literals), and hashcode caching (used as HashMap keys). Modifying a String creates a new object.

**53. What is garbage collection?**
Automatic memory management that reclaims objects no longer reachable/referenced, so you don't manually free memory (Java/Python/C#). Prevents most memory leaks but adds runtime overhead and unpredictable pauses. (C/C++ require manual `free`/`delete`.)

**54. Stack vs Heap for objects?**
Local variables/primitives and references live on the **stack** (fast, auto-managed). Objects themselves live on the **heap** (dynamically allocated, garbage-collected). In Java, `new` always allocates on the heap.

**55. What is `final` / `const`?**
**`final`** (Java): a final variable can't be reassigned, a final method can't be overridden, a final class can't be subclassed. **`const`** (C++): a constant value/pointer. Used to express immutability and intent.

**56. What is the difference between an error and an exception?**
An **exception** is a recoverable abnormal condition you can catch and handle (e.g. file not found). An **error** (Java `Error`) is a serious problem usually not meant to be caught (e.g. `OutOfMemoryError`, `StackOverflowError`). Both are "Throwable" in Java.

**57. Checked vs Unchecked exceptions (Java)?**
**Checked**: must be declared or handled at compile time (e.g. `IOException`) — for recoverable conditions. **Unchecked** (RuntimeExceptions, e.g. `NullPointerException`): not enforced by the compiler — usually programming bugs.

**58. What is the `instanceof` operator?**
Checks whether an object is an instance of a particular class/interface (returns boolean). Used for type checking before casting. Overusing it often signals a missed polymorphism opportunity.

**59. What is dynamic binding (late binding)?**
Deciding which method implementation to call at **runtime** based on the actual object type, not the reference type. It's the mechanism behind method overriding / runtime polymorphism. Contrast with **static/early binding** (decided at compile time, e.g. overloading).

**60. ⭐ What is an object's lifecycle?**
**Creation** (memory allocated, constructor runs, fields initialized) → **In use** (methods called, state changes) → **Unreachable** (no references remain) → **Garbage collected** (memory reclaimed; destructor/finalizer may run). In manual-memory languages, you explicitly destroy it.

---

## ⭐ The 10 OOP must-knows:
4 pillars (3,4,6,7) · Encapsulation vs Abstraction (5) · Overloading vs Overriding (8) · Abstract class vs Interface (11) · Composition vs Inheritance (22) · SOLID (34) · Singleton/Factory (39,40) · Pass by value vs reference (47) · Why String is immutable (52) · Static vs dynamic binding (59).
