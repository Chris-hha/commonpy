## 面向对象

### 方法

1. 一个Java源文件可以包含多个类的定义，但只能定义一个public类，且public类名必须与文件名一致。如果要定义多个public类，必须拆到多个Java源文件中。
2. 方法返回值通过`return`语句实现，如果没有返回值，返回类型设置为`void`，可以省略`return`。
3. Class 中，直接操作`field`，容易造成逻辑混乱。为了避免外部代码直接去访问`field`，我们可以用`private`修饰`field`，拒绝外部访问。
4. 有`private`方法，自然就有`public`方法。和`private`字段一样，`private`方法不允许外部调用。
5. 在方法内部，可以使用一个隐含的变量`this`，它始终指向当前实例。因此，通过`this.field`就可以访问当前实例的字段。但是，如果有局部变量和字段重名，那么局部变量优先级更高，就必须加上`this`：
6. 可变参数用`类型...`定义，可变参数相当于数组类型：
7. 引用类型参数的传递，调用方的变量，和接收方的参数变量，指向的是同一个对象。双方任意一方对这个对象的修改，都会影响对方

### 构造方法

1. 由于构造方法是如此特殊，所以构造方法的名称就是类名。构造方法的参数没有限制，在方法内部，也可以编写任意语句。但是，和普通方法相比，构造方法没有返回值（也没有`void`），调用构造方法，必须用`new`操作符。

2. 原因是如果一个类没有定义构造方法，编译器会自动为我们生成一个默认构造方法，它没有参数，也没有执行语句，类似这样：

   ```java
   class Person {
       public Person() {
       }
   }
   ```

   如果我们自定义了一个构造方法，那么，编译器就*不再*自动创建默认构造方法

3. 如果既要能使用带参数的构造方法，又想保留不带参数的构造方法，那么只能把两个构造方法都定义出来：

   ```java
   class Person {
       private String name;
       private int age;
   
       public Person() {
       }
   
       public Person(String name, int age) {
           this.name = name;
           this.age = age;
       }
    }
   ```

   没有在构造方法中初始化字段时，引用类型的字段默认是`null`，数值类型的字段用默认值，`int`类型默认值是`0`，布尔类型默认值是`false`

4. 可以定义多个构造方法，在通过`new`操作符调用的时候，编译器通过构造方法的参数数量、位置和类型自动区分.一个构造方法可以调用其他构造方法，这样做的目的是便于代码复用。调用其他构造方法的语法是`this(…)`

   ```java
   class Person {
       private String name;
       private int age;
   
       public Person(String name, int age) {
           this.name = name;
           this.age = age;
       }
   
       public Person(String name) {
           this(name, 18); // 调用另一个构造方法Person(String, int)
       }
   
       public Person() {
           this("Unnamed"); // 调用另一个构造方法Person(String)
       }
   }
   ```

### 方法重载

1. 在一个类中，我们可以定义多个方法。如果有一系列方法，它们的功能都是类似的，只有参数有所不同，那么，可以把这一组方法名做成*同名*方法。这种方法名相同，但各自的参数不同，称为方法重载（`Overload`）。方法重载的返回值类型通常都是相同的。

### 继承

1. Java使用`extends`关键字来实现继承。子类自动获得了父类的所有字段，严禁定义与父类重名的字段！在Java中，没有明确写`extends`的类，编译器会自动加上`extends Object`。Java只允许一个class继承自一个类，因此，一个类有且仅有一个父类。只有`Object`特殊，它没有父类。继承有个特点，就是子类无法访问父类的`private`字段或者`private`方法。这使得继承的作用被削弱了。为了让子类可以访问父类的字段，我们需要把`private`改为`protected`。用`protected`修饰的字段可以被子类访问。`protected`关键字可以把字段和方法的访问权限控制在继承树内部，一个`protected`字段和方法可以被其子类 ，以及子类的子类所访问。

2. `super`关键字表示父类（超类）。子类引用父类的字段时，可以用`super.fieldName`。例如：

   ```java
   class Student extends Person {
       public String hello() {
           return "Hello, " + super.name;
       }
   }
   ```

   实际上，这里使用`super.name`，或者`this.name`，或者`name`，效果都是一样的。编译器会自动定位到父类的`name`字段。

3. 在某些时候，就必须使用`super`。如果父类没有默认的构造方法，子类就必须显式调用`super()`并给出参数以便让编译器定位到父类的一个合适的构造方法。子类*不会继承*任何父类的构造方法。子类默认的构造方法是编译器自动生成的，不是继承的。

   ```java
   public class Main {
       public static void main(String[] args) {
           Student s = new Student("Xiao Ming", 12, 89);
       }
   }
   
   class Person {
       protected String name;
       protected int age;
   
       public Person(String name, int age) {
           this.name = name;
           this.age = age;
       }
   }
   
   class Student extends Person {
       protected int score;
   
       public Student(String name, int age, int score) {
         	super(name, age);
           this.score = score;
       }
   }
   
   ```

4. 把一个子类类型安全地变为父类类型的赋值，被称为向上转型（upcasting）。

### 多态

1. 在继承关系中，子类如果定义了一个与父类方法签名完全相同的方法，被称为覆写（Override）。Override和Overload不同的是，如果方法签名不同，就是Overload，Overload方法是一个新方法；如果方法签名相同，并且返回值也相同，就是`Override`。

2. 加上`@Override`可以让编译器帮助检查是否进行了正确的覆写。希望进行覆写，但是不小心写错了方法签名，编译器会报错

   ```java
   class Person {
       public void run() { … }
   }
   
   class Student extends Person {
       // 不是Override，因为参数不同:
       public void run(String s) { … }
       // 不是Override，因为返回值不同:
       public int run() { … }
   }
   ```

   重写方法不能比被重写方法限制有更严格的限制访问级别，会报错，如被继承是public，则重写方法也是public，而不能是private

   形参和返回值都必须是一样的

   重写方法不能抛出新的异常或比被重写方法生命的检查异常更广的检查异常

   不能重写被标识为final的方法

   如果一个方法不能被继承，则不能重写，比如private

   子类不能用静态方法重写父类的非静态方法

