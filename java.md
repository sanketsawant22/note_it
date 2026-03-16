
# How Java Works

Java follows a flow where the source code written by the developer is first compiled into **bytecode**, and then that bytecode is executed by the **JVM**.

## Basic Flow

Developer → Java Code (.java) → Compiler (javac) → Bytecode (.class) → JVM → Output

## Important Terms

### JVM (Java Virtual Machine)

* JVM stands for **Java Virtual Machine**.
* It is responsible for **executing Java bytecode**.
* JVM does **not understand normal human-readable Java code directly**.
* It only understands **bytecode**.

### JRE (Java Runtime Environment)

* JRE stands for **Java Runtime Environment**.
* It provides the **runtime environment** needed to run Java applications.
* It contains:

  * **JVM**
  * **Libraries**
  * Other files required to run Java programs

### JDK (Java Development Kit)

* JDK stands for **Java Development Kit**.
* It is used to **develop, compile, and run** Java programs.
* It contains:

  * **JRE**
  * Development tools like **javac**
* If you want to **write Java code**, you need **JDK**.

<img width="1651" height="875" alt="image" src="https://github.com/user-attachments/assets/779a2ce5-d435-4d85-b104-88e3b277ab1a" />

# Datatypes in java

## Premitive

* Integer
  * byte 
  * short
  * int
  * long
  
* Float
  * float
  * double
  
By default, decimal values in Java are treated as double.
If you want to store a decimal number in a float, you must add f at the end.
  
  ```java
  double a = 23.25;
  float b = 23.25f;
  ```
  
* Character
  * char
  
* boolean

# Type conversion and Type casting in java

## 1. Type Conversion
If we assign a **smaller datatype to a bigger datatype**, it is called **type conversion**.

This happens **automatically** because there is no risk of data loss.

### Example:
```java
byte a = 120;
int b;

b = a;   // conversion 
```

## 2. Type Casting

If we assign a bigger datatype to a smaller datatype, it is called type casting.

This does not happen automatically because there may be data loss.
So, we must explicitly mention the datatype in brackets.

### Example:
```java
int b = 12;
byte a;

a = (byte) b;   // casting
```
### note
during casting if value is larger than types capacity then it will do modulo of that value with its range and store result 

  ```java
  int a = 257;
  byte b = (byte) a;

  // b has range of -128 to 127
  // total of 256 
  // so it will store 257 % 256 = 1
  ```


  # Object in Java

An object in Java is an **instance of a class**.

It is a real entity that has:

- **properties / data** → called **attributes**
- **behavior / actions** → called **methods**

A class is a blueprint, and an object is created from that blueprint.

## Example
```java
class Student {
    String name = "Sanket";
    int age = 20;
}

public class Main {
    public static void main(String[] args) {
        Student s1 = new Student();   // object
        System.out.println(s1.name);
        System.out.println(s1.age);
    }
}
