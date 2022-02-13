# t-c-f 细节

* 如果没有出现异常，则执行try块中所有语句，不执行catch块语句，如果有finally，最后还需要执行finally里面语句
* 如果出现异常，则执行try块中异常发生后，try块剩下的语句不再执行。将执行catch块语句，如果有finally，最后还需要执行finally里面语句



### 练习1

```java
public class try02 {
    public static int m() {
        int i = 1;
        try {
            i++;
            String[] names = new String[3];
            if (names[1].equals("tom")) { // NullPointerException
                System.out.println(names[1]);
            } else {
                names[3] = "xxx";
            }
            return 1;
        } catch (ArrayIndexOutOfBoundsException e) {
            return 2;
        } catch (NullPointerException e) {
            return 3;
        } finally {
            return 4;
        }
    }

    public static void main(String[] args) {
        System.out.println(m());
        //finally 无论如何都会执行 所以最后是4
    }
}
```



### 练习2

```java
public class try03 {
    public static int m() {
        int i = 1;
        try {
            i++;
            String[] names = new String[3];
            if (names[1].equals("tom")) { // NullPointerException
                System.out.println(names[1]);
            } else {
                names[3] = "xxx";
            }
            return 1;
        } catch (ArrayIndexOutOfBoundsException e) {
            return ++i;
        } catch (NullPointerException e) {
            return ++i; // i = 3
        } finally {
            return ++i; // i = 4
        }
    }

    public static void main(String[] args) {
        System.out.println(m());
        //先catch 最后finally 所以最后是4
    }
}
```



### 练习3

```java
public class try01 {
    public static int m() {
        int i = 1;
        try {
            i++;
            String[] names = new String[3];
            if (names[1].equals("tom")) { // NullPointerException
                System.out.println(names[1]);
            } else {
                names[3] = "xxx";
            }
            return 1;
        } catch (ArrayIndexOutOfBoundsException e) {
            return 2;
        } catch (NullPointerException e) {
            return ++i; // i = 3 -> temp = i -> temp = 3
        } finally {
            ++i; // i = 4
            System.out.println("i = " + i); // i = 4
        }
    }

    public static void main(String[] args) {
        System.out.println(m()); // temp = i -> temp = 3
    }
}
```