3. Java的实例方法调用是基于运行时的实际类型的动态调用，而非变量的声明类型。

   `多态`是指，针对某个类型的方法调用，其真正执行的方法取决于运行时期实际类型的方法。

   ```java
   public class Main {
       public static void main(String[] args) {
           Person p = new Student();
           p.run(); // 应该打印Person.run还是Student.run?实际上是Student.run
       }
   }
   
   class Person {
       public void run() {
           System.out.println("Person.run");
       }
   }
   
   class Student extends Person {
       @Override
       public void run() {
           System.out.println("Student.run");
       }
   }
   ```

4. 多态具有一个非常强大的功能，就是允许添加更多类型的子类实现功能扩展，却不需要修改基于父类的代码。

```java
public class Main {
    public static void main(String[] args) {
        // 给一个有普通收入、工资收入和享受国务院特殊津贴的小伙伴算税:
        Income[] incomes = new Income[]{
                new Income(3000),
                new Salary(7500),
                new StateCouncilSpecialAllowance(15000)
        };
        System.out.println(totalTax(incomes));
    }

    public static double totalTax(Income... incomes) {
        double total = 0;
        for (Income income : incomes) {
            total += income.getTax();
        }
        return total;
    }
}

class Income {
    protected double income;

    public Income(double income) {
        this.income = income;
    }

    public double getTax() {
        return income * 0.1;
    }
}

class Salary extends Income {
    public Salary(double income) {
        super(income);
    }

    @Override
    public double getTax() {
        if (income < 5000) {
            return 0;
        }
        return (income - 5000) * 0.2;
    }
}

class StateCouncilSpecialAllowance extends Income {
    public StateCouncilSpecialAllowance(double income) {
        super(income);
    }

    @Override
    public double getTax() {
        return 0;
    }
}

```

观察`totalTax()`方法：利用多态，`totalTax()`方法只需要和`Income`打交道，它完全不需要知道`Salary`和`StateCouncilSpecialAllowance`的存在，就可以正确计算出总的税。如果我们要新增一种稿费收入，只需要从`Income`派生，然后正确覆写`getTax()`方法就可以。把新的类型传入`totalTax()`，不需要修改任何代码。

可见，多态具有一个非常强大的功能，就是允许添加更多类型的子类实现功能扩展，却不需要修改基于父类的代码。

5. 在子类的覆写方法中，如果要调用父类的被覆写的方法，可以通过`super`来调用。例如：

```java
class Person {
    protected String name;
    public String hello() {
        return "Hello, " + name;
    }
}

class Student extends Person {
    @Override
    public String hello() {
        // 调用父类的hello()方法:
        return super.hello() + "!";
    }
}
```

6. 继承可以允许子类覆写父类的方法。如果一个父类不允许子类对它的某个方法进行覆写，可以把该方法标记为`final`。用`final`修饰的方法不能被`Override`

7. 如果一个类不希望任何其他类继承自它，那么可以把这个类本身标记为`final`;

8. 对于一个类的实例字段，同样可以用`final`修饰。

   ```java
   class Person {
       public final String name = "Unamed";
   }
   ```

   用`final`修饰的字段在初始化后不能被修改。对`final`字段重新赋值会报错.

   ```java
   Person p = new Person();
   p.name = "New Name"; // compile error!
   ```

   可以在构造方法中初始化final字段：

   ```java
   class Person {
       public final String name;
       public Person(String name) {
           this.name = name;
       }
   }
   ```


### 抽象类

```java
abstract class Person {
    public abstract void run();
}
```

1. 如果父类的方法本身不需要实现任何功能，仅仅是为了定义方法签名，目的是让子类去覆写它，那么，可以把父类的方法声明为抽象方法：

2. 把一个方法声明为`abstract`，表示它是一个抽象方法，本身没有实现任何方法语句。因为这个抽象方法本身是无法执行的，所以，`Person`类也无法被实例化。编译器会告诉我们，无法编译`Person`类，因为它包含抽象方法。

   必须把`Person`类本身也声明为`abstract`，才能正确编译它

3. 因为抽象类本身被设计成只能用于被继承，因此，抽象类可以强迫子类实现其定义的抽象方法，否则编译会报错。因此，抽象方法实际上相当于定义了“规范”。
4. 面向抽象编程的本质就是：
   - 上层代码只定义规范（例如：`abstract class Person`）；
   - 不需要子类就可以实现业务逻辑（正常编译）； 
   - 具体的业务逻辑由不同的子类实现，调用者并不关心子类类型。

5. 抽象类不可以被实例化。

6. 抽象类可以有构造方法，主要被抽象子类调用

7. 抽象类中可以有抽象方法、静态方法或者实例方法

8. 抽象方法不可以被static和final，private修饰，一般使用public修饰

9. 抽象方法必须通过子类重写来使用

10. 抽象类的定义和使用规则如下：

    1. 抽象类和抽象方法都要使用 abstract 关键字声明。
    2. 如果一个方法被声明为抽象的，那么这个类也必须声明为抽象的。而一个抽象类中，可以有 0~n 个抽象方法，以及 0~n 个具体方法。
    3. 抽象类不能实例化，也就是不能使用 new 关键字创建对象。

11. 抽象方法的 3 个特征如下：

    1. 抽象方法没有方法体
    2. 抽象方法必须存在于抽象类中
    3. 子类重写父类时，必须重写父类所有的抽象方法


### 接口

1. 如果一个抽象类没有字段，所有的方法全部都是抽象方法，就可以把该抽象类改写成接口。接口没有字段，所有的方法默认都是public abstract的，当一个class去实现一个interface时，需要使用impements关键字。

2. 接口继承，相当于接口方法的扩展。

3. default方法。实现类可以不必覆写`default`方法。`default`方法的目的是，当我们需要给接口新增一个方法时，会涉及到修改全部子类。如果新增的是`default`方法，那么子类就不必全部修改，只需要在需要覆写的地方去覆写新增方法。

   `default`方法和抽象类的普通方法是有所不同的。因为`interface`没有字段，`default`方法无法访问字段，而抽象类的普通方法可以访问实例字段。

抽象类和接口的对比如下：

