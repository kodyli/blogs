### Understanding the JVM and JMM


- Briefly introduce what the JVM (Java Virtual Machine) and JMM (Java Memory Model) are.

##### What is Java Virtual Machine(JVM)

The Java Virtual Machine(JVM) is an abstract computing machine. Like a real computing machine, it has [an instruction set](https://docs.oracle.com/javase/specs/jvms/se11/html/jvms-6.html) and manipulates various memory areas at run time. There are three notions of the JVM: specification, implementation, and instance.

1. ``A specification`` Where working of Java Virtual Machine is specified. [Java Virtual Machine Specifications](https://docs.oracle.com/javase/specs/index.html)
2. ``An implementation`` Its implementation has been provided by Oracle and other companies.
3. ``Runtime Instance`` Whenever you write java command to run the java class, an instance of JVM is created.

The Java Virtual Machine is the cornerstone of the Java platform. It is the component of the technology responsible for its hardware- and operating system independence, the small size of its compiled code, and its ability to protect users from malicious programs.


The Java Virtual Machine knows nothing of the Java programming language, only of a particular binary format, [the `class` file format](https://docs.oracle.com/javase/specs/jvms/se11/html/jvms-4.html). 

> Tips: [Groovy: A multi-faceted language for the Java platform.](http://www.groovy-lang.org/)

##### Java Virtual Machine Architecture

![JVM Architecture](https://ts1.cn.mm.bing.net/th/id/R-C.0d59ea4667ad34a14466b71903c26840?rik=uohVDS%2ffNR0T5w&pid=ImgRaw&r=0)

It contains class loader, run time data area, execution engine, etc.

1. Class loader

It is mainly responsible for three activities. 

* Loading
* Linking
* Initialization

``Loading``: The Class loader reads the “.class” file, generate the corresponding binary data and save it in the method area.

``Linking``: Performs verification, preparation, and (optionally) resolution. 

``Initialization``: In this phase, all static variables are assigned with their values defined in the code and static block(if any).


##### Execution Example
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
~~~
cd Documents/java 
~~~
1.1.2 Check App.java file in the current folder.
~~~bash
ls
~~~
~~~
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

~~~
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
       8: getstatic     #7                  // Field java/lang/System.out:Ljava/io/PrintStream;
      11: ldc           #21                 // class App
      13: invokevirtual #23                 // Method java/lang/Class.getClassLoader:()Ljava/lang/ClassLoader;
      16: invokevirtual #29                 // Method java/io/PrintStream.println:(Ljava/lang/Object;)V
      19: return
}
```

2. Run the App.class file.

- Mention key components like class loader, runtime data areas, and execution engine.



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