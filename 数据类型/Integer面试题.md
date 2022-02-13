# Integer面试题

```java
Object obj1 = true ? new Integer(1):new Double(2.0);
System.out.println(obj1);
// 1.0
// 因为三元运算符是一个整体，会提高精度为Double
```

```java
Integer i1 = new Integer(127);
Integer i2 = new Integer(127);
System.out.println(i1 == i2); // False

Integer i3 = new Integer(128);
Integer i4 = new Integer(128);
System.out.println(i3 == i4); // False

Integer i5 = 127;// Integer.valueOf(127); -> cache(-128~127)
Integer i6 = 127;// Integer.valueOf(127); -> cache(-128~127)
System.out.println(i5 == i6); // True

Integer i7 = 128;// 不在缓存 所以会 new Integer(128);
Integer i8 = 128;
System.out.println(i7 == i8); // False

Integer i9 = 127;
Integer i10 = new Integer(127);
System.out.println(i9 == i10); // False

Integer i11 = 127;
int i12 = 127;
System.out.println(i11 == i12); // True 有基本数据类型 就会比较值是否一样

Integer i13 = 128;
int i14 = 128;
System.out.println(i13 == i14);// True 有基本数据类型 就会比较值是否一样
```