|            | abstract class       | interface                   |
| :--------- | :------------------- | :-------------------------- |
| 继承       | 只能extends一个class | 可以implements多个interface |
| 字段       | 可以定义实例字段     | 不能定义实例字段            |
| 抽象方法   | 可以定义抽象方法     | 可以定义抽象方法            |
| 非抽象方法 | 可以定义非抽象方法   | 可以定义default方法         |

4. 接口只能存在常量，不能存在其他属性

### 静态字段和静态方法

1. 实例字段在每个实例中都有自己的一个独立“空间”，但是静态字段只有一个共享“空间”，所有实例都会共享该字段。对于静态字段，无论修改哪个实例的静态字段，效果都是一样的：所有实例的静态字段都被修改了，原因是静态字段并不属于实例；在Java程序中，实例对象并没有静态字段。在代码中，实例对象能访问静态字段只是因为编译器可以根据实例类型自动转换为`类名.静态字段`来访问静态对象。
2. 调用实例方法必须通过一个实例变量，而调用静态方法则不需要实例变量，通过类名就可以调用。
3. 因为静态方法属于`class`而不属于实例，因此，静态方法内部，无法访问`this`变量，也无法访问实例字段，它只能访问静态字段。

### 包

1. Java定义了一种名字空间，称之为包：`package`。一个类总是属于某个包，类名（比如`Person`）只是一个简写，真正的完整类名是`包名.类名`。在Java虚拟机执行的时候，JVM只看完整类名，因此，只要包名不同，类就不同。

2. 特别注意：包没有父子关系。java.util和java.util.zip是不同的包，两者没有任何继承关系。

3. 作用域：限制作用域的关键字：`默认`，`public`，`private`，`protected`

   不用public、protected、private修饰的字段和方法就是包作用域。

   ```java
   ·定义为public的class、interface可以被其他任何类访问
   ·定义为private的field、method无法被其他类访问
   ·protected作用于继承关系。定义为protected的字段和方法可以被子类访问，以及子类的子类
   ```

   |               | **本类中** | **子类中**       | **同包类中** | **其他类中** |
   | ------------- | ---------- | ---------------- | ------------ | ------------ |
   | **public**    | **可以**   | **可以**         | **可以**     | **可以**     |
   | **protected** | **可以**   | **可以**         | **可以**     | **不可以**   |
   | **默认**      | **可以**   | **同包子类可以** | **可以**     | **不可以**   |
   | **private**   | **可以**   | **不可以**       | **不可以**   | **不可以**   |

   由于Java支持嵌套类，如果一个类内部还定义了嵌套类，那么，嵌套类拥有访问`private`的权限：

4. 当编译器遇到一个`class`名称时：

   如果是完整类名，就直接根据完整类名查找这个`class`；

   如果是简单类名，按下面的顺序依次查找：

   - 查找当前`package`是否存在这个`class`；
   - 查找`import`的包是否包含这个`class`；
   - 查找`java.lang`包是否包含这个`class`

5. final

   1. 用`final`修饰`class`可以阻止被继承
   2. 用`final`修饰`method`可以阻止被子类覆写
   3. 用`final`修饰`field`可以阻止被重新赋值
   4. 用`final`修饰局部变量可以阻止被重新赋值

### 内部类

1. Inner Class

   它与普通类有个最大的不同，就是Inner Class的实例不能单独存在，必须依附于一个Outer Class的实例。

   Inner Class除了有一个`this`指向它自己，还隐含地持有一个Outer Class实例，可以用`Outer.this`访问这个实例。所以，实例化一个Inner Class不能脱离Outer实例。

   Inner Class和普通Class相比，除了能引用Outer实例外，还有一个额外的“特权”，就是可以修改Outer Class的`private`字段，因为Inner Class的作用域在Outer Class内部，所以能访问Outer Class的`private`字段和方法。

2. Anonymous Class

   匿名类和Inner Class一样，可以访问Outer Class的`private`字段和方法。之所以我们要定义匿名类，是因为在这里我们通常不关心类名，比直接定义Inner Class可以少写很多代码。

3. Static Nested Class（静态内部类）

   用`static`修饰的内部类和Inner Class有很大的不同，它不再依附于`Outer`的实例，而是一个完全独立的类，因此无法引用`Outer.this`，但它可以访问`Outer`的`private`静态字段和静态方法。

   ```java
   public class Main {
     public static void main(String[] ags) {
      Outer.Inner inc = new Outer.Inner();
      inc.hello();
     }
   }
   
   class Outer {
     private static String NAME = "OUTER";
     private String name;
     
     Outer(String name) {
       this.name = name;
     }
     
     static class Inner {
       void hello() {
         System.out.println("Hello, "+ Outher.NAME);
       }
     }
     
   }
   ```

### Classpath 和jar

1. `classpath`是JVM用到的一个环境变量，它用来指示JVM如何搜索`class`。`classpath`就是一组目录的集合。

2. `classpath`的设定方法有两种：

   在系统环境变量中设置`classpath`环境变量，不推荐；

   在启动JVM时设置`classpath`变量，推荐。

   ```
   java -cp .;C:\work\project1\bin;C:\shared abc.xyz.Hello
   ```

   没有设置系统环境变量，也没有传入`-cp`参数，那么JVM默认的`classpath`为`.`

3. 不要把任何Java核心库添加到classpath中！JVM根本不依赖classpath加载核心库！

4. jar包就是用来干这个事的，它可以把`package`组织的目录层级，以及各个目录下的所有文件（包括`.class`文件和其他文件）都打成一个jar文件

5. jar包实际上就是一个zip格式的压缩文件，而jar包相当于目录。如果我们要执行一个jar包的`class`，就可以把jar包放到`classpath`中：

   ```
   java -cp ./hello.jar abc.xyz.Hello
   ```

   这样JVM会自动在`hello.jar`文件里去搜索某个类。

6. 需要特别注意的是，jar包里的第一层目录，不能是`bin`。JVM仍然无法从jar包中查找正确的`class`，原因是`hong.Person`必须按`hong/Person.class`存放，而不是`bin/hong/Person.class`

