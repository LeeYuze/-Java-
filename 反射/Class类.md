# 介绍

* Class也是类，因此也继承Object类

* Class类对象不是new出来的，而是系统创建的

  ```java
  // 传统方法
  //        public Class<?> loadClass(String name) throws 	ClassNotFoundException {
  //            return loadClass(name, false);
  //        }
  // 会有类加载
  Cat cat = new Cat();
  
  // 反射的方式
  //        public Class<?> loadClass(String name) throws ClassNotFoundException {
  //            return loadClass(name, false);
  //        }
  // 最终还是去类加载
  try {
    Class.forName("reflection_.Cat");
  } catch (ClassNotFoundException e) {
    e.printStackTrace();
  }
  ```

* 对于某个类的Class类对象，在内存中只有一份，因为类只加载一次

  ```java
  Class cls01 = Class.forName("reflection_.Cat");
  System.out.println(cls01.hashCode())
  Class cls02 = Class.forName("reflection_.Cat");
  System.out.println(cls02.hashCode())
  ```

* 每个类的实例都有记得自己是由哪个 Class 实力所生成
* 通过Class对象可以完整地得到一个类的完整结构，通过一系列API
* Class对象是存放在堆堆
* 类的字节码二进制数据，是放在方法区，有的地方成为类的元数据（包括 方法代码 变量名 方法名 访问权限等等）



# 常用方法

```java
String classAllPath = "com.hspedu.Car";
//1 . 获取到Car类 对应的 Class对象
//<?> 表示不确定的Java类型
Class<?> cls = Class.forName(classAllPath);
//2. 输出cls
System.out.println(cls); //显示cls对象, 是哪个类的Class对象 com.hspedu.Car
System.out.println(cls.getClass());//输出cls运行类型 java.lang.Class
//3. 得到包名
System.out.println(cls.getPackage().getName());//包名
//4. 得到全类名
System.out.println(cls.getName());
//5. 通过cls创建对象实例
Car car = (Car) cls.newInstance();
System.out.println(car);//car.toString()
//6. 通过反射获取属性 brand
Field brand = cls.getField("brand");
System.out.println(brand.get(car));//宝马
//7. 通过反射给属性赋值
brand.set(car, "奔驰");
System.out.println(brand.get(car));//奔驰
//8 我希望大家可以得到所有的属性(字段)
System.out.println("=======所有的字段属性====");
Field[] fields = cls.getFields();
for (Field f : fields) {
  System.out.println(f.getName());//名称
}
```



# 获取Class对象

* 前提：已知一个类的全类名，且该类在类路径下，可通过Class类的静态方法forName()获取，可能抛出ClassNotFoundException

  应用场景：多用于配置文件，读取类的全路径，加载类

  ```java
  Class cls1 = Class.forName("java.lang.Cat");
  ```

* 前提：若已知具体的类，通过类的class获取，该方式 最为安全可靠，程序性能最高

  应用场景：多用于参数传递，比如通过反射得到对应构造器对象

  ```java
  Class cls2 = Cat.class;
  ```

* 前提：已知某个类的实例，调用该实例的getClass()方法获取Class对象

  应用场景：通过创建好的对象，获取Class对象

  ```java
  Class clazz = 对象.getClass();
  ```

* 其他方式

  ```java
  ClassLoader cl = 对象.getClass().getClassLoader();
  Class clazz4 = cl.loadClass(“类的全类名”)
  ```

* 基本类型

  ```java
  Class<Integer> integerClass = int.class;
  
  Class<Integer> type1 = Integer.Type;
  ```