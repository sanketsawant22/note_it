# Object-Oriented Programming (OOP)

## **Class & Object**
- **Class** → Blueprint or template for creating objects.
- **Object** → Instance of a class that holds data and behaviors defined in the class.
- **Constructor** → A special method that gets called automatically when an object is created. It initializes the object.

## **Packages**
- **Default Package** → If no package is explicitly defined, Java places classes in the default package.
- **User-defined Package** → Custom packages created by developers using the `package` keyword to organize code.

## **Methods**
- Functions inside a class are called **methods** in Java.

## **Object Destruction & Garbage Collection**
- Unlike C++, Java **does not have destructors**.
- Java uses **Garbage Collector (GC)** to automatically remove unused objects, freeing memory.

---

## **Access Modifiers**
1. **public** → Accessible from anywhere.
2. **private** → Accessible only within the class.
   - **Getters & Setters** are used to access private variables.
3. **protected** → Accessible within the same class, same package, and subclasses.
4. **default (package-private)** → If no modifier is specified, accessible within the same package only.

---

## **Polymorphism**
**Polymorphism = Many forms** (Same work in different ways)

### **Method Overloading (Compile-time polymorphism)**
- Multiple methods with the same name but different **parameters**.
- **Rules for overloading:**
  1. Return type **can be the same or different**.
  2. If return type is the same, the parameter types must be different.
  3. If parameter types are the same, the number of parameters must be different.

### **Method Overriding (Run-time polymorphism)**
- A method in a child class **redefines** a method from the parent class with the **same signature** (method name, return type, and parameters).
- Requires **inheritance**.
- The overridden method must have the **same return type** or a **subtype (covariant return type)**.

---

## **Inheritance**
**Inheritance** → Acquiring properties and behaviors from a parent class, promoting code **reusability**.
- **`extends` keyword** is used.
- Java **does not support multiple inheritance**, but it can be achieved using **interfaces**.

---

## **Encapsulation**
- Wrapping **data** (variables) and **methods** inside a single class.
- **Access modifiers** (private, public, protected) are used to restrict direct access to data.

---

## **Abstraction**
- **Hiding implementation details** while showing only necessary features.
- Achieved using:
  1. **Abstract Classes (`abstract` keyword)** → Can have both **abstract methods** (without implementation) and **concrete methods** (with implementation).
  2. **Interfaces** → A blueprint of methods (only method signatures, no implementation).

---

## **Interfaces**
- **Cannot have constructors** (since objects of an interface cannot be created).
- **Methods are abstract by default** (no implementation, only method declarations).
- **Supports multiple inheritance**, unlike classes.
- From Java 8 onwards, interfaces can have **default** and **static methods** with implementations.

---

## **`static` Keyword**
- **Static variables** → Shared across all instances of a class (class-level memory).
- **Static methods** → Can be called without creating an object of the class.
- **Static blocks** → Used for static initialization of class variables.
- **Static classes** → Only nested classes can be static in Java.

---

## **Additional Important Points**
✅ **`super` keyword** → Used to access parent class methods and constructors.
✅ **`this` keyword** → Refers to the current object instance.
✅ **Final keyword**
   - **final variable** → Value cannot be changed (constant).
   - **final method** → Cannot be overridden.
   - **final class** → Cannot be inherited.

