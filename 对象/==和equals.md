# “==”介绍

* == 既可以判断基本类型，又可以判断引用类型

* 当判断时是基本类型的时候，判断的是值是否相等

* 当判断时是引用类型的时候，判断的是地址是否相等，即判断是不是同一个对象

  ```java
  class A {}
  class B extends A {}
  
  A a = new A();
  A b = a;
  A c = a;
  b == c; // true
  a == c; // true
  
  B b = new B();
  A a = b;
  a == b; // true
  
  ```



# equals 介绍

* equals是Object的方法，只能判断引用类型
* 默认判断地址是否相等，子类往往会重写该方法，用于判断内容是否相等