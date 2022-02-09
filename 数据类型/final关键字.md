# 介绍

> final 中文意思：最后的，最终的
>
> final 可以修饰类、属性、方法和局部变量

### 有以下需求就可以用到final

* 当不希望类被继承时，可以用final修饰

  ```java
  public class Final01 {
  }
  
  final class A {}
  //class B extends A{}
  ```

* 当不希望父类的某个方法被子类覆盖/重写时，可以用final关键字

  ```java
  class A {
      public final void a() {}
  }
  class B extends A{
  //    public void a(){}
  }
  ```

* 当不希望类的某个属性值被修改，可以用final修饰

  ```java
  public class Final01 {
      public static void main(String[] args) {
  //        new A().MAX_VALUE = 100;
      }
  }
  
  class A {
      public final int MAX_VALUE = 100;
  }
  ```

* 当不希望某个局部变量被修改，可以使用final修饰



# 细节

* final修饰的属性又叫常量，一般用 XX_XX 命名

* final修饰的属性在定义时，必须赋初值，并且以后都不能修改，赋值可以在如下位置之一

  * 定义时
  * 在构造器中
  * 在代码块中

  ```java
  class A {
  
      private final int MAX_VALUE1 = 1;
      private final int MAX_VALUE2;
      private final int MAX_VALUE3;
  
      {
          MAX_VALUE3 = 1;
      }
  
      public A() {
          MAX_VALUE2 = 1;
      }
  }
  ```

* 如果final修饰的属性是静态的，则初始化的位置只能是

  * 定义时
  * 在静态静态代码块
  * 不能在构造器中赋值

  ```java
  class A {
  
      private static final int MAX_VALUE1 = 1;
      private static final int MAX_VALUE2;
      private static final int MAX_VALUE3;
  
      static {
          MAX_VALUE2 = 1;
          MAX_VALUE3 = 1;
      }
  }
  ```

* final类虽不能继承，但是可以实例化对象

* 如果类不是final类，但是含有final方法，则该方法不能重写，但是可以被继承

* 一般来说，如果一个类已经是final类类，就没必要将方法修饰成final（因为没得被继承，就无法重写了呗）

* final不能修饰构造方法

* final和static 往往搭配使用，效率更高，不会导致类加载，底层编译器做了优化处理

  ```java
  public class Final01 {
      public static void main(String[] args) {
          System.out.println(A.MAX_VALUE1);
      }
  }
  
  class A {
      public static final int MAX_VALUE1 = 1;
  
      static {
          System.out.println("静态代码块");
      }
  }
  
  // 运行结果
  // 1
  ```

  