# String转基本类型

```java
String s = "123";
int i = Integer.parseInt(s);
double d = Double.parseDouble(s);
float f = Float.parseFloat(s);
long l = Long.parseLong(s);
short s1 = Short.parseShort(s);
byte b = Byte.parseByte(s);
boolean is = Boolean.parseBoolean("true");
```

# 基本类型转String

```java
int n1 = 10;
long n2 = 10L;
double n3 = 3.4;
float n4 = 3.4F;
String str1 = n1 + "";
String str2 = n2 + "";
String str3 = n3 + "";
String str4 = n4 + "";
String str5 = String.valueOf(n1);
```

