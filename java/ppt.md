# Understanding the JVM

<a name="section-1"></a>
## 1 What is Java Virtual Machine(JVM)

The Java Virtual Machine(JVM) is an abstract computing machine. Like a real computing machine, it has [an instruction set](https://docs.oracle.com/javase/specs/jvms/se11/html/jvms-6.html) and manipulates various memory areas at run time. There are three notions of the JVM: specification, implementation, and instance.

a. ``A specification`` Where working of Java Virtual Machine is specified. [Java Virtual Machine Specifications](https://docs.oracle.com/javase/specs/index.html)
b. ``An implementation`` Its implementation has been provided by Oracle and other companies.
c. ``Runtime Instance`` Whenever you write java command to run the java class, an instance of JVM is created.

The Java Virtual Machine is the cornerstone of the Java platform. It is the component of the technology responsible for its hardware- and operating system independence, the small size of its compiled code, and its ability to protect users from malicious programs.


The Java Virtual Machine knows nothing of the Java programming language, only of a particular binary format, [the `class` file format](https://docs.oracle.com/javase/specs/jvms/se11/html/jvms-4.html). 

> Tips: [Groovy: A multi-faceted language for the Java platform.](http://www.groovy-lang.org/)

<a name="section-2"></a>
## 2 Java Virtual Machine Architecture

![JVM Architecture](https://ts1.cn.mm.bing.net/th/id/R-C.0d59ea4667ad34a14466b71903c26840?rik=uohVDS%2ffNR0T5w&pid=ImgRaw&r=0)

It contains class loader, runtime data area, execution engine, etc.
### 2.1 Runtime Data Area
The Java Virtual Machine defines various run-time data areas that are used during execution of a program: `method area`, `heap`, `jvm stack`, `prpgram counter register`, `native method stack`. Method area and heap are created on Java Virtual Machine start-up and are destroyed only when the Java Virtual Machine exits. JVM stack, program counter register and native method stack are per thread. Per-thread data areas are created when a thread is created and destroyed when the thread exits.

![Runtime Data Area](https://ts1.cn.mm.bing.net/th/id/R-C.0ec9579d7ece952947a0db74f6470d57?rik=Cdb4%2f1U05fyVQQ&riu=http%3a%2f%2fwww.programcreek.com%2fwp-content%2fuploads%2f2013%2f04%2fJVM-runtime-data-area.jpg&ehk=UOaS7u5PslCIm%2fjW%2fkMaEnoQqpftuqVU7qwlkI5ztJ0%3d&risl=&pid=ImgRaw&r=0)

#### 2.1.1 Method Area - Class level data
It stores per-class structures such as the run-time constant pool, field and method data, and the code for methods and constructors, including [the special methods](https://docs.oracle.com/javase/specs/jvms/se11/html/jvms-2.html#jvms-2.9) used in class and interface initialization and in instance initialization.
#### 2.1.2 Heap - Object level data
The heap is the run-time data area from which memory for all class instances and arrays is allocated.

Heap storage for objects is reclaimed by an automatic storage management system (known as a garbage collector); objects are never explicitly deallocated. 
![GC](https://antapex.org/java_heaps_1.jpg)

#### 2.1.3 JVM Stack 
A Java Virtual Machine stack stores [frames](https://docs.oracle.com/javase/specs/jvms/se11/html/jvms-2.html#jvms-2.6).
##### 2.1.3.1 Frames - Method level data
A new frame is created each time a method is invoked. A frame is destroyed when its method invocation completes, whether that completion is normal or abrupt (it throws an uncaught exception). Each frame has its own array of local variables, its own operand stack, and a reference to the runtime constant pool of the class of the current method.

#### 2.1.4 Program Counter Regisger
Store address of current execution instruction of a thread.
#### 2.1.5 Native Method Stack
It stores native method information. 

``The viewpoint of the Java programming language``:
~~~java
public class Dog {
    public static int NUMBER_OF_LEGS = 4;//Method Area

    public String name = "Dog";//Heap

    public void run() {
        int speed = 4;//Frame, JVM Stack
        System.out.println(this.name + " is runing at " + speed + "km/hour.");
    }
}
~~~
>Read More:

>[Runtime Data Area](https://docs.oracle.com/javase/specs/jvms/se11/html/jvms-2.html#jvms-2.5)

### 2.2 Class Loader

It is mainly responsible for three activities: `Loading`, `Linking`, `Initialization`.

#### 2.2.1 Loading
The Class loader reads the “.class” file, generate the corresponding binary data and save it in the `method area`. There are three built-in classloaders in Java.

a. `Bootstrap Class Loader`: It loads the rt.jar file which contains all class files of Java Standard Edition like java.lang package classes, java.net package classes, java.util package classes, java.io package classes, java.sql package classes etc.

b. `Extension Class Loader`: It loades the jar files located inside $JAVA_HOME/jre/lib/ext directory.

c. `Application Class Loader`: It loads the classfiles from classpath. By default, classpath is set to current directory.

The Java platform uses a delegation model for loading classes. The basic idea is that every class loader has a "parent" class loader. When loading a class, a class loader first "delegates" the search for the class to its parent class loader before attempting to find the class itself.
![The Class Loader Hierarchy](https://media.geeksforgeeks.org/wp-content/uploads/jvmclassloader.jpg)

>Read More:

>[Class Loaders in Java](https://www.baeldung.com/java-classloaders)

#### 2.2.2 Linking
Performs verification, preparation, and (optionally) resolution. 

a. `Verification`: It ensures the correctness of the .class file i.e. it checks whether this file is properly formatted and generated by a valid compiler or not.

b. `Preparation`: JVM allocates memory for class static variables and initializing the memory to default values. 

c. `Resolution`: It is the process of replacing symbolic references from the type with direct references. It is done by searching into the method area to locate the referenced entity.

#### 2.2.3 Initialization
In this phase, all static variables are assigned with their values defined in the code and static block(if any).

<a name="section-5"></a>
## Execution Example
Here we present an overview of the process from the viewpoint of the Java programming language.

1. Get .class file.

```java
//App.java
public class App {
    public static void main(String[] args) {
        System.out.println("Hello World!");
    }
}
```
1.1 Compile the java file using `javac`.

1.1.1 Go to the folder.
~~~bash
cd Documents/java 
~~~
1.1.2 Check App.java file in the current folder.
~~~bash
ls
~~~
~~~bash
App.java
~~~
1.1.3 Compile the App.java file.
```bash
javac App.java
```
1.1.4 Check App.class file in the current folder.
~~~bash
ls
~~~
~~~bash
App.class	App.java
~~~

1.2 Open .class file in java using `javap -c`.
```bash
javap -c App.class
```
```cpp
Compiled from "App.java"
public class App {
  public App();
    Code:
       0: aload_0
       1: invokespecial #1                  // Method java/lang/Object."<init>":()V
       4: return

  public static void main(java.lang.String[]);
    Code:
       0: getstatic     #7                  // Field java/lang/System.out:Ljava/io/PrintStream;
       3: ldc           #13                 // String Hello World!
       5: invokevirtual #15                 // Method java/io/PrintStream.println:(Ljava/lang/String;)V
       8: return
}
```

2. Run the App.class file.




Data Types:

Like the Java programming language, the Java Virtual Machine operates on two
kinds of types: primitive types and reference types. There are, correspondingly, two
kinds of values that can be stored in variables, passed as arguments, returned by
methods, and operated upon: primitive values and reference values.



Run-Time Data Areas

1. Program Counter Register
2. Java Virtual Machine Stacks
3. Native Method Stacks
4. Heap
5. Method Area



Java 内存模型（Java Memory Model，JMM）名字看上去和 Java 内存结构（JVM 运行时内存结构）差不多，但这两者并不是一回事。JMM 并不像 JVM 内存结构一样是真实存在的，它只是一个抽象的概念。

Java 的线程间通过共享内存（Java堆和方法区）进行通信，在通信过程中会存在一系列如可见性、原子性、顺序性等问题，而 JMM 就是围绕着多线程通信以及与其相关的一系列特性而建立的模型。