7. jar包还可以包含一个特殊的`/META-INF/MANIFEST.MF`文件，`MANIFEST.MF`是纯文本，可以指定`Main-Class`和其它信息。JVM会自动读取这个`MANIFEST.MF`文件，如果存在`Main-Class`，我们就不必在命令行指定启动的类名，而是用更方便的命令：

   ```java
   java -jar hello.jar
   ```

### class版本

1. 我们通常说的Java 8，Java 11，Java 17，是指JDK的版本，也就是JVM的版本，更确切地说，就是`java.exe`这个程序的版本。而每个版本的JVM，它能执行的class文件版本也不同。例如，Java 11对应的class文件版本是55，而Java 17对应的class文件版本是61。

2. 如果使用`javac`编译时不指定任何版本参数，那么相当于使用`--release 当前版本`编译，即源码版本和输出版本均为当前版本。

3. 在开发阶段，多个版本的JDK可以同时安装，当前使用的JDK版本可由`JAVA_HOME`环境变量切换。

4. 在编写源代码的时候，我们通常会预设一个源码的版本。在编译的时候，如果用`--source`或`--release`指定源码版本，则使用指定的源码版本检查语法。

   例如，使用了lambda表达式的源码版本至少要为8才能编译，使用了`var`关键字的源码版本至少要为10才能编译，使用`switch`表达式的源码版本至少要为12才能编译，且12和13版本需要启用`--enable-preview`参数。

### 模块

1. jar只是用于存放class的容器，它并不关心class之间的依赖。从Java 9开始引入的模块，主要是为了解决“依赖”这个问题。如果`a.jar`必须依赖另一个`b.jar`才能运行，那我们应该给`a.jar`加点说明啥的，让程序在编译和运行的时候能自动定位到`b.jar`，这种自带“依赖关系”的class容器就是模块。

2. 模块之间的依赖关系已经被写入到模块内的`module-info.class`文件了。把一堆class封装为jar仅仅是一个打包的过程，而把一堆class封装为模块则不但需要打包，还需要写入依赖关系，并且还可以包含二进制代码。

3. 在`src`目录下多了一个`module-info.java`这个文件，这就是模块的描述文件。在这个模块中，它长这样：

   ```java
   module hello.world {
   	requires java.base; // 可不写，任何模块都会自动引入java.base
   	requires java.xml;
   }
   ```

