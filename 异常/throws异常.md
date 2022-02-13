# 介绍

* 如果一个方法可能生成某种异常，当是并不确定如何处理这种异常，则此方法应该显示地声明抛出异常，表面该方法将不对这些异常进行处理，而由该方法的**调用者负责处理**
* 在方法声明中用throws语句可以声明抛出异常的列表，throws后面的异常类型可以是方法中产生的异常类型，也可以是它的父类



# 细节

* 对于编译异常，程序中必须处理，比如try-catch 或者 throws

* 对于运行时异常，程序中如果没有处理，默认就是throws的方式处理

  ```java
  public class throws01 {
    	// main 也会默认throws ArithmeticException
    	// 交给jvm处理
      public static void main(String[] args) {
          f1();
      }
  
      public static void f1() throws ArithmeticException {
  
      }
  }
  ```

* 子类重写父类的方法时，对抛出异常的规定：子类重写的方法，所抛出的异常要么和父类抛出的异常一只，要么为父类抛出异常类型的子类型

  ```java
  class Father {
      public void m() throws RuntimeException {
      }
  }
  
  class Son extends Father {
      @Override
      public void m() throws ArithmeticException {
      }
  }
  ```

* 在throws过程中，如果有方法 try-catch，就相当于处理异常，就可以不用throws