4. 创建模块：

   1. 编译。

      ````java
      javac -d bin src/module-info.java src/com/itranswarp/sample/*.java
      ````

   2. 打包：把bin目录下的所有class文件先打包成jar，在打包的时候，注意传入`--main-class`参数，让这个jar包能自己定位`main`方法所在的类：

   ```java
   jar --create --file hello.jar --main-class com.itranswarp.sample.Main -C bin .
   ```

   可以直接使用命令`java -jar hello.jar`来运行它

   3. 创建模块

      使用JDK自带的`jmod`命令把一个jar包转换成模块：

      ```bash
      $ jmod create --class-path hello.jar hello.jmod
      ```

5. 运行模块

   ```bash
   $ java --module-path hello.jar --module hello.world
   Hello, xml!
   ```

6. module有什么用？

   可以用来打包JRE

   过去发布一个Java应用程序，要运行它，必须下载一个完整的JRE，再运行jar包。而完整的JRE块头很大，有100多M。怎么给JRE瘦身呢？

   JRE自身的标准库已经分拆成了模块，只需要带上程序用到的模块，其他的模块就可以被裁剪掉。么裁剪JRE呢？并不是说把系统安装的JRE给删掉部分模块，而是“复制”一份JRE，但只带上用到的模块。为此，JDK提供了`jlink`命令来干这件事。命令如下：

   ```bash
   $ jlink --module-path hello.jmod --add-modules java.base,java.xml,hello.world --output jre/
   ```

​	我们在`--module-path`参数指定了我们自己的模块`hello.jmod`，然后，在`--add-modules`参数中指定了我们	用到的3个模块`java.base`、`java.xml`和`hello.world`，用`,`分隔。最后，在`--output`参数指定输出目录。

​	试试直接运行这个JRE：

```bash
$ jre/bin/java --module hello.world
Hello, xml!
```

### 访问权限

1. Java的class访问权限分为public、protected、private和默认的包访问权限。class的这些访问权限只在一个模块内有效，模块和模块之间，例如，a模块要访问b模块的某个class，必要条件是b模块明确地导出了可以访问的包。

2. 我们编写的模块`hello.world`用到了模块`java.xml`的一个类`javax.xml.XMLConstants`，我们之所以能直接使用这个类，是因为模块`java.xml`的`module-info.java`中声明了若干导出：

   ```
   module java.xml {
       exports java.xml;
       exports javax.xml.catalog;
       exports javax.xml.datatype;
       ...
   }
   ```

## java核心类

### 字符串和编码

1. Java字符串的一个重要特点就是字符串*不可变*。这种不可变性是通过内部的`private final char[]`字段，以及没有任何修改`char[]`的方法实现的。

2. 两个字符串比较，必须总是使用`equals()`方法。要忽略大小写比较，使用`equalsIgnoreCase()`方法。

3. string的一些方法

   ```java
   "Hello".contains("ll"); // true
   "Hello".indexOf("l"); // 2
   "Hello".lastIndexOf("l"); // 3
   "Hello".startsWith("He"); // true
   "Hello".endsWith("lo"); // true
   "Hello".substring(2); // "llo"
   "Hello".substring(2, 4); "ll"
   "  \tHello\r\n ".trim(); // "Hello" // 使用trim()方法可以移除字符串首尾空白字符。空白字符包括空格，\t，\r，\n
   
   strip()方法也可以移除字符串首尾空白字符。它和trim()不同的是，类似中文的空格字符\u3000也会被移除
   "\u3000Hello\u3000".strip(); // "Hello"
   
   "".isEmpty(); // true，因为字符串长度为0
   "  ".isEmpty(); // false，因为字符串长度不为0
   "  \n".isBlank(); // true，因为只包含空白字符
   " Hello ".isBlank(); // false，因为包含非空白字符
   s.replace('l', 'w'); // "hewwo"，所有字符'l'被替换为'w'
   s.replace("ll", "~~"); // "he~~o"，所有子串"ll"被替换为"~~"
   
   分割：
   String s = "A,B,C,D";
   String[] ss = s.split("\\,"); // {"A", "B", "C", "D"}
   
   join：
   String[] arr = {"A", "B", "C"};
   String s = String.join("***", arr); // "A***B***C"
   格式化：
   字符串提供了formatted()方法和format()静态方法，可以传入其他参数，替换占位符，然后生成新的字符串：
   public class Main {
       public static void main(String[] args) {
           String s = "Hi %s, your score is %d!";
           System.out.println(s.formatted("Alice", 80));
           System.out.println(String.format("Hi %s, your score is %.2f!", "Bob", 59.5));
       }
   }
   ```

4. 类型转换

   要把任意基本类型或引用类型转换为字符串，可以使用静态方法`valueOf()`。这是一个重载方法，编译器会根据参数自动选择合适的方法：

   ```java
   String.valueOf(123); // "123"
   String.valueOf(45.67); // "45.67"
   String.valueOf(true); // "true"
   String.valueOf(new Object()); // 类似java.lang.Object@636be97c
   ```

5. string转其他类型：

​	要把字符串转换为其他类型，就需要根据情况。例如，把字符串转换为`int`类型：

```java
int n1 = Integer.parseInt("123"); // 123
int n2 = Integer.parseInt("ff", 16); // 按十六进制转换，255
```

​	把字符串转换为`boolean`类型：

```java
boolean b1 = Boolean.parseBoolean("true"); // true
boolean b2 = Boolean.parseBoolean("FALSE"); // false
```

​	`String`和`char[]`类型可以互相转换，方法是：

```java
char[] cs = "Hello".toCharArray(); // String -> char[]
String s = new String(cs); // char[] -> String
```

### StringBuilder

1. 为了能高效拼接字符串，Java标准库提供了`StringBuilder`，它是一个可变对象，可以预分配缓冲区，这样，往`StringBuilder`中新增字符时，不会创建新的临时对象

2. `StringBuilder`还可以进行链式操作：

   ```java
   public class Main {
       public static void main(String[] args) {
           var sb = new StringBuilder(1024);
           sb.append("Mr ")
             .append("Bob")
             .append("!")
             .insert(0, "Hello, ");2
           System.out.println(sb.toString());
       }
   }
   
   ```

### StringJoiner

用分隔符拼接数组的需求很常见，所以Java标准库还提供了一个`StringJoiner`来干这个事：

```java
public class Main {
    public static void main(String[] args) {
        String[] names = {"Bob", "Alice", "Grace"};
        var sj = new StringJoiner(", ", "Hello ", "!"); // 指定字符串拼接头和结尾
        for (String name : names) {
            sj.add(name);
        }
        System.out.println(sj.toString());
    }
}

```

在不需要指定“开头”和“结尾”的时候，用`String.join()`更方便，在内部使用了`StringJoiner`来拼接字符串

```java
String[] names = {"Bob", "Alice", "Grace"};
var s = String.join(", ", names);
```

### 包装类

1. 如何把一个基本类型视为对象（引用类型）？因为包装类型非常有用，Java核心库为每种基本类型都提供了对应的包装类型：

| 基本类型 | 对应的引用类型      |
| :------- | :------------------ |
| boolean  | java.lang.Boolean   |
| byte     | java.lang.Byte      |
| short    | java.lang.Short     |
| int      | java.lang.Integer   |
| long     | java.lang.Long      |
| float    | java.lang.Float     |
| double   | java.lang.Double    |
| char     | java.lang.Character |

2. 自动装箱和拆箱：因为`int`和`Integer`可以互相转换：

   ```java
   int i = 100;
   Integer n = Integer.valueOf(i); // 推荐使用这种方式创建Integer类型变量，而不用new，Byte.valueOf()方法的源码，可以看到，标准库返回的Byte实例全部是缓存实例，但调用者并不关心静态工厂方法以何种方式创建新实例还是直接返回缓存的实例。
   int x = n.intValue();
   ```

   所以，Java编译器可以帮助我们自动在`int`和`Integer`之间转型：

   ```
   Integer n = 100; // 编译器自动使用Integer.valueOf(int)
   int x = n; // 编译器自动使用Integer.intValue()
   ```

   这种直接把`int`变为`Integer`的赋值写法，称为`自动装箱（Auto Boxing）`，反过来，把`Integer`变为`int`的赋值写法，称为`自动拆箱（Auto Unboxing）`。

   注意：自动装箱和自动拆箱只发生在编译阶段，目的是为了少写代码。

3. 对两个`Integer`实例进行比较要特别注意：绝对不能用`==`比较，因为`Integer`是引用类型，必须使用`equals()`比较。

4. 进制转换。`Integer`还可以把整数格式化为指定进制的字符串：

   ```java
   public class Main {
       public static void main(String[] args) {
           System.out.println(Integer.toString(100)); // "100",表示为10进制
           System.out.println(Integer.toString(100, 36)); // "2s",表示为36进制
           System.out.println(Integer.toHexString(100)); // "64",表示为16进制
           System.out.println(Integer.toOctalString(100)); // "144",表示为8进制
           System.out.println(Integer.toBinaryString(100)); // "1100100",表示为2进制
       }
   }
   
   ```

5. Java的包装类型还定义了一些有用的静态变量

   ```java
   // boolean只有两个值true/false，其包装类型只需要引用Boolean提供的静态字段:
   Boolean t = Boolean.TRUE;
   Boolean f = Boolean.FALSE;
   // int可表示的最大/最小值:
   int max = Integer.MAX_VALUE; // 2147483647
   int min = Integer.MIN_VALUE; // -2147483648
   // long类型占用的bit和byte数量:
   int sizeOfLong = Long.SIZE; // 64 (bits)
   int bytesOfLong = Long.BYTES; // 8 (bytes)
   ```

6. 所有的整数和浮点数的包装类型都继承自`Number`，因此，可以非常方便地直接通过包装类型获取各种基本类型：

   ```java
   // 向上转型为Number:
   Number num = new Integer(999);
   // 获取byte, int, long, float, double:
   byte b = num.byteValue();
   int n = num.intValue();
   long ln = num.longValue();
   float f = num.floatValue();
   double d = num.doubleValue();
   ```

### JavaBean

1. 如果读写方法符合以下这种命名规范：

   ```
   // 读方法:
   public Type getXyz()
   // 写方法:
   public void setXyz(Type value)
   ```

   那么这种`class`被称为`JavaBean`：

   它通常具有如下特点：

   - 这个Java类必须具有一个无参的构造函数
   - 属性必须私有化。
   - 私有化的属性必须通过public类型的方法暴露给其它程序，并且方法的命名也必须遵守一定的命名规范。

2. JavaBean主要用来传递数据，即把一组数据组合成一个JavaBean便于传输。

3. 举例：

   ```java
   public class Person {
       private String name;
       private int age;
   
       public String getName() { return this.name; }
       public void setName(String name) { this.name = name; }
   
       public int getAge() { return this.age; }
       public void setAge(int age) { this.age = age; }
   
       public boolean isChild() {
           return age <= 6;
       }
   }
   ```

### 枚举类

1. 为了让编译器能自动检查某个值在枚举的集合内，并且，不同用途的枚举需要不同的类型来标记，不能混用，我们可以使用`enum`来定义枚举类：

2. 和`int`定义的常量相比，使用`enum`定义枚举有如下好处：

​	首先，`enum`常量本身带有类型信息，即`Weekday.SUN`类型是`Weekday`，编译器会自动检查出类型错误。例如，	下面的语句不可能编译通过：

```java
int day = 1;
if (day == Weekday.SUN) { // Compile error: bad operand types for binary operator '=='
}
```

​	其次，不可能引用到非枚举的值，因为无法通过编译。	

​	最后，不同类型的枚举不能互相比较或者赋值，因为类型不符。例如，不能给一个`Weekday`枚举类型的变量赋	值为`Color`枚举类型的值：

```
Weekday x = Weekday.SUN; // ok!
Weekday y = Color.RED; // Compile error: incompatible types
```

​	这就使得编译器可以在编译期自动检查出所有可能的潜在错误。

3. 普通引用类型必须使用equals()方法进行比较，但enum类型可以例外。

   这是因为`enum`类型的每个常量在JVM中只有一个唯一实例，所以可以直接用`==`比较：

   ```java
   if (day == Weekday.FRI) { // ok!
   }
   if (day.equals(Weekday.SUN)) { // ok, but more code!
   }
   ```

4.`enum`定义的枚举类，和其他的`class`有什么区别？

​	答案是没有任何区别。`enum`定义的类型就是`class`，只不过它有以下几个特点：

- 定义的`enum`类型总是继承自`java.lang.Enum`，且无法被继承；
- 只能定义出`enum`的实例，而无法通过`new`操作符创建`enum`的实例；
- 定义的每个实例都是引用类型的唯一实例；
- 可以将`enum`类型用于`switch`语句。

5. 因为`enum`是一个`class`，每个枚举的值都是`class`实例。因此，这些实例有一些方法：

   ```java
   String s = Weekday.SUN.name(); // "SUN",name()方法返回常量名
   int n = Weekday.MON.ordinal(); // 1，ordinal()方法返回定义的常量的顺序，从0开始计数
   ```

6.`Weekday`的枚举常量如果要和`int`转换，最好不要使用ordinal()方法返回的顺序，因为枚举顺序发生变化将会导致结果异常。因为`enum`本身是`class`，所以我们可以定义`private`的构造方法，并且，给每个枚举常量添加字段：

```java
public class Main {
    public static void main(String[] args) {
        Weekday day = Weekday.SUN;
        if (day.dayValue == 6 || day.dayValue == 0) {
            System.out.println("Work at home!");
        } else {
            System.out.println("Work at office!");
        }
    }
}

enum Weekday {
    MON(1), TUE(2), WED(3), THU(4), FRI(5), SAT(6), SUN(0);

    public final int dayValue;

    private Weekday(int dayValue) {
        this.dayValue = dayValue;
    }
}

```

7. 默认情况下，对枚举常量调用`toString()`会返回和`name()`一样的字符串。但是，`toString()`可以被覆写，而`name()`则不行。

## 反射

### class类

1. 反射就是Reflection，Java的反射是指程序在运行期可以拿到一个对象的所有信息。所以，反射是为了解决在运行期，对某个实例一无所知的情况下，如何调用其方法。

2. `class`是由JVM在执行过程中动态加载的。JVM在第一次读取到一种`class`类型时，将其加载进内存。

   每加载一种`class`，JVM就为其创建一个`Class`类型的实例，并关联起来。

   由于JVM为每个加载的`class`创建了对应的`Class`实例，并在实例中保存了该`class`的所有信息，我们就可以通过这个`Class`实例获取到该实例对应的`class`的所有信息。这种通过`Class`实例获取`class`信息的方法称为反射（Reflection）。

3. 如何获取一个`class`的`Class`实例？有三个方法：

   方法一：直接通过一个`class`的静态变量`class`获取：

   ```java
   Class cls = String.class;
   ```

   方法二：如果我们有一个实例变量，可以通过该实例变量提供的`getClass()`方法获取：

   ```java
   String s = "Hello";
   Class cls = s.getClass();
   ```

   方法三：如果知道一个`class`的完整类名，可以通过静态方法`Class.forName()`获取：

   ```java
   Class cls = Class.forName("java.lang.String");
   ```

4. 用`instanceof`不但匹配指定类型，还匹配指定类型的子类。而用`==`判断`class`实例可以精确地判断数据类型，但不能作子类型比较。

5. 因为反射的目的是为了获得某个实例的信息。因此，当我们拿到某个`Object`实例时，我们可以通过反射获取该`Object`的`class`信息

6. 动态加载：JVM在执行Java程序的时候，并不是一次性把所有用到的class全部加载到内存，而是第一次需要用到class时才加载。动态加载`class`的特性对于Java程序非常重要。利用JVM动态加载`class`的特性，我们才能在运行期根据条件加载不同的实现类。

### 访问字段

1. 先看看如何通过`Class`实例获取字段信息。`Class`类提供了以下几个方法来获取字段：

   - Field getField(name)：根据字段名获取某个public的field（包括父类）
   - Field getDeclaredField(name)：根据字段名获取当前类的某个field（不包括父类）
   - Field[] getFields()：获取所有public的field（包括父类）
   - Field[] getDeclaredFields()：获取当前类的所有field（不包括父类）

2. 一个`Field`对象包含了一个字段的所有信息：

   - `getName()`：返回字段名称，例如，`"name"`；

   - `getType()`：返回字段类型，也是一个`Class`实例，例如，`String.class`；

   - `getModifiers()`：返回字段的修饰符，它是一个`int`，不同的bit表示不同的含义。

   - ```java
     用反射获取该字段的信息，代码如下：
     
     Field f = String.class.getDeclaredField("value");
     f.getName(); // "value"
     f.getType(); // class [B 表示byte[]类型
     int m = f.getModifiers();
     Modifier.isFinal(m); // true
     Modifier.isPublic(m); // false
     Modifier.isProtected(m); // false
     Modifier.isPrivate(m); // true
     Modifier.isStatic(m); // false
     ```

3. 获取字段值。用`Field.get(Object)`获取指定实例的指定字段的值。

   ```java
   public class Main {
   
       public static void main(String[] args) throws Exception {
           Object p = new Person("Xiao Ming");
           Class c = p.getClass();
           Field f = c.getDeclaredField("name");
           Object value = f.get(p);
           System.out.println(value); // "Xiao Ming"
       }
   }
   
   class Person {
       private String name;
   
       public Person(String name) {
           this.name = name;
       }
   }
   
   ```

4. 设置字段值。设置字段值是通过`Field.set(Object, Object)`实现的，其中第一个`Object`参数是指定的实例，第二个`Object`参数是待修改的值。

   ```java
   public class Main {
   
       public static void main(String[] args) throws Exception {
           Person p = new Person("Xiao Ming");
           System.out.println(p.getName()); // "Xiao Ming"
           Class c = p.getClass();
           Field f = c.getDeclaredField("name");
           f.setAccessible(true);
           f.set(p, "Xiao Hong");
           System.out.println(p.getName()); // "Xiao Hong"
       }
   }
   
   class Person {
       private String name;
   
       public Person(String name) {
           this.name = name;
       }
   
       public String getName() {
           return this.name;
       }
   }
   
   ```

## 注解

1. 注解的作用：

从JVM的角度看，注解本身对代码逻辑没有任何影响，如何使用注解完全由工具决定。

Java的注解可以分为三类：

第一类是由编译器使用的注解，例如：

- `@Override`：让编译器检查该方法是否正确地实现了覆写；
- `@SuppressWarnings`：告诉编译器忽略此处代码产生的警告。

这类注解不会被编译进入`.class`文件，它们在编译后就被编译器扔掉了。

第二类是由工具处理`.class`文件使用的注解，比如有些工具会在加载class的时候，对class做动态修改，实现一些特殊的功能。这类注解会被编译进入`.class`文件，但加载结束后并不会存在于内存中。这类注解只被一些底层库使用，一般我们不必自己处理。

第三类是在程序运行期能够读取的注解，它们在加载后一直存在于JVM中，这也是最常用的注解。例如，一个配置了`@PostConstruct`的方法会在调用构造方法后自动被调用（这是Java代码读取该注解实现的功能，JVM并不会识别该注解）。

2. 定义注解：Java语言使用`@interface`语法来定义注解（`Annotation`）。自定义注解要使用元注解。@Target、@Retention、@Repeatable、@Inherited

3. 处理注解：使用反射API读取Annotation。

   使用反射API读取`Annotation`有两种方法。方法一是先判断`Annotation`是否存在，如果存在，就直接读取：

   ```java
   Class cls = Person.class;
   if (cls.isAnnotationPresent(Report.class)) {
       Report report = cls.getAnnotation(Report.class);
       ...
   }
   ```

   第二种方法是直接读取`Annotation`，如果`Annotation`不存在，将返回`null`：

   ```java
   Class cls = Person.class;
   Report report = cls.getAnnotation(Report.class);
   if (report != null) {
      ...
   }
   ```

4. 注解如何使用，完全由程序自己决定。定义了注解，本身对程序逻辑没有任何影响。我们必须自己编写代码来使用注解。

## 泛型

1. 泛型是一种“代码模板”，可以用一套代码套用各种类型。泛型的好处是使用时不必对类型进行强制转换，它通过编译器对类型进行检查；

   ```java
   public class ArrayList<T> {
       private T[] array;
       private int size;
       public void add(T e) {...}
       public void remove(int index) {...}
       public T get(int index) {...}
   }
   ```

   `T`可以是任何class。这样一来，我们就实现了：编写一次模版，可以创建任意类型的`ArrayList`：

   ```java
   // 创建可以存储String的ArrayList:
   ArrayList<String> strList = new ArrayList<String>();
   // 创建可以存储Float的ArrayList:
   ArrayList<Float> floatList = new ArrayList<Float>();
   // 创建可以存储Person的ArrayList:
   ArrayList<Person> personList = new ArrayList<Person>();
   ```

2. 除了`ArrayList<T>`使用了泛型，还可以在接口中使用泛型。例如，`Arrays.sort(Object[])`可以对任意数组进行排序，但待排序的元素必须实现`Comparable<T>`这个泛型接口：

   ```java
   public interface Comparable<T> {
       /**
        * 返回负数: 当前实例比参数o小
        * 返回0: 当前实例与参数o相等
        * 返回正数: 当前实例比参数o大
        */
       int compareTo(T o);
   }
   ```

3. 编写泛型。

   泛型还可以定义多种类型。例如，我们希望`Pair`不总是存储两个类型一样的对象，就可以使用类型`<T, K>`：

   ```java
   public class Pair<T, K> {
       private T first;
       private K last;
       public Pair(T first, K last) {
           this.first = first;
           this.last = last;
       }
       public T getFirst() { ... }
       public K getLast() { ... }
   }
   ```

   使用的时候，需要指出两种类型：

   ```java
   Pair<String, Integer> p = new Pair<>("test", 123);
   ```

4. Java的泛型是采用擦拭法实现的；

   所谓擦拭法是指，虚拟机对泛型其实一无所知，所有的工作都是编译器做的。编译器内部永远把所有类型`T`视为`Object`处理，但是，在需要转型的时候，编译器会根据`T`的类型自动为我们实行安全地强制转型。

   擦拭法决定了泛型`<T>`：

   - 不能是基本类型，例如：`int`；
   - 不能获取带泛型类型的`Class`，例如：`Pair<String>.class`；
   - 不能判断带泛型类型的类型，例如：`x instanceof Pair<String>`；
   - 不能实例化`T`类型，例如：`new T()`。

   泛型方法要防止重复定义方法，例如：`public boolean equals(T obj)`；

5. 我们无法获取`Pair<T>`的`T`类型，即给定一个变量`Pair<Integer> p`，无法从`p`中获取到`Integer`类型。

   但是，在父类是泛型类型的情况下，编译器就必须把类型`T`（对`IntPair`来说，也就是`Integer`类型）保存到子类的class文件中，不然编译器就不知道`IntPair`只能存取`Integer`这种类型。

   在继承了泛型类型的情况下，子类可以获取父类的泛型类型。

6. 使用`<? extends Number>`的泛型定义称之为上界通配符（Upper Bounds Wildcards），即把泛型类型`T`的上界限定在`Number`了。

   总结来说：使用类似`<T extends Number>`定义泛型类时表示：泛型类型限定为`Number`以及`Number`的子类。

7. 编译错误发生在`p.setFirst()`传入的参数是`Integer`类型。有些童鞋会问了，既然`p`的定义是`Pair<? extends Number>`，那么`setFirst(? extends Number)`为什么不能传入`Integer`？

   原因还在于擦拭法。如果我们传入的`p`是`Pair<Double>`，显然它满足参数定义`Pair<? extends Number>`，然而，`Pair<Double>`的`setFirst()`显然无法接受`Integer`类型。

   这就是`<? extends Number>`通配符的一个重要限制：方法参数签名`setFirst(? extends Number)`无法传递任何`Number`的子类型给`setFirst(? extends Number)`。

8. ```java
       /**
        * 这个<T> T 可以传入任何类型的List
        * 参数T
        *     第一个 表示是泛型
        *     第二个 表示返回的是T类型的数据
        *     第三个 限制参数类型为T
        * @param data
        * @return
        */
       private <T> T getListFisrt(List<T> data) {
           if (data == null || data.size() == 0) {
               return null;
           }
           return data.get(0);
       }
   
   ```

## maven

1. maven是java的的项目管理和整合工具，将项目的开发和管理过程抽象成一个项目对象模型（PMO）。只需要简单的配置就可以完成项目的编译、测试、部署及发布工作。当 Maven 项目需要某个依赖时，只要其 POM 中声明了依赖的坐标信息，Maven 就会自动从仓库中去下载该构件使用。

2. 约定优于配置时maven的核心理念，maven对项目结构有一定的约束，如；

   | 文件         | 目录               |
   | ------------ | ------------------ |
   | Java 源代码  | src/main/java      |
   | 资源文件     | src/main/resources |
   | 测试源代码   | src/test/java      |
   | 测试资源文件 | src/test/resources |
   | 打包输出文件 | target             |
   | 编译输出文件 | target/classes     |

3. POM定义了项目的基本信息，用于描述项目如何构建、生命项目依赖等

4. Maven 坐标一套规则，它规定：世界上任何一个构件都可以使用 Maven 坐标并作为其唯一标识，Maven 坐标包括 groupId、artifactId、version、packaging 等元素，只要用户提供了正确的坐标元素，Maven 就能找到对应的构件。 绝大部分依赖的 Maven 坐标都能在 https://mvnrepository.com/ 中获取

5. 依赖。dependencies 元素可以包含一个或者多个 dependency 子元素，用以声明一个或者多个项目依赖，每个依赖都可以包含以下元素：

   - groupId、artifactId 和 version：依赖的基本坐标，对于任何一个依赖来说，基本坐标是最重要的，Maven 根据坐标才能找到需要的依赖。
   - type：依赖的类型，对应于项目坐标定义的 packaging。大部分情况下，该元素不必声明，其默认值是 jar。
   - scope：依赖的范围。
   - optional：标记依赖是否可选。
   - exclusions：用来排除传递性依赖。

6. 生命周期。为了更好的理解 Maven 生命周期、插件以及插件目标三者的关系，我们可以将 Maven 生命周期理解成一个抽象父类，将插件理解成其子类，将插件目标理解成子类中重写的方法

   Maven 拥有三套标准的生命周期：

   - clean：用于清理项目
   - default：用于构建项目
   - site：用于建立项目站点

7. Maven 默认为一些核心的生命周期阶段绑定了插件目标，当用户调用这些阶段时，对应的插件目标就会自动执行相应的任务。

| 生命周期               | 阶段                                      | 插件目标                           | 执行的任务             |
| ---------------------- | ----------------------------------------- | ---------------------------------- | ---------------------- |
| clean                  | pre-clean                                 |                                    |                        |
| clean                  | maven-clean-plugin:clean                  | 清理 Maven 的输出目录              |                        |
| post-clean             |                                           |                                    |                        |
| site                   | pre-site                                  |                                    |                        |
| site                   | maven-site-plugin:site                    | 生成项目站点                       |                        |
| post-site              |                                           |                                    |                        |
| site-deploy            | maven-site-plugin:deploy                  | 部署项目站点                       |                        |
| default                | process-resources                         | maven-resources-plugin:resources   | 复制资源文件到输出目录 |
| compile                | maven-compiler-plugin:compile             | 编译代码到输出目录                 |                        |
| process-test-resources | maven-resources-plugin:testResources      | 复制测试资源文件到测试输出目录     |                        |
| test-compile           | maven-compiler-plugin:testCompile         | 编译测试代码到测试输出目录         |                        |
| test                   | maven-surefire-plugin:test                | 执行测试用例                       |                        |
| package                | maven-jar-plugin:jar/maven-jar-plugin:war | 创建项目 jar/war 包                |                        |
| install                | maven-install-plugin:install              | 将项目输出的包文件安装到本地仓库   |                        |
| deploy                 | maven-deploy-plugin:deploy                | 将项目输出的包文件部署到到远程仓库 |                        |
